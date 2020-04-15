---
title: Platforma ASP.NET Core — podstawy
author: rick-anderson
description: Poznaj podstawowe pojęcia dotyczące tworzenia aplikacji ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
uid: fundamentals/index
ms.openlocfilehash: c675644d8480ef7a5290045067e6cec2ea6f4764
ms.sourcegitcommit: f29a12486313e38e0163a643d8a97c8cecc7e871
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81384062"
---
# <a name="aspnet-core-fundamentals"></a><span data-ttu-id="14461-103">Platforma ASP.NET Core — podstawy</span><span class="sxs-lookup"><span data-stu-id="14461-103">ASP.NET Core fundamentals</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="14461-104">Ten artykuł zawiera omówienie kluczowych tematów do zrozumienia, jak tworzyć ASP.NET aplikacje Core.</span><span class="sxs-lookup"><span data-stu-id="14461-104">This article provides an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="14461-105">Klasa Startup</span><span class="sxs-lookup"><span data-stu-id="14461-105">The Startup class</span></span>

<span data-ttu-id="14461-106">Klasa `Startup` jest, gdzie:</span><span class="sxs-lookup"><span data-stu-id="14461-106">The `Startup` class is where:</span></span>

* <span data-ttu-id="14461-107">Usługi wymagane przez aplikację są skonfigurowane.</span><span class="sxs-lookup"><span data-stu-id="14461-107">Services required by the app are configured.</span></span>
* <span data-ttu-id="14461-108">Potok obsługi żądań aplikacji jest zdefiniowany jako seria składników oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="14461-108">The app's request handling pipeline is defined, as a series of middleware components.</span></span>

<span data-ttu-id="14461-109">Oto przykładowa `Startup` klasa:</span><span class="sxs-lookup"><span data-stu-id="14461-109">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Startup.cs?highlight=3,12)]

<span data-ttu-id="14461-110">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="14461-110">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="14461-111">Wstrzykiwanie zależności (usługi)</span><span class="sxs-lookup"><span data-stu-id="14461-111">Dependency injection (services)</span></span>

<span data-ttu-id="14461-112">ASP.NET Core zawiera wbudowaną strukturę iniekcji zależności (DI), która udostępnia skonfigurowane usługi w całej aplikacji.</span><span class="sxs-lookup"><span data-stu-id="14461-112">ASP.NET Core includes a built-in dependency injection (DI) framework that makes configured services available throughout an app.</span></span> <span data-ttu-id="14461-113">Na przykład składnik rejestrowania jest usługą.</span><span class="sxs-lookup"><span data-stu-id="14461-113">For example, a logging component is a service.</span></span>

<span data-ttu-id="14461-114">Kod do konfigurowania (lub *rejestracji)* `Startup.ConfigureServices` usług jest dodawany do metody.</span><span class="sxs-lookup"><span data-stu-id="14461-114">Code to configure (or *register*) services is added to the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="14461-115">Przykład:</span><span class="sxs-lookup"><span data-stu-id="14461-115">For example:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/ConfigureServices.cs)]

<span data-ttu-id="14461-116">Usługi są zazwyczaj rozpoznawane z DI przy użyciu iniekcji konstruktora.</span><span class="sxs-lookup"><span data-stu-id="14461-116">Services are typically resolved from DI using constructor injection.</span></span> <span data-ttu-id="14461-117">Wtrysku konstruktora klasy deklaruje parametr konstruktora wymaganego typu lub interfejsu.</span><span class="sxs-lookup"><span data-stu-id="14461-117">With constructor injection, a class declares a constructor parameter of either the required type or an interface.</span></span> <span data-ttu-id="14461-118">Struktura DI zapewnia wystąpienie tej usługi w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="14461-118">The DI framework provides an instance of this service at runtime.</span></span>

<span data-ttu-id="14461-119">W poniższym przykładzie użyto `RazorPagesMovieContext` iniekcji konstruktora w celu rozwiązania a z DI:</span><span class="sxs-lookup"><span data-stu-id="14461-119">The following example uses constructor injection to resolve a `RazorPagesMovieContext` from DI:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="14461-120">Jeśli wbudowany kontener inwersji kontroli (IoC) nie spełnia wszystkich potrzeb aplikacji, zamiast tego można użyć kontenera IoC innej firmy.</span><span class="sxs-lookup"><span data-stu-id="14461-120">If the built-in Inversion of Control (IoC) container doesn't meet all of an app's needs, a third-party IoC container can be used instead.</span></span>

<span data-ttu-id="14461-121">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="14461-121">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="14461-122">Oprogramowanie pośredniczące</span><span class="sxs-lookup"><span data-stu-id="14461-122">Middleware</span></span>

