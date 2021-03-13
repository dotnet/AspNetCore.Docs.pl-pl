---
title: Pliki statyczne w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak udostępniać i zabezpieczać pliki statyczne oraz skonfigurować zachowania oprogramowania pośredniczącego do obsługi plików statycznych w aplikacji internetowej ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 6/23/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/static-files
ms.openlocfilehash: fb5ef59a74a768a1c202b49872b8a10327c96ff8
ms.sourcegitcommit: 07e7ee573fe4e12be93249a385db745d714ff6ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103413460"
---
# <a name="static-files-in-aspnet-core"></a><span data-ttu-id="57748-103">Pliki statyczne w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="57748-103">Static files in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="57748-104">Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT) i [Kirka Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="57748-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="57748-105">Pliki statyczne, takie jak HTML, CSS, images i JavaScript, są zasobami, które domyślnie aplikacja ASP.NET Core bezpośrednio obsługuje klientów.</span><span class="sxs-lookup"><span data-stu-id="57748-105">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients by default.</span></span>

<span data-ttu-id="57748-106">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/static-files/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="57748-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="57748-107">Obsługuj pliki statyczne</span><span class="sxs-lookup"><span data-stu-id="57748-107">Serve static files</span></span>

<span data-ttu-id="57748-108">Pliki statyczne są przechowywane w katalogu [głównym sieci Web](xref:fundamentals/index#web-root) projektu.</span><span class="sxs-lookup"><span data-stu-id="57748-108">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="57748-109">Domyślnym katalogiem jest `{content root}/wwwroot` , ale można go zmienić za pomocą <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> metody.</span><span class="sxs-lookup"><span data-stu-id="57748-109">The default directory is `{content root}/wwwroot`, but it can be changed with the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> method.</span></span> <span data-ttu-id="57748-110">Aby uzyskać więcej informacji, zobacz [katalog główny zawartości](xref:fundamentals/index#content-root) i [katalog główny sieci Web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="57748-110">For more information, see [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="57748-111"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>Metoda ustawia katalog główny zawartości dla bieżącego katalogu:</span><span class="sxs-lookup"><span data-stu-id="57748-111">The <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> method sets the content root to the current directory:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Program2.cs?name=snippet_Program)]

<span data-ttu-id="57748-112">Poprzedni kod został utworzony przy użyciu szablonu aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="57748-112">The preceding code was created with the web app template.</span></span>

<span data-ttu-id="57748-113">Pliki statyczne są dostępne za pośrednictwem ścieżki względnej dla [katalogu głównego sieci Web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="57748-113">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="57748-114">Na przykład szablony projektu **aplikacji sieci Web** zawierają kilka folderów w `wwwroot` folderze:</span><span class="sxs-lookup"><span data-stu-id="57748-114">For example, the **Web Application** project templates contain several folders within the `wwwroot` folder:</span></span>

* `wwwroot`
  * `css`
  * `js`
  * `lib`

<span data-ttu-id="57748-115">Rozważ utworzenie folderu *wwwroot/images* i dodanie pliku *wwwroot/images/MyImage.jpg* .</span><span class="sxs-lookup"><span data-stu-id="57748-115">Consider creating the *wwwroot/images* folder and adding the *wwwroot/images/MyImage.jpg* file.</span></span> <span data-ttu-id="57748-116">Format identyfikatora URI dostępu do pliku w `images` folderze to `https://<hostname>/images/<image_file_name>` .</span><span class="sxs-lookup"><span data-stu-id="57748-116">The URI format to access a file in the `images` folder is `https://<hostname>/images/<image_file_name>`.</span></span> <span data-ttu-id="57748-117">Na przykład `https://localhost:5001/images/MyImage.jpg`</span><span class="sxs-lookup"><span data-stu-id="57748-117">For example, `https://localhost:5001/images/MyImage.jpg`</span></span>

### <a name="serve-files-in-web-root"></a><span data-ttu-id="57748-118">Obsługiwanie plików w katalogu głównym sieci Web</span><span class="sxs-lookup"><span data-stu-id="57748-118">Serve files in web root</span></span>

<span data-ttu-id="57748-119">Domyślne szablony aplikacji sieci Web wywołują <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> metodę w `Startup.Configure` , co umożliwia obsługę plików statycznych:</span><span class="sxs-lookup"><span data-stu-id="57748-119">The default web app templates call the <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> method in `Startup.Configure`, which enables static files to be served:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="57748-120">`UseStaticFiles`Przeciążenie metody bez parametrów oznacza pliki w [katalogu głównym sieci Web](xref:fundamentals/index#web-root) jako do zablokowania.</span><span class="sxs-lookup"><span data-stu-id="57748-120">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="57748-121">Następujące znaczniki odwołują się do *wwwroot/images/MyImage.jpg*:</span><span class="sxs-lookup"><span data-stu-id="57748-121">The following markup references *wwwroot/images/MyImage.jpg*:</span></span>

```html
<img src="~/images/MyImage.jpg" class="img" alt="My image" />
```

<span data-ttu-id="57748-122">W poprzednim kodzie znak tyldy `~/` wskazuje [element główny sieci Web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="57748-122">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="57748-123">Obsługiwanie plików poza katalogiem głównym sieci Web</span><span class="sxs-lookup"><span data-stu-id="57748-123">Serve files outside of web root</span></span>

<span data-ttu-id="57748-124">Rozważ hierarchię katalogów, w której pliki statyczne mają być obsługiwane poza [katalogiem głównym sieci Web](xref:fundamentals/index#web-root):</span><span class="sxs-lookup"><span data-stu-id="57748-124">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `red-rose.jpg`

<span data-ttu-id="57748-125">Żądanie może uzyskać dostęp do `red-rose.jpg` pliku przez skonfigurowanie pliku statycznego oprogramowania pośredniczącego w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="57748-125">A request can access the `red-rose.jpg` file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupRose.cs?name=snippet_Configure&highlight=15-22)]

<span data-ttu-id="57748-126">W powyższym kodzie hierarchia katalogów *MyStaticFiles* jest udostępniana publicznie za pośrednictwem segmentu identyfikatora URI *StaticFiles* .</span><span class="sxs-lookup"><span data-stu-id="57748-126">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="57748-127">Żądanie do `https://<hostname>/StaticFiles/images/red-rose.jpg` obsłużynia pliku *red-rose.jpg* .</span><span class="sxs-lookup"><span data-stu-id="57748-127">A request to `https://<hostname>/StaticFiles/images/red-rose.jpg` serves the *red-rose.jpg* file.</span></span>

<span data-ttu-id="57748-128">Następujące znaczniki odwołują się do *MyStaticFiles/images/red-rose.jpg*:</span><span class="sxs-lookup"><span data-stu-id="57748-128">The following markup references *MyStaticFiles/images/red-rose.jpg*:</span></span>

```html
<img src="~/StaticFiles/images/red-rose.jpg" class="img" alt="A red rose" />
```

### <a name="set-http-response-headers"></a><span data-ttu-id="57748-129">Ustawianie nagłówków odpowiedzi HTTP</span><span class="sxs-lookup"><span data-stu-id="57748-129">Set HTTP response headers</span></span>

<span data-ttu-id="57748-130"><xref:Microsoft.AspNetCore.Builder.StaticFileOptions>Obiekt może służyć do ustawiania nagłówków odpowiedzi HTTP.</span><span class="sxs-lookup"><span data-stu-id="57748-130">A <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> object can be used to set HTTP response headers.</span></span> <span data-ttu-id="57748-131">Oprócz konfigurowania pliku statycznego z poziomu [katalogu głównego sieci Web](xref:fundamentals/index#web-root), poniższy kod ustawia `Cache-Control` Nagłówek:</span><span class="sxs-lookup"><span data-stu-id="57748-131">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_Configure&highlight=15-24)]

<!-- Q: The preceding code sets max-age to 604800 seconds (7 days), so what does the following mean? -->

<span data-ttu-id="57748-132">Pliki statyczne są publicznie buforowane przez 600 sekund:</span><span class="sxs-lookup"><span data-stu-id="57748-132">Static files are publicly cacheable for 600 seconds:</span></span>

![Dodano nagłówki odpowiedzi pokazujące nagłówek Cache-Control](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="57748-134">Autoryzacja pliku statycznego</span><span class="sxs-lookup"><span data-stu-id="57748-134">Static file authorization</span></span>

<span data-ttu-id="57748-135">ASP.NET Core szablonów wywołań <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> przed wywołaniem <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> .</span><span class="sxs-lookup"><span data-stu-id="57748-135">The ASP.NET Core templates call <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> before calling <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A>.</span></span> <span data-ttu-id="57748-136">Większość aplikacji korzysta z tego wzorca.</span><span class="sxs-lookup"><span data-stu-id="57748-136">Most apps follow this pattern.</span></span> <span data-ttu-id="57748-137">Gdy oprogramowanie pośredniczące pliku statycznego jest wywoływane przed wyzwoleniem na oprogramowanie pośredniczące:</span><span class="sxs-lookup"><span data-stu-id="57748-137">When the Static File Middleware is called before the authorization middleware:</span></span>

  * <span data-ttu-id="57748-138">Na plikach statycznych nie są wykonywane żadne testy autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="57748-138">No authorization checks are performed on the static files.</span></span>
  * <span data-ttu-id="57748-139">Pliki statyczne obsługiwane przez oprogramowanie pośredniczące plików statycznych, takie jak te w programie `wwwroot` , są publicznie dostępne.</span><span class="sxs-lookup"><span data-stu-id="57748-139">Static files served by the Static File Middleware, such as those those under `wwwroot`, are publicly accessible.</span></span>
  
<span data-ttu-id="57748-140">Aby zapewnić możliwość korzystania z plików statycznych na podstawie autoryzacji:</span><span class="sxs-lookup"><span data-stu-id="57748-140">To serve static files based on authorization:</span></span>

  * <span data-ttu-id="57748-141">Przechowuj je poza programem `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="57748-141">Store them outside of `wwwroot`.</span></span>
  * <span data-ttu-id="57748-142">Wywoływanie `UseStaticFiles` , Określanie ścieżki po wywołaniu `UseAuthorization` .</span><span class="sxs-lookup"><span data-stu-id="57748-142">Call `UseStaticFiles`, specifying a path, after calling `UseAuthorization`.</span></span>
  * <span data-ttu-id="57748-143">Ustaw [zasady autoryzacji rezerwowej](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span><span class="sxs-lookup"><span data-stu-id="57748-143">Set the [fallback authorization policy](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span></span>

  [!code-csharp[](static-files/samples/3.x/StaticFileAuth/Startup.cs?name=snippet2&highlight=24-29)]
  
  [!code-csharp[](static-files/samples/3.x/StaticFileAuth/Startup.cs?name=snippet1&highlight=20-25)]

  <span data-ttu-id="57748-144">W powyższym kodzie rezerwowe zasady autoryzacji wymagają uwierzytelnienia ***wszystkich*** użytkowników.</span><span class="sxs-lookup"><span data-stu-id="57748-144">In the preceding code, the fallback authorization policy requires ***all*** users to be authenticated.</span></span> <span data-ttu-id="57748-145">Punkty końcowe, takie jak kontrolery, Razor strony itp. określają własne wymagania dotyczące autoryzacji, nie używają rezerwowych zasad autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="57748-145">Endpoints such as controllers, Razor Pages, etc that specify their own authorization requirements don't use the fallback authorization policy.</span></span> <span data-ttu-id="57748-146">Na przykład Razor strony, kontrolery lub metody akcji z `[AllowAnonymous]` lub `[Authorize(PolicyName="MyPolicy")]` używają stosowanego atrybutu autoryzacji, a nie rezerwowych zasad autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="57748-146">For example, Razor Pages, controllers, or action methods with `[AllowAnonymous]` or `[Authorize(PolicyName="MyPolicy")]` use the applied authorization attribute rather than the fallback authorization policy.</span></span>

  <span data-ttu-id="57748-147"><xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> dodaje <xref:Microsoft.AspNetCore.Authorization.Infrastructure.DenyAnonymousAuthorizationRequirement> do bieżącego wystąpienia, które wymusza, że bieżący użytkownik jest uwierzytelniony.</span><span class="sxs-lookup"><span data-stu-id="57748-147"><xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> adds <xref:Microsoft.AspNetCore.Authorization.Infrastructure.DenyAnonymousAuthorizationRequirement> to the current instance, which enforces that the current user is authenticated.</span></span>

  <span data-ttu-id="57748-148">Zasoby statyczne w obszarze `wwwroot` są publicznie dostępne, ponieważ domyślne oprogramowanie pośredniczące ( `app.UseStaticFiles();` ) jest wywoływane przed `UseAuthentication` .</span><span class="sxs-lookup"><span data-stu-id="57748-148">Static assets under `wwwroot` are publicly accessible because the default Static File Middleware (`app.UseStaticFiles();`) is called before `UseAuthentication`.</span></span> <span data-ttu-id="57748-149">Statyczne zasoby w folderze *MyStaticFiles* wymagają uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="57748-149">Static assets in the *MyStaticFiles* folder require authentication.</span></span> <span data-ttu-id="57748-150">[Przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/static-files/samples) demonstruje ten sposób.</span><span class="sxs-lookup"><span data-stu-id="57748-150">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/static-files/samples) demonstrates this.</span></span>

<span data-ttu-id="57748-151">Alternatywnym podejściem do obsługiwania plików na podstawie autoryzacji jest:</span><span class="sxs-lookup"><span data-stu-id="57748-151">An alternative approach to serve files based on authorization is to:</span></span>

  * <span data-ttu-id="57748-152">Przechowuj je poza programem `wwwroot` i dowolnym katalogiem dostępnym dla oprogramowania pośredniczącego plików statycznych.</span><span class="sxs-lookup"><span data-stu-id="57748-152">Store them outside of `wwwroot` and any directory accessible to the Static File Middleware.</span></span>
  * <span data-ttu-id="57748-153">Można je obsłużyć za pomocą metody akcji, do której zastosowano autoryzację i zwrócić <xref:Microsoft.AspNetCore.Mvc.FileResult> obiekt:</span><span class="sxs-lookup"><span data-stu-id="57748-153">Serve them via an action method to which authorization is applied and return a <xref:Microsoft.AspNetCore.Mvc.FileResult> object:</span></span>

  [!code-csharp[](static-files/samples/3.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImage)]

## <a name="directory-browsing"></a><span data-ttu-id="57748-154">Przeglądanie katalogów</span><span class="sxs-lookup"><span data-stu-id="57748-154">Directory browsing</span></span>

<span data-ttu-id="57748-155">Przeglądanie katalogów umożliwia wyświetlanie listy katalogów w określonych katalogach.</span><span class="sxs-lookup"><span data-stu-id="57748-155">Directory browsing allows directory listing within specified directories.</span></span>

<span data-ttu-id="57748-156">Przeglądanie katalogów jest domyślnie wyłączone ze względów bezpieczeństwa.</span><span class="sxs-lookup"><span data-stu-id="57748-156">Directory browsing is disabled by default for security reasons.</span></span> <span data-ttu-id="57748-157">Aby uzyskać więcej informacji, zobacz [uwagi](#considerations).</span><span class="sxs-lookup"><span data-stu-id="57748-157">For more information, see [Considerations](#considerations).</span></span>

<span data-ttu-id="57748-158">Włącz przeglądanie katalogów za pomocą:</span><span class="sxs-lookup"><span data-stu-id="57748-158">Enable directory browsing with:</span></span>

* <span data-ttu-id="57748-159"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> w programie `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="57748-159"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="57748-160"><xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> w programie `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="57748-160"><xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> in `Startup.Configure`.</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ClassMembers&highlight=4,21-35)]

<span data-ttu-id="57748-161">Poprzedni kod umożliwia przeglądanie katalogów folderu *wwwroot/images* przy użyciu adresu URL `https://<hostname>/MyImages` z linkami do każdego pliku i folderu:</span><span class="sxs-lookup"><span data-stu-id="57748-161">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL `https://<hostname>/MyImages`, with links to each file and folder:</span></span>

![przeglądanie katalogów](static-files/_static/dir-browse.png)

## <a name="serve-default-documents"></a><span data-ttu-id="57748-163">Obsługuj dokumenty domyślne</span><span class="sxs-lookup"><span data-stu-id="57748-163">Serve default documents</span></span>

<span data-ttu-id="57748-164">Ustawienie strony domyślnej umożliwia odwiedzającym punkt startowy w witrynie.</span><span class="sxs-lookup"><span data-stu-id="57748-164">Setting a default page provides visitors a starting point on a site.</span></span> <span data-ttu-id="57748-165">Aby udostępnić stronę domyślną z `wwwroot` bez w pełni kwalifikowanego identyfikatora URI, wywołaj <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> metodę:</span><span class="sxs-lookup"><span data-stu-id="57748-165">To serve a default page from `wwwroot` without a fully qualified URI, call the <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> method:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="57748-166">`UseDefaultFiles` musi być wywoływana przed zapisaniem `UseStaticFiles` pliku domyślnego.</span><span class="sxs-lookup"><span data-stu-id="57748-166">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="57748-167">`UseDefaultFiles` to adres URL służący do ponownego zapisu, który nie obsługuje tego pliku.</span><span class="sxs-lookup"><span data-stu-id="57748-167">`UseDefaultFiles` is a URL rewriter that doesn't serve the file.</span></span>

<span data-ttu-id="57748-168">W programie `UseDefaultFiles` żądania do folderu w usłudze `wwwroot` Search dla:</span><span class="sxs-lookup"><span data-stu-id="57748-168">With `UseDefaultFiles`, requests to a folder in `wwwroot` search for:</span></span>

* <span data-ttu-id="57748-169">*default.htm*</span><span class="sxs-lookup"><span data-stu-id="57748-169">*default.htm*</span></span>
* <span data-ttu-id="57748-170">*default.html*</span><span class="sxs-lookup"><span data-stu-id="57748-170">*default.html*</span></span>
* <span data-ttu-id="57748-171">*index.htm*</span><span class="sxs-lookup"><span data-stu-id="57748-171">*index.htm*</span></span>
* <span data-ttu-id="57748-172">*index.html*</span><span class="sxs-lookup"><span data-stu-id="57748-172">*index.html*</span></span>

<span data-ttu-id="57748-173">Pierwszy plik znaleziony z listy jest obsługiwany tak, jakby żądanie było w pełni kwalifikowanym identyfikatorem URI.</span><span class="sxs-lookup"><span data-stu-id="57748-173">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="57748-174">Adres URL przeglądarki nadal odzwierciedla żądany identyfikator URI.</span><span class="sxs-lookup"><span data-stu-id="57748-174">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="57748-175">Następujący kod zmienia domyślną nazwę pliku na *mydefault.html*:</span><span class="sxs-lookup"><span data-stu-id="57748-175">The following code changes the default file name to *mydefault.html*:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_DefaultFiles)]

<span data-ttu-id="57748-176">Poniższy kod ilustruje `Startup.Configure` poprzedni kod:</span><span class="sxs-lookup"><span data-stu-id="57748-176">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupDefault.cs?name=snippet_Configure&highlight=15-19)]

### <a name="usefileserver-for-default-documents"></a><span data-ttu-id="57748-177">UseFileServer dla dokumentów domyślnych</span><span class="sxs-lookup"><span data-stu-id="57748-177">UseFileServer for default documents</span></span>

<span data-ttu-id="57748-178"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> łączy funkcje `UseStaticFiles` , `UseDefaultFiles` i opcjonalnie `UseDirectoryBrowser` .</span><span class="sxs-lookup"><span data-stu-id="57748-178"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="57748-179">Wywołaj, `app.UseFileServer` Aby włączyć obsługę plików statycznych i pliku domyślnego.</span><span class="sxs-lookup"><span data-stu-id="57748-179">Call `app.UseFileServer` to enable the serving of static files and the default file.</span></span> <span data-ttu-id="57748-180">Przeglądanie katalogów nie jest włączone.</span><span class="sxs-lookup"><span data-stu-id="57748-180">Directory browsing isn't enabled.</span></span> <span data-ttu-id="57748-181">Poniższy kod ilustruje `Startup.Configure` `UseFileServer` :</span><span class="sxs-lookup"><span data-stu-id="57748-181">The following code shows `Startup.Configure` with `UseFileServer`:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty2.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="57748-182">Poniższy kod umożliwia obsługę plików statycznych, plik domyślny i przeglądanie katalogów:</span><span class="sxs-lookup"><span data-stu-id="57748-182">The following code enables the serving of static files, the default file, and directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="57748-183">Poniższy kod ilustruje `Startup.Configure` poprzedni kod:</span><span class="sxs-lookup"><span data-stu-id="57748-183">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupEmpty3.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="57748-184">Weź pod uwagę następującą hierarchię katalogów:</span><span class="sxs-lookup"><span data-stu-id="57748-184">Consider the following directory hierarchy:</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `MyImage.jpg`
  * `default.html`

<span data-ttu-id="57748-185">Poniższy kod umożliwia obsługę plików statycznych, plików domyślnych i przeglądanie katalogów `MyStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="57748-185">The following code enables the serving of static files, the default file, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileServer.cs?name=snippet_ClassMembers&highlight=4,21-31)]

<span data-ttu-id="57748-186"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> musi być wywoływana, gdy `EnableDirectoryBrowsing` wartość właściwości to `true` .</span><span class="sxs-lookup"><span data-stu-id="57748-186"><xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> must be called when the `EnableDirectoryBrowsing` property value is `true`.</span></span>

<span data-ttu-id="57748-187">Przy użyciu hierarchii plików i poprzedniego kodu adresy URL są rozpoznawane w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="57748-187">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="57748-188">URI</span><span class="sxs-lookup"><span data-stu-id="57748-188">URI</span></span>            |      <span data-ttu-id="57748-189">Reakcja</span><span class="sxs-lookup"><span data-stu-id="57748-189">Response</span></span>  |
| ------- | ------|
| `https://<hostname>/StaticFiles/images/MyImage.jpg` | <span data-ttu-id="57748-190">*MyStaticFiles/obrazy/MyImage.jpg*</span><span class="sxs-lookup"><span data-stu-id="57748-190">*MyStaticFiles/images/MyImage.jpg*</span></span> |
| `https://<hostname>/StaticFiles` | <span data-ttu-id="57748-191">*MyStaticFiles/default.html*</span><span class="sxs-lookup"><span data-stu-id="57748-191">*MyStaticFiles/default.html*</span></span> |

<span data-ttu-id="57748-192">Jeśli plik o nazwie Default nie istnieje w katalogu *MyStaticFiles* , `https://<hostname>/StaticFiles` zwraca listę katalogów z linkami, które można kliknąć:</span><span class="sxs-lookup"><span data-stu-id="57748-192">If no default-named file exists in the *MyStaticFiles* directory, `https://<hostname>/StaticFiles` returns the directory listing with clickable links:</span></span>

![Lista plików statycznych](static-files/_static/db2.png)

<span data-ttu-id="57748-194"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> i <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> wykonać przekierowanie po stronie klienta z docelowego identyfikatora URI bez końcowej `/`  do docelowego identyfikatora URI kończącego się na końcu `/` .</span><span class="sxs-lookup"><span data-stu-id="57748-194"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from the target URI without a trailing `/`  to the target URI with a trailing `/`.</span></span> <span data-ttu-id="57748-195">Na przykład z `https://<hostname>/StaticFiles` do `https://<hostname>/StaticFiles/` .</span><span class="sxs-lookup"><span data-stu-id="57748-195">For example, from `https://<hostname>/StaticFiles` to `https://<hostname>/StaticFiles/`.</span></span> <span data-ttu-id="57748-196">Względne adresy URL w katalogu *StaticFiles* są nieprawidłowe bez końcowego ukośnika ( `/` ).</span><span class="sxs-lookup"><span data-stu-id="57748-196">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash (`/`).</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="57748-197">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="57748-197">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="57748-198"><xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider>Klasa zawiera `Mappings` Właściwość, która służy jako mapowanie rozszerzeń plików na typy zawartości MIME.</span><span class="sxs-lookup"><span data-stu-id="57748-198">The <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> class contains a `Mappings` property that serves as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="57748-199">W poniższym przykładzie kilka rozszerzeń plików są mapowane na znane typy MIME.</span><span class="sxs-lookup"><span data-stu-id="57748-199">In the following sample, several file extensions are mapped to known MIME types.</span></span> <span data-ttu-id="57748-200">Rozszerzenie *. rtf* jest zastępowane, a plik *MP4* zostaje usunięty:</span><span class="sxs-lookup"><span data-stu-id="57748-200">The *.rtf* extension is replaced, and *.mp4* is removed:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Provider)]

