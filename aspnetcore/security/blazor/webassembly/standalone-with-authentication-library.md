---
title: Zabezpieczanie autonomicznej Blazor aplikacji ASP.NET Core WebAssembly za pomocą biblioteki uwierzytelniania
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-authentication-library
ms.openlocfilehash: 893fff10df37e1c2be549604f4cb83cd20049108
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977044"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-the-authentication-library"></a>Zabezpieczanie autonomicznej Blazor aplikacji ASP.NET Core WebAssembly za pomocą biblioteki uwierzytelniania

Autorstwa [Javiera Calvarro Nelsona](https://github.com/javiercn) i [Luke'a Lathama](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

*W przypadku usługi Azure Active Directory (AAD) i usługi Azure Active Directory B2C (AAD B2C) nie postępuj zgodnie ze wskazówkami zawartymi w tym temacie. Zobacz tematy AAD i AAD B2C w tym węźle spisu treści.*

Aby utworzyć Blazor autonomiczną aplikację WebAssembly, `Microsoft.AspNetCore.Components.WebAssembly.Authentication` która korzysta z biblioteki, wykonaj następujące polecenie w powłoce poleceń:

```dotnetcli
dotnet new blazorwasm -au Individual
```

Aby określić lokalizację wyjściową, która tworzy folder projektu, jeśli nie istnieje, należy dołączyć opcję `-o BlazorSample`wyjściową w poleceniu ze ścieżką (na przykład ). Nazwa folderu również staje się częścią nazwy projektu.

W programie Visual Studio [utwórz aplikację Blazor WebAssembly](xref:blazor/get-started). Ustaw **uwierzytelnianie** na **indywidualne konta użytkowników** za pomocą opcji Konta użytkowników Sklepu w **aplikacji.**

## <a name="authentication-package"></a>Pakiet uwierzytelniania

Po utworzeniu aplikacji do korzystania z kont poszczególnych użytkowników aplikacja `Microsoft.AspNetCore.Components.WebAssembly.Authentication` automatycznie odbiera odwołanie do pakietu w pliku projektu aplikacji. Pakiet zawiera zestaw uwierzytelniania, które pomagają aplikacji uwierzytelniać użytkowników i uzyskać tokeny do wywoływania chronionych interfejsów API.

Jeśli dodasz uwierzytelnianie do aplikacji, ręcznie dodaj pakiet do pliku projektu aplikacji:

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

Zastąp `{VERSION}` w poprzednim odwołaniu `Microsoft.AspNetCore.Blazor.Templates` do pakietu <xref:blazor/get-started> wersją pakietu przedstawioną w artykule.

## <a name="authentication-service-support"></a>Obsługa usługi uwierzytelniania

Obsługa uwierzytelniania użytkowników jest zarejestrowana w `AddOidcAuthentication` kontenerze usługi `Microsoft.AspNetCore.Components.WebAssembly.Authentication` przy użyciu metody rozszerzenia dostarczonej przez pakiet. Ta metoda konfiguruje wszystkie usługi wymagane dla aplikacji do interakcji z dostawcą tożsamości (IP).

*Program.cs:*

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    options.ProviderOptions.Authority = "{AUTHORITY}";
    options.ProviderOptions.ClientId = "{CLIENT ID}";
});
```

Obsługa uwierzytelniania dla autonomicznych aplikacji jest oferowana przy użyciu funkcji Open ID Connect (OIDC). Metoda `AddOidcAuthentication` akceptuje wywołanie zwrotne, aby skonfigurować parametry wymagane do uwierzytelnienia aplikacji przy użyciu funkcji OIDC. Wartości wymagane do skonfigurowania aplikacji można uzyskać z adresu IP zgodnego ze standardem OIDC. Uzyskaj wartości podczas rejestracji aplikacji, która zazwyczaj występuje w ich portalu online.

## <a name="access-token-scopes"></a>Zakresy tokenów dostępu

Szablon Blazor WebAssembly nie konfiguruje automatycznie aplikacji do żądania tokenu dostępu dla bezpiecznego interfejsu API. Aby aprowizować token jako część przepływu logowania, dodaj zakres do `OidcProviderOptions`domyślnych zakresów tokenu:

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> Jeśli portal Azure udostępnia identyfikator URI zakresu i **aplikacja zgłasza nieobsługiwanie wyjątek** po otrzymaniu *401 nieautoryzowanej* odpowiedzi z interfejsu API, spróbuj użyć identyfikatora URI zakresu, który nie zawiera schematu i hosta. Na przykład witryny Azure portal może zapewnić jeden z następujących formatów identyfikatora URI zakresu:
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> Podać identyfikator URI zakresu bez schematu i hosta:
>
> ```csharp
> options.ProviderOptions.DefaultScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

Aby uzyskać więcej informacji, zobacz <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.

## <a name="imports-file"></a>Import pliku

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a>Strona indeksu

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a>Składnik aplikacji

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a>Składnik RedirectToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>Składnik LogowanieWydajnik

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a>Składnik uwierzytelniania

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Żądanie dodatkowych tokenów dostępu](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
