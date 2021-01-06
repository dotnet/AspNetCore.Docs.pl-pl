---
page_type: sample
description: Dowiedz się, jak dodać Swashbuckle do projektu interfejsu API sieci Web ASP.NET Core, aby zintegrować interfejs użytkownika struktury Swagger.
languages:
- csharp
products:
- dotnet-core
- aspnet-core
- vs
- vs-code
- vs-mac
urlFragment: getstarted-swashbuckle-aspnetcore
ms.openlocfilehash: e02247325f430b0ce23dbb3f5bc344a60a1a164a
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "78659937"
---
# <a name="get-started-with-swashbuckle-and-aspnet-core"></a>Wprowadzenie do Swashbuckle i ASP.NET Core

W przypadku korzystania z internetowego interfejsu API zrozumienie jego różnych metod może być trudne dla dewelopera. Struktura [Swagger](https://swagger.io/), znana również jako [openapi](https://www.openapis.org/), rozwiązuje problem związany z generowaniem użytecznej dokumentacji i stron pomocy dla interfejsów API sieci Web. Zapewnia takie korzyści, jak dokumentacja interaktywna, generowanie zestawu SDK klienta i możliwość odnajdywania interfejsów API.

W tym przykładzie pokazano [Swashbuckle. AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) implementacja platformy .NET.

## <a name="add-and-configure-swagger-middleware"></a>Dodawanie i Konfigurowanie oprogramowania pośredniczącego programu Swagger

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<TodoContext>(opt =>
        opt.UseInMemoryDatabase("TodoList"));
    services.AddControllers();

    // Register the Swagger generator, defining 1 or more Swagger documents
    services.AddSwaggerGen(c =>
    {
        c.SwaggerDoc("v1", new OpenApiInfo { Title = "My API", Version = "v1" });
    });
}
```

W `Startup.Configure` metodzie Włącz oprogramowanie pośredniczące do obsługi wygenerowanego dokumentu JSON i interfejsu użytkownika programu Swagger:

```csharp
public void Configure(IApplicationBuilder app)
{
    // Enable middleware to serve generated Swagger as a JSON endpoint.
    app.UseSwagger();

    // Enable middleware to serve swagger-ui (HTML, JS, CSS, etc.),
    // specifying the Swagger JSON endpoint.
    app.UseSwaggerUI(c =>
    {
        c.SwaggerEndpoint("/swagger/v1/swagger.json", "My API V1"); 
    });

    app.UseRouting();
    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });
}
```

Poprzednie `UseSwaggerUI` wywołanie metody włącza [oprogramowanie pośredniczące pliku statycznego](https://docs.microsoft.com/aspnet/core/fundamentals/static-files). Jeśli obiektem docelowym jest .NET Framework lub .NET Core 1. x, Dodaj pakiet NuGet [Microsoft. AspNetCore. StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) do projektu.

Uruchom aplikację i przejdź do `http://localhost:<port>/swagger/v1/swagger.json` . Wygenerowany dokument opisujący punkty końcowe pojawia się, jak pokazano w [specyfikacji struktury Swagger (swagger.json)](https://docs.microsoft.com/aspnet/core/tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson).

Interfejs użytkownika struktury Swagger można znaleźć pod adresem `http://localhost:<port>/swagger` . Eksploruj interfejs API za pośrednictwem interfejsu użytkownika struktury Swagger i Uwzględnij go w innych programach.

> [!TIP]
> Aby obpracować interfejs użytkownika struktury Swagger w katalogu głównym aplikacji ( `http://localhost:<port>/` ), ustaw `RoutePrefix` Właściwość na pusty ciąg:
>
> ```csharp
>app.UseSwaggerUI(c =>
>{
>    c.SwaggerEndpoint("/swagger/v1/swagger.json", "My API V1");
>    c.RoutePrefix = string.Empty;
>});
>```

