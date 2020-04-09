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
# <a name="high-performance-logging-with-loggermessage-in-aspnet-core"></a>Rejestrowanie o wysokiej wydajności za pomocą loggerMessage w ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.Extensions.Logging.LoggerMessage>funkcje tworzą buforowalne delegatów, które wymagają mniej alokacji obiektów i zmniejszenie <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>obciążenia obliczeniowego w porównaniu do metod [rozszerzenia rejestratora,](xref:Microsoft.Extensions.Logging.LoggerExtensions)takich jak i . W przypadku scenariuszy rejestrowania o <xref:Microsoft.Extensions.Logging.LoggerMessage> wysokiej wydajności należy użyć wzorca.

<xref:Microsoft.Extensions.Logging.LoggerMessage>zapewnia następujące korzyści wydajności w stosunku do metod rozszerzenia Rejestratora:

* Metody rozszerzenia rejestratora wymagają "boksowania" (konwersji) typów wartości, takich jak `int`. `object` Wzorzec <xref:Microsoft.Extensions.Logging.LoggerMessage> unika boksu <xref:System.Action> przy użyciu pól statycznych i metod rozszerzenia z parametrami silnie typizowane.
* Metody rozszerzenia rejestratora należy przeanalizować szablon wiadomości (ciąg formatu o nazwie) za każdym razem, gdy jest zapisywany komunikat dziennika. <xref:Microsoft.Extensions.Logging.LoggerMessage>wymaga analizowania szablonu tylko raz, gdy wiadomość jest zdefiniowana.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))

Przykładowa aplikacja <xref:Microsoft.Extensions.Logging.LoggerMessage> pokazuje funkcje za pomocą podstawowego systemu śledzenia ofert. Aplikacja dodaje i usuwa cudzysłowy przy użyciu bazy danych w pamięci. W miarę występowania tych operacji <xref:Microsoft.Extensions.Logging.LoggerMessage> komunikaty dziennika są generowane przy użyciu wzorca.

## <a name="loggermessagedefine"></a>LoggerMessage.Define

[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) tworzy <xref:System.Action> pełnomocnika do rejestrowania wiadomości. <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*>przeciążenia umożliwiają przekazywanie maksymalnie sześciu parametrów typu do ciągu o nazwie formatu (szablonu).

Ciąg dostarczony do <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> metody jest szablonem, a nie interpolowanym ciągiem. Symbole zastępcze są wypełniane w kolejności, w określonej kolejności. Nazwy symboli zastępczych w szablonie powinny być opisowe i spójne w szablonach. Służą one jako nazwy właściwości w danych dziennika strukturalnego. Zalecamy [pascalową obudowę](/dotnet/standard/design-guidelines/capitalization-conventions) dla nazw symboli zastępczych. Na przykład `{Count}` `{FirstName}`, .

Każdy komunikat dziennika <xref:System.Action> jest przechowywany w polu statycznym utworzonym przez [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*). Na przykład przykładowa aplikacja tworzy pole opisujące komunikat dziennika dla żądania GET dla strony Indeks (*Internal/LoggerExtensions.cs*):

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet1)]

Dla <xref:System.Action>, określić:

* Poziom dziennika.
* Unikatowy identyfikator zdarzenia<xref:Microsoft.Extensions.Logging.EventId>( ) z nazwą metody rozszerzenia statycznego.
* Szablon wiadomości (ciąg formatu o nazwie). 

Żądanie dla indeksu strony przykładowej aplikacji ustawia:

* Poziom dziennika `Information`do .
* Identyfikator zdarzenia `1` z nazwą `IndexPageRequested` metody.
* Szablon wiadomości (nazwany ciąg formatu) do ciągu.

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet5)]

Magazyny rejestrowania strukturalnego mogą używać nazwy zdarzenia, gdy jest ona dostarczana z identyfikatorem zdarzenia, aby wzbogacić rejestrowanie. Na przykład [Serilog](https://github.com/serilog/serilog-extensions-logging) używa nazwy zdarzenia.

Jest <xref:System.Action> wywoływana za pomocą metody rozszerzenia silnie typizowane. Metoda `IndexPageRequested` rejestruje komunikat dla żądania GET strony indeksu w przykładowej aplikacji:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet9)]

