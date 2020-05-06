---
title: Rejestrowanie o wysokiej wydajności za pomocą LoggerMessage w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak używać LoggerMessage do tworzenia delegatów z pamięcią podręczną, które wymagają mniejszej liczby alokacji obiektów do scenariuszy rejestrowania o wysokiej wydajności.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/logging/loggermessage
ms.openlocfilehash: 67281b99f1ed8955ee29eb68b446d71a0c5c7838
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768367"
---
# <a name="high-performance-logging-with-loggermessage-in-aspnet-core"></a><span data-ttu-id="a44e6-103">Rejestrowanie o wysokiej wydajności za pomocą LoggerMessage w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a44e6-103">High-performance logging with LoggerMessage in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a44e6-104"><xref:Microsoft.Extensions.Logging.LoggerMessage>funkcje tworzą delegatów z pamięcią podręczną, którzy wymagają mniejszej liczby alokacji obiektów i zmniejszonego obciążenia obliczeniowego w <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> porównaniu <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>z [metodami rozszerzenia rejestratora](xref:Microsoft.Extensions.Logging.LoggerExtensions), takimi jak i.</span><span class="sxs-lookup"><span data-stu-id="a44e6-104"><xref:Microsoft.Extensions.Logging.LoggerMessage> features create cacheable delegates that require fewer object allocations and reduced computational overhead compared to [logger extension methods](xref:Microsoft.Extensions.Logging.LoggerExtensions), such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>.</span></span> <span data-ttu-id="a44e6-105">W przypadku scenariuszy rejestrowania o wysokiej wydajności Użyj <xref:Microsoft.Extensions.Logging.LoggerMessage> wzorca.</span><span class="sxs-lookup"><span data-stu-id="a44e6-105">For high-performance logging scenarios, use the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

<span data-ttu-id="a44e6-106"><xref:Microsoft.Extensions.Logging.LoggerMessage>zapewnia następujące korzyści wynikające z wydajności w porównaniu z metodami rozszerzenia rejestratora:</span><span class="sxs-lookup"><span data-stu-id="a44e6-106"><xref:Microsoft.Extensions.Logging.LoggerMessage> provides the following performance advantages over Logger extension methods:</span></span>

* <span data-ttu-id="a44e6-107">Metody rozszerzenia rejestratora wymagają "opakowania" (do konwersji) typów wartości, `int`takich jak `object`, do.</span><span class="sxs-lookup"><span data-stu-id="a44e6-107">Logger extension methods require "boxing" (converting) value types, such as `int`, into `object`.</span></span> <span data-ttu-id="a44e6-108"><xref:Microsoft.Extensions.Logging.LoggerMessage> Wzorzec unika pakowania przy użyciu pól statycznych <xref:System.Action> i metod rozszerzających z parametrami o jednoznacznie określonym typie.</span><span class="sxs-lookup"><span data-stu-id="a44e6-108">The <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern avoids boxing by using static <xref:System.Action> fields and extension methods with strongly-typed parameters.</span></span>
* <span data-ttu-id="a44e6-109">Metody rozszerzenia rejestratora muszą analizować szablon wiadomości (nazwanego ciągu formatu) za każdym razem, gdy zostanie zapisany komunikat dziennika.</span><span class="sxs-lookup"><span data-stu-id="a44e6-109">Logger extension methods must parse the message template (named format string) every time a log message is written.</span></span> <span data-ttu-id="a44e6-110"><xref:Microsoft.Extensions.Logging.LoggerMessage>tylko wymaga analizy szablonu tylko raz, gdy komunikat jest zdefiniowany.</span><span class="sxs-lookup"><span data-stu-id="a44e6-110"><xref:Microsoft.Extensions.Logging.LoggerMessage> only requires parsing a template once when the message is defined.</span></span>

<span data-ttu-id="a44e6-111">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a44e6-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="a44e6-112">Przykładowa aplikacja pokazuje <xref:Microsoft.Extensions.Logging.LoggerMessage> funkcje z podstawowym systemem śledzenia ofert.</span><span class="sxs-lookup"><span data-stu-id="a44e6-112">The sample app demonstrates <xref:Microsoft.Extensions.Logging.LoggerMessage> features with a basic quote tracking system.</span></span> <span data-ttu-id="a44e6-113">Aplikacja dodaje i usuwa oferty przy użyciu bazy danych w pamięci.</span><span class="sxs-lookup"><span data-stu-id="a44e6-113">The app adds and deletes quotes using an in-memory database.</span></span> <span data-ttu-id="a44e6-114">W przypadku wystąpienia tych operacji komunikaty dziennika są generowane przy użyciu <xref:Microsoft.Extensions.Logging.LoggerMessage> wzorca.</span><span class="sxs-lookup"><span data-stu-id="a44e6-114">As these operations occur, log messages are generated using the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

## <a name="loggermessagedefine"></a><span data-ttu-id="a44e6-115">LoggerMessage. define</span><span class="sxs-lookup"><span data-stu-id="a44e6-115">LoggerMessage.Define</span></span>

<span data-ttu-id="a44e6-116">[Zdefiniuj (LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) tworzy <xref:System.Action> delegata do rejestrowania wiadomości.</span><span class="sxs-lookup"><span data-stu-id="a44e6-116">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) creates an <xref:System.Action> delegate for logging a message.</span></span> <span data-ttu-id="a44e6-117"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*>przeciążenia dopuszczają przekazywanie do sześciu parametrów typu do nazwanego ciągu formatu (szablonu).</span><span class="sxs-lookup"><span data-stu-id="a44e6-117"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> overloads permit passing up to six type parameters to a named format string (template).</span></span>

