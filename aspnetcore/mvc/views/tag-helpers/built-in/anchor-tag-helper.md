---
title: Pomocnik tagu kotwicy w ASP.NET Core
author: pkellner
description: Odkryj atrybuty pomocnika ASP.NET Core znacznika i rolę, jaką każdy atrybut odgrywa w rozszerzeniu zachowania tagu kotwicy HTML.
ms.author: scaddie
ms.custom: mvc
ms.date: 10/13/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/anchor-tag-helper
ms.openlocfilehash: 142ad62bbbc25fc5390331b253a6173f064ef162
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773968"
---
# <a name="anchor-tag-helper-in-aspnet-core"></a>Pomocnik tagu kotwicy w ASP.NET Core

Według [Peterowi Kellner](https://peterkellner.net) i [Scott Addie](https://github.com/scottaddie)

[Pomocnik tagu kotwicy](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper) rozszerza standardowy tag kotwicy HTML (`<a ... ></a>`) przez dodanie nowych atrybutów. Według Konwencji nazwy atrybutów są poprzedzone prefiksem `asp-`. Wartość `href` atrybutu renderowanego elementu zakotwiczenia jest określana na podstawie wartości `asp-` atrybutów.

Aby zapoznać się z omówieniem pomocników tagów, <xref:mvc/views/tag-helpers/intro>Zobacz.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([jak pobrać](xref:index#how-to-download-a-sample))

*SpeakerController* jest używany w przykładach w tym dokumencie:

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?name=snippet_SpeakerController)]

## <a name="anchor-tag-helper-attributes"></a>Atrybuty pomocnika tagu kotwicy

### <a name="asp-controller"></a>ASP-Controller

Atrybut [kontrolera ASP](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Controller*) przypisuje kontroler używany do generowania adresu URL. Następujące znaczniki zawierają listę wszystkich głośników:

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspController)]

Wygenerowany kod HTML:

```html
<a href="/Speaker">All Speakers</a>
```

Jeśli `asp-controller` atrybut jest określony i `asp-action` nie jest, wartość domyślna `asp-action` to akcja kontrolera skojarzona z aktualnie wykonywanym widokiem. Jeśli `asp-action` zostanie pominięty z poprzedniego znacznika, a pomocnik tagu kotwicy jest używany w widoku indeksu *HomeController*( *Index* */Home*), wygenerowany kod HTML jest:

```html
<a href="/Home">All Speakers</a>
```

### <a name="asp-action"></a>ASP — akcja

Wartość atrybutu [akcji ASP](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Action*) reprezentuje nazwę akcji kontrolera zawartej w wygenerowanym `href` atrybucie. Poniższy znacznik ustawia wygenerowanej `href` wartości atrybutu na stronie oceny głośników:

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAction)]

Wygenerowany kod HTML:

```html
<a href="/Speaker/Evaluations">Speaker Evaluations</a>
```

Jeśli żaden `asp-controller` atrybut nie jest określony, używany jest domyślny kontroler wywołujący widok wykonujący bieżący widok.

Jeśli `asp-action` atrybut ma `Index`wartość, nie jest dołączany do adresu URL, co prowadzi do wywołania akcji domyślnej. `Index` Określona akcja (lub domyślna) musi istnieć na kontrolerze, do którego odwołuje się `asp-controller`w.

### <a name="asp-route-value"></a>ASP-Route-{Value}

Atrybut [ASP-Route-{Value}](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) włącza prefiks trasy wieloznacznej. Wszystkie wartości, które `{value}` zajmują symbol zastępczy, są interpretowane jako potencjalna wartość parametru trasy. Jeśli trasa domyślna nie zostanie znaleziona, ten prefiks trasy jest dołączany do `href` wygenerowanego atrybutu jako parametr i wartość żądania. W przeciwnym razie zostanie zastąpiony w szablonie trasy.

Rozważmy następującą akcję kontrolera:

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/BuiltInTagController.cs?name=snippet_AnchorTagHelperAction)]

Z domyślnym szablonem trasy zdefiniowanym podczas *uruchamiania. Konfiguracja*:

[!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_UseMvc&highlight=8-10)]

Widok MVC używa modelu dostarczonego przez akcję w następujący sposób:

```cshtml
@model Speaker
<!DOCTYPE html>
<html>
<body>
    <a asp-controller="Speaker"
       asp-action="Detail" 
       asp-route-id="@Model.SpeakerId">SpeakerId: @Model.SpeakerId</a>
</body>
</html>
```

`{id?}` Symbol zastępczy trasy domyślnej został dopasowany. Wygenerowany kod HTML:

```html
<a href="/Speaker/Detail/12">SpeakerId: 12</a>
```

