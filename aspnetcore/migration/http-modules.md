---
title: Migrowanie programów obsługi i modułów HTTP do ASP.NET Core oprogramowania pośredniczącego
author: rick-anderson
description: ''
ms.author: riande
ms.date: 12/07/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/http-modules
ms.openlocfilehash: 214e3fa86a1418f04a5e292cdc1b4baac8c75643
ms.sourcegitcommit: 4437f4c149f1ef6c28796dcfaa2863b4c088169c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2020
ms.locfileid: "85074181"
---
# <a name="migrate-http-handlers-and-modules-to-aspnet-core-middleware"></a><span data-ttu-id="e13d0-102">Migrowanie programów obsługi i modułów HTTP do ASP.NET Core oprogramowania pośredniczącego</span><span class="sxs-lookup"><span data-stu-id="e13d0-102">Migrate HTTP handlers and modules to ASP.NET Core middleware</span></span>

<span data-ttu-id="e13d0-103">W tym artykule przedstawiono sposób migrowania istniejących [modułów i programów ASP.net http z programu System. WebServer](/iis/configuration/system.webserver/) do ASP.NET Core [oprogramowania pośredniczącego](xref:fundamentals/middleware/index).</span><span class="sxs-lookup"><span data-stu-id="e13d0-103">This article shows how to migrate existing ASP.NET [HTTP modules and handlers from system.webserver](/iis/configuration/system.webserver/) to ASP.NET Core [middleware](xref:fundamentals/middleware/index).</span></span>

## <a name="modules-and-handlers-revisited"></a><span data-ttu-id="e13d0-104">Moduły i programy obsługi oglądany</span><span class="sxs-lookup"><span data-stu-id="e13d0-104">Modules and handlers revisited</span></span>

<span data-ttu-id="e13d0-105">Przed przystąpieniem do ASP.NET Core oprogramowania pośredniczącego najpierw podsumowanie sposób działania modułów i programów obsługi HTTP:</span><span class="sxs-lookup"><span data-stu-id="e13d0-105">Before proceeding to ASP.NET Core middleware, let's first recap how HTTP modules and handlers work:</span></span>

![Procedura obsługi modułów](http-modules/_static/moduleshandlers.png)

<span data-ttu-id="e13d0-107">**Programy obsługi:**</span><span class="sxs-lookup"><span data-stu-id="e13d0-107">**Handlers are:**</span></span>

* <span data-ttu-id="e13d0-108">Klasy implementujące [IHttpHandler](/dotnet/api/system.web.ihttphandler)</span><span class="sxs-lookup"><span data-stu-id="e13d0-108">Classes that implement [IHttpHandler](/dotnet/api/system.web.ihttphandler)</span></span>

* <span data-ttu-id="e13d0-109">Służy do obsługi żądań o danej nazwie pliku lub rozszerzeniu, takich jak *. Report*</span><span class="sxs-lookup"><span data-stu-id="e13d0-109">Used to handle requests with a given file name or extension, such as *.report*</span></span>

* <span data-ttu-id="e13d0-110">[Skonfigurowane](/iis/configuration/system.webserver/handlers/) w *Web.config*</span><span class="sxs-lookup"><span data-stu-id="e13d0-110">[Configured](/iis/configuration/system.webserver/handlers/) in *Web.config*</span></span>

<span data-ttu-id="e13d0-111">**Moduły to:**</span><span class="sxs-lookup"><span data-stu-id="e13d0-111">**Modules are:**</span></span>

* <span data-ttu-id="e13d0-112">Klasy implementujące [IHttpModule](/dotnet/api/system.web.ihttpmodule)</span><span class="sxs-lookup"><span data-stu-id="e13d0-112">Classes that implement [IHttpModule](/dotnet/api/system.web.ihttpmodule)</span></span>

* <span data-ttu-id="e13d0-113">Wywołane dla każdego żądania</span><span class="sxs-lookup"><span data-stu-id="e13d0-113">Invoked for every request</span></span>

* <span data-ttu-id="e13d0-114">Możliwość krótkiego obwodu (zaprzestanie przetwarzania żądania)</span><span class="sxs-lookup"><span data-stu-id="e13d0-114">Able to short-circuit (stop further processing of a request)</span></span>

* <span data-ttu-id="e13d0-115">Można dodać do odpowiedzi HTTP lub utworzyć własne</span><span class="sxs-lookup"><span data-stu-id="e13d0-115">Able to add to the HTTP response, or create their own</span></span>

* <span data-ttu-id="e13d0-116">[Skonfigurowane](/iis/configuration/system.webserver/modules/) w *Web.config*</span><span class="sxs-lookup"><span data-stu-id="e13d0-116">[Configured](/iis/configuration/system.webserver/modules/) in *Web.config*</span></span>

<span data-ttu-id="e13d0-117">**Kolejność, w której moduły przetwarzają żądania przychodzące, jest określana na podstawie:**</span><span class="sxs-lookup"><span data-stu-id="e13d0-117">**The order in which modules process incoming requests is determined by:**</span></span>

