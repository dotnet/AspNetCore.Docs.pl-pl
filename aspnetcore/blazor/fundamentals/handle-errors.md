---
title: Obsługa błędów w Blazor aplikacjach ASP.NET Core
author: guardrex
description: Odkryj, jak ASP.NET Core Blazor jak program Blazor zarządza nieobsługiwanymi wyjątkami i jak opracowywać aplikacje wykrywające i obsługujące błędy.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/25/2021
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
uid: blazor/fundamentals/handle-errors
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 96f4d7fcacf1f8eb03ffe83ba18b353e5588448e
ms.sourcegitcommit: a1db01b4d3bd8c57d7a9c94ce122a6db68002d66
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102109714"
---
# <a name="handle-errors-in-aspnet-core-blazor-apps"></a><span data-ttu-id="92309-103">Obsługa błędów w Blazor aplikacjach ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="92309-103">Handle errors in ASP.NET Core Blazor apps</span></span>

<span data-ttu-id="92309-104">W tym artykule opisano, jak Blazor zarządzać nieobsługiwanymi wyjątkami oraz jak opracowywać aplikacje wykrywające i obsługujące błędy.</span><span class="sxs-lookup"><span data-stu-id="92309-104">This article describes how Blazor manages unhandled exceptions and how to develop apps that detect and handle errors.</span></span>

::: zone pivot="webassembly"

## <a name="detailed-errors-during-development"></a><span data-ttu-id="92309-105">Szczegóły błędów podczas opracowywania</span><span class="sxs-lookup"><span data-stu-id="92309-105">Detailed errors during development</span></span>

<span data-ttu-id="92309-106">Gdy Blazor aplikacja nie działa prawidłowo podczas opracowywania, otrzymuje szczegółowe informacje o błędzie z aplikacji, które pomagają w rozwiązywaniu problemów i rozwiązaniu problemu.</span><span class="sxs-lookup"><span data-stu-id="92309-106">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="92309-107">Gdy wystąpi błąd, Blazor w dolnej części ekranu aplikacje są wyświetlane żółte paski:</span><span class="sxs-lookup"><span data-stu-id="92309-107">When an error occurs, Blazor apps display a light yellow bar at the bottom of the screen:</span></span>

* <span data-ttu-id="92309-108">Podczas tworzenia pasek kieruje użytkownika do konsoli przeglądarki, gdzie można zobaczyć wyjątek.</span><span class="sxs-lookup"><span data-stu-id="92309-108">During development, the bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="92309-109">W środowisku produkcyjnym pasek powiadamia użytkownika o wystąpieniu błędu i zaleca odświeżenie przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="92309-109">In production, the bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="92309-110">Interfejs użytkownika dla tego środowiska obsługi błędów jest częścią Blazor szablonów projektu.</span><span class="sxs-lookup"><span data-stu-id="92309-110">The UI for this error handling experience is part of the Blazor project templates.</span></span>

<span data-ttu-id="92309-111">W Blazor WebAssembly aplikacji Dostosuj środowisko w `wwwroot/index.html` pliku:</span><span class="sxs-lookup"><span data-stu-id="92309-111">In a Blazor WebAssembly app, customize the experience in the `wwwroot/index.html` file:</span></span>

