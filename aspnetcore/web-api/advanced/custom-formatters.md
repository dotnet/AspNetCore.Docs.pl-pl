---
title: Niestandardowe elementy formatujące w ASP.NET Core Web API
author: rick-anderson
description: Dowiedz się, jak tworzyć i używać niestandardowych elementów formatujących dla interfejsów API sieci Web w programie ASP.NET Core.
ms.author: riande
ms.date: 02/08/2017
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/advanced/custom-formatters
ms.openlocfilehash: 0836fc288a015adb9a6223c5a2b681b1b03bded4
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777322"
---
# <a name="custom-formatters-in-aspnet-core-web-api"></a><span data-ttu-id="b3cd1-103">Niestandardowe elementy formatujące w ASP.NET Core Web API</span><span class="sxs-lookup"><span data-stu-id="b3cd1-103">Custom formatters in ASP.NET Core Web API</span></span>

<span data-ttu-id="b3cd1-104">Autor [Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="b3cd1-104">By [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="b3cd1-105">ASP.NET Core MVC obsługuje wymianę danych w interfejsach API sieci Web przy użyciu danych wejściowych i wyjściowych.</span><span class="sxs-lookup"><span data-stu-id="b3cd1-105">ASP.NET Core MVC supports data exchange in Web APIs using input and output formatters.</span></span> <span data-ttu-id="b3cd1-106">Wejściowe elementy formatującego są używane przez [powiązanie modelu](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="b3cd1-106">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="b3cd1-107">Wyjściowe elementy formatujące są używane do [formatowania odpowiedzi](xref:web-api/advanced/formatting).</span><span class="sxs-lookup"><span data-stu-id="b3cd1-107">Output formatters are used to [format responses](xref:web-api/advanced/formatting).</span></span>

<span data-ttu-id="b3cd1-108">Struktura zawiera wbudowane, wejściowe i wyjściowe elementy formatujące dla JSON i XML.</span><span class="sxs-lookup"><span data-stu-id="b3cd1-108">The framework provides built-in input and output formatters for JSON and XML.</span></span> <span data-ttu-id="b3cd1-109">Udostępnia wbudowany program formatujący dane wyjściowe dla zwykłego tekstu, ale nie udostępnia wejściowego programu formatującego dla zwykłego tekstu.</span><span class="sxs-lookup"><span data-stu-id="b3cd1-109">It provides a built-in output formatter for plain text, but doesn't provide an input formatter for plain text.</span></span>

<span data-ttu-id="b3cd1-110">W tym artykule pokazano, jak dodać obsługę dodatkowych formatów, tworząc niestandardowe elementy formatujące.</span><span class="sxs-lookup"><span data-stu-id="b3cd1-110">This article shows how to add support for additional formats by creating custom formatters.</span></span> <span data-ttu-id="b3cd1-111">Aby zapoznać się z przykładem niestandardowego wejściowego programu formatującego dla zwykłego tekstu, zobacz [TextPlainInputFormatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) w witrynie GitHub.</span><span class="sxs-lookup"><span data-stu-id="b3cd1-111">For an example of a custom input formatter for plain text, see [TextPlainInputFormatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) on GitHub.</span></span>

<span data-ttu-id="b3cd1-112">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b3cd1-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-custom-formatters"></a><span data-ttu-id="b3cd1-113">Kiedy używać niestandardowych elementów formatujących</span><span class="sxs-lookup"><span data-stu-id="b3cd1-113">When to use custom formatters</span></span>

<span data-ttu-id="b3cd1-114">Użyj niestandardowego programu formatującego, jeśli chcesz, aby proces [negocjacji zawartości](xref:web-api/advanced/formatting#content-negotiation) obsługiwał typ zawartości, który nie jest obsługiwany przez wbudowane elementy formatujące.</span><span class="sxs-lookup"><span data-stu-id="b3cd1-114">Use a custom formatter when you want the [content negotiation](xref:web-api/advanced/formatting#content-negotiation) process to support a content type that isn't supported by the built-in formatters.</span></span>

<span data-ttu-id="b3cd1-115">Na przykład jeśli niektórzy klienci dla internetowego interfejsu API mogą obsłużyć format [protobuf](https://github.com/google/protobuf) , możesz chcieć użyć protobuf z tymi klientami, ponieważ jest to bardziej wydajne.</span><span class="sxs-lookup"><span data-stu-id="b3cd1-115">For example, if some of the clients for your web API can handle the [Protobuf](https://github.com/google/protobuf) format, you might want to use Protobuf with those clients because it's more efficient.</span></span> <span data-ttu-id="b3cd1-116">Możesz też chcieć, aby internetowy interfejs API wysyłał nazwy i adresy kontaktów w formacie [vCard](https://wikipedia.org/wiki/VCard) , powszechnie używany format do wymiany danych kontaktowych.</span><span class="sxs-lookup"><span data-stu-id="b3cd1-116">Or you might want your web API to send contact names and addresses in [vCard](https://wikipedia.org/wiki/VCard) format, a commonly used format for exchanging contact data.</span></span> <span data-ttu-id="b3cd1-117">Przykładowa aplikacja udostępniona w tym artykule implementuje prosty program formatujący vCard.</span><span class="sxs-lookup"><span data-stu-id="b3cd1-117">The sample app provided with this article implements a simple vCard formatter.</span></span>

## <a name="overview-of-how-to-use-a-custom-formatter"></a><span data-ttu-id="b3cd1-118">Omówienie korzystania z niestandardowego programu formatującego</span><span class="sxs-lookup"><span data-stu-id="b3cd1-118">Overview of how to use a custom formatter</span></span>

<span data-ttu-id="b3cd1-119">Poniżej przedstawiono procedurę tworzenia i używania niestandardowego programu formatującego:</span><span class="sxs-lookup"><span data-stu-id="b3cd1-119">Here are the steps to create and use a custom formatter:</span></span>

* <span data-ttu-id="b3cd1-120">Utwórz wyjściową klasę programu formatującego, jeśli chcesz serializować dane do wysłania do klienta.</span><span class="sxs-lookup"><span data-stu-id="b3cd1-120">Create an output formatter class if you want to serialize data to send to the client.</span></span>
* <span data-ttu-id="b3cd1-121">Utwórz wejściową klasę programu formatującego, jeśli chcesz zdeserializować dane odebrane od klienta.</span><span class="sxs-lookup"><span data-stu-id="b3cd1-121">Create an input formatter class if you want to deserialize data received from the client.</span></span>
* <span data-ttu-id="b3cd1-122">Dodaj wystąpienia elementów formatujących do kolekcji `InputFormatters` i `OutputFormatters` w [MvcOptions](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions).</span><span class="sxs-lookup"><span data-stu-id="b3cd1-122">Add instances of your formatters to the `InputFormatters` and `OutputFormatters` collections in [MvcOptions](/dotnet/api/microsoft.aspnetcore.mvc.mvcoptions).</span></span>

<span data-ttu-id="b3cd1-123">W poniższych sekcjach przedstawiono wskazówki i przykłady kodu dla każdego z tych kroków.</span><span class="sxs-lookup"><span data-stu-id="b3cd1-123">The following sections provide guidance and code examples for each of these steps.</span></span>

## <a name="how-to-create-a-custom-formatter-class"></a><span data-ttu-id="b3cd1-124">Jak utworzyć niestandardową klasę programu formatującego</span><span class="sxs-lookup"><span data-stu-id="b3cd1-124">How to create a custom formatter class</span></span>

<span data-ttu-id="b3cd1-125">Aby utworzyć program formatujący:</span><span class="sxs-lookup"><span data-stu-id="b3cd1-125">To create a formatter:</span></span>

* <span data-ttu-id="b3cd1-126">Utwórz klasę z odpowiedniej klasy bazowej.</span><span class="sxs-lookup"><span data-stu-id="b3cd1-126">Derive the class from the appropriate base class.</span></span>
* <span data-ttu-id="b3cd1-127">Określ prawidłowe typy nośników i kodowania w konstruktorze.</span><span class="sxs-lookup"><span data-stu-id="b3cd1-127">Specify valid media types and encodings in the constructor.</span></span>
* <span data-ttu-id="b3cd1-128">Metody `CanReadType` / `CanWriteType` przesłonięcia</span><span class="sxs-lookup"><span data-stu-id="b3cd1-128">Override `CanReadType`/`CanWriteType` methods</span></span>
* <span data-ttu-id="b3cd1-129">Metody `ReadRequestBodyAsync` / `WriteResponseBodyAsync` przesłonięcia</span><span class="sxs-lookup"><span data-stu-id="b3cd1-129">Override `ReadRequestBodyAsync`/`WriteResponseBodyAsync` methods</span></span>
  
### <a name="derive-from-the-appropriate-base-class"></a><span data-ttu-id="b3cd1-130">Pochodny od odpowiedniej klasy bazowej</span><span class="sxs-lookup"><span data-stu-id="b3cd1-130">Derive from the appropriate base class</span></span>

<span data-ttu-id="b3cd1-131">W przypadku typów multimediów tekstowych (na przykład vCard) pochodzi z klasy bazowej [TextInputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) lub [TextOutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter) .</span><span class="sxs-lookup"><span data-stu-id="b3cd1-131">For text media types (for example, vCard), derive from the [TextInputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) or [TextOutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter) base class.</span></span>

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=classdef)]

<span data-ttu-id="b3cd1-132">Przykład danych wejściowych programu formatującego można znaleźć w [aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample)przykładowej.</span><span class="sxs-lookup"><span data-stu-id="b3cd1-132">For an input formatter example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span></span>

<span data-ttu-id="b3cd1-133">W przypadku typów binarnych pochodzi z klasy bazowej [InputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.inputformatter) lub [OutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformatter) .</span><span class="sxs-lookup"><span data-stu-id="b3cd1-133">For binary types, derive from the [InputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.inputformatter) or [OutputFormatter](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformatter) base class.</span></span>

### <a name="specify-valid-media-types-and-encodings"></a><span data-ttu-id="b3cd1-134">Określ prawidłowe typy multimediów i kodowania</span><span class="sxs-lookup"><span data-stu-id="b3cd1-134">Specify valid media types and encodings</span></span>

<span data-ttu-id="b3cd1-135">W konstruktorze Określ prawidłowe typy nośników i kodowania przez dodanie do kolekcji `SupportedMediaTypes` i. `SupportedEncodings`</span><span class="sxs-lookup"><span data-stu-id="b3cd1-135">In the constructor, specify valid media types and encodings by adding to the `SupportedMediaTypes` and `SupportedEncodings` collections.</span></span>

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=ctor&highlight=3,5-6)]

<span data-ttu-id="b3cd1-136">Przykład danych wejściowych programu formatującego można znaleźć w [aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample)przykładowej.</span><span class="sxs-lookup"><span data-stu-id="b3cd1-136">For an input formatter example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span></span>

> [!NOTE]
> <span data-ttu-id="b3cd1-137">Nie można wykonać iniekcji zależności konstruktora w klasie programu formatującego.</span><span class="sxs-lookup"><span data-stu-id="b3cd1-137">You can't do constructor dependency injection in a formatter class.</span></span> <span data-ttu-id="b3cd1-138">Na przykład nie można uzyskać rejestratora, dodając parametr rejestratora do konstruktora.</span><span class="sxs-lookup"><span data-stu-id="b3cd1-138">For example, you can't get a logger by adding a logger parameter to the constructor.</span></span> <span data-ttu-id="b3cd1-139">Aby uzyskać dostęp do usług, należy użyć obiektu kontekstu, który jest przesyłany do Twoich metod.</span><span class="sxs-lookup"><span data-stu-id="b3cd1-139">To access services, you have to use the context object that gets passed in to your methods.</span></span> <span data-ttu-id="b3cd1-140">W [poniższym](#read-write) przykładzie kodu pokazano, jak to zrobić.</span><span class="sxs-lookup"><span data-stu-id="b3cd1-140">A code example [below](#read-write) shows how to do this.</span></span>

### <a name="override-canreadtypecanwritetype"></a><span data-ttu-id="b3cd1-141">Zastąp element overridetype/unwritetype</span><span class="sxs-lookup"><span data-stu-id="b3cd1-141">Override CanReadType/CanWriteType</span></span>

<span data-ttu-id="b3cd1-142">Określ typ, do którego można deserializować lub serializować z, zastępując `CanReadType` metody `CanWriteType` lub.</span><span class="sxs-lookup"><span data-stu-id="b3cd1-142">Specify the type you can deserialize into or serialize from by overriding the `CanReadType` or `CanWriteType` methods.</span></span> <span data-ttu-id="b3cd1-143">Na przykład możesz mieć możliwość tworzenia tylko tekstu w formacie vCard z typu i `Contact` na odwrót.</span><span class="sxs-lookup"><span data-stu-id="b3cd1-143">For example, you might only be able to create vCard text from a `Contact` type and vice versa.</span></span>

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=canwritetype)]

