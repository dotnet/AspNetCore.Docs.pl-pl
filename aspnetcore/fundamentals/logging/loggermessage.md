---
title: Rejestrowanie o wysokiej wydajności za pomocą loggerMessage w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak używać LoggerMessage do tworzenia buforowanych delegatów, które wymagają mniejszej liczby alokacji obiektów dla scenariuszy rejestrowania o wysokiej wydajności.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2019
uid: fundamentals/logging/loggermessage
ms.openlocfilehash: 48ebba69b5c15a0f9a42f7f6b3d2c1fcb0a2211c
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78663220"
---
# <a name="high-performance-logging-with-loggermessage-in-aspnet-core"></a><span data-ttu-id="c0e6b-103">Rejestrowanie o wysokiej wydajności za pomocą loggerMessage w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c0e6b-103">High-performance logging with LoggerMessage in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c0e6b-104"><xref:Microsoft.Extensions.Logging.LoggerMessage>funkcje tworzą buforowalne delegatów, które wymagają mniej alokacji obiektów i zmniejszenie <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>obciążenia obliczeniowego w porównaniu do metod [rozszerzenia rejestratora,](xref:Microsoft.Extensions.Logging.LoggerExtensions)takich jak i .</span><span class="sxs-lookup"><span data-stu-id="c0e6b-104"><xref:Microsoft.Extensions.Logging.LoggerMessage> features create cacheable delegates that require fewer object allocations and reduced computational overhead compared to [logger extension methods](xref:Microsoft.Extensions.Logging.LoggerExtensions), such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>.</span></span> <span data-ttu-id="c0e6b-105">W przypadku scenariuszy rejestrowania o <xref:Microsoft.Extensions.Logging.LoggerMessage> wysokiej wydajności należy użyć wzorca.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-105">For high-performance logging scenarios, use the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

<span data-ttu-id="c0e6b-106"><xref:Microsoft.Extensions.Logging.LoggerMessage>zapewnia następujące korzyści wydajności w stosunku do metod rozszerzenia Rejestratora:</span><span class="sxs-lookup"><span data-stu-id="c0e6b-106"><xref:Microsoft.Extensions.Logging.LoggerMessage> provides the following performance advantages over Logger extension methods:</span></span>

* <span data-ttu-id="c0e6b-107">Metody rozszerzenia rejestratora wymagają "boksowania" (konwersji) typów wartości, takich jak `int`. `object`</span><span class="sxs-lookup"><span data-stu-id="c0e6b-107">Logger extension methods require "boxing" (converting) value types, such as `int`, into `object`.</span></span> <span data-ttu-id="c0e6b-108">Wzorzec <xref:Microsoft.Extensions.Logging.LoggerMessage> unika boksu <xref:System.Action> przy użyciu pól statycznych i metod rozszerzenia z parametrami silnie typizowane.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-108">The <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern avoids boxing by using static <xref:System.Action> fields and extension methods with strongly-typed parameters.</span></span>
* <span data-ttu-id="c0e6b-109">Metody rozszerzenia rejestratora należy przeanalizować szablon wiadomości (ciąg formatu o nazwie) za każdym razem, gdy jest zapisywany komunikat dziennika.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-109">Logger extension methods must parse the message template (named format string) every time a log message is written.</span></span> <span data-ttu-id="c0e6b-110"><xref:Microsoft.Extensions.Logging.LoggerMessage>wymaga analizowania szablonu tylko raz, gdy wiadomość jest zdefiniowana.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-110"><xref:Microsoft.Extensions.Logging.LoggerMessage> only requires parsing a template once when the message is defined.</span></span>

<span data-ttu-id="c0e6b-111">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c0e6b-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="c0e6b-112">Przykładowa aplikacja <xref:Microsoft.Extensions.Logging.LoggerMessage> pokazuje funkcje za pomocą podstawowego systemu śledzenia ofert.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-112">The sample app demonstrates <xref:Microsoft.Extensions.Logging.LoggerMessage> features with a basic quote tracking system.</span></span> <span data-ttu-id="c0e6b-113">Aplikacja dodaje i usuwa cudzysłowy przy użyciu bazy danych w pamięci.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-113">The app adds and deletes quotes using an in-memory database.</span></span> <span data-ttu-id="c0e6b-114">W miarę występowania tych operacji <xref:Microsoft.Extensions.Logging.LoggerMessage> komunikaty dziennika są generowane przy użyciu wzorca.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-114">As these operations occur, log messages are generated using the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

## <a name="loggermessagedefine"></a><span data-ttu-id="c0e6b-115">LoggerMessage.Define</span><span class="sxs-lookup"><span data-stu-id="c0e6b-115">LoggerMessage.Define</span></span>

<span data-ttu-id="c0e6b-116">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) tworzy <xref:System.Action> pełnomocnika do rejestrowania wiadomości.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-116">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) creates an <xref:System.Action> delegate for logging a message.</span></span> <span data-ttu-id="c0e6b-117"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*>przeciążenia umożliwiają przekazywanie maksymalnie sześciu parametrów typu do ciągu o nazwie formatu (szablonu).</span><span class="sxs-lookup"><span data-stu-id="c0e6b-117"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> overloads permit passing up to six type parameters to a named format string (template).</span></span>

