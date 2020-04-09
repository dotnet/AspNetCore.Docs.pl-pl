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
# <a name="get-started-with-nswag-and-aspnet-core"></a><span data-ttu-id="0ede7-103">Wprowadzenie do NSwag i ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0ede7-103">Get started with NSwag and ASP.NET Core</span></span>

<span data-ttu-id="0ede7-104">[Christoph Nienaber](https://twitter.com/zuckerthoben), [Rico Suter](https://rsuter.com)i [Dave Brock](https://twitter.com/daveabrock)</span><span class="sxs-lookup"><span data-stu-id="0ede7-104">By [Christoph Nienaber](https://twitter.com/zuckerthoben), [Rico Suter](https://rsuter.com), and [Dave Brock](https://twitter.com/daveabrock)</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="0ede7-105">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0ede7-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="0ede7-106">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0ede7-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker-end

<span data-ttu-id="0ede7-107">NSwag oferuje następujące możliwości:</span><span class="sxs-lookup"><span data-stu-id="0ede7-107">NSwag offers the following capabilities:</span></span>

* <span data-ttu-id="0ede7-108">Możliwość wykorzystania Swagger UI i generator Swagger.</span><span class="sxs-lookup"><span data-stu-id="0ede7-108">The ability to utilize the Swagger UI and Swagger generator.</span></span>
* <span data-ttu-id="0ede7-109">Elastyczne możliwości generowania kodu.</span><span class="sxs-lookup"><span data-stu-id="0ede7-109">Flexible code generation capabilities.</span></span>

<span data-ttu-id="0ede7-110">Z NSwag, nie trzeba istniejącego&mdash;interfejsu API można użyć interfejsów API innych firm, które zawierają Swagger i generowania implementacji klienta.</span><span class="sxs-lookup"><span data-stu-id="0ede7-110">With NSwag, you don't need an existing API&mdash;you can use third-party APIs that incorporate Swagger and generate a client implementation.</span></span> <span data-ttu-id="0ede7-111">NSwag pozwala przyspieszyć cykl rozwoju i łatwo dostosować się do zmian API.</span><span class="sxs-lookup"><span data-stu-id="0ede7-111">NSwag allows you to expedite the development cycle and easily adapt to API changes.</span></span>

## <a name="register-the-nswag-middleware"></a><span data-ttu-id="0ede7-112">Zarejestruj oprogramowanie pośredniczące NSwag</span><span class="sxs-lookup"><span data-stu-id="0ede7-112">Register the NSwag middleware</span></span>

<span data-ttu-id="0ede7-113">Zarejestruj oprogramowanie pośredniczące NSwag w:</span><span class="sxs-lookup"><span data-stu-id="0ede7-113">Register the NSwag middleware to:</span></span>

* <span data-ttu-id="0ede7-114">Wygeneruj specyfikację Swagger dla zaimplementowanego interfejsu API sieci web.</span><span class="sxs-lookup"><span data-stu-id="0ede7-114">Generate the Swagger specification for the implemented web API.</span></span>
* <span data-ttu-id="0ede7-115">Służy interfejsu użytkownika Swagger do przeglądania i testowania interfejsu API sieci web.</span><span class="sxs-lookup"><span data-stu-id="0ede7-115">Serve the Swagger UI to browse and test the web API.</span></span>

<span data-ttu-id="0ede7-116">Aby użyć oprogramowania pośredniczącego [NSwag](https://github.com/RicoSuter/NSwag) ASP.NET Core, zainstaluj pakiet [NSwag.AspNetCore](https://www.nuget.org/packages/NSwag.AspNetCore/) NuGet.</span><span class="sxs-lookup"><span data-stu-id="0ede7-116">To use the [NSwag](https://github.com/RicoSuter/NSwag) ASP.NET Core middleware, install the [NSwag.AspNetCore](https://www.nuget.org/packages/NSwag.AspNetCore/) NuGet package.</span></span> <span data-ttu-id="0ede7-117">Ten pakiet zawiera oprogramowanie pośredniczące do generowania i obsługi specyfikacji Swagger, Swagger UI (v2 i v3) i [Interfejsu Użytkownika ReDoc](https://github.com/Rebilly/ReDoc).</span><span class="sxs-lookup"><span data-stu-id="0ede7-117">This package contains the middleware to generate and serve the Swagger specification, Swagger UI (v2 and v3), and [ReDoc UI](https://github.com/Rebilly/ReDoc).</span></span>

<span data-ttu-id="0ede7-118">Użyj jednego z następujących podejść, aby zainstalować pakiet NSwag NuGet:</span><span class="sxs-lookup"><span data-stu-id="0ede7-118">Use one of the following approaches to install the NSwag NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0ede7-119">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0ede7-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0ede7-120">W oknie **Konsoli Menedżera pakietów:**</span><span class="sxs-lookup"><span data-stu-id="0ede7-120">From the **Package Manager Console** window:</span></span>
  * <span data-ttu-id="0ede7-121">Przejdź do **zobacz** > inną**konsolę Menedżera pakietów** **systemu Windows** > </span><span class="sxs-lookup"><span data-stu-id="0ede7-121">Go to **View** > **Other Windows** > **Package Manager Console**</span></span>
  * <span data-ttu-id="0ede7-122">Przejdź do katalogu, w którym istnieje plik *TodoApi.csproj*</span><span class="sxs-lookup"><span data-stu-id="0ede7-122">Navigate to the directory in which the *TodoApi.csproj* file exists</span></span>
  * <span data-ttu-id="0ede7-123">Wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="0ede7-123">Execute the following command:</span></span>

    ```powershell
    Install-Package NSwag.AspNetCore
    ```

* <span data-ttu-id="0ede7-124">W oknie dialogowym **Zarządzanie pakietami NuGet:**</span><span class="sxs-lookup"><span data-stu-id="0ede7-124">From the **Manage NuGet Packages** dialog:</span></span>
  * <span data-ttu-id="0ede7-125">Kliknij prawym przyciskiem myszy projekt w **Eksploratorze** > rozwiązań**Zarządzaj pakietami NuGet**</span><span class="sxs-lookup"><span data-stu-id="0ede7-125">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
  * <span data-ttu-id="0ede7-126">Ustaw **źródło pakietu** na "nuget.org"</span><span class="sxs-lookup"><span data-stu-id="0ede7-126">Set the **Package source** to "nuget.org"</span></span>
  * <span data-ttu-id="0ede7-127">Wpisz "NSwag.AspNetCore" w polu wyszukiwania</span><span class="sxs-lookup"><span data-stu-id="0ede7-127">Enter "NSwag.AspNetCore" in the search box</span></span>
  * <span data-ttu-id="0ede7-128">Wybierz pakiet "NSwag.AspNetCore" z zakładki **Przeglądaj** i kliknij przycisk **Zainstaluj**</span><span class="sxs-lookup"><span data-stu-id="0ede7-128">Select the "NSwag.AspNetCore" package from the **Browse** tab and click **Install**</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0ede7-129">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="0ede7-129">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0ede7-130">Kliknij prawym przyciskiem myszy folder *Pakiety* w **panelu rozrachowy** > **Dodaj pakiety...**</span><span class="sxs-lookup"><span data-stu-id="0ede7-130">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**</span></span>
* <span data-ttu-id="0ede7-131">Ustaw okno **Dodawanie pakietów** **w oknie Źródło** listy rozwijanej na "nuget.org"</span><span class="sxs-lookup"><span data-stu-id="0ede7-131">Set the **Add Packages** window's **Source** drop-down to "nuget.org"</span></span>
* <span data-ttu-id="0ede7-132">Wpisz "NSwag.AspNetCore" w polu wyszukiwania</span><span class="sxs-lookup"><span data-stu-id="0ede7-132">Enter "NSwag.AspNetCore" in the search box</span></span>
* <span data-ttu-id="0ede7-133">Wybierz pakiet "NSwag.AspNetCore" z okienka wyników i kliknij przycisk **Dodaj pakiet**</span><span class="sxs-lookup"><span data-stu-id="0ede7-133">Select the "NSwag.AspNetCore" package from the results pane and click **Add Package**</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="0ede7-134">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="0ede7-134">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="0ede7-135">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="0ede7-135">Run the following command:</span></span>

```dotnetcli
dotnet add TodoApi.csproj package NSwag.AspNetCore
```

---

## <a name="add-and-configure-swagger-middleware"></a><span data-ttu-id="0ede7-136">Dodawanie i konfigurowanie oprogramowania pośredniczącego Swagger</span><span class="sxs-lookup"><span data-stu-id="0ede7-136">Add and configure Swagger middleware</span></span>

<span data-ttu-id="0ede7-137">Dodaj i skonfiguruj Swagger w aplikacji ASP.NET Core, wykonując następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="0ede7-137">Add and configure Swagger in your ASP.NET Core app by performing the following steps:</span></span>

* <span data-ttu-id="0ede7-138">W `Startup.ConfigureServices` metodzie zarejestruj wymagane usługi Swagger:</span><span class="sxs-lookup"><span data-stu-id="0ede7-138">In the `Startup.ConfigureServices` method, register the required Swagger services:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

* <span data-ttu-id="0ede7-139">W `Startup.Configure` metodzie włącz oprogramowanie pośredniczące do obsługi wygenerowanej specyfikacji Swagger i interfejsu użytkownika Swagger:</span><span class="sxs-lookup"><span data-stu-id="0ede7-139">In the `Startup.Configure` method, enable the middleware for serving the generated Swagger specification and the Swagger UI:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_Configure&highlight=6-7)]