1. <span data-ttu-id="e13d0-118">[Cykl życia aplikacji](https://msdn.microsoft.com/library/ms227673.aspx), czyli zdarzenia serii wywoływane przez ASP.NET: [BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest)itd. Każdy moduł może utworzyć procedurę obsługi dla jednego lub wielu zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="e13d0-118">The [application life cycle](https://msdn.microsoft.com/library/ms227673.aspx), which is a series events fired by ASP.NET: [BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest), etc. Each module can create a handler for one or more events.</span></span>

2. <span data-ttu-id="e13d0-119">Dla tego samego zdarzenia kolejność, w jakiej są skonfigurowane w *Web.config*.</span><span class="sxs-lookup"><span data-stu-id="e13d0-119">For the same event, the order in which they're configured in *Web.config*.</span></span>

<span data-ttu-id="e13d0-120">Oprócz modułów można dodać programy obsługi dla zdarzeń cyklu życia do pliku *Global.asax.cs* .</span><span class="sxs-lookup"><span data-stu-id="e13d0-120">In addition to modules, you can add handlers for the life cycle events to your *Global.asax.cs* file.</span></span> <span data-ttu-id="e13d0-121">Te programy obsługi są uruchamiane po programach obsługi w skonfigurowanych modułach.</span><span class="sxs-lookup"><span data-stu-id="e13d0-121">These handlers run after the handlers in the configured modules.</span></span>

## <a name="from-handlers-and-modules-to-middleware"></a><span data-ttu-id="e13d0-122">Z programów obsługi i modułów do oprogramowania pośredniczącego</span><span class="sxs-lookup"><span data-stu-id="e13d0-122">From handlers and modules to middleware</span></span>

<span data-ttu-id="e13d0-123">**Oprogramowanie pośredniczące jest prostsze niż moduły HTTP i programy obsługi:**</span><span class="sxs-lookup"><span data-stu-id="e13d0-123">**Middleware are simpler than HTTP modules and handlers:**</span></span>

* <span data-ttu-id="e13d0-124">Moduły, programy obsługi, *Global.asax.cs*, *Web.config* (z wyjątkiem konfiguracji usług IIS) i cykl życia aplikacji zostały utracone</span><span class="sxs-lookup"><span data-stu-id="e13d0-124">Modules, handlers, *Global.asax.cs*, *Web.config* (except for IIS configuration) and the application life cycle are gone</span></span>

* <span data-ttu-id="e13d0-125">Role obu modułów i programów obsługi zostały przejęte przez oprogramowanie pośredniczące</span><span class="sxs-lookup"><span data-stu-id="e13d0-125">The roles of both modules and handlers have been taken over by middleware</span></span>

* <span data-ttu-id="e13d0-126">Oprogramowanie pośredniczące jest konfigurowane przy użyciu kodu, a nie w *Web.config*</span><span class="sxs-lookup"><span data-stu-id="e13d0-126">Middleware are configured using code rather than in *Web.config*</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="e13d0-127">[Rozgałęzianie potokowe](xref:fundamentals/middleware/index#branch-the-middleware-pipeline) umożliwia wysyłanie żądań do określonego oprogramowania pośredniczącego, w oparciu o nie tylko adres URL, ale również w nagłówkach żądań, ciągach zapytań itd.</span><span class="sxs-lookup"><span data-stu-id="e13d0-127">[Pipeline branching](xref:fundamentals/middleware/index#branch-the-middleware-pipeline) lets you send requests to specific middleware, based on not only the URL but also on request headers, query strings, etc.</span></span>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="e13d0-128">[Rozgałęzianie potokowe](xref:fundamentals/middleware/index#use-run-and-map) umożliwia wysyłanie żądań do określonego oprogramowania pośredniczącego, w oparciu o nie tylko adres URL, ale również w nagłówkach żądań, ciągach zapytań itd.</span><span class="sxs-lookup"><span data-stu-id="e13d0-128">[Pipeline branching](xref:fundamentals/middleware/index#use-run-and-map) lets you send requests to specific middleware, based on not only the URL but also on request headers, query strings, etc.</span></span>

::: moniker-end

<span data-ttu-id="e13d0-129">**Oprogramowanie pośredniczące jest bardzo podobne do modułów:**</span><span class="sxs-lookup"><span data-stu-id="e13d0-129">**Middleware are very similar to modules:**</span></span>

* <span data-ttu-id="e13d0-130">Wywoływane w zasadzie dla każdego żądania</span><span class="sxs-lookup"><span data-stu-id="e13d0-130">Invoked in principle for every request</span></span>

* <span data-ttu-id="e13d0-131">Możliwość krótkiego obwodu żądania przez [nie przekazanie żądania do następnego oprogramowania pośredniczącego](#http-modules-shortcircuiting-middleware)</span><span class="sxs-lookup"><span data-stu-id="e13d0-131">Able to short-circuit a request, by [not passing the request to the next middleware](#http-modules-shortcircuiting-middleware)</span></span>

* <span data-ttu-id="e13d0-132">Możliwość utworzenia własnej odpowiedzi HTTP</span><span class="sxs-lookup"><span data-stu-id="e13d0-132">Able to create their own HTTP response</span></span>

<span data-ttu-id="e13d0-133">**Oprogramowanie pośredniczące i moduły są przetwarzane w innej kolejności:**</span><span class="sxs-lookup"><span data-stu-id="e13d0-133">**Middleware and modules are processed in a different order:**</span></span>

* <span data-ttu-id="e13d0-134">Kolejność oprogramowania pośredniczącego opiera się na kolejności, w której są wstawiane do potoku żądania, natomiast kolejność modułów opiera się głównie na zdarzeniach [cyklu życia aplikacji](https://msdn.microsoft.com/library/ms227673.aspx) .</span><span class="sxs-lookup"><span data-stu-id="e13d0-134">Order of middleware is based on the order in which they're inserted into the request pipeline, while order of modules is mainly based on [application life cycle](https://msdn.microsoft.com/library/ms227673.aspx) events</span></span>

* <span data-ttu-id="e13d0-135">Kolejność oprogramowania pośredniczącego w odniesieniu do odpowiedzi to odwrotność od żądania, natomiast kolejność modułów jest taka sama dla żądań i odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="e13d0-135">Order of middleware for responses is the reverse from that for requests, while order of modules is the same for requests and responses</span></span>

* <span data-ttu-id="e13d0-136">Zobacz [Tworzenie potoku oprogramowania pośredniczącego za pomocą IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)</span><span class="sxs-lookup"><span data-stu-id="e13d0-136">See [Create a middleware pipeline with IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)</span></span>

![Oprogramowanie pośredniczące](http-modules/_static/middleware.png)

<span data-ttu-id="e13d0-138">Należy zwrócić uwagę na to, jak na powyższym obrazie oprogramowanie pośredniczące uwierzytelniania krótko obobwóduje żądanie.</span><span class="sxs-lookup"><span data-stu-id="e13d0-138">Note how in the image above, the authentication middleware short-circuited the request.</span></span>

## <a name="migrating-module-code-to-middleware"></a><span data-ttu-id="e13d0-139">Migrowanie kodu modułu do oprogramowania pośredniczącego</span><span class="sxs-lookup"><span data-stu-id="e13d0-139">Migrating module code to middleware</span></span>

<span data-ttu-id="e13d0-140">Istniejący moduł HTTP będzie wyglądać podobnie do tego:</span><span class="sxs-lookup"><span data-stu-id="e13d0-140">An existing HTTP module will look similar to this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyModule.cs?highlight=6,8,24,31)]

<span data-ttu-id="e13d0-141">Jak pokazano na stronie [oprogramowania pośredniczącego](xref:fundamentals/middleware/index) , ASP.NET Core oprogramowanie pośredniczące jest klasą, która udostępnia `Invoke` metodę pobierającą `HttpContext` i zwracającą wynik `Task` .</span><span class="sxs-lookup"><span data-stu-id="e13d0-141">As shown in the [Middleware](xref:fundamentals/middleware/index) page, an ASP.NET Core middleware is a class that exposes an `Invoke` method taking an `HttpContext` and returning a `Task`.</span></span> <span data-ttu-id="e13d0-142">Nowe oprogramowanie pośredniczące będzie wyglądać następująco:</span><span class="sxs-lookup"><span data-stu-id="e13d0-142">Your new middleware will look like this:</span></span>

<a name="http-modules-usemiddleware"></a>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddleware.cs?highlight=9,13,20,24,28,30,32)]

