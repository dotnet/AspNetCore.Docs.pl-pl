---
title: Obsługa błędów w aplikacjach Blazor ASP.NET Core
author: guardrex
description: Odkryj, jak Blazor ASP.NET Core Blazor jak program zarządza nieobsługiwanymi wyjątkami i jak opracowywać aplikacje wykrywające i obsługujące błędy.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: blazor/handle-errors
ms.openlocfilehash: 63bb791958785fa9a4a676f1aab79126c6873068
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111048"
---
# <a name="handle-errors-in-aspnet-core-opno-locblazor-apps"></a><span data-ttu-id="73d59-103">Obsługa błędów w aplikacjach Blazor ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="73d59-103">Handle errors in ASP.NET Core Blazor apps</span></span>

<span data-ttu-id="73d59-104">[Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="73d59-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="73d59-105">W tym artykule opisano Blazor , jak zarządzać nieobsługiwanymi wyjątkami oraz jak opracowywać aplikacje wykrywające i obsługujące błędy.</span><span class="sxs-lookup"><span data-stu-id="73d59-105">This article describes how Blazor manages unhandled exceptions and how to develop apps that detect and handle errors.</span></span>

## <a name="detailed-errors-during-development"></a><span data-ttu-id="73d59-106">Szczegóły błędów podczas opracowywania</span><span class="sxs-lookup"><span data-stu-id="73d59-106">Detailed errors during development</span></span>

<span data-ttu-id="73d59-107">Gdy Blazor aplikacja nie działa prawidłowo podczas opracowywania, otrzymuje szczegółowe informacje o błędzie z aplikacji, które pomagają w rozwiązywaniu problemów i rozwiązaniu problemu.</span><span class="sxs-lookup"><span data-stu-id="73d59-107">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="73d59-108">Gdy wystąpi błąd, Blazor aplikacje wyświetlają złoty pasek u dołu ekranu:</span><span class="sxs-lookup"><span data-stu-id="73d59-108">When an error occurs, Blazor apps display a gold bar at the bottom of the screen:</span></span>

* <span data-ttu-id="73d59-109">W trakcie programowania złoty pasek kieruje użytkownika do konsoli przeglądarki, gdzie można zobaczyć wyjątek.</span><span class="sxs-lookup"><span data-stu-id="73d59-109">During development, the gold bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="73d59-110">W środowisku produkcyjnym złoty pasek powiadamia użytkownika o wystąpieniu błędu i zaleca odświeżenie przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="73d59-110">In production, the gold bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="73d59-111">Interfejs użytkownika dla tego środowiska obsługi błędów jest częścią szablonów Blazor projektu.</span><span class="sxs-lookup"><span data-stu-id="73d59-111">The UI for this error handling experience is part of the Blazor project templates.</span></span>

<span data-ttu-id="73d59-112">Blazor W aplikacji webassembly Dostosuj środowisko w pliku *wwwroot/index.html* :</span><span class="sxs-lookup"><span data-stu-id="73d59-112">In a Blazor WebAssembly app, customize the experience in the *wwwroot/index.html* file:</span></span>

```html
<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="73d59-113">W aplikacji Blazor serwera Dostosuj środowisko w pliku *pages/_Host. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="73d59-113">In a Blazor Server app, customize the experience in the *Pages/_Host.cshtml* file:</span></span>

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

<span data-ttu-id="73d59-114">`blazor-error-ui` Element jest ukryty przez style zawarte w Blazor szablonach (*wwwroot/CSS/site. css*), a następnie pokazywany w przypadku wystąpienia błędu:</span><span class="sxs-lookup"><span data-stu-id="73d59-114">The `blazor-error-ui` element is hidden by the styles included in the Blazor templates (*wwwroot/css/site.css*) and then shown when an error occurs:</span></span>

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

## <a name="how-a-opno-locblazor-server-app-reacts-to-unhandled-exceptions"></a><span data-ttu-id="73d59-115">Jak aplikacja Blazor serwera reaguje na Nieobsłużone wyjątki</span><span class="sxs-lookup"><span data-stu-id="73d59-115">How a Blazor Server app reacts to unhandled exceptions</span></span>

Blazor<span data-ttu-id="73d59-116">Serwer jest strukturą stanową.</span><span class="sxs-lookup"><span data-stu-id="73d59-116"> Server is a stateful framework.</span></span> <span data-ttu-id="73d59-117">Gdy użytkownicy współpracują z aplikacją, utrzymują połączenie z serwerem znanym jako *obwód*.</span><span class="sxs-lookup"><span data-stu-id="73d59-117">While users interact with an app, they maintain a connection to the server known as a *circuit*.</span></span> <span data-ttu-id="73d59-118">Obwód zawiera aktywne wystąpienia składnika, a także wiele innych aspektów stanu, takich jak:</span><span class="sxs-lookup"><span data-stu-id="73d59-118">The circuit holds active component instances, plus many other aspects of state, such as:</span></span>

* <span data-ttu-id="73d59-119">Najnowsze renderowane dane wyjściowe składników.</span><span class="sxs-lookup"><span data-stu-id="73d59-119">The most recent rendered output of components.</span></span>
* <span data-ttu-id="73d59-120">Bieżący zestaw delegatów obsługi zdarzeń, które mogą być wyzwalane przez zdarzenia po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="73d59-120">The current set of event-handling delegates that could be triggered by client-side events.</span></span>

<span data-ttu-id="73d59-121">Jeśli użytkownik otworzy aplikację na wielu kartach przeglądarki, mają one wiele niezależnych obwodów.</span><span class="sxs-lookup"><span data-stu-id="73d59-121">If a user opens the app in multiple browser tabs, they have multiple independent circuits.</span></span>

Blazor<span data-ttu-id="73d59-122">traktuje większość nieobsłużonych wyjątków jako krytyczne dla obwodu, w którym występują.</span><span class="sxs-lookup"><span data-stu-id="73d59-122"> treats most unhandled exceptions as fatal to the circuit where they occur.</span></span> <span data-ttu-id="73d59-123">Jeśli obwód zostanie przerwany z powodu nieobsługiwanego wyjątku, użytkownik może nadal korzystać z aplikacji tylko przez ponowne załadowanie strony w celu utworzenia nowego obwodu.</span><span class="sxs-lookup"><span data-stu-id="73d59-123">If a circuit is terminated due to an unhandled exception, the user can only continue to interact with the app by reloading the page to create a new circuit.</span></span> <span data-ttu-id="73d59-124">Nie ma to wpływu na obwody, które zostały przerwane, które są obwody dla innych użytkowników lub kart przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="73d59-124">Circuits outside of the one that's terminated, which are circuits for other users or other browser tabs, aren't affected.</span></span> <span data-ttu-id="73d59-125">Ten scenariusz jest podobny do aplikacji klasycznej,&mdash;w której awaria aplikacji musi zostać uruchomiona ponownie, ale nie ma to wpływu na inne aplikacje.</span><span class="sxs-lookup"><span data-stu-id="73d59-125">This scenario is similar to a desktop app that crashes&mdash;the crashed app must be restarted, but other apps aren't affected.</span></span>

<span data-ttu-id="73d59-126">Obwód jest zakończony, gdy wystąpił nieobsługiwany wyjątek z następujących powodów:</span><span class="sxs-lookup"><span data-stu-id="73d59-126">A circuit is terminated when an unhandled exception occurs for the following reasons:</span></span>

* <span data-ttu-id="73d59-127">Nieobsługiwany wyjątek często pozostawia obwód w niezdefiniowanym stanie.</span><span class="sxs-lookup"><span data-stu-id="73d59-127">An unhandled exception often leaves the circuit in an undefined state.</span></span>
* <span data-ttu-id="73d59-128">Nie można zagwarantować normalnej operacji aplikacji po wystąpieniu nieobsłużonego wyjątku.</span><span class="sxs-lookup"><span data-stu-id="73d59-128">The app's normal operation can't be guaranteed after an unhandled exception.</span></span>
* <span data-ttu-id="73d59-129">W przypadku kontynuowania obwodu w aplikacji mogą pojawić się luki w zabezpieczeniach.</span><span class="sxs-lookup"><span data-stu-id="73d59-129">Security vulnerabilities may appear in the app if the circuit continues.</span></span>

## <a name="manage-unhandled-exceptions-in-developer-code"></a><span data-ttu-id="73d59-130">Zarządzanie nieobsługiwanymi wyjątkami w kodzie dewelopera</span><span class="sxs-lookup"><span data-stu-id="73d59-130">Manage unhandled exceptions in developer code</span></span>

<span data-ttu-id="73d59-131">Aby aplikacja kontynuowała działanie po wystąpieniu błędu, aplikacja musi mieć logikę obsługi błędów.</span><span class="sxs-lookup"><span data-stu-id="73d59-131">For an app to continue after an error, the app must have error handling logic.</span></span> <span data-ttu-id="73d59-132">W dalszej części tego artykułu opisano potencjalne źródła nieobsłużonych wyjątków.</span><span class="sxs-lookup"><span data-stu-id="73d59-132">Later sections of this article describe potential sources of unhandled exceptions.</span></span>

<span data-ttu-id="73d59-133">W środowisku produkcyjnym nie Renderuj komunikatów wyjątków struktury ani śladów stosu w interfejsie użytkownika.</span><span class="sxs-lookup"><span data-stu-id="73d59-133">In production, don't render framework exception messages or stack traces in the UI.</span></span> <span data-ttu-id="73d59-134">Renderowanie komunikatów o wyjątkach lub śladów stosu może:</span><span class="sxs-lookup"><span data-stu-id="73d59-134">Rendering exception messages or stack traces could:</span></span>

* <span data-ttu-id="73d59-135">Ujawnianie poufnych informacji użytkownikom końcowym.</span><span class="sxs-lookup"><span data-stu-id="73d59-135">Disclose sensitive information to end users.</span></span>
* <span data-ttu-id="73d59-136">Pomóż złośliwemu użytkownikowi wykrywać słabe strony w aplikacji, która może naruszyć bezpieczeństwo aplikacji, serwera lub sieci.</span><span class="sxs-lookup"><span data-stu-id="73d59-136">Help a malicious user discover weaknesses in an app that can compromise the security of the app, server, or network.</span></span>

## <a name="log-errors-with-a-persistent-provider"></a><span data-ttu-id="73d59-137">Rejestrowanie błędów przez dostawcę trwałego</span><span class="sxs-lookup"><span data-stu-id="73d59-137">Log errors with a persistent provider</span></span>

<span data-ttu-id="73d59-138">Jeśli wystąpi nieobsługiwany wyjątek, wyjątek jest rejestrowany w <xref:Microsoft.Extensions.Logging.ILogger> wystąpieniach skonfigurowanych w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="73d59-138">If an unhandled exception occurs, the exception is logged to <xref:Microsoft.Extensions.Logging.ILogger> instances configured in the service container.</span></span> <span data-ttu-id="73d59-139">Domyślnie Blazor aplikacje rejestrują dane wyjściowe konsoli z dostawcą rejestrowania konsoli.</span><span class="sxs-lookup"><span data-stu-id="73d59-139">By default, Blazor apps log to console output with the Console Logging Provider.</span></span> <span data-ttu-id="73d59-140">Należy rozważyć logowanie do bardziej trwałej lokalizacji z dostawcą, który zarządza rozmiarem dziennika i rotacją dzienników.</span><span class="sxs-lookup"><span data-stu-id="73d59-140">Consider logging to a more permanent location with a provider that manages log size and log rotation.</span></span> <span data-ttu-id="73d59-141">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="73d59-141">For more information, see <xref:fundamentals/logging/index>.</span></span>

<span data-ttu-id="73d59-142">Podczas opracowywania Blazor programu zwykle program wysyła do konsoli przeglądarki pełne szczegóły dotyczące wyjątków, aby pomóc w debugowaniu.</span><span class="sxs-lookup"><span data-stu-id="73d59-142">During development, Blazor usually sends the full details of exceptions to the browser's console to aid in debugging.</span></span> <span data-ttu-id="73d59-143">W środowisku produkcyjnym szczegółowe błędy w konsoli przeglądarki są domyślnie wyłączone, co oznacza, że błędy nie są wysyłane do klientów, ale wszystkie szczegóły wyjątku nadal są rejestrowane po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="73d59-143">In production, detailed errors in the browser's console are disabled by default, which means that errors aren't sent to clients but the exception's full details are still logged server-side.</span></span> <span data-ttu-id="73d59-144">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="73d59-144">For more information, see <xref:fundamentals/error-handling>.</span></span>

<span data-ttu-id="73d59-145">Należy zdecydować, które zdarzenia mają być rejestrowane, oraz poziom ważności zarejestrowanych zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="73d59-145">You must decide which incidents to log and the level of severity of logged incidents.</span></span> <span data-ttu-id="73d59-146">Nieszkodliwi użytkownicy mogą być w stanie wyzwolić błędy w sposób celowy.</span><span class="sxs-lookup"><span data-stu-id="73d59-146">Hostile users might be able to trigger errors deliberately.</span></span> <span data-ttu-id="73d59-147">Na przykład nie należy rejestrować zdarzenia z błędu, gdy `ProductId` w adresie URL składnika, który zawiera szczegółowe informacje o produkcie.</span><span class="sxs-lookup"><span data-stu-id="73d59-147">For example, don't log an incident from an error where an unknown `ProductId` is supplied in the URL of a component that displays product details.</span></span> <span data-ttu-id="73d59-148">Nie wszystkie błędy powinny być traktowane jako zdarzenia o wysokiej ważności do rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="73d59-148">Not all errors should be treated as high-severity incidents for logging.</span></span>

<span data-ttu-id="73d59-149">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/logging/index#create-logs-in-blazor>.</span><span class="sxs-lookup"><span data-stu-id="73d59-149">For more information, see <xref:fundamentals/logging/index#create-logs-in-blazor>.</span></span>

## <a name="places-where-errors-may-occur"></a><span data-ttu-id="73d59-150">Miejsca, w których mogą wystąpić błędy</span><span class="sxs-lookup"><span data-stu-id="73d59-150">Places where errors may occur</span></span>

<span data-ttu-id="73d59-151">Kod struktury i aplikacji może wyzwolić Nieobsłużone wyjątki w jednej z następujących lokalizacji:</span><span class="sxs-lookup"><span data-stu-id="73d59-151">Framework and app code may trigger unhandled exceptions in any of the following locations:</span></span>

* [<span data-ttu-id="73d59-152">Tworzenie wystąpienia składnika</span><span class="sxs-lookup"><span data-stu-id="73d59-152">Component instantiation</span></span>](#component-instantiation)
* [<span data-ttu-id="73d59-153">Metody cyklu życia</span><span class="sxs-lookup"><span data-stu-id="73d59-153">Lifecycle methods</span></span>](#lifecycle-methods)
* [<span data-ttu-id="73d59-154">Logika renderowania</span><span class="sxs-lookup"><span data-stu-id="73d59-154">Rendering logic</span></span>](#rendering-logic)
* [<span data-ttu-id="73d59-155">Procedury obsługi zdarzeń</span><span class="sxs-lookup"><span data-stu-id="73d59-155">Event handlers</span></span>](#event-handlers)
* [<span data-ttu-id="73d59-156">Usuwanie składników</span><span class="sxs-lookup"><span data-stu-id="73d59-156">Component disposal</span></span>](#component-disposal)
* [<span data-ttu-id="73d59-157">Międzyoperacyjność w języku JavaScript</span><span class="sxs-lookup"><span data-stu-id="73d59-157">JavaScript interop</span></span>](#javascript-interop)
* <span data-ttu-id="73d59-158">[BlazorRenderowanie serwera](#blazor-server-prerendering)</span><span class="sxs-lookup"><span data-stu-id="73d59-158">[Blazor Server rerendering](#blazor-server-prerendering)</span></span>

<span data-ttu-id="73d59-159">Poprzednie Nieobsłużone wyjątki zostały opisane w poniższych sekcjach tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="73d59-159">The preceding unhandled exceptions are described in the following sections of this article.</span></span>

### <a name="component-instantiation"></a><span data-ttu-id="73d59-160">Tworzenie wystąpienia składnika</span><span class="sxs-lookup"><span data-stu-id="73d59-160">Component instantiation</span></span>

<span data-ttu-id="73d59-161">Podczas Blazor tworzenia wystąpienia składnika:</span><span class="sxs-lookup"><span data-stu-id="73d59-161">When Blazor creates an instance of a component:</span></span>

* <span data-ttu-id="73d59-162">Konstruktor składnika jest wywoływany.</span><span class="sxs-lookup"><span data-stu-id="73d59-162">The component's constructor is invoked.</span></span>
* <span data-ttu-id="73d59-163">Konstruktory wszelkich niepojedynczych usług DI dostarczonych do konstruktora składnika za pośrednictwem [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) dyrektywy lub [`[Inject]`](xref:blazor/dependency-injection#request-a-service-in-a-component) atrybutu są wywoływane.</span><span class="sxs-lookup"><span data-stu-id="73d59-163">The constructors of any non-singleton DI services supplied to the component's constructor via the [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) directive or the [`[Inject]`](xref:blazor/dependency-injection#request-a-service-in-a-component) attribute are invoked.</span></span>

<span data-ttu-id="73d59-164">Obwód Blazor serwera kończy się niepowodzeniem, gdy dowolny wykonany Konstruktor lub setter `[Inject]` dla każdej właściwości zgłasza nieobsługiwany wyjątek.</span><span class="sxs-lookup"><span data-stu-id="73d59-164">A Blazor Server circuit fails when any executed constructor or a setter for any `[Inject]` property throws an unhandled exception.</span></span> <span data-ttu-id="73d59-165">Wyjątek jest krytyczny, ponieważ struktura nie może utworzyć wystąpienia składnika.</span><span class="sxs-lookup"><span data-stu-id="73d59-165">The exception is fatal because the framework can't instantiate the component.</span></span> <span data-ttu-id="73d59-166">Jeśli logika konstruktora może generować wyjątki, aplikacja powinna zalewkować wyjątki przy użyciu instrukcji [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) z obsługą błędów i rejestrowaniem.</span><span class="sxs-lookup"><span data-stu-id="73d59-166">If constructor logic may throw exceptions, the app should trap the exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

### <a name="lifecycle-methods"></a><span data-ttu-id="73d59-167">Metody cyklu życia</span><span class="sxs-lookup"><span data-stu-id="73d59-167">Lifecycle methods</span></span>

<span data-ttu-id="73d59-168">W okresie istnienia składnika program Blazor wywołuje następujące [metody cyklu życia](xref:blazor/lifecycle):</span><span class="sxs-lookup"><span data-stu-id="73d59-168">During the lifetime of a component, Blazor invokes the following [lifecycle methods](xref:blazor/lifecycle):</span></span>

* `OnInitialized` / `OnInitializedAsync`
* `OnParametersSet` / `OnParametersSetAsync`
* `ShouldRender` / `ShouldRenderAsync`
* `OnAfterRender` / `OnAfterRenderAsync`

<span data-ttu-id="73d59-169">Jeśli jakakolwiek metoda cyklu życia zgłasza wyjątek, synchronicznie lub asynchronicznie, wyjątek jest krytyczny dla Blazor obwodu serwera.</span><span class="sxs-lookup"><span data-stu-id="73d59-169">If any lifecycle method throws an exception, synchronously or asynchronously, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="73d59-170">Aby składniki zajmowały błędy w metodach cyklu życia, Dodaj logikę obsługi błędów.</span><span class="sxs-lookup"><span data-stu-id="73d59-170">For components to deal with errors in lifecycle methods, add error handling logic.</span></span>

<span data-ttu-id="73d59-171">W poniższym przykładzie, gdzie `OnParametersSetAsync` wywołuje metodę w celu uzyskania produktu:</span><span class="sxs-lookup"><span data-stu-id="73d59-171">In the following example where `OnParametersSetAsync` calls a method to obtain a product:</span></span>

* <span data-ttu-id="73d59-172">Wyjątek zgłoszony w `ProductRepository.GetProductByIdAsync` metodzie jest obsługiwany przez `try-catch` instrukcję.</span><span class="sxs-lookup"><span data-stu-id="73d59-172">An exception thrown in the `ProductRepository.GetProductByIdAsync` method is handled by a `try-catch` statement.</span></span>
* <span data-ttu-id="73d59-173">Gdy `catch` blok jest wykonywany:</span><span class="sxs-lookup"><span data-stu-id="73d59-173">When the `catch` block is executed:</span></span>
  * <span data-ttu-id="73d59-174">`loadFailed`jest ustawiona na `true`, która jest używana do wyświetlania komunikatu o błędzie dla użytkownika.</span><span class="sxs-lookup"><span data-stu-id="73d59-174">`loadFailed` is set to `true`, which is used to display an error message to the user.</span></span>
  * <span data-ttu-id="73d59-175">Błąd jest rejestrowany.</span><span class="sxs-lookup"><span data-stu-id="73d59-175">The error is logged.</span></span>

[!code-razor[](handle-errors/samples_snapshot/3.x/product-details.razor?highlight=11,27-39)]

### <a name="rendering-logic"></a><span data-ttu-id="73d59-176">Logika renderowania</span><span class="sxs-lookup"><span data-stu-id="73d59-176">Rendering logic</span></span>

<span data-ttu-id="73d59-177">Znaczniki deklaratywne w pliku `.razor` składnika są kompilowane do metody języka C# o nazwie `BuildRenderTree`.</span><span class="sxs-lookup"><span data-stu-id="73d59-177">The declarative markup in a `.razor` component file is compiled into a C# method called `BuildRenderTree`.</span></span> <span data-ttu-id="73d59-178">Gdy składnik renderuje, `BuildRenderTree` wykonuje i tworzy strukturę danych opisującą elementy, tekst i składniki podrzędne renderowanego składnika.</span><span class="sxs-lookup"><span data-stu-id="73d59-178">When a component renders, `BuildRenderTree` executes and builds up a data structure describing the elements, text, and child components of the rendered component.</span></span>

<span data-ttu-id="73d59-179">Logika renderowania może zgłosić wyjątek.</span><span class="sxs-lookup"><span data-stu-id="73d59-179">Rendering logic can throw an exception.</span></span> <span data-ttu-id="73d59-180">Przykład tego scenariusza występuje, gdy `@someObject.PropertyName` jest oceniane, ale `@someObject` jest `null`.</span><span class="sxs-lookup"><span data-stu-id="73d59-180">An example of this scenario occurs when `@someObject.PropertyName` is evaluated but `@someObject` is `null`.</span></span> <span data-ttu-id="73d59-181">Nieobsługiwany wyjątek zgłoszony przez logikę renderowania jest krytyczny dla obwodu Blazor serwera.</span><span class="sxs-lookup"><span data-stu-id="73d59-181">An unhandled exception thrown by rendering logic is fatal to a Blazor Server circuit.</span></span>

<span data-ttu-id="73d59-182">Aby zapobiec wystąpieniu wyjątku odwołania o wartości null w logice renderowania, `null` przed uzyskaniem dostępu do elementów członkowskich Sprawdź, czy jest on obiektem.</span><span class="sxs-lookup"><span data-stu-id="73d59-182">To prevent a null reference exception in rendering logic, check for a `null` object before accessing its members.</span></span> <span data-ttu-id="73d59-183">W poniższym przykładzie właściwości nie `person.Address` są dostępne, jeśli `person.Address` `null`:</span><span class="sxs-lookup"><span data-stu-id="73d59-183">In the following example, `person.Address` properties aren't accessed if `person.Address` is `null`:</span></span>

[!code-razor[](handle-errors/samples_snapshot/3.x/person-example.razor?highlight=1)]

<span data-ttu-id="73d59-184">Poprzedni kod założono, `person` że `null`nie jest.</span><span class="sxs-lookup"><span data-stu-id="73d59-184">The preceding code assumes that `person` isn't `null`.</span></span> <span data-ttu-id="73d59-185">Często Struktura kodu gwarantuje, że obiekt istnieje w momencie renderowania składnika.</span><span class="sxs-lookup"><span data-stu-id="73d59-185">Often, the structure of the code guarantees that an object exists at the time the component is rendered.</span></span> <span data-ttu-id="73d59-186">W takich przypadkach nie jest konieczne sprawdzanie `null` logiki renderowania.</span><span class="sxs-lookup"><span data-stu-id="73d59-186">In those cases, it isn't necessary to check for `null` in rendering logic.</span></span> <span data-ttu-id="73d59-187">W poprzednim przykładzie można zagwarantować `person` , że istnieje, ponieważ `person` jest tworzony podczas tworzenia wystąpienia składnika.</span><span class="sxs-lookup"><span data-stu-id="73d59-187">In the prior example, `person` might be guaranteed to exist because `person` is created when the component is instantiated.</span></span>

### <a name="event-handlers"></a><span data-ttu-id="73d59-188">Procedury obsługi zdarzeń</span><span class="sxs-lookup"><span data-stu-id="73d59-188">Event handlers</span></span>

<span data-ttu-id="73d59-189">Kod po stronie klienta wyzwala wywołania kodu C#, gdy programy obsługi zdarzeń są tworzone przy użyciu:</span><span class="sxs-lookup"><span data-stu-id="73d59-189">Client-side code triggers invocations of C# code when event handlers are created using:</span></span>

* `@onclick`
* `@onchange`
* <span data-ttu-id="73d59-190">Inne `@on...` atrybuty</span><span class="sxs-lookup"><span data-stu-id="73d59-190">Other `@on...` attributes</span></span>
* `@bind`

<span data-ttu-id="73d59-191">Kod procedury obsługi zdarzeń może zgłosić nieobsługiwany wyjątek w tych scenariuszach.</span><span class="sxs-lookup"><span data-stu-id="73d59-191">Event handler code might throw an unhandled exception in these scenarios.</span></span>

<span data-ttu-id="73d59-192">Jeśli procedura obsługi zdarzeń zgłasza nieobsługiwany wyjątek (na przykład kwerenda bazy danych kończy się niepowodzeniem), wyjątek jest krytyczny dla obwodu Blazor serwera.</span><span class="sxs-lookup"><span data-stu-id="73d59-192">If an event handler throws an unhandled exception (for example, a database query fails), the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="73d59-193">Jeśli aplikacja wywołuje kod, który może zakończyć się niepowodzeniem z powodów zewnętrznych, należy zastosować wyjątek pułapki przy użyciu instrukcji [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) z obsługą błędów i rejestrowaniem.</span><span class="sxs-lookup"><span data-stu-id="73d59-193">If the app calls code that could fail for external reasons, trap exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="73d59-194">Jeśli kod użytkownika nie jest pułapk i nie obsługuje wyjątku, struktura rejestruje wyjątek i kończy obwód.</span><span class="sxs-lookup"><span data-stu-id="73d59-194">If user code doesn't trap and handle the exception, the framework logs the exception and terminates the circuit.</span></span>

### <a name="component-disposal"></a><span data-ttu-id="73d59-195">Usuwanie składników</span><span class="sxs-lookup"><span data-stu-id="73d59-195">Component disposal</span></span>

<span data-ttu-id="73d59-196">Składnik może zostać usunięty z interfejsu użytkownika, na przykład, ponieważ użytkownik przeszedł do innej strony.</span><span class="sxs-lookup"><span data-stu-id="73d59-196">A component may be removed from the UI, for example, because the user has navigated to another page.</span></span> <span data-ttu-id="73d59-197">Gdy składnik implementujący <xref:System.IDisposable?displayProperty=fullName> jest usuwany z interfejsu użytkownika, struktura wywołuje <xref:System.IDisposable.Dispose*> metodę składnika.</span><span class="sxs-lookup"><span data-stu-id="73d59-197">When a component that implements <xref:System.IDisposable?displayProperty=fullName> is removed from the UI, the framework calls the component's <xref:System.IDisposable.Dispose*> method.</span></span>

<span data-ttu-id="73d59-198">Jeśli `Dispose` Metoda składnika zgłasza nieobsługiwany wyjątek, wyjątek jest krytyczny dla obwodu Blazor serwera.</span><span class="sxs-lookup"><span data-stu-id="73d59-198">If the component's `Dispose` method throws an unhandled exception, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="73d59-199">Jeśli logika usuwania może generować wyjątki, aplikacja powinna zalewkować wyjątki przy użyciu instrukcji [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) z obsługą błędów i rejestrowaniem.</span><span class="sxs-lookup"><span data-stu-id="73d59-199">If disposal logic may throw exceptions, the app should trap the exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="73d59-200">Aby uzyskać więcej informacji na temat usuwania składników <xref:blazor/lifecycle#component-disposal-with-idisposable>, zobacz.</span><span class="sxs-lookup"><span data-stu-id="73d59-200">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>

### <a name="javascript-interop"></a><span data-ttu-id="73d59-201">Międzyoperacyjność w języku JavaScript</span><span class="sxs-lookup"><span data-stu-id="73d59-201">JavaScript interop</span></span>

<span data-ttu-id="73d59-202">`IJSRuntime.InvokeAsync<T>`umożliwia programowi .NET Code wykonywanie wywołań asynchronicznych do środowiska uruchomieniowego JavaScript w przeglądarce użytkownika.</span><span class="sxs-lookup"><span data-stu-id="73d59-202">`IJSRuntime.InvokeAsync<T>` allows .NET code to make asynchronous calls to the JavaScript runtime in the user's browser.</span></span>

<span data-ttu-id="73d59-203">Poniższe warunki dotyczą obsługi błędów w programie `InvokeAsync<T>`:</span><span class="sxs-lookup"><span data-stu-id="73d59-203">The following conditions apply to error handling with `InvokeAsync<T>`:</span></span>

* <span data-ttu-id="73d59-204">Jeśli wywołanie synchronicznie `InvokeAsync<T>` zakończy się niepowodzeniem, wystąpi wyjątek programu .NET.</span><span class="sxs-lookup"><span data-stu-id="73d59-204">If a call to `InvokeAsync<T>` fails synchronously, a .NET exception occurs.</span></span> <span data-ttu-id="73d59-205">Wywołanie `InvokeAsync<T>` może zakończyć się niepowodzeniem, na przykład dlatego, że nie można serializować dostarczonych argumentów.</span><span class="sxs-lookup"><span data-stu-id="73d59-205">A call to `InvokeAsync<T>` may fail, for example, because the supplied arguments can't be serialized.</span></span> <span data-ttu-id="73d59-206">Kod dewelopera musi przechwycić wyjątek.</span><span class="sxs-lookup"><span data-stu-id="73d59-206">Developer code must catch the exception.</span></span> <span data-ttu-id="73d59-207">Jeśli kod aplikacji w obsłudze zdarzeń lub metoda cyklu życia składnika nie obsłuży wyjątku, wynikający z nich wyjątek jest krytyczny dla obwodu Blazor serwera.</span><span class="sxs-lookup"><span data-stu-id="73d59-207">If app code in an event handler or component lifecycle method doesn't handle an exception, the resulting exception is fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="73d59-208">Jeśli wywołanie `InvokeAsync<T>` powiedzie się asynchronicznie, .NET <xref:System.Threading.Tasks.Task> kończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="73d59-208">If a call to `InvokeAsync<T>` fails asynchronously, the .NET <xref:System.Threading.Tasks.Task> fails.</span></span> <span data-ttu-id="73d59-209">Wywołanie `InvokeAsync<T>` może zakończyć się niepowodzeniem, na przykład ponieważ kod po stronie JavaScript zgłasza wyjątek lub zwraca `Promise` , który został ukończony jako. `rejected`</span><span class="sxs-lookup"><span data-stu-id="73d59-209">A call to `InvokeAsync<T>` may fail, for example, because the JavaScript-side code throws an exception or returns a `Promise` that completed as `rejected`.</span></span> <span data-ttu-id="73d59-210">Kod dewelopera musi przechwycić wyjątek.</span><span class="sxs-lookup"><span data-stu-id="73d59-210">Developer code must catch the exception.</span></span> <span data-ttu-id="73d59-211">W przypadku użycia operatora [await](/dotnet/csharp/language-reference/keywords/await) Rozważ zapakowanie wywołania metody w instrukcji [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) z obsługą błędów i rejestrowaniem.</span><span class="sxs-lookup"><span data-stu-id="73d59-211">If using the [await](/dotnet/csharp/language-reference/keywords/await) operator, consider wrapping the method call in a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span> <span data-ttu-id="73d59-212">W przeciwnym razie niepowodzenie kodu spowoduje nieobsłużony wyjątek, który jest krytyczny dla obwodu Blazor serwera.</span><span class="sxs-lookup"><span data-stu-id="73d59-212">Otherwise, the failing code results in an unhandled exception that's fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="73d59-213">Domyślnie wywołania programu muszą zakończyć `InvokeAsync<T>` się w określonym przedziale czasu lub w przeciwnym razie upłynął limit czasu połączenia. Domyślny limit czasu wynosi jedną minutę.</span><span class="sxs-lookup"><span data-stu-id="73d59-213">By default, calls to `InvokeAsync<T>` must complete within a certain period or else the call times out. The default timeout period is one minute.</span></span> <span data-ttu-id="73d59-214">Limit czasu chroni kod przed utratą połączenia sieciowego lub kodem JavaScript, który nigdy nie odsyła komunikat uzupełniający.</span><span class="sxs-lookup"><span data-stu-id="73d59-214">The timeout protects the code against a loss in network connectivity or JavaScript code that never sends back a completion message.</span></span> <span data-ttu-id="73d59-215">Jeśli wystąpiło przełączenie, `Task` wynikiem kończy się <xref:System.OperationCanceledException>niepowodzeniem a.</span><span class="sxs-lookup"><span data-stu-id="73d59-215">If the call times out, the resulting `Task` fails with an <xref:System.OperationCanceledException>.</span></span> <span data-ttu-id="73d59-216">Zalewka i przetwórz wyjątek z rejestrowaniem.</span><span class="sxs-lookup"><span data-stu-id="73d59-216">Trap and process the exception with logging.</span></span>

<span data-ttu-id="73d59-217">Podobnie kod JavaScript może inicjować wywołania metod .NET wskazywanych przez [`[JSInvokable]`](xref:blazor/call-dotnet-from-javascript) atrybut.</span><span class="sxs-lookup"><span data-stu-id="73d59-217">Similarly, JavaScript code may initiate calls to .NET methods indicated by the [`[JSInvokable]`](xref:blazor/call-dotnet-from-javascript) attribute.</span></span> <span data-ttu-id="73d59-218">Jeśli te metody .NET zgłaszają nieobsługiwany wyjątek:</span><span class="sxs-lookup"><span data-stu-id="73d59-218">If these .NET methods throw an unhandled exception:</span></span>

* <span data-ttu-id="73d59-219">Wyjątek nie jest traktowany jako krytyczny Blazor dla obwodu serwera.</span><span class="sxs-lookup"><span data-stu-id="73d59-219">The exception isn't treated as fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="73d59-220">Po stronie `Promise` JavaScript jest odrzucany.</span><span class="sxs-lookup"><span data-stu-id="73d59-220">The JavaScript-side `Promise` is rejected.</span></span>

<span data-ttu-id="73d59-221">Istnieje możliwość użycia kodu obsługi błędów po stronie .NET lub stronie JavaScript wywołania metody.</span><span class="sxs-lookup"><span data-stu-id="73d59-221">You have the option of using error handling code on either the .NET side or the JavaScript side of the method call.</span></span>

<span data-ttu-id="73d59-222">Aby uzyskać więcej informacji zobacz następujące artykuły:</span><span class="sxs-lookup"><span data-stu-id="73d59-222">For more information, see the following articles:</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

### <a name="opno-locblazor-server-prerendering"></a>Blazor<span data-ttu-id="73d59-223">Renderowanie na serwerze</span><span class="sxs-lookup"><span data-stu-id="73d59-223"> Server prerendering</span></span>

Blazor<span data-ttu-id="73d59-224">składniki mogą być wstępnie renderowane przy użyciu [pomocnika tagów składnika](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) , dzięki czemu RENDEROWANE znaczniki HTML są zwracane jako część początkowego żądania HTTP użytkownika.</span><span class="sxs-lookup"><span data-stu-id="73d59-224"> components can be prerendered using the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) so that their rendered HTML markup is returned as part of the user's initial HTTP request.</span></span> <span data-ttu-id="73d59-225">Działa to w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="73d59-225">This works by:</span></span>

* <span data-ttu-id="73d59-226">Tworzenie nowego obwodu dla wszystkich wstępnie renderowanych składników, które są częścią tej samej strony.</span><span class="sxs-lookup"><span data-stu-id="73d59-226">Creating a new circuit for all of the prerendered components that are part of the same page.</span></span>
* <span data-ttu-id="73d59-227">Generowanie początkowego kodu HTML.</span><span class="sxs-lookup"><span data-stu-id="73d59-227">Generating the initial HTML.</span></span>
* <span data-ttu-id="73d59-228">Przetraktowanie obwodu `disconnected` do momentu, aż przeglądarka SignalR użytkownika nawiąże połączenie z powrotem z tym samym serwerem.</span><span class="sxs-lookup"><span data-stu-id="73d59-228">Treating the circuit as `disconnected` until the user's browser establishes a SignalR connection back to the same server.</span></span> <span data-ttu-id="73d59-229">Po nawiązaniu połączenia zostanie wznowione działanie międzydziałające w obwodzie i zostanie zaktualizowane oznaczenie HTML składników.</span><span class="sxs-lookup"><span data-stu-id="73d59-229">When the connection is established, interactivity on the circuit is resumed and the components' HTML markup is updated.</span></span>

<span data-ttu-id="73d59-230">Jeśli jakikolwiek składnik zgłasza nieobsłużony wyjątek podczas renderowania pre, na przykład podczas wykonywania metody cyklu życia lub logiki renderowania:</span><span class="sxs-lookup"><span data-stu-id="73d59-230">If any component throws an unhandled exception during prerendering, for example, during a lifecycle method or in rendering logic:</span></span>

* <span data-ttu-id="73d59-231">Wyjątek jest krytyczny dla obwodu.</span><span class="sxs-lookup"><span data-stu-id="73d59-231">The exception is fatal to the circuit.</span></span>
* <span data-ttu-id="73d59-232">Wyjątek jest generowany w stosie wywołań z pomocnika `Component` tagów.</span><span class="sxs-lookup"><span data-stu-id="73d59-232">The exception is thrown up the call stack from the `Component` Tag Helper.</span></span> <span data-ttu-id="73d59-233">W związku z tym całe żądanie HTTP kończy się niepowodzeniem, chyba że wyjątek jest jawnie przechwycony przez kod dewelopera.</span><span class="sxs-lookup"><span data-stu-id="73d59-233">Therefore, the entire HTTP request fails unless the exception is explicitly caught by developer code.</span></span>

<span data-ttu-id="73d59-234">W normalnych warunkach w przypadku niepowodzenia wstępnego renderowania kontynuowanie kompilowania i renderowania składnika nie ma sensu, ponieważ nie można renderować składnika roboczego.</span><span class="sxs-lookup"><span data-stu-id="73d59-234">Under normal circumstances when prerendering fails, continuing to build and render the component doesn't make sense because a working component can't be rendered.</span></span>

<span data-ttu-id="73d59-235">Aby tolerować błędy, które mogą wystąpić podczas renderowania prerenderingu, logika obsługi błędów musi być umieszczona wewnątrz składnika, który może zgłaszać wyjątki.</span><span class="sxs-lookup"><span data-stu-id="73d59-235">To tolerate errors that may occur during prerendering, error handling logic must be placed inside a component that may throw exceptions.</span></span> <span data-ttu-id="73d59-236">Używaj instrukcji [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) z obsługą błędów i rejestrowaniem.</span><span class="sxs-lookup"><span data-stu-id="73d59-236">Use [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span> <span data-ttu-id="73d59-237">Zamiast zawijać pomocnika `Component` tagów w `try-catch` instrukcji, umieść logikę obsługi błędów w składniku renderowanym przez `Component` pomocnika tagów.</span><span class="sxs-lookup"><span data-stu-id="73d59-237">Instead of wrapping the `Component` Tag Helper in a `try-catch` statement, place error handling logic in the component rendered by the `Component` Tag Helper.</span></span>

## <a name="advanced-scenarios"></a><span data-ttu-id="73d59-238">Scenariusze zaawansowane</span><span class="sxs-lookup"><span data-stu-id="73d59-238">Advanced scenarios</span></span>

### <a name="recursive-rendering"></a><span data-ttu-id="73d59-239">Renderowanie cykliczne</span><span class="sxs-lookup"><span data-stu-id="73d59-239">Recursive rendering</span></span>

<span data-ttu-id="73d59-240">Składniki można cyklicznie zagnieżdżać.</span><span class="sxs-lookup"><span data-stu-id="73d59-240">Components can be nested recursively.</span></span> <span data-ttu-id="73d59-241">Jest to przydatne do reprezentowania struktur danych rekursywnych.</span><span class="sxs-lookup"><span data-stu-id="73d59-241">This is useful for representing recursive data structures.</span></span> <span data-ttu-id="73d59-242">Na przykład `TreeNode` składnik może renderować więcej `TreeNode` składników dla każdego węzła podrzędnego.</span><span class="sxs-lookup"><span data-stu-id="73d59-242">For example, a `TreeNode` component can render more `TreeNode` components for each of the node's children.</span></span>

<span data-ttu-id="73d59-243">W przypadku renderowania cyklicznego należy unikać tworzenia wzorców, które powodują nieskończoną rekursję:</span><span class="sxs-lookup"><span data-stu-id="73d59-243">When rendering recursively, avoid coding patterns that result in infinite recursion:</span></span>

* <span data-ttu-id="73d59-244">Nie Renderuj rekursywnie struktury danych zawierającej cykl.</span><span class="sxs-lookup"><span data-stu-id="73d59-244">Don't recursively render a data structure that contains a cycle.</span></span> <span data-ttu-id="73d59-245">Na przykład nie Renderuj węzła drzewa, którego elementy podrzędne zawierają sam siebie.</span><span class="sxs-lookup"><span data-stu-id="73d59-245">For example, don't render a tree node whose children includes itself.</span></span>
* <span data-ttu-id="73d59-246">Nie twórz łańcucha układów zawierających cykl.</span><span class="sxs-lookup"><span data-stu-id="73d59-246">Don't create a chain of layouts that contain a cycle.</span></span> <span data-ttu-id="73d59-247">Na przykład nie twórz układu, którego układ jest sam.</span><span class="sxs-lookup"><span data-stu-id="73d59-247">For example, don't create a layout whose layout is itself.</span></span>
* <span data-ttu-id="73d59-248">Nie Zezwalaj użytkownikowi końcowemu na naruszenie nieodmian rekursji (reguł) przy użyciu złośliwego wpisu danych lub wywołań międzyoperacyjnych języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="73d59-248">Don't allow an end user to violate recursion invariants (rules) through malicious data entry or JavaScript interop calls.</span></span>

<span data-ttu-id="73d59-249">Nieskończone pętle podczas renderowania:</span><span class="sxs-lookup"><span data-stu-id="73d59-249">Infinite loops during rendering:</span></span>

* <span data-ttu-id="73d59-250">Powoduje, że proces renderowania kontynuuje działanie zawsze.</span><span class="sxs-lookup"><span data-stu-id="73d59-250">Causes the rendering process to continue forever.</span></span>
* <span data-ttu-id="73d59-251">Jest równoznaczny z tworzeniem niezakończonej pętli.</span><span class="sxs-lookup"><span data-stu-id="73d59-251">Is equivalent to creating an unterminated loop.</span></span>

<span data-ttu-id="73d59-252">W tych scenariuszach obwód serwera, Blazor którego to dotyczy, kończy się niepowodzeniem, a wątek zwykle próbuje wykonać:</span><span class="sxs-lookup"><span data-stu-id="73d59-252">In these scenarios, an affected Blazor Server circuit fails, and the thread usually attempts to:</span></span>

* <span data-ttu-id="73d59-253">Zużywaj ilość czasu procesora CPU dozwoloną przez system operacyjny w nieskończoność.</span><span class="sxs-lookup"><span data-stu-id="73d59-253">Consume as much CPU time as permitted by the operating system, indefinitely.</span></span>
* <span data-ttu-id="73d59-254">Korzystaj z nieograniczonej ilości pamięci serwera.</span><span class="sxs-lookup"><span data-stu-id="73d59-254">Consume an unlimited amount of server memory.</span></span> <span data-ttu-id="73d59-255">Zużywanie nieograniczonej pamięci jest równoważne scenariuszowi, w którym niezakończona pętla dodaje wpisy do kolekcji na każdej iteracji.</span><span class="sxs-lookup"><span data-stu-id="73d59-255">Consuming unlimited memory is equivalent to the scenario where an unterminated loop adds entries to a collection on every iteration.</span></span>

<span data-ttu-id="73d59-256">Aby uniknąć nieskończonych wzorców rekursji, należy się upewnić, że kod renderowania cyklicznego zawiera odpowiednie warunki zatrzymania.</span><span class="sxs-lookup"><span data-stu-id="73d59-256">To avoid infinite recursion patterns, ensure that recursive rendering code contains suitable stopping conditions.</span></span>

### <a name="custom-render-tree-logic"></a><span data-ttu-id="73d59-257">Logika drzewa renderowania niestandardowego</span><span class="sxs-lookup"><span data-stu-id="73d59-257">Custom render tree logic</span></span>

<span data-ttu-id="73d59-258">Większość Blazor składników jest zaimplementowana jako pliki *Razor* i są kompilowane do tworzenia logiki, która działa `RenderTreeBuilder` w celu renderowania danych wyjściowych.</span><span class="sxs-lookup"><span data-stu-id="73d59-258">Most Blazor components are implemented as *.razor* files and are compiled to produce logic that operates on a `RenderTreeBuilder` to render their output.</span></span> <span data-ttu-id="73d59-259">Deweloper może ręcznie zaimplementować `RenderTreeBuilder` logikę przy użyciu procedury kodu w języku C#.</span><span class="sxs-lookup"><span data-stu-id="73d59-259">A developer may manually implement `RenderTreeBuilder` logic using procedural C# code.</span></span> <span data-ttu-id="73d59-260">Aby uzyskać więcej informacji, zobacz <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span><span class="sxs-lookup"><span data-stu-id="73d59-260">For more information, see <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span></span>

> [!WARNING]
> <span data-ttu-id="73d59-261">Korzystanie z logiki konstruktora drzewa renderowania ręcznego jest uznawane za zaawansowane i niebezpieczne scenariusze, które nie są zalecane do ogólnego tworzenia składników.</span><span class="sxs-lookup"><span data-stu-id="73d59-261">Use of manual render tree builder logic is considered an advanced and unsafe scenario, not recommended for general component development.</span></span>

<span data-ttu-id="73d59-262">Jeśli `RenderTreeBuilder` kod zostanie zapisany, Deweloper musi zagwarantować poprawność kodu.</span><span class="sxs-lookup"><span data-stu-id="73d59-262">If `RenderTreeBuilder` code is written, the developer must guarantee the correctness of the code.</span></span> <span data-ttu-id="73d59-263">Na przykład Deweloper musi upewnić się, że:</span><span class="sxs-lookup"><span data-stu-id="73d59-263">For example, the developer must ensure that:</span></span>

* <span data-ttu-id="73d59-264">Wywołania `OpenElement` i `CloseElement` są prawidłowo zrównoważone.</span><span class="sxs-lookup"><span data-stu-id="73d59-264">Calls to `OpenElement` and `CloseElement` are correctly balanced.</span></span>
* <span data-ttu-id="73d59-265">Atrybuty są dodawane tylko w prawidłowych miejscach.</span><span class="sxs-lookup"><span data-stu-id="73d59-265">Attributes are only added in the correct places.</span></span>

<span data-ttu-id="73d59-266">Nieprawidłowa ręczna logika konstruktora drzewa renderowania może spowodować dowolne niezdefiniowane zachowanie, w tym awarie, zawieszenie serwera i luki w zabezpieczeniach.</span><span class="sxs-lookup"><span data-stu-id="73d59-266">Incorrect manual render tree builder logic can cause arbitrary undefined behavior, including crashes, server hangs, and security vulnerabilities.</span></span>

<span data-ttu-id="73d59-267">Rozważ ręczne renderowanie logiki konstruktora drzew drzewa na tym samym poziomie złożoności i z tym samym poziomem *zagrożenia* co ręczne pisanie kodu zestawu lub instrukcji MSIL.</span><span class="sxs-lookup"><span data-stu-id="73d59-267">Consider manual render tree builder logic on the same level of complexity and with the same level of *danger* as writing assembly code or MSIL instructions by hand.</span></span>
