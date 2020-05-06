---
title: Używanie ASP.NET Core SignalR z językami TypeScript i WebPack
author: ssougnez
description: W tym samouczku skonfigurujesz pakiet WebPack do tworzenia pakietów i kompilowania ASP.NET Core SignalR aplikacji sieci Web, której klient został zapisany w języku TypeScript.
ms.author: bradyg
ms.custom: mvc
ms.date: 02/10/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/signalr-typescript-webpack
ms.openlocfilehash: 67a6217055db69fe540412f42411dd3a33bbbe73
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775508"
---
# <a name="use-aspnet-core-signalr-with-typescript-and-webpack"></a><span data-ttu-id="808d9-103">Korzystanie z ASP.NET Core sygnalizującego za pomocą języka TypeScript i pakietu WebPack</span><span class="sxs-lookup"><span data-stu-id="808d9-103">Use ASP.NET Core SignalR with TypeScript and Webpack</span></span>

<span data-ttu-id="808d9-104">Autorzy [Sébastien Sougnez](https://twitter.com/ssougnez) i [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="808d9-104">By [Sébastien Sougnez](https://twitter.com/ssougnez) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="808d9-105">[Pakiet WebPack](https://webpack.js.org/) umożliwia deweloperom tworzenie i kompilowanie zasobów po stronie klienta aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="808d9-105">[Webpack](https://webpack.js.org/) enables developers to bundle and build the client-side resources of a web app.</span></span> <span data-ttu-id="808d9-106">W tym samouczku pokazano, jak używać pakietu WebPack w aplikacji sieci Web sygnalizującej ASP.NET Core, której klient został zapisany w języku [TypeScript](https://www.typescriptlang.org/).</span><span class="sxs-lookup"><span data-stu-id="808d9-106">This tutorial demonstrates using Webpack in an ASP.NET Core SignalR web app whose client is written in [TypeScript](https://www.typescriptlang.org/).</span></span>

<span data-ttu-id="808d9-107">Ten samouczek zawiera informacje na temat wykonywania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="808d9-107">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="808d9-108">Tworzenie szkieletu aplikacji dla programu Start ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="808d9-108">Scaffold a starter ASP.NET Core SignalR app</span></span>
> * <span data-ttu-id="808d9-109">Konfigurowanie klienta TypeScript sygnalizującego</span><span class="sxs-lookup"><span data-stu-id="808d9-109">Configure the SignalR TypeScript client</span></span>
> * <span data-ttu-id="808d9-110">Konfigurowanie potoku kompilacji przy użyciu pakietu WebPack</span><span class="sxs-lookup"><span data-stu-id="808d9-110">Configure a build pipeline using Webpack</span></span>
> * <span data-ttu-id="808d9-111">Konfigurowanie serwera sygnalizującego</span><span class="sxs-lookup"><span data-stu-id="808d9-111">Configure the SignalR server</span></span>
> * <span data-ttu-id="808d9-112">Włącz komunikację między klientem a serwerem</span><span class="sxs-lookup"><span data-stu-id="808d9-112">Enable communication between client and server</span></span>

<span data-ttu-id="808d9-113">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="808d9-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="808d9-114">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="808d9-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="808d9-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="808d9-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="808d9-116">[Program Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) z **ASP.NET i programowaniem aplikacji sieci Web**</span><span class="sxs-lookup"><span data-stu-id="808d9-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="808d9-117">Zestaw .NET Core SDK 3.0 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="808d9-117">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="808d9-118">[Node. js](https://nodejs.org/) z [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="808d9-118">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="808d9-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="808d9-119">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="808d9-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="808d9-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="808d9-121">Zestaw .NET Core SDK 3.0 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="808d9-121">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="808d9-122">C# dla Visual Studio Code w wersji 1.17.1 lub nowszej</span><span class="sxs-lookup"><span data-stu-id="808d9-122">C# for Visual Studio Code version 1.17.1 or later</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* <span data-ttu-id="808d9-123">[Node. js](https://nodejs.org/) z [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="808d9-123">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

---

## <a name="create-the-aspnet-core-web-app"></a><span data-ttu-id="808d9-124">Tworzenie aplikacji sieci Web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="808d9-124">Create the ASP.NET Core web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="808d9-125">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="808d9-125">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="808d9-126">Skonfiguruj program Visual Studio, aby szukać npm w zmiennej środowiskowej *Path* .</span><span class="sxs-lookup"><span data-stu-id="808d9-126">Configure Visual Studio to look for npm in the *PATH* environment variable.</span></span> <span data-ttu-id="808d9-127">Domyślnie program Visual Studio używa wersji npm znajdującej się w katalogu instalacyjnym.</span><span class="sxs-lookup"><span data-stu-id="808d9-127">By default, Visual Studio uses the version of npm found in its installation directory.</span></span> <span data-ttu-id="808d9-128">Wykonaj te instrukcje w programie Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="808d9-128">Follow these instructions in Visual Studio:</span></span>

1. <span data-ttu-id="808d9-129">Uruchom program Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="808d9-129">Launch Visual Studio.</span></span> <span data-ttu-id="808d9-130">W oknie uruchamiania wybierz pozycję **Kontynuuj bez kodu**.</span><span class="sxs-lookup"><span data-stu-id="808d9-130">At the start window, select **Continue without code**.</span></span>
1. <span data-ttu-id="808d9-131">Przejdź do **Tools** > **opcji Narzędzia Opcje** > **projekty i rozwiązania** > **Sieć Web zarządzanie pakietami** > **zewnętrznych narzędzi sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="808d9-131">Navigate to **Tools** > **Options** > **Projects and Solutions** > **Web Package Management** > **External Web Tools**.</span></span>
1. <span data-ttu-id="808d9-132">Wybierz z listy wpis *$ (Path)* .</span><span class="sxs-lookup"><span data-stu-id="808d9-132">Select the *$(PATH)* entry from the list.</span></span> <span data-ttu-id="808d9-133">Kliknij strzałkę w górę, aby przenieść wpis do drugiej pozycji na liście, a następnie wybierz **przycisk OK**.</span><span class="sxs-lookup"><span data-stu-id="808d9-133">Click the up arrow to move the entry to the second position in the list, and select **OK**.</span></span>

    ![Konfiguracja programu Visual Studio](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

<span data-ttu-id="808d9-135">Konfiguracja programu Visual Studio została ukończona.</span><span class="sxs-lookup"><span data-stu-id="808d9-135">Visual Studio configuration is complete.</span></span>

1. <span data-ttu-id="808d9-136">Użyj opcji menu **plik** > **Nowy** > **projekt** , a następnie wybierz szablon **aplikacja sieci Web ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="808d9-136">Use the **File** > **New** > **Project** menu option and choose the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="808d9-137">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="808d9-137">Select **Next**.</span></span>
1. <span data-ttu-id="808d9-138">Nazwij projekt *SignalRWebPack*, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="808d9-138">Name the project *SignalRWebPack*, and select **Create**.</span></span>
1. <span data-ttu-id="808d9-139">Wybierz pozycję *.NET Core* z listy rozwijanej platforma docelowa, a następnie wybierz pozycję *ASP.NET Core 3,1* z listy rozwijanej selektora struktury.</span><span class="sxs-lookup"><span data-stu-id="808d9-139">Select *.NET Core* from the target framework drop-down, and select *ASP.NET Core 3.1* from the framework selector drop-down.</span></span> <span data-ttu-id="808d9-140">Wybierz **pusty** szablon i wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="808d9-140">Select the **Empty** template, and select **Create**.</span></span>

<span data-ttu-id="808d9-141">Dodaj `Microsoft.TypeScript.MSBuild` pakiet do projektu:</span><span class="sxs-lookup"><span data-stu-id="808d9-141">Add the `Microsoft.TypeScript.MSBuild` package to the project:</span></span>

1. <span data-ttu-id="808d9-142">W **Eksplorator rozwiązań** (prawego okienka) kliknij prawym przyciskiem myszy węzeł projektu i wybierz polecenie **Zarządzaj pakietami NuGet**.</span><span class="sxs-lookup"><span data-stu-id="808d9-142">In **Solution Explorer** (right pane), right-click the project node and select **Manage NuGet Packages**.</span></span> <span data-ttu-id="808d9-143">Na karcie **Przeglądaj** Wyszukaj `Microsoft.TypeScript.MSBuild`pozycję, a następnie kliknij pozycję **Zainstaluj** po prawej stronie, aby zainstalować pakiet.</span><span class="sxs-lookup"><span data-stu-id="808d9-143">In the **Browse** tab, search for `Microsoft.TypeScript.MSBuild`, and then click **Install** on the right to install the package.</span></span>

<span data-ttu-id="808d9-144">Program Visual Studio dodaje pakiet NuGet w węźle **zależności** w **Eksplorator rozwiązań**, włączając kompilację języka TypeScript w projekcie.</span><span class="sxs-lookup"><span data-stu-id="808d9-144">Visual Studio adds the NuGet package under the **Dependencies** node in **Solution Explorer**, enabling TypeScript compilation in the project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="808d9-145">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="808d9-145">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="808d9-146">Uruchom następujące polecenie w **zintegrowanym terminalu**:</span><span class="sxs-lookup"><span data-stu-id="808d9-146">Run the following command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet new web -o SignalRWebPack
code -r SignalRWebPack
```

* <span data-ttu-id="808d9-147">`dotnet new` Polecenie tworzy pustą aplikację sieci Web ASP.NET Core w katalogu *SignalRWebPack* .</span><span class="sxs-lookup"><span data-stu-id="808d9-147">The `dotnet new` command creates an empty ASP.NET Core web app in a *SignalRWebPack* directory.</span></span>
* <span data-ttu-id="808d9-148">`code` Polecenie otwiera folder *SignalRWebPack* w bieżącym wystąpieniu Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="808d9-148">The `code` command opens the *SignalRWebPack* folder in the current instance of Visual Studio Code.</span></span>

<span data-ttu-id="808d9-149">Uruchom następujące polecenie interfejs wiersza polecenia platformy .NET Core w **zintegrowanym terminalu**:</span><span class="sxs-lookup"><span data-stu-id="808d9-149">Run the following .NET Core CLI command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet add package Microsoft.TypeScript.MSBuild
```

<span data-ttu-id="808d9-150">Poprzednie polecenie dodaje pakiet [Microsoft. TypeScript. MSBuild](https://www.nuget.org/packages/Microsoft.TypeScript.MSBuild/) , włączając kompilację TypeScript w projekcie.</span><span class="sxs-lookup"><span data-stu-id="808d9-150">The preceding command adds the [Microsoft.TypeScript.MSBuild](https://www.nuget.org/packages/Microsoft.TypeScript.MSBuild/) package, enabling TypeScript compilation in the project.</span></span>

---

## <a name="configure-webpack-and-typescript"></a><span data-ttu-id="808d9-151">Konfigurowanie pakietu WebPack i języka TypeScript</span><span class="sxs-lookup"><span data-stu-id="808d9-151">Configure Webpack and TypeScript</span></span>

<span data-ttu-id="808d9-152">Poniższe kroki umożliwiają skonfigurowanie konwersji języka TypeScript na JavaScript i zgrupowanie zasobów po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="808d9-152">The following steps configure the conversion of TypeScript to JavaScript and the bundling of client-side resources.</span></span>

1. <span data-ttu-id="808d9-153">Uruchom następujące polecenie w katalogu głównym projektu, aby utworzyć plik *Package. JSON* :</span><span class="sxs-lookup"><span data-stu-id="808d9-153">Run the following command in the project root to create a *package.json* file:</span></span>

    ```console
    npm init -y
    ```

1. <span data-ttu-id="808d9-154">Dodaj wyróżnioną właściwość do pliku *Package. JSON* i Zapisz zmiany w pliku:</span><span class="sxs-lookup"><span data-stu-id="808d9-154">Add the highlighted property to the *package.json* file and save the file changes:</span></span>

    [!code-json[package.json](signalr-typescript-webpack/sample/3.x/snippets/package1.json?highlight=4)]

    <span data-ttu-id="808d9-155">Ustawienie `private` właściwości w celu `true` uniemożliwienia ostrzeżeń instalacji pakietu w następnym kroku.</span><span class="sxs-lookup"><span data-stu-id="808d9-155">Setting the `private` property to `true` prevents package installation warnings in the next step.</span></span>

1. <span data-ttu-id="808d9-156">Zainstaluj wymagane pakiety npm.</span><span class="sxs-lookup"><span data-stu-id="808d9-156">Install the required npm packages.</span></span> <span data-ttu-id="808d9-157">Uruchom następujące polecenie z poziomu głównego projektu:</span><span class="sxs-lookup"><span data-stu-id="808d9-157">Run the following command from the project root:</span></span>

    ```console
    npm i -D -E clean-webpack-plugin@3.0.0 css-loader@3.4.2 html-webpack-plugin@3.2.0 mini-css-extract-plugin@0.9.0 ts-loader@6.2.1 typescript@3.7.5 webpack@4.41.5 webpack-cli@3.3.10
    ```

    <span data-ttu-id="808d9-158">Niektóre szczegóły polecenia do uwagi:</span><span class="sxs-lookup"><span data-stu-id="808d9-158">Some command details to note:</span></span>

    * <span data-ttu-id="808d9-159">Numer wersji następuje po `@` znaku dla każdej nazwy pakietu.</span><span class="sxs-lookup"><span data-stu-id="808d9-159">A version number follows the `@` sign for each package name.</span></span> <span data-ttu-id="808d9-160">npm instaluje te określone wersje pakietu.</span><span class="sxs-lookup"><span data-stu-id="808d9-160">npm installs those specific package versions.</span></span>
    * <span data-ttu-id="808d9-161">`-E` Opcja wyłącza domyślne zachowanie npm podczas pisania operatorów zakresu [wersji semantycznej](https://semver.org/) w pliku *Package. JSON*.</span><span class="sxs-lookup"><span data-stu-id="808d9-161">The `-E` option disables npm's default behavior of writing [semantic versioning](https://semver.org/) range operators to *package.json*.</span></span> <span data-ttu-id="808d9-162">Na przykład, `"webpack": "4.41.5"` jest używany zamiast `"webpack": "^4.41.5"`.</span><span class="sxs-lookup"><span data-stu-id="808d9-162">For example, `"webpack": "4.41.5"` is used instead of `"webpack": "^4.41.5"`.</span></span> <span data-ttu-id="808d9-163">Ta opcja Zapobiega niezamierzonym uaktualnianiu do nowszych wersji pakietu.</span><span class="sxs-lookup"><span data-stu-id="808d9-163">This option prevents unintended upgrades to newer package versions.</span></span>

    <span data-ttu-id="808d9-164">Więcej szczegółów można znaleźć w dokumentacji [npm-Install](https://docs.npmjs.com/cli/install) .</span><span class="sxs-lookup"><span data-stu-id="808d9-164">See the [npm-install](https://docs.npmjs.com/cli/install) docs for more detail.</span></span>

1. <span data-ttu-id="808d9-165">Zastąp `scripts` właściwość pliku *Package. JSON* następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="808d9-165">Replace the `scripts` property of the *package.json* file with the following code:</span></span>

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    <span data-ttu-id="808d9-166">Niektóre objaśnienia dotyczące skryptów:</span><span class="sxs-lookup"><span data-stu-id="808d9-166">Some explanation of the scripts:</span></span>

    * <span data-ttu-id="808d9-167">`build`: Łączy zasoby po stronie klienta w trybie tworzenia i czujki pod kątem zmian w pliku.</span><span class="sxs-lookup"><span data-stu-id="808d9-167">`build`: Bundles the client-side resources in development mode and watches for file changes.</span></span> <span data-ttu-id="808d9-168">Obserwator plików powoduje, że pakiet jest generowany ponownie za każdym razem, gdy plik projektu jest zmieniany.</span><span class="sxs-lookup"><span data-stu-id="808d9-168">The file watcher causes the bundle to regenerate each time a project file changes.</span></span> <span data-ttu-id="808d9-169">`mode` Opcja wyłącza optymalizacje produkcyjne, takie jak wstrząsanie i minifikacjaowanie drzewa.</span><span class="sxs-lookup"><span data-stu-id="808d9-169">The `mode` option disables production optimizations, such as tree shaking and minification.</span></span> <span data-ttu-id="808d9-170">Używać `build` tylko w programowaniu.</span><span class="sxs-lookup"><span data-stu-id="808d9-170">Only use `build` in development.</span></span>
    * <span data-ttu-id="808d9-171">`release`: Pakietuje zasoby po stronie klienta w trybie produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="808d9-171">`release`: Bundles the client-side resources in production mode.</span></span>
    * <span data-ttu-id="808d9-172">`publish`: Uruchamia `release` skrypt służący do łączenia zasobów po stronie klienta w trybie produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="808d9-172">`publish`: Runs the `release` script to bundle the client-side resources in production mode.</span></span> <span data-ttu-id="808d9-173">Wywołuje polecenie [publikowania](/dotnet/core/tools/dotnet-publish) interfejs wiersza polecenia platformy .NET Core, aby opublikować aplikację.</span><span class="sxs-lookup"><span data-stu-id="808d9-173">It calls the .NET Core CLI's [publish](/dotnet/core/tools/dotnet-publish) command to publish the app.</span></span>

1. <span data-ttu-id="808d9-174">Utwórz plik o nazwie *WebPack. config. js*w katalogu głównym projektu o następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="808d9-174">Create a file named *webpack.config.js*, in the project root, with the following code:</span></span>

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/3.x/webpack.config.js)]

    <span data-ttu-id="808d9-175">Poprzedni plik konfiguruje kompilację pakietu WebPack.</span><span class="sxs-lookup"><span data-stu-id="808d9-175">The preceding file configures the Webpack compilation.</span></span> <span data-ttu-id="808d9-176">Niektóre szczegóły konfiguracji do uwagi:</span><span class="sxs-lookup"><span data-stu-id="808d9-176">Some configuration details to note:</span></span>

    * <span data-ttu-id="808d9-177">`output` Właściwość zastępuje domyślną wartość *rozkłu*.</span><span class="sxs-lookup"><span data-stu-id="808d9-177">The `output` property overrides the default value of *dist*.</span></span> <span data-ttu-id="808d9-178">Pakiet jest emitowany w katalogu *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="808d9-178">The bundle is instead emitted in the *wwwroot* directory.</span></span>
    * <span data-ttu-id="808d9-179">`resolve.extensions` Tablica zawiera *. js* do zaimportowania klienta sygnału JavaScript.</span><span class="sxs-lookup"><span data-stu-id="808d9-179">The `resolve.extensions` array includes *.js* to import the SignalR client JavaScript.</span></span>

1. <span data-ttu-id="808d9-180">Utwórz nowy katalog *src* w katalogu głównym projektu do przechowywania zasobów po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="808d9-180">Create a new *src* directory in the project root to store the project's client-side assets.</span></span>

1. <span data-ttu-id="808d9-181">Utwórz *src/index.html* z następującą adiustacją.</span><span class="sxs-lookup"><span data-stu-id="808d9-181">Create *src/index.html* with the following markup.</span></span>

    [!code-html[index.html](signalr-typescript-webpack/sample/3.x/src/index.html)]

    <span data-ttu-id="808d9-182">Powyższy kod HTML definiuje standardowe znaczniki strony głównej.</span><span class="sxs-lookup"><span data-stu-id="808d9-182">The preceding HTML defines the homepage's boilerplate markup.</span></span>

1. <span data-ttu-id="808d9-183">Utwórz nowy katalog *src/CSS* .</span><span class="sxs-lookup"><span data-stu-id="808d9-183">Create a new *src/css* directory.</span></span> <span data-ttu-id="808d9-184">Celem jest przechowywanie plików *CSS* projektu.</span><span class="sxs-lookup"><span data-stu-id="808d9-184">Its purpose is to store the project's *.css* files.</span></span>

1. <span data-ttu-id="808d9-185">Utwórz *src/CSS/Main. css* z następującym arkuszem CSS:</span><span class="sxs-lookup"><span data-stu-id="808d9-185">Create *src/css/main.css* with the following CSS:</span></span>

    [!code-css[main.css](signalr-typescript-webpack/sample/3.x/src/css/main.css)]

    <span data-ttu-id="808d9-186">Poprzedni plik *Main. css* jest stylem aplikacji.</span><span class="sxs-lookup"><span data-stu-id="808d9-186">The preceding *main.css* file styles the app.</span></span>

1. <span data-ttu-id="808d9-187">Utwórz plik *src/tsconfig. JSON* z następującym kodem JSON:</span><span class="sxs-lookup"><span data-stu-id="808d9-187">Create *src/tsconfig.json* with the following JSON:</span></span>

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/3.x/src/tsconfig.json)]

    <span data-ttu-id="808d9-188">Poprzedni kod konfiguruje kompilator języka TypeScript w celu utworzenia kodu JavaScript zgodnego z [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5.</span><span class="sxs-lookup"><span data-stu-id="808d9-188">The preceding code configures the TypeScript compiler to produce [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5-compatible JavaScript.</span></span>

1. <span data-ttu-id="808d9-189">Utwórz *src/index. TS* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="808d9-189">Create *src/index.ts* with the following code:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    <span data-ttu-id="808d9-190">Poprzedni kod TypeScript pobiera odwołania do elementów DOM i dołącza dwa programy obsługi zdarzeń:</span><span class="sxs-lookup"><span data-stu-id="808d9-190">The preceding TypeScript retrieves references to DOM elements and attaches two event handlers:</span></span>

    * <span data-ttu-id="808d9-191">`keyup`: To zdarzenie jest wyzwalane, gdy użytkownik wpisze `tbMessage`wartość w polu tekstowym.</span><span class="sxs-lookup"><span data-stu-id="808d9-191">`keyup`: This event fires when the user types in the `tbMessage`textbox.</span></span> <span data-ttu-id="808d9-192">`send` Funkcja jest wywoływana, gdy użytkownik naciśnie klawisz **Enter** .</span><span class="sxs-lookup"><span data-stu-id="808d9-192">The `send` function is called when the user presses the **Enter** key.</span></span>
    * <span data-ttu-id="808d9-193">`click`: To zdarzenie jest wyzwalane, gdy użytkownik kliknie przycisk **Wyślij** .</span><span class="sxs-lookup"><span data-stu-id="808d9-193">`click`: This event fires when the user clicks the **Send** button.</span></span> <span data-ttu-id="808d9-194">Wywołanie funkcji `send`.</span><span class="sxs-lookup"><span data-stu-id="808d9-194">The `send` function is called.</span></span>

## <a name="configure-the-app"></a><span data-ttu-id="808d9-195">Konfigurowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="808d9-195">Configure the app</span></span>

1. <span data-ttu-id="808d9-196">W `Startup.Configure`programie Dodaj wywołania do [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) i [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="808d9-196">In `Startup.Configure`, add calls to [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span>

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseStaticDefaultFiles&highlight=9-10)]

   <span data-ttu-id="808d9-197">Poprzedni kod pozwala serwerowi zlokalizować i obsłużyć plik *index. html* .</span><span class="sxs-lookup"><span data-stu-id="808d9-197">The preceding code allows the server to locate and serve the *index.html* file.</span></span>  <span data-ttu-id="808d9-198">Plik jest obsługiwany niezależnie od tego, czy użytkownik wprowadza pełny adres URL, czy też główny adres URL aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="808d9-198">The file is served whether the user enters its full URL or the root URL of the web app.</span></span>

1. <span data-ttu-id="808d9-199">Na końcu `Startup.Configure`należy zmapować */Hub* trasę do `ChatHub` centrum.</span><span class="sxs-lookup"><span data-stu-id="808d9-199">At the end of `Startup.Configure`, map a */hub* route to the `ChatHub` hub.</span></span> <span data-ttu-id="808d9-200">Zastąp kod, który wyświetla *Hello World!*</span><span class="sxs-lookup"><span data-stu-id="808d9-200">Replace the code that displays *Hello World!*</span></span> <span data-ttu-id="808d9-201">z następującym wierszem:</span><span class="sxs-lookup"><span data-stu-id="808d9-201">with the following line:</span></span> 

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseSignalR&highlight=3)]

1. <span data-ttu-id="808d9-202">W `Startup.ConfigureServices`, wywołaj metodę [addsignaler](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_).</span><span class="sxs-lookup"><span data-stu-id="808d9-202">In `Startup.ConfigureServices`, call [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_).</span></span>

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_AddSignalR)]

