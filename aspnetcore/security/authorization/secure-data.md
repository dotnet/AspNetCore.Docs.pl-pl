---
title: Tworzenie aplikacji ASP.NET Core przy użyciu danych użytkownika chronionych przez autoryzację
author: rick-anderson
description: Dowiedz się, jak utworzyć aplikację sieci Web ASP.NET Core przy użyciu danych użytkownika chronionych przez autoryzację. Obejmuje HTTPS, uwierzytelnianie, zabezpieczenia, ASP.NET Core Identity .
ms.author: riande
ms.date: 7/18/2020
ms.custom: mvc, seodec18
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/secure-data
ms.openlocfilehash: 44777369693f9eb29d78c3ba638db2e692f430ae
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021190"
---
# <a name="create-an-aspnet-core-web-app-with-user-data-protected-by-authorization"></a>Tworzenie aplikacji sieci Web ASP.NET Core przy użyciu danych użytkownika chronionych przez autoryzację

Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT) i Jan [Audette](https://twitter.com/joeaudette)

::: moniker range="= aspnetcore-2.0"

Zobacz [ten plik PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

W tym samouczku pokazano, jak utworzyć aplikację sieci Web ASP.NET Core przy użyciu danych użytkownika chronionych przez autoryzację. Zostanie wyświetlona lista kontaktów, które zostały utworzone przez uwierzytelnionych (zarejestrowanych) użytkowników. Istnieją trzy grupy zabezpieczeń:

* **Zarejestrowani użytkownicy** mogą wyświetlać wszystkie zatwierdzone dane i edytować/usuwać własne dane.
* **Menedżerowie** mogą zatwierdzać lub odrzucać dane kontaktów. Tylko zatwierdzone kontakty są widoczne dla użytkowników.
* **Administratorzy** mogą zatwierdzić/odrzucić i edytować/usunąć dowolne dane.

Obrazy w tym dokumencie nie są dokładnie zgodne z najnowszymi szablonami.

Na poniższej ilustracji użytkownik Rick ( `rick@example.com` ) jest zalogowany. Rick mogą wyświetlać tylko zatwierdzone kontakty i **edytować** / **Usuń** / **Utwórz nowe** linki dla swoich kontaktów. Tylko ostatni rekord utworzony przez Rick zawiera linki do **edycji** i **usuwania** . Inni użytkownicy nie będą widzieć ostatniego rekordu do momentu zmiany stanu na "zatwierdzone" przez Menedżera lub administratora.

![Zrzut ekranu przedstawiający Rick zalogowany](secure-data/_static/rick.png)

Na poniższej ilustracji `manager@contoso.com` jest zalogowany i w roli menedżera:

![Zrzut ekranu przedstawiający manager@contoso.com zalogowany](secure-data/_static/manager1.png)

Na poniższej ilustracji przedstawiono widok szczegółów osoby kontaktowej:

![Widok kontaktu w Menedżerze](secure-data/_static/manager.png)

Przyciski **Zatwierdź** i **Odrzuć** są wyświetlane tylko dla menedżerów i administratorów.

Na poniższej ilustracji `admin@contoso.com` jest zalogowany i w roli administratora:

![Zrzut ekranu przedstawiający admin@contoso.com zalogowany](secure-data/_static/admin.png)

Administrator ma wszystkie uprawnienia. Może odczytywać/edytować/usuwać dowolne kontakty i zmieniać stan kontaktów.

Aplikacja została utworzona przez utworzenie [szkieletu](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) następującego `Contact` modelu:

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

Przykład zawiera następujące programy obsługi autoryzacji:

* `ContactIsOwnerAuthorizationHandler`: Zapewnia, że użytkownik może edytować tylko swoje dane.
* `ContactManagerAuthorizationHandler`: Umożliwia menedżerom zatwierdzanie lub odrzucanie kontaktów.
* `ContactAdministratorsAuthorizationHandler`: Umożliwia administratorom zatwierdzanie lub odrzucanie kontaktów oraz Edytowanie/usuwanie kontaktów.

## <a name="prerequisites"></a>Wymagania wstępne

Ten samouczek jest zaawansowany. Należy zapoznać się z:

* [ASP.NET Core](xref:tutorials/first-mvc-app/start-mvc)
* [Authentication](xref:security/authentication/identity)
* [Potwierdzenie konta i odzyskiwanie hasła](xref:security/authentication/accconfirm)
* [Autoryzacja](xref:security/authorization/introduction)
* [Entity Framework Core](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a>Aplikacja Starter i ukończona

[Pobierz](xref:index#how-to-download-a-sample) [ukończoną](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) aplikację. [Przetestuj](#test-the-completed-app) ukończoną aplikację, aby zapoznać się z jej funkcjami zabezpieczeń.

### <a name="the-starter-app"></a>Aplikacja początkowa

[Pobierz](xref:index#how-to-download-a-sample) aplikację [startową](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) .

Uruchom aplikację, naciśnij link **ContactManager** i sprawdź, czy można tworzyć, edytować i usuwać kontakty.

## <a name="secure-user-data"></a>Zabezpieczanie danych użytkownika

Poniższe sekcje zawierają wszystkie najważniejsze kroki umożliwiające utworzenie aplikacji zabezpieczonych danych użytkownika. Pomocne może być odwołanie do ukończonego projektu.

### <a name="tie-the-contact-data-to-the-user"></a>Powiązanie danych kontaktowych z użytkownikiem

Użyj [Identity](xref:security/authentication/identity) identyfikatora użytkownika ASP.NET, aby upewnić się, że użytkownicy mogą edytować swoje dane, ale nie inne dane użytkowników. Dodaj `OwnerID` i `ContactStatus` do `Contact` modelu:

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

`OwnerID`jest IDENTYFIKATORem użytkownika z `AspNetUser` tabeli w [Identity](xref:security/authentication/identity) bazie danych. `Status`Pole określa, czy kontakt jest widoczny dla użytkowników ogólnych.

Utwórz nową migrację i zaktualizuj bazę danych:

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a>Dodawanie usług ról do programuIdentity

Dołącz [Addroles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) , aby dodać usługi ról:

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

<a name="rau"></a>

### <a name="require-authenticated-users"></a>Wymagaj uwierzytelnionych użytkowników

Ustaw rezerwowe zasady uwierzytelniania, aby wymagać uwierzytelnienia użytkowników:

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=13-99)]

Poprzedni wyróżniony kod ustawia [rezerwowe zasady uwierzytelniania](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy). Rezerwowe zasady uwierzytelniania wymagają uwierzytelnienia ***wszystkich*** użytkowników, z wyjątkiem Razor stron, kontrolerów lub metod akcji z atrybutem uwierzytelniania. Na przykład Razor strony, kontrolery lub metody akcji z `[AllowAnonymous]` lub `[Authorize(PolicyName="MyPolicy")]` używają stosowanego atrybutu uwierzytelniania zamiast rezerwowych zasad uwierzytelniania.

Rezerwowe zasady uwierzytelniania:

* Stosuje się do wszystkich żądań, które nie określają jawnie zasad uwierzytelniania. W przypadku żądań obsłużonych przez Routing punktów końcowych może to obejmować wszystkie punkty końcowe, które nie określają atrybutu autoryzacji. W przypadku żądań obsłużonych przez inne oprogramowanie pośredniczące po wyzwoleniu na oprogramowanie pośredniczące, takie jak [pliki statyczne](xref:fundamentals/static-files), zasady te będą stosowane do wszystkich żądań.

Ustawienie rezerwowych zasad uwierzytelniania wymaga uwierzytelniania użytkowników chroniących nowo dodane Razor strony i kontrolery. Uwierzytelnianie wymagane domyślnie jest bezpieczniejsze niż poleganie na nowych kontrolerach i stronach w Razor celu uwzględnienia `[Authorize]` atrybutu.

<xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions>Klasa zawiera również <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType> . `DefaultPolicy`To zasady używane z `[Authorize]` atrybutem, jeśli nie określono żadnych zasad. `[Authorize]`nie zawiera nazwanych zasad, w przeciwieństwie do `[Authorize(PolicyName="MyPolicy")]` .

Aby uzyskać więcej informacji na temat zasad, zobacz <xref:security/authorization/policies> .

Alternatywny sposób, w jaki kontrolery MVC i Razor strony wymagające uwierzytelnienia wszystkich użytkowników są dodawane filtr autoryzacji:

[!code-csharp[](secure-data/samples/final3/Startup2.cs?name=snippet&highlight=14-99)]

Poprzedni kod używa filtru autoryzacji, ustawienie zasad powrotu używa routingu punktu końcowego. Ustawienie zasad powrotu jest preferowanym sposobem wymagania uwierzytelniania wszystkich użytkowników.

Dodaj [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) do `Index` stron i, `Privacy` aby anonimowi użytkownicy mogli uzyskać informacje o witrynie przed zarejestrowaniem:

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a>Konfigurowanie konta testowego

`SeedData`Klasa tworzy dwa konta: administrator i Menedżer. Użyj [Narzędzia Secret Manager](xref:security/app-secrets) , aby ustawić hasło dla tych kont. Ustaw hasło z katalogu projektu (katalog zawierający *program.cs*):

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

Jeśli nie określono silnego hasła, wyjątek jest zgłaszany, gdy `SeedData.Initialize` jest wywoływany.

Zaktualizuj `Main` , aby użyć hasła testu:

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a>Tworzenie kont testowych i aktualizowanie kontaktów

Zaktualizuj `Initialize` metodę w klasie, `SeedData` Aby utworzyć konta testowe:

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

Dodaj identyfikator użytkownika administratora i `ContactStatus` kontakty. Utwórz jeden z kontaktów "przesłane" i jeden "odrzucony". Dodaj identyfikator i stan użytkownika do wszystkich kontaktów. Pokazywany jest tylko jeden kontakt:

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a>Tworzenie programów do obsługi autoryzacji właściciela, Menedżera i administratora

Utwórz `ContactIsOwnerAuthorizationHandler` klasę w folderze *autoryzacji* . `ContactIsOwnerAuthorizationHandler`Sprawdza, czy użytkownik, który działa na zasobów, jest właścicielem zasobu.

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

`ContactIsOwnerAuthorizationHandler`Kontekst wywołań [. Powodzenie](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) , jeśli bieżący użytkownik uwierzytelniony jest właścicielem osoby kontaktowej. Obsługa autoryzacji zazwyczaj:

* Zwróć `context.Succeed` , gdy wymagania są spełnione.
* Zwracaj, `Task.CompletedTask` gdy wymagania nie są spełnione. `Task.CompletedTask`nie powiodło się lub niepowodzenie — &mdash; zezwala na uruchamianie innych programów obsługi autoryzacji.

Jeśli musisz jawnie niepowodzeniem, zwróć [kontekst. Nie powiodło się](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).

Aplikacja umożliwia właścicielom kontaktu Edytowanie/usuwanie/tworzenie własnych danych. `ContactIsOwnerAuthorizationHandler`nie trzeba sprawdzać operacji przesłanej w parametrze wymagania.

### <a name="create-a-manager-authorization-handler"></a>Tworzenie procedury obsługi autoryzacji Menedżera

Utwórz `ContactManagerAuthorizationHandler` klasę w folderze *autoryzacji* . `ContactManagerAuthorizationHandler`Sprawdza, czy użytkownik działający na tym zasobie jest menedżerem. Tylko menedżerowie mogą zatwierdzać lub odrzucać zmiany zawartości (nowe lub zmienione).

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a>Tworzenie procedury obsługi autoryzacji administratora

Utwórz `ContactAdministratorsAuthorizationHandler` klasę w folderze *autoryzacji* . `ContactAdministratorsAuthorizationHandler`Sprawdza, czy użytkownik działający na tym zasobie jest administratorem. Administrator może wykonać wszystkie operacje.

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a>Rejestrowanie programów obsługi autoryzacji

Usługi korzystające z Entity Framework Core muszą być zarejestrowane dla [iniekcji zależności](xref:fundamentals/dependency-injection) przy użyciu funkcji [addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions). `ContactIsOwnerAuthorizationHandler`Używa ASP.NET Core [Identity](xref:security/authentication/identity) , która jest oparta na Entity Framework Core. Zarejestruj procedury obsługi w kolekcji usług, aby były dostępne dla `ContactsController` [iniekcji](xref:fundamentals/dependency-injection)przez. Dodaj następujący kod na końcu `ConfigureServices` :

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

`ContactAdministratorsAuthorizationHandler`i `ContactManagerAuthorizationHandler` są dodawane jako pojedyncze. Są one pojedynczymi, ponieważ nie korzystają z EF, a wszystkie potrzebne informacje znajdują się w `Context` parametrze `HandleRequirementAsync` metody.

## <a name="support-authorization"></a>Obsługa autoryzacji

Ta sekcja służy do aktualizowania Razor stron i dodawania klasy wymagań dotyczących operacji.

### <a name="review-the-contact-operations-requirements-class"></a>Przeglądanie klasy wymagań operacji kontaktu

Zapoznaj się z `ContactOperations` klasą. Ta klasa zawiera wymagania obsługiwane przez aplikację:

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a>Utwórz klasę bazową dla stron kontaktów Razor

Utwórz klasę bazową zawierającą usługi używane na Razor stronach kontaktów. Klasa bazowa umieszcza kod inicjujący w jednej lokalizacji:

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

Powyższy kod ma następujące działanie:

* Dodaje `IAuthorizationService` usługę do dostępu do programów obsługi autoryzacji.
* Dodaje Identity `UserManager` usługę.
* Dodaj `ApplicationDbContext` .

### <a name="update-the-createmodel"></a>Aktualizowanie modelu

Zaktualizuj konstruktora Create Page model, aby używał `DI_BasePageModel` klasy bazowej:

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

Zaktualizuj `CreateModel.OnPostAsync` metodę do:

* Dodaj identyfikator użytkownika do `Contact` modelu.
* Wywołaj procedurę obsługi autoryzacji, aby upewnić się, że użytkownik ma uprawnienia do tworzenia kontaktów.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a>Aktualizowanie IndexModel

Zaktualizuj `OnGetAsync` metodę, tak aby tylko zatwierdzone kontakty były widoczne dla użytkowników ogólnych:

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a>Aktualizowanie EditModel

Dodaj procedurę obsługi autoryzacji, aby sprawdzić, czy użytkownik jest właścicielem osoby kontaktowej. Ponieważ autoryzacja zasobów jest sprawdzana, `[Authorize]` atrybut jest za mały. Aplikacja nie ma dostępu do zasobu, gdy są oceniane atrybuty. Autoryzacja na podstawie zasobów musi być bezwzględna. Testy muszą zostać wykonane, gdy aplikacja ma dostęp do zasobu, przez załadowanie go w modelu strony lub przez załadowanie go w ramach procedury obsługi. Często uzyskujesz dostęp do zasobu przez przekazanie klucza zasobu.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a>Aktualizowanie DeleteModel

Zaktualizuj model usuwania stron, aby użyć procedury obsługi autoryzacji do sprawdzenia, czy użytkownik ma uprawnienie do usuwania w kontakcie.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a>Wsuń usługę autoryzacji do widoków

Obecnie interfejs użytkownika zawiera linki do edycji i usuwania dla kontaktów, których użytkownik nie może modyfikować.

Wsuń usługę autoryzacji w pliku *Pages/_ViewImports. cshtml* , aby była dostępna dla wszystkich widoków:

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

Powyższy znacznik dodaje kilka `using` instrukcji.

Zaktualizuj linki **Edytuj** i **Usuń** w obszarze *strony/Kontakty/index. cshtml* , aby były renderowane tylko dla użytkowników z odpowiednimi uprawnieniami:

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> Ukrycie linków użytkowników, którzy nie mają uprawnień do zmiany danych, nie powoduje zabezpieczenia aplikacji. Ukrycie linków sprawia, że aplikacja jest bardziej przyjazny dla użytkownika, wyświetlając tylko prawidłowe linki. Użytkownicy mogą zahakerować wygenerowane adresy URL, aby wywoływać operacje edycji i usuwania na danych, które nie są właścicielami. RazorStrona lub kontroler muszą wymuszać testy dostępu w celu zabezpieczenia danych.

### <a name="update-details"></a>Szczegóły aktualizacji

Zaktualizuj widok szczegółów, aby menedżerowie mogli zatwierdzać lub odrzucać kontakty:

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

Zaktualizuj model strony szczegółów:

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a>Dodawanie użytkownika do roli lub usuwanie go

Zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/8502) , aby uzyskać informacje na temat:

* Usuwanie uprawnień użytkownika. Na przykład wyciszenie użytkownika w aplikacji czatu.
* Dodawanie uprawnień do użytkownika.

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a>Różnice między wyzwaniem i Zabroń

Ta aplikacja ustawia zasady domyślne, aby [wymagać uwierzytelnionych użytkowników](#rau). Poniższy kod umożliwia anonimowym użytkownikom. Użytkownicy anonimowi mogą wyświetlać różnice między wyzwaniem a zabranianiem.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

Powyższy kod ma następujące działanie:

* Jeśli użytkownik **nie** jest uwierzytelniony, `ChallengeResult` zwracany jest element. Gdy `ChallengeResult` jest zwracany, użytkownik zostanie przekierowany do strony logowania.
* Gdy użytkownik jest uwierzytelniany, ale nie jest autoryzowany, `ForbidResult` zwracany jest element. Gdy `ForbidResult` jest zwracany, użytkownik zostanie przekierowany do strony odmowa dostępu.

## <a name="test-the-completed-app"></a>Testowanie ukończonej aplikacji

Jeśli nie ustawiono jeszcze hasła dla kont użytkowników, użyj [Narzędzia Menedżera wpisów tajnych](xref:security/app-secrets#secret-manager) , aby ustawić hasło:

* Wybierz silne hasło: Użyj ośmiu lub więcej znaków oraz co najmniej jednego znaku wielkie litery, cyfry i symbolu. Na przykład `Passw0rd!` spełnia wymagania dotyczące silnych haseł.
* Wykonaj następujące polecenie w folderze projektu, gdzie `<PW>` to hasło:

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

Jeśli aplikacja ma kontakty:

* Usuń wszystkie rekordy z `Contact` tabeli.
* Uruchom ponownie aplikację, aby wypełniać bazę danych.

Prostym sposobem przetestowania ukończonej aplikacji jest uruchomienie trzech różnych przeglądarek (lub sesji incognito/InPrivate). W jednej przeglądarce Zarejestruj nowego użytkownika (na przykład `test@example.com` ). Zaloguj się do każdej przeglądarki za pomocą innego użytkownika. Sprawdź następujące operacje:

* Zarejestrowani użytkownicy mogą wyświetlać wszystkie zatwierdzone dane kontaktowe.
* Zarejestrowani użytkownicy mogą edytować/usuwać własne dane.
* Menedżerowie mogą zatwierdzać i odrzucać dane kontaktów. `Details`Widok zawiera przyciski **Zatwierdź** i **Odrzuć** .
* Administratorzy mogą zatwierdzić/odrzucić i edytować/usunąć wszystkie dane.

| Użytkownik                | Wypełnianie przez aplikację | Opcje                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | Nie                | Edytuj/Usuń własne dane.                |
| manager@contoso.com | Tak               | Zatwierdź/Odrzuć i edytuj/usuń własne dane. |
| admin@contoso.com   | Tak               | Zatwierdź/Odrzuć i edytuj/usuń wszystkie dane. |

Utwórz kontakt w przeglądarce administratora. Skopiuj adres URL służący do usuwania i edytowania z osoby kontaktowej administratora. Wklej te linki do przeglądarki użytkownika testowego, aby sprawdzić, czy użytkownik testowy nie może wykonać tych operacji.

## <a name="create-the-starter-app"></a>Tworzenie aplikacji Starter

* Tworzenie Razor aplikacji stronicowej o nazwie "contacter"
  * Utwórz aplikację przy użyciu **poszczególnych kont użytkowników**.
  * Nadaj mu nazwę "ContactName", aby przestrzeń nazw była zgodna z przestrzenią nazw używaną w przykładzie.
  * `-uld`Określa LocalDB zamiast oprogramowania SQLite

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* Dodaj *modele/Contact. cs*:

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* Tworzenie szkieletu `Contact` modelu.
* Utwórz migrację początkową i zaktualizuj bazę danych:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

Jeśli wystąpi usterka z `dotnet aspnet-codegenerator razorpage` poleceniem, zobacz [ten problem](https://github.com/aspnet/Scaffolding/issues/984)w usłudze GitHub.

* Zaktualizuj kotwicę **ContactManager** w pliku *Pages/Shared/_Layout. cshtml* :

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* Testowanie aplikacji przez tworzenie, edytowanie i usuwanie kontaktu

### <a name="seed-the-database"></a>Wypełnianie bazy danych

Dodaj klasę [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) do folderu *danych* :

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

Wywołanie `SeedData.Initialize` z `Main` :

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

Sprawdź, czy aplikacja wykorzystana z bazy danych. Jeśli w bazie danych kontaktów znajdują się jakieś wiersze, Metoda inicjatora nie zostanie uruchomiona.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

W tym samouczku pokazano, jak utworzyć aplikację sieci Web ASP.NET Core przy użyciu danych użytkownika chronionych przez autoryzację. Zostanie wyświetlona lista kontaktów, które zostały utworzone przez uwierzytelnionych (zarejestrowanych) użytkowników. Istnieją trzy grupy zabezpieczeń:

* **Zarejestrowani użytkownicy** mogą wyświetlać wszystkie zatwierdzone dane i edytować/usuwać własne dane.
* **Menedżerowie** mogą zatwierdzać lub odrzucać dane kontaktów. Tylko zatwierdzone kontakty są widoczne dla użytkowników.
* **Administratorzy** mogą zatwierdzić/odrzucić i edytować/usunąć dowolne dane.

Na poniższej ilustracji użytkownik Rick ( `rick@example.com` ) jest zalogowany. Rick mogą wyświetlać tylko zatwierdzone kontakty i **edytować** / **Usuń** / **Utwórz nowe** linki dla swoich kontaktów. Tylko ostatni rekord utworzony przez Rick zawiera linki do **edycji** i **usuwania** . Inni użytkownicy nie będą widzieć ostatniego rekordu do momentu zmiany stanu na "zatwierdzone" przez Menedżera lub administratora.

![Zrzut ekranu przedstawiający Rick zalogowany](secure-data/_static/rick.png)

Na poniższej ilustracji `manager@contoso.com` jest zalogowany i w roli menedżera:

![Zrzut ekranu przedstawiający manager@contoso.com zalogowany](secure-data/_static/manager1.png)

Na poniższej ilustracji przedstawiono widok szczegółów osoby kontaktowej:

![Widok kontaktu w Menedżerze](secure-data/_static/manager.png)

Przyciski **Zatwierdź** i **Odrzuć** są wyświetlane tylko dla menedżerów i administratorów.

Na poniższej ilustracji `admin@contoso.com` jest zalogowany i w roli administratora:

![Zrzut ekranu przedstawiający admin@contoso.com zalogowany](secure-data/_static/admin.png)

Administrator ma wszystkie uprawnienia. Może odczytywać/edytować/usuwać dowolne kontakty i zmieniać stan kontaktów.

Aplikacja została utworzona przez utworzenie [szkieletu](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) następującego `Contact` modelu:

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

Przykład zawiera następujące programy obsługi autoryzacji:

* `ContactIsOwnerAuthorizationHandler`: Zapewnia, że użytkownik może edytować tylko swoje dane.
* `ContactManagerAuthorizationHandler`: Umożliwia menedżerom zatwierdzanie lub odrzucanie kontaktów.
* `ContactAdministratorsAuthorizationHandler`: Umożliwia administratorom zatwierdzanie lub odrzucanie kontaktów oraz Edytowanie/usuwanie kontaktów.

## <a name="prerequisites"></a>Wymagania wstępne

Ten samouczek jest zaawansowany. Należy zapoznać się z:

* [ASP.NET Core](xref:tutorials/first-mvc-app/start-mvc)
* [Authentication](xref:security/authentication/identity)
* [Potwierdzenie konta i odzyskiwanie hasła](xref:security/authentication/accconfirm)
* [Autoryzacja](xref:security/authorization/introduction)
* [Entity Framework Core](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a>Aplikacja Starter i ukończona

[Pobierz](xref:index#how-to-download-a-sample) [ukończoną](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) aplikację. [Przetestuj](#test-the-completed-app) ukończoną aplikację, aby zapoznać się z jej funkcjami zabezpieczeń.

### <a name="the-starter-app"></a>Aplikacja początkowa

[Pobierz](xref:index#how-to-download-a-sample) aplikację [startową](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) .

Uruchom aplikację, naciśnij link **ContactManager** i sprawdź, czy można tworzyć, edytować i usuwać kontakty.

## <a name="secure-user-data"></a>Zabezpieczanie danych użytkownika

Poniższe sekcje zawierają wszystkie najważniejsze kroki umożliwiające utworzenie aplikacji zabezpieczonych danych użytkownika. Pomocne może być odwołanie do ukończonego projektu.

### <a name="tie-the-contact-data-to-the-user"></a>Powiązanie danych kontaktowych z użytkownikiem

Użyj [Identity](xref:security/authentication/identity) identyfikatora użytkownika ASP.NET, aby upewnić się, że użytkownicy mogą edytować swoje dane, ale nie inne dane użytkowników. Dodaj `OwnerID` i `ContactStatus` do `Contact` modelu:

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

`OwnerID`jest IDENTYFIKATORem użytkownika z `AspNetUser` tabeli w [Identity](xref:security/authentication/identity) bazie danych. `Status`Pole określa, czy kontakt jest widoczny dla użytkowników ogólnych.

Utwórz nową migrację i zaktualizuj bazę danych:

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a>Dodawanie usług ról do programuIdentity

Dołącz [Addroles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) , aby dodać usługi ról:

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=11)]

### <a name="require-authenticated-users"></a>Wymagaj uwierzytelnionych użytkowników

Ustaw domyślne zasady uwierzytelniania, aby wymagać uwierzytelnienia użytkowników:

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 Można zrezygnować z uwierzytelniania na Razor poziomie strony, kontrolera lub metody akcji przy użyciu `[AllowAnonymous]` atrybutu. Ustawienie domyślnych zasad uwierzytelniania wymaga uwierzytelniania użytkowników chroniących nowo dodane Razor strony i kontrolery. Uwierzytelnianie wymagane domyślnie jest bezpieczniejsze niż poleganie na nowych kontrolerach i stronach w Razor celu uwzględnienia `[Authorize]` atrybutu.

Dodaj [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) do strony indeks, informacje i kontakty, dzięki czemu anonimowi użytkownicy mogą uzyskać informacje o witrynie przed ich zarejestrowaniem.

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a>Konfigurowanie konta testowego

`SeedData`Klasa tworzy dwa konta: administrator i Menedżer. Użyj [Narzędzia Secret Manager](xref:security/app-secrets) , aby ustawić hasło dla tych kont. Ustaw hasło z katalogu projektu (katalog zawierający *program.cs*):

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

Jeśli nie określono silnego hasła, wyjątek jest zgłaszany, gdy `SeedData.Initialize` jest wywoływany.

Zaktualizuj `Main` , aby użyć hasła testu:

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a>Tworzenie kont testowych i aktualizowanie kontaktów

Zaktualizuj `Initialize` metodę w klasie, `SeedData` Aby utworzyć konta testowe:

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

Dodaj identyfikator użytkownika administratora i `ContactStatus` kontakty. Utwórz jeden z kontaktów "przesłane" i jeden "odrzucony". Dodaj identyfikator i stan użytkownika do wszystkich kontaktów. Pokazywany jest tylko jeden kontakt:

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a>Tworzenie programów do obsługi autoryzacji właściciela, Menedżera i administratora

Utwórz folder *autoryzacji* i Utwórz `ContactIsOwnerAuthorizationHandler` w nim klasę. `ContactIsOwnerAuthorizationHandler`Sprawdza, czy użytkownik, który działa na zasobów, jest właścicielem zasobu.

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

`ContactIsOwnerAuthorizationHandler`Kontekst wywołań [. Powodzenie](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) , jeśli bieżący użytkownik uwierzytelniony jest właścicielem osoby kontaktowej. Obsługa autoryzacji zazwyczaj:

* Zwróć `context.Succeed` , gdy wymagania są spełnione.
* Zwracaj, `Task.CompletedTask` gdy wymagania nie są spełnione. `Task.CompletedTask`nie powiodło się lub niepowodzenie — &mdash; zezwala na uruchamianie innych programów obsługi autoryzacji.

Jeśli musisz jawnie niepowodzeniem, zwróć [kontekst. Nie powiodło się](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).

Aplikacja umożliwia właścicielom kontaktu Edytowanie/usuwanie/tworzenie własnych danych. `ContactIsOwnerAuthorizationHandler`nie trzeba sprawdzać operacji przesłanej w parametrze wymagania.

### <a name="create-a-manager-authorization-handler"></a>Tworzenie procedury obsługi autoryzacji Menedżera

Utwórz `ContactManagerAuthorizationHandler` klasę w folderze *autoryzacji* . `ContactManagerAuthorizationHandler`Sprawdza, czy użytkownik działający na tym zasobie jest menedżerem. Tylko menedżerowie mogą zatwierdzać lub odrzucać zmiany zawartości (nowe lub zmienione).

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a>Tworzenie procedury obsługi autoryzacji administratora

Utwórz `ContactAdministratorsAuthorizationHandler` klasę w folderze *autoryzacji* . `ContactAdministratorsAuthorizationHandler`Sprawdza, czy użytkownik działający na tym zasobie jest administratorem. Administrator może wykonać wszystkie operacje.

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a>Rejestrowanie programów obsługi autoryzacji

Usługi korzystające z Entity Framework Core muszą być zarejestrowane dla [iniekcji zależności](xref:fundamentals/dependency-injection) przy użyciu funkcji [addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions). `ContactIsOwnerAuthorizationHandler`Używa ASP.NET Core [Identity](xref:security/authentication/identity) , która jest oparta na Entity Framework Core. Zarejestruj procedury obsługi w kolekcji usług, aby były dostępne dla `ContactsController` [iniekcji](xref:fundamentals/dependency-injection)przez. Dodaj następujący kod na końcu `ConfigureServices` :

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

`ContactAdministratorsAuthorizationHandler`i `ContactManagerAuthorizationHandler` są dodawane jako pojedyncze. Są one pojedynczymi, ponieważ nie korzystają z EF, a wszystkie potrzebne informacje znajdują się w `Context` parametrze `HandleRequirementAsync` metody.

## <a name="support-authorization"></a>Obsługa autoryzacji

Ta sekcja służy do aktualizowania Razor stron i dodawania klasy wymagań dotyczących operacji.

### <a name="review-the-contact-operations-requirements-class"></a>Przeglądanie klasy wymagań operacji kontaktu

Zapoznaj się z `ContactOperations` klasą. Ta klasa zawiera wymagania obsługiwane przez aplikację:

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a>Utwórz klasę bazową dla stron kontaktów Razor

Utwórz klasę bazową zawierającą usługi używane na Razor stronach kontaktów. Klasa bazowa umieszcza kod inicjujący w jednej lokalizacji:

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

Powyższy kod ma następujące działanie:

* Dodaje `IAuthorizationService` usługę do dostępu do programów obsługi autoryzacji.
* Dodaje Identity `UserManager` usługę.
* Dodaj `ApplicationDbContext` .

### <a name="update-the-createmodel"></a>Aktualizowanie modelu

Zaktualizuj konstruktora Create Page model, aby używał `DI_BasePageModel` klasy bazowej:

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

Zaktualizuj `CreateModel.OnPostAsync` metodę do:

* Dodaj identyfikator użytkownika do `Contact` modelu.
* Wywołaj procedurę obsługi autoryzacji, aby upewnić się, że użytkownik ma uprawnienia do tworzenia kontaktów.

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a>Aktualizowanie IndexModel

Zaktualizuj `OnGetAsync` metodę, tak aby tylko zatwierdzone kontakty były widoczne dla użytkowników ogólnych:

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a>Aktualizowanie EditModel

Dodaj procedurę obsługi autoryzacji, aby sprawdzić, czy użytkownik jest właścicielem osoby kontaktowej. Ponieważ autoryzacja zasobów jest sprawdzana, `[Authorize]` atrybut jest za mały. Aplikacja nie ma dostępu do zasobu, gdy są oceniane atrybuty. Autoryzacja na podstawie zasobów musi być bezwzględna. Testy muszą zostać wykonane, gdy aplikacja ma dostęp do zasobu, przez załadowanie go w modelu strony lub przez załadowanie go w ramach procedury obsługi. Często uzyskujesz dostęp do zasobu przez przekazanie klucza zasobu.

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a>Aktualizowanie DeleteModel

Zaktualizuj model usuwania stron, aby użyć procedury obsługi autoryzacji do sprawdzenia, czy użytkownik ma uprawnienie do usuwania w kontakcie.

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a>Wsuń usługę autoryzacji do widoków

Obecnie interfejs użytkownika zawiera linki do edycji i usuwania dla kontaktów, których użytkownik nie może modyfikować.

Wsuń usługę autoryzacji w pliku *views/_ViewImports. cshtml* , aby była dostępna dla wszystkich widoków:

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

Powyższy znacznik dodaje kilka `using` instrukcji.

Zaktualizuj linki **Edytuj** i **Usuń** w obszarze *strony/Kontakty/index. cshtml* , aby były renderowane tylko dla użytkowników z odpowiednimi uprawnieniami:

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> Ukrycie linków użytkowników, którzy nie mają uprawnień do zmiany danych, nie powoduje zabezpieczenia aplikacji. Ukrycie linków sprawia, że aplikacja jest bardziej przyjazny dla użytkownika, wyświetlając tylko prawidłowe linki. Użytkownicy mogą zahakerować wygenerowane adresy URL, aby wywoływać operacje edycji i usuwania na danych, które nie są właścicielami. RazorStrona lub kontroler muszą wymuszać testy dostępu w celu zabezpieczenia danych.

### <a name="update-details"></a>Szczegóły aktualizacji

Zaktualizuj widok szczegółów, aby menedżerowie mogli zatwierdzać lub odrzucać kontakty:

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

Zaktualizuj model strony szczegółów:

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a>Dodawanie użytkownika do roli lub usuwanie go

Zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/8502) , aby uzyskać informacje na temat:

* Usuwanie uprawnień użytkownika. Na przykład wyciszenie użytkownika w aplikacji czatu.
* Dodawanie uprawnień do użytkownika.

## <a name="test-the-completed-app"></a>Testowanie ukończonej aplikacji

Jeśli nie ustawiono jeszcze hasła dla kont użytkowników, użyj [Narzędzia Menedżera wpisów tajnych](xref:security/app-secrets#secret-manager) , aby ustawić hasło:

* Wybierz silne hasło: Użyj ośmiu lub więcej znaków oraz co najmniej jednego znaku wielkie litery, cyfry i symbolu. Na przykład `Passw0rd!` spełnia wymagania dotyczące silnych haseł.
* Wykonaj następujące polecenie w folderze projektu, gdzie `<PW>` to hasło:

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* Porzuć i zaktualizuj bazę danych

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* Uruchom ponownie aplikację, aby wypełniać bazę danych.

Prostym sposobem przetestowania ukończonej aplikacji jest uruchomienie trzech różnych przeglądarek (lub sesji incognito/InPrivate). W jednej przeglądarce Zarejestruj nowego użytkownika (na przykład `test@example.com` ). Zaloguj się do każdej przeglądarki za pomocą innego użytkownika. Sprawdź następujące operacje:

* Zarejestrowani użytkownicy mogą wyświetlać wszystkie zatwierdzone dane kontaktowe.
* Zarejestrowani użytkownicy mogą edytować/usuwać własne dane.
* Menedżerowie mogą zatwierdzać i odrzucać dane kontaktów. `Details`Widok zawiera przyciski **Zatwierdź** i **Odrzuć** .
* Administratorzy mogą zatwierdzić/odrzucić i edytować/usunąć wszystkie dane.

| Użytkownik                | Wypełnianie przez aplikację | Opcje                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | Nie                | Edytuj/Usuń własne dane.                |
| manager@contoso.com | Tak               | Zatwierdź/Odrzuć i edytuj/usuń własne dane. |
| admin@contoso.com   | Tak               | Zatwierdź/Odrzuć i edytuj/usuń wszystkie dane. |

Utwórz kontakt w przeglądarce administratora. Skopiuj adres URL służący do usuwania i edytowania z osoby kontaktowej administratora. Wklej te linki do przeglądarki użytkownika testowego, aby sprawdzić, czy użytkownik testowy nie może wykonać tych operacji.

## <a name="create-the-starter-app"></a>Tworzenie aplikacji Starter

* Tworzenie Razor aplikacji stronicowej o nazwie "contacter"
  * Utwórz aplikację przy użyciu **poszczególnych kont użytkowników**.
  * Nadaj mu nazwę "ContactName", aby przestrzeń nazw była zgodna z przestrzenią nazw używaną w przykładzie.
  * `-uld`Określa LocalDB zamiast oprogramowania SQLite

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* Dodaj *modele/Contact. cs*:

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* Tworzenie szkieletu `Contact` modelu.
* Utwórz migrację początkową i zaktualizuj bazę danych:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* Zaktualizuj kotwicę **ContactManager** w pliku *pages/_Layout. cshtml* :

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* Testowanie aplikacji przez tworzenie, edytowanie i usuwanie kontaktu

### <a name="seed-the-database"></a>Wypełnianie bazy danych

Dodaj klasę [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) do folderu *danych* .

Wywołanie `SeedData.Initialize` z `Main` :

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

Sprawdź, czy aplikacja wykorzystana z bazy danych. Jeśli w bazie danych kontaktów znajdują się jakieś wiersze, Metoda inicjatora nie zostanie uruchomiona.

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a>Zasoby dodatkowe

* [Tworzenie aplikacji internetowej platformy .NET Core i usługi SQL Database w usłudze Azure App Service](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [ASP.NET Core laboratorium autoryzacji](https://github.com/blowdart/AspNetAuthorizationWorkshop). To laboratorium prowadzi do bardziej szczegółowych informacji na temat funkcji zabezpieczeń wprowadzonych w tym samouczku.
* <xref:security/authorization/introduction>
* [Niestandardowa Autoryzacja oparta na zasadach](xref:security/authorization/policies)
