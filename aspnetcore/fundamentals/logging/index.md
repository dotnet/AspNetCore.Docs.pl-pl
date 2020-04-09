---
title: Logowanie do .NET Core i ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak korzystać z struktury rejestrowania dostarczonej przez pakiet Microsoft.Extensions.Logging NuGet.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/05/2020
uid: fundamentals/logging/index
ms.openlocfilehash: 58e236ad7f0863b87907d5585e1cb6bf61d46e99
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78663304"
---
# <a name="logging-in-net-core-and-aspnet-core"></a>Logowanie do .NET Core i ASP.NET Core

Przez [Tom Dykstra](https://github.com/tdykstra) i [Steve Smith](https://ardalis.com/)

Program .NET Core obsługuje interfejs API rejestrowania, który współpracuje z różnymi wbudowanymi i zewnętrznymi dostawcami rejestrowania. W tym artykule pokazano, jak używać interfejsu API rejestrowania z wbudowanymi dostawcami.

::: moniker range=">= aspnetcore-3.0"

Większość przykładów kodu pokazano w tym artykule są z ASP.NET podstawowych aplikacji. Części tych fragmentów kodu specyficzne dla rejestrowania mają zastosowanie do dowolnej aplikacji .NET Core, która używa [hosta ogólnego.](xref:fundamentals/host/generic-host) Na przykład sposobu używania hosta ogólnego w aplikacji konsoli niesienia sieci Web zobacz *Program.cs* plik [przykładowej aplikacji Zadania w tle](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).

Rejestrowanie kodu dla aplikacji bez ogólnego hosta różni się sposobem [dodawania dostawców](#add-providers) i [tworzenia rejestratorów.](#create-logs) Przykłady kodu niebędącego hostem są wyświetlane w tych sekcjach artykułu.

::: moniker-end

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="add-providers"></a>Dodawanie dostawców

Dostawca rejestrowania wyświetla lub przechowuje dzienniki. Na przykład dostawca konsoli wyświetla dzienniki na konsoli, a dostawca usługi Azure Application Insights przechowuje je w usłudze Azure Application Insights. Dzienniki mogą być wysyłane do wielu miejsc docelowych, dodając wielu dostawców.

::: moniker range=">= aspnetcore-3.0"

Aby dodać dostawcę w aplikacji korzystającej z hosta `Add{provider name}` ogólnego, należy wywołać metodę rozszerzenia dostawcy w *Program.cs:*

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=6)]

W aplikacji konsoli nie-hosta, wywołać metodę `Add{provider name}` rozszerzenia `LoggerFactory`dostawcy podczas tworzenia:

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=1,7)]

`LoggerFactory`i `AddConsole` wymagają `using` instrukcji `Microsoft.Extensions.Logging`dla .

Wywołać <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>domyślne szablony projektów ASP.NET Core, które dodaje następujących dostawców rejestrowania:

