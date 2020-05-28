---
<span data-ttu-id="e1d8d-101">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="e1d8d-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e1d8d-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e1d8d-102">'Blazor'</span></span>
- <span data-ttu-id="e1d8d-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e1d8d-103">'Identity'</span></span>
- <span data-ttu-id="e1d8d-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e1d8d-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="e1d8d-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e1d8d-105">'Razor'</span></span>
- <span data-ttu-id="e1d8d-106">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="e1d8d-106">'SignalR' uid:</span></span> 

---
# <a name="high-performance-logging-with-loggermessage-in-aspnet-core"></a><span data-ttu-id="e1d8d-107">Rejestrowanie o wysokiej wydajności za pomocą LoggerMessage w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e1d8d-107">High-performance logging with LoggerMessage in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e1d8d-108"><xref:Microsoft.Extensions.Logging.LoggerMessage>funkcje tworzą delegatów z pamięcią podręczną, którzy wymagają mniejszej liczby alokacji obiektów i zmniejszonego obciążenia obliczeniowego w porównaniu z [metodami rozszerzenia rejestratora](xref:Microsoft.Extensions.Logging.LoggerExtensions), takimi jak <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> i <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*> .</span><span class="sxs-lookup"><span data-stu-id="e1d8d-108"><xref:Microsoft.Extensions.Logging.LoggerMessage> features create cacheable delegates that require fewer object allocations and reduced computational overhead compared to [logger extension methods](xref:Microsoft.Extensions.Logging.LoggerExtensions), such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>.</span></span> <span data-ttu-id="e1d8d-109">W przypadku scenariuszy rejestrowania o wysokiej wydajności Użyj <xref:Microsoft.Extensions.Logging.LoggerMessage> wzorca.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-109">For high-performance logging scenarios, use the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

<span data-ttu-id="e1d8d-110"><xref:Microsoft.Extensions.Logging.LoggerMessage>zapewnia następujące korzyści wynikające z wydajności w porównaniu z metodami rozszerzenia rejestratora:</span><span class="sxs-lookup"><span data-stu-id="e1d8d-110"><xref:Microsoft.Extensions.Logging.LoggerMessage> provides the following performance advantages over Logger extension methods:</span></span>

* <span data-ttu-id="e1d8d-111">Metody rozszerzenia rejestratora wymagają "opakowania" (do konwersji) typów wartości, takich jak `int` , do `object` .</span><span class="sxs-lookup"><span data-stu-id="e1d8d-111">Logger extension methods require "boxing" (converting) value types, such as `int`, into `object`.</span></span> <span data-ttu-id="e1d8d-112"><xref:Microsoft.Extensions.Logging.LoggerMessage>Wzorzec unika pakowania przy użyciu <xref:System.Action> pól statycznych i metod rozszerzających z parametrami o jednoznacznie określonym typie.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-112">The <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern avoids boxing by using static <xref:System.Action> fields and extension methods with strongly-typed parameters.</span></span>
* <span data-ttu-id="e1d8d-113">Metody rozszerzenia rejestratora muszą analizować szablon wiadomości (nazwanego ciągu formatu) za każdym razem, gdy zostanie zapisany komunikat dziennika.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-113">Logger extension methods must parse the message template (named format string) every time a log message is written.</span></span> <span data-ttu-id="e1d8d-114"><xref:Microsoft.Extensions.Logging.LoggerMessage>tylko wymaga analizy szablonu tylko raz, gdy komunikat jest zdefiniowany.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-114"><xref:Microsoft.Extensions.Logging.LoggerMessage> only requires parsing a template once when the message is defined.</span></span>

<span data-ttu-id="e1d8d-115">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e1d8d-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="e1d8d-116">Przykładowa aplikacja pokazuje <xref:Microsoft.Extensions.Logging.LoggerMessage> funkcje z podstawowym systemem śledzenia ofert.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-116">The sample app demonstrates <xref:Microsoft.Extensions.Logging.LoggerMessage> features with a basic quote tracking system.</span></span> <span data-ttu-id="e1d8d-117">Aplikacja dodaje i usuwa oferty przy użyciu bazy danych w pamięci.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-117">The app adds and deletes quotes using an in-memory database.</span></span> <span data-ttu-id="e1d8d-118">W przypadku wystąpienia tych operacji komunikaty dziennika są generowane przy użyciu <xref:Microsoft.Extensions.Logging.LoggerMessage> wzorca.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-118">As these operations occur, log messages are generated using the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

## <a name="loggermessagedefine"></a><span data-ttu-id="e1d8d-119">LoggerMessage. define</span><span class="sxs-lookup"><span data-stu-id="e1d8d-119">LoggerMessage.Define</span></span>

