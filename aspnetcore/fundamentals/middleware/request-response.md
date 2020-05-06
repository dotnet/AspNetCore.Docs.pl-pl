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
# <a name="request-and-response-operations-in-aspnet-core"></a><span data-ttu-id="f67b8-103">Operacje żądań i odpowiedzi w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f67b8-103">Request and response operations in ASP.NET Core</span></span>

<span data-ttu-id="f67b8-104">Autor [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="f67b8-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

<span data-ttu-id="f67b8-105">W tym artykule wyjaśniono sposób odczytywania treści żądania i zapisywania jej w treści odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="f67b8-105">This article explains how to read from the request body and write to the response body.</span></span> <span data-ttu-id="f67b8-106">Kod dla tych operacji może być wymagany podczas pisania oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="f67b8-106">Code for these operations might be required when writing middleware.</span></span> <span data-ttu-id="f67b8-107">Poza pisaniem oprogramowania pośredniczącego kod niestandardowy nie jest zazwyczaj wymagany, ponieważ operacje są obsługiwane przez MVC i Razor strony.</span><span class="sxs-lookup"><span data-stu-id="f67b8-107">Outside of writing middleware, custom code isn't generally required because the operations are handled by MVC and Razor Pages.</span></span>

<span data-ttu-id="f67b8-108">Istnieją dwa abstrakcje treści żądania i odpowiedzi: <xref:System.IO.Stream> i. <xref:System.IO.Pipelines.Pipe></span><span class="sxs-lookup"><span data-stu-id="f67b8-108">There are two abstractions for the request and response bodies: <xref:System.IO.Stream> and <xref:System.IO.Pipelines.Pipe>.</span></span> <span data-ttu-id="f67b8-109">W przypadku odczytu żądania żądanie [HttpRequest. Body](xref:Microsoft.AspNetCore.Http.HttpRequest.Body) ma <xref:System.IO.Stream>wartość, `HttpRequest.BodyReader` a jest <xref:System.IO.Pipelines.PipeReader>.</span><span class="sxs-lookup"><span data-stu-id="f67b8-109">For request reading, [HttpRequest.Body](xref:Microsoft.AspNetCore.Http.HttpRequest.Body) is a <xref:System.IO.Stream>, and `HttpRequest.BodyReader` is a <xref:System.IO.Pipelines.PipeReader>.</span></span> <span data-ttu-id="f67b8-110">Do pisania odpowiedzi [HttpResponse. Body](xref:Microsoft.AspNetCore.Http.HttpResponse.Body) jest <xref:System.IO.Stream>i `HttpResponse.BodyWriter` <xref:System.IO.Pipelines.PipeWriter>.</span><span class="sxs-lookup"><span data-stu-id="f67b8-110">For response writing, [HttpResponse.Body](xref:Microsoft.AspNetCore.Http.HttpResponse.Body) is a <xref:System.IO.Stream>, and `HttpResponse.BodyWriter` is a <xref:System.IO.Pipelines.PipeWriter>.</span></span>

<span data-ttu-id="f67b8-111">[Potoki](/dotnet/standard/io/pipelines) są zalecane za pośrednictwem strumieni.</span><span class="sxs-lookup"><span data-stu-id="f67b8-111">[Pipelines](/dotnet/standard/io/pipelines) are recommended over streams.</span></span> <span data-ttu-id="f67b8-112">Strumienie mogą być łatwiejsze w przypadku niektórych prostych operacji, ale potoki mają zalety wydajności i są łatwiejsze w większości scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="f67b8-112">Streams can be easier to use for some simple operations, but pipelines have a performance advantage and are easier to use in most scenarios.</span></span> <span data-ttu-id="f67b8-113">ASP.NET Core zaczyna używać potoków zamiast strumieni wewnętrznie.</span><span class="sxs-lookup"><span data-stu-id="f67b8-113">ASP.NET Core is starting to use pipelines instead of streams internally.</span></span> <span data-ttu-id="f67b8-114">Przykłady:</span><span class="sxs-lookup"><span data-stu-id="f67b8-114">Examples include:</span></span>

* `FormReader`
* `TextReader`
* `TextWriter`
* `HttpResponse.WriteAsync`

<span data-ttu-id="f67b8-115">Strumienie nie są usuwane z struktury.</span><span class="sxs-lookup"><span data-stu-id="f67b8-115">Streams aren't being removed from the framework.</span></span> <span data-ttu-id="f67b8-116">Strumienie są nadal używane w środowisku .NET, a wiele typów strumieni nie ma odpowiedników potoku, `FileStreams` takich `ResponseCompression`jak i.</span><span class="sxs-lookup"><span data-stu-id="f67b8-116">Streams continue to be used throughout .NET, and many stream types don't have pipe equivalents, such as `FileStreams` and `ResponseCompression`.</span></span>

## <a name="stream-examples"></a><span data-ttu-id="f67b8-117">Przykłady przesyłania strumieniowego</span><span class="sxs-lookup"><span data-stu-id="f67b8-117">Stream examples</span></span>

<span data-ttu-id="f67b8-118">Załóżmy, że celem jest utworzenie oprogramowania pośredniczącego, które odczytuje całą treść żądania jako listę ciągów, dzieląc je na nowe wiersze.</span><span class="sxs-lookup"><span data-stu-id="f67b8-118">Suppose the goal is to create a middleware that reads the entire request body as a list of strings, splitting on new lines.</span></span> <span data-ttu-id="f67b8-119">Prosta implementacja strumienia może wyglądać podobnie do poniższego przykładu:</span><span class="sxs-lookup"><span data-stu-id="f67b8-119">A simple stream implementation might look like the following example:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStream)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="f67b8-120">Ten kod działa, ale wystąpiły pewne problemy:</span><span class="sxs-lookup"><span data-stu-id="f67b8-120">This code works, but there are some issues:</span></span>

