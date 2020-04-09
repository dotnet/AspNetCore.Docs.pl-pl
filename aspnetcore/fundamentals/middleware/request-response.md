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
# <a name="request-and-response-operations-in-aspnet-core"></a><span data-ttu-id="9616c-103">Operacje żądania i odpowiedzi w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9616c-103">Request and response operations in ASP.NET Core</span></span>

<span data-ttu-id="9616c-104">Przez [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="9616c-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

<span data-ttu-id="9616c-105">W tym artykule wyjaśniono, jak odczytać z treści żądania i napisać do treści odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="9616c-105">This article explains how to read from the request body and write to the response body.</span></span> <span data-ttu-id="9616c-106">Kod dla tych operacji może być wymagane podczas pisania oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="9616c-106">Code for these operations might be required when writing middleware.</span></span> <span data-ttu-id="9616c-107">Poza pisaniem oprogramowania pośredniczącego kod niestandardowy nie jest zazwyczaj wymagany, ponieważ operacje są obsługiwane przez strony MVC i Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="9616c-107">Outside of writing middleware, custom code isn't generally required because the operations are handled by MVC and Razor Pages.</span></span>

<span data-ttu-id="9616c-108">Istnieją dwie abstrakcje dla żądań <xref:System.IO.Stream> <xref:System.IO.Pipelines.Pipe>i organów odpowiedzi: i .</span><span class="sxs-lookup"><span data-stu-id="9616c-108">There are two abstractions for the request and response bodies: <xref:System.IO.Stream> and <xref:System.IO.Pipelines.Pipe>.</span></span> <span data-ttu-id="9616c-109">Do odczytu żądania, [HttpRequest.Body](xref:Microsoft.AspNetCore.Http.HttpRequest.Body) jest <xref:System.IO.Stream>, i `HttpRequest.BodyReader` jest . <xref:System.IO.Pipelines.PipeReader></span><span class="sxs-lookup"><span data-stu-id="9616c-109">For request reading, [HttpRequest.Body](xref:Microsoft.AspNetCore.Http.HttpRequest.Body) is a <xref:System.IO.Stream>, and `HttpRequest.BodyReader` is a <xref:System.IO.Pipelines.PipeReader>.</span></span> <span data-ttu-id="9616c-110">Do pisania [odpowiedzi, HttpResponse.Body](xref:Microsoft.AspNetCore.Http.HttpResponse.Body) `HttpResponse.BodyWriter` jest <xref:System.IO.Pipelines.PipeWriter> <xref:System.IO.Stream>, i jest .</span><span class="sxs-lookup"><span data-stu-id="9616c-110">For response writing, [HttpResponse.Body](xref:Microsoft.AspNetCore.Http.HttpResponse.Body) is a <xref:System.IO.Stream>, and `HttpResponse.BodyWriter` is a <xref:System.IO.Pipelines.PipeWriter>.</span></span>

<span data-ttu-id="9616c-111">[Potoki](/dotnet/standard/io/pipelines) są zalecane za 100 000 000 000 000</span><span class="sxs-lookup"><span data-stu-id="9616c-111">[Pipelines](/dotnet/standard/io/pipelines) are recommended over streams.</span></span> <span data-ttu-id="9616c-112">Strumienie mogą być łatwiejsze w użyciu dla niektórych prostych operacji, ale potoki mają przewagę wydajności i są łatwiejsze w użyciu w większości scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="9616c-112">Streams can be easier to use for some simple operations, but pipelines have a performance advantage and are easier to use in most scenarios.</span></span> <span data-ttu-id="9616c-113">ASP.NET Core zaczyna używać potoków zamiast strumieni wewnętrznie.</span><span class="sxs-lookup"><span data-stu-id="9616c-113">ASP.NET Core is starting to use pipelines instead of streams internally.</span></span> <span data-ttu-id="9616c-114">Przykłady:</span><span class="sxs-lookup"><span data-stu-id="9616c-114">Examples include:</span></span>

* `FormReader`
* `TextReader`
* `TextWriter`
* `HttpResponse.WriteAsync`

<span data-ttu-id="9616c-115">Strumienie nie są usuwane z struktury.</span><span class="sxs-lookup"><span data-stu-id="9616c-115">Streams aren't being removed from the framework.</span></span> <span data-ttu-id="9616c-116">Strumienie są nadal używane w całej .NET, a wiele typów strumieni `FileStreams` nie `ResponseCompression`ma odpowiedników potoków, takich jak i .</span><span class="sxs-lookup"><span data-stu-id="9616c-116">Streams continue to be used throughout .NET, and many stream types don't have pipe equivalents, such as `FileStreams` and `ResponseCompression`.</span></span>

## <a name="stream-examples"></a><span data-ttu-id="9616c-117">Przykłady strumienia</span><span class="sxs-lookup"><span data-stu-id="9616c-117">Stream examples</span></span>

<span data-ttu-id="9616c-118">Załóżmy, że celem jest utworzenie oprogramowania pośredniczącego, które odczytuje całą treść żądania jako listę ciągów, dzieląc się na nowe wiersze.</span><span class="sxs-lookup"><span data-stu-id="9616c-118">Suppose the goal is to create a middleware that reads the entire request body as a list of strings, splitting on new lines.</span></span> <span data-ttu-id="9616c-119">Implementacja prostego strumienia może wyglądać następująco:</span><span class="sxs-lookup"><span data-stu-id="9616c-119">A simple stream implementation might look like the following example:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStream)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="9616c-120">Ten kod działa, ale istnieją pewne problemy:</span><span class="sxs-lookup"><span data-stu-id="9616c-120">This code works, but there are some issues:</span></span>