<span data-ttu-id="e13d0-143">Poprzedni szablon oprogramowania pośredniczącego został pobrany z sekcji podczas [pisania oprogramowania pośredniczącego](xref:fundamentals/middleware/write).</span><span class="sxs-lookup"><span data-stu-id="e13d0-143">The preceding middleware template was taken from the section on [writing middleware](xref:fundamentals/middleware/write).</span></span>

<span data-ttu-id="e13d0-144">Klasa pomocnika *MyMiddlewareExtensions* ułatwia konfigurowanie oprogramowania pośredniczącego w `Startup` klasie.</span><span class="sxs-lookup"><span data-stu-id="e13d0-144">The *MyMiddlewareExtensions* helper class makes it easier to configure your middleware in your `Startup` class.</span></span> <span data-ttu-id="e13d0-145">`UseMyMiddleware`Metoda dodaje klasę oprogramowania pośredniczącego do potoku żądania.</span><span class="sxs-lookup"><span data-stu-id="e13d0-145">The `UseMyMiddleware` method adds your middleware class to the request pipeline.</span></span> <span data-ttu-id="e13d0-146">Usługi wymagane przez oprogramowanie pośredniczące są wprowadzane w konstruktorze oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="e13d0-146">Services required by the middleware get injected in the middleware's constructor.</span></span>

<a name="http-modules-shortcircuiting-middleware"></a>

<span data-ttu-id="e13d0-147">Moduł może zakończyć żądanie, na przykład jeśli użytkownik nie ma autoryzacji:</span><span class="sxs-lookup"><span data-stu-id="e13d0-147">Your module might terminate a request, for example if the user isn't authorized:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyTerminatingModule.cs?highlight=9,10,11,12,13&name=snippet_Terminate)]

<span data-ttu-id="e13d0-148">Oprogramowanie pośredniczące obsługuje to, nie wywołując `Invoke` w następnym oprogramowaniu pośredniczącym w potoku.</span><span class="sxs-lookup"><span data-stu-id="e13d0-148">A middleware handles this by not calling `Invoke` on the next middleware in the pipeline.</span></span> <span data-ttu-id="e13d0-149">Należy pamiętać, że to nie przerywa w pełni żądania, ponieważ poprzednie middlewares nadal będą wywoływane, gdy odpowiedź przejdzie do tyłu przez potok.</span><span class="sxs-lookup"><span data-stu-id="e13d0-149">Keep in mind that this doesn't fully terminate the request, because previous middlewares will still be invoked when the response makes its way back through the pipeline.</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyTerminatingMiddleware.cs?highlight=7,8&name=snippet_Terminate)]

<span data-ttu-id="e13d0-150">Po przeprowadzeniu migracji funkcjonalności modułu do nowego oprogramowania pośredniczącego, może się okazać, że Twój kod nie kompiluje się, ponieważ `HttpContext` Klasa została znacząco zmieniona w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e13d0-150">When you migrate your module's functionality to your new middleware, you may find that your code doesn't compile because the `HttpContext` class has significantly changed in ASP.NET Core.</span></span> <span data-ttu-id="e13d0-151">[Później](#migrating-to-the-new-httpcontext)zobaczysz, jak przeprowadzić migrację do nowego ASP.NET Core HttpContext.</span><span class="sxs-lookup"><span data-stu-id="e13d0-151">[Later on](#migrating-to-the-new-httpcontext), you'll see how to migrate to the new ASP.NET Core HttpContext.</span></span>

## <a name="migrating-module-insertion-into-the-request-pipeline"></a><span data-ttu-id="e13d0-152">Migrowanie wstawiania modułu do potoku żądania</span><span class="sxs-lookup"><span data-stu-id="e13d0-152">Migrating module insertion into the request pipeline</span></span>

<span data-ttu-id="e13d0-153">Moduły HTTP są zazwyczaj dodawane do potoku żądania przy użyciu *Web.config*:</span><span class="sxs-lookup"><span data-stu-id="e13d0-153">HTTP modules are typically added to the request pipeline using *Web.config*:</span></span>

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32-33,36,43,50,101)]

<span data-ttu-id="e13d0-154">Przekształć to, [dodając nowe oprogramowanie pośredniczące](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) do potoku żądania w `Startup` klasie:</span><span class="sxs-lookup"><span data-stu-id="e13d0-154">Convert this by [adding your new middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) to the request pipeline in your `Startup` class:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=16)]

<span data-ttu-id="e13d0-155">Dokładne miejsce w potoku, w którym wstawiasz nowe oprogramowanie pośredniczące, zależy od zdarzenia, które zostało obsłużone jako moduł ( `BeginRequest` , `EndRequest` itp.) i jego kolejność na liście modułów w *Web.config*.</span><span class="sxs-lookup"><span data-stu-id="e13d0-155">The exact spot in the pipeline where you insert your new middleware depends on the event that it handled as a module (`BeginRequest`, `EndRequest`, etc.) and its order in your list of modules in *Web.config*.</span></span>

<span data-ttu-id="e13d0-156">Jak wspomniano wcześniej, nie ma cyklu życia aplikacji w ASP.NET Core i kolejności, w której odpowiedzi są przetwarzane przez oprogramowanie pośredniczące, różnią się od kolejności używanej przez moduły.</span><span class="sxs-lookup"><span data-stu-id="e13d0-156">As previously stated, there's no application life cycle in ASP.NET Core and the order in which responses are processed by middleware differs from the order used by modules.</span></span> <span data-ttu-id="e13d0-157">Może to spowodować, że decyzje dotyczące porządkowania są bardziej trudne.</span><span class="sxs-lookup"><span data-stu-id="e13d0-157">This could make your ordering decision more challenging.</span></span>

