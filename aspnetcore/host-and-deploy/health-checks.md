---
title: Kontrole zdrowia w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak skonfigurować kontrole kondycji ASP.NET podstawowej infrastruktury, takiej jak aplikacje i bazy danych.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 12/15/2019
uid: host-and-deploy/health-checks
ms.openlocfilehash: 314e55c818cddf1dad2e3ec74d4d1e041ce7366f
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664886"
---
# <a name="health-checks-in-aspnet-core"></a>Kontrole zdrowia w ASP.NET Core

Przez [Glenn Condron](https://github.com/glennc)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core oferuje oprogramowanie pośredniczące i biblioteki health checks do raportowania kondycji składników infrastruktury aplikacji.

Kontrole kondycji są udostępniane przez aplikację jako punkty końcowe HTTP. Punkty końcowe sprawdzania kondycji można skonfigurować dla różnych scenariuszy monitorowania w czasie rzeczywistym:

* Sondy kondycji mogą być używane przez koordynatorów kontenerów i moduły równoważenia obciążenia w celu sprawdzenia stanu aplikacji. Na przykład orchestrator kontenera może odpowiedzieć na sprawdzanie kondycji wada, zatrzymując wdrożenie stopniowe lub ponowne uruchomienie kontenera. Moduł równoważenia obciążenia może reagować na złej aplikacji przez routing ruchu z dala od wystąpienia awarii do wystąpienia w dobrej kondycji.
* Korzystanie z pamięci, dysku i innych zasobów serwera fizycznego można monitorować pod kątem stanu zdrowia.
* Kontrole kondycji można przetestować zależności aplikacji, takich jak bazy danych i punktów końcowych usługi zewnętrznej, aby potwierdzić dostępność i normalne funkcjonowanie.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

Przykładowa aplikacja zawiera przykłady scenariuszy opisanych w tym temacie. Aby uruchomić przykładową aplikację dla danego scenariusza, użyj polecenia [dotnet run](/dotnet/core/tools/dotnet-run) z folderu projektu w powłoce poleceń. Zobacz przykładowy plik *README.md* aplikacji i opisy scenariuszy w tym temacie, aby uzyskać szczegółowe informacje na temat korzystania z przykładowej aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

Kontrole kondycji są zwykle używane z zewnętrzną usługą monitorowania lub koordynatorem kontenerów w celu sprawdzenia stanu aplikacji. Przed dodaniem kontroli kondycji do aplikacji, zdecydować, który system monitorowania do użycia. System monitorowania określa, jakie typy kontroli kondycji do utworzenia i jak skonfigurować ich punktów końcowych.

Pakiet [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) odwołuje się niejawnie do aplikacji ASP.NET Core. Aby przeprowadzić kontrole kondycji przy użyciu programu Entity Framework Core, dodaj odwołanie do pakietu [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore.](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore)

Przykładowa aplikacja udostępnia kod startowy, aby zademonstrować testy kondycji dla kilku scenariuszy. Scenariusz [sondy bazy danych](#database-probe) sprawdza kondycję połączenia z bazą danych przy użyciu [aspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks). Scenariusz [sondy DbContext](#entity-framework-core-dbcontext-probe) sprawdza bazę `DbContext`danych przy użyciu ef core . Aby eksplorować scenariusze bazy danych, przykładowa aplikacja:

* Tworzy bazę danych i udostępnia jej parametry połączenia w pliku *appsettings.json.*
* Ma następujące odwołania do pakietu w pliku projektu:
  * [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) nie jest obsługiwany ani obsługiwany przez firmę Microsoft.

Inny scenariusz sprawdzania kondycji pokazuje, jak filtrować kontrole kondycji do portu zarządzania. Przykładowa aplikacja wymaga utworzenia pliku *Properties/launchSettings.json,* który zawiera adres URL zarządzania i port zarządzania. Aby uzyskać więcej informacji, zobacz [sekcję Filtruj według portów.](#filter-by-port)

## <a name="basic-health-probe"></a>Podstawowa sonda kondycji

W przypadku wielu aplikacji podstawowa konfiguracja sondy kondycji, która zgłasza dostępność aplikacji do przetwarzania żądań *(żywotność)* jest wystarczająca do odnajdowania stanu aplikacji.

Podstawowa konfiguracja rejestruje usługi sprawdzania kondycji i wywołuje oprogramowanie pośredniczące kontroli kondycji, aby odpowiedzieć w punkcie końcowym adresu URL z odpowiedzią kondycji. Domyślnie nie są rejestrowane żadne określone kontrole kondycji w celu przetestowania określonej zależności lub podsystemu. Aplikacja jest uważana za w dobrej kondycji, jeśli jest w stanie odpowiedzieć na adres URL punktu końcowego kondycji. Domyślny moduł zapisujący odpowiedzi<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>zapisuje stan ( ) jako odpowiedź w postaci zwykłego tekstu z powrotem do klienta, wskazując stan [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) lub [HealthStatus.Unhealthy.](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)

Zarejestruj usługi kontroli <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> `Startup.ConfigureServices`kondycji w pliku . Utwórz punkt końcowy sprawdzania `MapHealthChecks` `Startup.Configure`kondycji, wywołując program .

W przykładowej aplikacji punkt końcowy oceny `/health` kondycji jest tworzony w (*BasicStartup.cs*):

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

Aby uruchomić podstawowy scenariusz konfiguracji przy użyciu przykładowej aplikacji, wykonaj następujące polecenie z folderu projektu w powłoce poleceń:

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a>Przykład platformy Docker

[Docker](xref:host-and-deploy/docker/index) oferuje wbudowaną `HEALTHCHECK` dyrektywę, która może służyć do sprawdzania stanu aplikacji, która używa podstawowej konfiguracji sprawdzania kondycji:

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a>Tworzenie kontroli kondycji

Kontrole kondycji są tworzone <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> przez implementowanie interfejsu. Metoda <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> zwraca, który wskazuje kondycję `Degraded`jako `Unhealthy` `Healthy`, lub . Wynik jest zapisywany jako odpowiedź w postaci zwykłego tekstu z konfigurowalnym kodem stanu (konfiguracja jest opisana w sekcji [Opcje sprawdzania kondycji).](#health-check-options) <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>może również zwrócić opcjonalne pary klucz-wartość.

W `ExampleHealthCheck` poniższej klasie przedstawiono układ kontroli kondycji. Logika kontroli kondycji jest `CheckHealthAsync` umieszczana w metodzie. W poniższym przykładzie ustawia `healthCheckResultHealthy`zmienną manekina, na . `true` Jeśli wartość `healthCheckResultHealthy` jest ustawiona na `false`, [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) stan jest zwracany.

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

## <a name="register-health-check-services"></a>Zarejestruj usługi sprawdzania kondycji

Typ `ExampleHealthCheck` jest dodawany do <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> usług `Startup.ConfigureServices`kontroli kondycji z w:

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

Przeciążenie <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> pokazane w poniższym przykładzie<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>ustawia stan błędu ( ) do raportu, gdy sprawdzanie kondycji zgłasza błąd. Jeśli stan błędu jest `null` ustawiony na (domyślnie), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) jest zgłaszane. To przeciążenie jest przydatnym scenariuszem dla autorów biblioteki, w którym stan awarii wskazany przez bibliotekę jest wymuszany przez aplikację, gdy wystąpi błąd sprawdzania kondycji, jeśli implementacja sprawdzania kondycji honoruje to ustawienie.

*Tagi* mogą służyć do filtrowania kontroli kondycji (opisane dalej w [sekcji Filtrowanie kontroli kondycji).](#filter-health-checks)

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>można również wykonać funkcję lambda. W poniższym przykładzie nazwa sprawdzania `Example` kondycji jest określona jako i sprawdzanie zawsze zwraca stan dobrej kondycji:

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

Wywołanie <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddTypeActivatedCheck*> przekazywania argumentów do implementacji sprawdzania kondycji. W poniższym `TestHealthCheckWithArgs` przykładzie akceptuje całkowitą i ciąg do <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> użycia, gdy jest wywoływana:

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

`TestHealthCheckWithArgs`jest rejestrowany `AddTypeActivatedCheck` przez wywołanie z całkowitej liczby i ciąg przekazany do implementacji:

```csharp
services.AddHealthChecks()
    .AddTypeActivatedCheck<TestHealthCheckWithArgs>(
        "test", 
        failureStatus: HealthStatus.Degraded, 
        tags: new[] { "example" }, 
        args: new object[] { 5, "string" });
```

## <a name="use-health-checks-routing"></a>Korzystanie z routingu kontroli kondycji

W `Startup.Configure`programie `MapHealthChecks` wywołaj konstruktora punktów końcowych z adresem URL punktu końcowego lub ścieżką względną:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

### <a name="require-host"></a>Wymagaj hosta

Wywołanie, `RequireHost` aby określić jeden lub więcej dozwolonych hostów dla punktu końcowego sprawdzania kondycji. Hosty powinny być Unicode, a nie punycode i może zawierać port. Jeśli kolekcja nie jest dostarczana, każdy host jest akceptowany.

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireHost("www.contoso.com:5001");
});
```

Aby uzyskać więcej informacji, zobacz [sekcję Filtruj według portów.](#filter-by-port)

### <a name="require-authorization"></a>Wymagaj autoryzacji

Wywołanie `RequireAuthorization` uruchomienia oprogramowania pośredniczącego autoryzacji w punkcie końcowym żądania sprawdzenia kondycji. Przeciążenie `RequireAuthorization` akceptuje co najmniej jedną zasadę autoryzacji. Jeśli zasady nie są dostarczane, używana jest domyślna zasada autoryzacji.

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireAuthorization();
});
```

### <a name="enable-cross-origin-requests-cors"></a>Włączanie żądań Cross-Origin (CORS)

Chociaż ręczne wykonywanie kontroli kondycji z przeglądarki nie jest typowym scenariuszem użycia, oprogramowanie pośredniczące CORS można włączyć, wywołując `RequireCors` punkty końcowe kontroli kondycji. Przeciążenie `RequireCors` akceptuje delegata konstruktora`CorsPolicyBuilder`zasad CORS ( ) lub nazwę zasad. Jeśli zasady nie są podane, używana jest domyślna zasada CORS. Aby uzyskać więcej informacji, zobacz <xref:security/cors>.

## <a name="health-check-options"></a>Opcje sprawdzania kondycji

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions>możliwość dostosowania zachowania sprawdzania kondycji:

* [Sprawdzanie kondycji filtrowania](#filter-health-checks)
* [Dostosowywanie kodu stanu HTTP](#customize-the-http-status-code)
* [Pomijanie nagłówków pamięci podręcznej](#suppress-cache-headers)
* [Dostosowywanie danych wyjściowych](#customize-output)

### <a name="filter-health-checks"></a>Sprawdzanie kondycji filtrowania

Domyślnie oprogramowanie pośredniczące health checks uruchamia wszystkie zarejestrowane kontrole kondycji. Aby uruchomić podzbiór kontroli kondycji, podaj funkcję, która zwraca wartość logiczną <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> do opcji. W poniższym przykładzie `Bar` sprawdzanie kondycji jest odfiltrowywało według jego znacznika (`bar_tag`) w instrukcji warunkowej funkcji, gdzie `true` jest zwracane tylko wtedy, gdy <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> właściwość sprawdzania kondycji jest zgodna `foo_tag` lub: `baz_tag`

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

W `Startup.Configure`, `Predicate` odfiltruje "Bar" kontroli kondycji. Tylko Foo i Baz wykonać.:

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

Służy <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> do dostosowywania mapowania stanu kondycji do kodów stanu HTTP. Następujące <xref:Microsoft.AspNetCore.Http.StatusCodes> przypisania są wartościami domyślnymi używanymi przez oprogramowanie pośredniczące. Zmień wartości kodu stanu, aby spełnić wymagania.

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

### <a name="suppress-cache-headers"></a>Pomijanie nagłówków pamięci podręcznej

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses>określa, czy oprogramowanie pośredniczące kontroli kondycji dodaje nagłówki HTTP do odpowiedzi sondy, aby zapobiec buforowaniu odpowiedzi. Jeśli wartość `false` jest (domyślna), oprogramowanie pośredniczące `Cache-Control` `Expires`ustawia `Pragma` lub zastępuje , i nagłówki, aby zapobiec buforowaniu odpowiedzi. Jeśli wartość `true`jest , oprogramowanie pośredniczące nie modyfikuje nagłówki pamięci podręcznej odpowiedzi.

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

### <a name="customize-output"></a>Dostosowywanie danych wyjściowych

W `Startup.Configure`, ustaw [HealthCheckOptions.ResponseWriter](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter) opcji delegata do pisania odpowiedzi:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
});
```

Domyślny delegat zapisuje minimalną odpowiedź w postaci zwykłego tekstu z wartością ciągu [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status). Następujące delegatów niestandardowych danych wyjściowych niestandardowej odpowiedzi JSON.

Pierwszy przykład z przykładowej aplikacji pokazuje, jak używać: <xref:System.Text.Json?displayProperty=fullName>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse_SystemTextJson)]

Drugi przykład pokazuje, jak korzystać z [Newtonsoft.Json:](https://www.nuget.org/packages/Newtonsoft.Json/)

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse_NewtonSoftJson)]

W przykładowej aplikacji skomentuj `SYSTEM_TEXT_JSON` [dyrektywę preprocesora](xref:index#preprocessor-directives-in-sample-code) w `Newtonsoft.Json` *CustomWriterStartup.cs,* aby włączyć wersję programu `WriteResponse`.

Interfejs API kontroli kondycji nie zapewnia wbudowanej obsługi złożonych formatów zwracanych JSON, ponieważ format jest specyficzny dla wybranego systemu monitorowania. Dostosuj odpowiedź w poprzednich przykładach w razie potrzeby. Aby uzyskać więcej informacji na `System.Text.Json`temat serializacji JSON za pomocą , zobacz [Jak serializować i deserialize JSON w .NET](/dotnet/standard/serialization/system-text-json-how-to).

## <a name="database-probe"></a>Sonda bazy danych

Sprawdzanie kondycji można określić kwerendę bazy danych do uruchomienia jako test logiczny, aby wskazać, czy baza danych odpowiada normalnie.

Przykładowa aplikacja używa [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), biblioteki sprawdzania kondycji dla aplikacji ASP.NET Core, do sprawdzania kondycji w bazie danych programu SQL Server. `AspNetCore.Diagnostics.HealthChecks`wykonuje kwerendę do `SELECT 1` bazy danych, aby potwierdzić, że połączenie z bazą danych jest w dobrej kondycji.

> [!WARNING]
> Podczas sprawdzania połączenia bazy danych z kwerendą wybierz kwerendę, która zwraca szybko. Podejście zapytania ryzykuje przeciążenie bazy danych i obniżenie jej wydajności. W większości przypadków uruchamianie kwerendy testowej nie jest konieczne. Wystarczy jedynie pomyślne połączenie z bazą danych. Jeśli okaże się konieczne uruchomienie kwerendy, wybierz proste `SELECT 1`zapytanie SELECT, takie jak .

Dołącz odwołanie do pakietu [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).

Podaj prawidłowy ciąg połączenia bazy danych w pliku *appsettings.json* przykładowej aplikacji. Aplikacja korzysta z bazy danych `HealthCheckSample`programu SQL Server o nazwie:

[!code-json[](health-checks/samples/3.x/HealthChecksSample/appsettings.json?highlight=3)]

Zarejestruj usługi kontroli <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> `Startup.ConfigureServices`kondycji w pliku . Przykładowa aplikacja `AddSqlServer` wywołuje metodę z ciągiem połączenia bazy danych *(DbHealthStartup.cs):*

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

Punkt końcowy sprawdzania kondycji jest `MapHealthChecks` `Startup.Configure`tworzony przez wywołanie w:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

Aby uruchomić scenariusz sondy bazy danych przy użyciu przykładowej aplikacji, wykonaj następujące polecenie z folderu projektu w powłoce poleceń:

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) nie jest obsługiwany ani obsługiwany przez firmę Microsoft.

## <a name="entity-framework-core-dbcontext-probe"></a>Sonda Core DbContext programu Entity Framework

Sprawdzanie `DbContext` potwierdza, że aplikacja może komunikować się z bazą danych skonfigurowaną dla ef core `DbContext`. Sprawdzanie `DbContext` jest obsługiwane w aplikacjach, które:

* Użyj [core (Entity Framework) Core](/ef/core/).
* Dołącz odwołanie do pakietu [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).

`AddDbContextCheck<TContext>`rejestruje kontrolę kondycji dla `DbContext`. Jest `DbContext` dostarczany jako `TContext` do metody. Przeciążenie jest dostępne do skonfigurowania stanu błędu, tagów i niestandardowej kwerendy testowej.

Domyślnie:

* Wywołanie `DbContextHealthCheck` metody EF `CanConnectAsync` Core. Można dostosować, jaka operacja jest uruchamiana podczas sprawdzania kondycji przy użyciu `AddDbContextCheck` przeciążenia metody.
* Nazwa sprawdzania kondycji jest nazwą `TContext` typu.

W przykładowej `AppDbContext` aplikacji, `AddDbContextCheck` jest dostarczany i `Startup.ConfigureServices` zarejestrowany jako usługa w (*DbContextHealthStartup.cs*):

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

Punkt końcowy sprawdzania kondycji jest `MapHealthChecks` `Startup.Configure`tworzony przez wywołanie w:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

Aby uruchomić `DbContext` scenariusz sondowania przy użyciu przykładowej aplikacji, upewnij się, że baza danych określona przez parametry połączenia nie istnieje w wystąpieniu programu SQL Server. Jeśli baza danych istnieje, usuń ją.

Wykonaj następujące polecenie z folderu projektu w powłoce poleceń:

```dotnetcli
dotnet run --scenario dbcontext
```

Po uruchomieniu aplikacji sprawdź stan kondycji, wysyłając żądanie `/health` do punktu końcowego w przeglądarce. Baza danych `AppDbContext` i nie istnieje, więc aplikacja zapewnia następującą odpowiedź:

```
Unhealthy
```

Wyzwalanie przykładowej aplikacji, aby utworzyć bazę danych. Złożyć wniosek `/createdatabase`do . Aplikacja odpowiada:

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

Złożyć żądanie `/health` do punktu końcowego. Istnieje baza danych i kontekst, więc aplikacja odpowiada:

```
Healthy
```

Wyzwalanie przykładowej aplikacji, aby usunąć bazę danych. Złożyć wniosek `/deletedatabase`do . Aplikacja odpowiada:

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

Złożyć żądanie `/health` do punktu końcowego. Aplikacja zapewnia w złej kondycji odpowiedzi:

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a>Oddzielne sondy gotowości i żywotności

W niektórych scenariuszach hostingu używana jest para kontroli kondycji, które rozróżniają dwa stany aplikacji:

* Aplikacja działa, ale nie jest jeszcze gotowa do odbierania żądań. Ten stan jest *gotowość*aplikacji .
* Aplikacja działa i odpowiada na żądania. Ten stan jest *żywość*aplikacji .

Sprawdzanie gotowości zwykle wykonuje bardziej obszerny i czasochłonny zestaw kontroli, aby ustalić, czy wszystkie podsystemy i zasoby aplikacji są dostępne. Sprawdzanie żywotności wykonuje tylko szybkie sprawdzenie, aby ustalić, czy aplikacja jest dostępna do przetwarzania żądań. Po przejściu przez aplikację kontroli gotowości, nie ma potrzeby, aby obciążyć&mdash;aplikację dalej z drogim zestawem kontroli gotowości dalsze kontrole wymagają tylko sprawdzenia, czy nie ma żywotności.

Przykładowa aplikacja zawiera sprawdzanie kondycji, aby zgłosić zakończenie długotrwałego zadania uruchamiania w [usłudze hosted](xref:fundamentals/host/hosted-services). Udostępnia `StartupHostedServiceHealthCheck` właściwość, `StartupTaskCompleted`że usługa hostowana można `true` ustawić po zakończeniu jej długotrwałe zadanie *(StartupHostedServiceHealthCheck.cs):*

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

Długotrwałe zadanie w tle jest uruchamiane przez [usługę hostowana](xref:fundamentals/host/hosted-services) *(Usługi/StartupHostedService).* Po zakończeniu zadania, `StartupHostedServiceHealthCheck.StartupTaskCompleted` jest ustawiony `true`na:

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

Kontrola kondycji jest <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> zarejestrowana w `Startup.ConfigureServices` wraz z usługą hosta. Ponieważ usługa hostowana musi ustawić właściwość na kontroli kondycji, kontrola kondycji jest również zarejestrowana w kontenerze serwisowym (*LivenessProbeStartup.cs):*

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

Punkt końcowy sprawdzania kondycji jest `MapHealthChecks` `Startup.Configure`tworzony przez wywołanie w . W przykładowej aplikacji punkty końcowe sprawdzania kondycji są tworzone w:

* `/health/ready`do kontroli gotowości. Sprawdzanie gotowości filtruje kontrole kondycji do sprawdzenia `ready` kondycji za pomocą tagu.
* `/health/live`do sprawdzania żywotności. Sprawdzanie żywotności filtruje `StartupHostedServiceHealthCheck` przez `false` powrót w [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (aby uzyskać więcej informacji, zobacz [filtrowanie kontroli kondycji)](#filter-health-checks)

W poniższym przykładzie kodu:

* Kontrola gotowości używa wszystkich zarejestrowanych czeków z tagiem "ready".
* Wyklucza `Predicate` wszystkie kontrole i zwraca 200-Ok.

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

Aby uruchomić scenariusz konfiguracji gotowości/żywotności przy użyciu przykładowej aplikacji, wykonaj następujące polecenie z folderu projektu w powłoce poleceń:

```dotnetcli
dotnet run --scenario liveness
```

W przeglądarce `/health/ready` odwiedź kilka razy, aż minęło 15 sekund. Kontrola kondycji raportuje *wzdrowy sposób* przez pierwsze 15 sekund. Po 15 sekundach punkt końcowy raportuje *w dobrej kondycji*, co odzwierciedla ukończenie długotrwałego zadania przez hostowany serwis.

W tym przykładzie tworzy<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> również Health Check Publisher (implementacja), który uruchamia pierwsze sprawdzanie gotowości z dwusekundowym opóźnieniem. Aby uzyskać więcej informacji, zobacz sekcję [Wydawca sprawdzania kondycji.](#health-check-publisher)

### <a name="kubernetes-example"></a>Przykład Kubernetes

Korzystanie z oddzielnych kontroli gotowości i żywotności jest przydatne w środowisku takim jak [Kubernetes](https://kubernetes.io/). W umięsierze aplikacja może być wymagana do wykonywania czasochłonnych prac podczas uruchamiania przed zaakceptowaniem żądań, takich jak test podstawowej dostępności bazy danych. Za pomocą oddzielnych kontroli umożliwia orchestrator odróżnić, czy aplikacja działa, ale nie jest jeszcze gotowy lub jeśli aplikacja nie powiodło się uruchomić. Aby uzyskać więcej informacji na temat gotowości i sondy żywotności w umięsienia, zobacz [Konfigurowanie sondy żywotności i gotowości](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) w dokumentacji programu Kubernetes.

W poniższym przykładzie pokazano konfigurację sondy gotowości Kubernetes:

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

## <a name="metric-based-probe-with-a-custom-response-writer"></a>Sonda oparta na metrykach z modułem zapisu odpowiedzi niestandardowej

Przykładowa aplikacja pokazuje sprawdzanie kondycji pamięci za pomocą modułu zapisującego odpowiedzi niestandardowej.

`MemoryHealthCheck`zgłasza stan obniżony, jeśli aplikacja używa więcej niż danego progu pamięci (1 GB w przykładowej aplikacji). Informacje <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> o odpadach odśmiecania (GC) dla aplikacji *(MemoryHealthCheck.cs):*

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

Zarejestruj usługi kontroli <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> `Startup.ConfigureServices`kondycji w pliku . Zamiast włączać sprawdzanie kondycji, przekazując <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>go `MemoryHealthCheck` do , jest zarejestrowany jako usługa. Wszystkie <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> zarejestrowane usługi są dostępne dla usług kontroli kondycji i oprogramowania pośredniczącego. Zalecamy rejestrowanie usług kontroli kondycji jako usług Singleton.

W *CustomWriterStartup.cs* przykładowej aplikacji:

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

Punkt końcowy sprawdzania kondycji jest `MapHealthChecks` `Startup.Configure`tworzony przez wywołanie w . Pełnomocnik `WriteResponse` jest dostarczany do <Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> właściwości do wysuczenia niestandardowej odpowiedzi JSON podczas wykonywania sprawdzania kondycji:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
}
```

Delegat `WriteResponse` formatuje `CompositeHealthCheckResult` do obiektu JSON i daje wyjście JSON dla odpowiedzi na sprawdzenie kondycji. Aby uzyskać więcej informacji, zobacz sekcję [Dostosowywanie danych wyjściowych.](#customize-output)

Aby uruchomić sondę opartą na metrykach z niestandardowym wyjściem modułu zapisującego odpowiedzi przy użyciu przykładowej aplikacji, wykonaj następujące polecenie z folderu projektu w powłoce poleceń:

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) zawiera scenariusze sprawdzania kondycji oparte na metrykach, w tym sprawdzanie żywotności dysku i maksymalnej wartości.
>
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) nie jest obsługiwany ani obsługiwany przez firmę Microsoft.

## <a name="filter-by-port"></a>Filtrowanie według portów

`RequireHost` Wywołanie `MapHealthChecks` z wzorcem adresu URL, który określa port, aby ograniczyć żądania sprawdzania kondycji do określonego portu. Jest to zwykle używane w środowisku kontenera, aby udostępnić port dla usług monitorowania.

Przykładowa aplikacja konfiguruje port przy użyciu [dostawcy konfiguracji zmiennych środowiskowych](xref:fundamentals/configuration/index#environment-variables-configuration-provider). Port jest ustawiony w pliku *launchSettings.json* i przekazywany do dostawcy konfiguracji za pośrednictwem zmiennej środowiskowej. Należy również skonfigurować serwer do nasłuchiwać żądań na porcie zarządzania.

Aby użyć przykładowej aplikacji do zademonstrowania konfiguracji portu zarządzania, utwórz plik *launchSettings.json* w folderze *Właściwości.*

Następujący plik *Properties/launchSettings.json* w przykładowej aplikacji nie jest uwzględniony w przykładowych plikach projektu aplikacji i musi zostać utworzony ręcznie:

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

Zarejestruj usługi kontroli <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> `Startup.ConfigureServices`kondycji w pliku . Utwórz punkt końcowy sprawdzania `MapHealthChecks` `Startup.Configure`kondycji, wywołując program .

W przykładowej aplikacji wywołanie `RequireHost` punktu końcowego w `Startup.Configure` określa port zarządzania z konfiguracji:

```csharp
endpoints.MapHealthChecks("/health")
    .RequireHost($"*:{Configuration["ManagementPort"]}");
```

Punkty końcowe są tworzone w `Startup.Configure`przykładowej aplikacji w programie . W poniższym przykładzie kodu:

* Kontrola gotowości używa wszystkich zarejestrowanych czeków z tagiem "ready".
* Wyklucza `Predicate` wszystkie kontrole i zwraca 200-Ok.

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
> Można uniknąć tworzenia *pliku launchSettings.json* w przykładowej aplikacji, ustawiając port zarządzania jawnie w kodzie. W *Program.cs,* w <xref:Microsoft.Extensions.Hosting.HostBuilder> którym jest tworzony, <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*> dodaj wywołanie i podaj punkt końcowy portu zarządzania aplikacji. W `Configure` *ManagementPortStartup.cs*określić port zarządzania `RequireHost`za pomocą:
>
> *Program.cs:*
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
> *ManagementPortStartup.cs:*
>
> ```csharp
> app.UseEndpoints(endpoints =>
> {
>     endpoints.MapHealthChecks("/health").RequireHost("*:5001");
> });
> ```

Aby uruchomić scenariusz konfiguracji portu zarządzania przy użyciu przykładowej aplikacji, wykonaj następujące polecenie z folderu projektu w powłoce poleceń:

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a>Rozpowszechnianie biblioteki sprawdzania kondycji

Aby rozpowszechniać sprawdzanie kondycji jako bibliotekę:

1. Napisz sprawdzenie kondycji, które <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> implementuje interfejs jako klasę autonomiczną. Klasa może polegać na [iniekcji zależności (DI),](xref:fundamentals/dependency-injection)aktywacji typu i [nazwanych opcjach](xref:fundamentals/configuration/options) dostępu do danych konfiguracji.

   W logice kontroli `CheckHealthAsync`kondycji:

   * `data1`i `data2` są używane w metodzie do uruchamiania logiki sprawdzania kondycji sondy.
   * `AccessViolationException`obsługiwane.

   Gdy <xref:System.AccessViolationException> wystąpi, <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> jest zwracany <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> z, aby umożliwić użytkownikom skonfigurowanie stanu awarii kontroli kondycji.

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

1. Napisz metodę rozszerzenia z parametrami, które `Startup.Configure` zużywająca aplikacja wywołuje w swojej metodzie. W poniższym przykładzie załóżmy, że następująca metoda sprawdzania kondycji podpisu:

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   Poprzedni podpis wskazuje, że `ExampleHealthCheck` wymaga dodatkowych danych do przetwarzania logiki sondy sprawdzania kondycji. Dane są dostarczane do delegata używane do tworzenia wystąpienia sprawdzania kondycji, gdy sprawdzanie kondycji jest zarejestrowany za pomocą metody rozszerzenia. W poniższym przykładzie wywołujący określa opcjonalne:

   * nazwa kontroli`name`kondycji ( ). Jeśli `null` `example_health_check` , jest używany.
   * punkt danych ciągu dla`data1`kontroli kondycji ( ).
   * punktu danych dla kontroli kondycji`data2`( ). Jeśli `null` `1` , jest używany.
   * stan awarii<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>( ). Wartość domyślna to `null`. Jeśli `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) jest zgłaszane dla stanu błędu.
   * tagi`IEnumerable<string>`( ).

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

## <a name="health-check-publisher"></a>Wydawca sprawdzania kondycji

Po <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> dodaniu do kontenera usługi system sprawdzania kondycji okresowo wykonuje `PublishAsync` kontrole kondycji i wywołuje z wynikiem. Jest to przydatne w scenariuszu systemu monitorowania kondycji opartego na wypychach, który oczekuje, że każdy proces będzie okresowo wywoływał system monitorowania w celu określenia kondycji.

Interfejs <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> ma jedną metodę:

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions>umożliwia ustawienie:

* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>&ndash; Początkowe opóźnienie zastosowane po uruchomieniu aplikacji <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> przed wykonaniem wystąpień. Opóźnienie jest stosowane raz podczas uruchamiania i nie ma zastosowania do kolejnych iteracji. Wartość domyślna to pięć sekund.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>&ndash; Okres <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> realizacji. Wartość domyślna to 30 sekund.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>&ndash; Jeśli <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> `null` jest (domyślnie), usługa wydawcy sprawdzania kondycji uruchamia wszystkie zarejestrowane kontrole kondycji. Aby uruchomić podzbiór kontroli kondycji, podaj funkcję, która filtruje zestaw kontroli. Predykat jest oceniany w każdym okresie.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>&ndash; Limit czasu wykonywania kontroli kondycji dla <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> wszystkich wystąpień. Służy <xref:System.Threading.Timeout.InfiniteTimeSpan> do wykonywania bez limitu czasu. Wartość domyślna to 30 sekund.

W przykładowej `ReadinessPublisher` aplikacji <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> jest implementacja. Stan sprawdzania kondycji jest rejestrowany dla każdego sprawdzenia na poziomie dziennika:

* Informacje<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>( ), jeśli stan <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>kontroli kondycji jest .
* Błąd<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>( ), jeśli stan <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>jest albo lub .

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

W przykładzie przykładowej aplikacji `StartupHostedService` sprawdzanie gotowości ma dwa sekundy opóźnienia uruchamiania `LivenessProbeStartup` i uruchamia sprawdzanie co 30 sekund. Aby aktywować <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementację, `ReadinessPublisher` przykład rejestruje jako usługę singleton w kontenerze [iniekcji zależności (DI):](xref:fundamentals/dependency-injection)

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) obejmuje wydawców kilku systemów, w tym [application insights](/azure/application-insights/app-insights-overview).
>
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) nie jest obsługiwany ani obsługiwany przez firmę Microsoft.

## <a name="restrict-health-checks-with-mapwhen"></a>Ograniczanie kontroli kondycji za pomocą mapWhen

Służy <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> do warunkowego rozgałęzienia potoku żądań dla punktów końcowych sprawdzania kondycji.

W poniższym `MapWhen` przykładzie odgałęzia potok żądania, aby aktywować `api/HealthCheck` oprogramowanie pośredniczące kontroli kondycji, jeśli żądanie GET zostanie odebrane dla punktu końcowego:

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

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/middleware/index#use-run-and-map>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core oferuje oprogramowanie pośredniczące i biblioteki health checks do raportowania kondycji składników infrastruktury aplikacji.

Kontrole kondycji są udostępniane przez aplikację jako punkty końcowe HTTP. Punkty końcowe sprawdzania kondycji można skonfigurować dla różnych scenariuszy monitorowania w czasie rzeczywistym:

* Sondy kondycji mogą być używane przez koordynatorów kontenerów i moduły równoważenia obciążenia w celu sprawdzenia stanu aplikacji. Na przykład orchestrator kontenera może odpowiedzieć na sprawdzanie kondycji wada, zatrzymując wdrożenie stopniowe lub ponowne uruchomienie kontenera. Moduł równoważenia obciążenia może reagować na złej aplikacji przez routing ruchu z dala od wystąpienia awarii do wystąpienia w dobrej kondycji.
* Korzystanie z pamięci, dysku i innych zasobów serwera fizycznego można monitorować pod kątem stanu zdrowia.
* Kontrole kondycji można przetestować zależności aplikacji, takich jak bazy danych i punktów końcowych usługi zewnętrznej, aby potwierdzić dostępność i normalne funkcjonowanie.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

Przykładowa aplikacja zawiera przykłady scenariuszy opisanych w tym temacie. Aby uruchomić przykładową aplikację dla danego scenariusza, użyj polecenia [dotnet run](/dotnet/core/tools/dotnet-run) z folderu projektu w powłoce poleceń. Zobacz przykładowy plik *README.md* aplikacji i opisy scenariuszy w tym temacie, aby uzyskać szczegółowe informacje na temat korzystania z przykładowej aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

Kontrole kondycji są zwykle używane z zewnętrzną usługą monitorowania lub koordynatorem kontenerów w celu sprawdzenia stanu aplikacji. Przed dodaniem kontroli kondycji do aplikacji, zdecydować, który system monitorowania do użycia. System monitorowania określa, jakie typy kontroli kondycji do utworzenia i jak skonfigurować ich punktów końcowych.

Odwołuje się do [metapakietu Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) lub dodaj odwołanie do pakietu [Microsoft.AspNetCore.Diagnostics.HealthChecks.](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks)

Przykładowa aplikacja udostępnia kod startowy, aby zademonstrować testy kondycji dla kilku scenariuszy. Scenariusz [sondy bazy danych](#database-probe) sprawdza kondycję połączenia z bazą danych przy użyciu [aspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks). Scenariusz [sondy DbContext](#entity-framework-core-dbcontext-probe) sprawdza bazę `DbContext`danych przy użyciu ef core . Aby eksplorować scenariusze bazy danych, przykładowa aplikacja:

* Tworzy bazę danych i udostępnia jej parametry połączenia w pliku *appsettings.json.*
* Ma następujące odwołania do pakietu w pliku projektu:
  * [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) nie jest obsługiwany ani obsługiwany przez firmę Microsoft.

Inny scenariusz sprawdzania kondycji pokazuje, jak filtrować kontrole kondycji do portu zarządzania. Przykładowa aplikacja wymaga utworzenia pliku *Properties/launchSettings.json,* który zawiera adres URL zarządzania i port zarządzania. Aby uzyskać więcej informacji, zobacz [sekcję Filtruj według portów.](#filter-by-port)

## <a name="basic-health-probe"></a>Podstawowa sonda kondycji

W przypadku wielu aplikacji podstawowa konfiguracja sondy kondycji, która zgłasza dostępność aplikacji do przetwarzania żądań *(żywotność)* jest wystarczająca do odnajdowania stanu aplikacji.

Podstawowa konfiguracja rejestruje usługi sprawdzania kondycji i wywołuje oprogramowanie pośredniczące kontroli kondycji, aby odpowiedzieć w punkcie końcowym adresu URL z odpowiedzią kondycji. Domyślnie nie są rejestrowane żadne określone kontrole kondycji w celu przetestowania określonej zależności lub podsystemu. Aplikacja jest uważana za w dobrej kondycji, jeśli jest w stanie odpowiedzieć na adres URL punktu końcowego kondycji. Domyślny moduł zapisujący odpowiedzi<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>zapisuje stan ( ) jako odpowiedź w postaci zwykłego tekstu z powrotem do klienta, wskazując stan [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) lub [HealthStatus.Unhealthy.](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus)

Zarejestruj usługi kontroli <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> `Startup.ConfigureServices`kondycji w pliku . Dodaj punkt końcowy dla oprogramowania <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> pośredniczącego kontroli `Startup.Configure`kondycji w potoku przetwarzania żądań .

W przykładowej aplikacji punkt końcowy oceny `/health` kondycji jest tworzony w (*BasicStartup.cs*):

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

Aby uruchomić podstawowy scenariusz konfiguracji przy użyciu przykładowej aplikacji, wykonaj następujące polecenie z folderu projektu w powłoce poleceń:

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a>Przykład platformy Docker

[Docker](xref:host-and-deploy/docker/index) oferuje wbudowaną `HEALTHCHECK` dyrektywę, która może służyć do sprawdzania stanu aplikacji, która używa podstawowej konfiguracji sprawdzania kondycji:

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a>Tworzenie kontroli kondycji

Kontrole kondycji są tworzone <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> przez implementowanie interfejsu. Metoda <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> zwraca, który wskazuje kondycję `Degraded`jako `Unhealthy` `Healthy`, lub . Wynik jest zapisywany jako odpowiedź w postaci zwykłego tekstu z konfigurowalnym kodem stanu (konfiguracja jest opisana w sekcji [Opcje sprawdzania kondycji).](#health-check-options) <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult>może również zwrócić opcjonalne pary klucz-wartość.

### <a name="example-health-check"></a>Przykładowa kontrola kondycji

W `ExampleHealthCheck` poniższej klasie przedstawiono układ kontroli kondycji. Logika kontroli kondycji jest `CheckHealthAsync` umieszczana w metodzie. W poniższym przykładzie ustawia `healthCheckResultHealthy`zmienną manekina, na . `true` Jeśli wartość `healthCheckResultHealthy` jest ustawiona na `false`, [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) stan jest zwracany.

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

### <a name="register-health-check-services"></a>Zarejestruj usługi sprawdzania kondycji

Typ `ExampleHealthCheck` jest dodawany do `Startup.ConfigureServices` usług <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>sprawdzania kondycji w:

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

Przeciążenie <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> pokazane w poniższym przykładzie<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>ustawia stan błędu ( ) do raportu, gdy sprawdzanie kondycji zgłasza błąd. Jeśli stan błędu jest `null` ustawiony na (domyślnie), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) jest zgłaszane. To przeciążenie jest przydatnym scenariuszem dla autorów biblioteki, w którym stan awarii wskazany przez bibliotekę jest wymuszany przez aplikację, gdy wystąpi błąd sprawdzania kondycji, jeśli implementacja sprawdzania kondycji honoruje to ustawienie.

*Tagi* mogą służyć do filtrowania kontroli kondycji (opisane dalej w [sekcji Filtrowanie kontroli kondycji).](#filter-health-checks)

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>można również wykonać funkcję lambda. W poniższym `Startup.ConfigureServices` przykładzie nazwa sprawdzania `Example` kondycji jest określona jako i sprawdzanie zawsze zwraca stan dobrej kondycji:

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

### <a name="use-health-checks-middleware"></a>Korzystanie z oprogramowania pośredniczącego w zakresie kontroli kondycji

W `Startup.Configure`, <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> wywołać w potoku przetwarzania z adresem URL punktu końcowego lub ścieżki względnej:

```csharp
app.UseHealthChecks("/health");
```

Jeśli kontrole kondycji powinny nasłuchiwać określonego portu, użyj przeciążenia, <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> aby ustawić port (opisany dalej w sekcji Filtr według [portu):](#filter-by-port)

```csharp
app.UseHealthChecks("/health", port: 8000);
```

## <a name="health-check-options"></a>Opcje sprawdzania kondycji

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions>możliwość dostosowania zachowania sprawdzania kondycji:

* [Sprawdzanie kondycji filtrowania](#filter-health-checks)
* [Dostosowywanie kodu stanu HTTP](#customize-the-http-status-code)
* [Pomijanie nagłówków pamięci podręcznej](#suppress-cache-headers)
* [Dostosowywanie danych wyjściowych](#customize-output)

### <a name="filter-health-checks"></a>Sprawdzanie kondycji filtrowania

Domyślnie oprogramowanie pośredniczące health checks uruchamia wszystkie zarejestrowane kontrole kondycji. Aby uruchomić podzbiór kontroli kondycji, podaj funkcję, która zwraca wartość logiczną <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> do opcji. W poniższym przykładzie `Bar` sprawdzanie kondycji jest odfiltrowywało według jego znacznika (`bar_tag`) w instrukcji warunkowej funkcji, gdzie `true` jest zwracane tylko wtedy, gdy <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> właściwość sprawdzania kondycji jest zgodna `foo_tag` lub: `baz_tag`

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

Służy <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> do dostosowywania mapowania stanu kondycji do kodów stanu HTTP. Następujące <xref:Microsoft.AspNetCore.Http.StatusCodes> przypisania są wartościami domyślnymi używanymi przez oprogramowanie pośredniczące. Zmień wartości kodu stanu, aby spełnić wymagania.

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

### <a name="suppress-cache-headers"></a>Pomijanie nagłówków pamięci podręcznej

<xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses>określa, czy oprogramowanie pośredniczące kontroli kondycji dodaje nagłówki HTTP do odpowiedzi sondy, aby zapobiec buforowaniu odpowiedzi. Jeśli wartość `false` jest (domyślna), oprogramowanie pośredniczące `Cache-Control` `Expires`ustawia `Pragma` lub zastępuje , i nagłówki, aby zapobiec buforowaniu odpowiedzi. Jeśli wartość `true`jest , oprogramowanie pośredniczące nie modyfikuje nagłówki pamięci podręcznej odpowiedzi.

W pliku `Startup.Configure`:

```csharp
//using Microsoft.AspNetCore.Diagnostics.HealthChecks;
//using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    AllowCachingResponses = false
});
```

### <a name="customize-output"></a>Dostosowywanie danych wyjściowych

Opcja <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> pobiera lub ustawia delegata używanego do pisania odpowiedzi. Domyślny delegat zapisuje minimalną odpowiedź w postaci zwykłego tekstu z wartością ciągu [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).

W pliku `Startup.Configure`:

```csharp
// using Microsoft.AspNetCore.Diagnostics.HealthChecks;
// using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResponseWriter = WriteResponse
});
```

Domyślny delegat zapisuje minimalną odpowiedź w postaci zwykłego tekstu z wartością ciągu [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status). Następujący delegat niestandardowy `WriteResponse`, wyprowadza niestandardową odpowiedź JSON:

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

System kontroli kondycji nie zapewnia wbudowanej obsługi złożonych formatów zwracanych JSON, ponieważ format jest specyficzny dla wybranego systemu monitorowania. Możesz dostosować w `JObject` poprzednim przykładzie, gdy jest to konieczne do twoich potrzeb.

## <a name="database-probe"></a>Sonda bazy danych

Sprawdzanie kondycji można określić kwerendę bazy danych do uruchomienia jako test logiczny, aby wskazać, czy baza danych odpowiada normalnie.

Przykładowa aplikacja używa [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), biblioteki sprawdzania kondycji dla aplikacji ASP.NET Core, do sprawdzania kondycji w bazie danych programu SQL Server. `AspNetCore.Diagnostics.HealthChecks`wykonuje kwerendę do `SELECT 1` bazy danych, aby potwierdzić, że połączenie z bazą danych jest w dobrej kondycji.

> [!WARNING]
> Podczas sprawdzania połączenia bazy danych z kwerendą wybierz kwerendę, która zwraca szybko. Podejście zapytania ryzykuje przeciążenie bazy danych i obniżenie jej wydajności. W większości przypadków uruchamianie kwerendy testowej nie jest konieczne. Wystarczy jedynie pomyślne połączenie z bazą danych. Jeśli okaże się konieczne uruchomienie kwerendy, wybierz proste `SELECT 1`zapytanie SELECT, takie jak .

Dołącz odwołanie do pakietu [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).

Podaj prawidłowy ciąg połączenia bazy danych w pliku *appsettings.json* przykładowej aplikacji. Aplikacja korzysta z bazy danych `HealthCheckSample`programu SQL Server o nazwie:

[!code-json[](health-checks/samples/2.x/HealthChecksSample/appsettings.json?highlight=3)]

Zarejestruj usługi kontroli <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> `Startup.ConfigureServices`kondycji w pliku . Przykładowa aplikacja `AddSqlServer` wywołuje metodę z ciągiem połączenia bazy danych *(DbHealthStartup.cs):*

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

Wywołanie health checks Middleware w `Startup.Configure`potoku przetwarzania aplikacji w:

```csharp
app.UseHealthChecks("/health");
```

Aby uruchomić scenariusz sondy bazy danych przy użyciu przykładowej aplikacji, wykonaj następujące polecenie z folderu projektu w powłoce poleceń:

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) nie jest obsługiwany ani obsługiwany przez firmę Microsoft.

## <a name="entity-framework-core-dbcontext-probe"></a>Sonda Core DbContext programu Entity Framework

Sprawdzanie `DbContext` potwierdza, że aplikacja może komunikować się z bazą danych skonfigurowaną dla ef core `DbContext`. Sprawdzanie `DbContext` jest obsługiwane w aplikacjach, które:

* Użyj [core (Entity Framework) Core](/ef/core/).
* Dołącz odwołanie do pakietu [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).

`AddDbContextCheck<TContext>`rejestruje kontrolę kondycji dla `DbContext`. Jest `DbContext` dostarczany jako `TContext` do metody. Przeciążenie jest dostępne do skonfigurowania stanu błędu, tagów i niestandardowej kwerendy testowej.

Domyślnie:

* Wywołanie `DbContextHealthCheck` metody EF `CanConnectAsync` Core. Można dostosować, jaka operacja jest uruchamiana podczas sprawdzania kondycji przy użyciu `AddDbContextCheck` przeciążenia metody.
* Nazwa sprawdzania kondycji jest nazwą `TContext` typu.

W przykładowej `AppDbContext` aplikacji, `AddDbContextCheck` jest dostarczany i `Startup.ConfigureServices` zarejestrowany jako usługa w (*DbContextHealthStartup.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

W przykładowej `UseHealthChecks` aplikacji dodaje oprogramowanie pośredniczące kontroli kondycji w `Startup.Configure`.

```csharp
app.UseHealthChecks("/health");
```

Aby uruchomić `DbContext` scenariusz sondowania przy użyciu przykładowej aplikacji, upewnij się, że baza danych określona przez parametry połączenia nie istnieje w wystąpieniu programu SQL Server. Jeśli baza danych istnieje, usuń ją.

Wykonaj następujące polecenie z folderu projektu w powłoce poleceń:

```dotnetcli
dotnet run --scenario dbcontext
```

Po uruchomieniu aplikacji sprawdź stan kondycji, wysyłając żądanie `/health` do punktu końcowego w przeglądarce. Baza danych `AppDbContext` i nie istnieje, więc aplikacja zapewnia następującą odpowiedź:

```
Unhealthy
```

Wyzwalanie przykładowej aplikacji, aby utworzyć bazę danych. Złożyć wniosek `/createdatabase`do . Aplikacja odpowiada:

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

Złożyć żądanie `/health` do punktu końcowego. Istnieje baza danych i kontekst, więc aplikacja odpowiada:

```
Healthy
```

Wyzwalanie przykładowej aplikacji, aby usunąć bazę danych. Złożyć wniosek `/deletedatabase`do . Aplikacja odpowiada:

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

Złożyć żądanie `/health` do punktu końcowego. Aplikacja zapewnia w złej kondycji odpowiedzi:

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a>Oddzielne sondy gotowości i żywotności

W niektórych scenariuszach hostingu używana jest para kontroli kondycji, które rozróżniają dwa stany aplikacji:

* Aplikacja działa, ale nie jest jeszcze gotowa do odbierania żądań. Ten stan jest *gotowość*aplikacji .
* Aplikacja działa i odpowiada na żądania. Ten stan jest *żywość*aplikacji .

Sprawdzanie gotowości zwykle wykonuje bardziej obszerny i czasochłonny zestaw kontroli, aby ustalić, czy wszystkie podsystemy i zasoby aplikacji są dostępne. Sprawdzanie żywotności wykonuje tylko szybkie sprawdzenie, aby ustalić, czy aplikacja jest dostępna do przetwarzania żądań. Po przejściu przez aplikację kontroli gotowości, nie ma potrzeby, aby obciążyć&mdash;aplikację dalej z drogim zestawem kontroli gotowości dalsze kontrole wymagają tylko sprawdzenia, czy nie ma żywotności.

Przykładowa aplikacja zawiera sprawdzanie kondycji, aby zgłosić zakończenie długotrwałego zadania uruchamiania w [usłudze hosted](xref:fundamentals/host/hosted-services). Udostępnia `StartupHostedServiceHealthCheck` właściwość, `StartupTaskCompleted`że usługa hostowana można `true` ustawić po zakończeniu jej długotrwałe zadanie *(StartupHostedServiceHealthCheck.cs):*

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

Długotrwałe zadanie w tle jest uruchamiane przez [usługę hostowana](xref:fundamentals/host/hosted-services) *(Usługi/StartupHostedService).* Po zakończeniu zadania, `StartupHostedServiceHealthCheck.StartupTaskCompleted` jest ustawiony `true`na:

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

Kontrola kondycji jest <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> zarejestrowana w `Startup.ConfigureServices` wraz z usługą hosta. Ponieważ usługa hostowana musi ustawić właściwość na kontroli kondycji, kontrola kondycji jest również zarejestrowana w kontenerze serwisowym (*LivenessProbeStartup.cs):*

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

Wywołanie health checks Middleware w `Startup.Configure`potoku przetwarzania aplikacji w pliku . W przykładowej aplikacji punkty końcowe sprawdzania `/health/ready` kondycji są tworzone `/health/live` w celu sprawdzenia gotowości i sprawdzania żywotności. Sprawdzanie gotowości filtruje kontrole kondycji do sprawdzenia `ready` kondycji za pomocą tagu. Sprawdzanie żywotności filtruje `StartupHostedServiceHealthCheck` przez `false` powrót w [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (aby uzyskać więcej informacji, zobacz [filtr kontroli kondycji):](#filter-health-checks)

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

Aby uruchomić scenariusz konfiguracji gotowości/żywotności przy użyciu przykładowej aplikacji, wykonaj następujące polecenie z folderu projektu w powłoce poleceń:

```dotnetcli
dotnet run --scenario liveness
```

W przeglądarce `/health/ready` odwiedź kilka razy, aż minęło 15 sekund. Kontrola kondycji raportuje *wzdrowy sposób* przez pierwsze 15 sekund. Po 15 sekundach punkt końcowy raportuje *w dobrej kondycji*, co odzwierciedla ukończenie długotrwałego zadania przez hostowany serwis.

W tym przykładzie tworzy<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> również Health Check Publisher (implementacja), który uruchamia pierwsze sprawdzanie gotowości z dwusekundowym opóźnieniem. Aby uzyskać więcej informacji, zobacz sekcję [Wydawca sprawdzania kondycji.](#health-check-publisher)

### <a name="kubernetes-example"></a>Przykład Kubernetes

Korzystanie z oddzielnych kontroli gotowości i żywotności jest przydatne w środowisku takim jak [Kubernetes](https://kubernetes.io/). W umięsierze aplikacja może być wymagana do wykonywania czasochłonnych prac podczas uruchamiania przed zaakceptowaniem żądań, takich jak test podstawowej dostępności bazy danych. Za pomocą oddzielnych kontroli umożliwia orchestrator odróżnić, czy aplikacja działa, ale nie jest jeszcze gotowy lub jeśli aplikacja nie powiodło się uruchomić. Aby uzyskać więcej informacji na temat gotowości i sondy żywotności w umięsienia, zobacz [Konfigurowanie sondy żywotności i gotowości](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) w dokumentacji programu Kubernetes.

W poniższym przykładzie pokazano konfigurację sondy gotowości Kubernetes:

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

## <a name="metric-based-probe-with-a-custom-response-writer"></a>Sonda oparta na metrykach z modułem zapisu odpowiedzi niestandardowej

Przykładowa aplikacja pokazuje sprawdzanie kondycji pamięci za pomocą modułu zapisującego odpowiedzi niestandardowej.

`MemoryHealthCheck`zgłasza stan w złej kondycji, jeśli aplikacja używa więcej niż danego progu pamięci (1 GB w przykładowej aplikacji). Informacje <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> o odpadach odśmiecania (GC) dla aplikacji *(MemoryHealthCheck.cs):*

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

Zarejestruj usługi kontroli <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> `Startup.ConfigureServices`kondycji w pliku . Zamiast włączać sprawdzanie kondycji, przekazując <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>go `MemoryHealthCheck` do , jest zarejestrowany jako usługa. Wszystkie <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> zarejestrowane usługi są dostępne dla usług kontroli kondycji i oprogramowania pośredniczącego. Zalecamy rejestrowanie usług kontroli kondycji jako usług Singleton.

W przykładowej aplikacji (*CustomWriterStartup.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

Wywołanie health checks Middleware w `Startup.Configure`potoku przetwarzania aplikacji w pliku . Pełnomocnik `WriteResponse` jest dostarczany `ResponseWriter` do właściwości do wysuń niestandardową odpowiedź JSON podczas wykonywania sprawdzania kondycji:

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

Metoda `WriteResponse` formatuje `CompositeHealthCheckResult` do obiektu JSON i daje wyjście JSON dla odpowiedzi na sprawdzenie kondycji:

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse)]

Aby uruchomić sondę opartą na metrykach z niestandardowym wyjściem modułu zapisującego odpowiedzi przy użyciu przykładowej aplikacji, wykonaj następujące polecenie z folderu projektu w powłoce poleceń:

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) zawiera scenariusze sprawdzania kondycji oparte na metrykach, w tym sprawdzanie żywotności dysku i maksymalnej wartości.
>
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) nie jest obsługiwany ani obsługiwany przez firmę Microsoft.

## <a name="filter-by-port"></a>Filtrowanie według portów

Wywołanie <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> z portem ogranicza żądania sprawdzania kondycji do określonego portu. Jest to zwykle używane w środowisku kontenera, aby udostępnić port dla usług monitorowania.

Przykładowa aplikacja konfiguruje port przy użyciu [dostawcy konfiguracji zmiennych środowiskowych](xref:fundamentals/configuration/index#environment-variables-configuration-provider). Port jest ustawiony w pliku *launchSettings.json* i przekazywany do dostawcy konfiguracji za pośrednictwem zmiennej środowiskowej. Należy również skonfigurować serwer do nasłuchiwać żądań na porcie zarządzania.

Aby użyć przykładowej aplikacji do zademonstrowania konfiguracji portu zarządzania, utwórz plik *launchSettings.json* w folderze *Właściwości.*

Następujący plik *Properties/launchSettings.json* w przykładowej aplikacji nie jest uwzględniony w przykładowych plikach projektu aplikacji i musi zostać utworzony ręcznie:

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

Zarejestruj usługi kontroli <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> `Startup.ConfigureServices`kondycji w pliku . Wywołanie <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> określa port zarządzania (*ManagementPortStartup.cs*):

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ManagementPortStartup.cs?name=snippet1&highlight=17)]

> [!NOTE]
> Można uniknąć tworzenia *pliku launchSettings.json* w przykładowej aplikacji, ustawiając adresy URL i port zarządzania jawnie w kodzie. W *Program.cs,* w <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> którym jest tworzony, <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> dodaj wywołanie i podaj normalnego punktu końcowego odpowiedzi aplikacji i punktu końcowego portu zarządzania. W *ManagementPortStartup.cs* gdzie <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> jest wywoływana, należy określić port zarządzania jawnie.
>
> *Program.cs:*
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
> *ManagementPortStartup.cs:*
>
> ```csharp
> app.UseHealthChecks("/health", port: 5001);
> ```

Aby uruchomić scenariusz konfiguracji portu zarządzania przy użyciu przykładowej aplikacji, wykonaj następujące polecenie z folderu projektu w powłoce poleceń:

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a>Rozpowszechnianie biblioteki sprawdzania kondycji

Aby rozpowszechniać sprawdzanie kondycji jako bibliotekę:

1. Napisz sprawdzenie kondycji, które <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> implementuje interfejs jako klasę autonomiczną. Klasa może polegać na [iniekcji zależności (DI),](xref:fundamentals/dependency-injection)aktywacji typu i [nazwanych opcjach](xref:fundamentals/configuration/options) dostępu do danych konfiguracji.

   W logice kontroli `CheckHealthAsync`kondycji:

   * `data1`i `data2` są używane w metodzie do uruchamiania logiki sprawdzania kondycji sondy.
   * `AccessViolationException`obsługiwane.

   Gdy <xref:System.AccessViolationException> wystąpi, <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> jest zwracany <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> z, aby umożliwić użytkownikom skonfigurowanie stanu awarii kontroli kondycji.

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

1. Napisz metodę rozszerzenia z parametrami, które `Startup.Configure` zużywająca aplikacja wywołuje w swojej metodzie. W poniższym przykładzie załóżmy, że następująca metoda sprawdzania kondycji podpisu:

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   Poprzedni podpis wskazuje, że `ExampleHealthCheck` wymaga dodatkowych danych do przetwarzania logiki sondy sprawdzania kondycji. Dane są dostarczane do delegata używane do tworzenia wystąpienia sprawdzania kondycji, gdy sprawdzanie kondycji jest zarejestrowany za pomocą metody rozszerzenia. W poniższym przykładzie wywołujący określa opcjonalne:

   * nazwa kontroli`name`kondycji ( ). Jeśli `null` `example_health_check` , jest używany.
   * punkt danych ciągu dla`data1`kontroli kondycji ( ).
   * punktu danych dla kontroli kondycji`data2`( ). Jeśli `null` `1` , jest używany.
   * stan awarii<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>( ). Wartość domyślna to `null`. Jeśli `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) jest zgłaszane dla stanu błędu.
   * tagi`IEnumerable<string>`( ).

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

