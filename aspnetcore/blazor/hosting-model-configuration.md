---
title: Konfiguracja modelu Blazor hostingu ASP.NET Core
author: guardrex
description: Dowiedz Blazor się więcej o konfiguracji modelu hostingu, w tym o tym, jak zintegrować komponenty Razor ze stronami Razor i aplikacjami MVC.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2020
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-model-configuration
ms.openlocfilehash: ca1b3ea9092640ca561b3fbe02ddce6f974c525e
ms.sourcegitcommit: e8dc30453af8bbefcb61857987090d79230a461d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/11/2020
ms.locfileid: "81123372"
---
# <a name="aspnet-core-blazor-hosting-model-configuration"></a><span data-ttu-id="9abf5-103">konfiguracja modelu hostingowego ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="9abf5-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="9abf5-104">Przez [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="9abf5-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="9abf5-105">W tym artykule omówiono konfigurację modelu hostingu.</span><span class="sxs-lookup"><span data-stu-id="9abf5-105">This article covers hosting model configuration.</span></span>

## <a name="blazor-webassembly"></a><span data-ttu-id="9abf5-106">Zestaw WebAssembly Blazor</span><span class="sxs-lookup"><span data-stu-id="9abf5-106">Blazor WebAssembly</span></span>

### <a name="environment"></a><span data-ttu-id="9abf5-107">Środowisko</span><span class="sxs-lookup"><span data-stu-id="9abf5-107">Environment</span></span>

<span data-ttu-id="9abf5-108">Podczas uruchamiania aplikacji lokalnie środowiska domyślnie rozwoju.</span><span class="sxs-lookup"><span data-stu-id="9abf5-108">When running an app locally, the environment defaults to Development.</span></span> <span data-ttu-id="9abf5-109">Po opublikowaniu aplikacji środowisko domyślnie ma wartość Produkcja.</span><span class="sxs-lookup"><span data-stu-id="9abf5-109">When the app is published, the environment defaults to Production.</span></span>

<span data-ttu-id="9abf5-110">Hostowana aplikacja Blazor WebAssembly odbiera środowisko z serwera za pośrednictwem oprogramowania pośredniczącego, `blazor-environment` które komunikuje środowisko z przeglądarką przez dodanie nagłówka.</span><span class="sxs-lookup"><span data-stu-id="9abf5-110">A hosted Blazor WebAssembly app picks up the environment from the server via a middleware that communicates the environment to the browser by adding the `blazor-environment` header.</span></span> <span data-ttu-id="9abf5-111">Wartością nagłówka jest środowisko.</span><span class="sxs-lookup"><span data-stu-id="9abf5-111">The value of the header is the environment.</span></span> <span data-ttu-id="9abf5-112">Hostowana aplikacja Blazor i aplikacja serwera współużytkuje to samo środowisko.</span><span class="sxs-lookup"><span data-stu-id="9abf5-112">The hosted Blazor app and the server app share the same environment.</span></span> <span data-ttu-id="9abf5-113">Aby uzyskać więcej informacji, w tym <xref:fundamentals/environments>jak skonfigurować środowisko, zobacz .</span><span class="sxs-lookup"><span data-stu-id="9abf5-113">For more information, including how to configure the environment, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="9abf5-114">W przypadku autonomicznej aplikacji działającej lokalnie `blazor-environment` serwer deweloperów dodaje nagłówek w celu określenia środowiska deweloperskiego.</span><span class="sxs-lookup"><span data-stu-id="9abf5-114">For a standalone app running locally, the development server adds the `blazor-environment` header to specify the Development environment.</span></span> <span data-ttu-id="9abf5-115">Aby określić środowisko dla innych środowisk `blazor-environment` hostingowych, dodaj nagłówek.</span><span class="sxs-lookup"><span data-stu-id="9abf5-115">To specify the environment for other hosting environments, add the `blazor-environment` header.</span></span>

