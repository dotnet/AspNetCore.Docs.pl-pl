---
title: Pracuj z modelem aplikacji w ASP.NET Core
author: ardalis
description: Dowiedz się, jak czytać i manipulować modelem aplikacji, aby modyfikować sposób zachowania elementów MVC w ASP.NET Core.
ms.author: riande
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/controllers/application-model
ms.openlocfilehash: 5e31d2e6611321bec7442534ce41350de10478e0
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768666"
---
# <a name="work-with-the-application-model-in-aspnet-core"></a>Pracuj z modelem aplikacji w ASP.NET Core

Przez [Steve Smith](https://ardalis.com/)

ASP.NET Core MVC definiuje *model aplikacji* reprezentujący składniki aplikacji MVC. Można odczytywać i manipulować tym modelem, aby modyfikować sposób zachowania elementów MVC. Domyślnie MVC stosuje pewne konwencje, aby określić, które klasy są uważane za kontrolery, które metody dla tych klas są akcjami oraz jak działają parametry i Routing. Takie zachowanie można dostosować do potrzeb aplikacji, tworząc własne konwencje i stosując je globalnie lub jako atrybuty.

## <a name="models-and-providers"></a>Modele i dostawcy

Model aplikacji ASP.NET Core MVC obejmuje zarówno interfejsy abstrakcyjne, jak i klasy konkretnych implementacji, które opisują aplikację MVC. Ten model jest wynikiem odnajdywania kontrolerów aplikacji, akcji, parametrów akcji, tras i filtrów zgodnie z konwencjami domyślnymi. Pracując z modelem aplikacji, można zmodyfikować aplikację, tak aby była zgodna z innymi konwencjami z domyślnego zachowania MVC. Parametry, nazwy, trasy i filtry są używane jako dane konfiguracji dla akcji i kontrolerów.

Model aplikacji ASP.NET Core MVC ma następującą strukturę:

* ApplicationModel
  * Kontrolery (ControllerModel)
    * Akcje (ActionModel)
      * Parametry (ParameterModel)

Każdy poziom modelu ma dostęp do typowej `Properties` kolekcji, a niższe poziomy mogą uzyskać dostęp do wartości właściwości ustawionych przez wyższe poziomy w hierarchii. Właściwości są utrwalane `ActionDescriptor.Properties` podczas tworzenia akcji. Następnie, gdy żądanie jest obsługiwane, można uzyskać dostęp do wszystkich właściwości dodanej lub zmodyfikowanej `ActionContext.ActionDescriptor.Properties`Konwencji. Korzystanie z właściwości to doskonały sposób konfigurowania filtrów, powiązań modelu itp. na podstawie akcji.

> [!NOTE]
> `ActionDescriptor.Properties` Kolekcja nie jest bezpieczna wątkowo (dla zapisów) po zakończeniu uruchamiania aplikacji. Konwencje są najlepszym sposobem bezpiecznego dodawania danych do tej kolekcji.

### <a name="iapplicationmodelprovider"></a>IApplicationModelProvider

ASP.NET Core MVC ładuje model aplikacji przy użyciu wzorca dostawcy zdefiniowanego przez interfejs [IApplicationModelProvider](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iapplicationmodelprovider) . W tej sekcji omówiono niektóre wewnętrzne szczegóły implementacji działania tego dostawcy. Jest to zaawansowany temat-większość aplikacji, które wykorzystują model aplikacji, należy to zrobić przez pracę z konwencjami.

Implementacje `IApplicationModelProvider` interfejsu "Otocz", przy czym każda implementacja wywołuje `OnProvidersExecuting` w kolejności rosnącej na podstawie jej `Order` właściwości. `OnProvidersExecuted` Metoda jest następnie wywoływana w odwrotnej kolejności. Struktura definiuje kilku dostawców:

First (`Order=-1000`):

* [`DefaultApplicationModelProvider`](/dotnet/api/microsoft.aspnetcore.mvc.internal.defaultapplicationmodelprovider)

Następnie (`Order=-990`):

* [`AuthorizationApplicationModelProvider`](/dotnet/api/microsoft.aspnetcore.mvc.internal.authorizationapplicationmodelprovider)
* [`CorsApplicationModelProvider`](/dotnet/api/microsoft.aspnetcore.mvc.cors.internal.corsapplicationmodelprovider)

> [!NOTE]
> Kolejność, w której `Order` są wywoływane dwa dostawcy o tej samej wartości, jest niezdefiniowana i dlatego nie należy jej opierać.

> [!NOTE]
> `IApplicationModelProvider`jest zaawansowaną koncepcją dla autorów struktury, która ma zostać rozszerzona. Ogólnie rzecz biorąc aplikacje powinny używać konwencji i struktur, powinny używać dostawców. Odrębność klucza polega na tym, że dostawcy zawsze są uruchamiani przed konwencjami.

`DefaultApplicationModelProvider` Tworzy wiele zachowań domyślnych używanych przez ASP.NET Core MVC. Jej obowiązki obejmują:

* Dodawanie filtrów globalnych do kontekstu
* Dodawanie kontrolerów do kontekstu
* Dodawanie metod kontrolera publicznego jako akcji
* Dodawanie parametrów metody akcji do kontekstu
* Stosowanie trasy i innych atrybutów

Niektóre wbudowane zachowania są implementowane przez `DefaultApplicationModelProvider`. Ten dostawca jest odpowiedzialny za konstruowanie [`ControllerModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.controllermodel), który z kolei powoduje [`ActionModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.actionmodel)odtworzenie [`PropertyModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.propertymodel)odwołań [`ParameterModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.parametermodel) , i wystąpień. `DefaultApplicationModelProvider` Klasa jest szczegółami wewnętrznej implementacji platformy, które mogą i zmienią się w przyszłości. 

`AuthorizationApplicationModelProvider` Jest odpowiedzialny za stosowanie zachowań skojarzonych z atrybutami `AuthorizeFilter` i `AllowAnonymousFilter` . [Dowiedz się więcej na temat tych atrybutów](xref:security/authorization/simple).

Zachowanie `CorsApplicationModelProvider` implementuje skojarzone z `IEnableCorsAttribute` i `IDisableCorsAttribute`i. `DisableCorsAuthorizationFilter` [Dowiedz się więcej na temat mechanizmu CORS](xref:security/cors).

## <a name="conventions"></a>Konwencje

Model aplikacji definiuje abstrakcje Konwencji, które zapewniają prostszy sposób dostosowywania zachowania modeli niż zastępowanie całego modelu lub dostawcy. Te streszczenia są zalecanym sposobem modyfikacji zachowania aplikacji. Konwencje umożliwiają pisanie kodu, który będzie dynamicznie stosować dostosowania. [Filtry](xref:mvc/controllers/filters) umożliwiają modyfikowanie zachowania struktury, jednak dostosowania pozwalają kontrolować, jak cała aplikacja działa razem.

Dostępne są następujące konwencje:

* [`IApplicationModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iapplicationmodelconvention)
* [`IControllerModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.icontrollermodelconvention)
* [`IActionModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iactionmodelconvention)
* [`IParameterModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iparametermodelconvention)

Konwencje są stosowane przez dodanie ich do opcji MVC lub przez `Attribute`zaimplementowanie i zastosowanie ich do kontrolerów, akcji lub parametrów akcji (podobnie [`Filters`](xref:mvc/controllers/filters)jak w przypadku). W przeciwieństwie do filtrów, konwencje są wykonywane tylko wtedy, gdy aplikacja jest uruchamiana, a nie jako część każdego żądania.

### <a name="sample-modifying-the-applicationmodel"></a>Przykład: modyfikowanie ApplicationModel

Poniższa Konwencja służy do dodawania właściwości do modelu aplikacji. 

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ApplicationDescription.cs)]

