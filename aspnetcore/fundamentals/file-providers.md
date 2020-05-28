---
<span data-ttu-id="39505-101">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="39505-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39505-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39505-102">'Blazor'</span></span>
- <span data-ttu-id="39505-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39505-103">'Identity'</span></span>
- <span data-ttu-id="39505-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39505-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="39505-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39505-105">'Razor'</span></span>
- <span data-ttu-id="39505-106">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="39505-106">'SignalR' uid:</span></span> 

---
# <a name="file-providers-in-aspnet-core"></a><span data-ttu-id="39505-107">Dostawcy plików w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="39505-107">File Providers in ASP.NET Core</span></span>

<span data-ttu-id="39505-108">Przez [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="39505-108">By [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="39505-109">ASP.NET Core abstrakcję dostępu systemu plików przy użyciu dostawców plików.</span><span class="sxs-lookup"><span data-stu-id="39505-109">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="39505-110">Dostawcy plików są używani w całym ASP.NET Core Framework.</span><span class="sxs-lookup"><span data-stu-id="39505-110">File Providers are used throughout the ASP.NET Core framework.</span></span> <span data-ttu-id="39505-111">Przykład:</span><span class="sxs-lookup"><span data-stu-id="39505-111">For example:</span></span>

* <span data-ttu-id="39505-112"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>udostępnia [katalog główny zawartości](xref:fundamentals/index#content-root) aplikacji i [katalogu głównego sieci Web](xref:fundamentals/index#web-root) jako `IFileProvider` typy.</span><span class="sxs-lookup"><span data-stu-id="39505-112"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="39505-113">[Oprogramowanie pośredniczące plików statycznych](xref:fundamentals/static-files) używa dostawców plików do lokalizowania plików statycznych.</span><span class="sxs-lookup"><span data-stu-id="39505-113">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="39505-114">[Razor](xref:mvc/views/razor)używa dostawców plików do lokalizowania stron i widoków.</span><span class="sxs-lookup"><span data-stu-id="39505-114">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="39505-115">Narzędzia .NET Core używają dostawców plików i wzorców globalizowania, aby określić, które pliki powinny zostać opublikowane.</span><span class="sxs-lookup"><span data-stu-id="39505-115">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="39505-116">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="39505-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="39505-117">Interfejsy dostawcy plików</span><span class="sxs-lookup"><span data-stu-id="39505-117">File Provider interfaces</span></span>

<span data-ttu-id="39505-118">Podstawowy interfejs to <xref:Microsoft.Extensions.FileProviders.IFileProvider> .</span><span class="sxs-lookup"><span data-stu-id="39505-118">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="39505-119">`IFileProvider`udostępnia metody:</span><span class="sxs-lookup"><span data-stu-id="39505-119">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="39505-120">Uzyskaj informacje o pliku ( <xref:Microsoft.Extensions.FileProviders.IFileInfo> ).</span><span class="sxs-lookup"><span data-stu-id="39505-120">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="39505-121">Uzyskaj informacje o katalogu ( <xref:Microsoft.Extensions.FileProviders.IDirectoryContents> ).</span><span class="sxs-lookup"><span data-stu-id="39505-121">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="39505-122">Skonfiguruj powiadomienia o zmianach (przy użyciu <xref:Microsoft.Extensions.Primitives.IChangeToken> ).</span><span class="sxs-lookup"><span data-stu-id="39505-122">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="39505-123">`IFileInfo`zapewnia metody i właściwości do pracy z plikami:</span><span class="sxs-lookup"><span data-stu-id="39505-123">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="39505-124"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length>(w bajtach)</span><span class="sxs-lookup"><span data-stu-id="39505-124"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="39505-125"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified>dniu</span><span class="sxs-lookup"><span data-stu-id="39505-125"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="39505-126">Można odczytać z pliku za pomocą <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> metody.</span><span class="sxs-lookup"><span data-stu-id="39505-126">You can read from the file using the <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> method.</span></span>

<span data-ttu-id="39505-127">Przykładowa aplikacja *FileProviderSample* pokazuje, jak skonfigurować dostawcę plików w programie `Startup.ConfigureServices` do użycia w całej aplikacji za pomocą [iniekcji zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="39505-127">The *FileProviderSample* sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="39505-128">Implementacje dostawcy plików</span><span class="sxs-lookup"><span data-stu-id="39505-128">File Provider implementations</span></span>

<span data-ttu-id="39505-129">W poniższej tabeli wymieniono implementacje programu `IFileProvider` .</span><span class="sxs-lookup"><span data-stu-id="39505-129">The following table lists implementations of `IFileProvider`.</span></span>

| <span data-ttu-id="39505-130">Implementacja</span><span class="sxs-lookup"><span data-stu-id="39505-130">Implementation</span></span> | <span data-ttu-id="39505-131">Opis</span><span class="sxs-lookup"><span data-stu-id="39505-131">Description</span></span> |
| ---
<span data-ttu-id="39505-132">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="39505-132">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39505-133">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39505-133">'Blazor'</span></span>
- <span data-ttu-id="39505-134">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39505-134">'Identity'</span></span>
- <span data-ttu-id="39505-135">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39505-135">'Let's Encrypt'</span></span>
- <span data-ttu-id="39505-136">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39505-136">'Razor'</span></span>
- <span data-ttu-id="39505-137">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="39505-137">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39505-138">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="39505-138">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39505-139">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39505-139">'Blazor'</span></span>
- <span data-ttu-id="39505-140">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39505-140">'Identity'</span></span>
- <span data-ttu-id="39505-141">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39505-141">'Let's Encrypt'</span></span>
- <span data-ttu-id="39505-142">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39505-142">'Razor'</span></span>
- <span data-ttu-id="39505-143">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="39505-143">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39505-144">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="39505-144">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39505-145">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39505-145">'Blazor'</span></span>
- <span data-ttu-id="39505-146">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39505-146">'Identity'</span></span>
- <span data-ttu-id="39505-147">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39505-147">'Let's Encrypt'</span></span>
- <span data-ttu-id="39505-148">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39505-148">'Razor'</span></span>
- <span data-ttu-id="39505-149">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="39505-149">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39505-150">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="39505-150">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39505-151">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39505-151">'Blazor'</span></span>
- <span data-ttu-id="39505-152">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39505-152">'Identity'</span></span>
- <span data-ttu-id="39505-153">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39505-153">'Let's Encrypt'</span></span>
- <span data-ttu-id="39505-154">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39505-154">'Razor'</span></span>
- <span data-ttu-id="39505-155">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="39505-155">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39505-156">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="39505-156">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39505-157">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39505-157">'Blazor'</span></span>
- <span data-ttu-id="39505-158">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39505-158">'Identity'</span></span>
- <span data-ttu-id="39505-159">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39505-159">'Let's Encrypt'</span></span>
- <span data-ttu-id="39505-160">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39505-160">'Razor'</span></span>
- <span data-ttu-id="39505-161">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="39505-161">'SignalR' uid:</span></span> 

<span data-ttu-id="39505-162">------- | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="39505-162">------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39505-163">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39505-163">'Blazor'</span></span>
- <span data-ttu-id="39505-164">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39505-164">'Identity'</span></span>
- <span data-ttu-id="39505-165">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39505-165">'Let's Encrypt'</span></span>
- <span data-ttu-id="39505-166">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39505-166">'Razor'</span></span>
- <span data-ttu-id="39505-167">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="39505-167">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39505-168">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="39505-168">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39505-169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39505-169">'Blazor'</span></span>
- <span data-ttu-id="39505-170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39505-170">'Identity'</span></span>
- <span data-ttu-id="39505-171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39505-171">'Let's Encrypt'</span></span>
- <span data-ttu-id="39505-172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39505-172">'Razor'</span></span>
- <span data-ttu-id="39505-173">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="39505-173">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39505-174">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="39505-174">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39505-175">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39505-175">'Blazor'</span></span>
- <span data-ttu-id="39505-176">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39505-176">'Identity'</span></span>
- <span data-ttu-id="39505-177">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39505-177">'Let's Encrypt'</span></span>
- <span data-ttu-id="39505-178">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39505-178">'Razor'</span></span>
- <span data-ttu-id="39505-179">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="39505-179">'SignalR' uid:</span></span> 

<span data-ttu-id="39505-180">------ | | [CompositeFileProvider](#compositefileprovider) | Służy do zapewniania połączonego dostępu do plików i katalogów z co najmniej jednego dostawcy.</span><span class="sxs-lookup"><span data-stu-id="39505-180">------ | | [CompositeFileProvider](#compositefileprovider) | Used to provide combined access to files and directories from one or more other providers.</span></span> <span data-ttu-id="39505-181">| | [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | Służy do uzyskiwania dostępu do plików osadzonych w zestawach.</span><span class="sxs-lookup"><span data-stu-id="39505-181">| | [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | Used to access files embedded in assemblies.</span></span> <span data-ttu-id="39505-182">| | [PhysicalFileProvider](#physicalfileprovider) | Służy do uzyskiwania dostępu do plików fizycznych systemu.</span><span class="sxs-lookup"><span data-stu-id="39505-182">| | [PhysicalFileProvider](#physicalfileprovider) | Used to access the system's physical files.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="39505-183">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="39505-183">PhysicalFileProvider</span></span>

<span data-ttu-id="39505-184"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>Zapewnia dostęp do fizycznego systemu plików.</span><span class="sxs-lookup"><span data-stu-id="39505-184">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="39505-185">`PhysicalFileProvider`używa <xref:System.IO.File?displayProperty=fullName> typu (dla dostawcy fizycznego) i zakresy wszystkie ścieżki do katalogu i jego elementów podrzędnych.</span><span class="sxs-lookup"><span data-stu-id="39505-185">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="39505-186">Takie Określanie zakresu uniemożliwia dostęp do systemu plików poza określonym katalogiem i jego elementami podrzędnymi.</span><span class="sxs-lookup"><span data-stu-id="39505-186">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="39505-187">Najbardziej typowym scenariuszem tworzenia i używania elementu `PhysicalFileProvider` jest zażądanie `IFileProvider` w konstruktorze przy użyciu [iniekcji zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="39505-187">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="39505-188">W przypadku bezpośredniego tworzenia wystąpienia tego dostawcy wymagana jest bezwzględna ścieżka do katalogu i służy jako ścieżka podstawowa dla wszystkich żądań wysyłanych przy użyciu dostawcy.</span><span class="sxs-lookup"><span data-stu-id="39505-188">When instantiating this provider directly, an absolute directory path is required and serves as the base path for all requests made using the provider.</span></span> <span data-ttu-id="39505-189">Wzorce globalizowania nie są obsługiwane w ścieżce katalogu.</span><span class="sxs-lookup"><span data-stu-id="39505-189">Glob patterns aren't supported in the directory path.</span></span>

<span data-ttu-id="39505-190">Poniższy kod przedstawia sposób korzystania `PhysicalFileProvider` z programu w celu uzyskania zawartości katalogu i informacji o pliku:</span><span class="sxs-lookup"><span data-stu-id="39505-190">The following code shows how to use `PhysicalFileProvider` to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var filePath = Path.Combine("wwwroot", "js", "site.js");
var fileInfo = provider.GetFileInfo(filePath);
```

<span data-ttu-id="39505-191">Typy w poprzednim przykładzie:</span><span class="sxs-lookup"><span data-stu-id="39505-191">Types in the preceding example:</span></span>

* <span data-ttu-id="39505-192">`provider`jest `IFileProvider` .</span><span class="sxs-lookup"><span data-stu-id="39505-192">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="39505-193">`contents`jest `IDirectoryContents` .</span><span class="sxs-lookup"><span data-stu-id="39505-193">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="39505-194">`fileInfo`jest `IFileInfo` .</span><span class="sxs-lookup"><span data-stu-id="39505-194">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="39505-195">Dostawca plików może służyć do iteracji przez katalog określony przez `applicationRoot` lub wywołanie `GetFileInfo` w celu uzyskania informacji o pliku.</span><span class="sxs-lookup"><span data-stu-id="39505-195">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="39505-196">Nie można przekazywać wzorców globalizowania do `GetFileInfo` metody.</span><span class="sxs-lookup"><span data-stu-id="39505-196">Glob patterns can't be passed to the `GetFileInfo` method.</span></span> <span data-ttu-id="39505-197">Dostawca plików nie ma dostępu poza `applicationRoot` katalogiem.</span><span class="sxs-lookup"><span data-stu-id="39505-197">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="39505-198">Przykładowa aplikacja *FileProviderSample* tworzy dostawcę w `Startup.ConfigureServices` metodzie przy użyciu <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="39505-198">The *FileProviderSample* sample app creates the provider in the `Startup.ConfigureServices` method using <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType>:</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="39505-199">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="39505-199">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="39505-200">Służy <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> do uzyskiwania dostępu do plików osadzonych w zestawach.</span><span class="sxs-lookup"><span data-stu-id="39505-200">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="39505-201">`ManifestEmbeddedFileProvider`Używa manifestu skompilowanego w zestawie, aby odtworzyć oryginalne ścieżki osadzonych plików.</span><span class="sxs-lookup"><span data-stu-id="39505-201">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="39505-202">Aby wygenerować manifest osadzonych plików:</span><span class="sxs-lookup"><span data-stu-id="39505-202">To generate a manifest of the embedded files:</span></span>

1. <span data-ttu-id="39505-203">Dodaj pakiet NuGet [Microsoft. Extensions. FileProviders. Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) do projektu.</span><span class="sxs-lookup"><span data-stu-id="39505-203">Add the [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) NuGet package to your project.</span></span>
1. <span data-ttu-id="39505-204">Ustaw `<GenerateEmbeddedFilesManifest>` Właściwość na wartość `true` .</span><span class="sxs-lookup"><span data-stu-id="39505-204">Set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="39505-205">Określ pliki do osadzenia przy użyciu [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects) :</span><span class="sxs-lookup"><span data-stu-id="39505-205">Specify the files to embed with [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

    [!code-xml[](file-providers/samples/3.x/FileProviderSample/FileProviderSample.csproj?highlight=5,13)]

<span data-ttu-id="39505-206">Użyj [wzorców globalizowania](#glob-patterns) , aby określić jeden lub więcej plików do osadzenia w zestawie.</span><span class="sxs-lookup"><span data-stu-id="39505-206">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="39505-207">Aplikacja Przykładowa *FileProviderSample* tworzy `ManifestEmbeddedFileProvider` i przekazuje aktualnie wykonywany zestaw do jego konstruktora.</span><span class="sxs-lookup"><span data-stu-id="39505-207">The *FileProviderSample* sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="39505-208">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="39505-208">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="39505-209">Dodatkowe przeciążenia umożliwiają:</span><span class="sxs-lookup"><span data-stu-id="39505-209">Additional overloads allow you to:</span></span>

* <span data-ttu-id="39505-210">Określ względną ścieżkę pliku.</span><span class="sxs-lookup"><span data-stu-id="39505-210">Specify a relative file path.</span></span>
* <span data-ttu-id="39505-211">Zakres plików do daty ostatniej modyfikacji.</span><span class="sxs-lookup"><span data-stu-id="39505-211">Scope files to a last modified date.</span></span>
* <span data-ttu-id="39505-212">Nazwij osadzony zasób zawierający manifest pliku osadzonego.</span><span class="sxs-lookup"><span data-stu-id="39505-212">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="39505-213">Występują</span><span class="sxs-lookup"><span data-stu-id="39505-213">Overload</span></span> | <span data-ttu-id="39505-214">Opis</span><span class="sxs-lookup"><span data-stu-id="39505-214">Description</span></span> |
| ---
<span data-ttu-id="39505-215">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="39505-215">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39505-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39505-216">'Blazor'</span></span>
- <span data-ttu-id="39505-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39505-217">'Identity'</span></span>
- <span data-ttu-id="39505-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39505-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="39505-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39505-219">'Razor'</span></span>
- <span data-ttu-id="39505-220">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="39505-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39505-221">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="39505-221">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39505-222">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39505-222">'Blazor'</span></span>
- <span data-ttu-id="39505-223">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39505-223">'Identity'</span></span>
- <span data-ttu-id="39505-224">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39505-224">'Let's Encrypt'</span></span>
- <span data-ttu-id="39505-225">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39505-225">'Razor'</span></span>
- <span data-ttu-id="39505-226">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="39505-226">'SignalR' uid:</span></span> 

<span data-ttu-id="39505-227">---- | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="39505-227">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39505-228">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39505-228">'Blazor'</span></span>
- <span data-ttu-id="39505-229">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39505-229">'Identity'</span></span>
- <span data-ttu-id="39505-230">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39505-230">'Let's Encrypt'</span></span>
- <span data-ttu-id="39505-231">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39505-231">'Razor'</span></span>
- <span data-ttu-id="39505-232">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="39505-232">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39505-233">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="39505-233">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39505-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39505-234">'Blazor'</span></span>
- <span data-ttu-id="39505-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39505-235">'Identity'</span></span>
- <span data-ttu-id="39505-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39505-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="39505-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39505-237">'Razor'</span></span>
- <span data-ttu-id="39505-238">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="39505-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39505-239">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="39505-239">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39505-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39505-240">'Blazor'</span></span>
- <span data-ttu-id="39505-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39505-241">'Identity'</span></span>
- <span data-ttu-id="39505-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39505-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="39505-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39505-243">'Razor'</span></span>
- <span data-ttu-id="39505-244">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="39505-244">'SignalR' uid:</span></span> 

<span data-ttu-id="39505-245">------ | | `ManifestEmbeddedFileProvider(Assembly, String)` | Akceptuje opcjonalny `root` parametr ścieżki względnej.</span><span class="sxs-lookup"><span data-stu-id="39505-245">------ | | `ManifestEmbeddedFileProvider(Assembly, String)` | Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="39505-246">Określ do `root` zakresu wywołania do <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> tych zasobów w ramach podanej ścieżki.</span><span class="sxs-lookup"><span data-stu-id="39505-246">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> <span data-ttu-id="39505-247">| | `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Akceptuje opcjonalny `root` parametr ścieżki względnej i `lastModified` parametr Date ( <xref:System.DateTimeOffset> ).</span><span class="sxs-lookup"><span data-stu-id="39505-247">| | `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="39505-248">`lastModified`Data zakresy daty ostatniej modyfikacji dla <xref:Microsoft.Extensions.FileProviders.IFileInfo> wystąpień zwracanych przez <xref:Microsoft.Extensions.FileProviders.IFileProvider> .</span><span class="sxs-lookup"><span data-stu-id="39505-248">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="39505-249">| | `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Akceptuje opcjonalną `root` ścieżkę względną, `lastModified` datę i `manifestName` parametry.</span><span class="sxs-lookup"><span data-stu-id="39505-249">| | `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="39505-250">`manifestName`Reprezentuje nazwę zasobu osadzonego zawierającego manifest.</span><span class="sxs-lookup"><span data-stu-id="39505-250">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="39505-251">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="39505-251">CompositeFileProvider</span></span>

<span data-ttu-id="39505-252"><xref:Microsoft.Extensions.FileProviders.CompositeFileProvider>Łączy `IFileProvider` wystąpienia, uwidaczniając pojedynczy interfejs do pracy z plikami z wielu dostawców.</span><span class="sxs-lookup"><span data-stu-id="39505-252">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="39505-253">Podczas tworzenia `CompositeFileProvider` , należy przekazać co najmniej jedno `IFileProvider` wystąpienie do jego konstruktora.</span><span class="sxs-lookup"><span data-stu-id="39505-253">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="39505-254">W przykładowej aplikacji *FileProviderSample* `PhysicalFileProvider` i `ManifestEmbeddedFileProvider` Podaj pliki do `CompositeFileProvider` zarejestrowanego w kontenerze usługi aplikacji.</span><span class="sxs-lookup"><span data-stu-id="39505-254">In the *FileProviderSample* sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container.</span></span> <span data-ttu-id="39505-255">W metodzie projektu znaleziono następujący kod `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="39505-255">The following code is found in the project's `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="39505-256">Obejrzyj zmiany</span><span class="sxs-lookup"><span data-stu-id="39505-256">Watch for changes</span></span>

<span data-ttu-id="39505-257"><xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType>Metoda zawiera scenariusz, aby obejrzeć zmiany w jednym lub kilku plikach lub katalogach.</span><span class="sxs-lookup"><span data-stu-id="39505-257">The <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="39505-258">`Watch`Metoda:</span><span class="sxs-lookup"><span data-stu-id="39505-258">The `Watch` method:</span></span>

* <span data-ttu-id="39505-259">Akceptuje ciąg ścieżki pliku, który może używać [wzorców globalizowania](#glob-patterns) do określenia wielu plików.</span><span class="sxs-lookup"><span data-stu-id="39505-259">Accepts a file path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span>
* <span data-ttu-id="39505-260">Zwraca wartość <xref:Microsoft.Extensions.Primitives.IChangeToken> .</span><span class="sxs-lookup"><span data-stu-id="39505-260">Returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span>

<span data-ttu-id="39505-261">Otrzymany token zmiany ujawnia:</span><span class="sxs-lookup"><span data-stu-id="39505-261">The resulting change token exposes:</span></span>

* <span data-ttu-id="39505-262"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: Właściwość, którą można sprawdzić w celu ustalenia, czy wprowadzono zmianę.</span><span class="sxs-lookup"><span data-stu-id="39505-262"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="39505-263"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Wywołuje się, gdy zostaną wykryte zmiany do określonego ciągu ścieżki.</span><span class="sxs-lookup"><span data-stu-id="39505-263"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="39505-264">Każdy token zmiany wywołuje tylko skojarzone wywołanie zwrotne w odpowiedzi na pojedynczą zmianę.</span><span class="sxs-lookup"><span data-stu-id="39505-264">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="39505-265">Aby włączyć monitorowanie stałe, użyj <xref:System.Threading.Tasks.TaskCompletionSource`1> (pokazanego poniżej) lub ponownie Utwórz `IChangeToken` wystąpienia w odpowiedzi na zmiany.</span><span class="sxs-lookup"><span data-stu-id="39505-265">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="39505-266">Przykładowa aplikacja *WatchConsole* zapisuje komunikat za każdym razem, gdy plik *txt* w katalogu *TextFiles* zostanie zmodyfikowany:</span><span class="sxs-lookup"><span data-stu-id="39505-266">The *WatchConsole* sample app writes a message whenever a *.txt* file in the *TextFiles* directory is modified:</span></span>

[!code-csharp[](file-providers/samples/3.x/WatchConsole/Program.cs?name=snippet1)]

<span data-ttu-id="39505-267">Niektóre systemy plików, takie jak kontenery platformy Docker i udziały sieciowe, mogą niezawodnie wysyłać powiadomienia o zmianach.</span><span class="sxs-lookup"><span data-stu-id="39505-267">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="39505-268">Ustaw `DOTNET_USE_POLLING_FILE_WATCHER` zmienną środowiskową na `1` lub `true` , aby sondować system plików pod kątem zmian co cztery sekundy (nie można skonfigurować).</span><span class="sxs-lookup"><span data-stu-id="39505-268">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

### <a name="glob-patterns"></a><span data-ttu-id="39505-269">Wzorce globalizowania</span><span class="sxs-lookup"><span data-stu-id="39505-269">Glob patterns</span></span>

<span data-ttu-id="39505-270">Ścieżki systemu plików używają wzorców symboli wieloznacznych o nazwie *globalizowania (lub obsługi symboli wieloznacznych)*.</span><span class="sxs-lookup"><span data-stu-id="39505-270">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="39505-271">Określ grupy plików z tymi wzorcami.</span><span class="sxs-lookup"><span data-stu-id="39505-271">Specify groups of files with these patterns.</span></span> <span data-ttu-id="39505-272">Dwa symbole wieloznaczne to `*` `**` :</span><span class="sxs-lookup"><span data-stu-id="39505-272">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="39505-273">Dopasowuje wszystko na bieżącym poziomie folderu, dowolnej nazwie pliku lub dowolnym rozszerzeniu pliku.</span><span class="sxs-lookup"><span data-stu-id="39505-273">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="39505-274">Dopasowania są kończone przez `/` `.` znaki i w ścieżce pliku.</span><span class="sxs-lookup"><span data-stu-id="39505-274">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="39505-275">Dopasowuje wszystko na wielu poziomach katalogów.</span><span class="sxs-lookup"><span data-stu-id="39505-275">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="39505-276">Może służyć do rekursywnego dopasowania wielu plików w hierarchii katalogów.</span><span class="sxs-lookup"><span data-stu-id="39505-276">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="39505-277">W poniższej tabeli przedstawiono typowe przykłady wzorców globalizowania.</span><span class="sxs-lookup"><span data-stu-id="39505-277">The following table provides common examples of glob patterns.</span></span>

|<span data-ttu-id="39505-278">Wzorce</span><span class="sxs-lookup"><span data-stu-id="39505-278">Pattern</span></span>  |<span data-ttu-id="39505-279">Opis</span><span class="sxs-lookup"><span data-stu-id="39505-279">Description</span></span>  |
|---
<span data-ttu-id="39505-280">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="39505-280">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39505-281">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39505-281">'Blazor'</span></span>
- <span data-ttu-id="39505-282">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39505-282">'Identity'</span></span>
- <span data-ttu-id="39505-283">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39505-283">'Let's Encrypt'</span></span>
- <span data-ttu-id="39505-284">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39505-284">'Razor'</span></span>
- <span data-ttu-id="39505-285">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="39505-285">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39505-286">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="39505-286">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39505-287">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39505-287">'Blazor'</span></span>
- <span data-ttu-id="39505-288">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39505-288">'Identity'</span></span>
- <span data-ttu-id="39505-289">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39505-289">'Let's Encrypt'</span></span>
- <span data-ttu-id="39505-290">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39505-290">'Razor'</span></span>
- <span data-ttu-id="39505-291">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="39505-291">'SignalR' uid:</span></span> 

<span data-ttu-id="39505-292">-----|---
title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="39505-292">-----|---
title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39505-293">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39505-293">'Blazor'</span></span>
- <span data-ttu-id="39505-294">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39505-294">'Identity'</span></span>
- <span data-ttu-id="39505-295">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39505-295">'Let's Encrypt'</span></span>
- <span data-ttu-id="39505-296">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39505-296">'Razor'</span></span>
- <span data-ttu-id="39505-297">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="39505-297">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39505-298">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="39505-298">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39505-299">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39505-299">'Blazor'</span></span>
- <span data-ttu-id="39505-300">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39505-300">'Identity'</span></span>
- <span data-ttu-id="39505-301">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39505-301">'Let's Encrypt'</span></span>
- <span data-ttu-id="39505-302">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39505-302">'Razor'</span></span>
- <span data-ttu-id="39505-303">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="39505-303">'SignalR' uid:</span></span> 

<span data-ttu-id="39505-304">-----|
|`directory/file.txt`| Dopasowuje określony plik w określonym katalogu. | |`directory/*.txt`| Dopasowuje wszystkie pliki z rozszerzeniem *. txt* w określonym katalogu. | |`directory/*/appsettings.json`| Dopasowuje wszystkie pliki *appSettings. JSON* w katalogach dokładnie o jeden poziom poniżej folderu *katalogu* . | |`directory/**/*.txt`| Dopasowuje wszystkie pliki z rozszerzeniem *. txt* , które znajdują się w dowolnym miejscu w folderze *katalogu* . |</span><span class="sxs-lookup"><span data-stu-id="39505-304">-----|
|`directory/file.txt`|Matches a specific file in a specific directory.| |`directory/*.txt`|Matches all files with *.txt* extension in a specific directory.| |`directory/*/appsettings.json`|Matches all *appsettings.json* files in directories exactly one level below the *directory* folder.| |`directory/**/*.txt`|Matches all files with a *.txt* extension found anywhere under the *directory* folder.|</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="39505-305">ASP.NET Core abstrakcję dostępu systemu plików przy użyciu dostawców plików.</span><span class="sxs-lookup"><span data-stu-id="39505-305">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="39505-306">Dostawcy plików są używani w całym ASP.NET Core Framework:</span><span class="sxs-lookup"><span data-stu-id="39505-306">File Providers are used throughout the ASP.NET Core framework:</span></span>

* <span data-ttu-id="39505-307"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment>udostępnia [katalog główny zawartości](xref:fundamentals/index#content-root) aplikacji i [katalogu głównego sieci Web](xref:fundamentals/index#web-root) jako `IFileProvider` typy.</span><span class="sxs-lookup"><span data-stu-id="39505-307"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="39505-308">[Oprogramowanie pośredniczące plików statycznych](xref:fundamentals/static-files) używa dostawców plików do lokalizowania plików statycznych.</span><span class="sxs-lookup"><span data-stu-id="39505-308">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="39505-309">[Razor](xref:mvc/views/razor)używa dostawców plików do lokalizowania stron i widoków.</span><span class="sxs-lookup"><span data-stu-id="39505-309">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="39505-310">Narzędzia .NET Core używają dostawców plików i wzorców globalizowania, aby określić, które pliki powinny zostać opublikowane.</span><span class="sxs-lookup"><span data-stu-id="39505-310">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="39505-311">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="39505-311">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="39505-312">Interfejsy dostawcy plików</span><span class="sxs-lookup"><span data-stu-id="39505-312">File Provider interfaces</span></span>

<span data-ttu-id="39505-313">Podstawowy interfejs to <xref:Microsoft.Extensions.FileProviders.IFileProvider> .</span><span class="sxs-lookup"><span data-stu-id="39505-313">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="39505-314">`IFileProvider`udostępnia metody:</span><span class="sxs-lookup"><span data-stu-id="39505-314">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="39505-315">Uzyskaj informacje o pliku ( <xref:Microsoft.Extensions.FileProviders.IFileInfo> ).</span><span class="sxs-lookup"><span data-stu-id="39505-315">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="39505-316">Uzyskaj informacje o katalogu ( <xref:Microsoft.Extensions.FileProviders.IDirectoryContents> ).</span><span class="sxs-lookup"><span data-stu-id="39505-316">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="39505-317">Skonfiguruj powiadomienia o zmianach (przy użyciu <xref:Microsoft.Extensions.Primitives.IChangeToken> ).</span><span class="sxs-lookup"><span data-stu-id="39505-317">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="39505-318">`IFileInfo`zapewnia metody i właściwości do pracy z plikami:</span><span class="sxs-lookup"><span data-stu-id="39505-318">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="39505-319"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length>(w bajtach)</span><span class="sxs-lookup"><span data-stu-id="39505-319"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="39505-320"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified>dniu</span><span class="sxs-lookup"><span data-stu-id="39505-320"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="39505-321">Można odczytać z pliku za pomocą metody [IFileInfo. CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) .</span><span class="sxs-lookup"><span data-stu-id="39505-321">You can read from the file using the [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) method.</span></span>

<span data-ttu-id="39505-322">Przykładowa aplikacja pokazuje, jak skonfigurować dostawcę plików w programie `Startup.ConfigureServices` do użycia w całej aplikacji za pomocą [iniekcji zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="39505-322">The sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="39505-323">Implementacje dostawcy plików</span><span class="sxs-lookup"><span data-stu-id="39505-323">File Provider implementations</span></span>

<span data-ttu-id="39505-324">Dostępne są trzy implementacje programu `IFileProvider` .</span><span class="sxs-lookup"><span data-stu-id="39505-324">Three implementations of `IFileProvider` are available.</span></span>

| <span data-ttu-id="39505-325">Implementacja</span><span class="sxs-lookup"><span data-stu-id="39505-325">Implementation</span></span> | <span data-ttu-id="39505-326">Opis</span><span class="sxs-lookup"><span data-stu-id="39505-326">Description</span></span> |
| ---
<span data-ttu-id="39505-327">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="39505-327">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39505-328">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39505-328">'Blazor'</span></span>
- <span data-ttu-id="39505-329">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39505-329">'Identity'</span></span>
- <span data-ttu-id="39505-330">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39505-330">'Let's Encrypt'</span></span>
- <span data-ttu-id="39505-331">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39505-331">'Razor'</span></span>
- <span data-ttu-id="39505-332">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="39505-332">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39505-333">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="39505-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39505-334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39505-334">'Blazor'</span></span>
- <span data-ttu-id="39505-335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39505-335">'Identity'</span></span>
- <span data-ttu-id="39505-336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39505-336">'Let's Encrypt'</span></span>
- <span data-ttu-id="39505-337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39505-337">'Razor'</span></span>
- <span data-ttu-id="39505-338">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="39505-338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39505-339">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="39505-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39505-340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39505-340">'Blazor'</span></span>
- <span data-ttu-id="39505-341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39505-341">'Identity'</span></span>
- <span data-ttu-id="39505-342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39505-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="39505-343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39505-343">'Razor'</span></span>
- <span data-ttu-id="39505-344">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="39505-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39505-345">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="39505-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39505-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39505-346">'Blazor'</span></span>
- <span data-ttu-id="39505-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39505-347">'Identity'</span></span>
- <span data-ttu-id="39505-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39505-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="39505-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39505-349">'Razor'</span></span>
- <span data-ttu-id="39505-350">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="39505-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39505-351">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="39505-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39505-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39505-352">'Blazor'</span></span>
- <span data-ttu-id="39505-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39505-353">'Identity'</span></span>
- <span data-ttu-id="39505-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39505-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="39505-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39505-355">'Razor'</span></span>
- <span data-ttu-id="39505-356">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="39505-356">'SignalR' uid:</span></span> 

<span data-ttu-id="39505-357">------- | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="39505-357">------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39505-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39505-358">'Blazor'</span></span>
- <span data-ttu-id="39505-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39505-359">'Identity'</span></span>
- <span data-ttu-id="39505-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39505-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="39505-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39505-361">'Razor'</span></span>
- <span data-ttu-id="39505-362">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="39505-362">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39505-363">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="39505-363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39505-364">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39505-364">'Blazor'</span></span>
- <span data-ttu-id="39505-365">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39505-365">'Identity'</span></span>
- <span data-ttu-id="39505-366">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39505-366">'Let's Encrypt'</span></span>
- <span data-ttu-id="39505-367">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39505-367">'Razor'</span></span>
- <span data-ttu-id="39505-368">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="39505-368">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39505-369">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="39505-369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39505-370">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39505-370">'Blazor'</span></span>
- <span data-ttu-id="39505-371">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39505-371">'Identity'</span></span>
- <span data-ttu-id="39505-372">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39505-372">'Let's Encrypt'</span></span>
- <span data-ttu-id="39505-373">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39505-373">'Razor'</span></span>
- <span data-ttu-id="39505-374">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="39505-374">'SignalR' uid:</span></span> 

<span data-ttu-id="39505-375">------ | | [PhysicalFileProvider](#physicalfileprovider) | Dostawca fizyczny jest używany do uzyskiwania dostępu do plików fizycznych systemu.</span><span class="sxs-lookup"><span data-stu-id="39505-375">------ | | [PhysicalFileProvider](#physicalfileprovider) | The physical provider is used to access the system's physical files.</span></span> <span data-ttu-id="39505-376">| | [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | Dostawca osadzony manifestu służy do uzyskiwania dostępu do plików osadzonych w zestawach.</span><span class="sxs-lookup"><span data-stu-id="39505-376">| | [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | The manifest embedded provider is used to access files embedded in assemblies.</span></span> <span data-ttu-id="39505-377">| | [CompositeFileProvider](#compositefileprovider) | Dostawca złożony służy do zapewniania połączonego dostępu do plików i katalogów z jednego lub kilku innych dostawców.</span><span class="sxs-lookup"><span data-stu-id="39505-377">| | [CompositeFileProvider](#compositefileprovider) | The composite provider is used to provide combined access to files and directories from one or more other providers.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="39505-378">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="39505-378">PhysicalFileProvider</span></span>

<span data-ttu-id="39505-379"><xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>Zapewnia dostęp do fizycznego systemu plików.</span><span class="sxs-lookup"><span data-stu-id="39505-379">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="39505-380">`PhysicalFileProvider`używa <xref:System.IO.File?displayProperty=fullName> typu (dla dostawcy fizycznego) i zakresy wszystkie ścieżki do katalogu i jego elementów podrzędnych.</span><span class="sxs-lookup"><span data-stu-id="39505-380">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="39505-381">Takie Określanie zakresu uniemożliwia dostęp do systemu plików poza określonym katalogiem i jego elementami podrzędnymi.</span><span class="sxs-lookup"><span data-stu-id="39505-381">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="39505-382">Najbardziej typowym scenariuszem tworzenia i używania elementu `PhysicalFileProvider` jest zażądanie `IFileProvider` w konstruktorze przy użyciu [iniekcji zależności](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="39505-382">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="39505-383">W przypadku bezpośredniego tworzenia wystąpienia tego dostawcy ścieżka katalogu jest wymagana i służy jako ścieżka podstawowa dla wszystkich żądań wysyłanych przy użyciu dostawcy.</span><span class="sxs-lookup"><span data-stu-id="39505-383">When instantiating this provider directly, a directory path is required and serves as the base path for all requests made using the provider.</span></span>

<span data-ttu-id="39505-384">Poniższy kod przedstawia sposób tworzenia `PhysicalFileProvider` i używania go do uzyskiwania informacji o zawartości i pliku katalogu:</span><span class="sxs-lookup"><span data-stu-id="39505-384">The following code shows how to create a `PhysicalFileProvider` and use it to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

<span data-ttu-id="39505-385">Typy w poprzednim przykładzie:</span><span class="sxs-lookup"><span data-stu-id="39505-385">Types in the preceding example:</span></span>

* <span data-ttu-id="39505-386">`provider`jest `IFileProvider` .</span><span class="sxs-lookup"><span data-stu-id="39505-386">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="39505-387">`contents`jest `IDirectoryContents` .</span><span class="sxs-lookup"><span data-stu-id="39505-387">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="39505-388">`fileInfo`jest `IFileInfo` .</span><span class="sxs-lookup"><span data-stu-id="39505-388">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="39505-389">Dostawca plików może służyć do iteracji przez katalog określony przez `applicationRoot` lub wywołanie `GetFileInfo` w celu uzyskania informacji o pliku.</span><span class="sxs-lookup"><span data-stu-id="39505-389">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="39505-390">Dostawca plików nie ma dostępu poza `applicationRoot` katalogiem.</span><span class="sxs-lookup"><span data-stu-id="39505-390">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="39505-391">Przykładowa aplikacja tworzy dostawcę w `Startup.ConfigureServices` klasie aplikacji za pomocą [IHostingEnvironment. ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span><span class="sxs-lookup"><span data-stu-id="39505-391">The sample app creates the provider in the app's `Startup.ConfigureServices` class using [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="39505-392">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="39505-392">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="39505-393">Służy <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> do uzyskiwania dostępu do plików osadzonych w zestawach.</span><span class="sxs-lookup"><span data-stu-id="39505-393">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="39505-394">`ManifestEmbeddedFileProvider`Używa manifestu skompilowanego w zestawie, aby odtworzyć oryginalne ścieżki osadzonych plików.</span><span class="sxs-lookup"><span data-stu-id="39505-394">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="39505-395">Aby wygenerować manifest osadzonych plików, należy ustawić `<GenerateEmbeddedFilesManifest>` Właściwość na `true` .</span><span class="sxs-lookup"><span data-stu-id="39505-395">To generate a manifest of the embedded files, set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="39505-396">Określ pliki do osadzenia przy użyciu [ &lt; EmbeddedResource &gt; ](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span><span class="sxs-lookup"><span data-stu-id="39505-396">Specify the files to embed with [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

<span data-ttu-id="39505-397">Użyj [wzorców globalizowania](#glob-patterns) , aby określić jeden lub więcej plików do osadzenia w zestawie.</span><span class="sxs-lookup"><span data-stu-id="39505-397">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="39505-398">Przykładowa aplikacja tworzy `ManifestEmbeddedFileProvider` i przekazuje aktualnie wykonywany zestaw do jego konstruktora.</span><span class="sxs-lookup"><span data-stu-id="39505-398">The sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="39505-399">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="39505-399">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="39505-400">Dodatkowe przeciążenia umożliwiają:</span><span class="sxs-lookup"><span data-stu-id="39505-400">Additional overloads allow you to:</span></span>

* <span data-ttu-id="39505-401">Określ względną ścieżkę pliku.</span><span class="sxs-lookup"><span data-stu-id="39505-401">Specify a relative file path.</span></span>
* <span data-ttu-id="39505-402">Zakres plików do daty ostatniej modyfikacji.</span><span class="sxs-lookup"><span data-stu-id="39505-402">Scope files to a last modified date.</span></span>
* <span data-ttu-id="39505-403">Nazwij osadzony zasób zawierający manifest pliku osadzonego.</span><span class="sxs-lookup"><span data-stu-id="39505-403">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="39505-404">Występują</span><span class="sxs-lookup"><span data-stu-id="39505-404">Overload</span></span> | <span data-ttu-id="39505-405">Opis</span><span class="sxs-lookup"><span data-stu-id="39505-405">Description</span></span> |
| ---
<span data-ttu-id="39505-406">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="39505-406">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39505-407">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39505-407">'Blazor'</span></span>
- <span data-ttu-id="39505-408">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39505-408">'Identity'</span></span>
- <span data-ttu-id="39505-409">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39505-409">'Let's Encrypt'</span></span>
- <span data-ttu-id="39505-410">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39505-410">'Razor'</span></span>
- <span data-ttu-id="39505-411">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="39505-411">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39505-412">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="39505-412">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39505-413">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39505-413">'Blazor'</span></span>
- <span data-ttu-id="39505-414">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39505-414">'Identity'</span></span>
- <span data-ttu-id="39505-415">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39505-415">'Let's Encrypt'</span></span>
- <span data-ttu-id="39505-416">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39505-416">'Razor'</span></span>
- <span data-ttu-id="39505-417">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="39505-417">'SignalR' uid:</span></span> 

<span data-ttu-id="39505-418">---- | ---title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="39505-418">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39505-419">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39505-419">'Blazor'</span></span>
- <span data-ttu-id="39505-420">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39505-420">'Identity'</span></span>
- <span data-ttu-id="39505-421">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39505-421">'Let's Encrypt'</span></span>
- <span data-ttu-id="39505-422">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39505-422">'Razor'</span></span>
- <span data-ttu-id="39505-423">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="39505-423">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39505-424">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="39505-424">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39505-425">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39505-425">'Blazor'</span></span>
- <span data-ttu-id="39505-426">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39505-426">'Identity'</span></span>
- <span data-ttu-id="39505-427">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39505-427">'Let's Encrypt'</span></span>
- <span data-ttu-id="39505-428">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39505-428">'Razor'</span></span>
- <span data-ttu-id="39505-429">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="39505-429">'SignalR' uid:</span></span> 

-
<span data-ttu-id="39505-430">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="39505-430">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="39505-431">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="39505-431">'Blazor'</span></span>
- <span data-ttu-id="39505-432">'Identity'</span><span class="sxs-lookup"><span data-stu-id="39505-432">'Identity'</span></span>
- <span data-ttu-id="39505-433">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="39505-433">'Let's Encrypt'</span></span>
- <span data-ttu-id="39505-434">'Razor'</span><span class="sxs-lookup"><span data-stu-id="39505-434">'Razor'</span></span>
- <span data-ttu-id="39505-435">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="39505-435">'SignalR' uid:</span></span> 

<span data-ttu-id="39505-436">------ | | `ManifestEmbeddedFileProvider(Assembly, String)` | Akceptuje opcjonalny `root` parametr ścieżki względnej.</span><span class="sxs-lookup"><span data-stu-id="39505-436">------ | | `ManifestEmbeddedFileProvider(Assembly, String)` | Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="39505-437">Określ do `root` zakresu wywołania do <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> tych zasobów w ramach podanej ścieżki.</span><span class="sxs-lookup"><span data-stu-id="39505-437">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> <span data-ttu-id="39505-438">| | `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Akceptuje opcjonalny `root` parametr ścieżki względnej i `lastModified` parametr Date ( <xref:System.DateTimeOffset> ).</span><span class="sxs-lookup"><span data-stu-id="39505-438">| | `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="39505-439">`lastModified`Data zakresy daty ostatniej modyfikacji dla <xref:Microsoft.Extensions.FileProviders.IFileInfo> wystąpień zwracanych przez <xref:Microsoft.Extensions.FileProviders.IFileProvider> .</span><span class="sxs-lookup"><span data-stu-id="39505-439">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="39505-440">| | `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Akceptuje opcjonalną `root` ścieżkę względną, `lastModified` datę i `manifestName` parametry.</span><span class="sxs-lookup"><span data-stu-id="39505-440">| | `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="39505-441">`manifestName`Reprezentuje nazwę zasobu osadzonego zawierającego manifest.</span><span class="sxs-lookup"><span data-stu-id="39505-441">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="39505-442">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="39505-442">CompositeFileProvider</span></span>

<span data-ttu-id="39505-443"><xref:Microsoft.Extensions.FileProviders.CompositeFileProvider>Łączy `IFileProvider` wystąpienia, uwidaczniając pojedynczy interfejs do pracy z plikami z wielu dostawców.</span><span class="sxs-lookup"><span data-stu-id="39505-443">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="39505-444">Podczas tworzenia `CompositeFileProvider` , należy przekazać co najmniej jedno `IFileProvider` wystąpienie do jego konstruktora.</span><span class="sxs-lookup"><span data-stu-id="39505-444">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="39505-445">W przykładowej aplikacji a `PhysicalFileProvider` i `ManifestEmbeddedFileProvider` udostępniaj pliki `CompositeFileProvider` zarejestrowane w kontenerze usługi aplikacji:</span><span class="sxs-lookup"><span data-stu-id="39505-445">In the sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container:</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="39505-446">Obejrzyj zmiany</span><span class="sxs-lookup"><span data-stu-id="39505-446">Watch for changes</span></span>

<span data-ttu-id="39505-447">Metoda [IFileProvider. Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) zawiera scenariusz, aby obejrzeć zmiany w jednym lub kilku plikach lub katalogach.</span><span class="sxs-lookup"><span data-stu-id="39505-447">The [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="39505-448">`Watch`akceptuje ciąg ścieżki, który może używać [wzorców globalizowania](#glob-patterns) do określenia wielu plików.</span><span class="sxs-lookup"><span data-stu-id="39505-448">`Watch` accepts a path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span> <span data-ttu-id="39505-449">`Watch`Zwraca wartość <xref:Microsoft.Extensions.Primitives.IChangeToken> .</span><span class="sxs-lookup"><span data-stu-id="39505-449">`Watch` returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span> <span data-ttu-id="39505-450">Token zmiany ujawnia:</span><span class="sxs-lookup"><span data-stu-id="39505-450">The change token exposes:</span></span>

* <span data-ttu-id="39505-451"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: Właściwość, którą można sprawdzić w celu ustalenia, czy wprowadzono zmianę.</span><span class="sxs-lookup"><span data-stu-id="39505-451"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="39505-452"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Wywołuje się, gdy zostaną wykryte zmiany do określonego ciągu ścieżki.</span><span class="sxs-lookup"><span data-stu-id="39505-452"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="39505-453">Każdy token zmiany wywołuje tylko skojarzone wywołanie zwrotne w odpowiedzi na pojedynczą zmianę.</span><span class="sxs-lookup"><span data-stu-id="39505-453">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="39505-454">Aby włączyć monitorowanie stałe, użyj <xref:System.Threading.Tasks.TaskCompletionSource`1> (pokazanego poniżej) lub ponownie Utwórz `IChangeToken` wystąpienia w odpowiedzi na zmiany.</span><span class="sxs-lookup"><span data-stu-id="39505-454">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="39505-455">W aplikacji przykładowej Aplikacja konsolowa *WatchConsole* jest skonfigurowana do wyświetlania komunikatu za każdym razem, gdy plik tekstowy zostanie zmodyfikowany:</span><span class="sxs-lookup"><span data-stu-id="39505-455">In the sample app, the *WatchConsole* console app is configured to display a message whenever a text file is modified:</span></span>

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

<span data-ttu-id="39505-456">Niektóre systemy plików, takie jak kontenery platformy Docker i udziały sieciowe, mogą niezawodnie wysyłać powiadomienia o zmianach.</span><span class="sxs-lookup"><span data-stu-id="39505-456">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="39505-457">Ustaw `DOTNET_USE_POLLING_FILE_WATCHER` zmienną środowiskową na `1` lub `true` , aby sondować system plików pod kątem zmian co cztery sekundy (nie można skonfigurować).</span><span class="sxs-lookup"><span data-stu-id="39505-457">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

## <a name="glob-patterns"></a><span data-ttu-id="39505-458">Wzorce globalizowania</span><span class="sxs-lookup"><span data-stu-id="39505-458">Glob patterns</span></span>

<span data-ttu-id="39505-459">Ścieżki systemu plików używają wzorców symboli wieloznacznych o nazwie *globalizowania (lub obsługi symboli wieloznacznych)*.</span><span class="sxs-lookup"><span data-stu-id="39505-459">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="39505-460">Określ grupy plików z tymi wzorcami.</span><span class="sxs-lookup"><span data-stu-id="39505-460">Specify groups of files with these patterns.</span></span> <span data-ttu-id="39505-461">Dwa symbole wieloznaczne to `*` `**` :</span><span class="sxs-lookup"><span data-stu-id="39505-461">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="39505-462">Dopasowuje wszystko na bieżącym poziomie folderu, dowolnej nazwie pliku lub dowolnym rozszerzeniu pliku.</span><span class="sxs-lookup"><span data-stu-id="39505-462">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="39505-463">Dopasowania są kończone przez `/` `.` znaki i w ścieżce pliku.</span><span class="sxs-lookup"><span data-stu-id="39505-463">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="39505-464">Dopasowuje wszystko na wielu poziomach katalogów.</span><span class="sxs-lookup"><span data-stu-id="39505-464">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="39505-465">Może służyć do rekursywnego dopasowania wielu plików w hierarchii katalogów.</span><span class="sxs-lookup"><span data-stu-id="39505-465">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="39505-466">**Przykłady wzorców globalizowania**</span><span class="sxs-lookup"><span data-stu-id="39505-466">**Glob pattern examples**</span></span>

**`directory/file.txt`**  
<span data-ttu-id="39505-467">Dopasowuje określony plik w określonym katalogu.</span><span class="sxs-lookup"><span data-stu-id="39505-467">Matches a specific file in a specific directory.</span></span>

**`directory/*.txt`**  
<span data-ttu-id="39505-468">Dopasowuje wszystkie pliki z rozszerzeniem *. txt* w określonym katalogu.</span><span class="sxs-lookup"><span data-stu-id="39505-468">Matches all files with *.txt* extension in a specific directory.</span></span>

**`directory/*/appsettings.json`**  
<span data-ttu-id="39505-469">Dopasowuje wszystkie `appsettings.json` pliki w katalogach dokładnie o jeden poziom poniżej folderu *katalogu* .</span><span class="sxs-lookup"><span data-stu-id="39505-469">Matches all `appsettings.json` files in directories exactly one level below the *directory* folder.</span></span>

**`directory/**/*.txt`**  
<span data-ttu-id="39505-470">Dopasowuje wszystkie pliki z rozszerzeniem *. txt* , które znajdują się w dowolnym miejscu w folderze *katalogu* .</span><span class="sxs-lookup"><span data-stu-id="39505-470">Matches all files with *.txt* extension found anywhere under the *directory* folder.</span></span>

::: moniker-end