<span data-ttu-id="14461-123">Potok obsługi żądań składa się jako seria składników oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="14461-123">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="14461-124">Każdy składnik wykonuje operacje `HttpContext` na i albo wywołuje następne oprogramowanie pośredniczące w potoku lub kończy żądanie.</span><span class="sxs-lookup"><span data-stu-id="14461-124">Each component performs operations on an `HttpContext` and either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="14461-125">Zgodnie z konwencją składnik oprogramowania pośredniczącego `Use...` jest dodawany `Startup.Configure` do potoku przez wywołanie metody rozszerzenia w metodzie.</span><span class="sxs-lookup"><span data-stu-id="14461-125">By convention, a middleware component is added to the pipeline by invoking a `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="14461-126">Na przykład, aby włączyć renderowanie plików `UseStaticFiles`statycznych, wywołaj .</span><span class="sxs-lookup"><span data-stu-id="14461-126">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="14461-127">Poniższy przykład konfiguruje potok obsługi żądań:</span><span class="sxs-lookup"><span data-stu-id="14461-127">The following example configures a request handling pipeline:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Configure.cs)]

<span data-ttu-id="14461-128">ASP.NET Core zawiera bogaty zestaw wbudowanego oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="14461-128">ASP.NET Core includes a rich set of built-in middleware.</span></span> <span data-ttu-id="14461-129">Można również zapisywać niestandardowe składniki oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="14461-129">Custom middleware components can also be written.</span></span>

<span data-ttu-id="14461-130">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="14461-130">For more information, see <xref:fundamentals/middleware/index>.</span></span>

## <a name="host"></a><span data-ttu-id="14461-131">Host</span><span class="sxs-lookup"><span data-stu-id="14461-131">Host</span></span>

<span data-ttu-id="14461-132">Podczas uruchamiania aplikacja ASP.NET Core tworzy *hosta.*</span><span class="sxs-lookup"><span data-stu-id="14461-132">On startup, an ASP.NET Core app builds a *host*.</span></span> <span data-ttu-id="14461-133">Host hermetyzuje wszystkie zasoby aplikacji, takie jak:</span><span class="sxs-lookup"><span data-stu-id="14461-133">The host encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="14461-134">Implementacja serwera HTTP</span><span class="sxs-lookup"><span data-stu-id="14461-134">An HTTP server implementation</span></span>
* <span data-ttu-id="14461-135">Składniki oprogramowania pośredniczącego</span><span class="sxs-lookup"><span data-stu-id="14461-135">Middleware components</span></span>
* <span data-ttu-id="14461-136">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="14461-136">Logging</span></span>
* <span data-ttu-id="14461-137">Usługi iniekcji zależności (DI)</span><span class="sxs-lookup"><span data-stu-id="14461-137">Dependency injection (DI) services</span></span>
* <span data-ttu-id="14461-138">Konfigurowanie</span><span class="sxs-lookup"><span data-stu-id="14461-138">Configuration</span></span>

<span data-ttu-id="14461-139">Istnieją dwa różne hosty:</span><span class="sxs-lookup"><span data-stu-id="14461-139">There are two different hosts:</span></span> 

* <span data-ttu-id="14461-140">Host ogólny platformy .NET</span><span class="sxs-lookup"><span data-stu-id="14461-140">.NET Generic Host</span></span>
* <span data-ttu-id="14461-141">ASP.NET Core Web Host</span><span class="sxs-lookup"><span data-stu-id="14461-141">ASP.NET Core Web Host</span></span>

<span data-ttu-id="14461-142">Zaleca się host ogólny platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="14461-142">The .NET Generic Host is recommended.</span></span> <span data-ttu-id="14461-143">ASP.NET Core Web Host jest dostępny tylko dla zgodności z przeszywnie.</span><span class="sxs-lookup"><span data-stu-id="14461-143">The ASP.NET Core Web Host is available only for backwards compatibility.</span></span>

<span data-ttu-id="14461-144">Poniższy przykład tworzy hosta ogólnego platformy .NET:</span><span class="sxs-lookup"><span data-stu-id="14461-144">The following example creates a .NET Generic Host:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Program.cs)]

<span data-ttu-id="14461-145">Metody `CreateDefaultBuilder` `ConfigureWebHostDefaults` i metody konfigurują hosta z zestawem opcji domyślnych, takich jak:</span><span class="sxs-lookup"><span data-stu-id="14461-145">The `CreateDefaultBuilder` and `ConfigureWebHostDefaults` methods configure a host with a set of default options, such as:</span></span>

* <span data-ttu-id="14461-146">Użyj [Kestrel](#servers) jako serwera sieci web i włącz integrację usług IIS.</span><span class="sxs-lookup"><span data-stu-id="14461-146">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="14461-147">Załaduj konfigurację z *pliku appsettings.json*, *appsettings.{ Nazwa środowiska}.json*, zmienne środowiskowe, argumenty wiersza polecenia i inne źródła konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="14461-147">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="14461-148">Wyślij dane wyjściowe rejestrowania do dostawców konsoli i debugowania.</span><span class="sxs-lookup"><span data-stu-id="14461-148">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="14461-149">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="14461-149">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="non-web-scenarios"></a><span data-ttu-id="14461-150">Scenariusze inne niż internetowe</span><span class="sxs-lookup"><span data-stu-id="14461-150">Non-web scenarios</span></span>

<span data-ttu-id="14461-151">Host ogólny umożliwia innym typom aplikacji używanie rozszerzeń struktury przekrojowych, takich jak rejestrowanie, iniekcja zależności (DI), konfiguracja i zarządzanie okresem istnienia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="14461-151">The Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, dependency injection (DI), configuration, and app lifetime management.</span></span> <span data-ttu-id="14461-152">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/generic-host> i <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="14461-152">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="14461-153">Serwery</span><span class="sxs-lookup"><span data-stu-id="14461-153">Servers</span></span>

<span data-ttu-id="14461-154">Aplikacja ASP.NET Core używa implementacji serwera HTTP do nasłuchiwać żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="14461-154">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="14461-155">Serwer powierzchnie żądań do aplikacji jako zestaw funkcji `HttpContext` [żądania](xref:fundamentals/request-features) składa się w .</span><span class="sxs-lookup"><span data-stu-id="14461-155">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

# <a name="windows"></a>[<span data-ttu-id="14461-156">Windows</span><span class="sxs-lookup"><span data-stu-id="14461-156">Windows</span></span>](#tab/windows)

<span data-ttu-id="14461-157">ASP.NET Core udostępnia następujące implementacje serwera:</span><span class="sxs-lookup"><span data-stu-id="14461-157">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="14461-158">*Pustułka* to wieloplatformowy serwer www.</span><span class="sxs-lookup"><span data-stu-id="14461-158">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="14461-159">Pustułka jest często uruchamiana w odwrotnej konfiguracji serwera proxy przy użyciu [usług IIS](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="14461-159">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="14461-160">W ASP.NET Core 2.0 lub nowszym Kestrel może być uruchamiany jako publiczny serwer brzegowy udostępniane bezpośrednio do Internetu.</span><span class="sxs-lookup"><span data-stu-id="14461-160">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="14461-161">*Serwer HTTP usług IIS* to serwer dla systemu Windows, który korzysta z usług IIS.</span><span class="sxs-lookup"><span data-stu-id="14461-161">*IIS HTTP Server* is a server for Windows that uses IIS.</span></span> <span data-ttu-id="14461-162">Z tego serwera ASP.NET Core aplikacji i usług IIS uruchomić w tym samym procesie.</span><span class="sxs-lookup"><span data-stu-id="14461-162">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="14461-163">*HTTP.sys* to serwer dla systemu Windows, który nie jest używany z usługami IIS.</span><span class="sxs-lookup"><span data-stu-id="14461-163">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="14461-164">macOS</span><span class="sxs-lookup"><span data-stu-id="14461-164">macOS</span></span>](#tab/macos)

<span data-ttu-id="14461-165">ASP.NET Core zapewnia implementację serwera międzyplatformowego *Kestrel.*</span><span class="sxs-lookup"><span data-stu-id="14461-165">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="14461-166">W ASP.NET Core 2.0 lub nowszym Kestrel może działać jako publiczny serwer brzegowy udostępniane bezpośrednio do Internetu.</span><span class="sxs-lookup"><span data-stu-id="14461-166">In ASP.NET Core 2.0 or later, Kestrel can run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="14461-167">Pustułka jest często uruchamiana w odwrotnej konfiguracji proxy z [Nginx](https://nginx.org) lub [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="14461-167">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="14461-168">Linux</span><span class="sxs-lookup"><span data-stu-id="14461-168">Linux</span></span>](#tab/linux)

<span data-ttu-id="14461-169">ASP.NET Core zapewnia implementację serwera międzyplatformowego *Kestrel.*</span><span class="sxs-lookup"><span data-stu-id="14461-169">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="14461-170">W ASP.NET Core 2.0 lub nowszym Kestrel może działać jako publiczny serwer brzegowy udostępniane bezpośrednio do Internetu.</span><span class="sxs-lookup"><span data-stu-id="14461-170">In ASP.NET Core 2.0 or later, Kestrel can run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="14461-171">Pustułka jest często uruchamiana w odwrotnej konfiguracji proxy z [Nginx](https://nginx.org) lub [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="14461-171">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

<span data-ttu-id="14461-172">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/servers/index>.</span><span class="sxs-lookup"><span data-stu-id="14461-172">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="14461-173">Konfigurowanie</span><span class="sxs-lookup"><span data-stu-id="14461-173">Configuration</span></span>

<span data-ttu-id="14461-174">ASP.NET Core zapewnia strukturę konfiguracji, która pobiera ustawienia jako pary nazwa-wartość z uporządkowanego zestawu dostawców konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="14461-174">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="14461-175">Wbudowane dostawcy konfiguracji są dostępni dla różnych źródeł, takich jak pliki *json,* pliki *xml,* zmienne środowiskowe i argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="14461-175">Built-in configuration providers are available for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="14461-176">Napisz dostawców konfiguracji niestandardowych do obsługi innych źródeł.</span><span class="sxs-lookup"><span data-stu-id="14461-176">Write custom configuration providers to support other sources.</span></span>

<span data-ttu-id="14461-177">[Domyślnie](xref:fundamentals/configuration/index#default)aplikacje ASP.NET Core są skonfigurowane do odczytu z *pliku appsettings.json*, zmiennych środowiskowych, wiersza polecenia i innych.</span><span class="sxs-lookup"><span data-stu-id="14461-177">By [default](xref:fundamentals/configuration/index#default), ASP.NET Core apps are configured to read from *appsettings.json*, environment variables, the command line, and more.</span></span> <span data-ttu-id="14461-178">Po załadowaniu konfiguracji aplikacji wartości ze zmiennych środowiskowych zastępują wartości z *pliku appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="14461-178">When the app's configuration is loaded, values from environment variables override values from *appsettings.json*.</span></span>

<span data-ttu-id="14461-179">Preferowanym sposobem odczytywania powiązanych wartości konfiguracyjnych jest użycie [wzorca opcji](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="14461-179">The preferred way to read related configuration values is using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="14461-180">Aby uzyskać więcej informacji, zobacz [Powiązanie danych konfiguracji hierarchicznej przy użyciu wzorca opcji](xref:fundamentals/configuration/index#optpat).</span><span class="sxs-lookup"><span data-stu-id="14461-180">For more information, see [Bind hierarchical configuration data using the options pattern](xref:fundamentals/configuration/index#optpat).</span></span>

<span data-ttu-id="14461-181">Do zarządzania poufnymi danymi konfiguracyjnymi, takimi jak hasła, ASP.NET Core udostępnia [Tajnego Menedżera](xref:security/app-secrets#secret-manager).</span><span class="sxs-lookup"><span data-stu-id="14461-181">For managing confidential configuration data such as passwords, ASP.NET Core provides the [Secret Manager](xref:security/app-secrets#secret-manager).</span></span> <span data-ttu-id="14461-182">W przypadku wpisów tajnych produkcji zalecamy [usługę Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="14461-182">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="14461-183">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="14461-183">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="environments"></a><span data-ttu-id="14461-184">Środowiska</span><span class="sxs-lookup"><span data-stu-id="14461-184">Environments</span></span>

<span data-ttu-id="14461-185">Środowiska wykonywania, takie `Development` `Staging`jak `Production`, i , są pojęciem pierwszej klasy w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="14461-185">Execution environments, such as `Development`, `Staging`, and `Production`, are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="14461-186">Określ środowisko, w które działa `ASPNETCORE_ENVIRONMENT` aplikacja, ustawiając zmienną środowiskową.</span><span class="sxs-lookup"><span data-stu-id="14461-186">Specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="14461-187">ASP.NET Core odczytuje tę zmienną środowiskową `IWebHostEnvironment` podczas uruchamiania aplikacji i przechowuje wartość w implementacji.</span><span class="sxs-lookup"><span data-stu-id="14461-187">ASP.NET Core reads that environment variable at app startup and stores the value in an `IWebHostEnvironment` implementation.</span></span> <span data-ttu-id="14461-188">Ta implementacja jest dostępna w dowolnym miejscu w aplikacji za pośrednictwem iniekcji zależności (DI).</span><span class="sxs-lookup"><span data-stu-id="14461-188">This implementation is available anywhere in an app via dependency injection (DI).</span></span>

<span data-ttu-id="14461-189">Poniższy przykład konfiguruje aplikację, aby zapewnić `Development` szczegółowe informacje o błędzie podczas uruchamiania w środowisku:</span><span class="sxs-lookup"><span data-stu-id="14461-189">The following example configures the app to provide detailed error information when running in the `Development` environment:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/StartupConfigure.cs?highlight=3-6)]

<span data-ttu-id="14461-190">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="14461-190">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="14461-191">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="14461-191">Logging</span></span>

<span data-ttu-id="14461-192">ASP.NET Core obsługuje interfejs API rejestrowania, który współpracuje z różnymi wbudowanymi i zewnętrznymi dostawcami rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="14461-192">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="14461-193">Do dostępnych dostawców należą:</span><span class="sxs-lookup"><span data-stu-id="14461-193">Available providers include:</span></span>

* <span data-ttu-id="14461-194">Konsola</span><span class="sxs-lookup"><span data-stu-id="14461-194">Console</span></span>
* <span data-ttu-id="14461-195">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="14461-195">Debug</span></span>
* <span data-ttu-id="14461-196">Śledzenie zdarzeń w systemie Windows</span><span class="sxs-lookup"><span data-stu-id="14461-196">Event Tracing on Windows</span></span>
* <span data-ttu-id="14461-197">Dziennik zdarzeń systemu Windows</span><span class="sxs-lookup"><span data-stu-id="14461-197">Windows Event Log</span></span>
* <span data-ttu-id="14461-198">TraceSource</span><span class="sxs-lookup"><span data-stu-id="14461-198">TraceSource</span></span>
* <span data-ttu-id="14461-199">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="14461-199">Azure App Service</span></span>
* <span data-ttu-id="14461-200">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="14461-200">Azure Application Insights</span></span>

<span data-ttu-id="14461-201">Aby utworzyć dzienniki, <xref:Microsoft.Extensions.Logging.ILogger%601> rozwiąż problem usługi z iniekcji <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>zależności (DI) i wywołania metod rejestrowania, takich jak .</span><span class="sxs-lookup"><span data-stu-id="14461-201">To create logs, resolve an <xref:Microsoft.Extensions.Logging.ILogger%601> service from dependency injection (DI) and call logging methods such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>.</span></span> <span data-ttu-id="14461-202">Przykład:</span><span class="sxs-lookup"><span data-stu-id="14461-202">For example:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/TodoController.cs?highlight=5,13,19)]

<span data-ttu-id="14461-203">Metody rejestrowania, `LogInformation` takie jak obsługa dowolnej liczby pól.</span><span class="sxs-lookup"><span data-stu-id="14461-203">Logging methods such as `LogInformation` support any number of fields.</span></span> <span data-ttu-id="14461-204">Te pola są często używane `string`do konstruowania wiadomości, ale niektórzy dostawcy rejestrowania wysyłają je do magazynu danych jako oddzielne pola.</span><span class="sxs-lookup"><span data-stu-id="14461-204">These fields are commonly used to construct a message `string`, but some logging providers send these to a data store as separate fields.</span></span> <span data-ttu-id="14461-205">Ta funkcja umożliwia dostawcom rejestrowania zaimplementowanie [rejestrowania semantycznego, znanego również jako rejestrowanie strukturalne.](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)</span><span class="sxs-lookup"><span data-stu-id="14461-205">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="14461-206">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="14461-206">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="14461-207">Routing</span><span class="sxs-lookup"><span data-stu-id="14461-207">Routing</span></span>

<span data-ttu-id="14461-208">*Trasa* jest wzorcem adresu URL, który jest mapowany do programu obsługi.</span><span class="sxs-lookup"><span data-stu-id="14461-208">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="14461-209">Program obsługi jest zazwyczaj razor strony, metody akcji w kontrolerze MVC lub oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="14461-209">The handler is typically a Razor page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="14461-210">ASP.NET Routing podstawowy daje ci kontrolę nad adresami URL używanymi przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="14461-210">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="14461-211">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="14461-211">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="14461-212">Obsługa błędów</span><span class="sxs-lookup"><span data-stu-id="14461-212">Error handling</span></span>

<span data-ttu-id="14461-213">ASP.NET Core ma wbudowane funkcje obsługi błędów, takie jak:</span><span class="sxs-lookup"><span data-stu-id="14461-213">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="14461-214">Strona wyjątku dla deweloperów</span><span class="sxs-lookup"><span data-stu-id="14461-214">A developer exception page</span></span>
* <span data-ttu-id="14461-215">Niestandardowe strony błędów</span><span class="sxs-lookup"><span data-stu-id="14461-215">Custom error pages</span></span>
* <span data-ttu-id="14461-216">Statyczne strony kodowe stanu</span><span class="sxs-lookup"><span data-stu-id="14461-216">Static status code pages</span></span>
* <span data-ttu-id="14461-217">Obsługa wyjątków uruchamiania</span><span class="sxs-lookup"><span data-stu-id="14461-217">Startup exception handling</span></span>

<span data-ttu-id="14461-218">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="14461-218">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="14461-219">Zgłaszanie żądań HTTP</span><span class="sxs-lookup"><span data-stu-id="14461-219">Make HTTP requests</span></span>

<span data-ttu-id="14461-220">Implementacja `IHttpClientFactory` jest dostępna do `HttpClient` tworzenia wystąpień.</span><span class="sxs-lookup"><span data-stu-id="14461-220">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="14461-221">Fabryka:</span><span class="sxs-lookup"><span data-stu-id="14461-221">The factory:</span></span>

* <span data-ttu-id="14461-222">Zapewnia centralną lokalizację nazewnictwa `HttpClient` i konfigurowania wystąpień logicznych.</span><span class="sxs-lookup"><span data-stu-id="14461-222">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="14461-223">Na przykład zarejestruj i skonfiguruj klienta *github,* aby uzyskać dostęp do usługi GitHub.</span><span class="sxs-lookup"><span data-stu-id="14461-223">For example, register and configure a *github* client for accessing GitHub.</span></span> <span data-ttu-id="14461-224">Zarejestruj i skonfiguruj klienta domyślnego do innych celów.</span><span class="sxs-lookup"><span data-stu-id="14461-224">Register and configure a default client for other purposes.</span></span>
* <span data-ttu-id="14461-225">Obsługuje rejestrację i tworzenie łańcucha wielu programów obsługi delegujących do tworzenia potoku oprogramowania pośredniczącego żądania wychodzącego.</span><span class="sxs-lookup"><span data-stu-id="14461-225">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="14461-226">Ten wzorzec jest podobny do potoku przychodzącego oprogramowania pośredniczącego ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="14461-226">This pattern is similar to ASP.NET Core's inbound middleware pipeline.</span></span> <span data-ttu-id="14461-227">Wzorzec zapewnia mechanizm zarządzania przekrojowe problemy dla żądań HTTP, w tym buforowania, obsługi błędów, serializacji i rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="14461-227">The pattern provides a mechanism to manage cross-cutting concerns for HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="14461-228">Integruje się z *Polly*, popularną biblioteką innych firm do obsługi błędów przejściowych.</span><span class="sxs-lookup"><span data-stu-id="14461-228">Integrates with *Polly*, a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="14461-229">Zarządza buforowanie i okres `HttpClientHandler` istnienia wystąpienia podstawowe, aby uniknąć `HttpClient` typowych problemów z systemem DNS, które występują podczas ręcznego zarządzania okresami istnienia.</span><span class="sxs-lookup"><span data-stu-id="14461-229">Manages the pooling and lifetime of underlying `HttpClientHandler` instances to avoid common DNS problems that occur when managing `HttpClient` lifetimes manually.</span></span>
* <span data-ttu-id="14461-230">Dodaje konfigurowalne środowisko <xref:Microsoft.Extensions.Logging.ILogger> rejestrowania za pośrednictwem dla wszystkich żądań wysyłanych za pośrednictwem klientów utworzonych przez fabrykę.</span><span class="sxs-lookup"><span data-stu-id="14461-230">Adds a configurable logging experience via <xref:Microsoft.Extensions.Logging.ILogger> for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="14461-231">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="14461-231">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="14461-232">Katalog główny zawartości</span><span class="sxs-lookup"><span data-stu-id="14461-232">Content root</span></span>

<span data-ttu-id="14461-233">Katalog główny zawartości jest podstawową ścieżką dla:</span><span class="sxs-lookup"><span data-stu-id="14461-233">The content root is the base path for:</span></span>

* <span data-ttu-id="14461-234">Plik wykonywalny hosting aplikacji (*.exe*).</span><span class="sxs-lookup"><span data-stu-id="14461-234">The executable hosting the app (*.exe*).</span></span>
* <span data-ttu-id="14461-235">Skompilowane zestawy, które tworzą aplikację (*.dll*).</span><span class="sxs-lookup"><span data-stu-id="14461-235">Compiled assemblies that make up the app (*.dll*).</span></span>
* <span data-ttu-id="14461-236">Pliki zawartości używane przez aplikację, takie jak:</span><span class="sxs-lookup"><span data-stu-id="14461-236">Content files used by the app, such as:</span></span>
  * <span data-ttu-id="14461-237">Pliki razor (*.cshtml*, *.brzytwa*)</span><span class="sxs-lookup"><span data-stu-id="14461-237">Razor files (*.cshtml*, *.razor*)</span></span>
  * <span data-ttu-id="14461-238">Pliki konfiguracyjne (*.json*, *.xml*)</span><span class="sxs-lookup"><span data-stu-id="14461-238">Configuration files (*.json*, *.xml*)</span></span>
  * <span data-ttu-id="14461-239">Pliki danych (*.db*)</span><span class="sxs-lookup"><span data-stu-id="14461-239">Data files (*.db*)</span></span>
* <span data-ttu-id="14461-240">[Katalog główny sieci Web](#web-root), zazwyczaj folder *wwwroot.*</span><span class="sxs-lookup"><span data-stu-id="14461-240">The [Web root](#web-root), typically the *wwwroot* folder.</span></span>

<span data-ttu-id="14461-241">Podczas tworzenia katalogu głównego zawartości domyślnie katalogu głównego projektu.</span><span class="sxs-lookup"><span data-stu-id="14461-241">During development, the content root defaults to the project's root directory.</span></span> <span data-ttu-id="14461-242">Ten katalog jest również podstawową ścieżką zarówno dla plików zawartości aplikacji, jak i [katalogu głównego sieci Web.](#web-root)</span><span class="sxs-lookup"><span data-stu-id="14461-242">This directory is also the base path for both the app's content files and the [Web root](#web-root).</span></span> <span data-ttu-id="14461-243">Określ inny katalog główny zawartości, ustawiając jego ścieżkę podczas [tworzenia hosta](#host).</span><span class="sxs-lookup"><span data-stu-id="14461-243">Specify a different content root by setting its path when [building the host](#host).</span></span> <span data-ttu-id="14461-244">Aby uzyskać więcej informacji, zobacz [Katalog główny zawartości](xref:fundamentals/host/generic-host#contentroot).</span><span class="sxs-lookup"><span data-stu-id="14461-244">For more information, see [Content root](xref:fundamentals/host/generic-host#contentroot).</span></span>

## <a name="web-root"></a><span data-ttu-id="14461-245">Katalog główny sieci Web</span><span class="sxs-lookup"><span data-stu-id="14461-245">Web root</span></span>

<span data-ttu-id="14461-246">Katalog główny sieci Web jest ścieżką bazową dla publicznych, statycznych plików zasobów, takich jak:</span><span class="sxs-lookup"><span data-stu-id="14461-246">The web root is the base path for public, static resource files, such as:</span></span>

* <span data-ttu-id="14461-247">Arkusze stylów (*.css*)</span><span class="sxs-lookup"><span data-stu-id="14461-247">Stylesheets (*.css*)</span></span>
* <span data-ttu-id="14461-248">JavaScript (*.js*)</span><span class="sxs-lookup"><span data-stu-id="14461-248">JavaScript (*.js*)</span></span>
* <span data-ttu-id="14461-249">Zdjęcia (*.png*, *.jpg*)</span><span class="sxs-lookup"><span data-stu-id="14461-249">Images (*.png*, *.jpg*)</span></span>

<span data-ttu-id="14461-250">Domyślnie pliki statyczne są obsługiwane tylko z katalogu głównego sieci Web i jego podkatastów.</span><span class="sxs-lookup"><span data-stu-id="14461-250">By default, static files are served only from the web root directory and its sub-directories.</span></span> <span data-ttu-id="14461-251">Ścieżka główna sieci Web domyślnie *{zawartość root}/wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="14461-251">The web root path defaults to *{content root}/wwwroot*.</span></span> <span data-ttu-id="14461-252">Określ inny katalog główny sieci Web, ustawiając jego ścieżkę podczas [tworzenia hosta](#host).</span><span class="sxs-lookup"><span data-stu-id="14461-252">Specify a different web root by setting its path when [building the host](#host).</span></span> <span data-ttu-id="14461-253">Aby uzyskać więcej informacji, zobacz [Katalog główny sieci Web](xref:fundamentals/host/generic-host#webroot).</span><span class="sxs-lookup"><span data-stu-id="14461-253">For more information, see [Web root](xref:fundamentals/host/generic-host#webroot).</span></span>

<span data-ttu-id="14461-254">Zapobiegaj publikowaniu plików w [ \<](/visualstudio/msbuild/common-msbuild-project-items#content) *witrynie wwwroot* za pomocą elementu projektu> zawartości w pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="14461-254">Prevent publishing files in *wwwroot* with the [\<Content> project item](/visualstudio/msbuild/common-msbuild-project-items#content) in the project file.</span></span> <span data-ttu-id="14461-255">Poniższy przykład zapobiega publikowaniu zawartości w *wwwroot/local* i jego podkatastronach:</span><span class="sxs-lookup"><span data-stu-id="14461-255">The following example prevents publishing content in *wwwroot/local* and its sub-directories:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="14461-256">W plikach Razor *.cshtml,* `~/`tylda-ukośnik ( ) wskazuje na katalog główny sieci Web.</span><span class="sxs-lookup"><span data-stu-id="14461-256">In Razor *.cshtml* files, tilde-slash (`~/`) points to the web root.</span></span> <span data-ttu-id="14461-257">Ścieżka rozpoczynająca `~/` się od ścieżki jest określana jako *ścieżka wirtualna*.</span><span class="sxs-lookup"><span data-stu-id="14461-257">A path beginning with `~/` is referred to as a *virtual path*.</span></span>

<span data-ttu-id="14461-258">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="14461-258">For more information, see <xref:fundamentals/static-files>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="14461-259">Ten artykuł zawiera omówienie kluczowych tematów do zrozumienia, jak tworzyć ASP.NET aplikacje Core.</span><span class="sxs-lookup"><span data-stu-id="14461-259">This article is an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="14461-260">Klasa Startup</span><span class="sxs-lookup"><span data-stu-id="14461-260">The Startup class</span></span>

<span data-ttu-id="14461-261">Klasa `Startup` jest, gdzie:</span><span class="sxs-lookup"><span data-stu-id="14461-261">The `Startup` class is where:</span></span>

* <span data-ttu-id="14461-262">Usługi wymagane przez aplikację są skonfigurowane.</span><span class="sxs-lookup"><span data-stu-id="14461-262">Services required by the app are configured.</span></span>
* <span data-ttu-id="14461-263">Potok obsługi żądań jest zdefiniowany.</span><span class="sxs-lookup"><span data-stu-id="14461-263">The request handling pipeline is defined.</span></span>

<span data-ttu-id="14461-264">*Usługi* są składnikami, które są używane przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="14461-264">*Services* are components that are used by the app.</span></span> <span data-ttu-id="14461-265">Na przykład składnik rejestrowania jest usługą.</span><span class="sxs-lookup"><span data-stu-id="14461-265">For example, a logging component is a service.</span></span> <span data-ttu-id="14461-266">Kod do konfigurowania (lub *rejestracji)* `Startup.ConfigureServices` usług jest dodawany do metody.</span><span class="sxs-lookup"><span data-stu-id="14461-266">Code to configure (or *register*) services is added to the `Startup.ConfigureServices` method.</span></span>

<span data-ttu-id="14461-267">Potok obsługi żądań składa się jako seria składników *oprogramowania pośredniczącego.*</span><span class="sxs-lookup"><span data-stu-id="14461-267">The request handling pipeline is composed as a series of *middleware* components.</span></span> <span data-ttu-id="14461-268">Na przykład oprogramowanie pośredniczące może obsługiwać żądania plików statycznych lub przekierowywać żądania HTTP do protokołu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="14461-268">For example, a middleware might handle requests for static files or redirect HTTP requests to HTTPS.</span></span> <span data-ttu-id="14461-269">Każde oprogramowanie pośredniczące wykonuje operacje asynchroniczne na a `HttpContext` następnie wywołuje następne oprogramowanie pośredniczące w potoku lub kończy żądanie.</span><span class="sxs-lookup"><span data-stu-id="14461-269">Each middleware performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span> <span data-ttu-id="14461-270">Kod, aby skonfigurować potok obsługi `Startup.Configure` żądań jest dodawany do metody.</span><span class="sxs-lookup"><span data-stu-id="14461-270">Code to configure the request handling pipeline is added to the `Startup.Configure` method.</span></span>

<span data-ttu-id="14461-271">Oto przykładowa `Startup` klasa:</span><span class="sxs-lookup"><span data-stu-id="14461-271">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=3,12)]

<span data-ttu-id="14461-272">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="14461-272">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="14461-273">Wstrzykiwanie zależności (usługi)</span><span class="sxs-lookup"><span data-stu-id="14461-273">Dependency injection (services)</span></span>

<span data-ttu-id="14461-274">ASP.NET Core ma wbudowaną strukturę iniekcji zależności (DI), która udostępnia skonfigurowane usługi klasom aplikacji.</span><span class="sxs-lookup"><span data-stu-id="14461-274">ASP.NET Core has a built-in dependency injection (DI) framework that makes configured services available to an app's classes.</span></span> <span data-ttu-id="14461-275">Jednym ze sposobów uzyskania wystąpienia usługi w klasie jest utworzenie konstruktora z parametrem wymaganego typu.</span><span class="sxs-lookup"><span data-stu-id="14461-275">One way to get an instance of a service in a class is to create a constructor with a parameter of the required type.</span></span> <span data-ttu-id="14461-276">Parametrem może być typ usługi lub interfejs.</span><span class="sxs-lookup"><span data-stu-id="14461-276">The parameter can be the service type or an interface.</span></span> <span data-ttu-id="14461-277">System DI zapewnia usługę w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="14461-277">The DI system provides the service at runtime.</span></span>

<span data-ttu-id="14461-278">Oto klasa, która używa DI, aby uzyskać obiekt kontekstu Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="14461-278">Here's a class that uses DI to get an Entity Framework Core context object.</span></span> <span data-ttu-id="14461-279">Podświetlona linia jest przykładem iniekcji konstruktora:</span><span class="sxs-lookup"><span data-stu-id="14461-279">The highlighted line is an example of constructor injection:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="14461-280">Gdy di jest wbudowany, został zaprojektowany, aby umożliwić podłączenie kontenera inwersji sterowania (IoC) innej firmy, jeśli wolisz.</span><span class="sxs-lookup"><span data-stu-id="14461-280">While DI is built in, it's designed to let you plug in a third-party Inversion of Control (IoC) container if you prefer.</span></span>

<span data-ttu-id="14461-281">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="14461-281">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="14461-282">Oprogramowanie pośredniczące</span><span class="sxs-lookup"><span data-stu-id="14461-282">Middleware</span></span>

<span data-ttu-id="14461-283">Potok obsługi żądań składa się jako seria składników oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="14461-283">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="14461-284">Każdy składnik wykonuje operacje asynchroniczne na a `HttpContext` następnie wywołuje następne oprogramowanie pośredniczące w potoku lub kończy żądanie.</span><span class="sxs-lookup"><span data-stu-id="14461-284">Each component performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="14461-285">Zgodnie z konwencją składnik oprogramowania pośredniczącego `Use...` jest dodawany `Startup.Configure` do potoku przez wywołanie jego metody rozszerzenia w metodzie.</span><span class="sxs-lookup"><span data-stu-id="14461-285">By convention, a middleware component is added to the pipeline by invoking its `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="14461-286">Na przykład, aby włączyć renderowanie plików `UseStaticFiles`statycznych, wywołaj .</span><span class="sxs-lookup"><span data-stu-id="14461-286">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="14461-287">Wyróżniony kod w poniższym przykładzie konfiguruje potok obsługi żądań:</span><span class="sxs-lookup"><span data-stu-id="14461-287">The highlighted code in the following example configures the request handling pipeline:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=14-16)]