Załóżmy, że prefiks trasy nie jest częścią zgodnego szablonu routingu, tak jak w przypadku następującego widoku MVC:

```cshtml
@model Speaker
<!DOCTYPE html>
<html>
<body>
    <a asp-controller="Speaker"
       asp-action="Detail"
       asp-route-speakerid="@Model.SpeakerId">SpeakerId: @Model.SpeakerId</a>
<body>
</html>
```

Następujący kod HTML jest generowany, `speakerid` ponieważ nie został odnaleziony w zgodnej trasie:

```html
<a href="/Speaker/Detail?speakerid=12">SpeakerId: 12</a>
```

`asp-controller` Jeśli albo `asp-action` nie zostanie określony, to to samo domyślne przetwarzanie zostanie wykonane, tak jak w `asp-route` atrybucie.

### <a name="asp-route"></a>ASP — Route

Atrybut [ASP-Route](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Route*) służy do tworzenia łączenia adresów URL bezpośrednio z nazwaną trasą. Przy użyciu [atrybutów routingu](xref:mvc/controllers/routing#attribute-routing), trasa może być nazywana, jak pokazano `SpeakerController` w i używane w `Evaluations` jej akcji:

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?range=22-24)]

W poniższym znaczniku `asp-route` atrybut odwołuje się do nazwanej trasy:

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspRoute)]

Pomocnik tagu kotwicy generuje trasę bezpośrednio do tej akcji kontrolera przy użyciu adresu URL */Speaker/Evaluations*. Wygenerowany kod HTML:

```html
<a href="/Speaker/Evaluations">Speaker Evaluations</a>
```

Jeśli `asp-controller` wartość `asp-action` lub jest określona oprócz `asp-route`, wygenerowana trasa nie może być oczekiwana przez użytkownika. Aby uniknąć konfliktu trasy, `asp-route` nie należy używać z atrybutami `asp-controller` i `asp-action` .

### <a name="asp-all-route-data"></a>ASP — wszystkie trasy — dane

Atrybut [ASP-All-Route-Data](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.RouteValues*) obsługuje tworzenie słownika par klucz-wartość. Klucz jest nazwą parametru, a wartość jest wartością parametru.

W poniższym przykładzie słownik został zainicjowany i przeszedł do Razor widoku. Alternatywnie dane mogły zostać przesłane przy użyciu modelu.

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAllRouteData)]

Poprzedni kod generuje następujący HTML:

```html
<a href="/Speaker/EvaluationsCurrent?speakerId=11&currentYear=true">Speaker Evaluations</a>
```

`asp-all-route-data` Słownik jest spłaszczony, aby utworzyć ciąg QueryString spełniający wymagania przeciążonej `Evaluations` akcji:

[!code-csharp[](samples/TagHelpersBuiltIn/Controllers/SpeakerController.cs?range=26-30)]

Jeśli wszystkie klucze w słowniku pasują do parametrów trasy, te wartości są zastępowane w marszrucie, zgodnie z potrzebami. Inne niezgodne wartości są generowane jako parametry żądania.

### <a name="asp-fragment"></a>ASP — fragment

Atrybut [ASP-fragment](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Fragment*) definiuje fragment adresu URL do dołączenia do adresu URL. Pomocnik tagu kotwicy dodaje znak skrótu (#). Rozważ następujące oznakowanie:

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspFragment)]

Wygenerowany kod HTML:

```html
<a href="/Speaker/Evaluations#SpeakerEvaluations">Speaker Evaluations</a>
```

Tagi skrótów są przydatne podczas tworzenia aplikacji po stronie klienta. Mogą one służyć do łatwego oznaczania i wyszukiwania w języku JavaScript, na przykład.

### <a name="asp-area"></a>obszar ASP

Atrybut [obszaru ASP](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Area*) ustawia nazwę obszaru używaną do ustawiania odpowiedniej trasy. W poniższych przykładach przedstawiono sposób `asp-area` , w jaki atrybut powoduje ponowne mapowanie tras.

#### <a name="usage-in-razor-pages"></a>Użycie na Razor stronach

RazorObszary stron są obsługiwane w ASP.NET Core 2,1 lub nowszych.

Weź pod uwagę następującą hierarchię katalogów:

* **{Nazwa projektu}**
  * **wwwroot**
  * **Obszary**
    * **Sesje**
      * **Pages**
        * *\_ViewStart. cshtml*
        * *Index. cshtml*
        * *Index.cshtml.cs*
  * **Pages**

Znacznikiem odwołującym się do strony *indeksu* Razor obszaru *sesji* jest:

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspAreaRazorPages)]

Wygenerowany kod HTML:

