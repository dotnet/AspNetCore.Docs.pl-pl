---
title: Używanie ASP.NET Core SignalR z typescriptem i pakietem internetowym
author: ssougnez
description: W tym samouczku można skonfigurować pakiet Webpack do SignalR wiązki i tworzenia aplikacji sieci web ASP.NET Core, której klient jest napisany w języku TypeScript.
ms.author: bradyg
ms.custom: mvc
ms.date: 02/10/2020
no-loc:
- SignalR
uid: tutorials/signalr-typescript-webpack
ms.openlocfilehash: ce5752743912a979a95fb5d504e4bcbb2b69ce1e
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511343"
---
# <a name="use-aspnet-core-signalr-with-typescript-and-webpack"></a><span data-ttu-id="eaa4d-103">Użyj ASP.NET Core SignalR z TypeScript i Webpack</span><span class="sxs-lookup"><span data-stu-id="eaa4d-103">Use ASP.NET Core SignalR with TypeScript and Webpack</span></span>

<span data-ttu-id="eaa4d-104">Przez [Sébastien Sougnez](https://twitter.com/ssougnez) i [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="eaa4d-104">By [Sébastien Sougnez](https://twitter.com/ssougnez) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="eaa4d-105">[Pakiet internetowy](https://webpack.js.org/) umożliwia deweloperom wiązać i tworzyć zasoby po stronie klienta aplikacji sieci web.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-105">[Webpack](https://webpack.js.org/) enables developers to bundle and build the client-side resources of a web app.</span></span> <span data-ttu-id="eaa4d-106">Ten samouczek pokazuje, przy użyciu Webpack w ASP.NET Core SignalR aplikacji internetowej, której klient jest napisany w [języku TypeScript](https://www.typescriptlang.org/).</span><span class="sxs-lookup"><span data-stu-id="eaa4d-106">This tutorial demonstrates using Webpack in an ASP.NET Core SignalR web app whose client is written in [TypeScript](https://www.typescriptlang.org/).</span></span>

<span data-ttu-id="eaa4d-107">Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="eaa4d-107">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="eaa4d-108">Rusztowanie aplikacji Core SignalR ASP.NET starter</span><span class="sxs-lookup"><span data-stu-id="eaa4d-108">Scaffold a starter ASP.NET Core SignalR app</span></span>
> * <span data-ttu-id="eaa4d-109">Konfigurowanie klienta SignalR TypeScript</span><span class="sxs-lookup"><span data-stu-id="eaa4d-109">Configure the SignalR TypeScript client</span></span>
> * <span data-ttu-id="eaa4d-110">Konfigurowanie potoku kompilacji przy użyciu pakietu Webpack</span><span class="sxs-lookup"><span data-stu-id="eaa4d-110">Configure a build pipeline using Webpack</span></span>
> * <span data-ttu-id="eaa4d-111">Konfigurowanie serwera SignalR</span><span class="sxs-lookup"><span data-stu-id="eaa4d-111">Configure the SignalR server</span></span>
> * <span data-ttu-id="eaa4d-112">Włączanie komunikacji między klientem a serwerem</span><span class="sxs-lookup"><span data-stu-id="eaa4d-112">Enable communication between client and server</span></span>

<span data-ttu-id="eaa4d-113">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="eaa4d-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="eaa4d-114">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="eaa4d-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="eaa4d-115">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eaa4d-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="eaa4d-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) z **obciążeniem ASP.NET i tworzenia stron internetowych**</span><span class="sxs-lookup"><span data-stu-id="eaa4d-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="eaa4d-117">Zestaw .NET Core SDK 3.0 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="eaa4d-117">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="eaa4d-118">[Node.js](https://nodejs.org/) z [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="eaa4d-118">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="eaa4d-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eaa4d-119">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="eaa4d-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eaa4d-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="eaa4d-121">Zestaw .NET Core SDK 3.0 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="eaa4d-121">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="eaa4d-122">C# dla programu Visual Studio Code w wersji 1.17.1 lub nowszej</span><span class="sxs-lookup"><span data-stu-id="eaa4d-122">C# for Visual Studio Code version 1.17.1 or later</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* <span data-ttu-id="eaa4d-123">[Node.js](https://nodejs.org/) z [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="eaa4d-123">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

---

## <a name="create-the-aspnet-core-web-app"></a><span data-ttu-id="eaa4d-124">Tworzenie aplikacji sieci Web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="eaa4d-124">Create the ASP.NET Core web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="eaa4d-125">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eaa4d-125">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="eaa4d-126">Skonfiguruj program Visual Studio, aby wyszukywać npm w zmiennej środowiskowej *PATH.*</span><span class="sxs-lookup"><span data-stu-id="eaa4d-126">Configure Visual Studio to look for npm in the *PATH* environment variable.</span></span> <span data-ttu-id="eaa4d-127">Domyślnie program Visual Studio używa wersji npm znalezionej w katalogu instalacyjnym.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-127">By default, Visual Studio uses the version of npm found in its installation directory.</span></span> <span data-ttu-id="eaa4d-128">Postępuj zgodnie z poniższymi instrukcjami w programie Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="eaa4d-128">Follow these instructions in Visual Studio:</span></span>

1. <span data-ttu-id="eaa4d-129">Uruchom program Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-129">Launch Visual Studio.</span></span> <span data-ttu-id="eaa4d-130">W oknie startowym wybierz pozycję **Kontynuuj bez kodu**.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-130">At the start window, select **Continue without code**.</span></span>
1. <span data-ttu-id="eaa4d-131">Przejdź do **pozycji** > **Opcje** > narzędzi Projekty i rozwiązania **Zarządzania pakietami** > **Projects and Solutions** > sieci Web Zewnętrzne narzędzia sieci **Web**.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-131">Navigate to **Tools** > **Options** > **Projects and Solutions** > **Web Package Management** > **External Web Tools**.</span></span>
1. <span data-ttu-id="eaa4d-132">Wybierz wpis *$(PATH)* z listy.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-132">Select the *$(PATH)* entry from the list.</span></span> <span data-ttu-id="eaa4d-133">Kliknij strzałkę w górę, aby przenieść wpis na drugą pozycję na liście, a następnie wybierz **przycisk OK**.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-133">Click the up arrow to move the entry to the second position in the list, and select **OK**.</span></span>

    ![Konfiguracja programu Visual Studio](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

<span data-ttu-id="eaa4d-135">Konfiguracja programu Visual Studio została ukończona.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-135">Visual Studio configuration is complete.</span></span>

1. <span data-ttu-id="eaa4d-136">Użyj opcji menu **Plik** > **nowego** > **projektu** i wybierz szablon ASP.NET Core **Web Application.**</span><span class="sxs-lookup"><span data-stu-id="eaa4d-136">Use the **File** > **New** > **Project** menu option and choose the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="eaa4d-137">Wybierz **pozycję Dalej**.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-137">Select **Next**.</span></span>
1. <span data-ttu-id="eaa4d-138">Nazwij projekt *SignalRWebPack*i wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-138">Name the project *SignalRWebPack*, and select **Create**.</span></span>
1. <span data-ttu-id="eaa4d-139">Wybierz *.NET Core* z listy rozwijanej struktury docelowej i wybierz *ASP.NET Core 3.1* z listy rozwijanej selektora platformy.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-139">Select *.NET Core* from the target framework drop-down, and select *ASP.NET Core 3.1* from the framework selector drop-down.</span></span> <span data-ttu-id="eaa4d-140">Zaznacz **pusty** szablon i wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-140">Select the **Empty** template, and select **Create**.</span></span>

<span data-ttu-id="eaa4d-141">Dodaj `Microsoft.TypeScript.MSBuild` pakiet do projektu:</span><span class="sxs-lookup"><span data-stu-id="eaa4d-141">Add the `Microsoft.TypeScript.MSBuild` package to the project:</span></span>

1. <span data-ttu-id="eaa4d-142">W **Eksploratorze rozwiązań** (prawe okienko) kliknij prawym przyciskiem myszy węzeł projektu i wybierz polecenie **Zarządzaj pakietami NuGet**.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-142">In **Solution Explorer** (right pane), right-click the project node and select **Manage NuGet Packages**.</span></span> <span data-ttu-id="eaa4d-143">Na karcie **Przeglądaj** wyszukaj `Microsoft.TypeScript.MSBuild`, a następnie kliknij pozycję **Zainstaluj** po prawej stronie, aby zainstalować pakiet.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-143">In the **Browse** tab, search for `Microsoft.TypeScript.MSBuild`, and then click **Install** on the right to install the package.</span></span>

<span data-ttu-id="eaa4d-144">Visual Studio dodaje pakiet NuGet w węźle **Zależności** w **Eksploratorze rozwiązań,** włączając kompilację TypeScript w projekcie.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-144">Visual Studio adds the NuGet package under the **Dependencies** node in **Solution Explorer**, enabling TypeScript compilation in the project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="eaa4d-145">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eaa4d-145">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="eaa4d-146">Uruchom następujące polecenie w **terminalu zintegrowanym:**</span><span class="sxs-lookup"><span data-stu-id="eaa4d-146">Run the following command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet new web -o SignalRWebPack
code -r SignalRWebPack
```

* <span data-ttu-id="eaa4d-147">Polecenie `dotnet new` tworzy pustą aplikację sieci web ASP.NET Core w katalogu *SignalRWebPack.*</span><span class="sxs-lookup"><span data-stu-id="eaa4d-147">The `dotnet new` command creates an empty ASP.NET Core web app in a *SignalRWebPack* directory.</span></span>
* <span data-ttu-id="eaa4d-148">Polecenie `code` otwiera folder *SignalRWebPack* w bieżącym wystąpieniu programu Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-148">The `code` command opens the *SignalRWebPack* folder in the current instance of Visual Studio Code.</span></span>

<span data-ttu-id="eaa4d-149">Uruchom następujące polecenie .NET Core CLI w **terminalu zintegrowanym:**</span><span class="sxs-lookup"><span data-stu-id="eaa4d-149">Run the following .NET Core CLI command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet add package Microsoft.TypeScript.MSBuild
```

<span data-ttu-id="eaa4d-150">Poprzednie polecenie dodaje pakiet [Microsoft.TypeScript.MSBuild,](https://www.nuget.org/packages/Microsoft.TypeScript.MSBuild/) włączając kompilację TypeScript w projekcie.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-150">The preceding command adds the [Microsoft.TypeScript.MSBuild](https://www.nuget.org/packages/Microsoft.TypeScript.MSBuild/) package, enabling TypeScript compilation in the project.</span></span>

---

## <a name="configure-webpack-and-typescript"></a><span data-ttu-id="eaa4d-151">Konfigurowanie pakietu Web i kodu TypeScript</span><span class="sxs-lookup"><span data-stu-id="eaa4d-151">Configure Webpack and TypeScript</span></span>

<span data-ttu-id="eaa4d-152">Poniższe kroki konfigurują konwersję kodu TypeScript na język JavaScript i łączenie zasobów po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-152">The following steps configure the conversion of TypeScript to JavaScript and the bundling of client-side resources.</span></span>

1. <span data-ttu-id="eaa4d-153">Uruchom następujące polecenie w katalogu głównym projektu, aby utworzyć plik *package.json:*</span><span class="sxs-lookup"><span data-stu-id="eaa4d-153">Run the following command in the project root to create a *package.json* file:</span></span>

    ```console
    npm init -y
    ```

1. <span data-ttu-id="eaa4d-154">Dodaj wyróżnioną właściwość do pliku *package.json* i zapisz zmiany pliku:</span><span class="sxs-lookup"><span data-stu-id="eaa4d-154">Add the highlighted property to the *package.json* file and save the file changes:</span></span>

    [!code-json[package.json](signalr-typescript-webpack/sample/3.x/snippets/package1.json?highlight=4)]

    <span data-ttu-id="eaa4d-155">Ustawienie `private` właściwości, `true` aby zapobiec ostrzeżenia instalacji pakietu w następnym kroku.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-155">Setting the `private` property to `true` prevents package installation warnings in the next step.</span></span>

1. <span data-ttu-id="eaa4d-156">Zainstaluj wymagane pakiety npm.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-156">Install the required npm packages.</span></span> <span data-ttu-id="eaa4d-157">Uruchom następujące polecenie z poziomu głównego projektu:</span><span class="sxs-lookup"><span data-stu-id="eaa4d-157">Run the following command from the project root:</span></span>

    ```console
    npm i -D -E clean-webpack-plugin@3.0.0 css-loader@3.4.2 html-webpack-plugin@3.2.0 mini-css-extract-plugin@0.9.0 ts-loader@6.2.1 typescript@3.7.5 webpack@4.41.5 webpack-cli@3.3.10
    ```

    <span data-ttu-id="eaa4d-158">Niektóre szczegóły polecenia, aby pamiętać:</span><span class="sxs-lookup"><span data-stu-id="eaa4d-158">Some command details to note:</span></span>

    * <span data-ttu-id="eaa4d-159">Numer wersji jest `@` zgodny ze znakiem dla każdej nazwy pakietu.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-159">A version number follows the `@` sign for each package name.</span></span> <span data-ttu-id="eaa4d-160">npm instaluje te konkretne wersje pakietów.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-160">npm installs those specific package versions.</span></span>
    * <span data-ttu-id="eaa4d-161">Opcja `-E` wyłącza domyślne zachowanie npm podczas pisania operatorów zakresu [przechowywania wersji semantycznych](https://semver.org/) do *pliku package.json*.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-161">The `-E` option disables npm's default behavior of writing [semantic versioning](https://semver.org/) range operators to *package.json*.</span></span> <span data-ttu-id="eaa4d-162">Na przykład, `"webpack": "4.41.5"` jest używany `"webpack": "^4.41.5"`zamiast .</span><span class="sxs-lookup"><span data-stu-id="eaa4d-162">For example, `"webpack": "4.41.5"` is used instead of `"webpack": "^4.41.5"`.</span></span> <span data-ttu-id="eaa4d-163">Ta opcja zapobiega niezamierzonej aktualizacji do nowszych wersji pakietu.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-163">This option prevents unintended upgrades to newer package versions.</span></span>

    <span data-ttu-id="eaa4d-164">Aby uzyskać więcej informacji, zobacz dokumenty [npm-install.](https://docs.npmjs.com/cli/install)</span><span class="sxs-lookup"><span data-stu-id="eaa4d-164">See the [npm-install](https://docs.npmjs.com/cli/install) docs for more detail.</span></span>

1. <span data-ttu-id="eaa4d-165">Zastąp `scripts` właściwość pliku *package.json* następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="eaa4d-165">Replace the `scripts` property of the *package.json* file with the following code:</span></span>

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    <span data-ttu-id="eaa4d-166">Niektóre wyjaśnienie skryptów:</span><span class="sxs-lookup"><span data-stu-id="eaa4d-166">Some explanation of the scripts:</span></span>

    * <span data-ttu-id="eaa4d-167">`build`: Łączy zasoby po stronie klienta w trybie rozwoju i obserwuje zmiany w plikach.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-167">`build`: Bundles the client-side resources in development mode and watches for file changes.</span></span> <span data-ttu-id="eaa4d-168">Obserwator plików powoduje, że pakiet do ponownego generowania za każdym razem, gdy zmienia się plik projektu.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-168">The file watcher causes the bundle to regenerate each time a project file changes.</span></span> <span data-ttu-id="eaa4d-169">Opcja `mode` wyłącza optymalizacje produkcji, takie jak potrząsanie drzewem i minyfikacja.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-169">The `mode` option disables production optimizations, such as tree shaking and minification.</span></span> <span data-ttu-id="eaa4d-170">Używaj `build` tylko w rozwoju.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-170">Only use `build` in development.</span></span>
    * <span data-ttu-id="eaa4d-171">`release`: Łączy zasoby po stronie klienta w trybie produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-171">`release`: Bundles the client-side resources in production mode.</span></span>
    * <span data-ttu-id="eaa4d-172">`publish`: Uruchamia `release` skrypt w celu wiązania zasobów po stronie klienta w trybie produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-172">`publish`: Runs the `release` script to bundle the client-side resources in production mode.</span></span> <span data-ttu-id="eaa4d-173">Wywołuje polecenie [publikowania](/dotnet/core/tools/dotnet-publish) interfejsu wiersza polecenia .NET Core, aby opublikować aplikację.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-173">It calls the .NET Core CLI's [publish](/dotnet/core/tools/dotnet-publish) command to publish the app.</span></span>

1. <span data-ttu-id="eaa4d-174">Utwórz plik o nazwie *webpack.config.js*, w katalogu głównym projektu, z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="eaa4d-174">Create a file named *webpack.config.js*, in the project root, with the following code:</span></span>

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/3.x/webpack.config.js)]

    <span data-ttu-id="eaa4d-175">Poprzedni plik konfiguruje kompilację webpacku.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-175">The preceding file configures the Webpack compilation.</span></span> <span data-ttu-id="eaa4d-176">Niektóre szczegóły konfiguracji, aby pamiętać:</span><span class="sxs-lookup"><span data-stu-id="eaa4d-176">Some configuration details to note:</span></span>

    * <span data-ttu-id="eaa4d-177">Właściwość `output` zastępuje domyślną wartość *dist*.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-177">The `output` property overrides the default value of *dist*.</span></span> <span data-ttu-id="eaa4d-178">Pakiet jest zamiast emitowane w katalogu *wwwroot.*</span><span class="sxs-lookup"><span data-stu-id="eaa4d-178">The bundle is instead emitted in the *wwwroot* directory.</span></span>
    * <span data-ttu-id="eaa4d-179">Tablica `resolve.extensions` zawiera *plik .js* do importowania javascript klienta SignalR.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-179">The `resolve.extensions` array includes *.js* to import the SignalR client JavaScript.</span></span>

1. <span data-ttu-id="eaa4d-180">Utwórz nowy katalog *src* w katalogu głównym projektu, aby przechowywać zasoby po stronie klienta projektu.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-180">Create a new *src* directory in the project root to store the project's client-side assets.</span></span>

1. <span data-ttu-id="eaa4d-181">Utwórz *src/index.html* za pomocą następujących znaczników.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-181">Create *src/index.html* with the following markup.</span></span>

    [!code-html[index.html](signalr-typescript-webpack/sample/3.x/src/index.html)]

    <span data-ttu-id="eaa4d-182">Poprzedni kod HTML definiuje znaczniki standardowego strony głównej.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-182">The preceding HTML defines the homepage's boilerplate markup.</span></span>

1. <span data-ttu-id="eaa4d-183">Utwórz nowy katalog *src/css.*</span><span class="sxs-lookup"><span data-stu-id="eaa4d-183">Create a new *src/css* directory.</span></span> <span data-ttu-id="eaa4d-184">Jego celem jest przechowywanie plików *.css* projektu.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-184">Its purpose is to store the project's *.css* files.</span></span>

1. <span data-ttu-id="eaa4d-185">Utwórz *src/css/main.css* z następującymi CSS:</span><span class="sxs-lookup"><span data-stu-id="eaa4d-185">Create *src/css/main.css* with the following CSS:</span></span>

    [!code-css[main.css](signalr-typescript-webpack/sample/3.x/src/css/main.css)]

    <span data-ttu-id="eaa4d-186">Poprzedni plik *main.css* stylizuje aplikację.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-186">The preceding *main.css* file styles the app.</span></span>

1. <span data-ttu-id="eaa4d-187">Utwórz *src/tsconfig.json* z następującym JSON:</span><span class="sxs-lookup"><span data-stu-id="eaa4d-187">Create *src/tsconfig.json* with the following JSON:</span></span>

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/3.x/src/tsconfig.json)]

    <span data-ttu-id="eaa4d-188">Powyższy kod konfiguruje kompilator TypeScript do tworzenia kodu JavaScript zgodnego z [kodem ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-188">The preceding code configures the TypeScript compiler to produce [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5-compatible JavaScript.</span></span>

1. <span data-ttu-id="eaa4d-189">Utwórz *src/index.ts* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="eaa4d-189">Create *src/index.ts* with the following code:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    <span data-ttu-id="eaa4d-190">Poprzedni kod TypeScript pobiera odwołania do elementów DOM i dołącza dwa programy obsługi zdarzeń:</span><span class="sxs-lookup"><span data-stu-id="eaa4d-190">The preceding TypeScript retrieves references to DOM elements and attaches two event handlers:</span></span>

    * <span data-ttu-id="eaa4d-191">`keyup`: To zdarzenie jest uruchamiane, gdy użytkownik wpisuje pole tekstowe. `tbMessage`</span><span class="sxs-lookup"><span data-stu-id="eaa4d-191">`keyup`: This event fires when the user types in the `tbMessage`textbox.</span></span> <span data-ttu-id="eaa4d-192">Funkcja `send` jest wywoływana, gdy użytkownik naciśnie klawisz **Enter.**</span><span class="sxs-lookup"><span data-stu-id="eaa4d-192">The `send` function is called when the user presses the **Enter** key.</span></span>
    * <span data-ttu-id="eaa4d-193">`click`: To zdarzenie jest uruchamiane, gdy użytkownik kliknie przycisk **Wyślij.**</span><span class="sxs-lookup"><span data-stu-id="eaa4d-193">`click`: This event fires when the user clicks the **Send** button.</span></span> <span data-ttu-id="eaa4d-194">Wywołanie funkcji `send`.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-194">The `send` function is called.</span></span>

## <a name="configure-the-app"></a><span data-ttu-id="eaa4d-195">Konfigurowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="eaa4d-195">Configure the app</span></span>

1. <span data-ttu-id="eaa4d-196">W `Startup.Configure`, dodaj wywołania [usedefaultfiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) i [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="eaa4d-196">In `Startup.Configure`, add calls to [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span>

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseStaticDefaultFiles&highlight=9-10)]

   <span data-ttu-id="eaa4d-197">Powyższy kod umożliwia serwerowi zlokalizowanie i obsługa pliku *index.html.*</span><span class="sxs-lookup"><span data-stu-id="eaa4d-197">The preceding code allows the server to locate and serve the *index.html* file.</span></span>  <span data-ttu-id="eaa4d-198">Plik jest obsługiwany, czy użytkownik wprowadzi pełny adres URL lub główny adres URL aplikacji internetowej.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-198">The file is served whether the user enters its full URL or the root URL of the web app.</span></span>

1. <span data-ttu-id="eaa4d-199">Na końcu `Startup.Configure`, map */hub* trasy `ChatHub` do koncentratora.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-199">At the end of `Startup.Configure`, map a */hub* route to the `ChatHub` hub.</span></span> <span data-ttu-id="eaa4d-200">Zastąp kod, który wyświetla *Hello World!*</span><span class="sxs-lookup"><span data-stu-id="eaa4d-200">Replace the code that displays *Hello World!*</span></span> <span data-ttu-id="eaa4d-201">z następującą linią:</span><span class="sxs-lookup"><span data-stu-id="eaa4d-201">with the following line:</span></span> 

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseSignalR&highlight=3)]

1. <span data-ttu-id="eaa4d-202">W `Startup.ConfigureServices`, zadzwoń [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_).</span><span class="sxs-lookup"><span data-stu-id="eaa4d-202">In `Startup.ConfigureServices`, call [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_).</span></span>

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_AddSignalR)]