* <span data-ttu-id="9616c-121">Przed dołączeniem do `StringBuilder`, przykład tworzy`encodedString`inny ciąg ( ), który jest natychmiast wyrzucany.</span><span class="sxs-lookup"><span data-stu-id="9616c-121">Before appending to the `StringBuilder`, the example creates another string (`encodedString`) that is thrown away immediately.</span></span> <span data-ttu-id="9616c-122">Ten proces występuje dla wszystkich bajtów w strumieniu, więc wynik jest alokacja pamięci dodatkowe rozmiar całej treści żądania.</span><span class="sxs-lookup"><span data-stu-id="9616c-122">This process occurs for all bytes in the stream, so the result is extra memory allocation the size of the entire request body.</span></span>
* <span data-ttu-id="9616c-123">W przykładzie odczytuje cały ciąg przed podziałem na nowe wiersze.</span><span class="sxs-lookup"><span data-stu-id="9616c-123">The example reads the entire string before splitting on new lines.</span></span> <span data-ttu-id="9616c-124">Bardziej wydajne jest sprawdzanie nowych wierszy w tablicy bajtów.</span><span class="sxs-lookup"><span data-stu-id="9616c-124">It's more efficient to check for new lines in the byte array.</span></span>

<span data-ttu-id="9616c-125">Oto przykład, który rozwiązuje niektóre z powyższych problemów:</span><span class="sxs-lookup"><span data-stu-id="9616c-125">Here's an example that fixes some of the preceding issues:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStreamMoreEfficient)]

<span data-ttu-id="9616c-126">Ten poprzedni przykład:</span><span class="sxs-lookup"><span data-stu-id="9616c-126">This preceding example:</span></span>

* <span data-ttu-id="9616c-127">Nie buforuje całego treści żądania `StringBuilder` w a, chyba że nie ma żadnych znaków nowego pliku.</span><span class="sxs-lookup"><span data-stu-id="9616c-127">Doesn't buffer the entire request body in a `StringBuilder` unless there aren't any newline characters.</span></span>
* <span data-ttu-id="9616c-128">Nie wywołanie `Split` ciągu.</span><span class="sxs-lookup"><span data-stu-id="9616c-128">Doesn't call `Split` on the string.</span></span>

<span data-ttu-id="9616c-129">Jednak nadal istnieje kilka problemów:</span><span class="sxs-lookup"><span data-stu-id="9616c-129">However, there are still are a few issues:</span></span>

* <span data-ttu-id="9616c-130">Jeśli znaki nowego typu są rozrzedzone, większość treści żądania jest buforowana w ciągu.</span><span class="sxs-lookup"><span data-stu-id="9616c-130">If newline characters are sparse, much of the request body is buffered in the string.</span></span>
* <span data-ttu-id="9616c-131">Kod kontynuuje tworzenie ciągów`remainingString`( ) i dodaje je do buforu ciągu, co powoduje dodatkową alokację.</span><span class="sxs-lookup"><span data-stu-id="9616c-131">The code continues to create strings (`remainingString`) and adds them to the string buffer, which results in an extra allocation.</span></span>

<span data-ttu-id="9616c-132">Te problemy można naprawić, ale kod staje się coraz bardziej skomplikowane z niewielkim udoskonaleniem.</span><span class="sxs-lookup"><span data-stu-id="9616c-132">These issues are fixable, but the code is becoming progressively more complicated with little improvement.</span></span> <span data-ttu-id="9616c-133">Potoki umożliwiają rozwiązanie tych problemów przy minimalnej złożoności kodu.</span><span class="sxs-lookup"><span data-stu-id="9616c-133">Pipelines provide a way to solve these problems with minimal code complexity.</span></span>