<span data-ttu-id="e1d8d-120">[Zdefiniuj (LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) tworzy <xref:System.Action> delegata do rejestrowania wiadomości.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-120">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) creates an <xref:System.Action> delegate for logging a message.</span></span> <span data-ttu-id="e1d8d-121"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*>przeciążenia dopuszczają przekazywanie do sześciu parametrów typu do nazwanego ciągu formatu (szablonu).</span><span class="sxs-lookup"><span data-stu-id="e1d8d-121"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> overloads permit passing up to six type parameters to a named format string (template).</span></span>

<span data-ttu-id="e1d8d-122">Ciąg dostarczony do <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> metody jest szablonem, a nie ciągiem interpolowanym.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-122">The string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="e1d8d-123">Symbole zastępcze są wypełniane w kolejności, w jakiej są określone typy.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-123">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="e1d8d-124">Nazwy symboli zastępczych w szablonie powinny być opisowe i spójne w szablonach.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-124">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="e1d8d-125">Służą one jako nazwy właściwości w danych dziennika strukturalnego.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-125">They serve as property names within structured log data.</span></span> <span data-ttu-id="e1d8d-126">Zalecamy używanie [wielkości liter](/dotnet/standard/design-guidelines/capitalization-conventions) w języku Pascal dla nazw zastępczych.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-126">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="e1d8d-127">Na przykład `{Count}` , `{FirstName}` .</span><span class="sxs-lookup"><span data-stu-id="e1d8d-127">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="e1d8d-128">Każdy komunikat dziennika jest <xref:System.Action> przechowywany w polu statycznym utworzonym przez [LoggerMessage. define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span><span class="sxs-lookup"><span data-stu-id="e1d8d-128">Each log message is an <xref:System.Action> held in a static field created by [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span></span> <span data-ttu-id="e1d8d-129">Przykładowo Przykładowa aplikacja tworzy pole opisujące komunikat dziennika dla żądania GET dla strony indeksu (*Internal/LoggerExtensions. cs*):</span><span class="sxs-lookup"><span data-stu-id="e1d8d-129">For example, the sample app creates a field to describe a log message for a GET request for the Index page (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet1)]

<span data-ttu-id="e1d8d-130">Dla <xref:System.Action> , określ:</span><span class="sxs-lookup"><span data-stu-id="e1d8d-130">For the <xref:System.Action>, specify:</span></span>

* <span data-ttu-id="e1d8d-131">Poziom dziennika.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-131">The log level.</span></span>
* <span data-ttu-id="e1d8d-132">Unikatowy identyfikator zdarzenia ( <xref:Microsoft.Extensions.Logging.EventId> ) z nazwą statycznej metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-132">A unique event identifier (<xref:Microsoft.Extensions.Logging.EventId>) with the name of the static extension method.</span></span>
* <span data-ttu-id="e1d8d-133">Szablon wiadomości (nazwany ciąg formatu).</span><span class="sxs-lookup"><span data-stu-id="e1d8d-133">The message template (named format string).</span></span> 

<span data-ttu-id="e1d8d-134">Żądanie dotyczące strony indeksu przykładowej aplikacji ustawia:</span><span class="sxs-lookup"><span data-stu-id="e1d8d-134">A request for the Index page of the sample app sets the:</span></span>

* <span data-ttu-id="e1d8d-135">Poziom rejestrowania do `Information` .</span><span class="sxs-lookup"><span data-stu-id="e1d8d-135">Log level to `Information`.</span></span>
* <span data-ttu-id="e1d8d-136">Identyfikator zdarzenia do `1` nazwy `IndexPageRequested` metody.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-136">Event id to `1` with the name of the `IndexPageRequested` method.</span></span>
* <span data-ttu-id="e1d8d-137">Szablon wiadomości (nazwany ciąg formatu) do ciągu.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-137">Message template (named format string) to a string.</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet5)]

<span data-ttu-id="e1d8d-138">Magazyny rejestrowania strukturalnego mogą używać nazwy zdarzenia, gdy jest ona dostarczana z identyfikatorem zdarzenia w celu wzbogacania rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-138">Structured logging stores may use the event name when it's supplied with the event id to enrich logging.</span></span> <span data-ttu-id="e1d8d-139">Na przykład [Serilog](https://github.com/serilog/serilog-extensions-logging) używa nazwy zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-139">For example, [Serilog](https://github.com/serilog/serilog-extensions-logging) uses the event name.</span></span>

<span data-ttu-id="e1d8d-140"><xref:System.Action>Jest wywoływany za pomocą metody rozszerzającej o jednoznacznie określonym typie.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-140">The <xref:System.Action> is invoked through a strongly-typed extension method.</span></span> <span data-ttu-id="e1d8d-141">`IndexPageRequested`Metoda rejestruje komunikat dla strony indeksu żądania GET w przykładowej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="e1d8d-141">The `IndexPageRequested` method logs a message for an Index page GET request in the sample app:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet9)]

<span data-ttu-id="e1d8d-142">`IndexPageRequested`jest wywoływana dla rejestratora w `OnGetAsync` metodzie w *Pages/index. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="e1d8d-142">`IndexPageRequested` is called on the logger in the `OnGetAsync` method in *Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

<span data-ttu-id="e1d8d-143">Sprawdź dane wyjściowe konsoli aplikacji:</span><span class="sxs-lookup"><span data-stu-id="e1d8d-143">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

<span data-ttu-id="e1d8d-144">Aby przekazać parametry do komunikatu dziennika, zdefiniuj maksymalnie sześć typów podczas tworzenia pola statycznego.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-144">To pass parameters to a log message, define up to six types when creating the static field.</span></span> <span data-ttu-id="e1d8d-145">Przykładowa aplikacja rejestruje ciąg podczas dodawania oferty przez zdefiniowanie `string` typu dla <xref:System.Action> pola:</span><span class="sxs-lookup"><span data-stu-id="e1d8d-145">The sample app logs a string when adding a quote by defining a `string` type for the <xref:System.Action> field:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet2)]

<span data-ttu-id="e1d8d-146">Szablon komunikatu dziennika delegata otrzymuje wartości zastępcze z dostarczonych typów.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-146">The delegate's log message template receives its placeholder values from the types provided.</span></span> <span data-ttu-id="e1d8d-147">Przykładowa aplikacja definiuje delegata do dodawania oferty, w której parametr Quote jest `string` :</span><span class="sxs-lookup"><span data-stu-id="e1d8d-147">The sample app defines a delegate for adding a quote where the quote parameter is a `string`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet6)]

<span data-ttu-id="e1d8d-148">Statyczna metoda rozszerzenia służąca do dodawania oferty, `QuoteAdded` , odbiera wartość argumentu cudzysłowu i przekazuje ją do <xref:System.Action> delegata:</span><span class="sxs-lookup"><span data-stu-id="e1d8d-148">The static extension method for adding a quote, `QuoteAdded`, receives the quote argument value and passes it to the <xref:System.Action> delegate:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet10)]

