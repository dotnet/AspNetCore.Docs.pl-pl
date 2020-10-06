---
title: Wprowadzenie do uwierzytelniania aplikacji jednostronicowych na ASP.NET Core
author: javiercn
description: Używanie Identity z aplikacją jednostronicową hostowaną w aplikacji ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 11/08/2019
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
uid: security/authentication/identity/spa
ms.openlocfilehash: 1e50fb2f2f5e3621b189f756c53e80a2dd64c8a6
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91754531"
---
# <a name="authentication-and-authorization-for-spas"></a>Uwierzytelnianie i autoryzacja dla aplikacji jednostronicowych

Szablony ASP.NET Core 3,1 i nowsze oferują uwierzytelnianie w aplikacjach jednostronicowych (aplikacji jednostronicowych) przy użyciu obsługi autoryzacji interfejsu API. ASP.NET Core Identityuwierzytelnianie i przechowywanie użytkowników są łączone z [ Identity serwerem](https://identityserver.io/) w celu zaimplementowania połączenia OpenID Connect.

Parametr uwierzytelniania został dodany do szablonów projektów **kątowych** i **reagowania** , które są podobne do parametrów uwierzytelniania w szablonach **aplikacji sieci Web (Model-View-Controller)** (MVC) i **aplikacji sieci Web** ( Razor strony). Dozwolone wartości parametrów to **none** i **indywidualny**. Szablon projektu **React.js i Redux** nie obsługuje w tym momencie parametru Authentication.

## <a name="create-an-app-with-api-authorization-support"></a>Tworzenie aplikacji z obsługą autoryzacji interfejsu API

Uwierzytelnianie i autoryzacja użytkowników mogą być używane z aplikacji jednostronicowychą kątową i reagują. Otwórz powłokę poleceń i uruchom następujące polecenie:

**Kątowy**:

```dotnetcli
dotnet new angular -o <output_directory_name> -au Individual
```

**Reagowanie**:

```dotnetcli
dotnet new react -o <output_directory_name> -au Individual
```

Poprzednie polecenie tworzy aplikację ASP.NET Core z katalogiem *ClientApp* zawierającym spa.

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a>Ogólny opis składników ASP.NET Core aplikacji

W poniższych sekcjach opisano Dodatki do projektu w przypadku włączenia obsługi uwierzytelniania:

### <a name="startup-class"></a>Klasa początkowa

Poniższe przykłady kodu bazują na [Microsoft. AspNetCore. ApiAuthorization. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.ApiAuthorization.IdentityServer) Pakiet NuGet serwera. Przykłady konfigurowania uwierzytelniania i autoryzacji interfejsu API przy użyciu <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiResourceCollection.AddIdentityServerJwt%2A> metod rozszerzenia i. Projekty używające szablonów projektów reaguje lub SPA z uwierzytelnianiem zawierają odwołanie do tego pakietu.

`Startup`Klasa zawiera następujące dodatki:

* Wewnątrz `Startup.ConfigureServices` metody:
  * Identity przy użyciu domyślnego interfejsu użytkownika:

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * IdentitySerwer z dodatkową `AddApiAuthorization` metodą pomocniczą, która konfiguruje niektóre domyślne konwencje ASP.NET Core na Identity serwerze:

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * Uwierzytelnianie za pomocą dodatkowej `AddIdentityServerJwt` metody pomocnika, która konfiguruje aplikację do weryfikacji tokenów JWT utworzonych przez Identity serwer:

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* Wewnątrz `Startup.Configure` metody:
  * Oprogramowanie pośredniczące uwierzytelniania odpowiedzialne za Weryfikowanie poświadczeń żądania i Ustawianie użytkownika w kontekście żądania:

    ```csharp
    app.UseAuthentication();
    ```

  * IdentityOprogramowanie pośredniczące serwera, które uwidacznia punkty końcowe połączenia OpenID Connect:

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a>AddApiAuthorization

Ta metoda pomocnika konfiguruje Identity serwer tak, aby korzystał z naszej obsługiwanej konfiguracji. IdentitySerwer to zaawansowana i rozszerzalna platforma do obsługi zagadnień związanych z zabezpieczeniami aplikacji. W tym samym czasie, które ujawnia niezbędną złożoność dla najbardziej typowych scenariuszy. W związku z tym zestaw Konwencji i opcji konfiguracji jest dostarczany do użytkownika, który jest uważany za dobry punkt wyjścia. Po zmianie uwierzytelniania należy zapewnić pełną moc Identity serwera, aby dostosować uwierzytelnianie do własnych potrzeb.

### <a name="addno-locidentityserverjwt"></a>Dodaj Identity ServerJwt

Ta metoda pomocnika konfiguruje schemat zasad dla aplikacji jako domyślną procedurę obsługi uwierzytelniania. Zasady są skonfigurowane tak, aby Identity obsługiwać wszystkie żądania kierowane do dowolnej ścieżki podrzędnej w Identity przestrzeni adresów URL "/ Identity ". `JwtBearerHandler`Obsługuje wszystkie inne żądania. Ponadto ta metoda rejestruje `<<ApplicationName>>API` zasób interfejsu API z Identity serwerem z domyślnym zakresem `<<ApplicationName>>API` i konfiguruje oprogramowanie pośredniczące tokenu okaziciela JWT do weryfikacji tokenów wystawionych przez Identity serwer dla aplikacji.

### <a name="weatherforecastcontroller"></a>WeatherForecastController

W pliku *Controllers\WeatherForecastController.cs* Zwróć uwagę na `[Authorize]` atrybut zastosowany do klasy, który wskazuje, że użytkownik musi być autoryzowany na podstawie domyślnych zasad dostępu do zasobu. Domyślne zasady autoryzacji są skonfigurowane tak, aby korzystały z domyślnego schematu uwierzytelniania, który jest skonfigurowany przez `AddIdentityServerJwt` program do schematu zasad, który został wymieniony powyżej, przez `JwtBearerHandler` skonfigurowanie przez tę metodę pomocnika domyślnej procedury obsługi żądań do aplikacji.

### <a name="applicationdbcontext"></a>ApplicationDbContext

W pliku *Data\ApplicationDbContext.cs* Zauważ, `DbContext` że jest on używany w Identity połączeniu z wyjątkiem, który rozszerza `ApiAuthorizationDbContext` (Klasa pochodna z), `IdentityDbContext` Aby uwzględnić schemat dla Identity serwera.

Aby uzyskać pełną kontrolę nad schematem bazy danych, Dziedzicz z jednej z dostępnych Identity `DbContext` klas i skonfiguruj kontekst, aby uwzględnić Identity schemat przez wywołanie `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` `OnModelCreating` metody.

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

W pliku *Controllers\OidcConfigurationController.cs* Zwróć uwagę na punkt końcowy, który jest wstępnie zainicjowany do obsługi parametrów OIDC wymaganych przez klienta.

### <a name="appsettingsjson"></a>appsettings.json

W *appsettings.js* w pliku katalogu głównego projektu znajduje się nowa `IdentityServer` sekcja opisująca listę skonfigurowanych klientów. W poniższym przykładzie istnieje pojedynczy klient. Nazwa klienta odpowiada nazwie aplikacji i jest zamapowana według Konwencji do `ClientId` parametru OAuth. Profil wskazuje konfigurowany typ aplikacji. Jest on używany wewnętrznie w przypadku Konwencji, które upraszczają proces konfiguracji serwera. Istnieje kilka dostępnych profilów, zgodnie z opisem w sekcji [Profile aplikacji](#application-profiles) .

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a>appsettings.Development.jsna

W *appsettings.Development.jsna* pliku katalogu głównego projektu znajduje się sekcja opisująca `IdentityServer` klucz używany do podpisywania tokenów. Podczas wdrażania w środowisku produkcyjnym należy zainicjować i wdrożyć klucz wraz z aplikacją, jak wyjaśniono w sekcji [wdrażanie w środowisku produkcyjnym](#deploy-to-production) .

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a>Ogólny opis aplikacji kątowej

Obsługa uwierzytelniania i autoryzacji interfejsu API w szablonie kątowym znajduje się w jego własnym module skośnym w katalogu *ClientApp\src\api-Authorization* . Moduł składa się z następujących elementów:

* 3 składniki:
  * *login. Component. TS*: obsługuje przepływ logowania aplikacji.
  * *Wyloguj. składnik. TS*: obsługuje przepływ wylogowania aplikacji.
  * *login-menu. składnik. TS*: element widget wyświetlający jeden z następujących zestawów linków:
    * Zarządzanie profilami użytkowników i wylogowywanie łączy podczas uwierzytelniania użytkownika.
    * Rejestrowanie i logowanie w przypadku braku uwierzytelnienia użytkownika.
* Ochrona trasy `AuthorizeGuard` , którą można dodać do tras i wymaga uwierzytelnienia użytkownika przed odwiedzeniem trasy.
* Interceptor protokołu HTTP `AuthorizeInterceptor` , który dołącza token dostępu do wychodzących żądań HTTP przeznaczonych dla interfejsu API podczas uwierzytelniania użytkownika.
* Usługa `AuthorizeService` , która obsługuje szczegóły niższego poziomu procesu uwierzytelniania i ujawnia informacje o uwierzytelnionym użytkowniku w pozostałej części aplikacji do użycia.
* Moduł kątowy, który definiuje trasy skojarzone z częściami uwierzytelniania aplikacji. Przedstawia on składnik menu logowania, Interceptor, ochronę i usługę do użycia w pozostałej części aplikacji.

## <a name="general-description-of-the-react-app"></a>Ogólny opis aplikacji do reagowania

Obsługa uwierzytelniania i autoryzacji interfejsu API w szablonie reagowania znajduje się w katalogu *ClientApp\src\components\api-Authorization* . Składa się z następujących elementów:

* 4 składniki:
  * *Login.js*: obsługuje przepływ logowania aplikacji.
  * *Logout.js*: obsługuje przepływ wylogowania aplikacji.
  * *LoginMenu.js*: element widget wyświetlający jeden z następujących zestawów linków:
    * Zarządzanie profilami użytkowników i wylogowywanie łączy podczas uwierzytelniania użytkownika.
    * Rejestrowanie i logowanie w przypadku braku uwierzytelnienia użytkownika.
  * *AuthorizeRoute.js*: składnik trasy, który wymaga uwierzytelnienia użytkownika przed renderowaniem składnika wskazanego w `Component` parametrze.
* Wyeksportowane `authService` wystąpienie klasy `AuthorizeService` , które obsługuje szczegóły niższego poziomu procesu uwierzytelniania i ujawnia informacje o uwierzytelnionym użytkowniku w pozostałej części aplikacji do użycia.

Teraz, gdy widzisz główne składniki rozwiązania, możesz zapoznać się ze szczegółowymi scenariuszami dotyczącymi aplikacji.

## <a name="require-authorization-on-a-new-api"></a>Wymagaj autoryzacji na nowym interfejsie API

Domyślnie system jest skonfigurowany tak, aby z łatwością wymagał autoryzacji dla nowych interfejsów API. W tym celu Utwórz nowy kontroler i Dodaj `[Authorize]` atrybut do klasy Controller lub do dowolnej akcji w ramach kontrolera.

## <a name="customize-the-api-authentication-handler"></a>Dostosowywanie procedury obsługi uwierzytelniania interfejsu API

Aby dostosować konfigurację procedury obsługi JWT interfejsu API, należy skonfigurować jej <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> wystąpienie:

```csharp
services.AddAuthentication()
    .AddIdentityServerJwt();

services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        ...
    });
```

Procedura obsługi JWT interfejsu API wywołuje zdarzenia, które umożliwiają kontrolę nad procesem uwierzytelniania przy użyciu `JwtBearerEvents` . Aby zapewnić obsługę autoryzacji interfejsu API, `AddIdentityServerJwt` rejestruje własne programy obsługi zdarzeń.

Aby dostosować obsługę zdarzenia, zawiń istniejący program obsługi zdarzeń z dodatkową logiką zgodnie z wymaganiami. Na przykład:

```csharp
services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        var onTokenValidated = options.Events.OnTokenValidated;       
        
        options.Events.OnTokenValidated = async context =>
        {
            await onTokenValidated(context);
            ...
        }
    });
```

W poprzednim kodzie `OnTokenValidated` procedura obsługi zdarzeń jest zastępowana implementacją niestandardową. Ta implementacja:

1. Wywołuje oryginalną implementację dostarczoną przez obsługę autoryzacji interfejsu API.
1. Uruchom własną logikę niestandardową.

## <a name="protect-a-client-side-route-angular"></a>Ochrona trasy po stronie klienta (wartość kątowa)

Ochrona trasy po stronie klienta odbywa się przez dodanie funkcji Autoryzuj ochronę do listy osłon do uruchomienia podczas konfigurowania trasy. Na przykład można zobaczyć, jak `fetch-data` trasa jest skonfigurowana w module kątowym aplikacji głównej:

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

Należy pamiętać, że ochrona trasy nie chroni faktycznego punktu końcowego (co nadal wymaga `[Authorize]` zastosowania atrybutu), ale uniemożliwia użytkownikowi przechodzenie do podanej trasy po stronie klienta, gdy nie jest ona uwierzytelniana.

## <a name="authenticate-api-requests-angular"></a>Uwierzytelnianie żądań interfejsu API (kątowy)

Żądania uwierzytelniające do interfejsów API hostowanych razem z aplikacją są wykonywane automatycznie przy użyciu interceptora klienta HTTP zdefiniowanego przez aplikację.

## <a name="protect-a-client-side-route-react"></a>Ochrona trasy po stronie klienta (reagowanie)

Ochrona trasy po stronie klienta przy użyciu `AuthorizeRoute` składnika zamiast zwykłego `Route` składnika. Na przykład Zwróć uwagę na to, jak `fetch-data` trasa jest skonfigurowana w `App` składniku:

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

Ochrona trasy:

* Nie chroni rzeczywistego punktu końcowego (co nadal wymaga `[Authorize]` zastosowania atrybutu).
* Uniemożliwia użytkownikowi przechodzenie do danej trasy po stronie klienta, gdy nie jest ona uwierzytelniana.

## <a name="authenticate-api-requests-react"></a>Uwierzytelnianie żądań interfejsu API (reagowanie)

Żądania uwierzytelniania przy użyciu reakcji są wykonywane najpierw przez zaimportowanie `authService` wystąpienia z programu `AuthorizeService` . Token dostępu jest pobierany z `authService` i jest dołączany do żądania, jak pokazano poniżej. W przypadku składników reagujących ta czynność jest zwykle wykonywana w `componentDidMount` metodzie cyklu życia lub w wyniku działania niektórych działań użytkownika.

### <a name="import-the-authservice-into-your-component"></a>Zaimportuj authService do składnika

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a>Pobieranie i dołączanie tokenu dostępu do odpowiedzi

```javascript
async populateWeatherData() {
  const token = await authService.getAccessToken();
  const response = await fetch('api/SampleData/WeatherForecasts', {
    headers: !token ? {} : { 'Authorization': `Bearer ${token}` }
  });
  const data = await response.json();
  this.setState({ forecasts: data, loading: false });
}
```

## <a name="deploy-to-production"></a>Wdrażanie w środowisku produkcyjnym

Aby wdrożyć aplikację w środowisku produkcyjnym, należy zainicjować następujące zasoby:

* Baza danych przechowująca Identity konta użytkowników i Identity dotacje serwera.
* Certyfikat produkcyjny do użycia na potrzeby podpisywania tokenów.
  * Nie ma określonych wymagań dotyczących tego certyfikatu; może to być certyfikat z podpisem własnym lub certyfikat obsługiwany przez urząd certyfikacji.
  * Można go wygenerować za poorednictwem standardowych narzędzi, takich jak PowerShell lub OpenSSL.
  * Można go zainstalować w magazynie certyfikatów na maszynach docelowych lub wdrożyć jako plik *PFX* przy użyciu silnego hasła.

### <a name="example-deploy-to-azure-app-service"></a>Przykład: Wdróż do Azure App Service

W tej sekcji opisano wdrażanie aplikacji do Azure App Service przy użyciu certyfikatu przechowywanego w magazynie certyfikatów. Aby zmodyfikować aplikację w celu załadowania certyfikatu z magazynu certyfikatów, podczas konfigurowania aplikacji w Azure Portal w późniejszym kroku wymagany jest plan usługi warstwy Standardowa lub lepszy.

W *appsettings.js* aplikacji w pliku zmodyfikuj `IdentityServer` sekcję, aby uwzględnić szczegóły klucza:

```json
"IdentityServer": {
  "Key": {
    "Type": "Store",
    "StoreName": "My",
    "StoreLocation": "CurrentUser",
    "Name": "CN=MyApplication"
  }
}
```

* Nazwa magazynu reprezentuje nazwę magazynu certyfikatów, w którym przechowywany jest certyfikat. W tym przypadku wskazuje osobisty magazyn użytkownika.
* Lokalizacja magazynu reprezentuje miejsce załadowania certyfikatu ( `CurrentUser` lub `LocalMachine` ).
* Właściwość Name certyfikatu odpowiada podmiotowi wyróżnionemu dla certyfikatu.

Aby wdrożyć program do Azure App Service, wykonaj kroki opisane w artykule [wdrażanie aplikacji na platformie Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure), w którym wyjaśniono, jak utworzyć niezbędne zasoby platformy Azure i wdrożyć aplikację w środowisku produkcyjnym.

Po wykonaniu powyższych instrukcji aplikacja zostanie wdrożona na platformie Azure, ale nie jest jeszcze funkcjonalna. Certyfikat używany w aplikacji musi być skonfigurowany w Azure Portal. Znajdź odcisk palca certyfikatu i postępuj zgodnie z instrukcjami opisanymi w artykule [ładowanie certyfikatów](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).

Chociaż te kroki zawierają informacje o protokole SSL, w Azure Portal znajduje się sekcja **Certyfikaty prywatne** , w której można przekazać certyfikat z obsługą administracyjną do użycia z aplikacją.

Po skonfigurowaniu aplikacji i ustawieniach aplikacji w Azure Portal ponownie uruchom aplikację w portalu.

## <a name="other-configuration-options"></a>Inne opcje konfiguracji

Obsługa autoryzacji interfejsu API jest oparta na Identity serwerze z zestawem Konwencji, wartościami domyślnymi i ulepszeniami, aby uprościć środowisko aplikacji jednostronicowych. Niezbędna jest pełna moc Identity serwera w tle, jeśli integracja ASP.NET Core nie obejmuje Twojego scenariusza. Pomoc techniczna ASP.NET Core jest skoncentrowana na aplikacjach "pierwszej strony", w których wszystkie aplikacje są tworzone i wdrażane przez naszą organizację. W związku z tym pomoc techniczna nie jest oferowana dla elementów, takich jak zgody lub Federacji. W tych scenariuszach Użyj Identity serwera i postępuj zgodnie z dokumentacją.

### <a name="application-profiles"></a>Profile aplikacji

Profile aplikacji są wstępnie zdefiniowanymi konfiguracjami dla aplikacji, które definiują ich parametry. W tej chwili obsługiwane są następujące profile:

* `IdentityServerSPA`: Reprezentuje funkcję SPA hostowaną razem z Identity serwerem jako pojedynczą jednostką.
  * `redirect_uri`Wartość domyślna to `/authentication/login-callback` .
  * `post_logout_redirect_uri`Wartość domyślna to `/authentication/logout-callback` .
  * Zestaw zakresów obejmuje `openid` , `profile` i każdy zakres zdefiniowany dla interfejsów API w aplikacji.
  * Zestaw dozwolonych typów odpowiedzi OIDC jest `id_token token` lub każdy z nich indywidualnie ( `id_token` , `token` ).
  * Dozwolony tryb odpowiedzi to `fragment` .
* `SPA`: Reprezentuje SPA, który nie jest obsługiwany z Identity serwerem.
  * Zestaw zakresów obejmuje `openid` , `profile` i każdy zakres zdefiniowany dla interfejsów API w aplikacji.
  * Zestaw dozwolonych typów odpowiedzi OIDC jest `id_token token` lub każdy z nich indywidualnie ( `id_token` , `token` ).
  * Dozwolony tryb odpowiedzi to `fragment` .
* `IdentityServerJwt`: Reprezentuje interfejs API, który jest hostowany razem z Identity serwerem.
  * Aplikacja jest skonfigurowana tak, aby zawierała pojedynczy zakres, który jest wartością domyślną dla nazwy aplikacji.
* `API`: Reprezentuje interfejs API, który nie jest obsługiwany z Identity serwerem.
  * Aplikacja jest skonfigurowana tak, aby zawierała pojedynczy zakres, który jest wartością domyślną dla nazwy aplikacji.

### <a name="configuration-through-appsettings"></a>Konfiguracja za poorednictwem AppSettings

Skonfiguruj aplikacje za pomocą systemu konfiguracji, dodając je do listy `Clients` lub `Resources` .

Skonfiguruj poszczególne klienta `redirect_uri` i `post_logout_redirect_uri` właściwości, jak pokazano w następującym przykładzie:

```json
"IdentityServer": {
  "Clients": {
    "MySPA": {
      "Profile": "SPA",
      "RedirectUri": "https://www.example.com/authentication/login-callback",
      "LogoutUri": "https://www.example.com/authentication/logout-callback"
    }
  }
}
```

Podczas konfigurowania zasobów można skonfigurować zakresy dla zasobu, jak pokazano poniżej:

```json
"IdentityServer": {
  "Resources": {
    "MyExternalApi": {
      "Profile": "API",
      "Scopes": "a b c"
    }
  }
}
```

### <a name="configuration-through-code"></a>Konfiguracja przy użyciu kodu

Klientów i zasoby można również skonfigurować za pomocą kodu przy użyciu przeciążenia `AddApiAuthorization` , które wykonuje akcję konfigurowania opcji.

```csharp
AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options =>
{
    options.Clients.AddSPA(
        "My SPA", spa =>
        spa.WithRedirectUri("http://www.example.com/authentication/login-callback")
           .WithLogoutRedirectUri(
               "http://www.example.com/authentication/logout-callback"));

    options.ApiResources.AddApiResource("MyExternalApi", resource =>
        resource.WithScopes("a", "b", "c"));
});
```

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:spa/angular>
* <xref:spa/react>
* <xref:security/authentication/scaffold-identity>
