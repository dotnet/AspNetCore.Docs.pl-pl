---
title: Rozwiązywanie problemów i debugowanie projektów ASP.NET podstawowych
author: Rick-Anderson
description: Zrozumienie i rozwiązywanie problemów z ostrzeżeniami i błędami w projektach ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
uid: test/troubleshoot
ms.openlocfilehash: 345967f08cf99ef5f18d0c9bcd59ab29c74454f1
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511512"
---
# <a name="troubleshoot-and-debug-aspnet-core-projects"></a><span data-ttu-id="e4538-103">Rozwiązywanie problemów i debugowanie projektów ASP.NET podstawowych</span><span class="sxs-lookup"><span data-stu-id="e4538-103">Troubleshoot and debug ASP.NET Core projects</span></span>

<span data-ttu-id="e4538-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e4538-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e4538-105">Poniższe łącza zawierają wskazówki dotyczące rozwiązywania problemów:</span><span class="sxs-lookup"><span data-stu-id="e4538-105">The following links provide troubleshooting guidance:</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="e4538-106">Konferencja NDC (Londyn, 2018): Diagnozowanie problemów w ASP.NET podstawowych aplikacji</span><span class="sxs-lookup"><span data-stu-id="e4538-106">NDC Conference (London, 2018): Diagnosing issues in ASP.NET Core Applications</span></span>](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [<span data-ttu-id="e4538-107">ASP.NET Blog: Rozwiązywanie problemów z wydajnością ASP.NET podstawowych</span><span class="sxs-lookup"><span data-stu-id="e4538-107">ASP.NET Blog: Troubleshooting ASP.NET Core Performance Problems</span></span>](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a><span data-ttu-id="e4538-108">Ostrzeżenia o sdku rdzenia .NET</span><span class="sxs-lookup"><span data-stu-id="e4538-108">.NET Core SDK warnings</span></span>

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a><span data-ttu-id="e4538-109">32-bitowe i 64-bitowe wersje sdk .NET Core SDK są zainstalowane</span><span class="sxs-lookup"><span data-stu-id="e4538-109">Both the 32-bit and 64-bit versions of the .NET Core SDK are installed</span></span>

<span data-ttu-id="e4538-110">W oknie dialogowym **Nowy projekt** dla ASP.NET Core może pojawić się następujące ostrzeżenie:</span><span class="sxs-lookup"><span data-stu-id="e4538-110">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="e4538-111">Są zainstalowane zarówno 32-bitowe, jak i 64-bitowe wersje sdk .NET Core.</span><span class="sxs-lookup"><span data-stu-id="e4538-111">Both 32-bit and 64-bit versions of the .NET Core SDK are installed.</span></span> <span data-ttu-id="e4538-112">Wyświetlane są tylko szablony z wersji 64-bitowych zainstalowanych w\\'C: Program\\Files dotnet\\sdk'.\\</span><span class="sxs-lookup"><span data-stu-id="e4538-112">Only templates from the 64-bit versions installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>

<span data-ttu-id="e4538-113">To ostrzeżenie pojawia się po zainstalowaniu zarówno 32-bitowych (x86) i 64-bitowych (x64) wersji [.NET Core SDK.](https://dotnet.microsoft.com/download/dotnet-core)</span><span class="sxs-lookup"><span data-stu-id="e4538-113">This warning appears when both 32-bit (x86) and 64-bit (x64) versions of the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) are installed.</span></span> <span data-ttu-id="e4538-114">Typowe powody, dla których obie wersje mogą być zainstalowane, obejmują:</span><span class="sxs-lookup"><span data-stu-id="e4538-114">Common reasons both versions may be installed include:</span></span>

* <span data-ttu-id="e4538-115">Instalator .NET Core SDK został pierwotnie pobrany przy użyciu komputera 32-bitowego, ale następnie skopiowano go i zainstalowałeś na komputerze 64-bitowym.</span><span class="sxs-lookup"><span data-stu-id="e4538-115">You originally downloaded the .NET Core SDK installer using a 32-bit machine but then copied it across and installed it on a 64-bit machine.</span></span>
* <span data-ttu-id="e4538-116">32-bitowy pakiet SDK .NET Core został zainstalowany przez inną aplikację.</span><span class="sxs-lookup"><span data-stu-id="e4538-116">The 32-bit .NET Core SDK was installed by another application.</span></span>
* <span data-ttu-id="e4538-117">Pobrano i zainstalowano niewłaściwą wersję.</span><span class="sxs-lookup"><span data-stu-id="e4538-117">The wrong version was downloaded and installed.</span></span>

<span data-ttu-id="e4538-118">Odinstaluj 32-bitowy pakiet SDK .NET Core, aby zapobiec temu ostrzeżeniu.</span><span class="sxs-lookup"><span data-stu-id="e4538-118">Uninstall the 32-bit .NET Core SDK to prevent this warning.</span></span> <span data-ttu-id="e4538-119">Odinstaluj z **Panelu** > **sterowania Programy i funkcje** > **Odinstaluj lub zmień program**.</span><span class="sxs-lookup"><span data-stu-id="e4538-119">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="e4538-120">Jeśli zrozumiesz, dlaczego ostrzeżenie występuje i jego konsekwencje, można zignorować ostrzeżenie.</span><span class="sxs-lookup"><span data-stu-id="e4538-120">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a><span data-ttu-id="e4538-121">Zestaw SDK .NET Core jest instalowany w wielu lokalizacjach</span><span class="sxs-lookup"><span data-stu-id="e4538-121">The .NET Core SDK is installed in multiple locations</span></span>

<span data-ttu-id="e4538-122">W oknie dialogowym **Nowy projekt** dla ASP.NET Core może pojawić się następujące ostrzeżenie:</span><span class="sxs-lookup"><span data-stu-id="e4538-122">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="e4538-123">Zestaw SDK rdzenia .NET jest instalowany w wielu lokalizacjach.</span><span class="sxs-lookup"><span data-stu-id="e4538-123">The .NET Core SDK is installed in multiple locations.</span></span> <span data-ttu-id="e4538-124">Wyświetlane są tylko szablony z pakietów\\SDK zainstalowanych w 'C: Program Files\\dotnet\\sdk'.\\</span><span class="sxs-lookup"><span data-stu-id="e4538-124">Only templates from the SDKs installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>

<span data-ttu-id="e4538-125">Ten komunikat jest wyświetlany, gdy masz co najmniej jedną instalację .NET Core SDK w katalogu poza *C:\\Program Files\\dotnet\\sdk\\*.</span><span class="sxs-lookup"><span data-stu-id="e4538-125">You see this message when you have at least one installation of the .NET Core SDK in a directory outside of *C:\\Program Files\\dotnet\\sdk\\*.</span></span> <span data-ttu-id="e4538-126">Zwykle dzieje się tak, gdy pakiet .NET Core SDK został wdrożony na komputerze przy użyciu funkcji kopiowania/wklejania zamiast instalatora MSI.</span><span class="sxs-lookup"><span data-stu-id="e4538-126">Usually this happens when the .NET Core SDK has been deployed on a machine using copy/paste instead of the MSI installer.</span></span>

<span data-ttu-id="e4538-127">Odinstaluj wszystkie 32-bitowe 32-bitowe core sdks .NET i środowiska wykonawcze, aby zapobiec temu ostrzeżeniu.</span><span class="sxs-lookup"><span data-stu-id="e4538-127">Uninstall all 32-bit .NET Core SDKs and runtimes to prevent this warning.</span></span> <span data-ttu-id="e4538-128">Odinstaluj z **Panelu** > **sterowania Programy i funkcje** > **Odinstaluj lub zmień program**.</span><span class="sxs-lookup"><span data-stu-id="e4538-128">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="e4538-129">Jeśli zrozumiesz, dlaczego ostrzeżenie występuje i jego konsekwencje, można zignorować ostrzeżenie.</span><span class="sxs-lookup"><span data-stu-id="e4538-129">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="no-net-core-sdks-were-detected"></a><span data-ttu-id="e4538-130">Nie wykryto żadnych podstawowych sdk .NET</span><span class="sxs-lookup"><span data-stu-id="e4538-130">No .NET Core SDKs were detected</span></span>

* <span data-ttu-id="e4538-131">W oknie dialogowym Visual Studio **New Project** dla ASP.NET Core może zostać wyświetlone następujące ostrzeżenie:</span><span class="sxs-lookup"><span data-stu-id="e4538-131">In the Visual Studio **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

  > <span data-ttu-id="e4538-132">Nie wykryto żadnych podstawowych sdk .NET, upewnij się, że są one zawarte w zmiennej `PATH`środowiskowej .</span><span class="sxs-lookup"><span data-stu-id="e4538-132">No .NET Core SDKs were detected, ensure they are included in the environment variable `PATH`.</span></span>

* <span data-ttu-id="e4538-133">Podczas wykonywania `dotnet` polecenia ostrzeżenie jest wyświetlane w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="e4538-133">When executing a `dotnet` command, the warning appears as:</span></span>

  > <span data-ttu-id="e4538-134">Nie można było znaleźć żadnych zainstalowanych dotnet SDK.</span><span class="sxs-lookup"><span data-stu-id="e4538-134">It was not possible to find any installed dotnet SDKs.</span></span>

<span data-ttu-id="e4538-135">Te ostrzeżenia są wyświetlane, gdy zmienna `PATH` środowiskowa nie wskazuje żadnych .NET Core SDK na komputerze.</span><span class="sxs-lookup"><span data-stu-id="e4538-135">These warnings appear when the environment variable `PATH` doesn't point to any .NET Core SDKs on the machine.</span></span> <span data-ttu-id="e4538-136">Aby rozwiązać ten problem:</span><span class="sxs-lookup"><span data-stu-id="e4538-136">To resolve this problem:</span></span>

* <span data-ttu-id="e4538-137">Zainstaluj pakiet SDK rdzenia .NET.</span><span class="sxs-lookup"><span data-stu-id="e4538-137">Install the .NET Core SDK.</span></span> <span data-ttu-id="e4538-138">Pobierz najnowszy instalator z [pliku .NET Downloads](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="e4538-138">Obtain the latest installer from [.NET Downloads](https://dotnet.microsoft.com/download).</span></span>
* <span data-ttu-id="e4538-139">Sprawdź, `PATH` czy zmienna środowiskowa wskazuje lokalizację, w`C:\Program Files\dotnet\` której jest zainstalowany pakiet SDK (dla 64-bit/x64 lub `C:\Program Files (x86)\dotnet\` 32-bit/x86).</span><span class="sxs-lookup"><span data-stu-id="e4538-139">Verify that the `PATH` environment variable points to the location where the SDK is installed (`C:\Program Files\dotnet\` for 64-bit/x64 or `C:\Program Files (x86)\dotnet\` for 32-bit/x86).</span></span> <span data-ttu-id="e4538-140">Instalator SDK zwykle ustawia `PATH`plik .</span><span class="sxs-lookup"><span data-stu-id="e4538-140">The SDK installer normally sets the `PATH`.</span></span> <span data-ttu-id="e4538-141">Zawsze instaluj te same bityści sdk i środowiska wykonawcze na tym samym komputerze.</span><span class="sxs-lookup"><span data-stu-id="e4538-141">Always install the same bitness SDKs and runtimes on the same machine.</span></span>

### <a name="missing-sdk-after-installing-the-net-core-hosting-bundle"></a><span data-ttu-id="e4538-142">Brak zestawu SDK po zainstalowaniu pakietu hostingowego .NET Core</span><span class="sxs-lookup"><span data-stu-id="e4538-142">Missing SDK after installing the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="e4538-143">Zainstalowanie [pakietu hostingowego .NET Core](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) modyfikuje `PATH` czas instalacji środowiska uruchomieniowego .NET Core w celu wskazania`C:\Program Files (x86)\dotnet\`32-bitowej (x86) wersji programu .NET Core ( ).</span><span class="sxs-lookup"><span data-stu-id="e4538-143">Installing the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) modifies the `PATH` when it installs the .NET Core runtime to point to the 32-bit (x86) version of .NET Core (`C:\Program Files (x86)\dotnet\`).</span></span> <span data-ttu-id="e4538-144">Może to spowodować brakujących zestawów SDK, gdy zostanie użyte `dotnet` 32-bitowe (x86) polecenie .NET Core[(wykryto nie wykryto zestawów SDK .NET Core).](#no-net-core-sdks-were-detected)</span><span class="sxs-lookup"><span data-stu-id="e4538-144">This can result in missing SDKs when the 32-bit (x86) .NET Core `dotnet` command is used ([No .NET Core SDKs were detected](#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="e4538-145">Aby rozwiązać ten `C:\Program Files\dotnet\` problem, przejdź `C:\Program Files (x86)\dotnet\` do `PATH`pozycji przed na .</span><span class="sxs-lookup"><span data-stu-id="e4538-145">To resolve this problem, move `C:\Program Files\dotnet\` to a position before `C:\Program Files (x86)\dotnet\` on the `PATH`.</span></span>

## <a name="obtain-data-from-an-app"></a><span data-ttu-id="e4538-146">Uzyskiwanie danych z aplikacji</span><span class="sxs-lookup"><span data-stu-id="e4538-146">Obtain data from an app</span></span>

<span data-ttu-id="e4538-147">Jeśli aplikacja jest w stanie odpowiadać na żądania, można uzyskać następujące dane z aplikacji za pomocą oprogramowania pośredniczącego:</span><span class="sxs-lookup"><span data-stu-id="e4538-147">If an app is capable of responding to requests, you can obtain the following data from the app using middleware:</span></span>

* <span data-ttu-id="e4538-148">Metoda &ndash; żądania, schemat, host, baza ścieżek, ścieżka, ciąg zapytania, nagłówki</span><span class="sxs-lookup"><span data-stu-id="e4538-148">Request &ndash; Method, scheme, host, pathbase, path, query string, headers</span></span>
* <span data-ttu-id="e4538-149">Zdalny adres IP połączenia, &ndash; port zdalny, lokalny adres IP, port lokalny, certyfikat klienta</span><span class="sxs-lookup"><span data-stu-id="e4538-149">Connection &ndash; Remote IP address, remote port, local IP address, local port, client certificate</span></span>
* <span data-ttu-id="e4538-150">Nazwa &ndash; tożsamości, nazwa wyświetlana</span><span class="sxs-lookup"><span data-stu-id="e4538-150">Identity &ndash; Name, display name</span></span>
* <span data-ttu-id="e4538-151">Ustawienia konfiguracji</span><span class="sxs-lookup"><span data-stu-id="e4538-151">Configuration settings</span></span>
* <span data-ttu-id="e4538-152">Zmienne środowiskowe</span><span class="sxs-lookup"><span data-stu-id="e4538-152">Environment variables</span></span>

<span data-ttu-id="e4538-153">Umieść następujący kod [oprogramowania pośredniczącego](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) na początku potoku `Startup.Configure` przetwarzania żądania metody.</span><span class="sxs-lookup"><span data-stu-id="e4538-153">Place the following [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) code at the beginning of the `Startup.Configure` method's request processing pipeline.</span></span> <span data-ttu-id="e4538-154">Środowisko jest sprawdzane przed uruchomieniem oprogramowania pośredniczącego, aby upewnić się, że kod jest wykonywany tylko w środowisku deweloperskim.</span><span class="sxs-lookup"><span data-stu-id="e4538-154">The environment is checked before the middleware is run to ensure that the code is only executed in the Development environment.</span></span>

<span data-ttu-id="e4538-155">Aby uzyskać środowisko, należy użyć jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="e4538-155">To obtain the environment, use either of the following approaches:</span></span>

* <span data-ttu-id="e4538-156">Wstrzyknąć do `IHostingEnvironment` `Startup.Configure` metody i sprawdzić środowisko z zmienną lokalną.</span><span class="sxs-lookup"><span data-stu-id="e4538-156">Inject the `IHostingEnvironment` into the `Startup.Configure` method and check the environment with the local variable.</span></span> <span data-ttu-id="e4538-157">Poniższy przykładowy kod demonstruje to podejście.</span><span class="sxs-lookup"><span data-stu-id="e4538-157">The following sample code demonstrates this approach.</span></span>

* <span data-ttu-id="e4538-158">Przypisz środowisko do właściwości `Startup` w klasie.</span><span class="sxs-lookup"><span data-stu-id="e4538-158">Assign the environment to a property in the `Startup` class.</span></span> <span data-ttu-id="e4538-159">Sprawdź środowisko przy użyciu właściwości `if (Environment.IsDevelopment())`(na przykład).</span><span class="sxs-lookup"><span data-stu-id="e4538-159">Check the environment using the property (for example, `if (Environment.IsDevelopment())`).</span></span>

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

## <a name="debug-aspnet-core-apps"></a><span data-ttu-id="e4538-160">Debugowanie aplikacji ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e4538-160">Debug ASP.NET Core apps</span></span>

<span data-ttu-id="e4538-161">Poniższe łącza zawierają informacje na temat debugowania ASP.NET aplikacji Core.</span><span class="sxs-lookup"><span data-stu-id="e4538-161">The following links provide information on debugging ASP.NET Core apps.</span></span>

* [<span data-ttu-id="e4538-162">Debugowanie rdzenia ASP w systemie Linux</span><span class="sxs-lookup"><span data-stu-id="e4538-162">Debugging ASP Core on Linux</span></span>](https://devblogs.microsoft.com/premier-developer/debugging-asp-core-on-linux-with-visual-studio-2017/)
* [<span data-ttu-id="e4538-163">Debugowanie .NET Core na unixie przez SSH</span><span class="sxs-lookup"><span data-stu-id="e4538-163">Debugging .NET Core on Unix over SSH</span></span>](https://devblogs.microsoft.com/devops/debugging-net-core-on-unix-over-ssh/)
* [<span data-ttu-id="e4538-164">Szybki start: debugowanie ASP.NET za pomocą debugera programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e4538-164">Quickstart: Debug ASP.NET with the Visual Studio debugger</span></span>](/visualstudio/debugger/quickstart-debug-aspnet)
* <span data-ttu-id="e4538-165">Zobacz [ten problem GitHub](https://github.com/dotnet/AspNetCore.Docs/issues/2960) więcej informacji debugowania.</span><span class="sxs-lookup"><span data-stu-id="e4538-165">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/2960) for more debugging information.</span></span>
