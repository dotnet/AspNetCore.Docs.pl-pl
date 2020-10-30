---
title: 'Używanie ASP.NET Core :::no-loc(SignalR)::: z językami TypeScript i WebPack'
author: ssougnez
description: 'W tym samouczku skonfigurujesz pakiet WebPack do tworzenia pakietów i kompilowania ASP.NET Core :::no-loc(SignalR)::: aplikacji sieci Web, której klient został zapisany w języku TypeScript.'
ms.author: bradyg
ms.custom: mvc
ms.date: 02/10/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: tutorials/signalr-typescript-webpack
ms.openlocfilehash: 949276bf4aae33c3af3fd1b8219a83868095f378
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056845"
---
# <a name="use-aspnet-core-no-locsignalr-with-typescript-and-webpack"></a><span data-ttu-id="670e0-103">Używanie ASP.NET Core :::no-loc(SignalR)::: z językami TypeScript i WebPack</span><span class="sxs-lookup"><span data-stu-id="670e0-103">Use ASP.NET Core :::no-loc(SignalR)::: with TypeScript and Webpack</span></span>

<span data-ttu-id="670e0-104">Autorzy [Sébastien Sougnez](https://twitter.com/ssougnez) i [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="670e0-104">By [Sébastien Sougnez](https://twitter.com/ssougnez) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="670e0-105">[Pakiet WebPack](https://webpack.js.org/) umożliwia deweloperom tworzenie i kompilowanie zasobów po stronie klienta aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="670e0-105">[Webpack](https://webpack.js.org/) enables developers to bundle and build the client-side resources of a web app.</span></span> <span data-ttu-id="670e0-106">W tym samouczku pokazano, jak używać pakietu WebPack w ASP.NET Core :::no-loc(SignalR)::: aplikacji sieci Web, której klient został zapisany w języku [TypeScript](https://www.typescriptlang.org/).</span><span class="sxs-lookup"><span data-stu-id="670e0-106">This tutorial demonstrates using Webpack in an ASP.NET Core :::no-loc(SignalR)::: web app whose client is written in [TypeScript](https://www.typescriptlang.org/).</span></span>

<span data-ttu-id="670e0-107">Ten samouczek zawiera informacje na temat wykonywania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="670e0-107">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="670e0-108">Tworzenie szkieletu aplikacji Starter ASP.NET Core :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="670e0-108">Scaffold a starter ASP.NET Core :::no-loc(SignalR)::: app</span></span>
> * <span data-ttu-id="670e0-109">Konfigurowanie :::no-loc(SignalR)::: klienta TypeScript</span><span class="sxs-lookup"><span data-stu-id="670e0-109">Configure the :::no-loc(SignalR)::: TypeScript client</span></span>
> * <span data-ttu-id="670e0-110">Konfigurowanie potoku kompilacji przy użyciu pakietu WebPack</span><span class="sxs-lookup"><span data-stu-id="670e0-110">Configure a build pipeline using Webpack</span></span>
> * <span data-ttu-id="670e0-111">Konfigurowanie :::no-loc(SignalR)::: serwera</span><span class="sxs-lookup"><span data-stu-id="670e0-111">Configure the :::no-loc(SignalR)::: server</span></span>
> * <span data-ttu-id="670e0-112">Włącz komunikację między klientem a serwerem</span><span class="sxs-lookup"><span data-stu-id="670e0-112">Enable communication between client and server</span></span>

<span data-ttu-id="670e0-113">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="670e0-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="670e0-114">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="670e0-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="670e0-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="670e0-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="670e0-116">[Program Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) z **ASP.NET i programowaniem aplikacji sieci Web**</span><span class="sxs-lookup"><span data-stu-id="670e0-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="670e0-117">Zestaw .NET Core SDK 3.0 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="670e0-117">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="670e0-118">[Node.js](https://nodejs.org/) z [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="670e0-118">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="670e0-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="670e0-119">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="670e0-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="670e0-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="670e0-121">Zestaw .NET Core SDK 3.0 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="670e0-121">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="670e0-122">C# dla Visual Studio Code w wersji 1.17.1 lub nowszej</span><span class="sxs-lookup"><span data-stu-id="670e0-122">C# for Visual Studio Code version 1.17.1 or later</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* <span data-ttu-id="670e0-123">[Node.js](https://nodejs.org/) z [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="670e0-123">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

---

## <a name="create-the-aspnet-core-web-app"></a><span data-ttu-id="670e0-124">Tworzenie aplikacji sieci Web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="670e0-124">Create the ASP.NET Core web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="670e0-125">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="670e0-125">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="670e0-126">Skonfiguruj program Visual Studio, aby szukać npm w zmiennej środowiskowej *Path* .</span><span class="sxs-lookup"><span data-stu-id="670e0-126">Configure Visual Studio to look for npm in the *PATH* environment variable.</span></span> <span data-ttu-id="670e0-127">Domyślnie program Visual Studio używa wersji npm znajdującej się w katalogu instalacyjnym.</span><span class="sxs-lookup"><span data-stu-id="670e0-127">By default, Visual Studio uses the version of npm found in its installation directory.</span></span> <span data-ttu-id="670e0-128">Wykonaj te instrukcje w programie Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="670e0-128">Follow these instructions in Visual Studio:</span></span>

1. <span data-ttu-id="670e0-129">Uruchom program Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="670e0-129">Launch Visual Studio.</span></span> <span data-ttu-id="670e0-130">W oknie uruchamiania wybierz pozycję **Kontynuuj bez kodu** .</span><span class="sxs-lookup"><span data-stu-id="670e0-130">At the start window, select **Continue without code** .</span></span>
1. <span data-ttu-id="670e0-131">Przejdź do **Tools** > **opcji Narzędzia Opcje** > **projekty i rozwiązania** > **Sieć Web zarządzanie pakietami** > **zewnętrznych narzędzi sieci Web** .</span><span class="sxs-lookup"><span data-stu-id="670e0-131">Navigate to **Tools** > **Options** > **Projects and Solutions** > **Web Package Management** > **External Web Tools** .</span></span>
1. <span data-ttu-id="670e0-132">Wybierz z listy wpis *$ (Path)* .</span><span class="sxs-lookup"><span data-stu-id="670e0-132">Select the *$(PATH)* entry from the list.</span></span> <span data-ttu-id="670e0-133">Kliknij strzałkę w górę, aby przenieść wpis do drugiej pozycji na liście, a następnie wybierz **przycisk OK** .</span><span class="sxs-lookup"><span data-stu-id="670e0-133">Click the up arrow to move the entry to the second position in the list, and select **OK** .</span></span>

    ![Konfiguracja programu Visual Studio](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

<span data-ttu-id="670e0-135">Konfiguracja programu Visual Studio została ukończona.</span><span class="sxs-lookup"><span data-stu-id="670e0-135">Visual Studio configuration is complete.</span></span>

1. <span data-ttu-id="670e0-136">Użyj opcji menu **plik**  >  **Nowy**  >  **projekt** , a następnie wybierz szablon **aplikacja sieci Web ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="670e0-136">Use the **File** > **New** > **Project** menu option and choose the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="670e0-137">Wybierz opcję **Dalej** .</span><span class="sxs-lookup"><span data-stu-id="670e0-137">Select **Next** .</span></span>
1. <span data-ttu-id="670e0-138">Nadaj nazwę projekt *:::no-loc(SignalR)::: WebPack* , a następnie wybierz pozycję **Utwórz** .</span><span class="sxs-lookup"><span data-stu-id="670e0-138">Name the project *:::no-loc(SignalR):::WebPack* , and select **Create** .</span></span>
1. <span data-ttu-id="670e0-139">Wybierz pozycję *.NET Core* z listy rozwijanej platforma docelowa, a następnie wybierz pozycję *ASP.NET Core 3,1* z listy rozwijanej selektora struktury.</span><span class="sxs-lookup"><span data-stu-id="670e0-139">Select *.NET Core* from the target framework drop-down, and select *ASP.NET Core 3.1* from the framework selector drop-down.</span></span> <span data-ttu-id="670e0-140">Wybierz **pusty** szablon i wybierz pozycję **Utwórz** .</span><span class="sxs-lookup"><span data-stu-id="670e0-140">Select the **Empty** template, and select **Create** .</span></span>

<span data-ttu-id="670e0-141">Dodaj `Microsoft.TypeScript.MSBuild` pakiet do projektu:</span><span class="sxs-lookup"><span data-stu-id="670e0-141">Add the `Microsoft.TypeScript.MSBuild` package to the project:</span></span>

1. <span data-ttu-id="670e0-142">W **Eksplorator rozwiązań** (prawego okienka) kliknij prawym przyciskiem myszy węzeł projektu i wybierz polecenie **Zarządzaj pakietami NuGet** .</span><span class="sxs-lookup"><span data-stu-id="670e0-142">In **Solution Explorer** (right pane), right-click the project node and select **Manage NuGet Packages** .</span></span> <span data-ttu-id="670e0-143">Na karcie **Przeglądaj** Wyszukaj `Microsoft.TypeScript.MSBuild` pozycję, a następnie kliknij pozycję **Zainstaluj** po prawej stronie, aby zainstalować pakiet.</span><span class="sxs-lookup"><span data-stu-id="670e0-143">In the **Browse** tab, search for `Microsoft.TypeScript.MSBuild`, and then click **Install** on the right to install the package.</span></span>

<span data-ttu-id="670e0-144">Program Visual Studio dodaje pakiet NuGet w węźle **zależności** w **Eksplorator rozwiązań** , włączając kompilację języka TypeScript w projekcie.</span><span class="sxs-lookup"><span data-stu-id="670e0-144">Visual Studio adds the NuGet package under the **Dependencies** node in **Solution Explorer** , enabling TypeScript compilation in the project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="670e0-145">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="670e0-145">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="670e0-146">Uruchom następujące polecenie w **zintegrowanym terminalu** :</span><span class="sxs-lookup"><span data-stu-id="670e0-146">Run the following command in the **Integrated Terminal** :</span></span>

```dotnetcli
dotnet new web -o :::no-loc(SignalR):::WebPack
code -r :::no-loc(SignalR):::WebPack
```

* <span data-ttu-id="670e0-147">`dotnet new`Polecenie tworzy pustą aplikację sieci web ASP.NET Core w katalogu *:::no-loc(SignalR)::: pakietu WebPack* .</span><span class="sxs-lookup"><span data-stu-id="670e0-147">The `dotnet new` command creates an empty ASP.NET Core web app in a *:::no-loc(SignalR):::WebPack* directory.</span></span>
* <span data-ttu-id="670e0-148">`code`Polecenie otwiera folder *:::no-loc(SignalR)::: WebPack* w bieżącym wystąpieniu Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="670e0-148">The `code` command opens the *:::no-loc(SignalR):::WebPack* folder in the current instance of Visual Studio Code.</span></span>

<span data-ttu-id="670e0-149">Uruchom następujące polecenie interfejs wiersza polecenia platformy .NET Core w **zintegrowanym terminalu** :</span><span class="sxs-lookup"><span data-stu-id="670e0-149">Run the following .NET Core CLI command in the **Integrated Terminal** :</span></span>

```dotnetcli
dotnet add package Microsoft.TypeScript.MSBuild
```

<span data-ttu-id="670e0-150">Poprzednie polecenie dodaje pakiet [Microsoft. TypeScript. MSBuild](https://www.nuget.org/packages/Microsoft.TypeScript.MSBuild/) , włączając kompilację TypeScript w projekcie.</span><span class="sxs-lookup"><span data-stu-id="670e0-150">The preceding command adds the [Microsoft.TypeScript.MSBuild](https://www.nuget.org/packages/Microsoft.TypeScript.MSBuild/) package, enabling TypeScript compilation in the project.</span></span>

---

## <a name="configure-webpack-and-typescript"></a><span data-ttu-id="670e0-151">Konfigurowanie pakietu WebPack i języka TypeScript</span><span class="sxs-lookup"><span data-stu-id="670e0-151">Configure Webpack and TypeScript</span></span>

<span data-ttu-id="670e0-152">Poniższe kroki umożliwiają skonfigurowanie konwersji języka TypeScript na JavaScript i zgrupowanie zasobów po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="670e0-152">The following steps configure the conversion of TypeScript to JavaScript and the bundling of client-side resources.</span></span>

1. <span data-ttu-id="670e0-153">Uruchom następujące polecenie w katalogu głównym projektu, aby utworzyć *package.jsw* pliku:</span><span class="sxs-lookup"><span data-stu-id="670e0-153">Run the following command in the project root to create a *package.json* file:</span></span>

    ```console
    npm init -y
    ```

1. <span data-ttu-id="670e0-154">Dodaj wyróżnioną właściwość do *package.jsw* pliku i Zapisz zmiany w pliku:</span><span class="sxs-lookup"><span data-stu-id="670e0-154">Add the highlighted property to the *package.json* file and save the file changes:</span></span>

    [!code-json[package.json](signalr-typescript-webpack/sample/3.x/snippets/package1.json?highlight=4)]

    <span data-ttu-id="670e0-155">Ustawienie `private` właściwości w celu `true` uniemożliwienia ostrzeżeń instalacji pakietu w następnym kroku.</span><span class="sxs-lookup"><span data-stu-id="670e0-155">Setting the `private` property to `true` prevents package installation warnings in the next step.</span></span>

1. <span data-ttu-id="670e0-156">Zainstaluj wymagane pakiety npm.</span><span class="sxs-lookup"><span data-stu-id="670e0-156">Install the required npm packages.</span></span> <span data-ttu-id="670e0-157">Uruchom następujące polecenie z poziomu głównego projektu:</span><span class="sxs-lookup"><span data-stu-id="670e0-157">Run the following command from the project root:</span></span>

    ```console
    npm i -D -E clean-webpack-plugin@3.0.0 css-loader@3.4.2 html-webpack-plugin@3.2.0 mini-css-extract-plugin@0.9.0 ts-loader@6.2.1 typescript@3.7.5 webpack@4.41.5 webpack-cli@3.3.10
    ```

    <span data-ttu-id="670e0-158">Niektóre szczegóły polecenia do uwagi:</span><span class="sxs-lookup"><span data-stu-id="670e0-158">Some command details to note:</span></span>

    * <span data-ttu-id="670e0-159">Numer wersji następuje po `@` znaku dla każdej nazwy pakietu.</span><span class="sxs-lookup"><span data-stu-id="670e0-159">A version number follows the `@` sign for each package name.</span></span> <span data-ttu-id="670e0-160">npm instaluje te określone wersje pakietu.</span><span class="sxs-lookup"><span data-stu-id="670e0-160">npm installs those specific package versions.</span></span>
    * <span data-ttu-id="670e0-161">`-E`Opcja wyłącza domyślne zachowanie npm w pisaniu operatorów zakresu [wersji semantycznej](https://semver.org/) w celu *package.jsna* .</span><span class="sxs-lookup"><span data-stu-id="670e0-161">The `-E` option disables npm's default behavior of writing [semantic versioning](https://semver.org/) range operators to *package.json* .</span></span> <span data-ttu-id="670e0-162">Na przykład, `"webpack": "4.41.5"` jest używany zamiast `"webpack": "^4.41.5"` .</span><span class="sxs-lookup"><span data-stu-id="670e0-162">For example, `"webpack": "4.41.5"` is used instead of `"webpack": "^4.41.5"`.</span></span> <span data-ttu-id="670e0-163">Ta opcja Zapobiega niezamierzonym uaktualnianiu do nowszych wersji pakietu.</span><span class="sxs-lookup"><span data-stu-id="670e0-163">This option prevents unintended upgrades to newer package versions.</span></span>

    <span data-ttu-id="670e0-164">Więcej szczegółów można znaleźć w dokumentacji [npm-Install](https://docs.npmjs.com/cli/install) .</span><span class="sxs-lookup"><span data-stu-id="670e0-164">See the [npm-install](https://docs.npmjs.com/cli/install) docs for more detail.</span></span>

1. <span data-ttu-id="670e0-165">Zastąp `scripts` właściwość *package.jsw* pliku następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="670e0-165">Replace the `scripts` property of the *package.json* file with the following code:</span></span>

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    <span data-ttu-id="670e0-166">Niektóre objaśnienia dotyczące skryptów:</span><span class="sxs-lookup"><span data-stu-id="670e0-166">Some explanation of the scripts:</span></span>

    * <span data-ttu-id="670e0-167">`build`: Łączy zasoby po stronie klienta w trybie tworzenia i czujki pod kątem zmian w pliku.</span><span class="sxs-lookup"><span data-stu-id="670e0-167">`build`: Bundles the client-side resources in development mode and watches for file changes.</span></span> <span data-ttu-id="670e0-168">Obserwator plików powoduje, że pakiet jest generowany ponownie za każdym razem, gdy plik projektu jest zmieniany.</span><span class="sxs-lookup"><span data-stu-id="670e0-168">The file watcher causes the bundle to regenerate each time a project file changes.</span></span> <span data-ttu-id="670e0-169">`mode`Opcja wyłącza optymalizacje produkcyjne, takie jak wstrząsanie i minifikacjaowanie drzewa.</span><span class="sxs-lookup"><span data-stu-id="670e0-169">The `mode` option disables production optimizations, such as tree shaking and minification.</span></span> <span data-ttu-id="670e0-170">Używać tylko `build` w programowaniu.</span><span class="sxs-lookup"><span data-stu-id="670e0-170">Only use `build` in development.</span></span>
    * <span data-ttu-id="670e0-171">`release`: Pakietuje zasoby po stronie klienta w trybie produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="670e0-171">`release`: Bundles the client-side resources in production mode.</span></span>
    * <span data-ttu-id="670e0-172">`publish`: Uruchamia skrypt służący `release` do łączenia zasobów po stronie klienta w trybie produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="670e0-172">`publish`: Runs the `release` script to bundle the client-side resources in production mode.</span></span> <span data-ttu-id="670e0-173">Wywołuje polecenie [publikowania](/dotnet/core/tools/dotnet-publish) interfejs wiersza polecenia platformy .NET Core, aby opublikować aplikację.</span><span class="sxs-lookup"><span data-stu-id="670e0-173">It calls the .NET Core CLI's [publish](/dotnet/core/tools/dotnet-publish) command to publish the app.</span></span>

1. <span data-ttu-id="670e0-174">Utwórz plik o nazwie *webpack.config.js* , w katalogu głównym projektu, przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="670e0-174">Create a file named *webpack.config.js* , in the project root, with the following code:</span></span>

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/3.x/webpack.config.js)]

    <span data-ttu-id="670e0-175">Poprzedni plik konfiguruje kompilację pakietu WebPack.</span><span class="sxs-lookup"><span data-stu-id="670e0-175">The preceding file configures the Webpack compilation.</span></span> <span data-ttu-id="670e0-176">Niektóre szczegóły konfiguracji do uwagi:</span><span class="sxs-lookup"><span data-stu-id="670e0-176">Some configuration details to note:</span></span>

    * <span data-ttu-id="670e0-177">`output`Właściwość zastępuje domyślną wartość *rozkłu* .</span><span class="sxs-lookup"><span data-stu-id="670e0-177">The `output` property overrides the default value of *dist* .</span></span> <span data-ttu-id="670e0-178">Pakiet jest emitowany w katalogu *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="670e0-178">The bundle is instead emitted in the *wwwroot* directory.</span></span>
    * <span data-ttu-id="670e0-179">`resolve.extensions`Tablica zawiera kod *js* do zaimportowania :::no-loc(SignalR)::: klienta JavaScript.</span><span class="sxs-lookup"><span data-stu-id="670e0-179">The `resolve.extensions` array includes *.js* to import the :::no-loc(SignalR)::: client JavaScript.</span></span>

1. <span data-ttu-id="670e0-180">Utwórz nowy katalog *src* w katalogu głównym projektu do przechowywania zasobów po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="670e0-180">Create a new *src* directory in the project root to store the project's client-side assets.</span></span>

1. <span data-ttu-id="670e0-181">Utwórz *src/index.html* z następującą adiustacją.</span><span class="sxs-lookup"><span data-stu-id="670e0-181">Create *src/index.html* with the following markup.</span></span>

    [!code-html[index.html](signalr-typescript-webpack/sample/3.x/src/index.html)]

    <span data-ttu-id="670e0-182">Powyższy kod HTML definiuje standardowe znaczniki strony głównej.</span><span class="sxs-lookup"><span data-stu-id="670e0-182">The preceding HTML defines the homepage's boilerplate markup.</span></span>

1. <span data-ttu-id="670e0-183">Utwórz nowy katalog *src/CSS* .</span><span class="sxs-lookup"><span data-stu-id="670e0-183">Create a new *src/css* directory.</span></span> <span data-ttu-id="670e0-184">Celem jest przechowywanie plików *CSS* projektu.</span><span class="sxs-lookup"><span data-stu-id="670e0-184">Its purpose is to store the project's *.css* files.</span></span>

1. <span data-ttu-id="670e0-185">Utwórz *src/CSS/Main. css* z następującym arkuszem CSS:</span><span class="sxs-lookup"><span data-stu-id="670e0-185">Create *src/css/main.css* with the following CSS:</span></span>

    [!code-css[main.css](signalr-typescript-webpack/sample/3.x/src/css/main.css)]

    <span data-ttu-id="670e0-186">Poprzedni plik *Main. css* jest stylem aplikacji.</span><span class="sxs-lookup"><span data-stu-id="670e0-186">The preceding *main.css* file styles the app.</span></span>

1. <span data-ttu-id="670e0-187">Utwórz *src/tsconfig.js* przy użyciu następującego kodu JSON:</span><span class="sxs-lookup"><span data-stu-id="670e0-187">Create *src/tsconfig.json* with the following JSON:</span></span>

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/3.x/src/tsconfig.json)]

    <span data-ttu-id="670e0-188">Poprzedni kod konfiguruje kompilator języka TypeScript w celu utworzenia kodu JavaScript zgodnego z [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5.</span><span class="sxs-lookup"><span data-stu-id="670e0-188">The preceding code configures the TypeScript compiler to produce [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5-compatible JavaScript.</span></span>

1. <span data-ttu-id="670e0-189">Utwórz *src/index. TS* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="670e0-189">Create *src/index.ts* with the following code:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    <span data-ttu-id="670e0-190">Poprzedni kod TypeScript pobiera odwołania do elementów DOM i dołącza dwa programy obsługi zdarzeń:</span><span class="sxs-lookup"><span data-stu-id="670e0-190">The preceding TypeScript retrieves references to DOM elements and attaches two event handlers:</span></span>

    * <span data-ttu-id="670e0-191">`keyup`: To zdarzenie jest wyzwalane, gdy użytkownik wpisze wartość w polu `tbMessage` tekstowym.</span><span class="sxs-lookup"><span data-stu-id="670e0-191">`keyup`: This event fires when the user types in the `tbMessage`textbox.</span></span> <span data-ttu-id="670e0-192">`send`Funkcja jest wywoływana, gdy użytkownik naciśnie klawisz **Enter** .</span><span class="sxs-lookup"><span data-stu-id="670e0-192">The `send` function is called when the user presses the **Enter** key.</span></span>
    * <span data-ttu-id="670e0-193">`click`: To zdarzenie jest wyzwalane, gdy użytkownik kliknie przycisk **Wyślij** .</span><span class="sxs-lookup"><span data-stu-id="670e0-193">`click`: This event fires when the user clicks the **Send** button.</span></span> <span data-ttu-id="670e0-194">Wywołanie funkcji `send`.</span><span class="sxs-lookup"><span data-stu-id="670e0-194">The `send` function is called.</span></span>

## <a name="configure-the-app"></a><span data-ttu-id="670e0-195">Konfigurowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="670e0-195">Configure the app</span></span>

1. <span data-ttu-id="670e0-196">W programie `Startup.Configure` Dodaj wywołania do [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) i [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="670e0-196">In `Startup.Configure`, add calls to [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span>

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseStaticDefaultFiles&highlight=9-10)]

   <span data-ttu-id="670e0-197">Poprzedni kod pozwala serwerowi zlokalizować i obsłużyć plik *index.html* .</span><span class="sxs-lookup"><span data-stu-id="670e0-197">The preceding code allows the server to locate and serve the *index.html* file.</span></span>  <span data-ttu-id="670e0-198">Plik jest obsługiwany niezależnie od tego, czy użytkownik wprowadza pełny adres URL, czy też główny adres URL aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="670e0-198">The file is served whether the user enters its full URL or the root URL of the web app.</span></span>

1. <span data-ttu-id="670e0-199">Na końcu `Startup.Configure` należy zmapować */Hub* trasę do `ChatHub` centrum.</span><span class="sxs-lookup"><span data-stu-id="670e0-199">At the end of `Startup.Configure`, map a */hub* route to the `ChatHub` hub.</span></span> <span data-ttu-id="670e0-200">Zastąp kod, który wyświetla *Hello World!*</span><span class="sxs-lookup"><span data-stu-id="670e0-200">Replace the code that displays *Hello World!*</span></span> <span data-ttu-id="670e0-201">z następującym wierszem:</span><span class="sxs-lookup"><span data-stu-id="670e0-201">with the following line:</span></span> 

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_Use:::no-loc(SignalR):::&highlight=3)]

1. <span data-ttu-id="670e0-202">W programie `Startup.ConfigureServices` Wywołaj polecenie [Add :::no-loc(SignalR)::: ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_:::no-loc(SignalR):::DependencyInjectionExtensions_Add:::no-loc(SignalR):::_Microsoft_Extensions_DependencyInjection_IServiceCollection_).</span><span class="sxs-lookup"><span data-stu-id="670e0-202">In `Startup.ConfigureServices`, call [Add:::no-loc(SignalR):::](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_:::no-loc(SignalR):::DependencyInjectionExtensions_Add:::no-loc(SignalR):::_Microsoft_Extensions_DependencyInjection_IServiceCollection_).</span></span>

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_Add:::no-loc(SignalR):::)]