## <a name="health-check-publisher"></a>Wydawca sprawdzania kondycji

Po <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> dodaniu do kontenera usługi system sprawdzania kondycji okresowo wykonuje `PublishAsync` kontrole kondycji i wywołuje z wynikiem. Jest to przydatne w scenariuszu systemu monitorowania kondycji opartego na wypychach, który oczekuje, że każdy proces będzie okresowo wywoływał system monitorowania w celu określenia kondycji.

Interfejs <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> ma jedną metodę:

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions>umożliwia ustawienie:

* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>&ndash; Początkowe opóźnienie zastosowane po uruchomieniu aplikacji <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> przed wykonaniem wystąpień. Opóźnienie jest stosowane raz podczas uruchamiania i nie ma zastosowania do kolejnych iteracji. Wartość domyślna to pięć sekund.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>&ndash; Okres <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> realizacji. Wartość domyślna to 30 sekund.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>&ndash; Jeśli <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> `null` jest (domyślnie), usługa wydawcy sprawdzania kondycji uruchamia wszystkie zarejestrowane kontrole kondycji. Aby uruchomić podzbiór kontroli kondycji, podaj funkcję, która filtruje zestaw kontroli. Predykat jest oceniany w każdym okresie.
* <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>&ndash; Limit czasu wykonywania kontroli kondycji dla <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> wszystkich wystąpień. Służy <xref:System.Threading.Timeout.InfiniteTimeSpan> do wykonywania bez limitu czasu. Wartość domyślna to 30 sekund.