<span data-ttu-id="e1d8d-149">W modelu strony strony indeksu (*Pages/index. cshtml. cs*) `QuoteAdded` jest wywoływana w celu zarejestrowania komunikatu:</span><span class="sxs-lookup"><span data-stu-id="e1d8d-149">In the Index page's page model (*Pages/Index.cshtml.cs*), `QuoteAdded` is called to log the message:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

<span data-ttu-id="e1d8d-150">Sprawdź dane wyjściowe konsoli aplikacji:</span><span class="sxs-lookup"><span data-stu-id="e1d8d-150">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand')
```

<span data-ttu-id="e1d8d-151">Przykładowa aplikacja implementuje wzór [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) do usuwania oferty.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-151">The sample app implements a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern for quote deletion.</span></span> <span data-ttu-id="e1d8d-152">Komunikat informacyjny jest rejestrowany w przypadku pomyślnego usunięcia operacji usuwania.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-152">An informational message is logged for a successful delete operation.</span></span> <span data-ttu-id="e1d8d-153">Komunikat o błędzie jest rejestrowany dla operacji usuwania, gdy zostanie zgłoszony wyjątek.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-153">An error message is logged for a delete operation when an exception is thrown.</span></span> <span data-ttu-id="e1d8d-154">Komunikat dziennika dla nieprawidłowej operacji usuwania obejmuje ślad stosu wyjątku (*wewnętrzny/LoggerExtensions. cs*):</span><span class="sxs-lookup"><span data-stu-id="e1d8d-154">The log message for the unsuccessful delete operation includes the exception stack trace (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet7)]

<span data-ttu-id="e1d8d-155">Zwróć uwagę, jak wyjątek jest przesyłany do delegata w `QuoteDeleteFailed` :</span><span class="sxs-lookup"><span data-stu-id="e1d8d-155">Note how the exception is passed to the delegate in `QuoteDeleteFailed`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet11)]

<span data-ttu-id="e1d8d-156">W modelu strony dla strony indeksu pomyślne usunięcie cudzysłowu wywołuje `QuoteDeleted` metodę w rejestratorze.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-156">In the page model for the Index page, a successful quote deletion calls the `QuoteDeleted` method on the logger.</span></span> <span data-ttu-id="e1d8d-157">Gdy cytat nie zostanie znaleziony do usunięcia, <xref:System.ArgumentNullException> jest zgłaszany.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-157">When a quote isn't found for deletion, an <xref:System.ArgumentNullException> is thrown.</span></span> <span data-ttu-id="e1d8d-158">Wyjątek jest zalewkowany przez instrukcję [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) i rejestrowany przez wywołanie `QuoteDeleteFailed` metody w rejestratorze w bloku [catch](/dotnet/csharp/language-reference/keywords/try-catch) (*Pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="e1d8d-158">The exception is trapped by the [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement and logged by calling the `QuoteDeleteFailed` method on the logger in the [catch](/dotnet/csharp/language-reference/keywords/try-catch) block (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet5&highlight=9,13)]

<span data-ttu-id="e1d8d-159">Po pomyślnym usunięciu oferty Sprawdź dane wyjściowe konsoli aplikacji:</span><span class="sxs-lookup"><span data-stu-id="e1d8d-159">When a quote is successfully deleted, inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand' Id = 1)
```

<span data-ttu-id="e1d8d-160">Po niepowodzeniu usuwania oferty Sprawdź dane wyjściowe konsoli aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-160">When quote deletion fails, inspect the app's console output.</span></span> <span data-ttu-id="e1d8d-161">Należy zauważyć, że wyjątek jest zawarty w komunikacie dziennika:</span><span class="sxs-lookup"><span data-stu-id="e1d8d-161">Note that the exception is included in the log message:</span></span>

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

## <a name="loggermessagedefinescope"></a><span data-ttu-id="e1d8d-162">LoggerMessage. DefineScope —</span><span class="sxs-lookup"><span data-stu-id="e1d8d-162">LoggerMessage.DefineScope</span></span>

