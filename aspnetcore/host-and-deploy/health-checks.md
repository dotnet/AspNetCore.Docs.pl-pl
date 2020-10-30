---
title: Kontrole kondycji w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak skonfigurować Sprawdzanie kondycji infrastruktury ASP.NET Core, na przykład aplikacje i bazy danych.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 06/22/2020
no-loc:
- appsettings.json
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
uid: host-and-deploy/health-checks
ms.openlocfilehash: 32b7a4c6722ba45ba998f9430f5d6da6ddca53f9
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058665"
---
# <a name="health-checks-in-aspnet-core"></a>Kontrole kondycji w ASP.NET Core

Autor [Glenn Condron](https://github.com/glennc)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core oferuje oprogramowanie do sprawdzania kondycji i biblioteki do raportowania kondycji składników infrastruktury aplikacji.

Kontrole kondycji są udostępniane przez aplikację jako punkty końcowe HTTP. Punkty końcowe sprawdzania kondycji można skonfigurować dla różnych scenariuszy monitorowania w czasie rzeczywistym:

* Sondy kondycji mogą być używane przez koordynatorów kontenerów i moduły równoważenia obciążenia w celu sprawdzenia stanu aplikacji. Na przykład koordynator kontenera może odpowiedzieć na niepowodzenie sprawdzania kondycji przez zatrzymanie wdrożenia stopniowego lub ponowne uruchomienie kontenera. Moduł równoważenia obciążenia może reagować na aplikację w złej kondycji przez kierowanie ruchu z nieprawidłowego wystąpienia do prawidłowego wystąpienia.
* Użycie pamięci, dysku i innych zasobów serwera fizycznego może być monitorowane w celu zapewnienia prawidłowego stanu.
* Kontrole kondycji umożliwiają testowanie zależności aplikacji, takich jak bazy danych i punkty końcowe usług zewnętrznych, w celu potwierdzenia dostępności i normalnego działania.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

Przykładowa aplikacja zawiera przykłady scenariuszy opisanych w tym temacie. Aby uruchomić przykładową aplikację dla danego scenariusza, użyj polecenia [dotnet Run](/dotnet/core/tools/dotnet-run) z folderu projektu w powłoce poleceń. Zobacz plik *README.MD* aplikacji przykładowej i opisy scenariuszy w tym temacie, aby uzyskać szczegółowe informacje na temat korzystania z przykładowej aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

Kontrole kondycji są zwykle używane z zewnętrzną usługą monitorowania lub koordynatorem kontenera w celu sprawdzenia stanu aplikacji. Przed dodaniem kontroli kondycji do aplikacji należy określić system monitorowania, który ma być używany. System monitorujący określa, jakie typy testów kondycji należy utworzyć i jak skonfigurować ich punkty końcowe.

Pakiet [Microsoft. AspNetCore. Diagnostics. HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) jest wywoływany niejawnie dla aplikacji ASP.NET Core. Aby przeprowadzić kontrolę kondycji przy użyciu Entity Framework Core, Dodaj odwołanie do pakietu do pakietu [Microsoft. Extensions. Diagnostics. HealthChecks. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore) .

Przykładowa aplikacja zawiera kod uruchamiania, aby zademonstrować Sprawdzanie kondycji kilku scenariuszy. Scenariusz [sondowania bazy danych](#database-probe) sprawdza kondycję połączenia z bazą danych przy użyciu [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks). Scenariusz [sondowania DbContext](#entity-framework-core-dbcontext-probe) sprawdza bazę danych przy użyciu EF Core `DbContext` . Aby poznać scenariusze baz danych, przykładową aplikację:

* Tworzy bazę danych i udostępnia jej parametry połączenia w *appsettings.json* pliku.
* W pliku projektu znajdują się następujące odwołania do pakietów:
  * [AspNetCore. HealthChecks. SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [Microsoft. Extensions. Diagnostics. HealthChecks. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) nie jest obsługiwana przez firmę Microsoft lub nie są przez nią obsługiwane.

W innym scenariuszu sprawdzania kondycji przedstawiono sposób filtrowania kontroli kondycji do portu zarządzania. Przykładowa aplikacja wymaga utworzenia *Właściwości/launchSettings.jsw* pliku, który zawiera adres URL zarządzania i port zarządzania. Aby uzyskać więcej informacji, zobacz sekcję [filtrowanie według portów](#filter-by-port) .

## <a name="basic-health-probe"></a>Podstawowa sonda kondycji

W przypadku wielu aplikacji Podstawowa konfiguracja sondy kondycji, która zgłasza dostępność aplikacji do żądań przetwarzania, *liveness* jest wystarczająca do odnajdywania stanu aplikacji.

Konfiguracja podstawowa rejestruje usługi sprawdzania kondycji i wywołuje program do sprawdzania kondycji, aby odpowiedzieć na punkt końcowy adresu URL z odpowiedzią na kondycję. Domyślnie żadne określone kontrole kondycji nie są rejestrowane do testowania żadnej konkretnej zależności lub podsystemu. Aplikacja jest uważana za działającą w dobrej kondycji, jeśli jest w stanie reagować na adres URL punktu końcowego kondycji. Domyślny moduł zapisujący odpowiedzi zapisuje stan ( <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus> ) jako odpowiedź w postaci zwykłego tekstu z powrotem do klienta, co oznacza, że [HealthStatus. zdrowy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus. obniżone](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) lub [HealthStatus stan złej kondycji](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) .

Zarejestruj usługi sprawdzania kondycji <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> w programie w programie `Startup.ConfigureServices` . Utwórz punkt końcowy sprawdzania kondycji, wywołując `MapHealthChecks` w `Startup.Configure` .

W przykładowej aplikacji jest tworzony punkt końcowy sprawdzania kondycji `/health` ( *BasicStartup.cs* ):

```csharp
public class BasicStartup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddHealthChecks();
    }

    public void Configure(IApplicationBuilder app)
    {
        app.UseRouting();

        app.UseEndpoints(endpoints =>
        {
            endpoints.MapHealthChecks("/health");
        });
    }
}
```

Aby uruchomić podstawowy scenariusz konfiguracji przy użyciu przykładowej aplikacji, wykonaj następujące polecenie w folderze projektu w powłoce poleceń:

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a>Przykład platformy Docker

Platforma [Docker](xref:host-and-deploy/docker/index) oferuje wbudowaną `HEALTHCHECK` dyrektywę, której można użyć do sprawdzenia stanu aplikacji korzystającej z podstawowej konfiguracji sprawdzania kondycji:

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a>Utwórz kontrole kondycji

Kontrole kondycji są tworzone przez implementację <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interfejsu. <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*>Metoda zwraca wartość <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> wskazującą kondycję jako `Healthy` , `Degraded` lub `Unhealthy` . Wynik jest zapisywana jako odpowiedź w postaci zwykłego tekstu ze konfigurowalnym kodem stanu (Konfiguracja jest opisana w sekcji [Opcje sprawdzania kondycji](#health-check-options) ). <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> może również zwracać opcjonalne pary klucz-wartość.

W poniższej `ExampleHealthCheck` klasie przedstawiono układ kontroli kondycji. Logika kontroli kondycji jest umieszczana w `CheckHealthAsync` metodzie. Poniższy przykład ustawia zmienną fikcyjną, `healthCheckResultHealthy` do `true` . Jeśli wartość jest równa `healthCheckResultHealthy` `false` , zwracany jest stan [HealthCheckResult. złej kondycji](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) .

```csharp
public class ExampleHealthCheck : IHealthCheck
{
    public Task<HealthCheckResult> CheckHealthAsync(
        HealthCheckContext context,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        var healthCheckResultHealthy = true;

        if (healthCheckResultHealthy)
        {
            return Task.FromResult(
                HealthCheckResult.Healthy("A healthy result."));
        }

        return Task.FromResult(
            HealthCheckResult.Unhealthy("An unhealthy result."));
    }
}
```

## <a name="register-health-check-services"></a>Rejestrowanie usług sprawdzania kondycji

`ExampleHealthCheck`Typ jest dodawany do usług sprawdzania kondycji <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> w programie `Startup.ConfigureServices` :

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

<xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>Przeciążenie pokazane w poniższym przykładzie ustawia stan błędu ( <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus> ) w celu raportowania, kiedy Sprawdzanie kondycji zgłasza błąd. Jeśli stan niepowodzenia jest ustawiony na wartość `null` (domyślnie), zostanie zgłoszony [HealthStatus. złej kondycji](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) . To przeciążenie jest przydatnym scenariuszem dla autorów biblioteki, w którym stan niepowodzenia wskazywany przez bibliotekę jest wymuszany przez aplikację w przypadku niepowodzenia sprawdzania kondycji, jeśli implementacja sprawdzania kondycji przestrzega tego ustawienia.

*Tagi* mogą służyć do filtrowania kontroli kondycji (opisanych w sekcji [Sprawdzanie kondycji filtru](#filter-health-checks) ).

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> można również wykonać funkcję lambda. W poniższym przykładzie nazwa sprawdzania kondycji jest określona jako, `Example` a sprawdzanie zawsze zwraca prawidłowy stan:

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

Wywołanie <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddTypeActivatedCheck*> przekazywania argumentów do implementacji kontroli kondycji. W poniższym przykładzie `TestHealthCheckWithArgs` akceptuje liczbę całkowitą i ciąg do użycia, gdy <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> jest wywoływana:

```csharp
private class TestHealthCheckWithArgs : IHealthCheck
{
    public TestHealthCheckWithArgs(int i, string s)
    {
        I = i;
        S = s;
    }

    public int I { get; set; }

    public string S { get; set; }

    public Task<HealthCheckResult> CheckHealthAsync(HealthCheckContext context, 
        CancellationToken cancellationToken = default)
    {
        ...
    }
}
```

`TestHealthCheckWithArgs` jest zarejestrowany przez wywołanie `AddTypeActivatedCheck` z liczbą całkowitą i ciągiem przekazaną do implementacji:

```csharp
services.AddHealthChecks()
    .AddTypeActivatedCheck<TestHealthCheckWithArgs>(
        "test", 
        failureStatus: HealthStatus.Degraded, 
        tags: new[] { "example" }, 
        args: new object[] { 5, "string" });
```

## <a name="use-health-checks-routing"></a>Użyj routingu kontroli kondycji

W programie `Startup.Configure` Wywołaj program `MapHealthChecks` Endpoint Builder z adresem URL punktu końcowego lub ścieżką względną:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

### <a name="require-host"></a>Wymagaj hosta

Wywołaj, `RequireHost` Aby określić co najmniej jeden dozwolony Host dla punktu końcowego sprawdzania kondycji. Hosty powinny być w formacie Unicode, a nie formacie Punycode i mogą zawierać port. Jeśli kolekcja nie zostanie podana, każdy host zostanie zaakceptowany.

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireHost("www.contoso.com:5001");
});
```

Aby uzyskać więcej informacji, zobacz sekcję [filtrowanie według portów](#filter-by-port) .

### <a name="require-authorization"></a>Wymagaj autoryzacji

Wywołaj `RequireAuthorization` , aby uruchomić oprogramowanie pośredniczące autoryzacji w punkcie końcowym żądania sprawdzania kondycji. `RequireAuthorization`Przeciążenie akceptuje co najmniej jedną zasadę autoryzacji. Jeśli nie podano zasad, zostanie użyta domyślna zasada autoryzacji.

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireAuthorization();
});
```

### <a name="enable-cross-origin-requests-cors"></a>Włączanie żądań Cross-Origin (CORS)

Mimo że kontrole kondycji są wykonywane ręcznie z przeglądarki, nie jest to typowy scenariusz użycia, można włączyć oprogramowanie do obsługi mechanizmu CORS, wywołując `RequireCors` Testy kondycji punktów końcowych. `RequireCors`Przeciążenie akceptuje delegata konstruktora zasad CORS ( `CorsPolicyBuilder` ) lub nazwę zasady. Jeśli nie podano zasad, zostanie użyta domyślna zasada CORS. Aby uzyskać więcej informacji, zobacz <xref:security/cors>.

## <a name="health-check-options"></a>Opcje sprawdzania kondycji

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> możliwość dostosowania zachowania kontroli kondycji:

* [Filtrowanie kontroli kondycji](#filter-health-checks)
* [Dostosowywanie kodu stanu HTTP](#customize-the-http-status-code)
* [Pomiń nagłówki pamięci podręcznej](#suppress-cache-headers)
* [Dostosuj dane wyjściowe](#customize-output)

### <a name="filter-health-checks"></a>Filtrowanie kontroli kondycji

Domyślnie kontrole kondycji oprogramowania pośredniczącego uruchamia wszystkie zarejestrowane testy kondycji. Aby uruchomić podzestaw kontroli kondycji, podaj funkcję, która zwraca wartość logiczną dla <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> opcji. W poniższym przykładzie `Bar` Sprawdzanie kondycji jest odfiltrowane przez tag ( `bar_tag` ) w instrukcji warunkowej funkcji, gdzie `true` jest zwracany tylko wtedy, gdy właściwość sprawdzania kondycji jest <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> zgodna `foo_tag` lub `baz_tag` :

W pliku `Startup.ConfigureServices`:

```csharp
services.AddHealthChecks()
    .AddCheck("Foo", () =>
        HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
    .AddCheck("Bar", () =>
        HealthCheckResult.Unhealthy("Bar is unhealthy!"), tags: new[] { "bar_tag" })
    .AddCheck("Baz", () =>
        HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
```

W programie `Startup.Configure` program `Predicate` filtruje kontrolę kondycji "bar". Tylko Foo i baz Execute.:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("foo_tag") ||
            check.Tags.Contains("baz_tag")
    });
});
```

### <a name="customize-the-http-status-code"></a>Dostosowywanie kodu stanu HTTP

Użyj <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> , aby dostosować mapowanie stanu kondycji do kodów stanu HTTP. Następujące <xref:Microsoft.AspNetCore.Http.StatusCodes> przypisania są wartościami domyślnymi używanymi przez oprogramowanie pośredniczące. Zmień wartości kodów stanu, aby spełniały Twoje wymagania.

W pliku `Startup.Configure`:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResultStatusCodes =
        {
            [HealthStatus.Healthy] = StatusCodes.Status200OK,
            [HealthStatus.Degraded] = StatusCodes.Status200OK,
            [HealthStatus.Unhealthy] = StatusCodes.Status503ServiceUnavailable
        }
    });
});
```

### <a name="suppress-cache-headers"></a>Pomiń nagłówki pamięci podręcznej

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> Określa, czy kontrole kondycji powodują dodanie nagłówków HTTP do odpowiedzi sondy, aby zapobiec buforowaniu odpowiedzi. Jeśli wartość jest `false` (domyślnie), oprogramowanie pośredniczące ustawia lub zastępuje `Cache-Control` `Expires` nagłówki, i, `Pragma` Aby zapobiec buforowaniu odpowiedzi. Jeśli wartość to `true` , oprogramowanie pośredniczące nie modyfikuje nagłówków pamięci podręcznej odpowiedzi.

W pliku `Startup.Configure`:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        AllowCachingResponses = false
    });
});
```

### <a name="customize-output"></a>Dostosuj dane wyjściowe

W programie `Startup.Configure` Ustaw dla opcji [HealthCheckOptions. ResponseWriter](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter) delegata na potrzeby zapisywania odpowiedzi:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
});
```

Domyślnym delegatem jest zapisanie minimalnej odpowiedzi w postaci zwykłego tekstu z wartością ciągu [HealthReport. status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status). Następujące niestandardowe Delegaty wyprowadzają niestandardową odpowiedź JSON.

Pierwszy przykład z przykładowej aplikacji pokazuje, jak używać <xref:System.Text.Json?displayProperty=fullName> :

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse_SystemTextJson)]