1. <span data-ttu-id="eaa4d-203">Utwórz nowy katalog o nazwie *Hubs* w katalogu głównym projektu *SignalRWebPack/* do przechowywania centrum SignalR.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-203">Create a new directory named *Hubs* in the project root *SignalRWebPack/* to store the SignalR hub.</span></span>

1. <span data-ttu-id="eaa4d-204">Utwórz centrum *hubs/ChatHub.cs* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="eaa4d-204">Create hub *Hubs/ChatHub.cs* with the following code:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. <span data-ttu-id="eaa4d-205">Dodaj następującą `using` instrukcję w górnej części pliku *Startup.cs,* aby rozwiązać `ChatHub` odwołanie:</span><span class="sxs-lookup"><span data-stu-id="eaa4d-205">Add the following `using` statement at the top of the *Startup.cs* file to resolve the `ChatHub` reference:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a><span data-ttu-id="eaa4d-206">Włączanie komunikacji z klientem i serwerem</span><span class="sxs-lookup"><span data-stu-id="eaa4d-206">Enable client and server communication</span></span>

<span data-ttu-id="eaa4d-207">Aplikacja wyświetla obecnie podstawowy formularz do wysyłania wiadomości, ale nie jest jeszcze funkcjonalny.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-207">The app currently displays a basic form to send messages, but is not yet functional.</span></span> <span data-ttu-id="eaa4d-208">Serwer nasłuchuje określonej trasy, ale nic nie robi z wysłanymi wiadomościami.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-208">The server is listening to a specific route but does nothing with sent messages.</span></span>

1. <span data-ttu-id="eaa4d-209">Uruchom następujące polecenie w katalogu głównym projektu:</span><span class="sxs-lookup"><span data-stu-id="eaa4d-209">Run the following command at the project root:</span></span>

    ```console
    npm i @microsoft/signalr @types/node
    ```

    <span data-ttu-id="eaa4d-210">Poprzednie polecenie instaluje:</span><span class="sxs-lookup"><span data-stu-id="eaa4d-210">The preceding command installs:</span></span>

     * <span data-ttu-id="eaa4d-211">[Klient SignalR TypeScript](https://www.npmjs.com/package/@microsoft/signalr), który umożliwia klientowi wysyłanie wiadomości do serwera.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-211">The [SignalR TypeScript client](https://www.npmjs.com/package/@microsoft/signalr), which allows the client to send messages to the server.</span></span>
     * <span data-ttu-id="eaa4d-212">Definicje typów TypeScript dla node.js, który umożliwia sprawdzanie w czasie kompilacji typów Node.js.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-212">The TypeScript type definitions for Node.js, which enables compile-time checking of Node.js types.</span></span>

1. <span data-ttu-id="eaa4d-213">Dodaj wyróżniony kod do pliku *src/index.ts:*</span><span class="sxs-lookup"><span data-stu-id="eaa4d-213">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    <span data-ttu-id="eaa4d-214">Poprzedni kod obsługuje odbieranie wiadomości z serwera.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-214">The preceding code supports receiving messages from the server.</span></span> <span data-ttu-id="eaa4d-215">Klasa `HubConnectionBuilder` tworzy nowego konstruktora do konfigurowania połączenia z serwerem.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-215">The `HubConnectionBuilder` class creates a new builder for configuring the server connection.</span></span> <span data-ttu-id="eaa4d-216">Funkcja `withUrl` konfiguruje adres URL centrum.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-216">The `withUrl` function configures the hub URL.</span></span>

    <span data-ttu-id="eaa4d-217">SignalR umożliwia wymianę wiadomości między klientem a serwerem.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-217">SignalR enables the exchange of messages between a client and a server.</span></span> <span data-ttu-id="eaa4d-218">Każda wiadomość ma określoną nazwę.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-218">Each message has a specific name.</span></span> <span data-ttu-id="eaa4d-219">Na przykład wiadomości o `messageReceived` nazwie można uruchomić logikę odpowiedzialną za wyświetlanie nowej wiadomości w strefie wiadomości.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-219">For example, messages with the name `messageReceived` can run the logic responsible for displaying the new message in the messages zone.</span></span> <span data-ttu-id="eaa4d-220">Nasłuchiwanie określonej wiadomości `on` można wykonać za pomocą funkcji.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-220">Listening to a specific message can be done via the `on` function.</span></span> <span data-ttu-id="eaa4d-221">Można odsłuchać dowolną liczbę nazw wiadomości.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-221">Any number of message names can be listened to.</span></span> <span data-ttu-id="eaa4d-222">Możliwe jest również przekazywanie parametrów do wiadomości, takich jak imię i nazwisko autora oraz zawartość odebranej wiadomości.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-222">It's also possible to pass parameters to the message, such as the author's name and the content of the message received.</span></span> <span data-ttu-id="eaa4d-223">Po odebraniu wiadomości przez `div` klienta tworzony jest nowy element z nazwą `innerHTML` autora i zawartością wiadomości w jego atrybucie.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-223">Once the client receives a message, a new `div` element is created with the author's name and the message content in its `innerHTML` attribute.</span></span> <span data-ttu-id="eaa4d-224">Jest dodawany do `div` głównego elementu wyświetlającego komunikaty.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-224">It's added to the main `div` element displaying the messages.</span></span>

1. <span data-ttu-id="eaa4d-225">Teraz, gdy klient może odbierać wiadomość, skonfiguruj ją do wysyłania wiadomości.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-225">Now that the client can receive a message, configure it to send messages.</span></span> <span data-ttu-id="eaa4d-226">Dodaj wyróżniony kod do pliku *src/index.ts:*</span><span class="sxs-lookup"><span data-stu-id="eaa4d-226">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/src/index.ts?highlight=34-35)]

    <span data-ttu-id="eaa4d-227">Wysyłanie wiadomości za pośrednictwem połączenia WebSockets wymaga wywołania `send` metody.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-227">Sending a message through the WebSockets connection requires calling the `send` method.</span></span> <span data-ttu-id="eaa4d-228">Pierwszym parametrem metody jest nazwa wiadomości.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-228">The method's first parameter is the message name.</span></span> <span data-ttu-id="eaa4d-229">Dane wiadomości zamieszkuje inne parametry.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-229">The message data inhabits the other parameters.</span></span> <span data-ttu-id="eaa4d-230">W tym przykładzie komunikat `newMessage` zidentyfikowany jako jest wysyłany do serwera.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-230">In this example, a message identified as `newMessage` is sent to the server.</span></span> <span data-ttu-id="eaa4d-231">Wiadomość składa się z nazwy użytkownika i danych wejściowych użytkownika z pola tekstowego.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-231">The message consists of the username and the user input from a text box.</span></span> <span data-ttu-id="eaa4d-232">Jeśli wiadomość działa, wartość pola tekstowego jest wyczyszczona.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-232">If the send works, the text box value is cleared.</span></span>

1. <span data-ttu-id="eaa4d-233">Dodaj metodę `NewMessage` do klasy `ChatHub`:</span><span class="sxs-lookup"><span data-stu-id="eaa4d-233">Add the `NewMessage` method to the `ChatHub` class:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/Hubs/ChatHub.cs?highlight=8-11)]

    <span data-ttu-id="eaa4d-234">Poprzedni kod emituje odebrane wiadomości do wszystkich połączonych użytkowników po serwer otrzyma je.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-234">The preceding code broadcasts received messages to all connected users once the server receives them.</span></span> <span data-ttu-id="eaa4d-235">Nie ma potrzeby, aby `on` mieć ogólną metodę, aby odbierać wszystkie wiadomości.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-235">It's unnecessary to have a generic `on` method to receive all the messages.</span></span> <span data-ttu-id="eaa4d-236">Metoda nazwana nazwą wiadomości wystarczy.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-236">A method named after the message name suffices.</span></span>

    <span data-ttu-id="eaa4d-237">W tym przykładzie klient TypeScript wysyła `newMessage`wiadomość zidentyfikowaną jako .</span><span class="sxs-lookup"><span data-stu-id="eaa4d-237">In this example, the TypeScript client sends a message identified as `newMessage`.</span></span> <span data-ttu-id="eaa4d-238">Metoda C# `NewMessage` oczekuje danych wysyłanych przez klienta.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-238">The C# `NewMessage` method expects the data sent by the client.</span></span> <span data-ttu-id="eaa4d-239">Połączenie jest nawiązywać do [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) na [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span><span class="sxs-lookup"><span data-stu-id="eaa4d-239">A call is made to [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) on [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span></span> <span data-ttu-id="eaa4d-240">Odebrane wiadomości są wysyłane do wszystkich klientów połączonych z koncentratorem.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-240">The received messages are sent to all clients connected to the hub.</span></span>

## <a name="test-the-app"></a><span data-ttu-id="eaa4d-241">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="eaa4d-241">Test the app</span></span>

<span data-ttu-id="eaa4d-242">Upewnij się, że aplikacja działa z następującymi krokami.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-242">Confirm that the app works with the following steps.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="eaa4d-243">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eaa4d-243">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="eaa4d-244">Uruchom pakiet Webpack w trybie *wydania.*</span><span class="sxs-lookup"><span data-stu-id="eaa4d-244">Run Webpack in *release* mode.</span></span> <span data-ttu-id="eaa4d-245">Korzystając z okna **Konsola Menedżera pakietów,** uruchom następujące polecenie w katalogu głównym projektu.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-245">Using the **Package Manager Console** window, run the following command in the project root.</span></span> <span data-ttu-id="eaa4d-246">Jeśli nie znajdujesz się w `cd SignalRWebPack` katalogu głównym projektu, wprowadź przed wprowadzeniem polecenia.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-246">If you are not in the project root, enter `cd SignalRWebPack` before entering the command.</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="eaa4d-247">Wybierz **debugowanie** > **start bez debugowania,** aby uruchomić aplikację w przeglądarce bez dołączania debugera.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-247">Select **Debug** > **Start without debugging** to launch the app in a browser without attaching the debugger.</span></span> <span data-ttu-id="eaa4d-248">Plik *wwwroot/index.html* jest obsługiwany w `http://localhost:<port_number>`pliku .</span><span class="sxs-lookup"><span data-stu-id="eaa4d-248">The *wwwroot/index.html* file is served at `http://localhost:<port_number>`.</span></span>

   <span data-ttu-id="eaa4d-249">Jeśli zostanie wyświetlonych błędów kompilacji, spróbuj zamknąć i ponownie otworzyć rozwiązanie.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-249">If you get compile errors, try closing and reopening the solution.</span></span> 

1. <span data-ttu-id="eaa4d-250">Otwórz inne wystąpienie przeglądarki (dowolną przeglądarkę).</span><span class="sxs-lookup"><span data-stu-id="eaa4d-250">Open another browser instance (any browser).</span></span> <span data-ttu-id="eaa4d-251">Wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-251">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="eaa4d-252">Wybierz jedną z tych opcji przeglądarki, wpisz coś w polu tekstowym **Wiadomość** i kliknij przycisk **Wyślij.**</span><span class="sxs-lookup"><span data-stu-id="eaa4d-252">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="eaa4d-253">Unikatowa nazwa użytkownika i komunikat są natychmiast wyświetlane na obu stronach.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-253">The unique user name and message are displayed on both pages instantly.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="eaa4d-254">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eaa4d-254">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="eaa4d-255">Uruchom pakiet Webpack w trybie *wydania,* wykonując następujące polecenie w katalogu głównym projektu:</span><span class="sxs-lookup"><span data-stu-id="eaa4d-255">Run Webpack in *release* mode by executing the following command in the project root:</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="eaa4d-256">Skompiluj i uruchom aplikację, wykonując następujące polecenie w katalogu głównym projektu:</span><span class="sxs-lookup"><span data-stu-id="eaa4d-256">Build and run the app by executing the following command in the project root:</span></span>

    ```dotnetcli
    dotnet run
    ```

    <span data-ttu-id="eaa4d-257">Serwer sieci web uruchamia aplikację i udostępnia ją na localhost.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-257">The web server starts the app and makes it available on localhost.</span></span>

1. <span data-ttu-id="eaa4d-258">Otwórz przeglądarkę, aby . `http://localhost:<port_number>`</span><span class="sxs-lookup"><span data-stu-id="eaa4d-258">Open a browser to `http://localhost:<port_number>`.</span></span> <span data-ttu-id="eaa4d-259">Zostanie podany plik *wwwroot/index.html.*</span><span class="sxs-lookup"><span data-stu-id="eaa4d-259">The *wwwroot/index.html* file is served.</span></span> <span data-ttu-id="eaa4d-260">Skopiuj adres URL z paska adresu.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-260">Copy the URL from the address bar.</span></span>

1. <span data-ttu-id="eaa4d-261">Otwórz inne wystąpienie przeglądarki (dowolną przeglądarkę).</span><span class="sxs-lookup"><span data-stu-id="eaa4d-261">Open another browser instance (any browser).</span></span> <span data-ttu-id="eaa4d-262">Wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-262">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="eaa4d-263">Wybierz jedną z tych opcji przeglądarki, wpisz coś w polu tekstowym **Wiadomość** i kliknij przycisk **Wyślij.**</span><span class="sxs-lookup"><span data-stu-id="eaa4d-263">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="eaa4d-264">Unikatowa nazwa użytkownika i komunikat są natychmiast wyświetlane na obu stronach.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-264">The unique user name and message are displayed on both pages instantly.</span></span>

---

![komunikat wyświetlany w obu oknach przeglądarki](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="eaa4d-266">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="eaa4d-266">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="eaa4d-267">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eaa4d-267">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="eaa4d-268">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) z **obciążeniem ASP.NET i tworzenia stron internetowych**</span><span class="sxs-lookup"><span data-stu-id="eaa4d-268">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="eaa4d-269">.NET Core SDK 2.2 lub nowsza</span><span class="sxs-lookup"><span data-stu-id="eaa4d-269">.NET Core SDK 2.2 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="eaa4d-270">[Node.js](https://nodejs.org/) z [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="eaa4d-270">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="eaa4d-271">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eaa4d-271">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="eaa4d-272">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eaa4d-272">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="eaa4d-273">.NET Core SDK 2.2 lub nowsza</span><span class="sxs-lookup"><span data-stu-id="eaa4d-273">.NET Core SDK 2.2 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="eaa4d-274">C# dla programu Visual Studio Code w wersji 1.17.1 lub nowszej</span><span class="sxs-lookup"><span data-stu-id="eaa4d-274">C# for Visual Studio Code version 1.17.1 or later</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* <span data-ttu-id="eaa4d-275">[Node.js](https://nodejs.org/) z [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="eaa4d-275">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

---

## <a name="create-the-aspnet-core-web-app"></a><span data-ttu-id="eaa4d-276">Tworzenie aplikacji sieci Web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="eaa4d-276">Create the ASP.NET Core web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="eaa4d-277">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eaa4d-277">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="eaa4d-278">Skonfiguruj program Visual Studio, aby wyszukywać npm w zmiennej środowiskowej *PATH.*</span><span class="sxs-lookup"><span data-stu-id="eaa4d-278">Configure Visual Studio to look for npm in the *PATH* environment variable.</span></span> <span data-ttu-id="eaa4d-279">Domyślnie program Visual Studio używa wersji npm znalezionej w katalogu instalacyjnym.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-279">By default, Visual Studio uses the version of npm found in its installation directory.</span></span> <span data-ttu-id="eaa4d-280">Postępuj zgodnie z poniższymi instrukcjami w programie Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="eaa4d-280">Follow these instructions in Visual Studio:</span></span>

1. <span data-ttu-id="eaa4d-281">Przejdź do **pozycji** > **Opcje** > narzędzi Projekty i rozwiązania **Zarządzania pakietami** > **Projects and Solutions** > sieci Web Zewnętrzne narzędzia sieci **Web**.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-281">Navigate to **Tools** > **Options** > **Projects and Solutions** > **Web Package Management** > **External Web Tools**.</span></span>
1. <span data-ttu-id="eaa4d-282">Wybierz wpis *$(PATH)* z listy.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-282">Select the *$(PATH)* entry from the list.</span></span> <span data-ttu-id="eaa4d-283">Kliknij strzałkę w górę, aby przenieść wpis na drugą pozycję na liście.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-283">Click the up arrow to move the entry to the second position in the list.</span></span>

    ![Konfiguracja programu Visual Studio](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

<span data-ttu-id="eaa4d-285">Konfiguracja programu Visual Studio została ukończona.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-285">Visual Studio configuration is completed.</span></span> <span data-ttu-id="eaa4d-286">Nadszedł czas, aby utworzyć projekt.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-286">It's time to create the project.</span></span>

1. <span data-ttu-id="eaa4d-287">Użyj opcji menu **Plik** > **nowego** > **projektu** i wybierz szablon ASP.NET Core **Web Application.**</span><span class="sxs-lookup"><span data-stu-id="eaa4d-287">Use the **File** > **New** > **Project** menu option and choose the **ASP.NET Core Web Application** template.</span></span>
1. <span data-ttu-id="eaa4d-288">Nazwij projekt *SignalRWebPack*i wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-288">Name the project *SignalRWebPack*, and select **Create**.</span></span>
1. <span data-ttu-id="eaa4d-289">Wybierz *.NET Core* z listy rozwijanej struktury docelowej i wybierz *ASP.NET Core 2.2* z listy rozwijanej selektora platformy.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-289">Select *.NET Core* from the target framework drop-down, and select *ASP.NET Core 2.2* from the framework selector drop-down.</span></span> <span data-ttu-id="eaa4d-290">Zaznacz **pusty** szablon i wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-290">Select the **Empty** template, and select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="eaa4d-291">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eaa4d-291">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="eaa4d-292">Uruchom następujące polecenie w **terminalu zintegrowanym:**</span><span class="sxs-lookup"><span data-stu-id="eaa4d-292">Run the following command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet new web -o SignalRWebPack
```

<span data-ttu-id="eaa4d-293">W katalogu *SignalRWebPack* tworzona jest pusta aplikacja sieci web ASP.NET Core, kierowana na program .NET Core.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-293">An empty ASP.NET Core web app, targeting .NET Core, is created in a *SignalRWebPack* directory.</span></span>

---

## <a name="configure-webpack-and-typescript"></a><span data-ttu-id="eaa4d-294">Konfigurowanie pakietu Web i kodu TypeScript</span><span class="sxs-lookup"><span data-stu-id="eaa4d-294">Configure Webpack and TypeScript</span></span>

<span data-ttu-id="eaa4d-295">Poniższe kroki konfigurują konwersję kodu TypeScript na język JavaScript i łączenie zasobów po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-295">The following steps configure the conversion of TypeScript to JavaScript and the bundling of client-side resources.</span></span>

1. <span data-ttu-id="eaa4d-296">Uruchom następujące polecenie w katalogu głównym projektu, aby utworzyć plik *package.json:*</span><span class="sxs-lookup"><span data-stu-id="eaa4d-296">Run the following command in the project root to create a *package.json* file:</span></span>

    ```console
    npm init -y
    ```

1. <span data-ttu-id="eaa4d-297">Dodaj wyróżnioną właściwość do pliku *package.json:*</span><span class="sxs-lookup"><span data-stu-id="eaa4d-297">Add the highlighted property to the *package.json* file:</span></span>

    [!code-json[package.json](signalr-typescript-webpack/sample/2.x/snippets/package1.json?highlight=4)]

    <span data-ttu-id="eaa4d-298">Ustawienie `private` właściwości, `true` aby zapobiec ostrzeżenia instalacji pakietu w następnym kroku.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-298">Setting the `private` property to `true` prevents package installation warnings in the next step.</span></span>

1. <span data-ttu-id="eaa4d-299">Zainstaluj wymagane pakiety npm.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-299">Install the required npm packages.</span></span> <span data-ttu-id="eaa4d-300">Uruchom następujące polecenie z poziomu głównego projektu:</span><span class="sxs-lookup"><span data-stu-id="eaa4d-300">Run the following command from the project root:</span></span>

    ```console
    npm install -D -E clean-webpack-plugin@1.0.1 css-loader@2.1.0 html-webpack-plugin@4.0.0-beta.5 mini-css-extract-plugin@0.5.0 ts-loader@5.3.3 typescript@3.3.3 webpack@4.29.3 webpack-cli@3.2.3
    ```

    <span data-ttu-id="eaa4d-301">Niektóre szczegóły polecenia, aby pamiętać:</span><span class="sxs-lookup"><span data-stu-id="eaa4d-301">Some command details to note:</span></span>

    * <span data-ttu-id="eaa4d-302">Numer wersji jest `@` zgodny ze znakiem dla każdej nazwy pakietu.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-302">A version number follows the `@` sign for each package name.</span></span> <span data-ttu-id="eaa4d-303">npm instaluje te konkretne wersje pakietów.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-303">npm installs those specific package versions.</span></span>
    * <span data-ttu-id="eaa4d-304">Opcja `-E` wyłącza domyślne zachowanie npm podczas pisania operatorów zakresu [przechowywania wersji semantycznych](https://semver.org/) do *pliku package.json*.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-304">The `-E` option disables npm's default behavior of writing [semantic versioning](https://semver.org/) range operators to *package.json*.</span></span> <span data-ttu-id="eaa4d-305">Na przykład, `"webpack": "4.29.3"` jest używany `"webpack": "^4.29.3"`zamiast .</span><span class="sxs-lookup"><span data-stu-id="eaa4d-305">For example, `"webpack": "4.29.3"` is used instead of `"webpack": "^4.29.3"`.</span></span> <span data-ttu-id="eaa4d-306">Ta opcja zapobiega niezamierzonej aktualizacji do nowszych wersji pakietu.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-306">This option prevents unintended upgrades to newer package versions.</span></span>

    <span data-ttu-id="eaa4d-307">Aby uzyskać więcej informacji, zobacz dokumenty [npm-install.](https://docs.npmjs.com/cli/install)</span><span class="sxs-lookup"><span data-stu-id="eaa4d-307">See the [npm-install](https://docs.npmjs.com/cli/install) docs for more detail.</span></span>

1. <span data-ttu-id="eaa4d-308">Zastąp `scripts` właściwość pliku *package.json* następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="eaa4d-308">Replace the `scripts` property of the *package.json* file with the following code:</span></span>

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    <span data-ttu-id="eaa4d-309">Niektóre wyjaśnienie skryptów:</span><span class="sxs-lookup"><span data-stu-id="eaa4d-309">Some explanation of the scripts:</span></span>

    * <span data-ttu-id="eaa4d-310">`build`: Łączy zasoby po stronie klienta w trybie rozwoju i obserwuje zmiany w plikach.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-310">`build`: Bundles the client-side resources in development mode and watches for file changes.</span></span> <span data-ttu-id="eaa4d-311">Obserwator plików powoduje, że pakiet do ponownego generowania za każdym razem, gdy zmienia się plik projektu.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-311">The file watcher causes the bundle to regenerate each time a project file changes.</span></span> <span data-ttu-id="eaa4d-312">Opcja `mode` wyłącza optymalizacje produkcji, takie jak potrząsanie drzewem i minyfikacja.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-312">The `mode` option disables production optimizations, such as tree shaking and minification.</span></span> <span data-ttu-id="eaa4d-313">Używaj `build` tylko w rozwoju.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-313">Only use `build` in development.</span></span>
    * <span data-ttu-id="eaa4d-314">`release`: Łączy zasoby po stronie klienta w trybie produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-314">`release`: Bundles the client-side resources in production mode.</span></span>
    * <span data-ttu-id="eaa4d-315">`publish`: Uruchamia `release` skrypt w celu wiązania zasobów po stronie klienta w trybie produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-315">`publish`: Runs the `release` script to bundle the client-side resources in production mode.</span></span> <span data-ttu-id="eaa4d-316">Wywołuje polecenie [publikowania](/dotnet/core/tools/dotnet-publish) interfejsu wiersza polecenia .NET Core, aby opublikować aplikację.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-316">It calls the .NET Core CLI's [publish](/dotnet/core/tools/dotnet-publish) command to publish the app.</span></span>

1. <span data-ttu-id="eaa4d-317">Utwórz plik o nazwie *webpack.config.js* w katalogu głównym projektu z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="eaa4d-317">Create a file named *webpack.config.js* in the project root, with the following code:</span></span>

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/2.x/webpack.config.js)]

    <span data-ttu-id="eaa4d-318">Poprzedni plik konfiguruje kompilację webpacku.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-318">The preceding file configures the Webpack compilation.</span></span> <span data-ttu-id="eaa4d-319">Niektóre szczegóły konfiguracji, aby pamiętać:</span><span class="sxs-lookup"><span data-stu-id="eaa4d-319">Some configuration details to note:</span></span>

    * <span data-ttu-id="eaa4d-320">Właściwość `output` zastępuje domyślną wartość *dist*.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-320">The `output` property overrides the default value of *dist*.</span></span> <span data-ttu-id="eaa4d-321">Pakiet jest zamiast emitowane w katalogu *wwwroot.*</span><span class="sxs-lookup"><span data-stu-id="eaa4d-321">The bundle is instead emitted in the *wwwroot* directory.</span></span>
    * <span data-ttu-id="eaa4d-322">Tablica `resolve.extensions` zawiera *plik .js* do importowania javascript klienta SignalR.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-322">The `resolve.extensions` array includes *.js* to import the SignalR client JavaScript.</span></span>

1. <span data-ttu-id="eaa4d-323">Utwórz nowy katalog *src* w katalogu głównym projektu, aby przechowywać zasoby po stronie klienta projektu.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-323">Create a new *src* directory in the project root to store the project's client-side assets.</span></span>

1. <span data-ttu-id="eaa4d-324">Utwórz *src/index.html* za pomocą następujących znaczników.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-324">Create *src/index.html* with the following markup.</span></span>

    [!code-html[index.html](signalr-typescript-webpack/sample/2.x/src/index.html)]

    <span data-ttu-id="eaa4d-325">Poprzedni kod HTML definiuje znaczniki standardowego strony głównej.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-325">The preceding HTML defines the homepage's boilerplate markup.</span></span>

1. <span data-ttu-id="eaa4d-326">Utwórz nowy katalog *src/css.*</span><span class="sxs-lookup"><span data-stu-id="eaa4d-326">Create a new *src/css* directory.</span></span> <span data-ttu-id="eaa4d-327">Jego celem jest przechowywanie plików *.css* projektu.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-327">Its purpose is to store the project's *.css* files.</span></span>

1. <span data-ttu-id="eaa4d-328">Utwórz *src/css/main.css* z następującymi znacznikami:</span><span class="sxs-lookup"><span data-stu-id="eaa4d-328">Create *src/css/main.css* with the following markup:</span></span>

    [!code-css[main.css](signalr-typescript-webpack/sample/2.x/src/css/main.css)]

    <span data-ttu-id="eaa4d-329">Poprzedni plik *main.css* stylizuje aplikację.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-329">The preceding *main.css* file styles the app.</span></span>

1. <span data-ttu-id="eaa4d-330">Utwórz *src/tsconfig.json* z następującym JSON:</span><span class="sxs-lookup"><span data-stu-id="eaa4d-330">Create *src/tsconfig.json* with the following JSON:</span></span>

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/2.x/src/tsconfig.json)]

    <span data-ttu-id="eaa4d-331">Powyższy kod konfiguruje kompilator TypeScript do tworzenia kodu JavaScript zgodnego z [kodem ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-331">The preceding code configures the TypeScript compiler to produce [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5-compatible JavaScript.</span></span>

1. <span data-ttu-id="eaa4d-332">Utwórz *src/index.ts* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="eaa4d-332">Create *src/index.ts* with the following code:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    <span data-ttu-id="eaa4d-333">Poprzedni kod TypeScript pobiera odwołania do elementów DOM i dołącza dwa programy obsługi zdarzeń:</span><span class="sxs-lookup"><span data-stu-id="eaa4d-333">The preceding TypeScript retrieves references to DOM elements and attaches two event handlers:</span></span>

    * <span data-ttu-id="eaa4d-334">`keyup`: To zdarzenie jest uruchamiane, gdy użytkownik wpisuje pole tekstowe. `tbMessage`</span><span class="sxs-lookup"><span data-stu-id="eaa4d-334">`keyup`: This event fires when the user types in the `tbMessage` textbox.</span></span> <span data-ttu-id="eaa4d-335">Funkcja `send` jest wywoływana, gdy użytkownik naciśnie klawisz **Enter.**</span><span class="sxs-lookup"><span data-stu-id="eaa4d-335">The `send` function is called when the user presses the **Enter** key.</span></span>
    * <span data-ttu-id="eaa4d-336">`click`: To zdarzenie jest uruchamiane, gdy użytkownik kliknie przycisk **Wyślij.**</span><span class="sxs-lookup"><span data-stu-id="eaa4d-336">`click`: This event fires when the user clicks the **Send** button.</span></span> <span data-ttu-id="eaa4d-337">Wywołanie funkcji `send`.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-337">The `send` function is called.</span></span>

## <a name="configure-the-aspnet-core-app"></a><span data-ttu-id="eaa4d-338">Konfigurowanie aplikacji ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="eaa4d-338">Configure the ASP.NET Core app</span></span>

1. <span data-ttu-id="eaa4d-339">Kod podany w `Startup.Configure` metodzie wyświetla *Hello World!*.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-339">The code provided in the `Startup.Configure` method displays *Hello World!*.</span></span> <span data-ttu-id="eaa4d-340">Zastąp wywołanie `app.Run` metody wywołaniem [usedefaultfiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) i [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="eaa4d-340">Replace the `app.Run` method call with calls to [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseStaticDefaultFiles)]

    <span data-ttu-id="eaa4d-341">Powyższy kod umożliwia serwerowi zlokalizowanie i wyświetlenie pliku *index.html,* niezależnie od tego, czy użytkownik wprowadzi pełny adres URL, czy główny adres URL aplikacji internetowej.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-341">The preceding code allows the server to locate and serve the *index.html* file, whether the user enters its full URL or the root URL of the web app.</span></span>

1. <span data-ttu-id="eaa4d-342">Zadzwoń [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) w `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-342">Call [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="eaa4d-343">Dodaje usługi SignalR do projektu.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-343">It adds the SignalR services to the project.</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_AddSignalR)]

1. <span data-ttu-id="eaa4d-344">Mapuj trasę */hub* do `ChatHub` koncentratora.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-344">Map a */hub* route to the `ChatHub` hub.</span></span> <span data-ttu-id="eaa4d-345">Dodaj następujące wiersze na `Startup.Configure`końcu:</span><span class="sxs-lookup"><span data-stu-id="eaa4d-345">Add the following lines at the end of `Startup.Configure`:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseSignalR)]

