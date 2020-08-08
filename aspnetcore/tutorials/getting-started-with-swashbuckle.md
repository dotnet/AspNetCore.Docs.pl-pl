---
title: Wprowadzenie do Swashbuckle i ASP.NET Core
author: zuckerthoben
description: Dowiedz się, jak dodać Swashbuckle do projektu interfejsu API sieci Web ASP.NET Core, aby zintegrować interfejs użytkownika struktury Swagger.
ms.author: scaddie
ms.custom: mvc
ms.date: 06/26/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/get-started-with-swashbuckle
ms.openlocfilehash: 5ecb6379f00c3ad505a447dfc0d3fac5f857cb09
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022189"
---
# <a name="get-started-with-swashbuckle-and-aspnet-core"></a>Wprowadzenie do Swashbuckle i ASP.NET Core

Autorzy [Shayne Boyer](https://twitter.com/spboyer) i [Scott Addie](https://twitter.com/Scott_Addie)

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))

Pakiet Swashbuckle składa się z trzech głównych składników:

* [Swashbuckle. AspNetCore. Swagger](https://www.nuget.org/packages/Swashbuckle.AspNetCore.Swagger/): model obiektów Swagger i oprogramowanie pośredniczące umożliwiające Uwidacznianie `SwaggerDocument` obiektów jako punktów końcowych JSON.

* [Swashbuckle. AspNetCore. SwaggerGen](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerGen/): Generator Swagger, który kompiluje `SwaggerDocument` obiekty bezpośrednio z tras, kontrolerów i modeli. Zazwyczaj jest ona łączona z oprogramowaniem pośredniczącym punktu końcowego programu Swagger, aby automatycznie uwidaczniać kod JSON programu Swagger.

* [Swashbuckle. AspNetCore. SwaggerUI](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerUI/): wbudowana wersja narzędzia interfejsu użytkownika struktury Swagger. Interpretuje kod JSON programu Swagger w celu tworzenia rozbudowanych, możliwych do dostosowania środowisk na potrzeby opisywania funkcji internetowego interfejsu API. Obejmuje wbudowane kontrolery testów dla metod publicznych.

## <a name="package-installation"></a>Instalacja pakietu

Swashbuckle można dodać przy użyciu następujących metod:

### <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* W oknie **konsola Menedżera pakietów** :
  * Przejdź do **wyświetlania**  >  **innych**  >  **konsoli Menedżera pakietów** systemu Windows
  * Przejdź do katalogu, w którym znajduje się plik *TodoApi. csproj*
  * Uruchom następujące polecenie:

    ```powershell
    Install-Package Swashbuckle.AspNetCore -Version 5.5.0
    ```

* W oknie dialogowym **Zarządzanie pakietami NuGet** :
  * Kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań**  >  **Zarządzanie pakietami NuGet**
  * Ustaw **Źródło pakietu** na "NuGet.org"
  * Upewnij się, że opcja "Uwzględnij wersję wstępną" jest włączona
  * Wprowadź ciąg "Swashbuckle. AspNetCore" w polu wyszukiwania
  * Wybierz najnowszy pakiet "Swashbuckle. AspNetCore" z karty **Przeglądaj** , a następnie kliknij przycisk **Instaluj** .

### <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

* Kliknij prawym przyciskiem myszy folder *pakiety* w **okienko rozwiązania**  >  **Dodaj pakiety...**
* Ustaw listę rozwijaną **źródła** okna **Dodaj pakiety** na "NuGet.org"
* Upewnij się, że opcja "Pokaż pakiety wersji wstępnej" jest włączona
* Wprowadź ciąg "Swashbuckle. AspNetCore" w polu wyszukiwania
* Wybierz najnowszy pakiet "Swashbuckle. AspNetCore" z okienka wyników, a następnie kliknij pozycję **Dodaj pakiet** .

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Uruchom następujące polecenie w **zintegrowanym terminalu**:

```dotnetcli
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore -v 5.5.0
```

### <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/netcore-cli)

Uruchom następujące polecenie:

```dotnetcli
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore -v 5.5.0
```

---

## <a name="add-and-configure-swagger-middleware"></a>Dodawanie i Konfigurowanie oprogramowania pośredniczącego programu Swagger

Dodaj Generator Swagger do kolekcji usług w `Startup.ConfigureServices` metodzie:

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=9)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8)]