<span data-ttu-id="a44e6-118">Ciąg dostarczony do <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> metody jest szablonem, a nie ciągiem interpolowanym.</span><span class="sxs-lookup"><span data-stu-id="a44e6-118">The string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="a44e6-119">Symbole zastępcze są wypełniane w kolejności, w jakiej są określone typy.</span><span class="sxs-lookup"><span data-stu-id="a44e6-119">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="a44e6-120">Nazwy symboli zastępczych w szablonie powinny być opisowe i spójne w szablonach.</span><span class="sxs-lookup"><span data-stu-id="a44e6-120">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="a44e6-121">Służą one jako nazwy właściwości w danych dziennika strukturalnego.</span><span class="sxs-lookup"><span data-stu-id="a44e6-121">They serve as property names within structured log data.</span></span> <span data-ttu-id="a44e6-122">Zalecamy używanie [wielkości liter](/dotnet/standard/design-guidelines/capitalization-conventions) w języku Pascal dla nazw zastępczych.</span><span class="sxs-lookup"><span data-stu-id="a44e6-122">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="a44e6-123">Na przykład `{Count}`, `{FirstName}`.</span><span class="sxs-lookup"><span data-stu-id="a44e6-123">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="a44e6-124">Każdy komunikat dziennika jest <xref:System.Action> przechowywany w polu statycznym utworzonym przez [LoggerMessage. define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span><span class="sxs-lookup"><span data-stu-id="a44e6-124">Each log message is an <xref:System.Action> held in a static field created by [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span></span> <span data-ttu-id="a44e6-125">Przykładowo Przykładowa aplikacja tworzy pole opisujące komunikat dziennika dla żądania GET dla strony indeksu (*Internal/LoggerExtensions. cs*):</span><span class="sxs-lookup"><span data-stu-id="a44e6-125">For example, the sample app creates a field to describe a log message for a GET request for the Index page (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet1)]

<span data-ttu-id="a44e6-126">Dla <xref:System.Action>, określ:</span><span class="sxs-lookup"><span data-stu-id="a44e6-126">For the <xref:System.Action>, specify:</span></span>

* <span data-ttu-id="a44e6-127">Poziom dziennika.</span><span class="sxs-lookup"><span data-stu-id="a44e6-127">The log level.</span></span>
* <span data-ttu-id="a44e6-128">Unikatowy identyfikator zdarzenia (<xref:Microsoft.Extensions.Logging.EventId>) z nazwą statycznej metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="a44e6-128">A unique event identifier (<xref:Microsoft.Extensions.Logging.EventId>) with the name of the static extension method.</span></span>
* <span data-ttu-id="a44e6-129">Szablon wiadomości (nazwany ciąg formatu).</span><span class="sxs-lookup"><span data-stu-id="a44e6-129">The message template (named format string).</span></span> 

<span data-ttu-id="a44e6-130">Żądanie dotyczące strony indeksu przykładowej aplikacji ustawia:</span><span class="sxs-lookup"><span data-stu-id="a44e6-130">A request for the Index page of the sample app sets the:</span></span>

* <span data-ttu-id="a44e6-131">Poziom rejestrowania do `Information`.</span><span class="sxs-lookup"><span data-stu-id="a44e6-131">Log level to `Information`.</span></span>
* <span data-ttu-id="a44e6-132">Identyfikator zdarzenia do `1` nazwy `IndexPageRequested` metody.</span><span class="sxs-lookup"><span data-stu-id="a44e6-132">Event id to `1` with the name of the `IndexPageRequested` method.</span></span>
* <span data-ttu-id="a44e6-133">Szablon wiadomości (nazwany ciąg formatu) do ciągu.</span><span class="sxs-lookup"><span data-stu-id="a44e6-133">Message template (named format string) to a string.</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet5)]

<span data-ttu-id="a44e6-134">Magazyny rejestrowania strukturalnego mogą używać nazwy zdarzenia, gdy jest ona dostarczana z identyfikatorem zdarzenia w celu wzbogacania rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="a44e6-134">Structured logging stores may use the event name when it's supplied with the event id to enrich logging.</span></span> <span data-ttu-id="a44e6-135">Na przykład [Serilog](https://github.com/serilog/serilog-extensions-logging) używa nazwy zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="a44e6-135">For example, [Serilog](https://github.com/serilog/serilog-extensions-logging) uses the event name.</span></span>

<span data-ttu-id="a44e6-136"><xref:System.Action> Jest wywoływany za pomocą metody rozszerzającej o jednoznacznie określonym typie.</span><span class="sxs-lookup"><span data-stu-id="a44e6-136">The <xref:System.Action> is invoked through a strongly-typed extension method.</span></span> <span data-ttu-id="a44e6-137">`IndexPageRequested` Metoda rejestruje komunikat dla strony indeksu żądania GET w przykładowej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="a44e6-137">The `IndexPageRequested` method logs a message for an Index page GET request in the sample app:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet9)]

<span data-ttu-id="a44e6-138">`IndexPageRequested`jest wywoływana dla rejestratora w `OnGetAsync` metodzie w *Pages/index. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="a44e6-138">`IndexPageRequested` is called on the logger in the `OnGetAsync` method in *Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

<span data-ttu-id="a44e6-139">Sprawdź dane wyjściowe konsoli aplikacji:</span><span class="sxs-lookup"><span data-stu-id="a44e6-139">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

<span data-ttu-id="a44e6-140">Aby przekazać parametry do komunikatu dziennika, zdefiniuj maksymalnie sześć typów podczas tworzenia pola statycznego.</span><span class="sxs-lookup"><span data-stu-id="a44e6-140">To pass parameters to a log message, define up to six types when creating the static field.</span></span> <span data-ttu-id="a44e6-141">Przykładowa aplikacja rejestruje ciąg podczas dodawania oferty przez zdefiniowanie `string` typu dla <xref:System.Action> pola:</span><span class="sxs-lookup"><span data-stu-id="a44e6-141">The sample app logs a string when adding a quote by defining a `string` type for the <xref:System.Action> field:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet2)]

<span data-ttu-id="a44e6-142">Szablon komunikatu dziennika delegata otrzymuje wartości zastępcze z dostarczonych typów.</span><span class="sxs-lookup"><span data-stu-id="a44e6-142">The delegate's log message template receives its placeholder values from the types provided.</span></span> <span data-ttu-id="a44e6-143">Przykładowa aplikacja definiuje delegata do dodawania oferty, w której parametr Quote jest `string`:</span><span class="sxs-lookup"><span data-stu-id="a44e6-143">The sample app defines a delegate for adding a quote where the quote parameter is a `string`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet6)]

<span data-ttu-id="a44e6-144">Statyczna metoda rozszerzenia służąca do dodawania oferty `QuoteAdded`,, odbiera wartość argumentu cudzysłowu i przekazuje ją do <xref:System.Action> delegata:</span><span class="sxs-lookup"><span data-stu-id="a44e6-144">The static extension method for adding a quote, `QuoteAdded`, receives the quote argument value and passes it to the <xref:System.Action> delegate:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet10)]