`IndexPageRequested`jest wywoływana na rejestratorze w metodzie `OnGetAsync` w *Pages/Index.cshtml.cs*:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

Sprawdź dane wyjściowe konsoli aplikacji:

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

Aby przekazać parametry do komunikatu dziennika, należy zdefiniować maksymalnie sześć typów podczas tworzenia pola statycznego. Przykładowa aplikacja rejestruje ciąg podczas dodawania cudzysłowu, `string` definiując typ <xref:System.Action> dla pola:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet2)]

Szablon komunikatu dziennika pełnomocnika odbiera jego wartości zastępcze z podanych typów. Przykładowa aplikacja definiuje pełnomocnika do dodawania oferty, `string`gdzie parametr oferty jest:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet6)]

Metoda rozszerzenia statycznego do dodawania oferty, `QuoteAdded`otrzymuje wartość argumentu <xref:System.Action> oferty i przekazuje ją do delegata:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet10)]

W modelu strony indeksu *(Pages/Index.cshtml.cs)* `QuoteAdded` jest wywoływana w celu zarejestrowania wiadomości:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

Sprawdź dane wyjściowe konsoli aplikacji:

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand')
```

Przykładowa aplikacja implementuje wzorzec [try&ndash;catch](/dotnet/csharp/language-reference/keywords/try-catch) do usunięcia oferty. Komunikat informacyjny jest rejestrowany dla pomyślnej operacji usuwania. Komunikat o błędzie jest rejestrowany dla operacji usuwania, gdy wyjątek. Komunikat dziennika dla operacji usuwania nieudanego zawiera śledzenie stosu*wyjątków (Internal/LoggerExtensions.cs*):

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet7)]

Zwróć uwagę, jak wyjątek `QuoteDeleteFailed`jest przekazywany do pełnomocnika w:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet11)]

W modelu strony dla indeksu strony pomyślne usunięcie `QuoteDeleted` oferty wywołuje metodę na rejestratorze. Gdy cytat nie zostanie znaleziony do usunięcia, jest wyrzucany. <xref:System.ArgumentNullException> Wyjątek jest zalewkowany przez [try&ndash;catch](/dotnet/csharp/language-reference/keywords/try-catch) instrukcji `QuoteDeleteFailed` i rejestrowane przez wywołanie metody na rejestratora w bloku [catch](/dotnet/csharp/language-reference/keywords/try-catch) *(Pages/Index.cshtml.cs):*

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet5&highlight=9,13)]

Po pomyślnym usunięciu oferty sprawdź dane wyjściowe konsoli aplikacji:

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand' Id = 1)
```

Gdy usunięcie oferty nie powiedzie się, sprawdź dane wyjściowe konsoli aplikacji. Należy zauważyć, że wyjątek znajduje się w komunikacie dziennika:

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

## <a name="loggermessagedefinescope"></a>LoggerMessage.DefineScope

[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) tworzy <xref:System.Func%601> pełnomocnika do definiowania [zakresu dziennika](xref:fundamentals/logging/index#log-scopes). <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*>przeciążenia umożliwiają przekazywanie maksymalnie trzech parametrów typu do ciągu o nazwie formatu (szablonu).

Podobnie jak w <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> przypadku metody, ciąg <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> dostarczony do metody jest szablonem, a nie interpolowanym ciągiem. Symbole zastępcze są wypełniane w kolejności, w określonej kolejności. Nazwy symboli zastępczych w szablonie powinny być opisowe i spójne w szablonach. Służą one jako nazwy właściwości w danych dziennika strukturalnego. Zalecamy [pascalową obudowę](/dotnet/standard/design-guidelines/capitalization-conventions) dla nazw symboli zastępczych. Na przykład `{Count}` `{FirstName}`, .

Zdefiniuj [zakres dziennika,](xref:fundamentals/logging/index#log-scopes) aby zastosować <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> do serii komunikatów dziennika przy użyciu metody.

Przykładowa aplikacja ma przycisk **Wyczyść wszystko** do usuwania wszystkich cudzysłowów w bazie danych. Cudzysłowy są usuwane przez usunięcie ich po jednym na raz. Za każdym razem, gdy `QuoteDeleted` cytat jest usuwany, metoda jest wywoływana na rejestratorze. Zakres dziennika jest dodawany do tych komunikatów dziennika.

Włącz `IncludeScopes` w sekcji rejestrator konsoli *appsettings.json*:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/appsettings.json?highlight=3-5)]

