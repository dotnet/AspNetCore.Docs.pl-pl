---
<span data-ttu-id="0a7c2-101">title: Author: Description: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0a7c2-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0a7c2-102">'Blazor'</span></span>
- <span data-ttu-id="0a7c2-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0a7c2-103">'Identity'</span></span>
- <span data-ttu-id="0a7c2-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0a7c2-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="0a7c2-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0a7c2-105">'Razor'</span></span>
- <span data-ttu-id="0a7c2-106">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-106">'SignalR' uid:</span></span> 

---
# <a name="use-hosting-startup-assemblies-in-aspnet-core"></a><span data-ttu-id="0a7c2-107">Używanie zestawów startowych hostingu w ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0a7c2-107">Use hosting startup assemblies in ASP.NET Core</span></span>

<span data-ttu-id="0a7c2-108">Autor [Pavel Krymets](https://github.com/pakrym)</span><span class="sxs-lookup"><span data-stu-id="0a7c2-108">By [Pavel Krymets](https://github.com/pakrym)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0a7c2-109"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup>Implementacja (uruchamianie hostingu) dodaje usprawnienia do aplikacji podczas uruchamiania z zestawu zewnętrznego.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-109">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="0a7c2-110">Na przykład biblioteka zewnętrzna może użyć implementacji uruchamiania hostingu, aby zapewnić dodatkowym dostawcom konfiguracji lub usługom aplikację.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-110">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="0a7c2-111">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0a7c2-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="0a7c2-112">HostingStartup — atrybut</span><span class="sxs-lookup"><span data-stu-id="0a7c2-112">HostingStartup attribute</span></span>

<span data-ttu-id="0a7c2-113">Atrybut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) wskazuje obecność zestawu startowego hostingu do aktywowania w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-113">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="0a7c2-114">Zestaw wpisów lub zestaw zawierający `Startup` klasę jest automatycznie skanowany dla `HostingStartup` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-114">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="0a7c2-115">Lista zestawów do wyszukiwania `HostingStartup` atrybutów jest ładowana w czasie wykonywania z konfiguracji w [WebHostDefaults. HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="0a7c2-115">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span> <span data-ttu-id="0a7c2-116">Lista zestawów do wykluczenia z odnajdywania jest ładowana z [WebHostDefaults. HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="0a7c2-116">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span>

<span data-ttu-id="0a7c2-117">W poniższym przykładzie przestrzeń nazw zestawu startowego hostingu to `StartupEnhancement` .</span><span class="sxs-lookup"><span data-stu-id="0a7c2-117">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="0a7c2-118">Klasa zawierająca kod uruchomienia hostingu `StartupEnhancementHostingStartup` :</span><span class="sxs-lookup"><span data-stu-id="0a7c2-118">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="0a7c2-119">Ten `HostingStartup` atrybut zazwyczaj znajduje się w pliku klasy implementacji zestawu startowego hostingu `IHostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="0a7c2-119">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="0a7c2-120">Odkryj załadowane zestawy uruchamiania hostingu</span><span class="sxs-lookup"><span data-stu-id="0a7c2-120">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="0a7c2-121">Aby odnaleźć załadowane zestawy uruchamiania hostingu, Włącz rejestrowanie i Sprawdź dzienniki aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-121">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="0a7c2-122">Błędy występujące podczas rejestrowania zestawów.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-122">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="0a7c2-123">Załadowane zestawy startowe hostingu są rejestrowane na poziomie debugowania, a wszystkie błędy są rejestrowane.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-123">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="0a7c2-124">Wyłącz automatyczne ładowanie zestawów startowych hostingu</span><span class="sxs-lookup"><span data-stu-id="0a7c2-124">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="0a7c2-125">Aby wyłączyć automatyczne ładowanie zestawów startowych hostingu, należy użyć jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-125">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="0a7c2-126">Aby zapobiec ładowaniu wszystkich początkowych zestawów uruchamiania, należy ustawić jedną z następujących opcji na `true` lub `1` :</span><span class="sxs-lookup"><span data-stu-id="0a7c2-126">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>

  * <span data-ttu-id="0a7c2-127">Zapobiegaj ustawieniu konfiguracji hosta uruchamiania hostingu:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-127">Prevent Hosting Startup host configuration setting:</span></span>

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

  * <span data-ttu-id="0a7c2-128">`ASPNETCORE_PREVENTHOSTINGSTARTUP`Zmienna środowiskowa.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-128">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>

* <span data-ttu-id="0a7c2-129">Aby zapobiec ładowaniu określonych początkowych zestawów uruchamiania, należy ustawić jedną z następujących wartości rozdzielanej średnikami ciąg początkowych zestawów startowych do wykluczenia podczas uruchamiania:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-129">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>

  * <span data-ttu-id="0a7c2-130">Uruchamianie hostingu Wyklucz ustawienia konfiguracja hosta:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-130">Hosting Startup Exclude Assemblies host configuration setting:</span></span>

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

  * <span data-ttu-id="0a7c2-131">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`Zmienna środowiskowa.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-131">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="0a7c2-132">Jeśli ustawienia konfiguracji hosta i zmienna środowiskowa są ustawione, ustawienie hosta steruje zachowaniem.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-132">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="0a7c2-133">Wyłączenie hostingu zestawów startowych przy użyciu ustawienia hosta lub zmiennej środowiskowej wyłącza zestaw globalnie i może wyłączyć kilka cech aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-133">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="0a7c2-134">Project</span><span class="sxs-lookup"><span data-stu-id="0a7c2-134">Project</span></span>

<span data-ttu-id="0a7c2-135">Utwórz uruchamianie hostingu przy użyciu jednego z następujących typów projektów:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-135">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="0a7c2-136">Biblioteka klas</span><span class="sxs-lookup"><span data-stu-id="0a7c2-136">Class library</span></span>](#class-library)
* [<span data-ttu-id="0a7c2-137">Aplikacja konsolowa bez punktu wejścia</span><span class="sxs-lookup"><span data-stu-id="0a7c2-137">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="0a7c2-138">Biblioteka klas</span><span class="sxs-lookup"><span data-stu-id="0a7c2-138">Class library</span></span>

<span data-ttu-id="0a7c2-139">Rozszerzenie uruchamiania hostingu można podać w bibliotece klas.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-139">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="0a7c2-140">Biblioteka zawiera `HostingStartup` atrybut.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-140">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="0a7c2-141">[Przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) zawiera Razor aplikacje Pages, *HostingStartupApp*i Biblioteka klas *HostingStartupLibrary*.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-141">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="0a7c2-142">Biblioteka klas:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-142">The class library:</span></span>

* <span data-ttu-id="0a7c2-143">Zawiera klasę uruchomieniową hostingu, `ServiceKeyInjection` która implementuje `IHostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="0a7c2-143">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="0a7c2-144">`ServiceKeyInjection`Dodaje parę ciągów usługi do konfiguracji aplikacji za pomocą dostawcy konfiguracji w pamięci ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span><span class="sxs-lookup"><span data-stu-id="0a7c2-144">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>
* <span data-ttu-id="0a7c2-145">Zawiera `HostingStartup` atrybut, który identyfikuje przestrzeń nazw i klasę uruchomienia hostingu.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-145">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="0a7c2-146">`ServiceKeyInjection` <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> Metoda klasy używa <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> do dodawania ulepszeń do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-146">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

<span data-ttu-id="0a7c2-147">*HostingStartupLibrary/ServiceKeyInjection. cs*:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-147">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="0a7c2-148">Strona indeksu aplikacji odczytuje i renderuje wartości konfiguracyjne dla dwóch kluczy ustawionych przez zestaw startowy obsługujący bibliotekę klas:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-148">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="0a7c2-149">*HostingStartupApp/Pages/index. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-149">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="0a7c2-150">[Przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) zawiera również projekt pakietu NuGet, który zapewnia oddzielne uruchamianie hostingu, *HostingStartupPackage*.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-150">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="0a7c2-151">Pakiet ma takie same charakterystyki biblioteki klas opisanej wcześniej.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-151">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="0a7c2-152">Pakiet:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-152">The package:</span></span>

* <span data-ttu-id="0a7c2-153">Zawiera klasę uruchomieniową hostingu, `ServiceKeyInjection` która implementuje `IHostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="0a7c2-153">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="0a7c2-154">`ServiceKeyInjection`Dodaje parę ciągów usługi do konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-154">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="0a7c2-155">Zawiera `HostingStartup` atrybut.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-155">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="0a7c2-156">*HostingStartupPackage/ServiceKeyInjection. cs*:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-156">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="0a7c2-157">Strona indeksu aplikacji odczytuje i renderuje wartości konfiguracyjne dla dwóch kluczy ustawionych przez zestaw startowy obsługujący pakiet:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-157">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="0a7c2-158">*HostingStartupApp/Pages/index. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-158">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/3.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="0a7c2-159">Aplikacja konsolowa bez punktu wejścia</span><span class="sxs-lookup"><span data-stu-id="0a7c2-159">Console app without an entry point</span></span>

<span data-ttu-id="0a7c2-160">*Ta metoda jest dostępna tylko dla aplikacji platformy .NET Core, a nie .NET Framework.*</span><span class="sxs-lookup"><span data-stu-id="0a7c2-160">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="0a7c2-161">Rozszerzenie dynamicznego uruchamiania hostingu, które nie wymaga odwołania do czasu kompilowania dla aktywacji, można dostarczyć w aplikacji konsolowej bez punktu wejścia, który zawiera `HostingStartup` atrybut.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-161">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="0a7c2-162">Opublikowanie aplikacji konsolowej tworzy zestaw startowy hostujący, który może być używany z magazynu środowiska uruchomieniowego.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-162">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="0a7c2-163">W tym procesie jest używana aplikacja konsolowa bez punktu wejścia, ponieważ:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-163">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="0a7c2-164">Plik zależności jest wymagany do korzystania z uruchamiania hostingu w zestawie uruchamiania hostingu.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-164">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="0a7c2-165">Plik zależności to zasób aplikacji możliwy do uruchomienia, który jest tworzony przez opublikowanie aplikacji, a nie biblioteki.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-165">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="0a7c2-166">Biblioteki nie można dodać bezpośrednio do [magazynu pakietów środowiska uruchomieniowego](/dotnet/core/deploying/runtime-store), który wymaga projektu możliwy do uruchomienia, który jest przeznaczony dla udostępnionego środowiska uruchomieniowego.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-166">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="0a7c2-167">Podczas tworzenia dynamicznego uruchamiania hostingu:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-167">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="0a7c2-168">Zestaw startowy obsługujący hosting jest tworzony z poziomu aplikacji konsolowej bez punktu wejścia, który:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-168">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="0a7c2-169">Zawiera klasę, która zawiera `IHostingStartup` implementację.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-169">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="0a7c2-170">Zawiera atrybut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) do identyfikacji `IHostingStartup` klasy implementacji.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-170">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="0a7c2-171">Aplikacja konsolowa zostanie opublikowana w celu uzyskania zależności uruchamiania hostingu.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-171">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="0a7c2-172">Wynikiem publikowania aplikacji konsolowej jest to, że nieużywane zależności są przycinane z pliku zależności.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-172">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="0a7c2-173">Plik zależności został zmodyfikowany w celu ustawienia lokalizacji środowiska uruchomieniowego zestawu startowego hostingu.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-173">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="0a7c2-174">Zestaw startowy hostingu i jego plik zależności są umieszczane w magazynie pakietów środowiska uruchomieniowego.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-174">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="0a7c2-175">Aby odnaleźć zestaw startowy hostingu i jego plik zależności, są one wymienione w parze zmiennych środowiskowych.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-175">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="0a7c2-176">Aplikacja konsolowa odwołuje się do pakietu [Microsoft. AspNetCore. hosting. Abstracts](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) :</span><span class="sxs-lookup"><span data-stu-id="0a7c2-176">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.csproj)]

<span data-ttu-id="0a7c2-177">Atrybut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) identyfikuje klasę jako implementację `IHostingStartup` do ładowania i wykonywania podczas kompilowania <xref:Microsoft.AspNetCore.Hosting.IWebHost> .</span><span class="sxs-lookup"><span data-stu-id="0a7c2-177">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span> <span data-ttu-id="0a7c2-178">W poniższym przykładzie przestrzeń nazw ma wartość `StartupEnhancement` , a Klasa to `StartupEnhancementHostingStartup` :</span><span class="sxs-lookup"><span data-stu-id="0a7c2-178">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="0a7c2-179">Implementuje klasę `IHostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="0a7c2-179">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="0a7c2-180"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*>Metoda klasy używa <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> do dodawania ulepszeń do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-180">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="0a7c2-181">`IHostingStartup.Configure`w zestawie startowym hostingu jest wywoływany przez środowisko uruchomieniowe przed `Startup.Configure` kodem użytkownika, co umożliwia kod użytkownika zastępowanie dowolnej konfiguracji podanej przez zestaw startowy hostingu.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-181">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="0a7c2-182">Podczas kompilowania `IHostingStartup` projektu plik zależności (*. deps. JSON*) ustawia `runtime` lokalizację zestawu do folderu *bin* :</span><span class="sxs-lookup"><span data-stu-id="0a7c2-182">When building an `IHostingStartup` project, the dependencies file (*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/3.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="0a7c2-183">Wyświetlana jest tylko część pliku.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-183">Only part of the file is shown.</span></span> <span data-ttu-id="0a7c2-184">Nazwa zestawu w przykładzie to `StartupEnhancement` .</span><span class="sxs-lookup"><span data-stu-id="0a7c2-184">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="0a7c2-185">Konfiguracja dostarczona przez uruchomienie hostingu</span><span class="sxs-lookup"><span data-stu-id="0a7c2-185">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="0a7c2-186">Istnieją dwa podejścia do obsługi konfiguracji w zależności od tego, czy konfiguracja uruchamiania hostingu ma mieć pierwszeństwo, czy konfiguracja aplikacji ma mieć pierwszeństwo:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-186">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="0a7c2-187">Podaj konfigurację aplikacji przy użyciu programu, <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> Aby załadować konfigurację po <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> wykonaniu delegatów aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-187">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="0a7c2-188">Hostowanie konfiguracji uruchamiania ma wyższy priorytet niż konfiguracja aplikacji przy użyciu tego podejścia.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-188">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="0a7c2-189">Podaj konfigurację aplikacji przy użyciu programu, <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> Aby załadować konfigurację przed <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> wykonaniem delegatów aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-189">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="0a7c2-190">Wartości konfiguracyjne aplikacji mają pierwszeństwo przed tymi, które są udostępniane przez uruchamianie hostingu przy użyciu tego podejścia.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-190">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

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

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="0a7c2-191">Określ zestaw startowy hostingu</span><span class="sxs-lookup"><span data-stu-id="0a7c2-191">Specify the hosting startup assembly</span></span>

<span data-ttu-id="0a7c2-192">W przypadku biblioteki klas lub uruchamiania hostingu obsługiwanej przez aplikację konsoli Określ nazwę zestawu startowego hostingu w `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-192">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="0a7c2-193">Zmienna środowiskowa to rozdzielana średnikami lista zestawów.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-193">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="0a7c2-194">Tylko hosting zestawów startowych jest skanowany dla `HostingStartup` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-194">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="0a7c2-195">W przypadku przykładowej aplikacji, *HostingStartupApp*, aby odnaleźć opisane wcześniej uruchomienia hostingu, zmienna środowiskowa jest ustawiona na następującą wartość:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-195">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="0a7c2-196">Zestaw startowy obsługujący hosting można również ustawić przy użyciu ustawienia konfiguracji hosta zestawów uruchamiania hostingu:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-196">A hosting startup assembly can also be set using the Hosting Startup Assemblies host configuration setting:</span></span>

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

<span data-ttu-id="0a7c2-197">Gdy są obecne wiele asemblerów uruchamiania, ich <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> metody są wykonywane w kolejności, w jakiej są wymienione zestawy.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-197">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="0a7c2-198">Uaktywnienie</span><span class="sxs-lookup"><span data-stu-id="0a7c2-198">Activation</span></span>

<span data-ttu-id="0a7c2-199">Opcje hostingu aktywacji uruchamiania są następujące:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-199">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="0a7c2-200">[Magazyn środowiska uruchomieniowego](#runtime-store): aktywacja nie wymaga odwołania do czasu kompilacji na potrzeby aktywacji.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-200">[Runtime store](#runtime-store): Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="0a7c2-201">Przykładowa aplikacja umieszcza zestaw startowy obsługujący i pliki zależności w folderze, *wdrożeniu*, aby ułatwić wdrożenie uruchamiania hostingu w środowisku wielomaszynowym.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-201">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="0a7c2-202">Folder *wdrożenia* zawiera także skrypt programu PowerShell, który tworzy lub modyfikuje zmienne środowiskowe w systemie wdrażania, aby umożliwić uruchamianie hostingu.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-202">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="0a7c2-203">Odwołanie do czasu kompilacji wymagane do aktywacji</span><span class="sxs-lookup"><span data-stu-id="0a7c2-203">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="0a7c2-204">Pakiet NuGet</span><span class="sxs-lookup"><span data-stu-id="0a7c2-204">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="0a7c2-205">Folder bin projektu</span><span class="sxs-lookup"><span data-stu-id="0a7c2-205">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="0a7c2-206">Magazyn środowiska uruchomieniowego</span><span class="sxs-lookup"><span data-stu-id="0a7c2-206">Runtime store</span></span>

<span data-ttu-id="0a7c2-207">Implementacja uruchamiania hostingu jest umieszczana w [magazynie w czasie wykonywania](/dotnet/core/deploying/runtime-store).</span><span class="sxs-lookup"><span data-stu-id="0a7c2-207">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="0a7c2-208">Informacje o odwołaniu do zestawu nie są wymagane przez rozszerzoną aplikację.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-208">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="0a7c2-209">Po skompilowaniu uruchomienia hostingu magazyn środowiska uruchomieniowego jest generowany przy użyciu pliku projektu manifestu i polecenia [magazynu dotnet](/dotnet/core/tools/dotnet-store) .</span><span class="sxs-lookup"><span data-stu-id="0a7c2-209">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="0a7c2-210">W przykładowej aplikacji (projekt*RuntimeStore* ) używane jest następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-210">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="0a7c2-211">Aby środowisko uruchomieniowe wykrywało magazyn środowiska uruchomieniowego, lokalizacja magazynu środowiska uruchomieniowego jest dodawana do `DOTNET_SHARED_STORE` zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-211">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="0a7c2-212">**Modyfikuj i umieść plik zależności uruchamiania hostingu**</span><span class="sxs-lookup"><span data-stu-id="0a7c2-212">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="0a7c2-213">Aby aktywować rozszerzanie bez odwołania do pakietu do rozszerzenia, określ dodatkowe zależności od środowiska uruchomieniowego za pomocą programu `additionalDeps` .</span><span class="sxs-lookup"><span data-stu-id="0a7c2-213">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="0a7c2-214">`additionalDeps`umożliwia:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-214">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="0a7c2-215">Rozwiń Graf biblioteki aplikacji, dostarczając zestaw dodatkowych plików *. deps. JSON* do scalenia z własnym plikiem *. deps. JSON* podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-215">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="0a7c2-216">Ustaw możliwość odnajdywania i ładowania zestawu uruchamiania hostingu.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-216">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="0a7c2-217">Zalecanym podejściem do generowania dodatkowego pliku zależności jest:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-217">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="0a7c2-218">Wykonaj w `dotnet publish` pliku manifestu magazynu środowiska uruchomieniowego, do którego odwołuje się w poprzedniej sekcji.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-218">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="0a7c2-219">Usuń odwołanie do manifestu z bibliotek i `runtime` sekcji pliku z wynikiem *. deps. JSON* .</span><span class="sxs-lookup"><span data-stu-id="0a7c2-219">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="0a7c2-220">W przykładowym projekcie `store.manifest/1.0.0` Właściwość jest usuwana z `targets` `libraries` sekcji i:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-220">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

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

<span data-ttu-id="0a7c2-221">Umieść plik *. deps. JSON* w następującej lokalizacji:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-221">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="0a7c2-222">`{ADDITIONAL DEPENDENCIES PATH}`: Lokalizacja została dodana do `DOTNET_ADDITIONAL_DEPS` zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-222">`{ADDITIONAL DEPENDENCIES PATH}`: Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="0a7c2-223">`{SHARED FRAMEWORK NAME}`: Udostępnione środowisko wymagane dla tego pliku zależności dodatkowych.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-223">`{SHARED FRAMEWORK NAME}`: Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="0a7c2-224">`{SHARED FRAMEWORK VERSION}`: Minimalna udostępniona wersja platformy.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-224">`{SHARED FRAMEWORK VERSION}`: Minimum shared framework version.</span></span>
* <span data-ttu-id="0a7c2-225">`{ENHANCEMENT ASSEMBLY NAME}`: Nazwa zestawu rozszerzeń.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-225">`{ENHANCEMENT ASSEMBLY NAME}`: The enhancement's assembly name.</span></span>

<span data-ttu-id="0a7c2-226">W przykładowej aplikacji (projekt*RuntimeStore* ) plik dodatkowych zależności jest umieszczany w następującej lokalizacji:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-226">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/3.0.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="0a7c2-227">Aby środowisko uruchomieniowe wykrywało lokalizację magazynu środowiska uruchomieniowego, do zmiennej środowiskowej zostanie dodana lokalizacja pliku z dodatkowymi zależnościami `DOTNET_ADDITIONAL_DEPS` .</span><span class="sxs-lookup"><span data-stu-id="0a7c2-227">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="0a7c2-228">W przykładowej aplikacji (projekt*RuntimeStore* ) Kompilowanie magazynu środowiska uruchomieniowego i generowanie pliku dodatkowych zależności odbywa się przy użyciu skryptu [programu PowerShell](/powershell/scripting/powershell-scripting) .</span><span class="sxs-lookup"><span data-stu-id="0a7c2-228">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="0a7c2-229">Przykłady sposobu ustawiania zmiennych środowiskowych dla różnych systemów operacyjnych znajdują się w temacie [Używanie wielu środowisk](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="0a7c2-229">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="0a7c2-230">**Wdrożenie**</span><span class="sxs-lookup"><span data-stu-id="0a7c2-230">**Deployment**</span></span>

<span data-ttu-id="0a7c2-231">Aby ułatwić wdrożenie uruchamiania hostingu w środowisku wielokomputerowym, aplikacja Przykładowa tworzy folder *wdrożenia* w opublikowanych danych wyjściowych zawierający:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-231">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="0a7c2-232">Magazyn środowiska uruchomieniowego uruchamiania hostingu.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-232">The hosting startup runtime store.</span></span>
* <span data-ttu-id="0a7c2-233">Plik zależności uruchamiania hostingu.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-233">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="0a7c2-234">Skrypt programu PowerShell, który tworzy lub modyfikuje `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` , `DOTNET_SHARED_STORE` i `DOTNET_ADDITIONAL_DEPS` w celu obsługi aktywacji uruchamiania hostingu.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-234">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="0a7c2-235">Uruchom skrypt z wiersza polecenia administracyjnego programu PowerShell w systemie wdrażania.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-235">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="0a7c2-236">Pakiet NuGet</span><span class="sxs-lookup"><span data-stu-id="0a7c2-236">NuGet package</span></span>

<span data-ttu-id="0a7c2-237">Rozszerzenie uruchamiania hostingu można dostarczyć w pakiecie NuGet.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-237">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="0a7c2-238">Pakiet ma `HostingStartup` atrybut.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-238">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="0a7c2-239">Typy uruchamiania hostingu udostępniane przez pakiet są udostępniane aplikacji przy użyciu jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-239">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="0a7c2-240">Plik projektu aplikacji rozszerzonej udostępnia odwołanie do pakietu dla uruchomienia hostingu w pliku projektu aplikacji (odwołanie w czasie kompilacji).</span><span class="sxs-lookup"><span data-stu-id="0a7c2-240">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="0a7c2-241">Wraz z odwołaniem w czasie kompilacji, zestaw startowy hostingu i wszystkie jego zależności są zawarte w pliku zależności aplikacji (*. deps. JSON*).</span><span class="sxs-lookup"><span data-stu-id="0a7c2-241">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="0a7c2-242">Takie podejście ma zastosowanie do pakietu zestawu startowego hostingu opublikowanego w [NuGet.org](https://www.nuget.org/).</span><span class="sxs-lookup"><span data-stu-id="0a7c2-242">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="0a7c2-243">Plik zależności uruchamiania hostingu jest udostępniany aplikacji rozszerzonej, zgodnie z opisem w sekcji [Magazyn środowiska uruchomieniowego](#runtime-store) (bez odwołania w czasie kompilacji).</span><span class="sxs-lookup"><span data-stu-id="0a7c2-243">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="0a7c2-244">Aby uzyskać więcej informacji na temat pakietów NuGet i magazynu środowiska uruchomieniowego, zobacz następujące tematy:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-244">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="0a7c2-245">Jak utworzyć pakiet NuGet przy użyciu narzędzi międzyplatformowych</span><span class="sxs-lookup"><span data-stu-id="0a7c2-245">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="0a7c2-246">Publikowanie pakietów</span><span class="sxs-lookup"><span data-stu-id="0a7c2-246">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="0a7c2-247">Magazyn pakietu środowiska uruchomieniowego</span><span class="sxs-lookup"><span data-stu-id="0a7c2-247">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="0a7c2-248">Folder bin projektu</span><span class="sxs-lookup"><span data-stu-id="0a7c2-248">Project bin folder</span></span>

<span data-ttu-id="0a7c2-249">Rozszerzanie uruchamiania hostingu może być dostarczone przez zestaw wdrożony przez *bin*w aplikacji rozszerzonej.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-249">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="0a7c2-250">Typy uruchamiania hostingu udostępniane przez zestaw są udostępniane aplikacji przy użyciu jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-250">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="0a7c2-251">Plik projektu aplikacji rozszerzonej tworzy odwołanie do zestawu do uruchomienia hostingu (odwołanie w czasie kompilacji).</span><span class="sxs-lookup"><span data-stu-id="0a7c2-251">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="0a7c2-252">Wraz z odwołaniem w czasie kompilacji, zestaw startowy hostingu i wszystkie jego zależności są zawarte w pliku zależności aplikacji (*. deps. JSON*).</span><span class="sxs-lookup"><span data-stu-id="0a7c2-252">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="0a7c2-253">Takie podejście ma zastosowanie, gdy scenariusz wdrażania wywoła odwołanie do zestawu uruchamiania hostingu (plik *. dll* ) i przenosząc zestaw do jednego z tych metod:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-253">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly (*.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="0a7c2-254">Projekt zużywający.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-254">The consuming project.</span></span>
  * <span data-ttu-id="0a7c2-255">Lokalizacja dostępna przez projekt zużywający.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-255">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="0a7c2-256">Plik zależności uruchamiania hostingu jest udostępniany aplikacji rozszerzonej, zgodnie z opisem w sekcji [Magazyn środowiska uruchomieniowego](#runtime-store) (bez odwołania w czasie kompilacji).</span><span class="sxs-lookup"><span data-stu-id="0a7c2-256">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="0a7c2-257">Podczas określania .NET Framework, zestaw jest ładowany w domyślnym kontekście ładowania, który na .NET Framework oznacza, że zestaw znajduje się w jednej z następujących lokalizacji:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-257">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="0a7c2-258">Ścieżka bazowa aplikacji: folder *bin* , w którym znajduje się plik wykonywalny (*. exe*) aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-258">Application base path: The *bin* folder where the app's executable (*.exe*) is located.</span></span>
  * <span data-ttu-id="0a7c2-259">Globalna pamięć podręczna zestawów (GAC): Magazyn GAC przechowuje zestawy, które są dostępne dla kilku .NET Framework aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-259">Global Assembly Cache (GAC): The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="0a7c2-260">Aby uzyskać więcej informacji, zobacz [jak: Instalowanie zestawu w globalnej pamięci podręcznej zestawów](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) w dokumentacji .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-260">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="0a7c2-261">Przykładowy kod</span><span class="sxs-lookup"><span data-stu-id="0a7c2-261">Sample code</span></span>

<span data-ttu-id="0a7c2-262">[Przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([jak pobrać](xref:index#how-to-download-a-sample)) pokazuje hosting scenariuszy implementacji uruchamiania:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-262">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="0a7c2-263">Dwa zestawy startowe hostingu (biblioteki klas) ustawiają parę par klucz-wartość konfiguracji w pamięci:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-263">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="0a7c2-264">Pakiet NuGet (*HostingStartupPackage*)</span><span class="sxs-lookup"><span data-stu-id="0a7c2-264">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="0a7c2-265">Biblioteka klas (*HostingStartupLibrary*)</span><span class="sxs-lookup"><span data-stu-id="0a7c2-265">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="0a7c2-266">Uruchamianie hostingu jest aktywowane z zestawu wdrożonego w sklepie uruchomieniowym (*StartupDiagnostics*).</span><span class="sxs-lookup"><span data-stu-id="0a7c2-266">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="0a7c2-267">Zestaw dodaje dwie middlewares do aplikacji podczas uruchamiania, które dostarczają informacji diagnostycznych na:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-267">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="0a7c2-268">Zarejestrowane usługi</span><span class="sxs-lookup"><span data-stu-id="0a7c2-268">Registered services</span></span>
  * <span data-ttu-id="0a7c2-269">Adres (schemat, host, baza ścieżki, ścieżka, ciąg zapytania)</span><span class="sxs-lookup"><span data-stu-id="0a7c2-269">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="0a7c2-270">Połączenie (zdalny adres IP, Port zdalny, lokalny adres IP, port lokalny, certyfikat klienta)</span><span class="sxs-lookup"><span data-stu-id="0a7c2-270">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="0a7c2-271">Nagłówki żądań</span><span class="sxs-lookup"><span data-stu-id="0a7c2-271">Request headers</span></span>
  * <span data-ttu-id="0a7c2-272">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="0a7c2-272">Environment variables</span></span>

<span data-ttu-id="0a7c2-273">Aby uruchomić przykład:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-273">To run the sample:</span></span>

<span data-ttu-id="0a7c2-274">**Aktywacja z pakietu NuGet**</span><span class="sxs-lookup"><span data-stu-id="0a7c2-274">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="0a7c2-275">Skompiluj pakiet *HostingStartupPackage* przy użyciu polecenia [pakietu dotnet Pack](/dotnet/core/tools/dotnet-pack) .</span><span class="sxs-lookup"><span data-stu-id="0a7c2-275">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="0a7c2-276">Dodaj nazwę zestawu pakietu *HostingStartupPackage* do `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-276">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="0a7c2-277">Skompiluj i uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-277">Compile and run the app.</span></span> <span data-ttu-id="0a7c2-278">Odwołanie do pakietu jest obecne w aplikacji rozszerzonej (odwołanie w czasie kompilacji).</span><span class="sxs-lookup"><span data-stu-id="0a7c2-278">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="0a7c2-279">`<PropertyGroup>`W pliku projektu aplikacji określa dane wyjściowe projektu pakietu (*.. /HostingStartupPackage/bin/Debug*) jako źródło pakietu.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-279">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="0a7c2-280">Dzięki temu aplikacja może korzystać z pakietu bez przekazywania pakietu do [NuGet.org](https://www.nuget.org/). Aby uzyskać więcej informacji, zobacz uwagi w pliku projektu HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-280">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="0a7c2-281">Zwróć uwagę, że wartości klucza konfiguracji usługi renderowane przez stronę indeksu są zgodne z wartościami ustawionymi przez `ServiceKeyInjection.Configure` metodę pakietu.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-281">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="0a7c2-282">Jeśli wprowadzisz zmiany w projekcie *HostingStartupPackage* i skompilujesz go ponownie, wyczyść pamięć podręczną pakietów NuGet, aby upewnić się, że *HostingStartupApp* odbierze zaktualizowany pakiet, a nie stary pakiet z lokalnej pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-282">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="0a7c2-283">Aby wyczyścić lokalne pamięci podręczne NuGet, należy wykonać następujące polecenie [locale NuGet programu dotnet](/dotnet/core/tools/dotnet-nuget-locals) :</span><span class="sxs-lookup"><span data-stu-id="0a7c2-283">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="0a7c2-284">**Aktywacja z biblioteki klas**</span><span class="sxs-lookup"><span data-stu-id="0a7c2-284">**Activation from a class library**</span></span>

1. <span data-ttu-id="0a7c2-285">Skompiluj bibliotekę klas *HostingStartupLibrary* za pomocą polecenia [kompilacji dotnet](/dotnet/core/tools/dotnet-build) .</span><span class="sxs-lookup"><span data-stu-id="0a7c2-285">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="0a7c2-286">Dodaj nazwę zestawu *HostingStartupLibrary* biblioteki klas do `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-286">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="0a7c2-287">*bin*— Wdróż zestaw biblioteki klas w aplikacji przez skopiowanie pliku *HostingStartupLibrary. dll* z skompilowanych danych wyjściowych biblioteki klas do folderu *bin/debug* aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-287">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="0a7c2-288">Skompiluj i uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-288">Compile and run the app.</span></span> <span data-ttu-id="0a7c2-289">`<ItemGroup>`Plik projektu aplikacji odwołuje się do zestawu biblioteki klas (*.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*) (odwołanie w czasie kompilacji).</span><span class="sxs-lookup"><span data-stu-id="0a7c2-289">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="0a7c2-290">Aby uzyskać więcej informacji, zobacz uwagi w pliku projektu HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-290">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp3.0\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp3.0\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion> 
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="0a7c2-291">Zwróć uwagę, że wartości klucza konfiguracji usługi renderowane przez stronę indeksu są zgodne z wartościami ustawionymi przez metodę biblioteki klas `ServiceKeyInjection.Configure` .</span><span class="sxs-lookup"><span data-stu-id="0a7c2-291">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="0a7c2-292">**Aktywacja z zestawu wdrożonego w sklepie uruchomieniowym**</span><span class="sxs-lookup"><span data-stu-id="0a7c2-292">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="0a7c2-293">Projekt *StartupDiagnostics* używa [programu PowerShell](/powershell/scripting/powershell-scripting) do zmodyfikowania pliku *StartupDiagnostics. deps. JSON* .</span><span class="sxs-lookup"><span data-stu-id="0a7c2-293">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="0a7c2-294">Program PowerShell jest instalowany domyślnie w systemie Windows, począwszy od systemu Windows 7 z dodatkiem SP1 i Windows Server 2008 R2 z dodatkiem SP1.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-294">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="0a7c2-295">Aby uzyskać program PowerShell na innych platformach, zobacz [Instalowanie różnych wersji programu PowerShell](/powershell/scripting/install/installing-powershell).</span><span class="sxs-lookup"><span data-stu-id="0a7c2-295">To obtain PowerShell on other platforms, see [Installing various versions of PowerShell](/powershell/scripting/install/installing-powershell).</span></span>
1. <span data-ttu-id="0a7c2-296">Wykonaj skrypt *Build. ps1* w folderze *RuntimeStore* .</span><span class="sxs-lookup"><span data-stu-id="0a7c2-296">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="0a7c2-297">Skrypt:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-297">The script:</span></span>
   * <span data-ttu-id="0a7c2-298">Generuje `StartupDiagnostics` pakiet w folderze *obj\packages* .</span><span class="sxs-lookup"><span data-stu-id="0a7c2-298">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span>
   * <span data-ttu-id="0a7c2-299">Generuje magazyn środowiska uruchomieniowego dla `StartupDiagnostics` programu w folderze *Store* .</span><span class="sxs-lookup"><span data-stu-id="0a7c2-299">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="0a7c2-300">`dotnet store`Polecenie w skrypcie używa `win7-x64` [identyfikatora środowiska uruchomieniowego (RID)](/dotnet/core/rid-catalog) do uruchomienia hostingu wdrożonego w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-300">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="0a7c2-301">Podczas zapewniania uruchamiania hostingu dla innego środowiska uruchomieniowego należy zastąpić prawidłowy identyfikator RID w wierszu 37 skryptu.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-301">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="0a7c2-302">Magazyn środowiska uruchomieniowego programu `StartupDiagnostics` będzie później przenoszony do magazynu środowiska uruchomieniowego użytkownika lub systemu na komputerze, na którym zostanie użyty zestaw.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-302">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span> <span data-ttu-id="0a7c2-303">Lokalizacja instalacji magazynu środowiska uruchomieniowego użytkownika dla `StartupDiagnostics` zestawu to *. dotnet/Store/x64/netcoreapp 3.0/startupdiagnostics/1.0.0/lib/netcoreapp 3.0/startupdiagnostics. dll*.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-303">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp3.0/startupdiagnostics/1.0.0/lib/netcoreapp3.0/StartupDiagnostics.dll*.</span></span>
   * <span data-ttu-id="0a7c2-304">Generuje `additionalDeps` dla elementu `StartupDiagnostics` w folderze *additionalDeps* .</span><span class="sxs-lookup"><span data-stu-id="0a7c2-304">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="0a7c2-305">Dodatkowe zależności byłyby później przenoszone do dodatkowych zależności użytkownika lub systemu.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-305">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span> <span data-ttu-id="0a7c2-306">`StartupDiagnostics`Dodatkowa lokalizacja instalacji zależności użytkownika to *. dotnet/x64/AdditionalDeps/StartupDiagnostics/Shared/Microsoft. servicecore. app/3.0.0/StartupDiagnostics. deps. JSON*.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-306">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/3.0.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="0a7c2-307">Umieszcza plik *Deploy. ps1* w folderze *Deployment* .</span><span class="sxs-lookup"><span data-stu-id="0a7c2-307">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="0a7c2-308">Uruchom skrypt *Deploy. ps1* w folderze *Deployment* .</span><span class="sxs-lookup"><span data-stu-id="0a7c2-308">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="0a7c2-309">Dołączenie skryptu:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-309">The script appends:</span></span>
   * <span data-ttu-id="0a7c2-310">`StartupDiagnostics`do `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-310">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="0a7c2-311">Ścieżka zależności uruchamiania hostingu (w folderze *wdrażania* projektu RuntimeStore) do `DOTNET_ADDITIONAL_DEPS` zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-311">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="0a7c2-312">Ścieżka magazynu środowiska uruchomieniowego (w folderze *wdrażania* projektu RuntimeStore) do `DOTNET_SHARED_STORE` zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-312">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="0a7c2-313">Uruchom przykładową aplikację.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-313">Run the sample app.</span></span>
1. <span data-ttu-id="0a7c2-314">Zażądaj `/services` punktu końcowego, aby zobaczyć zarejestrowane usługi aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-314">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="0a7c2-315">Zażądaj `/diag` punktu końcowego, aby wyświetlić informacje diagnostyczne.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-315">Request the `/diag` endpoint to see the diagnostic information.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0a7c2-316"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup>Implementacja (uruchamianie hostingu) dodaje usprawnienia do aplikacji podczas uruchamiania z zestawu zewnętrznego.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-316">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> (hosting startup) implementation adds enhancements to an app at startup from an external assembly.</span></span> <span data-ttu-id="0a7c2-317">Na przykład biblioteka zewnętrzna może użyć implementacji uruchamiania hostingu, aby zapewnić dodatkowym dostawcom konfiguracji lub usługom aplikację.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-317">For example, an external library can use a hosting startup implementation to provide additional configuration providers or services to an app.</span></span>

<span data-ttu-id="0a7c2-318">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0a7c2-318">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="hostingstartup-attribute"></a><span data-ttu-id="0a7c2-319">HostingStartup — atrybut</span><span class="sxs-lookup"><span data-stu-id="0a7c2-319">HostingStartup attribute</span></span>

<span data-ttu-id="0a7c2-320">Atrybut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) wskazuje obecność zestawu startowego hostingu do aktywowania w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-320">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute indicates the presence of a hosting startup assembly to activate at runtime.</span></span>

<span data-ttu-id="0a7c2-321">Zestaw wpisów lub zestaw zawierający `Startup` klasę jest automatycznie skanowany dla `HostingStartup` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-321">The entry assembly or the assembly containing the `Startup` class is automatically scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="0a7c2-322">Lista zestawów do wyszukiwania `HostingStartup` atrybutów jest ładowana w czasie wykonywania z konfiguracji w [WebHostDefaults. HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="0a7c2-322">The list of assemblies to search for `HostingStartup` attributes is loaded at runtime from configuration in the [WebHostDefaults.HostingStartupAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupAssembliesKey).</span></span> <span data-ttu-id="0a7c2-323">Lista zestawów do wykluczenia z odnajdywania jest ładowana z [WebHostDefaults. HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span><span class="sxs-lookup"><span data-stu-id="0a7c2-323">The list of assemblies to exclude from discovery is loaded from the [WebHostDefaults.HostingStartupExcludeAssembliesKey](xref:Microsoft.AspNetCore.Hosting.WebHostDefaults.HostingStartupExcludeAssembliesKey).</span></span> <span data-ttu-id="0a7c2-324">Aby uzyskać więcej informacji, zobacz [host sieci Web: hosting zestawów startowych](xref:fundamentals/host/web-host#hosting-startup-assemblies) i [hosta sieci Web: hosting z wykluczeniem uruchomienia](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span><span class="sxs-lookup"><span data-stu-id="0a7c2-324">For more information, see [Web Host: Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) and [Web Host: Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).</span></span>

<span data-ttu-id="0a7c2-325">W poniższym przykładzie przestrzeń nazw zestawu startowego hostingu to `StartupEnhancement` .</span><span class="sxs-lookup"><span data-stu-id="0a7c2-325">In the following example, the namespace of the hosting startup assembly is `StartupEnhancement`.</span></span> <span data-ttu-id="0a7c2-326">Klasa zawierająca kod uruchomienia hostingu `StartupEnhancementHostingStartup` :</span><span class="sxs-lookup"><span data-stu-id="0a7c2-326">The class containing the hosting startup code is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="0a7c2-327">Ten `HostingStartup` atrybut zazwyczaj znajduje się w pliku klasy implementacji zestawu startowego hostingu `IHostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="0a7c2-327">The `HostingStartup` attribute is typically located in the hosting startup assembly's `IHostingStartup` implementation class file.</span></span>

## <a name="discover-loaded-hosting-startup-assemblies"></a><span data-ttu-id="0a7c2-328">Odkryj załadowane zestawy uruchamiania hostingu</span><span class="sxs-lookup"><span data-stu-id="0a7c2-328">Discover loaded hosting startup assemblies</span></span>

<span data-ttu-id="0a7c2-329">Aby odnaleźć załadowane zestawy uruchamiania hostingu, Włącz rejestrowanie i Sprawdź dzienniki aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-329">To discover loaded hosting startup assemblies, enable logging and check the app's logs.</span></span> <span data-ttu-id="0a7c2-330">Błędy występujące podczas rejestrowania zestawów.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-330">Errors that occur when loading assemblies are logged.</span></span> <span data-ttu-id="0a7c2-331">Załadowane zestawy startowe hostingu są rejestrowane na poziomie debugowania, a wszystkie błędy są rejestrowane.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-331">Loaded hosting startup assemblies are logged at the Debug level, and all errors are logged.</span></span>

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a><span data-ttu-id="0a7c2-332">Wyłącz automatyczne ładowanie zestawów startowych hostingu</span><span class="sxs-lookup"><span data-stu-id="0a7c2-332">Disable automatic loading of hosting startup assemblies</span></span>

<span data-ttu-id="0a7c2-333">Aby wyłączyć automatyczne ładowanie zestawów startowych hostingu, należy użyć jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-333">To disable automatic loading of hosting startup assemblies, use one of the following approaches:</span></span>

* <span data-ttu-id="0a7c2-334">Aby zapobiec ładowaniu wszystkich początkowych zestawów uruchamiania, należy ustawić jedną z następujących opcji na `true` lub `1` :</span><span class="sxs-lookup"><span data-stu-id="0a7c2-334">To prevent all hosting startup assemblies from loading, set one of the following to `true` or `1`:</span></span>
  * <span data-ttu-id="0a7c2-335">[Zapobiegaj](xref:fundamentals/host/web-host#prevent-hosting-startup) ustawieniu konfiguracji hosta uruchamiania hostingu.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-335">[Prevent Hosting Startup](xref:fundamentals/host/web-host#prevent-hosting-startup) host configuration setting.</span></span>
  * <span data-ttu-id="0a7c2-336">`ASPNETCORE_PREVENTHOSTINGSTARTUP`Zmienna środowiskowa.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-336">`ASPNETCORE_PREVENTHOSTINGSTARTUP` environment variable.</span></span>
* <span data-ttu-id="0a7c2-337">Aby zapobiec ładowaniu określonych początkowych zestawów uruchamiania, należy ustawić jedną z następujących wartości rozdzielanej średnikami ciąg początkowych zestawów startowych do wykluczenia podczas uruchamiania:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-337">To prevent specific hosting startup assemblies from loading, set one of the following to a semicolon-delimited string of hosting startup assemblies to exclude at startup:</span></span>
  * <span data-ttu-id="0a7c2-338">[Uruchamianie hostingu Wyklucz](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies) ustawienia konfiguracja hosta.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-338">[Hosting Startup Exclude Assemblies](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies) host configuration setting.</span></span>
  * <span data-ttu-id="0a7c2-339">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`Zmienna środowiskowa.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-339">`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES` environment variable.</span></span>

<span data-ttu-id="0a7c2-340">Jeśli ustawienia konfiguracji hosta i zmienna środowiskowa są ustawione, ustawienie hosta steruje zachowaniem.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-340">If both the host configuration setting and the environment variable are set, the host setting controls the behavior.</span></span>

<span data-ttu-id="0a7c2-341">Wyłączenie hostingu zestawów startowych przy użyciu ustawienia hosta lub zmiennej środowiskowej wyłącza zestaw globalnie i może wyłączyć kilka cech aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-341">Disabling hosting startup assemblies using the host setting or environment variable disables the assembly globally and may disable several characteristics of an app.</span></span>

## <a name="project"></a><span data-ttu-id="0a7c2-342">Project</span><span class="sxs-lookup"><span data-stu-id="0a7c2-342">Project</span></span>

<span data-ttu-id="0a7c2-343">Utwórz uruchamianie hostingu przy użyciu jednego z następujących typów projektów:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-343">Create a hosting startup with either of the following project types:</span></span>

* [<span data-ttu-id="0a7c2-344">Biblioteka klas</span><span class="sxs-lookup"><span data-stu-id="0a7c2-344">Class library</span></span>](#class-library)
* [<span data-ttu-id="0a7c2-345">Aplikacja konsolowa bez punktu wejścia</span><span class="sxs-lookup"><span data-stu-id="0a7c2-345">Console app without an entry point</span></span>](#console-app-without-an-entry-point)

### <a name="class-library"></a><span data-ttu-id="0a7c2-346">Biblioteka klas</span><span class="sxs-lookup"><span data-stu-id="0a7c2-346">Class library</span></span>

<span data-ttu-id="0a7c2-347">Rozszerzenie uruchamiania hostingu można podać w bibliotece klas.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-347">A hosting startup enhancement can be provided in a class library.</span></span> <span data-ttu-id="0a7c2-348">Biblioteka zawiera `HostingStartup` atrybut.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-348">The library contains a `HostingStartup` attribute.</span></span>

<span data-ttu-id="0a7c2-349">[Przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) zawiera Razor aplikacje Pages, *HostingStartupApp*i Biblioteka klas *HostingStartupLibrary*.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-349">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) includes a Razor Pages app, *HostingStartupApp*, and a class library, *HostingStartupLibrary*.</span></span> <span data-ttu-id="0a7c2-350">Biblioteka klas:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-350">The class library:</span></span>

* <span data-ttu-id="0a7c2-351">Zawiera klasę uruchomieniową hostingu, `ServiceKeyInjection` która implementuje `IHostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="0a7c2-351">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="0a7c2-352">`ServiceKeyInjection`Dodaje parę ciągów usługi do konfiguracji aplikacji za pomocą dostawcy konfiguracji w pamięci ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span><span class="sxs-lookup"><span data-stu-id="0a7c2-352">`ServiceKeyInjection` adds a pair of service strings to the app's configuration using the in-memory configuration provider ([AddInMemoryCollection](xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*)).</span></span>
* <span data-ttu-id="0a7c2-353">Zawiera `HostingStartup` atrybut, który identyfikuje przestrzeń nazw i klasę uruchomienia hostingu.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-353">Includes a `HostingStartup` attribute that identifies the hosting startup's namespace and class.</span></span>

<span data-ttu-id="0a7c2-354">`ServiceKeyInjection` <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> Metoda klasy używa <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> do dodawania ulepszeń do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-354">The `ServiceKeyInjection` class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span>

<span data-ttu-id="0a7c2-355">*HostingStartupLibrary/ServiceKeyInjection. cs*:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-355">*HostingStartupLibrary/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="0a7c2-356">Strona indeksu aplikacji odczytuje i renderuje wartości konfiguracyjne dla dwóch kluczy ustawionych przez zestaw startowy obsługujący bibliotekę klas:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-356">The app's Index page reads and renders the configuration values for the two keys set by the class library's hosting startup assembly:</span></span>

<span data-ttu-id="0a7c2-357">*HostingStartupApp/Pages/index. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-357">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

<span data-ttu-id="0a7c2-358">[Przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) zawiera również projekt pakietu NuGet, który zapewnia oddzielne uruchamianie hostingu, *HostingStartupPackage*.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-358">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) also includes a NuGet package project that provides a separate hosting startup, *HostingStartupPackage*.</span></span> <span data-ttu-id="0a7c2-359">Pakiet ma takie same charakterystyki biblioteki klas opisanej wcześniej.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-359">The package has the same characteristics of the class library described earlier.</span></span> <span data-ttu-id="0a7c2-360">Pakiet:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-360">The package:</span></span>

* <span data-ttu-id="0a7c2-361">Zawiera klasę uruchomieniową hostingu, `ServiceKeyInjection` która implementuje `IHostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="0a7c2-361">Contains a hosting startup class, `ServiceKeyInjection`, which implements `IHostingStartup`.</span></span> <span data-ttu-id="0a7c2-362">`ServiceKeyInjection`Dodaje parę ciągów usługi do konfiguracji aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-362">`ServiceKeyInjection` adds a pair of service strings to the app's configuration.</span></span>
* <span data-ttu-id="0a7c2-363">Zawiera `HostingStartup` atrybut.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-363">Includes a `HostingStartup` attribute.</span></span>

<span data-ttu-id="0a7c2-364">*HostingStartupPackage/ServiceKeyInjection. cs*:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-364">*HostingStartupPackage/ServiceKeyInjection.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

<span data-ttu-id="0a7c2-365">Strona indeksu aplikacji odczytuje i renderuje wartości konfiguracyjne dla dwóch kluczy ustawionych przez zestaw startowy obsługujący pakiet:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-365">The app's Index page reads and renders the configuration values for the two keys set by the package's hosting startup assembly:</span></span>

<span data-ttu-id="0a7c2-366">*HostingStartupApp/Pages/index. cshtml. cs*:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-366">*HostingStartupApp/Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a><span data-ttu-id="0a7c2-367">Aplikacja konsolowa bez punktu wejścia</span><span class="sxs-lookup"><span data-stu-id="0a7c2-367">Console app without an entry point</span></span>

<span data-ttu-id="0a7c2-368">*Ta metoda jest dostępna tylko dla aplikacji platformy .NET Core, a nie .NET Framework.*</span><span class="sxs-lookup"><span data-stu-id="0a7c2-368">*This approach is only available for .NET Core apps, not .NET Framework.*</span></span>

<span data-ttu-id="0a7c2-369">Rozszerzenie dynamicznego uruchamiania hostingu, które nie wymaga odwołania do czasu kompilowania dla aktywacji, można dostarczyć w aplikacji konsolowej bez punktu wejścia, który zawiera `HostingStartup` atrybut.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-369">A dynamic hosting startup enhancement that doesn't require a compile-time reference for activation can be provided in a console app without an entry point that contains a `HostingStartup` attribute.</span></span> <span data-ttu-id="0a7c2-370">Opublikowanie aplikacji konsolowej tworzy zestaw startowy hostujący, który może być używany z magazynu środowiska uruchomieniowego.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-370">Publishing the console app produces a hosting startup assembly that can be consumed from the runtime store.</span></span>

<span data-ttu-id="0a7c2-371">W tym procesie jest używana aplikacja konsolowa bez punktu wejścia, ponieważ:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-371">A console app without an entry point is used in this process because:</span></span>

* <span data-ttu-id="0a7c2-372">Plik zależności jest wymagany do korzystania z uruchamiania hostingu w zestawie uruchamiania hostingu.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-372">A dependencies file is required to consume the hosting startup in the hosting startup assembly.</span></span> <span data-ttu-id="0a7c2-373">Plik zależności to zasób aplikacji możliwy do uruchomienia, który jest tworzony przez opublikowanie aplikacji, a nie biblioteki.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-373">A dependencies file is a runnable app asset that's produced by publishing an app, not a library.</span></span>
* <span data-ttu-id="0a7c2-374">Biblioteki nie można dodać bezpośrednio do [magazynu pakietów środowiska uruchomieniowego](/dotnet/core/deploying/runtime-store), który wymaga projektu możliwy do uruchomienia, który jest przeznaczony dla udostępnionego środowiska uruchomieniowego.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-374">A library can't be added directly to the [runtime package store](/dotnet/core/deploying/runtime-store), which requires a runnable project that targets the shared runtime.</span></span>

<span data-ttu-id="0a7c2-375">Podczas tworzenia dynamicznego uruchamiania hostingu:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-375">In the creation of a dynamic hosting startup:</span></span>

* <span data-ttu-id="0a7c2-376">Zestaw startowy obsługujący hosting jest tworzony z poziomu aplikacji konsolowej bez punktu wejścia, który:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-376">A hosting startup assembly is created from the console app without an entry point that:</span></span>
  * <span data-ttu-id="0a7c2-377">Zawiera klasę, która zawiera `IHostingStartup` implementację.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-377">Includes a class that contains the `IHostingStartup` implementation.</span></span>
  * <span data-ttu-id="0a7c2-378">Zawiera atrybut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) do identyfikacji `IHostingStartup` klasy implementacji.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-378">Includes a [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute to identify the `IHostingStartup` implementation class.</span></span>
* <span data-ttu-id="0a7c2-379">Aplikacja konsolowa zostanie opublikowana w celu uzyskania zależności uruchamiania hostingu.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-379">The console app is published to obtain the hosting startup's dependencies.</span></span> <span data-ttu-id="0a7c2-380">Wynikiem publikowania aplikacji konsolowej jest to, że nieużywane zależności są przycinane z pliku zależności.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-380">A consequence of publishing the console app is that unused dependencies are trimmed from the dependencies file.</span></span>
* <span data-ttu-id="0a7c2-381">Plik zależności został zmodyfikowany w celu ustawienia lokalizacji środowiska uruchomieniowego zestawu startowego hostingu.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-381">The dependencies file is modified to set the runtime location of the hosting startup assembly.</span></span>
* <span data-ttu-id="0a7c2-382">Zestaw startowy hostingu i jego plik zależności są umieszczane w magazynie pakietów środowiska uruchomieniowego.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-382">The hosting startup assembly and its dependencies file is placed into the runtime package store.</span></span> <span data-ttu-id="0a7c2-383">Aby odnaleźć zestaw startowy hostingu i jego plik zależności, są one wymienione w parze zmiennych środowiskowych.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-383">To discover the hosting startup assembly and its dependencies file, they're listed in a pair of environment variables.</span></span>

<span data-ttu-id="0a7c2-384">Aplikacja konsolowa odwołuje się do pakietu [Microsoft. AspNetCore. hosting. Abstracts](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) :</span><span class="sxs-lookup"><span data-stu-id="0a7c2-384">The console app references the [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/) package:</span></span>

[!code-xml[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.csproj)]

<span data-ttu-id="0a7c2-385">Atrybut [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) identyfikuje klasę jako implementację `IHostingStartup` do ładowania i wykonywania podczas kompilowania <xref:Microsoft.AspNetCore.Hosting.IWebHost> .</span><span class="sxs-lookup"><span data-stu-id="0a7c2-385">A [HostingStartup](xref:Microsoft.AspNetCore.Hosting.HostingStartupAttribute) attribute identifies a class as an implementation of `IHostingStartup` for loading and execution when building the <xref:Microsoft.AspNetCore.Hosting.IWebHost>.</span></span> <span data-ttu-id="0a7c2-386">W poniższym przykładzie przestrzeń nazw ma wartość `StartupEnhancement` , a Klasa to `StartupEnhancementHostingStartup` :</span><span class="sxs-lookup"><span data-stu-id="0a7c2-386">In the following example, the namespace is `StartupEnhancement`, and the class is `StartupEnhancementHostingStartup`:</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

<span data-ttu-id="0a7c2-387">Implementuje klasę `IHostingStartup` .</span><span class="sxs-lookup"><span data-stu-id="0a7c2-387">A class implements `IHostingStartup`.</span></span> <span data-ttu-id="0a7c2-388"><xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*>Metoda klasy używa <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> do dodawania ulepszeń do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-388">The class's <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> method uses an <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> to add enhancements to an app.</span></span> <span data-ttu-id="0a7c2-389">`IHostingStartup.Configure`w zestawie startowym hostingu jest wywoływany przez środowisko uruchomieniowe przed `Startup.Configure` kodem użytkownika, co umożliwia kod użytkownika zastępowanie dowolnej konfiguracji podanej przez zestaw startowy hostingu.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-389">`IHostingStartup.Configure` in the hosting startup assembly is called by the runtime before `Startup.Configure` in user code, which allows user code to overwrite any configuration provided by the hosting startup assembly.</span></span>

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

<span data-ttu-id="0a7c2-390">Podczas kompilowania `IHostingStartup` projektu plik zależności (*. deps. JSON*) ustawia `runtime` lokalizację zestawu do folderu *bin* :</span><span class="sxs-lookup"><span data-stu-id="0a7c2-390">When building an `IHostingStartup` project, the dependencies file (*.deps.json*) sets the `runtime` location of the assembly to the *bin* folder:</span></span>

[!code-json[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

<span data-ttu-id="0a7c2-391">Wyświetlana jest tylko część pliku.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-391">Only part of the file is shown.</span></span> <span data-ttu-id="0a7c2-392">Nazwa zestawu w przykładzie to `StartupEnhancement` .</span><span class="sxs-lookup"><span data-stu-id="0a7c2-392">The assembly name in the example is `StartupEnhancement`.</span></span>

## <a name="configuration-provided-by-the-hosting-startup"></a><span data-ttu-id="0a7c2-393">Konfiguracja dostarczona przez uruchomienie hostingu</span><span class="sxs-lookup"><span data-stu-id="0a7c2-393">Configuration provided by the hosting startup</span></span>

<span data-ttu-id="0a7c2-394">Istnieją dwa podejścia do obsługi konfiguracji w zależności od tego, czy konfiguracja uruchamiania hostingu ma mieć pierwszeństwo, czy konfiguracja aplikacji ma mieć pierwszeństwo:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-394">There are two approaches to handling configuration depending on whether you want the hosting startup's configuration to take precedence or the app's configuration to take precedence:</span></span>

1. <span data-ttu-id="0a7c2-395">Podaj konfigurację aplikacji przy użyciu programu, <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> Aby załadować konfigurację po <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> wykonaniu delegatów aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-395">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> to load the configuration after the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="0a7c2-396">Hostowanie konfiguracji uruchamiania ma wyższy priorytet niż konfiguracja aplikacji przy użyciu tego podejścia.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-396">Hosting startup configuration takes priority over the app's configuration using this approach.</span></span>
1. <span data-ttu-id="0a7c2-397">Podaj konfigurację aplikacji przy użyciu programu, <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> Aby załadować konfigurację przed <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> wykonaniem delegatów aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-397">Provide configuration to the app using <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> to load the configuration before the app's <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> delegates execute.</span></span> <span data-ttu-id="0a7c2-398">Wartości konfiguracyjne aplikacji mają pierwszeństwo przed tymi, które są udostępniane przez uruchamianie hostingu przy użyciu tego podejścia.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-398">The app's configuration values take priority over those provided by the hosting startup using this approach.</span></span>

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

## <a name="specify-the-hosting-startup-assembly"></a><span data-ttu-id="0a7c2-399">Określ zestaw startowy hostingu</span><span class="sxs-lookup"><span data-stu-id="0a7c2-399">Specify the hosting startup assembly</span></span>

<span data-ttu-id="0a7c2-400">W przypadku biblioteki klas lub uruchamiania hostingu obsługiwanej przez aplikację konsoli Określ nazwę zestawu startowego hostingu w `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-400">For either a class library- or console app-supplied hosting startup, specify the hosting startup assembly's name in the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span> <span data-ttu-id="0a7c2-401">Zmienna środowiskowa to rozdzielana średnikami lista zestawów.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-401">The environment variable is a semicolon-delimited list of assemblies.</span></span>

<span data-ttu-id="0a7c2-402">Tylko hosting zestawów startowych jest skanowany dla `HostingStartup` atrybutu.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-402">Only hosting startup assemblies are scanned for the `HostingStartup` attribute.</span></span> <span data-ttu-id="0a7c2-403">W przypadku przykładowej aplikacji, *HostingStartupApp*, aby odnaleźć opisane wcześniej uruchomienia hostingu, zmienna środowiskowa jest ustawiona na następującą wartość:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-403">For the sample app, *HostingStartupApp*, to discover the hosting startups described earlier, the environment variable is set to the following value:</span></span>

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

<span data-ttu-id="0a7c2-404">Zestaw startowy obsługujący hosting można również ustawić przy użyciu ustawienia konfiguracji hosta [zestawów startowych](xref:fundamentals/host/web-host#hosting-startup-assemblies) .</span><span class="sxs-lookup"><span data-stu-id="0a7c2-404">A hosting startup assembly can also be set using the [Hosting Startup Assemblies](xref:fundamentals/host/web-host#hosting-startup-assemblies) host configuration setting.</span></span>

<span data-ttu-id="0a7c2-405">Gdy są obecne wiele asemblerów uruchamiania, ich <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> metody są wykonywane w kolejności, w jakiej są wymienione zestawy.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-405">When multiple hosting startup assembles are present, their <xref:Microsoft.AspNetCore.Hosting.IHostingStartup.Configure*> methods are executed in the order that the assemblies are listed.</span></span>

## <a name="activation"></a><span data-ttu-id="0a7c2-406">Uaktywnienie</span><span class="sxs-lookup"><span data-stu-id="0a7c2-406">Activation</span></span>

<span data-ttu-id="0a7c2-407">Opcje hostingu aktywacji uruchamiania są następujące:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-407">Options for hosting startup activation are:</span></span>

* <span data-ttu-id="0a7c2-408">[Magazyn środowiska uruchomieniowego](#runtime-store): aktywacja nie wymaga odwołania do czasu kompilacji na potrzeby aktywacji.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-408">[Runtime store](#runtime-store): Activation doesn't require a compile-time reference for activation.</span></span> <span data-ttu-id="0a7c2-409">Przykładowa aplikacja umieszcza zestaw startowy obsługujący i pliki zależności w folderze, *wdrożeniu*, aby ułatwić wdrożenie uruchamiania hostingu w środowisku wielomaszynowym.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-409">The sample app places the hosting startup assembly and dependencies files into a folder, *deployment*, to facilitate deployment of the hosting startup in a multimachine environment.</span></span> <span data-ttu-id="0a7c2-410">Folder *wdrożenia* zawiera także skrypt programu PowerShell, który tworzy lub modyfikuje zmienne środowiskowe w systemie wdrażania, aby umożliwić uruchamianie hostingu.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-410">The *deployment* folder also includes a PowerShell script that creates or modifies environment variables on the deployment system to enable the hosting startup.</span></span>
* <span data-ttu-id="0a7c2-411">Odwołanie do czasu kompilacji wymagane do aktywacji</span><span class="sxs-lookup"><span data-stu-id="0a7c2-411">Compile-time reference required for activation</span></span>
  * [<span data-ttu-id="0a7c2-412">Pakiet NuGet</span><span class="sxs-lookup"><span data-stu-id="0a7c2-412">NuGet package</span></span>](#nuget-package)
  * [<span data-ttu-id="0a7c2-413">Folder bin projektu</span><span class="sxs-lookup"><span data-stu-id="0a7c2-413">Project bin folder</span></span>](#project-bin-folder)

### <a name="runtime-store"></a><span data-ttu-id="0a7c2-414">Magazyn środowiska uruchomieniowego</span><span class="sxs-lookup"><span data-stu-id="0a7c2-414">Runtime store</span></span>

<span data-ttu-id="0a7c2-415">Implementacja uruchamiania hostingu jest umieszczana w [magazynie w czasie wykonywania](/dotnet/core/deploying/runtime-store).</span><span class="sxs-lookup"><span data-stu-id="0a7c2-415">The hosting startup implementation is placed in the [runtime store](/dotnet/core/deploying/runtime-store).</span></span> <span data-ttu-id="0a7c2-416">Informacje o odwołaniu do zestawu nie są wymagane przez rozszerzoną aplikację.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-416">A compile-time reference to the assembly isn't required by the enhanced app.</span></span>

<span data-ttu-id="0a7c2-417">Po skompilowaniu uruchomienia hostingu magazyn środowiska uruchomieniowego jest generowany przy użyciu pliku projektu manifestu i polecenia [magazynu dotnet](/dotnet/core/tools/dotnet-store) .</span><span class="sxs-lookup"><span data-stu-id="0a7c2-417">After the hosting startup is built, a runtime store is generated using the manifest project file and the [dotnet store](/dotnet/core/tools/dotnet-store) command.</span></span>

```dotnetcli
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

<span data-ttu-id="0a7c2-418">W przykładowej aplikacji (projekt*RuntimeStore* ) używane jest następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-418">In the sample app (*RuntimeStore* project) the following command is used:</span></span>

```dotnetcli
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

<span data-ttu-id="0a7c2-419">Aby środowisko uruchomieniowe wykrywało magazyn środowiska uruchomieniowego, lokalizacja magazynu środowiska uruchomieniowego jest dodawana do `DOTNET_SHARED_STORE` zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-419">For the runtime to discover the runtime store, the runtime store's location is added to the `DOTNET_SHARED_STORE` environment variable.</span></span>

<span data-ttu-id="0a7c2-420">**Modyfikuj i umieść plik zależności uruchamiania hostingu**</span><span class="sxs-lookup"><span data-stu-id="0a7c2-420">**Modify and place the hosting startup's dependencies file**</span></span>

<span data-ttu-id="0a7c2-421">Aby aktywować rozszerzanie bez odwołania do pakietu do rozszerzenia, określ dodatkowe zależności od środowiska uruchomieniowego za pomocą programu `additionalDeps` .</span><span class="sxs-lookup"><span data-stu-id="0a7c2-421">To activate the enhancement without a package reference to the enhancement, specify additional dependencies to the runtime with `additionalDeps`.</span></span> <span data-ttu-id="0a7c2-422">`additionalDeps`umożliwia:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-422">`additionalDeps` allows you to:</span></span>

* <span data-ttu-id="0a7c2-423">Rozwiń Graf biblioteki aplikacji, dostarczając zestaw dodatkowych plików *. deps. JSON* do scalenia z własnym plikiem *. deps. JSON* podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-423">Extend the app's library graph by providing a set of additional *.deps.json* files to merge with the app's own *.deps.json* file on startup.</span></span>
* <span data-ttu-id="0a7c2-424">Ustaw możliwość odnajdywania i ładowania zestawu uruchamiania hostingu.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-424">Make the hosting startup assembly discoverable and loadable.</span></span>

<span data-ttu-id="0a7c2-425">Zalecanym podejściem do generowania dodatkowego pliku zależności jest:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-425">The recommended approach for generating the additional dependencies file is to:</span></span>

 1. <span data-ttu-id="0a7c2-426">Wykonaj w `dotnet publish` pliku manifestu magazynu środowiska uruchomieniowego, do którego odwołuje się w poprzedniej sekcji.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-426">Execute `dotnet publish` on the runtime store manifest file referenced in the previous section.</span></span>
 1. <span data-ttu-id="0a7c2-427">Usuń odwołanie do manifestu z bibliotek i `runtime` sekcji pliku z wynikiem *. deps. JSON* .</span><span class="sxs-lookup"><span data-stu-id="0a7c2-427">Remove the manifest reference from libraries and the `runtime` section of the resulting *.deps.json* file.</span></span>

<span data-ttu-id="0a7c2-428">W przykładowym projekcie `store.manifest/1.0.0` Właściwość jest usuwana z `targets` `libraries` sekcji i:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-428">In the example project, the `store.manifest/1.0.0` property is removed from the `targets` and `libraries` section:</span></span>

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

<span data-ttu-id="0a7c2-429">Umieść plik *. deps. JSON* w następującej lokalizacji:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-429">Place the *.deps.json* file into the following location:</span></span>

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* <span data-ttu-id="0a7c2-430">`{ADDITIONAL DEPENDENCIES PATH}`: Lokalizacja została dodana do `DOTNET_ADDITIONAL_DEPS` zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-430">`{ADDITIONAL DEPENDENCIES PATH}`: Location added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
* <span data-ttu-id="0a7c2-431">`{SHARED FRAMEWORK NAME}`: Udostępnione środowisko wymagane dla tego pliku zależności dodatkowych.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-431">`{SHARED FRAMEWORK NAME}`: Shared framework required for this additional dependencies file.</span></span>
* <span data-ttu-id="0a7c2-432">`{SHARED FRAMEWORK VERSION}`: Minimalna udostępniona wersja platformy.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-432">`{SHARED FRAMEWORK VERSION}`: Minimum shared framework version.</span></span>
* <span data-ttu-id="0a7c2-433">`{ENHANCEMENT ASSEMBLY NAME}`: Nazwa zestawu rozszerzeń.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-433">`{ENHANCEMENT ASSEMBLY NAME}`: The enhancement's assembly name.</span></span>

<span data-ttu-id="0a7c2-434">W przykładowej aplikacji (projekt*RuntimeStore* ) plik dodatkowych zależności jest umieszczany w następującej lokalizacji:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-434">In the sample app (*RuntimeStore* project), the additional dependencies file is placed into the following location:</span></span>

```
deployment/additionalDeps/shared/Microsoft.AspNetCore.App/2.1.0/StartupDiagnostics.deps.json
```

<span data-ttu-id="0a7c2-435">Aby środowisko uruchomieniowe wykrywało lokalizację magazynu środowiska uruchomieniowego, do zmiennej środowiskowej zostanie dodana lokalizacja pliku z dodatkowymi zależnościami `DOTNET_ADDITIONAL_DEPS` .</span><span class="sxs-lookup"><span data-stu-id="0a7c2-435">For runtime to discover the runtime store location, the additional dependencies file location is added to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>

<span data-ttu-id="0a7c2-436">W przykładowej aplikacji (projekt*RuntimeStore* ) Kompilowanie magazynu środowiska uruchomieniowego i generowanie pliku dodatkowych zależności odbywa się przy użyciu skryptu [programu PowerShell](/powershell/scripting/powershell-scripting) .</span><span class="sxs-lookup"><span data-stu-id="0a7c2-436">In the sample app (*RuntimeStore* project), building the runtime store and generating the additional dependencies file is accomplished using a [PowerShell](/powershell/scripting/powershell-scripting) script.</span></span>

<span data-ttu-id="0a7c2-437">Przykłady sposobu ustawiania zmiennych środowiskowych dla różnych systemów operacyjnych znajdują się w temacie [Używanie wielu środowisk](xref:fundamentals/environments).</span><span class="sxs-lookup"><span data-stu-id="0a7c2-437">For examples of how to set environment variables for various operating systems, see [Use multiple environments](xref:fundamentals/environments).</span></span>

<span data-ttu-id="0a7c2-438">**Wdrożenie**</span><span class="sxs-lookup"><span data-stu-id="0a7c2-438">**Deployment**</span></span>

<span data-ttu-id="0a7c2-439">Aby ułatwić wdrożenie uruchamiania hostingu w środowisku wielokomputerowym, aplikacja Przykładowa tworzy folder *wdrożenia* w opublikowanych danych wyjściowych zawierający:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-439">To facilitate the deployment of a hosting startup in a multimachine environment, the sample app creates a *deployment* folder in published output that contains:</span></span>

* <span data-ttu-id="0a7c2-440">Magazyn środowiska uruchomieniowego uruchamiania hostingu.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-440">The hosting startup runtime store.</span></span>
* <span data-ttu-id="0a7c2-441">Plik zależności uruchamiania hostingu.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-441">The hosting startup dependencies file.</span></span>
* <span data-ttu-id="0a7c2-442">Skrypt programu PowerShell, który tworzy lub modyfikuje `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` , `DOTNET_SHARED_STORE` i `DOTNET_ADDITIONAL_DEPS` w celu obsługi aktywacji uruchamiania hostingu.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-442">A PowerShell script that creates or modifies the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE`, and `DOTNET_ADDITIONAL_DEPS` to support the activation of the hosting startup.</span></span> <span data-ttu-id="0a7c2-443">Uruchom skrypt z wiersza polecenia administracyjnego programu PowerShell w systemie wdrażania.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-443">Run the script from an administrative PowerShell command prompt on the deployment system.</span></span>

### <a name="nuget-package"></a><span data-ttu-id="0a7c2-444">Pakiet NuGet</span><span class="sxs-lookup"><span data-stu-id="0a7c2-444">NuGet package</span></span>

<span data-ttu-id="0a7c2-445">Rozszerzenie uruchamiania hostingu można dostarczyć w pakiecie NuGet.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-445">A hosting startup enhancement can be provided in a NuGet package.</span></span> <span data-ttu-id="0a7c2-446">Pakiet ma `HostingStartup` atrybut.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-446">The package has a `HostingStartup` attribute.</span></span> <span data-ttu-id="0a7c2-447">Typy uruchamiania hostingu udostępniane przez pakiet są udostępniane aplikacji przy użyciu jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-447">The hosting startup types provided by the package are made available to the app using either of the following approaches:</span></span>

* <span data-ttu-id="0a7c2-448">Plik projektu aplikacji rozszerzonej udostępnia odwołanie do pakietu dla uruchomienia hostingu w pliku projektu aplikacji (odwołanie w czasie kompilacji).</span><span class="sxs-lookup"><span data-stu-id="0a7c2-448">The enhanced app's project file makes a package reference for the hosting startup in the app's project file (a compile-time reference).</span></span> <span data-ttu-id="0a7c2-449">Wraz z odwołaniem w czasie kompilacji, zestaw startowy hostingu i wszystkie jego zależności są zawarte w pliku zależności aplikacji (*. deps. JSON*).</span><span class="sxs-lookup"><span data-stu-id="0a7c2-449">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="0a7c2-450">Takie podejście ma zastosowanie do pakietu zestawu startowego hostingu opublikowanego w [NuGet.org](https://www.nuget.org/).</span><span class="sxs-lookup"><span data-stu-id="0a7c2-450">This approach applies to a hosting startup assembly package published to [nuget.org](https://www.nuget.org/).</span></span>
* <span data-ttu-id="0a7c2-451">Plik zależności uruchamiania hostingu jest udostępniany aplikacji rozszerzonej, zgodnie z opisem w sekcji [Magazyn środowiska uruchomieniowego](#runtime-store) (bez odwołania w czasie kompilacji).</span><span class="sxs-lookup"><span data-stu-id="0a7c2-451">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>

<span data-ttu-id="0a7c2-452">Aby uzyskać więcej informacji na temat pakietów NuGet i magazynu środowiska uruchomieniowego, zobacz następujące tematy:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-452">For more information on NuGet packages and the runtime store, see the following topics:</span></span>

* [<span data-ttu-id="0a7c2-453">Jak utworzyć pakiet NuGet przy użyciu narzędzi międzyplatformowych</span><span class="sxs-lookup"><span data-stu-id="0a7c2-453">How to Create a NuGet Package with Cross Platform Tools</span></span>](/dotnet/core/deploying/creating-nuget-packages)
* [<span data-ttu-id="0a7c2-454">Publikowanie pakietów</span><span class="sxs-lookup"><span data-stu-id="0a7c2-454">Publishing packages</span></span>](/nuget/create-packages/publish-a-package)
* [<span data-ttu-id="0a7c2-455">Magazyn pakietu środowiska uruchomieniowego</span><span class="sxs-lookup"><span data-stu-id="0a7c2-455">Runtime package store</span></span>](/dotnet/core/deploying/runtime-store)

### <a name="project-bin-folder"></a><span data-ttu-id="0a7c2-456">Folder bin projektu</span><span class="sxs-lookup"><span data-stu-id="0a7c2-456">Project bin folder</span></span>

<span data-ttu-id="0a7c2-457">Rozszerzanie uruchamiania hostingu może być dostarczone przez zestaw wdrożony przez *bin*w aplikacji rozszerzonej.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-457">A hosting startup enhancement can be provided by a *bin*-deployed assembly in the enhanced app.</span></span> <span data-ttu-id="0a7c2-458">Typy uruchamiania hostingu udostępniane przez zestaw są udostępniane aplikacji przy użyciu jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-458">The hosting startup types provided by the assembly are made available to the app using one of the following approaches:</span></span>

* <span data-ttu-id="0a7c2-459">Plik projektu aplikacji rozszerzonej tworzy odwołanie do zestawu do uruchomienia hostingu (odwołanie w czasie kompilacji).</span><span class="sxs-lookup"><span data-stu-id="0a7c2-459">The enhanced app's project file makes an assembly reference to the hosting startup (a compile-time reference).</span></span> <span data-ttu-id="0a7c2-460">Wraz z odwołaniem w czasie kompilacji, zestaw startowy hostingu i wszystkie jego zależności są zawarte w pliku zależności aplikacji (*. deps. JSON*).</span><span class="sxs-lookup"><span data-stu-id="0a7c2-460">With the compile-time reference in place, the hosting startup assembly and all of its dependencies are incorporated into the app's dependency file (*.deps.json*).</span></span> <span data-ttu-id="0a7c2-461">Takie podejście ma zastosowanie, gdy scenariusz wdrażania wywoła odwołanie do zestawu uruchamiania hostingu (plik *. dll* ) i przenosząc zestaw do jednego z tych metod:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-461">This approach applies when the deployment scenario calls for making a compile-time reference to the hosting startup's assembly (*.dll* file) and moving the assembly to either:</span></span>
  * <span data-ttu-id="0a7c2-462">Projekt zużywający.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-462">The consuming project.</span></span>
  * <span data-ttu-id="0a7c2-463">Lokalizacja dostępna przez projekt zużywający.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-463">A location accessible by the consuming project.</span></span>
* <span data-ttu-id="0a7c2-464">Plik zależności uruchamiania hostingu jest udostępniany aplikacji rozszerzonej, zgodnie z opisem w sekcji [Magazyn środowiska uruchomieniowego](#runtime-store) (bez odwołania w czasie kompilacji).</span><span class="sxs-lookup"><span data-stu-id="0a7c2-464">The hosting startup's dependencies file is made available to the enhanced app as described in the [Runtime store](#runtime-store) section (without a compile-time reference).</span></span>
* <span data-ttu-id="0a7c2-465">Podczas określania .NET Framework, zestaw jest ładowany w domyślnym kontekście ładowania, który na .NET Framework oznacza, że zestaw znajduje się w jednej z następujących lokalizacji:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-465">When targeting the .NET Framework, the assembly is loadable in the default load context, which on .NET Framework means that the assembly is located at either of the following locations:</span></span>
  * <span data-ttu-id="0a7c2-466">Ścieżka bazowa aplikacji: folder *bin* , w którym znajduje się plik wykonywalny (*. exe*) aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-466">Application base path: The *bin* folder where the app's executable (*.exe*) is located.</span></span>
  * <span data-ttu-id="0a7c2-467">Globalna pamięć podręczna zestawów (GAC): Magazyn GAC przechowuje zestawy, które są dostępne dla kilku .NET Framework aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-467">Global Assembly Cache (GAC): The GAC stores assemblies that several .NET Framework apps share.</span></span> <span data-ttu-id="0a7c2-468">Aby uzyskać więcej informacji, zobacz [jak: Instalowanie zestawu w globalnej pamięci podręcznej zestawów](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) w dokumentacji .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-468">For more information, see [How to: Install an assembly into the global assembly cache](/dotnet/framework/app-domains/how-to-install-an-assembly-into-the-gac) in the .NET Framework documentation.</span></span>

## <a name="sample-code"></a><span data-ttu-id="0a7c2-469">Przykładowy kod</span><span class="sxs-lookup"><span data-stu-id="0a7c2-469">Sample code</span></span>

<span data-ttu-id="0a7c2-470">[Przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([jak pobrać](xref:index#how-to-download-a-sample)) pokazuje hosting scenariuszy implementacji uruchamiania:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-470">The [sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([how to download](xref:index#how-to-download-a-sample)) demonstrates hosting startup implementation scenarios:</span></span>

* <span data-ttu-id="0a7c2-471">Dwa zestawy startowe hostingu (biblioteki klas) ustawiają parę par klucz-wartość konfiguracji w pamięci:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-471">Two hosting startup assemblies (class libraries) set a pair of in-memory configuration key-value pairs each:</span></span>
  * <span data-ttu-id="0a7c2-472">Pakiet NuGet (*HostingStartupPackage*)</span><span class="sxs-lookup"><span data-stu-id="0a7c2-472">NuGet package (*HostingStartupPackage*)</span></span>
  * <span data-ttu-id="0a7c2-473">Biblioteka klas (*HostingStartupLibrary*)</span><span class="sxs-lookup"><span data-stu-id="0a7c2-473">Class library (*HostingStartupLibrary*)</span></span>
* <span data-ttu-id="0a7c2-474">Uruchamianie hostingu jest aktywowane z zestawu wdrożonego w sklepie uruchomieniowym (*StartupDiagnostics*).</span><span class="sxs-lookup"><span data-stu-id="0a7c2-474">A hosting startup is activated from a runtime store-deployed assembly (*StartupDiagnostics*).</span></span> <span data-ttu-id="0a7c2-475">Zestaw dodaje dwie middlewares do aplikacji podczas uruchamiania, które dostarczają informacji diagnostycznych na:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-475">The assembly adds two middlewares to the app at startup that provide diagnostic information on:</span></span>
  * <span data-ttu-id="0a7c2-476">Zarejestrowane usługi</span><span class="sxs-lookup"><span data-stu-id="0a7c2-476">Registered services</span></span>
  * <span data-ttu-id="0a7c2-477">Adres (schemat, host, baza ścieżki, ścieżka, ciąg zapytania)</span><span class="sxs-lookup"><span data-stu-id="0a7c2-477">Address (scheme, host, path base, path, query string)</span></span>
  * <span data-ttu-id="0a7c2-478">Połączenie (zdalny adres IP, Port zdalny, lokalny adres IP, port lokalny, certyfikat klienta)</span><span class="sxs-lookup"><span data-stu-id="0a7c2-478">Connection (remote IP, remote port, local IP, local port, client certificate)</span></span>
  * <span data-ttu-id="0a7c2-479">Nagłówki żądań</span><span class="sxs-lookup"><span data-stu-id="0a7c2-479">Request headers</span></span>
  * <span data-ttu-id="0a7c2-480">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="0a7c2-480">Environment variables</span></span>

<span data-ttu-id="0a7c2-481">Aby uruchomić przykład:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-481">To run the sample:</span></span>

<span data-ttu-id="0a7c2-482">**Aktywacja z pakietu NuGet**</span><span class="sxs-lookup"><span data-stu-id="0a7c2-482">**Activation from a NuGet package**</span></span>

1. <span data-ttu-id="0a7c2-483">Skompiluj pakiet *HostingStartupPackage* przy użyciu polecenia [pakietu dotnet Pack](/dotnet/core/tools/dotnet-pack) .</span><span class="sxs-lookup"><span data-stu-id="0a7c2-483">Compile the *HostingStartupPackage* package with the [dotnet pack](/dotnet/core/tools/dotnet-pack) command.</span></span>
1. <span data-ttu-id="0a7c2-484">Dodaj nazwę zestawu pakietu *HostingStartupPackage* do `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-484">Add the package's assembly name of the *HostingStartupPackage* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="0a7c2-485">Skompiluj i uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-485">Compile and run the app.</span></span> <span data-ttu-id="0a7c2-486">Odwołanie do pakietu jest obecne w aplikacji rozszerzonej (odwołanie w czasie kompilacji).</span><span class="sxs-lookup"><span data-stu-id="0a7c2-486">A package reference is present in the enhanced app (a compile-time reference).</span></span> <span data-ttu-id="0a7c2-487">`<PropertyGroup>`W pliku projektu aplikacji określa dane wyjściowe projektu pakietu (*.. /HostingStartupPackage/bin/Debug*) jako źródło pakietu.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-487">A `<PropertyGroup>` in the app's project file specifies the package project's output (*../HostingStartupPackage/bin/Debug*) as a package source.</span></span> <span data-ttu-id="0a7c2-488">Dzięki temu aplikacja może korzystać z pakietu bez przekazywania pakietu do [NuGet.org](https://www.nuget.org/). Aby uzyskać więcej informacji, zobacz uwagi w pliku projektu HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-488">This allows the app to use the package without uploading the package to [nuget.org](https://www.nuget.org/). For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. <span data-ttu-id="0a7c2-489">Zwróć uwagę, że wartości klucza konfiguracji usługi renderowane przez stronę indeksu są zgodne z wartościami ustawionymi przez `ServiceKeyInjection.Configure` metodę pakietu.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-489">Observe that the service configuration key values rendered by the Index page match the values set by the package's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="0a7c2-490">Jeśli wprowadzisz zmiany w projekcie *HostingStartupPackage* i skompilujesz go ponownie, wyczyść pamięć podręczną pakietów NuGet, aby upewnić się, że *HostingStartupApp* odbierze zaktualizowany pakiet, a nie stary pakiet z lokalnej pamięci podręcznej.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-490">If you make changes to the *HostingStartupPackage* project and recompile it, clear the local NuGet package caches to ensure that the *HostingStartupApp* receives the updated package and not a stale package from the local cache.</span></span> <span data-ttu-id="0a7c2-491">Aby wyczyścić lokalne pamięci podręczne NuGet, należy wykonać następujące polecenie [locale NuGet programu dotnet](/dotnet/core/tools/dotnet-nuget-locals) :</span><span class="sxs-lookup"><span data-stu-id="0a7c2-491">To clear the local NuGet caches, execute the following [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals) command:</span></span>

```dotnetcli
dotnet nuget locals all --clear
```

<span data-ttu-id="0a7c2-492">**Aktywacja z biblioteki klas**</span><span class="sxs-lookup"><span data-stu-id="0a7c2-492">**Activation from a class library**</span></span>

1. <span data-ttu-id="0a7c2-493">Skompiluj bibliotekę klas *HostingStartupLibrary* za pomocą polecenia [kompilacji dotnet](/dotnet/core/tools/dotnet-build) .</span><span class="sxs-lookup"><span data-stu-id="0a7c2-493">Compile the *HostingStartupLibrary* class library with the [dotnet build](/dotnet/core/tools/dotnet-build) command.</span></span>
1. <span data-ttu-id="0a7c2-494">Dodaj nazwę zestawu *HostingStartupLibrary* biblioteki klas do `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-494">Add the class library's assembly name of *HostingStartupLibrary* to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
1. <span data-ttu-id="0a7c2-495">*bin*— Wdróż zestaw biblioteki klas w aplikacji przez skopiowanie pliku *HostingStartupLibrary. dll* z skompilowanych danych wyjściowych biblioteki klas do folderu *bin/debug* aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-495">*bin*-deploy the class library's assembly to the app by copying the *HostingStartupLibrary.dll* file from the class library's compiled output to the app's *bin/Debug* folder.</span></span>
1. <span data-ttu-id="0a7c2-496">Skompiluj i uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-496">Compile and run the app.</span></span> <span data-ttu-id="0a7c2-497">`<ItemGroup>`Plik projektu aplikacji odwołuje się do zestawu biblioteki klas (*.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (odwołanie w czasie kompilacji).</span><span class="sxs-lookup"><span data-stu-id="0a7c2-497">An `<ItemGroup>` in the app's project file references the class library's assembly (*.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (a compile-time reference).</span></span> <span data-ttu-id="0a7c2-498">Aby uzyskać więcej informacji, zobacz uwagi w pliku projektu HostingStartupApp.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-498">For more information, see the notes in the HostingStartupApp's project file.</span></span>

   ```xml
   <ItemGroup>
     <Reference Include=".\\bin\\Debug\\netcoreapp2.1\\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion>
     </Reference>
   </ItemGroup>
   ```

1. <span data-ttu-id="0a7c2-499">Zwróć uwagę, że wartości klucza konfiguracji usługi renderowane przez stronę indeksu są zgodne z wartościami ustawionymi przez metodę biblioteki klas `ServiceKeyInjection.Configure` .</span><span class="sxs-lookup"><span data-stu-id="0a7c2-499">Observe that the service configuration key values rendered by the Index page match the values set by the class library's `ServiceKeyInjection.Configure` method.</span></span>

<span data-ttu-id="0a7c2-500">**Aktywacja z zestawu wdrożonego w sklepie uruchomieniowym**</span><span class="sxs-lookup"><span data-stu-id="0a7c2-500">**Activation from a runtime store-deployed assembly**</span></span>

1. <span data-ttu-id="0a7c2-501">Projekt *StartupDiagnostics* używa [programu PowerShell](/powershell/scripting/powershell-scripting) do zmodyfikowania pliku *StartupDiagnostics. deps. JSON* .</span><span class="sxs-lookup"><span data-stu-id="0a7c2-501">The *StartupDiagnostics* project uses [PowerShell](/powershell/scripting/powershell-scripting) to modify its *StartupDiagnostics.deps.json* file.</span></span> <span data-ttu-id="0a7c2-502">Program PowerShell jest instalowany domyślnie w systemie Windows, począwszy od systemu Windows 7 z dodatkiem SP1 i Windows Server 2008 R2 z dodatkiem SP1.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-502">PowerShell is installed by default on Windows starting with Windows 7 SP1 and Windows Server 2008 R2 SP1.</span></span> <span data-ttu-id="0a7c2-503">Aby uzyskać program PowerShell na innych platformach, zobacz [Instalowanie różnych wersji programu PowerShell](/powershell/scripting/install/installing-powershell).</span><span class="sxs-lookup"><span data-stu-id="0a7c2-503">To obtain PowerShell on other platforms, see [Installing various versions of PowerShell](/powershell/scripting/install/installing-powershell).</span></span>
1. <span data-ttu-id="0a7c2-504">Wykonaj skrypt *Build. ps1* w folderze *RuntimeStore* .</span><span class="sxs-lookup"><span data-stu-id="0a7c2-504">Execute the *build.ps1* script in the *RuntimeStore* folder.</span></span> <span data-ttu-id="0a7c2-505">Skrypt:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-505">The script:</span></span>
   * <span data-ttu-id="0a7c2-506">Generuje `StartupDiagnostics` pakiet w folderze *obj\packages* .</span><span class="sxs-lookup"><span data-stu-id="0a7c2-506">Generates the `StartupDiagnostics` package in the *obj\packages* folder.</span></span>
   * <span data-ttu-id="0a7c2-507">Generuje magazyn środowiska uruchomieniowego dla `StartupDiagnostics` programu w folderze *Store* .</span><span class="sxs-lookup"><span data-stu-id="0a7c2-507">Generates the runtime store for `StartupDiagnostics` in the *store* folder.</span></span> <span data-ttu-id="0a7c2-508">`dotnet store`Polecenie w skrypcie używa `win7-x64` [identyfikatora środowiska uruchomieniowego (RID)](/dotnet/core/rid-catalog) do uruchomienia hostingu wdrożonego w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-508">The `dotnet store` command in the script uses the `win7-x64` [runtime identifier (RID)](/dotnet/core/rid-catalog) for a hosting startup deployed to Windows.</span></span> <span data-ttu-id="0a7c2-509">Podczas zapewniania uruchamiania hostingu dla innego środowiska uruchomieniowego należy zastąpić prawidłowy identyfikator RID w wierszu 37 skryptu.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-509">When providing the hosting startup for a different runtime, substitute the correct RID on line 37 of the script.</span></span> <span data-ttu-id="0a7c2-510">Magazyn środowiska uruchomieniowego programu `StartupDiagnostics` będzie później przenoszony do magazynu środowiska uruchomieniowego użytkownika lub systemu na komputerze, na którym zostanie użyty zestaw.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-510">The runtime store for `StartupDiagnostics` would later be moved to the user's or system's runtime store on the machine where the assembly will be consumed.</span></span> <span data-ttu-id="0a7c2-511">Lokalizacja instalacji magazynu środowiska uruchomieniowego użytkownika dla `StartupDiagnostics` zestawu to *. dotnet/Store/x64/netcoreapp 2.2/startupdiagnostics/1.0.0/lib/netcoreapp 2.2/startupdiagnostics. dll*.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-511">The user runtime store install location for the `StartupDiagnostics` assembly is *.dotnet/store/x64/netcoreapp2.2/startupdiagnostics/1.0.0/lib/netcoreapp2.2/StartupDiagnostics.dll*.</span></span>
   * <span data-ttu-id="0a7c2-512">Generuje `additionalDeps` dla elementu `StartupDiagnostics` w folderze *additionalDeps* .</span><span class="sxs-lookup"><span data-stu-id="0a7c2-512">Generates the `additionalDeps` for `StartupDiagnostics` in the *additionalDeps* folder.</span></span> <span data-ttu-id="0a7c2-513">Dodatkowe zależności byłyby później przenoszone do dodatkowych zależności użytkownika lub systemu.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-513">The additional dependencies would later be moved to the user's or system's additional dependencies.</span></span> <span data-ttu-id="0a7c2-514">`StartupDiagnostics`Dodatkowa lokalizacja instalacji zależności użytkownika to *. dotnet/x64/AdditionalDeps/StartupDiagnostics/Shared/Microsoft. servicecore. App/2.2.0/StartupDiagnostics. deps. JSON*.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-514">The user `StartupDiagnostics` additional dependencies install location is *.dotnet/x64/additionalDeps/StartupDiagnostics/shared/Microsoft.NETCore.App/2.2.0/StartupDiagnostics.deps.json*.</span></span>
   * <span data-ttu-id="0a7c2-515">Umieszcza plik *Deploy. ps1* w folderze *Deployment* .</span><span class="sxs-lookup"><span data-stu-id="0a7c2-515">Places the *deploy.ps1* file in the *deployment* folder.</span></span>
1. <span data-ttu-id="0a7c2-516">Uruchom skrypt *Deploy. ps1* w folderze *Deployment* .</span><span class="sxs-lookup"><span data-stu-id="0a7c2-516">Run the *deploy.ps1* script in the *deployment* folder.</span></span> <span data-ttu-id="0a7c2-517">Dołączenie skryptu:</span><span class="sxs-lookup"><span data-stu-id="0a7c2-517">The script appends:</span></span>
   * <span data-ttu-id="0a7c2-518">`StartupDiagnostics`do `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-518">`StartupDiagnostics` to the `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` environment variable.</span></span>
   * <span data-ttu-id="0a7c2-519">Ścieżka zależności uruchamiania hostingu (w folderze *wdrażania* projektu RuntimeStore) do `DOTNET_ADDITIONAL_DEPS` zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-519">The hosting startup dependencies path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_ADDITIONAL_DEPS` environment variable.</span></span>
   * <span data-ttu-id="0a7c2-520">Ścieżka magazynu środowiska uruchomieniowego (w folderze *wdrażania* projektu RuntimeStore) do `DOTNET_SHARED_STORE` zmiennej środowiskowej.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-520">The runtime store path (in the RuntimeStore project's *deployment* folder) to the `DOTNET_SHARED_STORE` environment variable.</span></span>
1. <span data-ttu-id="0a7c2-521">Uruchom przykładową aplikację.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-521">Run the sample app.</span></span>
1. <span data-ttu-id="0a7c2-522">Zażądaj `/services` punktu końcowego, aby zobaczyć zarejestrowane usługi aplikacji.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-522">Request the `/services` endpoint to see the app's registered services.</span></span> <span data-ttu-id="0a7c2-523">Zażądaj `/diag` punktu końcowego, aby wyświetlić informacje diagnostyczne.</span><span class="sxs-lookup"><span data-stu-id="0a7c2-523">Request the `/diag` endpoint to see the diagnostic information.</span></span>

::: moniker-end