<span data-ttu-id="9abf5-116">W poniższym przykładzie dla usług IIS dodaj niestandardowy nagłówek do opublikowanego pliku *web.config.*</span><span class="sxs-lookup"><span data-stu-id="9abf5-116">In the following example for IIS, add the custom header to the published *web.config* file.</span></span> <span data-ttu-id="9abf5-117">Plik *web.config* znajduje się w folderze *bin/release/{TARGET FRAMEWORK}/publish:*</span><span class="sxs-lookup"><span data-stu-id="9abf5-117">The *web.config* file is located in the *bin/Release/{TARGET FRAMEWORK}/publish* folder:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>

    ...

    <httpProtocol>
      <customHeaders>
        <add name="blazor-environment" value="Staging" />
      </customHeaders>
    </httpProtocol>
  </system.webServer>
</configuration>
```

> [!NOTE]
> <span data-ttu-id="9abf5-118">Aby użyć niestandardowego pliku *web.config* dla usług IIS, który nie jest zastępowany <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>podczas publikowania aplikacji w folderze *publikowania,* zobacz .</span><span class="sxs-lookup"><span data-stu-id="9abf5-118">To use a custom *web.config* file for IIS that isn't overwritten when the app is published to the *publish* folder, see <xref:host-and-deploy/blazor/webassembly#use-a-custom-webconfig>.</span></span>

<span data-ttu-id="9abf5-119">Uzyskaj środowisko aplikacji w składniku, `IWebAssemblyHostEnvironment` wstrzykując i odczytując `Environment` właściwość:</span><span class="sxs-lookup"><span data-stu-id="9abf5-119">Obtain the app's environment in a component by injecting `IWebAssemblyHostEnvironment` and reading the `Environment` property:</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Components.WebAssembly.Hosting
@inject IWebAssemblyHostEnvironment HostEnvironment

<h1>Environment example</h1>

<p>Environment: @HostEnvironment.Environment</p>
```

### <a name="configuration"></a><span data-ttu-id="9abf5-120">Konfigurowanie</span><span class="sxs-lookup"><span data-stu-id="9abf5-120">Configuration</span></span>

<span data-ttu-id="9abf5-121">W wersji ASP.NET Core 3.2 Preview 3, Blazor WebAssembly obsługuje konfigurację z:</span><span class="sxs-lookup"><span data-stu-id="9abf5-121">As of the ASP.NET Core 3.2 Preview 3 release, Blazor WebAssembly supports configuration from:</span></span>

* <span data-ttu-id="9abf5-122">*wwwroot/appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="9abf5-122">*wwwroot/appsettings.json*</span></span>
* <span data-ttu-id="9abf5-123">*wwwroot/appsettings. {ŚRODOWISKO}.json*</span><span class="sxs-lookup"><span data-stu-id="9abf5-123">*wwwroot/appsettings.{ENVIRONMENT}.json*</span></span>

<span data-ttu-id="9abf5-124">Dodaj plik *appsettings.json* w folderze *wwwroot:*</span><span class="sxs-lookup"><span data-stu-id="9abf5-124">Add an *appsettings.json* file in the *wwwroot* folder:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="9abf5-125">Wstrzyknąć wystąpienie <xref:Microsoft.Extensions.Configuration.IConfiguration> do składnika, aby uzyskać dostęp do danych konfiguracyjnych:</span><span class="sxs-lookup"><span data-stu-id="9abf5-125">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