```html
<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="92309-112">`blazor-error-ui`Element jest zwykle ukryty z powodu obecności `display: none` stylu `blazor-error-ui` klasy CSS w arkuszu stylów aplikacji ( `wwwroot/css/app.css` ).</span><span class="sxs-lookup"><span data-stu-id="92309-112">The `blazor-error-ui` element is normally hidden due the presence of the `display: none` style of the `blazor-error-ui` CSS class in the app's stylesheet (`wwwroot/css/app.css`).</span></span> <span data-ttu-id="92309-113">Gdy wystąpi błąd, struktura ma zastosowanie `display: block` do elementu.</span><span class="sxs-lookup"><span data-stu-id="92309-113">When an error occurs, the framework applies `display: block` to the element.</span></span>

## <a name="manage-unhandled-exceptions-in-developer-code"></a><span data-ttu-id="92309-114">Zarządzanie nieobsługiwanymi wyjątkami w kodzie dewelopera</span><span class="sxs-lookup"><span data-stu-id="92309-114">Manage unhandled exceptions in developer code</span></span>

<span data-ttu-id="92309-115">Aby aplikacja kontynuowała działanie po wystąpieniu błędu, aplikacja musi mieć logikę obsługi błędów.</span><span class="sxs-lookup"><span data-stu-id="92309-115">For an app to continue after an error, the app must have error handling logic.</span></span> <span data-ttu-id="92309-116">W dalszej części tego artykułu opisano potencjalne źródła nieobsłużonych wyjątków.</span><span class="sxs-lookup"><span data-stu-id="92309-116">Later sections of this article describe potential sources of unhandled exceptions.</span></span>

<span data-ttu-id="92309-117">W środowisku produkcyjnym nie Renderuj komunikatów wyjątków struktury ani śladów stosu w interfejsie użytkownika.</span><span class="sxs-lookup"><span data-stu-id="92309-117">In production, don't render framework exception messages or stack traces in the UI.</span></span> <span data-ttu-id="92309-118">Renderowanie komunikatów o wyjątkach lub śladów stosu może:</span><span class="sxs-lookup"><span data-stu-id="92309-118">Rendering exception messages or stack traces could:</span></span>

* <span data-ttu-id="92309-119">Ujawnianie poufnych informacji użytkownikom końcowym.</span><span class="sxs-lookup"><span data-stu-id="92309-119">Disclose sensitive information to end users.</span></span>
* <span data-ttu-id="92309-120">Pomóż złośliwemu użytkownikowi wykrywać słabe strony w aplikacji, która może naruszyć bezpieczeństwo aplikacji, serwera lub sieci.</span><span class="sxs-lookup"><span data-stu-id="92309-120">Help a malicious user discover weaknesses in an app that can compromise the security of the app, server, or network.</span></span>

## <a name="global-exception-handling"></a><span data-ttu-id="92309-121">Globalna obsługa wyjątków</span><span class="sxs-lookup"><span data-stu-id="92309-121">Global exception handling</span></span>

[!INCLUDE[](~/blazor/includes/handle-errors/global-exception-handling.md)]

## <a name="log-errors-with-a-persistent-provider"></a><span data-ttu-id="92309-122">Rejestrowanie błędów przez dostawcę trwałego</span><span class="sxs-lookup"><span data-stu-id="92309-122">Log errors with a persistent provider</span></span>

<span data-ttu-id="92309-123">Jeśli wystąpi nieobsługiwany wyjątek, wyjątek jest rejestrowany w <xref:Microsoft.Extensions.Logging.ILogger> wystąpieniach skonfigurowanych w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="92309-123">If an unhandled exception occurs, the exception is logged to <xref:Microsoft.Extensions.Logging.ILogger> instances configured in the service container.</span></span> <span data-ttu-id="92309-124">Domyślnie Blazor aplikacje rejestrują dane wyjściowe konsoli z dostawcą rejestrowania konsoli.</span><span class="sxs-lookup"><span data-stu-id="92309-124">By default, Blazor apps log to console output with the Console Logging Provider.</span></span> <span data-ttu-id="92309-125">Należy rozważyć logowanie do bardziej trwałej lokalizacji na serwerze, wysyłając informacje o błędzie do interfejsu API sieci Web zaplecza, który używa dostawcy rejestrowania z zarządzaniem rozmiarem dziennika i rotacją dzienników.</span><span class="sxs-lookup"><span data-stu-id="92309-125">Consider logging to a more permanent location on the server by sending error information to a backend web API that uses a logging provider with log size management and log rotation.</span></span> <span data-ttu-id="92309-126">Alternatywnie aplikacja interfejsu API sieci Web zaplecza może używać usługi zarządzania wydajnością aplikacji (APM), takiej jak [Azure Application Insights (Azure monitor) &dagger; ](/azure/azure-monitor/app/app-insights-overview), aby rejestrować informacje o błędach odbierane od klientów.</span><span class="sxs-lookup"><span data-stu-id="92309-126">Alternatively, the backend web API app can use an Application Performance Management (APM) service, such as [Azure Application Insights (Azure Monitor)&dagger;](/azure/azure-monitor/app/app-insights-overview), to record error information that it receives from clients.</span></span>

<span data-ttu-id="92309-127">Należy zdecydować, które zdarzenia mają być rejestrowane, oraz poziom ważności zarejestrowanych zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="92309-127">You must decide which incidents to log and the level of severity of logged incidents.</span></span> <span data-ttu-id="92309-128">Nieszkodliwi użytkownicy mogą być w stanie wyzwolić błędy w sposób celowy.</span><span class="sxs-lookup"><span data-stu-id="92309-128">Hostile users might be able to trigger errors deliberately.</span></span> <span data-ttu-id="92309-129">Na przykład nie należy rejestrować zdarzenia z błędu, gdy `ProductId` w adresie URL składnika, który zawiera szczegółowe informacje o produkcie.</span><span class="sxs-lookup"><span data-stu-id="92309-129">For example, don't log an incident from an error where an unknown `ProductId` is supplied in the URL of a component that displays product details.</span></span> <span data-ttu-id="92309-130">Nie wszystkie błędy powinny być traktowane jako zdarzenia do rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="92309-130">Not all errors should be treated as incidents for logging.</span></span>

<span data-ttu-id="92309-131">Aby uzyskać więcej informacji, zobacz następujące artykuły:</span><span class="sxs-lookup"><span data-stu-id="92309-131">For more information, see the following articles:</span></span>

* <xref:blazor/fundamentals/logging>
* <xref:fundamentals/error-handling>&Dagger;
* <xref:web-api/index>

<span data-ttu-id="92309-132">&dagger;Natywne funkcje [Application Insights](/azure/azure-monitor/app/app-insights-overview) obsługujące Blazor WebAssembly aplikacje i natywną Blazor obsługę platformy [Google Analytics](https://analytics.google.com/analytics/web/) mogą być dostępne w przyszłych wersjach tych technologii.</span><span class="sxs-lookup"><span data-stu-id="92309-132">&dagger;Native [Application Insights](/azure/azure-monitor/app/app-insights-overview) features to support Blazor WebAssembly apps and native Blazor framework support for [Google Analytics](https://analytics.google.com/analytics/web/) might become available in future releases of these technologies.</span></span> <span data-ttu-id="92309-133">Aby uzyskać więcej informacji, zobacz temat [Obsługa usługi App Insights na Blazor stronie klienta WASM (Microsoft/ApplicationInsights-dotnet #2143)](https://github.com/microsoft/ApplicationInsights-dotnet/issues/2143) oraz [Web Analytics i Diagnostics (w tym linki do implementacji społeczności) (w #5461 dotnet/aspnetcore)](https://github.com/dotnet/aspnetcore/issues/5461).</span><span class="sxs-lookup"><span data-stu-id="92309-133">For more information, see [Support App Insights in Blazor WASM Client Side (microsoft/ApplicationInsights-dotnet #2143)](https://github.com/microsoft/ApplicationInsights-dotnet/issues/2143) and [Web analytics and diagnostics (includes links to community implementations) (dotnet/aspnetcore #5461)](https://github.com/dotnet/aspnetcore/issues/5461).</span></span> <span data-ttu-id="92309-134">W międzyczasie aplikacja po stronie klienta Blazor WebAssembly może używać [Application Insights zestawu SDK języka JavaScript](/azure/azure-monitor/app/javascript) z usługą [js Interop](xref:blazor/call-javascript-from-dotnet) do rejestrowania błędów bezpośrednio do Application Insights z aplikacji po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="92309-134">In the meantime, a client-side Blazor WebAssembly app can use the [Application Insights JavaScript SDK](/azure/azure-monitor/app/javascript) with [JS interop](xref:blazor/call-javascript-from-dotnet) to log errors directly to Application Insights from a client-side app.</span></span>

<span data-ttu-id="92309-135">&Dagger;Dotyczy aplikacji ASP.NET Core po stronie serwera, które są aplikacjami zaplecza interfejsu API sieci Web dla Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="92309-135">&Dagger;Applies to server-side ASP.NET Core apps that are web API backend apps for Blazor apps.</span></span> <span data-ttu-id="92309-136">Aplikacje po stronie klienta zalewki i wysyłają informacje o błędach do internetowego interfejsu API, który rejestruje informacje o błędzie dla dostawcy trwałego rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="92309-136">Client-side apps trap and send error information to a web API, which logs the error information to a persistent logging provider.</span></span>

## <a name="places-where-errors-may-occur"></a><span data-ttu-id="92309-137">Miejsca, w których mogą wystąpić błędy</span><span class="sxs-lookup"><span data-stu-id="92309-137">Places where errors may occur</span></span>

<span data-ttu-id="92309-138">Kod struktury i aplikacji może wyzwolić Nieobsłużone wyjątki w następujących lokalizacjach, które opisano w następujących sekcjach tego artykułu:</span><span class="sxs-lookup"><span data-stu-id="92309-138">Framework and app code may trigger unhandled exceptions in any of the following locations, which are described further in the following sections of this article:</span></span>

* [<span data-ttu-id="92309-139">Tworzenie wystąpienia składnika</span><span class="sxs-lookup"><span data-stu-id="92309-139">Component instantiation</span></span>](#component-instantiation-webassembly)
* [<span data-ttu-id="92309-140">Metody cyklu życia</span><span class="sxs-lookup"><span data-stu-id="92309-140">Lifecycle methods</span></span>](#lifecycle-methods-webassembly)
* [<span data-ttu-id="92309-141">Logika renderowania</span><span class="sxs-lookup"><span data-stu-id="92309-141">Rendering logic</span></span>](#rendering-logic-webassembly)
* [<span data-ttu-id="92309-142">Procedury obsługi zdarzeń</span><span class="sxs-lookup"><span data-stu-id="92309-142">Event handlers</span></span>](#event-handlers-webassembly)
* [<span data-ttu-id="92309-143">Usuwanie składników</span><span class="sxs-lookup"><span data-stu-id="92309-143">Component disposal</span></span>](#component-disposal-webassembly)
* [<span data-ttu-id="92309-144">Międzyoperacyjność w języku JavaScript</span><span class="sxs-lookup"><span data-stu-id="92309-144">JavaScript interop</span></span>](#javascript-interop-webassembly)

<h3 id="component-instantiation-webassembly"><span data-ttu-id="92309-145">Tworzenie wystąpienia składnika</span><span class="sxs-lookup"><span data-stu-id="92309-145">Component instantiation</span></span></h3>

<span data-ttu-id="92309-146">Podczas Blazor tworzenia wystąpienia składnika:</span><span class="sxs-lookup"><span data-stu-id="92309-146">When Blazor creates an instance of a component:</span></span>

* <span data-ttu-id="92309-147">Konstruktor składnika jest wywoływany.</span><span class="sxs-lookup"><span data-stu-id="92309-147">The component's constructor is invoked.</span></span>
* <span data-ttu-id="92309-148">Konstruktory wszelkich niepojedynczych usług DI dostarczonych do konstruktora składnika za pośrednictwem [`@inject`](xref:mvc/views/razor#inject) dyrektywy lub [ `[Inject]` atrybutu](xref:blazor/fundamentals/dependency-injection#request-a-service-in-a-component) są wywoływane.</span><span class="sxs-lookup"><span data-stu-id="92309-148">The constructors of any non-singleton DI services supplied to the component's constructor via the [`@inject`](xref:mvc/views/razor#inject) directive or the [`[Inject]` attribute](xref:blazor/fundamentals/dependency-injection#request-a-service-in-a-component) are invoked.</span></span>

<span data-ttu-id="92309-149">Błąd w wykonanym konstruktorze lub Metoda ustawiająca dla dowolnej `[Inject]` właściwości powoduje nieobsłużony wyjątek i uniemożliwia utworzenie wystąpienia składnika przez strukturę.</span><span class="sxs-lookup"><span data-stu-id="92309-149">An error in an executed constructor or a setter for any `[Inject]` property results in an unhandled exception and stops the framework from instantiating the component.</span></span> <span data-ttu-id="92309-150">Jeśli logika konstruktora może generować wyjątki, aplikacja powinna zalewkować wyjątki przy użyciu [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instrukcji z obsługą błędów i rejestrowaniem.</span><span class="sxs-lookup"><span data-stu-id="92309-150">If constructor logic may throw exceptions, the app should trap the exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<h3 id="lifecycle-methods-webassembly"><span data-ttu-id="92309-151">Metody cyklu życia</span><span class="sxs-lookup"><span data-stu-id="92309-151">Lifecycle methods</span></span></h3>

<span data-ttu-id="92309-152">W okresie istnienia składnika Blazor wywołuje [metody cyklu życia](xref:blazor/components/lifecycle#lifecycle-methods).</span><span class="sxs-lookup"><span data-stu-id="92309-152">During the lifetime of a component, Blazor invokes [lifecycle methods](xref:blazor/components/lifecycle#lifecycle-methods).</span></span> <span data-ttu-id="92309-153">Aby składniki zajmowały błędy w metodach cyklu życia, Dodaj logikę obsługi błędów.</span><span class="sxs-lookup"><span data-stu-id="92309-153">For components to deal with errors in lifecycle methods, add error handling logic.</span></span>

<span data-ttu-id="92309-154">W poniższym przykładzie, gdzie <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> wywołuje metodę w celu uzyskania produktu:</span><span class="sxs-lookup"><span data-stu-id="92309-154">In the following example where <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> calls a method to obtain a product:</span></span>

* <span data-ttu-id="92309-155">Wyjątek zgłoszony w `ProductRepository.GetProductByIdAsync` metodzie jest obsługiwany przez [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instrukcję.</span><span class="sxs-lookup"><span data-stu-id="92309-155">An exception thrown in the `ProductRepository.GetProductByIdAsync` method is handled by a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement.</span></span>
* <span data-ttu-id="92309-156">Gdy `catch` blok jest wykonywany:</span><span class="sxs-lookup"><span data-stu-id="92309-156">When the `catch` block is executed:</span></span>
  * <span data-ttu-id="92309-157">`loadFailed` jest ustawiona na `true` , która jest używana do wyświetlania komunikatu o błędzie dla użytkownika.</span><span class="sxs-lookup"><span data-stu-id="92309-157">`loadFailed` is set to `true`, which is used to display an error message to the user.</span></span>
  * <span data-ttu-id="92309-158">Błąd jest rejestrowany.</span><span class="sxs-lookup"><span data-stu-id="92309-158">The error is logged.</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/handle-errors/ProductDetails.razor?name=snippet&highlight=11,27-39)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/handle-errors/ProductDetails.razor?name=snippet&highlight=11,27-39)]

::: moniker-end

<h3 id="rendering-logic-webassembly"><span data-ttu-id="92309-159">Logika renderowania</span><span class="sxs-lookup"><span data-stu-id="92309-159">Rendering logic</span></span></h3>

<span data-ttu-id="92309-160">Znaczniki deklaratywne w Razor pliku składnika ( `.razor` ) są kompilowane do metody języka C# o nazwie <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> .</span><span class="sxs-lookup"><span data-stu-id="92309-160">The declarative markup in a Razor component file (`.razor`) is compiled into a C# method called <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A>.</span></span> <span data-ttu-id="92309-161">Gdy składnik renderuje, <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> wykonuje i tworzy strukturę danych opisującą elementy, tekst i składniki podrzędne renderowanego składnika.</span><span class="sxs-lookup"><span data-stu-id="92309-161">When a component renders, <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> executes and builds up a data structure describing the elements, text, and child components of the rendered component.</span></span>

<span data-ttu-id="92309-162">Logika renderowania może zgłosić wyjątek.</span><span class="sxs-lookup"><span data-stu-id="92309-162">Rendering logic can throw an exception.</span></span> <span data-ttu-id="92309-163">Przykład tego scenariusza występuje, gdy `@someObject.PropertyName` jest oceniane, ale `@someObject` jest `null` .</span><span class="sxs-lookup"><span data-stu-id="92309-163">An example of this scenario occurs when `@someObject.PropertyName` is evaluated but `@someObject` is `null`.</span></span>

<span data-ttu-id="92309-164">Aby uniknąć <xref:System.NullReferenceException> renderowania logiki, `null` przed uzyskaniem dostępu do elementów członkowskich Sprawdź, czy jest on obiektem.</span><span class="sxs-lookup"><span data-stu-id="92309-164">To prevent a <xref:System.NullReferenceException> in rendering logic, check for a `null` object before accessing its members.</span></span> <span data-ttu-id="92309-165">W poniższym przykładzie `person.Address` właściwości nie są dostępne, jeśli `person.Address` `null` :</span><span class="sxs-lookup"><span data-stu-id="92309-165">In the following example, `person.Address` properties aren't accessed if `person.Address` is `null`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/handle-errors/PersonExample.razor?name=snippet&highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/handle-errors/PersonExample.razor?name=snippet&highlight=1)]

::: moniker-end

<span data-ttu-id="92309-166">Poprzedni kod założono, że `person` nie jest `null` .</span><span class="sxs-lookup"><span data-stu-id="92309-166">The preceding code assumes that `person` isn't `null`.</span></span> <span data-ttu-id="92309-167">Często Struktura kodu gwarantuje, że obiekt istnieje w momencie renderowania składnika.</span><span class="sxs-lookup"><span data-stu-id="92309-167">Often, the structure of the code guarantees that an object exists at the time the component is rendered.</span></span> <span data-ttu-id="92309-168">W takich przypadkach nie jest konieczne sprawdzanie `null` logiki renderowania.</span><span class="sxs-lookup"><span data-stu-id="92309-168">In those cases, it isn't necessary to check for `null` in rendering logic.</span></span> <span data-ttu-id="92309-169">W poprzednim przykładzie `person` można zagwarantować, że istnieje, ponieważ `person` jest tworzony podczas tworzenia wystąpienia składnika, jak pokazano na poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="92309-169">In the prior example, `person` might be guaranteed to exist because `person` is created when the component is instantiated, as the following example shows:</span></span>

```razor
@code {
    private Person person = new Person();

    ...
}
```

<h3 id="event-handlers-webassembly"><span data-ttu-id="92309-170">Procedury obsługi zdarzeń</span><span class="sxs-lookup"><span data-stu-id="92309-170">Event handlers</span></span></h3>

<span data-ttu-id="92309-171">Kod po stronie klienta wyzwala wywołania kodu C#, gdy programy obsługi zdarzeń są tworzone przy użyciu:</span><span class="sxs-lookup"><span data-stu-id="92309-171">Client-side code triggers invocations of C# code when event handlers are created using:</span></span>

* `@onclick`
* `@onchange`
* <span data-ttu-id="92309-172">Inne `@on...` atrybuty</span><span class="sxs-lookup"><span data-stu-id="92309-172">Other `@on...` attributes</span></span>
* `@bind`

<span data-ttu-id="92309-173">Kod procedury obsługi zdarzeń może zgłosić nieobsługiwany wyjątek w tych scenariuszach.</span><span class="sxs-lookup"><span data-stu-id="92309-173">Event handler code might throw an unhandled exception in these scenarios.</span></span>

<span data-ttu-id="92309-174">Jeśli aplikacja wywołuje kod, który może zakończyć się niepowodzeniem z powodów zewnętrznych, należy zastosować wyjątek pułapki przy użyciu [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instrukcji z obsługą błędów i rejestrowaniem.</span><span class="sxs-lookup"><span data-stu-id="92309-174">If the app calls code that could fail for external reasons, trap exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="92309-175">Jeśli kod użytkownika nie pułapki i obsłuży wyjątek, struktura rejestruje wyjątek.</span><span class="sxs-lookup"><span data-stu-id="92309-175">If user code doesn't trap and handle the exception, the framework logs the exception.</span></span>

<h3 id="component-disposal-webassembly"><span data-ttu-id="92309-176">Usuwanie składników</span><span class="sxs-lookup"><span data-stu-id="92309-176">Component disposal</span></span></h3>

<span data-ttu-id="92309-177">Składnik może zostać usunięty z interfejsu użytkownika, na przykład, ponieważ użytkownik przeszedł do innej strony.</span><span class="sxs-lookup"><span data-stu-id="92309-177">A component may be removed from the UI, for example, because the user has navigated to another page.</span></span> <span data-ttu-id="92309-178">Gdy składnik implementujący <xref:System.IDisposable?displayProperty=fullName> jest usuwany z interfejsu użytkownika, struktura wywołuje <xref:System.IDisposable.Dispose%2A> metodę składnika.</span><span class="sxs-lookup"><span data-stu-id="92309-178">When a component that implements <xref:System.IDisposable?displayProperty=fullName> is removed from the UI, the framework calls the component's <xref:System.IDisposable.Dispose%2A> method.</span></span>

<span data-ttu-id="92309-179">Jeśli logika usuwania może generować wyjątki, aplikacja powinna zalewkować wyjątki przy użyciu [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instrukcji z obsługą błędów i rejestrowaniem.</span><span class="sxs-lookup"><span data-stu-id="92309-179">If disposal logic may throw exceptions, the app should trap the exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="92309-180">Aby uzyskać więcej informacji na temat usuwania składników, zobacz <xref:blazor/components/lifecycle#component-disposal-with-idisposable> .</span><span class="sxs-lookup"><span data-stu-id="92309-180">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

<h3 id="javascript-interop-webassembly"><span data-ttu-id="92309-181">Międzyoperacyjność w języku JavaScript</span><span class="sxs-lookup"><span data-stu-id="92309-181">JavaScript interop</span></span></h3>

<span data-ttu-id="92309-182"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> umożliwia programowi .NET Code wykonywanie wywołań asynchronicznych do środowiska uruchomieniowego JavaScript w przeglądarce użytkownika.</span><span class="sxs-lookup"><span data-stu-id="92309-182"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> allows .NET code to make asynchronous calls to the JavaScript runtime in the user's browser.</span></span>

<span data-ttu-id="92309-183">Poniższe warunki dotyczą obsługi błędów w programie <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> :</span><span class="sxs-lookup"><span data-stu-id="92309-183">The following conditions apply to error handling with <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>:</span></span>

* <span data-ttu-id="92309-184">Jeśli wywołanie <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> synchronicznie zakończy się niepowodzeniem, wystąpi wyjątek programu .NET.</span><span class="sxs-lookup"><span data-stu-id="92309-184">If a call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> fails synchronously, a .NET exception occurs.</span></span> <span data-ttu-id="92309-185">Wywołanie <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> może zakończyć się niepowodzeniem, na przykład dlatego, że nie można serializować dostarczonych argumentów.</span><span class="sxs-lookup"><span data-stu-id="92309-185">A call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> may fail, for example, because the supplied arguments can't be serialized.</span></span> <span data-ttu-id="92309-186">Kod dewelopera musi przechwycić wyjątek.</span><span class="sxs-lookup"><span data-stu-id="92309-186">Developer code must catch the exception.</span></span>
* <span data-ttu-id="92309-187">Jeśli wywołanie <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> powiedzie się asynchronicznie, .NET <xref:System.Threading.Tasks.Task> kończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="92309-187">If a call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> fails asynchronously, the .NET <xref:System.Threading.Tasks.Task> fails.</span></span> <span data-ttu-id="92309-188">Wywołanie <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> może zakończyć się niepowodzeniem, na przykład ponieważ kod po stronie JavaScript zgłasza wyjątek lub zwraca `Promise` , który został ukończony jako `rejected` .</span><span class="sxs-lookup"><span data-stu-id="92309-188">A call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> may fail, for example, because the JavaScript-side code throws an exception or returns a `Promise` that completed as `rejected`.</span></span> <span data-ttu-id="92309-189">Kod dewelopera musi przechwycić wyjątek.</span><span class="sxs-lookup"><span data-stu-id="92309-189">Developer code must catch the exception.</span></span> <span data-ttu-id="92309-190">W przypadku użycia [`await`](/dotnet/csharp/language-reference/keywords/await) operatora Rozważ zapakowanie wywołania metody w [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instrukcji z obsługą błędów i rejestrowaniem.</span><span class="sxs-lookup"><span data-stu-id="92309-190">If using the [`await`](/dotnet/csharp/language-reference/keywords/await) operator, consider wrapping the method call in a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>
* <span data-ttu-id="92309-191">Domyślnie wywołania programu <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> muszą zakończyć się w określonym przedziale czasu lub w przeciwnym razie upłynął limit czasu połączenia. Domyślny limit czasu wynosi jedną minutę.</span><span class="sxs-lookup"><span data-stu-id="92309-191">By default, calls to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> must complete within a certain period or else the call times out. The default timeout period is one minute.</span></span> <span data-ttu-id="92309-192">Limit czasu chroni kod przed utratą połączenia sieciowego lub kodem JavaScript, który nigdy nie odsyła komunikat uzupełniający.</span><span class="sxs-lookup"><span data-stu-id="92309-192">The timeout protects the code against a loss in network connectivity or JavaScript code that never sends back a completion message.</span></span> <span data-ttu-id="92309-193">Jeśli wystąpiło przełączenie, wynikiem <xref:System.Threading.Tasks> kończy się niepowodzeniem a <xref:System.OperationCanceledException> .</span><span class="sxs-lookup"><span data-stu-id="92309-193">If the call times out, the resulting <xref:System.Threading.Tasks> fails with an <xref:System.OperationCanceledException>.</span></span> <span data-ttu-id="92309-194">Zalewka i przetwórz wyjątek z rejestrowaniem.</span><span class="sxs-lookup"><span data-stu-id="92309-194">Trap and process the exception with logging.</span></span>

<span data-ttu-id="92309-195">Podobnie kod JavaScript może inicjować wywołania metod .NET wskazywanych przez [ `[JSInvokable]` atrybut](xref:blazor/call-dotnet-from-javascript).</span><span class="sxs-lookup"><span data-stu-id="92309-195">Similarly, JavaScript code may initiate calls to .NET methods indicated by the [`[JSInvokable]` attribute](xref:blazor/call-dotnet-from-javascript).</span></span> <span data-ttu-id="92309-196">Jeśli te metody .NET zgłaszają nieobsłużony wyjątek, po stronie JavaScript `Promise` zostanie odrzucona.</span><span class="sxs-lookup"><span data-stu-id="92309-196">If these .NET methods throw an unhandled exception, the JavaScript-side `Promise` is rejected.</span></span>

<span data-ttu-id="92309-197">Istnieje możliwość użycia kodu obsługi błędów po stronie .NET lub stronie JavaScript wywołania metody.</span><span class="sxs-lookup"><span data-stu-id="92309-197">You have the option of using error handling code on either the .NET side or the JavaScript side of the method call.</span></span>

<span data-ttu-id="92309-198">Aby uzyskać więcej informacji, zobacz następujące artykuły:</span><span class="sxs-lookup"><span data-stu-id="92309-198">For more information, see the following articles:</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

## <a name="advanced-scenarios"></a><span data-ttu-id="92309-199">Scenariusze zaawansowane</span><span class="sxs-lookup"><span data-stu-id="92309-199">Advanced scenarios</span></span>

### <a name="recursive-rendering"></a><span data-ttu-id="92309-200">Renderowanie cykliczne</span><span class="sxs-lookup"><span data-stu-id="92309-200">Recursive rendering</span></span>

<span data-ttu-id="92309-201">Składniki można cyklicznie zagnieżdżać.</span><span class="sxs-lookup"><span data-stu-id="92309-201">Components can be nested recursively.</span></span> <span data-ttu-id="92309-202">Jest to przydatne do reprezentowania struktur danych rekursywnych.</span><span class="sxs-lookup"><span data-stu-id="92309-202">This is useful for representing recursive data structures.</span></span> <span data-ttu-id="92309-203">Na przykład `TreeNode` składnik może renderować więcej `TreeNode` składników dla każdego węzła podrzędnego.</span><span class="sxs-lookup"><span data-stu-id="92309-203">For example, a `TreeNode` component can render more `TreeNode` components for each of the node's children.</span></span>

<span data-ttu-id="92309-204">W przypadku renderowania cyklicznego należy unikać tworzenia wzorców, które powodują nieskończoną rekursję:</span><span class="sxs-lookup"><span data-stu-id="92309-204">When rendering recursively, avoid coding patterns that result in infinite recursion:</span></span>

* <span data-ttu-id="92309-205">Nie Renderuj rekursywnie struktury danych zawierającej cykl.</span><span class="sxs-lookup"><span data-stu-id="92309-205">Don't recursively render a data structure that contains a cycle.</span></span> <span data-ttu-id="92309-206">Na przykład nie Renderuj węzła drzewa, którego elementy podrzędne zawierają sam siebie.</span><span class="sxs-lookup"><span data-stu-id="92309-206">For example, don't render a tree node whose children includes itself.</span></span>
* <span data-ttu-id="92309-207">Nie twórz łańcucha układów zawierających cykl.</span><span class="sxs-lookup"><span data-stu-id="92309-207">Don't create a chain of layouts that contain a cycle.</span></span> <span data-ttu-id="92309-208">Na przykład nie twórz układu, którego układ jest sam.</span><span class="sxs-lookup"><span data-stu-id="92309-208">For example, don't create a layout whose layout is itself.</span></span>
* <span data-ttu-id="92309-209">Nie Zezwalaj użytkownikowi końcowemu na naruszenie nieodmian rekursji (reguł) przy użyciu złośliwego wpisu danych lub wywołań międzyoperacyjnych języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="92309-209">Don't allow an end user to violate recursion invariants (rules) through malicious data entry or JavaScript interop calls.</span></span>

<span data-ttu-id="92309-210">Nieskończone pętle podczas renderowania:</span><span class="sxs-lookup"><span data-stu-id="92309-210">Infinite loops during rendering:</span></span>

* <span data-ttu-id="92309-211">Powoduje, że proces renderowania kontynuuje działanie zawsze.</span><span class="sxs-lookup"><span data-stu-id="92309-211">Causes the rendering process to continue forever.</span></span>
* <span data-ttu-id="92309-212">Jest równoznaczny z tworzeniem niezakończonej pętli.</span><span class="sxs-lookup"><span data-stu-id="92309-212">Is equivalent to creating an unterminated loop.</span></span>

<span data-ttu-id="92309-213">W tych scenariuszach wątek zwykle próbuje wykonać:</span><span class="sxs-lookup"><span data-stu-id="92309-213">In these scenarios, the thread usually attempts to:</span></span>

* <span data-ttu-id="92309-214">Zużywaj ilość czasu procesora CPU dozwoloną przez system operacyjny w nieskończoność.</span><span class="sxs-lookup"><span data-stu-id="92309-214">Consume as much CPU time as permitted by the operating system, indefinitely.</span></span>
* <span data-ttu-id="92309-215">Korzystaj z nieograniczonej ilości pamięci klienta.</span><span class="sxs-lookup"><span data-stu-id="92309-215">Consume an unlimited amount of client memory.</span></span> <span data-ttu-id="92309-216">Zużywanie nieograniczonej pamięci jest równoważne scenariuszowi, w którym niezakończona pętla dodaje wpisy do kolekcji na każdej iteracji.</span><span class="sxs-lookup"><span data-stu-id="92309-216">Consuming unlimited memory is equivalent to the scenario where an unterminated loop adds entries to a collection on every iteration.</span></span>

<span data-ttu-id="92309-217">Aby uniknąć nieskończonych wzorców rekursji, należy się upewnić, że kod renderowania cyklicznego zawiera odpowiednie warunki zatrzymania.</span><span class="sxs-lookup"><span data-stu-id="92309-217">To avoid infinite recursion patterns, ensure that recursive rendering code contains suitable stopping conditions.</span></span>

### <a name="custom-render-tree-logic"></a><span data-ttu-id="92309-218">Logika drzewa renderowania niestandardowego</span><span class="sxs-lookup"><span data-stu-id="92309-218">Custom render tree logic</span></span>

<span data-ttu-id="92309-219">Większość Blazor składników jest implementowana jako Razor pliki składników ( `.razor` ) i są kompilowane przez platformę w celu utworzenia logiki, która działa w <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> celu renderowania danych wyjściowych.</span><span class="sxs-lookup"><span data-stu-id="92309-219">Most Blazor components are implemented as Razor component files (`.razor`) and are compiled by the framework to produce logic that operates on a <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> to render their output.</span></span> <span data-ttu-id="92309-220">Jednak deweloper może ręcznie zaimplementować <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logikę przy użyciu procedury kodu w języku C#.</span><span class="sxs-lookup"><span data-stu-id="92309-220">However, a developer may manually implement <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic using procedural C# code.</span></span> <span data-ttu-id="92309-221">Aby uzyskać więcej informacji, zobacz <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span><span class="sxs-lookup"><span data-stu-id="92309-221">For more information, see <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span></span>

> [!WARNING]
> <span data-ttu-id="92309-222">Korzystanie z logiki konstruktora drzewa renderowania ręcznego jest uznawane za zaawansowane i niebezpieczne scenariusze, które nie są zalecane do ogólnego tworzenia składników.</span><span class="sxs-lookup"><span data-stu-id="92309-222">Use of manual render tree builder logic is considered an advanced and unsafe scenario, not recommended for general component development.</span></span>

<span data-ttu-id="92309-223">Jeśli <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> kod zostanie zapisany, Deweloper musi zagwarantować poprawność kodu.</span><span class="sxs-lookup"><span data-stu-id="92309-223">If <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> code is written, the developer must guarantee the correctness of the code.</span></span> <span data-ttu-id="92309-224">Na przykład Deweloper musi upewnić się, że:</span><span class="sxs-lookup"><span data-stu-id="92309-224">For example, the developer must ensure that:</span></span>

* <span data-ttu-id="92309-225">Wywołania <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> i <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A> są prawidłowo zrównoważone.</span><span class="sxs-lookup"><span data-stu-id="92309-225">Calls to <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> and <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A> are correctly balanced.</span></span>
* <span data-ttu-id="92309-226">Atrybuty są dodawane tylko w prawidłowych miejscach.</span><span class="sxs-lookup"><span data-stu-id="92309-226">Attributes are only added in the correct places.</span></span>

<span data-ttu-id="92309-227">Nieprawidłowa ręczna logika konstruktora drzewa renderowania może spowodować dowolne niezdefiniowane zachowanie, w tym awarie, zawieszenie aplikacji i luki w zabezpieczeniach.</span><span class="sxs-lookup"><span data-stu-id="92309-227">Incorrect manual render tree builder logic can cause arbitrary undefined behavior, including crashes, app hangs, and security vulnerabilities.</span></span>

<span data-ttu-id="92309-228">Rozważ ręczne renderowanie logiki konstruktora drzew drzewa na tym samym poziomie złożoności i z tym samym poziomem *zagrożenia* co ręczne pisanie kodu zestawu lub [języka pośredniego firmy Microsoft (MSIL)](/dotnet/standard/managed-code) .</span><span class="sxs-lookup"><span data-stu-id="92309-228">Consider manual render tree builder logic on the same level of complexity and with the same level of *danger* as writing assembly code or [Microsoft Intermediate Language (MSIL)](/dotnet/standard/managed-code) instructions by hand.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="92309-229">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="92309-229">Additional resources</span></span>

* <xref:blazor/fundamentals/logging>
* <xref:fundamentals/error-handling>&dagger;
* <xref:web-api/index>

<span data-ttu-id="92309-230">&dagger;Dotyczy aplikacji interfejsu API sieci Web ASP.NET Core zaplecza, które są Blazor WebAssembly używane przez aplikacje po stronie klienta do rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="92309-230">&dagger;Applies to backend ASP.NET Core web API apps that client-side Blazor WebAssembly apps use for logging.</span></span>

::: zone-end

::: zone pivot="server"

## <a name="detailed-errors-during-development"></a><span data-ttu-id="92309-231">Szczegóły błędów podczas opracowywania</span><span class="sxs-lookup"><span data-stu-id="92309-231">Detailed errors during development</span></span>

<span data-ttu-id="92309-232">Gdy Blazor aplikacja nie działa prawidłowo podczas opracowywania, otrzymuje szczegółowe informacje o błędzie z aplikacji, które pomagają w rozwiązywaniu problemów i rozwiązaniu problemu.</span><span class="sxs-lookup"><span data-stu-id="92309-232">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="92309-233">Gdy wystąpi błąd, Blazor w dolnej części ekranu aplikacje są wyświetlane żółte paski:</span><span class="sxs-lookup"><span data-stu-id="92309-233">When an error occurs, Blazor apps display a light yellow bar at the bottom of the screen:</span></span>

* <span data-ttu-id="92309-234">Podczas tworzenia pasek kieruje użytkownika do konsoli przeglądarki, gdzie można zobaczyć wyjątek.</span><span class="sxs-lookup"><span data-stu-id="92309-234">During development, the bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="92309-235">W środowisku produkcyjnym pasek powiadamia użytkownika o wystąpieniu błędu i zaleca odświeżenie przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="92309-235">In production, the bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="92309-236">Interfejs użytkownika dla tego środowiska obsługi błędów jest częścią Blazor szablonów projektu.</span><span class="sxs-lookup"><span data-stu-id="92309-236">The UI for this error handling experience is part of the Blazor project templates.</span></span>

<span data-ttu-id="92309-237">W Blazor Server aplikacji Dostosuj środowisko w `Pages/_Host.cshtml` pliku:</span><span class="sxs-lookup"><span data-stu-id="92309-237">In a Blazor Server app, customize the experience in the `Pages/_Host.cshtml` file:</span></span>

```cshtml
<div id="blazor-error-ui">
    <environment include="Staging,Production">
        An error has occurred. This application may no longer respond until reloaded.
    </environment>
    <environment include="Development">
        An unhandled exception has occurred. See browser dev tools for details.
    </environment>
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="92309-238">`blazor-error-ui`Element jest zwykle ukryty z powodu obecności `display: none` stylu `blazor-error-ui` klasy CSS w arkuszu stylów witryny ( `wwwroot/css/site.css` ).</span><span class="sxs-lookup"><span data-stu-id="92309-238">The `blazor-error-ui` element is normally hidden due the presence of the `display: none` style of the `blazor-error-ui` CSS class in the site's stylesheet (`wwwroot/css/site.css`).</span></span> <span data-ttu-id="92309-239">Gdy wystąpi błąd, struktura ma zastosowanie `display: block` do elementu.</span><span class="sxs-lookup"><span data-stu-id="92309-239">When an error occurs, the framework applies `display: block` to the element.</span></span>