<span data-ttu-id="57748-201">Poniższy kod ilustruje `Startup.Configure` poprzedni kod:</span><span class="sxs-lookup"><span data-stu-id="57748-201">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_Configure&highlight=15-43)]

<span data-ttu-id="57748-202">Zobacz [typy zawartości MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).</span><span class="sxs-lookup"><span data-stu-id="57748-202">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="57748-203">Niestandardowe typy zawartości</span><span class="sxs-lookup"><span data-stu-id="57748-203">Non-standard content types</span></span>

<span data-ttu-id="57748-204">Oprogramowanie pośredniczące plików statycznych rozpoznaje niemal 400 znanych typów zawartości plików.</span><span class="sxs-lookup"><span data-stu-id="57748-204">The Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="57748-205">Jeśli użytkownik zażąda pliku z nieznanym typem pliku, oprogramowanie pośredniczące plików przesyła żądanie do następnego oprogramowania pośredniczącego w potoku.</span><span class="sxs-lookup"><span data-stu-id="57748-205">If the user requests a file with an unknown file type, the Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="57748-206">Jeśli żadne oprogramowanie pośredniczące nie obsługuje żądania, zwracana jest odpowiedź *404* .</span><span class="sxs-lookup"><span data-stu-id="57748-206">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="57748-207">Jeśli przeglądanie katalogów jest włączone, link do pliku zostanie wyświetlony na liście katalogów.</span><span class="sxs-lookup"><span data-stu-id="57748-207">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="57748-208">Poniższy kod umożliwia obsługę nieznanych typów i renderuje nieznany plik jako obraz:</span><span class="sxs-lookup"><span data-stu-id="57748-208">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_UseStaticFiles)]