> [!WARNING]
> <span data-ttu-id="9abf5-126">Konfiguracja w aplikacji Blazor WebAssembly jest widoczna dla użytkowników.</span><span class="sxs-lookup"><span data-stu-id="9abf5-126">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="9abf5-127">**Nie przechowuj wpisów tajnych aplikacji ani poświadczeń w konfiguracji.**</span><span class="sxs-lookup"><span data-stu-id="9abf5-127">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="9abf5-128">Pliki konfiguracyjne są buforowane do użytku w trybie offline.</span><span class="sxs-lookup"><span data-stu-id="9abf5-128">Configuration files are cached for offline use.</span></span> <span data-ttu-id="9abf5-129">Dzięki [progresywnym aplikacjom sieci Web (PWA)](xref:blazor/progressive-web-app)można aktualizować tylko pliki konfiguracyjne podczas tworzenia nowego wdrożenia.</span><span class="sxs-lookup"><span data-stu-id="9abf5-129">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="9abf5-130">Edytowanie plików konfiguracyjnych między wdrożeniami nie ma wpływu, ponieważ:</span><span class="sxs-lookup"><span data-stu-id="9abf5-130">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="9abf5-131">Użytkownicy mają buforowane wersje plików, które nadal używają.</span><span class="sxs-lookup"><span data-stu-id="9abf5-131">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="9abf5-132">Pliki *service-worker.js* i *service-worker-assets.js* muszą zostać przebudowane na kompilacji, która sygnalizuje aplikacji przy następnej wizycie online użytkownika, że aplikacja została ponownie rozmieszczona.</span><span class="sxs-lookup"><span data-stu-id="9abf5-132">The PWA's *service-worker.js* and *service-worker-assets.js* files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="9abf5-133">Aby uzyskać więcej informacji na temat sposobu obsługi <xref:blazor/progressive-web-app#background-updates>aktualizacji w tle przez programy PWA, zobacz .</span><span class="sxs-lookup"><span data-stu-id="9abf5-133">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>

## <a name="blazor-server"></a><span data-ttu-id="9abf5-134">Serwer Blazor</span><span class="sxs-lookup"><span data-stu-id="9abf5-134">Blazor Server</span></span>

### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="9abf5-135">Odzwierciedlanie stanu połączenia w interfejsie użytkownika</span><span class="sxs-lookup"><span data-stu-id="9abf5-135">Reflect the connection state in the UI</span></span>