<span data-ttu-id="c0e6b-118">Ciąg dostarczony do <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> metody jest szablonem, a nie interpolowanym ciągiem.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-118">The string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="c0e6b-119">Symbole zastępcze są wypełniane w kolejności, w określonej kolejności.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-119">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="c0e6b-120">Nazwy symboli zastępczych w szablonie powinny być opisowe i spójne w szablonach.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-120">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="c0e6b-121">Służą one jako nazwy właściwości w danych dziennika strukturalnego.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-121">They serve as property names within structured log data.</span></span> <span data-ttu-id="c0e6b-122">Zalecamy [pascalową obudowę](/dotnet/standard/design-guidelines/capitalization-conventions) dla nazw symboli zastępczych.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-122">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="c0e6b-123">Na przykład `{Count}` `{FirstName}`, .</span><span class="sxs-lookup"><span data-stu-id="c0e6b-123">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="c0e6b-124">Każdy komunikat dziennika <xref:System.Action> jest przechowywany w polu statycznym utworzonym przez [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span><span class="sxs-lookup"><span data-stu-id="c0e6b-124">Each log message is an <xref:System.Action> held in a static field created by [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span></span> <span data-ttu-id="c0e6b-125">Na przykład przykładowa aplikacja tworzy pole opisujące komunikat dziennika dla żądania GET dla strony Indeks (*Internal/LoggerExtensions.cs*):</span><span class="sxs-lookup"><span data-stu-id="c0e6b-125">For example, the sample app creates a field to describe a log message for a GET request for the Index page (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet1)]

<span data-ttu-id="c0e6b-126">Dla <xref:System.Action>, określić:</span><span class="sxs-lookup"><span data-stu-id="c0e6b-126">For the <xref:System.Action>, specify:</span></span>

* <span data-ttu-id="c0e6b-127">Poziom dziennika.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-127">The log level.</span></span>
* <span data-ttu-id="c0e6b-128">Unikatowy identyfikator zdarzenia<xref:Microsoft.Extensions.Logging.EventId>( ) z nazwą metody rozszerzenia statycznego.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-128">A unique event identifier (<xref:Microsoft.Extensions.Logging.EventId>) with the name of the static extension method.</span></span>
* <span data-ttu-id="c0e6b-129">Szablon wiadomości (ciąg formatu o nazwie).</span><span class="sxs-lookup"><span data-stu-id="c0e6b-129">The message template (named format string).</span></span> 

<span data-ttu-id="c0e6b-130">Żądanie dla indeksu strony przykładowej aplikacji ustawia:</span><span class="sxs-lookup"><span data-stu-id="c0e6b-130">A request for the Index page of the sample app sets the:</span></span>

* <span data-ttu-id="c0e6b-131">Poziom dziennika `Information`do .</span><span class="sxs-lookup"><span data-stu-id="c0e6b-131">Log level to `Information`.</span></span>
* <span data-ttu-id="c0e6b-132">Identyfikator zdarzenia `1` z nazwą `IndexPageRequested` metody.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-132">Event id to `1` with the name of the `IndexPageRequested` method.</span></span>
* <span data-ttu-id="c0e6b-133">Szablon wiadomości (nazwany ciąg formatu) do ciągu.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-133">Message template (named format string) to a string.</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet5)]

<span data-ttu-id="c0e6b-134">Magazyny rejestrowania strukturalnego mogą używać nazwy zdarzenia, gdy jest ona dostarczana z identyfikatorem zdarzenia, aby wzbogacić rejestrowanie.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-134">Structured logging stores may use the event name when it's supplied with the event id to enrich logging.</span></span> <span data-ttu-id="c0e6b-135">Na przykład [Serilog](https://github.com/serilog/serilog-extensions-logging) używa nazwy zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-135">For example, [Serilog](https://github.com/serilog/serilog-extensions-logging) uses the event name.</span></span>

<span data-ttu-id="c0e6b-136">Jest <xref:System.Action> wywoływana za pomocą metody rozszerzenia silnie typizowane.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-136">The <xref:System.Action> is invoked through a strongly-typed extension method.</span></span> <span data-ttu-id="c0e6b-137">Metoda `IndexPageRequested` rejestruje komunikat dla żądania GET strony indeksu w przykładowej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="c0e6b-137">The `IndexPageRequested` method logs a message for an Index page GET request in the sample app:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet9)]

<span data-ttu-id="c0e6b-138">`IndexPageRequested`jest wywoływana na rejestratorze w metodzie `OnGetAsync` w *Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="c0e6b-138">`IndexPageRequested` is called on the logger in the `OnGetAsync` method in *Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

<span data-ttu-id="c0e6b-139">Sprawdź dane wyjściowe konsoli aplikacji:</span><span class="sxs-lookup"><span data-stu-id="c0e6b-139">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

<span data-ttu-id="c0e6b-140">Aby przekazać parametry do komunikatu dziennika, należy zdefiniować maksymalnie sześć typów podczas tworzenia pola statycznego.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-140">To pass parameters to a log message, define up to six types when creating the static field.</span></span> <span data-ttu-id="c0e6b-141">Przykładowa aplikacja rejestruje ciąg podczas dodawania cudzysłowu, `string` definiując typ <xref:System.Action> dla pola:</span><span class="sxs-lookup"><span data-stu-id="c0e6b-141">The sample app logs a string when adding a quote by defining a `string` type for the <xref:System.Action> field:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet2)]

<span data-ttu-id="c0e6b-142">Szablon komunikatu dziennika pełnomocnika odbiera jego wartości zastępcze z podanych typów.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-142">The delegate's log message template receives its placeholder values from the types provided.</span></span> <span data-ttu-id="c0e6b-143">Przykładowa aplikacja definiuje pełnomocnika do dodawania oferty, `string`gdzie parametr oferty jest:</span><span class="sxs-lookup"><span data-stu-id="c0e6b-143">The sample app defines a delegate for adding a quote where the quote parameter is a `string`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet6)]

<span data-ttu-id="c0e6b-144">Metoda rozszerzenia statycznego do dodawania oferty, `QuoteAdded`otrzymuje wartość argumentu <xref:System.Action> oferty i przekazuje ją do delegata:</span><span class="sxs-lookup"><span data-stu-id="c0e6b-144">The static extension method for adding a quote, `QuoteAdded`, receives the quote argument value and passes it to the <xref:System.Action> delegate:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet10)]

