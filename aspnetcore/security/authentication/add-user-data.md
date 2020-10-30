---
title: Dodawanie, pobieranie i usuwanie danych użytkownika do Identity w projekcie ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak dodać niestandardowe dane użytkownika do Identity projektu ASP.NET Core. Usuń dane na Rodo.
ms.author: riande
ms.date: 03/26/2020
ms.custom: mvc, seodec18
no-loc:
- appsettings.json
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
uid: security/authentication/add-user-data
ms.openlocfilehash: a4e1fd780947cfa5f09fb1e03964595fa09f0f18
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061421"
---
# <a name="add-download-and-delete-custom-user-data-to-no-locidentity-in-an-aspnet-core-project"></a>Dodawanie, pobieranie i usuwanie niestandardowych danych użytkownika do programu Identity w projekcie ASP.NET Core

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

W tym artykule pokazano, jak:

* Dodawanie niestandardowych danych użytkownika do aplikacji sieci Web ASP.NET Core.
* Oznacz niestandardowy model danych użytkownika <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> atrybutami, aby był on automatycznie dostępny do pobrania i usunięcia. Aby można było pobrać i usunąć dane, można spełnić wymagania [Rodo](xref:security/gdpr) .

Przykład projektu jest tworzony na podstawie Razor strony aplikacji sieci Web, ale instrukcje są podobne dla aplikacji sieci web ASP.NET Core MVC.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Wymagania wstępne

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-no-locrazor-web-app"></a>Tworzenie Razor aplikacji sieci Web

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* Z menu **plik** programu Visual Studio wybierz pozycję **Nowy**  >  **projekt** . Nadaj projektowi nazwę **WebApp1** , jeśli chcesz dopasować ją do przestrzeni nazw [przykładowego kodu pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) .
* Wybierz pozycję **ASP.NET Core aplikację sieci Web** > **OK**
* Na liście rozwijanej wybierz pozycję **ASP.NET Core 3,0**
* Wybierz **aplikację sieci Web** > **OK**
* Skompiluj i Uruchom projekt.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* Z menu **plik** programu Visual Studio wybierz pozycję **Nowy**  >  **projekt** . Nadaj projektowi nazwę **WebApp1** , jeśli chcesz dopasować ją do przestrzeni nazw [przykładowego kodu pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) .
* Wybierz pozycję **ASP.NET Core aplikację sieci Web** > **OK**
* Na liście rozwijanej wybierz pozycję **ASP.NET Core 2,2**
* Wybierz **aplikację sieci Web** > **OK**
* Skompiluj i Uruchom projekt.

::: moniker-end


# <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-no-locidentity-scaffolder"></a>Uruchamianie Identity szkieletu

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt > **Dodaj**  >  **nowy element szkieletowy** .
* W lewym okienku okna dialogowego **Dodawanie szkieletu** wybierz pozycję **Identity**  >  **Dodaj** .
* W oknie **dialogowym Identity Dodawanie** następujące opcje:
  * Wybierz istniejący plik układu  *~/Pages/Shared/_Layout. cshtml*
  * Wybierz następujące pliki do przesłonięcia:
    * **Konto/rejestr**
    * **Konto/Zarządzanie/indeks**
  * Wybierz **+** przycisk, aby utworzyć nową **klasę kontekstu danych** . Zaakceptuj typ ( **WebApp1. models. WebApp1Context** , jeśli projekt jest nazwany **WebApp1** ).
  * Wybierz **+** przycisk, aby utworzyć nową **klasę użytkownika** . Zaakceptuj typ ( **WebApp1User** , jeśli projekt ma nazwę **WebApp1** ) > **Dodaj** .
* Wybierz pozycję **Dodaj** .

# <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli)

Jeśli jeszcze nie zainstalowano szkieletu ASP.NET Core, zainstaluj go teraz:

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Dodaj odwołanie do pakietu do [Microsoft. VisualStudio. Web. CodeGeneration. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) do pliku projektu (. csproj). Uruchom następujące polecenie w katalogu projektu:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

Uruchom następujące polecenie, aby wyświetlić listę Identity opcji szkieletu:

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

W folderze projektu uruchom program Identity szkieletowy:

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