1. <span data-ttu-id="eaa4d-346">Utwórz nowy katalog, o nazwie *Centra*, w katalogu głównym projektu.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-346">Create a new directory, called *Hubs*, in the project root.</span></span> <span data-ttu-id="eaa4d-347">Jego celem jest przechowywanie koncentratora SignalR, który jest tworzony w następnym kroku.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-347">Its purpose is to store the SignalR hub, which is created in the next step.</span></span>

1. <span data-ttu-id="eaa4d-348">Utwórz centrum *hubs/ChatHub.cs* z następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="eaa4d-348">Create hub *Hubs/ChatHub.cs* with the following code:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. <span data-ttu-id="eaa4d-349">Dodaj następujący kod w górnej części pliku *Startup.cs,* `ChatHub` aby rozwiązać odwołanie:</span><span class="sxs-lookup"><span data-stu-id="eaa4d-349">Add the following code at the top of the *Startup.cs* file to resolve the `ChatHub` reference:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a><span data-ttu-id="eaa4d-350">Włączanie komunikacji z klientem i serwerem</span><span class="sxs-lookup"><span data-stu-id="eaa4d-350">Enable client and server communication</span></span>

<span data-ttu-id="eaa4d-351">Aplikacja wyświetla obecnie prosty formularz do wysyłania wiadomości.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-351">The app currently displays a simple form to send messages.</span></span> <span data-ttu-id="eaa4d-352">Nic się nie dzieje, gdy próbujesz to zrobić.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-352">Nothing happens when you try to do so.</span></span> <span data-ttu-id="eaa4d-353">Serwer nasłuchuje określonej trasy, ale nic nie robi z wysłanymi wiadomościami.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-353">The server is listening to a specific route but does nothing with sent messages.</span></span>