<span data-ttu-id="a44e6-145">W modelu strony strony indeksu (*Pages/index. cshtml. cs*) `QuoteAdded` jest wywoływana w celu zarejestrowania komunikatu:</span><span class="sxs-lookup"><span data-stu-id="a44e6-145">In the Index page's page model (*Pages/Index.cshtml.cs*), `QuoteAdded` is called to log the message:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

<span data-ttu-id="a44e6-146">Sprawdź dane wyjściowe konsoli aplikacji:</span><span class="sxs-lookup"><span data-stu-id="a44e6-146">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand')
```

<span data-ttu-id="a44e6-147">Przykładowa aplikacja implementuje wzór [try&ndash;catch](/dotnet/csharp/language-reference/keywords/try-catch) dla usunięcia oferty.</span><span class="sxs-lookup"><span data-stu-id="a44e6-147">The sample app implements a [try&ndash;catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern for quote deletion.</span></span> <span data-ttu-id="a44e6-148">Komunikat informacyjny jest rejestrowany w przypadku pomyślnego usunięcia operacji usuwania.</span><span class="sxs-lookup"><span data-stu-id="a44e6-148">An informational message is logged for a successful delete operation.</span></span> <span data-ttu-id="a44e6-149">Komunikat o błędzie jest rejestrowany dla operacji usuwania, gdy zostanie zgłoszony wyjątek.</span><span class="sxs-lookup"><span data-stu-id="a44e6-149">An error message is logged for a delete operation when an exception is thrown.</span></span> <span data-ttu-id="a44e6-150">Komunikat dziennika dla nieprawidłowej operacji usuwania obejmuje ślad stosu wyjątku (*wewnętrzny/LoggerExtensions. cs*):</span><span class="sxs-lookup"><span data-stu-id="a44e6-150">The log message for the unsuccessful delete operation includes the exception stack trace (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet7)]

<span data-ttu-id="a44e6-151">Zwróć uwagę, jak wyjątek jest przesyłany do delegata w `QuoteDeleteFailed`:</span><span class="sxs-lookup"><span data-stu-id="a44e6-151">Note how the exception is passed to the delegate in `QuoteDeleteFailed`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet11)]

<span data-ttu-id="a44e6-152">W modelu strony dla strony indeksu pomyślne usunięcie cudzysłowu wywołuje `QuoteDeleted` metodę w rejestratorze.</span><span class="sxs-lookup"><span data-stu-id="a44e6-152">In the page model for the Index page, a successful quote deletion calls the `QuoteDeleted` method on the logger.</span></span> <span data-ttu-id="a44e6-153">Gdy cytat nie zostanie znaleziony do usunięcia, <xref:System.ArgumentNullException> jest zgłaszany.</span><span class="sxs-lookup"><span data-stu-id="a44e6-153">When a quote isn't found for deletion, an <xref:System.ArgumentNullException> is thrown.</span></span> <span data-ttu-id="a44e6-154">Wyjątek jest zalewkowany przez instrukcję [try&ndash;catch](/dotnet/csharp/language-reference/keywords/try-catch) i rejestrowany przez wywołanie `QuoteDeleteFailed` metody w rejestratorze w bloku [catch](/dotnet/csharp/language-reference/keywords/try-catch) (*Pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="a44e6-154">The exception is trapped by the [try&ndash;catch](/dotnet/csharp/language-reference/keywords/try-catch) statement and logged by calling the `QuoteDeleteFailed` method on the logger in the [catch](/dotnet/csharp/language-reference/keywords/try-catch) block (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet5&highlight=9,13)]

<span data-ttu-id="a44e6-155">Po pomyślnym usunięciu oferty Sprawdź dane wyjściowe konsoli aplikacji:</span><span class="sxs-lookup"><span data-stu-id="a44e6-155">When a quote is successfully deleted, inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand' Id = 1)
```

<span data-ttu-id="a44e6-156">Po niepowodzeniu usuwania oferty Sprawdź dane wyjściowe konsoli aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a44e6-156">When quote deletion fails, inspect the app's console output.</span></span> <span data-ttu-id="a44e6-157">Należy zauważyć, że wyjątek jest zawarty w komunikacie dziennika:</span><span class="sxs-lookup"><span data-stu-id="a44e6-157">Note that the exception is included in the log message:</span></span>