> [!WARNING]
> W wydaniu ASP.NET Core 2.2 <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> ustawienie nie jest <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> honorowane przez implementację; ustawia wartość . <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> Ten problem został rozwiązany w ASP.NET Core 3.0.

W przykładowej `ReadinessPublisher` aplikacji <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> jest implementacja. Stan sprawdzania kondycji jest rejestrowany dla każdego sprawdzenia w następujący sposób:

* Informacje<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>( ), jeśli stan <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>kontroli kondycji jest .
* Błąd<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>( ), jeśli stan <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>jest albo lub .

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

W przykładzie przykładowej aplikacji `StartupHostedService` sprawdzanie gotowości ma dwa sekundy opóźnienia uruchamiania `LivenessProbeStartup` i uruchamia sprawdzanie co 30 sekund. Aby aktywować <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementację, `ReadinessPublisher` przykład rejestruje jako usługę singleton w kontenerze [iniekcji zależności (DI):](xref:fundamentals/dependency-injection)

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices&highlight=12-17,28)]

> [!NOTE]
> Poniższe obejście zezwala <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> na dodanie wystąpienia do kontenera usługi, gdy jedna lub więcej innych usług hostowanych zostało już dodanych do aplikacji. To obejście nie jest wymagane w ASP.NET Core 3.0.
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
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) obejmuje wydawców kilku systemów, w tym [application insights](/azure/application-insights/app-insights-overview).
>
> [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) nie jest obsługiwany ani obsługiwany przez firmę Microsoft.

## <a name="restrict-health-checks-with-mapwhen"></a>Ograniczanie kontroli kondycji za pomocą mapWhen

Służy <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> do warunkowego rozgałęzienia potoku żądań dla punktów końcowych sprawdzania kondycji.

W poniższym `MapWhen` przykładzie odgałęzia potok żądania, aby aktywować `api/HealthCheck` oprogramowanie pośredniczące kontroli kondycji, jeśli żądanie GET zostanie odebrane dla punktu końcowego:

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseMvc();
```

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/middleware/index#use-run-and-map>.

::: moniker-end