1. <span data-ttu-id="eaa4d-354">Uruchom następujące polecenie w katalogu głównym projektu:</span><span class="sxs-lookup"><span data-stu-id="eaa4d-354">Run the following command at the project root:</span></span>

    ```console
    npm install @aspnet/signalr
    ```

    <span data-ttu-id="eaa4d-355">Poprzednie polecenie instaluje [klienta SignalR TypeScript](https://www.npmjs.com/package/@microsoft/signalr), co umożliwia klientowi wysyłanie wiadomości do serwera.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-355">The preceding command installs the [SignalR TypeScript client](https://www.npmjs.com/package/@microsoft/signalr), which allows the client to send messages to the server.</span></span>

1. <span data-ttu-id="eaa4d-356">Dodaj wyróżniony kod do pliku *src/index.ts:*</span><span class="sxs-lookup"><span data-stu-id="eaa4d-356">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    <span data-ttu-id="eaa4d-357">Poprzedni kod obsługuje odbieranie wiadomości z serwera.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-357">The preceding code supports receiving messages from the server.</span></span> <span data-ttu-id="eaa4d-358">Klasa `HubConnectionBuilder` tworzy nowego konstruktora do konfigurowania połączenia z serwerem.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-358">The `HubConnectionBuilder` class creates a new builder for configuring the server connection.</span></span> <span data-ttu-id="eaa4d-359">Funkcja `withUrl` konfiguruje adres URL centrum.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-359">The `withUrl` function configures the hub URL.</span></span>

    <span data-ttu-id="eaa4d-360">SignalR umożliwia wymianę wiadomości między klientem a serwerem.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-360">SignalR enables the exchange of messages between a client and a server.</span></span> <span data-ttu-id="eaa4d-361">Każda wiadomość ma określoną nazwę.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-361">Each message has a specific name.</span></span> <span data-ttu-id="eaa4d-362">Na przykład wiadomości o `messageReceived` nazwie można uruchomić logikę odpowiedzialną za wyświetlanie nowej wiadomości w strefie wiadomości.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-362">For example, messages with the name `messageReceived` can run the logic responsible for displaying the new message in the messages zone.</span></span> <span data-ttu-id="eaa4d-363">Nasłuchiwanie określonej wiadomości `on` można wykonać za pomocą funkcji.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-363">Listening to a specific message can be done via the `on` function.</span></span> <span data-ttu-id="eaa4d-364">Możesz słuchać dowolnej liczby nazw wiadomości.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-364">You can listen to any number of message names.</span></span> <span data-ttu-id="eaa4d-365">Możliwe jest również przekazywanie parametrów do wiadomości, takich jak imię i nazwisko autora oraz zawartość odebranej wiadomości.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-365">It's also possible to pass parameters to the message, such as the author's name and the content of the message received.</span></span> <span data-ttu-id="eaa4d-366">Po odebraniu wiadomości przez `div` klienta tworzony jest nowy element z nazwą `innerHTML` autora i zawartością wiadomości w jego atrybucie.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-366">Once the client receives a message, a new `div` element is created with the author's name and the message content in its `innerHTML` attribute.</span></span> <span data-ttu-id="eaa4d-367">Nowa wiadomość jest dodawana `div` do głównego elementu wyświetlającego komunikaty.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-367">The new message is added to the main `div` element displaying the messages.</span></span>

1. <span data-ttu-id="eaa4d-368">Teraz, gdy klient może odbierać wiadomość, skonfiguruj ją do wysyłania wiadomości.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-368">Now that the client can receive a message, configure it to send messages.</span></span> <span data-ttu-id="eaa4d-369">Dodaj wyróżniony kod do pliku *src/index.ts:*</span><span class="sxs-lookup"><span data-stu-id="eaa4d-369">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/src/index.ts?highlight=34-35)]

    <span data-ttu-id="eaa4d-370">Wysyłanie wiadomości za pośrednictwem połączenia WebSockets wymaga wywołania `send` metody.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-370">Sending a message through the WebSockets connection requires calling the `send` method.</span></span> <span data-ttu-id="eaa4d-371">Pierwszym parametrem metody jest nazwa wiadomości.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-371">The method's first parameter is the message name.</span></span> <span data-ttu-id="eaa4d-372">Dane wiadomości zamieszkuje inne parametry.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-372">The message data inhabits the other parameters.</span></span> <span data-ttu-id="eaa4d-373">W tym przykładzie komunikat `newMessage` zidentyfikowany jako jest wysyłany do serwera.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-373">In this example, a message identified as `newMessage` is sent to the server.</span></span> <span data-ttu-id="eaa4d-374">Wiadomość składa się z nazwy użytkownika i danych wejściowych użytkownika z pola tekstowego.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-374">The message consists of the username and the user input from a text box.</span></span> <span data-ttu-id="eaa4d-375">Jeśli wiadomość działa, wartość pola tekstowego jest wyczyszczona.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-375">If the send works, the text box value is cleared.</span></span>

1. <span data-ttu-id="eaa4d-376">Dodaj metodę `NewMessage` do klasy `ChatHub`:</span><span class="sxs-lookup"><span data-stu-id="eaa4d-376">Add the `NewMessage` method to the `ChatHub` class:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/Hubs/ChatHub.cs?highlight=8-11)]

    <span data-ttu-id="eaa4d-377">Poprzedni kod emituje odebrane wiadomości do wszystkich połączonych użytkowników po serwer otrzyma je.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-377">The preceding code broadcasts received messages to all connected users once the server receives them.</span></span> <span data-ttu-id="eaa4d-378">Nie ma potrzeby, aby `on` mieć ogólną metodę, aby odbierać wszystkie wiadomości.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-378">It's unnecessary to have a generic `on` method to receive all the messages.</span></span> <span data-ttu-id="eaa4d-379">Metoda nazwana nazwą wiadomości wystarczy.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-379">A method named after the message name suffices.</span></span>

    <span data-ttu-id="eaa4d-380">W tym przykładzie klient TypeScript wysyła `newMessage`wiadomość zidentyfikowaną jako .</span><span class="sxs-lookup"><span data-stu-id="eaa4d-380">In this example, the TypeScript client sends a message identified as `newMessage`.</span></span> <span data-ttu-id="eaa4d-381">Metoda C# `NewMessage` oczekuje danych wysyłanych przez klienta.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-381">The C# `NewMessage` method expects the data sent by the client.</span></span> <span data-ttu-id="eaa4d-382">Połączenie jest nawiązywać do [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) na [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span><span class="sxs-lookup"><span data-stu-id="eaa4d-382">A call is made to [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) on [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span></span> <span data-ttu-id="eaa4d-383">Odebrane wiadomości są wysyłane do wszystkich klientów połączonych z koncentratorem.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-383">The received messages are sent to all clients connected to the hub.</span></span>