```console
LoggerMessageSample.Pages.IndexModel: Error: Quote delete failed (Id = 999)

System.NullReferenceException: Object reference not set to an instance of an object.
   at lambda_method(Closure , ValueBuffer )
   at System.Linq.Enumerable.SelectEnumerableIterator`2.MoveNext()
   at Microsoft.EntityFrameworkCore.InMemory.Query.Internal.InMemoryShapedQueryCompilingExpressionVisitor.AsyncQueryingEnumerable`1.AsyncEnumerator.MoveNextAsync()
   at Microsoft.EntityFrameworkCore.Query.ShapedQueryCompilingExpressionVisitor.SingleOrDefaultAsync[TSource](IAsyncEnumerable`1 asyncEnumerable, CancellationToken cancellationToken)
   at Microsoft.EntityFrameworkCore.Query.ShapedQueryCompilingExpressionVisitor.SingleOrDefaultAsync[TSource](IAsyncEnumerable`1 asyncEnumerable, CancellationToken cancellationToken)
   at LoggerMessageSample.Pages.IndexModel.OnPostDeleteQuoteAsync(Int32 id) in c:\Users\guard\Documents\GitHub\Docs\aspnetcore\fundamentals\logging\loggermessage\samples\3.x\LoggerMessageSample\Pages\Index.cshtml.cs:line 77
```

## <a name="loggermessagedefinescope"></a><span data-ttu-id="a44e6-158">LoggerMessage. DefineScope —</span><span class="sxs-lookup"><span data-stu-id="a44e6-158">LoggerMessage.DefineScope</span></span>

<span data-ttu-id="a44e6-159">[DefineScope — (ciąg)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) tworzy <xref:System.Func%601> delegata do definiowania [zakresu dziennika](xref:fundamentals/logging/index#log-scopes).</span><span class="sxs-lookup"><span data-stu-id="a44e6-159">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) creates a <xref:System.Func%601> delegate for defining a [log scope](xref:fundamentals/logging/index#log-scopes).</span></span> <span data-ttu-id="a44e6-160"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*>przeciążenia dopuszczają przekazywanie do trzech parametrów typu do nazwanego ciągu formatu (szablonu).</span><span class="sxs-lookup"><span data-stu-id="a44e6-160"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> overloads permit passing up to three type parameters to a named format string (template).</span></span>

<span data-ttu-id="a44e6-161">Podobnie jak w przypadku <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> metody, ciąg dostarczony do <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> metody jest szablonem, a nie ciągiem interpolowanym.</span><span class="sxs-lookup"><span data-stu-id="a44e6-161">As is the case with the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method, the string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="a44e6-162">Symbole zastępcze są wypełniane w kolejności, w jakiej są określone typy.</span><span class="sxs-lookup"><span data-stu-id="a44e6-162">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="a44e6-163">Nazwy symboli zastępczych w szablonie powinny być opisowe i spójne w szablonach.</span><span class="sxs-lookup"><span data-stu-id="a44e6-163">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="a44e6-164">Służą one jako nazwy właściwości w danych dziennika strukturalnego.</span><span class="sxs-lookup"><span data-stu-id="a44e6-164">They serve as property names within structured log data.</span></span> <span data-ttu-id="a44e6-165">Zalecamy używanie [wielkości liter](/dotnet/standard/design-guidelines/capitalization-conventions) w języku Pascal dla nazw zastępczych.</span><span class="sxs-lookup"><span data-stu-id="a44e6-165">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="a44e6-166">Na przykład `{Count}`, `{FirstName}`.</span><span class="sxs-lookup"><span data-stu-id="a44e6-166">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="a44e6-167">Zdefiniuj [zakres dziennika](xref:fundamentals/logging/index#log-scopes) , który ma zostać zastosowany do serii komunikatów dziennika przy <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> użyciu metody.</span><span class="sxs-lookup"><span data-stu-id="a44e6-167">Define a [log scope](xref:fundamentals/logging/index#log-scopes) to apply to a series of log messages using the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method.</span></span>

<span data-ttu-id="a44e6-168">Przykładowa aplikacja ma przycisk **Wyczyść wszystko** , aby usunąć wszystkie cudzysłowy w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="a44e6-168">The sample app has a **Clear All** button for deleting all of the quotes in the database.</span></span> <span data-ttu-id="a44e6-169">Cudzysłowy są usuwane, usuwając je pojedynczo.</span><span class="sxs-lookup"><span data-stu-id="a44e6-169">The quotes are deleted by removing them one at a time.</span></span> <span data-ttu-id="a44e6-170">Przy każdym usunięciu oferty `QuoteDeleted` Metoda jest wywoływana w rejestratorze.</span><span class="sxs-lookup"><span data-stu-id="a44e6-170">Each time a quote is deleted, the `QuoteDeleted` method is called on the logger.</span></span> <span data-ttu-id="a44e6-171">Do tych komunikatów dziennika jest dodawany zakres dziennika.</span><span class="sxs-lookup"><span data-stu-id="a44e6-171">A log scope is added to these log messages.</span></span>

<span data-ttu-id="a44e6-172">Włącz `IncludeScopes` w sekcji rejestratora konsoli pliku *appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="a44e6-172">Enable `IncludeScopes` in the console logger section of *appsettings.json*:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/appsettings.json?highlight=3-5)]

<span data-ttu-id="a44e6-173">Aby utworzyć zakres dziennika, należy dodać pole do przechowywania <xref:System.Func%601> delegata dla zakresu.</span><span class="sxs-lookup"><span data-stu-id="a44e6-173">To create a log scope, add a field to hold a <xref:System.Func%601> delegate for the scope.</span></span> <span data-ttu-id="a44e6-174">Przykładowa aplikacja tworzy pole o nazwie `_allQuotesDeletedScope` (*wewnętrzne/LoggerExtensions. cs*):</span><span class="sxs-lookup"><span data-stu-id="a44e6-174">The sample app creates a field called `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet4)]

<span data-ttu-id="a44e6-175">Użyj <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> , aby utworzyć delegata.</span><span class="sxs-lookup"><span data-stu-id="a44e6-175">Use <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> to create the delegate.</span></span> <span data-ttu-id="a44e6-176">Można określić maksymalnie trzy typy do użycia jako argumenty szablonu, gdy obiekt delegowany jest wywoływany.</span><span class="sxs-lookup"><span data-stu-id="a44e6-176">Up to three types can be specified for use as template arguments when the delegate is invoked.</span></span> <span data-ttu-id="a44e6-177">Przykładowa aplikacja używa szablonu komunikatu zawierającego liczbę usuniętych cudzysłowów ( `int` typ):</span><span class="sxs-lookup"><span data-stu-id="a44e6-177">The sample app uses a message template that includes the number of deleted quotes (an `int` type):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet8)]

<span data-ttu-id="a44e6-178">Podaj statyczną metodę rozszerzenia dla komunikatu dziennika.</span><span class="sxs-lookup"><span data-stu-id="a44e6-178">Provide a static extension method for the log message.</span></span> <span data-ttu-id="a44e6-179">Dołącz wszystkie parametry typu dla nazwanych właściwości, które są wyświetlane w szablonie wiadomości.</span><span class="sxs-lookup"><span data-stu-id="a44e6-179">Include any type parameters for named properties that appear in the message template.</span></span> <span data-ttu-id="a44e6-180">Przykładowa aplikacja wykonuje `count` cudzysłowy, aby usunąć i zwracać `_allQuotesDeletedScope`:</span><span class="sxs-lookup"><span data-stu-id="a44e6-180">The sample app takes in a `count` of quotes to delete and returns `_allQuotesDeletedScope`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet12)]

<span data-ttu-id="a44e6-181">Zakres zawija wywołania rozszerzenia rejestrowania w bloku [using](/dotnet/csharp/language-reference/keywords/using-statement) :</span><span class="sxs-lookup"><span data-stu-id="a44e6-181">The scope wraps the logging extension calls in a [using](/dotnet/csharp/language-reference/keywords/using-statement) block:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

<span data-ttu-id="a44e6-182">Sprawdź komunikaty dziennika w danych wyjściowych konsoli aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a44e6-182">Inspect the log messages in the app's console output.</span></span> <span data-ttu-id="a44e6-183">Poniższy wynik pokazuje trzy cudzysłowy usunięte z uwzględnieniem komunikatu o zakresie dziennika:</span><span class="sxs-lookup"><span data-stu-id="a44e6-183">The following result shows three quotes deleted with the log scope message included:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 1' Id = 2)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 2' Id = 3)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 3' Id = 4)
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a44e6-184"><xref:Microsoft.Extensions.Logging.LoggerMessage>funkcje tworzą delegatów z pamięcią podręczną, którzy wymagają mniejszej liczby alokacji obiektów i zmniejszonego obciążenia obliczeniowego w <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> porównaniu <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>z [metodami rozszerzenia rejestratora](xref:Microsoft.Extensions.Logging.LoggerExtensions), takimi jak i.</span><span class="sxs-lookup"><span data-stu-id="a44e6-184"><xref:Microsoft.Extensions.Logging.LoggerMessage> features create cacheable delegates that require fewer object allocations and reduced computational overhead compared to [logger extension methods](xref:Microsoft.Extensions.Logging.LoggerExtensions), such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>.</span></span> <span data-ttu-id="a44e6-185">W przypadku scenariuszy rejestrowania o wysokiej wydajności Użyj <xref:Microsoft.Extensions.Logging.LoggerMessage> wzorca.</span><span class="sxs-lookup"><span data-stu-id="a44e6-185">For high-performance logging scenarios, use the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

<span data-ttu-id="a44e6-186"><xref:Microsoft.Extensions.Logging.LoggerMessage>zapewnia następujące korzyści wynikające z wydajności w porównaniu z metodami rozszerzenia rejestratora:</span><span class="sxs-lookup"><span data-stu-id="a44e6-186"><xref:Microsoft.Extensions.Logging.LoggerMessage> provides the following performance advantages over Logger extension methods:</span></span>

* <span data-ttu-id="a44e6-187">Metody rozszerzenia rejestratora wymagają "opakowania" (do konwersji) typów wartości, `int`takich jak `object`, do.</span><span class="sxs-lookup"><span data-stu-id="a44e6-187">Logger extension methods require "boxing" (converting) value types, such as `int`, into `object`.</span></span> <span data-ttu-id="a44e6-188"><xref:Microsoft.Extensions.Logging.LoggerMessage> Wzorzec unika pakowania przy użyciu pól statycznych <xref:System.Action> i metod rozszerzających z parametrami o jednoznacznie określonym typie.</span><span class="sxs-lookup"><span data-stu-id="a44e6-188">The <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern avoids boxing by using static <xref:System.Action> fields and extension methods with strongly-typed parameters.</span></span>
* <span data-ttu-id="a44e6-189">Metody rozszerzenia rejestratora muszą analizować szablon wiadomości (nazwanego ciągu formatu) za każdym razem, gdy zostanie zapisany komunikat dziennika.</span><span class="sxs-lookup"><span data-stu-id="a44e6-189">Logger extension methods must parse the message template (named format string) every time a log message is written.</span></span> <span data-ttu-id="a44e6-190"><xref:Microsoft.Extensions.Logging.LoggerMessage>tylko wymaga analizy szablonu tylko raz, gdy komunikat jest zdefiniowany.</span><span class="sxs-lookup"><span data-stu-id="a44e6-190"><xref:Microsoft.Extensions.Logging.LoggerMessage> only requires parsing a template once when the message is defined.</span></span>

<span data-ttu-id="a44e6-191">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a44e6-191">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="a44e6-192">Przykładowa aplikacja pokazuje <xref:Microsoft.Extensions.Logging.LoggerMessage> funkcje z podstawowym systemem śledzenia ofert.</span><span class="sxs-lookup"><span data-stu-id="a44e6-192">The sample app demonstrates <xref:Microsoft.Extensions.Logging.LoggerMessage> features with a basic quote tracking system.</span></span> <span data-ttu-id="a44e6-193">Aplikacja dodaje i usuwa oferty przy użyciu bazy danych w pamięci.</span><span class="sxs-lookup"><span data-stu-id="a44e6-193">The app adds and deletes quotes using an in-memory database.</span></span> <span data-ttu-id="a44e6-194">W przypadku wystąpienia tych operacji komunikaty dziennika są generowane przy użyciu <xref:Microsoft.Extensions.Logging.LoggerMessage> wzorca.</span><span class="sxs-lookup"><span data-stu-id="a44e6-194">As these operations occur, log messages are generated using the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

## <a name="loggermessagedefine"></a><span data-ttu-id="a44e6-195">LoggerMessage. define</span><span class="sxs-lookup"><span data-stu-id="a44e6-195">LoggerMessage.Define</span></span>

<span data-ttu-id="a44e6-196">[Zdefiniuj (LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) tworzy <xref:System.Action> delegata do rejestrowania wiadomości.</span><span class="sxs-lookup"><span data-stu-id="a44e6-196">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) creates an <xref:System.Action> delegate for logging a message.</span></span> <span data-ttu-id="a44e6-197"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*>przeciążenia dopuszczają przekazywanie do sześciu parametrów typu do nazwanego ciągu formatu (szablonu).</span><span class="sxs-lookup"><span data-stu-id="a44e6-197"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> overloads permit passing up to six type parameters to a named format string (template).</span></span>

<span data-ttu-id="a44e6-198">Ciąg dostarczony do <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> metody jest szablonem, a nie ciągiem interpolowanym.</span><span class="sxs-lookup"><span data-stu-id="a44e6-198">The string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="a44e6-199">Symbole zastępcze są wypełniane w kolejności, w jakiej są określone typy.</span><span class="sxs-lookup"><span data-stu-id="a44e6-199">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="a44e6-200">Nazwy symboli zastępczych w szablonie powinny być opisowe i spójne w szablonach.</span><span class="sxs-lookup"><span data-stu-id="a44e6-200">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="a44e6-201">Służą one jako nazwy właściwości w danych dziennika strukturalnego.</span><span class="sxs-lookup"><span data-stu-id="a44e6-201">They serve as property names within structured log data.</span></span> <span data-ttu-id="a44e6-202">Zalecamy używanie [wielkości liter](/dotnet/standard/design-guidelines/capitalization-conventions) w języku Pascal dla nazw zastępczych.</span><span class="sxs-lookup"><span data-stu-id="a44e6-202">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="a44e6-203">Na przykład `{Count}`, `{FirstName}`.</span><span class="sxs-lookup"><span data-stu-id="a44e6-203">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="a44e6-204">Każdy komunikat dziennika jest <xref:System.Action> przechowywany w polu statycznym utworzonym przez [LoggerMessage. define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span><span class="sxs-lookup"><span data-stu-id="a44e6-204">Each log message is an <xref:System.Action> held in a static field created by [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span></span> <span data-ttu-id="a44e6-205">Przykładowo Przykładowa aplikacja tworzy pole opisujące komunikat dziennika dla żądania GET dla strony indeksu (*Internal/LoggerExtensions. cs*):</span><span class="sxs-lookup"><span data-stu-id="a44e6-205">For example, the sample app creates a field to describe a log message for a GET request for the Index page (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet1)]

<span data-ttu-id="a44e6-206">Dla <xref:System.Action>, określ:</span><span class="sxs-lookup"><span data-stu-id="a44e6-206">For the <xref:System.Action>, specify:</span></span>

* <span data-ttu-id="a44e6-207">Poziom dziennika.</span><span class="sxs-lookup"><span data-stu-id="a44e6-207">The log level.</span></span>
* <span data-ttu-id="a44e6-208">Unikatowy identyfikator zdarzenia (<xref:Microsoft.Extensions.Logging.EventId>) z nazwą statycznej metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="a44e6-208">A unique event identifier (<xref:Microsoft.Extensions.Logging.EventId>) with the name of the static extension method.</span></span>
* <span data-ttu-id="a44e6-209">Szablon wiadomości (nazwany ciąg formatu).</span><span class="sxs-lookup"><span data-stu-id="a44e6-209">The message template (named format string).</span></span> 

<span data-ttu-id="a44e6-210">Żądanie dotyczące strony indeksu przykładowej aplikacji ustawia:</span><span class="sxs-lookup"><span data-stu-id="a44e6-210">A request for the Index page of the sample app sets the:</span></span>

* <span data-ttu-id="a44e6-211">Poziom rejestrowania do `Information`.</span><span class="sxs-lookup"><span data-stu-id="a44e6-211">Log level to `Information`.</span></span>
* <span data-ttu-id="a44e6-212">Identyfikator zdarzenia do `1` nazwy `IndexPageRequested` metody.</span><span class="sxs-lookup"><span data-stu-id="a44e6-212">Event id to `1` with the name of the `IndexPageRequested` method.</span></span>
* <span data-ttu-id="a44e6-213">Szablon wiadomości (nazwany ciąg formatu) do ciągu.</span><span class="sxs-lookup"><span data-stu-id="a44e6-213">Message template (named format string) to a string.</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet5)]

<span data-ttu-id="a44e6-214">Magazyny rejestrowania strukturalnego mogą używać nazwy zdarzenia, gdy jest ona dostarczana z identyfikatorem zdarzenia w celu wzbogacania rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="a44e6-214">Structured logging stores may use the event name when it's supplied with the event id to enrich logging.</span></span> <span data-ttu-id="a44e6-215">Na przykład [Serilog](https://github.com/serilog/serilog-extensions-logging) używa nazwy zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="a44e6-215">For example, [Serilog](https://github.com/serilog/serilog-extensions-logging) uses the event name.</span></span>

<span data-ttu-id="a44e6-216"><xref:System.Action> Jest wywoływany za pomocą metody rozszerzającej o jednoznacznie określonym typie.</span><span class="sxs-lookup"><span data-stu-id="a44e6-216">The <xref:System.Action> is invoked through a strongly-typed extension method.</span></span> <span data-ttu-id="a44e6-217">`IndexPageRequested` Metoda rejestruje komunikat dla strony indeksu żądania GET w przykładowej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="a44e6-217">The `IndexPageRequested` method logs a message for an Index page GET request in the sample app:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet9)]

<span data-ttu-id="a44e6-218">`IndexPageRequested`jest wywoływana dla rejestratora w `OnGetAsync` metodzie w *Pages/index. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="a44e6-218">`IndexPageRequested` is called on the logger in the `OnGetAsync` method in *Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

<span data-ttu-id="a44e6-219">Sprawdź dane wyjściowe konsoli aplikacji:</span><span class="sxs-lookup"><span data-stu-id="a44e6-219">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

<span data-ttu-id="a44e6-220">Aby przekazać parametry do komunikatu dziennika, zdefiniuj maksymalnie sześć typów podczas tworzenia pola statycznego.</span><span class="sxs-lookup"><span data-stu-id="a44e6-220">To pass parameters to a log message, define up to six types when creating the static field.</span></span> <span data-ttu-id="a44e6-221">Przykładowa aplikacja rejestruje ciąg podczas dodawania oferty przez zdefiniowanie `string` typu dla <xref:System.Action> pola:</span><span class="sxs-lookup"><span data-stu-id="a44e6-221">The sample app logs a string when adding a quote by defining a `string` type for the <xref:System.Action> field:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet2)]

<span data-ttu-id="a44e6-222">Szablon komunikatu dziennika delegata otrzymuje wartości zastępcze z dostarczonych typów.</span><span class="sxs-lookup"><span data-stu-id="a44e6-222">The delegate's log message template receives its placeholder values from the types provided.</span></span> <span data-ttu-id="a44e6-223">Przykładowa aplikacja definiuje delegata do dodawania oferty, w której parametr Quote jest `string`:</span><span class="sxs-lookup"><span data-stu-id="a44e6-223">The sample app defines a delegate for adding a quote where the quote parameter is a `string`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet6)]

<span data-ttu-id="a44e6-224">Statyczna metoda rozszerzenia służąca do dodawania oferty `QuoteAdded`,, odbiera wartość argumentu cudzysłowu i przekazuje ją do <xref:System.Action> delegata:</span><span class="sxs-lookup"><span data-stu-id="a44e6-224">The static extension method for adding a quote, `QuoteAdded`, receives the quote argument value and passes it to the <xref:System.Action> delegate:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet10)]

<span data-ttu-id="a44e6-225">W modelu strony strony indeksu (*Pages/index. cshtml. cs*) `QuoteAdded` jest wywoływana w celu zarejestrowania komunikatu:</span><span class="sxs-lookup"><span data-stu-id="a44e6-225">In the Index page's page model (*Pages/Index.cshtml.cs*), `QuoteAdded` is called to log the message:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

<span data-ttu-id="a44e6-226">Sprawdź dane wyjściowe konsoli aplikacji:</span><span class="sxs-lookup"><span data-stu-id="a44e6-226">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand')
```

<span data-ttu-id="a44e6-227">Przykładowa aplikacja implementuje wzór [try&ndash;catch](/dotnet/csharp/language-reference/keywords/try-catch) dla usunięcia oferty.</span><span class="sxs-lookup"><span data-stu-id="a44e6-227">The sample app implements a [try&ndash;catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern for quote deletion.</span></span> <span data-ttu-id="a44e6-228">Komunikat informacyjny jest rejestrowany w przypadku pomyślnego usunięcia operacji usuwania.</span><span class="sxs-lookup"><span data-stu-id="a44e6-228">An informational message is logged for a successful delete operation.</span></span> <span data-ttu-id="a44e6-229">Komunikat o błędzie jest rejestrowany dla operacji usuwania, gdy zostanie zgłoszony wyjątek.</span><span class="sxs-lookup"><span data-stu-id="a44e6-229">An error message is logged for a delete operation when an exception is thrown.</span></span> <span data-ttu-id="a44e6-230">Komunikat dziennika dla nieprawidłowej operacji usuwania obejmuje ślad stosu wyjątku (*wewnętrzny/LoggerExtensions. cs*):</span><span class="sxs-lookup"><span data-stu-id="a44e6-230">The log message for the unsuccessful delete operation includes the exception stack trace (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet7)]

<span data-ttu-id="a44e6-231">Zwróć uwagę, jak wyjątek jest przesyłany do delegata w `QuoteDeleteFailed`:</span><span class="sxs-lookup"><span data-stu-id="a44e6-231">Note how the exception is passed to the delegate in `QuoteDeleteFailed`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet11)]