<span data-ttu-id="e1d8d-163">[DefineScope — (ciąg)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) tworzy <xref:System.Func%601> delegata do definiowania [zakresu dziennika](xref:fundamentals/logging/index#log-scopes).</span><span class="sxs-lookup"><span data-stu-id="e1d8d-163">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) creates a <xref:System.Func%601> delegate for defining a [log scope](xref:fundamentals/logging/index#log-scopes).</span></span> <span data-ttu-id="e1d8d-164"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*>przeciążenia dopuszczają przekazywanie do trzech parametrów typu do nazwanego ciągu formatu (szablonu).</span><span class="sxs-lookup"><span data-stu-id="e1d8d-164"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> overloads permit passing up to three type parameters to a named format string (template).</span></span>

<span data-ttu-id="e1d8d-165">Podobnie jak w przypadku <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> metody, ciąg dostarczony do <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> metody jest szablonem, a nie ciągiem interpolowanym.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-165">As is the case with the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method, the string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="e1d8d-166">Symbole zastępcze są wypełniane w kolejności, w jakiej są określone typy.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-166">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="e1d8d-167">Nazwy symboli zastępczych w szablonie powinny być opisowe i spójne w szablonach.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-167">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="e1d8d-168">Służą one jako nazwy właściwości w danych dziennika strukturalnego.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-168">They serve as property names within structured log data.</span></span> <span data-ttu-id="e1d8d-169">Zalecamy używanie [wielkości liter](/dotnet/standard/design-guidelines/capitalization-conventions) w języku Pascal dla nazw zastępczych.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-169">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="e1d8d-170">Na przykład `{Count}` , `{FirstName}` .</span><span class="sxs-lookup"><span data-stu-id="e1d8d-170">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="e1d8d-171">Zdefiniuj [zakres dziennika](xref:fundamentals/logging/index#log-scopes) , który ma zostać zastosowany do serii komunikatów dziennika przy użyciu <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> metody.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-171">Define a [log scope](xref:fundamentals/logging/index#log-scopes) to apply to a series of log messages using the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method.</span></span>

<span data-ttu-id="e1d8d-172">Przykładowa aplikacja ma przycisk **Wyczyść wszystko** , aby usunąć wszystkie cudzysłowy w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-172">The sample app has a **Clear All** button for deleting all of the quotes in the database.</span></span> <span data-ttu-id="e1d8d-173">Cudzysłowy są usuwane, usuwając je pojedynczo.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-173">The quotes are deleted by removing them one at a time.</span></span> <span data-ttu-id="e1d8d-174">Przy każdym usunięciu oferty `QuoteDeleted` Metoda jest wywoływana w rejestratorze.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-174">Each time a quote is deleted, the `QuoteDeleted` method is called on the logger.</span></span> <span data-ttu-id="e1d8d-175">Do tych komunikatów dziennika jest dodawany zakres dziennika.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-175">A log scope is added to these log messages.</span></span>

<span data-ttu-id="e1d8d-176">Włącz `IncludeScopes` w sekcji rejestratora konsoli pliku *appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="e1d8d-176">Enable `IncludeScopes` in the console logger section of *appsettings.json*:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/appsettings.json?highlight=3-5)]

<span data-ttu-id="e1d8d-177">Aby utworzyć zakres dziennika, należy dodać pole do przechowywania <xref:System.Func%601> delegata dla zakresu.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-177">To create a log scope, add a field to hold a <xref:System.Func%601> delegate for the scope.</span></span> <span data-ttu-id="e1d8d-178">Przykładowa aplikacja tworzy pole o nazwie `_allQuotesDeletedScope` (*wewnętrzne/LoggerExtensions. cs*):</span><span class="sxs-lookup"><span data-stu-id="e1d8d-178">The sample app creates a field called `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet4)]

<span data-ttu-id="e1d8d-179">Użyj, <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> Aby utworzyć delegata.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-179">Use <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> to create the delegate.</span></span> <span data-ttu-id="e1d8d-180">Można określić maksymalnie trzy typy do użycia jako argumenty szablonu, gdy obiekt delegowany jest wywoływany.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-180">Up to three types can be specified for use as template arguments when the delegate is invoked.</span></span> <span data-ttu-id="e1d8d-181">Przykładowa aplikacja używa szablonu komunikatu zawierającego liczbę usuniętych cudzysłowów ( `int` Typ):</span><span class="sxs-lookup"><span data-stu-id="e1d8d-181">The sample app uses a message template that includes the number of deleted quotes (an `int` type):</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet8)]

<span data-ttu-id="e1d8d-182">Podaj statyczną metodę rozszerzenia dla komunikatu dziennika.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-182">Provide a static extension method for the log message.</span></span> <span data-ttu-id="e1d8d-183">Dołącz wszystkie parametry typu dla nazwanych właściwości, które są wyświetlane w szablonie wiadomości.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-183">Include any type parameters for named properties that appear in the message template.</span></span> <span data-ttu-id="e1d8d-184">Przykładowa aplikacja wykonuje `count` cudzysłowy, aby usunąć i zwracać `_allQuotesDeletedScope` :</span><span class="sxs-lookup"><span data-stu-id="e1d8d-184">The sample app takes in a `count` of quotes to delete and returns `_allQuotesDeletedScope`:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet12)]

<span data-ttu-id="e1d8d-185">Zakres zawija wywołania rozszerzenia rejestrowania w bloku [using](/dotnet/csharp/language-reference/keywords/using-statement) :</span><span class="sxs-lookup"><span data-stu-id="e1d8d-185">The scope wraps the logging extension calls in a [using](/dotnet/csharp/language-reference/keywords/using-statement) block:</span></span>

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

<span data-ttu-id="e1d8d-186">Sprawdź komunikaty dziennika w danych wyjściowych konsoli aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-186">Inspect the log messages in the app's console output.</span></span> <span data-ttu-id="e1d8d-187">Poniższy wynik pokazuje trzy cudzysłowy usunięte z uwzględnieniem komunikatu o zakresie dziennika:</span><span class="sxs-lookup"><span data-stu-id="e1d8d-187">The following result shows three quotes deleted with the log scope message included:</span></span>

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