1. <span data-ttu-id="670e0-203">Utwórz nowy katalog o nazwie *Hubs* w głównym programie Project *:::no-loc(SignalR)::: /* w celu zapisania :::no-loc(SignalR)::: centrum.</span><span class="sxs-lookup"><span data-stu-id="670e0-203">Create a new directory named *Hubs* in the project root *:::no-loc(SignalR):::WebPack/* to store the :::no-loc(SignalR)::: hub.</span></span>

1. <span data-ttu-id="670e0-204">Utwórz centra centrów */ChatHub. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="670e0-204">Create hub *Hubs/ChatHub.cs* with the following code:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. <span data-ttu-id="670e0-205">Dodaj następującą `using` instrukcję w górnej części pliku *Startup.cs* , aby rozwiązać `ChatHub` odwołanie:</span><span class="sxs-lookup"><span data-stu-id="670e0-205">Add the following `using` statement at the top of the *Startup.cs* file to resolve the `ChatHub` reference:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a><span data-ttu-id="670e0-206">Włączanie komunikacji klienta i serwera</span><span class="sxs-lookup"><span data-stu-id="670e0-206">Enable client and server communication</span></span>

<span data-ttu-id="670e0-207">W aplikacji jest obecnie wyświetlany podstawowy formularz służący do wysyłania komunikatów, ale nie jest on jeszcze funkcjonalny.</span><span class="sxs-lookup"><span data-stu-id="670e0-207">The app currently displays a basic form to send messages, but is not yet functional.</span></span> <span data-ttu-id="670e0-208">Serwer nasłuchuje określonej trasy, ale nic nie robi z wysłanymi komunikatami.</span><span class="sxs-lookup"><span data-stu-id="670e0-208">The server is listening to a specific route but does nothing with sent messages.</span></span>

