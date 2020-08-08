---
title: ASP.NET Core Blazor globalizacja i lokalizacja
author: guardrex
description: Dowiedz się, jak udostępnić Razor składniki użytkownikom w wielu kulturach i językach.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/globalization-localization
ms.openlocfilehash: 59b6e4cb2f466594d8a105a239e175e9c7b37ad8
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014249"
---
# <a name="aspnet-core-no-locblazor-globalization-and-localization"></a><span data-ttu-id="ead75-103">ASP.NET Core Blazor globalizacja i lokalizacja</span><span class="sxs-lookup"><span data-stu-id="ead75-103">ASP.NET Core Blazor globalization and localization</span></span>

<span data-ttu-id="ead75-104">Autorzy [Luke Latham](https://github.com/guardrex) i [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="ead75-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="ead75-105">Razorskładniki mogą być udostępniane użytkownikom w wielu kulturach i językach.</span><span class="sxs-lookup"><span data-stu-id="ead75-105">Razor components can be made accessible to users in multiple cultures and languages.</span></span> <span data-ttu-id="ead75-106">Dostępne są następujące scenariusze globalizacji i lokalizacji platformy .NET:</span><span class="sxs-lookup"><span data-stu-id="ead75-106">The following .NET globalization and localization scenarios are available:</span></span>

* <span data-ttu-id="ead75-107">. System zasobów netto</span><span class="sxs-lookup"><span data-stu-id="ead75-107">.NET's resources system</span></span>
* <span data-ttu-id="ead75-108">Formatowanie liczb i dat specyficznych dla kultury</span><span class="sxs-lookup"><span data-stu-id="ead75-108">Culture-specific number and date formatting</span></span>

<span data-ttu-id="ead75-109">Obecnie obsługiwane są ograniczone zestawy ASP.NET Core scenariuszy lokalizacji:</span><span class="sxs-lookup"><span data-stu-id="ead75-109">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="ead75-110"><xref:Microsoft.Extensions.Localization.IStringLocalizer>i <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> *są obsługiwane* w Blazor aplikacjach.</span><span class="sxs-lookup"><span data-stu-id="ead75-110"><xref:Microsoft.Extensions.Localization.IStringLocalizer> and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> *are supported* in Blazor apps.</span></span>
* <span data-ttu-id="ead75-111"><xref:Microsoft.AspNetCore.Mvc.Localization.IHtmlLocalizer><xref:Microsoft.AspNetCore.Mvc.Localization.IViewLocalizer>lokalizacje adnotacji danych są ASP.NET Core w SCENARIUSZACH MVC i **nie są obsługiwane** w Blazor aplikacjach.</span><span class="sxs-lookup"><span data-stu-id="ead75-111"><xref:Microsoft.AspNetCore.Mvc.Localization.IHtmlLocalizer>, <xref:Microsoft.AspNetCore.Mvc.Localization.IViewLocalizer>, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in Blazor apps.</span></span>

<span data-ttu-id="ead75-112">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="ead75-112">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="globalization"></a><span data-ttu-id="ead75-113">Globalizacja</span><span class="sxs-lookup"><span data-stu-id="ead75-113">Globalization</span></span>

<span data-ttu-id="ead75-114">Blazor[`@bind`](xref:mvc/views/razor#bind)Funkcja wykonuje formatowanie i analizuje wartości na potrzeby wyświetlania w oparciu o bieżącą kulturę użytkownika.</span><span class="sxs-lookup"><span data-stu-id="ead75-114">Blazor's [`@bind`](xref:mvc/views/razor#bind) functionality performs formats and parses values for display based on the user's current culture.</span></span>

<span data-ttu-id="ead75-115">Dla bieżącej kultury można uzyskać dostęp z <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> właściwości.</span><span class="sxs-lookup"><span data-stu-id="ead75-115">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="ead75-116"><xref:System.Globalization.CultureInfo.InvariantCulture?displayProperty=nameWithType>jest używany dla następujących typów pól ( `<input type="{TYPE}" />` ):</span><span class="sxs-lookup"><span data-stu-id="ead75-116"><xref:System.Globalization.CultureInfo.InvariantCulture?displayProperty=nameWithType> is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="ead75-117">Poprzednie typy pól:</span><span class="sxs-lookup"><span data-stu-id="ead75-117">The preceding field types:</span></span>

* <span data-ttu-id="ead75-118">Są wyświetlane przy użyciu odpowiednich reguł formatowania opartych na przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="ead75-118">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="ead75-119">Nie może zawierać tekstu o dowolnym formacie.</span><span class="sxs-lookup"><span data-stu-id="ead75-119">Can't contain free-form text.</span></span>
* <span data-ttu-id="ead75-120">Podaj charakterystykę interakcji użytkownika w oparciu o implementację przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="ead75-120">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="ead75-121">Następujące typy pól mają określone wymagania dotyczące formatowania i nie są obecnie obsługiwane przez program, Blazor ponieważ nie są obsługiwane przez wszystkie główne przeglądarki:</span><span class="sxs-lookup"><span data-stu-id="ead75-121">The following field types have specific formatting requirements and aren't currently supported by Blazor because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="ead75-122">[`@bind`](xref:mvc/views/razor#bind)obsługuje `@bind:culture` parametr, aby zapewnić <xref:System.Globalization.CultureInfo?displayProperty=fullName> analizę i formatowanie wartości.</span><span class="sxs-lookup"><span data-stu-id="ead75-122">[`@bind`](xref:mvc/views/razor#bind) supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="ead75-123">Określanie kultury nie jest zalecane w przypadku używania `date` `number` typów pól i.</span><span class="sxs-lookup"><span data-stu-id="ead75-123">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="ead75-124">`date`i `number` ma wbudowaną Blazor obsługę, która udostępnia wymaganą kulturę.</span><span class="sxs-lookup"><span data-stu-id="ead75-124">`date` and `number` have built-in Blazor support that provides the required culture.</span></span>

## <a name="localization"></a><span data-ttu-id="ead75-125">Lokalizacja</span><span class="sxs-lookup"><span data-stu-id="ead75-125">Localization</span></span>

### Blazor WebAssembly

<span data-ttu-id="ead75-126">Blazor WebAssemblyaplikacje ustawiają kulturę przy użyciu [preferencji języka](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages)użytkownika.</span><span class="sxs-lookup"><span data-stu-id="ead75-126">Blazor WebAssembly apps set the culture using the user's [language preference](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages).</span></span>

<span data-ttu-id="ead75-127">Aby jawnie skonfigurować kulturę, ustaw <xref:System.Globalization.CultureInfo.DefaultThreadCurrentCulture?displayProperty=nameWithType> i <xref:System.Globalization.CultureInfo.DefaultThreadCurrentUICulture?displayProperty=nameWithType> w `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="ead75-127">To explicitly configure the culture, set <xref:System.Globalization.CultureInfo.DefaultThreadCurrentCulture?displayProperty=nameWithType> and <xref:System.Globalization.CultureInfo.DefaultThreadCurrentUICulture?displayProperty=nameWithType> in `Program.Main`.</span></span>

<span data-ttu-id="ead75-128">Domyślnie Blazor Konfiguracja konsolidatora aplikacji umożliwia rozłączenie Blazor WebAssembly informacji o ustawieniach regionalnych, z wyjątkiem sytuacji, w których jawnie zażądano.</span><span class="sxs-lookup"><span data-stu-id="ead75-128">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="ead75-129">Aby uzyskać więcej informacji i wskazówek dotyczących kontrolowania zachowania konsolidatora, zobacz <xref:blazor/host-and-deploy/configure-linker#configure-the-linker-for-internationalization> .</span><span class="sxs-lookup"><span data-stu-id="ead75-129">For more information and guidance on controlling the linker's behavior, see <xref:blazor/host-and-deploy/configure-linker#configure-the-linker-for-internationalization>.</span></span>

<span data-ttu-id="ead75-130">Chociaż kultura, która Blazor wybiera domyślnie, może być wystarczająca dla większości użytkowników, należy rozważyć umożliwienie użytkownikom określenia ich preferowanych ustawień regionalnych.</span><span class="sxs-lookup"><span data-stu-id="ead75-130">While the culture that Blazor selects by default might be sufficient for most users, consider offering a way for users to specify their preferred locale.</span></span> <span data-ttu-id="ead75-131">Aby uzyskać Blazor WebAssembly przykładową aplikację z selektorem kultury, zobacz [`LocSample`](https://github.com/pranavkm/LocSample) przykładową aplikację lokalizacyjną.</span><span class="sxs-lookup"><span data-stu-id="ead75-131">For a Blazor WebAssembly sample app with a culture picker, see the [`LocSample`](https://github.com/pranavkm/LocSample) localization sample app.</span></span>

### Blazor Server

<span data-ttu-id="ead75-132">Blazor Serveraplikacje są zlokalizowane przy użyciu [oprogramowania pośredniczącego](xref:fundamentals/localization#localization-middleware).</span><span class="sxs-lookup"><span data-stu-id="ead75-132">Blazor Server apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="ead75-133">Oprogramowanie pośredniczące wybiera odpowiednią kulturę dla użytkowników żądających zasobów z aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ead75-133">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="ead75-134">Kulturę można ustawić przy użyciu jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="ead75-134">The culture can be set using one of the following approaches:</span></span>

* <span data-ttu-id="ead75-135">[Cookiewolumin](#cookies)</span><span class="sxs-lookup"><span data-stu-id="ead75-135">[Cookies](#cookies)</span></span>
* [<span data-ttu-id="ead75-136">Podaj interfejs użytkownika, aby wybrać kulturę</span><span class="sxs-lookup"><span data-stu-id="ead75-136">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="ead75-137">Aby uzyskać więcej informacji i przykładów, zobacz <xref:fundamentals/localization> .</span><span class="sxs-lookup"><span data-stu-id="ead75-137">For more information and examples, see <xref:fundamentals/localization>.</span></span>

#### <a name="no-loccookies"></a><span data-ttu-id="ead75-138">Cookiewolumin</span><span class="sxs-lookup"><span data-stu-id="ead75-138">Cookies</span></span>

<span data-ttu-id="ead75-139">Kultura lokalizacji cookie może utrwalać kulturę użytkownika.</span><span class="sxs-lookup"><span data-stu-id="ead75-139">A localization culture cookie can persist the user's culture.</span></span> <span data-ttu-id="ead75-140">Oprogramowanie pośredniczące lokalizacji odczytuje cookie kolejne żądania, aby ustawić kulturę użytkownika.</span><span class="sxs-lookup"><span data-stu-id="ead75-140">The Localization Middleware reads the cookie on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="ead75-141">Użycie a cookie zapewnia, że połączenie WebSocket może prawidłowo propagować kulturę.</span><span class="sxs-lookup"><span data-stu-id="ead75-141">Use of a cookie ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="ead75-142">Jeśli schematy lokalizacji są oparte na ścieżce URL lub ciągu zapytania, schemat może nie być w stanie współdziałać z usługą WebSockets, więc nie będzie można zachować kultury.</span><span class="sxs-lookup"><span data-stu-id="ead75-142">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="ead75-143">W związku z tym zaleca się użycie kultury lokalizacji cookie .</span><span class="sxs-lookup"><span data-stu-id="ead75-143">Therefore, use of a localization culture cookie is the recommended approach.</span></span>

<span data-ttu-id="ead75-144">Każda technika może służyć do przypisywania kultury, jeśli kultura jest utrwalona w lokalizacji cookie .</span><span class="sxs-lookup"><span data-stu-id="ead75-144">Any technique can be used to assign a culture if the culture is persisted in a localization cookie.</span></span> <span data-ttu-id="ead75-145">Jeśli aplikacja ma już ustalony schemat lokalizacji dla ASP.NET Core po stronie serwera, Kontynuuj korzystanie z istniejącej infrastruktury lokalizacji aplikacji i ustaw kulturę lokalizacji cookie w schemacie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ead75-145">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture cookie within the app's scheme.</span></span>

<span data-ttu-id="ead75-146">Poniższy przykład pokazuje, jak ustawić bieżącą kulturę cookie , która może być odczytana przez oprogramowanie pośredniczące lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="ead75-146">The following example shows how to set the current culture in a cookie that can be read by the Localization Middleware.</span></span> <span data-ttu-id="ead75-147">Utwórz Razor wyrażenie w `Pages/_Host.cshtml` pliku bezpośrednio wewnątrz tagu otwierającego `<body>` :</span><span class="sxs-lookup"><span data-stu-id="ead75-147">Create a Razor expression in the `Pages/_Host.cshtml` file immediately inside the opening `<body>` tag:</span></span>

```cshtml
@using System.Globalization
@using Microsoft.AspNetCore.Localization

...

<body>
    @{
        this.HttpContext.Response.Cookies.Append(
            CookieRequestCultureProvider.DefaultCookieName,
            CookieRequestCultureProvider.MakeCookieValue(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }

    ...
</body>
```

<span data-ttu-id="ead75-148">Lokalizacja jest obsługiwana przez aplikację w następującej kolejności zdarzeń:</span><span class="sxs-lookup"><span data-stu-id="ead75-148">Localization is handled by the app in the following sequence of events:</span></span>

1. <span data-ttu-id="ead75-149">Przeglądarka wysyła początkowe żądanie HTTP do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ead75-149">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="ead75-150">Kultura jest przypisana przez oprogramowanie pośredniczące lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="ead75-150">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="ead75-151">RazorWyrażenie na `_Host` stronie ( `_Host.cshtml` ) utrwala kulturę w cookie jako część odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="ead75-151">The Razor expression in the `_Host` page (`_Host.cshtml`) persists the culture in a cookie as part of the response.</span></span>
1. <span data-ttu-id="ead75-152">Przeglądarka otwiera połączenie WebSocket, aby utworzyć sesję interaktywną Blazor Server .</span><span class="sxs-lookup"><span data-stu-id="ead75-152">The browser opens a WebSocket connection to create an interactive Blazor Server session.</span></span>
1. <span data-ttu-id="ead75-153">Oprogramowanie pośredniczące lokalizacji odczytuje cookie i przypisuje kulturę.</span><span class="sxs-lookup"><span data-stu-id="ead75-153">The Localization Middleware reads the cookie and assigns the culture.</span></span>
1. <span data-ttu-id="ead75-154">Blazor ServerSesja rozpoczyna się od poprawnej kultury.</span><span class="sxs-lookup"><span data-stu-id="ead75-154">The Blazor Server session begins with the correct culture.</span></span>

#### <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="ead75-155">Podaj interfejs użytkownika, aby wybrać kulturę</span><span class="sxs-lookup"><span data-stu-id="ead75-155">Provide UI to choose the culture</span></span>

<span data-ttu-id="ead75-156">Aby zapewnić interfejs użytkownika, aby umożliwić użytkownikowi wybranie kultury, zalecane jest *podejście oparte na przekierowaniu* .</span><span class="sxs-lookup"><span data-stu-id="ead75-156">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="ead75-157">Ten proces jest podobny do tego, co się dzieje w aplikacji sieci Web, gdy użytkownik próbuje uzyskać dostęp do bezpiecznego zasobu.</span><span class="sxs-lookup"><span data-stu-id="ead75-157">The process is similar to what happens in a web app when a user attempts to access a secure resource.</span></span> <span data-ttu-id="ead75-158">Użytkownik zostanie przekierowany do strony logowania, a następnie przekierowany z powrotem do oryginalnego zasobu.</span><span class="sxs-lookup"><span data-stu-id="ead75-158">The user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="ead75-159">Aplikacja utrzymuje wybraną kulturę użytkownika za pośrednictwem przekierowania do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="ead75-159">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="ead75-160">Kontroler ustawia wybraną kulturę użytkownika na cookie i przekierowuje użytkownika z powrotem do oryginalnego identyfikatora URI.</span><span class="sxs-lookup"><span data-stu-id="ead75-160">The controller sets the user's selected culture into a cookie and redirects the user back to the original URI.</span></span>

<span data-ttu-id="ead75-161">Ustanów punkt końcowy HTTP na serwerze, aby ustawić kulturę wybraną przez użytkownika cookie i przekierować z powrotem do oryginalnego identyfikatora URI:</span><span class="sxs-lookup"><span data-stu-id="ead75-161">Establish an HTTP endpoint on the server to set the user's selected culture in a cookie and perform the redirect back to the original URI:</span></span>

```csharp
[Route("[controller]/[action]")]
public class CultureController : Controller
{
    public IActionResult SetCulture(string culture, string redirectUri)
    {
        if (culture != null)
        {
            HttpContext.Response.Cookies.Append(
                CookieRequestCultureProvider.DefaultCookieName,
                CookieRequestCultureProvider.MakeCookieValue(
                    new RequestCulture(culture)));
        }

        return LocalRedirect(redirectUri);
    }
}
```

> [!WARNING]
> <span data-ttu-id="ead75-162">Użyj <xref:Microsoft.AspNetCore.Mvc.ControllerBase.LocalRedirect%2A> wyniku działania, aby zapobiec atakom typu Open redirect.</span><span class="sxs-lookup"><span data-stu-id="ead75-162">Use the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.LocalRedirect%2A> action result to prevent open redirect attacks.</span></span> <span data-ttu-id="ead75-163">Aby uzyskać więcej informacji, zobacz <xref:security/preventing-open-redirects>.</span><span class="sxs-lookup"><span data-stu-id="ead75-163">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="ead75-164">Jeśli aplikacja nie jest skonfigurowana do przetwarzania akcji kontrolera:</span><span class="sxs-lookup"><span data-stu-id="ead75-164">If the app isn't configured to process controller actions:</span></span>

* <span data-ttu-id="ead75-165">Dodaj usługi MVC do kolekcji usług w `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="ead75-165">Add MVC services to the service collection in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddControllers();
  ```

* <span data-ttu-id="ead75-166">Dodaj Routing punktu końcowego kontrolera w `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="ead75-166">Add controller endpoint routing in `Startup.Configure`:</span></span>

  ```csharp
  app.UseEndpoints(endpoints =>
  {
      endpoints.MapControllers();
      endpoints.MapBlazorHub();
      endpoints.MapFallbackToPage("/_Host");
  });
  ```

<span data-ttu-id="ead75-167">Poniższy składnik przedstawia przykład sposobu wykonywania wstępnego przekierowania, gdy użytkownik wybierze kulturę:</span><span class="sxs-lookup"><span data-stu-id="ead75-167">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

```razor
@inject NavigationManager NavigationManager

<h3>Select your language</h3>

<select @onchange="OnSelected">
    <option>Select...</option>
    <option value="en-US">English</option>
    <option value="fr-FR">Français</option>
</select>

@code {
    private void OnSelected(ChangeEventArgs e)
    {
        var culture = (string)e.Value;
        var uri = new Uri(NavigationManager.Uri)
            .GetComponents(UriComponents.PathAndQuery, UriFormat.Unescaped);
        var query = $"?culture={Uri.EscapeDataString(culture)}&" +
            $"redirectUri={Uri.EscapeDataString(uri)}";

        NavigationManager.NavigateTo("/Culture/SetCulture" + query, forceLoad: true);
    }
}
```

## <a name="additional-resources"></a><span data-ttu-id="ead75-168">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="ead75-168">Additional resources</span></span>

* <xref:fundamentals/localization>
