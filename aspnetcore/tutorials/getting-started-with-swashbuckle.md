---
title: Zacznij ciemiężc z Swashbuckle i ASP.NET Core
author: zuckerthoben
description: Dowiedz się, jak dodać Swashbuckle do projektu internetowego interfejsu API ASP.NET Core, aby zintegrować interfejs użytkownika Swagger.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/17/2020
uid: tutorials/get-started-with-swashbuckle
ms.openlocfilehash: da848ef9c5fa85f5186d1b6f0a6111d8c8d069c4
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78661302"
---
# <a name="get-started-with-swashbuckle-and-aspnet-core"></a><span data-ttu-id="1b222-103">Zacznij ciemiężc z Swashbuckle i ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1b222-103">Get started with Swashbuckle and ASP.NET Core</span></span>

<span data-ttu-id="1b222-104">Przez [Shayne Boyer](https://twitter.com/spboyer) i [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="1b222-104">By [Shayne Boyer](https://twitter.com/spboyer) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="1b222-105">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1b222-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="1b222-106">Pakiet Swashbuckle składa się z trzech głównych składników:</span><span class="sxs-lookup"><span data-stu-id="1b222-106">There are three main components to Swashbuckle:</span></span>

* <span data-ttu-id="1b222-107">[Swashbuckle.AspNetCore.Swagger](https://www.nuget.org/packages/Swashbuckle.AspNetCore.Swagger/): model obiektów Swagger i `SwaggerDocument` oprogramowanie pośredniczące do uwidaczniania obiektów jako punktów końcowych JSON.</span><span class="sxs-lookup"><span data-stu-id="1b222-107">[Swashbuckle.AspNetCore.Swagger](https://www.nuget.org/packages/Swashbuckle.AspNetCore.Swagger/): a Swagger object model and middleware to expose `SwaggerDocument` objects as JSON endpoints.</span></span>

* <span data-ttu-id="1b222-108">[Swashbuckle.AspNetCore.SwaggerGen](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerGen/): generator Swagger, `SwaggerDocument` który buduje obiekty bezpośrednio z tras, kontrolerów i modeli.</span><span class="sxs-lookup"><span data-stu-id="1b222-108">[Swashbuckle.AspNetCore.SwaggerGen](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerGen/): a Swagger generator that builds `SwaggerDocument` objects directly from your routes, controllers, and models.</span></span> <span data-ttu-id="1b222-109">Zazwyczaj jest ona łączona z oprogramowaniem pośredniczącym punktu końcowego programu Swagger, aby automatycznie uwidaczniać kod JSON programu Swagger.</span><span class="sxs-lookup"><span data-stu-id="1b222-109">It's typically combined with the Swagger endpoint middleware to automatically expose Swagger JSON.</span></span>

* <span data-ttu-id="1b222-110">[Swashbuckle.AspNetCore.SwaggerUI](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerUI/): osadzona wersja narzędzia Swagger UI.</span><span class="sxs-lookup"><span data-stu-id="1b222-110">[Swashbuckle.AspNetCore.SwaggerUI](https://www.nuget.org/packages/Swashbuckle.AspNetCore.SwaggerUI/): an embedded version of the Swagger UI tool.</span></span> <span data-ttu-id="1b222-111">Interpretuje kod JSON programu Swagger w celu tworzenia rozbudowanych, możliwych do dostosowania środowisk na potrzeby opisywania funkcji internetowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="1b222-111">It interprets Swagger JSON to build a rich, customizable experience for describing the web API functionality.</span></span> <span data-ttu-id="1b222-112">Obejmuje wbudowane kontrolery testów dla metod publicznych.</span><span class="sxs-lookup"><span data-stu-id="1b222-112">It includes built-in test harnesses for the public methods.</span></span>

## <a name="package-installation"></a><span data-ttu-id="1b222-113">Instalacja pakietu</span><span class="sxs-lookup"><span data-stu-id="1b222-113">Package installation</span></span>

<span data-ttu-id="1b222-114">Swashbuckle można dodać z następującymi podejściami:</span><span class="sxs-lookup"><span data-stu-id="1b222-114">Swashbuckle can be added with the following approaches:</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="1b222-115">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1b222-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1b222-116">W oknie **Konsoli Menedżera pakietów:**</span><span class="sxs-lookup"><span data-stu-id="1b222-116">From the **Package Manager Console** window:</span></span>
  * <span data-ttu-id="1b222-117">Przejdź do **zobacz** > inną**konsolę Menedżera pakietów** **systemu Windows** > </span><span class="sxs-lookup"><span data-stu-id="1b222-117">Go to **View** > **Other Windows** > **Package Manager Console**</span></span>
  * <span data-ttu-id="1b222-118">Przejdź do katalogu, w którym istnieje plik *TodoApi.csproj*</span><span class="sxs-lookup"><span data-stu-id="1b222-118">Navigate to the directory in which the *TodoApi.csproj* file exists</span></span>
  * <span data-ttu-id="1b222-119">Wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="1b222-119">Execute the following command:</span></span>

    ```powershell
    Install-Package Swashbuckle.AspNetCore -Version 5.0.0
    ```

* <span data-ttu-id="1b222-120">W oknie dialogowym **Zarządzanie pakietami NuGet:**</span><span class="sxs-lookup"><span data-stu-id="1b222-120">From the **Manage NuGet Packages** dialog:</span></span>
  * <span data-ttu-id="1b222-121">Kliknij prawym przyciskiem myszy projekt w **Eksploratorze** > rozwiązań**Zarządzaj pakietami NuGet**</span><span class="sxs-lookup"><span data-stu-id="1b222-121">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
  * <span data-ttu-id="1b222-122">Ustaw **źródło pakietu** na "nuget.org"</span><span class="sxs-lookup"><span data-stu-id="1b222-122">Set the **Package source** to "nuget.org"</span></span>
  * <span data-ttu-id="1b222-123">Upewnij się, że opcja "Dołącz wydanie wstępne" jest włączona</span><span class="sxs-lookup"><span data-stu-id="1b222-123">Ensure the "Include prerelease" option is enabled</span></span>
  * <span data-ttu-id="1b222-124">Wpisz "Swashbuckle.AspNetCore" w polu wyszukiwania</span><span class="sxs-lookup"><span data-stu-id="1b222-124">Enter "Swashbuckle.AspNetCore" in the search box</span></span>
  * <span data-ttu-id="1b222-125">Wybierz najnowszy pakiet "Swashbuckle.AspNetCore" z zakładki **Przeglądaj** i **kliknij** zainstaluj</span><span class="sxs-lookup"><span data-stu-id="1b222-125">Select the latest "Swashbuckle.AspNetCore" package from the **Browse** tab and click **Install**</span></span>

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1b222-126">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="1b222-126">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="1b222-127">Kliknij prawym przyciskiem myszy folder *Pakiety* w **panelu rozrachowy** > **Dodaj pakiety...**</span><span class="sxs-lookup"><span data-stu-id="1b222-127">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**</span></span>
* <span data-ttu-id="1b222-128">Ustaw okno **Dodawanie pakietów** **w oknie Źródło** listy rozwijanej na "nuget.org"</span><span class="sxs-lookup"><span data-stu-id="1b222-128">Set the **Add Packages** window's **Source** drop-down to "nuget.org"</span></span>
* <span data-ttu-id="1b222-129">Upewnij się, że opcja "Pokaż pakiety wersji wstępnej" jest włączona</span><span class="sxs-lookup"><span data-stu-id="1b222-129">Ensure the "Show pre-release packages" option is enabled</span></span>
* <span data-ttu-id="1b222-130">Wpisz "Swashbuckle.AspNetCore" w polu wyszukiwania</span><span class="sxs-lookup"><span data-stu-id="1b222-130">Enter "Swashbuckle.AspNetCore" in the search box</span></span>
* <span data-ttu-id="1b222-131">Wybierz najnowszy pakiet "Swashbuckle.AspNetCore" z okienka wyników i kliknij dodaj **pakiet**</span><span class="sxs-lookup"><span data-stu-id="1b222-131">Select the latest "Swashbuckle.AspNetCore" package from the results pane and click **Add Package**</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="1b222-132">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1b222-132">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1b222-133">Uruchom następujące polecenie z **terminalu zintegrowanego:**</span><span class="sxs-lookup"><span data-stu-id="1b222-133">Run the following command from the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore -v 5.0.0
```

### <a name="net-core-cli"></a>[<span data-ttu-id="1b222-134">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="1b222-134">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="1b222-135">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="1b222-135">Run the following command:</span></span>

```dotnetcli
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore -v 5.0.0
```

---

## <a name="add-and-configure-swagger-middleware"></a><span data-ttu-id="1b222-136">Dodawanie i konfigurowanie oprogramowania pośredniczącego Swagger</span><span class="sxs-lookup"><span data-stu-id="1b222-136">Add and configure Swagger middleware</span></span>

<span data-ttu-id="1b222-137">W `Startup` klasie zaimportuj następujący `OpenApiInfo` obszar nazw, aby użyć tej klasy:</span><span class="sxs-lookup"><span data-stu-id="1b222-137">In the `Startup` class, import the following namespace to use the `OpenApiInfo` class:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_InfoClassNamespace)]

<span data-ttu-id="1b222-138">Dodaj generator Swagger do kolekcji `Startup.ConfigureServices` usług w metodzie:</span><span class="sxs-lookup"><span data-stu-id="1b222-138">Add the Swagger generator to the services collection in the `Startup.ConfigureServices` method:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8-11)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=9-12)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_ConfigureServices&highlight=8-11)]

::: moniker-end

<span data-ttu-id="1b222-139">W `Startup.Configure` metodzie włącz oprogramowanie pośredniczące do obsługi wygenerowanego dokumentu JSON i interfejsu użytkownika Swagger:</span><span class="sxs-lookup"><span data-stu-id="1b222-139">In the `Startup.Configure` method, enable the middleware for serving the generated JSON document and the Swagger UI:</span></span>

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup2.cs?name=snippet_Configure&highlight=4,8-11)]

::: moniker-end

<span data-ttu-id="1b222-140">Poprzednie wywołanie `UseSwaggerUI` metody włącza [oprogramowanie pośredniczące pliku statycznego](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="1b222-140">The preceding `UseSwaggerUI` method call enables the [Static File Middleware](xref:fundamentals/static-files).</span></span> <span data-ttu-id="1b222-141">Jeśli jest kierowana na program .NET Framework lub .NET Core 1.x, dodaj do projektu pakiet [NuGet firmy Microsoft.AspNetCore.StaticFiles.](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/)</span><span class="sxs-lookup"><span data-stu-id="1b222-141">If targeting .NET Framework or .NET Core 1.x, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) NuGet package to the project.</span></span>

<span data-ttu-id="1b222-142">Uruchom aplikację i przejdź `http://localhost:<port>/swagger/v1/swagger.json`do .</span><span class="sxs-lookup"><span data-stu-id="1b222-142">Launch the app, and navigate to `http://localhost:<port>/swagger/v1/swagger.json`.</span></span> <span data-ttu-id="1b222-143">Wygenerowany dokument opisujący punkty końcowe jest wyświetlany w sposób pokazany w [specyfikacji Swaggera (swagger.json).](xref:tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson)</span><span class="sxs-lookup"><span data-stu-id="1b222-143">The generated document describing the endpoints appears as shown in [Swagger specification (swagger.json)](xref:tutorials/web-api-help-pages-using-swagger#swagger-specification-swaggerjson).</span></span>

<span data-ttu-id="1b222-144">Interfejs użytkownika Swagger można `http://localhost:<port>/swagger`znaleźć w .</span><span class="sxs-lookup"><span data-stu-id="1b222-144">The Swagger UI can be found at `http://localhost:<port>/swagger`.</span></span> <span data-ttu-id="1b222-145">Eksploruj interfejs API za pośrednictwem interfejsu użytkownika Swagger i włącz go do innych programów.</span><span class="sxs-lookup"><span data-stu-id="1b222-145">Explore the API via Swagger UI and incorporate it in other programs.</span></span>

> [!TIP]
> <span data-ttu-id="1b222-146">Aby służyć interfejsowi użytkownika swagger w`http://localhost:<port>/`katalogu głównym `RoutePrefix` aplikacji ( ), ustaw właściwość na pusty ciąg:</span><span class="sxs-lookup"><span data-stu-id="1b222-146">To serve the Swagger UI at the app's root (`http://localhost:<port>/`), set the `RoutePrefix` property to an empty string:</span></span>
>
> [!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup3.cs?name=snippet_UseSwaggerUI&highlight=4)]

<span data-ttu-id="1b222-147">Jeśli używasz katalogów z usługami IIS lub odwrotnego serwera proxy, `./` ustaw punkt końcowy Swagger na ścieżkę względną przy użyciu prefiksu.</span><span class="sxs-lookup"><span data-stu-id="1b222-147">If using directories with IIS or a reverse proxy, set the Swagger endpoint to a relative path using the `./` prefix.</span></span> <span data-ttu-id="1b222-148">Na przykład `./swagger/v1/swagger.json`.</span><span class="sxs-lookup"><span data-stu-id="1b222-148">For example, `./swagger/v1/swagger.json`.</span></span> <span data-ttu-id="1b222-149">Za `/swagger/v1/swagger.json` pomocą nakazuje aplikacji, aby wyszukać plik JSON w prawdziwym katalogu głównym adresu URL (plus prefiks trasy, jeśli jest używany).</span><span class="sxs-lookup"><span data-stu-id="1b222-149">Using `/swagger/v1/swagger.json` instructs the app to look for the JSON file at the true root of the URL (plus the route prefix, if used).</span></span> <span data-ttu-id="1b222-150">Na przykład: użyj opcji `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` zamiast `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.</span><span class="sxs-lookup"><span data-stu-id="1b222-150">For example, use `http://localhost:<port>/<route_prefix>/swagger/v1/swagger.json` instead of `http://localhost:<port>/<virtual_directory>/<route_prefix>/swagger/v1/swagger.json`.</span></span>

## <a name="customize-and-extend"></a><span data-ttu-id="1b222-151">Dostosowywanie i rozszerzanie</span><span class="sxs-lookup"><span data-stu-id="1b222-151">Customize and extend</span></span>

<span data-ttu-id="1b222-152">Swagger udostępnia opcje dokumentowania modelu obiektu i dostosowywania interfejsu użytkownika w celu dopasowania do motywu.</span><span class="sxs-lookup"><span data-stu-id="1b222-152">Swagger provides options for documenting the object model and customizing the UI to match your theme.</span></span>

<span data-ttu-id="1b222-153">W `Startup` klasie dodaj następujące przestrzenie nazw:</span><span class="sxs-lookup"><span data-stu-id="1b222-153">In the `Startup` class, add the following namespaces:</span></span>

```csharp
using System;
using System.Reflection;
using System.IO;
```

### <a name="api-info-and-description"></a><span data-ttu-id="1b222-154">Informacje i opis interfejsu API</span><span class="sxs-lookup"><span data-stu-id="1b222-154">API info and description</span></span>

<span data-ttu-id="1b222-155">Akcja konfiguracji przekazana `AddSwaggerGen` do metody dodaje informacje, takie jak autor, licencja i opis:</span><span class="sxs-lookup"><span data-stu-id="1b222-155">The configuration action passed to the `AddSwaggerGen` method adds information such as the author, license, and description:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Startup4.cs?name=snippet_AddSwaggerGen)]

