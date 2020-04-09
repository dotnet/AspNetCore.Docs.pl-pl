---
title: Interfejs Razor wielokrotnego rozrządu wielokrotnego rozrządu w bibliotekach klas z ASP.NET Core
author: Rick-Anderson
description: W tym artykule wyjaśniono, jak utworzyć interfejs użytkownika razor wielokrotnego użycia przy użyciu widoków częściowych w bibliotece klas w ASP.NET Core.
ms.author: riande
ms.date: 01/25/2020
ms.custom: mvc, seodec18
uid: razor-pages/ui-class
ms.openlocfilehash: f24dc62eba345a8a3d35143805b4966cb51832fa
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667567"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a><span data-ttu-id="de8f7-103">Tworzenie interfejsu użytkownika wielokrotnego użycia przy użyciu projektu biblioteki klas Razor w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="de8f7-103">Create reusable UI using the Razor class library project in ASP.NET Core</span></span>

<span data-ttu-id="de8f7-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="de8f7-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="de8f7-105">Widoki maszynki do golenia, strony, kontrolery, modele stron, [komponenty Razor,](xref:blazor/class-libraries) [Wyświetl komponenty](xref:mvc/views/view-components)i modele danych mogą być wbudowane w bibliotekę klas Razor (RCL).</span><span class="sxs-lookup"><span data-stu-id="de8f7-105">Razor views, pages, controllers, page models, [Razor components](xref:blazor/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="de8f7-106">RCL mogą być pakowane i ponownie.</span><span class="sxs-lookup"><span data-stu-id="de8f7-106">The RCL can be packaged and reused.</span></span> <span data-ttu-id="de8f7-107">Aplikacje mogą zawierać rcl i zastąpić widoki i strony, które zawiera.</span><span class="sxs-lookup"><span data-stu-id="de8f7-107">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="de8f7-108">Gdy widok, widok częściowy lub razor Page znajduje się zarówno w aplikacji sieci web i RCL, znaczników Razor (plik*cshtml)* w aplikacji sieci web ma pierwszeństwo.</span><span class="sxs-lookup"><span data-stu-id="de8f7-108">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="de8f7-109">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="de8f7-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="de8f7-110">Tworzenie biblioteki klas zawierającej interfejs użytkownika razor</span><span class="sxs-lookup"><span data-stu-id="de8f7-110">Create a class library containing Razor UI</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="de8f7-111">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="de8f7-111">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="de8f7-112">Z programu Visual Studio wybierz pozycję **Utwórz nowy projekt**.</span><span class="sxs-lookup"><span data-stu-id="de8f7-112">From Visual Studio select **Create new a new project**.</span></span>
* <span data-ttu-id="de8f7-113">Wybierz **bibliotekę** > klas Razor **Next**.</span><span class="sxs-lookup"><span data-stu-id="de8f7-113">Select **Razor Class Library** > **Next**.</span></span>
* <span data-ttu-id="de8f7-114">Nazwij bibliotekę (na przykład "RazorClassLib"), > **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="de8f7-114">Name the library (for example, "RazorClassLib"), > **Create**.</span></span> <span data-ttu-id="de8f7-115">Aby uniknąć kolizji nazwy pliku z wygenerowaną biblioteką widoku, `.Views`upewnij się, że nazwa biblioteki nie kończy się na .</span><span class="sxs-lookup"><span data-stu-id="de8f7-115">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="de8f7-116">Wybierz **strony pomocy technicznej i widoki,** jeśli chcesz obsługiwać widoki.</span><span class="sxs-lookup"><span data-stu-id="de8f7-116">Select **Support pages and views** if you need to support views.</span></span> <span data-ttu-id="de8f7-117">Domyślnie obsługiwane są tylko strony Razor.</span><span class="sxs-lookup"><span data-stu-id="de8f7-117">By default, only Razor Pages are supported.</span></span> <span data-ttu-id="de8f7-118">Wybierz **pozycję Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="de8f7-118">Select **Create**.</span></span>

<span data-ttu-id="de8f7-119">Szablon biblioteki klas Razor (RCL) domyślnie domyślnie jest owy do tworzenia komponentów Razor.</span><span class="sxs-lookup"><span data-stu-id="de8f7-119">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="de8f7-120">**Opcja Obsługa stron i widoków** obsługuje strony i widoki.</span><span class="sxs-lookup"><span data-stu-id="de8f7-120">The **Support pages and views** option supports pages and views.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="de8f7-121">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="de8f7-121">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="de8f7-122">Z wiersza polecenia `dotnet new razorclasslib`uruchom polecenie .</span><span class="sxs-lookup"><span data-stu-id="de8f7-122">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="de8f7-123">Przykład:</span><span class="sxs-lookup"><span data-stu-id="de8f7-123">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="de8f7-124">Szablon biblioteki klas Razor (RCL) domyślnie domyślnie jest owy do tworzenia komponentów Razor.</span><span class="sxs-lookup"><span data-stu-id="de8f7-124">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="de8f7-125">Przekaż `--support-pages-and-views` opcję`dotnet new razorclasslib --support-pages-and-views`( ), aby zapewnić obsługę stron i widoków.</span><span class="sxs-lookup"><span data-stu-id="de8f7-125">Pass the `--support-pages-and-views` option (`dotnet new razorclasslib --support-pages-and-views`) to provide support for pages and views.</span></span>

<span data-ttu-id="de8f7-126">Aby uzyskać więcej informacji, zobacz [dotnet new](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="de8f7-126">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="de8f7-127">Aby uniknąć kolizji nazwy pliku z wygenerowaną biblioteką widoku, `.Views`upewnij się, że nazwa biblioteki nie kończy się na .</span><span class="sxs-lookup"><span data-stu-id="de8f7-127">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="de8f7-128">Dodaj pliki Razor do RCL.</span><span class="sxs-lookup"><span data-stu-id="de8f7-128">Add Razor files to the RCL.</span></span>

<span data-ttu-id="de8f7-129">Szablony ASP.NET Core zakładają, że zawartość RCL znajduje się w folderze *Obszary.*</span><span class="sxs-lookup"><span data-stu-id="de8f7-129">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="de8f7-130">Zobacz [układ strony RCL,](#rcl-pages-layout) aby utworzyć umowę `~/Pages` RCL, która udostępnia zawartość, a nie `~/Areas/Pages`.</span><span class="sxs-lookup"><span data-stu-id="de8f7-130">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="de8f7-131">Odwołuje się do zawartości RCL</span><span class="sxs-lookup"><span data-stu-id="de8f7-131">Reference RCL content</span></span>

<span data-ttu-id="de8f7-132">RCL może się odwoływać:</span><span class="sxs-lookup"><span data-stu-id="de8f7-132">The RCL can be referenced by:</span></span>

* <span data-ttu-id="de8f7-133">Pakiet NuGet.</span><span class="sxs-lookup"><span data-stu-id="de8f7-133">NuGet package.</span></span> <span data-ttu-id="de8f7-134">Zobacz [Tworzenie pakietów NuGet](/nuget/create-packages/creating-a-package) i [dotnet dodać pakiet](/dotnet/core/tools/dotnet-add-package) i tworzenie i [publikowanie pakietu NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="de8f7-134">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="de8f7-135">*{ProjectName}.csproj*.</span><span class="sxs-lookup"><span data-stu-id="de8f7-135">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="de8f7-136">Zobacz [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span><span class="sxs-lookup"><span data-stu-id="de8f7-136">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="de8f7-137">Zastępowanie widoków, widoków częściowych i stron</span><span class="sxs-lookup"><span data-stu-id="de8f7-137">Override views, partial views, and pages</span></span>

<span data-ttu-id="de8f7-138">Gdy widok, widok częściowy lub razor Page znajduje się zarówno w aplikacji sieci web i RCL, znaczników Razor (plik*cshtml)* w aplikacji sieci web ma pierwszeństwo.</span><span class="sxs-lookup"><span data-stu-id="de8f7-138">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="de8f7-139">Na przykład dodaj *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* do aplikacji WebApp1, a strona1 w aplikacji WebApp1 będzie mieć pierwszeństwo przed stroną1 w rcl.</span><span class="sxs-lookup"><span data-stu-id="de8f7-139">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="de8f7-140">W przykładowym pobieraniu zmień nazwę *webapp1/areas/MyFeature2* na *WebApp1/Areas/MyFeature,* aby przetestować pierwszeństwo.</span><span class="sxs-lookup"><span data-stu-id="de8f7-140">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="de8f7-141">Skopiuj widok *częściowy RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* do *aplikacji WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="de8f7-141">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="de8f7-142">Zaktualizuj znaczniki, aby wskazać nową lokalizację.</span><span class="sxs-lookup"><span data-stu-id="de8f7-142">Update the markup to indicate the new location.</span></span> <span data-ttu-id="de8f7-143">Skompiluj i uruchom aplikację, aby sprawdzić, czy używana jest wersja częściowa aplikacji.</span><span class="sxs-lookup"><span data-stu-id="de8f7-143">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="de8f7-144">Układ strony RCL</span><span class="sxs-lookup"><span data-stu-id="de8f7-144">RCL Pages layout</span></span>

<span data-ttu-id="de8f7-145">Aby odwoływać się do zawartości RCL tak, jakby była częścią folderu *Pages* aplikacji sieci web, utwórz projekt RCL z następującą strukturą plików:</span><span class="sxs-lookup"><span data-stu-id="de8f7-145">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="de8f7-146">*RazorUIClassLib/Strony*</span><span class="sxs-lookup"><span data-stu-id="de8f7-146">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="de8f7-147">*RazorUIClassLib/Pages/Udostępnione*</span><span class="sxs-lookup"><span data-stu-id="de8f7-147">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="de8f7-148">Załóżmy, że *RazorUIClassLib/Pages/Shared* zawiera dwa pliki częściowe: *_Header.cshtml* i *_Footer.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="de8f7-148">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="de8f7-149">Znaczniki `<partial>` można dodać do *pliku _Layout.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="de8f7-149">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

## <a name="create-an-rcl-with-static-assets"></a><span data-ttu-id="de8f7-150">Tworzenie listy RCL z zasobami statycznymi</span><span class="sxs-lookup"><span data-stu-id="de8f7-150">Create an RCL with static assets</span></span>

<span data-ttu-id="de8f7-151">Lista RCL może wymagać towarzyszących zasobów statycznych, do których można się odwoływać za pomocą listy RCL lub aplikacji zużywającej listy RCL.</span><span class="sxs-lookup"><span data-stu-id="de8f7-151">An RCL may require companion static assets that can be referenced by either the RCL or the consuming app of the RCL.</span></span> <span data-ttu-id="de8f7-152">ASP.NET Core umożliwia tworzenie list RL, które zawierają zasoby statyczne, które są dostępne dla aplikacji zużywającej.</span><span class="sxs-lookup"><span data-stu-id="de8f7-152">ASP.NET Core allows creating RCLs that include static assets that are available to a consuming app.</span></span>

<span data-ttu-id="de8f7-153">Aby uwzględnić zasoby towarzyszące jako część listy RCL, utwórz folder *wwwroot* w bibliotece klas i dołącz do tego folderu wszystkie wymagane pliki.</span><span class="sxs-lookup"><span data-stu-id="de8f7-153">To include companion assets as part of an RCL, create a *wwwroot* folder in the class library and include any required files in that folder.</span></span>

<span data-ttu-id="de8f7-154">Podczas pakowania listy RCL wszystkie zasoby towarzyszące w folderze *wwwroot* są automatycznie uwzględniane w pakiecie.</span><span class="sxs-lookup"><span data-stu-id="de8f7-154">When packing an RCL, all companion assets in the *wwwroot* folder are automatically included in the package.</span></span>

### <a name="exclude-static-assets"></a><span data-ttu-id="de8f7-155">Wykluczanie aktywów statycznych</span><span class="sxs-lookup"><span data-stu-id="de8f7-155">Exclude static assets</span></span>

<span data-ttu-id="de8f7-156">Aby wykluczyć zasoby statyczne, dodaj żądaną `$(DefaultItemExcludes)` ścieżkę wykluczenia do grupy właściwości w pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="de8f7-156">To exclude static assets, add the desired exclusion path to the `$(DefaultItemExcludes)` property group in the project file.</span></span> <span data-ttu-id="de8f7-157">Oddzielne wpisy ze średnikiem`;`( ).</span><span class="sxs-lookup"><span data-stu-id="de8f7-157">Separate entries with a semicolon (`;`).</span></span>

<span data-ttu-id="de8f7-158">W poniższym przykładzie arkusz *stylów lib.css* w folderze *wwwroot* nie jest uważany za zasób statyczny i nie jest uwzględniony w opublikowanej liście RCL:</span><span class="sxs-lookup"><span data-stu-id="de8f7-158">In the following example, the *lib.css* stylesheet in the *wwwroot* folder isn't considered a static asset and isn't included in the published RCL:</span></span>

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a><span data-ttu-id="de8f7-159">Integracja z typemscriptu</span><span class="sxs-lookup"><span data-stu-id="de8f7-159">Typescript integration</span></span>

<span data-ttu-id="de8f7-160">Aby uwzględnić pliki TypeScript w rcl:</span><span class="sxs-lookup"><span data-stu-id="de8f7-160">To include TypeScript files in an RCL:</span></span>

1. <span data-ttu-id="de8f7-161">Umieść pliki TypeScript *(ts)* poza folderem *wwwroot.*</span><span class="sxs-lookup"><span data-stu-id="de8f7-161">Place the TypeScript files (*.ts*) outside of the *wwwroot* folder.</span></span> <span data-ttu-id="de8f7-162">Na przykład umieść pliki w folderze *klient.*</span><span class="sxs-lookup"><span data-stu-id="de8f7-162">For example, place the files in a *Client* folder.</span></span>

1. <span data-ttu-id="de8f7-163">Skonfiguruj dane wyjściowe kompilacji TypeScript dla folderu *wwwroot.*</span><span class="sxs-lookup"><span data-stu-id="de8f7-163">Configure the TypeScript build output for the *wwwroot* folder.</span></span> <span data-ttu-id="de8f7-164">Ustaw `TypescriptOutDir` właściwość wewnątrz `PropertyGroup` pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="de8f7-164">Set the `TypescriptOutDir` property inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. <span data-ttu-id="de8f7-165">Uwzględnij cel TypeScript jako zależność `ResolveCurrentProjectStaticWebAssets` obiektu docelowego, dodając `PropertyGroup` w pliku projektu następujący obiekt docelowy:</span><span class="sxs-lookup"><span data-stu-id="de8f7-165">Include the TypeScript target as a dependency of the `ResolveCurrentProjectStaticWebAssets` target by adding the following target inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     CompileTypeScript;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a><span data-ttu-id="de8f7-166">Korzystanie z zawartości z listy RCL, do którego istnieje odwołanie</span><span class="sxs-lookup"><span data-stu-id="de8f7-166">Consume content from a referenced RCL</span></span>

<span data-ttu-id="de8f7-167">Pliki zawarte w folderze *wwwroot* rcl są narażone na rcl lub aplikacji zużywającej `_content/{LIBRARY NAME}/`pod prefiksem .</span><span class="sxs-lookup"><span data-stu-id="de8f7-167">The files included in the *wwwroot* folder of the RCL are exposed to either the RCL or the consuming app under the prefix `_content/{LIBRARY NAME}/`.</span></span> <span data-ttu-id="de8f7-168">Na przykład biblioteka o nazwie *Razor.Class.Lib* powoduje ścieżkę do zawartości statycznej w pliku `_content/Razor.Class.Lib/`.</span><span class="sxs-lookup"><span data-stu-id="de8f7-168">For example, a library named *Razor.Class.Lib* results in a path to static content at `_content/Razor.Class.Lib/`.</span></span> <span data-ttu-id="de8f7-169">Podczas tworzenia pakietu NuGet i nazwa zestawu nie jest taka sama jak identyfikator pakietu, należy użyć identyfikatora pakietu dla `{LIBRARY NAME}`.</span><span class="sxs-lookup"><span data-stu-id="de8f7-169">When producing a NuGet package and the assembly name isn't the same as the package ID, use the package ID for `{LIBRARY NAME}`.</span></span>

<span data-ttu-id="de8f7-170">Aplikacja zużywająca odwołuje się do zasobów statycznych `<script>` `<style>`dostarczanych przez bibliotekę za pomocą `<img>`, i innych tagów HTML.</span><span class="sxs-lookup"><span data-stu-id="de8f7-170">The consuming app references static assets provided by the library with `<script>`, `<style>`, `<img>`, and other HTML tags.</span></span> <span data-ttu-id="de8f7-171">Aplikacja zużywająca musi mieć włączoną `Startup.Configure`obsługę plików [statycznych](xref:fundamentals/static-files) w:</span><span class="sxs-lookup"><span data-stu-id="de8f7-171">The consuming app must have [static file support](xref:fundamentals/static-files) enabled in `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

<span data-ttu-id="de8f7-172">Podczas uruchamiania aplikacji zużywającej`dotnet run`z danych wyjściowych kompilacji ( ), statyczne zasoby sieci web są domyślnie włączone w środowisku programistycznym.</span><span class="sxs-lookup"><span data-stu-id="de8f7-172">When running the consuming app from build output (`dotnet run`), static web assets are enabled by default in the Development environment.</span></span> <span data-ttu-id="de8f7-173">Aby obsługiwać zasoby w innych środowiskach podczas `UseStaticWebAssets` uruchamiania z danych wyjściowych kompilacji, wywołaj konstruktora hosta w *Program.cs:*</span><span class="sxs-lookup"><span data-stu-id="de8f7-173">To support assets in other environments when running from build output, call `UseStaticWebAssets` on the host builder in *Program.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStaticWebAssets();
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="de8f7-174">Wywołanie `UseStaticWebAssets` nie jest wymagane podczas uruchamiania aplikacji`dotnet publish`z opublikowanych danych wyjściowych ( ).</span><span class="sxs-lookup"><span data-stu-id="de8f7-174">Calling `UseStaticWebAssets` isn't required when running an app from published output (`dotnet publish`).</span></span>

### <a name="multi-project-development-flow"></a><span data-ttu-id="de8f7-175">Przepływ rozwoju wielu projektów</span><span class="sxs-lookup"><span data-stu-id="de8f7-175">Multi-project development flow</span></span>

<span data-ttu-id="de8f7-176">Po uruchomieniu aplikacji zużywającej:</span><span class="sxs-lookup"><span data-stu-id="de8f7-176">When the consuming app runs:</span></span>

* <span data-ttu-id="de8f7-177">Zasoby w liście RCL pozostają w oryginalnych folderach.</span><span class="sxs-lookup"><span data-stu-id="de8f7-177">The assets in the RCL stay in their original folders.</span></span> <span data-ttu-id="de8f7-178">Zasoby nie są przenoszone do aplikacji zużywającej.</span><span class="sxs-lookup"><span data-stu-id="de8f7-178">The assets aren't moved to the consuming app.</span></span>
* <span data-ttu-id="de8f7-179">Wszelkie zmiany w folderze *wwwroot* listy RCL jest odzwierciedlona w aplikacji zużywającej po RCL jest przebudowywany i bez przebudowy aplikacji zużywających.</span><span class="sxs-lookup"><span data-stu-id="de8f7-179">Any change within the RCL's *wwwroot* folder is reflected in the consuming app after the RCL is rebuilt and without rebuilding the consuming app.</span></span>

<span data-ttu-id="de8f7-180">Po zbudowaniu listy RCL jest produkowany manifest opisujący statyczne lokalizacje zasobów sieci web.</span><span class="sxs-lookup"><span data-stu-id="de8f7-180">When the RCL is built, a manifest is produced that describes the static web asset locations.</span></span> <span data-ttu-id="de8f7-181">Aplikacja zużywająca odczytuje manifest w czasie wykonywania, aby korzystać z zasobów z projektów i pakietów, do których istnieje odwołanie.</span><span class="sxs-lookup"><span data-stu-id="de8f7-181">The consuming app reads the manifest at runtime to consume the assets from referenced projects and packages.</span></span> <span data-ttu-id="de8f7-182">Po dodaniu nowego zasobu do listy RCL listy RCL musi zostać przebudowany, aby zaktualizować jego manifest, zanim aplikacja zużywająca może uzyskać dostęp do nowego zasobu.</span><span class="sxs-lookup"><span data-stu-id="de8f7-182">When a new asset is added to an RCL, the RCL must be rebuilt to update its manifest before a consuming app can access the new asset.</span></span>

### <a name="publish"></a><span data-ttu-id="de8f7-183">Publikowanie</span><span class="sxs-lookup"><span data-stu-id="de8f7-183">Publish</span></span>

<span data-ttu-id="de8f7-184">Po opublikowaniu aplikacji zasoby towarzyszące ze wszystkich projektów i pakietów, do których istnieją odwołania, `_content/{LIBRARY NAME}/`są kopiowane do folderu *wwwroot* opublikowanej aplikacji w obszarze .</span><span class="sxs-lookup"><span data-stu-id="de8f7-184">When the app is published, the companion assets from all referenced projects and packages are copied into the *wwwroot* folder of the published app under `_content/{LIBRARY NAME}/`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="de8f7-185">Widoki maszynki do golenia, strony, kontrolery, modele stron, [komponenty Razor,](xref:blazor/class-libraries) [Wyświetl komponenty](xref:mvc/views/view-components)i modele danych mogą być wbudowane w bibliotekę klas Razor (RCL).</span><span class="sxs-lookup"><span data-stu-id="de8f7-185">Razor views, pages, controllers, page models, [Razor components](xref:blazor/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="de8f7-186">RCL mogą być pakowane i ponownie.</span><span class="sxs-lookup"><span data-stu-id="de8f7-186">The RCL can be packaged and reused.</span></span> <span data-ttu-id="de8f7-187">Aplikacje mogą zawierać rcl i zastąpić widoki i strony, które zawiera.</span><span class="sxs-lookup"><span data-stu-id="de8f7-187">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="de8f7-188">Gdy widok, widok częściowy lub razor Page znajduje się zarówno w aplikacji sieci web i RCL, znaczników Razor (plik*cshtml)* w aplikacji sieci web ma pierwszeństwo.</span><span class="sxs-lookup"><span data-stu-id="de8f7-188">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="de8f7-189">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="de8f7-189">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="de8f7-190">Tworzenie biblioteki klas zawierającej interfejs użytkownika razor</span><span class="sxs-lookup"><span data-stu-id="de8f7-190">Create a class library containing Razor UI</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="de8f7-191">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="de8f7-191">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="de8f7-192">Z menu **Plik** programu Visual Studio wybierz polecenie **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="de8f7-192">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="de8f7-193">Wybierz **ASP.NET podstawową aplikację sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="de8f7-193">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="de8f7-194">Nazwij bibliotekę (na przykład "RazorClassLib") > **OK**.</span><span class="sxs-lookup"><span data-stu-id="de8f7-194">Name the library (for example, "RazorClassLib") > **OK**.</span></span> <span data-ttu-id="de8f7-195">Aby uniknąć kolizji nazwy pliku z wygenerowaną biblioteką widoku, `.Views`upewnij się, że nazwa biblioteki nie kończy się na .</span><span class="sxs-lookup"><span data-stu-id="de8f7-195">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="de8f7-196">Sprawdź, **czy ASP.NET wybrano core 2.1** lub nowszą.</span><span class="sxs-lookup"><span data-stu-id="de8f7-196">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="de8f7-197">Wybierz opcję **Biblioteka** > klas Razor **OK**.</span><span class="sxs-lookup"><span data-stu-id="de8f7-197">Select **Razor Class Library** > **OK**.</span></span>

<span data-ttu-id="de8f7-198">RCL ma następujący plik projektu:</span><span class="sxs-lookup"><span data-stu-id="de8f7-198">An RCL has the following project file:</span></span>

[!code-xml[](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-cli"></a>[<span data-ttu-id="de8f7-199">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="de8f7-199">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="de8f7-200">Z wiersza polecenia `dotnet new razorclasslib`uruchom polecenie .</span><span class="sxs-lookup"><span data-stu-id="de8f7-200">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="de8f7-201">Przykład:</span><span class="sxs-lookup"><span data-stu-id="de8f7-201">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="de8f7-202">Aby uzyskać więcej informacji, zobacz [dotnet new](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="de8f7-202">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="de8f7-203">Aby uniknąć kolizji nazwy pliku z wygenerowaną biblioteką widoku, `.Views`upewnij się, że nazwa biblioteki nie kończy się na .</span><span class="sxs-lookup"><span data-stu-id="de8f7-203">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="de8f7-204">Dodaj pliki Razor do RCL.</span><span class="sxs-lookup"><span data-stu-id="de8f7-204">Add Razor files to the RCL.</span></span>

<span data-ttu-id="de8f7-205">Szablony ASP.NET Core zakładają, że zawartość RCL znajduje się w folderze *Obszary.*</span><span class="sxs-lookup"><span data-stu-id="de8f7-205">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="de8f7-206">Zobacz [układ strony RCL,](#rcl-pages-layout) aby utworzyć umowę `~/Pages` RCL, która udostępnia zawartość, a nie `~/Areas/Pages`.</span><span class="sxs-lookup"><span data-stu-id="de8f7-206">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="de8f7-207">Odwołuje się do zawartości RCL</span><span class="sxs-lookup"><span data-stu-id="de8f7-207">Reference RCL content</span></span>

<span data-ttu-id="de8f7-208">RCL może się odwoływać:</span><span class="sxs-lookup"><span data-stu-id="de8f7-208">The RCL can be referenced by:</span></span>

* <span data-ttu-id="de8f7-209">Pakiet NuGet.</span><span class="sxs-lookup"><span data-stu-id="de8f7-209">NuGet package.</span></span> <span data-ttu-id="de8f7-210">Zobacz [Tworzenie pakietów NuGet](/nuget/create-packages/creating-a-package) i [dotnet dodać pakiet](/dotnet/core/tools/dotnet-add-package) i tworzenie i [publikowanie pakietu NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="de8f7-210">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="de8f7-211">*{ProjectName}.csproj*.</span><span class="sxs-lookup"><span data-stu-id="de8f7-211">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="de8f7-212">Zobacz [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span><span class="sxs-lookup"><span data-stu-id="de8f7-212">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-razor-pages-project"></a><span data-ttu-id="de8f7-213">Instruktaż: Tworzenie projektu RCL i używanie go w projekcie Razor Pages</span><span class="sxs-lookup"><span data-stu-id="de8f7-213">Walkthrough: Create an RCL project and use from a Razor Pages project</span></span>

<span data-ttu-id="de8f7-214">Możesz pobrać [kompletny projekt](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) i przetestować go, a nie go utworzyć.</span><span class="sxs-lookup"><span data-stu-id="de8f7-214">You can download the [complete project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) and test it rather than creating it.</span></span> <span data-ttu-id="de8f7-215">Przykładowe pobieranie zawiera dodatkowy kod i łącza, które ułatwiają testowanie projektu.</span><span class="sxs-lookup"><span data-stu-id="de8f7-215">The sample download contains additional code and links that make the project easy to test.</span></span> <span data-ttu-id="de8f7-216">Możesz wystawić opinię w tym problemie z [githubem](https://github.com/dotnet/AspNetCore.Docs/issues/6098) z komentarzami dotyczącymi próbek pobierania w porównaniu z instrukcjami krok po kroku.</span><span class="sxs-lookup"><span data-stu-id="de8f7-216">You can leave feedback in [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/6098) with your comments on download samples versus step-by-step instructions.</span></span>

### <a name="test-the-download-app"></a><span data-ttu-id="de8f7-217">Testowanie aplikacji do pobierania</span><span class="sxs-lookup"><span data-stu-id="de8f7-217">Test the download app</span></span>

<span data-ttu-id="de8f7-218">Jeśli nie pobrałeś ukończonej aplikacji i wolisz utworzyć projekt instruktażowy, przejdź do [następnej sekcji](#create-an-rcl).</span><span class="sxs-lookup"><span data-stu-id="de8f7-218">If you haven't downloaded the completed app and would rather create the walkthrough project, skip to the [next section](#create-an-rcl).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="de8f7-219">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="de8f7-219">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="de8f7-220">Otwórz plik *.sln* w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="de8f7-220">Open the *.sln* file in Visual Studio.</span></span> <span data-ttu-id="de8f7-221">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="de8f7-221">Run the app.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="de8f7-222">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="de8f7-222">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="de8f7-223">Z wiersza polecenia w katalogu *cli* skompiluj rcl i aplikację internetową.</span><span class="sxs-lookup"><span data-stu-id="de8f7-223">From a command prompt in the *cli* directory, build the RCL and web app.</span></span>

```dotnetcli
dotnet build
```

<span data-ttu-id="de8f7-224">Przejdź do katalogu *WebApp1* i uruchom aplikację:</span><span class="sxs-lookup"><span data-stu-id="de8f7-224">Move to the *WebApp1* directory and run the app:</span></span>

```dotnetcli
dotnet run
```

---

<span data-ttu-id="de8f7-225">Postępuj zgodnie z instrukcjami w [aplikacji Test WebApp1](#test-webapp1)</span><span class="sxs-lookup"><span data-stu-id="de8f7-225">Follow the instructions in [Test WebApp1](#test-webapp1)</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="de8f7-226">Tworzenie listy RCL</span><span class="sxs-lookup"><span data-stu-id="de8f7-226">Create an RCL</span></span>

<span data-ttu-id="de8f7-227">W tej sekcji tworzony jest rcl.</span><span class="sxs-lookup"><span data-stu-id="de8f7-227">In this section, an RCL is created.</span></span> <span data-ttu-id="de8f7-228">Pliki brzytwy są dodawane do RCL.</span><span class="sxs-lookup"><span data-stu-id="de8f7-228">Razor files are added to the RCL.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="de8f7-229">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="de8f7-229">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="de8f7-230">Utwórz projekt RCL:</span><span class="sxs-lookup"><span data-stu-id="de8f7-230">Create the RCL project:</span></span>

* <span data-ttu-id="de8f7-231">Z menu **Plik** programu Visual Studio wybierz polecenie **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="de8f7-231">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="de8f7-232">Wybierz **ASP.NET podstawową aplikację sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="de8f7-232">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="de8f7-233">Nazwij aplikację **RazorUIClassLib** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="de8f7-233">Name the app **RazorUIClassLib** > **OK**.</span></span>
* <span data-ttu-id="de8f7-234">Sprawdź, **czy ASP.NET wybrano core 2.1** lub nowszą.</span><span class="sxs-lookup"><span data-stu-id="de8f7-234">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="de8f7-235">Wybierz opcję **Biblioteka** > klas Razor **OK**.</span><span class="sxs-lookup"><span data-stu-id="de8f7-235">Select **Razor Class Library** > **OK**.</span></span>
* <span data-ttu-id="de8f7-236">Dodaj plik widoku częściowego Razor o nazwie *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="de8f7-236">Add a Razor partial view file named *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="de8f7-237">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="de8f7-237">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="de8f7-238">W wierszu polecenia uruchom następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="de8f7-238">From the command line, run the following:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="de8f7-239">Poprzednie polecenia:</span><span class="sxs-lookup"><span data-stu-id="de8f7-239">The preceding commands:</span></span>

* <span data-ttu-id="de8f7-240">Tworzy `RazorUIClassLib` rcl.</span><span class="sxs-lookup"><span data-stu-id="de8f7-240">Creates the `RazorUIClassLib` RCL.</span></span>
* <span data-ttu-id="de8f7-241">Tworzy stronę razor _Message i dodaje ją do listy RCL.</span><span class="sxs-lookup"><span data-stu-id="de8f7-241">Creates a Razor _Message page, and adds it to the RCL.</span></span> <span data-ttu-id="de8f7-242">Parametr `-np` tworzy stronę bez `PageModel`pliku .</span><span class="sxs-lookup"><span data-stu-id="de8f7-242">The `-np` parameter creates the page without a `PageModel`.</span></span>
* <span data-ttu-id="de8f7-243">Tworzy [plik _ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) i dodaje go do listy RCL.</span><span class="sxs-lookup"><span data-stu-id="de8f7-243">Creates a [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) file and adds it to the RCL.</span></span>

<span data-ttu-id="de8f7-244">Plik *_ViewStart.cshtml* jest wymagany do użycia układu projektu Razor Pages (który jest dodawany w następnej sekcji).</span><span class="sxs-lookup"><span data-stu-id="de8f7-244">The *_ViewStart.cshtml* file is required to use the layout of the Razor Pages project (which is added in the next section).</span></span>

---

### <a name="add-razor-files-and-folders-to-the-project"></a><span data-ttu-id="de8f7-245">Dodawanie plików i folderów Razor do projektu</span><span class="sxs-lookup"><span data-stu-id="de8f7-245">Add Razor files and folders to the project</span></span>

* <span data-ttu-id="de8f7-246">Zastąp znaczniki w *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="de8f7-246">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* <span data-ttu-id="de8f7-247">Zastąp znaczniki w *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="de8f7-247">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

  <span data-ttu-id="de8f7-248">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`jest wymagane do korzystania z`<partial name="_Message" />`widoku częściowego ( ).</span><span class="sxs-lookup"><span data-stu-id="de8f7-248">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` is required to use the partial view (`<partial name="_Message" />`).</span></span> <span data-ttu-id="de8f7-249">Zamiast dołączać `@addTagHelper` dyrektywę, można dodać plik *_ViewImports.cshtml.*</span><span class="sxs-lookup"><span data-stu-id="de8f7-249">Rather than including the `@addTagHelper` directive, you can add a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="de8f7-250">Przykład:</span><span class="sxs-lookup"><span data-stu-id="de8f7-250">For example:</span></span>

  ```dotnetcli
  dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
  ```

  <span data-ttu-id="de8f7-251">Aby uzyskać więcej informacji na temat *_ViewImports.cshtml*, zobacz [Importowanie dyrektyw udostępnionych](xref:mvc/views/layout#importing-shared-directives)</span><span class="sxs-lookup"><span data-stu-id="de8f7-251">For more information on *_ViewImports.cshtml*, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives)</span></span>

* <span data-ttu-id="de8f7-252">Tworzenie biblioteki klas w celu sprawdzenia, czy nie ma żadnych błędów kompilatora:</span><span class="sxs-lookup"><span data-stu-id="de8f7-252">Build the class library to verify there are no compiler errors:</span></span>

  ```dotnetcli
  dotnet build RazorUIClassLib
  ```

<span data-ttu-id="de8f7-253">Dane wyjściowe kompilacji zawiera *razorUIClassLib.dll* i *RazorUIClassLib.Views.dll*.</span><span class="sxs-lookup"><span data-stu-id="de8f7-253">The build output contains *RazorUIClassLib.dll* and *RazorUIClassLib.Views.dll*.</span></span> <span data-ttu-id="de8f7-254">*RazorUIClassLib.Views.dll* zawiera skompilowaną zawartość Razor.</span><span class="sxs-lookup"><span data-stu-id="de8f7-254">*RazorUIClassLib.Views.dll* contains the compiled Razor content.</span></span>

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a><span data-ttu-id="de8f7-255">Korzystanie z biblioteki razor UI z projektu Razor Pages</span><span class="sxs-lookup"><span data-stu-id="de8f7-255">Use the Razor UI library from a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="de8f7-256">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="de8f7-256">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="de8f7-257">Tworzenie aplikacji internetowej Razor Pages:</span><span class="sxs-lookup"><span data-stu-id="de8f7-257">Create the Razor Pages web app:</span></span>

* <span data-ttu-id="de8f7-258">W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy rozwiązanie > **Dodaj** > **nowy projekt**.  </span><span class="sxs-lookup"><span data-stu-id="de8f7-258">From **Solution Explorer**, right-click the solution > **Add** >  **New Project**.</span></span>
* <span data-ttu-id="de8f7-259">Wybierz **ASP.NET podstawową aplikację sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="de8f7-259">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="de8f7-260">Nazwij aplikację **WebApp1**.</span><span class="sxs-lookup"><span data-stu-id="de8f7-260">Name the app **WebApp1**.</span></span>
* <span data-ttu-id="de8f7-261">Sprawdź, **czy ASP.NET wybrano core 2.1** lub nowszą.</span><span class="sxs-lookup"><span data-stu-id="de8f7-261">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="de8f7-262">Wybierz **pozycję Aplikacja** > sieci Web **OK**.</span><span class="sxs-lookup"><span data-stu-id="de8f7-262">Select **Web Application** > **OK**.</span></span>

* <span data-ttu-id="de8f7-263">W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy **webapp1** i wybierz pozycję **Ustaw jako projekt startowy**.</span><span class="sxs-lookup"><span data-stu-id="de8f7-263">From **Solution Explorer**, right-click on **WebApp1** and select **Set as StartUp Project**.</span></span>
* <span data-ttu-id="de8f7-264">W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy **webapp1** i wybierz polecenie **Konsylij** > **zależności zależności projektu**.</span><span class="sxs-lookup"><span data-stu-id="de8f7-264">From **Solution Explorer**, right-click on **WebApp1** and select **Build Dependencies** > **Project Dependencies**.</span></span>
* <span data-ttu-id="de8f7-265">Sprawdź **RazorUIClassLib** jako zależność **WebApp1**.</span><span class="sxs-lookup"><span data-stu-id="de8f7-265">Check **RazorUIClassLib** as a dependency of **WebApp1**.</span></span>
* <span data-ttu-id="de8f7-266">W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy **webapp1** i wybierz pozycję **Dodaj** > **odwołanie**.</span><span class="sxs-lookup"><span data-stu-id="de8f7-266">From **Solution Explorer**, right-click on **WebApp1** and select **Add** > **Reference**.</span></span>
* <span data-ttu-id="de8f7-267">W oknie dialogowym **Menedżer odwołań** zaznacz numer **RazorUIClassLib** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="de8f7-267">In the **Reference Manager** dialog, check **RazorUIClassLib** > **OK**.</span></span>

<span data-ttu-id="de8f7-268">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="de8f7-268">Run the app.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="de8f7-269">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="de8f7-269">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="de8f7-270">Utwórz aplikację internetową Razor Pages i plik rozwiązania zawierający aplikację Razor Pages i RCL:</span><span class="sxs-lookup"><span data-stu-id="de8f7-270">Create a Razor Pages web app and a solution file containing the Razor Pages app and the RCL:</span></span>

```dotnetcli
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

<span data-ttu-id="de8f7-271">Tworzenie i uruchamianie aplikacji internetowej:</span><span class="sxs-lookup"><span data-stu-id="de8f7-271">Build and run the web app:</span></span>

```dotnetcli
cd WebApp1
dotnet run
```

---

### <a name="test-webapp1"></a><span data-ttu-id="de8f7-272">Testowanie webapp1</span><span class="sxs-lookup"><span data-stu-id="de8f7-272">Test WebApp1</span></span>

<span data-ttu-id="de8f7-273">Przejdź do, aby `/MyFeature/Page1` sprawdzić, czy biblioteka klas razor UI jest w użyciu.</span><span class="sxs-lookup"><span data-stu-id="de8f7-273">Browse to `/MyFeature/Page1` to verify that the Razor UI class library is in use.</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="de8f7-274">Zastępowanie widoków, widoków częściowych i stron</span><span class="sxs-lookup"><span data-stu-id="de8f7-274">Override views, partial views, and pages</span></span>

<span data-ttu-id="de8f7-275">Gdy widok, widok częściowy lub razor Page znajduje się zarówno w aplikacji sieci web i RCL, znaczników Razor (plik*cshtml)* w aplikacji sieci web ma pierwszeństwo.</span><span class="sxs-lookup"><span data-stu-id="de8f7-275">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="de8f7-276">Na przykład dodaj *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* do aplikacji WebApp1, a strona1 w aplikacji WebApp1 będzie mieć pierwszeństwo przed stroną1 w rcl.</span><span class="sxs-lookup"><span data-stu-id="de8f7-276">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="de8f7-277">W przykładowym pobieraniu zmień nazwę *webapp1/areas/MyFeature2* na *WebApp1/Areas/MyFeature,* aby przetestować pierwszeństwo.</span><span class="sxs-lookup"><span data-stu-id="de8f7-277">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="de8f7-278">Skopiuj widok *częściowy RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* do *aplikacji WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="de8f7-278">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="de8f7-279">Zaktualizuj znaczniki, aby wskazać nową lokalizację.</span><span class="sxs-lookup"><span data-stu-id="de8f7-279">Update the markup to indicate the new location.</span></span> <span data-ttu-id="de8f7-280">Skompiluj i uruchom aplikację, aby sprawdzić, czy używana jest wersja częściowa aplikacji.</span><span class="sxs-lookup"><span data-stu-id="de8f7-280">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="de8f7-281">Układ strony RCL</span><span class="sxs-lookup"><span data-stu-id="de8f7-281">RCL Pages layout</span></span>

<span data-ttu-id="de8f7-282">Aby odwoływać się do zawartości RCL tak, jakby była częścią folderu *Pages* aplikacji sieci web, utwórz projekt RCL z następującą strukturą plików:</span><span class="sxs-lookup"><span data-stu-id="de8f7-282">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="de8f7-283">*RazorUIClassLib/Strony*</span><span class="sxs-lookup"><span data-stu-id="de8f7-283">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="de8f7-284">*RazorUIClassLib/Pages/Udostępnione*</span><span class="sxs-lookup"><span data-stu-id="de8f7-284">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="de8f7-285">Załóżmy, że *RazorUIClassLib/Pages/Shared* zawiera dwa pliki częściowe: *_Header.cshtml* i *_Footer.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="de8f7-285">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="de8f7-286">Znaczniki `<partial>` można dodać do *pliku _Layout.cshtml:*</span><span class="sxs-lookup"><span data-stu-id="de8f7-286">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="de8f7-287">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="de8f7-287">Additional resources</span></span>

* <xref:blazor/class-libraries>
