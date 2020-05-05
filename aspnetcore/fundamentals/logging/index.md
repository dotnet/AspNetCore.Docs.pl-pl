---
title: Rejestrowanie w programie .NET Core i ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak używać struktury rejestrowania dostarczonej przez pakiet NuGet Microsoft. Extensions. Logging.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/23/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/logging/index
ms.openlocfilehash: ca62e374c6031ca3c2d438df87f2d13636d9c612
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776106"
---
# <a name="logging-in-net-core-and-aspnet-core"></a>Rejestrowanie w programie .NET Core i ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Autorzy [Dykstra](https://github.com/tdykstra) i [Steve Smith](https://ardalis.com/)

Platforma .NET Core obsługuje interfejs API rejestrowania, który współpracuje z różnymi dostawcami rejestrowania wbudowanych i innych firm. W tym artykule pokazano, jak używać interfejsu API rejestrowania z wbudowanymi dostawcami.

Większość przykładów kodu pokazanych w tym artykule pochodzą z ASP.NET Core aplikacji. Części tych fragmentów kodu dotyczące rejestrowania mają zastosowanie do dowolnej aplikacji .NET Core, która korzysta z [hosta ogólnego](xref:fundamentals/host/generic-host). Przykład korzystania z hosta ogólnego w aplikacji konsolowej innej niż sieci Web można znaleźć w pliku *program.cs* [zadania w tle](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).

Rejestrowanie kodu dla aplikacji bez hosta ogólnego różni się w sposób, w jaki [są dodawane dostawcy](#add-providers) i [są tworzone rejestratory](#create-logs). Przykłady kodu niehosta są wyświetlane w tych częściach artykułu.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="add-providers"></a>Dodaj dostawców

Dostawca rejestrowania wyświetla lub przechowuje dzienniki. Na przykład dostawca konsoli wyświetla dzienniki w konsoli programu, a Dostawca usługi Azure Application Insights przechowuje je na platformie Azure Application Insights. Dzienniki mogą być wysyłane do wielu miejsc docelowych przez dodanie wielu dostawców.

Aby dodać dostawcę w aplikacji korzystającej z hosta ogólnego, wywołaj metodę `Add{provider name}` rozszerzenia dostawcy w *program.cs*:

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=6)]

W aplikacji konsolowej innej niż host Wywołaj metodę `Add{provider name}` rozszerzenia dostawcy podczas tworzenia: `LoggerFactory`

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=1,7)]

`LoggerFactory`i `AddConsole` wymagają `using` instrukcji dla `Microsoft.Extensions.Logging`.

Domyślne wywołanie <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>szablonów projektu ASP.NET Core, które dodaje następujących dostawców rejestrowania:

* [Konsola](#console-provider)
* [Rozpocząć](#debug-provider)
* [EventSource](#event-source-provider)
* [EventLog](#windows-eventlog-provider) (tylko w przypadku uruchamiania w systemie Windows)

Dostawców domyślnych można zastąpić własnymi opcjami. Wywołaj <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>i Dodaj żądanych dostawców.

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=5)]

Więcej informacji na temat [wbudowanych dostawców rejestrowania](#built-in-logging-providers) i [dostawców rejestrowania innych](#third-party-logging-providers) firm znajduje się w dalszej części artykułu.

## <a name="create-logs"></a>Tworzenie dzienników

Aby utworzyć dzienniki, użyj <xref:Microsoft.Extensions.Logging.ILogger%601> obiektu. W aplikacji sieci Web lub hostowanej usłudze Pobierz `ILogger` od iniekcji zależności (di). W aplikacjach konsolowych innych niż host Użyj programu `LoggerFactory` , aby utworzyć `ILogger`.

Poniższy ASP.NET Core przykład tworzy Rejestrator przy użyciu `TodoApiSample.Pages.AboutModel` jako kategorii. *Kategoria* dziennika jest ciągiem, który jest skojarzony z każdym dziennikiem. `ILogger<T>` Wystąpienie zapewniane przez program di tworzy dzienniki, które mają w pełni kwalifikowaną nazwę `T` typu jako kategorię. 

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

Poniższy przykład aplikacji nieprzyjmującej konsoli tworzy Rejestrator z `LoggingConsoleApp.Program` kategorią.

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=10)]

W poniższych przykładach ASP.NET Core i aplikacji konsoli Rejestrator służy do tworzenia dzienników z użyciem `Information` jako poziom. *Poziom* dziennika wskazuje ważność rejestrowanego zdarzenia.

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11)]

[Poziomy](#log-level) i [Kategorie](#log-category) zostały wyjaśnione bardziej szczegółowo w dalszej części tego artykułu.

### <a name="create-logs-in-the-program-class"></a>Tworzenie dzienników w klasie programu

Aby napisać dzienniki w `Program` klasie ASP.NET Core aplikacji, Pobierz `ILogger` wystąpienie z programu di po skompilowaniu hosta:

[!code-csharp[](index/samples_snapshot/3.x/TodoApiSample/Program.cs?highlight=9,10)]

Rejestrowanie podczas konstruowania hosta nie jest bezpośrednio obsługiwane. Można jednak użyć osobnego rejestratora. W poniższym przykładzie jest używany Rejestrator [Serilog](https://serilog.net/) do logowania `CreateHostBuilder`. `AddSerilog`używa konfiguracji statycznej określonej w `Log.Logger`:

```csharp
using System;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return Host.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddRazorPages();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {   
                    logging.AddSerilog();
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

### <a name="create-logs-in-the-startup-class"></a>Tworzenie dzienników w klasie startowej

Aby napisać dzienniki w `Startup.Configure` metodzie aplikacji ASP.NET Core, Dołącz `ILogger` parametr do sygnatury metody:

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_Configure&highlight=1,5)]

Zapisywanie dzienników przed ukończeniem instalacji programu DI Container w `Startup.ConfigureServices` metodzie nie jest obsługiwane:

* Iniekcja rejestratora `Startup` do konstruktora nie jest obsługiwana.
* Iniekcja rejestratora `Startup.ConfigureServices` do sygnatury metody nie jest obsługiwana

Przyczyną tego ograniczenia jest to, że rejestrowanie jest zależne od typu i konfiguracji, która z tego powodu zależy od DI. Kontener DI nie jest ustawiany do momentu `ConfigureServices` zakończenia.

Wstrzykiwanie konstruktora rejestratora do `Startup` działa we wcześniejszych wersjach ASP.NET Core, ponieważ dla hosta sieci Web jest tworzony oddzielny kontener di. Aby uzyskać informacje o tym, dlaczego dla hosta generycznego jest tworzony tylko jeden kontener, zobacz [zawiadomienie o rozdzieleniu zmian](https://github.com/aspnet/Announcements/issues/353).

Jeśli trzeba skonfigurować usługę, która zależy od programu `ILogger<T>`, można to zrobić za pomocą iniekcji konstruktora lub dostarczając metodę fabryki. Podejście metody fabryki jest zalecane tylko wtedy, gdy nie ma żadnych innych opcji. Załóżmy na przykład, że musisz wypełnić Właściwość usługą z:

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

Poprzedni wyróżniony kod jest `Func` uruchomiony, gdy jest uruchamiany po raz pierwszy kontener di musi utworzyć wystąpienie. `MyService` W ten sposób można uzyskać dostęp do dowolnych zarejestrowanych usług.

### <a name="create-logs-in-blazor"></a>Tworzenie dzienników w Blazor

#### <a name="blazor-webassembly"></a>Zestaw WebAssembly Blazor

Konfigurowanie rejestrowania w aplikacjach Blazor webassembly z `WebAssemblyHostBuilder.Logging` właściwością w `Program.Main`:

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

...

var builder = WebAssemblyHostBuilder.CreateDefault(args);

builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

`Logging` Właściwość jest typu <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, więc wszystkie dostępne metody rozszerzenia <xref:Microsoft.Extensions.Logging.ILoggingBuilder> są również dostępne w systemie `Logging`.

#### <a name="log-in-razor-components"></a>Logowanie składników Razor

Rejestratory respektują konfigurację uruchamiania aplikacji.

`using` Dyrektywa <xref:Microsoft.Extensions.Logging> for jest wymagana do obsługi uzupełniania technologii IntelliSense dla interfejsów API, takich <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> jak <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>i.

Poniższy przykład demonstruje rejestrowanie przy użyciu <xref:Microsoft.Extensions.Logging.ILogger> składników Razor:

```razor
@page "/counter"
@using Microsoft.Extensions.Logging;
@inject ILogger<Counter> logger;

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        logger.LogWarning("Someone has clicked me!");

        currentCount++;
    }
}
```

Poniższy przykład demonstruje rejestrowanie przy użyciu <xref:Microsoft.Extensions.Logging.ILoggerFactory> składników Razor:

```razor
@page "/counter"
@using Microsoft.Extensions.Logging;
@inject ILoggerFactory LoggerFactory

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        var logger = LoggerFactory.CreateLogger<Counter>();
        logger.LogWarning("Someone has clicked me!");

        currentCount++;
    }
}
```

### <a name="no-asynchronous-logger-methods"></a>Brak metod rejestratora asynchronicznego

Rejestracja powinna być tak szybka, że nie jest to koszt wydajności kodu asynchronicznego. Jeśli magazyn danych rejestrowania jest wolny, nie zapisuj go bezpośrednio. Najpierw Rozważ zapisanie komunikatów dziennika do szybkiego sklepu, a następnie przeniesienie ich do wolnego magazynu później. Na przykład jeśli rejestrujesz się do SQL Server, nie chcesz tego robić bezpośrednio w `Log` metodzie, ponieważ `Log` metody są synchroniczne. Zamiast tego można synchronicznie dodawać komunikaty dziennika do kolejki w pamięci, a proces roboczy w tle ściągał komunikaty z kolejki, aby wykonać asynchroniczne działanie wypychania danych do SQL Server. Aby uzyskać więcej informacji, zobacz [ten](https://github.com/dotnet/AspNetCore.Docs/issues/11801) problem w serwisie GitHub.

## <a name="configuration"></a>Konfigurowanie

Konfiguracja dostawcy rejestrowania jest świadczona przez co najmniej jednego dostawcę konfiguracji:

* Formaty plików (INI, JSON i XML).
* Argumenty wiersza polecenia.
* Zmienne środowiskowe.
* Obiekty platformy .NET w pamięci.
* Magazyn niezaszyfrowanego [klucza tajnego](xref:security/app-secrets) .
* Zaszyfrowany magazyn użytkowników, taki jak [Azure Key Vault](xref:security/key-vault-configuration).
* Dostawcy niestandardowi (instalowani lub utworzony).

Na przykład konfiguracja rejestrowania jest zwykle dostarczana przez `Logging` sekcję plików ustawień aplikacji. Poniższy przykład pokazuje zawartość typowej wartości *appSettings. Plik Development. JSON* :

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    },
    "Console":
    {
      "IncludeScopes": true
    }
  }
}
```