<span data-ttu-id="1b222-156">Interfejs użytkownika Swagger wyświetla informacje o wersji:</span><span class="sxs-lookup"><span data-stu-id="1b222-156">The Swagger UI displays the version's information:</span></span>

![Swagger UI z informacjami o wersji: opis, autor i zobacz więcej linków](web-api-help-pages-using-swagger/_static/custom-info.png)

### <a name="xml-comments"></a><span data-ttu-id="1b222-158">komentarze XML</span><span class="sxs-lookup"><span data-stu-id="1b222-158">XML comments</span></span>

<span data-ttu-id="1b222-159">Komentarze XML można włączyć za pomocą następujących metod:</span><span class="sxs-lookup"><span data-stu-id="1b222-159">XML comments can be enabled with the following approaches:</span></span>

#### <a name="visual-studio"></a>[<span data-ttu-id="1b222-160">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1b222-160">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="1b222-161">Kliknij prawym przyciskiem myszy projekt w **Eksploratorze rozwiązań** i wybierz polecenie **Edytuj <project_name>.csproj**.</span><span class="sxs-lookup"><span data-stu-id="1b222-161">Right-click the project in **Solution Explorer** and select **Edit <project_name>.csproj**.</span></span>
* <span data-ttu-id="1b222-162">Ręcznie dodaj wyróżnione wiersze do pliku *csproj:*</span><span class="sxs-lookup"><span data-stu-id="1b222-162">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="1b222-163">Kliknij prawym przyciskiem myszy projekt w **Eksploratorze rozwiązań** i wybierz polecenie **Właściwości**.</span><span class="sxs-lookup"><span data-stu-id="1b222-163">Right-click the project in **Solution Explorer** and select **Properties**.</span></span>
* <span data-ttu-id="1b222-164">Zaznacz pole **pliku dokumentacji XML** w sekcji **Dane wyjściowe** na karcie **Kompilacja.**</span><span class="sxs-lookup"><span data-stu-id="1b222-164">Check the **XML documentation file** box under the **Output** section of the **Build** tab.</span></span>