1. <span data-ttu-id="808d9-203">Utwórz nowy katalog o nazwie *Hubs* w katalogu głównym *SignalRWebPack/* do przechowywania centrum sygnałów.</span><span class="sxs-lookup"><span data-stu-id="808d9-203">Create a new directory named *Hubs* in the project root *SignalRWebPack/* to store the SignalR hub.</span></span>

1. <span data-ttu-id="808d9-204">Utwórz centra centrów */ChatHub. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="808d9-204">Create hub *Hubs/ChatHub.cs* with the following code:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. <span data-ttu-id="808d9-205">Dodaj następującą `using` instrukcję w górnej części pliku *Startup.cs* , aby rozwiązać `ChatHub` odwołanie:</span><span class="sxs-lookup"><span data-stu-id="808d9-205">Add the following `using` statement at the top of the *Startup.cs* file to resolve the `ChatHub` reference:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a><span data-ttu-id="808d9-206">Włączanie komunikacji klienta i serwera</span><span class="sxs-lookup"><span data-stu-id="808d9-206">Enable client and server communication</span></span>

<span data-ttu-id="808d9-207">W aplikacji jest obecnie wyświetlany podstawowy formularz służący do wysyłania komunikatów, ale nie jest on jeszcze funkcjonalny.</span><span class="sxs-lookup"><span data-stu-id="808d9-207">The app currently displays a basic form to send messages, but is not yet functional.</span></span> <span data-ttu-id="808d9-208">Serwer nasłuchuje określonej trasy, ale nic nie robi z wysłanymi komunikatami.</span><span class="sxs-lookup"><span data-stu-id="808d9-208">The server is listening to a specific route but does nothing with sent messages.</span></span>