W przypadku używania katalogów z usługami IIS lub zwrotnego serwera proxy Ustaw punkt końcowy struktury Swagger na ścieżkę względną przy użyciu `./` prefiksu. Na przykład `./swagger/v1/swagger.json`. Użycie `/swagger/v1/swagger.json` instruuje aplikację, aby wyszukać plik JSON w prawdziwym katalogu głównym adresu URL (plus prefiks trasy, jeśli jest używany). Na przykład: użyj opcji `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` zamiast `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.

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

```csharp
// Register the Swagger generator, defining 1 or more Swagger documents
services.AddSwaggerGen(c =>
{
    c.SwaggerDoc("v1", new OpenApiInfo
    {
        Version = "v1",
        Title = "ToDo API",
        Description = "A simple example ASP.NET Core Web API",
        TermsOfService = new Uri("https://example.com/terms"),
        Contact = new OpenApiContact
        {
            Name = "Shayne Boyer",
            Email = string.Empty,
            Url = new Uri("https://twitter.com/spboyer"),
        },
        License = new OpenApiLicense
        {
            Name = "Use under LICX",
            Url = new Uri("https://example.com/license"),
        }
    });
});
```

Interfejs użytkownika struktury Swagger wyświetla informacje o wersji:

![Interfejs użytkownika struktury Swagger z informacjami o wersji: Description, Author i linku więcej](sample_images/custom-info.png)

### <a name="xml-comments"></a>Komentarze XML

Komentarze XML można włączyć przy użyciu następujących metod:

#### <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* Kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań** a następnie wybierz polecenie **edytuj <Project_Name>. csproj**.
* Ręcznie Dodaj wyróżnione wiersze do pliku *csproj* :

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

#### <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

* W *okienko rozwiązania* naciśnij klawisz **Control** i kliknij nazwę projektu. Przejdź do **menu Narzędzia**  >  **Edytuj plik**.
* Ręcznie Dodaj wyróżnione wiersze do pliku *csproj* :

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Ręcznie Dodaj wyróżnione wiersze do pliku *csproj* :

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

---

Włączenie komentarzy XML zapewnia informacje debugowania dla nieudokumentowanych typów publicznych i członków. Nieudokumentowane typy i elementy członkowskie są wskazywane przez komunikat ostrzegawczy. Na przykład następujący komunikat oznacza naruszenie kodu ostrzegawczego 1591:

```text
warning CS1591: Missing XML comment for publicly visible type or member 'TodoController.GetAll()'
```

Aby pominąć ostrzeżenia dla całego projektu, należy zdefiniować rozdzielaną średnikami listę kodów ostrzeżeń do ignorowania w pliku projektu. Dołączanie kodów ostrzeżeń w celu `$(NoWarn);` zastosowania [wartości domyślnych języka C#](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16) .

```xml
<NoWarn>$(NoWarn);1591</NoWarn>
```

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

```csharp
/// NOTE LAST 3 LINES IN THIS SNIPPET
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<TodoContext>(opt =>
        opt.UseInMemoryDatabase("TodoList"));
    services.AddControllers();

    // Register the Swagger generator, defining 1 or more Swagger documents
    services.AddSwaggerGen(c =>
    {
        c.SwaggerDoc("v1", new OpenApiInfo
        {
            Version = "v1",
            Title = "ToDo API",
            Description = "A simple example ASP.NET Core Web API",
            TermsOfService = new Uri("https://example.com/terms"),
            Contact = new OpenApiContact
            {
                Name = "Shayne Boyer",
                Email = string.Empty,
                Url = new Uri("https://twitter.com/spboyer"),
            },
            License = new OpenApiLicense
            {
                Name = "Use under LICX",
                Url = new Uri("https://example.com/license"),
            }
        });

        // Set the comments path for the Swagger JSON and UI.
        var xmlFile = $"{Assembly.GetExecutingAssembly().GetName().Name}.xml";
        var xmlPath = Path.Combine(AppContext.BaseDirectory, xmlFile);
        c.IncludeXmlComments(xmlPath);
    });
}
```

W poprzednim kodzie [odbicie](/dotnet/csharp/programming-guide/concepts/reflection) jest używane do kompilowania nazwy pliku XML pasującego do projektu interfejsu API sieci Web. Właściwość [AppContext. BaseDirectory](/dotnet/api/system.appcontext.basedirectory) służy do konstruowania ścieżki do pliku XML. Niektóre funkcje struktury Swagger (na przykład schematu parametrów wejściowych lub metod HTTP i kodów odpowiedzi z odpowiednich atrybutów) działają bez użycia pliku dokumentacji XML. W przypadku większości funkcji, a mianowicie podsumowania metod i opisów parametrów i kodów odpowiedzi, użycie pliku XML jest obowiązkowe.

Dodawanie do akcji komentarzy z potrójnym ukośnikiem ulepsza wyniki narzędzia Swagger UI przez dodanie opisu do nagłówka sekcji. Dodaj [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) element powyżej `Delete` akcji:

```csharp
/// <summary>
/// Deletes a specific TodoItem.
/// </summary>
/// <param name="id"></param>        
[HttpDelete("{id}")]
public IActionResult Delete(long id)
{
    var todo = _context.TodoItems.Find(id);

    if (todo == null)
    {
        return NotFound();
    }

    _context.TodoItems.Remove(todo);
    _context.SaveChanges();

    return NoContent();
}
```
Interfejs użytkownika struktury Swagger wyświetla tekst wewnętrzny elementu poprzedniego kodu `<summary>` :

![Interfejs użytkownika struktury Swagger pokazujący komentarz XML "Usuwa określony TodoItem". dla metody DELETE](sample_images/triple-slash-comments.png)

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

```csharp
/// <summary>
/// Creates a TodoItem.
/// </summary>
/// <remarks>
/// Sample request:
///
///     POST /Todo
///     {
///        "id": 1,
///        "name": "Item1",
///        "isComplete": true
///     }
///
/// </remarks>
/// <param name="item"></param>
/// <returns>A newly created TodoItem</returns>
/// <response code="201">Returns the newly created item</response>
/// <response code="400">If the item is null</response>            
[HttpPost]
[ProducesResponseType(StatusCodes.Status201Created)]
[ProducesResponseType(StatusCodes.Status400BadRequest)]
public ActionResult<TodoItem> Create(TodoItem item)
{
    _context.TodoItems.Add(item);
    _context.SaveChanges();

    return CreatedAtRoute("GetTodo", new { id = item.Id }, item);
}
```
Zwróć uwagę na ulepszenia interfejsu użytkownika z następującymi dodatkowymi komentarzami:

![Interfejs użytkownika struktury Swagger z pokazanymi dodatkowymi komentarzami](sample_images/xml-comments-extended.png)

### <a name="data-annotations"></a>Adnotacje danych

Oznacz model atrybutami, które znajdują się w przestrzeni nazw [System. ComponentModel. DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) , aby ułatwić DYSKOM interfejsu użytkownika struktury Swagger.

Dodaj `[Required]` atrybut do `Name` właściwości `TodoItem` klasy:

```csharp
using System.ComponentModel;
using System.ComponentModel.DataAnnotations;