<span data-ttu-id="c0e6b-145">W modelu strony indeksu *(Pages/Index.cshtml.cs)* `QuoteAdded` jest wywoływana w celu zarejestrowania wiadomości:</span><span class="sxs-lookup"><span data-stu-id="c0e6b-145">In the Index page's page model (*Pages/Index.cshtml.cs*), `QuoteAdded` is called to log the message:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

<span data-ttu-id="c0e6b-146">Sprawdź dane wyjściowe konsoli aplikacji:</span><span class="sxs-lookup"><span data-stu-id="c0e6b-146">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand')
```

<span data-ttu-id="c0e6b-147">Przykładowa aplikacja implementuje wzorzec [try&ndash;catch](/dotnet/csharp/language-reference/keywords/try-catch) do usunięcia oferty.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-147">The sample app implements a [try&ndash;catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern for quote deletion.</span></span> <span data-ttu-id="c0e6b-148">Komunikat informacyjny jest rejestrowany dla pomyślnej operacji usuwania.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-148">An informational message is logged for a successful delete operation.</span></span> <span data-ttu-id="c0e6b-149">Komunikat o błędzie jest rejestrowany dla operacji usuwania, gdy wyjątek.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-149">An error message is logged for a delete operation when an exception is thrown.</span></span> <span data-ttu-id="c0e6b-150">Komunikat dziennika dla operacji usuwania nieudanego zawiera śledzenie stosu*wyjątków (Internal/LoggerExtensions.cs*):</span><span class="sxs-lookup"><span data-stu-id="c0e6b-150">The log message for the unsuccessful delete operation includes the exception stack trace (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet7)]

<span data-ttu-id="c0e6b-151">Zwróć uwagę, jak wyjątek `QuoteDeleteFailed`jest przekazywany do pełnomocnika w:</span><span class="sxs-lookup"><span data-stu-id="c0e6b-151">Note how the exception is passed to the delegate in `QuoteDeleteFailed`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet11)]

<span data-ttu-id="c0e6b-152">W modelu strony dla indeksu strony pomyślne usunięcie `QuoteDeleted` oferty wywołuje metodę na rejestratorze.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-152">In the page model for the Index page, a successful quote deletion calls the `QuoteDeleted` method on the logger.</span></span> <span data-ttu-id="c0e6b-153">Gdy cytat nie zostanie znaleziony do usunięcia, jest wyrzucany. <xref:System.ArgumentNullException></span><span class="sxs-lookup"><span data-stu-id="c0e6b-153">When a quote isn't found for deletion, an <xref:System.ArgumentNullException> is thrown.</span></span> <span data-ttu-id="c0e6b-154">Wyjątek jest zalewkowany przez [try&ndash;catch](/dotnet/csharp/language-reference/keywords/try-catch) instrukcji `QuoteDeleteFailed` i rejestrowane przez wywołanie metody na rejestratora w bloku [catch](/dotnet/csharp/language-reference/keywords/try-catch) *(Pages/Index.cshtml.cs):*</span><span class="sxs-lookup"><span data-stu-id="c0e6b-154">The exception is trapped by the [try&ndash;catch](/dotnet/csharp/language-reference/keywords/try-catch) statement and logged by calling the `QuoteDeleteFailed` method on the logger in the [catch](/dotnet/csharp/language-reference/keywords/try-catch) block (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet5&highlight=9,13)]

<span data-ttu-id="c0e6b-155">Po pomyślnym usunięciu oferty sprawdź dane wyjściowe konsoli aplikacji:</span><span class="sxs-lookup"><span data-stu-id="c0e6b-155">When a quote is successfully deleted, inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand' Id = 1)
```

<span data-ttu-id="c0e6b-156">Gdy usunięcie oferty nie powiedzie się, sprawdź dane wyjściowe konsoli aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-156">When quote deletion fails, inspect the app's console output.</span></span> <span data-ttu-id="c0e6b-157">Należy zauważyć, że wyjątek znajduje się w komunikacie dziennika:</span><span class="sxs-lookup"><span data-stu-id="c0e6b-157">Note that the exception is included in the log message:</span></span>

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

## <a name="loggermessagedefinescope"></a><span data-ttu-id="c0e6b-158">LoggerMessage.DefineScope</span><span class="sxs-lookup"><span data-stu-id="c0e6b-158">LoggerMessage.DefineScope</span></span>

