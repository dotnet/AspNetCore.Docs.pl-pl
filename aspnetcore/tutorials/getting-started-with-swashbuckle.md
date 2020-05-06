---
title: Wprowadzenie do Swashbuckle i ASP.NET Core
author: zuckerthoben
description: Dowiedz się, jak dodać Swashbuckle do projektu interfejsu API sieci Web ASP.NET Core, aby zintegrować interfejs użytkownika struktury Swagger.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/17/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/get-started-with-swashbuckle
ms.openlocfilehash: 6e4d80afa1c38344321ad45031ff21fec71ae0a4
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776724"
---
# <a name="get-started-with-swashbuckle-and-aspnet-core"></a><span data-ttu-id="d6f8b-103">Wprowadzenie do Swashbuckle i ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d6f8b-103">Get started with Swashbuckle and ASP.NET Core</span></span>

<span data-ttu-id="d6f8b-104">Autorzy [Shayne Boyer](https://twitter.com/spboyer) i [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="d6f8b-104">By [Shayne Boyer](https://twitter.com/spboyer) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="d6f8b-105">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d6f8b-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="d6f8b-106">Pakiet Swashbuckle składa się z trzech głównych składników:</span><span class="sxs-lookup"><span data-stu-id="d6f8b-106">There are three main components to Swashbuckle:</span></span>

* <span data-ttu-id="d6f8b-107">[Swashbuckle. AspNetCore. Swagger](https://www.nuget.org/packages/Swashbuckle.AspNetCore.Swagger/): model obiektów Swagger i oprogramowanie pośredniczące umożliwiające Uwidacznianie `SwaggerDocument` obiektów jako punktów końcowych JSON.</span><span class="sxs-lookup"><span data-stu-id="d6f8b-107">[Swashbuckle.AspNetCore.Swagger](https://www.nuget.org/packages/Swashbuckle.AspNetCore.Swagger/): a Swagger object model and middleware to expose `SwaggerDocument` objects as JSON endpoints.</span></span>

* <span data-ttu-id="d6f8b-108">[Swashbuckle. AspNetCore. SwaggerGen](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerGen/): Generator Swagger, który kompiluje `SwaggerDocument` obiekty bezpośrednio z tras, kontrolerów i modeli.</span><span class="sxs-lookup"><span data-stu-id="d6f8b-108">[Swashbuckle.AspNetCore.SwaggerGen](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerGen/): a Swagger generator that builds `SwaggerDocument` objects directly from your routes, controllers, and models.</span></span> <span data-ttu-id="d6f8b-109">Zazwyczaj jest ona łączona z oprogramowaniem pośredniczącym punktu końcowego programu Swagger, aby automatycznie uwidaczniać kod JSON programu Swagger.</span><span class="sxs-lookup"><span data-stu-id="d6f8b-109">It's typically combined with the Swagger endpoint middleware to automatically expose Swagger JSON.</span></span>

* <span data-ttu-id="d6f8b-110">[Swashbuckle. AspNetCore. SwaggerUI](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerUI/): wbudowana wersja narzędzia interfejsu użytkownika struktury Swagger.</span><span class="sxs-lookup"><span data-stu-id="d6f8b-110">[Swashbuckle.AspNetCore.SwaggerUI](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerUI/): an embedded version of the Swagger UI tool.</span></span> <span data-ttu-id="d6f8b-111">Interpretuje kod JSON programu Swagger w celu tworzenia rozbudowanych, możliwych do dostosowania środowisk na potrzeby opisywania funkcji internetowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="d6f8b-111">It interprets Swagger JSON to build a rich, customizable experience for describing the web API functionality.</span></span> <span data-ttu-id="d6f8b-112">Obejmuje wbudowane kontrolery testów dla metod publicznych.</span><span class="sxs-lookup"><span data-stu-id="d6f8b-112">It includes built-in test harnesses for the public methods.</span></span>

## <a name="package-installation"></a><span data-ttu-id="d6f8b-113">Instalacja pakietu</span><span class="sxs-lookup"><span data-stu-id="d6f8b-113">Package installation</span></span>

<span data-ttu-id="d6f8b-114">Swashbuckle można dodać przy użyciu następujących metod:</span><span class="sxs-lookup"><span data-stu-id="d6f8b-114">Swashbuckle can be added with the following approaches:</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="d6f8b-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d6f8b-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d6f8b-116">W oknie **konsola Menedżera pakietów** :</span><span class="sxs-lookup"><span data-stu-id="d6f8b-116">From the **Package Manager Console** window:</span></span>
  * <span data-ttu-id="d6f8b-117">Przejdź do **wyświetlania** > **innych** > **konsoli Menedżera pakietów** systemu Windows</span><span class="sxs-lookup"><span data-stu-id="d6f8b-117">Go to **View** > **Other Windows** > **Package Manager Console**</span></span>
  * <span data-ttu-id="d6f8b-118">Przejdź do katalogu, w którym znajduje się plik *TodoApi. csproj*</span><span class="sxs-lookup"><span data-stu-id="d6f8b-118">Navigate to the directory in which the *TodoApi.csproj* file exists</span></span>
  * <span data-ttu-id="d6f8b-119">Wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="d6f8b-119">Execute the following command:</span></span>

    ```powershell
    Install-Package Swashbuckle.AspNetCore -Version 5.0.0
    ```

* <span data-ttu-id="d6f8b-120">W oknie dialogowym **Zarządzanie pakietami NuGet** :</span><span class="sxs-lookup"><span data-stu-id="d6f8b-120">From the **Manage NuGet Packages** dialog:</span></span>
  * <span data-ttu-id="d6f8b-121">Kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań** > **Zarządzanie pakietami NuGet**</span><span class="sxs-lookup"><span data-stu-id="d6f8b-121">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
  * <span data-ttu-id="d6f8b-122">Ustaw **Źródło pakietu** na "NuGet.org"</span><span class="sxs-lookup"><span data-stu-id="d6f8b-122">Set the **Package source** to "nuget.org"</span></span>
  * <span data-ttu-id="d6f8b-123">Upewnij się, że opcja "Uwzględnij wersję wstępną" jest włączona</span><span class="sxs-lookup"><span data-stu-id="d6f8b-123">Ensure the "Include prerelease" option is enabled</span></span>
  * <span data-ttu-id="d6f8b-124">Wprowadź ciąg "Swashbuckle. AspNetCore" w polu wyszukiwania</span><span class="sxs-lookup"><span data-stu-id="d6f8b-124">Enter "Swashbuckle.AspNetCore" in the search box</span></span>
  * <span data-ttu-id="d6f8b-125">Wybierz najnowszy pakiet "Swashbuckle. AspNetCore" z karty **Przeglądaj** , a następnie kliknij przycisk **Instaluj** .</span><span class="sxs-lookup"><span data-stu-id="d6f8b-125">Select the latest "Swashbuckle.AspNetCore" package from the **Browse** tab and click **Install**</span></span>

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d6f8b-126">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="d6f8b-126">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d6f8b-127">Kliknij prawym przyciskiem myszy folder *pakiety* w **okienko rozwiązania** > **Dodaj pakiety...**</span><span class="sxs-lookup"><span data-stu-id="d6f8b-127">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**</span></span>
* <span data-ttu-id="d6f8b-128">Ustaw listę rozwijaną **źródła** okna **Dodaj pakiety** na "NuGet.org"</span><span class="sxs-lookup"><span data-stu-id="d6f8b-128">Set the **Add Packages** window's **Source** drop-down to "nuget.org"</span></span>
* <span data-ttu-id="d6f8b-129">Upewnij się, że opcja "Pokaż pakiety wersji wstępnej" jest włączona</span><span class="sxs-lookup"><span data-stu-id="d6f8b-129">Ensure the "Show pre-release packages" option is enabled</span></span>
* <span data-ttu-id="d6f8b-130">Wprowadź ciąg "Swashbuckle. AspNetCore" w polu wyszukiwania</span><span class="sxs-lookup"><span data-stu-id="d6f8b-130">Enter "Swashbuckle.AspNetCore" in the search box</span></span>
* <span data-ttu-id="d6f8b-131">Wybierz najnowszy pakiet "Swashbuckle. AspNetCore" z okienka wyników, a następnie kliknij pozycję **Dodaj pakiet** .</span><span class="sxs-lookup"><span data-stu-id="d6f8b-131">Select the latest "Swashbuckle.AspNetCore" package from the results pane and click **Add Package**</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="d6f8b-132">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d6f8b-132">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d6f8b-133">Uruchom następujące polecenie w **zintegrowanym terminalu**:</span><span class="sxs-lookup"><span data-stu-id="d6f8b-133">Run the following command from the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore -v 5.0.0
```

### <a name="net-core-cli"></a>[<span data-ttu-id="d6f8b-134">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="d6f8b-134">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="d6f8b-135">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="d6f8b-135">Run the following command:</span></span>

```dotnetcli
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore -v 5.0.0
```

---

## <a name="add-and-configure-swagger-middleware"></a><span data-ttu-id="d6f8b-136">Dodawanie i Konfigurowanie oprogramowania pośredniczącego programu Swagger</span><span class="sxs-lookup"><span data-stu-id="d6f8b-136">Add and configure Swagger middleware</span></span>

<span data-ttu-id="d6f8b-137">W `Startup` klasie zaimportuj następującą przestrzeń nazw, aby użyć `OpenApiInfo` klasy:</span><span class="sxs-lookup"><span data-stu-id="d6f8b-137">In the `Startup` class, import the following namespace to use the `OpenApiInfo` class:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_InfoClassNamespace)]

<span data-ttu-id="d6f8b-138">Dodaj Generator Swagger do kolekcji usług w `Startup.ConfigureServices` metodzie:</span><span class="sxs-lookup"><span data-stu-id="d6f8b-138">Add the Swagger generator to the services collection in the `Startup.ConfigureServices` method:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8-11)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=9-12)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8-11)]

::: moniker-end

<span data-ttu-id="d6f8b-139">W `Startup.Configure` metodzie Włącz oprogramowanie pośredniczące do obsługi wygenerowanego dokumentu JSON i interfejsu użytkownika programu Swagger:</span><span class="sxs-lookup"><span data-stu-id="d6f8b-139">In the `Startup.Configure` method, enable the middleware for serving the generated JSON document and the Swagger UI:</span></span>

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

::: moniker-end

<span data-ttu-id="d6f8b-140">Poprzednie `UseSwaggerUI` wywołanie metody włącza [oprogramowanie pośredniczące pliku statycznego](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="d6f8b-140">The preceding `UseSwaggerUI` method call enables the [Static File Middleware](xref:fundamentals/static-files).</span></span> <span data-ttu-id="d6f8b-141">Jeśli obiektem docelowym jest .NET Framework lub .NET Core 1. x, Dodaj pakiet NuGet [Microsoft. AspNetCore. StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) do projektu.</span><span class="sxs-lookup"><span data-stu-id="d6f8b-141">If targeting .NET Framework or .NET Core 1.x, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) NuGet package to the project.</span></span>

<span data-ttu-id="d6f8b-142">Uruchom aplikację i przejdź do `http://localhost:<port>/swagger/v1/swagger.json`.</span><span class="sxs-lookup"><span data-stu-id="d6f8b-142">Launch the app, and navigate to `http://localhost:<port>/swagger/v1/swagger.json`.</span></span> <span data-ttu-id="d6f8b-143">Wygenerowany dokument opisujący punkty końcowe pojawia się, jak pokazano w [specyfikacji Swagger (Swagger. JSON)](xref:tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson).</span><span class="sxs-lookup"><span data-stu-id="d6f8b-143">The generated document describing the endpoints appears as shown in [Swagger specification (swagger.json)](xref:tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson).</span></span>

<span data-ttu-id="d6f8b-144">Interfejs użytkownika struktury Swagger można znaleźć pod `http://localhost:<port>/swagger`adresem.</span><span class="sxs-lookup"><span data-stu-id="d6f8b-144">The Swagger UI can be found at `http://localhost:<port>/swagger`.</span></span> <span data-ttu-id="d6f8b-145">Eksploruj interfejs API za pośrednictwem interfejsu użytkownika struktury Swagger i Uwzględnij go w innych programach.</span><span class="sxs-lookup"><span data-stu-id="d6f8b-145">Explore the API via Swagger UI and incorporate it in other programs.</span></span>

> [!TIP]
> <span data-ttu-id="d6f8b-146">Aby obpracować interfejs użytkownika struktury Swagger w katalogu głównym aplikacji`http://localhost:<port>/`(), ustaw `RoutePrefix` właściwość na pusty ciąg:</span><span class="sxs-lookup"><span data-stu-id="d6f8b-146">To serve the Swagger UI at the app's root (`http://localhost:<port>/`), set the `RoutePrefix` property to an empty string:</span></span>
>
> [!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup3.cs?name=snippet_UseSwaggerUI&highlight=4)]

<span data-ttu-id="d6f8b-147">W przypadku używania katalogów z usługami IIS lub zwrotnego serwera proxy Ustaw punkt końcowy struktury Swagger na ścieżkę względną przy użyciu `./` prefiksu.</span><span class="sxs-lookup"><span data-stu-id="d6f8b-147">If using directories with IIS or a reverse proxy, set the Swagger endpoint to a relative path using the `./` prefix.</span></span> <span data-ttu-id="d6f8b-148">Na przykład `./swagger/v1/swagger.json`.</span><span class="sxs-lookup"><span data-stu-id="d6f8b-148">For example, `./swagger/v1/swagger.json`.</span></span> <span data-ttu-id="d6f8b-149">Użycie `/swagger/v1/swagger.json` instruuje aplikację, aby wyszukać plik JSON w prawdziwym katalogu głównym adresu URL (plus prefiks trasy, jeśli jest używany).</span><span class="sxs-lookup"><span data-stu-id="d6f8b-149">Using `/swagger/v1/swagger.json` instructs the app to look for the JSON file at the true root of the URL (plus the route prefix, if used).</span></span> <span data-ttu-id="d6f8b-150">Na przykład: użyj opcji `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` zamiast `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.</span><span class="sxs-lookup"><span data-stu-id="d6f8b-150">For example, use `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` instead of `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.</span></span>

## <a name="customize-and-extend"></a><span data-ttu-id="d6f8b-151">Dostosuj i rozwiń</span><span class="sxs-lookup"><span data-stu-id="d6f8b-151">Customize and extend</span></span>

<span data-ttu-id="d6f8b-152">Struktura Swagger zawiera opcje dokumentowania modelu obiektów i dostosowywania interfejsu użytkownika w celu dopasowania go do motywu.</span><span class="sxs-lookup"><span data-stu-id="d6f8b-152">Swagger provides options for documenting the object model and customizing the UI to match your theme.</span></span>

<span data-ttu-id="d6f8b-153">W `Startup` klasie Dodaj następujące przestrzenie nazw:</span><span class="sxs-lookup"><span data-stu-id="d6f8b-153">In the `Startup` class, add the following namespaces:</span></span>

```csharp
using System;
using System.Reflection;
using System.IO;
```

### <a name="api-info-and-description"></a><span data-ttu-id="d6f8b-154">Informacje o interfejsie API i opis</span><span class="sxs-lookup"><span data-stu-id="d6f8b-154">API info and description</span></span>

<span data-ttu-id="d6f8b-155">Akcja konfiguracji przeniesiona do `AddSwaggerGen` metody powoduje dodanie informacji, takich jak autor, licencja i opis:</span><span class="sxs-lookup"><span data-stu-id="d6f8b-155">The configuration action passed to the `AddSwaggerGen` method adds information such as the author, license, and description:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup4.cs?name=snippet_AddSwaggerGen)]