namespace TodoApi.Models
{
    public class TodoItem
    {
        public long Id { get; set; }

        [Required]
        public string Name { get; set; }

        [DefaultValue(false)]
        public bool IsComplete { get; set; }
    }
}
```

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

```csharp
[Produces("application/json")]
[Route("api/[controller]")]
[ApiController]
public class TodoController : ControllerBase
{
    private readonly TodoContext _context;
```
Lista rozwijana **Typ zawartości odpowiedzi** wybiera ten typ zawartości jako domyślny dla akcji Get kontrolera:

![Interfejs użytkownika struktury Swagger z domyślnym typem zawartości odpowiedzi](sample_images/json-response-content-type.png)

W miarę wzrostu użycia adnotacji danych w interfejsie API sieci Web strony interfejsu użytkownika i interfejsu API stają się bardziej opisowe i przydatne.

### <a name="describe-response-types"></a>Opisz typy odpowiedzi

Deweloperzy korzystający z internetowego interfejsu API są najbardziej zainteresowani, które są zwracane w &mdash; szczególności typy odpowiedzi i kody błędów (jeśli nie są standardem). Typy odpowiedzi i kody błędów są oznaczane w komentarzach XML i adnotacjach danych.

`Create`Akcja zwraca kod stanu HTTP 201 na sukcesie. Kod stanu HTTP 400 jest zwracany, gdy treść ogłoszonego żądania ma wartość null. Bez odpowiedniej dokumentacji w interfejsie użytkownika programu Swagger klient nie ma wiedzy na temat oczekiwanych wyników. Rozwiąż ten problem, dodając wyróżnione wiersze w następującym przykładzie:

```csharp
/// <returns>A newly created TodoItem</returns>
/// <response code="201">Returns the newly created item</response>
/// <response code="400">If the item is null</response>            
[HttpPost]
[ProducesResponseType(StatusCodes.Status201Created)]
[ProducesResponseType(StatusCodes.Status400BadRequest)]
public ActionResult<TodoItem> Create(TodoItem item)
```

Interfejs użytkownika struktury Swagger teraz jasno dokumentuje oczekiwane kody odpowiedzi HTTP:

![Interfejs użytkownika struktury Swagger pokazujący opis klasy odpowiedzi "zwraca nowo utworzony element zadania" i "400-Jeśli element ma wartość null" dla kodu stanu i przyczyny w komunikatach odpowiedzi](sample_images/data-annotations-response-types.png)

W ASP.NET Core 2,2 lub nowszych Konwencji mogą służyć jako alternatywa dla jawnego dekorowania nazwy poszczególnych akcji z `[ProducesResponseType]` . Aby uzyskać więcej informacji, zobacz [Korzystanie z Konwencji interfejsu API sieci Web](https://docs.microsoft.com/aspnet/core/web-api/advanced/conventions).

Aby uzyskać informacje na temat dostosowywania interfejsu użytkownika, zobacz: [Dostosowywanie interfejsu użytkownika](/aspnet/core/tutorials/getting-started-with-swashbuckle?#customize-and-extend)