<span data-ttu-id="e1d8d-188"><xref:Microsoft.Extensions.Logging.LoggerMessage>funkcje tworzą delegatów z pamięcią podręczną, którzy wymagają mniejszej liczby alokacji obiektów i zmniejszonego obciążenia obliczeniowego w porównaniu z [metodami rozszerzenia rejestratora](xref:Microsoft.Extensions.Logging.LoggerExtensions), takimi jak <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> i <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*> .</span><span class="sxs-lookup"><span data-stu-id="e1d8d-188"><xref:Microsoft.Extensions.Logging.LoggerMessage> features create cacheable delegates that require fewer object allocations and reduced computational overhead compared to [logger extension methods](xref:Microsoft.Extensions.Logging.LoggerExtensions), such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>.</span></span> <span data-ttu-id="e1d8d-189">W przypadku scenariuszy rejestrowania o wysokiej wydajności Użyj <xref:Microsoft.Extensions.Logging.LoggerMessage> wzorca.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-189">For high-performance logging scenarios, use the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

<span data-ttu-id="e1d8d-190"><xref:Microsoft.Extensions.Logging.LoggerMessage>zapewnia następujące korzyści wynikające z wydajności w porównaniu z metodami rozszerzenia rejestratora:</span><span class="sxs-lookup"><span data-stu-id="e1d8d-190"><xref:Microsoft.Extensions.Logging.LoggerMessage> provides the following performance advantages over Logger extension methods:</span></span>

* <span data-ttu-id="e1d8d-191">Metody rozszerzenia rejestratora wymagają "opakowania" (do konwersji) typów wartości, takich jak `int` , do `object` .</span><span class="sxs-lookup"><span data-stu-id="e1d8d-191">Logger extension methods require "boxing" (converting) value types, such as `int`, into `object`.</span></span> <span data-ttu-id="e1d8d-192"><xref:Microsoft.Extensions.Logging.LoggerMessage>Wzorzec unika pakowania przy użyciu <xref:System.Action> pól statycznych i metod rozszerzających z parametrami o jednoznacznie określonym typie.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-192">The <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern avoids boxing by using static <xref:System.Action> fields and extension methods with strongly-typed parameters.</span></span>
* <span data-ttu-id="e1d8d-193">Metody rozszerzenia rejestratora muszą analizować szablon wiadomości (nazwanego ciągu formatu) za każdym razem, gdy zostanie zapisany komunikat dziennika.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-193">Logger extension methods must parse the message template (named format string) every time a log message is written.</span></span> <span data-ttu-id="e1d8d-194"><xref:Microsoft.Extensions.Logging.LoggerMessage>tylko wymaga analizy szablonu tylko raz, gdy komunikat jest zdefiniowany.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-194"><xref:Microsoft.Extensions.Logging.LoggerMessage> only requires parsing a template once when the message is defined.</span></span>

<span data-ttu-id="e1d8d-195">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e1d8d-195">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="e1d8d-196">Przykładowa aplikacja pokazuje <xref:Microsoft.Extensions.Logging.LoggerMessage> funkcje z podstawowym systemem śledzenia ofert.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-196">The sample app demonstrates <xref:Microsoft.Extensions.Logging.LoggerMessage> features with a basic quote tracking system.</span></span> <span data-ttu-id="e1d8d-197">Aplikacja dodaje i usuwa oferty przy użyciu bazy danych w pamięci.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-197">The app adds and deletes quotes using an in-memory database.</span></span> <span data-ttu-id="e1d8d-198">W przypadku wystąpienia tych operacji komunikaty dziennika są generowane przy użyciu <xref:Microsoft.Extensions.Logging.LoggerMessage> wzorca.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-198">As these operations occur, log messages are generated using the <xref:Microsoft.Extensions.Logging.LoggerMessage> pattern.</span></span>

## <a name="loggermessagedefine"></a><span data-ttu-id="e1d8d-199">LoggerMessage. define</span><span class="sxs-lookup"><span data-stu-id="e1d8d-199">LoggerMessage.Define</span></span>

