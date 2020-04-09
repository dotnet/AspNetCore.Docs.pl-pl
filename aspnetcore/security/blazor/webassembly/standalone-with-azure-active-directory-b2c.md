---
title: Zabezpieczanie autonomicznej Blazor aplikacji ASP.NET Core WebAssembly za pomocą usługi Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 0734bad2d4281eb856783a362ef8c608a303c17a
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977057"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a>Zabezpieczanie autonomicznej Blazor aplikacji ASP.NET Core WebAssembly za pomocą usługi Azure Active Directory B2C

Autorstwa [Javiera Calvarro Nelsona](https://github.com/javiercn) i [Luke'a Lathama](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Aby utworzyć Blazor autonomiczną aplikację WebAssembly, która używa [usługi Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) do uwierzytelniania:

1. Postępuj zgodnie ze wskazówkami w następujących tematach, aby utworzyć dzierżawę i zarejestrować aplikację sieci web w witrynie Azure Portal:

   * &ndash; [Utwórz dzierżawę usługi AAD B2C](/azure/active-directory-b2c/tutorial-create-tenant) Rejestr następujących informacji:

     1\. AAD B2C (na `https://contoso.b2clogin.com/`przykład , który zawiera ukośnik)<br>
     2\. Domena dzierżawy AAD B2C (na `contoso.onmicrosoft.com`przykład)

   * [Zarejestruj aplikację](/azure/active-directory-b2c/tutorial-register-applications) &ndash; internetową Dokonaj następujących wyborów podczas rejestracji aplikacji:

     1\. Ustaw **interfejs API aplikacji sieci Web / sieci Web** na **Tak**.<br>
     2\. Ustaw **zezwalaj na przepływ niejawny** tak . **Yes**<br>
     3\. Dodaj **adres** URL `https://localhost:5001/authentication/login-callback`odpowiedzi .

     Zarejestruj identyfikator aplikacji (identyfikator klienta) (na `11111111-1111-1111-1111-111111111111`przykład ).

   * [Tworzenie przepływów](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; użytkowników Tworzenie przepływu użytkownika rejestracji i logowania.

     Co najmniej wybierz atrybut Użytkownika Nazwa > **wyświetlana** **oświadczeń aplikacji,** `LoginDisplay` aby wypełnić `context.User.Identity.Name` składnik (*Shared/LoginDisplay.brzytwa*).

     Nagraj nazwę przepływu użytkownika rejestracji i logowania utworzoną `B2C_1_signupsignin`dla aplikacji (na przykład ).

1. Zastąp symbole zastępcze w następującym poleceniu informacjami zarejestrowanymi wcześniej i wykonaj polecenie w powłoce polecenia:

   ```dotnetcli
   dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
   ```

   Aby określić lokalizację wyjściową, która tworzy folder projektu, jeśli nie istnieje, należy dołączyć opcję `-o BlazorSample`wyjściową w poleceniu ze ścieżką (na przykład ). Nazwa folderu również staje się częścią nazwy projektu.

## <a name="authentication-package"></a>Pakiet uwierzytelniania

Po utworzeniu aplikacji do korzystania z indywidualnego konta B2C (`IndividualB2C`aplikacja automatycznie otrzymuje odwołanie do pakietu dla [biblioteki uwierzytelniania Firmy Microsoft](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`). Pakiet zawiera zestaw uwierzytelniania, które pomagają aplikacji uwierzytelniać użytkowników i uzyskać tokeny do wywoływania chronionych interfejsów API.

Jeśli dodasz uwierzytelnianie do aplikacji, ręcznie dodaj pakiet do pliku projektu aplikacji:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

Zastąp `{VERSION}` w poprzednim odwołaniu `Microsoft.AspNetCore.Blazor.Templates` do pakietu <xref:blazor/get-started> wersją pakietu przedstawioną w artykule.

Pakiet `Microsoft.Authentication.WebAssembly.Msal` przechodnie dodaje `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pakiet do aplikacji.

## <a name="authentication-service-support"></a>Obsługa usługi uwierzytelniania

Obsługa uwierzytelniania użytkowników jest zarejestrowana w `AddMsalAuthentication` kontenerze usługi `Microsoft.Authentication.WebAssembly.Msal` przy użyciu metody rozszerzenia dostarczonej przez pakiet. Ta metoda konfiguruje wszystkie usługi wymagane dla aplikacji do interakcji z dostawcą tożsamości (IP).

*Program.cs:*

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = 
        "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}";
    authentication.ClientId = "{CLIENT ID}";
    authentication.ValidateAuthority = false;
});
```

Metoda `AddMsalAuthentication` akceptuje wywołanie zwrotne, aby skonfigurować parametry wymagane do uwierzytelnienia aplikacji. Wartości wymagane do skonfigurowania aplikacji można uzyskać z konfiguracji usługi AAD usługi Azure Portal podczas rejestracji aplikacji.

## <a name="access-token-scopes"></a>Zakresy tokenów dostępu

Szablon Blazor WebAssembly nie konfiguruje automatycznie aplikacji do żądania tokenu dostępu dla bezpiecznego interfejsu API. Aby aprowizować token jako część przepływu logowania, dodaj zakres do domyślnych zakresów tokenu `MsalProviderOptions`dostępu:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
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
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

Aby uzyskać więcej informacji, zobacz <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.

## <a name="imports-file"></a>Import pliku

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a>Strona indeksu

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

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
* <xref:security/authentication/azure-ad-b2c>
* [Samouczek: tworzenie dzierżawy usługi Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-create-tenant)
* [Dokumentacja poświęcona platformie tożsamości firmy Microsoft](/azure/active-directory/develop/)