<span data-ttu-id="c0e6b-159">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) tworzy <xref:System.Func%601> pełnomocnika do definiowania [zakresu dziennika](xref:fundamentals/logging/index#log-scopes).</span><span class="sxs-lookup"><span data-stu-id="c0e6b-159">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) creates a <xref:System.Func%601> delegate for defining a [log scope](xref:fundamentals/logging/index#log-scopes).</span></span> <span data-ttu-id="c0e6b-160"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*>przeciążenia umożliwiają przekazywanie maksymalnie trzech parametrów typu do ciągu o nazwie formatu (szablonu).</span><span class="sxs-lookup"><span data-stu-id="c0e6b-160"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> overloads permit passing up to three type parameters to a named format string (template).</span></span>

<span data-ttu-id="c0e6b-161">Podobnie jak w <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> przypadku metody, ciąg <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> dostarczony do metody jest szablonem, a nie interpolowanym ciągiem.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-161">As is the case with the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method, the string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="c0e6b-162">Symbole zastępcze są wypełniane w kolejności, w określonej kolejności.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-162">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="c0e6b-163">Nazwy symboli zastępczych w szablonie powinny być opisowe i spójne w szablonach.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-163">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="c0e6b-164">Służą one jako nazwy właściwości w danych dziennika strukturalnego.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-164">They serve as property names within structured log data.</span></span> <span data-ttu-id="c0e6b-165">Zalecamy [pascalową obudowę](/dotnet/standard/design-guidelines/capitalization-conventions) dla nazw symboli zastępczych.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-165">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="c0e6b-166">Na przykład `{Count}` `{FirstName}`, .</span><span class="sxs-lookup"><span data-stu-id="c0e6b-166">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="c0e6b-167">Zdefiniuj [zakres dziennika,](xref:fundamentals/logging/index#log-scopes) aby zastosować <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> do serii komunikatów dziennika przy użyciu metody.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-167">Define a [log scope](xref:fundamentals/logging/index#log-scopes) to apply to a series of log messages using the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method.</span></span>

<span data-ttu-id="c0e6b-168">Przykładowa aplikacja ma przycisk **Wyczyść wszystko** do usuwania wszystkich cudzysłowów w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-168">The sample app has a **Clear All** button for deleting all of the quotes in the database.</span></span> <span data-ttu-id="c0e6b-169">Cudzysłowy są usuwane przez usunięcie ich po jednym na raz.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-169">The quotes are deleted by removing them one at a time.</span></span> <span data-ttu-id="c0e6b-170">Za każdym razem, gdy `QuoteDeleted` cytat jest usuwany, metoda jest wywoływana na rejestratorze.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-170">Each time a quote is deleted, the `QuoteDeleted` method is called on the logger.</span></span> <span data-ttu-id="c0e6b-171">Zakres dziennika jest dodawany do tych komunikatów dziennika.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-171">A log scope is added to these log messages.</span></span>

<span data-ttu-id="c0e6b-172">Włącz `IncludeScopes` w sekcji rejestrator konsoli *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="c0e6b-172">Enable `IncludeScopes` in the console logger section of *appsettings.json*:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/appsettings.json?highlight=3-5)]

<span data-ttu-id="c0e6b-173">Aby utworzyć zakres dziennika, dodaj pole <xref:System.Func%601> do przechowywania pełnomocnika dla zakresu.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-173">To create a log scope, add a field to hold a <xref:System.Func%601> delegate for the scope.</span></span> <span data-ttu-id="c0e6b-174">Przykładowa aplikacja tworzy `_allQuotesDeletedScope` pole o nazwie *(Internal/LoggerExtensions.cs):*</span><span class="sxs-lookup"><span data-stu-id="c0e6b-174">The sample app creates a field called `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet4)]

<span data-ttu-id="c0e6b-175">Służy <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> do tworzenia pełnomocnika.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-175">Use <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> to create the delegate.</span></span> <span data-ttu-id="c0e6b-176">Do trzech typów można określić do użycia jako argumenty szablonu, gdy jest wywoływany delegat.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-176">Up to three types can be specified for use as template arguments when the delegate is invoked.</span></span> <span data-ttu-id="c0e6b-177">Przykładowa aplikacja używa szablonu wiadomości, który zawiera liczbę `int` usuniętych ofert (typ):</span><span class="sxs-lookup"><span data-stu-id="c0e6b-177">The sample app uses a message template that includes the number of deleted quotes (an `int` type):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet8)]

<span data-ttu-id="c0e6b-178">Podaj metodę rozszerzenia statycznego dla komunikatu dziennika.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-178">Provide a static extension method for the log message.</span></span> <span data-ttu-id="c0e6b-179">Dołącz wszystkie parametry typu dla nazwanych właściwości, które pojawiają się w szablonie wiadomości.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-179">Include any type parameters for named properties that appear in the message template.</span></span> <span data-ttu-id="c0e6b-180">Przykładowa aplikacja przyjmuje `count` w cudzysłowie, aby usunąć i zwraca: `_allQuotesDeletedScope`</span><span class="sxs-lookup"><span data-stu-id="c0e6b-180">The sample app takes in a `count` of quotes to delete and returns `_allQuotesDeletedScope`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet12)]

<span data-ttu-id="c0e6b-181">Zakres zawija wywołania rozszerzenia rejestrowania w [using](/dotnet/csharp/language-reference/keywords/using-statement) bloku:</span><span class="sxs-lookup"><span data-stu-id="c0e6b-181">The scope wraps the logging extension calls in a [using](/dotnet/csharp/language-reference/keywords/using-statement) block:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

