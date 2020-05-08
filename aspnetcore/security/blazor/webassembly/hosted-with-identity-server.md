---
title: Zabezpiecz aplikację hostowaną ASP.NET Core Blazor webassembly z Identity serwerem
author: guardrex
description: Aby utworzyć nową Blazor hostowaną aplikację z uwierzytelnianiem z poziomu programu Visual Studio, który używa zaplecza [IdentityServer](https://identityserver.io/)
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/24/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: bf2298618e922df412e0742177afd390c4116388
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768129"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-identity-server"></a>Zabezpiecz aplikację hostowaną ASP.NET Core Blazor webassembly z Identity serwerem

Autorzy [Javier Calvarro Nelson](https://github.com/javiercn) i [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Aby utworzyć nową Blazor hostowaną aplikację w programie Visual Studio, która używa [IdentityServer](https://identityserver.io/) do uwierzytelniania użytkowników i wywołań interfejsu API:

1. Użyj programu Visual Studio, aby utworzyć nową ** Blazor aplikację webassembly** . Aby uzyskać więcej informacji, zobacz <xref:blazor/get-started>.
1. W oknie dialogowym **Tworzenie nowej Blazor aplikacji** wybierz pozycję **Zmień** w sekcji **uwierzytelnianie** .
1. Wybierz **pojedyncze konta użytkowników** , a następnie **przycisk OK**.
1. Zaznacz pole wyboru **hostowane ASP.NET Core** w sekcji **Zaawansowane** .
1. Wybierz przycisk **Utwórz**.

Aby utworzyć aplikację w powłoce poleceń, wykonaj następujące polecenie:

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

Aby określić lokalizację wyjściową, która tworzy folder projektu, jeśli nie istnieje, Uwzględnij opcję Output w poleceniu z ścieżką (na przykład `-o BlazorSample`). Nazwa folderu jest również częścią nazwy projektu.

## <a name="server-app-configuration"></a>Konfiguracja aplikacji serwera

W poniższych sekcjach opisano Dodatki do projektu w przypadku włączenia obsługi uwierzytelniania.

### <a name="startup-class"></a>Klasa początkowa

`Startup` Klasa zawiera następujące dodatki:

* W pliku `Startup.ConfigureServices`:

  * Identity:

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * IdentityServer z dodatkową <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> metodą pomocniczą, która konfiguruje niektóre domyślne konwencje ASP.NET Core w oparciu o IdentityServer:

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * Uwierzytelnianie za pomocą dodatkowej <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> metody pomocnika, która konfiguruje aplikację do weryfikowania tokenów JWT utworzonych przez IdentityServer:

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* W pliku `Startup.Configure`:

  * Oprogramowanie pośredniczące uwierzytelniania odpowiedzialne za Weryfikowanie poświadczeń żądania i Ustawianie użytkownika w kontekście żądania:

    ```csharp
    app.UseAuthentication();
    ```

  * Oprogramowanie pośredniczące IdentityServer, które uwidacznia punkty końcowe połączenia Open ID Connect (OIDC):

    ```csharp
    app.UseIdentityServer();
    ```

  * Oprogramowanie pośredniczące uwierzytelniania i autoryzacji:

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a>AddApiAuthorization

Metoda <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> pomocnika konfiguruje [IdentityServer](https://identityserver.io/) dla scenariuszy ASP.NET Core. IdentityServer to zaawansowane i rozszerzalne środowisko do obsługi zagadnień związanych z zabezpieczeniami aplikacji. IdentityServer ujawnia niepotrzebną złożoność w najbardziej typowych scenariuszach. W związku z tym zestaw Konwencji i opcji konfiguracji jest dostępny, ponieważ rozważamy dobry punkt wyjścia. Gdy uwierzytelnianie będzie wymagało zmiany, pełne możliwości IdentityServer są nadal dostępne, aby dostosować uwierzytelnianie zgodnie z wymaganiami aplikacji.

### <a name="addidentityserverjwt"></a>AddIdentityServerJwt

Metoda <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> pomocnika konfiguruje schemat zasad dla aplikacji jako domyślną procedurę obsługi uwierzytelniania. Zasady są skonfigurowane tak, aby Identity zezwalały na obsługę wszystkich żądań kierowanych do dowolnej ścieżki Identity podrzędnej `/Identity`w przestrzeni adresów URL. <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> Obsługuje wszystkie inne żądania. Ponadto ta metoda:

* Rejestruje zasób `{APPLICATION NAME}API` interfejsu API z IdentityServer z domyślnym zakresem `{APPLICATION NAME}API`.
* Konfiguruje oprogramowanie pośredniczące tokenu okaziciela JWT do weryfikowania tokenów wystawionych przez IdentityServer dla aplikacji.

### <a name="weatherforecastcontroller"></a>WeatherForecastController

W `WeatherForecastController` (*controllers/WeatherForecastController. cs*) [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) atrybut jest stosowany do klasy. Ten atrybut wskazuje, że użytkownik musi być autoryzowany na podstawie domyślnych zasad dostępu do zasobu. Domyślne zasady autoryzacji są skonfigurowane tak, aby korzystały z domyślnego schematu uwierzytelniania, który jest skonfigurowany <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> zgodnie ze schematem zasad wymienionym wcześniej. Metoda pomocnika konfiguruje <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> jako domyślną procedurę obsługi dla żądań do aplikacji.

### <a name="applicationdbcontext"></a>ApplicationDbContext

W programie `ApplicationDbContext` (*Data/ApplicationDbContext. cs*) ta sama <xref:Microsoft.EntityFrameworkCore.DbContext> wartość jest używana w Identity połączeniu z wyjątkiem, który rozszerza <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> w celu uwzględnienia schematu IdentityServer. <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601>pochodzi od <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.

Aby uzyskać pełną kontrolę nad schematem bazy danych, Dziedzicz z jednej z dostępnych Identity <xref:Microsoft.EntityFrameworkCore.DbContext> klas i skonfiguruj kontekst, aby uwzględnić Identity schemat przez wywołanie `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` `OnModelCreating` metody.

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

W `OidcConfigurationController` (*controllers/OidcConfigurationController. cs*) punkt końcowy klienta jest inicjowany do obsługi parametrów OIDC.

### <a name="app-settings-files"></a>Pliki ustawień aplikacji

W sekcji plik ustawień aplikacji (*appSettings. JSON*) w katalogu głównym projektu znajduje `IdentityServer` się opis listy skonfigurowanych klientów. W poniższym przykładzie istnieje pojedynczy klient. Nazwa klienta odpowiada nazwie aplikacji i jest zamapowana według Konwencji do parametru OAuth `ClientId` . Profil wskazuje konfigurowany typ aplikacji. Profil jest używany wewnętrznie w celu napędu Konwencji upraszczających proces konfiguracji serwera. <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

## <a name="client-app-configuration"></a>Konfiguracja aplikacji klienta

### <a name="authentication-package"></a>Pakiet uwierzytelniania

Gdy aplikacja zostanie utworzona w celu używania poszczególnych kont użytkowników (`Individual`), aplikacja automatycznie otrzymuje odwołanie `Microsoft.AspNetCore.Components.WebAssembly.Authentication` do pakietu w pliku projektu aplikacji. Pakiet zawiera zestaw elementów podstawowych, które ułatwiają aplikacji uwierzytelnianie użytkowników i uzyskiwanie tokenów do wywoływania chronionych interfejsów API.

W przypadku dodawania uwierzytelniania do aplikacji ręcznie Dodaj pakiet do pliku projektu aplikacji:

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

Zastąp `{VERSION}` odwołanie do poprzedniego pakietu wersją `Microsoft.AspNetCore.Blazor.Templates` pakietu pokazanego w <xref:blazor/get-started> artykule.

### <a name="api-authorization-support"></a>Obsługa autoryzacji interfejsu API

Obsługa uwierzytelniania użytkowników jest podłączona do kontenera usługi przez metodę rozszerzenia dostarczoną w `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pakiecie. Ta metoda konfiguruje wszystkie usługi, które są konieczne, aby aplikacja mogła współdziałać z istniejącym systemem autoryzacji.

```csharp
builder.Services.AddApiAuthorization();
```

Domyślnie ładuje ona konfigurację aplikacji według Konwencji z programu `_configuration/{client-id}`. Zgodnie z Konwencją identyfikator klienta jest ustawiany na nazwę zestawu aplikacji. Ten adres URL można zmienić tak, aby wskazywał osobny punkt końcowy przez wywołanie przeciążenia z opcjami.

### <a name="imports-file"></a>Importuje plik

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>Strona indeksu

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a>Składnik aplikacji

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>Składnik RedirectToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>Składnik LoginDisplay

Składnik (*Shared/LoginDisplay. Razor* `MainLayout` ) jest renderowany w składniku (*Shared/MainLayout. Razor*) i zarządza następującymi zachowaniami: `LoginDisplay`

* Dla uwierzytelnionych użytkowników:
  * Wyświetla bieżącą nazwę użytkownika.
  * Oferuje link do strony profilu użytkownika w ASP.NET Core Identity.
  * Oferuje przycisk umożliwiający wylogowanie się z aplikacji.
* Dla użytkowników anonimowych:
  * Oferuje opcję rejestracji.
  * Oferuje opcję logowania.

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

Uruchom aplikację z projektu serwera. W przypadku korzystania z programu Visual Studio wybierz projekt serwera w **Eksplorator rozwiązań** a następnie wybierz przycisk **Uruchom** na pasku narzędzi lub Uruchom aplikację z menu **Debuguj** .

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:security/blazor/webassembly/additional-scenarios>