Drugi przykład ilustruje sposób użycia [Newtonsoft.Jsw](https://www.nuget.org/packages/Newtonsoft.Json/):

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse_NewtonSoftJson)]

W przykładowej aplikacji Dodaj komentarz do `SYSTEM_TEXT_JSON` [dyrektywy preprocesora](xref:index#preprocessor-directives-in-sample-code) w programie *CustomWriterStartup.cs* , aby włączyć `Newtonsoft.Json` wersję programu `WriteResponse` .

Interfejs API kontroli kondycji nie zapewnia wbudowanej obsługi złożonych formatów powrotu JSON, ponieważ format jest specyficzny dla wybranego systemu monitorowania. W razie konieczności dostosuj odpowiedź w powyższych przykładach. Aby uzyskać więcej informacji na temat serializacji JSON przy użyciu `System.Text.Json` , zobacz [jak serializować i DESERIALIZOWAĆ kod JSON w programie .NET](/dotnet/standard/serialization/system-text-json-how-to).

## <a name="database-probe"></a>Sonda bazy danych

Kontrola kondycji może określić zapytanie bazy danych, które ma zostać uruchomione jako test logiczny, aby wskazać, czy baza danych ma zwykle odpowiadać.

Przykładowa aplikacja używa [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), biblioteki sprawdzania kondycji dla aplikacji ASP.NET Core, aby przeprowadzić kontrolę kondycji SQL Server bazie danych. `AspNetCore.Diagnostics.HealthChecks` wykonuje `SELECT 1` zapytanie względem bazy danych w celu potwierdzenia, że połączenie z bazą danych jest w dobrej kondycji.

> [!WARNING]
> Podczas sprawdzania połączenia z bazą danych za pomocą zapytania wybierz zapytanie, które zwraca szybko. Podejście zapytania uruchamia ryzyko przeciążenia bazy danych i spadek jej wydajności. W większości przypadków uruchomienie zapytania testowego nie jest konieczne. Wystarczy, że połączenie z bazą danych zostało zakończone pomyślnie. Jeśli okaże się, że konieczne jest uruchomienie zapytania, wybierz proste zapytanie SELECT, takie jak `SELECT 1` .

Dołącz odwołanie do pakietu do [AspNetCore. HealthChecks. SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).

Podaj prawidłowe parametry połączenia z bazą danych w *appsettings.json* pliku przykładowej aplikacji. Aplikacja używa SQL Server bazy danych o nazwie `HealthCheckSample` :

[!code-json[](health-checks/samples/3.x/HealthChecksSample/appsettings.json?highlight=3)]

Zarejestruj usługi sprawdzania kondycji <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> w programie w programie `Startup.ConfigureServices` . Przykładowa aplikacja wywołuje `AddSqlServer` metodę z parametrami połączenia bazy danych ( *DbHealthStartup.cs* ):

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

Punkt końcowy sprawdzania kondycji jest tworzony przez wywołanie `MapHealthChecks` w `Startup.Configure` :

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

Aby uruchomić scenariusz sondowania bazy danych za pomocą przykładowej aplikacji, wykonaj następujące polecenie w folderze projektu w powłoce poleceń:

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) nie jest obsługiwana przez firmę Microsoft lub nie są przez nią obsługiwane.

## <a name="entity-framework-core-dbcontext-probe"></a>Badanie Entity Framework Core DbContext

`DbContext`Sprawdzanie potwierdza, że aplikacja może komunikować się z bazą danych skonfigurowaną dla EF Core `DbContext` . `DbContext`Sprawdzanie jest obsługiwane w aplikacjach, które:

* Użyj [Entity Framework (EF) Core](/ef/core/).
* Dołącz odwołanie do pakietu do [Microsoft. Extensions. Diagnostics. HealthChecks. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).

`AddDbContextCheck<TContext>` rejestruje kontrolę kondycji dla elementu `DbContext` . `DbContext`Jest dostarczany jako `TContext` do metody. Jest dostępne Przeciążenie umożliwiające skonfigurowanie stanu niepowodzenia, tagów i niestandardowego zapytania testowego.

Domyślnie:

* `DbContextHealthCheck`Metoda wywołań EF Core `CanConnectAsync` . Można dostosować, jaka operacja jest uruchamiana podczas sprawdzania kondycji przy użyciu `AddDbContextCheck` przeciążenia metod.
* Nazwa sprawdzania kondycji jest nazwą `TContext` typu.

W przykładowej aplikacji `AppDbContext` jest dostarczany `AddDbContextCheck` i zarejestrowany jako usługa w `Startup.ConfigureServices` ( *DbContextHealthStartup.cs* ):

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

Punkt końcowy sprawdzania kondycji jest tworzony przez wywołanie `MapHealthChecks` w `Startup.Configure` :

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

Aby uruchomić `DbContext` scenariusz sondowania za pomocą przykładowej aplikacji, upewnij się, że baza danych określona przez parametry połączenia nie istnieje w wystąpieniu SQL Server. Jeśli baza danych istnieje, usuń ją.

Wykonaj następujące polecenie z folderu projektu w powłoce poleceń:

```dotnetcli
dotnet run --scenario dbcontext
```

Po uruchomieniu aplikacji Sprawdź stan kondycji, wysyłając żądanie do `/health` punktu końcowego w przeglądarce. Baza danych programu i `AppDbContext` nie istnieje, więc aplikacja udostępnia następujące odpowiedzi:

```
Unhealthy
```

Wyzwól przykładową aplikację, aby utworzyć bazę danych. Wprowadź żądanie do `/createdatabase` . Aplikacja odpowiada:

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

Utwórz żądanie do `/health` punktu końcowego. Istnieje baza danych i kontekst, aby aplikacja odpowiadała:

```
Healthy
```

Wyzwól przykładową aplikację, aby usunąć bazę danych. Wprowadź żądanie do `/deletedatabase` . Aplikacja odpowiada:

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

Utwórz żądanie do `/health` punktu końcowego. Aplikacja zawiera odpowiedź w złej kondycji:

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a>Oddzielne sondy gotowości i na żywo

W niektórych scenariuszach hostingu jest używana para kontroli kondycji, która odróżnia dwa stany aplikacji:

* *Gotowość* wskazuje, czy aplikacja działa normalnie, ale nie jest gotowa do odbierania żądań.
* *Dynamiczna* informacja wskazuje, czy aplikacja uległa awarii i musi zostać uruchomiona ponownie.

Rozważmy następujący przykład: aplikacja musi pobrać duży plik konfiguracji, zanim będzie gotowy do przetwarzania żądań. Nie chcemy, aby aplikacja była ponownie uruchamiana, jeśli pobieranie początkowe nie powiedzie się, ponieważ aplikacja może próbować pobrać plik kilka razy. Używamy *sondy na żywo* do opisywania wartości Live procesu, nie są wykonywane żadne dodatkowe kontrole. Chcemy również zapobiec wysyłaniu żądań do aplikacji przed pomyślnym pobraniem pliku konfiguracji. Użyjemy *sondy gotowości* , aby wskazać, że stan "nie jest gotowy" do momentu pomyślnego pobrania, a aplikacja będzie gotowa do odbierania żądań.

Przykładowa aplikacja zawiera kontrolę kondycji, aby zgłosić ukończenie długotrwałego zadania uruchamiania w [hostowanej usłudze](xref:fundamentals/host/hosted-services). `StartupHostedServiceHealthCheck`Uwidacznia właściwość, `StartupTaskCompleted` która może zostać ustawiona przez usługę hostowaną `true` po zakończeniu długotrwałego zadania ( *StartupHostedServiceHealthCheck.cs* ):

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

Długotrwałe zadanie w tle jest uruchamiane przez [hostowaną usługę](xref:fundamentals/host/hosted-services) ( *usługi/StartupHostedService* ). Po zakończeniu zadania `StartupHostedServiceHealthCheck.StartupTaskCompleted` jest ustawiony na `true` :

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

Kontrola kondycji jest zarejestrowana <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> w programie w programie `Startup.ConfigureServices` wraz z usługą hostowaną. Ponieważ usługa hostowana musi ustawić właściwość na sprawdzaniu kondycji, sprawdzanie kondycji jest również rejestrowane w kontenerze usługi ( *LivenessProbeStartup.cs* ):

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

Punkt końcowy sprawdzania kondycji jest tworzony przez wywołanie `MapHealthChecks` w `Startup.Configure` . W przykładowej aplikacji punkty końcowe sprawdzania kondycji są tworzone w:

* `/health/ready` w celu sprawdzenia gotowości. Sprawdzanie gotowości filtruje kontrolę kondycji w celu sprawdzenia kondycji za pomocą `ready` znacznika.
* `/health/live` na potrzeby kontroli na żywo. Sprawdzenie stanu na żywo odfiltruje `StartupHostedServiceHealthCheck` przez zwrócenie `false` elementu [HealthCheckOptions. predykatu](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (Aby uzyskać więcej informacji, [zobacz Sprawdzanie kondycji filtru](#filter-health-checks))

W poniższym przykładowym kodzie:

* Sprawdzenie gotowości używa wszystkich zarejestrowanych testów ze znacznikiem "gotowe".
* `Predicate`Wyklucza wszystkie testy i zwracają 200-OK.

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health/ready", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("ready"),
    });

    endpoints.MapHealthChecks("/health/live", new HealthCheckOptions()
    {
        Predicate = (_) => false
    });
}
```

Aby uruchomić scenariusz konfiguracji gotowości/na żywo za pomocą przykładowej aplikacji, wykonaj następujące polecenie w folderze projektu w powłoce poleceń:

```dotnetcli
dotnet run --scenario liveness
```

W przeglądarce odwiedź `/health/ready` kilka razy do 15 sekund. Sprawdzanie kondycji raportuje w *złej kondycji* przez pierwsze 15 sekund. Po upływie 15 sekund punkt końcowy zgłasza w *dobrej kondycji* , co odzwierciedla ukończenie długotrwałego zadania wykonywanego przez usługę hostowaną.

W tym przykładzie tworzony jest również Wydawca sprawdzania kondycji ( <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementacja), który uruchamia pierwsze sprawdzenie gotowości z dwoma drugim opóźnieniem. Aby uzyskać więcej informacji, zapoznaj się z sekcją [Sprawdzanie kondycji wydawcy](#health-check-publisher) .

### <a name="kubernetes-example"></a>Przykład Kubernetes

Korzystanie z odrębnych gotowości i kontroli na żywo jest przydatne w środowisku, takim jak [Kubernetes](https://kubernetes.io/). W programie Kubernetes aplikacja może być wymagana do wykonywania czasochłonnych zadań uruchamiania przed zaakceptowaniem żądań, takich jak Test dostępności źródłowej bazy danych. Przy użyciu osobnych kontroli program Orchestrator może rozróżnić, czy aplikacja działa, ale nie jest jeszcze gotowa lub Jeśli uruchomienie aplikacji nie powiodło się. Aby uzyskać więcej informacji na temat gotowości i sondy na żywo w programie Kubernetes, zobacz [Konfigurowanie sondy na żywo i gotowości](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) w dokumentacji Kubernetes.

Poniższy przykład demonstruje konfigurację sondowania gotowości Kubernetes:

```yml
spec:
  template:
  spec:
    readinessProbe:
      # an http probe
      httpGet:
        path: /health/ready
        port: 80
      # length of time to wait for a pod to initialize
      # after pod startup, before applying health checking
      initialDelaySeconds: 30
      timeoutSeconds: 1
    ports:
      - containerPort: 80
```

## <a name="metric-based-probe-with-a-custom-response-writer"></a>Sondowanie oparte na metrykach z niestandardowym modułem zapisywania odpowiedzi

Przykładowa aplikacja demonstruje kontrolę kondycji pamięci za pomocą modułu zapisywania odpowiedzi niestandardowych.

`MemoryHealthCheck` zgłasza stan obniżonej wydajności, jeśli aplikacja używa więcej niż danego progu pamięci (1 GB w przykładowej aplikacji). <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>Zawiera informacje dotyczące modułu wyrzucania elementów bezużytecznych (GC) dla aplikacji ( *MemoryHealthCheck.cs* ):

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

Zarejestruj usługi sprawdzania kondycji <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> w programie w programie `Startup.ConfigureServices` . Zamiast włączać kontrolę kondycji przez przekazanie jej do programu <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> `MemoryHealthCheck` jest zarejestrowany jako usługa. Wszystkie <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> zarejestrowane usługi są dostępne dla usług sprawdzania kondycji i oprogramowania pośredniczącego. Zalecamy rejestrację usług sprawdzania kondycji jako usług pojedynczych.

W *CustomWriterStartup.cs* przykładowej aplikacji:

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

Punkt końcowy sprawdzania kondycji jest tworzony przez wywołanie `MapHealthChecks` w `Startup.Configure` . `WriteResponse`Delegat jest dostarczany do właściwości <Microsoft. AspNetCore. Diagnostics. HealthChecks. HealthCheckOptions. ResponseWriter> do wyprowadzania niestandardowej odpowiedzi JSON, gdy jest wykonywane sprawdzanie kondycji:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
}
```