## <a name="test-the-app"></a><span data-ttu-id="eaa4d-384">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="eaa4d-384">Test the app</span></span>

<span data-ttu-id="eaa4d-385">Upewnij się, że aplikacja działa z następującymi krokami.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-385">Confirm that the app works with the following steps.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="eaa4d-386">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eaa4d-386">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="eaa4d-387">Uruchom pakiet Webpack w trybie *wydania.*</span><span class="sxs-lookup"><span data-stu-id="eaa4d-387">Run Webpack in *release* mode.</span></span> <span data-ttu-id="eaa4d-388">Korzystając z okna **Konsola Menedżera pakietów,** uruchom następujące polecenie w katalogu głównym projektu.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-388">Using the **Package Manager Console** window, run the following command in the project root.</span></span> <span data-ttu-id="eaa4d-389">Jeśli nie znajdujesz się w `cd SignalRWebPack` katalogu głównym projektu, wprowadź przed wprowadzeniem polecenia.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-389">If you are not in the project root, enter `cd SignalRWebPack` before entering the command.</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="eaa4d-390">Wybierz **debugowanie** > **start bez debugowania,** aby uruchomić aplikację w przeglądarce bez dołączania debugera.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-390">Select **Debug** > **Start without debugging** to launch the app in a browser without attaching the debugger.</span></span> <span data-ttu-id="eaa4d-391">Plik *wwwroot/index.html* jest obsługiwany w `http://localhost:<port_number>`pliku .</span><span class="sxs-lookup"><span data-stu-id="eaa4d-391">The *wwwroot/index.html* file is served at `http://localhost:<port_number>`.</span></span>

