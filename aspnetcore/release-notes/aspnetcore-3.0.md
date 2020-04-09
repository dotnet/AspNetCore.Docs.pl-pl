---
title: Co nowego w ASP.NET Core 3.0
author: rick-anderson
description: Dowiedz się więcej o nowych funkcjach w ASP.NET Core 3.0.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- SignalR
uid: aspnetcore-3.0
ms.openlocfilehash: 4886673a9b16b8be8d9a0b0d5c7002a91760544e
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80976979"
---
# <a name="whats-new-in-aspnet-core-30"></a>Co nowego w ASP.NET Core 3.0

W tym artykule przedstawiono najważniejsze zmiany w ASP.NET Core 3.0 z łączami do odpowiedniej dokumentacji.

## Blazor

Blazorto nowa struktura w ASP.NET Core do tworzenia interaktywnego interfejsu użytkownika sieci web po stronie klienta za pomocą platformy .NET:

* Tworzenie rozbudowanych interaktywnych interfejsów użytkownika przy użyciu języka C# zamiast javascript.
* Udostępnianie logiki aplikacji po stronie serwera i po stronie klienta napisanej w programie .NET.
* Renderuj interfejs użytkownika jako HTML i CSS, aby uzyskać szeroką obsługę przeglądarki, w tym przeglądarek mobilnych.

Blazorscenariuszy obsługiwanych przez ramy prawne:

* Komponenty interfejsu użytkownika wielokrotnego rozrządu wielokrotnego rozrządu (komponenty razor)
* Routing po stronie klienta
* Układy komponentów
* Obsługa iniekcji zależności
* Formularze i walidacja
* Tworzenie bibliotek składników za pomocą bibliotek klas Razor
* Międzyoperacyjność w języku JavaScript

Aby uzyskać więcej informacji, zobacz <xref:blazor/index>.

### <a name="opno-locblazor-server"></a>BlazorSerwera

Blazoroddziela logikę renderowania składników od sposobu stosowania aktualizacji interfejsu użytkownika. BlazorSerwer zapewnia obsługę hostowania składników Razor na serwerze w aplikacji ASP.NET Core. Aktualizacje interfejsu użytkownika są SignalR obsługiwane przez połączenie. BlazorSerwer jest obsługiwany w ASP.NET Core 3.0.

### <a name="opno-locblazor-webassembly-preview"></a>BlazorWebAssembly (wersja zapoznawcza)

Blazoraplikacje można również uruchamiać bezpośrednio w przeglądarce przy użyciu środowiska uruchomieniowego .NET opartego na webassembly. BlazorWebAssembly jest w wersji zapoznawczej i *nie* jest obsługiwany w ASP.NET Core 3.0. BlazorWebAssembly będzie obsługiwany w przyszłej wersji ASP.NET Core.

### <a name="razor-components"></a>Elementy maszynki do golenia

Blazoraplikacje są zbudowane z komponentów. Składniki są samodzielnymi fragmentami interfejsu użytkownika ,, takimi jak strona, okno dialogowe lub formularz. Składniki są normalne klasy .NET, które definiują logikę renderowania interfejsu użytkownika i obsługi zdarzeń po stronie klienta. Możesz tworzyć bogate interaktywne aplikacje internetowe bez javascriptu.

Składniki Blazor są zazwyczaj tworzone przy użyciu składni Razor, naturalne połączenie HTML i C#. Komponenty brzytwy są podobne do stron Razor i widoków MVC, ponieważ obie używają Razor. W przeciwieństwie do stron i widoków, które są oparte na modelu żądanie odpowiedź, składniki są używane specjalnie do obsługi kompozycji interfejsu użytkownika.

## <a name="grpc"></a>gRPC

