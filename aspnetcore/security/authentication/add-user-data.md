---
title: Dodawanie, pobieranie i usuwanie danych użytkownika do tożsamości w projekcie ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak dodać niestandardowe dane użytkownika do tożsamości w projekcie ASP.NET Core. Usuń dane zgodnie z RODO.
ms.author: riande
ms.date: 03/26/2020
ms.custom: mvc, seodec18
uid: security/authentication/add-user-data
ms.openlocfilehash: 76b83df22381429feab80056c36dbdac1e5f20c7
ms.sourcegitcommit: 1d8f1396ccc66a0c3fcb5e5f36ea29b50db6d92a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80501227"
---
# <a name="add-download-and-delete-custom-user-data-to-identity-in-an-aspnet-core-project"></a>Dodawanie, pobieranie i usuwanie niestandardowych danych użytkownika do tożsamości w projekcie ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

W tym artykule pokazano, jak:

* Dodawanie niestandardowych danych użytkownika do aplikacji sieci web ASP.NET Core.
* Oznacz niestandardowy model danych <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> użytkownika za pomocą atrybutu, aby był automatycznie dostępny do pobrania i usunięcia. Możliwość pobierania i usuwania danych pomaga spełnić wymagania [RODO.](xref:security/gdpr)

Przykład projektu jest tworzony z aplikacji sieci Web Razor Pages, ale instrukcje są podobne dla aplikacji sieci web ASP.NET Core MVC.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Wymagania wstępne

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-razor-web-app"></a>Tworzenie aplikacji internetowej Razor

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* Z menu **Plik** programu Visual Studio wybierz polecenie **Nowy** > **projekt**. Nazwij projekt **WebApp1,** jeśli chcesz, aby był zgodny z obszarem nazw przykładowego kodu [pobierania.](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data)
* Wybierz **ASP.NET Core Web Application** > **OK**
* Wybierz **ASP.NET Core 3.0** w rozwijanej
* Wybierz **aplikację** > sieci Web **OK**
* Tworzenie i uruchamianie projektu.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* Z menu **Plik** programu Visual Studio wybierz polecenie **Nowy** > **projekt**. Nazwij projekt **WebApp1,** jeśli chcesz, aby był zgodny z obszarem nazw przykładowego kodu [pobierania.](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data)
* Wybierz **ASP.NET Core Web Application** > **OK**
* Wybierz **ASP.NET Core 2.2** w rozwijanej
* Wybierz **aplikację** > sieci Web **OK**
* Tworzenie i uruchamianie projektu.

::: moniker-end


# <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-identity-scaffolder"></a>Uruchamianie szkieletu tożsamości

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* Z **Eksploratora rozwiązań**kliknij prawym przyciskiem myszy projekt > **Dodaj** > **nowy element rusztowania**.
* Z lewego okienka okna dialogowego **Dodawanie szkieletu** wybierz pozycję**Dodaj** **tożsamość** > .
* W oknie dialogowym **Dodawanie tożsamości** są dostępne następujące opcje:
  * Wybierz istniejący plik układu *~/Pages/Shared/_Layout.cshtml*
  * Wybierz następujące pliki do zastąpienia:
    * **Konto/zarejestruj się**
    * **Konto/Zarządzanie/Indeks**
  * Wybierz **+** przycisk, aby utworzyć nową **klasę kontekstu danych**. Zaakceptuj typ **(WebApp1.Models.WebApp1Context,** jeśli projekt nosi nazwę **WebApp1**).
  * Wybierz **+** przycisk, aby utworzyć nową **klasę użytkownika**. Zaakceptuj typ **(WebApp1Użytnik,** jeśli projekt nosi nazwę **WebApp1)**> **Dodaj**.
* Wybierz pozycję **Dodaj**.

# <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/netcore-cli)

Jeśli nie zainstalowano wcześniej ASP.NET rdzenia, zainstaluj go teraz:

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Dodaj odwołanie do pakietu do pliku [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) do pliku projektu (csproj). Uruchom następujące polecenie w katalogu projektu:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

Uruchom następujące polecenie, aby wyświetlić listę opcji szkieletu tożsamości:

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

W folderze projektu uruchom rusztowanie tożsamości:

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