* <span data-ttu-id="0ede7-140">Uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="0ede7-140">Launch the app.</span></span> <span data-ttu-id="0ede7-141">Przejdź do:</span><span class="sxs-lookup"><span data-stu-id="0ede7-141">Navigate to:</span></span>
  * <span data-ttu-id="0ede7-142">`http://localhost:<port>/swagger`, aby wyświetlić interfejs użytkownika swaggera.</span><span class="sxs-lookup"><span data-stu-id="0ede7-142">`http://localhost:<port>/swagger` to view the Swagger UI.</span></span>
  * <span data-ttu-id="0ede7-143">`http://localhost:<port>/swagger/v1/swagger.json`, aby wyświetlić specyfikację Swagger.</span><span class="sxs-lookup"><span data-stu-id="0ede7-143">`http://localhost:<port>/swagger/v1/swagger.json` to view the Swagger specification.</span></span>

## <a name="code-generation"></a><span data-ttu-id="0ede7-144">Generowanie kodu</span><span class="sxs-lookup"><span data-stu-id="0ede7-144">Code generation</span></span>

<span data-ttu-id="0ede7-145">Możesz skorzystać z możliwości generowania kodu NSwag, wybierając jedną z następujących opcji:</span><span class="sxs-lookup"><span data-stu-id="0ede7-145">You can take advantage of NSwag's code generation capabilities by choosing one of the following options:</span></span>

