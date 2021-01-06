---
title: Użyj szablonu projektu reagowanie z ASP.NET Core
author: SteveSandersonMS
description: Dowiedz się, jak rozpocząć pracę z szablonem projektu aplikacji jednostronicowej (SPA) ASP.NET Core na potrzeby reakcji i tworzenia aplikacji.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 03/07/2019
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
uid: spa/react
ms.openlocfilehash: 6c3539d96329489189f49a3af0c718791824be6b
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "93054453"
---
# <a name="use-the-react-project-template-with-aspnet-core"></a><span data-ttu-id="3a01a-103">Użyj szablonu projektu reagowanie z ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3a01a-103">Use the React project template with ASP.NET Core</span></span>

<span data-ttu-id="3a01a-104">Zaktualizowany szablon projektu dotyczącego reakcji umożliwia korzystanie z dogodnych punktów początkowych dla ASP.NET Core aplikacji przy użyciu konwencji reagowania i [tworzenia aplikacji](https://github.com/facebookincubator/create-react-app) (CRA) w celu zaimplementowania rozbudowanego interfejsu użytkownika po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="3a01a-104">The updated React project template provides a convenient starting point for ASP.NET Core apps using React and [create-react-app](https://github.com/facebookincubator/create-react-app) (CRA) conventions to implement a rich, client-side user interface (UI).</span></span>

<span data-ttu-id="3a01a-105">Szablon jest równoznaczny z tworzeniem projektu ASP.NET Core, który działa jako zaplecze interfejsu API, a standardowy projekt reakcji CRA reaguje na działanie jako interfejs użytkownika, ale z wygodą hostingu zarówno w jednym projekcie aplikacji, który można skompilować i opublikować jako pojedynczą jednostkę.</span><span class="sxs-lookup"><span data-stu-id="3a01a-105">The template is equivalent to creating both an ASP.NET Core project to act as an API backend, and a standard CRA React project to act as a UI, but with the convenience of hosting both in a single app project that can be built and published as a single unit.</span></span>

<span data-ttu-id="3a01a-106">Szablon projektu reaguje nie jest przeznaczony do renderowania po stronie serwera (SSR).</span><span class="sxs-lookup"><span data-stu-id="3a01a-106">The React project template isn't meant for server-side rendering (SSR).</span></span> <span data-ttu-id="3a01a-107">W przypadku programu SSR z reagowaniem na Node.js należy rozważyć [Next.js](https://github.com/zeit/next.js/) lub [Razzle](https://github.com/jaredpalmer/razzle).</span><span class="sxs-lookup"><span data-stu-id="3a01a-107">For SSR with React and Node.js, consider [Next.js](https://github.com/zeit/next.js/) or [Razzle](https://github.com/jaredpalmer/razzle).</span></span>

## <a name="create-a-new-app"></a><span data-ttu-id="3a01a-108">Tworzenie nowej aplikacji</span><span class="sxs-lookup"><span data-stu-id="3a01a-108">Create a new app</span></span>

<span data-ttu-id="3a01a-109">Jeśli zainstalowano ASP.NET Core 2,1, nie ma potrzeby instalowania szablonu projektu z reagowaniem.</span><span class="sxs-lookup"><span data-stu-id="3a01a-109">If you have ASP.NET Core 2.1 installed, there's no need to install the React project template.</span></span>

<span data-ttu-id="3a01a-110">Utwórz nowy projekt z wiersza polecenia przy użyciu polecenia `dotnet new react` w pustym katalogu.</span><span class="sxs-lookup"><span data-stu-id="3a01a-110">Create a new project from a command prompt using the command `dotnet new react` in an empty directory.</span></span> <span data-ttu-id="3a01a-111">Na przykład następujące polecenia tworzą aplikację w katalogu *My-New-App* i przełączają się do tego katalogu:</span><span class="sxs-lookup"><span data-stu-id="3a01a-111">For example, the following commands create the app in a *my-new-app* directory and switch to that directory:</span></span>

```dotnetcli
dotnet new react -o my-new-app
cd my-new-app
```

<span data-ttu-id="3a01a-112">Uruchom aplikację z poziomu programu Visual Studio lub interfejs wiersza polecenia platformy .NET Core:</span><span class="sxs-lookup"><span data-stu-id="3a01a-112">Run the app from either Visual Studio or the .NET Core CLI:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3a01a-113">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3a01a-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3a01a-114">Otwórz wygenerowany plik *csproj* i uruchom aplikację w zwykły sposób.</span><span class="sxs-lookup"><span data-stu-id="3a01a-114">Open the generated *.csproj* file, and run the app as normal from there.</span></span>

<span data-ttu-id="3a01a-115">Proces kompilacji przywraca zależności npm w pierwszym przebiegu, co może potrwać kilka minut.</span><span class="sxs-lookup"><span data-stu-id="3a01a-115">The build process restores npm dependencies on the first run, which can take several minutes.</span></span> <span data-ttu-id="3a01a-116">Kolejne kompilacje są znacznie szybsze.</span><span class="sxs-lookup"><span data-stu-id="3a01a-116">Subsequent builds are much faster.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="3a01a-117">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="3a01a-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="3a01a-118">Upewnij się, że masz zmienną środowiskową o nazwie `ASPNETCORE_Environment` z wartością `Development` .</span><span class="sxs-lookup"><span data-stu-id="3a01a-118">Ensure you have an environment variable called `ASPNETCORE_Environment` with value of `Development`.</span></span> <span data-ttu-id="3a01a-119">W systemie Windows (w komunikatach innych niż programu PowerShell) Uruchom polecenie `SET ASPNETCORE_Environment=Development` .</span><span class="sxs-lookup"><span data-stu-id="3a01a-119">On Windows (in non-PowerShell prompts), run `SET ASPNETCORE_Environment=Development`.</span></span> <span data-ttu-id="3a01a-120">W systemie Linux lub macOS Uruchom polecenie `export ASPNETCORE_Environment=Development` .</span><span class="sxs-lookup"><span data-stu-id="3a01a-120">On Linux or macOS, run `export ASPNETCORE_Environment=Development`.</span></span>

<span data-ttu-id="3a01a-121">Uruchom [kompilację dotnet](/dotnet/core/tools/dotnet-build) , aby sprawdzić, czy aplikacja jest poprawnie skompilowana.</span><span class="sxs-lookup"><span data-stu-id="3a01a-121">Run [dotnet build](/dotnet/core/tools/dotnet-build) to verify your app builds correctly.</span></span> <span data-ttu-id="3a01a-122">W pierwszym uruchomieniu proces kompilacji przywraca zależności npm, co może potrwać kilka minut.</span><span class="sxs-lookup"><span data-stu-id="3a01a-122">On the first run, the build process restores npm dependencies, which can take several minutes.</span></span> <span data-ttu-id="3a01a-123">Kolejne kompilacje są znacznie szybsze.</span><span class="sxs-lookup"><span data-stu-id="3a01a-123">Subsequent builds are much faster.</span></span>

<span data-ttu-id="3a01a-124">Uruchom [uruchomienie programu dotnet](/dotnet/core/tools/dotnet-run) , aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="3a01a-124">Run [dotnet run](/dotnet/core/tools/dotnet-run) to start the app.</span></span>

---

<span data-ttu-id="3a01a-125">Szablon projektu tworzy aplikację ASP.NET Core i aplikację do reagowania.</span><span class="sxs-lookup"><span data-stu-id="3a01a-125">The project template creates an ASP.NET Core app and a React app.</span></span> <span data-ttu-id="3a01a-126">Aplikacja ASP.NET Core jest przeznaczona do użycia na potrzeby dostępu do danych, autoryzacji i innych zagadnień po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="3a01a-126">The ASP.NET Core app is intended to be used for data access, authorization, and other server-side concerns.</span></span> <span data-ttu-id="3a01a-127">Aplikacja do reagowania znajdująca się w podkatalogu *ClientApp* jest przeznaczona do użycia w przypadku wszystkich problemów z interfejsem użytkownika.</span><span class="sxs-lookup"><span data-stu-id="3a01a-127">The React app, residing in the *ClientApp* subdirectory, is intended to be used for all UI concerns.</span></span>

## <a name="add-pages-images-styles-modules-etc"></a><span data-ttu-id="3a01a-128">Dodawanie stron, obrazów, stylów, modułów itp.</span><span class="sxs-lookup"><span data-stu-id="3a01a-128">Add pages, images, styles, modules, etc.</span></span>

<span data-ttu-id="3a01a-129">Katalog *ClientApp* jest standardową aplikacją do reagowania w CRA.</span><span class="sxs-lookup"><span data-stu-id="3a01a-129">The *ClientApp* directory is a standard CRA React app.</span></span> <span data-ttu-id="3a01a-130">Aby uzyskać więcej informacji, zobacz oficjalną [dokumentację CRA](https://create-react-app.dev/docs/getting-started/) .</span><span class="sxs-lookup"><span data-stu-id="3a01a-130">See the official [CRA documentation](https://create-react-app.dev/docs/getting-started/) for more information.</span></span>

<span data-ttu-id="3a01a-131">Istnieją niewielkie różnice między aplikacją reakcji utworzoną przez ten szablon a tym utworzonym przez CRA. jednak możliwości aplikacji nie są zmieniane.</span><span class="sxs-lookup"><span data-stu-id="3a01a-131">There are slight differences between the React app created by this template and the one created by CRA itself; however, the app's capabilities are unchanged.</span></span> <span data-ttu-id="3a01a-132">Aplikacja utworzona przez szablon zawiera układ oparty na [Bootstrap](https://getbootstrap.com/)i podstawowy przykład routingu.</span><span class="sxs-lookup"><span data-stu-id="3a01a-132">The app created by the template contains a [Bootstrap](https://getbootstrap.com/)-based layout and a basic routing example.</span></span>

## <a name="install-npm-packages"></a><span data-ttu-id="3a01a-133">Zainstaluj pakiety npm</span><span class="sxs-lookup"><span data-stu-id="3a01a-133">Install npm packages</span></span>

<span data-ttu-id="3a01a-134">Aby zainstalować pakiety npm innych firm, należy użyć wiersza polecenia w podkatalogu *ClientApp* .</span><span class="sxs-lookup"><span data-stu-id="3a01a-134">To install third-party npm packages, use a command prompt in the *ClientApp* subdirectory.</span></span> <span data-ttu-id="3a01a-135">Przykład:</span><span class="sxs-lookup"><span data-stu-id="3a01a-135">For example:</span></span>

```console
cd ClientApp
npm install --save <package_name>
```

## <a name="publish-and-deploy"></a><span data-ttu-id="3a01a-136">Publikowanie i wdrażanie</span><span class="sxs-lookup"><span data-stu-id="3a01a-136">Publish and deploy</span></span>

<span data-ttu-id="3a01a-137">W trakcie opracowywania aplikacja jest uruchamiana w trybie zoptymalizowanym pod kątem wygody dla deweloperów.</span><span class="sxs-lookup"><span data-stu-id="3a01a-137">In development, the app runs in a mode optimized for developer convenience.</span></span> <span data-ttu-id="3a01a-138">Na przykład pakiety JavaScript zawierają mapy źródłowe (w przypadku debugowania można zobaczyć oryginalny kod źródłowy).</span><span class="sxs-lookup"><span data-stu-id="3a01a-138">For example, JavaScript bundles include source maps (so that when debugging, you can see your original source code).</span></span> <span data-ttu-id="3a01a-139">Aplikacja obserwuje zmiany plików JavaScript, HTML i CSS na dysku, a następnie automatycznie ponownie kompiluje i ładuje je, gdy zobaczy zmiany tych plików.</span><span class="sxs-lookup"><span data-stu-id="3a01a-139">The app watches JavaScript, HTML, and CSS file changes on disk and automatically recompiles and reloads when it sees those files change.</span></span>

<span data-ttu-id="3a01a-140">W środowisku produkcyjnym można korzystać z wersji aplikacji zoptymalizowanej pod kątem wydajności.</span><span class="sxs-lookup"><span data-stu-id="3a01a-140">In production, serve a version of your app that's optimized for performance.</span></span> <span data-ttu-id="3a01a-141">To jest skonfigurowane tak, aby były wykonywane automatycznie.</span><span class="sxs-lookup"><span data-stu-id="3a01a-141">This is configured to happen automatically.</span></span> <span data-ttu-id="3a01a-142">Podczas publikowania, konfiguracja kompilacji emituje zminimalizowanego, przenoszącą wbudowaną kompilację kodu po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="3a01a-142">When you publish, the build configuration emits a minified, transpiled build of your client-side code.</span></span> <span data-ttu-id="3a01a-143">W przeciwieństwie do kompilacji deweloperskiej kompilacja produkcyjna nie wymaga zainstalowania Node.js na serwerze.</span><span class="sxs-lookup"><span data-stu-id="3a01a-143">Unlike the development build, the production build doesn't require Node.js to be installed on the server.</span></span>

<span data-ttu-id="3a01a-144">Można użyć standardowych [ASP.NET Core metod hostingu i wdrażania](xref:host-and-deploy/index).</span><span class="sxs-lookup"><span data-stu-id="3a01a-144">You can use standard [ASP.NET Core hosting and deployment methods](xref:host-and-deploy/index).</span></span>

## <a name="run-the-cra-server-independently"></a><span data-ttu-id="3a01a-145">Uruchom serwer CRA niezależnie</span><span class="sxs-lookup"><span data-stu-id="3a01a-145">Run the CRA server independently</span></span>

<span data-ttu-id="3a01a-146">Projekt jest skonfigurowany tak, aby uruchamiał własne wystąpienie serwera CRA Development w tle podczas uruchamiania aplikacji ASP.NET Core w trybie tworzenia.</span><span class="sxs-lookup"><span data-stu-id="3a01a-146">The project is configured to start its own instance of the CRA development server in the background when the ASP.NET Core app starts in development mode.</span></span> <span data-ttu-id="3a01a-147">Jest to wygodne, ponieważ oznacza to, że nie trzeba ręcznie uruchamiać oddzielnego serwera.</span><span class="sxs-lookup"><span data-stu-id="3a01a-147">This is convenient because it means you don't have to run a separate server manually.</span></span>

<span data-ttu-id="3a01a-148">Istnieje zwrot do tej konfiguracji domyślnej.</span><span class="sxs-lookup"><span data-stu-id="3a01a-148">There's a drawback to this default setup.</span></span> <span data-ttu-id="3a01a-149">Za każdym razem, gdy modyfikujesz kod w języku C#, a aplikacja ASP.NET Core wymaga ponownego uruchomienia, serwer CRA zostanie uruchomiony ponownie.</span><span class="sxs-lookup"><span data-stu-id="3a01a-149">Each time you modify your C# code and your ASP.NET Core app needs to restart, the CRA server restarts.</span></span> <span data-ttu-id="3a01a-150">Aby rozpocząć tworzenie kopii zapasowej, wymagane są kilka sekund.</span><span class="sxs-lookup"><span data-stu-id="3a01a-150">A few seconds are required to start back up.</span></span> <span data-ttu-id="3a01a-151">Jeśli tworzysz częste edycje kodu w języku C# i nie chcesz czekać na ponowne uruchomienie serwera CRA, uruchom serwer CRA na zewnątrz niezależnie od procesu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3a01a-151">If you're making frequent C# code edits and don't want to wait for the CRA server to restart, run the CRA server externally, independently of the ASP.NET Core process.</span></span> <span data-ttu-id="3a01a-152">W tym celu:</span><span class="sxs-lookup"><span data-stu-id="3a01a-152">To do so:</span></span>

1. <span data-ttu-id="3a01a-153">Dodaj plik *ENV* do podkatalogu *ClientApp* przy użyciu następującego ustawienia:</span><span class="sxs-lookup"><span data-stu-id="3a01a-153">Add a *.env* file to the *ClientApp* subdirectory with the following setting:</span></span>

    ```
    BROWSER=none
    ```

    <span data-ttu-id="3a01a-154">Uniemożliwi to otwieranie przeglądarki sieci Web podczas zewnętrznego uruchamiania serwera CRA.</span><span class="sxs-lookup"><span data-stu-id="3a01a-154">This will prevent your web browser from opening when starting the CRA server externally.</span></span>

2. <span data-ttu-id="3a01a-155">W wierszu polecenia przejdź do podkatalogu *ClientApp* i uruchom serwer deweloperski CRA:</span><span class="sxs-lookup"><span data-stu-id="3a01a-155">In a command prompt, switch to the *ClientApp* subdirectory, and launch the CRA development server:</span></span>

    ```console
    cd ClientApp
    npm start
    ```

3. <span data-ttu-id="3a01a-156">Zmodyfikuj aplikację ASP.NET Core tak, aby korzystała z zewnętrznego wystąpienia serwera CRA, zamiast uruchamiania jednego z nich.</span><span class="sxs-lookup"><span data-stu-id="3a01a-156">Modify your ASP.NET Core app to use the external CRA server instance instead of launching one of its own.</span></span> <span data-ttu-id="3a01a-157">W klasie *uruchomieniowej* Zastąp `spa.UseReactDevelopmentServer` wywołanie następującym:</span><span class="sxs-lookup"><span data-stu-id="3a01a-157">In your *Startup* class, replace the `spa.UseReactDevelopmentServer` invocation with the following:</span></span>

    ```csharp
    spa.UseProxyToSpaDevelopmentServer("http://localhost:3000");
    ```

<span data-ttu-id="3a01a-158">Po uruchomieniu aplikacji ASP.NET Core nie zostanie uruchomiony serwer CRA.</span><span class="sxs-lookup"><span data-stu-id="3a01a-158">When you start your ASP.NET Core app, it won't launch a CRA server.</span></span> <span data-ttu-id="3a01a-159">Wystąpienie, które zostało uruchomione ręcznie, jest używane w zamian.</span><span class="sxs-lookup"><span data-stu-id="3a01a-159">The instance you started manually is used instead.</span></span> <span data-ttu-id="3a01a-160">Pozwala to na szybkie uruchamianie i ponowne uruchamianie.</span><span class="sxs-lookup"><span data-stu-id="3a01a-160">This enables it to start and restart faster.</span></span> <span data-ttu-id="3a01a-161">Nie czeka już na odbudowanie aplikacji z reagowaniem za każdym razem.</span><span class="sxs-lookup"><span data-stu-id="3a01a-161">It's no longer waiting for your React app to rebuild each time.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="3a01a-162">"Renderowanie po stronie serwera" nie jest obsługiwaną funkcją tego szablonu.</span><span class="sxs-lookup"><span data-stu-id="3a01a-162">"Server-side rendering" is not a supported feature of this template.</span></span> <span data-ttu-id="3a01a-163">Naszym celem tego szablonu jest spełnienie warunków dotyczących "Tworzenie — reagowanie aplikacji".</span><span class="sxs-lookup"><span data-stu-id="3a01a-163">Our goal with this template is to meet parity with "create-react-app".</span></span> <span data-ttu-id="3a01a-164">W związku z tym scenariusze i funkcje, które nie są uwzględnione w projekcie "Create-rereagować na aplikacje" (na przykład SSR), nie są obsługiwane i pozostają jako ćwiczenie dla użytkownika.</span><span class="sxs-lookup"><span data-stu-id="3a01a-164">As such, scenarios and features not included in a "create-react-app" project (such as SSR) are not supported and are left as an exercise for the user.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3a01a-165">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="3a01a-165">Additional resources</span></span>

* <xref:security/authentication/identity/spa>
