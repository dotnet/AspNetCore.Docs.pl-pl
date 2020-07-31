---
title: Rejestrowanie na platformie .NET Core i ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak używać struktury rejestrowania dostarczonej przez pakiet NuGet Microsoft. Extensions. Logging.
ms.author: riande
ms.custom: mvc
ms.date: 6/29/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/logging/index
ms.openlocfilehash: a4962c3132c60b68cb2d4b5a97e9b082aba15d15
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2020
ms.locfileid: "87330772"
---
# <a name="logging-in-net-core-and-aspnet-core"></a>Rejestrowanie na platformie .NET Core i ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

[Kirka Larkin](https://twitter.com/serpent5), [Juergen Gutsch](https://github.com/JuergenGutsch) i [Rick Anderson](https://twitter.com/RickAndMSFT)

Platforma .NET Core obsługuje interfejs API rejestrowania, który współpracuje z różnymi dostawcami rejestrowania wbudowanych i innych firm. W tym artykule pokazano, jak używać interfejsu API rejestrowania z wbudowanymi dostawcami.

Większość przykładów kodu pokazanych w tym artykule pochodzą z ASP.NET Core aplikacji. Części tych fragmentów kodu dotyczące rejestrowania mają zastosowanie do dowolnej aplikacji .NET Core, która korzysta z [hosta ogólnego](xref:fundamentals/host/generic-host). Szablony aplikacji sieci Web ASP.NET Core korzystają z hosta ogólnego.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples/3.x) ([jak pobrać](xref:index#how-to-download-a-sample))

<a name="lp"></a>

## <a name="logging-providers"></a>Dostawcy rejestrowania

Dostawcy rejestrowania przechowują dzienniki, z wyjątkiem `Console` dostawcy wyświetlającego dzienniki. Na przykład dostawca usługi Azure Application Insights przechowuje dzienniki w usłudze Azure Application Insights. Można włączyć wielu dostawców.

Domyślne szablony aplikacji sieci Web ASP.NET Core:

* Użyj [hosta ogólnego](xref:fundamentals/host/generic-host).
* Wywołanie <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> , które dodaje następujących dostawców rejestrowania:
  * [Konsola](#console)
  * [Debugowanie](#debug)
  * [EventSource](#event-source)
  * [EventLog](#welog): tylko system Windows

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_TemplateCode&highlight=9)]

Poprzedni kod przedstawia `Program` klasę utworzoną za pomocą szablonów aplikacji sieci web ASP.NET Core. W następnych sekcjach przedstawiono przykłady w oparciu o szablony aplikacji sieci Web ASP.NET Core, które korzystają z hosta ogólnego. [Aplikacje konsolowe inne niż host](#nhca) są omawiane w dalszej części tego dokumentu.

Aby zastąpić domyślny zestaw dostawców rejestrowania dodany przez `Host.CreateDefaultBuilder` , należy wywołać `ClearProviders` i dodać wymaganych dostawców rejestrowania. Na przykład następujący kod:

* Wywołuje, <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> Aby usunąć wszystkie <xref:Microsoft.Extensions.Logging.ILoggerProvider> wystąpienia z konstruktora.
* Dodaje dostawcę rejestrowania [konsoli](#console) .

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_AddProvider&highlight=5-6)]

Aby uzyskać dodatkowych dostawców, zobacz:

* [Wbudowani dostawcy rejestrowania](#bilp)
* [Dostawcy rejestrowania innych firm](#third-party-logging-providers).

## <a name="create-logs"></a>Tworzenie dzienników

Aby utworzyć dzienniki, użyj <xref:Microsoft.Extensions.Logging.ILogger%601> obiektu z [iniekcji zależności](xref:fundamentals/dependency-injection) (di).

Poniższy przykład:

* Tworzy rejestrator, `ILogger<AboutModel>` który używa *kategorii* dziennika w pełni kwalifikowanej nazwy typu `AboutModel` . Kategoria dziennika jest ciągiem, który jest skojarzony z każdym dziennikiem.
* Wywołania <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> do rejestrowania na `Information` poziomie. *Poziom* dziennika wskazuje ważność rejestrowanego zdarzenia.

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=5,14)]

[Poziomy](#log-level) i [Kategorie](#log-category) zostały wyjaśnione bardziej szczegółowo w dalszej części tego dokumentu.

Aby uzyskać informacje na temat Blazor , zobacz [Tworzenie dzienników w Blazor i Blazor WebAssembly ](#clib) w tym dokumencie.

[Tworzenie dzienników w programie głównych i początkowych](#clms) przedstawia sposób tworzenia dzienników w `Main` i `Startup` .

## <a name="configure-logging"></a>Konfigurowanie rejestrowania

Konfiguracja rejestrowania jest zwykle udostępniana w `Logging` sekcji *AppSettings*. `{Environment}` pliki *. JSON* . Następujące *appsettings.Development.js* pliku są generowane przez Szablony aplikacji sieci Web ASP.NET Core:

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Development.json)]

W powyższym formacie JSON:

* `"Default"`Określono, `"Microsoft"` , i `"Microsoft.Hosting.Lifetime"` Kategorie.
* `"Microsoft"`Kategoria dotyczy wszystkich kategorii, które zaczynają się od `"Microsoft"` . Na przykład to ustawienie ma zastosowanie do `"Microsoft.AspNetCore.Routing.EndpointMiddleware"` kategorii.
* `"Microsoft"`Kategoria rejestruje się na poziomie dziennika `Warning` lub wyższym.
* `"Microsoft.Hosting.Lifetime"`Kategoria jest bardziej precyzyjna niż `"Microsoft"` Kategoria, więc jest `"Microsoft.Hosting.Lifetime"` rejestrowana w kategorii na poziomie dziennika "informacje" i wyższych.
* Określony dostawca dziennika nie został określony, dlatego `LogLevel` dotyczy wszystkich włączonych dostawców rejestrowania z wyjątkiem [dziennika zdarzeń systemu Windows](#welog).

`Logging`Właściwość może mieć <xref:Microsoft.Extensions.Logging.LogLevel> właściwości dostawcy dzienników i. `LogLevel`Określa minimalny [poziom](#log-level) rejestrowania wybranych kategorii. W poprzednim pliku JSON `Information` i `Warning` poziomy dziennika są określone. `LogLevel`wskazuje ważność dziennika i zakres od 0 do 6:

`Trace`= 0, `Debug` = 1, `Information` = 2, `Warning` = 3, `Error` = 4, `Critical` = 5 i `None` = 6.

Gdy `LogLevel` jest określony, funkcja rejestrowania jest włączona dla komunikatów na określonym poziomie i wyższych. W poprzednim pliku JSON `Default` Kategoria jest zarejestrowana dla `Information` i wyższa. Na przykład,,, `Information` `Warning` `Error` i `Critical` komunikaty są rejestrowane. Jeśli nie `LogLevel` jest określony, rejestrowane są wartości domyślne na `Information` poziomie. Aby uzyskać więcej informacji, zobacz [poziomy dzienników](#llvl).

Właściwość dostawcy może określać `LogLevel` Właściwość. `LogLevel`w obszarze dostawca Określa poziomy do rejestrowania dla tego dostawcy i zastępuje ustawienia dziennika niedostawcy. Rozważmy następujące *appsettings.js* pliku:

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Prod2.json)]

Ustawienia w `Logging.{providername}.LogLevel` ustawieniach przesłonięcia w programie `Logging.LogLevel` . W powyższym kodzie JSON `Debug` domyślny poziom rejestrowania dostawcy jest ustawiany na `Information` :

`Logging:Debug:LogLevel:Default:Information`

Powyższe ustawienie określa `Information` poziom rejestrowania dla każdej `Logging:Debug:` kategorii z wyjątkiem `Microsoft.Hosting` . Gdy określona kategoria jest wymieniona, określona Kategoria zastępuje domyślną kategorię. W powyższym formacie JSON `Logging:Debug:LogLevel` Kategorie `"Microsoft.Hosting"` i `"Default"` zastępują ustawienia w`Logging:LogLevel`

Minimalny poziom dziennika można określić dla dowolnego z:

* Określeni dostawcy: na przykład`Logging:EventSource:LogLevel:Default:Information`
* Określone kategorie: na przykład`Logging:LogLevel:Microsoft:Warning`
* Wszyscy dostawcy i wszystkie kategorie:`Logging:LogLevel:Default:Warning`

Wszystkie dzienniki poniżej minimalnego poziomu ***nie***są następujące:

* Przeszedł do dostawcy.
* Zarejestrowane lub wyświetlone.

Aby pominąć wszystkie dzienniki, należy określić [wartość LogLevel. None](xref:Microsoft.Extensions.Logging.LogLevel). `LogLevel.None`ma wartość 6, która jest większa niż `LogLevel.Critical` (5).

Jeśli dostawca obsługuje [zakresy rejestrowania](#logscopes), `IncludeScopes` wskazuje, czy są one włączone. Aby uzyskać więcej informacji, zobacz [zakresy dzienników](#logscopes)

Następująca *appsettings.jsw* pliku zawiera wszystkich dostawców włączonych domyślnie:

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Production.json)]

W poprzednim przykładzie:

* Kategorie i poziomy nie są sugerowanymi wartościami. Przykład podano, aby wyświetlić wszystkich dostawców domyślnych.
* Ustawienia w `Logging.{providername}.LogLevel` ustawieniach przesłonięcia w programie `Logging.LogLevel` . Na przykład poziom w `Debug.LogLevel.Default` zastępują poziom w `LogLevel.Default` .
* Każdy domyślny *alias* dostawcy jest używany. Każdy dostawca definiuje *alias* , który może być używany w konfiguracji zamiast w pełni kwalifikowanej nazwy typu. Aliasy wbudowane dostawcy to:
  * Konsola
  * Debugowanie
  * EventSource
  * Elemencie
  * AzureAppServicesFile
  * AzureAppServicesBlob
  * ApplicationInsights

## <a name="set-log-level-by-command-line-environment-variables-and-other-configuration"></a>Ustawianie poziomu dziennika według wiersza polecenia, zmiennych środowiskowych i innych konfiguracji

Poziom dziennika może być ustawiony przez dowolnego [dostawcę konfiguracji](xref:fundamentals/configuration/index). 

[!INCLUDE[](~/includes/environmentVarableColon.md)]

Następujące polecenia:

* Ustaw klucz środowiska na `Logging:LogLevel:Microsoft` wartość `Information` w systemie Windows.
* Przetestuj ustawienia w przypadku korzystania z aplikacji utworzonej za pomocą szablonów aplikacji sieci Web ASP.NET Core. `dotnet run`Polecenie musi być uruchamiane w katalogu projektu po użyciu `set` .

```cmd
set Logging__LogLevel__Microsoft=Information
dotnet run
```

Poprzednie ustawienie środowiska:

* Jest ustawiana tylko w ramach procesów uruchomionych z poziomu okna polecenia, które zostały ustawione w.
* Nie są odczytywane przez przeglądarki uruchomione przy użyciu programu Visual Studio.

Następujące polecenie [setx](/windows-server/administration/windows-commands/setx) ustawia również klucz środowiska i wartość w systemie Windows. W przeciwieństwie do `set` , `setx` Ustawienia są utrwalane. `/M`Przełącznik ustawia zmienną w środowisku systemowym. Jeśli `/M` nie jest używany, zmienna środowiskowa użytkownika jest ustawiona.

```cmd
setx Logging__LogLevel__Microsoft=Information /M
```

Na [Azure App Service](https://azure.microsoft.com/services/app-service/)wybierz pozycję **nowe ustawienie aplikacji** na stronie **Konfiguracja > ustawienia** . Ustawienia aplikacji Azure App Service są następujące:

* Szyfrowane i przesyłane przez zaszyfrowanego kanału.
* Uwidocznione jako zmienne środowiskowe.

Aby uzyskać więcej informacji, zobacz artykuł [Azure Apps: zastępowanie konfiguracji aplikacji przy użyciu witryny Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).

Aby uzyskać więcej informacji na temat ustawiania ASP.NET Core wartości konfiguracji przy użyciu zmiennych środowiskowych, zobacz [zmienne środowiskowe](xref:fundamentals/configuration/index#environment-variables). Aby uzyskać informacje na temat korzystania z innych źródeł konfiguracji, w tym z wiersza polecenia, Azure Key Vault, konfiguracja aplikacji platformy Azure, inne formaty plików i inne, zobacz <xref:fundamentals/configuration/index> .

## <a name="how-filtering-rules-are-applied"></a>Jak są stosowane reguły filtrowania

Po <xref:Microsoft.Extensions.Logging.ILogger%601> utworzeniu obiektu <xref:Microsoft.Extensions.Logging.ILoggerFactory> obiekt wybiera jedną regułę dla każdego dostawcy, która ma zostać zastosowana do tego rejestratora. Wszystkie komunikaty zapisywane przez `ILogger` wystąpienie są filtrowane na podstawie wybranych reguł. Dla każdego dostawcy i pary kategorii jest wybierana najbardziej konkretna reguła z dostępnych reguł.

Następujący algorytm jest używany dla każdego dostawcy, gdy `ILogger` jest tworzony dla danej kategorii:

* Wybierz wszystkie reguły, które pasują do dostawcy lub jego aliasu. Jeśli nie zostanie znalezione dopasowanie, zaznacz wszystkie reguły z pustym dostawcą.
* W wyniku poprzedniego kroku wybierz pozycję reguły z najdłuższym prefiksem kategorii. Jeśli nie zostanie znalezione dopasowanie, zaznacz wszystkie reguły, które nie określają kategorii.
* Jeśli wybrano wiele reguł, zrób to **ostatnie** .
* Jeśli nie wybrano żadnych reguł, użyj `MinimumLevel` .

<a name="dnrvs"></a>

## <a name="logging-output-from-dotnet-run-and-visual-studio"></a>Rejestrowanie danych wyjściowych z przebiegu dotnet i programu Visual Studio

Wyświetlane są dzienniki utworzone przy użyciu [domyślnych dostawców rejestrowania](#lp) :

* W programie Visual Studio
  * W oknie dane wyjściowe debugowania podczas debugowania.
  * W oknie ASP.NET Core serwer sieci Web.
* W oknie konsoli, gdy aplikacja jest uruchamiana z `dotnet run` .

Dzienniki zaczynające się od kategorii "Microsoft" pochodzą z kodu ASP.NET Core Framework. ASP.NET Core i kod aplikacji używają tego samego rejestrowania interfejsu API i dostawców.

<a name="lcat"></a>

## <a name="log-category"></a>Kategoria dziennika

Po `ILogger` utworzeniu obiektu zostanie określona *Kategoria* . Ta kategoria jest dołączona do każdego komunikatu dziennika utworzonego przez to wystąpienie `ILogger` . Ciąg kategorii jest dowolny, ale Konwencja ma używać nazwy klasy. Przykładowo może to być nazwa w kontrolerze `"TodoApi.Controllers.TodoController"` . ASP.NET Core aplikacje sieci Web używają `ILogger<T>` do automatycznego pobrania `ILogger` wystąpienia, które używa w pełni kwalifikowanej nazwy typu `T` jako kategorii:

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/Privacy.cshtml.cs?name=snippet)]

Aby jawnie określić kategorię, wywołaj `ILoggerFactory.CreateLogger` :

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/Contact.cshtml.cs?name=snippet)]

`ILogger<T>`jest odpowiednikiem wywołania `CreateLogger` z w pełni kwalifikowaną nazwą typu `T` .

<a name="llvl"></a>

## <a name="log-level"></a>Poziom dziennika

W poniższej tabeli wymieniono <xref:Microsoft.Extensions.Logging.LogLevel> wartości, wygodną `Log{LogLevel}` metodę rozszerzenia oraz Sugerowane użycie:

| LogLevel | Wartość | Metoda | Opis |
| -------- | ----- | ------ | ----------- |
| [Ślad](xref:Microsoft.Extensions.Logging.LogLevel) | 0 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogTrace%2A> | Zawierają najwięcej szczegółowych komunikatów. Te komunikaty mogą zawierać poufne dane aplikacji. Te komunikaty są domyślnie wyłączone i ***nie*** powinny być włączone w środowisku produkcyjnym. |
| [Debugowanie](xref:Microsoft.Extensions.Logging.LogLevel) | 1 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug%2A> | Na potrzeby debugowania i programowania. W środowisku produkcyjnym należy używać ostrożnie z powodu dużego wolumenu. |
| [Informacje](xref:Microsoft.Extensions.Logging.LogLevel) | 2 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation%2A> | Śledzi ogólny przepływ aplikacji. Może mieć wartość długoterminową. |
| [Ostrzeżenie](xref:Microsoft.Extensions.Logging.LogLevel) | 3 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> | Dla nietypowych lub nieoczekiwanych zdarzeń. Zwykle zawiera błędy lub warunki, które nie powodują błędu aplikacji. |
| [Błąd](xref:Microsoft.Extensions.Logging.LogLevel) | 4 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A> | W przypadku błędów i wyjątków, których nie można obsłużyć. Te komunikaty wskazują na niepowodzenie podczas bieżącej operacji lub żądania, a nie awarię całej aplikacji. |
| [Krytyczne](xref:Microsoft.Extensions.Logging.LogLevel) | 5 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogCritical%2A> | Dla niepowodzeń, które wymagają natychmiastowej uwagi. Przykłady: scenariusze utraty danych, brak miejsca na dysku. |
| [Brak](xref:Microsoft.Extensions.Logging.LogLevel) | 6 | | Określa, że Kategoria rejestrowania nie powinna zapisywać żadnych komunikatów. |

W poprzedniej tabeli `LogLevel` znajduje się na liście od najniższej do najwyższej wagi.

Pierwszy parametr metody [log](xref:Microsoft.Extensions.Logging.LoggerExtensions) <xref:Microsoft.Extensions.Logging.LogLevel> wskazuje ważność dziennika. Zamiast wywoływania `Log(LogLevel, ...)` , większość deweloperów wywołuje metody rozszerzenia [dziennika {LogLevel}](xref:Microsoft.Extensions.Logging.LoggerExtensions) . `Log{LogLevel}`Metody rozszerzające [wywołują metodę log i określają wartość LogLevel](https://github.com/dotnet/extensions/blob/release/3.1/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs). Na przykład następujące dwa wywołania rejestrowania są funkcjonalnie równoważne i generują ten sam dziennik:

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet0&highlight=6-7)]

`MyLogEvents.TestItem`jest IDENTYFIKATORem zdarzenia. `MyLogEvents`jest częścią przykładowej aplikacji i jest wyświetlana w sekcji [Identyfikator zdarzenia dziennika](#leid) .

[!INCLUDE[](~/includes/MyDisplayRouteInfoBoth.md)]

Poniższy kod tworzy `Information` i `Warning` rejestruje:

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

W poprzednim kodzie pierwszy `Log{LogLevel}` parametr, `MyLogEvents.GetItem` , jest [Identyfikator zdarzenia dziennika](#leid). Drugi parametr jest szablonem wiadomości z symbolami zastępczymi dla wartości argumentów dostarczonych przez pozostałe parametry metody. Parametry metody zostały wyjaśnione w sekcji [szablon komunikatu](#lmt) w dalszej części tego dokumentu.

Wywołaj odpowiednią `Log{LogLevel}` metodę, aby kontrolować, ile danych wyjściowych dziennika jest zapisywana w określonym nośniku magazynowania. Na przykład:

* W środowisku produkcyjnym:
  * Rejestrowanie na poziomie `Trace` lub `Information` powoduje utworzenie dużej ilości szczegółowych komunikatów dziennika. Aby kontrolować koszty i nie przekraczać limitów magazynowania danych `Trace` , `Information` komunikaty dzienników i na poziomie magazynu o dużej ilości danych. Rozważ ograniczenie `Trace` i `Information` do określonych kategorii.
  * Rejestrowanie przy `Warning` użyciu `Critical` poziomów powinno generować kilka komunikatów dziennika.
    * Koszty i limity magazynu zazwyczaj nie są przedmiotem obaw.
    * Niektóre dzienniki zapewniają większą elastyczność w zakresie wyboru magazynu danych.
* W programie Development:
  * Ustaw wartość `Warning`.
  * Dodawanie `Trace` lub `Information` komunikaty podczas rozwiązywania problemów. Aby ograniczyć ilość danych wyjściowych, ustaw `Trace` lub `Information` tylko dla kategorii poddawanych badaniu.

ASP.NET Core zapisuje dzienniki dla zdarzeń struktury. Rozważmy na przykład dane wyjściowe dziennika dla:

* RazorAplikacja ze stronami utworzona przy użyciu szablonów ASP.NET Core.
* Rejestrowanie ustawione na`Logging:Console:LogLevel:Microsoft:Information`
* Nawigacja do strony prywatność:

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 GET https://localhost:5001/Privacy
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint '/Privacy'
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[3]
      Route matched with {page = "/Privacy"}. Executing page /Privacy
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[101]
      Executing handler method DefaultRP.Pages.PrivacyModel.OnGet - ModelState is Valid
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[102]
      Executed handler method OnGet, returned result .
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[103]
      Executing an implicit handler method - ModelState is Valid
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[104]
      Executed an implicit handler method, returned result Microsoft.AspNetCore.Mvc.RazorPages.PageResult.
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[4]
      Executed page /Privacy in 74.5188ms
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint '/Privacy'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 149.3023ms 200 text/html; charset=utf-8
```

Następujące zestawy JSON `Logging:Console:LogLevel:Microsoft:Information` :

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.MSFT.json)]

<a name="leid"></a>

## <a name="log-event-id"></a>Identyfikator zdarzenia dziennika

Każdy dziennik może określać *Identyfikator zdarzenia*. Przykładowa aplikacja używa `MyLogEvents` klasy do definiowania identyfikatorów zdarzeń:

<!-- Review: to bad there is no way to use an enum for event ID's -->
[!code-csharp[](index/samples/3.x/TodoApiDTO/Models/MyLogEvents.cs?name=snippet_LoggingEvents)]

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

Identyfikator zdarzenia kojarzy zestaw zdarzeń. Na przykład wszystkie dzienniki związane z wyświetlaniem listy elementów na stronie mogą być 1001.

Dostawca rejestrowania może przechowywać identyfikator zdarzenia w polu identyfikatora, w komunikacie rejestrowania lub wcale. Dostawca debugowania nie pokazuje identyfikatorów zdarzeń. Dostawca konsoli pokazuje identyfikatory zdarzeń w nawiasach po kategorii:

```console
info: TodoApi.Controllers.TodoItemsController[1002]
      Getting item 1
warn: TodoApi.Controllers.TodoItemsController[4000]
      Get(1) NOT FOUND
```

Niektórzy dostawcy rejestrowania przechowują identyfikator zdarzenia w polu, co umożliwia filtrowanie identyfikatorów.

<a name="lmt"></a>

## <a name="log-message-template"></a>Szablon komunikatu dziennika
<!-- Review, Each log API uses a message template. -->
Każdy interfejs API dziennika używa szablonu wiadomości. Szablon wiadomości może zawierać symbole zastępcze, dla których podano argumenty. Użyj nazw dla symboli zastępczych, a nie liczby.

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

Kolejność symboli zastępczych, nie ich nazw, określa, które parametry są używane do dostarczania ich wartości. W poniższym kodzie nazwy parametrów są spoza sekwencji w szablonie wiadomości:

```csharp
string p1 = "param1";
string p2 = "param2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

Poprzedni kod tworzy komunikat dziennika z wartościami parametrów w kolejności:

```text
Parameter values: param1, param2
```

Takie podejście umożliwia dostawcom rejestrowania implementowanie [semantyki lub rejestrowania strukturalnego](https://github.com/NLog/NLog/wiki/How-to-use-structured-logging). Same argumenty są przesyłane do systemu rejestrowania, a nie tylko dla sformatowanego szablonu wiadomości. Umożliwia to dostawcom rejestrowania przechowywanie wartości parametrów jako pól. Rozważmy na przykład następujące metody rejestratora:

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

Na przykład podczas rejestrowania w usłudze Azure Table Storage:

* Każda jednostka tabeli platformy Azure może `ID` mieć `RequestTime` właściwości i.
* Tabele z właściwościami upraszczają zapytania dotyczące zarejestrowanych danych. Na przykład zapytanie może znaleźć wszystkie dzienniki w określonym `RequestTime` zakresie bez konieczności analizowania limitu czasu wiadomości tekstowej.

## <a name="log-exceptions"></a>Wyjątki dziennika

Metody rejestratora mają przeciążenia przyjmujące parametr wyjątku:

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet_Exp)]

[!INCLUDE[](~/includes/MyDisplayRouteInfoBoth.md)]

Rejestrowanie wyjątków jest specyficzne dla dostawcy.

### <a name="default-log-level"></a>Domyślny poziom dziennika

Jeśli domyślny poziom rejestrowania nie jest ustawiony, domyślna wartość poziomu dziennika to `Information` .

Rozważmy na przykład następującą aplikację sieci Web:

* Utworzono za pomocą szablonów aplikacji sieci Web ASP.NET.
* *appsettings.json* i *appsettings.Development.jspo* usunięciu lub zmianie nazwy.

Po powyższej instalacji przechodzenie do strony prywatność lub Strona główna powoduje utworzenie wielu `Trace` , `Debug` i `Information` komunikatów z `Microsoft` nazwą kategorii.

Poniższy kod ustawia domyślny poziom rejestrowania, jeśli domyślny poziom rejestrowania nie jest ustawiony w konfiguracji:

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_MinLevel&highlight=10)]

<!-- review required: I say this a couple times -->
Ogólnie rzecz biorąc, poziomy dziennika należy określić w obszarze Konfiguracja i nie kod.

### <a name="filter-function"></a>Funkcja filtrowania

Funkcja filtru jest wywoływana dla wszystkich dostawców i kategorii, które nie mają przypisanych do nich reguł przez konfigurację lub kod:

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_FilterFunction)]

