---
title: Zabezpieczanie hostowanego Blazor przez ASP.NET Core aplikacji hostowanego webassembly za pomocą serwera tożsamości
author: guardrex
description: Aby utworzyć Blazor nową aplikację hostowane z uwierzytelnianiem z poziomu programu Visual Studio, która używa zaplecza [serwera tożsamości](https://identityserver.io/)
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: 832109530c4aac372fd75aa1a1d2edbe3768f55f
ms.sourcegitcommit: 1d8f1396ccc66a0c3fcb5e5f36ea29b50db6d92a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80501285"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-identity-server"></a>Zabezpieczanie hostowanego Blazor przez ASP.NET Core aplikacji hostowanego webassembly za pomocą serwera tożsamości

Autorstwa [Javiera Calvarro Nelsona](https://github.com/javiercn) i [Luke'a Lathama](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Aby utworzyć Blazor nową aplikację hostowane w programie Visual Studio, która używa [usługi IdentityServer](https://identityserver.io/) do uwierzytelniania użytkowników i wywołań interfejsu API:

1. Użyj programu Visual Studio, aby utworzyć nową ** Blazor aplikację WebAssembly.** Aby uzyskać więcej informacji, zobacz <xref:blazor/get-started>.
1. W oknie **dialogowym Tworzenie Blazor nowej aplikacji** wybierz pozycję **Zmień** w sekcji **Uwierzytelnianie.**
1. Wybierz **pozycję Indywidualne konta użytkowników,** po których następuje przycisk **OK**.
1. Zaznacz pole wyboru **hostowane ASP.NET Core** w sekcji **Zaawansowane.**
1. Wybierz przycisk **Utwórz**.

Aby utworzyć aplikację w powłoce poleceń, wykonaj następujące polecenie:

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

Aby określić lokalizację wyjściową, która tworzy folder projektu, jeśli nie istnieje, należy dołączyć opcję `-o BlazorSample`wyjściową w poleceniu ze ścieżką (na przykład ). Nazwa folderu również staje się częścią nazwy projektu.

## <a name="server-app-configuration"></a>Konfiguracja aplikacji serwera

W poniższych sekcjach opisano dodatki do projektu, gdy obsługa uwierzytelniania jest uwzględniona.

### <a name="startup-class"></a>Klasa początkowa

Klasa `Startup` ma następujące dodatki:

* W pliku `Startup.ConfigureServices`:

  * Tożsamość z domyślnym interfejsem użytkownika:

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * IdentityServer z <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> dodatkową metodą pomocniczą, która konfiguruje niektóre domyślne konwencje ASP.NET Core na górze IdentityServer:

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * Uwierzytelnianie za <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> pomocą dodatkowej metody pomocniczej, która konfiguruje aplikację do sprawdzania poprawności tokenów JWT wyprodukowanych przez IdentityServer:

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* W pliku `Startup.Configure`:

  * Oprogramowanie pośredniczące uwierzytelniania, które jest odpowiedzialne za sprawdzanie poprawności poświadczeń żądania i ustawienie użytkownika w kontekście żądania:

    ```csharp
    app.UseAuthentication();
    ```

  * Oprogramowanie pośredniczące usługi IdentityServer, które udostępnia punkty końcowe Open ID Connect (OIDC):

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a>AddApiAuthorization (Dodawanie)

Metoda <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> pomocnika konfiguruje [IdentityServer](https://identityserver.io/) dla scenariuszy ASP.NET Core. IdentityServer to zaawansowana i rozszerzalna struktura obsługi problemów związanych z zabezpieczeniami aplikacji. IdentityServer udostępnia niepotrzebne złożoności dla najbardziej typowych scenariuszy. W związku z tym zestaw konwencji i opcji konfiguracji jest pod warunkiem, że uważamy za dobry punkt wyjścia. Po zmianie potrzeb uwierzytelniania, pełna moc IdentityServer jest nadal dostępna, aby dostosować uwierzytelnianie do wymagań aplikacji.

### <a name="addidentityserverjwt"></a>Dodaj IdentyfikatoryServerJwt

Metoda <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> pomocnika konfiguruje schemat zasad dla aplikacji jako domyślny program obsługi uwierzytelniania. Zasada jest skonfigurowana tak, aby umożliwić tożsamości do obsługi wszystkich żądań kierowanych do dowolnej ścieżki podrzędnej w przestrzeni `/Identity`adresu URL tożsamości . Obsługuje <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> wszystkie inne żądania. Ponadto ta metoda:

* Rejestruje zasób interfejsu API w `{APPLICATION NAME}API` usłudze `{APPLICATION NAME}API`IdentityServer o domyślnym zakresie .
* Konfiguruje oprogramowanie pośredniczące tokenu nośnika JWT w celu sprawdzania poprawności tokenów wystawionych przez IdentityServer dla aplikacji.

### <a name="weatherforecastcontroller"></a>WeatherForecastController (Kontroler weatherforecast)

W `WeatherForecastController` *(Controllers/WeatherForecastController.cs)* [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atrybut jest stosowany do klasy. Atrybut wskazuje, że użytkownik musi być autoryzowany na podstawie domyślnej zasady dostępu do zasobu. Domyślna zasada autoryzacji jest skonfigurowana do używania domyślnego schematu uwierzytelniania, który jest skonfigurowany przez <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> schemat zasad, który został wymieniony wcześniej. Metoda pomocnika konfiguruje <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> jako domyślny program obsługi żądań do aplikacji.

### <a name="applicationdbcontext"></a>ApplicationDbContext (ApplicationDbContext)

W `ApplicationDbContext` *(Data/ApplicationDbContext.cs),* to <xref:Microsoft.EntityFrameworkCore.DbContext> samo jest używane w tożsamości <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> z wyjątkiem, że rozszerza się na schemat IdentityServer. <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601>pochodzi z <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.

Aby uzyskać pełną kontrolę nad schematem bazy danych, <xref:Microsoft.EntityFrameworkCore.DbContext> dziedzicz po jednej z dostępnych klas `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` tożsamości `OnModelCreating` i skonfiguruj kontekst, aby uwzględnić schemat tożsamości, wywołując w metodzie.

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController (Kontroler konfiguracji oidc)

W `OidcConfigurationController` *(Controllers/OidcConfigurationController.cs)* punkt końcowy klienta jest aprowizowana do obsługi parametrów OIDC.

### <a name="app-settings-files"></a>Pliki ustawień aplikacji

W pliku ustawień aplikacji (*appsettings.json*) `IdentityServer` w katalogu głównym projektu w sekcji opisano listę skonfigurowanych klientów. W poniższym przykładzie istnieje jeden klient. Nazwa klienta odpowiada nazwie aplikacji i jest mapowana według konwencji `ClientId` na parametr OAuth. Profil wskazuje typ aplikacji, który jest konfigurowany. Profil jest używany wewnętrznie do kierowania konwencjami, które upraszczają proces konfiguracji serwera. <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "BlazorApplicationWithAuthentication.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

W pliku ustawień aplikacji środowiska deweloperskiego (*appsettings. Development.json*) w katalogu `IdentityServer` głównym projektu w sekcji opisano klucz używany do podpisywania tokenów. <!-- When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section. -->

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="client-app-configuration"></a>Konfiguracja aplikacji klienckiej

### <a name="authentication-package"></a>Pakiet uwierzytelniania

Po utworzeniu aplikacji do korzystania`Individual`z kont indywidualnych użytkowników ( ), `Microsoft.AspNetCore.Components.WebAssembly.Authentication` aplikacja automatycznie odbiera odwołanie do pakietu w pliku projektu aplikacji. Pakiet zawiera zestaw uwierzytelniania, które pomagają aplikacji uwierzytelniać użytkowników i uzyskać tokeny do wywoływania chronionych interfejsów API.

Jeśli dodasz uwierzytelnianie do aplikacji, ręcznie dodaj pakiet do pliku projektu aplikacji:

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

Zastąp `{VERSION}` w poprzednim odwołaniu `Microsoft.AspNetCore.Blazor.Templates` do pakietu <xref:blazor/get-started> wersją pakietu przedstawioną w artykule.

### <a name="api-authorization-support"></a>Obsługa autoryzacji interfejsu API

Obsługa uwierzytelniania użytkowników jest podłączony do kontenera usługi przez `Microsoft.AspNetCore.Components.WebAssembly.Authentication` metodę rozszerzenia pod warunkiem, wewnątrz pakietu. Ta metoda konfiguruje wszystkie usługi potrzebne do interakcji aplikacji z istniejącym systemem autoryzacji.

```csharp
builder.Services.AddApiAuthorization();
```

Domyślnie ładuje konfigurację aplikacji według konwencji `_configuration/{client-id}`z . Zgodnie z konwencją identyfikator klienta jest ustawiony na nazwę zestawu aplikacji. Ten adres URL można zmienić, aby wskazać oddzielny punkt końcowy, wywołując przeciążenie z opcjami.

### <a name="imports-file"></a>Import pliku

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>Strona indeksu

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a>Składnik aplikacji

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>Składnik RedirectToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>Składnik LogowanieWydajnik

Składnik `LoginDisplay` *(Shared/LoginDisplay.brzytwa)* jest `MainLayout` renderowany w składniku (*Shared/MainLayout.brzytwa*) i zarządza następującymi zachowaniami:

* Dla uwierzytelnionych użytkowników:
  * Wyświetla bieżącą nazwę użytkownika.
  * Oferuje łącze do strony profilu użytkownika w ASP.NET Tożsamość podstawowa.
  * Oferuje przycisk, aby wylogować się z aplikacji.
* Dla użytkowników anonimowych:
  * Oferuje możliwość rejestracji.
  * Oferuje możliwość logowania.

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        <a href="authentication/profile">Hello, @context.User.Identity.Name!</a>
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/register">Register</a>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

### <a name="authentication-component"></a>Składnik uwierzytelniania

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>Składnik FetchData

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>Uruchomienie aplikacji

Uruchom aplikację z projektu serwera. Korzystając z programu Visual Studio, wybierz projekt serwera w **Eksploratorze rozwiązań** i wybierz przycisk **Uruchom** na pasku narzędzi lub uruchom aplikację z menu **debugowania.**

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Żądanie dodatkowych tokenów dostępu](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