Postępuj zgodnie z instrukcjami w sekcji [migracje, UseAuthentication i układ](xref:security/authentication/scaffold-identity#efm) , aby wykonać następujące czynności:

* Utwórz migrację i zaktualizuj bazę danych.
* Add `UseAuthentication` to `Startup.Configure`.
* Dodaj `<partial name="_LoginPartial" />` do pliku układu.
* Przetestuj aplikację:
  * Rejestrowanie użytkownika
  * Wybierz nową nazwę użytkownika (obok linku **wylogowania** ). Może być konieczne rozszerzenie okna lub wybranie ikony paska nawigacyjnego, aby wyświetlić nazwę użytkownika i inne linki.
  * Wybierz kartę **dane osobowe** .
  * Wybierz przycisk **Pobierz** i zbadaj *PersonalData.js* pliku.
  * Przetestuj przycisk **Usuń** , który spowoduje usunięcie zalogowanego użytkownika.

## <a name="add-custom-user-data-to-the-no-locidentity-db"></a>Dodawanie niestandardowych danych użytkownika do Identity bazy danych

Zaktualizuj `IdentityUser` klasę pochodną o właściwościach niestandardowych. Jeśli nazwa projektu WebApp1, plik ma nazwę *obszary/ Identity /Data/WebApp1User.cs* . Zaktualizuj plik przy użyciu następującego kodu:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

Właściwości z atrybutem [personaldata](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) są następujące:

* Usuwane, gdy wywołują się strony *Areas/ Identity /Pages/Account/Manage/DeletePersonalData.cshtml* Razor `UserManager.Delete` .
* Uwzględnione w pobranych danych przez stronę *obszary/ Identity /Pages/Account/Manage/DownloadPersonalData.cshtml* Razor .

### <a name="update-the-accountmanageindexcshtml-page"></a>Aktualizowanie strony account/Manage/index. cshtml

Zaktualizuj `InputModel` w *obszarach/ Identity /Pages/Account/Manage/index.cshtml.cs* następujący wyróżniony kod:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

Zaktualizuj *obszary/ Identity /Pages/Account/Manage/index.cshtml* przy użyciu następującego wyróżnionego znacznika:

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

Zaktualizuj *obszary/ Identity /Pages/Account/Manage/index.cshtml* przy użyciu następującego wyróżnionego znacznika:

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a>Aktualizowanie strony account/register. cshtml

Zaktualizuj `InputModel` w *obszarach/ Identity /Pages/Account/register.cshtml.cs* następujący wyróżniony kod:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

Zaktualizuj *obszary/ Identity /Pages/Account/register.cshtml* przy użyciu następującego wyróżnionego znacznika:

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

Zaktualizuj *obszary/ Identity /Pages/Account/register.cshtml* przy użyciu następującego wyróżnionego znacznika:

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


Skompiluj projekt.

### <a name="add-a-migration-for-the-custom-user-data"></a>Dodawanie migracji niestandardowych danych użytkownika

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

W **konsoli Menedżera pakietów** programu Visual Studio:

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a>Testowanie tworzenia, wyświetlania, pobierania, usuwania niestandardowych danych użytkownika

Przetestuj aplikację:

* Zarejestruj nowego użytkownika.
* Wyświetlanie niestandardowych danych użytkownika na `/Identity/Account/Manage` stronie.
* Pobierz i Przejrzyj dane osobowe użytkowników ze `/Identity/Account/Manage/PersonalData` strony.

## <a name="add-claims-to-no-locidentity-using-iuserclaimsprincipalfactoryapplicationuser"></a>Dodawanie oświadczeń do Identity korzystania z IUserClaimsPrincipalFactory<ApplicationUser>

> [!NOTE]
> Ta sekcja nie jest rozszerzeniem poprzedniego samouczka. Aby zastosować następujące kroki do aplikacji skompilowanej przy użyciu samouczka, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/18797)w usłudze GitHub.

Dodatkowe oświadczenia można dodać do programu przy ASP.NET Core Identity użyciu `IUserClaimsPrincipalFactory<T>` interfejsu. Tę klasę można dodać do aplikacji w `Startup.ConfigureServices` metodzie. Dodaj niestandardową implementację klasy w następujący sposób:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddScoped<IUserClaimsPrincipalFactory<ApplicationUser>, 
        AdditionalUserClaimsPrincipalFactory>();
```

Kod demonstracyjny używa `ApplicationUser` klasy. Ta klasa dodaje `IsAdmin` Właściwość, która jest używana do dodawania dodatkowego żądania.

```csharp
public class ApplicationUser : IdentityUser
{
    public bool IsAdmin { get; set; }
}
```

`AdditionalUserClaimsPrincipalFactory`Implementuje `UserClaimsPrincipalFactory` interfejs. Do. zostanie dodane nowe zastrzeżenie roli `ClaimsPrincipal` .

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

Dodatkowe zgłoszenie można następnie użyć w aplikacji. Na Razor stronie `IAuthorizationService` wystąpienie może być używane w celu uzyskania dostępu do wartości żądania.

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