Poprzedni kod wyświetla dzienniki konsoli, gdy kategoria zawiera `Controller` lub `Microsoft` i poziom dziennika jest `Information` lub wyższy.

Ogólnie rzecz biorąc, poziomy dziennika należy określić w obszarze Konfiguracja i nie kod.

## <a name="aspnet-core-and-ef-core-categories"></a>Kategorie ASP.NET Core i EF Core

Poniższa tabela zawiera niektóre kategorie używane przez ASP.NET Core i Entity Framework Core z uwagami dotyczącymi dzienników:

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

Aby wyświetlić więcej kategorii w oknie konsoli, należy ustawić **appsettings.Development.jsna** następujące elementy:

[!code-json[](index/samples/3.x/MyMain/appsettings.Trace.json)]

<!-- Review: What other providers support scopes? Console is not generally used in staging/production  -->

<a name="logscopes"></a>

## <a name="log-scopes"></a>Zakresy dziennika

 *Zakres* może grupować zestaw operacji logicznych. Takie grupowanie może służyć do dołączania tych samych danych do każdego dziennika, który został utworzony jako część zestawu. Na przykład każdy dziennik utworzony w ramach przetwarzania transakcji może zawierać identyfikator transakcji.

Zakres:

* Jest <xref:System.IDisposable> typem zwracanym przez <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> metodę.
* Obowiązuje do momentu jego usunięcia.

