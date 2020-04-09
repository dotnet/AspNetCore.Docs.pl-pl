---
title: Obsługa błędów w aplikacjach ASP.NET Core Blazor
author: guardrex
description: Dowiedz się, jak Blazor ASP.NET Blazor Core, jak zarządza nieobsługiwałem wyjątków i jak tworzyć aplikacje, które wykrywają i obsługują błędy.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/29/2020
no-loc:
- Blazor
- SignalR
uid: blazor/handle-errors
ms.openlocfilehash: 4fdaf7fb90d126b8f7f029aac3af49eec3b69e74
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80382278"
---
# <a name="handle-errors-in-aspnet-core-opno-locblazor-apps"></a><span data-ttu-id="56b28-103">Obsługa błędów w aplikacjach ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="56b28-103">Handle errors in ASP.NET Core Blazor apps</span></span>

<span data-ttu-id="56b28-104">Przez [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="56b28-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="56b28-105">W tym Blazor artykule opisano, jak zarządza nieobsługiwanie wyjątków i jak tworzyć aplikacje, które wykrywają i obsługują błędy.</span><span class="sxs-lookup"><span data-stu-id="56b28-105">This article describes how Blazor manages unhandled exceptions and how to develop apps that detect and handle errors.</span></span>

## <a name="detailed-errors-during-development"></a><span data-ttu-id="56b28-106">Szczegółowe błędy podczas opracowywania</span><span class="sxs-lookup"><span data-stu-id="56b28-106">Detailed errors during development</span></span>

<span data-ttu-id="56b28-107">Gdy Blazor aplikacja nie działa poprawnie podczas tworzenia, otrzymywanie szczegółowych informacji o błędzie z aplikacji pomaga w rozwiązywaniu problemów i rozwiązywaniu problemu.</span><span class="sxs-lookup"><span data-stu-id="56b28-107">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="56b28-108">W przypadku wystąpienia Blazor błędu aplikacje wyświetlają złoty pasek u dołu ekranu:</span><span class="sxs-lookup"><span data-stu-id="56b28-108">When an error occurs, Blazor apps display a gold bar at the bottom of the screen:</span></span>

* <span data-ttu-id="56b28-109">Podczas tworzenia złotego paska kieruje cię do konsoli przeglądarki, gdzie można zobaczyć wyjątek.</span><span class="sxs-lookup"><span data-stu-id="56b28-109">During development, the gold bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="56b28-110">W produkcji złoty pasek powiadamia użytkownika o wystąpieniu błędu i zaleca odświeżenie przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="56b28-110">In production, the gold bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="56b28-111">Interfejs użytkownika dla tego środowiska obsługi Blazor błędów jest częścią szablonów projektu.</span><span class="sxs-lookup"><span data-stu-id="56b28-111">The UI for this error handling experience is part of the Blazor project templates.</span></span>

<span data-ttu-id="56b28-112">W Blazor aplikacji WebAssembly dostosuj środowisko w pliku *wwwroot/index.html:*</span><span class="sxs-lookup"><span data-stu-id="56b28-112">In a Blazor WebAssembly app, customize the experience in the *wwwroot/index.html* file:</span></span>

```html
<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="56b28-113">W Blazor aplikacji Serwer dostosuj środowisko w pliku *Pages/_Host.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="56b28-113">In a Blazor Server app, customize the experience in the *Pages/_Host.cshtml* file:</span></span>

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

<span data-ttu-id="56b28-114">Element `blazor-error-ui` jest ukryty przez style zawarte Blazor w szablonach (*wwwroot/css/site.css),* a następnie wyświetlany po wystąpieniu błędu:</span><span class="sxs-lookup"><span data-stu-id="56b28-114">The `blazor-error-ui` element is hidden by the styles included in the Blazor templates (*wwwroot/css/site.css*) and then shown when an error occurs:</span></span>

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

## <a name="how-a-opno-locblazor-server-app-reacts-to-unhandled-exceptions"></a><span data-ttu-id="56b28-115">Jak Blazor aplikacja Server reaguje na nieobsługiwalne wyjątki</span><span class="sxs-lookup"><span data-stu-id="56b28-115">How a Blazor Server app reacts to unhandled exceptions</span></span>

Blazor<span data-ttu-id="56b28-116">Serwer jest stanową strukturą.</span><span class="sxs-lookup"><span data-stu-id="56b28-116"> Server is a stateful framework.</span></span> <span data-ttu-id="56b28-117">Podczas gdy użytkownicy wchodzą w interakcję z aplikacją, utrzymują połączenie z serwerem znanym jako *obwód*.</span><span class="sxs-lookup"><span data-stu-id="56b28-117">While users interact with an app, they maintain a connection to the server known as a *circuit*.</span></span> <span data-ttu-id="56b28-118">Obwód zawiera aktywne wystąpienia składników oraz wiele innych aspektów stanu, takich jak:</span><span class="sxs-lookup"><span data-stu-id="56b28-118">The circuit holds active component instances, plus many other aspects of state, such as:</span></span>

* <span data-ttu-id="56b28-119">Najnowsze renderowane dane wyjściowe składników.</span><span class="sxs-lookup"><span data-stu-id="56b28-119">The most recent rendered output of components.</span></span>
* <span data-ttu-id="56b28-120">Bieżący zestaw delegatów obsługi zdarzeń, które mogą być wyzwalane przez zdarzenia po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="56b28-120">The current set of event-handling delegates that could be triggered by client-side events.</span></span>

<span data-ttu-id="56b28-121">Jeśli użytkownik otworzy aplikację w wielu kartach przeglądarki, mają wiele niezależnych obwodów.</span><span class="sxs-lookup"><span data-stu-id="56b28-121">If a user opens the app in multiple browser tabs, they have multiple independent circuits.</span></span>

Blazor<span data-ttu-id="56b28-122">traktuje większość nieobsługiwałem wyjątków jako śmiertelne dla obwodu, w którym występują.</span><span class="sxs-lookup"><span data-stu-id="56b28-122"> treats most unhandled exceptions as fatal to the circuit where they occur.</span></span> <span data-ttu-id="56b28-123">Jeśli obwód zostanie zakończony z powodu nieobsługiwał wyjątek, użytkownik może kontynuować interakcję z aplikacją tylko przez ponowne załadowanie strony w celu utworzenia nowego obwodu.</span><span class="sxs-lookup"><span data-stu-id="56b28-123">If a circuit is terminated due to an unhandled exception, the user can only continue to interact with the app by reloading the page to create a new circuit.</span></span> <span data-ttu-id="56b28-124">Nie ma to wpływu na obwody poza końców, które są obwodami dla innych użytkowników lub innymi kartami przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="56b28-124">Circuits outside of the one that's terminated, which are circuits for other users or other browser tabs, aren't affected.</span></span> <span data-ttu-id="56b28-125">Ten scenariusz jest podobny do&mdash;aplikacji klasycznej, która ulega awarii, należy ponownie uruchomić aplikację, ale nie ma to wpływu na inne aplikacje.</span><span class="sxs-lookup"><span data-stu-id="56b28-125">This scenario is similar to a desktop app that crashes&mdash;the crashed app must be restarted, but other apps aren't affected.</span></span>

<span data-ttu-id="56b28-126">Obwód zostaje zakończony, gdy wystąpi nieobsługiowany wyjątek z następujących powodów:</span><span class="sxs-lookup"><span data-stu-id="56b28-126">A circuit is terminated when an unhandled exception occurs for the following reasons:</span></span>

* <span data-ttu-id="56b28-127">Nieobsługiwał wyjątek często pozostawia obwód w stanie nieokreślonym.</span><span class="sxs-lookup"><span data-stu-id="56b28-127">An unhandled exception often leaves the circuit in an undefined state.</span></span>
* <span data-ttu-id="56b28-128">Normalna operacja aplikacji nie może być zagwarantowana po nieobsługiwał wyjątek.</span><span class="sxs-lookup"><span data-stu-id="56b28-128">The app's normal operation can't be guaranteed after an unhandled exception.</span></span>
* <span data-ttu-id="56b28-129">Jeśli obwód będzie kontynuowany, w aplikacji mogą pojawić się luki w zabezpieczeniach.</span><span class="sxs-lookup"><span data-stu-id="56b28-129">Security vulnerabilities may appear in the app if the circuit continues.</span></span>

## <a name="manage-unhandled-exceptions-in-developer-code"></a><span data-ttu-id="56b28-130">Zarządzanie nieobsługiwałem wyjątków w kodzie dewelopera</span><span class="sxs-lookup"><span data-stu-id="56b28-130">Manage unhandled exceptions in developer code</span></span>

<span data-ttu-id="56b28-131">Aby aplikacja mogła być kontynuowana po błędzie, aplikacja musi mieć logikę obsługi błędów.</span><span class="sxs-lookup"><span data-stu-id="56b28-131">For an app to continue after an error, the app must have error handling logic.</span></span> <span data-ttu-id="56b28-132">W dalszej części tego artykułu opisano potencjalne źródła nieobsługiwał wyjątków.</span><span class="sxs-lookup"><span data-stu-id="56b28-132">Later sections of this article describe potential sources of unhandled exceptions.</span></span>

<span data-ttu-id="56b28-133">W produkcji nie renderuj komunikatów wyjątków framework lub ślady stosu w interfejsie użytkownika.</span><span class="sxs-lookup"><span data-stu-id="56b28-133">In production, don't render framework exception messages or stack traces in the UI.</span></span> <span data-ttu-id="56b28-134">Renderowanie komunikatów o wyjątkach lub śladów stosu może:</span><span class="sxs-lookup"><span data-stu-id="56b28-134">Rendering exception messages or stack traces could:</span></span>

* <span data-ttu-id="56b28-135">Ujawnianie poufnych informacji użytkownikom końcowym.</span><span class="sxs-lookup"><span data-stu-id="56b28-135">Disclose sensitive information to end users.</span></span>
* <span data-ttu-id="56b28-136">Pomóż złośliwemu użytkownikowi odkryć słabe punkty w aplikacji, które mogą naruszyć bezpieczeństwo aplikacji, serwera lub sieci.</span><span class="sxs-lookup"><span data-stu-id="56b28-136">Help a malicious user discover weaknesses in an app that can compromise the security of the app, server, or network.</span></span>

## <a name="log-errors-with-a-persistent-provider"></a><span data-ttu-id="56b28-137">Rejestrowanie błędów u trwałego dostawcy</span><span class="sxs-lookup"><span data-stu-id="56b28-137">Log errors with a persistent provider</span></span>

<span data-ttu-id="56b28-138">Jeśli wystąpi nieobsługiowany wyjątek, <xref:Microsoft.Extensions.Logging.ILogger> wyjątek jest rejestrowany w wystąpieniach skonfigurowanych w kontenerze usługi.</span><span class="sxs-lookup"><span data-stu-id="56b28-138">If an unhandled exception occurs, the exception is logged to <xref:Microsoft.Extensions.Logging.ILogger> instances configured in the service container.</span></span> <span data-ttu-id="56b28-139">Domyślnie Blazor aplikacje logują się do danych wyjściowych konsoli za pomocą dostawcy rejestrowania konsoli.</span><span class="sxs-lookup"><span data-stu-id="56b28-139">By default, Blazor apps log to console output with the Console Logging Provider.</span></span> <span data-ttu-id="56b28-140">Należy wziąć pod uwagę rejestrowanie do bardziej stałej lokalizacji z dostawcą, który zarządza rozmiar dziennika i rotacji dziennika.</span><span class="sxs-lookup"><span data-stu-id="56b28-140">Consider logging to a more permanent location with a provider that manages log size and log rotation.</span></span> <span data-ttu-id="56b28-141">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="56b28-141">For more information, see <xref:fundamentals/logging/index>.</span></span>

<span data-ttu-id="56b28-142">Podczas tworzenia Blazor zwykle wysyła pełne szczegóły wyjątków do konsoli przeglądarki, aby pomóc w debugowaniu.</span><span class="sxs-lookup"><span data-stu-id="56b28-142">During development, Blazor usually sends the full details of exceptions to the browser's console to aid in debugging.</span></span> <span data-ttu-id="56b28-143">W wersji produkcyjnej szczegółowe błędy w konsoli przeglądarki są domyślnie wyłączone, co oznacza, że błędy nie są wysyłane do klientów, ale pełne szczegóły wyjątku są nadal rejestrowane po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="56b28-143">In production, detailed errors in the browser's console are disabled by default, which means that errors aren't sent to clients but the exception's full details are still logged server-side.</span></span> <span data-ttu-id="56b28-144">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="56b28-144">For more information, see <xref:fundamentals/error-handling>.</span></span>

<span data-ttu-id="56b28-145">Należy zdecydować, które zdarzenia mają zostać zarejestrowane i poziom ważności zarejestrowanych zdarzeń.</span><span class="sxs-lookup"><span data-stu-id="56b28-145">You must decide which incidents to log and the level of severity of logged incidents.</span></span> <span data-ttu-id="56b28-146">Wrogo nastawieni użytkownicy mogą celowo wyzwolić błędy.</span><span class="sxs-lookup"><span data-stu-id="56b28-146">Hostile users might be able to trigger errors deliberately.</span></span> <span data-ttu-id="56b28-147">Na przykład nie rejestruj zdarzenia z powodu `ProductId` błędu, w którym w adresie URL składnika, który wyświetla szczegóły produktu, znajduje się nieznany.</span><span class="sxs-lookup"><span data-stu-id="56b28-147">For example, don't log an incident from an error where an unknown `ProductId` is supplied in the URL of a component that displays product details.</span></span> <span data-ttu-id="56b28-148">Nie wszystkie błędy powinny być traktowane jako zdarzenia o wysokiej ważności do rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="56b28-148">Not all errors should be treated as high-severity incidents for logging.</span></span>

## <a name="places-where-errors-may-occur"></a><span data-ttu-id="56b28-149">Miejsca, w których mogą wystąpić błędy</span><span class="sxs-lookup"><span data-stu-id="56b28-149">Places where errors may occur</span></span>

<span data-ttu-id="56b28-150">Framework i kod aplikacji może wyzwolić nieobsługiwał wyjątki w dowolnej z następujących lokalizacji:</span><span class="sxs-lookup"><span data-stu-id="56b28-150">Framework and app code may trigger unhandled exceptions in any of the following locations:</span></span>

* [<span data-ttu-id="56b28-151">Tworzenie wystąpienia składników</span><span class="sxs-lookup"><span data-stu-id="56b28-151">Component instantiation</span></span>](#component-instantiation)
* [<span data-ttu-id="56b28-152">Metody cyklu życia</span><span class="sxs-lookup"><span data-stu-id="56b28-152">Lifecycle methods</span></span>](#lifecycle-methods)
* [<span data-ttu-id="56b28-153">Logika renderowania</span><span class="sxs-lookup"><span data-stu-id="56b28-153">Rendering logic</span></span>](#rendering-logic)
* [<span data-ttu-id="56b28-154">Procedury obsługi zdarzeń</span><span class="sxs-lookup"><span data-stu-id="56b28-154">Event handlers</span></span>](#event-handlers)
* [<span data-ttu-id="56b28-155">Utylizacja komponentów</span><span class="sxs-lookup"><span data-stu-id="56b28-155">Component disposal</span></span>](#component-disposal)
* [<span data-ttu-id="56b28-156">Międzyoperacyjność w języku JavaScript</span><span class="sxs-lookup"><span data-stu-id="56b28-156">JavaScript interop</span></span>](#javascript-interop)
* <span data-ttu-id="56b28-157">[BlazorRerendering serwera](#blazor-server-prerendering)</span><span class="sxs-lookup"><span data-stu-id="56b28-157">[Blazor Server rerendering](#blazor-server-prerendering)</span></span>

<span data-ttu-id="56b28-158">Poprzednie nieobsługiwały wyjątki są opisane w poniższych sekcjach tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="56b28-158">The preceding unhandled exceptions are described in the following sections of this article.</span></span>

### <a name="component-instantiation"></a><span data-ttu-id="56b28-159">Tworzenie wystąpienia składników</span><span class="sxs-lookup"><span data-stu-id="56b28-159">Component instantiation</span></span>

<span data-ttu-id="56b28-160">Po Blazor wykonaniu wystąpienia składnika:</span><span class="sxs-lookup"><span data-stu-id="56b28-160">When Blazor creates an instance of a component:</span></span>

* <span data-ttu-id="56b28-161">Konstruktor składnika jest wywoływany.</span><span class="sxs-lookup"><span data-stu-id="56b28-161">The component's constructor is invoked.</span></span>
* <span data-ttu-id="56b28-162">Konstruktory wszystkich usług DI innych niż singleton dostarczane do [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) konstruktora składnika za pośrednictwem dyrektywy lub atrybutu [`[Inject]`](xref:blazor/dependency-injection#request-a-service-in-a-component) są wywoływane.</span><span class="sxs-lookup"><span data-stu-id="56b28-162">The constructors of any non-singleton DI services supplied to the component's constructor via the [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) directive or the [`[Inject]`](xref:blazor/dependency-injection#request-a-service-in-a-component) attribute are invoked.</span></span>

<span data-ttu-id="56b28-163">Obwód Blazor serwera kończy się niepowodzeniem, gdy `[Inject]` dowolny wykonany konstruktor lub ustawiacz dla dowolnej właściwości zgłasza nieobsługiowany wyjątek.</span><span class="sxs-lookup"><span data-stu-id="56b28-163">A Blazor Server circuit fails when any executed constructor or a setter for any `[Inject]` property throws an unhandled exception.</span></span> <span data-ttu-id="56b28-164">Wyjątek jest krytyczny, ponieważ struktura nie może utworzyć wystąpienia składnika.</span><span class="sxs-lookup"><span data-stu-id="56b28-164">The exception is fatal because the framework can't instantiate the component.</span></span> <span data-ttu-id="56b28-165">Jeśli logika konstruktora może zgłaszać wyjątki, aplikacja powinna zalewkować wyjątki przy użyciu [instrukcji try-catch](/dotnet/csharp/language-reference/keywords/try-catch) z obsługą błędów i rejestrowaniem.</span><span class="sxs-lookup"><span data-stu-id="56b28-165">If constructor logic may throw exceptions, the app should trap the exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

### <a name="lifecycle-methods"></a><span data-ttu-id="56b28-166">Metody cyklu życia</span><span class="sxs-lookup"><span data-stu-id="56b28-166">Lifecycle methods</span></span>

<span data-ttu-id="56b28-167">W okresie istnienia składnika Blazor wywołuje następujące metody [cyklu życia:](xref:blazor/lifecycle)</span><span class="sxs-lookup"><span data-stu-id="56b28-167">During the lifetime of a component, Blazor invokes the following [lifecycle methods](xref:blazor/lifecycle):</span></span>

* `OnInitialized` / `OnInitializedAsync`
* `OnParametersSet` / `OnParametersSetAsync`
* `ShouldRender` / `ShouldRenderAsync`
* `OnAfterRender` / `OnAfterRenderAsync`

<span data-ttu-id="56b28-168">Jeśli dowolna metoda cyklu życia zgłasza wyjątek, synchronicznie lub asynchronicznie, wyjątek jest krytyczny dla obwodu Blazor serwera.</span><span class="sxs-lookup"><span data-stu-id="56b28-168">If any lifecycle method throws an exception, synchronously or asynchronously, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="56b28-169">Aby składniki radziły sobie z błędami w metodach cyklu życia, dodaj logikę obsługi błędów.</span><span class="sxs-lookup"><span data-stu-id="56b28-169">For components to deal with errors in lifecycle methods, add error handling logic.</span></span>

<span data-ttu-id="56b28-170">W poniższym `OnParametersSetAsync` przykładzie, w którym wywołuje metodę uzyskania produktu:</span><span class="sxs-lookup"><span data-stu-id="56b28-170">In the following example where `OnParametersSetAsync` calls a method to obtain a product:</span></span>

* <span data-ttu-id="56b28-171">Wyjątek zgłoszony `ProductRepository.GetProductByIdAsync` w metodzie jest `try-catch` obsługiwany przez instrukcję.</span><span class="sxs-lookup"><span data-stu-id="56b28-171">An exception thrown in the `ProductRepository.GetProductByIdAsync` method is handled by a `try-catch` statement.</span></span>
* <span data-ttu-id="56b28-172">Po `catch` wykonaniu bloku:</span><span class="sxs-lookup"><span data-stu-id="56b28-172">When the `catch` block is executed:</span></span>
  * <span data-ttu-id="56b28-173">`loadFailed`jest ustawiona na `true`, który jest używany do wyświetlania komunikatu o błędzie dla użytkownika.</span><span class="sxs-lookup"><span data-stu-id="56b28-173">`loadFailed` is set to `true`, which is used to display an error message to the user.</span></span>
  * <span data-ttu-id="56b28-174">Błąd jest rejestrowany.</span><span class="sxs-lookup"><span data-stu-id="56b28-174">The error is logged.</span></span>

[!code-razor[](handle-errors/samples_snapshot/3.x/product-details.razor?highlight=11,27-39)]

### <a name="rendering-logic"></a><span data-ttu-id="56b28-175">Logika renderowania</span><span class="sxs-lookup"><span data-stu-id="56b28-175">Rendering logic</span></span>

<span data-ttu-id="56b28-176">Znaczników deklaratywnych w pliku składnika `.razor` jest `BuildRenderTree`kompilowany do metody C# o nazwie .</span><span class="sxs-lookup"><span data-stu-id="56b28-176">The declarative markup in a `.razor` component file is compiled into a C# method called `BuildRenderTree`.</span></span> <span data-ttu-id="56b28-177">Gdy składnik renderuje, `BuildRenderTree` wykonuje i buduje strukturę danych opisującą elementy, tekst i składniki podrzędne renderowanego składnika.</span><span class="sxs-lookup"><span data-stu-id="56b28-177">When a component renders, `BuildRenderTree` executes and builds up a data structure describing the elements, text, and child components of the rendered component.</span></span>

<span data-ttu-id="56b28-178">Logika renderowania może zgłosić wyjątek.</span><span class="sxs-lookup"><span data-stu-id="56b28-178">Rendering logic can throw an exception.</span></span> <span data-ttu-id="56b28-179">Przykład tego scenariusza `@someObject.PropertyName` występuje, `@someObject` gdy `null`jest oceniany, ale jest .</span><span class="sxs-lookup"><span data-stu-id="56b28-179">An example of this scenario occurs when `@someObject.PropertyName` is evaluated but `@someObject` is `null`.</span></span> <span data-ttu-id="56b28-180">Nieobsłużony wyjątek zgłaszany przez Blazor logikę renderowania jest krytyczny dla obwodu serwera.</span><span class="sxs-lookup"><span data-stu-id="56b28-180">An unhandled exception thrown by rendering logic is fatal to a Blazor Server circuit.</span></span>

<span data-ttu-id="56b28-181">Aby zapobiec wyjątek odwołania null w logice renderowania, sprawdź obiekt przed dostępem `null` do jego elementów członkowskich.</span><span class="sxs-lookup"><span data-stu-id="56b28-181">To prevent a null reference exception in rendering logic, check for a `null` object before accessing its members.</span></span> <span data-ttu-id="56b28-182">W poniższym `person.Address` przykładzie właściwości nie są `person.Address` `null`dostępne, jeśli jest:</span><span class="sxs-lookup"><span data-stu-id="56b28-182">In the following example, `person.Address` properties aren't accessed if `person.Address` is `null`:</span></span>

[!code-razor[](handle-errors/samples_snapshot/3.x/person-example.razor?highlight=1)]

<span data-ttu-id="56b28-183">Poprzedni kod zakłada, `person` że nie `null`jest .</span><span class="sxs-lookup"><span data-stu-id="56b28-183">The preceding code assumes that `person` isn't `null`.</span></span> <span data-ttu-id="56b28-184">Często struktura kodu gwarantuje, że obiekt istnieje w czasie renderowania składnika.</span><span class="sxs-lookup"><span data-stu-id="56b28-184">Often, the structure of the code guarantees that an object exists at the time the component is rendered.</span></span> <span data-ttu-id="56b28-185">W takich przypadkach nie jest konieczne, `null` aby sprawdzić w logice renderowania.</span><span class="sxs-lookup"><span data-stu-id="56b28-185">In those cases, it isn't necessary to check for `null` in rendering logic.</span></span> <span data-ttu-id="56b28-186">W poprzednim przykładzie może być gwarantowane istnieć, `person` ponieważ `person` jest tworzony podczas tworzenia składnika jest tworzona.</span><span class="sxs-lookup"><span data-stu-id="56b28-186">In the prior example, `person` might be guaranteed to exist because `person` is created when the component is instantiated.</span></span>

### <a name="event-handlers"></a><span data-ttu-id="56b28-187">Procedury obsługi zdarzeń</span><span class="sxs-lookup"><span data-stu-id="56b28-187">Event handlers</span></span>

<span data-ttu-id="56b28-188">Kod po stronie klienta wyzwala wywołania kodu Języka C#, gdy programy obsługi zdarzeń są tworzone przy użyciu:</span><span class="sxs-lookup"><span data-stu-id="56b28-188">Client-side code triggers invocations of C# code when event handlers are created using:</span></span>

* `@onclick`
* `@onchange`
* <span data-ttu-id="56b28-189">Inne `@on...` atrybuty</span><span class="sxs-lookup"><span data-stu-id="56b28-189">Other `@on...` attributes</span></span>
* `@bind`

<span data-ttu-id="56b28-190">Kod obsługi zdarzeń może zgłosić nieobsługiowany wyjątek w tych scenariuszach.</span><span class="sxs-lookup"><span data-stu-id="56b28-190">Event handler code might throw an unhandled exception in these scenarios.</span></span>

<span data-ttu-id="56b28-191">Jeśli program obsługi zdarzeń zgłasza nieobsługiowany wyjątek (na przykład kwerenda Blazor bazy danych kończy się niepowodzeniem), wyjątek jest krytyczny dla obwodu serwera.</span><span class="sxs-lookup"><span data-stu-id="56b28-191">If an event handler throws an unhandled exception (for example, a database query fails), the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="56b28-192">Jeśli aplikacja wywołuje kod, który może zakończyć się niepowodzeniem z przyczyn zewnętrznych, wyjątki pułapki przy użyciu [instrukcji try-catch](/dotnet/csharp/language-reference/keywords/try-catch) z obsługi błędów i rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="56b28-192">If the app calls code that could fail for external reasons, trap exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="56b28-193">Jeśli kod użytkownika nie pułapki i obsługi wyjątku, struktura rejestruje wyjątek i kończy obwód.</span><span class="sxs-lookup"><span data-stu-id="56b28-193">If user code doesn't trap and handle the exception, the framework logs the exception and terminates the circuit.</span></span>

### <a name="component-disposal"></a><span data-ttu-id="56b28-194">Utylizacja komponentów</span><span class="sxs-lookup"><span data-stu-id="56b28-194">Component disposal</span></span>

<span data-ttu-id="56b28-195">Składnik może zostać usunięty z interfejsu użytkownika, na przykład, ponieważ użytkownik przeszedł do innej strony.</span><span class="sxs-lookup"><span data-stu-id="56b28-195">A component may be removed from the UI, for example, because the user has navigated to another page.</span></span> <span data-ttu-id="56b28-196">Gdy składnik, który <xref:System.IDisposable?displayProperty=fullName> implementuje jest usuwany z interfejsu użytkownika, struktura wywołuje metodę składnika. <xref:System.IDisposable.Dispose*></span><span class="sxs-lookup"><span data-stu-id="56b28-196">When a component that implements <xref:System.IDisposable?displayProperty=fullName> is removed from the UI, the framework calls the component's <xref:System.IDisposable.Dispose*> method.</span></span>

<span data-ttu-id="56b28-197">Jeśli składnika `Dispose` metoda zgłasza nieobsługiwać wyjątek, wyjątek jest krytyczny dla obwodu Blazor serwera.</span><span class="sxs-lookup"><span data-stu-id="56b28-197">If the component's `Dispose` method throws an unhandled exception, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="56b28-198">Jeśli logika usuwania może zgłaszać wyjątki, aplikacja powinna zalewkować wyjątki przy użyciu [instrukcji try-catch](/dotnet/csharp/language-reference/keywords/try-catch) z obsługi błędów i rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="56b28-198">If disposal logic may throw exceptions, the app should trap the exceptions using a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="56b28-199">Aby uzyskać więcej informacji <xref:blazor/lifecycle#component-disposal-with-idisposable>na temat usuwania komponentów, zobacz .</span><span class="sxs-lookup"><span data-stu-id="56b28-199">For more information on component disposal, see <xref:blazor/lifecycle#component-disposal-with-idisposable>.</span></span>

### <a name="javascript-interop"></a><span data-ttu-id="56b28-200">Międzyoperacyjność w języku JavaScript</span><span class="sxs-lookup"><span data-stu-id="56b28-200">JavaScript interop</span></span>

<span data-ttu-id="56b28-201">`IJSRuntime.InvokeAsync<T>`umożliwia kodowi .NET wykonywanie wywołań asynchronicznych do środowiska wykonawczego JavaScript w przeglądarce użytkownika.</span><span class="sxs-lookup"><span data-stu-id="56b28-201">`IJSRuntime.InvokeAsync<T>` allows .NET code to make asynchronous calls to the JavaScript runtime in the user's browser.</span></span>

<span data-ttu-id="56b28-202">Następujące warunki mają zastosowanie `InvokeAsync<T>`do obsługi błędów z:</span><span class="sxs-lookup"><span data-stu-id="56b28-202">The following conditions apply to error handling with `InvokeAsync<T>`:</span></span>

* <span data-ttu-id="56b28-203">Jeśli `InvokeAsync<T>` wywołanie nie powiedzie się synchronicznie, wystąpi wyjątek .NET.</span><span class="sxs-lookup"><span data-stu-id="56b28-203">If a call to `InvokeAsync<T>` fails synchronously, a .NET exception occurs.</span></span> <span data-ttu-id="56b28-204">Wywołanie `InvokeAsync<T>` może zakończyć się niepowodzeniem, na przykład, ponieważ dostarczone argumenty nie mogą być serializowane.</span><span class="sxs-lookup"><span data-stu-id="56b28-204">A call to `InvokeAsync<T>` may fail, for example, because the supplied arguments can't be serialized.</span></span> <span data-ttu-id="56b28-205">Kod dewelopera musi przechwytyć wyjątek.</span><span class="sxs-lookup"><span data-stu-id="56b28-205">Developer code must catch the exception.</span></span> <span data-ttu-id="56b28-206">Jeśli kod aplikacji w programie obsługi zdarzeń lub metody cyklu życia składnika nie Blazor obsługuje wyjątek, wynikowy wyjątek jest krytyczny dla obwodu serwera.</span><span class="sxs-lookup"><span data-stu-id="56b28-206">If app code in an event handler or component lifecycle method doesn't handle an exception, the resulting exception is fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="56b28-207">Jeśli wywołanie `InvokeAsync<T>` nie powiedzie się asynchronicznie, .NET <xref:System.Threading.Tasks.Task> kończy się niepowodzeniem.</span><span class="sxs-lookup"><span data-stu-id="56b28-207">If a call to `InvokeAsync<T>` fails asynchronously, the .NET <xref:System.Threading.Tasks.Task> fails.</span></span> <span data-ttu-id="56b28-208">Wywołanie `InvokeAsync<T>` może zakończyć się niepowodzeniem, na przykład, ponieważ kod `Promise` po stronie `rejected`JavaScript zgłasza wyjątek lub zwraca, który został ukończony jako .</span><span class="sxs-lookup"><span data-stu-id="56b28-208">A call to `InvokeAsync<T>` may fail, for example, because the JavaScript-side code throws an exception or returns a `Promise` that completed as `rejected`.</span></span> <span data-ttu-id="56b28-209">Kod dewelopera musi przechwytyć wyjątek.</span><span class="sxs-lookup"><span data-stu-id="56b28-209">Developer code must catch the exception.</span></span> <span data-ttu-id="56b28-210">Jeśli używasz [await](/dotnet/csharp/language-reference/keywords/await) operatora, należy rozważyć zawijanie wywołania metody w [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) instrukcji z obsługi błędów i rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="56b28-210">If using the [await](/dotnet/csharp/language-reference/keywords/await) operator, consider wrapping the method call in a [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span> <span data-ttu-id="56b28-211">W przeciwnym razie kod w przypadku awarii powoduje nieobsługiwać wyjątek, który jest krytyczny dla obwodu Blazor serwera.</span><span class="sxs-lookup"><span data-stu-id="56b28-211">Otherwise, the failing code results in an unhandled exception that's fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="56b28-212">Domyślnie wywołania `InvokeAsync<T>` muszą zakończyć się w określonym czasie lub w przeciwnym razie przesunie się czas połączenia. Domyślny limit czasu wynosi jedną minutę.</span><span class="sxs-lookup"><span data-stu-id="56b28-212">By default, calls to `InvokeAsync<T>` must complete within a certain period or else the call times out. The default timeout period is one minute.</span></span> <span data-ttu-id="56b28-213">Limit czasu chroni kod przed utratą łączności sieciowej lub kodu JavaScript, który nigdy nie wysyła z powrotem komunikatu uzupełniania.</span><span class="sxs-lookup"><span data-stu-id="56b28-213">The timeout protects the code against a loss in network connectivity or JavaScript code that never sends back a completion message.</span></span> <span data-ttu-id="56b28-214">Jeśli przesiąknie `Task` czasu połączenia, wynik nie powiedzie się z <xref:System.OperationCanceledException>.</span><span class="sxs-lookup"><span data-stu-id="56b28-214">If the call times out, the resulting `Task` fails with an <xref:System.OperationCanceledException>.</span></span> <span data-ttu-id="56b28-215">Zalewka i przetwarzać wyjątek z rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="56b28-215">Trap and process the exception with logging.</span></span>

<span data-ttu-id="56b28-216">Podobnie kod JavaScript może inicjować wywołania metod [`[JSInvokable]`](xref:blazor/call-dotnet-from-javascript) platformy .NET wskazanych przez atrybut.</span><span class="sxs-lookup"><span data-stu-id="56b28-216">Similarly, JavaScript code may initiate calls to .NET methods indicated by the [`[JSInvokable]`](xref:blazor/call-dotnet-from-javascript) attribute.</span></span> <span data-ttu-id="56b28-217">Jeśli te metody platformy .NET zgłaszają nieobsługiowany wyjątek:</span><span class="sxs-lookup"><span data-stu-id="56b28-217">If these .NET methods throw an unhandled exception:</span></span>

* <span data-ttu-id="56b28-218">Wyjątek nie jest traktowany jako Blazor krytyczny dla obwodu serwera.</span><span class="sxs-lookup"><span data-stu-id="56b28-218">The exception isn't treated as fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="56b28-219">Strona JavaScript `Promise` zostanie odrzucona.</span><span class="sxs-lookup"><span data-stu-id="56b28-219">The JavaScript-side `Promise` is rejected.</span></span>

<span data-ttu-id="56b28-220">Można użyć kodu obsługi błędów po stronie platformy .NET lub javascript wywołania metody.</span><span class="sxs-lookup"><span data-stu-id="56b28-220">You have the option of using error handling code on either the .NET side or the JavaScript side of the method call.</span></span>

<span data-ttu-id="56b28-221">Aby uzyskać więcej informacji zobacz następujące artykuły:</span><span class="sxs-lookup"><span data-stu-id="56b28-221">For more information, see the following articles:</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

### <a name="opno-locblazor-server-prerendering"></a>Blazor<span data-ttu-id="56b28-222">Prerendering serwera</span><span class="sxs-lookup"><span data-stu-id="56b28-222"> Server prerendering</span></span>

Blazor<span data-ttu-id="56b28-223">składniki mogą być prerendered przy użyciu [Pomocnika tagu składnika,](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) tak aby ich renderowane znaczniki HTML jest zwracany jako część początkowego żądania HTTP użytkownika.</span><span class="sxs-lookup"><span data-stu-id="56b28-223"> components can be prerendered using the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) so that their rendered HTML markup is returned as part of the user's initial HTTP request.</span></span> <span data-ttu-id="56b28-224">Działa to poprzez:</span><span class="sxs-lookup"><span data-stu-id="56b28-224">This works by:</span></span>

* <span data-ttu-id="56b28-225">Tworzenie nowego obwodu dla wszystkich wstępnie powstałych komponentów, które są częścią tej samej strony.</span><span class="sxs-lookup"><span data-stu-id="56b28-225">Creating a new circuit for all of the prerendered components that are part of the same page.</span></span>
* <span data-ttu-id="56b28-226">Generowanie początkowego kodu HTML.</span><span class="sxs-lookup"><span data-stu-id="56b28-226">Generating the initial HTML.</span></span>
* <span data-ttu-id="56b28-227">Traktowanie obwodu `disconnected` jako dopóki przeglądarka użytkownika SignalR nie nawiązuje połączenia z powrotem do tego samego serwera.</span><span class="sxs-lookup"><span data-stu-id="56b28-227">Treating the circuit as `disconnected` until the user's browser establishes a SignalR connection back to the same server.</span></span> <span data-ttu-id="56b28-228">Po nawiązaniu połączenia interakcyjność w obwodzie jest wznawiana, a znaczniki HTML składników są aktualizowane.</span><span class="sxs-lookup"><span data-stu-id="56b28-228">When the connection is established, interactivity on the circuit is resumed and the components' HTML markup is updated.</span></span>

<span data-ttu-id="56b28-229">Jeśli dowolny składnik zgłasza nieobsługiwanie wyjątek podczas prerendering, na przykład podczas metody cyklu życia lub w logice renderowania:</span><span class="sxs-lookup"><span data-stu-id="56b28-229">If any component throws an unhandled exception during prerendering, for example, during a lifecycle method or in rendering logic:</span></span>

* <span data-ttu-id="56b28-230">Wyjątek jest fatalny dla obwodu.</span><span class="sxs-lookup"><span data-stu-id="56b28-230">The exception is fatal to the circuit.</span></span>
* <span data-ttu-id="56b28-231">Wyjątek jest zgłaszany stos `Component` wywołania z Pomocnika tagu.</span><span class="sxs-lookup"><span data-stu-id="56b28-231">The exception is thrown up the call stack from the `Component` Tag Helper.</span></span> <span data-ttu-id="56b28-232">W związku z tym całe żądanie HTTP kończy się niepowodzeniem, chyba że wyjątek jest jawnie złowionych przez kod dewelopera.</span><span class="sxs-lookup"><span data-stu-id="56b28-232">Therefore, the entire HTTP request fails unless the exception is explicitly caught by developer code.</span></span>

<span data-ttu-id="56b28-233">W normalnych warunkach, gdy program prerendering kończy się niepowodzeniem, kontynuowanie tworzenia i renderowania składnika nie ma sensu, ponieważ nie można renderować składnika roboczego.</span><span class="sxs-lookup"><span data-stu-id="56b28-233">Under normal circumstances when prerendering fails, continuing to build and render the component doesn't make sense because a working component can't be rendered.</span></span>

<span data-ttu-id="56b28-234">Aby tolerować błędy, które mogą wystąpić podczas prerendering, logika obsługi błędów musi być umieszczony wewnątrz składnika, który może zgłaszać wyjątki.</span><span class="sxs-lookup"><span data-stu-id="56b28-234">To tolerate errors that may occur during prerendering, error handling logic must be placed inside a component that may throw exceptions.</span></span> <span data-ttu-id="56b28-235">Użyj [instrukcji try-catch](/dotnet/csharp/language-reference/keywords/try-catch) z obsługą błędów i rejestrowaniem.</span><span class="sxs-lookup"><span data-stu-id="56b28-235">Use [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span> <span data-ttu-id="56b28-236">Zamiast zawijać `Component` Pomocnika znacznika w instrukcji, umieść logikę `try-catch` obsługi `Component` błędów w składniku renderowanym przez Pomocnika tagu.</span><span class="sxs-lookup"><span data-stu-id="56b28-236">Instead of wrapping the `Component` Tag Helper in a `try-catch` statement, place error handling logic in the component rendered by the `Component` Tag Helper.</span></span>

## <a name="advanced-scenarios"></a><span data-ttu-id="56b28-237">Scenariusze zaawansowane</span><span class="sxs-lookup"><span data-stu-id="56b28-237">Advanced scenarios</span></span>

### <a name="recursive-rendering"></a><span data-ttu-id="56b28-238">Renderowanie cykliczne</span><span class="sxs-lookup"><span data-stu-id="56b28-238">Recursive rendering</span></span>

<span data-ttu-id="56b28-239">Komponenty mogą być zagnieżdżone rekursywnie.</span><span class="sxs-lookup"><span data-stu-id="56b28-239">Components can be nested recursively.</span></span> <span data-ttu-id="56b28-240">Jest to przydatne do reprezentowania struktur danych cyklicznych.</span><span class="sxs-lookup"><span data-stu-id="56b28-240">This is useful for representing recursive data structures.</span></span> <span data-ttu-id="56b28-241">Na przykład `TreeNode` składnik może `TreeNode` renderować więcej składników dla każdego z elementów podrzędnych węzła.</span><span class="sxs-lookup"><span data-stu-id="56b28-241">For example, a `TreeNode` component can render more `TreeNode` components for each of the node's children.</span></span>

<span data-ttu-id="56b28-242">Podczas renderowania rekursywnie należy unikać wzorców kodowania, które powodują nieskończoną rekursję:</span><span class="sxs-lookup"><span data-stu-id="56b28-242">When rendering recursively, avoid coding patterns that result in infinite recursion:</span></span>

* <span data-ttu-id="56b28-243">Nie cyklicznie renderować struktury danych, która zawiera cykl.</span><span class="sxs-lookup"><span data-stu-id="56b28-243">Don't recursively render a data structure that contains a cycle.</span></span> <span data-ttu-id="56b28-244">Na przykład nie renderuj węzła drzewa, którego dzieci zawiera się.</span><span class="sxs-lookup"><span data-stu-id="56b28-244">For example, don't render a tree node whose children includes itself.</span></span>
* <span data-ttu-id="56b28-245">Nie należy tworzyć łańcucha układów, które zawierają cykl.</span><span class="sxs-lookup"><span data-stu-id="56b28-245">Don't create a chain of layouts that contain a cycle.</span></span> <span data-ttu-id="56b28-246">Na przykład nie należy tworzyć układu, którego układ jest sam.</span><span class="sxs-lookup"><span data-stu-id="56b28-246">For example, don't create a layout whose layout is itself.</span></span>
* <span data-ttu-id="56b28-247">Nie zezwalaj użytkownikowi końcowemu na naruszanie invariants recursion (reguł) za pośrednictwem złośliwego wprowadzania danych lub wywołań interop JavaScript.</span><span class="sxs-lookup"><span data-stu-id="56b28-247">Don't allow an end user to violate recursion invariants (rules) through malicious data entry or JavaScript interop calls.</span></span>

<span data-ttu-id="56b28-248">Nieskończone pętle podczas renderowania:</span><span class="sxs-lookup"><span data-stu-id="56b28-248">Infinite loops during rendering:</span></span>

* <span data-ttu-id="56b28-249">Powoduje, że proces renderowania trwa wiecznie.</span><span class="sxs-lookup"><span data-stu-id="56b28-249">Causes the rendering process to continue forever.</span></span>
* <span data-ttu-id="56b28-250">Jest odpowiednikiem tworzenia pętli nieokreślonej.</span><span class="sxs-lookup"><span data-stu-id="56b28-250">Is equivalent to creating an unterminated loop.</span></span>

<span data-ttu-id="56b28-251">W tych scenariuszach obwód Blazor serwera, którego dotyczy problem, kończy się niepowodzeniem, a wątek zwykle próbuje:</span><span class="sxs-lookup"><span data-stu-id="56b28-251">In these scenarios, an affected Blazor Server circuit fails, and the thread usually attempts to:</span></span>

* <span data-ttu-id="56b28-252">Zużywają tyle czasu procesora, ile pozwala system operacyjny, przez czas nieokreślony.</span><span class="sxs-lookup"><span data-stu-id="56b28-252">Consume as much CPU time as permitted by the operating system, indefinitely.</span></span>
* <span data-ttu-id="56b28-253">Zużywają nieograniczoną ilość pamięci serwera.</span><span class="sxs-lookup"><span data-stu-id="56b28-253">Consume an unlimited amount of server memory.</span></span> <span data-ttu-id="56b28-254">Korzystanie z pamięci nieograniczonej jest odpowiednikiem scenariusza, w którym nieokreślona pętla dodaje wpisy do kolekcji w każdej iteracji.</span><span class="sxs-lookup"><span data-stu-id="56b28-254">Consuming unlimited memory is equivalent to the scenario where an unterminated loop adds entries to a collection on every iteration.</span></span>

<span data-ttu-id="56b28-255">Aby uniknąć nieskończonych wzorców rekursji, upewnij się, że rekursywny kod renderowania zawiera odpowiednie warunki zatrzymania.</span><span class="sxs-lookup"><span data-stu-id="56b28-255">To avoid infinite recursion patterns, ensure that recursive rendering code contains suitable stopping conditions.</span></span>

### <a name="custom-render-tree-logic"></a><span data-ttu-id="56b28-256">Niestandardowa logika drzewa renderowania</span><span class="sxs-lookup"><span data-stu-id="56b28-256">Custom render tree logic</span></span>

<span data-ttu-id="56b28-257">Większość Blazor składników są implementowane jako pliki *.brzytwa* i są `RenderTreeBuilder` kompilowane do tworzenia logiki, która działa na a do renderowania ich danych wyjściowych.</span><span class="sxs-lookup"><span data-stu-id="56b28-257">Most Blazor components are implemented as *.razor* files and are compiled to produce logic that operates on a `RenderTreeBuilder` to render their output.</span></span> <span data-ttu-id="56b28-258">Deweloper może ręcznie `RenderTreeBuilder` zaimplementować logikę przy użyciu proceduralnego kodu języka C#.</span><span class="sxs-lookup"><span data-stu-id="56b28-258">A developer may manually implement `RenderTreeBuilder` logic using procedural C# code.</span></span> <span data-ttu-id="56b28-259">Aby uzyskać więcej informacji, zobacz <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span><span class="sxs-lookup"><span data-stu-id="56b28-259">For more information, see <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span></span>

> [!WARNING]
> <span data-ttu-id="56b28-260">Korzystanie z logiki konstruktora drzewa renderowania ręcznego jest uważany za zaawansowany i niebezpieczny scenariusz, nie zalecane dla ogólnego rozwoju składników.</span><span class="sxs-lookup"><span data-stu-id="56b28-260">Use of manual render tree builder logic is considered an advanced and unsafe scenario, not recommended for general component development.</span></span>

<span data-ttu-id="56b28-261">Jeśli `RenderTreeBuilder` kod jest napisany, deweloper musi zagwarantować poprawność kodu.</span><span class="sxs-lookup"><span data-stu-id="56b28-261">If `RenderTreeBuilder` code is written, the developer must guarantee the correctness of the code.</span></span> <span data-ttu-id="56b28-262">Na przykład deweloper musi upewnić się, że:</span><span class="sxs-lookup"><span data-stu-id="56b28-262">For example, the developer must ensure that:</span></span>

* <span data-ttu-id="56b28-263">Połączenia `OpenElement` do `CloseElement` i są prawidłowo wyważone.</span><span class="sxs-lookup"><span data-stu-id="56b28-263">Calls to `OpenElement` and `CloseElement` are correctly balanced.</span></span>
* <span data-ttu-id="56b28-264">Atrybuty są dodawane tylko w odpowiednich miejscach.</span><span class="sxs-lookup"><span data-stu-id="56b28-264">Attributes are only added in the correct places.</span></span>

<span data-ttu-id="56b28-265">Niepoprawna logika konstruktora drzew renderowania ręcznego może powodować dowolne niezdefiniowane zachowanie, w tym awarie, zawieszanie się serwera i luki w zabezpieczeniach.</span><span class="sxs-lookup"><span data-stu-id="56b28-265">Incorrect manual render tree builder logic can cause arbitrary undefined behavior, including crashes, server hangs, and security vulnerabilities.</span></span>

<span data-ttu-id="56b28-266">Należy wziąć pod uwagę ręczne renderowania logiki konstruktora drzewa na tym samym poziomie złożoności i z tym samym poziomie *niebezpieczeństwa,* jak pisanie kodu zestawu lub instrukcje MSIL ręcznie.</span><span class="sxs-lookup"><span data-stu-id="56b28-266">Consider manual render tree builder logic on the same level of complexity and with the same level of *danger* as writing assembly code or MSIL instructions by hand.</span></span>
