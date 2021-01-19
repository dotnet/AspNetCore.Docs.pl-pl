---
title: Interfejs użytkownika wielokrotnego użytku Razor w bibliotekach klas z ASP.NET Core
author: Rick-Anderson
description: Wyjaśnia, jak utworzyć interfejs użytkownika wielokrotnego użytku Razor przy użyciu częściowych widoków w bibliotece klas w ASP.NET Core.
ms.author: riande
ms.date: 01/19/2021
ms.custom: mvc, seodec18
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: razor-pages/ui-class
ms.openlocfilehash: a878a3485ecee0782b21ac69c5ec6ff832b9f06c
ms.sourcegitcommit: cb984e0d7dc23a88c3a4121f23acfaea0acbfe1e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2021
ms.locfileid: "98571011"
---
# <a name="create-reusable-ui-using-the-no-locrazor-class-library-project-in-aspnet-core"></a><span data-ttu-id="33baa-103">Utwórz interfejs użytkownika wielokrotnego użytku przy użyciu Razor projektu biblioteki klas w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="33baa-103">Create reusable UI using the Razor class library project in ASP.NET Core</span></span>

<span data-ttu-id="33baa-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="33baa-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="33baa-105">Razorwidoki, strony, kontrolery, modele stron, [ Razor składniki](xref:blazor/components/class-libraries), [składniki widoku](xref:mvc/views/view-components)i modele danych mogą być wbudowane w Razor bibliotekę klas (RCL).</span><span class="sxs-lookup"><span data-stu-id="33baa-105">Razor views, pages, controllers, page models, [Razor components](xref:blazor/components/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="33baa-106">RCL można spakować i ponownie użyć.</span><span class="sxs-lookup"><span data-stu-id="33baa-106">The RCL can be packaged and reused.</span></span> <span data-ttu-id="33baa-107">Aplikacje mogą zawierać RCL i przesłonić widoki oraz strony, które zawiera.</span><span class="sxs-lookup"><span data-stu-id="33baa-107">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="33baa-108">Gdy widok, częściowy widok lub Razor Strona znajdują się zarówno w aplikacji sieci Web, jak i w RCL, Razor pierwszeństwo ma znacznik (plik *. cshtml* ) w aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="33baa-108">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="33baa-109">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="33baa-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-no-locrazor-ui"></a><span data-ttu-id="33baa-110">Tworzenie biblioteki klas zawierającej Razor interfejs użytkownika</span><span class="sxs-lookup"><span data-stu-id="33baa-110">Create a class library containing Razor UI</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="33baa-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="33baa-111">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="33baa-112">W programie Visual Studio wybierz pozycję **Utwórz nowy projekt**.</span><span class="sxs-lookup"><span data-stu-id="33baa-112">From Visual Studio select **Create new a new project**.</span></span>
* <span data-ttu-id="33baa-113">Wybierz pozycję **Razor Biblioteka klas** > **dalej**.</span><span class="sxs-lookup"><span data-stu-id="33baa-113">Select **Razor Class Library** > **Next**.</span></span>
* <span data-ttu-id="33baa-114">Nazwij bibliotekę (na przykład " Razor określono"), > **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="33baa-114">Name the library (for example, "RazorClassLib"), > **Create**.</span></span> <span data-ttu-id="33baa-115">Aby uniknąć kolizji nazw plików z wygenerowanej biblioteki widoków, upewnij się, że nazwa biblioteki nie kończy się `.Views` .</span><span class="sxs-lookup"><span data-stu-id="33baa-115">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="33baa-116">Wybierz **strony i widoki pomocy technicznej** , jeśli chcesz obsługiwać widoki.</span><span class="sxs-lookup"><span data-stu-id="33baa-116">Select **Support pages and views** if you need to support views.</span></span> <span data-ttu-id="33baa-117">Domyślnie Razor obsługiwane są tylko strony.</span><span class="sxs-lookup"><span data-stu-id="33baa-117">By default, only Razor Pages are supported.</span></span> <span data-ttu-id="33baa-118">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="33baa-118">Select **Create**.</span></span>

<span data-ttu-id="33baa-119">RazorDomyślnie szablon biblioteki klas (RCL) jest Razor domyślnym programowaniem składników.</span><span class="sxs-lookup"><span data-stu-id="33baa-119">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="33baa-120">Opcja **strony i widoki pomocy technicznej** obsługuje strony i widoki.</span><span class="sxs-lookup"><span data-stu-id="33baa-120">The **Support pages and views** option supports pages and views.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="33baa-121">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="33baa-121">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="33baa-122">W wierszu polecenia Uruchom polecenie `dotnet new razorclasslib` .</span><span class="sxs-lookup"><span data-stu-id="33baa-122">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="33baa-123">Przykład:</span><span class="sxs-lookup"><span data-stu-id="33baa-123">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="33baa-124">RazorDomyślnie szablon biblioteki klas (RCL) jest Razor domyślnym programowaniem składników.</span><span class="sxs-lookup"><span data-stu-id="33baa-124">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="33baa-125">`--support-pages-and-views` `dotnet new razorclasslib --support-pages-and-views` Aby zapewnić obsługę stron i widoków, należy przekazać opcję ().</span><span class="sxs-lookup"><span data-stu-id="33baa-125">Pass the `--support-pages-and-views` option (`dotnet new razorclasslib --support-pages-and-views`) to provide support for pages and views.</span></span>

<span data-ttu-id="33baa-126">Aby uzyskać więcej informacji, zobacz [dotnet New](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="33baa-126">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="33baa-127">Aby uniknąć kolizji nazw plików z wygenerowanej biblioteki widoków, upewnij się, że nazwa biblioteki nie kończy się `.Views` .</span><span class="sxs-lookup"><span data-stu-id="33baa-127">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="33baa-128">Dodaj Razor pliki do RCL.</span><span class="sxs-lookup"><span data-stu-id="33baa-128">Add Razor files to the RCL.</span></span>

<span data-ttu-id="33baa-129">Szablony ASP.NET Core założono, że zawartość RCL znajduje się w folderze *obszarów* .</span><span class="sxs-lookup"><span data-stu-id="33baa-129">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="33baa-130">Zobacz [układ stron RCL](#rcl-pages-layout) , aby utworzyć RCL, który uwidacznia zawartość `~/Pages` zamiast `~/Areas/Pages` .</span><span class="sxs-lookup"><span data-stu-id="33baa-130">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="33baa-131">Odwołanie do zawartości RCL</span><span class="sxs-lookup"><span data-stu-id="33baa-131">Reference RCL content</span></span>

<span data-ttu-id="33baa-132">RCL może odwoływać się do:</span><span class="sxs-lookup"><span data-stu-id="33baa-132">The RCL can be referenced by:</span></span>

* <span data-ttu-id="33baa-133">Pakiet NuGet.</span><span class="sxs-lookup"><span data-stu-id="33baa-133">NuGet package.</span></span> <span data-ttu-id="33baa-134">Zobacz [Tworzenie pakietów NuGet](/nuget/create-packages/creating-a-package) i [dotnet Dodawanie pakietu](/dotnet/core/tools/dotnet-add-package) oraz [Tworzenie i publikowanie pakietu NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="33baa-134">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="33baa-135">*{ProjectName}. csproj*.</span><span class="sxs-lookup"><span data-stu-id="33baa-135">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="33baa-136">Zobacz sekcję [dotnet — Dodaj odwołanie](/dotnet/core/tools/dotnet-add-reference).</span><span class="sxs-lookup"><span data-stu-id="33baa-136">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="33baa-137">Zastąp widoki, częściowe widoki i strony</span><span class="sxs-lookup"><span data-stu-id="33baa-137">Override views, partial views, and pages</span></span>

<span data-ttu-id="33baa-138">Gdy widok, częściowy widok lub Razor Strona znajdują się zarówno w aplikacji sieci Web, jak i w RCL, Razor pierwszeństwo ma znacznik (plik *. cshtml* ) w aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="33baa-138">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="33baa-139">Na przykład Dodaj *WebApp1/Areas/webfeature/Pages/Strona1. cshtml* do WebApp1, a element Strona1 w WebApp1 będzie miał pierwszeństwo przed STRONA1 w RCL.</span><span class="sxs-lookup"><span data-stu-id="33baa-139">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="33baa-140">W przykładowym pobieraniu Zmień nazwę *WebApp1/obszarów/MyFeature2* na *WebApp1/Areas/webfeature* , aby przetestować priorytet.</span><span class="sxs-lookup"><span data-stu-id="33baa-140">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="33baa-141">Skopiuj widok części *Razor UIClassLib/Areas/webfeature/Pages/shared/_Message. cshtml* częściowo do *WebApp1/Areas/webfeature/Pages/Shared/_Message. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="33baa-141">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="33baa-142">Zaktualizuj adiustację, aby wskazać nową lokalizację.</span><span class="sxs-lookup"><span data-stu-id="33baa-142">Update the markup to indicate the new location.</span></span> <span data-ttu-id="33baa-143">Skompiluj i uruchom aplikację, aby sprawdzić, czy jest używana częściowa wersja aplikacji.</span><span class="sxs-lookup"><span data-stu-id="33baa-143">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="33baa-144">Układ stron RCL</span><span class="sxs-lookup"><span data-stu-id="33baa-144">RCL Pages layout</span></span>

<span data-ttu-id="33baa-145">Aby odwoływać się do zawartości RCL, tak jakby była częścią folderu *stron* aplikacji sieci Web, Utwórz projekt RCL o następującej strukturze plików:</span><span class="sxs-lookup"><span data-stu-id="33baa-145">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="33baa-146">*RazorUIClassLib/strony*</span><span class="sxs-lookup"><span data-stu-id="33baa-146">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="33baa-147">*RazorUIClassLib/strony/udostępnione*</span><span class="sxs-lookup"><span data-stu-id="33baa-147">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="33baa-148">Załóżmy, że *Razor UIClassLib/Pages/Shared* zawiera dwa częściowe pliki: *_Header. cshtml* i *_Footer. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="33baa-148">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="33baa-149">`<partial>`Tagi można dodać do pliku *_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="33baa-149">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

<span data-ttu-id="33baa-150">Dodaj plik *_ViewStart. cshtml* do folderu *stron* projektu RCL, aby użyć pliku *_Layout. cshtml* z aplikacji sieci Web hosta:</span><span class="sxs-lookup"><span data-stu-id="33baa-150">Add the *_ViewStart.cshtml* file to the RCL project's *Pages* folder to use the *_Layout.cshtml* file from the host web app:</span></span>

```cshtml
@{
    Layout = "_Layout";
}
```

## <a name="create-an-rcl-with-static-assets"></a><span data-ttu-id="33baa-151">Tworzenie RCL przy użyciu statycznych zasobów</span><span class="sxs-lookup"><span data-stu-id="33baa-151">Create an RCL with static assets</span></span>

<span data-ttu-id="33baa-152">RCL może wymagać pomocnika zasobów statycznych, do których można odwoływać się za pomocą RCL lub aplikacji zużywanej przez RCL.</span><span class="sxs-lookup"><span data-stu-id="33baa-152">An RCL may require companion static assets that can be referenced by either the RCL or the consuming app of the RCL.</span></span> <span data-ttu-id="33baa-153">ASP.NET Core umożliwia tworzenie RCLs zawierających statyczne zasoby, które są dostępne dla aplikacji zużywanej.</span><span class="sxs-lookup"><span data-stu-id="33baa-153">ASP.NET Core allows creating RCLs that include static assets that are available to a consuming app.</span></span>

<span data-ttu-id="33baa-154">Aby dołączyć zasoby towarzyszące jako część RCL, Utwórz folder *wwwroot* w bibliotece klas i Dołącz wszystkie wymagane pliki w tym folderze.</span><span class="sxs-lookup"><span data-stu-id="33baa-154">To include companion assets as part of an RCL, create a *wwwroot* folder in the class library and include any required files in that folder.</span></span>

<span data-ttu-id="33baa-155">Podczas pakowania RCL wszystkie zasoby towarzyszące w folderze *wwwroot* zostaną automatycznie dołączone do pakietu.</span><span class="sxs-lookup"><span data-stu-id="33baa-155">When packing an RCL, all companion assets in the *wwwroot* folder are automatically included in the package.</span></span>

<span data-ttu-id="33baa-156">Użyj `dotnet pack` polecenia zamiast wersji NuGet.exe `nuget pack` .</span><span class="sxs-lookup"><span data-stu-id="33baa-156">Use the `dotnet pack` command rather than the NuGet.exe version `nuget pack`.</span></span>

### <a name="exclude-static-assets"></a><span data-ttu-id="33baa-157">Wyklucz zasoby statyczne</span><span class="sxs-lookup"><span data-stu-id="33baa-157">Exclude static assets</span></span>

<span data-ttu-id="33baa-158">Aby wykluczyć statyczne elementy zawartości, Dodaj żądaną ścieżkę wykluczenia do `$(DefaultItemExcludes)` grupy właściwości w pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="33baa-158">To exclude static assets, add the desired exclusion path to the `$(DefaultItemExcludes)` property group in the project file.</span></span> <span data-ttu-id="33baa-159">Oddzielaj wpisy średnikami ( `;` ).</span><span class="sxs-lookup"><span data-stu-id="33baa-159">Separate entries with a semicolon (`;`).</span></span>

<span data-ttu-id="33baa-160">W poniższym przykładzie arkusz stylów *lib. css* w folderze *wwwroot* nie jest traktowany jako statyczny zasób i nie jest uwzględniony w opublikowanym RCL:</span><span class="sxs-lookup"><span data-stu-id="33baa-160">In the following example, the *lib.css* stylesheet in the *wwwroot* folder isn't considered a static asset and isn't included in the published RCL:</span></span>

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a><span data-ttu-id="33baa-161">Integracja języka TypeScript</span><span class="sxs-lookup"><span data-stu-id="33baa-161">Typescript integration</span></span>

<span data-ttu-id="33baa-162">Aby dołączyć pliki TypeScript do RCL:</span><span class="sxs-lookup"><span data-stu-id="33baa-162">To include TypeScript files in an RCL:</span></span>

1. <span data-ttu-id="33baa-163">Umieść pliki TypeScript (*. TS*) poza folderem *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="33baa-163">Place the TypeScript files (*.ts*) outside of the *wwwroot* folder.</span></span> <span data-ttu-id="33baa-164">Na przykład Umieść pliki w folderze *Client* .</span><span class="sxs-lookup"><span data-stu-id="33baa-164">For example, place the files in a *Client* folder.</span></span>

1. <span data-ttu-id="33baa-165">Skonfiguruj dane wyjściowe kompilacji TypeScript dla folderu *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="33baa-165">Configure the TypeScript build output for the *wwwroot* folder.</span></span> <span data-ttu-id="33baa-166">Ustaw `TypescriptOutDir` Właściwość wewnątrz elementu `PropertyGroup` w pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="33baa-166">Set the `TypescriptOutDir` property inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. <span data-ttu-id="33baa-167">Dołącz obiekt docelowy TypeScript jako zależność `ResolveCurrentProjectStaticWebAssets` obiektu docelowego, dodając następujący element docelowy wewnątrz `PropertyGroup` pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="33baa-167">Include the TypeScript target as a dependency of the `ResolveCurrentProjectStaticWebAssets` target by adding the following target inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     CompileTypeScript;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a><span data-ttu-id="33baa-168">Korzystanie z zawartości z RCL, do którego istnieje odwołanie</span><span class="sxs-lookup"><span data-stu-id="33baa-168">Consume content from a referenced RCL</span></span>

<span data-ttu-id="33baa-169">Pliki znajdujące się w folderze *WWWROOT* RCL są uwidocznione w RCL lub aplikacji zużywanej pod prefiksem `_content/{LIBRARY NAME}/` .</span><span class="sxs-lookup"><span data-stu-id="33baa-169">The files included in the *wwwroot* folder of the RCL are exposed to either the RCL or the consuming app under the prefix `_content/{LIBRARY NAME}/`.</span></span> <span data-ttu-id="33baa-170">Na przykład biblioteka o nazwie *Razor . Klasa. lib* skutkuje ścieżką do zawartości statycznej w lokalizacji `_content/Razor.Class.Lib/` .</span><span class="sxs-lookup"><span data-stu-id="33baa-170">For example, a library named *Razor.Class.Lib* results in a path to static content at `_content/Razor.Class.Lib/`.</span></span> <span data-ttu-id="33baa-171">Podczas tworzenia pakietu NuGet, a nazwa zestawu nie jest taka sama jak identyfikator pakietu, użyj identyfikatora pakietu dla `{LIBRARY NAME}` .</span><span class="sxs-lookup"><span data-stu-id="33baa-171">When producing a NuGet package and the assembly name isn't the same as the package ID, use the package ID for `{LIBRARY NAME}`.</span></span>

<span data-ttu-id="33baa-172">Aplikacja, która zużywa odwołania, odwołuje się do statycznych zasobów udostępnianych przez bibliotekę z `<script>` ,, `<style>` `<img>` i innymi tagami HTML.</span><span class="sxs-lookup"><span data-stu-id="33baa-172">The consuming app references static assets provided by the library with `<script>`, `<style>`, `<img>`, and other HTML tags.</span></span> <span data-ttu-id="33baa-173">Aplikacja zużywana musi mieć włączoną [obsługę pliku statycznego](xref:fundamentals/static-files) w programie `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="33baa-173">The consuming app must have [static file support](xref:fundamentals/static-files) enabled in `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

<span data-ttu-id="33baa-174">W przypadku uruchamiania aplikacji zużywającej dane wyjściowe kompilacji ( `dotnet run` ) statyczne zasoby sieci Web są domyślnie włączone w środowisku programistycznym.</span><span class="sxs-lookup"><span data-stu-id="33baa-174">When running the consuming app from build output (`dotnet run`), static web assets are enabled by default in the Development environment.</span></span> <span data-ttu-id="33baa-175">Aby zapewnić obsługę zasobów w innych środowiskach podczas uruchamiania z danych wyjściowych kompilacji, należy wywołać `UseStaticWebAssets` konstruktora hosta w *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="33baa-175">To support assets in other environments when running from build output, call `UseStaticWebAssets` on the host builder in *Program.cs*:</span></span>

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

<span data-ttu-id="33baa-176">Wywołanie `UseStaticWebAssets` nie jest wymagane w przypadku uruchamiania aplikacji z opublikowanych danych wyjściowych ( `dotnet publish` ).</span><span class="sxs-lookup"><span data-stu-id="33baa-176">Calling `UseStaticWebAssets` isn't required when running an app from published output (`dotnet publish`).</span></span>

### <a name="multi-project-development-flow"></a><span data-ttu-id="33baa-177">Przepływ programistyczny dla wieloprojektowych</span><span class="sxs-lookup"><span data-stu-id="33baa-177">Multi-project development flow</span></span>

<span data-ttu-id="33baa-178">Po uruchomieniu aplikacji zużywanej przez aplikację:</span><span class="sxs-lookup"><span data-stu-id="33baa-178">When the consuming app runs:</span></span>

* <span data-ttu-id="33baa-179">Zasoby w RCL pozostają w swoich oryginalnych folderach.</span><span class="sxs-lookup"><span data-stu-id="33baa-179">The assets in the RCL stay in their original folders.</span></span> <span data-ttu-id="33baa-180">Elementy zawartości nie są przenoszone do aplikacji zużywanej.</span><span class="sxs-lookup"><span data-stu-id="33baa-180">The assets aren't moved to the consuming app.</span></span>
* <span data-ttu-id="33baa-181">Wszelkie zmiany w folderze *WWWROOT* RCL są odzwierciedlone w aplikacji zużywanej po odtworzeniu RCL i niepomyślnym skompilowaniu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="33baa-181">Any change within the RCL's *wwwroot* folder is reflected in the consuming app after the RCL is rebuilt and without rebuilding the consuming app.</span></span>

<span data-ttu-id="33baa-182">Po skompilowaniu RCL jest tworzony manifest, który opisuje lokalizacje statycznego elementu zawartości sieci Web.</span><span class="sxs-lookup"><span data-stu-id="33baa-182">When the RCL is built, a manifest is produced that describes the static web asset locations.</span></span> <span data-ttu-id="33baa-183">Aplikacja, która korzysta z aplikacji, odczytuje manifest w czasie wykonywania, aby wykorzystać zasoby z przywoływanych projektów i pakietów.</span><span class="sxs-lookup"><span data-stu-id="33baa-183">The consuming app reads the manifest at runtime to consume the assets from referenced projects and packages.</span></span> <span data-ttu-id="33baa-184">Po dodaniu nowego elementu zawartości do RCL należy ponownie skompilować RCL, aby zaktualizować jego manifest, zanim aplikacja zużywa dostęp do nowego elementu zawartości.</span><span class="sxs-lookup"><span data-stu-id="33baa-184">When a new asset is added to an RCL, the RCL must be rebuilt to update its manifest before a consuming app can access the new asset.</span></span>

### <a name="publish"></a><span data-ttu-id="33baa-185">Publikowanie</span><span class="sxs-lookup"><span data-stu-id="33baa-185">Publish</span></span>

<span data-ttu-id="33baa-186">Po opublikowaniu aplikacji składniki towarzyszące ze wszystkich przywoływanych projektów i pakietów są kopiowane do folderu *wwwroot* opublikowanej aplikacji w obszarze `_content/{LIBRARY NAME}/` .</span><span class="sxs-lookup"><span data-stu-id="33baa-186">When the app is published, the companion assets from all referenced projects and packages are copied into the *wwwroot* folder of the published app under `_content/{LIBRARY NAME}/`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="33baa-187">Razorwidoki, strony, kontrolery, modele stron, [ Razor składniki](xref:blazor/components/class-libraries), [składniki widoku](xref:mvc/views/view-components)i modele danych mogą być wbudowane w Razor bibliotekę klas (RCL).</span><span class="sxs-lookup"><span data-stu-id="33baa-187">Razor views, pages, controllers, page models, [Razor components](xref:blazor/components/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="33baa-188">RCL można spakować i ponownie użyć.</span><span class="sxs-lookup"><span data-stu-id="33baa-188">The RCL can be packaged and reused.</span></span> <span data-ttu-id="33baa-189">Aplikacje mogą zawierać RCL i przesłonić widoki oraz strony, które zawiera.</span><span class="sxs-lookup"><span data-stu-id="33baa-189">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="33baa-190">Gdy widok, częściowy widok lub Razor Strona znajdują się zarówno w aplikacji sieci Web, jak i w RCL, Razor pierwszeństwo ma znacznik (plik *. cshtml* ) w aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="33baa-190">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="33baa-191">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="33baa-191">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-no-locrazor-ui"></a><span data-ttu-id="33baa-192">Tworzenie biblioteki klas zawierającej Razor interfejs użytkownika</span><span class="sxs-lookup"><span data-stu-id="33baa-192">Create a class library containing Razor UI</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="33baa-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="33baa-193">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="33baa-194">Z menu **plik** programu Visual Studio wybierz pozycję **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="33baa-194">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="33baa-195">Wybierz **ASP.NET Core aplikacji sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="33baa-195">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="33baa-196">Nazwij bibliotekę (na przykład " Razor określono") > **OK**.</span><span class="sxs-lookup"><span data-stu-id="33baa-196">Name the library (for example, "RazorClassLib") > **OK**.</span></span> <span data-ttu-id="33baa-197">Aby uniknąć kolizji nazw plików z wygenerowanej biblioteki widoków, upewnij się, że nazwa biblioteki nie kończy się `.Views` .</span><span class="sxs-lookup"><span data-stu-id="33baa-197">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="33baa-198">Sprawdź, czy wybrano **ASP.NET Core 2,1** lub nowszą.</span><span class="sxs-lookup"><span data-stu-id="33baa-198">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="33baa-199">Wybierz pozycję **Razor Biblioteka klas** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="33baa-199">Select **Razor Class Library** > **OK**.</span></span>

<span data-ttu-id="33baa-200">RCL ma następujący plik projektu:</span><span class="sxs-lookup"><span data-stu-id="33baa-200">An RCL has the following project file:</span></span>

[!code-xml[](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-cli"></a>[<span data-ttu-id="33baa-201">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="33baa-201">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="33baa-202">W wierszu polecenia Uruchom polecenie `dotnet new razorclasslib` .</span><span class="sxs-lookup"><span data-stu-id="33baa-202">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="33baa-203">Przykład:</span><span class="sxs-lookup"><span data-stu-id="33baa-203">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="33baa-204">Aby uzyskać więcej informacji, zobacz [dotnet New](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="33baa-204">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="33baa-205">Aby uniknąć kolizji nazw plików z wygenerowanej biblioteki widoków, upewnij się, że nazwa biblioteki nie kończy się `.Views` .</span><span class="sxs-lookup"><span data-stu-id="33baa-205">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="33baa-206">Dodaj Razor pliki do RCL.</span><span class="sxs-lookup"><span data-stu-id="33baa-206">Add Razor files to the RCL.</span></span>

<span data-ttu-id="33baa-207">Szablony ASP.NET Core założono, że zawartość RCL znajduje się w folderze *obszarów* .</span><span class="sxs-lookup"><span data-stu-id="33baa-207">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="33baa-208">Zobacz [układ stron RCL](#rcl-pages-layout) , aby utworzyć RCL, który uwidacznia zawartość `~/Pages` zamiast `~/Areas/Pages` .</span><span class="sxs-lookup"><span data-stu-id="33baa-208">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="33baa-209">Odwołanie do zawartości RCL</span><span class="sxs-lookup"><span data-stu-id="33baa-209">Reference RCL content</span></span>

<span data-ttu-id="33baa-210">RCL może odwoływać się do:</span><span class="sxs-lookup"><span data-stu-id="33baa-210">The RCL can be referenced by:</span></span>

* <span data-ttu-id="33baa-211">Pakiet NuGet.</span><span class="sxs-lookup"><span data-stu-id="33baa-211">NuGet package.</span></span> <span data-ttu-id="33baa-212">Zobacz [Tworzenie pakietów NuGet](/nuget/create-packages/creating-a-package) i [dotnet Dodawanie pakietu](/dotnet/core/tools/dotnet-add-package) oraz [Tworzenie i publikowanie pakietu NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="33baa-212">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="33baa-213">*{ProjectName}. csproj*.</span><span class="sxs-lookup"><span data-stu-id="33baa-213">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="33baa-214">Zobacz sekcję [dotnet — Dodaj odwołanie](/dotnet/core/tools/dotnet-add-reference).</span><span class="sxs-lookup"><span data-stu-id="33baa-214">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-no-locrazor-pages-project"></a><span data-ttu-id="33baa-215">Przewodnik: Tworzenie projektu RCL i korzystanie z niego z Razor projektu stron</span><span class="sxs-lookup"><span data-stu-id="33baa-215">Walkthrough: Create an RCL project and use from a Razor Pages project</span></span>

<span data-ttu-id="33baa-216">Możesz pobrać [kompletny projekt](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) i przetestować go zamiast go tworzyć.</span><span class="sxs-lookup"><span data-stu-id="33baa-216">You can download the [complete project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) and test it rather than creating it.</span></span> <span data-ttu-id="33baa-217">Pobranie próbek zawiera dodatkowy kod i linki, które ułatwiają przetestowanie projektu.</span><span class="sxs-lookup"><span data-stu-id="33baa-217">The sample download contains additional code and links that make the project easy to test.</span></span> <span data-ttu-id="33baa-218">Możesz wystawić opinię na temat [tego problemu](https://github.com/dotnet/AspNetCore.Docs/issues/6098) w usłudze GitHub, korzystając z komentarzy dotyczących pobierania próbek, a także instrukcje krok po kroku.</span><span class="sxs-lookup"><span data-stu-id="33baa-218">You can leave feedback in [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/6098) with your comments on download samples versus step-by-step instructions.</span></span>

### <a name="test-the-download-app"></a><span data-ttu-id="33baa-219">Testowanie aplikacji do pobrania</span><span class="sxs-lookup"><span data-stu-id="33baa-219">Test the download app</span></span>

<span data-ttu-id="33baa-220">Jeśli nie pobrano ukończonej aplikacji i wolisz utworzyć projekt przewodnika, przejdź do [następnej sekcji](#create-an-rcl).</span><span class="sxs-lookup"><span data-stu-id="33baa-220">If you haven't downloaded the completed app and would rather create the walkthrough project, skip to the [next section](#create-an-rcl).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="33baa-221">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="33baa-221">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="33baa-222">Otwórz plik *sln* w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="33baa-222">Open the *.sln* file in Visual Studio.</span></span> <span data-ttu-id="33baa-223">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="33baa-223">Run the app.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="33baa-224">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="33baa-224">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="33baa-225">Z wiersza polecenia w katalogu *CLI* Utwórz aplikację RCL i sieć Web.</span><span class="sxs-lookup"><span data-stu-id="33baa-225">From a command prompt in the *cli* directory, build the RCL and web app.</span></span>

```dotnetcli
dotnet build
```

<span data-ttu-id="33baa-226">Przejdź do katalogu *WebApp1* i uruchom aplikację:</span><span class="sxs-lookup"><span data-stu-id="33baa-226">Move to the *WebApp1* directory and run the app:</span></span>

```dotnetcli
dotnet run
```

---

<span data-ttu-id="33baa-227">Postępuj zgodnie z instrukcjami w temacie [test WebApp1](#test-webapp1)</span><span class="sxs-lookup"><span data-stu-id="33baa-227">Follow the instructions in [Test WebApp1](#test-webapp1)</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="33baa-228">Utwórz RCL</span><span class="sxs-lookup"><span data-stu-id="33baa-228">Create an RCL</span></span>

<span data-ttu-id="33baa-229">W tej sekcji jest tworzony RCL.</span><span class="sxs-lookup"><span data-stu-id="33baa-229">In this section, an RCL is created.</span></span> <span data-ttu-id="33baa-230">Razor pliki są dodawane do RCL.</span><span class="sxs-lookup"><span data-stu-id="33baa-230">Razor files are added to the RCL.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="33baa-231">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="33baa-231">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="33baa-232">Utwórz projekt RCL:</span><span class="sxs-lookup"><span data-stu-id="33baa-232">Create the RCL project:</span></span>

* <span data-ttu-id="33baa-233">Z menu **plik** programu Visual Studio wybierz pozycję **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="33baa-233">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="33baa-234">Wybierz **ASP.NET Core aplikacji sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="33baa-234">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="33baa-235">Nadaj aplikacji nazwę **Razor UIClassLib** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="33baa-235">Name the app **RazorUIClassLib** > **OK**.</span></span>
* <span data-ttu-id="33baa-236">Sprawdź, czy wybrano **ASP.NET Core 2,1** lub nowszą.</span><span class="sxs-lookup"><span data-stu-id="33baa-236">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="33baa-237">Wybierz pozycję **Razor Biblioteka klas** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="33baa-237">Select **Razor Class Library** > **OK**.</span></span>
* <span data-ttu-id="33baa-238">Dodaj Razor plik widoku częściowego o nazwie *Razor UIClassLib/Areas/Webfeature/Pages/Shared/_Message. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="33baa-238">Add a Razor partial view file named *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="33baa-239">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="33baa-239">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="33baa-240">W wierszu polecenia Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="33baa-240">From the command line, run the following:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="33baa-241">Poprzednie polecenia:</span><span class="sxs-lookup"><span data-stu-id="33baa-241">The preceding commands:</span></span>

* <span data-ttu-id="33baa-242">Tworzy `RazorUIClassLib` RCL.</span><span class="sxs-lookup"><span data-stu-id="33baa-242">Creates the `RazorUIClassLib` RCL.</span></span>
* <span data-ttu-id="33baa-243">Tworzy Razor stronę _Message i dodaje ją do RCL.</span><span class="sxs-lookup"><span data-stu-id="33baa-243">Creates a Razor _Message page, and adds it to the RCL.</span></span> <span data-ttu-id="33baa-244">`-np`Parametr tworzy stronę bez `PageModel` .</span><span class="sxs-lookup"><span data-stu-id="33baa-244">The `-np` parameter creates the page without a `PageModel`.</span></span>
* <span data-ttu-id="33baa-245">Tworzy plik [_ViewStart. cshtml](xref:mvc/views/layout#running-code-before-each-view) i dodaje go do RCL.</span><span class="sxs-lookup"><span data-stu-id="33baa-245">Creates a [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) file and adds it to the RCL.</span></span>

<span data-ttu-id="33baa-246">Plik *_ViewStart. cshtml* jest wymagany do używania układu Razor projektu stron (który jest dodawany w następnej sekcji).</span><span class="sxs-lookup"><span data-stu-id="33baa-246">The *_ViewStart.cshtml* file is required to use the layout of the Razor Pages project (which is added in the next section).</span></span>

---

### <a name="add-no-locrazor-files-and-folders-to-the-project"></a><span data-ttu-id="33baa-247">Dodawanie Razor plików i folderów do projektu</span><span class="sxs-lookup"><span data-stu-id="33baa-247">Add Razor files and folders to the project</span></span>

* <span data-ttu-id="33baa-248">Zastąp znaczniki w *Razor UIClassLib/Areas/webfeature/Pages/Shared/_Message. cshtml* następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="33baa-248">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* <span data-ttu-id="33baa-249">Zastąp znaczniki w *Razor UIClassLib/Areas/webfeature/Pages/Strona1. cshtml* następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="33baa-249">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

  <span data-ttu-id="33baa-250">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` jest wymagany do korzystania z widoku częściowego ( `<partial name="_Message" />` ).</span><span class="sxs-lookup"><span data-stu-id="33baa-250">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` is required to use the partial view (`<partial name="_Message" />`).</span></span> <span data-ttu-id="33baa-251">Zamiast `@addTagHelper` dyrektywy, można dodać plik *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="33baa-251">Rather than including the `@addTagHelper` directive, you can add a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="33baa-252">Przykład:</span><span class="sxs-lookup"><span data-stu-id="33baa-252">For example:</span></span>

  ```dotnetcli
  dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
  ```

  <span data-ttu-id="33baa-253">Aby uzyskać więcej informacji na temat *_ViewImports. cshtml*, zobacz [Importowanie wspólnych dyrektyw](xref:mvc/views/layout#importing-shared-directives)</span><span class="sxs-lookup"><span data-stu-id="33baa-253">For more information on *_ViewImports.cshtml*, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives)</span></span>

* <span data-ttu-id="33baa-254">Kompiluj bibliotekę klas, aby upewnić się, że nie występują błędy kompilatora:</span><span class="sxs-lookup"><span data-stu-id="33baa-254">Build the class library to verify there are no compiler errors:</span></span>

  ```dotnetcli
  dotnet build RazorUIClassLib
  ```

<span data-ttu-id="33baa-255">Dane wyjściowe kompilacji zawierają *RazorUIClassLib.dll* i *RazorUIClassLib.Views.dll*.</span><span class="sxs-lookup"><span data-stu-id="33baa-255">The build output contains *RazorUIClassLib.dll* and *RazorUIClassLib.Views.dll*.</span></span> <span data-ttu-id="33baa-256">*RazorUIClassLib.Views.dll* zawiera skompilowaną Razor zawartość.</span><span class="sxs-lookup"><span data-stu-id="33baa-256">*RazorUIClassLib.Views.dll* contains the compiled Razor content.</span></span>

### <a name="use-the-no-locrazor-ui-library-from-a-no-locrazor-pages-project"></a><span data-ttu-id="33baa-257">Korzystanie z Razor biblioteki interfejsu użytkownika z Razor projektu stron</span><span class="sxs-lookup"><span data-stu-id="33baa-257">Use the Razor UI library from a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="33baa-258">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="33baa-258">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="33baa-259">Tworzenie Razor aplikacji sieci Web dla stron:</span><span class="sxs-lookup"><span data-stu-id="33baa-259">Create the Razor Pages web app:</span></span>

* <span data-ttu-id="33baa-260">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy rozwiązanie > **Dodaj** > **Nowy projekt**.  </span><span class="sxs-lookup"><span data-stu-id="33baa-260">From **Solution Explorer**, right-click the solution > **Add** >  **New Project**.</span></span>
* <span data-ttu-id="33baa-261">Wybierz **ASP.NET Core aplikacji sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="33baa-261">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="33baa-262">Nadaj aplikacji nazwę **WebApp1**.</span><span class="sxs-lookup"><span data-stu-id="33baa-262">Name the app **WebApp1**.</span></span>
* <span data-ttu-id="33baa-263">Sprawdź, czy wybrano **ASP.NET Core 2,1** lub nowszą.</span><span class="sxs-lookup"><span data-stu-id="33baa-263">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="33baa-264">Wybierz pozycję **aplikacja sieci Web** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="33baa-264">Select **Web Application** > **OK**.</span></span>

* <span data-ttu-id="33baa-265">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy pozycję **WebApp1** i wybierz pozycję **Ustaw jako projekt startowy**.</span><span class="sxs-lookup"><span data-stu-id="33baa-265">From **Solution Explorer**, right-click on **WebApp1** and select **Set as StartUp Project**.</span></span>
* <span data-ttu-id="33baa-266">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy pozycję **WebApp1** i wybierz pozycję **kompilacja zależności** > **projektu zależności**.</span><span class="sxs-lookup"><span data-stu-id="33baa-266">From **Solution Explorer**, right-click on **WebApp1** and select **Build Dependencies** > **Project Dependencies**.</span></span>
* <span data-ttu-id="33baa-267">Sprawdź **Razor UIClassLib** jako zależność **WebApp1**.</span><span class="sxs-lookup"><span data-stu-id="33baa-267">Check **RazorUIClassLib** as a dependency of **WebApp1**.</span></span>
* <span data-ttu-id="33baa-268">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy pozycję **WebApp1** i wybierz pozycję **Dodaj** > **odwołanie**.</span><span class="sxs-lookup"><span data-stu-id="33baa-268">From **Solution Explorer**, right-click on **WebApp1** and select **Add** > **Reference**.</span></span>
* <span data-ttu-id="33baa-269">W oknie dialogowym **Menedżer odwołań** Sprawdź **Razor UIClassLib** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="33baa-269">In the **Reference Manager** dialog, check **RazorUIClassLib** > **OK**.</span></span>

<span data-ttu-id="33baa-270">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="33baa-270">Run the app.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="33baa-271">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="33baa-271">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="33baa-272">Utwórz Razor aplikację internetową stron i plik rozwiązania zawierający Razor aplikację Pages i RCL:</span><span class="sxs-lookup"><span data-stu-id="33baa-272">Create a Razor Pages web app and a solution file containing the Razor Pages app and the RCL:</span></span>

```dotnetcli
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

<span data-ttu-id="33baa-273">Kompiluj i uruchom aplikację sieci Web:</span><span class="sxs-lookup"><span data-stu-id="33baa-273">Build and run the web app:</span></span>

```dotnetcli
cd WebApp1
dotnet run
```

---

### <a name="test-webapp1"></a><span data-ttu-id="33baa-274">Test WebApp1</span><span class="sxs-lookup"><span data-stu-id="33baa-274">Test WebApp1</span></span>

<span data-ttu-id="33baa-275">Przejdź do `/MyFeature/Page1` strony, aby sprawdzić, czy Razor Biblioteka klas interfejsu użytkownika jest używana.</span><span class="sxs-lookup"><span data-stu-id="33baa-275">Browse to `/MyFeature/Page1` to verify that the Razor UI class library is in use.</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="33baa-276">Zastąp widoki, częściowe widoki i strony</span><span class="sxs-lookup"><span data-stu-id="33baa-276">Override views, partial views, and pages</span></span>

<span data-ttu-id="33baa-277">Gdy widok, częściowy widok lub Razor Strona znajdują się zarówno w aplikacji sieci Web, jak i w RCL, Razor pierwszeństwo ma znacznik (plik *. cshtml* ) w aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="33baa-277">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="33baa-278">Na przykład Dodaj *WebApp1/Areas/webfeature/Pages/Strona1. cshtml* do WebApp1, a element Strona1 w WebApp1 będzie miał pierwszeństwo przed STRONA1 w RCL.</span><span class="sxs-lookup"><span data-stu-id="33baa-278">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="33baa-279">W przykładowym pobieraniu Zmień nazwę *WebApp1/obszarów/MyFeature2* na *WebApp1/Areas/webfeature* , aby przetestować priorytet.</span><span class="sxs-lookup"><span data-stu-id="33baa-279">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="33baa-280">Skopiuj widok części *Razor UIClassLib/Areas/webfeature/Pages/shared/_Message. cshtml* częściowo do *WebApp1/Areas/webfeature/Pages/Shared/_Message. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="33baa-280">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="33baa-281">Zaktualizuj adiustację, aby wskazać nową lokalizację.</span><span class="sxs-lookup"><span data-stu-id="33baa-281">Update the markup to indicate the new location.</span></span> <span data-ttu-id="33baa-282">Skompiluj i uruchom aplikację, aby sprawdzić, czy jest używana częściowa wersja aplikacji.</span><span class="sxs-lookup"><span data-stu-id="33baa-282">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="33baa-283">Układ stron RCL</span><span class="sxs-lookup"><span data-stu-id="33baa-283">RCL Pages layout</span></span>

<span data-ttu-id="33baa-284">Aby odwoływać się do zawartości RCL, tak jakby była częścią folderu *stron* aplikacji sieci Web, Utwórz projekt RCL o następującej strukturze plików:</span><span class="sxs-lookup"><span data-stu-id="33baa-284">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="33baa-285">*RazorUIClassLib/strony*</span><span class="sxs-lookup"><span data-stu-id="33baa-285">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="33baa-286">*RazorUIClassLib/strony/udostępnione*</span><span class="sxs-lookup"><span data-stu-id="33baa-286">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="33baa-287">Załóżmy, że *Razor UIClassLib/Pages/Shared* zawiera dwa częściowe pliki: *_Header. cshtml* i *_Footer. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="33baa-287">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="33baa-288">`<partial>`Tagi można dodać do pliku *_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="33baa-288">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="33baa-289">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="33baa-289">Additional resources</span></span>

::: moniker range=">= aspnetcore-5.0"

* <xref:blazor/components/class-libraries>
* <xref:blazor/components/css-isolation#razor-class-library-rcl-support>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <xref:blazor/components/class-libraries>

::: moniker-end