`Logging` Właściwość może mieć `LogLevel` właściwości dostawcy dzienników i.

`LogLevel` Właściwość w obszarze `Logging` określa minimalny [poziom](#log-level) rejestrowania wybranych kategorii. `System` W przykładzie, `Microsoft` i kategorie są rejestrowane na `Information` poziomie, a wszystkie inne logowania na `Debug` poziomie.

Inne właściwości w `Logging` obszarze Określ dostawców rejestrowania. Przykład dotyczy dostawcy konsoli. Jeśli dostawca obsługuje [zakresy rejestrowania](#log-scopes), wskazuje `IncludeScopes` , czy są one włączone. Właściwość dostawcy (taka jak `Console` w przykładzie) może także określić `LogLevel` właściwość. `LogLevel`w obszarze dostawca Określa poziomy do rejestrowania dla tego dostawcy.

Jeśli w programie `Logging.{providername}.LogLevel`są określone poziomy, zastępują one wszystko `Logging.LogLevel`ustawione w.

Interfejs API rejestrowania nie zawiera scenariusza zmiany poziomów rejestrowania, gdy aplikacja jest uruchomiona. Niektórzy dostawcy konfiguracji mogą jednak ponownie ładować konfigurację, która zacznie natychmiastowo wpływać na konfigurację rejestrowania. Na przykład [dostawca konfiguracji plików](xref:fundamentals/configuration/index#file-configuration-provider), który jest dodawany przez `CreateDefaultBuilder` program do odczytu plików ustawień, ponownie ładuje konfigurację rejestrowania domyślnie. Jeśli konfiguracja zostanie zmieniona w kodzie w czasie, gdy aplikacja jest uruchomiona, aplikacja może wywołać [IConfigurationRoot. reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) , aby zaktualizować konfigurację rejestrowania aplikacji.

Informacje o implementowaniu dostawców konfiguracji znajdują <xref:fundamentals/configuration/index>się w temacie.

## <a name="sample-logging-output"></a>Przykładowe dane wyjściowe rejestrowania

W przypadku przykładowego kodu podanego w poprzedniej sekcji Dzienniki są wyświetlane w konsoli programu, gdy aplikacja jest uruchamiana z wiersza polecenia. Oto przykład danych wyjściowych konsoli:

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 84.26180000000001ms 307
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 GET https://localhost:5001/api/todo/0
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[3]
      Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
info: TodoApiSample.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
```

Poprzednie dzienniki zostały wygenerowane przez utworzenie żądania HTTP GET do przykładowej aplikacji w lokalizacji `http://localhost:5000/api/todo/0`.

Oto przykład tych samych dzienników, które są wyświetlane w oknie debugowania podczas uruchamiania przykładowej aplikacji w programie Visual Studio:

```console
Microsoft.AspNetCore.Hosting.Diagnostics: Information: Request starting HTTP/2.0 GET https://localhost:44328/api/todo/0  
Microsoft.AspNetCore.Routing.EndpointMiddleware: Information: Executing endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker: Information: Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
TodoApiSample.Controllers.TodoController: Information: Getting item 0
TodoApiSample.Controllers.TodoController: Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult: Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker: Information: Executed action TodoApiSample.Controllers.TodoController.GetById (TodoApiSample) in 34.167ms
Microsoft.AspNetCore.Routing.EndpointMiddleware: Information: Executed endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
Microsoft.AspNetCore.Hosting.Diagnostics: Information: Request finished in 98.41300000000001ms 404
```

Dzienniki utworzone przez `ILogger` wywołania pokazane w poprzedniej sekcji zaczynają się od "TodoApiSample". Dzienniki zaczynające się od kategorii "Microsoft" pochodzą z kodu ASP.NET Core Framework. ASP.NET Core i kod aplikacji używają tego samego rejestrowania interfejsu API i dostawców.

W pozostałej części tego artykułu opisano niektóre szczegóły i opcje rejestrowania.

## <a name="nuget-packages"></a>Pakiety NuGet

Interfejsy `ILogger` i `ILoggerFactory` są w [Microsoft. Extensions. Logging. Abstracts](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/)i domyślne implementacje dla nich są w [Microsoft. Extensions. Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).

## <a name="log-category"></a>Kategoria dziennika

Po utworzeniu `ILogger` obiektu jest dla niego określona *Kategoria* . Ta kategoria jest dołączona do każdego komunikatu dziennika utworzonego przez to wystąpienie `ILogger`. Kategoria może być dowolnym ciągiem, ale Konwencja ma używać nazwy klasy, takiej jak "TodoApi. controllers. TodoController".

Użyj `ILogger<T>` , aby uzyskać `ILogger` wystąpienie używające w pełni kwalifikowanej nazwy typu `T` jako kategorii:

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

Aby jawnie określić kategorię, wywołaj `ILoggerFactory.CreateLogger`:

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

`ILogger<T>`jest odpowiednikiem wywołania `CreateLogger` z w pełni kwalifikowaną nazwą typu `T`.

## <a name="log-level"></a>Poziom dziennika

Każdy dziennik Określa <xref:Microsoft.Extensions.Logging.LogLevel> wartość. Poziom dziennika wskazuje ważność lub ważność. Przykładowo można napisać `Information` dziennik, gdy metoda jest zwykle zakończona, a `Warning` dziennik, gdy metoda zwraca 404, *nie znaleziono* kodu stanu.

Poniższy kod tworzy `Information` i `Warning` rejestruje:

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

W powyższym kodzie pierwszy parametr jest [identyfikatorem zdarzenia dziennika](#log-event-id). Drugi parametr jest szablonem wiadomości z symbolami zastępczymi dla wartości argumentów dostarczonych przez pozostałe parametry metody. Parametry metody zostały wyjaśnione w [sekcji szablon komunikatu](#log-message-template) w dalszej części tego artykułu.

Metody rejestrowania, które obejmują poziom w nazwie metody (na przykład `LogInformation` i `LogWarning`) są [metodami rozszerzającymi dla ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions). Te metody wywołują `Log` metodę, która pobiera `LogLevel` parametr. `Log` Metodę można wywołać bezpośrednio zamiast jednej z tych metod rozszerzających, ale składnia jest stosunkowo skomplikowana. Aby uzyskać więcej informacji, <xref:Microsoft.Extensions.Logging.ILogger> Zobacz i [kod źródłowy rozszerzeń rejestratora](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).

ASP.NET Core definiuje następujące poziomy dziennika uporządkowane w tym miejscu od najniższej do najwyższej wagi.

* Ślad = 0

  Aby uzyskać informacje, które są zazwyczaj cenne tylko dla debugowania. Komunikaty te mogą zawierać poufne dane aplikacji, dlatego nie powinny być włączone w środowisku produkcyjnym. *Domyślnie wyłączona.*

* Debuguj = 1

  Informacje, które mogą być przydatne podczas tworzenia i debugowania. Przykład: `Entering method Configure with flag set to true.` Włącz `Debug` dzienniki poziomów w środowisku produkcyjnym tylko w przypadku rozwiązywania problemów, ze względu na dużą ilość dzienników.

* Informacje = 2

  Do śledzenia ogólnego przepływu aplikacji. Te dzienniki zwykle mają pewną wartość długoterminową. Przykład: `Request received for path /api/todo`

* Ostrzeżenie = 3

  Dla nietypowych lub nieoczekiwanych zdarzeń w przepływie aplikacji. Mogą to być błędy lub inne warunki, które nie powodują zatrzymania aplikacji, ale konieczne może być zbadanie. Obsłużone wyjątki są typowym miejscem do korzystania `Warning` z poziomu dziennika. Przykład: `FileNotFoundException for file quotes.txt.`

* Błąd = 4

  W przypadku błędów i wyjątków, których nie można obsłużyć. Te komunikaty wskazują niepowodzenie w bieżącym działaniu lub operacji (np. bieżące żądanie HTTP), a nie awaria całej aplikacji. Przykładowy komunikat dziennika:`Cannot insert record due to duplicate key violation.`

* Krytyczne = 5

  Dla niepowodzeń, które wymagają natychmiastowej uwagi. Przykłady: scenariusze utraty danych, brak miejsca na dysku.

Poziom dziennika służy do kontrolowania, ile danych wyjściowych dziennika jest zapisywana w określonym nośniku lub oknie wyświetlania. Przykład:

* W środowisku produkcyjnym:
  * Rejestrowanie na poziomie `Trace` za `Information` pomocą poziomów zapewnia wysoką liczbę szczegółowych komunikatów dziennika. Aby kontrolować koszty i nie przekraczać limitów magazynowania danych `Trace` , `Information` należy rejestrować komunikaty na poziomie w magazynach o wysokim poziomie ilości danych.
  * Rejestrowanie na `Warning` poziomie `Critical` poziomów zwykle generuje mniejszą liczbę mniejszych komunikatów dzienników. W związku z tym koszty i limity magazynu zazwyczaj nie są problemem, co skutkuje większą elastycznością wyboru magazynu danych.
* Podczas tworzenia:
  * Rejestruj `Warning` `Critical` komunikaty w konsoli programu.
  * Dodawanie `Trace` `Information` komunikatów podczas rozwiązywania problemów.

W sekcji [filtrowanie dzienników](#log-filtering) w dalszej części tego artykułu wyjaśniono, jak kontrolować poziomy dzienników obsługiwane przez dostawcę.

ASP.NET Core zapisuje dzienniki dla zdarzeń struktury. Przykłady dzienników znajdujące się wcześniej w tym artykule nie `Information` wykluczają dzienników `Debug` poniżej `Trace` , dlatego nie zostały utworzone dzienniki. Oto przykład dzienników konsoli utworzonych przez uruchomienie przykładowej aplikacji skonfigurowanej do wyświetlania `Debug` dzienników:

```console
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[3]
      Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of authorization filters (in the following order): None
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of resource filters (in the following order): Microsoft.AspNetCore.Mvc.ViewFeatures.Filters.SaveTempDataFilter
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of action filters (in the following order): Microsoft.AspNetCore.Mvc.Filters.ControllerActionFilter (Order: -2147483648), Microsoft.AspNetCore.Mvc.ModelBinding.UnsupportedContentTypeFilter (Order: -3000)
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of exception filters (in the following order): None
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of result filters (in the following order): Microsoft.AspNetCore.Mvc.ViewFeatures.Filters.SaveTempDataFilter
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[22]
      Attempting to bind parameter 'id' of type 'System.String' ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.Binders.SimpleTypeModelBinder[44]
      Attempting to bind parameter 'id' of type 'System.String' using the name 'id' in request data ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.Binders.SimpleTypeModelBinder[45]
      Done attempting to bind parameter 'id' of type 'System.String'.
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[23]
      Done attempting to bind parameter 'id' of type 'System.String'.
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[26]
      Attempting to validate the bound parameter 'id' of type 'System.String' ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[27]
      Done attempting to validate the bound parameter 'id' of type 'System.String'.
info: TodoApiSample.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]
      Executed action TodoApiSample.Controllers.TodoController.GetById (TodoApiSample) in 32.690400000000004ms
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 176.9103ms 404
```

## <a name="log-event-id"></a>Identyfikator zdarzenia dziennika

Każdy dziennik może określać *Identyfikator zdarzenia*. Aplikacja Przykładowa wykonuje to przy użyciu lokalnie zdefiniowanej `LoggingEvents` klasy:

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/3.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

Identyfikator zdarzenia kojarzy zestaw zdarzeń. Na przykład wszystkie dzienniki związane z wyświetlaniem listy elementów na stronie mogą być 1001.

Dostawca rejestrowania może przechowywać identyfikator zdarzenia w polu identyfikatora, w komunikacie rejestrowania lub wcale. Dostawca debugowania nie pokazuje identyfikatorów zdarzeń. Dostawca konsoli pokazuje identyfikatory zdarzeń w nawiasach po kategorii:

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a>Szablon komunikatu dziennika

Każdy dziennik Określa szablon wiadomości. Szablon wiadomości może zawierać symbole zastępcze, dla których podano argumenty. Użyj nazw dla symboli zastępczych, a nie liczby.

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

Kolejność symboli zastępczych, nie ich nazw, określa, które parametry są używane do dostarczania ich wartości. W poniższym kodzie Zwróć uwagę, że nazwy parametrów są poza kolejnością w szablonie wiadomości:

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

Ten kod tworzy komunikat dziennika z wartościami parametrów w kolejności:

```text
Parameter values: parm1, parm2
```

Struktura rejestrowania działa w ten sposób, aby dostawcy rejestrowania mogli zaimplementować [Rejestrowanie semantyczne, znane także jako rejestrowanie strukturalne](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging). Same argumenty są przesyłane do systemu rejestrowania, a nie tylko dla sformatowanego szablonu wiadomości. Te informacje umożliwiają dostawcom rejestrowania przechowywanie wartości parametrów jako pól. Na przykład załóżmy, że wywołania metod rejestratora wyglądają następująco:

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

W przypadku wysyłania dzienników do usługi Azure Table Storage każda jednostka tabeli platformy Azure może mieć `ID` właściwości i `RequestTime` , które upraszczają zapytania dotyczące danych dziennika. Zapytanie może znaleźć wszystkie dzienniki w określonym `RequestTime` zakresie bez analizowania limitu czasu wiadomości tekstowej.

## <a name="logging-exceptions"></a>Wyjątki rejestrowania

Metody rejestratora mają przeciążenia umożliwiające przekazanie wyjątku, jak w poniższym przykładzie:

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

Różni dostawcy obsługują informacje o wyjątkach na różne sposoby. Oto przykład danych wyjściowych dostawcy debugowania z kodu pokazanego powyżej.

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a>Filtrowanie dzienników

Można określić minimalny poziom rejestrowania dla określonego dostawcy i kategorii lub dla wszystkich dostawców lub wszystkich kategorii. Wszystkie dzienniki poniżej minimalnego poziomu nie są przesyłane do tego dostawcy, więc nie są wyświetlane ani przechowywane.

Aby pominąć wszystkie dzienniki, określ `LogLevel.None` jako minimalny poziom dziennika. Wartość całkowita `LogLevel.None` wynosi 6, która jest większa niż `LogLevel.Critical` (5).

### <a name="create-filter-rules-in-configuration"></a>Utwórz reguły filtru w konfiguracji

Kod szablonu projektu wywołuje `CreateDefaultBuilder` w celu skonfigurowania rejestrowania dla dostawców konsoli, debugowania i EventSource (ASP.NET Core 2,2 lub nowszych). `CreateDefaultBuilder` Metoda konfiguruje rejestrowanie, aby wyszukać konfigurację w `Logging` sekcji, jak wyjaśniono [wcześniej w tym artykule](#configuration).

Dane konfiguracyjne określają minimalne poziomy dziennika według dostawcy i kategorii, jak w poniższym przykładzie:

[!code-json[](index/samples/3.x/TodoApiSample/appsettings.json)]

Ten kod JSON tworzy sześć reguł filtrowania: jeden dla dostawcy debugowania, cztery dla dostawcy konsoli i jeden dla wszystkich dostawców. Dla każdego dostawcy wybierana jest pojedyncza reguła, `ILogger` gdy tworzony jest obiekt.

### <a name="filter-rules-in-code"></a>Filtrowanie reguł w kodzie

Poniższy przykład pokazuje, jak zarejestrować reguły filtru w kodzie:

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=2-3)]

Drugi `AddFilter` określa dostawcę debugowania za pomocą nazwy typu. Pierwszy `AddFilter` ma zastosowanie do wszystkich dostawców, ponieważ nie określa typu dostawcy.

### <a name="how-filtering-rules-are-applied"></a>Jak są stosowane reguły filtrowania

Dane konfiguracji i `AddFilter` kod przedstawiony w powyższych przykładach tworzą reguły pokazane w poniższej tabeli. Pierwsze sześć pochodzi z przykładu konfiguracji, a ostatnie dwa pochodzą z przykładu kodu.

| Liczba | Dostawca      | Kategorie zaczynające się od...          | Minimalny poziom rejestrowania |
| :----: | ------------- | --------------------------------------- | ----------------- |
| 1      | Debugowanie         | Wszystkie kategorie                          | Informacje       |
| 2      | Konsola       | Microsoft. AspNetCore. MVC. Razor. Internal | Ostrzeżenie           |
| 3      | Konsola       | Microsoft. AspNetCore. MVC. Razor. Razor    | Debugowanie             |
| 4      | Konsola       | Microsoft. AspNetCore. MVC. Razor          | Error             |
| 5      | Konsola       | Wszystkie kategorie                          | Informacje       |
| 6      | Wszyscy dostawcy | Wszystkie kategorie                          | Debugowanie             |
| 7      | Wszyscy dostawcy | System                                  | Debugowanie             |
| 8      | Debugowanie         | Microsoft                               | Ślad             |

Po utworzeniu `ILogger` obiektu `ILoggerFactory` obiekt wybiera jedną regułę dla każdego dostawcy, która ma zostać zastosowana do tego rejestratora. Wszystkie komunikaty zapisywane przez `ILogger` wystąpienie są filtrowane na podstawie wybranych reguł. Najbardziej konkretną regułą można wybrać dla każdego dostawcy i pary kategorii z dostępnych reguł.

Następujący algorytm jest używany dla każdego dostawcy, `ILogger` gdy jest tworzony dla danej kategorii:

* Wybierz wszystkie reguły, które pasują do dostawcy lub jego aliasu. Jeśli nie zostanie znalezione dopasowanie, zaznacz wszystkie reguły z pustym dostawcą.
* W wyniku poprzedniego kroku wybierz pozycję reguły z najdłuższym prefiksem kategorii. Jeśli nie zostanie znalezione dopasowanie, zaznacz wszystkie reguły, które nie określają kategorii.
* Jeśli wybrano wiele reguł, zrób to **ostatnie** .
* Jeśli nie wybrano żadnych reguł, `MinimumLevel`Użyj.

Na powyższej liście reguł Załóżmy, że tworzysz `ILogger` obiekt dla kategorii "Microsoft. AspNetCore. MVC. Razor. RazorViewEngine":

* Dla dostawcy debugowania obowiązują reguły 1, 6 i 8. Reguła 8 jest najbardziej specyficzna, więc jest to jedna wybrana.
* W przypadku dostawcy konsoli obowiązują reguły 3, 4, 5 i 6. Reguła 3 jest najbardziej specyficzna.

Wystąpienie wyników `ILogger` wysyła dzienniki `Trace` poziomu i powyżej do dostawcy debugowania. Dzienniki `Debug` poziomów i powyżej są wysyłane do dostawcy konsoli.

### <a name="provider-aliases"></a>Aliasy dostawców

Każdy dostawca definiuje *alias* , który może być używany w konfiguracji zamiast w pełni kwalifikowanej nazwy typu.  W przypadku dostawców wbudowanych Użyj następujących aliasów:

* Konsola
* Debugowanie
* EventSource
* Elemencie
* TraceSource
* AzureAppServicesFile
* AzureAppServicesBlob
* ApplicationInsights

### <a name="default-minimum-level"></a>Domyślny poziom minimalny

Istnieje ustawienie minimalnego poziomu, które działa tylko wtedy, gdy nie mają zastosowania żadne reguły z konfiguracji lub kodu dla danego dostawcy i kategorii. Poniższy przykład pokazuje, jak ustawić poziom minimalny:

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

Jeśli poziom minimalny nie został jawnie ustawiony, wartość domyślna to `Information`, co oznacza, że `Trace` dzienniki i `Debug` są ignorowane.

### <a name="filter-functions"></a>Funkcje filtrowania

Funkcja filtru jest wywoływana dla wszystkich dostawców i kategorii, które nie mają przypisanych do nich reguł przez konfigurację lub kod. Kod w funkcji ma dostęp do typu dostawcy, kategorii i poziomu dziennika. Przykład:

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=3-11)]

## <a name="system-categories-and-levels"></a>Kategorie i poziomy systemu

Poniżej przedstawiono niektóre kategorie używane przez ASP.NET Core i Entity Framework Core, z informacjami o dziennikach, od których należy się spodziewać:

| Kategoria                            | Uwagi |
| ----------------------------------- | ----- |
| Microsoft. AspNetCore                | Ogólna Diagnostyka ASP.NET Core. |
| Microsoft. AspNetCore. dataprotection | Które klucze zostały wzięte pod uwagę, znaleziono i użyte. |
| Microsoft. AspNetCore. HostFiltering  | Dozwolone hosty. |
| Microsoft. AspNetCore. hosting        | Jak długo trwa wykonywanie żądań HTTP i czas ich uruchomienia. Które hostowanie zestawów uruchamiania zostało załadowane. |
| Microsoft. AspNetCore. MVC            | Diagnostyka MVC i Razor. Powiązanie modelu, wykonywanie filtru, kompilacja widoku, wybór akcji. |
| Microsoft. AspNetCore. Routing        | Informacje o trasie. |
| Microsoft. AspNetCore. Server         | Reagowanie na uruchamianie, zatrzymywanie i utrzymywanie aktywności. Informacje o certyfikacie HTTPS. |
| Microsoft. AspNetCore. StaticFiles    | Obsługiwane pliki. |
| Microsoft. EntityFrameworkCore       | Ogólna Diagnostyka Entity Framework Core. Aktywność i Konfiguracja bazy danych, wykrywanie zmian, migracje. |

## <a name="log-scopes"></a>Zakresy dziennika

 *Zakres* może grupować zestaw operacji logicznych. Takie grupowanie może służyć do dołączania tych samych danych do każdego dziennika, który został utworzony jako część zestawu. Na przykład każdy dziennik utworzony w ramach przetwarzania transakcji może zawierać identyfikator transakcji.

Zakres jest `IDisposable` typem zwracanym przez <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> metodę i obowiązuje do momentu jego usunięcia. Użyj zakresu przez Zawijanie wywołań rejestratora w `using` bloku:

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

Poniższy kod włącza zakresy dla dostawcy konsoli:

*Program.cs*:

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=6)]