<span data-ttu-id="c0e6b-182">Sprawdź komunikaty dziennika w danych wyjściowych konsoli aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-182">Inspect the log messages in the app's console output.</span></span> <span data-ttu-id="c0e6b-183">Poniższy wynik pokazuje trzy cudzysłowy usunięte z komunikatem zakresu dziennika:</span><span class="sxs-lookup"><span data-stu-id="c0e6b-183">The following result shows three quotes deleted with the log scope message included:</span></span>

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

<span data-ttu-id="c0e6b-184"><xref:Microsoft.Extensions.Logging.LoggerMessage>funkcje tworzą buforowalne delegatów, które wymagają mniej alokacji obiektów i zmniejszenie <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>obciążenia obliczeniowego w porównaniu do metod [rozszerzenia rejestratora,](xref:Microsoft.Extensions.Logging.LoggerExtensions)takich jak i .</span><span class="sxs-lookup"><span data-stu-id="c0e6b-184"><xref:Microsoft.Extensions.Logging.LoggerMessage> features create cacheable delegates that require fewer object allocations and reduced computational overhead compared to [logger extension methods](xref:Microsoft.Extensions.Logging.LoggerExtensions), such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>.</span></span> <span data-ttu-id="c0e6b-185">W przypadku scenariuszy rejestrowania o <xref:Microsoft.Extensions.Logging.LoggerMessage> wysokiej wydajności należy użyć wzorca.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-185">For high-performance logging scenarios, use the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

<span data-ttu-id="c0e6b-186"><xref:Microsoft.Extensions.Logging.LoggerMessage>zapewnia następujące korzyści wydajności w stosunku do metod rozszerzenia Rejestratora:</span><span class="sxs-lookup"><span data-stu-id="c0e6b-186"><xref:Microsoft.Extensions.Logging.LoggerMessage> provides the following performance advantages over Logger extension methods:</span></span>

* <span data-ttu-id="c0e6b-187">Metody rozszerzenia rejestratora wymagają "boksowania" (konwersji) typów wartości, takich jak `int`. `object`</span><span class="sxs-lookup"><span data-stu-id="c0e6b-187">Logger extension methods require "boxing" (converting) value types, such as `int`, into `object`.</span></span> <span data-ttu-id="c0e6b-188">Wzorzec <xref:Microsoft.Extensions.Logging.LoggerMessage> unika boksu <xref:System.Action> przy użyciu pól statycznych i metod rozszerzenia z parametrami silnie typizowane.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-188">The <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern avoids boxing by using static <xref:System.Action> fields and extension methods with strongly-typed parameters.</span></span>
* <span data-ttu-id="c0e6b-189">Metody rozszerzenia rejestratora należy przeanalizować szablon wiadomości (ciąg formatu o nazwie) za każdym razem, gdy jest zapisywany komunikat dziennika.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-189">Logger extension methods must parse the message template (named format string) every time a log message is written.</span></span> <span data-ttu-id="c0e6b-190"><xref:Microsoft.Extensions.Logging.LoggerMessage>wymaga analizowania szablonu tylko raz, gdy wiadomość jest zdefiniowana.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-190"><xref:Microsoft.Extensions.Logging.LoggerMessage> only requires parsing a template once when the message is defined.</span></span>

<span data-ttu-id="c0e6b-191">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c0e6b-191">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="c0e6b-192">Przykładowa aplikacja <xref:Microsoft.Extensions.Logging.LoggerMessage> pokazuje funkcje za pomocą podstawowego systemu śledzenia ofert.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-192">The sample app demonstrates <xref:Microsoft.Extensions.Logging.LoggerMessage> features with a basic quote tracking system.</span></span> <span data-ttu-id="c0e6b-193">Aplikacja dodaje i usuwa cudzysłowy przy użyciu bazy danych w pamięci.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-193">The app adds and deletes quotes using an in-memory database.</span></span> <span data-ttu-id="c0e6b-194">W miarę występowania tych operacji <xref:Microsoft.Extensions.Logging.LoggerMessage> komunikaty dziennika są generowane przy użyciu wzorca.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-194">As these operations occur, log messages are generated using the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

## <a name="loggermessagedefine"></a><span data-ttu-id="c0e6b-195">LoggerMessage.Define</span><span class="sxs-lookup"><span data-stu-id="c0e6b-195">LoggerMessage.Define</span></span>

<span data-ttu-id="c0e6b-196">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) tworzy <xref:System.Action> pełnomocnika do rejestrowania wiadomości.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-196">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) creates an <xref:System.Action> delegate for logging a message.</span></span> <span data-ttu-id="c0e6b-197"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*>przeciążenia umożliwiają przekazywanie maksymalnie sześciu parametrów typu do ciągu o nazwie formatu (szablonu).</span><span class="sxs-lookup"><span data-stu-id="c0e6b-197"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> overloads permit passing up to six type parameters to a named format string (template).</span></span>