<span data-ttu-id="d6f8b-156">Interfejs użytkownika struktury Swagger wyświetla informacje o wersji:</span><span class="sxs-lookup"><span data-stu-id="d6f8b-156">The Swagger UI displays the version's information:</span></span>

![Interfejs użytkownika struktury Swagger z informacjami o wersji: Description, Author i linku więcej](web-api-help-pages-using-swagger/_static/custom-info.png)

### <a name="xml-comments"></a><span data-ttu-id="d6f8b-158">komentarze XML</span><span class="sxs-lookup"><span data-stu-id="d6f8b-158">XML comments</span></span>

<span data-ttu-id="d6f8b-159">Komentarze XML można włączyć przy użyciu następujących metod:</span><span class="sxs-lookup"><span data-stu-id="d6f8b-159">XML comments can be enabled with the following approaches:</span></span>

#### <a name="visual-studio"></a>[<span data-ttu-id="d6f8b-160">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d6f8b-160">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="d6f8b-161">Kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań** a następnie wybierz polecenie **edytuj <Project_Name>. csproj**.</span><span class="sxs-lookup"><span data-stu-id="d6f8b-161">Right-click the project in **Solution Explorer** and select **Edit <project_name>.csproj**.</span></span>
* <span data-ttu-id="d6f8b-162">Ręcznie Dodaj wyróżnione wiersze do pliku *csproj* :</span><span class="sxs-lookup"><span data-stu-id="d6f8b-162">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="d6f8b-163">Kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań** i wybierz polecenie **Właściwości**.</span><span class="sxs-lookup"><span data-stu-id="d6f8b-163">Right-click the project in **Solution Explorer** and select **Properties**.</span></span>
* <span data-ttu-id="d6f8b-164">Sprawdź pole **plik dokumentacji XML** w sekcji **dane wyjściowe** na karcie **kompilacja** .</span><span class="sxs-lookup"><span data-stu-id="d6f8b-164">Check the **XML documentation file** box under the **Output** section of the **Build** tab.</span></span>