Aby utworzyć zakres dziennika, dodaj pole <xref:System.Func%601> do przechowywania pełnomocnika dla zakresu. Przykładowa aplikacja tworzy `_allQuotesDeletedScope` pole o nazwie *(Internal/LoggerExtensions.cs):*

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet4)]

Służy <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> do tworzenia pełnomocnika. Do trzech typów można określić do użycia jako argumenty szablonu, gdy jest wywoływany delegat. Przykładowa aplikacja używa szablonu wiadomości, który zawiera liczbę `int` usuniętych ofert (typ):

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet8)]

Podaj metodę rozszerzenia statycznego dla komunikatu dziennika. Dołącz wszystkie parametry typu dla nazwanych właściwości, które pojawiają się w szablonie wiadomości. Przykładowa aplikacja przyjmuje `count` w cudzysłowie, aby usunąć i zwraca: `_allQuotesDeletedScope`

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet12)]

Zakres zawija wywołania rozszerzenia rejestrowania w [using](/dotnet/csharp/language-reference/keywords/using-statement) bloku:

[!code-csharp[](loggermessage/samples/3.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

Sprawdź komunikaty dziennika w danych wyjściowych konsoli aplikacji. Poniższy wynik pokazuje trzy cudzysłowy usunięte z komunikatem zakresu dziennika:

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

<xref:Microsoft.Extensions.Logging.LoggerMessage>funkcje tworzą buforowalne delegatów, które wymagają mniej alokacji obiektów i zmniejszenie <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug*>obciążenia obliczeniowego w porównaniu do metod [rozszerzenia rejestratora,](xref:Microsoft.Extensions.Logging.LoggerExtensions)takich jak i . W przypadku scenariuszy rejestrowania o <xref:Microsoft.Extensions.Logging.LoggerMessage> wysokiej wydajności należy użyć wzorca.

<xref:Microsoft.Extensions.Logging.LoggerMessage>zapewnia następujące korzyści wydajności w stosunku do metod rozszerzenia Rejestratora:

* Metody rozszerzenia rejestratora wymagają "boksowania" (konwersji) typów wartości, takich jak `int`. `object` Wzorzec <xref:Microsoft.Extensions.Logging.LoggerMessage> unika boksu <xref:System.Action> przy użyciu pól statycznych i metod rozszerzenia z parametrami silnie typizowane.
* Metody rozszerzenia rejestratora należy przeanalizować szablon wiadomości (ciąg formatu o nazwie) za każdym razem, gdy jest zapisywany komunikat dziennika. <xref:Microsoft.Extensions.Logging.LoggerMessage>wymaga analizowania szablonu tylko raz, gdy wiadomość jest zdefiniowana.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/loggermessage/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))

Przykładowa aplikacja <xref:Microsoft.Extensions.Logging.LoggerMessage> pokazuje funkcje za pomocą podstawowego systemu śledzenia ofert. Aplikacja dodaje i usuwa cudzysłowy przy użyciu bazy danych w pamięci. W miarę występowania tych operacji <xref:Microsoft.Extensions.Logging.LoggerMessage> komunikaty dziennika są generowane przy użyciu wzorca.

## <a name="loggermessagedefine"></a>LoggerMessage.Define

[Define(LogLevel, EventId, String)](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*) tworzy <xref:System.Action> pełnomocnika do rejestrowania wiadomości. <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*>przeciążenia umożliwiają przekazywanie maksymalnie sześciu parametrów typu do ciągu o nazwie formatu (szablonu).

Ciąg dostarczony do <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> metody jest szablonem, a nie interpolowanym ciągiem. Symbole zastępcze są wypełniane w kolejności, w określonej kolejności. Nazwy symboli zastępczych w szablonie powinny być opisowe i spójne w szablonach. Służą one jako nazwy właściwości w danych dziennika strukturalnego. Zalecamy [pascalową obudowę](/dotnet/standard/design-guidelines/capitalization-conventions) dla nazw symboli zastępczych. Na przykład `{Count}` `{FirstName}`, .