> [!NOTE]
> Konfigurowanie opcji `IncludeScopes` rejestratora konsoli jest wymagane do włączenia rejestrowania na podstawie zakresu.
>
> Informacje o konfiguracji znajdują się w sekcji [Konfiguracja](#configuration) .

Każdy komunikat dziennika zawiera informacje o zakresie:

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a>Wbudowani dostawcy rejestrowania

ASP.NET Core dostarcza następujących dostawców:

* [Konsola](#console-provider)
* [Rozpocząć](#debug-provider)
* [EventSource](#event-source-provider)
* [Elemencie](#windows-eventlog-provider)
* [TraceSource](#tracesource-provider)
* [AzureAppServicesFile](#azure-app-service-provider)
* [AzureAppServicesBlob](#azure-app-service-provider)
* [ApplicationInsights](#azure-application-insights-trace-logging)

Aby uzyskać informacje na temat strumienia stdout i rejestrowania debugowania za pomocą modułu <xref:test/troubleshoot-azure-iis> ASP.NET Core <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>, zobacz i.

### <a name="console-provider"></a>Dostawca konsoli

Pakiet [Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) Provider wysyła dane wyjściowe dziennika do konsoli programu. 

```csharp
logging.AddConsole();
```

Aby wyświetlić dane wyjściowe rejestrowania konsoli, Otwórz wiersz polecenia w folderze projektu i uruchom następujące polecenie:

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a>Dostawca debugowania

Pakiet [Microsoft. Extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) Provider zapisuje dane wyjściowe dziennika przy użyciu klasy [System. Diagnostics. Debug](/dotnet/api/system.diagnostics.debug) (`Debug.WriteLine` wywołania metod).

W systemie Linux ten dostawca zapisuje dzienniki do */var/log/Message*.

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a>Dostawca źródła zdarzeń

Pakiet dostawcy [Microsoft. Extensions. Logging. EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) zapisuje na międzyplatformę źródła zdarzeń, używając nazwy `Microsoft-Extensions-Logging`. W systemie Windows Dostawca używa [funkcji ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).

```csharp
logging.AddEventSourceLogger();
```

Dostawca źródła zdarzeń zostanie dodany automatycznie, gdy `CreateDefaultBuilder` jest wywoływana w celu skompilowania hosta.

#### <a name="dotnet-trace-tooling"></a>narzędzia śledzenia dotnet

Narzędzie do [śledzenia dotnet](/dotnet/core/diagnostics/dotnet-trace) to międzyplatformowe narzędzie globalne interfejsu wiersza polecenia, które umożliwia zbieranie śladów programu .NET Core uruchomionego procesu. Narzędzie zbiera <xref:Microsoft.Extensions.Logging.EventSource> dane dostawcy za pomocą <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.

Zainstaluj narzędzia śledzenia dotnet przy użyciu następującego polecenia:

```dotnetcli
dotnet tool install --global dotnet-trace
```

Użyj narzędzi śledzenia dotnet, aby zebrać ślad z aplikacji:

1. Jeśli aplikacja nie kompiluje hosta za pomocą `CreateDefaultBuilder`programu, Dodaj [dostawcę źródła zdarzeń](#event-source-provider) do konfiguracji rejestrowania aplikacji.

1. Uruchom aplikację za pomocą `dotnet run` polecenia.

1. Określ identyfikator procesu (PID) aplikacji .NET Core:

   * W systemie Windows należy użyć jednej z następujących metod:
     * Menedżer zadań (Ctrl + Alt + Del)
     * [tasklist — polecenie](/windows-server/administration/windows-commands/tasklist)
     * [Get-Process — polecenie programu PowerShell](/powershell/module/microsoft.powershell.management/get-process)
   * W systemie Linux Użyj [polecenia pidof](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).

   Znajdź identyfikator PID procesu, który ma taką samą nazwę jak zestaw aplikacji.

1. Wykonaj `dotnet trace` polecenie.

   Ogólna składnia poleceń:

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   W przypadku korzystania z powłoki poleceń programu PowerShell należy ująć `--providers` wartość w apostrofy (`'`):

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   Na platformach innych niż Windows Dodaj `-f speedscope` opcję, aby zmienić format wyjściowego pliku śledzenia na. `speedscope`

   | Słowo kluczowe | Opis |
   | :-----: | ----------- |
   | 1       | Rejestruj meta zdarzenia dotyczące `LoggingEventSource`. Nie rejestruje zdarzeń z `ILogger`). |
   | 2       | Włącza zdarzenie, `Message` gdy `ILogger.Log()` jest wywoływana. Zawiera informacje w sposób programistyczny (nie sformatowany). |
   | 4       | Włącza zdarzenie, `FormatMessage` gdy `ILogger.Log()` jest wywoływana. Udostępnia sformatowaną wersję ciągu informacji. |
   | 8       | Włącza zdarzenie, `MessageJson` gdy `ILogger.Log()` jest wywoływana. Zawiera reprezentację argumentów w formacie JSON. |

   | Poziom zdarzenia | Opis     |
   | :---------: | --------------- |
   | 0           | `LogAlways`     |
   | 1           | `Critical`      |
   | 2           | `Error`         |
   | 3           | `Warning`       |
   | 4           | `Informational` |
   | 5           | `Verbose`       |

   `FilterSpecs`wpisy dla `{Logger Category}` i `{Event Level}` przedstawiają dodatkowe warunki filtrowania dzienników. Oddzielaj `FilterSpecs` wpisy średnikami (`;`).

   Przykład użycia powłoki poleceń systemu Windows (**Brak** pojedynczego cudzysłowu wokół `--providers` wartości):

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   Poprzednie polecenie aktywuje:

   * Rejestrator źródła zdarzeń do tworzenia sformatowanych ciągów (`4`) dla błędów (`2`).
   * `Microsoft.AspNetCore.Hosting`Rejestrowanie na poziomie `Informational` rejestrowania (`4`).

1. Zatrzymaj narzędzia śledzenia dotnet, naciskając klawisz ENTER lub CTRL + C.

   Ślad jest zapisywany z nazwą *Trace. webtrace* w folderze, w którym jest `dotnet trace` wykonywane polecenie.

1. Otwórz ślad z [Narzędzia PerfView](#perfview). Otwórz plik *Trace. webtrace* i zbadaj zdarzenia śledzenia.

Aby uzyskać więcej informacji, zobacz:

* [Śledzenie dla narzędzia do analizy wydajności (program dotnet-Trace)](/dotnet/core/diagnostics/dotnet-trace) (dokumentacja platformy .NET Core)
* [Śledzenie dla narzędzia analizy wydajności (dotnet-Trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dokumentacja repozytorium dotnet/Diagnostics)
* [LoggingEventSource — Klasa](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (przeglądarka interfejsu API platformy .NET)
* <xref:System.Diagnostics.Tracing.EventLevel>
* [Źródło odwołania LoggingEventSource (3,0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; Aby uzyskać Źródło odwołania dla innej wersji, Zmień gałąź na `release/{Version}`, gdzie `{Version}` jest pożądaną wersją ASP.NET Core.
* [Narzędzia PerfView](#perfview) &ndash; przydatny do wyświetlania śladów źródła zdarzeń.

#### <a name="perfview"></a>Narzędzia PerfView

Użyj [Narzędzia Narzędzia PerfView](https://github.com/Microsoft/perfview) do zbierania i wyświetlania dzienników. Istnieją inne narzędzia do wyświetlania dzienników ETW, ale narzędzia PerfView zapewnia najlepsze środowisko pracy z zdarzeniami ETW emitowanymi przez ASP.NET Core.

Aby skonfigurować narzędzia PerfView do zbierania zdarzeń rejestrowanych przez tego dostawcę, Dodaj ciąg `*Microsoft-Extensions-Logging` do listy **dodatkowych dostawców** . (Nie przegap gwiazdki na początku ciągu znaków).

![Narzędzia PerfView dodatkowych dostawców](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a>Dostawca dziennika zdarzeń systemu Windows

Pakiet dostawcy [Microsoft. Extensions. Logging. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) wysyła dane wyjściowe dziennika do dziennika zdarzeń systemu Windows.

```csharp
logging.AddEventLog();
```

[Przeciążenia addeventlog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) umożliwiają przekazywanie <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>. Jeśli `null` lub nie zostanie określony, są używane następujące ustawienia domyślne:

* `LogName`&ndash; "Aplikacja"
* `SourceName`&ndash; "Środowisko uruchomieniowe platformy .NET"
* `MachineName`&ndash; komputer lokalny

Zdarzenia są rejestrowane dla [poziomu ostrzeżeń i wyższych](#log-level). Aby rejestrować zdarzenia mniejsze niż `Warning`, jawnie ustaw poziom dziennika. Na przykład Dodaj następujący kod do pliku *appSettings. JSON* :

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a>Dostawca TraceSource

Pakiet dostawcy [Microsoft. Extensions. Logging. TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) używa <xref:System.Diagnostics.TraceSource> bibliotek i dostawców.

```csharp
logging.AddTraceSource(sourceSwitchName);
```

[Przeciążenia AddTraceSource](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) umożliwiają przekazywanie danych w przełączniku źródłowym i odbiorniku śledzenia.

Aby można było korzystać z tego dostawcy, aplikacja musi działać na .NET Framework (a nie na platformie .NET Core). Dostawca może kierować komunikaty do różnych [odbiorników](/dotnet/framework/debug-trace-profile/trace-listeners), takich jak <xref:System.Diagnostics.TextWriterTraceListener> używane w przykładowej aplikacji.

### <a name="azure-app-service-provider"></a>Dostawca Azure App Service

Pakiet [Microsoft. Extensions. Logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) Provider zapisuje dzienniki w plikach tekstowych w systemie plików aplikacji Azure App Service i w usłudze [BLOB Storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) na koncie usługi Azure Storage.

```csharp
logging.AddAzureWebAppDiagnostics();
```

Pakiet dostawcy nie jest uwzględniony w udostępnionej infrastrukturze. Aby użyć dostawcy, Dodaj pakiet dostawcy do projektu.

Aby skonfigurować ustawienia dostawcy, użyj <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> i <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, jak pokazano w następującym przykładzie:

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=17-28)]

Po wdrożeniu w aplikacji App Service, aplikacja będzie przestrzegać ustawień w sekcji [dzienniki App Service](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) na stronie **App Service** Azure Portal. Po zaktualizowaniu następujących ustawień zmiany zaczynają obowiązywać natychmiast, bez konieczności ponownego uruchomienia lub ponownej wdrożenia aplikacji.

* **Rejestrowanie aplikacji (system plików)**
* **Rejestrowanie aplikacji (BLOB)**

Domyślną lokalizacją plików dziennika jest folder *D:\\Home\\LogFiles\\* , a domyślna nazwa pliku to *Diagnostics-YYYYMMDD. txt*. Domyślny limit rozmiaru pliku wynosi 10 MB, a domyślna maksymalna liczba zachowywanych plików to 2. Domyślną nazwą obiektu BLOB jest *{App-Name} {timestamp}/yyyy/mm/dd/hh/{GUID}-applicationLog.txt*.

Dostawca działa tylko wtedy, gdy projekt jest uruchomiony w środowisku platformy Azure. Nie ma ono wpływu, gdy projekt jest uruchamiany lokalnie&mdash;, nie zapisuje w plikach lokalnych ani w lokalnym magazynie programistycznym dla obiektów BLOB.

#### <a name="azure-log-streaming"></a>Przesyłanie strumieniowe dzienników Azure

Usługa przesyłania strumieniowego w usłudze Azure log umożliwia wyświetlanie dziennika aktywności w czasie rzeczywistym z:

* Serwer aplikacji
* Serwer sieci Web
* Śledzenie nieudanych żądań

Aby skonfigurować przesyłanie strumieniowe dzienników Azure:

* Przejdź do strony **dzienników App Service** ze strony portalu aplikacji.
* Ustaw **Rejestrowanie aplikacji (system plików)** na **włączone**.
* Wybierz **poziom**dziennika. To ustawienie dotyczy tylko przesyłania strumieniowego dzienników platformy Azure, a nie innych dostawców rejestrowania w aplikacji.

Przejdź do strony **strumień dziennika** , aby wyświetlić komunikaty aplikacji. Są one rejestrowane przez aplikację za pomocą `ILogger` interfejsu.

### <a name="azure-application-insights-trace-logging"></a>Rejestrowanie śledzenia Application Insights platformy Azure

Pakiet [Microsoft. Extensions. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) Provider zapisuje dzienniki na platformie Azure Application Insights. Application Insights to usługa, która monitoruje aplikację internetową i udostępnia narzędzia do wykonywania zapytań i analizowania danych telemetrycznych. Jeśli używasz tego dostawcy, możesz wysyłać zapytania i analizować dzienniki przy użyciu narzędzi Application Insights.

Dostawca rejestrowania jest dołączony jako zależność [Microsoft. ApplicationInsights. AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), który jest pakietem, który zapewnia wszystkie dostępne dane telemetryczne dla ASP.NET Core. Jeśli używasz tego pakietu, nie musisz instalować pakietu dostawcy.

Nie używaj&mdash;pakietu [Microsoft. ApplicationInsights. Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) , który jest przeznaczony dla ASP.NET 4. x.

Więcej informacji zawierają następujące zasoby:

* [Omówienie usługi Application Insights](/azure/application-insights/app-insights-overview)
* [Application Insights dla ASP.NET Core aplikacji](/azure/azure-monitor/app/asp-net-core) — Zacznij tutaj, jeśli chcesz zaimplementować cały zakres Application Insights telemetrii wraz z rejestrowaniem.
* [ApplicationInsightsLoggerProvider for .NET Core ILogger](/azure/azure-monitor/app/ilogger) — Rozpocznij tutaj, jeśli chcesz zaimplementować dostawcę rejestrowania bez pozostałej części telemetrii Application Insights.
* [Karty rejestrowania Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).
* [Zainstaluj, skonfiguruj i zainicjuj samouczek Application INSIGHTS SDK](/learn/modules/instrument-web-app-code-with-application-insights) — interaktywny w witrynie Microsoft Learn.

## <a name="third-party-logging-providers"></a>Dostawcy rejestrowania innych firm

Platformy rejestrowania innych firm, które współpracują z ASP.NET Core:

* [ELMAH.IO](https://elmah.io/) ([repozytorium GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))
* [Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([repozytorium GitHub](https://github.com/mattwcole/gelf-extensions-logging))
* [JSNLog](https://jsnlog.com/) ([repozytorium GitHub](https://github.com/mperdeck/jsnlog))
* [KissLog.NET](https://kisslog.net/) ([repozytorium GitHub](https://github.com/catalingavan/KissLog-net))
* [Log4Net](https://logging.apache.org/log4net/) ([repozytorium GitHub](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))
* [Loggr](https://loggr.net/) ([repozytorium GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))
* [NLOG](https://nlog-project.org/) ([repozytorium GitHub](https://github.com/NLog/NLog.Extensions.Logging))
* [Sentry](https://sentry.io/welcome/) ([repozytorium GitHub](https://github.com/getsentry/sentry-dotnet))
* [Serilog](https://serilog.net/) ([repozytorium GitHub](https://github.com/serilog/serilog-aspnetcore))
* [Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([repozytorium GitHub](https://github.com/googleapis/google-cloud-dotnet))

Niektóre struktury innych firm mogą wykonywać [Rejestrowanie semantyczne, znane także jako rejestrowanie strukturalne](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Korzystanie z struktury innej firmy jest podobne do korzystania z jednego z wbudowanych dostawców:

1. Dodaj pakiet NuGet do projektu.
1. Wywoływanie `ILoggerFactory` metody rozszerzenia dostarczonej przez strukturę rejestrowania.

Aby uzyskać więcej informacji, zobacz dokumentację każdego dostawcy. Dostawcy rejestrowania innych firm nie są obsługiwani przez firmę Microsoft.

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:fundamentals/logging/loggermessage>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

Autorzy [Dykstra](https://github.com/tdykstra) i [Steve Smith](https://ardalis.com/)

Platforma .NET Core obsługuje interfejs API rejestrowania, który współpracuje z różnymi dostawcami rejestrowania wbudowanych i innych firm. W tym artykule pokazano, jak używać interfejsu API rejestrowania z wbudowanymi dostawcami.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="add-providers"></a>Dodaj dostawców

Dostawca rejestrowania wyświetla lub przechowuje dzienniki. Na przykład dostawca konsoli wyświetla dzienniki w konsoli programu, a Dostawca usługi Azure Application Insights przechowuje je na platformie Azure Application Insights. Dzienniki mogą być wysyłane do wielu miejsc docelowych przez dodanie wielu dostawców.

Aby dodać dostawcę, wywołaj metodę `Add{provider name}` rozszerzenia dostawcy w *program.cs*:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

Poprzedzający kod wymaga odwołania do `Microsoft.Extensions.Logging` i `Microsoft.Extensions.Configuration`.

Domyślne wywołania <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>szablonu projektu, które dodaje następujących dostawców rejestrowania:

* Konsola
* Debugowanie
* EventSource (rozpoczęcie w ASP.NET Core 2,2)

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

Jeśli używasz `CreateDefaultBuilder`programu, możesz zastąpić domyślnych dostawców własnymi opcjami. Wywołaj <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>i Dodaj żądanych dostawców.

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

Więcej informacji na temat [wbudowanych dostawców rejestrowania](#built-in-logging-providers) i [dostawców rejestrowania innych](#third-party-logging-providers) firm znajduje się w dalszej części artykułu.

## <a name="create-logs"></a>Tworzenie dzienników

Aby utworzyć dzienniki, użyj <xref:Microsoft.Extensions.Logging.ILogger%601> obiektu. W aplikacji sieci Web lub hostowanej usłudze Pobierz `ILogger` od iniekcji zależności (di). W aplikacjach konsolowych innych niż host Użyj programu `LoggerFactory` , aby utworzyć `ILogger`.

Poniższy ASP.NET Core przykład tworzy Rejestrator przy użyciu `TodoApiSample.Pages.AboutModel` jako kategorii. *Kategoria* dziennika jest ciągiem, który jest skojarzony z każdym dziennikiem. `ILogger<T>` Wystąpienie zapewniane przez program di tworzy dzienniki, które mają w pełni kwalifikowaną nazwę `T` typu jako kategorię. 

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

W poniższych przykładach ASP.NET Core i aplikacji konsoli Rejestrator służy do tworzenia dzienników z użyciem `Information` jako poziom. *Poziom* dziennika wskazuje ważność rejestrowanego zdarzenia.

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[Poziomy](#log-level) i [Kategorie](#log-category) zostały wyjaśnione bardziej szczegółowo w dalszej części tego artykułu.

### <a name="create-logs-in-startup"></a>Tworzenie dzienników w programie startowym

Aby napisać dzienniki w `Startup` klasie, Dołącz `ILogger` parametr do sygnatury konstruktora:

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a>Tworzenie dzienników w klasie programu

Aby napisać dzienniki w `Program` klasie, Pobierz wystąpienie z `ILogger` elementu di:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

Rejestrowanie podczas konstruowania hosta nie jest bezpośrednio obsługiwane. Można jednak użyć osobnego rejestratora. W poniższym przykładzie jest używany Rejestrator [Serilog](https://serilog.net/) do logowania `CreateWebHostBuilder`. `AddSerilog`używa konfiguracji statycznej określonej w `Log.Logger`:

```csharp
using System;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return WebHost.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddMvc();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {
                    logging.AddSerilog();
                })
                .UseStartup<Startup>();
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

### <a name="no-asynchronous-logger-methods"></a>Brak metod rejestratora asynchronicznego

Rejestracja powinna być tak szybka, że nie jest to koszt wydajności kodu asynchronicznego. Jeśli magazyn danych rejestrowania jest wolny, nie zapisuj go bezpośrednio. Najpierw Rozważ zapisanie komunikatów dziennika do szybkiego sklepu, a następnie przeniesienie ich do wolnego magazynu później. Na przykład jeśli rejestrujesz się do SQL Server, nie chcesz tego robić bezpośrednio w `Log` metodzie, ponieważ `Log` metody są synchroniczne. Zamiast tego można synchronicznie dodawać komunikaty dziennika do kolejki w pamięci, a proces roboczy w tle ściągał komunikaty z kolejki, aby wykonać asynchroniczne działanie wypychania danych do SQL Server. Aby uzyskać więcej informacji, zobacz [ten](https://github.com/dotnet/AspNetCore.Docs/issues/11801) problem w serwisie GitHub.

## <a name="configuration"></a>Konfigurowanie

Konfiguracja dostawcy rejestrowania jest świadczona przez co najmniej jednego dostawcę konfiguracji:

* Formaty plików (INI, JSON i XML).
* Argumenty wiersza polecenia.
* Zmienne środowiskowe.
* Obiekty platformy .NET w pamięci.
* Magazyn niezaszyfrowanego [klucza tajnego](xref:security/app-secrets) .
* Zaszyfrowany magazyn użytkowników, taki jak [Azure Key Vault](xref:security/key-vault-configuration).
* Dostawcy niestandardowi (instalowani lub utworzony).

Na przykład konfiguracja rejestrowania jest zwykle dostarczana przez `Logging` sekcję plików ustawień aplikacji. Poniższy przykład pokazuje zawartość typowej wartości *appSettings. Plik Development. JSON* :

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    },
    "Console":
    {
      "IncludeScopes": true
    }
  }
}
```

`Logging` Właściwość może mieć `LogLevel` właściwości dostawcy dzienników i.

`LogLevel` Właściwość w obszarze `Logging` określa minimalny [poziom](#log-level) rejestrowania wybranych kategorii. `System` W przykładzie, `Microsoft` i kategorie są rejestrowane na `Information` poziomie, a wszystkie inne logowania na `Debug` poziomie.

Inne właściwości w `Logging` obszarze Określ dostawców rejestrowania. Przykład dotyczy dostawcy konsoli. Jeśli dostawca obsługuje [zakresy rejestrowania](#log-scopes), wskazuje `IncludeScopes` , czy są one włączone. Właściwość dostawcy (taka jak `Console` w przykładzie) może także określić `LogLevel` właściwość. `LogLevel`w obszarze dostawca Określa poziomy do rejestrowania dla tego dostawcy.

Jeśli w programie `Logging.{providername}.LogLevel`są określone poziomy, zastępują one wszystko `Logging.LogLevel`ustawione w.

Interfejs API rejestrowania nie zawiera scenariusza zmiany poziomów rejestrowania, gdy aplikacja jest uruchomiona. Niektórzy dostawcy konfiguracji mogą jednak ponownie ładować konfigurację, która zacznie natychmiastowo wpływać na konfigurację rejestrowania. Na przykład [dostawca konfiguracji plików](xref:fundamentals/configuration/index#file-configuration-provider), który jest dodawany przez `CreateDefaultBuilder` program do odczytu plików ustawień, ponownie ładuje konfigurację rejestrowania domyślnie. Jeśli konfiguracja zostanie zmieniona w kodzie w czasie, gdy aplikacja jest uruchomiona, aplikacja może wywołać [IConfigurationRoot. reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) , aby zaktualizować konfigurację rejestrowania aplikacji.

Informacje o implementowaniu dostawców konfiguracji znajdują <xref:fundamentals/configuration/index>się w temacie.

## <a name="sample-logging-output"></a>Przykładowe dane wyjściowe rejestrowania

W przypadku przykładowego kodu podanego w poprzedniej sekcji Dzienniki są wyświetlane w konsoli programu, gdy aplikacja jest uruchamiana z wiersza polecenia. Oto przykład danych wyjściowych konsoli:

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 42.9286ms
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 148.889ms 404
```

Poprzednie dzienniki zostały wygenerowane przez utworzenie żądania HTTP GET do przykładowej aplikacji w lokalizacji `http://localhost:5000/api/todo/0`.

Oto przykład tych samych dzienników, które są wyświetlane w oknie debugowania podczas uruchamiania przykładowej aplikacji w programie Visual Studio:

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

Dzienniki utworzone przez `ILogger` wywołania pokazane w poprzedniej sekcji zaczynają się od "TodoApi". Dzienniki zaczynające się od kategorii "Microsoft" pochodzą z kodu ASP.NET Core Framework. ASP.NET Core i kod aplikacji używają tego samego rejestrowania interfejsu API i dostawców.

W pozostałej części tego artykułu opisano niektóre szczegóły i opcje rejestrowania.

## <a name="nuget-packages"></a>Pakiety NuGet

Interfejsy `ILogger` i `ILoggerFactory` są w [Microsoft. Extensions. Logging. Abstracts](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/)i domyślne implementacje dla nich są w [Microsoft. Extensions. Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).

## <a name="log-category"></a>Kategoria dziennika

Po utworzeniu `ILogger` obiektu jest dla niego określona *Kategoria* . Ta kategoria jest dołączona do każdego komunikatu dziennika utworzonego przez to wystąpienie `ILogger`. Kategoria może być dowolnym ciągiem, ale Konwencja ma używać nazwy klasy, takiej jak "TodoApi. controllers. TodoController".

Użyj `ILogger<T>` , aby uzyskać `ILogger` wystąpienie używające w pełni kwalifikowanej nazwy typu `T` jako kategorii:

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

Aby jawnie określić kategorię, wywołaj `ILoggerFactory.CreateLogger`:

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

`ILogger<T>`jest odpowiednikiem wywołania `CreateLogger` z w pełni kwalifikowaną nazwą typu `T`.

## <a name="log-level"></a>Poziom dziennika

Każdy dziennik Określa <xref:Microsoft.Extensions.Logging.LogLevel> wartość. Poziom dziennika wskazuje ważność lub ważność. Przykładowo można napisać `Information` dziennik, gdy metoda jest zwykle zakończona, a `Warning` dziennik, gdy metoda zwraca 404, *nie znaleziono* kodu stanu.

Poniższy kod tworzy `Information` i `Warning` rejestruje:

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

W powyższym kodzie pierwszy parametr jest [identyfikatorem zdarzenia dziennika](#log-event-id). Drugi parametr jest szablonem wiadomości z symbolami zastępczymi dla wartości argumentów dostarczonych przez pozostałe parametry metody. Parametry metody zostały wyjaśnione w [sekcji szablon komunikatu](#log-message-template) w dalszej części tego artykułu.

Metody rejestrowania, które obejmują poziom w nazwie metody (na przykład `LogInformation` i `LogWarning`) są [metodami rozszerzającymi dla ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions). Te metody wywołują `Log` metodę, która pobiera `LogLevel` parametr. `Log` Metodę można wywołać bezpośrednio zamiast jednej z tych metod rozszerzających, ale składnia jest stosunkowo skomplikowana. Aby uzyskać więcej informacji, <xref:Microsoft.Extensions.Logging.ILogger> Zobacz i [kod źródłowy rozszerzeń rejestratora](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).

ASP.NET Core definiuje następujące poziomy dziennika uporządkowane w tym miejscu od najniższej do najwyższej wagi.

* Ślad = 0

  Aby uzyskać informacje, które są zazwyczaj cenne tylko dla debugowania. Komunikaty te mogą zawierać poufne dane aplikacji, dlatego nie powinny być włączone w środowisku produkcyjnym. *Domyślnie wyłączona.*

* Debuguj = 1

  Informacje, które mogą być przydatne podczas tworzenia i debugowania. Przykład: `Entering method Configure with flag set to true.` Włącz `Debug` dzienniki poziomów w środowisku produkcyjnym tylko w przypadku rozwiązywania problemów, ze względu na dużą ilość dzienników.

* Informacje = 2

  Do śledzenia ogólnego przepływu aplikacji. Te dzienniki zwykle mają pewną wartość długoterminową. Przykład: `Request received for path /api/todo`

* Ostrzeżenie = 3

  Dla nietypowych lub nieoczekiwanych zdarzeń w przepływie aplikacji. Mogą to być błędy lub inne warunki, które nie powodują zatrzymania aplikacji, ale konieczne może być zbadanie. Obsłużone wyjątki są typowym miejscem do korzystania `Warning` z poziomu dziennika. Przykład: `FileNotFoundException for file quotes.txt.`

* Błąd = 4

  W przypadku błędów i wyjątków, których nie można obsłużyć. Te komunikaty wskazują niepowodzenie w bieżącym działaniu lub operacji (np. bieżące żądanie HTTP), a nie awaria całej aplikacji. Przykładowy komunikat dziennika:`Cannot insert record due to duplicate key violation.`

* Krytyczne = 5

  Dla niepowodzeń, które wymagają natychmiastowej uwagi. Przykłady: scenariusze utraty danych, brak miejsca na dysku.

Poziom dziennika służy do kontrolowania, ile danych wyjściowych dziennika jest zapisywana w określonym nośniku lub oknie wyświetlania. Przykład:

* W środowisku produkcyjnym:
  * Rejestrowanie na poziomie `Trace` za `Information` pomocą poziomów zapewnia wysoką liczbę szczegółowych komunikatów dziennika. Aby kontrolować koszty i nie przekraczać limitów magazynowania danych `Trace` , `Information` należy rejestrować komunikaty na poziomie w magazynach o wysokim poziomie ilości danych.
  * Rejestrowanie na `Warning` poziomie `Critical` poziomów zwykle generuje mniejszą liczbę mniejszych komunikatów dzienników. W związku z tym koszty i limity magazynu zazwyczaj nie są problemem, co skutkuje większą elastycznością wyboru magazynu danych.
* Podczas tworzenia:
  * Rejestruj `Warning` `Critical` komunikaty w konsoli programu.
  * Dodawanie `Trace` `Information` komunikatów podczas rozwiązywania problemów.

W sekcji [filtrowanie dzienników](#log-filtering) w dalszej części tego artykułu wyjaśniono, jak kontrolować poziomy dzienników obsługiwane przez dostawcę.

ASP.NET Core zapisuje dzienniki dla zdarzeń struktury. Przykłady dzienników znajdujące się wcześniej w tym artykule nie `Information` wykluczają dzienników `Debug` poniżej `Trace` , dlatego nie zostały utworzone dzienniki. Oto przykład dzienników konsoli utworzonych przez uruchomienie przykładowej aplikacji skonfigurowanej do wyświetlania `Debug` dzienników:

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:62555/api/todo/0
dbug: Microsoft.AspNetCore.Routing.Tree.TreeRouter[1]
      Request successfully matched the route with name 'GetTodo' and template 'api/Todo/{id}'.
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Update (TodoApi)' with id '089d59b6-92ec-472d-b552-cc613dfd625d' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Delete (TodoApi)' with id 'f3476abe-4bd9-4ad3-9261-3ead09607366' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action TodoApi.Controllers.TodoController.GetById (TodoApi)
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action method TodoApi.Controllers.TodoController.GetById (TodoApi), returned result Microsoft.AspNetCore.Mvc.NotFoundResult.
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 0.8788ms
dbug: Microsoft.AspNetCore.Server.Kestrel[9]
      Connection id "0HL6L7NEFF2QD" completed keep alive response.
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 2.7286ms 404
```

## <a name="log-event-id"></a>Identyfikator zdarzenia dziennika

Każdy dziennik może określać *Identyfikator zdarzenia*. Aplikacja Przykładowa wykonuje to przy użyciu lokalnie zdefiniowanej `LoggingEvents` klasy:

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

Identyfikator zdarzenia kojarzy zestaw zdarzeń. Na przykład wszystkie dzienniki związane z wyświetlaniem listy elementów na stronie mogą być 1001.

Dostawca rejestrowania może przechowywać identyfikator zdarzenia w polu identyfikatora, w komunikacie rejestrowania lub wcale. Dostawca debugowania nie pokazuje identyfikatorów zdarzeń. Dostawca konsoli pokazuje identyfikatory zdarzeń w nawiasach po kategorii:

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a>Szablon komunikatu dziennika

Każdy dziennik Określa szablon wiadomości. Szablon wiadomości może zawierać symbole zastępcze, dla których podano argumenty. Użyj nazw dla symboli zastępczych, a nie liczby.

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

Kolejność symboli zastępczych, nie ich nazw, określa, które parametry są używane do dostarczania ich wartości. W poniższym kodzie Zwróć uwagę, że nazwy parametrów są poza kolejnością w szablonie wiadomości:

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

Ten kod tworzy komunikat dziennika z wartościami parametrów w kolejności:

```text
Parameter values: parm1, parm2
```

Struktura rejestrowania działa w ten sposób, aby dostawcy rejestrowania mogli zaimplementować [Rejestrowanie semantyczne, znane także jako rejestrowanie strukturalne](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging). Same argumenty są przesyłane do systemu rejestrowania, a nie tylko dla sformatowanego szablonu wiadomości. Te informacje umożliwiają dostawcom rejestrowania przechowywanie wartości parametrów jako pól. Na przykład załóżmy, że wywołania metod rejestratora wyglądają następująco:

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

W przypadku wysyłania dzienników do usługi Azure Table Storage każda jednostka tabeli platformy Azure może mieć `ID` właściwości i `RequestTime` , które upraszczają zapytania dotyczące danych dziennika. Zapytanie może znaleźć wszystkie dzienniki w określonym `RequestTime` zakresie bez analizowania limitu czasu wiadomości tekstowej.

## <a name="logging-exceptions"></a>Wyjątki rejestrowania

Metody rejestratora mają przeciążenia umożliwiające przekazanie wyjątku, jak w poniższym przykładzie:

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

Różni dostawcy obsługują informacje o wyjątkach na różne sposoby. Oto przykład danych wyjściowych dostawcy debugowania z kodu pokazanego powyżej.

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a>Filtrowanie dzienników

Można określić minimalny poziom rejestrowania dla określonego dostawcy i kategorii lub dla wszystkich dostawców lub wszystkich kategorii. Wszystkie dzienniki poniżej minimalnego poziomu nie są przesyłane do tego dostawcy, więc nie są wyświetlane ani przechowywane.

Aby pominąć wszystkie dzienniki, określ `LogLevel.None` jako minimalny poziom dziennika. Wartość całkowita `LogLevel.None` wynosi 6, która jest większa niż `LogLevel.Critical` (5).

### <a name="create-filter-rules-in-configuration"></a>Utwórz reguły filtru w konfiguracji

Kod szablonu projektu wywołuje `CreateDefaultBuilder` w celu skonfigurowania rejestrowania dla dostawców konsoli, debugowania i EventSource (ASP.NET Core 2,2 lub nowszych). `CreateDefaultBuilder` Metoda konfiguruje rejestrowanie, aby wyszukać konfigurację w `Logging` sekcji, jak wyjaśniono [wcześniej w tym artykule](#configuration).

Dane konfiguracyjne określają minimalne poziomy dziennika według dostawcy i kategorii, jak w poniższym przykładzie:

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

Ten kod JSON tworzy sześć reguł filtrowania: jeden dla dostawcy debugowania, cztery dla dostawcy konsoli i jeden dla wszystkich dostawców. Dla każdego dostawcy wybierana jest pojedyncza reguła, `ILogger` gdy tworzony jest obiekt.

### <a name="filter-rules-in-code"></a>Filtrowanie reguł w kodzie

Poniższy przykład pokazuje, jak zarejestrować reguły filtru w kodzie:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

Drugi `AddFilter` określa dostawcę debugowania za pomocą nazwy typu. Pierwszy `AddFilter` ma zastosowanie do wszystkich dostawców, ponieważ nie określa typu dostawcy.

### <a name="how-filtering-rules-are-applied"></a>Jak są stosowane reguły filtrowania

Dane konfiguracji i `AddFilter` kod przedstawiony w powyższych przykładach tworzą reguły pokazane w poniższej tabeli. Pierwsze sześć pochodzi z przykładu konfiguracji, a ostatnie dwa pochodzą z przykładu kodu.

| Liczba | Dostawca      | Kategorie zaczynające się od...          | Minimalny poziom rejestrowania |
| :----: | ------------- | --------------------------------------- | ----------------- |
| 1      | Debugowanie         | Wszystkie kategorie                          | Informacje       |
| 2      | Konsola       | Microsoft. AspNetCore. MVC. Razor. Internal | Ostrzeżenie           |
| 3      | Konsola       | Microsoft. AspNetCore. MVC. Razor. Razor    | Debugowanie             |
| 4      | Konsola       | Microsoft. AspNetCore. MVC. Razor          | Error             |
| 5      | Konsola       | Wszystkie kategorie                          | Informacje       |
| 6      | Wszyscy dostawcy | Wszystkie kategorie                          | Debugowanie             |
| 7      | Wszyscy dostawcy | System                                  | Debugowanie             |
| 8      | Debugowanie         | Microsoft                               | Ślad             |

Po utworzeniu `ILogger` obiektu `ILoggerFactory` obiekt wybiera jedną regułę dla każdego dostawcy, która ma zostać zastosowana do tego rejestratora. Wszystkie komunikaty zapisywane przez `ILogger` wystąpienie są filtrowane na podstawie wybranych reguł. Najbardziej konkretną regułą można wybrać dla każdego dostawcy i pary kategorii z dostępnych reguł.

Następujący algorytm jest używany dla każdego dostawcy, `ILogger` gdy jest tworzony dla danej kategorii:

* Wybierz wszystkie reguły, które pasują do dostawcy lub jego aliasu. Jeśli nie zostanie znalezione dopasowanie, zaznacz wszystkie reguły z pustym dostawcą.
* W wyniku poprzedniego kroku wybierz pozycję reguły z najdłuższym prefiksem kategorii. Jeśli nie zostanie znalezione dopasowanie, zaznacz wszystkie reguły, które nie określają kategorii.
* Jeśli wybrano wiele reguł, zrób to **ostatnie** .
* Jeśli nie wybrano żadnych reguł, `MinimumLevel`Użyj.

Na powyższej liście reguł Załóżmy, że tworzysz `ILogger` obiekt dla kategorii "Microsoft. AspNetCore. MVC. Razor. RazorViewEngine":

* Dla dostawcy debugowania obowiązują reguły 1, 6 i 8. Reguła 8 jest najbardziej specyficzna, więc jest to jedna wybrana.
* W przypadku dostawcy konsoli obowiązują reguły 3, 4, 5 i 6. Reguła 3 jest najbardziej specyficzna.

Wystąpienie wyników `ILogger` wysyła dzienniki `Trace` poziomu i powyżej do dostawcy debugowania. Dzienniki `Debug` poziomów i powyżej są wysyłane do dostawcy konsoli.

### <a name="provider-aliases"></a>Aliasy dostawców

Każdy dostawca definiuje *alias* , który może być używany w konfiguracji zamiast w pełni kwalifikowanej nazwy typu.  W przypadku dostawców wbudowanych Użyj następujących aliasów:

* Konsola
* Debugowanie
* EventSource
* Elemencie
* TraceSource
* AzureAppServicesFile
* AzureAppServicesBlob
* ApplicationInsights

### <a name="default-minimum-level"></a>Domyślny poziom minimalny

Istnieje ustawienie minimalnego poziomu, które działa tylko wtedy, gdy nie mają zastosowania żadne reguły z konfiguracji lub kodu dla danego dostawcy i kategorii. Poniższy przykład pokazuje, jak ustawić poziom minimalny:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

Jeśli poziom minimalny nie został jawnie ustawiony, wartość domyślna to `Information`, co oznacza, że `Trace` dzienniki i `Debug` są ignorowane.

### <a name="filter-functions"></a>Funkcje filtrowania

Funkcja filtru jest wywoływana dla wszystkich dostawców i kategorii, które nie mają przypisanych do nich reguł przez konfigurację lub kod. Kod w funkcji ma dostęp do typu dostawcy, kategorii i poziomu dziennika. Przykład:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

## <a name="system-categories-and-levels"></a>Kategorie i poziomy systemu

Poniżej przedstawiono niektóre kategorie używane przez ASP.NET Core i Entity Framework Core, z informacjami o dziennikach, od których należy się spodziewać:

| Kategoria                            | Uwagi |
| ----------------------------------- | ----- |
| Microsoft. AspNetCore                | Ogólna Diagnostyka ASP.NET Core. |
| Microsoft. AspNetCore. dataprotection | Które klucze zostały wzięte pod uwagę, znaleziono i użyte. |
| Microsoft. AspNetCore. HostFiltering  | Dozwolone hosty. |
| Microsoft. AspNetCore. hosting        | Jak długo trwa wykonywanie żądań HTTP i czas ich uruchomienia. Które hostowanie zestawów uruchamiania zostało załadowane. |
| Microsoft. AspNetCore. MVC            | MVC i Razor Diagnostyka. Powiązanie modelu, wykonywanie filtru, kompilacja widoku, wybór akcji. |
| Microsoft. AspNetCore. Routing        | Informacje o trasie. |
| Microsoft. AspNetCore. Server         | Reagowanie na uruchamianie, zatrzymywanie i utrzymywanie aktywności. Informacje o certyfikacie HTTPS. |
| Microsoft. AspNetCore. StaticFiles    | Obsługiwane pliki. |
| Microsoft. EntityFrameworkCore       | Ogólna Diagnostyka Entity Framework Core. Aktywność i Konfiguracja bazy danych, wykrywanie zmian, migracje. |

## <a name="log-scopes"></a>Zakresy dziennika

 *Zakres* może grupować zestaw operacji logicznych. Takie grupowanie może służyć do dołączania tych samych danych do każdego dziennika, który został utworzony jako część zestawu. Na przykład każdy dziennik utworzony w ramach przetwarzania transakcji może zawierać identyfikator transakcji.

Zakres jest `IDisposable` typem zwracanym przez <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> metodę i obowiązuje do momentu jego usunięcia. Użyj zakresu przez Zawijanie wywołań rejestratora w `using` bloku:

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

Poniższy kod włącza zakresy dla dostawcy konsoli:

*Program.cs*:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> Konfigurowanie opcji `IncludeScopes` rejestratora konsoli jest wymagane do włączenia rejestrowania na podstawie zakresu.
>
> Informacje o konfiguracji znajdują się w sekcji [Konfiguracja](#configuration) .

Każdy komunikat dziennika zawiera informacje o zakresie:

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a>Wbudowani dostawcy rejestrowania

ASP.NET Core dostarcza następujących dostawców:

* [Konsola](#console-provider)
* [Rozpocząć](#debug-provider)
* [EventSource](#event-source-provider)
* [Elemencie](#windows-eventlog-provider)
* [TraceSource](#tracesource-provider)
* [AzureAppServicesFile](#azure-app-service-provider)
* [AzureAppServicesBlob](#azure-app-service-provider)
* [ApplicationInsights](#azure-application-insights-trace-logging)

Aby uzyskać informacje na temat strumienia stdout i rejestrowania debugowania za pomocą modułu <xref:test/troubleshoot-azure-iis> ASP.NET Core <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>, zobacz i.

### <a name="console-provider"></a>Dostawca konsoli

Pakiet [Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) Provider wysyła dane wyjściowe dziennika do konsoli programu. 

```csharp
logging.AddConsole();
```

Aby wyświetlić dane wyjściowe rejestrowania konsoli, Otwórz wiersz polecenia w folderze projektu i uruchom następujące polecenie:

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a>Dostawca debugowania

Pakiet [Microsoft. Extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) Provider zapisuje dane wyjściowe dziennika przy użyciu klasy [System. Diagnostics. Debug](/dotnet/api/system.diagnostics.debug) (`Debug.WriteLine` wywołania metod).

W systemie Linux ten dostawca zapisuje dzienniki do */var/log/Message*.

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a>Dostawca źródła zdarzeń

Pakiet dostawcy [Microsoft. Extensions. Logging. EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) zapisuje na międzyplatformę źródła zdarzeń, używając nazwy `Microsoft-Extensions-Logging`. W systemie Windows Dostawca używa [funkcji ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).

```csharp
logging.AddEventSourceLogger();
```

Dostawca źródła zdarzeń zostanie dodany automatycznie, gdy `CreateDefaultBuilder` jest wywoływana w celu skompilowania hosta.

Użyj [Narzędzia Narzędzia PerfView](https://github.com/Microsoft/perfview) do zbierania i wyświetlania dzienników. Istnieją inne narzędzia do wyświetlania dzienników ETW, ale narzędzia PerfView zapewnia najlepsze środowisko pracy z zdarzeniami ETW emitowanymi przez ASP.NET Core.

Aby skonfigurować narzędzia PerfView do zbierania zdarzeń rejestrowanych przez tego dostawcę, Dodaj ciąg `*Microsoft-Extensions-Logging` do listy **dodatkowych dostawców** . (Nie przegap gwiazdki na początku ciągu znaków).

![Narzędzia PerfView dodatkowych dostawców](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a>Dostawca dziennika zdarzeń systemu Windows

Pakiet dostawcy [Microsoft. Extensions. Logging. EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) wysyła dane wyjściowe dziennika do dziennika zdarzeń systemu Windows.

```csharp
logging.AddEventLog();
```

[Przeciążenia addeventlog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) umożliwiają przekazywanie <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>. Jeśli `null` lub nie zostanie określony, są używane następujące ustawienia domyślne:

* `LogName`&ndash; "Aplikacja"
* `SourceName`&ndash; "Środowisko uruchomieniowe platformy .NET"
* `MachineName`&ndash; komputer lokalny

Zdarzenia są rejestrowane dla [poziomu ostrzeżeń i wyższych](#log-level). Aby rejestrować zdarzenia mniejsze niż `Warning`, jawnie ustaw poziom dziennika. Na przykład Dodaj następujący kod do pliku *appSettings. JSON* :

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a>Dostawca TraceSource

Pakiet dostawcy [Microsoft. Extensions. Logging. TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) używa <xref:System.Diagnostics.TraceSource> bibliotek i dostawców.

```csharp
logging.AddTraceSource(sourceSwitchName);
```

[Przeciążenia AddTraceSource](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) umożliwiają przekazywanie danych w przełączniku źródłowym i odbiorniku śledzenia.

Aby można było korzystać z tego dostawcy, aplikacja musi działać na .NET Framework (a nie na platformie .NET Core). Dostawca może kierować komunikaty do różnych [odbiorników](/dotnet/framework/debug-trace-profile/trace-listeners), takich jak <xref:System.Diagnostics.TextWriterTraceListener> używane w przykładowej aplikacji.

### <a name="azure-app-service-provider"></a>Dostawca Azure App Service

Pakiet [Microsoft. Extensions. Logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) Provider zapisuje dzienniki w plikach tekstowych w systemie plików aplikacji Azure App Service i w usłudze [BLOB Storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) na koncie usługi Azure Storage.

```csharp
logging.AddAzureWebAppDiagnostics();
```

Pakiet dostawcy nie jest uwzględniony w [pakiecie Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app). Podczas określania elementu docelowego .NET Framework lub `Microsoft.AspNetCore.App` odwoływania się do niego, Dodaj pakiet dostawcy do projektu. 

<xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> Przeciążenie umożliwia przekazywanie <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>. Obiekt Settings może przesłonić ustawienia domyślne, takie jak szablon danych wyjściowych rejestrowania, nazwa obiektu BLOB i limit rozmiaru pliku. (*Szablon danych wyjściowych* to szablon wiadomości, który jest stosowany do wszystkich dzienników oprócz tego, co jest dostępne `ILogger` w wywołaniu metody).

Po wdrożeniu w aplikacji App Service, aplikacja będzie przestrzegać ustawień w sekcji [dzienniki App Service](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) na stronie **App Service** Azure Portal. Po zaktualizowaniu następujących ustawień zmiany zaczynają obowiązywać natychmiast, bez konieczności ponownego uruchomienia lub ponownej wdrożenia aplikacji.

* **Rejestrowanie aplikacji (system plików)**
* **Rejestrowanie aplikacji (BLOB)**

Domyślną lokalizacją plików dziennika jest folder *D:\\Home\\LogFiles\\* , a domyślna nazwa pliku to *Diagnostics-YYYYMMDD. txt*. Domyślny limit rozmiaru pliku wynosi 10 MB, a domyślna maksymalna liczba zachowywanych plików to 2. Domyślną nazwą obiektu BLOB jest *{App-Name} {timestamp}/yyyy/mm/dd/hh/{GUID}-applicationLog.txt*.

Dostawca działa tylko wtedy, gdy projekt jest uruchomiony w środowisku platformy Azure. Nie ma ono wpływu, gdy projekt jest uruchamiany lokalnie&mdash;, nie zapisuje w plikach lokalnych ani w lokalnym magazynie programistycznym dla obiektów BLOB.

#### <a name="azure-log-streaming"></a>Przesyłanie strumieniowe dzienników Azure

Usługa przesyłania strumieniowego w usłudze Azure log umożliwia wyświetlanie dziennika aktywności w czasie rzeczywistym z:

* Serwer aplikacji
* Serwer sieci Web
* Śledzenie nieudanych żądań

Aby skonfigurować przesyłanie strumieniowe dzienników Azure:

* Przejdź do strony **dzienników App Service** ze strony portalu aplikacji.
* Ustaw **Rejestrowanie aplikacji (system plików)** na **włączone**.
* Wybierz **poziom**dziennika. To ustawienie dotyczy tylko przesyłania strumieniowego dzienników platformy Azure, a nie innych dostawców rejestrowania w aplikacji.

Przejdź do strony **strumień dziennika** , aby wyświetlić komunikaty aplikacji. Są one rejestrowane przez aplikację za pomocą `ILogger` interfejsu.

### <a name="azure-application-insights-trace-logging"></a>Rejestrowanie śledzenia Application Insights platformy Azure

Pakiet [Microsoft. Extensions. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) Provider zapisuje dzienniki na platformie Azure Application Insights. Application Insights to usługa, która monitoruje aplikację internetową i udostępnia narzędzia do wykonywania zapytań i analizowania danych telemetrycznych. Jeśli używasz tego dostawcy, możesz wysyłać zapytania i analizować dzienniki przy użyciu narzędzi Application Insights.

Dostawca rejestrowania jest dołączony jako zależność [Microsoft. ApplicationInsights. AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), który jest pakietem, który zapewnia wszystkie dostępne dane telemetryczne dla ASP.NET Core. Jeśli używasz tego pakietu, nie musisz instalować pakietu dostawcy.

Nie używaj&mdash;pakietu [Microsoft. ApplicationInsights. Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) , który jest przeznaczony dla ASP.NET 4. x.

Więcej informacji zawierają następujące zasoby:

* [Omówienie usługi Application Insights](/azure/application-insights/app-insights-overview)
* [Application Insights dla ASP.NET Core aplikacji](/azure/azure-monitor/app/asp-net-core) — Zacznij tutaj, jeśli chcesz zaimplementować cały zakres Application Insights telemetrii wraz z rejestrowaniem.
* [ApplicationInsightsLoggerProvider for .NET Core ILogger](/azure/azure-monitor/app/ilogger) — Rozpocznij tutaj, jeśli chcesz zaimplementować dostawcę rejestrowania bez pozostałej części telemetrii Application Insights.
* [Karty rejestrowania Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).
* [Zainstaluj, skonfiguruj i zainicjuj samouczek Application INSIGHTS SDK](/learn/modules/instrument-web-app-code-with-application-insights) — interaktywny w witrynie Microsoft Learn.

## <a name="third-party-logging-providers"></a>Dostawcy rejestrowania innych firm

Platformy rejestrowania innych firm, które współpracują z ASP.NET Core:

* [ELMAH.IO](https://elmah.io/) ([repozytorium GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))
* [Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([repozytorium GitHub](https://github.com/mattwcole/gelf-extensions-logging))
* [JSNLog](https://jsnlog.com/) ([repozytorium GitHub](https://github.com/mperdeck/jsnlog))
* [KissLog.NET](https://kisslog.net/) ([repozytorium GitHub](https://github.com/catalingavan/KissLog-net))
* [Log4Net](https://logging.apache.org/log4net/) ([repozytorium GitHub](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))
* [Loggr](https://loggr.net/) ([repozytorium GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))
* [NLOG](https://nlog-project.org/) ([repozytorium GitHub](https://github.com/NLog/NLog.Extensions.Logging))
* [Sentry](https://sentry.io/welcome/) ([repozytorium GitHub](https://github.com/getsentry/sentry-dotnet))
* [Serilog](https://serilog.net/) ([repozytorium GitHub](https://github.com/serilog/serilog-aspnetcore))
* [Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([repozytorium GitHub](https://github.com/googleapis/google-cloud-dotnet))

Niektóre struktury innych firm mogą wykonywać [Rejestrowanie semantyczne, znane także jako rejestrowanie strukturalne](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Korzystanie z struktury innej firmy jest podobne do korzystania z jednego z wbudowanych dostawców:

1. Dodaj pakiet NuGet do projektu.
1. Wywoływanie `ILoggerFactory` metody rozszerzenia dostarczonej przez strukturę rejestrowania.

Aby uzyskać więcej informacji, zobacz dokumentację każdego dostawcy. Dostawcy rejestrowania innych firm nie są obsługiwani przez firmę Microsoft.

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:fundamentals/logging/loggermessage>

::: moniker-end
