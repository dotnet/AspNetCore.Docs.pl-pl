---
title: Konfiguracja modelu Blazor hostingu ASP.NET Core
author: guardrex
description: Dowiedz Blazor się więcej o konfiguracji modelu hostingu, w tym o tym, jak zintegrować komponenty Razor ze stronami Razor i aplikacjami MVC.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/24/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: 1f71ac63bbe9dc9d56cfca2ded19a5b863be828f
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80306433"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="d4aa0-103">konfiguracja modelu hostingowego ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="d4aa0-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="d4aa0-104">Przez [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="d4aa0-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="d4aa0-105">W tym artykule omówiono konfigurację modelu hostingu.</span><span class="sxs-lookup"><span data-stu-id="d4aa0-105">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a><span data-ttu-id="d4aa0-106">Zestaw WebAssembly Blazor</span><span class="sxs-lookup"><span data-stu-id="d4aa0-106">Blazor WebAssembly</span></span>

<span data-ttu-id="d4aa0-107">W wersji ASP.NET Core 3.2 Preview 3, Blazor WebAssembly obsługuje konfigurację z:</span><span class="sxs-lookup"><span data-stu-id="d4aa0-107">As of the ASP.NET Core 3.2 Preview 3 release, Blazor WebAssembly supports configuration from:</span></span>

* <span data-ttu-id="d4aa0-108">*wwwroot/appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="d4aa0-108">*wwwroot/appsettings.json*</span></span>
* <span data-ttu-id="d4aa0-109">*wwwroot/appsettings. {ŚRODOWISKO}.json*</span><span class="sxs-lookup"><span data-stu-id="d4aa0-109">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>

<span data-ttu-id="d4aa0-110">W aplikacji Blazor Hosted [środowisko wykonawcze](xref:fundamentals/environments) jest takie samo jak wartość aplikacji serwera.</span><span class="sxs-lookup"><span data-stu-id="d4aa0-110">In a Blazor Hosted app, the [runtime environment](xref:fundamentals/environments) is the same as the server app's value.</span></span>

<span data-ttu-id="d4aa0-111">Podczas uruchamiania aplikacji lokalnie środowiska domyślnie rozwoju.</span><span class="sxs-lookup"><span data-stu-id="d4aa0-111">When running the app locally, the environment defaults to Development.</span></span> <span data-ttu-id="d4aa0-112">Po opublikowaniu aplikacji środowisko domyślnie ma wartość Produkcja.</span><span class="sxs-lookup"><span data-stu-id="d4aa0-112">When the app is published, the environment defaults to Production.</span></span> <span data-ttu-id="d4aa0-113">Aby uzyskać więcej informacji, w tym <xref:fundamentals/environments>jak skonfigurować środowisko, zobacz .</span><span class="sxs-lookup"><span data-stu-id="d4aa0-113">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

> [!WARNING]
> <span data-ttu-id="d4aa0-114">Konfiguracja w aplikacji Blazor WebAssembly jest widoczna dla użytkowników.</span><span class="sxs-lookup"><span data-stu-id="d4aa0-114">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="d4aa0-115">**Nie przechowuj wpisów tajnych aplikacji ani poświadczeń w konfiguracji.**</span><span class="sxs-lookup"><span data-stu-id="d4aa0-115">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="d4aa0-116">Pliki konfiguracyjne są buforowane do użytku w trybie offline.</span><span class="sxs-lookup"><span data-stu-id="d4aa0-116">Configuration files are cached for offline use.</span></span> <span data-ttu-id="d4aa0-117">Dzięki [progresywnym aplikacjom sieci Web (PWA)](xref:blazor/progressive-web-app)można aktualizować tylko pliki konfiguracyjne podczas tworzenia nowego wdrożenia.</span><span class="sxs-lookup"><span data-stu-id="d4aa0-117">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="d4aa0-118">Edytowanie plików konfiguracyjnych między wdrożeniami nie ma wpływu, ponieważ:</span><span class="sxs-lookup"><span data-stu-id="d4aa0-118">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="d4aa0-119">Użytkownicy mają buforowane wersje plików, które nadal używają.</span><span class="sxs-lookup"><span data-stu-id="d4aa0-119">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="d4aa0-120">Pliki *service-worker.js* i *service-worker-assets.js* muszą zostać przebudowane na kompilacji, która sygnalizuje aplikacji przy następnej wizycie online użytkownika, że aplikacja została ponownie rozmieszczona.</span><span class="sxs-lookup"><span data-stu-id="d4aa0-120">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="d4aa0-121">Aby uzyskać więcej informacji na temat sposobu obsługi <xref:blazor/progressive-web-app#background-updates>aktualizacji w tle przez programy PWA, zobacz .</span><span class="sxs-lookup"><span data-stu-id="d4aa0-121">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

## <a name="blazor-server"></a><span data-ttu-id="d4aa0-122">Serwer Blazor</span><span class="sxs-lookup"><span data-stu-id="d4aa0-122">Blazor Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="d4aa0-123">Odzwierciedlanie stanu połączenia w interfejsie użytkownika</span><span class="sxs-lookup"><span data-stu-id="d4aa0-123">Reflect the connection state in the UI</span></span>

<span data-ttu-id="d4aa0-124">Gdy klient wykryje, że połączenie zostało utracone, domyślny interfejs użytkownika jest wyświetlany użytkownikowi, podczas gdy klient próbuje ponownie połączyć.</span><span class="sxs-lookup"><span data-stu-id="d4aa0-124">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="d4aa0-125">Jeśli ponowne połączenie nie powiedzie się, użytkownik ma możliwość ponowienia próby.</span><span class="sxs-lookup"><span data-stu-id="d4aa0-125">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="d4aa0-126">Aby dostosować interfejs użytkownika, zdefiniuj element z `id` dokączą `components-reconnect-modal` `<body>` na stronie *_Host.cshtml* Razor:</span><span class="sxs-lookup"><span data-stu-id="d4aa0-126">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="d4aa0-127">W poniższej tabeli opisano klasy `components-reconnect-modal` CSS zastosowane do elementu.</span><span class="sxs-lookup"><span data-stu-id="d4aa0-127">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="d4aa0-128">Klasa CSS</span><span class="sxs-lookup"><span data-stu-id="d4aa0-128">CSS class</span></span>                       | <span data-ttu-id="d4aa0-129">Wskazuje&hellip;</span><span class="sxs-lookup"><span data-stu-id="d4aa0-129">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="d4aa0-130">Utracone połączenie.</span><span class="sxs-lookup"><span data-stu-id="d4aa0-130">A lost connection.</span></span> <span data-ttu-id="d4aa0-131">Klient próbuje ponownie nawiązać połączenie.</span><span class="sxs-lookup"><span data-stu-id="d4aa0-131">The client is attempting to reconnect.</span></span> <span data-ttu-id="d4aa0-132">Pokaż modal.</span><span class="sxs-lookup"><span data-stu-id="d4aa0-132">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="d4aa0-133">Aktywne połączenie zostanie ponownie nawiązane do serwera.</span><span class="sxs-lookup"><span data-stu-id="d4aa0-133">An active connection is re-established to the server.</span></span> <span data-ttu-id="d4aa0-134">Ukryj modal.</span><span class="sxs-lookup"><span data-stu-id="d4aa0-134">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="d4aa0-135">Ponowne połączenie nie powiodło się, prawdopodobnie z powodu awarii sieci.</span><span class="sxs-lookup"><span data-stu-id="d4aa0-135">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="d4aa0-136">Aby spróbować ponownie połączyć, zadzwoń do `window.Blazor.reconnect()`pliku .</span><span class="sxs-lookup"><span data-stu-id="d4aa0-136">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="d4aa0-137">Ponowne połączenie odrzucone.</span><span class="sxs-lookup"><span data-stu-id="d4aa0-137">Reconnection rejected.</span></span> <span data-ttu-id="d4aa0-138">Serwer został osiągnięty, ale odmówił połączenia, a stan użytkownika na serwerze zostanie utracony.</span><span class="sxs-lookup"><span data-stu-id="d4aa0-138">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="d4aa0-139">Aby ponownie załadować `location.reload()`aplikację, zadzwoń .</span><span class="sxs-lookup"><span data-stu-id="d4aa0-139">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="d4aa0-140">Ten stan połączenia może spowodować, gdy:</span><span class="sxs-lookup"><span data-stu-id="d4aa0-140">This connection state may result when:</span></span><ul><li><span data-ttu-id="d4aa0-141">Wystąpi awaria obwodu po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="d4aa0-141">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="d4aa0-142">Klient jest rozłączany wystarczająco długo, aby serwer porzucić stan użytkownika.</span><span class="sxs-lookup"><span data-stu-id="d4aa0-142">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="d4aa0-143">Wystąpienia składników, z którymi użytkownik wchodzi w interakcję, są usuwane.</span><span class="sxs-lookup"><span data-stu-id="d4aa0-143">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="d4aa0-144">Serwer zostanie ponownie uruchomiony lub proces roboczy aplikacji jest odtwoniany.</span><span class="sxs-lookup"><span data-stu-id="d4aa0-144">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="d4aa0-145">Tryb renderowania</span><span class="sxs-lookup"><span data-stu-id="d4aa0-145">Render mode</span></span>

<span data-ttu-id="d4aa0-146">Aplikacje Blazor Server są domyślnie skonfigurowane do prerender interfejsu użytkownika na serwerze przed nawiązaniem połączenia klienta z serwerem.</span><span class="sxs-lookup"><span data-stu-id="d4aa0-146">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="d4aa0-147">Jest to skonfigurowane na stronie *_Host.cshtml* Razor:</span><span class="sxs-lookup"><span data-stu-id="d4aa0-147">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="d4aa0-148">`RenderMode`określa, czy składnik:</span><span class="sxs-lookup"><span data-stu-id="d4aa0-148">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="d4aa0-149">Jest prerendered do strony.</span><span class="sxs-lookup"><span data-stu-id="d4aa0-149">Is prerendered into the page.</span></span>
* <span data-ttu-id="d4aa0-150">Jest renderowany jako statyczny HTML na stronie lub jeśli zawiera niezbędne informacje do bootstrap aplikacji Blazor od agenta użytkownika.</span><span class="sxs-lookup"><span data-stu-id="d4aa0-150">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="d4aa0-151">Opis</span><span class="sxs-lookup"><span data-stu-id="d4aa0-151">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="d4aa0-152">Renderuje składnik do statycznego kodu HTML Blazor i zawiera znacznik aplikacji serwera.</span><span class="sxs-lookup"><span data-stu-id="d4aa0-152">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="d4aa0-153">Po uruchomieniu agenta użytkownika ten znacznik jest używany Blazor do uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d4aa0-153">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="d4aa0-154">Renderuje znacznik aplikacji Blazor serwera.</span><span class="sxs-lookup"><span data-stu-id="d4aa0-154">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="d4aa0-155">Dane wyjściowe ze składnika nie są uwzględniane.</span><span class="sxs-lookup"><span data-stu-id="d4aa0-155">Output from the component isn't included.</span></span> <span data-ttu-id="d4aa0-156">Po uruchomieniu agenta użytkownika ten znacznik jest używany Blazor do uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d4aa0-156">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="d4aa0-157">Renderuje składnik do statycznego kodu HTML.</span><span class="sxs-lookup"><span data-stu-id="d4aa0-157">Renders the component into static HTML.</span></span> |

<span data-ttu-id="d4aa0-158">Renderowanie składników serwera ze statycznej strony HTML nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="d4aa0-158">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a><span data-ttu-id="d4aa0-159">Renderowanie stanowych składników interaktywnych ze stron i widoków Razor</span><span class="sxs-lookup"><span data-stu-id="d4aa0-159">Render stateful interactive components from Razor pages and views</span></span>

<span data-ttu-id="d4aa0-160">Stanowe składniki interaktywne można dodać do strony lub widoku Razor.</span><span class="sxs-lookup"><span data-stu-id="d4aa0-160">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="d4aa0-161">Gdy strona lub widok renderuje:</span><span class="sxs-lookup"><span data-stu-id="d4aa0-161">When the page or view renders:</span></span>

* <span data-ttu-id="d4aa0-162">Składnik jest prerendered ze stroną lub widoku.</span><span class="sxs-lookup"><span data-stu-id="d4aa0-162">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="d4aa0-163">Początkowy stan składnika używany do wstępnego rozsyłania jest tracony.</span><span class="sxs-lookup"><span data-stu-id="d4aa0-163">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="d4aa0-164">Nowy stan składnika SignalR jest tworzony po nawiązaniu połączenia.</span><span class="sxs-lookup"><span data-stu-id="d4aa0-164">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="d4aa0-165">Następująca strona Razor `Counter` renderuje składnik:</span><span class="sxs-lookup"><span data-stu-id="d4aa0-165">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a><span data-ttu-id="d4aa0-166">Renderowanie składników nieinteraktywnych ze stron i widoków razor</span><span class="sxs-lookup"><span data-stu-id="d4aa0-166">Render noninteractive components from Razor pages and views</span></span>

<span data-ttu-id="d4aa0-167">Na następującej stronie Razor `Counter` składnik jest statycznie renderowany z wartością początkową określoną przy użyciu formularza:</span><span class="sxs-lookup"><span data-stu-id="d4aa0-167">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form:</span></span>

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="d4aa0-168">Ponieważ `MyComponent` jest statycznie renderowane, składnik nie może być interaktywny.</span><span class="sxs-lookup"><span data-stu-id="d4aa0-168">Since `MyComponent` is statically rendered, the component can't be interactive.</span></span>

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a><span data-ttu-id="d4aa0-169">Konfigurowanie SignalR klienta Blazor dla aplikacji serwera</span><span class="sxs-lookup"><span data-stu-id="d4aa0-169">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="d4aa0-170">Czasami należy skonfigurować SignalR klienta używanego Blazor przez aplikacje serwera.</span><span class="sxs-lookup"><span data-stu-id="d4aa0-170">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="d4aa0-171">Na przykład można skonfigurować rejestrowanie na SignalR kliencie, aby zdiagnozować problem z połączeniem.</span><span class="sxs-lookup"><span data-stu-id="d4aa0-171">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="d4aa0-172">Aby skonfigurować SignalR klienta w pliku *Pages/_Host.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="d4aa0-172">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="d4aa0-173">Dodaj `autostart="false"` atrybut do `<script>` znacznika `blazor.server.js` skryptu.</span><span class="sxs-lookup"><span data-stu-id="d4aa0-173">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="d4aa0-174">Wywołaj `Blazor.start` i przekaż w obiekcie SignalR konfiguracji, który określa konstruktora.</span><span class="sxs-lookup"><span data-stu-id="d4aa0-174">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging("information"); // LogLevel.Information
    }
  });
</script>
```