Każdy komunikat dziennika <xref:System.Action> jest przechowywany w polu statycznym utworzonym przez [LoggerMessage.Define](xref:Microsoft.Extensions.Logging.LoggerMessage.Define*). Na przykład przykładowa aplikacja tworzy pole opisujące komunikat dziennika dla żądania GET dla strony Indeks (*Internal/LoggerExtensions.cs*):

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet1)]

Dla <xref:System.Action>, określić:

* Poziom dziennika.
* Unikatowy identyfikator zdarzenia<xref:Microsoft.Extensions.Logging.EventId>( ) z nazwą metody rozszerzenia statycznego.
* Szablon wiadomości (ciąg formatu o nazwie). 

Żądanie dla indeksu strony przykładowej aplikacji ustawia:

* Poziom dziennika `Information`do .
* Identyfikator zdarzenia `1` z nazwą `IndexPageRequested` metody.
* Szablon wiadomości (nazwany ciąg formatu) do ciągu.

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet5)]

Magazyny rejestrowania strukturalnego mogą używać nazwy zdarzenia, gdy jest ona dostarczana z identyfikatorem zdarzenia, aby wzbogacić rejestrowanie. Na przykład [Serilog](https://github.com/serilog/serilog-extensions-logging) używa nazwy zdarzenia.

Jest <xref:System.Action> wywoływana za pomocą metody rozszerzenia silnie typizowane. Metoda `IndexPageRequested` rejestruje komunikat dla żądania GET strony indeksu w przykładowej aplikacji:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet9)]

`IndexPageRequested`jest wywoływana na rejestratorze w metodzie `OnGetAsync` w *Pages/Index.cshtml.cs*:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3)]

Sprawdź dane wyjściowe konsoli aplikacji:

```console
info: LoggerMessageSample.Pages.IndexModel[1]
      => RequestId:0HL90M6E7PHK4:00000001 RequestPath:/ => /Index
      GET request for Index page
```

Aby przekazać parametry do komunikatu dziennika, należy zdefiniować maksymalnie sześć typów podczas tworzenia pola statycznego. Przykładowa aplikacja rejestruje ciąg podczas dodawania cudzysłowu, `string` definiując typ <xref:System.Action> dla pola:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet2)]

Szablon komunikatu dziennika pełnomocnika odbiera jego wartości zastępcze z podanych typów. Przykładowa aplikacja definiuje pełnomocnika do dodawania oferty, `string`gdzie parametr oferty jest:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet6)]

Metoda rozszerzenia statycznego do dodawania oferty, `QuoteAdded`otrzymuje wartość argumentu <xref:System.Action> oferty i przekazuje ją do delegata:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet10)]

W modelu strony indeksu *(Pages/Index.cshtml.cs)* `QuoteAdded` jest wywoływana w celu zarejestrowania wiadomości:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet3&highlight=6)]

Sprawdź dane wyjściowe konsoli aplikacji:

```console
info: LoggerMessageSample.Pages.IndexModel[2]
      => RequestId:0HL90M6E7PHK5:0000000A RequestPath:/ => /Index
      Quote added (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand')
```

Przykładowa aplikacja implementuje wzorzec [try&ndash;catch](/dotnet/csharp/language-reference/keywords/try-catch) do usunięcia oferty. Komunikat informacyjny jest rejestrowany dla pomyślnej operacji usuwania. Komunikat o błędzie jest rejestrowany dla operacji usuwania, gdy wyjątek. Komunikat dziennika dla operacji usuwania nieudanego zawiera śledzenie stosu*wyjątków (Internal/LoggerExtensions.cs*):

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet3)]

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet7)]

Zwróć uwagę, jak wyjątek `QuoteDeleteFailed`jest przekazywany do pełnomocnika w:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet11)]

W modelu strony dla indeksu strony pomyślne usunięcie `QuoteDeleted` oferty wywołuje metodę na rejestratorze. Gdy cytat nie zostanie znaleziony do usunięcia, jest wyrzucany. <xref:System.ArgumentNullException> Wyjątek jest zalewkowany przez [try&ndash;catch](/dotnet/csharp/language-reference/keywords/try-catch) instrukcji `QuoteDeleteFailed` i rejestrowane przez wywołanie metody na rejestratora w bloku [catch](/dotnet/csharp/language-reference/keywords/try-catch) *(Pages/Index.cshtml.cs):*

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet5&highlight=14,18)]

