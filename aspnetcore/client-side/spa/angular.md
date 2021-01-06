---
title: Użyj szablonu projektu kątowego z ASP.NET Core
author: SteveSandersonMS
description: Dowiedz się, jak rozpocząć pracę z szablonem projektu aplikacji jednostronicowej (SPA) ASP.NET Core dla elementu kątowego i kątowego interfejsu wiersza polecenia.
monikerRange: '>= aspnetcore-2.1'
ms.author: stevesa
ms.custom: mvc
ms.date: 02/06/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: spa/angular
ms.openlocfilehash: 2fff0d60b71bbbab9347dbe74cad023264247388
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "93054570"
---
# <a name="use-the-angular-project-template-with-aspnet-core"></a><span data-ttu-id="741fa-103">Użyj szablonu projektu kątowego z ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="741fa-103">Use the Angular project template with ASP.NET Core</span></span>

<span data-ttu-id="741fa-104">Zaktualizowany szablon projektu kątowego zapewnia wygodny punkt początkowy dla ASP.NET Core aplikacji przy użyciu funkcji kątowych i kątowych interfejsu wiersza polecenia, aby zaimplementować rozbudowany interfejs użytkownika po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="741fa-104">The updated Angular project template provides a convenient starting point for ASP.NET Core apps using Angular and the Angular CLI to implement a rich, client-side user interface (UI).</span></span>

<span data-ttu-id="741fa-105">Szablon jest równoznaczny z tworzeniem ASP.NET Core projektu do działania jako zaplecza interfejsu API oraz projektu kątowego interfejsu wiersza polecenia do działania jako interfejs użytkownika.</span><span class="sxs-lookup"><span data-stu-id="741fa-105">The template is equivalent to creating an ASP.NET Core project to act as an API backend and an Angular CLI project to act as a UI.</span></span> <span data-ttu-id="741fa-106">Szablon oferuje wygodę hostingu obu typów projektów w jednym projekcie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="741fa-106">The template offers the convenience of hosting both project types in a single app project.</span></span> <span data-ttu-id="741fa-107">W związku z tym projekt aplikacji można skompilować i opublikować jako pojedynczą jednostkę.</span><span class="sxs-lookup"><span data-stu-id="741fa-107">Consequently, the app project can be built and published as a single unit.</span></span>

## <a name="create-a-new-app"></a><span data-ttu-id="741fa-108">Tworzenie nowej aplikacji</span><span class="sxs-lookup"><span data-stu-id="741fa-108">Create a new app</span></span>

<span data-ttu-id="741fa-109">Jeśli zainstalowano ASP.NET Core 2,1, nie ma potrzeby instalowania szablonu projektu kątowego.</span><span class="sxs-lookup"><span data-stu-id="741fa-109">If you have ASP.NET Core 2.1 installed, there's no need to install the Angular project template.</span></span>

<span data-ttu-id="741fa-110">Utwórz nowy projekt z wiersza polecenia przy użyciu polecenia `dotnet new angular` w pustym katalogu.</span><span class="sxs-lookup"><span data-stu-id="741fa-110">Create a new project from a command prompt using the command `dotnet new angular` in an empty directory.</span></span> <span data-ttu-id="741fa-111">Na przykład następujące polecenia tworzą aplikację w katalogu *My-New-App* i przełączają się do tego katalogu:</span><span class="sxs-lookup"><span data-stu-id="741fa-111">For example, the following commands create the app in a *my-new-app* directory and switch to that directory:</span></span>

```dotnetcli
dotnet new angular -o my-new-app
cd my-new-app
```