::: moniker-end

#### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1b222-165">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="1b222-165">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="1b222-166">W *panelu rozrachówek*naciśnij **kontrolkę** i kliknij nazwę projektu.</span><span class="sxs-lookup"><span data-stu-id="1b222-166">From the *Solution Pad*, press **control** and click the project name.</span></span> <span data-ttu-id="1b222-167">Przejdź do **pozycji Narzędzia** > **edycji pliku**.</span><span class="sxs-lookup"><span data-stu-id="1b222-167">Navigate to **Tools** > **Edit File**.</span></span>
* <span data-ttu-id="1b222-168">Ręcznie dodaj wyróżnione wiersze do pliku *csproj:*</span><span class="sxs-lookup"><span data-stu-id="1b222-168">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="1b222-169">Otwórz okno dialogowe **Opcje projektu** > **kompilator** **kompilacji** ></span><span class="sxs-lookup"><span data-stu-id="1b222-169">Open the **Project Options** dialog > **Build** > **Compiler**</span></span>
* <span data-ttu-id="1b222-170">Zaznacz pole **Wyboru Generuj dokumentację xml** w sekcji **Opcje ogólne**</span><span class="sxs-lookup"><span data-stu-id="1b222-170">Check the **Generate xml documentation** box under the **General Options** section</span></span>