1. <span data-ttu-id="808d9-209">Uruchom następujące polecenie w katalogu głównym projektu:</span><span class="sxs-lookup"><span data-stu-id="808d9-209">Run the following command at the project root:</span></span>

    ```console
    npm i @microsoft/signalr @types/node
    ```

    <span data-ttu-id="808d9-210">Poprzednie polecenie instaluje:</span><span class="sxs-lookup"><span data-stu-id="808d9-210">The preceding command installs:</span></span>

     * <span data-ttu-id="808d9-211">[Klient języka TypeScript sygnalizujący](https://www.npmjs.com/package/@microsoft/signalr), który umożliwia klientowi wysyłanie komunikatów do serwera.</span><span class="sxs-lookup"><span data-stu-id="808d9-211">The [SignalR TypeScript client](https://www.npmjs.com/package/@microsoft/signalr), which allows the client to send messages to the server.</span></span>
     * <span data-ttu-id="808d9-212">Definicje typów TypeScript dla środowiska Node. js, które umożliwiają sprawdzanie w czasie kompilacji typów Node. js.</span><span class="sxs-lookup"><span data-stu-id="808d9-212">The TypeScript type definitions for Node.js, which enables compile-time checking of Node.js types.</span></span>

1. <span data-ttu-id="808d9-213">Dodaj wyróżniony kod do pliku *src/index. TS* :</span><span class="sxs-lookup"><span data-stu-id="808d9-213">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    <span data-ttu-id="808d9-214">Poprzedni kod obsługuje otrzymywanie komunikatów z serwera.</span><span class="sxs-lookup"><span data-stu-id="808d9-214">The preceding code supports receiving messages from the server.</span></span> <span data-ttu-id="808d9-215">`HubConnectionBuilder` Klasa tworzy nowy Konstruktor służący do konfigurowania połączenia z serwerem.</span><span class="sxs-lookup"><span data-stu-id="808d9-215">The `HubConnectionBuilder` class creates a new builder for configuring the server connection.</span></span> <span data-ttu-id="808d9-216">`withUrl` Funkcja KONFIGURUJE adres URL centrum.</span><span class="sxs-lookup"><span data-stu-id="808d9-216">The `withUrl` function configures the hub URL.</span></span>

    <span data-ttu-id="808d9-217">Program sygnalizujący umożliwia wymianę komunikatów między klientem a serwerem.</span><span class="sxs-lookup"><span data-stu-id="808d9-217">SignalR enables the exchange of messages between a client and a server.</span></span> <span data-ttu-id="808d9-218">Każdy komunikat ma określoną nazwę.</span><span class="sxs-lookup"><span data-stu-id="808d9-218">Each message has a specific name.</span></span> <span data-ttu-id="808d9-219">Na przykład komunikaty o nazwie `messageReceived` mogą uruchamiać logikę odpowiedzialną za wyświetlanie nowej wiadomości w strefie messages.</span><span class="sxs-lookup"><span data-stu-id="808d9-219">For example, messages with the name `messageReceived` can run the logic responsible for displaying the new message in the messages zone.</span></span> <span data-ttu-id="808d9-220">Nasłuchiwanie określonego komunikatu można wykonać za pomocą `on` funkcji.</span><span class="sxs-lookup"><span data-stu-id="808d9-220">Listening to a specific message can be done via the `on` function.</span></span> <span data-ttu-id="808d9-221">Dowolna liczba nazw komunikatów może być nasłuchiwanie.</span><span class="sxs-lookup"><span data-stu-id="808d9-221">Any number of message names can be listened to.</span></span> <span data-ttu-id="808d9-222">Możliwe jest również przekazywanie parametrów do wiadomości, takich jak nazwa autora i zawartość otrzymanej wiadomości.</span><span class="sxs-lookup"><span data-stu-id="808d9-222">It's also possible to pass parameters to the message, such as the author's name and the content of the message received.</span></span> <span data-ttu-id="808d9-223">Po odebraniu komunikatu przez klienta zostanie utworzony nowy `div` element z nazwą autora i treścią komunikatu w jego `innerHTML` atrybucie.</span><span class="sxs-lookup"><span data-stu-id="808d9-223">Once the client receives a message, a new `div` element is created with the author's name and the message content in its `innerHTML` attribute.</span></span> <span data-ttu-id="808d9-224">Jest on dodawany do elementu głównego `div` wyświetlającego komunikaty.</span><span class="sxs-lookup"><span data-stu-id="808d9-224">It's added to the main `div` element displaying the messages.</span></span>

1. <span data-ttu-id="808d9-225">Teraz, gdy klient może odebrać komunikat, skonfiguruj go do wysyłania wiadomości.</span><span class="sxs-lookup"><span data-stu-id="808d9-225">Now that the client can receive a message, configure it to send messages.</span></span> <span data-ttu-id="808d9-226">Dodaj wyróżniony kod do pliku *src/index. TS* :</span><span class="sxs-lookup"><span data-stu-id="808d9-226">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/src/index.ts?highlight=34-35)]

    <span data-ttu-id="808d9-227">Wysyłanie komunikatu przez połączenie z usługą WebSockets wymaga wywołania `send` metody.</span><span class="sxs-lookup"><span data-stu-id="808d9-227">Sending a message through the WebSockets connection requires calling the `send` method.</span></span> <span data-ttu-id="808d9-228">Pierwszym parametrem metody jest nazwa komunikatu.</span><span class="sxs-lookup"><span data-stu-id="808d9-228">The method's first parameter is the message name.</span></span> <span data-ttu-id="808d9-229">Dane komunikatu są nieodpowiednie dla innych parametrów.</span><span class="sxs-lookup"><span data-stu-id="808d9-229">The message data inhabits the other parameters.</span></span> <span data-ttu-id="808d9-230">W tym przykładzie komunikat identyfikowany jako `newMessage` jest wysyłany do serwera.</span><span class="sxs-lookup"><span data-stu-id="808d9-230">In this example, a message identified as `newMessage` is sent to the server.</span></span> <span data-ttu-id="808d9-231">Komunikat składa się z nazwy użytkownika i danych wejściowych użytkownika z pola tekstowego.</span><span class="sxs-lookup"><span data-stu-id="808d9-231">The message consists of the username and the user input from a text box.</span></span> <span data-ttu-id="808d9-232">Jeśli wysyłanie działa, wartość pola tekstowego jest wyczyszczona.</span><span class="sxs-lookup"><span data-stu-id="808d9-232">If the send works, the text box value is cleared.</span></span>