`WriteResponse`Delegat formatuje `CompositeHealthCheckResult` obiekt w formacie JSON i zwraca dane wyjściowe JSON dla odpowiedzi kontroli kondycji. Aby uzyskać więcej informacji, zobacz sekcję [Dostosowywanie danych wyjściowych](#customize-output) .

Aby uruchomić sondę opartą na metrykach z niestandardowymi danymi wyjściowymi modułu zapisywania odpowiedzi przy użyciu przykładowej aplikacji, wykonaj następujące polecenie w folderze projektu w powłoce poleceń:

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) obejmuje scenariusze sprawdzania kondycji oparte na metrykach, w tym magazyn dyskowy i maksymalną liczbę sprawdzeń na żywo.
>
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) nie jest obsługiwana przez firmę Microsoft lub nie są przez nią obsługiwane.

## <a name="filter-by-port"></a>Filtruj według portu

Zadzwoń do `RequireHost` `MapHealthChecks` wzorca adresu URL, który określa port, aby ograniczyć żądania sprawdzania kondycji do określonego portu. Jest to zwykle używane w środowisku kontenera w celu udostępnienia portu usług monitorowania.

Przykładowa aplikacja konfiguruje port przy użyciu [zmiennej środowiskowej dostawcy konfiguracji](xref:fundamentals/configuration/index#environment-variables). Port jest ustawiany w *launchSettings.jsw* pliku i przeszedł do dostawcy konfiguracji za pośrednictwem zmiennej środowiskowej. Należy również skonfigurować serwer do nasłuchiwania żądań na porcie zarządzania.

Aby użyć przykładowej aplikacji do zademonstrowania konfiguracji portów zarządzania, Utwórz *launchSettings.jsw* pliku w folderze *Właściwości* .

Następujące *Właściwości/launchSettings.jsw* pliku w przykładowej aplikacji nie są uwzględnione w plikach projektu przykładowej aplikacji i muszą zostać utworzone ręcznie:

```json
{
  "profiles": {
    "SampleApp": {
      "commandName": "Project",
      "commandLineArgs": "",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development",
        "ASPNETCORE_URLS": "http://localhost:5000/;http://localhost:5001/",
        "ASPNETCORE_MANAGEMENTPORT": "5001"
      },
      "applicationUrl": "http://localhost:5000/"
    }
  }
}
```

Zarejestruj usługi sprawdzania kondycji <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> w programie w programie `Startup.ConfigureServices` . Utwórz punkt końcowy sprawdzania kondycji, wywołując `MapHealthChecks` w `Startup.Configure` .

W przykładowej aplikacji wywołanie do `RequireHost` punktu końcowego w programie `Startup.Configure` określa port zarządzania z konfiguracji:

```csharp
endpoints.MapHealthChecks("/health")
    .RequireHost($"*:{Configuration["ManagementPort"]}");
```

Punkty końcowe są tworzone w aplikacji przykładowej w programie `Startup.Configure` . W poniższym przykładowym kodzie:

* Sprawdzenie gotowości używa wszystkich zarejestrowanych testów ze znacznikiem "gotowe".
* `Predicate`Wyklucza wszystkie testy i zwracają 200-OK.

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health/ready", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("ready"),
    });

    endpoints.MapHealthChecks("/health/live", new HealthCheckOptions()
    {
        Predicate = (_) => false
    });
}
```

> [!NOTE]
> Można uniknąć tworzenia *launchSettings.jsw* pliku w przykładowej aplikacji przez ustawienie portu zarządzania jawnie w kodzie. W *program.cs* , gdzie <xref:Microsoft.Extensions.Hosting.HostBuilder> został utworzony, Dodaj wywołanie do <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*> i Podaj punkt końcowy portu zarządzania aplikacji. W `Configure` programie *ManagementPortStartup.cs* określ port zarządzania przy użyciu `RequireHost` :
>
> *Program.cs* :
>
> ```csharp
> return new HostBuilder()
>     .ConfigureWebHostDefaults(webBuilder =>
>     {
>         webBuilder.UseKestrel()
>             .ConfigureKestrel(serverOptions =>
>             {
>                 serverOptions.ListenAnyIP(5001);
>             })
>             .UseStartup(startupType);
>     })
>     .Build();
> ```
>
> *ManagementPortStartup.cs* :
>
> ```csharp
> app.UseEndpoints(endpoints =>
> {
>     endpoints.MapHealthChecks("/health").RequireHost("*:5001");
> });
> ```

Aby uruchomić scenariusz konfiguracji portu zarządzania przy użyciu przykładowej aplikacji, wykonaj następujące polecenie w folderze projektu w powłoce poleceń:

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a>Dystrybucja biblioteki kontroli kondycji

Aby rozpowszechnić kontrolę kondycji jako bibliotekę:

1. Napisz kontrolę kondycji, która implementuje <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interfejs jako autonomiczną klasę. Klasa może polegać na [iniekcji zależności (di)](xref:fundamentals/dependency-injection), aktywacji typu i [nazwanych opcjach](xref:fundamentals/configuration/options) w celu uzyskania dostępu do danych konfiguracyjnych.

   W logice kontroli kondycji `CheckHealthAsync` :

   * `data1` i `data2` są używane w metodzie do uruchamiania logiki sprawdzania kondycji sondy.
   * `AccessViolationException` jest obsługiwane.

   Gdy wystąpi <xref:System.AccessViolationException> , <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> zostaje zwrócony z, <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> Aby umożliwić użytkownikom Konfigurowanie stanu niepowodzenia sprawdzania kondycji.

   ```csharp
   using System;
   using System.Threading;
   using System.Threading.Tasks;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   namespace SampleApp
   {
       public class ExampleHealthCheck : IHealthCheck
       {
           private readonly string _data1;
           private readonly int? _data2;

           public ExampleHealthCheck(string data1, int? data2)
           {
               _data1 = data1 ?? throw new ArgumentNullException(nameof(data1));
               _data2 = data2 ?? throw new ArgumentNullException(nameof(data2));
           }

           public async Task<HealthCheckResult> CheckHealthAsync(
               HealthCheckContext context, CancellationToken cancellationToken)
           {
               try
               {
                   return HealthCheckResult.Healthy();
               }
               catch (AccessViolationException ex)
               {
                   return new HealthCheckResult(
                       context.Registration.FailureStatus,
                       description: "An access violation occurred during the check.",
                       exception: ex,
                       data: null);
               }
           }
       }
   }
   ```

1. Napisz metodę rozszerzenia z parametrami, które są używane przez zużywaną aplikację w jej `Startup.Configure` metodzie. W poniższym przykładzie przyjęto założenie, że następująca sygnatura metody kontroli kondycji:

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   Poprzednia sygnatura wskazuje, że `ExampleHealthCheck` wymaga dodatkowych danych do przetworzenia logiki sondowania sprawdzania kondycji. Dane są przekazywane delegatom używanym do tworzenia wystąpienia kontroli kondycji, gdy Sprawdzanie kondycji jest zarejestrowane za pomocą metody rozszerzenia. W poniższym przykładzie obiekt wywołujący określa opcjonalne:

   * Nazwa sprawdzania kondycji ( `name` ). Jeśli `null` `example_health_check` jest używany.
   * punkt danych ciągu dla kontroli kondycji ( `data1` ).
   * punkt danych liczb całkowitych dla kontroli kondycji ( `data2` ). Jeśli `null` `1` jest używany.
   * stan niepowodzenia ( <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus> ). Wartość domyślna to `null`. Jeśli `null` dla stanu błędu zostanie zgłoszona wartość [HealthStatus. w złej kondycji](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) .
   * Tagi ( `IEnumerable<string>` ).

   ```csharp
   using System.Collections.Generic;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public static class ExampleHealthCheckBuilderExtensions
   {
       const string DefaultName = "example_health_check";

       public static IHealthChecksBuilder AddExampleHealthCheck(
           this IHealthChecksBuilder builder,
           string name = default,
           string data1,
           int data2 = 1,
           HealthStatus? failureStatus = default,
           IEnumerable<string> tags = default)
       {
           return builder.Add(new HealthCheckRegistration(
               name ?? DefaultName,
               sp => new ExampleHealthCheck(data1, data2),
               failureStatus,
               tags));
       }
   }
   ```

## <a name="health-check-publisher"></a>Wydawca kontroli kondycji

Po <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> dodaniu do kontenera usługi system kontroli kondycji okresowo wykonuje sprawdzanie kondycji i wywołuje `PublishAsync` wynik. Jest to przydatne w scenariuszu systemu monitorowania kondycji opartej na wypychaniu, który oczekuje, że każdy proces będzie okresowo wywoływał system monitorowania w celu określenia kondycji.

<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>Interfejs ma jedną metodę:

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> Zezwól na ustawienie:

* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>: Początkowe opóźnienie stosowane po uruchomieniu aplikacji przed wykonaniem <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> wystąpień. Opóźnienie jest stosowane raz podczas uruchamiania i nie ma zastosowania do kolejnych iteracji. Wartość domyślna to pięć sekund.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>: Okres <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> wykonania. Wartość domyślna to 30 sekund.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>: Jeśli <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> jest `null` (domyślnie), usługa wydawcy sprawdzania kondycji uruchamia wszystkie zarejestrowane kontrole kondycji. Aby uruchomić podzestaw kontroli kondycji, należy określić funkcję, która filtruje zestaw kontroli. Predykat jest oceniany w każdym okresie.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>: Limit czasu wykonywania kontroli kondycji dla wszystkich <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> wystąpień. Użyj <xref:System.Threading.Timeout.InfiniteTimeSpan> , aby wykonać bez limitu czasu. Wartość domyślna to 30 sekund.

W przykładowej aplikacji `ReadinessPublisher` jest <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementacją. Stan sprawdzania kondycji jest rejestrowany dla każdego sprawdzenia na poziomie dziennika:

* Informacja ( <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> ), jeśli stan kontroli kondycji to <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy> .
* Error ( <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*> ), jeśli stan ma wartość <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> lub <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy> .

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

W `LivenessProbeStartup` przykładzie przykładowej aplikacji `StartupHostedService` sprawdzanie gotowości ma dwa drugie opóźnienie uruchamiania i sprawdza, co 30 sekund. Aby uaktywnić <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementację, przykład rejestruje się `ReadinessPublisher` jako usługa pojedyncza w kontenerze [iniekcji zależności (di)](xref:fundamentals/dependency-injection) :

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

> [!NOTE]
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) obejmuje wydawców dla kilku systemów, w tym [Application Insights](/azure/application-insights/app-insights-overview).
>
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) nie jest obsługiwana przez firmę Microsoft lub nie są przez nią obsługiwane.

## <a name="restrict-health-checks-with-mapwhen"></a>Ogranicz kontrolę kondycji za pomocą MapWhen

Użyj, <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> Aby warunkowo rozgałęziać potok żądania dla punktów końcowych sprawdzania kondycji.

W poniższym przykładzie `MapWhen` rozgałęzienie potoku żądania w celu aktywowania kontroli kondycji oprogramowania pośredniczącego w przypadku otrzymania żądania GET dla `api/HealthCheck` punktu końcowego:

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseEndpoints(endpoints =>
{
    endpoints.MapRazorPages();
});
```

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/middleware/index#branch-the-middleware-pipeline>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core oferuje oprogramowanie do sprawdzania kondycji i biblioteki do raportowania kondycji składników infrastruktury aplikacji.