1. <span data-ttu-id="670e0-209">Uruchom następujące polecenie w katalogu głównym projektu:</span><span class="sxs-lookup"><span data-stu-id="670e0-209">Run the following command at the project root:</span></span>

    ```console
    npm i @microsoft/signalr @types/node
    ```

    <span data-ttu-id="670e0-210">Poprzednie polecenie instaluje:</span><span class="sxs-lookup"><span data-stu-id="670e0-210">The preceding command installs:</span></span>

     * <span data-ttu-id="670e0-211">[ :::no-loc(SignalR)::: Klient języka TypeScript](https://www.npmjs.com/package/@microsoft/signalr), który umożliwia klientowi wysyłanie komunikatów do serwera.</span><span class="sxs-lookup"><span data-stu-id="670e0-211">The [:::no-loc(SignalR)::: TypeScript client](https://www.npmjs.com/package/@microsoft/signalr), which allows the client to send messages to the server.</span></span>
     * <span data-ttu-id="670e0-212">Definicje typu TypeScript dla Node.js, które umożliwiają sprawdzanie w czasie kompilacji typów Node.js.</span><span class="sxs-lookup"><span data-stu-id="670e0-212">The TypeScript type definitions for Node.js, which enables compile-time checking of Node.js types.</span></span>

1. <span data-ttu-id="670e0-213">Dodaj wyróżniony kod do pliku *src/index. TS* :</span><span class="sxs-lookup"><span data-stu-id="670e0-213">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    <span data-ttu-id="670e0-214">Poprzedni kod obsługuje otrzymywanie komunikatów z serwera.</span><span class="sxs-lookup"><span data-stu-id="670e0-214">The preceding code supports receiving messages from the server.</span></span> <span data-ttu-id="670e0-215">`HubConnectionBuilder`Klasa tworzy nowy Konstruktor służący do konfigurowania połączenia z serwerem.</span><span class="sxs-lookup"><span data-stu-id="670e0-215">The `HubConnectionBuilder` class creates a new builder for configuring the server connection.</span></span> <span data-ttu-id="670e0-216">`withUrl`Funkcja konfiguruje adres URL centrum.</span><span class="sxs-lookup"><span data-stu-id="670e0-216">The `withUrl` function configures the hub URL.</span></span>

    <span data-ttu-id="670e0-217">:::no-loc(SignalR)::: Włącza wymianę komunikatów między klientem a serwerem.</span><span class="sxs-lookup"><span data-stu-id="670e0-217">:::no-loc(SignalR)::: enables the exchange of messages between a client and a server.</span></span> <span data-ttu-id="670e0-218">Każdy komunikat ma określoną nazwę.</span><span class="sxs-lookup"><span data-stu-id="670e0-218">Each message has a specific name.</span></span> <span data-ttu-id="670e0-219">Na przykład komunikaty o nazwie `messageReceived` mogą uruchamiać logikę odpowiedzialną za wyświetlanie nowej wiadomości w strefie messages.</span><span class="sxs-lookup"><span data-stu-id="670e0-219">For example, messages with the name `messageReceived` can run the logic responsible for displaying the new message in the messages zone.</span></span> <span data-ttu-id="670e0-220">Nasłuchiwanie określonego komunikatu można wykonać za pomocą `on` funkcji.</span><span class="sxs-lookup"><span data-stu-id="670e0-220">Listening to a specific message can be done via the `on` function.</span></span> <span data-ttu-id="670e0-221">Dowolna liczba nazw komunikatów może być nasłuchiwanie.</span><span class="sxs-lookup"><span data-stu-id="670e0-221">Any number of message names can be listened to.</span></span> <span data-ttu-id="670e0-222">Możliwe jest również przekazywanie parametrów do wiadomości, takich jak nazwa autora i zawartość otrzymanej wiadomości.</span><span class="sxs-lookup"><span data-stu-id="670e0-222">It's also possible to pass parameters to the message, such as the author's name and the content of the message received.</span></span> <span data-ttu-id="670e0-223">Po odebraniu komunikatu przez klienta `div` zostanie utworzony nowy element z nazwą autora i treścią komunikatu w jego `innerHTML` atrybucie.</span><span class="sxs-lookup"><span data-stu-id="670e0-223">Once the client receives a message, a new `div` element is created with the author's name and the message content in its `innerHTML` attribute.</span></span> <span data-ttu-id="670e0-224">Jest on dodawany do elementu głównego `div` wyświetlającego komunikaty.</span><span class="sxs-lookup"><span data-stu-id="670e0-224">It's added to the main `div` element displaying the messages.</span></span>

1. <span data-ttu-id="670e0-225">Teraz, gdy klient może odebrać komunikat, skonfiguruj go do wysyłania wiadomości.</span><span class="sxs-lookup"><span data-stu-id="670e0-225">Now that the client can receive a message, configure it to send messages.</span></span> <span data-ttu-id="670e0-226">Dodaj wyróżniony kod do pliku *src/index. TS* :</span><span class="sxs-lookup"><span data-stu-id="670e0-226">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/src/index.ts?highlight=34-35)]

    <span data-ttu-id="670e0-227">Wysyłanie komunikatu przez połączenie z usługą WebSockets wymaga wywołania `send` metody.</span><span class="sxs-lookup"><span data-stu-id="670e0-227">Sending a message through the WebSockets connection requires calling the `send` method.</span></span> <span data-ttu-id="670e0-228">Pierwszym parametrem metody jest nazwa komunikatu.</span><span class="sxs-lookup"><span data-stu-id="670e0-228">The method's first parameter is the message name.</span></span> <span data-ttu-id="670e0-229">Dane komunikatu są nieodpowiednie dla innych parametrów.</span><span class="sxs-lookup"><span data-stu-id="670e0-229">The message data inhabits the other parameters.</span></span> <span data-ttu-id="670e0-230">W tym przykładzie komunikat identyfikowany jako `newMessage` jest wysyłany do serwera.</span><span class="sxs-lookup"><span data-stu-id="670e0-230">In this example, a message identified as `newMessage` is sent to the server.</span></span> <span data-ttu-id="670e0-231">Komunikat składa się z nazwy użytkownika i danych wejściowych użytkownika z pola tekstowego.</span><span class="sxs-lookup"><span data-stu-id="670e0-231">The message consists of the username and the user input from a text box.</span></span> <span data-ttu-id="670e0-232">Jeśli wysyłanie działa, wartość pola tekstowego jest wyczyszczona.</span><span class="sxs-lookup"><span data-stu-id="670e0-232">If the send works, the text box value is cleared.</span></span>