<span data-ttu-id="14461-288">ASP.NET Core zawiera bogaty zestaw wbudowanego oprogramowania pośredniczącego i można pisać niestandardowe oprogramowanie pośredniczące.</span><span class="sxs-lookup"><span data-stu-id="14461-288">ASP.NET Core includes a rich set of built-in middleware, and you can write custom middleware.</span></span>

<span data-ttu-id="14461-289">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="14461-289">For more information, see <xref:fundamentals/middleware/index>.</span></span>

## <a name="host"></a><span data-ttu-id="14461-290">Host</span><span class="sxs-lookup"><span data-stu-id="14461-290">Host</span></span>

<span data-ttu-id="14461-291">Aplikacja ASP.NET Core tworzy *hosta* podczas uruchamiania.</span><span class="sxs-lookup"><span data-stu-id="14461-291">An ASP.NET Core app builds a *host* on startup.</span></span> <span data-ttu-id="14461-292">Host jest obiektem, który hermetyzuje wszystkie zasoby aplikacji, takie jak:</span><span class="sxs-lookup"><span data-stu-id="14461-292">The host is an object that encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="14461-293">Implementacja serwera HTTP</span><span class="sxs-lookup"><span data-stu-id="14461-293">An HTTP server implementation</span></span>
* <span data-ttu-id="14461-294">Składniki oprogramowania pośredniczącego</span><span class="sxs-lookup"><span data-stu-id="14461-294">Middleware components</span></span>
* <span data-ttu-id="14461-295">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="14461-295">Logging</span></span>
* <span data-ttu-id="14461-296">Di</span><span class="sxs-lookup"><span data-stu-id="14461-296">DI</span></span>
* <span data-ttu-id="14461-297">Konfigurowanie</span><span class="sxs-lookup"><span data-stu-id="14461-297">Configuration</span></span>

