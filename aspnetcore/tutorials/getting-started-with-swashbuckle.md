---
title: Zacznij ciemiężc z Swashbuckle i ASP.NET Core
author: zuckerthoben
description: Dowiedz się, jak dodać Swashbuckle do projektu internetowego interfejsu API ASP.NET Core, aby zintegrować interfejs użytkownika Swagger.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/17/2020
uid: tutorials/get-started-with-swashbuckle
ms.openlocfilehash: da848ef9c5fa85f5186d1b6f0a6111d8c8d069c4
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78661302"
---
# <a name="get-started-with-swashbuckle-and-aspnet-core"></a>Zacznij ciemiężc z Swashbuckle i ASP.NET Core

Przez [Shayne Boyer](https://twitter.com/spboyer) i [Scott Addie](https://twitter.com/Scott_Addie)

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))

Pakiet Swashbuckle składa się z trzech głównych składników:

* [Swashbuckle.AspNetCore.Swagger](https://www.nuget.org/packages/Swashbuckle.AspNetCore.Swagger/): model obiektów Swagger i `SwaggerDocument` oprogramowanie pośredniczące do uwidaczniania obiektów jako punktów końcowych JSON.

* [Swashbuckle.AspNetCore.SwaggerGen](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerGen/): generator Swagger, `SwaggerDocument` który buduje obiekty bezpośrednio z tras, kontrolerów i modeli. Zazwyczaj jest ona łączona z oprogramowaniem pośredniczącym punktu końcowego programu Swagger, aby automatycznie uwidaczniać kod JSON programu Swagger.

* [Swashbuckle.AspNetCore.SwaggerUI](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerUI/): osadzona wersja narzędzia Swagger UI. Interpretuje kod JSON programu Swagger w celu tworzenia rozbudowanych, możliwych do dostosowania środowisk na potrzeby opisywania funkcji internetowego interfejsu API. Obejmuje wbudowane kontrolery testów dla metod publicznych.

## <a name="package-installation"></a>Instalacja pakietu

Swashbuckle można dodać z następującymi podejściami:

### <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* W oknie **Konsoli Menedżera pakietów:**
  * Przejdź do **zobacz** > inną**konsolę Menedżera pakietów** **systemu Windows** > 
  * Przejdź do katalogu, w którym istnieje plik *TodoApi.csproj*
  * Wykonaj następujące polecenie:

    ```powershell
    Install-Package Swashbuckle.AspNetCore -Version 5.0.0
    ```

* W oknie dialogowym **Zarządzanie pakietami NuGet:**
  * Kliknij prawym przyciskiem myszy projekt w **Eksploratorze** > rozwiązań**Zarządzaj pakietami NuGet**
  * Ustaw **źródło pakietu** na "nuget.org"
  * Upewnij się, że opcja "Dołącz wydanie wstępne" jest włączona
  * Wpisz "Swashbuckle.AspNetCore" w polu wyszukiwania
  * Wybierz najnowszy pakiet "Swashbuckle.AspNetCore" z zakładki **Przeglądaj** i **kliknij** zainstaluj

### <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

* Kliknij prawym przyciskiem myszy folder *Pakiety* w **panelu rozrachowy** > **Dodaj pakiety...**
* Ustaw okno **Dodawanie pakietów** **w oknie Źródło** listy rozwijanej na "nuget.org"
* Upewnij się, że opcja "Pokaż pakiety wersji wstępnej" jest włączona
* Wpisz "Swashbuckle.AspNetCore" w polu wyszukiwania
* Wybierz najnowszy pakiet "Swashbuckle.AspNetCore" z okienka wyników i kliknij dodaj **pakiet**

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Uruchom następujące polecenie z **terminalu zintegrowanego:**

```dotnetcli
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore -v 5.0.0
```

### <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/netcore-cli)

Uruchom następujące polecenie:

```dotnetcli
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore -v 5.0.0
```

---

## <a name="add-and-configure-swagger-middleware"></a>Dodawanie i konfigurowanie oprogramowania pośredniczącego Swagger

W `Startup` klasie zaimportuj następujący `OpenApiInfo` obszar nazw, aby użyć tej klasy:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_InfoClassNamespace)]

Dodaj generator Swagger do kolekcji `Startup.ConfigureServices` usług w metodzie:

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8-11)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=9-12)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8-11)]

::: moniker-end