<span data-ttu-id="a44e6-232">W modelu strony dla strony indeksu pomyślne usunięcie cudzysłowu wywołuje `QuoteDeleted` metodę w rejestratorze.</span><span class="sxs-lookup"><span data-stu-id="a44e6-232">In the page model for the Index page, a successful quote deletion calls the `QuoteDeleted` method on the logger.</span></span> <span data-ttu-id="a44e6-233">Gdy cytat nie zostanie znaleziony do usunięcia, <xref:System.ArgumentNullException> jest zgłaszany.</span><span class="sxs-lookup"><span data-stu-id="a44e6-233">When a quote isn't found for deletion, an <xref:System.ArgumentNullException> is thrown.</span></span> <span data-ttu-id="a44e6-234">Wyjątek jest zalewkowany przez instrukcję [try&ndash;catch](/dotnet/csharp/language-reference/keywords/try-catch) i rejestrowany przez wywołanie `QuoteDeleteFailed` metody w rejestratorze w bloku [catch](/dotnet/csharp/language-reference/keywords/try-catch) (*Pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="a44e6-234">The exception is trapped by the [try&ndash;catch](/dotnet/csharp/language-reference/keywords/try-catch) statement and logged by calling the `QuoteDeleteFailed` method on the logger in the [catch](/dotnet/csharp/language-reference/keywords/try-catch) block (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet5&highlight=14,18)]