## <a name="blazor-server-detailed-circuit-errors"></a><span data-ttu-id="92309-240">Blazor Server Szczegóły błędów obwodu</span><span class="sxs-lookup"><span data-stu-id="92309-240">Blazor Server detailed circuit errors</span></span>

<span data-ttu-id="92309-241">Błędy po stronie klienta nie obejmują stosu wywołań i nie zawierają szczegółów przyczyny błędu, ale Dzienniki serwera zawierają takie informacje.</span><span class="sxs-lookup"><span data-stu-id="92309-241">Client-side errors don't include the call stack and don't provide detail on the cause of the error, but server logs do contain such information.</span></span> <span data-ttu-id="92309-242">W celach programistycznych informacje o błędzie obwodu poufnego mogą być udostępniane klientowi, włączając szczegółowe błędy.</span><span class="sxs-lookup"><span data-stu-id="92309-242">For development purposes, sensitive circuit error information can be made available to the client by enabling detailed errors.</span></span>

<span data-ttu-id="92309-243">Ustaw <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors?displayProperty=nameWithType> wartość `true` .</span><span class="sxs-lookup"><span data-stu-id="92309-243">Set <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors?displayProperty=nameWithType> to `true`.</span></span> <span data-ttu-id="92309-244">Aby uzyskać więcej informacji i zapoznać się z przykładem, zobacz <xref:blazor/fundamentals/signalr#circuit-handler-options> .</span><span class="sxs-lookup"><span data-stu-id="92309-244">For more information and an example, see <xref:blazor/fundamentals/signalr#circuit-handler-options>.</span></span>

