---
title: Trasy stron brzytwy i konwencje aplikacji w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak konwencje dostawców tras i modeli aplikacji ułatwiają kontrolowanie routingu, odnajdowania i przetwarzania stron.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: b42d63c8f1b5b48fcfc771923171e1105d3f0a29
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277317"
---
# <a name="razor-pages-route-and-app-conventions-in-aspnet-core"></a>Trasy stron brzytwy i konwencje aplikacji w ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Dowiedz się, jak używać [konwencji routingu i dostawcy modelu aplikacji](xref:mvc/controllers/application-model#conventions) do kontrolowania routingu, odnajdowania i przetwarzania stron w aplikacjach Razor Pages.

Jeśli trzeba skonfigurować niestandardowe trasy stron dla poszczególnych stron, należy skonfigurować routing do stron z [Konwencją AddPageRoute](#configure-a-page-route) opisaną w dalszej części tego tematu.

Aby określić trasę strony, dodać segmenty trasy lub dodać parametry `@page` do trasy, użyj dyrektywy strony. Aby uzyskać więcej informacji, zobacz [Trasy niestandardowe](xref:razor-pages/index#custom-routes).

Istnieją słowa zastrzeżone, których nie można używać jako segmentów trasy ani nazw parametrów. Aby uzyskać więcej informacji, zobacz [Routing: Zarezerwowane nazwy routingu](xref:fundamentals/routing#reserved-routing-names).

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))

| Scenariusz | Przykład pokazuje ... |
| -------- | --------------------------- |
| [Konwencje modelu](#model-conventions)<br><br>Conventions.Add<ul><li>IPageRouteModelKonwencja</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelKonwencja</li></ul> | Dodawanie szablonu trasy i nagłówka do stron aplikacji. |
| [Konwencje akcji marszruty strony](#page-route-action-conventions)<ul><li>DodajfolderRouteModelConvention</li><li>Strona AddPageRouteModelConvention</li><li>Dodaj stronęProsie</li></ul> | Dodawanie szablonu trasy do stron w folderze i do jednej strony. |
| [Konwencje akcji modelu strony](#page-model-action-conventions)<ul><li>DodajfolderApplicationModelConvention</li><li>Strona AddPageApplicationModelConvention</li><li>ConfigureFilter (klasa filtru, wyrażenie lambda lub fabryka filtrów)</li></ul> | Dodaj nagłówek do stron w folderze, dodaj nagłówek do jednej strony i skonfiguruj [fabrykę filtrów,](xref:mvc/controllers/filters#ifilterfactory) aby dodać nagłówek do stron aplikacji. |

Konwencje stron maszynki do golenia <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> są <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> dodawane i konfigurowane przy użyciu metody rozszerzenia do kolekcji usług w `Startup` klasie. Następujące przykłady konwencji są wyjaśnione w dalszej części tego tematu:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

## <a name="route-order"></a>Kolejność marszruty

Trasy określają <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> do przetworzenia (dopasowywanie tras).

| Zamówienie            | Zachowanie |
| :--------------: | -------- |
| -1               | Trasa jest przetwarzana przed przetworzeniem innych tras. |
| 0                | Kolejność nie jest określona (wartość domyślna). Nie `Order` przypisywanie`Order = null`( ) `Order` domyślnie trasy do 0 (zero) do przetwarzania. |
| 1, 2, &hellip; n | Określa kolejność przetwarzania trasy. |

Przetwarzanie trasy jest ustanawiane zgodnie z konwencją:

* Trasy są przetwarzane w kolejności (-1, 0, 1, &hellip; 2, n).
* Gdy trasy mają `Order`to samo , najbardziej konkretna trasa jest dopasowywała najpierw mniej konkretne trasy.
* Gdy trasy o `Order` tej samej i takiej samej liczbie parametrów odpowiadają adresowi URL <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>żądania, trasy są przetwarzane w kolejności, w jakiej są dodawane do pliku .

Jeśli to możliwe, należy unikać w zależności od ustalonego zamówienia przetwarzania trasy. Ogólnie rzecz biorąc routing wybiera prawidłową trasę z dopasowaniem adresu URL. Jeśli musisz ustawić `Order` właściwości trasy do routingu żądań poprawnie, schemat routingu aplikacji jest prawdopodobnie mylące dla klientów i kruche do utrzymania. Dążyć do uproszczenia schematu routingu aplikacji. Przykładowa aplikacja wymaga jawnego zamówienia przetwarzania trasy, aby zademonstrować kilka scenariuszy routingu przy użyciu jednej aplikacji. Należy jednak podjąć próbę uniknięcia praktyki `Order` ustawiania trasy w aplikacjach produkcyjnych.

Routing stron razor i routing kontrolera MVC współużytkują implementację. Informacje o kolejności marszruty w tematach MVC są dostępne w [serwisie Routing do akcji kontrolera: Zamawianie tras atrybutów](xref:mvc/controllers/routing#ordering-attribute-routes).

## <a name="model-conventions"></a>Konwencje modelu

Dodaj <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> pełnomocnika, aby dodać [konwencje modelu,](xref:mvc/controllers/application-model#conventions) które mają zastosowanie do stron Razor.

### <a name="add-a-route-model-convention-to-all-pages"></a>Dodawanie konwencji modelu trasy do wszystkich stron

Służy <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> do tworzenia <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> i dodawania <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> do kolekcji wystąpień, które są stosowane podczas budowy modelu trasy strony.

Przykładowa aplikacja `{globalTemplate?}` dodaje szablon trasy do wszystkich stron w aplikacji:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

Właściwość <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> dla <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> jest `1`ustawiona na . Zapewnia to następujące zachowanie dopasowywania trasy w przykładowej aplikacji:

* Szablon trasy `TheContactPage/{text?}` dla jest dodawany w dalszej części tematu. Trasa strony kontaktu ma domyślną kolejność `null` (`Order = 0`), `{globalTemplate?}` więc jest zgodna przed szablonem trasy.
* Szablon `{aboutTemplate?}` trasy zostanie dodany w dalszej części tematu. Szablon `{aboutTemplate?}` otrzymuje . `Order` `2` Gdy strona Informacje jest `/About/RouteDataValue`wymagane w , "RouteDataValue" jest `RouteData.Values["aboutTemplate"]` `Order = 2`ładowany do `Order` `RouteData.Values["globalTemplate"]` (`Order = 1`) i nie ( ) z powodu ustawienia właściwości.
* Szablon `{otherPagesTemplate?}` trasy zostanie dodany w dalszej części tematu. Szablon `{otherPagesTemplate?}` otrzymuje . `Order` `2` Gdy dowolna strona w folderze *Pages/OtherPages* jest żądana z parametrem trasy (na `/OtherPages/Page1/RouteDataValue` `RouteData.Values["globalTemplate"]` przykład), "RouteDataValue" jest ładowany`Order = 1`do ( ) i nie `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) z powodu ustawienia `Order` właściwości.

Tam, gdzie to możliwe, nie `Order` `Order = 0`ustawiaj , co powoduje. Polegaj na routingu, aby wybrać właściwą trasę.

Opcje stron maszynki do <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>golenia, takie jak dodawanie , `Startup.ConfigureServices`są dodawane po dodaniu MVC do kolekcji usługi w pliku . Na przykład zobacz [przykładową aplikację](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

Poproś przykładową stronę Informacje `localhost:5000/About/GlobalRouteValue` o i sprawdź wynik:

![Strona Informacje jest żądana z segmentem trasy GlobalRouteValue. Renderowana strona pokazuje, że wartość danych trasy jest przechwytywany w OnGet metody strony.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>Dodawanie konwencji modelu aplikacji do wszystkich stron

Służy <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> do tworzenia <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> i dodawania <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> do kolekcji wystąpień, które są stosowane podczas tworzenia modelu aplikacji strony.

Aby zademonstrować tę i inne konwencje w dalszej części tematu, przykładowa aplikacja zawiera `AddHeaderAttribute` klasę. Konstruktor klasy akceptuje `name` zestaw `values` ciągów i tablicy ciągów. Te wartości są `OnResultExecuting` używane w jego metody, aby ustawić nagłówek odpowiedzi. Pełna klasa jest wyświetlana w sekcji [Konwencje akcji modelu strony](#page-model-action-conventions) w dalszej części tematu.

Przykładowa aplikacja używa `AddHeaderAttribute` klasy, aby `GlobalHeader`dodać nagłówek, do wszystkich stron w aplikacji:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs:*

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

Poproś przykładową stronę Informacje `localhost:5000/About` o i sprawdź nagłówki, aby wyświetlić wynik:

![Nagłówki odpowiedzi na stronie Informacje pokazują, że globalheader został dodany.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>Dodawanie konwencji modelu programu obsługi do wszystkich stron

Służy <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> do tworzenia <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> i dodawania <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> do kolekcji wystąpień, które są stosowane podczas budowy modelu programu obsługi strony.

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs:*

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>Konwencje akcji marszruty strony

Domyślny dostawca modelu trasy, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> który wywodzi się z konwencji wywoływania, które są przeznaczone do zapewnienia punktów rozszerzalności do konfigurowania tras stron.

### <a name="folder-route-model-convention"></a>Konwencja modelu trasy folderu

Służy <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> do tworzenia <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> i dodawania akcji, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> która wywołuje akcję na wszystkich stronach w określonym folderze.

Przykładowa aplikacja <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> służy do `{otherPagesTemplate?}` dodawania szablonu trasy do stron w folderze *OtherPages:*

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

Właściwość <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> dla <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> jest `2`ustawiona na . Gwarantuje to, że `{globalTemplate?}` szablon (ustawiony wcześniej `1`w temacie do) ma pierwszeństwo dla pierwszej pozycji wartości danych trasy, gdy podano pojedynczą wartość trasy. Jeśli strona w folderze *Pages/OtherPages* jest żądana z wartością parametru trasy (na `/OtherPages/Page1/RouteDataValue`przykład), `RouteData.Values["globalTemplate"]` `Order = 1`"RouteDataValue" jest ładowany do ( ) i nie `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) z powodu ustawienia `Order` właściwości.

Tam, gdzie to możliwe, nie `Order` `Order = 0`ustawiaj , co powoduje. Polegaj na routingu, aby wybrać właściwą trasę.

Poproś o przykładową stronę `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` Page1 i sprawdź wynik:

![Strona1 w folderze OtherPages jest żądana z segmentem trasy GlobalRouteValue i OtherPagesRouteValue. Renderowana strona pokazuje, że wartości danych trasy są przechwytywane w OnGet metody strony.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>Konwencja modelu trasy strony

Służy <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> do tworzenia <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> i dodawania akcji, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> która wywołuje akcję na stronie o określonej nazwie.

Przykładowa aplikacja `AddPageRouteModelConvention` służy do `{aboutTemplate?}` dodawania szablonu trasy do strony Informacje:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

Właściwość <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> dla <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> jest `2`ustawiona na . Gwarantuje to, że `{globalTemplate?}` szablon (ustawiony wcześniej `1`w temacie do) ma pierwszeństwo dla pierwszej pozycji wartości danych trasy, gdy podano pojedynczą wartość trasy. Jeśli strona Informacje jest żądana z `/About/RouteDataValue`wartością parametru trasy w , `RouteData.Values["globalTemplate"]` "RouteDataValue" jest ładowany do (`Order = 1`) i nie `RouteData.Values["aboutTemplate"]` (`Order = 2`) z powodu ustawienia `Order` właściwości.

Tam, gdzie to możliwe, nie `Order` `Order = 0`ustawiaj , co powoduje. Polegaj na routingu, aby wybrać właściwą trasę.

Poproś przykładową stronę Informacje `localhost:5000/About/GlobalRouteValue/AboutRouteValue` o i sprawdź wynik:

![Strona Informacje jest żądana z segmentami trasy dla GlobalRouteValue i AboutRouteValue. Renderowana strona pokazuje, że wartości danych trasy są przechwytywane w OnGet metody strony.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a>Dostosowywanie tras stron za pomocą transformatora parametrów

Trasy strony generowane przez ASP.NET Core można dostosować za pomocą transformatora parametrów. Transformator parametrów `IOutboundParameterTransformer` implementuje i przekształca wartość parametrów. Na przykład transformator parametrów `SlugifyParameterTransformer` `SubscriptionManagement` niestandardowych `subscription-management`zmienia wartość trasy na .

Konwencja `PageRouteTransformerConvention` modelu trasy strony stosuje transformator parametrów do segmentów folderu i nazw plików automatycznie generowanych tras stron w aplikacji. Na przykład plik Razor Pages w */Pages/SubscriptionManagement/ViewAll.cshtml* miałby `/SubscriptionManagement/ViewAll` swoją `/subscription-management/view-all`trasę przepisaną z do .

`PageRouteTransformerConvention`przekształca tylko automatycznie generowane segmenty trasy strony, które pochodzą z folderu Razor Pages i nazwy pliku. Nie przekształca segmentów trasy dodanych `@page` wraz z dyrektywą. Konwencja nie przekształca również tras <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>dodanych przez .

Jest `PageRouteTransformerConvention` zarejestrowany jako opcja `Startup.ConfigureServices`w:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add(
                new PageRouteTransformerConvention(
                    new SlugifyParameterTransformer()));
        });
}
```

[!code-csharp[](~/mvc/controllers/routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

[!INCLUDE[](~/includes/regex.md)]

## <a name="configure-a-page-route"></a>Konfigurowanie trasy strony

Służy <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> do konfigurowania trasy do strony w określonej ścieżce strony. Wygenerowane łącza do strony używają określonej trasy. `AddPageRoute`do `AddPageRouteModelConvention` ustalenia trasy.

Przykładowa aplikacja tworzy `/TheContactPage` trasę do *contact.cshtml:*

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet5)]

Do strony Kontakt można `/Contact` również uzyskać pod adresem za pomocą jej domyślnej trasy.

Niestandardowa trasa aplikacji przykładowej do strony Kontakt `text` umożliwia`{text?}`opcjonalny segment trasy ( ). Strona zawiera również ten opcjonalny segment w swojej `@page` dyrektywie `/Contact` w przypadku, gdy użytkownik uzyskuje dostęp do strony na swojej trasie:

[!code-cshtml[](razor-pages-conventions/samples/3.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

Należy zauważyć, że adres URL wygenerowany dla łącza **Kontakt** na renderowanej stronie odzwierciedla zaktualizowaną trasę:

![Przykładowe łącze kontaktowe aplikacji na pasku nawigacyjnym](razor-pages-conventions/_static/contact-link.png)

![Sprawdzenie łącza Kontakt w renderowanym kodzie HTML wskazuje, że href jest ustawiony na "/TheContactPage"](razor-pages-conventions/_static/contact-link-source.png)

Odwiedź stronę Kontakt przy zwykłej `/Contact`trasie lub trasie `/TheContactPage`niestandardowej . Jeśli podasz `text` dodatkowy segment trasy, strona pokazuje segment zakodowany w formacie HTML, który podajesz:

![Przykład przeglądarki krawędzi dostarczania opcjonalnego segmentu trasy "tekst" "TextValue" w adresie URL. Renderowana strona pokazuje wartość segmentu "tekst".](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>Konwencje akcji modelu strony

Domyślny dostawca modelu strony, który implementuje <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> wywoływania konwencji, które są przeznaczone do zapewnienia punktów rozszerzalności do konfigurowania modeli stron. Konwencje te są przydatne podczas tworzenia i modyfikowania scenariuszy odnajdowania i przetwarzania strony.

W przykładach w tej sekcji przykładowa `AddHeaderAttribute` aplikacja używa klasy, która jest <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, która stosuje nagłówek odpowiedzi:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

Korzystając z konwencji, w przykładzie pokazano, jak zastosować atrybut do wszystkich stron w folderze i do jednej strony.

**Konwencja modelu aplikacji folderu**

Służy <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> do tworzenia <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> i dodawania akcji <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> wywołującej akcję dla wystąpień dla wszystkich stron w określonym folderze.

Przykład pokazuje użycie `AddFolderApplicationModelConvention` przez dodanie nagłówka, `OtherPagesHeader`do stron wewnątrz *folderu OtherPages* aplikacji:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet6)]

Poproś przykładową stronę Page1 `localhost:5000/OtherPages/Page1` i sprawdź nagłówki, aby wyświetlić wynik:

![Nagłówki odpowiedzi strony OtherPages/Page1 pokazują, że dodano otherpagesheader.](razor-pages-conventions/_static/page1-otherpages-header.png)

**Konwencja modelu aplikacji strony**

Służy <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> do tworzenia <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> i dodawania akcji, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> która wywołuje akcję na stronie o określonej nazwie.

Przykład pokazuje użycie `AddPageApplicationModelConvention` przez dodanie nagłówka, `AboutHeader`do Informacje strony:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet7)]

Poproś przykładową stronę Informacje `localhost:5000/About` o i sprawdź nagłówki, aby wyświetlić wynik:

![Nagłówki odpowiedzi na stronie Informacje pokazują, że aboutheader został dodany.](razor-pages-conventions/_static/about-page-about-header.png)

**Konfigurowanie filtru**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>konfiguruje określony filtr do zastosowania. Można zaimplementować klasę filtru, ale przykładowa aplikacja pokazuje, jak zaimplementować filtr w wyrażeniu lambda, który jest implementowany za kulisami jako fabryka, która zwraca filtr:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet8)]

Model aplikacji strony służy do sprawdzania ścieżki względnej dla segmentów prowadzących do strony Page2 w folderze *OtherPages.* Jeśli warunek przejdzie, zostanie dodany nagłówek. Jeśli nie, `EmptyFilter` jest stosowany.

`EmptyFilter`jest [filtrem akcji](xref:mvc/controllers/filters#action-filters). Ponieważ filtry akcji są ignorowane przez `EmptyFilter` strony Razor Pages, nie ma `OtherPages/Page2`wpływu zgodnie z przeznaczeniem, jeśli ścieżka nie zawiera .

Poproś przykładową stronę Page2 `localhost:5000/OtherPages/Page2` o pozycję Page2 i sprawdź nagłówki, aby wyświetlić wynik:

![OtherPagesPage2Header zostanie dodany do odpowiedzi dla Page2.](razor-pages-conventions/_static/page2-filter-header.png)

**Konfigurowanie fabryki filtrów**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>konfiguruje określoną fabrykę, aby zastosować [filtry](xref:mvc/controllers/filters) do wszystkich stron Razor.

Przykładowa aplikacja zawiera przykład użycia [fabryki filtrów](xref:mvc/controllers/filters#ifilterfactory) `FilterFactoryHeader`przez dodanie nagłówka, z dwiema wartościami do stron aplikacji:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs:*

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

Poproś przykładową stronę Informacje `localhost:5000/About` o i sprawdź nagłówki, aby wyświetlić wynik:

![Nagłówki odpowiedzi na stronie Informacje pokazują, że dodano dwa nagłówki FilterFactoryHeader.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>Filtry MVC i filtr strony (IPageFilter)

[Filtry akcji](xref:mvc/controllers/filters#action-filters) MVC są ignorowane przez strony Razor Pages, ponieważ razor Pages używają metod obsługi. Dostępne są inne typy filtrów MVC: [Autoryzacja,](xref:mvc/controllers/filters#authorization-filters) [Wyjątek,](xref:mvc/controllers/filters#exception-filters) [Zasób](xref:mvc/controllers/filters#resource-filters)i [Wynik](xref:mvc/controllers/filters#result-filters). Aby uzyskać więcej informacji, zobacz temat [Filtry.](xref:mvc/controllers/filters)

Filtr Strony<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>( ) to filtr, który ma zastosowanie do stron Razor. Aby uzyskać więcej informacji, zobacz [Metody filtrowania stron razor](xref:razor-pages/filter).

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Dowiedz się, jak używać [konwencji routingu i dostawcy modelu aplikacji](xref:mvc/controllers/application-model#conventions) do kontrolowania routingu, odnajdowania i przetwarzania stron w aplikacjach Razor Pages.

Jeśli trzeba skonfigurować niestandardowe trasy stron dla poszczególnych stron, należy skonfigurować routing do stron z [Konwencją AddPageRoute](#configure-a-page-route) opisaną w dalszej części tego tematu.

Aby określić trasę strony, dodać segmenty trasy lub dodać parametry `@page` do trasy, użyj dyrektywy strony. Aby uzyskać więcej informacji, zobacz [Trasy niestandardowe](xref:razor-pages/index#custom-routes).

Istnieją słowa zastrzeżone, których nie można używać jako segmentów trasy ani nazw parametrów. Aby uzyskać więcej informacji, zobacz [Routing: Zarezerwowane nazwy routingu](xref:fundamentals/routing#reserved-routing-names).

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))

| Scenariusz | Przykład pokazuje ... |
| -------- | --------------------------- |
| [Konwencje modelu](#model-conventions)<br><br>Conventions.Add<ul><li>IPageRouteModelKonwencja</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelKonwencja</li></ul> | Dodawanie szablonu trasy i nagłówka do stron aplikacji. |
| [Konwencje akcji marszruty strony](#page-route-action-conventions)<ul><li>DodajfolderRouteModelConvention</li><li>Strona AddPageRouteModelConvention</li><li>Dodaj stronęProsie</li></ul> | Dodawanie szablonu trasy do stron w folderze i do jednej strony. |
| [Konwencje akcji modelu strony](#page-model-action-conventions)<ul><li>DodajfolderApplicationModelConvention</li><li>Strona AddPageApplicationModelConvention</li><li>ConfigureFilter (klasa filtru, wyrażenie lambda lub fabryka filtrów)</li></ul> | Dodaj nagłówek do stron w folderze, dodaj nagłówek do jednej strony i skonfiguruj [fabrykę filtrów,](xref:mvc/controllers/filters#ifilterfactory) aby dodać nagłówek do stron aplikacji. |

Konwencje stron maszynki do golenia <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> są <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> dodawane i konfigurowane przy użyciu metody rozszerzenia do kolekcji usług w `Startup` klasie. Następujące przykłady konwencji są wyjaśnione w dalszej części tego tematu:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

## <a name="route-order"></a>Kolejność marszruty

Trasy określają <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> do przetworzenia (dopasowywanie tras).

| Zamówienie            | Zachowanie |
| :--------------: | -------- |
| -1               | Trasa jest przetwarzana przed przetworzeniem innych tras. |
| 0                | Kolejność nie jest określona (wartość domyślna). Nie `Order` przypisywanie`Order = null`( ) `Order` domyślnie trasy do 0 (zero) do przetwarzania. |
| 1, 2, &hellip; n | Określa kolejność przetwarzania trasy. |

Przetwarzanie trasy jest ustanawiane zgodnie z konwencją:

* Trasy są przetwarzane w kolejności (-1, 0, 1, &hellip; 2, n).
* Gdy trasy mają `Order`to samo , najbardziej konkretna trasa jest dopasowywała najpierw mniej konkretne trasy.
* Gdy trasy o `Order` tej samej i takiej samej liczbie parametrów odpowiadają adresowi URL <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>żądania, trasy są przetwarzane w kolejności, w jakiej są dodawane do pliku .

Jeśli to możliwe, należy unikać w zależności od ustalonego zamówienia przetwarzania trasy. Ogólnie rzecz biorąc routing wybiera prawidłową trasę z dopasowaniem adresu URL. Jeśli musisz ustawić `Order` właściwości trasy do routingu żądań poprawnie, schemat routingu aplikacji jest prawdopodobnie mylące dla klientów i kruche do utrzymania. Dążyć do uproszczenia schematu routingu aplikacji. Przykładowa aplikacja wymaga jawnego zamówienia przetwarzania trasy, aby zademonstrować kilka scenariuszy routingu przy użyciu jednej aplikacji. Należy jednak podjąć próbę uniknięcia praktyki `Order` ustawiania trasy w aplikacjach produkcyjnych.

Routing stron razor i routing kontrolera MVC współużytkują implementację. Informacje o kolejności marszruty w tematach MVC są dostępne w [serwisie Routing do akcji kontrolera: Zamawianie tras atrybutów](xref:mvc/controllers/routing#ordering-attribute-routes).

## <a name="model-conventions"></a>Konwencje modelu

Dodaj <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> pełnomocnika, aby dodać [konwencje modelu,](xref:mvc/controllers/application-model#conventions) które mają zastosowanie do stron Razor.

### <a name="add-a-route-model-convention-to-all-pages"></a>Dodawanie konwencji modelu trasy do wszystkich stron

Służy <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> do tworzenia <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> i dodawania <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> do kolekcji wystąpień, które są stosowane podczas budowy modelu trasy strony.

Przykładowa aplikacja `{globalTemplate?}` dodaje szablon trasy do wszystkich stron w aplikacji:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

Właściwość <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> dla <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> jest `1`ustawiona na . Zapewnia to następujące zachowanie dopasowywania trasy w przykładowej aplikacji:

* Szablon trasy `TheContactPage/{text?}` dla jest dodawany w dalszej części tematu. Trasa strony kontaktu ma domyślną kolejność `null` (`Order = 0`), `{globalTemplate?}` więc jest zgodna przed szablonem trasy.
* Szablon `{aboutTemplate?}` trasy zostanie dodany w dalszej części tematu. Szablon `{aboutTemplate?}` otrzymuje . `Order` `2` Gdy strona Informacje jest `/About/RouteDataValue`wymagane w , "RouteDataValue" jest `RouteData.Values["aboutTemplate"]` `Order = 2`ładowany do `Order` `RouteData.Values["globalTemplate"]` (`Order = 1`) i nie ( ) z powodu ustawienia właściwości.
* Szablon `{otherPagesTemplate?}` trasy zostanie dodany w dalszej części tematu. Szablon `{otherPagesTemplate?}` otrzymuje . `Order` `2` Gdy dowolna strona w folderze *Pages/OtherPages* jest żądana z parametrem trasy (na `/OtherPages/Page1/RouteDataValue` `RouteData.Values["globalTemplate"]` przykład), "RouteDataValue" jest ładowany`Order = 1`do ( ) i nie `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) z powodu ustawienia `Order` właściwości.

Tam, gdzie to możliwe, nie `Order` `Order = 0`ustawiaj , co powoduje. Polegaj na routingu, aby wybrać właściwą trasę.

Opcje stron maszynki do <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>golenia, takie jak dodawanie , `Startup.ConfigureServices`są dodawane po dodaniu MVC do kolekcji usługi w pliku . Na przykład zobacz [przykładową aplikację](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

Poproś przykładową stronę Informacje `localhost:5000/About/GlobalRouteValue` o i sprawdź wynik:

![Strona Informacje jest żądana z segmentem trasy GlobalRouteValue. Renderowana strona pokazuje, że wartość danych trasy jest przechwytywany w OnGet metody strony.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>Dodawanie konwencji modelu aplikacji do wszystkich stron

Służy <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> do tworzenia <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> i dodawania <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> do kolekcji wystąpień, które są stosowane podczas tworzenia modelu aplikacji strony.

Aby zademonstrować tę i inne konwencje w dalszej części tematu, przykładowa aplikacja zawiera `AddHeaderAttribute` klasę. Konstruktor klasy akceptuje `name` zestaw `values` ciągów i tablicy ciągów. Te wartości są `OnResultExecuting` używane w jego metody, aby ustawić nagłówek odpowiedzi. Pełna klasa jest wyświetlana w sekcji [Konwencje akcji modelu strony](#page-model-action-conventions) w dalszej części tematu.

Przykładowa aplikacja używa `AddHeaderAttribute` klasy, aby `GlobalHeader`dodać nagłówek, do wszystkich stron w aplikacji:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs:*

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

Poproś przykładową stronę Informacje `localhost:5000/About` o i sprawdź nagłówki, aby wyświetlić wynik:

![Nagłówki odpowiedzi na stronie Informacje pokazują, że globalheader został dodany.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>Dodawanie konwencji modelu programu obsługi do wszystkich stron

Służy <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> do tworzenia <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> i dodawania <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> do kolekcji wystąpień, które są stosowane podczas budowy modelu programu obsługi strony.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs:*

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>Konwencje akcji marszruty strony

Domyślny dostawca modelu trasy, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> który wywodzi się z konwencji wywoływania, które są przeznaczone do zapewnienia punktów rozszerzalności do konfigurowania tras stron.

### <a name="folder-route-model-convention"></a>Konwencja modelu trasy folderu

Służy <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> do tworzenia <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> i dodawania akcji, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> która wywołuje akcję na wszystkich stronach w określonym folderze.

Przykładowa aplikacja <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> służy do `{otherPagesTemplate?}` dodawania szablonu trasy do stron w folderze *OtherPages:*

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

Właściwość <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> dla <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> jest `2`ustawiona na . Gwarantuje to, że `{globalTemplate?}` szablon (ustawiony wcześniej `1`w temacie do) ma pierwszeństwo dla pierwszej pozycji wartości danych trasy, gdy podano pojedynczą wartość trasy. Jeśli strona w folderze *Pages/OtherPages* jest żądana z wartością parametru trasy (na `/OtherPages/Page1/RouteDataValue`przykład), `RouteData.Values["globalTemplate"]` `Order = 1`"RouteDataValue" jest ładowany do ( ) i nie `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) z powodu ustawienia `Order` właściwości.

Tam, gdzie to możliwe, nie `Order` `Order = 0`ustawiaj , co powoduje. Polegaj na routingu, aby wybrać właściwą trasę.

Poproś o przykładową stronę `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` Page1 i sprawdź wynik:

![Strona1 w folderze OtherPages jest żądana z segmentem trasy GlobalRouteValue i OtherPagesRouteValue. Renderowana strona pokazuje, że wartości danych trasy są przechwytywane w OnGet metody strony.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>Konwencja modelu trasy strony

Służy <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> do tworzenia <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> i dodawania akcji, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> która wywołuje akcję na stronie o określonej nazwie.

Przykładowa aplikacja `AddPageRouteModelConvention` służy do `{aboutTemplate?}` dodawania szablonu trasy do strony Informacje:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

Właściwość <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> dla <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> jest `2`ustawiona na . Gwarantuje to, że `{globalTemplate?}` szablon (ustawiony wcześniej `1`w temacie do) ma pierwszeństwo dla pierwszej pozycji wartości danych trasy, gdy podano pojedynczą wartość trasy. Jeśli strona Informacje jest żądana z `/About/RouteDataValue`wartością parametru trasy w , `RouteData.Values["globalTemplate"]` "RouteDataValue" jest ładowany do (`Order = 1`) i nie `RouteData.Values["aboutTemplate"]` (`Order = 2`) z powodu ustawienia `Order` właściwości.

Tam, gdzie to możliwe, nie `Order` `Order = 0`ustawiaj , co powoduje. Polegaj na routingu, aby wybrać właściwą trasę.

Poproś przykładową stronę Informacje `localhost:5000/About/GlobalRouteValue/AboutRouteValue` o i sprawdź wynik:

![Strona Informacje jest żądana z segmentami trasy dla GlobalRouteValue i AboutRouteValue. Renderowana strona pokazuje, że wartości danych trasy są przechwytywane w OnGet metody strony.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a>Dostosowywanie tras stron za pomocą transformatora parametrów

Trasy strony generowane przez ASP.NET Core można dostosować za pomocą transformatora parametrów. Transformator parametrów `IOutboundParameterTransformer` implementuje i przekształca wartość parametrów. Na przykład transformator parametrów `SlugifyParameterTransformer` `SubscriptionManagement` niestandardowych `subscription-management`zmienia wartość trasy na .

Konwencja `PageRouteTransformerConvention` modelu trasy strony stosuje transformator parametrów do segmentów folderu i nazw plików automatycznie generowanych tras stron w aplikacji. Na przykład plik Razor Pages w */Pages/SubscriptionManagement/ViewAll.cshtml* miałby `/SubscriptionManagement/ViewAll` swoją `/subscription-management/view-all`trasę przepisaną z do .

`PageRouteTransformerConvention`przekształca tylko automatycznie generowane segmenty trasy strony, które pochodzą z folderu Razor Pages i nazwy pliku. Nie przekształca segmentów trasy dodanych `@page` wraz z dyrektywą. Konwencja nie przekształca również tras <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>dodanych przez .

Jest `PageRouteTransformerConvention` zarejestrowany jako opcja `Startup.ConfigureServices`w:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add(
                new PageRouteTransformerConvention(
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

## <a name="configure-a-page-route"></a>Konfigurowanie trasy strony

Służy <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> do konfigurowania trasy do strony w określonej ścieżce strony. Wygenerowane łącza do strony używają określonej trasy. `AddPageRoute`do `AddPageRouteModelConvention` ustalenia trasy.

Przykładowa aplikacja tworzy `/TheContactPage` trasę do *contact.cshtml:*

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

Do strony Kontakt można `/Contact` również uzyskać pod adresem za pomocą jej domyślnej trasy.

Niestandardowa trasa aplikacji przykładowej do strony Kontakt `text` umożliwia`{text?}`opcjonalny segment trasy ( ). Strona zawiera również ten opcjonalny segment w swojej `@page` dyrektywie `/Contact` w przypadku, gdy użytkownik uzyskuje dostęp do strony na swojej trasie:

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

Należy zauważyć, że adres URL wygenerowany dla łącza **Kontakt** na renderowanej stronie odzwierciedla zaktualizowaną trasę:

![Przykładowe łącze kontaktowe aplikacji na pasku nawigacyjnym](razor-pages-conventions/_static/contact-link.png)

![Sprawdzenie łącza Kontakt w renderowanym kodzie HTML wskazuje, że href jest ustawiony na "/TheContactPage"](razor-pages-conventions/_static/contact-link-source.png)

Odwiedź stronę Kontakt przy zwykłej `/Contact`trasie lub trasie `/TheContactPage`niestandardowej . Jeśli podasz `text` dodatkowy segment trasy, strona pokazuje segment zakodowany w formacie HTML, który podajesz:

![Przykład przeglądarki krawędzi dostarczania opcjonalnego segmentu trasy "tekst" "TextValue" w adresie URL. Renderowana strona pokazuje wartość segmentu "tekst".](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>Konwencje akcji modelu strony

Domyślny dostawca modelu strony, który implementuje <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> wywoływania konwencji, które są przeznaczone do zapewnienia punktów rozszerzalności do konfigurowania modeli stron. Konwencje te są przydatne podczas tworzenia i modyfikowania scenariuszy odnajdowania i przetwarzania strony.

W przykładach w tej sekcji przykładowa `AddHeaderAttribute` aplikacja używa klasy, która jest <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, która stosuje nagłówek odpowiedzi:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

Korzystając z konwencji, w przykładzie pokazano, jak zastosować atrybut do wszystkich stron w folderze i do jednej strony.

**Konwencja modelu aplikacji folderu**

Służy <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> do tworzenia <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> i dodawania akcji <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> wywołującej akcję dla wystąpień dla wszystkich stron w określonym folderze.

Przykład pokazuje użycie `AddFolderApplicationModelConvention` przez dodanie nagłówka, `OtherPagesHeader`do stron wewnątrz *folderu OtherPages* aplikacji:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

Poproś przykładową stronę Page1 `localhost:5000/OtherPages/Page1` i sprawdź nagłówki, aby wyświetlić wynik:

![Nagłówki odpowiedzi strony OtherPages/Page1 pokazują, że dodano otherpagesheader.](razor-pages-conventions/_static/page1-otherpages-header.png)

**Konwencja modelu aplikacji strony**

Służy <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> do tworzenia <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> i dodawania akcji, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> która wywołuje akcję na stronie o określonej nazwie.

Przykład pokazuje użycie `AddPageApplicationModelConvention` przez dodanie nagłówka, `AboutHeader`do Informacje strony:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

Poproś przykładową stronę Informacje `localhost:5000/About` o i sprawdź nagłówki, aby wyświetlić wynik:

![Nagłówki odpowiedzi na stronie Informacje pokazują, że aboutheader został dodany.](razor-pages-conventions/_static/about-page-about-header.png)

**Konfigurowanie filtru**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>konfiguruje określony filtr do zastosowania. Można zaimplementować klasę filtru, ale przykładowa aplikacja pokazuje, jak zaimplementować filtr w wyrażeniu lambda, który jest implementowany za kulisami jako fabryka, która zwraca filtr:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

Model aplikacji strony służy do sprawdzania ścieżki względnej dla segmentów prowadzących do strony Page2 w folderze *OtherPages.* Jeśli warunek przejdzie, zostanie dodany nagłówek. Jeśli nie, `EmptyFilter` jest stosowany.

`EmptyFilter`jest [filtrem akcji](xref:mvc/controllers/filters#action-filters). Ponieważ filtry akcji są ignorowane przez `EmptyFilter` strony Razor Pages, nie ma `OtherPages/Page2`wpływu zgodnie z przeznaczeniem, jeśli ścieżka nie zawiera .

Poproś przykładową stronę Page2 `localhost:5000/OtherPages/Page2` o pozycję Page2 i sprawdź nagłówki, aby wyświetlić wynik:

![OtherPagesPage2Header zostanie dodany do odpowiedzi dla Page2.](razor-pages-conventions/_static/page2-filter-header.png)

**Konfigurowanie fabryki filtrów**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>konfiguruje określoną fabrykę, aby zastosować [filtry](xref:mvc/controllers/filters) do wszystkich stron Razor.

Przykładowa aplikacja zawiera przykład użycia [fabryki filtrów](xref:mvc/controllers/filters#ifilterfactory) `FilterFactoryHeader`przez dodanie nagłówka, z dwiema wartościami do stron aplikacji:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs:*

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

Poproś przykładową stronę Informacje `localhost:5000/About` o i sprawdź nagłówki, aby wyświetlić wynik:

![Nagłówki odpowiedzi na stronie Informacje pokazują, że dodano dwa nagłówki FilterFactoryHeader.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>Filtry MVC i filtr strony (IPageFilter)

[Filtry akcji](xref:mvc/controllers/filters#action-filters) MVC są ignorowane przez strony Razor Pages, ponieważ razor Pages używają metod obsługi. Dostępne są inne typy filtrów MVC: [Autoryzacja,](xref:mvc/controllers/filters#authorization-filters) [Wyjątek,](xref:mvc/controllers/filters#exception-filters) [Zasób](xref:mvc/controllers/filters#resource-filters)i [Wynik](xref:mvc/controllers/filters#result-filters). Aby uzyskać więcej informacji, zobacz temat [Filtry.](xref:mvc/controllers/filters)

Filtr Strony<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>( ) to filtr, który ma zastosowanie do stron Razor. Aby uzyskać więcej informacji, zobacz [Metody filtrowania stron razor](xref:razor-pages/filter).

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Dowiedz się, jak używać [konwencji routingu i dostawcy modelu aplikacji](xref:mvc/controllers/application-model#conventions) do kontrolowania routingu, odnajdowania i przetwarzania stron w aplikacjach Razor Pages.

Jeśli trzeba skonfigurować niestandardowe trasy stron dla poszczególnych stron, należy skonfigurować routing do stron z [Konwencją AddPageRoute](#configure-a-page-route) opisaną w dalszej części tego tematu.

Aby określić trasę strony, dodać segmenty trasy lub dodać parametry `@page` do trasy, użyj dyrektywy strony. Aby uzyskać więcej informacji, zobacz [Trasy niestandardowe](xref:razor-pages/index#custom-routes).

Istnieją słowa zastrzeżone, których nie można używać jako segmentów trasy ani nazw parametrów. Aby uzyskać więcej informacji, zobacz [Routing: Zarezerwowane nazwy routingu](xref:fundamentals/routing#reserved-routing-names).

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))

| Scenariusz | Przykład pokazuje ... |
| -------- | --------------------------- |
| [Konwencje modelu](#model-conventions)<br><br>Conventions.Add<ul><li>IPageRouteModelKonwencja</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelKonwencja</li></ul> | Dodawanie szablonu trasy i nagłówka do stron aplikacji. |
| [Konwencje akcji marszruty strony](#page-route-action-conventions)<ul><li>DodajfolderRouteModelConvention</li><li>Strona AddPageRouteModelConvention</li><li>Dodaj stronęProsie</li></ul> | Dodawanie szablonu trasy do stron w folderze i do jednej strony. |
| [Konwencje akcji modelu strony](#page-model-action-conventions)<ul><li>DodajfolderApplicationModelConvention</li><li>Strona AddPageApplicationModelConvention</li><li>ConfigureFilter (klasa filtru, wyrażenie lambda lub fabryka filtrów)</li></ul> | Dodaj nagłówek do stron w folderze, dodaj nagłówek do jednej strony i skonfiguruj [fabrykę filtrów,](xref:mvc/controllers/filters#ifilterfactory) aby dodać nagłówek do stron aplikacji. |

Konwencje stron maszynki do golenia <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> są <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> dodawane i konfigurowane przy użyciu metody rozszerzenia do kolekcji usług w `Startup` klasie. Następujące przykłady konwencji są wyjaśnione w dalszej części tego tematu:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

## <a name="route-order"></a>Kolejność marszruty

Trasy określają <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> do przetworzenia (dopasowywanie tras).

| Zamówienie            | Zachowanie |
| :--------------: | -------- |
| -1               | Trasa jest przetwarzana przed przetworzeniem innych tras. |
| 0                | Kolejność nie jest określona (wartość domyślna). Nie `Order` przypisywanie`Order = null`( ) `Order` domyślnie trasy do 0 (zero) do przetwarzania. |
| 1, 2, &hellip; n | Określa kolejność przetwarzania trasy. |

Przetwarzanie trasy jest ustanawiane zgodnie z konwencją:

* Trasy są przetwarzane w kolejności (-1, 0, 1, &hellip; 2, n).
* Gdy trasy mają `Order`to samo , najbardziej konkretna trasa jest dopasowywała najpierw mniej konkretne trasy.
* Gdy trasy o `Order` tej samej i takiej samej liczbie parametrów odpowiadają adresowi URL <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>żądania, trasy są przetwarzane w kolejności, w jakiej są dodawane do pliku .

Jeśli to możliwe, należy unikać w zależności od ustalonego zamówienia przetwarzania trasy. Ogólnie rzecz biorąc routing wybiera prawidłową trasę z dopasowaniem adresu URL. Jeśli musisz ustawić `Order` właściwości trasy do routingu żądań poprawnie, schemat routingu aplikacji jest prawdopodobnie mylące dla klientów i kruche do utrzymania. Dążyć do uproszczenia schematu routingu aplikacji. Przykładowa aplikacja wymaga jawnego zamówienia przetwarzania trasy, aby zademonstrować kilka scenariuszy routingu przy użyciu jednej aplikacji. Należy jednak podjąć próbę uniknięcia praktyki `Order` ustawiania trasy w aplikacjach produkcyjnych.

Routing stron razor i routing kontrolera MVC współużytkują implementację. Informacje o kolejności marszruty w tematach MVC są dostępne w [serwisie Routing do akcji kontrolera: Zamawianie tras atrybutów](xref:mvc/controllers/routing#ordering-attribute-routes).

## <a name="model-conventions"></a>Konwencje modelu

Dodaj <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> pełnomocnika, aby dodać [konwencje modelu,](xref:mvc/controllers/application-model#conventions) które mają zastosowanie do stron Razor.

### <a name="add-a-route-model-convention-to-all-pages"></a>Dodawanie konwencji modelu trasy do wszystkich stron

Służy <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> do tworzenia <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> i dodawania <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> do kolekcji wystąpień, które są stosowane podczas budowy modelu trasy strony.

Przykładowa aplikacja `{globalTemplate?}` dodaje szablon trasy do wszystkich stron w aplikacji:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

Właściwość <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> dla <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> jest `1`ustawiona na . Zapewnia to następujące zachowanie dopasowywania trasy w przykładowej aplikacji:

* Szablon trasy `TheContactPage/{text?}` dla jest dodawany w dalszej części tematu. Trasa strony kontaktu ma domyślną kolejność `null` (`Order = 0`), `{globalTemplate?}` więc jest zgodna przed szablonem trasy.
* Szablon `{aboutTemplate?}` trasy zostanie dodany w dalszej części tematu. Szablon `{aboutTemplate?}` otrzymuje . `Order` `2` Gdy strona Informacje jest `/About/RouteDataValue`wymagane w , "RouteDataValue" jest `RouteData.Values["aboutTemplate"]` `Order = 2`ładowany do `Order` `RouteData.Values["globalTemplate"]` (`Order = 1`) i nie ( ) z powodu ustawienia właściwości.
* Szablon `{otherPagesTemplate?}` trasy zostanie dodany w dalszej części tematu. Szablon `{otherPagesTemplate?}` otrzymuje . `Order` `2` Gdy dowolna strona w folderze *Pages/OtherPages* jest żądana z parametrem trasy (na `/OtherPages/Page1/RouteDataValue` `RouteData.Values["globalTemplate"]` przykład), "RouteDataValue" jest ładowany`Order = 1`do ( ) i nie `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) z powodu ustawienia `Order` właściwości.

Tam, gdzie to możliwe, nie `Order` `Order = 0`ustawiaj , co powoduje. Polegaj na routingu, aby wybrać właściwą trasę.

Opcje stron maszynki do <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>golenia, takie jak dodawanie , `Startup.ConfigureServices`są dodawane po dodaniu MVC do kolekcji usługi w pliku . Na przykład zobacz [przykładową aplikację](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/).

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

Poproś przykładową stronę Informacje `localhost:5000/About/GlobalRouteValue` o i sprawdź wynik:

![Strona Informacje jest żądana z segmentem trasy GlobalRouteValue. Renderowana strona pokazuje, że wartość danych trasy jest przechwytywany w OnGet metody strony.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>Dodawanie konwencji modelu aplikacji do wszystkich stron

Służy <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> do tworzenia <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> i dodawania <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> do kolekcji wystąpień, które są stosowane podczas tworzenia modelu aplikacji strony.

Aby zademonstrować tę i inne konwencje w dalszej części tematu, przykładowa aplikacja zawiera `AddHeaderAttribute` klasę. Konstruktor klasy akceptuje `name` zestaw `values` ciągów i tablicy ciągów. Te wartości są `OnResultExecuting` używane w jego metody, aby ustawić nagłówek odpowiedzi. Pełna klasa jest wyświetlana w sekcji [Konwencje akcji modelu strony](#page-model-action-conventions) w dalszej części tematu.

Przykładowa aplikacja używa `AddHeaderAttribute` klasy, aby `GlobalHeader`dodać nagłówek, do wszystkich stron w aplikacji:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs:*

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

Poproś przykładową stronę Informacje `localhost:5000/About` o i sprawdź nagłówki, aby wyświetlić wynik:

![Nagłówki odpowiedzi na stronie Informacje pokazują, że globalheader został dodany.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>Dodawanie konwencji modelu programu obsługi do wszystkich stron

Służy <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> do tworzenia <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> i dodawania <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> do kolekcji wystąpień, które są stosowane podczas budowy modelu programu obsługi strony.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs:*

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>Konwencje akcji marszruty strony

Domyślny dostawca modelu trasy, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> który wywodzi się z konwencji wywoływania, które są przeznaczone do zapewnienia punktów rozszerzalności do konfigurowania tras stron.

### <a name="folder-route-model-convention"></a>Konwencja modelu trasy folderu

Służy <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> do tworzenia <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> i dodawania akcji, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> która wywołuje akcję na wszystkich stronach w określonym folderze.

Przykładowa aplikacja <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> służy do `{otherPagesTemplate?}` dodawania szablonu trasy do stron w folderze *OtherPages:*

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

Właściwość <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> dla <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> jest `2`ustawiona na . Gwarantuje to, że `{globalTemplate?}` szablon (ustawiony wcześniej `1`w temacie do) ma pierwszeństwo dla pierwszej pozycji wartości danych trasy, gdy podano pojedynczą wartość trasy. Jeśli strona w folderze *Pages/OtherPages* jest żądana z wartością parametru trasy (na `/OtherPages/Page1/RouteDataValue`przykład), `RouteData.Values["globalTemplate"]` `Order = 1`"RouteDataValue" jest ładowany do ( ) i nie `RouteData.Values["otherPagesTemplate"]` (`Order = 2`) z powodu ustawienia `Order` właściwości.

Tam, gdzie to możliwe, nie `Order` `Order = 0`ustawiaj , co powoduje. Polegaj na routingu, aby wybrać właściwą trasę.

Poproś o przykładową stronę `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` Page1 i sprawdź wynik:

![Strona1 w folderze OtherPages jest żądana z segmentem trasy GlobalRouteValue i OtherPagesRouteValue. Renderowana strona pokazuje, że wartości danych trasy są przechwytywane w OnGet metody strony.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>Konwencja modelu trasy strony

Służy <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> do tworzenia <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> i dodawania akcji, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> która wywołuje akcję na stronie o określonej nazwie.

Przykładowa aplikacja `AddPageRouteModelConvention` służy do `{aboutTemplate?}` dodawania szablonu trasy do strony Informacje:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

Właściwość <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> dla <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> jest `2`ustawiona na . Gwarantuje to, że `{globalTemplate?}` szablon (ustawiony wcześniej `1`w temacie do) ma pierwszeństwo dla pierwszej pozycji wartości danych trasy, gdy podano pojedynczą wartość trasy. Jeśli strona Informacje jest żądana z `/About/RouteDataValue`wartością parametru trasy w , `RouteData.Values["globalTemplate"]` "RouteDataValue" jest ładowany do (`Order = 1`) i nie `RouteData.Values["aboutTemplate"]` (`Order = 2`) z powodu ustawienia `Order` właściwości.

Tam, gdzie to możliwe, nie `Order` `Order = 0`ustawiaj , co powoduje. Polegaj na routingu, aby wybrać właściwą trasę.

Poproś przykładową stronę Informacje `localhost:5000/About/GlobalRouteValue/AboutRouteValue` o i sprawdź wynik:

![Strona Informacje jest żądana z segmentami trasy dla GlobalRouteValue i AboutRouteValue. Renderowana strona pokazuje, że wartości danych trasy są przechwytywane w OnGet metody strony.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="configure-a-page-route"></a>Konfigurowanie trasy strony

Służy <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> do konfigurowania trasy do strony w określonej ścieżce strony. Wygenerowane łącza do strony używają określonej trasy. `AddPageRoute`do `AddPageRouteModelConvention` ustalenia trasy.

Przykładowa aplikacja tworzy `/TheContactPage` trasę do *contact.cshtml:*

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

Do strony Kontakt można `/Contact` również uzyskać pod adresem za pomocą jej domyślnej trasy.

Niestandardowa trasa aplikacji przykładowej do strony Kontakt `text` umożliwia`{text?}`opcjonalny segment trasy ( ). Strona zawiera również ten opcjonalny segment w swojej `@page` dyrektywie `/Contact` w przypadku, gdy użytkownik uzyskuje dostęp do strony na swojej trasie:

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

Należy zauważyć, że adres URL wygenerowany dla łącza **Kontakt** na renderowanej stronie odzwierciedla zaktualizowaną trasę:

![Przykładowe łącze kontaktowe aplikacji na pasku nawigacyjnym](razor-pages-conventions/_static/contact-link.png)

![Sprawdzenie łącza Kontakt w renderowanym kodzie HTML wskazuje, że href jest ustawiony na "/TheContactPage"](razor-pages-conventions/_static/contact-link-source.png)

Odwiedź stronę Kontakt przy zwykłej `/Contact`trasie lub trasie `/TheContactPage`niestandardowej . Jeśli podasz `text` dodatkowy segment trasy, strona pokazuje segment zakodowany w formacie HTML, który podajesz:

![Przykład przeglądarki krawędzi dostarczania opcjonalnego segmentu trasy "tekst" "TextValue" w adresie URL. Renderowana strona pokazuje wartość segmentu "tekst".](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>Konwencje akcji modelu strony

Domyślny dostawca modelu strony, który implementuje <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> wywoływania konwencji, które są przeznaczone do zapewnienia punktów rozszerzalności do konfigurowania modeli stron. Konwencje te są przydatne podczas tworzenia i modyfikowania scenariuszy odnajdowania i przetwarzania strony.

W przykładach w tej sekcji przykładowa `AddHeaderAttribute` aplikacja używa klasy, która jest <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, która stosuje nagłówek odpowiedzi:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

Korzystając z konwencji, w przykładzie pokazano, jak zastosować atrybut do wszystkich stron w folderze i do jednej strony.

**Konwencja modelu aplikacji folderu**

Służy <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> do tworzenia <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> i dodawania akcji <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> wywołującej akcję dla wystąpień dla wszystkich stron w określonym folderze.

Przykład pokazuje użycie `AddFolderApplicationModelConvention` przez dodanie nagłówka, `OtherPagesHeader`do stron wewnątrz *folderu OtherPages* aplikacji:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

Poproś przykładową stronę Page1 `localhost:5000/OtherPages/Page1` i sprawdź nagłówki, aby wyświetlić wynik:

![Nagłówki odpowiedzi strony OtherPages/Page1 pokazują, że dodano otherpagesheader.](razor-pages-conventions/_static/page1-otherpages-header.png)

**Konwencja modelu aplikacji strony**

Służy <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> do tworzenia <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> i dodawania akcji, <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> która wywołuje akcję na stronie o określonej nazwie.

Przykład pokazuje użycie `AddPageApplicationModelConvention` przez dodanie nagłówka, `AboutHeader`do Informacje strony:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

Poproś przykładową stronę Informacje `localhost:5000/About` o i sprawdź nagłówki, aby wyświetlić wynik:

![Nagłówki odpowiedzi na stronie Informacje pokazują, że aboutheader został dodany.](razor-pages-conventions/_static/about-page-about-header.png)

**Konfigurowanie filtru**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>konfiguruje określony filtr do zastosowania. Można zaimplementować klasę filtru, ale przykładowa aplikacja pokazuje, jak zaimplementować filtr w wyrażeniu lambda, który jest implementowany za kulisami jako fabryka, która zwraca filtr:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

Model aplikacji strony służy do sprawdzania ścieżki względnej dla segmentów prowadzących do strony Page2 w folderze *OtherPages.* Jeśli warunek przejdzie, zostanie dodany nagłówek. Jeśli nie, `EmptyFilter` jest stosowany.

`EmptyFilter`jest [filtrem akcji](xref:mvc/controllers/filters#action-filters). Ponieważ filtry akcji są ignorowane przez `EmptyFilter` strony Razor Pages, nie ma `OtherPages/Page2`wpływu zgodnie z przeznaczeniem, jeśli ścieżka nie zawiera .

Poproś przykładową stronę Page2 `localhost:5000/OtherPages/Page2` o pozycję Page2 i sprawdź nagłówki, aby wyświetlić wynik:

![OtherPagesPage2Header zostanie dodany do odpowiedzi dla Page2.](razor-pages-conventions/_static/page2-filter-header.png)

**Konfigurowanie fabryki filtrów**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*>konfiguruje określoną fabrykę, aby zastosować [filtry](xref:mvc/controllers/filters) do wszystkich stron Razor.

Przykładowa aplikacja zawiera przykład użycia [fabryki filtrów](xref:mvc/controllers/filters#ifilterfactory) `FilterFactoryHeader`przez dodanie nagłówka, z dwiema wartościami do stron aplikacji:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs:*

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

Poproś przykładową stronę Informacje `localhost:5000/About` o i sprawdź nagłówki, aby wyświetlić wynik:

![Nagłówki odpowiedzi na stronie Informacje pokazują, że dodano dwa nagłówki FilterFactoryHeader.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>Filtry MVC i filtr strony (IPageFilter)

[Filtry akcji](xref:mvc/controllers/filters#action-filters) MVC są ignorowane przez strony Razor Pages, ponieważ razor Pages używają metod obsługi. Dostępne są inne typy filtrów MVC: [Autoryzacja,](xref:mvc/controllers/filters#authorization-filters) [Wyjątek,](xref:mvc/controllers/filters#exception-filters) [Zasób](xref:mvc/controllers/filters#resource-filters)i [Wynik](xref:mvc/controllers/filters#result-filters). Aby uzyskać więcej informacji, zobacz temat [Filtry.](xref:mvc/controllers/filters)

Filtr Strony<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>( ) to filtr, który ma zastosowanie do stron Razor. Aby uzyskać więcej informacji, zobacz [Metody filtrowania stron razor](xref:razor-pages/filter).

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end