Następujące dostawcy obsługują zakresy:

* `Console`
* [AzureAppServicesFile i AzureAppServicesBlob](xref:Microsoft.Extensions.Logging.AzureAppServices.BatchingLoggerOptions.IncludeScopes)

Użyj zakresu przez Zawijanie wywołań rejestratora w `using` bloku:

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet_Scopes)]

Poniższy kod JSON włącza zakresy dla dostawcy konsoli:

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Scopes.json)]

Poniższy kod włącza zakresy dla dostawcy konsoli:

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_Scopes)]

Ogólnie rzecz biorąc, rejestrowanie powinno być określone w konfiguracji, a nie w kodzie.

<a name="bilp"></a>

## <a name="built-in-logging-providers"></a>Wbudowani dostawcy rejestrowania

ASP.NET Core obejmuje następujących dostawców rejestrowania:

* [Konsola](#console)
* [Debugowanie](#debug)
* [EventSource](#event-source)
* [Elemencie](#welog)
* [AzureAppServicesFile i AzureAppServicesBlob](#azure-app-service)
* [ApplicationInsights](#azure-application-insights)

Aby uzyskać informacje dotyczące `stdout` rejestrowania i debugowania przy użyciu modułu ASP.NET Core, zobacz <xref:test/troubleshoot-azure-iis> i <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection> .

### <a name="console"></a>Konsola

`Console`Dostawca rejestruje dane wyjściowe w konsoli programu. Aby uzyskać więcej informacji na temat wyświetlania `Console` dzienników w programie Development, zobacz [Rejestrowanie danych wyjściowych z uruchamiania programu dotnet i programu Visual Studio](#dnrvs).

### <a name="debug"></a>Debugowanie

`Debug`Dostawca zapisuje dane wyjściowe dziennika za pomocą klasy [System. Diagnostics. Debug](/dotnet/api/system.diagnostics.debug) . Wywołania do `System.Diagnostics.Debug.WriteLine` zapisu dla `Debug` dostawcy.

W systemie Linux `Debug` Lokalizacja dziennika dostawcy jest zależna od dystrybucji i może być jedną z następujących czynności:

* */var/log/message*
* */var/log/syslog*

### <a name="event-source"></a>Źródło zdarzeń

`EventSource`Dostawca zapisuje do międzyplatformowego źródła zdarzeń o nazwie `Microsoft-Extensions-Logging` . W systemie Windows Dostawca używa [funkcji ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).

#### <a name="dotnet-trace-tooling"></a>narzędzia śledzenia dotnet

Narzędzie do [śledzenia dotnet](/dotnet/core/diagnostics/dotnet-trace) to międzyplatformowe narzędzie globalne interfejsu wiersza polecenia, które umożliwia zbieranie śladów programu .NET Core uruchomionego procesu. Narzędzie zbiera <xref:Microsoft.Extensions.Logging.EventSource> dane dostawcy za pomocą <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource> .

Aby uzyskać instrukcje dotyczące instalacji, zobacz [dotnet-Trace](/dotnet/core/diagnostics/dotnet-trace) .

Użyj narzędzi śledzenia dotnet, aby zebrać ślad z aplikacji:

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

   W przypadku korzystania z powłoki poleceń programu PowerShell należy ująć `--providers` wartość w apostrofy ( `'` ):

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   Na platformach innych niż Windows Dodaj opcję, `-f speedscope` Aby zmienić format wyjściowego pliku śledzenia na `speedscope` .

   | Słowo kluczowe | Opis |
   | :-----: | ----------- |
   | 1       | Rejestruj meta zdarzenia dotyczące `LoggingEventSource` . Nie rejestruje zdarzeń z `ILogger` ). |
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

   `FilterSpecs`wpisy dla `{Logger Category}` i `{Event Level}` przedstawiają dodatkowe warunki filtrowania dzienników. Oddzielaj `FilterSpecs` wpisy średnikami ( `;` ).

   Przykład użycia powłoki poleceń systemu Windows (**Brak** pojedynczego cudzysłowu wokół `--providers` wartości):

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   Poprzednie polecenie aktywuje:

   * Rejestrator źródła zdarzeń do tworzenia sformatowanych ciągów ( `4` ) dla błędów ( `2` ).
   * `Microsoft.AspNetCore.Hosting`Rejestrowanie na `Informational` poziomie rejestrowania ( `4` ).

1. Zatrzymaj narzędzia śledzenia dotnet, naciskając klawisz ENTER lub CTRL + C.

   Ślad jest zapisywany z nazwą *Trace. webtrace* w folderze, w którym `dotnet trace` jest wykonywane polecenie.

1. Otwórz ślad z [Narzędzia PerfView](#perfview). Otwórz plik *Trace. webtrace* i zbadaj zdarzenia śledzenia.

Jeśli aplikacja nie kompiluje hosta za pomocą programu `CreateDefaultBuilder` , Dodaj [dostawcę źródła zdarzeń](#event-source-provider) do konfiguracji rejestrowania aplikacji.

Aby uzyskać więcej informacji, zobacz:

* [Śledzenie dla narzędzia do analizy wydajności (program dotnet-Trace)](/dotnet/core/diagnostics/dotnet-trace) (dokumentacja platformy .NET Core)
* [Śledzenie dla narzędzia analizy wydajności (dotnet-Trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dokumentacja repozytorium dotnet/Diagnostics)
* [LoggingEventSource — Klasa](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (przeglądarka interfejsu API platformy .NET)
* <xref:System.Diagnostics.Tracing.EventLevel>
* [Źródło odwołania LoggingEventSource (3,0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs): Aby uzyskać Źródło odwołania dla innej wersji, Zmień gałąź na `release/{Version}` , gdzie `{Version}` jest wymagana wersja ASP.NET Core.
* [Narzędzia PerfView](#perfview): przydatne do wyświetlania śladów źródła zdarzeń.

#### <a name="perfview"></a>Narzędzia PerfView

Użyj [Narzędzia Narzędzia PerfView](https://github.com/Microsoft/perfview) do zbierania i wyświetlania dzienników. Istnieją inne narzędzia do wyświetlania dzienników ETW, ale narzędzia PerfView zapewnia najlepsze środowisko pracy z zdarzeniami ETW emitowanymi przez ASP.NET Core.

Aby skonfigurować narzędzia PerfView do zbierania zdarzeń rejestrowanych przez tego dostawcę, Dodaj ciąg `*Microsoft-Extensions-Logging` do listy **dodatkowych dostawców** . Nie przegap `*` na początku ciągu.

<a name="welog"></a>

### <a name="windows-eventlog"></a>Dziennik zdarzeń systemu Windows

`EventLog`Dostawca wysyła dane wyjściowe dziennika do dziennika zdarzeń systemu Windows. W przeciwieństwie do innych dostawców `EventLog` dostawca nie dziedziczy ***not*** domyślnych ustawień nienależących do dostawcy. Jeśli `EventLog` nie określono ustawień dziennika, [domyślnie ustawiana jest wartość LogLevel. Warning](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L99-L103).

Aby rejestrować zdarzenia mniejsze niż <xref:Microsoft.Extensions.Logging.LogLevel.Warning?displayProperty=nameWithType> , jawnie ustaw poziom dziennika. Poniższy przykład ustawia domyślny poziom dziennika dziennika zdarzeń <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> :

```json
"Logging": {
  "EventLog": {
    "LogLevel": {
      "Default": "Information"
    }
  }
}
```

[Przeciążania addeventlog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) można przekazać <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings> . Jeśli `null` lub nie zostanie określony, są używane następujące ustawienia domyślne:

* `LogName`: "Aplikacja"
* `SourceName`: "Środowisko uruchomieniowe platformy .NET"
* `MachineName`: Nazwa komputera lokalnego jest używana.

Poniższy kod zmienia `SourceName` wartość domyślną na `".NET Runtime"` `MyLogs` :

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippetEventLog)]

### <a name="azure-app-service"></a>Azure App Service

Pakiet [Microsoft. Extensions. Logging. AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) Provider zapisuje dzienniki w plikach tekstowych w systemie plików aplikacji Azure App Service i w usłudze [BLOB Storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) na koncie usługi Azure Storage.

Pakiet dostawcy nie jest uwzględniony w udostępnionej infrastrukturze. Aby użyć dostawcy, Dodaj pakiet dostawcy do projektu.

Aby skonfigurować ustawienia dostawcy, użyj <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> i <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions> , jak pokazano w następującym przykładzie:

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_AzLogOptions)]

Po wdrożeniu do Azure App Service aplikacja korzysta z ustawień w sekcji [dzienniki App Service](/azure/app-service/web-sites-enable-diagnostic-log/#enable-application-logging-windows) na stronie **App Service** Azure Portal. Po zaktualizowaniu następujących ustawień zmiany zaczynają obowiązywać natychmiast, bez konieczności ponownego uruchomienia lub ponownej wdrożenia aplikacji.

* **Rejestrowanie aplikacji (system plików)**
* **Rejestrowanie aplikacji (BLOB)**

Domyślną lokalizacją plików dziennika jest folder *D: \\ Home \\ \\ LogFiles* , a domyślna nazwa pliku to *diagnostics-yyyymmdd.txt*. Domyślny limit rozmiaru pliku wynosi 10 MB, a domyślna maksymalna liczba zachowywanych plików to 2. Domyślną nazwą obiektu BLOB jest *{App-Name} {timestamp}/yyyy/mm/dd/hh/{guid} -applicationLog.txt*.

Ten dostawca rejestruje się tylko wtedy, gdy projekt jest uruchomiony w środowisku platformy Azure.

#### <a name="azure-log-streaming"></a>Przesyłanie strumieniowe dzienników Azure

Przesyłanie strumieniowe w usłudze Azure log obsługuje wyświetlanie dziennika w czasie rzeczywistym z:

* Serwer aplikacji
* Serwer sieci Web
* Śledzenie nieudanych żądań

Aby skonfigurować przesyłanie strumieniowe dzienników Azure:

* Przejdź do strony **dzienników App Service** ze strony portalu aplikacji.
* Ustaw **Rejestrowanie aplikacji (system plików)** na **włączone**.
* Wybierz **poziom**dziennika. To ustawienie dotyczy tylko przesyłania strumieniowego dzienników Azure.

Przejdź do strony **strumień dziennika** , aby wyświetlić dzienniki. Zarejestrowane komunikaty są rejestrowane przy użyciu `ILogger` interfejsu.

### <a name="azure-application-insights"></a>Azure Application Insights

Pakiet [Microsoft. Extensions. Logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) Provider zapisuje dzienniki na [platformie Azure Application Insights](/azure/azure-monitor/app/cloudservices). Application Insights to usługa, która monitoruje aplikację internetową i udostępnia narzędzia do wykonywania zapytań i analizowania danych telemetrycznych. Jeśli używasz tego dostawcy, możesz wysyłać zapytania i analizować dzienniki przy użyciu narzędzi Application Insights.

Dostawca rejestrowania jest dołączony jako zależność [Microsoft. ApplicationInsights. AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), który jest pakietem, który zapewnia wszystkie dostępne dane telemetryczne dla ASP.NET Core. Jeśli używasz tego pakietu, nie musisz instalować pakietu dostawcy.

Pakiet [Microsoft. ApplicationInsights. Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) jest przeznaczony dla ASP.NET 4. x, a nie ASP.NET Core.

Więcej informacji można znaleźć w następujących zasobach:

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
* [PLogger](https://www.nuget.org/packages/InvertedSoftware.PLogger.Core/) ([repozytorium GitHub](https://github.com/invertedsoftware/InvertedSoftware.PLogger.Core))
* [Sentry](https://sentry.io/welcome/) ([repozytorium GitHub](https://github.com/getsentry/sentry-dotnet))
* [Serilog](https://serilog.net/) ([repozytorium GitHub](https://github.com/serilog/serilog-aspnetcore))
* [Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([repozytorium GitHub](https://github.com/googleapis/google-cloud-dotnet))

Niektóre struktury innych firm mogą wykonywać [Rejestrowanie semantyczne, znane także jako rejestrowanie strukturalne](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Korzystanie z struktury innej firmy jest podobne do korzystania z jednego z wbudowanych dostawców:

1. Dodaj pakiet NuGet do projektu.
1. Wywoływanie `ILoggerFactory` metody rozszerzenia dostarczonej przez strukturę rejestrowania.

Aby uzyskać więcej informacji, zobacz dokumentację każdego dostawcy. Dostawcy rejestrowania innych firm nie są obsługiwani przez firmę Microsoft.

<a name="nhca"></a>

## <a name="non-host-console-app"></a>Aplikacja konsolowa niebędąca hostem

Przykład korzystania z hosta ogólnego w aplikacji konsolowej innej niż sieci Web można znaleźć w pliku *program.cs* [zadania w tle](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) ( <xref:fundamentals/host/hosted-services> ).

Rejestrowanie kodu dla aplikacji bez hosta ogólnego różni się w sposób, w jaki [są dodawane dostawcy](#add-providers) i [są tworzone rejestratory](#create-logs). 

### <a name="logging-providers"></a>Dostawcy rejestrowania

W aplikacji konsolowej innej niż host Wywołaj `Add{provider name}` metodę rozszerzenia dostawcy podczas tworzenia `LoggerFactory` :

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11-12)]

### <a name="create-logs"></a>Tworzenie dzienników

Aby utworzyć dzienniki, użyj <xref:Microsoft.Extensions.Logging.ILogger%601> obiektu. Użyj, `LoggerFactory` Aby utworzyć `ILogger` .

Poniższy przykład tworzy Rejestrator przy użyciu `LoggingConsoleApp.Program` jako kategorii.

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=14)]

W następujących przykładowych ASP.NET podstawowych, Rejestrator jest używany do tworzenia dzienników `Information` jako poziom. *Poziom* dziennika wskazuje ważność rejestrowanego zdarzenia.

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=15)]

[Poziomy](#log-level) i [Kategorie](#log-category) zostały omówione bardziej szczegółowo w tym dokumencie.

<a name="lhc"></a>

## <a name="log-during-host-construction"></a>Rejestruj podczas konstruowania hosta

Rejestrowanie podczas konstruowania hosta nie jest bezpośrednio obsługiwane. Można jednak użyć osobnego rejestratora. W poniższym przykładzie jest używany Rejestrator [Serilog](https://serilog.net/) do logowania `CreateHostBuilder` . `AddSerilog`używa konfiguracji statycznej określonej w `Log.Logger` :

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

<a name="csdi"></a>

## <a name="configure-a-service-that-depends-on-ilogger"></a>Konfigurowanie usługi, która zależy od ILogger

Wstrzykiwanie konstruktora rejestratora do `Startup` działa we wcześniejszych wersjach ASP.NET Core, ponieważ dla hosta sieci Web jest tworzony oddzielny kontener di. Aby uzyskać informacje o tym, dlaczego dla hosta generycznego jest tworzony tylko jeden kontener, zobacz [zawiadomienie o rozdzieleniu zmian](https://github.com/aspnet/Announcements/issues/353).

Aby skonfigurować usługę, która jest zależna od `ILogger<T>` programu, należy użyć iniekcji konstruktora lub dostarczyć metody fabryki. Podejście metody fabryki jest zalecane tylko wtedy, gdy nie ma żadnych innych opcji. Rozważmy na przykład usługę, która wymaga `ILogger<T>` wystąpienia przez di:

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup2.cs?name=snippet_ConfigureServices&highlight=6-10)]

Poprzedni wyróżniony kod jest obiektem, który jest uruchamiany po raz pierwszy, [musi utworzyć wystąpienie](/dotnet/api/system.func-2) `MyService` . W ten sposób można uzyskać dostęp do dowolnych zarejestrowanych usług.

<a name="clms"></a>

## <a name="create-logs-in-main"></a>Tworzenie dzienników w głównej

Następujący kod jest zarejestrowana `Main` przez pobranie `ILogger` wystąpienia z programu di po skompilowaniu hosta:

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_LogProgram)]

### <a name="create-logs-in-startup"></a>Tworzenie dzienników w programie startowym

Poniższy kod zapisuje dzienniki w `Startup.Configure` :

[!code-csharp[](index/samples/3.x/TodoApiDTO/Startup.cs?name=snippet_Configure)]

Zapisywanie dzienników przed ukończeniem instalacji programu DI Container w `Startup.ConfigureServices` metodzie nie jest obsługiwane:

* Iniekcja rejestratora do `Startup` konstruktora nie jest obsługiwana.
* Iniekcja rejestratora do `Startup.ConfigureServices` sygnatury metody nie jest obsługiwana

Przyczyną tego ograniczenia jest to, że rejestrowanie jest zależne od typu i konfiguracji, która z tego powodu zależy od DI. Kontener DI nie jest ustawiany do momentu `ConfigureServices` zakończenia.

Aby uzyskać informacje na temat konfigurowania usługi, która zależy od `ILogger<T>` lub dlaczego wstrzyknięcie konstruktora rejestratora do `Startup` pracy we wcześniejszych wersjach, zobacz [Konfigurowanie usługi, która jest zależna od ILogger](#csdi)

### <a name="no-asynchronous-logger-methods"></a>Brak metod rejestratora asynchronicznego

Rejestracja powinna być tak szybka, że nie jest to koszt wydajności kodu asynchronicznego. Jeśli magazyn danych rejestrowania jest wolny, nie zapisuj go bezpośrednio. Rozważ ręczne zapisanie komunikatów dziennika w szybkim magazynie, a następnie przeniesienie ich do wolnego magazynu później. Na przykład podczas rejestrowania do SQL Server nie należy tego robić bezpośrednio w `Log` metodzie, ponieważ `Log` metody są synchroniczne. Zamiast tego można synchronicznie dodawać komunikaty dziennika do kolejki w pamięci, a proces roboczy w tle ściągał komunikaty z kolejki, aby wykonać asynchroniczne działanie wypychania danych do SQL Server. Aby uzyskać więcej informacji, zobacz [ten](https://github.com/dotnet/AspNetCore.Docs/issues/11801) problem w serwisie GitHub.

<a name="clib"></a>

## <a name="change-log-levels-in-a-running-app"></a>Zmień poziomy dziennika w działającej aplikacji

Interfejs API rejestrowania nie zawiera scenariusza zmiany poziomów rejestrowania, gdy aplikacja jest uruchomiona. Niektórzy dostawcy konfiguracji mogą jednak ponownie ładować konfigurację, która zacznie natychmiastowo wpływać na konfigurację rejestrowania. Na przykład [dostawca konfiguracji plików](xref:fundamentals/configuration/index#file-configuration-provider)ponownie ładuje konfigurację rejestrowania. Jeśli konfiguracja zostanie zmieniona w kodzie w czasie, gdy aplikacja jest uruchomiona, aplikacja może wywołać [IConfigurationRoot. reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) , aby zaktualizować konfigurację rejestrowania aplikacji.

## <a name="ilogger-and-iloggerfactory"></a>ILogger i ILoggerFactory

<xref:Microsoft.Extensions.Logging.ILogger%601>Interfejsy i <xref:Microsoft.Extensions.Logging.ILoggerFactory> i implementacje są zawarte w zestaw .NET Core SDK. Są one również dostępne w następujących pakietach NuGet:  

* Interfejsy są w [Microsoft. Extensions. Logging. Abstracts](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/).
* Implementacje domyślne są w [Microsoft. Extensions. Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).

<!-- review. Why would you want to hard code filtering rules in code? -->
<a name="fric"></a>

## <a name="apply-log-filter-rules-in-code"></a>Zastosuj reguły filtru dziennika w kodzie

Preferowanym podejściem do ustawiania reguł filtru dziennika jest użycie [konfiguracji](xref:fundamentals/configuration/index).

Poniższy przykład pokazuje, jak zarejestrować reguły filtru w kodzie:

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_FilterInCode)]

`logging.AddFilter("System", LogLevel.Debug)`Określa `System` kategorię i poziom dziennika `Debug` . Filtr jest stosowany do wszystkich dostawców, ponieważ określony dostawca nie został skonfigurowany.

`AddFilter<DebugLoggerProvider>("Microsoft", LogLevel.Information)`określając

* `Debug`Dostawca rejestrowania.
* Poziom dziennika `Information` lub wyższy.
* Wszystkie kategorie zaczynające się od `"Microsoft"` .

## <a name="create-a-custom-logger"></a>Tworzenie rejestratora niestandardowego

Aby dodać niestandardowy rejestrator, Dodaj element <xref:Microsoft.Extensions.Logging.ILoggerProvider> with <xref:Microsoft.Extensions.Logging.ILoggerFactory> :

```csharp
public void Configure(
    IApplicationBuilder app,
    IWebHostEnvironment env,
    ILoggerFactory loggerFactory)
{
    loggerFactory.AddProvider(new CustomLoggerProvider(new CustomLoggerConfiguration()));
```

`ILoggerProvider`Tworzy co najmniej jedno `ILogger` wystąpienie. `ILogger`Wystąpienia są używane przez platformę do rejestrowania informacji.

### <a name="sample-custom-logger-configuration"></a>Przykładowa konfiguracja rejestratora niestandardowego

Przykład:

* Został zaprojektowany jako bardzo podstawowy przykład, który ustawia kolor konsoli dziennika według identyfikatora zdarzenia i poziomu dziennika. Rejestratory zazwyczaj nie zmieniają się według identyfikatora zdarzenia i nie są specyficzne dla poziomu dziennika.
* Tworzy różne wpisy konsoli kolorów dla każdego poziomu dziennika i identyfikatora zdarzenia przy użyciu następującego typu konfiguracji:

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerConfiguration.cs?name=snippet)]

Poprzedni kod ustawia poziom domyślny na `Warning` i kolor `Yellow` . Jeśli wartość `EventId` jest równa 0, będziemy rejestrować wszystkie zdarzenia.

### <a name="create-the-custom-logger"></a>Tworzenie rejestratora niestandardowego

`ILogger`Nazwa kategorii implementacji jest zwykle źródłem rejestrowania. Na przykład typ, w którym jest tworzony Rejestrator:

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLogger.cs?name=snippet)]

Powyższy kod ma następujące działanie:

* Tworzy wystąpienie rejestratora na nazwę kategorii.
* Sprawdza `logLevel == _config.LogLevel` `IsEnabled` , czy każdy z nich `logLevel` ma unikatowy rejestrator. Ogólnie rzecz biorąc, rejestratory powinny również być włączone dla wszystkich wyższych poziomów dzienników:

```csharp
public bool IsEnabled(LogLevel logLevel)
{
    return logLevel >= _config.LogLevel;
}
```

### <a name="create-the-custom-loggerprovider"></a>Tworzenie niestandardowego LoggerProvider

`LoggerProvider`Jest klasą, która tworzy wystąpienia rejestratora. Być może nie jest to konieczne do utworzenia wystąpienia rejestratora dla kategorii, ale jest to zrozumiałe dla niektórych rejestratorów, takich jak NLog lub log4net. W tym celu można również wybrać różne docelowe dane wyjściowe rejestrowania według kategorii, jeśli jest to konieczne:

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerProvider.cs?name=snippet)]

W powyższym kodzie <xref:Microsoft.Build.Logging.LoggerDescription.CreateLogger*> tworzy pojedyncze wystąpienie dla `ColorConsoleLogger` nazwy kategorii i zapisuje je w [`ConcurrentDictionary<TKey,TValue>`](/dotnet/api/system.collections.concurrent.concurrentdictionary-2) ;

### <a name="usage-and-registration-of-the-custom-logger"></a>Użycie i rejestracja rejestratora niestandardowego

Zarejestruj rejestrator w `Startup.Configure` :

[!code-csharp[](index/samples/3.x/CustomLogger/Startup.cs?name=snippet)]

Dla poprzedniego kodu, należy podać co najmniej jedną metodę rozszerzającą dla `ILoggerFactory` :

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerExtensions.cs?name=snippet)]

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:fundamentals/logging/loggermessage>
* Błędy rejestrowania należy utworzyć w repozytorium [GitHub.com/dotnet/Runtime/](https://github.com/dotnet/runtime/issues) .
* <xref:blazor/fundamentals/logging>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Autorzy [Dykstra](https://github.com/tdykstra) i [Steve Smith](https://ardalis.com/)

Platforma .NET Core obsługuje interfejs API rejestrowania, który współpracuje z różnymi dostawcami rejestrowania wbudowanych i innych firm. W tym artykule pokazano, jak używać interfejsu API rejestrowania z wbudowanymi dostawcami.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="add-providers"></a>Dodaj dostawców

Dostawca rejestrowania wyświetla lub przechowuje dzienniki. Na przykład dostawca konsoli wyświetla dzienniki w konsoli programu, a Dostawca usługi Azure Application Insights przechowuje je na platformie Azure Application Insights. Dzienniki mogą być wysyłane do wielu miejsc docelowych przez dodanie wielu dostawców.

Aby dodać dostawcę, wywołaj `Add{provider name}` metodę rozszerzenia dostawcy w *program.cs*:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

Poprzedzający kod wymaga odwołania do `Microsoft.Extensions.Logging` i `Microsoft.Extensions.Configuration` .

Domyślne wywołania szablonu projektu <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> , które dodaje następujących dostawców rejestrowania:

* Konsola
* Debugowanie
* EventSource (rozpoczęcie w ASP.NET Core 2,2)

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

Jeśli używasz programu `CreateDefaultBuilder` , możesz zastąpić domyślnych dostawców własnymi opcjami. Wywołaj <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> i Dodaj żądanych dostawców.

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

Więcej informacji na temat [wbudowanych dostawców rejestrowania](#built-in-logging-providers) i [dostawców rejestrowania innych](#third-party-logging-providers) firm znajduje się w dalszej części artykułu.

## <a name="create-logs"></a>Tworzenie dzienników

Aby utworzyć dzienniki, użyj <xref:Microsoft.Extensions.Logging.ILogger%601> obiektu. W aplikacji sieci Web lub hostowanej usłudze Pobierz `ILogger` od iniekcji zależności (di). W aplikacjach konsolowych innych niż host Użyj programu, `LoggerFactory` Aby utworzyć `ILogger` .

Poniższy ASP.NET Core przykład tworzy Rejestrator przy użyciu `TodoApiSample.Pages.AboutModel` jako kategorii. *Kategoria* dziennika jest ciągiem, który jest skojarzony z każdym dziennikiem. `ILogger<T>`Wystąpienie zapewniane przez program di tworzy dzienniki, które mają w pełni kwalifikowaną nazwę typu `T` jako kategorię. 

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

W poniższych przykładach ASP.NET Core i aplikacji konsoli Rejestrator służy do tworzenia dzienników z użyciem `Information` jako poziom. *Poziom* dziennika wskazuje ważność rejestrowanego zdarzenia.

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[Poziomy](#log-level) i [Kategorie](#log-category) zostały wyjaśnione bardziej szczegółowo w dalszej części tego artykułu.

### <a name="create-logs-in-startup"></a>Tworzenie dzienników w programie startowym

Aby napisać dzienniki w `Startup` klasie, Dołącz `ILogger` parametr do sygnatury konstruktora:

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a>Tworzenie dzienników w klasie programu

Aby napisać dzienniki w `Program` klasie, Pobierz `ILogger` wystąpienie z elementu di:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

Rejestrowanie podczas konstruowania hosta nie jest bezpośrednio obsługiwane. Można jednak użyć osobnego rejestratora. W poniższym przykładzie jest używany Rejestrator [Serilog](https://serilog.net/) do logowania `CreateWebHostBuilder` . `AddSerilog`używa konfiguracji statycznej określonej w `Log.Logger` :

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

Na przykład konfiguracja rejestrowania jest zwykle dostarczana przez `Logging` sekcję plików ustawień aplikacji. Poniższy przykład pokazuje zawartość typowego *appsettings.Development.jsw* pliku:

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

`Logging`Właściwość może mieć `LogLevel` właściwości dostawcy dzienników i.

`LogLevel`Właściwość w obszarze `Logging` określa minimalny [poziom](#log-level) rejestrowania wybranych kategorii. W przykładzie, `System` i `Microsoft` Kategorie są rejestrowane na `Information` poziomie, a wszystkie inne logowania na `Debug` poziomie.

Inne właściwości w obszarze `Logging` Określ dostawców rejestrowania. Przykład dotyczy dostawcy konsoli. Jeśli dostawca obsługuje [zakresy rejestrowania](#log-scopes), `IncludeScopes` wskazuje, czy są one włączone. Właściwość dostawcy (taka jak `Console` w przykładzie) może także określić `LogLevel` Właściwość. `LogLevel`w obszarze dostawca Określa poziomy do rejestrowania dla tego dostawcy.

Jeśli w programie są określone poziomy `Logging.{providername}.LogLevel` , zastępują one wszystko ustawione w `Logging.LogLevel` . Rozważmy na przykład następujące dane JSON:

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.MSFT.json)]

W powyższym kodzie JSON `Console` Ustawienia dostawcy przesłaniają poprzedni poziom dziennika (domyślnie).

Interfejs API rejestrowania nie zawiera scenariusza zmiany poziomów rejestrowania, gdy aplikacja jest uruchomiona. Niektórzy dostawcy konfiguracji mogą jednak ponownie ładować konfigurację, która zacznie natychmiastowo wpływać na konfigurację rejestrowania. Na przykład [dostawca konfiguracji plików](xref:fundamentals/configuration/index#file-configuration-provider), który jest dodawany przez program `CreateDefaultBuilder` do odczytu plików ustawień, ponownie ładuje konfigurację rejestrowania domyślnie. Jeśli konfiguracja zostanie zmieniona w kodzie w czasie, gdy aplikacja jest uruchomiona, aplikacja może wywołać [IConfigurationRoot. reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) , aby zaktualizować konfigurację rejestrowania aplikacji.

Informacje o implementowaniu dostawców konfiguracji znajdują się w temacie <xref:fundamentals/configuration/index> .

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

Poprzednie dzienniki zostały wygenerowane przez utworzenie żądania HTTP GET do przykładowej aplikacji w lokalizacji `http://localhost:5000/api/todo/0` .

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

`ILogger`Interfejsy i `ILoggerFactory` są w [Microsoft. Extensions. Logging. Abstracts](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/)i domyślne implementacje dla nich są w [Microsoft. Extensions. Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).

## <a name="log-category"></a>Kategoria dziennika

Po `ILogger` utworzeniu obiektu jest dla niego określona *Kategoria* . Ta kategoria jest dołączona do każdego komunikatu dziennika utworzonego przez to wystąpienie `ILogger` . Kategoria może być dowolnym ciągiem, ale Konwencja ma używać nazwy klasy, takiej jak "TodoApi. controllers. TodoController".

Użyj, `ILogger<T>` Aby uzyskać `ILogger` wystąpienie używające w pełni kwalifikowanej nazwy typu `T` jako kategorii:

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

Aby jawnie określić kategorię, wywołaj `ILoggerFactory.CreateLogger` :

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

`ILogger<T>`jest odpowiednikiem wywołania `CreateLogger` z w pełni kwalifikowaną nazwą typu `T` .

## <a name="log-level"></a>Poziom dziennika

Każdy dziennik Określa <xref:Microsoft.Extensions.Logging.LogLevel> wartość. Poziom dziennika wskazuje ważność lub ważność. Przykładowo można napisać `Information` Dziennik, gdy metoda jest zwykle zakończona, a dziennik, `Warning` gdy metoda zwraca *404, nie znaleziono* kodu stanu.

Poniższy kod tworzy `Information` i `Warning` rejestruje:

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

W poprzednim kodzie `MyLogEvents.GetItem` `MyLogEvents.GetItemNotFound` Parametry i są [identyfikatorem zdarzenia dziennika](#log-event-id). Drugi parametr jest szablonem wiadomości z symbolami zastępczymi dla wartości argumentów dostarczonych przez pozostałe parametry metody. Parametry metody zostały wyjaśnione w [sekcji szablon komunikatu dziennika](#lmt) w tym artykule.

Metody rejestrowania, które obejmują poziom w nazwie metody (na przykład `LogInformation` i `LogWarning` ) są [metodami rozszerzającymi dla ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions). Te metody wywołują `Log` metodę, która pobiera `LogLevel` parametr. Metodę można wywołać `Log` bezpośrednio zamiast jednej z tych metod rozszerzających, ale składnia jest stosunkowo skomplikowana. Aby uzyskać więcej informacji, zobacz <xref:Microsoft.Extensions.Logging.ILogger> i [kod źródłowy rozszerzeń rejestratora](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).

ASP.NET Core definiuje następujące poziomy dziennika uporządkowane w tym miejscu od najniższej do najwyższej wagi.

* Ślad = 0

  Aby uzyskać informacje, które są zazwyczaj cenne tylko dla debugowania. Komunikaty te mogą zawierać poufne dane aplikacji, dlatego nie powinny być włączone w środowisku produkcyjnym. *Domyślnie wyłączona.*

* Debuguj = 1

  Informacje, które mogą być przydatne podczas tworzenia i debugowania. Przykład: `Entering method Configure with flag set to true.` Włącz `Debug` dzienniki poziomów w środowisku produkcyjnym tylko w przypadku rozwiązywania problemów, ze względu na dużą ilość dzienników.

* Informacje = 2

  Do śledzenia ogólnego przepływu aplikacji. Te dzienniki zwykle mają pewną wartość długoterminową. Przykład: `Request received for path /api/todo`

* Ostrzeżenie = 3

  Dla nietypowych lub nieoczekiwanych zdarzeń w przepływie aplikacji. Mogą to być błędy lub inne warunki, które nie powodują zatrzymania aplikacji, ale konieczne może być zbadanie. Obsłużone wyjątki są typowym miejscem do korzystania z `Warning` poziomu dziennika. Przykład: `FileNotFoundException for file quotes.txt.`

* Błąd = 4

  W przypadku błędów i wyjątków, których nie można obsłużyć. Te komunikaty wskazują niepowodzenie w bieżącym działaniu lub operacji (np. bieżące żądanie HTTP), a nie awaria całej aplikacji. Przykładowy komunikat dziennika:`Cannot insert record due to duplicate key violation.`

* Krytyczne = 5

  Dla niepowodzeń, które wymagają natychmiastowej uwagi. Przykłady: scenariusze utraty danych, brak miejsca na dysku.

Poziom dziennika służy do kontrolowania, ile danych wyjściowych dziennika jest zapisywana w określonym nośniku lub oknie wyświetlania. Na przykład:

* W środowisku produkcyjnym:
  * Rejestrowanie na poziomie `Trace` za pomocą `Information` poziomów zapewnia wysoką liczbę szczegółowych komunikatów dziennika. Aby kontrolować koszty i nie przekraczać limitów magazynowania danych, należy rejestrować `Trace` `Information` komunikaty na poziomie w magazynach o wysokim poziomie ilości danych.
  * Rejestrowanie na `Warning` poziomie `Critical` poziomów zwykle generuje mniejszą liczbę mniejszych komunikatów dzienników. W związku z tym koszty i limity magazynu zazwyczaj nie są problemem, co skutkuje większą elastycznością wyboru magazynu danych.
* Podczas tworzenia:
  * Rejestruj `Warning` `Critical` komunikaty w konsoli programu.
  * Dodawanie `Trace` `Information` komunikatów podczas rozwiązywania problemów.

W sekcji [filtrowanie dzienników](#log-filtering) w dalszej części tego artykułu wyjaśniono, jak kontrolować poziomy dzienników obsługiwane przez dostawcę.

ASP.NET Core zapisuje dzienniki dla zdarzeń struktury. Przykłady dzienników znajdujące się wcześniej w tym artykule nie wykluczają dzienników poniżej `Information` , dlatego nie `Debug` `Trace` zostały utworzone dzienniki. Oto przykład dzienników konsoli utworzonych przez uruchomienie przykładowej aplikacji skonfigurowanej do wyświetlania `Debug` dzienników:

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

Kod szablonu projektu wywołuje `CreateDefaultBuilder` w celu skonfigurowania rejestrowania dla dostawców konsoli, debugowania i EventSource (ASP.NET Core 2,2 lub nowszych). `CreateDefaultBuilder`Metoda konfiguruje rejestrowanie, aby wyszukać konfigurację w `Logging` sekcji, jak wyjaśniono [wcześniej w tym artykule](#configuration).

Dane konfiguracyjne określają minimalne poziomy dziennika według dostawcy i kategorii, jak w poniższym przykładzie:

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

Ten kod JSON tworzy sześć reguł filtrowania: jeden dla dostawcy debugowania, cztery dla dostawcy konsoli i jeden dla wszystkich dostawców. Dla każdego dostawcy wybierana jest pojedyncza reguła, gdy `ILogger` tworzony jest obiekt.

### <a name="filter-rules-in-code"></a>Filtrowanie reguł w kodzie

Poniższy przykład pokazuje, jak zarejestrować reguły filtru w kodzie:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

Drugi `AddFilter` określa dostawcę debugowania za pomocą nazwy typu. Pierwszy `AddFilter` ma zastosowanie do wszystkich dostawców, ponieważ nie określa typu dostawcy.

### <a name="how-filtering-rules-are-applied"></a>Jak są stosowane reguły filtrowania

Dane konfiguracji i `AddFilter` kod przedstawiony w powyższych przykładach tworzą reguły pokazane w poniższej tabeli. Pierwsze sześć pochodzi z przykładu konfiguracji, a ostatnie dwa pochodzą z przykładu kodu.

| Liczba | Dostawca      | Kategorie zaczynające się od...          | Minimalny poziom rejestrowania |
| :----: | ------------- | --------------------------------------- | ----------------- |
| 1      | Debugowanie         | Wszystkie kategorie                          | Informacje       |
| 2      | Konsola       | Microsoft. AspNetCore. MVC. Razor .. Wewnętrz | Ostrzeżenie           |
| 3      | Konsola       | Microsoft. AspNetCore. MVC. Razor ..Razor    | Debugowanie             |
| 4      | Konsola       | Microsoft. AspNetCore. MVC.Razor          | Błąd             |
| 5      | Konsola       | Wszystkie kategorie                          | Informacje       |
| 6      | Wszyscy dostawcy | Wszystkie kategorie                          | Debugowanie             |
| 7      | Wszyscy dostawcy | System                                  | Debugowanie             |
| 8      | Debugowanie         | Microsoft                               | Ślad             |

Po `ILogger` utworzeniu obiektu `ILoggerFactory` obiekt wybiera jedną regułę dla każdego dostawcy, która ma zostać zastosowana do tego rejestratora. Wszystkie komunikaty zapisywane przez `ILogger` wystąpienie są filtrowane na podstawie wybranych reguł. Najbardziej konkretną regułą można wybrać dla każdego dostawcy i pary kategorii z dostępnych reguł.

Następujący algorytm jest używany dla każdego dostawcy, gdy `ILogger` jest tworzony dla danej kategorii:

* Wybierz wszystkie reguły, które pasują do dostawcy lub jego aliasu. Jeśli nie zostanie znalezione dopasowanie, zaznacz wszystkie reguły z pustym dostawcą.
* W wyniku poprzedniego kroku wybierz pozycję reguły z najdłuższym prefiksem kategorii. Jeśli nie zostanie znalezione dopasowanie, zaznacz wszystkie reguły, które nie określają kategorii.
* Jeśli wybrano wiele reguł, zrób to **ostatnie** .
* Jeśli nie wybrano żadnych reguł, użyj `MinimumLevel` .

Na powyższej liście reguł Załóżmy, że tworzysz `ILogger` obiekt dla kategorii "Microsoft. AspNetCore. MVC. Razor . Razor ViewEngine":

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

Jeśli poziom minimalny nie został jawnie ustawiony, wartość domyślna to `Information` , co oznacza, że `Trace` dzienniki i `Debug` są ignorowane.

### <a name="filter-functions"></a>Funkcje filtrowania

Funkcja filtru jest wywoływana dla wszystkich dostawców i kategorii, które nie mają przypisanych do nich reguł przez konfigurację lub kod. Kod w funkcji ma dostęp do typu dostawcy, kategorii i poziomu dziennika. Na przykład:

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
> Konfigurowanie `IncludeScopes` opcji rejestratora konsoli jest wymagane do włączenia rejestrowania na podstawie zakresu.
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
* [Debugowanie](#debug-provider)
* [EventSource](#event-source-provider)
* [Elemencie](#windows-eventlog-provider)
* [TraceSource](#tracesource-provider)
* [AzureAppServicesFile](#azure-app-service-provider)
* [AzureAppServicesBlob](#azure-app-service-provider)
* [ApplicationInsights](#azure-application-insights-trace-logging)

Aby uzyskać informacje na temat strumienia stdout i rejestrowania debugowania za pomocą modułu ASP.NET Core, zobacz <xref:test/troubleshoot-azure-iis> i <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection> .

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

Pakiet [Microsoft. Extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) Provider zapisuje dane wyjściowe dziennika przy użyciu klasy [System. Diagnostics. Debug](/dotnet/api/system.diagnostics.debug) ( `Debug.WriteLine` wywołania metod).

W systemie Linux ten dostawca zapisuje dzienniki do */var/log/Message*.

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a>Dostawca źródła zdarzeń

Pakiet dostawcy [Microsoft. Extensions. Logging. EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) zapisuje na międzyplatformę źródła zdarzeń, używając nazwy `Microsoft-Extensions-Logging` . W systemie Windows Dostawca używa [funkcji ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).

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

[Przeciążenia addeventlog](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) umożliwiają przekazywanie <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings> . Jeśli `null` lub nie zostanie określony, są używane następujące ustawienia domyślne:

* `LogName`: "Aplikacja"
* `SourceName`: "Środowisko uruchomieniowe platformy .NET"
* `MachineName`: Nazwa komputera lokalnego jest używana.

Zdarzenia są rejestrowane dla [poziomu ostrzeżeń i wyższych](#log-level). Poniższy przykład ustawia domyślny poziom dziennika dziennika zdarzeń <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType> :

```json
"Logging": {
  "EventLog": {
    "LogLevel": {
      "Default": "Information"
    }
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

Pakiet dostawcy nie jest uwzględniony w [pakiecie Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app). Podczas określania elementu docelowego .NET Framework lub odwoływania się do niego `Microsoft.AspNetCore.App` , Dodaj pakiet dostawcy do projektu. 

<xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*>Przeciążenie umożliwia przekazywanie <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings> . Obiekt Settings może przesłonić ustawienia domyślne, takie jak szablon danych wyjściowych rejestrowania, nazwa obiektu BLOB i limit rozmiaru pliku. (*Szablon danych wyjściowych* to szablon wiadomości, który jest stosowany do wszystkich dzienników oprócz tego, co jest dostępne w `ILogger` wywołaniu metody).

Po wdrożeniu w aplikacji App Service, aplikacja będzie przestrzegać ustawień w sekcji [dzienniki App Service](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) na stronie **App Service** Azure Portal. Po zaktualizowaniu następujących ustawień zmiany zaczynają obowiązywać natychmiast, bez konieczności ponownego uruchomienia lub ponownej wdrożenia aplikacji.

* **Rejestrowanie aplikacji (system plików)**
* **Rejestrowanie aplikacji (BLOB)**

Domyślną lokalizacją plików dziennika jest folder *D: \\ Home \\ \\ LogFiles* , a domyślna nazwa pliku to *diagnostics-yyyymmdd.txt*. Domyślny limit rozmiaru pliku wynosi 10 MB, a domyślna maksymalna liczba zachowywanych plików to 2. Domyślną nazwą obiektu BLOB jest *{App-Name} {timestamp}/yyyy/mm/dd/hh/{guid} -applicationLog.txt*.

Dostawca działa tylko wtedy, gdy projekt jest uruchomiony w środowisku platformy Azure. Nie ma ono wpływu, gdy projekt jest uruchamiany lokalnie, &mdash; nie zapisuje w plikach lokalnych ani w lokalnym magazynie programistycznym dla obiektów BLOB.

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

Nie używaj pakietu [Microsoft. ApplicationInsights. Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) &mdash; , który jest przeznaczony dla ASP.NET 4. x.

Więcej informacji można znaleźć w następujących zasobach:

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

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:fundamentals/logging/loggermessage>

::: moniker-end
