---
title: Co nowego w ASP.NET Core 2,0
author: rick-anderson
description: Dowiedz się więcej o nowych funkcjach w ASP.NET Core 2,0.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: aspnetcore-2.0
ms.openlocfilehash: 1ec51bf054f1f0d60ed27ee280f436e6d4c8c4b0
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021200"
---
# <a name="whats-new-in-aspnet-core-20"></a><span data-ttu-id="dda81-103">Co nowego w ASP.NET Core 2,0</span><span class="sxs-lookup"><span data-stu-id="dda81-103">What's new in ASP.NET Core 2.0</span></span>

<span data-ttu-id="dda81-104">W tym artykule przedstawiono najbardziej znaczące zmiany w ASP.NET Core 2,0 z linkami do odpowiedniej dokumentacji.</span><span class="sxs-lookup"><span data-stu-id="dda81-104">This article highlights the most significant changes in ASP.NET Core 2.0, with links to relevant documentation.</span></span>

## <a name="no-locrazor-pages"></a><span data-ttu-id="dda81-105">RazorPage</span><span class="sxs-lookup"><span data-stu-id="dda81-105">Razor Pages</span></span>

<span data-ttu-id="dda81-106">RazorStrony są nową funkcją ASP.NET Core MVC, która sprawia, że kodowanie scenariuszy ukierunkowanych na strony jest łatwiejsze i bardziej produktywne.</span><span class="sxs-lookup"><span data-stu-id="dda81-106">Razor Pages is a new feature of ASP.NET Core MVC that makes coding page-focused scenarios easier and more productive.</span></span>

<span data-ttu-id="dda81-107">Aby uzyskać więcej informacji, zobacz Wprowadzenie i samouczek:</span><span class="sxs-lookup"><span data-stu-id="dda81-107">For more information, see the introduction and tutorial:</span></span>

* [<span data-ttu-id="dda81-108">Wprowadzenie do Razor stron</span><span class="sxs-lookup"><span data-stu-id="dda81-108">Introduction to Razor Pages</span></span>](xref:razor-pages/index)
* [<span data-ttu-id="dda81-109">Wprowadzenie do Razor stron</span><span class="sxs-lookup"><span data-stu-id="dda81-109">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start)

## <a name="aspnet-core-metapackage"></a><span data-ttu-id="dda81-110">ASP.NET Core, pakiet</span><span class="sxs-lookup"><span data-stu-id="dda81-110">ASP.NET Core metapackage</span></span>

<span data-ttu-id="dda81-111">Nowy ASP.NET Core pakietem zawierającym wszystkie pakiety wykonane i obsługiwane przez zespoły ASP.NET Core i Entity Framework Core wraz z ich wewnętrznymi i zewnętrznymi zależnościami.</span><span class="sxs-lookup"><span data-stu-id="dda81-111">A new ASP.NET Core metapackage includes all of the packages made and supported by the ASP.NET Core and Entity Framework Core teams, along with their internal and 3rd-party dependencies.</span></span> <span data-ttu-id="dda81-112">Nie trzeba już wybierać poszczególnych funkcji ASP.NET Core według pakietu.</span><span class="sxs-lookup"><span data-stu-id="dda81-112">You no longer need to choose individual ASP.NET Core features by package.</span></span> <span data-ttu-id="dda81-113">Wszystkie funkcje są zawarte w pakiecie [Microsoft. AspNetCore. All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) .</span><span class="sxs-lookup"><span data-stu-id="dda81-113">All features are included in the [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) package.</span></span> <span data-ttu-id="dda81-114">Szablony domyślne używają tego pakietu.</span><span class="sxs-lookup"><span data-stu-id="dda81-114">The default templates use this package.</span></span>

<span data-ttu-id="dda81-115">Aby uzyskać więcej informacji, zobacz [Microsoft. AspNetCore. All pakietu dla ASP.NET Core 2,0](xref:fundamentals/metapackage).</span><span class="sxs-lookup"><span data-stu-id="dda81-115">For more information, see [Microsoft.AspNetCore.All metapackage for ASP.NET Core 2.0](xref:fundamentals/metapackage).</span></span>