<span data-ttu-id="741fa-112">Uruchom aplikację z poziomu programu Visual Studio lub interfejs wiersza polecenia platformy .NET Core:</span><span class="sxs-lookup"><span data-stu-id="741fa-112">Run the app from either Visual Studio or the .NET Core CLI:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="741fa-113">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="741fa-113">Visual Studio</span></span>](#tab/visual-studio/)

<span data-ttu-id="741fa-114">Otwórz wygenerowany plik *csproj* i uruchom aplikację w zwykły sposób.</span><span class="sxs-lookup"><span data-stu-id="741fa-114">Open the generated *.csproj* file, and run the app as normal from there.</span></span>

<span data-ttu-id="741fa-115">Proces kompilacji przywraca zależności npm w pierwszym przebiegu, co może potrwać kilka minut.</span><span class="sxs-lookup"><span data-stu-id="741fa-115">The build process restores npm dependencies on the first run, which can take several minutes.</span></span> <span data-ttu-id="741fa-116">Kolejne kompilacje są znacznie szybsze.</span><span class="sxs-lookup"><span data-stu-id="741fa-116">Subsequent builds are much faster.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="741fa-117">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="741fa-117">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="741fa-118">Upewnij się, że masz zmienną środowiskową o nazwie `ASPNETCORE_Environment` z wartością `Development` .</span><span class="sxs-lookup"><span data-stu-id="741fa-118">Ensure you have an environment variable called `ASPNETCORE_Environment` with a value of `Development`.</span></span> <span data-ttu-id="741fa-119">W systemie Windows (w komunikatach innych niż programu PowerShell) Uruchom polecenie `SET ASPNETCORE_Environment=Development` .</span><span class="sxs-lookup"><span data-stu-id="741fa-119">On Windows (in non-PowerShell prompts), run `SET ASPNETCORE_Environment=Development`.</span></span> <span data-ttu-id="741fa-120">W systemie Linux lub macOS Uruchom polecenie `export ASPNETCORE_Environment=Development` .</span><span class="sxs-lookup"><span data-stu-id="741fa-120">On Linux or macOS, run `export ASPNETCORE_Environment=Development`.</span></span>

<span data-ttu-id="741fa-121">Uruchom [kompilację dotnet](/dotnet/core/tools/dotnet-build) , aby sprawdzić, czy aplikacja jest poprawnie skompilowana.</span><span class="sxs-lookup"><span data-stu-id="741fa-121">Run [dotnet build](/dotnet/core/tools/dotnet-build) to verify the app builds correctly.</span></span> <span data-ttu-id="741fa-122">W pierwszym uruchomieniu proces kompilacji przywraca zależności npm, co może potrwać kilka minut.</span><span class="sxs-lookup"><span data-stu-id="741fa-122">On the first run, the build process restores npm dependencies, which can take several minutes.</span></span> <span data-ttu-id="741fa-123">Kolejne kompilacje są znacznie szybsze.</span><span class="sxs-lookup"><span data-stu-id="741fa-123">Subsequent builds are much faster.</span></span>

<span data-ttu-id="741fa-124">Uruchom [uruchomienie programu dotnet](/dotnet/core/tools/dotnet-run) , aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="741fa-124">Run [dotnet run](/dotnet/core/tools/dotnet-run) to start the app.</span></span> <span data-ttu-id="741fa-125">Rejestrowany jest komunikat podobny do następującego:</span><span class="sxs-lookup"><span data-stu-id="741fa-125">A message similar to the following is logged:</span></span>

```console
Now listening on: http://localhost:<port>
```

<span data-ttu-id="741fa-126">Przejdź do tego adresu URL w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="741fa-126">Navigate to this URL in a browser.</span></span>

> [!WARNING]
> <span data-ttu-id="741fa-127">Aplikacja uruchamia wystąpienie kątowego serwera interfejsu wiersza polecenia w tle.</span><span class="sxs-lookup"><span data-stu-id="741fa-127">The app starts up an instance of the Angular CLI server in the background.</span></span> <span data-ttu-id="741fa-128">Rejestrowany jest komunikat podobny do następującego: Usługa *anie na żywo serwer programistyczny nasłuchuje na hoście lokalnym: &lt; otherport &gt; , Otwórz przeglądarkę do http://localhost:&lt ; otherport &gt; /*.</span><span class="sxs-lookup"><span data-stu-id="741fa-128">A message similar to the following is logged: *NG Live Development Server is listening on localhost:&lt;otherport&gt;, open a browser to http://localhost:&lt;otherport&gt;/*.</span></span> <span data-ttu-id="741fa-129">Zignoruj ten komunikat, &mdash; ponieważ **nie** jest to adres URL połączonej ASP.NET Core i aplikacji interfejsu wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="741fa-129">Ignore this message&mdash;it's **not** the URL for the combined ASP.NET Core and Angular CLI app.</span></span>

---

<span data-ttu-id="741fa-130">Szablon projektu tworzy aplikację ASP.NET Core i aplikację kątową.</span><span class="sxs-lookup"><span data-stu-id="741fa-130">The project template creates an ASP.NET Core app and an Angular app.</span></span> <span data-ttu-id="741fa-131">Aplikacja ASP.NET Core jest przeznaczona do użycia na potrzeby dostępu do danych, autoryzacji i innych zagadnień po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="741fa-131">The ASP.NET Core app is intended to be used for data access, authorization, and other server-side concerns.</span></span> <span data-ttu-id="741fa-132">Aplikacja kątowa znajdująca się w podkatalogu *ClientApp* jest przeznaczona do użycia dla wszystkich zagadnień związanych z interfejsem użytkownika.</span><span class="sxs-lookup"><span data-stu-id="741fa-132">The Angular app, residing in the *ClientApp* subdirectory, is intended to be used for all UI concerns.</span></span>

## <a name="add-pages-images-styles-modules-etc"></a><span data-ttu-id="741fa-133">Dodawanie stron, obrazów, stylów, modułów itp.</span><span class="sxs-lookup"><span data-stu-id="741fa-133">Add pages, images, styles, modules, etc.</span></span>

<span data-ttu-id="741fa-134">Katalog *ClientApp* zawiera standardową aplikację interfejsu wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="741fa-134">The *ClientApp* directory contains a standard Angular CLI app.</span></span> <span data-ttu-id="741fa-135">Aby uzyskać więcej informacji, zobacz oficjalną [dokumentację kątową](https://angular.io) .</span><span class="sxs-lookup"><span data-stu-id="741fa-135">See the official [Angular documentation](https://angular.io) for more information.</span></span>

<span data-ttu-id="741fa-136">Istnieją niewielkie różnice między aplikacją kątową utworzoną przez ten szablon a nią utworzoną przez skośny interfejs wiersza polecenia (za pośrednictwem `ng new` ), jednak możliwości aplikacji nie są zmieniane.</span><span class="sxs-lookup"><span data-stu-id="741fa-136">There are slight differences between the Angular app created by this template and the one created by Angular CLI itself (via `ng new`); however, the app's capabilities are unchanged.</span></span> <span data-ttu-id="741fa-137">Aplikacja utworzona przez szablon zawiera układ oparty na [Bootstrap](https://getbootstrap.com/)i podstawowy przykład routingu.</span><span class="sxs-lookup"><span data-stu-id="741fa-137">The app created by the template contains a [Bootstrap](https://getbootstrap.com/)-based layout and a basic routing example.</span></span>

## <a name="run-ng-commands"></a><span data-ttu-id="741fa-138">Uruchamianie poleceń ng</span><span class="sxs-lookup"><span data-stu-id="741fa-138">Run ng commands</span></span>

<span data-ttu-id="741fa-139">W wierszu polecenia przejdź do podkatalogu *ClientApp* :</span><span class="sxs-lookup"><span data-stu-id="741fa-139">In a command prompt, switch to the *ClientApp* subdirectory:</span></span>

```console
cd ClientApp
```

<span data-ttu-id="741fa-140">Jeśli `ng` Narzędzie jest zainstalowane globalnie, można uruchomić dowolne z jego poleceń.</span><span class="sxs-lookup"><span data-stu-id="741fa-140">If you have the `ng` tool installed globally, you can run any of its commands.</span></span> <span data-ttu-id="741fa-141">Na przykład można uruchomić `ng lint` , `ng test` lub dowolne z innych [poleceń kątowych interfejsu wiersza polecenia](https://angular.io/cli).</span><span class="sxs-lookup"><span data-stu-id="741fa-141">For example, you can run `ng lint`, `ng test`, or any of the other [Angular CLI commands](https://angular.io/cli).</span></span> <span data-ttu-id="741fa-142">Nie ma potrzeby uruchamiania mimo tego `ng serve` , że aplikacja ASP.NET Core obsługuje zarówno części aplikacji po stronie serwera, jak i klienta.</span><span class="sxs-lookup"><span data-stu-id="741fa-142">There's no need to run `ng serve` though, because your ASP.NET Core app deals with serving both server-side and client-side parts of your app.</span></span> <span data-ttu-id="741fa-143">Wewnętrznie używa go `ng serve` w trakcie opracowywania.</span><span class="sxs-lookup"><span data-stu-id="741fa-143">Internally, it uses `ng serve` in development.</span></span>

<span data-ttu-id="741fa-144">Jeśli nie masz `ng` zainstalowanego narzędzia, uruchom polecenie `npm run ng` .</span><span class="sxs-lookup"><span data-stu-id="741fa-144">If you don't have the `ng` tool installed, run `npm run ng` instead.</span></span> <span data-ttu-id="741fa-145">Na przykład można uruchomić system `npm run ng lint` lub `npm run ng test` .</span><span class="sxs-lookup"><span data-stu-id="741fa-145">For example, you can run `npm run ng lint` or `npm run ng test`.</span></span>

## <a name="install-npm-packages"></a><span data-ttu-id="741fa-146">Zainstaluj pakiety npm</span><span class="sxs-lookup"><span data-stu-id="741fa-146">Install npm packages</span></span>

<span data-ttu-id="741fa-147">Aby zainstalować pakiety npm innych firm, należy użyć wiersza polecenia w podkatalogu *ClientApp* .</span><span class="sxs-lookup"><span data-stu-id="741fa-147">To install third-party npm packages, use a command prompt in the *ClientApp* subdirectory.</span></span> <span data-ttu-id="741fa-148">Przykład:</span><span class="sxs-lookup"><span data-stu-id="741fa-148">For example:</span></span>

```console
cd ClientApp
npm install --save <package_name>
```

## <a name="publish-and-deploy"></a><span data-ttu-id="741fa-149">Publikowanie i wdrażanie</span><span class="sxs-lookup"><span data-stu-id="741fa-149">Publish and deploy</span></span>

<span data-ttu-id="741fa-150">W trakcie opracowywania aplikacja jest uruchamiana w trybie zoptymalizowanym pod kątem wygody dla deweloperów.</span><span class="sxs-lookup"><span data-stu-id="741fa-150">In development, the app runs in a mode optimized for developer convenience.</span></span> <span data-ttu-id="741fa-151">Na przykład pakiety JavaScript zawierają mapy źródłowe (w przypadku debugowania można zobaczyć oryginalny kod TypeScript).</span><span class="sxs-lookup"><span data-stu-id="741fa-151">For example, JavaScript bundles include source maps (so that when debugging, you can see your original TypeScript code).</span></span> <span data-ttu-id="741fa-152">Aplikacja będzie oglądać zmiany w plikach TypeScript, HTML i CSS na dysku, a następnie automatycznie ponownie kompilować i ładuje je, gdy zobaczy zmiany tych plików.</span><span class="sxs-lookup"><span data-stu-id="741fa-152">The app watches for TypeScript, HTML, and CSS file changes on disk and automatically recompiles and reloads when it sees those files change.</span></span>

<span data-ttu-id="741fa-153">W środowisku produkcyjnym można korzystać z wersji aplikacji zoptymalizowanej pod kątem wydajności.</span><span class="sxs-lookup"><span data-stu-id="741fa-153">In production, serve a version of your app that's optimized for performance.</span></span> <span data-ttu-id="741fa-154">To jest skonfigurowane tak, aby były wykonywane automatycznie.</span><span class="sxs-lookup"><span data-stu-id="741fa-154">This is configured to happen automatically.</span></span> <span data-ttu-id="741fa-155">Podczas publikowania konfiguracja kompilacji emituje kompilowaną kompilację kodu po stronie klienta (zminimalizowanego).</span><span class="sxs-lookup"><span data-stu-id="741fa-155">When you publish, the build configuration emits a minified, ahead-of-time (AoT) compiled build of your client-side code.</span></span> <span data-ttu-id="741fa-156">W przeciwieństwie do kompilacji deweloperskiej kompilacja produkcyjna nie wymaga zainstalowania Node.js na serwerze (o ile nie włączono renderowania po stronie serwera (SSR)).</span><span class="sxs-lookup"><span data-stu-id="741fa-156">Unlike the development build, the production build doesn't require Node.js to be installed on the server (unless you have enabled server-side rendering (SSR)).</span></span>

<span data-ttu-id="741fa-157">Można użyć standardowych [ASP.NET Core metod hostingu i wdrażania](xref:host-and-deploy/index).</span><span class="sxs-lookup"><span data-stu-id="741fa-157">You can use standard [ASP.NET Core hosting and deployment methods](xref:host-and-deploy/index).</span></span>

## <a name="run-ng-serve-independently"></a><span data-ttu-id="741fa-158">Uruchom "anie" niezależnie</span><span class="sxs-lookup"><span data-stu-id="741fa-158">Run "ng serve" independently</span></span>

<span data-ttu-id="741fa-159">Projekt jest skonfigurowany tak, aby uruchamiał własne wystąpienie kątowego serwera interfejsu wiersza polecenia w tle podczas uruchamiania aplikacji ASP.NET Core w trybie tworzenia.</span><span class="sxs-lookup"><span data-stu-id="741fa-159">The project is configured to start its own instance of the Angular CLI server in the background when the ASP.NET Core app starts in development mode.</span></span> <span data-ttu-id="741fa-160">Jest to wygodne, ponieważ nie trzeba ręcznie uruchamiać oddzielnego serwera.</span><span class="sxs-lookup"><span data-stu-id="741fa-160">This is convenient because you don't have to run a separate server manually.</span></span>

<span data-ttu-id="741fa-161">Istnieje zwrot do tej konfiguracji domyślnej.</span><span class="sxs-lookup"><span data-stu-id="741fa-161">There's a drawback to this default setup.</span></span> <span data-ttu-id="741fa-162">Za każdym razem, gdy modyfikujesz kod w języku C#, a aplikacja ASP.NET Core wymaga ponownego uruchomienia, serwer interfejsu CLI kątowych zostanie uruchomiony ponownie.</span><span class="sxs-lookup"><span data-stu-id="741fa-162">Each time you modify your C# code and your ASP.NET Core app needs to restart, the Angular CLI server restarts.</span></span> <span data-ttu-id="741fa-163">Aby rozpocząć tworzenie kopii zapasowej, wymagane jest około 10 sekund.</span><span class="sxs-lookup"><span data-stu-id="741fa-163">Around 10 seconds is required to start back up.</span></span> <span data-ttu-id="741fa-164">Jeśli tworzysz częste edycje kodu w języku C# i nie chcesz czekać na ponowne uruchomienie interfejsu wiersza polecenia, uruchom skośny serwer interfejsu wiersza polecenia, niezależnie od procesu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="741fa-164">If you're making frequent C# code edits and don't want to wait for Angular CLI to restart, run the Angular CLI server externally, independently of the ASP.NET Core process.</span></span> <span data-ttu-id="741fa-165">W tym celu:</span><span class="sxs-lookup"><span data-stu-id="741fa-165">To do so:</span></span>

1. <span data-ttu-id="741fa-166">W wierszu polecenia przejdź do podkatalogu *ClientApp* i uruchom kątowy serwer programistyczny CLI:</span><span class="sxs-lookup"><span data-stu-id="741fa-166">In a command prompt, switch to the *ClientApp* subdirectory, and launch the Angular CLI development server:</span></span>

    ```console
    cd ClientApp
    npm start
    ```

    > [!IMPORTANT]
    > <span data-ttu-id="741fa-167">Użyj polecenia, `npm start` Aby uruchomić serwer programistyczny języka CLI, `ng serve` a nie, aby konfiguracja w *package.js* była przestrzegana.</span><span class="sxs-lookup"><span data-stu-id="741fa-167">Use `npm start` to launch the Angular CLI development server, not `ng serve`, so that the configuration in *package.json* is respected.</span></span> <span data-ttu-id="741fa-168">Aby przekazać dodatkowe parametry do serwera kątowego interfejsu wiersza polecenia, należy dodać je do odpowiedniego `scripts` wiersza w *package.js* pliku.</span><span class="sxs-lookup"><span data-stu-id="741fa-168">To pass additional parameters to the Angular CLI server, add them to the relevant `scripts` line in your *package.json* file.</span></span>

2. <span data-ttu-id="741fa-169">Zmodyfikuj aplikację ASP.NET Core tak, aby korzystała z zewnętrznego wystąpienia interfejsu wiersza polecenia, zamiast uruchamiania jednego z nich.</span><span class="sxs-lookup"><span data-stu-id="741fa-169">Modify your ASP.NET Core app to use the external Angular CLI instance instead of launching one of its own.</span></span> <span data-ttu-id="741fa-170">W klasie *uruchomieniowej* Zastąp `spa.UseAngularCliServer` wywołanie następującym:</span><span class="sxs-lookup"><span data-stu-id="741fa-170">In your *Startup* class, replace the `spa.UseAngularCliServer` invocation with the following:</span></span>

    ```csharp
    spa.UseProxyToSpaDevelopmentServer("http://localhost:4200");
    ```

<span data-ttu-id="741fa-171">Po uruchomieniu aplikacji ASP.NET Core nie zostanie uruchomiony kątowy serwer interfejsu wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="741fa-171">When you start your ASP.NET Core app, it won't launch an Angular CLI server.</span></span> <span data-ttu-id="741fa-172">Wystąpienie, które zostało uruchomione ręcznie, jest używane w zamian.</span><span class="sxs-lookup"><span data-stu-id="741fa-172">The instance you started manually is used instead.</span></span> <span data-ttu-id="741fa-173">Pozwala to na szybkie uruchamianie i ponowne uruchamianie.</span><span class="sxs-lookup"><span data-stu-id="741fa-173">This enables it to start and restart faster.</span></span> <span data-ttu-id="741fa-174">Nie czeka już na kątowy interfejs wiersza polecenia, aby ponownie skompilować aplikację kliencką za każdym razem.</span><span class="sxs-lookup"><span data-stu-id="741fa-174">It's no longer waiting for Angular CLI to rebuild your client app each time.</span></span>

### <a name="pass-data-from-net-code-into-typescript-code"></a><span data-ttu-id="741fa-175">Przekazywanie danych z kodu platformy .NET do kodu TypeScript</span><span class="sxs-lookup"><span data-stu-id="741fa-175">Pass data from .NET code into TypeScript code</span></span>

<span data-ttu-id="741fa-176">Podczas procesu SSR możesz chcieć przekazać dane dla żądania z aplikacji ASP.NET Core do aplikacji kątowej.</span><span class="sxs-lookup"><span data-stu-id="741fa-176">During SSR, you might want to pass per-request data from your ASP.NET Core app into your Angular app.</span></span> <span data-ttu-id="741fa-177">Na przykład można przekazać cookie informacje lub coś odczytanego z bazy danych.</span><span class="sxs-lookup"><span data-stu-id="741fa-177">For example, you could pass cookie information or something read from a database.</span></span> <span data-ttu-id="741fa-178">Aby to zrobić, Edytuj klasę *uruchomieniową* .</span><span class="sxs-lookup"><span data-stu-id="741fa-178">To do this, edit your *Startup* class.</span></span> <span data-ttu-id="741fa-179">W polu wywołania zwrotne dla `UseSpaPrerendering` Ustaw wartość dla `options.SupplyData` opcji:</span><span class="sxs-lookup"><span data-stu-id="741fa-179">In the callback for `UseSpaPrerendering`, set a value for `options.SupplyData` such as the following:</span></span>

```csharp
options.SupplyData = (context, data) =>
{
    // Creates a new value called isHttpsRequest that's passed to TypeScript code
    data["isHttpsRequest"] = context.Request.IsHttps;
};
```

<span data-ttu-id="741fa-180">`SupplyData`Wywołanie zwrotne umożliwia przekazywanie dowolnych danych z możliwością serializacji kodu JSON (na przykład ciągów, wartości logicznych lub cyfr).</span><span class="sxs-lookup"><span data-stu-id="741fa-180">The `SupplyData` callback lets you pass arbitrary, per-request, JSON-serializable data (for example, strings, booleans, or numbers).</span></span> <span data-ttu-id="741fa-181">*Główny kod. Server. TS* odbiera to jako `params.data` .</span><span class="sxs-lookup"><span data-stu-id="741fa-181">Your *main.server.ts* code receives this as `params.data`.</span></span> <span data-ttu-id="741fa-182">Na przykład poprzedni przykładowy kod przekazuje wartość logiczną jako `params.data.isHttpsRequest` `createServerRenderer` wywołanie zwrotne.</span><span class="sxs-lookup"><span data-stu-id="741fa-182">For example, the preceding code sample passes a boolean value as `params.data.isHttpsRequest` into the `createServerRenderer` callback.</span></span> <span data-ttu-id="741fa-183">Można przekazać ten program do innych części aplikacji w dowolny sposób, który jest obsługiwany przez kątowe.</span><span class="sxs-lookup"><span data-stu-id="741fa-183">You can pass this to other parts of your app in any way supported by Angular.</span></span> <span data-ttu-id="741fa-184">Na przykład, zobacz jak *Main. Server. TS* przekazuje `BASE_URL` wartość do dowolnego składnika, którego Konstruktor został zadeklarowany w celu otrzymania go.</span><span class="sxs-lookup"><span data-stu-id="741fa-184">For example, see how *main.server.ts* passes the `BASE_URL` value to any component whose constructor is declared to receive it.</span></span>

### <a name="drawbacks-of-ssr"></a><span data-ttu-id="741fa-185">Wady systemu SSR</span><span class="sxs-lookup"><span data-stu-id="741fa-185">Drawbacks of SSR</span></span>

<span data-ttu-id="741fa-186">Nie wszystkie aplikacje korzystają z programu SSR.</span><span class="sxs-lookup"><span data-stu-id="741fa-186">Not all apps benefit from SSR.</span></span> <span data-ttu-id="741fa-187">Główną korzyścią jest postrzeganie wydajności.</span><span class="sxs-lookup"><span data-stu-id="741fa-187">The primary benefit is perceived performance.</span></span> <span data-ttu-id="741fa-188">Goście korzystający z aplikacji przez wolne połączenie sieciowe lub wolne urządzenia przenośne szybko zobaczą początkowy interfejs użytkownika, nawet jeśli pobieranie lub analizowanie pakietów JavaScript trwa dłużej.</span><span class="sxs-lookup"><span data-stu-id="741fa-188">Visitors reaching your app over a slow network connection or on slow mobile devices see the initial UI quickly, even if it takes a while to fetch or parse the JavaScript bundles.</span></span> <span data-ttu-id="741fa-189">Jednak wiele aplikacji jednostronicowych jest używanych głównie w szybkich, wewnętrznych sieciach firmowych na szybkich komputerach, na których aplikacja jest widoczna niemal natychmiast.</span><span class="sxs-lookup"><span data-stu-id="741fa-189">However, many SPAs are mainly used over fast, internal company networks on fast computers where the app appears almost instantly.</span></span>

<span data-ttu-id="741fa-190">W tym samym czasie istnieją znaczne wady umożliwiające włączenie SSR.</span><span class="sxs-lookup"><span data-stu-id="741fa-190">At the same time, there are significant drawbacks to enabling SSR.</span></span> <span data-ttu-id="741fa-191">Zwiększa złożoność procesu tworzenia.</span><span class="sxs-lookup"><span data-stu-id="741fa-191">It adds complexity to your development process.</span></span> <span data-ttu-id="741fa-192">Kod musi działać w dwóch różnych środowiskach: po stronie klienta i po stronie serwera (w środowisku Node.js wywoływanym z ASP.NET Core).</span><span class="sxs-lookup"><span data-stu-id="741fa-192">Your code must run in two different environments: client-side and server-side (in a Node.js environment invoked from ASP.NET Core).</span></span> <span data-ttu-id="741fa-193">Oto kilka rzeczy, na których warto mieć świadomość:</span><span class="sxs-lookup"><span data-stu-id="741fa-193">Here are some things to bear in mind:</span></span>

* <span data-ttu-id="741fa-194">SSR wymaga instalacji Node.js na serwerach produkcyjnych.</span><span class="sxs-lookup"><span data-stu-id="741fa-194">SSR requires a Node.js installation on your production servers.</span></span> <span data-ttu-id="741fa-195">W przypadku niektórych scenariuszy wdrażania, takich jak Azure App Services, ale nie dla innych, takich jak Azure Service Fabric.</span><span class="sxs-lookup"><span data-stu-id="741fa-195">This is automatically the case for some deployment scenarios, such as Azure App Services, but not for others, such as Azure Service Fabric.</span></span>
* <span data-ttu-id="741fa-196">Włączenie `BuildServerSideRenderer` flagi kompilacji powoduje, że katalog *node_modules* do opublikowania.</span><span class="sxs-lookup"><span data-stu-id="741fa-196">Enabling the `BuildServerSideRenderer` build flag causes your *node_modules* directory to publish.</span></span> <span data-ttu-id="741fa-197">Ten folder zawiera 20 000 plików, co wydłuża czas wdrażania.</span><span class="sxs-lookup"><span data-stu-id="741fa-197">This folder contains 20,000+ files, which increases deployment time.</span></span>
* <span data-ttu-id="741fa-198">Do uruchamiania kodu w środowisku Node.jsm nie można polegać na istnieniu interfejsów API JavaScript specyficznych dla przeglądarki, takich jak `window` lub `localStorage` .</span><span class="sxs-lookup"><span data-stu-id="741fa-198">To run your code in a Node.js environment, it can't rely on the existence of browser-specific JavaScript APIs such as `window` or `localStorage`.</span></span> <span data-ttu-id="741fa-199">Jeśli Twój kod (lub pewna dostosowana Biblioteka innej firmy) próbuje użyć tych interfejsów API, wystąpi błąd podczas procesu SSR.</span><span class="sxs-lookup"><span data-stu-id="741fa-199">If your code (or some third-party library you reference) tries to use these APIs, you'll get an error during SSR.</span></span> <span data-ttu-id="741fa-200">Na przykład nie należy używać jQuery, ponieważ odwołuje się do interfejsów API specyficznych dla przeglądarki w wielu miejscach.</span><span class="sxs-lookup"><span data-stu-id="741fa-200">For example, don't use jQuery because it references browser-specific APIs in many places.</span></span> <span data-ttu-id="741fa-201">Aby zapobiec błędom, należy koniecznie uniknąć lub uniknąć używania interfejsów API lub bibliotek specyficznych dla przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="741fa-201">To prevent errors, you must either avoid SSR or avoid browser-specific APIs or libraries.</span></span> <span data-ttu-id="741fa-202">Możesz otoczyć wszystkie wywołania takich interfejsów API w celu upewnienia się, że nie są one wywoływane podczas SSR.</span><span class="sxs-lookup"><span data-stu-id="741fa-202">You can wrap any calls to such APIs in checks to ensure they aren't invoked during SSR.</span></span> <span data-ttu-id="741fa-203">Na przykład użyj sprawdzenia, takiego jak następujące w kodzie JavaScript lub TypeScript:</span><span class="sxs-lookup"><span data-stu-id="741fa-203">For example, use a check such as the following in JavaScript or TypeScript code:</span></span>

    ```javascript
    if (typeof window !== 'undefined') {
        // Call browser-specific APIs here
    }
    ```

## <a name="additional-resources"></a><span data-ttu-id="741fa-204">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="741fa-204">Additional resources</span></span>

* <xref:security/authentication/identity/spa>
