---
title: Używanie zestawów uruchamiania hostingu w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak ulepszyć aplikację core ASP.NET z zestawu zewnętrznego przy użyciu implementacji IHostingStartup.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
uid: fundamentals/configuration/platform-specific-configuration
ms.openlocfilehash: ac667b0205f5daad395d86fbe129beb509a044a6
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80417623"
---
# <a name="use-hosting-startup-assemblies-in-aspnet-core"></a><span data-ttu-id="092a1-103">Używanie zestawów uruchamiania hostingu w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="092a1-103">Use hosting startup assemblies in ASP.NET Core</span></span>

<span data-ttu-id="092a1-104">Przez [Pavel Krymets](https://github.com/pakrym)</span><span class="sxs-lookup"><span data-stu-id="092a1-104">By [Pavel Krymets](https://github.com/pakrym)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="092a1-105">Implementacja <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) dodaje ulepszenia do aplikacji podczas uruchamiania z zestawu zewnętrznego.</span><span class="sxs-lookup"><span data-stu-id="092a1-105">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="092a1-106">Na przykład biblioteka zewnętrzna może użyć implementacji uruchamiania hostingu, aby zapewnić dodatkowe dostawcy konfiguracji lub usługi do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="092a1-106">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="092a1-107">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="092a1-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="092a1-108">Atrybut HostingStartup</span><span class="sxs-lookup"><span data-stu-id="092a1-108">HostingStartup attribute</span></span>

<span data-ttu-id="092a1-109">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) atrybut wskazuje obecność zestawu uruchamiania hostingu, aby aktywować w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="092a1-109">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="092a1-110">Zestaw wejścia lub zestaw zawierający `Startup` klasę jest automatycznie `HostingStartup` skanowany w poszukiwaniu atrybutu.</span><span class="sxs-lookup"><span data-stu-id="092a1-110">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="092a1-111">Lista zestawów do wyszukiwania `HostingStartup` atrybutów jest ładowana w czasie wykonywania z konfiguracji w [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="092a1-111">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span> <span data-ttu-id="092a1-112">Lista zestawów do wykluczenia z odnajdywania jest ładowana z [webhostdefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="092a1-112">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span>

<span data-ttu-id="092a1-113">W poniższym przykładzie obszar nazw zestawu `StartupEnhancement`uruchamiania hostingu jest .</span><span class="sxs-lookup"><span data-stu-id="092a1-113">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="092a1-114">Klasa zawierająca kod startowy `StartupEnhancementHostingStartup`hostingu to:</span><span class="sxs-lookup"><span data-stu-id="092a1-114">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="092a1-115">Atrybut `HostingStartup` zazwyczaj znajduje się w pliku klasy `IHostingStartup` implementacji zestawu uruchamiania hostingu.</span><span class="sxs-lookup"><span data-stu-id="092a1-115">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="092a1-116">Odkryj załadowane zestawy startowe hostingu</span><span class="sxs-lookup"><span data-stu-id="092a1-116">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="092a1-117">Aby odkryć załadowane zestawy uruchamiania hostingu, włącz rejestrowanie i sprawdź dzienniki aplikacji.</span><span class="sxs-lookup"><span data-stu-id="092a1-117">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="092a1-118">Błędy, które występują podczas ładowania zestawów są rejestrowane.</span><span class="sxs-lookup"><span data-stu-id="092a1-118">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="092a1-119">Załadowane zestawy uruchamiania hostingu są rejestrowane na poziomie debugowania, a wszystkie błędy są rejestrowane.</span><span class="sxs-lookup"><span data-stu-id="092a1-119">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="092a1-120">Wyłącz automatyczne ładowanie zestawów uruchamiania hostingu</span><span class="sxs-lookup"><span data-stu-id="092a1-120">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="092a1-121">Aby wyłączyć automatyczne ładowanie zestawów uruchamiania hostingu, należy użyć jednego z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="092a1-121">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="092a1-122">Aby zapobiec załadunku wszystkich zestawów uruchamiania `1`hostingu, ustaw jeden z następujących opcji na: `true`</span><span class="sxs-lookup"><span data-stu-id="092a1-122">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>

  * <span data-ttu-id="092a1-123">Zapobiegaj ustawieniu konfiguracji hosta hosta uruchamiania hostingu:</span><span class="sxs-lookup"><span data-stu-id="092a1-123">Prevent Hosting Startup host configuration setting:</span></span>

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.PreventHostingStartupKey, "true")
                    .UseStartup<Startup>();
            });
    ```

  * <span data-ttu-id="092a1-124">`ASPNETCORE_PREVENTHOSTINGSTARTUP`zmienną środowiskową.</span><span class="sxs-lookup"><span data-stu-id="092a1-124">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>

* <span data-ttu-id="092a1-125">Aby zapobiec załadunku określonych zestawów uruchamiania hostingu, ustaw jeden z następujących ciągów rozdzielanych średnikami zestawów uruchamiania hostingu, aby wykluczyć podczas uruchamiania:</span><span class="sxs-lookup"><span data-stu-id="092a1-125">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>

  * <span data-ttu-id="092a1-126">Ustawienie konfiguracji hosta hosta zestawów wykluczeń uruchamiania hostingu:</span><span class="sxs-lookup"><span data-stu-id="092a1-126">Hosting Startup Exclude Assemblies host configuration setting:</span></span>

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseSetting(
                        WebHostDefaults.HostingStartupExcludeAssembliesKey, 
                        "{ASSEMBLY1;ASSEMBLY2; ...}")
                    .UseStartup<Startup>();
            });
    ```

  * <span data-ttu-id="092a1-127">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`zmienną środowiskową.</span><span class="sxs-lookup"><span data-stu-id="092a1-127">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="092a1-128">Jeśli ustawiono zarówno ustawienie konfiguracji hosta, jak i zmienną środowiskową, ustawienie hosta steruje zachowaniem.</span><span class="sxs-lookup"><span data-stu-id="092a1-128">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="092a1-129">Wyłączenie zestawów uruchamiania hostingu przy użyciu ustawienia hosta lub zmiennej środowiskowej wyłącza zestaw globalnie i może wyłączyć kilka cech aplikacji.</span><span class="sxs-lookup"><span data-stu-id="092a1-129">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="092a1-130">Projekt</span><span class="sxs-lookup"><span data-stu-id="092a1-130">Project</span></span>

<span data-ttu-id="092a1-131">Utwórz uruchamianie hostingu z jednym z następujących typów projektów:</span><span class="sxs-lookup"><span data-stu-id="092a1-131">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="092a1-132">Biblioteka klas</span><span class="sxs-lookup"><span data-stu-id="092a1-132">Class library</span></span>](#class-library)
* [<span data-ttu-id="092a1-133">Aplikacja konsoli bez punktu wejścia</span><span class="sxs-lookup"><span data-stu-id="092a1-133">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="092a1-134">Biblioteka klas</span><span class="sxs-lookup"><span data-stu-id="092a1-134">Class library</span></span>

<span data-ttu-id="092a1-135">Rozszerzenie uruchamiania hostingu można zapewnić w bibliotece klas.</span><span class="sxs-lookup"><span data-stu-id="092a1-135">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="092a1-136">Biblioteka zawiera `HostingStartup` atrybut.</span><span class="sxs-lookup"><span data-stu-id="092a1-136">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="092a1-137">[Przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) zawiera aplikację Razor Pages, *HostingStartupApp*i bibliotekę *klas, HostingStartupLibrary*.</span><span class="sxs-lookup"><span data-stu-id="092a1-137">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="092a1-138">Biblioteka klas:</span><span class="sxs-lookup"><span data-stu-id="092a1-138">The class library:</span></span>

* <span data-ttu-id="092a1-139">Zawiera klasę uruchamiania `ServiceKeyInjection`hostingu, `IHostingStartup`która implementuje .</span><span class="sxs-lookup"><span data-stu-id="092a1-139">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="092a1-140">`ServiceKeyInjection`dodaje parę ciągów usług do konfiguracji aplikacji przy użyciu dostawcy konfiguracji w pamięci ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span><span class="sxs-lookup"><span data-stu-id="092a1-140">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>
* <span data-ttu-id="092a1-141">Zawiera `HostingStartup` atrybut identyfikujący obszar nazw i klasę uruchamiania hostingu.</span><span class="sxs-lookup"><span data-stu-id="092a1-141">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="092a1-142">Metoda klasy używa do <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> dodawania ulepszeń do aplikacji. `ServiceKeyInjection` <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*></span><span class="sxs-lookup"><span data-stu-id="092a1-142">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

<span data-ttu-id="092a1-143">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="092a1-143">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="092a1-144">Strona Indeks aplikacji odczytuje i renderuje wartości konfiguracji dla dwóch kluczy ustawionych przez zestaw uruchamiania biblioteki klas hostingu:</span><span class="sxs-lookup"><span data-stu-id="092a1-144">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="092a1-145">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="092a1-145">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="092a1-146">[Przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) zawiera również projekt pakietu NuGet, który zapewnia oddzielne uruchamianie hostingu, *HostingStartupPackage*.</span><span class="sxs-lookup"><span data-stu-id="092a1-146">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="092a1-147">Pakiet ma te same cechy biblioteki klas opisane wcześniej.</span><span class="sxs-lookup"><span data-stu-id="092a1-147">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="092a1-148">Pakiet:</span><span class="sxs-lookup"><span data-stu-id="092a1-148">The package:</span></span>

* <span data-ttu-id="092a1-149">Zawiera klasę uruchamiania `ServiceKeyInjection`hostingu, `IHostingStartup`która implementuje .</span><span class="sxs-lookup"><span data-stu-id="092a1-149">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="092a1-150">`ServiceKeyInjection`dodaje parę ciągów usług do konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="092a1-150">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="092a1-151">Zawiera `HostingStartup` atrybut.</span><span class="sxs-lookup"><span data-stu-id="092a1-151">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="092a1-152">*HostingStartupPackage/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="092a1-152">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="092a1-153">Strona Indeks aplikacji odczytuje i renderuje wartości konfiguracji dla dwóch kluczy ustawionych przez zestaw uruchamiania hostowania pakietu:</span><span class="sxs-lookup"><span data-stu-id="092a1-153">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="092a1-154">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="092a1-154">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="092a1-155">Aplikacja konsoli bez punktu wejścia</span><span class="sxs-lookup"><span data-stu-id="092a1-155">Console app without an entry point</span></span>

<span data-ttu-id="092a1-156">*Takie podejście jest dostępne tylko dla aplikacji .NET Core, a nie .NET Framework.*</span><span class="sxs-lookup"><span data-stu-id="092a1-156">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="092a1-157">Dynamiczne wzmocnienie uruchamiania hostingu, które nie wymaga odwołania do czasu kompilacji dla aktywacji, `HostingStartup` może być podane w aplikacji konsoli bez punktu wejścia, który zawiera atrybut.</span><span class="sxs-lookup"><span data-stu-id="092a1-157">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="092a1-158">Publikowanie aplikacji konsoli tworzy zestaw uruchamiania hostingu, który może być zużywany z magazynu środowiska wykonawczego.</span><span class="sxs-lookup"><span data-stu-id="092a1-158">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="092a1-159">W tym procesie jest używana aplikacja konsoli bez punktu wejścia, ponieważ:</span><span class="sxs-lookup"><span data-stu-id="092a1-159">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="092a1-160">Plik zależności jest wymagany do korzystania z uruchamiania hostingu w zestawie uruchamiania hostingu.</span><span class="sxs-lookup"><span data-stu-id="092a1-160">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="092a1-161">Plik zależności jest uruchamianym zasobem aplikacji, który jest produkowany przez publikowanie aplikacji, a nie biblioteki.</span><span class="sxs-lookup"><span data-stu-id="092a1-161">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="092a1-162">Nie można dodać biblioteki bezpośrednio do [magazynu pakietów środowiska wykonawczego,](/dotnet/core/deploying/runtime-store)co wymaga projektu możliwego do uruchomienia, który jest przeznaczony dla udostępnionego środowiska uruchomieniowego.</span><span class="sxs-lookup"><span data-stu-id="092a1-162">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="092a1-163">W tworzeniu dynamicznego uruchamiania hostingu:</span><span class="sxs-lookup"><span data-stu-id="092a1-163">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="092a1-164">Zestaw uruchamiania hostingu jest tworzony z aplikacji konsoli bez punktu wejścia, który:</span><span class="sxs-lookup"><span data-stu-id="092a1-164">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="092a1-165">Zawiera klasę, która `IHostingStartup` zawiera implementację.</span><span class="sxs-lookup"><span data-stu-id="092a1-165">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="092a1-166">Zawiera [atrybut HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) do `IHostingStartup` identyfikowania klasy implementacji.</span><span class="sxs-lookup"><span data-stu-id="092a1-166">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="092a1-167">Aplikacja konsoli jest publikowana w celu uzyskania zależności uruchamiania hostingu.</span><span class="sxs-lookup"><span data-stu-id="092a1-167">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="092a1-168">Konsekwencją publikowania aplikacji konsoli jest, że nieużywane zależności są przycinane z pliku zależności.</span><span class="sxs-lookup"><span data-stu-id="092a1-168">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="092a1-169">Plik zależności jest modyfikowany w celu ustawienia lokalizacji środowiska wykonawczego zestawu uruchamiania hostingu.</span><span class="sxs-lookup"><span data-stu-id="092a1-169">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="092a1-170">Zestaw uruchamiania hostingu i jego plik zależności jest umieszczany w magazynie pakietów środowiska wykonawczego.</span><span class="sxs-lookup"><span data-stu-id="092a1-170">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="092a1-171">Aby odkryć zestaw uruchamiania hostingu i jego plik zależności, są one wymienione w parze zmiennych środowiskowych.</span><span class="sxs-lookup"><span data-stu-id="092a1-171">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="092a1-172">Aplikacja konsoli odwołuje się do pakietu [Microsoft.AspNetCore.Hosting.Abstractions:](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/)</span><span class="sxs-lookup"><span data-stu-id="092a1-172">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.csproj)]

<span data-ttu-id="092a1-173">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) atrybut identyfikuje klasę jako `IHostingStartup` implementację do ładowania <xref:Microsoft.AspNetCore.Hosting.IWebHost>i wykonywania podczas tworzenia .</span><span class="sxs-lookup"><span data-stu-id="092a1-173">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span> <span data-ttu-id="092a1-174">W poniższym przykładzie obszar `StartupEnhancement`nazw jest `StartupEnhancementHostingStartup`, a klasa jest:</span><span class="sxs-lookup"><span data-stu-id="092a1-174">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="092a1-175">Klasa implementuje `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="092a1-175">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="092a1-176"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> Metoda klasy używa do <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> dodawania ulepszeń do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="092a1-176">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="092a1-177">`IHostingStartup.Configure`w zestawie uruchamiania hostingu jest `Startup.Configure` wywoływana przez środowisko wykonawcze przed w kodzie użytkownika, co pozwala kod użytkownika zastąpić dowolną konfigurację zapewnianą przez zestaw uruchamiania hostingu.</span><span class="sxs-lookup"><span data-stu-id="092a1-177">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="092a1-178">Podczas tworzenia `IHostingStartup` projektu plik zależności (*.deps.json* `runtime` ) ustawia lokalizację złożenia w folderze *bin:*</span><span class="sxs-lookup"><span data-stu-id="092a1-178">When building an `IHostingStartup` project, the dependencies file (*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="092a1-179">Wyświetlana jest tylko część pliku.</span><span class="sxs-lookup"><span data-stu-id="092a1-179">Only part of the file is shown.</span></span> <span data-ttu-id="092a1-180">Nazwa zestawu w przykładzie jest `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="092a1-180">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="092a1-181">Konfiguracja zapewniana przez startup hostingu</span><span class="sxs-lookup"><span data-stu-id="092a1-181">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="092a1-182">Istnieją dwa podejścia do obsługi konfiguracji w zależności od tego, czy konfiguracja uruchamiania hostingu ma mieć pierwszeństwo, czy konfiguracja aplikacji ma pierwszeństwo:</span><span class="sxs-lookup"><span data-stu-id="092a1-182">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="092a1-183">Zapewnij konfigurację <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> aplikacji za pomocą, aby <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> załadować konfigurację po wykonaniu delegatów aplikacji.</span><span class="sxs-lookup"><span data-stu-id="092a1-183">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="092a1-184">Konfiguracja uruchamiania hostingu ma pierwszeństwo przed konfiguracją aplikacji przy użyciu tej metody.</span><span class="sxs-lookup"><span data-stu-id="092a1-184">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="092a1-185">Zapewnij konfigurację <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> aplikacji przy użyciu, aby <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> załadować konfigurację przed wykonaniem delegatów aplikacji.</span><span class="sxs-lookup"><span data-stu-id="092a1-185">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="092a1-186">Wartości konfiguracji aplikacji mają pierwszeństwo przed wartościami dostarczonymi przez startup hostingu przy użyciu tej metody.</span><span class="sxs-lookup"><span data-stu-id="092a1-186">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="092a1-187">Określanie zestawu uruchamiania hostingu</span><span class="sxs-lookup"><span data-stu-id="092a1-187">Specify the hosting startup assembly</span></span>

<span data-ttu-id="092a1-188">W przypadku uruchamiania hostingu dostarczanego przez bibliotekę klas lub konsolę `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` należy określić nazwę zestawu uruchamiania hostingu w zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="092a1-188">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="092a1-189">Zmienna środowiskowa jest listą zestawów rozdzielanych średnikami.</span><span class="sxs-lookup"><span data-stu-id="092a1-189">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="092a1-190">Tylko zestawy uruchamiania hostingu `HostingStartup` są skanowane w celu uzyskania atrybutu.</span><span class="sxs-lookup"><span data-stu-id="092a1-190">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="092a1-191">Dla przykładowej aplikacji *HostingStartupApp*, aby odkryć startupy hostingu opisane wcześniej, zmienna środowiskowa jest ustawiona na następującą wartość:</span><span class="sxs-lookup"><span data-stu-id="092a1-191">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="092a1-192">Zestaw uruchamiania hostingu można również ustawić przy użyciu ustawienia konfiguracji hosta zestawy uruchamiania hostingu:</span><span class="sxs-lookup"><span data-stu-id="092a1-192">A hosting startup assembly can also be set using the Hosting Startup Assemblies host configuration setting:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseSetting(
                    WebHostDefaults.HostingStartupAssembliesKey, 
                    "{ASSEMBLY1;ASSEMBLY2; ...}")
                .UseStartup<Startup>();
        });
```

<span data-ttu-id="092a1-193">Gdy istnieje wiele zestawów uruchamiania hostingu, ich <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> metody są wykonywane w kolejności, w którym są wymienione zestawy.</span><span class="sxs-lookup"><span data-stu-id="092a1-193">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="092a1-194">Aktywacja</span><span class="sxs-lookup"><span data-stu-id="092a1-194">Activation</span></span>

<span data-ttu-id="092a1-195">Opcje aktywacji uruchamiania hostingu to:</span><span class="sxs-lookup"><span data-stu-id="092a1-195">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="092a1-196">[Aktywacja magazynu](#runtime-store) &ndash; środowiska uruchomieniowego nie wymaga odwołania do czasu kompilacji do aktywacji.</span><span class="sxs-lookup"><span data-stu-id="092a1-196">[Runtime store](#runtime-store) &ndash; Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="092a1-197">Przykładowa aplikacja umieszcza zestaw uruchamiania hostingu i zależności plików do folderu, *wdrożenia*, aby ułatwić wdrożenie uruchamiania hostingu w środowisku multimachine.</span><span class="sxs-lookup"><span data-stu-id="092a1-197">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="092a1-198">Folder *wdrażania* zawiera również skrypt programu PowerShell, który tworzy lub modyfikuje zmienne środowiskowe w systemie wdrażania, aby włączyć uruchamianie hostingu.</span><span class="sxs-lookup"><span data-stu-id="092a1-198">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="092a1-199">Odwołanie do czasu kompilacji wymagane do aktywacji</span><span class="sxs-lookup"><span data-stu-id="092a1-199">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="092a1-200">Pakiet NuGet</span><span class="sxs-lookup"><span data-stu-id="092a1-200">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="092a1-201">Folder pojemnika projektu</span><span class="sxs-lookup"><span data-stu-id="092a1-201">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="092a1-202">Magazyn środowiska uruchomieniowego</span><span class="sxs-lookup"><span data-stu-id="092a1-202">Runtime store</span></span>

<span data-ttu-id="092a1-203">Implementacja uruchamiania hostingu jest umieszczana w [magazynie środowiska wykonawczego](/dotnet/core/deploying/runtime-store).</span><span class="sxs-lookup"><span data-stu-id="092a1-203">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="092a1-204">Odwołanie w czasie kompilacji do zestawu nie jest wymagane przez aplikację rozszerzone.</span><span class="sxs-lookup"><span data-stu-id="092a1-204">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="092a1-205">Po zbudowaniu uruchamiania hostingu magazyn środowiska uruchomieniowego jest generowany przy użyciu pliku projektu manifestu i polecenia [magazynu dotnet.](/dotnet/core/tools/dotnet-store)</span><span class="sxs-lookup"><span data-stu-id="092a1-205">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="092a1-206">W przykładowej aplikacji (projekt*RuntimeStore)* używane jest następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="092a1-206">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="092a1-207">W środowisku uruchomieniowym, aby odnajdywać magazyn środowiska `DOTNET_SHARED_STORE` wykonawczego, lokalizacja magazynu środowiska wykonawczego jest dodawany do zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="092a1-207">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="092a1-208">**Modyfikowanie i umieszczanie pliku zależności uruchamiania hostingu**</span><span class="sxs-lookup"><span data-stu-id="092a1-208">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="092a1-209">Aby uaktywnić ulepszenie bez odwołania do pakietu do `additionalDeps`ulepszenia, należy określić dodatkowe zależności do środowiska wykonawczego za pomocą programu .</span><span class="sxs-lookup"><span data-stu-id="092a1-209">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="092a1-210">`additionalDeps`pozwala na:</span><span class="sxs-lookup"><span data-stu-id="092a1-210">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="092a1-211">Rozszerz wykres biblioteki aplikacji, udostępniając zestaw dodatkowych plików *.deps.json* do scalenia z własnym plikiem *deps.json* aplikacji podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="092a1-211">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="092a1-212">Spraw, aby zestaw uruchamiania hostingu był wykrywalny i ładowalny.</span><span class="sxs-lookup"><span data-stu-id="092a1-212">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="092a1-213">Zalecane podejście do generowania pliku dodatkowych zależności jest:</span><span class="sxs-lookup"><span data-stu-id="092a1-213">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="092a1-214">Wykonaj `dotnet publish` w pliku manifestu magazynu środowiska wykonawczego, do którego odwołuje się poprzednia sekcja.</span><span class="sxs-lookup"><span data-stu-id="092a1-214">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="092a1-215">Usuń odwołanie do manifestu `runtime` z bibliotek i sekcji wynikowego pliku *.deps.json.*</span><span class="sxs-lookup"><span data-stu-id="092a1-215">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="092a1-216">W przykładowym projekcie `store.manifest/1.0.0` właściwość jest `targets` `libraries` usuwana z sekcji i:</span><span class="sxs-lookup"><span data-stu-id="092a1-216">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v3.0",
    "signature": ""
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v3.0": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp3.0/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-xrhzuNSyM5/f4ZswhooJ9dmIYLP64wMnqUJSyTKVDKDVj5T+qtzypl8JmM/aFJLLpYrf0FYpVWvGujd7/FfMEw==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

<span data-ttu-id="092a1-217">Umieść plik *deps.json* w następującej lokalizacji:</span><span class="sxs-lookup"><span data-stu-id="092a1-217">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="092a1-218">`{ADDITIONAL DEPENDENCIES PATH}`&ndash; Lokalizacja dodana `DOTNET_ADDITIONAL_DEPS` do zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="092a1-218">`{ADDITIONAL DEPENDENCIES PATH}` &ndash; Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="092a1-219">`{SHARED FRAMEWORK NAME}`&ndash; Udostępnione framework wymagane dla tego pliku dodatkowych zależności.</span><span class="sxs-lookup"><span data-stu-id="092a1-219">`{SHARED FRAMEWORK NAME}` &ndash; Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="092a1-220">`{SHARED FRAMEWORK VERSION}`&ndash; Minimalna wersja współdzielonej struktury.</span><span class="sxs-lookup"><span data-stu-id="092a1-220">`{SHARED FRAMEWORK VERSION}` &ndash; Minimum shared framework version.</span></span>
* <span data-ttu-id="092a1-221">`{ENHANCEMENT ASSEMBLY NAME}`&ndash; Nazwa zestawu ulepszenia.</span><span class="sxs-lookup"><span data-stu-id="092a1-221">`{ENHANCEMENT ASSEMBLY NAME}` &ndash; The enhancement's assembly name.</span></span>

<span data-ttu-id="092a1-222">W przykładowej aplikacji (projekt*RuntimeStore)* plik dodatkowych zależności jest umieszczany w następującej lokalizacji:</span><span class="sxs-lookup"><span data-stu-id="092a1-222">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/3.0.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="092a1-223">W środowisku uruchomieniowym, aby odnajdywać lokalizację `DOTNET_ADDITIONAL_DEPS` magazynu środowiska wykonawczego, dodatkowa lokalizacja pliku zależności jest dodawana do zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="092a1-223">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="092a1-224">W przykładowej aplikacji (Projekt*RuntimeStore)* tworzenie magazynu środowiska wykonawczego i generowanie pliku dodatkowych zależności odbywa się za pomocą skryptu [programu PowerShell.](/powershell/scripting/powershell-scripting)</span><span class="sxs-lookup"><span data-stu-id="092a1-224">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="092a1-225">Aby zapoznać się z przykładami ustawiania zmiennych środowiskowych dla różnych systemów operacyjnych, zobacz [Używanie wielu środowisk](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="092a1-225">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="092a1-226">**Wdrożenie**</span><span class="sxs-lookup"><span data-stu-id="092a1-226">**Deployment**</span></span>

<span data-ttu-id="092a1-227">Aby ułatwić wdrażanie uruchamiania hostingu w środowisku wielomatynowym, przykładowa aplikacja tworzy folder *wdrażania* w opublikowanych danych wyjściowych, który zawiera:</span><span class="sxs-lookup"><span data-stu-id="092a1-227">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="092a1-228">Magazyn środowiska uruchomieniowego uruchamiania hostingu.</span><span class="sxs-lookup"><span data-stu-id="092a1-228">The hosting startup runtime store.</span></span>
* <span data-ttu-id="092a1-229">Plik zależności uruchamiania hostingu.</span><span class="sxs-lookup"><span data-stu-id="092a1-229">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="092a1-230">Skrypt programu PowerShell, który `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`tworzy `DOTNET_SHARED_STORE`lub `DOTNET_ADDITIONAL_DEPS` modyfikuje program , i obsługuje aktywację uruchamiania hostingu.</span><span class="sxs-lookup"><span data-stu-id="092a1-230">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="092a1-231">Uruchom skrypt z administracyjnego wiersza polecenia programu PowerShell w systemie wdrażania.</span><span class="sxs-lookup"><span data-stu-id="092a1-231">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="092a1-232">Pakiet NuGet</span><span class="sxs-lookup"><span data-stu-id="092a1-232">NuGet package</span></span>

<span data-ttu-id="092a1-233">Wzmocnienie uruchamiania hostingu mogą być dostarczane w pakiecie NuGet.</span><span class="sxs-lookup"><span data-stu-id="092a1-233">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="092a1-234">Pakiet ma `HostingStartup` atrybut.</span><span class="sxs-lookup"><span data-stu-id="092a1-234">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="092a1-235">Typy uruchamiania hostingu udostępniane przez pakiet są udostępniane aplikacji przy użyciu jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="092a1-235">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="092a1-236">Rozszerzony plik projektu aplikacji sprawia, że odwołanie do pakietu dla uruchamiania hostingu w pliku projektu aplikacji (odwołanie w czasie kompilacji).</span><span class="sxs-lookup"><span data-stu-id="092a1-236">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="092a1-237">Z odwołaniem w czasie kompilacji w miejscu, zestaw uruchamiania hostingu i wszystkie jego zależności są włączone do pliku zależności aplikacji (*.deps.json*).</span><span class="sxs-lookup"><span data-stu-id="092a1-237">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="092a1-238">Takie podejście ma zastosowanie do pakietu zestawu uruchamiania hostingu opublikowanego [w nuget.org](https://www.nuget.org/).</span><span class="sxs-lookup"><span data-stu-id="092a1-238">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="092a1-239">Plik zależności uruchamiania hostingu jest udostępniany aplikacji rozszerzonej, zgodnie z opisem w sekcji [Magazyn środowiska wykonawczego](#runtime-store) (bez odwołania w czasie kompilacji).</span><span class="sxs-lookup"><span data-stu-id="092a1-239">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="092a1-240">Aby uzyskać więcej informacji na temat pakietów NuGet i magazynu środowiska wykonawczego, zobacz następujące tematy:</span><span class="sxs-lookup"><span data-stu-id="092a1-240">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="092a1-241">Jak utworzyć pakiet NuGet za pomocą narzędzi wieloplatformowych</span><span class="sxs-lookup"><span data-stu-id="092a1-241">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="092a1-242">Publikowanie pakietów</span><span class="sxs-lookup"><span data-stu-id="092a1-242">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="092a1-243">Magazyn pakietu środowiska uruchomieniowego</span><span class="sxs-lookup"><span data-stu-id="092a1-243">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="092a1-244">Folder pojemnika projektu</span><span class="sxs-lookup"><span data-stu-id="092a1-244">Project bin folder</span></span>

<span data-ttu-id="092a1-245">Ulepszenie uruchamiania hostingu może być dostarczone przez zestaw wdrożony w *pojemniku*w ulepszonej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="092a1-245">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="092a1-246">Typy uruchamiania hostingu udostępniane przez zestaw są udostępniane aplikacji przy użyciu jednego z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="092a1-246">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="092a1-247">Rozszerzony plik projektu aplikacji sprawia, że odwołanie do zestawu do uruchamiania hostingu (odwołanie w czasie kompilacji).</span><span class="sxs-lookup"><span data-stu-id="092a1-247">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="092a1-248">Z odwołaniem w czasie kompilacji w miejscu, zestaw uruchamiania hostingu i wszystkie jego zależności są włączone do pliku zależności aplikacji (*.deps.json*).</span><span class="sxs-lookup"><span data-stu-id="092a1-248">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="092a1-249">Takie podejście ma zastosowanie, gdy scenariusz wdrażania wymaga dokonania odwołania w czasie kompilacji do zestawu uruchamiania hostingu (plik*dll)* i przenoszenia zestawu do:</span><span class="sxs-lookup"><span data-stu-id="092a1-249">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly (*.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="092a1-250">Projekt zużywający.</span><span class="sxs-lookup"><span data-stu-id="092a1-250">The consuming project.</span></span>
  * <span data-ttu-id="092a1-251">Lokalizacja dostępna dla projektu zużywającego.</span><span class="sxs-lookup"><span data-stu-id="092a1-251">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="092a1-252">Plik zależności uruchamiania hostingu jest udostępniany aplikacji rozszerzonej, zgodnie z opisem w sekcji [Magazyn środowiska wykonawczego](#runtime-store) (bez odwołania w czasie kompilacji).</span><span class="sxs-lookup"><span data-stu-id="092a1-252">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="092a1-253">Podczas kierowania na .NET Framework, zestaw jest wczytywany w kontekście obciążenia domyślnego, co w programie .NET Framework oznacza, że zestaw znajduje się w jednej z następujących lokalizacji:</span><span class="sxs-lookup"><span data-stu-id="092a1-253">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="092a1-254">Ścieżka &ndash; podstawowa aplikacji Folder *bin,* w którym znajduje się plik wykonywalny aplikacji (*.exe).*</span><span class="sxs-lookup"><span data-stu-id="092a1-254">Application base path &ndash; The *bin* folder where the app's executable (*.exe*) is located.</span></span>
  * <span data-ttu-id="092a1-255">Globalna pamięć podręczna &ndash; zestawów (GAC) GaC przechowuje zestawy współużytkowane przez kilka aplikacji platformy .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="092a1-255">Global Assembly Cache (GAC) &ndash; The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="092a1-256">Aby uzyskać więcej informacji, zobacz [Jak: Instalowanie zestawu w globalnej pamięci podręcznej zestawów](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) w dokumentacji programu .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="092a1-256">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="092a1-257">Przykładowy kod</span><span class="sxs-lookup"><span data-stu-id="092a1-257">Sample code</span></span>

<span data-ttu-id="092a1-258">[Przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) [(jak pobrać)](xref:index#how-to-download-a-sample)demonstruje scenariusze implementacji uruchamiania hostingu:</span><span class="sxs-lookup"><span data-stu-id="092a1-258">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="092a1-259">Dwa zestawy uruchamiania hostingu (biblioteki klas) ustawiają parę par klucza-wartości konfiguracji w pamięci:</span><span class="sxs-lookup"><span data-stu-id="092a1-259">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="092a1-260">Pakiet NuGet (*HostingStartupPackage*)</span><span class="sxs-lookup"><span data-stu-id="092a1-260">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="092a1-261">Biblioteka klas (*HostingStartupLibrary*)</span><span class="sxs-lookup"><span data-stu-id="092a1-261">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="092a1-262">Uruchamianie hostingu jest aktywowane z zestawu wdrożonego w magazynie uruchomieniowym *(StartupDiagnostics*).</span><span class="sxs-lookup"><span data-stu-id="092a1-262">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="092a1-263">Zestaw dodaje dwa oprogramowanie pośredniczące do aplikacji podczas uruchamiania, które zawierają informacje diagnostyczne na:</span><span class="sxs-lookup"><span data-stu-id="092a1-263">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="092a1-264">Zarejestrowane usługi</span><span class="sxs-lookup"><span data-stu-id="092a1-264">Registered services</span></span>
  * <span data-ttu-id="092a1-265">Adres (schemat, host, podstawa ścieżki, ścieżka, ciąg zapytania)</span><span class="sxs-lookup"><span data-stu-id="092a1-265">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="092a1-266">Połączenie (zdalny adres IP, port zdalny, lokalny adres IP, port lokalny, certyfikat klienta)</span><span class="sxs-lookup"><span data-stu-id="092a1-266">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="092a1-267">Nagłówki żądań</span><span class="sxs-lookup"><span data-stu-id="092a1-267">Request headers</span></span>
  * <span data-ttu-id="092a1-268">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="092a1-268">Environment variables</span></span>

<span data-ttu-id="092a1-269">Aby uruchomić próbkę:</span><span class="sxs-lookup"><span data-stu-id="092a1-269">To run the sample:</span></span>

<span data-ttu-id="092a1-270">**Aktywacja z pakietu NuGet**</span><span class="sxs-lookup"><span data-stu-id="092a1-270">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="092a1-271">Skompiluj pakiet *HostingStartupPackage* za pomocą polecenia [dotnet pack.](/dotnet/core/tools/dotnet-pack)</span><span class="sxs-lookup"><span data-stu-id="092a1-271">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="092a1-272">Dodaj nazwę zestawu pakietu *HostingStartupPackage* do `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="092a1-272">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="092a1-273">Skompiluj i uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="092a1-273">Compile and run the app.</span></span> <span data-ttu-id="092a1-274">Odwołanie do pakietu jest obecny w aplikacji rozszerzonej (odwołanie w czasie kompilacji).</span><span class="sxs-lookup"><span data-stu-id="092a1-274">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="092a1-275">A `<PropertyGroup>` w pliku projektu aplikacji określa dane wyjściowe projektu pakietu (*.. /HostingStartupPackage/bin/debug)* jako źródło pakietu.</span><span class="sxs-lookup"><span data-stu-id="092a1-275">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="092a1-276">Dzięki temu aplikacja może korzystać z pakietu bez przesyłania pakietu do [nuget.org](https://www.nuget.org/). Aby uzyskać więcej informacji, zobacz uwagi w pliku projektu HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="092a1-276">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="092a1-277">Należy zauważyć, że wartości klucza konfiguracji usługi renderowane przez stronę `ServiceKeyInjection.Configure` Indeks odpowiadają wartościom ustawionym przez metodę pakietu.</span><span class="sxs-lookup"><span data-stu-id="092a1-277">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="092a1-278">Jeśli wniesiesz zmiany do projektu *HostingStartupPackage* i ponownie skompilować go, wyczyść lokalne pamięci podręczne pakietu NuGet, aby upewnić się, że *HostingStartupApp* odbiera zaktualizowany pakiet, a nie nieaktualny pakiet z lokalnej pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="092a1-278">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="092a1-279">Aby wyczyścić lokalne bufory NuGet, wykonaj następujące polecenie [dotnet nuget locals:](/dotnet/core/tools/dotnet-nuget-locals)</span><span class="sxs-lookup"><span data-stu-id="092a1-279">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="092a1-280">**Aktywacja z biblioteki klas**</span><span class="sxs-lookup"><span data-stu-id="092a1-280">**Activation from a class library**</span></span>

1. <span data-ttu-id="092a1-281">Skompiluj bibliotekę klas *HostingStartupLibrary* za pomocą polecenia [dotnet build.](/dotnet/core/tools/dotnet-build)</span><span class="sxs-lookup"><span data-stu-id="092a1-281">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="092a1-282">Dodaj nazwę zestawu biblioteki klas *HostingStartupLibrary* do zmiennej środowiskowej. `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="092a1-282">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="092a1-283">*bin*-deploy zestawu biblioteki klas do aplikacji przez skopiowanie *pliku HostingStartupLibrary.dll* z biblioteki klas skompilowanych danych wyjściowych do folderu *bin/debug* aplikacji.</span><span class="sxs-lookup"><span data-stu-id="092a1-283">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="092a1-284">Skompiluj i uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="092a1-284">Compile and run the app.</span></span> <span data-ttu-id="092a1-285">Plik `<ItemGroup>` projektu aplikacji odwołuje się do zestawu biblioteki klas (*.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*) (odwołanie do czasu kompilacji).</span><span class="sxs-lookup"><span data-stu-id="092a1-285">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="092a1-286">Aby uzyskać więcej informacji, zobacz uwagi w pliku projektu HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="092a1-286">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp3.0\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion> 
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="092a1-287">Należy zauważyć, że wartości klucza konfiguracji usługi renderowane przez stronę `ServiceKeyInjection.Configure` Indeks odpowiadają wartościom ustawionym przez metodę biblioteki klas.</span><span class="sxs-lookup"><span data-stu-id="092a1-287">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="092a1-288">**Aktywacja z zestawu wdrożonego w magazynie uruchomieniowym**</span><span class="sxs-lookup"><span data-stu-id="092a1-288">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="092a1-289">Projekt *StartupDiagnostics* używa programu [PowerShell](/powershell/scripting/powershell-scripting) do modyfikowania pliku *StartupDiagnostics.deps.json.*</span><span class="sxs-lookup"><span data-stu-id="092a1-289">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="092a1-290">Program PowerShell jest instalowany domyślnie w systemie Windows, począwszy od systemu Windows 7 z dodatku SP1 i dodatku SP1 dla systemu Windows Server 2008 R2.</span><span class="sxs-lookup"><span data-stu-id="092a1-290">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="092a1-291">Aby uzyskać program PowerShell na innych platformach, zobacz [Instalowanie różnych wersji programu PowerShell](/powershell/scripting/install/installing-powershell).</span><span class="sxs-lookup"><span data-stu-id="092a1-291">To obtain PowerShell on other platforms, see [Installing various versions of PowerShell](/powershell/scripting/install/installing-powershell).</span></span>
1. <span data-ttu-id="092a1-292">Wykonaj skrypt *build.ps1* w folderze *RuntimeStore.*</span><span class="sxs-lookup"><span data-stu-id="092a1-292">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="092a1-293">Skrypt:</span><span class="sxs-lookup"><span data-stu-id="092a1-293">The script:</span></span>
   * <span data-ttu-id="092a1-294">Generuje `StartupDiagnostics` pakiet w folderze *obj\packages.*</span><span class="sxs-lookup"><span data-stu-id="092a1-294">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span>
   * <span data-ttu-id="092a1-295">Generuje magazyn środowiska wykonawczego `StartupDiagnostics` w folderze *magazynu.*</span><span class="sxs-lookup"><span data-stu-id="092a1-295">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="092a1-296">Polecenie `dotnet store` w skrypcie `win7-x64` używa [identyfikatora środowiska wykonawczego (RID)](/dotnet/core/rid-catalog) dla uruchamiania hostingu wdrożonego w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="092a1-296">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="092a1-297">Podczas dostarczania uruchamiania hostingu dla innego środowiska uruchomieniowego, zastąp poprawny rid w wierszu 37 skryptu.</span><span class="sxs-lookup"><span data-stu-id="092a1-297">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="092a1-298">Magazyn środowiska wykonawczego `StartupDiagnostics` zostanie później przeniesiony do magazynu środowiska wykonawczego użytkownika lub systemu na komputerze, na którym zostanie wykorzystany zestaw.</span><span class="sxs-lookup"><span data-stu-id="092a1-298">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span> <span data-ttu-id="092a1-299">Lokalizacja instalacji magazynu uruchomieniowego użytkownika dla `StartupDiagnostics` zestawu to *.dotnet/store/x64/netcoreapp3.0/startupdiagnostics/1.0.0/lib/netcoreapp3.0/StartupDiagnostics.dll*.</span><span class="sxs-lookup"><span data-stu-id="092a1-299">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp3.0/startupdiagnostics/1.0.0/lib/netcoreapp3.0/StartupDiagnostics.dll*.</span></span>
   * <span data-ttu-id="092a1-300">Generuje for `additionalDeps` `StartupDiagnostics` w *folderze additionalDeps.*</span><span class="sxs-lookup"><span data-stu-id="092a1-300">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="092a1-301">Dodatkowe zależności zostaną później przeniesione do dodatkowych zależności użytkownika lub systemu.</span><span class="sxs-lookup"><span data-stu-id="092a1-301">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span> <span data-ttu-id="092a1-302">Lokalizacją `StartupDiagnostics` instalacji dodatkowych zależności użytkownika jest *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/3.0.0/StartupDiagnostics.deps.json*.</span><span class="sxs-lookup"><span data-stu-id="092a1-302">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/3.0.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="092a1-303">Umieszcza plik *deploy.ps1* w folderze *wdrażania.*</span><span class="sxs-lookup"><span data-stu-id="092a1-303">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="092a1-304">Uruchom skrypt *deploy.ps1* w folderze *wdrażania.*</span><span class="sxs-lookup"><span data-stu-id="092a1-304">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="092a1-305">Skrypt dołącza:</span><span class="sxs-lookup"><span data-stu-id="092a1-305">The script appends:</span></span>
   * <span data-ttu-id="092a1-306">`StartupDiagnostics`do `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="092a1-306">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="092a1-307">Ścieżka zależności uruchamiania hostingu (w folderze *wdrażania* projektu `DOTNET_ADDITIONAL_DEPS` RuntimeStore) do zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="092a1-307">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="092a1-308">Ścieżka magazynu środowiska wykonawczego (w folderze *wdrażania* projektu RuntimeStore) do zmiennej środowiskowej. `DOTNET_SHARED_STORE`</span><span class="sxs-lookup"><span data-stu-id="092a1-308">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="092a1-309">Uruchom przykładową aplikację.</span><span class="sxs-lookup"><span data-stu-id="092a1-309">Run the sample app.</span></span>
1. <span data-ttu-id="092a1-310">Poproś `/services` o punkt końcowy, aby wyświetlić zarejestrowane usługi aplikacji.</span><span class="sxs-lookup"><span data-stu-id="092a1-310">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="092a1-311">Poproś `/diag` o punkt końcowy, aby wyświetlić informacje diagnostyczne.</span><span class="sxs-lookup"><span data-stu-id="092a1-311">Request the `/diag` endpoint to see the diagnostic information.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="092a1-312">Implementacja <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) dodaje ulepszenia do aplikacji podczas uruchamiania z zestawu zewnętrznego.</span><span class="sxs-lookup"><span data-stu-id="092a1-312">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="092a1-313">Na przykład biblioteka zewnętrzna może użyć implementacji uruchamiania hostingu, aby zapewnić dodatkowe dostawcy konfiguracji lub usługi do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="092a1-313">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="092a1-314">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="092a1-314">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="092a1-315">Atrybut HostingStartup</span><span class="sxs-lookup"><span data-stu-id="092a1-315">HostingStartup attribute</span></span>

<span data-ttu-id="092a1-316">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) atrybut wskazuje obecność zestawu uruchamiania hostingu, aby aktywować w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="092a1-316">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="092a1-317">Zestaw wejścia lub zestaw zawierający `Startup` klasę jest automatycznie `HostingStartup` skanowany w poszukiwaniu atrybutu.</span><span class="sxs-lookup"><span data-stu-id="092a1-317">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="092a1-318">Lista zestawów do wyszukiwania `HostingStartup` atrybutów jest ładowana w czasie wykonywania z konfiguracji w [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="092a1-318">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span> <span data-ttu-id="092a1-319">Lista zestawów do wykluczenia z odnajdywania jest ładowana z [webhostdefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="092a1-319">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span> <span data-ttu-id="092a1-320">Aby uzyskać więcej informacji, zobacz [Host sieci Web: Zestawy uruchamiania hostingu](xref:fundamentals/host/web-host#hosting-startup-assemblies) i [host sieci Web: Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span><span class="sxs-lookup"><span data-stu-id="092a1-320">For more information, see [Web Host: Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) and [Web Host: Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span></span>

<span data-ttu-id="092a1-321">W poniższym przykładzie obszar nazw zestawu `StartupEnhancement`uruchamiania hostingu jest .</span><span class="sxs-lookup"><span data-stu-id="092a1-321">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="092a1-322">Klasa zawierająca kod startowy `StartupEnhancementHostingStartup`hostingu to:</span><span class="sxs-lookup"><span data-stu-id="092a1-322">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="092a1-323">Atrybut `HostingStartup` zazwyczaj znajduje się w pliku klasy `IHostingStartup` implementacji zestawu uruchamiania hostingu.</span><span class="sxs-lookup"><span data-stu-id="092a1-323">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="092a1-324">Odkryj załadowane zestawy startowe hostingu</span><span class="sxs-lookup"><span data-stu-id="092a1-324">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="092a1-325">Aby odkryć załadowane zestawy uruchamiania hostingu, włącz rejestrowanie i sprawdź dzienniki aplikacji.</span><span class="sxs-lookup"><span data-stu-id="092a1-325">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="092a1-326">Błędy, które występują podczas ładowania zestawów są rejestrowane.</span><span class="sxs-lookup"><span data-stu-id="092a1-326">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="092a1-327">Załadowane zestawy uruchamiania hostingu są rejestrowane na poziomie debugowania, a wszystkie błędy są rejestrowane.</span><span class="sxs-lookup"><span data-stu-id="092a1-327">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="092a1-328">Wyłącz automatyczne ładowanie zestawów uruchamiania hostingu</span><span class="sxs-lookup"><span data-stu-id="092a1-328">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="092a1-329">Aby wyłączyć automatyczne ładowanie zestawów uruchamiania hostingu, należy użyć jednego z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="092a1-329">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="092a1-330">Aby zapobiec załadunku wszystkich zestawów uruchamiania `1`hostingu, ustaw jeden z następujących opcji na: `true`</span><span class="sxs-lookup"><span data-stu-id="092a1-330">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>
  * <span data-ttu-id="092a1-331">Zapobiegaj ustawieniu konfiguracji hosta [hosta uruchamiania hostingu.](xref:fundamentals/host/web-host#prevent-hosting-startup)</span><span class="sxs-lookup"><span data-stu-id="092a1-331">[Prevent Hosting Startup](xref:fundamentals/host/web-host#prevent-hosting-startup) host configuration setting.</span></span>
  * <span data-ttu-id="092a1-332">`ASPNETCORE_PREVENTHOSTINGSTARTUP`zmienną środowiskową.</span><span class="sxs-lookup"><span data-stu-id="092a1-332">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>
* <span data-ttu-id="092a1-333">Aby zapobiec załadunku określonych zestawów uruchamiania hostingu, ustaw jeden z następujących ciągów rozdzielanych średnikami zestawów uruchamiania hostingu, aby wykluczyć podczas uruchamiania:</span><span class="sxs-lookup"><span data-stu-id="092a1-333">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>
  * <span data-ttu-id="092a1-334">Ustawienie konfiguracji hosta [hosta wykluczeń uruchamiania hostingu.](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies)</span><span class="sxs-lookup"><span data-stu-id="092a1-334">[Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies) host configuration setting.</span></span>
  * <span data-ttu-id="092a1-335">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`zmienną środowiskową.</span><span class="sxs-lookup"><span data-stu-id="092a1-335">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="092a1-336">Jeśli ustawiono zarówno ustawienie konfiguracji hosta, jak i zmienną środowiskową, ustawienie hosta steruje zachowaniem.</span><span class="sxs-lookup"><span data-stu-id="092a1-336">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="092a1-337">Wyłączenie zestawów uruchamiania hostingu przy użyciu ustawienia hosta lub zmiennej środowiskowej wyłącza zestaw globalnie i może wyłączyć kilka cech aplikacji.</span><span class="sxs-lookup"><span data-stu-id="092a1-337">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="092a1-338">Projekt</span><span class="sxs-lookup"><span data-stu-id="092a1-338">Project</span></span>

<span data-ttu-id="092a1-339">Utwórz uruchamianie hostingu z jednym z następujących typów projektów:</span><span class="sxs-lookup"><span data-stu-id="092a1-339">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="092a1-340">Biblioteka klas</span><span class="sxs-lookup"><span data-stu-id="092a1-340">Class library</span></span>](#class-library)
* [<span data-ttu-id="092a1-341">Aplikacja konsoli bez punktu wejścia</span><span class="sxs-lookup"><span data-stu-id="092a1-341">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="092a1-342">Biblioteka klas</span><span class="sxs-lookup"><span data-stu-id="092a1-342">Class library</span></span>

<span data-ttu-id="092a1-343">Rozszerzenie uruchamiania hostingu można zapewnić w bibliotece klas.</span><span class="sxs-lookup"><span data-stu-id="092a1-343">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="092a1-344">Biblioteka zawiera `HostingStartup` atrybut.</span><span class="sxs-lookup"><span data-stu-id="092a1-344">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="092a1-345">[Przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) zawiera aplikację Razor Pages, *HostingStartupApp*i bibliotekę *klas, HostingStartupLibrary*.</span><span class="sxs-lookup"><span data-stu-id="092a1-345">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="092a1-346">Biblioteka klas:</span><span class="sxs-lookup"><span data-stu-id="092a1-346">The class library:</span></span>

* <span data-ttu-id="092a1-347">Zawiera klasę uruchamiania `ServiceKeyInjection`hostingu, `IHostingStartup`która implementuje .</span><span class="sxs-lookup"><span data-stu-id="092a1-347">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="092a1-348">`ServiceKeyInjection`dodaje parę ciągów usług do konfiguracji aplikacji przy użyciu dostawcy konfiguracji w pamięci ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span><span class="sxs-lookup"><span data-stu-id="092a1-348">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>
* <span data-ttu-id="092a1-349">Zawiera `HostingStartup` atrybut identyfikujący obszar nazw i klasę uruchamiania hostingu.</span><span class="sxs-lookup"><span data-stu-id="092a1-349">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="092a1-350">Metoda klasy używa do <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> dodawania ulepszeń do aplikacji. `ServiceKeyInjection` <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*></span><span class="sxs-lookup"><span data-stu-id="092a1-350">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

<span data-ttu-id="092a1-351">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="092a1-351">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="092a1-352">Strona Indeks aplikacji odczytuje i renderuje wartości konfiguracji dla dwóch kluczy ustawionych przez zestaw uruchamiania biblioteki klas hostingu:</span><span class="sxs-lookup"><span data-stu-id="092a1-352">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="092a1-353">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="092a1-353">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="092a1-354">[Przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) zawiera również projekt pakietu NuGet, który zapewnia oddzielne uruchamianie hostingu, *HostingStartupPackage*.</span><span class="sxs-lookup"><span data-stu-id="092a1-354">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="092a1-355">Pakiet ma te same cechy biblioteki klas opisane wcześniej.</span><span class="sxs-lookup"><span data-stu-id="092a1-355">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="092a1-356">Pakiet:</span><span class="sxs-lookup"><span data-stu-id="092a1-356">The package:</span></span>

* <span data-ttu-id="092a1-357">Zawiera klasę uruchamiania `ServiceKeyInjection`hostingu, `IHostingStartup`która implementuje .</span><span class="sxs-lookup"><span data-stu-id="092a1-357">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="092a1-358">`ServiceKeyInjection`dodaje parę ciągów usług do konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="092a1-358">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="092a1-359">Zawiera `HostingStartup` atrybut.</span><span class="sxs-lookup"><span data-stu-id="092a1-359">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="092a1-360">*HostingStartupPackage/ServiceKeyInjection.cs*:</span><span class="sxs-lookup"><span data-stu-id="092a1-360">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="092a1-361">Strona Indeks aplikacji odczytuje i renderuje wartości konfiguracji dla dwóch kluczy ustawionych przez zestaw uruchamiania hostowania pakietu:</span><span class="sxs-lookup"><span data-stu-id="092a1-361">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="092a1-362">*HostingStartupApp/Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="092a1-362">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="092a1-363">Aplikacja konsoli bez punktu wejścia</span><span class="sxs-lookup"><span data-stu-id="092a1-363">Console app without an entry point</span></span>

<span data-ttu-id="092a1-364">*Takie podejście jest dostępne tylko dla aplikacji .NET Core, a nie .NET Framework.*</span><span class="sxs-lookup"><span data-stu-id="092a1-364">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="092a1-365">Dynamiczne wzmocnienie uruchamiania hostingu, które nie wymaga odwołania do czasu kompilacji dla aktywacji, `HostingStartup` może być podane w aplikacji konsoli bez punktu wejścia, który zawiera atrybut.</span><span class="sxs-lookup"><span data-stu-id="092a1-365">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="092a1-366">Publikowanie aplikacji konsoli tworzy zestaw uruchamiania hostingu, który może być zużywany z magazynu środowiska wykonawczego.</span><span class="sxs-lookup"><span data-stu-id="092a1-366">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="092a1-367">W tym procesie jest używana aplikacja konsoli bez punktu wejścia, ponieważ:</span><span class="sxs-lookup"><span data-stu-id="092a1-367">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="092a1-368">Plik zależności jest wymagany do korzystania z uruchamiania hostingu w zestawie uruchamiania hostingu.</span><span class="sxs-lookup"><span data-stu-id="092a1-368">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="092a1-369">Plik zależności jest uruchamianym zasobem aplikacji, który jest produkowany przez publikowanie aplikacji, a nie biblioteki.</span><span class="sxs-lookup"><span data-stu-id="092a1-369">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="092a1-370">Nie można dodać biblioteki bezpośrednio do [magazynu pakietów środowiska wykonawczego,](/dotnet/core/deploying/runtime-store)co wymaga projektu możliwego do uruchomienia, który jest przeznaczony dla udostępnionego środowiska uruchomieniowego.</span><span class="sxs-lookup"><span data-stu-id="092a1-370">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="092a1-371">W tworzeniu dynamicznego uruchamiania hostingu:</span><span class="sxs-lookup"><span data-stu-id="092a1-371">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="092a1-372">Zestaw uruchamiania hostingu jest tworzony z aplikacji konsoli bez punktu wejścia, który:</span><span class="sxs-lookup"><span data-stu-id="092a1-372">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="092a1-373">Zawiera klasę, która `IHostingStartup` zawiera implementację.</span><span class="sxs-lookup"><span data-stu-id="092a1-373">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="092a1-374">Zawiera [atrybut HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) do `IHostingStartup` identyfikowania klasy implementacji.</span><span class="sxs-lookup"><span data-stu-id="092a1-374">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="092a1-375">Aplikacja konsoli jest publikowana w celu uzyskania zależności uruchamiania hostingu.</span><span class="sxs-lookup"><span data-stu-id="092a1-375">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="092a1-376">Konsekwencją publikowania aplikacji konsoli jest, że nieużywane zależności są przycinane z pliku zależności.</span><span class="sxs-lookup"><span data-stu-id="092a1-376">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="092a1-377">Plik zależności jest modyfikowany w celu ustawienia lokalizacji środowiska wykonawczego zestawu uruchamiania hostingu.</span><span class="sxs-lookup"><span data-stu-id="092a1-377">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="092a1-378">Zestaw uruchamiania hostingu i jego plik zależności jest umieszczany w magazynie pakietów środowiska wykonawczego.</span><span class="sxs-lookup"><span data-stu-id="092a1-378">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="092a1-379">Aby odkryć zestaw uruchamiania hostingu i jego plik zależności, są one wymienione w parze zmiennych środowiskowych.</span><span class="sxs-lookup"><span data-stu-id="092a1-379">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="092a1-380">Aplikacja konsoli odwołuje się do pakietu [Microsoft.AspNetCore.Hosting.Abstractions:](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/)</span><span class="sxs-lookup"><span data-stu-id="092a1-380">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.csproj)]

<span data-ttu-id="092a1-381">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) atrybut identyfikuje klasę jako `IHostingStartup` implementację do ładowania <xref:Microsoft.AspNetCore.Hosting.IWebHost>i wykonywania podczas tworzenia .</span><span class="sxs-lookup"><span data-stu-id="092a1-381">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span> <span data-ttu-id="092a1-382">W poniższym przykładzie obszar `StartupEnhancement`nazw jest `StartupEnhancementHostingStartup`, a klasa jest:</span><span class="sxs-lookup"><span data-stu-id="092a1-382">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="092a1-383">Klasa implementuje `IHostingStartup`.</span><span class="sxs-lookup"><span data-stu-id="092a1-383">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="092a1-384"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> Metoda klasy używa do <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> dodawania ulepszeń do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="092a1-384">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="092a1-385">`IHostingStartup.Configure`w zestawie uruchamiania hostingu jest `Startup.Configure` wywoływana przez środowisko wykonawcze przed w kodzie użytkownika, co pozwala kod użytkownika zastąpić dowolną konfigurację zapewnianą przez zestaw uruchamiania hostingu.</span><span class="sxs-lookup"><span data-stu-id="092a1-385">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="092a1-386">Podczas tworzenia `IHostingStartup` projektu plik zależności (*.deps.json* `runtime` ) ustawia lokalizację złożenia w folderze *bin:*</span><span class="sxs-lookup"><span data-stu-id="092a1-386">When building an `IHostingStartup` project, the dependencies file (*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="092a1-387">Wyświetlana jest tylko część pliku.</span><span class="sxs-lookup"><span data-stu-id="092a1-387">Only part of the file is shown.</span></span> <span data-ttu-id="092a1-388">Nazwa zestawu w przykładzie jest `StartupEnhancement`.</span><span class="sxs-lookup"><span data-stu-id="092a1-388">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="092a1-389">Konfiguracja zapewniana przez startup hostingu</span><span class="sxs-lookup"><span data-stu-id="092a1-389">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="092a1-390">Istnieją dwa podejścia do obsługi konfiguracji w zależności od tego, czy konfiguracja uruchamiania hostingu ma mieć pierwszeństwo, czy konfiguracja aplikacji ma pierwszeństwo:</span><span class="sxs-lookup"><span data-stu-id="092a1-390">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="092a1-391">Zapewnij konfigurację <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> aplikacji za pomocą, aby <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> załadować konfigurację po wykonaniu delegatów aplikacji.</span><span class="sxs-lookup"><span data-stu-id="092a1-391">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="092a1-392">Konfiguracja uruchamiania hostingu ma pierwszeństwo przed konfiguracją aplikacji przy użyciu tej metody.</span><span class="sxs-lookup"><span data-stu-id="092a1-392">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="092a1-393">Zapewnij konfigurację <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> aplikacji przy użyciu, aby <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> załadować konfigurację przed wykonaniem delegatów aplikacji.</span><span class="sxs-lookup"><span data-stu-id="092a1-393">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="092a1-394">Wartości konfiguracji aplikacji mają pierwszeństwo przed wartościami dostarczonymi przez startup hostingu przy użyciu tej metody.</span><span class="sxs-lookup"><span data-stu-id="092a1-394">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority " +
                    "than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority " +
                "than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="092a1-395">Określanie zestawu uruchamiania hostingu</span><span class="sxs-lookup"><span data-stu-id="092a1-395">Specify the hosting startup assembly</span></span>

<span data-ttu-id="092a1-396">W przypadku uruchamiania hostingu dostarczanego przez bibliotekę klas lub konsolę `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` należy określić nazwę zestawu uruchamiania hostingu w zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="092a1-396">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="092a1-397">Zmienna środowiskowa jest listą zestawów rozdzielanych średnikami.</span><span class="sxs-lookup"><span data-stu-id="092a1-397">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="092a1-398">Tylko zestawy uruchamiania hostingu `HostingStartup` są skanowane w celu uzyskania atrybutu.</span><span class="sxs-lookup"><span data-stu-id="092a1-398">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="092a1-399">Dla przykładowej aplikacji *HostingStartupApp*, aby odkryć startupy hostingu opisane wcześniej, zmienna środowiskowa jest ustawiona na następującą wartość:</span><span class="sxs-lookup"><span data-stu-id="092a1-399">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="092a1-400">Zestaw uruchamiania hostingu można również ustawić przy użyciu ustawienia konfiguracji hosta [zestawy uruchamiania hostingu.](xref:fundamentals/host/web-host#hosting-startup-assemblies)</span><span class="sxs-lookup"><span data-stu-id="092a1-400">A hosting startup assembly can also be set using the [Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) host configuration setting.</span></span>

<span data-ttu-id="092a1-401">Gdy istnieje wiele zestawów uruchamiania hostingu, ich <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> metody są wykonywane w kolejności, w którym są wymienione zestawy.</span><span class="sxs-lookup"><span data-stu-id="092a1-401">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="092a1-402">Aktywacja</span><span class="sxs-lookup"><span data-stu-id="092a1-402">Activation</span></span>

<span data-ttu-id="092a1-403">Opcje aktywacji uruchamiania hostingu to:</span><span class="sxs-lookup"><span data-stu-id="092a1-403">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="092a1-404">[Aktywacja magazynu](#runtime-store) &ndash; środowiska uruchomieniowego nie wymaga odwołania do czasu kompilacji do aktywacji.</span><span class="sxs-lookup"><span data-stu-id="092a1-404">[Runtime store](#runtime-store) &ndash; Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="092a1-405">Przykładowa aplikacja umieszcza zestaw uruchamiania hostingu i zależności plików do folderu, *wdrożenia*, aby ułatwić wdrożenie uruchamiania hostingu w środowisku multimachine.</span><span class="sxs-lookup"><span data-stu-id="092a1-405">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="092a1-406">Folder *wdrażania* zawiera również skrypt programu PowerShell, który tworzy lub modyfikuje zmienne środowiskowe w systemie wdrażania, aby włączyć uruchamianie hostingu.</span><span class="sxs-lookup"><span data-stu-id="092a1-406">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="092a1-407">Odwołanie do czasu kompilacji wymagane do aktywacji</span><span class="sxs-lookup"><span data-stu-id="092a1-407">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="092a1-408">Pakiet NuGet</span><span class="sxs-lookup"><span data-stu-id="092a1-408">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="092a1-409">Folder pojemnika projektu</span><span class="sxs-lookup"><span data-stu-id="092a1-409">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="092a1-410">Magazyn środowiska uruchomieniowego</span><span class="sxs-lookup"><span data-stu-id="092a1-410">Runtime store</span></span>

<span data-ttu-id="092a1-411">Implementacja uruchamiania hostingu jest umieszczana w [magazynie środowiska wykonawczego](/dotnet/core/deploying/runtime-store).</span><span class="sxs-lookup"><span data-stu-id="092a1-411">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="092a1-412">Odwołanie w czasie kompilacji do zestawu nie jest wymagane przez aplikację rozszerzone.</span><span class="sxs-lookup"><span data-stu-id="092a1-412">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="092a1-413">Po zbudowaniu uruchamiania hostingu magazyn środowiska uruchomieniowego jest generowany przy użyciu pliku projektu manifestu i polecenia [magazynu dotnet.](/dotnet/core/tools/dotnet-store)</span><span class="sxs-lookup"><span data-stu-id="092a1-413">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="092a1-414">W przykładowej aplikacji (projekt*RuntimeStore)* używane jest następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="092a1-414">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="092a1-415">W środowisku uruchomieniowym, aby odnajdywać magazyn środowiska `DOTNET_SHARED_STORE` wykonawczego, lokalizacja magazynu środowiska wykonawczego jest dodawany do zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="092a1-415">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="092a1-416">**Modyfikowanie i umieszczanie pliku zależności uruchamiania hostingu**</span><span class="sxs-lookup"><span data-stu-id="092a1-416">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="092a1-417">Aby uaktywnić ulepszenie bez odwołania do pakietu do `additionalDeps`ulepszenia, należy określić dodatkowe zależności do środowiska wykonawczego za pomocą programu .</span><span class="sxs-lookup"><span data-stu-id="092a1-417">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="092a1-418">`additionalDeps`pozwala na:</span><span class="sxs-lookup"><span data-stu-id="092a1-418">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="092a1-419">Rozszerz wykres biblioteki aplikacji, udostępniając zestaw dodatkowych plików *.deps.json* do scalenia z własnym plikiem *deps.json* aplikacji podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="092a1-419">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="092a1-420">Spraw, aby zestaw uruchamiania hostingu był wykrywalny i ładowalny.</span><span class="sxs-lookup"><span data-stu-id="092a1-420">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="092a1-421">Zalecane podejście do generowania pliku dodatkowych zależności jest:</span><span class="sxs-lookup"><span data-stu-id="092a1-421">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="092a1-422">Wykonaj `dotnet publish` w pliku manifestu magazynu środowiska wykonawczego, do którego odwołuje się poprzednia sekcja.</span><span class="sxs-lookup"><span data-stu-id="092a1-422">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="092a1-423">Usuń odwołanie do manifestu `runtime` z bibliotek i sekcji wynikowego pliku *.deps.json.*</span><span class="sxs-lookup"><span data-stu-id="092a1-423">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="092a1-424">W przykładowym projekcie `store.manifest/1.0.0` właściwość jest `targets` `libraries` usuwana z sekcji i:</span><span class="sxs-lookup"><span data-stu-id="092a1-424">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v2.1",
    "signature": "4ea77c7b75ad1895ae1ea65e6ba2399010514f99"
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v2.1": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp2.1/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-oiQr60vBQW7+nBTmgKLSldj06WNLRTdhOZpAdEbCuapoZ+M2DJH2uQbRLvFT8EGAAv4TAKzNtcztpx5YOgBXQQ==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

<span data-ttu-id="092a1-425">Umieść plik *deps.json* w następującej lokalizacji:</span><span class="sxs-lookup"><span data-stu-id="092a1-425">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="092a1-426">`{ADDITIONAL DEPENDENCIES PATH}`&ndash; Lokalizacja dodana `DOTNET_ADDITIONAL_DEPS` do zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="092a1-426">`{ADDITIONAL DEPENDENCIES PATH}` &ndash; Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="092a1-427">`{SHARED FRAMEWORK NAME}`&ndash; Udostępnione framework wymagane dla tego pliku dodatkowych zależności.</span><span class="sxs-lookup"><span data-stu-id="092a1-427">`{SHARED FRAMEWORK NAME}` &ndash; Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="092a1-428">`{SHARED FRAMEWORK VERSION}`&ndash; Minimalna wersja współdzielonej struktury.</span><span class="sxs-lookup"><span data-stu-id="092a1-428">`{SHARED FRAMEWORK VERSION}` &ndash; Minimum shared framework version.</span></span>
* <span data-ttu-id="092a1-429">`{ENHANCEMENT ASSEMBLY NAME}`&ndash; Nazwa zestawu ulepszenia.</span><span class="sxs-lookup"><span data-stu-id="092a1-429">`{ENHANCEMENT ASSEMBLY NAME}` &ndash; The enhancement's assembly name.</span></span>

<span data-ttu-id="092a1-430">W przykładowej aplikacji (projekt*RuntimeStore)* plik dodatkowych zależności jest umieszczany w następującej lokalizacji:</span><span class="sxs-lookup"><span data-stu-id="092a1-430">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/2.1.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="092a1-431">W środowisku uruchomieniowym, aby odnajdywać lokalizację `DOTNET_ADDITIONAL_DEPS` magazynu środowiska wykonawczego, dodatkowa lokalizacja pliku zależności jest dodawana do zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="092a1-431">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="092a1-432">W przykładowej aplikacji (Projekt*RuntimeStore)* tworzenie magazynu środowiska wykonawczego i generowanie pliku dodatkowych zależności odbywa się za pomocą skryptu [programu PowerShell.](/powershell/scripting/powershell-scripting)</span><span class="sxs-lookup"><span data-stu-id="092a1-432">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="092a1-433">Aby zapoznać się z przykładami ustawiania zmiennych środowiskowych dla różnych systemów operacyjnych, zobacz [Używanie wielu środowisk](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="092a1-433">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="092a1-434">**Wdrożenie**</span><span class="sxs-lookup"><span data-stu-id="092a1-434">**Deployment**</span></span>

<span data-ttu-id="092a1-435">Aby ułatwić wdrażanie uruchamiania hostingu w środowisku wielomatynowym, przykładowa aplikacja tworzy folder *wdrażania* w opublikowanych danych wyjściowych, który zawiera:</span><span class="sxs-lookup"><span data-stu-id="092a1-435">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="092a1-436">Magazyn środowiska uruchomieniowego uruchamiania hostingu.</span><span class="sxs-lookup"><span data-stu-id="092a1-436">The hosting startup runtime store.</span></span>
* <span data-ttu-id="092a1-437">Plik zależności uruchamiania hostingu.</span><span class="sxs-lookup"><span data-stu-id="092a1-437">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="092a1-438">Skrypt programu PowerShell, który `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`tworzy `DOTNET_SHARED_STORE`lub `DOTNET_ADDITIONAL_DEPS` modyfikuje program , i obsługuje aktywację uruchamiania hostingu.</span><span class="sxs-lookup"><span data-stu-id="092a1-438">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="092a1-439">Uruchom skrypt z administracyjnego wiersza polecenia programu PowerShell w systemie wdrażania.</span><span class="sxs-lookup"><span data-stu-id="092a1-439">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="092a1-440">Pakiet NuGet</span><span class="sxs-lookup"><span data-stu-id="092a1-440">NuGet package</span></span>

<span data-ttu-id="092a1-441">Wzmocnienie uruchamiania hostingu mogą być dostarczane w pakiecie NuGet.</span><span class="sxs-lookup"><span data-stu-id="092a1-441">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="092a1-442">Pakiet ma `HostingStartup` atrybut.</span><span class="sxs-lookup"><span data-stu-id="092a1-442">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="092a1-443">Typy uruchamiania hostingu udostępniane przez pakiet są udostępniane aplikacji przy użyciu jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="092a1-443">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="092a1-444">Rozszerzony plik projektu aplikacji sprawia, że odwołanie do pakietu dla uruchamiania hostingu w pliku projektu aplikacji (odwołanie w czasie kompilacji).</span><span class="sxs-lookup"><span data-stu-id="092a1-444">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="092a1-445">Z odwołaniem w czasie kompilacji w miejscu, zestaw uruchamiania hostingu i wszystkie jego zależności są włączone do pliku zależności aplikacji (*.deps.json*).</span><span class="sxs-lookup"><span data-stu-id="092a1-445">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="092a1-446">Takie podejście ma zastosowanie do pakietu zestawu uruchamiania hostingu opublikowanego [w nuget.org](https://www.nuget.org/).</span><span class="sxs-lookup"><span data-stu-id="092a1-446">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="092a1-447">Plik zależności uruchamiania hostingu jest udostępniany aplikacji rozszerzonej, zgodnie z opisem w sekcji [Magazyn środowiska wykonawczego](#runtime-store) (bez odwołania w czasie kompilacji).</span><span class="sxs-lookup"><span data-stu-id="092a1-447">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="092a1-448">Aby uzyskać więcej informacji na temat pakietów NuGet i magazynu środowiska wykonawczego, zobacz następujące tematy:</span><span class="sxs-lookup"><span data-stu-id="092a1-448">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="092a1-449">Jak utworzyć pakiet NuGet za pomocą narzędzi wieloplatformowych</span><span class="sxs-lookup"><span data-stu-id="092a1-449">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="092a1-450">Publikowanie pakietów</span><span class="sxs-lookup"><span data-stu-id="092a1-450">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="092a1-451">Magazyn pakietu środowiska uruchomieniowego</span><span class="sxs-lookup"><span data-stu-id="092a1-451">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="092a1-452">Folder pojemnika projektu</span><span class="sxs-lookup"><span data-stu-id="092a1-452">Project bin folder</span></span>

<span data-ttu-id="092a1-453">Ulepszenie uruchamiania hostingu może być dostarczone przez zestaw wdrożony w *pojemniku*w ulepszonej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="092a1-453">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="092a1-454">Typy uruchamiania hostingu udostępniane przez zestaw są udostępniane aplikacji przy użyciu jednego z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="092a1-454">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="092a1-455">Rozszerzony plik projektu aplikacji sprawia, że odwołanie do zestawu do uruchamiania hostingu (odwołanie w czasie kompilacji).</span><span class="sxs-lookup"><span data-stu-id="092a1-455">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="092a1-456">Z odwołaniem w czasie kompilacji w miejscu, zestaw uruchamiania hostingu i wszystkie jego zależności są włączone do pliku zależności aplikacji (*.deps.json*).</span><span class="sxs-lookup"><span data-stu-id="092a1-456">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="092a1-457">Takie podejście ma zastosowanie, gdy scenariusz wdrażania wymaga dokonania odwołania w czasie kompilacji do zestawu uruchamiania hostingu (plik*dll)* i przenoszenia zestawu do:</span><span class="sxs-lookup"><span data-stu-id="092a1-457">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly (*.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="092a1-458">Projekt zużywający.</span><span class="sxs-lookup"><span data-stu-id="092a1-458">The consuming project.</span></span>
  * <span data-ttu-id="092a1-459">Lokalizacja dostępna dla projektu zużywającego.</span><span class="sxs-lookup"><span data-stu-id="092a1-459">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="092a1-460">Plik zależności uruchamiania hostingu jest udostępniany aplikacji rozszerzonej, zgodnie z opisem w sekcji [Magazyn środowiska wykonawczego](#runtime-store) (bez odwołania w czasie kompilacji).</span><span class="sxs-lookup"><span data-stu-id="092a1-460">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="092a1-461">Podczas kierowania na .NET Framework, zestaw jest wczytywany w kontekście obciążenia domyślnego, co w programie .NET Framework oznacza, że zestaw znajduje się w jednej z następujących lokalizacji:</span><span class="sxs-lookup"><span data-stu-id="092a1-461">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="092a1-462">Ścieżka &ndash; podstawowa aplikacji Folder *bin,* w którym znajduje się plik wykonywalny aplikacji (*.exe).*</span><span class="sxs-lookup"><span data-stu-id="092a1-462">Application base path &ndash; The *bin* folder where the app's executable (*.exe*) is located.</span></span>
  * <span data-ttu-id="092a1-463">Globalna pamięć podręczna &ndash; zestawów (GAC) GaC przechowuje zestawy współużytkowane przez kilka aplikacji platformy .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="092a1-463">Global Assembly Cache (GAC) &ndash; The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="092a1-464">Aby uzyskać więcej informacji, zobacz [Jak: Instalowanie zestawu w globalnej pamięci podręcznej zestawów](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) w dokumentacji programu .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="092a1-464">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="092a1-465">Przykładowy kod</span><span class="sxs-lookup"><span data-stu-id="092a1-465">Sample code</span></span>

<span data-ttu-id="092a1-466">[Przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) [(jak pobrać)](xref:index#how-to-download-a-sample)demonstruje scenariusze implementacji uruchamiania hostingu:</span><span class="sxs-lookup"><span data-stu-id="092a1-466">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="092a1-467">Dwa zestawy uruchamiania hostingu (biblioteki klas) ustawiają parę par klucza-wartości konfiguracji w pamięci:</span><span class="sxs-lookup"><span data-stu-id="092a1-467">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="092a1-468">Pakiet NuGet (*HostingStartupPackage*)</span><span class="sxs-lookup"><span data-stu-id="092a1-468">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="092a1-469">Biblioteka klas (*HostingStartupLibrary*)</span><span class="sxs-lookup"><span data-stu-id="092a1-469">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="092a1-470">Uruchamianie hostingu jest aktywowane z zestawu wdrożonego w magazynie uruchomieniowym *(StartupDiagnostics*).</span><span class="sxs-lookup"><span data-stu-id="092a1-470">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="092a1-471">Zestaw dodaje dwa oprogramowanie pośredniczące do aplikacji podczas uruchamiania, które zawierają informacje diagnostyczne na:</span><span class="sxs-lookup"><span data-stu-id="092a1-471">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="092a1-472">Zarejestrowane usługi</span><span class="sxs-lookup"><span data-stu-id="092a1-472">Registered services</span></span>
  * <span data-ttu-id="092a1-473">Adres (schemat, host, podstawa ścieżki, ścieżka, ciąg zapytania)</span><span class="sxs-lookup"><span data-stu-id="092a1-473">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="092a1-474">Połączenie (zdalny adres IP, port zdalny, lokalny adres IP, port lokalny, certyfikat klienta)</span><span class="sxs-lookup"><span data-stu-id="092a1-474">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="092a1-475">Nagłówki żądań</span><span class="sxs-lookup"><span data-stu-id="092a1-475">Request headers</span></span>
  * <span data-ttu-id="092a1-476">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="092a1-476">Environment variables</span></span>

<span data-ttu-id="092a1-477">Aby uruchomić próbkę:</span><span class="sxs-lookup"><span data-stu-id="092a1-477">To run the sample:</span></span>

<span data-ttu-id="092a1-478">**Aktywacja z pakietu NuGet**</span><span class="sxs-lookup"><span data-stu-id="092a1-478">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="092a1-479">Skompiluj pakiet *HostingStartupPackage* za pomocą polecenia [dotnet pack.](/dotnet/core/tools/dotnet-pack)</span><span class="sxs-lookup"><span data-stu-id="092a1-479">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="092a1-480">Dodaj nazwę zestawu pakietu *HostingStartupPackage* do `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="092a1-480">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="092a1-481">Skompiluj i uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="092a1-481">Compile and run the app.</span></span> <span data-ttu-id="092a1-482">Odwołanie do pakietu jest obecny w aplikacji rozszerzonej (odwołanie w czasie kompilacji).</span><span class="sxs-lookup"><span data-stu-id="092a1-482">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="092a1-483">A `<PropertyGroup>` w pliku projektu aplikacji określa dane wyjściowe projektu pakietu (*.. /HostingStartupPackage/bin/debug)* jako źródło pakietu.</span><span class="sxs-lookup"><span data-stu-id="092a1-483">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="092a1-484">Dzięki temu aplikacja może korzystać z pakietu bez przesyłania pakietu do [nuget.org](https://www.nuget.org/). Aby uzyskać więcej informacji, zobacz uwagi w pliku projektu HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="092a1-484">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="092a1-485">Należy zauważyć, że wartości klucza konfiguracji usługi renderowane przez stronę `ServiceKeyInjection.Configure` Indeks odpowiadają wartościom ustawionym przez metodę pakietu.</span><span class="sxs-lookup"><span data-stu-id="092a1-485">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="092a1-486">Jeśli wniesiesz zmiany do projektu *HostingStartupPackage* i ponownie skompilować go, wyczyść lokalne pamięci podręczne pakietu NuGet, aby upewnić się, że *HostingStartupApp* odbiera zaktualizowany pakiet, a nie nieaktualny pakiet z lokalnej pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="092a1-486">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="092a1-487">Aby wyczyścić lokalne bufory NuGet, wykonaj następujące polecenie [dotnet nuget locals:](/dotnet/core/tools/dotnet-nuget-locals)</span><span class="sxs-lookup"><span data-stu-id="092a1-487">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="092a1-488">**Aktywacja z biblioteki klas**</span><span class="sxs-lookup"><span data-stu-id="092a1-488">**Activation from a class library**</span></span>

1. <span data-ttu-id="092a1-489">Skompiluj bibliotekę klas *HostingStartupLibrary* za pomocą polecenia [dotnet build.](/dotnet/core/tools/dotnet-build)</span><span class="sxs-lookup"><span data-stu-id="092a1-489">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="092a1-490">Dodaj nazwę zestawu biblioteki klas *HostingStartupLibrary* do zmiennej środowiskowej. `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="092a1-490">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="092a1-491">*bin*-deploy zestawu biblioteki klas do aplikacji przez skopiowanie *pliku HostingStartupLibrary.dll* z biblioteki klas skompilowanych danych wyjściowych do folderu *bin/debug* aplikacji.</span><span class="sxs-lookup"><span data-stu-id="092a1-491">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="092a1-492">Skompiluj i uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="092a1-492">Compile and run the app.</span></span> <span data-ttu-id="092a1-493">Plik `<ItemGroup>` projektu aplikacji odwołuje się do zestawu biblioteki klas (*.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (odwołanie do czasu kompilacji).</span><span class="sxs-lookup"><span data-stu-id="092a1-493">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="092a1-494">Aby uzyskać więcej informacji, zobacz uwagi w pliku projektu HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="092a1-494">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp2.1\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion>
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="092a1-495">Należy zauważyć, że wartości klucza konfiguracji usługi renderowane przez stronę `ServiceKeyInjection.Configure` Indeks odpowiadają wartościom ustawionym przez metodę biblioteki klas.</span><span class="sxs-lookup"><span data-stu-id="092a1-495">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="092a1-496">**Aktywacja z zestawu wdrożonego w magazynie uruchomieniowym**</span><span class="sxs-lookup"><span data-stu-id="092a1-496">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="092a1-497">Projekt *StartupDiagnostics* używa programu [PowerShell](/powershell/scripting/powershell-scripting) do modyfikowania pliku *StartupDiagnostics.deps.json.*</span><span class="sxs-lookup"><span data-stu-id="092a1-497">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="092a1-498">Program PowerShell jest instalowany domyślnie w systemie Windows, począwszy od systemu Windows 7 z dodatku SP1 i dodatku SP1 dla systemu Windows Server 2008 R2.</span><span class="sxs-lookup"><span data-stu-id="092a1-498">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="092a1-499">Aby uzyskać program PowerShell na innych platformach, zobacz [Instalowanie różnych wersji programu PowerShell](/powershell/scripting/install/installing-powershell).</span><span class="sxs-lookup"><span data-stu-id="092a1-499">To obtain PowerShell on other platforms, see [Installing various versions of PowerShell](/powershell/scripting/install/installing-powershell).</span></span>
1. <span data-ttu-id="092a1-500">Wykonaj skrypt *build.ps1* w folderze *RuntimeStore.*</span><span class="sxs-lookup"><span data-stu-id="092a1-500">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="092a1-501">Skrypt:</span><span class="sxs-lookup"><span data-stu-id="092a1-501">The script:</span></span>
   * <span data-ttu-id="092a1-502">Generuje `StartupDiagnostics` pakiet w folderze *obj\packages.*</span><span class="sxs-lookup"><span data-stu-id="092a1-502">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span>
   * <span data-ttu-id="092a1-503">Generuje magazyn środowiska wykonawczego `StartupDiagnostics` w folderze *magazynu.*</span><span class="sxs-lookup"><span data-stu-id="092a1-503">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="092a1-504">Polecenie `dotnet store` w skrypcie `win7-x64` używa [identyfikatora środowiska wykonawczego (RID)](/dotnet/core/rid-catalog) dla uruchamiania hostingu wdrożonego w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="092a1-504">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="092a1-505">Podczas dostarczania uruchamiania hostingu dla innego środowiska uruchomieniowego, zastąp poprawny rid w wierszu 37 skryptu.</span><span class="sxs-lookup"><span data-stu-id="092a1-505">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="092a1-506">Magazyn środowiska wykonawczego `StartupDiagnostics` zostanie później przeniesiony do magazynu środowiska wykonawczego użytkownika lub systemu na komputerze, na którym zostanie wykorzystany zestaw.</span><span class="sxs-lookup"><span data-stu-id="092a1-506">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span> <span data-ttu-id="092a1-507">Lokalizacja instalacji magazynu uruchomieniowego użytkownika dla `StartupDiagnostics` zestawu to *.dotnet/store/x64/netcoreapp2.2/startupdiagnostics/1.0.0/lib/netcoreapp2.2/StartupDiagnostics.dll*.</span><span class="sxs-lookup"><span data-stu-id="092a1-507">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp2.2/startupdiagnostics/1.0.0/lib/netcoreapp2.2/StartupDiagnostics.dll*.</span></span>
   * <span data-ttu-id="092a1-508">Generuje for `additionalDeps` `StartupDiagnostics` w *folderze additionalDeps.*</span><span class="sxs-lookup"><span data-stu-id="092a1-508">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="092a1-509">Dodatkowe zależności zostaną później przeniesione do dodatkowych zależności użytkownika lub systemu.</span><span class="sxs-lookup"><span data-stu-id="092a1-509">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span> <span data-ttu-id="092a1-510">Lokalizacją `StartupDiagnostics` instalacji dodatkowych zależności użytkownika jest *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/2.2.0/StartupDiagnostics.deps.json*.</span><span class="sxs-lookup"><span data-stu-id="092a1-510">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/2.2.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="092a1-511">Umieszcza plik *deploy.ps1* w folderze *wdrażania.*</span><span class="sxs-lookup"><span data-stu-id="092a1-511">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="092a1-512">Uruchom skrypt *deploy.ps1* w folderze *wdrażania.*</span><span class="sxs-lookup"><span data-stu-id="092a1-512">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="092a1-513">Skrypt dołącza:</span><span class="sxs-lookup"><span data-stu-id="092a1-513">The script appends:</span></span>
   * <span data-ttu-id="092a1-514">`StartupDiagnostics`do `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="092a1-514">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="092a1-515">Ścieżka zależności uruchamiania hostingu (w folderze *wdrażania* projektu `DOTNET_ADDITIONAL_DEPS` RuntimeStore) do zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="092a1-515">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="092a1-516">Ścieżka magazynu środowiska wykonawczego (w folderze *wdrażania* projektu RuntimeStore) do zmiennej środowiskowej. `DOTNET_SHARED_STORE`</span><span class="sxs-lookup"><span data-stu-id="092a1-516">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="092a1-517">Uruchom przykładową aplikację.</span><span class="sxs-lookup"><span data-stu-id="092a1-517">Run the sample app.</span></span>
1. <span data-ttu-id="092a1-518">Poproś `/services` o punkt końcowy, aby wyświetlić zarejestrowane usługi aplikacji.</span><span class="sxs-lookup"><span data-stu-id="092a1-518">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="092a1-519">Poproś `/diag` o punkt końcowy, aby wyświetlić informacje diagnostyczne.</span><span class="sxs-lookup"><span data-stu-id="092a1-519">Request the `/diag` endpoint to see the diagnostic information.</span></span>

::: moniker-end
