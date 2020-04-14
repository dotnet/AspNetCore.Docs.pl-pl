---
title: Routing do akcji kontrolera w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak ASP.NET Core MVC używa oprogramowania pośredniczącego routingu do dopasowywania adresów URL przychodzących żądań i mapowania ich na akcje.
ms.author: riande
ms.date: 3/25/2020
uid: mvc/controllers/routing
ms.openlocfilehash: 9f7a26a482cb115697a0a3d7439c14a062677c92
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277135"
---
# <a name="routing-to-controller-actions-in-aspnet-core"></a>Routing do akcji kontrolera w ASP.NET Core

Przez [Ryan Nowak](https://github.com/rynowak), Kirk [Larkin](https://twitter.com/serpent5)i [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

ASP.NET kontrolery Core używają [oprogramowania pośredniczącego](xref:fundamentals/middleware/index) Routing do dopasowania adresów URL przychodzących żądań i mapowania ich na [akcje.](#action)  Szablony tras:

* Są zdefiniowane w kodzie startowym lub atrybutach.
* Opisz, jak ścieżki adresów URL są dopasowywać do [akcji](#action).
* Są używane do generowania adresów URL dla łączy. Wygenerowane łącza są zazwyczaj zwracane w odpowiedziach.

Akcje są [kierowane konwencjonalnie](#cr) lub [kierowane atrybutem](#ar). Umieszczenie trasy na kontrolerze lub [akcji](#action) sprawia, że jest on atrybucowy. Zobacz [Routing mieszany, aby](#routing-mixed-ref-label) uzyskać więcej informacji.

Ten dokument:

* W tym artykule wyjaśniono interakcje między MVC a routingiem:
  * Jak typowe aplikacje MVC korzystają z funkcji routingu.
  * Obejmuje oba:
    * [Konwencjonalnie routingu](#cr) zwykle używane z kontrolerami i widokami.
    * *Routing atrybutów* używany z interfejsami API REST. Jeśli jesteś zainteresowany przede wszystkim routingu dla interfejsów API REST, przejdź do [routingu atrybut dla rest interfejsów API](#ar) sekcji.
  * Zobacz [Routing, aby](xref:fundamentals/routing) uzyskać szczegółowe informacje o routingu.
* Odnosi się do domyślnego systemu routingu dodanego w ASP.NET Core 3.0, nazywane routingiem punktów końcowych. W celu zapewnienia zgodności można używać kontrolerów z poprzednią wersją routingu. Instrukcje można znaleźć w [przewodniku po migracji 2.2-3.0.](xref:migration/22-to-30) Informacje na temat materiałów referencyjnych w starszym systemie routingu można znaleźć w [wersji 2.2 tego dokumentu.](xref:mvc/controllers/routing?view=aspnetcore-2.2)

<a name="cr"></a>

## <a name="set-up-conventional-route"></a>Konfigurowanie trasy konwencjonalnej

`Startup.Configure`zazwyczaj ma kod podobny do następującego podczas korzystania z [konwencjonalnego routingu:](#crd)

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet)]

Wewnątrz wywołania <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> do , służy do tworzenia jednej trasy. Pojedyncza trasa `default` nosi nazwę trasy. Większość aplikacji z kontrolerami i widokami `default` używa szablonu trasy podobnego do trasy. Interfejsy API REST powinny używać [routingu atrybutów](#ar).

Szablon `"{controller=Home}/{action=Index}/{id?}"`trasy:

* Dopasowuje ścieżkę adresu URL, taką jak`/Products/Details/5`
* Wyodrębnia wartości `{ controller = Products, action = Details, id = 5 }` trasy przez tokenizowanie ścieżki. Wyodrębnianie wartości trasy powoduje dopasowanie, jeśli aplikacja `ProductsController` ma `Details` kontroler o nazwie i akcję:

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippetA)]

  [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

* `/Products/Details/5`model wiąże `id = 5` wartość, aby `id` ustawić `5`parametr na . Zobacz [powiązanie modelu, aby](xref:mvc/models/model-binding) uzyskać więcej informacji.
* `{controller=Home}`definiuje `Home` jako domyślny `controller`.
* `{action=Index}`definiuje `Index` jako domyślny `action`.
*  Znak `?` w `{id?}` definiuje `id` jako opcjonalne.
  * Domyślne i opcjonalne parametry trasy nie muszą znajdować się w ścieżce adresu URL dopasowania. Zobacz [Odwołanie do szablonu trasy,](xref:fundamentals/routing#route-template-reference) aby uzyskać szczegółowy opis składni szablonu trasy.
* Pasuje do `/`ścieżki adresu URL .
* Tworzy wartości `{ controller = Home, action = Index }`trasy .

Wartości dla `controller` `action` i używać wartości domyślnych. `id`nie generuje wartości, ponieważ w ścieżce adresu URL nie ma odpowiedniego segmentu. `/`pasuje tylko wtedy, `HomeController` gdy `Index` istnieje a i akcja:

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

Przy użyciu poprzedniej definicji kontrolera `HomeController.Index` i szablonu trasy akcja jest uruchamiana dla następujących ścieżek adresów URL:

* `/Home/Index/17`
* `/Home/Index`
* `/Home`
* `/`

Ścieżka `/` adresu URL używa domyślnych `Home` kontrolerów i `Index` akcji szablonu trasy. Ścieżka `/Home` adresu URL używa domyślnej `Index` akcji szablonu trasy.

Metoda <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>wygody:

```csharp
endpoints.MapDefaultControllerRoute();
```

Zastępuje:

```csharp
endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

> [!IMPORTANT]
> Routing jest konfigurowany przy użyciu oprogramowania pośredniczącego <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> i. <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> Aby użyć kontrolerów:
>
> * <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> Wywołanie `UseEndpoints` wewnątrz do [mapowania atrybut routowanych](#ar) kontrolerów.
> * <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> Wywołanie <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>lub , aby mapować [tradycyjnie kierowane](#cr) kontrolery.

<a name="routing-conventional-ref-label"></a>
<a name="crd"></a>

## <a name="conventional-routing"></a>Routing konwencjonalny

Routing konwencjonalny jest używany z kontrolerami i widokami. Trasa: `default`

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet2)]

jest przykładem *konwencjonalnej trasy.* Nazywa się to *routingiem konwencjonalnym,* ponieważ ustanawia *konwencję* dla ścieżek adresów URL:

* Pierwszy segment ścieżki, `{controller=Home}`mapuje na nazwę kontrolera.
* Drugi segment `{action=Index}`, mapuje na nazwę [akcji.](#action)
* Trzeci segment, `{id?}` jest używany `id`do opcjonalnego . In `?` `{id?}` sprawia, że opcjonalne. `id`służy do mapowania do jednostki modelu.

Korzystając `default` z tej trasy, ścieżka adresu URL:

* `/Products/List`do `ProductsController.List` akcji.
* `/Blog/Article/17`mapuje `BlogController.Article` i zazwyczaj model `id` wiąże parametr z 17.

To mapowanie:

* Jest oparty **tylko**na kontrolerze i [nazwach akcji](#action) .
* Nie jest oparty na obszarach nazw, lokalizacjach plików źródłowych ani parametrach metody.

Korzystanie z routingu konwencjonalnego z trasą domyślną umożliwia tworzenie aplikacji bez konieczności wymyślania nowego wzorca adresu URL dla każdej akcji. W przypadku aplikacji z akcjami stylu [CRUD,](https://wikipedia.org/wiki/Create,_read,_update_and_delete) która ma spójność adresów URL między kontrolerami:

* Pomaga uprościć kod.
* Sprawia, że interfejs użytkownika bardziej przewidywalne.

> [!WARNING]
> W `id` poprzednim kodzie jest zdefiniowany jako opcjonalny przez szablon trasy. Akcje można wykonywać bez opcjonalnego identyfikatora podanego jako część adresu URL. Ogólnie rzecz`id` biorąc, gdy zostanie pominięty w adresie URL:
>
> * `id`jest ustawiona na `0` powiązanie modelu.
> * W dopasowywaniu `id == 0`bazy danych nie znaleziono żadnej encji .
>
> [Routing atrybutów](#ar) zapewnia precyzyjną kontrolę, aby identyfikator jest wymagany dla niektórych akcji, a nie dla innych. Zgodnie z konwencją dokumentacja `id` zawiera parametry opcjonalne, takie jak wtedy, gdy mogą pojawić się w prawidłowym użyciu.

Większość aplikacji powinna wybrać podstawowy i opisowy schemat routingu, aby adresy URL były czytelne i znaczące. Domyślna trasa `{controller=Home}/{action=Index}/{id?}`konwencjonalna:

* Obsługuje podstawowy i opisowy schemat routingu.
* Jest użytecznym punktem wyjścia dla aplikacji opartych na interfejsie użytkownika.
* Jest jedynym szablonem trasy potrzebnym dla wielu aplikacji interfejsu użytkownika sieci Web. W przypadku większych aplikacji interfejsu użytkownika sieci Web inna trasa przy użyciu [obszarów,](#areas) jeśli często wszystko, co jest potrzebne.

<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>oraz: <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*>

* Automatycznie przypisz wartość **zamówienia** do ich punktów końcowych na podstawie kolejności, w jakiej są wywoływane.

Routing punktu końcowego w ASP.NET Core 3.0 i nowszych:

* Nie ma pojęcia tras.
* Nie zapewnia gwarancji zamawiania dla wykonywania rozszerzalności, wszystkie punkty końcowe są przetwarzane jednocześnie.

Włącz [rejestrowanie,](xref:fundamentals/logging/index) aby zobaczyć, jak wbudowane implementacje routingu, takie jak <xref:Microsoft.AspNetCore.Routing.Route>, dopasowywanie żądań.

[Routing atrybutów](#ar) jest wyjaśniony w dalszej części tego dokumentu.

<a name="mr"></a>

### <a name="multiple-conventional-routes"></a>Wiele tras konwencjonalnych

Wiele [konwencjonalnych tras](#cr) `UseEndpoints` można dodać wewnątrz, dodając więcej połączeń do <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> i <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>. Pozwala to na zdefiniowanie wielu konwencji lub dodanie konwencjonalnych tras dedykowanych określonej [akcji,](#action)takiej jak:

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<a name="dcr"></a>

Trasa `blog` w poprzednim kodzie jest **dedykowaną trasą konwencjonalną.** Nazywa się to dedykowaną trasą konwencjonalną, ponieważ:

* Wykorzystuje [konwencjonalne routingu](#cr).
* Jest dedykowany do konkretnej [akcji](#action).

Ponieważ `controller` `action` i nie pojawiają się `"blog/{*article}"` w szablonie trasy jako parametry:

* Mogą mieć tylko wartości `{ controller = "Blog", action = "Article" }`domyślne .
* Ta trasa zawsze jest `BlogController.Article`mapowana do akcji.

`/Blog`, `/Blog/Article`i `/Blog/{any-string}` są jedynymi ścieżkami URL, które pasują do trasy blogu.

W poprzednim przykładzie:

* `blog`trasa ma wyższy priorytet dla `default` dopasowań niż trasa, ponieważ jest dodawana jako pierwsza.
* Jest i przykład routingu stylu [ślimaka,](https://developer.mozilla.org/docs/Glossary/Slug) gdzie jest to typowe, aby mieć nazwę artykułu jako część adresu URL.

> [!WARNING]
> W ASP.NET Core 3.0 i nowszych routing nie:
> * Zdefiniuj koncepcję zwaną *trasą*. `UseRouting`dodaje dopasowanie trasy do potoku oprogramowania pośredniczącego. Oprogramowanie `UseRouting` pośredniczące analizuje zestaw punktów końcowych zdefiniowanych w aplikacji i wybiera najlepsze dopasowanie punktu końcowego na podstawie żądania.
> * Zapewnij gwarancje dotyczące kolejności wykonywania <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> rozciągliwości, takich jak lub <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>.
>
>Zobacz [Routing](xref:fundamentals/routing) dla materiałów odniesienia na routingu.

<a name="cro"></a>

### <a name="conventional-routing-order"></a>Konwencjonalna kolejność routingu

Routing konwencjonalny pasuje tylko do kombinacji akcji i kontrolera, które są zdefiniowane przez aplikację. Ma to na celu uproszczenie przypadków, w których konwencjonalne trasy nakładają się na siebie.
Dodawanie tras <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>za <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> pomocą , i automatycznie przypisać wartość zamówienia do swoich punktów końcowych na podstawie kolejności, w jakiej są wywoływane. Dopasowania z trasy, która pojawia się wcześniej mają wyższy priorytet. Routing konwencjonalny jest zależny od zamówienia. Ogólnie rzecz biorąc, trasy z obszarami powinny być umieszczane wcześniej, ponieważ są bardziej szczegółowe niż trasy bez obszaru. [Dedykowane konwencjonalne trasy](#dcr) z `{*article}` połowu wszystkich parametrów trasy, takich jak może uczynić trasę zbyt [chciwy,](xref:fundamentals/routing#greedy)co oznacza, że pasuje do adresów URL, które mają być dopasowane do innych tras. Umieść chciwe trasy później w tabeli tras, aby zapobiec chciwych meczach.

<a name="best"></a>

### <a name="resolving-ambiguous-actions"></a>Rozwiązywanie niejednoznacznych działań

Gdy dwa punkty końcowe są zgodne z routingiem, routing musi wykonać jedną z następujących czynności:

* Wybierz najlepszego kandydata.
* Zgłosić wyjątek.

Przykład:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet9)]

Poprzedni kontroler definiuje dwie akcje, które pasują do:

* Ścieżka adresu URL`/Products33/Edit/17`
* Dane `{ controller = Products33, action = Edit, id = 17 }`trasy .

Jest to typowy wzorzec dla kontrolerów MVC:

* `Edit(int)`wyświetla formularz do edycji produktu.
* `Edit(int, Product)`przetwarza zaksięgowany formularz.

Aby rozwiązać właściwą trasę:

* `Edit(int, Product)`jest zaznaczona, gdy `POST`żądanie jest HTTP .
* `Edit(int)`jest zaznaczona, gdy [zlecenie HTTP](#verb) jest czymś innym. `Edit(int)`jest ogólnie wywoływana przez `GET`.

Aby <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> `[HttpPost]`można było wybrać metodę HTTP żądania, podano program routingu. `HttpPostAttribute` Sprawia, `Edit(int, Product)` że lepsze `Edit(int)`dopasowanie niż .

Ważne jest, aby zrozumieć rolę `HttpPostAttribute`atrybutów, takich jak . Podobne atrybuty są zdefiniowane dla innych [zleceń HTTP](#verb). W [przypadku routingu konwencjonalnego](#cr)często akcje używają tej samej nazwy akcji, gdy są częścią formularza pokazu, przesyłaj przepływ pracy formularza. Na przykład zobacz [Sprawdzanie dwóch metod akcji edycji](xref:tutorials/first-mvc-app/controller-methods-views#get-post).

Jeśli routing nie może wybrać najlepszego kandydata, <xref:System.Reflection.AmbiguousMatchException> jest generowany, lista wielu dopasowanych punktów końcowych.

<a name="routing-route-name-ref-label"></a>

### <a name="conventional-route-names"></a>Konwencjonalne nazwy tras

Ciągi `"blog"` i `"default"` w poniższych przykładach są konwencjonalne nazwy tras:

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

Nazwy trasy nadają trasie nazwę logiczną. Nazwana trasa może służyć do generowania adresów URL. Użycie nazwanej trasy upraszcza tworzenie adresów URL, gdy kolejność tras może skomplikować generowanie adresów URL. Nazwy tras muszą być unikatowe dla całej aplikacji.

Nazwy tras:

* Nie mają wpływu na dopasowywanie adresów URL lub obsługę żądań.
* Są używane tylko do generowania adresów URL.

Koncepcja nazwy trasy jest reprezentowana w routingu jako [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata). Nazwa **trasy** terminów i **nazwa punktu końcowego:**

* Są wymienne.
* Który z nich jest używany w dokumentacji i kod zależy od interfejsu API są opisane.

<a name="attribute-routing-ref-label"></a>
<a name="ar"></a>

## <a name="attribute-routing-for-rest-apis"></a>Routing atrybutów dla interfejsów API REST

Interfejsy API REST powinny używać routingu atrybutów do modelowania funkcji aplikacji jako zestawu zasobów, w których operacje są reprezentowane przez [zlecenia HTTP](#verb).

Routing atrybutów używa zestawu atrybutów do mapowania akcji bezpośrednio do szablonów tras. Następujący `StartUp.Configure` kod jest typowy dla interfejsu API REST i jest używany w następnym przykładzie:

[!code-csharp[](routing/samples/3.x/main/StartupApi.cs?name=snippet)]

W poprzednim kodzie <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> jest `UseEndpoints` wywoływana wewnątrz do mapowania atrybutu kierowanych kontrolerów.

W poniższym przykładzie:

* Używana jest `Configure` poprzednia metoda.
* `HomeController`dopasowuje zestaw adresów URL podobny `{controller=Home}/{action=Index}/{id?}` do domyślnej trasy konwencjonalnej.

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

Akcja `HomeController.Index` jest uruchamiana dla dowolnej `/`ścieżki `/Home` `/Home/Index`adresu `/Home/Index/3`URL , , lub .

W tym przykładzie przedstawiono kluczową różnicę programowania między routingiem atrybutów a [routingiem konwencjonalnym](#cr). Routing atrybutów wymaga więcej danych wejściowych, aby określić trasę. Konwencjonalna trasa domyślna obsługuje trasy bardziej zwięźle. Jednak routing atrybutów umożliwia i wymaga precyzyjnej kontroli, które szablony tras mają zastosowanie do każdej [akcji](#action).

W routingu atrybutów nazwa kontrolera i nazwy akcji nie odgrywają **żadnej** roli, w której akcja jest dopasowywała. Poniższy przykład pasuje do tych samych adresów URL, co w poprzednim przykładzie:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

Poniższy kod używa zastępowania tokenów `action` i: `controller`

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet22)]

Następujący kod ma `[Route("[controller]/[action]")]` zastosowanie do kontrolera:

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

W poprzednim kodzie `Index` szablony metod muszą `/` `~/` być dołączane lub do szablonów marszruty. Szablony tras zastosowane do akcji, `/` `~/` które rozpoczynają się lub nie są łączone z szablonami tras zastosowanymi do kontrolera.

Zobacz [Pierwszeństwo szablonu trasy, aby](xref:fundamentals/routing#rtp) uzyskać informacje o wyborze szablonu trasy.

## <a name="reserved-routing-names"></a>Nazwy marszruty zarezerwowane

Następujące słowa kluczowe to nazwy parametrów trasy zastrzeżonej podczas korzystania z kontrolerów lub stron razor:

* `action`
* `area`
* `controller`
* `handler`
* `page`

Użycie `page` jako parametru trasy z routingiem atrybutów jest częstym błędem. Powoduje to niespójne i mylące zachowanie z generowaniem adresów URL.

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo2Controller.cs?name=snippet)]

Nazwy parametrów specjalnych są używane przez generowanie adresów URL w celu ustalenia, czy operacja generowania adresów URL odnosi się do strony Razor lub kontrolera.

<a name="verb"></a>

## <a name="http-verb-templates"></a>Szablony czasowników HTTP

ASP.NET Core ma następujące szablony czasowników HTTP:

* [[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)
* [[HttpPost]](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)
* [[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)
* [[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)
* [[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)
* [[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)

<a name="rt"></a>

### <a name="route-templates"></a>Szablony tras

ASP.NET Core ma następujące szablony tras:

* Wszystkie [szablony zleceń HTTP](#verb) są szablonami marszrut.
* [[Trasa]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)

<a name="arx"></a>

### <a name="attribute-routing-with-http-verb-attributes"></a>Routing atrybutów z atrybutami zlecenia http

Należy wziąć pod uwagę następujący kontroler:

[!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet)]

Powyższy kod ma następujące działanie:

* Każda akcja `[HttpGet]` zawiera atrybut, który ogranicza dopasowywanie tylko do żądań HTTP GET.
* Akcja `GetProduct` zawiera `"{id}"` szablon, w `id` związku z tym `"api/[controller]"` jest dołączany do szablonu na kontrolerze. Szablon metody `"api/[controller]/"{id}""`jest . W związku z tym ta akcja `/api/test2/xyz`jest`/api/test2/123``/api/test2/{any string}`zgodna tylko z żądaniami GET dla formularza , , itp.
  [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet2)]
* Akcja `GetIntProduct` zawiera `"int/{id:int}")` szablon. Część `:int` szablonu ogranicza wartości `id` trasy do ciągów, które można przekonwertować na liczbę całkowitą. Wniosek GET `/api/test2/int/abc`do:
  * Nie pasuje do tej akcji.
  * Zwraca błąd [404 Nie znaleziono.](https://developer.mozilla.org/docs/Web/HTTP/Status/404)
    [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet3)]
* Akcja `GetInt2Product` zawiera `{id}` w szablonie, ale `id` nie ogranicza się do wartości, które mogą być konwertowane na całkowitą. Wniosek GET `/api/test2/int2/abc`do:
  * Pasuje do tej trasy.
  * Powiązanie modelu `abc` nie można przekonwertować na całkowitą. Parametrem `id` metody jest liczba całkowita.
  * Zwraca [400 Bad Request,](https://developer.mozilla.org/docs/Web/HTTP/Status/400) ponieważ powiązanie modelu nie można przekonwertować`abc` na liczbę całkowitą.
      [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet4)]

Routing atrybutów <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> może używać <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>atrybutów, takich jak , i <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>. Wszystkie atrybuty [zlecenia HTTP](#verb) akceptują szablon trasy. W poniższym przykładzie przedstawiono dwie akcje pasujące do tego samego szablonu trasy:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyProductsController.cs?name=snippet1)]

Korzystanie ze `/products3`ścieżki adresu URL:

* Akcja `MyProductsController.ListProducts` jest uruchamiana, gdy `GET`zlecenie [HTTP](#verb) jest .
* Akcja `MyProductsController.CreateProduct` jest uruchamiana, gdy `POST`zlecenie [HTTP](#verb) jest .

Podczas tworzenia interfejsu API REST, rzadko, że trzeba `[Route(...)]` będzie użyć w metodzie akcji, ponieważ akcja akceptuje wszystkie metody HTTP. Lepiej jest użyć bardziej szczegółowego [atrybutu zlecenia HTTP,](#verb) aby dokładnie określić, co obsługuje interfejs API. Klienci interfejsów API REST powinni wiedzieć, jakie ścieżki i zlecenia HTTP są mapowane na określone operacje logiczne.

Interfejsy API REST powinny używać routingu atrybutów do modelowania funkcji aplikacji jako zestawu zasobów, w których operacje są reprezentowane przez zlecenia HTTP. Oznacza to, że wiele operacji, na przykład GET i POST na tym samym zasobie logicznym, używa tego samego adresu URL. Routing atrybutów zapewnia poziom kontroli, który jest potrzebny do starannego projektowania układu publicznego punktu końcowego interfejsu API.

Ponieważ trasa atrybutu ma zastosowanie do określonej akcji, łatwo jest wprowadzić parametry wymagane jako część definicji szablonu trasy. W poniższym `id` przykładzie jest wymagane jako część ścieżki adresu URL:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

Akcja: `Products2ApiController.GetProduct(int)`

* Jest uruchamiany ze ścieżką URL, taką jak`/products2/3`
* Nie jest uruchamiany ze `/products2`ścieżką adresu URL .

[Atrybut [Zużywa]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) umożliwia akcję, aby ograniczyć typy zawartości obsługiwanych żądań. Aby uzyskać więcej informacji, zobacz [Definiowanie obsługiwanych typów zawartości żądań za pomocą atrybutu Zużywa](xref:web-api/index#consumes).

 Zobacz [Routing,](xref:fundamentals/routing) aby uzyskać pełny opis szablonów tras i powiązanych opcji.

Aby uzyskać `[ApiController]`więcej informacji na temat , zobacz [ApiController atrybut](xref:web-api/index##apicontroller-attribute).

## <a name="route-name"></a>Nazwa trasy

Poniższy kod definiuje nazwę `Products_List`trasy:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

Nazwy tras mogą być używane do generowania adresu URL na podstawie określonej trasy. Nazwy tras:

* Nie mają wpływu na zachowanie dopasowywania adresu URL routingu.
* Są używane tylko do generowania adresów URL.

Nazwy tras muszą być unikatowe dla całej aplikacji.

Kontrast poprzedniego kodu z konwencjonalną trasą domyślną, `id` która`{id?}`definiuje parametr jako opcjonalny ( ). Możliwość precyzyjnego określania interfejsów API ma zalety, takie jak zezwalanie `/products` i `/products/5` wysyłanie do różnych akcji.

<a name="routing-combining-ref-label"></a>

## <a name="combining-attribute-routes"></a>Łączenie tras atrybutów

Aby routing atrybutów był mniej powtarzalny, atrybuty trasy na kontrolerze są łączone z atrybutami trasy dla poszczególnych akcji. Wszystkie szablony tras zdefiniowane na kontrolerze są poprzedzane do szablonów tras w akcjach. Umieszczenie atrybutu trasy na kontrolerze powoduje, że **wszystkie** akcje w routingu atrybutu używanego przez kontrolera.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet)]

W poprzednim przykładzie:

* Ścieżka `/products` adresu URL może być zgodna`ProductsApi.ListProducts`
* Ścieżka `/products/5` adresu URL `ProductsApi.GetProduct(int)`może być zgodna .

Obie te akcje są `GET` zgodne tylko z `[HttpGet]` protokołem HTTP, ponieważ są oznaczone atrybutem.

Szablony tras zastosowane do akcji, `/` `~/` które rozpoczynają się lub nie są łączone z szablonami tras zastosowanymi do kontrolera. Poniższy przykład pasuje do zestawu ścieżek URL podobnych do trasy domyślnej.

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet)]

W poniższej `[Route]` tabeli opisano atrybuty w poprzednim kodzie:

| Atrybut               | Łączy się z`[Route("Home")]` | Definiuje szablon trasy |
| ----------------- | ------------ | --------- |
| `[Route("")]` | Tak | `"Home"` |
| `[Route("Index")]` | Tak | `"Home/Index"` |
| `[Route("/")]` | **Nie** | `""` |
| `[Route("About")]` | Tak | `"Home/About"` |

<a name="routing-ordering-ref-label"></a>
<a name="oar"></a>

### <a name="attribute-route-order"></a>Kolejność marszruty atrybutów

Routing tworzy drzewo i dopasowuje wszystkie punkty końcowe jednocześnie:

* Wpisy trasy zachowują się tak, jakby były umieszczone w idealnym kolejności.
* Najbardziej konkretne trasy mają szansę wykonać przed bardziej ogólnych tras.

Na przykład trasa atrybutu, taka jak, `blog/search/{topic}` jest `blog/{*article}`bardziej szczegółowa niż trasa atrybutu, taka jak . Trasa `blog/search/{topic}` ma wyższy priorytet, domyślnie, ponieważ jest bardziej szczegółowe. Przy użyciu [konwencjonalnego routingu](#cr)deweloper jest odpowiedzialny za umieszczanie tras w żądanej kolejności.

Trasy atrybutów można skonfigurować <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> zamówienie przy użyciu właściwości. Wszystkie [atrybuty trasy](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) pod `Order` warunkiem struktury obejmują . Trasy są przetwarzane zgodnie z rosnącym `Order` rodzajem właściwości. Domyślna kolejność `0`to . Ustawianie trasy `Order = -1` przy użyciu przebiegów przed trasami, które nie ustawiają zamówienia. Ustawianie trasy `Order = 1` przy użyciu przebiegów po domyślnym uporządkowaniu trasy.

**Unikaj** w `Order`zależności od . Jeśli miejsce adresu URL aplikacji wymaga jawnych wartości zamówienia do prawidłowego rozsyłania, prawdopodobnie jest to również mylące dla klientów. Ogólnie rzecz biorąc routing atrybutów wybiera prawidłową trasę z dopasowaniem adresu URL. Jeśli domyślna kolejność używana do generowania adresów URL nie działa, użycie nazwy trasy `Order` jako zastąpienia jest zwykle prostsze niż zastosowanie właściwości.

Rozważmy następujące dwa kontrolery, które `/home`definiują dopasowanie trasy:

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

Żądanie `/home` z poprzednim kodem zgłasza wyjątek podobny do następującego:

```text
AmbiguousMatchException: The request matched multiple endpoints. Matches:

 WebMvcRouting.Controllers.HomeController.Index
 WebMvcRouting.Controllers.MyDemoController.MyIndex
```

Dodanie `Order` do jednego z atrybutów trasy rozwiązuje niejednoznaczność:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo3Controller.cs?name=snippet3& highlight=2)]

Za pomocą poprzedniego `/home` kodu `HomeController.Index` uruchamia punkt końcowy. Aby dostać `MyDemoController.MyIndex`się `/home/MyIndex`do , wniosek . **Uwaga:**

* Powyższy kod jest przykładem lub złym projektem routingu. Został on użyty `Order` do zilustrowania nieruchomości.
* Właściwość `Order` rozwiązuje tylko niejednoznaczności, tego szablonu nie można dopasować. Byłoby lepiej, aby `[Route("Home")]` usunąć szablon.

Zobacz [Konwencje trasy i aplikacji Razor Pages: Kolejność tras, aby](xref:razor-pages/razor-pages-conventions#route-order) uzyskać informacje o zamówieniu trasy za pomocą strony Razor Pages.

W niektórych przypadkach zwracany jest błąd HTTP 500 z niejednoznacznymi trasami. Użyj [rejestrowania,](xref:fundamentals/logging/index) aby zobaczyć, `AmbiguousMatchException`które punkty końcowe spowodowały .

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a>Zastępowanie tokenów w szablonach tras [kontroler], [akcja], [obszar]

Dla wygody trasy atrybutów obsługują zastępowanie tokenów zarezerwowanych parametrów trasy, załączając token w jednym z następujących elementów:

* Szelki kwadratowe:`[]`
* Kręcone szelki:`{}`

Tokeny `[action]`, `[area]`i `[controller]` są zastępowane wartościami nazwy akcji, nazwy obszaru i nazwy kontrolera z akcji, w której trasa jest zdefiniowana:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet)]

Powyższy kod ma następujące działanie:

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet10)]

  * Pasuje`/Products0/List`

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet11)]

  * Pasuje`/Products0/Edit/{id}`

Zastępowanie tokenu występuje jako ostatni krok tworzenia tras atrybutu. W poprzednim przykładzie zachowuje się tak samo jak następujący kod:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet20)]

[!INCLUDE[](~/includes/MTcomments.md)]

Trasy atrybutów można również łączyć z dziedziczeniem. Jest to potężne w połączeniu z zastąpieniem tokenów. Zastępowanie tokenów ma również zastosowanie do nazw tras zdefiniowanych przez trasy atrybutów.
`[Route("[controller]/[action]", Name="[controller]_[action]")]`generuje unikatową nazwę trasy dla każdej akcji:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet5)]

Zastępowanie tokenów ma również zastosowanie do nazw tras zdefiniowanych przez trasy atrybutów.
`[Route("[controller]/[action]", Name="[controller]_[action]")]`
generuje unikatową nazwę trasy dla każdej akcji.

Aby dopasować dosłowny `[` ogranicznik zastępujący token lub `]`, ucieknij go, powtarzając znak (`[[` lub `]]`).

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a>Dostosowywanie zastępowania tokenów za pomocą transformatora parametrów

Zastąpienie tokenu można dostosować za pomocą transformatora parametrów. Transformator parametrów <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> implementuje i przekształca wartość parametrów. Na przykład transformator parametrów `SlugifyParameterTransformer` `SubscriptionManagement` niestandardowych `subscription-management`zmienia wartość trasy na:

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

Jest <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> to konwencja modelu aplikacji, która:

* Stosuje transformator parametrów do wszystkich tras atrybutów w aplikacji.
* Dostosowuje wartości tokenu marszruty atrybutu podczas ich zastępowyjca.

[!code-csharp[](routing/samples/3.x/main/Controllers/SubscriptionManagementController.cs?name=snippet)]

Poprzednia `ListAll` metoda `/subscription-management/list-all`jest zgodna .

Jest `RouteTokenTransformerConvention` zarejestrowany jako opcja `ConfigureServices`w .

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet)]

Zobacz [dokumenty sieci web MDN na ślimaku,](https://developer.mozilla.org/docs/Glossary/Slug) aby uzyskać definicję ślimaka.

[!INCLUDE[](~/includes/regex.md)]
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-attribute-routes"></a>Wiele tras atrybutów

Routing atrybutów obsługuje definiowanie wielu tras, które osiągają tę samą akcję. Najczęstszym zastosowaniem tego jest naśladować zachowanie domyślnej trasy konwencjonalnej, jak pokazano w poniższym przykładzie:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6x)]

Umieszczenie wielu atrybutów trasy na kontrolerze oznacza, że każdy z nich łączy się z każdym z atrybutów trasy w metodach akcji:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6)]

Implementują `IActionConstraint`wszystkie ograniczenia trasy [czasowników HTTP](#verb) .

Gdy wiele atrybutów <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> trasy, które implementują są umieszczane w akcji:

* Każde ograniczenie akcji łączy się z szablonem trasy zastosowanym do kontrolera.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet7)]

Korzystanie z wielu tras w akcjach może wydawać się przydatne i zaawansowane, lepiej zachować podstawową i dobrze zdefiniowaną przestrzeń adresową aplikacji. Użyj wielu tras w akcjach **tylko** wtedy, gdy jest to potrzebne, na przykład do obsługi istniejących klientów.

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a>Określanie parametrów opcjonalnych trasy atrybutu, wartości domyślnych i ograniczeń

Trasy atrybutów obsługują tę samą składnię wbudowaną co konwencjonalne trasy, aby określić parametry opcjonalne, wartości domyślne i ograniczenia.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet8&highlight=3)]

W poprzednim kodzie `[HttpPost("product/{id:int}")]` stosuje ograniczenie trasy. Akcja `ProductsController.ShowProduct` jest dopasowywała się `/product/3`tylko do ścieżek URL, takich jak . Część `{id:int}` szablonu marszruty ogranicza ten segment tylko do liczby całkowitej.

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

Zobacz [Odwołanie do szablonu trasy,](xref:fundamentals/routing#route-template-reference) aby uzyskać szczegółowy opis składni szablonu trasy.

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a>Niestandardowe atrybuty trasy przy użyciu IRouteTemplateProvider

Implementują wszystkie [atrybuty trasy](#rt) <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>. ASP.NET podstawowy czas pracy:

* Wyszukuje atrybuty na klasy kontrolera i metody akcji po uruchomieniu aplikacji.
* Używa atrybutów, `IRouteTemplateProvider` które implementują do tworzenia początkowego zestawu tras.

Implementuj `IRouteTemplateProvider` do definiowania atrybutów trasy niestandardowej. Każdy `IRouteTemplateProvider` z nich umożliwia zdefiniowanie pojedynczej trasy z niestandardowym szablonem trasy, kolejnością i nazwą:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyTestApiController.cs?name=snippet&highlight=1-10)]

Poprzednia `Get` metoda `Order = 2, Template = api/MyTestApi`zwraca .

<a name="routing-app-model-ref-label"></a>

### <a name="use-application-model-to-customize-attribute-routes"></a>Dostosowywanie tras atrybutów za pomocą modelu aplikacji

Model aplikacji:

* Jest modelem obiektów utworzonym podczas uruchamiania.
* Zawiera wszystkie metadane używane przez ASP.NET Core do kierowania i wykonywania akcji w aplikacji.

Model aplikacji zawiera wszystkie dane zebrane z atrybutów trasy. Dane z atrybutów trasy `IRouteTemplateProvider` jest dostarczany przez implementację. Konwencje:

* Można zapisać, aby zmodyfikować model aplikacji, aby dostosować zachowanie routingu.
* Są odczytywane przy uruchamianiu aplikacji.

W tej sekcji przedstawiono podstawowy przykład dostosowywania routingu przy użyciu modelu aplikacji. Poniższy kod sprawia, że trasy są z grubsza zgodne ze strukturą folderów projektu.

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet)]

Poniższy kod uniemożliwia `namespace` stosowanie konwencji do kontrolerów, które są kierowane atrybutem:

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet2)]

Na przykład następujący kontroler nie `NamespaceRoutingConvention`używa:

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/ManagersController.cs?name=snippet&highlight=1)]

Metoda: `NamespaceRoutingConvention.Apply`

* Nic nie robi, jeśli kontroler jest kierowane atrybut.
* Ustawia szablon kontrolerów na `namespace`podstawie , `namespace` z usuniętą bazą.

Można `NamespaceRoutingConvention` zastosować w: `Startup.ConfigureServices`

[!code-csharp[](routing/samples/3.x/nsrc/Startup.cs?name=snippet&highlight=1,14-18)]

Rozważmy na przykład następujący kontroler:

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/UsersController.cs)]

Powyższy kod ma następujące działanie:

* Podstawą `namespace` jest `My.Application`.
* Pełna nazwa poprzedniego kontrolera `My.Application.Admin.Controllers.UsersController`to .
* Szablon `NamespaceRoutingConvention` kontrolerów ma `Admin/Controllers/Users/[action]/{id?`być .

Można `NamespaceRoutingConvention` również zastosować jako atrybut na kontrolerze:

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/TestController.cs?name=snippet&highlight=1)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a>Routing mieszany: routing atrybutów a routing konwencjonalny

ASP.NET aplikacje Core mogą łączyć użycie konwencjonalnego routingu i routingu atrybutów. Typowe jest używanie konwencjonalnych tras dla kontrolerów obsługujących strony HTML dla przeglądarek i routingu atrybutów dla kontrolerów obsługujących interfejsy API REST.

Akcje są kierowane konwencjonalnie lub są kierowane. Umieszczenie trasy na kontrolerze lub akcji sprawia, że atrybut jest kierowany. Akcje definiujące trasy atrybutów nie mogą być osiągane za pośrednictwem tras konwencjonalnych i odwrotnie. **Każdy** atrybut trasy na kontrolerze sprawia, że **wszystkie** akcje w atrybucie kontrolera kierowane.

Routing atrybutów i routing konwencjonalny używają tego samego aparatu routingu.

<a name="routing-url-gen-ref-label"></a>
<a name="ambient"></a>

## <a name="url-generation-and-ambient-values"></a>Generowanie adresów URL i wartości otoczenia

Aplikacje mogą używać funkcji generowania adresów URL routingu do generowania łączy url do akcji. Generowanie adresów URL eliminuje twarde kodowanie adresów URL, dzięki czemu kod jest bardziej niezawodny i niezawodny. W tej sekcji skupiono się na funkcjach generowania adresów URL udostępnianych przez MVC i opisano tylko podstawy działania generowania adresów URL. Zobacz [Routing, aby](xref:fundamentals/routing) uzyskać szczegółowy opis generowania adresów URL.

Interfejs <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> jest podstawowym elementem infrastruktury między MVC i routingu do generowania adresów URL. Wystąpienie `IUrlHelper` jest dostępne `Url` za pośrednictwem właściwości w kontrolerach, widokach i składnikach widoku.

W poniższym przykładzie `IUrlHelper` interfejs jest `Controller.Url` używany za pośrednictwem właściwości do generowania adresu URL do innej akcji.

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

Jeśli aplikacja używa domyślnej trasy konwencjonalnej, `url` wartością zmiennej `/UrlGeneration/Destination`jest ciąg ścieżki adresu URL . Ta ścieżka adresu URL jest tworzona przez routing przez połączenie:

* Wartości trasy z bieżącego żądania, które są **nazywane wartościami otoczenia**.
* Wartości przekazywane `Url.Action` do i zastępowanie tych wartości w szablonie trasy:

``` text
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

Każdy parametr trasy w szablonie trasy ma swoją wartość zastąpiona pasującymi nazwami z wartościami i wartościami otoczenia. Parametr trasy, który nie ma wartości, może:

* Użyj wartości domyślnej, jeśli ją ma.
* Być pominięte, jeśli jest to opcjonalne. Na przykład `id` z szablonu `{controller}/{action}/{id?}`trasy .

Generowanie adresu URL kończy się niepowodzeniem, jeśli dowolny wymagany parametr trasy nie ma odpowiedniej wartości. Jeśli generowanie adresu URL nie powiedzie się dla trasy, następna trasa jest wypróbowywych, dopóki nie zostanie wypróbowane wszystkie trasy lub zostanie znalezione dopasowanie.

W poprzednim przykładzie `Url.Action` zakłada się [konwencjonalną routing](#cr). Generowanie adresów URL działa podobnie z [routingiem atrybutów,](#ar)chociaż pojęcia są różne. Z konwencjonalną trasą:

* Wartości trasy są używane do rozwijania szablonu.
* Wartości trasy `controller` dla `action` i zwykle pojawiają się w tym szablonie. Działa to, ponieważ adresy URL dopasowane przez routing są zgodne z konwencją.

W poniższym przykładzie użyto routingu atrybutów:

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationAttrController.cs?name=snippet_1)]

Akcja `Source` w poprzednim kodzie `custom/url/to/destination`generuje .

<xref:Microsoft.AspNetCore.Routing.LinkGenerator>w ASP.NET Core 3.0 jako alternatywa `IUrlHelper`dla . `LinkGenerator`oferuje podobną, ale bardziej elastyczną funkcjonalność. Każda metoda `IUrlHelper` na ma odpowiednią `LinkGenerator` rodzinę metod, jak również.

### <a name="generating-urls-by-action-name"></a>Generowanie adresów URL według nazwy akcji

[Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator.GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*)i wszystkie powiązane przeciążenia są przeznaczone do generowania docelowego punktu końcowego przez określenie nazwy kontrolera i nazwy akcji.

Podczas `Url.Action`korzystania z , `controller` bieżące wartości trasy dla i `action` są dostarczane przez środowisko wykonawcze:

* Wartość `controller` i `action` są częścią wartości [otoczenia](#ambient) i wartości. Metoda `Url.Action` zawsze używa bieżących `action` wartości `controller` i generuje ścieżkę adresu URL, która kieruje do bieżącej akcji.

Routing próbuje użyć wartości w wartościach otoczenia, aby wypełnić informacje, które nie zostały podane podczas generowania adresu URL. Rozważ trasę `{a}/{b}/{c}/{d}` podobną `{ a = Alice, b = Bob, c = Carol, d = David }`do wartości otoczenia:

* Routing ma wystarczającą ilość informacji, aby wygenerować adres URL bez żadnych dodatkowych wartości.
* Routing ma wystarczającą ilość informacji, ponieważ wszystkie parametry trasy mają wartość.

Jeśli wartość `{ d = Donovan }` zostanie dodana:

* Wartość `{ d = David }` jest ignorowana.
* Wygenerowana ścieżka `Alice/Bob/Carol/Donovan`adresu URL to .

**Ostrzeżenie:** ścieżki adresów URL są hierarchiczne. W poprzednim przykładzie, jeśli `{ c = Cheryl }` wartość jest dodana:

* Obie wartości `{ c = Carol, d = David }` są ignorowane.
* Nie ma już wartości `d` dla i generowania adresów URL nie powiedzie się.
* Żądane wartości `c` `d` i muszą być określone do generowania adresu URL.  

Można oczekiwać, aby trafić `{controller}/{action}/{id?}`ten problem z domyślną trasą . Ten problem jest rzadkością w praktyce, ponieważ `Url.Action` zawsze jawnie określa `controller` i `action` wartość.

Kilka przeciążeń [url.action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) podjąć obiekt wartości trasy, aby `controller` podać `action`wartości dla parametrów trasy innych niż i . Obiekt wartości trasy jest często `id`używany z programem . Na przykład `Url.Action("Buy", "Products", new { id = 17 })`. Obiekt wartości trasy:

* Zgodnie z konwencją jest zwykle obiektem typu anonimowego.
* Może być `IDictionary<>` POCO lub [POCO).](https://wikipedia.org/wiki/Plain_old_CLR_object)

Wszelkie dodatkowe wartości trasy, które nie pasują do parametrów trasy są umieszczane w ciągu zapytania.

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet)]

Poprzedni kod generuje `/Products/Buy/17?color=red`.

Poniższy kod generuje bezwzględny adres URL:

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet2)]

Aby utworzyć bezwzględny adres URL, użyj jednej z następujących czynności:

* Przeciążenie, które `protocol`akceptuje . Na przykład poprzedni kod.
* [LinkGenerator.GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), który domyślnie generuje bezwzględne identyfikatory URI.

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generate-urls-by-route"></a>Generowanie adresów URL według trasy

Poprzedni kod zademonstrował wygenerowanie adresu URL przez przekazanie w kontrolerze i nazwie akcji. `IUrlHelper`udostępnia również rodzinę metod [Url.RouteUrl.](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) Metody te są podobne do [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), ale nie `action` `controller` kopiują bieżących wartości i do wartości trasy. Najczęstszym zastosowaniem `Url.RouteUrl`:

* Określa nazwę trasy do wygenerowania adresu URL.
* Ogólnie nie określa nazwy kontrolera lub akcji.

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGeneration2Controller.cs?name=snippet_1)]

