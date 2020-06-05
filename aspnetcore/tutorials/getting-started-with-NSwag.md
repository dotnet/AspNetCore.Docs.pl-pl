---
title: Wprowadzenie do NSwag i ASP.NET Core
author: zuckerthoben
description: Dowiedz się, jak używać NSwag do generowania dokumentacji i stron pomocy dla ASP.NET Core internetowego interfejsu API.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/get-started-with-nswag
ms.openlocfilehash: cd4cc6778de7d2156243dc91fba64b2bdb79cf08
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2020
ms.locfileid: "84452125"
---
# <a name="get-started-with-nswag-and-aspnet-core"></a>Wprowadzenie do NSwag i ASP.NET Core

[Christoph Nienaber](https://twitter.com/zuckerthoben), [Suter](https://rsuter.com)i [Dave Brock](https://twitter.com/daveabrock)

::: moniker range=">= aspnetcore-2.1"

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag) ([jak pobrać](xref:index#how-to-download-a-sample))

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag) ([jak pobrać](xref:index#how-to-download-a-sample))

::: moniker-end

NSwag oferuje następujące możliwości:

* Możliwość korzystania z interfejsu użytkownika programu Swagger i generatora Swagger.
* Elastyczne możliwości generowania kodu.

W przypadku korzystania z usługi NSwag nie jest potrzebny istniejący interfejs API, który &mdash; zawiera interfejsy API innych firm, które zawierają strukturę Swagger i generują implementację klienta. NSwag umożliwia przyspieszenie cyklu programowania i łatwe dostosowanie do zmian interfejsu API.

## <a name="register-the-nswag-middleware"></a>Rejestrowanie oprogramowania pośredniczącego NSwag

Zarejestruj oprogramowanie pośredniczące NSwag w programie:

* Generuj specyfikację Swagger dla zaimplementowanego interfejsu API sieci Web.
* Zaoferują interfejs użytkownika struktury Swagger, aby przeglądać i testować internetowy interfejs API.

Aby użyć oprogramowania [NSwag](https://github.com/RicoSuter/NSwag) ASP.NET Core pośredniczącego, zainstaluj pakiet NuGet [NSwag. AspNetCore](https://www.nuget.org/packages/NSwag.AspNetCore/) . Ten pakiet zawiera oprogramowanie pośredniczące do generowania i obsługiwania specyfikacji struktury Swagger, interfejsu użytkownika struktury Swagger (v2 i V3) oraz [interfejsu użytkownika ReDoc](https://github.com/Rebilly/ReDoc).

Aby zainstalować pakiet NuGet NSwag, należy użyć jednej z następujących metod:

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* W oknie **konsola Menedżera pakietów** :
  * Przejdź do **wyświetlania**  >  **innych**  >  **konsoli Menedżera pakietów** systemu Windows
  * Przejdź do katalogu, w którym znajduje się plik *TodoApi. csproj*
  * Wykonaj następujące polecenie:

    ```powershell
    Install-Package NSwag.AspNetCore
    ```

* W oknie dialogowym **Zarządzanie pakietami NuGet** :
  * Kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań**  >  **Zarządzanie pakietami NuGet**
  * Ustaw **Źródło pakietu** na "NuGet.org"
  * Wprowadź ciąg "NSwag. AspNetCore" w polu wyszukiwania
  * Wybierz pakiet "NSwag. AspNetCore" z karty **Przeglądaj** , a następnie kliknij przycisk **Instaluj** .

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

* Kliknij prawym przyciskiem myszy folder *pakiety* w **okienko rozwiązania**  >  **Dodaj pakiety...**
* Ustaw listę rozwijaną **źródła** okna **Dodaj pakiety** na "NuGet.org"
* Wprowadź ciąg "NSwag. AspNetCore" w polu wyszukiwania
* Wybierz pakiet "NSwag. AspNetCore" z okienka wyników, a następnie kliknij pozycję **Dodaj pakiet** .

# <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli)

Uruchom następujące polecenie:

```dotnetcli
dotnet add TodoApi.csproj package NSwag.AspNetCore
```

---

## <a name="add-and-configure-swagger-middleware"></a>Dodawanie i Konfigurowanie oprogramowania pośredniczącego programu Swagger

Dodaj i skonfiguruj strukturę Swagger w aplikacji ASP.NET Core, wykonując następujące czynności:

* W `Startup.ConfigureServices` metodzie Zarejestruj wymagane usługi Swagger:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

* W `Startup.Configure` metodzie Włącz oprogramowanie pośredniczące do obsługi wygenerowanej specyfikacji struktury Swagger i interfejsu użytkownika programu Swagger:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_Configure&highlight=6-7)]

* Uruchomić aplikację. Przejdź do:
  * `http://localhost:<port>/swagger`Aby wyświetlić interfejs użytkownika struktury Swagger.
  * `http://localhost:<port>/swagger/v1/swagger.json`Aby wyświetlić specyfikację struktury Swagger.

## <a name="code-generation"></a>Generowanie kodu

Aby skorzystać z możliwości generowania kodu w programie NSwag, należy wybrać jedną z następujących opcji:

* [NSwagStudio](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio): aplikacja klasyczna systemu Windows do generowania kodu klienta interfejsu API w języku C# lub TypeScript.
* Pakiety NuGet [NSwag. CodeGeneration. CSharp](https://www.nuget.org/packages/NSwag.CodeGeneration.CSharp/) lub [NSwag. CodeGeneration. TypeScript](https://www.nuget.org/packages/NSwag.CodeGeneration.TypeScript/) na potrzeby generowania kodu w ramach projektu.
* NSwag z [wiersza polecenia](https://github.com/RicoSuter/NSwag/wiki/CommandLine).
* Pakiet NuGet [NSwag. MSBuild](https://github.com/RicoSuter/NSwag/wiki/NSwag.MSBuild) .
* [Połączona usługa openapi (Swagger)](https://marketplace.visualstudio.com/items?itemName=Unchase.unchaseopenapiconnectedservice): usługa połączona programu Visual Studio do generowania kodu klienta interfejsu API w języku C# lub TypeScript. Program generuje również kontrolery C# dla usług OpenAPI Services z NSwag.

### <a name="generate-code-with-nswagstudio"></a>Generuj kod przy użyciu NSwagStudio

* Zainstaluj program NSwagStudio, postępując zgodnie z instrukcjami w [repozytorium GitHub NSwagStudio](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio). Na stronie wydanie NSwag można pobrać wersję narzędzia XCOPY, którą można uruchomić bez uprawnień instalacji i administratora.
* Uruchom NSwagStudio i wprowadź adres URL pliku *Swagger. JSON* w polu tekstowym **adres URL specyfikacji struktury Swagger** . Na przykład *http://localhost:44354/swagger/v1/swagger.json* .
* Kliknij przycisk **Utwórz kopię lokalną** , aby wygenerować reprezentację JSON specyfikacji struktury Swagger.

  ![Utwórz lokalną kopię specyfikacji struktury Swagger](web-api-help-pages-using-swagger/_static/CreateLocalCopy-NSwagStudio.PNG)

* W obszarze dane **wyjściowe** kliknij pole wyboru **Klient CSharp** . W zależności od projektu można również wybrać **klienta TypeScript** lub **kontroler internetowego interfejsu API CSharp**. W przypadku wybrania opcji **kontroler internetowego interfejsu API CSharp**Specyfikacja usługi ponownie kompiluje usługę, służącą jako generacja odwrotna.
* Kliknij przycisk **Generuj dane wyjściowe** , aby utworzyć kompletną implementację klienta C# w projekcie *TodoApi. NSwag* . Aby wyświetlić wygenerowany kod klienta, kliknij kartę **Klient CSharp** :

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
> Kod klienta C# jest generowany na podstawie opcji wybranych na karcie **Ustawienia** . zmodyfikuj ustawienia, aby wykonać zadania, takie jak domyślna zmiana nazw obszaru nazw i generowanie metody synchronicznej.

* Skopiuj wygenerowany kod C# do pliku w projekcie klienta, który będzie korzystał z interfejsu API.
* Rozpocznij korzystanie z internetowego interfejsu API:

```csharp
 var todoClient = new TodoClient();

// Gets all to-dos from the API
 var allTodos = await todoClient.GetAllAsync();

 // Create a new TodoItem, and save it via the API.
var createdTodo = await todoClient.CreateAsync(new TodoItem());

// Get a single to-do by ID
var foundTodo = await todoClient.GetByIdAsync(1);
```

## <a name="customize-api-documentation"></a>Dostosuj dokumentację interfejsu API

Struktura Swagger zawiera opcje dokumentujące model obiektów, co ułatwia użycie internetowego interfejsu API.

### <a name="api-info-and-description"></a>Informacje o interfejsie API i opis

W `Startup.ConfigureServices` metodzie akcja konfiguracji przeniesiona do `AddSwaggerDocument` metody powoduje dodanie informacji, takich jak autor, licencja i opis:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup2.cs?name=snippet_AddSwaggerDocument)]

Interfejs użytkownika struktury Swagger wyświetla informacje o wersji:

![Interfejs użytkownika struktury Swagger z informacjami o wersji](web-api-help-pages-using-swagger/_static/custom-info-nswag.png)

### <a name="xml-comments"></a>komentarze XML

Aby włączyć Komentarze XML, wykonaj następujące czynności:

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* Kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań** a następnie wybierz polecenie **edytuj <Project_Name>. csproj**.
* Ręcznie Dodaj wyróżnione wiersze do pliku *csproj* :

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* Kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań** i wybierz polecenie **Właściwości**
* Sprawdź pole **plik dokumentacji XML** w sekcji **dane wyjściowe** na karcie **kompilacja**

::: moniker-end

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* W *okienko rozwiązania*naciśnij klawisz **Control** i kliknij nazwę projektu. Przejdź do **menu Narzędzia**  >  **Edytuj plik**.
* Ręcznie Dodaj wyróżnione wiersze do pliku *csproj* :

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* Otwórz okno dialogowe **Opcje projektu** > **Build** > **kompilator** kompilacji
* Zaznacz pole **Generuj dokumentację XML** w sekcji **Opcje ogólne** .

::: moniker-end

# <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli)

Ręcznie Dodaj wyróżnione wiersze do pliku *csproj* :

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

---

### <a name="data-annotations"></a>Adnotacje danych

::: moniker range="<= aspnetcore-2.0"

Ponieważ NSwag używa [odbicia](/dotnet/csharp/programming-guide/concepts/reflection), a zalecanym typem zwracanym dla akcji interfejsu API sieci Web jest [IActionResult](xref:Microsoft.AspNetCore.Mvc.IActionResult), nie może wnioskować o to, co działa i co zwraca.

Rozpatrzmy następujący przykład:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

Poprzednia akcja zwróci wartość `IActionResult` , ale wewnątrz akcji, która zwraca albo [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*) lub [nieprawidłowego żądania](xref:System.Web.Http.ApiController.BadRequest*). Użyj adnotacji danych, aby poinformować klientów, których kodów stanu HTTP Ta akcja jest zwracana. Oznacz akcję następującymi atrybutami:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

Ponieważ NSwag używa [odbicia](/dotnet/csharp/programming-guide/concepts/reflection), a zalecanym typem zwracanym dla akcji interfejsu API sieci Web jest [ActionResult \<T> ](xref:Microsoft.AspNetCore.Mvc.ActionResult%601), można tylko wnioskować o typie zwracanym zdefiniowanym przez `T` . Nie można automatycznie wywnioskować innych możliwych typów zwracanych.

Rozpatrzmy następujący przykład:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

Poprzednia akcja zwraca wartość `ActionResult<T>` . Wewnątrz akcji zwraca [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*). Ponieważ kontroler ma [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atrybut, istnieje również odpowiedź [nieprawidłowego żądania](xref:System.Web.Http.ApiController.BadRequest*) . Aby uzyskać więcej informacji, zobacz [Automatyczne HTTP 400 odpowiedzi](xref:web-api/index#automatic-http-400-responses). Użyj adnotacji danych, aby poinformować klientów, których kodów stanu HTTP Ta akcja jest zwracana. Oznacz akcję następującymi atrybutami:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

W ASP.NET Core 2,2 lub nowszych można używać konwencji zamiast jawnie dekorowania nazwy poszczególne akcje z `[ProducesResponseType]` . Aby uzyskać więcej informacji, zobacz <xref:web-api/advanced/conventions>.

::: moniker-end

Generator Swagger może teraz prawidłowo opisać tę akcję, a wygenerowane klienci wiedzą, co otrzymują podczas wywoływania punktu końcowego. Zgodnie z zaleceniem Oznacz wszystkie akcje z tymi atrybutami.

Aby uzyskać wskazówki dotyczące odpowiedzi HTTP, które powinny być zwracane przez działania interfejsu API, zobacz [specyfikację RFC 7231](https://tools.ietf.org/html/rfc7231#section-4.3).