::: moniker-end

#### <a name="visual-studio-code"></a>[<span data-ttu-id="1b222-171">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1b222-171">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1b222-172">Ręcznie dodaj wyróżnione wiersze do pliku *csproj:*</span><span class="sxs-lookup"><span data-stu-id="1b222-172">Manually add the highlighted lines to the *.csproj* file:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

#### <a name="net-core-cli"></a>[<span data-ttu-id="1b222-173">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="1b222-173">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="1b222-174">Ręcznie dodaj wyróżnione wiersze do pliku *csproj:*</span><span class="sxs-lookup"><span data-stu-id="1b222-174">Manually add the highlighted lines to the *.csproj* file:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=1-2,4)]

::: moniker-end

---

<span data-ttu-id="1b222-175">Włączenie komentarzy XML zapewnia informacje debugowania dla nieudokumentowanych typów publicznych i członków.</span><span class="sxs-lookup"><span data-stu-id="1b222-175">Enabling XML comments provides debug information for undocumented public types and members.</span></span> <span data-ttu-id="1b222-176">Nieudokumentowane typy i elementy członkowskie są oznaczone komunikatem ostrzegawczym.</span><span class="sxs-lookup"><span data-stu-id="1b222-176">Undocumented types and members are indicated by the warning message.</span></span> <span data-ttu-id="1b222-177">Na przykład następujący komunikat wskazuje naruszenie kodu ostrzeżenia 1591:</span><span class="sxs-lookup"><span data-stu-id="1b222-177">For example, the following message indicates a violation of warning code 1591:</span></span>