Postępuj zgodnie z instrukcjami w [migracje, UseAuthentication i układ,](xref:security/authentication/scaffold-identity#efm) aby wykonać następujące kroki:

* Utwórz migrację i zaktualizuj bazę danych.
* Add `UseAuthentication` to `Startup.Configure`.
* Dodaj `<partial name="_LoginPartial" />` do pliku układu.
* Przetestuj aplikację:
  * Zarejestruj użytkownika
  * Wybierz nową nazwę użytkownika (obok łącza **Wyloguj).** Może być konieczne rozwinięcie okna lub wybranie ikony paska nawigacyjnego, aby wyświetlić nazwę użytkownika i inne łącza.
  * Wybierz kartę **Dane osobowe.**
  * Wybierz przycisk **Pobierz** i przeanalizuj plik *PersonalData.json.*
  * Przetestuj przycisk **Usuń,** który usuwa zalogowanego użytkownika.

## <a name="add-custom-user-data-to-the-identity-db"></a>Dodawanie niestandardowych danych użytkownika do bazy danych tożsamości

Zaktualizuj klasę pochodną `IdentityUser` za pomocą właściwości niestandardowych. Jeśli projekt został nazwany WebApp1, plik nosi nazwę *Obszary/Tożsamość/Dane/WebApp1User.cs*. Zaktualizuj plik za pomocą następującego kodu:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

Właściwości z atrybutem [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) to:

* Usunięte po wywołaniu *strony Obszary/Tożsamość/Strony/Konto/Zarządzaj/UsuńPersonaldata.cshtml* Razor Page `UserManager.Delete`.
* Uwzględnione w pobranych danych przez *obszary / tożsamość / strony / konto / Manage / DownloadPersonalData.cshtml* Razor Page.

### <a name="update-the-accountmanageindexcshtml-page"></a>Aktualizowanie strony Konto/Zarządzanie/Indeks.cshtml

Zaktualizuj `InputModel` w *obszarze/tożsamości/pages/konto/zarządzaj/index.cshtml.cs* z podświetlonym kodem:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

Zaktualizuj *obszary/tożsamość/strony/konto/zarządzanie/indeks.cshtml* za pomocą następujących wyróżnionych znaczników:

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

Zaktualizuj *obszary/tożsamość/strony/konto/zarządzanie/indeks.cshtml* za pomocą następujących wyróżnionych znaczników:

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a>Aktualizowanie strony Konto/Register.cshtml

Zaktualizuj `InputModel` w *obszarze/tożsamości/pages/konto/Register.cshtml.cs* z następującym wyróżnionym kodem:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

Zaktualizuj *obszary/tożsamość/strony/konto/register.cshtml* za pomocą następujących wyróżnionych znaczników:

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

Zaktualizuj *obszary/tożsamość/strony/konto/register.cshtml* za pomocą następujących wyróżnionych znaczników:

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


Skompiluj projekt.

### <a name="add-a-migration-for-the-custom-user-data"></a>Dodawanie migracji dla niestandardowych danych użytkownika

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

W konsoli **Menedżera pakietów**programu Visual Studio:

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a>Testowanie tworzenia, przeglądania, pobierania, usuwania niestandardowych danych użytkownika

Przetestuj aplikację:

* Zarejestruj nowego użytkownika.
* Wyświetl niestandardowe dane `/Identity/Account/Manage` użytkownika na stronie.
* Pobierz i wyświetl dane osobowe `/Identity/Account/Manage/PersonalData` użytkowników ze strony.

## <a name="add-claims-to-identity-using-iuserclaimsprincipalfactoryapplicationuser"></a>Dodaj oświadczenia do tożsamości przy użyciu IUserClaimsPrincipalFactory<ApplicationUser>

Dodatkowe oświadczenia można dodać do ASP.NET tożsamości podstawowej `IUserClaimsPrincipalFactory<T>` przy użyciu interfejsu. Ta klasa może zostać dodana `Startup.ConfigureServices` do aplikacji w metodzie. Dodaj niestandardową implementację klasy w następujący sposób:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddScoped<IUserClaimsPrincipalFactory<ApplicationUser>, 
        AdditionalUserClaimsPrincipalFactory>();
```

Kod demonstracyjny `ApplicationUser` używa klasy. Ta klasa `IsAdmin` dodaje właściwość, która jest używana do dodawania dodatkowego oświadczenia.

```csharp
public class ApplicationUser : IdentityUser
{
    public bool IsAdmin { get; set; }
}
```

Implementuje `AdditionalUserClaimsPrincipalFactory` `UserClaimsPrincipalFactory` interfejs. Nowe oświadczenie roli jest `ClaimsPrincipal`dodawane do .

```csharp
public class AdditionalUserClaimsPrincipalFactory 
        : UserClaimsPrincipalFactory<ApplicationUser, IdentityRole>
{
    public AdditionalUserClaimsPrincipalFactory( 
        UserManager<ApplicationUser> userManager,
        RoleManager<IdentityRole> roleManager, 
        IOptions<IdentityOptions> optionsAccessor) 
        : base(userManager, roleManager, optionsAccessor)
    {}

    public async override Task<ClaimsPrincipal> CreateAsync(ApplicationUser user)
    {
        var principal = await base.CreateAsync(user);
        var identity = (ClaimsIdentity)principal.Identity;

        var claims = new List<Claim>();
        if (user.IsAdmin)
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "admin"));
        }
        else
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "user"));
        }

        identity.AddClaims(claims);
        return principal;
    }
}
```

Dodatkowe oświadczenie można następnie użyć w aplikacji. Na stronie Razor `IAuthorizationService` wystąpienie może służyć do uzyskania dostępu do wartości oświadczenia.

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

@if ((await AuthorizationService.AuthorizeAsync(User, "IsAdmin")).Succeeded)
{
    <ul class="mr-auto navbar-nav">
        <li class="nav-item">
            <a class="nav-link" asp-controller="Admin" asp-action="Index">ADMIN</a>
        </li>
    </ul>
}
```