1. <span data-ttu-id="808d9-233">Dodaj metodę `NewMessage` do klasy `ChatHub`:</span><span class="sxs-lookup"><span data-stu-id="808d9-233">Add the `NewMessage` method to the `ChatHub` class:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/Hubs/ChatHub.cs?highlight=8-11)]

    <span data-ttu-id="808d9-234">Poprzedni kod emituje odebrane komunikaty wszystkim połączonym użytkownikom po ich odebraniu przez serwer.</span><span class="sxs-lookup"><span data-stu-id="808d9-234">The preceding code broadcasts received messages to all connected users once the server receives them.</span></span> <span data-ttu-id="808d9-235">Nie jest konieczne posiadanie metody ogólnej `on` do odbierania wszystkich komunikatów.</span><span class="sxs-lookup"><span data-stu-id="808d9-235">It's unnecessary to have a generic `on` method to receive all the messages.</span></span> <span data-ttu-id="808d9-236">Metoda o nazwie po wystarczającej nazwie.</span><span class="sxs-lookup"><span data-stu-id="808d9-236">A method named after the message name suffices.</span></span>

    <span data-ttu-id="808d9-237">W tym przykładzie klient języka TypeScript wysyła komunikat identyfikowany jako `newMessage`.</span><span class="sxs-lookup"><span data-stu-id="808d9-237">In this example, the TypeScript client sends a message identified as `newMessage`.</span></span> <span data-ttu-id="808d9-238">Metoda języka `NewMessage` C# oczekuje danych wysyłanych przez klienta.</span><span class="sxs-lookup"><span data-stu-id="808d9-238">The C# `NewMessage` method expects the data sent by the client.</span></span> <span data-ttu-id="808d9-239">Wykonano wywołanie [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) na [klientach. wszystkie](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span><span class="sxs-lookup"><span data-stu-id="808d9-239">A call is made to [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) on [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span></span> <span data-ttu-id="808d9-240">Odebrane komunikaty są wysyłane do wszystkich klientów podłączonych do centrum.</span><span class="sxs-lookup"><span data-stu-id="808d9-240">The received messages are sent to all clients connected to the hub.</span></span>

## <a name="test-the-app"></a><span data-ttu-id="808d9-241">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="808d9-241">Test the app</span></span>

<span data-ttu-id="808d9-242">Upewnij się, że aplikacja działa z następującymi krokami.</span><span class="sxs-lookup"><span data-stu-id="808d9-242">Confirm that the app works with the following steps.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="808d9-243">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="808d9-243">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="808d9-244">Uruchom pakiet WebPack w trybie *wydania* .</span><span class="sxs-lookup"><span data-stu-id="808d9-244">Run Webpack in *release* mode.</span></span> <span data-ttu-id="808d9-245">Korzystając z okna **konsoli Menedżera pakietów** , uruchom następujące polecenie w katalogu głównym projektu.</span><span class="sxs-lookup"><span data-stu-id="808d9-245">Using the **Package Manager Console** window, run the following command in the project root.</span></span> <span data-ttu-id="808d9-246">Jeśli nie jesteś w katalogu głównym projektu, wprowadź `cd SignalRWebPack` przed wprowadzeniem polecenia.</span><span class="sxs-lookup"><span data-stu-id="808d9-246">If you are not in the project root, enter `cd SignalRWebPack` before entering the command.</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="808d9-247">Wybierz pozycję **Debuguj** > **Uruchom bez debugowania** , aby uruchomić aplikację w przeglądarce bez dołączania debugera.</span><span class="sxs-lookup"><span data-stu-id="808d9-247">Select **Debug** > **Start without debugging** to launch the app in a browser without attaching the debugger.</span></span> <span data-ttu-id="808d9-248">Plik *wwwroot/index.html* jest obsługiwany przez `http://localhost:<port_number>`.</span><span class="sxs-lookup"><span data-stu-id="808d9-248">The *wwwroot/index.html* file is served at `http://localhost:<port_number>`.</span></span>

   <span data-ttu-id="808d9-249">Jeśli zostaną wyświetlone błędy kompilacji, spróbuj zamknąć i ponownie otworzyć rozwiązanie.</span><span class="sxs-lookup"><span data-stu-id="808d9-249">If you get compile errors, try closing and reopening the solution.</span></span> 