<span data-ttu-id="14461-298">Głównym powodem włączenia wszystkich współzależnych zasobów aplikacji w jednym obiekcie jest zarządzanie okresem istnienia: kontrola nad uruchamianiem aplikacji i wdzięczne zamykanie.</span><span class="sxs-lookup"><span data-stu-id="14461-298">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="14461-299">Dostępne są dwa hosty: Host internetowy i Host ogólny.</span><span class="sxs-lookup"><span data-stu-id="14461-299">Two hosts are available: the Web Host and the Generic Host.</span></span> <span data-ttu-id="14461-300">W ASP.NET Core 2.x host ogólny jest przeznaczony tylko dla scenariuszy innych niż web.</span><span class="sxs-lookup"><span data-stu-id="14461-300">In ASP.NET Core 2.x, the Generic Host is only for non-web scenarios.</span></span>

<span data-ttu-id="14461-301">Kod do utworzenia hosta znajduje się w `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="14461-301">The code to create a host is in `Program.Main`:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Program.cs)]

<span data-ttu-id="14461-302">Metoda `CreateDefaultBuilder` konfiguruje hosta z powszechnie używanymi opcjami, takimi jak:</span><span class="sxs-lookup"><span data-stu-id="14461-302">The `CreateDefaultBuilder` method configures a host with commonly used options, such as the following:</span></span>

* <span data-ttu-id="14461-303">Użyj [Kestrel](#servers) jako serwera sieci web i włącz integrację usług IIS.</span><span class="sxs-lookup"><span data-stu-id="14461-303">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="14461-304">Załaduj konfigurację z *pliku appsettings.json*, *appsettings.{ Nazwa środowiska}.json*, zmienne środowiskowe, argumenty wiersza polecenia i inne źródła konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="14461-304">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="14461-305">Wyślij dane wyjściowe rejestrowania do dostawców konsoli i debugowania.</span><span class="sxs-lookup"><span data-stu-id="14461-305">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="14461-306">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/web-host>.</span><span class="sxs-lookup"><span data-stu-id="14461-306">For more information, see <xref:fundamentals/host/web-host>.</span></span>

### <a name="non-web-scenarios"></a><span data-ttu-id="14461-307">Scenariusze inne niż internetowe</span><span class="sxs-lookup"><span data-stu-id="14461-307">Non-web scenarios</span></span>

<span data-ttu-id="14461-308">Host ogólny umożliwia innym typom aplikacji używanie rozszerzeń struktury przekrojowych, takich jak rejestrowanie, iniekcja zależności (DI), konfiguracja i zarządzanie okresem istnienia aplikacji.</span><span class="sxs-lookup"><span data-stu-id="14461-308">The Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, dependency injection (DI), configuration, and app lifetime management.</span></span> <span data-ttu-id="14461-309">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/generic-host> i <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="14461-309">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="14461-310">Serwery</span><span class="sxs-lookup"><span data-stu-id="14461-310">Servers</span></span>

<span data-ttu-id="14461-311">Aplikacja ASP.NET Core używa implementacji serwera HTTP do nasłuchiwać żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="14461-311">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="14461-312">Serwer powierzchnie żądań do aplikacji jako zestaw funkcji `HttpContext` [żądania](xref:fundamentals/request-features) składa się w .</span><span class="sxs-lookup"><span data-stu-id="14461-312">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="14461-313">Windows</span><span class="sxs-lookup"><span data-stu-id="14461-313">Windows</span></span>](#tab/windows)

<span data-ttu-id="14461-314">ASP.NET Core udostępnia następujące implementacje serwera:</span><span class="sxs-lookup"><span data-stu-id="14461-314">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="14461-315">*Pustułka* to wieloplatformowy serwer www.</span><span class="sxs-lookup"><span data-stu-id="14461-315">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="14461-316">Pustułka jest często uruchamiana w odwrotnej konfiguracji serwera proxy przy użyciu [usług IIS](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="14461-316">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="14461-317">Pustułka może być uruchamiana jako publiczny serwer brzegowy narażony bezpośrednio do Internetu.</span><span class="sxs-lookup"><span data-stu-id="14461-317">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="14461-318">*Serwer HTTP usług IIS* to serwer dla systemu Windows, który korzysta z usług IIS.</span><span class="sxs-lookup"><span data-stu-id="14461-318">*IIS HTTP Server* is a server for windows that uses IIS.</span></span> <span data-ttu-id="14461-319">Z tego serwera ASP.NET Core aplikacji i usług IIS uruchomić w tym samym procesie.</span><span class="sxs-lookup"><span data-stu-id="14461-319">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="14461-320">*HTTP.sys* to serwer dla systemu Windows, który nie jest używany z usługami IIS.</span><span class="sxs-lookup"><span data-stu-id="14461-320">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="14461-321">macOS</span><span class="sxs-lookup"><span data-stu-id="14461-321">macOS</span></span>](#tab/macos)

<span data-ttu-id="14461-322">ASP.NET Core zapewnia implementację serwera międzyplatformowego *Kestrel.*</span><span class="sxs-lookup"><span data-stu-id="14461-322">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="14461-323">Pustułka może być uruchamiana jako publiczny serwer brzegowy narażony bezpośrednio do Internetu.</span><span class="sxs-lookup"><span data-stu-id="14461-323">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="14461-324">Pustułka jest często uruchamiana w odwrotnej konfiguracji proxy z [Nginx](https://nginx.org) lub [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="14461-324">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="14461-325">Linux</span><span class="sxs-lookup"><span data-stu-id="14461-325">Linux</span></span>](#tab/linux)

<span data-ttu-id="14461-326">ASP.NET Core zapewnia implementację serwera międzyplatformowego *Kestrel.*</span><span class="sxs-lookup"><span data-stu-id="14461-326">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="14461-327">Pustułka może być uruchamiana jako publiczny serwer brzegowy narażony bezpośrednio do Internetu.</span><span class="sxs-lookup"><span data-stu-id="14461-327">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="14461-328">Pustułka jest często uruchamiana w odwrotnej konfiguracji proxy z [Nginx](https://nginx.org) lub [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="14461-328">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="14461-329">Windows</span><span class="sxs-lookup"><span data-stu-id="14461-329">Windows</span></span>](#tab/windows)

<span data-ttu-id="14461-330">ASP.NET Core udostępnia następujące implementacje serwera:</span><span class="sxs-lookup"><span data-stu-id="14461-330">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="14461-331">*Pustułka* to wieloplatformowy serwer www.</span><span class="sxs-lookup"><span data-stu-id="14461-331">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="14461-332">Pustułka jest często uruchamiana w odwrotnej konfiguracji serwera proxy przy użyciu [usług IIS](https://www.iis.net/).</span><span class="sxs-lookup"><span data-stu-id="14461-332">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="14461-333">Pustułka może być uruchamiana jako publiczny serwer brzegowy narażony bezpośrednio do Internetu.</span><span class="sxs-lookup"><span data-stu-id="14461-333">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="14461-334">*HTTP.sys* to serwer dla systemu Windows, który nie jest używany z usługami IIS.</span><span class="sxs-lookup"><span data-stu-id="14461-334">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="14461-335">macOS</span><span class="sxs-lookup"><span data-stu-id="14461-335">macOS</span></span>](#tab/macos)

<span data-ttu-id="14461-336">ASP.NET Core zapewnia implementację serwera międzyplatformowego *Kestrel.*</span><span class="sxs-lookup"><span data-stu-id="14461-336">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="14461-337">Pustułka może być uruchamiana jako publiczny serwer brzegowy narażony bezpośrednio do Internetu.</span><span class="sxs-lookup"><span data-stu-id="14461-337">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="14461-338">Pustułka jest często uruchamiana w odwrotnej konfiguracji proxy z [Nginx](https://nginx.org) lub [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="14461-338">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="14461-339">Linux</span><span class="sxs-lookup"><span data-stu-id="14461-339">Linux</span></span>](#tab/linux)

<span data-ttu-id="14461-340">ASP.NET Core zapewnia implementację serwera międzyplatformowego *Kestrel.*</span><span class="sxs-lookup"><span data-stu-id="14461-340">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="14461-341">Pustułka może być uruchamiana jako publiczny serwer brzegowy narażony bezpośrednio do Internetu.</span><span class="sxs-lookup"><span data-stu-id="14461-341">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="14461-342">Pustułka jest często uruchamiana w odwrotnej konfiguracji proxy z [Nginx](https://nginx.org) lub [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="14461-342">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="14461-343">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/servers/index>.</span><span class="sxs-lookup"><span data-stu-id="14461-343">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="14461-344">Konfigurowanie</span><span class="sxs-lookup"><span data-stu-id="14461-344">Configuration</span></span>

<span data-ttu-id="14461-345">ASP.NET Core zapewnia strukturę konfiguracji, która pobiera ustawienia jako pary nazwa-wartość z uporządkowanego zestawu dostawców konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="14461-345">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="14461-346">Istnieją wbudowane dostawcy konfiguracji dla różnych źródeł, takich jak pliki *json,* pliki *xml,* zmienne środowiskowe i argumenty wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="14461-346">There are built-in configuration providers for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="14461-347">Można również napisać dostawców konfiguracji niestandardowych.</span><span class="sxs-lookup"><span data-stu-id="14461-347">You can also write custom configuration providers.</span></span>

<span data-ttu-id="14461-348">Na przykład można określić, że konfiguracja pochodzi z *appsettings.json* i zmiennych środowiskowych.</span><span class="sxs-lookup"><span data-stu-id="14461-348">For example, you could specify that configuration comes from *appsettings.json* and environment variables.</span></span> <span data-ttu-id="14461-349">Następnie, gdy żądana jest wartość *ConnectionString,* struktura wygląda najpierw w pliku *appsettings.json.*</span><span class="sxs-lookup"><span data-stu-id="14461-349">Then when the value of *ConnectionString* is requested, the framework looks first in the *appsettings.json* file.</span></span> <span data-ttu-id="14461-350">Jeśli wartość zostanie tam znaleziona, ale również w zmiennej środowiskowej, wartość ze zmiennej środowiskowej będzie miała pierwszeństwo.</span><span class="sxs-lookup"><span data-stu-id="14461-350">If the value is found there but also in an environment variable, the value from the environment variable would take precedence.</span></span>

<span data-ttu-id="14461-351">Do zarządzania poufnymi danymi konfiguracyjnymi, takimi jak hasła, ASP.NET Core udostępnia [narzędzie Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="14461-351">For managing confidential configuration data such as passwords, ASP.NET Core provides a [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="14461-352">W przypadku wpisów tajnych produkcji zalecamy [usługę Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="14461-352">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="14461-353">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="14461-353">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="options"></a><span data-ttu-id="14461-354">Opcje</span><span class="sxs-lookup"><span data-stu-id="14461-354">Options</span></span>

<span data-ttu-id="14461-355">Jeśli to możliwe, ASP.NET Core następuje *wzorzec opcji* do przechowywania i pobierania wartości konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="14461-355">Where possible, ASP.NET Core follows the *options pattern* for storing and retrieving configuration values.</span></span> <span data-ttu-id="14461-356">Wzorzec opcji używa klas do reprezentowania grup powiązanych ustawień.</span><span class="sxs-lookup"><span data-stu-id="14461-356">The options pattern uses classes to represent groups of related settings.</span></span>

<span data-ttu-id="14461-357">Na przykład następujący kod ustawia opcje WebSockets:</span><span class="sxs-lookup"><span data-stu-id="14461-357">For example, the following code sets WebSockets options:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/UseWebSockets.cs)]

<span data-ttu-id="14461-358">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="14461-358">For more information, see <xref:fundamentals/configuration/options>.</span></span>

## <a name="environments"></a><span data-ttu-id="14461-359">Środowiska</span><span class="sxs-lookup"><span data-stu-id="14461-359">Environments</span></span>

<span data-ttu-id="14461-360">Środowiska wykonywania, takie jak *Development,* *Staging*i *Production*, są pierwszorzędnym pojęciem w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="14461-360">Execution environments, such as *Development*, *Staging*, and *Production*, are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="14461-361">Można określić środowisko, w które działa `ASPNETCORE_ENVIRONMENT` aplikacja, ustawiając zmienną środowiskową.</span><span class="sxs-lookup"><span data-stu-id="14461-361">You can specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="14461-362">ASP.NET Core odczytuje tę zmienną środowiskową `IHostingEnvironment` podczas uruchamiania aplikacji i przechowuje wartość w implementacji.</span><span class="sxs-lookup"><span data-stu-id="14461-362">ASP.NET Core reads that environment variable at app startup and stores the value in an `IHostingEnvironment` implementation.</span></span> <span data-ttu-id="14461-363">Obiekt środowiska jest dostępny w dowolnym miejscu w aplikacji za pośrednictwem DI.</span><span class="sxs-lookup"><span data-stu-id="14461-363">The environment object is available anywhere in the app via DI.</span></span>

<span data-ttu-id="14461-364">Poniższy przykładowy `Startup` kod z klasy konfiguruje aplikację, aby zapewnić szczegółowe informacje o błędzie tylko wtedy, gdy jest uruchamiana w rozwoju:</span><span class="sxs-lookup"><span data-stu-id="14461-364">The following sample code from the `Startup` class configures the app to provide detailed error information only when it runs in development:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/StartupConfigure.cs?highlight=3-6)]

<span data-ttu-id="14461-365">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="14461-365">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="14461-366">Rejestrowanie</span><span class="sxs-lookup"><span data-stu-id="14461-366">Logging</span></span>

<span data-ttu-id="14461-367">ASP.NET Core obsługuje interfejs API rejestrowania, który współpracuje z różnymi wbudowanymi i zewnętrznymi dostawcami rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="14461-367">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="14461-368">Dostępni dostawcy są następujący:</span><span class="sxs-lookup"><span data-stu-id="14461-368">Available providers include the following:</span></span>

* <span data-ttu-id="14461-369">Konsola</span><span class="sxs-lookup"><span data-stu-id="14461-369">Console</span></span>
* <span data-ttu-id="14461-370">Debugowanie</span><span class="sxs-lookup"><span data-stu-id="14461-370">Debug</span></span>
* <span data-ttu-id="14461-371">Śledzenie zdarzeń w systemie Windows</span><span class="sxs-lookup"><span data-stu-id="14461-371">Event Tracing on Windows</span></span>
* <span data-ttu-id="14461-372">Dziennik zdarzeń systemu Windows</span><span class="sxs-lookup"><span data-stu-id="14461-372">Windows Event Log</span></span>
* <span data-ttu-id="14461-373">TraceSource</span><span class="sxs-lookup"><span data-stu-id="14461-373">TraceSource</span></span>
* <span data-ttu-id="14461-374">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="14461-374">Azure App Service</span></span>
* <span data-ttu-id="14461-375">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="14461-375">Azure Application Insights</span></span>

<span data-ttu-id="14461-376">Zapisuj dzienniki z dowolnego miejsca w `ILogger` kodzie aplikacji, uzyskując obiekt z DI i wywołując metody dziennika.</span><span class="sxs-lookup"><span data-stu-id="14461-376">Write logs from anywhere in an app's code by getting an `ILogger` object from DI and calling log methods.</span></span>

<span data-ttu-id="14461-377">Oto przykładowy kod, który `ILogger` używa obiektu, z iniekcji konstruktora i wywołanie metody rejestrowania wyróżnione.</span><span class="sxs-lookup"><span data-stu-id="14461-377">Here's sample code that uses an `ILogger` object, with constructor injection and the logging method calls highlighted.</span></span>

[!code-csharp[](index/samples_snapshot/2.x/TodoController.cs?highlight=5,13,17)]

<span data-ttu-id="14461-378">Interfejs `ILogger` umożliwia przekazywanie dowolnej liczby pól do dostawcy rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="14461-378">The `ILogger` interface lets you pass any number of fields to the logging provider.</span></span> <span data-ttu-id="14461-379">Pola są często używane do konstruowania ciągu wiadomości, ale dostawca może również wysłać je jako oddzielne pola do magazynu danych.</span><span class="sxs-lookup"><span data-stu-id="14461-379">The fields are commonly used to construct a message string, but the provider can also send them as separate fields to a data store.</span></span> <span data-ttu-id="14461-380">Ta funkcja umożliwia dostawcom rejestrowania zaimplementowanie [rejestrowania semantycznego, znanego również jako rejestrowanie strukturalne.](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)</span><span class="sxs-lookup"><span data-stu-id="14461-380">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="14461-381">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="14461-381">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="14461-382">Routing</span><span class="sxs-lookup"><span data-stu-id="14461-382">Routing</span></span>

<span data-ttu-id="14461-383">*Trasa* jest wzorcem adresu URL, który jest mapowany do programu obsługi.</span><span class="sxs-lookup"><span data-stu-id="14461-383">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="14461-384">Program obsługi jest zazwyczaj razor strony, metody akcji w kontrolerze MVC lub oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="14461-384">The handler is typically a Razor page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="14461-385">ASP.NET Routing podstawowy daje ci kontrolę nad adresami URL używanymi przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="14461-385">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="14461-386">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="14461-386">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="14461-387">Obsługa błędów</span><span class="sxs-lookup"><span data-stu-id="14461-387">Error handling</span></span>

<span data-ttu-id="14461-388">ASP.NET Core ma wbudowane funkcje obsługi błędów, takie jak:</span><span class="sxs-lookup"><span data-stu-id="14461-388">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="14461-389">Strona wyjątku dla deweloperów</span><span class="sxs-lookup"><span data-stu-id="14461-389">A developer exception page</span></span>
* <span data-ttu-id="14461-390">Niestandardowe strony błędów</span><span class="sxs-lookup"><span data-stu-id="14461-390">Custom error pages</span></span>
* <span data-ttu-id="14461-391">Statyczne strony kodowe stanu</span><span class="sxs-lookup"><span data-stu-id="14461-391">Static status code pages</span></span>
* <span data-ttu-id="14461-392">Obsługa wyjątków uruchamiania</span><span class="sxs-lookup"><span data-stu-id="14461-392">Startup exception handling</span></span>

<span data-ttu-id="14461-393">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="14461-393">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="14461-394">Zgłaszanie żądań HTTP</span><span class="sxs-lookup"><span data-stu-id="14461-394">Make HTTP requests</span></span>

<span data-ttu-id="14461-395">Implementacja `IHttpClientFactory` jest dostępna do `HttpClient` tworzenia wystąpień.</span><span class="sxs-lookup"><span data-stu-id="14461-395">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="14461-396">Fabryka:</span><span class="sxs-lookup"><span data-stu-id="14461-396">The factory:</span></span>

* <span data-ttu-id="14461-397">Zapewnia centralną lokalizację nazewnictwa `HttpClient` i konfigurowania wystąpień logicznych.</span><span class="sxs-lookup"><span data-stu-id="14461-397">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="14461-398">Na przykład klient *github* może być zarejestrowany i skonfigurowany do uzyskiwania dostępu do usługi GitHub.</span><span class="sxs-lookup"><span data-stu-id="14461-398">For example, a *github* client can be registered and configured to access GitHub.</span></span> <span data-ttu-id="14461-399">Domyślny klient może być zarejestrowany do innych celów.</span><span class="sxs-lookup"><span data-stu-id="14461-399">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="14461-400">Obsługuje rejestrację i tworzenie łańcucha wielu programów obsługi delegujących do tworzenia potoku oprogramowania pośredniczącego żądania wychodzącego.</span><span class="sxs-lookup"><span data-stu-id="14461-400">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="14461-401">Ten wzorzec jest podobny do potoku przychodzącego oprogramowania pośredniczącego w ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="14461-401">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="14461-402">Wzorzec zapewnia mechanizm zarządzania przekrojowe problemy wokół żądań HTTP, w tym buforowania, obsługi błędów, serializacji i rejestrowania.</span><span class="sxs-lookup"><span data-stu-id="14461-402">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="14461-403">Integruje się z *Polly*, popularną biblioteką innych firm do obsługi błędów przejściowych.</span><span class="sxs-lookup"><span data-stu-id="14461-403">Integrates with *Polly*, a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="14461-404">Zarządza buforowanie i okres `HttpClientHandler` istnienia wystąpienia podstawowe, aby uniknąć typowych `HttpClient` problemów z systemem DNS, które występują podczas ręcznego zarządzania okresami istnienia.</span><span class="sxs-lookup"><span data-stu-id="14461-404">Manages the pooling and lifetime of underlying `HttpClientHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="14461-405">Dodaje konfigurowalne środowisko rejestrowania (za pośrednictwem) `ILogger`dla wszystkich żądań wysyłanych za pośrednictwem klientów utworzonych przez fabrykę.</span><span class="sxs-lookup"><span data-stu-id="14461-405">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="14461-406">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="14461-406">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="14461-407">Katalog główny zawartości</span><span class="sxs-lookup"><span data-stu-id="14461-407">Content root</span></span>

