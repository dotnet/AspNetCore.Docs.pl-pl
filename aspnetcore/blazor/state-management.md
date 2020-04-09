---
title: ASP.NET Podstawowe Blazor zarządzanie stanem
author: guardrex
description: Dowiedz się, jak Blazor zachowywać stan w aplikacjach serwera.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- SignalR
uid: blazor/state-management
ms.openlocfilehash: e8a1959a8fc05ea59362bb5824181a9d2e418811
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80218872"
---
# <a name="aspnet-core-opno-locblazor-state-management"></a><span data-ttu-id="40f81-103">ASP.NET Podstawowe Blazor zarządzanie stanem</span><span class="sxs-lookup"><span data-stu-id="40f81-103">ASP.NET Core Blazor state management</span></span>

<span data-ttu-id="40f81-104">Przez [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="40f81-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor<span data-ttu-id="40f81-105">Serwer jest stanową strukturą aplikacji.</span><span class="sxs-lookup"><span data-stu-id="40f81-105"> Server is a stateful app framework.</span></span> <span data-ttu-id="40f81-106">W większości przypadków aplikacja utrzymuje stałe połączenie z serwerem.</span><span class="sxs-lookup"><span data-stu-id="40f81-106">Most of the time, the app maintains an ongoing connection to the server.</span></span> <span data-ttu-id="40f81-107">Stan użytkownika jest przechowywany w pamięci serwera w *obwodzie*.</span><span class="sxs-lookup"><span data-stu-id="40f81-107">The user's state is held in the server's memory in a *circuit*.</span></span> 

<span data-ttu-id="40f81-108">Przykłady stanu utrzymywanego dla obwodu użytkownika obejmują:</span><span class="sxs-lookup"><span data-stu-id="40f81-108">Examples of state held for a user's circuit include:</span></span>

* <span data-ttu-id="40f81-109">Renderowany interfejs&mdash;użytkownika hierarchii wystąpień składników i ich najnowsze dane wyjściowe renderowania.</span><span class="sxs-lookup"><span data-stu-id="40f81-109">The rendered UI&mdash;the hierarchy of component instances and their most recent render output.</span></span>
* <span data-ttu-id="40f81-110">Wartości wszystkich pól i właściwości w wystąpieniach komponentów.</span><span class="sxs-lookup"><span data-stu-id="40f81-110">The values of any fields and properties in component instances.</span></span>
* <span data-ttu-id="40f81-111">Dane przechowywane w instancja usługi [iniekcji zależności (DI),](xref:fundamentals/dependency-injection) które są ograniczone do obwodu.</span><span class="sxs-lookup"><span data-stu-id="40f81-111">Data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span>

> [!NOTE]
> <span data-ttu-id="40f81-112">Ten artykuł dotyczy trwałości stanu w Blazor aplikacjach serwera.</span><span class="sxs-lookup"><span data-stu-id="40f81-112">This article addresses state persistence in Blazor Server apps.</span></span> Blazor<span data-ttu-id="40f81-113">Aplikacje WebAssembly mogą korzystać z [trwałości stanu po stronie klienta w przeglądarce,](#client-side-in-the-browser) ale wymagają rozwiązań niestandardowych lub pakietów innych firm poza zakresem tego artykułu.</span><span class="sxs-lookup"><span data-stu-id="40f81-113"> WebAssembly apps can take advantage of [client-side state persistence in the browser](#client-side-in-the-browser) but require custom solutions or 3rd party packages beyond the scope of this article.</span></span>

## <a name="opno-locblazor-circuits"></a>Blazor<span data-ttu-id="40f81-114">Układy scalone</span><span class="sxs-lookup"><span data-stu-id="40f81-114"> circuits</span></span>

<span data-ttu-id="40f81-115">Jeśli użytkownik doświadcza tymczasowej utraty Blazor połączenia sieciowego, próbuje ponownie połączyć użytkownika z oryginalnym obwodem, aby mógł nadal korzystać z aplikacji.</span><span class="sxs-lookup"><span data-stu-id="40f81-115">If a user experiences a temporary network connection loss, Blazor attempts to reconnect the user to their original circuit so they can continue to use the app.</span></span> <span data-ttu-id="40f81-116">Jednak ponowne połączenie użytkownika z oryginalnym obwodem w pamięci serwera nie zawsze jest możliwe:</span><span class="sxs-lookup"><span data-stu-id="40f81-116">However, reconnecting a user to their original circuit in the server's memory isn't always possible:</span></span>

* <span data-ttu-id="40f81-117">Serwer nie może zachować odłączony obwód na zawsze.</span><span class="sxs-lookup"><span data-stu-id="40f81-117">The server can't retain a disconnected circuit forever.</span></span> <span data-ttu-id="40f81-118">Serwer musi zwolnić odłączony obwód po upływie limitu czasu lub gdy serwer znajduje się pod ciśnieniem pamięci.</span><span class="sxs-lookup"><span data-stu-id="40f81-118">The server must release a disconnected circuit after a timeout or when the server is under memory pressure.</span></span>
* <span data-ttu-id="40f81-119">W środowiskach wieloserwerowych, zrównoważonych obciążeniem, wszelkie żądania przetwarzania serwera mogą stać się niedostępne w danym momencie.</span><span class="sxs-lookup"><span data-stu-id="40f81-119">In multiserver, load-balanced deployment environments, any server processing requests may become unavailable at any given time.</span></span> <span data-ttu-id="40f81-120">Poszczególne serwery mogą zakończyć się niepowodzeniem lub zostać automatycznie usunięte, gdy nie są już wymagane do obsługi ogólnej liczby żądań.</span><span class="sxs-lookup"><span data-stu-id="40f81-120">Individual servers may fail or be automatically removed when no longer required to handle the overall volume of requests.</span></span> <span data-ttu-id="40f81-121">Oryginalny serwer może być niedostępny, gdy użytkownik próbuje ponownie nawiązać połączenie.</span><span class="sxs-lookup"><span data-stu-id="40f81-121">The original server may not be available when the user attempts to reconnect.</span></span>
* <span data-ttu-id="40f81-122">Użytkownik może zamknąć i ponownie otworzyć przeglądarkę lub ponownie załadować stronę, co usuwa każdy stan przechowywany w pamięci przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="40f81-122">The user might close and re-open their browser or reload the page, which removes any state held in the browser's memory.</span></span> <span data-ttu-id="40f81-123">Na przykład wartości ustawione za pośrednictwem wywołań interop JavaScript są tracone.</span><span class="sxs-lookup"><span data-stu-id="40f81-123">For example, values set through JavaScript interop calls are lost.</span></span>

<span data-ttu-id="40f81-124">Gdy nie można ponownie podłączyć użytkownika do oryginalnego obwodu, użytkownik otrzymuje nowy obwód z pustym stanem.</span><span class="sxs-lookup"><span data-stu-id="40f81-124">When a user can't be reconnected to their original circuit, the user receives a new circuit with an empty state.</span></span> <span data-ttu-id="40f81-125">Jest to równoważne zamknięciu i ponownym otwarciu aplikacji klasycznej.</span><span class="sxs-lookup"><span data-stu-id="40f81-125">This is equivalent to closing and re-opening a desktop app.</span></span>

## <a name="preserve-state-across-circuits"></a><span data-ttu-id="40f81-126">Zachowywanie stanu w obwodach</span><span class="sxs-lookup"><span data-stu-id="40f81-126">Preserve state across circuits</span></span>

<span data-ttu-id="40f81-127">W niektórych scenariuszach zachowanie stanu w obwodach jest pożądane.</span><span class="sxs-lookup"><span data-stu-id="40f81-127">In some scenarios, preserving state across circuits is desirable.</span></span> <span data-ttu-id="40f81-128">Aplikacja może zachować ważne dane dla użytkownika, jeśli:</span><span class="sxs-lookup"><span data-stu-id="40f81-128">An app can retain important data for a user if:</span></span>

* <span data-ttu-id="40f81-129">Serwer sieci web staje się niedostępny.</span><span class="sxs-lookup"><span data-stu-id="40f81-129">The web server becomes unavailable.</span></span>
* <span data-ttu-id="40f81-130">Przeglądarka użytkownika jest zmuszona do uruchomienia nowego obwodu z nowym serwerem www.</span><span class="sxs-lookup"><span data-stu-id="40f81-130">The user's browser is forced to start a new circuit with a new web server.</span></span>

<span data-ttu-id="40f81-131">Ogólnie rzecz biorąc utrzymanie stanu w obwodach ma zastosowanie do scenariuszy, w których użytkownicy aktywnie tworzą dane, a nie po prostu odczytują dane, które już istnieją.</span><span class="sxs-lookup"><span data-stu-id="40f81-131">In general, maintaining state across circuits applies to scenarios where users are actively creating data, not simply reading data that already exists.</span></span>

<span data-ttu-id="40f81-132">Aby zachować stan poza jednym obwodem, *nie przechowuj tylko danych w pamięci serwera.*</span><span class="sxs-lookup"><span data-stu-id="40f81-132">To preserve state beyond a single circuit, *don't merely store the data in the server's memory*.</span></span> <span data-ttu-id="40f81-133">Aplikacja musi utrwalić dane do innej lokalizacji magazynu.</span><span class="sxs-lookup"><span data-stu-id="40f81-133">The app must persist the data to some other storage location.</span></span> <span data-ttu-id="40f81-134">Trwałość stanu nie&mdash;jest automatyczne należy podjąć kroki podczas tworzenia aplikacji do implementowania trwałości danych stanowych.</span><span class="sxs-lookup"><span data-stu-id="40f81-134">State persistence isn't automatic&mdash;you must take steps when developing the app to implement stateful data persistence.</span></span>

<span data-ttu-id="40f81-135">Trwałość danych jest zazwyczaj wymagane tylko dla stanu o wysokiej wartości, że użytkownicy mają wydatkowane wysiłku, aby utworzyć.</span><span class="sxs-lookup"><span data-stu-id="40f81-135">Data persistence is typically only required for high-value state that users have expended effort to create.</span></span> <span data-ttu-id="40f81-136">W poniższych przykładach stan utrwalania oszczędza czas lub pomoce w działalności komercyjnej:</span><span class="sxs-lookup"><span data-stu-id="40f81-136">In the following examples, persisting state either saves time or aids in commercial activities:</span></span>

* <span data-ttu-id="40f81-137">Wieloetapowy &ndash; webform Jest to czasochłonne dla użytkownika, aby ponownie wprowadzić dane dla kilku zakończonych kroków wieloetapowego procesu, jeśli ich stan zostanie utracony.</span><span class="sxs-lookup"><span data-stu-id="40f81-137">Multistep webform &ndash; It's time-consuming for a user to re-enter data for several completed steps of a multistep process if their state is lost.</span></span> <span data-ttu-id="40f81-138">Użytkownik traci stan w tym scenariuszu, jeśli przejść od formularza wieloetapowego i powrócić do formularza później.</span><span class="sxs-lookup"><span data-stu-id="40f81-138">A user loses state in this scenario if they navigate away from the multistep form and return to the form later.</span></span>
* <span data-ttu-id="40f81-139">Koszyk &ndash; Każdy ważny z handlowego punktu widzenia składnik aplikacji, który reprezentuje potencjalne przychody, może zostać zachowany.</span><span class="sxs-lookup"><span data-stu-id="40f81-139">Shopping cart &ndash; Any commercially important component of an app that represents potential revenue can be maintained.</span></span> <span data-ttu-id="40f81-140">Użytkownik, który traci swój stan, a tym samym swój koszyk, może kupić mniej produktów lub usług, gdy wróci do witryny później.</span><span class="sxs-lookup"><span data-stu-id="40f81-140">A user who loses their state, and thus their shopping cart, may purchase fewer products or services when they return to the site later.</span></span>

<span data-ttu-id="40f81-141">Zwykle nie jest konieczne zachowanie łatwo odtworzonego stanu, takiego jak nazwa użytkownika wprowadzona w oknie dialogowym logowania, które nie zostało przesłane.</span><span class="sxs-lookup"><span data-stu-id="40f81-141">It's usually not necessary to preserve easily-recreated state, such as the username entered into a sign-in dialog that hasn't been submitted.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="40f81-142">Aplikacja może tylko zachowywać *stan aplikacji*.</span><span class="sxs-lookup"><span data-stu-id="40f81-142">An app can only persist *app state*.</span></span> <span data-ttu-id="40f81-143">Nie można utrwalić interfejsów użytkownika, takich jak wystąpienia składników i ich drzewa renderowania.</span><span class="sxs-lookup"><span data-stu-id="40f81-143">UIs can't be persisted, such as component instances and their render trees.</span></span> <span data-ttu-id="40f81-144">Składniki i drzewa renderowania nie są zazwyczaj serializable.</span><span class="sxs-lookup"><span data-stu-id="40f81-144">Components and render trees aren't generally serializable.</span></span> <span data-ttu-id="40f81-145">Aby utrwalić coś podobnego do stanu interfejsu użytkownika, takich jak rozwinięte węzły TreeView, aplikacja musi mieć niestandardowy kod do modelowania zachowania jako serializable stanu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="40f81-145">To persist something similar to UI state, such as the expanded nodes of a TreeView, the app must have custom code to model the behavior as serializable app state.</span></span>

## <a name="where-to-persist-state"></a><span data-ttu-id="40f81-146">Gdzie utrwalić stan</span><span class="sxs-lookup"><span data-stu-id="40f81-146">Where to persist state</span></span>

<span data-ttu-id="40f81-147">Istnieją trzy typowe lokalizacje dla Blazor stanu utrwalania w aplikacji serwera.</span><span class="sxs-lookup"><span data-stu-id="40f81-147">Three common locations exist for persisting state in a Blazor Server app.</span></span> <span data-ttu-id="40f81-148">Każde podejście najlepiej nadaje się do różnych scenariuszy i ma różne zastrzeżenia:</span><span class="sxs-lookup"><span data-stu-id="40f81-148">Each approach is best suited to different scenarios and has different caveats:</span></span>

* [<span data-ttu-id="40f81-149">Po stronie serwera w bazie danych</span><span class="sxs-lookup"><span data-stu-id="40f81-149">Server-side in a database</span></span>](#server-side-in-a-database)
* [<span data-ttu-id="40f81-150">Adres URL</span><span class="sxs-lookup"><span data-stu-id="40f81-150">URL</span></span>](#url)
* [<span data-ttu-id="40f81-151">Po stronie klienta w przeglądarce</span><span class="sxs-lookup"><span data-stu-id="40f81-151">Client-side in the browser</span></span>](#client-side-in-the-browser)

### <a name="server-side-in-a-database"></a><span data-ttu-id="40f81-152">Po stronie serwera w bazie danych</span><span class="sxs-lookup"><span data-stu-id="40f81-152">Server-side in a database</span></span>

<span data-ttu-id="40f81-153">W przypadku trwałej trwałości danych lub dla wszystkich danych, które muszą obejmować wielu użytkowników lub urządzenia, niezależna baza danych po stronie serwera jest prawie na pewno najlepszym wyborem.</span><span class="sxs-lookup"><span data-stu-id="40f81-153">For permanent data persistence or for any data that must span multiple users or devices, an independent server-side database is almost certainly the best choice.</span></span> <span data-ttu-id="40f81-154">Dostępne są następujące opcje:</span><span class="sxs-lookup"><span data-stu-id="40f81-154">Options include:</span></span>

* <span data-ttu-id="40f81-155">Relacyjna baza danych SQL</span><span class="sxs-lookup"><span data-stu-id="40f81-155">Relational SQL database</span></span>
* <span data-ttu-id="40f81-156">Magazyn wartości klucza</span><span class="sxs-lookup"><span data-stu-id="40f81-156">Key-value store</span></span>
* <span data-ttu-id="40f81-157">Sklep blob</span><span class="sxs-lookup"><span data-stu-id="40f81-157">Blob store</span></span>
* <span data-ttu-id="40f81-158">Sklep z tabelami</span><span class="sxs-lookup"><span data-stu-id="40f81-158">Table store</span></span>

<span data-ttu-id="40f81-159">Po zapisaniu danych w bazie danych, nowy obwód może być uruchomiony przez użytkownika w dowolnym momencie.</span><span class="sxs-lookup"><span data-stu-id="40f81-159">After data is saved in the database, a new circuit can be started by a user at any time.</span></span> <span data-ttu-id="40f81-160">Dane użytkownika są przechowywane i dostępne w każdym nowym obwodzie.</span><span class="sxs-lookup"><span data-stu-id="40f81-160">The user's data is retained and available in any new circuit.</span></span>

<span data-ttu-id="40f81-161">Aby uzyskać więcej informacji na temat opcji magazynu danych platformy Azure, zobacz [Dokumentację usługi Azure Storage](/azure/storage/) i [bazy danych platformy Azure](https://azure.microsoft.com/product-categories/databases/).</span><span class="sxs-lookup"><span data-stu-id="40f81-161">For more information on Azure data storage options, see the [Azure Storage Documentation](/azure/storage/) and [Azure Databases](https://azure.microsoft.com/product-categories/databases/).</span></span>

### <a name="url"></a><span data-ttu-id="40f81-162">Adres URL</span><span class="sxs-lookup"><span data-stu-id="40f81-162">URL</span></span>

<span data-ttu-id="40f81-163">W przypadku danych przejściowych reprezentujących stan nawigacji należy modelować dane jako część adresu URL.</span><span class="sxs-lookup"><span data-stu-id="40f81-163">For transient data representing navigation state, model the data as a part of the URL.</span></span> <span data-ttu-id="40f81-164">Przykłady stanu modelowanego w adresie URL obejmują:</span><span class="sxs-lookup"><span data-stu-id="40f81-164">Examples of state modeled in the URL include:</span></span>

* <span data-ttu-id="40f81-165">Identyfikator oglądanej encji.</span><span class="sxs-lookup"><span data-stu-id="40f81-165">The ID of a viewed entity.</span></span>
* <span data-ttu-id="40f81-166">Bieżący numer strony w siatce.</span><span class="sxs-lookup"><span data-stu-id="40f81-166">The current page number in a paged grid.</span></span>

<span data-ttu-id="40f81-167">Zawartość paska adresu przeglądarki jest zachowywana:</span><span class="sxs-lookup"><span data-stu-id="40f81-167">The contents of the browser's address bar are retained:</span></span>

* <span data-ttu-id="40f81-168">Jeśli użytkownik ręcznie ładuje stronę.</span><span class="sxs-lookup"><span data-stu-id="40f81-168">If the user manually reloads the page.</span></span>
* <span data-ttu-id="40f81-169">Jeśli serwer sieci&mdash;web stanie się niedostępny, użytkownik jest zmuszony do ponownego załadowania strony w celu nawiązania połączenia z innym serwerem.</span><span class="sxs-lookup"><span data-stu-id="40f81-169">If the web server becomes unavailable&mdash;the user is forced to reload the page in order to connect to a different server.</span></span>

<span data-ttu-id="40f81-170">Aby uzyskać informacje na temat `@page` definiowania <xref:blazor/routing>wzorców adresów URL za pomocą dyrektywy, zobacz .</span><span class="sxs-lookup"><span data-stu-id="40f81-170">For information on defining URL patterns with the `@page` directive, see <xref:blazor/routing>.</span></span>

### <a name="client-side-in-the-browser"></a><span data-ttu-id="40f81-171">Po stronie klienta w przeglądarce</span><span class="sxs-lookup"><span data-stu-id="40f81-171">Client-side in the browser</span></span>

<span data-ttu-id="40f81-172">W przypadku danych przejściowych, które użytkownik aktywnie tworzy, wspólnym zapasem kopii zapasowych jest przeglądarka `localStorage` i `sessionStorage` kolekcje.</span><span class="sxs-lookup"><span data-stu-id="40f81-172">For transient data that the user is actively creating, a common backing store is the browser's `localStorage` and `sessionStorage` collections.</span></span> <span data-ttu-id="40f81-173">Aplikacja nie jest wymagana do zarządzania lub czyścić stan przechowywany, jeśli obwód jest porzucony, co jest zaletą w stosunku do magazynu po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="40f81-173">The app isn't required to manage or clear the stored state if the circuit is abandoned, which is an advantage over server-side storage.</span></span>

> [!NOTE]
> <span data-ttu-id="40f81-174">"Po stronie klienta" w tej sekcji odnosi się do scenariuszy po stronie klienta w przeglądarce, a [ Blazor nie WebAssembly hosting modelu](xref:blazor/hosting-models#blazor-webassembly).</span><span class="sxs-lookup"><span data-stu-id="40f81-174">"Client-side" in this section refers to client-side scenarios in the browser, not the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly).</span></span> <span data-ttu-id="40f81-175">`localStorage`i `sessionStorage` może być Blazor używany w aplikacjach WebAssembly, ale tylko przez napisanie kodu niestandardowego lub przy użyciu pakietu innej firmy.</span><span class="sxs-lookup"><span data-stu-id="40f81-175">`localStorage` and `sessionStorage` can be used in Blazor WebAssembly apps but only by writing custom code or using a 3rd party package.</span></span>

<span data-ttu-id="40f81-176">`localStorage`i `sessionStorage` różnią się w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="40f81-176">`localStorage` and `sessionStorage` differ as follows:</span></span>

* <span data-ttu-id="40f81-177">`localStorage`jest objęty zakresem przeglądarki użytkownika.</span><span class="sxs-lookup"><span data-stu-id="40f81-177">`localStorage` is scoped to the user's browser.</span></span> <span data-ttu-id="40f81-178">Jeśli użytkownik ponownie ładuje stronę lub zamyka i ponownie otwiera przeglądarkę, stan będzie się powtarzał.</span><span class="sxs-lookup"><span data-stu-id="40f81-178">If the user reloads the page or closes and re-opens the browser, the state persists.</span></span> <span data-ttu-id="40f81-179">Jeśli użytkownik otworzy wiele kart przeglądarki, stan jest udostępniany na kartach.</span><span class="sxs-lookup"><span data-stu-id="40f81-179">If the user opens multiple browser tabs, the state is shared across the tabs.</span></span> <span data-ttu-id="40f81-180">Dane są `localStorage` zachowywane do momentu jawnego wyczyszczenie.</span><span class="sxs-lookup"><span data-stu-id="40f81-180">Data persists in `localStorage` until explicitly cleared.</span></span>
* <span data-ttu-id="40f81-181">`sessionStorage`jest objęty zakresem do karty przeglądarki użytkownika. Jeśli użytkownik ponownie ładuje kartę, stan będzie się powtarzał.</span><span class="sxs-lookup"><span data-stu-id="40f81-181">`sessionStorage` is scoped to the user's browser tab. If the user reloads the tab, the state persists.</span></span> <span data-ttu-id="40f81-182">Jeśli użytkownik zamknie kartę lub przeglądarkę, stan zostanie utracony.</span><span class="sxs-lookup"><span data-stu-id="40f81-182">If the user closes the tab or the browser, the state is lost.</span></span> <span data-ttu-id="40f81-183">Jeśli użytkownik otworzy wiele kart przeglądarki, każda karta ma własną niezależną wersję danych.</span><span class="sxs-lookup"><span data-stu-id="40f81-183">If the user opens multiple browser tabs, each tab has its own independent version of the data.</span></span>

<span data-ttu-id="40f81-184">Ogólnie rzecz `sessionStorage` biorąc, jest bezpieczniejsze w użyciu.</span><span class="sxs-lookup"><span data-stu-id="40f81-184">Generally, `sessionStorage` is safer to use.</span></span> <span data-ttu-id="40f81-185">`sessionStorage`pozwala uniknąć ryzyka, że użytkownik otworzy wiele kart i napotka następujące:</span><span class="sxs-lookup"><span data-stu-id="40f81-185">`sessionStorage` avoids the risk that a user opens multiple tabs and encounters the following:</span></span>

* <span data-ttu-id="40f81-186">Błędy w magazynie stanu na kartach.</span><span class="sxs-lookup"><span data-stu-id="40f81-186">Bugs in state storage across tabs.</span></span>
* <span data-ttu-id="40f81-187">Mylące zachowanie, gdy karta zastępuje stan innych kart.</span><span class="sxs-lookup"><span data-stu-id="40f81-187">Confusing behavior when a tab overwrites the state of other tabs.</span></span>

<span data-ttu-id="40f81-188">`localStorage`jest lepszym wyborem, jeśli aplikacja musi zachowywać stan przez zamknięcie i ponowne otwarcie przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="40f81-188">`localStorage` is the better choice if the app must persist state across closing and re-opening the browser.</span></span>

<span data-ttu-id="40f81-189">Zastrzeżenia dotyczące korzystania z pamięci masowej przeglądarki:</span><span class="sxs-lookup"><span data-stu-id="40f81-189">Caveats for using browser storage:</span></span>

* <span data-ttu-id="40f81-190">Podobnie jak w przypadku korzystania z bazy danych po stronie serwera, ładowanie i zapisywanie danych są asynchroniczne.</span><span class="sxs-lookup"><span data-stu-id="40f81-190">Similar to the use of a server-side database, loading and saving data are asynchronous.</span></span>
* <span data-ttu-id="40f81-191">W przeciwieństwie do bazy danych po stronie serwera magazyn nie jest dostępny podczas prerendering, ponieważ żądana strona nie istnieje w przeglądarce podczas etapu prerendering.</span><span class="sxs-lookup"><span data-stu-id="40f81-191">Unlike a server-side database, storage isn't available during prerendering because the requested page doesn't exist in the browser during the prerendering stage.</span></span>
* <span data-ttu-id="40f81-192">Przechowywanie kilku kilobajtów danych jest Blazor uzasadnione, aby utrzymać się dla aplikacji serwera.</span><span class="sxs-lookup"><span data-stu-id="40f81-192">Storage of a few kilobytes of data is reasonable to persist for Blazor Server apps.</span></span> <span data-ttu-id="40f81-193">Poza kilka kilobajtów należy wziąć pod uwagę wpływ na wydajność, ponieważ dane są ładowane i zapisywane w sieci.</span><span class="sxs-lookup"><span data-stu-id="40f81-193">Beyond a few kilobytes, you must consider the performance implications because the data is loaded and saved across the network.</span></span>
* <span data-ttu-id="40f81-194">Użytkownicy mogą przeglądać lub manipulować danymi.</span><span class="sxs-lookup"><span data-stu-id="40f81-194">Users may view or tamper with the data.</span></span> <span data-ttu-id="40f81-195">ASP.NET Podstawowa [ochrona danych](xref:security/data-protection/introduction) może zmniejszyć ryzyko.</span><span class="sxs-lookup"><span data-stu-id="40f81-195">ASP.NET Core [Data Protection](xref:security/data-protection/introduction) can mitigate the risk.</span></span>

## <a name="third-party-browser-storage-solutions"></a><span data-ttu-id="40f81-196">Rozwiązania do przechowywania przeglądarek innych firm</span><span class="sxs-lookup"><span data-stu-id="40f81-196">Third-party browser storage solutions</span></span>

<span data-ttu-id="40f81-197">Pakiety NuGet innych firm zapewniają interfejsy API do pracy z `localStorage` i `sessionStorage`.</span><span class="sxs-lookup"><span data-stu-id="40f81-197">Third-party NuGet packages provide APIs for working with `localStorage` and `sessionStorage`.</span></span>

<span data-ttu-id="40f81-198">Warto rozważyć wybór pakietu, który w przejrzysty sposób wykorzystuje ASP.NET [Ochronie Danych](xref:security/data-protection/introduction)Core.</span><span class="sxs-lookup"><span data-stu-id="40f81-198">It's worth considering choosing a package that transparently uses ASP.NET Core's [Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="40f81-199">ASP.NET Core Data Protection szyfruje przechowywane dane i zmniejsza potencjalne ryzyko manipulowania przechowywanymi danymi.</span><span class="sxs-lookup"><span data-stu-id="40f81-199">ASP.NET Core Data Protection encrypts stored data and reduces the potential risk of tampering with stored data.</span></span> <span data-ttu-id="40f81-200">Jeśli dane serializowane json są przechowywane w postaci zwykłego tekstu, użytkownicy mogą zobaczyć dane za pomocą narzędzi programistycznych przeglądarki, a także zmodyfikować przechowywane dane.</span><span class="sxs-lookup"><span data-stu-id="40f81-200">If JSON-serialized data is stored in plaintext, users can see the data using browser developer tools and also modify the stored data.</span></span> <span data-ttu-id="40f81-201">Zabezpieczanie danych nie zawsze stanowi problem, ponieważ dane mogą być trywialne w przyrodzie.</span><span class="sxs-lookup"><span data-stu-id="40f81-201">Securing data isn't always a problem because the data might be trivial in nature.</span></span> <span data-ttu-id="40f81-202">Na przykład odczyt lub modyfikacja przechowywanego koloru elementu interfejsu użytkownika nie stanowi istotnego zagrożenia bezpieczeństwa dla użytkownika lub organizacji.</span><span class="sxs-lookup"><span data-stu-id="40f81-202">For example, reading or modifying the stored color of a UI element isn't a significant security risk to the user or the organization.</span></span> <span data-ttu-id="40f81-203">Unikaj zezwalania użytkownikom na inspekcje lub manipulowanie *poufnymi danymi.*</span><span class="sxs-lookup"><span data-stu-id="40f81-203">Avoid allowing users to inspect or tamper with *sensitive data*.</span></span>

## <a name="protected-browser-storage-experimental-package"></a><span data-ttu-id="40f81-204">Pakiet eksperymentalny chronionego przechowywania przeglądarki</span><span class="sxs-lookup"><span data-stu-id="40f81-204">Protected Browser Storage experimental package</span></span>

<span data-ttu-id="40f81-205">Przykład pakietu NuGet, który zapewnia `localStorage` ochronę `sessionStorage` [danych](xref:security/data-protection/introduction) dla i jest [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span><span class="sxs-lookup"><span data-stu-id="40f81-205">An example of a NuGet package that provides [Data Protection](xref:security/data-protection/introduction) for `localStorage` and `sessionStorage` is [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>

> [!WARNING]
> <span data-ttu-id="40f81-206">`Microsoft.AspNetCore.ProtectedBrowserStorage`jest nieobsługiwanym pakietem eksperymentalnym nienadaje się do wykorzystania w tej chwili.</span><span class="sxs-lookup"><span data-stu-id="40f81-206">`Microsoft.AspNetCore.ProtectedBrowserStorage` is an unsupported experimental package unsuitable for production use at this time.</span></span>

### <a name="installation"></a><span data-ttu-id="40f81-207">Instalacja</span><span class="sxs-lookup"><span data-stu-id="40f81-207">Installation</span></span>

<span data-ttu-id="40f81-208">Aby zainstalować `Microsoft.AspNetCore.ProtectedBrowserStorage` pakiet:</span><span class="sxs-lookup"><span data-stu-id="40f81-208">To install the `Microsoft.AspNetCore.ProtectedBrowserStorage` package:</span></span>

1. <span data-ttu-id="40f81-209">W Blazor projekcie aplikacji Serwer dodaj odwołanie do pakietu do [pliku Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span><span class="sxs-lookup"><span data-stu-id="40f81-209">In the Blazor Server app project, add a package reference to [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage).</span></span>
1. <span data-ttu-id="40f81-210">W formacie HTML najwyższego poziomu (na przykład w pliku *Pages/_Host.cshtml* w domyślnym szablonie projektu) dodaj następujący `<script>` tag:</span><span class="sxs-lookup"><span data-stu-id="40f81-210">In the top-level HTML (for example, in the *Pages/_Host.cshtml* file in the default project template), add the following `<script>` tag:</span></span>

   ```html
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. <span data-ttu-id="40f81-211">W `Startup.ConfigureServices` metodzie `AddProtectedBrowserStorage` wywołać, aby dodać `localStorage` i `sessionStorage` usługi do kolekcji usługi:</span><span class="sxs-lookup"><span data-stu-id="40f81-211">In the `Startup.ConfigureServices` method, call `AddProtectedBrowserStorage` to add `localStorage` and `sessionStorage` services to the service collection:</span></span>

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a><span data-ttu-id="40f81-212">Zapisywanie i ładowanie danych w ramach składnika</span><span class="sxs-lookup"><span data-stu-id="40f81-212">Save and load data within a component</span></span>

<span data-ttu-id="40f81-213">W każdym składniku, który wymaga ładowania [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) lub zapisywania danych do magazynu przeglądarki, użyj, aby wstrzyknąć wystąpienie jednego z następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="40f81-213">In any component that requires loading or saving data to browser storage, use [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) to inject an instance of either of the following:</span></span>

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

<span data-ttu-id="40f81-214">Wybór zależy od tego, którego zapasu chcesz użyć.</span><span class="sxs-lookup"><span data-stu-id="40f81-214">The choice depends on which backing store you wish to use.</span></span> <span data-ttu-id="40f81-215">W poniższym `sessionStorage` przykładzie jest używany:</span><span class="sxs-lookup"><span data-stu-id="40f81-215">In the following example, `sessionStorage` is used:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

<span data-ttu-id="40f81-216">Instrukcję `@using` można umieścić w pliku *_Imports.brzytwa,* a nie w komponencie.</span><span class="sxs-lookup"><span data-stu-id="40f81-216">The `@using` statement can be placed into an *_Imports.razor* file instead of in the component.</span></span> <span data-ttu-id="40f81-217">Korzystanie z pliku *_Imports.razor* sprawia, że obszar nazw jest dostępny dla większych segmentów aplikacji lub całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="40f81-217">Use of the *_Imports.razor* file makes the namespace available to larger segments of the app or the whole app.</span></span>

<span data-ttu-id="40f81-218">Aby `_currentCount` utrwalić `Counter` wartość składnika szablonu `IncrementCount` projektu, `ProtectedSessionStore.SetAsync`zmodyfikuj metodę użycia:</span><span class="sxs-lookup"><span data-stu-id="40f81-218">To persist the `_currentCount` value in the `Counter` component of the project template, modify the `IncrementCount` method to use `ProtectedSessionStore.SetAsync`:</span></span>

```csharp
private async Task IncrementCount()
{
    _currentCount++;
    await ProtectedSessionStore.SetAsync("count", _currentCount);
}
```

<span data-ttu-id="40f81-219">W większych, bardziej realistycznych aplikacjach przechowywanie poszczególnych pól jest mało prawdopodobnym scenariuszem.</span><span class="sxs-lookup"><span data-stu-id="40f81-219">In larger, more realistic apps, storage of individual fields is an unlikely scenario.</span></span> <span data-ttu-id="40f81-220">Aplikacje są bardziej prawdopodobne, aby przechowywać całe obiekty modelu, które zawierają stan złożony.</span><span class="sxs-lookup"><span data-stu-id="40f81-220">Apps are more likely to store entire model objects that include complex state.</span></span> <span data-ttu-id="40f81-221">`ProtectedSessionStore`automatycznie serializuje i deserializuje dane JSON.</span><span class="sxs-lookup"><span data-stu-id="40f81-221">`ProtectedSessionStore` automatically serializes and deserializes JSON data.</span></span>

<span data-ttu-id="40f81-222">W poprzednim przykładzie kodu `_currentCount` dane są `sessionStorage['count']` przechowywane tak jak w przeglądarce użytkownika.</span><span class="sxs-lookup"><span data-stu-id="40f81-222">In the preceding code example, the `_currentCount` data is stored as `sessionStorage['count']` in the user's browser.</span></span> <span data-ttu-id="40f81-223">Dane nie są przechowywane w postaci zwykłego tekstu, ale są chronione za pomocą ASP.NET [Ochrony Danych Core.](xref:security/data-protection/introduction)</span><span class="sxs-lookup"><span data-stu-id="40f81-223">The data isn't stored in plaintext but rather is protected using ASP.NET Core's [Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="40f81-224">Zaszyfrowane dane można zobaczyć, jeśli `sessionStorage['count']` są oceniane w konsoli dewelopera przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="40f81-224">The encrypted data can be seen if `sessionStorage['count']` is evaluated in the browser's developer console.</span></span>

<span data-ttu-id="40f81-225">Aby odzyskać `_currentCount` dane, jeśli użytkownik `Counter` powróci do składnika później (w tym jeśli `ProtectedSessionStore.GetAsync`jest w zupełnie nowym obwodzie), należy użyć:</span><span class="sxs-lookup"><span data-stu-id="40f81-225">To recover the `_currentCount` data if the user returns to the `Counter` component later (including if they're on an entirely new circuit), use `ProtectedSessionStore.GetAsync`:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
    _currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

<span data-ttu-id="40f81-226">Jeśli parametry składnika obejmują stan `ProtectedSessionStore.GetAsync` nawigacji, wywołaj `OnParametersSetAsync`i `OnInitializedAsync`przypisz wynik w , nie .</span><span class="sxs-lookup"><span data-stu-id="40f81-226">If the component's parameters include navigation state, call `ProtectedSessionStore.GetAsync` and assign the result in `OnParametersSetAsync`, not `OnInitializedAsync`.</span></span> <span data-ttu-id="40f81-227">`OnInitializedAsync`jest wywoływana tylko jeden raz, gdy składnik jest po raz pierwszy smówiło się.</span><span class="sxs-lookup"><span data-stu-id="40f81-227">`OnInitializedAsync` is only called one time when the component is first instantiated.</span></span> <span data-ttu-id="40f81-228">`OnInitializedAsync`nie zostanie wywołana później, jeśli użytkownik przejdzie do innego adresu URL, pozostając na tej samej stronie.</span><span class="sxs-lookup"><span data-stu-id="40f81-228">`OnInitializedAsync` isn't called again later if the user navigates to a different URL while remaining on the same page.</span></span> <span data-ttu-id="40f81-229">Aby uzyskać więcej informacji, zobacz <xref:blazor/lifecycle>.</span><span class="sxs-lookup"><span data-stu-id="40f81-229">For more information, see <xref:blazor/lifecycle>.</span></span>

> [!WARNING]
> <span data-ttu-id="40f81-230">Przykłady w tej sekcji działają tylko wtedy, gdy serwer nie ma włączonego świszczącego dostępu.</span><span class="sxs-lookup"><span data-stu-id="40f81-230">The examples in this section only work if the server doesn't have prerendering enabled.</span></span> <span data-ttu-id="40f81-231">Po włączeniu prerenderingu generowany jest błąd podobny do:</span><span class="sxs-lookup"><span data-stu-id="40f81-231">With prerendering enabled, an error is generated similar to:</span></span>
>
> > <span data-ttu-id="40f81-232">W tej chwili nie można wykonywać połączeń międzyoperacyjnych JavaScript.</span><span class="sxs-lookup"><span data-stu-id="40f81-232">JavaScript interop calls cannot be issued at this time.</span></span> <span data-ttu-id="40f81-233">Dzieje się tak, ponieważ składnik jest prerendered.</span><span class="sxs-lookup"><span data-stu-id="40f81-233">This is because the component is being prerendered.</span></span>
>
> <span data-ttu-id="40f81-234">Wyłącz prerendering lub dodaj dodatkowy kod do pracy z prerendering.</span><span class="sxs-lookup"><span data-stu-id="40f81-234">Either disable prerendering or add additional code to work with prerendering.</span></span> <span data-ttu-id="40f81-235">Aby dowiedzieć się więcej na temat pisania kodu, który działa z prerendering, zobacz [Handle prerendering](#handle-prerendering) sekcji.</span><span class="sxs-lookup"><span data-stu-id="40f81-235">To learn more about writing code that works with prerendering, see the [Handle prerendering](#handle-prerendering) section.</span></span>

### <a name="handle-the-loading-state"></a><span data-ttu-id="40f81-236">Obsługa stanu załadunku</span><span class="sxs-lookup"><span data-stu-id="40f81-236">Handle the loading state</span></span>

<span data-ttu-id="40f81-237">Ponieważ magazyn przeglądarki jest asynchroniczne (dostęp za pośrednictwem połączenia sieciowego), zawsze jest okres czasu, zanim dane są ładowane i dostępne do użycia przez składnik.</span><span class="sxs-lookup"><span data-stu-id="40f81-237">Since browser storage is asynchronous (accessed over a network connection), there's always a period of time before the data is loaded and available for use by a component.</span></span> <span data-ttu-id="40f81-238">Aby uzyskać najlepsze wyniki, renderuj komunikat o stanie ładowania podczas ładowania, zamiast wyświetlania pustych lub domyślnych danych.</span><span class="sxs-lookup"><span data-stu-id="40f81-238">For the best results, render a loading-state message while loading is in progress instead of displaying blank or default data.</span></span>

<span data-ttu-id="40f81-239">Jednym z podejść jest `null` śledzenie, czy dane są (nadal ładowane), czy nie.</span><span class="sxs-lookup"><span data-stu-id="40f81-239">One approach is to track whether the data is `null` (still loading) or not.</span></span> <span data-ttu-id="40f81-240">W komponencie domyślnym `Counter` liczba `int`jest utrzymywana w pliku .</span><span class="sxs-lookup"><span data-stu-id="40f81-240">In the default `Counter` component, the count is held in an `int`.</span></span> <span data-ttu-id="40f81-241">Aby `_currentCount` można było anulować,`?`dodając znak`int`zapytania ( ) do typu ( ):</span><span class="sxs-lookup"><span data-stu-id="40f81-241">Make `_currentCount` nullable by adding a question mark (`?`) to the type (`int`):</span></span>

```csharp
private int? _currentCount;
```

<span data-ttu-id="40f81-242">Zamiast bezwarunkowo wyświetlać przycisk Count i **Increment,** wybierz opcję wyświetlania tych elementów tylko wtedy, gdy dane są ładowane:</span><span class="sxs-lookup"><span data-stu-id="40f81-242">Instead of unconditionally displaying the count and **Increment** button, choose to display these elements only if the data is loaded:</span></span>

```razor
@if (_currentCount.HasValue)
{
    <p>Current count: <strong>@_currentCount</strong></p>

    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a><span data-ttu-id="40f81-243">Przemówienie uchwytu</span><span class="sxs-lookup"><span data-stu-id="40f81-243">Handle prerendering</span></span>

<span data-ttu-id="40f81-244">Podczas prerendering:</span><span class="sxs-lookup"><span data-stu-id="40f81-244">During prerendering:</span></span>

* <span data-ttu-id="40f81-245">Interaktywne połączenie z przeglądarką użytkownika nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="40f81-245">An interactive connection to the user's browser doesn't exist.</span></span>
* <span data-ttu-id="40f81-246">Przeglądarka nie ma jeszcze strony, na której można uruchomić kod JavaScript.</span><span class="sxs-lookup"><span data-stu-id="40f81-246">The browser doesn't yet have a page in which it can run JavaScript code.</span></span>

<span data-ttu-id="40f81-247">`localStorage`lub `sessionStorage` nie są dostępne podczas prerendering.</span><span class="sxs-lookup"><span data-stu-id="40f81-247">`localStorage` or `sessionStorage` aren't available during prerendering.</span></span> <span data-ttu-id="40f81-248">Jeśli składnik próbuje wchodzić w interakcję z magazynem, generowany jest błąd podobny do:</span><span class="sxs-lookup"><span data-stu-id="40f81-248">If the component attempts to interact with storage, an error is generated similar to:</span></span>

> <span data-ttu-id="40f81-249">W tej chwili nie można wykonywać połączeń międzyoperacyjnych JavaScript.</span><span class="sxs-lookup"><span data-stu-id="40f81-249">JavaScript interop calls cannot be issued at this time.</span></span> <span data-ttu-id="40f81-250">Dzieje się tak, ponieważ składnik jest prerendered.</span><span class="sxs-lookup"><span data-stu-id="40f81-250">This is because the component is being prerendered.</span></span>

<span data-ttu-id="40f81-251">Jednym ze sposobów rozwiązania problemu jest wyłączenie prerenderingu.</span><span class="sxs-lookup"><span data-stu-id="40f81-251">One way to resolve the error is to disable prerendering.</span></span> <span data-ttu-id="40f81-252">Jest to zwykle najlepszy wybór, jeśli aplikacja intensywnie korzysta z pamięci masowej opartej na przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="40f81-252">This is usually the best choice if the app makes heavy use of browser-based storage.</span></span> <span data-ttu-id="40f81-253">Prerendering zwiększa złożoność i nie przynosi korzyści aplikacji, ponieważ aplikacja nie `localStorage` może `sessionStorage` prerenderować żadnej przydatnej zawartości, dopóki nie będzie dostępna.</span><span class="sxs-lookup"><span data-stu-id="40f81-253">Prerendering adds complexity and doesn't benefit the app because the app can't prerender any useful content until `localStorage` or `sessionStorage` are available.</span></span>

<span data-ttu-id="40f81-254">Aby wyłączyć program prerendering, otwórz plik *Pages/_Host.cshtml* i zmień `render-mode` pomocnika <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> [znacznika składnika](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) na .</span><span class="sxs-lookup"><span data-stu-id="40f81-254">To disable prerendering, open the *Pages/_Host.cshtml* file and change the `render-mode` of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>.</span></span>

<span data-ttu-id="40f81-255">Prerendering może być przydatny w przypadku `localStorage` innych `sessionStorage`stron, które nie są używane lub .</span><span class="sxs-lookup"><span data-stu-id="40f81-255">Prerendering might be useful for other pages that don't use `localStorage` or `sessionStorage`.</span></span> <span data-ttu-id="40f81-256">Aby zachować funkcję prerendering włączone, odroczyć operację ładowania, aż przeglądarka jest podłączony do obwodu.</span><span class="sxs-lookup"><span data-stu-id="40f81-256">To keep prerendering enabled, defer the loading operation until the browser is connected to the circuit.</span></span> <span data-ttu-id="40f81-257">Oto przykład przechowywania wartości licznika:</span><span class="sxs-lookup"><span data-stu-id="40f81-257">The following is an example for storing a counter value:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

... rendering code goes here ...

@code {
    private int? _currentCount;
    private bool _isConnected = false;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            // When execution reaches this point, the first *interactive* render
            // is complete. The component has an active connection to the browser.
            _isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        _currentCount = await ProtectedLocalStore.GetAsync<int>("prerenderedCount");
    }

    private async Task IncrementCount()
    {
        _currentCount++;
        await ProtectedSessionStore.SetAsync("count", _currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a><span data-ttu-id="40f81-258">Uwzględnij zachowanie stanu we wspólnym miejscu</span><span class="sxs-lookup"><span data-stu-id="40f81-258">Factor out the state preservation to a common location</span></span>

<span data-ttu-id="40f81-259">Jeśli wiele składników polega na magazyn oparty na przeglądarce, ponowne implementowanie kodu dostawcy stanu wiele razy tworzy duplikację kodu.</span><span class="sxs-lookup"><span data-stu-id="40f81-259">If many components rely on browser-based storage, re-implementing state provider code many times creates code duplication.</span></span> <span data-ttu-id="40f81-260">Jedną z opcji uniknięcia powielania kodu jest utworzenie *składnika nadrzędnego dostawcy stanu,* który hermetyzuje logikę dostawcy stanu.</span><span class="sxs-lookup"><span data-stu-id="40f81-260">One option for avoiding code duplication is to create a *state provider parent component* that encapsulates the state provider logic.</span></span> <span data-ttu-id="40f81-261">Składniki podrzędne mogą pracować z utrwalonych danych bez względu na mechanizm trwałości stanu.</span><span class="sxs-lookup"><span data-stu-id="40f81-261">Child components can work with persisted data without regard to the state persistence mechanism.</span></span>

<span data-ttu-id="40f81-262">W poniższym przykładzie składnika `CounterStateProvider` dane licznika są zachowywane:</span><span class="sxs-lookup"><span data-stu-id="40f81-262">In the following example of a `CounterStateProvider` component, counter data is persisted:</span></span>

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (_hasLoaded)
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
    private bool _hasLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        _hasLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

<span data-ttu-id="40f81-263">Składnik `CounterStateProvider` obsługuje fazy ładowania, nie renderowania jego zawartości podrzędnej, dopóki ładowanie jest zakończona.</span><span class="sxs-lookup"><span data-stu-id="40f81-263">The `CounterStateProvider` component handles the loading phase by not rendering its child content until loading is complete.</span></span>

<span data-ttu-id="40f81-264">Aby użyć `CounterStateProvider` składnika, zawiń wystąpienie składnika wokół dowolnego innego składnika, który wymaga dostępu do stanu licznika.</span><span class="sxs-lookup"><span data-stu-id="40f81-264">To use the `CounterStateProvider` component, wrap an instance of the component around any other component that requires access to the counter state.</span></span> <span data-ttu-id="40f81-265">Aby udostępnić stan wszystkim składnikom w `CounterStateProvider` aplikacji, `Router` zawiń `App` składnik wokół składnika (*App.brzytwa):*</span><span class="sxs-lookup"><span data-stu-id="40f81-265">To make the state accessible to all components in an app, wrap the `CounterStateProvider` component around the `Router` in the `App` component (*App.razor*):</span></span>

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

<span data-ttu-id="40f81-266">Opakowane składniki odbierać i można zmodyfikować stan licznika utrwalone.</span><span class="sxs-lookup"><span data-stu-id="40f81-266">Wrapped components receive and can modify the persisted counter state.</span></span> <span data-ttu-id="40f81-267">Następujący `Counter` składnik implementuje wzorzec:</span><span class="sxs-lookup"><span data-stu-id="40f81-267">The following `Counter` component implements the pattern:</span></span>

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

<span data-ttu-id="40f81-268">Poprzedni składnik nie jest wymagany do `ProtectedBrowserStorage`interakcji z , ani nie zajmuje się fazą "ładowania".</span><span class="sxs-lookup"><span data-stu-id="40f81-268">The preceding component isn't required to interact with `ProtectedBrowserStorage`, nor does it deal with a "loading" phase.</span></span>

<span data-ttu-id="40f81-269">Aby poradzić sobie z prerendering `CounterStateProvider` jak opisano wcześniej, można zmienić tak, aby wszystkie składniki, które zużywają dane licznika automatycznie pracować z prerendering.</span><span class="sxs-lookup"><span data-stu-id="40f81-269">To deal with prerendering as described earlier, `CounterStateProvider` can be amended so that all of the components that consume the counter data automatically work with prerendering.</span></span> <span data-ttu-id="40f81-270">Zobacz [Handle prerendering](#handle-prerendering) sekcji, aby uzyskać szczegółowe informacje.</span><span class="sxs-lookup"><span data-stu-id="40f81-270">See the [Handle prerendering](#handle-prerendering) section for details.</span></span>

<span data-ttu-id="40f81-271">Ogólnie rzecz biorąc zaleca się wzorzec *składnika nadrzędnego dostawcy stanu:*</span><span class="sxs-lookup"><span data-stu-id="40f81-271">In general, *state provider parent component* pattern is recommended:</span></span>

* <span data-ttu-id="40f81-272">Aby zużywać stan w wielu innych składnikach.</span><span class="sxs-lookup"><span data-stu-id="40f81-272">To consume state in many other components.</span></span>
* <span data-ttu-id="40f81-273">Jeśli istnieje tylko jeden obiekt stanu najwyższego poziomu do utrwalenia.</span><span class="sxs-lookup"><span data-stu-id="40f81-273">If there's just one top-level state object to persist.</span></span>

<span data-ttu-id="40f81-274">Aby utrwalić wiele różnych obiektów stanu i zużywają różne podzbiory obiektów w różnych miejscach, lepiej jest unikać obsługi ładowania i zapisywania stanu na całym świecie.</span><span class="sxs-lookup"><span data-stu-id="40f81-274">To persist many different state objects and consume different subsets of objects in different places, it's better to avoid handling the loading and saving of state globally.</span></span>