<span data-ttu-id="9abf5-136">Gdy klient wykryje, że połączenie zostało utracone, domyślny interfejs użytkownika jest wyświetlany użytkownikowi, podczas gdy klient próbuje ponownie połączyć.</span><span class="sxs-lookup"><span data-stu-id="9abf5-136">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="9abf5-137">Jeśli ponowne połączenie nie powiedzie się, użytkownik ma możliwość ponowienia próby.</span><span class="sxs-lookup"><span data-stu-id="9abf5-137">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="9abf5-138">Aby dostosować interfejs użytkownika, zdefiniuj element z `id` dokączą `components-reconnect-modal` `<body>` na stronie *_Host.cshtml* Razor:</span><span class="sxs-lookup"><span data-stu-id="9abf5-138">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="9abf5-139">W poniższej tabeli opisano klasy `components-reconnect-modal` CSS zastosowane do elementu.</span><span class="sxs-lookup"><span data-stu-id="9abf5-139">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="9abf5-140">Klasa CSS</span><span class="sxs-lookup"><span data-stu-id="9abf5-140">CSS class</span></span>                       | <span data-ttu-id="9abf5-141">Wskazuje&hellip;</span><span class="sxs-lookup"><span data-stu-id="9abf5-141">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="9abf5-142">Utracone połączenie.</span><span class="sxs-lookup"><span data-stu-id="9abf5-142">A lost connection.</span></span> <span data-ttu-id="9abf5-143">Klient próbuje ponownie nawiązać połączenie.</span><span class="sxs-lookup"><span data-stu-id="9abf5-143">The client is attempting to reconnect.</span></span> <span data-ttu-id="9abf5-144">Pokaż modal.</span><span class="sxs-lookup"><span data-stu-id="9abf5-144">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="9abf5-145">Aktywne połączenie zostanie ponownie nawiązane do serwera.</span><span class="sxs-lookup"><span data-stu-id="9abf5-145">An active connection is re-established to the server.</span></span> <span data-ttu-id="9abf5-146">Ukryj modal.</span><span class="sxs-lookup"><span data-stu-id="9abf5-146">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="9abf5-147">Ponowne połączenie nie powiodło się, prawdopodobnie z powodu awarii sieci.</span><span class="sxs-lookup"><span data-stu-id="9abf5-147">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="9abf5-148">Aby spróbować ponownie połączyć, zadzwoń do `window.Blazor.reconnect()`pliku .</span><span class="sxs-lookup"><span data-stu-id="9abf5-148">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="9abf5-149">Ponowne połączenie odrzucone.</span><span class="sxs-lookup"><span data-stu-id="9abf5-149">Reconnection rejected.</span></span> <span data-ttu-id="9abf5-150">Serwer został osiągnięty, ale odmówił połączenia, a stan użytkownika na serwerze zostanie utracony.</span><span class="sxs-lookup"><span data-stu-id="9abf5-150">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="9abf5-151">Aby ponownie załadować `location.reload()`aplikację, zadzwoń .</span><span class="sxs-lookup"><span data-stu-id="9abf5-151">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="9abf5-152">Ten stan połączenia może spowodować, gdy:</span><span class="sxs-lookup"><span data-stu-id="9abf5-152">This connection state may result when:</span></span><ul><li><span data-ttu-id="9abf5-153">Wystąpi awaria obwodu po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="9abf5-153">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="9abf5-154">Klient jest rozłączany wystarczająco długo, aby serwer porzucić stan użytkownika.</span><span class="sxs-lookup"><span data-stu-id="9abf5-154">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="9abf5-155">Wystąpienia składników, z którymi użytkownik wchodzi w interakcję, są usuwane.</span><span class="sxs-lookup"><span data-stu-id="9abf5-155">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="9abf5-156">Serwer zostanie ponownie uruchomiony lub proces roboczy aplikacji jest odtwoniany.</span><span class="sxs-lookup"><span data-stu-id="9abf5-156">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="render-mode"></a><span data-ttu-id="9abf5-157">Tryb renderowania</span><span class="sxs-lookup"><span data-stu-id="9abf5-157">Render mode</span></span>

<span data-ttu-id="9abf5-158">Aplikacje Blazor Server są domyślnie skonfigurowane do prerender interfejsu użytkownika na serwerze przed nawiązaniem połączenia klienta z serwerem.</span><span class="sxs-lookup"><span data-stu-id="9abf5-158">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="9abf5-159">Jest to skonfigurowane na stronie *_Host.cshtml* Razor:</span><span class="sxs-lookup"><span data-stu-id="9abf5-159">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="9abf5-160">`RenderMode`określa, czy składnik:</span><span class="sxs-lookup"><span data-stu-id="9abf5-160">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="9abf5-161">Jest prerendered do strony.</span><span class="sxs-lookup"><span data-stu-id="9abf5-161">Is prerendered into the page.</span></span>
* <span data-ttu-id="9abf5-162">Jest renderowany jako statyczny HTML na stronie lub jeśli zawiera niezbędne informacje do bootstrap aplikacji Blazor od agenta użytkownika.</span><span class="sxs-lookup"><span data-stu-id="9abf5-162">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="9abf5-163">Opis</span><span class="sxs-lookup"><span data-stu-id="9abf5-163">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="9abf5-164">Renderuje składnik do statycznego kodu HTML Blazor i zawiera znacznik aplikacji serwera.</span><span class="sxs-lookup"><span data-stu-id="9abf5-164">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="9abf5-165">Po uruchomieniu agenta użytkownika ten znacznik jest używany Blazor do uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9abf5-165">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="9abf5-166">Renderuje znacznik aplikacji Blazor serwera.</span><span class="sxs-lookup"><span data-stu-id="9abf5-166">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="9abf5-167">Dane wyjściowe ze składnika nie są uwzględniane.</span><span class="sxs-lookup"><span data-stu-id="9abf5-167">Output from the component isn't included.</span></span> <span data-ttu-id="9abf5-168">Po uruchomieniu agenta użytkownika ten znacznik jest używany Blazor do uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9abf5-168">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="9abf5-169">Renderuje składnik do statycznego kodu HTML.</span><span class="sxs-lookup"><span data-stu-id="9abf5-169">Renders the component into static HTML.</span></span> |