* <span data-ttu-id="0ede7-146">[NSwagStudio](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio) &ndash; aplikacji klasycznej systemu Windows do generowania kodu klienta interfejsu API w języku C# lub TypeScript.</span><span class="sxs-lookup"><span data-stu-id="0ede7-146">[NSwagStudio](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio) &ndash; a Windows desktop app for generating API client code in C# or TypeScript.</span></span>
* <span data-ttu-id="0ede7-147">[Pakiety NSwag.CodeGeneration.CSharp](https://www.nuget.org/packages/NSwag.CodeGeneration.CSharp/) lub [NSwag.CodeGeneration.TypeScript](https://www.nuget.org/packages/NSwag.CodeGeneration.TypeScript/) NuGet dla generowania kodu wewnątrz projektu.</span><span class="sxs-lookup"><span data-stu-id="0ede7-147">The [NSwag.CodeGeneration.CSharp](https://www.nuget.org/packages/NSwag.CodeGeneration.CSharp/) or [NSwag.CodeGeneration.TypeScript](https://www.nuget.org/packages/NSwag.CodeGeneration.TypeScript/) NuGet packages for code generation inside your project.</span></span>
* <span data-ttu-id="0ede7-148">NSwag z [wiersza polecenia](https://github.com/RicoSuter/NSwag/wiki/CommandLine).</span><span class="sxs-lookup"><span data-stu-id="0ede7-148">NSwag from the [command line](https://github.com/RicoSuter/NSwag/wiki/CommandLine).</span></span>
* <span data-ttu-id="0ede7-149">Pakiet [NSwag.MSBuild](https://github.com/RicoSuter/NSwag/wiki/NSwag.MSBuild) NuGet.</span><span class="sxs-lookup"><span data-stu-id="0ede7-149">The [NSwag.MSBuild](https://github.com/RicoSuter/NSwag/wiki/NSwag.MSBuild) NuGet package.</span></span>
* <span data-ttu-id="0ede7-150">[Odszypuł openapi (Swagger) Connected Service](https://marketplace.visualstudio.com/items?itemName=Unchase.unchaseopenapiconnectedservice) &ndash; visual studio connected service do generowania kodu klienta interfejsu API w języku C# lub TypeScript.</span><span class="sxs-lookup"><span data-stu-id="0ede7-150">The [Unchase OpenAPI (Swagger) Connected Service](https://marketplace.visualstudio.com/items?itemName=Unchase.unchaseopenapiconnectedservice) &ndash; a Visual Studio Connected Service for generating API client code in C# or TypeScript.</span></span> <span data-ttu-id="0ede7-151">Generuje również kontrolery języka C# dla usług OpenAPI z NSwag.</span><span class="sxs-lookup"><span data-stu-id="0ede7-151">Also generates C# controllers for OpenAPI services with NSwag.</span></span>

### <a name="generate-code-with-nswagstudio"></a><span data-ttu-id="0ede7-152">Generowanie kodu za pomocą NSwagStudio</span><span class="sxs-lookup"><span data-stu-id="0ede7-152">Generate code with NSwagStudio</span></span>

* <span data-ttu-id="0ede7-153">Zainstaluj NSwagStudio, postępując zgodnie z instrukcjami zawartymi w [repozytorium NSwagStudio GitHub](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio).</span><span class="sxs-lookup"><span data-stu-id="0ede7-153">Install NSwagStudio by following the instructions at the [NSwagStudio GitHub repository](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio).</span></span> <span data-ttu-id="0ede7-154">Na stronie wydania NSwag można pobrać wersję xcopy, którą można uruchomić bez uprawnień instalacyjnych i administracyjnych.</span><span class="sxs-lookup"><span data-stu-id="0ede7-154">On the NSwag release page you can download an xcopy version which can be started without installation and admin privileges.</span></span>
* <span data-ttu-id="0ede7-155">Uruchom NSwagStudio i wprowadź adres URL pliku *swagger.json* w polu tekstowym **URL specyfikacji Swaggera.**</span><span class="sxs-lookup"><span data-stu-id="0ede7-155">Launch NSwagStudio and enter the *swagger.json* file URL in the **Swagger Specification URL** text box.</span></span> <span data-ttu-id="0ede7-156">Na przykład *http://localhost:44354/swagger/v1/swagger.json*.</span><span class="sxs-lookup"><span data-stu-id="0ede7-156">For example, *http://localhost:44354/swagger/v1/swagger.json*.</span></span>
* <span data-ttu-id="0ede7-157">Kliknij przycisk **Utwórz kopię lokalną,** aby wygenerować reprezentację JSON specyfikacji Swagger.</span><span class="sxs-lookup"><span data-stu-id="0ede7-157">Click the **Create local Copy** button to generate a JSON representation of your Swagger specification.</span></span>

  ![Tworzenie lokalnej kopii specyfikacji Swagger](web-api-help-pages-using-swagger/_static/CreateLocalCopy-NSwagStudio.PNG)

* <span data-ttu-id="0ede7-159">W obszarze **Dane wyjściowe** kliknij pole wyboru **CSharp Client (Klient CSharp).**</span><span class="sxs-lookup"><span data-stu-id="0ede7-159">In the **Outputs** area, click the **CSharp Client** check box.</span></span> <span data-ttu-id="0ede7-160">W zależności od projektu można również wybrać **klienta TypeScript** lub **kontrolera interfejsu API sieci Web CSharp**.</span><span class="sxs-lookup"><span data-stu-id="0ede7-160">Depending on your project, you can also choose **TypeScript Client** or **CSharp Web API Controller**.</span></span> <span data-ttu-id="0ede7-161">W przypadku **wybrania cSharp Web API Controller**specyfikacja usługi odbudowuje usługę, służąc jako odwrotnej generacji.</span><span class="sxs-lookup"><span data-stu-id="0ede7-161">If you select **CSharp Web API Controller**, a service specification rebuilds the service, serving as a reverse generation.</span></span>
* <span data-ttu-id="0ede7-162">Kliknij **przycisk Generuj dane wyjściowe,** aby utworzyć pełną implementację klienta języka C# projektu *TodoApi.NSwag.*</span><span class="sxs-lookup"><span data-stu-id="0ede7-162">Click **Generate Outputs** to produce a complete C# client implementation of the *TodoApi.NSwag* project.</span></span> <span data-ttu-id="0ede7-163">Aby wyświetlić wygenerowany kod klienta, kliknij kartę **CSharp Client:**</span><span class="sxs-lookup"><span data-stu-id="0ede7-163">To see the generated client code, click the **CSharp Client** tab:</span></span>

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
> <span data-ttu-id="0ede7-164">Kod klienta języka C# jest generowany na podstawie wyborów na karcie **Ustawienia.**</span><span class="sxs-lookup"><span data-stu-id="0ede7-164">The C# client code is generated based on selections in the **Settings** tab. Modify the settings to perform tasks such as default namespace renaming and synchronous method generation.</span></span>

* <span data-ttu-id="0ede7-165">Skopiuj wygenerowany kod Języka C# do pliku w projekcie klienta, który będzie zużywał interfejs API.</span><span class="sxs-lookup"><span data-stu-id="0ede7-165">Copy the generated C# code into a file in the client project that will consume the API.</span></span>
* <span data-ttu-id="0ede7-166">Zacznij ciemiężyć internetowy interfejs API:</span><span class="sxs-lookup"><span data-stu-id="0ede7-166">Start consuming the web API:</span></span>

```csharp
 var todoClient = new TodoClient();

// Gets all to-dos from the API
 var allTodos = await todoClient.GetAllAsync();

 // Create a new TodoItem, and save it via the API.
var createdTodo = await todoClient.CreateAsync(new TodoItem());

// Get a single to-do by ID
var foundTodo = await todoClient.GetByIdAsync(1);
```

## <a name="customize-api-documentation"></a><span data-ttu-id="0ede7-167">Dostosowywanie dokumentacji interfejsu API</span><span class="sxs-lookup"><span data-stu-id="0ede7-167">Customize API documentation</span></span>

<span data-ttu-id="0ede7-168">Swagger udostępnia opcje dokumentowania modelu obiektów w celu ułatwienia zużycia internetowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="0ede7-168">Swagger provides options for documenting the object model to ease consumption of the web API.</span></span>

### <a name="api-info-and-description"></a><span data-ttu-id="0ede7-169">Informacje i opis interfejsu API</span><span class="sxs-lookup"><span data-stu-id="0ede7-169">API info and description</span></span>

<span data-ttu-id="0ede7-170">W `Startup.ConfigureServices` metodzie akcja konfiguracji przekazana `AddSwaggerDocument` do metody dodaje informacje, takie jak autor, licencja i opis:</span><span class="sxs-lookup"><span data-stu-id="0ede7-170">In the `Startup.ConfigureServices` method, a configuration action passed to the `AddSwaggerDocument` method adds information such as the author, license, and description:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup2.cs?name=snippet_AddSwaggerDocument)]

<span data-ttu-id="0ede7-171">Interfejs użytkownika Swagger wyświetla informacje o wersji:</span><span class="sxs-lookup"><span data-stu-id="0ede7-171">The Swagger UI displays the version's information:</span></span>

![Interfejs użytkownika Swagger z informacjami o wersji](web-api-help-pages-using-swagger/_static/custom-info-nswag.png)

### <a name="xml-comments"></a><span data-ttu-id="0ede7-173">komentarze XML</span><span class="sxs-lookup"><span data-stu-id="0ede7-173">XML comments</span></span>

<span data-ttu-id="0ede7-174">Aby włączyć komentarze XML, wykonaj następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="0ede7-174">To enable XML comments, perform the following steps:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0ede7-175">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0ede7-175">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="0ede7-176">Kliknij prawym przyciskiem myszy projekt w **Eksploratorze rozwiązań** i wybierz polecenie **Edytuj <project_name>.csproj**.</span><span class="sxs-lookup"><span data-stu-id="0ede7-176">Right-click the project in **Solution Explorer** and select **Edit <project_name>.csproj**.</span></span>
* <span data-ttu-id="0ede7-177">Ręcznie dodaj wyróżnione wiersze do pliku *csproj:*</span><span class="sxs-lookup"><span data-stu-id="0ede7-177">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="0ede7-178">Kliknij prawym przyciskiem myszy projekt w **Eksploratorze rozwiązań** i wybierz **polecenie Właściwości**</span><span class="sxs-lookup"><span data-stu-id="0ede7-178">Right-click the project in **Solution Explorer** and select **Properties**</span></span>
* <span data-ttu-id="0ede7-179">Zaznacz pole **pliku dokumentacji XML** w sekcji **Dane wyjściowe** na karcie **Kompilacja**</span><span class="sxs-lookup"><span data-stu-id="0ede7-179">Check the **XML documentation file** box under the **Output** section of the **Build** tab</span></span>

::: moniker-end

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0ede7-180">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="0ede7-180">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="0ede7-181">W *panelu rozrachówek*naciśnij **kontrolkę** i kliknij nazwę projektu.</span><span class="sxs-lookup"><span data-stu-id="0ede7-181">From the *Solution Pad*, press **control** and click the project name.</span></span> <span data-ttu-id="0ede7-182">Przejdź do **pozycji Narzędzia** > **edycji pliku**.</span><span class="sxs-lookup"><span data-stu-id="0ede7-182">Navigate to **Tools** > **Edit File**.</span></span>
* <span data-ttu-id="0ede7-183">Ręcznie dodaj wyróżnione wiersze do pliku *csproj:*</span><span class="sxs-lookup"><span data-stu-id="0ede7-183">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="0ede7-184">Otwórz okno dialogowe **Opcje projektu** > **kompilator** **kompilacji** ></span><span class="sxs-lookup"><span data-stu-id="0ede7-184">Open the **Project Options** dialog > **Build** > **Compiler**</span></span>
* <span data-ttu-id="0ede7-185">Zaznacz pole **Wyboru Generuj dokumentację xml** w sekcji **Opcje ogólne**</span><span class="sxs-lookup"><span data-stu-id="0ede7-185">Check the **Generate xml documentation** box under the **General Options** section</span></span>

::: moniker-end

# <a name="net-core-cli"></a>[<span data-ttu-id="0ede7-186">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="0ede7-186">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="0ede7-187">Ręcznie dodaj wyróżnione wiersze do pliku *csproj:*</span><span class="sxs-lookup"><span data-stu-id="0ede7-187">Manually add the highlighted lines to the *.csproj* file:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

---

### <a name="data-annotations"></a><span data-ttu-id="0ede7-188">Adnotacje danych</span><span class="sxs-lookup"><span data-stu-id="0ede7-188">Data annotations</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="0ede7-189">Ponieważ NSwag używa [odbicia](/dotnet/csharp/programming-guide/concepts/reflection), a zalecanym typem zwracania dla akcji interfejsu API sieci web jest [IActionResult](xref:Microsoft.AspNetCore.Mvc.IActionResult), nie można wywnioskować, co robi akcja i co zwraca.</span><span class="sxs-lookup"><span data-stu-id="0ede7-189">Because NSwag uses [Reflection](/dotnet/csharp/programming-guide/concepts/reflection), and the recommended return type for web API actions is [IActionResult](xref:Microsoft.AspNetCore.Mvc.IActionResult), it can't infer what your action is doing and what it returns.</span></span>

<span data-ttu-id="0ede7-190">Rozważmy następujący przykład:</span><span class="sxs-lookup"><span data-stu-id="0ede7-190">Consider the following example:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

<span data-ttu-id="0ede7-191">Poprzednia akcja `IActionResult`zwraca , ale wewnątrz akcji jest zwracany albo [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*) lub [BadRequest](xref:System.Web.Http.ApiController.BadRequest*).</span><span class="sxs-lookup"><span data-stu-id="0ede7-191">The preceding action returns `IActionResult`, but inside the action it's returning either [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*) or [BadRequest](xref:System.Web.Http.ApiController.BadRequest*).</span></span> <span data-ttu-id="0ede7-192">Adnotacje danych za pomocą adnotacji danych informują klientów, które kody stanu HTTP są znane jako zwracane przez tę akcję.</span><span class="sxs-lookup"><span data-stu-id="0ede7-192">Use data annotations to tell clients which HTTP status codes this action is known to return.</span></span> <span data-ttu-id="0ede7-193">Oznacz akcję następującymi atrybutami:</span><span class="sxs-lookup"><span data-stu-id="0ede7-193">Mark the action with the following attributes:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="0ede7-194">Ponieważ NSwag używa [odbicia,](/dotnet/csharp/programming-guide/concepts/reflection)a zalecanym typem zwracanym dla akcji web API jest [ActionResult\<T>, ](xref:Microsoft.AspNetCore.Mvc.ActionResult%601)można wywnioskować tylko typ zwracany zdefiniowany przez `T`program .</span><span class="sxs-lookup"><span data-stu-id="0ede7-194">Because NSwag uses [Reflection](/dotnet/csharp/programming-guide/concepts/reflection), and the recommended return type for web API actions is [ActionResult\<T>](xref:Microsoft.AspNetCore.Mvc.ActionResult%601), it can only infer the return type defined by `T`.</span></span> <span data-ttu-id="0ede7-195">Nie można automatycznie wywnioskować innych możliwych typów zwrotów.</span><span class="sxs-lookup"><span data-stu-id="0ede7-195">You can't automatically infer other possible return types.</span></span>

<span data-ttu-id="0ede7-196">Rozważmy następujący przykład:</span><span class="sxs-lookup"><span data-stu-id="0ede7-196">Consider the following example:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

<span data-ttu-id="0ede7-197">Poprzednia akcja `ActionResult<T>`zwraca .</span><span class="sxs-lookup"><span data-stu-id="0ede7-197">The preceding action returns `ActionResult<T>`.</span></span> <span data-ttu-id="0ede7-198">Wewnątrz akcji jest powrót [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*).</span><span class="sxs-lookup"><span data-stu-id="0ede7-198">Inside the action, it's returning [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*).</span></span> <span data-ttu-id="0ede7-199">Ponieważ kontroler ma [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atrybut, [BadRequest](xref:System.Web.Http.ApiController.BadRequest*) odpowiedzi jest możliwe, zbyt.</span><span class="sxs-lookup"><span data-stu-id="0ede7-199">Since the controller has the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute, a [BadRequest](xref:System.Web.Http.ApiController.BadRequest*) response is possible, too.</span></span> <span data-ttu-id="0ede7-200">Aby uzyskać więcej informacji, zobacz [Automatyczne odpowiedzi HTTP 400](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="0ede7-200">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span> <span data-ttu-id="0ede7-201">Adnotacje danych za pomocą adnotacji danych informują klientów, które kody stanu HTTP są znane jako zwracane przez tę akcję.</span><span class="sxs-lookup"><span data-stu-id="0ede7-201">Use data annotations to tell clients which HTTP status codes this action is known to return.</span></span> <span data-ttu-id="0ede7-202">Oznacz akcję następującymi atrybutami:</span><span class="sxs-lookup"><span data-stu-id="0ede7-202">Mark the action with the following attributes:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

<span data-ttu-id="0ede7-203">W ASP.NET Core 2.2 lub nowszym można używać konwencji zamiast jawnie `[ProducesResponseType]`dekorować poszczególne akcje za pomocą programu .</span><span class="sxs-lookup"><span data-stu-id="0ede7-203">In ASP.NET Core 2.2 or later, you can use conventions instead of explicitly decorating individual actions with `[ProducesResponseType]`.</span></span> <span data-ttu-id="0ede7-204">Aby uzyskać więcej informacji, zobacz <xref:web-api/advanced/conventions>.</span><span class="sxs-lookup"><span data-stu-id="0ede7-204">For more information, see <xref:web-api/advanced/conventions>.</span></span>

::: moniker-end

<span data-ttu-id="0ede7-205">Generator Swagger może teraz dokładnie opisać tę akcję i wygenerowanych klientów wiedzieć, co otrzymują podczas wywoływania punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="0ede7-205">The Swagger generator can now accurately describe this action, and generated clients know what they receive when calling the endpoint.</span></span> <span data-ttu-id="0ede7-206">Jako zalecenie zaznacz wszystkie akcje tymi atrybutami.</span><span class="sxs-lookup"><span data-stu-id="0ede7-206">As a recommendation, mark all actions with these attributes.</span></span>

<span data-ttu-id="0ede7-207">Aby uzyskać wskazówki dotyczące odpowiedzi HTTP, które akcje interfejsu API powinny zwracać, zobacz [specyfikację RFC 7231](https://tools.ietf.org/html/rfc7231#section-4.3).</span><span class="sxs-lookup"><span data-stu-id="0ede7-207">For guidelines on what HTTP responses your API actions should return, see the [RFC 7231 specification](https://tools.ietf.org/html/rfc7231#section-4.3).</span></span>
