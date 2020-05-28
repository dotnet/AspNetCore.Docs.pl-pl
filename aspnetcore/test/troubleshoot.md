---
<span data-ttu-id="23a38-101">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="23a38-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="23a38-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="23a38-102">'Blazor'</span></span>
- <span data-ttu-id="23a38-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="23a38-103">'Identity'</span></span>
- <span data-ttu-id="23a38-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="23a38-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="23a38-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="23a38-105">'Razor'</span></span>
- <span data-ttu-id="23a38-106">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="23a38-106">'SignalR' uid:</span></span> 

---
# <a name="troubleshoot-and-debug-aspnet-core-projects"></a><span data-ttu-id="23a38-107">Rozwiązywanie problemów i debugowanie ASP.NET Core projektów</span><span class="sxs-lookup"><span data-stu-id="23a38-107">Troubleshoot and debug ASP.NET Core projects</span></span>

<span data-ttu-id="23a38-108">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="23a38-108">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="23a38-109">Poniższe linki udostępniają wskazówki dotyczące rozwiązywania problemów:</span><span class="sxs-lookup"><span data-stu-id="23a38-109">The following links provide troubleshooting guidance:</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="23a38-110">Konferencja NDC (Londyn, 2018): diagnozowanie problemów w aplikacjach ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="23a38-110">NDC Conference (London, 2018): Diagnosing issues in ASP.NET Core Applications</span></span>](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [<span data-ttu-id="23a38-111">Blog ASP.NET: Rozwiązywanie problemów z wydajnością ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="23a38-111">ASP.NET Blog: Troubleshooting ASP.NET Core Performance Problems</span></span>](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a><span data-ttu-id="23a38-112">Ostrzeżenia zestaw .NET Core SDK</span><span class="sxs-lookup"><span data-stu-id="23a38-112">.NET Core SDK warnings</span></span>

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a><span data-ttu-id="23a38-113">Są zainstalowane zarówno 32-bitowe, jak i 64-bitowe wersje zestaw .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="23a38-113">Both the 32-bit and 64-bit versions of the .NET Core SDK are installed</span></span>

<span data-ttu-id="23a38-114">W oknie dialogowym **Nowy projekt** dla ASP.NET Core może zostać wyświetlone następujące ostrzeżenie:</span><span class="sxs-lookup"><span data-stu-id="23a38-114">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="23a38-115">Zainstalowane są zarówno 32-bitowe, jak i 64-bitowe wersje zestaw .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="23a38-115">Both 32-bit and 64-bit versions of the .NET Core SDK are installed.</span></span> <span data-ttu-id="23a38-116">Wyświetlane są tylko szablony z wersji 64-bitowych zainstalowanych w lokalizacji "C: \\ Program Files \\ dotnet \\ SDK \\ ".</span><span class="sxs-lookup"><span data-stu-id="23a38-116">Only templates from the 64-bit versions installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>

<span data-ttu-id="23a38-117">To ostrzeżenie jest wyświetlane, gdy są zainstalowane zarówno wersje 32-bitowe (x86), jak i 64-bitowe (x64) [zestaw .NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) .</span><span class="sxs-lookup"><span data-stu-id="23a38-117">This warning appears when both 32-bit (x86) and 64-bit (x64) versions of the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) are installed.</span></span> <span data-ttu-id="23a38-118">Typowe przyczyny instalacji obu wersji obejmują:</span><span class="sxs-lookup"><span data-stu-id="23a38-118">Common reasons both versions may be installed include:</span></span>

* <span data-ttu-id="23a38-119">Instalator zestaw .NET Core SDK został pierwotnie pobrany przy użyciu maszyny 32-bitowej, ale następnie został skopiowany na komputer z systemem 64-bitowym.</span><span class="sxs-lookup"><span data-stu-id="23a38-119">You originally downloaded the .NET Core SDK installer using a 32-bit machine but then copied it across and installed it on a 64-bit machine.</span></span>
* <span data-ttu-id="23a38-120">32-bitowy zestaw .NET Core SDK został zainstalowany przez inną aplikację.</span><span class="sxs-lookup"><span data-stu-id="23a38-120">The 32-bit .NET Core SDK was installed by another application.</span></span>
* <span data-ttu-id="23a38-121">Pobrano i zainstalowano nieprawidłową wersję.</span><span class="sxs-lookup"><span data-stu-id="23a38-121">The wrong version was downloaded and installed.</span></span>

<span data-ttu-id="23a38-122">Odinstaluj 32-bitowy zestaw .NET Core SDK, aby uniknąć tego ostrzeżenia.</span><span class="sxs-lookup"><span data-stu-id="23a38-122">Uninstall the 32-bit .NET Core SDK to prevent this warning.</span></span> <span data-ttu-id="23a38-123">Odinstaluj z **Panelu sterowania**  >  **programy i funkcje**  >  **Odinstaluj lub zmień program**.</span><span class="sxs-lookup"><span data-stu-id="23a38-123">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="23a38-124">Jeśli rozumiesz, dlaczego ostrzeżenie i jego konsekwencje, możesz zignorować to ostrzeżenie.</span><span class="sxs-lookup"><span data-stu-id="23a38-124">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a><span data-ttu-id="23a38-125">Zestaw .NET Core SDK jest zainstalowany w wielu lokalizacjach</span><span class="sxs-lookup"><span data-stu-id="23a38-125">The .NET Core SDK is installed in multiple locations</span></span>

<span data-ttu-id="23a38-126">W oknie dialogowym **Nowy projekt** dla ASP.NET Core może zostać wyświetlone następujące ostrzeżenie:</span><span class="sxs-lookup"><span data-stu-id="23a38-126">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="23a38-127">Zestaw .NET Core SDK jest zainstalowany w wielu lokalizacjach.</span><span class="sxs-lookup"><span data-stu-id="23a38-127">The .NET Core SDK is installed in multiple locations.</span></span> <span data-ttu-id="23a38-128">Wyświetlane są tylko szablony z zestawów SDK zainstalowanych w lokalizacji "C: \\ Program Files \\ dotnet \\ SDK \\ ".</span><span class="sxs-lookup"><span data-stu-id="23a38-128">Only templates from the SDKs installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>

<span data-ttu-id="23a38-129">Ten komunikat jest wyświetlany w przypadku co najmniej jednej instalacji zestaw .NET Core SDK w katalogu poza językiem \*C: \\ Program Files \\ \\ zestawu dotnet SDK \\ \*.</span><span class="sxs-lookup"><span data-stu-id="23a38-129">You see this message when you have at least one installation of the .NET Core SDK in a directory outside of *C:\\Program Files\\dotnet\\sdk\\*.</span></span> <span data-ttu-id="23a38-130">Zwykle zdarza się to, gdy zestaw .NET Core SDK został wdrożony na komputerze przy użyciu funkcji kopiowania/wklejania zamiast Instalatora MSI.</span><span class="sxs-lookup"><span data-stu-id="23a38-130">Usually this happens when the .NET Core SDK has been deployed on a machine using copy/paste instead of the MSI installer.</span></span>

<span data-ttu-id="23a38-131">Odinstaluj wszystkie 32-bitowe zestawy SDK platformy .NET Core i środowiska uruchomieniowe, aby uniknąć tego ostrzeżenia.</span><span class="sxs-lookup"><span data-stu-id="23a38-131">Uninstall all 32-bit .NET Core SDKs and runtimes to prevent this warning.</span></span> <span data-ttu-id="23a38-132">Odinstaluj z **Panelu sterowania**  >  **programy i funkcje**  >  **Odinstaluj lub zmień program**.</span><span class="sxs-lookup"><span data-stu-id="23a38-132">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="23a38-133">Jeśli rozumiesz, dlaczego ostrzeżenie i jego konsekwencje, możesz zignorować to ostrzeżenie.</span><span class="sxs-lookup"><span data-stu-id="23a38-133">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="no-net-core-sdks-were-detected"></a><span data-ttu-id="23a38-134">Nie wykryto żadnych zestawów .NET Core SDK</span><span class="sxs-lookup"><span data-stu-id="23a38-134">No .NET Core SDKs were detected</span></span>

* <span data-ttu-id="23a38-135">W oknie dialogowym **Nowy projekt** programu Visual Studio dla ASP.NET Core może zostać wyświetlone następujące ostrzeżenie:</span><span class="sxs-lookup"><span data-stu-id="23a38-135">In the Visual Studio **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

  > <span data-ttu-id="23a38-136">Nie wykryto żadnych zestawów .NET Core SDK, upewnij się, że są one uwzględnione w zmiennej środowiskowej `PATH` .</span><span class="sxs-lookup"><span data-stu-id="23a38-136">No .NET Core SDKs were detected, ensure they are included in the environment variable `PATH`.</span></span>

* <span data-ttu-id="23a38-137">Gdy wykonujesz `dotnet` polecenie, ostrzeżenie jest wyświetlane jako:</span><span class="sxs-lookup"><span data-stu-id="23a38-137">When executing a `dotnet` command, the warning appears as:</span></span>

  > <span data-ttu-id="23a38-138">Nie można znaleźć żadnych zainstalowanych zestawów SDK dotnet.</span><span class="sxs-lookup"><span data-stu-id="23a38-138">It was not possible to find any installed dotnet SDKs.</span></span>

<span data-ttu-id="23a38-139">Te ostrzeżenia są wyświetlane, gdy zmienna środowiskowa `PATH` nie wskazuje żadnych zestawów SDK platformy .NET Core na komputerze.</span><span class="sxs-lookup"><span data-stu-id="23a38-139">These warnings appear when the environment variable `PATH` doesn't point to any .NET Core SDKs on the machine.</span></span> <span data-ttu-id="23a38-140">Aby rozwiązać ten problem:</span><span class="sxs-lookup"><span data-stu-id="23a38-140">To resolve this problem:</span></span>

* <span data-ttu-id="23a38-141">Zainstaluj zestaw .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="23a38-141">Install the .NET Core SDK.</span></span> <span data-ttu-id="23a38-142">Uzyskaj najnowszy Instalator z [programu .NET downloads](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="23a38-142">Obtain the latest installer from [.NET Downloads](https://dotnet.microsoft.com/download).</span></span>
* <span data-ttu-id="23a38-143">Sprawdź, czy `PATH` zmienna środowiskowa wskazuje lokalizację, w której zainstalowano zestaw SDK ( `C:\Program Files\dotnet\` 64-bitowy/x64 lub `C:\Program Files (x86)\dotnet\` 32-bitowy/x86).</span><span class="sxs-lookup"><span data-stu-id="23a38-143">Verify that the `PATH` environment variable points to the location where the SDK is installed (`C:\Program Files\dotnet\` for 64-bit/x64 or `C:\Program Files (x86)\dotnet\` for 32-bit/x86).</span></span> <span data-ttu-id="23a38-144">Instalator zestawu SDK zazwyczaj ustawia `PATH` .</span><span class="sxs-lookup"><span data-stu-id="23a38-144">The SDK installer normally sets the `PATH`.</span></span> <span data-ttu-id="23a38-145">Zawsze Instaluj te same zestawy SDK i środowiska uruchomieniowe na tym samym komputerze.</span><span class="sxs-lookup"><span data-stu-id="23a38-145">Always install the same bitness SDKs and runtimes on the same machine.</span></span>

### <a name="missing-sdk-after-installing-the-net-core-hosting-bundle"></a><span data-ttu-id="23a38-146">Brak zestawu SDK po zainstalowaniu pakietu hostingu platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="23a38-146">Missing SDK after installing the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="23a38-147">Zainstalowanie [pakietu hostingu platformy .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) modyfikuje, `PATH` kiedy instaluje środowisko uruchomieniowe programu .NET Core w celu wskazywania wersji 32-bitowej (x86) platformy .NET Core ( `C:\Program Files (x86)\dotnet\` ).</span><span class="sxs-lookup"><span data-stu-id="23a38-147">Installing the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) modifies the `PATH` when it installs the .NET Core runtime to point to the 32-bit (x86) version of .NET Core (`C:\Program Files (x86)\dotnet\`).</span></span> <span data-ttu-id="23a38-148">Może to spowodować brak zestawów SDK, gdy zostanie użyte polecenie programu .NET Core 32-bitowe (x86) `dotnet` ([nie wykryto żadnych zestawów SDK dla platformy .NET Core](#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="23a38-148">This can result in missing SDKs when the 32-bit (x86) .NET Core `dotnet` command is used ([No .NET Core SDKs were detected](#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="23a38-149">Aby rozwiązać ten problem, przejdź `C:\Program Files\dotnet\` do pozycji przed `C:\Program Files (x86)\dotnet\` `PATH` .</span><span class="sxs-lookup"><span data-stu-id="23a38-149">To resolve this problem, move `C:\Program Files\dotnet\` to a position before `C:\Program Files (x86)\dotnet\` on the `PATH`.</span></span>

## <a name="obtain-data-from-an-app"></a><span data-ttu-id="23a38-150">Uzyskiwanie danych z aplikacji</span><span class="sxs-lookup"><span data-stu-id="23a38-150">Obtain data from an app</span></span>

<span data-ttu-id="23a38-151">Jeśli aplikacja może odpowiadać na żądania, można uzyskać następujące dane z aplikacji za pomocą oprogramowania pośredniczącego:</span><span class="sxs-lookup"><span data-stu-id="23a38-151">If an app is capable of responding to requests, you can obtain the following data from the app using middleware:</span></span>

* <span data-ttu-id="23a38-152">Żądanie: metoda, schemat, host, pathbase, ścieżka, ciąg zapytania, nagłówki</span><span class="sxs-lookup"><span data-stu-id="23a38-152">Request: Method, scheme, host, pathbase, path, query string, headers</span></span>
* <span data-ttu-id="23a38-153">Połączenie: zdalny adres IP, Port zdalny, lokalny adres IP, port lokalny, certyfikat klienta</span><span class="sxs-lookup"><span data-stu-id="23a38-153">Connection: Remote IP address, remote port, local IP address, local port, client certificate</span></span>
* Identity: Name, display name
* <span data-ttu-id="23a38-154">Ustawienia konfiguracji</span><span class="sxs-lookup"><span data-stu-id="23a38-154">Configuration settings</span></span>
* <span data-ttu-id="23a38-155">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="23a38-155">Environment variables</span></span>

<span data-ttu-id="23a38-156">Umieść poniższy kod [oprogramowania pośredniczącego](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) na początku `Startup.Configure` potoku przetwarzania żądania metody.</span><span class="sxs-lookup"><span data-stu-id="23a38-156">Place the following [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) code at the beginning of the `Startup.Configure` method's request processing pipeline.</span></span> <span data-ttu-id="23a38-157">Środowisko jest sprawdzane przed uruchomieniem oprogramowania pośredniczącego, aby upewnić się, że kod jest wykonywany tylko w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="23a38-157">The environment is checked before the middleware is run to ensure that the code is only executed in the Development environment.</span></span>

<span data-ttu-id="23a38-158">Aby uzyskać środowisko, użyj jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="23a38-158">To obtain the environment, use either of the following approaches:</span></span>

* <span data-ttu-id="23a38-159">Wstrzyknąć `IHostingEnvironment` do `Startup.Configure` metody i Sprawdź środowisko przy użyciu zmiennej lokalnej.</span><span class="sxs-lookup"><span data-stu-id="23a38-159">Inject the `IHostingEnvironment` into the `Startup.Configure` method and check the environment with the local variable.</span></span> <span data-ttu-id="23a38-160">Poniższy przykładowy kod demonstruje takie podejście.</span><span class="sxs-lookup"><span data-stu-id="23a38-160">The following sample code demonstrates this approach.</span></span>

* <span data-ttu-id="23a38-161">Przypisz środowisko do właściwości w `Startup` klasie.</span><span class="sxs-lookup"><span data-stu-id="23a38-161">Assign the environment to a property in the `Startup` class.</span></span> <span data-ttu-id="23a38-162">Sprawdź środowisko przy użyciu właściwości (na przykład `if (Environment.IsDevelopment())` ).</span><span class="sxs-lookup"><span data-stu-id="23a38-162">Check the environment using the property (for example, `if (Environment.IsDevelopment())`).</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, 
    IConfiguration config)
{
    if (env.IsDevelopment())
    {
        app.Run(async (context) =>
        {
            var sb = new StringBuilder();
            var nl = System.Environment.NewLine;
            var rule = string.Concat(nl, new string('-', 40), nl);
            var authSchemeProvider = app.ApplicationServices
                .GetRequiredService<IAuthenticationSchemeProvider>();

            sb.Append($"Request{rule}");
            sb.Append($"{DateTimeOffset.Now}{nl}");
            sb.Append($"{context.Request.Method} {context.Request.Path}{nl}");
            sb.Append($"Scheme: {context.Request.Scheme}{nl}");
            sb.Append($"Host: {context.Request.Headers["Host"]}{nl}");
            sb.Append($"PathBase: {context.Request.PathBase.Value}{nl}");
            sb.Append($"Path: {context.Request.Path.Value}{nl}");
            sb.Append($"Query: {context.Request.QueryString.Value}{nl}{nl}");

            sb.Append($"Connection{rule}");
            sb.Append($"RemoteIp: {context.Connection.RemoteIpAddress}{nl}");
            sb.Append($"RemotePort: {context.Connection.RemotePort}{nl}");
            sb.Append($"LocalIp: {context.Connection.LocalIpAddress}{nl}");
            sb.Append($"LocalPort: {context.Connection.LocalPort}{nl}");
            sb.Append($"ClientCert: {context.Connection.ClientCertificate}{nl}{nl}");

            sb.Append($"Identity{rule}");
            sb.Append($"User: {context.User.Identity.Name}{nl}");
            var scheme = await authSchemeProvider
                .GetSchemeAsync(IISDefaults.AuthenticationScheme);
            sb.Append($"DisplayName: {scheme?.DisplayName}{nl}{nl}");

            sb.Append($"Headers{rule}");
            foreach (var header in context.Request.Headers)
            {
                sb.Append($"{header.Key}: {header.Value}{nl}");
            }
            sb.Append(nl);

            sb.Append($"Websockets{rule}");
            if (context.Features.Get<IHttpUpgradeFeature>() != null)
            {
                sb.Append($"Status: Enabled{nl}{nl}");
            }
            else
            {
                sb.Append($"Status: Disabled{nl}{nl}");
            }

            sb.Append($"Configuration{rule}");
            foreach (var pair in config.AsEnumerable())
            {
                sb.Append($"{pair.Path}: {pair.Value}{nl}");
            }
            sb.Append(nl);

            sb.Append($"Environment Variables{rule}");
            var vars = System.Environment.GetEnvironmentVariables();
            foreach (var key in vars.Keys.Cast<string>().OrderBy(key => key, 
                StringComparer.OrdinalIgnoreCase))
            {
                var value = vars[key];
                sb.Append($"{key}: {value}{nl}");
            }

            context.Response.ContentType = "text/plain";
            await context.Response.WriteAsync(sb.ToString());
        });
    }
}
```

## <a name="debug-aspnet-core-apps"></a><span data-ttu-id="23a38-163">Debuguj ASP.NET Core aplikacje</span><span class="sxs-lookup"><span data-stu-id="23a38-163">Debug ASP.NET Core apps</span></span>

<span data-ttu-id="23a38-164">Poniższe linki zawierają informacje dotyczące debugowania ASP.NET Core aplikacji.</span><span class="sxs-lookup"><span data-stu-id="23a38-164">The following links provide information on debugging ASP.NET Core apps.</span></span>

* [<span data-ttu-id="23a38-165">Debugowanie środowiska ASP Core w systemie Linux</span><span class="sxs-lookup"><span data-stu-id="23a38-165">Debugging ASP Core on Linux</span></span>](https://devblogs.microsoft.com/premier-developer/debugging-asp-core-on-linux-with-visual-studio-2017/)
* [<span data-ttu-id="23a38-166">Debugowanie programu .NET Core w systemie UNIX za pośrednictwem protokołu SSH</span><span class="sxs-lookup"><span data-stu-id="23a38-166">Debugging .NET Core on Unix over SSH</span></span>](https://devblogs.microsoft.com/devops/debugging-net-core-on-unix-over-ssh/)
* [<span data-ttu-id="23a38-167">Szybki Start: debugowanie ASP.NET z debugerem programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="23a38-167">Quickstart: Debug ASP.NET with the Visual Studio debugger</span></span>](/visualstudio/debugger/quickstart-debug-aspnet)
* <span data-ttu-id="23a38-168">Aby uzyskać więcej informacji o debugowaniu, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/2960) w usłudze GitHub.</span><span class="sxs-lookup"><span data-stu-id="23a38-168">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/2960) for more debugging information.</span></span>
