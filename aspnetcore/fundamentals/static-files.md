---
title: Pliki statyczne w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak udostępniać i zabezpieczać pliki statyczne oraz skonfigurować zachowania oprogramowania pośredniczącego do obsługi plików statycznych w aplikacji internetowej ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 6/23/2020
no-loc:
- cookie
- Cookie
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/static-files
ms.openlocfilehash: 32f794d498533e846343831d2360e98939105fb1
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88016683"
---
# <a name="static-files-in-aspnet-core"></a><span data-ttu-id="08260-103">Pliki statyczne w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="08260-103">Static files in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="08260-104">Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT) i [Kirka Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="08260-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="08260-105">Pliki statyczne, takie jak HTML, CSS, images i JavaScript, są zasobami, które domyślnie aplikacja ASP.NET Core bezpośrednio obsługuje klientów.</span><span class="sxs-lookup"><span data-stu-id="08260-105">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients by default.</span></span>

<span data-ttu-id="08260-106">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="08260-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="08260-107">Obsługuj pliki statyczne</span><span class="sxs-lookup"><span data-stu-id="08260-107">Serve static files</span></span>

<span data-ttu-id="08260-108">Pliki statyczne są przechowywane w katalogu [głównym sieci Web](xref:fundamentals/index#web-root) projektu.</span><span class="sxs-lookup"><span data-stu-id="08260-108">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="08260-109">Domyślnym katalogiem jest `{content root}/wwwroot` , ale można go zmienić za pomocą <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> metody.</span><span class="sxs-lookup"><span data-stu-id="08260-109">The default directory is `{content root}/wwwroot`, but it can be changed with the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> method.</span></span> <span data-ttu-id="08260-110">Aby uzyskać więcej informacji, zobacz [katalog główny zawartości](xref:fundamentals/index#content-root) i [katalog główny sieci Web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="08260-110">For more information, see [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="08260-111"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>Metoda ustawia katalog główny zawartości dla bieżącego katalogu:</span><span class="sxs-lookup"><span data-stu-id="08260-111">The <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> method sets the content root to the current directory:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Program2.cs?name=snippet_Program)]

<span data-ttu-id="08260-112">Poprzedni kod został utworzony przy użyciu szablonu aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="08260-112">The preceding code was created with the web app template.</span></span>

<span data-ttu-id="08260-113">Pliki statyczne są dostępne za pośrednictwem ścieżki względnej dla [katalogu głównego sieci Web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="08260-113">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="08260-114">Na przykład szablony projektu **aplikacji sieci Web** zawierają kilka folderów w `wwwroot` folderze:</span><span class="sxs-lookup"><span data-stu-id="08260-114">For example, the **Web Application** project templates contain several folders within the `wwwroot` folder:</span></span>

* `wwwroot`
  * `css`
  * `js`
  * `lib`

<span data-ttu-id="08260-115">Rozważ utworzenie folderu *wwwroot/images* i dodanie pliku *wwwroot/images/MyImage.jpg* .</span><span class="sxs-lookup"><span data-stu-id="08260-115">Consider creating the *wwwroot/images* folder and adding the *wwwroot/images/MyImage.jpg* file.</span></span> <span data-ttu-id="08260-116">Format identyfikatora URI dostępu do pliku w `images` folderze to `https://<hostname>/images/<image_file_name>` .</span><span class="sxs-lookup"><span data-stu-id="08260-116">The URI format to access a file in the `images` folder is `https://<hostname>/images/<image_file_name>`.</span></span> <span data-ttu-id="08260-117">Na przykład `https://localhost:5001/images/MyImage.jpg`</span><span class="sxs-lookup"><span data-stu-id="08260-117">For example, `https://localhost:5001/images/MyImage.jpg`</span></span>

### <a name="serve-files-in-web-root"></a><span data-ttu-id="08260-118">Obsługiwanie plików w katalogu głównym sieci Web</span><span class="sxs-lookup"><span data-stu-id="08260-118">Serve files in web root</span></span>

<span data-ttu-id="08260-119">Domyślne szablony aplikacji sieci Web wywołują <xref:Owin.StaticFileExtensions.UseStaticFiles%2A> metodę w `Startup.Configure` , co umożliwia obsługę plików statycznych:</span><span class="sxs-lookup"><span data-stu-id="08260-119">The default web app templates call the <xref:Owin.StaticFileExtensions.UseStaticFiles%2A> method in `Startup.Configure`, which enables static files to be served:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="08260-120">`UseStaticFiles`Przeciążenie metody bez parametrów oznacza pliki w [katalogu głównym sieci Web](xref:fundamentals/index#web-root) jako do zablokowania.</span><span class="sxs-lookup"><span data-stu-id="08260-120">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="08260-121">Następujące znaczniki odwołują się do *wwwroot/images/MyImage.jpg*:</span><span class="sxs-lookup"><span data-stu-id="08260-121">The following markup references *wwwroot/images/MyImage.jpg*:</span></span>

```html
<img src="~/images/MyImage.jpg" class="img" alt="My image" />
```

<span data-ttu-id="08260-122">W poprzednim kodzie znak tyldy `~/` wskazuje [element główny sieci Web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="08260-122">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="08260-123">Obsługiwanie plików poza katalogiem głównym sieci Web</span><span class="sxs-lookup"><span data-stu-id="08260-123">Serve files outside of web root</span></span>

<span data-ttu-id="08260-124">Rozważ hierarchię katalogów, w której pliki statyczne mają być obsługiwane poza [katalogiem głównym sieci Web](xref:fundamentals/index#web-root):</span><span class="sxs-lookup"><span data-stu-id="08260-124">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `red-rose.jpg`

<span data-ttu-id="08260-125">Żądanie może uzyskać dostęp do `red-rose.jpg` pliku przez skonfigurowanie pliku statycznego oprogramowania pośredniczącego w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="08260-125">A request can access the `red-rose.jpg` file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupRose.cs?name=snippet_Configure&highlight=15-22)]

<span data-ttu-id="08260-126">W powyższym kodzie hierarchia katalogów *MyStaticFiles* jest udostępniana publicznie za pośrednictwem segmentu identyfikatora URI *StaticFiles* .</span><span class="sxs-lookup"><span data-stu-id="08260-126">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="08260-127">Żądanie do `https://<hostname>/StaticFiles/images/red-rose.jpg` obsłużynia pliku *red-rose.jpg* .</span><span class="sxs-lookup"><span data-stu-id="08260-127">A request to `https://<hostname>/StaticFiles/images/red-rose.jpg` serves the *red-rose.jpg* file.</span></span>

<span data-ttu-id="08260-128">Następujące znaczniki odwołują się do *MyStaticFiles/images/red-rose.jpg*:</span><span class="sxs-lookup"><span data-stu-id="08260-128">The following markup references *MyStaticFiles/images/red-rose.jpg*:</span></span>

```html
<img src="~/StaticFiles/images/red-rose.jpg" class="img" alt="A red rose" />
```

### <a name="set-http-response-headers"></a><span data-ttu-id="08260-129">Ustawianie nagłówków odpowiedzi HTTP</span><span class="sxs-lookup"><span data-stu-id="08260-129">Set HTTP response headers</span></span>

<span data-ttu-id="08260-130"><xref:Microsoft.AspNetCore.Builder.StaticFileOptions>Obiekt może służyć do ustawiania nagłówków odpowiedzi HTTP.</span><span class="sxs-lookup"><span data-stu-id="08260-130">A <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> object can be used to set HTTP response headers.</span></span> <span data-ttu-id="08260-131">Oprócz konfigurowania pliku statycznego z poziomu [katalogu głównego sieci Web](xref:fundamentals/index#web-root), poniższy kod ustawia `Cache-Control` Nagłówek:</span><span class="sxs-lookup"><span data-stu-id="08260-131">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_Configure&highlight=15-24)]

<!-- Q: The preceding code sets max-age to 604800 seconds (7 days), so what does the following mean? -->

<span data-ttu-id="08260-132">Pliki statyczne są publicznie buforowane przez 600 sekund:</span><span class="sxs-lookup"><span data-stu-id="08260-132">Static files are publicly cacheable for 600 seconds:</span></span>

![Nagłówki odpowiedzi pokazujące nagłówek Cache-Control został dodany](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="08260-134">Autoryzacja pliku statycznego</span><span class="sxs-lookup"><span data-stu-id="08260-134">Static file authorization</span></span>

<span data-ttu-id="08260-135">Oprogramowanie pośredniczące plików statycznych nie zapewnia kontroli autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="08260-135">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="08260-136">Wszystkie pliki obsługiwane przez ten program, w tym w ramach `wwwroot` programu, są publicznie dostępne.</span><span class="sxs-lookup"><span data-stu-id="08260-136">Any files served by it, including those under `wwwroot`, are publicly accessible.</span></span> <span data-ttu-id="08260-137">Aby obpracować pliki na podstawie autoryzacji:</span><span class="sxs-lookup"><span data-stu-id="08260-137">To serve files based on authorization:</span></span>

* <span data-ttu-id="08260-138">Przechowuj je poza programem `wwwroot` i dowolnym katalogiem dostępnym dla oprogramowania pośredniczącego plików statycznych.</span><span class="sxs-lookup"><span data-stu-id="08260-138">Store them outside of `wwwroot` and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="08260-139">Można je obsłużyć za pomocą metody akcji, do której zastosowano autoryzację i zwrócić <xref:Microsoft.AspNetCore.Mvc.FileResult> obiekt:</span><span class="sxs-lookup"><span data-stu-id="08260-139">Serve them via an action method to which authorization is applied and return a <xref:Microsoft.AspNetCore.Mvc.FileResult> object:</span></span>

  [!code-csharp[](static-files/samples/3.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImage)]

## <a name="directory-browsing"></a><span data-ttu-id="08260-140">Przeglądanie katalogów</span><span class="sxs-lookup"><span data-stu-id="08260-140">Directory browsing</span></span>

<span data-ttu-id="08260-141">Przeglądanie katalogów umożliwia wyświetlanie listy katalogów w określonych katalogach.</span><span class="sxs-lookup"><span data-stu-id="08260-141">Directory browsing allows directory listing within specified directories.</span></span>

<span data-ttu-id="08260-142">Przeglądanie katalogów jest domyślnie wyłączone ze względów bezpieczeństwa.</span><span class="sxs-lookup"><span data-stu-id="08260-142">Directory browsing is disabled by default for security reasons.</span></span> <span data-ttu-id="08260-143">Aby uzyskać więcej informacji, zobacz [uwagi](#sc).</span><span class="sxs-lookup"><span data-stu-id="08260-143">For more information, see [Considerations](#sc).</span></span>

<span data-ttu-id="08260-144">Włącz przeglądanie katalogów za pomocą:</span><span class="sxs-lookup"><span data-stu-id="08260-144">Enable directory browsing with:</span></span>

* <span data-ttu-id="08260-145"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A>w programie `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="08260-145"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="08260-146"><xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A>w programie `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="08260-146"><xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> in `Startup.Configure`.</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ClassMembers&highlight=4,21-35)]

<span data-ttu-id="08260-147">Poprzedni kod umożliwia przeglądanie katalogów folderu *wwwroot/images* przy użyciu adresu URL `https://<hostname>/MyImages` z linkami do każdego pliku i folderu:</span><span class="sxs-lookup"><span data-stu-id="08260-147">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL `https://<hostname>/MyImages`, with links to each file and folder:</span></span>

![przeglądanie katalogów](static-files/_static/dir-browse.png)

## <a name="serve-default-documents"></a><span data-ttu-id="08260-149">Obsługuj dokumenty domyślne</span><span class="sxs-lookup"><span data-stu-id="08260-149">Serve default documents</span></span>

<span data-ttu-id="08260-150">Ustawienie strony domyślnej umożliwia odwiedzającym punkt startowy w witrynie.</span><span class="sxs-lookup"><span data-stu-id="08260-150">Setting a default page provides visitors a starting point on a site.</span></span> <span data-ttu-id="08260-151">Aby udostępnić stronę domyślną z `wwwroot` bez w pełni kwalifikowanego identyfikatora URI, wywołaj <xref:Owin.DefaultFilesExtensions.UseDefaultFiles%2A> metodę:</span><span class="sxs-lookup"><span data-stu-id="08260-151">To serve a default page from `wwwroot` without a fully qualified URI, call the <xref:Owin.DefaultFilesExtensions.UseDefaultFiles%2A> method:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="08260-152">`UseDefaultFiles`musi być wywoływana przed zapisaniem `UseStaticFiles` pliku domyślnego.</span><span class="sxs-lookup"><span data-stu-id="08260-152">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="08260-153">`UseDefaultFiles`to adres URL służący do ponownego zapisu, który nie obsługuje tego pliku.</span><span class="sxs-lookup"><span data-stu-id="08260-153">`UseDefaultFiles` is a URL rewriter that doesn't serve the file.</span></span>

<span data-ttu-id="08260-154">W programie `UseDefaultFiles` żądania do folderu w usłudze `wwwroot` Search dla:</span><span class="sxs-lookup"><span data-stu-id="08260-154">With `UseDefaultFiles`, requests to a folder in `wwwroot` search for:</span></span>

* <span data-ttu-id="08260-155">*default.htm*</span><span class="sxs-lookup"><span data-stu-id="08260-155">*default.htm*</span></span>
* <span data-ttu-id="08260-156">*default.html*</span><span class="sxs-lookup"><span data-stu-id="08260-156">*default.html*</span></span>
* <span data-ttu-id="08260-157">*index.htm*</span><span class="sxs-lookup"><span data-stu-id="08260-157">*index.htm*</span></span>
* <span data-ttu-id="08260-158">*index.html*</span><span class="sxs-lookup"><span data-stu-id="08260-158">*index.html*</span></span>

<span data-ttu-id="08260-159">Pierwszy plik znaleziony z listy jest obsługiwany tak, jakby żądanie było w pełni kwalifikowanym identyfikatorem URI.</span><span class="sxs-lookup"><span data-stu-id="08260-159">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="08260-160">Adres URL przeglądarki nadal odzwierciedla żądany identyfikator URI.</span><span class="sxs-lookup"><span data-stu-id="08260-160">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="08260-161">Następujący kod zmienia domyślną nazwę pliku na *mydefault.html*:</span><span class="sxs-lookup"><span data-stu-id="08260-161">The following code changes the default file name to *mydefault.html*:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_DefaultFiles)]

<span data-ttu-id="08260-162">Poniższy kod ilustruje `Startup.Configure` poprzedni kod:</span><span class="sxs-lookup"><span data-stu-id="08260-162">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_Configure&highlight=15-19)]

