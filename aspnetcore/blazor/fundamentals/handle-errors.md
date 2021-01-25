---
title: Obsługa błędów w Blazor aplikacjach ASP.NET Core
author: guardrex
description: Odkryj, jak ASP.NET Core Blazor jak program Blazor zarządza nieobsługiwanymi wyjątkami i jak opracowywać aplikacje wykrywające i obsługujące błędy.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
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
ms.openlocfilehash: 5a255c2d3535311cecd6b7219447e80d1ae78877
ms.sourcegitcommit: d4836f9b7c508f51c6c4ee6d0cc719b38c1729c4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98758254"
---
# <a name="handle-errors-in-aspnet-core-no-locblazor-apps"></a><span data-ttu-id="daa5e-103">Obsługa błędów w Blazor aplikacjach ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="daa5e-103">Handle errors in ASP.NET Core Blazor apps</span></span>

<span data-ttu-id="daa5e-104">[Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="daa5e-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="daa5e-105">W tym artykule opisano, jak Blazor zarządzać nieobsługiwanymi wyjątkami oraz jak opracowywać aplikacje wykrywające i obsługujące błędy.</span><span class="sxs-lookup"><span data-stu-id="daa5e-105">This article describes how Blazor manages unhandled exceptions and how to develop apps that detect and handle errors.</span></span>

## <a name="detailed-errors-during-development"></a><span data-ttu-id="daa5e-106">Szczegóły błędów podczas opracowywania</span><span class="sxs-lookup"><span data-stu-id="daa5e-106">Detailed errors during development</span></span>

<span data-ttu-id="daa5e-107">Gdy Blazor aplikacja nie działa prawidłowo podczas opracowywania, otrzymuje szczegółowe informacje o błędzie z aplikacji, które pomagają w rozwiązywaniu problemów i rozwiązaniu problemu.</span><span class="sxs-lookup"><span data-stu-id="daa5e-107">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="daa5e-108">Gdy wystąpi błąd, Blazor aplikacje wyświetlają złoty pasek u dołu ekranu:</span><span class="sxs-lookup"><span data-stu-id="daa5e-108">When an error occurs, Blazor apps display a gold bar at the bottom of the screen:</span></span>

* <span data-ttu-id="daa5e-109">W trakcie programowania złoty pasek kieruje użytkownika do konsoli przeglądarki, gdzie można zobaczyć wyjątek.</span><span class="sxs-lookup"><span data-stu-id="daa5e-109">During development, the gold bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="daa5e-110">W środowisku produkcyjnym złoty pasek powiadamia użytkownika o wystąpieniu błędu i zaleca odświeżenie przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="daa5e-110">In production, the gold bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="daa5e-111">Interfejs użytkownika dla tego środowiska obsługi błędów jest częścią Blazor szablonów projektu.</span><span class="sxs-lookup"><span data-stu-id="daa5e-111">The UI for this error handling experience is part of the Blazor project templates.</span></span>

<span data-ttu-id="daa5e-112">W Blazor WebAssembly aplikacji Dostosuj środowisko w `wwwroot/index.html` pliku:</span><span class="sxs-lookup"><span data-stu-id="daa5e-112">In a Blazor WebAssembly app, customize the experience in the `wwwroot/index.html` file:</span></span>

```html
<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="daa5e-113">W Blazor Server aplikacji Dostosuj środowisko w `Pages/_Host.cshtml` pliku:</span><span class="sxs-lookup"><span data-stu-id="daa5e-113">In a Blazor Server app, customize the experience in the `Pages/_Host.cshtml` file:</span></span>

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

<span data-ttu-id="daa5e-114">`blazor-error-ui`Element jest ukryty przez style zawarte w Blazor szablonach ( `wwwroot/css/app.css` lub `wwwroot/css/site.css` ), a następnie pokazywany w przypadku wystąpienia błędu:</span><span class="sxs-lookup"><span data-stu-id="daa5e-114">The `blazor-error-ui` element is hidden by the styles included in the Blazor templates (`wwwroot/css/app.css` or `wwwroot/css/site.css`) and then shown when an error occurs:</span></span>

```css
#blazor-error-ui {
    background: lightyellow;
    bottom: 0;
    box-shadow: 0 -1px 2px rgba(0, 0, 0, 0.2);
    display: none;
    left: 0;
    padding: 0.6rem 1.25rem 0.7rem 1.25rem;
    position: fixed;
    width: 100%;
    z-index: 1000;
}