<span data-ttu-id="57748-209">Poniższy kod ilustruje `Startup.Configure` poprzedni kod:</span><span class="sxs-lookup"><span data-stu-id="57748-209">The following code shows `Startup.Configure` with the preceding code:</span></span>

[!code-csharp[](~/fundamentals/static-files/samples/3.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_Configure&highlight=15-19)]

<span data-ttu-id="57748-210">W powyższym kodzie żądanie dotyczące pliku z nieznanym typem zawartości jest zwracane jako obraz.</span><span class="sxs-lookup"><span data-stu-id="57748-210">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="57748-211">Włączenie <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> stanowi zagrożenie bezpieczeństwa.</span><span class="sxs-lookup"><span data-stu-id="57748-211">Enabling <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> is a security risk.</span></span> <span data-ttu-id="57748-212">Jest on domyślnie wyłączony i nie jest zalecane jego użycie.</span><span class="sxs-lookup"><span data-stu-id="57748-212">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="57748-213">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) zapewnia bezpieczniejsze rozwiązanie do obsługi plików z rozszerzeniami niestandardowymi.</span><span class="sxs-lookup"><span data-stu-id="57748-213">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="57748-214">Obsługiwanie plików z wielu lokalizacji</span><span class="sxs-lookup"><span data-stu-id="57748-214">Serve files from multiple locations</span></span>