<span data-ttu-id="14461-408">Katalog główny zawartości jest podstawową ścieżką do:</span><span class="sxs-lookup"><span data-stu-id="14461-408">The content root is the base path to the:</span></span>

* <span data-ttu-id="14461-409">Plik wykonywalny hosting aplikacji (*.exe*).</span><span class="sxs-lookup"><span data-stu-id="14461-409">Executable hosting the app (*.exe*).</span></span>
* <span data-ttu-id="14461-410">Skompilowane zestawy, które tworzą aplikację (*.dll*).</span><span class="sxs-lookup"><span data-stu-id="14461-410">Compiled assemblies that make up the app (*.dll*).</span></span>
* <span data-ttu-id="14461-411">Pliki zawartości niekodowej używane przez aplikację, takie jak:</span><span class="sxs-lookup"><span data-stu-id="14461-411">Non-code content files used by the app, such as:</span></span>
  * <span data-ttu-id="14461-412">Pliki razor (*.cshtml*, *.brzytwa*)</span><span class="sxs-lookup"><span data-stu-id="14461-412">Razor files (*.cshtml*, *.razor*)</span></span>
  * <span data-ttu-id="14461-413">Pliki konfiguracyjne (*.json*, *.xml*)</span><span class="sxs-lookup"><span data-stu-id="14461-413">Configuration files (*.json*, *.xml*)</span></span>
  * <span data-ttu-id="14461-414">Pliki danych (*.db*)</span><span class="sxs-lookup"><span data-stu-id="14461-414">Data files (*.db*)</span></span>
