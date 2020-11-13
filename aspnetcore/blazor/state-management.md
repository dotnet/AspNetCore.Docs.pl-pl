---
title: :::no-loc(Blazor):::Zarządzanie stanem ASP.NET Core
author: guardrex
description: 'Dowiedz się, jak utrwalać stan w :::no-loc(Blazor Server)::: aplikacjach.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/state-management
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 7e79836e3dd1da175a62a84e11dfd30fee7b2f1b
ms.sourcegitcommit: 1ea3f23bec63e96ffc3a927992f30a5fc0de3ff9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94570149"
---
# <a name="aspnet-core-no-locblazor-state-management"></a><span data-ttu-id="2c459-103">:::no-loc(Blazor):::Zarządzanie stanem ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2c459-103">ASP.NET Core :::no-loc(Blazor)::: state management</span></span>

<span data-ttu-id="2c459-104">[Steve Sanderson](https://github.com/SteveSandersonMS) i [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2c459-104">By [Steve Sanderson](https://github.com/SteveSandersonMS) and [Luke Latham](https://github.com/guardrex)</span></span>

::: zone pivot="webassembly"

<span data-ttu-id="2c459-105">Stan użytkownika utworzony w :::no-loc(Blazor WebAssembly)::: aplikacji jest przechowywany w pamięci przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="2c459-105">User state created in a :::no-loc(Blazor WebAssembly)::: app is held in the browser's memory.</span></span>

<span data-ttu-id="2c459-106">Przykłady stanu użytkownika znajdującego się w pamięci przeglądarki obejmują:</span><span class="sxs-lookup"><span data-stu-id="2c459-106">Examples of user state held in browser memory include:</span></span>

* <span data-ttu-id="2c459-107">Hierarchia wystąpień składników i ich najnowsze dane wyjściowe renderowania w renderowanym interfejsie użytkownika.</span><span class="sxs-lookup"><span data-stu-id="2c459-107">The hierarchy of component instances and their most recent render output in the rendered UI.</span></span>
* <span data-ttu-id="2c459-108">Wartości pól i właściwości w wystąpieniach składników.</span><span class="sxs-lookup"><span data-stu-id="2c459-108">The values of fields and properties in component instances.</span></span>
* <span data-ttu-id="2c459-109">Dane przechowywane w wystąpieniach usługi [wtrysku zależności (di)](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="2c459-109">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances.</span></span>
* <span data-ttu-id="2c459-110">Wartości ustawione za poorednictwem wywołań [międzyoperacyjnych języka JavaScript](xref:blazor/call-javascript-from-dotnet) .</span><span class="sxs-lookup"><span data-stu-id="2c459-110">Values set through [JavaScript interop](xref:blazor/call-javascript-from-dotnet) calls.</span></span>

<span data-ttu-id="2c459-111">Gdy użytkownik zamknie i ponownie otworzy swoją przeglądarkę lub załaduje stronę, stan użytkownika znajdujący się w pamięci przeglądarki zostanie utracony.</span><span class="sxs-lookup"><span data-stu-id="2c459-111">When a user closes and re-opens their browser or reloads the page, user state held in the browser's memory is lost.</span></span>

## <a name="persist-state-across-browser-sessions"></a><span data-ttu-id="2c459-112">Stan utrwalania w sesjach przeglądarki</span><span class="sxs-lookup"><span data-stu-id="2c459-112">Persist state across browser sessions</span></span>

<span data-ttu-id="2c459-113">Ogólnie rzecz biorąc, utrzymuje stan w sesjach przeglądarki, w których użytkownicy aktywnie tworzą dane, a nie tylko odczytujące dane, które już istnieją.</span><span class="sxs-lookup"><span data-stu-id="2c459-113">Generally, maintain state across browser sessions where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="2c459-114">Aby zachować stan między sesjami przeglądarki, aplikacja musi przechowywać dane w innej lokalizacji magazynu niż pamięć przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="2c459-114">To preserve state across browser sessions, the app must persist the data to some other storage location than the browser's memory.</span></span> <span data-ttu-id="2c459-115">Trwałość stanu nie jest automatyczna.</span><span class="sxs-lookup"><span data-stu-id="2c459-115">State persistence isn't automatic.</span></span> <span data-ttu-id="2c459-116">Podczas tworzenia aplikacji należy wykonać kroki w celu zaimplementowania stanu trwałości danych stanowych.</span><span class="sxs-lookup"><span data-stu-id="2c459-116">You must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="2c459-117">Trwałość danych jest zazwyczaj wymagana tylko w przypadku stanu wysokiej wartości, który użytkownicy wystawią nakłady na tworzenie.</span><span class="sxs-lookup"><span data-stu-id="2c459-117">Data persistence is typically only required for high-value state that users expended effort to create.</span></span> <span data-ttu-id="2c459-118">W poniższych przykładach stan utrwalania polega na zapisywaniu czasu lub pomocy w działaniach komercyjnych:</span><span class="sxs-lookup"><span data-stu-id="2c459-118">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="2c459-119">Wieloetapowe formularze sieci Web: czasochłonne, aby użytkownik mógł ponownie wprowadzić dane dla kilku ukończonych kroków wieloetapowego formularza sieci Web, jeśli ich stan zostanie utracony.</span><span class="sxs-lookup"><span data-stu-id="2c459-119">Multi-step web forms: It's time-consuming for a user to re-enter data for several completed steps of a multi-step web form if their state is lost.</span></span> <span data-ttu-id="2c459-120">Użytkownik utraci stan w tym scenariuszu, jeśli opuszcza formularz i wróci później.</span><span class="sxs-lookup"><span data-stu-id="2c459-120">A user loses state in this scenario if they navigate away from the form and return later.</span></span>
* <span data-ttu-id="2c459-121">Koszyki: każdy handlowy istotny składnik aplikacji, który reprezentuje potencjalne dochody, może być utrzymywany.</span><span class="sxs-lookup"><span data-stu-id="2c459-121">Shopping carts: Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="2c459-122">Użytkownik, który straci swój stan, a tym samym koszyk, może zakupić mniejszą liczbę produktów lub usług w momencie powrotu do lokacji w przyszłości.</span><span class="sxs-lookup"><span data-stu-id="2c459-122">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="2c459-123">Aplikacja może utrzymywać tylko *stan aplikacji*.</span><span class="sxs-lookup"><span data-stu-id="2c459-123">An app can only persist *app state*.</span></span> <span data-ttu-id="2c459-124">Interfejsów użytkownika nie mogą być utrwalane, takie jak wystąpienia składników i ich drzewa renderowania.</span><span class="sxs-lookup"><span data-stu-id="2c459-124">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="2c459-125">Składniki i drzewa renderowania nie są generalnie serializowane.</span><span class="sxs-lookup"><span data-stu-id="2c459-125">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="2c459-126">Aby zachować stan interfejsu użytkownika, na przykład rozwinięte węzły kontrolki widoku drzewa, aplikacja musi używać niestandardowego kodu do modelowania zachowania stanu interfejsu użytkownika jako możliwy do serializacji stan aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2c459-126">To persist UI state, such as the expanded nodes of a tree view control, the app must use custom code to model the behavior of the UI state as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="2c459-127">Gdzie będzie trwały stan</span><span class="sxs-lookup"><span data-stu-id="2c459-127">Where to persist state</span></span>

<span data-ttu-id="2c459-128">Istnieją wspólne lokalizacje dla stanu utrwalania:</span><span class="sxs-lookup"><span data-stu-id="2c459-128">Common locations exist for persisting state:</span></span>

* [<span data-ttu-id="2c459-129">Magazyn po stronie serwera</span><span class="sxs-lookup"><span data-stu-id="2c459-129">Server-side storage</span></span>](#server-side-storage-wasm)
* [<span data-ttu-id="2c459-130">Adres URL</span><span class="sxs-lookup"><span data-stu-id="2c459-130">URL</span></span>](#url-wasm)
* [<span data-ttu-id="2c459-131">Magazyn przeglądarki</span><span class="sxs-lookup"><span data-stu-id="2c459-131">Browser storage</span></span>](#browser-storage-wasm)
* [<span data-ttu-id="2c459-132">Usługa kontenera stanu w pamięci</span><span class="sxs-lookup"><span data-stu-id="2c459-132">In-memory state container service</span></span>](#in-memory-state-container-service-wasm)

<h2 id="server-side-storage-wasm"><span data-ttu-id="2c459-133">Magazyn po stronie serwera</span><span class="sxs-lookup"><span data-stu-id="2c459-133">Server-side storage</span></span></h2>

<span data-ttu-id="2c459-134">W przypadku trwałej trwałości danych obejmującej wielu użytkowników i wszystkie urządzenia aplikacja może używać niezależnego magazynu po stronie serwera, dostępnego za pośrednictwem internetowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="2c459-134">For permanent data persistence that spans multiple users and devices, the app can use independent server-side storage accessed via a web API.</span></span> <span data-ttu-id="2c459-135">Dostępne są następujące opcje:</span><span class="sxs-lookup"><span data-stu-id="2c459-135">Options include:</span></span>

* <span data-ttu-id="2c459-136">Blob Storage</span><span class="sxs-lookup"><span data-stu-id="2c459-136">Blob storage</span></span>
* <span data-ttu-id="2c459-137">Magazyn kluczy i wartości</span><span class="sxs-lookup"><span data-stu-id="2c459-137">Key-value storage</span></span>
* <span data-ttu-id="2c459-138">Relacyjna baza danych</span><span class="sxs-lookup"><span data-stu-id="2c459-138">Relational database</span></span>
* <span data-ttu-id="2c459-139">Table Storage</span><span class="sxs-lookup"><span data-stu-id="2c459-139">Table storage</span></span>

<span data-ttu-id="2c459-140">Po zapisaniu danych stan użytkownika jest zachowywany i dostępny w każdej nowej sesji przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="2c459-140">After data is saved, the user's state is retained and available in any new browser session.</span></span>

<span data-ttu-id="2c459-141">Ponieważ :::no-loc(Blazor WebAssembly)::: aplikacje działają w całości w przeglądarce użytkownika, wymagają dodatkowych środków w celu uzyskania dostępu do bezpiecznych systemów zewnętrznych, takich jak usługi magazynu i bazy danych.</span><span class="sxs-lookup"><span data-stu-id="2c459-141">Because :::no-loc(Blazor WebAssembly)::: apps run entirely in the user's browser, they require additional measures to access secure external systems, such as storage services and databases.</span></span> <span data-ttu-id="2c459-142">:::no-loc(Blazor WebAssembly)::: aplikacje są zabezpieczone w taki sam sposób, jak aplikacje jednostronicowe (aplikacji jednostronicowych).</span><span class="sxs-lookup"><span data-stu-id="2c459-142">:::no-loc(Blazor WebAssembly)::: apps are secured in the same manner as Single Page Applications (SPAs).</span></span> <span data-ttu-id="2c459-143">Zazwyczaj aplikacja uwierzytelnia użytkownika za pośrednictwem protokołu [OAuth](https://oauth.net) / [OpenID Connect Connect (OIDC)](https://openid.net/connect/) , a następnie współdziała z usługami magazynu i bazami danych za pośrednictwem wywołań interfejsu API sieci Web dla aplikacji po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="2c459-143">Typically, an app authenticates a user via [OAuth](https://oauth.net)/[OpenID Connect (OIDC)](https://openid.net/connect/) and then interacts with storage services and databases through web API calls to a server-side app.</span></span> <span data-ttu-id="2c459-144">Aplikacja po stronie serwera koryguje transfer danych między :::no-loc(Blazor WebAssembly)::: aplikacją a usługą magazynu lub bazą danych.</span><span class="sxs-lookup"><span data-stu-id="2c459-144">The server-side app mediates the transfer of data between the :::no-loc(Blazor WebAssembly)::: app and the storage service or database.</span></span> <span data-ttu-id="2c459-145">:::no-loc(Blazor WebAssembly):::Aplikacja zachowuje tymczasowe połączenie z aplikacją po stronie serwera, podczas gdy aplikacja po stronie serwera ma trwałe połączenie z magazynem.</span><span class="sxs-lookup"><span data-stu-id="2c459-145">The :::no-loc(Blazor WebAssembly)::: app maintains an ephemeral connection to the server-side app, while the server-side app has a persistent connection to storage.</span></span>

<span data-ttu-id="2c459-146">Więcej informacji można znaleźć w następujących zasobach:</span><span class="sxs-lookup"><span data-stu-id="2c459-146">For more information, see the following resources:</span></span>

* <xref:blazor/call-web-api>
* <xref:blazor/security/webassembly/index>
* <span data-ttu-id="2c459-147">:::no-loc(Blazor):::*Zabezpieczenia i :::no-loc(Identity):::* artykułu</span><span class="sxs-lookup"><span data-stu-id="2c459-147">:::no-loc(Blazor)::: *Security and :::no-loc(Identity):::* articles</span></span>

<span data-ttu-id="2c459-148">Aby uzyskać więcej informacji na temat opcji usługi Azure Data Storage, zobacz następujące tematy:</span><span class="sxs-lookup"><span data-stu-id="2c459-148">For more information on Azure data storage options, see the following:</span></span>

* [<span data-ttu-id="2c459-149">Bazy danych platformy Azure</span><span class="sxs-lookup"><span data-stu-id="2c459-149">Azure Databases</span></span>](https://azure.microsoft.com/product-categories/databases/)
* [<span data-ttu-id="2c459-150">Dokumentacja usługi Azure Storage</span><span class="sxs-lookup"><span data-stu-id="2c459-150">Azure Storage Documentation</span></span>](/azure/storage/)

<h2 id="url-wasm"><span data-ttu-id="2c459-151">Adres URL</span><span class="sxs-lookup"><span data-stu-id="2c459-151">URL</span></span></h2>

<span data-ttu-id="2c459-152">W przypadku danych przejściowych reprezentujących stan nawigacji należy modelować dane w ramach adresu URL.</span><span class="sxs-lookup"><span data-stu-id="2c459-152">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="2c459-153">Przykłady stanu użytkownika z modelem w adresie URL obejmują:</span><span class="sxs-lookup"><span data-stu-id="2c459-153">Examples of user state modeled in the URL include:</span></span>

* <span data-ttu-id="2c459-154">Identyfikator wyświetlonej jednostki.</span><span class="sxs-lookup"><span data-stu-id="2c459-154">The ID of a viewed entity.</span></span>
* <span data-ttu-id="2c459-155">Numer bieżącej strony w siatce stronicowanej.</span><span class="sxs-lookup"><span data-stu-id="2c459-155">The current page number in a paged grid.</span></span>

<span data-ttu-id="2c459-156">Zawartość paska adresu przeglądarki jest zachowywana, jeśli użytkownik ręcznie ponownie załaduje stronę.</span><span class="sxs-lookup"><span data-stu-id="2c459-156">The contents of the browser's address bar are retained if the user manually reloads the page.</span></span>

<span data-ttu-id="2c459-157">Aby uzyskać informacje na temat definiowania wzorców adresów URL za pomocą [`@page`](xref:mvc/views/razor#page) dyrektywy, zobacz <xref:blazor/fundamentals/routing> .</span><span class="sxs-lookup"><span data-stu-id="2c459-157">For information on defining URL patterns with the [`@page`](xref:mvc/views/razor#page) directive, see <xref:blazor/fundamentals/routing>.</span></span>

<h2 id="browser-storage-wasm"><span data-ttu-id="2c459-158">Magazyn przeglądarki</span><span class="sxs-lookup"><span data-stu-id="2c459-158">Browser storage</span></span></h2>

<span data-ttu-id="2c459-159">W przypadku danych przejściowych, które użytkownik aktywnie tworzy, często używaną lokalizacją magazynu jest przeglądarka [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) i [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) Kolekcje:</span><span class="sxs-lookup"><span data-stu-id="2c459-159">For transient data that the user is actively creating, a commonly used storage location is the browser's [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) collections:</span></span>

* <span data-ttu-id="2c459-160">`localStorage` jest zakresem okna przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="2c459-160">`localStorage` is scoped to the browser's window.</span></span> <span data-ttu-id="2c459-161">Jeśli użytkownik ponownie załaduje stronę lub zamknie i otworzy przeglądarkę, stan będzie się utrzymywał.</span><span class="sxs-lookup"><span data-stu-id="2c459-161">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="2c459-162">Jeśli użytkownik otworzy wiele kart przeglądarki, stan jest udostępniany na kartach.</span><span class="sxs-lookup"><span data-stu-id="2c459-162">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="2c459-163">Dane są zachowywane `localStorage` do momentu jawnego wyczyszczenia.</span><span class="sxs-lookup"><span data-stu-id="2c459-163">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="2c459-164">`sessionStorage` jest zakresem na karcie przeglądarki. Jeśli użytkownik ponownie załaduje kartę, stan będzie się utrzymywał.</span><span class="sxs-lookup"><span data-stu-id="2c459-164">`sessionStorage` is scoped to the browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="2c459-165">Jeśli użytkownik zamknie kartę lub przeglądarkę, stan zostanie utracony.</span><span class="sxs-lookup"><span data-stu-id="2c459-165">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="2c459-166">Jeśli użytkownik otworzy wiele kart przeglądarki, każda karta ma własną niezależną wersję danych.</span><span class="sxs-lookup"><span data-stu-id="2c459-166">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

> [!NOTE]
> <span data-ttu-id="2c459-167">`localStorage` i `sessionStorage` mogą być używane w :::no-loc(Blazor WebAssembly)::: aplikacjach, ale tylko przez napisanie kodu niestandardowego lub użycie pakietu innej firmy.</span><span class="sxs-lookup"><span data-stu-id="2c459-167">`localStorage` and `sessionStorage` can be used in :::no-loc(Blazor WebAssembly)::: apps but only by writing custom code or using a third-party package.</span></span>

<span data-ttu-id="2c459-168">Ogólnie rzecz biorąc, `sessionStorage` jest bezpiecznie używać.</span><span class="sxs-lookup"><span data-stu-id="2c459-168">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="2c459-169">`sessionStorage` pozwala uniknąć ryzyka, że użytkownik otwiera wiele kart i napotyka następujące kwestie:</span><span class="sxs-lookup"><span data-stu-id="2c459-169">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="2c459-170">Błędy w magazynie Stanów na kartach.</span><span class="sxs-lookup"><span data-stu-id="2c459-170">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="2c459-171">Zachowanie mylące, gdy karta zastępuje stan innych kart.</span><span class="sxs-lookup"><span data-stu-id="2c459-171">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="2c459-172">`localStorage` jest lepszym rozwiązaniem, jeśli aplikacja musi utrzymywać stan w trakcie zamykania i ponownego otwierania przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="2c459-172">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

> [!WARNING]
> <span data-ttu-id="2c459-173">Użytkownicy mogą wyświetlać lub naruszać dane przechowywane w `localStorage` i `sessionStorage` .</span><span class="sxs-lookup"><span data-stu-id="2c459-173">Users may view or tamper with the data stored in `localStorage` and `sessionStorage`.</span></span>

<h2 id="in-memory-state-container-service-wasm"><span data-ttu-id="2c459-174">Usługa kontenera stanu w pamięci</span><span class="sxs-lookup"><span data-stu-id="2c459-174">In-memory state container service</span></span></h2>

[!INCLUDE[](~/includes/blazor-state-management/state-container.md)]

## <a name="additional-resources"></a><span data-ttu-id="2c459-175">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="2c459-175">Additional resources</span></span>

* [<span data-ttu-id="2c459-176">Zapisz stan aplikacji przed operacją uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="2c459-176">Save app state before an authentication operation</span></span>](xref:blazor/security/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation)
* <xref:blazor/call-web-api>
* <xref:blazor/security/webassembly/index>

::: zone-end

::: zone pivot="server"

<span data-ttu-id="2c459-177">:::no-loc(Blazor Server)::: jest platformą aplikacji stanowych.</span><span class="sxs-lookup"><span data-stu-id="2c459-177">:::no-loc(Blazor Server)::: is a stateful app framework.</span></span> <span data-ttu-id="2c459-178">W większości przypadków aplikacja utrzymuje połączenie z serwerem.</span><span class="sxs-lookup"><span data-stu-id="2c459-178">Most of the time, the app maintains a connection to the server.</span></span> <span data-ttu-id="2c459-179">Stan użytkownika jest przechowywany w pamięci serwera w ramach *obwodu*.</span><span class="sxs-lookup"><span data-stu-id="2c459-179">The user's state is held in the server's memory in a *circuit*.</span></span> 

<span data-ttu-id="2c459-180">Przykłady stanu użytkownika przechowywanego w obwodzie:</span><span class="sxs-lookup"><span data-stu-id="2c459-180">Examples of user state held in a circuit include:</span></span>

* <span data-ttu-id="2c459-181">Hierarchia wystąpień składników i ich najnowsze dane wyjściowe renderowania w renderowanym interfejsie użytkownika.</span><span class="sxs-lookup"><span data-stu-id="2c459-181">The hierarchy of component instances and their most recent render output in the rendered UI.</span></span>
* <span data-ttu-id="2c459-182">Wartości pól i właściwości w wystąpieniach składników.</span><span class="sxs-lookup"><span data-stu-id="2c459-182">The values of fields and properties in component instances.</span></span>
* <span data-ttu-id="2c459-183">Dane przechowywane w wystąpieniach usługi [wtrysku zależności (di)](xref:fundamentals/dependency-injection) , które są objęte zakresem obwodu.</span><span class="sxs-lookup"><span data-stu-id="2c459-183">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span>

<span data-ttu-id="2c459-184">Stan użytkownika może również znajdować się w zmiennych JavaScript w zestawie pamięci w przeglądarce za pośrednictwem wywołań [międzyoperacyjnych języka JavaScript](xref:blazor/call-javascript-from-dotnet) .</span><span class="sxs-lookup"><span data-stu-id="2c459-184">User state might also be found in JavaScript variables in the browser's memory set via [JavaScript interop](xref:blazor/call-javascript-from-dotnet) calls.</span></span>

<span data-ttu-id="2c459-185">Jeśli użytkownik napotyka chwilową utratę połączenia sieciowego, program :::no-loc(Blazor)::: spróbuje ponownie połączyć użytkownika z oryginalnym obwodem.</span><span class="sxs-lookup"><span data-stu-id="2c459-185">If a user experiences a temporary network connection loss, :::no-loc(Blazor)::: attempts to reconnect the user to their original circuit with their original state.</span></span> <span data-ttu-id="2c459-186">Jednak ponowne połączenie użytkownika z oryginalnym obwodem w pamięci serwera nie zawsze jest możliwe:</span><span class="sxs-lookup"><span data-stu-id="2c459-186">However, reconnecting a user to their original circuit in the server's memory isn't always possible:</span></span>

* <span data-ttu-id="2c459-187">Serwer nie może całkowicie zachować rozłączonego obwodu.</span><span class="sxs-lookup"><span data-stu-id="2c459-187">The server can't retain a disconnected circuit forever.</span></span> <span data-ttu-id="2c459-188">Serwer musi zwolnić obwód rozłączony po upływie limitu czasu lub w przypadku, gdy na serwerze znajduje się pamięć.</span><span class="sxs-lookup"><span data-stu-id="2c459-188">The server must release a disconnected circuit after a timeout or when the server is under memory pressure.</span></span>
* <span data-ttu-id="2c459-189">W środowiskach wdrażania z obsługą równoważenia obciążenia poszczególne serwery mogą kończyć się niepowodzeniem lub być automatycznie usuwane, gdy nie są już wymagane do obsługi ogólnej liczby żądań.</span><span class="sxs-lookup"><span data-stu-id="2c459-189">In multi-server, load-balanced deployment environments, individual servers may fail or be automatically removed when no longer required to handle the overall volume of requests.</span></span> <span data-ttu-id="2c459-190">Oryginalne żądania przetwarzania serwera dla użytkownika mogą stać się niedostępne, gdy użytkownik spróbuje ponownie nawiązać połączenie.</span><span class="sxs-lookup"><span data-stu-id="2c459-190">The original server processing requests for a user may become unavailable when the user attempts to reconnect.</span></span>
* <span data-ttu-id="2c459-191">Użytkownik może zamknąć i ponownie otworzyć przeglądarkę lub ponownie załadować stronę, co spowoduje usunięcie wszystkich stanów przechowywanych w pamięci przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="2c459-191">The user might close and re-open their browser or reload the page, which removes any state held in the browser's memory.</span></span> <span data-ttu-id="2c459-192">Na przykład wartości zmiennych JavaScript ustawione za poorednictwem wywołań międzyoperacyjnych języka JavaScript są tracone.</span><span class="sxs-lookup"><span data-stu-id="2c459-192">For example, JavaScript variable values set through JavaScript interop calls are lost.</span></span>

<span data-ttu-id="2c459-193">Gdy nie można ponownie nawiązać połączenia użytkownika z oryginalnym obwodem, użytkownik otrzymuje nowy obwód z pustym stanem.</span><span class="sxs-lookup"><span data-stu-id="2c459-193">When a user can't be reconnected to their original circuit, the user receives a new circuit with an empty state.</span></span> <span data-ttu-id="2c459-194">Jest to równoznaczne z zamknięciem i ponownym otwarciem aplikacji klasycznej.</span><span class="sxs-lookup"><span data-stu-id="2c459-194">This is equivalent to closing and re-opening a desktop app.</span></span>

## <a name="persist-state-across-circuits"></a><span data-ttu-id="2c459-195">Stan utrwalania w obwodach</span><span class="sxs-lookup"><span data-stu-id="2c459-195">Persist state across circuits</span></span>

<span data-ttu-id="2c459-196">Ogólnie rzecz biorąc, utrzymuje stan w obwodach, w których użytkownicy aktywnie tworzą dane, a nie tylko odczytujące dane, które już istnieją.</span><span class="sxs-lookup"><span data-stu-id="2c459-196">Generally, maintain state across circuits where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="2c459-197">Aby zachować stan w obwodach, aplikacja musi utrzymywać dane w innej lokalizacji magazynu niż pamięć serwera.</span><span class="sxs-lookup"><span data-stu-id="2c459-197">To preserve state across circuits, the app must persist the data to some other storage location than the server's memory.</span></span> <span data-ttu-id="2c459-198">Trwałość stanu nie jest automatyczna.</span><span class="sxs-lookup"><span data-stu-id="2c459-198">State persistence isn't automatic.</span></span> <span data-ttu-id="2c459-199">Podczas tworzenia aplikacji należy wykonać kroki w celu zaimplementowania stanu trwałości danych stanowych.</span><span class="sxs-lookup"><span data-stu-id="2c459-199">You must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="2c459-200">Trwałość danych jest zazwyczaj wymagana tylko w przypadku stanu wysokiej wartości, który użytkownicy wystawią nakłady na tworzenie.</span><span class="sxs-lookup"><span data-stu-id="2c459-200">Data persistence is typically only required for high-value state that users expended effort to create.</span></span> <span data-ttu-id="2c459-201">W poniższych przykładach stan utrwalania polega na zapisywaniu czasu lub pomocy w działaniach komercyjnych:</span><span class="sxs-lookup"><span data-stu-id="2c459-201">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="2c459-202">Wieloetapowe formularze sieci Web: czasochłonne, aby użytkownik mógł ponownie wprowadzić dane dla kilku ukończonych kroków wieloetapowego formularza sieci Web, jeśli ich stan zostanie utracony.</span><span class="sxs-lookup"><span data-stu-id="2c459-202">Multi-step web forms: It's time-consuming for a user to re-enter data for several completed steps of a multi-step web form if their state is lost.</span></span> <span data-ttu-id="2c459-203">Użytkownik utraci stan w tym scenariuszu, jeśli opuszcza formularz i wróci później.</span><span class="sxs-lookup"><span data-stu-id="2c459-203">A user loses state in this scenario if they navigate away from the form and return later.</span></span>
* <span data-ttu-id="2c459-204">Koszyki: każdy handlowy istotny składnik aplikacji, który reprezentuje potencjalne dochody, może być utrzymywany.</span><span class="sxs-lookup"><span data-stu-id="2c459-204">Shopping carts: Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="2c459-205">Użytkownik, który straci swój stan, a tym samym koszyk, może zakupić mniejszą liczbę produktów lub usług w momencie powrotu do lokacji w przyszłości.</span><span class="sxs-lookup"><span data-stu-id="2c459-205">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="2c459-206">Aplikacja może utrzymywać tylko *stan aplikacji*.</span><span class="sxs-lookup"><span data-stu-id="2c459-206">An app can only persist *app state*.</span></span> <span data-ttu-id="2c459-207">Interfejsów użytkownika nie mogą być utrwalane, takie jak wystąpienia składników i ich drzewa renderowania.</span><span class="sxs-lookup"><span data-stu-id="2c459-207">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="2c459-208">Składniki i drzewa renderowania nie są generalnie serializowane.</span><span class="sxs-lookup"><span data-stu-id="2c459-208">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="2c459-209">Aby zachować stan interfejsu użytkownika, na przykład rozwinięte węzły kontrolki widoku drzewa, aplikacja musi używać niestandardowego kodu do modelowania zachowania stanu interfejsu użytkownika jako możliwy do serializacji stan aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2c459-209">To persist UI state, such as the expanded nodes of a tree view control, the app must use custom code to model the behavior of the UI state as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="2c459-210">Gdzie będzie trwały stan</span><span class="sxs-lookup"><span data-stu-id="2c459-210">Where to persist state</span></span>

<span data-ttu-id="2c459-211">Istnieją wspólne lokalizacje dla stanu utrwalania:</span><span class="sxs-lookup"><span data-stu-id="2c459-211">Common locations exist for persisting state:</span></span>

* [<span data-ttu-id="2c459-212">Magazyn po stronie serwera</span><span class="sxs-lookup"><span data-stu-id="2c459-212">Server-side storage</span></span>](#server-side-storage-server)
* [<span data-ttu-id="2c459-213">Adres URL</span><span class="sxs-lookup"><span data-stu-id="2c459-213">URL</span></span>](#url-server)
* [<span data-ttu-id="2c459-214">Magazyn przeglądarki</span><span class="sxs-lookup"><span data-stu-id="2c459-214">Browser storage</span></span>](#browser-storage-server)
* [<span data-ttu-id="2c459-215">Usługa kontenera stanu w pamięci</span><span class="sxs-lookup"><span data-stu-id="2c459-215">In-memory state container service</span></span>](#in-memory-state-container-service-server)

<h2 id="server-side-storage-server"><span data-ttu-id="2c459-216">Magazyn po stronie serwera</span><span class="sxs-lookup"><span data-stu-id="2c459-216">Server-side storage</span></span></h2>

<span data-ttu-id="2c459-217">W przypadku trwałej trwałości danych obejmującej wielu użytkowników i urządzenia aplikacja może korzystać z magazynu po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="2c459-217">For permanent data persistence that spans multiple users and devices, the app can use server-side storage.</span></span> <span data-ttu-id="2c459-218">Dostępne są następujące opcje:</span><span class="sxs-lookup"><span data-stu-id="2c459-218">Options include:</span></span>

* <span data-ttu-id="2c459-219">Blob Storage</span><span class="sxs-lookup"><span data-stu-id="2c459-219">Blob storage</span></span>
* <span data-ttu-id="2c459-220">Magazyn kluczy i wartości</span><span class="sxs-lookup"><span data-stu-id="2c459-220">Key-value storage</span></span>
* <span data-ttu-id="2c459-221">Relacyjna baza danych</span><span class="sxs-lookup"><span data-stu-id="2c459-221">Relational database</span></span>
* <span data-ttu-id="2c459-222">Table Storage</span><span class="sxs-lookup"><span data-stu-id="2c459-222">Table storage</span></span>

<span data-ttu-id="2c459-223">Po zapisaniu danych stan użytkownika jest zachowywany i dostępny w dowolnym nowym obwodie.</span><span class="sxs-lookup"><span data-stu-id="2c459-223">After data is saved, the user's state is retained and available in any new circuit.</span></span>

<span data-ttu-id="2c459-224">Aby uzyskać więcej informacji na temat opcji usługi Azure Data Storage, zobacz następujące tematy:</span><span class="sxs-lookup"><span data-stu-id="2c459-224">For more information on Azure data storage options, see the following:</span></span>

* [<span data-ttu-id="2c459-225">Bazy danych platformy Azure</span><span class="sxs-lookup"><span data-stu-id="2c459-225">Azure Databases</span></span>](https://azure.microsoft.com/product-categories/databases/)
* [<span data-ttu-id="2c459-226">Dokumentacja usługi Azure Storage</span><span class="sxs-lookup"><span data-stu-id="2c459-226">Azure Storage Documentation</span></span>](/azure/storage/)

<h2 id="url-server"><span data-ttu-id="2c459-227">Adres URL</span><span class="sxs-lookup"><span data-stu-id="2c459-227">URL</span></span></h2>

<span data-ttu-id="2c459-228">W przypadku danych przejściowych reprezentujących stan nawigacji należy modelować dane w ramach adresu URL.</span><span class="sxs-lookup"><span data-stu-id="2c459-228">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="2c459-229">Przykłady stanu użytkownika z modelem w adresie URL obejmują:</span><span class="sxs-lookup"><span data-stu-id="2c459-229">Examples of user state modeled in the URL include:</span></span>

* <span data-ttu-id="2c459-230">Identyfikator wyświetlonej jednostki.</span><span class="sxs-lookup"><span data-stu-id="2c459-230">The ID of a viewed entity.</span></span>
* <span data-ttu-id="2c459-231">Numer bieżącej strony w siatce stronicowanej.</span><span class="sxs-lookup"><span data-stu-id="2c459-231">The current page number in a paged grid.</span></span>

<span data-ttu-id="2c459-232">Zawartość paska adresu przeglądarki jest zachowywana:</span><span class="sxs-lookup"><span data-stu-id="2c459-232">The contents of the browser's address bar are retained:</span></span>

* <span data-ttu-id="2c459-233">Jeśli użytkownik ręcznie ponownie załaduje stronę.</span><span class="sxs-lookup"><span data-stu-id="2c459-233">If the user manually reloads the page.</span></span>
* <span data-ttu-id="2c459-234">Jeśli serwer sieci Web stał się niedostępny, a użytkownik jest zmuszony do ponownego załadowania strony, aby można było połączyć się z innym serwerem.</span><span class="sxs-lookup"><span data-stu-id="2c459-234">If the web server becomes unavailable, and the user is forced to reload the page in order to connect to a different server.</span></span>

<span data-ttu-id="2c459-235">Aby uzyskać informacje na temat definiowania wzorców adresów URL za pomocą [`@page`](xref:mvc/views/razor#page) dyrektywy, zobacz <xref:blazor/fundamentals/routing> .</span><span class="sxs-lookup"><span data-stu-id="2c459-235">For information on defining URL patterns with the [`@page`](xref:mvc/views/razor#page) directive, see <xref:blazor/fundamentals/routing>.</span></span>

<h2 id="browser-storage-server"><span data-ttu-id="2c459-236">Magazyn przeglądarki</span><span class="sxs-lookup"><span data-stu-id="2c459-236">Browser storage</span></span></h2>

<span data-ttu-id="2c459-237">W przypadku danych przejściowych, które użytkownik aktywnie tworzy, często używaną lokalizacją magazynu jest przeglądarka [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) i [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) Kolekcje:</span><span class="sxs-lookup"><span data-stu-id="2c459-237">For transient data that the user is actively creating, a commonly used storage location is the browser's [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) collections:</span></span>

* <span data-ttu-id="2c459-238">`localStorage` jest zakresem okna przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="2c459-238">`localStorage` is scoped to the browser's window.</span></span> <span data-ttu-id="2c459-239">Jeśli użytkownik ponownie załaduje stronę lub zamknie i otworzy przeglądarkę, stan będzie się utrzymywał.</span><span class="sxs-lookup"><span data-stu-id="2c459-239">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="2c459-240">Jeśli użytkownik otworzy wiele kart przeglądarki, stan jest udostępniany na kartach.</span><span class="sxs-lookup"><span data-stu-id="2c459-240">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="2c459-241">Dane są zachowywane `localStorage` do momentu jawnego wyczyszczenia.</span><span class="sxs-lookup"><span data-stu-id="2c459-241">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="2c459-242">`sessionStorage` jest zakresem na karcie przeglądarki. Jeśli użytkownik ponownie załaduje kartę, stan będzie się utrzymywał.</span><span class="sxs-lookup"><span data-stu-id="2c459-242">`sessionStorage` is scoped to the browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="2c459-243">Jeśli użytkownik zamknie kartę lub przeglądarkę, stan zostanie utracony.</span><span class="sxs-lookup"><span data-stu-id="2c459-243">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="2c459-244">Jeśli użytkownik otworzy wiele kart przeglądarki, każda karta ma własną niezależną wersję danych.</span><span class="sxs-lookup"><span data-stu-id="2c459-244">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

<span data-ttu-id="2c459-245">Ogólnie rzecz biorąc, `sessionStorage` jest bezpiecznie używać.</span><span class="sxs-lookup"><span data-stu-id="2c459-245">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="2c459-246">`sessionStorage` pozwala uniknąć ryzyka, że użytkownik otwiera wiele kart i napotyka następujące kwestie:</span><span class="sxs-lookup"><span data-stu-id="2c459-246">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="2c459-247">Błędy w magazynie Stanów na kartach.</span><span class="sxs-lookup"><span data-stu-id="2c459-247">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="2c459-248">Zachowanie mylące, gdy karta zastępuje stan innych kart.</span><span class="sxs-lookup"><span data-stu-id="2c459-248">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="2c459-249">`localStorage` jest lepszym rozwiązaniem, jeśli aplikacja musi utrzymywać stan w trakcie zamykania i ponownego otwierania przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="2c459-249">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

<span data-ttu-id="2c459-250">Ostrzeżenia dotyczące korzystania z magazynu przeglądarki:</span><span class="sxs-lookup"><span data-stu-id="2c459-250">Caveats for using browser storage:</span></span>

* <span data-ttu-id="2c459-251">Podobnie jak w przypadku korzystania z bazy danych po stronie serwera, ładowanie i zapisywanie danych są asynchroniczne.</span><span class="sxs-lookup"><span data-stu-id="2c459-251">Similar to the use of a server-side database, loading and saving data are asynchronous.</span></span>
* <span data-ttu-id="2c459-252">W przeciwieństwie do bazy danych po stronie serwera, magazyn nie jest dostępny podczas renderowania wstępnego, ponieważ żądana strona nie istnieje w przeglądarce na etapie renderowania.</span><span class="sxs-lookup"><span data-stu-id="2c459-252">Unlike a server-side database, storage isn't available during prerendering because the requested page doesn't exist in the browser during the prerendering stage.</span></span>
* <span data-ttu-id="2c459-253">Przechowywanie kilku kilobajtów danych jest rozsądne dla :::no-loc(Blazor Server)::: aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2c459-253">Storage of a few kilobytes of data is reasonable to persist for :::no-loc(Blazor Server)::: apps.</span></span> <span data-ttu-id="2c459-254">Po kilku kilobajtach należy wziąć pod uwagę wpływ na wydajność, ponieważ dane są ładowane i zapisywane w sieci.</span><span class="sxs-lookup"><span data-stu-id="2c459-254">Beyond a few kilobytes, you must consider the performance implications because the data is loaded and saved across the network.</span></span>
* <span data-ttu-id="2c459-255">Użytkownicy mogą wyświetlać i modyfikować dane.</span><span class="sxs-lookup"><span data-stu-id="2c459-255">Users may view or tamper with the data.</span></span> <span data-ttu-id="2c459-256">[Ochrona danych ASP.NET Core](xref:security/data-protection/introduction) może ograniczyć ryzyko.</span><span class="sxs-lookup"><span data-stu-id="2c459-256">[ASP.NET Core Data Protection](xref:security/data-protection/introduction) can mitigate the risk.</span></span> <span data-ttu-id="2c459-257">Na przykład [ASP.NET Core chronionej przeglądarki](#aspnet-core-protected-browser-storage) używa ASP.NET Core ochrony danych.</span><span class="sxs-lookup"><span data-stu-id="2c459-257">For example, [ASP.NET Core Protected Browser Storage](#aspnet-core-protected-browser-storage) uses ASP.NET Core Data Protection.</span></span>

<span data-ttu-id="2c459-258">Pakiety NuGet innych firm zapewniają interfejsy API do pracy z `localStorage` i `sessionStorage` .</span><span class="sxs-lookup"><span data-stu-id="2c459-258">Third-party NuGet packages provide APIs for working with `localStorage` and `sessionStorage`.</span></span> <span data-ttu-id="2c459-259">Warto rozważać wybór pakietu, który w sposób przezroczysty używa [ASP.NET Core ochrony danych](xref:security/data-protection/introduction).</span><span class="sxs-lookup"><span data-stu-id="2c459-259">It's worth considering choosing a package that transparently uses [ASP.NET Core Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="2c459-260">Ochrona danych szyfruje przechowywane dane i zmniejsza potencjalne ryzyko naruszenia przechowywanych danych.</span><span class="sxs-lookup"><span data-stu-id="2c459-260">Data Protection encrypts stored data and reduces the potential risk of tampering with stored data.</span></span> <span data-ttu-id="2c459-261">Jeśli dane serializowane w formacie JSON są przechowywane w postaci zwykłego tekstu, użytkownicy mogą zobaczyć dane przy użyciu narzędzi deweloperskich przeglądarki, a także zmodyfikować przechowywane dane.</span><span class="sxs-lookup"><span data-stu-id="2c459-261">If JSON-serialized data is stored in plain text, users can see the data using browser developer tools and also modify the stored data.</span></span> <span data-ttu-id="2c459-262">Zabezpieczanie danych nie zawsze jest problemem, ponieważ dane mogą być proste.</span><span class="sxs-lookup"><span data-stu-id="2c459-262">Securing data isn't always a problem because the data might be trivial in nature.</span></span> <span data-ttu-id="2c459-263">Na przykład odczytywanie lub modyfikowanie zapisanego koloru elementu interfejsu użytkownika nie jest istotnym zagrożeniem bezpieczeństwa użytkownika lub organizacji.</span><span class="sxs-lookup"><span data-stu-id="2c459-263">For example, reading or modifying the stored color of a UI element isn't a significant security risk to the user or the organization.</span></span> <span data-ttu-id="2c459-264">Unikaj zezwalania użytkownikom na inspekcję i manipulowanie *danymi poufnymi*.</span><span class="sxs-lookup"><span data-stu-id="2c459-264">Avoid allowing users to inspect or tamper with *sensitive data*.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="aspnet-core-protected-browser-storage"></a><span data-ttu-id="2c459-265">ASP.NET Core chronionego magazynu przeglądarki</span><span class="sxs-lookup"><span data-stu-id="2c459-265">ASP.NET Core Protected Browser Storage</span></span>

<span data-ttu-id="2c459-266">ASP.NET Core magazynu chronionej przeglądarki wykorzystuje [ochronę danych ASP.NET Core](xref:security/data-protection/introduction) dla [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) i [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) .</span><span class="sxs-lookup"><span data-stu-id="2c459-266">ASP.NET Core Protected Browser Storage leverages [ASP.NET Core Data Protection](xref:security/data-protection/introduction) for [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage).</span></span>

> [!NOTE]
> <span data-ttu-id="2c459-267">Magazyn chronionej przeglądarki opiera się na ASP.NET Core ochrony danych i jest obsługiwany tylko w przypadku :::no-loc(Blazor Server)::: aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2c459-267">Protected Browser Storage relies on ASP.NET Core Data Protection and is only supported for :::no-loc(Blazor Server)::: apps.</span></span>

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="2c459-268">Zapisz i Załaduj dane w składniku</span><span class="sxs-lookup"><span data-stu-id="2c459-268">Save and load data within a component</span></span>

<span data-ttu-id="2c459-269">W dowolnym składniku wymagającym ładowania lub zapisywania danych w magazynie przeglądarki Użyj [`@inject`](xref:mvc/views/razor#inject) dyrektywy, aby wstrzyknąć wystąpienie jednego z następujących elementów:</span><span class="sxs-lookup"><span data-stu-id="2c459-269">In any component that requires loading or saving data to browser storage, use the [`@inject`](xref:mvc/views/razor#inject) directive to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="2c459-270">Wybór zależy od lokalizacji magazynu przeglądarki, która ma być używana.</span><span class="sxs-lookup"><span data-stu-id="2c459-270">The choice depends on which browser storage location you wish to use.</span></span> <span data-ttu-id="2c459-271">W poniższym przykładzie `sessionStorage` jest używany:</span><span class="sxs-lookup"><span data-stu-id="2c459-271">In the following example, `sessionStorage` is used:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="2c459-272">`@using`Dyrektywa może zostać umieszczona w `_Imports.razor` pliku aplikacji zamiast w składniku.</span><span class="sxs-lookup"><span data-stu-id="2c459-272">The `@using` directive can be placed in the app's `_Imports.razor` file instead of in the component.</span></span> <span data-ttu-id="2c459-273">Użycie `_Imports.razor` pliku sprawia, że przestrzeń nazw jest dostępna dla większych segmentów aplikacji lub całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2c459-273">Use of the `_Imports.razor` file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="2c459-274">Aby zachować `currentCount` wartość w `Counter` składniku aplikacji na podstawie :::no-loc(Blazor Server)::: szablonu projektu, zmodyfikuj `IncrementCount` metodę, która ma być używana `ProtectedSessionStore.SetAsync` :</span><span class="sxs-lookup"><span data-stu-id="2c459-274">To persist the `currentCount` value in the `Counter` component of an app based on the :::no-loc(Blazor Server)::: project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

<span data-ttu-id="2c459-275">W większych, bardziej realistycznych aplikacjach przechowywanie pojedynczych pól jest mało prawdopodobne.</span><span class="sxs-lookup"><span data-stu-id="2c459-275">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="2c459-276">Aplikacje są bardziej podobne do przechowywania całych obiektów modelu, które zawierają złożone Stany.</span><span class="sxs-lookup"><span data-stu-id="2c459-276">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="2c459-277">`ProtectedSessionStore` automatycznie deserializacji i deserializacji dane JSON do przechowywania obiektów stanu złożonego.</span><span class="sxs-lookup"><span data-stu-id="2c459-277">`ProtectedSessionStore` automatically serializes and deserializes JSON data to store complex state objects.</span></span>

<span data-ttu-id="2c459-278">W poprzednim przykładzie kodu `currentCount` dane są przechowywane jako `sessionStorage['count']` w przeglądarce użytkownika.</span><span class="sxs-lookup"><span data-stu-id="2c459-278">In the preceding code example, the `currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="2c459-279">Dane nie są przechowywane w postaci zwykłego tekstu, ale nie są chronione przy użyciu ASP.NET Core ochrony danych.</span><span class="sxs-lookup"><span data-stu-id="2c459-279">The data isn't stored in plain text but rather is protected using ASP.NET Core Data Protection.</span></span> <span data-ttu-id="2c459-280">Zaszyfrowane dane można sprawdzić, jeśli `sessionStorage['count']` są oceniane w konsoli dewelopera w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="2c459-280">The encrypted data can be inspected if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="2c459-281">Aby odzyskać `currentCount` dane, jeśli użytkownik powróci do `Counter` składnika później, w tym, jeśli użytkownik znajduje się w nowym obwodzie, użyj `ProtectedSessionStore.GetAsync` :</span><span class="sxs-lookup"><span data-stu-id="2c459-281">To recover the `currentCount` data if the user returns to the `Counter` component later, including if the user is on a new circuit, use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    var result = await ProtectedSessionStore.GetAsync<int>("count");
    currentCount = result.Success ? result.Value : 0;
}
```

<span data-ttu-id="2c459-282">Jeśli parametry składnika obejmują stan nawigacji, wywołanie `ProtectedSessionStore.GetAsync` i przypisanie `null` niewyniku do <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> , nie <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="2c459-282">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign a non-`null` result in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>, not <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span> <span data-ttu-id="2c459-283"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> jest wywoływana tylko raz podczas pierwszego wystąpienia składnika.</span><span class="sxs-lookup"><span data-stu-id="2c459-283"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is only called once when the component is first instantiated.</span></span> <span data-ttu-id="2c459-284"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> nie zostanie wywołana ponownie później, jeśli użytkownik przejdzie do innego adresu URL, a pozostałe na tej samej stronie.</span><span class="sxs-lookup"><span data-stu-id="2c459-284"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span> <span data-ttu-id="2c459-285">Aby uzyskać więcej informacji, zobacz <xref:blazor/components/lifecycle>.</span><span class="sxs-lookup"><span data-stu-id="2c459-285">For more information, see <xref:blazor/components/lifecycle>.</span></span>

> [!WARNING]
> <span data-ttu-id="2c459-286">Przykłady w tej sekcji działają tylko wtedy, gdy serwer nie ma włączonej obsługi przed renderowaniem.</span><span class="sxs-lookup"><span data-stu-id="2c459-286">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="2c459-287">Po włączeniu obsługi przed renderowaniem zostanie wygenerowany błąd wyjaśniający, że nie można wystawić wywołań międzyoperacyjnych języka JavaScript, ponieważ składnik jest wstępnie renderowany.</span><span class="sxs-lookup"><span data-stu-id="2c459-287">With prerendering enabled, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>
>
> <span data-ttu-id="2c459-288">Wyłącz renderowanie lub Dodaj dodatkowy kod, aby współpracował z renderowaniem.</span><span class="sxs-lookup"><span data-stu-id="2c459-288">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="2c459-289">Aby dowiedzieć się więcej na temat pisania kodu, który działa z renderowaniem, zobacz sekcję [Obsługa przed renderowaniem](#handle-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="2c459-289">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="2c459-290">Obsłuż stan ładowania</span><span class="sxs-lookup"><span data-stu-id="2c459-290">Handle the loading state</span></span>

<span data-ttu-id="2c459-291">Ponieważ dostęp do magazynu przeglądarki odbywa się asynchronicznie za pośrednictwem połączenia sieciowego, zawsze istnieje okres, po upływie którego dane są ładowane i dostępne dla składnika.</span><span class="sxs-lookup"><span data-stu-id="2c459-291">Since browser storage is accessed asynchronously over a network connection, there's always a period of time before the data is loaded and available to a component.</span></span> <span data-ttu-id="2c459-292">Aby uzyskać najlepsze wyniki, renderowanie komunikatu o stanie ładowania podczas ładowania jest w toku zamiast wyświetlania danych pustych lub domyślnych.</span><span class="sxs-lookup"><span data-stu-id="2c459-292">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="2c459-293">Jednym z rozwiązań jest śledzenie, czy dane są `null` , co oznacza, że dane są nadal ładowane.</span><span class="sxs-lookup"><span data-stu-id="2c459-293">One approach is to track whether the data is `null`, which means that the data is still loading.</span></span> <span data-ttu-id="2c459-294">W `Counter` składniku domyślnym liczba jest przechowywana w `int` .</span><span class="sxs-lookup"><span data-stu-id="2c459-294">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="2c459-295">[Wprowadź `currentCount` wartość null](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) , dodając znak zapytania ( `?` ) do typu ( `int` ):</span><span class="sxs-lookup"><span data-stu-id="2c459-295">[Make `currentCount` nullable](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? currentCount;
```

<span data-ttu-id="2c459-296">Zamiast bezwarunkowo wyświetlać liczbę i **`Increment`** przycisk, Wyświetl te elementy tylko wtedy, gdy dane są ładowane przez sprawdzenie <xref:System.Nullable%601.HasValue%2A> :</span><span class="sxs-lookup"><span data-stu-id="2c459-296">Instead of unconditionally displaying the count and **`Increment`** button, display these elements only if the data is loaded by checking <xref:System.Nullable%601.HasValue%2A>:</span></span>

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

### <a name="handle-prerendering"></a><span data-ttu-id="2c459-297">Obsługa przed renderowaniem</span><span class="sxs-lookup"><span data-stu-id="2c459-297">Handle prerendering</span></span>

<span data-ttu-id="2c459-298">Podczas renderowania:</span><span class="sxs-lookup"><span data-stu-id="2c459-298">During prerendering:</span></span>

* <span data-ttu-id="2c459-299">Połączenie interaktywne z przeglądarką użytkownika nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="2c459-299">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="2c459-300">Przeglądarka nie zawiera jeszcze strony, w której można uruchomić kod JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2c459-300">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="2c459-301">`localStorage` lub `sessionStorage` nie są dostępne podczas renderowania.</span><span class="sxs-lookup"><span data-stu-id="2c459-301">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="2c459-302">Jeśli składnik próbuje współdziałać z magazynem, zostanie wygenerowany błąd z wyjaśnieniem, że nie można wystawić wywołań międzyoperacyjnych języka JavaScript, ponieważ składnik jest wstępnie renderowany.</span><span class="sxs-lookup"><span data-stu-id="2c459-302">If the component attempts to interact with storage, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>

<span data-ttu-id="2c459-303">Jednym ze sposobów na rozwiązanie błędu jest wyłączenie renderowania.</span><span class="sxs-lookup"><span data-stu-id="2c459-303">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="2c459-304">Jest to zazwyczaj najlepszym wyborem, jeśli aplikacja znacznie korzysta z magazynu opartego na przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="2c459-304">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="2c459-305">Renderowanie zwiększa złożoność i nie korzysta z aplikacji, ponieważ aplikacja nie może przeprowadzić renderowania żadnej przydatnej zawartości do momentu, gdy nie `localStorage` `sessionStorage` jest dostępna.</span><span class="sxs-lookup"><span data-stu-id="2c459-305">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="2c459-306">Aby wyłączyć renderowanie, Otwórz `Pages/_Host.cshtml` plik i Zmień `render-mode` atrybut [pomocnika znacznika składnika](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) , aby <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> :</span><span class="sxs-lookup"><span data-stu-id="2c459-306">To disable prerendering, open the `Pages/_Host.cshtml` file and change the `render-mode` attribute of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>:</span></span>

```cshtml
<component type="typeof(App)" render-mode="Server" />
```

<span data-ttu-id="2c459-307">Renderowanie może być przydatne w przypadku innych stron, które nie używają `localStorage` ani `sessionStorage` .</span><span class="sxs-lookup"><span data-stu-id="2c459-307">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="2c459-308">Aby zachować renderowanie, odłóż operację ładowania do momentu podłączenia przeglądarki do obwodu.</span><span class="sxs-lookup"><span data-stu-id="2c459-308">To retain prerendering, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="2c459-309">Poniżej przedstawiono przykład przechowywania wartości licznika:</span><span class="sxs-lookup"><span data-stu-id="2c459-309">The following is an example for storing a counter value:</span></span>

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

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="2c459-310">Wyrównać zachowywanie stanu do wspólnej lokalizacji</span><span class="sxs-lookup"><span data-stu-id="2c459-310">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="2c459-311">Jeśli wiele składników korzysta z magazynu opartego na przeglądarce, ponowne implementowanie kodu dostawcy stanu wiele razy powoduje utworzenie duplikacji kodu.</span><span class="sxs-lookup"><span data-stu-id="2c459-311">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="2c459-312">Jedną z opcji unikania duplikowania kodu jest utworzenie *składnika nadrzędnego dostawcy stanu* , który hermetyzuje logikę dostawcy stanu.</span><span class="sxs-lookup"><span data-stu-id="2c459-312">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="2c459-313">Składniki podrzędne mogą współpracować z trwałymi danymi bez względu na mechanizm trwałości stanu.</span><span class="sxs-lookup"><span data-stu-id="2c459-313">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="2c459-314">W poniższym przykładzie `CounterStateProvider` składnika dane licznika są utrwalane `sessionStorage` :</span><span class="sxs-lookup"><span data-stu-id="2c459-314">In the following example of a `CounterStateProvider` component, counter data is persisted to `sessionStorage`:</span></span>

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

<span data-ttu-id="2c459-315">`CounterStateProvider`Składnik obsługuje fazę ładowania, przez co nie renderuje jej zawartości podrzędnej do momentu ukończenia ładowania.</span><span class="sxs-lookup"><span data-stu-id="2c459-315">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="2c459-316">Aby użyć `CounterStateProvider` składnika, zawiń wystąpienie składnika wokół dowolnego innego składnika, który wymaga dostępu do stanu licznika.</span><span class="sxs-lookup"><span data-stu-id="2c459-316">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="2c459-317">Aby zapewnić dostępność stanu dla wszystkich składników w aplikacji, zawiń `CounterStateProvider` składnik wokół składnika <xref:Microsoft.AspNetCore.Components.Routing.Router> w `App` składniku ( `App.razor` ):</span><span class="sxs-lookup"><span data-stu-id="2c459-317">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the <xref:Microsoft.AspNetCore.Components.Routing.Router> in the `App` component (`App.razor`):</span></span>

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

<span data-ttu-id="2c459-318">Zapakowane składniki są odbierane i mogą modyfikować stan trwałych liczników.</span><span class="sxs-lookup"><span data-stu-id="2c459-318">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="2c459-319">Poniższy `Counter` składnik implementuje wzorzec:</span><span class="sxs-lookup"><span data-stu-id="2c459-319">The following `Counter` component implements the pattern:</span></span>

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

<span data-ttu-id="2c459-320">Poprzedni składnik nie jest wymagany do współpracy z programem `ProtectedBrowserStorage` ani nie zajmuje się fazą "Ładowanie".</span><span class="sxs-lookup"><span data-stu-id="2c459-320">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="2c459-321">Aby można było zaradzić sobie z instrukcją prerenderingu zgodnie z wcześniejszym opisem, `CounterStateProvider` może zostać zmieniona, aby wszystkie składniki korzystające z danych licznika automatycznie działały z użyciem prerenderowania.</span><span class="sxs-lookup"><span data-stu-id="2c459-321">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="2c459-322">Aby uzyskać więcej informacji, zobacz sekcję [Obsługa przed renderowaniem](#handle-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="2c459-322">For more information, see the [Handle prerendering](#handle-prerendering) section.</span></span>

<span data-ttu-id="2c459-323">Ogólnie rzecz biorąc, zalecany jest wzorzec *składnika nadrzędnego dostawcy stanu* :</span><span class="sxs-lookup"><span data-stu-id="2c459-323">In general, the *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="2c459-324">Aby korzystać z stanu w wielu składnikach.</span><span class="sxs-lookup"><span data-stu-id="2c459-324">To consume state across many components.</span></span>
* <span data-ttu-id="2c459-325">Jeśli istnieje tylko jeden obiekt stanu najwyższego poziomu, który ma być trwały.</span><span class="sxs-lookup"><span data-stu-id="2c459-325">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="2c459-326">Aby zachować wiele różnych obiektów stanu i korzystać z różnych podzbiorów obiektów w różnych miejscach, lepiej jest unikać utrwalania stanu w całości.</span><span class="sxs-lookup"><span data-stu-id="2c459-326">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid persisting state globally.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="protected-browser-storage-experimental-nuget-package"></a><span data-ttu-id="2c459-327">Eksperymentalny pakiet NuGet magazynu chronionej przeglądarki</span><span class="sxs-lookup"><span data-stu-id="2c459-327">Protected Browser Storage experimental NuGet package</span></span>

<span data-ttu-id="2c459-328">ASP.NET Core magazynu chronionej przeglądarki wykorzystuje [ochronę danych ASP.NET Core](xref:security/data-protection/introduction) dla [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) i [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage) .</span><span class="sxs-lookup"><span data-stu-id="2c459-328">ASP.NET Core Protected Browser Storage leverages [ASP.NET Core Data Protection](xref:security/data-protection/introduction) for [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) and [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage).</span></span>

> [!WARNING]
> <span data-ttu-id="2c459-329">`Microsoft.AspNetCore.ProtectedBrowserStorage` jest nieobsługiwanym, eksperymentalnym pakietem nieodpowiedninym do użycia w środowisku produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="2c459-329">`Microsoft.AspNetCore.ProtectedBrowserStorage` is an unsupported, experimental package unsuitable for production use.</span></span>
>
> <span data-ttu-id="2c459-330">Pakiet jest dostępny tylko do użytku w aplikacjach ASP.NET Core 3,1 :::no-loc(Blazor Server)::: .</span><span class="sxs-lookup"><span data-stu-id="2c459-330">The package is only available for use in ASP.NET Core 3.1 :::no-loc(Blazor Server)::: apps.</span></span>

### <a name="configuration"></a><span data-ttu-id="2c459-331">Konfiguracja</span><span class="sxs-lookup"><span data-stu-id="2c459-331">Configuration</span></span>

1. <span data-ttu-id="2c459-332">Dodaj odwołanie do pakietu do [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage) .</span><span class="sxs-lookup"><span data-stu-id="2c459-332">Add a package reference to [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>
1. <span data-ttu-id="2c459-333">W `Pages/_Host.cshtml` pliku Dodaj następujący skrypt wewnątrz tagu zamykającego `</body>` :</span><span class="sxs-lookup"><span data-stu-id="2c459-333">In the `Pages/_Host.cshtml` file, add the following script inside the closing `</body>` tag:</span></span>

   ```cshtml
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. <span data-ttu-id="2c459-334">W programie `Startup.ConfigureServices` Wywołaj `AddProtectedBrowserStorage` `localStorage` kolekcję usługi i Dodaj do `sessionStorage` niej usługi:</span><span class="sxs-lookup"><span data-stu-id="2c459-334">In `Startup.ConfigureServices`, call `AddProtectedBrowserStorage` to add `localStorage` and `sessionStorage` services to the service collection:</span></span>

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="2c459-335">Zapisz i Załaduj dane w składniku</span><span class="sxs-lookup"><span data-stu-id="2c459-335">Save and load data within a component</span></span>

<span data-ttu-id="2c459-336">W dowolnym składniku wymagającym ładowania lub zapisywania danych w magazynie przeglądarki Użyj [`@inject`](xref:mvc/views/razor#inject) dyrektywy, aby wstrzyknąć wystąpienie jednego z następujących elementów:</span><span class="sxs-lookup"><span data-stu-id="2c459-336">In any component that requires loading or saving data to browser storage, use the [`@inject`](xref:mvc/views/razor#inject) directive to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="2c459-337">Wybór zależy od lokalizacji magazynu przeglądarki, która ma być używana.</span><span class="sxs-lookup"><span data-stu-id="2c459-337">The choice depends on which browser storage location you wish to use.</span></span> <span data-ttu-id="2c459-338">W poniższym przykładzie `sessionStorage` jest używany:</span><span class="sxs-lookup"><span data-stu-id="2c459-338">In the following example, `sessionStorage` is used:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="2c459-339">`@using`Instrukcja może zostać umieszczona w `_Imports.razor` pliku, a nie w składniku.</span><span class="sxs-lookup"><span data-stu-id="2c459-339">The `@using` statement can be placed into an `_Imports.razor` file instead of in the component.</span></span> <span data-ttu-id="2c459-340">Użycie `_Imports.razor` pliku sprawia, że przestrzeń nazw jest dostępna dla większych segmentów aplikacji lub całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="2c459-340">Use of the `_Imports.razor` file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="2c459-341">Aby zachować `currentCount` wartość w `Counter` składniku aplikacji na podstawie :::no-loc(Blazor Server)::: szablonu projektu, zmodyfikuj `IncrementCount` metodę, która ma być używana `ProtectedSessionStore.SetAsync` :</span><span class="sxs-lookup"><span data-stu-id="2c459-341">To persist the `currentCount` value in the `Counter` component of an app based on the :::no-loc(Blazor Server)::: project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

<span data-ttu-id="2c459-342">W większych, bardziej realistycznych aplikacjach przechowywanie pojedynczych pól jest mało prawdopodobne.</span><span class="sxs-lookup"><span data-stu-id="2c459-342">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="2c459-343">Aplikacje są bardziej podobne do przechowywania całych obiektów modelu, które zawierają złożone Stany.</span><span class="sxs-lookup"><span data-stu-id="2c459-343">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="2c459-344">`ProtectedSessionStore` automatycznie serializować i deserializacji danych JSON.</span><span class="sxs-lookup"><span data-stu-id="2c459-344">`ProtectedSessionStore` automatically serializes and deserializes JSON data.</span></span>

<span data-ttu-id="2c459-345">W poprzednim przykładzie kodu `currentCount` dane są przechowywane jako `sessionStorage['count']` w przeglądarce użytkownika.</span><span class="sxs-lookup"><span data-stu-id="2c459-345">In the preceding code example, the `currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="2c459-346">Dane nie są przechowywane w postaci zwykłego tekstu, ale nie są chronione przy użyciu ASP.NET Core ochrony danych.</span><span class="sxs-lookup"><span data-stu-id="2c459-346">The data isn't stored in plain text but rather is protected using ASP.NET Core Data Protection.</span></span> <span data-ttu-id="2c459-347">Zaszyfrowane dane można sprawdzić, jeśli `sessionStorage['count']` są oceniane w konsoli dewelopera w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="2c459-347">The encrypted data can be inspected if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="2c459-348">Aby odzyskać `currentCount` dane, jeśli użytkownik powróci do `Counter` składnika później, w tym, jeśli znajdują się w całkowicie nowym obwodzie, użyj `ProtectedSessionStore.GetAsync` :</span><span class="sxs-lookup"><span data-stu-id="2c459-348">To recover the `currentCount` data if the user returns to the `Counter` component later, including if they're on an entirely new circuit, use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

<span data-ttu-id="2c459-349">Jeśli parametry składnika obejmują stan nawigacji, wywołaj `ProtectedSessionStore.GetAsync` i przypisz wynik w <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> , nie <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="2c459-349">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign the result in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>, not <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span> <span data-ttu-id="2c459-350"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> jest wywoływana tylko raz podczas pierwszego wystąpienia składnika.</span><span class="sxs-lookup"><span data-stu-id="2c459-350"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> is only called once when the component is first instantiated.</span></span> <span data-ttu-id="2c459-351"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> nie zostanie wywołana ponownie później, jeśli użytkownik przejdzie do innego adresu URL, a pozostałe na tej samej stronie.</span><span class="sxs-lookup"><span data-stu-id="2c459-351"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span> <span data-ttu-id="2c459-352">Aby uzyskać więcej informacji, zobacz <xref:blazor/components/lifecycle>.</span><span class="sxs-lookup"><span data-stu-id="2c459-352">For more information, see <xref:blazor/components/lifecycle>.</span></span>

> [!WARNING]
> <span data-ttu-id="2c459-353">Przykłady w tej sekcji działają tylko wtedy, gdy serwer nie ma włączonej obsługi przed renderowaniem.</span><span class="sxs-lookup"><span data-stu-id="2c459-353">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="2c459-354">Po włączeniu obsługi przed renderowaniem zostanie wygenerowany błąd wyjaśniający, że nie można wystawić wywołań międzyoperacyjnych języka JavaScript, ponieważ składnik jest wstępnie renderowany.</span><span class="sxs-lookup"><span data-stu-id="2c459-354">With prerendering enabled, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>
>
> <span data-ttu-id="2c459-355">Wyłącz renderowanie lub Dodaj dodatkowy kod, aby współpracował z renderowaniem.</span><span class="sxs-lookup"><span data-stu-id="2c459-355">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="2c459-356">Aby dowiedzieć się więcej na temat pisania kodu, który działa z renderowaniem, zobacz sekcję [Obsługa przed renderowaniem](#handle-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="2c459-356">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="2c459-357">Obsłuż stan ładowania</span><span class="sxs-lookup"><span data-stu-id="2c459-357">Handle the loading state</span></span>

<span data-ttu-id="2c459-358">Ponieważ dostęp do magazynu przeglądarki odbywa się asynchronicznie za pośrednictwem połączenia sieciowego, zawsze istnieje okres, po upływie którego dane są ładowane i dostępne dla składnika.</span><span class="sxs-lookup"><span data-stu-id="2c459-358">Since browser storage is accessed asynchronously over a network connection, there's always a period of time before the data is loaded and available to a component.</span></span> <span data-ttu-id="2c459-359">Aby uzyskać najlepsze wyniki, renderowanie komunikatu o stanie ładowania podczas ładowania jest w toku zamiast wyświetlania danych pustych lub domyślnych.</span><span class="sxs-lookup"><span data-stu-id="2c459-359">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="2c459-360">Jednym z rozwiązań jest śledzenie, czy dane są `null` , co oznacza, że dane są nadal ładowane.</span><span class="sxs-lookup"><span data-stu-id="2c459-360">One approach is to track whether the data is `null`, which means that the data is still loading.</span></span> <span data-ttu-id="2c459-361">W `Counter` składniku domyślnym liczba jest przechowywana w `int` .</span><span class="sxs-lookup"><span data-stu-id="2c459-361">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="2c459-362">[Wprowadź `currentCount` wartość null](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) , dodając znak zapytania ( `?` ) do typu ( `int` ):</span><span class="sxs-lookup"><span data-stu-id="2c459-362">[Make `currentCount` nullable](/dotnet/csharp/language-reference/builtin-types/nullable-value-types) by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? currentCount;
```

<span data-ttu-id="2c459-363">Zamiast bezwarunkowo wyświetlić liczbę i **`Increment`** przycisk, wybierz, aby wyświetlić te elementy tylko wtedy, gdy dane są ładowane:</span><span class="sxs-lookup"><span data-stu-id="2c459-363">Instead of unconditionally displaying the count and **`Increment`** button, choose to display these elements only if the data is loaded:</span></span>

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

### <a name="handle-prerendering"></a><span data-ttu-id="2c459-364">Obsługa przed renderowaniem</span><span class="sxs-lookup"><span data-stu-id="2c459-364">Handle prerendering</span></span>

<span data-ttu-id="2c459-365">Podczas renderowania:</span><span class="sxs-lookup"><span data-stu-id="2c459-365">During prerendering:</span></span>

* <span data-ttu-id="2c459-366">Połączenie interaktywne z przeglądarką użytkownika nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="2c459-366">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="2c459-367">Przeglądarka nie zawiera jeszcze strony, w której można uruchomić kod JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2c459-367">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="2c459-368">`localStorage` lub `sessionStorage` nie są dostępne podczas renderowania.</span><span class="sxs-lookup"><span data-stu-id="2c459-368">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="2c459-369">Jeśli składnik próbuje współdziałać z magazynem, zostanie wygenerowany błąd z wyjaśnieniem, że nie można wystawić wywołań międzyoperacyjnych języka JavaScript, ponieważ składnik jest wstępnie renderowany.</span><span class="sxs-lookup"><span data-stu-id="2c459-369">If the component attempts to interact with storage, an error is generated explaining that JavaScript interop calls cannot be issued because the component is being prerendered.</span></span>

<span data-ttu-id="2c459-370">Jednym ze sposobów na rozwiązanie błędu jest wyłączenie renderowania.</span><span class="sxs-lookup"><span data-stu-id="2c459-370">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="2c459-371">Jest to zazwyczaj najlepszym wyborem, jeśli aplikacja znacznie korzysta z magazynu opartego na przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="2c459-371">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="2c459-372">Renderowanie zwiększa złożoność i nie korzysta z aplikacji, ponieważ aplikacja nie może przeprowadzić renderowania żadnej przydatnej zawartości do momentu, gdy nie `localStorage` `sessionStorage` jest dostępna.</span><span class="sxs-lookup"><span data-stu-id="2c459-372">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="2c459-373">Aby wyłączyć renderowanie, Otwórz `Pages/_Host.cshtml` plik i Zmień `render-mode` atrybut [pomocnika znacznika składnika](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) , aby <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> :</span><span class="sxs-lookup"><span data-stu-id="2c459-373">To disable prerendering, open the `Pages/_Host.cshtml` file and change the `render-mode` attribute of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>:</span></span>

```cshtml
<component type="typeof(App)" render-mode="Server" />
```

<span data-ttu-id="2c459-374">Renderowanie może być przydatne w przypadku innych stron, które nie używają `localStorage` ani `sessionStorage` .</span><span class="sxs-lookup"><span data-stu-id="2c459-374">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="2c459-375">Aby zachować renderowanie, odłóż operację ładowania do momentu podłączenia przeglądarki do obwodu.</span><span class="sxs-lookup"><span data-stu-id="2c459-375">To retain prerendering, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="2c459-376">Poniżej przedstawiono przykład przechowywania wartości licznika:</span><span class="sxs-lookup"><span data-stu-id="2c459-376">The following is an example for storing a counter value:</span></span>

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

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="2c459-377">Wyrównać zachowywanie stanu do wspólnej lokalizacji</span><span class="sxs-lookup"><span data-stu-id="2c459-377">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="2c459-378">Jeśli wiele składników korzysta z magazynu opartego na przeglądarce, ponowne implementowanie kodu dostawcy stanu wiele razy powoduje utworzenie duplikacji kodu.</span><span class="sxs-lookup"><span data-stu-id="2c459-378">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="2c459-379">Jedną z opcji unikania duplikowania kodu jest utworzenie *składnika nadrzędnego dostawcy stanu* , który hermetyzuje logikę dostawcy stanu.</span><span class="sxs-lookup"><span data-stu-id="2c459-379">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="2c459-380">Składniki podrzędne mogą współpracować z trwałymi danymi bez względu na mechanizm trwałości stanu.</span><span class="sxs-lookup"><span data-stu-id="2c459-380">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="2c459-381">W poniższym przykładzie `CounterStateProvider` składnika dane licznika są utrwalane `sessionStorage` :</span><span class="sxs-lookup"><span data-stu-id="2c459-381">In the following example of a `CounterStateProvider` component, counter data is persisted to `sessionStorage`:</span></span>

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

<span data-ttu-id="2c459-382">`CounterStateProvider`Składnik obsługuje fazę ładowania, przez co nie renderuje jej zawartości podrzędnej do momentu ukończenia ładowania.</span><span class="sxs-lookup"><span data-stu-id="2c459-382">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="2c459-383">Aby użyć `CounterStateProvider` składnika, zawiń wystąpienie składnika wokół dowolnego innego składnika, który wymaga dostępu do stanu licznika.</span><span class="sxs-lookup"><span data-stu-id="2c459-383">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="2c459-384">Aby zapewnić dostępność stanu dla wszystkich składników w aplikacji, zawiń `CounterStateProvider` składnik wokół składnika <xref:Microsoft.AspNetCore.Components.Routing.Router> w `App` składniku ( `App.razor` ):</span><span class="sxs-lookup"><span data-stu-id="2c459-384">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the <xref:Microsoft.AspNetCore.Components.Routing.Router> in the `App` component (`App.razor`):</span></span>

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

<span data-ttu-id="2c459-385">Zapakowane składniki są odbierane i mogą modyfikować stan trwałych liczników.</span><span class="sxs-lookup"><span data-stu-id="2c459-385">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="2c459-386">Poniższy `Counter` składnik implementuje wzorzec:</span><span class="sxs-lookup"><span data-stu-id="2c459-386">The following `Counter` component implements the pattern:</span></span>

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

<span data-ttu-id="2c459-387">Poprzedni składnik nie jest wymagany do współpracy z programem `ProtectedBrowserStorage` ani nie zajmuje się fazą "Ładowanie".</span><span class="sxs-lookup"><span data-stu-id="2c459-387">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="2c459-388">Aby można było zaradzić sobie z instrukcją prerenderingu zgodnie z wcześniejszym opisem, `CounterStateProvider` może zostać zmieniona, aby wszystkie składniki korzystające z danych licznika automatycznie działały z użyciem prerenderowania.</span><span class="sxs-lookup"><span data-stu-id="2c459-388">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="2c459-389">Aby uzyskać więcej informacji, zobacz sekcję [Obsługa przed renderowaniem](#handle-prerendering) .</span><span class="sxs-lookup"><span data-stu-id="2c459-389">For more information, see the [Handle prerendering](#handle-prerendering) section.</span></span>

<span data-ttu-id="2c459-390">Ogólnie rzecz biorąc, zalecany jest wzorzec *składnika nadrzędnego dostawcy stanu* :</span><span class="sxs-lookup"><span data-stu-id="2c459-390">In general, *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="2c459-391">Aby korzystać z stanu w wielu składnikach.</span><span class="sxs-lookup"><span data-stu-id="2c459-391">To consume state across many components.</span></span>
* <span data-ttu-id="2c459-392">Jeśli istnieje tylko jeden obiekt stanu najwyższego poziomu, który ma być trwały.</span><span class="sxs-lookup"><span data-stu-id="2c459-392">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="2c459-393">Aby zachować wiele różnych obiektów stanu i korzystać z różnych podzbiorów obiektów w różnych miejscach, lepiej jest unikać utrwalania stanu w całości.</span><span class="sxs-lookup"><span data-stu-id="2c459-393">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid persisting state globally.</span></span>

::: moniker-end

<h2 id="in-memory-state-container-service-server"><span data-ttu-id="2c459-394">Usługa kontenera stanu w pamięci</span><span class="sxs-lookup"><span data-stu-id="2c459-394">In-memory state container service</span></span></h2>

[!INCLUDE[](~/includes/blazor-state-management/state-container.md)]

::: zone-end