<span data-ttu-id="c0e6b-198">Ciąg dostarczony do <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> metody jest szablonem, a nie interpolowanym ciągiem.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-198">The string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="c0e6b-199">Symbole zastępcze są wypełniane w kolejności, w określonej kolejności.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-199">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="c0e6b-200">Nazwy symboli zastępczych w szablonie powinny być opisowe i spójne w szablonach.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-200">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="c0e6b-201">Służą one jako nazwy właściwości w danych dziennika strukturalnego.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-201">They serve as property names within structured log data.</span></span> <span data-ttu-id="c0e6b-202">Zalecamy [pascalową obudowę](/dotnet/standard/design-guidelines/capitalization-conventions) dla nazw symboli zastępczych.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-202">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="c0e6b-203">Na przykład `{Count}` `{FirstName}`, .</span><span class="sxs-lookup"><span data-stu-id="c0e6b-203">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="c0e6b-204">Każdy komunikat dziennika <xref:System.Action> jest przechowywany w polu statycznym utworzonym przez [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span><span class="sxs-lookup"><span data-stu-id="c0e6b-204">Each log message is an <xref:System.Action> held in a static field created by [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span></span> <span data-ttu-id="c0e6b-205">Na przykład przykładowa aplikacja tworzy pole opisujące komunikat dziennika dla żądania GET dla strony Indeks (*Internal/LoggerExtensions.cs*):</span><span class="sxs-lookup"><span data-stu-id="c0e6b-205">For example, the sample app creates a field to describe a log message for a GET request for the Index page (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet1)]

<span data-ttu-id="c0e6b-206">Dla <xref:System.Action>, określić:</span><span class="sxs-lookup"><span data-stu-id="c0e6b-206">For the <xref:System.Action>, specify:</span></span>

* <span data-ttu-id="c0e6b-207">Poziom dziennika.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-207">The log level.</span></span>
* <span data-ttu-id="c0e6b-208">Unikatowy identyfikator zdarzenia<xref:Microsoft.Extensions.Logging.EventId>( ) z nazwą metody rozszerzenia statycznego.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-208">A unique event identifier (<xref:Microsoft.Extensions.Logging.EventId>) with the name of the static extension method.</span></span>
* <span data-ttu-id="c0e6b-209">Szablon wiadomości (ciąg formatu o nazwie).</span><span class="sxs-lookup"><span data-stu-id="c0e6b-209">The message template (named format string).</span></span> 

<span data-ttu-id="c0e6b-210">Żądanie dla indeksu strony przykładowej aplikacji ustawia:</span><span class="sxs-lookup"><span data-stu-id="c0e6b-210">A request for the Index page of the sample app sets the:</span></span>

* <span data-ttu-id="c0e6b-211">Poziom dziennika `Information`do .</span><span class="sxs-lookup"><span data-stu-id="c0e6b-211">Log level to `Information`.</span></span>
* <span data-ttu-id="c0e6b-212">Identyfikator zdarzenia `1` z nazwą `IndexPageRequested` metody.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-212">Event id to `1` with the name of the `IndexPageRequested` method.</span></span>
* <span data-ttu-id="c0e6b-213">Szablon wiadomości (nazwany ciąg formatu) do ciągu.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-213">Message template (named format string) to a string.</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet5)]

<span data-ttu-id="c0e6b-214">Magazyny rejestrowania strukturalnego mogą używać nazwy zdarzenia, gdy jest ona dostarczana z identyfikatorem zdarzenia, aby wzbogacić rejestrowanie.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-214">Structured logging stores may use the event name when it's supplied with the event id to enrich logging.</span></span> <span data-ttu-id="c0e6b-215">Na przykład [Serilog](https://github.com/serilog/serilog-extensions-logging) używa nazwy zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-215">For example, [Serilog](https://github.com/serilog/serilog-extensions-logging) uses the event name.</span></span>

<span data-ttu-id="c0e6b-216">Jest <xref:System.Action> wywoływana za pomocą metody rozszerzenia silnie typizowane.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-216">The <xref:System.Action> is invoked through a strongly-typed extension method.</span></span> <span data-ttu-id="c0e6b-217">Metoda `IndexPageRequested` rejestruje komunikat dla żądania GET strony indeksu w przykładowej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="c0e6b-217">The `IndexPageRequested` method logs a message for an Index page GET request in the sample app:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet9)]

<span data-ttu-id="c0e6b-218">`IndexPageRequested`jest wywoływana na rejestratorze w metodzie `OnGetAsync` w *Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="c0e6b-218">`IndexPageRequested` is called on the logger in the `OnGetAsync` method in *Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

<span data-ttu-id="c0e6b-219">Sprawdź dane wyjściowe konsoli aplikacji:</span><span class="sxs-lookup"><span data-stu-id="c0e6b-219">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

<span data-ttu-id="c0e6b-220">Aby przekazać parametry do komunikatu dziennika, należy zdefiniować maksymalnie sześć typów podczas tworzenia pola statycznego.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-220">To pass parameters to a log message, define up to six types when creating the static field.</span></span> <span data-ttu-id="c0e6b-221">Przykładowa aplikacja rejestruje ciąg podczas dodawania cudzysłowu, `string` definiując typ <xref:System.Action> dla pola:</span><span class="sxs-lookup"><span data-stu-id="c0e6b-221">The sample app logs a string when adding a quote by defining a `string` type for the <xref:System.Action> field:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet2)]

<span data-ttu-id="c0e6b-222">Szablon komunikatu dziennika pełnomocnika odbiera jego wartości zastępcze z podanych typów.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-222">The delegate's log message template receives its placeholder values from the types provided.</span></span> <span data-ttu-id="c0e6b-223">Przykładowa aplikacja definiuje pełnomocnika do dodawania oferty, `string`gdzie parametr oferty jest:</span><span class="sxs-lookup"><span data-stu-id="c0e6b-223">The sample app defines a delegate for adding a quote where the quote parameter is a `string`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet6)]

<span data-ttu-id="c0e6b-224">Metoda rozszerzenia statycznego do dodawania oferty, `QuoteAdded`otrzymuje wartość argumentu <xref:System.Action> oferty i przekazuje ją do delegata:</span><span class="sxs-lookup"><span data-stu-id="c0e6b-224">The static extension method for adding a quote, `QuoteAdded`, receives the quote argument value and passes it to the <xref:System.Action> delegate:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet10)]

