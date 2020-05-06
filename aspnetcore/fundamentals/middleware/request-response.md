---
title: Operacje żądań i odpowiedzi w ASP.NET Core
author: jkotalik
description: Dowiedz się, jak odczytać treść żądania i napisać treść odpowiedzi w ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jukotali
ms.custom: mvc
ms.date: 08/29/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/middleware/request-response
ms.openlocfilehash: f16bc7ec61c10600fe72a763fef96987210fbe76
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776002"
---
# <a name="request-and-response-operations-in-aspnet-core"></a>Operacje żądań i odpowiedzi w ASP.NET Core

Autor [Justin Kotalik](https://github.com/jkotalik)

W tym artykule wyjaśniono sposób odczytywania treści żądania i zapisywania jej w treści odpowiedzi. Kod dla tych operacji może być wymagany podczas pisania oprogramowania pośredniczącego. Poza pisaniem oprogramowania pośredniczącego kod niestandardowy nie jest zazwyczaj wymagany, ponieważ operacje są obsługiwane przez MVC i Razor strony.

Istnieją dwa abstrakcje treści żądania i odpowiedzi: <xref:System.IO.Stream> i. <xref:System.IO.Pipelines.Pipe> W przypadku odczytu żądania żądanie [HttpRequest. Body](xref:Microsoft.AspNetCore.Http.HttpRequest.Body) ma <xref:System.IO.Stream>wartość, `HttpRequest.BodyReader` a jest <xref:System.IO.Pipelines.PipeReader>. Do pisania odpowiedzi [HttpResponse. Body](xref:Microsoft.AspNetCore.Http.HttpResponse.Body) jest <xref:System.IO.Stream>i `HttpResponse.BodyWriter` <xref:System.IO.Pipelines.PipeWriter>.

[Potoki](/dotnet/standard/io/pipelines) są zalecane za pośrednictwem strumieni. Strumienie mogą być łatwiejsze w przypadku niektórych prostych operacji, ale potoki mają zalety wydajności i są łatwiejsze w większości scenariuszy. ASP.NET Core zaczyna używać potoków zamiast strumieni wewnętrznie. Przykłady:

* `FormReader`
* `TextReader`
* `TextWriter`
* `HttpResponse.WriteAsync`

Strumienie nie są usuwane z struktury. Strumienie są nadal używane w środowisku .NET, a wiele typów strumieni nie ma odpowiedników potoku, `FileStreams` takich `ResponseCompression`jak i.

## <a name="stream-examples"></a>Przykłady przesyłania strumieniowego

Załóżmy, że celem jest utworzenie oprogramowania pośredniczącego, które odczytuje całą treść żądania jako listę ciągów, dzieląc je na nowe wiersze. Prosta implementacja strumienia może wyglądać podobnie do poniższego przykładu:

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStream)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Ten kod działa, ale wystąpiły pewne problemy:

* Przed dołączeniem do `StringBuilder`, przykład tworzy kolejny ciąg (`encodedString`), który jest wyrzucany natychmiast. Ten proces występuje dla wszystkich bajtów w strumieniu, więc wynikiem jest dodatkowa alokacja pamięci o rozmiarze całej treści żądania.
* Przykład odczytuje cały ciąg przed podziałem w nowych wierszach. Aby sprawdzić, czy nowe wiersze w tablicy bajtów są bardziej wydajne.

Oto przykład, który rozwiązuje niektóre z powyższych problemów:

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStreamMoreEfficient)]

Ten poprzedni przykład:

* Nie buforuje całej treści żądania w a `StringBuilder` , chyba że nie występują żadne znaki nowego wiersza.
* Nie wywołuje `Split` ciągu.

Jednak nadal występują pewne problemy:

* Jeśli znaki nowego wiersza są rozrzedzone, większość treści żądania jest buforowana w ciągu.
* Kod nadal tworzy ciągi (`remainingString`) i dodaje je do buforu ciągów, co skutkuje dodatkową alokacją.

Te problemy są fixable, ale kod staje się coraz bardziej skomplikowany przy niewielkim ulepszaniu. Potoki umożliwiają rozwiązanie tych problemów przy minimalnej złożoności kodu.

## <a name="pipelines"></a>Potoki

Poniższy przykład pokazuje, jak można obsłużyć ten sam scenariusz przy `PipeReader`użyciu:

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringFromPipe)]

Ten przykład naprawia wiele problemów, które zostały wdrożone przez implementacje strumieni:

* Nie ma potrzeby używania bufora ciągów, ponieważ `PipeReader` obsługuje on bajty, które nie były używane.
* Zakodowane ciągi są bezpośrednio dodawane do listy zwracanych ciągów.
* Tworzenie ciągów jest wolne od alokacji poza pamięcią używaną przez ciąg (z wyjątkiem `ToArray()` wywołania).

## <a name="adapters"></a>Zainstalowanych

Obie `Body` właściwości `BodyReader/BodyWriter` i są dostępne dla `HttpRequest` i `HttpResponse`. Po ustawieniu `Body` innego strumienia nowy zestaw kart automatycznie dostosowuje każdy typ do drugiego. Jeśli ustawisz `HttpRequest.Body` nowy strumień, `HttpRequest.BodyReader` zostanie automatycznie ustawiony na nowe `PipeReader` Zawijanie. `HttpRequest.Body`

## <a name="startasync"></a>StartAsync

`HttpResponse.StartAsync`służy do wskazywania, że nagłówki są niemodyfikowalne i `OnStarting` aby można było uruchamiać wywołania zwrotne. Podczas używania `StartAsync` Kestrel jako serwera, wywołując przed użyciem `PipeReader` gwarancji, że pamięć zwrócona przez `GetMemory` należy do wewnętrznego <xref:System.IO.Pipelines.Pipe> , a nie do buforu zewnętrznego.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Wprowadzenie do System. IO. potoków](https://devblogs.microsoft.com/dotnet/system-io-pipelines-high-performance-io-in-net/)
* <xref:fundamentals/middleware/write>