::: moniker-end

#### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d6f8b-165">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="d6f8b-165">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="d6f8b-166">W *okienko rozwiązania*naciśnij klawisz **Control** i kliknij nazwę projektu.</span><span class="sxs-lookup"><span data-stu-id="d6f8b-166">From the *Solution Pad*, press **control** and click the project name.</span></span> <span data-ttu-id="d6f8b-167">Przejdź do **menu Narzędzia** > **Edytuj plik**.</span><span class="sxs-lookup"><span data-stu-id="d6f8b-167">Navigate to **Tools** > **Edit File**.</span></span>
* <span data-ttu-id="d6f8b-168">Ręcznie Dodaj wyróżnione wiersze do pliku *csproj* :</span><span class="sxs-lookup"><span data-stu-id="d6f8b-168">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="d6f8b-169">Otwórz okno dialogowe **Opcje projektu** > **Build** > **kompilator** kompilacji</span><span class="sxs-lookup"><span data-stu-id="d6f8b-169">Open the **Project Options** dialog > **Build** > **Compiler**</span></span>
* <span data-ttu-id="d6f8b-170">Zaznacz pole **Generuj dokumentację XML** w sekcji **Opcje ogólne** .</span><span class="sxs-lookup"><span data-stu-id="d6f8b-170">Check the **Generate xml documentation** box under the **General Options** section</span></span>