<span data-ttu-id="b3cd1-144">Przykład danych wejściowych programu formatującego można znaleźć w [aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample)przykładowej.</span><span class="sxs-lookup"><span data-stu-id="b3cd1-144">For an input formatter example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span></span>

#### <a name="the-canwriteresult-method"></a><span data-ttu-id="b3cd1-145">Metoda CanWriteResult</span><span class="sxs-lookup"><span data-stu-id="b3cd1-145">The CanWriteResult method</span></span>

<span data-ttu-id="b3cd1-146">W niektórych scenariuszach należy przesłonić `CanWriteResult` zamiast `CanWriteType`.</span><span class="sxs-lookup"><span data-stu-id="b3cd1-146">In some scenarios you have to override `CanWriteResult` instead of `CanWriteType`.</span></span> <span data-ttu-id="b3cd1-147">Użyj `CanWriteResult` , jeśli spełnione są następujące warunki:</span><span class="sxs-lookup"><span data-stu-id="b3cd1-147">Use `CanWriteResult` if the following conditions are true:</span></span>

* <span data-ttu-id="b3cd1-148">Metoda działania zwraca klasę modelu.</span><span class="sxs-lookup"><span data-stu-id="b3cd1-148">Your action method returns a model class.</span></span>
* <span data-ttu-id="b3cd1-149">Istnieją klasy pochodne, które mogą być zwracane w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="b3cd1-149">There are derived classes which might be returned at runtime.</span></span>
* <span data-ttu-id="b3cd1-150">Musisz wiedzieć w czasie wykonywania, który Klasa pochodna została zwrócona przez akcję.</span><span class="sxs-lookup"><span data-stu-id="b3cd1-150">You need to know at runtime which derived class was returned by the action.</span></span>

