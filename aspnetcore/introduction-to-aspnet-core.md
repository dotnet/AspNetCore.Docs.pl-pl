---
title: Wprowadzenie do platformy ASP.NET Core
author: rick-anderson
description: Wprowadzenie do ASP.NET Core, wieloplatformowej, wydajnej struktury open source do tworzenia nowoczesnych aplikacji połączonych z Chmurą, połączonych z Internetem.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
no-loc:
- Blazor
- SignalR
uid: index
ms.openlocfilehash: c5a5a0ada996d88cb9252da25b5580fe0cf46f0b
ms.sourcegitcommit: 636efd1afc0a1e6fd4b12ae3a542917b356abb93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81615946"
---
# <a name="introduction-to-aspnet-core"></a><span data-ttu-id="a8d55-103">Wprowadzenie do platformy ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a8d55-103">Introduction to ASP.NET Core</span></span>

<span data-ttu-id="a8d55-104">[Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT) i [Shaun Luttin](https://twitter.com/dicshaunary)</span><span class="sxs-lookup"><span data-stu-id="a8d55-104">By [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Shaun Luttin](https://twitter.com/dicshaunary)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a8d55-105">ASP.NET Core to wieloplatformowa, wydajna struktura [open source](https://github.com/dotnet/aspnetcore) do tworzenia nowoczesnych aplikacji połączonych z Chmurą i połączonych z Internetem.</span><span class="sxs-lookup"><span data-stu-id="a8d55-105">ASP.NET Core is a cross-platform, high-performance, [open-source](https://github.com/dotnet/aspnetcore) framework for building modern, cloud-enabled, Internet-connected apps.</span></span> <span data-ttu-id="a8d55-106">Platforma ASP.NET Core umożliwia:</span><span class="sxs-lookup"><span data-stu-id="a8d55-106">With ASP.NET Core, you can:</span></span>

* <span data-ttu-id="a8d55-107">Tworzenie aplikacji i usług internetowych, aplikacji [Internetu rzeczy (IoT)](https://www.microsoft.com/internet-of-things/) i zaplecza mobilnego.</span><span class="sxs-lookup"><span data-stu-id="a8d55-107">Build web apps and services, [Internet of Things (IoT)](https://www.microsoft.com/internet-of-things/) apps, and mobile backends.</span></span>
* <span data-ttu-id="a8d55-108">Używanie ulubionych narzędzi programistycznych w systemach Windows, macOS i Linux.</span><span class="sxs-lookup"><span data-stu-id="a8d55-108">Use your favorite development tools on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="a8d55-109">Wdrażanie w chmurze lub lokalnie.</span><span class="sxs-lookup"><span data-stu-id="a8d55-109">Deploy to the cloud or on-premises.</span></span>
* <span data-ttu-id="a8d55-110">Uruchom na [.NET Core](/dotnet/core/introduction).</span><span class="sxs-lookup"><span data-stu-id="a8d55-110">Run on [.NET Core](/dotnet/core/introduction).</span></span>

## <a name="why-choose-aspnet-core"></a><span data-ttu-id="a8d55-111">Dlaczego warto wybrać ASP.NET Core?</span><span class="sxs-lookup"><span data-stu-id="a8d55-111">Why choose ASP.NET Core?</span></span>

<span data-ttu-id="a8d55-112">Miliony programistów używają lub używają [ASP.NET 4.x](/aspnet/overview) do tworzenia aplikacji internetowych.</span><span class="sxs-lookup"><span data-stu-id="a8d55-112">Millions of developers use or have used [ASP.NET 4.x](/aspnet/overview) to create web apps.</span></span> <span data-ttu-id="a8d55-113">ASP.NET Core to przeprojektowanie ASP.NET 4.x, w tym zmian architektonicznych, które skutkują szczuplejszą, bardziej modułową strukturą.</span><span class="sxs-lookup"><span data-stu-id="a8d55-113">ASP.NET Core is a redesign of ASP.NET 4.x, including architectural changes that result in a leaner, more modular framework.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a><span data-ttu-id="a8d55-114">Tworzenie internetowego interfejsu użytkownika i internetowych interfejsów API przy użyciu wzorca MVC platformy ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a8d55-114">Build web APIs and web UI using ASP.NET Core MVC</span></span>

<span data-ttu-id="a8d55-115">Platforma ASP.NET Core MVC udostępnia funkcje, które umożliwiają tworzenie [internetowych interfejsów API](xref:tutorials/first-web-api) i [aplikacji internetowych](xref:tutorials/razor-pages/index):</span><span class="sxs-lookup"><span data-stu-id="a8d55-115">ASP.NET Core MVC provides features to build [web APIs](xref:tutorials/first-web-api) and [web apps](xref:tutorials/razor-pages/index):</span></span>

* <span data-ttu-id="a8d55-116">Wzorzec [MVC (Model View Controller)](xref:mvc/overview) ułatwia zapewnienie możliwości testowania aplikacji internetowych i internetowych interfejsów API.</span><span class="sxs-lookup"><span data-stu-id="a8d55-116">The [Model-View-Controller (MVC) pattern](xref:mvc/overview) helps make your web APIs and web apps testable.</span></span>
* <span data-ttu-id="a8d55-117">[Razor Pages](xref:razor-pages/index) to oparty na stronach model programowania, który umożliwia łatwiejsze i bardziej wydajne tworzenie internetowego interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="a8d55-117">[Razor Pages](xref:razor-pages/index) is a page-based programming model that makes building web UI easier and more productive.</span></span>
* <span data-ttu-id="a8d55-118">[Składnia Razor](xref:mvc/views/razor) zapewnia wydajny język dla [stron Razor](xref:razor-pages/index) i [widoków MVC](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="a8d55-118">[Razor markup](xref:mvc/views/razor) provides a productive syntax for [Razor Pages](xref:razor-pages/index) and [MVC views](xref:mvc/views/overview).</span></span>
* <span data-ttu-id="a8d55-119">[Pomocnicy tagów](xref:mvc/views/tag-helpers/intro) umożliwiają uczestniczenie kodu po stronie serwera w tworzeniu i renderowaniu elementów HTML w plikach Razor.</span><span class="sxs-lookup"><span data-stu-id="a8d55-119">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>
* <span data-ttu-id="a8d55-120">Wbudowana obsługa [wiele formatów danych i negocjacji zawartości](xref:web-api/advanced/formatting) umożliwia internetowym interfejsom API obsługę szerokiej gamy klientów, w tym przeglądarek i urządzeń przenośnych.</span><span class="sxs-lookup"><span data-stu-id="a8d55-120">Built-in support for [multiple data formats and content negotiation](xref:web-api/advanced/formatting) lets your web APIs reach a broad range of clients, including browsers and mobile devices.</span></span>
* <span data-ttu-id="a8d55-121">[Powiązanie modelu](xref:mvc/models/model-binding) automatycznie mapuje dane z żądań HTTP na parametry metod akcji.</span><span class="sxs-lookup"><span data-stu-id="a8d55-121">[Model binding](xref:mvc/models/model-binding) automatically maps data from HTTP requests to action method parameters.</span></span>
* <span data-ttu-id="a8d55-122">[Walidacja modelu](xref:mvc/models/validation) automatycznie przeprowadza walidację po stronie klienta i serwera.</span><span class="sxs-lookup"><span data-stu-id="a8d55-122">[Model validation](xref:mvc/models/validation) automatically performs client-side and server-side validation.</span></span>

## <a name="client-side-development"></a><span data-ttu-id="a8d55-123">Programowanie po stronie klienta</span><span class="sxs-lookup"><span data-stu-id="a8d55-123">Client-side development</span></span>

<span data-ttu-id="a8d55-124">ASP.NET Core bezproblemowo integruje się z popularnymi strukturami i bibliotekami po stronie klienta, w tym [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react)i [Bootstrap.](https://getbootstrap.com/)</span><span class="sxs-lookup"><span data-stu-id="a8d55-124">ASP.NET Core integrates seamlessly with popular client-side frameworks and libraries, including [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react), and [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="a8d55-125">Aby uzyskać więcej <xref:blazor/index> informacji, zobacz i tematy pokrewne w obszarze *Rozwój po stronie klienta*.</span><span class="sxs-lookup"><span data-stu-id="a8d55-125">For more information, see <xref:blazor/index> and related topics under *Client-side development*.</span></span>

<a name="target-framework"></a>

## <a name="aspnet-core-target-frameworks"></a><span data-ttu-id="a8d55-126">ASP.NET podstawowe struktury docelowe</span><span class="sxs-lookup"><span data-stu-id="a8d55-126">ASP.NET Core target frameworks</span></span>

<span data-ttu-id="a8d55-127">ASP.NET Core 3.x i nowszych może być tylko ukierunkowane .NET Core.</span><span class="sxs-lookup"><span data-stu-id="a8d55-127">ASP.NET Core 3.x and later can only target .NET Core.</span></span> <span data-ttu-id="a8d55-128">Ogólnie rzecz biorąc ASP.NET Core składa się z bibliotek [.NET Standard.](/dotnet/standard/net-standard)</span><span class="sxs-lookup"><span data-stu-id="a8d55-128">Generally, ASP.NET Core is composed of [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="a8d55-129">Biblioteki napisane za pomocą platformy .NET Standard 2.0 są uruchamiane na dowolnej [platformie .NET, która implementuje program .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span><span class="sxs-lookup"><span data-stu-id="a8d55-129">Libraries written with .NET Standard 2.0 run on any [.NET platform that implements .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span></span>

<span data-ttu-id="a8d55-130">Jest kilka zalet przyjmowania platformy .NET Core jako docelowej, a ich liczba rośnie z każdym wydaniem.</span><span class="sxs-lookup"><span data-stu-id="a8d55-130">There are several advantages to targeting .NET Core, and these advantages increase with each release.</span></span> <span data-ttu-id="a8d55-131">Niektóre z zalet platformy .NET Core nad platformą .NET Framework to:</span><span class="sxs-lookup"><span data-stu-id="a8d55-131">Some advantages of .NET Core over .NET Framework include:</span></span>

* <span data-ttu-id="a8d55-132">Wieloplatformowość.</span><span class="sxs-lookup"><span data-stu-id="a8d55-132">Cross-platform.</span></span> <span data-ttu-id="a8d55-133">Działa w systemach Windows, macOS i Linux.</span><span class="sxs-lookup"><span data-stu-id="a8d55-133">Runs on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="a8d55-134">Większa wydajność</span><span class="sxs-lookup"><span data-stu-id="a8d55-134">Improved performance</span></span>
* [<span data-ttu-id="a8d55-135">Przechowywanie wersji obok siebie</span><span class="sxs-lookup"><span data-stu-id="a8d55-135">Side-by-side versioning</span></span>](/dotnet/standard/choosing-core-framework-server#a-need-for-side-by-side-of-net-versions-per-application-level)
* <span data-ttu-id="a8d55-136">Nowe interfejsy API</span><span class="sxs-lookup"><span data-stu-id="a8d55-136">New APIs</span></span>
* <span data-ttu-id="a8d55-137">Kod open source</span><span class="sxs-lookup"><span data-stu-id="a8d55-137">Open source</span></span>

## <a name="recommended-learning-path"></a><span data-ttu-id="a8d55-138">Zalecana ścieżka szkoleniowa</span><span class="sxs-lookup"><span data-stu-id="a8d55-138">Recommended learning path</span></span>

<span data-ttu-id="a8d55-139">Firma Microsoft zaleca następującą sekwencję samouczków dla wprowadzenia do tworzenia aplikacji ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="a8d55-139">We recommend the following sequence of tutorials for an introduction to developing ASP.NET Core apps:</span></span>

1. <span data-ttu-id="a8d55-140">Postępuj zgodnie z samouczkiem dla typu aplikacji, który chcesz opracować lub utrzymać.</span><span class="sxs-lookup"><span data-stu-id="a8d55-140">Follow a tutorial for the app type you want to develop or maintain.</span></span>

   |<span data-ttu-id="a8d55-141">Typ aplikacji</span><span class="sxs-lookup"><span data-stu-id="a8d55-141">App type</span></span>  |<span data-ttu-id="a8d55-142">Scenariusz</span><span class="sxs-lookup"><span data-stu-id="a8d55-142">Scenario</span></span>  |<span data-ttu-id="a8d55-143">Samouczek</span><span class="sxs-lookup"><span data-stu-id="a8d55-143">Tutorial</span></span>  |
   |----------|----------|----------|
   |<span data-ttu-id="a8d55-144">Aplikacja internetowa</span><span class="sxs-lookup"><span data-stu-id="a8d55-144">Web app</span></span>                   | <span data-ttu-id="a8d55-145">Nowe tworzenie interfejsu użytkownika sieci web po stronie serwera</span><span class="sxs-lookup"><span data-stu-id="a8d55-145">New server-side web UI development</span></span> |[<span data-ttu-id="a8d55-146">Wprowadzenie do korzystania ze stron Razor</span><span class="sxs-lookup"><span data-stu-id="a8d55-146">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start) |
   |<span data-ttu-id="a8d55-147">Aplikacja internetowa</span><span class="sxs-lookup"><span data-stu-id="a8d55-147">Web app</span></span>                   | <span data-ttu-id="a8d55-148">Obsługa aplikacji MVC</span><span class="sxs-lookup"><span data-stu-id="a8d55-148">Maintaining an MVC app</span></span> |[<span data-ttu-id="a8d55-149">Wprowadzenie do wzorca MVC</span><span class="sxs-lookup"><span data-stu-id="a8d55-149">Get started with MVC</span></span>](xref:tutorials/first-mvc-app/start-mvc)|
   |<span data-ttu-id="a8d55-150">Aplikacja internetowa</span><span class="sxs-lookup"><span data-stu-id="a8d55-150">Web app</span></span>                   | <span data-ttu-id="a8d55-151">Tworzenie interfejsu użytkownika sieci web po stronie klienta</span><span class="sxs-lookup"><span data-stu-id="a8d55-151">Client-side web UI development</span></span> |[<span data-ttu-id="a8d55-152">Wprowadzenie do blazora</span><span class="sxs-lookup"><span data-stu-id="a8d55-152">Get started with Blazor</span></span>](xref:tutorials/first-blazor-app) |
   |<span data-ttu-id="a8d55-153">Interfejs API sieci Web</span><span class="sxs-lookup"><span data-stu-id="a8d55-153">Web API</span></span>                   | <span data-ttu-id="a8d55-154">Restful usług HTTP</span><span class="sxs-lookup"><span data-stu-id="a8d55-154">RESTful HTTP services</span></span> |<span data-ttu-id="a8d55-155">[Tworzenie internetowego interfejsu API](xref:tutorials/first-web-api)&dagger;</span><span class="sxs-lookup"><span data-stu-id="a8d55-155">[Create a web API](xref:tutorials/first-web-api)&dagger;</span></span> |
   |<span data-ttu-id="a8d55-156">Aplikacja Do wywołania procedury zdalnej</span><span class="sxs-lookup"><span data-stu-id="a8d55-156">Remote Procedure Call app</span></span> | <span data-ttu-id="a8d55-157">Usługi oparte na kontraktach przy użyciu buforów protokołów</span><span class="sxs-lookup"><span data-stu-id="a8d55-157">Contract-first services using Protocol Buffers</span></span> |[<span data-ttu-id="a8d55-158">Wprowadzenie do usługi gRPC</span><span class="sxs-lookup"><span data-stu-id="a8d55-158">Get started with a gRPC service</span></span>](xref:tutorials/grpc/grpc-start) |
   |<span data-ttu-id="a8d55-159">Aplikacja czasu rzeczywistego</span><span class="sxs-lookup"><span data-stu-id="a8d55-159">Real-time app</span></span>             | <span data-ttu-id="a8d55-160">Dwukierunkowa komunikacja między serwerami a podłączonymi klientami</span><span class="sxs-lookup"><span data-stu-id="a8d55-160">Bidirectional communication between servers and connected clients</span></span> |[<span data-ttu-id="a8d55-161">Wprowadzenie do usługi SignalR</span><span class="sxs-lookup"><span data-stu-id="a8d55-161">Get started with SignalR</span></span>](xref:tutorials/signalr) |

1. <span data-ttu-id="a8d55-162">Postępuj zgodnie z samouczkiem, który pokazuje, jak zrobić podstawowy dostęp do danych.</span><span class="sxs-lookup"><span data-stu-id="a8d55-162">Follow a tutorial that shows how to do basic data access.</span></span>

   |<span data-ttu-id="a8d55-163">Scenariusz</span><span class="sxs-lookup"><span data-stu-id="a8d55-163">Scenario</span></span>  |<span data-ttu-id="a8d55-164">Samouczek</span><span class="sxs-lookup"><span data-stu-id="a8d55-164">Tutorial</span></span>  |
   |----------|----------|
   |<span data-ttu-id="a8d55-165">Nowy rozwój</span><span class="sxs-lookup"><span data-stu-id="a8d55-165">New development</span></span>        |[<span data-ttu-id="a8d55-166">Platforma Razor Pages z platformą Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="a8d55-166">Razor Pages with Entity Framework Core</span></span>](xref:data/ef-rp/intro) |
   |<span data-ttu-id="a8d55-167">Obsługa aplikacji MVC</span><span class="sxs-lookup"><span data-stu-id="a8d55-167">Maintaining an MVC app</span></span> |[<span data-ttu-id="a8d55-168">Wzorzec MVC z platformą Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="a8d55-168">MVC with Entity Framework Core</span></span>](xref:data/ef-mvc/intro) |

1. <span data-ttu-id="a8d55-169">Przeczytaj omówienie ASP.NET [podstawowe podstawy,](xref:fundamentals/index) które mają zastosowanie do wszystkich typów aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a8d55-169">Read an overview of ASP.NET Core [fundamentals](xref:fundamentals/index) that apply to all app types.</span></span>

1. <span data-ttu-id="a8d55-170">Przejrzyj spis treści w poszukiwaniu innych interesujących tematów.</span><span class="sxs-lookup"><span data-stu-id="a8d55-170">Browse the table of contents for other topics of interest.</span></span>

<span data-ttu-id="a8d55-171">&dagger;Istnieje również [interaktywny samouczek interfejsu API sieci.](/learn/modules/build-web-api-net-core)</span><span class="sxs-lookup"><span data-stu-id="a8d55-171">&dagger;There's also an [interactive web API tutorial](/learn/modules/build-web-api-net-core).</span></span> <span data-ttu-id="a8d55-172">Nie jest wymagana lokalna instalacja narzędzi programistycznych.</span><span class="sxs-lookup"><span data-stu-id="a8d55-172">No local installation of development tools is required.</span></span> <span data-ttu-id="a8d55-173">Kod jest uruchamiany w [usłudze Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) w przeglądarce, a [curl](https://curl.haxx.se/) jest używany do testowania.</span><span class="sxs-lookup"><span data-stu-id="a8d55-173">The code runs in an [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) in your browser, and [curl](https://curl.haxx.se/) is used for testing.</span></span>

## <a name="migrate-from-net-framework"></a><span data-ttu-id="a8d55-174">Migrowanie z programu .NET Framework</span><span class="sxs-lookup"><span data-stu-id="a8d55-174">Migrate from .NET Framework</span></span>

<span data-ttu-id="a8d55-175">Aby uzyskać przewodnik po migracji ASP.NET aplikacji 4.x do ASP.NET Core, zobacz <xref:migration/proper-to-2x/index>.</span><span class="sxs-lookup"><span data-stu-id="a8d55-175">For a reference guide to migrating ASP.NET 4.x apps to ASP.NET Core, see <xref:migration/proper-to-2x/index>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a8d55-176">ASP.NET Core to wieloplatformowa, wydajna struktura [open source](https://github.com/dotnet/aspnetcore) do tworzenia nowoczesnych aplikacji połączonych z Chmurą i połączonych z Internetem.</span><span class="sxs-lookup"><span data-stu-id="a8d55-176">ASP.NET Core is a cross-platform, high-performance, [open-source](https://github.com/dotnet/aspnetcore) framework for building modern, cloud-enabled, Internet-connected apps.</span></span> <span data-ttu-id="a8d55-177">Platforma ASP.NET Core umożliwia:</span><span class="sxs-lookup"><span data-stu-id="a8d55-177">With ASP.NET Core, you can:</span></span>

* <span data-ttu-id="a8d55-178">Tworzenie aplikacji i usług internetowych, aplikacji [Internetu rzeczy (IoT)](https://www.microsoft.com/internet-of-things/) i zaplecza mobilnego.</span><span class="sxs-lookup"><span data-stu-id="a8d55-178">Build web apps and services, [Internet of Things (IoT)](https://www.microsoft.com/internet-of-things/) apps, and mobile backends.</span></span>
* <span data-ttu-id="a8d55-179">Używanie ulubionych narzędzi programistycznych w systemach Windows, macOS i Linux.</span><span class="sxs-lookup"><span data-stu-id="a8d55-179">Use your favorite development tools on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="a8d55-180">Wdrażanie w chmurze lub lokalnie.</span><span class="sxs-lookup"><span data-stu-id="a8d55-180">Deploy to the cloud or on-premises.</span></span>
* <span data-ttu-id="a8d55-181">Uruchamianie na platformie [.NET Core lub .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).</span><span class="sxs-lookup"><span data-stu-id="a8d55-181">Run on [.NET Core or .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).</span></span>

## <a name="why-choose-aspnet-core"></a><span data-ttu-id="a8d55-182">Dlaczego warto wybrać ASP.NET Core?</span><span class="sxs-lookup"><span data-stu-id="a8d55-182">Why choose ASP.NET Core?</span></span>

<span data-ttu-id="a8d55-183">Miliony programistów używają lub używają [ASP.NET 4.x](/aspnet/overview) do tworzenia aplikacji internetowych.</span><span class="sxs-lookup"><span data-stu-id="a8d55-183">Millions of developers use or have used [ASP.NET 4.x](/aspnet/overview) to create web apps.</span></span> <span data-ttu-id="a8d55-184">ASP.NET Core to przeprojektowana platforma ASP.NET 4.x, w której wprowadzono zmiany architektoniczne w celu stworzenia bardziej zwartej i modułowej struktury.</span><span class="sxs-lookup"><span data-stu-id="a8d55-184">ASP.NET Core is a redesign of ASP.NET 4.x, with architectural changes that result in a leaner, more modular framework.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a><span data-ttu-id="a8d55-185">Tworzenie internetowego interfejsu użytkownika i internetowych interfejsów API przy użyciu wzorca MVC platformy ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a8d55-185">Build web APIs and web UI using ASP.NET Core MVC</span></span>

<span data-ttu-id="a8d55-186">Platforma ASP.NET Core MVC udostępnia funkcje, które umożliwiają tworzenie [internetowych interfejsów API](xref:tutorials/first-web-api) i [aplikacji internetowych](xref:tutorials/razor-pages/index):</span><span class="sxs-lookup"><span data-stu-id="a8d55-186">ASP.NET Core MVC provides features to build [web APIs](xref:tutorials/first-web-api) and [web apps](xref:tutorials/razor-pages/index):</span></span>

* <span data-ttu-id="a8d55-187">Wzorzec [MVC (Model View Controller)](xref:mvc/overview) ułatwia zapewnienie możliwości testowania aplikacji internetowych i internetowych interfejsów API.</span><span class="sxs-lookup"><span data-stu-id="a8d55-187">The [Model-View-Controller (MVC) pattern](xref:mvc/overview) helps make your web APIs and web apps testable.</span></span>
* <span data-ttu-id="a8d55-188">[Razor Pages](xref:razor-pages/index) to oparty na stronach model programowania, który umożliwia łatwiejsze i bardziej wydajne tworzenie internetowego interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="a8d55-188">[Razor Pages](xref:razor-pages/index) is a page-based programming model that makes building web UI easier and more productive.</span></span>
* <span data-ttu-id="a8d55-189">[Składnia Razor](xref:mvc/views/razor) zapewnia wydajny język dla [stron Razor](xref:razor-pages/index) i [widoków MVC](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="a8d55-189">[Razor markup](xref:mvc/views/razor) provides a productive syntax for [Razor Pages](xref:razor-pages/index) and [MVC views](xref:mvc/views/overview).</span></span>
* <span data-ttu-id="a8d55-190">[Pomocnicy tagów](xref:mvc/views/tag-helpers/intro) umożliwiają uczestniczenie kodu po stronie serwera w tworzeniu i renderowaniu elementów HTML w plikach Razor.</span><span class="sxs-lookup"><span data-stu-id="a8d55-190">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>
* <span data-ttu-id="a8d55-191">Wbudowana obsługa [wiele formatów danych i negocjacji zawartości](xref:web-api/advanced/formatting) umożliwia internetowym interfejsom API obsługę szerokiej gamy klientów, w tym przeglądarek i urządzeń przenośnych.</span><span class="sxs-lookup"><span data-stu-id="a8d55-191">Built-in support for [multiple data formats and content negotiation](xref:web-api/advanced/formatting) lets your web APIs reach a broad range of clients, including browsers and mobile devices.</span></span>
* <span data-ttu-id="a8d55-192">[Powiązanie modelu](xref:mvc/models/model-binding) automatycznie mapuje dane z żądań HTTP na parametry metod akcji.</span><span class="sxs-lookup"><span data-stu-id="a8d55-192">[Model binding](xref:mvc/models/model-binding) automatically maps data from HTTP requests to action method parameters.</span></span>
* <span data-ttu-id="a8d55-193">[Walidacja modelu](xref:mvc/models/validation) automatycznie przeprowadza walidację po stronie klienta i serwera.</span><span class="sxs-lookup"><span data-stu-id="a8d55-193">[Model validation](xref:mvc/models/validation) automatically performs client-side and server-side validation.</span></span>

## <a name="client-side-development"></a><span data-ttu-id="a8d55-194">Programowanie po stronie klienta</span><span class="sxs-lookup"><span data-stu-id="a8d55-194">Client-side development</span></span>

<span data-ttu-id="a8d55-195">ASP.NET Core bezproblemowo integruje się z popularnymi strukturami i bibliotekami po stronie klienta, w tym [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react)i [Bootstrap.](https://getbootstrap.com/)</span><span class="sxs-lookup"><span data-stu-id="a8d55-195">ASP.NET Core integrates seamlessly with popular client-side frameworks and libraries, including [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react), and [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="a8d55-196">Aby uzyskać więcej <xref:blazor/index> informacji, zobacz i tematy pokrewne w obszarze *Rozwój po stronie klienta*.</span><span class="sxs-lookup"><span data-stu-id="a8d55-196">For more information, see <xref:blazor/index> and related topics under *Client-side development*.</span></span>

<a name="target-framework"></a>

## <a name="aspnet-core-targeting-net-framework"></a><span data-ttu-id="a8d55-197">Platforma ASP.NET Core ukierunkowana na platformę .NET Framework</span><span class="sxs-lookup"><span data-stu-id="a8d55-197">ASP.NET Core targeting .NET Framework</span></span>

<span data-ttu-id="a8d55-198">Platforma ASP.NET Core 2.x może jako cel mieć platformę .NET Core lub .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="a8d55-198">ASP.NET Core 2.x can target .NET Core or .NET Framework.</span></span> <span data-ttu-id="a8d55-199">Aplikacje platformy ASP.NET Core ukierunkowane na platformę .NET Framework nie są wieloplatformowe &mdash; działają tylko w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="a8d55-199">ASP.NET Core apps targeting .NET Framework aren't cross-platform&mdash;they run on Windows only.</span></span> <span data-ttu-id="a8d55-200">Ogólnie rzecz biorąc, platforma ASP.NET Core 2.x jest zbudowana z bibliotek [.NET Standard](/dotnet/standard/net-standard).</span><span class="sxs-lookup"><span data-stu-id="a8d55-200">Generally, ASP.NET Core 2.x is made up of [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="a8d55-201">Biblioteki napisane za pomocą platformy .NET Standard 2.0 są uruchamiane na dowolnej [platformie .NET, która implementuje program .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span><span class="sxs-lookup"><span data-stu-id="a8d55-201">Libraries written with .NET Standard 2.0 run on any [.NET platform that implements .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span></span>

<span data-ttu-id="a8d55-202">ASP.NET Core 2.x jest obsługiwany w wersjach programu .NET Framework implementuj .NET Standard 2.0:</span><span class="sxs-lookup"><span data-stu-id="a8d55-202">ASP.NET Core 2.x is supported on .NET Framework versions that implement .NET Standard 2.0:</span></span>

* <span data-ttu-id="a8d55-203">Zalecana jest najnowsza wersja programu .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="a8d55-203">.NET Framework latest version is recommended.</span></span>
* <span data-ttu-id="a8d55-204">Platforma .NET Framework 4.6.1 lub nowsza.</span><span class="sxs-lookup"><span data-stu-id="a8d55-204">.NET Framework 4.6.1 and later.</span></span>

<span data-ttu-id="a8d55-205">Platforma ASP.NET Core 3.0 i nowsze wersje będą działać tylko na platformie .NET Core.</span><span class="sxs-lookup"><span data-stu-id="a8d55-205">ASP.NET Core 3.0 and later will only run on .NET Core.</span></span> <span data-ttu-id="a8d55-206">Aby uzyskać więcej informacji o tej zmianie, zobacz [A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/) (Pierwsze spojrzenie na zmiany wprowadzane na platformie ASP.NET Core 3.0).</span><span class="sxs-lookup"><span data-stu-id="a8d55-206">For more details regarding this change, see [A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span></span>

<span data-ttu-id="a8d55-207">Jest kilka zalet przyjmowania platformy .NET Core jako docelowej, a ich liczba rośnie z każdym wydaniem.</span><span class="sxs-lookup"><span data-stu-id="a8d55-207">There are several advantages to targeting .NET Core, and these advantages increase with each release.</span></span> <span data-ttu-id="a8d55-208">Niektóre z zalet platformy .NET Core nad platformą .NET Framework to:</span><span class="sxs-lookup"><span data-stu-id="a8d55-208">Some advantages of .NET Core over .NET Framework include:</span></span>

* <span data-ttu-id="a8d55-209">Wieloplatformowość.</span><span class="sxs-lookup"><span data-stu-id="a8d55-209">Cross-platform.</span></span> <span data-ttu-id="a8d55-210">Działa w systemach macOS, Linux i Windows.</span><span class="sxs-lookup"><span data-stu-id="a8d55-210">Runs on macOS, Linux, and Windows.</span></span>
* <span data-ttu-id="a8d55-211">Większa wydajność</span><span class="sxs-lookup"><span data-stu-id="a8d55-211">Improved performance</span></span>
* [<span data-ttu-id="a8d55-212">Przechowywanie wersji obok siebie</span><span class="sxs-lookup"><span data-stu-id="a8d55-212">Side-by-side versioning</span></span>](/dotnet/standard/choosing-core-framework-server#a-need-for-side-by-side-of-net-versions-per-application-level)
* <span data-ttu-id="a8d55-213">Nowe interfejsy API</span><span class="sxs-lookup"><span data-stu-id="a8d55-213">New APIs</span></span>
* <span data-ttu-id="a8d55-214">Kod open source</span><span class="sxs-lookup"><span data-stu-id="a8d55-214">Open source</span></span>

<span data-ttu-id="a8d55-215">Ciężko pracujemy nad zlikwidowaniem rozbieżności między interfejsami API platform .NET Framework i .NET Core.</span><span class="sxs-lookup"><span data-stu-id="a8d55-215">We're working hard to close the API gap from .NET Framework to .NET Core.</span></span> <span data-ttu-id="a8d55-216">Pakiet [Windows Compatibility Pack](/dotnet/core/porting/windows-compat-pack) udostępnił tysiące interfejsów API specyficznych dla systemu Windows na platformie .NET Core.</span><span class="sxs-lookup"><span data-stu-id="a8d55-216">The [Windows Compatibility Pack](/dotnet/core/porting/windows-compat-pack) made thousands of Windows-only APIs available in .NET Core.</span></span> <span data-ttu-id="a8d55-217">Te interfejsy API nie były dostępne na platformie .NET Core 1.x.</span><span class="sxs-lookup"><span data-stu-id="a8d55-217">These APIs weren't available in .NET Core 1.x.</span></span>

## <a name="recommended-learning-path"></a><span data-ttu-id="a8d55-218">Zalecana ścieżka szkoleniowa</span><span class="sxs-lookup"><span data-stu-id="a8d55-218">Recommended learning path</span></span>

<span data-ttu-id="a8d55-219">Przy rozpoczynaniu programowania aplikacji platformy ASP.NET Core zalecamy następujący zestaw samouczków i artykułów:</span><span class="sxs-lookup"><span data-stu-id="a8d55-219">We recommend the following sequence of tutorials and articles for an introduction to developing ASP.NET Core apps:</span></span>

1. <span data-ttu-id="a8d55-220">Postępuj zgodnie z samouczkiem typu aplikacji, którą chcesz opracować lub obsługiwać.</span><span class="sxs-lookup"><span data-stu-id="a8d55-220">Follow a tutorial for the type of app you want to develop or maintain.</span></span>

   |<span data-ttu-id="a8d55-221">Typ aplikacji</span><span class="sxs-lookup"><span data-stu-id="a8d55-221">App type</span></span>  |<span data-ttu-id="a8d55-222">Scenariusz</span><span class="sxs-lookup"><span data-stu-id="a8d55-222">Scenario</span></span>  |<span data-ttu-id="a8d55-223">Samouczek</span><span class="sxs-lookup"><span data-stu-id="a8d55-223">Tutorial</span></span>  |
   |----------|----------|----------|
   |<span data-ttu-id="a8d55-224">Aplikacja internetowa</span><span class="sxs-lookup"><span data-stu-id="a8d55-224">Web app</span></span>                   | <span data-ttu-id="a8d55-225">Programowanie od nowa</span><span class="sxs-lookup"><span data-stu-id="a8d55-225">For new development</span></span>        |[<span data-ttu-id="a8d55-226">Wprowadzenie do korzystania ze stron Razor</span><span class="sxs-lookup"><span data-stu-id="a8d55-226">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start) |
   |<span data-ttu-id="a8d55-227">Aplikacja internetowa</span><span class="sxs-lookup"><span data-stu-id="a8d55-227">Web app</span></span>                   | <span data-ttu-id="a8d55-228">Konserwacja aplikacji MVC</span><span class="sxs-lookup"><span data-stu-id="a8d55-228">For maintaining an MVC app</span></span> |[<span data-ttu-id="a8d55-229">Wprowadzenie do wzorca MVC</span><span class="sxs-lookup"><span data-stu-id="a8d55-229">Get started with MVC</span></span>](xref:tutorials/first-mvc-app/start-mvc)|
   |<span data-ttu-id="a8d55-230">Interfejs API sieci Web</span><span class="sxs-lookup"><span data-stu-id="a8d55-230">Web API</span></span>                   |                            |<span data-ttu-id="a8d55-231">[Tworzenie internetowego interfejsu API](xref:tutorials/first-web-api)&dagger;</span><span class="sxs-lookup"><span data-stu-id="a8d55-231">[Create a web API](xref:tutorials/first-web-api)&dagger;</span></span> |
   |<span data-ttu-id="a8d55-232">Aplikacja czasu rzeczywistego</span><span class="sxs-lookup"><span data-stu-id="a8d55-232">Real-time app</span></span>             |                            |[<span data-ttu-id="a8d55-233">Wprowadzenie do usługi SignalR</span><span class="sxs-lookup"><span data-stu-id="a8d55-233">Get started with SignalR</span></span>](xref:tutorials/signalr) |

1. <span data-ttu-id="a8d55-234">Postępuj zgodnie z samouczkiem, który pokazuje, jak zrobić podstawowy dostęp do danych.</span><span class="sxs-lookup"><span data-stu-id="a8d55-234">Follow a tutorial that shows how to do basic data access.</span></span>

   |<span data-ttu-id="a8d55-235">Scenariusz</span><span class="sxs-lookup"><span data-stu-id="a8d55-235">Scenario</span></span>  |<span data-ttu-id="a8d55-236">Samouczek</span><span class="sxs-lookup"><span data-stu-id="a8d55-236">Tutorial</span></span>  |
   |----------|----------|
   | <span data-ttu-id="a8d55-237">Programowanie od nowa</span><span class="sxs-lookup"><span data-stu-id="a8d55-237">For new development</span></span>        |[<span data-ttu-id="a8d55-238">Platforma Razor Pages z platformą Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="a8d55-238">Razor Pages with Entity Framework Core</span></span>](xref:data/ef-rp/intro) |
   | <span data-ttu-id="a8d55-239">Konserwacja aplikacji MVC</span><span class="sxs-lookup"><span data-stu-id="a8d55-239">For maintaining an MVC app</span></span> |[<span data-ttu-id="a8d55-240">Wzorzec MVC z platformą Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="a8d55-240">MVC with Entity Framework Core</span></span>](xref:data/ef-mvc/intro) |

1. <span data-ttu-id="a8d55-241">Przeczytaj omówienie ASP.NET [podstawowe podstawy,](xref:fundamentals/index) które mają zastosowanie do wszystkich typów aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a8d55-241">Read an overview of ASP.NET Core [fundamentals](xref:fundamentals/index) that apply to all app types.</span></span>

1. <span data-ttu-id="a8d55-242">Przeglądaj spis treści, aby znaleźć inne interesujące tematy.</span><span class="sxs-lookup"><span data-stu-id="a8d55-242">Browse the Table of Contents for other topics of interest.</span></span>

<span data-ttu-id="a8d55-243">&dagger;Istnieje również [samouczek interfejsu API sieci web, który można wykonać całkowicie w przeglądarce,](/learn/modules/build-web-api-net-core)nie jest wymagana instalacja lokalnego IDE.</span><span class="sxs-lookup"><span data-stu-id="a8d55-243">&dagger;There's also a [web API tutorial that you follow entirely in the browser](/learn/modules/build-web-api-net-core), no local IDE installation required.</span></span> <span data-ttu-id="a8d55-244">Kod jest wykonywany w usłudze [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/), a do testowania służy narzędzie [curl](https://curl.haxx.se/).</span><span class="sxs-lookup"><span data-stu-id="a8d55-244">The code runs in an [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/), and [curl](https://curl.haxx.se/) is used for testing.</span></span>

## <a name="migrate-from-net-framework"></a><span data-ttu-id="a8d55-245">Migrowanie z programu .NET Framework</span><span class="sxs-lookup"><span data-stu-id="a8d55-245">Migrate from .NET Framework</span></span>

<span data-ttu-id="a8d55-246">Aby uzyskać przewodnik po migracji ASP.NET aplikacji do ASP.NET Core, <xref:migration/proper-to-2x/index>zobacz .</span><span class="sxs-lookup"><span data-stu-id="a8d55-246">For a reference guide to migrating ASP.NET apps to ASP.NET Core, see <xref:migration/proper-to-2x/index>.</span></span>

::: moniker-end

## <a name="how-to-download-a-sample"></a><span data-ttu-id="a8d55-247">Instrukcje: pobieranie pliku</span><span class="sxs-lookup"><span data-stu-id="a8d55-247">How to download a sample</span></span>

<span data-ttu-id="a8d55-248">Wiele artykułów i samouczków zawiera linki do kodu przykładowego.</span><span class="sxs-lookup"><span data-stu-id="a8d55-248">Many of the articles and tutorials include links to sample code.</span></span>

1. <span data-ttu-id="a8d55-249">[Pobierz plik zip repozytorium ASP.NET](https://codeload.github.com/dotnet/AspNetCore.Docs/zip/master).</span><span class="sxs-lookup"><span data-stu-id="a8d55-249">[Download the ASP.NET repository zip file](https://codeload.github.com/dotnet/AspNetCore.Docs/zip/master).</span></span>
1. <span data-ttu-id="a8d55-250">Rozpakuj plik *Docs-master.zip*.</span><span class="sxs-lookup"><span data-stu-id="a8d55-250">Unzip the *Docs-master.zip* file.</span></span>
1. <span data-ttu-id="a8d55-251">Adres URL linku do przykładu pomoże Ci przejść do katalogu przykładu.</span><span class="sxs-lookup"><span data-stu-id="a8d55-251">Use the URL in the sample link to help you navigate to the sample directory.</span></span>

### <a name="preprocessor-directives-in-sample-code"></a><span data-ttu-id="a8d55-252">Dyrektywy preprocesora w przykładowym kodzie</span><span class="sxs-lookup"><span data-stu-id="a8d55-252">Preprocessor directives in sample code</span></span>

<span data-ttu-id="a8d55-253">Aby zademonstrować wiele scenariuszy, `#define` `#if-#else/#elif-#endif` przykładowe aplikacje używają dyrektyw i preprocesora do selektywnego kompilowania i uruchamiania różnych sekcji przykładowego kodu.</span><span class="sxs-lookup"><span data-stu-id="a8d55-253">To demonstrate multiple scenarios, sample apps use the `#define` and `#if-#else/#elif-#endif` preprocessor directives to selectively compile and run different sections of sample code.</span></span> <span data-ttu-id="a8d55-254">Dla tych przykładów, które korzystają z `#define` tego podejścia, ustaw dyrektywę w górnej części plików Języka C#, aby zdefiniować symbol skojarzony ze scenariuszem, który chcesz uruchomić.</span><span class="sxs-lookup"><span data-stu-id="a8d55-254">For those samples that make use of this approach, set the `#define` directive at the top of the C# files to define the symbol associated with the scenario that you want to run.</span></span> <span data-ttu-id="a8d55-255">Niektóre przykłady wymagają zdefiniowania symbolu u góry wielu plików w celu uruchomienia scenariusza.</span><span class="sxs-lookup"><span data-stu-id="a8d55-255">Some samples require defining the symbol at the top of multiple files in order to run a scenario.</span></span>

<span data-ttu-id="a8d55-256">Na przykład następująca lista symboli `#define` wskazuje, że są dostępne cztery scenariusze (jeden scenariusz na symbol).</span><span class="sxs-lookup"><span data-stu-id="a8d55-256">For example, the following `#define` symbol list indicates that four scenarios are available (one scenario per symbol).</span></span> <span data-ttu-id="a8d55-257">Aktualna konfiguracja przykładu powoduje uruchomienie scenariusza `TemplateCode`:</span><span class="sxs-lookup"><span data-stu-id="a8d55-257">The current sample configuration runs the `TemplateCode` scenario:</span></span>

```csharp
#define TemplateCode // or LogFromMain or ExpandDefault or FilterInCode
```

<span data-ttu-id="a8d55-258">Aby zmienić przykład w celu uruchomienia scenariusza `ExpandDefault`, zdefiniuj symbol `ExpandDefault` i pozostaw pozostałe symbole jako przekształcone w komentarz:</span><span class="sxs-lookup"><span data-stu-id="a8d55-258">To change the sample to run the `ExpandDefault` scenario, define the `ExpandDefault` symbol and leave the remaining symbols commented-out:</span></span>

```csharp
#define ExpandDefault // TemplateCode or LogFromMain or FilterInCode
```

<span data-ttu-id="a8d55-259">Więcej informacji na temat używania [ dyrektyw preprocesora języka C#](/dotnet/csharp/language-reference/preprocessor-directives/) do selektywnego kompilowania sekcji kodu można znaleźć w tematach [#define (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) (#define (odwołanie w języku C#)) i [#if (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) (#if (odwołanie w języku C#)).</span><span class="sxs-lookup"><span data-stu-id="a8d55-259">For more information on using [C# preprocessor directives](/dotnet/csharp/language-reference/preprocessor-directives/) to selectively compile sections of code, see [#define (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) and [#if (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if).</span></span>

### <a name="regions-in-sample-code"></a><span data-ttu-id="a8d55-260">Regiony w przykładowym kodzie</span><span class="sxs-lookup"><span data-stu-id="a8d55-260">Regions in sample code</span></span>

<span data-ttu-id="a8d55-261">Niektóre przykładowe aplikacje zawierają sekcje kodu w otoczeniu [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) i [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) dyrektyw C#.</span><span class="sxs-lookup"><span data-stu-id="a8d55-261">Some sample apps contain sections of code surrounded by [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) and [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) C# directives.</span></span> <span data-ttu-id="a8d55-262">System tworzenia dokumentacji wstawia te regiony do renderowanych tematów dokumentacji.</span><span class="sxs-lookup"><span data-stu-id="a8d55-262">The documentation build system injects these regions into the rendered documentation topics.</span></span>  

<span data-ttu-id="a8d55-263">Nazwy regionów zwykle zawierają wyraz „snippet”.</span><span class="sxs-lookup"><span data-stu-id="a8d55-263">Region names usually contain the word "snippet."</span></span> <span data-ttu-id="a8d55-264">W poniższym przykładzie pokazano region o nazwie `snippet_WebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="a8d55-264">The following example shows a region named `snippet_WebHostDefaults`:</span></span>

```csharp
#region snippet_WebHostDefaults
Host.CreateDefaultBuilder(args)
    .ConfigureWebHostDefaults(webBuilder =>
    {
        webBuilder.UseStartup<Startup>();
    });
#endregion
```

<span data-ttu-id="a8d55-265">Wcześniejszy fragment kodu w języku C# jest przywoływany w pliku markdown tematu za pomocą następującego wiersza:</span><span class="sxs-lookup"><span data-stu-id="a8d55-265">The preceding C# code snippet is referenced in the topic's markdown file with the following line:</span></span>

```md
[!code-csharp[](sample/SampleApp/Program.cs?name=snippet_WebHostDefaults)]
```

<span data-ttu-id="a8d55-266">Można bezpiecznie zignorować (lub `#region` `#endregion` usunąć) i dyrektyw, które otaczają kod.</span><span class="sxs-lookup"><span data-stu-id="a8d55-266">You may safely ignore (or remove) the `#region` and `#endregion` directives that surround the code.</span></span> <span data-ttu-id="a8d55-267">Nie należy zmieniać kodu w ramach tych dyrektyw, jeśli planujesz uruchomić przykładowe scenariusze opisane w temacie.</span><span class="sxs-lookup"><span data-stu-id="a8d55-267">Don't alter the code within these directives if you plan to run the sample scenarios described in the topic.</span></span> <span data-ttu-id="a8d55-268">Kod możesz swobodnie modyfikować, eksperymentując z innymi scenariuszami.</span><span class="sxs-lookup"><span data-stu-id="a8d55-268">Feel free to alter the code when experimenting with other scenarios.</span></span>

<span data-ttu-id="a8d55-269">Aby uzyskać więcej informacji, zobacz [Współtworzenie dokumentacji platformy ASP.NET: fragmenty kodu](https://github.com/dotnet/AspNetCore.Docs/blob/master/CONTRIBUTING.md#code-snippets).</span><span class="sxs-lookup"><span data-stu-id="a8d55-269">For more information, see [Contribute to the ASP.NET documentation: Code snippets](https://github.com/dotnet/AspNetCore.Docs/blob/master/CONTRIBUTING.md#code-snippets).</span></span>

## <a name="next-steps"></a><span data-ttu-id="a8d55-270">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="a8d55-270">Next steps</span></span>

<span data-ttu-id="a8d55-271">Więcej informacji zawierają następujące zasoby:</span><span class="sxs-lookup"><span data-stu-id="a8d55-271">For more information, see the following resources:</span></span>

* <xref:getting-started>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="a8d55-272">Platforma ASP.NET Core — podstawy</span><span class="sxs-lookup"><span data-stu-id="a8d55-272">ASP.NET Core fundamentals</span></span>](xref:fundamentals/index)
* <span data-ttu-id="a8d55-273">[Cotygodniowe podsumowanie ASP.NET Community Standup](https://live.asp.net/) zawiera aktualności o postępach i planach zespołu.</span><span class="sxs-lookup"><span data-stu-id="a8d55-273">[The weekly ASP.NET community standup](https://live.asp.net/) covers the team's progress and plans.</span></span> <span data-ttu-id="a8d55-274">Zawiera też informacje o polecanych blogach i oprogramowaniu innych firm.</span><span class="sxs-lookup"><span data-stu-id="a8d55-274">It features new blogs and third-party software.</span></span>