### <a name="usefileserver-for-default-documents"></a><span data-ttu-id="08260-163">UseFileServer dla dokumentów domyślnych</span><span class="sxs-lookup"><span data-stu-id="08260-163">UseFileServer for default documents</span></span>

<span data-ttu-id="08260-164"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*>łączy funkcje `UseStaticFiles` , `UseDefaultFiles` i opcjonalnie `UseDirectoryBrowser` .</span><span class="sxs-lookup"><span data-stu-id="08260-164"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="08260-165">Wywołaj, `app.UseFileServer` Aby włączyć obsługę plików statycznych i pliku domyślnego.</span><span class="sxs-lookup"><span data-stu-id="08260-165">Call `app.UseFileServer` to enable the serving of static files and the default file.</span></span> <span data-ttu-id="08260-166">Przeglądanie katalogów nie jest włączone.</span><span class="sxs-lookup"><span data-stu-id="08260-166">Directory browsing isn't enabled.</span></span> <span data-ttu-id="08260-167">Poniższy kod ilustruje `Startup.Configure` `UseFileServer` :</span><span class="sxs-lookup"><span data-stu-id="08260-167">The following code shows `Startup.Configure` with `UseFileServer`:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty2.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="08260-168">Poniższy kod umożliwia obsługę plików statycznych, plik domyślny i przeglądanie katalogów:</span><span class="sxs-lookup"><span data-stu-id="08260-168">The following code enables the serving of static files, the default file, and directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="08260-169">Poniższy kod ilustruje `Startup.Configure` poprzedni kod:</span><span class="sxs-lookup"><span data-stu-id="08260-169">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty3.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="08260-170">Weź pod uwagę następującą hierarchię katalogów:</span><span class="sxs-lookup"><span data-stu-id="08260-170">Consider the following directory hierarchy:</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `MyImage.jpg`
  * `default.html`

