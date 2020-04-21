---
title: Bezpieczne ASP.NET Core Blazor WebAssembly
author: guardrex
description: Dowiedz się, Blazor jak zabezpieczyć aplikacje WebAssemlby jako aplikacje jednostronicowe (OSO).
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/19/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/index
ms.openlocfilehash: b82341f3d1d0665d4ee31bb6d967ccf305bae9c3
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661783"
---
# <a name="secure-aspnet-core-opno-locblazor-webassembly"></a>Bezpieczne ASP.NET Core Blazor WebAssembly

Przez [Javier Calvarro Nelson](https://github.com/javiercn)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

BlazorAplikacje WebAssembly są zabezpieczone w taki sam sposób jak aplikacje jednostronicowe (OSO). Istnieje kilka podejść do uwierzytelniania użytkowników do OSO, ale najbardziej powszechne i kompleksowe podejście jest użycie implementacji opartej na [protokole OAuth 2.0](https://oauth.net/), takich jak [Open ID Connect (OIDC)](https://openid.net/connect/).

## <a name="authentication-library"></a>Biblioteka uwierzytelniania

BlazorWebAssembly obsługuje uwierzytelnianie i autoryzowanie aplikacji `Microsoft.AspNetCore.Components.WebAssembly.Authentication` za pomocą OIDC za pośrednictwem biblioteki. Biblioteka zawiera zestaw podstawowych do bezproblemowego uwierzytelniania względem zaplecza ASP.NET Core. Biblioteka integruje ASP.NET Podstawową tożsamość z obsługą autoryzacji interfejsu API opartą na [serwerze tożsamości](https://identityserver.io/). Biblioteka może uwierzytelniać się względem dowolnego dostawcy tożsamości (IP) innej firmy, który obsługuje oidc, które są nazywane OpenID Providers (OP).

Obsługa uwierzytelniania Blazor w webassembly jest zbudowany na górze *biblioteki oidc-client.js,* który jest używany do obsługi szczegółów protokołu uwierzytelniania podstawowego.

Istnieją inne opcje uwierzytelniania OSO, takie jak korzystanie z plików cookie SameSite. Jednak projekt inżynieryjny Blazor WebAssembly jest rozliczany na OAuth i OIDC jako najlepszą opcję uwierzytelniania w Blazor aplikacjach WebAssembly. [Uwierzytelnianie oparte](xref:security/anti-request-forgery#token-based-authentication) na tokenach oparte na [tokenach JSON Web Tokens (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) zostało wybrane za pomocą [uwierzytelniania opartego na plikach cookie](xref:security/anti-request-forgery#cookie-based-authentication) ze względów funkcjonalnych i bezpieczeństwa:

* Za pomocą protokołu opartego na tokenie oferuje mniejszą powierzchnię ataku, jak tokeny nie są wysyłane we wszystkich żądaniach.
* Punkty końcowe serwera nie wymagają ochrony przed [fałszersko żądania między witrynami (CSRF),](xref:security/anti-request-forgery) ponieważ tokeny są wysyłane jawnie. Dzięki temu można Blazor hostować aplikacje WebAssembly obok aplikacji MVC lub Razor stron.
* Tokeny mają węższe uprawnienia niż pliki cookie. Na przykład tokeny nie mogą być używane do zarządzania kontem użytkownika lub zmiany hasła użytkownika, chyba że taka funkcja jest jawnie zaimplementowana.
* Tokeny mają krótki okres istnienia, domyślnie jedną godzinę, co ogranicza okno ataku. Tokeny można również odwołać w dowolnym momencie.
* Samodzielne JWTs oferują gwarancje dla klienta i serwera dotyczące procesu uwierzytelniania. Na przykład klient ma środki do wykrywania i sprawdzania poprawności, że tokeny, które otrzymuje są zgodne z prawem i zostały wyemitowane w ramach danego procesu uwierzytelniania. Jeśli strona trzecia próbuje przełączyć token w środku procesu uwierzytelniania, klient może wykryć włączony token i uniknąć używania go.
* Tokeny z OAuth i OIDC nie opierają się na agenta użytkownika zachowuje się poprawnie, aby upewnić się, że aplikacja jest bezpieczna.
* Protokoły oparte na tokenach, takie jak OAuth i OIDC, umożliwiają uwierzytelnianie i autoryzowanie hostowanych i autonomicznych aplikacji z tym samym zestawem właściwości zabezpieczeń.

## <a name="authentication-process-with-oidc"></a>Proces uwierzytelniania za pomocą OIDC

Biblioteka `Microsoft.AspNetCore.Components.WebAssembly.Authentication` oferuje kilka ujednolicenia do implementacji uwierzytelniania i autoryzacji przy użyciu OIDC. Ogólnie rzecz biorąc, uwierzytelnianie działa w następujący sposób:

* Gdy użytkownik anonimowy wybierze przycisk logowania lub [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) zażąda strony z zastosowanym atrybutem, użytkownik zostanie`/authentication/login`przekierowany na stronę logowania aplikacji ( ).
* Na stronie logowania biblioteka uwierzytelniania przygotowuje się do przekierowania do punktu końcowego autoryzacji. Punkt końcowy autoryzacji znajduje Blazor się poza aplikacją WebAssembly i może być obsługiwany w osobnym źródle pochodzenia. Punkt końcowy jest odpowiedzialny za określenie, czy użytkownik jest uwierzytelniony i do wystawiania jednego lub więcej tokenów w odpowiedzi. Biblioteka uwierzytelniania udostępnia wywołanie zwrotne logowania, aby otrzymać odpowiedź uwierzytelniania.
  * Jeśli użytkownik nie jest uwierzytelniony, użytkownik jest przekierowywał do podstawowego systemu uwierzytelniania, który jest zwykle ASP.NET tożsamości podstawowej.
  * Jeśli użytkownik został już uwierzytelniony, punkt końcowy autoryzacji generuje odpowiednie tokeny i przekierowuje`/authentication/login-callback`przeglądarkę z powrotem do punktu końcowego wywołania zwrotnego logowania ( ).
* Gdy Blazor aplikacja WebAssembly ładuje punkt końcowy`/authentication/login-callback`wywołania zwrotnego logowania ( ), odpowiedź uwierzytelniania jest przetwarzana.
  * Jeśli proces uwierzytelniania zakończy się pomyślnie, użytkownik jest uwierzytelniany i opcjonalnie odesłany do oryginalnego chronionego adresu URL, o który poprosił użytkownik.
  * Jeśli proces uwierzytelniania nie powiedzie się z jakiegokolwiek powodu,`/authentication/login-failed`użytkownik jest wysyłany do strony logowania nie powiodło się ( ), a błąd jest wyświetlany.

## <a name="support-prerendering-with-authentication"></a>Obsługa prerenderingu za pomocą uwierzytelniania

Po zastosowaniu się do wskazówek Blazor w jednym z hostowanych tematów aplikacji WebAssembly, użyj następujących instrukcji, aby utworzyć aplikację, która:

* Ścieżki prerenders, dla których autoryzacja nie jest wymagana.
* Nie jest prerender ścieżki, dla których wymagana jest autoryzacja.

W `Program` klasie aplikacji klienta (*Program.cs*), współczynnik rejestracji wspólnej usługi w osobnej `ConfigureCommonServices`metodzie (na przykład):

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.RootComponents.Add<App>("app");

        builder.Services.AddSingleton(new HttpClient 
        {
            BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
        });

        services.Add...;

        ConfigureCommonServices(builder.Services);

        await builder.Build().RunAsync();
    }

    public static void ConfigureCommonServices(IServiceCollection services)
    {
        // Common service registrations
    }
}
```

W aplikacji `Startup.ConfigureServices`Serwer zarejestruj następujące usługi dodatkowe:

```csharp
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Server;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddRazorPages();
    services.AddScoped<AuthenticationStateProvider, ServerAuthenticationStateProvider>();
    services.AddScoped<SignOutSessionStateManager>();

    Client.Program.ConfigureCommonServices(services);
}
```

W `Startup.Configure` metodzie aplikacji Serwer `endpoints.MapFallbackToFile("index.html")` zamień na `endpoints.MapFallbackToPage("/_Host")`:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

W aplikacji Serwer utwórz folder *Strony,* jeśli nie istnieje. Utwórz stronę *_Host.cshtml* w folderze *Strony aplikacji* Serwer. Wklej zawartość z pliku *wwwroot/index.html* aplikacji klienta do pliku *Pages/_Host.cshtml.* Zaktualizuj zawartość pliku:

* Dodaj `@page "_Host"` do górnej części pliku.
* Zastąp `<app>Loading...</app>` znacznik następującym:

  ```cshtml
  <app>
      @if (!HttpContext.Request.Path.StartsWithSegments("/authentication"))
      {
          <component type="typeof(Wasm.Authentication.Client.App)" render-mode="Static" />
      }
      else
      {
          <text>Loading...</text>
      }
  </app>
  ```
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a>Opcje dla hostowanych aplikacji i dostawców logowania innych firm

Podczas uwierzytelniania i autoryzowania Blazor hosta aplikacji WebAssembly z dostawcą innej firmy dostępnych jest kilka opcji uwierzytelniania użytkownika. Który z nich wybierzesz zależy od scenariusza.

Aby uzyskać więcej informacji, zobacz <xref:security/authentication/social/additional-claims>.

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a>Uwierzytelnij użytkowników, aby wywoływać tylko chronione interfejsy API innych firm

Uwierzytelnij użytkownika przy użyciu przepływu OAuth po stronie klienta względem zewnętrznego dostawcy interfejsu API:

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 W tym scenariuszu:

* Serwer obsługujący aplikację nie odgrywa roli.
* Nie można chronić interfejsów API na serwerze.
* Aplikacja może wywoływać tylko chronione interfejsy API innych firm.

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a>Uwierzytelnij użytkowników za pomocą zewnętrznego dostawcy i wywołaj chronione interfejsy API na serwerze hosta i u innej firmy

Skonfiguruj tożsamość za pomocą zewnętrznego dostawcy logowania. Uzyskaj tokeny wymagane do dostępu do interfejsu API innych firm i przechowuj je.

Gdy użytkownik się loguje, Identity zbiera tokeny dostępu i odświeżania w ramach procesu uwierzytelniania. W tym momencie istnieje kilka metod dostępnych do wykonywania wywołań interfejsu API do interfejsów API innych firm.

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a>Pobieranie tokenu dostępu innej firmy za pomocą tokenu dostępu do serwera

Użyj tokenu dostępu wygenerowanego na serwerze, aby pobrać token dostępu innej firmy z punktu końcowego interfejsu API serwera. Stamtąd użyj tokenu dostępu innej firmy, aby wywołać zasoby interfejsu API innych firm bezpośrednio z tożsamości na kliencie.

Nie zalecamy takiego podejścia. Takie podejście wymaga traktowania tokenu dostępu innej firmy tak, jakby zostały wygenerowane dla klienta publicznego. W terminach OAuth publiczna aplikacja nie ma klucza tajnego klienta, ponieważ nie można ufać, aby bezpiecznie przechowywać wpisy tajne, a token dostępu jest produkowany dla poufnego klienta. Klient poufny jest klientem, który ma klucz tajny klienta i zakłada się, że może bezpiecznie przechowywać wpisy tajne.

* Token dostępu innej firmy może otrzymać dodatkowe zakresy do wykonywania poufnych operacji na podstawie faktu, że innej firmy emitowane tokenu dla bardziej zaufanego klienta.
* Podobnie tokeny odświeżania nie powinny być wystawiane klientowi, który nie jest zaufany, ponieważ w ten sposób daje klientowi nieograniczony dostęp, chyba że zostaną wprowadzone inne ograniczenia.

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a>Wykonuj wywołania interfejsu API od klienta do interfejsu API serwera w celu wywołania interfejsów API innych firm

Nawład wywołanie interfejsu API od klienta do interfejsu API serwera. Z serwera pobierz token dostępu dla zasobu interfejsu API innej firmy i wydaj cokolwiek wywołania jest konieczne.

Takie podejście wymaga dodatkowego przeskoku sieciowego za pośrednictwem serwera, aby wywołać interfejs API innej firmy, ostatecznie powoduje to bezpieczniejsze środowisko:

* Serwer może przechowywać tokeny odświeżania i upewnić się, że aplikacja nie utraci dostępu do zasobów innych firm.
* Aplikacja nie może wyciekać tokenów dostępu z serwera, które mogą zawierać bardziej poufne uprawnienia.