1. <span data-ttu-id="670e0-233">Dodaj metodę `NewMessage` do klasy `ChatHub`:</span><span class="sxs-lookup"><span data-stu-id="670e0-233">Add the `NewMessage` method to the `ChatHub` class:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/Hubs/ChatHub.cs?highlight=8-11)]

    <span data-ttu-id="670e0-234">Poprzedni kod emituje odebrane komunikaty wszystkim połączonym użytkownikom po ich odebraniu przez serwer.</span><span class="sxs-lookup"><span data-stu-id="670e0-234">The preceding code broadcasts received messages to all connected users once the server receives them.</span></span> <span data-ttu-id="670e0-235">Nie jest konieczne posiadanie `on` metody ogólnej do odbierania wszystkich komunikatów.</span><span class="sxs-lookup"><span data-stu-id="670e0-235">It's unnecessary to have a generic `on` method to receive all the messages.</span></span> <span data-ttu-id="670e0-236">Metoda o nazwie po wystarczającej nazwie.</span><span class="sxs-lookup"><span data-stu-id="670e0-236">A method named after the message name suffices.</span></span>

    <span data-ttu-id="670e0-237">W tym przykładzie klient języka TypeScript wysyła komunikat identyfikowany jako `newMessage` .</span><span class="sxs-lookup"><span data-stu-id="670e0-237">In this example, the TypeScript client sends a message identified as `newMessage`.</span></span> <span data-ttu-id="670e0-238">Metoda języka C# `NewMessage` oczekuje danych wysyłanych przez klienta.</span><span class="sxs-lookup"><span data-stu-id="670e0-238">The C# `NewMessage` method expects the data sent by the client.</span></span> <span data-ttu-id="670e0-239">Wykonano wywołanie [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) na [klientach. wszystkie](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span><span class="sxs-lookup"><span data-stu-id="670e0-239">A call is made to [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) on [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span></span> <span data-ttu-id="670e0-240">Odebrane komunikaty są wysyłane do wszystkich klientów podłączonych do centrum.</span><span class="sxs-lookup"><span data-stu-id="670e0-240">The received messages are sent to all clients connected to the hub.</span></span>

## <a name="test-the-app"></a><span data-ttu-id="670e0-241">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="670e0-241">Test the app</span></span>

<span data-ttu-id="670e0-242">Upewnij się, że aplikacja działa z następującymi krokami.</span><span class="sxs-lookup"><span data-stu-id="670e0-242">Confirm that the app works with the following steps.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="670e0-243">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="670e0-243">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="670e0-244">Uruchom pakiet WebPack w trybie *wydania* .</span><span class="sxs-lookup"><span data-stu-id="670e0-244">Run Webpack in *release* mode.</span></span> <span data-ttu-id="670e0-245">Korzystając z okna **konsoli Menedżera pakietów** , uruchom następujące polecenie w katalogu głównym projektu.</span><span class="sxs-lookup"><span data-stu-id="670e0-245">Using the **Package Manager Console** window, run the following command in the project root.</span></span> <span data-ttu-id="670e0-246">Jeśli nie jesteś w katalogu głównym projektu, wprowadź `cd :::no-loc(SignalR):::WebPack` przed wprowadzeniem polecenia.</span><span class="sxs-lookup"><span data-stu-id="670e0-246">If you are not in the project root, enter `cd :::no-loc(SignalR):::WebPack` before entering the command.</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="670e0-247">Wybierz pozycję **Debuguj**  >  **Uruchom bez debugowania** , aby uruchomić aplikację w przeglądarce bez dołączania debugera.</span><span class="sxs-lookup"><span data-stu-id="670e0-247">Select **Debug** > **Start without debugging** to launch the app in a browser without attaching the debugger.</span></span> <span data-ttu-id="670e0-248">Plik *wwwroot/index.html* jest obsługiwany pod adresem `http://localhost:<port_number>` .</span><span class="sxs-lookup"><span data-stu-id="670e0-248">The *wwwroot/index.html* file is served at `http://localhost:<port_number>`.</span></span>

   <span data-ttu-id="670e0-249">Jeśli zostaną wyświetlone błędy kompilacji, spróbuj zamknąć i ponownie otworzyć rozwiązanie.</span><span class="sxs-lookup"><span data-stu-id="670e0-249">If you get compile errors, try closing and reopening the solution.</span></span> 