<span data-ttu-id="08260-171">Poniższy kod umożliwia obsługę plików statycznych, plików domyślnych i przeglądanie katalogów `MyStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="08260-171">The following code enables the serving of static files, the default file, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileServer.cs?name=snippet_ClassMembers&highlight=4,21-31)]

<span data-ttu-id="08260-172"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A>musi być wywoływana, gdy `EnableDirectoryBrowsing` wartość właściwości to `true` .</span><span class="sxs-lookup"><span data-stu-id="08260-172"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> must be called when the `EnableDirectoryBrowsing` property value is `true`.</span></span>

<span data-ttu-id="08260-173">Przy użyciu hierarchii plików i poprzedniego kodu adresy URL są rozpoznawane w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="08260-173">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="08260-174">URI</span><span class="sxs-lookup"><span data-stu-id="08260-174">URI</span></span>            |      <span data-ttu-id="08260-175">Odpowiedź</span><span class="sxs-lookup"><span data-stu-id="08260-175">Response</span></span>  |
| ------- | ------|
| `https://<hostname>/StaticFiles/images/MyImage.jpg` | <span data-ttu-id="08260-176">*MyStaticFiles/obrazy/MyImage.jpg*</span><span class="sxs-lookup"><span data-stu-id="08260-176">*MyStaticFiles/images/MyImage.jpg*</span></span> |
| `https://<hostname>/StaticFiles` | <span data-ttu-id="08260-177">*MyStaticFiles/default.html*</span><span class="sxs-lookup"><span data-stu-id="08260-177">*MyStaticFiles/default.html*</span></span> |

<span data-ttu-id="08260-178">Jeśli plik o nazwie Default nie istnieje w katalogu *MyStaticFiles* , `https://<hostname>/StaticFiles` zwraca listę katalogów z linkami, które można kliknąć:</span><span class="sxs-lookup"><span data-stu-id="08260-178">If no default-named file exists in the *MyStaticFiles* directory, `https://<hostname>/StaticFiles` returns the directory listing with clickable links:</span></span>

![Lista plików statycznych](static-files/_static/db2.png)

<span data-ttu-id="08260-180"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*>i <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> wykonać przekierowanie po stronie klienta z docelowego identyfikatora URI bez końcowej `/` do docelowego identyfikatora URI kończącego się na końcu `/` .</span><span class="sxs-lookup"><span data-stu-id="08260-180"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from the target URI without a trailing `/`  to the target URI with a trailing `/`.</span></span> <span data-ttu-id="08260-181">Na przykład z `https://<hostname>/StaticFiles` do `https://<hostname>/StaticFiles/` .</span><span class="sxs-lookup"><span data-stu-id="08260-181">For example, from `https://<hostname>/StaticFiles` to `https://<hostname>/StaticFiles/`.</span></span> <span data-ttu-id="08260-182">Względne adresy URL w katalogu *StaticFiles* są nieprawidłowe bez końcowego ukośnika ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="08260-182">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash (`/`).</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="08260-183">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="08260-183">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="08260-184"><xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider>Klasa zawiera `Mappings` Właściwość, która służy jako mapowanie rozszerzeń plików na typy zawartości MIME.</span><span class="sxs-lookup"><span data-stu-id="08260-184">The <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> class contains a `Mappings` property that serves as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="08260-185">W poniższym przykładzie kilka rozszerzeń plików są mapowane na znane typy MIME.</span><span class="sxs-lookup"><span data-stu-id="08260-185">In the following sample, several file extensions are mapped to known MIME types.</span></span> <span data-ttu-id="08260-186">Rozszerzenie *. rtf* jest zastępowane, a plik *MP4* zostaje usunięty:</span><span class="sxs-lookup"><span data-stu-id="08260-186">The *.rtf* extension is replaced, and *.mp4* is removed:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Provider)]

<span data-ttu-id="08260-187">Poniższy kod ilustruje `Startup.Configure` poprzedni kod:</span><span class="sxs-lookup"><span data-stu-id="08260-187">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Configure&highlight=15-43)]

<span data-ttu-id="08260-188">Zobacz [typy zawartości MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).</span><span class="sxs-lookup"><span data-stu-id="08260-188">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="08260-189">Niestandardowe typy zawartości</span><span class="sxs-lookup"><span data-stu-id="08260-189">Non-standard content types</span></span>

<span data-ttu-id="08260-190">Oprogramowanie pośredniczące plików statycznych rozpoznaje niemal 400 znanych typów zawartości plików.</span><span class="sxs-lookup"><span data-stu-id="08260-190">The Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="08260-191">Jeśli użytkownik zażąda pliku z nieznanym typem pliku, oprogramowanie pośredniczące plików przesyła żądanie do następnego oprogramowania pośredniczącego w potoku.</span><span class="sxs-lookup"><span data-stu-id="08260-191">If the user requests a file with an unknown file type, the Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="08260-192">Jeśli żadne oprogramowanie pośredniczące nie obsługuje żądania, zwracana jest odpowiedź *404* .</span><span class="sxs-lookup"><span data-stu-id="08260-192">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="08260-193">Jeśli przeglądanie katalogów jest włączone, link do pliku zostanie wyświetlony na liście katalogów.</span><span class="sxs-lookup"><span data-stu-id="08260-193">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="08260-194">Poniższy kod umożliwia obsługę nieznanych typów i renderuje nieznany plik jako obraz:</span><span class="sxs-lookup"><span data-stu-id="08260-194">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_UseStaticFiles)]