1. <span data-ttu-id="eaa4d-392">Otwórz inne wystąpienie przeglądarki (dowolną przeglądarkę).</span><span class="sxs-lookup"><span data-stu-id="eaa4d-392">Open another browser instance (any browser).</span></span> <span data-ttu-id="eaa4d-393">Wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-393">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="eaa4d-394">Wybierz jedną z tych opcji przeglądarki, wpisz coś w polu tekstowym **Wiadomość** i kliknij przycisk **Wyślij.**</span><span class="sxs-lookup"><span data-stu-id="eaa4d-394">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="eaa4d-395">Unikatowa nazwa użytkownika i komunikat są natychmiast wyświetlane na obu stronach.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-395">The unique user name and message are displayed on both pages instantly.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="eaa4d-396">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="eaa4d-396">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="eaa4d-397">Uruchom pakiet Webpack w trybie *wydania,* wykonując następujące polecenie w katalogu głównym projektu:</span><span class="sxs-lookup"><span data-stu-id="eaa4d-397">Run Webpack in *release* mode by executing the following command in the project root:</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="eaa4d-398">Skompiluj i uruchom aplikację, wykonując następujące polecenie w katalogu głównym projektu:</span><span class="sxs-lookup"><span data-stu-id="eaa4d-398">Build and run the app by executing the following command in the project root:</span></span>

    ```dotnetcli
    dotnet run
    ```

    <span data-ttu-id="eaa4d-399">Serwer sieci web uruchamia aplikację i udostępnia ją na localhost.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-399">The web server starts the app and makes it available on localhost.</span></span>