* <span data-ttu-id="f67b8-121">Przed dołączeniem do `StringBuilder`, przykład tworzy kolejny ciąg (`encodedString`), który jest wyrzucany natychmiast.</span><span class="sxs-lookup"><span data-stu-id="f67b8-121">Before appending to the `StringBuilder`, the example creates another string (`encodedString`) that is thrown away immediately.</span></span> <span data-ttu-id="f67b8-122">Ten proces występuje dla wszystkich bajtów w strumieniu, więc wynikiem jest dodatkowa alokacja pamięci o rozmiarze całej treści żądania.</span><span class="sxs-lookup"><span data-stu-id="f67b8-122">This process occurs for all bytes in the stream, so the result is extra memory allocation the size of the entire request body.</span></span>
* <span data-ttu-id="f67b8-123">Przykład odczytuje cały ciąg przed podziałem w nowych wierszach.</span><span class="sxs-lookup"><span data-stu-id="f67b8-123">The example reads the entire string before splitting on new lines.</span></span> <span data-ttu-id="f67b8-124">Aby sprawdzić, czy nowe wiersze w tablicy bajtów są bardziej wydajne.</span><span class="sxs-lookup"><span data-stu-id="f67b8-124">It's more efficient to check for new lines in the byte array.</span></span>

<span data-ttu-id="f67b8-125">Oto przykład, który rozwiązuje niektóre z powyższych problemów:</span><span class="sxs-lookup"><span data-stu-id="f67b8-125">Here's an example that fixes some of the preceding issues:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStreamMoreEfficient)]

<span data-ttu-id="f67b8-126">Ten poprzedni przykład:</span><span class="sxs-lookup"><span data-stu-id="f67b8-126">This preceding example:</span></span>

* <span data-ttu-id="f67b8-127">Nie buforuje całej treści żądania w a `StringBuilder` , chyba że nie występują żadne znaki nowego wiersza.</span><span class="sxs-lookup"><span data-stu-id="f67b8-127">Doesn't buffer the entire request body in a `StringBuilder` unless there aren't any newline characters.</span></span>
* <span data-ttu-id="f67b8-128">Nie wywołuje `Split` ciągu.</span><span class="sxs-lookup"><span data-stu-id="f67b8-128">Doesn't call `Split` on the string.</span></span>

<span data-ttu-id="f67b8-129">Jednak nadal występują pewne problemy:</span><span class="sxs-lookup"><span data-stu-id="f67b8-129">However, there are still are a few issues:</span></span>

* <span data-ttu-id="f67b8-130">Jeśli znaki nowego wiersza są rozrzedzone, większość treści żądania jest buforowana w ciągu.</span><span class="sxs-lookup"><span data-stu-id="f67b8-130">If newline characters are sparse, much of the request body is buffered in the string.</span></span>
* <span data-ttu-id="f67b8-131">Kod nadal tworzy ciągi (`remainingString`) i dodaje je do buforu ciągów, co skutkuje dodatkową alokacją.</span><span class="sxs-lookup"><span data-stu-id="f67b8-131">The code continues to create strings (`remainingString`) and adds them to the string buffer, which results in an extra allocation.</span></span>

<span data-ttu-id="f67b8-132">Te problemy są fixable, ale kod staje się coraz bardziej skomplikowany przy niewielkim ulepszaniu.</span><span class="sxs-lookup"><span data-stu-id="f67b8-132">These issues are fixable, but the code is becoming progressively more complicated with little improvement.</span></span> <span data-ttu-id="f67b8-133">Potoki umożliwiają rozwiązanie tych problemów przy minimalnej złożoności kodu.</span><span class="sxs-lookup"><span data-stu-id="f67b8-133">Pipelines provide a way to solve these problems with minimal code complexity.</span></span>