W `Startup.Configure` metodzie włącz oprogramowanie pośredniczące do obsługi wygenerowanego dokumentu JSON i interfejsu użytkownika Swagger:

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

::: moniker-end

Poprzednie wywołanie `UseSwaggerUI` metody włącza [oprogramowanie pośredniczące pliku statycznego](xref:fundamentals/static-files). Jeśli jest kierowana na program .NET Framework lub .NET Core 1.x, dodaj do projektu pakiet [NuGet firmy Microsoft.AspNetCore.StaticFiles.](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/)

Uruchom aplikację i przejdź `http://localhost:<port>/swagger/v1/swagger.json`do . Wygenerowany dokument opisujący punkty końcowe jest wyświetlany w sposób pokazany w [specyfikacji Swaggera (swagger.json).](xref:tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson)

Interfejs użytkownika Swagger można `http://localhost:<port>/swagger`znaleźć w . Eksploruj interfejs API za pośrednictwem interfejsu użytkownika Swagger i włącz go do innych programów.

> [!TIP]
> Aby służyć interfejsowi użytkownika swagger w`http://localhost:<port>/`katalogu głównym `RoutePrefix` aplikacji ( ), ustaw właściwość na pusty ciąg:
>
> [!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup3.cs?name=snippet_UseSwaggerUI&highlight=4)]

