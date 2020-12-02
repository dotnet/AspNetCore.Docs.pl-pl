---
title: Operacje żądań i odpowiedzi w ASP.NET Core
author: jkotalik
description: Dowiedz się, jak odczytać treść żądania i napisać treść odpowiedzi w ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jukotali
ms.custom: mvc
ms.date: 5/29/2019
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/middleware/request-response
ms.openlocfilehash: 5ad39821778ea58097169def85a940a06f1d036e
ms.sourcegitcommit: 0bcc0d6df3145a0727da7c4be2f4bda8f27eeaa3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96513112"
---
# <a name="request-and-response-operations-in-aspnet-core"></a>Operacje żądań i odpowiedzi w ASP.NET Core

Autor [Justin Kotalik](https://github.com/jkotalik)

W tym artykule wyjaśniono sposób odczytywania treści żądania i zapisywania jej w treści odpowiedzi. Kod dla tych operacji może być wymagany podczas pisania oprogramowania pośredniczącego. Poza pisaniem oprogramowania pośredniczącego kod niestandardowy nie jest zazwyczaj wymagany, ponieważ operacje są obsługiwane przez MVC i Razor strony.

Istnieją dwa abstrakcje treści żądania i odpowiedzi: <xref:System.IO.Stream> i <xref:System.IO.Pipelines.Pipe> . Do odczytu żądania, <xref:Microsoft.AspNetCore.Http.HttpRequest.Body?displayProperty=nameWithType> is <xref:System.IO.Stream> i `HttpRequest.BodyReader` <xref:System.IO.Pipelines.PipeReader> . Na potrzeby pisania odpowiedzi <xref:Microsoft.AspNetCore.Http.HttpResponse.Body?displayProperty=nameWithType> jest <xref:System.IO.Stream> i `HttpResponse.BodyWriter` <xref:System.IO.Pipelines.PipeWriter> .

[Potoki](/dotnet/standard/io/pipelines) są zalecane za pośrednictwem strumieni. Strumienie mogą być łatwiejsze w przypadku niektórych prostych operacji, ale potoki mają zalety wydajności i są łatwiejsze w większości scenariuszy. ASP.NET Core zaczyna używać potoków zamiast strumieni wewnętrznie. Przykłady:

* `FormReader`
* `TextReader`
* `TextWriter`
* `HttpResponse.WriteAsync`

Strumienie nie są usuwane z struktury. Strumienie są nadal używane w środowisku .NET, a wiele typów strumieni nie ma odpowiedników potoku, takich jak `FileStreams` i `ResponseCompression` .

## <a name="stream-examples"></a>Przykłady przesyłania strumieniowego

<!-- see "fundamentals\middleware\request-response\static\TestPipes.JPG for testing sample -->

Załóżmy, że celem jest utworzenie oprogramowania pośredniczącego, które odczytuje całą treść żądania jako listę ciągów, dzieląc je na nowe wiersze. Prosta implementacja strumienia może wyglądać podobnie do poniższego przykładu:

> [!WARNING]
> Następujący kod:
> * Służy do zademonstrowania problemów nieużywających potoku w celu odczytania treści żądania.
> * Nie jest przeznaczony do użycia w aplikacjach produkcyjnych.

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStream)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Ten kod działa, ale wystąpiły pewne problemy:

* Przed dołączeniem do `StringBuilder` , przykład tworzy kolejny ciąg ( `encodedString` ), który jest wyrzucany natychmiast. Ten proces występuje dla wszystkich bajtów w strumieniu, więc wynikiem jest dodatkowa alokacja pamięci o rozmiarze całej treści żądania.
* Przykład odczytuje cały ciąg przed podziałem w nowych wierszach. Aby sprawdzić, czy nowe wiersze w tablicy bajtów są bardziej wydajne.

Oto przykład, który rozwiązuje niektóre z powyższych problemów:

> [!WARNING]
> Następujący kod:
> * Służy do zademonstrowania rozwiązań niektórych problemów występujących w poprzednim kodzie bez rozwiązywania wszystkich problemów.
> * Nie jest przeznaczony do użycia w aplikacjach produkcyjnych.

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStreamMoreEfficient)]

Ten poprzedni przykład:

* Nie buforuje całej treści żądania w a, `StringBuilder` chyba że nie występują żadne znaki nowego wiersza.
* Nie wywołuje `Split` ciągu.

Jednak nadal występują pewne problemy:

* Jeśli znaki nowego wiersza są rozrzedzone, większość treści żądania jest buforowana w ciągu.
* Kod nadal tworzy ciągi ( `remainingString` ) i dodaje je do buforu ciągów, co skutkuje dodatkową alokacją.

Te problemy są fixable, ale kod staje się coraz bardziej skomplikowany przy niewielkim ulepszaniu. Potoki umożliwiają rozwiązanie tych problemów przy minimalnej złożoności kodu.

## <a name="pipelines"></a>Pipelines

Poniższy przykład pokazuje, jak można obsłużyć ten sam scenariusz przy użyciu [PipeReader](/dotnet/standard/io/pipelines#pipe):

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringFromPipe)]

Ten przykład naprawia wiele problemów, które zostały wdrożone przez implementacje strumieni:

* Nie ma potrzeby używania bufora ciągów `PipeReader` , ponieważ obsługuje on bajty, które nie były używane.
* Zakodowane ciągi są bezpośrednio dodawane do listy zwracanych ciągów.
* Oprócz `ToArray` wywołania i pamięci używanej przez ciąg, tworzenie ciągów jest bezpłatne.

## <a name="adapters"></a>Zainstalowanych

`Body`Właściwości, `BodyReader` i `BodyWriter` są dostępne dla `HttpRequest` i `HttpResponse` . Po ustawieniu `Body` innego strumienia nowy zestaw kart automatycznie dostosowuje każdy typ do drugiego. Jeśli ustawisz `HttpRequest.Body` Nowy strumień, `HttpRequest.BodyReader` zostanie automatycznie ustawiony na nowe `PipeReader` Zawijanie `HttpRequest.Body` .

## <a name="startasync"></a>StartAsync

`HttpResponse.StartAsync` służy do wskazywania, że nagłówki są niemodyfikowalne i aby można było uruchamiać `OnStarting` wywołania zwrotne. Podczas używania Kestrel jako serwera, wywołując `StartAsync` przed użyciem `PipeReader` gwarancji, że pamięć zwrócona przez `GetMemory` należy do wewnętrznego, <xref:System.IO.Pipelines.Pipe> a nie do buforu zewnętrznego.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [System. IO. potoki w środowisku .NET](/dotnet/standard/io/pipelines)
* <xref:fundamentals/middleware/write>

<!-- Test with Postman or other tool. See image in static directory. -->