<span data-ttu-id="e13d0-158">Jeśli porządkowanie stanie się problemem, można podzielić moduł na wiele składników oprogramowania pośredniczącego, które mogą być uporządkowane niezależnie.</span><span class="sxs-lookup"><span data-stu-id="e13d0-158">If ordering becomes a problem, you could split your module into multiple middleware components that can be ordered independently.</span></span>

## <a name="migrating-handler-code-to-middleware"></a><span data-ttu-id="e13d0-159">Migrowanie kodu programu obsługi do oprogramowania pośredniczącego</span><span class="sxs-lookup"><span data-stu-id="e13d0-159">Migrating handler code to middleware</span></span>

<span data-ttu-id="e13d0-160">Procedura obsługi protokołu HTTP wygląda następująco:</span><span class="sxs-lookup"><span data-stu-id="e13d0-160">An HTTP handler looks something like this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/HttpHandlers/ReportHandler.cs?highlight=5,7,13,14,15,16)]

<span data-ttu-id="e13d0-161">W projekcie ASP.NET Core można je przetłumaczyć na oprogramowanie pośredniczące podobne do tego:</span><span class="sxs-lookup"><span data-stu-id="e13d0-161">In your ASP.NET Core project, you would translate this to a middleware similar to this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/ReportHandlerMiddleware.cs?highlight=7,9,13,20,21,22,23,40,42,44)]

<span data-ttu-id="e13d0-162">To oprogramowanie pośredniczące jest bardzo podobne do oprogramowania pośredniczącego odpowiadającego modułom.</span><span class="sxs-lookup"><span data-stu-id="e13d0-162">This middleware is very similar to the middleware corresponding to modules.</span></span> <span data-ttu-id="e13d0-163">Jedyną rzeczywistą różnicą jest to, że nie jest to wywołanie `_next.Invoke(context)` .</span><span class="sxs-lookup"><span data-stu-id="e13d0-163">The only real difference is that here there's no call to `_next.Invoke(context)`.</span></span> <span data-ttu-id="e13d0-164">Ma to sens, ponieważ program obsługi znajduje się na końcu potoku żądania, więc nie będzie można wywołać następnego oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="e13d0-164">That makes sense, because the handler is at the end of the request pipeline, so there will be no next middleware to invoke.</span></span>

## <a name="migrating-handler-insertion-into-the-request-pipeline"></a><span data-ttu-id="e13d0-165">Migrowanie wstawiania procedury obsługi do potoku żądania</span><span class="sxs-lookup"><span data-stu-id="e13d0-165">Migrating handler insertion into the request pipeline</span></span>

<span data-ttu-id="e13d0-166">Konfigurowanie obsługi protokołu HTTP odbywa się w *Web.config* i wygląda następująco:</span><span class="sxs-lookup"><span data-stu-id="e13d0-166">Configuring an HTTP handler is done in *Web.config* and looks something like this:</span></span>

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32,46-48,50,101)]

<span data-ttu-id="e13d0-167">Można to przekonwertować, dodając nowe oprogramowanie pośredniczące programu obsługi do potoku żądania w `Startup` klasie, podobnie jak oprogramowanie pośredniczące konwertowane z modułów.</span><span class="sxs-lookup"><span data-stu-id="e13d0-167">You could convert this by adding your new handler middleware to the request pipeline in your `Startup` class, similar to middleware converted from modules.</span></span> <span data-ttu-id="e13d0-168">Problem z tym podejściem polega na tym, że wyśle wszystkie żądania do nowego oprogramowania pośredniczącego programu obsługi.</span><span class="sxs-lookup"><span data-stu-id="e13d0-168">The problem with that approach is that it would send all requests to your new handler middleware.</span></span> <span data-ttu-id="e13d0-169">Jednak tylko żądania z danym rozszerzeniem mogą dotrzeć do oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="e13d0-169">However, you only want requests with a given extension to reach your middleware.</span></span> <span data-ttu-id="e13d0-170">Zapewnia to takie same funkcje, jak w przypadku programu obsługi protokołu HTTP.</span><span class="sxs-lookup"><span data-stu-id="e13d0-170">That would give you the same functionality you had with your HTTP handler.</span></span>

<span data-ttu-id="e13d0-171">Jednym z rozwiązań jest rozgałęzienie potoku dla żądań z danym rozszerzeniem przy użyciu `MapWhen` metody rozszerzenia.</span><span class="sxs-lookup"><span data-stu-id="e13d0-171">One solution is to branch the pipeline for requests with a given extension, using the `MapWhen` extension method.</span></span> <span data-ttu-id="e13d0-172">Należy to zrobić w tej samej `Configure` metodzie, w której można dodać inne oprogramowanie pośredniczące:</span><span class="sxs-lookup"><span data-stu-id="e13d0-172">You do this in the same `Configure` method where you add the other middleware:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=27-34)]

<span data-ttu-id="e13d0-173">`MapWhen`przyjmuje następujące parametry:</span><span class="sxs-lookup"><span data-stu-id="e13d0-173">`MapWhen` takes these parameters:</span></span>

1. <span data-ttu-id="e13d0-174">Lambda, która pobiera `HttpContext` i zwraca, `true` Jeśli żądanie powinno przejść do gałęzi.</span><span class="sxs-lookup"><span data-stu-id="e13d0-174">A lambda that takes the `HttpContext` and returns `true` if the request should go down the branch.</span></span> <span data-ttu-id="e13d0-175">Oznacza to, że można rozgałęziać żądania nie tylko na podstawie ich rozszerzenia, ale także w nagłówkach żądań, parametrach ciągu zapytania itd.</span><span class="sxs-lookup"><span data-stu-id="e13d0-175">This means you can branch requests not just based on their extension, but also on request headers, query string parameters, etc.</span></span>

2. <span data-ttu-id="e13d0-176">Lambda, która pobiera `IApplicationBuilder` i dodaje wszystkie oprogramowanie pośredniczące dla gałęzi.</span><span class="sxs-lookup"><span data-stu-id="e13d0-176">A lambda that takes an `IApplicationBuilder` and adds all the middleware for the branch.</span></span> <span data-ttu-id="e13d0-177">Oznacza to, że można dodać dodatkowe oprogramowanie pośredniczące do gałęzi przed programem obsługi oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="e13d0-177">This means you can add additional middleware to the branch in front of your handler middleware.</span></span>