Kontrole kondycji są udostępniane przez aplikację jako punkty końcowe HTTP. Punkty końcowe sprawdzania kondycji można skonfigurować dla różnych scenariuszy monitorowania w czasie rzeczywistym:

* Sondy kondycji mogą być używane przez koordynatorów kontenerów i moduły równoważenia obciążenia w celu sprawdzenia stanu aplikacji. Na przykład koordynator kontenera może odpowiedzieć na niepowodzenie sprawdzania kondycji przez zatrzymanie wdrożenia stopniowego lub ponowne uruchomienie kontenera. Moduł równoważenia obciążenia może reagować na aplikację w złej kondycji przez kierowanie ruchu z nieprawidłowego wystąpienia do prawidłowego wystąpienia.
* Użycie pamięci, dysku i innych zasobów serwera fizycznego może być monitorowane w celu zapewnienia prawidłowego stanu.
* Kontrole kondycji umożliwiają testowanie zależności aplikacji, takich jak bazy danych i punkty końcowe usług zewnętrznych, w celu potwierdzenia dostępności i normalnego działania.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

Przykładowa aplikacja zawiera przykłady scenariuszy opisanych w tym temacie. Aby uruchomić przykładową aplikację dla danego scenariusza, użyj polecenia [dotnet Run](/dotnet/core/tools/dotnet-run) z folderu projektu w powłoce poleceń. Zobacz plik *README.MD* aplikacji przykładowej i opisy scenariuszy w tym temacie, aby uzyskać szczegółowe informacje na temat korzystania z przykładowej aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

Kontrole kondycji są zwykle używane z zewnętrzną usługą monitorowania lub koordynatorem kontenera w celu sprawdzenia stanu aplikacji. Przed dodaniem kontroli kondycji do aplikacji należy określić system monitorowania, który ma być używany. System monitorujący określa, jakie typy testów kondycji należy utworzyć i jak skonfigurować ich punkty końcowe.

Odwołującego się do pakietu [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) lub Dodaj odwołanie do pakietu w pakiecie [Microsoft. AspNetCore. Diagnostics. HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) .

