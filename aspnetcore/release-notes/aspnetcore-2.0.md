---
title: Co nowego w ASP.NET Core 2.0
author: rick-anderson
description: Dowiedz się więcej o nowych funkcjach w ASP.NET Core 2.0.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: aspnetcore-2.0
ms.openlocfilehash: 5ca43bab1496aa9fda65282cbb0b1177ad8689eb
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667259"
---
# <a name="whats-new-in-aspnet-core-20"></a><span data-ttu-id="4c1f9-103">Co nowego w ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="4c1f9-103">What's new in ASP.NET Core 2.0</span></span>

<span data-ttu-id="4c1f9-104">W tym artykule przedstawiono najważniejsze zmiany w ASP.NET Core 2.0, z łączami do odpowiedniej dokumentacji.</span><span class="sxs-lookup"><span data-stu-id="4c1f9-104">This article highlights the most significant changes in ASP.NET Core 2.0, with links to relevant documentation.</span></span>

## <a name="razor-pages"></a><span data-ttu-id="4c1f9-105">Razor Pages</span><span class="sxs-lookup"><span data-stu-id="4c1f9-105">Razor Pages</span></span>

<span data-ttu-id="4c1f9-106">Razor Pages to nowa funkcja ASP.NET Core MVC, która sprawia, że kodowanie scenariuszy skoncentrowanych na stronach jest łatwiejsze i bardziej wydajne.</span><span class="sxs-lookup"><span data-stu-id="4c1f9-106">Razor Pages is a new feature of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.</span></span>

<span data-ttu-id="4c1f9-107">Aby uzyskać więcej informacji, zobacz wprowadzenie i samouczek:</span><span class="sxs-lookup"><span data-stu-id="4c1f9-107">For more information, see the introduction and tutorial:</span></span>

* [<span data-ttu-id="4c1f9-108">Wprowadzenie do produktu Razor Pages</span><span class="sxs-lookup"><span data-stu-id="4c1f9-108">Introduction to Razor Pages</span></span>](xref:razor-pages/index)
* [<span data-ttu-id="4c1f9-109">Wprowadzenie do korzystania ze stron Razor</span><span class="sxs-lookup"><span data-stu-id="4c1f9-109">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start)

## <a name="aspnet-core-metapackage"></a><span data-ttu-id="4c1f9-110">ASP.NET Rdzenie metapakiet</span><span class="sxs-lookup"><span data-stu-id="4c1f9-110">ASP.NET Core metapackage</span></span>