<span data-ttu-id="57748-215">`UseStaticFiles` i `UseFileServer` domyślny dla dostawcy plików wskazywanego przez `wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="57748-215">`UseStaticFiles` and `UseFileServer` default to the file provider pointing at `wwwroot`.</span></span> <span data-ttu-id="57748-216">Dodatkowe wystąpienia `UseStaticFiles` i `UseFileServer` mogą być dostarczone z innymi dostawcami plików w celu obsłużenia plików z innych lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="57748-216">Additional instances of `UseStaticFiles` and `UseFileServer` can be provided with other file providers to serve files from other locations.</span></span> <span data-ttu-id="57748-217">Aby uzyskać więcej informacji, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/15578)w serwisie GitHub.</span><span class="sxs-lookup"><span data-stu-id="57748-217">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

<a name="sc"></a>

### <a name="security-considerations-for-static-files"></a><span data-ttu-id="57748-218">Zagadnienia dotyczące zabezpieczeń plików statycznych</span><span class="sxs-lookup"><span data-stu-id="57748-218">Security considerations for static files</span></span>

> [!WARNING]
> <span data-ttu-id="57748-219">`UseDirectoryBrowser` i `UseStaticFiles` mogą wyciekać klucze tajne.</span><span class="sxs-lookup"><span data-stu-id="57748-219">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="57748-220">Wyłączenie przeglądania katalogów w środowisku produkcyjnym jest zdecydowanie zalecane.</span><span class="sxs-lookup"><span data-stu-id="57748-220">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="57748-221">Uważnie Przejrzyj katalogi, które są włączone za pośrednictwem `UseStaticFiles` lub `UseDirectoryBrowser` .</span><span class="sxs-lookup"><span data-stu-id="57748-221">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="57748-222">Cały katalog i jego katalogi podrzędne stają się publicznie dostępne.</span><span class="sxs-lookup"><span data-stu-id="57748-222">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="57748-223">Pliki magazynu odpowiednie do obsłużenia publicznie w dedykowanym katalogu, na przykład `<content_root>/wwwroot` .</span><span class="sxs-lookup"><span data-stu-id="57748-223">Store files suitable for serving to the public in a dedicated directory, such as `<content_root>/wwwroot`.</span></span> <span data-ttu-id="57748-224">Oddziel te pliki od widoków MVC, Razor stron, plików konfiguracji itp.</span><span class="sxs-lookup"><span data-stu-id="57748-224">Separate these files from MVC views, Razor Pages, configuration files, etc.</span></span>

* <span data-ttu-id="57748-225">Adresy URL dla zawartości uwidocznionej `UseDirectoryBrowser` i `UseStaticFiles` podlegają ograniczeniom dotyczącym wielkości liter i znaków w źródłowym systemie plików.</span><span class="sxs-lookup"><span data-stu-id="57748-225">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="57748-226">Na przykład w systemie Windows nie jest rozróżniana wielkość liter, ale macOS i Linux nie są.</span><span class="sxs-lookup"><span data-stu-id="57748-226">For example, Windows is case insensitive, but macOS and Linux aren't.</span></span>

* <span data-ttu-id="57748-227">ASP.NET Core aplikacje hostowane w usługach IIS używają [modułu ASP.NET Core](xref:host-and-deploy/aspnet-core-module) do przesyłania dalej wszystkich żądań do aplikacji, w tym żądań plików statycznych.</span><span class="sxs-lookup"><span data-stu-id="57748-227">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="57748-228">Procedura obsługi pliku statycznego usług IIS nie jest używana i nie ma możliwości obsługi żądań.</span><span class="sxs-lookup"><span data-stu-id="57748-228">The IIS static file handler isn't used and has no chance to handle requests.</span></span>

* <span data-ttu-id="57748-229">Wykonaj następujące kroki w Menedżerze usług IIS, aby usunąć program obsługi plików statycznych usług IIS na poziomie serwera lub witryny sieci Web:</span><span class="sxs-lookup"><span data-stu-id="57748-229">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="57748-230">Przejdź do funkcji **moduły** .</span><span class="sxs-lookup"><span data-stu-id="57748-230">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="57748-231">Na liście wybierz pozycję **StaticFileModule** .</span><span class="sxs-lookup"><span data-stu-id="57748-231">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="57748-232">Kliknij przycisk **Usuń** na pasku bocznym **Akcje** .</span><span class="sxs-lookup"><span data-stu-id="57748-232">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="57748-233">Jeśli program obsługi plików statycznych usług IIS jest włączony **i** moduł ASP.NET Core jest niepoprawnie skonfigurowany, obsługiwane są pliki statyczne.</span><span class="sxs-lookup"><span data-stu-id="57748-233">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="57748-234">Dzieje się tak na przykład wtedy, gdy plik *web.config* nie zostanie wdrożony.</span><span class="sxs-lookup"><span data-stu-id="57748-234">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="57748-235">Umieść pliki kodu, w tym *. cs* i *. cshtml*, poza [katalogiem głównym sieci Web](xref:fundamentals/index#web-root)projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="57748-235">Place code files, including *.cs* and *.cshtml*, outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="57748-236">W związku z tym tworzone jest podział logiczny między zawartością po stronie klienta a kodem opartym na serwerze.</span><span class="sxs-lookup"><span data-stu-id="57748-236">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="57748-237">Zapobiega to wyciekowi kodu po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="57748-237">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="57748-238">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="57748-238">Additional resources</span></span>

* [<span data-ttu-id="57748-239">Oprogramowanie pośredniczące</span><span class="sxs-lookup"><span data-stu-id="57748-239">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="57748-240">Wprowadzenie do programu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="57748-240">Introduction to ASP.NET Core</span></span>](xref:index)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="57748-241">Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT) i [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="57748-241">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="57748-242">Pliki statyczne, takie jak HTML, CSS, images i JavaScript, są zasobami, które aplikacja ASP.NET Core może bezpośrednio obsługiwać klientów.</span><span class="sxs-lookup"><span data-stu-id="57748-242">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients.</span></span> <span data-ttu-id="57748-243">Aby umożliwić obsługę tych plików, wymagana jest pewna konfiguracja.</span><span class="sxs-lookup"><span data-stu-id="57748-243">Some configuration is required to enable serving of these files.</span></span>

<span data-ttu-id="57748-244">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/static-files/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="57748-244">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="57748-245">Obsługuj pliki statyczne</span><span class="sxs-lookup"><span data-stu-id="57748-245">Serve static files</span></span>

<span data-ttu-id="57748-246">Pliki statyczne są przechowywane w katalogu [głównym sieci Web](xref:fundamentals/index#web-root) projektu.</span><span class="sxs-lookup"><span data-stu-id="57748-246">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="57748-247">Domyślnym katalogiem jest *{Content root}/wwwroot*, ale można go zmienić za pomocą <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> metody.</span><span class="sxs-lookup"><span data-stu-id="57748-247">The default directory is *{content root}/wwwroot*, but it can be changed via the <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseWebRoot%2A> method.</span></span> <span data-ttu-id="57748-248">Aby uzyskać więcej informacji, zobacz [katalog główny zawartości](xref:fundamentals/index#content-root) i [katalog główny sieci Web](xref:fundamentals/index#web-root) .</span><span class="sxs-lookup"><span data-stu-id="57748-248">See [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root) for more information.</span></span>

<span data-ttu-id="57748-249">Host sieci Web aplikacji musi być świadomy katalogu głównego zawartości.</span><span class="sxs-lookup"><span data-stu-id="57748-249">The app's web host must be made aware of the content root directory.</span></span>

<span data-ttu-id="57748-250">`WebHost.CreateDefaultBuilder`Metoda ustawia katalog główny zawartości dla bieżącego katalogu:</span><span class="sxs-lookup"><span data-stu-id="57748-250">The `WebHost.CreateDefaultBuilder` method sets the content root to the current directory:</span></span>

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

<span data-ttu-id="57748-251">Pliki statyczne są dostępne za pośrednictwem ścieżki względnej dla [katalogu głównego sieci Web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="57748-251">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="57748-252">Na przykład szablon projektu **aplikacji sieci Web** zawiera kilka folderów w `wwwroot` folderze:</span><span class="sxs-lookup"><span data-stu-id="57748-252">For example, the **Web Application** project template contains several folders within the `wwwroot` folder:</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`