1. <span data-ttu-id="808d9-250">Otwórz inne wystąpienie przeglądarki (dowolna przeglądarka).</span><span class="sxs-lookup"><span data-stu-id="808d9-250">Open another browser instance (any browser).</span></span> <span data-ttu-id="808d9-251">Wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="808d9-251">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="808d9-252">Wybierz opcję przeglądarka, wpisz coś w polu tekstowym **komunikat** , a następnie kliknij przycisk **Wyślij** .</span><span class="sxs-lookup"><span data-stu-id="808d9-252">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="808d9-253">Unikatowa nazwa użytkownika i komunikat są wyświetlane na obu stronach natychmiastowo.</span><span class="sxs-lookup"><span data-stu-id="808d9-253">The unique user name and message are displayed on both pages instantly.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="808d9-254">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="808d9-254">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="808d9-255">Uruchom pakiet WebPack w trybie *wydania* , wykonując następujące polecenie w katalogu głównym projektu:</span><span class="sxs-lookup"><span data-stu-id="808d9-255">Run Webpack in *release* mode by executing the following command in the project root:</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="808d9-256">Skompiluj i uruchom aplikację, wykonując następujące polecenie w katalogu głównym projektu:</span><span class="sxs-lookup"><span data-stu-id="808d9-256">Build and run the app by executing the following command in the project root:</span></span>

    ```dotnetcli
    dotnet run
    ```

    <span data-ttu-id="808d9-257">Serwer sieci Web uruchamia aplikację i udostępnia ją na hoście lokalnym.</span><span class="sxs-lookup"><span data-stu-id="808d9-257">The web server starts the app and makes it available on localhost.</span></span>

1. <span data-ttu-id="808d9-258">Otwórz przeglądarkę do `http://localhost:<port_number>`programu.</span><span class="sxs-lookup"><span data-stu-id="808d9-258">Open a browser to `http://localhost:<port_number>`.</span></span> <span data-ttu-id="808d9-259">Plik *wwwroot/index.html* jest obsługiwany.</span><span class="sxs-lookup"><span data-stu-id="808d9-259">The *wwwroot/index.html* file is served.</span></span> <span data-ttu-id="808d9-260">Skopiuj adres URL z paska adresu.</span><span class="sxs-lookup"><span data-stu-id="808d9-260">Copy the URL from the address bar.</span></span>

1. <span data-ttu-id="808d9-261">Otwórz inne wystąpienie przeglądarki (dowolna przeglądarka).</span><span class="sxs-lookup"><span data-stu-id="808d9-261">Open another browser instance (any browser).</span></span> <span data-ttu-id="808d9-262">Wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="808d9-262">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="808d9-263">Wybierz opcję przeglądarka, wpisz coś w polu tekstowym **komunikat** , a następnie kliknij przycisk **Wyślij** .</span><span class="sxs-lookup"><span data-stu-id="808d9-263">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="808d9-264">Unikatowa nazwa użytkownika i komunikat są wyświetlane na obu stronach natychmiastowo.</span><span class="sxs-lookup"><span data-stu-id="808d9-264">The unique user name and message are displayed on both pages instantly.</span></span>

---

