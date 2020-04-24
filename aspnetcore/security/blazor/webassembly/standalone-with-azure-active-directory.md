---
title: Zabezpiecz ASP.NET Core Blazor autonomiczną aplikację webassembly z Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/24/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: 4c5fc6a07ce52ee9560cd2aa0c7dbc3032522f00
ms.sourcegitcommit: 4f91da9ce4543b39dba5e8920a9500d3ce959746
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82138473"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory"></a>Zabezpiecz ASP.NET Core Blazor autonomiczną aplikację webassembly z Azure Active Directory

Autorzy [Javier Calvarro Nelson](https://github.com/javiercn) i [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Aby utworzyć Blazor autonomiczną aplikację webassembly, która używa usługi [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) do uwierzytelniania:

[Utwórz dzierżawę usługi AAD i aplikację sieci Web](/azure/active-directory/develop/v2-overview):

Zarejestruj aplikację usługi AAD w obszarze **Azure Active Directory** > **rejestracje aplikacji** Azure Active Directory w Azure Portal:

1. Podaj **nazwę** aplikacji (na przykład ** Blazor klienta AAD**).
1. Wybierz **obsługiwane typy kont**. **W tym katalogu organizacji można wybrać konta tylko** dla tego środowiska.
1. Pozostaw pole listy rozwijanej **Identyfikator URI przekierowania** na **Sieć Web**i podaj identyfikator URI `https://localhost:5001/authentication/login-callback`przekierowania.
1. Wyłącz pole wyboru**Przyznaj administratorowi uprawnienia do OpenID Connect i uprawnień offline_access** . **Permissions** > 
1. Wybierz pozycję **Zarejestruj**.

W obszarze**konfiguracje** > platformy **uwierzytelniania** > w**sieci Web**:

1. Upewnij się, że `https://localhost:5001/authentication/login-callback` jest obecny **Identyfikator URI przekierowania** .
1. W przypadku **niejawnego przydzielenia**zaznacz pola wyboru dla **tokenów dostępu** i **tokenów identyfikatorów**.
1. Pozostałe wartości domyślne dla aplikacji są dopuszczalne dla tego środowiska.
1. Wybierz ikonę **Zapisz**.

Zapisz następujące informacje:

* Identyfikator aplikacji (identyfikator klienta) (na przykład `11111111-1111-1111-1111-111111111111`)
* Identyfikator katalogu (identyfikator dzierżawy) (na przykład `22222222-2222-2222-2222-222222222222`)

Zastąp symbole zastępcze w poniższym poleceniu zapisanymi wcześniej informacjami i wykonaj polecenie w powłoce poleceń:

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

Aby określić lokalizację wyjściową, która tworzy folder projektu, jeśli nie istnieje, Uwzględnij opcję Output w poleceniu z ścieżką (na przykład `-o BlazorSample`). Nazwa folderu jest również częścią nazwy projektu.

## <a name="authentication-package"></a>Pakiet uwierzytelniania

Gdy aplikacja zostanie utworzona w celu korzystania z kont służbowych (`SingleOrg`), aplikacja automatycznie otrzymuje odwołanie do pakietu dla [biblioteki uwierzytelniania firmy Microsoft](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`). Pakiet zawiera zestaw elementów podstawowych, które ułatwiają aplikacji uwierzytelnianie użytkowników i uzyskiwanie tokenów do wywoływania chronionych interfejsów API.

W przypadku dodawania uwierzytelniania do aplikacji ręcznie Dodaj pakiet do pliku projektu aplikacji:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

Zastąp `{VERSION}` odwołanie do poprzedniego pakietu wersją `Microsoft.AspNetCore.Blazor.Templates` pakietu pokazanego w <xref:blazor/get-started> artykule.

Pakiet `Microsoft.Authentication.WebAssembly.Msal` zawiera przechodnie Dodawanie `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pakietu do aplikacji.

## <a name="authentication-service-support"></a>Obsługa usługi uwierzytelniania

Obsługa uwierzytelniania użytkowników jest rejestrowana w kontenerze usługi przy użyciu metody `AddMsalAuthentication` rozszerzenia dostarczonej przez `Microsoft.Authentication.WebAssembly.Msal` pakiet. Ta metoda umożliwia skonfigurowanie wszystkich usług wymaganych przez aplikację do współpracy z dostawcą tożsamości (IP).

*Program.cs*:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

`AddMsalAuthentication` Metoda akceptuje wywołanie zwrotne w celu skonfigurowania parametrów wymaganych do uwierzytelnienia aplikacji. Wartości wymagane do skonfigurowania aplikacji można uzyskać z konfiguracji kont Microsoft podczas rejestrowania aplikacji.

Konfiguracja jest dostarczana przez plik *wwwroot/appSettings. JSON* :

```json
{
    "AzureAd": {
        "Authority": "https://login.microsoftonline.com/{TENANT ID}",
        "ClientId": "{CLIENT ID}"
    }
}
```

Przykład:

```json
{
    "AzureAd": {
        "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
        "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd"
    }
}
```

## <a name="access-token-scopes"></a>Zakresy tokenów dostępu

Blazor Szablon webassembly nie konfiguruje automatycznie aplikacji do żądania tokenu dostępu dla bezpiecznego interfejsu API. Aby zainicjować obsługę administracyjną tokenu dostępu w ramach przepływu logowania, Dodaj zakres do domyślnych zakresów tokenów dostępu `MsalProviderOptions`:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> Jeśli Azure Portal udostępnia identyfikator URI zakresu, a **aplikacja zgłasza nieobsłużony wyjątek** , gdy odbierze *401 nieautoryzowaną* odpowiedź z interfejsu API, spróbuj użyć identyfikatora URI zakresu, który nie zawiera schematu i hosta. Na przykład Azure Portal może podać jeden z następujących formatów identyfikatorów URI zakresu:
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> Podaj identyfikator URI zakresu bez schematu i hosta:
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

Aby uzyskać więcej informacji, zobacz następujące sekcje *dodatkowych scenariuszy* :

* [Żądaj dodatkowych tokenów dostępu](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [Dołącz tokeny do żądań wychodzących](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

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

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/authentication/azure-active-directory/index>
* [Dokumentacja poświęcona platformie tożsamości firmy Microsoft](/azure/active-directory/develop/)