## <a name="runtime-store"></a><span data-ttu-id="dda81-116">Magazyn środowiska uruchomieniowego</span><span class="sxs-lookup"><span data-stu-id="dda81-116">Runtime Store</span></span>

<span data-ttu-id="dda81-117">Aplikacje korzystające z `Microsoft.AspNetCore.All` pakietu metadanych automatycznie wykorzystują nowy magazyn środowiska uruchomieniowego platformy .NET Core.</span><span class="sxs-lookup"><span data-stu-id="dda81-117">Applications that use the `Microsoft.AspNetCore.All` metapackage automatically take advantage of the new .NET Core Runtime Store.</span></span> <span data-ttu-id="dda81-118">Magazyn zawiera wszystkie zasoby środowiska uruchomieniowego potrzebne do uruchamiania aplikacji ASP.NET Core 2,0.</span><span class="sxs-lookup"><span data-stu-id="dda81-118">The Store contains all the runtime assets needed to run ASP.NET Core 2.0 applications.</span></span> <span data-ttu-id="dda81-119">W przypadku korzystania z `Microsoft.AspNetCore.All` pakietubinding nie są wdrażane żadne zasoby z przywoływanych ASP.NET Core pakietów NuGet z aplikacją, ponieważ znajdują się one już w systemie docelowym.</span><span class="sxs-lookup"><span data-stu-id="dda81-119">When you use the `Microsoft.AspNetCore.All` metapackage, no assets from the referenced ASP.NET Core NuGet packages are deployed with the application because they already reside on the target system.</span></span> <span data-ttu-id="dda81-120">Elementy zawartości w magazynie środowiska uruchomieniowego są również wstępnie skompilowane w celu poprawienia czasu uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="dda81-120">The assets in the Runtime Store are also precompiled to improve application startup time.</span></span>

<span data-ttu-id="dda81-121">Aby uzyskać więcej informacji, zobacz [Magazyn środowiska uruchomieniowego](/dotnet/core/deploying/runtime-store)</span><span class="sxs-lookup"><span data-stu-id="dda81-121">For more information, see [Runtime store](/dotnet/core/deploying/runtime-store)</span></span>

## <a name="net-standard-20"></a><span data-ttu-id="dda81-122">.NET Standard 2,0</span><span class="sxs-lookup"><span data-stu-id="dda81-122">.NET Standard 2.0</span></span>

<span data-ttu-id="dda81-123">.NET Standard 2,0 pakietów ASP.NET Core 2,0.</span><span class="sxs-lookup"><span data-stu-id="dda81-123">The ASP.NET Core 2.0 packages target .NET Standard 2.0.</span></span> <span data-ttu-id="dda81-124">Do pakietów mogą odwoływać się inne .NET Standard biblioteki 2,0, które mogą być uruchamiane na .NET Standard 2,0 zgodnych implementacjach platformy .NET, w tym .NET Core 2,0 i .NET Framework 4.6.1.</span><span class="sxs-lookup"><span data-stu-id="dda81-124">The packages can be referenced by other .NET Standard 2.0 libraries, and they can run on .NET Standard 2.0-compliant implementations of .NET, including .NET Core 2.0 and .NET Framework 4.6.1.</span></span> 

<span data-ttu-id="dda81-125">`Microsoft.AspNetCore.All`Pakietem docelowym jest tylko .net core 2,0, ponieważ jest on przeznaczony do użycia z magazynem środowiska uruchomieniowego programu .net core 2,0.</span><span class="sxs-lookup"><span data-stu-id="dda81-125">The `Microsoft.AspNetCore.All` metapackage targets .NET Core 2.0 only, because it's intended to be used with the .NET Core 2.0 Runtime Store.</span></span>

## <a name="configuration-update"></a><span data-ttu-id="dda81-126">Aktualizacja konfiguracji</span><span class="sxs-lookup"><span data-stu-id="dda81-126">Configuration update</span></span>