## <a name="pipelines"></a><span data-ttu-id="f67b8-134">Potoki</span><span class="sxs-lookup"><span data-stu-id="f67b8-134">Pipelines</span></span>

<span data-ttu-id="f67b8-135">Poniższy przykład pokazuje, jak można obsłużyć ten sam scenariusz przy `PipeReader`użyciu:</span><span class="sxs-lookup"><span data-stu-id="f67b8-135">The following example shows how the same scenario can be handled using a `PipeReader`:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringFromPipe)]

<span data-ttu-id="f67b8-136">Ten przykład naprawia wiele problemów, które zostały wdrożone przez implementacje strumieni:</span><span class="sxs-lookup"><span data-stu-id="f67b8-136">This example fixes many issues that the streams implementations had:</span></span>

* <span data-ttu-id="f67b8-137">Nie ma potrzeby używania bufora ciągów, ponieważ `PipeReader` obsługuje on bajty, które nie były używane.</span><span class="sxs-lookup"><span data-stu-id="f67b8-137">There's no need for a string buffer because the `PipeReader` handles bytes that haven't been used.</span></span>
* <span data-ttu-id="f67b8-138">Zakodowane ciągi są bezpośrednio dodawane do listy zwracanych ciągów.</span><span class="sxs-lookup"><span data-stu-id="f67b8-138">Encoded strings are directly added to the list of returned strings.</span></span>
* <span data-ttu-id="f67b8-139">Tworzenie ciągów jest wolne od alokacji poza pamięcią używaną przez ciąg (z wyjątkiem `ToArray()` wywołania).</span><span class="sxs-lookup"><span data-stu-id="f67b8-139">String creation is allocation-free besides the memory used by the string (except the `ToArray()` call).</span></span>

## <a name="adapters"></a><span data-ttu-id="f67b8-140">Zainstalowanych</span><span class="sxs-lookup"><span data-stu-id="f67b8-140">Adapters</span></span>

<span data-ttu-id="f67b8-141">Obie `Body` właściwości `BodyReader/BodyWriter` i są dostępne dla `HttpRequest` i `HttpResponse`.</span><span class="sxs-lookup"><span data-stu-id="f67b8-141">Both `Body` and `BodyReader/BodyWriter` properties are available for `HttpRequest` and `HttpResponse`.</span></span> <span data-ttu-id="f67b8-142">Po ustawieniu `Body` innego strumienia nowy zestaw kart automatycznie dostosowuje każdy typ do drugiego.</span><span class="sxs-lookup"><span data-stu-id="f67b8-142">When you set `Body` to a different stream, a new set of adapters automatically adapt each type to the other.</span></span> <span data-ttu-id="f67b8-143">Jeśli ustawisz `HttpRequest.Body` nowy strumień, `HttpRequest.BodyReader` zostanie automatycznie ustawiony na nowe `PipeReader` Zawijanie. `HttpRequest.Body`</span><span class="sxs-lookup"><span data-stu-id="f67b8-143">If you set `HttpRequest.Body` to a new stream, `HttpRequest.BodyReader` is automatically set to a new `PipeReader` that wraps `HttpRequest.Body`.</span></span>

## <a name="startasync"></a><span data-ttu-id="f67b8-144">StartAsync</span><span class="sxs-lookup"><span data-stu-id="f67b8-144">StartAsync</span></span>

<span data-ttu-id="f67b8-145">`HttpResponse.StartAsync`służy do wskazywania, że nagłówki są niemodyfikowalne i `OnStarting` aby można było uruchamiać wywołania zwrotne.</span><span class="sxs-lookup"><span data-stu-id="f67b8-145">`HttpResponse.StartAsync` is used to indicate that headers are unmodifiable and to run `OnStarting` callbacks.</span></span> <span data-ttu-id="f67b8-146">Podczas używania `StartAsync` Kestrel jako serwera, wywołując przed użyciem `PipeReader` gwarancji, że pamięć zwrócona przez `GetMemory` należy do wewnętrznego <xref:System.IO.Pipelines.Pipe> , a nie do buforu zewnętrznego.</span><span class="sxs-lookup"><span data-stu-id="f67b8-146">When using Kestrel as a server, calling `StartAsync` before using the `PipeReader` guarantees that memory returned by `GetMemory` belongs to Kestrel's internal <xref:System.IO.Pipelines.Pipe> rather than an external buffer.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f67b8-147">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="f67b8-147">Additional resources</span></span>

* [<span data-ttu-id="f67b8-148">Wprowadzenie do System. IO. potoków</span><span class="sxs-lookup"><span data-stu-id="f67b8-148">Introducing System.IO.Pipelines</span></span>](https://devblogs.microsoft.com/dotnet/system-io-pipelines-high-performance-io-in-net/)
* <xref:fundamentals/middleware/write>
