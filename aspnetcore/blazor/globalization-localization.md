---
title: ASP.NET Podstawowa Blazor globalizacja i lokalizacja
author: guardrex
description: Dowiedz się, jak sprawić, by składniki Razor były dostępne dla użytkowników w wielu kulturach i językach.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
no-loc:
- Blazor
- SignalR
uid: blazor/globalization-localization
ms.openlocfilehash: 0883a67e0129590f7a3fb68689eaba8d85e5523f
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440717"
---
# <a name="aspnet-core-opno-locblazor-globalization-and-localization"></a>ASP.NET Podstawowa Blazor globalizacja i lokalizacja

Autorstwa [Luke'a Lathama](https://github.com/guardrex) i [Daniela Rotha](https://github.com/danroth27)

Składniki maszynki do golenia mogą być dostępne dla użytkowników w wielu kulturach i językach. Dostępne są następujące scenariusze globalizacji i lokalizacji platformy .NET:

* . System zasobów NET
* Formatowanie liczb i dat specyficznych dla kultury

Obecnie obsługiwany jest ograniczony zestaw scenariuszy lokalizacji ASP.NET Core:

* `IStringLocalizer<>`*jest obsługiwana* w Blazor aplikacjach.
* `IHtmlLocalizer<>`, `IViewLocalizer<>`i lokalizacja adnotacji danych są ASP.NET scenariuszy Core MVC Blazor i nie są **obsługiwane** w aplikacjach.

Aby uzyskać więcej informacji, zobacz <xref:fundamentals/localization>.

## <a name="globalization"></a>Globalizacja

Blazor's `@bind` funkcje wykonuje formaty i analizuje wartości wyświetlania na podstawie bieżącej kultury użytkownika.

Bieżąca kultura jest dostępna <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> z właściwości.

[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) jest używany dla następujących`<input type="{TYPE}" />`typów pól ( ):

* `date`
* `number`

Poprzednie typy pól:

* Są wyświetlane przy użyciu odpowiednich reguł formatowania opartego na przeglądarce.
* Nie może zawierać tekstu w postaci swobodnej.
* Podaj charakterystykę interakcji użytkownika na podstawie implementacji przeglądarki.

Następujące typy pól mają określone wymagania dotyczące formatowania i Blazor nie są obecnie obsługiwane, ponieważ nie są obsługiwane przez wszystkie główne przeglądarki:

* `datetime-local`
* `month`
* `week`

`@bind`obsługuje `@bind:culture` parametr, aby <xref:System.Globalization.CultureInfo?displayProperty=fullName> zapewnić analizowanie i formatowanie wartości. Określanie kultury nie jest zalecane `date` podczas `number` korzystania z typów pól i. `date`i `number` mają wbudowaną Blazor obsługę, która zapewnia wymaganą kulturę.

## <a name="localization"></a>Lokalizacja

### <a name="opno-locblazor-webassembly"></a>BlazorWebAssembly (WebAssembly)

Domyślnie Blazorkonfiguracja konsolidatora webassembly Blazor usuwa informacje o internacjonalizacji, z wyjątkiem ustawień regionalnych jawnie żądane. Aby uzyskać więcej informacji i wskazówek dotyczących kontrolowania <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>zachowania konsolidatora, zobacz .

<!-- HOLD FOR 3.2 PREVIEW 4: Replace prior paragraph with ...

Blazor WebAssembly apps set the culture using the user's [language preference](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages).

To explicitly configure the culture, set `CultureInfo.DefaultThreadCurrentCulture` and `CultureInfo.DefaultThreadCurrentUICulture` in `Program.Main`.

By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested. For more information and guidance on controlling the linker's behavior, see <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.

While the culture that Blazor selects by default might be sufficient for most users, consider offering a way for users to specify their preferred locale. For a Blazor WebAssembly sample app with a culture picker, see the [LocSample](https://github.com/pranavkm/LocSample) localization sample app.

-->

### <a name="opno-locblazor-server"></a>BlazorSerwera

BlazorAplikacje serwera są zlokalizowane przy użyciu [oprogramowania pośredniczącego lokalizacji](xref:fundamentals/localization#localization-middleware). Oprogramowanie pośredniczące wybiera odpowiednią kulturę dla użytkowników żądających zasobów z aplikacji.

Kultury można ustawić przy użyciu jednego z następujących metod:

* [Plik cookie](#cookies)
* [Udostępnij interfejs użytkownika, aby wybrać kulturę](#provide-ui-to-choose-the-culture)

Aby uzyskać więcej informacji <xref:fundamentals/localization>i przykładów, zobacz .

#### <a name="cookies"></a>Pliki cookie

Plik cookie kultury lokalizacji może utrwalać kulturę użytkownika. Plik cookie jest `OnGet` tworzony metodą strony hosta aplikacji (*Pages/Host.cshtml.cs*). Oprogramowanie pośredniczące lokalizacji odczytuje plik cookie na kolejne żądania, aby ustawić kulturę użytkownika. 

Użycie pliku cookie gwarantuje, że połączenie WebSocket może poprawnie propagować kulturę. Jeśli schematy lokalizacji są oparte na ścieżce adresu URL lub ciągu kwerendy, schemat może nie być w stanie pracować z WebSockets, w związku z czym nie można utrwalić kultury. W związku z tym użycie pliku cookie kultury lokalizacji jest zalecane podejście.

Dowolna technika może służyć do przypisywania kultury, jeśli kultura jest utrwalona w pliku cookie lokalizacji. Jeśli aplikacja ma już ustalony schemat lokalizacji dla ASP.NET Core po stronie serwera, nadal używaj istniejącej infrastruktury lokalizacyjnej aplikacji i ustaw plik cookie kultury lokalizacji w schemacie aplikacji.

W poniższym przykładzie pokazano, jak ustawić bieżącą kulturę w pliku cookie, który może być odczytywany przez oprogramowanie pośredniczące lokalizacji. Utwórz plik *Pages/_Host.cshtml.cs* z następującą Blazor zawartością w aplikacji Serwer:

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

Lokalizacja jest obsługiwana przez aplikację w następującej sekwencji zdarzeń:

1. Przeglądarka wysyła początkowe żądanie HTTP do aplikacji.
1. Kultura jest przypisywana przez oprogramowanie pośredniczące lokalizacji.
1. Metoda `OnGet` w *_Host.cshtml.cs* utrzymuje kultury w pliku cookie jako część odpowiedzi.
1. Przeglądarka otwiera połączenie WebSocket w Blazor celu utworzenia interaktywnej sesji serwera.
1. Oprogramowanie pośredniczące lokalizacji odczytuje plik cookie i przypisuje kulturę.
1. Sesja Blazor serwera rozpoczyna się od poprawnej kultury.

#### <a name="provide-ui-to-choose-the-culture"></a>Udostępnij interfejs użytkownika, aby wybrać kulturę

Aby zapewnić interfejs użytkownika, aby umożliwić użytkownikowi wybranie kultury, *zaleca się podejście oparte na przekierowanie.* Proces jest podobny do tego, co dzieje się w aplikacji sieci web, gdy użytkownik próbuje uzyskać dostęp do bezpiecznego zasobu. Użytkownik jest przekierowywał do strony logowania, a następnie przekierowywał z powrotem do oryginalnego zasobu. 

Aplikacja utrzymuje wybranej kultury użytkownika za pośrednictwem przekierowania do kontrolera. Kontroler ustawia wybraną kulturę użytkownika w plik cookie i przekierowuje użytkownika z powrotem do oryginalnego identyfikatora URI.

Ustanowienie punktu końcowego HTTP na serwerze, aby ustawić wybraną kulturę użytkownika w pliku cookie i wykonać przekierowanie z powrotem do oryginalnego identyfikatora URI:

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
> Użyj `LocalRedirect` wyniku akcji, aby zapobiec otwartym atakom przekierowania. Aby uzyskać więcej informacji, zobacz <xref:security/preventing-open-redirects>.

Następujący składnik przedstawia przykład wykonywania początkowego przekierowania, gdy użytkownik wybierze kulturę:

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

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:fundamentals/localization>