::: moniker-end

#### <a name="visual-studio-code"></a>[<span data-ttu-id="d6f8b-171">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d6f8b-171">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d6f8b-172">Ręcznie Dodaj wyróżnione wiersze do pliku *csproj* :</span><span class="sxs-lookup"><span data-stu-id="d6f8b-172">Manually add the highlighted lines to the *.csproj* file:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

#### <a name="net-core-cli"></a>[<span data-ttu-id="d6f8b-173">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="d6f8b-173">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="d6f8b-174">Ręcznie Dodaj wyróżnione wiersze do pliku *csproj* :</span><span class="sxs-lookup"><span data-stu-id="d6f8b-174">Manually add the highlighted lines to the *.csproj* file:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

---

<span data-ttu-id="d6f8b-175">Włączenie komentarzy XML zapewnia informacje debugowania dla nieudokumentowanych typów publicznych i członków.</span><span class="sxs-lookup"><span data-stu-id="d6f8b-175">Enabling XML comments provides debug information for undocumented public types and members.</span></span> <span data-ttu-id="d6f8b-176">Nieudokumentowane typy i elementy członkowskie są wskazywane przez komunikat ostrzegawczy.</span><span class="sxs-lookup"><span data-stu-id="d6f8b-176">Undocumented types and members are indicated by the warning message.</span></span> <span data-ttu-id="d6f8b-177">Na przykład następujący komunikat oznacza naruszenie kodu ostrzegawczego 1591:</span><span class="sxs-lookup"><span data-stu-id="d6f8b-177">For example, the following message indicates a violation of warning code 1591:</span></span>

```text
warning CS1591: Missing XML comment for publicly visible type or member 'TodoController.GetAll()'
```