```html
<a href="/Sessions">View Sessions</a>
```

> [!TIP]
> Aby obsługiwać obszary w aplikacji Razor stron, wykonaj jedną z następujących czynności w programie `Startup.ConfigureServices`:
>
> * Ustaw [wersję zgodności](xref:mvc/compatibility-version) na 2,1 lub nowszą.
> * Ustaw właściwość [RazorPagesOptions. AllowAreas](xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.AllowAreas*) na `true`:
>
>   [!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_AllowAreas)]

#### <a name="usage-in-mvc"></a>Użycie w MVC

Weź pod uwagę następującą hierarchię katalogów:

* **{Nazwa projektu}**
  * **wwwroot**
  * **Obszary**
    * **Blogi**
      * **Kontrolery**
        * *HomeController.cs*
      * **Widoki**
        * **Strona główna**
          * *AboutBlog. cshtml*
          * *Index. cshtml*
        * *\_ViewStart. cshtml*
  * **Kontrolery**

Ustawienie `asp-area` "Blogi" prefiksuje *obszary katalogu/Blogi* do tras skojarzonych kontrolerów i widoków dla tego tagu zakotwiczenia. Znacznikiem odwołującym się do widoku *AboutBlog* jest:

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspArea)]

Wygenerowany kod HTML:

```html
<a href="/Blogs/Home/AboutBlog">About Blog</a>
```

> [!TIP]
> Aby obsługiwać obszary w aplikacji MVC, szablon trasy musi zawierać odwołanie do obszaru, jeśli istnieje. Ten szablon jest reprezentowany przez drugi parametr wywołania `routes.MapRoute` metody w trakcie *uruchamiania. Skonfiguruj*:
>
> [!code-csharp[](samples/TagHelpersBuiltIn/Startup.cs?name=snippet_UseMvc&highlight=5)]

### <a name="asp-protocol"></a>ASP — protokół

Atrybut [ASP-Protocol](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Protocol*) służy do określania protokołu (takiego jak `https`) w adresie URL. Przykład:

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspProtocol)]

Wygenerowany kod HTML:

```html
<a href="https://localhost/Home/About">About</a>
```

Nazwa hosta w przykładzie jest localhost. Pomocnik tagu kotwicy używa domeny publicznej witryny sieci Web podczas generowania adresu URL.

### <a name="asp-host"></a>ASP — Host

Atrybut [ASP-Host](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Host*) służy do określania nazwy hosta w adresie URL. Przykład:

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspHost)]

Wygenerowany kod HTML:

```html
<a href="https://microsoft.com/Home/About">About</a>
```

### <a name="asp-page"></a>ASP — Strona

Atrybut [ASP-Page](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.Page*) jest używany ze Razor stronami. Użyj go, aby ustawić wartość `href` atrybutu tagu kotwicy na określoną stronę. Prefiks nazwy strony z ukośnikiem ("/") powoduje utworzenie adresu URL.

Następujący przykład wskazuje na stronę uczestnika Razor :

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPage)]

Wygenerowany kod HTML:

```html
<a href="/Attendee">All Attendees</a>
```

Ten `asp-page` atrybut wzajemnie się wykluczają `asp-route`z `asp-controller`atrybutami `asp-action` , i. Program `asp-page` może być jednak używany z `asp-route-{value}` programem w celu kontrolowania routingu, ponieważ ilustruje to następujące oznakowanie:

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPageAspRouteId)]

Wygenerowany kod HTML:

```html
<a href="/Attendee?attendeeid=10">View Attendee</a>
```

### <a name="asp-page-handler"></a>ASP — obsługa stron

Atrybut [ASP-Page-Handler](xref:Microsoft.AspNetCore.Mvc.TagHelpers.AnchorTagHelper.PageHandler*) jest używany ze Razor stronami. Jest on przeznaczony do łączenia z konkretnymi programami obsługi stron.

Weź pod uwagę następujące procedury obsługi stron:

[!code-csharp[](samples/TagHelpersBuiltIn/Pages/Attendee.cshtml.cs?name=snippet_OnGetProfileHandler)]

Skojarzone ze znacznikiem model strony łącza do obsługi `OnGetProfile` stron. Zwróć uwagę `On<Verb>` , że prefiks nazwy metody obsługi stron został pominięty w wartości `asp-page-handler` atrybutu. Gdy metoda jest asynchroniczna, `Async` sufiks jest pomijany.

[!code-cshtml[](samples/TagHelpersBuiltIn/Views/Home/Index.cshtml?name=snippet_AspPageHandler)]

Wygenerowany kod HTML:

```html
<a href="/Attendee?attendeeid=12&handler=Profile">Attendee Profile</a>
```

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
