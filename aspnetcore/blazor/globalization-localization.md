---
title: ASP.NET Podstawowa Blazor globalizacja i lokalizacja
author: guardrex
description: Dowiedz się, jak sprawić, by składniki Razor były dostępne dla użytkowników w wielu kulturach i językach.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2020
no-loc:
- Blazor
- SignalR
uid: blazor/globalization-localization
ms.openlocfilehash: 2795b21f418ccb2722e7fd0e76a77cfde58e524a
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80976745"
---
# <a name="aspnet-core-opno-locblazor-globalization-and-localization"></a><span data-ttu-id="b530d-103">ASP.NET Podstawowa Blazor globalizacja i lokalizacja</span><span class="sxs-lookup"><span data-stu-id="b530d-103">ASP.NET Core Blazor globalization and localization</span></span>

<span data-ttu-id="b530d-104">Autorstwa [Luke'a Lathama](https://github.com/guardrex) i [Daniela Rotha](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="b530d-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="b530d-105">Składniki maszynki do golenia mogą być dostępne dla użytkowników w wielu kulturach i językach.</span><span class="sxs-lookup"><span data-stu-id="b530d-105">Razor components can be made accessible to users in multiple cultures and languages.</span></span> <span data-ttu-id="b530d-106">Dostępne są następujące scenariusze globalizacji i lokalizacji platformy .NET:</span><span class="sxs-lookup"><span data-stu-id="b530d-106">The following .NET globalization and localization scenarios are available:</span></span>

* <span data-ttu-id="b530d-107">. System zasobów NET</span><span class="sxs-lookup"><span data-stu-id="b530d-107">.NET's resources system</span></span>
* <span data-ttu-id="b530d-108">Formatowanie liczb i dat specyficznych dla kultury</span><span class="sxs-lookup"><span data-stu-id="b530d-108">Culture-specific number and date formatting</span></span>

<span data-ttu-id="b530d-109">Obecnie obsługiwany jest ograniczony zestaw scenariuszy lokalizacji ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="b530d-109">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="b530d-110">`IStringLocalizer<>`*jest obsługiwana* w Blazor aplikacjach.</span><span class="sxs-lookup"><span data-stu-id="b530d-110">`IStringLocalizer<>` *is supported* in Blazor apps.</span></span>
* <span data-ttu-id="b530d-111">`IHtmlLocalizer<>`, `IViewLocalizer<>`i lokalizacja adnotacji danych są ASP.NET scenariuszy Core MVC Blazor i nie są **obsługiwane** w aplikacjach.</span><span class="sxs-lookup"><span data-stu-id="b530d-111">`IHtmlLocalizer<>`, `IViewLocalizer<>`, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in Blazor apps.</span></span>

<span data-ttu-id="b530d-112">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="b530d-112">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="globalization"></a><span data-ttu-id="b530d-113">Globalizacja</span><span class="sxs-lookup"><span data-stu-id="b530d-113">Globalization</span></span>

Blazor<span data-ttu-id="b530d-114">'s `@bind` funkcje wykonuje formaty i analizuje wartości wyświetlania na podstawie bieżącej kultury użytkownika.</span><span class="sxs-lookup"><span data-stu-id="b530d-114">'s `@bind` functionality performs formats and parses values for display based on the user's current culture.</span></span>

<span data-ttu-id="b530d-115">Bieżąca kultura jest dostępna <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> z właściwości.</span><span class="sxs-lookup"><span data-stu-id="b530d-115">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="b530d-116">[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) jest używany dla następujących`<input type="{TYPE}" />`typów pól ( ):</span><span class="sxs-lookup"><span data-stu-id="b530d-116">[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="b530d-117">Poprzednie typy pól:</span><span class="sxs-lookup"><span data-stu-id="b530d-117">The preceding field types:</span></span>

* <span data-ttu-id="b530d-118">Są wyświetlane przy użyciu odpowiednich reguł formatowania opartego na przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="b530d-118">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="b530d-119">Nie może zawierać tekstu w postaci swobodnej.</span><span class="sxs-lookup"><span data-stu-id="b530d-119">Can't contain free-form text.</span></span>
* <span data-ttu-id="b530d-120">Podaj charakterystykę interakcji użytkownika na podstawie implementacji przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="b530d-120">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="b530d-121">Następujące typy pól mają określone wymagania dotyczące formatowania i Blazor nie są obecnie obsługiwane, ponieważ nie są obsługiwane przez wszystkie główne przeglądarki:</span><span class="sxs-lookup"><span data-stu-id="b530d-121">The following field types have specific formatting requirements and aren't currently supported by Blazor because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="b530d-122">`@bind`obsługuje `@bind:culture` parametr, aby <xref:System.Globalization.CultureInfo?displayProperty=fullName> zapewnić analizowanie i formatowanie wartości.</span><span class="sxs-lookup"><span data-stu-id="b530d-122">`@bind` supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="b530d-123">Określanie kultury nie jest zalecane `date` podczas `number` korzystania z typów pól i.</span><span class="sxs-lookup"><span data-stu-id="b530d-123">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="b530d-124">`date`i `number` mają wbudowaną Blazor obsługę, która zapewnia wymaganą kulturę.</span><span class="sxs-lookup"><span data-stu-id="b530d-124">`date` and `number` have built-in Blazor support that provides the required culture.</span></span>

## <a name="localization"></a><span data-ttu-id="b530d-125">Lokalizacja</span><span class="sxs-lookup"><span data-stu-id="b530d-125">Localization</span></span>

### <a name="opno-locblazor-webassembly"></a>Blazor<span data-ttu-id="b530d-126">WebAssembly (WebAssembly)</span><span class="sxs-lookup"><span data-stu-id="b530d-126"> WebAssembly</span></span>

Blazor<span data-ttu-id="b530d-127">Aplikacje WebAssembly ustawiają kulturę przy użyciu [preferencji językowych](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages)użytkownika .</span><span class="sxs-lookup"><span data-stu-id="b530d-127"> WebAssembly apps set the culture using the user's [language preference](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages).</span></span>

<span data-ttu-id="b530d-128">Aby jawnie skonfigurować kulturę, `CultureInfo.DefaultThreadCurrentUICulture` `Program.Main`ustaw `CultureInfo.DefaultThreadCurrentCulture` i w .</span><span class="sxs-lookup"><span data-stu-id="b530d-128">To explicitly configure the culture, set `CultureInfo.DefaultThreadCurrentCulture` and `CultureInfo.DefaultThreadCurrentUICulture` in `Program.Main`.</span></span>

<span data-ttu-id="b530d-129">Domyślnie Blazorkonfiguracja konsolidatora webassembly Blazor usuwa informacje o internacjonalizacji, z wyjątkiem ustawień regionalnych jawnie żądane.</span><span class="sxs-lookup"><span data-stu-id="b530d-129">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="b530d-130">Aby uzyskać więcej informacji i wskazówek dotyczących kontrolowania <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>zachowania konsolidatora, zobacz .</span><span class="sxs-lookup"><span data-stu-id="b530d-130">For more information and guidance on controlling the linker's behavior, see <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.</span></span>

<span data-ttu-id="b530d-131">Podczas gdy Blazor kultura, która wybiera domyślnie może być wystarczająca dla większości użytkowników, należy rozważyć oferowanie użytkownikom sposobu, aby określić preferowane ustawienia regionalne.</span><span class="sxs-lookup"><span data-stu-id="b530d-131">While the culture that Blazor selects by default might be sufficient for most users, consider offering a way for users to specify their preferred locale.</span></span> <span data-ttu-id="b530d-132">Aby Blazor uzyskać przykładową aplikację WebAssembly z selektorem kultury, zobacz przykładową aplikację [lokalizacji LocSample.](https://github.com/pranavkm/LocSample)</span><span class="sxs-lookup"><span data-stu-id="b530d-132">For a Blazor WebAssembly sample app with a culture picker, see the [LocSample](https://github.com/pranavkm/LocSample) localization sample app.</span></span>

### <a name="opno-locblazor-server"></a>Blazor<span data-ttu-id="b530d-133">Serwera</span><span class="sxs-lookup"><span data-stu-id="b530d-133"> Server</span></span>

Blazor<span data-ttu-id="b530d-134">Aplikacje serwera są zlokalizowane przy użyciu [oprogramowania pośredniczącego lokalizacji](xref:fundamentals/localization#localization-middleware).</span><span class="sxs-lookup"><span data-stu-id="b530d-134"> Server apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="b530d-135">Oprogramowanie pośredniczące wybiera odpowiednią kulturę dla użytkowników żądających zasobów z aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b530d-135">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="b530d-136">Kultury można ustawić przy użyciu jednego z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="b530d-136">The culture can be set using one of the following approaches:</span></span>

* [<span data-ttu-id="b530d-137">Plik cookie</span><span class="sxs-lookup"><span data-stu-id="b530d-137">Cookies</span></span>](#cookies)
* [<span data-ttu-id="b530d-138">Udostępnij interfejs użytkownika, aby wybrać kulturę</span><span class="sxs-lookup"><span data-stu-id="b530d-138">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="b530d-139">Aby uzyskać więcej informacji <xref:fundamentals/localization>i przykładów, zobacz .</span><span class="sxs-lookup"><span data-stu-id="b530d-139">For more information and examples, see <xref:fundamentals/localization>.</span></span>

#### <a name="cookies"></a><span data-ttu-id="b530d-140">Pliki cookie</span><span class="sxs-lookup"><span data-stu-id="b530d-140">Cookies</span></span>

<span data-ttu-id="b530d-141">Plik cookie kultury lokalizacji może utrwalać kulturę użytkownika.</span><span class="sxs-lookup"><span data-stu-id="b530d-141">A localization culture cookie can persist the user's culture.</span></span> <span data-ttu-id="b530d-142">Plik cookie jest `OnGet` tworzony metodą strony hosta aplikacji (*Pages/Host.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="b530d-142">The cookie is created by the `OnGet` method of the app's host page (*Pages/Host.cshtml.cs*).</span></span> <span data-ttu-id="b530d-143">Oprogramowanie pośredniczące lokalizacji odczytuje plik cookie na kolejne żądania, aby ustawić kulturę użytkownika.</span><span class="sxs-lookup"><span data-stu-id="b530d-143">The Localization Middleware reads the cookie on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="b530d-144">Użycie pliku cookie gwarantuje, że połączenie WebSocket może poprawnie propagować kulturę.</span><span class="sxs-lookup"><span data-stu-id="b530d-144">Use of a cookie ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="b530d-145">Jeśli schematy lokalizacji są oparte na ścieżce adresu URL lub ciągu kwerendy, schemat może nie być w stanie pracować z WebSockets, w związku z czym nie można utrwalić kultury.</span><span class="sxs-lookup"><span data-stu-id="b530d-145">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="b530d-146">W związku z tym użycie pliku cookie kultury lokalizacji jest zalecane podejście.</span><span class="sxs-lookup"><span data-stu-id="b530d-146">Therefore, use of a localization culture cookie is the recommended approach.</span></span>

<span data-ttu-id="b530d-147">Dowolna technika może służyć do przypisywania kultury, jeśli kultura jest utrwalona w pliku cookie lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="b530d-147">Any technique can be used to assign a culture if the culture is persisted in a localization cookie.</span></span> <span data-ttu-id="b530d-148">Jeśli aplikacja ma już ustalony schemat lokalizacji dla ASP.NET Core po stronie serwera, nadal używaj istniejącej infrastruktury lokalizacyjnej aplikacji i ustaw plik cookie kultury lokalizacji w schemacie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b530d-148">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture cookie within the app's scheme.</span></span>

<span data-ttu-id="b530d-149">W poniższym przykładzie pokazano, jak ustawić bieżącą kulturę w pliku cookie, który może być odczytywany przez oprogramowanie pośredniczące lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="b530d-149">The following example shows how to set the current culture in a cookie that can be read by the Localization Middleware.</span></span> <span data-ttu-id="b530d-150">Utwórz plik *Pages/_Host.cshtml.cs* z następującą Blazor zawartością w aplikacji Serwer:</span><span class="sxs-lookup"><span data-stu-id="b530d-150">Create a *Pages/_Host.cshtml.cs* file with the following contents in the Blazor Server app:</span></span>

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

<span data-ttu-id="b530d-151">Lokalizacja jest obsługiwana przez aplikację w następującej sekwencji zdarzeń:</span><span class="sxs-lookup"><span data-stu-id="b530d-151">Localization is handled by the app in the following sequence of events:</span></span>

1. <span data-ttu-id="b530d-152">Przeglądarka wysyła początkowe żądanie HTTP do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="b530d-152">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="b530d-153">Kultura jest przypisywana przez oprogramowanie pośredniczące lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="b530d-153">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="b530d-154">Metoda `OnGet` w *_Host.cshtml.cs* utrzymuje kultury w pliku cookie jako część odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="b530d-154">The `OnGet` method in *_Host.cshtml.cs* persists the culture in a cookie as part of the response.</span></span>
1. <span data-ttu-id="b530d-155">Przeglądarka otwiera połączenie WebSocket w Blazor celu utworzenia interaktywnej sesji serwera.</span><span class="sxs-lookup"><span data-stu-id="b530d-155">The browser opens a WebSocket connection to create an interactive Blazor Server session.</span></span>
1. <span data-ttu-id="b530d-156">Oprogramowanie pośredniczące lokalizacji odczytuje plik cookie i przypisuje kulturę.</span><span class="sxs-lookup"><span data-stu-id="b530d-156">The Localization Middleware reads the cookie and assigns the culture.</span></span>
1. <span data-ttu-id="b530d-157">Sesja Blazor serwera rozpoczyna się od poprawnej kultury.</span><span class="sxs-lookup"><span data-stu-id="b530d-157">The Blazor Server session begins with the correct culture.</span></span>

#### <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="b530d-158">Udostępnij interfejs użytkownika, aby wybrać kulturę</span><span class="sxs-lookup"><span data-stu-id="b530d-158">Provide UI to choose the culture</span></span>

<span data-ttu-id="b530d-159">Aby zapewnić interfejs użytkownika, aby umożliwić użytkownikowi wybranie kultury, *zaleca się podejście oparte na przekierowanie.*</span><span class="sxs-lookup"><span data-stu-id="b530d-159">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="b530d-160">Proces jest podobny do tego, co dzieje się w aplikacji sieci web, gdy użytkownik próbuje uzyskać dostęp do bezpiecznego zasobu.</span><span class="sxs-lookup"><span data-stu-id="b530d-160">The process is similar to what happens in a web app when a user attempts to access a secure resource.</span></span> <span data-ttu-id="b530d-161">Użytkownik jest przekierowywał do strony logowania, a następnie przekierowywał z powrotem do oryginalnego zasobu.</span><span class="sxs-lookup"><span data-stu-id="b530d-161">The user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="b530d-162">Aplikacja utrzymuje wybranej kultury użytkownika za pośrednictwem przekierowania do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="b530d-162">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="b530d-163">Kontroler ustawia wybraną kulturę użytkownika w plik cookie i przekierowuje użytkownika z powrotem do oryginalnego identyfikatora URI.</span><span class="sxs-lookup"><span data-stu-id="b530d-163">The controller sets the user's selected culture into a cookie and redirects the user back to the original URI.</span></span>

<span data-ttu-id="b530d-164">Ustanowienie punktu końcowego HTTP na serwerze, aby ustawić wybraną kulturę użytkownika w pliku cookie i wykonać przekierowanie z powrotem do oryginalnego identyfikatora URI:</span><span class="sxs-lookup"><span data-stu-id="b530d-164">Establish an HTTP endpoint on the server to set the user's selected culture in a cookie and perform the redirect back to the original URI:</span></span>

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
> <span data-ttu-id="b530d-165">Użyj `LocalRedirect` wyniku akcji, aby zapobiec otwartym atakom przekierowania.</span><span class="sxs-lookup"><span data-stu-id="b530d-165">Use the `LocalRedirect` action result to prevent open redirect attacks.</span></span> <span data-ttu-id="b530d-166">Aby uzyskać więcej informacji, zobacz <xref:security/preventing-open-redirects>.</span><span class="sxs-lookup"><span data-stu-id="b530d-166">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="b530d-167">Następujący składnik przedstawia przykład wykonywania początkowego przekierowania, gdy użytkownik wybierze kulturę:</span><span class="sxs-lookup"><span data-stu-id="b530d-167">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="b530d-168">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="b530d-168">Additional resources</span></span>

* <xref:fundamentals/localization>