Konwencje modelu aplikacji są stosowane jako opcje po dodaniu MVC `ConfigureServices` w `Startup`programie w programie.

[!code-csharp[](./application-model/sample/src/AppModelSample/Startup.cs?name=ConfigureServices&highlight=5)]

Właściwości są dostępne z kolekcji `ActionDescriptor` właściwości w ramach akcji kontrolera:

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/AppModelController.cs?name=AppModelController)]

### <a name="sample-modifying-the-controllermodel-description"></a>Przykład: Modyfikowanie opisu ControllerModel

Podobnie jak w poprzednim przykładzie, model kontrolera można także zmodyfikować, aby uwzględnić właściwości niestandardowe. Zostaną one zastąpione istniejącymi właściwościami o tej samej nazwie określonej w modelu aplikacji. Następujący atrybut Konwencji dodaje opis na poziomie kontrolera:

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ControllerDescriptionAttribute.cs)]

Ta konwencja jest stosowana jako atrybut na kontrolerze.

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/DescriptionAttributesController.cs?name=ControllerDescription&highlight=1)]

Właściwość "Description" jest dostępna w taki sam sposób jak w poprzednich przykładach.

### <a name="sample-modifying-the-actionmodel-description"></a>Przykład: Modyfikowanie opisu ActionModel

