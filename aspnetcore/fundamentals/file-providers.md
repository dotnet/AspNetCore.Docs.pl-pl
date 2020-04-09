---
title: Dostawcy plików w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak ASP.NET Core abstrakcji dostępu do systemu plików za pomocą dostawców plików.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2020
uid: fundamentals/file-providers
ms.openlocfilehash: 25607bd534cae05a6c6b11fa6d8902faa3c0684c
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80751101"
---
# <a name="file-providers-in-aspnet-core"></a><span data-ttu-id="d06b3-103">Dostawcy plików w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d06b3-103">File Providers in ASP.NET Core</span></span>

<span data-ttu-id="d06b3-104">Przez [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="d06b3-104">By [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d06b3-105">ASP.NET Core abstrakcje dostęp do systemu plików za pomocą dostawców plików.</span><span class="sxs-lookup"><span data-stu-id="d06b3-105">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="d06b3-106">Dostawcy plików są używane w całej ASP.NET core framework.</span><span class="sxs-lookup"><span data-stu-id="d06b3-106">File Providers are used throughout the ASP.NET Core framework.</span></span> <span data-ttu-id="d06b3-107">Przykład:</span><span class="sxs-lookup"><span data-stu-id="d06b3-107">For example:</span></span>

* <span data-ttu-id="d06b3-108"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>udostępnia katalog główny [zawartości](xref:fundamentals/index#content-root) aplikacji i `IFileProvider` katalog główny sieci [Web](xref:fundamentals/index#web-root) jako typy.</span><span class="sxs-lookup"><span data-stu-id="d06b3-108"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="d06b3-109">[Oprogramowanie pośredniczące plików statycznych](xref:fundamentals/static-files) używa dostawców plików do lokalizowania plików statycznych.</span><span class="sxs-lookup"><span data-stu-id="d06b3-109">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="d06b3-110">[Razor](xref:mvc/views/razor) używa dostawców plików do lokalizowania stron i widoków.</span><span class="sxs-lookup"><span data-stu-id="d06b3-110">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="d06b3-111">Narzędzia .NET Core używają dostawców plików i wzorców glob, aby określić, które pliki powinny zostać opublikowane.</span><span class="sxs-lookup"><span data-stu-id="d06b3-111">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="d06b3-112">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d06b3-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="d06b3-113">Interfejsy dostawcy plików</span><span class="sxs-lookup"><span data-stu-id="d06b3-113">File Provider interfaces</span></span>

<span data-ttu-id="d06b3-114">Podstawowym interfejsem <xref:Microsoft.Extensions.FileProviders.IFileProvider>jest .</span><span class="sxs-lookup"><span data-stu-id="d06b3-114">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="d06b3-115">`IFileProvider`udostępnia metody na:</span><span class="sxs-lookup"><span data-stu-id="d06b3-115">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="d06b3-116">Uzyskiwanie informacji<xref:Microsoft.Extensions.FileProviders.IFileInfo>o pliku ( ).</span><span class="sxs-lookup"><span data-stu-id="d06b3-116">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="d06b3-117">Uzyskiwanie informacji<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>o katalogu ( ).</span><span class="sxs-lookup"><span data-stu-id="d06b3-117">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="d06b3-118">Konfigurowanie powiadomień o <xref:Microsoft.Extensions.Primitives.IChangeToken>zmianach (przy użyciu ).</span><span class="sxs-lookup"><span data-stu-id="d06b3-118">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="d06b3-119">`IFileInfo`zapewnia metody i właściwości do pracy z plikami:</span><span class="sxs-lookup"><span data-stu-id="d06b3-119">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="d06b3-120"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length>(w bajtach)</span><span class="sxs-lookup"><span data-stu-id="d06b3-120"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="d06b3-121"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified>Data</span><span class="sxs-lookup"><span data-stu-id="d06b3-121"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="d06b3-122">Można odczytać z pliku <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> przy użyciu metody.</span><span class="sxs-lookup"><span data-stu-id="d06b3-122">You can read from the file using the <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> method.</span></span>

<span data-ttu-id="d06b3-123">*FileProviderPróbuj* przykładową aplikację pokazuje, jak skonfigurować `Startup.ConfigureServices` dostawcę plików do użytku w całej aplikacji za pomocą [iniekcji zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="d06b3-123">The *FileProviderSample* sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="d06b3-124">Implementacje dostawcy plików</span><span class="sxs-lookup"><span data-stu-id="d06b3-124">File Provider implementations</span></span>

<span data-ttu-id="d06b3-125">W poniższej tabeli `IFileProvider`wymieniono implementacje programu .</span><span class="sxs-lookup"><span data-stu-id="d06b3-125">The following table lists implementations of `IFileProvider`.</span></span>

| <span data-ttu-id="d06b3-126">Wdrażanie</span><span class="sxs-lookup"><span data-stu-id="d06b3-126">Implementation</span></span> | <span data-ttu-id="d06b3-127">Opis</span><span class="sxs-lookup"><span data-stu-id="d06b3-127">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="d06b3-128">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="d06b3-128">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="d06b3-129">Służy do zapewnienia połączonego dostępu do plików i katalogów od jednego lub więcej innych dostawców.</span><span class="sxs-lookup"><span data-stu-id="d06b3-129">Used to provide combined access to files and directories from one or more other providers.</span></span> |
| [<span data-ttu-id="d06b3-130">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="d06b3-130">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="d06b3-131">Służy do uzyskiwania dostępu do plików osadzonych w złożeniach.</span><span class="sxs-lookup"><span data-stu-id="d06b3-131">Used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="d06b3-132">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="d06b3-132">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="d06b3-133">Służy do uzyskiwania dostępu do plików fizycznych systemu.</span><span class="sxs-lookup"><span data-stu-id="d06b3-133">Used to access the system's physical files.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="d06b3-134">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="d06b3-134">PhysicalFileProvider</span></span>

<span data-ttu-id="d06b3-135">Zapewnia <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> dostęp do fizycznego systemu plików.</span><span class="sxs-lookup"><span data-stu-id="d06b3-135">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="d06b3-136">`PhysicalFileProvider`używa <xref:System.IO.File?displayProperty=fullName> typu (dla dostawcy fizycznego) i obejmuje wszystkie ścieżki do katalogu i jego wiązki obrażeń.</span><span class="sxs-lookup"><span data-stu-id="d06b3-136">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="d06b3-137">Ten zakres uniemożliwia dostęp do systemu plików poza określonym katalogiem i jego kamiłę kamicy.</span><span class="sxs-lookup"><span data-stu-id="d06b3-137">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="d06b3-138">Najczęstszym scenariuszem tworzenia i `PhysicalFileProvider` używania jest `IFileProvider` żądanie w konstruktorze poprzez [iniekcję zależności.](xref:fundamentals/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="d06b3-138">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="d06b3-139">Podczas bezpośredniego tworzenia wystąpienia tego dostawcy wymagana jest bezwzględna ścieżka katalogu i służy jako ścieżka podstawowa dla wszystkich żądań wykonanych przy użyciu dostawcy.</span><span class="sxs-lookup"><span data-stu-id="d06b3-139">When instantiating this provider directly, an absolute directory path is required and serves as the base path for all requests made using the provider.</span></span> <span data-ttu-id="d06b3-140">Wzorce glob nie są obsługiwane w ścieżce katalogu.</span><span class="sxs-lookup"><span data-stu-id="d06b3-140">Glob patterns aren't supported in the directory path.</span></span>

<span data-ttu-id="d06b3-141">Poniższy kod pokazuje, `PhysicalFileProvider` jak używać do uzyskiwania zawartości katalogu i informacji o plikach:</span><span class="sxs-lookup"><span data-stu-id="d06b3-141">The following code shows how to use `PhysicalFileProvider` to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var filePath = Path.Combine("wwwroot", "js", "site.js");
var fileInfo = provider.GetFileInfo(filePath);
```

<span data-ttu-id="d06b3-142">Typy w poprzednim przykładzie:</span><span class="sxs-lookup"><span data-stu-id="d06b3-142">Types in the preceding example:</span></span>

* <span data-ttu-id="d06b3-143">`provider`jest `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="d06b3-143">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="d06b3-144">`contents`jest `IDirectoryContents`.</span><span class="sxs-lookup"><span data-stu-id="d06b3-144">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="d06b3-145">`fileInfo`jest `IFileInfo`.</span><span class="sxs-lookup"><span data-stu-id="d06b3-145">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="d06b3-146">Dostawca plików może służyć do iteracji za `applicationRoot` pośrednictwem `GetFileInfo` katalogu określonego przez lub wywołać, aby uzyskać informacje o pliku.</span><span class="sxs-lookup"><span data-stu-id="d06b3-146">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="d06b3-147">Wzory glob nie mogą być `GetFileInfo` przekazywane do metody.</span><span class="sxs-lookup"><span data-stu-id="d06b3-147">Glob patterns can't be passed to the `GetFileInfo` method.</span></span> <span data-ttu-id="d06b3-148">Dostawca plików nie ma dostępu `applicationRoot` poza katalogiem.</span><span class="sxs-lookup"><span data-stu-id="d06b3-148">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="d06b3-149">Przykładowa aplikacja *FileProviderSample* tworzy `Startup.ConfigureServices` dostawcę <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType>w metodzie przy użyciu:</span><span class="sxs-lookup"><span data-stu-id="d06b3-149">The *FileProviderSample* sample app creates the provider in the `Startup.ConfigureServices` method using <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType>:</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="d06b3-150">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="d06b3-150">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="d06b3-151">Jest <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> używany do uzyskiwania dostępu do plików osadzonych w zestawach.</span><span class="sxs-lookup"><span data-stu-id="d06b3-151">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="d06b3-152">Używa `ManifestEmbeddedFileProvider` manifestu skompilowanego do zestawu w celu odtworzenia oryginalnych ścieżek osadzonych plików.</span><span class="sxs-lookup"><span data-stu-id="d06b3-152">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="d06b3-153">Aby wygenerować manifest osadzonych plików:</span><span class="sxs-lookup"><span data-stu-id="d06b3-153">To generate a manifest of the embedded files:</span></span>

1. <span data-ttu-id="d06b3-154">Dodaj pakiet [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) NuGet do projektu.</span><span class="sxs-lookup"><span data-stu-id="d06b3-154">Add the [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) NuGet package to your project.</span></span>
1. <span data-ttu-id="d06b3-155">Ustaw `<GenerateEmbeddedFilesManifest>` właściwość `true`na .</span><span class="sxs-lookup"><span data-stu-id="d06b3-155">Set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="d06b3-156">Określ pliki, które mają być osadzane za pomocą [ \<>EmbeddedResource: ](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects)</span><span class="sxs-lookup"><span data-stu-id="d06b3-156">Specify the files to embed with [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

    [!code-xml[](file-providers/samples/3.x/FileProviderSample/FileProviderSample.csproj?highlight=5,13)]

<span data-ttu-id="d06b3-157">Użyj [wzorców glob,](#glob-patterns) aby określić jeden lub więcej plików do osadzenia w złożeniu.</span><span class="sxs-lookup"><span data-stu-id="d06b3-157">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="d06b3-158">*FileProviderPróbka* przykładowa `ManifestEmbeddedFileProvider` aplikacja tworzy i przekazuje aktualnie wykonywanyzestaw do konstruktora.</span><span class="sxs-lookup"><span data-stu-id="d06b3-158">The *FileProviderSample* sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="d06b3-159">*Startup.cs:*</span><span class="sxs-lookup"><span data-stu-id="d06b3-159">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="d06b3-160">Dodatkowe przeciążenia pozwalają na:</span><span class="sxs-lookup"><span data-stu-id="d06b3-160">Additional overloads allow you to:</span></span>

* <span data-ttu-id="d06b3-161">Określ względną ścieżkę pliku.</span><span class="sxs-lookup"><span data-stu-id="d06b3-161">Specify a relative file path.</span></span>
* <span data-ttu-id="d06b3-162">Zakres plików do ostatniej zmodyfikowanej daty.</span><span class="sxs-lookup"><span data-stu-id="d06b3-162">Scope files to a last modified date.</span></span>
* <span data-ttu-id="d06b3-163">Nazwij osadzony zasób zawierający osadzony manifest pliku.</span><span class="sxs-lookup"><span data-stu-id="d06b3-163">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="d06b3-164">Przeciążenie</span><span class="sxs-lookup"><span data-stu-id="d06b3-164">Overload</span></span> | <span data-ttu-id="d06b3-165">Opis</span><span class="sxs-lookup"><span data-stu-id="d06b3-165">Description</span></span> |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | <span data-ttu-id="d06b3-166">Akceptuje opcjonalny `root` parametr ścieżki względnej.</span><span class="sxs-lookup"><span data-stu-id="d06b3-166">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="d06b3-167">Określ `root` do zakresu <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> wywołania do tych zasobów w ramach podanej ścieżki.</span><span class="sxs-lookup"><span data-stu-id="d06b3-167">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | <span data-ttu-id="d06b3-168">Akceptuje opcjonalny `root` parametr ścieżki `lastModified` względnej<xref:System.DateTimeOffset>i parametr daty ( ).</span><span class="sxs-lookup"><span data-stu-id="d06b3-168">Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="d06b3-169">Data `lastModified` obejmuje datę ostatniej modyfikacji <xref:Microsoft.Extensions.FileProviders.IFileInfo> dla wystąpień <xref:Microsoft.Extensions.FileProviders.IFileProvider>zwróconych przez plik .</span><span class="sxs-lookup"><span data-stu-id="d06b3-169">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | <span data-ttu-id="d06b3-170">Akceptuje opcjonalną `root` ścieżkę `lastModified` względną, datę i `manifestName` parametry.</span><span class="sxs-lookup"><span data-stu-id="d06b3-170">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="d06b3-171">Reprezentuje `manifestName` nazwę osadzonego zasobu zawierającego manifest.</span><span class="sxs-lookup"><span data-stu-id="d06b3-171">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="d06b3-172">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="d06b3-172">CompositeFileProvider</span></span>

<span data-ttu-id="d06b3-173">Łączy <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> `IFileProvider` wystąpienia, uwidaczniając pojedynczy interfejs do pracy z plikami od wielu dostawców.</span><span class="sxs-lookup"><span data-stu-id="d06b3-173">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="d06b3-174">Podczas tworzenia `CompositeFileProvider`, przekazać jedno `IFileProvider` lub więcej wystąpień do jego konstruktora.</span><span class="sxs-lookup"><span data-stu-id="d06b3-174">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="d06b3-175">W *FileProviderSample* przykładowej `PhysicalFileProvider` aplikacji `ManifestEmbeddedFileProvider` a i `CompositeFileProvider` udostępnić pliki do zarejestrowanego w kontenerze usługi aplikacji.</span><span class="sxs-lookup"><span data-stu-id="d06b3-175">In the *FileProviderSample* sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container.</span></span> <span data-ttu-id="d06b3-176">W `Startup.ConfigureServices` metodzie projektu znajduje się następujący kod:</span><span class="sxs-lookup"><span data-stu-id="d06b3-176">The following code is found in the project's `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="d06b3-177">Uważaj na zmiany</span><span class="sxs-lookup"><span data-stu-id="d06b3-177">Watch for changes</span></span>

<span data-ttu-id="d06b3-178">Metoda <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> zawiera scenariusz, aby obejrzeć jeden lub więcej plików lub katalogów dla zmian.</span><span class="sxs-lookup"><span data-stu-id="d06b3-178">The <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="d06b3-179">Metoda: `Watch`</span><span class="sxs-lookup"><span data-stu-id="d06b3-179">The `Watch` method:</span></span>

* <span data-ttu-id="d06b3-180">Akceptuje ciąg ścieżki pliku, który może używać [wzorców glob](#glob-patterns) do określania wielu plików.</span><span class="sxs-lookup"><span data-stu-id="d06b3-180">Accepts a file path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span>
* <span data-ttu-id="d06b3-181">Zwraca <xref:Microsoft.Extensions.Primitives.IChangeToken>plik .</span><span class="sxs-lookup"><span data-stu-id="d06b3-181">Returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span>

<span data-ttu-id="d06b3-182">Wynikowy token zmiany udostępnia:</span><span class="sxs-lookup"><span data-stu-id="d06b3-182">The resulting change token exposes:</span></span>

* <span data-ttu-id="d06b3-183"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>&ndash;Właściwość, która może być kontrolowane, aby ustalić, czy nastąpiła zmiana.</span><span class="sxs-lookup"><span data-stu-id="d06b3-183"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>&ndash;A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="d06b3-184"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>&ndash;Wywoływane po wykryciu zmian do określonego ciągu ścieżki.</span><span class="sxs-lookup"><span data-stu-id="d06b3-184"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>&ndash;Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="d06b3-185">Każdy token zmiany wywołuje tylko skojarzone wywołanie zwrotne w odpowiedzi na pojedynczą zmianę.</span><span class="sxs-lookup"><span data-stu-id="d06b3-185">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="d06b3-186">Aby włączyć ciągłe monitorowanie, należy użyć <xref:System.Threading.Tasks.TaskCompletionSource`1> (pokazane `IChangeToken` poniżej) lub odtworzyć wystąpień w odpowiedzi na zmiany.</span><span class="sxs-lookup"><span data-stu-id="d06b3-186">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="d06b3-187">Przykładowa aplikacja *WatchConsole* zapisuje komunikat za każdym razem, gdy plik *txt* w katalogu *TextFiles* zostanie zmodyfikowany:</span><span class="sxs-lookup"><span data-stu-id="d06b3-187">The *WatchConsole* sample app writes a message whenever a *.txt* file in the *TextFiles* directory is modified:</span></span>

[!code-csharp[](file-providers/samples/3.x/WatchConsole/Program.cs?name=snippet1)]

<span data-ttu-id="d06b3-188">Niektóre systemy plików, takie jak kontenery platformy Docker i udziały sieciowe, mogą nie niezawodnie wysyłać powiadomień o zmianach.</span><span class="sxs-lookup"><span data-stu-id="d06b3-188">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="d06b3-189">Ustaw `DOTNET_USE_POLLING_FILE_WATCHER` zmienną `1` środowiskową na lub `true` sondować system plików pod kątem zmian co cztery sekundy (nie można ich skonfigurować).</span><span class="sxs-lookup"><span data-stu-id="d06b3-189">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

### <a name="glob-patterns"></a><span data-ttu-id="d06b3-190">Wzory globów</span><span class="sxs-lookup"><span data-stu-id="d06b3-190">Glob patterns</span></span>

<span data-ttu-id="d06b3-191">Ścieżki systemu plików używają wzorców symboli wieloznacznych zwanych *wzorami glob (lub globbing).*</span><span class="sxs-lookup"><span data-stu-id="d06b3-191">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="d06b3-192">Określ grupy plików z tymi wzorami.</span><span class="sxs-lookup"><span data-stu-id="d06b3-192">Specify groups of files with these patterns.</span></span> <span data-ttu-id="d06b3-193">Dwa symbole wieloznaczne są `*` i: `**`</span><span class="sxs-lookup"><span data-stu-id="d06b3-193">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="d06b3-194">Dopasowuje wszystko na bieżącym poziomie folderu, dowolną nazwę pliku lub dowolne rozszerzenie pliku.</span><span class="sxs-lookup"><span data-stu-id="d06b3-194">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="d06b3-195">Dopasowania są przerywane przez `/` i `.` znaki w ścieżce pliku.</span><span class="sxs-lookup"><span data-stu-id="d06b3-195">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="d06b3-196">Dopasowuje wszystko na wielu poziomach katalogów.</span><span class="sxs-lookup"><span data-stu-id="d06b3-196">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="d06b3-197">Może służyć do cyklicznego dopasowania wielu plików w hierarchii katalogów.</span><span class="sxs-lookup"><span data-stu-id="d06b3-197">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="d06b3-198">W poniższej tabeli przedstawiono typowe przykłady wzorców glob.</span><span class="sxs-lookup"><span data-stu-id="d06b3-198">The following table provides common examples of glob patterns.</span></span>

|<span data-ttu-id="d06b3-199">Wzorce</span><span class="sxs-lookup"><span data-stu-id="d06b3-199">Pattern</span></span>  |<span data-ttu-id="d06b3-200">Opis</span><span class="sxs-lookup"><span data-stu-id="d06b3-200">Description</span></span>  |
|---------|---------|
|`directory/file.txt`|<span data-ttu-id="d06b3-201">Pasuje do określonego pliku w określonym katalogu.</span><span class="sxs-lookup"><span data-stu-id="d06b3-201">Matches a specific file in a specific directory.</span></span>|
|`directory/*.txt`|<span data-ttu-id="d06b3-202">Dopasowuje wszystkie pliki z rozszerzeniem *txt* w określonym katalogu.</span><span class="sxs-lookup"><span data-stu-id="d06b3-202">Matches all files with *.txt* extension in a specific directory.</span></span>|
|`directory/*/appsettings.json`|<span data-ttu-id="d06b3-203">Dopasowuje wszystkie pliki *appsettings.json* w katalogach dokładnie jeden poziom poniżej folderu *katalogu.*</span><span class="sxs-lookup"><span data-stu-id="d06b3-203">Matches all *appsettings.json* files in directories exactly one level below the *directory* folder.</span></span>|
|`directory/**/*.txt`|<span data-ttu-id="d06b3-204">Dopasowuje wszystkie pliki z rozszerzeniem *txt* znalezionym w dowolnym miejscu w folderze *katalogu.*</span><span class="sxs-lookup"><span data-stu-id="d06b3-204">Matches all files with a *.txt* extension found anywhere under the *directory* folder.</span></span>|

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d06b3-205">ASP.NET Core abstrakcje dostęp do systemu plików za pomocą dostawców plików.</span><span class="sxs-lookup"><span data-stu-id="d06b3-205">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="d06b3-206">Dostawcy plików są używane w całej ASP.NET Core framework:</span><span class="sxs-lookup"><span data-stu-id="d06b3-206">File Providers are used throughout the ASP.NET Core framework:</span></span>

* <span data-ttu-id="d06b3-207"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment>udostępnia katalog główny [zawartości](xref:fundamentals/index#content-root) aplikacji i `IFileProvider` katalog główny sieci [Web](xref:fundamentals/index#web-root) jako typy.</span><span class="sxs-lookup"><span data-stu-id="d06b3-207"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="d06b3-208">[Oprogramowanie pośredniczące plików statycznych](xref:fundamentals/static-files) używa dostawców plików do lokalizowania plików statycznych.</span><span class="sxs-lookup"><span data-stu-id="d06b3-208">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="d06b3-209">[Razor](xref:mvc/views/razor) używa dostawców plików do lokalizowania stron i widoków.</span><span class="sxs-lookup"><span data-stu-id="d06b3-209">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="d06b3-210">Narzędzia .NET Core używają dostawców plików i wzorców glob, aby określić, które pliki powinny zostać opublikowane.</span><span class="sxs-lookup"><span data-stu-id="d06b3-210">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="d06b3-211">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d06b3-211">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="d06b3-212">Interfejsy dostawcy plików</span><span class="sxs-lookup"><span data-stu-id="d06b3-212">File Provider interfaces</span></span>

<span data-ttu-id="d06b3-213">Podstawowym interfejsem <xref:Microsoft.Extensions.FileProviders.IFileProvider>jest .</span><span class="sxs-lookup"><span data-stu-id="d06b3-213">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="d06b3-214">`IFileProvider`udostępnia metody na:</span><span class="sxs-lookup"><span data-stu-id="d06b3-214">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="d06b3-215">Uzyskiwanie informacji<xref:Microsoft.Extensions.FileProviders.IFileInfo>o pliku ( ).</span><span class="sxs-lookup"><span data-stu-id="d06b3-215">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="d06b3-216">Uzyskiwanie informacji<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>o katalogu ( ).</span><span class="sxs-lookup"><span data-stu-id="d06b3-216">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="d06b3-217">Konfigurowanie powiadomień o <xref:Microsoft.Extensions.Primitives.IChangeToken>zmianach (przy użyciu ).</span><span class="sxs-lookup"><span data-stu-id="d06b3-217">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="d06b3-218">`IFileInfo`zapewnia metody i właściwości do pracy z plikami:</span><span class="sxs-lookup"><span data-stu-id="d06b3-218">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="d06b3-219"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length>(w bajtach)</span><span class="sxs-lookup"><span data-stu-id="d06b3-219"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="d06b3-220"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified>Data</span><span class="sxs-lookup"><span data-stu-id="d06b3-220"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="d06b3-221">Można odczytać z pliku przy użyciu [metody IFileInfo.CreateReadStream.](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*)</span><span class="sxs-lookup"><span data-stu-id="d06b3-221">You can read from the file using the [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) method.</span></span>

<span data-ttu-id="d06b3-222">Przykładowa aplikacja pokazuje, jak skonfigurować `Startup.ConfigureServices` dostawcę plików do użytku w całej aplikacji za pomocą [iniekcji zależności.](xref:fundamentals/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="d06b3-222">The sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="d06b3-223">Implementacje dostawcy plików</span><span class="sxs-lookup"><span data-stu-id="d06b3-223">File Provider implementations</span></span>

<span data-ttu-id="d06b3-224">Dostępne są `IFileProvider` trzy implementacje.</span><span class="sxs-lookup"><span data-stu-id="d06b3-224">Three implementations of `IFileProvider` are available.</span></span>

| <span data-ttu-id="d06b3-225">Wdrażanie</span><span class="sxs-lookup"><span data-stu-id="d06b3-225">Implementation</span></span> | <span data-ttu-id="d06b3-226">Opis</span><span class="sxs-lookup"><span data-stu-id="d06b3-226">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="d06b3-227">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="d06b3-227">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="d06b3-228">Dostawca fizyczny jest używany do uzyskiwania dostępu do plików fizycznych systemu.</span><span class="sxs-lookup"><span data-stu-id="d06b3-228">The physical provider is used to access the system's physical files.</span></span> |
| [<span data-ttu-id="d06b3-229">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="d06b3-229">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="d06b3-230">Manifest osadzony dostawca służy do uzyskiwania dostępu do plików osadzonych w zestawach.</span><span class="sxs-lookup"><span data-stu-id="d06b3-230">The manifest embedded provider is used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="d06b3-231">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="d06b3-231">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="d06b3-232">Dostawca złożony służy do zapewnienia połączonego dostępu do plików i katalogów od jednego lub więcej innych dostawców.</span><span class="sxs-lookup"><span data-stu-id="d06b3-232">The composite provider is used to provide combined access to files and directories from one or more other providers.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="d06b3-233">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="d06b3-233">PhysicalFileProvider</span></span>

<span data-ttu-id="d06b3-234">Zapewnia <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> dostęp do fizycznego systemu plików.</span><span class="sxs-lookup"><span data-stu-id="d06b3-234">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="d06b3-235">`PhysicalFileProvider`używa <xref:System.IO.File?displayProperty=fullName> typu (dla dostawcy fizycznego) i obejmuje wszystkie ścieżki do katalogu i jego wiązki obrażeń.</span><span class="sxs-lookup"><span data-stu-id="d06b3-235">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="d06b3-236">Ten zakres uniemożliwia dostęp do systemu plików poza określonym katalogiem i jego kamiłę kamicy.</span><span class="sxs-lookup"><span data-stu-id="d06b3-236">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="d06b3-237">Najczęstszym scenariuszem tworzenia i `PhysicalFileProvider` używania jest `IFileProvider` żądanie w konstruktorze poprzez [iniekcję zależności.](xref:fundamentals/dependency-injection)</span><span class="sxs-lookup"><span data-stu-id="d06b3-237">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="d06b3-238">Podczas tworzenia wystąpienia tego dostawcy bezpośrednio, ścieżka katalogu jest wymagana i służy jako ścieżka podstawowa dla wszystkich żądań wykonanych przy użyciu dostawcy.</span><span class="sxs-lookup"><span data-stu-id="d06b3-238">When instantiating this provider directly, a directory path is required and serves as the base path for all requests made using the provider.</span></span>

<span data-ttu-id="d06b3-239">Poniższy kod pokazuje, `PhysicalFileProvider` jak utworzyć i używać go do uzyskania zawartości katalogu i informacji o pliku:</span><span class="sxs-lookup"><span data-stu-id="d06b3-239">The following code shows how to create a `PhysicalFileProvider` and use it to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

<span data-ttu-id="d06b3-240">Typy w poprzednim przykładzie:</span><span class="sxs-lookup"><span data-stu-id="d06b3-240">Types in the preceding example:</span></span>

* <span data-ttu-id="d06b3-241">`provider`jest `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="d06b3-241">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="d06b3-242">`contents`jest `IDirectoryContents`.</span><span class="sxs-lookup"><span data-stu-id="d06b3-242">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="d06b3-243">`fileInfo`jest `IFileInfo`.</span><span class="sxs-lookup"><span data-stu-id="d06b3-243">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="d06b3-244">Dostawca plików może służyć do iteracji za `applicationRoot` pośrednictwem `GetFileInfo` katalogu określonego przez lub wywołać, aby uzyskać informacje o pliku.</span><span class="sxs-lookup"><span data-stu-id="d06b3-244">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="d06b3-245">Dostawca plików nie ma dostępu `applicationRoot` poza katalogiem.</span><span class="sxs-lookup"><span data-stu-id="d06b3-245">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="d06b3-246">Przykładowa aplikacja tworzy dostawcę w `Startup.ConfigureServices` klasie aplikacji przy użyciu [IHostingEnvironment.ContentRootFileProvider:](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider)</span><span class="sxs-lookup"><span data-stu-id="d06b3-246">The sample app creates the provider in the app's `Startup.ConfigureServices` class using [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="d06b3-247">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="d06b3-247">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="d06b3-248">Jest <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> używany do uzyskiwania dostępu do plików osadzonych w zestawach.</span><span class="sxs-lookup"><span data-stu-id="d06b3-248">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="d06b3-249">Używa `ManifestEmbeddedFileProvider` manifestu skompilowanego do zestawu w celu odtworzenia oryginalnych ścieżek osadzonych plików.</span><span class="sxs-lookup"><span data-stu-id="d06b3-249">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="d06b3-250">Aby wygenerować manifest osadzonych `<GenerateEmbeddedFilesManifest>` plików, `true`ustaw właściwość na .</span><span class="sxs-lookup"><span data-stu-id="d06b3-250">To generate a manifest of the embedded files, set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="d06b3-251">Określ pliki, które mają być osadzane za pomocą [ &lt;programu EmbeddedResource:&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects)</span><span class="sxs-lookup"><span data-stu-id="d06b3-251">Specify the files to embed with [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

<span data-ttu-id="d06b3-252">Użyj [wzorców glob,](#glob-patterns) aby określić jeden lub więcej plików do osadzenia w złożeniu.</span><span class="sxs-lookup"><span data-stu-id="d06b3-252">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="d06b3-253">Przykładowa aplikacja `ManifestEmbeddedFileProvider` tworzy i przekazuje aktualnie wykonywanyzestaw do jego konstruktora.</span><span class="sxs-lookup"><span data-stu-id="d06b3-253">The sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="d06b3-254">*Startup.cs:*</span><span class="sxs-lookup"><span data-stu-id="d06b3-254">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="d06b3-255">Dodatkowe przeciążenia pozwalają na:</span><span class="sxs-lookup"><span data-stu-id="d06b3-255">Additional overloads allow you to:</span></span>

* <span data-ttu-id="d06b3-256">Określ względną ścieżkę pliku.</span><span class="sxs-lookup"><span data-stu-id="d06b3-256">Specify a relative file path.</span></span>
* <span data-ttu-id="d06b3-257">Zakres plików do ostatniej zmodyfikowanej daty.</span><span class="sxs-lookup"><span data-stu-id="d06b3-257">Scope files to a last modified date.</span></span>
* <span data-ttu-id="d06b3-258">Nazwij osadzony zasób zawierający osadzony manifest pliku.</span><span class="sxs-lookup"><span data-stu-id="d06b3-258">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="d06b3-259">Przeciążenie</span><span class="sxs-lookup"><span data-stu-id="d06b3-259">Overload</span></span> | <span data-ttu-id="d06b3-260">Opis</span><span class="sxs-lookup"><span data-stu-id="d06b3-260">Description</span></span> |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | <span data-ttu-id="d06b3-261">Akceptuje opcjonalny `root` parametr ścieżki względnej.</span><span class="sxs-lookup"><span data-stu-id="d06b3-261">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="d06b3-262">Określ `root` do zakresu <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> wywołania do tych zasobów w ramach podanej ścieżki.</span><span class="sxs-lookup"><span data-stu-id="d06b3-262">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | <span data-ttu-id="d06b3-263">Akceptuje opcjonalny `root` parametr ścieżki `lastModified` względnej<xref:System.DateTimeOffset>i parametr daty ( ).</span><span class="sxs-lookup"><span data-stu-id="d06b3-263">Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="d06b3-264">Data `lastModified` obejmuje datę ostatniej modyfikacji <xref:Microsoft.Extensions.FileProviders.IFileInfo> dla wystąpień <xref:Microsoft.Extensions.FileProviders.IFileProvider>zwróconych przez plik .</span><span class="sxs-lookup"><span data-stu-id="d06b3-264">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | <span data-ttu-id="d06b3-265">Akceptuje opcjonalną `root` ścieżkę `lastModified` względną, datę i `manifestName` parametry.</span><span class="sxs-lookup"><span data-stu-id="d06b3-265">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="d06b3-266">Reprezentuje `manifestName` nazwę osadzonego zasobu zawierającego manifest.</span><span class="sxs-lookup"><span data-stu-id="d06b3-266">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="d06b3-267">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="d06b3-267">CompositeFileProvider</span></span>

<span data-ttu-id="d06b3-268">Łączy <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> `IFileProvider` wystąpienia, uwidaczniając pojedynczy interfejs do pracy z plikami od wielu dostawców.</span><span class="sxs-lookup"><span data-stu-id="d06b3-268">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="d06b3-269">Podczas tworzenia `CompositeFileProvider`, przekazać jedno `IFileProvider` lub więcej wystąpień do jego konstruktora.</span><span class="sxs-lookup"><span data-stu-id="d06b3-269">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="d06b3-270">W przykładowej aplikacji `PhysicalFileProvider` a `ManifestEmbeddedFileProvider` i a `CompositeFileProvider` udostępnić pliki do zarejestrowanego w kontenerze usługi aplikacji:</span><span class="sxs-lookup"><span data-stu-id="d06b3-270">In the sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container:</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="d06b3-271">Uważaj na zmiany</span><span class="sxs-lookup"><span data-stu-id="d06b3-271">Watch for changes</span></span>

<span data-ttu-id="d06b3-272">[IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) Metoda zapewnia scenariusz do oglądania jednego lub więcej plików lub katalogów dla zmian.</span><span class="sxs-lookup"><span data-stu-id="d06b3-272">The [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="d06b3-273">`Watch`akceptuje ciąg ścieżki, który może używać [wzorców glob](#glob-patterns) do określania wielu plików.</span><span class="sxs-lookup"><span data-stu-id="d06b3-273">`Watch` accepts a path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span> <span data-ttu-id="d06b3-274">`Watch`zwraca <xref:Microsoft.Extensions.Primitives.IChangeToken>plik .</span><span class="sxs-lookup"><span data-stu-id="d06b3-274">`Watch` returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span> <span data-ttu-id="d06b3-275">Token zmiany udostępnia:</span><span class="sxs-lookup"><span data-stu-id="d06b3-275">The change token exposes:</span></span>

* <span data-ttu-id="d06b3-276"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>&ndash; Właściwość, która może być kontrolowane, aby ustalić, czy nastąpiła zmiana.</span><span class="sxs-lookup"><span data-stu-id="d06b3-276"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> &ndash; A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="d06b3-277"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>&ndash; Wywoływane po wykryciu zmian do określonego ciągu ścieżki.</span><span class="sxs-lookup"><span data-stu-id="d06b3-277"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*> &ndash; Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="d06b3-278">Każdy token zmiany wywołuje tylko skojarzone wywołanie zwrotne w odpowiedzi na pojedynczą zmianę.</span><span class="sxs-lookup"><span data-stu-id="d06b3-278">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="d06b3-279">Aby włączyć ciągłe monitorowanie, należy użyć <xref:System.Threading.Tasks.TaskCompletionSource`1> (pokazane `IChangeToken` poniżej) lub odtworzyć wystąpień w odpowiedzi na zmiany.</span><span class="sxs-lookup"><span data-stu-id="d06b3-279">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="d06b3-280">W przykładowej aplikacji aplikacja konsoli *WatchConsole* jest skonfigurowana do wyświetlania komunikatu za każdym razem, gdy plik tekstowy jest modyfikowany:</span><span class="sxs-lookup"><span data-stu-id="d06b3-280">In the sample app, the *WatchConsole* console app is configured to display a message whenever a text file is modified:</span></span>

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

<span data-ttu-id="d06b3-281">Niektóre systemy plików, takie jak kontenery platformy Docker i udziały sieciowe, mogą nie niezawodnie wysyłać powiadomień o zmianach.</span><span class="sxs-lookup"><span data-stu-id="d06b3-281">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="d06b3-282">Ustaw `DOTNET_USE_POLLING_FILE_WATCHER` zmienną `1` środowiskową na lub `true` sondować system plików pod kątem zmian co cztery sekundy (nie można ich skonfigurować).</span><span class="sxs-lookup"><span data-stu-id="d06b3-282">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

## <a name="glob-patterns"></a><span data-ttu-id="d06b3-283">Wzory globów</span><span class="sxs-lookup"><span data-stu-id="d06b3-283">Glob patterns</span></span>

<span data-ttu-id="d06b3-284">Ścieżki systemu plików używają wzorców symboli wieloznacznych zwanych *wzorami glob (lub globbing).*</span><span class="sxs-lookup"><span data-stu-id="d06b3-284">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="d06b3-285">Określ grupy plików z tymi wzorami.</span><span class="sxs-lookup"><span data-stu-id="d06b3-285">Specify groups of files with these patterns.</span></span> <span data-ttu-id="d06b3-286">Dwa symbole wieloznaczne są `*` i: `**`</span><span class="sxs-lookup"><span data-stu-id="d06b3-286">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="d06b3-287">Dopasowuje wszystko na bieżącym poziomie folderu, dowolną nazwę pliku lub dowolne rozszerzenie pliku.</span><span class="sxs-lookup"><span data-stu-id="d06b3-287">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="d06b3-288">Dopasowania są przerywane przez `/` i `.` znaki w ścieżce pliku.</span><span class="sxs-lookup"><span data-stu-id="d06b3-288">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="d06b3-289">Dopasowuje wszystko na wielu poziomach katalogów.</span><span class="sxs-lookup"><span data-stu-id="d06b3-289">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="d06b3-290">Może służyć do cyklicznego dopasowania wielu plików w hierarchii katalogów.</span><span class="sxs-lookup"><span data-stu-id="d06b3-290">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="d06b3-291">**Przykłady wzorca Glob**</span><span class="sxs-lookup"><span data-stu-id="d06b3-291">**Glob pattern examples**</span></span>

**`directory/file.txt`**  
<span data-ttu-id="d06b3-292">Pasuje do określonego pliku w określonym katalogu.</span><span class="sxs-lookup"><span data-stu-id="d06b3-292">Matches a specific file in a specific directory.</span></span>

**`directory/*.txt`**  
<span data-ttu-id="d06b3-293">Dopasowuje wszystkie pliki z rozszerzeniem *txt* w określonym katalogu.</span><span class="sxs-lookup"><span data-stu-id="d06b3-293">Matches all files with *.txt* extension in a specific directory.</span></span>

**`directory/*/appsettings.json`**  
<span data-ttu-id="d06b3-294">Dopasowuje wszystkie `appsettings.json` pliki w katalogach dokładnie jeden poziom poniżej folderu *katalogu.*</span><span class="sxs-lookup"><span data-stu-id="d06b3-294">Matches all `appsettings.json` files in directories exactly one level below the *directory* folder.</span></span>

**`directory/**/*.txt`**  
<span data-ttu-id="d06b3-295">Dopasowuje wszystkie pliki z rozszerzeniem *txt* znajdującym się w dowolnym miejscu w folderze *katalogu.*</span><span class="sxs-lookup"><span data-stu-id="d06b3-295">Matches all files with *.txt* extension found anywhere under the *directory* folder.</span></span>

::: moniker-end