#blazor-error-ui .dismiss {
    cursor: pointer;
    position: absolute;
    right: 0.75rem;
    top: 0.5rem;
}
```

## <a name="no-locblazor-server-detailed-circuit-errors"></a><span data-ttu-id="daa5e-115">Blazor Server Szczegóły błędów obwodu</span><span class="sxs-lookup"><span data-stu-id="daa5e-115">Blazor Server detailed circuit errors</span></span>

<span data-ttu-id="daa5e-116">Błędy po stronie klienta nie zawierają stosu wywołań i nie zawierają szczegółów przyczyny błędu, ale Dzienniki serwera zawierają takie informacje.</span><span class="sxs-lookup"><span data-stu-id="daa5e-116">Client-side errors don't include the callstack and don't provide detail on the cause of the error, but server logs do contain such information.</span></span> <span data-ttu-id="daa5e-117">W celach programistycznych informacje o błędzie obwodu poufnego mogą być udostępniane klientowi, włączając szczegółowe błędy.</span><span class="sxs-lookup"><span data-stu-id="daa5e-117">For development purposes, sensitive circuit error information can be made available to the client by enabling detailed errors.</span></span>

<span data-ttu-id="daa5e-118">Włącz Blazor Server szczegółowe błędy przy użyciu następujących metod:</span><span class="sxs-lookup"><span data-stu-id="daa5e-118">Enable Blazor Server detailed errors using the following approaches:</span></span>

* <span data-ttu-id="daa5e-119"><xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="daa5e-119"><xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors?displayProperty=nameWithType>.</span></span>
* <span data-ttu-id="daa5e-120">`DetailedErrors`Klucz konfiguracji ustawiony na `true` , który można ustawić w pliku ustawień programowania aplikacji ( `appsettings.Development.json` ).</span><span class="sxs-lookup"><span data-stu-id="daa5e-120">The `DetailedErrors` configuration key set to `true`, which can be set in the app's Development settings file (`appsettings.Development.json`).</span></span> <span data-ttu-id="daa5e-121">Klucz można również ustawić przy użyciu `ASPNETCORE_DETAILEDERRORS` zmiennej środowiskowej o wartości `true` .</span><span class="sxs-lookup"><span data-stu-id="daa5e-121">The key can also be set using the `ASPNETCORE_DETAILEDERRORS` environment variable with a value of `true`.</span></span>
* <span data-ttu-id="daa5e-122">[ SignalR Rejestrowanie po stronie serwera](xref:signalr/diagnostics#server-side-logging) ( `Microsoft.AspNetCore.SignalR` ) można ustawić na [debugowanie](xref:Microsoft.Extensions.Logging.LogLevel) lub [śledzenie](xref:Microsoft.Extensions.Logging.LogLevel) szczegółowego SignalR rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="daa5e-122">[SignalR server-side logging](xref:signalr/diagnostics#server-side-logging) (`Microsoft.AspNetCore.SignalR`) can be set to [Debug](xref:Microsoft.Extensions.Logging.LogLevel) or [Trace](xref:Microsoft.Extensions.Logging.LogLevel) for detailed SignalR logging.</span></span>

<span data-ttu-id="daa5e-123">`appsettings.Development.json`:</span><span class="sxs-lookup"><span data-stu-id="daa5e-123">`appsettings.Development.json`:</span></span>

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

> [!WARNING]
> <span data-ttu-id="daa5e-124">Ujawnienie informacji o błędach klientom w Internecie stanowi zagrożenie bezpieczeństwa, które należy zawsze uniknąć.</span><span class="sxs-lookup"><span data-stu-id="daa5e-124">Exposing error information to clients on the Internet is a security risk that should always be avoided.</span></span>

## <a name="how-a-no-locblazor-server-app-reacts-to-unhandled-exceptions"></a><span data-ttu-id="daa5e-125">Jak Blazor Server Aplikacja reaguje na Nieobsłużone wyjątki</span><span class="sxs-lookup"><span data-stu-id="daa5e-125">How a Blazor Server app reacts to unhandled exceptions</span></span>

<span data-ttu-id="daa5e-126">Blazor Server jest strukturą stanową.</span><span class="sxs-lookup"><span data-stu-id="daa5e-126">Blazor Server is a stateful framework.</span></span> <span data-ttu-id="daa5e-127">Gdy użytkownicy współpracują z aplikacją, utrzymują połączenie z serwerem znanym jako *obwód*.</span><span class="sxs-lookup"><span data-stu-id="daa5e-127">While users interact with an app, they maintain a connection to the server known as a *circuit*.</span></span> <span data-ttu-id="daa5e-128">Obwód zawiera aktywne wystąpienia składnika, a także wiele innych aspektów stanu, takich jak:</span><span class="sxs-lookup"><span data-stu-id="daa5e-128">The circuit holds active component instances, plus many other aspects of state, such as:</span></span>

* <span data-ttu-id="daa5e-129">Najnowsze renderowane dane wyjściowe składników.</span><span class="sxs-lookup"><span data-stu-id="daa5e-129">The most recent rendered output of components.</span></span>
* <span data-ttu-id="daa5e-130">Bieżący zestaw delegatów obsługi zdarzeń, które mogą być wyzwalane przez zdarzenia po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="daa5e-130">The current set of event-handling delegates that could be triggered by client-side events.</span></span>

<span data-ttu-id="daa5e-131">Jeśli użytkownik otworzy aplikację na wielu kartach przeglądarki, mają one wiele niezależnych obwodów.</span><span class="sxs-lookup"><span data-stu-id="daa5e-131">If a user opens the app in multiple browser tabs, they have multiple independent circuits.</span></span>

<span data-ttu-id="daa5e-132">Blazor traktuje większość nieobsłużonych wyjątków jako krytyczne dla obwodu, w którym występują.</span><span class="sxs-lookup"><span data-stu-id="daa5e-132">Blazor treats most unhandled exceptions as fatal to the circuit where they occur.</span></span> <span data-ttu-id="daa5e-133">Jeśli obwód zostanie przerwany z powodu nieobsługiwanego wyjątku, użytkownik może nadal korzystać z aplikacji tylko przez ponowne załadowanie strony w celu utworzenia nowego obwodu.</span><span class="sxs-lookup"><span data-stu-id="daa5e-133">If a circuit is terminated due to an unhandled exception, the user can only continue to interact with the app by reloading the page to create a new circuit.</span></span> <span data-ttu-id="daa5e-134">Nie ma to wpływu na obwody, które zostały przerwane, które są obwody dla innych użytkowników lub kart przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="daa5e-134">Circuits outside of the one that's terminated, which are circuits for other users or other browser tabs, aren't affected.</span></span> <span data-ttu-id="daa5e-135">Ten scenariusz jest podobny do aplikacji klasycznej, która ulega awarii.</span><span class="sxs-lookup"><span data-stu-id="daa5e-135">This scenario is similar to a desktop app that crashes.</span></span> <span data-ttu-id="daa5e-136">Aplikacja, która uległa awarii, musi zostać ponownie uruchomiona, ale nie ma to wpływu na inne aplikacje.</span><span class="sxs-lookup"><span data-stu-id="daa5e-136">The crashed app must be restarted, but other apps aren't affected.</span></span>

<span data-ttu-id="daa5e-137">Obwód jest zakończony, gdy wystąpił nieobsługiwany wyjątek z następujących powodów:</span><span class="sxs-lookup"><span data-stu-id="daa5e-137">A circuit is terminated when an unhandled exception occurs for the following reasons:</span></span>

* <span data-ttu-id="daa5e-138">Nieobsługiwany wyjątek często pozostawia obwód w niezdefiniowanym stanie.</span><span class="sxs-lookup"><span data-stu-id="daa5e-138">An unhandled exception often leaves the circuit in an undefined state.</span></span>
* <span data-ttu-id="daa5e-139">Nie można zagwarantować normalnej operacji aplikacji po wystąpieniu nieobsłużonego wyjątku.</span><span class="sxs-lookup"><span data-stu-id="daa5e-139">The app's normal operation can't be guaranteed after an unhandled exception.</span></span>
* <span data-ttu-id="daa5e-140">W przypadku kontynuowania obwodu w aplikacji mogą pojawić się luki w zabezpieczeniach.</span><span class="sxs-lookup"><span data-stu-id="daa5e-140">Security vulnerabilities may appear in the app if the circuit continues.</span></span>

## <a name="manage-unhandled-exceptions-in-developer-code"></a><span data-ttu-id="daa5e-141">Zarządzanie nieobsługiwanymi wyjątkami w kodzie dewelopera</span><span class="sxs-lookup"><span data-stu-id="daa5e-141">Manage unhandled exceptions in developer code</span></span>

<span data-ttu-id="daa5e-142">Aby aplikacja kontynuowała działanie po wystąpieniu błędu, aplikacja musi mieć logikę obsługi błędów.</span><span class="sxs-lookup"><span data-stu-id="daa5e-142">For an app to continue after an error, the app must have error handling logic.</span></span> <span data-ttu-id="daa5e-143">W dalszej części tego artykułu opisano potencjalne źródła nieobsłużonych wyjątków.</span><span class="sxs-lookup"><span data-stu-id="daa5e-143">Later sections of this article describe potential sources of unhandled exceptions.</span></span>

<span data-ttu-id="daa5e-144">W środowisku produkcyjnym nie Renderuj komunikatów wyjątków struktury ani śladów stosu w interfejsie użytkownika.</span><span class="sxs-lookup"><span data-stu-id="daa5e-144">In production, don't render framework exception messages or stack traces in the UI.</span></span> <span data-ttu-id="daa5e-145">Renderowanie komunikatów o wyjątkach lub śladów stosu może:</span><span class="sxs-lookup"><span data-stu-id="daa5e-145">Rendering exception messages or stack traces could:</span></span>

* <span data-ttu-id="daa5e-146">Ujawnianie poufnych informacji użytkownikom końcowym.</span><span class="sxs-lookup"><span data-stu-id="daa5e-146">Disclose sensitive information to end users.</span></span>
* <span data-ttu-id="daa5e-147">Pomóż złośliwemu użytkownikowi wykrywać słabe strony w aplikacji, która może naruszyć bezpieczeństwo aplikacji, serwera lub sieci.</span><span class="sxs-lookup"><span data-stu-id="daa5e-147">Help a malicious user discover weaknesses in an app that can compromise the security of the app, server, or network.</span></span>

## <a name="log-errors-with-a-persistent-provider"></a><span data-ttu-id="daa5e-148">Rejestrowanie błędów przez dostawcę trwałego</span><span class="sxs-lookup"><span data-stu-id="daa5e-148">Log errors with a persistent provider</span></span>

<span data-ttu-id="daa5e-149">Jeśli wystąpi nieobsługiwany wyjątek, wyjątek jest rejestrowany w <xref:Microsoft.Extensions.Logging.ILogger> wystąpieniach skonfigurowanych w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="daa5e-149">If an unhandled exception occurs, the exception is logged to <xref:Microsoft.Extensions.Logging.ILogger> instances configured in the service container.</span></span> <span data-ttu-id="daa5e-150">Domyślnie Blazor aplikacje rejestrują dane wyjściowe konsoli z dostawcą rejestrowania konsoli.</span><span class="sxs-lookup"><span data-stu-id="daa5e-150">By default, Blazor apps log to console output with the Console Logging Provider.</span></span> <span data-ttu-id="daa5e-151">Należy rozważyć logowanie do bardziej trwałej lokalizacji z dostawcą, który zarządza rozmiarem dziennika i rotacją dzienników.</span><span class="sxs-lookup"><span data-stu-id="daa5e-151">Consider logging to a more permanent location with a provider that manages log size and log rotation.</span></span> <span data-ttu-id="daa5e-152">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="daa5e-152">For more information, see <xref:fundamentals/logging/index>.</span></span>

<span data-ttu-id="daa5e-153">Podczas opracowywania programu Blazor zwykle program wysyła do konsoli przeglądarki pełne szczegóły dotyczące wyjątków, aby pomóc w debugowaniu.</span><span class="sxs-lookup"><span data-stu-id="daa5e-153">During development, Blazor usually sends the full details of exceptions to the browser's console to aid in debugging.</span></span> <span data-ttu-id="daa5e-154">W środowisku produkcyjnym szczegółowe błędy w konsoli przeglądarki są domyślnie wyłączone, co oznacza, że błędy nie są wysyłane do klientów, ale wszystkie szczegóły wyjątku nadal są rejestrowane po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="daa5e-154">In production, detailed errors in the browser's console are disabled by default, which means that errors aren't sent to clients but the exception's full details are still logged server-side.</span></span> <span data-ttu-id="daa5e-155">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="daa5e-155">For more information, see <xref:fundamentals/error-handling>.</span></span>

<span data-ttu-id="daa5e-156">Należy zdecydować, które zdarzenia mają być rejestrowane, oraz poziom ważności zarejestrowanych zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="daa5e-156">You must decide which incidents to log and the level of severity of logged incidents.</span></span> <span data-ttu-id="daa5e-157">Nieszkodliwi użytkownicy mogą być w stanie wyzwolić błędy w sposób celowy.</span><span class="sxs-lookup"><span data-stu-id="daa5e-157">Hostile users might be able to trigger errors deliberately.</span></span> <span data-ttu-id="daa5e-158">Na przykład nie należy rejestrować zdarzenia z błędu, gdy `ProductId` w adresie URL składnika, który zawiera szczegółowe informacje o produkcie.</span><span class="sxs-lookup"><span data-stu-id="daa5e-158">For example, don't log an incident from an error where an unknown `ProductId` is supplied in the URL of a component that displays product details.</span></span> <span data-ttu-id="daa5e-159">Nie wszystkie błędy powinny być traktowane jako zdarzenia o wysokiej ważności do rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="daa5e-159">Not all errors should be treated as high-severity incidents for logging.</span></span>

<span data-ttu-id="daa5e-160">Aby uzyskać więcej informacji, zobacz <xref:blazor/fundamentals/logging>.</span><span class="sxs-lookup"><span data-stu-id="daa5e-160">For more information, see <xref:blazor/fundamentals/logging>.</span></span>

## <a name="places-where-errors-may-occur"></a><span data-ttu-id="daa5e-161">Miejsca, w których mogą wystąpić błędy</span><span class="sxs-lookup"><span data-stu-id="daa5e-161">Places where errors may occur</span></span>

<span data-ttu-id="daa5e-162">Kod struktury i aplikacji może wyzwolić Nieobsłużone wyjątki w jednej z następujących lokalizacji:</span><span class="sxs-lookup"><span data-stu-id="daa5e-162">Framework and app code may trigger unhandled exceptions in any of the following locations:</span></span>

* [<span data-ttu-id="daa5e-163">Tworzenie wystąpienia składnika</span><span class="sxs-lookup"><span data-stu-id="daa5e-163">Component instantiation</span></span>](#component-instantiation)
* [<span data-ttu-id="daa5e-164">Metody cyklu życia</span><span class="sxs-lookup"><span data-stu-id="daa5e-164">Lifecycle methods</span></span>](#lifecycle-methods)
* [<span data-ttu-id="daa5e-165">Logika renderowania</span><span class="sxs-lookup"><span data-stu-id="daa5e-165">Rendering logic</span></span>](#rendering-logic)
* [<span data-ttu-id="daa5e-166">Procedury obsługi zdarzeń</span><span class="sxs-lookup"><span data-stu-id="daa5e-166">Event handlers</span></span>](#event-handlers)
* [<span data-ttu-id="daa5e-167">Usuwanie składników</span><span class="sxs-lookup"><span data-stu-id="daa5e-167">Component disposal</span></span>](#component-disposal)
* [<span data-ttu-id="daa5e-168">Międzyoperacyjność w języku JavaScript</span><span class="sxs-lookup"><span data-stu-id="daa5e-168">JavaScript interop</span></span>](#javascript-interop)
* [<span data-ttu-id="daa5e-169">Blazor Server Renderowanie</span><span class="sxs-lookup"><span data-stu-id="daa5e-169">Blazor Server rerendering</span></span>](#blazor-server-prerendering)

<span data-ttu-id="daa5e-170">Poprzednie Nieobsłużone wyjątki zostały opisane w poniższych sekcjach tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="daa5e-170">The preceding unhandled exceptions are described in the following sections of this article.</span></span>

### <a name="component-instantiation"></a><span data-ttu-id="daa5e-171">Tworzenie wystąpienia składnika</span><span class="sxs-lookup"><span data-stu-id="daa5e-171">Component instantiation</span></span>

<span data-ttu-id="daa5e-172">Podczas Blazor tworzenia wystąpienia składnika:</span><span class="sxs-lookup"><span data-stu-id="daa5e-172">When Blazor creates an instance of a component:</span></span>

* <span data-ttu-id="daa5e-173">Konstruktor składnika jest wywoływany.</span><span class="sxs-lookup"><span data-stu-id="daa5e-173">The component's constructor is invoked.</span></span>
* <span data-ttu-id="daa5e-174">Konstruktory wszelkich niepojedynczych usług DI dostarczonych do konstruktora składnika za pośrednictwem [`@inject`](xref:mvc/views/razor#inject) dyrektywy lub [`[Inject]`](xref:blazor/fundamentals/dependency-injection#request-a-service-in-a-component) atrybutu są wywoływane.</span><span class="sxs-lookup"><span data-stu-id="daa5e-174">The constructors of any non-singleton DI services supplied to the component's constructor via the [`@inject`](xref:mvc/views/razor#inject) directive or the [`[Inject]`](xref:blazor/fundamentals/dependency-injection#request-a-service-in-a-component) attribute are invoked.</span></span>

<span data-ttu-id="daa5e-175">Blazor ServerObwód kończy się niepowodzeniem, gdy dowolny wykonany Konstruktor lub setter dla każdej `[Inject]` właściwości zgłasza nieobsługiwany wyjątek.</span><span class="sxs-lookup"><span data-stu-id="daa5e-175">A Blazor Server circuit fails when any executed constructor or a setter for any `[Inject]` property throws an unhandled exception.</span></span> <span data-ttu-id="daa5e-176">Wyjątek jest krytyczny, ponieważ struktura nie może utworzyć wystąpienia składnika.</span><span class="sxs-lookup"><span data-stu-id="daa5e-176">The exception is fatal because the framework can't instantiate the component.</span></span> <span data-ttu-id="daa5e-177">Jeśli logika konstruktora może generować wyjątki, aplikacja powinna zalewkować wyjątki przy użyciu [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instrukcji z obsługą błędów i rejestrowaniem.</span><span class="sxs-lookup"><span data-stu-id="daa5e-177">If constructor logic may throw exceptions, the app should trap the exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

### <a name="lifecycle-methods"></a><span data-ttu-id="daa5e-178">Metody cyklu życia</span><span class="sxs-lookup"><span data-stu-id="daa5e-178">Lifecycle methods</span></span>

<span data-ttu-id="daa5e-179">W okresie istnienia składnika program Blazor wywołuje następujące [metody cyklu życia](xref:blazor/components/lifecycle):</span><span class="sxs-lookup"><span data-stu-id="daa5e-179">During the lifetime of a component, Blazor invokes the following [lifecycle methods](xref:blazor/components/lifecycle):</span></span>

* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>

<span data-ttu-id="daa5e-180">Jeśli jakakolwiek metoda cyklu życia zgłasza wyjątek, synchronicznie lub asynchronicznie, wyjątek jest krytyczny dla Blazor Server obwodu.</span><span class="sxs-lookup"><span data-stu-id="daa5e-180">If any lifecycle method throws an exception, synchronously or asynchronously, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="daa5e-181">Aby składniki zajmowały błędy w metodach cyklu życia, Dodaj logikę obsługi błędów.</span><span class="sxs-lookup"><span data-stu-id="daa5e-181">For components to deal with errors in lifecycle methods, add error handling logic.</span></span>

<span data-ttu-id="daa5e-182">W poniższym przykładzie, gdzie <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> wywołuje metodę w celu uzyskania produktu:</span><span class="sxs-lookup"><span data-stu-id="daa5e-182">In the following example where <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> calls a method to obtain a product:</span></span>

* <span data-ttu-id="daa5e-183">Wyjątek zgłoszony w `ProductRepository.GetProductByIdAsync` metodzie jest obsługiwany przez [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instrukcję.</span><span class="sxs-lookup"><span data-stu-id="daa5e-183">An exception thrown in the `ProductRepository.GetProductByIdAsync` method is handled by a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement.</span></span>
* <span data-ttu-id="daa5e-184">Gdy `catch` blok jest wykonywany:</span><span class="sxs-lookup"><span data-stu-id="daa5e-184">When the `catch` block is executed:</span></span>
  * <span data-ttu-id="daa5e-185">`loadFailed` jest ustawiona na `true` , która jest używana do wyświetlania komunikatu o błędzie dla użytkownika.</span><span class="sxs-lookup"><span data-stu-id="daa5e-185">`loadFailed` is set to `true`, which is used to display an error message to the user.</span></span>
  * <span data-ttu-id="daa5e-186">Błąd jest rejestrowany.</span><span class="sxs-lookup"><span data-stu-id="daa5e-186">The error is logged.</span></span>

[!code-razor[](handle-errors/samples_snapshot/3.x/product-details.razor?highlight=11,27-39)]

### <a name="rendering-logic"></a><span data-ttu-id="daa5e-187">Logika renderowania</span><span class="sxs-lookup"><span data-stu-id="daa5e-187">Rendering logic</span></span>

<span data-ttu-id="daa5e-188">Znaczniki deklaratywne w `.razor` pliku składnika są kompilowane do metody języka C# o nazwie <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> .</span><span class="sxs-lookup"><span data-stu-id="daa5e-188">The declarative markup in a `.razor` component file is compiled into a C# method called <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A>.</span></span> <span data-ttu-id="daa5e-189">Gdy składnik renderuje, <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> wykonuje i tworzy strukturę danych opisującą elementy, tekst i składniki podrzędne renderowanego składnika.</span><span class="sxs-lookup"><span data-stu-id="daa5e-189">When a component renders, <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> executes and builds up a data structure describing the elements, text, and child components of the rendered component.</span></span>

<span data-ttu-id="daa5e-190">Logika renderowania może zgłosić wyjątek.</span><span class="sxs-lookup"><span data-stu-id="daa5e-190">Rendering logic can throw an exception.</span></span> <span data-ttu-id="daa5e-191">Przykład tego scenariusza występuje, gdy `@someObject.PropertyName` jest oceniane, ale `@someObject` jest `null` .</span><span class="sxs-lookup"><span data-stu-id="daa5e-191">An example of this scenario occurs when `@someObject.PropertyName` is evaluated but `@someObject` is `null`.</span></span> <span data-ttu-id="daa5e-192">Nieobsługiwany wyjątek zgłoszony przez logikę renderowania jest krytyczny dla Blazor Server obwodu.</span><span class="sxs-lookup"><span data-stu-id="daa5e-192">An unhandled exception thrown by rendering logic is fatal to a Blazor Server circuit.</span></span>

<span data-ttu-id="daa5e-193">Aby zapobiec wystąpieniu wyjątku odwołania o wartości null w logice renderowania, `null` przed uzyskaniem dostępu do elementów członkowskich Sprawdź, czy jest on obiektem.</span><span class="sxs-lookup"><span data-stu-id="daa5e-193">To prevent a null reference exception in rendering logic, check for a `null` object before accessing its members.</span></span> <span data-ttu-id="daa5e-194">W poniższym przykładzie `person.Address` właściwości nie są dostępne, jeśli `person.Address` `null` :</span><span class="sxs-lookup"><span data-stu-id="daa5e-194">In the following example, `person.Address` properties aren't accessed if `person.Address` is `null`:</span></span>

[!code-razor[](handle-errors/samples_snapshot/3.x/person-example.razor?highlight=1)]

<span data-ttu-id="daa5e-195">Poprzedni kod założono, że `person` nie jest `null` .</span><span class="sxs-lookup"><span data-stu-id="daa5e-195">The preceding code assumes that `person` isn't `null`.</span></span> <span data-ttu-id="daa5e-196">Często Struktura kodu gwarantuje, że obiekt istnieje w momencie renderowania składnika.</span><span class="sxs-lookup"><span data-stu-id="daa5e-196">Often, the structure of the code guarantees that an object exists at the time the component is rendered.</span></span> <span data-ttu-id="daa5e-197">W takich przypadkach nie jest konieczne sprawdzanie `null` logiki renderowania.</span><span class="sxs-lookup"><span data-stu-id="daa5e-197">In those cases, it isn't necessary to check for `null` in rendering logic.</span></span> <span data-ttu-id="daa5e-198">W poprzednim przykładzie `person` można zagwarantować, że istnieje, ponieważ `person` jest tworzony podczas tworzenia wystąpienia składnika.</span><span class="sxs-lookup"><span data-stu-id="daa5e-198">In the prior example, `person` might be guaranteed to exist because `person` is created when the component is instantiated.</span></span>

### <a name="event-handlers"></a><span data-ttu-id="daa5e-199">Procedury obsługi zdarzeń</span><span class="sxs-lookup"><span data-stu-id="daa5e-199">Event handlers</span></span>

<span data-ttu-id="daa5e-200">Kod po stronie klienta wyzwala wywołania kodu C#, gdy programy obsługi zdarzeń są tworzone przy użyciu:</span><span class="sxs-lookup"><span data-stu-id="daa5e-200">Client-side code triggers invocations of C# code when event handlers are created using:</span></span>

* `@onclick`
* `@onchange`
* <span data-ttu-id="daa5e-201">Inne `@on...` atrybuty</span><span class="sxs-lookup"><span data-stu-id="daa5e-201">Other `@on...` attributes</span></span>
* `@bind`

<span data-ttu-id="daa5e-202">Kod procedury obsługi zdarzeń może zgłosić nieobsługiwany wyjątek w tych scenariuszach.</span><span class="sxs-lookup"><span data-stu-id="daa5e-202">Event handler code might throw an unhandled exception in these scenarios.</span></span>

<span data-ttu-id="daa5e-203">Jeśli procedura obsługi zdarzeń zgłasza nieobsługiwany wyjątek (na przykład kwerenda bazy danych kończy się niepowodzeniem), wyjątek jest krytyczny dla Blazor Server obwodu.</span><span class="sxs-lookup"><span data-stu-id="daa5e-203">If an event handler throws an unhandled exception (for example, a database query fails), the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="daa5e-204">Jeśli aplikacja wywołuje kod, który może zakończyć się niepowodzeniem z powodów zewnętrznych, należy zastosować wyjątek pułapki przy użyciu [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instrukcji z obsługą błędów i rejestrowaniem.</span><span class="sxs-lookup"><span data-stu-id="daa5e-204">If the app calls code that could fail for external reasons, trap exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="daa5e-205">Jeśli kod użytkownika nie jest pułapk i nie obsługuje wyjątku, struktura rejestruje wyjątek i kończy obwód.</span><span class="sxs-lookup"><span data-stu-id="daa5e-205">If user code doesn't trap and handle the exception, the framework logs the exception and terminates the circuit.</span></span>

### <a name="component-disposal"></a><span data-ttu-id="daa5e-206">Usuwanie składników</span><span class="sxs-lookup"><span data-stu-id="daa5e-206">Component disposal</span></span>

<span data-ttu-id="daa5e-207">Składnik może zostać usunięty z interfejsu użytkownika, na przykład, ponieważ użytkownik przeszedł do innej strony.</span><span class="sxs-lookup"><span data-stu-id="daa5e-207">A component may be removed from the UI, for example, because the user has navigated to another page.</span></span> <span data-ttu-id="daa5e-208">Gdy składnik implementujący <xref:System.IDisposable?displayProperty=fullName> jest usuwany z interfejsu użytkownika, struktura wywołuje <xref:System.IDisposable.Dispose%2A> metodę składnika.</span><span class="sxs-lookup"><span data-stu-id="daa5e-208">When a component that implements <xref:System.IDisposable?displayProperty=fullName> is removed from the UI, the framework calls the component's <xref:System.IDisposable.Dispose%2A> method.</span></span>

<span data-ttu-id="daa5e-209">Jeśli `Dispose` Metoda składnika zgłasza nieobsługiwany wyjątek, wyjątek jest krytyczny dla Blazor Server obwodu.</span><span class="sxs-lookup"><span data-stu-id="daa5e-209">If the component's `Dispose` method throws an unhandled exception, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="daa5e-210">Jeśli logika usuwania może generować wyjątki, aplikacja powinna zalewkować wyjątki przy użyciu [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instrukcji z obsługą błędów i rejestrowaniem.</span><span class="sxs-lookup"><span data-stu-id="daa5e-210">If disposal logic may throw exceptions, the app should trap the exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="daa5e-211">Aby uzyskać więcej informacji na temat usuwania składników, zobacz <xref:blazor/components/lifecycle#component-disposal-with-idisposable> .</span><span class="sxs-lookup"><span data-stu-id="daa5e-211">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

### <a name="javascript-interop"></a><span data-ttu-id="daa5e-212">Międzyoperacyjność w języku JavaScript</span><span class="sxs-lookup"><span data-stu-id="daa5e-212">JavaScript interop</span></span>

<span data-ttu-id="daa5e-213"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> umożliwia programowi .NET Code wykonywanie wywołań asynchronicznych do środowiska uruchomieniowego JavaScript w przeglądarce użytkownika.</span><span class="sxs-lookup"><span data-stu-id="daa5e-213"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> allows .NET code to make asynchronous calls to the JavaScript runtime in the user's browser.</span></span>

<span data-ttu-id="daa5e-214">Poniższe warunki dotyczą obsługi błędów w programie <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> :</span><span class="sxs-lookup"><span data-stu-id="daa5e-214">The following conditions apply to error handling with <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>:</span></span>

* <span data-ttu-id="daa5e-215">Jeśli wywołanie <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> synchronicznie zakończy się niepowodzeniem, wystąpi wyjątek programu .NET.</span><span class="sxs-lookup"><span data-stu-id="daa5e-215">If a call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> fails synchronously, a .NET exception occurs.</span></span> <span data-ttu-id="daa5e-216">Wywołanie <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> może zakończyć się niepowodzeniem, na przykład dlatego, że nie można serializować dostarczonych argumentów.</span><span class="sxs-lookup"><span data-stu-id="daa5e-216">A call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> may fail, for example, because the supplied arguments can't be serialized.</span></span> <span data-ttu-id="daa5e-217">Kod dewelopera musi przechwycić wyjątek.</span><span class="sxs-lookup"><span data-stu-id="daa5e-217">Developer code must catch the exception.</span></span> <span data-ttu-id="daa5e-218">Jeśli kod aplikacji w obsłudze zdarzeń lub metoda cyklu życia składnika nie obsługuje wyjątku, wynikający z nich wyjątek jest krytyczny dla Blazor Server obwodu.</span><span class="sxs-lookup"><span data-stu-id="daa5e-218">If app code in an event handler or component lifecycle method doesn't handle an exception, the resulting exception is fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="daa5e-219">Jeśli wywołanie <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> powiedzie się asynchronicznie, .NET <xref:System.Threading.Tasks.Task> kończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="daa5e-219">If a call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> fails asynchronously, the .NET <xref:System.Threading.Tasks.Task> fails.</span></span> <span data-ttu-id="daa5e-220">Wywołanie <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> może zakończyć się niepowodzeniem, na przykład ponieważ kod po stronie JavaScript zgłasza wyjątek lub zwraca `Promise` , który został ukończony jako `rejected` .</span><span class="sxs-lookup"><span data-stu-id="daa5e-220">A call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> may fail, for example, because the JavaScript-side code throws an exception or returns a `Promise` that completed as `rejected`.</span></span> <span data-ttu-id="daa5e-221">Kod dewelopera musi przechwycić wyjątek.</span><span class="sxs-lookup"><span data-stu-id="daa5e-221">Developer code must catch the exception.</span></span> <span data-ttu-id="daa5e-222">W przypadku użycia [`await`](/dotnet/csharp/language-reference/keywords/await) operatora Rozważ zapakowanie wywołania metody w [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instrukcji z obsługą błędów i rejestrowaniem.</span><span class="sxs-lookup"><span data-stu-id="daa5e-222">If using the [`await`](/dotnet/csharp/language-reference/keywords/await) operator, consider wrapping the method call in a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span> <span data-ttu-id="daa5e-223">W przeciwnym razie niepowodzenie kodu spowoduje nieobsłużony wyjątek, który jest krytyczny dla Blazor Server obwodu.</span><span class="sxs-lookup"><span data-stu-id="daa5e-223">Otherwise, the failing code results in an unhandled exception that's fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="daa5e-224">Domyślnie wywołania programu <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> muszą zakończyć się w określonym przedziale czasu lub w przeciwnym razie upłynął limit czasu połączenia. Domyślny limit czasu wynosi jedną minutę.</span><span class="sxs-lookup"><span data-stu-id="daa5e-224">By default, calls to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> must complete within a certain period or else the call times out. The default timeout period is one minute.</span></span> <span data-ttu-id="daa5e-225">Limit czasu chroni kod przed utratą połączenia sieciowego lub kodem JavaScript, który nigdy nie odsyła komunikat uzupełniający.</span><span class="sxs-lookup"><span data-stu-id="daa5e-225">The timeout protects the code against a loss in network connectivity or JavaScript code that never sends back a completion message.</span></span> <span data-ttu-id="daa5e-226">Jeśli wystąpiło przełączenie, wynikiem <xref:System.Threading.Tasks> kończy się niepowodzeniem a <xref:System.OperationCanceledException> .</span><span class="sxs-lookup"><span data-stu-id="daa5e-226">If the call times out, the resulting <xref:System.Threading.Tasks> fails with an <xref:System.OperationCanceledException>.</span></span> <span data-ttu-id="daa5e-227">Zalewka i przetwórz wyjątek z rejestrowaniem.</span><span class="sxs-lookup"><span data-stu-id="daa5e-227">Trap and process the exception with logging.</span></span>

<span data-ttu-id="daa5e-228">Podobnie kod JavaScript może inicjować wywołania metod .NET wskazywanych przez [`[JSInvokable]`](xref:blazor/call-dotnet-from-javascript) atrybut.</span><span class="sxs-lookup"><span data-stu-id="daa5e-228">Similarly, JavaScript code may initiate calls to .NET methods indicated by the [`[JSInvokable]`](xref:blazor/call-dotnet-from-javascript) attribute.</span></span> <span data-ttu-id="daa5e-229">Jeśli te metody .NET zgłaszają nieobsługiwany wyjątek:</span><span class="sxs-lookup"><span data-stu-id="daa5e-229">If these .NET methods throw an unhandled exception:</span></span>

* <span data-ttu-id="daa5e-230">Wyjątek nie jest traktowany jako krytyczny dla Blazor Server obwodu.</span><span class="sxs-lookup"><span data-stu-id="daa5e-230">The exception isn't treated as fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="daa5e-231">Po stronie JavaScript `Promise` jest odrzucany.</span><span class="sxs-lookup"><span data-stu-id="daa5e-231">The JavaScript-side `Promise` is rejected.</span></span>

<span data-ttu-id="daa5e-232">Istnieje możliwość użycia kodu obsługi błędów po stronie .NET lub stronie JavaScript wywołania metody.</span><span class="sxs-lookup"><span data-stu-id="daa5e-232">You have the option of using error handling code on either the .NET side or the JavaScript side of the method call.</span></span>

<span data-ttu-id="daa5e-233">Aby uzyskać więcej informacji, zobacz następujące artykuły:</span><span class="sxs-lookup"><span data-stu-id="daa5e-233">For more information, see the following articles:</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

### <a name="no-locblazor-server-prerendering"></a><span data-ttu-id="daa5e-234">Blazor Server Renderowanie prerenderingu</span><span class="sxs-lookup"><span data-stu-id="daa5e-234">Blazor Server prerendering</span></span>

<span data-ttu-id="daa5e-235">Blazor składniki mogą być wstępnie renderowane przy użyciu [pomocnika tagów składnika](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) , dzięki czemu RENDEROWANE znaczniki HTML są zwracane jako część początkowego żądania HTTP użytkownika.</span><span class="sxs-lookup"><span data-stu-id="daa5e-235">Blazor components can be prerendered using the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) so that their rendered HTML markup is returned as part of the user's initial HTTP request.</span></span> <span data-ttu-id="daa5e-236">Działa to w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="daa5e-236">This works by:</span></span>

* <span data-ttu-id="daa5e-237">Tworzenie nowego obwodu dla wszystkich wstępnie renderowanych składników, które są częścią tej samej strony.</span><span class="sxs-lookup"><span data-stu-id="daa5e-237">Creating a new circuit for all of the prerendered components that are part of the same page.</span></span>
* <span data-ttu-id="daa5e-238">Generowanie początkowego kodu HTML.</span><span class="sxs-lookup"><span data-stu-id="daa5e-238">Generating the initial HTML.</span></span>
* <span data-ttu-id="daa5e-239">Przetraktowanie obwodu `disconnected` do momentu, aż przeglądarka użytkownika nawiąże SignalR połączenie z powrotem z tym samym serwerem.</span><span class="sxs-lookup"><span data-stu-id="daa5e-239">Treating the circuit as `disconnected` until the user's browser establishes a SignalR connection back to the same server.</span></span> <span data-ttu-id="daa5e-240">Po nawiązaniu połączenia zostanie wznowione działanie międzydziałające w obwodzie i zostanie zaktualizowane oznaczenie HTML składników.</span><span class="sxs-lookup"><span data-stu-id="daa5e-240">When the connection is established, interactivity on the circuit is resumed and the components' HTML markup is updated.</span></span>

<span data-ttu-id="daa5e-241">Jeśli jakikolwiek składnik zgłasza nieobsłużony wyjątek podczas renderowania pre, na przykład podczas wykonywania metody cyklu życia lub logiki renderowania:</span><span class="sxs-lookup"><span data-stu-id="daa5e-241">If any component throws an unhandled exception during prerendering, for example, during a lifecycle method or in rendering logic:</span></span>

* <span data-ttu-id="daa5e-242">Wyjątek jest krytyczny dla obwodu.</span><span class="sxs-lookup"><span data-stu-id="daa5e-242">The exception is fatal to the circuit.</span></span>
* <span data-ttu-id="daa5e-243">Wyjątek jest generowany w stosie wywołań z <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> pomocnika tagów.</span><span class="sxs-lookup"><span data-stu-id="daa5e-243">The exception is thrown up the call stack from the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper.</span></span> <span data-ttu-id="daa5e-244">W związku z tym całe żądanie HTTP kończy się niepowodzeniem, chyba że wyjątek jest jawnie przechwycony przez kod dewelopera.</span><span class="sxs-lookup"><span data-stu-id="daa5e-244">Therefore, the entire HTTP request fails unless the exception is explicitly caught by developer code.</span></span>

<span data-ttu-id="daa5e-245">W normalnych warunkach w przypadku niepowodzenia wstępnego renderowania kontynuowanie kompilowania i renderowania składnika nie ma sensu, ponieważ nie można renderować składnika roboczego.</span><span class="sxs-lookup"><span data-stu-id="daa5e-245">Under normal circumstances when prerendering fails, continuing to build and render the component doesn't make sense because a working component can't be rendered.</span></span>

<span data-ttu-id="daa5e-246">Aby tolerować błędy, które mogą wystąpić podczas renderowania prerenderingu, logika obsługi błędów musi być umieszczona wewnątrz składnika, który może zgłaszać wyjątki.</span><span class="sxs-lookup"><span data-stu-id="daa5e-246">To tolerate errors that may occur during prerendering, error handling logic must be placed inside a component that may throw exceptions.</span></span> <span data-ttu-id="daa5e-247">Używaj [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instrukcji z obsługą błędów i rejestrowaniem.</span><span class="sxs-lookup"><span data-stu-id="daa5e-247">Use [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span> <span data-ttu-id="daa5e-248">Zamiast zawijać <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> pomocnika tagów w [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) instrukcji, umieść logikę obsługi błędów w składniku renderowanym przez <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> pomocnika tagów.</span><span class="sxs-lookup"><span data-stu-id="daa5e-248">Instead of wrapping the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper in a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement, place error handling logic in the component rendered by the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper.</span></span>

## <a name="advanced-scenarios"></a><span data-ttu-id="daa5e-249">Scenariusze zaawansowane</span><span class="sxs-lookup"><span data-stu-id="daa5e-249">Advanced scenarios</span></span>

### <a name="recursive-rendering"></a><span data-ttu-id="daa5e-250">Renderowanie cykliczne</span><span class="sxs-lookup"><span data-stu-id="daa5e-250">Recursive rendering</span></span>

<span data-ttu-id="daa5e-251">Składniki można cyklicznie zagnieżdżać.</span><span class="sxs-lookup"><span data-stu-id="daa5e-251">Components can be nested recursively.</span></span> <span data-ttu-id="daa5e-252">Jest to przydatne do reprezentowania struktur danych rekursywnych.</span><span class="sxs-lookup"><span data-stu-id="daa5e-252">This is useful for representing recursive data structures.</span></span> <span data-ttu-id="daa5e-253">Na przykład `TreeNode` składnik może renderować więcej `TreeNode` składników dla każdego węzła podrzędnego.</span><span class="sxs-lookup"><span data-stu-id="daa5e-253">For example, a `TreeNode` component can render more `TreeNode` components for each of the node's children.</span></span>

<span data-ttu-id="daa5e-254">W przypadku renderowania cyklicznego należy unikać tworzenia wzorców, które powodują nieskończoną rekursję:</span><span class="sxs-lookup"><span data-stu-id="daa5e-254">When rendering recursively, avoid coding patterns that result in infinite recursion:</span></span>

* <span data-ttu-id="daa5e-255">Nie Renderuj rekursywnie struktury danych zawierającej cykl.</span><span class="sxs-lookup"><span data-stu-id="daa5e-255">Don't recursively render a data structure that contains a cycle.</span></span> <span data-ttu-id="daa5e-256">Na przykład nie Renderuj węzła drzewa, którego elementy podrzędne zawierają sam siebie.</span><span class="sxs-lookup"><span data-stu-id="daa5e-256">For example, don't render a tree node whose children includes itself.</span></span>
* <span data-ttu-id="daa5e-257">Nie twórz łańcucha układów zawierających cykl.</span><span class="sxs-lookup"><span data-stu-id="daa5e-257">Don't create a chain of layouts that contain a cycle.</span></span> <span data-ttu-id="daa5e-258">Na przykład nie twórz układu, którego układ jest sam.</span><span class="sxs-lookup"><span data-stu-id="daa5e-258">For example, don't create a layout whose layout is itself.</span></span>
* <span data-ttu-id="daa5e-259">Nie Zezwalaj użytkownikowi końcowemu na naruszenie nieodmian rekursji (reguł) przy użyciu złośliwego wpisu danych lub wywołań międzyoperacyjnych języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="daa5e-259">Don't allow an end user to violate recursion invariants (rules) through malicious data entry or JavaScript interop calls.</span></span>

<span data-ttu-id="daa5e-260">Nieskończone pętle podczas renderowania:</span><span class="sxs-lookup"><span data-stu-id="daa5e-260">Infinite loops during rendering:</span></span>

* <span data-ttu-id="daa5e-261">Powoduje, że proces renderowania kontynuuje działanie zawsze.</span><span class="sxs-lookup"><span data-stu-id="daa5e-261">Causes the rendering process to continue forever.</span></span>
* <span data-ttu-id="daa5e-262">Jest równoznaczny z tworzeniem niezakończonej pętli.</span><span class="sxs-lookup"><span data-stu-id="daa5e-262">Is equivalent to creating an unterminated loop.</span></span>

<span data-ttu-id="daa5e-263">W tych scenariuszach obwód, którego to dotyczy, Blazor Server kończy się niepowodzeniem, a wątek zwykle próbuje wykonać:</span><span class="sxs-lookup"><span data-stu-id="daa5e-263">In these scenarios, an affected Blazor Server circuit fails, and the thread usually attempts to:</span></span>

* <span data-ttu-id="daa5e-264">Zużywaj ilość czasu procesora CPU dozwoloną przez system operacyjny w nieskończoność.</span><span class="sxs-lookup"><span data-stu-id="daa5e-264">Consume as much CPU time as permitted by the operating system, indefinitely.</span></span>
* <span data-ttu-id="daa5e-265">Korzystaj z nieograniczonej ilości pamięci serwera.</span><span class="sxs-lookup"><span data-stu-id="daa5e-265">Consume an unlimited amount of server memory.</span></span> <span data-ttu-id="daa5e-266">Zużywanie nieograniczonej pamięci jest równoważne scenariuszowi, w którym niezakończona pętla dodaje wpisy do kolekcji na każdej iteracji.</span><span class="sxs-lookup"><span data-stu-id="daa5e-266">Consuming unlimited memory is equivalent to the scenario where an unterminated loop adds entries to a collection on every iteration.</span></span>

<span data-ttu-id="daa5e-267">Aby uniknąć nieskończonych wzorców rekursji, należy się upewnić, że kod renderowania cyklicznego zawiera odpowiednie warunki zatrzymania.</span><span class="sxs-lookup"><span data-stu-id="daa5e-267">To avoid infinite recursion patterns, ensure that recursive rendering code contains suitable stopping conditions.</span></span>

### <a name="custom-render-tree-logic"></a><span data-ttu-id="daa5e-268">Logika drzewa renderowania niestandardowego</span><span class="sxs-lookup"><span data-stu-id="daa5e-268">Custom render tree logic</span></span>

<span data-ttu-id="daa5e-269">Większość Blazor składników jest zaimplementowana jako `.razor` pliki i są kompilowane do tworzenia logiki, która działa w <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> celu renderowania danych wyjściowych.</span><span class="sxs-lookup"><span data-stu-id="daa5e-269">Most Blazor components are implemented as `.razor` files and are compiled to produce logic that operates on a <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> to render their output.</span></span> <span data-ttu-id="daa5e-270">Deweloper może ręcznie zaimplementować <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logikę przy użyciu procedury kodu w języku C#.</span><span class="sxs-lookup"><span data-stu-id="daa5e-270">A developer may manually implement <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic using procedural C# code.</span></span> <span data-ttu-id="daa5e-271">Aby uzyskać więcej informacji, zobacz <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span><span class="sxs-lookup"><span data-stu-id="daa5e-271">For more information, see <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span></span>

> [!WARNING]
> <span data-ttu-id="daa5e-272">Korzystanie z logiki konstruktora drzewa renderowania ręcznego jest uznawane za zaawansowane i niebezpieczne scenariusze, które nie są zalecane do ogólnego tworzenia składników.</span><span class="sxs-lookup"><span data-stu-id="daa5e-272">Use of manual render tree builder logic is considered an advanced and unsafe scenario, not recommended for general component development.</span></span>

<span data-ttu-id="daa5e-273">Jeśli <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> kod zostanie zapisany, Deweloper musi zagwarantować poprawność kodu.</span><span class="sxs-lookup"><span data-stu-id="daa5e-273">If <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> code is written, the developer must guarantee the correctness of the code.</span></span> <span data-ttu-id="daa5e-274">Na przykład Deweloper musi upewnić się, że:</span><span class="sxs-lookup"><span data-stu-id="daa5e-274">For example, the developer must ensure that:</span></span>

* <span data-ttu-id="daa5e-275">Wywołania <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> i <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A> są prawidłowo zrównoważone.</span><span class="sxs-lookup"><span data-stu-id="daa5e-275">Calls to <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> and <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A> are correctly balanced.</span></span>
* <span data-ttu-id="daa5e-276">Atrybuty są dodawane tylko w prawidłowych miejscach.</span><span class="sxs-lookup"><span data-stu-id="daa5e-276">Attributes are only added in the correct places.</span></span>

<span data-ttu-id="daa5e-277">Nieprawidłowa ręczna logika konstruktora drzewa renderowania może spowodować dowolne niezdefiniowane zachowanie, w tym awarie, zawieszenie serwera i luki w zabezpieczeniach.</span><span class="sxs-lookup"><span data-stu-id="daa5e-277">Incorrect manual render tree builder logic can cause arbitrary undefined behavior, including crashes, server hangs, and security vulnerabilities.</span></span>

<span data-ttu-id="daa5e-278">Rozważ ręczne renderowanie logiki konstruktora drzew drzewa na tym samym poziomie złożoności i z tym samym poziomem *zagrożenia* co ręczne pisanie kodu zestawu lub instrukcji MSIL.</span><span class="sxs-lookup"><span data-stu-id="daa5e-278">Consider manual render tree builder logic on the same level of complexity and with the same level of *danger* as writing assembly code or MSIL instructions by hand.</span></span>