<span data-ttu-id="92309-245">Alternatywą dla ustawienia <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors?displayProperty=nameWithType> jest ustawienie `DetailedErrors` klucza konfiguracji `true` w pliku ustawień środowiska deweloperskiego aplikacji ( `appsettings.Development.json` ).</span><span class="sxs-lookup"><span data-stu-id="92309-245">An alternative to setting <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors?displayProperty=nameWithType> is to set the `DetailedErrors` configuration key to `true` in the app's Development environment settings file (`appsettings.Development.json`).</span></span>  <span data-ttu-id="92309-246">Ponadto należy ustawić [ SignalR funkcję rejestrowania po stronie serwera](xref:signalr/diagnostics#server-side-logging) ( `Microsoft.AspNetCore.SignalR` ) w celu [debugowania](xref:Microsoft.Extensions.Logging.LogLevel) lub [śledzenia](xref:Microsoft.Extensions.Logging.LogLevel) szczegółowego SignalR rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="92309-246">Additionally, set [SignalR server-side logging](xref:signalr/diagnostics#server-side-logging) (`Microsoft.AspNetCore.SignalR`) to [Debug](xref:Microsoft.Extensions.Logging.LogLevel) or [Trace](xref:Microsoft.Extensions.Logging.LogLevel) for detailed SignalR logging.</span></span>

<span data-ttu-id="92309-247">`appsettings.Development.json`:</span><span class="sxs-lookup"><span data-stu-id="92309-247">`appsettings.Development.json`:</span></span>

```json
{
  "DetailedErrors": true,
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information",
      "Microsoft.AspNetCore.SignalR": "Debug"
    }
  }
}
```

<span data-ttu-id="92309-248"><xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors>Klucz konfiguracji można również ustawić `true` przy użyciu `ASPNETCORE_DETAILEDERRORS` zmiennej środowiskowej o wartości `true` na serwerach środowiska deweloperskiego/przejściowego lub w systemie lokalnym.</span><span class="sxs-lookup"><span data-stu-id="92309-248">The <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors> configuration key can also be set to `true` using the `ASPNETCORE_DETAILEDERRORS` environment variable with a value of `true` on Development/Staging environment servers or on your local system.</span></span>

> [!WARNING]
> <span data-ttu-id="92309-249">Zawsze należy unikać ujawniania informacji o błędach klientom w Internecie, co stanowi zagrożenie bezpieczeństwa.</span><span class="sxs-lookup"><span data-stu-id="92309-249">Always avoid exposing error information to clients on the Internet, which is a security risk.</span></span>