## <a name="pipelines"></a><span data-ttu-id="9616c-134">Potoki</span><span class="sxs-lookup"><span data-stu-id="9616c-134">Pipelines</span></span>

<span data-ttu-id="9616c-135">W poniższym przykładzie pokazano, jak ten `PipeReader`sam scenariusz może być obsługiwany przy użyciu:</span><span class="sxs-lookup"><span data-stu-id="9616c-135">The following example shows how the same scenario can be handled using a `PipeReader`:</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringFromPipe)]

<span data-ttu-id="9616c-136">W tym przykładzie rozwiązuje wiele problemów, które implementacje strumieni miał:</span><span class="sxs-lookup"><span data-stu-id="9616c-136">This example fixes many issues that the streams implementations had:</span></span>

* <span data-ttu-id="9616c-137">Nie ma potrzeby buforu ciągów, ponieważ `PipeReader` dojścia bajtów, które nie zostały użyte.</span><span class="sxs-lookup"><span data-stu-id="9616c-137">There's no need for a string buffer because the `PipeReader` handles bytes that haven't been used.</span></span>
* <span data-ttu-id="9616c-138">Zakodowane ciągi są bezpośrednio dodawane do listy zwróconych ciągów.</span><span class="sxs-lookup"><span data-stu-id="9616c-138">Encoded strings are directly added to the list of returned strings.</span></span>
* <span data-ttu-id="9616c-139">Tworzenie ciągów jest wolne od alokacji oprócz `ToArray()` pamięci używanej przez ciąg (z wyjątkiem wywołania).</span><span class="sxs-lookup"><span data-stu-id="9616c-139">String creation is allocation-free besides the memory used by the string (except the `ToArray()` call).</span></span>

## <a name="adapters"></a><span data-ttu-id="9616c-140">Karty</span><span class="sxs-lookup"><span data-stu-id="9616c-140">Adapters</span></span>

<span data-ttu-id="9616c-141">Zarówno `Body` `BodyReader/BodyWriter` właściwości, jak `HttpRequest` i `HttpResponse`właściwości są dostępne dla i .</span><span class="sxs-lookup"><span data-stu-id="9616c-141">Both `Body` and `BodyReader/BodyWriter` properties are available for `HttpRequest` and `HttpResponse`.</span></span> <span data-ttu-id="9616c-142">Po ustawieniu `Body` innego strumienia nowy zestaw kart automatycznie dostosowuje każdy typ do drugiego.</span><span class="sxs-lookup"><span data-stu-id="9616c-142">When you set `Body` to a different stream, a new set of adapters automatically adapt each type to the other.</span></span> <span data-ttu-id="9616c-143">Jeśli `HttpRequest.Body` ustawisz nowy `HttpRequest.BodyReader` strumień, zostanie automatycznie `PipeReader` ustawiona `HttpRequest.Body`na nowy, który zawija .</span><span class="sxs-lookup"><span data-stu-id="9616c-143">If you set `HttpRequest.Body` to a new stream, `HttpRequest.BodyReader` is automatically set to a new `PipeReader` that wraps `HttpRequest.Body`.</span></span>

## <a name="startasync"></a><span data-ttu-id="9616c-144">StartAsync (StartAsync)</span><span class="sxs-lookup"><span data-stu-id="9616c-144">StartAsync</span></span>

<span data-ttu-id="9616c-145">`HttpResponse.StartAsync`służy do oznaczania, że nagłówki są niemodifiable i do uruchamiania `OnStarting` wywołań zwrotnych.</span><span class="sxs-lookup"><span data-stu-id="9616c-145">`HttpResponse.StartAsync` is used to indicate that headers are unmodifiable and to run `OnStarting` callbacks.</span></span> <span data-ttu-id="9616c-146">Podczas korzystania z Kestrel `StartAsync` jako serwera, wywołanie przed użyciem `PipeReader` gwarancji, że pamięć zwracana przez `GetMemory` należy do pustułki wewnętrznej, <xref:System.IO.Pipelines.Pipe> a nie zewnętrznego buforu.</span><span class="sxs-lookup"><span data-stu-id="9616c-146">When using Kestrel as a server, calling `StartAsync` before using the `PipeReader` guarantees that memory returned by `GetMemory` belongs to Kestrel's internal <xref:System.IO.Pipelines.Pipe> rather than an external buffer.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9616c-147">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="9616c-147">Additional resources</span></span>

* [<span data-ttu-id="9616c-148">Przedstawiamy system.IO.Pipelines</span><span class="sxs-lookup"><span data-stu-id="9616c-148">Introducing System.IO.Pipelines</span></span>](https://devblogs.microsoft.com/dotnet/system-io-pipelines-high-performance-io-in-net/)
* <xref:fundamentals/middleware/write>