<span data-ttu-id="c0e6b-225">W modelu strony indeksu *(Pages/Index.cshtml.cs)* `QuoteAdded` jest wywoływana w celu zarejestrowania wiadomości:</span><span class="sxs-lookup"><span data-stu-id="c0e6b-225">In the Index page's page model (*Pages/Index.cshtml.cs*), `QuoteAdded` is called to log the message:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

<span data-ttu-id="c0e6b-226">Sprawdź dane wyjściowe konsoli aplikacji:</span><span class="sxs-lookup"><span data-stu-id="c0e6b-226">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand')
```

<span data-ttu-id="c0e6b-227">Przykładowa aplikacja implementuje wzorzec [try&ndash;catch](/dotnet/csharp/language-reference/keywords/try-catch) do usunięcia oferty.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-227">The sample app implements a [try&ndash;catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern for quote deletion.</span></span> <span data-ttu-id="c0e6b-228">Komunikat informacyjny jest rejestrowany dla pomyślnej operacji usuwania.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-228">An informational message is logged for a successful delete operation.</span></span> <span data-ttu-id="c0e6b-229">Komunikat o błędzie jest rejestrowany dla operacji usuwania, gdy wyjątek.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-229">An error message is logged for a delete operation when an exception is thrown.</span></span> <span data-ttu-id="c0e6b-230">Komunikat dziennika dla operacji usuwania nieudanego zawiera śledzenie stosu*wyjątków (Internal/LoggerExtensions.cs*):</span><span class="sxs-lookup"><span data-stu-id="c0e6b-230">The log message for the unsuccessful delete operation includes the exception stack trace (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet7)]

<span data-ttu-id="c0e6b-231">Zwróć uwagę, jak wyjątek `QuoteDeleteFailed`jest przekazywany do pełnomocnika w:</span><span class="sxs-lookup"><span data-stu-id="c0e6b-231">Note how the exception is passed to the delegate in `QuoteDeleteFailed`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet11)]

<span data-ttu-id="c0e6b-232">W modelu strony dla indeksu strony pomyślne usunięcie `QuoteDeleted` oferty wywołuje metodę na rejestratorze.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-232">In the page model for the Index page, a successful quote deletion calls the `QuoteDeleted` method on the logger.</span></span> <span data-ttu-id="c0e6b-233">Gdy cytat nie zostanie znaleziony do usunięcia, jest wyrzucany. <xref:System.ArgumentNullException></span><span class="sxs-lookup"><span data-stu-id="c0e6b-233">When a quote isn't found for deletion, an <xref:System.ArgumentNullException> is thrown.</span></span> <span data-ttu-id="c0e6b-234">Wyjątek jest zalewkowany przez [try&ndash;catch](/dotnet/csharp/language-reference/keywords/try-catch) instrukcji `QuoteDeleteFailed` i rejestrowane przez wywołanie metody na rejestratora w bloku [catch](/dotnet/csharp/language-reference/keywords/try-catch) *(Pages/Index.cshtml.cs):*</span><span class="sxs-lookup"><span data-stu-id="c0e6b-234">The exception is trapped by the [try&ndash;catch](/dotnet/csharp/language-reference/keywords/try-catch) statement and logged by calling the `QuoteDeleteFailed` method on the logger in the [catch](/dotnet/csharp/language-reference/keywords/try-catch) block (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet5&highlight=14,18)]

<span data-ttu-id="c0e6b-235">Po pomyślnym usunięciu oferty sprawdź dane wyjściowe konsoli aplikacji:</span><span class="sxs-lookup"><span data-stu-id="c0e6b-235">When a quote is successfully deleted, inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand' Id = 1)
```

<span data-ttu-id="c0e6b-236">Gdy usunięcie oferty nie powiedzie się, sprawdź dane wyjściowe konsoli aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-236">When quote deletion fails, inspect the app's console output.</span></span> <span data-ttu-id="c0e6b-237">Należy zauważyć, że wyjątek znajduje się w komunikacie dziennika:</span><span class="sxs-lookup"><span data-stu-id="c0e6b-237">Note that the exception is included in the log message:</span></span>

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

## <a name="loggermessagedefinescope"></a><span data-ttu-id="c0e6b-238">LoggerMessage.DefineScope</span><span class="sxs-lookup"><span data-stu-id="c0e6b-238">LoggerMessage.DefineScope</span></span>

