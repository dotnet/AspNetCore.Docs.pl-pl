---
title: Dostawcy plików w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak ASP.NET Core abstrakcji dostępu do systemu plików przy użyciu dostawców plików.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/file-providers
ms.openlocfilehash: 2f1151d7854aeeb3e315d0de2b0be5267fe2e8f0
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776288"
---
# <a name="file-providers-in-aspnet-core"></a><span data-ttu-id="7995e-103">Dostawcy plików w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7995e-103">File Providers in ASP.NET Core</span></span>

<span data-ttu-id="7995e-104">Przez [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="7995e-104">By [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7995e-105">ASP.NET Core abstrakcję dostępu systemu plików przy użyciu dostawców plików.</span><span class="sxs-lookup"><span data-stu-id="7995e-105">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="7995e-106">Dostawcy plików są używani w całym ASP.NET Core Framework.</span><span class="sxs-lookup"><span data-stu-id="7995e-106">File Providers are used throughout the ASP.NET Core framework.</span></span> <span data-ttu-id="7995e-107">Przykład:</span><span class="sxs-lookup"><span data-stu-id="7995e-107">For example:</span></span>

* <span data-ttu-id="7995e-108"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>udostępnia [katalog główny zawartości](xref:fundamentals/index#content-root) aplikacji i [katalogu głównego sieci Web](xref:fundamentals/index#web-root) jako `IFileProvider` typy.</span><span class="sxs-lookup"><span data-stu-id="7995e-108"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="7995e-109">[Oprogramowanie pośredniczące plików statycznych](xref:fundamentals/static-files) używa dostawców plików do lokalizowania plików statycznych.</span><span class="sxs-lookup"><span data-stu-id="7995e-109">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="7995e-110">[Razor](xref:mvc/views/razor) używa dostawców plików do lokalizowania stron i widoków.</span><span class="sxs-lookup"><span data-stu-id="7995e-110">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="7995e-111">Narzędzia .NET Core używają dostawców plików i wzorców globalizowania, aby określić, które pliki powinny zostać opublikowane.</span><span class="sxs-lookup"><span data-stu-id="7995e-111">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="7995e-112">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7995e-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="7995e-113">Interfejsy dostawcy plików</span><span class="sxs-lookup"><span data-stu-id="7995e-113">File Provider interfaces</span></span>

<span data-ttu-id="7995e-114">Podstawowy interfejs to <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="7995e-114">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="7995e-115">`IFileProvider`udostępnia metody:</span><span class="sxs-lookup"><span data-stu-id="7995e-115">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="7995e-116">Uzyskaj informacje o pliku<xref:Microsoft.Extensions.FileProviders.IFileInfo>().</span><span class="sxs-lookup"><span data-stu-id="7995e-116">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="7995e-117">Uzyskaj informacje o katalogu<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>().</span><span class="sxs-lookup"><span data-stu-id="7995e-117">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="7995e-118">Skonfiguruj powiadomienia o zmianach (przy <xref:Microsoft.Extensions.Primitives.IChangeToken>użyciu).</span><span class="sxs-lookup"><span data-stu-id="7995e-118">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="7995e-119">`IFileInfo`zapewnia metody i właściwości do pracy z plikami:</span><span class="sxs-lookup"><span data-stu-id="7995e-119">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="7995e-120"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length>(w bajtach)</span><span class="sxs-lookup"><span data-stu-id="7995e-120"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="7995e-121"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified>dniu</span><span class="sxs-lookup"><span data-stu-id="7995e-121"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="7995e-122">Można odczytać z pliku za pomocą <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> metody.</span><span class="sxs-lookup"><span data-stu-id="7995e-122">You can read from the file using the <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> method.</span></span>

<span data-ttu-id="7995e-123">Przykładowa aplikacja *FileProviderSample* pokazuje, jak skonfigurować dostawcę plików w programie `Startup.ConfigureServices` do użycia w całej aplikacji za pomocą [iniekcji zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="7995e-123">The *FileProviderSample* sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="7995e-124">Implementacje dostawcy plików</span><span class="sxs-lookup"><span data-stu-id="7995e-124">File Provider implementations</span></span>

<span data-ttu-id="7995e-125">W poniższej tabeli wymieniono implementacje programu `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="7995e-125">The following table lists implementations of `IFileProvider`.</span></span>

| <span data-ttu-id="7995e-126">Wdrażanie</span><span class="sxs-lookup"><span data-stu-id="7995e-126">Implementation</span></span> | <span data-ttu-id="7995e-127">Opis</span><span class="sxs-lookup"><span data-stu-id="7995e-127">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="7995e-128">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="7995e-128">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="7995e-129">Służy do zapewniania połączonego dostępu do plików i katalogów z co najmniej jednego dostawcy.</span><span class="sxs-lookup"><span data-stu-id="7995e-129">Used to provide combined access to files and directories from one or more other providers.</span></span> |
| [<span data-ttu-id="7995e-130">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="7995e-130">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="7995e-131">Służy do uzyskiwania dostępu do plików osadzonych w zestawach.</span><span class="sxs-lookup"><span data-stu-id="7995e-131">Used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="7995e-132">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="7995e-132">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="7995e-133">Służy do uzyskiwania dostępu do plików fizycznych systemu.</span><span class="sxs-lookup"><span data-stu-id="7995e-133">Used to access the system's physical files.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="7995e-134">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="7995e-134">PhysicalFileProvider</span></span>

<span data-ttu-id="7995e-135"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> Zapewnia dostęp do fizycznego systemu plików.</span><span class="sxs-lookup"><span data-stu-id="7995e-135">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="7995e-136">`PhysicalFileProvider`używa <xref:System.IO.File?displayProperty=fullName> typu (dla dostawcy fizycznego) i zakresy wszystkie ścieżki do katalogu i jego elementów podrzędnych.</span><span class="sxs-lookup"><span data-stu-id="7995e-136">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="7995e-137">Takie Określanie zakresu uniemożliwia dostęp do systemu plików poza określonym katalogiem i jego elementami podrzędnymi.</span><span class="sxs-lookup"><span data-stu-id="7995e-137">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="7995e-138">Najbardziej typowym scenariuszem tworzenia i używania elementu `PhysicalFileProvider` jest zażądanie `IFileProvider` w konstruktorze przy użyciu [iniekcji zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="7995e-138">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="7995e-139">W przypadku bezpośredniego tworzenia wystąpienia tego dostawcy wymagana jest bezwzględna ścieżka do katalogu i służy jako ścieżka podstawowa dla wszystkich żądań wysyłanych przy użyciu dostawcy.</span><span class="sxs-lookup"><span data-stu-id="7995e-139">When instantiating this provider directly, an absolute directory path is required and serves as the base path for all requests made using the provider.</span></span> <span data-ttu-id="7995e-140">Wzorce globalizowania nie są obsługiwane w ścieżce katalogu.</span><span class="sxs-lookup"><span data-stu-id="7995e-140">Glob patterns aren't supported in the directory path.</span></span>

<span data-ttu-id="7995e-141">Poniższy kod przedstawia sposób korzystania `PhysicalFileProvider` z programu w celu uzyskania zawartości katalogu i informacji o pliku:</span><span class="sxs-lookup"><span data-stu-id="7995e-141">The following code shows how to use `PhysicalFileProvider` to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var filePath = Path.Combine("wwwroot", "js", "site.js");
var fileInfo = provider.GetFileInfo(filePath);
```

<span data-ttu-id="7995e-142">Typy w poprzednim przykładzie:</span><span class="sxs-lookup"><span data-stu-id="7995e-142">Types in the preceding example:</span></span>

* <span data-ttu-id="7995e-143">`provider`jest `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="7995e-143">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="7995e-144">`contents`jest `IDirectoryContents`.</span><span class="sxs-lookup"><span data-stu-id="7995e-144">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="7995e-145">`fileInfo`jest `IFileInfo`.</span><span class="sxs-lookup"><span data-stu-id="7995e-145">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="7995e-146">Dostawca plików może służyć do iteracji przez katalog określony przez `applicationRoot` lub wywołanie `GetFileInfo` w celu uzyskania informacji o pliku.</span><span class="sxs-lookup"><span data-stu-id="7995e-146">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="7995e-147">Nie można przekazywać wzorców globalizowania do `GetFileInfo` metody.</span><span class="sxs-lookup"><span data-stu-id="7995e-147">Glob patterns can't be passed to the `GetFileInfo` method.</span></span> <span data-ttu-id="7995e-148">Dostawca plików nie ma dostępu poza `applicationRoot` katalogiem.</span><span class="sxs-lookup"><span data-stu-id="7995e-148">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="7995e-149">Przykładowa aplikacja *FileProviderSample* tworzy dostawcę w `Startup.ConfigureServices` metodzie przy użyciu <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType>:</span><span class="sxs-lookup"><span data-stu-id="7995e-149">The *FileProviderSample* sample app creates the provider in the `Startup.ConfigureServices` method using <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType>:</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="7995e-150">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="7995e-150">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="7995e-151"><xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> Służy do uzyskiwania dostępu do plików osadzonych w zestawach.</span><span class="sxs-lookup"><span data-stu-id="7995e-151">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="7995e-152">`ManifestEmbeddedFileProvider` Używa manifestu skompilowanego w zestawie, aby odtworzyć oryginalne ścieżki osadzonych plików.</span><span class="sxs-lookup"><span data-stu-id="7995e-152">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="7995e-153">Aby wygenerować manifest osadzonych plików:</span><span class="sxs-lookup"><span data-stu-id="7995e-153">To generate a manifest of the embedded files:</span></span>

1. <span data-ttu-id="7995e-154">Dodaj pakiet NuGet [Microsoft. Extensions. FileProviders. Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) do projektu.</span><span class="sxs-lookup"><span data-stu-id="7995e-154">Add the [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) NuGet package to your project.</span></span>
1. <span data-ttu-id="7995e-155">Ustaw `<GenerateEmbeddedFilesManifest>` właściwość na `true`wartość.</span><span class="sxs-lookup"><span data-stu-id="7995e-155">Set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="7995e-156">Określ pliki do osadzenia przy użyciu [ \<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span><span class="sxs-lookup"><span data-stu-id="7995e-156">Specify the files to embed with [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

    [!code-xml[](file-providers/samples/3.x/FileProviderSample/FileProviderSample.csproj?highlight=5,13)]

<span data-ttu-id="7995e-157">Użyj [wzorców globalizowania](#glob-patterns) , aby określić jeden lub więcej plików do osadzenia w zestawie.</span><span class="sxs-lookup"><span data-stu-id="7995e-157">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="7995e-158">Aplikacja Przykładowa *FileProviderSample* tworzy `ManifestEmbeddedFileProvider` i przekazuje aktualnie wykonywany zestaw do jego konstruktora.</span><span class="sxs-lookup"><span data-stu-id="7995e-158">The *FileProviderSample* sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="7995e-159">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="7995e-159">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="7995e-160">Dodatkowe przeciążenia umożliwiają:</span><span class="sxs-lookup"><span data-stu-id="7995e-160">Additional overloads allow you to:</span></span>

* <span data-ttu-id="7995e-161">Określ względną ścieżkę pliku.</span><span class="sxs-lookup"><span data-stu-id="7995e-161">Specify a relative file path.</span></span>
* <span data-ttu-id="7995e-162">Zakres plików do daty ostatniej modyfikacji.</span><span class="sxs-lookup"><span data-stu-id="7995e-162">Scope files to a last modified date.</span></span>
* <span data-ttu-id="7995e-163">Nazwij osadzony zasób zawierający manifest pliku osadzonego.</span><span class="sxs-lookup"><span data-stu-id="7995e-163">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="7995e-164">Występują</span><span class="sxs-lookup"><span data-stu-id="7995e-164">Overload</span></span> | <span data-ttu-id="7995e-165">Opis</span><span class="sxs-lookup"><span data-stu-id="7995e-165">Description</span></span> |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | <span data-ttu-id="7995e-166">Akceptuje opcjonalny `root` parametr ścieżki względnej.</span><span class="sxs-lookup"><span data-stu-id="7995e-166">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="7995e-167">`root` Określ do zakresu wywołania <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> do tych zasobów w ramach podanej ścieżki.</span><span class="sxs-lookup"><span data-stu-id="7995e-167">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | <span data-ttu-id="7995e-168">Akceptuje opcjonalny `root` parametr ścieżki względnej i parametr `lastModified` Date (<xref:System.DateTimeOffset>).</span><span class="sxs-lookup"><span data-stu-id="7995e-168">Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="7995e-169">`lastModified` Data zakresy daty ostatniej modyfikacji dla <xref:Microsoft.Extensions.FileProviders.IFileInfo> wystąpień zwracanych przez <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="7995e-169">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | <span data-ttu-id="7995e-170">Akceptuje opcjonalną `root` ścieżkę względną `lastModified` , datę i `manifestName` parametry.</span><span class="sxs-lookup"><span data-stu-id="7995e-170">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="7995e-171">`manifestName` Reprezentuje nazwę zasobu osadzonego zawierającego manifest.</span><span class="sxs-lookup"><span data-stu-id="7995e-171">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="7995e-172">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="7995e-172">CompositeFileProvider</span></span>

<span data-ttu-id="7995e-173"><xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> Łączy `IFileProvider` wystąpienia, uwidaczniając pojedynczy interfejs do pracy z plikami z wielu dostawców.</span><span class="sxs-lookup"><span data-stu-id="7995e-173">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="7995e-174">Podczas tworzenia `CompositeFileProvider`, należy przekazać co najmniej jedno `IFileProvider` wystąpienie do jego konstruktora.</span><span class="sxs-lookup"><span data-stu-id="7995e-174">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="7995e-175">W przykładowej aplikacji *FileProviderSample* `PhysicalFileProvider` i `ManifestEmbeddedFileProvider` Podaj pliki do `CompositeFileProvider` zarejestrowanego w kontenerze usługi aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7995e-175">In the *FileProviderSample* sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container.</span></span> <span data-ttu-id="7995e-176">W `Startup.ConfigureServices` metodzie projektu znaleziono następujący kod:</span><span class="sxs-lookup"><span data-stu-id="7995e-176">The following code is found in the project's `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="7995e-177">Obejrzyj zmiany</span><span class="sxs-lookup"><span data-stu-id="7995e-177">Watch for changes</span></span>

<span data-ttu-id="7995e-178"><xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> Metoda zawiera scenariusz, aby obejrzeć zmiany w jednym lub kilku plikach lub katalogach.</span><span class="sxs-lookup"><span data-stu-id="7995e-178">The <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="7995e-179">`Watch` Metoda:</span><span class="sxs-lookup"><span data-stu-id="7995e-179">The `Watch` method:</span></span>

* <span data-ttu-id="7995e-180">Akceptuje ciąg ścieżki pliku, który może używać [wzorców globalizowania](#glob-patterns) do określenia wielu plików.</span><span class="sxs-lookup"><span data-stu-id="7995e-180">Accepts a file path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span>
* <span data-ttu-id="7995e-181">Zwraca wartość <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="7995e-181">Returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span>

<span data-ttu-id="7995e-182">Otrzymany token zmiany ujawnia:</span><span class="sxs-lookup"><span data-stu-id="7995e-182">The resulting change token exposes:</span></span>

* <span data-ttu-id="7995e-183"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>&ndash;Właściwość, którą można sprawdzić w celu ustalenia, czy wprowadzono zmianę.</span><span class="sxs-lookup"><span data-stu-id="7995e-183"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>&ndash;A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="7995e-184"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>&ndash;Wywołuje się, gdy zostaną wykryte zmiany do określonego ciągu ścieżki.</span><span class="sxs-lookup"><span data-stu-id="7995e-184"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>&ndash;Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="7995e-185">Każdy token zmiany wywołuje tylko skojarzone wywołanie zwrotne w odpowiedzi na pojedynczą zmianę.</span><span class="sxs-lookup"><span data-stu-id="7995e-185">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="7995e-186">Aby włączyć monitorowanie stałe, użyj <xref:System.Threading.Tasks.TaskCompletionSource`1> (pokazanego poniżej) lub ponownie `IChangeToken` Utwórz wystąpienia w odpowiedzi na zmiany.</span><span class="sxs-lookup"><span data-stu-id="7995e-186">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="7995e-187">Przykładowa aplikacja *WatchConsole* zapisuje komunikat za każdym razem, gdy plik *txt* w katalogu *TextFiles* zostanie zmodyfikowany:</span><span class="sxs-lookup"><span data-stu-id="7995e-187">The *WatchConsole* sample app writes a message whenever a *.txt* file in the *TextFiles* directory is modified:</span></span>

[!code-csharp[](file-providers/samples/3.x/WatchConsole/Program.cs?name=snippet1)]

<span data-ttu-id="7995e-188">Niektóre systemy plików, takie jak kontenery platformy Docker i udziały sieciowe, mogą niezawodnie wysyłać powiadomienia o zmianach.</span><span class="sxs-lookup"><span data-stu-id="7995e-188">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="7995e-189">Ustaw zmienną `DOTNET_USE_POLLING_FILE_WATCHER` środowiskową na `1` lub `true` , aby sondować system plików pod kątem zmian co cztery sekundy (nie można skonfigurować).</span><span class="sxs-lookup"><span data-stu-id="7995e-189">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

### <a name="glob-patterns"></a><span data-ttu-id="7995e-190">Wzorce globalizowania</span><span class="sxs-lookup"><span data-stu-id="7995e-190">Glob patterns</span></span>

<span data-ttu-id="7995e-191">Ścieżki systemu plików używają wzorców symboli wieloznacznych o nazwie *globalizowania (lub obsługi symboli wieloznacznych)*.</span><span class="sxs-lookup"><span data-stu-id="7995e-191">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="7995e-192">Określ grupy plików z tymi wzorcami.</span><span class="sxs-lookup"><span data-stu-id="7995e-192">Specify groups of files with these patterns.</span></span> <span data-ttu-id="7995e-193">Dwa symbole wieloznaczne to `*` `**`:</span><span class="sxs-lookup"><span data-stu-id="7995e-193">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="7995e-194">Dopasowuje wszystko na bieżącym poziomie folderu, dowolnej nazwie pliku lub dowolnym rozszerzeniu pliku.</span><span class="sxs-lookup"><span data-stu-id="7995e-194">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="7995e-195">Dopasowania są kończone przez `/` znaki `.` i w ścieżce pliku.</span><span class="sxs-lookup"><span data-stu-id="7995e-195">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="7995e-196">Dopasowuje wszystko na wielu poziomach katalogów.</span><span class="sxs-lookup"><span data-stu-id="7995e-196">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="7995e-197">Może służyć do rekursywnego dopasowania wielu plików w hierarchii katalogów.</span><span class="sxs-lookup"><span data-stu-id="7995e-197">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="7995e-198">W poniższej tabeli przedstawiono typowe przykłady wzorców globalizowania.</span><span class="sxs-lookup"><span data-stu-id="7995e-198">The following table provides common examples of glob patterns.</span></span>

|<span data-ttu-id="7995e-199">Wzorce</span><span class="sxs-lookup"><span data-stu-id="7995e-199">Pattern</span></span>  |<span data-ttu-id="7995e-200">Opis</span><span class="sxs-lookup"><span data-stu-id="7995e-200">Description</span></span>  |
|---------|---------|
|`directory/file.txt`|<span data-ttu-id="7995e-201">Dopasowuje określony plik w określonym katalogu.</span><span class="sxs-lookup"><span data-stu-id="7995e-201">Matches a specific file in a specific directory.</span></span>|
|`directory/*.txt`|<span data-ttu-id="7995e-202">Dopasowuje wszystkie pliki z rozszerzeniem *. txt* w określonym katalogu.</span><span class="sxs-lookup"><span data-stu-id="7995e-202">Matches all files with *.txt* extension in a specific directory.</span></span>|
|`directory/*/appsettings.json`|<span data-ttu-id="7995e-203">Dopasowuje wszystkie pliki *appSettings. JSON* w katalogach dokładnie o jeden poziom poniżej folderu *katalogu* .</span><span class="sxs-lookup"><span data-stu-id="7995e-203">Matches all *appsettings.json* files in directories exactly one level below the *directory* folder.</span></span>|
|`directory/**/*.txt`|<span data-ttu-id="7995e-204">Dopasowuje wszystkie pliki z rozszerzeniem *. txt* , które znajdują się w dowolnym miejscu w folderze *katalogu* .</span><span class="sxs-lookup"><span data-stu-id="7995e-204">Matches all files with a *.txt* extension found anywhere under the *directory* folder.</span></span>|

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7995e-205">ASP.NET Core abstrakcję dostępu systemu plików przy użyciu dostawców plików.</span><span class="sxs-lookup"><span data-stu-id="7995e-205">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="7995e-206">Dostawcy plików są używani w całym ASP.NET Core Framework:</span><span class="sxs-lookup"><span data-stu-id="7995e-206">File Providers are used throughout the ASP.NET Core framework:</span></span>

* <span data-ttu-id="7995e-207"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment>udostępnia [katalog główny zawartości](xref:fundamentals/index#content-root) aplikacji i [katalogu głównego sieci Web](xref:fundamentals/index#web-root) jako `IFileProvider` typy.</span><span class="sxs-lookup"><span data-stu-id="7995e-207"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="7995e-208">[Oprogramowanie pośredniczące plików statycznych](xref:fundamentals/static-files) używa dostawców plików do lokalizowania plików statycznych.</span><span class="sxs-lookup"><span data-stu-id="7995e-208">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="7995e-209">[Razor](xref:mvc/views/razor) używa dostawców plików do lokalizowania stron i widoków.</span><span class="sxs-lookup"><span data-stu-id="7995e-209">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="7995e-210">Narzędzia .NET Core używają dostawców plików i wzorców globalizowania, aby określić, które pliki powinny zostać opublikowane.</span><span class="sxs-lookup"><span data-stu-id="7995e-210">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="7995e-211">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7995e-211">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="7995e-212">Interfejsy dostawcy plików</span><span class="sxs-lookup"><span data-stu-id="7995e-212">File Provider interfaces</span></span>

<span data-ttu-id="7995e-213">Podstawowy interfejs to <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="7995e-213">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="7995e-214">`IFileProvider`udostępnia metody:</span><span class="sxs-lookup"><span data-stu-id="7995e-214">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="7995e-215">Uzyskaj informacje o pliku<xref:Microsoft.Extensions.FileProviders.IFileInfo>().</span><span class="sxs-lookup"><span data-stu-id="7995e-215">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="7995e-216">Uzyskaj informacje o katalogu<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>().</span><span class="sxs-lookup"><span data-stu-id="7995e-216">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="7995e-217">Skonfiguruj powiadomienia o zmianach (przy <xref:Microsoft.Extensions.Primitives.IChangeToken>użyciu).</span><span class="sxs-lookup"><span data-stu-id="7995e-217">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="7995e-218">`IFileInfo`zapewnia metody i właściwości do pracy z plikami:</span><span class="sxs-lookup"><span data-stu-id="7995e-218">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="7995e-219"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length>(w bajtach)</span><span class="sxs-lookup"><span data-stu-id="7995e-219"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="7995e-220"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified>dniu</span><span class="sxs-lookup"><span data-stu-id="7995e-220"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="7995e-221">Można odczytać z pliku za pomocą metody [IFileInfo. CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) .</span><span class="sxs-lookup"><span data-stu-id="7995e-221">You can read from the file using the [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) method.</span></span>

<span data-ttu-id="7995e-222">Przykładowa aplikacja pokazuje, jak skonfigurować dostawcę plików w programie `Startup.ConfigureServices` do użycia w całej aplikacji za pomocą [iniekcji zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="7995e-222">The sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="7995e-223">Implementacje dostawcy plików</span><span class="sxs-lookup"><span data-stu-id="7995e-223">File Provider implementations</span></span>

<span data-ttu-id="7995e-224">Dostępne są trzy `IFileProvider` implementacje programu.</span><span class="sxs-lookup"><span data-stu-id="7995e-224">Three implementations of `IFileProvider` are available.</span></span>

| <span data-ttu-id="7995e-225">Wdrażanie</span><span class="sxs-lookup"><span data-stu-id="7995e-225">Implementation</span></span> | <span data-ttu-id="7995e-226">Opis</span><span class="sxs-lookup"><span data-stu-id="7995e-226">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="7995e-227">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="7995e-227">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="7995e-228">Dostawca fizyczny jest używany do uzyskiwania dostępu do plików fizycznych systemu.</span><span class="sxs-lookup"><span data-stu-id="7995e-228">The physical provider is used to access the system's physical files.</span></span> |
| [<span data-ttu-id="7995e-229">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="7995e-229">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="7995e-230">Dostawca osadzony manifestu służy do uzyskiwania dostępu do plików osadzonych w zestawach.</span><span class="sxs-lookup"><span data-stu-id="7995e-230">The manifest embedded provider is used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="7995e-231">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="7995e-231">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="7995e-232">Dostawca złożony służy do zapewniania połączonego dostępu do plików i katalogów z jednego lub kilku innych dostawców.</span><span class="sxs-lookup"><span data-stu-id="7995e-232">The composite provider is used to provide combined access to files and directories from one or more other providers.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="7995e-233">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="7995e-233">PhysicalFileProvider</span></span>

<span data-ttu-id="7995e-234"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> Zapewnia dostęp do fizycznego systemu plików.</span><span class="sxs-lookup"><span data-stu-id="7995e-234">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="7995e-235">`PhysicalFileProvider`używa <xref:System.IO.File?displayProperty=fullName> typu (dla dostawcy fizycznego) i zakresy wszystkie ścieżki do katalogu i jego elementów podrzędnych.</span><span class="sxs-lookup"><span data-stu-id="7995e-235">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="7995e-236">Takie Określanie zakresu uniemożliwia dostęp do systemu plików poza określonym katalogiem i jego elementami podrzędnymi.</span><span class="sxs-lookup"><span data-stu-id="7995e-236">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="7995e-237">Najbardziej typowym scenariuszem tworzenia i używania elementu `PhysicalFileProvider` jest zażądanie `IFileProvider` w konstruktorze przy użyciu [iniekcji zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="7995e-237">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="7995e-238">W przypadku bezpośredniego tworzenia wystąpienia tego dostawcy ścieżka katalogu jest wymagana i służy jako ścieżka podstawowa dla wszystkich żądań wysyłanych przy użyciu dostawcy.</span><span class="sxs-lookup"><span data-stu-id="7995e-238">When instantiating this provider directly, a directory path is required and serves as the base path for all requests made using the provider.</span></span>

<span data-ttu-id="7995e-239">Poniższy kod przedstawia sposób tworzenia `PhysicalFileProvider` i używania go do uzyskiwania informacji o zawartości i pliku katalogu:</span><span class="sxs-lookup"><span data-stu-id="7995e-239">The following code shows how to create a `PhysicalFileProvider` and use it to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

<span data-ttu-id="7995e-240">Typy w poprzednim przykładzie:</span><span class="sxs-lookup"><span data-stu-id="7995e-240">Types in the preceding example:</span></span>

* <span data-ttu-id="7995e-241">`provider`jest `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="7995e-241">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="7995e-242">`contents`jest `IDirectoryContents`.</span><span class="sxs-lookup"><span data-stu-id="7995e-242">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="7995e-243">`fileInfo`jest `IFileInfo`.</span><span class="sxs-lookup"><span data-stu-id="7995e-243">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="7995e-244">Dostawca plików może służyć do iteracji przez katalog określony przez `applicationRoot` lub wywołanie `GetFileInfo` w celu uzyskania informacji o pliku.</span><span class="sxs-lookup"><span data-stu-id="7995e-244">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="7995e-245">Dostawca plików nie ma dostępu poza `applicationRoot` katalogiem.</span><span class="sxs-lookup"><span data-stu-id="7995e-245">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="7995e-246">Przykładowa aplikacja tworzy dostawcę w `Startup.ConfigureServices` klasie aplikacji za pomocą [IHostingEnvironment. ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span><span class="sxs-lookup"><span data-stu-id="7995e-246">The sample app creates the provider in the app's `Startup.ConfigureServices` class using [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="7995e-247">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="7995e-247">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="7995e-248"><xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> Służy do uzyskiwania dostępu do plików osadzonych w zestawach.</span><span class="sxs-lookup"><span data-stu-id="7995e-248">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="7995e-249">`ManifestEmbeddedFileProvider` Używa manifestu skompilowanego w zestawie, aby odtworzyć oryginalne ścieżki osadzonych plików.</span><span class="sxs-lookup"><span data-stu-id="7995e-249">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="7995e-250">Aby wygenerować manifest osadzonych plików, należy ustawić `<GenerateEmbeddedFilesManifest>` właściwość na. `true`</span><span class="sxs-lookup"><span data-stu-id="7995e-250">To generate a manifest of the embedded files, set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="7995e-251">Określ pliki do osadzenia przy użyciu [ &lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span><span class="sxs-lookup"><span data-stu-id="7995e-251">Specify the files to embed with [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

<span data-ttu-id="7995e-252">Użyj [wzorców globalizowania](#glob-patterns) , aby określić jeden lub więcej plików do osadzenia w zestawie.</span><span class="sxs-lookup"><span data-stu-id="7995e-252">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="7995e-253">Przykładowa aplikacja tworzy `ManifestEmbeddedFileProvider` i przekazuje aktualnie wykonywany zestaw do jego konstruktora.</span><span class="sxs-lookup"><span data-stu-id="7995e-253">The sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="7995e-254">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="7995e-254">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="7995e-255">Dodatkowe przeciążenia umożliwiają:</span><span class="sxs-lookup"><span data-stu-id="7995e-255">Additional overloads allow you to:</span></span>

* <span data-ttu-id="7995e-256">Określ względną ścieżkę pliku.</span><span class="sxs-lookup"><span data-stu-id="7995e-256">Specify a relative file path.</span></span>
* <span data-ttu-id="7995e-257">Zakres plików do daty ostatniej modyfikacji.</span><span class="sxs-lookup"><span data-stu-id="7995e-257">Scope files to a last modified date.</span></span>
* <span data-ttu-id="7995e-258">Nazwij osadzony zasób zawierający manifest pliku osadzonego.</span><span class="sxs-lookup"><span data-stu-id="7995e-258">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="7995e-259">Występują</span><span class="sxs-lookup"><span data-stu-id="7995e-259">Overload</span></span> | <span data-ttu-id="7995e-260">Opis</span><span class="sxs-lookup"><span data-stu-id="7995e-260">Description</span></span> |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | <span data-ttu-id="7995e-261">Akceptuje opcjonalny `root` parametr ścieżki względnej.</span><span class="sxs-lookup"><span data-stu-id="7995e-261">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="7995e-262">`root` Określ do zakresu wywołania <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> do tych zasobów w ramach podanej ścieżki.</span><span class="sxs-lookup"><span data-stu-id="7995e-262">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | <span data-ttu-id="7995e-263">Akceptuje opcjonalny `root` parametr ścieżki względnej i parametr `lastModified` Date (<xref:System.DateTimeOffset>).</span><span class="sxs-lookup"><span data-stu-id="7995e-263">Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="7995e-264">`lastModified` Data zakresy daty ostatniej modyfikacji dla <xref:Microsoft.Extensions.FileProviders.IFileInfo> wystąpień zwracanych przez <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="7995e-264">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | <span data-ttu-id="7995e-265">Akceptuje opcjonalną `root` ścieżkę względną `lastModified` , datę i `manifestName` parametry.</span><span class="sxs-lookup"><span data-stu-id="7995e-265">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="7995e-266">`manifestName` Reprezentuje nazwę zasobu osadzonego zawierającego manifest.</span><span class="sxs-lookup"><span data-stu-id="7995e-266">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="7995e-267">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="7995e-267">CompositeFileProvider</span></span>

<span data-ttu-id="7995e-268"><xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> Łączy `IFileProvider` wystąpienia, uwidaczniając pojedynczy interfejs do pracy z plikami z wielu dostawców.</span><span class="sxs-lookup"><span data-stu-id="7995e-268">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="7995e-269">Podczas tworzenia `CompositeFileProvider`, należy przekazać co najmniej jedno `IFileProvider` wystąpienie do jego konstruktora.</span><span class="sxs-lookup"><span data-stu-id="7995e-269">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="7995e-270">W przykładowej aplikacji a `PhysicalFileProvider` i `ManifestEmbeddedFileProvider` udostępniaj pliki `CompositeFileProvider` zarejestrowane w kontenerze usługi aplikacji:</span><span class="sxs-lookup"><span data-stu-id="7995e-270">In the sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container:</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="7995e-271">Obejrzyj zmiany</span><span class="sxs-lookup"><span data-stu-id="7995e-271">Watch for changes</span></span>

<span data-ttu-id="7995e-272">Metoda [IFileProvider. Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) zawiera scenariusz, aby obejrzeć zmiany w jednym lub kilku plikach lub katalogach.</span><span class="sxs-lookup"><span data-stu-id="7995e-272">The [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="7995e-273">`Watch`akceptuje ciąg ścieżki, który może używać [wzorców globalizowania](#glob-patterns) do określenia wielu plików.</span><span class="sxs-lookup"><span data-stu-id="7995e-273">`Watch` accepts a path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span> <span data-ttu-id="7995e-274">`Watch`Zwraca wartość <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="7995e-274">`Watch` returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span> <span data-ttu-id="7995e-275">Token zmiany ujawnia:</span><span class="sxs-lookup"><span data-stu-id="7995e-275">The change token exposes:</span></span>

* <span data-ttu-id="7995e-276"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>&ndash; Właściwość, którą można sprawdzić w celu ustalenia, czy wprowadzono zmianę.</span><span class="sxs-lookup"><span data-stu-id="7995e-276"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> &ndash; A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="7995e-277"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>&ndash; Wywołuje się, gdy zostaną wykryte zmiany do określonego ciągu ścieżki.</span><span class="sxs-lookup"><span data-stu-id="7995e-277"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*> &ndash; Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="7995e-278">Każdy token zmiany wywołuje tylko skojarzone wywołanie zwrotne w odpowiedzi na pojedynczą zmianę.</span><span class="sxs-lookup"><span data-stu-id="7995e-278">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="7995e-279">Aby włączyć monitorowanie stałe, użyj <xref:System.Threading.Tasks.TaskCompletionSource`1> (pokazanego poniżej) lub ponownie `IChangeToken` Utwórz wystąpienia w odpowiedzi na zmiany.</span><span class="sxs-lookup"><span data-stu-id="7995e-279">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="7995e-280">W aplikacji przykładowej Aplikacja konsolowa *WatchConsole* jest skonfigurowana do wyświetlania komunikatu za każdym razem, gdy plik tekstowy zostanie zmodyfikowany:</span><span class="sxs-lookup"><span data-stu-id="7995e-280">In the sample app, the *WatchConsole* console app is configured to display a message whenever a text file is modified:</span></span>

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

<span data-ttu-id="7995e-281">Niektóre systemy plików, takie jak kontenery platformy Docker i udziały sieciowe, mogą niezawodnie wysyłać powiadomienia o zmianach.</span><span class="sxs-lookup"><span data-stu-id="7995e-281">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="7995e-282">Ustaw zmienną `DOTNET_USE_POLLING_FILE_WATCHER` środowiskową na `1` lub `true` , aby sondować system plików pod kątem zmian co cztery sekundy (nie można skonfigurować).</span><span class="sxs-lookup"><span data-stu-id="7995e-282">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

## <a name="glob-patterns"></a><span data-ttu-id="7995e-283">Wzorce globalizowania</span><span class="sxs-lookup"><span data-stu-id="7995e-283">Glob patterns</span></span>

<span data-ttu-id="7995e-284">Ścieżki systemu plików używają wzorców symboli wieloznacznych o nazwie *globalizowania (lub obsługi symboli wieloznacznych)*.</span><span class="sxs-lookup"><span data-stu-id="7995e-284">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="7995e-285">Określ grupy plików z tymi wzorcami.</span><span class="sxs-lookup"><span data-stu-id="7995e-285">Specify groups of files with these patterns.</span></span> <span data-ttu-id="7995e-286">Dwa symbole wieloznaczne to `*` `**`:</span><span class="sxs-lookup"><span data-stu-id="7995e-286">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="7995e-287">Dopasowuje wszystko na bieżącym poziomie folderu, dowolnej nazwie pliku lub dowolnym rozszerzeniu pliku.</span><span class="sxs-lookup"><span data-stu-id="7995e-287">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="7995e-288">Dopasowania są kończone przez `/` znaki `.` i w ścieżce pliku.</span><span class="sxs-lookup"><span data-stu-id="7995e-288">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="7995e-289">Dopasowuje wszystko na wielu poziomach katalogów.</span><span class="sxs-lookup"><span data-stu-id="7995e-289">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="7995e-290">Może służyć do rekursywnego dopasowania wielu plików w hierarchii katalogów.</span><span class="sxs-lookup"><span data-stu-id="7995e-290">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="7995e-291">**Przykłady wzorców globalizowania**</span><span class="sxs-lookup"><span data-stu-id="7995e-291">**Glob pattern examples**</span></span>

**`directory/file.txt`**  
<span data-ttu-id="7995e-292">Dopasowuje określony plik w określonym katalogu.</span><span class="sxs-lookup"><span data-stu-id="7995e-292">Matches a specific file in a specific directory.</span></span>

**`directory/*.txt`**  
<span data-ttu-id="7995e-293">Dopasowuje wszystkie pliki z rozszerzeniem *. txt* w określonym katalogu.</span><span class="sxs-lookup"><span data-stu-id="7995e-293">Matches all files with *.txt* extension in a specific directory.</span></span>

**`directory/*/appsettings.json`**  
<span data-ttu-id="7995e-294">Dopasowuje `appsettings.json` wszystkie pliki w katalogach dokładnie o jeden poziom poniżej folderu *katalogu* .</span><span class="sxs-lookup"><span data-stu-id="7995e-294">Matches all `appsettings.json` files in directories exactly one level below the *directory* folder.</span></span>

**`directory/**/*.txt`**  
<span data-ttu-id="7995e-295">Dopasowuje wszystkie pliki z rozszerzeniem *. txt* , które znajdują się w dowolnym miejscu w folderze *katalogu* .</span><span class="sxs-lookup"><span data-stu-id="7995e-295">Matches all files with *.txt* extension found anywhere under the *directory* folder.</span></span>

::: moniker-end