Przykładowa aplikacja zawiera kod uruchamiania, aby zademonstrować Sprawdzanie kondycji kilku scenariuszy. Scenariusz [sondowania bazy danych](#database-probe) sprawdza kondycję połączenia z bazą danych przy użyciu [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks). Scenariusz [sondowania DbContext](#entity-framework-core-dbcontext-probe) sprawdza bazę danych przy użyciu EF Core `DbContext` . Aby poznać scenariusze baz danych, przykładową aplikację:

* Tworzy bazę danych i udostępnia jej parametry połączenia w *appsettings.json* pliku.
* W pliku projektu znajdują się następujące odwołania do pakietów:
  * [AspNetCore. HealthChecks. SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [Microsoft. Extensions. Diagnostics. HealthChecks. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) nie jest obsługiwana przez firmę Microsoft lub nie są przez nią obsługiwane.

W innym scenariuszu sprawdzania kondycji przedstawiono sposób filtrowania kontroli kondycji do portu zarządzania. Przykładowa aplikacja wymaga utworzenia *Właściwości/launchSettings.jsw* pliku, który zawiera adres URL zarządzania i port zarządzania. Aby uzyskać więcej informacji, zobacz sekcję [filtrowanie według portów](#filter-by-port) .

## <a name="basic-health-probe"></a>Podstawowa sonda kondycji

W przypadku wielu aplikacji Podstawowa konfiguracja sondy kondycji, która zgłasza dostępność aplikacji do żądań przetwarzania, *liveness* jest wystarczająca do odnajdywania stanu aplikacji.

Konfiguracja podstawowa rejestruje usługi sprawdzania kondycji i wywołuje program do sprawdzania kondycji, aby odpowiedzieć na punkt końcowy adresu URL z odpowiedzią na kondycję. Domyślnie żadne określone kontrole kondycji nie są rejestrowane do testowania żadnej konkretnej zależności lub podsystemu. Aplikacja jest uważana za działającą w dobrej kondycji, jeśli jest w stanie reagować na adres URL punktu końcowego kondycji. Domyślny moduł zapisujący odpowiedzi zapisuje stan ( <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus> ) jako odpowiedź w postaci zwykłego tekstu z powrotem do klienta, co oznacza, że [HealthStatus. zdrowy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus. obniżone](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) lub [HealthStatus stan złej kondycji](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) .

Zarejestruj usługi sprawdzania kondycji <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> w programie w programie `Startup.ConfigureServices` . Dodaj punkt końcowy dla programu testowego kondycji programu z <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> programu w potoku przetwarzania żądania `Startup.Configure` .

W przykładowej aplikacji jest tworzony punkt końcowy sprawdzania kondycji `/health` ( *BasicStartup.cs* ):

```csharp
public class BasicStartup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddHealthChecks();
    }

    public void Configure(IApplicationBuilder app)
    {
        app.UseHealthChecks("/health");
    }
}
```

Aby uruchomić podstawowy scenariusz konfiguracji przy użyciu przykładowej aplikacji, wykonaj następujące polecenie w folderze projektu w powłoce poleceń:

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a>Przykład platformy Docker

Platforma [Docker](xref:host-and-deploy/docker/index) oferuje wbudowaną `HEALTHCHECK` dyrektywę, której można użyć do sprawdzenia stanu aplikacji korzystającej z podstawowej konfiguracji sprawdzania kondycji:

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a>Utwórz kontrole kondycji

Kontrole kondycji są tworzone przez implementację <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interfejsu. <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*>Metoda zwraca wartość <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> wskazującą kondycję jako `Healthy` , `Degraded` lub `Unhealthy` . Wynik jest zapisywana jako odpowiedź w postaci zwykłego tekstu ze konfigurowalnym kodem stanu (Konfiguracja jest opisana w sekcji [Opcje sprawdzania kondycji](#health-check-options) ). <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> może również zwracać opcjonalne pary klucz-wartość.

### <a name="example-health-check"></a>Przykładowe Sprawdzenie kondycji

W poniższej `ExampleHealthCheck` klasie przedstawiono układ kontroli kondycji. Logika kontroli kondycji jest umieszczana w `CheckHealthAsync` metodzie. Poniższy przykład ustawia zmienną fikcyjną, `healthCheckResultHealthy` do `true` . Jeśli wartość jest równa `healthCheckResultHealthy` `false` , zwracany jest stan [HealthCheckResult. złej kondycji](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) .

```csharp
public class ExampleHealthCheck : IHealthCheck
{
    public Task<HealthCheckResult> CheckHealthAsync(
        HealthCheckContext context,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        var healthCheckResultHealthy = true;

        if (healthCheckResultHealthy)
        {
            return Task.FromResult(
                HealthCheckResult.Healthy("The check indicates a healthy result."));
        }

        return Task.FromResult(
            HealthCheckResult.Unhealthy("The check indicates an unhealthy result."));
    }
}
```

### <a name="register-health-check-services"></a>Rejestrowanie usług sprawdzania kondycji

`ExampleHealthCheck`Typ jest dodawany do usług sprawdzania kondycji w programie w `Startup.ConfigureServices` programie <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> :

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

<xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>Przeciążenie pokazane w poniższym przykładzie ustawia stan błędu ( <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus> ) w celu raportowania, kiedy Sprawdzanie kondycji zgłasza błąd. Jeśli stan niepowodzenia jest ustawiony na wartość `null` (domyślnie), zostanie zgłoszony [HealthStatus. złej kondycji](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) . To przeciążenie jest przydatnym scenariuszem dla autorów biblioteki, w którym stan niepowodzenia wskazywany przez bibliotekę jest wymuszany przez aplikację w przypadku niepowodzenia sprawdzania kondycji, jeśli implementacja sprawdzania kondycji przestrzega tego ustawienia.

*Tagi* mogą służyć do filtrowania kontroli kondycji (opisanych w sekcji [Sprawdzanie kondycji filtru](#filter-health-checks) ).

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> można również wykonać funkcję lambda. W poniższym `Startup.ConfigureServices` przykładzie nazwa sprawdzania kondycji jest określona jako, `Example` a sprawdzanie zawsze zwraca prawidłowy stan:

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

### <a name="use-health-checks-middleware"></a>Używanie oprogramowania do sprawdzania kondycji

W programie `Startup.Configure` Wywołaj <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> w potoku przetwarzania z adresem URL punktu końcowego lub ścieżką względną:

```csharp
app.UseHealthChecks("/health");
```

Jeśli kontrole kondycji powinny nasłuchiwać określonego portu, Użyj przeciążenia, <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> Aby ustawić port (dokładniej opisane w sekcji [Filtruj według portu](#filter-by-port) ):

```csharp
app.UseHealthChecks("/health", port: 8000);
```

## <a name="health-check-options"></a>Opcje sprawdzania kondycji

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> możliwość dostosowania zachowania kontroli kondycji:

* [Filtrowanie kontroli kondycji](#filter-health-checks)
* [Dostosowywanie kodu stanu HTTP](#customize-the-http-status-code)
* [Pomiń nagłówki pamięci podręcznej](#suppress-cache-headers)
* [Dostosuj dane wyjściowe](#customize-output)

### <a name="filter-health-checks"></a>Filtrowanie kontroli kondycji

Domyślnie kontrole kondycji oprogramowania pośredniczącego uruchamia wszystkie zarejestrowane testy kondycji. Aby uruchomić podzestaw kontroli kondycji, podaj funkcję, która zwraca wartość logiczną dla <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> opcji. W poniższym przykładzie `Bar` Sprawdzanie kondycji jest odfiltrowane przez tag ( `bar_tag` ) w instrukcji warunkowej funkcji, gdzie `true` jest zwracany tylko wtedy, gdy właściwość sprawdzania kondycji jest <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> zgodna `foo_tag` lub `baz_tag` :

```csharp
using System.Threading.Tasks;
using Microsoft.AspNetCore.Diagnostics.HealthChecks;
using Microsoft.Extensions.Diagnostics.HealthChecks;

public void ConfigureServices(IServiceCollection services)
{
    services.AddHealthChecks()
        .AddCheck("Foo", () =>
            HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
        .AddCheck("Bar", () =>
            HealthCheckResult.Unhealthy("Bar is unhealthy!"), 
                tags: new[] { "bar_tag" })
        .AddCheck("Baz", () =>
            HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
}

public void Configure(IApplicationBuilder app)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("foo_tag") ||
            check.Tags.Contains("baz_tag")
    });
}
```

### <a name="customize-the-http-status-code"></a>Dostosowywanie kodu stanu HTTP

Użyj <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> , aby dostosować mapowanie stanu kondycji do kodów stanu HTTP. Następujące <xref:Microsoft.AspNetCore.Http.StatusCodes> przypisania są wartościami domyślnymi używanymi przez oprogramowanie pośredniczące. Zmień wartości kodów stanu, aby spełniały Twoje wymagania.

W pliku `Startup.Configure`:

```csharp
//using Microsoft.AspNetCore.Diagnostics.HealthChecks;
//using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResultStatusCodes =
    {
        [HealthStatus.Healthy] = StatusCodes.Status200OK,
        [HealthStatus.Degraded] = StatusCodes.Status200OK,
        [HealthStatus.Unhealthy] = StatusCodes.Status503ServiceUnavailable
    }
});
```

### <a name="suppress-cache-headers"></a>Pomiń nagłówki pamięci podręcznej

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> Określa, czy kontrole kondycji powodują dodanie nagłówków HTTP do odpowiedzi sondy, aby zapobiec buforowaniu odpowiedzi. Jeśli wartość jest `false` (domyślnie), oprogramowanie pośredniczące ustawia lub zastępuje `Cache-Control` `Expires` nagłówki, i, `Pragma` Aby zapobiec buforowaniu odpowiedzi. Jeśli wartość to `true` , oprogramowanie pośredniczące nie modyfikuje nagłówków pamięci podręcznej odpowiedzi.

W pliku `Startup.Configure`:

```csharp
//using Microsoft.AspNetCore.Diagnostics.HealthChecks;
//using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    AllowCachingResponses = false
});
```

### <a name="customize-output"></a>Dostosuj dane wyjściowe

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter>Opcja Pobiera lub ustawia delegata używany do zapisywania odpowiedzi. Domyślnym delegatem jest zapisanie minimalnej odpowiedzi w postaci zwykłego tekstu z wartością ciągu [HealthReport. status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).

W pliku `Startup.Configure`:

```csharp
// using Microsoft.AspNetCore.Diagnostics.HealthChecks;
// using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResponseWriter = WriteResponse
});
```

Domyślnym delegatem jest zapisanie minimalnej odpowiedzi w postaci zwykłego tekstu z wartością ciągu [HealthReport. status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status). Następujący delegat niestandardowy, `WriteResponse` , wyprowadza niestandardową odpowiedź JSON:

```csharp
private static Task WriteResponse(HttpContext httpContext, HealthReport result)
{
    httpContext.Response.ContentType = "application/json";

    var json = new JObject(
        new JProperty("status", result.Status.ToString()),
        new JProperty("results", new JObject(result.Entries.Select(pair =>
            new JProperty(pair.Key, new JObject(
                new JProperty("status", pair.Value.Status.ToString()),
                new JProperty("description", pair.Value.Description),
                new JProperty("data", new JObject(pair.Value.Data.Select(
                    p => new JProperty(p.Key, p.Value))))))))));
    return httpContext.Response.WriteAsync(
        json.ToString(Formatting.Indented));
}
```

System kontroli kondycji nie zapewnia wbudowanej obsługi złożonych formatów powrotu JSON, ponieważ format jest specyficzny dla wybranego systemu monitorowania. Możesz dowolnie dostosowywać `JObject` w powyższym przykładzie, aby zaspokoić Twoje potrzeby.

## <a name="database-probe"></a>Sonda bazy danych

Kontrola kondycji może określić zapytanie bazy danych, które ma zostać uruchomione jako test logiczny, aby wskazać, czy baza danych ma zwykle odpowiadać.

Przykładowa aplikacja używa [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), biblioteki sprawdzania kondycji dla aplikacji ASP.NET Core, aby przeprowadzić kontrolę kondycji SQL Server bazie danych. `AspNetCore.Diagnostics.HealthChecks` wykonuje `SELECT 1` zapytanie względem bazy danych w celu potwierdzenia, że połączenie z bazą danych jest w dobrej kondycji.

> [!WARNING]
> Podczas sprawdzania połączenia z bazą danych za pomocą zapytania wybierz zapytanie, które zwraca szybko. Podejście zapytania uruchamia ryzyko przeciążenia bazy danych i spadek jej wydajności. W większości przypadków uruchomienie zapytania testowego nie jest konieczne. Wystarczy, że połączenie z bazą danych zostało zakończone pomyślnie. Jeśli okaże się, że konieczne jest uruchomienie zapytania, wybierz proste zapytanie SELECT, takie jak `SELECT 1` .

Dołącz odwołanie do pakietu do [AspNetCore. HealthChecks. SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).

Podaj prawidłowe parametry połączenia z bazą danych w *appsettings.json* pliku przykładowej aplikacji. Aplikacja używa SQL Server bazy danych o nazwie `HealthCheckSample` :

[!code-json[](health-checks/samples/2.x/HealthChecksSample/appsettings.json?highlight=3)]

Zarejestruj usługi sprawdzania kondycji <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> w programie w programie `Startup.ConfigureServices` . Przykładowa aplikacja wywołuje `AddSqlServer` metodę z parametrami połączenia bazy danych ( *DbHealthStartup.cs* ):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

Wywołaj kontrolę kondycji oprogramowania pośredniczącego w potoku przetwarzania aplikacji w `Startup.Configure` :

```csharp
app.UseHealthChecks("/health");
```

Aby uruchomić scenariusz sondowania bazy danych za pomocą przykładowej aplikacji, wykonaj następujące polecenie w folderze projektu w powłoce poleceń:

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) nie jest obsługiwana przez firmę Microsoft lub nie są przez nią obsługiwane.

## <a name="entity-framework-core-dbcontext-probe"></a>Badanie Entity Framework Core DbContext

`DbContext`Sprawdzanie potwierdza, że aplikacja może komunikować się z bazą danych skonfigurowaną dla EF Core `DbContext` . `DbContext`Sprawdzanie jest obsługiwane w aplikacjach, które:

* Użyj [Entity Framework (EF) Core](/ef/core/).
* Dołącz odwołanie do pakietu do [Microsoft. Extensions. Diagnostics. HealthChecks. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).

`AddDbContextCheck<TContext>` rejestruje kontrolę kondycji dla elementu `DbContext` . `DbContext`Jest dostarczany jako `TContext` do metody. Jest dostępne Przeciążenie umożliwiające skonfigurowanie stanu niepowodzenia, tagów i niestandardowego zapytania testowego.

Domyślnie:

* `DbContextHealthCheck`Metoda wywołań EF Core `CanConnectAsync` . Można dostosować, jaka operacja jest uruchamiana podczas sprawdzania kondycji przy użyciu `AddDbContextCheck` przeciążenia metod.
* Nazwa sprawdzania kondycji jest nazwą `TContext` typu.

W przykładowej aplikacji `AppDbContext` jest dostarczany `AddDbContextCheck` i zarejestrowany jako usługa w `Startup.ConfigureServices` ( *DbContextHealthStartup.cs* ):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

W przykładowej aplikacji program `UseHealthChecks` dodaje do programu oprogramowanie do sprawdzania kondycji `Startup.Configure` .

```csharp
app.UseHealthChecks("/health");
```

Aby uruchomić `DbContext` scenariusz sondowania za pomocą przykładowej aplikacji, upewnij się, że baza danych określona przez parametry połączenia nie istnieje w wystąpieniu SQL Server. Jeśli baza danych istnieje, usuń ją.

Wykonaj następujące polecenie z folderu projektu w powłoce poleceń:

```dotnetcli
dotnet run --scenario dbcontext
```

Po uruchomieniu aplikacji Sprawdź stan kondycji, wysyłając żądanie do `/health` punktu końcowego w przeglądarce. Baza danych programu i `AppDbContext` nie istnieje, więc aplikacja udostępnia następujące odpowiedzi:

```
Unhealthy
```

Wyzwól przykładową aplikację, aby utworzyć bazę danych. Wprowadź żądanie do `/createdatabase` . Aplikacja odpowiada:

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

Utwórz żądanie do `/health` punktu końcowego. Istnieje baza danych i kontekst, aby aplikacja odpowiadała:

```
Healthy
```

Wyzwól przykładową aplikację, aby usunąć bazę danych. Wprowadź żądanie do `/deletedatabase` . Aplikacja odpowiada:

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

Utwórz żądanie do `/health` punktu końcowego. Aplikacja zawiera odpowiedź w złej kondycji:

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a>Oddzielne sondy gotowości i na żywo

W niektórych scenariuszach hostingu jest używana para kontroli kondycji, która odróżnia dwa stany aplikacji:

* *Gotowość* wskazuje, czy aplikacja działa normalnie, ale nie jest gotowa do odbierania żądań.
* *Dynamiczna* informacja wskazuje, czy aplikacja uległa awarii i musi zostać uruchomiona ponownie.

Rozważmy następujący przykład: aplikacja musi pobrać duży plik konfiguracji, zanim będzie gotowy do przetwarzania żądań. Nie chcemy, aby aplikacja była ponownie uruchamiana, jeśli pobieranie początkowe nie powiedzie się, ponieważ aplikacja może próbować pobrać plik kilka razy. Używamy *sondy na żywo* do opisywania wartości Live procesu, nie są wykonywane żadne dodatkowe kontrole. Chcemy również zapobiec wysyłaniu żądań do aplikacji przed pomyślnym pobraniem pliku konfiguracji. Użyjemy *sondy gotowości* , aby wskazać, że stan "nie jest gotowy" do momentu pomyślnego pobrania, a aplikacja będzie gotowa do odbierania żądań.

Przykładowa aplikacja zawiera kontrolę kondycji, aby zgłosić ukończenie długotrwałego zadania uruchamiania w [hostowanej usłudze](xref:fundamentals/host/hosted-services). `StartupHostedServiceHealthCheck`Uwidacznia właściwość, `StartupTaskCompleted` która może zostać ustawiona przez usługę hostowaną `true` po zakończeniu długotrwałego zadania ( *StartupHostedServiceHealthCheck.cs* ):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

Długotrwałe zadanie w tle jest uruchamiane przez [hostowaną usługę](xref:fundamentals/host/hosted-services) ( *usługi/StartupHostedService* ). Po zakończeniu zadania `StartupHostedServiceHealthCheck.StartupTaskCompleted` jest ustawiony na `true` :

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

Kontrola kondycji jest zarejestrowana <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> w programie w programie `Startup.ConfigureServices` wraz z usługą hostowaną. Ponieważ usługa hostowana musi ustawić właściwość na sprawdzaniu kondycji, sprawdzanie kondycji jest również rejestrowane w kontenerze usługi ( *LivenessProbeStartup.cs* ):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

Wywołaj Sprawdzanie kondycji oprogramowania pośredniczącego w potoku przetwarzania aplikacji w programie `Startup.Configure` . W przykładowej aplikacji punkty końcowe sprawdzania kondycji są tworzone na `/health/ready` potrzeby kontroli gotowości i `/health/live` kontroli stanu na żywo. Sprawdzanie gotowości filtruje kontrolę kondycji w celu sprawdzenia kondycji za pomocą `ready` znacznika. Sprawdzenie stanu na żywo odfiltruje `StartupHostedServiceHealthCheck` przez zwrócenie `false` elementu [HealthCheckOptions. predykatu](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (Aby uzyskać więcej informacji, [zobacz Sprawdzanie kondycji filtru](#filter-health-checks)):

```csharp
app.UseHealthChecks("/health/ready", new HealthCheckOptions()
{
    Predicate = (check) => check.Tags.Contains("ready"), 
});

app.UseHealthChecks("/health/live", new HealthCheckOptions()
{
    Predicate = (_) => false
});
```

Aby uruchomić scenariusz konfiguracji gotowości/na żywo za pomocą przykładowej aplikacji, wykonaj następujące polecenie w folderze projektu w powłoce poleceń:

```dotnetcli
dotnet run --scenario liveness
```

W przeglądarce odwiedź `/health/ready` kilka razy do 15 sekund. Sprawdzanie kondycji raportuje w *złej kondycji* przez pierwsze 15 sekund. Po upływie 15 sekund punkt końcowy zgłasza w *dobrej kondycji* , co odzwierciedla ukończenie długotrwałego zadania wykonywanego przez usługę hostowaną.

W tym przykładzie tworzony jest również Wydawca sprawdzania kondycji ( <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementacja), który uruchamia pierwsze sprawdzenie gotowości z dwoma drugim opóźnieniem. Aby uzyskać więcej informacji, zapoznaj się z sekcją [Sprawdzanie kondycji wydawcy](#health-check-publisher) .

### <a name="kubernetes-example"></a>Przykład Kubernetes

Korzystanie z odrębnych gotowości i kontroli na żywo jest przydatne w środowisku, takim jak [Kubernetes](https://kubernetes.io/). W programie Kubernetes aplikacja może być wymagana do wykonywania czasochłonnych zadań uruchamiania przed zaakceptowaniem żądań, takich jak Test dostępności źródłowej bazy danych. Przy użyciu osobnych kontroli program Orchestrator może rozróżnić, czy aplikacja działa, ale nie jest jeszcze gotowa lub Jeśli uruchomienie aplikacji nie powiodło się. Aby uzyskać więcej informacji na temat gotowości i sondy na żywo w programie Kubernetes, zobacz [Konfigurowanie sondy na żywo i gotowości](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) w dokumentacji Kubernetes.

Poniższy przykład demonstruje konfigurację sondowania gotowości Kubernetes:

```
spec:
  template:
  spec:
    readinessProbe:
      # an http probe
      httpGet:
        path: /health/ready
        port: 80
      # length of time to wait for a pod to initialize
      # after pod startup, before applying health checking
      initialDelaySeconds: 30
      timeoutSeconds: 1
    ports:
      - containerPort: 80
```

## <a name="metric-based-probe-with-a-custom-response-writer"></a>Sondowanie oparte na metrykach z niestandardowym modułem zapisywania odpowiedzi

Przykładowa aplikacja demonstruje kontrolę kondycji pamięci za pomocą modułu zapisywania odpowiedzi niestandardowych.

`MemoryHealthCheck` zgłasza stan złej kondycji, jeśli aplikacja używa więcej niż danego progu pamięci (1 GB w przykładowej aplikacji). <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>Zawiera informacje dotyczące modułu wyrzucania elementów bezużytecznych (GC) dla aplikacji ( *MemoryHealthCheck.cs* ):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

Zarejestruj usługi sprawdzania kondycji <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> w programie w programie `Startup.ConfigureServices` . Zamiast włączać kontrolę kondycji przez przekazanie jej do programu <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> `MemoryHealthCheck` jest zarejestrowany jako usługa. Wszystkie <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> zarejestrowane usługi są dostępne dla usług sprawdzania kondycji i oprogramowania pośredniczącego. Zalecamy rejestrację usług sprawdzania kondycji jako usług pojedynczych.

W przykładowej aplikacji ( *CustomWriterStartup.cs* ):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

Wywołaj Sprawdzanie kondycji oprogramowania pośredniczącego w potoku przetwarzania aplikacji w programie `Startup.Configure` . `WriteResponse`Obiekt delegowany jest dostarczany do `ResponseWriter` właściwości w celu wygenerowania NIESTANDARDOWEJ odpowiedzi JSON po zakończeniu sprawdzania kondycji:

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        // This custom writer formats the detailed status as JSON.
        ResponseWriter = WriteResponse
    });
}
```

`WriteResponse`Metoda formatuje `CompositeHealthCheckResult` do obiektu JSON i zwraca dane wyjściowe JSON dla odpowiedzi kontroli kondycji:

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse)]

Aby uruchomić sondę opartą na metrykach z niestandardowymi danymi wyjściowymi modułu zapisywania odpowiedzi przy użyciu przykładowej aplikacji, wykonaj następujące polecenie w folderze projektu w powłoce poleceń:

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) obejmuje scenariusze sprawdzania kondycji oparte na metrykach, w tym magazyn dyskowy i maksymalną liczbę sprawdzeń na żywo.
>
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) nie jest obsługiwana przez firmę Microsoft lub nie są przez nią obsługiwane.

## <a name="filter-by-port"></a>Filtruj według portu

Wywołanie <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> z portem ogranicza liczbę żądań sprawdzania kondycji do określonego portu. Jest to zwykle używane w środowisku kontenera w celu udostępnienia portu usług monitorowania.

Przykładowa aplikacja konfiguruje port przy użyciu [zmiennej środowiskowej dostawcy konfiguracji](xref:fundamentals/configuration/index#environment-variables-configuration-provider). Port jest ustawiany w *launchSettings.jsw* pliku i przeszedł do dostawcy konfiguracji za pośrednictwem zmiennej środowiskowej. Należy również skonfigurować serwer do nasłuchiwania żądań na porcie zarządzania.

Aby użyć przykładowej aplikacji do zademonstrowania konfiguracji portów zarządzania, Utwórz *launchSettings.jsw* pliku w folderze *Właściwości* .

Następujące *Właściwości/launchSettings.jsw* pliku w przykładowej aplikacji nie są uwzględnione w plikach projektu przykładowej aplikacji i muszą zostać utworzone ręcznie:

```json
{
  "profiles": {
    "SampleApp": {
      "commandName": "Project",
      "commandLineArgs": "",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development",
        "ASPNETCORE_URLS": "http://localhost:5000/;http://localhost:5001/",
        "ASPNETCORE_MANAGEMENTPORT": "5001"
      },
      "applicationUrl": "http://localhost:5000/"
    }
  }
}
```

Zarejestruj usługi sprawdzania kondycji <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> w programie w programie `Startup.ConfigureServices` . Wywołanie <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> określające port zarządzania ( *ManagementPortStartup.cs* ):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ManagementPortStartup.cs?name=snippet1&highlight=17)]

> [!NOTE]
> Możesz uniknąć tworzenia *launchSettings.jsw* pliku w przykładowej aplikacji, ustawiając adresy URL i port zarządzania jawnie w kodzie. W *program.cs* , w którym <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> został utworzony, Dodaj wywołanie do <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> i podaj normalny punkt końcowy odpowiedzi aplikacji oraz punkt końcowy portu zarządzania. W *ManagementPortStartup.cs* <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> , gdzie jest wywoływana, określ port zarządzania jawnie.
>
> *Program.cs* :
>
> ```csharp
> return new WebHostBuilder()
>     .UseConfiguration(config)
>     .UseUrls("http://localhost:5000/;http://localhost:5001/")
>     .ConfigureLogging(builder =>
>     {
>         builder.SetMinimumLevel(LogLevel.Trace);
>         builder.AddConfiguration(config);
>         builder.AddConsole();
>     })
>     .UseKestrel()
>     .UseStartup(startupType)
>     .Build();
> ```
>
> *ManagementPortStartup.cs* :
>
> ```csharp
> app.UseHealthChecks("/health", port: 5001);
> ```

Aby uruchomić scenariusz konfiguracji portu zarządzania przy użyciu przykładowej aplikacji, wykonaj następujące polecenie w folderze projektu w powłoce poleceń:

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a>Dystrybucja biblioteki kontroli kondycji

Aby rozpowszechnić kontrolę kondycji jako bibliotekę:

1. Napisz kontrolę kondycji, która implementuje <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interfejs jako autonomiczną klasę. Klasa może polegać na [iniekcji zależności (di)](xref:fundamentals/dependency-injection), aktywacji typu i [nazwanych opcjach](xref:fundamentals/configuration/options) w celu uzyskania dostępu do danych konfiguracyjnych.

   W logice kontroli kondycji `CheckHealthAsync` :

   * `data1` i `data2` są używane w metodzie do uruchamiania logiki sprawdzania kondycji sondy.
   * `AccessViolationException` jest obsługiwane.

   Gdy wystąpi <xref:System.AccessViolationException> , <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> zostaje zwrócony z, <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> Aby umożliwić użytkownikom Konfigurowanie stanu niepowodzenia sprawdzania kondycji.

   ```csharp
   using System;
   using System.Threading;
   using System.Threading.Tasks;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public class ExampleHealthCheck : IHealthCheck
   {
       private readonly string _data1;
       private readonly int? _data2;

       public ExampleHealthCheck(string data1, int? data2)
       {
           _data1 = data1 ?? throw new ArgumentNullException(nameof(data1));
           _data2 = data2 ?? throw new ArgumentNullException(nameof(data2));
       }

       public async Task<HealthCheckResult> CheckHealthAsync(
           HealthCheckContext context, CancellationToken cancellationToken)
       {
           try
           {
               return HealthCheckResult.Healthy();
           }
           catch (AccessViolationException ex)
           {
               return new HealthCheckResult(
                   context.Registration.FailureStatus,
                   description: "An access violation occurred during the check.",
                   exception: ex,
                   data: null);
           }
       }
   }
   ```

1. Napisz metodę rozszerzenia z parametrami, które są używane przez zużywaną aplikację w jej `Startup.Configure` metodzie. W poniższym przykładzie przyjęto założenie, że następująca sygnatura metody kontroli kondycji:

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   Poprzednia sygnatura wskazuje, że `ExampleHealthCheck` wymaga dodatkowych danych do przetworzenia logiki sondowania sprawdzania kondycji. Dane są przekazywane delegatom używanym do tworzenia wystąpienia kontroli kondycji, gdy Sprawdzanie kondycji jest zarejestrowane za pomocą metody rozszerzenia. W poniższym przykładzie obiekt wywołujący określa opcjonalne:

   * Nazwa sprawdzania kondycji ( `name` ). Jeśli `null` `example_health_check` jest używany.
   * punkt danych ciągu dla kontroli kondycji ( `data1` ).
   * punkt danych liczb całkowitych dla kontroli kondycji ( `data2` ). Jeśli `null` `1` jest używany.
   * stan niepowodzenia ( <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus> ). Wartość domyślna to `null`. Jeśli `null` dla stanu błędu zostanie zgłoszona wartość [HealthStatus. w złej kondycji](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) .
   * Tagi ( `IEnumerable<string>` ).

   ```csharp
   using System.Collections.Generic;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public static class ExampleHealthCheckBuilderExtensions
   {
       const string DefaultName = "example_health_check";

       public static IHealthChecksBuilder AddExampleHealthCheck(
           this IHealthChecksBuilder builder,
           string name = default,
           string data1,
           int data2 = 1,
           HealthStatus? failureStatus = default,
           IEnumerable<string> tags = default)
       {
           return builder.Add(new HealthCheckRegistration(
               name ?? DefaultName,
               sp => new ExampleHealthCheck(data1, data2),
               failureStatus,
               tags));
       }
   }
   ```

## <a name="health-check-publisher"></a>Wydawca kontroli kondycji

Po <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> dodaniu do kontenera usługi system kontroli kondycji okresowo wykonuje sprawdzanie kondycji i wywołuje `PublishAsync` wynik. Jest to przydatne w scenariuszu systemu monitorowania kondycji opartej na wypychaniu, który oczekuje, że każdy proces będzie okresowo wywoływał system monitorowania w celu określenia kondycji.

<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>Interfejs ma jedną metodę:

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> Zezwól na ustawienie:

* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>: Początkowe opóźnienie stosowane po uruchomieniu aplikacji przed wykonaniem <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> wystąpień. Opóźnienie jest stosowane raz podczas uruchamiania i nie ma zastosowania do kolejnych iteracji. Wartość domyślna to pięć sekund.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>: Okres <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> wykonania. Wartość domyślna to 30 sekund.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>: Jeśli <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> jest `null` (domyślnie), usługa wydawcy sprawdzania kondycji uruchamia wszystkie zarejestrowane kontrole kondycji. Aby uruchomić podzestaw kontroli kondycji, należy określić funkcję, która filtruje zestaw kontroli. Predykat jest oceniany w każdym okresie.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>: Limit czasu wykonywania kontroli kondycji dla wszystkich <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> wystąpień. Użyj <xref:System.Threading.Timeout.InfiniteTimeSpan> , aby wykonać bez limitu czasu. Wartość domyślna to 30 sekund.

> [!WARNING]
> W wersji 2,2 ASP.NET Core ustawienie <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> nie jest honorowane przez <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementację; ustawia wartość <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> . Ten problem został rozwiązany w ASP.NET Core 3,0.

W przykładowej aplikacji `ReadinessPublisher` jest <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementacją. Stan sprawdzania kondycji jest rejestrowany dla każdego sprawdzenia jako:

* Informacja ( <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> ), jeśli stan kontroli kondycji to <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy> .
* Error ( <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*> ), jeśli stan ma wartość <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> lub <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy> .

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

W `LivenessProbeStartup` przykładzie przykładowej aplikacji `StartupHostedService` sprawdzanie gotowości ma dwa drugie opóźnienie uruchamiania i sprawdza, co 30 sekund. Aby uaktywnić <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementację, przykład rejestruje się `ReadinessPublisher` jako usługa pojedyncza w kontenerze [iniekcji zależności (di)](xref:fundamentals/dependency-injection) :

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices&highlight=12-17,28)]

> [!NOTE]
> Następujące obejście umożliwia dodanie <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> wystąpienia do kontenera usługi, gdy co najmniej jedna inna usługa hostowana została już dodana do aplikacji. To obejście nie będzie wymagane w ASP.NET Core 3,0.
>
> ```csharp
> private const string HealthCheckServiceAssembly =
>     "Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherHostedService";
>
> services.TryAddEnumerable(
>     ServiceDescriptor.Singleton(typeof(IHostedService),
>         typeof(HealthCheckPublisherOptions).Assembly
>             .GetType(HealthCheckServiceAssembly)));
> ```

> [!NOTE]
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) obejmuje wydawców dla kilku systemów, w tym [Application Insights](/azure/application-insights/app-insights-overview).
>
> [AspNetCore. Diagnostics. HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) nie jest obsługiwana przez firmę Microsoft lub nie są przez nią obsługiwane.

## <a name="restrict-health-checks-with-mapwhen"></a>Ogranicz kontrolę kondycji za pomocą MapWhen

Użyj, <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> Aby warunkowo rozgałęziać potok żądania dla punktów końcowych sprawdzania kondycji.

W poniższym przykładzie `MapWhen` rozgałęzienie potoku żądania w celu aktywowania kontroli kondycji oprogramowania pośredniczącego w przypadku otrzymania żądania GET dla `api/HealthCheck` punktu końcowego:

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseMvc();
```

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/middleware/index#use-run-and-map>.

::: moniker-end