<span data-ttu-id="e13d0-178">Oprogramowanie pośredniczące dodane do potoku, zanim gałąź zostanie wywołana na wszystkich żądaniach; Rozgałęzienie nie będzie miało wpływu na te elementy.</span><span class="sxs-lookup"><span data-stu-id="e13d0-178">Middleware added to the pipeline before the branch will be invoked on all requests; the branch will have no impact on them.</span></span>

## <a name="loading-middleware-options-using-the-options-pattern"></a><span data-ttu-id="e13d0-179">Ładowanie opcji oprogramowania pośredniczącego przy użyciu wzorca opcji</span><span class="sxs-lookup"><span data-stu-id="e13d0-179">Loading middleware options using the options pattern</span></span>

<span data-ttu-id="e13d0-180">Niektóre moduły i programy obsługi mają opcje konfiguracji, które są przechowywane w *Web.config*. Jednak w ASP.NET Core jest używany nowy model konfiguracji zamiast *Web.config*.</span><span class="sxs-lookup"><span data-stu-id="e13d0-180">Some modules and handlers have configuration options that are stored in *Web.config*. However, in ASP.NET Core a new configuration model is used in place of *Web.config*.</span></span>

<span data-ttu-id="e13d0-181">Nowy [system konfiguracji](xref:fundamentals/configuration/index) zapewnia następujące opcje:</span><span class="sxs-lookup"><span data-stu-id="e13d0-181">The new [configuration system](xref:fundamentals/configuration/index) gives you these options to solve this:</span></span>

* <span data-ttu-id="e13d0-182">Bezpośrednio wstrzyknąć opcje do oprogramowania pośredniczącego, jak pokazano w [następnej sekcji](#loading-middleware-options-through-direct-injection).</span><span class="sxs-lookup"><span data-stu-id="e13d0-182">Directly inject the options into the middleware, as shown in the [next section](#loading-middleware-options-through-direct-injection).</span></span>

* <span data-ttu-id="e13d0-183">Użyj [wzorca opcji](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="e13d0-183">Use the [options pattern](xref:fundamentals/configuration/options):</span></span>

1. <span data-ttu-id="e13d0-184">Utwórz klasę zawierającą Opcje oprogramowania pośredniczącego, na przykład:</span><span class="sxs-lookup"><span data-stu-id="e13d0-184">Create a class to hold your middleware options, for example:</span></span>

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Options)]

2. <span data-ttu-id="e13d0-185">Przechowywanie wartości opcji</span><span class="sxs-lookup"><span data-stu-id="e13d0-185">Store the option values</span></span>

   <span data-ttu-id="e13d0-186">System konfiguracji umożliwia przechowywanie wartości opcji w dowolnym miejscu.</span><span class="sxs-lookup"><span data-stu-id="e13d0-186">The configuration system allows you to store option values anywhere you want.</span></span> <span data-ttu-id="e13d0-187">Jednak większość lokacji używa *appsettings.jsna*, więc zajmiemy się tym podejściem:</span><span class="sxs-lookup"><span data-stu-id="e13d0-187">However, most sites use *appsettings.json*, so we'll take that approach:</span></span>

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,14-18)]

   <span data-ttu-id="e13d0-188">*MyMiddlewareOptionsSection* tutaj to nazwa sekcji.</span><span class="sxs-lookup"><span data-stu-id="e13d0-188">*MyMiddlewareOptionsSection* here is a section name.</span></span> <span data-ttu-id="e13d0-189">Nie musi być taka sama jak nazwa klasy Options.</span><span class="sxs-lookup"><span data-stu-id="e13d0-189">It doesn't have to be the same as the name of your options class.</span></span>

3. <span data-ttu-id="e13d0-190">Skojarz wartości opcji z klasą opcji</span><span class="sxs-lookup"><span data-stu-id="e13d0-190">Associate the option values with the options class</span></span>

    <span data-ttu-id="e13d0-191">Wzorzec opcji używa struktury wstrzykiwania zależności ASP.NET Core do kojarzenia typu opcji (na przykład `MyMiddlewareOptions` ) z `MyMiddlewareOptions` obiektem, który ma rzeczywiste opcje.</span><span class="sxs-lookup"><span data-stu-id="e13d0-191">The options pattern uses ASP.NET Core's dependency injection framework to associate the options type (such as `MyMiddlewareOptions`) with a `MyMiddlewareOptions` object that has the actual options.</span></span>

    <span data-ttu-id="e13d0-192">Aktualizowanie `Startup` klasy:</span><span class="sxs-lookup"><span data-stu-id="e13d0-192">Update your `Startup` class:</span></span>

   1. <span data-ttu-id="e13d0-193">Jeśli używasz *appsettings.json*, Dodaj go do konstruktora konfiguracji w `Startup` konstruktorze:</span><span class="sxs-lookup"><span data-stu-id="e13d0-193">If you're using *appsettings.json*, add it to the configuration builder in the `Startup` constructor:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Ctor&highlight=5-6)]

   2. <span data-ttu-id="e13d0-194">Skonfiguruj usługę opcji:</span><span class="sxs-lookup"><span data-stu-id="e13d0-194">Configure the options service:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

   3. <span data-ttu-id="e13d0-195">Skojarz swoje opcje z klasą opcji:</span><span class="sxs-lookup"><span data-stu-id="e13d0-195">Associate your options with your options class:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=6-8)]