<span data-ttu-id="57748-253">Format identyfikatora URI służący do uzyskiwania dostępu do pliku w podfolderze *images* to *http:// \<server_address> \<image_file_name> /images/*.</span><span class="sxs-lookup"><span data-stu-id="57748-253">The URI format to access a file in the *images* subfolder is *http://\<server_address>/images/\<image_file_name>*.</span></span> <span data-ttu-id="57748-254">Przykładowy adres URL to *http://localhost:9189/images/banner3.svg*.</span><span class="sxs-lookup"><span data-stu-id="57748-254">For example, *http://localhost:9189/images/banner3.svg*.</span></span>

<span data-ttu-id="57748-255">Jeśli .NET Framework określania wartości docelowej, Dodaj pakiet [Microsoft. AspNetCore. StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) do projektu.</span><span class="sxs-lookup"><span data-stu-id="57748-255">If targeting .NET Framework, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to the project.</span></span> <span data-ttu-id="57748-256">W przypadku określania platformy .NET Core [pakiet Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) zawiera ten pakiet.</span><span class="sxs-lookup"><span data-stu-id="57748-256">If targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) includes this package.</span></span>

<span data-ttu-id="57748-257">Skonfiguruj [oprogramowanie pośredniczące](xref:fundamentals/middleware/index), które umożliwia obsługę plików statycznych.</span><span class="sxs-lookup"><span data-stu-id="57748-257">Configure the [middleware](xref:fundamentals/middleware/index), which enables the serving of static files.</span></span>

### <a name="serve-files-inside-of-web-root"></a><span data-ttu-id="57748-258">Obsługuj pliki wewnątrz katalogu głównego sieci Web</span><span class="sxs-lookup"><span data-stu-id="57748-258">Serve files inside of web root</span></span>

<span data-ttu-id="57748-259">Wywołaj <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> metodę w `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="57748-259">Invoke the <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> method within `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