```text
warning CS1591: Missing XML comment for publicly visible type or member 'TodoController.GetAll()'
```

<span data-ttu-id="1b222-178">Aby pominąć ostrzeżenia w całym projekcie, zdefiniuj listę kodów ostrzeżeń rozdzielonych średnikami, które mają być ignorowane w pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="1b222-178">To suppress warnings project-wide, define a semicolon-delimited list of warning codes to ignore in the project file.</span></span> <span data-ttu-id="1b222-179">Dołączanie kodów ostrzeżeń do `$(NoWarn);` zastosowania wartości [domyślnych języka C#.](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16)</span><span class="sxs-lookup"><span data-stu-id="1b222-179">Appending the warning codes to `$(NoWarn);` applies the [C# default values](https://github.com/dotnet/sdk/blob/2eb6c546931b5bcb92cd3128b93932a980553ea1/src/Tasks/Microsoft.NET.Build.Tasks/targets/Microsoft.NET.Sdk.CSharp.props#L16) too.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/TodoApi.csproj?name=snippet_SuppressWarnings&highlight=3)]

::: moniker-end

<span data-ttu-id="1b222-180">Aby pominąć ostrzeżenia tylko dla określonych elementów członkowskich, należy ująć kod w [#pragma ostrzeżenia](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) dyrektywy preprocesora.</span><span class="sxs-lookup"><span data-stu-id="1b222-180">To suppress warnings only for specific members, enclose the code in [#pragma warning](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-pragma-warning) preprocessor directives.</span></span> <span data-ttu-id="1b222-181">Takie podejście jest przydatne dla kodu, który nie powinien być narażony za pośrednictwem dokumentów interfejsu API. W poniższym przykładzie kod ostrzeżenia CS1591 `Program` jest ignorowany dla całej klasy.</span><span class="sxs-lookup"><span data-stu-id="1b222-181">This approach is useful for code that shouldn't be exposed via the API docs. In the following example, warning code CS1591 is ignored for the entire `Program` class.</span></span> <span data-ttu-id="1b222-182">Wymuszanie kodu ostrzeżenia jest przywracany po zamknięciu definicji klasy.</span><span class="sxs-lookup"><span data-stu-id="1b222-182">Enforcement of the warning code is restored at the close of the class definition.</span></span> <span data-ttu-id="1b222-183">Określ wiele kodów ostrzeżeń z listą rozdzielanymi przecinkami.</span><span class="sxs-lookup"><span data-stu-id="1b222-183">Specify multiple warning codes with a comma-delimited list.</span></span>

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

<span data-ttu-id="1b222-184">Skonfiguruj Swagger do używania pliku XML, który jest generowany z poprzednimi instrukcjami.</span><span class="sxs-lookup"><span data-stu-id="1b222-184">Configure Swagger to use the XML file that's generated with the preceding instructions.</span></span> <span data-ttu-id="1b222-185">W systemach operacyjnych Linux lub innych niż Windows nazwy plików i ścieżki mogą być rozróżniane.</span><span class="sxs-lookup"><span data-stu-id="1b222-185">For Linux or non-Windows operating systems, file names and paths can be case-sensitive.</span></span> <span data-ttu-id="1b222-186">Na przykład plik *TodoApi.XML* jest prawidłowy w systemie Windows, ale nie CentOS.</span><span class="sxs-lookup"><span data-stu-id="1b222-186">For example, a *TodoApi.XML* file is valid on Windows but not CentOS.</span></span>

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

<span data-ttu-id="1b222-187">W poprzednim kodzie [Odbicie](/dotnet/csharp/programming-guide/concepts/reflection) służy do tworzenia nazwy pliku XML odpowiadającej nazwie projektu internetowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="1b222-187">In the preceding code, [Reflection](/dotnet/csharp/programming-guide/concepts/reflection) is used to build an XML file name matching that of the web API project.</span></span> <span data-ttu-id="1b222-188">[Właściwość AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory*) służy do konstruowania ścieżki do pliku XML.</span><span class="sxs-lookup"><span data-stu-id="1b222-188">The [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory*) property is used to construct a path to the XML file.</span></span> <span data-ttu-id="1b222-189">Niektóre funkcje Swagger (na przykład schemat parametrów wejściowych lub metody HTTP i kody odpowiedzi z odpowiednich atrybutów) działają bez użycia pliku dokumentacji XML.</span><span class="sxs-lookup"><span data-stu-id="1b222-189">Some Swagger features (for example, schemata of input parameters or HTTP methods and response codes from the respective attributes) work without the use of an XML documentation file.</span></span> <span data-ttu-id="1b222-190">W przypadku większości funkcji, a mianowicie podsumowań metod i opisów parametrów i kodów odpowiedzi, użycie pliku XML jest obowiązkowe.</span><span class="sxs-lookup"><span data-stu-id="1b222-190">For most features, namely method summaries and the descriptions of parameters and response codes, the use of an XML file is mandatory.</span></span>

<span data-ttu-id="1b222-191">Dodawanie do akcji komentarzy z potrójnym ukośnikiem ulepsza wyniki narzędzia Swagger UI przez dodanie opisu do nagłówka sekcji.</span><span class="sxs-lookup"><span data-stu-id="1b222-191">Adding triple-slash comments to an action enhances the Swagger UI by adding the description to the section header.</span></span> <span data-ttu-id="1b222-192">Dodaj [ \<element podsumowania>](/dotnet/csharp/programming-guide/xmldoc/summary) nad `Delete` akcją:</span><span class="sxs-lookup"><span data-stu-id="1b222-192">Add a [\<summary>](/dotnet/csharp/programming-guide/xmldoc/summary) element above the `Delete` action:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Delete&highlight=1-3)]