[gRPC](https://grpc.io/):

* Jest popularną, wysokowydajną strukturą RPC (zdalne wywołanie procedury).
* Oferuje opinionated umowy pierwszy podejście do tworzenia interfejsu API.
* Wykorzystuje nowoczesne technologie, takie jak:

  * HTTP/2 dla transportu.
  * Bufory protokołu jako język opisu interfejsu.
  * Binarny format serializacji.
* Udostępnia funkcje, takie jak:

  * Authentication
  * Dwukierunkowe przesyłanie strumieniowe i sterowanie przepływem.
  * Anulowanie i limity czasu.

Funkcja gRPC w ASP.NET Core 3.0 obejmuje:

* [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) &ndash; ASP.NET podstawową strukturą do hostingu usług gRPC. GRPC na ASP.NET Core integruje się ze standardowymi ASP.NET podstawowe funkcje, takie jak rejestrowanie, iniekcja zależności (DI), uwierzytelnianie i autoryzacja.
* [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) &ndash; Klient gRPC dla .NET Core, `HttpClient`który opiera się na znanych . .
* [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) &ndash; gRPC integracji klienta z `HttpClientFactory`.

Aby uzyskać więcej informacji, zobacz <xref:grpc/index>.

## SignalR

Zobacz [ SignalR Aktualizowanie kodu,](xref:migration/22-to-30#signalr) aby uzyskać instrukcje dotyczące migracji. SignalRteraz używa `System.Text.Json` do serializacji/deserializacji komunikatów JSON. Zobacz [Switch do Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson) instrukcje `Newtonsoft.Json`przywracania serializatora opartego na.

W językach JavaScript i SignalR.NET Klienci dla , dodano obsługę automatycznego ponownego połączenia. Domyślnie klient próbuje natychmiast ponownie połączyć się i ponowić próbę po 2, 10 i 30 sekund, jeśli to konieczne. Jeśli klient pomyślnie połączy się ponownie, otrzyma nowy identyfikator połączenia. Automatyczne ponowne łączenie jest zgłodne:

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

Interwały ponownego połączenia można określić, przekazując tablicę milowych czasów trwania:

```javascript
.withAutomaticReconnect([0, 3000, 5000, 10000, 15000, 30000])
//.withAutomaticReconnect([0, 2000, 10000, 30000]) The default intervals.
```

Implementacja niestandardowa może być przekazywana w celu pełnej kontroli interwałów ponownego połączenia.

Jeśli ponowne połączenie nie powiedzie się po ostatnim interwale ponownego nawiązania połączenia:

* Klient uważa, że połączenie jest w trybie offline.
* Klient przestaje próbować ponownie nawiązać połączenie.

Podczas prób ponownego połączenia zaktualizuj interfejs użytkownika aplikacji, aby powiadomić użytkownika o próbie ponownego połączenia.

Aby zapewnić opinie interfejsu użytkownika po przerwaniu połączenia, interfejs API SignalR klienta został rozwinięty w celu uwzględnienia następujących programów obsługi zdarzeń:

* `onreconnecting`: Daje deweloperom możliwość wyłączenia interfejsu użytkownika lub poinformowania użytkowników, że aplikacja jest w trybie offline.
* `onreconnected`: Daje deweloperom możliwość aktualizacji interfejsu użytkownika po przywróceniu połączenia.

Następujący kod używa `onreconnecting` do aktualizacji interfejsu użytkownika podczas próby połączenia:

```javascript
connection.onreconnecting((error) => {
    const status = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messageInput").disabled = true;
    document.getElementById("sendButton").disabled = true;
    document.getElementById("connectionStatus").innerText = status;
});
```

Następujący kod służy `onreconnected` do aktualizowania interfejsu użytkownika w połączeniu:

```javascript
connection.onreconnected((connectionId) => {
    const status = `Connection reestablished. Connected.`;
    document.getElementById("messageInput").disabled = false;
    document.getElementById("sendButton").disabled = false;
    document.getElementById("connectionStatus").innerText = status;
});
```

SignalR3.0 i nowsze udostępnia niestandardowy zasób do obsługi autoryzacji, gdy metoda koncentratora wymaga autoryzacji. Zasób jest `HubInvocationContext`wystąpieniem pliku . Obejmuje `HubInvocationContext` ona:

* `HubCallerContext`
* Nazwa wywoływanej metody koncentratora.
* Argumenty do metody koncentratora.

Rozważmy poniższy przykład aplikacji pokoju rozmów umożliwiającej logowanie się do wielu organizacji za pośrednictwem usługi Azure Active Directory. Każda osoba posiadająca konto Microsoft może zalogować się na czacie, ale tylko członkowie organizacji stanowiącej właścicielki mogą zablokować użytkowników lub wyświetlić historie czatów użytkowników. Aplikacja może ograniczyć niektóre funkcje od określonych użytkowników.

```csharp
public class DomainRestrictedRequirement :
    AuthorizationHandler<DomainRestrictedRequirement, HubInvocationContext>,
    IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
        DomainRestrictedRequirement requirement,
        HubInvocationContext resource)
    {
        if (context.User?.Identity?.Name == null)
        {
            return Task.CompletedTask;
        }

        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.Identity.Name))
        {
            context.Succeed(requirement);
        }

        return Task.CompletedTask;
    }

    private bool IsUserAllowedToDoThis(string hubMethodName, string currentUsername)
    {
        if (hubMethodName.Equals("banUser", StringComparison.OrdinalIgnoreCase))
        {
            return currentUsername.Equals("bob42@jabbr.net", StringComparison.OrdinalIgnoreCase);
        }

        return currentUsername.EndsWith("@jabbr.net", StringComparison.OrdinalIgnoreCase));
    }
}
```

W poprzednim kodzie `DomainRestrictedRequirement` służy jako `IAuthorizationRequirement`niestandardowy . Ponieważ `HubInvocationContext` parametr zasobu jest przekazywany, logika wewnętrzna może:

* Sprawdź kontekst, w którym jest wywoływana hub.
* Podejmuj decyzje dotyczące zezwalania użytkownikowi na wykonywanie poszczególnych metod centrum.

Poszczególne metody centrum mogą być oznaczone nazwą zasad sprawdza kod w czasie wykonywania. Gdy klienci próbują wywołać poszczególne `DomainRestrictedRequirement` metody centrum, program obsługi uruchamia i kontroluje dostęp do metod. W zależności od `DomainRestrictedRequirement` sposobu, w jaki kontroluje dostęp:

* Wszyscy zalogowani użytkownicy mogą `SendMessage` wywołać tę metodę.
* Tylko użytkownicy, którzy zalogowali się przy za pomocą adresu `@jabbr.net` e-mail, mogą wyświetlać historie użytkowników.
* Tylko `bob42@jabbr.net` może zablokować użytkowników z pokoju rozmów.

```csharp
[Authorize]
public class ChatHub : Hub
{
    public void SendMessage(string message)
    {
    }

    [Authorize("DomainRestricted")]
    public void BanUser(string username)
    {
    }

    [Authorize("DomainRestricted")]
    public void ViewUserHistory(string username)
    {
    }
}
```

Tworzenie `DomainRestricted` zasad może obejmować:

* W *Startup.cs*, dodając nową politykę.
* Podaj `DomainRestrictedRequirement` wymagania niestandardowe jako parametr.
* Rejestracja `DomainRestricted` w oprogramowaniu pośredniczącym autoryzacji.

```csharp
services
    .AddAuthorization(options =>
    {
        options.AddPolicy("DomainRestricted", policy =>
        {
            policy.Requirements.Add(new DomainRestrictedRequirement());
        });
    });
```

SignalRkoncentratorów korzysta z [usługi Endpoint Routing](xref:fundamentals/routing). SignalRpołączenie koncentratora zostało wcześniej wykonane jawnie:

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});
```

W poprzedniej wersji deweloperzy musieli podłączyć kontrolery, strony Razor i koncentratory w różnych miejscach. Jawne połączenie powoduje serię niemal identycznych segmentów routingu:

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});

app.UseRouting(routes =>
{
    routes.MapRazorPages();
});
```