Oddzielną Konwencję atrybutu można zastosować do poszczególnych akcji, zastępując zachowanie już stosowane na poziomie aplikacji lub kontrolera.

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ActionDescriptionAttribute.cs)]

Zastosowanie tego do akcji w ramach kontrolera poprzedniego przykładu pokazuje, jak zastępuje Konwencję poziomu kontrolera:

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/DescriptionAttributesController.cs?name=DescriptionAttributesController&highlight=9)]

### <a name="sample-modifying-the-parametermodel"></a>Przykład: modyfikowanie ParameterModel

Do parametrów akcji można zastosować następujące konwencje w celu zmodyfikowania `BindingInfo`ich. Poniższa Konwencja wymaga, aby parametr był parametrem trasy; inne potencjalne źródła powiązań (takie jak wartości ciągu zapytania) są ignorowane.

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/MustBeInRouteParameterModelConvention.cs)]

Ten atrybut może być stosowany do dowolnego parametru akcji:

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/ParameterModelController.cs?name=ParameterModelController&highlight=5)]

### <a name="sample-modifying-the-actionmodel-name"></a>Przykład: modyfikowanie nazwy ActionModel

Poniższa Konwencja modyfikuje `ActionModel` , aby zaktualizować *nazwę* akcji, do której zastosowano. Nowa nazwa jest podawana jako parametr do atrybutu. Ta nowa nazwa jest używana przez Routing, więc wpłynie ona na trasę używaną do osiągnięcia tej metody akcji.

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/CustomActionNameAttribute.cs)]

Ten atrybut jest stosowany do metody akcji w `HomeController`:

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/HomeController.cs?name=ActionModelConvention&highlight=2)]

Mimo że nazwa metody to `SomeName`, atrybut zastępuje Konwencję MVC o użyciu nazwy metody i zamienia nazwę akcji na. `MyCoolAction` W ten sposób trasa używana do osiągnięcia tej akcji wynosi `/Home/MyCoolAction`.

> [!NOTE]
> Ten przykład jest zasadniczo taki sam jak użycie wbudowanego atrybutu [ActionName](/dotnet/api/microsoft.aspnetcore.mvc.actionnameattribute) .

### <a name="sample-custom-routing-convention"></a>Przykład: niestandardowa Konwencja routingu

Można użyć, `IApplicationModelConvention` aby dostosować sposób działania routingu. Na przykład następująca Konwencja obejmuje obszary nazw kontrolerów, które zastępują `.` w przestrzeni nazw `/` w ramach trasy:

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/NamespaceRoutingConvention.cs)]

Konwencja jest dodawana jako opcja podczas uruchamiania.

[!code-csharp[](./application-model/sample/src/AppModelSample/Startup.cs?name=ConfigureServices&highlight=6)]

> [!TIP]
> Możesz dodawać konwencje do [oprogramowania pośredniczącego](xref:fundamentals/middleware/index) , uzyskując dostęp do `MvcOptions` użycia`services.Configure<MvcOptions>(c => c.Conventions.Add(YOURCONVENTION));`

Ten przykład dotyczy tras, które nie korzystają z routingu atrybutu, w którym kontroler ma nazwę "namespace". Następujący kontroler demonstruje tę Konwencję:

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/NamespaceRoutingController.cs?highlight=7-8)]

## <a name="application-model-usage-in-webapicompatshim"></a>Użycie modelu aplikacji w WebApiCompatShim