<span data-ttu-id="e1d8d-200">[Zdefiniuj (LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) tworzy <xref:System.Action> delegata do rejestrowania wiadomości.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-200">[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) creates an <xref:System.Action> delegate for logging a message.</span></span> <span data-ttu-id="e1d8d-201"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*>przeciążenia dopuszczają przekazywanie do sześciu parametrów typu do nazwanego ciągu formatu (szablonu).</span><span class="sxs-lookup"><span data-stu-id="e1d8d-201"><xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> overloads permit passing up to six type parameters to a named format string (template).</span></span>

<span data-ttu-id="e1d8d-202">Ciąg dostarczony do <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> metody jest szablonem, a nie ciągiem interpolowanym.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-202">The string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="e1d8d-203">Symbole zastępcze są wypełniane w kolejności, w jakiej są określone typy.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-203">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="e1d8d-204">Nazwy symboli zastępczych w szablonie powinny być opisowe i spójne w szablonach.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-204">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="e1d8d-205">Służą one jako nazwy właściwości w danych dziennika strukturalnego.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-205">They serve as property names within structured log data.</span></span> <span data-ttu-id="e1d8d-206">Zalecamy używanie [wielkości liter](/dotnet/standard/design-guidelines/capitalization-conventions) w języku Pascal dla nazw zastępczych.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-206">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="e1d8d-207">Na przykład `{Count}` , `{FirstName}` .</span><span class="sxs-lookup"><span data-stu-id="e1d8d-207">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="e1d8d-208">Każdy komunikat dziennika jest <xref:System.Action> przechowywany w polu statycznym utworzonym przez [LoggerMessage. define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span><span class="sxs-lookup"><span data-stu-id="e1d8d-208">Each log message is an <xref:System.Action> held in a static field created by [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*).</span></span> <span data-ttu-id="e1d8d-209">Przykładowo Przykładowa aplikacja tworzy pole opisujące komunikat dziennika dla żądania GET dla strony indeksu (*Internal/LoggerExtensions. cs*):</span><span class="sxs-lookup"><span data-stu-id="e1d8d-209">For example, the sample app creates a field to describe a log message for a GET request for the Index page (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet1)]

<span data-ttu-id="e1d8d-210">Dla <xref:System.Action> , określ:</span><span class="sxs-lookup"><span data-stu-id="e1d8d-210">For the <xref:System.Action>, specify:</span></span>

* <span data-ttu-id="e1d8d-211">Poziom dziennika.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-211">The log level.</span></span>
* <span data-ttu-id="e1d8d-212">Unikatowy identyfikator zdarzenia ( <xref:Microsoft.Extensions.Logging.EventId> ) z nazwą statycznej metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-212">A unique event identifier (<xref:Microsoft.Extensions.Logging.EventId>) with the name of the static extension method.</span></span>
* <span data-ttu-id="e1d8d-213">Szablon wiadomości (nazwany ciąg formatu).</span><span class="sxs-lookup"><span data-stu-id="e1d8d-213">The message template (named format string).</span></span> 

<span data-ttu-id="e1d8d-214">Żądanie dotyczące strony indeksu przykładowej aplikacji ustawia:</span><span class="sxs-lookup"><span data-stu-id="e1d8d-214">A request for the Index page of the sample app sets the:</span></span>

* <span data-ttu-id="e1d8d-215">Poziom rejestrowania do `Information` .</span><span class="sxs-lookup"><span data-stu-id="e1d8d-215">Log level to `Information`.</span></span>
* <span data-ttu-id="e1d8d-216">Identyfikator zdarzenia do `1` nazwy `IndexPageRequested` metody.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-216">Event id to `1` with the name of the `IndexPageRequested` method.</span></span>
* <span data-ttu-id="e1d8d-217">Szablon wiadomości (nazwany ciąg formatu) do ciągu.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-217">Message template (named format string) to a string.</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet5)]

<span data-ttu-id="e1d8d-218">Magazyny rejestrowania strukturalnego mogą używać nazwy zdarzenia, gdy jest ona dostarczana z identyfikatorem zdarzenia w celu wzbogacania rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-218">Structured logging stores may use the event name when it's supplied with the event id to enrich logging.</span></span> <span data-ttu-id="e1d8d-219">Na przykład [Serilog](https://github.com/serilog/serilog-extensions-logging) używa nazwy zdarzenia.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-219">For example, [Serilog](https://github.com/serilog/serilog-extensions-logging) uses the event name.</span></span>

<span data-ttu-id="e1d8d-220"><xref:System.Action>Jest wywoływany za pomocą metody rozszerzającej o jednoznacznie określonym typie.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-220">The <xref:System.Action> is invoked through a strongly-typed extension method.</span></span> <span data-ttu-id="e1d8d-221">`IndexPageRequested`Metoda rejestruje komunikat dla strony indeksu żądania GET w przykładowej aplikacji:</span><span class="sxs-lookup"><span data-stu-id="e1d8d-221">The `IndexPageRequested` method logs a message for an Index page GET request in the sample app:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet9)]

<span data-ttu-id="e1d8d-222">`IndexPageRequested`jest wywoływana dla rejestratora w `OnGetAsync` metodzie w *Pages/index. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="e1d8d-222">`IndexPageRequested` is called on the logger in the `OnGetAsync` method in *Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

<span data-ttu-id="e1d8d-223">Sprawdź dane wyjściowe konsoli aplikacji:</span><span class="sxs-lookup"><span data-stu-id="e1d8d-223">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

<span data-ttu-id="e1d8d-224">Aby przekazać parametry do komunikatu dziennika, zdefiniuj maksymalnie sześć typów podczas tworzenia pola statycznego.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-224">To pass parameters to a log message, define up to six types when creating the static field.</span></span> <span data-ttu-id="e1d8d-225">Przykładowa aplikacja rejestruje ciąg podczas dodawania oferty przez zdefiniowanie `string` typu dla <xref:System.Action> pola:</span><span class="sxs-lookup"><span data-stu-id="e1d8d-225">The sample app logs a string when adding a quote by defining a `string` type for the <xref:System.Action> field:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet2)]

<span data-ttu-id="e1d8d-226">Szablon komunikatu dziennika delegata otrzymuje wartości zastępcze z dostarczonych typów.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-226">The delegate's log message template receives its placeholder values from the types provided.</span></span> <span data-ttu-id="e1d8d-227">Przykładowa aplikacja definiuje delegata do dodawania oferty, w której parametr Quote jest `string` :</span><span class="sxs-lookup"><span data-stu-id="e1d8d-227">The sample app defines a delegate for adding a quote where the quote parameter is a `string`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet6)]

<span data-ttu-id="e1d8d-228">Statyczna metoda rozszerzenia służąca do dodawania oferty, `QuoteAdded` , odbiera wartość argumentu cudzysłowu i przekazuje ją do <xref:System.Action> delegata:</span><span class="sxs-lookup"><span data-stu-id="e1d8d-228">The static extension method for adding a quote, `QuoteAdded`, receives the quote argument value and passes it to the <xref:System.Action> delegate:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet10)]