* [Konsola](#console-provider)
* [Debugowanie](#debug-provider)
* [Eventsource](#event-source-provider)
* [EventLog](#windows-eventlog-provider) (tylko w przypadku pracy w systemie Windows)

Dostawców domyślnych można zastąpić własnymi wyborami. Zadzwoń <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>i dodaj dostawców, których chcesz.

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0 "

Aby dodać dostawcę, zadzwoń `Add{provider name}` do metody rozszerzenia dostawcy w *Program.cs:*

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

Powyższy kod wymaga odwołań do `Microsoft.Extensions.Logging` i `Microsoft.Extensions.Configuration`.

Domyślny szablon <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>projektu wywołuje, który dodaje następujących dostawców rejestrowania:

* Konsola
* Debugowanie
* EventSource (począwszy od ASP.NET Core 2.2)

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

Jeśli używasz, `CreateDefaultBuilder`możesz zastąpić domyślnych dostawców własnymi wyborami. Zadzwoń <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>i dodaj dostawców, których chcesz.

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

::: moniker-end

Dowiedz się więcej o [wbudowanych dostawców rejestrowania](#built-in-logging-providers) i [dostawców rejestrowania innych firm](#third-party-logging-providers) w dalszej części artykułu.

## <a name="create-logs"></a>Tworzenie dzienników

Aby utworzyć dzienniki, <xref:Microsoft.Extensions.Logging.ILogger%601> użyj obiektu. W aplikacji sieci web lub usługi `ILogger` hostowanej, uzyskać z iniekcji zależności (DI). W aplikacjach konsoli nie-hosta użyj go `LoggerFactory` do utworzenia pliku `ILogger`.

Poniższy przykład ASP.NET Core tworzy rejestrator `TodoApiSample.Pages.AboutModel` z jako kategorii. Kategoria *dziennika* jest ciągiem skojarzonym z każdym dziennikiem. Wystąpienie `ILogger<T>` dostarczone przez DI tworzy dzienniki, które `T` mają w pełni kwalifikowaną nazwę typu jako kategorii. 

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

Poniższy przykład aplikacji konsoli nie-hosta `LoggingConsoleApp.Program` tworzy rejestrator z jako kategorii.

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

::: moniker-end

W poniższych ASP.NET przykłady aplikacji Core i konsoli rejestrator jest używany `Information` do tworzenia dzienników z jako poziom. Poziom *dziennika* wskazuje ważność zarejestrowanego zdarzenia. 

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

::: moniker-end

[Poziomy](#log-level) i kategorie są [wyjaśnione](#log-category) bardziej szczegółowo w dalszej części tego artykułu. 

::: moniker range=">= aspnetcore-3.0"

### <a name="create-logs-in-the-program-class"></a>Tworzenie dzienników w klasie Program

Aby napisać dzienniki `Program` w klasie aplikacji ASP.NET Core, `ILogger` pobierz wystąpienie z DI po zbudowaniu hosta:

[!code-csharp[](index/samples_snapshot/3.x/TodoApiSample/Program.cs?highlight=9,10)]

Rejestrowanie podczas budowy hosta nie jest bezpośrednio obsługiwane. Można jednak użyć oddzielnego rejestratora. W poniższym przykładzie [rejestrator Serilog](https://serilog.net/) jest `CreateHostBuilder`używany do logowania . `AddSerilog`wykorzystuje konfigurację statyczną `Log.Logger`określoną w:

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

### <a name="create-logs-in-the-startup-class"></a>Tworzenie dzienników w klasie Uruchamiani

Aby zapisać dzienniki `Startup.Configure` w metodzie aplikacji ASP.NET Core, `ILogger` dołącz parametr do podpisu metody:

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_Configure&highlight=1,5)]

Zapisywanie dzienników przed zakończeniem konfiguracji `Startup.ConfigureServices` kontenera DI w metodzie nie jest obsługiwane:

* Logger wtrysku do konstruktora `Startup` nie jest obsługiwany.
* Wtrysk `Startup.ConfigureServices` rejestratora do podpisu metody nie jest obsługiwany

Powodem tego ograniczenia jest to, że rejestrowanie zależy od DI i konfiguracji, co z kolei zależy od DI. Kontener DI nie jest skonfigurowany do zakończenia. `ConfigureServices`

Inicjowanie konstruktora rejestratora do `Startup` prac we wcześniejszych wersjach ASP.NET Core, ponieważ dla hosta sieci Web jest tworzony oddzielny kontener DI. Aby uzyskać informacje o tym, dlaczego dla hosta ogólnego tworzony jest tylko jeden kontener, zobacz [anons zmiany podziału](https://github.com/aspnet/Announcements/issues/353).

Jeśli trzeba skonfigurować usługę, która `ILogger<T>`zależy od , nadal można to zrobić za pomocą wtrysku konstruktora lub pod warunkiem, że metoda fabryczna. Podejście metody fabrycznej jest zalecane tylko wtedy, gdy nie ma innej opcji. Załóżmy na przykład, że musisz wypełnić właściwość usługą z DI:

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

Poprzedni wyróżniony kod `Func` jest, który uruchamia po raz pierwszy kontener `MyService`DI musi skonstruować wystąpienie . W ten sposób można uzyskać dostęp do dowolnej z zarejestrowanych usług.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

### <a name="create-logs-in-startup"></a>Tworzenie dzienników w starcie

Aby zapisać dzienniki `Startup` w klasie, dołącz `ILogger` parametr w podpisie konstruktora:

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a>Tworzenie dzienników w klasie Program

Aby napisać dzienniki `Program` w klasie, pobierz wystąpienie `ILogger` z DI:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

Rejestrowanie podczas budowy hosta nie jest bezpośrednio obsługiwane. Można jednak użyć oddzielnego rejestratora. W poniższym przykładzie [rejestrator Serilog](https://serilog.net/) jest `CreateWebHostBuilder`używany do logowania . `AddSerilog`wykorzystuje konfigurację statyczną `Log.Logger`określoną w:

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

::: moniker-end

### <a name="no-asynchronous-logger-methods"></a>Brak metod rejestratora asynchronii

Rejestrowanie powinno być tak szybkie, że nie jest warte kosztu wydajności kodu asynchroniowego. Jeśli magazyn danych rejestrowania jest powolny, nie zapisuj do niego bezpośrednio. Należy rozważyć zapisanie komunikatów dziennika do szybkiego magazynu początkowo, a następnie przenieść je do wolnego magazynu później. Na przykład jeśli logujesz się do programu SQL Server, nie chcesz tego `Log` robić bezpośrednio `Log` w metodzie, ponieważ metody są synchroniczne. Zamiast tego synchronicznie dodać komunikaty dziennika do kolejki w pamięci i mieć proces roboczy w tle wyciągnąć wiadomości z kolejki, aby wykonać asynchronicznie pracy wypychania danych do programu SQL Server. Aby uzyskać więcej informacji, zobacz [ten](https://github.com/dotnet/AspNetCore.Docs/issues/11801) problem z githubem.

## <a name="configuration"></a>Konfigurowanie

Konfiguracja dostawcy rejestrowania jest dostarczana przez jednego lub więcej dostawców konfiguracji:

* Formaty plików (INI, JSON i XML).
* Argumenty wiersza polecenia.
* Zmienne środowiskowe.
* W pamięci obiektów .NET.
* Niezaszyfrowany magazyn [Menedżera tajnych.](xref:security/app-secrets)
* Zaszyfrowany magazyn użytkowników, taki jak [Azure Key Vault](xref:security/key-vault-configuration).
* Dostawcy niestandardowi (zainstalowane lub utworzone).

Na przykład konfiguracja rejestrowania jest `Logging` często dostarczana przez sekcję plików ustawień aplikacji. W poniższym przykładzie przedstawiono zawartość typowych *appsettings. Plik Development.json:*

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

Właściwość `Logging` może `LogLevel` mieć i rejestrować właściwości dostawcy (konsola jest wyświetlana).

Właściwość `LogLevel` `Logging` w obszarze określa minimalny [poziom](#log-level) do dziennika dla wybranych kategorii. W przykładzie `System` `Microsoft` i kategorii `Information` dziennika na poziomie, `Debug` a wszystkie inne dziennika na poziomie.

Inne właściwości `Logging` w obszarze określić dostawców rejestrowania. Przykład jest dla dostawcy konsoli. Jeśli dostawca obsługuje [zakresy dziennika,](#log-scopes) `IncludeScopes` wskazuje, czy są one włączone. Właściwość dostawcy (na przykład `Console` w przykładzie) może również określić `LogLevel` właściwość. `LogLevel`pod dostawcą określa poziomy do zalogowania dla tego dostawcy.

Jeśli poziomy są `Logging.{providername}.LogLevel`określone w , zastępują wszystko ustawione w `Logging.LogLevel`.

Interfejs API rejestrowania nie zawiera scenariusza, aby zmienić poziomy dziennika, gdy aplikacja jest uruchomiona. Jednak niektórzy dostawcy konfiguracji są w stanie przeładować konfigurację, co ma natychmiastowy wpływ na konfigurację rejestrowania. Na przykład [dostawca konfiguracji plików](xref:fundamentals/configuration/index#file-configuration-provider), `CreateDefaultBuilder` który jest dodawany przez pliki ustawień odczytu, domyślnie ładuje konfigurację rejestrowania. Jeśli konfiguracja zostanie zmieniona w kodzie, gdy aplikacja jest uruchomiona, aplikacja może wywołać [IConfigurationRoot.Reload,](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) aby zaktualizować konfigurację rejestrowania aplikacji.

Aby uzyskać informacje na temat <xref:fundamentals/configuration/index>wdrażania dostawców konfiguracji, zobacz .

## <a name="sample-logging-output"></a>Przykładowe dane wyjściowe rejestrowania

Po wyświetleniu przykładowego kodu w poprzedniej sekcji dzienniki są wyświetlane w konsoli, gdy aplikacja jest uruchamiana z wiersza polecenia. Oto przykład danych wyjściowych konsoli:

::: moniker range=">= aspnetcore-3.0"

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

::: moniker-end

::: moniker range="< aspnetcore-3.0"

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

::: moniker-end

Poprzednie dzienniki zostały wygenerowane przez wykonanie żądania HTTP `http://localhost:5000/api/todo/0`Get do przykładowej aplikacji o godzinie .

Oto przykład tych samych dzienników, które pojawiają się w oknie debugowania po uruchomieniu przykładowej aplikacji w programie Visual Studio:

::: moniker range=">= aspnetcore-3.0"

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

Dzienniki, które są `ILogger` tworzone przez wywołania wyświetlane w poprzedniej sekcji zaczynają się od "TodoApiSample". Dzienniki, które zaczynają się od kategorii "Microsoft" pochodzą z ASP.NET kodu frameworka Core. ASP.NET Core i kod aplikacji są przy użyciu tego samego interfejsu API rejestrowania i dostawców.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

Dzienniki, które są `ILogger` tworzone przez wywołania pokazane w poprzedniej sekcji zaczynają się od "TodoApi". Dzienniki, które zaczynają się od kategorii "Microsoft" pochodzą z ASP.NET kodu frameworka Core. ASP.NET Core i kod aplikacji są przy użyciu tego samego interfejsu API rejestrowania i dostawców.

::: moniker-end

Poniżej dalsza część artykułu Wyjaśniono niektóre szczegóły i opcje rejestrowania.

## <a name="nuget-packages"></a>Pakiety NuGet

`ILogger` Interfejsy `ILoggerFactory` i interfejsy znajdują się w witrynie [Microsoft.Extensions.Logging.Abstractions,](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/)a domyślne implementacje dla nich znajdują się w [witrynie Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).

## <a name="log-category"></a>Kategoria dziennika

Podczas `ILogger` tworzenia obiektu określana jest dla niego *kategoria.* Ta kategoria jest dołączona do każdej wiadomości `ILogger`dziennika utworzonej przez to wystąpienie . Kategoria może być dowolny ciąg, ale konwencja jest użycie nazwy klasy, takich jak "TodoApi.Controllers.TodoController".

Użyj, `ILogger<T>` aby `ILogger` uzyskać wystąpienie, które używa `T` w pełni kwalifikowanej nazwy typu jako kategorii:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

Aby jawnie określić kategorię, zadzwoń: `ILoggerFactory.CreateLogger`

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

`ILogger<T>`jest odpowiednikiem `CreateLogger` wywołania z w `T`pełni kwalifikowaną nazwą typu .

## <a name="log-level"></a>Poziom dziennika

Każdy dziennik określa <xref:Microsoft.Extensions.Logging.LogLevel> wartość. Poziom dziennika wskazuje ważność lub ważność. Na przykład można napisać `Information` dziennik, gdy metoda kończy `Warning` się normalnie i dziennika, gdy metoda zwraca kod stanu *404 Nie znaleziono.*

Następujący kod `Information` tworzy `Warning` i rejestruje:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

W poprzednim kodzie pierwszym parametrem jest [identyfikator zdarzenia Log](#log-event-id). Drugi parametr jest szablonem wiadomości z symbolami zastępczymi dla wartości argumentów dostarczonych przez pozostałe parametry metody. Parametry metody są wyjaśnione w [sekcji szablonu wiadomości](#log-message-template) w dalszej części tego artykułu.

Metody dziennika, które zawierają poziom w nazwie `LogInformation` `LogWarning`metody (na przykład i ) są [metody rozszerzenia dla ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions). Te metody `Log` wywołać metodę, która przyjmuje `LogLevel` parametr. Można wywołać `Log` metodę bezpośrednio, a nie jedną z tych metod rozszerzenia, ale składnia jest stosunkowo skomplikowana. Aby uzyskać więcej <xref:Microsoft.Extensions.Logging.ILogger> informacji, zobacz kod [źródłowy rozszerzeń rejestratora](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).

ASP.NET Core definiuje następujące poziomy dziennika, uporządkowane w tym miejscu od najniższej do najwyższej ważności.

* Ślad = 0

  Aby uzyskać informacje, które są zazwyczaj cenne tylko do debugowania. Te komunikaty mogą zawierać poufne dane aplikacji, a więc nie powinny być włączone w środowisku produkcyjnym. *Domyślnie wyłączone.*

* Debugowanie = 1

  Aby uzyskać informacje, które mogą być przydatne w rozwoju i debugowania. Przykład: `Entering method Configure with flag set to true.` `Debug` Włącz logi poziomu w produkcji tylko podczas rozwiązywania problemów, ze względu na dużą liczbę dzienników.

* Informacje = 2

  Do śledzenia ogólnego przepływu aplikacji. Te dzienniki zazwyczaj mają pewną wartość długoterminową. Przykład: `Request received for path /api/todo`

* Ostrzeżenie = 3

  W przypadku nieprawidłowych lub nieoczekiwanych zdarzeń w przepływie aplikacji. Mogą to być błędy lub inne warunki, które nie powodują zatrzymania aplikacji, ale mogą wymagać zbadania. Obsługiwane wyjątki są wspólne miejsce `Warning` do korzystania z poziomu dziennika. Przykład: `FileNotFoundException for file quotes.txt.`

* Błąd = 4

  Dla błędów i wyjątków, które nie mogą być obsługiwane. Te komunikaty wskazują na błąd w bieżącym działaniu lub operacji (takich jak bieżące żądanie HTTP), a nie błąd całej aplikacji. Przykładowy komunikat dziennika:`Cannot insert record due to duplicate key violation.`

* Krytyczny = 5

  W przypadku awarii, które wymagają natychmiastowej uwagi. Przykłady: scenariusze utraty danych, brak miejsca na dysku.

Użyj poziomu dziennika, aby kontrolować, ile danych wyjściowych dziennika jest zapisywany na określonym nośniku pamięci lub oknie wyświetlania. Przykład:

* W produkcji:
  * Rejestrowanie na `Trace` `Information` poziomach za pośrednictwem tworzy dużą ilość szczegółowych komunikatów dziennika. Aby kontrolować koszty i nie przekraczać `Information` limitów magazynowania danych, rejestruj `Trace` komunikaty poziomu do magazynu danych o dużej objętości i niskich kosztach.
  * Rejestrowanie `Warning` na `Critical` poziomach za pośrednictwem zazwyczaj generuje mniej, mniejsze komunikaty dziennika. W związku z tym koszty i limity magazynowania zwykle nie są problemem, co skutkuje większą elastycznością wyboru magazynu danych.
* Podczas rozwoju:
  * Zaloguj `Warning` `Critical` się za pośrednictwem wiadomości do konsoli.
  * Dodaj `Trace` `Information` za pośrednictwem wiadomości podczas rozwiązywania problemów.

[Sekcja filtrowania dziennika](#log-filtering) w dalszej części tego artykułu wyjaśnia, jak kontrolować poziomy dziennika, które obsługuje dostawca.

ASP.NET Core zapisuje dzienniki dla zdarzeń struktury. Przykłady dziennika wcześniej w tym artykule wykluczone dzienniki poniżej `Information` poziomu, więc nie `Debug` lub `Trace` poziom dzienniki zostały utworzone. Oto przykład dzienników konsoli wyprodukowanych przez uruchomienie przykładowej `Debug` aplikacji skonfigurowanej do pokazywalek dzienników:

::: moniker range=">= aspnetcore-3.0"

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

::: moniker-end

::: moniker range="< aspnetcore-3.0"

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

::: moniker-end

## <a name="log-event-id"></a>Identyfikator zdarzenia dziennika

Każdy dziennik może określić *identyfikator zdarzenia*. Przykładowa aplikacja robi to przy `LoggingEvents` użyciu klasy zdefiniowanej lokalnie:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/3.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

Identyfikator zdarzenia kojarzy zestaw zdarzeń. Na przykład wszystkie dzienniki związane z wyświetlaniem listy elementów na stronie może być 1001.

Dostawca rejestrowania może przechowywać identyfikator zdarzenia w polu identyfikator, w komunikacie rejestrowania lub w ogóle. Dostawca debugowania nie pokazuje identyfikatorów zdarzeń. Dostawca konsoli wyświetla identyfikatory zdarzeń w nawiasach po kategorii:

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a>Szablon komunikatu dziennika

Każdy dziennik określa szablon wiadomości. Szablon wiadomości może zawierać symbole zastępcze, dla których są dostarczane argumenty. Użyj nazw symboli zastępczych, a nie liczb.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

Kolejność symboli zastępczych, a nie ich nazw, określa, które parametry są używane do dostarczania ich wartości. W poniższym kodzie należy zauważyć, że nazwy parametrów są niesekwencjonowane w szablonie wiadomości:

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

Ten kod tworzy komunikat dziennika z wartościami parametrów w sekwencji:

```text
Parameter values: parm1, parm2
```

Struktura rejestrowania działa w ten sposób, dzięki czemu dostawcy rejestrowania mogą zaimplementować [rejestrowanie semantyczne, znane również jako rejestrowanie strukturalne.](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging) Same argumenty są przekazywane do systemu rejestrowania, a nie tylko do sformatowanego szablonu wiadomości. Te informacje umożliwiają dostawcom rejestrowania przechowywanie wartości parametrów jako pól. Załóżmy na przykład, że wywołania metody rejestratora wyglądają następująco:

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

Jeśli wysyłasz dzienniki do usługi Azure Table Storage, `ID` każda `RequestTime` jednostka tabeli platformy Azure może mieć i właściwości, co upraszcza zapytania dotyczące danych dziennika. Kwerenda może znaleźć wszystkie dzienniki w określonym `RequestTime` zakresie bez analizowania czasu poza wiadomością tekstową.

## <a name="logging-exceptions"></a>Rejestrowanie wyjątków

Metody rejestratora mają przeciążenia, które umożliwiają przekazywanie w wyjątku, jak w poniższym przykładzie:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

Różni dostawcy obsługują informacje o wyjątkach na różne sposoby. Oto przykład danych wyjściowych dostawcy debugowania z kodu pokazanego powyżej.

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a>Filtrowanie dzienników

Można określić minimalny poziom dziennika dla określonego dostawcy i kategorii lub dla wszystkich dostawców lub wszystkich kategorii. Wszelkie dzienniki poniżej minimalnego poziomu nie są przekazywane do tego dostawcy, więc nie są wyświetlane ani przechowywane.

Aby pominąć wszystkie `LogLevel.None` dzienniki, należy określić jako minimalny poziom dziennika. Wartość całkowita `LogLevel.None` wynosi 6, która jest `LogLevel.Critical` wyższa niż (5).

### <a name="create-filter-rules-in-configuration"></a>Tworzenie reguł filtrowania w konfiguracji

Kod szablonu `CreateDefaultBuilder` projektu wywołuje skonfigurowanie rejestrowania dla dostawców konsoli, debugowania i źródła zdarzeń (ASP.NET Core 2.2 lub nowszych). Metoda `CreateDefaultBuilder` konfiguruje rejestrowanie w celu `Logging` wyszukywania konfiguracji w sekcji, jak wyjaśniono [wcześniej w tym artykule](#configuration).

Dane konfiguracyjne określają minimalne poziomy dziennika według dostawcy i kategorii, jak w poniższym przykładzie:

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/TodoApiSample/appsettings.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

::: moniker-end

Ten JSON tworzy sześć reguł filtrowania: jeden dla dostawcy debugowania, cztery dla dostawcy konsoli i jeden dla wszystkich dostawców. Pojedyncza reguła jest wybierana `ILogger` dla każdego dostawcy podczas tworzenia obiektu.

### <a name="filter-rules-in-code"></a>Reguły filtrowania w kodzie

W poniższym przykładzie pokazano, jak zarejestrować reguły filtrowania w kodzie:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=2-3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

::: moniker-end

Drugi `AddFilter` określa dostawcę debugowania przy użyciu jego nazwy typu. Pierwszy `AddFilter` dotyczy wszystkich dostawców, ponieważ nie określa typu dostawcy.

### <a name="how-filtering-rules-are-applied"></a>Jak stosowane są reguły filtrowania

Dane konfiguracji i `AddFilter` kod pokazany w poprzednich przykładach tworzą reguły pokazane w poniższej tabeli. Pierwsze sześć pochodzą z przykładu konfiguracji i dwa ostatnie pochodzą z przykładu kodu.

| Liczba | Dostawca      | Kategorie, które zaczynają się od ...          | Minimalny poziom dziennika |
| :----: | ------------- | --------------------------------------- | ----------------- |
| 1      | Debugowanie         | Wszystkie kategorie                          | Informacje       |
| 2      | Konsola       | Microsoft.AspNetCore.Mvc.Razor.Internal | Ostrzeżenie           |
| 3      | Konsola       | Microsoft.AspNetCore.Mvc.Razor.Razor    | Debugowanie             |
| 4      | Konsola       | Microsoft.AspNetCore.Mvc.Razor          | Błąd             |
| 5      | Konsola       | Wszystkie kategorie                          | Informacje       |
| 6      | Wszyscy dostawcy | Wszystkie kategorie                          | Debugowanie             |
| 7      | Wszyscy dostawcy | System                                  | Debugowanie             |
| 8      | Debugowanie         | Microsoft                               | Ślad             |

Po `ILogger` utworzeniu `ILoggerFactory` obiektu obiekt wybiera jedną regułę na dostawcę, aby zastosować do tego rejestratora. Wszystkie wiadomości napisane `ILogger` przez wystąpienie są filtrowane na podstawie wybranych reguł. Najbardziej specyficzna reguła możliwa dla każdego dostawcy i pary kategorii jest wybierana z dostępnych reguł.

Następujący algorytm jest używany dla `ILogger` każdego dostawcy, gdy jest tworzony dla danej kategorii:

* Wybierz wszystkie reguły, które pasują do dostawcy lub jego aliasu. Jeśli nie zostanie znaleziony żaden mecz, wybierz wszystkie reguły z pustym dostawcą.
* Z wyniku poprzedniego kroku wybierz reguły z najdłuższym pasującym prefiksem kategorii. Jeśli nie zostanie znalezione żadne dopasowanie, wybierz wszystkie reguły, które nie określają kategorii.
* Jeśli zaznaczono wiele reguł, weź **ostatni.**
* Jeśli nie wybrano żadnych reguł, użyj . `MinimumLevel`

Załóżmy, że na powyższej `ILogger` liście reguł zostanie utworzony obiekt dla kategorii "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":

* W przypadku dostawcy debugowania obowiązują reguły 1, 6 i 8. Reguła 8 jest najbardziej szczegółowa, więc to jest ta wybrana.
* W przypadku dostawcy konsoli obowiązują zasady 3, 4, 5 i 6. Zasada 3 jest najbardziej szczegółowa.

Wynikowe `ILogger` wystąpienie wysyła `Trace` dzienniki poziomu i powyżej do dostawcy debugowania. Dzienniki `Debug` poziomu i powyżej są wysyłane do dostawcy konsoli.

### <a name="provider-aliases"></a>Aliasy dostawców

Każdy dostawca definiuje *alias,* który może być używany w konfiguracji zamiast w pełni kwalifikowanej nazwy typu.  W przypadku wbudowanych dostawców użyj następujących aliasów:

* Konsola
* Debugowanie
* EventSource
* Eventlog
* TraceSource
* Plik usługi AzureAppServicesFile
* AzureAppServicesBlob
* ApplicationInsights

### <a name="default-minimum-level"></a>Domyślny poziom minimalny

Istnieje minimalne ustawienie poziomu, które staje się skuteczne tylko wtedy, gdy żadne reguły z konfiguracji lub kodu nie mają zastosowania do danego dostawcy i kategorii. W poniższym przykładzie pokazano, jak ustawić minimalny poziom:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

::: moniker-end

Jeśli nie ustawisz jawnie minimalnego poziomu, `Information`wartością `Trace` domyślną jest , co oznacza, że i `Debug` dzienniki są ignorowane.

### <a name="filter-functions"></a>Funkcje filtrowania

Funkcja filtrowania jest wywoływana dla wszystkich dostawców i kategorii, które nie mają reguł przypisanych do nich przez konfigurację lub kod. Kod w funkcji ma dostęp do typu dostawcy, kategorii i poziomu dziennika. Przykład:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=3-11)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

::: moniker-end

## <a name="system-categories-and-levels"></a>Kategorie i poziomy systemu

Oto kilka kategorii używanych przez ASP.NET Core i Entity Framework Core, z uwagami na temat tego, jakich dzienników można się po nich spodziewać:

| Kategoria                            | Uwagi |
| ----------------------------------- | ----- |
| Microsoft.AspNetCore                | Diagnostyka ASP.NET podstawowa. |
| Microsoft.AspNetCore.DataProtection | Które klucze zostały uznane, znalezione i używane. |
| Filtrowanie pliku Microsoft.AspNetCore.HostFiltering  | Hosty dozwolone. |
| Microsoft.AspNetCore.Hosting        | Jak długo trwa wypełnianie żądań HTTP i o której godzinie rozpoczęto. Które zestawy uruchamiania hostingu zostały załadowane. |
| Microsoft.AspNetCore.Mvc            | Diagnostyka MVC i Razor. Powiązanie modelu, wykonanie filtru, kompilacja widoku, wybór akcji. |
| Marszruta Microsoft.AspNetCore.Routing        | Informacje o dopasowaniu trasy. |
| Serwer Microsoft.AspNetCore.Server         | Połączenie rozpoczyna, zatrzymuje i utrzymuje żywe odpowiedzi. Informacje o certyfikacie HTTPS. |
| Plikami Microsoft.AspNetCore.Static    | Pliki obsługiwane. |
| Microsoft.EntityFrameCore       | Diagnostyka rdzenia podstawowej struktury jednostki ogólne. Aktywność i konfiguracja bazy danych, wykrywanie zmian, migracje. |

## <a name="log-scopes"></a>Zakresy dzienników

 *Zakres* można grupować zestaw operacji logicznych. To grupowanie może służyć do dołączania tych samych danych do każdego dziennika, który jest tworzony jako część zestawu. Na przykład każdy dziennik utworzony w ramach przetwarzania transakcji może zawierać identyfikator transakcji.

Zakres jest `IDisposable` typem, który jest <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> zwracany przez metodę i trwa do momentu jego usunięcia. Użyj zakresu przez zawijania wywołań rejestratora `using` w bloku:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

::: moniker-end

Poniższy kod umożliwia zakresy dla dostawcy konsoli:

*Program.cs:*

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

::: moniker-end

> [!NOTE]
> Konfigurowanie `IncludeScopes` opcji rejestratora konsoli jest wymagane, aby włączyć rejestrowanie oparte na zakresie.
>
> Aby uzyskać informacje na temat konfiguracji, zobacz [sekcję Konfiguracja.](#configuration)

Każdy komunikat dziennika zawiera informacje o zakresie:

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a>Wbudowane dostawcy rejestrowania

ASP.NET Core wysyła następujących dostawców:

* [Konsola](#console-provider)
* [Debugowanie](#debug-provider)
* [Eventsource](#event-source-provider)
* [Eventlog](#windows-eventlog-provider)
* [TraceSource](#tracesource-provider)
* [Plik usługi AzureAppServicesFile](#azure-app-service-provider)
* [AzureAppServicesBlob](#azure-app-service-provider)
* [ApplicationInsights](#azure-application-insights-trace-logging)

Aby uzyskać informacje na temat rejestrowania stdout i debugowania za pomocą ASP.NET Core Module, zobacz <xref:test/troubleshoot-azure-iis> i <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

### <a name="console-provider"></a>Dostawca konsoli

Pakiet dostawcy [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) wysyła dane wyjściowe dziennika do konsoli. 

```csharp
logging.AddConsole();
```

Aby wyświetlić dane wyjściowe rejestrowania konsoli, otwórz wiersz polecenia w folderze projektu i uruchom następujące polecenie:

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a>Dostawca debugowania

Pakiet dostawcy [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) zapisuje dane wyjściowe dziennika przy użyciu`Debug.WriteLine` klasy [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) ( wywołania metody).

W systemie Linux ten dostawca zapisuje dzienniki do */var/log/message*.

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a>Dostawca źródła zdarzeń

Pakiet dostawcy [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) zapisuje na międzyplatformie `Microsoft-Extensions-Logging`źródła zdarzeń o nazwie . W systemie Windows dostawca używa [etw](https://msdn.microsoft.com/library/windows/desktop/bb968803).

```csharp
logging.AddEventSourceLogger();
```

Dostawca źródła zdarzeń jest dodawany automatycznie po `CreateDefaultBuilder` wywołaniu do utworzenia hosta.

::: moniker range=">= aspnetcore-3.0"

#### <a name="dotnet-trace-tooling"></a>narzędzia śledzenia dotnet

Narzędzie [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) jest wieloplatformowym narzędziem interfejsu wiersza polecenia, które umożliwia zbieranie śladów rdzenia .NET uruchomionego procesu. Narzędzie zbiera <xref:Microsoft.Extensions.Logging.EventSource> dane dostawcy <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>przy użyciu pliku .

Zainstaluj narzędzia śledzenia dotnet za pomocą następującego polecenia:

```dotnetcli
dotnet tool install --global dotnet-trace
```

Użyj narzędzia śledzenia dotnet, aby zebrać ślad z aplikacji:

1. Jeśli aplikacja nie tworzy hosta `CreateDefaultBuilder`za pomocą , dodaj [dostawcę źródła zdarzeń](#event-source-provider) do konfiguracji rejestrowania aplikacji.

1. Uruchom aplikację za `dotnet run` pomocą polecenia.

1. Określ identyfikator procesu (PID) aplikacji .NET Core:

   * W systemie Windows należy użyć jednego z następujących podejść:
     * Menedżer zadań (Ctrl+Alt+Del)
     * [polecenie lista zadań](/windows-server/administration/windows-commands/tasklist)
     * [Polecenie Get-Process Powershell](/powershell/module/microsoft.powershell.management/get-process)
   * W systemie Linux użyj [polecenia pidof](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).

   Znajdź identyfikator PID dla procesu, który ma taką samą nazwę jak zestaw aplikacji.

1. Wykonaj `dotnet trace` polecenie.

   Składnia polecenia ogólnego:

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   W przypadku korzystania z powłoki polecenia `--providers` programu PowerShell`'`należy ująć wartość w pojedyncze cudzysłowy ( ):

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   Na platformach innych niż `-f speedscope` Windows dodaj opcję zmiany formatu `speedscope`wyjściowego pliku śledzenia na .

   | Słowo kluczowe | Opis |
   | :-----: | ----------- |
   | 1       | Rejestrowanie zdarzeń `LoggingEventSource`meta dotyczących pliku . Nie rejestruje zdarzeń `ILogger`z ). |
   | 2       | Włącza zdarzenie, `Message` `ILogger.Log()` gdy jest wywoływane. Zawiera informacje w sposób programowy (niesformowany). |
   | 4       | Włącza zdarzenie, `FormatMessage` `ILogger.Log()` gdy jest wywoływane. Zawiera sformatowaną wersję ciągu informacji. |
   | 8       | Włącza zdarzenie, `MessageJson` `ILogger.Log()` gdy jest wywoływane. Zapewnia reprezentację JSON argumentów. |

   | Poziom zdarzenia | Opis     |
   | :---------: | --------------- |
   | 0           | `LogAlways`     |
   | 1           | `Critical`      |
   | 2           | `Error`         |
   | 3           | `Warning`       |
   | 4           | `Informational` |
   | 5           | `Verbose`       |

   `FilterSpecs`wpisy `{Logger Category}` dla `{Event Level}` i reprezentują dodatkowe warunki filtrowania dziennika. Oddzielne `FilterSpecs` wpisy ze średnikiem`;`( ).

   Przykład użycia powłoki**no** poleceń systemu Windows `--providers` ( brak pojedynczych cudzysłowów wokół wartości):

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   Poprzednie polecenie aktywuje:

   * Rejestrator źródła zdarzeń do tworzenia sformatowanych ciągów (`4`) dla błędów (`2`).
   * `Microsoft.AspNetCore.Hosting`rejestrowania na `Informational` poziomie rejestrowania (`4`).

1. Zatrzymaj narzędzie śledzenia dotnetu, naciskając klawisz Enter lub Klawisze Ctrl+C.

   Śledzenie jest zapisywane z nazwą *trace.nettrace* `dotnet trace` w folderze, w którym polecenie jest wykonywane.

1. Otwórz śledzenie za pomocą [programu Perfview](#perfview). Otwórz plik *trace.nettrace* i eksploruj zdarzenia śledzenia.

Aby uzyskać więcej informacji, zobacz:

* [Narzędzie do analizy wydajności śledzenia (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (dokumentacja rdzenia.NET)
* [Narzędzie do analizy wydajności śledzenia (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dokumentacja repozytorium GitHub dotnet/diagnostyka)
* [Klasa LoggingEventSource](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (przeglądarka interfejsu API.NET)
* <xref:System.Diagnostics.Tracing.EventLevel>
* [LoggingEventSource źródło odniesienia (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; Aby uzyskać źródło referencyjne `release/{Version}`dla `{Version}` innej wersji, zmień gałąź na , gdzie jest żądana wersja ASP.NET Core.
* [Perfview](#perfview) &ndash; Przydatne do wyświetlania śladów źródła zdarzeń.

#### <a name="perfview"></a>Perfview

::: moniker-end

Narzędzie [PerfView](https://github.com/Microsoft/perfview) służy do zbierania i wyświetlania dzienników. Istnieją inne narzędzia do przeglądania dzienników ETW, ale PerfView zapewnia najlepsze środowisko do pracy ze zdarzeniami ETW emitowanymi przez ASP.NET Core.

Aby skonfigurować Program PerfView do zbierania zdarzeń zarejestrowanych przez tego dostawcę, dodaj ten ciąg `*Microsoft-Extensions-Logging` do listy Dostawcy **dodatkowi.** (Nie przegap gwiazdki na początku ciągu.)

![Dodatkowi dostawcy Perfview](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a>Dostawca usługi Windows EventLog

Pakiet dostawcy [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) wysyła dane wyjściowe dziennika do dziennika zdarzeń systemu Windows.

```csharp
logging.AddEventLog();
```

[Przeciążenia AddEventLog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) pozwalają <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>przejść w . Jeśli `null` zostanie określony, używane są następujące ustawienia domyślne:

* `LogName`&ndash; "Aplikacja"
* `SourceName`&ndash; ".NET Środowisko uruchomieniowe"
* `MachineName`&ndash; maszyna lokalna

Zdarzenia są rejestrowane dla [poziomu ostrzeżenia i wyższej](#log-level). Aby rejestrować `Warning`zdarzenia niższe niż , jawnie ustawić poziom dziennika. Na przykład dodaj następujące elementy do pliku *appsettings.json:*

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a>Dostawca tracesource

Pakiet dostawcy [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) używa <xref:System.Diagnostics.TraceSource> bibliotek i dostawców.

```csharp
logging.AddTraceSource(sourceSwitchName);
```

[AddTraceSource przeciążenia](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) umożliwiają przekazywanie w przełączniku źródłowym i odbiornik śledzenia.

Aby korzystać z tego dostawcy, aplikacja musi działać w programie .NET Framework (a nie .NET Core). Dostawca może kierować wiadomości do różnych [odbiorników,](/dotnet/framework/debug-trace-profile/trace-listeners)takich jak <xref:System.Diagnostics.TextWriterTraceListener> używane w przykładowej aplikacji.

### <a name="azure-app-service-provider"></a>Dostawca usługi aplikacji platformy Azure

Pakiet dostawcy [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) zapisuje dzienniki do plików tekstowych w systemie plików aplikacji usługi Azure App Service i magazynu [obiektów blob](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) na koncie usługi Azure Storage.

```csharp
logging.AddAzureWebAppDiagnostics();
```

::: moniker range=">= aspnetcore-3.0"

Pakiet dostawcy nie jest uwzględniony w udostępnionej ramach. Aby użyć dostawcy, dodaj pakiet dostawcy do projektu.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Pakiet dostawcy nie jest uwzględniony w [metapakiecie Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app). Podczas kierowania .NET Framework lub `Microsoft.AspNetCore.App` odwoływania się do metapakiet, dodaj pakiet dostawcy do projektu. 

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

Aby skonfigurować ustawienia <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> dostawcy, należy użyć i <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, jak pokazano w poniższym przykładzie:

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=17-28)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Aby skonfigurować ustawienia <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> dostawcy, należy użyć i <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, jak pokazano w poniższym przykładzie:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=19-27)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

Przeciążenie <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> pozwala przejść <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>w . Obiekt ustawień może zastąpić ustawienia domyślne, takie jak szablon danych wyjściowych rejestrowania, nazwa obiektu blob i limit rozmiaru pliku. (Szablon*wyjściowy* to szablon wiadomości, który jest stosowany do wszystkich dzienników `ILogger` oprócz tego, co jest dostarczane z wywołaniem metody.)

::: moniker-end

Podczas wdrażania w aplikacji usługi App Service aplikacja honoruje ustawienia w sekcji [Dzienniki usługi aplikacji](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) na stronie **Usługi aplikacji** w witrynie Azure portal. Po zaktualizowaniu następujących ustawień zmiany zaczynają obowiązywać natychmiast bez konieczności ponownego uruchamiania lub ponownego wprowadzania aplikacji.

* **Rejestrowanie aplikacji (system plików)**
* **Rejestrowanie aplikacji (obiekt blob)**

Domyślna lokalizacja plików dziennika znajduje się w folderze *D:\\\\home LogFiles\\Application,* a domyślną nazwą pliku jest *diagnostics-yyyymmdd.txt*. Domyślny limit rozmiaru pliku wynosi 10 MB, a domyślna maksymalna liczba zachowanych plików to 2. Domyślna nazwa obiektu blob to *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.

Dostawca działa tylko wtedy, gdy projekt jest uruchamiany w środowisku platformy Azure. Nie ma wpływu, gdy projekt&mdash;jest uruchamiany lokalnie nie zapisuje do plików lokalnych lub magazynu lokalnego rozwoju dla obiektów blob.

#### <a name="azure-log-streaming"></a>Przesyłanie strumieniowe dziennika platformy Azure

Przesyłanie strumieniowe dziennika platformy Azure umożliwia wyświetlanie aktywności dziennika w czasie rzeczywistym z:

* Serwer aplikacji
* Serwer www
* Śledzenie żądań nie powiodło się

Aby skonfigurować przesyłanie strumieniowe dziennika platformy Azure:

* Przejdź do strony **Dzienniki usługi app service** ze strony portalu aplikacji.
* Ustaw **rejestrowanie aplikacji (system plików)** **na Włączone**.
* Wybierz **poziom**dziennika . To ustawienie dotyczy tylko przesyłania strumieniowego dziennika platformy Azure, a nie innych dostawców rejestrowania w aplikacji.

Przejdź do strony **Strumień dziennika,** aby wyświetlić komunikaty aplikacji. Są one rejestrowane przez aplikację `ILogger` za pośrednictwem interfejsu.

### <a name="azure-application-insights-trace-logging"></a>Rejestrowanie śledzenia usługi Azure Application Insights

Pakiet dostawcy [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) zapisuje dzienniki w usłudze Azure Application Insights. Usługa Application Insights to usługa, która monitoruje aplikację sieci web i udostępnia narzędzia do wykonywania zapytań i analizowania danych telemetrycznych. Jeśli używasz tego dostawcy, można kwerendy i analizować dzienniki za pomocą narzędzi usługi Application Insights.

Dostawca rejestrowania jest uwzględniony jako zależność [microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), który jest pakietem, który udostępnia wszystkie dostępne dane telemetryczne dla ASP.NET Core. Jeśli używasz tego pakietu, nie trzeba instalować pakietu dostawcy.

Nie należy używać pakietu&mdash; [Microsoft.ApplicationInsights.Web,](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) który jest przeznaczony dla ASP.NET 4.x.

Więcej informacji zawierają następujące zasoby:

* [Omówienie usługi Application Insights](/azure/application-insights/app-insights-overview)
* [Usługa Application Insights dla aplikacji ASP.NET Core](/azure/azure-monitor/app/asp-net-core) — uruchom tutaj, jeśli chcesz zaimplementować pełny zakres danych telemetrycznych usługi Application Insights wraz z rejestrowaniem.
* [ApplicationInsightsLoggerProvider dla dzienników .NET Core ILogger](/azure/azure-monitor/app/ilogger) — uruchom tutaj, jeśli chcesz zaimplementować dostawcę rejestrowania bez pozostałej części telemetrii usługi Application Insights.
* [Karty rejestrowania usługi Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).
* [Instalowanie, konfigurowanie i inicjowanie zestawu SDK usługi Application Insights](/learn/modules/instrument-web-app-code-with-application-insights) — interaktywny samouczek w witrynie Microsoft Learn.

## <a name="third-party-logging-providers"></a>Zewnętrzni dostawcy usług rejestrowania

Struktury rejestrowania innych firm, które współpracują z ASP.NET Core:

* [elmah.io](https://elmah.io/) ([Repozytorium GitHub](https://github.com/elmahio/Elmah.Io.Extensions.Logging))
* [Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([Repozytorium GitHub](https://github.com/mattwcole/gelf-extensions-logging))
* [JSNLog](https://jsnlog.com/) ([Repozytorium GitHub](https://github.com/mperdeck/jsnlog))
* [KissLog.net](https://kisslog.net/) ([Repozytorium GitHub](https://github.com/catalingavan/KissLog-net))
* [Log4Net](https://logging.apache.org/log4net/) ([Repozytorium GitHub](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))
* [Loggr](https://loggr.net/) ([Repozytorium GitHub](https://github.com/imobile3/Loggr.Extensions.Logging))
* [NLog](https://nlog-project.org/) ([Repozytorium GitHub](https://github.com/NLog/NLog.Extensions.Logging))
* [Działko](https://sentry.io/welcome/) [(Repozytorium GitHub)](https://github.com/getsentry/sentry-dotnet)
* [Serilog](https://serilog.net/) ([Repozytorium GitHub](https://github.com/serilog/serilog-aspnetcore))
* [Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))

Niektóre struktury innych firm mogą wykonywać [rejestrowanie semantyczne, znane również jako rejestrowanie strukturalne.](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)

Korzystanie z struktury innej firmy jest podobne do korzystania z jednego z wbudowanych dostawców:

1. Dodaj pakiet NuGet do projektu.
1. Wywołanie `ILoggerFactory` metody rozszerzenia dostarczone przez strukturę rejestrowania.

Aby uzyskać więcej informacji, zobacz dokumentację każdego dostawcy. Dostawcy rejestrowania innych firm nie są obsługiwani przez firmę Microsoft.

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:fundamentals/logging/loggermessage>