<span data-ttu-id="1b222-193">Interfejs użytkownika Swagger wyświetla wewnętrzny tekst `<summary>` elementu poprzedniego kodu:</span><span class="sxs-lookup"><span data-stu-id="1b222-193">The Swagger UI displays the inner text of the preceding code's `<summary>` element:</span></span>

![Interfejs użytkownika Swagger z komentarzem XML "Usuwa określony Element TodoItem".](web-api-help-pages-using-swagger/_static/triple-slash-comments.png)

<span data-ttu-id="1b222-196">Interfejs użytkownika jest napędzany przez wygenerowany schemat JSON:</span><span class="sxs-lookup"><span data-stu-id="1b222-196">The UI is driven by the generated JSON schema:</span></span>

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

<span data-ttu-id="1b222-197">Dodaj [ \<uwagi>](/dotnet/csharp/programming-guide/xmldoc/remarks) element do `Create` dokumentacji metody akcji.</span><span class="sxs-lookup"><span data-stu-id="1b222-197">Add a [\<remarks>](/dotnet/csharp/programming-guide/xmldoc/remarks) element to the `Create` action method documentation.</span></span> <span data-ttu-id="1b222-198">Uzupełnia informacje określone `<summary>` w elemencie i zapewnia bardziej niezawodny interfejs użytkownika Swagger.</span><span class="sxs-lookup"><span data-stu-id="1b222-198">It supplements information specified in the `<summary>` element and provides a more robust Swagger UI.</span></span> <span data-ttu-id="1b222-199">Zawartość `<remarks>` elementu może składać się z tekstu, JSON lub XML.</span><span class="sxs-lookup"><span data-stu-id="1b222-199">The `<remarks>` element content can consist of text, JSON, or XML.</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]

::: moniker-end

<span data-ttu-id="1b222-200">Zwróć uwagę na ulepszenia interfejsu użytkownika z tymi dodatkowymi komentarzami:</span><span class="sxs-lookup"><span data-stu-id="1b222-200">Notice the UI enhancements with these additional comments:</span></span>

![Interfejs użytkownika Swagger z dodatkowymi komentarzami](web-api-help-pages-using-swagger/_static/xml-comments-extended.png)

### <a name="data-annotations"></a><span data-ttu-id="1b222-202">Adnotacje danych</span><span class="sxs-lookup"><span data-stu-id="1b222-202">Data annotations</span></span>

<span data-ttu-id="1b222-203">Oznacz model atrybutami znajdującymi się w obszarze nazw [System.ComponentModel.DataAnnotations,](/dotnet/api/system.componentmodel.dataannotations) aby ułatwić prowadzenie składników interfejsu użytkownika Swagger.</span><span class="sxs-lookup"><span data-stu-id="1b222-203">Mark the model with attributes, found in the [System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) namespace, to help drive the Swagger UI components.</span></span>

<span data-ttu-id="1b222-204">Dodaj `[Required]` atrybut do `Name` właściwości `TodoItem` klasy:</span><span class="sxs-lookup"><span data-stu-id="1b222-204">Add the `[Required]` attribute to the `Name` property of the `TodoItem` class:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Models/TodoItem.cs?highlight=10)]

<span data-ttu-id="1b222-205">Obecność tego atrybutu zmienia zachowanie interfejsu użytkownika i zmienia podstawowy schemat JSON:</span><span class="sxs-lookup"><span data-stu-id="1b222-205">The presence of this attribute changes the UI behavior and alters the underlying JSON schema:</span></span>

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

<span data-ttu-id="1b222-206">Dodaj `[Produces("application/json")]` atrybut do kontrolera interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="1b222-206">Add the `[Produces("application/json")]` attribute to the API controller.</span></span> <span data-ttu-id="1b222-207">Jego celem jest stwierdzenie, że działania administratora obsługują typ treści odpowiedzi *aplikacji/json:*</span><span class="sxs-lookup"><span data-stu-id="1b222-207">Its purpose is to declare that the controller's actions support a response content type of *application/json*:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_TodoController&highlight=1)]

::: moniker-end