<span data-ttu-id="57748-260">`UseStaticFiles`Przeciążenie metody bez parametrów oznacza pliki w [katalogu głównym sieci Web](xref:fundamentals/index#web-root) jako do zablokowania.</span><span class="sxs-lookup"><span data-stu-id="57748-260">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="57748-261">Następujące znaczniki odwołują się do *wwwroot/images/banner1. SVG*:</span><span class="sxs-lookup"><span data-stu-id="57748-261">The following markup references *wwwroot/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

<span data-ttu-id="57748-262">W poprzednim kodzie znak tyldy `~/` wskazuje [element główny sieci Web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="57748-262">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="57748-263">Obsługiwanie plików poza katalogiem głównym sieci Web</span><span class="sxs-lookup"><span data-stu-id="57748-263">Serve files outside of web root</span></span>

<span data-ttu-id="57748-264">Rozważ hierarchię katalogów, w której pliki statyczne mają być obsługiwane poza [katalogiem głównym sieci Web](xref:fundamentals/index#web-root):</span><span class="sxs-lookup"><span data-stu-id="57748-264">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* `wwwroot`
  * `css`
  * `images`
  * `js`
* `MyStaticFiles`
  * `images`
    * `banner1.svg`

<span data-ttu-id="57748-265">Żądanie może uzyskać dostęp do pliku *banner1. SVG* przez skonfigurowanie pliku statycznego pośredniczącego w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="57748-265">A request can access the *banner1.svg* file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

<span data-ttu-id="57748-266">W powyższym kodzie hierarchia katalogów *MyStaticFiles* jest udostępniana publicznie za pośrednictwem segmentu identyfikatora URI *StaticFiles* .</span><span class="sxs-lookup"><span data-stu-id="57748-266">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="57748-267">Żądanie *http:// \<server_address> /StaticFiles/images/banner1.SVG* obsługuje plik *banner1. SVG* .</span><span class="sxs-lookup"><span data-stu-id="57748-267">A request to *http://\<server_address>/StaticFiles/images/banner1.svg* serves the *banner1.svg* file.</span></span>

<span data-ttu-id="57748-268">Następujące znaczniki odwołują się do *MyStaticFiles/images/banner1. SVG*:</span><span class="sxs-lookup"><span data-stu-id="57748-268">The following markup references *MyStaticFiles/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1.x/StaticFilesSample/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a><span data-ttu-id="57748-269">Ustawianie nagłówków odpowiedzi HTTP</span><span class="sxs-lookup"><span data-stu-id="57748-269">Set HTTP response headers</span></span>

<span data-ttu-id="57748-270"><xref:Microsoft.AspNetCore.Builder.StaticFileOptions>Obiekt może służyć do ustawiania nagłówków odpowiedzi HTTP.</span><span class="sxs-lookup"><span data-stu-id="57748-270">A <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> object can be used to set HTTP response headers.</span></span> <span data-ttu-id="57748-271">Oprócz konfigurowania pliku statycznego z poziomu [katalogu głównego sieci Web](xref:fundamentals/index#web-root), poniższy kod ustawia `Cache-Control` Nagłówek:</span><span class="sxs-lookup"><span data-stu-id="57748-271">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupAddHeader.cs?name=snippet_ConfigureMethod)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="57748-272"><xref:Microsoft.AspNetCore.Http.HeaderDictionaryExtensions.Append%2A?displayProperty=nameWithType>Metoda istnieje w pakiecie [Microsoft. AspNetCore. http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) .</span><span class="sxs-lookup"><span data-stu-id="57748-272">The <xref:Microsoft.AspNetCore.Http.HeaderDictionaryExtensions.Append%2A?displayProperty=nameWithType> method exists in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

<span data-ttu-id="57748-273">Pliki zostały publicznie przetworzone w pamięci podręcznej przez 10 minut (600 sekund) w środowisku deweloperskim:</span><span class="sxs-lookup"><span data-stu-id="57748-273">The files have been made publicly cacheable for 10 minutes (600 seconds) in the Development environment:</span></span>

![Dodano nagłówki odpowiedzi pokazujące nagłówek Cache-Control](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="57748-275">Autoryzacja pliku statycznego</span><span class="sxs-lookup"><span data-stu-id="57748-275">Static file authorization</span></span>

<span data-ttu-id="57748-276">Oprogramowanie pośredniczące plików statycznych nie zapewnia kontroli autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="57748-276">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="57748-277">Wszystkie pliki obsługiwane przez ten program, w tym w katalogu *wwwroot*, są publicznie dostępne.</span><span class="sxs-lookup"><span data-stu-id="57748-277">Any files served by it, including those under *wwwroot*, are publicly accessible.</span></span> <span data-ttu-id="57748-278">Aby obpracować pliki na podstawie autoryzacji:</span><span class="sxs-lookup"><span data-stu-id="57748-278">To serve files based on authorization:</span></span>

* <span data-ttu-id="57748-279">Przechowaj je poza katalogiem *wwwroot* i dowolnym katalogu dostępnym dla oprogramowania pośredniczącego plików statycznych.</span><span class="sxs-lookup"><span data-stu-id="57748-279">Store them outside of *wwwroot* and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="57748-280">Obsłużyć je za pomocą metody akcji, do której zastosowano autoryzację.</span><span class="sxs-lookup"><span data-stu-id="57748-280">Serve them via an action method to which authorization is applied.</span></span> <span data-ttu-id="57748-281">Zwróć <xref:Microsoft.AspNetCore.Mvc.FileResult> obiekt:</span><span class="sxs-lookup"><span data-stu-id="57748-281">Return a <xref:Microsoft.AspNetCore.Mvc.FileResult> object:</span></span>

  [!code-csharp[](static-files/samples/1.x/StaticFilesSample/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a><span data-ttu-id="57748-282">Włącz przeglądanie katalogów</span><span class="sxs-lookup"><span data-stu-id="57748-282">Enable directory browsing</span></span>

<span data-ttu-id="57748-283">Przeglądanie katalogów umożliwia użytkownikom aplikacji sieci Web Wyświetlanie listy katalogów i plików w określonym katalogu.</span><span class="sxs-lookup"><span data-stu-id="57748-283">Directory browsing allows users of your web app to see a directory listing and files within a specified directory.</span></span> <span data-ttu-id="57748-284">Przeglądanie katalogów jest domyślnie wyłączone ze względów bezpieczeństwa (zobacz [uwagi](#considerations)).</span><span class="sxs-lookup"><span data-stu-id="57748-284">Directory browsing is disabled by default for security reasons (see [Considerations](#considerations)).</span></span> <span data-ttu-id="57748-285">Włącz przeglądanie katalogów przez wywołanie <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> metody w `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="57748-285">Enable directory browsing by invoking the <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser%2A> method in `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

<span data-ttu-id="57748-286">Dodaj wymagane usługi, wywołując <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> metodę z `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="57748-286">Add required services by invoking the <xref:Microsoft.Extensions.DependencyInjection.DirectoryBrowserServiceExtensions.AddDirectoryBrowser%2A> method from `Startup.ConfigureServices`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

<span data-ttu-id="57748-287">Poprzedni kod umożliwia przeglądanie katalogów folderu *wwwroot/images* przy użyciu adresu URL *http:// \<server_address> /myimages*, z linkami do każdego pliku i folderu:</span><span class="sxs-lookup"><span data-stu-id="57748-287">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*, with links to each file and folder:</span></span>

![przeglądanie katalogów](static-files/_static/dir-browse.png)

<span data-ttu-id="57748-289">Zapoznaj się z [uwagami](#considerations) dotyczącymi zagrożeń bezpieczeństwa podczas włączania przeglądania.</span><span class="sxs-lookup"><span data-stu-id="57748-289">See [Considerations](#considerations) on the security risks when enabling browsing.</span></span>

<span data-ttu-id="57748-290">Zwróć uwagę na dwa `UseStaticFiles` wywołania w poniższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="57748-290">Note the two `UseStaticFiles` calls in the following example.</span></span> <span data-ttu-id="57748-291">Pierwsze wywołanie umożliwia obsługę plików statycznych w folderze *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="57748-291">The first call enables the serving of static files in the *wwwroot* folder.</span></span> <span data-ttu-id="57748-292">Drugie wywołanie umożliwia przeglądanie katalogów folderu *wwwroot/images* przy użyciu adresu URL *http:// \<server_address> /myimages*:</span><span class="sxs-lookup"><span data-stu-id="57748-292">The second call enables directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a><span data-ttu-id="57748-293">Obsługuj dokument domyślny</span><span class="sxs-lookup"><span data-stu-id="57748-293">Serve a default document</span></span>

<span data-ttu-id="57748-294">Ustawienie domyślnej strony głównej zapewnia odwiedzającym logiczny punkt wyjścia podczas odwiedzania witryny.</span><span class="sxs-lookup"><span data-stu-id="57748-294">Setting a default home page provides visitors a logical starting point when visiting your site.</span></span> <span data-ttu-id="57748-295">Aby obtworzyć stronę domyślną bez zakwalifikowania identyfikatora URI użytkownika, wywołaj <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> metodę z `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="57748-295">To serve a default page without the user fully qualifying the URI, call the <xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A> method from `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> <span data-ttu-id="57748-296">`UseDefaultFiles` musi być wywoływana przed zapisaniem `UseStaticFiles` pliku domyślnego.</span><span class="sxs-lookup"><span data-stu-id="57748-296">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="57748-297">`UseDefaultFiles` to adres URL, który faktycznie nie obsługuje pliku.</span><span class="sxs-lookup"><span data-stu-id="57748-297">`UseDefaultFiles` is a URL rewriter that doesn't actually serve the file.</span></span> <span data-ttu-id="57748-298">Włącz oprogramowanie pośredniczące plików statycznych za pośrednictwem programu `UseStaticFiles` w celu obsługi pliku.</span><span class="sxs-lookup"><span data-stu-id="57748-298">Enable Static File Middleware via `UseStaticFiles` to serve the file.</span></span>

<span data-ttu-id="57748-299">W programie `UseDefaultFiles` żądania do folderu wyszukują:</span><span class="sxs-lookup"><span data-stu-id="57748-299">With `UseDefaultFiles`, requests to a folder search for:</span></span>

* <span data-ttu-id="57748-300">*default.htm*</span><span class="sxs-lookup"><span data-stu-id="57748-300">*default.htm*</span></span>
* <span data-ttu-id="57748-301">*default.html*</span><span class="sxs-lookup"><span data-stu-id="57748-301">*default.html*</span></span>
* <span data-ttu-id="57748-302">*index.htm*</span><span class="sxs-lookup"><span data-stu-id="57748-302">*index.htm*</span></span>
* <span data-ttu-id="57748-303">*index.html*</span><span class="sxs-lookup"><span data-stu-id="57748-303">*index.html*</span></span>

<span data-ttu-id="57748-304">Pierwszy plik znaleziony z listy jest obsługiwany tak, jakby żądanie było w pełni kwalifikowanym identyfikatorem URI.</span><span class="sxs-lookup"><span data-stu-id="57748-304">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="57748-305">Adres URL przeglądarki nadal odzwierciedla żądany identyfikator URI.</span><span class="sxs-lookup"><span data-stu-id="57748-305">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="57748-306">Następujący kod zmienia domyślną nazwę pliku na *mydefault.html*:</span><span class="sxs-lookup"><span data-stu-id="57748-306">The following code changes the default file name to *mydefault.html*:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a><span data-ttu-id="57748-307">UseFileServer</span><span class="sxs-lookup"><span data-stu-id="57748-307">UseFileServer</span></span>

<span data-ttu-id="57748-308"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> łączy funkcje `UseStaticFiles` , `UseDefaultFiles` i opcjonalnie `UseDirectoryBrowser` .</span><span class="sxs-lookup"><span data-stu-id="57748-308"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="57748-309">Poniższy kod umożliwia obsługę plików statycznych i pliku domyślnego.</span><span class="sxs-lookup"><span data-stu-id="57748-309">The following code enables the serving of static files and the default file.</span></span> <span data-ttu-id="57748-310">Przeglądanie katalogów nie jest włączone.</span><span class="sxs-lookup"><span data-stu-id="57748-310">Directory browsing isn't enabled.</span></span>

```csharp
app.UseFileServer();
```

<span data-ttu-id="57748-311">Poniższy kod kompiluje na Przeciążenie bez parametrów przez włączenie przeglądania katalogów:</span><span class="sxs-lookup"><span data-stu-id="57748-311">The following code builds upon the parameterless overload by enabling directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="57748-312">Weź pod uwagę następującą hierarchię katalogów:</span><span class="sxs-lookup"><span data-stu-id="57748-312">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="57748-313">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="57748-313">**wwwroot**</span></span>
  * <span data-ttu-id="57748-314">**kaskad**</span><span class="sxs-lookup"><span data-stu-id="57748-314">**css**</span></span>
  * <span data-ttu-id="57748-315">**rastrow**</span><span class="sxs-lookup"><span data-stu-id="57748-315">**images**</span></span>
  * <span data-ttu-id="57748-316">**JS**</span><span class="sxs-lookup"><span data-stu-id="57748-316">**js**</span></span>
* <span data-ttu-id="57748-317">**MyStaticFiles**</span><span class="sxs-lookup"><span data-stu-id="57748-317">**MyStaticFiles**</span></span>
  * <span data-ttu-id="57748-318">**rastrow**</span><span class="sxs-lookup"><span data-stu-id="57748-318">**images**</span></span>
    * <span data-ttu-id="57748-319">*banner1. SVG*</span><span class="sxs-lookup"><span data-stu-id="57748-319">*banner1.svg*</span></span>
  * <span data-ttu-id="57748-320">*default.html*</span><span class="sxs-lookup"><span data-stu-id="57748-320">*default.html*</span></span>

<span data-ttu-id="57748-321">Poniższy kod umożliwia włączenie plików statycznych, plików domyślnych i przeglądanie katalogów `MyStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="57748-321">The following code enables static files, default files, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

<span data-ttu-id="57748-322">`AddDirectoryBrowser` musi być wywoływana, gdy `EnableDirectoryBrowsing` wartość właściwości to `true` :</span><span class="sxs-lookup"><span data-stu-id="57748-322">`AddDirectoryBrowser` must be called when the `EnableDirectoryBrowsing` property value is `true`:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

<span data-ttu-id="57748-323">Przy użyciu hierarchii plików i poprzedniego kodu adresy URL są rozpoznawane w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="57748-323">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="57748-324">URI</span><span class="sxs-lookup"><span data-stu-id="57748-324">URI</span></span>            |                             <span data-ttu-id="57748-325">Reakcja</span><span class="sxs-lookup"><span data-stu-id="57748-325">Response</span></span>  |
| ------- | ------|
| <span data-ttu-id="57748-326">*http:// \<server_address> /StaticFiles/images/banner1.SVG*</span><span class="sxs-lookup"><span data-stu-id="57748-326">*http://\<server_address>/StaticFiles/images/banner1.svg*</span></span>    |      <span data-ttu-id="57748-327">MyStaticFiles/images/banner1. SVG</span><span class="sxs-lookup"><span data-stu-id="57748-327">MyStaticFiles/images/banner1.svg</span></span> |
| <span data-ttu-id="57748-328">*http:// \<server_address> /StaticFiles*</span><span class="sxs-lookup"><span data-stu-id="57748-328">*http://\<server_address>/StaticFiles*</span></span>             |     <span data-ttu-id="57748-329">MyStaticFiles/default.html</span><span class="sxs-lookup"><span data-stu-id="57748-329">MyStaticFiles/default.html</span></span> |

<span data-ttu-id="57748-330">Jeśli plik o nazwie Default nie istnieje w katalogu *MyStaticFiles* , *http:// \<server_address> /StaticFiles* zwraca listę katalogów z linkami, które można kliknąć:</span><span class="sxs-lookup"><span data-stu-id="57748-330">If no default-named file exists in the *MyStaticFiles* directory, *http://\<server_address>/StaticFiles* returns the directory listing with clickable links:</span></span>

![Lista plików statycznych](static-files/_static/db2.png)

> [!NOTE]
> <span data-ttu-id="57748-332"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> i <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> przekierować po stronie klienta z `http://{SERVER ADDRESS}/StaticFiles` (bez końcowego ukośnika) do `http://{SERVER ADDRESS}/StaticFiles/` (z końcowym ukośnikiem).</span><span class="sxs-lookup"><span data-stu-id="57748-332"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from `http://{SERVER ADDRESS}/StaticFiles` (without a trailing slash) to `http://{SERVER ADDRESS}/StaticFiles/` (with a trailing slash).</span></span> <span data-ttu-id="57748-333">Względne adresy URL w katalogu *StaticFiles* są nieprawidłowe bez końcowej kreski ułamkowej.</span><span class="sxs-lookup"><span data-stu-id="57748-333">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash.</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="57748-334">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="57748-334">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="57748-335"><xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider>Klasa zawiera `Mappings` Właściwość służącą jako mapowanie rozszerzeń plików na typy zawartości MIME.</span><span class="sxs-lookup"><span data-stu-id="57748-335">The <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> class contains a `Mappings` property serving as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="57748-336">W poniższym przykładzie kilka rozszerzeń plików są zarejestrowane na znanych typach MIME.</span><span class="sxs-lookup"><span data-stu-id="57748-336">In the following sample, several file extensions are registered to known MIME types.</span></span> <span data-ttu-id="57748-337">Rozszerzenie *. rtf* jest zastępowane, a plik *MP4* jest usuwany.</span><span class="sxs-lookup"><span data-stu-id="57748-337">The *.rtf* extension is replaced, and *.mp4* is removed.</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

<span data-ttu-id="57748-338">Zobacz [typy zawartości MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).</span><span class="sxs-lookup"><span data-stu-id="57748-338">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

<span data-ttu-id="57748-339">Aby uzyskać informacje na temat używania niestandardowego <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> lub do konfigurowania <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> innych Blazor aplikacji serwerowych, zobacz <xref:blazor/fundamentals/static-files> .</span><span class="sxs-lookup"><span data-stu-id="57748-339">For information on using a custom <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> or to configure other <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> in Blazor Server apps, see <xref:blazor/fundamentals/static-files>.</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="57748-340">Niestandardowe typy zawartości</span><span class="sxs-lookup"><span data-stu-id="57748-340">Non-standard content types</span></span>

<span data-ttu-id="57748-341">Oprogramowanie pośredniczące plików static rozpoznaje niemal 400 znanych typów zawartości plików.</span><span class="sxs-lookup"><span data-stu-id="57748-341">Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="57748-342">Jeśli użytkownik zażąda pliku z nieznanym typem pliku, oprogramowanie pośredniczące plików przesyła żądanie do następnego oprogramowania pośredniczącego w potoku.</span><span class="sxs-lookup"><span data-stu-id="57748-342">If the user requests a file with an unknown file type, Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="57748-343">Jeśli żadne oprogramowanie pośredniczące nie obsługuje żądania, zwracana jest odpowiedź *404* .</span><span class="sxs-lookup"><span data-stu-id="57748-343">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="57748-344">Jeśli przeglądanie katalogów jest włączone, link do pliku zostanie wyświetlony na liście katalogów.</span><span class="sxs-lookup"><span data-stu-id="57748-344">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="57748-345">Poniższy kod umożliwia obsługę nieznanych typów i renderuje nieznany plik jako obraz:</span><span class="sxs-lookup"><span data-stu-id="57748-345">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](static-files/samples/1.x/StaticFilesSample/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

<span data-ttu-id="57748-346">W powyższym kodzie żądanie dotyczące pliku z nieznanym typem zawartości jest zwracane jako obraz.</span><span class="sxs-lookup"><span data-stu-id="57748-346">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="57748-347">Włączenie <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> stanowi zagrożenie bezpieczeństwa.</span><span class="sxs-lookup"><span data-stu-id="57748-347">Enabling <xref:Microsoft.AspNetCore.Builder.StaticFileOptions.ServeUnknownFileTypes> is a security risk.</span></span> <span data-ttu-id="57748-348">Jest on domyślnie wyłączony i nie jest zalecane jego użycie.</span><span class="sxs-lookup"><span data-stu-id="57748-348">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="57748-349">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) zapewnia bezpieczniejsze rozwiązanie do obsługi plików z rozszerzeniami niestandardowymi.</span><span class="sxs-lookup"><span data-stu-id="57748-349">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="57748-350">Obsługiwanie plików z wielu lokalizacji</span><span class="sxs-lookup"><span data-stu-id="57748-350">Serve files from multiple locations</span></span>

<span data-ttu-id="57748-351">`UseStaticFiles` i `UseFileServer` domyślnym dostawcą plików wskazywanym w lokalizacji *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="57748-351">`UseStaticFiles` and `UseFileServer` defaults to the file provider pointing at *wwwroot*.</span></span> <span data-ttu-id="57748-352">Można podać dodatkowe wystąpienia `UseStaticFiles` i `UseFileServer` z innymi dostawcami plików do obsługi plików z innych lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="57748-352">You can provide additional instances of `UseStaticFiles` and `UseFileServer` with other file providers to serve files from other locations.</span></span> <span data-ttu-id="57748-353">Aby uzyskać więcej informacji, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/15578)w serwisie GitHub.</span><span class="sxs-lookup"><span data-stu-id="57748-353">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

### <a name="considerations"></a><span data-ttu-id="57748-354">Zagadnienia do rozważenia</span><span class="sxs-lookup"><span data-stu-id="57748-354">Considerations</span></span>

> [!WARNING]
> <span data-ttu-id="57748-355">`UseDirectoryBrowser` i `UseStaticFiles` mogą wyciekać klucze tajne.</span><span class="sxs-lookup"><span data-stu-id="57748-355">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="57748-356">Wyłączenie przeglądania katalogów w środowisku produkcyjnym jest zdecydowanie zalecane.</span><span class="sxs-lookup"><span data-stu-id="57748-356">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="57748-357">Uważnie Przejrzyj katalogi, które są włączone za pośrednictwem `UseStaticFiles` lub `UseDirectoryBrowser` .</span><span class="sxs-lookup"><span data-stu-id="57748-357">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="57748-358">Cały katalog i jego katalogi podrzędne stają się publicznie dostępne.</span><span class="sxs-lookup"><span data-stu-id="57748-358">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="57748-359">Pliki magazynu odpowiednie do obsłużenia publicznie w dedykowanym katalogu, takie jak *\<content_root> /wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="57748-359">Store files suitable for serving to the public in a dedicated directory, such as *\<content_root>/wwwroot*.</span></span> <span data-ttu-id="57748-360">Oddziel te pliki od widoków MVC, Razor stron (tylko 2. x), plików konfiguracji itp.</span><span class="sxs-lookup"><span data-stu-id="57748-360">Separate these files from MVC views, Razor Pages (2.x only), configuration files, etc.</span></span>

* <span data-ttu-id="57748-361">Adresy URL dla zawartości uwidocznionej `UseDirectoryBrowser` i `UseStaticFiles` podlegają ograniczeniom dotyczącym wielkości liter i znaków w źródłowym systemie plików.</span><span class="sxs-lookup"><span data-stu-id="57748-361">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="57748-362">Na przykład w systemie Windows nie jest rozróżniana wielkość liter &mdash; macOS i Linux.</span><span class="sxs-lookup"><span data-stu-id="57748-362">For example, Windows is case insensitive&mdash;macOS and Linux aren't.</span></span>

* <span data-ttu-id="57748-363">ASP.NET Core aplikacje hostowane w usługach IIS używają [modułu ASP.NET Core](xref:host-and-deploy/aspnet-core-module) do przesyłania dalej wszystkich żądań do aplikacji, w tym żądań plików statycznych.</span><span class="sxs-lookup"><span data-stu-id="57748-363">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="57748-364">Procedura obsługi pliku statycznego usług IIS nie jest używana.</span><span class="sxs-lookup"><span data-stu-id="57748-364">The IIS static file handler isn't used.</span></span> <span data-ttu-id="57748-365">Nie ma możliwości obsługi żądań, zanim są one obsługiwane przez moduł.</span><span class="sxs-lookup"><span data-stu-id="57748-365">It has no chance to handle requests before they're handled by the module.</span></span>

* <span data-ttu-id="57748-366">Wykonaj następujące kroki w Menedżerze usług IIS, aby usunąć program obsługi plików statycznych usług IIS na poziomie serwera lub witryny sieci Web:</span><span class="sxs-lookup"><span data-stu-id="57748-366">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="57748-367">Przejdź do funkcji **moduły** .</span><span class="sxs-lookup"><span data-stu-id="57748-367">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="57748-368">Na liście wybierz pozycję **StaticFileModule** .</span><span class="sxs-lookup"><span data-stu-id="57748-368">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="57748-369">Kliknij przycisk **Usuń** na pasku bocznym **Akcje** .</span><span class="sxs-lookup"><span data-stu-id="57748-369">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="57748-370">Jeśli program obsługi plików statycznych usług IIS jest włączony **i** moduł ASP.NET Core jest niepoprawnie skonfigurowany, obsługiwane są pliki statyczne.</span><span class="sxs-lookup"><span data-stu-id="57748-370">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="57748-371">Dzieje się tak na przykład wtedy, gdy plik *web.config* nie zostanie wdrożony.</span><span class="sxs-lookup"><span data-stu-id="57748-371">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="57748-372">Umieść pliki kodu (w tym *. cs* i *. cshtml*) poza [katalogiem głównym sieci Web](xref:fundamentals/index#web-root)projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="57748-372">Place code files (including *.cs* and *.cshtml*) outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="57748-373">W związku z tym tworzone jest podział logiczny między zawartością po stronie klienta a kodem opartym na serwerze.</span><span class="sxs-lookup"><span data-stu-id="57748-373">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="57748-374">Zapobiega to wyciekowi kodu po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="57748-374">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="57748-375">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="57748-375">Additional resources</span></span>

* [<span data-ttu-id="57748-376">Oprogramowanie pośredniczące</span><span class="sxs-lookup"><span data-stu-id="57748-376">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="57748-377">Wprowadzenie do programu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="57748-377">Introduction to ASP.NET Core</span></span>](xref:index)

::: moniker-end
