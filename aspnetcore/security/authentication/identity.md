---
title: Wprowadzenie do tożsamości na ASP.NET Core
author: rick-anderson
description: Użyj tożsamości z aplikacją ASP.NET Core. Dowiedz się, jak ustawiać wymagania dotyczące haseł (RequireDigit, RequiredLength, RequiredUniqueChars itd.).
ms.author: riande
ms.date: 01/15/2020
uid: security/authentication/identity
ms.openlocfilehash: 4bc5f206b3aee7c2d34055703acc5b6c5218f964
ms.sourcegitcommit: 56861af66bb364a5d60c3c72d133d854b4cf292d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82205946"
---
# <a name="introduction-to-identity-on-aspnet-core"></a>Wprowadzenie do tożsamości na ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core tożsamość:

* Jest interfejsem API, który obsługuje funkcje logowania interfejsu użytkownika.
* Zarządza użytkownikami, hasłami, danymi profilu, rolami, oświadczeniami, tokenami, potwierdzeniem wiadomości e-mail i innymi.

Użytkownicy mogą utworzyć konto z informacjami logowania przechowywanymi w tożsamości lub korzystać z zewnętrznego dostawcy logowania. Obsługiwane zewnętrzne dostawcy logowania obejmują [serwis Facebook, Google, konto Microsoft i Twitter](xref:security/authentication/social/index).

[Kod źródłowy tożsamości](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) jest dostępny w serwisie GitHub. [Tożsamość szkieletu](xref:security/authentication/scaffold-identity) i wyświetlanie wygenerowanych plików w celu przejrzenia interakcji szablonu z tożsamością.

Tożsamość jest zazwyczaj konfigurowana przy użyciu bazy danych SQL Server do przechowywania nazw użytkowników, haseł i danych profilu. Alternatywnie można użyć innego magazynu trwałego, na przykład Azure Table Storage.

W tym temacie dowiesz się, jak używać tożsamości do rejestrowania, logowania i wylogowywania użytkownika. Uwaga: szablony traktują nazwy użytkownika i wiadomości e-mail jako takie same dla użytkowników. Aby uzyskać bardziej szczegółowe instrukcje dotyczące tworzenia aplikacji korzystających z tożsamości, zobacz sekcję następne kroki na końcu tego artykułu.

[Platforma tożsamości firmy Microsoft](/azure/active-directory/develop/) to:

* Ewolucja platformy deweloperów Azure Active Directory (Azure AD).
* Niepowiązane z tożsamością ASP.NET Core.

[!INCLUDE[](~/includes/IdentityServer4.md)]

[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([jak pobrać)](xref:index#how-to-download-a-sample)).

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a>Tworzenie aplikacji sieci Web z uwierzytelnianiem

Utwórz projekt aplikacji sieci Web ASP.NET Core przy użyciu poszczególnych kont użytkowników.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Wybierz kolejno pozycje **Plik** > **Nowy** > **Projekt**.
* Wybierz **ASP.NET Core aplikacji sieci Web**. Nazwij projekt **WebApp1** tak, aby miał tę samą przestrzeń nazw co pobieranie projektu. Kliknij przycisk **OK**.
* Wybierz **aplikację sieci Web**ASP.NET Core, a następnie wybierz pozycję **Zmień uwierzytelnianie**.
* Wybierz pozycję **indywidualne konta użytkowników** , a następnie kliknij przycisk **OK**.

# <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

Poprzednie polecenie tworzy aplikację sieci Web Razor przy użyciu oprogramowania SQLite. Aby utworzyć aplikację sieci Web za pomocą LocalDB, uruchom następujące polecenie:

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

Wygenerowany projekt zapewnia [ASP.NET Core tożsamość](xref:security/authentication/identity) jako [bibliotekę klas Razor](xref:razor-pages/ui-class). Biblioteka klas Razor tożsamość ujawnia punkty końcowe z `Identity` obszarem. Przykład:

* /Identity/Account/Login
* /Identity/Account/Logout
* /Identity/Account/Manage

### <a name="apply-migrations"></a>Zastosuj migracje

Zastosuj migracje, aby zainicjować bazę danych.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Uruchom następujące polecenie w konsoli Menedżera pakietów (PMC):

`PM> Update-Database`

# <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/netcore-cli)

W przypadku korzystania z oprogramowania SQLite migracja nie jest konieczna. W przypadku LocalDB Uruchom następujące polecenie:

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a>Testuj rejestr i logowanie

Uruchom aplikację i zarejestruj użytkownika. W zależności od rozmiaru ekranu może być konieczne wybranie przycisku przełącznika nawigacji, aby wyświetlić linki **rejestru** i **logowania** .

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a>Konfigurowanie usług tożsamości

Usługi są dodawane do `ConfigureServices`programu. Typowym wzorcem jest wywoływanie wszystkich `Add{Service}` metod, a następnie wywoływanie wszystkich `services.Configure{Service}` metod.

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=10-99)]