<span data-ttu-id="dda81-127">`IConfiguration`Wystąpienie jest domyślnie dodawane do kontenera usługi w ASP.NET Core 2,0.</span><span class="sxs-lookup"><span data-stu-id="dda81-127">An `IConfiguration` instance is added to the services container by default in ASP.NET Core 2.0.</span></span> <span data-ttu-id="dda81-128">`IConfiguration`w kontenerze usługi Usługa ułatwia aplikacjom pobieranie wartości konfiguracyjnych z kontenera.</span><span class="sxs-lookup"><span data-stu-id="dda81-128">`IConfiguration` in the services container makes it easier for applications to retrieve configuration values from the container.</span></span>

<span data-ttu-id="dda81-129">Aby uzyskać informacje o stanie planowanej dokumentacji, zobacz problem z usługą [GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/3387).</span><span class="sxs-lookup"><span data-stu-id="dda81-129">For information about the status of planned documentation, see the [GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/3387).</span></span>

## <a name="logging-update"></a><span data-ttu-id="dda81-130">Aktualizacja rejestrowania</span><span class="sxs-lookup"><span data-stu-id="dda81-130">Logging update</span></span>

<span data-ttu-id="dda81-131">W ASP.NET Core 2,0 rejestrowanie jest domyślnie włączone do systemu iniekcji zależności.</span><span class="sxs-lookup"><span data-stu-id="dda81-131">In ASP.NET Core 2.0, logging is incorporated into the dependency injection (DI) system by default.</span></span> <span data-ttu-id="dda81-132">Należy dodać dostawców i skonfigurować filtrowanie w pliku *program.cs* , a nie w pliku *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="dda81-132">You add providers and configure filtering in the *Program.cs* file instead of in the *Startup.cs* file.</span></span> <span data-ttu-id="dda81-133">Domyślnie `ILoggerFactory` obsługuje filtrowanie w taki sposób, który pozwala korzystać z jednej elastycznej metody filtrowania między dostawcami i filtrowania określonego dostawcy.</span><span class="sxs-lookup"><span data-stu-id="dda81-133">And the default `ILoggerFactory` supports filtering in a way that lets you use one flexible approach for both cross-provider filtering and specific-provider filtering.</span></span>

