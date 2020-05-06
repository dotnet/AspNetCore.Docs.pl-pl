---
title: Wprowadzenie do platformy ASP.NET Core
author: rick-anderson
description: Zapoznaj się z wprowadzeniem do ASP.NET Core, międzyplatformowej platformy typu open source do tworzenia nowoczesnych aplikacji z włączoną obsługą chmury.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: index
ms.openlocfilehash: 7f46051193681ecac59428b77ca1e36830c7bb63
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776336"
---
# <a name="introduction-to-aspnet-core"></a><span data-ttu-id="c71f2-103">Wprowadzenie do platformy ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c71f2-103">Introduction to ASP.NET Core</span></span>

<span data-ttu-id="c71f2-104">[Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT) i [Shaun Luttin](https://twitter.com/dicshaunary)</span><span class="sxs-lookup"><span data-stu-id="c71f2-104">By [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Shaun Luttin](https://twitter.com/dicshaunary)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c71f2-105">ASP.NET Core to międzyplatformowa platforma typu ["Open Source](https://github.com/dotnet/aspnetcore) ", która umożliwia tworzenie nowoczesnych aplikacji z obsługą chmury.</span><span class="sxs-lookup"><span data-stu-id="c71f2-105">ASP.NET Core is a cross-platform, high-performance, [open-source](https://github.com/dotnet/aspnetcore) framework for building modern, cloud-enabled, Internet-connected apps.</span></span> <span data-ttu-id="c71f2-106">Platforma ASP.NET Core umożliwia:</span><span class="sxs-lookup"><span data-stu-id="c71f2-106">With ASP.NET Core, you can:</span></span>

* <span data-ttu-id="c71f2-107">Twórz aplikacje sieci Web i usługi, aplikacje [Internet rzeczy (IoT)](https://www.microsoft.com/internet-of-things/) i frontony mobilne.</span><span class="sxs-lookup"><span data-stu-id="c71f2-107">Build web apps and services, [Internet of Things (IoT)](https://www.microsoft.com/internet-of-things/) apps, and mobile backends.</span></span>
* <span data-ttu-id="c71f2-108">Używanie ulubionych narzędzi programistycznych w systemach Windows, macOS i Linux.</span><span class="sxs-lookup"><span data-stu-id="c71f2-108">Use your favorite development tools on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="c71f2-109">Wdrażanie w chmurze lub lokalnie.</span><span class="sxs-lookup"><span data-stu-id="c71f2-109">Deploy to the cloud or on-premises.</span></span>
* <span data-ttu-id="c71f2-110">Uruchamiany na [platformie .NET Core](/dotnet/core/introduction).</span><span class="sxs-lookup"><span data-stu-id="c71f2-110">Run on [.NET Core](/dotnet/core/introduction).</span></span>

## <a name="why-choose-aspnet-core"></a><span data-ttu-id="c71f2-111">Dlaczego warto wybrać ASP.NET Core?</span><span class="sxs-lookup"><span data-stu-id="c71f2-111">Why choose ASP.NET Core?</span></span>

<span data-ttu-id="c71f2-112">Miliony deweloperów używają lub używały [ASP.NET 4. x](/aspnet/overview) do tworzenia aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="c71f2-112">Millions of developers use or have used [ASP.NET 4.x](/aspnet/overview) to create web apps.</span></span> <span data-ttu-id="c71f2-113">ASP.NET Core to projekt ASP.NET 4. x, w tym zmiany architektury, które powodują powstanie produkcji oszczędnej i większej struktury modularnej.</span><span class="sxs-lookup"><span data-stu-id="c71f2-113">ASP.NET Core is a redesign of ASP.NET 4.x, including architectural changes that result in a leaner, more modular framework.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a><span data-ttu-id="c71f2-114">Tworzenie internetowego interfejsu użytkownika i internetowych interfejsów API przy użyciu wzorca MVC platformy ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c71f2-114">Build web APIs and web UI using ASP.NET Core MVC</span></span>

<span data-ttu-id="c71f2-115">Platforma ASP.NET Core MVC udostępnia funkcje, które umożliwiają tworzenie [internetowych interfejsów API](xref:tutorials/first-web-api) i [aplikacji internetowych](xref:tutorials/razor-pages/index):</span><span class="sxs-lookup"><span data-stu-id="c71f2-115">ASP.NET Core MVC provides features to build [web APIs](xref:tutorials/first-web-api) and [web apps](xref:tutorials/razor-pages/index):</span></span>

* <span data-ttu-id="c71f2-116">Wzorzec [MVC (Model View Controller)](xref:mvc/overview) ułatwia zapewnienie możliwości testowania aplikacji internetowych i internetowych interfejsów API.</span><span class="sxs-lookup"><span data-stu-id="c71f2-116">The [Model-View-Controller (MVC) pattern](xref:mvc/overview) helps make your web APIs and web apps testable.</span></span>
* <span data-ttu-id="c71f2-117">[Razor Pages](xref:razor-pages/index) to oparty na stronach model programowania, który umożliwia łatwiejsze i bardziej wydajne tworzenie internetowego interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="c71f2-117">[Razor Pages](xref:razor-pages/index) is a page-based programming model that makes building web UI easier and more productive.</span></span>
* <span data-ttu-id="c71f2-118">[Składnia Razor](xref:mvc/views/razor) zapewnia wydajny język dla [stron Razor](xref:razor-pages/index) i [widoków MVC](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="c71f2-118">[Razor markup](xref:mvc/views/razor) provides a productive syntax for [Razor Pages](xref:razor-pages/index) and [MVC views](xref:mvc/views/overview).</span></span>
* <span data-ttu-id="c71f2-119">[Pomocnicy tagów](xref:mvc/views/tag-helpers/intro) umożliwiają uczestniczenie kodu po stronie serwera w tworzeniu i renderowaniu elementów HTML w plikach Razor.</span><span class="sxs-lookup"><span data-stu-id="c71f2-119">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>
* <span data-ttu-id="c71f2-120">Wbudowana obsługa [wiele formatów danych i negocjacji zawartości](xref:web-api/advanced/formatting) umożliwia internetowym interfejsom API obsługę szerokiej gamy klientów, w tym przeglądarek i urządzeń przenośnych.</span><span class="sxs-lookup"><span data-stu-id="c71f2-120">Built-in support for [multiple data formats and content negotiation](xref:web-api/advanced/formatting) lets your web APIs reach a broad range of clients, including browsers and mobile devices.</span></span>
* <span data-ttu-id="c71f2-121">[Powiązanie modelu](xref:mvc/models/model-binding) automatycznie mapuje dane z żądań HTTP na parametry metod akcji.</span><span class="sxs-lookup"><span data-stu-id="c71f2-121">[Model binding](xref:mvc/models/model-binding) automatically maps data from HTTP requests to action method parameters.</span></span>
* <span data-ttu-id="c71f2-122">[Walidacja modelu](xref:mvc/models/validation) automatycznie przeprowadza walidację po stronie klienta i serwera.</span><span class="sxs-lookup"><span data-stu-id="c71f2-122">[Model validation](xref:mvc/models/validation) automatically performs client-side and server-side validation.</span></span>

## <a name="client-side-development"></a><span data-ttu-id="c71f2-123">Programowanie po stronie klienta</span><span class="sxs-lookup"><span data-stu-id="c71f2-123">Client-side development</span></span>

<span data-ttu-id="c71f2-124">ASP.NET Core zapewnia bezproblemową integrację z popularnymi strukturami i bibliotekami po stronie klienta, takimi jak [Blazor](xref:blazor/index), [kątowy](xref:spa/angular), [reagowanie](xref:spa/react)i [Bootstrap](https://getbootstrap.com/).</span><span class="sxs-lookup"><span data-stu-id="c71f2-124">ASP.NET Core integrates seamlessly with popular client-side frameworks and libraries, including [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react), and [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="c71f2-125">Aby uzyskać więcej informacji, <xref:blazor/index> Zobacz i Tematy pokrewne w obszarze *programowanie po stronie klienta*.</span><span class="sxs-lookup"><span data-stu-id="c71f2-125">For more information, see <xref:blazor/index> and related topics under *Client-side development*.</span></span>

<a name="target-framework"></a>

## <a name="aspnet-core-target-frameworks"></a><span data-ttu-id="c71f2-126">ASP.NET Core platform docelowych</span><span class="sxs-lookup"><span data-stu-id="c71f2-126">ASP.NET Core target frameworks</span></span>

<span data-ttu-id="c71f2-127">ASP.NET Core 3. x i nowsze mogą dotyczyć tylko .NET Core.</span><span class="sxs-lookup"><span data-stu-id="c71f2-127">ASP.NET Core 3.x and later can only target .NET Core.</span></span> <span data-ttu-id="c71f2-128">Ogólnie rzecz biorąc ASP.NET Core składa się z bibliotek [.NET Standard](/dotnet/standard/net-standard) .</span><span class="sxs-lookup"><span data-stu-id="c71f2-128">Generally, ASP.NET Core is composed of [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="c71f2-129">Biblioteki z .NET Standard 2,0 są uruchamiane na dowolnej [platformie .NET implementującej .NET Standard 2,0](/dotnet/standard/net-standard#net-implementation-support).</span><span class="sxs-lookup"><span data-stu-id="c71f2-129">Libraries written with .NET Standard 2.0 run on any [.NET platform that implements .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span></span>

<span data-ttu-id="c71f2-130">Jest kilka zalet przyjmowania platformy .NET Core jako docelowej, a ich liczba rośnie z każdym wydaniem.</span><span class="sxs-lookup"><span data-stu-id="c71f2-130">There are several advantages to targeting .NET Core, and these advantages increase with each release.</span></span> <span data-ttu-id="c71f2-131">Niektóre z zalet platformy .NET Core nad platformą .NET Framework to:</span><span class="sxs-lookup"><span data-stu-id="c71f2-131">Some advantages of .NET Core over .NET Framework include:</span></span>

* <span data-ttu-id="c71f2-132">Wieloplatformowość.</span><span class="sxs-lookup"><span data-stu-id="c71f2-132">Cross-platform.</span></span> <span data-ttu-id="c71f2-133">Działa w systemach Windows, macOS i Linux.</span><span class="sxs-lookup"><span data-stu-id="c71f2-133">Runs on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="c71f2-134">Większa wydajność</span><span class="sxs-lookup"><span data-stu-id="c71f2-134">Improved performance</span></span>
* [<span data-ttu-id="c71f2-135">Przechowywanie wersji obok siebie</span><span class="sxs-lookup"><span data-stu-id="c71f2-135">Side-by-side versioning</span></span>](/dotnet/standard/choosing-core-framework-server#side-by-side-net-versions-per-application-level)
* <span data-ttu-id="c71f2-136">Nowe interfejsy API</span><span class="sxs-lookup"><span data-stu-id="c71f2-136">New APIs</span></span>
* <span data-ttu-id="c71f2-137">Kod open source</span><span class="sxs-lookup"><span data-stu-id="c71f2-137">Open source</span></span>

## <a name="recommended-learning-path"></a><span data-ttu-id="c71f2-138">Zalecana ścieżka szkoleniowa</span><span class="sxs-lookup"><span data-stu-id="c71f2-138">Recommended learning path</span></span>

<span data-ttu-id="c71f2-139">Zalecamy poniższą sekwencję samouczków, aby zapoznać się z wprowadzeniem do tworzenia aplikacji ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="c71f2-139">We recommend the following sequence of tutorials for an introduction to developing ASP.NET Core apps:</span></span>

1. <span data-ttu-id="c71f2-140">Postępuj zgodnie z samouczkiem dotyczącym typu aplikacji, który chcesz opracowywać lub obsłudze.</span><span class="sxs-lookup"><span data-stu-id="c71f2-140">Follow a tutorial for the app type you want to develop or maintain.</span></span>

   |<span data-ttu-id="c71f2-141">Typ aplikacji</span><span class="sxs-lookup"><span data-stu-id="c71f2-141">App type</span></span>  |<span data-ttu-id="c71f2-142">Scenariusz</span><span class="sxs-lookup"><span data-stu-id="c71f2-142">Scenario</span></span>  |<span data-ttu-id="c71f2-143">Samouczek</span><span class="sxs-lookup"><span data-stu-id="c71f2-143">Tutorial</span></span>  |
   |----------|----------|----------|
   |<span data-ttu-id="c71f2-144">Aplikacja internetowa</span><span class="sxs-lookup"><span data-stu-id="c71f2-144">Web app</span></span>                   | <span data-ttu-id="c71f2-145">Tworzenie nowego interfejsu użytkownika sieci Web po stronie serwera</span><span class="sxs-lookup"><span data-stu-id="c71f2-145">New server-side web UI development</span></span> |[<span data-ttu-id="c71f2-146">Wprowadzenie do korzystania ze stron Razor</span><span class="sxs-lookup"><span data-stu-id="c71f2-146">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start) |
   |<span data-ttu-id="c71f2-147">Aplikacja internetowa</span><span class="sxs-lookup"><span data-stu-id="c71f2-147">Web app</span></span>                   | <span data-ttu-id="c71f2-148">Obsługa aplikacji MVC</span><span class="sxs-lookup"><span data-stu-id="c71f2-148">Maintaining an MVC app</span></span> |[<span data-ttu-id="c71f2-149">Wprowadzenie do wzorca MVC</span><span class="sxs-lookup"><span data-stu-id="c71f2-149">Get started with MVC</span></span>](xref:tutorials/first-mvc-app/start-mvc)|
   |<span data-ttu-id="c71f2-150">Aplikacja internetowa</span><span class="sxs-lookup"><span data-stu-id="c71f2-150">Web app</span></span>                   | <span data-ttu-id="c71f2-151">Programowanie interfejsu użytkownika sieci Web po stronie klienta</span><span class="sxs-lookup"><span data-stu-id="c71f2-151">Client-side web UI development</span></span> |[<span data-ttu-id="c71f2-152">Wprowadzenie do Blazor</span><span class="sxs-lookup"><span data-stu-id="c71f2-152">Get started with Blazor</span></span>](xref:tutorials/first-blazor-app) |
   |<span data-ttu-id="c71f2-153">Interfejs API sieci Web</span><span class="sxs-lookup"><span data-stu-id="c71f2-153">Web API</span></span>                   | <span data-ttu-id="c71f2-154">RESTful usługi HTTP</span><span class="sxs-lookup"><span data-stu-id="c71f2-154">RESTful HTTP services</span></span> |<span data-ttu-id="c71f2-155">[Tworzenie internetowego interfejsu API](xref:tutorials/first-web-api)&dagger;</span><span class="sxs-lookup"><span data-stu-id="c71f2-155">[Create a web API](xref:tutorials/first-web-api)&dagger;</span></span> |
   |<span data-ttu-id="c71f2-156">Aplikacja zdalnego wywołania procedury</span><span class="sxs-lookup"><span data-stu-id="c71f2-156">Remote Procedure Call app</span></span> | <span data-ttu-id="c71f2-157">Usługi pierwszego kontraktu przy użyciu buforów protokołu</span><span class="sxs-lookup"><span data-stu-id="c71f2-157">Contract-first services using Protocol Buffers</span></span> |[<span data-ttu-id="c71f2-158">Wprowadzenie do usługi gRPC</span><span class="sxs-lookup"><span data-stu-id="c71f2-158">Get started with a gRPC service</span></span>](xref:tutorials/grpc/grpc-start) |
   |<span data-ttu-id="c71f2-159">Aplikacja czasu rzeczywistego</span><span class="sxs-lookup"><span data-stu-id="c71f2-159">Real-time app</span></span>             | <span data-ttu-id="c71f2-160">Dwukierunkowa komunikacja między serwerami i połączonymi klientami</span><span class="sxs-lookup"><span data-stu-id="c71f2-160">Bidirectional communication between servers and connected clients</span></span> |[<span data-ttu-id="c71f2-161">Wprowadzenie do usługi SignalR</span><span class="sxs-lookup"><span data-stu-id="c71f2-161">Get started with SignalR</span></span>](xref:tutorials/signalr) |

1. <span data-ttu-id="c71f2-162">Postępuj zgodnie z samouczkiem, który pokazuje, jak przeprowadzić podstawowy dostęp do danych.</span><span class="sxs-lookup"><span data-stu-id="c71f2-162">Follow a tutorial that shows how to do basic data access.</span></span>

   |<span data-ttu-id="c71f2-163">Scenariusz</span><span class="sxs-lookup"><span data-stu-id="c71f2-163">Scenario</span></span>  |<span data-ttu-id="c71f2-164">Samouczek</span><span class="sxs-lookup"><span data-stu-id="c71f2-164">Tutorial</span></span>  |
   |----------|----------|
   |<span data-ttu-id="c71f2-165">Nowe programowanie</span><span class="sxs-lookup"><span data-stu-id="c71f2-165">New development</span></span>        |[<span data-ttu-id="c71f2-166">Platforma Razor Pages z platformą Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="c71f2-166">Razor Pages with Entity Framework Core</span></span>](xref:data/ef-rp/intro) |
   |<span data-ttu-id="c71f2-167">Obsługa aplikacji MVC</span><span class="sxs-lookup"><span data-stu-id="c71f2-167">Maintaining an MVC app</span></span> |[<span data-ttu-id="c71f2-168">Wzorzec MVC z platformą Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="c71f2-168">MVC with Entity Framework Core</span></span>](xref:data/ef-mvc/intro) |

1. <span data-ttu-id="c71f2-169">Zapoznaj się z omówieniem ASP.NET Core [podstawowych](xref:fundamentals/index) , które mają zastosowanie do wszystkich typów aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c71f2-169">Read an overview of ASP.NET Core [fundamentals](xref:fundamentals/index) that apply to all app types.</span></span>

1. <span data-ttu-id="c71f2-170">Przejrzyj Spis treści pod kątem innych tematów zainteresowania.</span><span class="sxs-lookup"><span data-stu-id="c71f2-170">Browse the table of contents for other topics of interest.</span></span>

<span data-ttu-id="c71f2-171">&dagger;Dostępny jest również [Interaktywny samouczek internetowy interfejsu API](/learn/modules/build-web-api-net-core).</span><span class="sxs-lookup"><span data-stu-id="c71f2-171">&dagger;There's also an [interactive web API tutorial](/learn/modules/build-web-api-net-core).</span></span> <span data-ttu-id="c71f2-172">Nie jest wymagana instalacja lokalna narzędzi programistycznych.</span><span class="sxs-lookup"><span data-stu-id="c71f2-172">No local installation of development tools is required.</span></span> <span data-ttu-id="c71f2-173">Kod jest uruchamiany w [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) w przeglądarce, a [zwinięcie](https://curl.haxx.se/) służy do testowania.</span><span class="sxs-lookup"><span data-stu-id="c71f2-173">The code runs in an [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) in your browser, and [curl](https://curl.haxx.se/) is used for testing.</span></span>

## <a name="migrate-from-net-framework"></a><span data-ttu-id="c71f2-174">Migrowanie z .NET Framework</span><span class="sxs-lookup"><span data-stu-id="c71f2-174">Migrate from .NET Framework</span></span>

<span data-ttu-id="c71f2-175">Przewodnik referencyjny dotyczący migrowania aplikacji ASP.NET 4. x do ASP.NET Core można <xref:migration/proper-to-2x/index>znaleźć w temacie.</span><span class="sxs-lookup"><span data-stu-id="c71f2-175">For a reference guide to migrating ASP.NET 4.x apps to ASP.NET Core, see <xref:migration/proper-to-2x/index>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c71f2-176">ASP.NET Core to międzyplatformowa platforma typu ["Open Source](https://github.com/dotnet/aspnetcore) ", która umożliwia tworzenie nowoczesnych aplikacji z obsługą chmury.</span><span class="sxs-lookup"><span data-stu-id="c71f2-176">ASP.NET Core is a cross-platform, high-performance, [open-source](https://github.com/dotnet/aspnetcore) framework for building modern, cloud-enabled, Internet-connected apps.</span></span> <span data-ttu-id="c71f2-177">Platforma ASP.NET Core umożliwia:</span><span class="sxs-lookup"><span data-stu-id="c71f2-177">With ASP.NET Core, you can:</span></span>

* <span data-ttu-id="c71f2-178">Twórz aplikacje sieci Web i usługi, aplikacje [Internet rzeczy (IoT)](https://www.microsoft.com/internet-of-things/) i frontony mobilne.</span><span class="sxs-lookup"><span data-stu-id="c71f2-178">Build web apps and services, [Internet of Things (IoT)](https://www.microsoft.com/internet-of-things/) apps, and mobile backends.</span></span>
* <span data-ttu-id="c71f2-179">Używanie ulubionych narzędzi programistycznych w systemach Windows, macOS i Linux.</span><span class="sxs-lookup"><span data-stu-id="c71f2-179">Use your favorite development tools on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="c71f2-180">Wdrażanie w chmurze lub lokalnie.</span><span class="sxs-lookup"><span data-stu-id="c71f2-180">Deploy to the cloud or on-premises.</span></span>
* <span data-ttu-id="c71f2-181">Uruchamianie na platformie [.NET Core lub .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).</span><span class="sxs-lookup"><span data-stu-id="c71f2-181">Run on [.NET Core or .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).</span></span>

## <a name="why-choose-aspnet-core"></a><span data-ttu-id="c71f2-182">Dlaczego warto wybrać ASP.NET Core?</span><span class="sxs-lookup"><span data-stu-id="c71f2-182">Why choose ASP.NET Core?</span></span>

<span data-ttu-id="c71f2-183">Miliony deweloperów używają lub używały [ASP.NET 4. x](/aspnet/overview) do tworzenia aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="c71f2-183">Millions of developers use or have used [ASP.NET 4.x](/aspnet/overview) to create web apps.</span></span> <span data-ttu-id="c71f2-184">ASP.NET Core to przeprojektowana platforma ASP.NET 4.x, w której wprowadzono zmiany architektoniczne w celu stworzenia bardziej zwartej i modułowej struktury.</span><span class="sxs-lookup"><span data-stu-id="c71f2-184">ASP.NET Core is a redesign of ASP.NET 4.x, with architectural changes that result in a leaner, more modular framework.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a><span data-ttu-id="c71f2-185">Tworzenie internetowego interfejsu użytkownika i internetowych interfejsów API przy użyciu wzorca MVC platformy ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c71f2-185">Build web APIs and web UI using ASP.NET Core MVC</span></span>

<span data-ttu-id="c71f2-186">Platforma ASP.NET Core MVC udostępnia funkcje, które umożliwiają tworzenie [internetowych interfejsów API](xref:tutorials/first-web-api) i [aplikacji internetowych](xref:tutorials/razor-pages/index):</span><span class="sxs-lookup"><span data-stu-id="c71f2-186">ASP.NET Core MVC provides features to build [web APIs](xref:tutorials/first-web-api) and [web apps](xref:tutorials/razor-pages/index):</span></span>

* <span data-ttu-id="c71f2-187">Wzorzec [MVC (Model View Controller)](xref:mvc/overview) ułatwia zapewnienie możliwości testowania aplikacji internetowych i internetowych interfejsów API.</span><span class="sxs-lookup"><span data-stu-id="c71f2-187">The [Model-View-Controller (MVC) pattern](xref:mvc/overview) helps make your web APIs and web apps testable.</span></span>
* <span data-ttu-id="c71f2-188">[Razor Pages](xref:razor-pages/index) to oparty na stronach model programowania, który umożliwia łatwiejsze i bardziej wydajne tworzenie internetowego interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="c71f2-188">[Razor Pages](xref:razor-pages/index) is a page-based programming model that makes building web UI easier and more productive.</span></span>
* <span data-ttu-id="c71f2-189">[Składnia Razor](xref:mvc/views/razor) zapewnia wydajny język dla [stron Razor](xref:razor-pages/index) i [widoków MVC](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="c71f2-189">[Razor markup](xref:mvc/views/razor) provides a productive syntax for [Razor Pages](xref:razor-pages/index) and [MVC views](xref:mvc/views/overview).</span></span>
* <span data-ttu-id="c71f2-190">[Pomocnicy tagów](xref:mvc/views/tag-helpers/intro) umożliwiają uczestniczenie kodu po stronie serwera w tworzeniu i renderowaniu elementów HTML w plikach Razor.</span><span class="sxs-lookup"><span data-stu-id="c71f2-190">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>
* <span data-ttu-id="c71f2-191">Wbudowana obsługa [wiele formatów danych i negocjacji zawartości](xref:web-api/advanced/formatting) umożliwia internetowym interfejsom API obsługę szerokiej gamy klientów, w tym przeglądarek i urządzeń przenośnych.</span><span class="sxs-lookup"><span data-stu-id="c71f2-191">Built-in support for [multiple data formats and content negotiation](xref:web-api/advanced/formatting) lets your web APIs reach a broad range of clients, including browsers and mobile devices.</span></span>
* <span data-ttu-id="c71f2-192">[Powiązanie modelu](xref:mvc/models/model-binding) automatycznie mapuje dane z żądań HTTP na parametry metod akcji.</span><span class="sxs-lookup"><span data-stu-id="c71f2-192">[Model binding](xref:mvc/models/model-binding) automatically maps data from HTTP requests to action method parameters.</span></span>
* <span data-ttu-id="c71f2-193">[Walidacja modelu](xref:mvc/models/validation) automatycznie przeprowadza walidację po stronie klienta i serwera.</span><span class="sxs-lookup"><span data-stu-id="c71f2-193">[Model validation](xref:mvc/models/validation) automatically performs client-side and server-side validation.</span></span>

## <a name="client-side-development"></a><span data-ttu-id="c71f2-194">Programowanie po stronie klienta</span><span class="sxs-lookup"><span data-stu-id="c71f2-194">Client-side development</span></span>

<span data-ttu-id="c71f2-195">ASP.NET Core zapewnia bezproblemową integrację z popularnymi strukturami i bibliotekami po stronie klienta, takimi jak [Blazor](xref:blazor/index), [kątowy](xref:spa/angular), [reagowanie](xref:spa/react)i [Bootstrap](https://getbootstrap.com/).</span><span class="sxs-lookup"><span data-stu-id="c71f2-195">ASP.NET Core integrates seamlessly with popular client-side frameworks and libraries, including [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react), and [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="c71f2-196">Aby uzyskać więcej informacji, <xref:blazor/index> Zobacz i Tematy pokrewne w obszarze *programowanie po stronie klienta*.</span><span class="sxs-lookup"><span data-stu-id="c71f2-196">For more information, see <xref:blazor/index> and related topics under *Client-side development*.</span></span>

<a name="target-framework"></a>

## <a name="aspnet-core-targeting-net-framework"></a><span data-ttu-id="c71f2-197">Platforma ASP.NET Core ukierunkowana na platformę .NET Framework</span><span class="sxs-lookup"><span data-stu-id="c71f2-197">ASP.NET Core targeting .NET Framework</span></span>

<span data-ttu-id="c71f2-198">Platforma ASP.NET Core 2.x może jako cel mieć platformę .NET Core lub .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="c71f2-198">ASP.NET Core 2.x can target .NET Core or .NET Framework.</span></span> <span data-ttu-id="c71f2-199">Aplikacje platformy ASP.NET Core ukierunkowane na platformę .NET Framework nie są wieloplatformowe &mdash; działają tylko w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="c71f2-199">ASP.NET Core apps targeting .NET Framework aren't cross-platform&mdash;they run on Windows only.</span></span> <span data-ttu-id="c71f2-200">Ogólnie rzecz biorąc, platforma ASP.NET Core 2.x jest zbudowana z bibliotek [.NET Standard](/dotnet/standard/net-standard).</span><span class="sxs-lookup"><span data-stu-id="c71f2-200">Generally, ASP.NET Core 2.x is made up of [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="c71f2-201">Biblioteki z .NET Standard 2,0 są uruchamiane na dowolnej [platformie .NET implementującej .NET Standard 2,0](/dotnet/standard/net-standard#net-implementation-support).</span><span class="sxs-lookup"><span data-stu-id="c71f2-201">Libraries written with .NET Standard 2.0 run on any [.NET platform that implements .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span></span>

<span data-ttu-id="c71f2-202">ASP.NET Core 2. x jest obsługiwana w wersjach .NET Framework, które implementują .NET Standard 2,0:</span><span class="sxs-lookup"><span data-stu-id="c71f2-202">ASP.NET Core 2.x is supported on .NET Framework versions that implement .NET Standard 2.0:</span></span>

* <span data-ttu-id="c71f2-203">Zaleca się .NET Framework najnowszej wersji.</span><span class="sxs-lookup"><span data-stu-id="c71f2-203">.NET Framework latest version is recommended.</span></span>
* <span data-ttu-id="c71f2-204">Platforma .NET Framework 4.6.1 lub nowsza.</span><span class="sxs-lookup"><span data-stu-id="c71f2-204">.NET Framework 4.6.1 and later.</span></span>

<span data-ttu-id="c71f2-205">Platforma ASP.NET Core 3.0 i nowsze wersje będą działać tylko na platformie .NET Core.</span><span class="sxs-lookup"><span data-stu-id="c71f2-205">ASP.NET Core 3.0 and later will only run on .NET Core.</span></span> <span data-ttu-id="c71f2-206">Aby uzyskać więcej informacji o tej zmianie, zobacz [A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/) (Pierwsze spojrzenie na zmiany wprowadzane na platformie ASP.NET Core 3.0).</span><span class="sxs-lookup"><span data-stu-id="c71f2-206">For more details regarding this change, see [A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span></span>

<span data-ttu-id="c71f2-207">Jest kilka zalet przyjmowania platformy .NET Core jako docelowej, a ich liczba rośnie z każdym wydaniem.</span><span class="sxs-lookup"><span data-stu-id="c71f2-207">There are several advantages to targeting .NET Core, and these advantages increase with each release.</span></span> <span data-ttu-id="c71f2-208">Niektóre z zalet platformy .NET Core nad platformą .NET Framework to:</span><span class="sxs-lookup"><span data-stu-id="c71f2-208">Some advantages of .NET Core over .NET Framework include:</span></span>

* <span data-ttu-id="c71f2-209">Wieloplatformowość.</span><span class="sxs-lookup"><span data-stu-id="c71f2-209">Cross-platform.</span></span> <span data-ttu-id="c71f2-210">Działa w systemach macOS, Linux i Windows.</span><span class="sxs-lookup"><span data-stu-id="c71f2-210">Runs on macOS, Linux, and Windows.</span></span>
* <span data-ttu-id="c71f2-211">Większa wydajność</span><span class="sxs-lookup"><span data-stu-id="c71f2-211">Improved performance</span></span>
* [<span data-ttu-id="c71f2-212">Przechowywanie wersji obok siebie</span><span class="sxs-lookup"><span data-stu-id="c71f2-212">Side-by-side versioning</span></span>](/dotnet/standard/choosing-core-framework-server#side-by-side-net-versions-per-application-level)
* <span data-ttu-id="c71f2-213">Nowe interfejsy API</span><span class="sxs-lookup"><span data-stu-id="c71f2-213">New APIs</span></span>
* <span data-ttu-id="c71f2-214">Kod open source</span><span class="sxs-lookup"><span data-stu-id="c71f2-214">Open source</span></span>

<span data-ttu-id="c71f2-215">Aby pomóc w zamknięciu przerwy w interfejsie API z .NET Framework do programu .NET Core, [pakiet zgodności systemu Windows](/dotnet/core/porting/windows-compat-pack) wprowadził tysiące interfejsów API tylko dla systemu Windows dostępnych w programie .NET Core.</span><span class="sxs-lookup"><span data-stu-id="c71f2-215">To help close the API gap from .NET Framework to .NET Core, the [Windows Compatibility Pack](/dotnet/core/porting/windows-compat-pack) made thousands of Windows-only APIs available in .NET Core.</span></span> <span data-ttu-id="c71f2-216">Te interfejsy API nie były dostępne na platformie .NET Core 1.x.</span><span class="sxs-lookup"><span data-stu-id="c71f2-216">These APIs weren't available in .NET Core 1.x.</span></span>

## <a name="recommended-learning-path"></a><span data-ttu-id="c71f2-217">Zalecana ścieżka szkoleniowa</span><span class="sxs-lookup"><span data-stu-id="c71f2-217">Recommended learning path</span></span>

<span data-ttu-id="c71f2-218">Przy rozpoczynaniu programowania aplikacji platformy ASP.NET Core zalecamy następujący zestaw samouczków i artykułów:</span><span class="sxs-lookup"><span data-stu-id="c71f2-218">We recommend the following sequence of tutorials and articles for an introduction to developing ASP.NET Core apps:</span></span>

1. <span data-ttu-id="c71f2-219">Postępuj zgodnie z samouczkiem dotyczącym typu aplikacji, którą chcesz opracowywać lub obsłudze.</span><span class="sxs-lookup"><span data-stu-id="c71f2-219">Follow a tutorial for the type of app you want to develop or maintain.</span></span>

   |<span data-ttu-id="c71f2-220">Typ aplikacji</span><span class="sxs-lookup"><span data-stu-id="c71f2-220">App type</span></span>  |<span data-ttu-id="c71f2-221">Scenariusz</span><span class="sxs-lookup"><span data-stu-id="c71f2-221">Scenario</span></span>  |<span data-ttu-id="c71f2-222">Samouczek</span><span class="sxs-lookup"><span data-stu-id="c71f2-222">Tutorial</span></span>  |
   |----------|----------|----------|
   |<span data-ttu-id="c71f2-223">Aplikacja internetowa</span><span class="sxs-lookup"><span data-stu-id="c71f2-223">Web app</span></span>                   | <span data-ttu-id="c71f2-224">Programowanie od nowa</span><span class="sxs-lookup"><span data-stu-id="c71f2-224">For new development</span></span>        |[<span data-ttu-id="c71f2-225">Wprowadzenie do korzystania ze stron Razor</span><span class="sxs-lookup"><span data-stu-id="c71f2-225">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start) |
   |<span data-ttu-id="c71f2-226">Aplikacja internetowa</span><span class="sxs-lookup"><span data-stu-id="c71f2-226">Web app</span></span>                   | <span data-ttu-id="c71f2-227">Konserwacja aplikacji MVC</span><span class="sxs-lookup"><span data-stu-id="c71f2-227">For maintaining an MVC app</span></span> |[<span data-ttu-id="c71f2-228">Wprowadzenie do wzorca MVC</span><span class="sxs-lookup"><span data-stu-id="c71f2-228">Get started with MVC</span></span>](xref:tutorials/first-mvc-app/start-mvc)|
   |<span data-ttu-id="c71f2-229">Interfejs API sieci Web</span><span class="sxs-lookup"><span data-stu-id="c71f2-229">Web API</span></span>                   |                            |<span data-ttu-id="c71f2-230">[Tworzenie internetowego interfejsu API](xref:tutorials/first-web-api)&dagger;</span><span class="sxs-lookup"><span data-stu-id="c71f2-230">[Create a web API](xref:tutorials/first-web-api)&dagger;</span></span> |
   |<span data-ttu-id="c71f2-231">Aplikacja czasu rzeczywistego</span><span class="sxs-lookup"><span data-stu-id="c71f2-231">Real-time app</span></span>             |                            |[<span data-ttu-id="c71f2-232">Wprowadzenie do usługi SignalR</span><span class="sxs-lookup"><span data-stu-id="c71f2-232">Get started with SignalR</span></span>](xref:tutorials/signalr) |

1. <span data-ttu-id="c71f2-233">Postępuj zgodnie z samouczkiem, który pokazuje, jak przeprowadzić podstawowy dostęp do danych.</span><span class="sxs-lookup"><span data-stu-id="c71f2-233">Follow a tutorial that shows how to do basic data access.</span></span>

   |<span data-ttu-id="c71f2-234">Scenariusz</span><span class="sxs-lookup"><span data-stu-id="c71f2-234">Scenario</span></span>  |<span data-ttu-id="c71f2-235">Samouczek</span><span class="sxs-lookup"><span data-stu-id="c71f2-235">Tutorial</span></span>  |
   |----------|----------|
   | <span data-ttu-id="c71f2-236">Programowanie od nowa</span><span class="sxs-lookup"><span data-stu-id="c71f2-236">For new development</span></span>        |<span data-ttu-id="c71f2-237">[RazorStrony z Entity Framework Core](xref:data/ef-rp/intro)</span><span class="sxs-lookup"><span data-stu-id="c71f2-237">[Razor Pages with Entity Framework Core](xref:data/ef-rp/intro)</span></span> |
   | <span data-ttu-id="c71f2-238">Konserwacja aplikacji MVC</span><span class="sxs-lookup"><span data-stu-id="c71f2-238">For maintaining an MVC app</span></span> |[<span data-ttu-id="c71f2-239">Wzorzec MVC z platformą Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="c71f2-239">MVC with Entity Framework Core</span></span>](xref:data/ef-mvc/intro) |

1. <span data-ttu-id="c71f2-240">Zapoznaj się z omówieniem ASP.NET Core [podstawowych](xref:fundamentals/index) , które mają zastosowanie do wszystkich typów aplikacji.</span><span class="sxs-lookup"><span data-stu-id="c71f2-240">Read an overview of ASP.NET Core [fundamentals](xref:fundamentals/index) that apply to all app types.</span></span>

1. <span data-ttu-id="c71f2-241">Przeglądaj spis treści, aby znaleźć inne interesujące tematy.</span><span class="sxs-lookup"><span data-stu-id="c71f2-241">Browse the Table of Contents for other topics of interest.</span></span>

<span data-ttu-id="c71f2-242">&dagger;Dostępny jest również [samouczek interfejsu API sieci Web, który należy wykonać w całości w przeglądarce](/learn/modules/build-web-api-net-core), nie jest wymagana żadna lokalna instalacja środowiska IDE.</span><span class="sxs-lookup"><span data-stu-id="c71f2-242">&dagger;There's also a [web API tutorial that you follow entirely in the browser](/learn/modules/build-web-api-net-core), no local IDE installation required.</span></span> <span data-ttu-id="c71f2-243">Kod jest wykonywany w usłudze [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/), a do testowania służy narzędzie [curl](https://curl.haxx.se/).</span><span class="sxs-lookup"><span data-stu-id="c71f2-243">The code runs in an [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/), and [curl](https://curl.haxx.se/) is used for testing.</span></span>

## <a name="migrate-from-net-framework"></a><span data-ttu-id="c71f2-244">Migrowanie z .NET Framework</span><span class="sxs-lookup"><span data-stu-id="c71f2-244">Migrate from .NET Framework</span></span>

<span data-ttu-id="c71f2-245">Przewodnik referencyjny dotyczący migrowania aplikacji ASP.NET do ASP.NET Core można <xref:migration/proper-to-2x/index>znaleźć w temacie.</span><span class="sxs-lookup"><span data-stu-id="c71f2-245">For a reference guide to migrating ASP.NET apps to ASP.NET Core, see <xref:migration/proper-to-2x/index>.</span></span>

::: moniker-end

## <a name="how-to-download-a-sample"></a><span data-ttu-id="c71f2-246">Instrukcje: pobieranie pliku</span><span class="sxs-lookup"><span data-stu-id="c71f2-246">How to download a sample</span></span>

<span data-ttu-id="c71f2-247">Wiele artykułów i samouczków zawiera linki do kodu przykładowego.</span><span class="sxs-lookup"><span data-stu-id="c71f2-247">Many of the articles and tutorials include links to sample code.</span></span>

1. <span data-ttu-id="c71f2-248">[Pobierz plik zip repozytorium ASP.NET](https://codeload.github.com/dotnet/AspNetCore.Docs/zip/master).</span><span class="sxs-lookup"><span data-stu-id="c71f2-248">[Download the ASP.NET repository zip file](https://codeload.github.com/dotnet/AspNetCore.Docs/zip/master).</span></span>
1. <span data-ttu-id="c71f2-249">Rozpakuj plik *Docs-master.zip*.</span><span class="sxs-lookup"><span data-stu-id="c71f2-249">Unzip the *Docs-master.zip* file.</span></span>
1. <span data-ttu-id="c71f2-250">Adres URL linku do przykładu pomoże Ci przejść do katalogu przykładu.</span><span class="sxs-lookup"><span data-stu-id="c71f2-250">Use the URL in the sample link to help you navigate to the sample directory.</span></span>

### <a name="preprocessor-directives-in-sample-code"></a><span data-ttu-id="c71f2-251">Dyrektywy preprocesora w przykładowym kodzie</span><span class="sxs-lookup"><span data-stu-id="c71f2-251">Preprocessor directives in sample code</span></span>

<span data-ttu-id="c71f2-252">Aby przedstawić wiele scenariuszy, przykładowe aplikacje wykorzystują `#define` dyrektywy `#if-#else/#elif-#endif` i preprocesora, aby wybiórczo kompilować i uruchamiać różne sekcje przykładowego kodu.</span><span class="sxs-lookup"><span data-stu-id="c71f2-252">To demonstrate multiple scenarios, sample apps use the `#define` and `#if-#else/#elif-#endif` preprocessor directives to selectively compile and run different sections of sample code.</span></span> <span data-ttu-id="c71f2-253">Dla tych przykładów, które korzystają z tego podejścia, należy ustawić `#define` dyrektywę w górnej części plików C#, aby zdefiniować symbol skojarzony z scenariuszem, który chcesz uruchomić.</span><span class="sxs-lookup"><span data-stu-id="c71f2-253">For those samples that make use of this approach, set the `#define` directive at the top of the C# files to define the symbol associated with the scenario that you want to run.</span></span> <span data-ttu-id="c71f2-254">Niektóre przykłady wymagają zdefiniowania symbolu w górnej części wielu plików, aby można było uruchomić scenariusz.</span><span class="sxs-lookup"><span data-stu-id="c71f2-254">Some samples require defining the symbol at the top of multiple files in order to run a scenario.</span></span>

<span data-ttu-id="c71f2-255">Na przykład następująca lista symboli `#define` wskazuje, że są dostępne cztery scenariusze (jeden scenariusz na symbol).</span><span class="sxs-lookup"><span data-stu-id="c71f2-255">For example, the following `#define` symbol list indicates that four scenarios are available (one scenario per symbol).</span></span> <span data-ttu-id="c71f2-256">Aktualna konfiguracja przykładu powoduje uruchomienie scenariusza `TemplateCode`:</span><span class="sxs-lookup"><span data-stu-id="c71f2-256">The current sample configuration runs the `TemplateCode` scenario:</span></span>

```csharp
#define TemplateCode // or LogFromMain or ExpandDefault or FilterInCode
```

<span data-ttu-id="c71f2-257">Aby zmienić przykład w celu uruchomienia scenariusza `ExpandDefault`, zdefiniuj symbol `ExpandDefault` i pozostaw pozostałe symbole jako przekształcone w komentarz:</span><span class="sxs-lookup"><span data-stu-id="c71f2-257">To change the sample to run the `ExpandDefault` scenario, define the `ExpandDefault` symbol and leave the remaining symbols commented-out:</span></span>

```csharp
#define ExpandDefault // TemplateCode or LogFromMain or FilterInCode
```

<span data-ttu-id="c71f2-258">Więcej informacji na temat używania [ dyrektyw preprocesora języka C#](/dotnet/csharp/language-reference/preprocessor-directives/) do selektywnego kompilowania sekcji kodu można znaleźć w tematach [#define (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) (#define (odwołanie w języku C#)) i [#if (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) (#if (odwołanie w języku C#)).</span><span class="sxs-lookup"><span data-stu-id="c71f2-258">For more information on using [C# preprocessor directives](/dotnet/csharp/language-reference/preprocessor-directives/) to selectively compile sections of code, see [#define (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) and [#if (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if).</span></span>

### <a name="regions-in-sample-code"></a><span data-ttu-id="c71f2-259">Regiony w przykładowym kodzie</span><span class="sxs-lookup"><span data-stu-id="c71f2-259">Regions in sample code</span></span>

<span data-ttu-id="c71f2-260">Niektóre przykładowe aplikacje zawierają sekcje kodu otoczone dyrektywami [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) i [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) C#.</span><span class="sxs-lookup"><span data-stu-id="c71f2-260">Some sample apps contain sections of code surrounded by [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) and [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) C# directives.</span></span> <span data-ttu-id="c71f2-261">System tworzenia dokumentacji wstawia te regiony do renderowanych tematów dokumentacji.</span><span class="sxs-lookup"><span data-stu-id="c71f2-261">The documentation build system injects these regions into the rendered documentation topics.</span></span>  

<span data-ttu-id="c71f2-262">Nazwy regionów zwykle zawierają wyraz „snippet”.</span><span class="sxs-lookup"><span data-stu-id="c71f2-262">Region names usually contain the word "snippet."</span></span> <span data-ttu-id="c71f2-263">W poniższym przykładzie pokazano region o nazwie `snippet_WebHostDefaults`:</span><span class="sxs-lookup"><span data-stu-id="c71f2-263">The following example shows a region named `snippet_WebHostDefaults`:</span></span>

```csharp
#region snippet_WebHostDefaults
Host.CreateDefaultBuilder(args)
    .ConfigureWebHostDefaults(webBuilder =>
    {
        webBuilder.UseStartup<Startup>();
    });
#endregion
```

<span data-ttu-id="c71f2-264">Wcześniejszy fragment kodu w języku C# jest przywoływany w pliku markdown tematu za pomocą następującego wiersza:</span><span class="sxs-lookup"><span data-stu-id="c71f2-264">The preceding C# code snippet is referenced in the topic's markdown file with the following line:</span></span>

```md
[!code-csharp[](sample/SampleApp/Program.cs?name=snippet_WebHostDefaults)]
```

<span data-ttu-id="c71f2-265">Licencjobiorca może bezpiecznie zignorować (lub usunąć) `#region` dyrektywy `#endregion` i, które są otaczające kod.</span><span class="sxs-lookup"><span data-stu-id="c71f2-265">You may safely ignore (or remove) the `#region` and `#endregion` directives that surround the code.</span></span> <span data-ttu-id="c71f2-266">Nie zmieniaj kodu w ramach tych dyrektyw, jeśli planujesz uruchamiać przykładowe scenariusze opisane w temacie.</span><span class="sxs-lookup"><span data-stu-id="c71f2-266">Don't alter the code within these directives if you plan to run the sample scenarios described in the topic.</span></span> <span data-ttu-id="c71f2-267">Kod możesz swobodnie modyfikować, eksperymentując z innymi scenariuszami.</span><span class="sxs-lookup"><span data-stu-id="c71f2-267">Feel free to alter the code when experimenting with other scenarios.</span></span>

<span data-ttu-id="c71f2-268">Aby uzyskać więcej informacji, zobacz [Współtworzenie dokumentacji platformy ASP.NET: fragmenty kodu](https://github.com/dotnet/AspNetCore.Docs/blob/master/CONTRIBUTING.md#code-snippets).</span><span class="sxs-lookup"><span data-stu-id="c71f2-268">For more information, see [Contribute to the ASP.NET documentation: Code snippets](https://github.com/dotnet/AspNetCore.Docs/blob/master/CONTRIBUTING.md#code-snippets).</span></span>

## <a name="next-steps"></a><span data-ttu-id="c71f2-269">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="c71f2-269">Next steps</span></span>

<span data-ttu-id="c71f2-270">Więcej informacji zawierają następujące zasoby:</span><span class="sxs-lookup"><span data-stu-id="c71f2-270">For more information, see the following resources:</span></span>

* <xref:getting-started>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="c71f2-271">Platforma ASP.NET Core — podstawy</span><span class="sxs-lookup"><span data-stu-id="c71f2-271">ASP.NET Core fundamentals</span></span>](xref:fundamentals/index)
* <span data-ttu-id="c71f2-272">[Cotygodniowe podsumowanie ASP.NET Community Standup](https://live.asp.net/) zawiera aktualności o postępach i planach zespołu.</span><span class="sxs-lookup"><span data-stu-id="c71f2-272">[The weekly ASP.NET community standup](https://live.asp.net/) covers the team's progress and plans.</span></span> <span data-ttu-id="c71f2-273">Zawiera też informacje o polecanych blogach i oprogramowaniu innych firm.</span><span class="sxs-lookup"><span data-stu-id="c71f2-273">It features new blogs and third-party software.</span></span>
