---
title: Migrowanie z interfejsu API sieci Web ASP.NET do ASP.NET Core
author: ardalis
description: Dowiedz się, jak migrować implementację internetowego interfejsu API z ASP.NET 4. x sieci Web API do ASP.NET Core MVC.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/webapi
ms.openlocfilehash: dda457daa0cb8a59ccd4c326a601e375fe4a81bb
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82766592"
---
# <a name="migrate-from-aspnet-web-api-to-aspnet-core"></a>Migrowanie z interfejsu API sieci Web ASP.NET do ASP.NET Core

Przez [Scott Addie](https://twitter.com/scott_addie) i [Steve Smith](https://ardalis.com/)

Internetowy interfejs API ASP.NET 4. x to usługa HTTP, która dociera do szerokiego zakresu klientów, w tym przeglądarek i urządzeń przenośnych. ASP.NET Core modele aplikacji MVC i Web API łączy ASP.NET 4. x w prostszy model programowania znany jako ASP.NET Core MVC. W tym artykule przedstawiono kroki wymagane do przeprowadzenia migracji z interfejsu API sieci Web ASP.NET 4. x do ASP.NET Core MVC.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([jak pobrać](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs2019-2.2.md)]

## <a name="review-aspnet-4x-web-api-project"></a>Przegląd projektu interfejsu API sieci Web ASP.NET 4. x

Jako punkt początkowy, w tym artykule używany jest projekt *ProductsApp* utworzony w [wprowadzenie z ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api). W tym projekcie jest skonfigurowany prosty projekt interfejsu API sieci Web ASP.NET 4. x w następujący sposób.

W *Global.asax.cs*następuje wywołanie `WebApiConfig.Register`:

[!code-csharp[](webapi/sample/ProductsApp/Global.asax.cs?highlight=14)]

Klasa znajduje się w folderze *App_Start* i ma metodę statyczną `Register` `WebApiConfig`

[!code-csharp[](webapi/sample/ProductsApp/App_Start/WebApiConfig.cs)]

Ta klasa konfiguruje [Routing atrybutów](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), chociaż nie jest faktycznie używana w projekcie. Konfiguruje również tabelę routingu, która jest używana przez interfejs API sieci Web ASP.NET. W takim przypadku interfejs API sieci Web ASP.NET 4. x oczekuje, że adresy URL są `/api/{controller}/{id}`zgodne z `{id}` formatem i są opcjonalne.

Projekt *ProductsApp* zawiera jeden kontroler. Kontroler dziedziczy z `ApiController` i zawiera dwie akcje:

[!code-csharp[](webapi/sample/ProductsApp/Controllers/ProductsController.cs?highlight=28,33)]

`Product` Model używany przez `ProductsController` jest prostą klasą:

[!code-csharp[](webapi/sample/ProductsApp/Models/Product.cs)]

W poniższych sekcjach przedstawiono migrację projektu interfejsu API sieci Web do ASP.NET Core MVC.

## <a name="create-destination-project"></a>Utwórz projekt docelowy

Wykonaj następujące kroki w programie Visual Studio:

* Przejdź do pozycji **plik** > **Nowy** > **projekt** > **Inne typy** > projektów**Visual Studio**. Wybierz pozycję **puste rozwiązanie**i nazwij rozwiązanie *WebAPIMigration*. Kliknij przycisk **OK** .
* Dodaj istniejący projekt *ProductsApp* do rozwiązania.
* Dodaj nowy projekt **aplikacji sieci Web ASP.NET Core** do rozwiązania. Wybierz platformę docelową **platformy .NET Core** z listy rozwijanej i wybierz szablon projektu **interfejsu API** . Nazwij projekt *ProductsCore*, a następnie kliknij przycisk **OK** .

Rozwiązanie zawiera teraz dwa projekty. W poniższych sekcjach opisano Migrowanie zawartości projektu *ProductsApp* do projektu *ProductsCore* .

## <a name="migrate-configuration"></a>Migruj konfigurację

ASP.NET Core nie używa folderu *App_Start* ani pliku *Global. asax* , a plik *Web. config* jest dodawany w czasie publikacji. *Startup.cs* jest zamiennikiem elementu *Global. asax* i znajduje się w katalogu głównym projektu. `Startup` Klasa obsługuje wszystkie zadania uruchamiania aplikacji. Aby uzyskać więcej informacji, zobacz <xref:fundamentals/startup>.

W ASP.NET Core MVC, routing atrybutu jest uwzględniany domyślnie, <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> gdy jest wywoływana `Startup.Configure`w. Następujące `UseMvc` wywołanie zastępuje plik */Webapiconfig.cs App_Start* projektu *ProductsApp* :

[!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_Configure&highlight=13])]

## <a name="migrate-models-and-controllers"></a>Migrowanie modeli i kontrolerów

Kopiuj przez kontroler projektu *ProductApp* oraz model, którego używa. Wykonaj następujące kroki:

1. Skopiuj *Kontrolery/ProductsController. cs* z oryginalnego projektu do nowego.
1. Skopiuj cały folder *modele* z oryginalnego projektu do nowego.
1. Zmień przestrzenie nazw skopiowane pliki na pasujące do nowej nazwy projektu (*ProductsCore*). Dostosuj `using ProductsApp.Models;` instrukcję w *ProductsController.cs* .

W tym momencie Kompilowanie aplikacji powoduje szereg błędów kompilacji. Błędy występują, ponieważ następujące składniki nie istnieją w ASP.NET Core:

* Klasa `ApiController`
* `System.Web.Http`obszaru
* `IHttpActionResult`interfejsu

Usuń błędy w następujący sposób:

1. Zmień `ApiController` na <xref:Microsoft.AspNetCore.Mvc.ControllerBase>. Dodaj `using Microsoft.AspNetCore.Mvc;` , `ControllerBase` aby rozwiązać odwołanie.
1. Usuń folder `using System.Web.Http;`.
1. Zmień typ `GetProduct` zwracany akcji z `IHttpActionResult` na `ActionResult<Product>`.

Uprość `GetProduct` `return` instrukcję akcji do następujących:

```csharp
return product;
```

## <a name="configure-routing"></a>Configure routing (Konfigurowanie routingu)

Skonfiguruj Routing w następujący sposób:

1. Oznacz `ProductsController` klasę następującymi atrybutami:

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    Poprzedni [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) atrybut konfiguruje wzorzec routingu atrybutu kontrolera. Ten [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atrybut powoduje, że atrybut routingu wymaga dla wszystkich akcji w tym kontrolerze.

    Routing atrybutów obsługuje tokeny, takie `[controller]` jak `[action]`i. W czasie wykonywania każdy token jest zastępowany odpowiednio nazwą kontrolera lub akcji, do którego zastosowano atrybut. Tokeny zmniejszają liczbę ciągów magicznych w projekcie. Tokeny zapewniają również, że trasy pozostają zsynchronizowane z odpowiednimi kontrolerami i akcjami, gdy stosowane są automatyczne refaktoryzacje zmiany nazwy.
1. Ustaw tryb zgodności projektu na ASP.NET Core 2,2:

    [!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    Poprzednia zmiana:

    * Jest wymagany do użycia `[ApiController]` atrybutu na poziomie kontrolera.
    * Umożliwia potencjalne uszkodzenie zachowań wprowadzonych w ASP.NET Core 2,2.
1. Włącz żądania HTTP GET do `ProductController` akcji:
    * Zastosuj [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) atrybut do `GetAllProducts` akcji.
    * Zastosuj `[HttpGet("{id}")]` atrybut do `GetProduct` akcji.

Po powyższych zmianach i usunięciu nieużywanych `using` instrukcji plik *ProductsController.cs* wygląda następująco:

[!code-csharp[](webapi/sample/ProductsCore/Controllers/ProductsController.cs)]

Uruchom zmigrowany projekt i przejdź do `/api/products`. Zostanie wyświetlona pełna lista trzech produktów. Przejdź do `/api/products/1`. Zostanie wyświetlony pierwszy produkt.

## <a name="compatibility-shim"></a>Podkładka zgodności

Biblioteka [Microsoft. AspNetCore. MVC. WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) zawiera podkładkę zgodności do przenoszenia projektów interfejsu API sieci Web ASP.NET 4. x do ASP.NET Core. Podkładka zgodności rozszerza ASP.NET Core do obsługi wielu konwencji z ASP.NET 4. x Web API 2. Przykładowy port podany wcześniej w tym dokumencie jest wystarczająco mały, że podkładka zgodności była niepotrzebna. W przypadku większych projektów użycie podkładki zgodności może być przydatne do tymczasowego mostkowania przerwy między ASP.NET Core i ASP.NET 4. x Web API 2.

Podkładka zgodności dla interfejsu API sieci Web ma służyć jako tymczasowa miara do obsługi migrowania dużych ASP.NETych projektów interfejsu API sieci Web do ASP.NET Core. W miarę upływu czasu projekty należy zaktualizować tak, aby korzystały z wzorców ASP.NET Core zamiast polegać na podkładki zgodności.

Funkcje zgodności zawarte w `Microsoft.AspNetCore.Mvc.WebApiCompatShim` programie obejmują:

* Dodaje `ApiController` typ, aby nie trzeba było aktualizować typów podstawowych kontrolerów.
* Włącza powiązanie modelu internetowego interfejsu API. ASP.NET Core funkcji powiązania modelu MVC podobnie jak w przypadku ASP.NET 4. x MVC 5, domyślnie. W ramach podkładki zgodności zmiany powiązania modelu są bardziej podobne do Konwencji powiązań modelu ASP.NET 4. x sieci Web. Na przykład typy złożone są automatycznie powiązane z treścią żądania.
* Rozszerza powiązanie modelu, aby akcje kontrolera mogły przyjmować parametry typu `HttpRequestMessage`.
* Dodaje elementy formatujące komunikaty umożliwiające działanie zwracające wyniki typu `HttpResponseMessage`.
* Dodaje dodatkowe metody odpowiedzi, które mogą być używane przez działania Web API 2 do obsłużenia odpowiedzi:
  * `HttpResponseMessage`wytwornic
    * `CreateResponse<T>`
    * `CreateErrorResponse`
  * Metody wyniku akcji:
    * `BadRequestErrorMessageResult`
    * `ExceptionResult`
    * `InternalServerErrorResult`
    * `InvalidModelStateResult`
    * `NegotiatedContentResult`
    * `ResponseMessageResult`
* Dodaje wystąpienie `IContentNegotiator` do kontenera iniekcji zależności aplikacji i udostępnia typy powiązane z negocjowaniem zawartości z [Microsoft. ASPNET. WebApi. Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/). Przykłady takich typów obejmują `DefaultContentNegotiator` i. `MediaTypeFormatter`

Aby użyć podkładek zgodności:

1. Zainstaluj pakiet NuGet [Microsoft. AspNetCore. MVC. WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) .
1. Zarejestruj usługi podkładki zgodności z kontenerem DI aplikacji, wywołując `services.AddMvc().AddWebApiConventions()` w. `Startup.ConfigureServices`
1. Zdefiniuj trasy specyficzne dla interfejsu API sieci `MapWebApiRoute` Web za `IRouteBuilder` pomocą polecenia w `IApplicationBuilder.UseMvc` wywołaniu aplikacji.

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>