Następujący plik Razor generuje łącze `Destination_Route`HTML do:

[!code-cshtml[](routing/samples/3.x/main/Views/Shared/MyLink.cshtml)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generate-urls-in-html-and-razor"></a>Generowanie adresów URL w formacie HTML i Razor

<xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper>zapewnia <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> metody [Html.BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) i [Html.ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) do generowania `<form>` i `<a>` elementów odpowiednio. Te metody używają [metody Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) do generowania adresu URL i akceptują podobne argumenty. Towarzysze `Url.RouteUrl` są `HtmlHelper` `Html.BeginRouteForm` i `Html.RouteLink` które mają podobną funkcjonalność.

TagHelpers generowania adresów URL za pośrednictwem `form` TagHelper i `<a>` TagHelper. Oba te `IUrlHelper` zastosowania do ich realizacji. Aby uzyskać więcej [informacji, zobacz Pomocnicy tagów w formularzach.](xref:mvc/views/working-with-forms)

Widoki wewnątrz `IUrlHelper` jest dostępny `Url` za pośrednictwem właściwości dla każdego generowania adresów URL ad hoc nie objęte powyższe.

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="url-generation-in-action-results"></a>Generowanie adresu URL w wynikach działania

Powyższe przykłady pokazano `IUrlHelper` przy użyciu w kontrolerze. Najczęstszym użyciem w kontrolerze jest generowanie adresu URL jako część wyniku akcji.

Klasy <xref:Microsoft.AspNetCore.Mvc.ControllerBase> <xref:Microsoft.AspNetCore.Mvc.Controller> i klasy podstawowe zapewniają wygodę metod wyników akcji, które odwołują się do innej akcji. Jednym z typowych zastosowań jest przekierowanie po zaakceptowaniu danych wejściowych użytkownika:

[!code-csharp[](routing/samples/3.x/main/Controllers/CustomerController.cs?name=snippet)]

Wyniki akcji metody fabryczne, takie jak <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> i <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> `IUrlHelper`postępuj zgodnie z podobnym wzorcem do metod na .

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a>Specjalny przypadek dla dedykowanych tras konwencjonalnych

[Routing konwencjonalny](#cr) może używać specjalnego rodzaju definicji trasy zwanej [dedykowaną trasą konwencjonalną.](#dcr) W poniższym przykładzie `blog` nazwana trasa jest dedykowaną trasą konwencjonalną:

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

Korzystając z poprzednich definicji `Url.Action("Index", "Home")` trasy, generuje `/` ścieżkę adresu URL przy użyciu `default` trasy, ale dlaczego? Można się domyślać, że wartości `{ controller = Home, action = Index }` trasy `blog`wystarczą do wygenerowania adresu URL za pomocą , a wynikiem będzie `/blog?action=Index&controller=Home`.

[Dedykowane konwencjonalne trasy](#dcr) opierają się na specjalnym zachowaniu wartości domyślnych, które nie mają odpowiedniego parametru trasy, który uniemożliwia zbyt [chciwość](xref:fundamentals/routing#greedy) trasy z generowaniem adresu URL. W tym przypadku wartościami domyślnymi są `{ controller = Blog, action = Article }`, i ani `controller` ani nie `action` pojawia się jako parametr trasy. Podczas generowania adresu URL podczas routingu podane wartości muszą być zgodne z wartościami domyślnymi. Generowanie `blog` adresu URL `{ controller = Home, action = Index }` za pomocą `{ controller = Blog, action = Article }`nie powiedzie się, ponieważ wartości nie są zgodne . Routing następnie spada `default`z powrotem, aby spróbować , co powiedzie się.

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a>Obszary

[Obszary](xref:mvc/controllers/areas) są funkcją MVC używaną do organizowania powiązanych funkcji w grupę jako osobną:

* Routing obszaru nazw dla akcji kontrolera.
* Struktura folderów dla widoków.

Korzystanie z obszarów umożliwia aplikacji wiele kontrolerów o tej samej nazwie, o ile mają różne obszary. Za pomocą obszarów tworzy hierarchię do celów routingu, dodając inny parametr trasy, `area` do `controller` i `action`. W tej sekcji omówiono sposób interakcji routingu z obszarami. Szczegółowe informacje na temat sposobu wykorzystania obszarów z widokami można znaleźć w części [Obszary.](xref:mvc/controllers/areas)

Poniższy przykład konfiguruje MVC do używania domyślnej trasy konwencjonalnej `area` i trasy dla nazwanej: `area` `Blog`

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

W poprzednim kodzie <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> jest wywoływana `"blog_route"`w celu utworzenia pliku . Drugi parametr `"Blog"`, to nazwa obszaru.

Podczas dopasowywania `/Manage/Users/AddUser`ścieżki `"blog_route"` ADRESU URL, `{ area = Blog, controller = Users, action = AddUser }`takiej jak, trasa generuje wartości trasy. Wartość `area` trasy jest wytwarzana przez `area`wartość domyślną dla . Trasa utworzona `MapAreaControllerRoute` przez jest równoważna następującej trasie:

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup2.cs?name=snippet2)]

`MapAreaControllerRoute`tworzy trasę przy użyciu zarówno wartości `area` domyślnej, jak i `Blog`ograniczenia dla używania podanej nazwy obszaru, w tym przypadku . Wartość domyślna zapewnia, że trasa `{ area = Blog, ... }`zawsze tworzy, `{ area = Blog, ... }` ograniczenie wymaga wartości generowania adresu URL.

Routing konwencjonalny jest zależny od zamówienia. Ogólnie rzecz biorąc, trasy z obszarami powinny być umieszczane wcześniej, ponieważ są bardziej szczegółowe niż trasy bez obszaru.

W poprzednim przykładzie wartości `{ area = Blog, controller = Users, action = AddUser }` trasy są zgodne z następującą czynnością:

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

Atrybut [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) oznacza kontroler jako część obszaru. Ten kontroler znajduje `Blog` się w obszarze. Kontrolery bez `[Area]` atrybutu nie są członkami żadnego obszaru `area` i **nie** są zgodne, gdy wartość trasy jest dostarczana przez routing. W poniższym przykładzie tylko pierwszy kontroler na `{ area = Blog, controller = Users, action = AddUser }`liście może być zgodny z wartościami trasy .

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

Obszar nazw każdego kontrolera jest pokazany w tym miejscu dla kompletności. Jeśli poprzednie kontrolery używają tego samego obszaru nazw, zostanie wygenerowany błąd kompilatora. Przestrzenie nazw klas nie mają wpływu na routing MVC.

Pierwsze dwa kontrolery są członkami obszarów i są zgodne tylko `area` wtedy, gdy ich nazwa obszaru jest podana przez wartość trasy. Trzeci kontroler nie jest członkiem dowolnego obszaru i może być `area` dopasowany tylko wtedy, gdy żadna wartość nie jest dostarczana przez routing.

<a name="aa"></a>

Pod względem dopasowania *bez wartości,* `area` brak wartości jest taki sam, jak gdyby wartość dla `area` były null lub pusty ciąg.

Podczas wykonywania akcji wewnątrz obszaru, wartość `area` trasy jest dostępna jako [wartość otoczenia](#ambient) dla routingu do użycia do generowania adresu URL. Oznacza to, że domyślnie obszary działają *lepkie* dla generowania adresów URL, jak pokazano w poniższym przykładzie.

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup3.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

Poniższy kod generuje adres `/Zebra/Users/AddUser`URL do:

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/HomeController.cs?name=snippet)]