<span data-ttu-id="1b222-208">Z listy rozwijanej **Typ zawartości odpowiedzi** wybiera ten typ zawartości jako domyślny dla akcji GET kontrolera:</span><span class="sxs-lookup"><span data-stu-id="1b222-208">The **Response Content Type** drop-down selects this content type as the default for the controller's GET actions:</span></span>

![Interfejs użytkownika swagger z domyślnym typem zawartości odpowiedzi](web-api-help-pages-using-swagger/_static/json-response-content-type.png)

<span data-ttu-id="1b222-210">Wraz ze wzrostem użycia adnotacji danych w interfejsie API sieci Web strony pomocy interfejsu użytkownika i interfejsu API stają się bardziej opisowe i przydatne.</span><span class="sxs-lookup"><span data-stu-id="1b222-210">As the usage of data annotations in the web API increases, the UI and API help pages become more descriptive and useful.</span></span>

### <a name="describe-response-types"></a><span data-ttu-id="1b222-211">Opis typów odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="1b222-211">Describe response types</span></span>

<span data-ttu-id="1b222-212">Deweloperzy zużywający internetowy interfejs API są najbardziej&mdash;zaniepokojeni zwracanymi typami odpowiedzi i kodami błędów (jeśli nie standardowymi).</span><span class="sxs-lookup"><span data-stu-id="1b222-212">Developers consuming a web API are most concerned with what's returned&mdash;specifically response types and error codes (if not standard).</span></span> <span data-ttu-id="1b222-213">Typy odpowiedzi i kody błędów są oznaczone w komentarzach XML i adnotacjach danych.</span><span class="sxs-lookup"><span data-stu-id="1b222-213">The response types and error codes are denoted in the XML comments and data annotations.</span></span>

<span data-ttu-id="1b222-214">Akcja `Create` zwraca kod stanu HTTP 201 po sukcesie.</span><span class="sxs-lookup"><span data-stu-id="1b222-214">The `Create` action returns an HTTP 201 status code on success.</span></span> <span data-ttu-id="1b222-215">Kod stanu HTTP 400 jest zwracany, gdy treść zaksięgowanego żądania ma wartość null.</span><span class="sxs-lookup"><span data-stu-id="1b222-215">An HTTP 400 status code is returned when the posted request body is null.</span></span> <span data-ttu-id="1b222-216">Bez odpowiedniej dokumentacji w interfejsie użytkownika Swagger konsument nie zna tych oczekiwanych wyników.</span><span class="sxs-lookup"><span data-stu-id="1b222-216">Without proper documentation in the Swagger UI, the consumer lacks knowledge of these expected outcomes.</span></span> <span data-ttu-id="1b222-217">Rozwiązać ten problem, dodając wyróżnione wiersze w poniższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="1b222-217">Fix that problem by adding the highlighted lines in the following example:</span></span>

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]

::: moniker-end

<span data-ttu-id="1b222-218">Interfejs użytkownika Swagger teraz wyraźnie dokumentuje oczekiwane kody odpowiedzi HTTP:</span><span class="sxs-lookup"><span data-stu-id="1b222-218">The Swagger UI now clearly documents the expected HTTP response codes:</span></span>

![Interfejs użytkownika swagger pokazujący opis klasy odpowiedzi POST "Zwraca nowo utworzony element Todo" i "400 — jeśli element ma wartość null" dla kodu stanu i przyczyny w obszarze Wiadomości odpowiedzi](web-api-help-pages-using-swagger/_static/data-annotations-response-types.png)

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="1b222-220">W ASP.NET Core 2.2 lub nowszym konwencje mogą być używane jako `[ProducesResponseType]`alternatywa dla jawnego dekorowania poszczególnych działań za pomocą programu .</span><span class="sxs-lookup"><span data-stu-id="1b222-220">In ASP.NET Core 2.2 or later, conventions can be used as an alternative to explicitly decorating individual actions with `[ProducesResponseType]`.</span></span> <span data-ttu-id="1b222-221">Aby uzyskać więcej informacji, zobacz <xref:web-api/advanced/conventions>.</span><span class="sxs-lookup"><span data-stu-id="1b222-221">For more information, see <xref:web-api/advanced/conventions>.</span></span>

::: moniker-end

### <a name="customize-the-ui"></a><span data-ttu-id="1b222-222">Dostosowywanie interfejsu użytkownika</span><span class="sxs-lookup"><span data-stu-id="1b222-222">Customize the UI</span></span>

<span data-ttu-id="1b222-223">Interfejs użytkownika akcji jest zarówno funkcjonalny, jak i reprezentacyjny.</span><span class="sxs-lookup"><span data-stu-id="1b222-223">The stock UI is both functional and presentable.</span></span> <span data-ttu-id="1b222-224">Jednak strony dokumentacji interfejsu API powinny reprezentować Twoją markę lub motyw.</span><span class="sxs-lookup"><span data-stu-id="1b222-224">However, API documentation pages should represent your brand or theme.</span></span> <span data-ttu-id="1b222-225">Znakowanie składników Swashbuckle wymaga dodania zasobów do obsługi plików statycznych i tworzenia struktury folderów do obsługi tych plików.</span><span class="sxs-lookup"><span data-stu-id="1b222-225">Branding the Swashbuckle components requires adding the resources to serve static files and building the folder structure to host those files.</span></span>