4. <span data-ttu-id="e13d0-196">Wsuń opcje do konstruktora oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="e13d0-196">Inject the options into your middleware constructor.</span></span> <span data-ttu-id="e13d0-197">Jest to podobne do iniekcji opcji do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="e13d0-197">This is similar to injecting options into a controller.</span></span>

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_MiddlewareWithParams&highlight=4,7,10,15-16)]

   <span data-ttu-id="e13d0-198">Metoda rozszerzenia [UseMiddleware](#http-modules-usemiddleware) , która dodaje oprogramowanie pośredniczące do `IApplicationBuilder` opieki nad iniekcją zależności.</span><span class="sxs-lookup"><span data-stu-id="e13d0-198">The [UseMiddleware](#http-modules-usemiddleware) extension method that adds your middleware to the `IApplicationBuilder` takes care of dependency injection.</span></span>

   <span data-ttu-id="e13d0-199">Nie jest to ograniczone do `IOptions` obiektów.</span><span class="sxs-lookup"><span data-stu-id="e13d0-199">This isn't limited to `IOptions` objects.</span></span> <span data-ttu-id="e13d0-200">Każdy inny obiekt, którego potrzebuje oprogramowanie pośredniczące, można wprowadzić w ten sposób.</span><span class="sxs-lookup"><span data-stu-id="e13d0-200">Any other object that your middleware requires can be injected this way.</span></span>

## <a name="loading-middleware-options-through-direct-injection"></a><span data-ttu-id="e13d0-201">Ładowanie opcji oprogramowania pośredniczącego za pośrednictwem bezpośredniego wtrysku</span><span class="sxs-lookup"><span data-stu-id="e13d0-201">Loading middleware options through direct injection</span></span>

<span data-ttu-id="e13d0-202">Wzorzec opcji ma zalety tworzenia swobodnego sprzężenia między wartościami opcji i ich konsumentami.</span><span class="sxs-lookup"><span data-stu-id="e13d0-202">The options pattern has the advantage that it creates loose coupling between options values and their consumers.</span></span> <span data-ttu-id="e13d0-203">Po skojarzeniu klasy opcji z rzeczywistymi wartościami opcji każda inna Klasa może uzyskać dostęp do opcji za pomocą struktury iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="e13d0-203">Once you've associated an options class with the actual options values, any other class can get access to the options through the dependency injection framework.</span></span> <span data-ttu-id="e13d0-204">Nie ma potrzeby przekazywania żadnych wartości opcji.</span><span class="sxs-lookup"><span data-stu-id="e13d0-204">There's no need to pass around options values.</span></span>

<span data-ttu-id="e13d0-205">Ten podział działa inaczej, jeśli chcesz użyć tego samego oprogramowania pośredniczącego dwa razy, z innymi opcjami.</span><span class="sxs-lookup"><span data-stu-id="e13d0-205">This breaks down though if you want to use the same middleware twice, with different options.</span></span> <span data-ttu-id="e13d0-206">Na przykład oprogramowanie pośredniczące autoryzacji używane w różnych gałęziach, które zezwalają na różne role.</span><span class="sxs-lookup"><span data-stu-id="e13d0-206">For example an authorization middleware used in different branches allowing different roles.</span></span> <span data-ttu-id="e13d0-207">Nie można skojarzyć dwóch różnych obiektów Options z jedną klasą opcji.</span><span class="sxs-lookup"><span data-stu-id="e13d0-207">You can't associate two different options objects with the one options class.</span></span>

<span data-ttu-id="e13d0-208">Rozwiązaniem jest uzyskanie obiektów Options z rzeczywistymi wartościami opcji w `Startup` klasie i przekazywanie ich bezpośrednio do każdego wystąpienia oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="e13d0-208">The solution is to get the options objects with the actual options values in your `Startup` class and pass those directly to each instance of your middleware.</span></span>

1. <span data-ttu-id="e13d0-209">Dodaj drugi klucz do *appsettings.js*</span><span class="sxs-lookup"><span data-stu-id="e13d0-209">Add a second key to *appsettings.json*</span></span>

   <span data-ttu-id="e13d0-210">Aby dodać drugi zestaw opcji do *appsettings.jsw* pliku, Użyj nowego klucza w celu jego jednoznacznej identyfikacji:</span><span class="sxs-lookup"><span data-stu-id="e13d0-210">To add a second set of options to the *appsettings.json* file, use a new key to uniquely identify it:</span></span>

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,10-18&highlight=2-5)]

2. <span data-ttu-id="e13d0-211">Pobierz wartości opcji i przekaż je do oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="e13d0-211">Retrieve options values and pass them to middleware.</span></span> <span data-ttu-id="e13d0-212">`Use...`Metoda rozszerzająca (która dodaje oprogramowanie pośredniczące do potoku) jest miejscem logicznym do przekazania w wartościach opcji:</span><span class="sxs-lookup"><span data-stu-id="e13d0-212">The `Use...` extension method (which adds your middleware to the pipeline) is a logical place to pass in the option values:</span></span> 

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=20-23)]

3. <span data-ttu-id="e13d0-213">Włącz oprogramowanie pośredniczące, aby pobrać parametr Options.</span><span class="sxs-lookup"><span data-stu-id="e13d0-213">Enable middleware to take an options parameter.</span></span> <span data-ttu-id="e13d0-214">Podaj Przeciążenie `Use...` metody rozszerzenia (która przyjmuje parametr Options i przekazuje go do `UseMiddleware` ).</span><span class="sxs-lookup"><span data-stu-id="e13d0-214">Provide an overload of the `Use...` extension method (that takes the options parameter and passes it to `UseMiddleware`).</span></span> <span data-ttu-id="e13d0-215">Gdy `UseMiddleware` jest wywoływana z parametrami, przekazuje parametry do konstruktora oprogramowania pośredniczącego podczas tworzenia wystąpienia obiektu pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="e13d0-215">When `UseMiddleware` is called with parameters, it passes the parameters to your middleware constructor when it instantiates the middleware object.</span></span>

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Extensions&highlight=9-14)]

   <span data-ttu-id="e13d0-216">Zwróć uwagę, jak to zawija obiekt Options w `OptionsWrapper` obiekcie.</span><span class="sxs-lookup"><span data-stu-id="e13d0-216">Note how this wraps the options object in an `OptionsWrapper` object.</span></span> <span data-ttu-id="e13d0-217">To implementuje `IOptions` , zgodnie z oczekiwaniami w konstruktorze oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="e13d0-217">This implements `IOptions`, as expected by the middleware constructor.</span></span>

## <a name="migrating-to-the-new-httpcontext"></a><span data-ttu-id="e13d0-218">Migrowanie do nowego obiektu HttpContext</span><span class="sxs-lookup"><span data-stu-id="e13d0-218">Migrating to the new HttpContext</span></span>

<span data-ttu-id="e13d0-219">Wcześniej zawarto, że `Invoke` Metoda w oprogramowaniu pośredniczącym przyjmuje parametr typu `HttpContext` :</span><span class="sxs-lookup"><span data-stu-id="e13d0-219">You saw earlier that the `Invoke` method in your middleware takes a parameter of type `HttpContext`:</span></span>

```csharp
public async Task Invoke(HttpContext context)
```

