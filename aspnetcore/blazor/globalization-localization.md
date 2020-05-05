---
title: ASP.NET Core Blazor globalizacja i lokalizacja
author: guardrex
description: Dowiedz się, Razor jak udostępnić składniki użytkownikom w wielu kulturach i językach.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/globalization-localization
ms.openlocfilehash: b39695f8b506744b4af27a1d7e09bfac9594d7ca
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82772494"
---
# <a name="aspnet-core-blazor-globalization-and-localization"></a><span data-ttu-id="802bc-103">ASP.NET Core Blazor globalizacja i lokalizacja</span><span class="sxs-lookup"><span data-stu-id="802bc-103">ASP.NET Core Blazor globalization and localization</span></span>

<span data-ttu-id="802bc-104">Autorzy [Luke Latham](https://github.com/guardrex) i [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="802bc-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

Razor<span data-ttu-id="802bc-105">składniki mogą być udostępniane użytkownikom w wielu kulturach i językach.</span><span class="sxs-lookup"><span data-stu-id="802bc-105"> components can be made accessible to users in multiple cultures and languages.</span></span> <span data-ttu-id="802bc-106">Dostępne są następujące scenariusze globalizacji i lokalizacji platformy .NET:</span><span class="sxs-lookup"><span data-stu-id="802bc-106">The following .NET globalization and localization scenarios are available:</span></span>

* <span data-ttu-id="802bc-107">. System zasobów netto</span><span class="sxs-lookup"><span data-stu-id="802bc-107">.NET's resources system</span></span>
* <span data-ttu-id="802bc-108">Formatowanie liczb i dat specyficznych dla kultury</span><span class="sxs-lookup"><span data-stu-id="802bc-108">Culture-specific number and date formatting</span></span>

<span data-ttu-id="802bc-109">Obecnie obsługiwane są ograniczone zestawy ASP.NET Core scenariuszy lokalizacji:</span><span class="sxs-lookup"><span data-stu-id="802bc-109">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="802bc-110">`IStringLocalizer<>`*jest obsługiwany* w Blazor aplikacjach.</span><span class="sxs-lookup"><span data-stu-id="802bc-110">`IStringLocalizer<>` *is supported* in Blazor apps.</span></span>
* <span data-ttu-id="802bc-111">`IHtmlLocalizer<>`Lokalizacje adnotacji danych są ASP.NET Core w scenariuszach MVC i **nie są obsługiwane** w Blazor aplikacjach. `IViewLocalizer<>`</span><span class="sxs-lookup"><span data-stu-id="802bc-111">`IHtmlLocalizer<>`, `IViewLocalizer<>`, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in Blazor apps.</span></span>

<span data-ttu-id="802bc-112">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="802bc-112">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="globalization"></a><span data-ttu-id="802bc-113">Globalizacja</span><span class="sxs-lookup"><span data-stu-id="802bc-113">Globalization</span></span>

Blazor<span data-ttu-id="802bc-114">`@bind` funkcja wykonuje formatowanie i analizuje wartości na potrzeby wyświetlania w oparciu o bieżącą kulturę użytkownika.</span><span class="sxs-lookup"><span data-stu-id="802bc-114">'s `@bind` functionality performs formats and parses values for display based on the user's current culture.</span></span>

<span data-ttu-id="802bc-115">Dla bieżącej kultury można uzyskać dostęp z <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> właściwości.</span><span class="sxs-lookup"><span data-stu-id="802bc-115">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="802bc-116">[CultureInfo. InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) jest używany dla następujących typów pól (`<input type="{TYPE}" />`):</span><span class="sxs-lookup"><span data-stu-id="802bc-116">[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="802bc-117">Poprzednie typy pól:</span><span class="sxs-lookup"><span data-stu-id="802bc-117">The preceding field types:</span></span>

* <span data-ttu-id="802bc-118">Są wyświetlane przy użyciu odpowiednich reguł formatowania opartych na przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="802bc-118">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="802bc-119">Nie może zawierać tekstu o dowolnym formacie.</span><span class="sxs-lookup"><span data-stu-id="802bc-119">Can't contain free-form text.</span></span>
* <span data-ttu-id="802bc-120">Podaj charakterystykę interakcji użytkownika w oparciu o implementację przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="802bc-120">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="802bc-121">Następujące typy pól mają określone wymagania dotyczące formatowania i nie są obecnie obsługiwane Blazor przez program, ponieważ nie są obsługiwane przez wszystkie główne przeglądarki:</span><span class="sxs-lookup"><span data-stu-id="802bc-121">The following field types have specific formatting requirements and aren't currently supported by Blazor because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="802bc-122">`@bind`obsługuje `@bind:culture` parametr, aby zapewnić <xref:System.Globalization.CultureInfo?displayProperty=fullName> analizę i formatowanie wartości.</span><span class="sxs-lookup"><span data-stu-id="802bc-122">`@bind` supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="802bc-123">Określanie kultury nie jest zalecane w `date` przypadku używania typów `number` pól i.</span><span class="sxs-lookup"><span data-stu-id="802bc-123">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="802bc-124">`date`i `number` ma wbudowaną Blazor obsługę, która udostępnia wymaganą kulturę.</span><span class="sxs-lookup"><span data-stu-id="802bc-124">`date` and `number` have built-in Blazor support that provides the required culture.</span></span>

## <a name="localization"></a><span data-ttu-id="802bc-125">Lokalizacja</span><span class="sxs-lookup"><span data-stu-id="802bc-125">Localization</span></span>

### <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="802bc-126">Zestaw webassembly</span><span class="sxs-lookup"><span data-stu-id="802bc-126"> WebAssembly</span></span>

Blazor<span data-ttu-id="802bc-127">Aplikacje webassembly ustawiają kulturę przy użyciu [preferencji języka](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages)użytkownika.</span><span class="sxs-lookup"><span data-stu-id="802bc-127"> WebAssembly apps set the culture using the user's [language preference](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages).</span></span>

<span data-ttu-id="802bc-128">Aby jawnie skonfigurować kulturę, ustaw `CultureInfo.DefaultThreadCurrentCulture` i `CultureInfo.DefaultThreadCurrentUICulture` w `Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="802bc-128">To explicitly configure the culture, set `CultureInfo.DefaultThreadCurrentCulture` and `CultureInfo.DefaultThreadCurrentUICulture` in `Program.Main`.</span></span>

<span data-ttu-id="802bc-129">Domyślnie Blazorkonfiguracja konsolidatora dla aplikacji webassembly umożliwia Blazor rozłączenie informacji o danych wielojęzycznych z wyjątkiem lokalizacji lokalnych jawnie żądanych.</span><span class="sxs-lookup"><span data-stu-id="802bc-129">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="802bc-130">Aby uzyskać więcej informacji i wskazówek dotyczących kontrolowania zachowania konsolidatora, zobacz <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.</span><span class="sxs-lookup"><span data-stu-id="802bc-130">For more information and guidance on controlling the linker's behavior, see <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.</span></span>

<span data-ttu-id="802bc-131">Chociaż kultura, która Blazor wybiera domyślnie, może być wystarczająca dla większości użytkowników, należy rozważyć umożliwienie użytkownikom określenia ich preferowanych ustawień regionalnych.</span><span class="sxs-lookup"><span data-stu-id="802bc-131">While the culture that Blazor selects by default might be sufficient for most users, consider offering a way for users to specify their preferred locale.</span></span> <span data-ttu-id="802bc-132">Blazor Aby zapoznać się z przykładową aplikacją webassembly z selektorem kultury, zobacz przykładową aplikację [LocSample](https://github.com/pranavkm/LocSample) lokalizacyjną.</span><span class="sxs-lookup"><span data-stu-id="802bc-132">For a Blazor WebAssembly sample app with a culture picker, see the [LocSample](https://github.com/pranavkm/LocSample) localization sample app.</span></span>

### <a name="blazor-server"></a>Blazor<span data-ttu-id="802bc-133">Server</span><span class="sxs-lookup"><span data-stu-id="802bc-133"> Server</span></span>

Blazor<span data-ttu-id="802bc-134">Aplikacje serwera są zlokalizowane przy użyciu [oprogramowania pośredniczącego](xref:fundamentals/localization#localization-middleware).</span><span class="sxs-lookup"><span data-stu-id="802bc-134"> Server apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="802bc-135">Oprogramowanie pośredniczące wybiera odpowiednią kulturę dla użytkowników żądających zasobów z aplikacji.</span><span class="sxs-lookup"><span data-stu-id="802bc-135">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="802bc-136">Kulturę można ustawić przy użyciu jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="802bc-136">The culture can be set using one of the following approaches:</span></span>

* [<span data-ttu-id="802bc-137">Plik cookie</span><span class="sxs-lookup"><span data-stu-id="802bc-137">Cookies</span></span>](#cookies)
* [<span data-ttu-id="802bc-138">Podaj interfejs użytkownika, aby wybrać kulturę</span><span class="sxs-lookup"><span data-stu-id="802bc-138">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="802bc-139">Aby uzyskać więcej informacji i przykładów, <xref:fundamentals/localization>Zobacz.</span><span class="sxs-lookup"><span data-stu-id="802bc-139">For more information and examples, see <xref:fundamentals/localization>.</span></span>

#### <a name="cookies"></a><span data-ttu-id="802bc-140">Pliki cookie</span><span class="sxs-lookup"><span data-stu-id="802bc-140">Cookies</span></span>

<span data-ttu-id="802bc-141">Plik cookie kultury lokalizacji może utrzymywać kulturę użytkownika.</span><span class="sxs-lookup"><span data-stu-id="802bc-141">A localization culture cookie can persist the user's culture.</span></span> <span data-ttu-id="802bc-142">Plik cookie jest tworzony przez `OnGet` metodę strony hosta aplikacji (*strony/host. cshtml. cs*).</span><span class="sxs-lookup"><span data-stu-id="802bc-142">The cookie is created by the `OnGet` method of the app's host page (*Pages/Host.cshtml.cs*).</span></span> <span data-ttu-id="802bc-143">Oprogramowanie pośredniczące lokalizacji odczytuje plik cookie na kolejnych żądaniach, aby ustawić kulturę użytkownika.</span><span class="sxs-lookup"><span data-stu-id="802bc-143">The Localization Middleware reads the cookie on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="802bc-144">Użycie pliku cookie zapewnia, że połączenie z użyciem protokołu WebSocket może prawidłowo propagować kulturę.</span><span class="sxs-lookup"><span data-stu-id="802bc-144">Use of a cookie ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="802bc-145">Jeśli schematy lokalizacji są oparte na ścieżce URL lub ciągu zapytania, schemat może nie być w stanie współdziałać z usługą WebSockets, więc nie będzie można zachować kultury.</span><span class="sxs-lookup"><span data-stu-id="802bc-145">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="802bc-146">W związku z tym zalecanym podejściem jest użycie pliku cookie kultury lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="802bc-146">Therefore, use of a localization culture cookie is the recommended approach.</span></span>

<span data-ttu-id="802bc-147">Każda technika może służyć do przypisywania kultury, jeśli kultura jest utrwalona w pliku cookie lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="802bc-147">Any technique can be used to assign a culture if the culture is persisted in a localization cookie.</span></span> <span data-ttu-id="802bc-148">Jeśli aplikacja ma już ustalony schemat lokalizacji dla ASP.NET Core po stronie serwera, Kontynuuj korzystanie z istniejącej infrastruktury lokalizacji aplikacji i Ustaw plik cookie kultury lokalizacji w schemacie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="802bc-148">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture cookie within the app's scheme.</span></span>

<span data-ttu-id="802bc-149">Poniższy przykład pokazuje, jak ustawić bieżącą kulturę w pliku cookie, który może zostać odczytany przez oprogramowanie pośredniczące lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="802bc-149">The following example shows how to set the current culture in a cookie that can be read by the Localization Middleware.</span></span> <span data-ttu-id="802bc-150">Utwórz plik *Pages/_Host. cshtml. cs* z następującą zawartością w aplikacji Blazor serwerowej:</span><span class="sxs-lookup"><span data-stu-id="802bc-150">Create a *Pages/_Host.cshtml.cs* file with the following contents in the Blazor Server app:</span></span>

```csharp
public class HostModel : PageModel
{
    public void OnGet()
    {
        HttpContext.Response.Cookies.Append(
            CookieRequestCultureProvider.DefaultCookieName,
            CookieRequestCultureProvider.MakeCookieValue(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }
}
```

<span data-ttu-id="802bc-151">Lokalizacja jest obsługiwana przez aplikację w następującej kolejności zdarzeń:</span><span class="sxs-lookup"><span data-stu-id="802bc-151">Localization is handled by the app in the following sequence of events:</span></span>

1. <span data-ttu-id="802bc-152">Przeglądarka wysyła początkowe żądanie HTTP do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="802bc-152">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="802bc-153">Kultura jest przypisana przez oprogramowanie pośredniczące lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="802bc-153">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="802bc-154">`OnGet` Metoda w *_Host. cshtml. cs* utrzymuje kulturę w pliku cookie jako część odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="802bc-154">The `OnGet` method in *_Host.cshtml.cs* persists the culture in a cookie as part of the response.</span></span>
1. <span data-ttu-id="802bc-155">Przeglądarka otwiera połączenie WebSocket, aby utworzyć interaktywną Blazor sesję serwera.</span><span class="sxs-lookup"><span data-stu-id="802bc-155">The browser opens a WebSocket connection to create an interactive Blazor Server session.</span></span>
1. <span data-ttu-id="802bc-156">Oprogramowanie pośredniczące lokalizacji odczytuje plik cookie i przypisuje kulturę.</span><span class="sxs-lookup"><span data-stu-id="802bc-156">The Localization Middleware reads the cookie and assigns the culture.</span></span>
1. <span data-ttu-id="802bc-157">Sesja Blazor serwera rozpoczyna się od poprawnej kultury.</span><span class="sxs-lookup"><span data-stu-id="802bc-157">The Blazor Server session begins with the correct culture.</span></span>

#### <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="802bc-158">Podaj interfejs użytkownika, aby wybrać kulturę</span><span class="sxs-lookup"><span data-stu-id="802bc-158">Provide UI to choose the culture</span></span>

<span data-ttu-id="802bc-159">Aby zapewnić interfejs użytkownika, aby umożliwić użytkownikowi wybranie kultury, zalecane jest *podejście oparte na przekierowaniu* .</span><span class="sxs-lookup"><span data-stu-id="802bc-159">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="802bc-160">Ten proces jest podobny do tego, co się dzieje w aplikacji sieci Web, gdy użytkownik próbuje uzyskać dostęp do bezpiecznego zasobu.</span><span class="sxs-lookup"><span data-stu-id="802bc-160">The process is similar to what happens in a web app when a user attempts to access a secure resource.</span></span> <span data-ttu-id="802bc-161">Użytkownik zostanie przekierowany do strony logowania, a następnie przekierowany z powrotem do oryginalnego zasobu.</span><span class="sxs-lookup"><span data-stu-id="802bc-161">The user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="802bc-162">Aplikacja utrzymuje wybraną kulturę użytkownika za pośrednictwem przekierowania do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="802bc-162">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="802bc-163">Kontroler ustawia wybraną kulturę użytkownika na plik cookie i przekierowuje użytkownika z powrotem do oryginalnego identyfikatora URI.</span><span class="sxs-lookup"><span data-stu-id="802bc-163">The controller sets the user's selected culture into a cookie and redirects the user back to the original URI.</span></span>

<span data-ttu-id="802bc-164">Ustanów punkt końcowy HTTP na serwerze, aby ustawić kulturę wybraną przez użytkownika w pliku cookie i wykonać przekierowanie z powrotem do oryginalnego identyfikatora URI:</span><span class="sxs-lookup"><span data-stu-id="802bc-164">Establish an HTTP endpoint on the server to set the user's selected culture in a cookie and perform the redirect back to the original URI:</span></span>

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
> <span data-ttu-id="802bc-165">Użyj wyniku `LocalRedirect` działania, aby zapobiec atakom typu Open redirect.</span><span class="sxs-lookup"><span data-stu-id="802bc-165">Use the `LocalRedirect` action result to prevent open redirect attacks.</span></span> <span data-ttu-id="802bc-166">Aby uzyskać więcej informacji, zobacz <xref:security/preventing-open-redirects>.</span><span class="sxs-lookup"><span data-stu-id="802bc-166">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="802bc-167">Poniższy składnik przedstawia przykład sposobu wykonywania wstępnego przekierowania, gdy użytkownik wybierze kulturę:</span><span class="sxs-lookup"><span data-stu-id="802bc-167">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="802bc-168">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="802bc-168">Additional resources</span></span>

* <xref:fundamentals/localization>
