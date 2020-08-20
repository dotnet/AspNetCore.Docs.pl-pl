---
title: Wprowadzenie do Identity ASP.NET Core
author: rick-anderson
description: Używanie Identity z aplikacją ASP.NET Core. Dowiedz się, jak ustawiać wymagania dotyczące haseł (RequireDigit, RequiredLength, RequiredUniqueChars itd.).
ms.author: riande
ms.date: 7/15/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity
ms.openlocfilehash: 1d14a3668192a165ea5025ae0575a8e3d6dfd8e4
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633256"
---
# <a name="introduction-to-no-locidentity-on-aspnet-core"></a>Wprowadzenie do Identity ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core Identity:

* Jest interfejsem API, który obsługuje funkcje logowania interfejsu użytkownika.
* Zarządza użytkownikami, hasłami, danymi profilu, rolami, oświadczeniami, tokenami, potwierdzeniem wiadomości e-mail i innymi.

Użytkownicy mogą utworzyć konto z informacjami logowania przechowywanymi w Identity usłudze lub korzystać z zewnętrznego dostawcy logowania. Obsługiwane zewnętrzne dostawcy logowania obejmują [serwis Facebook, Google, konto Microsoft i Twitter](xref:security/authentication/social/index).

[!INCLUDE[](~/includes/requireAuth.md)]

[ Identity Kod źródłowy](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) jest dostępny w serwisie GitHub. [Szkielet Identity ](xref:security/authentication/scaffold-identity) i Wyświetl wygenerowane pliki w celu przejrzenia interakcji z szablonem Identity .

Identity jest zazwyczaj konfigurowany przy użyciu bazy danych SQL Server do przechowywania nazw użytkowników, haseł i danych profilu. Alternatywnie można użyć innego magazynu trwałego, na przykład Azure Table Storage.

