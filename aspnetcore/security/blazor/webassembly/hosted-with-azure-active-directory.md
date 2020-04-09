---
title: Zabezpieczanie hostowanego Blazor przez ASP.NET core aplikacji sieci Web za pomocą usługi Azure Active Directory
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 8fec9f585f42469665cf29069674a199e1626629
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977135"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory"></a>Zabezpieczanie hostowanego Blazor przez ASP.NET core aplikacji sieci Web za pomocą usługi Azure Active Directory

Autorstwa [Javiera Calvarro Nelsona](https://github.com/javiercn) i [Luke'a Lathama](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]



W tym artykule opisano sposób tworzenia [ Blazor aplikacji hostowanej webassembly,](xref:blazor/hosting-models#blazor-webassembly) która używa [usługi Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) do uwierzytelniania.

## <a name="register-apps-in-aad-b2c-and-create-solution"></a>Rejestrowanie aplikacji w UAD B2C i tworzenie rozwiązania

### <a name="create-a-tenant"></a>Tworzenie dzierżawy

Postępuj zgodnie ze wskazówkami w [przewodniku Szybki start: Konfigurowanie dzierżawy](/azure/active-directory/develop/quickstart-create-new-tenant) w celu utworzenia dzierżawy w ujrzętm dalej.

### <a name="register-a-server-api-app"></a>Rejestrowanie aplikacji interfejsu API serwera

Postępuj zgodnie ze wskazówkami w [przewodniku Szybki start: Zarejestruj aplikację na platformie tożsamości firmy Microsoft](/azure/active-directory/develop/quickstart-register-app) i kolejnych tematach usługi Azure AAD, aby zarejestrować aplikację usługi AAD dla *aplikacji interfejsu API serwera* w obszarze**rejestracji aplikacji** usługi Azure **Active Directory** > w portalu Azure portal:

1. Wybierz **pozycję Nowa rejestracja**.
1. Podaj **nazwę** aplikacji (na przykład ** Blazor Server AAD**).
1. Wybierz **typy obsługiwanych kont**. Dla tego środowiska można wybrać opcję **Konta w tym katalogu organizacyjnym (tylko** pojedyncza dzierżawa).
1. *Aplikacja interfejsu API serwera* nie wymaga **identyfikatora URI przekierowania** w tym scenariuszu, więc pozostaw zestaw rozwijany do **sieci Web** i nie należy wprowadzać identyfikatora URI przekierowania.
1. Wyłącz pole **wyboru Uprawnienia** > **udzielaj administracyjnej do otwierania i offline_access uprawnień.**
1. Wybierz pozycję **Zarejestruj**.

W **uprawnieniach interfejsu API**usuń uprawnienie Microsoft **Graph** > **User.Read,** ponieważ aplikacja nie wymaga logowania ani dostępu do profilu uer.

W **expose interfejsu API:**

1. Wybierz polecenie **Dodaj zakres**.
1. Wybierz przycisk **Zapisz i kontynuuj**.
1. Podaj **nazwę zakresu** `API.Access`(na przykład ).
1. Podaj **nazwę wyświetlaną zgody administratora** (na przykład `Access API`).
1. Podaj **opis zgody administratora** (na `Allows the app to access server app API endpoints.`przykład).
1. Potwierdź, że **stan** jest ustawiony na **Włączone**.
1. Wybierz **pozycję Dodaj zakres**.

Zapisz następujące informacje:

* *Aplikacja interfejsu API serwera* Identyfikator aplikacji (identyfikator klienta) (na `11111111-1111-1111-1111-111111111111`przykład)
* Identyfikator URI aplikacji (na `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` `api://11111111-1111-1111-1111-111111111111`przykład , lub podana wartość niestandardowa)
* Identyfikator katalogu (identyfikator dzierżawy) (na `222222222-2222-2222-2222-222222222222`przykład )
* Domena dzierżawy usługi AAD (na przykład `contoso.onmicrosoft.com`)
* Domyślny zakres (na przykład `API.Access`)

### <a name="register-a-client-app"></a>Rejestrowanie aplikacji klienckiej

Postępuj zgodnie ze wskazówkami w [przewodniku Szybki start: Zarejestruj aplikację za pomocą platformy tożsamości firmy Microsoft](/azure/active-directory/develop/quickstart-register-app) i kolejnych tematów usługi Azure AAD, aby zarejestrować aplikację usługi AAD dla aplikacji *klienta* w obszarze**rejestracji aplikacji** usługi Azure **Active Directory** > w portalu Azure:

1. Wybierz **pozycję Nowa rejestracja**.
1. Podaj **nazwę** aplikacji (na przykład ** Blazor client AAD).**
1. Wybierz **typy obsługiwanych kont**. Dla tego środowiska można wybrać opcję **Konta w tym katalogu organizacyjnym (tylko** pojedyncza dzierżawa).
1. Pozostaw zestaw rozwijany **Przekierowania identyfikatora URI** `https://localhost:5001/authentication/login-callback`do sieci **Web**i podaj identyfikator URI przekierowania .
1. Wyłącz pole **wyboru Uprawnienia** > **udzielaj administracyjnej do otwierania i offline_access uprawnień.**
1. Wybierz pozycję **Zarejestruj**.

W**konfiguracji** > platformy **uwierzytelniania** > **w sieci Web:**

1. Upewnij się, że `https://localhost:5001/authentication/login-callback` identyfikator **URI przekierowania** jest obecny.
1. W przypadku **dotacji niejawnych**zaznacz pola wyboru **tokenów programu Access** i **tokenów identyfikatorów**.
1. Pozostałe wartości domyślne dla aplikacji są dopuszczalne dla tego środowiska.
1. Wybierz ikonę **Zapisz**.

W **uprawnieniach interfejsu API:**

1. Upewnij się, że aplikacja ma uprawnienie **Microsoft Graph** > **User.Read.**
1. Wybierz **pozycję Dodaj uprawnienie,** a następnie **moje interfejsy API**.
1. Wybierz *aplikację Interfejsu API serwera* z kolumny **Nazwa** (na przykład ** Blazor Serwer AAD**).
1. Otwórz listę **interfejsu API.**
1. Włącz dostęp do interfejsu API `API.Access`(na przykład ).
1. Wybierz pozycję **Dodaj uprawnienia**.
1. Wybierz **pozycję Przyznaj zawartość administratora dla przycisku {NAZWA NAJEMCY}.** Wybierz pozycję **Tak**, aby potwierdzić.

Zarejestruj identyfikator aplikacji *klienckiej* (identyfikator klienta) `33333333-3333-3333-3333-333333333333`(na przykład ).

### <a name="create-the-app"></a>Tworzymy aplikację.

Zastąp symbole zastępcze w następującym poleceniu informacjami zarejestrowanymi wcześniej i wykonaj polecenie w powłoce polecenia:

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

Aby określić lokalizację wyjściową, która tworzy folder projektu, jeśli nie istnieje, należy dołączyć opcję `-o BlazorSample`wyjściową w poleceniu ze ścieżką (na przykład ). Nazwa folderu również staje się częścią nazwy projektu.

> [!NOTE]
> Przekaż identyfikator URI identyfikatora `app-id-uri` aplikacji do opcji, ale zanotuj, że zmiana konfiguracji może być wymagana w aplikacji klienckiej, która jest opisana w sekcji [Zakresy tokenów dostępu.](#access-token-scopes)

## <a name="server-app-configuration"></a>Konfiguracja aplikacji serwera

*Ta sekcja dotyczy aplikacji **Server** rozwiązania.*

### <a name="authentication-package"></a>Pakiet uwierzytelniania

Obsługa uwierzytelniania i autoryzowania wywołań ASP.NET core interfejsów API sieci `Microsoft.AspNetCore.Authentication.AzureAD.UI`Web jest świadczona przez:

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a>Obsługa usługi uwierzytelniania

Metoda `AddAuthentication` konfiguruje usługi uwierzytelniania w aplikacji i konfiguruje program obsługi nośnika JWT jako domyślną metodę uwierzytelniania. Metoda `AddAzureADBearer` konfiguruje określone parametry w programie obsługi nośnika JWT wymaganym do sprawdzania poprawności tokenów emitowanych przez usługę Azure Active Directory:

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

`UseAuthentication`i `UseAuthorization` zapewnić, że:

* Aplikacja próbuje przeanalizować i sprawdzić poprawność tokenów w żądaniach przychodzących.
* Każde żądanie próbujące uzyskać dostęp do chronionego zasobu bez odpowiednich poświadczeń kończy się niepowodzeniem.

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a>User.Identity.Name

Domyślnie interfejs API aplikacji serwera `User.Identity.Name` wypełnia wartość `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` z typu oświadczenia `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`(na przykład ).

Aby skonfigurować aplikację do odbierania `name` wartości z typu oświadczenia, należy skonfigurować [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> w: `Startup.ConfigureServices`

```csharp
services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a>Ustawienia aplikacji

Plik *appsettings.json* zawiera opcje konfigurowania programu obsługi na okaziciela JWT używanego do sprawdzania poprawności tokenów dostępu.

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

Kontroler WeatherForecast *(Controllers/WeatherForecastController.cs)* udostępnia chroniony interfejs `[Authorize]` API z atrybutem zastosowanym do kontrolera. **Ważne** jest, aby zrozumieć, że:

* Atrybut `[Authorize]` w tym kontrolerze interfejsu API jest jedyną rzeczą, która chroni ten interfejs API przed nieautoryzowanym dostępem.
* Atrybut `[Authorize]` używany w Blazor aplikacji WebAssembly służy tylko jako wskazówka do aplikacji, że użytkownik powinien być autoryzowany dla aplikacji do poprawnego działania.

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

## <a name="client-app-configuration"></a>Konfiguracja aplikacji klienckiej

*Ta sekcja dotyczy aplikacji **klienta** rozwiązania.*

### <a name="authentication-package"></a>Pakiet uwierzytelniania

Po utworzeniu aplikacji do używania`SingleOrg`kont służbowych ( aplikacja automatycznie otrzymuje odwołanie`Microsoft.Authentication.WebAssembly.Msal`do pakietu dla [biblioteki uwierzytelniania Firmy Microsoft](/azure/active-directory/develop/msal-overview) ( ). Pakiet zawiera zestaw uwierzytelniania, które pomagają aplikacji uwierzytelniać użytkowników i uzyskać tokeny do wywoływania chronionych interfejsów API.

Jeśli dodasz uwierzytelnianie do aplikacji, ręcznie dodaj pakiet do pliku projektu aplikacji:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

Zastąp `{VERSION}` w poprzednim odwołaniu `Microsoft.AspNetCore.Blazor.Templates` do pakietu <xref:blazor/get-started> wersją pakietu przedstawioną w artykule.

Pakiet `Microsoft.Authentication.WebAssembly.Msal` przechodnie dodaje `Microsoft.AspNetCore.Components.WebAssembly.Authentication` pakiet do aplikacji.

### <a name="authentication-service-support"></a>Obsługa usługi uwierzytelniania

Obsługa uwierzytelniania użytkowników jest zarejestrowana w `AddMsalAuthentication` kontenerze usługi `Microsoft.Authentication.WebAssembly.Msal` przy użyciu metody rozszerzenia dostarczonej przez pakiet. Ta metoda konfiguruje wszystkie usługi wymagane dla aplikacji do interakcji z dostawcą tożsamości (IP).

*Program.cs:*

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

Metoda `AddMsalAuthentication` akceptuje wywołanie zwrotne, aby skonfigurować parametry wymagane do uwierzytelnienia aplikacji. Wartości wymagane do skonfigurowania aplikacji można uzyskać z konfiguracji usługi AAD usługi Azure Portal podczas rejestracji aplikacji.

### <a name="access-token-scopes"></a>Zakresy tokenów dostępu

Domyślne zakresy tokenów dostępu reprezentują listę zakresów tokenów dostępu, które są:

* Domyślnie uwzględnione w żądaniu logowania.
* Służy do inicjowania obsługi administracyjnej tokenu dostępu natychmiast po uwierzytelnieniu.

Wszystkie zakresy muszą należeć do tej samej aplikacji zgodnie z regułami usługi Azure Active Directory. W razie potrzeby można dodać dodatkowe zakresy dla dodatkowych aplikacji interfejsu API:

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

### <a name="imports-file"></a>Import pliku

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>Strona indeksu

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a>Składnik aplikacji

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>Składnik RedirectToLogin

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>Składnik LogowanieWydajnik

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a>Składnik uwierzytelniania

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>Składnik FetchData

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>Uruchomienie aplikacji

Uruchom aplikację z projektu serwera. Korzystając z programu Visual Studio, wybierz projekt serwera w **Eksploratorze rozwiązań** i wybierz przycisk **Uruchom** na pasku narzędzi lub uruchom aplikację z menu **debugowania.**

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Żądanie dodatkowych tokenów dostępu](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* <xref:security/authentication/azure-active-directory/index>
* [Dokumentacja poświęcona platformie tożsamości firmy Microsoft](/azure/active-directory/develop/)
