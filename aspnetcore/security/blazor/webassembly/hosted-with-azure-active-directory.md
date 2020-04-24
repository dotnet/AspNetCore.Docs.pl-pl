---
title: Zabezpiecz aplikację hostowaną ASP.NET Core Blazor webassembly przy użyciu Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 8c24546da50607d692a9cdc9f9c007d6ac8645ad
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82110931"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory"></a>Zabezpiecz aplikację hostowaną ASP.NET Core Blazor webassembly przy użyciu Azure Active Directory

Autorzy [Javier Calvarro Nelson](https://github.com/javiercn) i [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> Wskazówki zawarte w tym artykule dotyczą ASP.NET Core 3,2 w wersji zapoznawczej 4. Ten temat zostanie zaktualizowany do wersji zapoznawczej 5 w piątek, 24 kwietnia.

W tym artykule opisano sposób tworzenia [ Blazor aplikacji hostowanej w programie webassembly](xref:blazor/hosting-models#blazor-webassembly) , która używa usługi [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) do uwierzytelniania.

## <a name="register-apps-in-aad-b2c-and-create-solution"></a>Rejestrowanie aplikacji w AAD B2C i tworzenie rozwiązania

### <a name="create-a-tenant"></a>Tworzenie dzierżawy

Postępuj zgodnie ze wskazówkami w [przewodniku szybki start: Konfigurowanie dzierżawy](/azure/active-directory/develop/quickstart-create-new-tenant) w celu utworzenia dzierżawy w usłudze AAD.

### <a name="register-a-server-api-app"></a>Rejestrowanie aplikacji interfejsu API serwera

Postępuj zgodnie ze wskazówkami w [przewodniku szybki start: Zarejestruj aplikację przy użyciu platformy tożsamości firmy Microsoft](/azure/active-directory/develop/quickstart-register-app) i kolejnych tematów usługi Azure AAD, aby zarejestrować aplikację w usłudze AAD dla *aplikacji interfejsu API serwera* w obszarze **Azure Active Directory** > **rejestracje aplikacji** w Azure Portal:

1. Wybierz pozycję **Nowa rejestracja**.
1. Podaj **nazwę** aplikacji (na przykład ** Blazor serwer AAD**).
1. Wybierz **obsługiwane typy kont**. W tym środowisku możesz wybrać **tylko konta w tym katalogu organizacji** (pojedynczy dzierżawca).
1. *Aplikacja interfejsu API serwera* nie wymaga **identyfikatora URI przekierowania** w tym scenariuszu, więc pozostaw listę rozwijaną w **sieci Web** i nie wprowadzaj identyfikatora URI przekierowania.
1. Wyłącz pole wyboru**Przyznaj administratorowi uprawnienia do OpenID Connect i uprawnień offline_access** . **Permissions** > 
1. Wybierz pozycję **Zarejestruj**.

W obszarze **uprawnienia interfejsu API**usuń uprawnienie **Microsoft Graph** > **User. Read** , ponieważ aplikacja nie wymaga dostępu do profilu Logowanie lub UER.

W obszarze **Uwidacznianie interfejsu API**:

1. Wybierz polecenie **Dodaj zakres**.
1. Wybierz przycisk **Zapisz i kontynuuj**.
1. Podaj **nazwę zakresu** (na przykład `API.Access`).
1. Podaj **nazwę wyświetlaną zgody administratora** (na przykład `Access API`).
1. Podaj **Opis zgody administratora** (na przykład `Allows the app to access server app API endpoints.`).
1. Upewnij się, że **stan** jest ustawiony na **włączone**.
1. Wybierz pozycję **Dodaj zakres**.

Zapisz następujące informacje:

* *Aplikacja interfejsu API serwera* Identyfikator aplikacji (identyfikator klienta) (na przykład `11111111-1111-1111-1111-111111111111`)
* Identyfikator URI identyfikatora aplikacji (na przykład `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` `api://11111111-1111-1111-1111-111111111111`, lub podana wartość niestandardowa)
* Identyfikator katalogu (identyfikator dzierżawy) (na przykład `222222222-2222-2222-2222-222222222222`)
* Domena dzierżawy usługi AAD (na `contoso.onmicrosoft.com`przykład)
* Zakres domyślny (na przykład `API.Access`)

### <a name="register-a-client-app"></a>Rejestrowanie aplikacji klienckiej

Postępuj zgodnie ze wskazówkami w [przewodniku szybki start: Zarejestruj aplikację przy użyciu platformy tożsamości firmy Microsoft](/azure/active-directory/develop/quickstart-register-app) i kolejnych tematów usługi Azure AAD, aby zarejestrować aplikację w usłudze AAD dla *aplikacji klienckiej* w obszarze **Azure Active Directory** > **rejestracje aplikacji** w Azure Portal:

1. Wybierz pozycję **Nowa rejestracja**.
1. Podaj **nazwę** aplikacji (na przykład ** Blazor klienta AAD**).
1. Wybierz **obsługiwane typy kont**. W tym środowisku możesz wybrać **tylko konta w tym katalogu organizacji** (pojedynczy dzierżawca).
1. Pozostaw pole listy rozwijanej **Identyfikator URI przekierowania** na **Sieć Web**i podaj identyfikator URI `https://localhost:5001/authentication/login-callback`przekierowania.
1. Wyłącz pole wyboru**Przyznaj administratorowi uprawnienia do OpenID Connect i uprawnień offline_access** . **Permissions** > 
1. Wybierz pozycję **Zarejestruj**.

W obszarze**konfiguracje** > platformy **uwierzytelniania** > w**sieci Web**:

1. Upewnij się, że `https://localhost:5001/authentication/login-callback` jest obecny **Identyfikator URI przekierowania** .
1. W przypadku **niejawnego przydzielenia**zaznacz pola wyboru dla **tokenów dostępu** i **tokenów identyfikatorów**.
1. Pozostałe wartości domyślne dla aplikacji są dopuszczalne dla tego środowiska.
1. Wybierz ikonę **Zapisz**.

W **uprawnienia interfejsu API**:

1. Upewnij się, że aplikacja ma **Microsoft Graph** > uprawnienie**użytkownika. odczyt** .
1. Wybierz pozycję **Dodaj uprawnienia** , a następnie **Moje interfejsy API**.
1. Wybierz *aplikację interfejsu API serwera* z kolumny **Nazwa** (na przykład ** Blazor serwer AAD**).
1. Otwórz listę **interfejsów API** .
1. Włącz dostęp do interfejsu API (na przykład `API.Access`).
1. Wybierz pozycję **Dodaj uprawnienia**.
1. Wybierz przycisk **Udziel zawartości administratora dla {Nazwa dzierżawy}** . Wybierz pozycję **Tak**, aby potwierdzić.

Zapisz identyfikator aplikacji *klienta* (identyfikator klienta) (na przykład `33333333-3333-3333-3333-333333333333`).

### <a name="create-the-app"></a>Tworzymy aplikację.

Zastąp symbole zastępcze w poniższym poleceniu zapisanymi wcześniej informacjami i wykonaj polecenie w powłoce poleceń:

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

Aby określić lokalizację wyjściową, która tworzy folder projektu, jeśli nie istnieje, Uwzględnij opcję Output w poleceniu z ścieżką (na przykład `-o BlazorSample`). Nazwa folderu jest również częścią nazwy projektu.

> [!NOTE]
> Przekaż identyfikator URI aplikacji do `app-id-uri` opcji, ale Uwaga w aplikacji klienckiej może być wymagana zmiana konfiguracji, która jest opisana w sekcji [zakresy tokenu dostępu](#access-token-scopes) .

## <a name="server-app-configuration"></a>Konfiguracja aplikacji serwera

*Ta sekcja dotyczy aplikacji **serwerowej** rozwiązania.*

### <a name="authentication-package"></a>Pakiet uwierzytelniania

Obsługa uwierzytelniania i autoryzowania wywołań ASP.NET Core interfejsów API sieci Web jest zapewniana przez `Microsoft.AspNetCore.Authentication.AzureAD.UI`:

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a>Obsługa usługi uwierzytelniania

`AddAuthentication` Metoda konfiguruje usługi uwierzytelniania w ramach aplikacji i konfiguruje procedurę obsługi okaziciela JWT jako domyślną metodę uwierzytelniania. `AddAzureADBearer` Metoda ustawia określone parametry w obsłudze okaziciela JWT wymagane do weryfikacji tokenów emitowanych przez Azure Active Directory:

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

`UseAuthentication`i `UseAuthorization` upewnij się, że:

* Aplikacja próbuje analizować tokeny i sprawdzać ich poprawność w żądaniach przychodzących.
* Wszystkie żądania próbujące uzyskać dostęp do chronionego zasobu bez poprawnego poświadczenia nie powiedzie się.

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a>User.Identity.Name

Domyślnie interfejs API aplikacji serwera wypełnia `User.Identity.Name` wartość z `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` typu "typ" (na przykład `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).

`name` Aby skonfigurować aplikację do odbierania wartości z typu, należy skonfigurować [TokenValidationParameters. NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> w programie: `Startup.ConfigureServices`

```csharp
services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a>Ustawienia aplikacji

Plik *appSettings. JSON* zawiera opcje konfigurowania procedury obsługi okaziciela JWT używanej do sprawdzania poprawności tokenów dostępu.

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{API CLIENT ID}",
  }
}
```

### <a name="weatherforecast-controller"></a>Kontroler WeatherForecast

Kontroler WeatherForecast (*controllers/WeatherForecastController. cs*) ujawnia chroniony interfejs API z `[Authorize]` atrybutem zastosowanym do kontrolera. **Ważne** jest, aby zrozumieć, że:

* `[Authorize]` Atrybut w tym kontrolerze interfejsu API jest jedynym warunkiem, że ten interfejs API jest chroniony przed nieautoryzowanym dostępem.
* `[Authorize]` Atrybut używany w Blazor aplikacji webassembly służy tylko jako Wskazówka dla aplikacji, którą użytkownik powinien mieć autoryzację, aby aplikacja działała poprawnie.

```csharp
[Authorize]
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet]
    public IEnumerable<WeatherForecast> Get()
    {
        ...
    }
}
```

## <a name="client-app-configuration"></a>Konfiguracja aplikacji klienta

*Ta sekcja dotyczy aplikacji **klienckiej** rozwiązania.*

### <a name="authentication-package"></a>Pakiet uwierzytelniania

Gdy aplikacja zostanie utworzona w celu korzystania z kont służbowych (`SingleOrg`), aplikacja automatycznie otrzymuje odwołanie do pakietu dla [biblioteki uwierzytelniania firmy Microsoft](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`). Pakiet zawiera zestaw elementów podstawowych, które ułatwiają aplikacji uwierzytelnianie użytkowników i uzyskiwanie tokenów do wywoływania chronionych interfejsów API.

