---
title: Zabezpieczanie ASP.NET Core Blazor WebAssembly aplikacji autonomicznej za pomocą Azure Active Directory B2C
author: guardrex
description: Dowiedz się, jak zabezpieczyć Blazor WebAssembly aplikację autonomiczną ASP.NET Core przy użyciu Azure Active Directory B2C.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/08/2020
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
uid: blazor/security/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 93644484cbff4c03fb25136afe9c1646104b652a
ms.sourcegitcommit: 9a90b956af8d8584d597f1e5c1dbfb0ea9bb8454
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2020
ms.locfileid: "88712470"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a>Zabezpieczanie ASP.NET Core Blazor WebAssembly aplikacji autonomicznej za pomocą Azure Active Directory B2C

Autorzy [Javier Calvarro Nelson](https://github.com/javiercn) i [Luke Latham](https://github.com/guardrex)

Aby utworzyć [autonomiczną Blazor WebAssembly aplikację](xref:blazor/hosting-models#blazor-webassembly) korzystającą z [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) do uwierzytelniania:

Postępuj zgodnie ze wskazówkami w poniższych tematach, aby utworzyć dzierżawę i zarejestrować aplikację sieci Web w witrynie Azure Portal:

[Tworzenie dzierżawy AAD B2C](/azure/active-directory-b2c/tutorial-create-tenant)

Zapisz następujące informacje:

* Wystąpienie AAD B2C (na przykład `https://contoso.b2clogin.com/` , które obejmuje końcowy ukośnik): wystąpienie jest schematem i hostem rejestracji aplikacji Azure B2C, którą można znaleźć, otwierając okno **punkty końcowe** ze strony **rejestracje aplikacji** w Azure Portal.
* AAD B2C domeny podstawowej/wydawcy/dzierżawy (na przykład `contoso.onmicrosoft.com` ): domena jest dostępna jako **domena wydawcy** w bloku **znakowania** Azure Portal dla zarejestrowanej aplikacji.

Postępuj zgodnie ze wskazówkami w [samouczku: Zarejestruj aplikację w Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) ponownie, aby zarejestrować aplikację usługi AAD dla *aplikacji klienckiej* , a następnie wykonaj następujące czynności:

1. W **Azure Active Directory**  >  **rejestracje aplikacji**wybierz pozycję **Nowa rejestracja**.
1. Podaj **nazwę** aplikacji (na przykład ** Blazor autonomiczna AAD B2C**).
1. W przypadku **obsługiwanych typów kont**wybierz opcję wiele dzierżawców: **konta w dowolnym katalogu organizacyjnym lub dowolnego dostawcę tożsamości. Do uwierzytelniania użytkowników za pomocą Azure AD B2C.**
1. Pozostaw pole listy rozwijanej **Identyfikator URI przekierowania** jako **Sieć Web** i podaj następujący identyfikator URI przekierowania: `https://localhost:{PORT}/authentication/login-callback` . Domyślnym portem dla aplikacji działającej w Kestrel jest 5001. Jeśli aplikacja jest uruchamiana na innym porcie Kestrel, użyj portu aplikacji. W przypadku IIS Express losowo wygenerowany port dla aplikacji można znaleźć we właściwościach aplikacji w panelu **debugowanie** . Ponieważ aplikacja nie istnieje w tym punkcie i port IIS Express nie jest znany, Wróć do tego kroku po utworzeniu aplikacji i zaktualizowaniu identyfikatora URI przekierowania. Uwaga zostanie wyświetlona w dalszej części tego tematu, aby przypominać IIS Express użytkownikom w celu zaktualizowania identyfikatora URI przekierowania.
1. Upewnij się, że **uprawnienia**  >  **udzielają zgody administratora na OpenID Connect, a uprawnienia offline_access** są włączone.
1. Wybierz pozycję **Rejestruj**.

Zapisz identyfikator aplikacji (klienta) (na przykład `41451fa7-82d9-4673-8fa5-69eff5a761fd` ).

W **Authentication**obszarze  >  **konfiguracje platformy**uwierzytelniania w  >  **sieci Web**:

1. Upewnij się, że jest obecny **Identyfikator URI przekierowania** `https://localhost:{PORT}/authentication/login-callback` .
1. W przypadku **niejawnego przydzielenia**zaznacz pola wyboru dla **tokenów dostępu** i **tokenów identyfikatorów**.
1. Pozostałe wartości domyślne dla aplikacji są dopuszczalne dla tego środowiska.
1. Wybierz ikonę **Zapisz**.

W **Home**obszarze  >  **Azure AD B2C**  >  **przepływy użytkowników**w Azure AD B2C domowej:

[Tworzenie przepływu użytkownika dotyczącego rejestracji i logowania](/azure/active-directory-b2c/tutorial-create-user-flows)

Wybierz co najmniej **Application claims**  >  atrybut użytkownika**Nazwa wyświetlana** oświadczenia aplikacji, aby wypełnić `context.User.Identity.Name` `LoginDisplay` składnik ( `Shared/LoginDisplay.razor` ).

Zapisz nazwę nowego przepływu logowania i logowania utworzonego dla aplikacji (na przykład `B2C_1_signupsignin` ).

W pustym folderze Zastąp symbole zastępcze w poniższym poleceniu zapisanymi wcześniej informacjami i wykonaj polecenie w powłoce poleceń:

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{TENANT DOMAIN}" -o {APP NAME} -ssp "{SIGN UP OR SIGN IN POLICY}"
```

| Symbol zastępczy                   | Nazwa Azure Portal               | Przykład                                |
| ----------------------------- | ------------------------------- | -------------------------------------- |
| `{AAD B2C INSTANCE}`          | Wystąpienie                        | `https://contoso.b2clogin.com/`        |
| `{APP NAME}`                  | &mdash;                         | `BlazorSample`                         |
| `{CLIENT ID}`                 | Identyfikator aplikacji (klienta)         | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{SIGN UP OR SIGN IN POLICY}` | Przepływ użytkownika rejestracji/logowania       | `B2C_1_signupsignin1`                  |
| `{TENANT DOMAIN}`             | Domena podstawowa/Wydawca/dzierżawa | `contoso.onmicrosoft.com`              |

Lokalizacja wyjściowa określona przy użyciu `-o|--output` opcji tworzy folder projektu, jeśli nie istnieje, i wchodzi w skład nazwy aplikacji.

> [!NOTE]
> W Azure Portal **Authentication**  >  **Konfiguracja platformy**uwierzytelniania w  >  **sieci Web**  >  **Identyfikator URI przekierowania** dla aplikacji jest skonfigurowany dla portu 5001 dla aplikacji, które działają na serwerze Kestrel z ustawieniami domyślnymi.
>
> Jeśli aplikacja jest uruchamiana na losowo IIS Express porcie, port aplikacji można znaleźć we właściwościach aplikacji w panelu **debugowanie** .
>
> Jeśli port nie został wcześniej skonfigurowany przy użyciu znanego portu aplikacji, Wróć do rejestracji aplikacji w Azure Portal i zaktualizuj identyfikator URI przekierowania z prawidłowym portem.

Po utworzeniu aplikacji powinno być możliwe:

* Zaloguj się do aplikacji przy użyciu konta użytkownika usługi AAD.
* Zażądaj tokenów dostępu dla interfejsów API firmy Microsoft. Aby uzyskać więcej informacji, zobacz:
  * [Zakresy tokenów dostępu](#access-token-scopes)
  * [Szybki Start: Konfigurowanie aplikacji do udostępniania interfejsów API sieci Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).

## <a name="authentication-package"></a>Pakiet uwierzytelniania

Gdy aplikacja zostanie utworzona w celu korzystania z pojedynczego konta B2C ( `IndividualB2C` ), aplikacja automatycznie otrzymuje odwołanie do pakietu dla [biblioteki uwierzytelniania firmy Microsoft](/azure/active-directory/develop/msal-overview) ( [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) ). Pakiet zawiera zestaw elementów podstawowych, które ułatwiają aplikacji uwierzytelnianie użytkowników i uzyskiwanie tokenów do wywoływania chronionych interfejsów API.

W przypadku dodawania uwierzytelniania do aplikacji ręcznie Dodaj pakiet do pliku projektu aplikacji:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

Dla symbolu zastępczego `{VERSION}` Najnowsza stabilna wersja pakietu, która pasuje do udostępnionej struktury aplikacji, znajduje się w **historii wersji** pakietu pod adresem [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).

[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)Pakiet zawiera przechodnie Dodawanie [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) pakietu do aplikacji.

## <a name="authentication-service-support"></a>Obsługa usługi uwierzytelniania

Obsługa uwierzytelniania użytkowników jest rejestrowana w kontenerze usługi przy użyciu <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> metody rozszerzenia dostarczonej przez [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) pakiet. Ta metoda umożliwia skonfigurowanie wszystkich usług wymaganych przez aplikację do współpracy z Identity dostawcą (IP).

`Program.cs`:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
});
```

<xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>Metoda akceptuje wywołanie zwrotne w celu skonfigurowania parametrów wymaganych do uwierzytelnienia aplikacji. Wartości wymagane do skonfigurowania aplikacji można uzyskać z konfiguracji usługi AAD podczas rejestrowania aplikacji.

Plik jest dostarczany przez konfigurację `wwwroot/appsettings.json` :

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

Przykład:

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": false
  }
}
```

## <a name="access-token-scopes"></a>Zakresy tokenów dostępu

Blazor WebAssemblySzablon nie konfiguruje automatycznie aplikacji do żądania tokenu dostępu dla bezpiecznego interfejsu API. Aby zainicjować obsługę administracyjną tokenu dostępu w ramach przepływu logowania, Dodaj zakres do domyślnych zakresów tokenów dostępu <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> :

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

Aby uzyskać więcej informacji, zobacz następujące sekcje *dodatkowych scenariuszy* :

* [Żądaj dodatkowych tokenów dostępu](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [Dołącz tokeny do żądań wychodzących](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

## <a name="login-mode"></a>Tryb logowania

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

## <a name="imports-file"></a>Importuje plik

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a>Strona indeksu

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a>Składnik aplikacji

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a>Składnik RedirectToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>Składnik LoginDisplay

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a>Składnik uwierzytelniania

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:blazor/security/webassembly/additional-scenarios>
* [Nieuwierzytelnione lub nieautoryzowane żądania interfejsu API sieci Web w aplikacji z bezpiecznym klientem domyślnym](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [Samouczek: Tworzenie dzierżawy usługi Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant)
* [Dokumentacja poświęcona platformie tożsamości firmy Microsoft](/azure/active-directory/develop/)