1. <span data-ttu-id="670e0-250">Otwórz inne wystąpienie przeglądarki (dowolna przeglądarka).</span><span class="sxs-lookup"><span data-stu-id="670e0-250">Open another browser instance (any browser).</span></span> <span data-ttu-id="670e0-251">Wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="670e0-251">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="670e0-252">Wybierz opcję przeglądarka, wpisz coś w polu tekstowym **komunikat** , a następnie kliknij przycisk **Wyślij** .</span><span class="sxs-lookup"><span data-stu-id="670e0-252">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="670e0-253">Unikatowa nazwa użytkownika i komunikat są wyświetlane na obu stronach natychmiastowo.</span><span class="sxs-lookup"><span data-stu-id="670e0-253">The unique user name and message are displayed on both pages instantly.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="670e0-254">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="670e0-254">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="670e0-255">Uruchom pakiet WebPack w trybie *wydania* , wykonując następujące polecenie w katalogu głównym projektu:</span><span class="sxs-lookup"><span data-stu-id="670e0-255">Run Webpack in *release* mode by executing the following command in the project root:</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="670e0-256">Skompiluj i uruchom aplikację, wykonując następujące polecenie w katalogu głównym projektu:</span><span class="sxs-lookup"><span data-stu-id="670e0-256">Build and run the app by executing the following command in the project root:</span></span>

    ```dotnetcli
    dotnet run
    ```

    <span data-ttu-id="670e0-257">Serwer sieci Web uruchamia aplikację i udostępnia ją na hoście lokalnym.</span><span class="sxs-lookup"><span data-stu-id="670e0-257">The web server starts the app and makes it available on localhost.</span></span>

1. <span data-ttu-id="670e0-258">Otwórz przeglądarkę do programu `http://localhost:<port_number>` .</span><span class="sxs-lookup"><span data-stu-id="670e0-258">Open a browser to `http://localhost:<port_number>`.</span></span> <span data-ttu-id="670e0-259">Plik *wwwroot/index.html* jest obsługiwany.</span><span class="sxs-lookup"><span data-stu-id="670e0-259">The *wwwroot/index.html* file is served.</span></span> <span data-ttu-id="670e0-260">Skopiuj adres URL z paska adresu.</span><span class="sxs-lookup"><span data-stu-id="670e0-260">Copy the URL from the address bar.</span></span>

1. <span data-ttu-id="670e0-261">Otwórz inne wystąpienie przeglądarki (dowolna przeglądarka).</span><span class="sxs-lookup"><span data-stu-id="670e0-261">Open another browser instance (any browser).</span></span> <span data-ttu-id="670e0-262">Wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="670e0-262">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="670e0-263">Wybierz opcję przeglądarka, wpisz coś w polu tekstowym **komunikat** , a następnie kliknij przycisk **Wyślij** .</span><span class="sxs-lookup"><span data-stu-id="670e0-263">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="670e0-264">Unikatowa nazwa użytkownika i komunikat są wyświetlane na obu stronach natychmiastowo.</span><span class="sxs-lookup"><span data-stu-id="670e0-264">The unique user name and message are displayed on both pages instantly.</span></span>

---

