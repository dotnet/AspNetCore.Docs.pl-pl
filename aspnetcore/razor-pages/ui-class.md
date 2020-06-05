---
title: Interfejs użytkownika wielokrotnego użytku Razor w bibliotekach klas z ASP.NET Core
author: Rick-Anderson
description: Wyjaśnia, jak utworzyć interfejs użytkownika wielokrotnego użytku Razor przy użyciu częściowych widoków w bibliotece klas w ASP.NET Core.
ms.author: riande
ms.date: 01/25/2020
ms.custom: mvc, seodec18
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: razor-pages/ui-class
ms.openlocfilehash: 32aa1cdab0e552a1255c01b5135e9a82a0e37c77
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2020
ms.locfileid: "84451904"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a><span data-ttu-id="3e6fc-103">Utwórz interfejs użytkownika wielokrotnego użytku przy użyciu Razor projektu biblioteki klas w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3e6fc-103">Create reusable UI using the Razor class library project in ASP.NET Core</span></span>

<span data-ttu-id="3e6fc-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3e6fc-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

Razor<span data-ttu-id="3e6fc-105">widoki, strony, kontrolery, modele stron, [ Razor składniki](xref:blazor/class-libraries), [składniki widoku](xref:mvc/views/view-components)i modele danych mogą być wbudowane w Razor bibliotekę klas (RCL).</span><span class="sxs-lookup"><span data-stu-id="3e6fc-105"> views, pages, controllers, page models, [Razor components](xref:blazor/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="3e6fc-106">RCL można spakować i ponownie użyć.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-106">The RCL can be packaged and reused.</span></span> <span data-ttu-id="3e6fc-107">Aplikacje mogą zawierać RCL i przesłonić widoki oraz strony, które zawiera.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-107">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="3e6fc-108">Gdy widok, częściowy widok lub Razor Strona znajdują się zarówno w aplikacji sieci Web, jak i w RCL, Razor pierwszeństwo ma znacznik (plik *. cshtml* ) w aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-108">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="3e6fc-109">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3e6fc-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="3e6fc-110">Tworzenie biblioteki klas zawierającej Razor interfejs użytkownika</span><span class="sxs-lookup"><span data-stu-id="3e6fc-110">Create a class library containing Razor UI</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3e6fc-111">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3e6fc-111">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3e6fc-112">W programie Visual Studio wybierz pozycję **Utwórz nowy projekt**.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-112">From Visual Studio select **Create new a new project**.</span></span>
* <span data-ttu-id="3e6fc-113">Wybierz pozycję \*\* Razor Biblioteka klas\*\* > **dalej**.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-113">Select **Razor Class Library** > **Next**.</span></span>
* <span data-ttu-id="3e6fc-114">Nazwij bibliotekę (na przykład "RazorClassLib"), > **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-114">Name the library (for example, "RazorClassLib"), > **Create**.</span></span> <span data-ttu-id="3e6fc-115">Aby uniknąć kolizji nazw plików z wygenerowanej biblioteki widoków, upewnij się, że nazwa biblioteki nie kończy się `.Views` .</span><span class="sxs-lookup"><span data-stu-id="3e6fc-115">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="3e6fc-116">Wybierz **strony i widoki pomocy technicznej** , jeśli chcesz obsługiwać widoki.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-116">Select **Support pages and views** if you need to support views.</span></span> <span data-ttu-id="3e6fc-117">Domyślnie Razor obsługiwane są tylko strony.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-117">By default, only Razor Pages are supported.</span></span> <span data-ttu-id="3e6fc-118">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-118">Select **Create**.</span></span>

<span data-ttu-id="3e6fc-119">RazorDomyślnie szablon biblioteki klas (RCL) jest Razor domyślnym programowaniem składników.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-119">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="3e6fc-120">Opcja **strony i widoki pomocy technicznej** obsługuje strony i widoki.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-120">The **Support pages and views** option supports pages and views.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="3e6fc-121">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="3e6fc-121">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="3e6fc-122">W wierszu polecenia Uruchom polecenie `dotnet new razorclasslib` .</span><span class="sxs-lookup"><span data-stu-id="3e6fc-122">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="3e6fc-123">Przykład:</span><span class="sxs-lookup"><span data-stu-id="3e6fc-123">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="3e6fc-124">RazorDomyślnie szablon biblioteki klas (RCL) jest Razor domyślnym programowaniem składników.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-124">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="3e6fc-125">`--support-pages-and-views` `dotnet new razorclasslib --support-pages-and-views` Aby zapewnić obsługę stron i widoków, należy przekazać opcję ().</span><span class="sxs-lookup"><span data-stu-id="3e6fc-125">Pass the `--support-pages-and-views` option (`dotnet new razorclasslib --support-pages-and-views`) to provide support for pages and views.</span></span>

<span data-ttu-id="3e6fc-126">Aby uzyskać więcej informacji, zobacz [dotnet New](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="3e6fc-126">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="3e6fc-127">Aby uniknąć kolizji nazw plików z wygenerowanej biblioteki widoków, upewnij się, że nazwa biblioteki nie kończy się `.Views` .</span><span class="sxs-lookup"><span data-stu-id="3e6fc-127">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="3e6fc-128">Dodaj Razor pliki do RCL.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-128">Add Razor files to the RCL.</span></span>

<span data-ttu-id="3e6fc-129">Szablony ASP.NET Core założono, że zawartość RCL znajduje się w folderze *obszarów* .</span><span class="sxs-lookup"><span data-stu-id="3e6fc-129">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="3e6fc-130">Zobacz [układ stron RCL](#rcl-pages-layout) , aby utworzyć RCL, który uwidacznia zawartość `~/Pages` zamiast `~/Areas/Pages` .</span><span class="sxs-lookup"><span data-stu-id="3e6fc-130">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="3e6fc-131">Odwołanie do zawartości RCL</span><span class="sxs-lookup"><span data-stu-id="3e6fc-131">Reference RCL content</span></span>

<span data-ttu-id="3e6fc-132">RCL może odwoływać się do:</span><span class="sxs-lookup"><span data-stu-id="3e6fc-132">The RCL can be referenced by:</span></span>

* <span data-ttu-id="3e6fc-133">Pakiet NuGet.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-133">NuGet package.</span></span> <span data-ttu-id="3e6fc-134">Zobacz [Tworzenie pakietów NuGet](/nuget/create-packages/creating-a-package) i [dotnet Dodawanie pakietu](/dotnet/core/tools/dotnet-add-package) oraz [Tworzenie i publikowanie pakietu NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="3e6fc-134">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="3e6fc-135">*{ProjectName}. csproj*.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-135">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="3e6fc-136">Zobacz sekcję [dotnet — Dodaj odwołanie](/dotnet/core/tools/dotnet-add-reference).</span><span class="sxs-lookup"><span data-stu-id="3e6fc-136">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="3e6fc-137">Zastąp widoki, częściowe widoki i strony</span><span class="sxs-lookup"><span data-stu-id="3e6fc-137">Override views, partial views, and pages</span></span>

<span data-ttu-id="3e6fc-138">Gdy widok, częściowy widok lub Razor Strona znajdują się zarówno w aplikacji sieci Web, jak i w RCL, Razor pierwszeństwo ma znacznik (plik *. cshtml* ) w aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-138">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="3e6fc-139">Na przykład Dodaj *WebApp1/Areas/webfeature/Pages/Strona1. cshtml* do WebApp1, a element Strona1 w WebApp1 będzie miał pierwszeństwo przed STRONA1 w RCL.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-139">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="3e6fc-140">W przykładowym pobieraniu Zmień nazwę *WebApp1/obszarów/MyFeature2* na *WebApp1/Areas/webfeature* , aby przetestować priorytet.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-140">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="3e6fc-141">Skopiuj widok części *RazorUIClassLib/Areas/Webfeature/Pages/Shared/_Message. cshtml* częściowo do *WebApp1/Areas/Webfeature/Pages/Shared/_Message. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-141">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="3e6fc-142">Zaktualizuj adiustację, aby wskazać nową lokalizację.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-142">Update the markup to indicate the new location.</span></span> <span data-ttu-id="3e6fc-143">Skompiluj i uruchom aplikację, aby sprawdzić, czy jest używana częściowa wersja aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-143">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="3e6fc-144">Układ stron RCL</span><span class="sxs-lookup"><span data-stu-id="3e6fc-144">RCL Pages layout</span></span>

<span data-ttu-id="3e6fc-145">Aby odwoływać się do zawartości RCL, tak jakby była częścią folderu *stron* aplikacji sieci Web, Utwórz projekt RCL o następującej strukturze plików:</span><span class="sxs-lookup"><span data-stu-id="3e6fc-145">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="3e6fc-146">*RazorUIClassLib/strony*</span><span class="sxs-lookup"><span data-stu-id="3e6fc-146">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="3e6fc-147">*RazorUIClassLib/strony/udostępnione*</span><span class="sxs-lookup"><span data-stu-id="3e6fc-147">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="3e6fc-148">Załóżmy, że *RazorUIClassLib/Pages/Shared* zawiera dwa częściowe pliki: *_Header. cshtml* i *_Footer. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-148">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="3e6fc-149">`<partial>`Tagi można dodać do pliku *_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="3e6fc-149">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

## <a name="create-an-rcl-with-static-assets"></a><span data-ttu-id="3e6fc-150">Tworzenie RCL przy użyciu statycznych zasobów</span><span class="sxs-lookup"><span data-stu-id="3e6fc-150">Create an RCL with static assets</span></span>

<span data-ttu-id="3e6fc-151">RCL może wymagać pomocnika zasobów statycznych, do których można odwoływać się za pomocą RCL lub aplikacji zużywanej przez RCL.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-151">An RCL may require companion static assets that can be referenced by either the RCL or the consuming app of the RCL.</span></span> <span data-ttu-id="3e6fc-152">ASP.NET Core umożliwia tworzenie RCLs zawierających statyczne zasoby, które są dostępne dla aplikacji zużywanej.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-152">ASP.NET Core allows creating RCLs that include static assets that are available to a consuming app.</span></span>

<span data-ttu-id="3e6fc-153">Aby dołączyć zasoby towarzyszące jako część RCL, Utwórz folder *wwwroot* w bibliotece klas i Dołącz wszystkie wymagane pliki w tym folderze.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-153">To include companion assets as part of an RCL, create a *wwwroot* folder in the class library and include any required files in that folder.</span></span>

<span data-ttu-id="3e6fc-154">Podczas pakowania RCL wszystkie zasoby towarzyszące w folderze *wwwroot* zostaną automatycznie dołączone do pakietu.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-154">When packing an RCL, all companion assets in the *wwwroot* folder are automatically included in the package.</span></span>

<span data-ttu-id="3e6fc-155">Użyj `dotnet pack` polecenia zamiast wersji NuGet. exe `nuget pack` .</span><span class="sxs-lookup"><span data-stu-id="3e6fc-155">Use the `dotnet pack` command rather than the NuGet.exe version `nuget pack`.</span></span>

### <a name="exclude-static-assets"></a><span data-ttu-id="3e6fc-156">Wyklucz zasoby statyczne</span><span class="sxs-lookup"><span data-stu-id="3e6fc-156">Exclude static assets</span></span>

<span data-ttu-id="3e6fc-157">Aby wykluczyć statyczne elementy zawartości, Dodaj żądaną ścieżkę wykluczenia do `$(DefaultItemExcludes)` grupy właściwości w pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-157">To exclude static assets, add the desired exclusion path to the `$(DefaultItemExcludes)` property group in the project file.</span></span> <span data-ttu-id="3e6fc-158">Oddzielaj wpisy średnikami ( `;` ).</span><span class="sxs-lookup"><span data-stu-id="3e6fc-158">Separate entries with a semicolon (`;`).</span></span>

<span data-ttu-id="3e6fc-159">W poniższym przykładzie arkusz stylów *lib. css* w folderze *wwwroot* nie jest traktowany jako statyczny zasób i nie jest uwzględniony w opublikowanym RCL:</span><span class="sxs-lookup"><span data-stu-id="3e6fc-159">In the following example, the *lib.css* stylesheet in the *wwwroot* folder isn't considered a static asset and isn't included in the published RCL:</span></span>

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a><span data-ttu-id="3e6fc-160">Integracja języka TypeScript</span><span class="sxs-lookup"><span data-stu-id="3e6fc-160">Typescript integration</span></span>

<span data-ttu-id="3e6fc-161">Aby dołączyć pliki TypeScript do RCL:</span><span class="sxs-lookup"><span data-stu-id="3e6fc-161">To include TypeScript files in an RCL:</span></span>

1. <span data-ttu-id="3e6fc-162">Umieść pliki TypeScript (*. TS*) poza folderem *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="3e6fc-162">Place the TypeScript files (*.ts*) outside of the *wwwroot* folder.</span></span> <span data-ttu-id="3e6fc-163">Na przykład Umieść pliki w folderze *Client* .</span><span class="sxs-lookup"><span data-stu-id="3e6fc-163">For example, place the files in a *Client* folder.</span></span>

1. <span data-ttu-id="3e6fc-164">Skonfiguruj dane wyjściowe kompilacji TypeScript dla folderu *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="3e6fc-164">Configure the TypeScript build output for the *wwwroot* folder.</span></span> <span data-ttu-id="3e6fc-165">Ustaw `TypescriptOutDir` Właściwość wewnątrz elementu `PropertyGroup` w pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="3e6fc-165">Set the `TypescriptOutDir` property inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. <span data-ttu-id="3e6fc-166">Dołącz obiekt docelowy TypeScript jako zależność `ResolveCurrentProjectStaticWebAssets` obiektu docelowego, dodając następujący element docelowy wewnątrz `PropertyGroup` pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="3e6fc-166">Include the TypeScript target as a dependency of the `ResolveCurrentProjectStaticWebAssets` target by adding the following target inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     CompileTypeScript;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a><span data-ttu-id="3e6fc-167">Korzystanie z zawartości z RCL, do którego istnieje odwołanie</span><span class="sxs-lookup"><span data-stu-id="3e6fc-167">Consume content from a referenced RCL</span></span>

<span data-ttu-id="3e6fc-168">Pliki znajdujące się w folderze *WWWROOT* RCL są uwidocznione w RCL lub aplikacji zużywanej pod prefiksem `_content/{LIBRARY NAME}/` .</span><span class="sxs-lookup"><span data-stu-id="3e6fc-168">The files included in the *wwwroot* folder of the RCL are exposed to either the RCL or the consuming app under the prefix `_content/{LIBRARY NAME}/`.</span></span> <span data-ttu-id="3e6fc-169">Na przykład biblioteka o nazwie \* Razor . Klasa. lib\* skutkuje ścieżką do zawartości statycznej w lokalizacji `_content/Razor.Class.Lib/` .</span><span class="sxs-lookup"><span data-stu-id="3e6fc-169">For example, a library named *Razor.Class.Lib* results in a path to static content at `_content/Razor.Class.Lib/`.</span></span> <span data-ttu-id="3e6fc-170">Podczas tworzenia pakietu NuGet, a nazwa zestawu nie jest taka sama jak identyfikator pakietu, użyj identyfikatora pakietu dla `{LIBRARY NAME}` .</span><span class="sxs-lookup"><span data-stu-id="3e6fc-170">When producing a NuGet package and the assembly name isn't the same as the package ID, use the package ID for `{LIBRARY NAME}`.</span></span>

<span data-ttu-id="3e6fc-171">Aplikacja, która zużywa odwołania, odwołuje się do statycznych zasobów udostępnianych przez bibliotekę z `<script>` ,, `<style>` `<img>` i innymi tagami HTML.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-171">The consuming app references static assets provided by the library with `<script>`, `<style>`, `<img>`, and other HTML tags.</span></span> <span data-ttu-id="3e6fc-172">Aplikacja zużywana musi mieć włączoną [obsługę pliku statycznego](xref:fundamentals/static-files) w programie `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="3e6fc-172">The consuming app must have [static file support](xref:fundamentals/static-files) enabled in `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

<span data-ttu-id="3e6fc-173">W przypadku uruchamiania aplikacji zużywającej dane wyjściowe kompilacji ( `dotnet run` ) statyczne zasoby sieci Web są domyślnie włączone w środowisku programistycznym.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-173">When running the consuming app from build output (`dotnet run`), static web assets are enabled by default in the Development environment.</span></span> <span data-ttu-id="3e6fc-174">Aby zapewnić obsługę zasobów w innych środowiskach podczas uruchamiania z danych wyjściowych kompilacji, należy wywołać `UseStaticWebAssets` konstruktora hosta w *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="3e6fc-174">To support assets in other environments when running from build output, call `UseStaticWebAssets` on the host builder in *Program.cs*:</span></span>

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

<span data-ttu-id="3e6fc-175">Wywołanie `UseStaticWebAssets` nie jest wymagane w przypadku uruchamiania aplikacji z opublikowanych danych wyjściowych ( `dotnet publish` ).</span><span class="sxs-lookup"><span data-stu-id="3e6fc-175">Calling `UseStaticWebAssets` isn't required when running an app from published output (`dotnet publish`).</span></span>

### <a name="multi-project-development-flow"></a><span data-ttu-id="3e6fc-176">Przepływ programistyczny dla wieloprojektowych</span><span class="sxs-lookup"><span data-stu-id="3e6fc-176">Multi-project development flow</span></span>

<span data-ttu-id="3e6fc-177">Po uruchomieniu aplikacji zużywanej przez aplikację:</span><span class="sxs-lookup"><span data-stu-id="3e6fc-177">When the consuming app runs:</span></span>

* <span data-ttu-id="3e6fc-178">Zasoby w RCL pozostają w swoich oryginalnych folderach.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-178">The assets in the RCL stay in their original folders.</span></span> <span data-ttu-id="3e6fc-179">Elementy zawartości nie są przenoszone do aplikacji zużywanej.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-179">The assets aren't moved to the consuming app.</span></span>
* <span data-ttu-id="3e6fc-180">Wszelkie zmiany w folderze *WWWROOT* RCL są odzwierciedlone w aplikacji zużywanej po odtworzeniu RCL i niepomyślnym skompilowaniu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-180">Any change within the RCL's *wwwroot* folder is reflected in the consuming app after the RCL is rebuilt and without rebuilding the consuming app.</span></span>

<span data-ttu-id="3e6fc-181">Po skompilowaniu RCL jest tworzony manifest, który opisuje lokalizacje statycznego elementu zawartości sieci Web.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-181">When the RCL is built, a manifest is produced that describes the static web asset locations.</span></span> <span data-ttu-id="3e6fc-182">Aplikacja, która korzysta z aplikacji, odczytuje manifest w czasie wykonywania, aby wykorzystać zasoby z przywoływanych projektów i pakietów.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-182">The consuming app reads the manifest at runtime to consume the assets from referenced projects and packages.</span></span> <span data-ttu-id="3e6fc-183">Po dodaniu nowego elementu zawartości do RCL należy ponownie skompilować RCL, aby zaktualizować jego manifest, zanim aplikacja zużywa dostęp do nowego elementu zawartości.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-183">When a new asset is added to an RCL, the RCL must be rebuilt to update its manifest before a consuming app can access the new asset.</span></span>

### <a name="publish"></a><span data-ttu-id="3e6fc-184">Publikowanie</span><span class="sxs-lookup"><span data-stu-id="3e6fc-184">Publish</span></span>

<span data-ttu-id="3e6fc-185">Po opublikowaniu aplikacji składniki towarzyszące ze wszystkich przywoływanych projektów i pakietów są kopiowane do folderu *wwwroot* opublikowanej aplikacji w obszarze `_content/{LIBRARY NAME}/` .</span><span class="sxs-lookup"><span data-stu-id="3e6fc-185">When the app is published, the companion assets from all referenced projects and packages are copied into the *wwwroot* folder of the published app under `_content/{LIBRARY NAME}/`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Razor<span data-ttu-id="3e6fc-186">widoki, strony, kontrolery, modele stron, [ Razor składniki](xref:blazor/class-libraries), [składniki widoku](xref:mvc/views/view-components)i modele danych mogą być wbudowane w Razor bibliotekę klas (RCL).</span><span class="sxs-lookup"><span data-stu-id="3e6fc-186"> views, pages, controllers, page models, [Razor components](xref:blazor/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="3e6fc-187">RCL można spakować i ponownie użyć.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-187">The RCL can be packaged and reused.</span></span> <span data-ttu-id="3e6fc-188">Aplikacje mogą zawierać RCL i przesłonić widoki oraz strony, które zawiera.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-188">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="3e6fc-189">Gdy widok, częściowy widok lub Razor Strona znajdują się zarówno w aplikacji sieci Web, jak i w RCL, Razor pierwszeństwo ma znacznik (plik *. cshtml* ) w aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-189">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="3e6fc-190">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3e6fc-190">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="3e6fc-191">Tworzenie biblioteki klas zawierającej Razor interfejs użytkownika</span><span class="sxs-lookup"><span data-stu-id="3e6fc-191">Create a class library containing Razor UI</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3e6fc-192">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3e6fc-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3e6fc-193">Z menu **plik** programu Visual Studio wybierz pozycję **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-193">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="3e6fc-194">Wybierz **ASP.NET Core aplikacji sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-194">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="3e6fc-195">Nazwij bibliotekę (na przykład "RazorClassLib") > **OK**.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-195">Name the library (for example, "RazorClassLib") > **OK**.</span></span> <span data-ttu-id="3e6fc-196">Aby uniknąć kolizji nazw plików z wygenerowanej biblioteki widoków, upewnij się, że nazwa biblioteki nie kończy się `.Views` .</span><span class="sxs-lookup"><span data-stu-id="3e6fc-196">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="3e6fc-197">Sprawdź, czy wybrano **ASP.NET Core 2,1** lub nowszą.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-197">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="3e6fc-198">Wybierz pozycję \*\* Razor Biblioteka klas\*\* > **OK**.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-198">Select **Razor Class Library** > **OK**.</span></span>

<span data-ttu-id="3e6fc-199">RCL ma następujący plik projektu:</span><span class="sxs-lookup"><span data-stu-id="3e6fc-199">An RCL has the following project file:</span></span>

[!code-xml[](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-cli"></a>[<span data-ttu-id="3e6fc-200">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="3e6fc-200">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="3e6fc-201">W wierszu polecenia Uruchom polecenie `dotnet new razorclasslib` .</span><span class="sxs-lookup"><span data-stu-id="3e6fc-201">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="3e6fc-202">Przykład:</span><span class="sxs-lookup"><span data-stu-id="3e6fc-202">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="3e6fc-203">Aby uzyskać więcej informacji, zobacz [dotnet New](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="3e6fc-203">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="3e6fc-204">Aby uniknąć kolizji nazw plików z wygenerowanej biblioteki widoków, upewnij się, że nazwa biblioteki nie kończy się `.Views` .</span><span class="sxs-lookup"><span data-stu-id="3e6fc-204">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="3e6fc-205">Dodaj Razor pliki do RCL.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-205">Add Razor files to the RCL.</span></span>

<span data-ttu-id="3e6fc-206">Szablony ASP.NET Core założono, że zawartość RCL znajduje się w folderze *obszarów* .</span><span class="sxs-lookup"><span data-stu-id="3e6fc-206">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="3e6fc-207">Zobacz [układ stron RCL](#rcl-pages-layout) , aby utworzyć RCL, który uwidacznia zawartość `~/Pages` zamiast `~/Areas/Pages` .</span><span class="sxs-lookup"><span data-stu-id="3e6fc-207">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="3e6fc-208">Odwołanie do zawartości RCL</span><span class="sxs-lookup"><span data-stu-id="3e6fc-208">Reference RCL content</span></span>

<span data-ttu-id="3e6fc-209">RCL może odwoływać się do:</span><span class="sxs-lookup"><span data-stu-id="3e6fc-209">The RCL can be referenced by:</span></span>

* <span data-ttu-id="3e6fc-210">Pakiet NuGet.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-210">NuGet package.</span></span> <span data-ttu-id="3e6fc-211">Zobacz [Tworzenie pakietów NuGet](/nuget/create-packages/creating-a-package) i [dotnet Dodawanie pakietu](/dotnet/core/tools/dotnet-add-package) oraz [Tworzenie i publikowanie pakietu NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="3e6fc-211">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="3e6fc-212">*{ProjectName}. csproj*.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-212">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="3e6fc-213">Zobacz sekcję [dotnet — Dodaj odwołanie](/dotnet/core/tools/dotnet-add-reference).</span><span class="sxs-lookup"><span data-stu-id="3e6fc-213">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-razor-pages-project"></a><span data-ttu-id="3e6fc-214">Przewodnik: Tworzenie projektu RCL i korzystanie z niego z Razor projektu stron</span><span class="sxs-lookup"><span data-stu-id="3e6fc-214">Walkthrough: Create an RCL project and use from a Razor Pages project</span></span>

<span data-ttu-id="3e6fc-215">Możesz pobrać [kompletny projekt](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) i przetestować go zamiast go tworzyć.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-215">You can download the [complete project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) and test it rather than creating it.</span></span> <span data-ttu-id="3e6fc-216">Pobranie próbek zawiera dodatkowy kod i linki, które ułatwiają przetestowanie projektu.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-216">The sample download contains additional code and links that make the project easy to test.</span></span> <span data-ttu-id="3e6fc-217">Możesz wystawić opinię na temat [tego problemu](https://github.com/dotnet/AspNetCore.Docs/issues/6098) w usłudze GitHub, korzystając z komentarzy dotyczących pobierania próbek, a także instrukcje krok po kroku.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-217">You can leave feedback in [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/6098) with your comments on download samples versus step-by-step instructions.</span></span>

### <a name="test-the-download-app"></a><span data-ttu-id="3e6fc-218">Testowanie aplikacji do pobrania</span><span class="sxs-lookup"><span data-stu-id="3e6fc-218">Test the download app</span></span>

<span data-ttu-id="3e6fc-219">Jeśli nie pobrano ukończonej aplikacji i wolisz utworzyć projekt przewodnika, przejdź do [następnej sekcji](#create-an-rcl).</span><span class="sxs-lookup"><span data-stu-id="3e6fc-219">If you haven't downloaded the completed app and would rather create the walkthrough project, skip to the [next section](#create-an-rcl).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3e6fc-220">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3e6fc-220">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3e6fc-221">Otwórz plik *sln* w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-221">Open the *.sln* file in Visual Studio.</span></span> <span data-ttu-id="3e6fc-222">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-222">Run the app.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="3e6fc-223">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="3e6fc-223">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="3e6fc-224">Z wiersza polecenia w katalogu *CLI* Utwórz aplikację RCL i sieć Web.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-224">From a command prompt in the *cli* directory, build the RCL and web app.</span></span>

```dotnetcli
dotnet build
```

<span data-ttu-id="3e6fc-225">Przejdź do katalogu *WebApp1* i uruchom aplikację:</span><span class="sxs-lookup"><span data-stu-id="3e6fc-225">Move to the *WebApp1* directory and run the app:</span></span>

```dotnetcli
dotnet run
```

---

<span data-ttu-id="3e6fc-226">Postępuj zgodnie z instrukcjami w temacie [test WebApp1](#test-webapp1)</span><span class="sxs-lookup"><span data-stu-id="3e6fc-226">Follow the instructions in [Test WebApp1](#test-webapp1)</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="3e6fc-227">Utwórz RCL</span><span class="sxs-lookup"><span data-stu-id="3e6fc-227">Create an RCL</span></span>

<span data-ttu-id="3e6fc-228">W tej sekcji jest tworzony RCL.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-228">In this section, an RCL is created.</span></span> Razor<span data-ttu-id="3e6fc-229">pliki są dodawane do RCL.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-229"> files are added to the RCL.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3e6fc-230">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3e6fc-230">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3e6fc-231">Utwórz projekt RCL:</span><span class="sxs-lookup"><span data-stu-id="3e6fc-231">Create the RCL project:</span></span>

* <span data-ttu-id="3e6fc-232">Z menu **plik** programu Visual Studio wybierz pozycję **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-232">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="3e6fc-233">Wybierz **ASP.NET Core aplikacji sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-233">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="3e6fc-234">Nadaj aplikacji nazwę **RazorUIClassLib** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-234">Name the app **RazorUIClassLib** > **OK**.</span></span>
* <span data-ttu-id="3e6fc-235">Sprawdź, czy wybrano **ASP.NET Core 2,1** lub nowszą.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-235">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="3e6fc-236">Wybierz pozycję \*\* Razor Biblioteka klas\*\* > **OK**.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-236">Select **Razor Class Library** > **OK**.</span></span>
* <span data-ttu-id="3e6fc-237">Dodaj Razor plik widoku częściowego o nazwie *RazorUIClassLib/Areas/webfeature/Pages/Shared/_Message. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-237">Add a Razor partial view file named *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="3e6fc-238">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="3e6fc-238">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="3e6fc-239">W wierszu polecenia Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="3e6fc-239">From the command line, run the following:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="3e6fc-240">Poprzednie polecenia:</span><span class="sxs-lookup"><span data-stu-id="3e6fc-240">The preceding commands:</span></span>

* <span data-ttu-id="3e6fc-241">Tworzy `RazorUIClassLib` RCL.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-241">Creates the `RazorUIClassLib` RCL.</span></span>
* <span data-ttu-id="3e6fc-242">Tworzy Razor stronę _Message i dodaje ją do RCL.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-242">Creates a Razor _Message page, and adds it to the RCL.</span></span> <span data-ttu-id="3e6fc-243">`-np`Parametr tworzy stronę bez `PageModel` .</span><span class="sxs-lookup"><span data-stu-id="3e6fc-243">The `-np` parameter creates the page without a `PageModel`.</span></span>
* <span data-ttu-id="3e6fc-244">Tworzy plik [_ViewStart. cshtml](xref:mvc/views/layout#running-code-before-each-view) i dodaje go do RCL.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-244">Creates a [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) file and adds it to the RCL.</span></span>

<span data-ttu-id="3e6fc-245">Plik *_ViewStart. cshtml* jest wymagany do używania układu Razor projektu stron (który jest dodawany w następnej sekcji).</span><span class="sxs-lookup"><span data-stu-id="3e6fc-245">The *_ViewStart.cshtml* file is required to use the layout of the Razor Pages project (which is added in the next section).</span></span>

---

### <a name="add-razor-files-and-folders-to-the-project"></a><span data-ttu-id="3e6fc-246">Dodawanie Razor plików i folderów do projektu</span><span class="sxs-lookup"><span data-stu-id="3e6fc-246">Add Razor files and folders to the project</span></span>

* <span data-ttu-id="3e6fc-247">Zastąp znaczniki w *RazorUIClassLib/Areas/webfeature/Pages/Shared/_Message. cshtml* następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="3e6fc-247">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* <span data-ttu-id="3e6fc-248">Zastąp znaczniki w *RazorUIClassLib/Areas/webfeature/Pages/Strona1. cshtml* następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="3e6fc-248">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

  <span data-ttu-id="3e6fc-249">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`jest wymagany do korzystania z widoku częściowego ( `<partial name="_Message" />` ).</span><span class="sxs-lookup"><span data-stu-id="3e6fc-249">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` is required to use the partial view (`<partial name="_Message" />`).</span></span> <span data-ttu-id="3e6fc-250">Zamiast `@addTagHelper` dyrektywy, można dodać plik *_ViewImports. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="3e6fc-250">Rather than including the `@addTagHelper` directive, you can add a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="3e6fc-251">Przykład:</span><span class="sxs-lookup"><span data-stu-id="3e6fc-251">For example:</span></span>

  ```dotnetcli
  dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
  ```

  <span data-ttu-id="3e6fc-252">Aby uzyskać więcej informacji na temat *_ViewImports. cshtml*, zobacz [Importowanie wspólnych dyrektyw](xref:mvc/views/layout#importing-shared-directives)</span><span class="sxs-lookup"><span data-stu-id="3e6fc-252">For more information on *_ViewImports.cshtml*, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives)</span></span>

* <span data-ttu-id="3e6fc-253">Kompiluj bibliotekę klas, aby upewnić się, że nie występują błędy kompilatora:</span><span class="sxs-lookup"><span data-stu-id="3e6fc-253">Build the class library to verify there are no compiler errors:</span></span>

  ```dotnetcli
  dotnet build RazorUIClassLib
  ```

<span data-ttu-id="3e6fc-254">Dane wyjściowe kompilacji zawierają *RazorUIClassLib. dll* i *RazorUIClassLib. views. dll*.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-254">The build output contains *RazorUIClassLib.dll* and *RazorUIClassLib.Views.dll*.</span></span> <span data-ttu-id="3e6fc-255">*RazorUIClassLib. views. dll* zawiera skompilowaną Razor zawartość.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-255">*RazorUIClassLib.Views.dll* contains the compiled Razor content.</span></span>

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a><span data-ttu-id="3e6fc-256">Korzystanie z Razor biblioteki interfejsu użytkownika z Razor projektu stron</span><span class="sxs-lookup"><span data-stu-id="3e6fc-256">Use the Razor UI library from a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3e6fc-257">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3e6fc-257">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3e6fc-258">Tworzenie Razor aplikacji sieci Web dla stron:</span><span class="sxs-lookup"><span data-stu-id="3e6fc-258">Create the Razor Pages web app:</span></span>

* <span data-ttu-id="3e6fc-259">W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy rozwiązanie > **Dodaj** > **Nowy projekt**.  </span><span class="sxs-lookup"><span data-stu-id="3e6fc-259">From **Solution Explorer**, right-click the solution > **Add** >  **New Project**.</span></span>
* <span data-ttu-id="3e6fc-260">Wybierz **ASP.NET Core aplikacji sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-260">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="3e6fc-261">Nadaj aplikacji nazwę **WebApp1**.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-261">Name the app **WebApp1**.</span></span>
* <span data-ttu-id="3e6fc-262">Sprawdź, czy wybrano **ASP.NET Core 2,1** lub nowszą.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-262">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="3e6fc-263">Wybierz pozycję **aplikacja sieci Web** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-263">Select **Web Application** > **OK**.</span></span>

* <span data-ttu-id="3e6fc-264">W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy pozycję **WebApp1** i wybierz pozycję **Ustaw jako projekt startowy**.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-264">From **Solution Explorer**, right-click on **WebApp1** and select **Set as StartUp Project**.</span></span>
* <span data-ttu-id="3e6fc-265">W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy pozycję **WebApp1** i wybierz pozycję **kompilacja zależności** > **projektu zależności**.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-265">From **Solution Explorer**, right-click on **WebApp1** and select **Build Dependencies** > **Project Dependencies**.</span></span>
* <span data-ttu-id="3e6fc-266">Sprawdź **RazorUIClassLib** jako zależność **WebApp1**.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-266">Check **RazorUIClassLib** as a dependency of **WebApp1**.</span></span>
* <span data-ttu-id="3e6fc-267">W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy pozycję **WebApp1** i wybierz pozycję **Dodaj** > **odwołanie**.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-267">From **Solution Explorer**, right-click on **WebApp1** and select **Add** > **Reference**.</span></span>
* <span data-ttu-id="3e6fc-268">W oknie dialogowym **Menedżer odwołań** Sprawdź **RazorUIClassLib** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-268">In the **Reference Manager** dialog, check **RazorUIClassLib** > **OK**.</span></span>

<span data-ttu-id="3e6fc-269">Uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-269">Run the app.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="3e6fc-270">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="3e6fc-270">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="3e6fc-271">Utwórz Razor aplikację internetową stron i plik rozwiązania zawierający Razor aplikację Pages i RCL:</span><span class="sxs-lookup"><span data-stu-id="3e6fc-271">Create a Razor Pages web app and a solution file containing the Razor Pages app and the RCL:</span></span>

```dotnetcli
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

<span data-ttu-id="3e6fc-272">Kompiluj i uruchom aplikację sieci Web:</span><span class="sxs-lookup"><span data-stu-id="3e6fc-272">Build and run the web app:</span></span>

```dotnetcli
cd WebApp1
dotnet run
```

---

### <a name="test-webapp1"></a><span data-ttu-id="3e6fc-273">Test WebApp1</span><span class="sxs-lookup"><span data-stu-id="3e6fc-273">Test WebApp1</span></span>

<span data-ttu-id="3e6fc-274">Przejdź do `/MyFeature/Page1` strony, aby sprawdzić, czy Razor Biblioteka klas interfejsu użytkownika jest używana.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-274">Browse to `/MyFeature/Page1` to verify that the Razor UI class library is in use.</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="3e6fc-275">Zastąp widoki, częściowe widoki i strony</span><span class="sxs-lookup"><span data-stu-id="3e6fc-275">Override views, partial views, and pages</span></span>

<span data-ttu-id="3e6fc-276">Gdy widok, częściowy widok lub Razor Strona znajdują się zarówno w aplikacji sieci Web, jak i w RCL, Razor pierwszeństwo ma znacznik (plik *. cshtml* ) w aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-276">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="3e6fc-277">Na przykład Dodaj *WebApp1/Areas/webfeature/Pages/Strona1. cshtml* do WebApp1, a element Strona1 w WebApp1 będzie miał pierwszeństwo przed STRONA1 w RCL.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-277">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="3e6fc-278">W przykładowym pobieraniu Zmień nazwę *WebApp1/obszarów/MyFeature2* na *WebApp1/Areas/webfeature* , aby przetestować priorytet.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-278">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="3e6fc-279">Skopiuj widok części *RazorUIClassLib/Areas/Webfeature/Pages/Shared/_Message. cshtml* częściowo do *WebApp1/Areas/Webfeature/Pages/Shared/_Message. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-279">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="3e6fc-280">Zaktualizuj adiustację, aby wskazać nową lokalizację.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-280">Update the markup to indicate the new location.</span></span> <span data-ttu-id="3e6fc-281">Skompiluj i uruchom aplikację, aby sprawdzić, czy jest używana częściowa wersja aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-281">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="3e6fc-282">Układ stron RCL</span><span class="sxs-lookup"><span data-stu-id="3e6fc-282">RCL Pages layout</span></span>

<span data-ttu-id="3e6fc-283">Aby odwoływać się do zawartości RCL, tak jakby była częścią folderu *stron* aplikacji sieci Web, Utwórz projekt RCL o następującej strukturze plików:</span><span class="sxs-lookup"><span data-stu-id="3e6fc-283">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="3e6fc-284">*RazorUIClassLib/strony*</span><span class="sxs-lookup"><span data-stu-id="3e6fc-284">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="3e6fc-285">*RazorUIClassLib/strony/udostępnione*</span><span class="sxs-lookup"><span data-stu-id="3e6fc-285">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="3e6fc-286">Załóżmy, że *RazorUIClassLib/Pages/Shared* zawiera dwa częściowe pliki: *_Header. cshtml* i *_Footer. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="3e6fc-286">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="3e6fc-287">`<partial>`Tagi można dodać do pliku *_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="3e6fc-287">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="3e6fc-288">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="3e6fc-288">Additional resources</span></span>

* <xref:blazor/class-libraries>
