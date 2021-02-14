---
title: Zabezpieczanie ASP.NET Core Blazor WebAssembly autonomicznej aplikacji przy użyciu biblioteki uwierzytelniania
author: guardrex
description: Dowiedz się, jak zabezpieczyć Blazor WebAssembly aplikację autonomiczną ASP.NET Core przy użyciu biblioteki uwierzytelniania.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/10/2021
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
uid: blazor/security/webassembly/standalone-with-authentication-library
ms.openlocfilehash: a198606caf55232c221f1d1f1224918d3f87f04c
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280889"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-the-authentication-library"></a>Zabezpieczanie ASP.NET Core Blazor WebAssembly autonomicznej aplikacji przy użyciu biblioteki uwierzytelniania

*W przypadku Azure Active Directory (AAD) i Azure Active Directory B2C (AAD B2C) nie postępuj zgodnie ze wskazówkami w tym temacie. Zobacz tematy dotyczące usługi AAD i AAD B2C w tym węźle spisu treści.*

Aby utworzyć [autonomiczną Blazor WebAssembly aplikację](xref:blazor/hosting-models#blazor-webassembly) korzystającą z [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) biblioteki, postępuj zgodnie ze wskazówkami dotyczącymi wybranego narzędzia. W przypadku dodawania obsługi uwierzytelniania programu zapoznaj się z poniższymi sekcjami tego artykułu, aby uzyskać wskazówki dotyczące konfigurowania i konfigurowania aplikacji.

> [!NOTE]
> IdentityDostawca (IP) musi używać [OpenID Connect Connect (OIDC)](https://openid.net/connect/). Na przykład adres IP w serwisie Facebook nie jest zgodny z OIDC, więc wskazówki zawarte w tym temacie nie działają z adresem IP w usłudze Facebook. Aby uzyskać więcej informacji, zobacz <xref:blazor/security/webassembly/index#authentication-library>.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Aby utworzyć nowy Blazor WebAssembly projekt z mechanizmem uwierzytelniania:

1. Po wybraniu szablonu **Blazor WebAssembly aplikacji** w oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** wybierz pozycję **Zmień** w obszarze **uwierzytelnianie**.

1. Wybierz pozycję **indywidualne konta użytkowników** z opcją **Zapisz konta użytkowników w aplikacji** , aby użyć systemu ASP.NET Core [Identity](xref:security/authentication/identity) . Wybranie tej opcji powoduje dodanie obsługi uwierzytelniania i nie powoduje przechowywania użytkowników w bazie danych. Poniższe sekcje tego artykułu zawierają szczegółowe informacje.

# <a name="visual-studio-code--net-core-cli"></a>[Visual Studio Code/interfejs wiersza polecenia platformy .NET Core](#tab/visual-studio-code+netcore-cli)

Utwórz nowy Blazor WebAssembly projekt z mechanizmem uwierzytelniania w pustym folderze. Określ `Individual` mechanizm uwierzytelniania przy `-au|--auth` użyciu opcji używania [Identity](xref:security/authentication/identity) systemu ASP.NET Core. Wybranie tej opcji powoduje dodanie obsługi uwierzytelniania i nie powoduje przechowywania użytkowników w bazie danych. Poniższe sekcje tego artykułu zawierają szczegółowe informacje.

```dotnetcli
dotnet new blazorwasm -au Individual -o {APP NAME}
```

| Symbol zastępczy  | Przykład        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

Lokalizacja wyjściowa określona przy użyciu `-o|--output` opcji tworzy folder projektu, jeśli nie istnieje, i wchodzi w skład nazwy aplikacji.

Aby uzyskać więcej informacji, zobacz [`dotnet new`](/dotnet/core/tools/dotnet-new) polecenie w przewodniku .NET Core.

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

Aby utworzyć nowy Blazor WebAssembly projekt z mechanizmem uwierzytelniania:

1. W kroku **Skonfiguruj nową Blazor WebAssembly aplikację** wybierz pozycję **uwierzytelnianie indywidualne (w aplikacji)** na liście rozwijanej **uwierzytelnianie** .

1. Aplikacja zostanie utworzona, aby używać ASP.NET Core [Identity](xref:security/authentication/identity) i nie powoduje przechowywania użytkowników w bazie danych. Poniższe sekcje tego artykułu zawierają szczegółowe informacje.

---

## <a name="authentication-package"></a>Pakiet uwierzytelniania

Gdy aplikacja zostanie utworzona w celu używania poszczególnych kont użytkowników, aplikacja automatycznie otrzymuje odwołanie do pakietu [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) w pliku projektu aplikacji. Pakiet zawiera zestaw elementów podstawowych, które ułatwiają aplikacji uwierzytelnianie użytkowników i uzyskiwanie tokenów do wywoływania chronionych interfejsów API.

W przypadku dodawania uwierzytelniania do aplikacji ręcznie Dodaj pakiet do pliku projektu aplikacji:

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="{VERSION}" />
```

Dla symbolu zastępczego `{VERSION}` Najnowsza stabilna wersja pakietu, która pasuje do udostępnionej struktury aplikacji, znajduje się w **historii wersji** pakietu pod adresem [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).

## <a name="authentication-service-support"></a>Obsługa usługi uwierzytelniania

Obsługa uwierzytelniania użytkowników jest rejestrowana w kontenerze usługi przy użyciu <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> metody rozszerzenia dostarczonej przez [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) pakiet. Ta metoda konfiguruje usługi wymagane przez aplikację do współpracy z Identity dostawcą (IP).

Dla nowej aplikacji podaj wartości dla `{AUTHORITY}` `{CLIENT ID}` symboli zastępczych i w poniższej konfiguracji. Podaj inne wartości konfiguracyjne, które są wymagane do użycia z adresem IP aplikacji. Przykład dotyczy usługi Google, która wymaga `PostLogoutRedirectUri` , `RedirectUri` i `ResponseType` . W przypadku dodawania uwierzytelniania do aplikacji ręcznie Dodaj następujący kod i konfigurację do aplikacji z wartościami dla symboli zastępczych i innych wartości konfiguracyjnych.

`Program.cs`:

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

Plik jest dostarczany przez konfigurację `wwwroot/appsettings.json` :

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

Przykład OIDC usługi Google OAuth 2,0:

```json
{
  "Local": {
    "Authority": "https://accounts.google.com/",
    "ClientId": "2.......7-e.....................q.apps.googleusercontent.com",
    "PostLogoutRedirectUri": "https://localhost:5001/authentication/logout-callback",
    "RedirectUri": "https://localhost:5001/authentication/login-callback",
    "ResponseType": "id_token"
  }
}
```

Identyfikator URI przekierowania ( `https://localhost:5001/authentication/login-callback` ) jest zarejestrowany w [konsoli interfejsów API usługi Google](https://console.developers.google.com/apis/dashboard) w obszarze **poświadczenia**  >  **`{NAME}`**  >  **autoryzowanych identyfikatorów URI przekierowania**, gdzie `{NAME}` to nazwa klienta aplikacji na liście **identyfikatorów klienta OAuth 2,0** w konsoli interfejsów API Google.

Obsługa uwierzytelniania dla aplikacji autonomicznych jest oferowana przy użyciu OpenID Connect Connect (OIDC). <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>Metoda akceptuje wywołanie zwrotne w celu skonfigurowania parametrów wymaganych do uwierzytelniania aplikacji przy użyciu OIDC. Wartości wymagane do skonfigurowania aplikacji można uzyskać z adresu IP zgodnego z OIDC. Uzyskaj wartości podczas rejestrowania aplikacji, która zwykle odbywa się w portalu online.

## <a name="access-token-scopes"></a>Zakresy tokenów dostępu

Blazor WebAssemblySzablon automatycznie konfiguruje domyślne zakresy dla `openid` i `profile` .

Blazor WebAssemblySzablon nie konfiguruje automatycznie aplikacji do żądania tokenu dostępu dla bezpiecznego interfejsu API. Aby zainicjować obsługę administracyjną tokenu dostępu w ramach przepływu logowania, Dodaj zakres do domyślnych zakresów tokenów <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions> . W przypadku dodawania uwierzytelniania do aplikacji należy ręcznie dodać poniższy kod i skonfigurować identyfikator URI zakresu.

`Program.cs`:

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

Aby uzyskać więcej informacji, zobacz następujące sekcje *dodatkowych scenariuszy* :

* [Żądaj dodatkowych tokenów dostępu](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [Dołącz tokeny do żądań wychodzących](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a>Importuje plik

[!INCLUDE[](~/blazor/includes/security/imports-file-standalone.md)]

## <a name="index-page"></a>Strona indeksu

[!INCLUDE[](~/blazor/includes/security/index-page-authentication.md)]

## <a name="app-component"></a>Składnik aplikacji

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

## <a name="redirecttologin-component"></a>Składnik RedirectToLogin

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>Składnik LoginDisplay

`LoginDisplay`Składnik ( `Shared/LoginDisplay.razor` ) jest renderowany w `MainLayout` składniku ( `Shared/MainLayout.razor` ) i zarządza następującymi zachowaniami:

* Dla uwierzytelnionych użytkowników:
  * Wyświetla bieżącą nazwę użytkownika.
  * Oferuje przycisk umożliwiający wylogowanie się z aplikacji.
* W przypadku użytkowników anonimowych program oferuje opcję logowania.

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        Hello, @context.User.Identity.Name!
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
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

## <a name="authentication-component"></a>Składnik uwierzytelniania

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:blazor/security/webassembly/additional-scenarios>
* [Nieuwierzytelnione lub nieautoryzowane żądania interfejsu API sieci Web w aplikacji z bezpiecznym klientem domyślnym](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:host-and-deploy/proxy-load-balancer>: Zawiera wskazówki dotyczące:
  * Używanie przekierowanych nagłówków oprogramowania do zachowywania informacji schematu HTTPS między serwerami proxy i sieciami wewnętrznymi.
  * Dodatkowe scenariusze i przypadki użycia, w tym konfiguracja schematu ręcznego, zmiany ścieżki żądań dla poprawnego routingu żądań i przekazywanie schematu żądań dla systemu Linux i innych niż usługi IIS zwrotnych serwerów proxy.
