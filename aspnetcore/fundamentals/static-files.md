---
title: Pliki statyczne w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak wyświetlać i zabezpieczać pliki statyczne oraz konfigurować zachowania statycznego oprogramowania pośredniczącego hostingu plików w aplikacji sieci web ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
uid: fundamentals/static-files
ms.openlocfilehash: 95a77defc7e98328e1f4e3615648b1d14485e51e
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78660126"
---
# <a name="static-files-in-aspnet-core"></a><span data-ttu-id="fbbd8-103">Pliki statyczne w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fbbd8-103">Static files in ASP.NET Core</span></span>

<span data-ttu-id="fbbd8-104">Przez [Rick Anderson](https://twitter.com/RickAndMSFT) i Scott [Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="fbbd8-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="fbbd8-105">Pliki statyczne, takie jak HTML, CSS, obrazy i JavaScript, to zasoby, które aplikacja ASP.NET Core służy bezpośrednio klientom.</span><span class="sxs-lookup"><span data-stu-id="fbbd8-105">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients.</span></span> <span data-ttu-id="fbbd8-106">Do włączenia obsługi tych plików wymagana jest pewna konfiguracja.</span><span class="sxs-lookup"><span data-stu-id="fbbd8-106">Some configuration is required to enable serving of these files.</span></span>

<span data-ttu-id="fbbd8-107">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="fbbd8-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="fbbd8-108">Obsługa plików statycznych</span><span class="sxs-lookup"><span data-stu-id="fbbd8-108">Serve static files</span></span>

<span data-ttu-id="fbbd8-109">Pliki statyczne są przechowywane w katalogu [głównym](xref:fundamentals/index#web-root) projektu.</span><span class="sxs-lookup"><span data-stu-id="fbbd8-109">Static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="fbbd8-110">Katalog domyślny to *{content root}/wwwroot*, ale można go zmienić za pomocą metody [UseWebRoot.](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_)</span><span class="sxs-lookup"><span data-stu-id="fbbd8-110">The default directory is *{content root}/wwwroot*, but it can be changed via the [UseWebRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) method.</span></span> <span data-ttu-id="fbbd8-111">Aby uzyskać więcej informacji, zobacz [Katalog główny zawartości](xref:fundamentals/index#content-root) i katalog główny sieci [Web.](xref:fundamentals/index#web-root)</span><span class="sxs-lookup"><span data-stu-id="fbbd8-111">See [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root) for more information.</span></span>

<span data-ttu-id="fbbd8-112">Host internetowy aplikacji musi być poinformowany o katalogu głównym zawartości.</span><span class="sxs-lookup"><span data-stu-id="fbbd8-112">The app's web host must be made aware of the content root directory.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="fbbd8-113">Metoda `WebHost.CreateDefaultBuilder` ustawia katalog główny zawartości do bieżącego katalogu:</span><span class="sxs-lookup"><span data-stu-id="fbbd8-113">The `WebHost.CreateDefaultBuilder` method sets the content root to the current directory:</span></span>

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="fbbd8-114">Ustaw katalog główny zawartości na bieżący katalog, wywołując [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseContentRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) wewnątrz `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="fbbd8-114">Set the content root to the current directory by invoking [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseContentRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) inside of `Program.Main`:</span></span>

[!code-csharp[](static-files/samples/1x/Program.cs?name=snippet_ProgramClass&highlight=7)]

::: moniker-end

<span data-ttu-id="fbbd8-115">Pliki statyczne są dostępne za pośrednictwem ścieżki względem [katalogu głównego sieci Web.](xref:fundamentals/index#web-root)</span><span class="sxs-lookup"><span data-stu-id="fbbd8-115">Static files are accessible via a path relative to the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="fbbd8-116">Na przykład szablon projektu **aplikacji sieci Web** zawiera kilka folderów w folderze *wwwroot:*</span><span class="sxs-lookup"><span data-stu-id="fbbd8-116">For example, the **Web Application** project template contains several folders within the *wwwroot* folder:</span></span>

* <span data-ttu-id="fbbd8-117">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="fbbd8-117">**wwwroot**</span></span>
  * <span data-ttu-id="fbbd8-118">**Css**</span><span class="sxs-lookup"><span data-stu-id="fbbd8-118">**css**</span></span>
  * <span data-ttu-id="fbbd8-119">**Obrazów**</span><span class="sxs-lookup"><span data-stu-id="fbbd8-119">**images**</span></span>
  * <span data-ttu-id="fbbd8-120">**Js**</span><span class="sxs-lookup"><span data-stu-id="fbbd8-120">**js**</span></span>

<span data-ttu-id="fbbd8-121">Format URI dostępu do pliku w podfolderze *obrazów* jest *http://\<server_address>/images/\<image_file_name>*.</span><span class="sxs-lookup"><span data-stu-id="fbbd8-121">The URI format to access a file in the *images* subfolder is *http://\<server_address>/images/\<image_file_name>*.</span></span> <span data-ttu-id="fbbd8-122">Na przykład *http://localhost:9189/images/banner3.svg*.</span><span class="sxs-lookup"><span data-stu-id="fbbd8-122">For example, *http://localhost:9189/images/banner3.svg*.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="fbbd8-123">Jeśli jest kierowana na program .NET Framework, dodaj do projektu pakiet [Microsoft.AspNetCore.StaticFiles.](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/)</span><span class="sxs-lookup"><span data-stu-id="fbbd8-123">If targeting .NET Framework, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to the project.</span></span> <span data-ttu-id="fbbd8-124">Jeśli jest kierowana na program .NET Core, [metapakiet Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) zawiera ten pakiet.</span><span class="sxs-lookup"><span data-stu-id="fbbd8-124">If targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) includes this package.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="fbbd8-125">Jeśli jest kierowana na program .NET Framework, dodaj do projektu pakiet [Microsoft.AspNetCore.StaticFiles.](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/)</span><span class="sxs-lookup"><span data-stu-id="fbbd8-125">If targeting .NET Framework, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to the project.</span></span> <span data-ttu-id="fbbd8-126">Jeśli jest kierowana na program .NET Core, [metapakiet Microsoft.AspNetCore.All](xref:fundamentals/metapackage) zawiera ten pakiet.</span><span class="sxs-lookup"><span data-stu-id="fbbd8-126">If targeting .NET Core, the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage) includes this package.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="fbbd8-127">Dodaj pakiet [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) do projektu.</span><span class="sxs-lookup"><span data-stu-id="fbbd8-127">Add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to the project.</span></span>

::: moniker-end

<span data-ttu-id="fbbd8-128">Skonfiguruj [oprogramowanie pośredniczące,](xref:fundamentals/middleware/index) które umożliwia obsługę plików statycznych.</span><span class="sxs-lookup"><span data-stu-id="fbbd8-128">Configure the [middleware](xref:fundamentals/middleware/index) which enables the serving of static files.</span></span>

### <a name="serve-files-inside-of-web-root"></a><span data-ttu-id="fbbd8-129">Obsługa plików wewnątrz katalogu głównego sieci Web</span><span class="sxs-lookup"><span data-stu-id="fbbd8-129">Serve files inside of web root</span></span>

<span data-ttu-id="fbbd8-130">Wywołać [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) metody `Startup.Configure`w :</span><span class="sxs-lookup"><span data-stu-id="fbbd8-130">Invoke the [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) method within `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

<span data-ttu-id="fbbd8-131">Przeciążenie `UseStaticFiles` metody bez parametrów oznacza pliki w [katalogu głównym sieci Web](xref:fundamentals/index#web-root) jako servable.</span><span class="sxs-lookup"><span data-stu-id="fbbd8-131">The parameterless `UseStaticFiles` method overload marks the files in [web root](xref:fundamentals/index#web-root) as servable.</span></span> <span data-ttu-id="fbbd8-132">Następujące oznaczenia odwołują się do *wwwroot/images/banner1.svg:*</span><span class="sxs-lookup"><span data-stu-id="fbbd8-132">The following markup references *wwwroot/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

<span data-ttu-id="fbbd8-133">W poprzednim kodzie znak `~/` tyldy wskazuje katalog główny sieci [Web](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="fbbd8-133">In the preceding code, the tilde character `~/` points to the [web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="fbbd8-134">Obsługa plików poza katalogiem głównym sieci Web</span><span class="sxs-lookup"><span data-stu-id="fbbd8-134">Serve files outside of web root</span></span>

<span data-ttu-id="fbbd8-135">Należy wziąć pod uwagę hierarchię katalogów, w której pliki statyczne, które mają być obsługiwane, znajdują się poza [katalogiem głównym sieci Web:](xref:fundamentals/index#web-root)</span><span class="sxs-lookup"><span data-stu-id="fbbd8-135">Consider a directory hierarchy in which the static files to be served reside outside of the [web root](xref:fundamentals/index#web-root):</span></span>

* <span data-ttu-id="fbbd8-136">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="fbbd8-136">**wwwroot**</span></span>
  * <span data-ttu-id="fbbd8-137">**Css**</span><span class="sxs-lookup"><span data-stu-id="fbbd8-137">**css**</span></span>
  * <span data-ttu-id="fbbd8-138">**Obrazów**</span><span class="sxs-lookup"><span data-stu-id="fbbd8-138">**images**</span></span>
  * <span data-ttu-id="fbbd8-139">**Js**</span><span class="sxs-lookup"><span data-stu-id="fbbd8-139">**js**</span></span>
* <span data-ttu-id="fbbd8-140">**Pliki MyStatic**</span><span class="sxs-lookup"><span data-stu-id="fbbd8-140">**MyStaticFiles**</span></span>
  * <span data-ttu-id="fbbd8-141">**Obrazów**</span><span class="sxs-lookup"><span data-stu-id="fbbd8-141">**images**</span></span>
    * <span data-ttu-id="fbbd8-142">*banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="fbbd8-142">*banner1.svg*</span></span>

<span data-ttu-id="fbbd8-143">Żądanie może uzyskać dostęp do pliku *banner1.svg,* konfigurując oprogramowanie pośredniczące plików statycznych w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="fbbd8-143">A request can access the *banner1.svg* file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](static-files/samples/1x/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

<span data-ttu-id="fbbd8-144">W poprzednim kodzie hierarchia *katalogów MyStaticFiles* jest publicznie widoczna za pośrednictwem segmentu URI *StaticFiles.*</span><span class="sxs-lookup"><span data-stu-id="fbbd8-144">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="fbbd8-145">Żądanie *http://\<server_address>/StaticFiles/images/banner1.svg* obsługuje plik *banner1.svg.*</span><span class="sxs-lookup"><span data-stu-id="fbbd8-145">A request to *http://\<server_address>/StaticFiles/images/banner1.svg* serves the *banner1.svg* file.</span></span>

<span data-ttu-id="fbbd8-146">Następujące oznaczenia odwołują się do *mystaticfiles/images/banner1.svg:*</span><span class="sxs-lookup"><span data-stu-id="fbbd8-146">The following markup references *MyStaticFiles/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a><span data-ttu-id="fbbd8-147">Ustawianie nagłówków odpowiedzi HTTP</span><span class="sxs-lookup"><span data-stu-id="fbbd8-147">Set HTTP response headers</span></span>

<span data-ttu-id="fbbd8-148">Obiekt [StaticFileOptions](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions) może służyć do ustawiania nagłówków odpowiedzi HTTP.</span><span class="sxs-lookup"><span data-stu-id="fbbd8-148">A [StaticFileOptions](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions) object can be used to set HTTP response headers.</span></span> <span data-ttu-id="fbbd8-149">Oprócz konfigurowania pliku statycznego obsługującego z katalogu głównego `Cache-Control` sieci [Web,](xref:fundamentals/index#web-root)następujący kod ustawia nagłówek:</span><span class="sxs-lookup"><span data-stu-id="fbbd8-149">In addition to configuring static file serving from the [web root](xref:fundamentals/index#web-root), the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](static-files/samples/1x/StartupAddHeader.cs?name=snippet_ConfigureMethod)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="fbbd8-150">Metoda [HeaderDictionaryExtensions.Append](/dotnet/api/microsoft.aspnetcore.http.headerdictionaryextensions.append) istnieje w pakiecie [Microsoft.AspNetCore.Http.](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/)</span><span class="sxs-lookup"><span data-stu-id="fbbd8-150">The [HeaderDictionaryExtensions.Append](/dotnet/api/microsoft.aspnetcore.http.headerdictionaryextensions.append) method exists in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

<span data-ttu-id="fbbd8-151">Pliki zostały udostępnione publicznie w pamięci podręcznej przez 10 minut (600 sekund) w środowisku programistycznym:</span><span class="sxs-lookup"><span data-stu-id="fbbd8-151">The files have been made publicly cacheable for 10 minutes (600 seconds) in the Development environment:</span></span>

![Dodano nagłówki odpowiedzi z nagłówkiem Cache-Control](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="fbbd8-153">Autoryzacja plików statycznych</span><span class="sxs-lookup"><span data-stu-id="fbbd8-153">Static file authorization</span></span>

<span data-ttu-id="fbbd8-154">Oprogramowanie pośredniczące plików statycznych nie zapewnia sprawdzania autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="fbbd8-154">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="fbbd8-155">Wszystkie pliki, które są przez nią obsługiwane, w tym te objęte *wwwroot,* są publicznie dostępne.</span><span class="sxs-lookup"><span data-stu-id="fbbd8-155">Any files served by it, including those under *wwwroot*, are publicly accessible.</span></span> <span data-ttu-id="fbbd8-156">Aby wyświetlać pliki na podstawie autoryzacji:</span><span class="sxs-lookup"><span data-stu-id="fbbd8-156">To serve files based on authorization:</span></span>

* <span data-ttu-id="fbbd8-157">Przechowuj je poza *wwwroot* i dowolnego katalogu dostępnego dla oprogramowania pośredniczącego plików statycznych.</span><span class="sxs-lookup"><span data-stu-id="fbbd8-157">Store them outside of *wwwroot* and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="fbbd8-158">Podawać je za pomocą metody akcji, do której jest stosowana autoryzacja.</span><span class="sxs-lookup"><span data-stu-id="fbbd8-158">Serve them via an action method to which authorization is applied.</span></span> <span data-ttu-id="fbbd8-159">Zwraca obiekt [FileResult:](/dotnet/api/microsoft.aspnetcore.mvc.fileresult)</span><span class="sxs-lookup"><span data-stu-id="fbbd8-159">Return a [FileResult](/dotnet/api/microsoft.aspnetcore.mvc.fileresult) object:</span></span>

  [!code-csharp[](static-files/samples/1x/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a><span data-ttu-id="fbbd8-160">Włączanie przeglądania katalogów</span><span class="sxs-lookup"><span data-stu-id="fbbd8-160">Enable directory browsing</span></span>

<span data-ttu-id="fbbd8-161">Przeglądanie katalogów umożliwia użytkownikom aplikacji sieci web wyświetlanie listy katalogów i plików w określonym katalogu.</span><span class="sxs-lookup"><span data-stu-id="fbbd8-161">Directory browsing allows users of your web app to see a directory listing and files within a specified directory.</span></span> <span data-ttu-id="fbbd8-162">Przeglądanie katalogów jest domyślnie wyłączone ze względów bezpieczeństwa (patrz [Zagadnienia).](#considerations)</span><span class="sxs-lookup"><span data-stu-id="fbbd8-162">Directory browsing is disabled by default for security reasons (see [Considerations](#considerations)).</span></span> <span data-ttu-id="fbbd8-163">Włącz przeglądanie katalogów, wywołując metodę [UseDirectoryBrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) w `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="fbbd8-163">Enable directory browsing by invoking the [UseDirectoryBrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) method in `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

<span data-ttu-id="fbbd8-164">Dodaj wymagane usługi, wywołując metodę [AddDirectoryBrowser](/dotnet/api/microsoft.extensions.dependencyinjection.directorybrowserserviceextensions.adddirectorybrowser#Microsoft_Extensions_DependencyInjection_DirectoryBrowserServiceExtensions_AddDirectoryBrowser_Microsoft_Extensions_DependencyInjection_IServiceCollection_) z `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="fbbd8-164">Add required services by invoking the [AddDirectoryBrowser](/dotnet/api/microsoft.extensions.dependencyinjection.directorybrowserserviceextensions.adddirectorybrowser#Microsoft_Extensions_DependencyInjection_DirectoryBrowserServiceExtensions_AddDirectoryBrowser_Microsoft_Extensions_DependencyInjection_IServiceCollection_) method from `Startup.ConfigureServices`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

<span data-ttu-id="fbbd8-165">Poprzedni kod umożliwia przeglądanie katalogu folderu *wwwroot/images* przy użyciu adresu URL *http://\<server_address>/MyImages,* z linkami do każdego pliku i folderu:</span><span class="sxs-lookup"><span data-stu-id="fbbd8-165">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*, with links to each file and folder:</span></span>

![przeglądanie katalogów](static-files/_static/dir-browse.png)

<span data-ttu-id="fbbd8-167">Zobacz [zagadnienia dotyczące](#considerations) zagrożeń bezpieczeństwa podczas włączania przeglądania.</span><span class="sxs-lookup"><span data-stu-id="fbbd8-167">See [Considerations](#considerations) on the security risks when enabling browsing.</span></span>

<span data-ttu-id="fbbd8-168">Zanotuj dwa `UseStaticFiles` wywołania w poniższym przykładzie.</span><span class="sxs-lookup"><span data-stu-id="fbbd8-168">Note the two `UseStaticFiles` calls in the following example.</span></span> <span data-ttu-id="fbbd8-169">Pierwsze wywołanie umożliwia wyświetlanie plików statycznych w folderze *wwwroot.*</span><span class="sxs-lookup"><span data-stu-id="fbbd8-169">The first call enables the serving of static files in the *wwwroot* folder.</span></span> <span data-ttu-id="fbbd8-170">Drugie wywołanie umożliwia przeglądanie katalogu folderu *wwwroot/images* przy użyciu adresu URL *http://\<server_address>/MyImages:*</span><span class="sxs-lookup"><span data-stu-id="fbbd8-170">The second call enables directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*:</span></span>

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a><span data-ttu-id="fbbd8-171">Obsługa dokumentu domyślnego</span><span class="sxs-lookup"><span data-stu-id="fbbd8-171">Serve a default document</span></span>

<span data-ttu-id="fbbd8-172">Ustawienie domyślnej strony głównej zapewnia odwiedzającym logiczny punkt wyjścia podczas odwiedzania witryny.</span><span class="sxs-lookup"><span data-stu-id="fbbd8-172">Setting a default home page provides visitors a logical starting point when visiting your site.</span></span> <span data-ttu-id="fbbd8-173">Aby obsługiwać stronę domyślną bez pełnego kwalifikowania identyfikatora URI przez `Startup.Configure`użytkownika, należy wywołać metodę [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) z:</span><span class="sxs-lookup"><span data-stu-id="fbbd8-173">To serve a default page without the user fully qualifying the URI, call the [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) method from `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> <span data-ttu-id="fbbd8-174">`UseDefaultFiles`należy wywołać `UseStaticFiles` przed, aby służyć plik domyślny.</span><span class="sxs-lookup"><span data-stu-id="fbbd8-174">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="fbbd8-175">`UseDefaultFiles`to nagrywarka adresów URL, która w rzeczywistości nie obsługuje pliku.</span><span class="sxs-lookup"><span data-stu-id="fbbd8-175">`UseDefaultFiles` is a URL rewriter that doesn't actually serve the file.</span></span> <span data-ttu-id="fbbd8-176">Włącz oprogramowanie pośredniczące `UseStaticFiles` plików statycznych za pośrednictwem do obsługi pliku.</span><span class="sxs-lookup"><span data-stu-id="fbbd8-176">Enable Static File Middleware via `UseStaticFiles` to serve the file.</span></span>

<span data-ttu-id="fbbd8-177">W `UseDefaultFiles`przypadku żądań do wyszukiwania folderów:</span><span class="sxs-lookup"><span data-stu-id="fbbd8-177">With `UseDefaultFiles`, requests to a folder search for:</span></span>

* <span data-ttu-id="fbbd8-178">*Default.htm*</span><span class="sxs-lookup"><span data-stu-id="fbbd8-178">*default.htm*</span></span>
* <span data-ttu-id="fbbd8-179">*Default.html*</span><span class="sxs-lookup"><span data-stu-id="fbbd8-179">*default.html*</span></span>
* <span data-ttu-id="fbbd8-180">*Index.htm*</span><span class="sxs-lookup"><span data-stu-id="fbbd8-180">*index.htm*</span></span>
* <span data-ttu-id="fbbd8-181">*Index.html*</span><span class="sxs-lookup"><span data-stu-id="fbbd8-181">*index.html*</span></span>

<span data-ttu-id="fbbd8-182">Pierwszy plik znaleziony z listy jest obsługiwany tak, jakby żądanie było w pełni kwalifikowanym identyfikatorem URI.</span><span class="sxs-lookup"><span data-stu-id="fbbd8-182">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="fbbd8-183">Adres URL przeglądarki nadal odzwierciedla żądany identyfikator URI.</span><span class="sxs-lookup"><span data-stu-id="fbbd8-183">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="fbbd8-184">Poniższy kod zmienia domyślną nazwę pliku na *mydefault.html:*</span><span class="sxs-lookup"><span data-stu-id="fbbd8-184">The following code changes the default file name to *mydefault.html*:</span></span>

[!code-csharp[](static-files/samples/1x/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a><span data-ttu-id="fbbd8-185">Użyj pliku serwera</span><span class="sxs-lookup"><span data-stu-id="fbbd8-185">UseFileServer</span></span>

<span data-ttu-id="fbbd8-186"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*>łączy w sobie `UseStaticFiles`funkcjonalność `UseDefaultFiles`, i `UseDirectoryBrowser`opcjonalnie .</span><span class="sxs-lookup"><span data-stu-id="fbbd8-186"><xref:Microsoft.AspNetCore.Builder.FileServerExtensions.UseFileServer*> combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and optionally `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="fbbd8-187">Poniższy kod umożliwia wyświetlanie plików statycznych i pliku domyślnego.</span><span class="sxs-lookup"><span data-stu-id="fbbd8-187">The following code enables the serving of static files and the default file.</span></span> <span data-ttu-id="fbbd8-188">Przeglądanie katalogów nie jest włączone.</span><span class="sxs-lookup"><span data-stu-id="fbbd8-188">Directory browsing isn't enabled.</span></span>

```csharp
app.UseFileServer();
```

<span data-ttu-id="fbbd8-189">Następujący kod opiera się na przeciążeniu bez parametrów, włączając przeglądanie katalogów:</span><span class="sxs-lookup"><span data-stu-id="fbbd8-189">The following code builds upon the parameterless overload by enabling directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="fbbd8-190">Należy wziąć pod uwagę następującą hierarchię katalogów:</span><span class="sxs-lookup"><span data-stu-id="fbbd8-190">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="fbbd8-191">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="fbbd8-191">**wwwroot**</span></span>
  * <span data-ttu-id="fbbd8-192">**Css**</span><span class="sxs-lookup"><span data-stu-id="fbbd8-192">**css**</span></span>
  * <span data-ttu-id="fbbd8-193">**Obrazów**</span><span class="sxs-lookup"><span data-stu-id="fbbd8-193">**images**</span></span>
  * <span data-ttu-id="fbbd8-194">**Js**</span><span class="sxs-lookup"><span data-stu-id="fbbd8-194">**js**</span></span>
* <span data-ttu-id="fbbd8-195">**Pliki MyStatic**</span><span class="sxs-lookup"><span data-stu-id="fbbd8-195">**MyStaticFiles**</span></span>
  * <span data-ttu-id="fbbd8-196">**Obrazów**</span><span class="sxs-lookup"><span data-stu-id="fbbd8-196">**images**</span></span>
    * <span data-ttu-id="fbbd8-197">*banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="fbbd8-197">*banner1.svg*</span></span>
  * <span data-ttu-id="fbbd8-198">*Default.html*</span><span class="sxs-lookup"><span data-stu-id="fbbd8-198">*default.html*</span></span>

<span data-ttu-id="fbbd8-199">Poniższy kod umożliwia przeglądanie plików statycznych, plików `MyStaticFiles`domyślnych i katalogów:</span><span class="sxs-lookup"><span data-stu-id="fbbd8-199">The following code enables static files, default files, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

<span data-ttu-id="fbbd8-200">`AddDirectoryBrowser`musi być wywoływana, `EnableDirectoryBrowsing` `true`gdy wartość właściwości jest:</span><span class="sxs-lookup"><span data-stu-id="fbbd8-200">`AddDirectoryBrowser` must be called when the `EnableDirectoryBrowsing` property value is `true`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

<span data-ttu-id="fbbd8-201">Korzystając z hierarchii plików i poprzedniego kodu, adresy URL rozwiązują następujące rozwiązania:</span><span class="sxs-lookup"><span data-stu-id="fbbd8-201">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="fbbd8-202">Identyfikator URI</span><span class="sxs-lookup"><span data-stu-id="fbbd8-202">URI</span></span>            |                             <span data-ttu-id="fbbd8-203">Odpowiedź</span><span class="sxs-lookup"><span data-stu-id="fbbd8-203">Response</span></span>  |
| ------- | ------|
| <span data-ttu-id="fbbd8-204">*http://\<server_address>/StaticFiles/images/banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="fbbd8-204">*http://\<server_address>/StaticFiles/images/banner1.svg*</span></span>    |      <span data-ttu-id="fbbd8-205">MyStaticFiles/images/banner1.svg</span><span class="sxs-lookup"><span data-stu-id="fbbd8-205">MyStaticFiles/images/banner1.svg</span></span> |
| <span data-ttu-id="fbbd8-206">*http://\<server_address>/StaticFiles*</span><span class="sxs-lookup"><span data-stu-id="fbbd8-206">*http://\<server_address>/StaticFiles*</span></span>             |     <span data-ttu-id="fbbd8-207">MyStaticFiles/default.html</span><span class="sxs-lookup"><span data-stu-id="fbbd8-207">MyStaticFiles/default.html</span></span> |

<span data-ttu-id="fbbd8-208">Jeśli w katalogu *MyStaticFiles* nie istnieje plik o domyślnej nazwie, *http://\<server_address>/StaticFiles* zwraca listę katalogów z klikalnymi łączami:</span><span class="sxs-lookup"><span data-stu-id="fbbd8-208">If no default-named file exists in the *MyStaticFiles* directory, *http://\<server_address>/StaticFiles* returns the directory listing with clickable links:</span></span>

![Lista plików statycznych](static-files/_static/db2.png)

> [!NOTE]
> <span data-ttu-id="fbbd8-210"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*>i <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> wykonać przekierowanie po `http://{SERVER ADDRESS}/StaticFiles` stronie klienta z (bez końcowego ukośnika) do `http://{SERVER ADDRESS}/StaticFiles/` (z ukośnikiem).</span><span class="sxs-lookup"><span data-stu-id="fbbd8-210"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from `http://{SERVER ADDRESS}/StaticFiles` (without a trailing slash) to `http://{SERVER ADDRESS}/StaticFiles/` (with a trailing slash).</span></span> <span data-ttu-id="fbbd8-211">Względne adresy URL w katalogu *StaticFiles* są nieprawidłowe bez końcowego ukośnika.</span><span class="sxs-lookup"><span data-stu-id="fbbd8-211">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash.</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="fbbd8-212">PlikExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="fbbd8-212">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="fbbd8-213">[Klasa FileExtensionContentTypeProvider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) zawiera `Mappings` właściwość służącą jako mapowanie rozszerzeń plików do typów zawartości MIME.</span><span class="sxs-lookup"><span data-stu-id="fbbd8-213">The [FileExtensionContentTypeProvider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) class contains a `Mappings` property serving as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="fbbd8-214">W poniższym przykładzie kilka rozszerzeń plików są rejestrowane do znanych typów MIME.</span><span class="sxs-lookup"><span data-stu-id="fbbd8-214">In the following sample, several file extensions are registered to known MIME types.</span></span> <span data-ttu-id="fbbd8-215">Rozszerzenie *.rtf* zostanie zastąpione, a *plik .mp4* zostanie usunięty.</span><span class="sxs-lookup"><span data-stu-id="fbbd8-215">The *.rtf* extension is replaced, and *.mp4* is removed.</span></span>

[!code-csharp[](static-files/samples/1x/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

<span data-ttu-id="fbbd8-216">Zobacz [typy zawartości MIME](https://www.iana.org/assignments/media-types/media-types.xhtml).</span><span class="sxs-lookup"><span data-stu-id="fbbd8-216">See [MIME content types](https://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="fbbd8-217">Niestandardowe typy zawartości</span><span class="sxs-lookup"><span data-stu-id="fbbd8-217">Non-standard content types</span></span>

<span data-ttu-id="fbbd8-218">Oprogramowanie pośredniczące plików statycznych rozumie prawie 400 znanych typów zawartości plików.</span><span class="sxs-lookup"><span data-stu-id="fbbd8-218">Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="fbbd8-219">Jeśli użytkownik zażąda pliku o nieznanym typie pliku, oprogramowanie pośredniczące plików statycznych przekazuje żądanie do następnego oprogramowania pośredniczącego w potoku.</span><span class="sxs-lookup"><span data-stu-id="fbbd8-219">If the user requests a file with an unknown file type, Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="fbbd8-220">Jeśli żadne oprogramowanie pośredniczące obsługuje żądanie, zwracana jest odpowiedź *404 Nie znaleziono.*</span><span class="sxs-lookup"><span data-stu-id="fbbd8-220">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="fbbd8-221">Jeśli przeglądanie katalogów jest włączone, łącze do pliku jest wyświetlane na liście katalogów.</span><span class="sxs-lookup"><span data-stu-id="fbbd8-221">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="fbbd8-222">Poniższy kod umożliwia wyświetlanie nieznanych typów i renderuje nieznany plik jako obraz:</span><span class="sxs-lookup"><span data-stu-id="fbbd8-222">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](static-files/samples/1x/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

<span data-ttu-id="fbbd8-223">W poprzednim kodzie żądanie pliku o nieznanym typie zawartości jest zwracane jako obraz.</span><span class="sxs-lookup"><span data-stu-id="fbbd8-223">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="fbbd8-224">Włączenie [ServeUnknownFileTypes](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes) jest zagrożeniem bezpieczeństwa.</span><span class="sxs-lookup"><span data-stu-id="fbbd8-224">Enabling [ServeUnknownFileTypes](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes) is a security risk.</span></span> <span data-ttu-id="fbbd8-225">Jest domyślnie wyłączona, a jego użycie jest odradzane.</span><span class="sxs-lookup"><span data-stu-id="fbbd8-225">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="fbbd8-226">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) zapewnia bezpieczniejszą alternatywę dla obsługi plików z niestandardowymi rozszerzeniami.</span><span class="sxs-lookup"><span data-stu-id="fbbd8-226">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

## <a name="serve-files-from-multiple-locations"></a><span data-ttu-id="fbbd8-227">Obsługa plików z wielu lokalizacji</span><span class="sxs-lookup"><span data-stu-id="fbbd8-227">Serve files from multiple locations</span></span>

<span data-ttu-id="fbbd8-228">`UseStaticFiles`i `UseFileServer` domyślnie dostawca plików wskazujący na *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="fbbd8-228">`UseStaticFiles` and `UseFileServer` defaults to the file provider pointing at *wwwroot*.</span></span> <span data-ttu-id="fbbd8-229">Można podać dodatkowe wystąpienia `UseStaticFiles` `UseFileServer` i z innymi dostawcami plików do obsługi plików z innych lokalizacji.</span><span class="sxs-lookup"><span data-stu-id="fbbd8-229">You can provide additional instances of `UseStaticFiles` and `UseFileServer` with other file providers to serve files from other locations.</span></span> <span data-ttu-id="fbbd8-230">Aby uzyskać więcej informacji, zobacz [ten problem z githubem](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span><span class="sxs-lookup"><span data-stu-id="fbbd8-230">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/15578).</span></span>

### <a name="considerations"></a><span data-ttu-id="fbbd8-231">Zagadnienia do rozważenia</span><span class="sxs-lookup"><span data-stu-id="fbbd8-231">Considerations</span></span>

> [!WARNING]
> <span data-ttu-id="fbbd8-232">`UseDirectoryBrowser`i `UseStaticFiles` może wyciekać tajemnice.</span><span class="sxs-lookup"><span data-stu-id="fbbd8-232">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="fbbd8-233">Zaleca się wyłączenie przeglądania katalogów w produkcji.</span><span class="sxs-lookup"><span data-stu-id="fbbd8-233">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="fbbd8-234">Dokładnie sprawdź, które katalogi `UseStaticFiles` `UseDirectoryBrowser`są włączone za pośrednictwem lub .</span><span class="sxs-lookup"><span data-stu-id="fbbd8-234">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="fbbd8-235">Cały katalog i jego podkatasty stają się publicznie dostępne.</span><span class="sxs-lookup"><span data-stu-id="fbbd8-235">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="fbbd8-236">Przechowuj pliki odpowiednie do publicznego wyświetlania w dedykowanym katalogu, takie jak \* \<content_root>/wwwroot\*.</span><span class="sxs-lookup"><span data-stu-id="fbbd8-236">Store files suitable for serving to the public in a dedicated directory, such as *\<content_root>/wwwroot*.</span></span> <span data-ttu-id="fbbd8-237">Oddziel te pliki od widoków MVC, stron Razor (tylko 2.x), plików konfiguracyjnych itp.</span><span class="sxs-lookup"><span data-stu-id="fbbd8-237">Separate these files from MVC views, Razor Pages (2.x only), configuration files, etc.</span></span>

* <span data-ttu-id="fbbd8-238">Adresy URL zawartości `UseDirectoryBrowser` udostępnianej z `UseStaticFiles` i podlegają ograniczeniom dotyczącymi wrażliwości i znaków w podstawowym systemie plików.</span><span class="sxs-lookup"><span data-stu-id="fbbd8-238">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="fbbd8-239">Na przykład system Windows jest bez&mdash;uwzględniania wielkości liter macOS i Linux nie są.</span><span class="sxs-lookup"><span data-stu-id="fbbd8-239">For example, Windows is case insensitive&mdash;macOS and Linux aren't.</span></span>

* <span data-ttu-id="fbbd8-240">aplikacje ASP.NET Core hostowane w usługach IIS używają [modułu ASP.NET Core](xref:host-and-deploy/aspnet-core-module) do przekazywania wszystkich żądań do aplikacji, w tym żądań plików statycznych.</span><span class="sxs-lookup"><span data-stu-id="fbbd8-240">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="fbbd8-241">Nie jest używany statyczny program obsługi plików IIS.</span><span class="sxs-lookup"><span data-stu-id="fbbd8-241">The IIS static file handler isn't used.</span></span> <span data-ttu-id="fbbd8-242">Nie ma szans na obsługę żądań, zanim będą obsługiwane przez moduł.</span><span class="sxs-lookup"><span data-stu-id="fbbd8-242">It has no chance to handle requests before they're handled by the module.</span></span>

* <span data-ttu-id="fbbd8-243">Wykonaj następujące kroki w Menedżerze usług IIS, aby usunąć statyczny program obsługi plików usług IIS na poziomie serwera lub witryny sieci Web:</span><span class="sxs-lookup"><span data-stu-id="fbbd8-243">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="fbbd8-244">Przejdź do funkcji **Moduły.**</span><span class="sxs-lookup"><span data-stu-id="fbbd8-244">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="fbbd8-245">Wybierz **staticFileModule** na liście.</span><span class="sxs-lookup"><span data-stu-id="fbbd8-245">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="fbbd8-246">Kliknij **pozycję Usuń** na pasku bocznym **Akcje.**</span><span class="sxs-lookup"><span data-stu-id="fbbd8-246">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="fbbd8-247">Jeśli program obsługi plików statycznych IIS jest **włączony, a** ASP.NET modułu rdzenia jest niepoprawnie skonfigurowany, są obsługiwane pliki statyczne.</span><span class="sxs-lookup"><span data-stu-id="fbbd8-247">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="fbbd8-248">Dzieje się tak na przykład, jeśli plik *web.config* nie jest wdrożony.</span><span class="sxs-lookup"><span data-stu-id="fbbd8-248">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="fbbd8-249">Umieść pliki kodu (w tym *.cs* i *.cshtml)* poza [katalogiem głównym](xref:fundamentals/index#web-root)projektu aplikacji.</span><span class="sxs-lookup"><span data-stu-id="fbbd8-249">Place code files (including *.cs* and *.cshtml*) outside of the app project's [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="fbbd8-250">W związku z tym zostanie utworzona separacja logiczna między zawartością po stronie klienta aplikacji a kodem opartym na serwerze.</span><span class="sxs-lookup"><span data-stu-id="fbbd8-250">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="fbbd8-251">Zapobiega to wyciekaniu kodu po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="fbbd8-251">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fbbd8-252">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="fbbd8-252">Additional resources</span></span>

* [<span data-ttu-id="fbbd8-253">Oprogramowanie pośredniczące</span><span class="sxs-lookup"><span data-stu-id="fbbd8-253">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="fbbd8-254">Wprowadzenie do programu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fbbd8-254">Introduction to ASP.NET Core</span></span>](xref:index)