::: moniker-end

W `Startup.Configure` metodzie Włącz oprogramowanie pośredniczące do obsługi wygenerowanego dokumentu JSON i interfejsu użytkownika programu Swagger:

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

::: moniker-end

> [!NOTE]
> Swashbuckle opiera się na MVC <xref:Microsoft.AspNetCore.Mvc.ApiExplorer> , aby odnaleźć trasy i punkty końcowe. Jeśli wywołania projektu <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc%2A> , trasy i punkty końcowe są wykrywane automatycznie. Podczas wywoływania <xref:Microsoft.Extensions.DependencyInjection.MvcCoreServiceCollectionExtensions.AddMvcCore%2A> <xref:Microsoft.Extensions.DependencyInjection.MvcApiExplorerMvcCoreBuilderExtensions.AddApiExplorer%2A> Metoda musi być jawnie wywołana. Aby uzyskać więcej informacji, zobacz [Swashbuckle, ApiExplorer i Routing](https://github.com/domaindrivendev/Swashbuckle.AspNetCore#swashbuckle-apiexplorer-and-routing).

Poprzednie `UseSwaggerUI` wywołanie metody włącza [oprogramowanie pośredniczące pliku statycznego](xref:fundamentals/static-files). Jeśli obiektem docelowym jest .NET Framework lub .NET Core 1. x, Dodaj pakiet NuGet [Microsoft. AspNetCore. StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) do projektu.

Uruchom aplikację i przejdź do `http://localhost:<port>/swagger/v1/swagger.json` . Wygenerowany dokument opisujący punkty końcowe wygląda tak, jak pokazano w [specyfikacji openapi (openapi.json)](xref:tutorials/web-api-help-pages-using-swagger#openapi-specification-openapijson).

Interfejs użytkownika struktury Swagger można znaleźć pod adresem `http://localhost:<port>/swagger` . Eksploruj interfejs API za pośrednictwem interfejsu użytkownika struktury Swagger i Uwzględnij go w innych programach.

> [!TIP]
> Aby obpracować interfejs użytkownika struktury Swagger w katalogu głównym aplikacji ( `http://localhost:<port>/` ), ustaw `RoutePrefix` Właściwość na pusty ciąg:
>
> [!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup3.cs?name=snippet_UseSwaggerUI&highlight=4)]

W przypadku używania katalogów z usługami IIS lub zwrotnego serwera proxy Ustaw punkt końcowy struktury Swagger na ścieżkę względną przy użyciu `./` prefiksu. Na przykład `./swagger/v1/swagger.json`. Użycie `/swagger/v1/swagger.json` instruuje aplikację, aby wyszukać plik JSON w prawdziwym katalogu głównym adresu URL (plus prefiks trasy, jeśli jest używany). Na przykład: użyj opcji `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` zamiast `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.

> [!NOTE]
> Domyślnie Swashbuckle generuje i uwidacznia kod JSON struktury Swagger w wersji 3,0 specyfikacji &mdash; oficjalnie zwanej specyfikacją openapi. Aby zapewnić zgodność z poprzednimi wersjami, można w zamian wybrać kod JSON w formacie 2,0. Ten format 2,0 jest ważny dla integracji, takich jak Microsoft PowerShell Apps i Microsoft Flow, które obecnie obsługują OpenAPI w wersji 2,0. Aby wybrać format 2,0, ustaw `SerializeAsV2` Właściwość w `Startup.Configure` :
>
> [!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup3.cs?name=snippet_Configure&highlight=4-7)]

## <a name="customize-and-extend"></a>Dostosuj i rozwiń

Struktura Swagger zawiera opcje dokumentowania modelu obiektów i dostosowywania interfejsu użytkownika w celu dopasowania go do motywu.

W `Startup` klasie Dodaj następujące przestrzenie nazw:

```csharp
using System;
using System.Reflection;
using System.IO;
```

### <a name="api-info-and-description"></a>Informacje o interfejsie API i opis

Akcja konfiguracji przeniesiona do `AddSwaggerGen` metody powoduje dodanie informacji, takich jak autor, licencja i opis:

W `Startup` klasie zaimportuj następującą przestrzeń nazw, aby użyć `OpenApiInfo` klasy:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_InfoClassNamespace)]

Korzystając z `OpenApiInfo` klasy, należy zmodyfikować informacje wyświetlane w interfejsie użytkownika:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup4.cs?name=snippet_AddSwaggerGen)]

Interfejs użytkownika struktury Swagger wyświetla informacje o wersji:

![Interfejs użytkownika struktury Swagger z informacjami o wersji: Description, Author i linku więcej](web-api-help-pages-using-swagger/_static/custom-info.png)

### <a name="xml-comments"></a>Komentarze XML

Komentarze XML można włączyć przy użyciu następujących metod:

#### <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* Kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań** a następnie wybierz polecenie **edytuj <Project_Name>. csproj**.
* Ręcznie Dodaj wyróżnione wiersze do pliku *csproj* :

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* Kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań** i wybierz polecenie **Właściwości**.
* Sprawdź pole **plik dokumentacji XML** w sekcji **dane wyjściowe** na karcie **kompilacja** .

::: moniker-end

#### <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* W *okienko rozwiązania*naciśnij klawisz **Control** i kliknij nazwę projektu. Przejdź do **menu Narzędzia**  >  **Edytuj plik**.
* Ręcznie Dodaj wyróżnione wiersze do pliku *csproj* :

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* Otwórz okno dialogowe **Opcje projektu** > **Build** > **kompilator** kompilacji
* Zaznacz pole **Generuj dokumentację XML** w sekcji **Opcje ogólne** .

::: moniker-end

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Ręcznie Dodaj wyróżnione wiersze do pliku *csproj* :

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

#### <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/netcore-cli)

Ręcznie Dodaj wyróżnione wiersze do pliku *csproj* :

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

---

Włączenie komentarzy XML zapewnia informacje debugowania dla nieudokumentowanych typów publicznych i członków. Nieudokumentowane typy i elementy członkowskie są wskazywane przez komunikat ostrzegawczy. Na przykład następujący komunikat oznacza naruszenie kodu ostrzegawczego 1591:

```text
warning CS1591: Missing XML comment for publicly visible type or member 'TodoController.GetAll()'
```

Aby pominąć ostrzeżenia dla całego projektu, należy zdefiniować rozdzielaną średnikami listę kodów ostrzeżeń do ignorowania w pliku projektu. Dołączanie kodów ostrzeżeń w celu `$(NoWarn);` zastosowania [wartości domyślnych języka C#](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16) .

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

Aby pominąć ostrzeżenia tylko dla określonych elementów członkowskich, należy ująć kod w [#pragma ostrzeżenia](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) preprocesora. Takie podejście jest przydatne w przypadku kodu, który nie powinien być ujawniony za pośrednictwem dokumentacji interfejsu API. W poniższym przykładzie kod ostrzegawczy CS1591 jest ignorowany dla całej `Program` klasy. Wymuszanie kodu ostrzegawczego jest przywracane po zamknięciu definicji klasy. Określ wiele kodów ostrzeżeń z listą rozdzielaną przecinkami.

```csharp
namespace TodoApi
{
#pragma warning disable CS1591
    public class Program
    {
        public static void Main(string[] args) =>
            BuildWebHost(args).Run();

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseStartup<Startup>()
                .Build();
    }
#pragma warning restore CS1591
}
```

Skonfiguruj strukturę Swagger, aby używała pliku XML, który jest generowany z poprzednimi instrukcjami. W przypadku systemów operacyjnych Linux lub innych niż Windows nazwy plików i ścieżki mogą być rozróżniane wielkości liter. Na przykład plik *TodoApi.XML* jest prawidłowy w systemie Windows, ale nie CentOS.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=31-33)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup1x.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

W poprzednim kodzie [odbicie](/dotnet/csharp/programming-guide/concepts/reflection) jest używane do kompilowania nazwy pliku XML pasującego do projektu interfejsu API sieci Web. Właściwość [AppContext. BaseDirectory](xref:System.AppContext.BaseDirectory*) służy do konstruowania ścieżki do pliku XML. Niektóre funkcje struktury Swagger (na przykład schematu parametrów wejściowych lub metod HTTP i kodów odpowiedzi z odpowiednich atrybutów) działają bez użycia pliku dokumentacji XML. W przypadku większości funkcji, a mianowicie podsumowania metod i opisów parametrów i kodów odpowiedzi, użycie pliku XML jest obowiązkowe.

Dodawanie do akcji komentarzy z potrójnym ukośnikiem ulepsza wyniki narzędzia Swagger UI przez dodanie opisu do nagłówka sekcji. Dodaj [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) element powyżej `Delete` akcji:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Delete&highlight=1-3)]

Interfejs użytkownika struktury Swagger wyświetla tekst wewnętrzny elementu poprzedniego kodu `<summary>` :

![Interfejs użytkownika struktury Swagger pokazujący komentarz XML "Usuwa określony TodoItem". dla metody DELETE](web-api-help-pages-using-swagger/_static/triple-slash-comments.png)

Interfejs użytkownika jest oparty na wygenerowanym schemacie JSON:

```json
"delete": {
    "tags": [
        "Todo"
    ],
    "summary": "Deletes a specific TodoItem.",
    "operationId": "ApiTodoByIdDelete",
    "consumes": [],
    "produces": [],
    "parameters": [
        {
            "name": "id",
            "in": "path",
            "description": "",
            "required": true,
            "type": "integer",
            "format": "int64"
        }
    ],
    "responses": {
        "200": {
            "description": "Success"
        }
    }
}
```

Dodaj [\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) element do `Create` dokumentacji metody akcji. Uzupełnia on informacje określone w `<summary>` elemencie i zapewnia bardziej niezawodny interfejs użytkownika struktury Swagger. `<remarks>`Zawartość elementu może składać się z tekstu, JSON lub XML.

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

Zwróć uwagę na ulepszenia interfejsu użytkownika z następującymi dodatkowymi komentarzami:

![Interfejs użytkownika struktury Swagger z pokazanymi dodatkowymi komentarzami](web-api-help-pages-using-swagger/_static/xml-comments-extended.png)

### <a name="data-annotations"></a>Adnotacje danych

Oznacz model atrybutami, które znajdują się w przestrzeni nazw [System. ComponentModel. DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) , aby ułatwić DYSKOM interfejsu użytkownika struktury Swagger.

Dodaj `[Required]` atrybut do `Name` właściwości `TodoItem` klasy:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Models/TodoItem.cs?highlight=10)]

Obecność tego atrybutu zmienia zachowanie interfejsu użytkownika i zmienia źródłowy schemat JSON:

```json
"definitions": {
    "TodoItem": {
        "required": [
            "name"
        ],
        "type": "object",
        "properties": {
            "id": {
                "format": "int64",
                "type": "integer"
            },
            "name": {
                "type": "string"
            },
            "isComplete": {
                "default": false,
                "type": "boolean"
            }
        }
    }
},
```

Dodaj `[Produces("application/json")]` atrybut do kontrolera interfejsu API. Celem jest zadeklarowanie, że działania kontrolera obsługują typ zawartości odpowiedzi dla *aplikacji/JSON*:

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

Lista rozwijana **Typ zawartości odpowiedzi** wybiera ten typ zawartości jako domyślny dla akcji Get kontrolera:

![Interfejs użytkownika struktury Swagger z domyślnym typem zawartości odpowiedzi](web-api-help-pages-using-swagger/_static/json-response-content-type.png)

W miarę wzrostu użycia adnotacji danych w interfejsie API sieci Web strony interfejsu użytkownika i interfejsu API stają się bardziej opisowe i przydatne.

### <a name="describe-response-types"></a>Opisz typy odpowiedzi

Deweloperzy korzystający z internetowego interfejsu API są najbardziej zainteresowani, które są zwracane w &mdash; szczególności typy odpowiedzi i kody błędów (jeśli nie są standardem). Typy odpowiedzi i kody błędów są oznaczane w komentarzach XML i adnotacjach danych.

`Create`Akcja zwraca kod stanu HTTP 201 na sukcesie. Kod stanu HTTP 400 jest zwracany, gdy treść ogłoszonego żądania ma wartość null. Bez odpowiedniej dokumentacji w interfejsie użytkownika programu Swagger klient nie ma wiedzy na temat oczekiwanych wyników. Rozwiąż ten problem, dodając wyróżnione wiersze w następującym przykładzie:

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

Interfejs użytkownika struktury Swagger teraz jasno dokumentuje oczekiwane kody odpowiedzi HTTP:

![Interfejs użytkownika struktury Swagger pokazujący opis klasy odpowiedzi "zwraca nowo utworzony element zadania" i "400-Jeśli element ma wartość null" dla kodu stanu i przyczyny w komunikatach odpowiedzi](web-api-help-pages-using-swagger/_static/data-annotations-response-types.png)

::: moniker range=">= aspnetcore-2.2"

W ASP.NET Core 2,2 lub nowszych Konwencji mogą służyć jako alternatywa dla jawnego dekorowania nazwy poszczególnych akcji z `[ProducesResponseType]` . Aby uzyskać więcej informacji, zobacz <xref:web-api/advanced/conventions>.

Aby można było obsłużyć `[ProducesResponseType]` dekorację, pakiet [Swashbuckle. AspNetCore. Annotations](https://github.com/domaindrivendev/Swashbuckle.AspNetCore/blob/master/README.md#swashbuckleaspnetcoreannotations) oferuje rozszerzenia umożliwiające włączenie i wzbogacenie metadanych odpowiedzi, schematu i parametru.

::: moniker-end

### <a name="customize-the-ui"></a>Dostosowywanie interfejsu użytkownika

Domyślny interfejs użytkownika jest zarówno funkcjonalny, jak i najbardziej do wysłania. Jednak strony dokumentacji interfejsu API powinny reprezentować swoją markę lub motyw. Znakowanie składników Swashbuckle wymaga dodania zasobów do obsługi plików statycznych i skompilowania struktury folderów do hostowania tych plików.

Jeśli obiektem docelowym jest .NET Framework lub .NET Core 1. x, Dodaj pakiet NuGet [Microsoft. AspNetCore. StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles) do projektu:

```xml
<PackageReference Include="Microsoft.AspNetCore.StaticFiles" Version="2.0.0" />
```

Poprzedni pakiet NuGet jest już zainstalowany, jeśli celem jest .NET Core 2. x i używanie [pakietu](xref:fundamentals/metapackage).

Włącz oprogramowanie pośredniczące plików statycznych:

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

::: moniker-end

Aby wstrzyknąć dodatkowe arkusze stylów CSS, Dodaj je do folderu *wwwroot* projektu i określ ścieżkę względną w opcjach oprogramowania pośredniczącego:

```csharp
app.UseSwaggerUI(c =>
{
     c.InjectStylesheet("/swagger-ui/custom.css");
}
```