SignalRKoncentratory 3.0 mogą być kierowane za pośrednictwem routingu punktu końcowego. W celu prowadzenia routingu w punktach końcowych `UseRouting`zazwyczaj wszystkie routing można skonfigurować w :

```csharp
app.UseRouting(routes =>
{
    routes.MapRazorPages();
    routes.MapHub<ChatHub>("hubs/chat");
});
```

ASP.NET Core 3.0 SignalR dodał:

Przesyłanie strumieniowe między klientami. W przypadku przesyłania strumieniowego między klientami metody po stronie `IAsyncEnumerable<T>` serwera `ChannelReader<T>`mogą przyjmować wystąpienia typu "od klienta do serwera" lub . W poniższym przykładzie `UploadStream` języka C# metoda w centrum otrzyma strumień ciągów od klienta:

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        // process content
    }
}
```

Aplikacje klienckie platformy `IAsyncEnumerable<T>` `ChannelReader<T>` .NET `stream` mogą przekazać `UploadStream` wystąpienie lub wystąpienie jako argument metody Hub powyżej.

Po `for` zakończeniu pętli i zamknięciu funkcji lokalnej zostanie wysłana zakończenie strumienia:

```csharp
async IAsyncEnumerable<string> clientStreamData()
{
    for (var i = 0; i < 5; i++)
    {
        var data = await FetchSomeData();
        yield return data;
    }
}

