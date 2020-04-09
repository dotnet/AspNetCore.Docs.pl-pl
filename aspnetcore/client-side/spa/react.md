---
title: Użyj szablonu projektu React z ASP.NET Core
author: SteveSandersonMS
description: Dowiedz się, jak rozpocząć pracę z szablonem projektu ASP.NET Core Single Page Application (SPA) dla aplikacji React i create-react-app.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 03/07/2019
uid: spa/react
ms.openlocfilehash: 9703a62eb7f779974382fe0fb01702d9fcd37d64
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664963"
---
# <a name="use-the-react-project-template-with-aspnet-core"></a><span data-ttu-id="5e643-103">Użyj szablonu projektu React z ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5e643-103">Use the React project template with ASP.NET Core</span></span>

<span data-ttu-id="5e643-104">Zaktualizowany szablon projektu React zapewnia wygodny punkt wyjścia dla aplikacji ASP.NET Core przy użyciu konwencji React i create-react-app (CRA) w celu zaimplementowania bogatego interfejsu użytkownika po stronie klienta.The updated React project template provides a convenient starting point for ASP.NET Core apps using React and [create-react-app](https://github.com/facebookincubator/create-react-app) (CRA) conventions to implement a rich, client-side user interface (UI).</span><span class="sxs-lookup"><span data-stu-id="5e643-104">The updated React project template provides a convenient starting point for ASP.NET Core apps using React and [create-react-app](https://github.com/facebookincubator/create-react-app) (CRA) conventions to implement a rich, client-side user interface (UI).</span></span>

<span data-ttu-id="5e643-105">Szablon jest odpowiednikiem tworzenia zarówno projektu ASP.NET Core do działania jako zaplecze interfejsu API, jak i standardowego projektu CRA React, aby działać jako interfejs użytkownika, ale z wygodą hostingu zarówno w jednym projekcie aplikacji, który może być zbudowany i opublikowany jako pojedyncza jednostka.</span><span class="sxs-lookup"><span data-stu-id="5e643-105">The template is equivalent to creating both an ASP.NET Core project to act as an API backend, and a standard CRA React project to act as a UI, but with the convenience of hosting both in a single app project that can be built and published as a single unit.</span></span>

<span data-ttu-id="5e643-106">Szablon projektu React nie jest przeznaczony do renderowania po stronie serwera (SSR).</span><span class="sxs-lookup"><span data-stu-id="5e643-106">The React project template isn't meant for server-side rendering (SSR).</span></span> <span data-ttu-id="5e643-107">Dla SSR z React i Node.js, należy wziąć pod uwagę [Next.js](https://github.com/zeit/next.js/) lub [Razzle](https://github.com/jaredpalmer/razzle).</span><span class="sxs-lookup"><span data-stu-id="5e643-107">For SSR with React and Node.js, consider [Next.js](https://github.com/zeit/next.js/) or [Razzle](https://github.com/jaredpalmer/razzle).</span></span>

## <a name="create-a-new-app"></a><span data-ttu-id="5e643-108">Tworzenie nowej aplikacji</span><span class="sxs-lookup"><span data-stu-id="5e643-108">Create a new app</span></span>

<span data-ttu-id="5e643-109">Jeśli masz ASP.NET zainstalowanym core 2.1, nie ma potrzeby instalowania szablonu projektu React.</span><span class="sxs-lookup"><span data-stu-id="5e643-109">If you have ASP.NET Core 2.1 installed, there's no need to install the React project template.</span></span>

<span data-ttu-id="5e643-110">Utwórz nowy projekt z wiersza `dotnet new react` polecenia za pomocą polecenia w pustym katalogu.</span><span class="sxs-lookup"><span data-stu-id="5e643-110">Create a new project from a command prompt using the command `dotnet new react` in an empty directory.</span></span> <span data-ttu-id="5e643-111">Na przykład następujące polecenia tworzą aplikację w katalogu *my-new-app* i przełączają się do tego katalogu:</span><span class="sxs-lookup"><span data-stu-id="5e643-111">For example, the following commands create the app in a *my-new-app* directory and switch to that directory:</span></span>

```dotnetcli
dotnet new react -o my-new-app
cd my-new-app
```

<span data-ttu-id="5e643-112">Uruchom aplikację z programu Visual Studio lub interfejsu wiersza polecenia .NET Core:</span><span class="sxs-lookup"><span data-stu-id="5e643-112">Run the app from either Visual Studio or the .NET Core CLI:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5e643-113">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5e643-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5e643-114">Otwórz wygenerowany plik *csproj* i uruchom aplikację normalnie stamtąd.</span><span class="sxs-lookup"><span data-stu-id="5e643-114">Open the generated *.csproj* file, and run the app as normal from there.</span></span>

<span data-ttu-id="5e643-115">Proces kompilacji przywraca zależności npm przy pierwszym uruchomieniu, co może potrwać kilka minut.</span><span class="sxs-lookup"><span data-stu-id="5e643-115">The build process restores npm dependencies on the first run, which can take several minutes.</span></span> <span data-ttu-id="5e643-116">Kolejne kompilacje są znacznie szybsze.</span><span class="sxs-lookup"><span data-stu-id="5e643-116">Subsequent builds are much faster.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="5e643-117">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="5e643-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="5e643-118">Upewnij się, że `ASPNETCORE_Environment` masz zmienną środowiskową o nazwie o wartości `Development`.</span><span class="sxs-lookup"><span data-stu-id="5e643-118">Ensure you have an environment variable called `ASPNETCORE_Environment` with value of `Development`.</span></span> <span data-ttu-id="5e643-119">W systemie Windows (w monitach innych `SET ASPNETCORE_Environment=Development`niż powershell) uruchom program .</span><span class="sxs-lookup"><span data-stu-id="5e643-119">On Windows (in non-PowerShell prompts), run `SET ASPNETCORE_Environment=Development`.</span></span> <span data-ttu-id="5e643-120">W systemie Linux lub `export ASPNETCORE_Environment=Development`macOS uruchom plik .</span><span class="sxs-lookup"><span data-stu-id="5e643-120">On Linux or macOS, run `export ASPNETCORE_Environment=Development`.</span></span>

<span data-ttu-id="5e643-121">Uruchom [kompilację dotnet,](/dotnet/core/tools/dotnet-build) aby poprawnie zweryfikować kompilacje aplikacji.</span><span class="sxs-lookup"><span data-stu-id="5e643-121">Run [dotnet build](/dotnet/core/tools/dotnet-build) to verify your app builds correctly.</span></span> <span data-ttu-id="5e643-122">Przy pierwszym uruchomieniu proces kompilacji przywraca zależności npm, co może potrwać kilka minut.</span><span class="sxs-lookup"><span data-stu-id="5e643-122">On the first run, the build process restores npm dependencies, which can take several minutes.</span></span> <span data-ttu-id="5e643-123">Kolejne kompilacje są znacznie szybsze.</span><span class="sxs-lookup"><span data-stu-id="5e643-123">Subsequent builds are much faster.</span></span>

<span data-ttu-id="5e643-124">Uruchom [dotnet uruchom,](/dotnet/core/tools/dotnet-run) aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="5e643-124">Run [dotnet run](/dotnet/core/tools/dotnet-run) to start the app.</span></span>

---

<span data-ttu-id="5e643-125">Szablon projektu tworzy aplikację ASP.NET Core i aplikację React.</span><span class="sxs-lookup"><span data-stu-id="5e643-125">The project template creates an ASP.NET Core app and a React app.</span></span> <span data-ttu-id="5e643-126">Aplikacja ASP.NET Core jest przeznaczona do użycia w celu uzyskania dostępu do danych, autoryzacji i innych problemów po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="5e643-126">The ASP.NET Core app is intended to be used for data access, authorization, and other server-side concerns.</span></span> <span data-ttu-id="5e643-127">Aplikacja React, zamieszkała w podkatalogu *ClientApp,* jest przeznaczona do użycia dla wszystkich problemów z interfejsem użytkownika.</span><span class="sxs-lookup"><span data-stu-id="5e643-127">The React app, residing in the *ClientApp* subdirectory, is intended to be used for all UI concerns.</span></span>

## <a name="add-pages-images-styles-modules-etc"></a><span data-ttu-id="5e643-128">Dodaj strony, obrazy, style, moduły itp.</span><span class="sxs-lookup"><span data-stu-id="5e643-128">Add pages, images, styles, modules, etc.</span></span>

<span data-ttu-id="5e643-129">Katalog *ClientApp* jest standardową aplikacją CRA React.</span><span class="sxs-lookup"><span data-stu-id="5e643-129">The *ClientApp* directory is a standard CRA React app.</span></span> <span data-ttu-id="5e643-130">Więcej informacji można znaleźć w oficjalnej [dokumentacji CRA.](https://create-react-app.dev/docs/getting-started/)</span><span class="sxs-lookup"><span data-stu-id="5e643-130">See the official [CRA documentation](https://create-react-app.dev/docs/getting-started/) for more information.</span></span>

<span data-ttu-id="5e643-131">Istnieją niewielkie różnice między aplikacją React utworzoną przez ten szablon a aplikacją stworzoną przez samą cra; jednak możliwości aplikacji pozostają niezmienione.</span><span class="sxs-lookup"><span data-stu-id="5e643-131">There are slight differences between the React app created by this template and the one created by CRA itself; however, the app's capabilities are unchanged.</span></span> <span data-ttu-id="5e643-132">Aplikacja utworzona przez szablon zawiera układ oparty na [bootstrap](https://getbootstrap.com/)i przykład podstawowego routingu.</span><span class="sxs-lookup"><span data-stu-id="5e643-132">The app created by the template contains a [Bootstrap](https://getbootstrap.com/)-based layout and a basic routing example.</span></span>

## <a name="install-npm-packages"></a><span data-ttu-id="5e643-133">Instalowanie pakietów npm</span><span class="sxs-lookup"><span data-stu-id="5e643-133">Install npm packages</span></span>

<span data-ttu-id="5e643-134">Aby zainstalować pakiety npm innych firm, użyj wiersza polecenia w podkatalogu *ClientApp.*</span><span class="sxs-lookup"><span data-stu-id="5e643-134">To install third-party npm packages, use a command prompt in the *ClientApp* subdirectory.</span></span> <span data-ttu-id="5e643-135">Przykład:</span><span class="sxs-lookup"><span data-stu-id="5e643-135">For example:</span></span>

```console
cd ClientApp
npm install --save <package_name>
```

## <a name="publish-and-deploy"></a><span data-ttu-id="5e643-136">Publikowanie i wdrażanie</span><span class="sxs-lookup"><span data-stu-id="5e643-136">Publish and deploy</span></span>

<span data-ttu-id="5e643-137">W rozwoju aplikacja działa w trybie zoptymalizowanym pod kątem wygody dewelopera.</span><span class="sxs-lookup"><span data-stu-id="5e643-137">In development, the app runs in a mode optimized for developer convenience.</span></span> <span data-ttu-id="5e643-138">Na przykład pakiety JavaScript zawierają mapy źródłowe (dzięki czemu podczas debugowania można zobaczyć oryginalny kod źródłowy).</span><span class="sxs-lookup"><span data-stu-id="5e643-138">For example, JavaScript bundles include source maps (so that when debugging, you can see your original source code).</span></span> <span data-ttu-id="5e643-139">Aplikacja obserwuje zmiany plików JavaScript, HTML i CSS na dysku i automatycznie ponownie kompiluje i ładuje się, gdy widzi, że te pliki się zmieniają.</span><span class="sxs-lookup"><span data-stu-id="5e643-139">The app watches JavaScript, HTML, and CSS file changes on disk and automatically recompiles and reloads when it sees those files change.</span></span>

<span data-ttu-id="5e643-140">W produkcji wyświetlaj wersję aplikacji zoptymalizowaną pod kątem wydajności.</span><span class="sxs-lookup"><span data-stu-id="5e643-140">In production, serve a version of your app that's optimized for performance.</span></span> <span data-ttu-id="5e643-141">Jest to skonfigurowane tak, aby odbywało się automatycznie.</span><span class="sxs-lookup"><span data-stu-id="5e643-141">This is configured to happen automatically.</span></span> <span data-ttu-id="5e643-142">Podczas publikowania konfiguracji kompilacji emituje wbudowane, transpiled kompilacji kodu po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="5e643-142">When you publish, the build configuration emits a minified, transpiled build of your client-side code.</span></span> <span data-ttu-id="5e643-143">W przeciwieństwie do kompilacji deweloperów kompilacja produkcyjna nie wymaga instalowania node.js na serwerze.</span><span class="sxs-lookup"><span data-stu-id="5e643-143">Unlike the development build, the production build doesn't require Node.js to be installed on the server.</span></span>

<span data-ttu-id="5e643-144">Można użyć standardowych [ASP.NET metody hostingu i wdrażania Core](xref:host-and-deploy/index).</span><span class="sxs-lookup"><span data-stu-id="5e643-144">You can use standard [ASP.NET Core hosting and deployment methods](xref:host-and-deploy/index).</span></span>

## <a name="run-the-cra-server-independently"></a><span data-ttu-id="5e643-145">Uruchamianie serwera CRA niezależnie</span><span class="sxs-lookup"><span data-stu-id="5e643-145">Run the CRA server independently</span></span>

<span data-ttu-id="5e643-146">Projekt jest skonfigurowany do uruchamiania własnego wystąpienia serwera dewelopera CRA w tle, gdy aplikacja ASP.NET Core zostanie uruchomiony w trybie programowania.</span><span class="sxs-lookup"><span data-stu-id="5e643-146">The project is configured to start its own instance of the CRA development server in the background when the ASP.NET Core app starts in development mode.</span></span> <span data-ttu-id="5e643-147">Jest to wygodne, ponieważ oznacza to, że nie trzeba ręcznie uruchamiać oddzielnego serwera.</span><span class="sxs-lookup"><span data-stu-id="5e643-147">This is convenient because it means you don't have to run a separate server manually.</span></span>

<span data-ttu-id="5e643-148">Istnieje wada tej domyślnej konfiguracji.</span><span class="sxs-lookup"><span data-stu-id="5e643-148">There's a drawback to this default setup.</span></span> <span data-ttu-id="5e643-149">Za każdym razem, gdy modyfikujesz kod C#, a aplikacja ASP.NET Core musi zostać ponownie uruchomiona, serwer CRA zostanie ponownie uruchomiony.</span><span class="sxs-lookup"><span data-stu-id="5e643-149">Each time you modify your C# code and your ASP.NET Core app needs to restart, the CRA server restarts.</span></span> <span data-ttu-id="5e643-150">Do uruchomienia kopii zapasowej wymagane jest kilka sekund.</span><span class="sxs-lookup"><span data-stu-id="5e643-150">A few seconds are required to start back up.</span></span> <span data-ttu-id="5e643-151">Jeśli wprowadzasz częste zmiany kodu języka C# i nie chcesz czekać na ponowne uruchomienie serwera CRA, uruchom serwer USŁUGI CRA zewnętrznie, niezależnie od procesu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5e643-151">If you're making frequent C# code edits and don't want to wait for the CRA server to restart, run the CRA server externally, independently of the ASP.NET Core process.</span></span> <span data-ttu-id="5e643-152">W tym celu:</span><span class="sxs-lookup"><span data-stu-id="5e643-152">To do so:</span></span>

1. <span data-ttu-id="5e643-153">Dodaj plik *.env* do podkatalogu *ClientApp* z następującym ustawieniem:</span><span class="sxs-lookup"><span data-stu-id="5e643-153">Add a *.env* file to the *ClientApp* subdirectory with the following setting:</span></span>

    ```
    BROWSER=none
    ```

    <span data-ttu-id="5e643-154">Zapobiegnie to otwarciu przeglądarki internetowej podczas uruchamiania serwera CRA na zewnątrz.</span><span class="sxs-lookup"><span data-stu-id="5e643-154">This will prevent your web browser from opening when starting the CRA server externally.</span></span>

2. <span data-ttu-id="5e643-155">W wierszu polecenia przełącz się do podkatalogu *ClientApp* i uruchom serwer deweloperski USŁUGI CRA:</span><span class="sxs-lookup"><span data-stu-id="5e643-155">In a command prompt, switch to the *ClientApp* subdirectory, and launch the CRA development server:</span></span>

    ```console
    cd ClientApp
    npm start
    ```

3. <span data-ttu-id="5e643-156">Zmodyfikuj aplikację ASP.NET Core, aby użyć zewnętrznego wystąpienia serwera CRA zamiast uruchamiać własne.</span><span class="sxs-lookup"><span data-stu-id="5e643-156">Modify your ASP.NET Core app to use the external CRA server instance instead of launching one of its own.</span></span> <span data-ttu-id="5e643-157">W klasie *uruchamiania* `spa.UseReactDevelopmentServer` zastąp wywołanie następującymi:</span><span class="sxs-lookup"><span data-stu-id="5e643-157">In your *Startup* class, replace the `spa.UseReactDevelopmentServer` invocation with the following:</span></span>

    ```csharp
    spa.UseProxyToSpaDevelopmentServer("http://localhost:3000");
    ```

<span data-ttu-id="5e643-158">Po uruchomieniu aplikacji ASP.NET Core nie uruchomi ona serwera CRA.</span><span class="sxs-lookup"><span data-stu-id="5e643-158">When you start your ASP.NET Core app, it won't launch a CRA server.</span></span> <span data-ttu-id="5e643-159">Zamiast tego używane jest wystąpienie uruchomione ręcznie.</span><span class="sxs-lookup"><span data-stu-id="5e643-159">The instance you started manually is used instead.</span></span> <span data-ttu-id="5e643-160">Dzięki temu można go uruchomić i uruchomić szybciej.</span><span class="sxs-lookup"><span data-stu-id="5e643-160">This enables it to start and restart faster.</span></span> <span data-ttu-id="5e643-161">Nie czeka już na odbudowanie aplikacji React za każdym razem.</span><span class="sxs-lookup"><span data-stu-id="5e643-161">It's no longer waiting for your React app to rebuild each time.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="5e643-162">"Renderowanie po stronie serwera" nie jest obsługiwaną funkcją tego szablonu.</span><span class="sxs-lookup"><span data-stu-id="5e643-162">"Server-side rendering" is not a supported feature of this template.</span></span> <span data-ttu-id="5e643-163">Naszym celem w tym szablonie jest spełnienie parytetu z "create-react-app".</span><span class="sxs-lookup"><span data-stu-id="5e643-163">Our goal with this template is to meet parity with "create-react-app".</span></span> <span data-ttu-id="5e643-164">W związku z tym scenariusze i funkcje, które nie zostały uwzględnione w projekcie "create-react-app" (takim jak SSR) nie są obsługiwane i pozostają jako ćwiczenie dla użytkownika.</span><span class="sxs-lookup"><span data-stu-id="5e643-164">As such, scenarios and features not included in a "create-react-app" project (such as SSR) are not supported and are left as an exercise for the user.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5e643-165">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="5e643-165">Additional resources</span></span>

* <xref:security/authentication/identity/spa>
