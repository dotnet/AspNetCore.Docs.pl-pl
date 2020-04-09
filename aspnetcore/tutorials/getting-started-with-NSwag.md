---
title: Wprowadzenie do NSwag i ASP.NET Core
author: zuckerthoben
description: Dowiedz się, jak używać NSwag do generowania dokumentacji i stron pomocy dla ASP.NET core internetowego interfejsu API.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
uid: tutorials/get-started-with-nswag
ms.openlocfilehash: 3eae5d3c66204a10806a8036c8f114af6c501b2c
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666055"
---
# <a name="get-started-with-nswag-and-aspnet-core"></a>Wprowadzenie do NSwag i ASP.NET Core

[Christoph Nienaber](https://twitter.com/zuckerthoben), [Rico Suter](https://rsuter.com)i [Dave Brock](https://twitter.com/daveabrock)

::: moniker range=">= aspnetcore-2.1"

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag) ([jak pobrać](xref:index#how-to-download-a-sample))

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag) ([jak pobrać](xref:index#how-to-download-a-sample))

::: moniker-end

NSwag oferuje następujące możliwości:

* Możliwość wykorzystania Swagger UI i generator Swagger.
* Elastyczne możliwości generowania kodu.

Z NSwag, nie trzeba istniejącego&mdash;interfejsu API można użyć interfejsów API innych firm, które zawierają Swagger i generowania implementacji klienta. NSwag pozwala przyspieszyć cykl rozwoju i łatwo dostosować się do zmian API.

## <a name="register-the-nswag-middleware"></a>Zarejestruj oprogramowanie pośredniczące NSwag

Zarejestruj oprogramowanie pośredniczące NSwag w:

* Wygeneruj specyfikację Swagger dla zaimplementowanego interfejsu API sieci web.
* Służy interfejsu użytkownika Swagger do przeglądania i testowania interfejsu API sieci web.

Aby użyć oprogramowania pośredniczącego [NSwag](https://github.com/RicoSuter/NSwag) ASP.NET Core, zainstaluj pakiet [NSwag.AspNetCore](https://www.nuget.org/packages/NSwag.AspNetCore/) NuGet. Ten pakiet zawiera oprogramowanie pośredniczące do generowania i obsługi specyfikacji Swagger, Swagger UI (v2 i v3) i [Interfejsu Użytkownika ReDoc](https://github.com/Rebilly/ReDoc).

Użyj jednego z następujących podejść, aby zainstalować pakiet NSwag NuGet:

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* W oknie **Konsoli Menedżera pakietów:**
  * Przejdź do **zobacz** > inną**konsolę Menedżera pakietów** **systemu Windows** > 
  * Przejdź do katalogu, w którym istnieje plik *TodoApi.csproj*
  * Wykonaj następujące polecenie:

    ```powershell
    Install-Package NSwag.AspNetCore
    ```

* W oknie dialogowym **Zarządzanie pakietami NuGet:**
  * Kliknij prawym przyciskiem myszy projekt w **Eksploratorze** > rozwiązań**Zarządzaj pakietami NuGet**
  * Ustaw **źródło pakietu** na "nuget.org"
  * Wpisz "NSwag.AspNetCore" w polu wyszukiwania
  * Wybierz pakiet "NSwag.AspNetCore" z zakładki **Przeglądaj** i kliknij przycisk **Zainstaluj**

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

* Kliknij prawym przyciskiem myszy folder *Pakiety* w **panelu rozrachowy** > **Dodaj pakiety...**
* Ustaw okno **Dodawanie pakietów** **w oknie Źródło** listy rozwijanej na "nuget.org"
* Wpisz "NSwag.AspNetCore" w polu wyszukiwania
* Wybierz pakiet "NSwag.AspNetCore" z okienka wyników i kliknij przycisk **Dodaj pakiet**

# <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/netcore-cli)

Uruchom następujące polecenie:

```dotnetcli
dotnet add TodoApi.csproj package NSwag.AspNetCore
```

---

## <a name="add-and-configure-swagger-middleware"></a>Dodawanie i konfigurowanie oprogramowania pośredniczącego Swagger

Dodaj i skonfiguruj Swagger w aplikacji ASP.NET Core, wykonując następujące czynności:

* W `Startup.ConfigureServices` metodzie zarejestruj wymagane usługi Swagger:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

* W `Startup.Configure` metodzie włącz oprogramowanie pośredniczące do obsługi wygenerowanej specyfikacji Swagger i interfejsu użytkownika Swagger:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_Configure&highlight=6-7)]

* Uruchomić aplikację. Przejdź do:
  * `http://localhost:<port>/swagger`, aby wyświetlić interfejs użytkownika swaggera.
  * `http://localhost:<port>/swagger/v1/swagger.json`, aby wyświetlić specyfikację Swagger.

## <a name="code-generation"></a>Generowanie kodu

Możesz skorzystać z możliwości generowania kodu NSwag, wybierając jedną z następujących opcji:

* [NSwagStudio](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio) &ndash; aplikacji klasycznej systemu Windows do generowania kodu klienta interfejsu API w języku C# lub TypeScript.
* [Pakiety NSwag.CodeGeneration.CSharp](https://www.nuget.org/packages/NSwag.CodeGeneration.CSharp/) lub [NSwag.CodeGeneration.TypeScript](https://www.nuget.org/packages/NSwag.CodeGeneration.TypeScript/) NuGet dla generowania kodu wewnątrz projektu.
* NSwag z [wiersza polecenia](https://github.com/RicoSuter/NSwag/wiki/CommandLine).
* Pakiet [NSwag.MSBuild](https://github.com/RicoSuter/NSwag/wiki/NSwag.MSBuild) NuGet.
* [Odszypuł openapi (Swagger) Connected Service](https://marketplace.visualstudio.com/items?itemName=Unchase.unchaseopenapiconnectedservice) &ndash; visual studio connected service do generowania kodu klienta interfejsu API w języku C# lub TypeScript. Generuje również kontrolery języka C# dla usług OpenAPI z NSwag.

### <a name="generate-code-with-nswagstudio"></a>Generowanie kodu za pomocą NSwagStudio

* Zainstaluj NSwagStudio, postępując zgodnie z instrukcjami zawartymi w [repozytorium NSwagStudio GitHub](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio). Na stronie wydania NSwag można pobrać wersję xcopy, którą można uruchomić bez uprawnień instalacyjnych i administracyjnych.
* Uruchom NSwagStudio i wprowadź adres URL pliku *swagger.json* w polu tekstowym **URL specyfikacji Swaggera.** Na przykład *http://localhost:44354/swagger/v1/swagger.json*.
* Kliknij przycisk **Utwórz kopię lokalną,** aby wygenerować reprezentację JSON specyfikacji Swagger.

  ![Tworzenie lokalnej kopii specyfikacji Swagger](web-api-help-pages-using-swagger/_static/CreateLocalCopy-NSwagStudio.PNG)

* W obszarze **Dane wyjściowe** kliknij pole wyboru **CSharp Client (Klient CSharp).** W zależności od projektu można również wybrać **klienta TypeScript** lub **kontrolera interfejsu API sieci Web CSharp**. W przypadku **wybrania cSharp Web API Controller**specyfikacja usługi odbudowuje usługę, służąc jako odwrotnej generacji.
* Kliknij **przycisk Generuj dane wyjściowe,** aby utworzyć pełną implementację klienta języka C# projektu *TodoApi.NSwag.* Aby wyświetlić wygenerowany kod klienta, kliknij kartę **CSharp Client:**

```csharp
//----------------------
// <auto-generated>
//     Generated using the NSwag toolchain v12.0.9.0 (NJsonSchema v9.13.10.0 (Newtonsoft.Json v11.0.0.0)) (http://NSwag.org)
// </auto-generated>
//----------------------

namespace MyNamespace
{
    #pragma warning disable

    [System.CodeDom.Compiler.GeneratedCode("NSwag", "12.0.9.0 (NJsonSchema v9.13.10.0 (Newtonsoft.Json v11.0.0.0))")]
    public partial class TodoClient
    {
        private string _baseUrl = "https://localhost:44354";
        private System.Net.Http.HttpClient _httpClient;
        private System.Lazy<Newtonsoft.Json.JsonSerializerSettings> _settings;

        public TodoClient(System.Net.Http.HttpClient httpClient)
        {
            _httpClient = httpClient;
            _settings = new System.Lazy<Newtonsoft.Json.JsonSerializerSettings>(() =>
            {
                var settings = new Newtonsoft.Json.JsonSerializerSettings();
                UpdateJsonSerializerSettings(settings);
                return settings;
            });
        }

        public string BaseUrl
        {
            get { return _baseUrl; }
            set { _baseUrl = value; }
        }

        // code omitted for brevity
```

> [!TIP]
> Kod klienta języka C# jest generowany na podstawie wyborów na karcie **Ustawienia.**

* Skopiuj wygenerowany kod Języka C# do pliku w projekcie klienta, który będzie zużywał interfejs API.
* Zacznij ciemiężyć internetowy interfejs API:

```csharp
 var todoClient = new TodoClient();

// Gets all to-dos from the API
 var allTodos = await todoClient.GetAllAsync();

 // Create a new TodoItem, and save it via the API.
var createdTodo = await todoClient.CreateAsync(new TodoItem());

// Get a single to-do by ID
var foundTodo = await todoClient.GetByIdAsync(1);
```

## <a name="customize-api-documentation"></a>Dostosowywanie dokumentacji interfejsu API

Swagger udostępnia opcje dokumentowania modelu obiektów w celu ułatwienia zużycia internetowego interfejsu API.

### <a name="api-info-and-description"></a>Informacje i opis interfejsu API

W `Startup.ConfigureServices` metodzie akcja konfiguracji przekazana `AddSwaggerDocument` do metody dodaje informacje, takie jak autor, licencja i opis:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup2.cs?name=snippet_AddSwaggerDocument)]

Interfejs użytkownika Swagger wyświetla informacje o wersji:

![Interfejs użytkownika Swagger z informacjami o wersji](web-api-help-pages-using-swagger/_static/custom-info-nswag.png)

### <a name="xml-comments"></a>komentarze XML

Aby włączyć komentarze XML, wykonaj następujące czynności:

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* Kliknij prawym przyciskiem myszy projekt w **Eksploratorze rozwiązań** i wybierz polecenie **Edytuj <project_name>.csproj**.
* Ręcznie dodaj wyróżnione wiersze do pliku *csproj:*

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* Kliknij prawym przyciskiem myszy projekt w **Eksploratorze rozwiązań** i wybierz **polecenie Właściwości**
* Zaznacz pole **pliku dokumentacji XML** w sekcji **Dane wyjściowe** na karcie **Kompilacja**

::: moniker-end

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* W *panelu rozrachówek*naciśnij **kontrolkę** i kliknij nazwę projektu. Przejdź do **pozycji Narzędzia** > **edycji pliku**.
* Ręcznie dodaj wyróżnione wiersze do pliku *csproj:*

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* Otwórz okno dialogowe **Opcje projektu** > **kompilator** **kompilacji** >
* Zaznacz pole **Wyboru Generuj dokumentację xml** w sekcji **Opcje ogólne**

::: moniker-end

# <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/netcore-cli)

Ręcznie dodaj wyróżnione wiersze do pliku *csproj:*

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

---

### <a name="data-annotations"></a>Adnotacje danych

::: moniker range="<= aspnetcore-2.0"

Ponieważ NSwag używa [odbicia](/dotnet/csharp/programming-guide/concepts/reflection), a zalecanym typem zwracania dla akcji interfejsu API sieci web jest [IActionResult](xref:Microsoft.AspNetCore.Mvc.IActionResult), nie można wywnioskować, co robi akcja i co zwraca.

Rozważmy następujący przykład:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

Poprzednia akcja `IActionResult`zwraca , ale wewnątrz akcji jest zwracany albo [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*) lub [BadRequest](xref:System.Web.Http.ApiController.BadRequest*). Adnotacje danych za pomocą adnotacji danych informują klientów, które kody stanu HTTP są znane jako zwracane przez tę akcję. Oznacz akcję następującymi atrybutami:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

Ponieważ NSwag używa [odbicia,](/dotnet/csharp/programming-guide/concepts/reflection)a zalecanym typem zwracanym dla akcji web API jest [ActionResult\<T>, ](xref:Microsoft.AspNetCore.Mvc.ActionResult%601)można wywnioskować tylko typ zwracany zdefiniowany przez `T`program . Nie można automatycznie wywnioskować innych możliwych typów zwrotów.

Rozważmy następujący przykład:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

Poprzednia akcja `ActionResult<T>`zwraca . Wewnątrz akcji jest powrót [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*). Ponieważ kontroler ma [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atrybut, [BadRequest](xref:System.Web.Http.ApiController.BadRequest*) odpowiedzi jest możliwe, zbyt. Aby uzyskać więcej informacji, zobacz [Automatyczne odpowiedzi HTTP 400](xref:web-api/index#automatic-http-400-responses). Adnotacje danych za pomocą adnotacji danych informują klientów, które kody stanu HTTP są znane jako zwracane przez tę akcję. Oznacz akcję następującymi atrybutami:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

W ASP.NET Core 2.2 lub nowszym można używać konwencji zamiast jawnie `[ProducesResponseType]`dekorować poszczególne akcje za pomocą programu . Aby uzyskać więcej informacji, zobacz <xref:web-api/advanced/conventions>.

::: moniker-end

Generator Swagger może teraz dokładnie opisać tę akcję i wygenerowanych klientów wiedzieć, co otrzymują podczas wywoływania punktu końcowego. Jako zalecenie zaznacz wszystkie akcje tymi atrybutami.

Aby uzyskać wskazówki dotyczące odpowiedzi HTTP, które akcje interfejsu API powinny zwracać, zobacz [specyfikację RFC 7231](https://tools.ietf.org/html/rfc7231#section-4.3).