<span data-ttu-id="c0e6b-239">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) tworzy <xref:System.Func%601> pełnomocnika do definiowania [zakresu dziennika](xref:fundamentals/logging/index#log-scopes).</span><span class="sxs-lookup"><span data-stu-id="c0e6b-239">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) creates a <xref:System.Func%601> delegate for defining a [log scope](xref:fundamentals/logging/index#log-scopes).</span></span> <span data-ttu-id="c0e6b-240"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*>przeciążenia umożliwiają przekazywanie maksymalnie trzech parametrów typu do ciągu o nazwie formatu (szablonu).</span><span class="sxs-lookup"><span data-stu-id="c0e6b-240"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> overloads permit passing up to three type parameters to a named format string (template).</span></span>

<span data-ttu-id="c0e6b-241">Podobnie jak w <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> przypadku metody, ciąg <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> dostarczony do metody jest szablonem, a nie interpolowanym ciągiem.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-241">As is the case with the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method, the string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="c0e6b-242">Symbole zastępcze są wypełniane w kolejności, w określonej kolejności.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-242">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="c0e6b-243">Nazwy symboli zastępczych w szablonie powinny być opisowe i spójne w szablonach.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-243">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="c0e6b-244">Służą one jako nazwy właściwości w danych dziennika strukturalnego.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-244">They serve as property names within structured log data.</span></span> <span data-ttu-id="c0e6b-245">Zalecamy [pascalową obudowę](/dotnet/standard/design-guidelines/capitalization-conventions) dla nazw symboli zastępczych.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-245">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="c0e6b-246">Na przykład `{Count}` `{FirstName}`, .</span><span class="sxs-lookup"><span data-stu-id="c0e6b-246">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="c0e6b-247">Zdefiniuj [zakres dziennika,](xref:fundamentals/logging/index#log-scopes) aby zastosować <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> do serii komunikatów dziennika przy użyciu metody.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-247">Define a [log scope](xref:fundamentals/logging/index#log-scopes) to apply to a series of log messages using the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method.</span></span>

<span data-ttu-id="c0e6b-248">Przykładowa aplikacja ma przycisk **Wyczyść wszystko** do usuwania wszystkich cudzysłowów w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-248">The sample app has a **Clear All** button for deleting all of the quotes in the database.</span></span> <span data-ttu-id="c0e6b-249">Cudzysłowy są usuwane przez usunięcie ich po jednym na raz.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-249">The quotes are deleted by removing them one at a time.</span></span> <span data-ttu-id="c0e6b-250">Za każdym razem, gdy `QuoteDeleted` cytat jest usuwany, metoda jest wywoływana na rejestratorze.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-250">Each time a quote is deleted, the `QuoteDeleted` method is called on the logger.</span></span> <span data-ttu-id="c0e6b-251">Zakres dziennika jest dodawany do tych komunikatów dziennika.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-251">A log scope is added to these log messages.</span></span>

<span data-ttu-id="c0e6b-252">Włącz `IncludeScopes` w sekcji rejestrator konsoli *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="c0e6b-252">Enable `IncludeScopes` in the console logger section of *appsettings.json*:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/appsettings.json?highlight=3-5)]

<span data-ttu-id="c0e6b-253">Aby utworzyć zakres dziennika, dodaj pole <xref:System.Func%601> do przechowywania pełnomocnika dla zakresu.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-253">To create a log scope, add a field to hold a <xref:System.Func%601> delegate for the scope.</span></span> <span data-ttu-id="c0e6b-254">Przykładowa aplikacja tworzy `_allQuotesDeletedScope` pole o nazwie *(Internal/LoggerExtensions.cs):*</span><span class="sxs-lookup"><span data-stu-id="c0e6b-254">The sample app creates a field called `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet4)]

<span data-ttu-id="c0e6b-255">Służy <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> do tworzenia pełnomocnika.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-255">Use <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> to create the delegate.</span></span> <span data-ttu-id="c0e6b-256">Do trzech typów można określić do użycia jako argumenty szablonu, gdy jest wywoływany delegat.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-256">Up to three types can be specified for use as template arguments when the delegate is invoked.</span></span> <span data-ttu-id="c0e6b-257">Przykładowa aplikacja używa szablonu wiadomości, który zawiera liczbę `int` usuniętych ofert (typ):</span><span class="sxs-lookup"><span data-stu-id="c0e6b-257">The sample app uses a message template that includes the number of deleted quotes (an `int` type):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet8)]

<span data-ttu-id="c0e6b-258">Podaj metodę rozszerzenia statycznego dla komunikatu dziennika.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-258">Provide a static extension method for the log message.</span></span> <span data-ttu-id="c0e6b-259">Dołącz wszystkie parametry typu dla nazwanych właściwości, które pojawiają się w szablonie wiadomości.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-259">Include any type parameters for named properties that appear in the message template.</span></span> <span data-ttu-id="c0e6b-260">Przykładowa aplikacja przyjmuje `count` w cudzysłowie, aby usunąć i zwraca: `_allQuotesDeletedScope`</span><span class="sxs-lookup"><span data-stu-id="c0e6b-260">The sample app takes in a `count` of quotes to delete and returns `_allQuotesDeletedScope`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet12)]

<span data-ttu-id="c0e6b-261">Zakres zawija wywołania rozszerzenia rejestrowania w [using](/dotnet/csharp/language-reference/keywords/using-statement) bloku:</span><span class="sxs-lookup"><span data-stu-id="c0e6b-261">The scope wraps the logging extension calls in a [using](/dotnet/csharp/language-reference/keywords/using-statement) block:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

<span data-ttu-id="c0e6b-262">Sprawdź komunikaty dziennika w danych wyjściowych konsoli aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c0e6b-262">Inspect the log messages in the app's console output.</span></span> <span data-ttu-id="c0e6b-263">Poniższy wynik pokazuje trzy cudzysłowy usunięte z komunikatem zakresu dziennika:</span><span class="sxs-lookup"><span data-stu-id="c0e6b-263">The following result shows three quotes deleted with the log scope message included:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="c0e6b-264">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="c0e6b-264">Additional resources</span></span>

* [<span data-ttu-id="c0e6b-265">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="c0e6b-265">Logging</span></span>](xref:fundamentals/logging/index)