W przypadku dodawania uwierzytelniania do aplikacji ręcznie Dodaj pakiet do pliku projektu aplikacji:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

Zastąp `{VERSION}` odwołanie do poprzedniego pakietu wersją `Microsoft.AspNetCore.Blazor.Templates` pakietu pokazanego w <xref:blazor/get-started> artykule.

Pakiet `Microsoft.Authentication.WebAssembly.Msal` zawiera przechodnie Dodawanie `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pakietu do aplikacji.

### <a name="authentication-service-support"></a>Obsługa usługi uwierzytelniania

Obsługa uwierzytelniania użytkowników jest rejestrowana w kontenerze usługi przy użyciu metody `AddMsalAuthentication` rozszerzenia dostarczonej przez `Microsoft.Authentication.WebAssembly.Msal` pakiet. Ta metoda umożliwia skonfigurowanie wszystkich usług wymaganych przez aplikację do współpracy z dostawcą tożsamości (IP).

*Program.cs*:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

`AddMsalAuthentication` Metoda akceptuje wywołanie zwrotne w celu skonfigurowania parametrów wymaganych do uwierzytelnienia aplikacji. Wartości wymagane do skonfigurowania aplikacji można uzyskać z konfiguracji usługi AAD w witrynie Azure Portal podczas rejestrowania aplikacji.

### <a name="access-token-scopes"></a>Zakresy tokenów dostępu

Domyślne zakresy tokenów dostępu reprezentują listę zakresów tokenów dostępu, które są następujące:

* Uwzględnione domyślnie w żądaniu logowania.
* Służy do aprowizacji tokenu dostępu zaraz po uwierzytelnieniu.

Wszystkie zakresy muszą należeć do tej samej aplikacji na Azure Active Directory reguł. Dodatkowe zakresy można dodać do dodatkowych aplikacji interfejsu API w razie potrzeby:

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

Aby uzyskać więcej informacji, zobacz <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.

<!--
    For more information, see <xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests>.
-->

### <a name="imports-file"></a>Importuje plik

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>Strona indeksu

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a>Składnik aplikacji

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>Składnik RedirectToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>Składnik LoginDisplay

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a>Składnik uwierzytelniania

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>Składnik FetchData

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>Uruchomienie aplikacji

Uruchom aplikację z projektu serwera. W przypadku korzystania z programu Visual Studio wybierz projekt serwera w **Eksplorator rozwiązań** a następnie wybierz przycisk **Uruchom** na pasku narzędzi lub Uruchom aplikację z menu **Debuguj** .

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/authentication/azure-active-directory/index>
* [Dokumentacja poświęcona platformie tożsamości firmy Microsoft](/azure/active-directory/develop/)