<span data-ttu-id="a44e6-235">Po pomyślnym usunięciu oferty Sprawdź dane wyjściowe konsoli aplikacji:</span><span class="sxs-lookup"><span data-stu-id="a44e6-235">When a quote is successfully deleted, inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand' Id = 1)
```

<span data-ttu-id="a44e6-236">Po niepowodzeniu usuwania oferty Sprawdź dane wyjściowe konsoli aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a44e6-236">When quote deletion fails, inspect the app's console output.</span></span> <span data-ttu-id="a44e6-237">Należy zauważyć, że wyjątek jest zawarty w komunikacie dziennika:</span><span class="sxs-lookup"><span data-stu-id="a44e6-237">Note that the exception is included in the log message:</span></span>

```console
fail: LoggerMessageSample.Pages.IndexModel[5]
      => RequestId:0HL90M6E7PHK5:00000010 RequestPath:/ => /Index
      Quote delete failed (Id = 999)
System.ArgumentNullException: Value cannot be null.
Parameter name: entity
   at Microsoft.EntityFrameworkCore.Utilities.Check.NotNull[T]
       (T value, String parameterName)
   at Microsoft.EntityFrameworkCore.DbContext.Remove[TEntity](TEntity entity)
   at Microsoft.EntityFrameworkCore.Internal.InternalDbSet`1.Remove(TEntity entity)
   at LoggerMessageSample.Pages.IndexModel.<OnPostDeleteQuoteAsync>d__14.MoveNext() 
      in <PATH>\sample\Pages\Index.cshtml.cs:line 87
```