<span data-ttu-id="d6f8b-178">Aby pominąć ostrzeżenia dla całego projektu, należy zdefiniować rozdzielaną średnikami listę kodów ostrzeżeń do ignorowania w pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="d6f8b-178">To suppress warnings project-wide, define a semicolon-delimited list of warning codes to ignore in the project file.</span></span> <span data-ttu-id="d6f8b-179">Dołączanie kodów ostrzeżeń w `$(NoWarn);` celu zastosowania [wartości domyślnych języka C#](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16) .</span><span class="sxs-lookup"><span data-stu-id="d6f8b-179">Appending the warning codes to `$(NoWarn);` applies the [C# default values](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16) too.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

<span data-ttu-id="d6f8b-180">Aby pominąć ostrzeżenia tylko dla określonych elementów członkowskich, należy ująć kod w [#pragma ostrzeżenia](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) preprocesora.</span><span class="sxs-lookup"><span data-stu-id="d6f8b-180">To suppress warnings only for specific members, enclose the code in [#pragma warning](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) preprocessor directives.</span></span> <span data-ttu-id="d6f8b-181">Takie podejście jest przydatne w przypadku kodu, który nie powinien być ujawniony za pośrednictwem dokumentacji interfejsu API. W poniższym przykładzie kod ostrzegawczy CS1591 jest ignorowany dla całej `Program` klasy.</span><span class="sxs-lookup"><span data-stu-id="d6f8b-181">This approach is useful for code that shouldn't be exposed via the API docs. In the following example, warning code CS1591 is ignored for the entire `Program` class.</span></span> <span data-ttu-id="d6f8b-182">Wymuszanie kodu ostrzegawczego jest przywracane po zamknięciu definicji klasy.</span><span class="sxs-lookup"><span data-stu-id="d6f8b-182">Enforcement of the warning code is restored at the close of the class definition.</span></span> <span data-ttu-id="d6f8b-183">Określ wiele kodów ostrzeżeń z listą rozdzielaną przecinkami.</span><span class="sxs-lookup"><span data-stu-id="d6f8b-183">Specify multiple warning codes with a comma-delimited list.</span></span>

```csharp
namespace TodoApi
{
#pragma warning disable CS1591
    public class Program
    {
        public static void Main(string[] args) =>
            BuildWebHost(args).Run();

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseStartup<Startup>()
                .Build();
    }
#pragma warning restore CS1591
}
```

<span data-ttu-id="d6f8b-184">Skonfiguruj strukturę Swagger, aby używała pliku XML, który jest generowany z poprzednimi instrukcjami.</span><span class="sxs-lookup"><span data-stu-id="d6f8b-184">Configure Swagger to use the XML file that's generated with the preceding instructions.</span></span> <span data-ttu-id="d6f8b-185">W przypadku systemów operacyjnych Linux lub innych niż Windows nazwy plików i ścieżki mogą być rozróżniane wielkości liter.</span><span class="sxs-lookup"><span data-stu-id="d6f8b-185">For Linux or non-Windows operating systems, file names and paths can be case-sensitive.</span></span> <span data-ttu-id="d6f8b-186">Na przykład plik *TodoApi. XML* jest prawidłowy w systemie Windows, ale nie CentOS.</span><span class="sxs-lookup"><span data-stu-id="d6f8b-186">For example, a *TodoApi.XML* file is valid on Windows but not CentOS.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=31-33)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup1x.cs?name=snippet_ConfigureServices&highlight=30-32)]

::: moniker-end

<span data-ttu-id="d6f8b-187">W poprzednim kodzie [odbicie](/dotnet/csharp/programming-guide/concepts/reflection) jest używane do kompilowania nazwy pliku XML pasującego do projektu interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="d6f8b-187">In the preceding code, [Reflection](/dotnet/csharp/programming-guide/concepts/reflection) is used to build an XML file name matching that of the web API project.</span></span> <span data-ttu-id="d6f8b-188">Właściwość [AppContext. BaseDirectory](xref:System.AppContext.BaseDirectory*) służy do konstruowania ścieżki do pliku XML.</span><span class="sxs-lookup"><span data-stu-id="d6f8b-188">The [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory*) property is used to construct a path to the XML file.</span></span> <span data-ttu-id="d6f8b-189">Niektóre funkcje struktury Swagger (na przykład schematu parametrów wejściowych lub metod HTTP i kodów odpowiedzi z odpowiednich atrybutów) działają bez użycia pliku dokumentacji XML.</span><span class="sxs-lookup"><span data-stu-id="d6f8b-189">Some Swagger features (for example, schemata of input parameters or HTTP methods and response codes from the respective attributes) work without the use of an XML documentation file.</span></span> <span data-ttu-id="d6f8b-190">W przypadku większości funkcji, a mianowicie podsumowania metod i opisów parametrów i kodów odpowiedzi, użycie pliku XML jest obowiązkowe.</span><span class="sxs-lookup"><span data-stu-id="d6f8b-190">For most features, namely method summaries and the descriptions of parameters and response codes, the use of an XML file is mandatory.</span></span>

<span data-ttu-id="d6f8b-191">Dodawanie do akcji komentarzy z potrójnym ukośnikiem ulepsza wyniki narzędzia Swagger UI przez dodanie opisu do nagłówka sekcji.</span><span class="sxs-lookup"><span data-stu-id="d6f8b-191">Adding triple-slash comments to an action enhances the Swagger UI by adding the description to the section header.</span></span> <span data-ttu-id="d6f8b-192">Dodaj element [ \<podsumowania>](/dotnet/csharp/programming-guide/xmldoc/summary) powyżej `Delete` akcji:</span><span class="sxs-lookup"><span data-stu-id="d6f8b-192">Add a [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) element above the `Delete` action:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Delete&highlight=1-3)]

