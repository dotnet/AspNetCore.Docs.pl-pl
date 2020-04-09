---
title: Operacje żądania i odpowiedzi w ASP.NET Core
author: jkotalik
description: Dowiedz się, jak odczytać treść żądania i napisać treść odpowiedzi w ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jukotali
ms.custom: mvc
ms.date: 08/29/2019
uid: fundamentals/middleware/request-response
ms.openlocfilehash: b473fa02e1d23f02bc5d2e15fa54ab7b1dbbb17c
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667217"
---
# <a name="request-and-response-operations-in-aspnet-core"></a>Operacje żądania i odpowiedzi w ASP.NET Core

Przez [Justin Kotalik](https://github.com/jkotalik)

W tym artykule wyjaśniono, jak odczytać z treści żądania i napisać do treści odpowiedzi. Kod dla tych operacji może być wymagane podczas pisania oprogramowania pośredniczącego. Poza pisaniem oprogramowania pośredniczącego kod niestandardowy nie jest zazwyczaj wymagany, ponieważ operacje są obsługiwane przez strony MVC i Razor Pages.

Istnieją dwie abstrakcje dla żądań <xref:System.IO.Stream> <xref:System.IO.Pipelines.Pipe>i organów odpowiedzi: i . Do odczytu żądania, [HttpRequest.Body](xref:Microsoft.AspNetCore.Http.HttpRequest.Body) jest <xref:System.IO.Stream>, i `HttpRequest.BodyReader` jest . <xref:System.IO.Pipelines.PipeReader> Do pisania [odpowiedzi, HttpResponse.Body](xref:Microsoft.AspNetCore.Http.HttpResponse.Body) `HttpResponse.BodyWriter` jest <xref:System.IO.Pipelines.PipeWriter> <xref:System.IO.Stream>, i jest .

[Potoki](/dotnet/standard/io/pipelines) są zalecane za 100 000 000 000 000 Strumienie mogą być łatwiejsze w użyciu dla niektórych prostych operacji, ale potoki mają przewagę wydajności i są łatwiejsze w użyciu w większości scenariuszy. ASP.NET Core zaczyna używać potoków zamiast strumieni wewnętrznie. Przykłady:

* `FormReader`
* `TextReader`
* `TextWriter`
* `HttpResponse.WriteAsync`

Strumienie nie są usuwane z struktury. Strumienie są nadal używane w całej .NET, a wiele typów strumieni `FileStreams` nie `ResponseCompression`ma odpowiedników potoków, takich jak i .

## <a name="stream-examples"></a>Przykłady strumienia

Załóżmy, że celem jest utworzenie oprogramowania pośredniczącego, które odczytuje całą treść żądania jako listę ciągów, dzieląc się na nowe wiersze. Implementacja prostego strumienia może wyglądać następująco:

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStream)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Ten kod działa, ale istnieją pewne problemy:

* Przed dołączeniem do `StringBuilder`, przykład tworzy`encodedString`inny ciąg ( ), który jest natychmiast wyrzucany. Ten proces występuje dla wszystkich bajtów w strumieniu, więc wynik jest alokacja pamięci dodatkowe rozmiar całej treści żądania.
* W przykładzie odczytuje cały ciąg przed podziałem na nowe wiersze. Bardziej wydajne jest sprawdzanie nowych wierszy w tablicy bajtów.

Oto przykład, który rozwiązuje niektóre z powyższych problemów:

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStreamMoreEfficient)]

Ten poprzedni przykład:

* Nie buforuje całego treści żądania `StringBuilder` w a, chyba że nie ma żadnych znaków nowego pliku.
* Nie wywołanie `Split` ciągu.

Jednak nadal istnieje kilka problemów:

* Jeśli znaki nowego typu są rozrzedzone, większość treści żądania jest buforowana w ciągu.
* Kod kontynuuje tworzenie ciągów`remainingString`( ) i dodaje je do buforu ciągu, co powoduje dodatkową alokację.

Te problemy można naprawić, ale kod staje się coraz bardziej skomplikowane z niewielkim udoskonaleniem. Potoki umożliwiają rozwiązanie tych problemów przy minimalnej złożoności kodu.

## <a name="pipelines"></a>Potoki

W poniższym przykładzie pokazano, jak ten `PipeReader`sam scenariusz może być obsługiwany przy użyciu:

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringFromPipe)]

W tym przykładzie rozwiązuje wiele problemów, które implementacje strumieni miał:

* Nie ma potrzeby buforu ciągów, ponieważ `PipeReader` dojścia bajtów, które nie zostały użyte.
* Zakodowane ciągi są bezpośrednio dodawane do listy zwróconych ciągów.
* Tworzenie ciągów jest wolne od alokacji oprócz `ToArray()` pamięci używanej przez ciąg (z wyjątkiem wywołania).

## <a name="adapters"></a>Karty

Zarówno `Body` `BodyReader/BodyWriter` właściwości, jak `HttpRequest` i `HttpResponse`właściwości są dostępne dla i . Po ustawieniu `Body` innego strumienia nowy zestaw kart automatycznie dostosowuje każdy typ do drugiego. Jeśli `HttpRequest.Body` ustawisz nowy `HttpRequest.BodyReader` strumień, zostanie automatycznie `PipeReader` ustawiona `HttpRequest.Body`na nowy, który zawija .

## <a name="startasync"></a>StartAsync (StartAsync)

`HttpResponse.StartAsync`służy do oznaczania, że nagłówki są niemodifiable i do uruchamiania `OnStarting` wywołań zwrotnych. Podczas korzystania z Kestrel `StartAsync` jako serwera, wywołanie przed użyciem `PipeReader` gwarancji, że pamięć zwracana przez `GetMemory` należy do pustułki wewnętrznej, <xref:System.IO.Pipelines.Pipe> a nie zewnętrznego buforu.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Przedstawiamy system.IO.Pipelines](https://devblogs.microsoft.com/dotnet/system-io-pipelines-high-performance-io-in-net/)
* <xref:fundamentals/middleware/write>