Po pomyślnym usunięciu oferty sprawdź dane wyjściowe konsoli aplikacji:

```console
info: LoggerMessageSample.Pages.IndexModel[4]
      => RequestId:0HL90M6E7PHK5:00000016 RequestPath:/ => /Index
      Quote deleted (Quote = 'You can avoid reality, but you cannot avoid the 
          consequences of avoiding reality. - Ayn Rand' Id = 1)
```

Gdy usunięcie oferty nie powiedzie się, sprawdź dane wyjściowe konsoli aplikacji. Należy zauważyć, że wyjątek znajduje się w komunikacie dziennika:

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

## <a name="loggermessagedefinescope"></a>LoggerMessage.DefineScope

[DefineScope(String)](xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*) tworzy <xref:System.Func%601> pełnomocnika do definiowania [zakresu dziennika](xref:fundamentals/logging/index#log-scopes). <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*>przeciążenia umożliwiają przekazywanie maksymalnie trzech parametrów typu do ciągu o nazwie formatu (szablonu).

Podobnie jak w <xref:Microsoft.Extensions.Logging.LoggerMessage.Define*> przypadku metody, ciąg <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> dostarczony do metody jest szablonem, a nie interpolowanym ciągiem. Symbole zastępcze są wypełniane w kolejności, w określonej kolejności. Nazwy symboli zastępczych w szablonie powinny być opisowe i spójne w szablonach. Służą one jako nazwy właściwości w danych dziennika strukturalnego. Zalecamy [pascalową obudowę](/dotnet/standard/design-guidelines/capitalization-conventions) dla nazw symboli zastępczych. Na przykład `{Count}` `{FirstName}`, .

Zdefiniuj [zakres dziennika,](xref:fundamentals/logging/index#log-scopes) aby zastosować <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> do serii komunikatów dziennika przy użyciu metody.

Przykładowa aplikacja ma przycisk **Wyczyść wszystko** do usuwania wszystkich cudzysłowów w bazie danych. Cudzysłowy są usuwane przez usunięcie ich po jednym na raz. Za każdym razem, gdy `QuoteDeleted` cytat jest usuwany, metoda jest wywoływana na rejestratorze. Zakres dziennika jest dodawany do tych komunikatów dziennika.

Włącz `IncludeScopes` w sekcji rejestrator konsoli *appsettings.json*:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/appsettings.json?highlight=3-5)]

Aby utworzyć zakres dziennika, dodaj pole <xref:System.Func%601> do przechowywania pełnomocnika dla zakresu. Przykładowa aplikacja tworzy `_allQuotesDeletedScope` pole o nazwie *(Internal/LoggerExtensions.cs):*

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet4)]

Służy <xref:Microsoft.Extensions.Logging.LoggerMessage.DefineScope*> do tworzenia pełnomocnika. Do trzech typów można określić do użycia jako argumenty szablonu, gdy jest wywoływany delegat. Przykładowa aplikacja używa szablonu wiadomości, który zawiera liczbę `int` usuniętych ofert (typ):

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet8)]

Podaj metodę rozszerzenia statycznego dla komunikatu dziennika. Dołącz wszystkie parametry typu dla nazwanych właściwości, które pojawiają się w szablonie wiadomości. Przykładowa aplikacja przyjmuje `count` w cudzysłowie, aby usunąć i zwraca: `_allQuotesDeletedScope`

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Internal/LoggerExtensions.cs?name=snippet12)]

Zakres zawija wywołania rozszerzenia rejestrowania w [using](/dotnet/csharp/language-reference/keywords/using-statement) bloku:

[!code-csharp[](loggermessage/samples/2.x/LoggerMessageSample/Pages/Index.cshtml.cs?name=snippet4&highlight=5-6,14)]

Sprawdź komunikaty dziennika w danych wyjściowych konsoli aplikacji. Poniższy wynik pokazuje trzy cudzysłowy usunięte z komunikatem zakresu dziennika:

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

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Rejestrowanie](xref:fundamentals/logging/index)