<span data-ttu-id="d6f8b-193">Interfejs użytkownika struktury Swagger wyświetla tekst wewnętrzny `<summary>` elementu poprzedniego kodu:</span><span class="sxs-lookup"><span data-stu-id="d6f8b-193">The Swagger UI displays the inner text of the preceding code's `<summary>` element:</span></span>

![Interfejs użytkownika struktury Swagger pokazujący komentarz XML "Usuwa określony TodoItem".](web-api-help-pages-using-swagger/_static/triple-slash-comments.png)

<span data-ttu-id="d6f8b-196">Interfejs użytkownika jest oparty na wygenerowanym schemacie JSON:</span><span class="sxs-lookup"><span data-stu-id="d6f8b-196">The UI is driven by the generated JSON schema:</span></span>

```json
"delete": {
    "tags": [
        "Todo"
    ],
    "summary": "Deletes a specific TodoItem.",
    "operationId": "ApiTodoByIdDelete",
    "consumes": [],
    "produces": [],
    "parameters": [
        {
            "name": "id",
            "in": "path",
            "description": "",
            "required": true,
            "type": "integer",
            "format": "int64"
        }
    ],
    "responses": {
        "200": {
            "description": "Success"
        }
    }
}
```

<span data-ttu-id="d6f8b-197">Dodaj [ \<>element uwagi](/dotnet/csharp/programming-guide/xmldoc/remarks) do dokumentacji metody `Create` akcji.</span><span class="sxs-lookup"><span data-stu-id="d6f8b-197">Add a [\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) element to the `Create` action method documentation.</span></span> <span data-ttu-id="d6f8b-198">Uzupełnia on informacje określone w `<summary>` elemencie i zapewnia bardziej niezawodny interfejs użytkownika struktury Swagger.</span><span class="sxs-lookup"><span data-stu-id="d6f8b-198">It supplements information specified in the `<summary>` element and provides a more robust Swagger UI.</span></span> <span data-ttu-id="d6f8b-199">Zawartość `<remarks>` elementu może składać się z tekstu, JSON lub XML.</span><span class="sxs-lookup"><span data-stu-id="d6f8b-199">The `<remarks>` element content can consist of text, JSON, or XML.</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

<span data-ttu-id="d6f8b-200">Zwróć uwagę na ulepszenia interfejsu użytkownika z następującymi dodatkowymi komentarzami:</span><span class="sxs-lookup"><span data-stu-id="d6f8b-200">Notice the UI enhancements with these additional comments:</span></span>

![Interfejs użytkownika struktury Swagger z pokazanymi dodatkowymi komentarzami](web-api-help-pages-using-swagger/_static/xml-comments-extended.png)

### <a name="data-annotations"></a><span data-ttu-id="d6f8b-202">Adnotacje danych</span><span class="sxs-lookup"><span data-stu-id="d6f8b-202">Data annotations</span></span>

<span data-ttu-id="d6f8b-203">Oznacz model atrybutami, które znajdują się w przestrzeni nazw [System. ComponentModel. DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) , aby ułatwić DYSKOM interfejsu użytkownika struktury Swagger.</span><span class="sxs-lookup"><span data-stu-id="d6f8b-203">Mark the model with attributes, found in the [System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) namespace, to help drive the Swagger UI components.</span></span>

<span data-ttu-id="d6f8b-204">Dodaj `[Required]` atrybut do `Name` właściwości `TodoItem` klasy:</span><span class="sxs-lookup"><span data-stu-id="d6f8b-204">Add the `[Required]` attribute to the `Name` property of the `TodoItem` class:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Models/TodoItem.cs?highlight=10)]

<span data-ttu-id="d6f8b-205">Obecność tego atrybutu zmienia zachowanie interfejsu użytkownika i zmienia źródłowy schemat JSON:</span><span class="sxs-lookup"><span data-stu-id="d6f8b-205">The presence of this attribute changes the UI behavior and alters the underlying JSON schema:</span></span>

```json
"definitions": {
    "TodoItem": {
        "required": [
            "name"
        ],
        "type": "object",
        "properties": {
            "id": {
                "format": "int64",
                "type": "integer"
            },
            "name": {
                "type": "string"
            },
            "isComplete": {
                "default": false,
                "type": "boolean"
            }
        }
    }
},
```

<span data-ttu-id="d6f8b-206">Dodaj `[Produces("application/json")]` atrybut do kontrolera interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="d6f8b-206">Add the `[Produces("application/json")]` attribute to the API controller.</span></span> <span data-ttu-id="d6f8b-207">Celem jest zadeklarowanie, że działania kontrolera obsługują typ zawartości odpowiedzi dla *aplikacji/JSON*:</span><span class="sxs-lookup"><span data-stu-id="d6f8b-207">Its purpose is to declare that the controller's actions support a response content type of *application/json*:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

