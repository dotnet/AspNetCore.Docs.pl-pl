---
title: Zabezpieczanie autonomicznej Blazor aplikacji ASP.NET Core WebAssembly za pomocą kont Microsoft
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: 8c409651b3338c2baeae497bef43b994823a20f9
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977083"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a>Zabezpieczanie autonomicznej Blazor aplikacji ASP.NET Core WebAssembly za pomocą kont Microsoft

Autorstwa [Javiera Calvarro Nelsona](https://github.com/javiercn) i [Luke'a Lathama](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Aby utworzyć Blazor autonomiczną aplikację WebAssembly, która używa [kont Microsoft z usługą Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) do uwierzytelniania:

1. [Tworzenie dzierżawy usługi AAD i aplikacji sieci Web](/azure/active-directory/develop/v2-overview)

   Zarejestruj aplikację usługi AAD w obszarze**rejestracji aplikacji** usługi Azure **Active Directory** > w witrynie Azure portal:

   1\. Podaj **nazwę** aplikacji (na przykład ** Blazor client AAD).**<br>
   2\. W **obszarze Obsługiwane typy kont**wybierz pozycję Konta w dowolnym katalogu **organizacji**.<br>
   3\. Pozostaw zestaw rozwijany **Przekierowania identyfikatora URI** `https://localhost:5001/authentication/login-callback`do sieci **Web**i podaj identyfikator URI przekierowania .<br>
   4\. Wyłącz pole **wyboru Uprawnienia** > **udzielaj administracyjnej do otwierania i offline_access uprawnień.**<br>
   5\. Wybierz pozycję **Zarejestruj**.

   W**konfiguracji** > platformy **uwierzytelniania** > **w sieci Web:**

   1\. Upewnij się, że `https://localhost:5001/authentication/login-callback` identyfikator **URI przekierowania** jest obecny.<br>
   2\. W przypadku **dotacji niejawnych**zaznacz pola wyboru **tokenów programu Access** i **tokenów identyfikatorów**.<br>
   3\. Pozostałe wartości domyślne dla aplikacji są dopuszczalne dla tego środowiska.<br>
   4\. Wybierz ikonę **Zapisz**.

   Zarejestruj identyfikator aplikacji (identyfikator klienta) (na `11111111-1111-1111-1111-111111111111`przykład ).

1. Zastąp symbole zastępcze w następującym poleceniu informacjami zarejestrowanymi wcześniej i wykonaj polecenie w powłoce polecenia:

   ```dotnetcli
   dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
   ```

   Aby określić lokalizację wyjściową, która tworzy folder projektu, jeśli nie istnieje, należy dołączyć opcję `-o BlazorSample`wyjściową w poleceniu ze ścieżką (na przykład ). Nazwa folderu również staje się częścią nazwy projektu.

Po utworzeniu aplikacji powinieneś być w stanie:

* Zaloguj się do aplikacji przy użyciu konta Microsoft.
* Żądaj tokenów dostępu dla interfejsów API firmy Microsoft Blazor przy użyciu tego samego podejścia, co w przypadku aplikacji autonomicznych, pod warunkiem że aplikacja została poprawnie skonfigurowana. Aby uzyskać więcej informacji, zobacz [Szybki start: Konfigurowanie aplikacji w celu udostępnienia interfejsów API sieci Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).

## <a name="authentication-package"></a>Pakiet uwierzytelniania

Po utworzeniu aplikacji do używania`SingleOrg`kont służbowych ( aplikacja automatycznie otrzymuje odwołanie`Microsoft.Authentication.WebAssembly.Msal`do pakietu dla [biblioteki uwierzytelniania Firmy Microsoft](/azure/active-directory/develop/msal-overview) ( ). Pakiet zawiera zestaw uwierzytelniania, które pomagają aplikacji uwierzytelniać użytkowników i uzyskać tokeny do wywoływania chronionych interfejsów API.

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
    authentication.Authority = "{AUTHORITY}";
    authentication.ClientId = "{CLIENT ID}";
});
```

Metoda `AddMsalAuthentication` akceptuje wywołanie zwrotne, aby skonfigurować parametry wymagane do uwierzytelnienia aplikacji. Wartości wymagane do skonfigurowania aplikacji można uzyskać z konfiguracji kont Microsoft podczas rejestracji aplikacji.

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
* [Szybki start: rejestrowanie aplikacji na platformie tożsamości firmy Microsoft](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [Szybki start: konfigurowanie aplikacji do udostępniania interfejsów API sieci Web](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