await connection.SendAsync("UploadStream", clientStreamData());
```

Aplikacje klienckie JavaScript SignalR `Subject` używają (lub [RxJS Subject)](https://rxjs.dev/api/index/class/Subject)dla `stream` argumentu `UploadStream` hub metody powyżej.

```javascript
let subject = new signalR.Subject();
await connection.send("StartStream", "MyAsciiArtStream", subject);
```

Kod JavaScript może `subject.next` używać metody do obsługi ciągów, ponieważ są one przechwytywane i gotowe do wysłania do serwera.

```javascript
subject.next("example");
subject.complete();
```

Za pomocą kodu, podobnie jak dwa poprzednie fragmenty kodu, można utworzyć środowisko przesyłania strumieniowego w czasie rzeczywistym.

## <a name="new-json-serialization"></a>Nowa serializacja JSON

ASP.NET Core 3.0 używa <xref:System.Text.Json> teraz domyślnie serializacji JSON:

* Odczytuje i zapisuje JSON asynchronicznie.
* Jest zoptymalizowany pod kątem tekstu UTF-8.
* Zazwyczaj wyższa wydajność `Newtonsoft.Json`niż .

Aby dodać Json.NET do ASP.NET Core 3.0, zobacz [Dodawanie obsługi formatu JSON opartego na newtonsoft.Json](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).

## <a name="new-razor-directives"></a>Nowe dyrektywy razor

Poniższa lista zawiera nowe dyrektywy Razor:

* [`@attribute`](xref:mvc/views/razor#attribute)&ndash; Dyrektywa `@attribute` stosuje dany atrybut do klasy wygenerowanej strony lub widoku. Na przykład `@attribute [Authorize]`.
* [`@implements`](xref:mvc/views/razor#implements)&ndash; Dyrektywa `@implements` implementuje interfejs dla wygenerowanej klasy. Na przykład `@implements IDisposable`.

## <a name="identityserver4-supports-authentication-and-authorization-for-web-apis-and-spas"></a>IdentityServer4 obsługuje uwierzytelnianie i autoryzację interfejsów API sieci Web i oso

ASP.NET Core 3.0 oferuje uwierzytelnianie w aplikacjach jednostronicowych (SPA) przy użyciu obsługi autoryzacji interfejsu API sieci Web. ASP.NET podstawową tożsamość do uwierzytelniania i przechowywania użytkowników jest połączona z [IdentityServer4](https://identityserver.io/) w celu wdrożenia Open ID Connect.

IdentityServer4 to rama OpenID Connect i OAuth 2.0 dla ASP.NET Core 3.0. Umożliwia następujące funkcje zabezpieczeń:

* Uwierzytelnianie jako usługa (AaaS)
* Logowanie jednokrotne (Logowanie jednokrotne) w wielu typach aplikacji
* Kontrola dostępu dla interfejsów API
* Brama federacji

Aby uzyskać więcej informacji, zobacz [dokumentację IdentityServer4](http://docs.identityserver.io/en/latest/index.html) lub [Uwierzytelnianie i autoryzacja dla OSO](xref:security/authentication/identity/spa).

## <a name="certificate-and-kerberos-authentication"></a>Uwierzytelnianie certyfikatu i protokołu Kerberos

Uwierzytelnianie certyfikatu wymaga:

* Konfigurowanie serwera do akceptowania certyfikatów.
* Dodawanie oprogramowania pośredniczącego uwierzytelniania w `Startup.Configure`pliku .
* Dodawanie usługi uwierzytelniania `Startup.ConfigureServices`certyfikatów w pliku .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
            .AddCertificate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

Opcje uwierzytelniania certyfikatów obejmują możliwość:

* Akceptowanie certyfikatów z podpisem własnym.
* Sprawdź, czy nie ma odwołania certyfikatu.
* Sprawdź, czy certyfikat oferty jest w nim odpowiedni.

Domyślny podmiot zabezpieczeń użytkownika jest konstruowany z właściwości certyfikatu. Podmiot zabezpieczeń użytkownika zawiera zdarzenie, które umożliwia uzupełnienie lub zastąpienie podmiotu. Aby uzyskać więcej informacji, zobacz <xref:security/authentication/certauth>.

[Uwierzytelnianie systemu Windows](/windows-server/security/windows-authentication/windows-authentication-overview) zostało rozszerzone na systemy Linux i macOS. W poprzednich wersjach uwierzytelnianie systemu Windows było ograniczone do [iIS](xref:host-and-deploy/iis/index) i [HttpSys](xref:fundamentals/servers/httpsys). W ASP.NET Core 3.0 [Kestrel](xref:fundamentals/servers/kestrel) ma możliwość używania hostów Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview)i [NTLM w systemach Windows,](/windows-server/security/kerberos/ntlm-overview)Linux i macOS dla hostów przyłączonych do domeny systemu Windows. Obsługa pustułki tych schematów uwierzytelniania jest dostarczana przez pakiet [Microsoft.AspNetCore.Authentication.Negotiate NuGet.](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) Podobnie jak w przypadku innych usług uwierzytelniania, skonfiguruj aplikację uwierzytelniającą w szerokim zakresie, a następnie skonfiguruj usługę:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
        .AddNegotiate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

Wymagania dotyczące hosta:

* Hosty systemu Windows muszą mieć dodane [nazwy główne usług](/windows/win32/ad/service-principal-names) (SPN) do konta użytkownika obsługującego aplikację.
* Do domeny należy dołączyć komputery z systemem Linux i macOS.
  * SPN muszą być tworzone dla procesu sieci web.
  * [Pliki keytab](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) muszą być generowane i konfigurowane na komputerze-hoście.

Aby uzyskać więcej informacji, zobacz <xref:security/authentication/windowsauth>.

## <a name="template-changes"></a>Zmiany szablonu

Szablony interfejsu użytkownika sieci Web (Razor Pages, MVC z kontrolerem i widokami) zostały usunięte:

* Interfejs użytkownika zgody na pliki cookie nie jest już uwzględniony. Aby włączyć funkcję zgody na pliki cookie w aplikacji wygenerowanej przez <xref:security/gdpr>szablon core 3.0 ASP.NET Core, zobacz .
* Skrypty i powiązane zasoby statyczne są teraz odwoływane jako pliki lokalne zamiast używania sieci CDN. Aby uzyskać więcej informacji, zobacz [Skrypty i powiązane zasoby statyczne są teraz odwoływane jako pliki lokalne zamiast używania sieci CDN opartych na bieżącym środowisku (aspnet/AspNetCore.Docs #14350)](https://github.com/dotnet/AspNetCore.Docs/issues/14350).

Szablon kątowy zaktualizowany do używania angular 8.

Szablon biblioteki klas Razor (RCL) domyślnie domyślnie jest owy do tworzenia komponentów Razor. Nowa opcja szablonu w programie Visual Studio zapewnia obsługę szablonów dla stron i widoków. Podczas tworzenia listy RCL z szablonu w `--support-pages-and-views` powłoce poleceń należy przekazać tę opcję (`dotnet new razorclasslib --support-pages-and-views`).

## <a name="generic-host"></a>Host ogólny

Używane są szablony ASP.NET Core 3.0 <xref:fundamentals/host/generic-host>. Poprzednie wersje <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>używane . Korzystanie z hosta .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) zapewnia lepszą integrację aplikacji ASP.NET Core z innymi scenariuszami serwera, które nie są specyficzne dla sieci Web. Aby uzyskać więcej informacji, zobacz [HostBuilder zastępuje WebHostBuilder](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).

### <a name="host-configuration"></a>Konfiguracja hosta

Przed wydaniem ASP.NET Core 3.0, zmienne środowiskowe poprzedzone `ASPNETCORE_` zostały załadowane do konfiguracji hosta hosta sieci Web Host. W 3.0, `AddEnvironmentVariables` służy do ładowania zmiennych `DOTNET_` środowiskowych poprzedzonych do konfiguracji hosta z `CreateDefaultBuilder`.

### <a name="changes-to-startup-constructor-injection"></a>Zmiany w iniekcji konstruktora uruchamiania

Host ogólny obsługuje tylko następujące `Startup` typy iniekcji konstruktora:

* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

Wszystkie usługi mogą być wstrzykiwane bezpośrednio `Startup.Configure` jako argumenty do metody. Aby uzyskać więcej informacji, zobacz [Generic Host ogranicza iniekcję konstruktora uruchamiania (aspnet/Anonse #353)](https://github.com/aspnet/Announcements/issues/353).

## <a name="kestrel"></a>Kestrel

* Konfiguracja pustułki została zaktualizowana w celu migracji do hosta ogólnego. W 3.0 Kestrel jest skonfigurowany na konstruktorze hosta internetowego dostarczonego przez `ConfigureWebHostDefaults`.
* Karty połączeń zostały usunięte z Kestrel i zastąpione oprogramowaniem pośredniczącym połączeń, które jest podobne do oprogramowania pośredniczącego HTTP w potoku ASP.NET Core, ale dla połączeń niższego poziomu.
* Warstwa transportowa Kestrel została ujawniona `Connections.Abstractions`jako interfejs publiczny w .
* Niejednoznaczność między nagłówkami i przyczepami została rozwiązana przez przeniesienie nagłówków końcowych do nowej kolekcji.
* Synchroniczne interfejsy API we/wy, takie jak `HttpRequest.Body.Read`, są częstym źródłem głodu wątku prowadzącego do awarii aplikacji. W 3.0 `AllowSynchronousIO` jest domyślnie wyłączona.

Aby uzyskać więcej informacji, zobacz <xref:migration/22-to-30#kestrel>.

## <a name="http2-enabled-by-default"></a>Http/2 domyślnie włączony

Protokół HTTP/2 jest domyślnie włączony w kestrel dla punktów końcowych HTTPS. Obsługa protokołu HTTP/2 dla systemów IIS lub HTTP.sys jest włączona, gdy jest obsługiwana przez system operacyjny.

## <a name="eventcounters-on-request"></a>EventCounters na życzenie

Hosting EventSource, `Microsoft.AspNetCore.Hosting`emituje następujące <xref:System.Diagnostics.Tracing.EventCounter> nowe typy związane z przychodzące żądania:

* `requests-per-second`
* `total-requests`
* `current-requests`
* `failed-requests`

## <a name="endpoint-routing"></a>Routing punktu końcowego

Routing punktu końcowego, który umożliwia struktur (na przykład MVC) do pracy dobrze z oprogramowaniem pośredniczącym, jest zwiększona:

* Kolejność oprogramowania pośredniczącego i punktów końcowych jest konfigurowana w potoku przetwarzania żądań `Startup.Configure`programu .
* Punkty końcowe i oprogramowanie pośredniczące dobrze komponują się z innymi ASP.NET oparte na technologiach opartych na rdzeniu, takich jak kontrole kondycji.
* Punkty końcowe można zaimplementować zasady, takie jak CORS lub autoryzacji, zarówno w oprogramowaniu pośredniczącym i MVC.
* Filtry i atrybuty mogą być umieszczane na metodach w kontrolerach.

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/routing#routing-basics>.

## <a name="health-checks"></a>Kontrole kondycji

Kontrole kondycji używają routingu punktu końcowego z hostem ogólnym. W `Startup.Configure`programie `MapHealthChecks` wywołaj konstruktora punktów końcowych z adresem URL punktu końcowego lub ścieżką względną:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

Punkty końcowe kontroli kondycji mogą:

* Określ co najmniej jeden dozwolony host/porty.
* Wymagaj autoryzacji.
* Wymagają CORS.

Aby uzyskać więcej informacji zobacz następujące artykuły:

* <xref:migration/22-to-30#health-checks>
* <xref:host-and-deploy/health-checks>

## <a name="pipes-on-httpcontext"></a>Potoki na httpContext

Teraz można odczytać treść żądania i napisać treść odpowiedzi <xref:System.IO.Pipelines> przy użyciu interfejsu API. Dla zasobu <!-- <xref:Microsoft.AspNetCore.Http.HttpRequest.BodyReader> --> `HttpRequest.BodyReader`właściwość <xref:System.IO.Pipelines.PipeReader> zapewnia, który może służyć do odczytu treści żądania. Dla zasobu <!-- <xref:Microsoft.AspNetCore.Http.> --> `HttpResponse.BodyWriter`właściwość <xref:System.IO.Pipelines.PipeWriter> zapewnia, że może służyć do zapisu treści odpowiedzi. `HttpRequest.BodyReader`jest analogiem strumienia. `HttpRequest.Body` `HttpResponse.BodyWriter`jest analogiem strumienia. `HttpResponse.Body`

<!-- indirectly related, https://github.com/dotnet/docs/pull/14414 won't be published by 9/23  -->

## <a name="improved-error-reporting-in-iis"></a>Ulepszone raportowanie błędów w ujm iis

Błędy uruchamiania podczas hostowania aplikacji ASP.NET Core w usługach IIS teraz generują bogatsze dane diagnostyczne. Te błędy są zgłaszane do dziennika zdarzeń systemu Windows ze śladami stosu wszędzie tam, gdzie ma to zastosowanie. Ponadto wszystkie ostrzeżenia, błędy i nieobsługiwały wyjątki są rejestrowane w dzienniku zdarzeń systemu Windows.

## <a name="worker-service-and-worker-sdk"></a>SDK obsługi pracownika i pracownika

Program .NET Core 3.0 wprowadza nowy szablon aplikacji usługi worker service. Ten szablon stanowi punkt wyjścia do pisania usług długo działających w .NET Core.

Aby uzyskać więcej informacji, zobacz:

* [Core Pracownicy platformy .NET jako usługi systemu Windows](https://devblogs.microsoft.com/aspnet/net-core-workers-as-windows-services/)
* <xref:fundamentals/host/hosted-services>
* <xref:host-and-deploy/windows-service>

## <a name="forwarded-headers-middleware-improvements"></a>Ulepszenia oprogramowania pośredniczącego nagłówków przesyłanych dalej

W poprzednich wersjach ASP.NET Core, <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> wywoływanie i były problematyczne podczas wdrażania na platformie Azure Linux lub za dowolnym odwrotnym serwerem proxy innym niż IIS. Poprawka dla poprzednich wersji jest udokumentowana w [Forward schemat dla linuksa i odwrotnych serwerów proxy innych niż IIS](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies).

Ten scenariusz został ustalony w ASP.NET Core 3.0. Host włącza [oprogramowanie pośredniczące nagłówków przesyłanych dalej,](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) gdy zmienna środowiskowa `ASPNETCORE_FORWARDEDHEADERS_ENABLED` jest ustawiona na `true`. `ASPNETCORE_FORWARDEDHEADERS_ENABLED`jest ustawiona na `true` naszych obrazach kontenerów.

## <a name="performance-improvements"></a>Ulepszenia wydajności

ASP.NET Core 3.0 zawiera wiele ulepszeń, które zmniejszają użycie pamięci i zwiększają przepustowość:

* Zmniejszenie użycia pamięci podczas korzystania z wbudowanego kontenera iniekcji zależności dla usług o określonym zakresie.
* Zmniejszenie alokacji w ramach, w tym scenariuszy oprogramowania pośredniczącego i routingu.
* Zmniejszenie użycia pamięci dla połączeń WebSocket.
* Ulepszenia redukcji pamięci i przepływności dla połączeń HTTPS.
* Nowy zoptymalizowany i w pełni asynchroniczne serializator JSON.
* Zmniejszenie użycia pamięci i ulepszenia przepływności w analizowaniu formularzy.

## <a name="aspnet-core-30-only-runs-on-net-core-30"></a>ASP.NET Core 3.0 działa tylko na .NET Core 3.0

Od ASP.NET Core 3.0 program .NET Framework nie jest już obsługiwaną platformą docelową. Projekty ukierunkowane na program .NET Framework mogą być kontynuowane w pełni obsługiwany sposób za pomocą [wersji .NET Core 2.1 LTS](https://dotnet.microsoft.com/download/dotnet-core/2.1). Większość pakietów związanych z ASP.NET core 2.1.x będzie obsługiwana przez czas nieokreślony, poza trzyletnim okresem LTS dla platformy .NET Core 2.1.

Aby uzyskać informacje o migracji, zobacz [Port kodu z programu .NET Framework do platformy .NET Core](/dotnet/core/porting/).

## <a name="use-the-aspnet-core-shared-framework"></a>Korzystanie z udostępnionej struktury ASP.NET Core

ASP.NET framework udostępniony Core 3.0, zawarte w [metapakiecie Microsoft.AspNetCore.App,](xref:fundamentals/metapackage-app)nie `<PackageReference />` wymaga już jawnego elementu w pliku projektu. Udostępnione framework jest automatycznie odwołuje się `Microsoft.NET.Sdk.Web` podczas korzystania z SDK w pliku projektu:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

## <a name="assemblies-removed-from-the-aspnet-core-shared-framework"></a>Zestawy usunięte z udostępnionej struktury ASP.NET Core

Najbardziej znaczące zestawy usunięte z ASP.NET frameworka współużytkowanego Core 3.0 to:

* [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) (Json.NET). Aby dodać Json.NET do ASP.NET Core 3.0, zobacz [Dodawanie obsługi formatu JSON opartego na newtonsoft.Json](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support). ASP.NET Core 3.0 wprowadza `System.Text.Json` do czytania i pisania JSON. Aby uzyskać więcej informacji, zobacz [Nowa serializacja JSON](#new-json-serialization) w tym dokumencie.
* [Entity Framework Core](/ef/core/)

Aby uzyskać pełną listę zestawów usuniętych z udostępnionej struktury, zobacz [Zestawy usuwane z pliku Microsoft.AspNetCore.App 3.0](https://github.com/dotnet/AspNetCore/issues/3755). Aby uzyskać więcej informacji na temat motywacji tej zmiany, zobacz [Przerywanie zmian w witrynie Microsoft.AspNetCore.App w 3.0](https://github.com/aspnet/Announcements/issues/325) i [Pierwsze spojrzenie na zmiany nadchodzące w ASP.NET Core 3.0](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/).

<!-- 
## Additional information
For the complete list of changes, see the [ASP.NET Core 3.0 Release Notes](WHERE IS THIS????).
-->
 