W tym temacie dowiesz się, jak używać Identity do rejestrowania, logowania i wylogowywania użytkownika. Uwaga: szablony traktują nazwy użytkownika i wiadomości e-mail jako takie same dla użytkowników. Aby uzyskać bardziej szczegółowe instrukcje dotyczące tworzenia aplikacji, które używają Identity , zobacz [następne kroki](#next).

[Platforma tożsamości firmy Microsoft](/azure/active-directory/develop/) to:

* Ewolucja platformy deweloperów Azure Active Directory (Azure AD).
* Niepowiązane z ASP.NET Core Identity .

[!INCLUDE[](~/includes/IdentityServer4.md)]

[Wyświetl lub Pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([jak pobrać](xref:index#how-to-download-a-sample)).

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a>Tworzenie aplikacji sieci Web z uwierzytelnianiem

Utwórz projekt aplikacji sieci Web ASP.NET Core przy użyciu poszczególnych kont użytkowników.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Wybierz kolejno pozycje **Plik** > **Nowy** > **Projekt**.
* Wybierz **ASP.NET Core aplikacji sieci Web**. Nazwij projekt **WebApp1** tak, aby miał tę samą przestrzeń nazw co pobieranie projektu. Kliknij pozycję **OK**.
* Wybierz **aplikację sieci Web**ASP.NET Core, a następnie wybierz pozycję **Zmień uwierzytelnianie**.
* Wybierz pozycję **indywidualne konta użytkowników** , a następnie kliknij przycisk **OK**.

# <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

Poprzednie polecenie tworzy Razor aplikację internetową przy użyciu oprogramowania SQLite. Aby utworzyć aplikację sieci Web za pomocą LocalDB, uruchom następujące polecenie:

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

Wygenerowany projekt jest udostępniany [ASP.NET Core Identity](xref:security/authentication/identity) jako [ Razor Biblioteka klas](xref:razor-pages/ui-class). Identity Razor Biblioteka klas uwidacznia punkty końcowe z `Identity` obszarem. Na przykład:

* /Identity/Account/Login
* /Identity/Account/Logout
* /Identity/Account/Manage

### <a name="apply-migrations"></a>Zastosuj migracje

Zastosuj migracje, aby zainicjować bazę danych.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Uruchom następujące polecenie w konsoli Menedżera pakietów (PMC):

`PM> Update-Database`

# <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli)

W przypadku korzystania z oprogramowania SQLite migracja nie jest konieczna.

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

W przypadku LocalDB Uruchom następujące polecenie:

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a>Testuj rejestr i logowanie

Uruchom aplikację i zarejestruj użytkownika. W zależności od rozmiaru ekranu może być konieczne wybranie przycisku przełącznika nawigacji, aby wyświetlić linki **rejestru** i **logowania** .

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-no-locidentity-services"></a>Konfigurowanie Identity usług

Usługi są dodawane do programu `ConfigureServices` . Typowym wzorcem jest wywoływanie wszystkich `Add{Service}` metod, a następnie wywoływanie wszystkich `services.Configure{Service}` metod.

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=11-99)]

Poprzedni wyróżniony kod konfiguruje Identity domyślne wartości opcji. Usługi są udostępniane aplikacji za pomocą [iniekcji zależności](xref:fundamentals/dependency-injection).

Identity jest włączona przez wywołanie <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> . `UseAuthentication` dodaje [oprogramowanie pośredniczące](xref:fundamentals/middleware/index) uwierzytelniania do potoku żądania.

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

Aplikacja wygenerowana przez szablon nie korzysta z [autoryzacji](xref:security/authorization/secure-data). `app.UseAuthorization` jest uwzględniony w celu upewnienia się, że jest ona dodana w odpowiedniej kolejności, gdyby aplikacja mogła dodać autoryzację. `UseRouting`, `UseAuthentication` , `UseAuthorization` i `UseEndpoints` muszą być wywoływane w kolejności pokazanej w powyższym kodzie.

Aby uzyskać więcej informacji na temat programów `IdentityOptions` i `Startup` , zobacz <xref:Microsoft.AspNetCore.Identity.IdentityOptions> i [Uruchamianie aplikacji](xref:fundamentals/startup).

## <a name="scaffold-register-login-logout-and-registerconfirmation"></a>Rejestrowanie szkieletów, logowanie, wylogowywanie i RegisterConfirmation

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Dodaj `Register` pliki, `Login` , `LogOut` i `RegisterConfirmation` . Postępuj zgodnie z informacjami o [tożsamości szkieletowej w Razor projekcie z instrukcjami autoryzacji](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) w celu wygenerowania kodu pokazanego w tej sekcji.

# <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli)

Jeśli utworzono projekt o nazwie **WebApp1**, uruchom następujące polecenia. W przeciwnym razie użyj prawidłowej przestrzeni nazw dla `ApplicationDbContext` :

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.RegisterConfirmation"
```

Program PowerShell używa średnika jako separatora poleceń. W przypadku korzystania z programu PowerShell wpisz średniki na liście plików lub Umieść listę plików w podwójnym cudzysłowie, jak pokazano w powyższym przykładzie.

Aby uzyskać więcej informacji na temat tworzenia szkieletów Identity , zobacz [tożsamość szkieletu w Razor projekcie z autoryzacją](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).

---

### <a name="examine-register"></a>Badaj rejestr

Gdy użytkownik kliknie przycisk **zarejestruj** na `Register` stronie, `RegisterModel.OnPostAsync` Akcja jest wywoływana. Użytkownik jest tworzony przez wartość [IsAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) na `_userManager` obiekcie:

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<!-- .NET 5 fixes this, see
https://github.com/dotnet/aspnetcore/blob/master/src/Identity/UI/src/Areas/Identity/Pages/V4/Account/RegisterConfirmation.cshtml.cs#L74-L77
-->
[!INCLUDE[](~/includes/disableVer.md)]

### <a name="log-in"></a>Logowanie się

Formularz logowania jest wyświetlany, gdy:

* Wybrano łącze **Zaloguj** .
* Użytkownik próbuje uzyskać dostęp do strony z ograniczeniami, do której nie ma uprawnień dostępu **lub** kiedy nie został uwierzytelniony przez system.

Po przesłaniu formularza na stronie logowania `OnPostAsync` zostanie wywołana akcja. `PasswordSignInAsync` jest wywoływana dla `_signInManager` obiektu.

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

Aby uzyskać informacje na temat sposobu podejmowania decyzji dotyczących autoryzacji, zobacz <xref:security/authorization/introduction> .

### <a name="log-out"></a>Wyloguj

Link **Wyloguj** wywołuje `LogoutModel.OnPost` akcję. 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

W powyższym kodzie kod `return RedirectToPage();` musi być przekierowaniem, aby przeglądarka wykonywała nowe żądanie, a tożsamość użytkownika zostanie zaktualizowana.

[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) czyści oświadczenia użytkownika przechowywane w cookie .

Wpis jest określony na *stronie/Shared/_LoginPartial. cshtml*:

[!code-cshtml[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-no-locidentity"></a>Badan Identity

Domyślne szablony projektu sieci Web umożliwiają anonimowy dostęp do stron głównych. Aby przetestować Identity , Dodaj [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) :

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

Jeśli użytkownik jest zalogowany, Wyloguj się. Uruchom aplikację i wybierz łącze **prywatność** . Nastąpi przekierowanie do strony logowania.

### <a name="explore-no-locidentity"></a>Przeglądać Identity

Aby poznać Identity więcej szczegółów:

* [Utwórz źródło interfejsu użytkownika pełnej tożsamości](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* Przejrzyj Źródło poszczególnych stron i przechodzenie przez debuger.

## <a name="no-locidentity-components"></a>Identity Komponentów

Wszystkie Identity zależne pakiety NuGet są zawarte w [ASP.NET Core udostępnionej platformie](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).

Podstawowym pakietem Identity programu jest [Microsoft. AspNetCore. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/). Ten pakiet zawiera podstawowy zestaw interfejsów dla programu ASP.NET Core Identity i jest dołączany przez program `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .

## <a name="migrating-to-no-locaspnet-core-identity"></a>Migrowanie do programu ASP.NET Core Identity

Aby uzyskać więcej informacji i wskazówek dotyczących migrowania istniejącego Identity magazynu, zobacz [Migrowanie Identity uwierzytelniania i ](xref:migration/identity).

## <a name="setting-password-strength"></a>Ustawianie siły hasła

Zobacz [Konfiguracja](#pw) dla przykładu, który ustawia minimalne wymagania dotyczące hasła.

## <a name="adddefaultno-locidentity-and-addno-locidentity"></a>ADDDEFAULT Identity i AddIdentity

<xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> wprowadzono w ASP.NET Core 2,1. Wywołanie `AddDefaultIdentity` jest podobne do wywołania następujących:

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

Aby uzyskać więcej informacji, zobacz [ADDDEFAULT Identity Source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .

## <a name="prevent-publish-of-static-no-locidentity-assets"></a>Zapobiegaj publikowaniu Identity zasobów statycznych

Aby uniemożliwić publikowanie statycznych Identity zasobów (arkuszy stylów i plików JavaScript dla Identity interfejsu użytkownika) w katalogu głównym sieci Web, Dodaj następującą `ResolveStaticWebAssetsInputsDependsOn` Właściwość i `RemoveIdentityAssets` obiekt docelowy do pliku projektu aplikacji:

```xml
<PropertyGroup>
  <ResolveStaticWebAssetsInputsDependsOn>RemoveIdentityAssets</ResolveStaticWebAssetsInputsDependsOn>
</PropertyGroup>

<Target Name="RemoveIdentityAssets">
  <ItemGroup>
    <StaticWebAsset Remove="@(StaticWebAsset)" Condition="%(SourceId) == 'Microsoft.AspNetCore.Identity.UI'" />
  </ItemGroup>
</Target>
```

<a name="next"></a>

## <a name="next-steps"></a>Następne kroki

* [ASP.NET Core Identity kod źródłowy](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)
* Zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/5131) w usłudze GitHub, aby uzyskać informacje dotyczące konfigurowania Identity za pomocą oprogramowania SQLite.
* [Ponowne Identity](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core Identity jest systemem członkostwa, który dodaje funkcje logowania do ASP.NET Core aplikacji. Użytkownicy mogą utworzyć konto z informacjami logowania przechowywanymi w Identity usłudze lub korzystać z zewnętrznego dostawcy logowania. Obsługiwane zewnętrzne dostawcy logowania obejmują [serwis Facebook, Google, konto Microsoft i Twitter](xref:security/authentication/social/index).

Identity można skonfigurować przy użyciu bazy danych SQL Server do przechowywania nazw użytkowników, haseł i danych profilu. Alternatywnie można użyć innego magazynu trwałego, na przykład Azure Table Storage.

[Wyświetl lub Pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([jak pobrać](xref:index#how-to-download-a-sample)).

W tym temacie dowiesz się, jak używać Identity do rejestrowania, logowania i wylogowywania użytkownika. Aby uzyskać bardziej szczegółowe instrukcje dotyczące tworzenia aplikacji, które używają Identity , zobacz sekcję następne kroki na końcu tego artykułu.

<a name="adi"></a>

## <a name="adddefaultno-locidentity-and-addno-locidentity"></a>ADDDEFAULT Identity i AddIdentity

<xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> wprowadzono w ASP.NET Core 2,1. Wywołanie `AddDefaultIdentity` jest podobne do wywołania następujących:

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

Aby uzyskać więcej informacji, zobacz [ADDDEFAULT Identity Source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .

## <a name="create-a-web-app-with-authentication"></a>Tworzenie aplikacji sieci Web z uwierzytelnianiem

Utwórz projekt aplikacji sieci Web ASP.NET Core przy użyciu poszczególnych kont użytkowników.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Wybierz kolejno pozycje **Plik** > **Nowy** > **Projekt**.
* Wybierz **ASP.NET Core aplikacji sieci Web**. Nazwij projekt **WebApp1** tak, aby miał tę samą przestrzeń nazw co pobieranie projektu. Kliknij pozycję **OK**.
* Wybierz **aplikację sieci Web**ASP.NET Core, a następnie wybierz pozycję **Zmień uwierzytelnianie**.
* Wybierz pozycję **indywidualne konta użytkowników** , a następnie kliknij przycisk **OK**.

# <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

Wygenerowany projekt jest udostępniany [ASP.NET Core Identity](xref:security/authentication/identity) jako [ Razor Biblioteka klas](xref:razor-pages/ui-class). Identity Razor Biblioteka klas uwidacznia punkty końcowe z `Identity` obszarem. Na przykład:

* /Identity/Account/Login
* /Identity/Account/Logout
* /Identity/Account/Manage

### <a name="apply-migrations"></a>Zastosuj migracje

Zastosuj migracje, aby zainicjować bazę danych.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Uruchom następujące polecenie w konsoli Menedżera pakietów (PMC):

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a>Testuj rejestr i logowanie

Uruchom aplikację i zarejestruj użytkownika. W zależności od rozmiaru ekranu może być konieczne wybranie przycisku przełącznika nawigacji, aby wyświetlić linki **rejestru** i **logowania** .

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-no-locidentity-services"></a>Konfigurowanie Identity usług

Usługi są dodawane do programu `ConfigureServices` . Typowym wzorcem jest wywoływanie wszystkich `Add{Service}` metod, a następnie wywoływanie wszystkich `services.Configure{Service}` metod.

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

Poprzedni kod konfiguruje Identity z użyciem domyślnych wartości opcji. Usługi są udostępniane aplikacji za pomocą [iniekcji zależności](xref:fundamentals/dependency-injection).

Identity jest włączona przez wywołanie [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_). `UseAuthentication` dodaje [oprogramowanie pośredniczące](xref:fundamentals/middleware/index) uwierzytelniania do potoku żądania.

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

Aby uzyskać więcej informacji, zobacz [ Identity klasy opcji](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) i [uruchamiania aplikacji](xref:fundamentals/startup).

## <a name="scaffold-register-login-and-logout"></a>Rejestrowanie, logowanie i wylogowywanie szkieletu

Postępuj zgodnie z informacjami o [tożsamości szkieletowej w Razor projekcie z instrukcjami autoryzacji](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) w celu wygenerowania kodu pokazanego w tej sekcji.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Dodaj pliki rejestru, logowania i wylogowywania.

# <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli)

Jeśli utworzono projekt o nazwie **WebApp1**, uruchom następujące polecenia. W przeciwnym razie użyj prawidłowej przestrzeni nazw dla `ApplicationDbContext` :

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

Program PowerShell używa średnika jako separatora poleceń. W przypadku korzystania z programu PowerShell wpisz średniki na liście plików lub Umieść listę plików w podwójnym cudzysłowie, jak pokazano w powyższym przykładzie.

---

### <a name="examine-register"></a>Badaj rejestr

Gdy użytkownik kliknie łącze **zarejestruj** , `RegisterModel.OnPostAsync` zostanie wywołana akcja. Użytkownik jest tworzony przez wartość [IsAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) na `_userManager` obiekcie:

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

Jeśli użytkownik został utworzony pomyślnie, użytkownik jest zalogowany przez wywołanie `_signInManager.SignInAsync` .

**Uwaga:** Zobacz [potwierdzenie konta](xref:security/authentication/accconfirm#prevent-login-at-registration) , aby zapobiec natychmiastowemu logowaniu przy rejestracji.

### <a name="log-in"></a>Logowanie się

Formularz logowania jest wyświetlany, gdy:

* Wybrano łącze **Zaloguj** .
* Użytkownik próbuje uzyskać dostęp do strony z ograniczeniami, do której nie ma uprawnień dostępu **lub** kiedy nie został uwierzytelniony przez system.

Po przesłaniu formularza na stronie logowania `OnPostAsync` zostanie wywołana akcja. `PasswordSignInAsync` jest wywoływana dla `_signInManager` obiektu.

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

Aby uzyskać informacje na temat sposobu podejmowania decyzji dotyczących autoryzacji, zobacz <xref:security/authorization/introduction> .

### <a name="log-out"></a>Wyloguj

Link **Wyloguj** wywołuje `LogoutModel.OnPost` akcję. 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) czyści oświadczenia użytkownika przechowywane w cookie .

Wpis jest określony na *stronie/Shared/_LoginPartial. cshtml*:

[!code-cshtml[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-no-locidentity"></a>Badan Identity

Domyślne szablony projektu sieci Web umożliwiają anonimowy dostęp do stron głównych. Aby przetestować Identity , Dodaj [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) stronę prywatność.

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

Jeśli użytkownik jest zalogowany, Wyloguj się. Uruchom aplikację i wybierz łącze **prywatność** . Nastąpi przekierowanie do strony logowania.

### <a name="explore-no-locidentity"></a>Przeglądać Identity

Aby poznać Identity więcej szczegółów:

* [Utwórz źródło interfejsu użytkownika pełnej tożsamości](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* Przejrzyj Źródło poszczególnych stron i przechodzenie przez debuger.

## <a name="no-locidentity-components"></a>Identity Komponentów

Wszystkie Identity zależne pakiety NuGet są zawarte w [pakiecie Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).

Podstawowym pakietem Identity programu jest [Microsoft. AspNetCore. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/). Ten pakiet zawiera podstawowy zestaw interfejsów dla programu ASP.NET Core Identity i jest dołączany przez program `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .

## <a name="migrating-to-no-locaspnet-core-identity"></a>Migrowanie do programu ASP.NET Core Identity

Aby uzyskać więcej informacji i wskazówek dotyczących migrowania istniejącego Identity magazynu, zobacz [Migrowanie Identity uwierzytelniania i ](xref:migration/identity).

## <a name="setting-password-strength"></a>Ustawianie siły hasła

Zobacz [Konfiguracja](#pw) dla przykładu, który ustawia minimalne wymagania dotyczące hasła.

## <a name="next-steps"></a>Następne kroki

* Zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/5131) w usłudze GitHub, aby uzyskać informacje dotyczące konfigurowania Identity za pomocą oprogramowania SQLite.
* [Ponowne Identity](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