<span data-ttu-id="08260-195">Poniższy kod ilustruje `Startup.Configure` poprzedni kod:</span><span class="sxs-lookup"><span data-stu-id="08260-195">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_Configure&highlight=15-19)]

<span data-ttu-id="08260-196">W powyższym kodzie żądanie dotyczące pliku z nieznanym typem zawartości jest zwracane jako obraz.</span><span class="sxs-lookup"><span data-stu-id="08260-196">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="08260-197">Włączenie <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> stanowi zagrożenie bezpieczeństwa.</span><span class="sxs-lookup"><span data-stu-id="08260-197">Enabling <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> is a security risk.</span></span> <span data-ttu-id="08260-198">Jest on domyślnie wyłączony i nie jest zalecane jego użycie.</span><span class="sxs-lookup"><span data-stu-id="08260-198">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="08260-199">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) zapewnia bezpieczniejsze rozwiązanie do obsługi plików z rozszerzeniami niestandardowymi.</span><span class="sxs-lookup"><span data-stu-id="08260-199">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="08260-200">Obsługiwanie plików z wielu lokalizacji</span><span class="sxs-lookup"><span data-stu-id="08260-200">Serve files from multiple locations</span></span>

<span data-ttu-id="08260-201">`UseStaticFiles`i `UseFileServer` domyślny dla dostawcy plików wskazywanego przez `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="08260-201">`UseStaticFiles` and `UseFileServer` default to the file provider pointing at `wwwroot`.</span></span> <span data-ttu-id="08260-202">Dodatkowe wystąpienia `UseStaticFiles` i `UseFileServer` mogą być dostarczone z innymi dostawcami plików w celu obsłużenia plików z innych lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="08260-202">Additional instances of `UseStaticFiles` and `UseFileServer` can be provided with other file providers to serve files from other locations.</span></span> <span data-ttu-id="08260-203">Aby uzyskać więcej informacji, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/15578)w serwisie GitHub.</span><span class="sxs-lookup"><span data-stu-id="08260-203">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

<a name="sc"></a>

### <a name="security-considerations-for-static-files"></a><span data-ttu-id="08260-204">Zagadnienia dotyczące zabezpieczeń plików statycznych</span><span class="sxs-lookup"><span data-stu-id="08260-204">Security considerations for static files</span></span>

> [!WARNING]
> <span data-ttu-id="08260-205">`UseDirectoryBrowser`i `UseStaticFiles` mogą wyciekać klucze tajne.</span><span class="sxs-lookup"><span data-stu-id="08260-205">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="08260-206">Wyłączenie przeglądania katalogów w środowisku produkcyjnym jest zdecydowanie zalecane.</span><span class="sxs-lookup"><span data-stu-id="08260-206">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="08260-207">Uważnie Przejrzyj katalogi, które są włączone za pośrednictwem `UseStaticFiles` lub `UseDirectoryBrowser` .</span><span class="sxs-lookup"><span data-stu-id="08260-207">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="08260-208">Cały katalog i jego katalogi podrzędne stają się publicznie dostępne.</span><span class="sxs-lookup"><span data-stu-id="08260-208">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="08260-209">Pliki magazynu odpowiednie do obsłużenia publicznie w dedykowanym katalogu, na przykład `<content_root>/wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="08260-209">Store files suitable for serving to the public in a dedicated directory, such as `<content_root>/wwwroot`.</span></span> <span data-ttu-id="08260-210">Oddziel te pliki od widoków MVC, Razor stron, plików konfiguracji itp.</span><span class="sxs-lookup"><span data-stu-id="08260-210">Separate these files from MVC views, Razor Pages, configuration files, etc.</span></span>

* <span data-ttu-id="08260-211">Adresy URL dla zawartości uwidocznionej `UseDirectoryBrowser` i `UseStaticFiles` podlegają ograniczeniom dotyczącym wielkości liter i znaków w źródłowym systemie plików.</span><span class="sxs-lookup"><span data-stu-id="08260-211">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="08260-212">Na przykład w systemie Windows nie jest rozróżniana wielkość liter, ale macOS i Linux nie są.</span><span class="sxs-lookup"><span data-stu-id="08260-212">For example, Windows is case insensitive, but macOS and Linux aren't.</span></span>

* <span data-ttu-id="08260-213">ASP.NET Core aplikacje hostowane w usługach IIS używają [modułu ASP.NET Core](xref:host-and-deploy/aspnet-core-module) do przesyłania dalej wszystkich żądań do aplikacji, w tym żądań plików statycznych.</span><span class="sxs-lookup"><span data-stu-id="08260-213">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="08260-214">Procedura obsługi pliku statycznego usług IIS nie jest używana i nie ma możliwości obsługi żądań.</span><span class="sxs-lookup"><span data-stu-id="08260-214">The IIS static file handler isn't used and has no chance to handle requests.</span></span>