1. <span data-ttu-id="eaa4d-400">Otwórz przeglądarkę, aby . `http://localhost:<port_number>`</span><span class="sxs-lookup"><span data-stu-id="eaa4d-400">Open a browser to `http://localhost:<port_number>`.</span></span> <span data-ttu-id="eaa4d-401">Zostanie podany plik *wwwroot/index.html.*</span><span class="sxs-lookup"><span data-stu-id="eaa4d-401">The *wwwroot/index.html* file is served.</span></span> <span data-ttu-id="eaa4d-402">Skopiuj adres URL z paska adresu.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-402">Copy the URL from the address bar.</span></span>

1. <span data-ttu-id="eaa4d-403">Otwórz inne wystąpienie przeglądarki (dowolną przeglądarkę).</span><span class="sxs-lookup"><span data-stu-id="eaa4d-403">Open another browser instance (any browser).</span></span> <span data-ttu-id="eaa4d-404">Wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-404">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="eaa4d-405">Wybierz jedną z tych opcji przeglądarki, wpisz coś w polu tekstowym **Wiadomość** i kliknij przycisk **Wyślij.**</span><span class="sxs-lookup"><span data-stu-id="eaa4d-405">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="eaa4d-406">Unikatowa nazwa użytkownika i komunikat są natychmiast wyświetlane na obu stronach.</span><span class="sxs-lookup"><span data-stu-id="eaa4d-406">The unique user name and message are displayed on both pages instantly.</span></span>

---

![komunikat wyświetlany w obu oknach przeglądarki](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="eaa4d-408">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="eaa4d-408">Additional resources</span></span>

* <xref:signalr/javascript-client>
* <xref:signalr/hubs>