<span data-ttu-id="e1d8d-229">W modelu strony strony indeksu (*Pages/index. cshtml. cs*) `QuoteAdded` jest wywoływana w celu zarejestrowania komunikatu:</span><span class="sxs-lookup"><span data-stu-id="e1d8d-229">In the Index page's page model (*Pages/Index.cshtml.cs*), `QuoteAdded` is called to log the message:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

<span data-ttu-id="e1d8d-230">Sprawdź dane wyjściowe konsoli aplikacji:</span><span class="sxs-lookup"><span data-stu-id="e1d8d-230">Inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand')
```

<span data-ttu-id="e1d8d-231">Przykładowa aplikacja implementuje wzór [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) do usuwania oferty.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-231">The sample app implements a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern for quote deletion.</span></span> <span data-ttu-id="e1d8d-232">Komunikat informacyjny jest rejestrowany w przypadku pomyślnego usunięcia operacji usuwania.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-232">An informational message is logged for a successful delete operation.</span></span> <span data-ttu-id="e1d8d-233">Komunikat o błędzie jest rejestrowany dla operacji usuwania, gdy zostanie zgłoszony wyjątek.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-233">An error message is logged for a delete operation when an exception is thrown.</span></span> <span data-ttu-id="e1d8d-234">Komunikat dziennika dla nieprawidłowej operacji usuwania obejmuje ślad stosu wyjątku (*wewnętrzny/LoggerExtensions. cs*):</span><span class="sxs-lookup"><span data-stu-id="e1d8d-234">The log message for the unsuccessful delete operation includes the exception stack trace (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet7)]

<span data-ttu-id="e1d8d-235">Zwróć uwagę, jak wyjątek jest przesyłany do delegata w `QuoteDeleteFailed` :</span><span class="sxs-lookup"><span data-stu-id="e1d8d-235">Note how the exception is passed to the delegate in `QuoteDeleteFailed`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet11)]

<span data-ttu-id="e1d8d-236">W modelu strony dla strony indeksu pomyślne usunięcie cudzysłowu wywołuje `QuoteDeleted` metodę w rejestratorze.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-236">In the page model for the Index page, a successful quote deletion calls the `QuoteDeleted` method on the logger.</span></span> <span data-ttu-id="e1d8d-237">Gdy cytat nie zostanie znaleziony do usunięcia, <xref:System.ArgumentNullException> jest zgłaszany.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-237">When a quote isn't found for deletion, an <xref:System.ArgumentNullException> is thrown.</span></span> <span data-ttu-id="e1d8d-238">Wyjątek jest zalewkowany przez instrukcję [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) i rejestrowany przez wywołanie `QuoteDeleteFailed` metody w rejestratorze w bloku [catch](/dotnet/csharp/language-reference/keywords/try-catch) (*Pages/index. cshtml. cs*):</span><span class="sxs-lookup"><span data-stu-id="e1d8d-238">The exception is trapped by the [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement and logged by calling the `QuoteDeleteFailed` method on the logger in the [catch](/dotnet/csharp/language-reference/keywords/try-catch) block (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet5&highlight=14,18)]

<span data-ttu-id="e1d8d-239">Po pomyślnym usunięciu oferty Sprawdź dane wyjściowe konsoli aplikacji:</span><span class="sxs-lookup"><span data-stu-id="e1d8d-239">When a quote is successfully deleted, inspect the app's console output:</span></span>

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand' Id = 1)
```

<span data-ttu-id="e1d8d-240">Po niepowodzeniu usuwania oferty Sprawdź dane wyjściowe konsoli aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-240">When quote deletion fails, inspect the app's console output.</span></span> <span data-ttu-id="e1d8d-241">Należy zauważyć, że wyjątek jest zawarty w komunikacie dziennika:</span><span class="sxs-lookup"><span data-stu-id="e1d8d-241">Note that the exception is included in the log message:</span></span>

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

## <a name="loggermessagedefinescope"></a><span data-ttu-id="e1d8d-242">LoggerMessage. DefineScope —</span><span class="sxs-lookup"><span data-stu-id="e1d8d-242">LoggerMessage.DefineScope</span></span>