<span data-ttu-id="9abf5-170">Renderowanie składników serwera ze statycznej strony HTML nie jest obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="9abf5-170">Rendering server components from a static HTML page isn't supported.</span></span>

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a><span data-ttu-id="9abf5-171">Renderowanie stanowych składników interaktywnych ze stron i widoków Razor</span><span class="sxs-lookup"><span data-stu-id="9abf5-171">Render stateful interactive components from Razor pages and views</span></span>

<span data-ttu-id="9abf5-172">Stanowe składniki interaktywne można dodać do strony lub widoku Razor.</span><span class="sxs-lookup"><span data-stu-id="9abf5-172">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="9abf5-173">Gdy strona lub widok renderuje:</span><span class="sxs-lookup"><span data-stu-id="9abf5-173">When the page or view renders:</span></span>

* <span data-ttu-id="9abf5-174">Składnik jest prerendered ze stroną lub widoku.</span><span class="sxs-lookup"><span data-stu-id="9abf5-174">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="9abf5-175">Początkowy stan składnika używany do wstępnego rozsyłania jest tracony.</span><span class="sxs-lookup"><span data-stu-id="9abf5-175">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="9abf5-176">Nowy stan składnika SignalR jest tworzony po nawiązaniu połączenia.</span><span class="sxs-lookup"><span data-stu-id="9abf5-176">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="9abf5-177">Następująca strona Razor `Counter` renderuje składnik:</span><span class="sxs-lookup"><span data-stu-id="9abf5-177">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a><span data-ttu-id="9abf5-178">Renderowanie składników nieinteraktywnych ze stron i widoków razor</span><span class="sxs-lookup"><span data-stu-id="9abf5-178">Render noninteractive components from Razor pages and views</span></span>

<span data-ttu-id="9abf5-179">Na następującej stronie Razor `Counter` składnik jest statycznie renderowany z wartością początkową określoną przy użyciu formularza:</span><span class="sxs-lookup"><span data-stu-id="9abf5-179">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form:</span></span>

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

<span data-ttu-id="9abf5-180">Ponieważ `MyComponent` jest statycznie renderowane, składnik nie może być interaktywny.</span><span class="sxs-lookup"><span data-stu-id="9abf5-180">Since `MyComponent` is statically rendered, the component can't be interactive.</span></span>

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a><span data-ttu-id="9abf5-181">Konfigurowanie SignalR klienta Blazor dla aplikacji serwera</span><span class="sxs-lookup"><span data-stu-id="9abf5-181">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="9abf5-182">Czasami należy skonfigurować SignalR klienta używanego Blazor przez aplikacje serwera.</span><span class="sxs-lookup"><span data-stu-id="9abf5-182">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="9abf5-183">Na przykład można skonfigurować rejestrowanie na SignalR kliencie, aby zdiagnozować problem z połączeniem.</span><span class="sxs-lookup"><span data-stu-id="9abf5-183">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="9abf5-184">Aby skonfigurować SignalR klienta w pliku *Pages/_Host.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="9abf5-184">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="9abf5-185">Dodaj `autostart="false"` atrybut do `<script>` znacznika `blazor.server.js` skryptu.</span><span class="sxs-lookup"><span data-stu-id="9abf5-185">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="9abf5-186">Wywołaj `Blazor.start` i przekaż w obiekcie SignalR konfiguracji, który określa konstruktora.</span><span class="sxs-lookup"><span data-stu-id="9abf5-186">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

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