<span data-ttu-id="1b222-226">Jeśli obiekt .NET Framework lub .NET Core 1.x jest skierowany do projektu, dodaj do projektu pakiet [NuGet microsoft.AspNetCore.StaticFiles:](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles)</span><span class="sxs-lookup"><span data-stu-id="1b222-226">If targeting .NET Framework or .NET Core 1.x, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles) NuGet package to the project:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.StaticFiles" Version="2.0.0" />
```

<span data-ttu-id="1b222-227">Poprzedni pakiet NuGet jest już zainstalowany, jeśli jest skierowany na .NET Core 2.x i używa [metapakiety](xref:fundamentals/metapackage).</span><span class="sxs-lookup"><span data-stu-id="1b222-227">The preceding NuGet package is already installed if targeting .NET Core 2.x and using the [metapackage](xref:fundamentals/metapackage).</span></span>

<span data-ttu-id="1b222-228">Włącz oprogramowanie pośredniczące plików statycznych:</span><span class="sxs-lookup"><span data-stu-id="1b222-228">Enable Static File Middleware:</span></span>

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/3.0/TodoApi.Swashbuckle/Startup.cs?name=snippet_Configure&highlight=3)]

::: moniker-end

<span data-ttu-id="1b222-229">Uzyskaj zawartość folderu *dist* z [repozytorium Swagger UI GitHub](https://github.com/swagger-api/swagger-ui/tree/master/dist).</span><span class="sxs-lookup"><span data-stu-id="1b222-229">Acquire the contents of the *dist* folder from the [Swagger UI GitHub repository](https://github.com/swagger-api/swagger-ui/tree/master/dist).</span></span> <span data-ttu-id="1b222-230">Ten folder zawiera niezbędne zasoby dla strony interfejsu użytkownika swagger.</span><span class="sxs-lookup"><span data-stu-id="1b222-230">This folder contains the necessary assets for the Swagger UI page.</span></span>

<span data-ttu-id="1b222-231">Utwórz folder *wwwroot/swagger/ui* i skopiuj do niego zawartość folderu *dist.*</span><span class="sxs-lookup"><span data-stu-id="1b222-231">Create a *wwwroot/swagger/ui* folder, and copy into it the contents of the *dist* folder.</span></span>

<span data-ttu-id="1b222-232">Utwórz plik *custom.css* w *wwwroot/swagger/ui*z następującym CSS, aby dostosować nagłówek strony:</span><span class="sxs-lookup"><span data-stu-id="1b222-232">Create a *custom.css* file, in *wwwroot/swagger/ui*, with the following CSS to customize the page header:</span></span>

[!code-css[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/wwwroot/swagger/ui/custom.css)]

<span data-ttu-id="1b222-233">Odwołanie *do pliku custom.css* w pliku *index.html* wewnątrz folderu interfejsu użytkownika, po innych plikach CSS:</span><span class="sxs-lookup"><span data-stu-id="1b222-233">Reference *custom.css* in the *index.html* file inside ui folder, after any other CSS files:</span></span>

[!code-html[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.Swashbuckle/wwwroot/swagger/ui/index.html?name=snippet_SwaggerUiCss&highlight=3)]

<span data-ttu-id="1b222-234">Przejdź do strony *index.html* pod adresem `http://localhost:<port>/swagger/ui/index.html`.</span><span class="sxs-lookup"><span data-stu-id="1b222-234">Browse to the *index.html* page at `http://localhost:<port>/swagger/ui/index.html`.</span></span> <span data-ttu-id="1b222-235">Wprowadź `https://localhost:<port>/swagger/v1/swagger.json` w polu tekstowym nagłówka i kliknij przycisk **Eksploruj.**</span><span class="sxs-lookup"><span data-stu-id="1b222-235">Enter `https://localhost:<port>/swagger/v1/swagger.json` in the header's textbox, and click the **Explore** button.</span></span> <span data-ttu-id="1b222-236">Wynikowa strona wygląda następująco:</span><span class="sxs-lookup"><span data-stu-id="1b222-236">The resulting page looks as follows:</span></span>

![Interfejs użytkownika swagger z niestandardowym tytułem nagłówka](web-api-help-pages-using-swagger/_static/custom-header.png)

<span data-ttu-id="1b222-238">Strona może zrobić znacznie więcej.</span><span class="sxs-lookup"><span data-stu-id="1b222-238">There's much more you can do with the page.</span></span> <span data-ttu-id="1b222-239">Zobacz pełne możliwości zasobów interfejsu użytkownika w [repozytorium GitHub interfejsu użytkownika użytkownika użytkownika.](https://github.com/swagger-api/swagger-ui)</span><span class="sxs-lookup"><span data-stu-id="1b222-239">See the full capabilities for the UI resources at the [Swagger UI GitHub repository](https://github.com/swagger-api/swagger-ui).</span></span>
