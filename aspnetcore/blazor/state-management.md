---
title: BlazorZarządzanie stanem ASP.NET Core
author: guardrex
description: Dowiedz się, jak utrwalać stan w Blazor Server aplikacjach.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2020
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
uid: blazor/state-management
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 5b7a6fefd13d7255fbe3ae7010df48c6211fa755
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "97854341"
---
# <a name="aspnet-core-no-locblazor-state-management"></a><span data-ttu-id="57fac-103">BlazorZarządzanie stanem ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="57fac-103">ASP.NET Core Blazor state management</span></span>

<span data-ttu-id="57fac-104">[Steve Sanderson](https://github.com/SteveSandersonMS) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="57fac-104">By [Steve Sanderson](https://github.com/SteveSandersonMS) and [Luke Latham](https://github.com/guardrex)</span></span>

::: zone pivot="webassembly"

<span data-ttu-id="57fac-105">Stan użytkownika utworzony w Blazor WebAssembly aplikacji jest przechowywany w pamięci przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="57fac-105">User state created in a Blazor WebAssembly app is held in the browser's memory.</span></span>

<span data-ttu-id="57fac-106">Przykłady stanu użytkownika znajdującego się w pamięci przeglądarki obejmują:</span><span class="sxs-lookup"><span data-stu-id="57fac-106">Examples of user state held in browser memory include:</span></span>

* <span data-ttu-id="57fac-107">Hierarchia wystąpień składników i ich najnowsze dane wyjściowe renderowania w renderowanym interfejsie użytkownika.</span><span class="sxs-lookup"><span data-stu-id="57fac-107">The hierarchy of component instances and their most recent render output in the rendered UI.</span></span>
* <span data-ttu-id="57fac-108">Wartości pól i właściwości w wystąpieniach składników.</span><span class="sxs-lookup"><span data-stu-id="57fac-108">The values of fields and properties in component instances.</span></span>
* <span data-ttu-id="57fac-109">Dane przechowywane w wystąpieniach usługi [wtrysku zależności (di)](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="57fac-109">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances.</span></span>
* <span data-ttu-id="57fac-110">Wartości ustawione za poorednictwem wywołań [międzyoperacyjnych języka JavaScript](xref:blazor/call-javascript-from-dotnet) .</span><span class="sxs-lookup"><span data-stu-id="57fac-110">Values set through [JavaScript interop](xref:blazor/call-javascript-from-dotnet) calls.</span></span>

<span data-ttu-id="57fac-111">Gdy użytkownik zamknie i ponownie otworzy swoją przeglądarkę lub załaduje stronę, stan użytkownika znajdujący się w pamięci przeglądarki zostanie utracony.</span><span class="sxs-lookup"><span data-stu-id="57fac-111">When a user closes and re-opens their browser or reloads the page, user state held in the browser's memory is lost.</span></span>

> [!NOTE]
> <span data-ttu-id="57fac-112">[Chroniony magazyn przeglądarki](xref:blazor/state-management?pivots=server#aspnet-core-protected-browser-storage) ( <xref:Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage?displayProperty=fullName> przestrzeń nazw) opiera się na ASP.NET Core ochrony danych i jest obsługiwany tylko w przypadku Blazor Server aplikacji.</span><span class="sxs-lookup"><span data-stu-id="57fac-112">[Protected Browser Storage](xref:blazor/state-management?pivots=server#aspnet-core-protected-browser-storage) (<xref:Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage?displayProperty=fullName> namespace) relies on ASP.NET Core Data Protection and is only supported for Blazor Server apps.</span></span>

## <a name="persist-state-across-browser-sessions"></a><span data-ttu-id="57fac-113">Stan utrwalania w sesjach przeglądarki</span><span class="sxs-lookup"><span data-stu-id="57fac-113">Persist state across browser sessions</span></span>

<span data-ttu-id="57fac-114">Ogólnie rzecz biorąc, utrzymuje stan w sesjach przeglądarki, w których użytkownicy aktywnie tworzą dane, a nie tylko odczytujące dane, które już istnieją.</span><span class="sxs-lookup"><span data-stu-id="57fac-114">Generally, maintain state across browser sessions where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="57fac-115">Aby zachować stan między sesjami przeglądarki, aplikacja musi przechowywać dane w innej lokalizacji magazynu niż pamięć przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="57fac-115">To preserve state across browser sessions, the app must persist the data to some other storage location than the browser's memory.</span></span> <span data-ttu-id="57fac-116">Trwałość stanu nie jest automatyczna.</span><span class="sxs-lookup"><span data-stu-id="57fac-116">State persistence isn't automatic.</span></span> <span data-ttu-id="57fac-117">Podczas tworzenia aplikacji należy wykonać kroki w celu zaimplementowania stanu trwałości danych stanowych.</span><span class="sxs-lookup"><span data-stu-id="57fac-117">You must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="57fac-118">Trwałość danych jest zazwyczaj wymagana tylko w przypadku stanu wysokiej wartości, który użytkownicy wystawią nakłady na tworzenie.</span><span class="sxs-lookup"><span data-stu-id="57fac-118">Data persistence is typically only required for high-value state that users expended effort to create.</span></span> <span data-ttu-id="57fac-119">W poniższych przykładach stan utrwalania polega na zapisywaniu czasu lub pomocy w działaniach komercyjnych:</span><span class="sxs-lookup"><span data-stu-id="57fac-119">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="57fac-120">Wieloetapowe formularze sieci Web: czasochłonne, aby użytkownik mógł ponownie wprowadzić dane dla kilku ukończonych kroków wieloetapowego formularza sieci Web, jeśli ich stan zostanie utracony.</span><span class="sxs-lookup"><span data-stu-id="57fac-120">Multi-step web forms: It's time-consuming for a user to re-enter data for several completed steps of a multi-step web form if their state is lost.</span></span> <span data-ttu-id="57fac-121">Użytkownik utraci stan w tym scenariuszu, jeśli opuszcza formularz i wróci później.</span><span class="sxs-lookup"><span data-stu-id="57fac-121">A user loses state in this scenario if they navigate away from the form and return later.</span></span>
* <span data-ttu-id="57fac-122">Koszyki: każdy handlowy istotny składnik aplikacji, który reprezentuje potencjalne dochody, może być utrzymywany.</span><span class="sxs-lookup"><span data-stu-id="57fac-122">Shopping carts: Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="57fac-123">Użytkownik, który straci swój stan, a tym samym koszyk, może zakupić mniejszą liczbę produktów lub usług w momencie powrotu do lokacji w przyszłości.</span><span class="sxs-lookup"><span data-stu-id="57fac-123">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="57fac-124">Aplikacja może utrzymywać tylko *stan aplikacji*.</span><span class="sxs-lookup"><span data-stu-id="57fac-124">An app can only persist *app state*.</span></span> <span data-ttu-id="57fac-125">Interfejsów użytkownika nie mogą być utrwalane, takie jak wystąpienia składników i ich drzewa renderowania.</span><span class="sxs-lookup"><span data-stu-id="57fac-125">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="57fac-126">Składniki i drzewa renderowania nie są generalnie serializowane.</span><span class="sxs-lookup"><span data-stu-id="57fac-126">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="57fac-127">Aby zachować stan interfejsu użytkownika, na przykład rozwinięte węzły kontrolki widoku drzewa, aplikacja musi używać niestandardowego kodu do modelowania zachowania stanu interfejsu użytkownika jako możliwy do serializacji stan aplikacji.</span><span class="sxs-lookup"><span data-stu-id="57fac-127">To persist UI state, such as the expanded nodes of a tree view control, the app must use custom code to model the behavior of the UI state as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="57fac-128">Gdzie będzie trwały stan</span><span class="sxs-lookup"><span data-stu-id="57fac-128">Where to persist state</span></span>

<span data-ttu-id="57fac-129">Istnieją wspólne lokalizacje dla stanu utrwalania:</span><span class="sxs-lookup"><span data-stu-id="57fac-129">Common locations exist for persisting state:</span></span>

* [<span data-ttu-id="57fac-130">Magazyn po stronie serwera</span><span class="sxs-lookup"><span data-stu-id="57fac-130">Server-side storage</span></span>](#server-side-storage-wasm)
* [<span data-ttu-id="57fac-131">Adres URL</span><span class="sxs-lookup"><span data-stu-id="57fac-131">URL</span></span>](#url-wasm)
* [<span data-ttu-id="57fac-132">Magazyn przeglądarki</span><span class="sxs-lookup"><span data-stu-id="57fac-132">Browser storage</span></span>](#browser-storage-wasm)
* [<span data-ttu-id="57fac-133">Usługa kontenera stanu w pamięci</span><span class="sxs-lookup"><span data-stu-id="57fac-133">In-memory state container service</span></span>](#in-memory-state-container-service-wasm)

<h2 id="server-side-storage-wasm"><span data-ttu-id="57fac-134">Magazyn po stronie serwera</span><span class="sxs-lookup"><span data-stu-id="57fac-134">Server-side storage</span></span></h2>

<span data-ttu-id="57fac-135">W przypadku trwałej trwałości danych obejmującej wielu użytkowników i wszystkie urządzenia aplikacja może używać niezależnego magazynu po stronie serwera, dostępnego za pośrednictwem internetowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="57fac-135">For permanent data persistence that spans multiple users and devices, the app can use independent server-side storage accessed via a web API.</span></span> <span data-ttu-id="57fac-136">Dostępne opcje:</span><span class="sxs-lookup"><span data-stu-id="57fac-136">Options include:</span></span>

* <span data-ttu-id="57fac-137">Blob Storage</span><span class="sxs-lookup"><span data-stu-id="57fac-137">Blob storage</span></span>
* <span data-ttu-id="57fac-138">Magazyn kluczy i wartości</span><span class="sxs-lookup"><span data-stu-id="57fac-138">Key-value storage</span></span>
* <span data-ttu-id="57fac-139">Relacyjna baza danych</span><span class="sxs-lookup"><span data-stu-id="57fac-139">Relational database</span></span>
* <span data-ttu-id="57fac-140">Magazyn tabel</span><span class="sxs-lookup"><span data-stu-id="57fac-140">Table storage</span></span>

<span data-ttu-id="57fac-141">Po zapisaniu danych stan użytkownika jest zachowywany i dostępny w każdej nowej sesji przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="57fac-141">After data is saved, the user's state is retained and available in any new browser session.</span></span>

<span data-ttu-id="57fac-142">Ponieważ Blazor WebAssembly aplikacje działają w całości w przeglądarce użytkownika, wymagają dodatkowych środków w celu uzyskania dostępu do bezpiecznych systemów zewnętrznych, takich jak usługi magazynu i bazy danych.</span><span class="sxs-lookup"><span data-stu-id="57fac-142">Because Blazor WebAssembly apps run entirely in the user's browser, they require additional measures to access secure external systems, such as storage services and databases.</span></span> <span data-ttu-id="57fac-143">Blazor WebAssembly aplikacje są zabezpieczone w taki sam sposób, jak aplikacje jednostronicowe (aplikacji jednostronicowych).</span><span class="sxs-lookup"><span data-stu-id="57fac-143">Blazor WebAssembly apps are secured in the same manner as Single Page Applications (SPAs).</span></span> <span data-ttu-id="57fac-144">Zazwyczaj aplikacja uwierzytelnia użytkownika za pośrednictwem protokołu [OAuth](https://oauth.net) / [OpenID Connect Connect (OIDC)](https://openid.net/connect/) , a następnie współdziała z usługami magazynu i bazami danych za pośrednictwem wywołań interfejsu API sieci Web dla aplikacji po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="57fac-144">Typically, an app authenticates a user via [OAuth](https://oauth.net)/[OpenID Connect (OIDC)](https://openid.net/connect/) and then interacts with storage services and databases through web API calls to a server-side app.</span></span> <span data-ttu-id="57fac-145">Aplikacja po stronie serwera koryguje transfer danych między Blazor WebAssembly aplikacją a usługą magazynu lub bazą danych.</span><span class="sxs-lookup"><span data-stu-id="57fac-145">The server-side app mediates the transfer of data between the Blazor WebAssembly app and the storage service or database.</span></span> <span data-ttu-id="57fac-146">Blazor WebAssemblyAplikacja zachowuje tymczasowe połączenie z aplikacją po stronie serwera, podczas gdy aplikacja po stronie serwera ma trwałe połączenie z magazynem.</span><span class="sxs-lookup"><span data-stu-id="57fac-146">The Blazor WebAssembly app maintains an ephemeral connection to the server-side app, while the server-side app has a persistent connection to storage.</span></span>

<span data-ttu-id="57fac-147">Więcej informacji można znaleźć w następujących zasobach:</span><span class="sxs-lookup"><span data-stu-id="57fac-147">For more information, see the following resources:</span></span>

* <xref:blazor/call-web-api>
* <xref:blazor/security/webassembly/index>
* <span data-ttu-id="57fac-148">Blazor*Zabezpieczenia i Identity* artykułu</span><span class="sxs-lookup"><span data-stu-id="57fac-148">Blazor *Security and Identity* articles</span></span>

<span data-ttu-id="57fac-149">Aby uzyskać więcej informacji na temat opcji usługi Azure Data Storage, zobacz następujące tematy:</span><span class="sxs-lookup"><span data-stu-id="57fac-149">For more information on Azure data storage options, see the following:</span></span>

* [<span data-ttu-id="57fac-150">Bazy danych platformy Azure</span><span class="sxs-lookup"><span data-stu-id="57fac-150">Azure Databases</span></span>](https://azure.microsoft.com/product-categories/databases/)
* [<span data-ttu-id="57fac-151">Dokumentacja usługi Azure Storage</span><span class="sxs-lookup"><span data-stu-id="57fac-151">Azure Storage Documentation</span></span>](/azure/storage/)

<h2 id="url-wasm"><span data-ttu-id="57fac-152">Adres URL</span><span class="sxs-lookup"><span data-stu-id="57fac-152">URL</span></span></h2>

<span data-ttu-id="57fac-153">W przypadku danych przejściowych reprezentujących stan nawigacji należy modelować dane w ramach adresu URL.</span><span class="sxs-lookup"><span data-stu-id="57fac-153">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="57fac-154">Przykłady stanu użytkownika z modelem w adresie URL obejmują:</span><span class="sxs-lookup"><span data-stu-id="57fac-154">Examples of user state modeled in the URL include:</span></span>

* <span data-ttu-id="57fac-155">Identyfikator wyświetlonej jednostki.</span><span class="sxs-lookup"><span data-stu-id="57fac-155">The ID of a viewed entity.</span></span>
* <span data-ttu-id="57fac-156">Numer bieżącej strony w siatce stronicowanej.</span><span class="sxs-lookup"><span data-stu-id="57fac-156">The current page number in a paged grid.</span></span>

<span data-ttu-id="57fac-157">Zawartość paska adresu przeglądarki jest zachowywana, jeśli użytkownik ręcznie ponownie załaduje stronę.</span><span class="sxs-lookup"><span data-stu-id="57fac-157">The contents of the browser's address bar are retained if the user manually reloads the page.</span></span>

<span data-ttu-id="57fac-158">Aby uzyskać informacje na temat definiowania wzorców adresów URL za pomocą [`@page`](xref:mvc/views/razor#page) dyrektywy, zobacz <xref:blazor/fundamentals/routing> .</span><span class="sxs-lookup"><span data-stu-id="57fac-158">For information on defining URL patterns with the [`@page`](xref:mvc/views/razor#page) directive, see <xref:blazor/fundamentals/routing>.</span></span>

<h2 id="browser-storage-wasm"><span data-ttu-id="57fac-159">Magazyn przeglądarki</span><span class="sxs-lookup"><span data-stu-id="57fac-159">Browser storage</span></span></h2>

<span data-ttu-id="57fac-160">W przypadku danych przejściowych, które użytkownik aktywnie tworzy, często używaną lokalizacją magazynu jest przeglądarka [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) i [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) Kolekcje:</span><span class="sxs-lookup"><span data-stu-id="57fac-160">For transient data that the user is actively creating, a commonly used storage location is the browser's [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) collections:</span></span>

* <span data-ttu-id="57fac-161">`localStorage` jest zakresem okna przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="57fac-161">`localStorage` is scoped to the browser's window.</span></span> <span data-ttu-id="57fac-162">Jeśli użytkownik ponownie załaduje stronę lub zamknie i otworzy przeglądarkę, stan będzie się utrzymywał.</span><span class="sxs-lookup"><span data-stu-id="57fac-162">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="57fac-163">Jeśli użytkownik otworzy wiele kart przeglądarki, stan jest udostępniany na kartach.</span><span class="sxs-lookup"><span data-stu-id="57fac-163">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="57fac-164">Dane są zachowywane `localStorage` do momentu jawnego wyczyszczenia.</span><span class="sxs-lookup"><span data-stu-id="57fac-164">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="57fac-165">`sessionStorage` jest zakresem na karcie przeglądarki. Jeśli użytkownik ponownie załaduje kartę, stan będzie się utrzymywał.</span><span class="sxs-lookup"><span data-stu-id="57fac-165">`sessionStorage` is scoped to the browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="57fac-166">Jeśli użytkownik zamknie kartę lub przeglądarkę, stan zostanie utracony.</span><span class="sxs-lookup"><span data-stu-id="57fac-166">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="57fac-167">Jeśli użytkownik otworzy wiele kart przeglądarki, każda karta ma własną niezależną wersję danych.</span><span class="sxs-lookup"><span data-stu-id="57fac-167">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

> [!NOTE]
> <span data-ttu-id="57fac-168">`localStorage` i `sessionStorage` mogą być używane w Blazor WebAssembly aplikacjach, ale tylko przez napisanie kodu niestandardowego lub użycie pakietu innej firmy.</span><span class="sxs-lookup"><span data-stu-id="57fac-168">`localStorage` and `sessionStorage` can be used in Blazor WebAssembly apps but only by writing custom code or using a third-party package.</span></span>

<span data-ttu-id="57fac-169">Ogólnie rzecz biorąc, `sessionStorage` jest bezpiecznie używać.</span><span class="sxs-lookup"><span data-stu-id="57fac-169">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="57fac-170">`sessionStorage` pozwala uniknąć ryzyka, że użytkownik otwiera wiele kart i napotyka następujące kwestie:</span><span class="sxs-lookup"><span data-stu-id="57fac-170">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="57fac-171">Błędy w magazynie Stanów na kartach.</span><span class="sxs-lookup"><span data-stu-id="57fac-171">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="57fac-172">Zachowanie mylące, gdy karta zastępuje stan innych kart.</span><span class="sxs-lookup"><span data-stu-id="57fac-172">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="57fac-173">`localStorage` jest lepszym rozwiązaniem, jeśli aplikacja musi utrzymywać stan w trakcie zamykania i ponownego otwierania przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="57fac-173">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

> [!WARNING]
> <span data-ttu-id="57fac-174">Użytkownicy mogą wyświetlać lub naruszać dane przechowywane w `localStorage` i `sessionStorage` .</span><span class="sxs-lookup"><span data-stu-id="57fac-174">Users may view or tamper with the data stored in `localStorage` and `sessionStorage`.</span></span>

<h2 id="in-memory-state-container-service-wasm"><span data-ttu-id="57fac-175">Usługa kontenera stanu w pamięci</span><span class="sxs-lookup"><span data-stu-id="57fac-175">In-memory state container service</span></span></h2>

[!INCLUDE[](~/blazor/includes/state-container.md)]

## <a name="additional-resources"></a><span data-ttu-id="57fac-176">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="57fac-176">Additional resources</span></span>

* [<span data-ttu-id="57fac-177">Zapisz stan aplikacji przed operacją uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="57fac-177">Save app state before an authentication operation</span></span>](xref:blazor/security/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation)
* <xref:blazor/call-web-api>
* <xref:blazor/security/webassembly/index>

::: zone-end

::: zone pivot="server"

<span data-ttu-id="57fac-178">Blazor Server jest platformą aplikacji stanowych.</span><span class="sxs-lookup"><span data-stu-id="57fac-178">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="57fac-179">W większości przypadków aplikacja utrzymuje połączenie z serwerem.</span><span class="sxs-lookup"><span data-stu-id="57fac-179">Most of the time, the app maintains a connection to the server.</span></span> <span data-ttu-id="57fac-180">Stan użytkownika jest przechowywany w pamięci serwera w ramach *obwodu*.</span><span class="sxs-lookup"><span data-stu-id="57fac-180">The user's state is held in the server's memory in a *circuit*.</span></span> 

<span data-ttu-id="57fac-181">Przykłady stanu użytkownika przechowywanego w obwodzie:</span><span class="sxs-lookup"><span data-stu-id="57fac-181">Examples of user state held in a circuit include:</span></span>

* <span data-ttu-id="57fac-182">Hierarchia wystąpień składników i ich najnowsze dane wyjściowe renderowania w renderowanym interfejsie użytkownika.</span><span class="sxs-lookup"><span data-stu-id="57fac-182">The hierarchy of component instances and their most recent render output in the rendered UI.</span></span>
* <span data-ttu-id="57fac-183">Wartości pól i właściwości w wystąpieniach składników.</span><span class="sxs-lookup"><span data-stu-id="57fac-183">The values of fields and properties in component instances.</span></span>
* <span data-ttu-id="57fac-184">Dane przechowywane w wystąpieniach usługi [wtrysku zależności (di)](xref:fundamentals/dependency-injection) , które są objęte zakresem obwodu.</span><span class="sxs-lookup"><span data-stu-id="57fac-184">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span>

<span data-ttu-id="57fac-185">Stan użytkownika może również znajdować się w zmiennych JavaScript w zestawie pamięci w przeglądarce za pośrednictwem wywołań [międzyoperacyjnych języka JavaScript](xref:blazor/call-javascript-from-dotnet) .</span><span class="sxs-lookup"><span data-stu-id="57fac-185">User state might also be found in JavaScript variables in the browser's memory set via [JavaScript interop](xref:blazor/call-javascript-from-dotnet) calls.</span></span>

<span data-ttu-id="57fac-186">Jeśli użytkownik napotyka chwilową utratę połączenia sieciowego, program Blazor spróbuje ponownie połączyć użytkownika z oryginalnym obwodem.</span><span class="sxs-lookup"><span data-stu-id="57fac-186">If a user experiences a temporary network connection loss, Blazor attempts to reconnect the user to their original circuit with their original state.</span></span> <span data-ttu-id="57fac-187">Jednak ponowne połączenie użytkownika z oryginalnym obwodem w pamięci serwera nie zawsze jest możliwe:</span><span class="sxs-lookup"><span data-stu-id="57fac-187">However, reconnecting a user to their original circuit in the server's memory isn't always possible:</span></span>

* <span data-ttu-id="57fac-188">Serwer nie może całkowicie zachować rozłączonego obwodu.</span><span class="sxs-lookup"><span data-stu-id="57fac-188">The server can't retain a disconnected circuit forever.</span></span> <span data-ttu-id="57fac-189">Serwer musi zwolnić obwód rozłączony po upływie limitu czasu lub w przypadku, gdy na serwerze znajduje się pamięć.</span><span class="sxs-lookup"><span data-stu-id="57fac-189">The server must release a disconnected circuit after a timeout or when the server is under memory pressure.</span></span>
* <span data-ttu-id="57fac-190">W środowiskach wdrażania z obsługą równoważenia obciążenia poszczególne serwery mogą kończyć się niepowodzeniem lub być automatycznie usuwane, gdy nie są już wymagane do obsługi ogólnej liczby żądań.</span><span class="sxs-lookup"><span data-stu-id="57fac-190">In multi-server, load-balanced deployment environments, individual servers may fail or be automatically removed when no longer required to handle the overall volume of requests.</span></span> <span data-ttu-id="57fac-191">Oryginalne żądania przetwarzania serwera dla użytkownika mogą stać się niedostępne, gdy użytkownik spróbuje ponownie nawiązać połączenie.</span><span class="sxs-lookup"><span data-stu-id="57fac-191">The original server processing requests for a user may become unavailable when the user attempts to reconnect.</span></span>
* <span data-ttu-id="57fac-192">Użytkownik może zamknąć i ponownie otworzyć przeglądarkę lub ponownie załadować stronę, co spowoduje usunięcie wszystkich stanów przechowywanych w pamięci przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="57fac-192">The user might close and re-open their browser or reload the page, which removes any state held in the browser's memory.</span></span> <span data-ttu-id="57fac-193">Na przykład wartości zmiennych JavaScript ustawione za poorednictwem wywołań międzyoperacyjnych języka JavaScript są tracone.</span><span class="sxs-lookup"><span data-stu-id="57fac-193">For example, JavaScript variable values set through JavaScript interop calls are lost.</span></span>

<span data-ttu-id="57fac-194">Gdy nie można ponownie nawiązać połączenia użytkownika z oryginalnym obwodem, użytkownik otrzymuje nowy obwód z pustym stanem.</span><span class="sxs-lookup"><span data-stu-id="57fac-194">When a user can't be reconnected to their original circuit, the user receives a new circuit with an empty state.</span></span> <span data-ttu-id="57fac-195">Jest to równoznaczne z zamknięciem i ponownym otwarciem aplikacji klasycznej.</span><span class="sxs-lookup"><span data-stu-id="57fac-195">This is equivalent to closing and re-opening a desktop app.</span></span>

## <a name="persist-state-across-circuits"></a><span data-ttu-id="57fac-196">Stan utrwalania w obwodach</span><span class="sxs-lookup"><span data-stu-id="57fac-196">Persist state across circuits</span></span>

<span data-ttu-id="57fac-197">Ogólnie rzecz biorąc, utrzymuje stan w obwodach, w których użytkownicy aktywnie tworzą dane, a nie tylko odczytujące dane, które już istnieją.</span><span class="sxs-lookup"><span data-stu-id="57fac-197">Generally, maintain state across circuits where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="57fac-198">Aby zachować stan w obwodach, aplikacja musi utrzymywać dane w innej lokalizacji magazynu niż pamięć serwera.</span><span class="sxs-lookup"><span data-stu-id="57fac-198">To preserve state across circuits, the app must persist the data to some other storage location than the server's memory.</span></span> <span data-ttu-id="57fac-199">Trwałość stanu nie jest automatyczna.</span><span class="sxs-lookup"><span data-stu-id="57fac-199">State persistence isn't automatic.</span></span> <span data-ttu-id="57fac-200">Podczas tworzenia aplikacji należy wykonać kroki w celu zaimplementowania stanu trwałości danych stanowych.</span><span class="sxs-lookup"><span data-stu-id="57fac-200">You must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="57fac-201">Trwałość danych jest zazwyczaj wymagana tylko w przypadku stanu wysokiej wartości, który użytkownicy wystawią nakłady na tworzenie.</span><span class="sxs-lookup"><span data-stu-id="57fac-201">Data persistence is typically only required for high-value state that users expended effort to create.</span></span> <span data-ttu-id="57fac-202">W poniższych przykładach stan utrwalania polega na zapisywaniu czasu lub pomocy w działaniach komercyjnych:</span><span class="sxs-lookup"><span data-stu-id="57fac-202">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="57fac-203">Wieloetapowe formularze sieci Web: czasochłonne, aby użytkownik mógł ponownie wprowadzić dane dla kilku ukończonych kroków wieloetapowego formularza sieci Web, jeśli ich stan zostanie utracony.</span><span class="sxs-lookup"><span data-stu-id="57fac-203">Multi-step web forms: It's time-consuming for a user to re-enter data for several completed steps of a multi-step web form if their state is lost.</span></span> <span data-ttu-id="57fac-204">Użytkownik utraci stan w tym scenariuszu, jeśli opuszcza formularz i wróci później.</span><span class="sxs-lookup"><span data-stu-id="57fac-204">A user loses state in this scenario if they navigate away from the form and return later.</span></span>
* <span data-ttu-id="57fac-205">Koszyki: każdy handlowy istotny składnik aplikacji, który reprezentuje potencjalne dochody, może być utrzymywany.</span><span class="sxs-lookup"><span data-stu-id="57fac-205">Shopping carts: Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="57fac-206">Użytkownik, który straci swój stan, a tym samym koszyk, może zakupić mniejszą liczbę produktów lub usług w momencie powrotu do lokacji w przyszłości.</span><span class="sxs-lookup"><span data-stu-id="57fac-206">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="57fac-207">Aplikacja może utrzymywać tylko *stan aplikacji*.</span><span class="sxs-lookup"><span data-stu-id="57fac-207">An app can only persist *app state*.</span></span> <span data-ttu-id="57fac-208">Interfejsów użytkownika nie mogą być utrwalane, takie jak wystąpienia składników i ich drzewa renderowania.</span><span class="sxs-lookup"><span data-stu-id="57fac-208">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="57fac-209">Składniki i drzewa renderowania nie są generalnie serializowane.</span><span class="sxs-lookup"><span data-stu-id="57fac-209">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="57fac-210">Aby zachować stan interfejsu użytkownika, na przykład rozwinięte węzły kontrolki widoku drzewa, aplikacja musi używać niestandardowego kodu do modelowania zachowania stanu interfejsu użytkownika jako możliwy do serializacji stan aplikacji.</span><span class="sxs-lookup"><span data-stu-id="57fac-210">To persist UI state, such as the expanded nodes of a tree view control, the app must use custom code to model the behavior of the UI state as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="57fac-211">Gdzie będzie trwały stan</span><span class="sxs-lookup"><span data-stu-id="57fac-211">Where to persist state</span></span>

<span data-ttu-id="57fac-212">Istnieją wspólne lokalizacje dla stanu utrwalania:</span><span class="sxs-lookup"><span data-stu-id="57fac-212">Common locations exist for persisting state:</span></span>

* [<span data-ttu-id="57fac-213">Magazyn po stronie serwera</span><span class="sxs-lookup"><span data-stu-id="57fac-213">Server-side storage</span></span>](#server-side-storage-server)
* [<span data-ttu-id="57fac-214">Adres URL</span><span class="sxs-lookup"><span data-stu-id="57fac-214">URL</span></span>](#url-server)
* [<span data-ttu-id="57fac-215">Magazyn przeglądarki</span><span class="sxs-lookup"><span data-stu-id="57fac-215">Browser storage</span></span>](#browser-storage-server)
* [<span data-ttu-id="57fac-216">Usługa kontenera stanu w pamięci</span><span class="sxs-lookup"><span data-stu-id="57fac-216">In-memory state container service</span></span>](#in-memory-state-container-service-server)

<h2 id="server-side-storage-server"><span data-ttu-id="57fac-217">Magazyn po stronie serwera</span><span class="sxs-lookup"><span data-stu-id="57fac-217">Server-side storage</span></span></h2>

<span data-ttu-id="57fac-218">W przypadku trwałej trwałości danych obejmującej wielu użytkowników i urządzenia aplikacja może korzystać z magazynu po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="57fac-218">For permanent data persistence that spans multiple users and devices, the app can use server-side storage.</span></span> <span data-ttu-id="57fac-219">Dostępne opcje:</span><span class="sxs-lookup"><span data-stu-id="57fac-219">Options include:</span></span>

* <span data-ttu-id="57fac-220">Blob Storage</span><span class="sxs-lookup"><span data-stu-id="57fac-220">Blob storage</span></span>
* <span data-ttu-id="57fac-221">Magazyn kluczy i wartości</span><span class="sxs-lookup"><span data-stu-id="57fac-221">Key-value storage</span></span>
* <span data-ttu-id="57fac-222">Relacyjna baza danych</span><span class="sxs-lookup"><span data-stu-id="57fac-222">Relational database</span></span>
* <span data-ttu-id="57fac-223">Magazyn tabel</span><span class="sxs-lookup"><span data-stu-id="57fac-223">Table storage</span></span>

<span data-ttu-id="57fac-224">Po zapisaniu danych stan użytkownika jest zachowywany i dostępny w dowolnym nowym obwodie.</span><span class="sxs-lookup"><span data-stu-id="57fac-224">After data is saved, the user's state is retained and available in any new circuit.</span></span>

<span data-ttu-id="57fac-225">Aby uzyskać więcej informacji na temat opcji usługi Azure Data Storage, zobacz następujące tematy:</span><span class="sxs-lookup"><span data-stu-id="57fac-225">For more information on Azure data storage options, see the following:</span></span>

* [<span data-ttu-id="57fac-226">Bazy danych platformy Azure</span><span class="sxs-lookup"><span data-stu-id="57fac-226">Azure Databases</span></span>](https://azure.microsoft.com/product-categories/databases/)
* [<span data-ttu-id="57fac-227">Dokumentacja usługi Azure Storage</span><span class="sxs-lookup"><span data-stu-id="57fac-227">Azure Storage Documentation</span></span>](/azure/storage/)

<h2 id="url-server"><span data-ttu-id="57fac-228">Adres URL</span><span class="sxs-lookup"><span data-stu-id="57fac-228">URL</span></span></h2>

<span data-ttu-id="57fac-229">W przypadku danych przejściowych reprezentujących stan nawigacji należy modelować dane w ramach adresu URL.</span><span class="sxs-lookup"><span data-stu-id="57fac-229">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="57fac-230">Przykłady stanu użytkownika z modelem w adresie URL obejmują:</span><span class="sxs-lookup"><span data-stu-id="57fac-230">Examples of user state modeled in the URL include:</span></span>

* <span data-ttu-id="57fac-231">Identyfikator wyświetlonej jednostki.</span><span class="sxs-lookup"><span data-stu-id="57fac-231">The ID of a viewed entity.</span></span>
* <span data-ttu-id="57fac-232">Numer bieżącej strony w siatce stronicowanej.</span><span class="sxs-lookup"><span data-stu-id="57fac-232">The current page number in a paged grid.</span></span>

<span data-ttu-id="57fac-233">Zawartość paska adresu przeglądarki jest zachowywana:</span><span class="sxs-lookup"><span data-stu-id="57fac-233">The contents of the browser's address bar are retained:</span></span>

* <span data-ttu-id="57fac-234">Jeśli użytkownik ręcznie ponownie załaduje stronę.</span><span class="sxs-lookup"><span data-stu-id="57fac-234">If the user manually reloads the page.</span></span>
* <span data-ttu-id="57fac-235">Jeśli serwer sieci Web stał się niedostępny, a użytkownik jest zmuszony do ponownego załadowania strony, aby można było połączyć się z innym serwerem.</span><span class="sxs-lookup"><span data-stu-id="57fac-235">If the web server becomes unavailable, and the user is forced to reload the page in order to connect to a different server.</span></span>

<span data-ttu-id="57fac-236">Aby uzyskać informacje na temat definiowania wzorców adresów URL za pomocą [`@page`](xref:mvc/views/razor#page) dyrektywy, zobacz <xref:blazor/fundamentals/routing> .</span><span class="sxs-lookup"><span data-stu-id="57fac-236">For information on defining URL patterns with the [`@page`](xref:mvc/views/razor#page) directive, see <xref:blazor/fundamentals/routing>.</span></span>

<h2 id="browser-storage-server"><span data-ttu-id="57fac-237">Magazyn przeglądarki</span><span class="sxs-lookup"><span data-stu-id="57fac-237">Browser storage</span></span></h2>

<span data-ttu-id="57fac-238">W przypadku danych przejściowych, które użytkownik aktywnie tworzy, często używaną lokalizacją magazynu jest przeglądarka [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) i [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) Kolekcje:</span><span class="sxs-lookup"><span data-stu-id="57fac-238">For transient data that the user is actively creating, a commonly used storage location is the browser's [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) collections:</span></span>

* <span data-ttu-id="57fac-239">`localStorage` jest zakresem okna przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="57fac-239">`localStorage` is scoped to the browser's window.</span></span> <span data-ttu-id="57fac-240">Jeśli użytkownik ponownie załaduje stronę lub zamknie i otworzy przeglądarkę, stan będzie się utrzymywał.</span><span class="sxs-lookup"><span data-stu-id="57fac-240">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="57fac-241">Jeśli użytkownik otworzy wiele kart przeglądarki, stan jest udostępniany na kartach.</span><span class="sxs-lookup"><span data-stu-id="57fac-241">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="57fac-242">Dane są zachowywane `localStorage` do momentu jawnego wyczyszczenia.</span><span class="sxs-lookup"><span data-stu-id="57fac-242">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="57fac-243">`sessionStorage` jest zakresem na karcie przeglądarki. Jeśli użytkownik ponownie załaduje kartę, stan będzie się utrzymywał.</span><span class="sxs-lookup"><span data-stu-id="57fac-243">`sessionStorage` is scoped to the browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="57fac-244">Jeśli użytkownik zamknie kartę lub przeglądarkę, stan zostanie utracony.</span><span class="sxs-lookup"><span data-stu-id="57fac-244">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="57fac-245">Jeśli użytkownik otworzy wiele kart przeglądarki, każda karta ma własną niezależną wersję danych.</span><span class="sxs-lookup"><span data-stu-id="57fac-245">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

<span data-ttu-id="57fac-246">Ogólnie rzecz biorąc, `sessionStorage` jest bezpiecznie używać.</span><span class="sxs-lookup"><span data-stu-id="57fac-246">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="57fac-247">`sessionStorage` pozwala uniknąć ryzyka, że użytkownik otwiera wiele kart i napotyka następujące kwestie:</span><span class="sxs-lookup"><span data-stu-id="57fac-247">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="57fac-248">Błędy w magazynie Stanów na kartach.</span><span class="sxs-lookup"><span data-stu-id="57fac-248">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="57fac-249">Zachowanie mylące, gdy karta zastępuje stan innych kart.</span><span class="sxs-lookup"><span data-stu-id="57fac-249">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="57fac-250">`localStorage` jest lepszym rozwiązaniem, jeśli aplikacja musi utrzymywać stan w trakcie zamykania i ponownego otwierania przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="57fac-250">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

<span data-ttu-id="57fac-251">Ostrzeżenia dotyczące korzystania z magazynu przeglądarki:</span><span class="sxs-lookup"><span data-stu-id="57fac-251">Caveats for using browser storage:</span></span>

* <span data-ttu-id="57fac-252">Podobnie jak w przypadku korzystania z bazy danych po stronie serwera, ładowanie i zapisywanie danych są asynchroniczne.</span><span class="sxs-lookup"><span data-stu-id="57fac-252">Similar to the use of a server-side database, loading and saving data are asynchronous.</span></span>
* <span data-ttu-id="57fac-253">W przeciwieństwie do bazy danych po stronie serwera, magazyn nie jest dostępny podczas renderowania wstępnego, ponieważ żądana strona nie istnieje w przeglądarce na etapie renderowania.</span><span class="sxs-lookup"><span data-stu-id="57fac-253">Unlike a server-side database, storage isn't available during prerendering because the requested page doesn't exist in the browser during the prerendering stage.</span></span>
* <span data-ttu-id="57fac-254">Przechowywanie kilku kilobajtów danych jest rozsądne dla Blazor Server aplikacji.</span><span class="sxs-lookup"><span data-stu-id="57fac-254">Storage of a few kilobytes of data is reasonable to persist for Blazor Server apps.</span></span> <span data-ttu-id="57fac-255">Po kilku kilobajtach należy wziąć pod uwagę wpływ na wydajność, ponieważ dane są ładowane i zapisywane w sieci.</span><span class="sxs-lookup"><span data-stu-id="57fac-255">Beyond a few kilobytes, you must consider the performance implications because the data is loaded and saved across the network.</span></span>
* <span data-ttu-id="57fac-256">Użytkownicy mogą wyświetlać i modyfikować dane.</span><span class="sxs-lookup"><span data-stu-id="57fac-256">Users may view or tamper with the data.</span></span> <span data-ttu-id="57fac-257">[Ochrona danych ASP.NET Core](xref:security/data-protection/introduction) może ograniczyć ryzyko.</span><span class="sxs-lookup"><span data-stu-id="57fac-257">[ASP.NET Core Data Protection](xref:security/data-protection/introduction) can mitigate the risk.</span></span> <span data-ttu-id="57fac-258">Na przykład [ASP.NET Core chronionej przeglądarki](#aspnet-core-protected-browser-storage) używa ASP.NET Core ochrony danych.</span><span class="sxs-lookup"><span data-stu-id="57fac-258">For example, [ASP.NET Core Protected Browser Storage](#aspnet-core-protected-browser-storage) uses ASP.NET Core Data Protection.</span></span>

<span data-ttu-id="57fac-259">Pakiety NuGet innych firm zapewniają interfejsy API do pracy z `localStorage` i `sessionStorage` .</span><span class="sxs-lookup"><span data-stu-id="57fac-259">Third-party NuGet packages provide APIs for working with `localStorage` and `sessionStorage`.</span></span> <span data-ttu-id="57fac-260">Warto rozważać wybór pakietu, który w sposób przezroczysty używa [ASP.NET Core ochrony danych](xref:security/data-protection/introduction).</span><span class="sxs-lookup"><span data-stu-id="57fac-260">It's worth considering choosing a package that transparently uses [ASP.NET Core Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="57fac-261">Ochrona danych szyfruje przechowywane dane i zmniejsza potencjalne ryzyko naruszenia przechowywanych danych.</span><span class="sxs-lookup"><span data-stu-id="57fac-261">Data Protection encrypts stored data and reduces the potential risk of tampering with stored data.</span></span> <span data-ttu-id="57fac-262">Jeśli dane serializowane w formacie JSON są przechowywane w postaci zwykłego tekstu, użytkownicy mogą zobaczyć dane przy użyciu narzędzi deweloperskich przeglądarki, a także zmodyfikować przechowywane dane.</span><span class="sxs-lookup"><span data-stu-id="57fac-262">If JSON-serialized data is stored in plain text, users can see the data using browser developer tools and also modify the stored data.</span></span> <span data-ttu-id="57fac-263">Zabezpieczanie danych nie zawsze jest problemem, ponieważ dane mogą być proste.</span><span class="sxs-lookup"><span data-stu-id="57fac-263">Securing data isn't always a problem because the data might be trivial in nature.</span></span> <span data-ttu-id="57fac-264">Na przykład odczytywanie lub modyfikowanie zapisanego koloru elementu interfejsu użytkownika nie jest istotnym zagrożeniem bezpieczeństwa użytkownika lub organizacji.</span><span class="sxs-lookup"><span data-stu-id="57fac-264">For example, reading or modifying the stored color of a UI element isn't a significant security risk to the user or the organization.</span></span> <span data-ttu-id="57fac-265">Unikaj zezwalania użytkownikom na inspekcję i manipulowanie *danymi poufnymi*.</span><span class="sxs-lookup"><span data-stu-id="57fac-265">Avoid allowing users to inspect or tamper with *sensitive data*.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="aspnet-core-protected-browser-storage"></a><span data-ttu-id="57fac-266">ASP.NET Core chronionego magazynu przeglądarki</span><span class="sxs-lookup"><span data-stu-id="57fac-266">ASP.NET Core Protected Browser Storage</span></span>

<span data-ttu-id="57fac-267">ASP.NET Core magazynu chronionej przeglądarki wykorzystuje [ochronę danych ASP.NET Core](xref:security/data-protection/introduction) dla [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) i [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) .</span><span class="sxs-lookup"><span data-stu-id="57fac-267">ASP.NET Core Protected Browser Storage leverages [ASP.NET Core Data Protection](xref:security/data-protection/introduction) for [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage).</span></span>

> [!NOTE]
> <span data-ttu-id="57fac-268">Magazyn chronionej przeglądarki opiera się na ASP.NET Core ochrony danych i jest obsługiwany tylko w przypadku Blazor Server aplikacji.</span><span class="sxs-lookup"><span data-stu-id="57fac-268">Protected Browser Storage relies on ASP.NET Core Data Protection and is only supported for Blazor Server apps.</span></span>

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="57fac-269">Zapisz i Załaduj dane w składniku</span><span class="sxs-lookup"><span data-stu-id="57fac-269">Save and load data within a component</span></span>

<span data-ttu-id="57fac-270">W dowolnym składniku wymagającym ładowania lub zapisywania danych w magazynie przeglądarki Użyj [`@inject`](xref:mvc/views/razor#inject) dyrektywy, aby wstrzyknąć wystąpienie jednego z następujących elementów:</span><span class="sxs-lookup"><span data-stu-id="57fac-270">In any component that requires loading or saving data to browser storage, use the [`@inject`](xref:mvc/views/razor#inject) directive to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="57fac-271">Wybór zależy od lokalizacji magazynu przeglądarki, która ma być używana.</span><span class="sxs-lookup"><span data-stu-id="57fac-271">The choice depends on which browser storage location you wish to use.</span></span> <span data-ttu-id="57fac-272">W poniższym przykładzie `sessionStorage` jest używany:</span><span class="sxs-lookup"><span data-stu-id="57fac-272">In the following example, `sessionStorage` is used:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="57fac-273">`@using`Dyrektywa może zostać umieszczona w `_Imports.razor` pliku aplikacji zamiast w składniku.</span><span class="sxs-lookup"><span data-stu-id="57fac-273">The `@using` directive can be placed in the app's `_Imports.razor` file instead of in the component.</span></span> <span data-ttu-id="57fac-274">Użycie `_Imports.razor` pliku sprawia, że przestrzeń nazw jest dostępna dla większych segmentów aplikacji lub całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="57fac-274">Use of the `_Imports.razor` file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="57fac-275">Aby zachować `currentCount` wartość w `Counter` składniku aplikacji na podstawie Blazor Server szablonu projektu, zmodyfikuj `IncrementCount` metodę, która ma być używana `ProtectedSessionStore.SetAsync` :</span><span class="sxs-lookup"><span data-stu-id="57fac-275">To persist the `currentCount` value in the `Counter` component of an app based on the Blazor Server project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

<span data-ttu-id="57fac-276">W większych, bardziej realistycznych aplikacjach przechowywanie pojedynczych pól jest mało prawdopodobne.</span><span class="sxs-lookup"><span data-stu-id="57fac-276">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="57fac-277">Aplikacje są bardziej podobne do przechowywania całych obiektów modelu, które zawierają złożone Stany.</span><span class="sxs-lookup"><span data-stu-id="57fac-277">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="57fac-278">`ProtectedSessionStore` automatycznie deserializacji i deserializacji dane JSON do przechowywania obiektów stanu złożonego.</span><span class="sxs-lookup"><span data-stu-id="57fac-278">`ProtectedSessionStore` automatically serializes and deserializes JSON data to store complex state objects.</span></span>

<span data-ttu-id="57fac-279">W poprzednim przykładzie kodu `currentCount` dane są przechowywane jako `sessionStorage['count']` w przeglądarce użytkownika.</span><span class="sxs-lookup"><span data-stu-id="57fac-279">In the preceding code example, the `currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="57fac-280">Dane nie są przechowywane w postaci zwykłego tekstu, ale nie są chronione przy użyciu ASP.NET Core ochrony danych.</span><span class="sxs-lookup"><span data-stu-id="57fac-280">The data isn't stored in plain text but rather is protected using ASP.NET Core Data Protection.</span></span> <span data-ttu-id="57fac-281">Zaszyfrowane dane można sprawdzić, jeśli `sessionStorage['count']` są oceniane w konsoli dewelopera w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="57fac-281">The encrypted data can be inspected if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="57fac-282">Aby odzyskać `currentCount` dane, jeśli użytkownik powróci do `Counter` składnika później, w tym, jeśli użytkownik znajduje się w nowym obwodzie, użyj `ProtectedSessionStore.GetAsync` :</span><span class="sxs-lookup"><span data-stu-id="57fac-282">To recover the `currentCount` data if the user returns to the `Counter` component later, including if the user is on a new circuit, use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    var result = await ProtectedSessionStore.GetAsync<int>("count");
    currentCount = result.Success ? result.Value : 0;
}
```

<span data-ttu-id="57fac-283">Jeśli parametry składnika obejmują stan nawigacji, wywołanie `ProtectedSessionStore.GetAsync` i przypisanie `null` niewyniku do <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> , nie <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="57fac-283">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign a non-`null` result in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>, not <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span> <span data-ttu-id="57fac-284"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> jest wywoływana tylko raz podczas pierwszego wystąpienia składnika.</span><span class="sxs-lookup"><span data-stu-id="57fac-284"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is only called once when the component is first instantiated.</span></span> <span data-ttu-id="57fac-285"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> nie zostanie wywołana ponownie później, jeśli użytkownik przejdzie do innego adresu URL, a pozostałe na tej samej stronie.</span><span class="sxs-lookup"><span data-stu-id="57fac-285"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span> <span data-ttu-id="57fac-286">Aby uzyskać więcej informacji, zobacz <xref:blazor/components/lifecycle>.</span><span class="sxs-lookup"><span data-stu-id="57fac-286">For more information, see <xref:blazor/components/lifecycle>.</span></span>

> [!WARNING]
> <span data-ttu-id="57fac-287">Przykłady w tej sekcji działają tylko wtedy, gdy serwer nie ma włączonej obsługi przed renderowaniem.</span><span class="sxs-lookup"><span data-stu-id="57fac-287">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="57fac-288">Po włączeniu obsługi przed renderowaniem zostanie wygenerowany błąd wyjaśniający, że nie można wystawić wywołań międzyoperacyjnych języka JavaScript, ponieważ składnik jest wstępnie renderowany.</span><span class="sxs-lookup"><span data-stu-id="57fac-288">With prerendering enabled, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>
>
> <span data-ttu-id="57fac-289">Wyłącz renderowanie lub Dodaj dodatkowy kod, aby współpracował z renderowaniem.</span><span class="sxs-lookup"><span data-stu-id="57fac-289">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="57fac-290">Aby dowiedzieć się więcej na temat pisania kodu, który działa z renderowaniem, zobacz sekcję [Obsługa przed renderowaniem](#handle-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="57fac-290">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="57fac-291">Obsłuż stan ładowania</span><span class="sxs-lookup"><span data-stu-id="57fac-291">Handle the loading state</span></span>

<span data-ttu-id="57fac-292">Ponieważ dostęp do magazynu przeglądarki odbywa się asynchronicznie za pośrednictwem połączenia sieciowego, zawsze istnieje okres, po upływie którego dane są ładowane i dostępne dla składnika.</span><span class="sxs-lookup"><span data-stu-id="57fac-292">Since browser storage is accessed asynchronously over a network connection, there's always a period of time before the data is loaded and available to a component.</span></span> <span data-ttu-id="57fac-293">Aby uzyskać najlepsze wyniki, renderowanie komunikatu o stanie ładowania podczas ładowania jest w toku zamiast wyświetlania danych pustych lub domyślnych.</span><span class="sxs-lookup"><span data-stu-id="57fac-293">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="57fac-294">Jednym z rozwiązań jest śledzenie, czy dane są `null` , co oznacza, że dane są nadal ładowane.</span><span class="sxs-lookup"><span data-stu-id="57fac-294">One approach is to track whether the data is `null`, which means that the data is still loading.</span></span> <span data-ttu-id="57fac-295">W `Counter` składniku domyślnym liczba jest przechowywana w `int` .</span><span class="sxs-lookup"><span data-stu-id="57fac-295">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="57fac-296">[Wprowadź `currentCount` wartość null](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) , dodając znak zapytania ( `?` ) do typu ( `int` ):</span><span class="sxs-lookup"><span data-stu-id="57fac-296">[Make `currentCount` nullable](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? currentCount;
```

<span data-ttu-id="57fac-297">Zamiast bezwarunkowo wyświetlać liczbę i **`Increment`** przycisk, Wyświetl te elementy tylko wtedy, gdy dane są ładowane przez sprawdzenie <xref:System.Nullable%601.HasValue%2A> :</span><span class="sxs-lookup"><span data-stu-id="57fac-297">Instead of unconditionally displaying the count and **`Increment`** button, display these elements only if the data is loaded by checking <xref:System.Nullable%601.HasValue%2A>:</span></span>

```razor
@if (currentCount.HasValue)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a><span data-ttu-id="57fac-298">Obsługa przed renderowaniem</span><span class="sxs-lookup"><span data-stu-id="57fac-298">Handle prerendering</span></span>

<span data-ttu-id="57fac-299">Podczas renderowania:</span><span class="sxs-lookup"><span data-stu-id="57fac-299">During prerendering:</span></span>

* <span data-ttu-id="57fac-300">Połączenie interaktywne z przeglądarką użytkownika nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="57fac-300">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="57fac-301">Przeglądarka nie zawiera jeszcze strony, w której można uruchomić kod JavaScript.</span><span class="sxs-lookup"><span data-stu-id="57fac-301">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="57fac-302">`localStorage` lub `sessionStorage` nie są dostępne podczas renderowania.</span><span class="sxs-lookup"><span data-stu-id="57fac-302">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="57fac-303">Jeśli składnik próbuje współdziałać z magazynem, zostanie wygenerowany błąd z wyjaśnieniem, że nie można wystawić wywołań międzyoperacyjnych języka JavaScript, ponieważ składnik jest wstępnie renderowany.</span><span class="sxs-lookup"><span data-stu-id="57fac-303">If the component attempts to interact with storage, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>

<span data-ttu-id="57fac-304">Jednym ze sposobów na rozwiązanie błędu jest wyłączenie renderowania.</span><span class="sxs-lookup"><span data-stu-id="57fac-304">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="57fac-305">Jest to zazwyczaj najlepszym wyborem, jeśli aplikacja znacznie korzysta z magazynu opartego na przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="57fac-305">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="57fac-306">Renderowanie zwiększa złożoność i nie korzysta z aplikacji, ponieważ aplikacja nie może przeprowadzić renderowania żadnej przydatnej zawartości do momentu, gdy nie `localStorage` `sessionStorage` jest dostępna.</span><span class="sxs-lookup"><span data-stu-id="57fac-306">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="57fac-307">Aby wyłączyć renderowanie, Otwórz `Pages/_Host.cshtml` plik i Zmień `render-mode` atrybut [pomocnika znacznika składnika](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) , aby <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> :</span><span class="sxs-lookup"><span data-stu-id="57fac-307">To disable prerendering, open the `Pages/_Host.cshtml` file and change the `render-mode` attribute of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>:</span></span>

```cshtml
<component type="typeof(App)" render-mode="Server" />
```

<span data-ttu-id="57fac-308">Renderowanie może być przydatne w przypadku innych stron, które nie używają `localStorage` ani `sessionStorage` .</span><span class="sxs-lookup"><span data-stu-id="57fac-308">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="57fac-309">Aby zachować renderowanie, odłóż operację ładowania do momentu podłączenia przeglądarki do obwodu.</span><span class="sxs-lookup"><span data-stu-id="57fac-309">To retain prerendering, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="57fac-310">Poniżej przedstawiono przykład przechowywania wartości licznika:</span><span class="sxs-lookup"><span data-stu-id="57fac-310">The following is an example for storing a counter value:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

@if (isConnected)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}

@code {
    private int currentCount;
    private bool isConnected;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        var result = await ProtectedLocalStore.GetAsync<int>("count");
        currentCount = result.Success ? result.Value : 0;
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedLocalStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="57fac-311">Wyrównać zachowywanie stanu do wspólnej lokalizacji</span><span class="sxs-lookup"><span data-stu-id="57fac-311">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="57fac-312">Jeśli wiele składników korzysta z magazynu opartego na przeglądarce, ponowne implementowanie kodu dostawcy stanu wiele razy powoduje utworzenie duplikacji kodu.</span><span class="sxs-lookup"><span data-stu-id="57fac-312">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="57fac-313">Jedną z opcji unikania duplikowania kodu jest utworzenie *składnika nadrzędnego dostawcy stanu* , który hermetyzuje logikę dostawcy stanu.</span><span class="sxs-lookup"><span data-stu-id="57fac-313">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="57fac-314">Składniki podrzędne mogą współpracować z trwałymi danymi bez względu na mechanizm trwałości stanu.</span><span class="sxs-lookup"><span data-stu-id="57fac-314">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="57fac-315">W poniższym przykładzie `CounterStateProvider` składnika dane licznika są utrwalane `sessionStorage` :</span><span class="sxs-lookup"><span data-stu-id="57fac-315">In the following example of a `CounterStateProvider` component, counter data is persisted to `sessionStorage`:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (isLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool isLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        var result = await ProtectedSessionStore.GetAsync<int>("count");
        currentCount = result.Success ? result.Value : 0;
        isLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

<span data-ttu-id="57fac-316">`CounterStateProvider`Składnik obsługuje fazę ładowania, przez co nie renderuje jej zawartości podrzędnej do momentu ukończenia ładowania.</span><span class="sxs-lookup"><span data-stu-id="57fac-316">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="57fac-317">Aby użyć `CounterStateProvider` składnika, zawiń wystąpienie składnika wokół dowolnego innego składnika, który wymaga dostępu do stanu licznika.</span><span class="sxs-lookup"><span data-stu-id="57fac-317">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="57fac-318">Aby zapewnić dostępność stanu dla wszystkich składników w aplikacji, zawiń `CounterStateProvider` składnik wokół składnika <xref:Microsoft.AspNetCore.Components.Routing.Router> w `App` składniku ( `App.razor` ):</span><span class="sxs-lookup"><span data-stu-id="57fac-318">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the <xref:Microsoft.AspNetCore.Components.Routing.Router> in the `App` component (`App.razor`):</span></span>

```razor
<CounterStateProvider>
    <Router AppAssembly="@typeof(Program).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

<span data-ttu-id="57fac-319">Zapakowane składniki są odbierane i mogą modyfikować stan trwałych liczników.</span><span class="sxs-lookup"><span data-stu-id="57fac-319">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="57fac-320">Poniższy `Counter` składnik implementuje wzorzec:</span><span class="sxs-lookup"><span data-stu-id="57fac-320">The following `Counter` component implements the pattern:</span></span>

```razor
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>
<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

<span data-ttu-id="57fac-321">Poprzedni składnik nie jest wymagany do współpracy z programem `ProtectedBrowserStorage` ani nie zajmuje się fazą "Ładowanie".</span><span class="sxs-lookup"><span data-stu-id="57fac-321">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="57fac-322">Aby można było zaradzić sobie z instrukcją prerenderingu zgodnie z wcześniejszym opisem, `CounterStateProvider` może zostać zmieniona, aby wszystkie składniki korzystające z danych licznika automatycznie działały z użyciem prerenderowania.</span><span class="sxs-lookup"><span data-stu-id="57fac-322">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="57fac-323">Aby uzyskać więcej informacji, zobacz sekcję [Obsługa przed renderowaniem](#handle-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="57fac-323">For more information, see the [Handle prerendering](#handle-prerendering) section.</span></span>

<span data-ttu-id="57fac-324">Ogólnie rzecz biorąc, zalecany jest wzorzec *składnika nadrzędnego dostawcy stanu* :</span><span class="sxs-lookup"><span data-stu-id="57fac-324">In general, the *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="57fac-325">Aby korzystać z stanu w wielu składnikach.</span><span class="sxs-lookup"><span data-stu-id="57fac-325">To consume state across many components.</span></span>
* <span data-ttu-id="57fac-326">Jeśli istnieje tylko jeden obiekt stanu najwyższego poziomu, który ma być trwały.</span><span class="sxs-lookup"><span data-stu-id="57fac-326">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="57fac-327">Aby zachować wiele różnych obiektów stanu i korzystać z różnych podzbiorów obiektów w różnych miejscach, lepiej jest unikać utrwalania stanu w całości.</span><span class="sxs-lookup"><span data-stu-id="57fac-327">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid persisting state globally.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="protected-browser-storage-experimental-nuget-package"></a><span data-ttu-id="57fac-328">Eksperymentalny pakiet NuGet magazynu chronionej przeglądarki</span><span class="sxs-lookup"><span data-stu-id="57fac-328">Protected Browser Storage experimental NuGet package</span></span>

<span data-ttu-id="57fac-329">ASP.NET Core magazynu chronionej przeglądarki wykorzystuje [ochronę danych ASP.NET Core](xref:security/data-protection/introduction) dla [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) i [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) .</span><span class="sxs-lookup"><span data-stu-id="57fac-329">ASP.NET Core Protected Browser Storage leverages [ASP.NET Core Data Protection](xref:security/data-protection/introduction) for [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage).</span></span>

> [!WARNING]
> <span data-ttu-id="57fac-330">`Microsoft.AspNetCore.ProtectedBrowserStorage` jest nieobsługiwanym, eksperymentalnym pakietem nieodpowiedninym do użycia w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="57fac-330">`Microsoft.AspNetCore.ProtectedBrowserStorage` is an unsupported, experimental package unsuitable for production use.</span></span>
>
> <span data-ttu-id="57fac-331">Pakiet jest dostępny tylko do użytku w aplikacjach ASP.NET Core 3,1 Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="57fac-331">The package is only available for use in ASP.NET Core 3.1 Blazor Server apps.</span></span>

### <a name="configuration"></a><span data-ttu-id="57fac-332">Konfigurowanie</span><span class="sxs-lookup"><span data-stu-id="57fac-332">Configuration</span></span>

1. <span data-ttu-id="57fac-333">Dodaj odwołanie do pakietu do [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage) .</span><span class="sxs-lookup"><span data-stu-id="57fac-333">Add a package reference to [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>
1. <span data-ttu-id="57fac-334">W `Pages/_Host.cshtml` pliku Dodaj następujący skrypt wewnątrz tagu zamykającego `</body>` :</span><span class="sxs-lookup"><span data-stu-id="57fac-334">In the `Pages/_Host.cshtml` file, add the following script inside the closing `</body>` tag:</span></span>

   ```cshtml
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. <span data-ttu-id="57fac-335">W programie `Startup.ConfigureServices` Wywołaj `AddProtectedBrowserStorage` `localStorage` kolekcję usługi i Dodaj do `sessionStorage` niej usługi:</span><span class="sxs-lookup"><span data-stu-id="57fac-335">In `Startup.ConfigureServices`, call `AddProtectedBrowserStorage` to add `localStorage` and `sessionStorage` services to the service collection:</span></span>

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="57fac-336">Zapisz i Załaduj dane w składniku</span><span class="sxs-lookup"><span data-stu-id="57fac-336">Save and load data within a component</span></span>

<span data-ttu-id="57fac-337">W dowolnym składniku wymagającym ładowania lub zapisywania danych w magazynie przeglądarki Użyj [`@inject`](xref:mvc/views/razor#inject) dyrektywy, aby wstrzyknąć wystąpienie jednego z następujących elementów:</span><span class="sxs-lookup"><span data-stu-id="57fac-337">In any component that requires loading or saving data to browser storage, use the [`@inject`](xref:mvc/views/razor#inject) directive to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="57fac-338">Wybór zależy od lokalizacji magazynu przeglądarki, która ma być używana.</span><span class="sxs-lookup"><span data-stu-id="57fac-338">The choice depends on which browser storage location you wish to use.</span></span> <span data-ttu-id="57fac-339">W poniższym przykładzie `sessionStorage` jest używany:</span><span class="sxs-lookup"><span data-stu-id="57fac-339">In the following example, `sessionStorage` is used:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="57fac-340">`@using`Instrukcja może zostać umieszczona w `_Imports.razor` pliku, a nie w składniku.</span><span class="sxs-lookup"><span data-stu-id="57fac-340">The `@using` statement can be placed into an `_Imports.razor` file instead of in the component.</span></span> <span data-ttu-id="57fac-341">Użycie `_Imports.razor` pliku sprawia, że przestrzeń nazw jest dostępna dla większych segmentów aplikacji lub całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="57fac-341">Use of the `_Imports.razor` file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="57fac-342">Aby zachować `currentCount` wartość w `Counter` składniku aplikacji na podstawie Blazor Server szablonu projektu, zmodyfikuj `IncrementCount` metodę, która ma być używana `ProtectedSessionStore.SetAsync` :</span><span class="sxs-lookup"><span data-stu-id="57fac-342">To persist the `currentCount` value in the `Counter` component of an app based on the Blazor Server project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

<span data-ttu-id="57fac-343">W większych, bardziej realistycznych aplikacjach przechowywanie pojedynczych pól jest mało prawdopodobne.</span><span class="sxs-lookup"><span data-stu-id="57fac-343">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="57fac-344">Aplikacje są bardziej podobne do przechowywania całych obiektów modelu, które zawierają złożone Stany.</span><span class="sxs-lookup"><span data-stu-id="57fac-344">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="57fac-345">`ProtectedSessionStore` automatycznie serializować i deserializacji danych JSON.</span><span class="sxs-lookup"><span data-stu-id="57fac-345">`ProtectedSessionStore` automatically serializes and deserializes JSON data.</span></span>

<span data-ttu-id="57fac-346">W poprzednim przykładzie kodu `currentCount` dane są przechowywane jako `sessionStorage['count']` w przeglądarce użytkownika.</span><span class="sxs-lookup"><span data-stu-id="57fac-346">In the preceding code example, the `currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="57fac-347">Dane nie są przechowywane w postaci zwykłego tekstu, ale nie są chronione przy użyciu ASP.NET Core ochrony danych.</span><span class="sxs-lookup"><span data-stu-id="57fac-347">The data isn't stored in plain text but rather is protected using ASP.NET Core Data Protection.</span></span> <span data-ttu-id="57fac-348">Zaszyfrowane dane można sprawdzić, jeśli `sessionStorage['count']` są oceniane w konsoli dewelopera w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="57fac-348">The encrypted data can be inspected if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="57fac-349">Aby odzyskać `currentCount` dane, jeśli użytkownik powróci do `Counter` składnika później, w tym, jeśli znajdują się w całkowicie nowym obwodzie, użyj `ProtectedSessionStore.GetAsync` :</span><span class="sxs-lookup"><span data-stu-id="57fac-349">To recover the `currentCount` data if the user returns to the `Counter` component later, including if they're on an entirely new circuit, use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

<span data-ttu-id="57fac-350">Jeśli parametry składnika obejmują stan nawigacji, wywołaj `ProtectedSessionStore.GetAsync` i przypisz wynik w <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> , nie <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="57fac-350">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign the result in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>, not <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span> <span data-ttu-id="57fac-351"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> jest wywoływana tylko raz podczas pierwszego wystąpienia składnika.</span><span class="sxs-lookup"><span data-stu-id="57fac-351"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is only called once when the component is first instantiated.</span></span> <span data-ttu-id="57fac-352"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> nie zostanie wywołana ponownie później, jeśli użytkownik przejdzie do innego adresu URL, a pozostałe na tej samej stronie.</span><span class="sxs-lookup"><span data-stu-id="57fac-352"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span> <span data-ttu-id="57fac-353">Aby uzyskać więcej informacji, zobacz <xref:blazor/components/lifecycle>.</span><span class="sxs-lookup"><span data-stu-id="57fac-353">For more information, see <xref:blazor/components/lifecycle>.</span></span>

> [!WARNING]
> <span data-ttu-id="57fac-354">Przykłady w tej sekcji działają tylko wtedy, gdy serwer nie ma włączonej obsługi przed renderowaniem.</span><span class="sxs-lookup"><span data-stu-id="57fac-354">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="57fac-355">Po włączeniu obsługi przed renderowaniem zostanie wygenerowany błąd wyjaśniający, że nie można wystawić wywołań międzyoperacyjnych języka JavaScript, ponieważ składnik jest wstępnie renderowany.</span><span class="sxs-lookup"><span data-stu-id="57fac-355">With prerendering enabled, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>
>
> <span data-ttu-id="57fac-356">Wyłącz renderowanie lub Dodaj dodatkowy kod, aby współpracował z renderowaniem.</span><span class="sxs-lookup"><span data-stu-id="57fac-356">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="57fac-357">Aby dowiedzieć się więcej na temat pisania kodu, który działa z renderowaniem, zobacz sekcję [Obsługa przed renderowaniem](#handle-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="57fac-357">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="57fac-358">Obsłuż stan ładowania</span><span class="sxs-lookup"><span data-stu-id="57fac-358">Handle the loading state</span></span>

<span data-ttu-id="57fac-359">Ponieważ dostęp do magazynu przeglądarki odbywa się asynchronicznie za pośrednictwem połączenia sieciowego, zawsze istnieje okres, po upływie którego dane są ładowane i dostępne dla składnika.</span><span class="sxs-lookup"><span data-stu-id="57fac-359">Since browser storage is accessed asynchronously over a network connection, there's always a period of time before the data is loaded and available to a component.</span></span> <span data-ttu-id="57fac-360">Aby uzyskać najlepsze wyniki, renderowanie komunikatu o stanie ładowania podczas ładowania jest w toku zamiast wyświetlania danych pustych lub domyślnych.</span><span class="sxs-lookup"><span data-stu-id="57fac-360">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="57fac-361">Jednym z rozwiązań jest śledzenie, czy dane są `null` , co oznacza, że dane są nadal ładowane.</span><span class="sxs-lookup"><span data-stu-id="57fac-361">One approach is to track whether the data is `null`, which means that the data is still loading.</span></span> <span data-ttu-id="57fac-362">W `Counter` składniku domyślnym liczba jest przechowywana w `int` .</span><span class="sxs-lookup"><span data-stu-id="57fac-362">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="57fac-363">[Wprowadź `currentCount` wartość null](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) , dodając znak zapytania ( `?` ) do typu ( `int` ):</span><span class="sxs-lookup"><span data-stu-id="57fac-363">[Make `currentCount` nullable](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? currentCount;
```

<span data-ttu-id="57fac-364">Zamiast bezwarunkowo wyświetlić liczbę i **`Increment`** przycisk, wybierz, aby wyświetlić te elementy tylko wtedy, gdy dane są ładowane:</span><span class="sxs-lookup"><span data-stu-id="57fac-364">Instead of unconditionally displaying the count and **`Increment`** button, choose to display these elements only if the data is loaded:</span></span>

```razor
@if (currentCount.HasValue)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a><span data-ttu-id="57fac-365">Obsługa przed renderowaniem</span><span class="sxs-lookup"><span data-stu-id="57fac-365">Handle prerendering</span></span>

<span data-ttu-id="57fac-366">Podczas renderowania:</span><span class="sxs-lookup"><span data-stu-id="57fac-366">During prerendering:</span></span>

* <span data-ttu-id="57fac-367">Połączenie interaktywne z przeglądarką użytkownika nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="57fac-367">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="57fac-368">Przeglądarka nie zawiera jeszcze strony, w której można uruchomić kod JavaScript.</span><span class="sxs-lookup"><span data-stu-id="57fac-368">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="57fac-369">`localStorage` lub `sessionStorage` nie są dostępne podczas renderowania.</span><span class="sxs-lookup"><span data-stu-id="57fac-369">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="57fac-370">Jeśli składnik próbuje współdziałać z magazynem, zostanie wygenerowany błąd z wyjaśnieniem, że nie można wystawić wywołań międzyoperacyjnych języka JavaScript, ponieważ składnik jest wstępnie renderowany.</span><span class="sxs-lookup"><span data-stu-id="57fac-370">If the component attempts to interact with storage, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>

<span data-ttu-id="57fac-371">Jednym ze sposobów na rozwiązanie błędu jest wyłączenie renderowania.</span><span class="sxs-lookup"><span data-stu-id="57fac-371">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="57fac-372">Jest to zazwyczaj najlepszym wyborem, jeśli aplikacja znacznie korzysta z magazynu opartego na przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="57fac-372">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="57fac-373">Renderowanie zwiększa złożoność i nie korzysta z aplikacji, ponieważ aplikacja nie może przeprowadzić renderowania żadnej przydatnej zawartości do momentu, gdy nie `localStorage` `sessionStorage` jest dostępna.</span><span class="sxs-lookup"><span data-stu-id="57fac-373">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="57fac-374">Aby wyłączyć renderowanie, Otwórz `Pages/_Host.cshtml` plik i Zmień `render-mode` atrybut [pomocnika znacznika składnika](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) , aby <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> :</span><span class="sxs-lookup"><span data-stu-id="57fac-374">To disable prerendering, open the `Pages/_Host.cshtml` file and change the `render-mode` attribute of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>:</span></span>

```cshtml
<component type="typeof(App)" render-mode="Server" />
```

<span data-ttu-id="57fac-375">Renderowanie może być przydatne w przypadku innych stron, które nie używają `localStorage` ani `sessionStorage` .</span><span class="sxs-lookup"><span data-stu-id="57fac-375">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="57fac-376">Aby zachować renderowanie, odłóż operację ładowania do momentu podłączenia przeglądarki do obwodu.</span><span class="sxs-lookup"><span data-stu-id="57fac-376">To retain prerendering, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="57fac-377">Poniżej przedstawiono przykład przechowywania wartości licznika:</span><span class="sxs-lookup"><span data-stu-id="57fac-377">The following is an example for storing a counter value:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

@if (isConnected)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}

@code {
    private int? currentCount;
    private bool isConnected = false;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        currentCount = await ProtectedLocalStore.GetAsync<int>("count");
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedLocalStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="57fac-378">Wyrównać zachowywanie stanu do wspólnej lokalizacji</span><span class="sxs-lookup"><span data-stu-id="57fac-378">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="57fac-379">Jeśli wiele składników korzysta z magazynu opartego na przeglądarce, ponowne implementowanie kodu dostawcy stanu wiele razy powoduje utworzenie duplikacji kodu.</span><span class="sxs-lookup"><span data-stu-id="57fac-379">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="57fac-380">Jedną z opcji unikania duplikowania kodu jest utworzenie *składnika nadrzędnego dostawcy stanu* , który hermetyzuje logikę dostawcy stanu.</span><span class="sxs-lookup"><span data-stu-id="57fac-380">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="57fac-381">Składniki podrzędne mogą współpracować z trwałymi danymi bez względu na mechanizm trwałości stanu.</span><span class="sxs-lookup"><span data-stu-id="57fac-381">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="57fac-382">W poniższym przykładzie `CounterStateProvider` składnika dane licznika są utrwalane `sessionStorage` :</span><span class="sxs-lookup"><span data-stu-id="57fac-382">In the following example of a `CounterStateProvider` component, counter data is persisted to `sessionStorage`:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (isLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool isLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        isLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

<span data-ttu-id="57fac-383">`CounterStateProvider`Składnik obsługuje fazę ładowania, przez co nie renderuje jej zawartości podrzędnej do momentu ukończenia ładowania.</span><span class="sxs-lookup"><span data-stu-id="57fac-383">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="57fac-384">Aby użyć `CounterStateProvider` składnika, zawiń wystąpienie składnika wokół dowolnego innego składnika, który wymaga dostępu do stanu licznika.</span><span class="sxs-lookup"><span data-stu-id="57fac-384">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="57fac-385">Aby zapewnić dostępność stanu dla wszystkich składników w aplikacji, zawiń `CounterStateProvider` składnik wokół składnika <xref:Microsoft.AspNetCore.Components.Routing.Router> w `App` składniku ( `App.razor` ):</span><span class="sxs-lookup"><span data-stu-id="57fac-385">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the <xref:Microsoft.AspNetCore.Components.Routing.Router> in the `App` component (`App.razor`):</span></span>

```razor
<CounterStateProvider>
    <Router AppAssembly="@typeof(Program).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

<span data-ttu-id="57fac-386">Zapakowane składniki są odbierane i mogą modyfikować stan trwałych liczników.</span><span class="sxs-lookup"><span data-stu-id="57fac-386">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="57fac-387">Poniższy `Counter` składnik implementuje wzorzec:</span><span class="sxs-lookup"><span data-stu-id="57fac-387">The following `Counter` component implements the pattern:</span></span>

```razor
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>
<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

<span data-ttu-id="57fac-388">Poprzedni składnik nie jest wymagany do współpracy z programem `ProtectedBrowserStorage` ani nie zajmuje się fazą "Ładowanie".</span><span class="sxs-lookup"><span data-stu-id="57fac-388">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="57fac-389">Aby można było zaradzić sobie z instrukcją prerenderingu zgodnie z wcześniejszym opisem, `CounterStateProvider` może zostać zmieniona, aby wszystkie składniki korzystające z danych licznika automatycznie działały z użyciem prerenderowania.</span><span class="sxs-lookup"><span data-stu-id="57fac-389">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="57fac-390">Aby uzyskać więcej informacji, zobacz sekcję [Obsługa przed renderowaniem](#handle-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="57fac-390">For more information, see the [Handle prerendering](#handle-prerendering) section.</span></span>

<span data-ttu-id="57fac-391">Ogólnie rzecz biorąc, zalecany jest wzorzec *składnika nadrzędnego dostawcy stanu* :</span><span class="sxs-lookup"><span data-stu-id="57fac-391">In general, *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="57fac-392">Aby korzystać z stanu w wielu składnikach.</span><span class="sxs-lookup"><span data-stu-id="57fac-392">To consume state across many components.</span></span>
* <span data-ttu-id="57fac-393">Jeśli istnieje tylko jeden obiekt stanu najwyższego poziomu, który ma być trwały.</span><span class="sxs-lookup"><span data-stu-id="57fac-393">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="57fac-394">Aby zachować wiele różnych obiektów stanu i korzystać z różnych podzbiorów obiektów w różnych miejscach, lepiej jest unikać utrwalania stanu w całości.</span><span class="sxs-lookup"><span data-stu-id="57fac-394">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid persisting state globally.</span></span>

::: moniker-end

<h2 id="in-memory-state-container-service-server"><span data-ttu-id="57fac-395">Usługa kontenera stanu w pamięci</span><span class="sxs-lookup"><span data-stu-id="57fac-395">In-memory state container service</span></span></h2>

[!INCLUDE[](~/blazor/includes/state-container.md)]

::: zone-end