## <a name="loggermessagedefinescope"></a><span data-ttu-id="a44e6-238">LoggerMessage. DefineScope —</span><span class="sxs-lookup"><span data-stu-id="a44e6-238">LoggerMessage.DefineScope</span></span>

<span data-ttu-id="a44e6-239">[DefineScope — (ciąg)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) tworzy <xref:System.Func%601> delegata do definiowania [zakresu dziennika](xref:fundamentals/logging/index#log-scopes).</span><span class="sxs-lookup"><span data-stu-id="a44e6-239">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) creates a <xref:System.Func%601> delegate for defining a [log scope](xref:fundamentals/logging/index#log-scopes).</span></span> <span data-ttu-id="a44e6-240"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*>przeciążenia dopuszczają przekazywanie do trzech parametrów typu do nazwanego ciągu formatu (szablonu).</span><span class="sxs-lookup"><span data-stu-id="a44e6-240"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> overloads permit passing up to three type parameters to a named format string (template).</span></span>

<span data-ttu-id="a44e6-241">Podobnie jak w przypadku <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> metody, ciąg dostarczony do <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> metody jest szablonem, a nie ciągiem interpolowanym.</span><span class="sxs-lookup"><span data-stu-id="a44e6-241">As is the case with the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method, the string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="a44e6-242">Symbole zastępcze są wypełniane w kolejności, w jakiej są określone typy.</span><span class="sxs-lookup"><span data-stu-id="a44e6-242">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="a44e6-243">Nazwy symboli zastępczych w szablonie powinny być opisowe i spójne w szablonach.</span><span class="sxs-lookup"><span data-stu-id="a44e6-243">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="a44e6-244">Służą one jako nazwy właściwości w danych dziennika strukturalnego.</span><span class="sxs-lookup"><span data-stu-id="a44e6-244">They serve as property names within structured log data.</span></span> <span data-ttu-id="a44e6-245">Zalecamy używanie [wielkości liter](/dotnet/standard/design-guidelines/capitalization-conventions) w języku Pascal dla nazw zastępczych.</span><span class="sxs-lookup"><span data-stu-id="a44e6-245">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="a44e6-246">Na przykład `{Count}`, `{FirstName}`.</span><span class="sxs-lookup"><span data-stu-id="a44e6-246">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="a44e6-247">Zdefiniuj [zakres dziennika](xref:fundamentals/logging/index#log-scopes) , który ma zostać zastosowany do serii komunikatów dziennika przy <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> użyciu metody.</span><span class="sxs-lookup"><span data-stu-id="a44e6-247">Define a [log scope](xref:fundamentals/logging/index#log-scopes) to apply to a series of log messages using the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method.</span></span>

<span data-ttu-id="a44e6-248">Przykładowa aplikacja ma przycisk **Wyczyść wszystko** , aby usunąć wszystkie cudzysłowy w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="a44e6-248">The sample app has a **Clear All** button for deleting all of the quotes in the database.</span></span> <span data-ttu-id="a44e6-249">Cudzysłowy są usuwane, usuwając je pojedynczo.</span><span class="sxs-lookup"><span data-stu-id="a44e6-249">The quotes are deleted by removing them one at a time.</span></span> <span data-ttu-id="a44e6-250">Przy każdym usunięciu oferty `QuoteDeleted` Metoda jest wywoływana w rejestratorze.</span><span class="sxs-lookup"><span data-stu-id="a44e6-250">Each time a quote is deleted, the `QuoteDeleted` method is called on the logger.</span></span> <span data-ttu-id="a44e6-251">Do tych komunikatów dziennika jest dodawany zakres dziennika.</span><span class="sxs-lookup"><span data-stu-id="a44e6-251">A log scope is added to these log messages.</span></span>

<span data-ttu-id="a44e6-252">Włącz `IncludeScopes` w sekcji rejestratora konsoli pliku *appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="a44e6-252">Enable `IncludeScopes` in the console logger section of *appsettings.json*:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/appsettings.json?highlight=3-5)]