<span data-ttu-id="e13d0-220">`HttpContext`został znacząco zmieniony w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e13d0-220">`HttpContext` has significantly changed in ASP.NET Core.</span></span> <span data-ttu-id="e13d0-221">W tej sekcji pokazano, jak przetłumaczyć najczęściej używane właściwości elementu [System. Web. HttpContext](/dotnet/api/system.web.httpcontext) na nowy `Microsoft.AspNetCore.Http.HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="e13d0-221">This section shows how to translate the most commonly used properties of [System.Web.HttpContext](/dotnet/api/system.web.httpcontext) to the new `Microsoft.AspNetCore.Http.HttpContext`.</span></span>

### <a name="httpcontext"></a><span data-ttu-id="e13d0-222">HttpContext</span><span class="sxs-lookup"><span data-stu-id="e13d0-222">HttpContext</span></span>

<span data-ttu-id="e13d0-223">**Element HttpContext. Items** Wykonuje translację do:</span><span class="sxs-lookup"><span data-stu-id="e13d0-223">**HttpContext.Items** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Items)]

<span data-ttu-id="e13d0-224">**Unikatowy identyfikator żądania (brak odpowiednika system. Web. HttpContext)**</span><span class="sxs-lookup"><span data-stu-id="e13d0-224">**Unique request ID (no System.Web.HttpContext counterpart)**</span></span>

<span data-ttu-id="e13d0-225">Zapewnia unikatowy identyfikator dla każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="e13d0-225">Gives you a unique id for each request.</span></span> <span data-ttu-id="e13d0-226">Bardzo przydatne do uwzględnienia w dziennikach.</span><span class="sxs-lookup"><span data-stu-id="e13d0-226">Very useful to include in your logs.</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Trace)]

### <a name="httpcontextrequest"></a><span data-ttu-id="e13d0-227">Element HttpContext. Request</span><span class="sxs-lookup"><span data-stu-id="e13d0-227">HttpContext.Request</span></span>

<span data-ttu-id="e13d0-228">Element **HttpContext. Request. HttpMethod** Wykonuje translację na:</span><span class="sxs-lookup"><span data-stu-id="e13d0-228">**HttpContext.Request.HttpMethod** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Method)]

<span data-ttu-id="e13d0-229">Element **HttpContext. Request. QueryString** tłumaczy na:</span><span class="sxs-lookup"><span data-stu-id="e13d0-229">**HttpContext.Request.QueryString** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Query)]

<span data-ttu-id="e13d0-230">**Właściwość HttpContext. Request. URL** i **HttpContext. Request. RawUrl** są tłumaczone na:</span><span class="sxs-lookup"><span data-stu-id="e13d0-230">**HttpContext.Request.Url** and **HttpContext.Request.RawUrl** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Url)]

<span data-ttu-id="e13d0-231">Element **HttpContext. Request. IsSecureConnection** Wykonuje translację na:</span><span class="sxs-lookup"><span data-stu-id="e13d0-231">**HttpContext.Request.IsSecureConnection** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Secure)]

<span data-ttu-id="e13d0-232">Element **HttpContext. Request. UserHostAddress** Wykonuje translację na:</span><span class="sxs-lookup"><span data-stu-id="e13d0-232">**HttpContext.Request.UserHostAddress** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Host)]

<span data-ttu-id="e13d0-233">Element **HttpContext. Request. cookies** tłumaczy na:</span><span class="sxs-lookup"><span data-stu-id="e13d0-233">**HttpContext.Request.Cookies** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Cookies)]

<span data-ttu-id="e13d0-234">Element **HttpContext. Request. RequestContext. RouteData** tłumaczy na:</span><span class="sxs-lookup"><span data-stu-id="e13d0-234">**HttpContext.Request.RequestContext.RouteData** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Route)]

<span data-ttu-id="e13d0-235">Element **HttpContext. Request. Heads** Wykonuje translację na:</span><span class="sxs-lookup"><span data-stu-id="e13d0-235">**HttpContext.Request.Headers** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Headers)]

<span data-ttu-id="e13d0-236">Element **HttpContext. Request. userAgent** Wykonuje translację na:</span><span class="sxs-lookup"><span data-stu-id="e13d0-236">**HttpContext.Request.UserAgent** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Agent)]

<span data-ttu-id="e13d0-237">Element **HttpContext. Request. UrlReferrer** Wykonuje translację na:</span><span class="sxs-lookup"><span data-stu-id="e13d0-237">**HttpContext.Request.UrlReferrer** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Referrer)]

<span data-ttu-id="e13d0-238">Element **HttpContext. Request. ContentType** tłumaczy na:</span><span class="sxs-lookup"><span data-stu-id="e13d0-238">**HttpContext.Request.ContentType** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Type)]

<span data-ttu-id="e13d0-239">Element **HttpContext. Request. form** tłumaczy na:</span><span class="sxs-lookup"><span data-stu-id="e13d0-239">**HttpContext.Request.Form** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Form)]

> [!WARNING]
> <span data-ttu-id="e13d0-240">Odczytaj wartości formularza tylko wtedy, gdy podtyp zawartości to *x-www-form-urlencoded* lub *form-Data*.</span><span class="sxs-lookup"><span data-stu-id="e13d0-240">Read form values only if the content sub type is *x-www-form-urlencoded* or *form-data*.</span></span>

<span data-ttu-id="e13d0-241">Element **HttpContext. Request. InputStream** Wykonuje translację na:</span><span class="sxs-lookup"><span data-stu-id="e13d0-241">**HttpContext.Request.InputStream** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Input)]

> [!WARNING]
> <span data-ttu-id="e13d0-242">Użyj tego kodu tylko w oprogramowaniu pośredniczącym typu programu obsługi, na końcu potoku.</span><span class="sxs-lookup"><span data-stu-id="e13d0-242">Use this code only in a handler type middleware, at the end of a pipeline.</span></span>
>
><span data-ttu-id="e13d0-243">Nieprzetworzoną treść można odczytać, jak pokazano powyżej tylko raz dla każdego żądania.</span><span class="sxs-lookup"><span data-stu-id="e13d0-243">You can read the raw body as shown above only once per request.</span></span> <span data-ttu-id="e13d0-244">Oprogramowanie pośredniczące próbujące odczytać treść po pierwszym odczytaniu odczyta pustą treść.</span><span class="sxs-lookup"><span data-stu-id="e13d0-244">Middleware trying to read the body after the first read will read an empty body.</span></span>
>
><span data-ttu-id="e13d0-245">Nie dotyczy to odczytywania formularza jak pokazano wcześniej, ponieważ jest to wykonywane z bufora.</span><span class="sxs-lookup"><span data-stu-id="e13d0-245">This doesn't apply to reading a form as shown earlier, because that's done from a buffer.</span></span>