Poprzedni wyróżniony kod konfiguruje tożsamość z domyślnymi wartościami opcji. Usługi są udostępniane aplikacji za pomocą [iniekcji zależności](xref:fundamentals/dependency-injection).

Tożsamość jest włączona przez wywołanie <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>. `UseAuthentication`dodaje [oprogramowanie pośredniczące](xref:fundamentals/middleware/index) uwierzytelniania do potoku żądania.

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

Aplikacja wygenerowana przez szablon nie korzysta z [autoryzacji](xref:security/authorization/secure-data). `app.UseAuthorization`jest uwzględniony w celu upewnienia się, że jest ona dodana w odpowiedniej kolejności, gdyby aplikacja mogła dodać autoryzację. `UseRouting`, `UseAuthentication`, `UseAuthorization`i `UseEndpoints` muszą być wywoływane w kolejności pokazanej w powyższym kodzie.

Aby uzyskać więcej informacji `IdentityOptions` na `Startup`temat programów <xref:Microsoft.AspNetCore.Identity.IdentityOptions> i, zobacz i [Uruchamianie aplikacji](xref:fundamentals/startup).

## <a name="scaffold-register-login-and-logout"></a>Rejestrowanie, logowanie i wylogowywanie szkieletu

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Dodaj pliki rejestru, logowania i wylogowywania. Postępuj zgodnie z informacjami o [tożsamości szkieletowej w projekcie Razor z instrukcjami autoryzacji](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) w celu wygenerowania kodu pokazanego w tej sekcji.

# <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/netcore-cli)

Jeśli utworzono projekt o nazwie **WebApp1**, uruchom następujące polecenia. W przeciwnym razie użyj prawidłowej przestrzeni nazw `ApplicationDbContext`dla:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

Program PowerShell używa średnika jako separatora poleceń. W przypadku korzystania z programu PowerShell wpisz średniki na liście plików lub Umieść listę plików w podwójnym cudzysłowie, jak pokazano w powyższym przykładzie.

Aby uzyskać więcej informacji na temat tworzenia szkieletów tożsamości, zobacz [tożsamość szkieletu w projekcie Razor z autoryzacją](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).

---

### <a name="examine-register"></a>Badaj rejestr

Gdy użytkownik kliknie łącze **zarejestruj** , zostanie wywołana `RegisterModel.OnPostAsync` akcja. Użytkownik jest tworzony przez wartość [IsAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) dla `_userManager` obiektu. `_userManager`jest dostarczany przez wstrzyknięcie zależności):

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

Jeśli użytkownik został utworzony pomyślnie, użytkownik jest zalogowany przez wywołanie `_signInManager.SignInAsync`.