<span data-ttu-id="a44e6-253">Aby utworzyć zakres dziennika, należy dodać pole do przechowywania <xref:System.Func%601> delegata dla zakresu.</span><span class="sxs-lookup"><span data-stu-id="a44e6-253">To create a log scope, add a field to hold a <xref:System.Func%601> delegate for the scope.</span></span> <span data-ttu-id="a44e6-254">Przykładowa aplikacja tworzy pole o nazwie `_allQuotesDeletedScope` (*wewnętrzne/LoggerExtensions. cs*):</span><span class="sxs-lookup"><span data-stu-id="a44e6-254">The sample app creates a field called `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet4)]

<span data-ttu-id="a44e6-255">Użyj <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> , aby utworzyć delegata.</span><span class="sxs-lookup"><span data-stu-id="a44e6-255">Use <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> to create the delegate.</span></span> <span data-ttu-id="a44e6-256">Można określić maksymalnie trzy typy do użycia jako argumenty szablonu, gdy obiekt delegowany jest wywoływany.</span><span class="sxs-lookup"><span data-stu-id="a44e6-256">Up to three types can be specified for use as template arguments when the delegate is invoked.</span></span> <span data-ttu-id="a44e6-257">Przykładowa aplikacja używa szablonu komunikatu zawierającego liczbę usuniętych cudzysłowów ( `int` typ):</span><span class="sxs-lookup"><span data-stu-id="a44e6-257">The sample app uses a message template that includes the number of deleted quotes (an `int` type):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet8)]

<span data-ttu-id="a44e6-258">Podaj statyczną metodę rozszerzenia dla komunikatu dziennika.</span><span class="sxs-lookup"><span data-stu-id="a44e6-258">Provide a static extension method for the log message.</span></span> <span data-ttu-id="a44e6-259">Dołącz wszystkie parametry typu dla nazwanych właściwości, które są wyświetlane w szablonie wiadomości.</span><span class="sxs-lookup"><span data-stu-id="a44e6-259">Include any type parameters for named properties that appear in the message template.</span></span> <span data-ttu-id="a44e6-260">Przykładowa aplikacja wykonuje `count` cudzysłowy, aby usunąć i zwracać `_allQuotesDeletedScope`:</span><span class="sxs-lookup"><span data-stu-id="a44e6-260">The sample app takes in a `count` of quotes to delete and returns `_allQuotesDeletedScope`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet12)]

<span data-ttu-id="a44e6-261">Zakres zawija wywołania rozszerzenia rejestrowania w bloku [using](/dotnet/csharp/language-reference/keywords/using-statement) :</span><span class="sxs-lookup"><span data-stu-id="a44e6-261">The scope wraps the logging extension calls in a [using](/dotnet/csharp/language-reference/keywords/using-statement) block:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

<span data-ttu-id="a44e6-262">Sprawdź komunikaty dziennika w danych wyjściowych konsoli aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a44e6-262">Inspect the log messages in the app's console output.</span></span> <span data-ttu-id="a44e6-263">Poniższy wynik pokazuje trzy cudzysłowy usunięte z uwzględnieniem komunikatu o zakresie dziennika:</span><span class="sxs-lookup"><span data-stu-id="a44e6-263">The following result shows three quotes deleted with the log scope message included:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 1' Id = 2)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 2' Id = 3)
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:0000002E RequestPath:/ => /Index => 
          All quotes deleted (Count = 3)
      Quote deleted (Quote = 'Quote 3' Id = 4)
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="a44e6-264">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="a44e6-264">Additional resources</span></span>

* [<span data-ttu-id="a44e6-265">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="a44e6-265">Logging</span></span>](xref:fundamentals/logging/index)
