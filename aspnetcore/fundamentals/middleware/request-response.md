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
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "96513112"
---
# <a name="request-and-response-operations-in-aspnet-core"></a><span data-ttu-id="cdd7c-103">Operacje żądań i odpowiedzi w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cdd7c-103">Request and response operations in ASP.NET Core</span></span>

<span data-ttu-id="cdd7c-104">Autor [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="cdd7c-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

<span data-ttu-id="cdd7c-105">W tym artykule wyjaśniono sposób odczytywania treści żądania i zapisywania jej w treści odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="cdd7c-105">This article explains how to read from the request body and write to the response body.</span></span> <span data-ttu-id="cdd7c-106">Kod dla tych operacji może być wymagany podczas pisania oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="cdd7c-106">Code for these operations might be required when writing middleware.</span></span> <span data-ttu-id="cdd7c-107">Poza pisaniem oprogramowania pośredniczącego kod niestandardowy nie jest zazwyczaj wymagany, ponieważ operacje są obsługiwane przez MVC i Razor strony.</span><span class="sxs-lookup"><span data-stu-id="cdd7c-107">Outside of writing middleware, custom code isn't generally required because the operations are handled by MVC and Razor Pages.</span></span>

<span data-ttu-id="cdd7c-108">Istnieją dwa abstrakcje treści żądania i odpowiedzi: <xref:System.IO.Stream> i <xref:System.IO.Pipelines.Pipe> .</span><span class="sxs-lookup"><span data-stu-id="cdd7c-108">There are two abstractions for the request and response bodies: <xref:System.IO.Stream> and <xref:System.IO.Pipelines.Pipe>.</span></span> <span data-ttu-id="cdd7c-109">Do odczytu żądania, <xref:Microsoft.AspNetCore.Http.HttpRequest.Body?displayProperty=nameWithType> is <xref:System.IO.Stream> i `HttpRequest.BodyReader` <xref:System.IO.Pipelines.PipeReader> .</span><span class="sxs-lookup"><span data-stu-id="cdd7c-109">For request reading, <xref:Microsoft.AspNetCore.Http.HttpRequest.Body?displayProperty=nameWithType> is a <xref:System.IO.Stream>, and `HttpRequest.BodyReader` is a <xref:System.IO.Pipelines.PipeReader>.</span></span> <span data-ttu-id="cdd7c-110">Na potrzeby pisania odpowiedzi <xref:Microsoft.AspNetCore.Http.HttpResponse.Body?displayProperty=nameWithType> jest <xref:System.IO.Stream> i `HttpResponse.BodyWriter` <xref:System.IO.Pipelines.PipeWriter> .</span><span class="sxs-lookup"><span data-stu-id="cdd7c-110">For response writing, <xref:Microsoft.AspNetCore.Http.HttpResponse.Body?displayProperty=nameWithType> is a <xref:System.IO.Stream>, and `HttpResponse.BodyWriter` is a <xref:System.IO.Pipelines.PipeWriter>.</span></span>

<span data-ttu-id="cdd7c-111">[Potoki](/dotnet/standard/io/pipelines) są zalecane za pośrednictwem strumieni.</span><span class="sxs-lookup"><span data-stu-id="cdd7c-111">[Pipelines](/dotnet/standard/io/pipelines) are recommended over streams.</span></span> <span data-ttu-id="cdd7c-112">Strumienie mogą być łatwiejsze w przypadku niektórych prostych operacji, ale potoki mają zalety wydajności i są łatwiejsze w większości scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="cdd7c-112">Streams can be easier to use for some simple operations, but pipelines have a performance advantage and are easier to use in most scenarios.</span></span> <span data-ttu-id="cdd7c-113">ASP.NET Core zaczyna używać potoków zamiast strumieni wewnętrznie.</span><span class="sxs-lookup"><span data-stu-id="cdd7c-113">ASP.NET Core is starting to use pipelines instead of streams internally.</span></span> <span data-ttu-id="cdd7c-114">Przykłady obejmują:</span><span class="sxs-lookup"><span data-stu-id="cdd7c-114">Examples include:</span></span>

* `FormReader`
* `TextReader`
* `TextWriter`
* `HttpResponse.WriteAsync`

<span data-ttu-id="cdd7c-115">Strumienie nie są usuwane z struktury.</span><span class="sxs-lookup"><span data-stu-id="cdd7c-115">Streams aren't being removed from the framework.</span></span> <span data-ttu-id="cdd7c-116">Strumienie są nadal używane w środowisku .NET, a wiele typów strumieni nie ma odpowiedników potoku, takich jak `FileStreams` i `ResponseCompression` .</span><span class="sxs-lookup"><span data-stu-id="cdd7c-116">Streams continue to be used throughout .NET, and many stream types don't have pipe equivalents, such as `FileStreams` and `ResponseCompression`.</span></span>

## <a name="stream-examples"></a><span data-ttu-id="cdd7c-117">Przykłady przesyłania strumieniowego</span><span class="sxs-lookup"><span data-stu-id="cdd7c-117">Stream examples</span></span>

<!-- see "fundamentals\middleware\request-response\static\TestPipes.JPG for testing sample -->

<span data-ttu-id="cdd7c-118">Załóżmy, że celem jest utworzenie oprogramowania pośredniczącego, które odczytuje całą treść żądania jako listę ciągów, dzieląc je na nowe wiersze.</span><span class="sxs-lookup"><span data-stu-id="cdd7c-118">Suppose the goal is to create a middleware that reads the entire request body as a list of strings, splitting on new lines.</span></span> <span data-ttu-id="cdd7c-119">Prosta implementacja strumienia może wyglądać podobnie do poniższego przykładu:</span><span class="sxs-lookup"><span data-stu-id="cdd7c-119">A simple stream implementation might look like the following example:</span></span>

> [!WARNING]
> <span data-ttu-id="cdd7c-120">Następujący kod:</span><span class="sxs-lookup"><span data-stu-id="cdd7c-120">The following code:</span></span>
> * <span data-ttu-id="cdd7c-121">Służy do zademonstrowania problemów nieużywających potoku w celu odczytania treści żądania.</span><span class="sxs-lookup"><span data-stu-id="cdd7c-121">Is used to demonstrate the problems with not using a pipe to read the request body.</span></span>
> * <span data-ttu-id="cdd7c-122">Nie jest przeznaczony do użycia w aplikacjach produkcyjnych.</span><span class="sxs-lookup"><span data-stu-id="cdd7c-122">Is not intended to be used in production apps.</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStream)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="cdd7c-123">Ten kod działa, ale wystąpiły pewne problemy:</span><span class="sxs-lookup"><span data-stu-id="cdd7c-123">This code works, but there are some issues:</span></span>

