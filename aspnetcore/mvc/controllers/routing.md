---
title: Routing do akcji kontrolera w ASP.NET Core
author: rick-anderson
description: Dowiedz się, w jaki sposób ASP.NET Core MVC używa programów pośredniczących routingu, aby dopasować adresy URL żądań przychodzących i zmapować je na akcje.
ms.author: riande
ms.date: 3/25/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/controllers/routing
ms.openlocfilehash: 4208ef8fb7a9b10621f214f79679ff8d7fd83996
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775027"
---
# <a name="routing-to-controller-actions-in-aspnet-core"></a>Routing do akcji kontrolera w ASP.NET Core

[Ryan Nowak](https://github.com/rynowak), [Kirka Larkin](https://twitter.com/serpent5)i [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Kontrolery ASP.NET Core używają [oprogramowania pośredniczącego](xref:fundamentals/middleware/index) routingu do dopasowywania adresów URL żądań przychodzących i mapują je na [Akcje](#action).  Szablony tras:

* Są zdefiniowane w kodzie startowym lub atrybutów.
* Opisz, w jaki sposób ścieżki URL są dopasowywane do [akcji](#action).
* Służy do generowania adresów URL dla łączy. Wygenerowane linki są zwykle zwracane w odpowiedziach.

Akcje są albo [podkierowane do Konwencji](#cr) lub [kierowane przez atrybut](#ar). Umieszczenie trasy na kontrolerze lub [akcji](#action) sprawia, że jest ona kierowana przez atrybut. Aby uzyskać więcej informacji, zobacz [Routing mieszany](#routing-mixed-ref-label) .

Ten dokument:

* Wyjaśnia interakcje między MVC i Routing:
  * Jak typowe aplikacje MVC używają funkcji routingu.
  * Obejmuje zarówno:
    * [UNC routingu](#cr) zwykle używany z kontrolerami i widokami.
    * *Routing atrybutów* używany z interfejsami API REST. Jeśli jesteś głównie zainteresowani routingiem interfejsów API REST, przejdź do sekcji [Routing atrybutów dla interfejsów API REST](#ar) .
  * Szczegóły dotyczące routingu zaawansowanego znajdują się w temacie [Routing](xref:fundamentals/routing) .
* Odnosi się do domyślnego systemu routingu dodanego w ASP.NET Core 3,0, nazywanego routingiem punktu końcowego. Możliwe jest używanie kontrolerów z poprzednią wersją routingu w celu zapewnienia zgodności. Instrukcje można znaleźć w [przewodniku migracji 2.2-3.0](xref:migration/22-to-30) . Zapoznaj się z [wersją 2,2 tego dokumentu](xref:mvc/controllers/routing?view=aspnetcore-2.2) dotyczącą materiałów referencyjnych w starszym systemie routingu.

<a name="cr"></a>

## <a name="set-up-conventional-route"></a>Konfigurowanie trasy konwencjonalnej

`Startup.Configure`w przypadku korzystania z [konwencjonalnego routingu](#crd)zazwyczaj ma kod podobny do poniższego:

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet)]

Wewnątrz wywołania do <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> służy do tworzenia pojedynczej trasy. Pojedyncza trasa ma nazwę `default` Route. Większość aplikacji z kontrolerami i widokami używa szablonu trasy podobnego do `default` trasy. Interfejsy API REST powinny używać [routingu atrybutów](#ar).

Szablon `"{controller=Home}/{action=Index}/{id?}"`trasy:

* Dopasowuje ścieżkę URL, taką jak`/Products/Details/5`
* Wyodrębnia wartości `{ controller = Products, action = Details, id = 5 }` trasy przez tokenizowanie ścieżki. Wyodrębnienie wartości tras powoduje dopasowanie, jeśli aplikacja ma kontroler o nazwie `ProductsController` i `Details` akcję:

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippetA)]

  [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

* `/Products/Details/5`Model wiąże wartość, `id = 5` aby ustawić `id` parametr jako. `5` Aby uzyskać więcej informacji, zobacz [powiązanie modelu](xref:mvc/models/model-binding) .
* `{controller=Home}`definiuje `Home` jako domyślny `controller`.
* `{action=Index}`definiuje `Index` jako domyślny `action`.
*  `?` Znak w `{id?}` definiuje `id` jako opcjonalny.
  * Domyślne i opcjonalne parametry trasy nie muszą być obecne w ścieżce URL dla dopasowania. Aby uzyskać szczegółowy opis składni szablonu trasy, zobacz [odwołanie do szablonu trasy](xref:fundamentals/routing#route-template-reference) .
* Dopasowuje ścieżkę `/`URL.
* Tworzy wartości `{ controller = Home, action = Index }`trasy.

Wartości dla `controller` i `action` używają wartości domyślnych. `id`nie produkuje wartości, ponieważ nie ma odpowiedniego segmentu w ścieżce adresu URL. `/`dopasowuje się `HomeController` tylko wtedy, gdy `Index` istnieje Akcja i:

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

Przy użyciu definicji kontrolera i szablonu trasy, `HomeController.Index` akcja jest uruchamiana dla następujących ścieżek URL:

* `/Home/Index/17`
* `/Home/Index`
* `/Home`
* `/`

Ścieżka `/` adresu URL używa domyślnych `Home` kontrolerów i `Index` akcji szablonu trasy. Ścieżka `/Home` adresu URL używa domyślnej `Index` akcji szablonu trasy.

Wygodna metoda <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>:

```csharp
endpoints.MapDefaultControllerRoute();
```

Zastępując

```csharp
endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

> [!IMPORTANT]
> Routing jest konfigurowany przy użyciu <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> programu <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> i oprogramowania pośredniczącego. Aby użyć kontrolerów:
>
> * <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> Wywołaj `UseEndpoints` wewnątrz, aby zmapować kontrolery z [routingiem atrybutów](#ar) .
> * <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> Wywołaj <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>lub, aby zamapować kontrolery z [Konwencją routingu](#cr) .

<a name="routing-conventional-ref-label"></a>
<a name="crd"></a>

## <a name="conventional-routing"></a>Routing konwencjonalny

Routing konwencjonalny jest używany z kontrolerami i widokami. `default` Trasa:

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet2)]

jest przykładem *konwencjonalnego routingu*. Jest on nazywany *konwencjonalnym routingiem* , ponieważ ustanawia *Konwencję* dla ścieżek adresów URL:

* Pierwszy segment `{controller=Home}`ścieżki, mapuje na nazwę kontrolera.
* Drugi segment, `{action=Index}`, mapuje na nazwę [akcji](#action) .
* Trzeci segment `{id?}` jest używany jako opcjonalny `id`. `?` W programie `{id?}` w programie jest to opcjonalne. `id`służy do mapowania na jednostkę modelu.

Przy użyciu `default` tej trasy ścieżka URL:

* `/Products/List`mapuje do `ProductsController.List` akcji.
* `/Blog/Article/17`mapowanie na `BlogController.Article` model i zwykle wiąże się `id` z parametrem 17.

To mapowanie:

* Jest oparty **tylko**na nazwach kontrolera i [akcji](#action) .
* Nie jest oparty na przestrzeniach nazw, lokalizacjach plików źródłowych ani parametrach metod.

Użycie konwencjonalnego routingu z domyślną trasą umożliwia tworzenie aplikacji bez konieczności korzystania z nowego wzorca adresu URL dla każdej akcji. W przypadku aplikacji z akcjami w stylu [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) mających spójność dla adresów URL między kontrolerami:

* Upraszcza kod.
* Sprawia, że interfejs użytkownika jest bardziej przewidywalny.

> [!WARNING]
> `id` W powyższym kodzie jest zdefiniowany jako opcjonalny przez szablon trasy. Akcje można wykonać bez opcjonalnego identyfikatora podanego w ramach adresu URL. Ogólnie mówiąc,`id` gdy pominięto w adresie URL:
>
> * `id`jest ustawiony na `0` przez powiązanie modelu.
> * Nie znaleziono jednostki w dopasowaniu `id == 0`do bazy danych.
>
> [Routing atrybutu](#ar) zawiera szczegółowy formant, który umożliwia określanie identyfikatora wymaganego dla niektórych akcji, a nie dla innych. Zgodnie z Konwencją, dokumentacja zawiera opcjonalne parametry `id` , takie jak, gdy prawdopodobnie pojawi się w prawidłowym użyciu.

Większość aplikacji powinna wybrać podstawowy i opisowy schemat routingu, aby adresy URL były czytelne i zrozumiałe. Domyślna trasa `{controller=Home}/{action=Index}/{id?}`konwencjonalna:

* Obsługuje podstawowy i opisowy schemat routingu.
* Jest użytecznym punktem wyjścia dla aplikacji opartych na interfejsie użytkownika.
* Jest jedynym szablonem trasy wymaganym przez wiele aplikacji interfejsu użytkownika sieci Web. W przypadku większych aplikacji interfejsu użytkownika sieci Web inna trasa korzysta z [obszarów](#areas) , jeśli są one często używane.

<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>i <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> :

* Automatycznie Przypisz wartość **zamówienia** do punktów końcowych na podstawie kolejności, w której są wywoływane.

Routing punktów końcowych w ASP.NET Core 3,0 i nowszych:

* Nie ma koncepcji tras.
* Nie oferuje gwarancji porządkowania dla wykonywania rozszerzalności, wszystkie punkty końcowe są przetwarzane jednocześnie.

Włącz [Rejestrowanie](xref:fundamentals/logging/index) , aby zobaczyć, jak wbudowane implementacje routingu, takie jak <xref:Microsoft.AspNetCore.Routing.Route>, dopasowują żądania.

[Routing atrybutów](#ar) został wyjaśniony w dalszej części tego dokumentu.

<a name="mr"></a>

### <a name="multiple-conventional-routes"></a>Wiele konwencjonalnych tras

Wewnątrz `UseEndpoints` można dodać wiele [konwencjonalnych tras](#cr) , dodając więcej wywołań <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> do <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>i. Dzięki temu można definiować wiele Konwencji lub dodać do nich trasy konwencjonalne, które są przeznaczone dla konkretnej [akcji](#action), na przykład:

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<a name="dcr"></a>

`blog` Trasa w powyższym kodzie jest **dedykowaną konwencjonalną trasą**. Jest on nazywany dedykowaną trasą konwencjonalną, ponieważ:

* Używa ona [konwencjonalnego routingu](#cr).
* Jest on przeznaczony dla konkretnej [akcji](#action).

Ponieważ `controller` i `action` nie pojawiają się w szablonie `"blog/{*article}"` trasy jako parametry:

* Mogą mieć tylko wartości `{ controller = "Blog", action = "Article" }`domyślne.
* Ta trasa zawsze jest mapowana na akcję `BlogController.Article`.

`/Blog`, `/Blog/Article`i `/Blog/{any-string}` są JEDYNYMi ścieżkami URL, które pasują do trasy blogu.

Powyższy przykład:

* `blog`trasa ma wyższy priorytet dla dopasowania niż trasa, `default` ponieważ jest dodawana jako pierwsza.
* Jest i przykładem routingu stylów [informacji](https://developer.mozilla.org/docs/Glossary/Slug) o sobie, w którym typowym jest nazwa artykułu w ramach adresu URL.

> [!WARNING]
> W ASP.NET Core 3,0 i nowszych routingu nie są:
> * Zdefiniuj koncepcję o nazwie *trasa*. `UseRouting`dodaje dopasowanie trasy do potoku programu pośredniczącego. `UseRouting` Oprogramowanie pośredniczące sprawdza zestaw punktów końcowych zdefiniowanych w aplikacji i wybiera najlepsze dopasowanie punktu końcowego na podstawie żądania.
> * Podaj gwarancje dotyczące kolejności wykonywania rozszerzalności, <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> takich <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>jak lub.
>
>Zobacz [Routing](xref:fundamentals/routing) dla materiałów referencyjnych na trasie.

<a name="cro"></a>

### <a name="conventional-routing-order"></a>Tradycyjna kolejność routingu

Routing konwencjonalny pasuje do kombinacji akcji i kontrolera, które są zdefiniowane przez aplikację. Jest to przeznaczone do uproszczenia sytuacji, w których trasy konwencjonalne nakładają się na siebie.
Dodanie tras przy <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>użyciu <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>, i <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> automatycznie przypisanie wartości zamówienia do punktów końcowych w oparciu o kolejność, w której są wywoływane. Dopasowania z trasy, która pojawiła się wcześniej, mają wyższy priorytet. Routowanie konwencjonalne jest zależne od kolejności. Ogólnie rzecz biorąc, trasy z obszarami powinny być umieszczone wcześniej, ponieważ są bardziej specyficzne niż trasy bez obszaru. [Dedykowane konwencjonalne trasy](#dcr) ze wszystkimi parametrami tras, `{*article}` takich jak, mogą spowodować zbyt [zachłanne](xref:fundamentals/routing#greedy)trasy, co oznacza, że pasuje do adresów URL, które mają być dopasowane przez inne trasy. Umieść trasy zachłanne w dalszej części tabeli tras, aby zapobiec dopasowania zachłanne.

[!INCLUDE[](~/includes/catchall.md)]

<a name="best"></a>

### <a name="resolving-ambiguous-actions"></a>Rozwiązywanie niejednoznacznych akcji

Gdy dwa punkty końcowe pasują do routingu, routing musi wykonać jedną z następujących czynności:

* Wybierz najlepszego kandydata.
* Zgłoś wyjątek.

Przykład:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet9)]

Poprzedni kontroler definiuje dwie akcje, które są zgodne:

* Ścieżka adresu URL`/Products33/Edit/17`
* Kierowanie `{ controller = Products33, action = Edit, id = 17 }`danych.

Jest to typowy wzorzec dla kontrolerów MVC:

* `Edit(int)`Wyświetla formularz, w którym można edytować produkt.
* `Edit(int, Product)`przetwarza opublikowany formularz.

Aby rozwiązać poprawność trasy:

* `Edit(int, Product)`jest wybierany, gdy żądanie jest HTTP `POST`.
* `Edit(int)`jest wybierany, gdy [czasownik http](#verb) jest inny. `Edit(int)`jest zazwyczaj wywoływany `GET`za pośrednictwem.

<xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, `[HttpPost]`,, Jest dostarczany do routingu, aby można było wybrać oparty na metodzie HTTP żądania. `HttpPostAttribute` Zapewnia `Edit(int, Product)` lepszy odpowiednik niż `Edit(int)`.

Ważne jest, aby zrozumieć rolę atrybutów, takich jak `HttpPostAttribute`. Podobne atrybuty są zdefiniowane dla innych [czasowników HTTP](#verb). W przypadku [routingu konwencjonalnego](#cr)typowe dla akcji używanie tej samej nazwy akcji, gdy są one częścią formularza Pokaż, przesyłania formularza. Na przykład zapoznaj [się z tematem badanie dwóch metod akcji edycji](xref:tutorials/first-mvc-app/controller-methods-views#get-post).

Jeśli routingu nie można wybrać najlepszego kandydata, <xref:System.Reflection.AmbiguousMatchException> zostanie zgłoszony, zawierający wiele pasujących punktów końcowych.

<a name="routing-route-name-ref-label"></a>

### <a name="conventional-route-names"></a>Nazwy tras konwencjonalnych

Ciągi `"blog"` i `"default"` w poniższych przykładach są nazwami konwencjonalnych tras:

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

Nazwy tras nadają trasie nazwę logiczną. Nazwana trasa może służyć do generowania adresów URL. Użycie nazwanej trasy upraszcza tworzenie adresów URL, gdy porządkowanie tras może spowodować skomplikowane generowanie adresów URL. Nazwy tras muszą być unikatowe w całej aplikacji.

Nazwy tras:

* Nie mają wpływu na pasujące adresy URL ani obsługę żądań.
* Są używane tylko do generowania adresów URL.

Koncepcja nazwy trasy jest reprezentowana w obszarze Routing jako [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata). **Nazwa trasy** i nazwa **punktu końcowego**:

* Są zamienne.
* Który jest używany w dokumentacji i kodu zależy od opisanego interfejsu API.

<a name="attribute-routing-ref-label"></a>
<a name="ar"></a>

## <a name="attribute-routing-for-rest-apis"></a>Routing atrybutów dla interfejsów API REST

Interfejsy API REST powinny używać routingu atrybutów do modelowania funkcjonalności aplikacji jako zestawu zasobów, w których operacje są reprezentowane przez [zlecenia http](#verb).

Funkcja routingu atrybutów używa zestawu atrybutów do mapowania akcji bezpośrednio do szablonów tras. Poniższy `StartUp.Configure` kod jest typowy dla interfejsu API REST i jest używany w następnym przykładzie:

[!code-csharp[](routing/samples/3.x/main/StartupApi.cs?name=snippet)]

W powyższym kodzie <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*> jest wywoływana w `UseEndpoints` celu mapowania kontrolerów z routingiem atrybutu.

W poniższym przykładzie:

* Poprzednia `Configure` Metoda jest używana.
* `HomeController`dopasowuje zestaw adresów URL podobny do tego, co jest zgodne `{controller=Home}/{action=Index}/{id?}` z domyślną trasą konwencjonalny.

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

`HomeController.Index` `/`Akcja jest uruchamiana dla dowolnej ścieżki URL, `/Home`, `/Home/Index`, lub. `/Home/Index/3`

W tym przykładzie przedstawiono najważniejsze różnice programistyczne między routingiem atrybutów i [routingiem konwencjonalnym](#cr). Routing atrybutów wymaga więcej danych wejściowych w celu określenia trasy. Konwencjonalne trasy domyślne obsługuje trasy bardziej zwięzłie. Jednak Routing atrybutu zezwala na ścisłą kontrolę nad tym, które szablony tras mają zastosowanie do poszczególnych [akcji](#action).

W przypadku routingu atrybutów nazwa kontrolera i nazwy akcji nie odgrywają **żadnej** roli, w której akcja jest dopasowywana. Poniższy przykład dopasowuje te same adresy URL, co w poprzednim przykładzie:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

Następujący kod używa zastąpienia tokenu dla `action` i: `controller`

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet22)]

Następujący kod ma zastosowanie `[Route("[controller]/[action]")]` do kontrolera:

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

W poprzednim kodzie, szablony `Index` metod muszą być poprzedzone `/` lub `~/` do szablonów tras. Szablony tras zastosowane do akcji rozpoczynającej się od `/` lub `~/` nie są połączone z szablonami tras zastosowanymi do kontrolera.

Zobacz [pierwszeństwo szablonu trasy](xref:fundamentals/routing#rtp) , aby uzyskać informacje o wyborze szablonu trasy.

## <a name="reserved-routing-names"></a>Zastrzeżone nazwy routingu

Następujące słowa kluczowe są zastrzeżonymi nazwami parametrów trasy podczas korzystania z kontrolerów lub Razor Pages:

* `action`
* `area`
* `controller`
* `handler`
* `page`

Użycie `page` jako parametru trasy z routingiem atrybutu jest typowym błędem. Powoduje to niespójne i mylące zachowanie przy generowaniu adresów URL.

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo2Controller.cs?name=snippet)]

Specjalne nazwy parametrów są używane przez generowanie adresów URL w celu ustalenia, czy operacja generowania adresu URL odwołuje się do strony Razor lub do kontrolera.

<a name="verb"></a>

## <a name="http-verb-templates"></a>Szablony czasowników HTTP

ASP.NET Core ma następujące szablony czasowników HTTP:

* [Narzędzia HttpGet](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)
* [HTTPPOST](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)
* [[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)
* [[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)
* [[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)
* [[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)

<a name="rt"></a>

### <a name="route-templates"></a>Szablony tras

ASP.NET Core ma następujące szablony tras:

* Wszystkie [Szablony czasowników HTTP](#verb) są szablonami tras.
* [Szlak](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)

<a name="arx"></a>

### <a name="attribute-routing-with-http-verb-attributes"></a>Routing atrybutów z atrybutami czasownik http

Rozważmy następujący kontroler:

[!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet)]

Powyższy kod ma następujące działanie:

* Każda akcja zawiera `[HttpGet]` atrybut, który ogranicza dopasowywanie do żądań HTTP GET.
* `GetProduct` Akcja `"{id}"` obejmuje szablon, dlatego `id` jest dołączany do `"api/[controller]"` szablonu na kontrolerze. Szablon metod to `"api/[controller]/"{id}""`. W związku z tym ta akcja dopasowuje tylko żądania get `/api/test2/xyz`dla`/api/test2/123`formularza`/api/test2/{any string}`,, itp.
  [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet2)]
* `GetIntProduct` Akcja zawiera `"int/{id:int}")` szablon. `:int` Część szablonu ogranicza wartości `id` trasy do ciągów, które mogą być konwertowane na liczbę całkowitą. Żądanie GET `/api/test2/int/abc`:
  * Nie jest zgodna z tą akcją.
  * Zwraca błąd [nie znaleziono 404](https://developer.mozilla.org/docs/Web/HTTP/Status/404) .
    [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet3)]
* `GetInt2Product` Akcja zawiera `{id}` w szablonie, ale nie ogranicza `id` wartości, które można przekonwertować na liczbę całkowitą. Żądanie GET `/api/test2/int2/abc`:
  * Pasuje do tej trasy.
  * Nie można przekonwertować `abc` powiązania modelu na liczbę całkowitą. `id` Parametr metody jest liczbą całkowitą.
  * Zwraca [400 Nieprawidłowe żądanie](https://developer.mozilla.org/docs/Web/HTTP/Status/400) , ponieważ nie można skonwertować`abc` powiązania modelu na liczbę całkowitą.
      [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet4)]

Funkcja routingu atrybutów może <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> używać atrybutów takich <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>jak <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>,, <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>i. Wszystkie atrybuty [czasowników HTTP](#verb) akceptują szablon trasy. Poniższy przykład przedstawia dwie akcje, które pasują do tego samego szablonu trasy:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyProductsController.cs?name=snippet1)]

Przy użyciu ścieżki `/products3`URL:

* Akcja jest uruchamiana, gdy [czasownik http](#verb) to `GET` `MyProductsController.ListProducts`
* Akcja jest uruchamiana, gdy [czasownik http](#verb) to `POST` `MyProductsController.CreateProduct`

Podczas kompilowania interfejsu API REST jest to rzadko konieczne, aby użyć `[Route(...)]` metody akcji, ponieważ akcja akceptuje wszystkie metody http. Lepiej jest używać bardziej szczegółowego [atrybutu zlecenia http](#verb) , aby precyzyjnie dowiedzieć się, co obsługuje interfejs API. Klienci interfejsów API REST powinni wiedzieć, jakie ścieżki i czasowniki HTTP mapują na określone operacje logiczne.

Interfejsy API REST powinny używać routingu atrybutów do modelowania funkcjonalności aplikacji jako zestawu zasobów, w których operacje są reprezentowane przez zlecenia HTTP. Oznacza to, że wiele operacji, na przykład Pobierz i Opublikuj dla tego samego zasobu logicznego, użyj tego samego adresu URL. Routing atrybutu zapewnia poziom kontroli, który jest wymagany do dokładnego projektowania układu publicznego punktu końcowego interfejsu API.

Ponieważ atrybut Route ma zastosowanie do określonej akcji, można łatwo wprowadzić parametry wymagane jako część definicji szablonu trasy. W poniższym przykładzie `id` jest wymagane jako część ścieżki URL:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

`Products2ApiController.GetProduct(int)` Akcja:

* Jest uruchamiany z ścieżką URL podobną do`/products2/3`
* Nie jest uruchamiany z ścieżką `/products2`URL.

Atrybut [[](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) Requests] umożliwia akcja ograniczenia obsługiwanych typów zawartości żądania. Aby uzyskać więcej informacji, zobacz [Definiowanie obsługiwanych typów zawartości żądania przy użyciu atrybutu użycia](xref:web-api/index#consumes).

 Aby uzyskać pełny opis szablonów tras i powiązanych opcji, zobacz [Routing](xref:fundamentals/routing) .

Aby uzyskać więcej informacji `[ApiController]`na temat, zobacz [ApiController Attribute](xref:web-api/index##apicontroller-attribute).

## <a name="route-name"></a>Nazwa trasy

Następujący kod definiuje nazwę trasy `Products_List`:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

Nazwy tras mogą służyć do generowania adresów URL na podstawie określonej trasy. Nazwy tras:

* Nie ma wpływu na zachowanie w zakresie routingu zgodnego z adresem URL.
* Są używane tylko na potrzeby generowania adresów URL.

Nazwy tras muszą być unikatowe w całej aplikacji.

Powyższy kod przy użyciu konwencjonalnej trasy domyślnej, która definiuje `id` parametr jako opcjonalny (`{id?}`). Możliwość precyzyjnego określania interfejsów API ma zalety, takie jak `/products` umożliwienie `/products/5` i wysyłanie ich do różnych akcji.

<a name="routing-combining-ref-label"></a>

## <a name="combining-attribute-routes"></a>Łączenie tras atrybutów

Aby mniej powtarzać Routing atrybutów, atrybuty trasy na kontrolerze są łączone z atrybutami trasy dla poszczególnych akcji. Wszystkie szablony tras zdefiniowane na kontrolerze są poprzedzone w celu rozesłania szablonów w akcjach. Umieszczenie atrybutu trasy na kontrolerze powoduje, że **wszystkie** akcje w kontrolerze używają routingu atrybutów.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet)]

W poprzednim przykładzie:

* Ścieżka `/products` adresu URL może być taka sama`ProductsApi.ListProducts`
* Ścieżka `/products/5` adresu URL może być `ProductsApi.GetProduct(int)`taka sama.

Obie te akcje pasują tylko do `GET` protokołu HTTP, ponieważ są oznaczone `[HttpGet]` atrybutem.

Szablony tras zastosowane do akcji rozpoczynającej się od `/` lub `~/` nie są połączone z szablonami tras zastosowanymi do kontrolera. Poniższy przykład dopasowuje zestaw ścieżek adresów URL podobny do trasy domyślnej.

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet)]

W poniższej tabeli opisano `[Route]` atrybuty w poprzednim kodzie:

| Atrybut               | Łączy z`[Route("Home")]` | Definiuje szablon trasy |
| ----------------- | ------------ | --------- |
| `[Route("")]` | Tak | `"Home"` |
| `[Route("Index")]` | Tak | `"Home/Index"` |
| `[Route("/")]` | **Nie** | `""` |
| `[Route("About")]` | Tak | `"Home/About"` |

<a name="routing-ordering-ref-label"></a>
<a name="oar"></a>

### <a name="attribute-route-order"></a>Porządek trasy dla atrybutu

Routing kompiluje drzewo i dopasowuje wszystkie punkty końcowe jednocześnie:

* Wpisy trasy zachowują się tak, jakby zostały umieszczone w idealnej kolejności.
* Najbardziej konkretne trasy mają możliwość wykonania przed bardziej ogólnymi trasami.

Na przykład, podobnie jak `blog/search/{topic}` w przypadku bardziej szczegółowych informacji niż trasie atrybutu. `blog/{*article}` `blog/search/{topic}` Trasa ma wyższy priorytet, ponieważ jest domyślnie bardziej specyficzna. Przy użyciu [konwencjonalnego routingu](#cr)deweloper jest odpowiedzialny za umieszczanie tras w odpowiedniej kolejności.

Trasy atrybutów mogą konfigurować zamówienie przy użyciu <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> właściwości. Wszystkie [atrybuty trasy](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) dostarczonej przez platformę `Order` obejmują. Trasy są przetwarzane zgodnie z rosnącą sortowaniem `Order` właściwości. Kolejność domyślna to `0`. Ustawianie trasy przy użyciu `Order = -1` przebiegów przed trasami, które nie ustawiają kolejności. Ustawianie trasy przy użyciu `Order = 1` przebiegu po domyślnej kolejności tras.

**Należy unikać** w `Order`zależności od. Jeśli przestrzeń adresów URL aplikacji wymaga jawnych wartości kolejności, aby można było prawidłowo kierować trasy, prawdopodobnie również jest myląca dla klientów. Ogólnie rzecz biorąc, routing atrybutów wybiera prawidłową trasę z dopasowywaniem adresów URL. Jeśli domyślna kolejność generowania adresów URL nie działa, użycie nazwy trasy jako przesłonięcia jest zwykle prostsze niż stosowanie `Order` właściwości.

Należy wziąć pod uwagę następujące dwa kontrolery, które definiują `/home`trasę zgodną z:

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

Żądanie `/home` z powyższym kodem zgłasza wyjątek podobny do następującego:

```text
AmbiguousMatchException: The request matched multiple endpoints. Matches:

 WebMvcRouting.Controllers.HomeController.Index
 WebMvcRouting.Controllers.MyDemoController.MyIndex
```

Dodanie `Order` do jednego z atrybutów trasy rozwiązuje niejednoznaczność:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo3Controller.cs?name=snippet3& highlight=2)]

Za pomocą powyższego `/home` kodu program `HomeController.Index` uruchamia punkt końcowy. `MyDemoController.MyIndex`, Aby przejść do żądania `/home/MyIndex`. **Uwaga**:

* Poprzedni kod jest przykładem lub słabym projektem routingu. Została użyta do zilustrowania `Order` właściwości.
* `Order` Właściwość rozwiązuje tylko niejednoznaczność, ale nie można dopasować tego szablonu. Lepszym rozwiązaniem jest usunięcie `[Route("Home")]` szablonu.

Zobacz [ Razor strony trasy i konwencje aplikacji: kolejność tras](xref:razor-pages/razor-pages-conventions#route-order) na potrzeby informacji o kolejności Razor tras na stronach.

W niektórych przypadkach błąd HTTP 500 jest zwracany z niejednoznacznych tras. Użyj [rejestrowania](xref:fundamentals/logging/index) , aby zobaczyć, które punkty `AmbiguousMatchException`końcowe spowodowały.

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a>Zastępowanie tokenu w szablonach tras [Controller], [Action], [obszar]

Dla wygody atrybut trasy obsługują zastępowanie tokenów dla zarezerwowanych parametrów trasy przez załączanie tokenu w jednym z następujących:

* Nawiasy kwadratowe:`[]`
* Nawiasy klamrowe:`{}`

Tokeny `[action]` `[area]`, i `[controller]` są zastępowane wartościami nazwy akcji, obszaru i nazwy kontrolera z akcji, w której zdefiniowano trasę:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet)]

Powyższy kod ma następujące działanie:

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet10)]

  * Prawdopodobny`/Products0/List`

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet11)]

  * Prawdopodobny`/Products0/Edit/{id}`

Zastępowanie tokenu występuje jako ostatni krok tworzenia tras atrybutów. Poprzedni przykład zachowuje się tak samo jak w poniższym kodzie:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet20)]

[!INCLUDE[](~/includes/MTcomments.md)]

Trasy atrybutu można także łączyć z dziedziczeniem. Jest to zaawansowane połączenie z zastępowaniem tokenu. Zastępowanie tokenu dotyczy również nazw tras zdefiniowanych przez trasy atrybutów.
`[Route("[controller]/[action]", Name="[controller]_[action]")]`generuje unikatową nazwę trasy dla każdej akcji:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet5)]

Zastępowanie tokenu dotyczy również nazw tras zdefiniowanych przez trasy atrybutów.
`[Route("[controller]/[action]", Name="[controller]_[action]")]`
generuje unikatową nazwę trasy dla każdej akcji.

Aby dopasować ogranicznik zamiany tokenu `[` literału lub `]`, należy to zrobić, powtarzając znak (`[[` lub `]]`).

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a>Używanie transformatora parametrów do dostosowywania zastępowania tokenu

Zastępowanie tokenu można dostosować za pomocą transformatora parametrów. Transformator parametrów implementuje <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> i przekształca wartość parametrów. Na przykład niestandardowy `SlugifyParameterTransformer` transformator parametrów zmienia wartość `SubscriptionManagement` trasy na: `subscription-management`

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> Jest konwencją modelu aplikacji, która:

* Stosuje transformator parametrów do wszystkich tras atrybutów w aplikacji.
* Dostosowuje wartości tokenów tras w miarę ich wymiany.

[!code-csharp[](routing/samples/3.x/main/Controllers/SubscriptionManagementController.cs?name=snippet)]

Poprzednia `ListAll` Metoda `/subscription-management/list-all`pasuje.

`RouteTokenTransformerConvention` Jest zarejestrowany jako opcja w `ConfigureServices`.

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet)]

Zobacz [powiadomienia MDN Web docs](https://developer.mozilla.org/docs/Glossary/Slug) , aby zapoznać się z definicją informacji o sobie.

[!INCLUDE[](~/includes/regex.md)]
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-attribute-routes"></a>Wiele tras atrybutów

Routing atrybutów obsługuje definiowanie wielu tras, które docierają do tej samej akcji. Najczęstszym sposobem użycia tej metody jest naśladowanie zachowania domyślnej trasy konwencjonalnej, jak pokazano w następującym przykładzie:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6x)]

Umieszczenie wielu atrybutów trasy na kontrolerze oznacza, że każda z nich łączy się z każdym z atrybutów tras w metodach akcji:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6)]

Wszystkie ograniczenia trasy [http zlecenia](#verb) są implementowane `IActionConstraint`.

Gdy wiele atrybutów trasy, które <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> implementują są umieszczane w akcji:

* Każde ograniczenie akcji łączy się z szablonem trasy zastosowanym do kontrolera.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet7)]

Korzystanie z wielu tras w akcjach może wydawać się użyteczne i zaawansowane, dlatego lepiej jest zachować podstawową i dobrze zdefiniowaną przestrzeń adresów URL aplikacji. Użyj wielu tras w przypadku akcji **tylko wtedy** , gdy jest to konieczne, na przykład w celu obsługi istniejących klientów.

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a>Określanie opcjonalnych parametrów trasy, wartości domyślnych i ograniczeń

Trasy atrybutów obsługują tę samą składnię wbudowaną co konwencjonalne trasy, aby określić parametry opcjonalne, wartości domyślne i ograniczenia.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet8&highlight=3)]

W poprzednim kodzie, `[HttpPost("product/{id:int}")]` stosuje ograniczenie trasy. `ProductsController.ShowProduct` Akcja jest dopasowywana tylko przez ścieżki URL, `/product/3`takie jak. Część `{id:int}` szablonu trasy ogranicza ten segment tylko do liczb całkowitych.

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

Aby uzyskać szczegółowy opis składni szablonu trasy, zobacz [odwołanie do szablonu trasy](xref:fundamentals/routing#route-template-reference) .

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a>Niestandardowe atrybuty trasy przy użyciu IRouteTemplateProvider

Wszystkie [atrybuty trasy](#rt) implementują <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>. Środowisko uruchomieniowe ASP.NET Core:

* Wyszukuje atrybuty klas kontrolera i metod akcji podczas uruchamiania aplikacji.
* Używa atrybutów, które implementują `IRouteTemplateProvider` w celu utworzenia początkowego zestawu tras.

Zaimplementuj `IRouteTemplateProvider` , aby zdefiniować niestandardowe atrybuty trasy. Każdy `IRouteTemplateProvider` z nich umożliwia definiowanie pojedynczej trasy z niestandardowym szablonem trasy, kolejnością i nazwą:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyTestApiController.cs?name=snippet&highlight=1-10)]

Poprzednia `Get` Metoda `Order = 2, Template = api/MyTestApi`zwraca.

<a name="routing-app-model-ref-label"></a>

### <a name="use-application-model-to-customize-attribute-routes"></a>Dostosowywanie tras atrybutów przy użyciu modelu aplikacji

Model aplikacji:

* Jest modelem obiektu utworzonym podczas uruchamiania.
* Zawiera wszystkie metadane używane przez ASP.NET Core do kierowania i wykonywania akcji w aplikacji.

Model aplikacji zawiera wszystkie dane zebrane z atrybutów tras. Dane z atrybutów trasy są udostępniane przez `IRouteTemplateProvider` implementację. Obowiązujący

* Można napisać, aby zmodyfikować model aplikacji, aby dostosować sposób zachowania routingu.
* Są odczytywane podczas uruchamiania aplikacji.

W tej sekcji przedstawiono podstawowy przykład dostosowywania routingu przy użyciu modelu aplikacji. Poniższy kod sprawia, że trasy są w przybliżeniu zgodne ze strukturą folderów projektu.

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet)]

Poniższy kod uniemożliwia stosowanie `namespace` Konwencji do kontrolerów, które są kierowane przez atrybut:

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet2)]

Na przykład następujący kontroler nie używa `NamespaceRoutingConvention`:

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/ManagersController.cs?name=snippet&highlight=1)]

`NamespaceRoutingConvention.Apply` Metoda:

* Nie robi nic, Jeśli kontroler ma przypisany atrybut.
* Ustawia szablon szablonu oparty na `namespace`, z usuniętym podstawowym. `namespace`

`NamespaceRoutingConvention` Można zastosować w `Startup.ConfigureServices`:

[!code-csharp[](routing/samples/3.x/nsrc/Startup.cs?name=snippet&highlight=1,14-18)]

Rozważmy na przykład następujący kontroler:

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/UsersController.cs)]

Powyższy kod ma następujące działanie:

* Podstawą `namespace` jest `My.Application`.
* Pełna nazwa poprzedniego kontrolera to `My.Application.Admin.Controllers.UsersController`.
* `NamespaceRoutingConvention` Ustawia szablon controllers na `Admin/Controllers/Users/[action]/{id?`.

`NamespaceRoutingConvention` Można również zastosować jako atrybut na kontrolerze:

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/TestController.cs?name=snippet&highlight=1)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a>Routing mieszany: Routing atrybutów a konwencjonalny Routing

Aplikacje ASP.NET Core mogą łączyć użycie konwencjonalnych routingu i routingu atrybutów. Typowym zastosowaniem są trasy konwencjonalne dla kontrolerów obsługujących strony HTML dla przeglądarek i routingu atrybutów dla kontrolerów obsługujących interfejsy API REST.

Akcje są albo podkierowane do Konwencji lub kierowane przez atrybut. Umieszczenie trasy na kontrolerze lub akcja powoduje, że atrybut jest kierowany. Akcje definiujące trasy atrybutów nie mogą być osiągane za pomocą konwencjonalnych tras i vice versa. **Każdy** atrybut trasy na kontrolerze powoduje kierowanie **wszystkich** akcji w atrybucie kontrolera.

Routing atrybutów i Routing konwencjonalny używają tego samego aparatu routingu.

<a name="routing-url-gen-ref-label"></a>
<a name="ambient"></a>

## <a name="url-generation-and-ambient-values"></a>Generowanie adresów URL i wartości otoczenia

Aplikacje mogą używać funkcji generowania adresów URL routingu do generowania linków URL do akcji. Generowanie adresów URL eliminuje adresy URL zakodowana, co sprawia, że kod jest bardziej niezawodny i konserwowany. Ta sekcja koncentruje się na funkcjach generowania adresów URL udostępnianych przez MVC i obejmuje jedynie podstawowe informacje na temat sposobu działania generowania adresów URL. Aby uzyskać szczegółowy opis generowania adresów URL, zobacz temat [Routing](xref:fundamentals/routing) .

<xref:Microsoft.AspNetCore.Mvc.IUrlHelper> Interfejs jest podstawowym elementem infrastruktury między MVC i Routing na potrzeby generowania adresów URL. Wystąpienie `IUrlHelper` jest dostępne za pomocą `Url` właściwości w kontrolerach, widokach i składnikach widoku.

W poniższym przykładzie `IUrlHelper` interfejs jest używany przez `Controller.Url` właściwość w celu wygenerowania adresu URL dla innej akcji.

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

Jeśli aplikacja używa domyślnej trasy konwencjonalnej, wartość `url` zmiennej jest CIĄGIEM `/UrlGeneration/Destination`ścieżki URL. Ta ścieżka URL jest tworzona przez połączenie za pomocą routingu:

* Wartości trasy z bieżącego żądania, które są nazywane **wartościami otoczenia**.
* Wartości przesyłane do `Url.Action` i podstawiające te wartości do szablonu trasy:

``` text
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

Każdy parametr trasy w szablonie trasy ma swoją wartość zastępowaną przez pasujące nazwy wartościami i wartościami otoczenia. Parametr trasy, który nie ma wartości, może:

* Użyj wartości domyślnej, jeśli ma ją.
* Można pominąć, jeśli jest to opcjonalne. Na przykład, `id` z szablonu `{controller}/{action}/{id?}`trasy.

Generowanie adresu URL kończy się niepowodzeniem, jeśli którykolwiek z wymaganych parametrów trasy nie ma odpowiadającej mu wartości. Jeśli generowanie adresów URL kończy się niepowodzeniem dla trasy, kolejna trasa zostanie ponowiona do momentu przetworzenia wszystkich tras lub znalezienia dopasowania.

W poprzednim przykładzie `Url.Action` przyjęto założenie [konwencjonalnego routingu](#cr). Generowanie adresów URL działa podobnie jak w przypadku [routingu atrybutów](#ar), chociaż koncepcje różnią się. Z routingiem konwencjonalnym:

* Wartości trasy służą do rozszerzania szablonu.
* Wartości trasy dla `controller` i `action` zwykle pojawiają się w tym szablonie. Dzieje się tak, ponieważ adresy URL dopasowane przez Routing są zgodne z Konwencją.

W poniższym przykładzie zastosowano Routing atrybutów:

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationAttrController.cs?name=snippet_1)]

`Source` Akcja w poprzednim kodzie generuje `custom/url/to/destination`.

<xref:Microsoft.AspNetCore.Routing.LinkGenerator>został dodany w ASP.NET Core 3,0 jako alternatywa dla `IUrlHelper`. `LinkGenerator`oferuje podobne, ale bardziej elastyczne funkcje. Każda metoda w `IUrlHelper` systemie ma również odpowiednią rodzinę metod `LinkGenerator` .

### <a name="generating-urls-by-action-name"></a>Generowanie adresów URL według nazwy akcji

Wartość [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator. GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*)i wszystkie powiązane przeciążenia są przeznaczone do generowania docelowego punktu końcowego przez określenie nazwy kontrolera i nazwy akcji.

W przypadku `Url.Action`używania, bieżąca wartość trasy dla `controller` i `action` jest udostępniana przez środowisko uruchomieniowe:

* Wartość `controller` i `action` jest częścią zarówno [wartości otoczenia](#ambient) , jak i wartości. Metoda `Url.Action` zawsze używa bieżących wartości `action` i `controller` i generuje ścieżkę URL, która jest przesyłana do bieżącej akcji.

Funkcja routingu próbuje użyć wartości w otoczeniu wartości, aby podać informacje, które nie zostały dostarczone podczas generowania adresu URL. Rozważ użycie trasy podobnej `{a}/{b}/{c}/{d}` do wartości `{ a = Alice, b = Bob, c = Carol, d = David }`otoczenia:

* Routing ma wystarczającą ilość informacji do wygenerowania adresu URL bez dodatkowych wartości.
* Routing ma wystarczającą ilość informacji, ponieważ wszystkie parametry tras mają wartość.

Jeśli wartość `{ d = Donovan }` zostanie dodana:

* Wartość `{ d = David }` jest ignorowana.
* Wygenerowana Ścieżka adresu URL `Alice/Bob/Carol/Donovan`to.

**Ostrzeżenie**: ścieżki URL są hierarchiczne. W poprzednim przykładzie, jeśli wartość `{ c = Cheryl }` jest dodawana:

* Obie wartości `{ c = Carol, d = David }` są ignorowane.
* Nie jest już dostępna wartość `d` i generowanie adresu URL nie powiodło się.
* Wymagane wartości `c` i `d` muszą być określone w celu wygenerowania adresu URL.  

Prawdopodobnie wystąpił problem z domyślną trasą `{controller}/{action}/{id?}`. Ten problem występuje rzadko `Url.Action` `controller` , ponieważ zawsze jawnie określa wartość i. `action`

Kilka przeciążeń [adresu URL. akcja](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) przyjmuje obiekt wartości trasy, aby podać wartości parametrów trasy innych `controller` niż `action`i. Obiekt wartości trasy jest często używany z `id`. Na przykład `Url.Action("Buy", "Products", new { id = 17 })`. Obiekt wartości trasy:

* Według Konwencji jest zwykle obiektem typu anonimowego.
* Może to być `IDictionary<>` lub [poco](https://wikipedia.org/wiki/Plain_old_CLR_object)).

Wszystkie dodatkowe wartości trasy, które nie pasują do parametrów trasy, są umieszczane w ciągu zapytania.

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet)]

Poprzedni kod generuje `/Products/Buy/17?color=red`.

Poniższy kod generuje bezwzględny adres URL:

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet2)]

Aby utworzyć bezwzględny adres URL, użyj jednego z następujących elementów:

* Przeciążenie, które akceptuje `protocol`. Na przykład poprzedzający kod.
* [LinkGenerator. GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), który domyślnie generuje bezwzględne identyfikatory URI.

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generate-urls-by-route"></a>Generuj adresy URL według trasy

Poprzedni kod wykazał wygenerowanie adresu URL przez przekazanie go do kontrolera i nazwy akcji. `IUrlHelper`zapewnia także rodzinę [adresów URL. RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) . Te metody są podobne do [adresu URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), ale nie kopiują bieżących wartości `action` i `controller` do wartości trasy. Najczęstsze użycie `Url.RouteUrl`:

* Określa nazwę trasy do wygenerowania adresu URL.
* Zwykle nie określa kontrolera ani nazwy akcji.

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGeneration2Controller.cs?name=snippet_1)]

