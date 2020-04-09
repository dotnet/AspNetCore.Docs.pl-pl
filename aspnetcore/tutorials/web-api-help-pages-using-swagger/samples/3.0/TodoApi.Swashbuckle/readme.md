---
page_type: sample
description: Dowiedz się, jak dodać Swashbuckle do projektu internetowego interfejsu API ASP.NET Core, aby zintegrować interfejs użytkownika Swagger.
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
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78659937"
---
# <a name="get-started-with-swashbuckle-and-aspnet-core"></a>Zacznij ciemiężc z Swashbuckle i ASP.NET Core

Podczas korzystania z interfejsu API sieci Web, zrozumienie jego różnych metod może być trudne dla dewelopera. [Swagger](https://swagger.io/), znany również jako [OpenAPI,](https://www.openapis.org/)rozwiązuje problem generowania przydatnych dokumentów i stron pomocy dla interfejsów API sieci Web. Zapewnia korzyści, takie jak interaktywna dokumentacja, generowanie sdk klienta i wykrywalność interfejsu API.

W tym przykładzie [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) .NET implementacji jest wyświetlany.

## <a name="add-and-configure-swagger-middleware"></a>Dodawanie i konfigurowanie oprogramowania pośredniczącego Swagger

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

W `Startup.Configure` metodzie włącz oprogramowanie pośredniczące do obsługi wygenerowanego dokumentu JSON i interfejsu użytkownika Swagger:

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

Poprzednie wywołanie `UseSwaggerUI` metody włącza [oprogramowanie pośredniczące pliku statycznego](https://docs.microsoft.com/aspnet/core/fundamentals/static-files). Jeśli jest kierowana na program .NET Framework lub .NET Core 1.x, dodaj do projektu pakiet [NuGet firmy Microsoft.AspNetCore.StaticFiles.](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/)

Uruchom aplikację i przejdź `http://localhost:<port>/swagger/v1/swagger.json`do . Wygenerowany dokument opisujący punkty końcowe jest wyświetlany w sposób pokazany w [specyfikacji Swaggera (swagger.json).](https://docs.microsoft.com/aspnet/core/tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson)

Interfejs użytkownika Swagger można `http://localhost:<port>/swagger`znaleźć w . Eksploruj interfejs API za pośrednictwem interfejsu użytkownika Swagger i włącz go do innych programów.

> [!TIP]
> Aby służyć interfejsowi użytkownika swagger w`http://localhost:<port>/`katalogu głównym `RoutePrefix` aplikacji ( ), ustaw właściwość na pusty ciąg:
>
> ```csharp
>app.UseSwaggerUI(c =>
>{
>    c.SwaggerEndpoint("/swagger/v1/swagger.json", "My API V1");
>    c.RoutePrefix = string.Empty;
>});
>```

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

Interfejs użytkownika Swagger wyświetla informacje o wersji:

![Swagger UI z informacjami o wersji: opis, autor i zobacz więcej linków](sample_images/custom-info.png)

### <a name="xml-comments"></a>komentarze XML

Komentarze XML można włączyć za pomocą następujących metod:

#### <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

* Kliknij prawym przyciskiem myszy projekt w **Eksploratorze rozwiązań** i wybierz polecenie **Edytuj <project_name>.csproj**.
* Ręcznie dodaj wyróżnione wiersze do pliku *csproj:*

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

#### <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

* W *panelu rozrachówek*naciśnij **kontrolkę** i kliknij nazwę projektu. Przejdź do **pozycji Narzędzia** > **edycji pliku**.
* Ręcznie dodaj wyróżnione wiersze do pliku *csproj:*

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Ręcznie dodaj wyróżnione wiersze do pliku *csproj:*

```xml
<PropertyGroup>
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
</PropertyGroup>
```

---

Włączenie komentarzy XML zapewnia informacje debugowania dla nieudokumentowanych typów publicznych i członków. Nieudokumentowane typy i elementy członkowskie są oznaczone komunikatem ostrzegawczym. Na przykład następujący komunikat wskazuje naruszenie kodu ostrzeżenia 1591:

```text
warning CS1591: Missing XML comment for publicly visible type or member 'TodoController.GetAll()'
```

Aby pominąć ostrzeżenia w całym projekcie, zdefiniuj listę kodów ostrzeżeń rozdzielonych średnikami, które mają być ignorowane w pliku projektu. Dołączanie kodów ostrzeżeń do `$(NoWarn);` zastosowania wartości [domyślnych języka C#.](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16)

```xml
<NoWarn>$(NoWarn);1591</NoWarn>
```

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

W poprzednim kodzie [Odbicie](/dotnet/csharp/programming-guide/concepts/reflection) służy do tworzenia nazwy pliku XML odpowiadającej nazwie projektu internetowego interfejsu API. [Właściwość AppContext.BaseDirectory](/dotnet/api/system.appcontext.basedirectory) służy do konstruowania ścieżki do pliku XML. Niektóre funkcje Swagger (na przykład schemat parametrów wejściowych lub metody HTTP i kody odpowiedzi z odpowiednich atrybutów) działają bez użycia pliku dokumentacji XML. W przypadku większości funkcji, a mianowicie podsumowań metod i opisów parametrów i kodów odpowiedzi, użycie pliku XML jest obowiązkowe.

Dodawanie do akcji komentarzy z potrójnym ukośnikiem ulepsza wyniki narzędzia Swagger UI przez dodanie opisu do nagłówka sekcji. Dodaj [ \<element podsumowania>](/dotnet/csharp/programming-guide/xmldoc/summary) nad `Delete` akcją:

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
Interfejs użytkownika Swagger wyświetla wewnętrzny tekst `<summary>` elementu poprzedniego kodu:

![Interfejs użytkownika Swagger z komentarzem XML "Usuwa określony Element TodoItem". dla metody DELETE](sample_images/triple-slash-comments.png)

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
Zwróć uwagę na ulepszenia interfejsu użytkownika z tymi dodatkowymi komentarzami:

![Interfejs użytkownika Swagger z dodatkowymi komentarzami](sample_images/xml-comments-extended.png)

### <a name="data-annotations"></a>Adnotacje danych

Oznacz model atrybutami znajdującymi się w obszarze nazw [System.ComponentModel.DataAnnotations,](/dotnet/api/system.componentmodel.dataannotations) aby ułatwić prowadzenie składników interfejsu użytkownika Swagger.

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

```csharp
[Produces("application/json")]
[Route("api/[controller]")]
[ApiController]
public class TodoController : ControllerBase
{
    private readonly TodoContext _context;
```
Z listy rozwijanej **Typ zawartości odpowiedzi** wybiera ten typ zawartości jako domyślny dla akcji GET kontrolera:

![Interfejs użytkownika swagger z domyślnym typem zawartości odpowiedzi](sample_images/json-response-content-type.png)

Wraz ze wzrostem użycia adnotacji danych w interfejsie API sieci Web strony pomocy interfejsu użytkownika i interfejsu API stają się bardziej opisowe i przydatne.

### <a name="describe-response-types"></a>Opis typów odpowiedzi

Deweloperzy zużywający internetowy interfejs API są najbardziej&mdash;zaniepokojeni zwracanymi typami odpowiedzi i kodami błędów (jeśli nie standardowymi). Typy odpowiedzi i kody błędów są oznaczone w komentarzach XML i adnotacjach danych.

Akcja `Create` zwraca kod stanu HTTP 201 po sukcesie. Kod stanu HTTP 400 jest zwracany, gdy treść zaksięgowanego żądania ma wartość null. Bez odpowiedniej dokumentacji w interfejsie użytkownika Swagger konsument nie zna tych oczekiwanych wyników. Rozwiązać ten problem, dodając wyróżnione wiersze w poniższym przykładzie:

```csharp
/// <returns>A newly created TodoItem</returns>
/// <response code="201">Returns the newly created item</response>
/// <response code="400">If the item is null</response>            
[HttpPost]
[ProducesResponseType(StatusCodes.Status201Created)]
[ProducesResponseType(StatusCodes.Status400BadRequest)]
public ActionResult<TodoItem> Create(TodoItem item)
```

Interfejs użytkownika Swagger teraz wyraźnie dokumentuje oczekiwane kody odpowiedzi HTTP:

![Interfejs użytkownika swagger pokazujący opis klasy odpowiedzi POST "Zwraca nowo utworzony element Todo" i "400 — jeśli element ma wartość null" dla kodu stanu i przyczyny w obszarze Wiadomości odpowiedzi](sample_images/data-annotations-response-types.png)

W ASP.NET Core 2.2 lub nowszym konwencje mogą być używane jako `[ProducesResponseType]`alternatywa dla jawnego dekorowania poszczególnych działań za pomocą programu . Aby uzyskać więcej informacji, zobacz [Korzystanie z konwencji interfejsu API sieci Web](https://docs.microsoft.com/aspnet/core/web-api/advanced/conventions).

Aby uzyskać informacje na temat dostosowywania interfejsu użytkownika, zobacz: [Dostosowywanie interfejsu użytkownika](/aspnet/core/tutorials/getting-started-with-swashbuckle?#customize-and-extend)