![komunikat wyświetlany w obu oknach przeglądarki](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="670e0-266">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="670e0-266">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="670e0-267">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="670e0-267">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="670e0-268">[Program Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) z **ASP.NET i programowaniem aplikacji sieci Web**</span><span class="sxs-lookup"><span data-stu-id="670e0-268">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="670e0-269">Zestaw .NET Core SDK 2,2 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="670e0-269">.NET Core SDK 2.2 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="670e0-270">[Node.js](https://nodejs.org/) z [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="670e0-270">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="670e0-271">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="670e0-271">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="670e0-272">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="670e0-272">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="670e0-273">Zestaw .NET Core SDK 2,2 lub nowszy</span><span class="sxs-lookup"><span data-stu-id="670e0-273">.NET Core SDK 2.2 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="670e0-274">C# dla Visual Studio Code w wersji 1.17.1 lub nowszej</span><span class="sxs-lookup"><span data-stu-id="670e0-274">C# for Visual Studio Code version 1.17.1 or later</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* <span data-ttu-id="670e0-275">[Node.js](https://nodejs.org/) z [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="670e0-275">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

---

## <a name="create-the-aspnet-core-web-app"></a><span data-ttu-id="670e0-276">Tworzenie aplikacji sieci Web ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="670e0-276">Create the ASP.NET Core web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="670e0-277">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="670e0-277">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="670e0-278">Skonfiguruj program Visual Studio, aby szukać npm w zmiennej środowiskowej *Path* .</span><span class="sxs-lookup"><span data-stu-id="670e0-278">Configure Visual Studio to look for npm in the *PATH* environment variable.</span></span> <span data-ttu-id="670e0-279">Domyślnie program Visual Studio używa wersji npm znajdującej się w katalogu instalacyjnym.</span><span class="sxs-lookup"><span data-stu-id="670e0-279">By default, Visual Studio uses the version of npm found in its installation directory.</span></span> <span data-ttu-id="670e0-280">Wykonaj te instrukcje w programie Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="670e0-280">Follow these instructions in Visual Studio:</span></span>

1. <span data-ttu-id="670e0-281">Przejdź do **Tools** > **opcji Narzędzia Opcje** > **projekty i rozwiązania** > **Sieć Web zarządzanie pakietami** > **zewnętrznych narzędzi sieci Web** .</span><span class="sxs-lookup"><span data-stu-id="670e0-281">Navigate to **Tools** > **Options** > **Projects and Solutions** > **Web Package Management** > **External Web Tools** .</span></span>
1. <span data-ttu-id="670e0-282">Wybierz z listy wpis *$ (Path)* .</span><span class="sxs-lookup"><span data-stu-id="670e0-282">Select the *$(PATH)* entry from the list.</span></span> <span data-ttu-id="670e0-283">Kliknij strzałkę w górę, aby przenieść wpis do drugiej pozycji na liście.</span><span class="sxs-lookup"><span data-stu-id="670e0-283">Click the up arrow to move the entry to the second position in the list.</span></span>

    ![Konfiguracja programu Visual Studio](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

<span data-ttu-id="670e0-285">Konfiguracja programu Visual Studio została ukończona.</span><span class="sxs-lookup"><span data-stu-id="670e0-285">Visual Studio configuration is completed.</span></span> <span data-ttu-id="670e0-286">Czas na utworzenie projektu.</span><span class="sxs-lookup"><span data-stu-id="670e0-286">It's time to create the project.</span></span>

1. <span data-ttu-id="670e0-287">Użyj opcji menu **plik** > **Nowy** > **projekt** , a następnie wybierz szablon **aplikacja sieci Web ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="670e0-287">Use the **File** > **New** > **Project** menu option and choose the **ASP.NET Core Web Application** template.</span></span>
1. <span data-ttu-id="670e0-288">Nadaj nazwę projekt *:::no-loc(SignalR)::: WebPack* , a następnie wybierz pozycję **Utwórz** .</span><span class="sxs-lookup"><span data-stu-id="670e0-288">Name the project *:::no-loc(SignalR):::WebPack* , and select **Create** .</span></span>
1. <span data-ttu-id="670e0-289">Wybierz pozycję *.NET Core* z listy rozwijanej platforma docelowa, a następnie wybierz pozycję *ASP.NET Core 2,2* z listy rozwijanej selektora struktury.</span><span class="sxs-lookup"><span data-stu-id="670e0-289">Select *.NET Core* from the target framework drop-down, and select *ASP.NET Core 2.2* from the framework selector drop-down.</span></span> <span data-ttu-id="670e0-290">Wybierz **pusty** szablon i wybierz pozycję **Utwórz** .</span><span class="sxs-lookup"><span data-stu-id="670e0-290">Select the **Empty** template, and select **Create** .</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="670e0-291">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="670e0-291">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="670e0-292">Uruchom następujące polecenie w **zintegrowanym terminalu** :</span><span class="sxs-lookup"><span data-stu-id="670e0-292">Run the following command in the **Integrated Terminal** :</span></span>

```dotnetcli
dotnet new web -o :::no-loc(SignalR):::WebPack
```

<span data-ttu-id="670e0-293">Pusta aplikacja sieci Web ASP.NET Core, ukierunkowana na .NET Core, jest tworzona w katalogu *:::no-loc(SignalR)::: pakietu WebPack* .</span><span class="sxs-lookup"><span data-stu-id="670e0-293">An empty ASP.NET Core web app, targeting .NET Core, is created in a *:::no-loc(SignalR):::WebPack* directory.</span></span>

---

## <a name="configure-webpack-and-typescript"></a><span data-ttu-id="670e0-294">Konfigurowanie pakietu WebPack i języka TypeScript</span><span class="sxs-lookup"><span data-stu-id="670e0-294">Configure Webpack and TypeScript</span></span>

<span data-ttu-id="670e0-295">Poniższe kroki umożliwiają skonfigurowanie konwersji języka TypeScript na JavaScript i zgrupowanie zasobów po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="670e0-295">The following steps configure the conversion of TypeScript to JavaScript and the bundling of client-side resources.</span></span>

1. <span data-ttu-id="670e0-296">Uruchom następujące polecenie w katalogu głównym projektu, aby utworzyć *package.jsw* pliku:</span><span class="sxs-lookup"><span data-stu-id="670e0-296">Run the following command in the project root to create a *package.json* file:</span></span>

    ```console
    npm init -y
    ```

1. <span data-ttu-id="670e0-297">Dodaj wyróżnioną właściwość do *package.jsw* pliku:</span><span class="sxs-lookup"><span data-stu-id="670e0-297">Add the highlighted property to the *package.json* file:</span></span>

    [!code-json[package.json](signalr-typescript-webpack/sample/2.x/snippets/package1.json?highlight=4)]

    <span data-ttu-id="670e0-298">Ustawienie `private` właściwości w celu `true` uniemożliwienia ostrzeżeń instalacji pakietu w następnym kroku.</span><span class="sxs-lookup"><span data-stu-id="670e0-298">Setting the `private` property to `true` prevents package installation warnings in the next step.</span></span>

1. <span data-ttu-id="670e0-299">Zainstaluj wymagane pakiety npm.</span><span class="sxs-lookup"><span data-stu-id="670e0-299">Install the required npm packages.</span></span> <span data-ttu-id="670e0-300">Uruchom następujące polecenie z poziomu głównego projektu:</span><span class="sxs-lookup"><span data-stu-id="670e0-300">Run the following command from the project root:</span></span>

    ```console
    npm install -D -E clean-webpack-plugin@1.0.1 css-loader@2.1.0 html-webpack-plugin@4.0.0-beta.5 mini-css-extract-plugin@0.5.0 ts-loader@5.3.3 typescript@3.3.3 webpack@4.29.3 webpack-cli@3.2.3
    ```

    <span data-ttu-id="670e0-301">Niektóre szczegóły polecenia do uwagi:</span><span class="sxs-lookup"><span data-stu-id="670e0-301">Some command details to note:</span></span>

    * <span data-ttu-id="670e0-302">Numer wersji następuje po `@` znaku dla każdej nazwy pakietu.</span><span class="sxs-lookup"><span data-stu-id="670e0-302">A version number follows the `@` sign for each package name.</span></span> <span data-ttu-id="670e0-303">npm instaluje te określone wersje pakietu.</span><span class="sxs-lookup"><span data-stu-id="670e0-303">npm installs those specific package versions.</span></span>
    * <span data-ttu-id="670e0-304">`-E`Opcja wyłącza domyślne zachowanie npm w pisaniu operatorów zakresu [wersji semantycznej](https://semver.org/) w celu *package.jsna* .</span><span class="sxs-lookup"><span data-stu-id="670e0-304">The `-E` option disables npm's default behavior of writing [semantic versioning](https://semver.org/) range operators to *package.json* .</span></span> <span data-ttu-id="670e0-305">Na przykład, `"webpack": "4.29.3"` jest używany zamiast `"webpack": "^4.29.3"` .</span><span class="sxs-lookup"><span data-stu-id="670e0-305">For example, `"webpack": "4.29.3"` is used instead of `"webpack": "^4.29.3"`.</span></span> <span data-ttu-id="670e0-306">Ta opcja Zapobiega niezamierzonym uaktualnianiu do nowszych wersji pakietu.</span><span class="sxs-lookup"><span data-stu-id="670e0-306">This option prevents unintended upgrades to newer package versions.</span></span>

    <span data-ttu-id="670e0-307">Więcej szczegółów można znaleźć w dokumentacji [npm-Install](https://docs.npmjs.com/cli/install) .</span><span class="sxs-lookup"><span data-stu-id="670e0-307">See the [npm-install](https://docs.npmjs.com/cli/install) docs for more detail.</span></span>

1. <span data-ttu-id="670e0-308">Zastąp `scripts` właściwość *package.jsw* pliku następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="670e0-308">Replace the `scripts` property of the *package.json* file with the following code:</span></span>

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    <span data-ttu-id="670e0-309">Niektóre objaśnienia dotyczące skryptów:</span><span class="sxs-lookup"><span data-stu-id="670e0-309">Some explanation of the scripts:</span></span>

    * <span data-ttu-id="670e0-310">`build`: Łączy zasoby po stronie klienta w trybie tworzenia i czujki pod kątem zmian w pliku.</span><span class="sxs-lookup"><span data-stu-id="670e0-310">`build`: Bundles the client-side resources in development mode and watches for file changes.</span></span> <span data-ttu-id="670e0-311">Obserwator plików powoduje, że pakiet jest generowany ponownie za każdym razem, gdy plik projektu jest zmieniany.</span><span class="sxs-lookup"><span data-stu-id="670e0-311">The file watcher causes the bundle to regenerate each time a project file changes.</span></span> <span data-ttu-id="670e0-312">`mode`Opcja wyłącza optymalizacje produkcyjne, takie jak wstrząsanie i minifikacjaowanie drzewa.</span><span class="sxs-lookup"><span data-stu-id="670e0-312">The `mode` option disables production optimizations, such as tree shaking and minification.</span></span> <span data-ttu-id="670e0-313">Używać tylko `build` w programowaniu.</span><span class="sxs-lookup"><span data-stu-id="670e0-313">Only use `build` in development.</span></span>
    * <span data-ttu-id="670e0-314">`release`: Pakietuje zasoby po stronie klienta w trybie produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="670e0-314">`release`: Bundles the client-side resources in production mode.</span></span>
    * <span data-ttu-id="670e0-315">`publish`: Uruchamia skrypt służący `release` do łączenia zasobów po stronie klienta w trybie produkcyjnym.</span><span class="sxs-lookup"><span data-stu-id="670e0-315">`publish`: Runs the `release` script to bundle the client-side resources in production mode.</span></span> <span data-ttu-id="670e0-316">Wywołuje polecenie [publikowania](/dotnet/core/tools/dotnet-publish) interfejs wiersza polecenia platformy .NET Core, aby opublikować aplikację.</span><span class="sxs-lookup"><span data-stu-id="670e0-316">It calls the .NET Core CLI's [publish](/dotnet/core/tools/dotnet-publish) command to publish the app.</span></span>

1. <span data-ttu-id="670e0-317">Utwórz plik o nazwie *webpack.config.js* w katalogu głównym projektu przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="670e0-317">Create a file named *webpack.config.js* in the project root, with the following code:</span></span>

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/2.x/webpack.config.js)]

    <span data-ttu-id="670e0-318">Poprzedni plik konfiguruje kompilację pakietu WebPack.</span><span class="sxs-lookup"><span data-stu-id="670e0-318">The preceding file configures the Webpack compilation.</span></span> <span data-ttu-id="670e0-319">Niektóre szczegóły konfiguracji do uwagi:</span><span class="sxs-lookup"><span data-stu-id="670e0-319">Some configuration details to note:</span></span>

    * <span data-ttu-id="670e0-320">`output`Właściwość zastępuje domyślną wartość *rozkłu* .</span><span class="sxs-lookup"><span data-stu-id="670e0-320">The `output` property overrides the default value of *dist* .</span></span> <span data-ttu-id="670e0-321">Pakiet jest emitowany w katalogu *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="670e0-321">The bundle is instead emitted in the *wwwroot* directory.</span></span>
    * <span data-ttu-id="670e0-322">`resolve.extensions`Tablica zawiera kod *js* do zaimportowania :::no-loc(SignalR)::: klienta JavaScript.</span><span class="sxs-lookup"><span data-stu-id="670e0-322">The `resolve.extensions` array includes *.js* to import the :::no-loc(SignalR)::: client JavaScript.</span></span>

1. <span data-ttu-id="670e0-323">Utwórz nowy katalog *src* w katalogu głównym projektu do przechowywania zasobów po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="670e0-323">Create a new *src* directory in the project root to store the project's client-side assets.</span></span>

1. <span data-ttu-id="670e0-324">Utwórz *src/index.html* z następującą adiustacją.</span><span class="sxs-lookup"><span data-stu-id="670e0-324">Create *src/index.html* with the following markup.</span></span>

    [!code-html[index.html](signalr-typescript-webpack/sample/2.x/src/index.html)]

    <span data-ttu-id="670e0-325">Powyższy kod HTML definiuje standardowe znaczniki strony głównej.</span><span class="sxs-lookup"><span data-stu-id="670e0-325">The preceding HTML defines the homepage's boilerplate markup.</span></span>

1. <span data-ttu-id="670e0-326">Utwórz nowy katalog *src/CSS* .</span><span class="sxs-lookup"><span data-stu-id="670e0-326">Create a new *src/css* directory.</span></span> <span data-ttu-id="670e0-327">Celem jest przechowywanie plików *CSS* projektu.</span><span class="sxs-lookup"><span data-stu-id="670e0-327">Its purpose is to store the project's *.css* files.</span></span>

1. <span data-ttu-id="670e0-328">Utwórz *src/CSS/Main. css* z następującą adiustacją:</span><span class="sxs-lookup"><span data-stu-id="670e0-328">Create *src/css/main.css* with the following markup:</span></span>

    [!code-css[main.css](signalr-typescript-webpack/sample/2.x/src/css/main.css)]

    <span data-ttu-id="670e0-329">Poprzedni plik *Main. css* jest stylem aplikacji.</span><span class="sxs-lookup"><span data-stu-id="670e0-329">The preceding *main.css* file styles the app.</span></span>

1. <span data-ttu-id="670e0-330">Utwórz *src/tsconfig.js* przy użyciu następującego kodu JSON:</span><span class="sxs-lookup"><span data-stu-id="670e0-330">Create *src/tsconfig.json* with the following JSON:</span></span>

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/2.x/src/tsconfig.json)]

    <span data-ttu-id="670e0-331">Poprzedni kod konfiguruje kompilator języka TypeScript w celu utworzenia kodu JavaScript zgodnego z [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5.</span><span class="sxs-lookup"><span data-stu-id="670e0-331">The preceding code configures the TypeScript compiler to produce [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5-compatible JavaScript.</span></span>

1. <span data-ttu-id="670e0-332">Utwórz *src/index. TS* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="670e0-332">Create *src/index.ts* with the following code:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    <span data-ttu-id="670e0-333">Poprzedni kod TypeScript pobiera odwołania do elementów DOM i dołącza dwa programy obsługi zdarzeń:</span><span class="sxs-lookup"><span data-stu-id="670e0-333">The preceding TypeScript retrieves references to DOM elements and attaches two event handlers:</span></span>

    * <span data-ttu-id="670e0-334">`keyup`: To zdarzenie jest wyzwalane, gdy użytkownik wpisze wartość w polu `tbMessage` tekstowym.</span><span class="sxs-lookup"><span data-stu-id="670e0-334">`keyup`: This event fires when the user types in the `tbMessage` textbox.</span></span> <span data-ttu-id="670e0-335">`send`Funkcja jest wywoływana, gdy użytkownik naciśnie klawisz **Enter** .</span><span class="sxs-lookup"><span data-stu-id="670e0-335">The `send` function is called when the user presses the **Enter** key.</span></span>
    * <span data-ttu-id="670e0-336">`click`: To zdarzenie jest wyzwalane, gdy użytkownik kliknie przycisk **Wyślij** .</span><span class="sxs-lookup"><span data-stu-id="670e0-336">`click`: This event fires when the user clicks the **Send** button.</span></span> <span data-ttu-id="670e0-337">Wywołanie funkcji `send`.</span><span class="sxs-lookup"><span data-stu-id="670e0-337">The `send` function is called.</span></span>

## <a name="configure-the-aspnet-core-app"></a><span data-ttu-id="670e0-338">Konfigurowanie aplikacji ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="670e0-338">Configure the ASP.NET Core app</span></span>

1. <span data-ttu-id="670e0-339">Kod podany w `Startup.Configure` metodzie wyświetla *Hello World!* .</span><span class="sxs-lookup"><span data-stu-id="670e0-339">The code provided in the `Startup.Configure` method displays *Hello World!* .</span></span> <span data-ttu-id="670e0-340">Zastąp `app.Run` wywołanie metody wywołaniami do [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) i [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="670e0-340">Replace the `app.Run` method call with calls to [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseStaticDefaultFiles)]

    <span data-ttu-id="670e0-341">Poprzedni kod pozwala serwerowi zlokalizować i obsłużyć plik *index.html* , niezależnie od tego, czy użytkownik wprowadzi pełny adres URL, czy główny adres URL aplikacji sieci Web.</span><span class="sxs-lookup"><span data-stu-id="670e0-341">The preceding code allows the server to locate and serve the *index.html* file, whether the user enters its full URL or the root URL of the web app.</span></span>

1. <span data-ttu-id="670e0-342">Wywołanie [Add :::no-loc(SignalR)::: ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_:::no-loc(SignalR):::DependencyInjectionExtensions_Add:::no-loc(SignalR):::_Microsoft_Extensions_DependencyInjection_IServiceCollection_) in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="670e0-342">Call [Add:::no-loc(SignalR):::](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_:::no-loc(SignalR):::DependencyInjectionExtensions_Add:::no-loc(SignalR):::_Microsoft_Extensions_DependencyInjection_IServiceCollection_) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="670e0-343">Dodaje :::no-loc(SignalR)::: usługi do projektu.</span><span class="sxs-lookup"><span data-stu-id="670e0-343">It adds the :::no-loc(SignalR)::: services to the project.</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_Add:::no-loc(SignalR):::)]

1. <span data-ttu-id="670e0-344">Mapuj trasę */Hub* do `ChatHub` centrum.</span><span class="sxs-lookup"><span data-stu-id="670e0-344">Map a */hub* route to the `ChatHub` hub.</span></span> <span data-ttu-id="670e0-345">Dodaj następujące wiersze na końcu `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="670e0-345">Add the following lines at the end of `Startup.Configure`:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_Use:::no-loc(SignalR):::)]

1. <span data-ttu-id="670e0-346">Utwórz nowy katalog o nazwie *Hubs* w katalogu głównym projektu.</span><span class="sxs-lookup"><span data-stu-id="670e0-346">Create a new directory, called *Hubs* , in the project root.</span></span> <span data-ttu-id="670e0-347">Celem jest przechowywanie :::no-loc(SignalR)::: centrum, które jest tworzone w następnym kroku.</span><span class="sxs-lookup"><span data-stu-id="670e0-347">Its purpose is to store the :::no-loc(SignalR)::: hub, which is created in the next step.</span></span>

1. <span data-ttu-id="670e0-348">Utwórz centra centrów */ChatHub. cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="670e0-348">Create hub *Hubs/ChatHub.cs* with the following code:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. <span data-ttu-id="670e0-349">Dodaj następujący kod w górnej części pliku *Startup.cs* , aby rozwiązać `ChatHub` odwołanie:</span><span class="sxs-lookup"><span data-stu-id="670e0-349">Add the following code at the top of the *Startup.cs* file to resolve the `ChatHub` reference:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a><span data-ttu-id="670e0-350">Włączanie komunikacji klienta i serwera</span><span class="sxs-lookup"><span data-stu-id="670e0-350">Enable client and server communication</span></span>

<span data-ttu-id="670e0-351">W aplikacji jest obecnie wyświetlany prosty formularz służący do wysyłania komunikatów.</span><span class="sxs-lookup"><span data-stu-id="670e0-351">The app currently displays a simple form to send messages.</span></span> <span data-ttu-id="670e0-352">Nic się nie dzieje, gdy użytkownik spróbuje to zrobić.</span><span class="sxs-lookup"><span data-stu-id="670e0-352">Nothing happens when you try to do so.</span></span> <span data-ttu-id="670e0-353">Serwer nasłuchuje określonej trasy, ale nic nie robi z wysłanymi komunikatami.</span><span class="sxs-lookup"><span data-stu-id="670e0-353">The server is listening to a specific route but does nothing with sent messages.</span></span>

1. <span data-ttu-id="670e0-354">Uruchom następujące polecenie w katalogu głównym projektu:</span><span class="sxs-lookup"><span data-stu-id="670e0-354">Run the following command at the project root:</span></span>

    ```console
    npm install @aspnet/signalr
    ```

    <span data-ttu-id="670e0-355">Poprzednie polecenie instaluje klienta języka [ :::no-loc(SignalR)::: TypeScript](https://www.npmjs.com/package/@microsoft/signalr), który umożliwia klientowi wysyłanie komunikatów do serwera.</span><span class="sxs-lookup"><span data-stu-id="670e0-355">The preceding command installs the [:::no-loc(SignalR)::: TypeScript client](https://www.npmjs.com/package/@microsoft/signalr), which allows the client to send messages to the server.</span></span>

1. <span data-ttu-id="670e0-356">Dodaj wyróżniony kod do pliku *src/index. TS* :</span><span class="sxs-lookup"><span data-stu-id="670e0-356">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    <span data-ttu-id="670e0-357">Poprzedni kod obsługuje otrzymywanie komunikatów z serwera.</span><span class="sxs-lookup"><span data-stu-id="670e0-357">The preceding code supports receiving messages from the server.</span></span> <span data-ttu-id="670e0-358">`HubConnectionBuilder`Klasa tworzy nowy Konstruktor służący do konfigurowania połączenia z serwerem.</span><span class="sxs-lookup"><span data-stu-id="670e0-358">The `HubConnectionBuilder` class creates a new builder for configuring the server connection.</span></span> <span data-ttu-id="670e0-359">`withUrl`Funkcja konfiguruje adres URL centrum.</span><span class="sxs-lookup"><span data-stu-id="670e0-359">The `withUrl` function configures the hub URL.</span></span>

    <span data-ttu-id="670e0-360">:::no-loc(SignalR)::: Włącza wymianę komunikatów między klientem a serwerem.</span><span class="sxs-lookup"><span data-stu-id="670e0-360">:::no-loc(SignalR)::: enables the exchange of messages between a client and a server.</span></span> <span data-ttu-id="670e0-361">Każdy komunikat ma określoną nazwę.</span><span class="sxs-lookup"><span data-stu-id="670e0-361">Each message has a specific name.</span></span> <span data-ttu-id="670e0-362">Na przykład komunikaty o nazwie `messageReceived` mogą uruchamiać logikę odpowiedzialną za wyświetlanie nowej wiadomości w strefie messages.</span><span class="sxs-lookup"><span data-stu-id="670e0-362">For example, messages with the name `messageReceived` can run the logic responsible for displaying the new message in the messages zone.</span></span> <span data-ttu-id="670e0-363">Nasłuchiwanie określonego komunikatu można wykonać za pomocą `on` funkcji.</span><span class="sxs-lookup"><span data-stu-id="670e0-363">Listening to a specific message can be done via the `on` function.</span></span> <span data-ttu-id="670e0-364">Można nasłuchiwać dowolnej liczby nazw komunikatów.</span><span class="sxs-lookup"><span data-stu-id="670e0-364">You can listen to any number of message names.</span></span> <span data-ttu-id="670e0-365">Możliwe jest również przekazywanie parametrów do wiadomości, takich jak nazwa autora i zawartość otrzymanej wiadomości.</span><span class="sxs-lookup"><span data-stu-id="670e0-365">It's also possible to pass parameters to the message, such as the author's name and the content of the message received.</span></span> <span data-ttu-id="670e0-366">Po odebraniu komunikatu przez klienta `div` zostanie utworzony nowy element z nazwą autora i treścią komunikatu w jego `innerHTML` atrybucie.</span><span class="sxs-lookup"><span data-stu-id="670e0-366">Once the client receives a message, a new `div` element is created with the author's name and the message content in its `innerHTML` attribute.</span></span> <span data-ttu-id="670e0-367">Nowa wiadomość zostanie dodana do głównego `div` elementu wyświetlającego komunikaty.</span><span class="sxs-lookup"><span data-stu-id="670e0-367">The new message is added to the main `div` element displaying the messages.</span></span>

1. <span data-ttu-id="670e0-368">Teraz, gdy klient może odebrać komunikat, skonfiguruj go do wysyłania wiadomości.</span><span class="sxs-lookup"><span data-stu-id="670e0-368">Now that the client can receive a message, configure it to send messages.</span></span> <span data-ttu-id="670e0-369">Dodaj wyróżniony kod do pliku *src/index. TS* :</span><span class="sxs-lookup"><span data-stu-id="670e0-369">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/src/index.ts?highlight=34-35)]

    <span data-ttu-id="670e0-370">Wysyłanie komunikatu przez połączenie z usługą WebSockets wymaga wywołania `send` metody.</span><span class="sxs-lookup"><span data-stu-id="670e0-370">Sending a message through the WebSockets connection requires calling the `send` method.</span></span> <span data-ttu-id="670e0-371">Pierwszym parametrem metody jest nazwa komunikatu.</span><span class="sxs-lookup"><span data-stu-id="670e0-371">The method's first parameter is the message name.</span></span> <span data-ttu-id="670e0-372">Dane komunikatu są nieodpowiednie dla innych parametrów.</span><span class="sxs-lookup"><span data-stu-id="670e0-372">The message data inhabits the other parameters.</span></span> <span data-ttu-id="670e0-373">W tym przykładzie komunikat identyfikowany jako `newMessage` jest wysyłany do serwera.</span><span class="sxs-lookup"><span data-stu-id="670e0-373">In this example, a message identified as `newMessage` is sent to the server.</span></span> <span data-ttu-id="670e0-374">Komunikat składa się z nazwy użytkownika i danych wejściowych użytkownika z pola tekstowego.</span><span class="sxs-lookup"><span data-stu-id="670e0-374">The message consists of the username and the user input from a text box.</span></span> <span data-ttu-id="670e0-375">Jeśli wysyłanie działa, wartość pola tekstowego jest wyczyszczona.</span><span class="sxs-lookup"><span data-stu-id="670e0-375">If the send works, the text box value is cleared.</span></span>

1. <span data-ttu-id="670e0-376">Dodaj metodę `NewMessage` do klasy `ChatHub`:</span><span class="sxs-lookup"><span data-stu-id="670e0-376">Add the `NewMessage` method to the `ChatHub` class:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/Hubs/ChatHub.cs?highlight=8-11)]

    <span data-ttu-id="670e0-377">Poprzedni kod emituje odebrane komunikaty wszystkim połączonym użytkownikom po ich odebraniu przez serwer.</span><span class="sxs-lookup"><span data-stu-id="670e0-377">The preceding code broadcasts received messages to all connected users once the server receives them.</span></span> <span data-ttu-id="670e0-378">Nie jest konieczne posiadanie `on` metody ogólnej do odbierania wszystkich komunikatów.</span><span class="sxs-lookup"><span data-stu-id="670e0-378">It's unnecessary to have a generic `on` method to receive all the messages.</span></span> <span data-ttu-id="670e0-379">Metoda o nazwie po wystarczającej nazwie.</span><span class="sxs-lookup"><span data-stu-id="670e0-379">A method named after the message name suffices.</span></span>

    <span data-ttu-id="670e0-380">W tym przykładzie klient języka TypeScript wysyła komunikat identyfikowany jako `newMessage` .</span><span class="sxs-lookup"><span data-stu-id="670e0-380">In this example, the TypeScript client sends a message identified as `newMessage`.</span></span> <span data-ttu-id="670e0-381">Metoda języka C# `NewMessage` oczekuje danych wysyłanych przez klienta.</span><span class="sxs-lookup"><span data-stu-id="670e0-381">The C# `NewMessage` method expects the data sent by the client.</span></span> <span data-ttu-id="670e0-382">Wykonano wywołanie [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) na [klientach. wszystkie](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span><span class="sxs-lookup"><span data-stu-id="670e0-382">A call is made to [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) on [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span></span> <span data-ttu-id="670e0-383">Odebrane komunikaty są wysyłane do wszystkich klientów podłączonych do centrum.</span><span class="sxs-lookup"><span data-stu-id="670e0-383">The received messages are sent to all clients connected to the hub.</span></span>

## <a name="test-the-app"></a><span data-ttu-id="670e0-384">Testowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="670e0-384">Test the app</span></span>

<span data-ttu-id="670e0-385">Upewnij się, że aplikacja działa z następującymi krokami.</span><span class="sxs-lookup"><span data-stu-id="670e0-385">Confirm that the app works with the following steps.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="670e0-386">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="670e0-386">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="670e0-387">Uruchom pakiet WebPack w trybie *wydania* .</span><span class="sxs-lookup"><span data-stu-id="670e0-387">Run Webpack in *release* mode.</span></span> <span data-ttu-id="670e0-388">Korzystając z okna **konsoli Menedżera pakietów** , uruchom następujące polecenie w katalogu głównym projektu.</span><span class="sxs-lookup"><span data-stu-id="670e0-388">Using the **Package Manager Console** window, run the following command in the project root.</span></span> <span data-ttu-id="670e0-389">Jeśli nie jesteś w katalogu głównym projektu, wprowadź `cd :::no-loc(SignalR):::WebPack` przed wprowadzeniem polecenia.</span><span class="sxs-lookup"><span data-stu-id="670e0-389">If you are not in the project root, enter `cd :::no-loc(SignalR):::WebPack` before entering the command.</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="670e0-390">Wybierz pozycję **Debuguj**  >  **Uruchom bez debugowania** , aby uruchomić aplikację w przeglądarce bez dołączania debugera.</span><span class="sxs-lookup"><span data-stu-id="670e0-390">Select **Debug** > **Start without debugging** to launch the app in a browser without attaching the debugger.</span></span> <span data-ttu-id="670e0-391">Plik *wwwroot/index.html* jest obsługiwany pod adresem `http://localhost:<port_number>` .</span><span class="sxs-lookup"><span data-stu-id="670e0-391">The *wwwroot/index.html* file is served at `http://localhost:<port_number>`.</span></span>

1. <span data-ttu-id="670e0-392">Otwórz inne wystąpienie przeglądarki (dowolna przeglądarka).</span><span class="sxs-lookup"><span data-stu-id="670e0-392">Open another browser instance (any browser).</span></span> <span data-ttu-id="670e0-393">Wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="670e0-393">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="670e0-394">Wybierz opcję przeglądarka, wpisz coś w polu tekstowym **komunikat** , a następnie kliknij przycisk **Wyślij** .</span><span class="sxs-lookup"><span data-stu-id="670e0-394">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="670e0-395">Unikatowa nazwa użytkownika i komunikat są wyświetlane na obu stronach natychmiastowo.</span><span class="sxs-lookup"><span data-stu-id="670e0-395">The unique user name and message are displayed on both pages instantly.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="670e0-396">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="670e0-396">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="670e0-397">Uruchom pakiet WebPack w trybie *wydania* , wykonując następujące polecenie w katalogu głównym projektu:</span><span class="sxs-lookup"><span data-stu-id="670e0-397">Run Webpack in *release* mode by executing the following command in the project root:</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="670e0-398">Skompiluj i uruchom aplikację, wykonując następujące polecenie w katalogu głównym projektu:</span><span class="sxs-lookup"><span data-stu-id="670e0-398">Build and run the app by executing the following command in the project root:</span></span>

    ```dotnetcli
    dotnet run
    ```

    <span data-ttu-id="670e0-399">Serwer sieci Web uruchamia aplikację i udostępnia ją na hoście lokalnym.</span><span class="sxs-lookup"><span data-stu-id="670e0-399">The web server starts the app and makes it available on localhost.</span></span>

1. <span data-ttu-id="670e0-400">Otwórz przeglądarkę do programu `http://localhost:<port_number>` .</span><span class="sxs-lookup"><span data-stu-id="670e0-400">Open a browser to `http://localhost:<port_number>`.</span></span> <span data-ttu-id="670e0-401">Plik *wwwroot/index.html* jest obsługiwany.</span><span class="sxs-lookup"><span data-stu-id="670e0-401">The *wwwroot/index.html* file is served.</span></span> <span data-ttu-id="670e0-402">Skopiuj adres URL z paska adresu.</span><span class="sxs-lookup"><span data-stu-id="670e0-402">Copy the URL from the address bar.</span></span>

1. <span data-ttu-id="670e0-403">Otwórz inne wystąpienie przeglądarki (dowolna przeglądarka).</span><span class="sxs-lookup"><span data-stu-id="670e0-403">Open another browser instance (any browser).</span></span> <span data-ttu-id="670e0-404">Wklej adres URL na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="670e0-404">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="670e0-405">Wybierz opcję przeglądarka, wpisz coś w polu tekstowym **komunikat** , a następnie kliknij przycisk **Wyślij** .</span><span class="sxs-lookup"><span data-stu-id="670e0-405">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="670e0-406">Unikatowa nazwa użytkownika i komunikat są wyświetlane na obu stronach natychmiastowo.</span><span class="sxs-lookup"><span data-stu-id="670e0-406">The unique user name and message are displayed on both pages instantly.</span></span>

---

![komunikat wyświetlany w obu oknach przeglądarki](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="670e0-408">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="670e0-408">Additional resources</span></span>

* <xref:signalr/javascript-client>
* <xref:signalr/hubs>