* <span data-ttu-id="14461-415">[Katalog główny sieci Web](#web-root), zazwyczaj opublikowany folder *wwwroot.*</span><span class="sxs-lookup"><span data-stu-id="14461-415">[Web root](#web-root), typically the published *wwwroot* folder.</span></span>

<span data-ttu-id="14461-416">Podczas rozwoju:</span><span class="sxs-lookup"><span data-stu-id="14461-416">During development:</span></span>

* <span data-ttu-id="14461-417">Katalog główny zawartości jest domyślnie wyświetlany w katalogu głównym projektu.</span><span class="sxs-lookup"><span data-stu-id="14461-417">The content root defaults to the project's root directory.</span></span>
* <span data-ttu-id="14461-418">Katalog główny projektu jest używany do tworzenia:</span><span class="sxs-lookup"><span data-stu-id="14461-418">The project's root directory is used to create the:</span></span>
  * <span data-ttu-id="14461-419">Ścieżka do plików zawartości niekodowej aplikacji w katalogu głównym projektu.</span><span class="sxs-lookup"><span data-stu-id="14461-419">Path to the app's non-code content files in the project's root directory.</span></span>
  * <span data-ttu-id="14461-420">[Katalog główny sieci Web](#web-root), zazwyczaj folder *wwwroot* w katalogu głównym projektu.</span><span class="sxs-lookup"><span data-stu-id="14461-420">[Web root](#web-root), typically the *wwwroot* folder in the project's root directory.</span></span>

<span data-ttu-id="14461-421">Alternatywna ścieżka główna zawartości może być określona podczas [tworzenia hosta](#host).</span><span class="sxs-lookup"><span data-stu-id="14461-421">An alternative content root path can be specified when [building the host](#host).</span></span> <span data-ttu-id="14461-422">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/host/web-host#content-root>.</span><span class="sxs-lookup"><span data-stu-id="14461-422">For more information, see <xref:fundamentals/host/web-host#content-root>.</span></span>

## <a name="web-root"></a><span data-ttu-id="14461-423">Katalog główny sieci Web</span><span class="sxs-lookup"><span data-stu-id="14461-423">Web root</span></span>

<span data-ttu-id="14461-424">Katalog główny sieci Web jest podstawową ścieżką do publicznych, niekodowych, statycznych plików zasobów, takich jak:</span><span class="sxs-lookup"><span data-stu-id="14461-424">The web root is the base path to public, non-code, static resource files, such as:</span></span>

* <span data-ttu-id="14461-425">Arkusze stylów (*.css*)</span><span class="sxs-lookup"><span data-stu-id="14461-425">Stylesheets (*.css*)</span></span>
* <span data-ttu-id="14461-426">JavaScript (*.js*)</span><span class="sxs-lookup"><span data-stu-id="14461-426">JavaScript (*.js*)</span></span>
* <span data-ttu-id="14461-427">Zdjęcia (*.png*, *.jpg*)</span><span class="sxs-lookup"><span data-stu-id="14461-427">Images (*.png*, *.jpg*)</span></span>

<span data-ttu-id="14461-428">Pliki statyczne są domyślnie obsługiwane tylko z katalogu głównego sieci Web (i podkatastów).</span><span class="sxs-lookup"><span data-stu-id="14461-428">Static files are only served by default from the web root directory (and sub-directories).</span></span>

<span data-ttu-id="14461-429">Ścieżka główna sieci Web domyślnie *{zawartość root}/wwwroot*, ale podczas [tworzenia hosta](#host)można określić inny katalog główny sieci Web.</span><span class="sxs-lookup"><span data-stu-id="14461-429">The web root path defaults to *{content root}/wwwroot*, but a different web root can be specified when [building the host](#host).</span></span> <span data-ttu-id="14461-430">Aby uzyskać więcej informacji, zobacz [Katalog główny sieci Web](xref:fundamentals/host/web-host#web-root).</span><span class="sxs-lookup"><span data-stu-id="14461-430">For more information, see [Web root](xref:fundamentals/host/web-host#web-root).</span></span>

<span data-ttu-id="14461-431">Zapobiegaj publikowaniu plików w [ \<](/visualstudio/msbuild/common-msbuild-project-items#content) *witrynie wwwroot* za pomocą elementu projektu> zawartości w pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="14461-431">Prevent publishing files in *wwwroot* with the [\<Content> project item](/visualstudio/msbuild/common-msbuild-project-items#content) in the project file.</span></span> <span data-ttu-id="14461-432">Poniższy przykład zapobiega publikowaniu zawartości w *katalogu wwwroot/local* i podkatastronach:</span><span class="sxs-lookup"><span data-stu-id="14461-432">The following example prevents publishing content in the *wwwroot/local* directory and sub-directories:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="14461-433">W razor (*.cshtml*) plików,`~/`tylda-ukośnik ( ) wskazuje na katalog główny sieci Web.</span><span class="sxs-lookup"><span data-stu-id="14461-433">In Razor (*.cshtml*) files, the tilde-slash (`~/`) points to the web root.</span></span> <span data-ttu-id="14461-434">Ścieżka rozpoczynająca `~/` się od ścieżki jest określana jako *ścieżka wirtualna*.</span><span class="sxs-lookup"><span data-stu-id="14461-434">A path beginning with `~/` is referred to as a *virtual path*.</span></span>

<span data-ttu-id="14461-435">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="14461-435">For more information, see <xref:fundamentals/static-files>.</span></span>

::: moniker-end