<span data-ttu-id="dda81-134">Aby uzyskać więcej informacji, zobacz [wprowadzenie do rejestrowania](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="dda81-134">For more information, see [Introduction to Logging](xref:fundamentals/logging/index).</span></span>

## <a name="authentication-update"></a><span data-ttu-id="dda81-135">Aktualizacja uwierzytelniania</span><span class="sxs-lookup"><span data-stu-id="dda81-135">Authentication update</span></span>

<span data-ttu-id="dda81-136">Nowy model uwierzytelniania ułatwia konfigurowanie uwierzytelniania aplikacji przy użyciu funkcji DI.</span><span class="sxs-lookup"><span data-stu-id="dda81-136">A new authentication model makes it easier to configure authentication for an application using DI.</span></span>

<span data-ttu-id="dda81-137">Nowe szablony są dostępne do konfigurowania uwierzytelniania dla aplikacji sieci Web i interfejsów API sieci Web przy użyciu [Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/).</span><span class="sxs-lookup"><span data-stu-id="dda81-137">New templates are available for configuring authentication for web apps and web APIs using [Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/).</span></span>

<span data-ttu-id="dda81-138">Aby uzyskać informacje o stanie planowanej dokumentacji, zobacz problem z usługą [GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/3054).</span><span class="sxs-lookup"><span data-stu-id="dda81-138">For information about the status of planned documentation, see the [GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/3054).</span></span>

## <a name="no-locidentity-update"></a><span data-ttu-id="dda81-139">Identityaktualizacji</span><span class="sxs-lookup"><span data-stu-id="dda81-139">Identity update</span></span>

<span data-ttu-id="dda81-140">Ułatwiamy tworzenie bezpiecznych interfejsów API sieci Web za pomocą Identity programu w ASP.NET Core 2,0.</span><span class="sxs-lookup"><span data-stu-id="dda81-140">We've made it easier to build secure web APIs using Identity in ASP.NET Core 2.0.</span></span> <span data-ttu-id="dda81-141">Możesz uzyskać tokeny dostępu do uzyskiwania dostępu do interfejsów API sieci Web przy użyciu [biblioteki uwierzytelniania firmy Microsoft (MSAL)](https://www.nuget.org/packages/Microsoft.Identity.Client).</span><span class="sxs-lookup"><span data-stu-id="dda81-141">You can acquire access tokens for accessing your web APIs using the [Microsoft Authentication Library (MSAL)](https://www.nuget.org/packages/Microsoft.Identity.Client).</span></span>

<span data-ttu-id="dda81-142">Aby uzyskać więcej informacji na temat zmian uwierzytelniania w 2,0, zobacz następujące zasoby:</span><span class="sxs-lookup"><span data-stu-id="dda81-142">For more information on authentication changes in 2.0, see the following resources:</span></span>

* [<span data-ttu-id="dda81-143">Potwierdzenie konta i odzyskiwanie hasła w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dda81-143">Account confirmation and password recovery in ASP.NET Core</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="dda81-144">Włącz generowanie kodu QR dla aplikacji uwierzytelniania w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dda81-144">Enable QR Code generation for authenticator apps in ASP.NET Core</span></span>](xref:security/authentication/identity-enable-qrcodes)
* [<span data-ttu-id="dda81-145">Migrowanie uwierzytelniania i Identity do ASP.NET Core 2,0</span><span class="sxs-lookup"><span data-stu-id="dda81-145">Migrate Authentication and Identity to ASP.NET Core 2.0</span></span>](xref:migration/1x-to-2x/identity-2x)

## <a name="spa-templates"></a><span data-ttu-id="dda81-146">Szablony SPA</span><span class="sxs-lookup"><span data-stu-id="dda81-146">SPA templates</span></span>

<span data-ttu-id="dda81-147">Dostępne są szablony projektów aplikacji jednostronicowych (SPA) dla kątowych, Aurelia, Knockout.js, React.js i React.js z Redux.</span><span class="sxs-lookup"><span data-stu-id="dda81-147">Single Page Application (SPA) project templates for Angular, Aurelia, Knockout.js, React.js, and React.js with Redux are available.</span></span> <span data-ttu-id="dda81-148">Szablon kątowy został zaktualizowany do kąta 4.</span><span class="sxs-lookup"><span data-stu-id="dda81-148">The Angular template has been updated to Angular 4.</span></span> <span data-ttu-id="dda81-149">Szablony kątowe i reagowanie są domyślnie dostępne; Aby uzyskać informacje o sposobach uzyskiwania innych szablonów, zobacz [Tworzenie nowego projektu Spa](xref:client-side/spa-services#create-a-new-project).</span><span class="sxs-lookup"><span data-stu-id="dda81-149">The Angular and React templates are available by default; for information about how to get the other templates, see [Create a new SPA project](xref:client-side/spa-services#create-a-new-project).</span></span> <span data-ttu-id="dda81-150">Aby uzyskać informacje na temat sposobu tworzenia SPA w ASP.NET Core, zobacz <xref:client-side/spa-services> .</span><span class="sxs-lookup"><span data-stu-id="dda81-150">For information about how to build a SPA in ASP.NET Core, see <xref:client-side/spa-services>.</span></span>

## <a name="kestrel-improvements"></a><span data-ttu-id="dda81-151">Udoskonalenia Kestrel</span><span class="sxs-lookup"><span data-stu-id="dda81-151">Kestrel improvements</span></span>

<span data-ttu-id="dda81-152">Serwer sieci Web Kestrel ma nowe funkcje, które sprawiają, że jest to bardziej odpowiednie jako serwer dostępny z Internetu.</span><span class="sxs-lookup"><span data-stu-id="dda81-152">The Kestrel web server has new features that make it more suitable as an Internet-facing server.</span></span> <span data-ttu-id="dda81-153">W nowej właściwości klasy są dodawane różne opcje konfiguracji ograniczeń serwera `KestrelServerOptions` `Limits` .</span><span class="sxs-lookup"><span data-stu-id="dda81-153">A number of server constraint configuration options are added in the `KestrelServerOptions` class's new `Limits` property.</span></span> <span data-ttu-id="dda81-154">Dodaj limity dla następujących:</span><span class="sxs-lookup"><span data-stu-id="dda81-154">Add limits for the following:</span></span>

* <span data-ttu-id="dda81-155">Maksymalna liczba połączeń klienta</span><span class="sxs-lookup"><span data-stu-id="dda81-155">Maximum client connections</span></span>
* <span data-ttu-id="dda81-156">Maksymalny rozmiar treści żądania</span><span class="sxs-lookup"><span data-stu-id="dda81-156">Maximum request body size</span></span>
* <span data-ttu-id="dda81-157">Minimalna stawka danych treści żądania</span><span class="sxs-lookup"><span data-stu-id="dda81-157">Minimum request body data rate</span></span>

<span data-ttu-id="dda81-158">Aby uzyskać więcej informacji, zobacz [Implementacja serwera sieci Web Kestrel w ASP.NET Core](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="dda81-158">For more information, see [Kestrel web server implementation in ASP.NET Core](xref:fundamentals/servers/kestrel).</span></span>

## <a name="weblistener-renamed-to-httpsys"></a><span data-ttu-id="dda81-159">Nazwa elementu webListener została zmieniona na HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="dda81-159">WebListener renamed to HTTP.sys</span></span>

<span data-ttu-id="dda81-160">Pakiety `Microsoft.AspNetCore.Server.WebListener` i zostały `Microsoft.Net.Http.Server` scalone z nowym pakietem `Microsoft.AspNetCore.Server.HttpSys` .</span><span class="sxs-lookup"><span data-stu-id="dda81-160">The packages `Microsoft.AspNetCore.Server.WebListener` and `Microsoft.Net.Http.Server` have been merged into a new package `Microsoft.AspNetCore.Server.HttpSys`.</span></span> <span data-ttu-id="dda81-161">Przestrzenie nazw zostały zaktualizowane w celu dopasowania.</span><span class="sxs-lookup"><span data-stu-id="dda81-161">The namespaces have been updated to match.</span></span>

<span data-ttu-id="dda81-162">Aby uzyskać więcej informacji, zobacz [HTTP.sys implementacja serwera sieci Web w programie ASP.NET Core](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="dda81-162">For more information, see [HTTP.sys web server implementation in ASP.NET Core](xref:fundamentals/servers/httpsys).</span></span>

## <a name="enhanced-http-header-support"></a><span data-ttu-id="dda81-163">Ulepszona obsługa nagłówka HTTP</span><span class="sxs-lookup"><span data-stu-id="dda81-163">Enhanced HTTP header support</span></span>

<span data-ttu-id="dda81-164">W przypadku przesyłania lub do programu przy użyciu MVC jest `FileStreamResult` `FileContentResult` teraz dostępna opcja ustawiania `ETag` lub `LastModified` daty przesyłanej zawartości.</span><span class="sxs-lookup"><span data-stu-id="dda81-164">When using MVC to transmit a `FileStreamResult` or a `FileContentResult`, you now have the option to set an `ETag` or a `LastModified` date on the content you transmit.</span></span> <span data-ttu-id="dda81-165">Można ustawić te wartości w zwracanej zawartości z kodem podobnym do poniższego:</span><span class="sxs-lookup"><span data-stu-id="dda81-165">You can set these values on the returned content with code similar to the following:</span></span>

```csharp
var data = Encoding.UTF8.GetBytes("This is a sample text from a binary array");
var entityTag = new EntityTagHeaderValue("\"MyCalculatedEtagValue\"");
return File(data, "text/plain", "downloadName.txt", lastModified: DateTime.UtcNow.AddSeconds(-5), entityTag: entityTag);
```

<span data-ttu-id="dda81-166">Plik zwrócony do odwiedzających ma odpowiednie nagłówki HTTP dla `ETag` `LastModified` wartości i.</span><span class="sxs-lookup"><span data-stu-id="dda81-166">The file returned to your visitors has the appropriate HTTP headers for the `ETag` and `LastModified` values.</span></span>

<span data-ttu-id="dda81-167">Jeśli osoba odwiedzająca aplikację żąda zawartości z nagłówkiem żądania zakresu, ASP.NET Core rozpoznaje żądanie i obsługuje nagłówek.</span><span class="sxs-lookup"><span data-stu-id="dda81-167">If an application visitor requests content with a Range Request header, ASP.NET Core recognizes the request and handles the header.</span></span> <span data-ttu-id="dda81-168">Jeśli żądana zawartość może zostać dostarczona częściowo, ASP.NET Core odpowiednio pomija i zwróci tylko żądany zestaw bajtów.</span><span class="sxs-lookup"><span data-stu-id="dda81-168">If the requested content can be partially delivered, ASP.NET Core appropriately skips and returns just the requested set of bytes.</span></span> <span data-ttu-id="dda81-169">Nie musisz pisać żadnych specjalnych programów obsługi w swoich metodach, aby dostosować lub obsłużyć tę funkcję. jest ono automatycznie obsługiwane.</span><span class="sxs-lookup"><span data-stu-id="dda81-169">You don't need to write any special handlers into your methods to adapt or handle this feature; it's automatically handled for you.</span></span>

## <a name="hosting-startup-and-application-insights"></a><span data-ttu-id="dda81-170">Hosting uruchamiania i Application Insights</span><span class="sxs-lookup"><span data-stu-id="dda81-170">Hosting startup and Application Insights</span></span>

<span data-ttu-id="dda81-171">Środowiska hostingu mogą teraz dodawać dodatkowe zależności pakietu i wykonywać kod podczas uruchamiania aplikacji, bez konieczności jawnego podjęcia zależności przez aplikację lub wywołania jakichkolwiek metod.</span><span class="sxs-lookup"><span data-stu-id="dda81-171">Hosting environments can now inject extra package dependencies and execute code during application startup, without the application needing to explicitly take a dependency or call any methods.</span></span> <span data-ttu-id="dda81-172">Ta funkcja może służyć do włączania pewnych środowisk jako "świateł-up", które są unikatowe dla danego środowiska, bez konieczności poznania się przed czasem.</span><span class="sxs-lookup"><span data-stu-id="dda81-172">This feature can be used to enable certain environments to "light-up" features unique to that environment without the application needing to know ahead of time.</span></span> 

<span data-ttu-id="dda81-173">W ASP.NET Core 2,0 Ta funkcja jest używana do automatycznego włączania diagnostyki Application Insights podczas debugowania w programie Visual Studio i (po dodaniu go w programie) podczas uruchamiania na platformie Azure App Services.</span><span class="sxs-lookup"><span data-stu-id="dda81-173">In ASP.NET Core 2.0, this feature is used to automatically enable Application Insights diagnostics when debugging in Visual Studio and (after opting in) when running in Azure App Services.</span></span> <span data-ttu-id="dda81-174">W związku z tym szablony projektu nie będą już domyślnie dodawać Application Insights pakietów i kodu.</span><span class="sxs-lookup"><span data-stu-id="dda81-174">As a result, the project templates no longer add Application Insights packages and code by default.</span></span>

<span data-ttu-id="dda81-175">Aby uzyskać informacje o stanie planowanej dokumentacji, zobacz problem z usługą [GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/3389).</span><span class="sxs-lookup"><span data-stu-id="dda81-175">For information about the status of planned documentation, see the [GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/3389).</span></span>

## <a name="automatic-use-of-anti-forgery-tokens"></a><span data-ttu-id="dda81-176">Automatyczne korzystanie z tokenów chroniących przed fałszerstwem</span><span class="sxs-lookup"><span data-stu-id="dda81-176">Automatic use of anti-forgery tokens</span></span>

<span data-ttu-id="dda81-177">ASP.NET Core zawsze pomogła korzystać z zawartości kodowanej w języku HTML domyślnie, ale z nową wersją jest podejmowana dodatkowa czynność, która pomaga zapobiegać atakom przez wiele witryn (XSRF).</span><span class="sxs-lookup"><span data-stu-id="dda81-177">ASP.NET Core has always helped HTML-encode content by default, but with the new version an extra step is taken to help prevent cross-site request forgery (XSRF) attacks.</span></span> <span data-ttu-id="dda81-178">ASP.NET Core teraz emitują tokeny zabezpieczające przed fałszerstwem domyślnie i sprawdzają poprawność przy użyciu akcji POST formularzy i stron bez dodatkowej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="dda81-178">ASP.NET Core will now emit anti-forgery tokens by default and validate them on form POST actions and pages without extra configuration.</span></span>

<span data-ttu-id="dda81-179">Aby uzyskać więcej informacji, zobacz [Zapobiegaj fałszowaniu żądań między witrynami (XSRF/CSRF)](xref:security/anti-request-forgery).</span><span class="sxs-lookup"><span data-stu-id="dda81-179">For more information, see [Prevent Cross-Site Request Forgery (XSRF/CSRF) attacks](xref:security/anti-request-forgery).</span></span>

## <a name="automatic-precompilation"></a><span data-ttu-id="dda81-180">Automatyczna kompilacja prekompilowana</span><span class="sxs-lookup"><span data-stu-id="dda81-180">Automatic precompilation</span></span>

<span data-ttu-id="dda81-181">RazorWidok sprzed kompilacji jest domyślnie włączony podczas publikowania, zmniejszając rozmiar danych wyjściowych publikowania i czas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="dda81-181">Razor view pre-compilation is enabled during publish by default, reducing the publish output size and application startup time.</span></span>

<span data-ttu-id="dda81-182">Aby uzyskać więcej informacji, zobacz [ Razor przeglądanie kompilacji i wstępnej kompilacji w ASP.NET Core](xref:mvc/views/view-compilation).</span><span class="sxs-lookup"><span data-stu-id="dda81-182">For more information, see [Razor view compilation and precompilation in ASP.NET Core](xref:mvc/views/view-compilation).</span></span>

## <a name="no-locrazor-support-for-c-71"></a><span data-ttu-id="dda81-183">RazorObsługa języka C# 7,1</span><span class="sxs-lookup"><span data-stu-id="dda81-183">Razor support for C# 7.1</span></span>

<span data-ttu-id="dda81-184">RazorAparat widoku został zaktualizowany, aby współpracował z nowym kompilatorem Roslyn.</span><span class="sxs-lookup"><span data-stu-id="dda81-184">The Razor view engine has been updated to work with the new Roslyn compiler.</span></span> <span data-ttu-id="dda81-185">Obejmuje to obsługę funkcji języka C# 7,1, takich jak domyślne wyrażenia, wywnioskowane nazwy krotek i dopasowanie do wzorca przy użyciu typów ogólnych.</span><span class="sxs-lookup"><span data-stu-id="dda81-185">That includes support for C# 7.1 features like Default Expressions, Inferred Tuple Names, and Pattern-Matching with Generics.</span></span> <span data-ttu-id="dda81-186">Aby użyć języka C# 7,1 w projekcie, Dodaj następującą właściwość w pliku projektu, a następnie załaduj ponownie rozwiązanie:</span><span class="sxs-lookup"><span data-stu-id="dda81-186">To use C# 7.1 in your project, add the following property in your project file and then reload the solution:</span></span>

```xml
<LangVersion>latest</LangVersion>
```

<span data-ttu-id="dda81-187">Aby uzyskać informacje o stanie funkcji języka C# 7,1, zobacz [repozytorium GitHub Roslyn](https://github.com/dotnet/roslyn/blob/master/docs/Language%20Feature%20Status.md).</span><span class="sxs-lookup"><span data-stu-id="dda81-187">For information about the status of C# 7.1 features, see [the Roslyn GitHub repository](https://github.com/dotnet/roslyn/blob/master/docs/Language%20Feature%20Status.md).</span></span>

## <a name="other-documentation-updates-for-20"></a><span data-ttu-id="dda81-188">Inne aktualizacje dokumentacji dotyczące 2,0</span><span class="sxs-lookup"><span data-stu-id="dda81-188">Other documentation updates for 2.0</span></span>

* [<span data-ttu-id="dda81-189">Profile publikowania programu Visual Studio dla wdrożenia aplikacji ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dda81-189">Visual Studio publish profiles for ASP.NET Core app deployment</span></span>](xref:host-and-deploy/visual-studio-publish-profiles)
* [<span data-ttu-id="dda81-190">Zarządzanie kluczami</span><span class="sxs-lookup"><span data-stu-id="dda81-190">Key Management</span></span>](xref:security/data-protection/implementation/key-management)
* [<span data-ttu-id="dda81-191">Konfigurowanie uwierzytelniania w usłudze Facebook</span><span class="sxs-lookup"><span data-stu-id="dda81-191">Configure Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="dda81-192">Konfigurowanie uwierzytelniania w usłudze Twitter</span><span class="sxs-lookup"><span data-stu-id="dda81-192">Configure Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="dda81-193">Skonfiguruj uwierzytelnianie Google</span><span class="sxs-lookup"><span data-stu-id="dda81-193">Configure Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="dda81-194">Konfigurowanie uwierzytelniania konta Microsoft</span><span class="sxs-lookup"><span data-stu-id="dda81-194">Configure Microsoft Account authentication</span></span>](xref:security/authentication/microsoft-logins)

## <a name="migration-guidance"></a><span data-ttu-id="dda81-195">Wskazówki dotyczące migracji</span><span class="sxs-lookup"><span data-stu-id="dda81-195">Migration guidance</span></span>

<span data-ttu-id="dda81-196">Aby uzyskać wskazówki dotyczące sposobu migracji aplikacji ASP.NET Core 1. x do ASP.NET Core 2,0, zobacz następujące zasoby:</span><span class="sxs-lookup"><span data-stu-id="dda81-196">For guidance on how to migrate ASP.NET Core 1.x applications to ASP.NET Core 2.0, see the following resources:</span></span>

* [<span data-ttu-id="dda81-197">Migrowanie z ASP.NET Core 1. x do ASP.NET Core 2,0</span><span class="sxs-lookup"><span data-stu-id="dda81-197">Migrate from ASP.NET Core 1.x to ASP.NET Core 2.0</span></span>](xref:migration/1x-to-2x/index)
* [<span data-ttu-id="dda81-198">Migrowanie uwierzytelniania i Identity do ASP.NET Core 2,0</span><span class="sxs-lookup"><span data-stu-id="dda81-198">Migrate Authentication and Identity to ASP.NET Core 2.0</span></span>](xref:migration/1x-to-2x/identity-2x)

## <a name="additional-information"></a><span data-ttu-id="dda81-199">Dodatkowe informacje</span><span class="sxs-lookup"><span data-stu-id="dda81-199">Additional Information</span></span>

<span data-ttu-id="dda81-200">Aby uzyskać pełną listę zmian, zapoznaj się z [informacjami o wersji ASP.NET Core 2,0](https://github.com/dotnet/aspnetcore/releases/tag/2.0.0).</span><span class="sxs-lookup"><span data-stu-id="dda81-200">For the complete list of changes, see the [ASP.NET Core 2.0 Release Notes](https://github.com/dotnet/aspnetcore/releases/tag/2.0.0).</span></span>

<span data-ttu-id="dda81-201">Aby nawiązać połączenie z postępem i planami zespołu deweloperów ASP.NET Core, Dostosuj do [ASP.NET Community standup](https://live.asp.net/).</span><span class="sxs-lookup"><span data-stu-id="dda81-201">To connect with the ASP.NET Core development team's progress and plans, tune in to the [ASP.NET Community Standup](https://live.asp.net/).</span></span>