* <span data-ttu-id="cdd7c-124">Przed dołączeniem do `StringBuilder` , przykład tworzy kolejny ciąg ( `encodedString` ), który jest wyrzucany natychmiast.</span><span class="sxs-lookup"><span data-stu-id="cdd7c-124">Before appending to the `StringBuilder`, the example creates another string (`encodedString`) that is thrown away immediately.</span></span> <span data-ttu-id="cdd7c-125">Ten proces występuje dla wszystkich bajtów w strumieniu, więc wynikiem jest dodatkowa alokacja pamięci o rozmiarze całej treści żądania.</span><span class="sxs-lookup"><span data-stu-id="cdd7c-125">This process occurs for all bytes in the stream, so the result is extra memory allocation the size of the entire request body.</span></span>
* <span data-ttu-id="cdd7c-126">Przykład odczytuje cały ciąg przed podziałem w nowych wierszach.</span><span class="sxs-lookup"><span data-stu-id="cdd7c-126">The example reads the entire string before splitting on new lines.</span></span> <span data-ttu-id="cdd7c-127">Aby sprawdzić, czy nowe wiersze w tablicy bajtów są bardziej wydajne.</span><span class="sxs-lookup"><span data-stu-id="cdd7c-127">It's more efficient to check for new lines in the byte array.</span></span>