### <a name="httpcontextresponse"></a><span data-ttu-id="e13d0-246">HttpContext. Response</span><span class="sxs-lookup"><span data-stu-id="e13d0-246">HttpContext.Response</span></span>

<span data-ttu-id="e13d0-247">**Właściwość HttpContext. Response. status** i **HttpContext. Response. StatusDescription** są tłumaczone na:</span><span class="sxs-lookup"><span data-stu-id="e13d0-247">**HttpContext.Response.Status** and **HttpContext.Response.StatusDescription** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Status)]

<span data-ttu-id="e13d0-248">**Właściwość HttpContext. Response. ContentEncoding** i **HttpContext. Response. ContentType** przekładają się na:</span><span class="sxs-lookup"><span data-stu-id="e13d0-248">**HttpContext.Response.ContentEncoding** and **HttpContext.Response.ContentType** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespType)]

<span data-ttu-id="e13d0-249">**Właściwość HttpContext. Response. ContentType** jest również tłumaczona na:</span><span class="sxs-lookup"><span data-stu-id="e13d0-249">**HttpContext.Response.ContentType** on its own also translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespTypeOnly)]

<span data-ttu-id="e13d0-250">Element **HttpContext. Response. Output** tłumaczy na:</span><span class="sxs-lookup"><span data-stu-id="e13d0-250">**HttpContext.Response.Output** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Output)]

<span data-ttu-id="e13d0-251">**HttpContext. Response. TransmitFile**</span><span class="sxs-lookup"><span data-stu-id="e13d0-251">**HttpContext.Response.TransmitFile**</span></span>

<span data-ttu-id="e13d0-252">W [tym miejscu](../fundamentals/request-features.md#middleware-and-request-features)omówiono obsługę pliku.</span><span class="sxs-lookup"><span data-stu-id="e13d0-252">Serving up a file is discussed [here](../fundamentals/request-features.md#middleware-and-request-features).</span></span>

<span data-ttu-id="e13d0-253">**HttpContext. Response. Headers**</span><span class="sxs-lookup"><span data-stu-id="e13d0-253">**HttpContext.Response.Headers**</span></span>

<span data-ttu-id="e13d0-254">Wysyłanie nagłówków odpowiedzi jest skomplikowane przez fakt, że jeśli ustawisz je po zapisaniu jakichkolwiek elementów w treści odpowiedzi, nie będą one wysyłane.</span><span class="sxs-lookup"><span data-stu-id="e13d0-254">Sending response headers is complicated by the fact that if you set them after anything has been written to the response body, they will not be sent.</span></span>

<span data-ttu-id="e13d0-255">Rozwiązanie polega na ustawieniu metody wywołania zwrotnego, która będzie wywoływana w prawo przed rozpoczęciem zapisywania do odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="e13d0-255">The solution is to set a callback method that will be called right before writing to the response starts.</span></span> <span data-ttu-id="e13d0-256">Jest to najlepsze rozwiązanie na początku `Invoke` metody w oprogramowaniu pośredniczącym.</span><span class="sxs-lookup"><span data-stu-id="e13d0-256">This is best done at the start of the `Invoke` method in your middleware.</span></span> <span data-ttu-id="e13d0-257">Jest to metoda wywołania zwrotnego, która ustawia nagłówki odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="e13d0-257">It's this callback method that sets your response headers.</span></span>

<span data-ttu-id="e13d0-258">Poniższy kod ustawia metodę wywołania zwrotnego o nazwie `SetHeaders` :</span><span class="sxs-lookup"><span data-stu-id="e13d0-258">The following code sets a callback method called `SetHeaders`:</span></span>

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

<span data-ttu-id="e13d0-259">`SetHeaders`Metoda wywołania zwrotnego będzie wyglądać następująco:</span><span class="sxs-lookup"><span data-stu-id="e13d0-259">The `SetHeaders` callback method would look like this:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetHeaders)]

<span data-ttu-id="e13d0-260">**HttpContext. Response. cookies**</span><span class="sxs-lookup"><span data-stu-id="e13d0-260">**HttpContext.Response.Cookies**</span></span>

<span data-ttu-id="e13d0-261">Pliki cookie są przesyłane do przeglądarki w nagłówku odpowiedzi *Set-Cookie* .</span><span class="sxs-lookup"><span data-stu-id="e13d0-261">Cookies travel to the browser in a *Set-Cookie* response header.</span></span> <span data-ttu-id="e13d0-262">W związku z tym wysyłanie plików cookie wymaga tego samego wywołania zwrotnego, które są używane do wysyłania nagłówków odpowiedzi:</span><span class="sxs-lookup"><span data-stu-id="e13d0-262">As a result, sending cookies requires the same callback as used for sending response headers:</span></span>

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetCookies, state: httpContext);
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

<span data-ttu-id="e13d0-263">`SetCookies`Metoda wywołania zwrotnego będzie wyglądać następująco:</span><span class="sxs-lookup"><span data-stu-id="e13d0-263">The `SetCookies` callback method would look like the following:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetCookies)]

## <a name="additional-resources"></a><span data-ttu-id="e13d0-264">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="e13d0-264">Additional resources</span></span>

* [<span data-ttu-id="e13d0-265">Obsługa protokołu HTTP i moduły HTTP — Omówienie</span><span class="sxs-lookup"><span data-stu-id="e13d0-265">HTTP Handlers and HTTP Modules Overview</span></span>](/iis/configuration/system.webserver/)
* [<span data-ttu-id="e13d0-266">Konfiguracja</span><span class="sxs-lookup"><span data-stu-id="e13d0-266">Configuration</span></span>](xref:fundamentals/configuration/index)
* [<span data-ttu-id="e13d0-267">Uruchamianie aplikacji</span><span class="sxs-lookup"><span data-stu-id="e13d0-267">Application Startup</span></span>](xref:fundamentals/startup)
* [<span data-ttu-id="e13d0-268">Oprogramowanie pośredniczące</span><span class="sxs-lookup"><span data-stu-id="e13d0-268">Middleware</span></span>](xref:fundamentals/middleware/index)