<span data-ttu-id="d6f8b-208">Lista rozwijana **Typ zawartości odpowiedzi** wybiera ten typ zawartości jako domyślny dla akcji Get kontrolera:</span><span class="sxs-lookup"><span data-stu-id="d6f8b-208">The **Response Content Type** drop-down selects this content type as the default for the controller's GET actions:</span></span>

![Interfejs użytkownika struktury Swagger z domyślnym typem zawartości odpowiedzi](web-api-help-pages-using-swagger/_static/json-response-content-type.png)

<span data-ttu-id="d6f8b-210">W miarę wzrostu użycia adnotacji danych w interfejsie API sieci Web strony interfejsu użytkownika i interfejsu API stają się bardziej opisowe i przydatne.</span><span class="sxs-lookup"><span data-stu-id="d6f8b-210">As the usage of data annotations in the web API increases, the UI and API help pages become more descriptive and useful.</span></span>

### <a name="describe-response-types"></a><span data-ttu-id="d6f8b-211">Opisz typy odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="d6f8b-211">Describe response types</span></span>

<span data-ttu-id="d6f8b-212">Deweloperzy korzystający z internetowego interfejsu API są najbardziej zainteresowani, które są&mdash;zwracane w szczególności typy odpowiedzi i kody błędów (jeśli nie są standardem).</span><span class="sxs-lookup"><span data-stu-id="d6f8b-212">Developers consuming a web API are most concerned with what's returned&mdash;specifically response types and error codes (if not standard).</span></span> <span data-ttu-id="d6f8b-213">Typy odpowiedzi i kody błędów są oznaczane w komentarzach XML i adnotacjach danych.</span><span class="sxs-lookup"><span data-stu-id="d6f8b-213">The response types and error codes are denoted in the XML comments and data annotations.</span></span>

<span data-ttu-id="d6f8b-214">`Create` Akcja zwraca kod stanu HTTP 201 na sukcesie.</span><span class="sxs-lookup"><span data-stu-id="d6f8b-214">The `Create` action returns an HTTP 201 status code on success.</span></span> <span data-ttu-id="d6f8b-215">Kod stanu HTTP 400 jest zwracany, gdy treść ogłoszonego żądania ma wartość null.</span><span class="sxs-lookup"><span data-stu-id="d6f8b-215">An HTTP 400 status code is returned when the posted request body is null.</span></span> <span data-ttu-id="d6f8b-216">Bez odpowiedniej dokumentacji w interfejsie użytkownika programu Swagger klient nie ma wiedzy na temat oczekiwanych wyników.</span><span class="sxs-lookup"><span data-stu-id="d6f8b-216">Without proper documentation in the Swagger UI, the consumer lacks knowledge of these expected outcomes.</span></span> <span data-ttu-id="d6f8b-217">Rozwiąż ten problem, dodając wyróżnione wiersze w następującym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="d6f8b-217">Fix that problem by adding the highlighted lines in the following example:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

<span data-ttu-id="d6f8b-218">Interfejs użytkownika struktury Swagger teraz jasno dokumentuje oczekiwane kody odpowiedzi HTTP:</span><span class="sxs-lookup"><span data-stu-id="d6f8b-218">The Swagger UI now clearly documents the expected HTTP response codes:</span></span>

![Interfejs użytkownika struktury Swagger pokazujący opis klasy odpowiedzi "zwraca nowo utworzony element zadania" i "400-Jeśli element ma wartość null" dla kodu stanu i przyczyny w komunikatach odpowiedzi](web-api-help-pages-using-swagger/_static/data-annotations-response-types.png)

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="d6f8b-220">W ASP.NET Core 2,2 lub nowszych Konwencji mogą służyć jako alternatywa dla jawnego dekorowania nazwy poszczególnych akcji z `[ProducesResponseType]`.</span><span class="sxs-lookup"><span data-stu-id="d6f8b-220">In ASP.NET Core 2.2 or later, conventions can be used as an alternative to explicitly decorating individual actions with `[ProducesResponseType]`.</span></span> <span data-ttu-id="d6f8b-221">Aby uzyskać więcej informacji, zobacz <xref:web-api/advanced/conventions>.</span><span class="sxs-lookup"><span data-stu-id="d6f8b-221">For more information, see <xref:web-api/advanced/conventions>.</span></span>

::: moniker-end

### <a name="customize-the-ui"></a><span data-ttu-id="d6f8b-222">Dostosowywanie interfejsu użytkownika</span><span class="sxs-lookup"><span data-stu-id="d6f8b-222">Customize the UI</span></span>

<span data-ttu-id="d6f8b-223">Podstawowy interfejs użytkownika jest zarówno funkcjonalny, jak i najbardziej do wysłania.</span><span class="sxs-lookup"><span data-stu-id="d6f8b-223">The stock UI is both functional and presentable.</span></span> <span data-ttu-id="d6f8b-224">Jednak strony dokumentacji interfejsu API powinny reprezentować swoją markę lub motyw.</span><span class="sxs-lookup"><span data-stu-id="d6f8b-224">However, API documentation pages should represent your brand or theme.</span></span> <span data-ttu-id="d6f8b-225">Znakowanie składników Swashbuckle wymaga dodania zasobów do obsługi plików statycznych i skompilowania struktury folderów do hostowania tych plików.</span><span class="sxs-lookup"><span data-stu-id="d6f8b-225">Branding the Swashbuckle components requires adding the resources to serve static files and building the folder structure to host those files.</span></span>