<span data-ttu-id="cdd7c-128">Oto przykład, który rozwiązuje niektóre z powyższych problemów:</span><span class="sxs-lookup"><span data-stu-id="cdd7c-128">Here's an example that fixes some of the preceding issues:</span></span>

> [!WARNING]
> <span data-ttu-id="cdd7c-129">Następujący kod:</span><span class="sxs-lookup"><span data-stu-id="cdd7c-129">The following code:</span></span>
> * <span data-ttu-id="cdd7c-130">Służy do zademonstrowania rozwiązań niektórych problemów występujących w poprzednim kodzie bez rozwiązywania wszystkich problemów.</span><span class="sxs-lookup"><span data-stu-id="cdd7c-130">Is used to demonstrate the solutions to some problems in the preceding code while not solving all the problems.</span></span>
> * <span data-ttu-id="cdd7c-131">Nie jest przeznaczony do użycia w aplikacjach produkcyjnych.</span><span class="sxs-lookup"><span data-stu-id="cdd7c-131">Is not intended to be used in production apps.</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStreamMoreEfficient)]

<span data-ttu-id="cdd7c-132">Ten poprzedni przykład:</span><span class="sxs-lookup"><span data-stu-id="cdd7c-132">This preceding example:</span></span>

* <span data-ttu-id="cdd7c-133">Nie buforuje całej treści żądania w a, `StringBuilder` chyba że nie występują żadne znaki nowego wiersza.</span><span class="sxs-lookup"><span data-stu-id="cdd7c-133">Doesn't buffer the entire request body in a `StringBuilder` unless there aren't any newline characters.</span></span>
* <span data-ttu-id="cdd7c-134">Nie wywołuje `Split` ciągu.</span><span class="sxs-lookup"><span data-stu-id="cdd7c-134">Doesn't call `Split` on the string.</span></span>

<span data-ttu-id="cdd7c-135">Jednak nadal występują pewne problemy:</span><span class="sxs-lookup"><span data-stu-id="cdd7c-135">However, there are still are a few issues:</span></span>

* <span data-ttu-id="cdd7c-136">Jeśli znaki nowego wiersza są rozrzedzone, większość treści żądania jest buforowana w ciągu.</span><span class="sxs-lookup"><span data-stu-id="cdd7c-136">If newline characters are sparse, much of the request body is buffered in the string.</span></span>
* <span data-ttu-id="cdd7c-137">Kod nadal tworzy ciągi ( `remainingString` ) i dodaje je do buforu ciągów, co skutkuje dodatkową alokacją.</span><span class="sxs-lookup"><span data-stu-id="cdd7c-137">The code continues to create strings (`remainingString`) and adds them to the string buffer, which results in an extra allocation.</span></span>

<span data-ttu-id="cdd7c-138">Te problemy są fixable, ale kod staje się coraz bardziej skomplikowany przy niewielkim ulepszaniu.</span><span class="sxs-lookup"><span data-stu-id="cdd7c-138">These issues are fixable, but the code is becoming progressively more complicated with little improvement.</span></span> <span data-ttu-id="cdd7c-139">Potoki umożliwiają rozwiązanie tych problemów przy minimalnej złożoności kodu.</span><span class="sxs-lookup"><span data-stu-id="cdd7c-139">Pipelines provide a way to solve these problems with minimal code complexity.</span></span>

## <a name="pipelines"></a><span data-ttu-id="cdd7c-140">Pipelines</span><span class="sxs-lookup"><span data-stu-id="cdd7c-140">Pipelines</span></span>