<a name="action"></a>

## <a name="action-definition"></a>Definicja akcji

Metody publiczne na kontrolerze, z wyjątkiem tych z atrybutem [NonAction,](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) są akcje.

## <a name="sample-code"></a>Przykładowy kod

 * [Metoda MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) jest uwzględniona w [przykładowym pobieraniu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) i jest używana do wyświetlania informacji o routingu.
* [Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([jak pobrać](xref:index#how-to-download-a-sample))

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core MVC używa [oprogramowania pośredniczącego](xref:fundamentals/middleware/index) Routing do dopasowania adresów URL przychodzących żądań i mapowania ich na akcje. Trasy są definiowane w kodzie startowym lub atrybutach. Trasy opisują, jak ścieżki adresów URL powinny być dopasowywać do akcji. Trasy są również używane do generowania adresów URL (dla linków) wysyłanych w odpowiedziach.

Akcje są kierowane konwencjonalnie lub są kierowane. Umieszczenie trasy na kontrolerze lub akcji sprawia, że atrybut jest kierowany. Zobacz [Routing mieszany, aby](#routing-mixed-ref-label) uzyskać więcej informacji.

W tym dokumencie opisano interakcje między MVC i routingu i jak typowe aplikacje MVC korzystać z funkcji routingu. Zobacz [Routing, aby](xref:fundamentals/routing) uzyskać szczegółowe informacje na temat routingu zaawansowanego.

## <a name="setting-up-routing-middleware"></a>Konfigurowanie oprogramowania pośredniczącego routingu

W metodzie *Konfigurowanie* może być widoczny kod podobny do:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Wewnątrz wywołania `UseMvc` `MapRoute` do , służy do tworzenia jednej trasy, która `default` będziemy odnosić się do trasy. Większość aplikacji MVC użyje trasy z `default` szablonem podobnym do trasy.

Szablon `"{controller=Home}/{action=Index}/{id?}"` trasy może odpowiadać `/Products/Details/5` ścieżce adresu URL, takiej jak i wyodrębni wartości `{ controller = Products, action = Details, id = 5 }` trasy przez tokenizację ścieżki. MVC spróbuje zlokalizować `ProductsController` kontroler o `Details`nazwie i uruchomić akcję:

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

Należy zauważyć, że w tym przykładzie `id = 5` powiązanie `id` modelu `5` będzie używać wartości, aby ustawić parametr podczas wywoływania tej akcji. Zobacz [powiązanie modelu, aby](../models/model-binding.md) uzyskać więcej informacji.

Korzystanie `default` z trasy:

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

Szablon trasy:

* `{controller=Home}`definiuje `Home` jako domyślny`controller`

* `{action=Index}`definiuje `Index` jako domyślny`action`

* `{id?}`definiuje `id` jako opcjonalne

Domyślne i opcjonalne parametry trasy nie muszą znajdować się w ścieżce adresu URL dopasowania. Zobacz [Odwołanie do szablonu trasy,](xref:fundamentals/routing#route-template-reference) aby uzyskać szczegółowy opis składni szablonu trasy.

`"{controller=Home}/{action=Index}/{id?}"`może odpowiadać `/` ścieżce adresu URL `{ controller = Home, action = Index }`i będzie tworzyć wartości trasy . Wartości i `controller` `action` używać wartości domyślnych, `id` nie daje wartość, ponieważ nie ma odpowiedniego segmentu w ścieżce adresu URL. MVC użyje tych wartości `HomeController` trasy, aby wybrać i `Index` akcji:

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

Przy użyciu tej definicji `HomeController.Index` kontrolera i szablonu trasy akcja zostanie wykonana dla dowolnej z następujących ścieżek adresu URL:

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

Metoda `UseMvcWithDefaultRoute`wygody:

```csharp
app.UseMvcWithDefaultRoute();
```

Może być stosowany do wymiany:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

`UseMvc`i `UseMvcWithDefaultRoute` dodaj wystąpienie `RouterMiddleware` do potoku oprogramowania pośredniczącego. MVC nie wchodzi w interakcje bezpośrednio z oprogramowaniem pośredniczącym i używa routingu do obsługi żądań. MVC jest połączony z trasami `MvcRouteHandler`za pośrednictwem wystąpienia . Kod wewnątrz `UseMvc` jest podobny do następującego:

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

`UseMvc`nie definiuje bezpośrednio żadnych tras, dodaje symbol zastępczy do `attribute` kolekcji trasy dla trasy. Przeciążenie `UseMvc(Action<IRouteBuilder>)` umożliwia dodawanie własnych tras, a także obsługuje routing atrybutów.  `UseMvc`a wszystkie jego odmiany dodają symbol zastępczy dla trasy atrybutu — routing atrybutów jest zawsze dostępny niezależnie od konfiguracji `UseMvc`. `UseMvcWithDefaultRoute`definiuje trasę domyślną i obsługuje routing atrybutów. Sekcja [Routing atrybutów](#attribute-routing-ref-label) zawiera więcej szczegółów dotyczących routingu atrybutów.

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a>Routing konwencjonalny

Trasa: `default`

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

Poprzedni kod jest przykładem routingu konwencjonalnego. Ten styl jest nazywany routingiem konwencjonalnym, ponieważ ustanawia *konwencję* dla ścieżek adresów URL:

* Pierwszy segment ścieżki jest mapowana na nazwę kontrolera.
* Drugi mapuje do nazwy akcji.
* Trzeci segment jest używany `id`do opcjonalnego . `id`mapuje do jednostki modelu.

Korzystając `default` z tej trasy, `/Products/List` ścieżka `ProductsController.List` adresu `/Blog/Article/17` URL `BlogController.Article`jest mapowana do akcji i mapuje na . To mapowanie jest oparte **tylko** na nazwach kontrolera i akcji i nie jest oparte na przestrzeniach nazw, lokalizacjach plików źródłowych ani parametrach metody.

> [!TIP]
> Korzystanie z routingu konwencjonalnego z trasą domyślną umożliwia szybkie tworzenie aplikacji bez konieczności tworzenia nowego wzorca adresu URL dla każdej zdefiniowanej akcji. W przypadku aplikacji z akcjami stylu CRUD spójność adresów URL między kontrolerami może pomóc uprościć kod i uczynić interfejs użytkownika bardziej przewidywalnym.

> [!WARNING]
> Jest `id` zdefiniowany jako opcjonalny przez szablon trasy, co oznacza, że akcje można wykonać bez identyfikatora podanego jako część adresu URL. Zwykle to, `id` co się stanie, jeśli zostanie pominięty `0` w adresie URL jest to, że będzie `id == 0`on ustawiony na powiązanie modelu, a w rezultacie żadna jednostka nie zostanie znaleziona w dopasowywaniu bazy danych . Routing atrybutów może dać szczegółową kontrolę, aby identyfikator wymagany dla niektórych akcji, a nie dla innych. Zgodnie z konwencją dokumentacja `id` będzie zawierać parametry opcjonalne, takie jak wtedy, gdy mogą pojawić się w prawidłowym użyciu.

## <a name="multiple-routes"></a>Wiele tras

Można dodać wiele `UseMvc` tras wewnątrz, `MapRoute`dodając więcej połączeń do . W ten sposób można zdefiniować wiele konwencji lub dodać konwencjonalne trasy, które są dedykowane do określonej akcji, takich jak:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Trasa `blog` jest tutaj *dedykowaną trasą konwencjonalną,* co oznacza, że korzysta z konwencjonalnego systemu wyznaczania tras, ale jest dedykowana konkretnej akcji. Ponieważ `controller` `action` i nie pojawiają się w szablonie trasy jako parametry, mogą mieć tylko wartości domyślne, a zatem ta trasa będzie zawsze mapować do akcji `BlogController.Article`.

Trasy w kolekcji trasy są uporządkowane i będą przetwarzane w kolejności, w jakiej są dodawane. Tak więc w `blog` tym przykładzie trasa `default` zostanie wypróbowana przed trasą.

> [!NOTE]
> *Dedykowane konwencjonalne trasy* często używają `{*article}` parametrów trasy typu **catch-all,** takich jak przechwytywanie pozostałej części ścieżki adresu URL. Może to sprawić, że trasa będzie "zbyt chciwy", co oznacza, że pasuje do adresów URL, które mają być dopasowane do innych tras. Umieść "chciwe" trasy w dalszej części tabeli tras, aby rozwiązać ten problem.

### <a name="fallback"></a>Opcja rezerwowa

W ramach przetwarzania żądań MVC sprawdzi, czy wartości trasy mogą służyć do znajdowania kontrolera i akcji w aplikacji. Jeśli wartości trasy nie pasują do akcji, trasa nie jest uważana za dopasowanie, a następna trasa zostanie wypróbowana. Jest to nazywane *rezerwowym*i ma na celu uproszczenie przypadków, w których konwencjonalne trasy nakładają się na siebie.

### <a name="disambiguating-actions"></a>Rozróżnianie działań

Gdy dwie akcje są zgodne za pośrednictwem routingu, MVC musi rozróżniać, aby wybrać "najlepszego" kandydata lub zgłosić wyjątek. Przykład:

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

Ten kontroler definiuje dwie akcje, które `/Products/Edit/17` pasują `{ controller = Products, action = Edit, id = 17 }`do ścieżki adresu URL i danych trasy . Jest to typowy wzorzec dla `Edit(int)` kontrolerów MVC, gdzie `Edit(int, Product)` pokazuje formularz do edycji produktu i przetwarza zaksięgowanego formularza. Aby to możliwe MVC musiałby `Edit(int, Product)` wybrać, kiedy `POST` żądanie `Edit(int)` jest HTTP i gdy zlecenie HTTP jest cokolwiek innego.

( `HttpPostAttribute` `[HttpPost]` ) jest `IActionConstraint` implementacją, która pozwoli na działanie, które mają `POST`być wybrane tylko wtedy, gdy czasownik HTTP jest . Obecność sprawia, `IActionConstraint` że `Edit(int, Product)` "lepiej" mecz `Edit(int)`niż `Edit(int, Product)` , więc będzie sądzony pierwszy.

Będzie trzeba tylko napisać `IActionConstraint` implementacje niestandardowe w wyspecjalizowanych scenariuszach, ale `HttpPostAttribute` ważne jest, aby zrozumieć rolę atrybutów, takich jak — podobne atrybuty są zdefiniowane dla innych zleceń HTTP. W konwencjonalnym routingu jest często dla akcji, aby użyć tej `show form -> submit form` samej nazwy akcji, gdy są one częścią przepływu pracy. Wygoda tego wzorca stanie się bardziej widoczne po przejrzeniu [zrozumienia IActionConstraint](#understanding-iactionconstraint) sekcji.

Jeśli wiele tras pasuje, a MVC nie może znaleźć "najlepszej" trasy, wrzuci . `AmbiguousActionException`

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a>Nazwy tras

Ciągi `"blog"` i `"default"` w poniższych przykładach są nazwy tras:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Nazwy trasy nadają trasie nazwę logiczną, dzięki czemu nazwana trasa może być używana do generowania adresów URL. Znacznie upraszcza to tworzenie adresów URL, gdy kolejność tras może skomplikować generowanie adresów URL. Nazwy tras muszą być unikatowe dla całej aplikacji.

Nazwy tras nie mają wpływu na dopasowywanie adresów URL lub obsługę żądań; są one używane tylko do generowania adresów URL. [Routing](xref:fundamentals/routing) ma bardziej szczegółowe informacje na temat generowania adresów URL, w tym generowanie adresów URL w pomocnikach specyficznych dla MVC.

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a>Routing atrybutów

Routing atrybutów używa zestawu atrybutów do mapowania akcji bezpośrednio do szablonów tras. W poniższym `app.UseMvc();` przykładzie jest `Configure` używany w metodzie i nie trasa jest przekazywana. Będzie `HomeController` pasować do zestawu adresów URL `{controller=Home}/{action=Index}/{id?}` podobnych do tego, co będzie pasować do trasy domyślnej:

```csharp
public class HomeController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult Index()
   {
      return View();
   }
   [Route("Home/About")]
   public IActionResult About()
   {
      return View();
   }
   [Route("Home/Contact")]
   public IActionResult Contact()
   {
      return View();
   }
}
```

Akcja `HomeController.Index()` zostanie wykonana dla dowolnej ścieżki `/`adresu `/Home`URL `/Home/Index`, lub .

> [!NOTE]
> W tym przykładzie przedstawiono kluczową różnicę programowania między routingiem atrybutów a routingiem konwencjonalnym. Routing atrybutów wymaga więcej danych wejściowych, aby określić trasę; konwencjonalna trasa domyślna obsługuje trasy bardziej zwięźle. Jednak routing atrybutów umożliwia (i wymaga) precyzyjnej kontroli, które szablony tras mają zastosowanie do każdej akcji.

W celu routingu atrybutu nazwa kontrolera i nazwy akcji nie odgrywają **żadnej** roli, w której wybrana jest akcja. W tym przykładzie będą zgodne te same adresy URL, jak w poprzednim przykładzie.

```csharp
public class MyDemoController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult MyIndex()
   {
      return View("Index");
   }
   [Route("Home/About")]
   public IActionResult MyAbout()
   {
      return View("About");
   }
   [Route("Home/Contact")]
   public IActionResult MyContact()
   {
      return View("Contact");
   }
}
```

> [!NOTE]
> Powyższe szablony tras nie definiują parametrów trasy dla `action`, `area`i `controller`. W rzeczywistości te parametry trasy nie są dozwolone w trasach atrybutów. Ponieważ szablon trasy jest już skojarzony z akcją, nie ma sensu analizować nazwy akcji z adresu URL.

## <a name="attribute-routing-with-httpverb-attributes"></a>Routing atrybutów z atrybutami Http[Verb]

Routing atrybutów może również `Http[Verb]` korzystać `HttpPostAttribute`z atrybutów, takich jak . Wszystkie te atrybuty mogą akceptować szablon trasy. W tym przykładzie przedstawiono dwie akcje pasujące do tego samego szablonu trasy:

```csharp
[HttpGet("/products")]
public IActionResult ListProducts()
{
   // ...
}

[HttpPost("/products")]
public IActionResult CreateProduct(...)
{
   // ...
}
```

Dla ścieżki adresu `/products` `ProductsApi.ListProducts` URL, takiej jak akcja, `GET` zostanie `ProductsApi.CreateProduct` wykonana, gdy zlecenie HTTP `POST`jest i będzie wykonywane, gdy zlecenie HTTP jest . Routing atrybutów jako pierwszy pasuje do adresu URL względem zestawu szablonów tras zdefiniowanych przez atrybuty trasy. Gdy szablon trasy `IActionConstraint` pasuje, ograniczenia są stosowane w celu określenia, które akcje mogą być wykonywane.

> [!TIP]
> Podczas tworzenia interfejsu API REST, rzadko, że `[Route(...)]` należy użyć w metodzie akcji, jak akcja zaakceptuje wszystkie metody HTTP. Lepiej jest użyć bardziej szczegółowe, `Http*Verb*Attributes` aby być dokładne o tym, co obsługuje interfejs API. Klienci interfejsów API REST powinni wiedzieć, jakie ścieżki i zlecenia HTTP są mapowane na określone operacje logiczne.

Ponieważ trasa atrybutu ma zastosowanie do określonej akcji, łatwo jest wprowadzić parametry wymagane jako część definicji szablonu trasy. W tym `id` przykładzie jest wymagane jako część ścieżki adresu URL.

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

Akcja `ProductsApi.GetProduct(int)` zostanie wykonana dla ścieżki `/products/3` ADRESU URL, takiej `/products`jak ścieżka URL, taka jak . Zobacz [Routing,](xref:fundamentals/routing) aby uzyskać pełny opis szablonów tras i powiązanych opcji.

## <a name="route-name"></a>Nazwa trasy

Poniższy kod definiuje nazwę `Products_List` *trasy:*

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

Nazwy tras mogą być używane do generowania adresu URL na podstawie określonej trasy. Nazwy tras nie mają wpływu na zachowanie dopasowywania adresów URL routingu i są używane tylko do generowania adresów URL. Nazwy tras muszą być unikatowe dla całej aplikacji.

> [!NOTE]
> Kontrastuje to z konwencjonalną *trasą domyślną,* `id` która definiuje parametr jako opcjonalny (`{id?}`). Ta możliwość precyzyjnego określania interfejsów API ma `/products` `/products/5` zalety, takie jak zezwalanie i wysyłanie do różnych akcji.

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a>Łączenie tras

Aby routing atrybutów był mniej powtarzalny, atrybuty trasy na kontrolerze są łączone z atrybutami trasy dla poszczególnych akcji. Wszystkie szablony tras zdefiniowane na kontrolerze są poprzedzane do szablonów tras w akcjach. Umieszczenie atrybutu trasy na kontrolerze powoduje, że **wszystkie** akcje w routingu atrybutu używanego przez kontrolera.

```csharp
[Route("products")]
public class ProductsApiController : Controller
{
   [HttpGet]
   public IActionResult ListProducts() { ... }

   [HttpGet("{id}")]
   public ActionResult GetProduct(int id) { ... }
}
```

W tym przykładzie `/products` ścieżka `ProductsApi.ListProducts`adresu URL `/products/5` może `ProductsApi.GetProduct(int)`być zgodna, a ścieżka adresu URL może być zgodna . Obie te akcje są `GET` zgodne tylko z `HttpGetAttribute`protokołem HTTP, ponieważ są oznaczone symbolem .

Szablony tras zastosowane do akcji, `/` `~/` które rozpoczynają się lub nie są łączone z szablonami tras zastosowanymi do kontrolera. W tym przykładzie jest zgodny z zestawem ścieżek URL podobnych do *trasy domyślnej*.

```csharp
[Route("Home")]
public class HomeController : Controller
{
    [Route("")]      // Combines to define the route template "Home"
    [Route("Index")] // Combines to define the route template "Home/Index"
    [Route("/")]     // Doesn't combine, defines the route template ""
    public IActionResult Index()
    {
        ViewData["Message"] = "Home index";
        var url = Url.Action("Index", "Home");
        ViewData["Message"] = "Home index" + "var url = Url.Action; =  " + url;
        return View();
    }

    [Route("About")] // Combines to define the route template "Home/About"
    public IActionResult About()
    {
        return View();
    }   
}
```

<a name="routing-ordering-ref-label"></a>

### <a name="ordering-attribute-routes"></a>Zamawianie tras atrybutów

W przeciwieństwie do konwencjonalnych tras, które są wykonywane w zdefiniowanej kolejności, routing atrybutów tworzy drzewo i dopasowuje wszystkie trasy jednocześnie. To zachowuje się tak, jakby wpisy trasy zostały umieszczone w idealnym zamawianiu; najbardziej konkretne trasy mają szansę wykonać przed bardziej ogólnymi trasami.

Na przykład trasa `blog/search/{topic}` podobna jest bardziej `blog/{*article}`szczegółowa niż trasa, taka jak . Logicznie rzecz `blog/search/{topic}` biorąc trasa "działa" jako pierwsza, domyślnie, ponieważ jest to jedyna rozsądna kolejność. Przy użyciu konwencjonalnego routingu deweloper jest odpowiedzialny za umieszczanie tras w żądanej kolejności.

Trasy atrybutów można skonfigurować kolejność, przy użyciu `Order` właściwości wszystkich atrybutów trasy pod warunkiem struktury. Trasy są przetwarzane zgodnie z rosnącym `Order` rodzajem właściwości. Domyślna kolejność `0`to . Ustawienie trasy `Order = -1` przy użyciu będzie działać przed trasami, które nie ustawiają zamówienia. Ustawienie trasy `Order = 1` przy użyciu będzie działać po domyślnej kolejności trasy.

> [!TIP]
> Unikaj w `Order`zależności od . Jeśli miejsce w adresie URL wymaga jawnych wartości zamówienia do prawidłowego rozsyłania, prawdopodobnie jest to również mylące dla klientów. Ogólnie rzecz biorąc routing atrybutów wybierze prawidłową trasę z dopasowaniem adresu URL. Jeśli domyślna kolejność używana do generowania adresów URL nie działa, użycie nazwy trasy `Order` jako zastąpienia jest zwykle prostsze niż stosowanie właściwości.

Routing stron razor i routing kontrolera MVC współużytkują implementację. Informacje o kolejności trasy w tematach Strony razor są dostępne w [konwencji trasy Razor Pages i konwencji aplikacji: Kolejność tras](xref:razor-pages/razor-pages-conventions#route-order).

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a>Zastępowanie tokenów w szablonach tras ([kontroler], [akcja], [obszar])

Dla wygody trasy atrybutów obsługują *zastępowanie tokenów* przez`[`załączanie tokenu w nawiasach kwadratowych ( , `]`). Tokeny `[action]`, `[area]`i `[controller]` są zastępowane wartościami nazwy akcji, nazwy obszaru i nazwy kontrolera z akcji, w której trasa jest zdefiniowana. W poniższym przykładzie akcje są zgodne ze ścieżkami adresu URL zgodnie z opisem w komentarzach:

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

Zastępowanie tokenu występuje jako ostatni krok tworzenia tras atrybutu. Powyższy przykład będzie zachowywać się tak samo jak następujący kod:

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

Trasy atrybutów można również łączyć z dziedziczeniem. Jest to szczególnie potężne w połączeniu z wymianą tokenów.

```csharp
[Route("api/[controller]")]
public abstract class MyBaseController : Controller { ... }

public class ProductsController : MyBaseController
{
   [HttpGet] // Matches '/api/Products'
   public IActionResult List() { ... }

   [HttpPut("{id}")] // Matches '/api/Products/{id}'
   public IActionResult Edit(int id) { ... }
}
```

Zastępowanie tokenów ma również zastosowanie do nazw tras zdefiniowanych przez trasy atrybutów. `[Route("[controller]/[action]", Name="[controller]_[action]")]`generuje unikatową nazwę trasy dla każdej akcji.

Aby dopasować dosłowny `[` ogranicznik zastępujący token lub `]`, ucieknij go, powtarzając znak (`[[` lub `]]`).

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a>Dostosowywanie zastępowania tokenów za pomocą transformatora parametrów

Zastąpienie tokenu można dostosować za pomocą transformatora parametrów. Transformator parametrów `IOutboundParameterTransformer` implementuje i przekształca wartość parametrów. Na przykład transformator parametrów `SlugifyParameterTransformer` `SubscriptionManagement` niestandardowych `subscription-management`zmienia wartość trasy na .

Jest `RouteTokenTransformerConvention` to konwencja modelu aplikacji, która:

* Stosuje transformator parametrów do wszystkich tras atrybutów w aplikacji.
* Dostosowuje wartości tokenu marszruty atrybutu podczas ich zastępowyjca.

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

Jest `RouteTokenTransformerConvention` zarejestrowany jako opcja `ConfigureServices`w .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options =>
    {
        options.Conventions.Add(new RouteTokenTransformerConvention(
                                     new SlugifyParameterTransformer()));
    });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

::: moniker-end


::: moniker range="< aspnetcore-3.0"
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-routes"></a>Wiele tras

Routing atrybutów obsługuje definiowanie wielu tras, które osiągają tę samą akcję. Najczęstszym zastosowaniem tego jest naśladować zachowanie *domyślnej trasy konwencjonalnej,* jak pokazano w poniższym przykładzie:

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

Umieszczenie wielu atrybutów trasy na kontrolerze oznacza, że każdy z nich będzie łączyć z każdym z atrybutów trasy w metodach akcji.

```csharp
[Route("Store")]
[Route("[controller]")]
public class ProductsController : Controller
{
   [HttpPost("Buy")]     // Matches 'Products/Buy' and 'Store/Buy'
   [HttpPost("Checkout")] // Matches 'Products/Checkout' and 'Store/Checkout'
   public IActionResult Buy()
}
```

Gdy wiele atrybutów `IActionConstraint`trasy (które implementują) są umieszczane na akcję, a następnie każde ograniczenie akcji łączy się z szablonem trasy z atrybutu, który go zdefiniował.

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
   [HttpPut("Buy")]      // Matches PUT 'api/Products/Buy'
   [HttpPost("Checkout")] // Matches POST 'api/Products/Checkout'
   public IActionResult Buy()
}
```

> [!TIP]
> Podczas korzystania z wielu tras na akcje mogą wydawać się zaawansowane, lepiej zachować przestrzeń adresów URL aplikacji proste i dobrze zdefiniowane. Użyj wielu tras w akcjach tylko wtedy, gdy jest to potrzebne, na przykład do obsługi istniejących klientów.

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a>Określanie parametrów opcjonalnych trasy atrybutu, wartości domyślnych i ograniczeń

Trasy atrybutów obsługują tę samą składnię wbudowaną co konwencjonalne trasy, aby określić parametry opcjonalne, wartości domyślne i ograniczenia.

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

Zobacz [Odwołanie do szablonu trasy,](xref:fundamentals/routing#route-template-reference) aby uzyskać szczegółowy opis składni szablonu trasy.

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a>Niestandardowe atrybuty trasy przy użyciu`IRouteTemplateProvider`

Wszystkie atrybuty trasy podane w `[Route(...)]` `[HttpGet(...)]` ramach ( , `IRouteTemplateProvider` itp.) implementują interfejs. MVC wyszukuje atrybuty na klasy kontrolera i metody akcji `IRouteTemplateProvider` podczas uruchamiania aplikacji i używa tych, które implementują do tworzenia początkowego zestawu tras.

Można zaimplementować, `IRouteTemplateProvider` aby zdefiniować własne atrybuty trasy. Każdy `IRouteTemplateProvider` z nich umożliwia zdefiniowanie pojedynczej trasy z niestandardowym szablonem trasy, kolejnością i nazwą:

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

Atrybut z powyższego przykładu `Template` automatycznie `"api/[controller]"` ustawia `[MyApiController]` na kiedy jest stosowany.

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a>Dostosowywanie tras atrybutów za pomocą modelu aplikacji

*Model aplikacji* jest modelem obiektowym utworzonym podczas uruchamiania ze wszystkimi metadanymi używanymi przez MVC do kierowania i wykonywania akcji. *Model aplikacji* zawiera wszystkie dane zebrane z atrybutów trasy (przez `IRouteTemplateProvider`). Można napisać *konwencje,* aby zmodyfikować model aplikacji w czasie uruchamiania, aby dostosować zachowanie routingu. W tej sekcji przedstawiono prosty przykład dostosowywania routingu przy użyciu modelu aplikacji.

[!code-csharp[](routing/samples/2.x/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a>Routing mieszany: routing atrybutów a routing konwencjonalny

Aplikacje MVC można mieszać użycie konwencjonalnych routingu i routingu atrybutów. Typowe jest używanie konwencjonalnych tras dla kontrolerów obsługujących strony HTML dla przeglądarek i routingu atrybutów dla kontrolerów obsługujących interfejsy API REST.

Akcje są kierowane konwencjonalnie lub są kierowane. Umieszczenie trasy na kontrolerze lub akcji sprawia, że atrybut jest kierowany. Akcje definiujące trasy atrybutów nie mogą być osiągane za pośrednictwem tras konwencjonalnych i odwrotnie. **Każdy** atrybut trasy na kontrolerze sprawia, że wszystkie akcje w atrybucie kontrolera kierowane.

> [!NOTE]
> To, co odróżnia dwa typy systemów routingu, to proces stosowany po dopasowaniu adresu URL do szablonu trasy. W routingu konwencjonalnym wartości trasy z dopasowania są używane do wybierania akcji i kontrolera z tabeli odnośnych wszystkich konwencjonalnych akcji kierowanych. W routingu atrybutów każdy szablon jest już skojarzony z akcją i nie jest potrzebne dalsze wyszukiwanie.

## <a name="complex-segments"></a>Segmenty złożone

Złożone segmenty (na `[Route("/dog{token}cat")]`przykład ) są przetwarzane przez dopasowanie literałów od prawej do lewej w sposób niechciwy. Zobacz [kod źródłowy](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) opisu. Aby uzyskać więcej informacji, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/8197).

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a>Generowanie adresu URL

Aplikacje MVC mogą używać funkcji generowania adresów URL routingu do generowania łączy adresów URL do akcji. Generowanie adresów URL eliminuje adresy URL hardcoding, dzięki czemu kod jest bardziej niezawodny i niezawodny. W tej sekcji skupiono się na funkcjach generowania adresów URL dostarczonych przez MVC i będzie obejmować tylko podstawy działania generowania adresów URL. Zobacz [Routing, aby](xref:fundamentals/routing) uzyskać szczegółowy opis generowania adresów URL.

Interfejs `IUrlHelper` jest podstawowym elementem infrastruktury między MVC i routingu do generowania adresów URL. Znajdziesz wystąpienie `IUrlHelper` dostępne za pośrednictwem `Url` właściwości w kontrolerach, widokach i składnikach widoku.

W tym przykładzie `IUrlHelper` interfejs jest `Controller.Url` używany za pośrednictwem właściwości do generowania adresu URL do innej akcji.

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

Jeśli aplikacja używa domyślnej trasy konwencjonalnej, `url` wartością zmiennej `/UrlGeneration/Destination`będzie ciąg ścieżki adresu URL . Ta ścieżka adresu URL jest tworzona przez routing przez połączenie wartości trasy `Url.Action` z bieżącego żądania (wartości otoczenia) z wartościami przekazanymi do szablonu trasy i zastąpienie ich:

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

Każdy parametr trasy w szablonie trasy ma swoją wartość zastąpiona pasującymi nazwami z wartościami i wartościami otoczenia. Parametr trasy, który nie ma wartości, może użyć wartości domyślnej, jeśli ją ma, lub zostać `id` pominięty, jeśli jest opcjonalny (jak w tym przykładzie). Generowanie adresu URL zakończy się niepowodzeniem, jeśli dowolny wymagany parametr trasy nie ma odpowiedniej wartości. Jeśli generowanie adresu URL nie powiedzie się dla trasy, następna trasa jest wypróbowywych, dopóki nie zostanie wypróbowane wszystkie trasy lub zostanie znalezione dopasowanie.

W powyższym `Url.Action` przykładzie przyjęto założenie, że routing konwencjonalny, ale generowanie adresów URL działa podobnie z routingiem atrybutów, chociaż pojęcia są różne. W przypadku routingu konwencjonalnego wartości trasy są używane do `controller` `action` rozwijania szablonu, a wartości trasy dla i zwykle pojawiają się w tym szablonie — działa to, ponieważ adresy URL dopasowane przez routing są zgodne z *konwencją*. W routingu atrybutów wartości `controller` `action` trasy i nie mogą być wyświetlane w szablonie — zamiast tego są używane do wyszukiwania szablonu, którego chcesz użyć.

W tym przykładzie użyto routingu atrybutów:

[!code-csharp[](routing/samples/2.x/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

MVC tworzy tabelę odnośną wszystkich akcji kierowanych `controller` `action` atrybutami i będzie pasować do wartości i wartości, aby wybrać szablon trasy do generowania adresu URL. W powyższej `custom/url/to/destination` próbce jest generowany.

### <a name="generating-urls-by-action-name"></a>Generowanie adresów URL według nazwy akcji

`Url.Action` (`IUrlHelper` . `Action`) i wszystkie powiązane przeciążenia są oparte na tym pomyśle, który chcesz określić, do czego łączysz, określając nazwę kontrolera i nazwę akcji.

> [!NOTE]
> Podczas `Url.Action`korzystania z , `controller` bieżące wartości trasy `action` dla `controller` i `action` są określone dla Ciebie - wartość i są częścią wartości *otoczenia* **i** *wartości*. Metoda `Url.Action`, zawsze używa bieżących `action` `controller` wartości i wygeneruje ścieżkę adresu URL, która kieruje do bieżącej akcji.

Routing próbuje użyć wartości w wartościach otoczenia, aby wypełnić informacje, które nie zostały podane podczas generowania adresu URL. Przy użyciu `{a}/{b}/{c}/{d}` wartości `{ a = Alice, b = Bob, c = Carol, d = David }`podobnych i otaczających trasy routing ma wystarczającą ilość informacji, aby wygenerować adres URL bez żadnych dodatkowych wartości — ponieważ wszystkie parametry trasy mają wartość. Jeśli wartość `{ d = Donovan }`zostanie dodana, wartość `{ d = David }` zostanie zignorowana, a `Alice/Bob/Carol/Donovan`wygenerowana ścieżka adresu URL będzie .

> [!WARNING]
> Ścieżki adresów URL są hierarchiczne. W powyższym przykładzie, jeśli `{ c = Cheryl }`dodano wartość, `{ c = Carol, d = David }` obie wartości zostaną zignorowane. W takim przypadku nie mamy `d` już wartości dla i generowanie adresów URL zakończy się niepowodzeniem. Należy określić żądaną wartość `c` `d`i .  Można oczekiwać, aby trafić ten`{controller}/{action}/{id?}`problem z domyślną trasą ( `Url.Action` ) - ale `controller` `action` rzadko można spotkać to zachowanie w praktyce, jak zawsze jawnie określić i wartość.

Dłuższe `Url.Action` przeciążenia również podjąć dodatkowe wartości *trasy* obiektu, `controller` aby `action`podać wartości dla parametrów trasy innych niż i . Najczęściej widać to używane z `id` `Url.Action("Buy", "Products", new { id = 17 })`like . Zgodnie z konwencją obiekt *wartości trasy* jest zwykle obiektem `IDictionary<>` typu anonimowego, ale może być również *zwykłym lub starym obiektem .NET.* Wszelkie dodatkowe wartości trasy, które nie pasują do parametrów trasy są umieszczane w ciągu zapytania.

[!code-csharp[](routing/samples/2.x/main/Controllers/TestController.cs)]

> [!TIP]
> Aby utworzyć bezwzględny adres URL, użyj `protocol`przeciążenia, które akceptuje:`Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a>Generowanie adresów URL według trasy

Powyższy kod wykazał generowanie adresu URL przez przekazanie w kontrolerze i nazwę akcji. `IUrlHelper`zapewnia również `Url.RouteUrl` rodzinę metod. Metody te są `Url.Action`podobne do , ale nie `action` kopiują bieżące wartości i `controller` do wartości trasy. Najczęstszym zastosowaniem jest określenie nazwy trasy, aby użyć określonej trasy do wygenerowania adresu URL, zazwyczaj *bez* określania nazwy kontrolera lub akcji.

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a>Generowanie adresów URL w formacie HTML

`IHtmlHelper``HtmlHelper` zawiera metody `Html.BeginForm` `Html.ActionLink` oraz `<form>` do `<a>` generowania i elementów odpowiednio. Metody te `Url.Action` używają metody do generowania adresu URL i akceptują podobne argumenty. Towarzysze `Url.RouteUrl` są `HtmlHelper` `Html.BeginRouteForm` i `Html.RouteLink` które mają podobną funkcjonalność.

TagHelpers generowania adresów URL za pośrednictwem `form` TagHelper i `<a>` TagHelper. Oba te `IUrlHelper` zastosowania do ich realizacji. Aby uzyskać więcej informacji, zobacz [Praca z formularzami.](../views/working-with-forms.md)

Widoki wewnątrz `IUrlHelper` jest dostępny `Url` za pośrednictwem właściwości dla każdego generowania adresów URL ad hoc nie objęte powyższe.

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a>Generowanie adresów URL w wynikach akcji

Powyższe przykłady zostały `IUrlHelper` pokazane przy użyciu w kontrolerze, podczas gdy najczęstszym użyciem w kontrolerze jest generowanie adresu URL jako część wyniku akcji.

Klasy `ControllerBase` `Controller` i klasy podstawowe zapewniają wygodę metod wyników akcji, które odwołują się do innej akcji. Jednym z typowych zastosowań jest przekierowanie po zaakceptowaniu danych wejściowych użytkownika.

```csharp
public IActionResult Edit(int id, Customer customer)
{
    if (ModelState.IsValid)
    {
        // Update DB with new details.
        return RedirectToAction("Index");
    }
    return View(customer);
}
```

Metody fabryczne wyników akcji są zgodne `IUrlHelper`z podobnym wzorcem do metod na .

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a>Specjalny przypadek dla dedykowanych tras konwencjonalnych

Routing konwencjonalny może używać specjalnego rodzaju definicji trasy zwanej *dedykowaną trasą konwencjonalną.* W poniższym przykładzie `blog` nazwana trasa jest dedykowaną trasą konwencjonalną.

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Korzystając z tych `Url.Action("Index", "Home")` definicji trasy, `/` wygeneruje ścieżkę adresu URL z trasą, `default` ale dlaczego? Można się domyślać, że wartości `{ controller = Home, action = Index }` trasy `blog`wystarczą do wygenerowania adresu URL za pomocą , a wynikiem będzie `/blog?action=Index&controller=Home`.

Dedykowane konwencjonalne trasy opierają się na specjalnym zachowaniu wartości domyślnych, które nie mają odpowiedniego parametru trasy, który uniemożliwia trasę jako "zbyt chciwy" z generowaniem adresu URL. W tym przypadku wartościami domyślnymi są `{ controller = Blog, action = Article }`, i ani `controller` ani nie `action` pojawia się jako parametr trasy. Podczas generowania adresu URL podczas routingu podane wartości muszą być zgodne z wartościami domyślnymi. Generowanie `blog` adresów URL `{ controller = Home, action = Index }` za pomocą `{ controller = Blog, action = Article }`zakończy się niepowodzeniem, ponieważ wartości nie są zgodne . Routing następnie spada `default`z powrotem, aby spróbować , co powiedzie się.

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a>Obszary

[Obszary](areas.md) są funkcją MVC używaną do organizowania powiązanych funkcji w grupę jako oddzielny obszar nazw routingu (dla akcji kontrolera) i strukturę folderów (dla widoków). Korzystanie z obszarów pozwala aplikacji mieć wiele kontrolerów o tej samej nazwie - tak długo, jak mają różne *obszary.* Za pomocą obszarów tworzy hierarchię do celów routingu, dodając inny parametr trasy, `area` do `controller` i `action`. W tej sekcji omówimy interakcje routingu z obszarami — zobacz [Obszary, aby](areas.md) uzyskać szczegółowe informacje o sposobie wykorzystania obszarów z widokami.

W poniższym przykładzie konfiguruje MVC użycie domyślnej trasy `Blog`konwencjonalnej i trasy *obszaru* dla obszaru o nazwie:

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

Podczas dopasowywania `/Manage/Users/AddUser`ścieżki adresu URL, takiej `{ area = Blog, controller = Users, action = AddUser }`jak, pierwsza trasa będzie tworzyć wartości trasy. Wartość `area` trasy jest tworzona `area`przez wartość domyślną `MapAreaRoute` dla , w rzeczywistości trasa utworzona przez jest równoważna następującej:

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet2)]

`MapAreaRoute`tworzy trasę przy użyciu zarówno wartości `area` domyślnej, jak i `Blog`ograniczenia dla używania podanej nazwy obszaru, w tym przypadku . Wartość domyślna zapewnia, że trasa `{ area = Blog, ... }`zawsze tworzy, `{ area = Blog, ... }` ograniczenie wymaga wartości generowania adresu URL.

> [!TIP]
> Routing konwencjonalny jest zależny od zamówienia. Ogólnie rzecz biorąc, trasy z obszarami powinny być umieszczane wcześniej w tabeli tras, ponieważ są bardziej szczegółowe niż trasy bez obszaru.

W powyższym przykładzie wartości trasy odpowiadałyby następującej akcji:

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

To, `AreaAttribute` co oznacza kontroler jako część obszaru, mówimy, że ten `Blog` kontroler jest w obszarze. Kontrolery bez `[Area]` atrybutu nie są członkami żadnego obszaru `area` i **nie** będą zgodne, gdy wartość trasy jest dostarczana przez routing. W poniższym przykładzie tylko pierwszy kontroler na `{ area = Blog, controller = Users, action = AddUser }`liście może być zgodny z wartościami trasy .

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> Obszar nazw każdego kontrolera jest wyświetlany w tym miejscu dla kompletności — w przeciwnym razie kontrolery będą miały konflikt nazewnictwa i wygenerować błąd kompilatora. Przestrzenie nazw klas nie mają wpływu na routing MVC.

Pierwsze dwa kontrolery są członkami obszarów i są zgodne tylko `area` wtedy, gdy ich nazwa obszaru jest podana przez wartość trasy. Trzeci kontroler nie jest członkiem dowolnego obszaru i może być `area` dopasowany tylko wtedy, gdy żadna wartość nie jest dostarczana przez routing.

> [!NOTE]
> Pod względem dopasowania *bez wartości,* `area` brak wartości jest taki sam, jak gdyby wartość dla `area` były null lub pusty ciąg.

Podczas wykonywania akcji wewnątrz obszaru, wartość `area` trasy dla będzie dostępna jako *wartość otoczenia* dla routingu do użycia do generowania adresu URL. Oznacza to, że domyślnie obszary działają *lepkie* dla generowania adresów URL, jak pokazano w poniższym przykładzie.
[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a>Opis IActionConstraint

> [!NOTE]
> Ta sekcja jest głębokie zagłębienie na wewnętrznych framework i jak MVC wybiera akcję do wykonania. Typowa aplikacja nie będzie potrzebowała`IActionConstraint`

Prawdopodobnie już używane, `IActionConstraint` nawet jeśli nie jesteś zaznajomiony z interfejsem. Atrybut `[HttpGet]` i podobne `[Http-VERB]` atrybuty implementują `IActionConstraint` w celu ograniczenia wykonywania metody akcji.

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

Przy założeniu domyślnej trasy `/Products/Edit` konwencjonalnej `{ controller = Products, action = Edit }`ścieżka adresu URL będzie tworzyć wartości, które pasują do **obu** akcji pokazanych w tym miejscu. W `IActionConstraint` terminologii chcielibyśmy powiedzieć, że oba te działania są uważane za kandydatów - ponieważ oba pasują do danych trasy.

Gdy `HttpGetAttribute` wykonuje, powie, że *Edit()* jest dopasowanie dla *GET* i nie jest zgodne dla innych zleceń HTTP. Akcja `Edit(...)` nie ma żadnych ograniczeń zdefiniowanych, a więc będzie pasować do dowolnego zlecenia HTTP. Więc zakładając `POST` - `Edit(...)` tylko mecze. Ale dla `GET` obu działań może jeszcze dopasować `IActionConstraint` - jednak akcja z akcją jest zawsze uważana za *lepszą* niż akcja bez. `Edit()` Dlatego, `[HttpGet]` że jest uważany za bardziej szczegółowe i zostanie wybrany, jeśli obie akcje mogą być zgodne.

Koncepcyjnie `IActionConstraint` jest formą *przeciążenia*, ale zamiast przeciążać metody o tej samej nazwie, jest przeciążenie między akcjami, które pasują do tego samego adresu URL. Routing atrybutów również `IActionConstraint` używa i może spowodować akcje z różnych kontrolerów zarówno uważane za kandydatów.

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a>Wdrażanie IActionConstraint

Najprostszym `IActionConstraint` sposobem zaimplementowania jest utworzenie `System.Attribute` klasy pochodzące z i umieścić go na akcje i kontrolerów. MVC automatycznie odnajduje wszystkie, `IActionConstraint` które są stosowane jako atrybuty. Model aplikacji służy do stosowania ograniczeń i jest to prawdopodobnie najbardziej elastyczne podejście, ponieważ umożliwia metaprogramowanie sposobu ich stosowania.

W poniższym przykładzie ograniczenie wybiera akcję na podstawie *kodu kraju* z danych trasy. [Pełna próbka na GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).

```csharp
public class CountrySpecificAttribute : Attribute, IActionConstraint
{
    private readonly string _countryCode;

    public CountrySpecificAttribute(string countryCode)
    {
        _countryCode = countryCode;
    }

    public int Order
    {
        get
        {
            return 0;
        }
    }

    public bool Accept(ActionConstraintContext context)
    {
        return string.Equals(
            context.RouteContext.RouteData.Values["country"].ToString(),
            _countryCode,
            StringComparison.OrdinalIgnoreCase);
    }
}
```

Użytkownik jest odpowiedzialny za `Accept` wdrożenie metody i wybranie "Order" dla ograniczenia do wykonania. W takim przypadku `Accept` metoda `true` powraca do oznaczenia akcji `country` jest dopasowanie, gdy wartość trasy pasuje. To różni się `RouteValueAttribute` od tego, że umożliwia powrót do akcji niepodana. Przykład pokazuje, że jeśli `en-US` zdefiniujesz `fr-FR` akcję, kod kraju, taki jak powróci `[CountrySpecific(...)]` do bardziej ogólnego kontrolera, który nie został zastosowany.

Właściwość `Order` decyduje, który *etap* ograniczenia jest częścią. Ograniczenia akcji są uruchamiane `Order`w grupach na podstawie pliku . Na przykład wszystkie atrybuty metody http dostarczone `Order` przez platformę HTTP używają tej samej wartości, dzięki czemu są uruchamiane na tym samym etapie. Możesz mieć tyle etapów, ile potrzeba do wdrożenia żądanych zasad.

> [!TIP]
> Aby zdecydować o `Order` wartości, aby zastanowić się, czy ograniczenie powinno być stosowane przed metodami HTTP. Najpierw działają niższe liczby.

::: moniker-end