Jeśli używasz katalogów z usługami IIS lub odwrotnego serwera proxy, `./` ustaw punkt końcowy Swagger na ścieżkę względną przy użyciu prefiksu. Na przykład `./swagger/v1/swagger.json`. Za `/swagger/v1/swagger.json` pomocą nakazuje aplikacji, aby wyszukać plik JSON w prawdziwym katalogu głównym adresu URL (plus prefiks trasy, jeśli jest używany). Na przykład: użyj opcji `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` zamiast `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.

## <a name="customize-and-extend"></a>Dostosowywanie i rozszerzanie

Swagger udostępnia opcje dokumentowania modelu obiektu i dostosowywania interfejsu użytkownika w celu dopasowania do motywu.

W `Startup` klasie dodaj następujące przestrzenie nazw:

```csharp
using System;
using System.Reflection;
using System.IO;
```

### <a name="api-info-and-description"></a>Informacje i opis interfejsu API

Akcja konfiguracji przekazana `AddSwaggerGen` do metody dodaje informacje, takie jak autor, licencja i opis:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup4.cs?name=snippet_AddSwaggerGen)]

Interfejs użytkownika Swagger wyświetla informacje o wersji:

![Swagger UI z informacjami o wersji: opis, autor i zobacz więcej linków](web-api-help-pages-using-swagger/_static/custom-info.png)

### <a name="xml-comments"></a>komentarze XML

Komentarze XML można włączyć za pomocą następujących metod:

#### <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* Kliknij prawym przyciskiem myszy projekt w **Eksploratorze rozwiązań** i wybierz polecenie **Edytuj <project_name>.csproj**.
* Ręcznie dodaj wyróżnione wiersze do pliku *csproj:*

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* Kliknij prawym przyciskiem myszy projekt w **Eksploratorze rozwiązań** i wybierz polecenie **Właściwości**.
* Zaznacz pole **pliku dokumentacji XML** w sekcji **Dane wyjściowe** na karcie **Kompilacja.**

::: moniker-end

#### <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* W *panelu rozrachówek*naciśnij **kontrolkę** i kliknij nazwę projektu. Przejdź do **pozycji Narzędzia** > **edycji pliku**.
* Ręcznie dodaj wyróżnione wiersze do pliku *csproj:*

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* Otwórz okno dialogowe **Opcje projektu** > **kompilator** **kompilacji** >
* Zaznacz pole **Wyboru Generuj dokumentację xml** w sekcji **Opcje ogólne**

::: moniker-end

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Ręcznie dodaj wyróżnione wiersze do pliku *csproj:*

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

#### <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/netcore-cli)

Ręcznie dodaj wyróżnione wiersze do pliku *csproj:*

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

---

Włączenie komentarzy XML zapewnia informacje debugowania dla nieudokumentowanych typów publicznych i członków. Nieudokumentowane typy i elementy członkowskie są oznaczone komunikatem ostrzegawczym. Na przykład następujący komunikat wskazuje naruszenie kodu ostrzeżenia 1591:

```text
warning CS1591: Missing XML comment for publicly visible type or member 'TodoController.GetAll()'
```

Aby pominąć ostrzeżenia w całym projekcie, zdefiniuj listę kodów ostrzeżeń rozdzielonych średnikami, które mają być ignorowane w pliku projektu. Dołączanie kodów ostrzeżeń do `$(NoWarn);` zastosowania wartości [domyślnych języka C#.](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16)

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

Aby pominąć ostrzeżenia tylko dla określonych elementów członkowskich, należy ująć kod w [#pragma ostrzeżenia](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) dyrektywy preprocesora. Takie podejście jest przydatne dla kodu, który nie powinien być narażony za pośrednictwem dokumentów interfejsu API. W poniższym przykładzie kod ostrzeżenia CS1591 `Program` jest ignorowany dla całej klasy. Wymuszanie kodu ostrzeżenia jest przywracany po zamknięciu definicji klasy. Określ wiele kodów ostrzeżeń z listą rozdzielanymi przecinkami.

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

Skonfiguruj Swagger do używania pliku XML, który jest generowany z poprzednimi instrukcjami. W systemach operacyjnych Linux lub innych niż Windows nazwy plików i ścieżki mogą być rozróżniane. Na przykład plik *TodoApi.XML* jest prawidłowy w systemie Windows, ale nie CentOS.

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

W poprzednim kodzie [Odbicie](/dotnet/csharp/programming-guide/concepts/reflection) służy do tworzenia nazwy pliku XML odpowiadającej nazwie projektu internetowego interfejsu API. [Właściwość AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory*) służy do konstruowania ścieżki do pliku XML. Niektóre funkcje Swagger (na przykład schemat parametrów wejściowych lub metody HTTP i kody odpowiedzi z odpowiednich atrybutów) działają bez użycia pliku dokumentacji XML. W przypadku większości funkcji, a mianowicie podsumowań metod i opisów parametrów i kodów odpowiedzi, użycie pliku XML jest obowiązkowe.

Dodawanie do akcji komentarzy z potrójnym ukośnikiem ulepsza wyniki narzędzia Swagger UI przez dodanie opisu do nagłówka sekcji. Dodaj [ \<element podsumowania>](/dotnet/csharp/programming-guide/xmldoc/summary) nad `Delete` akcją:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Delete&highlight=1-3)]

Interfejs użytkownika Swagger wyświetla wewnętrzny tekst `<summary>` elementu poprzedniego kodu:

![Interfejs użytkownika Swagger z komentarzem XML "Usuwa określony Element TodoItem". dla metody DELETE](web-api-help-pages-using-swagger/_static/triple-slash-comments.png)

Interfejs użytkownika jest napędzany przez wygenerowany schemat JSON:

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

Dodaj [ \<uwagi>](/dotnet/csharp/programming-guide/xmldoc/remarks) element do `Create` dokumentacji metody akcji. Uzupełnia informacje określone `<summary>` w elemencie i zapewnia bardziej niezawodny interfejs użytkownika Swagger. Zawartość `<remarks>` elementu może składać się z tekstu, JSON lub XML.

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

Zwróć uwagę na ulepszenia interfejsu użytkownika z tymi dodatkowymi komentarzami:

![Interfejs użytkownika Swagger z dodatkowymi komentarzami](web-api-help-pages-using-swagger/_static/xml-comments-extended.png)

### <a name="data-annotations"></a>Adnotacje danych

Oznacz model atrybutami znajdującymi się w obszarze nazw [System.ComponentModel.DataAnnotations,](/dotnet/api/system.componentmodel.dataannotations) aby ułatwić prowadzenie składników interfejsu użytkownika Swagger.

Dodaj `[Required]` atrybut do `Name` właściwości `TodoItem` klasy:

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Models/TodoItem.cs?highlight=10)]

Obecność tego atrybutu zmienia zachowanie interfejsu użytkownika i zmienia podstawowy schemat JSON:

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

Dodaj `[Produces("application/json")]` atrybut do kontrolera interfejsu API. Jego celem jest stwierdzenie, że działania administratora obsługują typ treści odpowiedzi *aplikacji/json:*

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

Z listy rozwijanej **Typ zawartości odpowiedzi** wybiera ten typ zawartości jako domyślny dla akcji GET kontrolera:

![Interfejs użytkownika swagger z domyślnym typem zawartości odpowiedzi](web-api-help-pages-using-swagger/_static/json-response-content-type.png)

Wraz ze wzrostem użycia adnotacji danych w interfejsie API sieci Web strony pomocy interfejsu użytkownika i interfejsu API stają się bardziej opisowe i przydatne.

### <a name="describe-response-types"></a>Opis typów odpowiedzi

Deweloperzy zużywający internetowy interfejs API są najbardziej&mdash;zaniepokojeni zwracanymi typami odpowiedzi i kodami błędów (jeśli nie standardowymi). Typy odpowiedzi i kody błędów są oznaczone w komentarzach XML i adnotacjach danych.

Akcja `Create` zwraca kod stanu HTTP 201 po sukcesie. Kod stanu HTTP 400 jest zwracany, gdy treść zaksięgowanego żądania ma wartość null. Bez odpowiedniej dokumentacji w interfejsie użytkownika Swagger konsument nie zna tych oczekiwanych wyników. Rozwiązać ten problem, dodając wyróżnione wiersze w poniższym przykładzie:

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

Interfejs użytkownika Swagger teraz wyraźnie dokumentuje oczekiwane kody odpowiedzi HTTP:

![Interfejs użytkownika swagger pokazujący opis klasy odpowiedzi POST "Zwraca nowo utworzony element Todo" i "400 — jeśli element ma wartość null" dla kodu stanu i przyczyny w obszarze Wiadomości odpowiedzi](web-api-help-pages-using-swagger/_static/data-annotations-response-types.png)

::: moniker range=">= aspnetcore-2.2"

W ASP.NET Core 2.2 lub nowszym konwencje mogą być używane jako `[ProducesResponseType]`alternatywa dla jawnego dekorowania poszczególnych działań za pomocą programu . Aby uzyskać więcej informacji, zobacz <xref:web-api/advanced/conventions>.

::: moniker-end

### <a name="customize-the-ui"></a>Dostosowywanie interfejsu użytkownika

Interfejs użytkownika akcji jest zarówno funkcjonalny, jak i reprezentacyjny. Jednak strony dokumentacji interfejsu API powinny reprezentować Twoją markę lub motyw. Znakowanie składników Swashbuckle wymaga dodania zasobów do obsługi plików statycznych i tworzenia struktury folderów do obsługi tych plików.

Jeśli obiekt .NET Framework lub .NET Core 1.x jest skierowany do projektu, dodaj do projektu pakiet [NuGet microsoft.AspNetCore.StaticFiles:](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles)

```xml
<PackageReference Include="Microsoft.AspNetCore.StaticFiles" Version="2.0.0" />
```

Poprzedni pakiet NuGet jest już zainstalowany, jeśli jest skierowany na .NET Core 2.x i używa [metapakiety](xref:fundamentals/metapackage).

Włącz oprogramowanie pośredniczące plików statycznych:

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

::: moniker-end

Uzyskaj zawartość folderu *dist* z [repozytorium Swagger UI GitHub](https://github.com/swagger-api/swagger-ui/tree/master/dist). Ten folder zawiera niezbędne zasoby dla strony interfejsu użytkownika swagger.

Utwórz folder *wwwroot/swagger/ui* i skopiuj do niego zawartość folderu *dist.*

Utwórz plik *custom.css* w *wwwroot/swagger/ui*z następującym CSS, aby dostosować nagłówek strony:

[!code-css[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/wwwroot/swagger/ui/custom.css)]

Odwołanie *do pliku custom.css* w pliku *index.html* wewnątrz folderu interfejsu użytkownika, po innych plikach CSS:

[!code-html[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/wwwroot/swagger/ui/index.html?name=snippet_SwaggerUiCss&highlight=3)]

Przejdź do strony *index.html* pod adresem `http://localhost:<port>/swagger/ui/index.html`. Wprowadź `https://localhost:<port>/swagger/v1/swagger.json` w polu tekstowym nagłówka i kliknij przycisk **Eksploruj.** Wynikowa strona wygląda następująco:

![Interfejs użytkownika swagger z niestandardowym tytułem nagłówka](web-api-help-pages-using-swagger/_static/custom-header.png)

Strona może zrobić znacznie więcej. Zobacz pełne możliwości zasobów interfejsu użytkownika w [repozytorium GitHub interfejsu użytkownika użytkownika użytkownika.](https://github.com/swagger-api/swagger-ui)