<span data-ttu-id="cdd7c-141">Poniższy przykład pokazuje, jak można obsłużyć ten sam scenariusz przy użyciu [PipeReader](/dotnet/standard/io/pipelines#pipe):</span><span class="sxs-lookup"><span data-stu-id="cdd7c-141">The following example shows how the same scenario can be handled using a [PipeReader](/dotnet/standard/io/pipelines#pipe):</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringFromPipe)]

<span data-ttu-id="cdd7c-142">Ten przykład naprawia wiele problemów, które zostały wdrożone przez implementacje strumieni:</span><span class="sxs-lookup"><span data-stu-id="cdd7c-142">This example fixes many issues that the streams implementations had:</span></span>

* <span data-ttu-id="cdd7c-143">Nie ma potrzeby używania bufora ciągów `PipeReader` , ponieważ obsługuje on bajty, które nie były używane.</span><span class="sxs-lookup"><span data-stu-id="cdd7c-143">There's no need for a string buffer because the `PipeReader` handles bytes that haven't been used.</span></span>
* <span data-ttu-id="cdd7c-144">Zakodowane ciągi są bezpośrednio dodawane do listy zwracanych ciągów.</span><span class="sxs-lookup"><span data-stu-id="cdd7c-144">Encoded strings are directly added to the list of returned strings.</span></span>
* <span data-ttu-id="cdd7c-145">Oprócz `ToArray` wywołania i pamięci używanej przez ciąg, tworzenie ciągów jest bezpłatne.</span><span class="sxs-lookup"><span data-stu-id="cdd7c-145">Other than the `ToArray` call, and the memory used by the string, string creation is allocation free.</span></span>

## <a name="adapters"></a><span data-ttu-id="cdd7c-146">Zainstalowanych</span><span class="sxs-lookup"><span data-stu-id="cdd7c-146">Adapters</span></span>

<span data-ttu-id="cdd7c-147">`Body`Właściwości, `BodyReader` i `BodyWriter` są dostępne dla `HttpRequest` i `HttpResponse` .</span><span class="sxs-lookup"><span data-stu-id="cdd7c-147">The `Body`, `BodyReader`, and `BodyWriter` properties are available for `HttpRequest` and `HttpResponse`.</span></span> <span data-ttu-id="cdd7c-148">Po ustawieniu `Body` innego strumienia nowy zestaw kart automatycznie dostosowuje każdy typ do drugiego.</span><span class="sxs-lookup"><span data-stu-id="cdd7c-148">When you set `Body` to a different stream, a new set of adapters automatically adapt each type to the other.</span></span> <span data-ttu-id="cdd7c-149">Jeśli ustawisz `HttpRequest.Body` Nowy strumień, `HttpRequest.BodyReader` zostanie automatycznie ustawiony na nowe `PipeReader` Zawijanie `HttpRequest.Body` .</span><span class="sxs-lookup"><span data-stu-id="cdd7c-149">If you set `HttpRequest.Body` to a new stream, `HttpRequest.BodyReader` is automatically set to a new `PipeReader` that wraps `HttpRequest.Body`.</span></span>

## <a name="startasync"></a><span data-ttu-id="cdd7c-150">StartAsync</span><span class="sxs-lookup"><span data-stu-id="cdd7c-150">StartAsync</span></span>

<span data-ttu-id="cdd7c-151">`HttpResponse.StartAsync` służy do wskazywania, że nagłówki są niemodyfikowalne i aby można było uruchamiać `OnStarting` wywołania zwrotne.</span><span class="sxs-lookup"><span data-stu-id="cdd7c-151">`HttpResponse.StartAsync` is used to indicate that headers are unmodifiable and to run `OnStarting` callbacks.</span></span> <span data-ttu-id="cdd7c-152">Podczas używania Kestrel jako serwera, wywołując `StartAsync` przed użyciem `PipeReader` gwarancji, że pamięć zwrócona przez `GetMemory` należy do wewnętrznego, <xref:System.IO.Pipelines.Pipe> a nie do buforu zewnętrznego.</span><span class="sxs-lookup"><span data-stu-id="cdd7c-152">When using Kestrel as a server, calling `StartAsync` before using the `PipeReader` guarantees that memory returned by `GetMemory` belongs to Kestrel's internal <xref:System.IO.Pipelines.Pipe> rather than an external buffer.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cdd7c-153">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="cdd7c-153">Additional resources</span></span>

* [<span data-ttu-id="cdd7c-154">System. IO. potoki w środowisku .NET</span><span class="sxs-lookup"><span data-stu-id="cdd7c-154">System.IO.Pipelines in .NET</span></span>](/dotnet/standard/io/pipelines)
* <xref:fundamentals/middleware/write>

<!-- Test with Postman or other tool. See image in static directory. -->
