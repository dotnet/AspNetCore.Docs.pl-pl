---
title: Testowanie składników w ASP.NET Core Blazor
author: guardrex
description: Dowiedz się, jak testować componments w Blazor aplikacjach.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/10/2020
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
uid: blazor/test
ms.openlocfilehash: 1a7b1114934f4fe7006d60bdbd0f06792d2c6935
ms.sourcegitcommit: 1436bd4d70937d6ec3140da56d96caab33c4320b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/06/2021
ms.locfileid: "102394554"
---
# <a name="test-components-in-aspnet-core-blazor"></a><span data-ttu-id="0cb4b-103">Testowanie składników w ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="0cb4b-103">Test components in ASP.NET Core Blazor</span></span>

<span data-ttu-id="0cb4b-104">Autor: [Egil Hansen](https://egilhansen.com/)</span><span class="sxs-lookup"><span data-stu-id="0cb4b-104">By: [Egil Hansen](https://egilhansen.com/)</span></span>

<span data-ttu-id="0cb4b-105">Testowanie jest ważnym aspektem tworzenia stabilnych i konserwowanych oprogramowania.</span><span class="sxs-lookup"><span data-stu-id="0cb4b-105">Testing is an important aspect of building stable and maintainable software.</span></span>

<span data-ttu-id="0cb4b-106">Aby przetestować Blazor składnik, *składnik objęty testem* (Wytnij) to:</span><span class="sxs-lookup"><span data-stu-id="0cb4b-106">To test a Blazor component, the *Component Under Test* (CUT) is:</span></span>

* <span data-ttu-id="0cb4b-107">Renderowane z odpowiednimi danymi wejściowymi dla testu.</span><span class="sxs-lookup"><span data-stu-id="0cb4b-107">Rendered with relevant input for the test.</span></span>
* <span data-ttu-id="0cb4b-108">W zależności od typu wykonanego testu, który może podlegać interakcji lub modyfikacji.</span><span class="sxs-lookup"><span data-stu-id="0cb4b-108">Depending on the type of test performed, possibly subject to interaction or modification.</span></span> <span data-ttu-id="0cb4b-109">Na przykład programy obsługi zdarzeń mogą być wyzwalane, takie jak `onclick` zdarzenie dla przycisku.</span><span class="sxs-lookup"><span data-stu-id="0cb4b-109">For example, event handlers can be triggered, such as an `onclick` event for a button.</span></span>
* <span data-ttu-id="0cb4b-110">Inspekcja pod kątem oczekiwanych wartości.</span><span class="sxs-lookup"><span data-stu-id="0cb4b-110">Inspected for expected values.</span></span>

## <a name="test-approaches"></a><span data-ttu-id="0cb4b-111">Podejścia testowe</span><span class="sxs-lookup"><span data-stu-id="0cb4b-111">Test approaches</span></span>

<span data-ttu-id="0cb4b-112">Dwa typowe podejścia do testowania Blazor składników to kompleksowe (E2E) testowanie i testowanie jednostkowe:</span><span class="sxs-lookup"><span data-stu-id="0cb4b-112">Two common approaches for testing Blazor components are end-to-end (E2E) testing and unit testing:</span></span>

* <span data-ttu-id="0cb4b-113">**Testowanie jednostkowe**: [testy jednostkowe](/dotnet/core/testing/) są napisywane przy użyciu biblioteki testów jednostkowych, która zapewnia:</span><span class="sxs-lookup"><span data-stu-id="0cb4b-113">**Unit testing**: [Unit tests](/dotnet/core/testing/) are written with a unit testing library that provides:</span></span>
  * <span data-ttu-id="0cb4b-114">Renderowanie składnika.</span><span class="sxs-lookup"><span data-stu-id="0cb4b-114">Component rendering.</span></span>
  * <span data-ttu-id="0cb4b-115">Inspekcja danych wyjściowych i stanu składnika.</span><span class="sxs-lookup"><span data-stu-id="0cb4b-115">Inspection of component output and state.</span></span>
  * <span data-ttu-id="0cb4b-116">Wyzwalanie obsługi zdarzeń i metod cyklu życia.</span><span class="sxs-lookup"><span data-stu-id="0cb4b-116">Triggering of event handlers and life cycle methods.</span></span>
  * <span data-ttu-id="0cb4b-117">Potwierdza, że zachowanie składnika jest poprawne.</span><span class="sxs-lookup"><span data-stu-id="0cb4b-117">Assertions that component behavior is correct.</span></span>

  <span data-ttu-id="0cb4b-118">[bUnit](https://github.com/egil/bUnit) to przykład biblioteki, która umożliwia Razor testowanie jednostkowe składnika.</span><span class="sxs-lookup"><span data-stu-id="0cb4b-118">[bUnit](https://github.com/egil/bUnit) is an example of a library that enables Razor component unit testing.</span></span>

* <span data-ttu-id="0cb4b-119">**Testowanie E2E**: Test Runner uruchamia Blazor aplikację zawierającą wycięte i automatyzuje wystąpienie przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="0cb4b-119">**E2E testing**: A test runner runs a Blazor app containing the CUT and automates a browser instance.</span></span> <span data-ttu-id="0cb4b-120">Narzędzie do testowania sprawdza i współdziała z wycinaniem przez przeglądarkę.</span><span class="sxs-lookup"><span data-stu-id="0cb4b-120">The testing tool inspects and interacts with the CUT through the browser.</span></span> <span data-ttu-id="0cb4b-121">[Selen](https://github.com/SeleniumHQ/selenium) to przykład platformy testowania E2E, która może być używana z Blazor aplikacjami.</span><span class="sxs-lookup"><span data-stu-id="0cb4b-121">[Selenium](https://github.com/SeleniumHQ/selenium) is an example of an E2E testing framework that can be used with Blazor apps.</span></span>

<span data-ttu-id="0cb4b-122">W teście jednostkowym Blazor dotyczy tylko składnika ( Razor /c #).</span><span class="sxs-lookup"><span data-stu-id="0cb4b-122">In unit testing, only the Blazor component (Razor/C#) is involved.</span></span> <span data-ttu-id="0cb4b-123">Zależności zewnętrzne, takie jak usługi i międzyoperacyjność w programie JS, muszą być makietne.</span><span class="sxs-lookup"><span data-stu-id="0cb4b-123">External dependencies, such as services and JS interop, must be mocked.</span></span> <span data-ttu-id="0cb4b-124">W testowaniu E2E Blazor składnik i całość infrastruktury pomocniczej są częścią testu, w tym CSS, js i interfejsów API Dom i Browser.</span><span class="sxs-lookup"><span data-stu-id="0cb4b-124">In E2E testing, the Blazor component and all of it's auxiliary infrastructure are part of the test, including CSS, JS, and the DOM and browser APIs.</span></span>

<span data-ttu-id="0cb4b-125">*Zakres testów* opisuje, jak obszerne są testy.</span><span class="sxs-lookup"><span data-stu-id="0cb4b-125">*Test scope* describes how extensive the tests are.</span></span> <span data-ttu-id="0cb4b-126">Zakres testów ma zwykle wpływ na szybkość testów.</span><span class="sxs-lookup"><span data-stu-id="0cb4b-126">Test scope typically has an influence on the speed of the tests.</span></span> <span data-ttu-id="0cb4b-127">Testy jednostkowe działają w podzestawie podsystemów aplikacji i zwykle są wykonywane w milisekundach.</span><span class="sxs-lookup"><span data-stu-id="0cb4b-127">Unit tests run on a subset of the app's subsystems and usually execute in milliseconds.</span></span> <span data-ttu-id="0cb4b-128">Testy E2E, które testują szeroką grupę podsystemów aplikacji, mogą potrwać kilka sekund.</span><span class="sxs-lookup"><span data-stu-id="0cb4b-128">E2E tests, which test a broad group of the app's subsystems, can take several seconds to complete.</span></span> 

<span data-ttu-id="0cb4b-129">Testy jednostkowe umożliwiają również dostęp do wystąpienia wycinania, co pozwala na inspekcję i weryfikację stanu wewnętrznego składnika.</span><span class="sxs-lookup"><span data-stu-id="0cb4b-129">Unit testing also provides access to the instance of the CUT, allowing for inspection and verification of the component's internal state.</span></span> <span data-ttu-id="0cb4b-130">Zwykle nie jest to możliwe w testowaniu E2E.</span><span class="sxs-lookup"><span data-stu-id="0cb4b-130">This normally isn't possible in E2E testing.</span></span>

<span data-ttu-id="0cb4b-131">W odniesieniu do środowiska składnika testy E2E muszą mieć pewność, że oczekiwany stan środowiska został osiągnięty przed rozpoczęciem weryfikacji.</span><span class="sxs-lookup"><span data-stu-id="0cb4b-131">With regard to the component's environment, E2E tests must make sure that the expected environmental state has been reached before verification starts.</span></span> <span data-ttu-id="0cb4b-132">W przeciwnym razie wynik jest nieprzewidywalny.</span><span class="sxs-lookup"><span data-stu-id="0cb4b-132">Otherwise, the result is unpredictable.</span></span> <span data-ttu-id="0cb4b-133">W przypadku testów jednostkowych, renderowanie wycinania i cyklu życia testu jest bardziej zintegrowane, co poprawia stabilność testu.</span><span class="sxs-lookup"><span data-stu-id="0cb4b-133">In unit testing, the rendering of the CUT and the life cycle of the test are more integrated, which improves test stability.</span></span>

<span data-ttu-id="0cb4b-134">Testowanie E2E polega na uruchamianiu wielu procesów, operacji we/wy na dysku oraz innych działaniach podsystemu, które często prowadzą do słabej niezawodności testu.</span><span class="sxs-lookup"><span data-stu-id="0cb4b-134">E2E testing involves launching multiple processes, network and disk I/O, and other subsystem activity that often lead to poor test reliability.</span></span> <span data-ttu-id="0cb4b-135">Testy jednostkowe są zazwyczaj izolowane od tych rodzajów problemów.</span><span class="sxs-lookup"><span data-stu-id="0cb4b-135">Unit tests are typically insulated from these sorts of issues.</span></span>

<span data-ttu-id="0cb4b-136">Poniższa tabela zawiera podsumowanie różnic między dwoma podejściami do testowania.</span><span class="sxs-lookup"><span data-stu-id="0cb4b-136">The following table summarizes the difference between the two testing approaches.</span></span>

| <span data-ttu-id="0cb4b-137">Możliwość</span><span class="sxs-lookup"><span data-stu-id="0cb4b-137">Capability</span></span>                       | <span data-ttu-id="0cb4b-138">Testowanie jednostek</span><span class="sxs-lookup"><span data-stu-id="0cb4b-138">Unit testing</span></span>                     | <span data-ttu-id="0cb4b-139">Testowanie E2E</span><span class="sxs-lookup"><span data-stu-id="0cb4b-139">E2E testing</span></span>                             |
| -------------------------------- | -------------------------------- | --------------------------------------- |
| <span data-ttu-id="0cb4b-140">Zakres testu</span><span class="sxs-lookup"><span data-stu-id="0cb4b-140">Test scope</span></span>                       | <span data-ttu-id="0cb4b-141">Blazor tylko składnik ( Razor /c #)</span><span class="sxs-lookup"><span data-stu-id="0cb4b-141">Blazor component (Razor/C#) only</span></span> | <span data-ttu-id="0cb4b-142">Blazor składnik ( Razor /c #) z CSS/JS</span><span class="sxs-lookup"><span data-stu-id="0cb4b-142">Blazor component (Razor/C#) with CSS/JS</span></span> |
| <span data-ttu-id="0cb4b-143">Czas wykonywania testu</span><span class="sxs-lookup"><span data-stu-id="0cb4b-143">Test execution time</span></span>              | <span data-ttu-id="0cb4b-144">)</span><span class="sxs-lookup"><span data-stu-id="0cb4b-144">Milliseconds</span></span>                     | <span data-ttu-id="0cb4b-145">Sekundy</span><span class="sxs-lookup"><span data-stu-id="0cb4b-145">Seconds</span></span>                                 |
| <span data-ttu-id="0cb4b-146">Dostęp do wystąpienia składnika</span><span class="sxs-lookup"><span data-stu-id="0cb4b-146">Access to the component instance</span></span> | <span data-ttu-id="0cb4b-147">Tak</span><span class="sxs-lookup"><span data-stu-id="0cb4b-147">Yes</span></span>                              | <span data-ttu-id="0cb4b-148">Nie</span><span class="sxs-lookup"><span data-stu-id="0cb4b-148">No</span></span>                                      |
| <span data-ttu-id="0cb4b-149">Poufne dla środowiska</span><span class="sxs-lookup"><span data-stu-id="0cb4b-149">Sensitive to the environment</span></span>     | <span data-ttu-id="0cb4b-150">Nie</span><span class="sxs-lookup"><span data-stu-id="0cb4b-150">No</span></span>                               | <span data-ttu-id="0cb4b-151">Tak</span><span class="sxs-lookup"><span data-stu-id="0cb4b-151">Yes</span></span>                                     |
| <span data-ttu-id="0cb4b-152">Niezawodność</span><span class="sxs-lookup"><span data-stu-id="0cb4b-152">Reliability</span></span>                      | <span data-ttu-id="0cb4b-153">Bardziej niezawodne</span><span class="sxs-lookup"><span data-stu-id="0cb4b-153">More reliable</span></span>                    | <span data-ttu-id="0cb4b-154">Mniej niezawodne</span><span class="sxs-lookup"><span data-stu-id="0cb4b-154">Less reliable</span></span>                           |

## <a name="choose-the-most-appropriate-test-approach"></a><span data-ttu-id="0cb4b-155">Wybierz najbardziej odpowiednie podejście testowe</span><span class="sxs-lookup"><span data-stu-id="0cb4b-155">Choose the most appropriate test approach</span></span>

<span data-ttu-id="0cb4b-156">Rozważmy scenariusz podczas wybierania typu testów do wykonania.</span><span class="sxs-lookup"><span data-stu-id="0cb4b-156">Consider the scenario when choosing the type of testing to perform.</span></span> <span data-ttu-id="0cb4b-157">Niektóre zagadnienia zostały opisane w poniższej tabeli.</span><span class="sxs-lookup"><span data-stu-id="0cb4b-157">Some considerations are described in the following table.</span></span>

| <span data-ttu-id="0cb4b-158">Scenariusz</span><span class="sxs-lookup"><span data-stu-id="0cb4b-158">Scenario</span></span> | <span data-ttu-id="0cb4b-159">Sugerowane podejście</span><span class="sxs-lookup"><span data-stu-id="0cb4b-159">Suggested approach</span></span> | <span data-ttu-id="0cb4b-160">Uwagi</span><span class="sxs-lookup"><span data-stu-id="0cb4b-160">Remarks</span></span> |
| -------- | ------------------ | ------- |
| <span data-ttu-id="0cb4b-161">Składnik bez logiki międzyoperacyjna JS</span><span class="sxs-lookup"><span data-stu-id="0cb4b-161">Component without JS interop logic</span></span> | <span data-ttu-id="0cb4b-162">Testowanie jednostek</span><span class="sxs-lookup"><span data-stu-id="0cb4b-162">Unit testing</span></span> | <span data-ttu-id="0cb4b-163">Jeśli w składniku nie ma zależności od współdziałania ze JS Blazor , składnik można przetestować bez dostępu do js lub interfejsu API modelu DOM.</span><span class="sxs-lookup"><span data-stu-id="0cb4b-163">When there's no dependency on JS interop in a Blazor component, the component can be tested without access to JS or the DOM API.</span></span> <span data-ttu-id="0cb4b-164">W tym scenariuszu nie ma żadnych wad, aby wybrać testy jednostkowe.</span><span class="sxs-lookup"><span data-stu-id="0cb4b-164">In this scenario, there are no disadvantages to choosing unit testing.</span></span> |
| <span data-ttu-id="0cb4b-165">Składnik z prostą logiką międzyoperacyjną JS</span><span class="sxs-lookup"><span data-stu-id="0cb4b-165">Component with simple JS interop logic</span></span> | <span data-ttu-id="0cb4b-166">Testowanie jednostek</span><span class="sxs-lookup"><span data-stu-id="0cb4b-166">Unit testing</span></span> | <span data-ttu-id="0cb4b-167">Jest to typowy element dla składników służących do wykonywania zapytań względem modelu DOM lub wyzwalania animacji za pomocą międzyoperacyjnego JS.</span><span class="sxs-lookup"><span data-stu-id="0cb4b-167">It's common for components to query the DOM or trigger animations through JS interop.</span></span> <span data-ttu-id="0cb4b-168">Testowanie jednostkowe jest zwykle preferowane w tym scenariuszu, ponieważ jest to proste, aby zasymulować interakcję JS za pomocą <xref:Microsoft.JSInterop.IJSRuntime> interfejsu.</span><span class="sxs-lookup"><span data-stu-id="0cb4b-168">Unit testing is usually preferred in this scenario, since it's straightforward to mock the JS interaction through the <xref:Microsoft.JSInterop.IJSRuntime> interface.</span></span> |
| <span data-ttu-id="0cb4b-169">Składnik, który zależy od złożonego kodu JS</span><span class="sxs-lookup"><span data-stu-id="0cb4b-169">Component that depends on complex JS code</span></span> | <span data-ttu-id="0cb4b-170">Testowanie jednostkowe i oddzielne testowanie JS</span><span class="sxs-lookup"><span data-stu-id="0cb4b-170">Unit testing and separate JS testing</span></span> | <span data-ttu-id="0cb4b-171">Jeśli składnik używa interfejsu JS Interop do wywołania dużej lub złożonej biblioteki JS, ale interakcja między Blazor biblioteką składnika i JS jest prosta, najlepszym rozwiązaniem jest prawdopodobnie traktowanie składnika i biblioteki js lub kodu jako dwóch oddzielnych części i przetestowanie każdego z nich.</span><span class="sxs-lookup"><span data-stu-id="0cb4b-171">If a component uses JS interop to call a large or complex JS library but the interaction between the Blazor component and JS library is simple, then the best approach is likely to treat the component and JS library or code as two separate parts and test each individually.</span></span> <span data-ttu-id="0cb4b-172">Przetestuj Blazor składnik przy użyciu biblioteki testów jednostkowych i przetestuj go przy użyciu biblioteki testowej js.</span><span class="sxs-lookup"><span data-stu-id="0cb4b-172">Test the Blazor component with a unit testing library, and test the JS with a JS testing library.</span></span> |
| <span data-ttu-id="0cb4b-173">Składnik z logiką, który zależy od manipulowania JS w modelu DOM przeglądarki</span><span class="sxs-lookup"><span data-stu-id="0cb4b-173">Component with logic that depends on JS manipulation of the browser DOM</span></span> | <span data-ttu-id="0cb4b-174">Testowanie E2E</span><span class="sxs-lookup"><span data-stu-id="0cb4b-174">E2E testing</span></span> | <span data-ttu-id="0cb4b-175">Gdy funkcje składnika są zależne od kodu JS i jego manipulowania modelem DOM, sprawdź, czy zarówno JS, jak i Blazor kod w teście E2E.</span><span class="sxs-lookup"><span data-stu-id="0cb4b-175">When a component's functionality is dependent on JS and its manipulation of the DOM, verify both the JS and Blazor code together in an E2E test.</span></span> <span data-ttu-id="0cb4b-176">Jest to podejście, które deweloperzy architektury pobrały Blazor za pomocą Blazor logiki renderowania przeglądarki, która ma ściśle połączony kod C# i JS.</span><span class="sxs-lookup"><span data-stu-id="0cb4b-176">This is the approach that the Blazor framework developers have taken with Blazor's browser rendering logic, which has tightly-coupled C# and JS code.</span></span> <span data-ttu-id="0cb4b-177">Kod C# i JS musi współpracować ze sobą w celu poprawnego renderowania Blazor składników w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="0cb4b-177">The C# and JS code must work together to correctly render Blazor components in a browser.</span></span>
| <span data-ttu-id="0cb4b-178">Składnik zależny od biblioteki składników innych firm z niezależnymi zależnościami</span><span class="sxs-lookup"><span data-stu-id="0cb4b-178">Component that depends on 3rd party component library with hard-to-mock dependencies</span></span> | <span data-ttu-id="0cb4b-179">Testowanie E2E</span><span class="sxs-lookup"><span data-stu-id="0cb4b-179">E2E testing</span></span> | <span data-ttu-id="0cb4b-180">Gdy funkcjonalność składnika jest zależna od biblioteki składników innej firmy, która ma trudne do makiety zależności, takich jak program JS Interop, testowanie E2E może być jedyną opcją do testowania składnika.</span><span class="sxs-lookup"><span data-stu-id="0cb4b-180">When a component's functionality is dependent on a 3rd party component library that has hard-to-mock dependencies, such as JS interop, E2E testing might be the only option to test the component.</span></span> |

## <a name="test-components-with-bunit"></a><span data-ttu-id="0cb4b-181">Składniki testowe z bUnit</span><span class="sxs-lookup"><span data-stu-id="0cb4b-181">Test components with bUnit</span></span>

<span data-ttu-id="0cb4b-182">Nie istnieje oficjalne środowisko do testowania firmy Microsoft dla programu Blazor , ale projekt oparty na społeczności [bUnit](https://github.com/egil/bUnit) zapewnia wygodny sposób na składniki testów jednostkowych Blazor .</span><span class="sxs-lookup"><span data-stu-id="0cb4b-182">There's no official Microsoft testing framework for Blazor, but the community-driven project [bUnit](https://github.com/egil/bUnit) provides a convenient way to unit test Blazor components.</span></span>

> [!NOTE]
> <span data-ttu-id="0cb4b-183">bUnit jest biblioteką testową innej firmy i nie jest obsługiwana ani utrzymywana przez firmę Microsoft.</span><span class="sxs-lookup"><span data-stu-id="0cb4b-183">bUnit is a third-party testing library and isn't supported or maintained by Microsoft.</span></span>

<span data-ttu-id="0cb4b-184">bUnit współpracuje z strukturami testowania ogólnego przeznaczenia, takimi jak [MSTest](/dotnet/core/testing/unit-testing-with-mstest), [nunit](https://nunit.org/)i [xUnit](https://xunit.github.io/).</span><span class="sxs-lookup"><span data-stu-id="0cb4b-184">bUnit works with general-purpose testing frameworks, such as [MSTest](/dotnet/core/testing/unit-testing-with-mstest), [NUnit](https://nunit.org/), and [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="0cb4b-185">Te platformy testowe sprawiają, że testy bUnit wyglądają i przypominają zwykłe testy jednostkowe.</span><span class="sxs-lookup"><span data-stu-id="0cb4b-185">These testing frameworks make bUnit tests look and feel like regular unit tests.</span></span> <span data-ttu-id="0cb4b-186">testy bUnit zintegrowane z platformą testowania ogólnego przeznaczenia są zwykle wykonywane przy użyciu:</span><span class="sxs-lookup"><span data-stu-id="0cb4b-186">bUnit tests integrated with a general-purpose testing framework are ordinarily executed with:</span></span>

* <span data-ttu-id="0cb4b-187">[Eksplorator testów programu Visual Studio](/visualstudio/test/run-unit-tests-with-test-explorer).</span><span class="sxs-lookup"><span data-stu-id="0cb4b-187">[Visual Studio's Test Explorer](/visualstudio/test/run-unit-tests-with-test-explorer).</span></span>
* <span data-ttu-id="0cb4b-188">[`dotnet test`](/dotnet/core/tools/dotnet-test) Polecenie interfejsu wiersza polecenia w powłoce poleceń.</span><span class="sxs-lookup"><span data-stu-id="0cb4b-188">[`dotnet test`](/dotnet/core/tools/dotnet-test) CLI command in a command shell.</span></span>
* <span data-ttu-id="0cb4b-189">Zautomatyzowany potok testowania DevOps.</span><span class="sxs-lookup"><span data-stu-id="0cb4b-189">An automated DevOps testing pipeline.</span></span>

> [!NOTE]
> <span data-ttu-id="0cb4b-190">Koncepcje testowe i implementacje testów w różnych strukturach testów są podobne, ale nie są identyczne.</span><span class="sxs-lookup"><span data-stu-id="0cb4b-190">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span> <span data-ttu-id="0cb4b-191">Wskazówki można znaleźć w dokumentacji platformy testowej.</span><span class="sxs-lookup"><span data-stu-id="0cb4b-191">Refer to the test framework's documentation for guidance.</span></span>

<span data-ttu-id="0cb4b-192">Poniżej przedstawiono strukturę testu bUnit na `Counter` składniku w aplikacji na podstawie [ Blazor szablonu projektu](xref:blazor/project-structure).</span><span class="sxs-lookup"><span data-stu-id="0cb4b-192">The following demonstrates the structure of a bUnit test on the `Counter` component in an app based on a [Blazor project template](xref:blazor/project-structure).</span></span> <span data-ttu-id="0cb4b-193">`Counter`Składnik wyświetla i zwiększa licznik na podstawie użytkownika wybierającego przycisk na stronie:</span><span class="sxs-lookup"><span data-stu-id="0cb4b-193">The `Counter` component displays and increments a counter based on the user selecting a button in the page:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

<span data-ttu-id="0cb4b-194">Poniższy test bUnit sprawdza, czy licznik wycinania jest zwiększany prawidłowo, gdy przycisk jest zaznaczony:</span><span class="sxs-lookup"><span data-stu-id="0cb4b-194">The following bUnit test verifies that the CUT's counter is incremented correctly when the button is selected:</span></span>

```csharp
[Fact]
public void CounterShouldIncrementWhenSelected()
{
    // Arrange
    using var ctx = new TestContext();
    var cut = ctx.RenderComponent<Counter>();
    var paraElm = cut.Find("p");

    // Act
    cut.Find("button").Click();
    var paraElmText = paraElm.TextContent;

    // Assert
    paraElmText.MarkupMatches("Current count: 1");
}
```

<span data-ttu-id="0cb4b-195">W każdym kroku testu odbywają się następujące działania:</span><span class="sxs-lookup"><span data-stu-id="0cb4b-195">The following actions take place at each step of the test:</span></span>

* <span data-ttu-id="0cb4b-196">*Porządkowanie*: `Counter` składnik jest renderowany przy użyciu bUnit `TestContext` .</span><span class="sxs-lookup"><span data-stu-id="0cb4b-196">*Arrange*: The `Counter` component is rendered using bUnit's `TestContext`.</span></span> <span data-ttu-id="0cb4b-197">Element akapitu wycinania ( `<p>` ) został znaleziony i przypisany do `paraElm` .</span><span class="sxs-lookup"><span data-stu-id="0cb4b-197">The CUT's paragraph element (`<p>`) is found and assigned to `paraElm`.</span></span>

* <span data-ttu-id="0cb4b-198">*Działanie*: element Button ( `<button>` ) jest zlokalizowany, a następnie wybierany przez wywołanie `Click` , które powinno zwiększyć licznik i zaktualizować zawartość znacznika akapitu ( `<p>` ).</span><span class="sxs-lookup"><span data-stu-id="0cb4b-198">*Act*: The button's element (`<button>`) is located and then selected by calling `Click`, which should increment the counter and update the content of the paragraph tag (`<p>`).</span></span> <span data-ttu-id="0cb4b-199">Zawartość tekstowa elementu akapitu jest uzyskiwana przez wywołanie `TextContent` .</span><span class="sxs-lookup"><span data-stu-id="0cb4b-199">The paragraph element text content is obtained by calling `TextContent`.</span></span>

* <span data-ttu-id="0cb4b-200">*Assert*: `MarkupMatches` jest wywoływana dla zawartości tekstowej w celu sprawdzenia, czy pasuje do oczekiwanego ciągu, czyli `Current count: 1` .</span><span class="sxs-lookup"><span data-stu-id="0cb4b-200">*Assert*: `MarkupMatches` is called on the text content to verify that it matches the expected string, which is `Current count: 1`.</span></span>

> [!NOTE]
> <span data-ttu-id="0cb4b-201">`MarkupMatches`Metoda Assert różni się od zwykłego potwierdzenia porównania ciągów (na przykład `Assert.Equal("Current count: 1", paraElmText);` ) `MarkupMatches` wykonuje porównanie semantyczne danych wejściowych i oczekiwanych znaczników HTML.</span><span class="sxs-lookup"><span data-stu-id="0cb4b-201">The `MarkupMatches` assert method differs from a regular string comparison assertion (for example, `Assert.Equal("Current count: 1", paraElmText);`) `MarkupMatches` performs a semantic comparison of the input and expected HTML markup.</span></span> <span data-ttu-id="0cb4b-202">Porównanie semantyczne ma świadomość semantyki języka HTML, co oznacza, że ignorowanie nieznaczących białych znaków.</span><span class="sxs-lookup"><span data-stu-id="0cb4b-202">A semantic comparison is aware of HTML semantics, meaning things like insignificant whitespace is ignored.</span></span> <span data-ttu-id="0cb4b-203">Powoduje to bardziej stabilne testy.</span><span class="sxs-lookup"><span data-stu-id="0cb4b-203">This results in more stable tests.</span></span> <span data-ttu-id="0cb4b-204">Aby uzyskać więcej informacji, zobacz [Dostosowywanie semantycznego porównania kodu HTML](https://bunit.egilhansen.com/docs/verification/semantic-html-comparison).</span><span class="sxs-lookup"><span data-stu-id="0cb4b-204">For more information, see [Customizing the Semantic HTML Comparison](https://bunit.egilhansen.com/docs/verification/semantic-html-comparison).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0cb4b-205">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="0cb4b-205">Additional resources</span></span>

* <span data-ttu-id="0cb4b-206">[Wprowadzenie z bUnit](https://bunit.egilhansen.com/docs/getting-started/): bUnit instrukcje obejmują wskazówki dotyczące tworzenia projektu testowego, odwoływania się do pakietów platformy testowania i kompilowania i uruchamiania testów.</span><span class="sxs-lookup"><span data-stu-id="0cb4b-206">[Getting Started with bUnit](https://bunit.egilhansen.com/docs/getting-started/): bUnit instructions include guidance on creating a test project, referencing testing framework packages, and building and running tests.</span></span>