<span data-ttu-id="d6f8b-226">Jeśli obiektem docelowym jest .NET Framework lub .NET Core 1. x, Dodaj pakiet NuGet [Microsoft. AspNetCore. StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles) do projektu:</span><span class="sxs-lookup"><span data-stu-id="d6f8b-226">If targeting .NET Framework or .NET Core 1.x, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles) NuGet package to the project:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.StaticFiles" Version="2.0.0" />
```

<span data-ttu-id="d6f8b-227">Poprzedni pakiet NuGet jest już zainstalowany, jeśli celem jest .NET Core 2. x i używanie [pakietu](xref:fundamentals/metapackage).</span><span class="sxs-lookup"><span data-stu-id="d6f8b-227">The preceding NuGet package is already installed if targeting .NET Core 2.x and using the [metapackage](xref:fundamentals/metapackage).</span></span>

<span data-ttu-id="d6f8b-228">Włącz oprogramowanie pośredniczące plików statycznych:</span><span class="sxs-lookup"><span data-stu-id="d6f8b-228">Enable Static File Middleware:</span></span>

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

::: moniker-end

<span data-ttu-id="d6f8b-229">Pobierz zawartość folderu *ROZKŁ* z [repozytorium GitHub interfejsu użytkownika programu Swagger](https://github.com/swagger-api/swagger-ui/tree/master/dist).</span><span class="sxs-lookup"><span data-stu-id="d6f8b-229">Acquire the contents of the *dist* folder from the [Swagger UI GitHub repository](https://github.com/swagger-api/swagger-ui/tree/master/dist).</span></span> <span data-ttu-id="d6f8b-230">Ten folder zawiera zasoby wymagane dla strony interfejsu użytkownika programu Swagger.</span><span class="sxs-lookup"><span data-stu-id="d6f8b-230">This folder contains the necessary assets for the Swagger UI page.</span></span>

<span data-ttu-id="d6f8b-231">Utwórz folder *wwwroot/Swagger/UI* i skopiuj go do zawartości folderu *ROZKŁ* .</span><span class="sxs-lookup"><span data-stu-id="d6f8b-231">Create a *wwwroot/swagger/ui* folder, and copy into it the contents of the *dist* folder.</span></span>

<span data-ttu-id="d6f8b-232">Utwórz *niestandardowy plik. css* w pliku *wwwroot/Swagger/UI*z następującym arkuszem CSS, aby dostosować nagłówek strony:</span><span class="sxs-lookup"><span data-stu-id="d6f8b-232">Create a *custom.css* file, in *wwwroot/swagger/ui*, with the following CSS to customize the page header:</span></span>

[!code-css[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/wwwroot/swagger/ui/custom.css)]

<span data-ttu-id="d6f8b-233">Odwołuje się do *niestandardowego. css* w pliku *index. html* w folderze UI, po dowolnych innych plikach CSS:</span><span class="sxs-lookup"><span data-stu-id="d6f8b-233">Reference *custom.css* in the *index.html* file inside ui folder, after any other CSS files:</span></span>

[!code-html[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/wwwroot/swagger/ui/index.html?name=snippet_SwaggerUiCss&highlight=3)]

<span data-ttu-id="d6f8b-234">Przejdź do strony *index. html* pod adresem `http://localhost:<port>/swagger/ui/index.html`.</span><span class="sxs-lookup"><span data-stu-id="d6f8b-234">Browse to the *index.html* page at `http://localhost:<port>/swagger/ui/index.html`.</span></span> <span data-ttu-id="d6f8b-235">Wprowadź `https://localhost:<port>/swagger/v1/swagger.json` tekst w polu tekstowym nagłówka i kliknij przycisk **Eksploruj** .</span><span class="sxs-lookup"><span data-stu-id="d6f8b-235">Enter `https://localhost:<port>/swagger/v1/swagger.json` in the header's textbox, and click the **Explore** button.</span></span> <span data-ttu-id="d6f8b-236">Wynikowa strona wygląda następująco:</span><span class="sxs-lookup"><span data-stu-id="d6f8b-236">The resulting page looks as follows:</span></span>

![Interfejs użytkownika struktury Swagger z niestandardowym tytułem nagłówka](web-api-help-pages-using-swagger/_static/custom-header.png)

<span data-ttu-id="d6f8b-238">Istnieje dużo więcej możliwości na stronie.</span><span class="sxs-lookup"><span data-stu-id="d6f8b-238">There's much more you can do with the page.</span></span> <span data-ttu-id="d6f8b-239">Zapoznaj się z pełnymi możliwościami zasobów interfejsu użytkownika w [repozytorium GitHub interfejsu użytkownika programu Swagger](https://github.com/swagger-api/swagger-ui).</span><span class="sxs-lookup"><span data-stu-id="d6f8b-239">See the full capabilities for the UI resources at the [Swagger UI GitHub repository](https://github.com/swagger-api/swagger-ui).</span></span>