Zobacz [potwierdzenie konta](xref:security/authentication/accconfirm#prevent-login-at-registration) , aby zapobiec natychmiastowemu logowaniu przy rejestracji.

### <a name="log-in"></a>Logowanie się

Formularz logowania jest wyświetlany, gdy:

* Wybrano łącze **Zaloguj** .
* Użytkownik próbuje uzyskać dostęp do strony z ograniczeniami, do której nie ma uprawnień dostępu **lub** kiedy nie został uwierzytelniony przez system.

Po przesłaniu formularza na stronie logowania zostanie wywołana `OnPostAsync` akcja. `PasswordSignInAsync`jest wywoływana dla `_signInManager` obiektu (dostarczone przez iniekcję zależności).

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

Klasa bazowa `Controller` uwidacznia `User` właściwość, do której można uzyskać dostęp z metod kontrolera. Na przykład można wyliczyć `User.Claims` i podjąć decyzje dotyczące autoryzacji. Aby uzyskać więcej informacji, zobacz <xref:security/authorization/introduction>.

### <a name="log-out"></a>Wyloguj

Link **Wyloguj** wywołuje `LogoutModel.OnPost` akcję. 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

W powyższym kodzie kod `return RedirectToPage();` musi być przekierowaniem, aby przeglądarka wykonywała nowe żądanie, a tożsamość użytkownika zostanie zaktualizowana.

[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) czyści oświadczenia użytkownika przechowywane w pliku cookie.

Wpis jest określony na *stronie/Shared/_LoginPartial. cshtml*:

[!code-csharp[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a>Testuj tożsamość

Domyślne szablony projektu sieci Web umożliwiają anonimowy dostęp do stron głównych. Aby przetestować tożsamość, Dodaj [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

Jeśli użytkownik jest zalogowany, Wyloguj się. Uruchom aplikację i wybierz łącze **prywatność** . Nastąpi przekierowanie do strony logowania.

### <a name="explore-identity"></a>Eksploruj tożsamość

Aby poznać tożsamość w bardziej szczegółowy sposób:

* [Utwórz źródło interfejsu użytkownika pełnej tożsamości](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* Przejrzyj Źródło poszczególnych stron i przechodzenie przez debuger.

## <a name="identity-components"></a>Składniki tożsamości

Wszystkie pakiety NuGet zależne od tożsamości są zawarte w [ASP.NET Core udostępnionej platformie](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).

Podstawowym pakietem tożsamości jest [Microsoft. AspNetCore. Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/). Ten pakiet zawiera podstawowy zestaw interfejsów dla ASP.NET Core Identity i jest dołączony do programu `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.

## <a name="migrating-to-aspnet-core-identity"></a>Migrowanie do ASP.NET Core Identity

Aby uzyskać więcej informacji i wskazówek dotyczących migrowania istniejącego magazynu tożsamości, zobacz [Migrowanie uwierzytelniania i tożsamości](xref:migration/identity).

## <a name="setting-password-strength"></a>Ustawianie siły hasła

Zobacz [Konfiguracja](#pw) dla przykładu, który ustawia minimalne wymagania dotyczące hasła.

## <a name="adddefaultidentity-and-addidentity"></a>AddDefaultIdentity i AddIdentity

<xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>wprowadzono w ASP.NET Core 2,1. Wywołanie `AddDefaultIdentity` jest podobne do wywołania następujących:

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

Aby uzyskać więcej informacji, zobacz [Źródło AddDefaultIdentity](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .

## <a name="prevent-publish-of-static-identity-assets"></a>Zapobiegaj publikowaniu zasobów tożsamości statycznej

Aby zapobiec publikowaniu statycznych zasobów tożsamości (arkuszy stylów i plików JavaScript dla interfejsu użytkownika tożsamości) do katalogu głównego sieci Web, `ResolveStaticWebAssetsInputsDependsOn` Dodaj następującą `RemoveIdentityAssets` Właściwość i obiekt docelowy do pliku projektu aplikacji:

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

## <a name="next-steps"></a>Następne kroki

* Zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/5131) w usłudze GitHub, aby uzyskać informacje dotyczące konfigurowania tożsamości przy użyciu oprogramowania SQLite.
* [Konfigurowanie systemu Identity](xref:security/authentication/identity-configuration)
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

ASP.NET Core Identity to system członkostwa, który dodaje funkcje logowania do ASP.NET Core aplikacji. Użytkownicy mogą utworzyć konto z informacjami logowania przechowywanymi w tożsamości lub korzystać z zewnętrznego dostawcy logowania. Obsługiwane zewnętrzne dostawcy logowania obejmują [serwis Facebook, Google, konto Microsoft i Twitter](xref:security/authentication/social/index).

Tożsamość można skonfigurować przy użyciu bazy danych SQL Server do przechowywania nazw użytkowników, haseł i danych profilu. Alternatywnie można użyć innego magazynu trwałego, na przykład Azure Table Storage.

[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([jak pobrać)](xref:index#how-to-download-a-sample)).

W tym temacie dowiesz się, jak używać tożsamości do rejestrowania, logowania i wylogowywania użytkownika. Aby uzyskać bardziej szczegółowe instrukcje dotyczące tworzenia aplikacji korzystających z tożsamości, zobacz sekcję następne kroki na końcu tego artykułu.

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a>AddDefaultIdentity i AddIdentity

<xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>wprowadzono w ASP.NET Core 2,1. Wywołanie `AddDefaultIdentity` jest podobne do wywołania następujących:

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

Aby uzyskać więcej informacji, zobacz [Źródło AddDefaultIdentity](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .

## <a name="create-a-web-app-with-authentication"></a>Tworzenie aplikacji sieci Web z uwierzytelnianiem

Utwórz projekt aplikacji sieci Web ASP.NET Core przy użyciu poszczególnych kont użytkowników.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Wybierz kolejno pozycje **Plik** > **Nowy** > **Projekt**.
* Wybierz **ASP.NET Core aplikacji sieci Web**. Nazwij projekt **WebApp1** tak, aby miał tę samą przestrzeń nazw co pobieranie projektu. Kliknij przycisk **OK**.
* Wybierz **aplikację sieci Web**ASP.NET Core, a następnie wybierz pozycję **Zmień uwierzytelnianie**.
* Wybierz pozycję **indywidualne konta użytkowników** , a następnie kliknij przycisk **OK**.

# <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

Wygenerowany projekt zapewnia [ASP.NET Core tożsamość](xref:security/authentication/identity) jako [bibliotekę klas Razor](xref:razor-pages/ui-class). Biblioteka klas Razor tożsamość ujawnia punkty końcowe z `Identity` obszarem. Przykład:

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

# <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a>Testuj rejestr i logowanie

Uruchom aplikację i zarejestruj użytkownika. W zależności od rozmiaru ekranu może być konieczne wybranie przycisku przełącznika nawigacji, aby wyświetlić linki **rejestru** i **logowania** .

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a>Konfigurowanie usług tożsamości

Usługi są dodawane do `ConfigureServices`programu. Typowym wzorcem jest wywoływanie wszystkich `Add{Service}` metod, a następnie wywoływanie wszystkich `services.Configure{Service}` metod.

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

Poprzedni kod konfiguruje tożsamość z domyślnymi wartościami opcji. Usługi są udostępniane aplikacji za pomocą [iniekcji zależności](xref:fundamentals/dependency-injection).

Tożsamość jest włączona przez wywołanie [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_). `UseAuthentication`dodaje [oprogramowanie pośredniczące](xref:fundamentals/middleware/index) uwierzytelniania do potoku żądania.

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

Aby uzyskać więcej informacji, zobacz [IdentityOptions klasy](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) i [uruchamiania aplikacji](xref:fundamentals/startup).

## <a name="scaffold-register-login-and-logout"></a>Rejestrowanie, logowanie i wylogowywanie szkieletu

Postępuj zgodnie z informacjami o [tożsamości szkieletowej w projekcie Razor z instrukcjami autoryzacji](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) w celu wygenerowania kodu pokazanego w tej sekcji.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Dodaj pliki rejestru, logowania i wylogowywania.

# <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/netcore-cli)

Jeśli utworzono projekt o nazwie **WebApp1**, uruchom następujące polecenia. W przeciwnym razie użyj prawidłowej przestrzeni nazw `ApplicationDbContext`dla:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

Program PowerShell używa średnika jako separatora poleceń. W przypadku korzystania z programu PowerShell wpisz średniki na liście plików lub Umieść listę plików w podwójnym cudzysłowie, jak pokazano w powyższym przykładzie.

---

### <a name="examine-register"></a>Badaj rejestr

Gdy użytkownik kliknie łącze **zarejestruj** , zostanie wywołana `RegisterModel.OnPostAsync` akcja. Użytkownik jest tworzony przez wartość [IsAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) dla `_userManager` obiektu. `_userManager`jest dostarczany przez wstrzyknięcie zależności):

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

Jeśli użytkownik został utworzony pomyślnie, użytkownik jest zalogowany przez wywołanie `_signInManager.SignInAsync`.

**Uwaga:** Zobacz [potwierdzenie konta](xref:security/authentication/accconfirm#prevent-login-at-registration) , aby zapobiec natychmiastowemu logowaniu przy rejestracji.

### <a name="log-in"></a>Logowanie się

Formularz logowania jest wyświetlany, gdy:

* Wybrano łącze **Zaloguj** .
* Użytkownik próbuje uzyskać dostęp do strony z ograniczeniami, do której nie ma uprawnień dostępu **lub** kiedy nie został uwierzytelniony przez system.

Po przesłaniu formularza na stronie logowania zostanie wywołana `OnPostAsync` akcja. `PasswordSignInAsync`jest wywoływana dla `_signInManager` obiektu (dostarczone przez iniekcję zależności).

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

Klasa bazowa `Controller` uwidacznia `User` właściwość, do której można uzyskać dostęp za pomocą metod kontrolera. Na przykład można wyliczyć `User.Claims` i podjąć decyzje dotyczące autoryzacji. Aby uzyskać więcej informacji, zobacz <xref:security/authorization/introduction>.

### <a name="log-out"></a>Wyloguj

Link **Wyloguj** wywołuje `LogoutModel.OnPost` akcję. 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) czyści oświadczenia użytkownika przechowywane w pliku cookie.

Wpis jest określony na *stronie/Shared/_LoginPartial. cshtml*:

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a>Testuj tożsamość

Domyślne szablony projektu sieci Web umożliwiają anonimowy dostęp do stron głównych. Aby przetestować tożsamość, Dodaj [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) stronę prywatność.

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

Jeśli użytkownik jest zalogowany, Wyloguj się. Uruchom aplikację i wybierz łącze **prywatność** . Nastąpi przekierowanie do strony logowania.

### <a name="explore-identity"></a>Eksploruj tożsamość

Aby poznać tożsamość w bardziej szczegółowy sposób:

* [Utwórz źródło interfejsu użytkownika pełnej tożsamości](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* Przejrzyj Źródło poszczególnych stron i przechodzenie przez debuger.

## <a name="identity-components"></a>Składniki tożsamości

Wszystkie pakiety NuGet zależne od tożsamości są zawarte w [pakiecie Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app).

Podstawowym pakietem tożsamości jest [Microsoft. AspNetCore. Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/). Ten pakiet zawiera podstawowy zestaw interfejsów dla ASP.NET Core Identity i jest dołączony do programu `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.

## <a name="migrating-to-aspnet-core-identity"></a>Migrowanie do ASP.NET Core Identity

Aby uzyskać więcej informacji i wskazówek dotyczących migrowania istniejącego magazynu tożsamości, zobacz [Migrowanie uwierzytelniania i tożsamości](xref:migration/identity).

## <a name="setting-password-strength"></a>Ustawianie siły hasła

Zobacz [Konfiguracja](#pw) dla przykładu, który ustawia minimalne wymagania dotyczące hasła.

## <a name="next-steps"></a>Następne kroki

* Zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/5131) w usłudze GitHub, aby uzyskać informacje dotyczące konfigurowania tożsamości przy użyciu oprogramowania SQLite.
* [Konfigurowanie systemu Identity](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