## <a name="how-a-blazor-server-app-reacts-to-unhandled-exceptions"></a><span data-ttu-id="92309-250">Jak Blazor Server Aplikacja reaguje na Nieobsłużone wyjątki</span><span class="sxs-lookup"><span data-stu-id="92309-250">How a Blazor Server app reacts to unhandled exceptions</span></span>

<span data-ttu-id="92309-251">Blazor Server jest strukturą stanową.</span><span class="sxs-lookup"><span data-stu-id="92309-251">Blazor Server is a stateful framework.</span></span> <span data-ttu-id="92309-252">Gdy użytkownicy współpracują z aplikacją, utrzymują połączenie z serwerem znanym jako *obwód*.</span><span class="sxs-lookup"><span data-stu-id="92309-252">While users interact with an app, they maintain a connection to the server known as a *circuit*.</span></span> <span data-ttu-id="92309-253">Obwód zawiera aktywne wystąpienia składnika, a także wiele innych aspektów stanu, takich jak:</span><span class="sxs-lookup"><span data-stu-id="92309-253">The circuit holds active component instances, plus many other aspects of state, such as:</span></span>

* <span data-ttu-id="92309-254">Najnowsze renderowane dane wyjściowe składników.</span><span class="sxs-lookup"><span data-stu-id="92309-254">The most recent rendered output of components.</span></span>
* <span data-ttu-id="92309-255">Bieżący zestaw delegatów obsługi zdarzeń, które mogą być wyzwalane przez zdarzenia po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="92309-255">The current set of event-handling delegates that could be triggered by client-side events.</span></span>

<span data-ttu-id="92309-256">Jeśli użytkownik otworzy aplikację na wielu kartach przeglądarki, użytkownik utworzy wiele niezależnych obwody.</span><span class="sxs-lookup"><span data-stu-id="92309-256">If a user opens the app in multiple browser tabs, the user creates multiple independent circuits.</span></span>

<span data-ttu-id="92309-257">Blazor traktuje większość nieobsłużonych wyjątków jako krytyczne dla obwodu, w którym występują.</span><span class="sxs-lookup"><span data-stu-id="92309-257">Blazor treats most unhandled exceptions as fatal to the circuit where they occur.</span></span> <span data-ttu-id="92309-258">Jeśli obwód zostanie przerwany z powodu nieobsługiwanego wyjątku, użytkownik może nadal korzystać z aplikacji tylko przez ponowne załadowanie strony w celu utworzenia nowego obwodu.</span><span class="sxs-lookup"><span data-stu-id="92309-258">If a circuit is terminated due to an unhandled exception, the user can only continue to interact with the app by reloading the page to create a new circuit.</span></span> <span data-ttu-id="92309-259">Nie ma to wpływu na obwody, które zostały przerwane, które są obwody dla innych użytkowników lub kart przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="92309-259">Circuits outside of the one that's terminated, which are circuits for other users or other browser tabs, aren't affected.</span></span> <span data-ttu-id="92309-260">Ten scenariusz jest podobny do aplikacji klasycznej, która ulega awarii.</span><span class="sxs-lookup"><span data-stu-id="92309-260">This scenario is similar to a desktop app that crashes.</span></span> <span data-ttu-id="92309-261">Aplikacja, która uległa awarii, musi zostać ponownie uruchomiona, ale nie ma to wpływu na inne aplikacje.</span><span class="sxs-lookup"><span data-stu-id="92309-261">The crashed app must be restarted, but other apps aren't affected.</span></span>

<span data-ttu-id="92309-262">Struktura kończy obwód, gdy wystąpi nieobsługiwany wyjątek z następujących powodów:</span><span class="sxs-lookup"><span data-stu-id="92309-262">The framework terminates a circuit when an unhandled exception occurs for the following reasons:</span></span>

* <span data-ttu-id="92309-263">Nieobsługiwany wyjątek często pozostawia obwód w niezdefiniowanym stanie.</span><span class="sxs-lookup"><span data-stu-id="92309-263">An unhandled exception often leaves the circuit in an undefined state.</span></span>
* <span data-ttu-id="92309-264">Nie można zagwarantować normalnej operacji aplikacji po wystąpieniu nieobsłużonego wyjątku.</span><span class="sxs-lookup"><span data-stu-id="92309-264">The app's normal operation can't be guaranteed after an unhandled exception.</span></span>
* <span data-ttu-id="92309-265">Luki w zabezpieczeniach mogą pojawić się w aplikacji, jeśli obwód będzie kontynuował działanie w niezdefiniowanym stanie.</span><span class="sxs-lookup"><span data-stu-id="92309-265">Security vulnerabilities may appear in the app if the circuit continues in an undefined state.</span></span>

## <a name="manage-unhandled-exceptions-in-developer-code"></a><span data-ttu-id="92309-266">Zarządzanie nieobsługiwanymi wyjątkami w kodzie dewelopera</span><span class="sxs-lookup"><span data-stu-id="92309-266">Manage unhandled exceptions in developer code</span></span>

<span data-ttu-id="92309-267">Aby aplikacja kontynuowała działanie po wystąpieniu błędu, aplikacja musi mieć logikę obsługi błędów.</span><span class="sxs-lookup"><span data-stu-id="92309-267">For an app to continue after an error, the app must have error handling logic.</span></span> <span data-ttu-id="92309-268">W dalszej części tego artykułu opisano potencjalne źródła nieobsłużonych wyjątków.</span><span class="sxs-lookup"><span data-stu-id="92309-268">Later sections of this article describe potential sources of unhandled exceptions.</span></span>

<span data-ttu-id="92309-269">W środowisku produkcyjnym nie Renderuj komunikatów wyjątków struktury ani śladów stosu w interfejsie użytkownika.</span><span class="sxs-lookup"><span data-stu-id="92309-269">In production, don't render framework exception messages or stack traces in the UI.</span></span> <span data-ttu-id="92309-270">Renderowanie komunikatów o wyjątkach lub śladów stosu może:</span><span class="sxs-lookup"><span data-stu-id="92309-270">Rendering exception messages or stack traces could:</span></span>

* <span data-ttu-id="92309-271">Ujawnianie poufnych informacji użytkownikom końcowym.</span><span class="sxs-lookup"><span data-stu-id="92309-271">Disclose sensitive information to end users.</span></span>
* <span data-ttu-id="92309-272">Pomóż złośliwemu użytkownikowi wykrywać słabe strony w aplikacji, która może naruszyć bezpieczeństwo aplikacji, serwera lub sieci.</span><span class="sxs-lookup"><span data-stu-id="92309-272">Help a malicious user discover weaknesses in an app that can compromise the security of the app, server, or network.</span></span>

## <a name="global-exception-handling"></a><span data-ttu-id="92309-273">Globalna obsługa wyjątków</span><span class="sxs-lookup"><span data-stu-id="92309-273">Global exception handling</span></span>

[!INCLUDE[](~/blazor/includes/handle-errors/global-exception-handling.md)]