<span data-ttu-id="e1d8d-243">[DefineScope — (ciąg)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) tworzy <xref:System.Func%601> delegata do definiowania [zakresu dziennika](xref:fundamentals/logging/index#log-scopes).</span><span class="sxs-lookup"><span data-stu-id="e1d8d-243">[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) creates a <xref:System.Func%601> delegate for defining a [log scope](xref:fundamentals/logging/index#log-scopes).</span></span> <span data-ttu-id="e1d8d-244"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*>przeciążenia dopuszczają przekazywanie do trzech parametrów typu do nazwanego ciągu formatu (szablonu).</span><span class="sxs-lookup"><span data-stu-id="e1d8d-244"><xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> overloads permit passing up to three type parameters to a named format string (template).</span></span>

<span data-ttu-id="e1d8d-245">Podobnie jak w przypadku <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> metody, ciąg dostarczony do <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> metody jest szablonem, a nie ciągiem interpolowanym.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-245">As is the case with the <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> method, the string provided to the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method is a template and not an interpolated string.</span></span> <span data-ttu-id="e1d8d-246">Symbole zastępcze są wypełniane w kolejności, w jakiej są określone typy.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-246">Placeholders are filled in the order that the types are specified.</span></span> <span data-ttu-id="e1d8d-247">Nazwy symboli zastępczych w szablonie powinny być opisowe i spójne w szablonach.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-247">Placeholder names in the template should be descriptive and consistent across templates.</span></span> <span data-ttu-id="e1d8d-248">Służą one jako nazwy właściwości w danych dziennika strukturalnego.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-248">They serve as property names within structured log data.</span></span> <span data-ttu-id="e1d8d-249">Zalecamy używanie [wielkości liter](/dotnet/standard/design-guidelines/capitalization-conventions) w języku Pascal dla nazw zastępczych.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-249">We recommend [Pascal casing](/dotnet/standard/design-guidelines/capitalization-conventions) for placeholder names.</span></span> <span data-ttu-id="e1d8d-250">Na przykład `{Count}` , `{FirstName}` .</span><span class="sxs-lookup"><span data-stu-id="e1d8d-250">For example, `{Count}`, `{FirstName}`.</span></span>

<span data-ttu-id="e1d8d-251">Zdefiniuj [zakres dziennika](xref:fundamentals/logging/index#log-scopes) , który ma zostać zastosowany do serii komunikatów dziennika przy użyciu <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> metody.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-251">Define a [log scope](xref:fundamentals/logging/index#log-scopes) to apply to a series of log messages using the <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> method.</span></span>

<span data-ttu-id="e1d8d-252">Przykładowa aplikacja ma przycisk **Wyczyść wszystko** , aby usunąć wszystkie cudzysłowy w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-252">The sample app has a **Clear All** button for deleting all of the quotes in the database.</span></span> <span data-ttu-id="e1d8d-253">Cudzysłowy są usuwane, usuwając je pojedynczo.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-253">The quotes are deleted by removing them one at a time.</span></span> <span data-ttu-id="e1d8d-254">Przy każdym usunięciu oferty `QuoteDeleted` Metoda jest wywoływana w rejestratorze.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-254">Each time a quote is deleted, the `QuoteDeleted` method is called on the logger.</span></span> <span data-ttu-id="e1d8d-255">Do tych komunikatów dziennika jest dodawany zakres dziennika.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-255">A log scope is added to these log messages.</span></span>

<span data-ttu-id="e1d8d-256">Włącz `IncludeScopes` w sekcji rejestratora konsoli pliku *appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="e1d8d-256">Enable `IncludeScopes` in the console logger section of *appsettings.json*:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/appsettings.json?highlight=3-5)]

<span data-ttu-id="e1d8d-257">Aby utworzyć zakres dziennika, należy dodać pole do przechowywania <xref:System.Func%601> delegata dla zakresu.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-257">To create a log scope, add a field to hold a <xref:System.Func%601> delegate for the scope.</span></span> <span data-ttu-id="e1d8d-258">Przykładowa aplikacja tworzy pole o nazwie `_allQuotesDeletedScope` (*wewnętrzne/LoggerExtensions. cs*):</span><span class="sxs-lookup"><span data-stu-id="e1d8d-258">The sample app creates a field called `_allQuotesDeletedScope` (*Internal/LoggerExtensions.cs*):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet4)]

<span data-ttu-id="e1d8d-259">Użyj, <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> Aby utworzyć delegata.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-259">Use <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> to create the delegate.</span></span> <span data-ttu-id="e1d8d-260">Można określić maksymalnie trzy typy do użycia jako argumenty szablonu, gdy obiekt delegowany jest wywoływany.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-260">Up to three types can be specified for use as template arguments when the delegate is invoked.</span></span> <span data-ttu-id="e1d8d-261">Przykładowa aplikacja używa szablonu komunikatu zawierającego liczbę usuniętych cudzysłowów ( `int` Typ):</span><span class="sxs-lookup"><span data-stu-id="e1d8d-261">The sample app uses a message template that includes the number of deleted quotes (an `int` type):</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet8)]

<span data-ttu-id="e1d8d-262">Podaj statyczną metodę rozszerzenia dla komunikatu dziennika.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-262">Provide a static extension method for the log message.</span></span> <span data-ttu-id="e1d8d-263">Dołącz wszystkie parametry typu dla nazwanych właściwości, które są wyświetlane w szablonie wiadomości.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-263">Include any type parameters for named properties that appear in the message template.</span></span> <span data-ttu-id="e1d8d-264">Przykładowa aplikacja wykonuje `count` cudzysłowy, aby usunąć i zwracać `_allQuotesDeletedScope` :</span><span class="sxs-lookup"><span data-stu-id="e1d8d-264">The sample app takes in a `count` of quotes to delete and returns `_allQuotesDeletedScope`:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet12)]

<span data-ttu-id="e1d8d-265">Zakres zawija wywołania rozszerzenia rejestrowania w bloku [using](/dotnet/csharp/language-reference/keywords/using-statement) :</span><span class="sxs-lookup"><span data-stu-id="e1d8d-265">The scope wraps the logging extension calls in a [using](/dotnet/csharp/language-reference/keywords/using-statement) block:</span></span>

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

<span data-ttu-id="e1d8d-266">Sprawdź komunikaty dziennika w danych wyjściowych konsoli aplikacji.</span><span class="sxs-lookup"><span data-stu-id="e1d8d-266">Inspect the log messages in the app's console output.</span></span> <span data-ttu-id="e1d8d-267">Poniższy wynik pokazuje trzy cudzysłowy usunięte z uwzględnieniem komunikatu o zakresie dziennika:</span><span class="sxs-lookup"><span data-stu-id="e1d8d-267">The following result shows three quotes deleted with the log scope message included:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="e1d8d-268">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="e1d8d-268">Additional resources</span></span>

* [<span data-ttu-id="e1d8d-269">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="e1d8d-269">Logging</span></span>](xref:fundamentals/logging/index)