![komunikat wyświetlany w obu oknach przeglądarki](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="808d9-266">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="808d9-266">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="808d9-267">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="808d9-267">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="808d9-268">[Program Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) z **ASP.NET i programowaniem aplikacji sieci Web**</span><span class="sxs-lookup"><span data-stu-id="808d9-268">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="808d9-269">Zestaw .NET Core SDK 2,2 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="808d9-269">.NET Core SDK 2.2 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="808d9-270">[Node. js](https://nodejs.org/) z [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="808d9-270">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="808d9-271">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="808d9-271">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="808d9-272">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="808d9-272">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="808d9-273">Zestaw .NET Core SDK 2,2 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="808d9-273">.NET Core SDK 2.2 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="808d9-274">C# dla Visual Studio Code w wersji 1.17.1 lub nowszej</span><span class="sxs-lookup"><span data-stu-id="808d9-274">C# for Visual Studio Code version 1.17.1 or later</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* <span data-ttu-id="808d9-275">[Node. js](https://nodejs.org/) z [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="808d9-275">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

---

## <a name="create-the-aspnet-core-web-app"></a><span data-ttu-id="808d9-276">Tworzenie aplikacji sieci Web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="808d9-276">Create the ASP.NET Core web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="808d9-277">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="808d9-277">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="808d9-278">Skonfiguruj program Visual Studio, aby szukać npm w zmiennej środowiskowej *Path* .</span><span class="sxs-lookup"><span data-stu-id="808d9-278">Configure Visual Studio to look for npm in the *PATH* environment variable.</span></span> <span data-ttu-id="808d9-279">Domyślnie program Visual Studio używa wersji npm znajdującej się w katalogu instalacyjnym.</span><span class="sxs-lookup"><span data-stu-id="808d9-279">By default, Visual Studio uses the version of npm found in its installation directory.</span></span> <span data-ttu-id="808d9-280">Wykonaj te instrukcje w programie Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="808d9-280">Follow these instructions in Visual Studio:</span></span>

1. <span data-ttu-id="808d9-281">Przejdź do **Tools** > **opcji Narzędzia Opcje** > **projekty i rozwiązania** > **Sieć Web zarządzanie pakietami** > **zewnętrznych narzędzi sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="808d9-281">Navigate to **Tools** > **Options** > **Projects and Solutions** > **Web Package Management** > **External Web Tools**.</span></span>
1. <span data-ttu-id="808d9-282">Wybierz z listy wpis *$ (Path)* .</span><span class="sxs-lookup"><span data-stu-id="808d9-282">Select the *$(PATH)* entry from the list.</span></span> <span data-ttu-id="808d9-283">Kliknij strzałkę w górę, aby przenieść wpis do drugiej pozycji na liście.</span><span class="sxs-lookup"><span data-stu-id="808d9-283">Click the up arrow to move the entry to the second position in the list.</span></span>

    ![Konfiguracja programu Visual Studio](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

<span data-ttu-id="808d9-285">Konfiguracja programu Visual Studio została ukończona.</span><span class="sxs-lookup"><span data-stu-id="808d9-285">Visual Studio configuration is completed.</span></span> <span data-ttu-id="808d9-286">Czas na utworzenie projektu.</span><span class="sxs-lookup"><span data-stu-id="808d9-286">It's time to create the project.</span></span>

1. <span data-ttu-id="808d9-287">Użyj opcji menu **plik** > **Nowy** > **projekt** , a następnie wybierz szablon **aplikacja sieci Web ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="808d9-287">Use the **File** > **New** > **Project** menu option and choose the **ASP.NET Core Web Application** template.</span></span>
1. <span data-ttu-id="808d9-288">Nazwij projekt *SignalRWebPack*, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="808d9-288">Name the project *SignalRWebPack*, and select **Create**.</span></span>
1. <span data-ttu-id="808d9-289">Wybierz pozycję *.NET Core* z listy rozwijanej platforma docelowa, a następnie wybierz pozycję *ASP.NET Core 2,2* z listy rozwijanej selektora struktury.</span><span class="sxs-lookup"><span data-stu-id="808d9-289">Select *.NET Core* from the target framework drop-down, and select *ASP.NET Core 2.2* from the framework selector drop-down.</span></span> <span data-ttu-id="808d9-290">Wybierz **pusty** szablon i wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="808d9-290">Select the **Empty** template, and select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="808d9-291">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="808d9-291">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="808d9-292">Uruchom następujące polecenie w **zintegrowanym terminalu**:</span><span class="sxs-lookup"><span data-stu-id="808d9-292">Run the following command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet new web -o SignalRWebPack
```

<span data-ttu-id="808d9-293">Pusta aplikacja sieci Web ASP.NET Core, ukierunkowana na .NET Core, jest tworzona w katalogu *SignalRWebPack* .</span><span class="sxs-lookup"><span data-stu-id="808d9-293">An empty ASP.NET Core web app, targeting .NET Core, is created in a *SignalRWebPack* directory.</span></span>

---

## <a name="configure-webpack-and-typescript"></a><span data-ttu-id="808d9-294">Konfigurowanie pakietu WebPack i języka TypeScript</span><span class="sxs-lookup"><span data-stu-id="808d9-294">Configure Webpack and TypeScript</span></span>

<span data-ttu-id="808d9-295">Poniższe kroki umożliwiają skonfigurowanie konwersji języka TypeScript na JavaScript i zgrupowanie zasobów po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="808d9-295">The following steps configure the conversion of TypeScript to JavaScript and the bundling of client-side resources.</span></span>

1. <span data-ttu-id="808d9-296">Uruchom następujące polecenie w katalogu głównym projektu, aby utworzyć plik *Package. JSON* :</span><span class="sxs-lookup"><span data-stu-id="808d9-296">Run the following command in the project root to create a *package.json* file:</span></span>

    ```console
    npm init -y
    ```

1. <span data-ttu-id="808d9-297">Dodaj wyróżnioną właściwość do pliku *Package. JSON* :</span><span class="sxs-lookup"><span data-stu-id="808d9-297">Add the highlighted property to the *package.json* file:</span></span>

    [!code-json[package.json](signalr-typescript-webpack/sample/2.x/snippets/package1.json?highlight=4)]

    <span data-ttu-id="808d9-298">Ustawienie `private` właściwości w celu `true` uniemożliwienia ostrzeżeń instalacji pakietu w następnym kroku.</span><span class="sxs-lookup"><span data-stu-id="808d9-298">Setting the `private` property to `true` prevents package installation warnings in the next step.</span></span>

1. <span data-ttu-id="808d9-299">Zainstaluj wymagane pakiety npm.</span><span class="sxs-lookup"><span data-stu-id="808d9-299">Install the required npm packages.</span></span> <span data-ttu-id="808d9-300">Uruchom następujące polecenie z poziomu głównego projektu:</span><span class="sxs-lookup"><span data-stu-id="808d9-300">Run the following command from the project root:</span></span>

    ```console
    npm install -D -E clean-webpack-plugin@1.0.1 css-loader@2.1.0 html-webpack-plugin@4.0.0-beta.5 mini-css-extract-plugin@0.5.0 ts-loader@5.3.3 typescript@3.3.3 webpack@4.29.3 webpack-cli@3.2.3
    ```

    <span data-ttu-id="808d9-301">Niektóre szczegóły polecenia do uwagi:</span><span class="sxs-lookup"><span data-stu-id="808d9-301">Some command details to note:</span></span>

    * <span data-ttu-id="808d9-302">Numer wersji następuje po `@` znaku dla każdej nazwy pakietu.</span><span class="sxs-lookup"><span data-stu-id="808d9-302">A version number follows the `@` sign for each package name.</span></span> <span data-ttu-id="808d9-303">npm instaluje te określone wersje pakietu.</span><span class="sxs-lookup"><span data-stu-id="808d9-303">npm installs those specific package versions.</span></span>
    * <span data-ttu-id="808d9-304">`-E` Opcja wyłącza domyślne zachowanie npm podczas pisania operatorów zakresu [wersji semantycznej](https://semver.org/) w pliku *Package. JSON*.</span><span class="sxs-lookup"><span data-stu-id="808d9-304">The `-E` option disables npm's default behavior of writing [semantic versioning](https://semver.org/) range operators to *package.json*.</span></span> <span data-ttu-id="808d9-305">Na przykład, `"webpack": "4.29.3"` jest używany zamiast `"webpack": "^4.29.3"`.</span><span class="sxs-lookup"><span data-stu-id="808d9-305">For example, `"webpack": "4.29.3"` is used instead of `"webpack": "^4.29.3"`.</span></span> <span data-ttu-id="808d9-306">Ta opcja Zapobiega niezamierzonym uaktualnianiu do nowszych wersji pakietu.</span><span class="sxs-lookup"><span data-stu-id="808d9-306">This option prevents unintended upgrades to newer package versions.</span></span>

    <span data-ttu-id="808d9-307">Więcej szczegółów można znaleźć w dokumentacji [npm-Install](https://docs.npmjs.com/cli/install) .</span><span class="sxs-lookup"><span data-stu-id="808d9-307">See the [npm-install](https://docs.npmjs.com/cli/install) docs for more detail.</span></span>

1. <span data-ttu-id="808d9-308">Zastąp `scripts` właściwość pliku *Package. JSON* następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="808d9-308">Replace the `scripts` property of the *package.json* file with the following code:</span></span>

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    <span data-ttu-id="808d9-309">Niektóre objaśnienia dotyczące skryptów:</span><span class="sxs-lookup"><span data-stu-id="808d9-309">Some explanation of the scripts:</span></span>

    * <span data-ttu-id="808d9-310">`build`: Łączy zasoby po stronie klienta w trybie tworzenia i czujki pod kątem zmian w pliku.</span><span class="sxs-lookup"><span data-stu-id="808d9-310">`build`: Bundles the client-side resources in development mode and watches for file changes.</span></span> <span data-ttu-id="808d9-311">Obserwator plików powoduje, że pakiet jest generowany ponownie za każdym razem, gdy plik projektu jest zmieniany.</span><span class="sxs-lookup"><span data-stu-id="808d9-311">The file watcher causes the bundle to regenerate each time a project file changes.</span></span> <span data-ttu-id="808d9-312">`mode` Opcja wyłącza optymalizacje produkcyjne, takie jak wstrząsanie i minifikacjaowanie drzewa.</span><span class="sxs-lookup"><span data-stu-id="808d9-312">The `mode` option disables production optimizations, such as tree shaking and minification.</span></span> <span data-ttu-id="808d9-313">Używać `build` tylko w programowaniu.</span><span class="sxs-lookup"><span data-stu-id="808d9-313">Only use `build` in development.</span></span>
    * <span data-ttu-id="808d9-314">`release`: Pakietuje zasoby po stronie klienta w trybie produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="808d9-314">`release`: Bundles the client-side resources in production mode.</span></span>
    * <span data-ttu-id="808d9-315">`publish`: Uruchamia `release` skrypt służący do łączenia zasobów po stronie klienta w trybie produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="808d9-315">`publish`: Runs the `release` script to bundle the client-side resources in production mode.</span></span> <span data-ttu-id="808d9-316">Wywołuje polecenie [publikowania](/dotnet/core/tools/dotnet-publish) interfejs wiersza polecenia platformy .NET Core, aby opublikować aplikację.</span><span class="sxs-lookup"><span data-stu-id="808d9-316">It calls the .NET Core CLI's [publish](/dotnet/core/tools/dotnet-publish) command to publish the app.</span></span>

1. <span data-ttu-id="808d9-317">Utwórz plik o nazwie *WebPack. config. js* w katalogu głównym projektu przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="808d9-317">Create a file named *webpack.config.js* in the project root, with the following code:</span></span>

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/2.x/webpack.config.js)]

    <span data-ttu-id="808d9-318">Poprzedni plik konfiguruje kompilację pakietu WebPack.</span><span class="sxs-lookup"><span data-stu-id="808d9-318">The preceding file configures the Webpack compilation.</span></span> <span data-ttu-id="808d9-319">Niektóre szczegóły konfiguracji do uwagi:</span><span class="sxs-lookup"><span data-stu-id="808d9-319">Some configuration details to note:</span></span>

    * <span data-ttu-id="808d9-320">`output` Właściwość zastępuje domyślną wartość *rozkłu*.</span><span class="sxs-lookup"><span data-stu-id="808d9-320">The `output` property overrides the default value of *dist*.</span></span> <span data-ttu-id="808d9-321">Pakiet jest emitowany w katalogu *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="808d9-321">The bundle is instead emitted in the *wwwroot* directory.</span></span>
    * <span data-ttu-id="808d9-322">`resolve.extensions` Tablica zawiera *. js* do zaimportowania klienta sygnału JavaScript.</span><span class="sxs-lookup"><span data-stu-id="808d9-322">The `resolve.extensions` array includes *.js* to import the SignalR client JavaScript.</span></span>

1. <span data-ttu-id="808d9-323">Utwórz nowy katalog *src* w katalogu głównym projektu do przechowywania zasobów po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="808d9-323">Create a new *src* directory in the project root to store the project's client-side assets.</span></span>

1. <span data-ttu-id="808d9-324">Utwórz *src/index.html* z następującą adiustacją.</span><span class="sxs-lookup"><span data-stu-id="808d9-324">Create *src/index.html* with the following markup.</span></span>

    [!code-html[index.html](signalr-typescript-webpack/sample/2.x/src/index.html)]

    <span data-ttu-id="808d9-325">Powyższy kod HTML definiuje standardowe znaczniki strony głównej.</span><span class="sxs-lookup"><span data-stu-id="808d9-325">The preceding HTML defines the homepage's boilerplate markup.</span></span>

1. <span data-ttu-id="808d9-326">Utwórz nowy katalog *src/CSS* .</span><span class="sxs-lookup"><span data-stu-id="808d9-326">Create a new *src/css* directory.</span></span> <span data-ttu-id="808d9-327">Celem jest przechowywanie plików *CSS* projektu.</span><span class="sxs-lookup"><span data-stu-id="808d9-327">Its purpose is to store the project's *.css* files.</span></span>

1. <span data-ttu-id="808d9-328">Utwórz *src/CSS/Main. css* z następującą adiustacją:</span><span class="sxs-lookup"><span data-stu-id="808d9-328">Create *src/css/main.css* with the following markup:</span></span>

    [!code-css[main.css](signalr-typescript-webpack/sample/2.x/src/css/main.css)]

    <span data-ttu-id="808d9-329">Poprzedni plik *Main. css* jest stylem aplikacji.</span><span class="sxs-lookup"><span data-stu-id="808d9-329">The preceding *main.css* file styles the app.</span></span>

1. <span data-ttu-id="808d9-330">Utwórz plik *src/tsconfig. JSON* z następującym kodem JSON:</span><span class="sxs-lookup"><span data-stu-id="808d9-330">Create *src/tsconfig.json* with the following JSON:</span></span>

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/2.x/src/tsconfig.json)]

    <span data-ttu-id="808d9-331">Poprzedni kod konfiguruje kompilator języka TypeScript w celu utworzenia kodu JavaScript zgodnego z [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5.</span><span class="sxs-lookup"><span data-stu-id="808d9-331">The preceding code configures the TypeScript compiler to produce [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5-compatible JavaScript.</span></span>

1. <span data-ttu-id="808d9-332">Utwórz *src/index. TS* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="808d9-332">Create *src/index.ts* with the following code:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    <span data-ttu-id="808d9-333">Poprzedni kod TypeScript pobiera odwołania do elementów DOM i dołącza dwa programy obsługi zdarzeń:</span><span class="sxs-lookup"><span data-stu-id="808d9-333">The preceding TypeScript retrieves references to DOM elements and attaches two event handlers:</span></span>

    * <span data-ttu-id="808d9-334">`keyup`: To zdarzenie jest wyzwalane, gdy użytkownik wpisze `tbMessage` wartość w polu tekstowym.</span><span class="sxs-lookup"><span data-stu-id="808d9-334">`keyup`: This event fires when the user types in the `tbMessage` textbox.</span></span> <span data-ttu-id="808d9-335">`send` Funkcja jest wywoływana, gdy użytkownik naciśnie klawisz **Enter** .</span><span class="sxs-lookup"><span data-stu-id="808d9-335">The `send` function is called when the user presses the **Enter** key.</span></span>
    * <span data-ttu-id="808d9-336">`click`: To zdarzenie jest wyzwalane, gdy użytkownik kliknie przycisk **Wyślij** .</span><span class="sxs-lookup"><span data-stu-id="808d9-336">`click`: This event fires when the user clicks the **Send** button.</span></span> <span data-ttu-id="808d9-337">Wywołanie funkcji `send`.</span><span class="sxs-lookup"><span data-stu-id="808d9-337">The `send` function is called.</span></span>

## <a name="configure-the-aspnet-core-app"></a><span data-ttu-id="808d9-338">Konfigurowanie aplikacji ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="808d9-338">Configure the ASP.NET Core app</span></span>

1. <span data-ttu-id="808d9-339">Kod podany w `Startup.Configure` metodzie wyświetla *Hello World!*.</span><span class="sxs-lookup"><span data-stu-id="808d9-339">The code provided in the `Startup.Configure` method displays *Hello World!*.</span></span> <span data-ttu-id="808d9-340">Zastąp `app.Run` wywołanie metody wywołaniami do [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) i [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="808d9-340">Replace the `app.Run` method call with calls to [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseStaticDefaultFiles)]

    <span data-ttu-id="808d9-341">Poprzedni kod pozwala serwerowi zlokalizować i obsłużyć plik *index. html* , niezależnie od tego, czy użytkownik wprowadza pełny adres URL, czy główny adres URL aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="808d9-341">The preceding code allows the server to locate and serve the *index.html* file, whether the user enters its full URL or the root URL of the web app.</span></span>

1. <span data-ttu-id="808d9-342">Wywołaj metodę [Addsignaler](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) w `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="808d9-342">Call [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="808d9-343">Dodaje do projektu usługi sygnalizujące.</span><span class="sxs-lookup"><span data-stu-id="808d9-343">It adds the SignalR services to the project.</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_AddSignalR)]

1. <span data-ttu-id="808d9-344">Mapuj trasę */Hub* do `ChatHub` centrum.</span><span class="sxs-lookup"><span data-stu-id="808d9-344">Map a */hub* route to the `ChatHub` hub.</span></span> <span data-ttu-id="808d9-345">Dodaj następujące wiersze na końcu `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="808d9-345">Add the following lines at the end of `Startup.Configure`:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseSignalR)]

1. <span data-ttu-id="808d9-346">Utwórz nowy katalog o nazwie *Hubs*w katalogu głównym projektu.</span><span class="sxs-lookup"><span data-stu-id="808d9-346">Create a new directory, called *Hubs*, in the project root.</span></span> <span data-ttu-id="808d9-347">Celem jest przechowywanie centrum sygnalizującego, które jest tworzone w następnym kroku.</span><span class="sxs-lookup"><span data-stu-id="808d9-347">Its purpose is to store the SignalR hub, which is created in the next step.</span></span>

1. <span data-ttu-id="808d9-348">Utwórz centra centrów */ChatHub. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="808d9-348">Create hub *Hubs/ChatHub.cs* with the following code:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. <span data-ttu-id="808d9-349">Dodaj następujący kod w górnej części pliku *Startup.cs* , aby rozwiązać `ChatHub` odwołanie:</span><span class="sxs-lookup"><span data-stu-id="808d9-349">Add the following code at the top of the *Startup.cs* file to resolve the `ChatHub` reference:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a><span data-ttu-id="808d9-350">Włączanie komunikacji klienta i serwera</span><span class="sxs-lookup"><span data-stu-id="808d9-350">Enable client and server communication</span></span>

<span data-ttu-id="808d9-351">W aplikacji jest obecnie wyświetlany prosty formularz służący do wysyłania komunikatów.</span><span class="sxs-lookup"><span data-stu-id="808d9-351">The app currently displays a simple form to send messages.</span></span> <span data-ttu-id="808d9-352">Nic się nie dzieje, gdy użytkownik spróbuje to zrobić.</span><span class="sxs-lookup"><span data-stu-id="808d9-352">Nothing happens when you try to do so.</span></span> <span data-ttu-id="808d9-353">Serwer nasłuchuje określonej trasy, ale nic nie robi z wysłanymi komunikatami.</span><span class="sxs-lookup"><span data-stu-id="808d9-353">The server is listening to a specific route but does nothing with sent messages.</span></span>

1. <span data-ttu-id="808d9-354">Uruchom następujące polecenie w katalogu głównym projektu:</span><span class="sxs-lookup"><span data-stu-id="808d9-354">Run the following command at the project root:</span></span>

    ```console
    npm install @aspnet/signalr
    ```

    <span data-ttu-id="808d9-355">Poprzednie polecenie instaluje klienta języka [TypeScript sygnalizującego](https://www.npmjs.com/package/@microsoft/signalr), który umożliwia klientowi wysyłanie komunikatów do serwera.</span><span class="sxs-lookup"><span data-stu-id="808d9-355">The preceding command installs the [SignalR TypeScript client](https://www.npmjs.com/package/@microsoft/signalr), which allows the client to send messages to the server.</span></span>

1. <span data-ttu-id="808d9-356">Dodaj wyróżniony kod do pliku *src/index. TS* :</span><span class="sxs-lookup"><span data-stu-id="808d9-356">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    <span data-ttu-id="808d9-357">Poprzedni kod obsługuje otrzymywanie komunikatów z serwera.</span><span class="sxs-lookup"><span data-stu-id="808d9-357">The preceding code supports receiving messages from the server.</span></span> <span data-ttu-id="808d9-358">`HubConnectionBuilder` Klasa tworzy nowy Konstruktor służący do konfigurowania połączenia z serwerem.</span><span class="sxs-lookup"><span data-stu-id="808d9-358">The `HubConnectionBuilder` class creates a new builder for configuring the server connection.</span></span> <span data-ttu-id="808d9-359">`withUrl` Funkcja KONFIGURUJE adres URL centrum.</span><span class="sxs-lookup"><span data-stu-id="808d9-359">The `withUrl` function configures the hub URL.</span></span>

    <span data-ttu-id="808d9-360">Program sygnalizujący umożliwia wymianę komunikatów między klientem a serwerem.</span><span class="sxs-lookup"><span data-stu-id="808d9-360">SignalR enables the exchange of messages between a client and a server.</span></span> <span data-ttu-id="808d9-361">Każdy komunikat ma określoną nazwę.</span><span class="sxs-lookup"><span data-stu-id="808d9-361">Each message has a specific name.</span></span> <span data-ttu-id="808d9-362">Na przykład komunikaty o nazwie `messageReceived` mogą uruchamiać logikę odpowiedzialną za wyświetlanie nowej wiadomości w strefie messages.</span><span class="sxs-lookup"><span data-stu-id="808d9-362">For example, messages with the name `messageReceived` can run the logic responsible for displaying the new message in the messages zone.</span></span> <span data-ttu-id="808d9-363">Nasłuchiwanie określonego komunikatu można wykonać za pomocą `on` funkcji.</span><span class="sxs-lookup"><span data-stu-id="808d9-363">Listening to a specific message can be done via the `on` function.</span></span> <span data-ttu-id="808d9-364">Można nasłuchiwać dowolnej liczby nazw komunikatów.</span><span class="sxs-lookup"><span data-stu-id="808d9-364">You can listen to any number of message names.</span></span> <span data-ttu-id="808d9-365">Możliwe jest również przekazywanie parametrów do wiadomości, takich jak nazwa autora i zawartość otrzymanej wiadomości.</span><span class="sxs-lookup"><span data-stu-id="808d9-365">It's also possible to pass parameters to the message, such as the author's name and the content of the message received.</span></span> <span data-ttu-id="808d9-366">Po odebraniu komunikatu przez klienta zostanie utworzony nowy `div` element z nazwą autora i treścią komunikatu w jego `innerHTML` atrybucie.</span><span class="sxs-lookup"><span data-stu-id="808d9-366">Once the client receives a message, a new `div` element is created with the author's name and the message content in its `innerHTML` attribute.</span></span> <span data-ttu-id="808d9-367">Nowa wiadomość zostanie dodana do głównego `div` elementu wyświetlającego komunikaty.</span><span class="sxs-lookup"><span data-stu-id="808d9-367">The new message is added to the main `div` element displaying the messages.</span></span>

1. <span data-ttu-id="808d9-368">Teraz, gdy klient może odebrać komunikat, skonfiguruj go do wysyłania wiadomości.</span><span class="sxs-lookup"><span data-stu-id="808d9-368">Now that the client can receive a message, configure it to send messages.</span></span> <span data-ttu-id="808d9-369">Dodaj wyróżniony kod do pliku *src/index. TS* :</span><span class="sxs-lookup"><span data-stu-id="808d9-369">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/src/index.ts?highlight=34-35)]

    <span data-ttu-id="808d9-370">Wysyłanie komunikatu przez połączenie z usługą WebSockets wymaga wywołania `send` metody.</span><span class="sxs-lookup"><span data-stu-id="808d9-370">Sending a message through the WebSockets connection requires calling the `send` method.</span></span> <span data-ttu-id="808d9-371">Pierwszym parametrem metody jest nazwa komunikatu.</span><span class="sxs-lookup"><span data-stu-id="808d9-371">The method's first parameter is the message name.</span></span> <span data-ttu-id="808d9-372">Dane komunikatu są nieodpowiednie dla innych parametrów.</span><span class="sxs-lookup"><span data-stu-id="808d9-372">The message data inhabits the other parameters.</span></span> <span data-ttu-id="808d9-373">W tym przykładzie komunikat identyfikowany jako `newMessage` jest wysyłany do serwera.</span><span class="sxs-lookup"><span data-stu-id="808d9-373">In this example, a message identified as `newMessage` is sent to the server.</span></span> <span data-ttu-id="808d9-374">Komunikat składa się z nazwy użytkownika i danych wejściowych użytkownika z pola tekstowego.</span><span class="sxs-lookup"><span data-stu-id="808d9-374">The message consists of the username and the user input from a text box.</span></span> <span data-ttu-id="808d9-375">Jeśli wysyłanie działa, wartość pola tekstowego jest wyczyszczona.</span><span class="sxs-lookup"><span data-stu-id="808d9-375">If the send works, the text box value is cleared.</span></span>

1. <span data-ttu-id="808d9-376">Dodaj metodę `NewMessage` do klasy `ChatHub`:</span><span class="sxs-lookup"><span data-stu-id="808d9-376">Add the `NewMessage` method to the `ChatHub` class:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/Hubs/ChatHub.cs?highlight=8-11)]

    <span data-ttu-id="808d9-377">Poprzedni kod emituje odebrane komunikaty wszystkim połączonym użytkownikom po ich odebraniu przez serwer.</span><span class="sxs-lookup"><span data-stu-id="808d9-377">The preceding code broadcasts received messages to all connected users once the server receives them.</span></span> <span data-ttu-id="808d9-378">Nie jest konieczne posiadanie metody ogólnej `on` do odbierania wszystkich komunikatów.</span><span class="sxs-lookup"><span data-stu-id="808d9-378">It's unnecessary to have a generic `on` method to receive all the messages.</span></span> <span data-ttu-id="808d9-379">Metoda o nazwie po wystarczającej nazwie.</span><span class="sxs-lookup"><span data-stu-id="808d9-379">A method named after the message name suffices.</span></span>

    <span data-ttu-id="808d9-380">W tym przykładzie klient języka TypeScript wysyła komunikat identyfikowany jako `newMessage`.</span><span class="sxs-lookup"><span data-stu-id="808d9-380">In this example, the TypeScript client sends a message identified as `newMessage`.</span></span> <span data-ttu-id="808d9-381">Metoda języka `NewMessage` C# oczekuje danych wysyłanych przez klienta.</span><span class="sxs-lookup"><span data-stu-id="808d9-381">The C# `NewMessage` method expects the data sent by the client.</span></span> <span data-ttu-id="808d9-382">Wykonano wywołanie [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) na [klientach. wszystkie](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span><span class="sxs-lookup"><span data-stu-id="808d9-382">A call is made to [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) on [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span></span> <span data-ttu-id="808d9-383">Odebrane komunikaty są wysyłane do wszystkich klientów podłączonych do centrum.</span><span class="sxs-lookup"><span data-stu-id="808d9-383">The received messages are sent to all clients connected to the hub.</span></span>

## <a name="test-the-app"></a><span data-ttu-id="808d9-384">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="808d9-384">Test the app</span></span>

<span data-ttu-id="808d9-385">Upewnij się, że aplikacja działa z następującymi krokami.</span><span class="sxs-lookup"><span data-stu-id="808d9-385">Confirm that the app works with the following steps.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="808d9-386">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="808d9-386">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="808d9-387">Uruchom pakiet WebPack w trybie *wydania* .</span><span class="sxs-lookup"><span data-stu-id="808d9-387">Run Webpack in *release* mode.</span></span> <span data-ttu-id="808d9-388">Korzystając z okna **konsoli Menedżera pakietów** , uruchom następujące polecenie w katalogu głównym projektu.</span><span class="sxs-lookup"><span data-stu-id="808d9-388">Using the **Package Manager Console** window, run the following command in the project root.</span></span> <span data-ttu-id="808d9-389">Jeśli nie jesteś w katalogu głównym projektu, wprowadź `cd SignalRWebPack` przed wprowadzeniem polecenia.</span><span class="sxs-lookup"><span data-stu-id="808d9-389">If you are not in the project root, enter `cd SignalRWebPack` before entering the command.</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="808d9-390">Wybierz pozycję **Debuguj** > **Uruchom bez debugowania** , aby uruchomić aplikację w przeglądarce bez dołączania debugera.</span><span class="sxs-lookup"><span data-stu-id="808d9-390">Select **Debug** > **Start without debugging** to launch the app in a browser without attaching the debugger.</span></span> <span data-ttu-id="808d9-391">Plik *wwwroot/index.html* jest obsługiwany przez `http://localhost:<port_number>`.</span><span class="sxs-lookup"><span data-stu-id="808d9-391">The *wwwroot/index.html* file is served at `http://localhost:<port_number>`.</span></span>

1. <span data-ttu-id="808d9-392">Otwórz inne wystąpienie przeglądarki (dowolna przeglądarka).</span><span class="sxs-lookup"><span data-stu-id="808d9-392">Open another browser instance (any browser).</span></span> <span data-ttu-id="808d9-393">Wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="808d9-393">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="808d9-394">Wybierz opcję przeglądarka, wpisz coś w polu tekstowym **komunikat** , a następnie kliknij przycisk **Wyślij** .</span><span class="sxs-lookup"><span data-stu-id="808d9-394">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="808d9-395">Unikatowa nazwa użytkownika i komunikat są wyświetlane na obu stronach natychmiastowo.</span><span class="sxs-lookup"><span data-stu-id="808d9-395">The unique user name and message are displayed on both pages instantly.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="808d9-396">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="808d9-396">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="808d9-397">Uruchom pakiet WebPack w trybie *wydania* , wykonując następujące polecenie w katalogu głównym projektu:</span><span class="sxs-lookup"><span data-stu-id="808d9-397">Run Webpack in *release* mode by executing the following command in the project root:</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="808d9-398">Skompiluj i uruchom aplikację, wykonując następujące polecenie w katalogu głównym projektu:</span><span class="sxs-lookup"><span data-stu-id="808d9-398">Build and run the app by executing the following command in the project root:</span></span>

    ```dotnetcli
    dotnet run
    ```

    <span data-ttu-id="808d9-399">Serwer sieci Web uruchamia aplikację i udostępnia ją na hoście lokalnym.</span><span class="sxs-lookup"><span data-stu-id="808d9-399">The web server starts the app and makes it available on localhost.</span></span>

1. <span data-ttu-id="808d9-400">Otwórz przeglądarkę do `http://localhost:<port_number>`programu.</span><span class="sxs-lookup"><span data-stu-id="808d9-400">Open a browser to `http://localhost:<port_number>`.</span></span> <span data-ttu-id="808d9-401">Plik *wwwroot/index.html* jest obsługiwany.</span><span class="sxs-lookup"><span data-stu-id="808d9-401">The *wwwroot/index.html* file is served.</span></span> <span data-ttu-id="808d9-402">Skopiuj adres URL z paska adresu.</span><span class="sxs-lookup"><span data-stu-id="808d9-402">Copy the URL from the address bar.</span></span>

1. <span data-ttu-id="808d9-403">Otwórz inne wystąpienie przeglądarki (dowolna przeglądarka).</span><span class="sxs-lookup"><span data-stu-id="808d9-403">Open another browser instance (any browser).</span></span> <span data-ttu-id="808d9-404">Wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="808d9-404">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="808d9-405">Wybierz opcję przeglądarka, wpisz coś w polu tekstowym **komunikat** , a następnie kliknij przycisk **Wyślij** .</span><span class="sxs-lookup"><span data-stu-id="808d9-405">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="808d9-406">Unikatowa nazwa użytkownika i komunikat są wyświetlane na obu stronach natychmiastowo.</span><span class="sxs-lookup"><span data-stu-id="808d9-406">The unique user name and message are displayed on both pages instantly.</span></span>

---

![komunikat wyświetlany w obu oknach przeglądarki](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="808d9-408">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="808d9-408">Additional resources</span></span>

* <xref:signalr/javascript-client>
* <xref:signalr/hubs>