<span data-ttu-id="92309-274">Ponieważ metody przedstawione w tej sekcji obsługują błędy w [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instrukcji, SignalR połączenie między klientem i serwerem nie jest zerwane w przypadku wystąpienia błędu, a obwód pozostaje aktywny.</span><span class="sxs-lookup"><span data-stu-id="92309-274">Because the approaches in this section handle errors with a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement, the SignalR connection between the client and server isn't broken when an error occurs and the circuit remains alive.</span></span> <span data-ttu-id="92309-275">Dowolny nieobsługiwany wyjątek jest krytyczny dla obwodu.</span><span class="sxs-lookup"><span data-stu-id="92309-275">Any unhandled exception is fatal to a circuit.</span></span> <span data-ttu-id="92309-276">Aby uzyskać więcej informacji, zobacz poprzednią sekcję dotyczącą sposobu, w [jaki Blazor Server Aplikacja reaguje na Nieobsłużone wyjątki](#how-a-blazor-server-app-reacts-to-unhandled-exceptions).</span><span class="sxs-lookup"><span data-stu-id="92309-276">For more information, see the preceding section on [how a Blazor Server app reacts to unhandled exceptions](#how-a-blazor-server-app-reacts-to-unhandled-exceptions).</span></span>

## <a name="log-errors-with-a-persistent-provider"></a><span data-ttu-id="92309-277">Rejestrowanie błędów przez dostawcę trwałego</span><span class="sxs-lookup"><span data-stu-id="92309-277">Log errors with a persistent provider</span></span>

<span data-ttu-id="92309-278">Jeśli wystąpi nieobsługiwany wyjątek, wyjątek jest rejestrowany w <xref:Microsoft.Extensions.Logging.ILogger> wystąpieniach skonfigurowanych w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="92309-278">If an unhandled exception occurs, the exception is logged to <xref:Microsoft.Extensions.Logging.ILogger> instances configured in the service container.</span></span> <span data-ttu-id="92309-279">Domyślnie Blazor aplikacje rejestrują dane wyjściowe konsoli z dostawcą rejestrowania konsoli.</span><span class="sxs-lookup"><span data-stu-id="92309-279">By default, Blazor apps log to console output with the Console Logging Provider.</span></span> <span data-ttu-id="92309-280">Należy rozważyć rejestrowanie do bardziej trwałej lokalizacji na serwerze z dostawcą, który zarządza rozmiarem dziennika i rotacją dzienników.</span><span class="sxs-lookup"><span data-stu-id="92309-280">Consider logging to a more permanent location on the server with a provider that manages log size and log rotation.</span></span> <span data-ttu-id="92309-281">Alternatywnie aplikacja może używać usługi zarządzania wydajnością aplikacji (APM), takiej jak [Azure Application Insights (Azure monitor)](/azure/azure-monitor/app/app-insights-overview).</span><span class="sxs-lookup"><span data-stu-id="92309-281">Alternatively, the app can use an Application Performance Management (APM) service, such as [Azure Application Insights (Azure Monitor)](/azure/azure-monitor/app/app-insights-overview).</span></span>

<span data-ttu-id="92309-282">Podczas opracowywania Blazor Server aplikacja zwykle wysyła pełne szczegóły wyjątków do konsoli przeglądarki w celu ułatwienia debugowania.</span><span class="sxs-lookup"><span data-stu-id="92309-282">During development, a Blazor Server app usually sends the full details of exceptions to the browser's console to aid in debugging.</span></span> <span data-ttu-id="92309-283">W środowisku produkcyjnym szczegółowe błędy nie są wysyłane do klientów, ale na serwerze są rejestrowane pełne szczegóły wyjątku.</span><span class="sxs-lookup"><span data-stu-id="92309-283">In production, detailed errors aren't sent to clients, but an exception's full details are logged on the server.</span></span>

<span data-ttu-id="92309-284">Należy zdecydować, które zdarzenia mają być rejestrowane, oraz poziom ważności zarejestrowanych zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="92309-284">You must decide which incidents to log and the level of severity of logged incidents.</span></span> <span data-ttu-id="92309-285">Nieszkodliwi użytkownicy mogą być w stanie wyzwolić błędy w sposób celowy.</span><span class="sxs-lookup"><span data-stu-id="92309-285">Hostile users might be able to trigger errors deliberately.</span></span> <span data-ttu-id="92309-286">Na przykład nie należy rejestrować zdarzenia z błędu, gdy `ProductId` w adresie URL składnika, który zawiera szczegółowe informacje o produkcie.</span><span class="sxs-lookup"><span data-stu-id="92309-286">For example, don't log an incident from an error where an unknown `ProductId` is supplied in the URL of a component that displays product details.</span></span> <span data-ttu-id="92309-287">Nie wszystkie błędy powinny być traktowane jako zdarzenia do rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="92309-287">Not all errors should be treated as incidents for logging.</span></span>

<span data-ttu-id="92309-288">Aby uzyskać więcej informacji, zobacz następujące artykuły:</span><span class="sxs-lookup"><span data-stu-id="92309-288">For more information, see the following articles:</span></span>

* <xref:blazor/fundamentals/logging>
* <xref:fundamentals/error-handling>&dagger;

<span data-ttu-id="92309-289">&dagger;Dotyczy aplikacji ASP.NET Core po stronie serwera, które są aplikacjami zaplecza interfejsu API sieci Web dla Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="92309-289">&dagger;Applies to server-side ASP.NET Core apps that are web API backend apps for Blazor apps.</span></span>

## <a name="places-where-errors-may-occur"></a><span data-ttu-id="92309-290">Miejsca, w których mogą wystąpić błędy</span><span class="sxs-lookup"><span data-stu-id="92309-290">Places where errors may occur</span></span>

<span data-ttu-id="92309-291">Kod struktury i aplikacji może wyzwolić Nieobsłużone wyjątki w następujących lokalizacjach, które opisano w następujących sekcjach tego artykułu:</span><span class="sxs-lookup"><span data-stu-id="92309-291">Framework and app code may trigger unhandled exceptions in any of the following locations, which are described further in the following sections of this article:</span></span>

* [<span data-ttu-id="92309-292">Tworzenie wystąpienia składnika</span><span class="sxs-lookup"><span data-stu-id="92309-292">Component instantiation</span></span>](#component-instantiation-server)
* [<span data-ttu-id="92309-293">Metody cyklu życia</span><span class="sxs-lookup"><span data-stu-id="92309-293">Lifecycle methods</span></span>](#lifecycle-methods-server)
* [<span data-ttu-id="92309-294">Logika renderowania</span><span class="sxs-lookup"><span data-stu-id="92309-294">Rendering logic</span></span>](#rendering-logic-server)
* [<span data-ttu-id="92309-295">Procedury obsługi zdarzeń</span><span class="sxs-lookup"><span data-stu-id="92309-295">Event handlers</span></span>](#event-handlers-server)
* [<span data-ttu-id="92309-296">Usuwanie składników</span><span class="sxs-lookup"><span data-stu-id="92309-296">Component disposal</span></span>](#component-disposal-server)
* [<span data-ttu-id="92309-297">Międzyoperacyjność w języku JavaScript</span><span class="sxs-lookup"><span data-stu-id="92309-297">JavaScript interop</span></span>](#javascript-interop-server)
* [<span data-ttu-id="92309-298">Blazor Server Renderowanie</span><span class="sxs-lookup"><span data-stu-id="92309-298">Blazor Server rerendering</span></span>](#blazor-server-prerendering-server)

<h3 id="component-instantiation-server"><span data-ttu-id="92309-299">Tworzenie wystąpienia składnika</span><span class="sxs-lookup"><span data-stu-id="92309-299">Component instantiation</span></span></h3>

<span data-ttu-id="92309-300">Podczas Blazor tworzenia wystąpienia składnika:</span><span class="sxs-lookup"><span data-stu-id="92309-300">When Blazor creates an instance of a component:</span></span>

* <span data-ttu-id="92309-301">Konstruktor składnika jest wywoływany.</span><span class="sxs-lookup"><span data-stu-id="92309-301">The component's constructor is invoked.</span></span>
* <span data-ttu-id="92309-302">Konstruktory wszelkich niepojedynczych usług DI dostarczonych do konstruktora składnika za pośrednictwem [`@inject`](xref:mvc/views/razor#inject) dyrektywy lub [ `[Inject]` atrybutu](xref:blazor/fundamentals/dependency-injection#request-a-service-in-a-component) są wywoływane.</span><span class="sxs-lookup"><span data-stu-id="92309-302">The constructors of any non-singleton DI services supplied to the component's constructor via the [`@inject`](xref:mvc/views/razor#inject) directive or the [`[Inject]` attribute](xref:blazor/fundamentals/dependency-injection#request-a-service-in-a-component) are invoked.</span></span>

<span data-ttu-id="92309-303">Blazor ServerObwód kończy się niepowodzeniem, gdy dowolny wykonany Konstruktor lub setter dla każdej `[Inject]` właściwości zgłasza nieobsługiwany wyjątek.</span><span class="sxs-lookup"><span data-stu-id="92309-303">A Blazor Server circuit fails when any executed constructor or a setter for any `[Inject]` property throws an unhandled exception.</span></span> <span data-ttu-id="92309-304">Wyjątek jest krytyczny, ponieważ struktura nie może utworzyć wystąpienia składnika.</span><span class="sxs-lookup"><span data-stu-id="92309-304">The exception is fatal because the framework can't instantiate the component.</span></span> <span data-ttu-id="92309-305">Jeśli logika konstruktora może generować wyjątki, aplikacja powinna zalewkować wyjątki przy użyciu [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instrukcji z obsługą błędów i rejestrowaniem.</span><span class="sxs-lookup"><span data-stu-id="92309-305">If constructor logic may throw exceptions, the app should trap the exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<h3 id="lifecycle-methods-server"><span data-ttu-id="92309-306">Metody cyklu życia</span><span class="sxs-lookup"><span data-stu-id="92309-306">Lifecycle methods</span></span></h3>

<span data-ttu-id="92309-307">W okresie istnienia składnika Blazor wywołuje [metody cyklu życia](xref:blazor/components/lifecycle#lifecycle-methods).</span><span class="sxs-lookup"><span data-stu-id="92309-307">During the lifetime of a component, Blazor invokes [lifecycle methods](xref:blazor/components/lifecycle#lifecycle-methods).</span></span> <span data-ttu-id="92309-308">Jeśli jakakolwiek metoda cyklu życia zgłasza wyjątek, synchronicznie lub asynchronicznie, wyjątek jest krytyczny dla Blazor Server obwodu.</span><span class="sxs-lookup"><span data-stu-id="92309-308">If any lifecycle method throws an exception, synchronously or asynchronously, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="92309-309">Aby składniki zajmowały błędy w metodach cyklu życia, Dodaj logikę obsługi błędów.</span><span class="sxs-lookup"><span data-stu-id="92309-309">For components to deal with errors in lifecycle methods, add error handling logic.</span></span>

<span data-ttu-id="92309-310">W poniższym przykładzie, gdzie <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> wywołuje metodę w celu uzyskania produktu:</span><span class="sxs-lookup"><span data-stu-id="92309-310">In the following example where <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> calls a method to obtain a product:</span></span>

* <span data-ttu-id="92309-311">Wyjątek zgłoszony w `ProductRepository.GetProductByIdAsync` metodzie jest obsługiwany przez [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instrukcję.</span><span class="sxs-lookup"><span data-stu-id="92309-311">An exception thrown in the `ProductRepository.GetProductByIdAsync` method is handled by a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement.</span></span>
* <span data-ttu-id="92309-312">Gdy `catch` blok jest wykonywany:</span><span class="sxs-lookup"><span data-stu-id="92309-312">When the `catch` block is executed:</span></span>
  * <span data-ttu-id="92309-313">`loadFailed` jest ustawiona na `true` , która jest używana do wyświetlania komunikatu o błędzie dla użytkownika.</span><span class="sxs-lookup"><span data-stu-id="92309-313">`loadFailed` is set to `true`, which is used to display an error message to the user.</span></span>
  * <span data-ttu-id="92309-314">Błąd jest rejestrowany.</span><span class="sxs-lookup"><span data-stu-id="92309-314">The error is logged.</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_Server/Pages/handle-errors/ProductDetails.razor?name=snippet&highlight=11,27-39)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_Server/Pages/handle-errors/ProductDetails.razor?name=snippet&highlight=11,27-39)]

::: moniker-end

<h3 id="rendering-logic-server"><span data-ttu-id="92309-315">Logika renderowania</span><span class="sxs-lookup"><span data-stu-id="92309-315">Rendering logic</span></span></h3>

<span data-ttu-id="92309-316">Znaczniki deklaratywne w Razor pliku składnika ( `.razor` ) są kompilowane do metody języka C# o nazwie <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> .</span><span class="sxs-lookup"><span data-stu-id="92309-316">The declarative markup in a Razor component file (`.razor`) is compiled into a C# method called <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A>.</span></span> <span data-ttu-id="92309-317">Gdy składnik renderuje, <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> wykonuje i tworzy strukturę danych opisującą elementy, tekst i składniki podrzędne renderowanego składnika.</span><span class="sxs-lookup"><span data-stu-id="92309-317">When a component renders, <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> executes and builds up a data structure describing the elements, text, and child components of the rendered component.</span></span>

<span data-ttu-id="92309-318">Logika renderowania może zgłosić wyjątek.</span><span class="sxs-lookup"><span data-stu-id="92309-318">Rendering logic can throw an exception.</span></span> <span data-ttu-id="92309-319">Przykład tego scenariusza występuje, gdy `@someObject.PropertyName` jest oceniane, ale `@someObject` jest `null` .</span><span class="sxs-lookup"><span data-stu-id="92309-319">An example of this scenario occurs when `@someObject.PropertyName` is evaluated but `@someObject` is `null`.</span></span> <span data-ttu-id="92309-320">Nieobsługiwany wyjątek zgłoszony przez logikę renderowania jest krytyczny dla Blazor Server obwodu.</span><span class="sxs-lookup"><span data-stu-id="92309-320">An unhandled exception thrown by rendering logic is fatal to a Blazor Server circuit.</span></span>

<span data-ttu-id="92309-321">Aby uniknąć <xref:System.NullReferenceException> renderowania logiki, `null` przed uzyskaniem dostępu do elementów członkowskich Sprawdź, czy jest on obiektem.</span><span class="sxs-lookup"><span data-stu-id="92309-321">To prevent a <xref:System.NullReferenceException> in rendering logic, check for a `null` object before accessing its members.</span></span> <span data-ttu-id="92309-322">W poniższym przykładzie `person.Address` właściwości nie są dostępne, jeśli `person.Address` `null` :</span><span class="sxs-lookup"><span data-stu-id="92309-322">In the following example, `person.Address` properties aren't accessed if `person.Address` is `null`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_Server/Pages/handle-errors/PersonExample.razor?name=snippet&highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_Server/Pages/handle-errors/PersonExample.razor?name=snippet&highlight=1)]

::: moniker-end

<span data-ttu-id="92309-323">Poprzedni kod założono, że `person` nie jest `null` .</span><span class="sxs-lookup"><span data-stu-id="92309-323">The preceding code assumes that `person` isn't `null`.</span></span> <span data-ttu-id="92309-324">Często Struktura kodu gwarantuje, że obiekt istnieje w momencie renderowania składnika.</span><span class="sxs-lookup"><span data-stu-id="92309-324">Often, the structure of the code guarantees that an object exists at the time the component is rendered.</span></span> <span data-ttu-id="92309-325">W takich przypadkach nie jest konieczne sprawdzanie `null` logiki renderowania.</span><span class="sxs-lookup"><span data-stu-id="92309-325">In those cases, it isn't necessary to check for `null` in rendering logic.</span></span> <span data-ttu-id="92309-326">W poprzednim przykładzie `person` można zagwarantować, że istnieje, ponieważ `person` jest tworzony podczas tworzenia wystąpienia składnika, jak pokazano na poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="92309-326">In the prior example, `person` might be guaranteed to exist because `person` is created when the component is instantiated, as the following example shows:</span></span>

```razor
@code {
    private Person person = new Person();

    ...
}
```

<h3 id="event-handlers-server"><span data-ttu-id="92309-327">Procedury obsługi zdarzeń</span><span class="sxs-lookup"><span data-stu-id="92309-327">Event handlers</span></span></h3>

<span data-ttu-id="92309-328">Kod po stronie klienta wyzwala wywołania kodu C#, gdy programy obsługi zdarzeń są tworzone przy użyciu:</span><span class="sxs-lookup"><span data-stu-id="92309-328">Client-side code triggers invocations of C# code when event handlers are created using:</span></span>

* `@onclick`
* `@onchange`
* <span data-ttu-id="92309-329">Inne `@on...` atrybuty</span><span class="sxs-lookup"><span data-stu-id="92309-329">Other `@on...` attributes</span></span>
* `@bind`

<span data-ttu-id="92309-330">Kod procedury obsługi zdarzeń może zgłosić nieobsługiwany wyjątek w tych scenariuszach.</span><span class="sxs-lookup"><span data-stu-id="92309-330">Event handler code might throw an unhandled exception in these scenarios.</span></span>

<span data-ttu-id="92309-331">Jeśli procedura obsługi zdarzeń zgłasza nieobsługiwany wyjątek (na przykład kwerenda bazy danych kończy się niepowodzeniem), wyjątek jest krytyczny dla Blazor Server obwodu.</span><span class="sxs-lookup"><span data-stu-id="92309-331">If an event handler throws an unhandled exception (for example, a database query fails), the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="92309-332">Jeśli aplikacja wywołuje kod, który może zakończyć się niepowodzeniem z powodów zewnętrznych, należy zastosować wyjątek pułapki przy użyciu [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instrukcji z obsługą błędów i rejestrowaniem.</span><span class="sxs-lookup"><span data-stu-id="92309-332">If the app calls code that could fail for external reasons, trap exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="92309-333">Jeśli kod użytkownika nie jest pułapk i nie obsługuje wyjątku, struktura rejestruje wyjątek i kończy obwód.</span><span class="sxs-lookup"><span data-stu-id="92309-333">If user code doesn't trap and handle the exception, the framework logs the exception and terminates the circuit.</span></span>

<h3 id="component-disposal-server"><span data-ttu-id="92309-334">Usuwanie składników</span><span class="sxs-lookup"><span data-stu-id="92309-334">Component disposal</span></span></h3>

<span data-ttu-id="92309-335">Składnik może zostać usunięty z interfejsu użytkownika, na przykład, ponieważ użytkownik przeszedł do innej strony.</span><span class="sxs-lookup"><span data-stu-id="92309-335">A component may be removed from the UI, for example, because the user has navigated to another page.</span></span> <span data-ttu-id="92309-336">Gdy składnik implementujący <xref:System.IDisposable?displayProperty=fullName> jest usuwany z interfejsu użytkownika, struktura wywołuje <xref:System.IDisposable.Dispose%2A> metodę składnika.</span><span class="sxs-lookup"><span data-stu-id="92309-336">When a component that implements <xref:System.IDisposable?displayProperty=fullName> is removed from the UI, the framework calls the component's <xref:System.IDisposable.Dispose%2A> method.</span></span>

<span data-ttu-id="92309-337">Jeśli `Dispose` Metoda składnika zgłasza nieobsługiwany wyjątek, wyjątek jest krytyczny dla Blazor Server obwodu.</span><span class="sxs-lookup"><span data-stu-id="92309-337">If the component's `Dispose` method throws an unhandled exception, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="92309-338">Jeśli logika usuwania może generować wyjątki, aplikacja powinna zalewkować wyjątki przy użyciu [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instrukcji z obsługą błędów i rejestrowaniem.</span><span class="sxs-lookup"><span data-stu-id="92309-338">If disposal logic may throw exceptions, the app should trap the exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="92309-339">Aby uzyskać więcej informacji na temat usuwania składników, zobacz <xref:blazor/components/lifecycle#component-disposal-with-idisposable> .</span><span class="sxs-lookup"><span data-stu-id="92309-339">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

<h3 id="javascript-interop-server"><span data-ttu-id="92309-340">Międzyoperacyjność w języku JavaScript</span><span class="sxs-lookup"><span data-stu-id="92309-340">JavaScript interop</span></span></h3>

<span data-ttu-id="92309-341"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> umożliwia programowi .NET Code wykonywanie wywołań asynchronicznych do środowiska uruchomieniowego JavaScript w przeglądarce użytkownika.</span><span class="sxs-lookup"><span data-stu-id="92309-341"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> allows .NET code to make asynchronous calls to the JavaScript runtime in the user's browser.</span></span>

<span data-ttu-id="92309-342">Poniższe warunki dotyczą obsługi błędów w programie <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> :</span><span class="sxs-lookup"><span data-stu-id="92309-342">The following conditions apply to error handling with <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>:</span></span>

* <span data-ttu-id="92309-343">Jeśli wywołanie <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> synchronicznie zakończy się niepowodzeniem, wystąpi wyjątek programu .NET.</span><span class="sxs-lookup"><span data-stu-id="92309-343">If a call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> fails synchronously, a .NET exception occurs.</span></span> <span data-ttu-id="92309-344">Wywołanie <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> może zakończyć się niepowodzeniem, na przykład dlatego, że nie można serializować dostarczonych argumentów.</span><span class="sxs-lookup"><span data-stu-id="92309-344">A call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> may fail, for example, because the supplied arguments can't be serialized.</span></span> <span data-ttu-id="92309-345">Kod dewelopera musi przechwycić wyjątek.</span><span class="sxs-lookup"><span data-stu-id="92309-345">Developer code must catch the exception.</span></span> <span data-ttu-id="92309-346">Jeśli kod aplikacji w obsłudze zdarzeń lub metoda cyklu życia składnika nie obsługuje wyjątku, wynikający z nich wyjątek jest krytyczny dla Blazor Server obwodu.</span><span class="sxs-lookup"><span data-stu-id="92309-346">If app code in an event handler or component lifecycle method doesn't handle an exception, the resulting exception is fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="92309-347">Jeśli wywołanie <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> powiedzie się asynchronicznie, .NET <xref:System.Threading.Tasks.Task> kończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="92309-347">If a call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> fails asynchronously, the .NET <xref:System.Threading.Tasks.Task> fails.</span></span> <span data-ttu-id="92309-348">Wywołanie <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> może zakończyć się niepowodzeniem, na przykład ponieważ kod po stronie JavaScript zgłasza wyjątek lub zwraca `Promise` , który został ukończony jako `rejected` .</span><span class="sxs-lookup"><span data-stu-id="92309-348">A call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> may fail, for example, because the JavaScript-side code throws an exception or returns a `Promise` that completed as `rejected`.</span></span> <span data-ttu-id="92309-349">Kod dewelopera musi przechwycić wyjątek.</span><span class="sxs-lookup"><span data-stu-id="92309-349">Developer code must catch the exception.</span></span> <span data-ttu-id="92309-350">W przypadku użycia [`await`](/dotnet/csharp/language-reference/keywords/await) operatora Rozważ zapakowanie wywołania metody w [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instrukcji z obsługą błędów i rejestrowaniem.</span><span class="sxs-lookup"><span data-stu-id="92309-350">If using the [`await`](/dotnet/csharp/language-reference/keywords/await) operator, consider wrapping the method call in a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span> <span data-ttu-id="92309-351">W przeciwnym razie niepowodzenie kodu spowoduje nieobsłużony wyjątek, który jest krytyczny dla Blazor Server obwodu.</span><span class="sxs-lookup"><span data-stu-id="92309-351">Otherwise, the failing code results in an unhandled exception that's fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="92309-352">Domyślnie wywołania programu <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> muszą zakończyć się w określonym przedziale czasu lub w przeciwnym razie upłynął limit czasu połączenia. Domyślny limit czasu wynosi jedną minutę.</span><span class="sxs-lookup"><span data-stu-id="92309-352">By default, calls to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> must complete within a certain period or else the call times out. The default timeout period is one minute.</span></span> <span data-ttu-id="92309-353">Limit czasu chroni kod przed utratą połączenia sieciowego lub kodem JavaScript, który nigdy nie odsyła komunikat uzupełniający.</span><span class="sxs-lookup"><span data-stu-id="92309-353">The timeout protects the code against a loss in network connectivity or JavaScript code that never sends back a completion message.</span></span> <span data-ttu-id="92309-354">Jeśli wystąpiło przełączenie, wynikiem <xref:System.Threading.Tasks> kończy się niepowodzeniem a <xref:System.OperationCanceledException> .</span><span class="sxs-lookup"><span data-stu-id="92309-354">If the call times out, the resulting <xref:System.Threading.Tasks> fails with an <xref:System.OperationCanceledException>.</span></span> <span data-ttu-id="92309-355">Zalewka i przetwórz wyjątek z rejestrowaniem.</span><span class="sxs-lookup"><span data-stu-id="92309-355">Trap and process the exception with logging.</span></span>

<span data-ttu-id="92309-356">Podobnie kod JavaScript może inicjować wywołania metod .NET wskazywanych przez [ `[JSInvokable]` atrybut](xref:blazor/call-dotnet-from-javascript).</span><span class="sxs-lookup"><span data-stu-id="92309-356">Similarly, JavaScript code may initiate calls to .NET methods indicated by the [`[JSInvokable]` attribute](xref:blazor/call-dotnet-from-javascript).</span></span> <span data-ttu-id="92309-357">Jeśli te metody .NET zgłaszają nieobsługiwany wyjątek:</span><span class="sxs-lookup"><span data-stu-id="92309-357">If these .NET methods throw an unhandled exception:</span></span>

* <span data-ttu-id="92309-358">Wyjątek nie jest traktowany jako krytyczny dla Blazor Server obwodu.</span><span class="sxs-lookup"><span data-stu-id="92309-358">The exception isn't treated as fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="92309-359">Po stronie JavaScript `Promise` jest odrzucany.</span><span class="sxs-lookup"><span data-stu-id="92309-359">The JavaScript-side `Promise` is rejected.</span></span>

<span data-ttu-id="92309-360">Istnieje możliwość użycia kodu obsługi błędów po stronie .NET lub stronie JavaScript wywołania metody.</span><span class="sxs-lookup"><span data-stu-id="92309-360">You have the option of using error handling code on either the .NET side or the JavaScript side of the method call.</span></span>

<span data-ttu-id="92309-361">Aby uzyskać więcej informacji, zobacz następujące artykuły:</span><span class="sxs-lookup"><span data-stu-id="92309-361">For more information, see the following articles:</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

<h3 id="blazor-server-prerendering-server"><span data-ttu-id="92309-362">Blazor Server Renderowanie prerenderingu</span><span class="sxs-lookup"><span data-stu-id="92309-362">Blazor Server prerendering</span></span></h3>

<span data-ttu-id="92309-363">Blazor składniki mogą być wstępnie renderowane przy użyciu [pomocnika tagów składnika](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) , dzięki czemu RENDEROWANE znaczniki HTML są zwracane jako część początkowego żądania HTTP użytkownika.</span><span class="sxs-lookup"><span data-stu-id="92309-363">Blazor components can be prerendered using the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) so that their rendered HTML markup is returned as part of the user's initial HTTP request.</span></span> <span data-ttu-id="92309-364">Działa to w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="92309-364">This works by:</span></span>

* <span data-ttu-id="92309-365">Tworzenie nowego obwodu dla wszystkich wstępnie renderowanych składników, które są częścią tej samej strony.</span><span class="sxs-lookup"><span data-stu-id="92309-365">Creating a new circuit for all of the prerendered components that are part of the same page.</span></span>
* <span data-ttu-id="92309-366">Generowanie początkowego kodu HTML.</span><span class="sxs-lookup"><span data-stu-id="92309-366">Generating the initial HTML.</span></span>
* <span data-ttu-id="92309-367">Przetraktowanie obwodu `disconnected` do momentu, aż przeglądarka użytkownika nawiąże SignalR połączenie z powrotem z tym samym serwerem.</span><span class="sxs-lookup"><span data-stu-id="92309-367">Treating the circuit as `disconnected` until the user's browser establishes a SignalR connection back to the same server.</span></span> <span data-ttu-id="92309-368">Po nawiązaniu połączenia zostanie wznowione działanie międzydziałające w obwodzie i zostanie zaktualizowane oznaczenie HTML składników.</span><span class="sxs-lookup"><span data-stu-id="92309-368">When the connection is established, interactivity on the circuit is resumed and the components' HTML markup is updated.</span></span>

<span data-ttu-id="92309-369">Jeśli jakikolwiek składnik zgłasza nieobsłużony wyjątek podczas renderowania pre, na przykład podczas wykonywania metody cyklu życia lub logiki renderowania:</span><span class="sxs-lookup"><span data-stu-id="92309-369">If any component throws an unhandled exception during prerendering, for example, during a lifecycle method or in rendering logic:</span></span>

* <span data-ttu-id="92309-370">Wyjątek jest krytyczny dla obwodu.</span><span class="sxs-lookup"><span data-stu-id="92309-370">The exception is fatal to the circuit.</span></span>
* <span data-ttu-id="92309-371">Wyjątek jest generowany w stosie wywołań z <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> pomocnika tagów.</span><span class="sxs-lookup"><span data-stu-id="92309-371">The exception is thrown up the call stack from the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper.</span></span> <span data-ttu-id="92309-372">W związku z tym całe żądanie HTTP kończy się niepowodzeniem, chyba że wyjątek jest jawnie przechwycony przez kod dewelopera.</span><span class="sxs-lookup"><span data-stu-id="92309-372">Therefore, the entire HTTP request fails unless the exception is explicitly caught by developer code.</span></span>

<span data-ttu-id="92309-373">W normalnych warunkach w przypadku niepowodzenia wstępnego renderowania kontynuowanie kompilowania i renderowania składnika nie ma sensu, ponieważ nie można renderować składnika roboczego.</span><span class="sxs-lookup"><span data-stu-id="92309-373">Under normal circumstances when prerendering fails, continuing to build and render the component doesn't make sense because a working component can't be rendered.</span></span>

<span data-ttu-id="92309-374">Aby tolerować błędy, które mogą wystąpić podczas renderowania prerenderingu, logika obsługi błędów musi być umieszczona wewnątrz składnika, który może zgłaszać wyjątki.</span><span class="sxs-lookup"><span data-stu-id="92309-374">To tolerate errors that may occur during prerendering, error handling logic must be placed inside a component that may throw exceptions.</span></span> <span data-ttu-id="92309-375">Używaj [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instrukcji z obsługą błędów i rejestrowaniem.</span><span class="sxs-lookup"><span data-stu-id="92309-375">Use [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span> <span data-ttu-id="92309-376">Zamiast zawijać <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> pomocnika tagów w [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instrukcji, umieść logikę obsługi błędów w składniku renderowanym przez <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> pomocnika tagów.</span><span class="sxs-lookup"><span data-stu-id="92309-376">Instead of wrapping the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper in a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement, place error handling logic in the component rendered by the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper.</span></span>

## <a name="advanced-scenarios"></a><span data-ttu-id="92309-377">Scenariusze zaawansowane</span><span class="sxs-lookup"><span data-stu-id="92309-377">Advanced scenarios</span></span>

### <a name="recursive-rendering"></a><span data-ttu-id="92309-378">Renderowanie cykliczne</span><span class="sxs-lookup"><span data-stu-id="92309-378">Recursive rendering</span></span>

<span data-ttu-id="92309-379">Składniki można cyklicznie zagnieżdżać.</span><span class="sxs-lookup"><span data-stu-id="92309-379">Components can be nested recursively.</span></span> <span data-ttu-id="92309-380">Jest to przydatne do reprezentowania struktur danych rekursywnych.</span><span class="sxs-lookup"><span data-stu-id="92309-380">This is useful for representing recursive data structures.</span></span> <span data-ttu-id="92309-381">Na przykład `TreeNode` składnik może renderować więcej `TreeNode` składników dla każdego węzła podrzędnego.</span><span class="sxs-lookup"><span data-stu-id="92309-381">For example, a `TreeNode` component can render more `TreeNode` components for each of the node's children.</span></span>

<span data-ttu-id="92309-382">W przypadku renderowania cyklicznego należy unikać tworzenia wzorców, które powodują nieskończoną rekursję:</span><span class="sxs-lookup"><span data-stu-id="92309-382">When rendering recursively, avoid coding patterns that result in infinite recursion:</span></span>

* <span data-ttu-id="92309-383">Nie Renderuj rekursywnie struktury danych zawierającej cykl.</span><span class="sxs-lookup"><span data-stu-id="92309-383">Don't recursively render a data structure that contains a cycle.</span></span> <span data-ttu-id="92309-384">Na przykład nie Renderuj węzła drzewa, którego elementy podrzędne zawierają sam siebie.</span><span class="sxs-lookup"><span data-stu-id="92309-384">For example, don't render a tree node whose children includes itself.</span></span>
* <span data-ttu-id="92309-385">Nie twórz łańcucha układów zawierających cykl.</span><span class="sxs-lookup"><span data-stu-id="92309-385">Don't create a chain of layouts that contain a cycle.</span></span> <span data-ttu-id="92309-386">Na przykład nie twórz układu, którego układ jest sam.</span><span class="sxs-lookup"><span data-stu-id="92309-386">For example, don't create a layout whose layout is itself.</span></span>
* <span data-ttu-id="92309-387">Nie Zezwalaj użytkownikowi końcowemu na naruszenie nieodmian rekursji (reguł) przy użyciu złośliwego wpisu danych lub wywołań międzyoperacyjnych języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="92309-387">Don't allow an end user to violate recursion invariants (rules) through malicious data entry or JavaScript interop calls.</span></span>

<span data-ttu-id="92309-388">Nieskończone pętle podczas renderowania:</span><span class="sxs-lookup"><span data-stu-id="92309-388">Infinite loops during rendering:</span></span>

* <span data-ttu-id="92309-389">Powoduje, że proces renderowania kontynuuje działanie zawsze.</span><span class="sxs-lookup"><span data-stu-id="92309-389">Causes the rendering process to continue forever.</span></span>
* <span data-ttu-id="92309-390">Jest równoznaczny z tworzeniem niezakończonej pętli.</span><span class="sxs-lookup"><span data-stu-id="92309-390">Is equivalent to creating an unterminated loop.</span></span>

<span data-ttu-id="92309-391">W tych scenariuszach obwód, którego to dotyczy, Blazor Server kończy się niepowodzeniem, a wątek zwykle próbuje wykonać:</span><span class="sxs-lookup"><span data-stu-id="92309-391">In these scenarios, an affected Blazor Server circuit fails, and the thread usually attempts to:</span></span>

* <span data-ttu-id="92309-392">Zużywaj ilość czasu procesora CPU dozwoloną przez system operacyjny w nieskończoność.</span><span class="sxs-lookup"><span data-stu-id="92309-392">Consume as much CPU time as permitted by the operating system, indefinitely.</span></span>
* <span data-ttu-id="92309-393">Korzystaj z nieograniczonej ilości pamięci serwera.</span><span class="sxs-lookup"><span data-stu-id="92309-393">Consume an unlimited amount of server memory.</span></span> <span data-ttu-id="92309-394">Zużywanie nieograniczonej pamięci jest równoważne scenariuszowi, w którym niezakończona pętla dodaje wpisy do kolekcji na każdej iteracji.</span><span class="sxs-lookup"><span data-stu-id="92309-394">Consuming unlimited memory is equivalent to the scenario where an unterminated loop adds entries to a collection on every iteration.</span></span>

<span data-ttu-id="92309-395">Aby uniknąć nieskończonych wzorców rekursji, należy się upewnić, że kod renderowania cyklicznego zawiera odpowiednie warunki zatrzymania.</span><span class="sxs-lookup"><span data-stu-id="92309-395">To avoid infinite recursion patterns, ensure that recursive rendering code contains suitable stopping conditions.</span></span>

### <a name="custom-render-tree-logic"></a><span data-ttu-id="92309-396">Logika drzewa renderowania niestandardowego</span><span class="sxs-lookup"><span data-stu-id="92309-396">Custom render tree logic</span></span>

<span data-ttu-id="92309-397">Większość Blazor składników jest implementowana jako Razor pliki składników ( `.razor` ) i są kompilowane przez platformę w celu utworzenia logiki, która działa w <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> celu renderowania danych wyjściowych.</span><span class="sxs-lookup"><span data-stu-id="92309-397">Most Blazor components are implemented as Razor component files (`.razor`) and are compiled by the framework to produce logic that operates on a <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> to render their output.</span></span> <span data-ttu-id="92309-398">Jednak deweloper może ręcznie zaimplementować <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logikę przy użyciu procedury kodu w języku C#.</span><span class="sxs-lookup"><span data-stu-id="92309-398">However, a developer may manually implement <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic using procedural C# code.</span></span> <span data-ttu-id="92309-399">Aby uzyskać więcej informacji, zobacz <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span><span class="sxs-lookup"><span data-stu-id="92309-399">For more information, see <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span></span>

> [!WARNING]
> <span data-ttu-id="92309-400">Korzystanie z logiki konstruktora drzewa renderowania ręcznego jest uznawane za zaawansowane i niebezpieczne scenariusze, które nie są zalecane do ogólnego tworzenia składników.</span><span class="sxs-lookup"><span data-stu-id="92309-400">Use of manual render tree builder logic is considered an advanced and unsafe scenario, not recommended for general component development.</span></span>

<span data-ttu-id="92309-401">Jeśli <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> kod zostanie zapisany, Deweloper musi zagwarantować poprawność kodu.</span><span class="sxs-lookup"><span data-stu-id="92309-401">If <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> code is written, the developer must guarantee the correctness of the code.</span></span> <span data-ttu-id="92309-402">Na przykład Deweloper musi upewnić się, że:</span><span class="sxs-lookup"><span data-stu-id="92309-402">For example, the developer must ensure that:</span></span>

* <span data-ttu-id="92309-403">Wywołania <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> i <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A> są prawidłowo zrównoważone.</span><span class="sxs-lookup"><span data-stu-id="92309-403">Calls to <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> and <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A> are correctly balanced.</span></span>
* <span data-ttu-id="92309-404">Atrybuty są dodawane tylko w prawidłowych miejscach.</span><span class="sxs-lookup"><span data-stu-id="92309-404">Attributes are only added in the correct places.</span></span>

<span data-ttu-id="92309-405">Nieprawidłowa ręczna logika konstruktora drzewa renderowania może spowodować dowolne niezdefiniowane zachowanie, w tym awarie, zawieszenie serwera i luki w zabezpieczeniach.</span><span class="sxs-lookup"><span data-stu-id="92309-405">Incorrect manual render tree builder logic can cause arbitrary undefined behavior, including crashes, server hangs, and security vulnerabilities.</span></span>

<span data-ttu-id="92309-406">Rozważ ręczne renderowanie logiki konstruktora drzew drzewa na tym samym poziomie złożoności i z tym samym poziomem *zagrożenia* co ręczne pisanie kodu zestawu lub [języka pośredniego firmy Microsoft (MSIL)](/dotnet/standard/managed-code) .</span><span class="sxs-lookup"><span data-stu-id="92309-406">Consider manual render tree builder logic on the same level of complexity and with the same level of *danger* as writing assembly code or [Microsoft Intermediate Language (MSIL)](/dotnet/standard/managed-code) instructions by hand.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="92309-407">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="92309-407">Additional resources</span></span>

* <xref:blazor/fundamentals/logging>
* <xref:fundamentals/error-handling>&dagger;

<span data-ttu-id="92309-408">&dagger;Dotyczy aplikacji ASP.NET Core po stronie serwera, które są aplikacjami zaplecza interfejsu API sieci Web dla Blazor aplikacji.</span><span class="sxs-lookup"><span data-stu-id="92309-408">&dagger;Applies to server-side ASP.NET Core apps that are web API backend apps for Blazor apps.</span></span>

::: zone-end