<span data-ttu-id="b3cd1-151">Na przykład `Person` Załóżmy, że podpis metody akcji zwraca typ, ale może zwrócić `Student` lub `Instructor` typ, który pochodzi od. `Person`</span><span class="sxs-lookup"><span data-stu-id="b3cd1-151">For example, suppose your action method signature returns a `Person` type, but it may return a `Student` or `Instructor` type that derives from `Person`.</span></span> <span data-ttu-id="b3cd1-152">Jeśli chcesz, aby program formatujący obsługiwał `Student` tylko obiekty, Sprawdź typ [obiektu](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext.object#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object) w obiekcie kontekstu udostępnionym `CanWriteResult` metody.</span><span class="sxs-lookup"><span data-stu-id="b3cd1-152">If you want your formatter to handle only `Student` objects, check the type of [Object](/dotnet/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext.object#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object) in the context object provided to the `CanWriteResult` method.</span></span> <span data-ttu-id="b3cd1-153">Należy zauważyć, że nie jest konieczne używanie `CanWriteResult` , gdy metoda akcji zwróci `IActionResult`wartość; w takim przypadku `CanWriteType` metoda odbiera typ środowiska uruchomieniowego.</span><span class="sxs-lookup"><span data-stu-id="b3cd1-153">Note that it's not necessary to use `CanWriteResult` when the action method returns `IActionResult`; in that case, the `CanWriteType` method receives the runtime type.</span></span>

<a id="read-write"></a>

### <a name="override-readrequestbodyasyncwriteresponsebodyasync"></a><span data-ttu-id="b3cd1-154">Zastąp ReadRequestBodyAsync/WriteResponseBodyAsync</span><span class="sxs-lookup"><span data-stu-id="b3cd1-154">Override ReadRequestBodyAsync/WriteResponseBodyAsync</span></span>

<span data-ttu-id="b3cd1-155">Wykonywanie rzeczywistej pracy deserializacji lub serializacji w `ReadRequestBodyAsync` lub. `WriteResponseBodyAsync`</span><span class="sxs-lookup"><span data-stu-id="b3cd1-155">You do the actual work of deserializing or serializing in `ReadRequestBodyAsync` or `WriteResponseBodyAsync`.</span></span> <span data-ttu-id="b3cd1-156">W wyróżnionych wierszach w poniższym przykładzie pokazano, jak uzyskać usługi z kontenera iniekcji zależności (nie można pobrać ich z parametrów konstruktora).</span><span class="sxs-lookup"><span data-stu-id="b3cd1-156">The highlighted lines in the following example show how to get services from the dependency injection container (you can't get them from constructor parameters).</span></span>

[!code-csharp[](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=writeresponse&highlight=3-4)]

<span data-ttu-id="b3cd1-157">Przykład danych wejściowych programu formatującego można znaleźć w [aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample)przykładowej.</span><span class="sxs-lookup"><span data-stu-id="b3cd1-157">For an input formatter example, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample).</span></span>

## <a name="how-to-configure-mvc-to-use-a-custom-formatter"></a><span data-ttu-id="b3cd1-158">Jak skonfigurować MVC do używania niestandardowego programu formatującego</span><span class="sxs-lookup"><span data-stu-id="b3cd1-158">How to configure MVC to use a custom formatter</span></span>

<span data-ttu-id="b3cd1-159">Aby użyć niestandardowego programu formatującego, Dodaj wystąpienie klasy programu formatującego do kolekcji `InputFormatters` lub. `OutputFormatters`</span><span class="sxs-lookup"><span data-stu-id="b3cd1-159">To use a custom formatter, add an instance of the formatter class to the `InputFormatters` or `OutputFormatters` collection.</span></span>

[!code-csharp[](custom-formatters/sample/Startup.cs?name=mvcoptions&highlight=3-4)]

<span data-ttu-id="b3cd1-160">Elementy formatujące są oceniane w kolejności, w jakiej je wstawiasz.</span><span class="sxs-lookup"><span data-stu-id="b3cd1-160">Formatters are evaluated in the order you insert them.</span></span> <span data-ttu-id="b3cd1-161">Pierwszeństwo ma pierwszy.</span><span class="sxs-lookup"><span data-stu-id="b3cd1-161">The first one takes precedence.</span></span>

## <a name="next-steps"></a><span data-ttu-id="b3cd1-162">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="b3cd1-162">Next steps</span></span>

* <span data-ttu-id="b3cd1-163">[Przykładowa aplikacja dla tego dokumentu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample), która implementuje proste elementy formatujące dane wejściowe i wyjściowe w formacie vCard.</span><span class="sxs-lookup"><span data-stu-id="b3cd1-163">[Sample app for this doc](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/sample), which implements simple vCard input and output formatters.</span></span> <span data-ttu-id="b3cd1-164">Aplikacje odczytuje i zapisuje wizytówki vCard, które wyglądają jak w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="b3cd1-164">The apps reads and writes vCards that look like the following example:</span></span>

```
BEGIN:VCARD
VERSION:2.1
N:Davolio;Nancy
FN:Nancy Davolio
no-loc: [Blazor, "Identity", "Let's Encrypt", Razor, SignalR]
uid:20293482-9240-4d68-b475-325df4a83728
END:VCARD
```

<span data-ttu-id="b3cd1-165">Aby wyświetlić dane wyjściowe w formacie vCard, uruchom aplikację i Wyślij żądanie Get z nagłówkiem Akceptuj "text/vCard `http://localhost:63313/api/contacts/` " do (w przypadku uruchamiania z programu `http://localhost:5000/api/contacts/` Visual Studio) lub (w przypadku uruchamiania z wiersza polecenia).</span><span class="sxs-lookup"><span data-stu-id="b3cd1-165">To see vCard output, run the application and send a Get request with Accept header "text/vcard" to `http://localhost:63313/api/contacts/` (when running from Visual Studio) or `http://localhost:5000/api/contacts/` (when running from the command line).</span></span>

<span data-ttu-id="b3cd1-166">Aby dodać wizytówkę vCard do kolekcji kontaktów w pamięci, Wyślij żądanie post na ten sam adres URL, z nagłówkiem content-type "text/vCard" i z tekstem w formacie vCard w treści, tak jak w powyższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="b3cd1-166">To add a vCard to the in-memory collection of contacts, send a Post request to the same URL, with Content-Type header "text/vcard" and with vCard text in the body, formatted like the example above.</span></span>