* <span data-ttu-id="08260-215">Wykonaj następujące kroki w Menedżerze usług IIS, aby usunąć program obsługi plików statycznych usług IIS na poziomie serwera lub witryny sieci Web:</span><span class="sxs-lookup"><span data-stu-id="08260-215">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="08260-216">Przejdź do funkcji **moduły** .</span><span class="sxs-lookup"><span data-stu-id="08260-216">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="08260-217">Na liście wybierz pozycję **StaticFileModule** .</span><span class="sxs-lookup"><span data-stu-id="08260-217">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="08260-218">Kliknij przycisk **Usuń** na pasku bocznym **Akcje** .</span><span class="sxs-lookup"><span data-stu-id="08260-218">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="08260-219">Jeśli program obsługi plików statycznych usług IIS jest włączony **i** moduł ASP.NET Core jest niepoprawnie skonfigurowany, obsługiwane są pliki statyczne.</span><span class="sxs-lookup"><span data-stu-id="08260-219">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="08260-220">Dzieje się tak na przykład wtedy, gdy plik *web.config* nie zostanie wdrożony.</span><span class="sxs-lookup"><span data-stu-id="08260-220">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="08260-221">Umieść pliki kodu, w tym *. cs* i *. cshtml*, poza [katalogiem głównym sieci Web](xref:fundamentals/index#web-root)projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="08260-221">Place code files, including *.cs* and *.cshtml*, outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="08260-222">W związku z tym tworzone jest podział logiczny między zawartością po stronie klienta a kodem opartym na serwerze.</span><span class="sxs-lookup"><span data-stu-id="08260-222">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="08260-223">Zapobiega to wyciekowi kodu po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="08260-223">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="08260-224">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="08260-224">Additional resources</span></span>

* [<span data-ttu-id="08260-225">Oprogramowanie pośredniczące</span><span class="sxs-lookup"><span data-stu-id="08260-225">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="08260-226">Wprowadzenie do programu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="08260-226">Introduction to ASP.NET Core</span></span>](xref:index)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="08260-227">Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT) i [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="08260-227">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="08260-228">Pliki statyczne, takie jak HTML, CSS, images i JavaScript, są zasobami, które aplikacja ASP.NET Core może bezpośrednio obsługiwać klientów.</span><span class="sxs-lookup"><span data-stu-id="08260-228">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients.</span></span> <span data-ttu-id="08260-229">Aby umożliwić obsługę tych plików, wymagana jest pewna konfiguracja.</span><span class="sxs-lookup"><span data-stu-id="08260-229">Some configuration is required to enable serving of these files.</span></span>

<span data-ttu-id="08260-230">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="08260-230">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="08260-231">Obsługuj pliki statyczne</span><span class="sxs-lookup"><span data-stu-id="08260-231">Serve static files</span></span>

<span data-ttu-id="08260-232">Pliki statyczne są przechowywane w katalogu [głównym sieci Web](xref:fundamentals/index#web-root) projektu.</span><span class="sxs-lookup"><span data-stu-id="08260-232">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="08260-233">Domyślnym katalogiem jest *{Content root}/wwwroot*, ale można go zmienić za pomocą <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> metody.</span><span class="sxs-lookup"><span data-stu-id="08260-233">The default directory is *{content root}/wwwroot*, but it can be changed via the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> method.</span></span> <span data-ttu-id="08260-234">Aby uzyskać więcej informacji, zobacz [katalog główny zawartości](xref:fundamentals/index#content-root) i [katalog główny sieci Web](xref:fundamentals/index#web-root) .</span><span class="sxs-lookup"><span data-stu-id="08260-234">See [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root) for more information.</span></span>

<span data-ttu-id="08260-235">Host sieci Web aplikacji musi być świadomy katalogu głównego zawartości.</span><span class="sxs-lookup"><span data-stu-id="08260-235">The app's web host must be made aware of the content root directory.</span></span>

<span data-ttu-id="08260-236">`WebHost.CreateDefaultBuilder`Metoda ustawia katalog główny zawartości dla bieżącego katalogu:</span><span class="sxs-lookup"><span data-stu-id="08260-236">The `WebHost.CreateDefaultBuilder` method sets the content root to the current directory:</span></span>

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

<span data-ttu-id="08260-237">Pliki statyczne są dostępne za pośrednictwem ścieżki względnej dla [katalogu głównego sieci Web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="08260-237">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="08260-238">Na przykład szablon projektu **aplikacji sieci Web** zawiera kilka folderów w `wwwroot` folderze:</span><span class="sxs-lookup"><span data-stu-id="08260-238">For example, the **Web Application** project template contains several folders within the `wwwroot` folder:</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`

<span data-ttu-id="08260-239">Format identyfikatora URI służący do uzyskiwania dostępu do pliku w podfolderze *images* to *http:// \<server_address> \<image_file_name> /images/*.</span><span class="sxs-lookup"><span data-stu-id="08260-239">The URI format to access a file in the *images* subfolder is *http://\<server_address>/images/\<image_file_name>*.</span></span> <span data-ttu-id="08260-240">Przykładowy adres URL to *http://localhost:9189/images/banner3.svg*.</span><span class="sxs-lookup"><span data-stu-id="08260-240">For example, *http://localhost:9189/images/banner3.svg*.</span></span>

<span data-ttu-id="08260-241">Jeśli .NET Framework określania wartości docelowej, Dodaj pakiet [Microsoft. AspNetCore. StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) do projektu.</span><span class="sxs-lookup"><span data-stu-id="08260-241">If targeting .NET Framework, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to the project.</span></span> <span data-ttu-id="08260-242">W przypadku określania platformy .NET Core [pakiet Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) zawiera ten pakiet.</span><span class="sxs-lookup"><span data-stu-id="08260-242">If targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) includes this package.</span></span>

<span data-ttu-id="08260-243">Skonfiguruj [oprogramowanie pośredniczące](xref:fundamentals/middleware/index), które umożliwia obsługę plików statycznych.</span><span class="sxs-lookup"><span data-stu-id="08260-243">Configure the [middleware](xref:fundamentals/middleware/index), which enables the serving of static files.</span></span>

### <a name="serve-files-inside-of-web-root"></a><span data-ttu-id="08260-244">Obsługuj pliki wewnątrz katalogu głównego sieci Web</span><span class="sxs-lookup"><span data-stu-id="08260-244">Serve files inside of web root</span></span>

<span data-ttu-id="08260-245">Wywołaj <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> metodę w `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="08260-245">Invoke the <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> method within `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

<span data-ttu-id="08260-246">`UseStaticFiles`Przeciążenie metody bez parametrów oznacza pliki w [katalogu głównym sieci Web](xref:fundamentals/index#web-root) jako do zablokowania.</span><span class="sxs-lookup"><span data-stu-id="08260-246">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="08260-247">Następujące znaczniki odwołują się do *wwwroot/images/banner1. SVG*:</span><span class="sxs-lookup"><span data-stu-id="08260-247">The following markup references *wwwroot/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

<span data-ttu-id="08260-248">W poprzednim kodzie znak tyldy `~/` wskazuje [element główny sieci Web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="08260-248">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="08260-249">Obsługiwanie plików poza katalogiem głównym sieci Web</span><span class="sxs-lookup"><span data-stu-id="08260-249">Serve files outside of web root</span></span>

<span data-ttu-id="08260-250">Rozważ hierarchię katalogów, w której pliki statyczne mają być obsługiwane poza [katalogiem głównym sieci Web](xref:fundamentals/index#web-root):</span><span class="sxs-lookup"><span data-stu-id="08260-250">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `banner1.svg`

<span data-ttu-id="08260-251">Żądanie może uzyskać dostęp do pliku *banner1. SVG* przez skonfigurowanie pliku statycznego pośredniczącego w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="08260-251">A request can access the *banner1.svg* file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

<span data-ttu-id="08260-252">W powyższym kodzie hierarchia katalogów *MyStaticFiles* jest udostępniana publicznie za pośrednictwem segmentu identyfikatora URI *StaticFiles* .</span><span class="sxs-lookup"><span data-stu-id="08260-252">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="08260-253">Żądanie *http:// \<server_address> /StaticFiles/images/banner1.SVG* obsługuje plik *banner1. SVG* .</span><span class="sxs-lookup"><span data-stu-id="08260-253">A request to *http://\<server_address>/StaticFiles/images/banner1.svg* serves the *banner1.svg* file.</span></span>

<span data-ttu-id="08260-254">Następujące znaczniki odwołują się do *MyStaticFiles/images/banner1. SVG*:</span><span class="sxs-lookup"><span data-stu-id="08260-254">The following markup references *MyStaticFiles/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a><span data-ttu-id="08260-255">Ustawianie nagłówków odpowiedzi HTTP</span><span class="sxs-lookup"><span data-stu-id="08260-255">Set HTTP response headers</span></span>

<span data-ttu-id="08260-256"><xref:Microsoft.AspNetCore.Builder.StaticFileOptions>Obiekt może służyć do ustawiania nagłówków odpowiedzi HTTP.</span><span class="sxs-lookup"><span data-stu-id="08260-256">A <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> object can be used to set HTTP response headers.</span></span> <span data-ttu-id="08260-257">Oprócz konfigurowania pliku statycznego z poziomu [katalogu głównego sieci Web](xref:fundamentals/index#web-root), poniższy kod ustawia `Cache-Control` Nagłówek:</span><span class="sxs-lookup"><span data-stu-id="08260-257">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_ConfigureMethod)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="08260-258"><xref:Microsoft.AspNetCore.Http.HeaderDictionaryExtensions.Append%2A?displayProperty=nameWithType>Metoda istnieje w pakiecie [Microsoft. AspNetCore. http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) .</span><span class="sxs-lookup"><span data-stu-id="08260-258">The <xref:Microsoft.AspNetCore.Http.HeaderDictionaryExtensions.Append%2A?displayProperty=nameWithType> method exists in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

<span data-ttu-id="08260-259">Pliki zostały publicznie przetworzone w pamięci podręcznej przez 10 minut (600 sekund) w środowisku deweloperskim:</span><span class="sxs-lookup"><span data-stu-id="08260-259">The files have been made publicly cacheable for 10 minutes (600 seconds) in the Development environment:</span></span>

![Nagłówki odpowiedzi pokazujące nagłówek Cache-Control został dodany](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="08260-261">Autoryzacja pliku statycznego</span><span class="sxs-lookup"><span data-stu-id="08260-261">Static file authorization</span></span>

<span data-ttu-id="08260-262">Oprogramowanie pośredniczące plików statycznych nie zapewnia kontroli autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="08260-262">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="08260-263">Wszystkie pliki obsługiwane przez ten program, w tym w katalogu *wwwroot*, są publicznie dostępne.</span><span class="sxs-lookup"><span data-stu-id="08260-263">Any files served by it, including those under *wwwroot*, are publicly accessible.</span></span> <span data-ttu-id="08260-264">Aby obpracować pliki na podstawie autoryzacji:</span><span class="sxs-lookup"><span data-stu-id="08260-264">To serve files based on authorization:</span></span>

* <span data-ttu-id="08260-265">Przechowaj je poza katalogiem *wwwroot* i dowolnym katalogu dostępnym dla oprogramowania pośredniczącego plików statycznych.</span><span class="sxs-lookup"><span data-stu-id="08260-265">Store them outside of *wwwroot* and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="08260-266">Obsłużyć je za pomocą metody akcji, do której zastosowano autoryzację.</span><span class="sxs-lookup"><span data-stu-id="08260-266">Serve them via an action method to which authorization is applied.</span></span> <span data-ttu-id="08260-267">Zwróć <xref:Microsoft.AspNetCore.Mvc.FileResult> obiekt:</span><span class="sxs-lookup"><span data-stu-id="08260-267">Return a <xref:Microsoft.AspNetCore.Mvc.FileResult> object:</span></span>

  [!code-csharp[](static-files/samples/1.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a><span data-ttu-id="08260-268">Włącz przeglądanie katalogów</span><span class="sxs-lookup"><span data-stu-id="08260-268">Enable directory browsing</span></span>

<span data-ttu-id="08260-269">Przeglądanie katalogów umożliwia użytkownikom aplikacji sieci Web Wyświetlanie listy katalogów i plików w określonym katalogu.</span><span class="sxs-lookup"><span data-stu-id="08260-269">Directory browsing allows users of your web app to see a directory listing and files within a specified directory.</span></span> <span data-ttu-id="08260-270">Przeglądanie katalogów jest domyślnie wyłączone ze względów bezpieczeństwa (zobacz [uwagi](#sc)).</span><span class="sxs-lookup"><span data-stu-id="08260-270">Directory browsing is disabled by default for security reasons (see [Considerations](#sc)).</span></span> <span data-ttu-id="08260-271">Włącz przeglądanie katalogów przez wywołanie <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> metody w `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="08260-271">Enable directory browsing by invoking the <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> method in `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

<span data-ttu-id="08260-272">Dodaj wymagane usługi, wywołując <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> metodę z `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="08260-272">Add required services by invoking the <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> method from `Startup.ConfigureServices`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

<span data-ttu-id="08260-273">Poprzedni kod umożliwia przeglądanie katalogów folderu *wwwroot/images* przy użyciu adresu URL *http:// \<server_address> /myimages*, z linkami do każdego pliku i folderu:</span><span class="sxs-lookup"><span data-stu-id="08260-273">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*, with links to each file and folder:</span></span>

![przeglądanie katalogów](static-files/_static/dir-browse.png)

<span data-ttu-id="08260-275">Zapoznaj się z [uwagami](#considerations) dotyczącymi zagrożeń bezpieczeństwa podczas włączania przeglądania.</span><span class="sxs-lookup"><span data-stu-id="08260-275">See [Considerations](#considerations) on the security risks when enabling browsing.</span></span>

<span data-ttu-id="08260-276">Zwróć uwagę na dwa `UseStaticFiles` wywołania w poniższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="08260-276">Note the two `UseStaticFiles` calls in the following example.</span></span> <span data-ttu-id="08260-277">Pierwsze wywołanie umożliwia obsługę plików statycznych w folderze *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="08260-277">The first call enables the serving of static files in the *wwwroot* folder.</span></span> <span data-ttu-id="08260-278">Drugie wywołanie umożliwia przeglądanie katalogów folderu *wwwroot/images* przy użyciu adresu URL *http:// \<server_address> /myimages*:</span><span class="sxs-lookup"><span data-stu-id="08260-278">The second call enables directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a><span data-ttu-id="08260-279">Obsługuj dokument domyślny</span><span class="sxs-lookup"><span data-stu-id="08260-279">Serve a default document</span></span>

<span data-ttu-id="08260-280">Ustawienie domyślnej strony głównej zapewnia odwiedzającym logiczny punkt wyjścia podczas odwiedzania witryny.</span><span class="sxs-lookup"><span data-stu-id="08260-280">Setting a default home page provides visitors a logical starting point when visiting your site.</span></span> <span data-ttu-id="08260-281">Aby obtworzyć stronę domyślną bez zakwalifikowania identyfikatora URI użytkownika, wywołaj <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> metodę z `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="08260-281">To serve a default page without the user fully qualifying the URI, call the <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> method from `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> <span data-ttu-id="08260-282">`UseDefaultFiles`musi być wywoływana przed zapisaniem `UseStaticFiles` pliku domyślnego.</span><span class="sxs-lookup"><span data-stu-id="08260-282">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="08260-283">`UseDefaultFiles`to adres URL, który faktycznie nie obsługuje pliku.</span><span class="sxs-lookup"><span data-stu-id="08260-283">`UseDefaultFiles` is a URL rewriter that doesn't actually serve the file.</span></span> <span data-ttu-id="08260-284">Włącz oprogramowanie pośredniczące plików statycznych za pośrednictwem programu `UseStaticFiles` w celu obsługi pliku.</span><span class="sxs-lookup"><span data-stu-id="08260-284">Enable Static File Middleware via `UseStaticFiles` to serve the file.</span></span>

<span data-ttu-id="08260-285">W programie `UseDefaultFiles` żądania do folderu wyszukują:</span><span class="sxs-lookup"><span data-stu-id="08260-285">With `UseDefaultFiles`, requests to a folder search for:</span></span>

* <span data-ttu-id="08260-286">*default.htm*</span><span class="sxs-lookup"><span data-stu-id="08260-286">*default.htm*</span></span>
* <span data-ttu-id="08260-287">*default.html*</span><span class="sxs-lookup"><span data-stu-id="08260-287">*default.html*</span></span>
* <span data-ttu-id="08260-288">*index.htm*</span><span class="sxs-lookup"><span data-stu-id="08260-288">*index.htm*</span></span>
* <span data-ttu-id="08260-289">*index.html*</span><span class="sxs-lookup"><span data-stu-id="08260-289">*index.html*</span></span>

<span data-ttu-id="08260-290">Pierwszy plik znaleziony z listy jest obsługiwany tak, jakby żądanie było w pełni kwalifikowanym identyfikatorem URI.</span><span class="sxs-lookup"><span data-stu-id="08260-290">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="08260-291">Adres URL przeglądarki nadal odzwierciedla żądany identyfikator URI.</span><span class="sxs-lookup"><span data-stu-id="08260-291">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="08260-292">Następujący kod zmienia domyślną nazwę pliku na *mydefault.html*:</span><span class="sxs-lookup"><span data-stu-id="08260-292">The following code changes the default file name to *mydefault.html*:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a><span data-ttu-id="08260-293">UseFileServer</span><span class="sxs-lookup"><span data-stu-id="08260-293">UseFileServer</span></span>

<span data-ttu-id="08260-294"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*>łączy funkcje `UseStaticFiles` , `UseDefaultFiles` i opcjonalnie `UseDirectoryBrowser` .</span><span class="sxs-lookup"><span data-stu-id="08260-294"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="08260-295">Poniższy kod umożliwia obsługę plików statycznych i pliku domyślnego.</span><span class="sxs-lookup"><span data-stu-id="08260-295">The following code enables the serving of static files and the default file.</span></span> <span data-ttu-id="08260-296">Przeglądanie katalogów nie jest włączone.</span><span class="sxs-lookup"><span data-stu-id="08260-296">Directory browsing isn't enabled.</span></span>

```csharp
app.UseFileServer();
```

<span data-ttu-id="08260-297">Poniższy kod kompiluje na Przeciążenie bez parametrów przez włączenie przeglądania katalogów:</span><span class="sxs-lookup"><span data-stu-id="08260-297">The following code builds upon the parameterless overload by enabling directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="08260-298">Weź pod uwagę następującą hierarchię katalogów:</span><span class="sxs-lookup"><span data-stu-id="08260-298">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="08260-299">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="08260-299">**wwwroot**</span></span>
  * <span data-ttu-id="08260-300">**kaskad**</span><span class="sxs-lookup"><span data-stu-id="08260-300">**css**</span></span>
  * <span data-ttu-id="08260-301">**rastrow**</span><span class="sxs-lookup"><span data-stu-id="08260-301">**images**</span></span>
  * <span data-ttu-id="08260-302">**JS**</span><span class="sxs-lookup"><span data-stu-id="08260-302">**js**</span></span>
* <span data-ttu-id="08260-303">**MyStaticFiles**</span><span class="sxs-lookup"><span data-stu-id="08260-303">**MyStaticFiles**</span></span>
  * <span data-ttu-id="08260-304">**rastrow**</span><span class="sxs-lookup"><span data-stu-id="08260-304">**images**</span></span>
    * <span data-ttu-id="08260-305">*banner1. SVG*</span><span class="sxs-lookup"><span data-stu-id="08260-305">*banner1.svg*</span></span>
  * <span data-ttu-id="08260-306">*default.html*</span><span class="sxs-lookup"><span data-stu-id="08260-306">*default.html*</span></span>

<span data-ttu-id="08260-307">Poniższy kod umożliwia włączenie plików statycznych, plików domyślnych i przeglądanie katalogów `MyStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="08260-307">The following code enables static files, default files, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

<span data-ttu-id="08260-308">`AddDirectoryBrowser`musi być wywoływana, gdy `EnableDirectoryBrowsing` wartość właściwości to `true` :</span><span class="sxs-lookup"><span data-stu-id="08260-308">`AddDirectoryBrowser` must be called when the `EnableDirectoryBrowsing` property value is `true`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

<span data-ttu-id="08260-309">Przy użyciu hierarchii plików i poprzedniego kodu adresy URL są rozpoznawane w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="08260-309">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="08260-310">URI</span><span class="sxs-lookup"><span data-stu-id="08260-310">URI</span></span>            |                             <span data-ttu-id="08260-311">Odpowiedź</span><span class="sxs-lookup"><span data-stu-id="08260-311">Response</span></span>  |
| ------- | ------|
| <span data-ttu-id="08260-312">*http:// \<server_address> /StaticFiles/images/banner1.SVG*</span><span class="sxs-lookup"><span data-stu-id="08260-312">*http://\<server_address>/StaticFiles/images/banner1.svg*</span></span>    |      <span data-ttu-id="08260-313">MyStaticFiles/images/banner1. SVG</span><span class="sxs-lookup"><span data-stu-id="08260-313">MyStaticFiles/images/banner1.svg</span></span> |
| <span data-ttu-id="08260-314">*http:// \<server_address> /StaticFiles*</span><span class="sxs-lookup"><span data-stu-id="08260-314">*http://\<server_address>/StaticFiles*</span></span>             |     <span data-ttu-id="08260-315">MyStaticFiles/default.html</span><span class="sxs-lookup"><span data-stu-id="08260-315">MyStaticFiles/default.html</span></span> |

<span data-ttu-id="08260-316">Jeśli plik o nazwie Default nie istnieje w katalogu *MyStaticFiles* , *http:// \<server_address> /StaticFiles* zwraca listę katalogów z linkami, które można kliknąć:</span><span class="sxs-lookup"><span data-stu-id="08260-316">If no default-named file exists in the *MyStaticFiles* directory, *http://\<server_address>/StaticFiles* returns the directory listing with clickable links:</span></span>

![Lista plików statycznych](static-files/_static/db2.png)

> [!NOTE]
> <span data-ttu-id="08260-318"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*>i <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> przekierować po stronie klienta z `http://{SERVER ADDRESS}/StaticFiles` (bez końcowego ukośnika) do `http://{SERVER ADDRESS}/StaticFiles/` (z końcowym ukośnikiem).</span><span class="sxs-lookup"><span data-stu-id="08260-318"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from `http://{SERVER ADDRESS}/StaticFiles` (without a trailing slash) to `http://{SERVER ADDRESS}/StaticFiles/` (with a trailing slash).</span></span> <span data-ttu-id="08260-319">Względne adresy URL w katalogu *StaticFiles* są nieprawidłowe bez końcowej kreski ułamkowej.</span><span class="sxs-lookup"><span data-stu-id="08260-319">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash.</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="08260-320">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="08260-320">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="08260-321"><xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider>Klasa zawiera `Mappings` Właściwość służącą jako mapowanie rozszerzeń plików na typy zawartości MIME.</span><span class="sxs-lookup"><span data-stu-id="08260-321">The <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> class contains a `Mappings` property serving as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="08260-322">W poniższym przykładzie kilka rozszerzeń plików są zarejestrowane na znanych typach MIME.</span><span class="sxs-lookup"><span data-stu-id="08260-322">In the following sample, several file extensions are registered to known MIME types.</span></span> <span data-ttu-id="08260-323">Rozszerzenie *. rtf* jest zastępowane, a plik *MP4* jest usuwany.</span><span class="sxs-lookup"><span data-stu-id="08260-323">The *.rtf* extension is replaced, and *.mp4* is removed.</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

<span data-ttu-id="08260-324">Zobacz [typy zawartości MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).</span><span class="sxs-lookup"><span data-stu-id="08260-324">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="08260-325">Niestandardowe typy zawartości</span><span class="sxs-lookup"><span data-stu-id="08260-325">Non-standard content types</span></span>

<span data-ttu-id="08260-326">Oprogramowanie pośredniczące plików static rozpoznaje niemal 400 znanych typów zawartości plików.</span><span class="sxs-lookup"><span data-stu-id="08260-326">Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="08260-327">Jeśli użytkownik zażąda pliku z nieznanym typem pliku, oprogramowanie pośredniczące plików przesyła żądanie do następnego oprogramowania pośredniczącego w potoku.</span><span class="sxs-lookup"><span data-stu-id="08260-327">If the user requests a file with an unknown file type, Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="08260-328">Jeśli żadne oprogramowanie pośredniczące nie obsługuje żądania, zwracana jest odpowiedź *404* .</span><span class="sxs-lookup"><span data-stu-id="08260-328">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="08260-329">Jeśli przeglądanie katalogów jest włączone, link do pliku zostanie wyświetlony na liście katalogów.</span><span class="sxs-lookup"><span data-stu-id="08260-329">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="08260-330">Poniższy kod umożliwia obsługę nieznanych typów i renderuje nieznany plik jako obraz:</span><span class="sxs-lookup"><span data-stu-id="08260-330">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

<span data-ttu-id="08260-331">W powyższym kodzie żądanie dotyczące pliku z nieznanym typem zawartości jest zwracane jako obraz.</span><span class="sxs-lookup"><span data-stu-id="08260-331">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="08260-332">Włączenie <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> stanowi zagrożenie bezpieczeństwa.</span><span class="sxs-lookup"><span data-stu-id="08260-332">Enabling <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> is a security risk.</span></span> <span data-ttu-id="08260-333">Jest on domyślnie wyłączony i nie jest zalecane jego użycie.</span><span class="sxs-lookup"><span data-stu-id="08260-333">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="08260-334">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) zapewnia bezpieczniejsze rozwiązanie do obsługi plików z rozszerzeniami niestandardowymi.</span><span class="sxs-lookup"><span data-stu-id="08260-334">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="08260-335">Obsługiwanie plików z wielu lokalizacji</span><span class="sxs-lookup"><span data-stu-id="08260-335">Serve files from multiple locations</span></span>

<span data-ttu-id="08260-336">`UseStaticFiles`i `UseFileServer` domyślnym dostawcą plików wskazywanym w lokalizacji *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="08260-336">`UseStaticFiles` and `UseFileServer` defaults to the file provider pointing at *wwwroot*.</span></span> <span data-ttu-id="08260-337">Można podać dodatkowe wystąpienia `UseStaticFiles` i `UseFileServer` z innymi dostawcami plików do obsługi plików z innych lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="08260-337">You can provide additional instances of `UseStaticFiles` and `UseFileServer` with other file providers to serve files from other locations.</span></span> <span data-ttu-id="08260-338">Aby uzyskać więcej informacji, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/15578)w serwisie GitHub.</span><span class="sxs-lookup"><span data-stu-id="08260-338">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

### <a name="considerations"></a><span data-ttu-id="08260-339">Zagadnienia do rozważenia</span><span class="sxs-lookup"><span data-stu-id="08260-339">Considerations</span></span>

> [!WARNING]
> <span data-ttu-id="08260-340">`UseDirectoryBrowser`i `UseStaticFiles` mogą wyciekać klucze tajne.</span><span class="sxs-lookup"><span data-stu-id="08260-340">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="08260-341">Wyłączenie przeglądania katalogów w środowisku produkcyjnym jest zdecydowanie zalecane.</span><span class="sxs-lookup"><span data-stu-id="08260-341">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="08260-342">Uważnie Przejrzyj katalogi, które są włączone za pośrednictwem `UseStaticFiles` lub `UseDirectoryBrowser` .</span><span class="sxs-lookup"><span data-stu-id="08260-342">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="08260-343">Cały katalog i jego katalogi podrzędne stają się publicznie dostępne.</span><span class="sxs-lookup"><span data-stu-id="08260-343">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="08260-344">Pliki magazynu odpowiednie do obsłużenia publicznie w dedykowanym katalogu, takie jak \* \<content_root> /wwwroot\*.</span><span class="sxs-lookup"><span data-stu-id="08260-344">Store files suitable for serving to the public in a dedicated directory, such as *\<content_root>/wwwroot*.</span></span> <span data-ttu-id="08260-345">Oddziel te pliki od widoków MVC, Razor stron (tylko 2. x), plików konfiguracji itp.</span><span class="sxs-lookup"><span data-stu-id="08260-345">Separate these files from MVC views, Razor Pages (2.x only), configuration files, etc.</span></span>

* <span data-ttu-id="08260-346">Adresy URL dla zawartości uwidocznionej `UseDirectoryBrowser` i `UseStaticFiles` podlegają ograniczeniom dotyczącym wielkości liter i znaków w źródłowym systemie plików.</span><span class="sxs-lookup"><span data-stu-id="08260-346">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="08260-347">Na przykład w systemie Windows nie jest rozróżniana wielkość liter &mdash; macOS i Linux.</span><span class="sxs-lookup"><span data-stu-id="08260-347">For example, Windows is case insensitive&mdash;macOS and Linux aren't.</span></span>

* <span data-ttu-id="08260-348">ASP.NET Core aplikacje hostowane w usługach IIS używają [modułu ASP.NET Core](xref:host-and-deploy/aspnet-core-module) do przesyłania dalej wszystkich żądań do aplikacji, w tym żądań plików statycznych.</span><span class="sxs-lookup"><span data-stu-id="08260-348">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="08260-349">Procedura obsługi pliku statycznego usług IIS nie jest używana.</span><span class="sxs-lookup"><span data-stu-id="08260-349">The IIS static file handler isn't used.</span></span> <span data-ttu-id="08260-350">Nie ma możliwości obsługi żądań, zanim są one obsługiwane przez moduł.</span><span class="sxs-lookup"><span data-stu-id="08260-350">It has no chance to handle requests before they're handled by the module.</span></span>

* <span data-ttu-id="08260-351">Wykonaj następujące kroki w Menedżerze usług IIS, aby usunąć program obsługi plików statycznych usług IIS na poziomie serwera lub witryny sieci Web:</span><span class="sxs-lookup"><span data-stu-id="08260-351">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="08260-352">Przejdź do funkcji **moduły** .</span><span class="sxs-lookup"><span data-stu-id="08260-352">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="08260-353">Na liście wybierz pozycję **StaticFileModule** .</span><span class="sxs-lookup"><span data-stu-id="08260-353">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="08260-354">Kliknij przycisk **Usuń** na pasku bocznym **Akcje** .</span><span class="sxs-lookup"><span data-stu-id="08260-354">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="08260-355">Jeśli program obsługi plików statycznych usług IIS jest włączony **i** moduł ASP.NET Core jest niepoprawnie skonfigurowany, obsługiwane są pliki statyczne.</span><span class="sxs-lookup"><span data-stu-id="08260-355">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="08260-356">Dzieje się tak na przykład wtedy, gdy plik *web.config* nie zostanie wdrożony.</span><span class="sxs-lookup"><span data-stu-id="08260-356">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="08260-357">Umieść pliki kodu (w tym *. cs* i *. cshtml*) poza [katalogiem głównym sieci Web](xref:fundamentals/index#web-root)projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="08260-357">Place code files (including *.cs* and *.cshtml*) outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="08260-358">W związku z tym tworzone jest podział logiczny między zawartością po stronie klienta a kodem opartym na serwerze.</span><span class="sxs-lookup"><span data-stu-id="08260-358">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="08260-359">Zapobiega to wyciekowi kodu po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="08260-359">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="08260-360">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="08260-360">Additional resources</span></span>

* [<span data-ttu-id="08260-361">Oprogramowanie pośredniczące</span><span class="sxs-lookup"><span data-stu-id="08260-361">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="08260-362">Wprowadzenie do programu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="08260-362">Introduction to ASP.NET Core</span></span>](xref:index)

::: moniker-end