Następujący Razor plik GENERUJE link HTML do `Destination_Route`:

[!code-cshtml[](routing/samples/3.x/main/Views/Shared/MyLink.cshtml)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generate-urls-in-html-and-razor"></a>Generuj adresy URL w kodzie HTML iRazor

<xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper>udostępnia <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> metody [HTML. BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) oraz [HTML. ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) do wygenerowania `<form>` i `<a>` elementów. Metody te używają metody [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) do generowania adresu URL i akceptują podobne argumenty. `Url.RouteUrl` Pomocników dla programu `HtmlHelper` mają `Html.BeginRouteForm` `Html.RouteLink` podobną funkcjonalność.

TagHelpers Generuj adresy URL za `form` pomocą TagHelper i `<a>` TagHelper. Oba te zastosowania `IUrlHelper` dla ich implementacji. Aby uzyskać więcej informacji, zobacz [pomocników tagów w formularzach](xref:mvc/views/working-with-forms) .

W widokach można `IUrlHelper` korzystać z `Url` właściwości dla dowolnej generacji adresów URL ad hoc, która nie jest objęta powyższym.

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="url-generation-in-action-results"></a>Generowanie adresów URL w wynikach akcji

Poprzednie przykłady pokazano przy użyciu `IUrlHelper` w kontrolerze. Najczęstszym sposobem użycia na kontrolerze jest wygenerowanie adresu URL jako części wyniku akcji.

Klasy <xref:Microsoft.AspNetCore.Mvc.ControllerBase> bazowe <xref:Microsoft.AspNetCore.Mvc.Controller> i zapewniają wygodne metody dla wyników akcji, które odwołują się do innej akcji. Jednym z typowych zastosowań jest przekierowanie po zaakceptowaniu danych wejściowych użytkownika:

[!code-csharp[](routing/samples/3.x/main/Controllers/CustomerController.cs?name=snippet)]

Wynikiem akcji są metody fabryki, takie <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*> jak <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> i, postępuj zgodnie z podobnym `IUrlHelper`wzorcem do metod w.

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a>Specjalny przypadek dla dedykowanych tras konwencjonalnych

Funkcja [routingu konwencjonalnego](#cr) może używać specjalnego rodzaju definicji trasy o nazwie [dedykowanej, konwencjonalnej trasy](#dcr). W poniższym przykładzie trasa o nazwie `blog` jest dedykowaną konwencjonalną trasą:

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

Przy użyciu powyższych definicji trasy program `Url.Action("Index", "Home")` GENERUJE ścieżkę `/` URL przy użyciu `default` trasy, ale dlaczego? Możliwe jest odpuszczenie, że `{ controller = Home, action = Index }` wartości trasy byłyby wystarczające do WYGENEROWANIA `blog`adresu URL przy użyciu, a `/blog?action=Index&controller=Home`wynikiem będzie.

[Dedykowane konwencjonalne trasy](#dcr) polegają na specjalnym zachowaniu wartości domyślnych, które nie mają odpowiadającego parametru trasy, który uniemożliwia zbyt [zachłanne](xref:fundamentals/routing#greedy) trasy z generowaniem adresów URL. W takim przypadku wartości domyślne to `{ controller = Blog, action = Article }`, a `controller` ani nie `action` pojawiają się jako parametr trasy. Gdy Routing wykonuje generowanie adresów URL, podane wartości muszą być zgodne z wartościami domyślnymi. Generowanie adresu URL `blog` nie powiodło się `{ controller = Home, action = Index }` , ponieważ `{ controller = Blog, action = Article }`wartości nie są zgodne. Następnie usługa routingu wraca do wypróbowania `default`, która się powiedzie.

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a>Obszary

[Obszary](xref:mvc/controllers/areas) są funkcją MVC służącą do organizowania powiązanych funkcji w grupie jako oddzielnej:

* Przestrzeń nazw routingu dla akcji kontrolera.
* Struktura folderów dla widoków.

Użycie obszarów umożliwia aplikacji posiadanie wielu kontrolerów o tej samej nazwie, o ile mają one różne obszary. Za pomocą obszarów tworzy hierarchię na potrzeby routingu przez dodanie kolejnego parametru trasy `area` do `controller` i. `action` W tej sekcji omówiono sposób, w jaki Routing współdziała z obszarami. Zobacz [obszary](xref:mvc/controllers/areas) , aby uzyskać szczegółowe informacje na temat sposobu używania obszarów w widokach.

Poniższy przykład konfiguruje MVC do używania domyślnej trasy konwencjonalnej i `area` trasy dla `area` nazwanego: `Blog`

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

W poprzednim kodzie <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> jest wywoływana w celu utworzenia `"blog_route"`. Drugi parametr, `"Blog"`, jest nazwą obszaru.

W przypadku dopasowania ścieżki URL podobnej `/Manage/Users/AddUser`do `"blog_route"` trasy generuje wartości `{ area = Blog, controller = Users, action = AddUser }`trasy. Wartość `area` trasy jest generowana przez wartość domyślną dla `area`. Trasa utworzona przez `MapAreaControllerRoute` jest równoważna następującym:

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup2.cs?name=snippet2)]

`MapAreaControllerRoute`tworzy trasę przy użyciu wartości domyślnej i ograniczenia dotyczącej `area` użycia podanej nazwy obszaru, w tym przypadku `Blog`. Wartość domyślna zapewnia, że trasa zawsze produkuje `{ area = Blog, ... }`, ograniczenie wymaga wartości `{ area = Blog, ... }` dla generowania adresów URL.

Routowanie konwencjonalne jest zależne od kolejności. Ogólnie rzecz biorąc, trasy z obszarami powinny być umieszczone wcześniej, ponieważ są bardziej specyficzne niż trasy bez obszaru.

Korzystając z powyższego przykładu, `{ area = Blog, controller = Users, action = AddUser }` wartości trasy są zgodne z następującą akcją:

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

Atrybut [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) wskazuje, co oznacza kontroler w ramach obszaru. Ten kontroler znajduje się w `Blog` obszarze. Kontrolery bez `[Area]` atrybutu nie są członkami żadnego obszaru i **nie** są zgodne, gdy wartość `area` trasy jest dostarczana przez Routing. W poniższym przykładzie tylko pierwszy kontroler może być zgodny z wartościami `{ area = Blog, controller = Users, action = AddUser }`trasy.

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

Przestrzeń nazw każdego kontrolera jest pokazana w tym miejscu na potrzeby kompletności. Jeśli powyższe kontrolery używają tego samego obszaru nazw, wygenerowany zostanie błąd kompilatora. Przestrzenie nazw klas nie mają wpływu na Routing MVC.

Pierwsze dwa kontrolery są członkami obszarów i są zgodne tylko wtedy, gdy ich nazwa obszaru jest podana przez wartość `area` trasy. Trzeci kontroler nie jest członkiem żadnego obszaru i można go dopasować tylko wtedy, gdy nie ma `area` żadnej wartości parametru Routing.

<a name="aa"></a>

W warunkach pasujących do *nie ma żadnej*wartości, brak `area` wartości jest taka sama jak wartość `area` null lub pusty ciąg.

Podczas wykonywania akcji wewnątrz obszaru wartość trasy dla `area` jest dostępna jako [wartość otoczenia](#ambient) dla routingu do użycia na potrzeby generowania adresów URL. Oznacza to, że domyślnie obszary programu *Sticky Notes* mają być używane do generowania adresów URL, jak pokazano w poniższym przykładzie.

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup3.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

Poniższy kod generuje adres URL `/Zebra/Users/AddUser`:

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/HomeController.cs?name=snippet)]