<span data-ttu-id="4c1f9-111">Nowy ASP.NET Core zawiera wszystkie pakiety wykonane i obsługiwane przez zespoły ASP.NET core core i entity framework core, wraz z ich wewnętrznych i zewnętrznych zależności.</span><span class="sxs-lookup"><span data-stu-id="4c1f9-111">A new ASP.NET Core metapackage includes all of the packages made and supported by the ASP.NET Core and Entity Framework Core teams, along with their internal and 3rd-party dependencies.</span></span> <span data-ttu-id="4c1f9-112">Nie musisz już wybierać poszczególnych funkcji ASP.NET Core według pakietu.</span><span class="sxs-lookup"><span data-stu-id="4c1f9-112">You no longer need to choose individual ASP.NET Core features by package.</span></span> <span data-ttu-id="4c1f9-113">Wszystkie funkcje są zawarte w pakiecie [Microsoft.AspNetCore.All.](https://www.nuget.org/packages/Microsoft.AspNetCore.All)</span><span class="sxs-lookup"><span data-stu-id="4c1f9-113">All features are included in the [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) package.</span></span> <span data-ttu-id="4c1f9-114">Szablony domyślne używają tego pakietu.</span><span class="sxs-lookup"><span data-stu-id="4c1f9-114">The default templates use this package.</span></span>

<span data-ttu-id="4c1f9-115">Aby uzyskać więcej informacji, zobacz [Microsoft.AspNetCore.All metapackage dla ASP.NET Core 2.0](xref:fundamentals/metapackage).</span><span class="sxs-lookup"><span data-stu-id="4c1f9-115">For more information, see [Microsoft.AspNetCore.All metapackage for ASP.NET Core 2.0](xref:fundamentals/metapackage).</span></span>

## <a name="runtime-store"></a><span data-ttu-id="4c1f9-116">Magazyn środowiska uruchomieniowego</span><span class="sxs-lookup"><span data-stu-id="4c1f9-116">Runtime Store</span></span>

<span data-ttu-id="4c1f9-117">Aplikacje korzystające `Microsoft.AspNetCore.All` z metapakiety automatycznie korzystają z nowego magazynu wykonywania .NET Core.</span><span class="sxs-lookup"><span data-stu-id="4c1f9-117">Applications that use the `Microsoft.AspNetCore.All` metapackage automatically take advantage of the new .NET Core Runtime Store.</span></span> <span data-ttu-id="4c1f9-118">Sklep zawiera wszystkie zasoby środowiska wykonawczego potrzebne do uruchamiania aplikacji ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="4c1f9-118">The Store contains all the runtime assets needed to run ASP.NET Core 2.0 applications.</span></span> <span data-ttu-id="4c1f9-119">Korzystając z `Microsoft.AspNetCore.All` metapakietu, żadne zasoby z pakietów Core NuGet, do których odwołuje się odwołuje się ASP.NET Core NuGet, nie są wdrażane z aplikacją, ponieważ znajdują się one już w systemie docelowym.</span><span class="sxs-lookup"><span data-stu-id="4c1f9-119">When you use the `Microsoft.AspNetCore.All` metapackage, no assets from the referenced ASP.NET Core NuGet packages are deployed with the application because they already reside on the target system.</span></span> <span data-ttu-id="4c1f9-120">Zasoby w Magazynie Środowiska wykonawczego są również wstępnie kompilowane w celu skrócenia czasu uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4c1f9-120">The assets in the Runtime Store are also precompiled to improve application startup time.</span></span>

<span data-ttu-id="4c1f9-121">Aby uzyskać więcej informacji, zobacz [Magazyn środowiska uruchomieniowego](/dotnet/core/deploying/runtime-store)</span><span class="sxs-lookup"><span data-stu-id="4c1f9-121">For more information, see [Runtime store](/dotnet/core/deploying/runtime-store)</span></span>

## <a name="net-standard-20"></a><span data-ttu-id="4c1f9-122">.NET Standard 2.0</span><span class="sxs-lookup"><span data-stu-id="4c1f9-122">.NET Standard 2.0</span></span>

<span data-ttu-id="4c1f9-123">ASP.NET core 2.0 docelowy pakietów .NET Standard 2.0.</span><span class="sxs-lookup"><span data-stu-id="4c1f9-123">The ASP.NET Core 2.0 packages target .NET Standard 2.0.</span></span> <span data-ttu-id="4c1f9-124">Do pakietów mogą odwoływać się inne biblioteki .NET Standard 2.0 i mogą być uruchamiane w implementacjach .NET Standard 2.0 .NET, w tym .NET Core 2.0 i .NET Framework 4.6.1.</span><span class="sxs-lookup"><span data-stu-id="4c1f9-124">The packages can be referenced by other .NET Standard 2.0 libraries, and they can run on .NET Standard 2.0-compliant implementations of .NET, including .NET Core 2.0 and .NET Framework 4.6.1.</span></span> 

<span data-ttu-id="4c1f9-125">Metapakiet `Microsoft.AspNetCore.All` jest przeznaczony tylko dla platformy .NET Core 2.0, ponieważ jest przeznaczony do użycia w magazynie wykonywania .NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="4c1f9-125">The `Microsoft.AspNetCore.All` metapackage targets .NET Core 2.0 only, because it's intended to be used with the .NET Core 2.0 Runtime Store.</span></span>

## <a name="configuration-update"></a><span data-ttu-id="4c1f9-126">Aktualizacja konfiguracji</span><span class="sxs-lookup"><span data-stu-id="4c1f9-126">Configuration update</span></span>

<span data-ttu-id="4c1f9-127">Wystąpienie `IConfiguration` jest domyślnie dodawane do kontenera usług w ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="4c1f9-127">An `IConfiguration` instance is added to the services container by default in ASP.NET Core 2.0.</span></span> <span data-ttu-id="4c1f9-128">`IConfiguration`w kontenerze usług ułatwia aplikacjom pobieranie wartości konfiguracji z kontenera.</span><span class="sxs-lookup"><span data-stu-id="4c1f9-128">`IConfiguration` in the services container makes it easier for applications to retrieve configuration values from the container.</span></span>

<span data-ttu-id="4c1f9-129">Aby uzyskać informacje o stanie planowanej dokumentacji, zobacz [problem z githubem](https://github.com/dotnet/AspNetCore.Docs/issues/3387).</span><span class="sxs-lookup"><span data-stu-id="4c1f9-129">For information about the status of planned documentation, see the [GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/3387).</span></span>

## <a name="logging-update"></a><span data-ttu-id="4c1f9-130">Aktualizacja rejestrowania</span><span class="sxs-lookup"><span data-stu-id="4c1f9-130">Logging update</span></span>

<span data-ttu-id="4c1f9-131">W ASP.NET Core 2.0 rejestrowanie jest domyślnie włączone do systemu iniekcji zależności (DI).</span><span class="sxs-lookup"><span data-stu-id="4c1f9-131">In ASP.NET Core 2.0, logging is incorporated into the dependency injection (DI) system by default.</span></span> <span data-ttu-id="4c1f9-132">Dodaj dostawców i konfigurujesz filtrowanie w pliku *Program.cs,* a nie w pliku *Startup.cs.*</span><span class="sxs-lookup"><span data-stu-id="4c1f9-132">You add providers and configure filtering in the *Program.cs* file instead of in the *Startup.cs* file.</span></span> <span data-ttu-id="4c1f9-133">Wartość domyślna `ILoggerFactory` obsługuje filtrowanie w sposób umożliwiający korzystanie z jednego elastycznego podejścia zarówno do filtrowania między dostawcami, jak i filtrowania określonego dostawcy.</span><span class="sxs-lookup"><span data-stu-id="4c1f9-133">And the default `ILoggerFactory` supports filtering in a way that lets you use one flexible approach for both cross-provider filtering and specific-provider filtering.</span></span>

<span data-ttu-id="4c1f9-134">Aby uzyskać więcej informacji, zobacz [Wprowadzenie do rejestrowania](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="4c1f9-134">For more information, see [Introduction to Logging](xref:fundamentals/logging/index).</span></span>

## <a name="authentication-update"></a><span data-ttu-id="4c1f9-135">Aktualizacja uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="4c1f9-135">Authentication update</span></span>

<span data-ttu-id="4c1f9-136">Nowy model uwierzytelniania ułatwia konfigurowanie uwierzytelniania dla aplikacji przy użyciu DI.</span><span class="sxs-lookup"><span data-stu-id="4c1f9-136">A new authentication model makes it easier to configure authentication for an application using DI.</span></span>

<span data-ttu-id="4c1f9-137">Dostępne są nowe szablony do konfigurowania uwierzytelniania aplikacji sieci Web i internetowych interfejsów API przy użyciu [usługi Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/).</span><span class="sxs-lookup"><span data-stu-id="4c1f9-137">New templates are available for configuring authentication for web apps and web APIs using [Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/).</span></span>

<span data-ttu-id="4c1f9-138">Aby uzyskać informacje o stanie planowanej dokumentacji, zobacz [problem z githubem](https://github.com/dotnet/AspNetCore.Docs/issues/3054).</span><span class="sxs-lookup"><span data-stu-id="4c1f9-138">For information about the status of planned documentation, see the [GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/3054).</span></span>

## <a name="identity-update"></a><span data-ttu-id="4c1f9-139">Aktualizacja tożsamości</span><span class="sxs-lookup"><span data-stu-id="4c1f9-139">Identity update</span></span>

<span data-ttu-id="4c1f9-140">Ułatwiliśmy tworzenie bezpiecznych interfejsów API sieci Web przy użyciu tożsamości w ASP.NET Core 2.0.</span><span class="sxs-lookup"><span data-stu-id="4c1f9-140">We've made it easier to build secure web APIs using Identity in ASP.NET Core 2.0.</span></span> <span data-ttu-id="4c1f9-141">Tokeny dostępu można uzyskać w celu uzyskania dostępu do internetowych interfejsów API przy użyciu [biblioteki uwierzytelniania firmy Microsoft (MSAL).](https://www.nuget.org/packages/Microsoft.Identity.Client)</span><span class="sxs-lookup"><span data-stu-id="4c1f9-141">You can acquire access tokens for accessing your web APIs using the [Microsoft Authentication Library (MSAL)](https://www.nuget.org/packages/Microsoft.Identity.Client).</span></span>

<span data-ttu-id="4c1f9-142">Aby uzyskać więcej informacji na temat zmian uwierzytelniania w 2.0, zobacz następujące zasoby:</span><span class="sxs-lookup"><span data-stu-id="4c1f9-142">For more information on authentication changes in 2.0, see the following resources:</span></span>

* [<span data-ttu-id="4c1f9-143">Potwierdzenie konta i odzyskiwanie hasła w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4c1f9-143">Account confirmation and password recovery in ASP.NET Core</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="4c1f9-144">Włącz generowanie kodu QR dla aplikacji uwierzytelniającego w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4c1f9-144">Enable QR Code generation for authenticator apps in ASP.NET Core</span></span>](xref:security/authentication/identity-enable-qrcodes)
* [<span data-ttu-id="4c1f9-145">Migrowanie uwierzytelniania i tożsamości w celu ASP.NET rdzenia 2.0</span><span class="sxs-lookup"><span data-stu-id="4c1f9-145">Migrate Authentication and Identity to ASP.NET Core 2.0</span></span>](xref:migration/1x-to-2x/identity-2x)

## <a name="spa-templates"></a><span data-ttu-id="4c1f9-146">Szablony SPA</span><span class="sxs-lookup"><span data-stu-id="4c1f9-146">SPA templates</span></span>

<span data-ttu-id="4c1f9-147">Dostępne są szablony projektów aplikacji jednostronicowej (SPA) dla angular, Aurelia, Knockout.js, React.js i React.js z Redux.</span><span class="sxs-lookup"><span data-stu-id="4c1f9-147">Single Page Application (SPA) project templates for Angular, Aurelia, Knockout.js, React.js, and React.js with Redux are available.</span></span> <span data-ttu-id="4c1f9-148">Szablon kątowy został zaktualizowany do Angular 4.</span><span class="sxs-lookup"><span data-stu-id="4c1f9-148">The Angular template has been updated to Angular 4.</span></span> <span data-ttu-id="4c1f9-149">Szablony Angular i React są domyślnie dostępne; aby uzyskać informacje o tym, jak uzyskać inne szablony, zobacz [Tworzenie nowego projektu SPA](xref:client-side/spa-services#create-a-new-project).</span><span class="sxs-lookup"><span data-stu-id="4c1f9-149">The Angular and React templates are available by default; for information about how to get the other templates, see [Create a new SPA project](xref:client-side/spa-services#create-a-new-project).</span></span> <span data-ttu-id="4c1f9-150">Aby uzyskać informacje na temat tworzenia SPA w <xref:client-side/spa-services>ASP.NET Core, zobacz .</span><span class="sxs-lookup"><span data-stu-id="4c1f9-150">For information about how to build a SPA in ASP.NET Core, see <xref:client-side/spa-services>.</span></span>

## <a name="kestrel-improvements"></a><span data-ttu-id="4c1f9-151">Ulepszenia pustułki</span><span class="sxs-lookup"><span data-stu-id="4c1f9-151">Kestrel improvements</span></span>

<span data-ttu-id="4c1f9-152">Serwer internetowy Kestrel ma nowe funkcje, które sprawiają, że jest bardziej odpowiedni jako serwer internetowy.</span><span class="sxs-lookup"><span data-stu-id="4c1f9-152">The Kestrel web server has new features that make it more suitable as an Internet-facing server.</span></span> <span data-ttu-id="4c1f9-153">W nowej `KestrelServerOptions` `Limits` właściwości klasy dodano wiele opcji konfiguracji ograniczeń serwera.</span><span class="sxs-lookup"><span data-stu-id="4c1f9-153">A number of server constraint configuration options are added in the `KestrelServerOptions` class's new `Limits` property.</span></span> <span data-ttu-id="4c1f9-154">Dodaj limity dla następujących elementów:</span><span class="sxs-lookup"><span data-stu-id="4c1f9-154">Add limits for the following:</span></span>

* <span data-ttu-id="4c1f9-155">Maksymalna liczba połączeń klientów</span><span class="sxs-lookup"><span data-stu-id="4c1f9-155">Maximum client connections</span></span>
* <span data-ttu-id="4c1f9-156">Maksymalny rozmiar treści żądania</span><span class="sxs-lookup"><span data-stu-id="4c1f9-156">Maximum request body size</span></span>
* <span data-ttu-id="4c1f9-157">Minimalna szybkość danych treści żądania</span><span class="sxs-lookup"><span data-stu-id="4c1f9-157">Minimum request body data rate</span></span>

<span data-ttu-id="4c1f9-158">Aby uzyskać więcej informacji, zobacz [Kestrel web server implementation in ASP.NET Core](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="4c1f9-158">For more information, see [Kestrel web server implementation in ASP.NET Core](xref:fundamentals/servers/kestrel).</span></span>

## <a name="weblistener-renamed-to-httpsys"></a><span data-ttu-id="4c1f9-159">Nazwa WebListener na HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="4c1f9-159">WebListener renamed to HTTP.sys</span></span>

<span data-ttu-id="4c1f9-160">Pakiety `Microsoft.AspNetCore.Server.WebListener` `Microsoft.Net.Http.Server` i zostały połączone w `Microsoft.AspNetCore.Server.HttpSys`nowy pakiet .</span><span class="sxs-lookup"><span data-stu-id="4c1f9-160">The packages `Microsoft.AspNetCore.Server.WebListener` and `Microsoft.Net.Http.Server` have been merged into a new package `Microsoft.AspNetCore.Server.HttpSys`.</span></span> <span data-ttu-id="4c1f9-161">Przestrzenie nazw zostały zaktualizowane, aby były zgodne.</span><span class="sxs-lookup"><span data-stu-id="4c1f9-161">The namespaces have been updated to match.</span></span>

<span data-ttu-id="4c1f9-162">Aby uzyskać więcej informacji, zobacz [HTTP.sys implementacji serwera sieci web w ASP.NET Core](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="4c1f9-162">For more information, see [HTTP.sys web server implementation in ASP.NET Core](xref:fundamentals/servers/httpsys).</span></span>

## <a name="enhanced-http-header-support"></a><span data-ttu-id="4c1f9-163">Ulepszona obsługa nagłówka HTTP</span><span class="sxs-lookup"><span data-stu-id="4c1f9-163">Enhanced HTTP header support</span></span>

<span data-ttu-id="4c1f9-164">Podczas korzystania z MVC `FileStreamResult` `FileContentResult`do przesyłania lub , masz `ETag` teraz `LastModified` możliwość ustawienia lub daty na treści, które przesyłane.</span><span class="sxs-lookup"><span data-stu-id="4c1f9-164">When using MVC to transmit a `FileStreamResult` or a `FileContentResult`, you now have the option to set an `ETag` or a `LastModified` date on the content you transmit.</span></span> <span data-ttu-id="4c1f9-165">Można ustawić te wartości na zwróconej zawartości z kodem podobnym do następującego:</span><span class="sxs-lookup"><span data-stu-id="4c1f9-165">You can set these values on the returned content with code similar to the following:</span></span>

```csharp
var data = Encoding.UTF8.GetBytes("This is a sample text from a binary array");
var entityTag = new EntityTagHeaderValue("\"MyCalculatedEtagValue\"");
return File(data, "text/plain", "downloadName.txt", lastModified: DateTime.UtcNow.AddSeconds(-5), entityTag: entityTag);
```

<span data-ttu-id="4c1f9-166">Plik zwrócony odwiedzającym ma odpowiednie nagłówki `ETag` HTTP `LastModified` dla i wartości.</span><span class="sxs-lookup"><span data-stu-id="4c1f9-166">The file returned to your visitors has the appropriate HTTP headers for the `ETag` and `LastModified` values.</span></span>

<span data-ttu-id="4c1f9-167">Jeśli użytkownik aplikacji żąda zawartości z nagłówkiem żądanie zakresu, ASP.NET Core rozpoznaje żądanie i obsługuje nagłówek.</span><span class="sxs-lookup"><span data-stu-id="4c1f9-167">If an application visitor requests content with a Range Request header, ASP.NET Core recognizes the request and handles the header.</span></span> <span data-ttu-id="4c1f9-168">Jeśli żądana zawartość może być częściowo dostarczona, ASP.NET Core odpowiednio pomija i zwraca tylko żądany zestaw bajtów.</span><span class="sxs-lookup"><span data-stu-id="4c1f9-168">If the requested content can be partially delivered, ASP.NET Core appropriately skips and returns just the requested set of bytes.</span></span> <span data-ttu-id="4c1f9-169">Nie trzeba zapisywać żadnych specjalnych programów obsługi do metod, aby dostosować lub obsługiwać tę funkcję; jest automatycznie obsługiwany za Ciebie.</span><span class="sxs-lookup"><span data-stu-id="4c1f9-169">You don't need to write any special handlers into your methods to adapt or handle this feature; it's automatically handled for you.</span></span>

## <a name="hosting-startup-and-application-insights"></a><span data-ttu-id="4c1f9-170">Hostowanie uruchamiania i usługi Application Insights</span><span class="sxs-lookup"><span data-stu-id="4c1f9-170">Hosting startup and Application Insights</span></span>

<span data-ttu-id="4c1f9-171">Środowiska hostingowe można teraz wstrzyknąć dodatkowe zależności pakietów i wykonać kod podczas uruchamiania aplikacji, bez konieczności aplikacji jawnie wziąć zależność lub wywołać żadnych metod.</span><span class="sxs-lookup"><span data-stu-id="4c1f9-171">Hosting environments can now inject extra package dependencies and execute code during application startup, without the application needing to explicitly take a dependency or call any methods.</span></span> <span data-ttu-id="4c1f9-172">Ta funkcja może służyć do umożliwienia niektórych środowisk do "light-up" funkcje unikatowe dla tego środowiska bez aplikacji konieczności wiedzieć z wyprzedzeniem.</span><span class="sxs-lookup"><span data-stu-id="4c1f9-172">This feature can be used to enable certain environments to "light-up" features unique to that environment without the application needing to know ahead of time.</span></span> 

<span data-ttu-id="4c1f9-173">W ASP.NET Core 2.0 ta funkcja jest używana do automatycznego włączania diagnostyki usługi Application Insights podczas debugowania w programie Visual Studio i (po włączeniu) podczas uruchamiania w usłudze Azure App Services.</span><span class="sxs-lookup"><span data-stu-id="4c1f9-173">In ASP.NET Core 2.0, this feature is used to automatically enable Application Insights diagnostics when debugging in Visual Studio and (after opting in) when running in Azure App Services.</span></span> <span data-ttu-id="4c1f9-174">W rezultacie szablony projektu nie są już domyślnie dodawanymi pakietami i kodem usługi Application Insights.</span><span class="sxs-lookup"><span data-stu-id="4c1f9-174">As a result, the project templates no longer add Application Insights packages and code by default.</span></span>

<span data-ttu-id="4c1f9-175">Aby uzyskać informacje o stanie planowanej dokumentacji, zobacz [problem z githubem](https://github.com/dotnet/AspNetCore.Docs/issues/3389).</span><span class="sxs-lookup"><span data-stu-id="4c1f9-175">For information about the status of planned documentation, see the [GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/3389).</span></span>

## <a name="automatic-use-of-anti-forgery-tokens"></a><span data-ttu-id="4c1f9-176">Automatyczne używanie tokenów zapobiegawczych fałszerskości</span><span class="sxs-lookup"><span data-stu-id="4c1f9-176">Automatic use of anti-forgery tokens</span></span>

<span data-ttu-id="4c1f9-177">ASP.NET Core zawsze pomagał zawartości kodowania HTML domyślnie, ale z nową wersją krok jest podejmowany, aby zapobiec atakom xsrf (cross-site request fałszerza).</span><span class="sxs-lookup"><span data-stu-id="4c1f9-177">ASP.NET Core has always helped HTML-encode content by default, but with the new version an extra step is taken to help prevent cross-site request forgery (XSRF) attacks.</span></span> <span data-ttu-id="4c1f9-178">ASP.NET Core będzie teraz domyślnie emitować tokeny antyzwiązania z fałszerstwem i sprawdzać ich poprawność w akcjach i stronach post formularza bez dodatkowej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="4c1f9-178">ASP.NET Core will now emit anti-forgery tokens by default and validate them on form POST actions and pages without extra configuration.</span></span>

<span data-ttu-id="4c1f9-179">Aby uzyskać więcej informacji, zobacz [Zapobieganie atakom xsrf/CSRF (Cross-Site Request Fałszerstwa" ( XSRF/CSRF) (Zapobieganie atakom wielomiejscowym).](xref:security/anti-request-forgery)</span><span class="sxs-lookup"><span data-stu-id="4c1f9-179">For more information, see [Prevent Cross-Site Request Forgery (XSRF/CSRF) attacks](xref:security/anti-request-forgery).</span></span>

## <a name="automatic-precompilation"></a><span data-ttu-id="4c1f9-180">Automatyczna wstępna kompilacja</span><span class="sxs-lookup"><span data-stu-id="4c1f9-180">Automatic precompilation</span></span>

<span data-ttu-id="4c1f9-181">Razor view pre-compilation jest domyślnie włączona podczas publikowania, co zmniejsza rozmiar wydruku i czas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="4c1f9-181">Razor view pre-compilation is enabled during publish by default, reducing the publish output size and application startup time.</span></span>

<span data-ttu-id="4c1f9-182">Aby uzyskać więcej informacji, zobacz [Kompilacja i wstępna kompilacja widoku Razor w ASP.NET Core](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="4c1f9-182">For more information, see [Razor view compilation and precompilation in ASP.NET Core](xref:mvc/views/view-compilation).</span></span>

## <a name="razor-support-for-c-71"></a><span data-ttu-id="4c1f9-183">Obsługa maszynki do golenia dla języka C# 7.1</span><span class="sxs-lookup"><span data-stu-id="4c1f9-183">Razor support for C# 7.1</span></span>

<span data-ttu-id="4c1f9-184">Aparat widoku Razor został zaktualizowany do pracy z nowym kompilatorem Roslyn.</span><span class="sxs-lookup"><span data-stu-id="4c1f9-184">The Razor view engine has been updated to work with the new Roslyn compiler.</span></span> <span data-ttu-id="4c1f9-185">Obejmuje to obsługę funkcji języka C# 7.1, takich jak domyślne wyrażenia, wywnioskowane nazwy krotek i dopasowywanie wzorców z rodzajami ogólnymi.</span><span class="sxs-lookup"><span data-stu-id="4c1f9-185">That includes support for C# 7.1 features like Default Expressions, Inferred Tuple Names, and Pattern-Matching with Generics.</span></span> <span data-ttu-id="4c1f9-186">Aby użyć języka C# 7.1 w projekcie, dodaj następującą właściwość w pliku projektu, a następnie ponownie załaduj rozwiązanie:</span><span class="sxs-lookup"><span data-stu-id="4c1f9-186">To use C# 7.1 in your project, add the following property in your project file and then reload the solution:</span></span>

```xml
<LangVersion>latest</LangVersion>
```

<span data-ttu-id="4c1f9-187">Aby uzyskać informacje o stanie funkcji języka C# 7.1, zobacz [repozytorium Roslyn GitHub](https://github.com/dotnet/roslyn/blob/master/docs/Language%20Feature%20Status.md).</span><span class="sxs-lookup"><span data-stu-id="4c1f9-187">For information about the status of C# 7.1 features, see [the Roslyn GitHub repository](https://github.com/dotnet/roslyn/blob/master/docs/Language%20Feature%20Status.md).</span></span>

## <a name="other-documentation-updates-for-20"></a><span data-ttu-id="4c1f9-188">Inne aktualizacje dokumentacji dla 2.0</span><span class="sxs-lookup"><span data-stu-id="4c1f9-188">Other documentation updates for 2.0</span></span>

* [<span data-ttu-id="4c1f9-189">Profile publikowania w programie Visual Studio dla ASP.NET wdrożenia aplikacji Core</span><span class="sxs-lookup"><span data-stu-id="4c1f9-189">Visual Studio publish profiles for ASP.NET Core app deployment</span></span>](xref:host-and-deploy/visual-studio-publish-profiles)
* [<span data-ttu-id="4c1f9-190">Zarządzanie kluczami</span><span class="sxs-lookup"><span data-stu-id="4c1f9-190">Key Management</span></span>](xref:security/data-protection/implementation/key-management)
* [<span data-ttu-id="4c1f9-191">Konfigurowanie uwierzytelniania na Facebooku</span><span class="sxs-lookup"><span data-stu-id="4c1f9-191">Configure Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="4c1f9-192">Konfigurowanie uwierzytelniania twitterowego</span><span class="sxs-lookup"><span data-stu-id="4c1f9-192">Configure Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="4c1f9-193">Konfigurowanie uwierzytelniania Google</span><span class="sxs-lookup"><span data-stu-id="4c1f9-193">Configure Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="4c1f9-194">Konfigurowanie uwierzytelniania konta Microsoft</span><span class="sxs-lookup"><span data-stu-id="4c1f9-194">Configure Microsoft Account authentication</span></span>](xref:security/authentication/microsoft-logins)

## <a name="migration-guidance"></a><span data-ttu-id="4c1f9-195">Wskazówki dotyczące migracji</span><span class="sxs-lookup"><span data-stu-id="4c1f9-195">Migration guidance</span></span>

<span data-ttu-id="4c1f9-196">Aby uzyskać wskazówki dotyczące migracji aplikacji ASP.NET Core 1.x do ASP.NET Core 2.0, zobacz następujące zasoby:</span><span class="sxs-lookup"><span data-stu-id="4c1f9-196">For guidance on how to migrate ASP.NET Core 1.x applications to ASP.NET Core 2.0, see the following resources:</span></span>

* [<span data-ttu-id="4c1f9-197">Migracja z ASP.NET Core 1.x do ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="4c1f9-197">Migrate from ASP.NET Core 1.x to ASP.NET Core 2.0</span></span>](xref:migration/1x-to-2x/index)
* [<span data-ttu-id="4c1f9-198">Migrowanie uwierzytelniania i tożsamości w celu ASP.NET rdzenia 2.0</span><span class="sxs-lookup"><span data-stu-id="4c1f9-198">Migrate Authentication and Identity to ASP.NET Core 2.0</span></span>](xref:migration/1x-to-2x/identity-2x)

## <a name="additional-information"></a><span data-ttu-id="4c1f9-199">Dodatkowe informacje</span><span class="sxs-lookup"><span data-stu-id="4c1f9-199">Additional Information</span></span>

<span data-ttu-id="4c1f9-200">Aby uzyskać pełną listę zmian, zobacz [ASP.NET Informacje o wersji Core 2.0](https://github.com/dotnet/aspnetcore/releases/tag/2.0.0).</span><span class="sxs-lookup"><span data-stu-id="4c1f9-200">For the complete list of changes, see the [ASP.NET Core 2.0 Release Notes](https://github.com/dotnet/aspnetcore/releases/tag/2.0.0).</span></span>

<span data-ttu-id="4c1f9-201">Aby połączyć się z postępami i planami zespołu programistów ASP.NET Core, dostroić się do [ASP.NET Standup społeczności](https://live.asp.net/).</span><span class="sxs-lookup"><span data-stu-id="4c1f9-201">To connect with the ASP.NET Core development team's progress and plans, tune in to the [ASP.NET Community Standup](https://live.asp.net/).</span></span>