ASP.NET Core MVC używa różnych zestawów Konwencji z ASP.NET Web API 2. Przy użyciu konwencji niestandardowych można zmodyfikować zachowanie aplikacji ASP.NET Core MVC, aby była spójna z tą aplikacją interfejsu API sieci Web. Firma Microsoft dostarcza do tego celu [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim/) .

> [!NOTE]
> Dowiedz się więcej o [migracji z interfejsu API sieci Web ASP.NET](xref:migration/webapi).

Aby użyć podkładki zgodności z interfejsem API sieci Web, należy dodać pakiet do projektu, a następnie dodać konwencje do MVC, `AddWebApiConventions` wywołując `Startup`w:

```csharp
services.AddMvc().AddWebApiConventions();
```

Konwencje dostarczone przez podkładkę są stosowane tylko do części aplikacji, do których zastosowano określone atrybuty. Następujące cztery atrybuty są używane do kontrolowania kontrolerów, które powinny być modyfikowane przez konwencje podkładki:

* [UseWebApiActionConventionsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiactionconventionsattribute)
* [UseWebApiOverloadingAttribute](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapioverloadingattribute)
* [UseWebApiParameterConventionsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiparameterconventionsattribute)
* [UseWebApiRoutesAttribute](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiroutesattribute)

### <a name="action-conventions"></a>Konwencje akcji

`UseWebApiActionConventionsAttribute` Służy do mapowania metody http na akcje na podstawie ich nazwy (na przykład `Get` mapuje do `HttpGet`). Ma zastosowanie tylko do akcji, które nie używają routingu atrybutów.

### <a name="overloading"></a>Przeciążenie

`UseWebApiOverloadingAttribute` Służy do zastosowania `WebApiOverloadingApplicationModelConvention` Konwencji. Ta konwencja dodaje `OverloadActionConstraint` do procesu wyboru akcji, który ogranicza akcje kandydatów do tych, dla których żądanie spełnia wszystkie parametry Nieopcjonalne.

### <a name="parameter-conventions"></a>Konwencje parametrów

`UseWebApiParameterConventionsAttribute` Służy do zastosowania konwencji `WebApiParameterConventionsApplicationModelConvention` akcji. Ta Konwencja określa, że proste typy używane jako parametry akcji są powiązane z identyfikatorem URI domyślnie, natomiast typy złożone są powiązane z treści żądania.

### <a name="routes"></a>Trasy

Kontroluje `UseWebApiRoutesAttribute` , czy Konwencja `WebApiApplicationModelConvention` kontrolera jest stosowana. Po włączeniu ta konwencja jest używana do dodawania obsługi [obszarów](xref:mvc/controllers/areas) do trasy.

Oprócz zestawu Konwencji pakiet zgodności zawiera klasę `System.Web.Http.ApiController` bazową, która zastępuje ją dostarczoną przez internetowy interfejs API. Dzięki temu kontrolery napisane dla interfejsu API sieci Web i dziedziczą z `ApiController` programu, aby działały tak, jak zostały zaprojektowane, podczas uruchamiania na ASP.NET Core MVC. Wszystkie `UseWebApi*` atrybuty wymienione wcześniej są stosowane do klasy kontrolera podstawowego. `ApiController` Uwidacznia właściwości, metody i typy wyników, które są zgodne z tymi znajdującymi się w INTERNETowym interfejsie API.

## <a name="using-apiexplorer-to-document-your-app"></a>Dokumentowanie aplikacji przy użyciu ApiExplorer

Model aplikacji udostępnia [`ApiExplorer`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.apiexplorermodel) właściwość na każdym poziomie, która może służyć do przechodzenia przez strukturę aplikacji. Może to służyć do [generowania stron pomocy dla interfejsów API sieci Web przy użyciu narzędzi, takich jak Swagger](xref:tutorials/web-api-help-pages-using-swagger). `ApiExplorer` Właściwość uwidacznia `IsVisible` właściwość, którą można ustawić, aby określić, które części modelu aplikacji mają być uwidocznione. To ustawienie można skonfigurować przy użyciu konwencji:

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/EnableApiExplorerApplicationConvention.cs)]

Korzystając z tej metody (i dodatkowych Konwencji, jeśli jest to wymagane), możesz włączyć lub wyłączyć widoczność interfejsu API na dowolnym poziomie w aplikacji. 