<a name="action"></a>

## <a name="action-definition"></a>Definicja akcji

Metody publiczne na kontrolerze, z wyjątkiem tych z atrybutem nie będącym [akcją](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) , są akcjami.

## <a name="sample-code"></a>Przykładowy kod

 * Metoda [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs) jest dołączana do [pobranego przykładu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) i służy do wyświetlania informacji o routingu.
* [Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([jak pobrać](xref:index#how-to-download-a-sample))

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core MVC używa [oprogramowania pośredniczącego](xref:fundamentals/middleware/index) routingu, aby dopasować adresy URL żądań przychodzących i zmapować je na akcje. Trasy są zdefiniowane w kodzie startowym lub atrybutów. Trasy opisują sposób dopasowywania ścieżek adresów URL do akcji. Trasy są również używane do generowania adresów URL (dla linków) wysyłanych w odpowiedziach.

Akcje są albo podkierowane do Konwencji lub kierowane przez atrybut. Umieszczenie trasy na kontrolerze lub akcja powoduje, że atrybut jest kierowany. Aby uzyskać więcej informacji, zobacz [Routing mieszany](#routing-mixed-ref-label) .

Ten dokument wyjaśnia interakcje między MVC i routingiem oraz sposób, w jaki typowe aplikacje MVC używają funkcji routingu. Zobacz [Routing](xref:fundamentals/routing) , aby uzyskać szczegółowe informacje na temat routingu zaawansowanego.

## <a name="setting-up-routing-middleware"></a>Konfigurowanie oprogramowania pośredniczącego routingu

W metodzie *konfigurowania* może zostać wyświetlony kod podobny do:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Wewnątrz wywołania do `UseMvc`, `MapRoute` służy do tworzenia pojedynczej trasy, którą będziemy odnosić jako `default` trasy. Większość aplikacji MVC będzie używać trasy z szablonem podobnym do `default` trasy.

Szablon `"{controller=Home}/{action=Index}/{id?}"` trasy może być zgodny z ścieżką URL `/Products/Details/5` , tak jak i będzie wyodrębniał wartości `{ controller = Products, action = Details, id = 5 }` trasy przez tokenizowanie ścieżki. MVC spróbuje zlokalizować kontroler o nazwie `ProductsController` i uruchomić akcję: `Details`

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

Należy zauważyć, że w tym przykładzie powiązanie modelu będzie używać wartości, `id = 5` aby ustawić `id` parametr `5` podczas wywoływania tej akcji. Aby uzyskać więcej informacji, zobacz [powiązanie modelu](../models/model-binding.md) .

Przy użyciu `default` trasy:

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

Szablon trasy:

* `{controller=Home}`definiuje `Home` jako domyślne`controller`

* `{action=Index}`definiuje `Index` jako domyślne`action`

* `{id?}`definiuje `id` jako opcjonalne

Domyślne i opcjonalne parametry trasy nie muszą być obecne w ścieżce URL dla dopasowania. Aby uzyskać szczegółowy opis składni szablonu trasy, zobacz [odwołanie do szablonu trasy](xref:fundamentals/routing#route-template-reference) .

`"{controller=Home}/{action=Index}/{id?}"`może być zgodna ze ścieżką `/` URL i będzie generować wartości `{ controller = Home, action = Index }`trasy. Wartości dla `controller` i `action` używają wartości domyślnych, nie tworzy wartości, `id` ponieważ w ścieżce URL nie ma odpowiedniego segmentu. Aby można było wybrać akcję `HomeController` i `Index` , MVC będzie używać tych wartości tras:

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

Przy użyciu tej definicji kontrolera i szablonu trasy `HomeController.Index` akcja będzie wykonywana dla dowolnej z następujących ścieżek URL:

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

Wygodna metoda `UseMvcWithDefaultRoute`:

```csharp
app.UseMvcWithDefaultRoute();
```

Może służyć do zastępowania:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

`UseMvc`i `UseMvcWithDefaultRoute` Dodaj wystąpienie `RouterMiddleware` do potoku programu pośredniczącego. MVC nie działa bezpośrednio w oprogramowaniu pośredniczącym i używa routingu do obsługi żądań. MVC jest połączony z trasami za pomocą wystąpienia `MvcRouteHandler`. Kod w programie `UseMvc` jest podobny do następującego:

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

`UseMvc`nie definiuje bezpośrednio żadnych tras, dodaje symbol zastępczy do kolekcji tras dla `attribute` trasy. Przeciążenie `UseMvc(Action<IRouteBuilder>)` umożliwia dodanie własnych tras, a także obsługuje routing atrybutów.  `UseMvc`i wszystkie jego odmiany dodają symbol zastępczy dla atrybutu trasy — atrybut jest zawsze dostępny niezależnie od sposobu konfigurowania `UseMvc`. `UseMvcWithDefaultRoute`Definiuje domyślną trasę i obsługuje routing atrybutów. Sekcja [Routing atrybutów](#attribute-routing-ref-label) zawiera więcej szczegółów dotyczących routingu atrybutów.

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a>Routing konwencjonalny

`default` Trasa:

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

Poprzedni kod jest przykładem konwencjonalnego routingu. Ten styl jest nazywany routingiem konwencjonalnym, ponieważ ustanawia *Konwencję* dla ścieżek adresów URL:

* Pierwszy segment ścieżki jest mapowany na nazwę kontrolera.
* Druga mapowanie na nazwę akcji.
* Trzeci segment jest używany opcjonalnie `id`. `id`mapuje do jednostki modelu.

Przy użyciu `default` tej trasy ścieżka `/Products/List` URL jest mapowana na `ProductsController.List` akcję i `/Blog/Article/17` jest mapowana na. `BlogController.Article` To mapowanie jest oparte **tylko** na nazwach kontrolera i akcji, a nie na podstawie przestrzeni nazw, lokalizacji plików źródłowych ani parametrów metody.

> [!TIP]
> Użycie konwencjonalnego routingu z domyślną trasą umożliwia szybkie Kompilowanie aplikacji bez konieczności podawania nowego wzorca adresu URL dla każdej zdefiniowanej akcji. W przypadku aplikacji z akcjami w stylu CRUD zachowanie spójności adresów URL na kontrolerach może pomóc uprościć kod i uczynić interfejs użytkownika bardziej przewidywalny.

> [!WARNING]
> `id` Jest zdefiniowany jako opcjonalny przez szablon trasy, co oznacza, że akcje mogą być wykonywane bez identyfikatora dostarczonego jako część adresu URL. Typowo, co się `id` stanie, jeśli zostanie pominięty z adresu URL, to zostanie ono `0` ustawione na podstawie powiązania modelu, a w rezultacie nie zostanie znaleziona żadna jednostka w dopasowaniu `id == 0`do bazy danych. Routing atrybutu może dać szczegółowy formant, który ma być wymagany dla niektórych akcji, a nie dla innych. Zgodnie z Konwencją dokumentacja będzie zawierać opcjonalne parametry `id` , takie jak, gdy prawdopodobnie będą widoczne w prawidłowym użyciu.

## <a name="multiple-routes"></a>Wiele tras

Można dodać wiele tras wewnątrz `UseMvc` , dodając więcej wywołań do `MapRoute`. Dzięki temu można zdefiniować wiele Konwencji lub dodać trasy konwencjonalne, które są przeznaczone dla konkretnej akcji, na przykład:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Trasa `blog` w tym miejscu jest *dedykowaną tradycyjną trasą*, co oznacza, że używa systemu routingu konwencjonalnego, ale jest przeznaczona dla konkretnej akcji. Ponieważ `controller` i `action` nie pojawiają się w szablonie trasy jako parametry, mogą mieć tylko wartości domyślne i w związku z tym trasa będzie zawsze mapowana na akcję `BlogController.Article`.

Trasy w kolekcji tras są uporządkowane i będą przetwarzane w kolejności, w jakiej zostały dodane. W tym przykładzie zostanie podjęta `blog` próba trasy przed `default` trasą.

> [!NOTE]
> *Dedykowane konwencjonalne trasy* często korzystają z parametrów trasy **catch-all** , takich jak `{*article}` przechwytywanie pozostałej części ścieżki URL. Może to spowodować, że trasa "zbyt zachłanne" oznacza, że pasuje do adresów URL, które mają być dopasowane przez inne trasy. Umieść trasy "zachłanne" później w tabeli tras, aby rozwiązać ten problem.

### <a name="fallback"></a>Opcja rezerwowa

W ramach przetwarzania żądań MVC sprawdzi, czy wartości trasy mogą być używane do znajdowania kontrolera i akcji w aplikacji. Jeśli wartości trasy nie są zgodne z akcją, trasa nie jest uważana za dopasowanie i zostanie podjęta kolejna trasa. Jest to nazywane *Fallback*i ma na celu uproszczenie przypadków, w których trasy konwencjonalne nakładają się na siebie.

### <a name="disambiguating-actions"></a>Niejednoznaczne akcje

Gdy dwie akcje są zgodne z routingiem, MVC musi odróżnić się, aby wybrać najlepszy kandydat lub w przeciwnym razie zgłosić wyjątek. Przykład:

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

Ten kontroler definiuje dwie akcje, które byłyby zgodne ze `/Products/Edit/17` ścieżką URL `{ controller = Products, action = Edit, id = 17 }`i danymi tras. Jest to typowy wzorzec dla kontrolerów MVC, gdzie `Edit(int)` pokazuje formularz służący do edytowania produktu i `Edit(int, Product)` przetwarza opublikowany formularz. Aby ten możliwy składnik MVC musiał wybrać `Edit(int, Product)` , gdy żądanie jest http `POST` i `Edit(int)` gdy zlecenie http jest inne.

`HttpPostAttribute` ( `[HttpPost]` ) Jest implementacją programu `IActionConstraint` , która będzie zezwalać na wybór akcji tylko wtedy, gdy czasownik http to `POST`. Obecność elementu `IActionConstraint` sprawia, że lepszym rozwiązaniem `Edit(int, Product)` jest dopasowanie `Edit(int)`, więc `Edit(int, Product)` zostanie ono najpierw ponowione.

Musisz tylko pisać Implementacje niestandardowe `IActionConstraint` w wyspecjalizowanych scenariuszach, ale ważne jest, aby zrozumieć rolę atrybutów, takich jak `HttpPostAttribute` atrybuty podobne do innych czasowników HTTP. W przypadku routingu konwencjonalnego typowe dla akcji używanie tej samej nazwy akcji, gdy są one częścią `show form -> submit form` przepływu pracy. Wygoda tego wzorca stanie się bardziej oczywista po przejrzeniu sekcji [zrozumienie IActionConstraint](#understanding-iactionconstraint) .

Jeśli wiele pasujących tras i MVC nie mogą znaleźć "najlepszej" trasy, spowoduje to zgłoszenie `AmbiguousActionException`.

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a>Nazwy tras

Ciągi `"blog"` i `"default"` w poniższych przykładach są nazwami tras:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Nazwy tras nadaj logicznej nazwie trasy, aby nazwana trasa mogła być używana do generowania adresów URL. Znacznie upraszcza to tworzenie adresów URL, gdy porządkowanie tras może spowodować skomplikowane generowanie adresów URL. Nazwy tras muszą być unikatowe w całej aplikacji.

Nazwy tras nie mają wpływu na Dopasowywanie adresów URL ani obsługę żądań; są one używane tylko do generowania adresów URL. [Routing](xref:fundamentals/routing) zawiera bardziej szczegółowe informacje na temat generowania adresów URL, w tym generowanie adresów URL w pomocnikach specyficznych dla MVC.

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a>Routing atrybutów

Funkcja routingu atrybutów używa zestawu atrybutów do mapowania akcji bezpośrednio do szablonów tras. W poniższym przykładzie `app.UseMvc();` jest używany w `Configure` metodzie i nie jest przesyłana żadna trasa. `HomeController` Będzie zgodna z zestawem adresów URL podobnym do trasy `{controller=Home}/{action=Index}/{id?}` domyślnej, która byłaby zgodna:

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

`HomeController.Index()` Akcja będzie wykonywana dla dowolnej ścieżki `/`URL, `/Home`lub `/Home/Index`.

> [!NOTE]
> W tym przykładzie przedstawiono najważniejsze różnice programistyczne między routingiem atrybutów i routingiem konwencjonalnym. Routing atrybutów wymaga więcej danych wejściowych w celu określenia trasy; konwencjonalne trasy domyślne obsługuje trasy bardziej zwięzłie. Jednak Routing atrybutu zezwala na (i wymaga) precyzyjnej kontroli, które szablony tras mają zastosowanie do poszczególnych akcji.

W przypadku routingu atrybutów nazwa kontrolera i nazwy akcji nie odgrywają **żadnej** roli, w której akcja jest zaznaczona. Ten przykład będzie pasował do tych samych adresów URL, co w poprzednim przykładzie.

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
> Powyższe szablony tras nie definiują parametrów trasy dla `action`, `area`i `controller`. W rzeczywistości te parametry tras są niedozwolone w trasach atrybutów. Ponieważ szablon trasy jest już skojarzony z akcją, nie ma sensu analizy nazwy akcji na podstawie adresu URL.

## <a name="attribute-routing-with-httpverb-attributes"></a>Routing atrybutów z atrybutami http [Verb]

Routing atrybutu może również używać `Http[Verb]` atrybutów, takich jak. `HttpPostAttribute` Wszystkie te atrybuty mogą akceptować szablon trasy. Ten przykład przedstawia dwie akcje, które pasują do tego samego szablonu trasy:

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

W przypadku `/products` ścieżki URL podobnej `ProductsApi.ListProducts` do akcji zostanie wykonane, gdy zlecenie http jest `GET` i `ProductsApi.CreateProduct` zostanie wykonane, gdy zlecenie http ma `POST`wartość. Routing atrybutu najpierw jest zgodny z adresem URL względem zestawu szablonów tras zdefiniowanych przez atrybuty trasy. Po dopasowaniu szablonu trasy ograniczenia `IActionConstraint` są stosowane w celu określenia, które akcje mogą być wykonywane.

> [!TIP]
> Podczas kompilowania interfejsu API REST jest to rzadki, że będzie on używany `[Route(...)]` na potrzeby metody akcji, ponieważ akcja akceptuje wszystkie metody http. Lepiej jest użyć bardziej szczegółowych `Http*Verb*Attributes` informacji na temat tego, co obsługuje interfejs API. Klienci interfejsów API REST powinni wiedzieć, jakie ścieżki i czasowniki HTTP mapują na określone operacje logiczne.

Ponieważ atrybut Route ma zastosowanie do określonej akcji, można łatwo wprowadzić parametry wymagane jako część definicji szablonu trasy. W tym przykładzie `id` jest wymagane jako część ścieżki URL.

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

`ProductsApi.GetProduct(int)` Akcja zostanie wykonana dla ścieżki URL, na przykład `/products/3` , ale nie dla ścieżki adresu URL `/products`. Aby uzyskać pełny opis szablonów tras i powiązanych opcji, zobacz [Routing](xref:fundamentals/routing) .

## <a name="route-name"></a>Nazwa trasy

Następujący kod definiuje *nazwę trasy* `Products_List`:

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

Nazwy tras mogą służyć do generowania adresów URL na podstawie określonej trasy. Nazwy tras nie mają wpływu na zachowanie routingu w adresie URL i są używane tylko na potrzeby generowania adresów URL. Nazwy tras muszą być unikatowe w całej aplikacji.

> [!NOTE]
> Jest to zróżnicowane dla konwencjonalnej *trasy domyślnej*, która `id` definiuje parametr jako opcjonalny`{id?}`(). Ta możliwość precyzyjnego określania interfejsów API ma zalety, takich jak `/products` umożliwienie i `/products/5` wysyłanie ich do różnych akcji.

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a>Łączenie tras

Aby mniej powtarzać Routing atrybutów, atrybuty trasy na kontrolerze są łączone z atrybutami trasy dla poszczególnych akcji. Wszystkie szablony tras zdefiniowane na kontrolerze są poprzedzone w celu rozesłania szablonów w akcjach. Umieszczenie atrybutu trasy na kontrolerze powoduje, że **wszystkie** akcje w kontrolerze używają routingu atrybutów.

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

W `/products` tym przykładzie ścieżka adresu URL może być `ProductsApi.ListProducts`taka sama, a ścieżka `/products/5` adresu URL `ProductsApi.GetProduct(int)`może być taka sama. Obie te akcje pasują tylko do `GET` protokołu HTTP, `HttpGetAttribute`ponieważ są oznaczone za pomocą.

Szablony tras zastosowane do akcji rozpoczynającej się od `/` lub `~/` nie są połączone z szablonami tras zastosowanymi do kontrolera. Ten przykład dopasowuje zestaw ścieżek URL podobny do *trasy domyślnej*.

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

### <a name="ordering-attribute-routes"></a>Określanie kolejności tras atrybutów

W przeciwieństwie do konwencjonalnych tras, które są wykonywane w określonej kolejności, routing atrybutu kompiluje drzewo i dopasowuje wszystkie trasy jednocześnie. Zachowuje się tak, jakby wpisy trasy zostały umieszczone w idealnym porządku; najbardziej konkretne trasy mają możliwość wykonania przed bardziej ogólnymi trasami.

Na przykład taka trasa `blog/search/{topic}` jest bardziej szczegółowa niż trasa, taka jak. `blog/{*article}` Logicznie mówiąc, że `blog/search/{topic}` trasy "uruchomienia" są najpierw domyślnie, ponieważ to jest jedyną rozsądną kolejnością. Przy użyciu konwencjonalnego routingu deweloper jest odpowiedzialny za umieszczanie tras w odpowiedniej kolejności.

Trasy atrybutów mogą konfigurować kolejność przy użyciu `Order` właściwości wszystkich atrybutów tras dostarczonych przez platformę. Trasy są przetwarzane zgodnie z rosnącą sortowaniem `Order` właściwości. Kolejność domyślna to `0`. Ustawienie trasy przy użyciu `Order = -1` zostanie uruchomione przed trasami, które nie ustawiają kolejności. Ustawienie trasy przy użyciu `Order = 1` zostanie uruchomione po określeniu domyślnej kolejności tras.

> [!TIP]
> Należy unikać w `Order`zależności od. Jeśli przestrzeń adresów URL wymaga jawnych wartości kolejności, aby można było prawidłowo kierować trasy, to prawdopodobnie również jest myląca dla klientów. W ogólnym routingu atrybutów wybierz prawidłową trasę z dopasowywaniem adresów URL. Jeśli domyślna kolejność generowania adresów URL nie działa, użycie nazwy trasy jako przesłonięcia jest zwykle prostsze niż stosowanie `Order` właściwości.

RazorStrony routingu i routingu kontrolera MVC współdzielą implementację. Informacje o zamówieniu trasy Razor w tematach stron są dostępne na [ Razor stronach trasy i konwencje aplikacji: kolejność trasy](xref:razor-pages/razor-pages-conventions#route-order).

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a>Zastępowanie tokenu w szablonach tras ([Controller], [Action], [obszar])

Dla wygody, trasy atrybutu obsługują *Zastępowanie tokenu* przez ujęcie tokenu w nawiasy kwadratowe`[`( `]`,). Tokeny `[action]` `[area]`, i `[controller]` są zastępowane wartościami nazwy akcji, obszaru i nazwy kontrolera z akcji, w której jest zdefiniowana trasa. W poniższym przykładzie akcje są zgodne ze ścieżkami URL zgodnie z opisem w komentarzach:

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

Zastępowanie tokenu występuje jako ostatni krok tworzenia tras atrybutów. Powyższy przykład będzie zachowywać się tak samo jak poniższy kod:

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

Trasy atrybutu można także łączyć z dziedziczeniem. Jest to szczególnie zaawansowane w połączeniu z zastępowaniem tokenu.

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

Zastępowanie tokenu dotyczy również nazw tras zdefiniowanych przez trasy atrybutów. `[Route("[controller]/[action]", Name="[controller]_[action]")]`generuje unikatową nazwę trasy dla każdej akcji.

Aby dopasować ogranicznik zamiany tokenu `[` literału lub `]`, należy to zrobić, powtarzając znak (`[[` lub `]]`).

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a>Używanie transformatora parametrów do dostosowywania zastępowania tokenu

Zastępowanie tokenu można dostosować za pomocą transformatora parametrów. Transformator parametrów implementuje `IOutboundParameterTransformer` i przekształca wartość parametrów. Na przykład niestandardowy `SlugifyParameterTransformer` transformator parametrów zmienia wartość `SubscriptionManagement` trasy na. `subscription-management`

`RouteTokenTransformerConvention` Jest konwencją modelu aplikacji, która:

* Stosuje transformator parametrów do wszystkich tras atrybutów w aplikacji.
* Dostosowuje wartości tokenów tras w miarę ich wymiany.

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

`RouteTokenTransformerConvention` Jest zarejestrowany jako opcja w `ConfigureServices`.

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

Routing atrybutów obsługuje definiowanie wielu tras, które docierają do tej samej akcji. Najczęstszym sposobem użycia tej metody jest naśladowanie zachowania *domyślnej trasy konwencjonalnej* , jak pokazano w następującym przykładzie:

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

Umieszczenie wielu atrybutów trasy na kontrolerze oznacza, że każda z nich będzie łączyć się z poszczególnymi atrybutami trasy w metodach akcji.

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

Gdy wiele atrybutów trasy (które Implementuj `IActionConstraint`) jest umieszczanych w akcji, każde ograniczenie akcji łączy się z szablonem trasy z atrybutu, który go zdefiniował.

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
> Chociaż używanie wielu tras w akcjach może wydawać się zaawansowane, lepiej jest zachować prosty i dobrze zdefiniowany obszar adresów URL aplikacji. Użyj wielu tras w przypadku akcji tylko wtedy, gdy jest to konieczne, na przykład do obsługi istniejących klientów.

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a>Określanie opcjonalnych parametrów trasy, wartości domyślnych i ograniczeń

Trasy atrybutów obsługują tę samą składnię wbudowaną co konwencjonalne trasy, aby określić parametry opcjonalne, wartości domyślne i ograniczenia.

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

Aby uzyskać szczegółowy opis składni szablonu trasy, zobacz [odwołanie do szablonu trasy](xref:fundamentals/routing#route-template-reference) .

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a>Niestandardowe atrybuty trasy przy użyciu`IRouteTemplateProvider`

Wszystkie atrybuty trasy podane w strukturze ( `[Route(...)]`, `[HttpGet(...)]` itp.) implementują `IRouteTemplateProvider` interfejs. MVC wyszukuje atrybuty klas kontrolera i metod akcji, gdy aplikacja jest uruchamiana i używa tych, które `IRouteTemplateProvider` implementują w celu utworzenia początkowego zestawu tras.

Można zaimplementować `IRouteTemplateProvider` , aby zdefiniować własne atrybuty trasy. Każdy `IRouteTemplateProvider` z nich umożliwia definiowanie pojedynczej trasy z niestandardowym szablonem trasy, kolejnością i nazwą:

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

Atrybut z powyższego przykładu automatycznie ustawia `Template` wartość `"api/[controller]"` na `[MyApiController]` gdy jest stosowany.

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a>Dostosowywanie tras atrybutów przy użyciu modelu aplikacji

*Model aplikacji* jest modelem obiektów tworzonym podczas uruchamiania ze wszystkimi metadanymi używanymi przez MVC do kierowania i wykonywania akcji. *Model aplikacji* zawiera wszystkie dane zebrane z atrybutów trasy (do `IRouteTemplateProvider`). Można napisać *konwencje* , aby zmodyfikować model aplikacji w czasie uruchamiania, aby dostosować sposób zachowania routingu. W tej sekcji przedstawiono prosty przykład dostosowywania routingu przy użyciu modelu aplikacji.

[!code-csharp[](routing/samples/2.x/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a>Routing mieszany: Routing atrybutów a konwencjonalny Routing

Aplikacje MVC mogą łączyć użycie konwencjonalnego routingu i routingu atrybutów. Typowym zastosowaniem są trasy konwencjonalne dla kontrolerów obsługujących strony HTML dla przeglądarek i routingu atrybutów dla kontrolerów obsługujących interfejsy API REST.

Akcje są albo podkierowane do Konwencji lub kierowane przez atrybut. Umieszczenie trasy na kontrolerze lub akcja powoduje, że atrybut jest kierowany. Akcje definiujące trasy atrybutów nie mogą być osiągane za pomocą konwencjonalnych tras i vice versa. **Każdy** atrybut trasy na kontrolerze powoduje kierowanie wszystkich akcji w atrybucie kontrolera.

> [!NOTE]
> Co odróżnia dwa typy systemów routingu, proces stosowany po adresie URL pasuje do szablonu trasy. W przypadku routingu konwencjonalnego wartości trasy z dopasowania są używane do wybierania akcji i kontrolera z tabeli odnośników wszystkich konwencjonalnych akcji kierowanych. W obszarze Routing atrybutów każdy szablon jest już skojarzony z akcją i nie jest wymagany dalsze wyszukiwanie.

## <a name="complex-segments"></a>Złożone segmenty

Złożone segmenty (na przykład `[Route("/dog{token}cat")]`) są przetwarzane przez dopasowanie literałów z prawej strony do lewej w sposób niezachłanney. Sprawdź [kod źródłowy](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) opisu. Aby uzyskać więcej informacji, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/8197).

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a>Generowanie adresu URL

Aplikacje MVC mogą używać funkcji generowania adresów URL routingu do generowania linków URL do akcji. Generowanie adresów URL eliminuje adresy URL zakodowana, co sprawia, że kod jest bardziej niezawodny i konserwowany. Ta sekcja koncentruje się na funkcjach generowania adresów URL dostarczonych przez MVC i obejmuje tylko podstawowe informacje na temat sposobu działania generowania adresów URL. Aby uzyskać szczegółowy opis generowania adresów URL, zobacz temat [Routing](xref:fundamentals/routing) .

`IUrlHelper` Interfejs jest podstawową częścią infrastruktury między MVC i routingiem na potrzeby generowania adresów URL. Wystąpienie `IUrlHelper` dostępne za pomocą `Url` właściwości można znaleźć w obszarze Kontrolery, widoki i składniki widoku.

W tym przykładzie `IUrlHelper` interfejs jest używany przez `Controller.Url` właściwość w celu wygenerowania adresu URL dla innej akcji.

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

Jeśli aplikacja używa domyślnej trasy konwencjonalnej, wartość `url` zmiennej będzie ciąg `/UrlGeneration/Destination`ścieżki URL. Ta ścieżka URL jest tworzona przez Routing przez połączenie wartości trasy z bieżącego żądania (wartości otoczenia), z wartościami przekazanymi do `Url.Action` i podstawianie tych wartości do szablonu trasy:

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

Każdy parametr trasy w szablonie trasy ma swoją wartość zastępowaną przez pasujące nazwy wartościami i wartościami otoczenia. Parametr trasy, który nie ma wartości, może korzystać z wartości domyślnej, jeśli ma taką wartość, lub być pominięty, jeśli jest opcjonalny (tak jak w przypadku `id` w przypadku w tym przykładzie). Generowanie adresu URL zakończy się niepowodzeniem, jeśli którykolwiek z wymaganych parametrów trasy nie ma odpowiadającej wartości. Jeśli generowanie adresów URL kończy się niepowodzeniem dla trasy, kolejna trasa zostanie ponowiona do momentu przetworzenia wszystkich tras lub znalezienia dopasowania.

W `Url.Action` powyższym przykładzie przyjęto założenie konwencjonalnego routingu, ale generowanie adresów URL działa podobnie jak w przypadku routingu atrybutów, chociaż koncepcje różnią się. W przypadku routingu konwencjonalnego wartości trasy służą do rozszerzania szablonu, a wartości trasy dla `controller` i `action` zwykle pojawiają się w tym szablonie — działa to, ponieważ adresy URL dopasowane przez Routing są zgodne z *Konwencją*. W obszarze Routing atrybutów wartości trasy dla `controller` i `action` nie mogą występować w szablonie — zamiast tego są używane do wyszukiwania szablonu do użycia.

W tym przykładzie zastosowano Routing atrybutów:

[!code-csharp[](routing/samples/2.x/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

MVC kompiluje tabelę odnośników wszystkich akcji przypisanych do atrybutu i odpowiada `controller` wartości `action` i, aby wybrać szablon trasy do użycia na potrzeby generowania adresów URL. W powyższym przykładzie `custom/url/to/destination` jest generowany.

### <a name="generating-urls-by-action-name"></a>Generowanie adresów URL według nazwy akcji

`Url.Action` (`IUrlHelper` . `Action`) i wszystkie powiązane przeciążenia są oparte na tym pomysłie, że chcesz określić, do czego łączysz, określając nazwę kontrolera i nazwę akcji.

> [!NOTE]
> W przypadku `Url.Action`używania, bieżąca wartość trasy dla `controller` i `action` jest określona dla Ciebie — `controller` wartości i `action` są częścią obu *wartości otoczenia* **i** *wartości*. Metoda `Url.Action`zawsze używa bieżących wartości `action` i `controller` i wygeneruje ścieżkę URL, która jest przesyłana do bieżącej akcji.

Funkcja routingu próbuje użyć wartości w otoczeniu wartości, aby podać informacje, które nie zostały wprowadzone podczas generowania adresu URL. Przy użyciu trasy podobnej `{a}/{b}/{c}/{d}` do i `{ a = Alice, b = Bob, c = Carol, d = David }`otoczenia Routing ma wystarczającą ilość informacji do wygenerowania adresu URL bez żadnych dodatkowych wartości — ponieważ wszystkie parametry trasy mają wartość. Po dodaniu wartości `{ d = Donovan }` `{ d = David }` wartość zostanie zignorowana, a wygenerowana Ścieżka adresu URL `Alice/Bob/Carol/Donovan`.

> [!WARNING]
> Ścieżki URL są hierarchiczne. W powyższym przykładzie, jeśli dodano wartość `{ c = Cheryl }`, obie wartości `{ c = Carol, d = David }` byłyby ignorowane. W takim przypadku nie jest już dostępna wartość `d` i generowanie adresu URL zakończy się niepowodzeniem. Należy określić pożądaną wartość `c` i. `d`  Można oczekiwać, że ten problem zostanie osiągnięty przy użyciu trasy`{controller}/{action}/{id?}`domyślnej () — ale w takiej sytuacji rzadko napotkasz to `Url.Action` zachowanie, ponieważ zawsze jawnie `controller` określimy wartość i `action` .

Dłuższe przeciążenia `Url.Action` również pobierają dodatkowy obiekt *wartości trasy* , aby zapewnić wartości parametrów trasy innych niż `controller` i `action`. Najczęściej zobaczysz, że będzie on używany `id` z `Url.Action("Buy", "Products", new { id = 17 })`usługą. Według Konwencji obiekt *wartości trasy* jest zwykle obiektem typu anonimowego, ale może również być `IDictionary<>` lub *zwykłym starym obiektem platformy .NET*. Wszystkie dodatkowe wartości trasy, które nie pasują do parametrów trasy, są umieszczane w ciągu zapytania.

[!code-csharp[](routing/samples/2.x/main/Controllers/TestController.cs)]

> [!TIP]
> Aby utworzyć bezwzględny adres URL, Użyj przeciążenia, które `protocol`akceptuje:`Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a>Generowanie adresów URL według trasy

Powyższy kod wygeneruje adres URL przez przekazanie go do kontrolera i nazwy akcji. `IUrlHelper`zapewnia także `Url.RouteUrl` rodzinę metod. Te metody są podobne do `Url.Action`, ale nie kopiują bieżących wartości `action` i `controller` do wartości trasy. Najbardziej typowym zastosowaniem jest określenie nazwy trasy do użycia określonej trasy do wygenerowania adresu URL, na ogół *bez* określania kontrolera lub nazwy akcji.

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a>Generowanie adresów URL w kodzie HTML

`IHtmlHelper``HtmlHelper` zapewnia `Html.BeginForm` metody `Html.ActionLink` i wygenerowanie `<form>` odpowiednio elementów `<a>` i. Metody te używają `Url.Action` metody do generowania adresu URL i akceptują podobne argumenty. `Url.RouteUrl` Pomocników dla programu `HtmlHelper` mają `Html.BeginRouteForm` `Html.RouteLink` podobną funkcjonalność.

TagHelpers Generuj adresy URL za `form` pomocą TagHelper i `<a>` TagHelper. Oba te zastosowania `IUrlHelper` dla ich implementacji. Aby uzyskać więcej informacji, zobacz [Praca z formularzami](../views/working-with-forms.md) .

W widokach można `IUrlHelper` korzystać z `Url` właściwości dla dowolnej generacji adresów URL ad hoc, która nie jest objęta powyższym.

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a>Generowanie adresów URL w wynikach akcji

Powyższe przykłady przedstawiono przy użyciu `IUrlHelper` w kontrolerze, podczas gdy najbardziej typowym użyciem na kontrolerze jest WYGENEROWANIE adresu URL jako części wyniku akcji.

Klasy `ControllerBase` bazowe `Controller` i zapewniają wygodne metody dla wyników akcji, które odwołują się do innej akcji. Jednym z typowych zastosowań jest przekierowanie po zaakceptowaniu danych wejściowych użytkownika.

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

Metody fabryki wyników akcji postępują zgodnie z podobnym wzorcem do `IUrlHelper`metod w.

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a>Specjalny przypadek dla dedykowanych tras konwencjonalnych

Funkcja routingu konwencjonalnego może używać specjalnego rodzaju definicji trasy o nazwie *dedykowanej, konwencjonalnej trasy*. W poniższym przykładzie trasa o nazwie `blog` jest dedykowaną umowną trasą.

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Użycie tych definicji tras `Url.Action("Index", "Home")` spowoduje wygenerowanie ścieżki `/` URL z `default` trasą, ale dlaczego? Możliwe jest odpuszczenie, że `{ controller = Home, action = Index }` wartości trasy byłyby wystarczające do WYGENEROWANIA `blog`adresu URL przy użyciu, a `/blog?action=Index&controller=Home`wynikiem będzie.

Dedykowane konwencjonalne trasy polegają na specjalnym zachowaniu wartości domyślnych, które nie mają odpowiadającego parametru trasy, który uniemożliwia trasie "zbyt zachłanne" z generowaniem adresów URL. W takim przypadku wartości domyślne to `{ controller = Blog, action = Article }`, a `controller` ani nie `action` pojawiają się jako parametr trasy. Gdy Routing wykonuje generowanie adresów URL, podane wartości muszą być zgodne z wartościami domyślnymi. Generowanie `blog` adresu URL nie powiedzie się, `{ controller = Home, action = Index }` ponieważ wartości `{ controller = Blog, action = Article }`nie są zgodne. Następnie usługa routingu wraca do wypróbowania `default`, która się powiedzie.

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a>Obszary

[Obszary](areas.md) są funkcją MVC służącą do organizowania powiązanych funkcji w grupie jako oddzielnej przestrzeni nazw routingu (dla akcji kontrolera) i struktury folderów (dla widoków). Użycie obszarów umożliwia aplikacji posiadanie wielu kontrolerów o tej samej nazwie, o ile mają one różne *obszary*. Za pomocą obszarów tworzy hierarchię na potrzeby routingu przez dodanie kolejnego parametru trasy `area` do `controller` i. `action` W tej sekcji omówiono sposób, w jaki Routing współdziała z obszarami — zobacz [obszary](areas.md) , aby uzyskać szczegółowe informacje o tym, jak obszary są używane w widokach.

Poniższy przykład konfiguruje MVC do używania domyślnej trasy konwencjonalnej i *trasy obszaru* dla obszaru o nazwie `Blog`:

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

W przypadku dopasowania ścieżki adresu URL `/Manage/Users/AddUser`, na przykład, Pierwsza trasa spowoduje wygenerowanie `{ area = Blog, controller = Users, action = AddUser }`wartości trasy. Wartość `area` trasy jest generowana przez wartość domyślną dla `area`, w rzeczywistości trasa utworzona przez `MapAreaRoute` jest równoważna z następującymi:

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet2)]

`MapAreaRoute`tworzy trasę przy użyciu wartości domyślnej i ograniczenia dotyczącej `area` użycia podanej nazwy obszaru, w tym przypadku `Blog`. Wartość domyślna zapewnia, że trasa zawsze produkuje `{ area = Blog, ... }`, ograniczenie wymaga wartości `{ area = Blog, ... }` dla generowania adresów URL.

> [!TIP]
> Routowanie konwencjonalne jest zależne od kolejności. Ogólnie rzecz biorąc, trasy z obszarami należy umieścić wcześniej w tabeli tras, ponieważ są one bardziej specyficzne niż trasy bez obszaru.

Korzystając z powyższego przykładu, wartości trasy będą zgodne z następującą akcją:

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

`AreaAttribute` Wskazuje to, co oznacza kontroler w ramach obszaru. Załóżmy, że ten kontroler znajduje się w `Blog` obszarze. Kontrolery bez `[Area]` atrybutu nie są członkami żadnego obszaru i **nie** będą zgodne, gdy wartość `area` trasy zostanie podana przez Routing. W poniższym przykładzie tylko pierwszy kontroler może być zgodny z wartościami `{ area = Blog, controller = Users, action = AddUser }`trasy.

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> Przestrzeń nazw każdego kontrolera jest pokazana w tym miejscu w celu zapewnienia kompletności — w przeciwnym razie kontrolery będą mieć konflikt nazw i generują błąd kompilatora. Przestrzenie nazw klas nie mają wpływu na Routing MVC.

Pierwsze dwa kontrolery są członkami obszarów i są zgodne tylko wtedy, gdy ich nazwa obszaru jest podana przez wartość `area` trasy. Trzeci kontroler nie jest członkiem żadnego obszaru i można go dopasować tylko wtedy, gdy nie ma `area` żadnej wartości parametru Routing.

> [!NOTE]
> W warunkach pasujących do *nie ma żadnej*wartości, brak `area` wartości jest taka sama jak wartość `area` null lub pusty ciąg.

Podczas wykonywania akcji wewnątrz obszaru wartość trasy dla `area` będzie dostępna jako *wartość otoczenia* dla routingu do użycia na potrzeby generowania adresów URL. Oznacza to, że domyślnie obszary programu *Sticky Notes* mają być używane do generowania adresów URL, jak pokazano w poniższym przykładzie.
[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a>Zrozumienie IActionConstraint

> [!NOTE]
> Ta sekcja jest głęboką szczegółoweą wewnętrznych struktur oraz jak MVC wybiera akcję do wykonania. Typowa aplikacja nie będzie potrzebować niestandardowego`IActionConstraint`

Prawdopodobnie już było używane `IActionConstraint` , nawet jeśli nie masz doświadczenia z interfejsem. `[HttpGet]` Atrybut i podobne `[Http-VERB]` atrybuty implementują `IActionConstraint` w celu ograniczenia wykonywania metody akcji.

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

Przy założeniu domyślnej trasy konwencjonalnej, ścieżka `/Products/Edit` URL będzie generować wartości `{ controller = Products, action = Edit }`, które byłyby zgodne z **obiema** akcjami przedstawionymi w tym miejscu. W `IActionConstraint` terminologii mamy, że obie te akcje są uważane za kandydatów, ponieważ oba te są zgodne z danymi trasy.

Gdy zostanie wykonane, zobaczy, że *Edit ()* jest dopasowaniem do get i nie jest dopasowaniem dla żadnego innego zlecenia http. *GET* `HttpGetAttribute` `Edit(...)` Akcja nie ma zdefiniowanych żadnych ograniczeń i dlatego będzie pasować do dowolnego czasownika http. Dlatego przy założeniu `POST` , `Edit(...)` że dopasowań są tylko. Jednak w przypadku `GET` obu akcji można nadal dopasować się, jednak akcja z funkcją `IActionConstraint` jest zawsze uznawana za *lepszą* od akcji bez. `Edit()` Dlatego, że jest traktowany jako bardziej szczegółowy i zostanie wybrany, jeśli obie akcje mogą być `[HttpGet]` zgodne.

Koncepcyjnie, `IActionConstraint` jest formą *przeciążenia*, ale zamiast przeciążać metody o tej samej nazwie, przeciążanie między akcjami, które pasują do tego samego adresu URL. Funkcja routingu atrybutów używa `IActionConstraint` także i może powodować, że działania z różnych kontrolerów są traktowane jako kandydaci.

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a>Implementowanie IActionConstraint

Najprostszym sposobem implementacji `IActionConstraint` jest utworzenie klasy pochodnej od `System.Attribute` i umieszczenie jej w akcjach i kontrolerach. MVC automatycznie odnajdzie wszystkie `IActionConstraint` , które są stosowane jako atrybuty. Możesz użyć modelu aplikacji, aby zastosować ograniczenia i prawdopodobnie jest to najbardziej elastyczne podejście, ponieważ pozwala to na to, w jaki sposób są stosowane.

W poniższym przykładzie ograniczenie wybiera akcję na podstawie *kodu kraju* z danych trasy. [Pełny przykład w witrynie GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).

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

Użytkownik jest odpowiedzialny za implementację `Accept` metody i wybranie "Order" dla ograniczenia, które ma zostać wykonane. W tym przypadku `Accept` Metoda zwraca `true` wartość, aby zauważyć, że akcja jest dopasowanie, gdy wartość `country` trasy jest zgodna. Różni się to od elementu `RouteValueAttribute` , który umożliwia powrót do akcji, która nie ma atrybutu. Przykład pokazuje, że w przypadku zdefiniowania `en-US` akcji, kod kraju, taki `fr-FR` jak, powróci do bardziej ogólnego kontrolera, który nie `[CountrySpecific(...)]` został zastosowany.

Właściwość decyduje, który etap jest częścią tego ograniczenia. *stage* `Order` Ograniczenia akcji są uruchamiane w grupach na podstawie `Order`. Na przykład wszystkie atrybuty metody HTTP podane przez platformę używają tej samej `Order` wartości, aby były uruchamiane na tym samym etapie. Możesz mieć tyle etapów, ile potrzebujesz, aby zaimplementować odpowiednie zasady.

> [!TIP]
> Aby zdecydować, `Order` czy należy zastosować ograniczenie, przed metodami http. Najpierw należy uruchomić mniejsze numery.

::: moniker-end
