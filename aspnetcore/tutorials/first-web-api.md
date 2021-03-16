---
title: 'Samouczek: Tworzenie internetowego interfejsu API za pomocą ASP.NET Core'
author: rick-anderson
description: Dowiedz się, jak utworzyć internetowy interfejs API za pomocą ASP.NET Core.
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 02/04/2021
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
- Models
uid: tutorials/first-web-api
ms.openlocfilehash: f80c574ddb85c50713e36c7a2cceb826863c7321
ms.sourcegitcommit: 00368bb6a5420983beaced5b62dabc1f94abdeba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103557845"
---
# <a name="tutorial-create-a-web-api-with-aspnet-core"></a><span data-ttu-id="3aa1f-103">Samouczek: Tworzenie internetowego interfejsu API za pomocą ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3aa1f-103">Tutorial: Create a web API with ASP.NET Core</span></span>

<span data-ttu-id="3aa1f-104">Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirka Larkin](https://twitter.com/serpent5)i [Jan Wasson](https://github.com/mikewasson)</span><span class="sxs-lookup"><span data-stu-id="3aa1f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), and [Mike Wasson](https://github.com/mikewasson)</span></span>

<span data-ttu-id="3aa1f-105">Ten samouczek uczy się podstaw tworzenia interfejsu API sieci Web za pomocą ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-105">This tutorial teaches the basics of building a web API with ASP.NET Core.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="3aa1f-106">Ten samouczek zawiera informacje na temat wykonywania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3aa1f-107">Utwórz projekt interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-107">Create a web API project.</span></span>
> * <span data-ttu-id="3aa1f-108">Dodaj klasę modelu i kontekst bazy danych.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-108">Add a model class and a database context.</span></span>
> * <span data-ttu-id="3aa1f-109">Tworzy szkielet kontrolera z metodami CRUD.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-109">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="3aa1f-110">Skonfiguruj Routing, ścieżki URL i wartości zwracane.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-110">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="3aa1f-111">Wywołaj interfejs API sieci Web za pomocą programu Poster.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-111">Call the web API with Postman.</span></span>

<span data-ttu-id="3aa1f-112">Na końcu znajduje się internetowy interfejs API, który może zarządzać elementami do wykonania przechowywanymi w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-112">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="3aa1f-113">Omówienie</span><span class="sxs-lookup"><span data-stu-id="3aa1f-113">Overview</span></span>

<span data-ttu-id="3aa1f-114">Ten samouczek tworzy następujący interfejs API:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-114">This tutorial creates the following API:</span></span>

|<span data-ttu-id="3aa1f-115">Interfejs API</span><span class="sxs-lookup"><span data-stu-id="3aa1f-115">API</span></span> | <span data-ttu-id="3aa1f-116">Opis</span><span class="sxs-lookup"><span data-stu-id="3aa1f-116">Description</span></span> | <span data-ttu-id="3aa1f-117">Treść żądania</span><span class="sxs-lookup"><span data-stu-id="3aa1f-117">Request body</span></span> | <span data-ttu-id="3aa1f-118">Treść odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="3aa1f-118">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="3aa1f-119">Pobierz wszystkie elementy do wykonania</span><span class="sxs-lookup"><span data-stu-id="3aa1f-119">Get all to-do items</span></span> | <span data-ttu-id="3aa1f-120">Brak</span><span class="sxs-lookup"><span data-stu-id="3aa1f-120">None</span></span> | <span data-ttu-id="3aa1f-121">Tablica elementów do wykonania</span><span class="sxs-lookup"><span data-stu-id="3aa1f-121">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="3aa1f-122">Pobieranie elementu według identyfikatora</span><span class="sxs-lookup"><span data-stu-id="3aa1f-122">Get an item by ID</span></span> | <span data-ttu-id="3aa1f-123">Brak</span><span class="sxs-lookup"><span data-stu-id="3aa1f-123">None</span></span> | <span data-ttu-id="3aa1f-124">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="3aa1f-124">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="3aa1f-125">Dodaj nowy element</span><span class="sxs-lookup"><span data-stu-id="3aa1f-125">Add a new item</span></span> | <span data-ttu-id="3aa1f-126">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="3aa1f-126">To-do item</span></span> | <span data-ttu-id="3aa1f-127">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="3aa1f-127">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="3aa1f-128">Aktualizowanie istniejącego elementu &nbsp;</span><span class="sxs-lookup"><span data-stu-id="3aa1f-128">Update an existing item &nbsp;</span></span> | <span data-ttu-id="3aa1f-129">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="3aa1f-129">To-do item</span></span> | <span data-ttu-id="3aa1f-130">Brak</span><span class="sxs-lookup"><span data-stu-id="3aa1f-130">None</span></span> |
|<span data-ttu-id="3aa1f-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="3aa1f-131">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="3aa1f-132">Usuń element &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="3aa1f-132">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="3aa1f-133">Brak</span><span class="sxs-lookup"><span data-stu-id="3aa1f-133">None</span></span> | <span data-ttu-id="3aa1f-134">Brak</span><span class="sxs-lookup"><span data-stu-id="3aa1f-134">None</span></span>|

<span data-ttu-id="3aa1f-135">Na poniższym diagramie przedstawiono projekt aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-135">The following diagram shows the design of the app.</span></span>

![Klient jest reprezentowany przez pole po lewej stronie.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="3aa1f-141">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="3aa1f-141">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3aa1f-142">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3aa1f-142">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="3aa1f-143">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3aa1f-143">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3aa1f-144">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="3aa1f-144">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="3aa1f-145">Tworzenie projektu sieci Web</span><span class="sxs-lookup"><span data-stu-id="3aa1f-145">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3aa1f-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3aa1f-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3aa1f-147">Z menu **plik** wybierz pozycję **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-147">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="3aa1f-148">Wybierz szablon **internetowego interfejsu API ASP.NET Core** i kliknij przycisk **dalej**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-148">Select the **ASP.NET Core Web API** template and click **Next**.</span></span>
* <span data-ttu-id="3aa1f-149">Nazwij projekt *TodoApi* i kliknij pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-149">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="3aa1f-150">W oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** upewnij się, że wybrano opcję **.net Core** i **ASP.NET Core 5,0** .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-150">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 5.0** are selected.</span></span> <span data-ttu-id="3aa1f-151">Wybierz szablon **interfejsu API** i kliknij przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-151">Select the **API** template and click **Create**.</span></span>

![Okno dialogowe programu VS New Project](first-web-api/_static/5/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="3aa1f-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3aa1f-153">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3aa1f-154">Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="3aa1f-154">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="3aa1f-155">Zmień katalog ( `cd` ) do folderu, który będzie zawierać folder projektu.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-155">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="3aa1f-156">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-156">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="3aa1f-157">Gdy zostanie wyświetlone okno dialogowe z pytaniem, czy chcesz dodać wymagane zasoby do projektu, wybierz opcję **tak**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-157">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="3aa1f-158">Poprzednie polecenia:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-158">The preceding commands:</span></span>

  * <span data-ttu-id="3aa1f-159">Tworzy nowy projekt internetowego interfejsu API i otwiera go w Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-159">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="3aa1f-160">Dodaje pakiety NuGet, które są wymagane w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-160">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3aa1f-161">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="3aa1f-161">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3aa1f-162">Wybierz pozycję **plik** > **nowe rozwiązanie**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-162">Select **File** > **New Solution**.</span></span>

  ![macOS nowe rozwiązanie](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="3aa1f-164">W Visual Studio dla komputerów Mac starszej niż wersja 8,6 Wybierz pozycję  >    >  **interfejs API** aplikacji .NET Core  >  .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-164">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="3aa1f-165">W wersji 8,6 lub nowszej wybierz pozycję   >    >  **interfejs API**  >  aplikacji sieci Web i konsoli.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-165">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![Wybór szablonu interfejsu API macOS](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="3aa1f-167">W oknie dialogowym **Konfigurowanie nowego interfejsu API sieci Web ASP.NET Core** wybierz najnowszą platformę **docelową**.NET Core 5. x.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-167">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 5.x **Target Framework**.</span></span> <span data-ttu-id="3aa1f-168">Wybierz opcję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-168">Select **Next**.</span></span>

* <span data-ttu-id="3aa1f-169">Wprowadź *TodoApi* jako **nazwę projektu** , a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-169">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![okno dialogowe konfiguracji](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="3aa1f-171">Otwórz Terminal poleceń w folderze projektu i uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-171">Open a command terminal in the project folder and run the following command:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-project"></a><span data-ttu-id="3aa1f-172">Testowanie projektu</span><span class="sxs-lookup"><span data-stu-id="3aa1f-172">Test the project</span></span>

<span data-ttu-id="3aa1f-173">Szablon projektu tworzy `WeatherForecast` interfejs API z obsługą [struktury Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span><span class="sxs-lookup"><span data-stu-id="3aa1f-173">The project template creates a `WeatherForecast` API with support for [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3aa1f-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3aa1f-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3aa1f-175">Naciśnij klawisze CTRL + F5, aby uruchomić bez debugera.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-175">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="3aa1f-176">Zostanie uruchomiony program Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-176">Visual Studio launches:</span></span>

* <span data-ttu-id="3aa1f-177">Serwer sieci Web IIS Express.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-177">The IIS Express web server.</span></span>
* <span data-ttu-id="3aa1f-178">Domyślna przeglądarka i nawiguje do `https://localhost:<port>/swagger/index.html` , gdzie `<port>` jest losowo wybranym numerem portu.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-178">The default browser and navigates to `https://localhost:<port>/swagger/index.html`, where `<port>` is a randomly chosen port number.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3aa1f-179">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3aa1f-179">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/trustCertVSC.md)]

<span data-ttu-id="3aa1f-180">Naciśnij klawisze CTRL + F5, aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-180">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="3aa1f-181">W przeglądarce przejdź do następującego adresu URL: [https://localhost:5001/swagger](https://localhost:5001/swagger)</span><span class="sxs-lookup"><span data-stu-id="3aa1f-181">In a browser, go to following URL: [https://localhost:5001/swagger](https://localhost:5001/swagger)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3aa1f-182">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="3aa1f-182">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="3aa1f-183">Wybierz pozycję **Uruchom**  >  **Rozpocznij debugowanie** , aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-183">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="3aa1f-184">Visual Studio dla komputerów Mac uruchamia przeglądarkę i nawiguje do `https://localhost:<port>` , gdzie `<port>` jest losowo wybranym numerem portu.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-184">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="3aa1f-185">Zwracany jest błąd HTTP 404 (nie znaleziono).</span><span class="sxs-lookup"><span data-stu-id="3aa1f-185">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="3aa1f-186">Dołącz `/swagger` do adresu URL (Zmień adres URL na `https://localhost:<port>/swagger` ).</span><span class="sxs-lookup"><span data-stu-id="3aa1f-186">Append `/swagger` to the URL (change the URL to `https://localhost:<port>/swagger`).</span></span>

---

<span data-ttu-id="3aa1f-187">`/swagger/index.html`Zostanie wyświetlona strona Swagger.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-187">The Swagger page `/swagger/index.html` is displayed.</span></span> <span data-ttu-id="3aa1f-188">Wybierz pozycję **Pobierz**  >  **spróbuj**  >  **wykonać** operację.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-188">Select **GET** > **Try it out** > **Execute**.</span></span> <span data-ttu-id="3aa1f-189">Zostanie wyświetlona strona:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-189">The page displays:</span></span>

* <span data-ttu-id="3aa1f-190">[Zwinięcie](https://curl.haxx.se/) polecenie do testowania interfejsu API WeatherForecast.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-190">The [Curl](https://curl.haxx.se/) command to test the WeatherForecast API.</span></span>
* <span data-ttu-id="3aa1f-191">Adres URL służący do testowania interfejsu API WeatherForecast.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-191">The URL to test the WeatherForecast API.</span></span>
* <span data-ttu-id="3aa1f-192">Kod odpowiedzi, treść i nagłówki.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-192">The response code, body, and headers.</span></span>
* <span data-ttu-id="3aa1f-193">Pole listy rozwijanej z typami nośników oraz przykładową wartością i schematem.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-193">A drop down list box with media types and the example value and schema.</span></span>

<!-- Review: Do we care the IE generates several errors. It shows the data, but with  Unrecognized response type; displaying content as text.
-->
<span data-ttu-id="3aa1f-194">Struktura Swagger służy do generowania przydatnej dokumentacji i stron pomocy dla interfejsów API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-194">Swagger is used to generate useful documentation and help pages for web APIs.</span></span> <span data-ttu-id="3aa1f-195">Ten samouczek koncentruje się na tworzeniu interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-195">This tutorial focuses on creating a web API.</span></span> <span data-ttu-id="3aa1f-196">Aby uzyskać więcej informacji na temat struktury Swagger, zobacz <xref:tutorials/web-api-help-pages-using-swagger> .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-196">For more information on Swagger, see <xref:tutorials/web-api-help-pages-using-swagger>.</span></span>

<span data-ttu-id="3aa1f-197">Skopiuj i wklej **adres URL żądania** w przeglądarce:  `https://localhost:<port>/WeatherForecast`</span><span class="sxs-lookup"><span data-stu-id="3aa1f-197">Copy and paste the **Request URL** in the browser:  `https://localhost:<port>/WeatherForecast`</span></span>

<span data-ttu-id="3aa1f-198">Zwracany jest kod JSON podobny do następującego:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-198">JSON similar to the following is returned:</span></span>

```json
[
    {
        "date": "2019-07-16T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2019-07-17T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2019-07-18T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2019-07-19T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": "2019-07-20T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

### <a name="update-the-launchurl"></a><span data-ttu-id="3aa1f-199">Aktualizowanie launchUrl</span><span class="sxs-lookup"><span data-stu-id="3aa1f-199">Update the launchUrl</span></span>

<span data-ttu-id="3aa1f-200">W *Properties\launchSettings.json* Update `launchUrl` od `"swagger"` do `"api/TodoItems"` :</span><span class="sxs-lookup"><span data-stu-id="3aa1f-200">In *Properties\launchSettings.json*, update `launchUrl` from `"swagger"` to `"api/TodoItems"`:</span></span>

```json
"launchUrl": "api/TodoItems",
```

<span data-ttu-id="3aa1f-201">Ze względu na to, że program Swagger został usunięty, poprzedzający znacznik zmienia adres URL, który jest uruchamiany w metodzie GET kontrolera dodanej w poniższych sekcjach.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-201">Because Swagger has been removed, the preceding markup changes the URL that is launched to the GET method of the controller added in the following sections.</span></span>

## <a name="add-a-model-class"></a><span data-ttu-id="3aa1f-202">Dodaj klasę modelu</span><span class="sxs-lookup"><span data-stu-id="3aa1f-202">Add a model class</span></span>

<span data-ttu-id="3aa1f-203">*Model* to zestaw klas, które reprezentują dane zarządzane przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-203">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="3aa1f-204">Model tej aplikacji jest pojedynczą `TodoItem` klasą.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-204">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3aa1f-205">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3aa1f-205">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3aa1f-206">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-206">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="3aa1f-207">Wybierz pozycję **Dodaj**  >  **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-207">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="3aa1f-208">Nazwij folder *Models* .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-208">Name the folder *Models*.</span></span>

* <span data-ttu-id="3aa1f-209">Kliknij prawym przyciskiem myszy *Models* folder i wybierz polecenie **Dodaj**  >  **klasę**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-209">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="3aa1f-210">Nadaj klasie nazwę *TodoItem* i wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-210">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="3aa1f-211">Zastąp kod szablonu następującym:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-211">Replace the template code with the following:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3aa1f-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3aa1f-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3aa1f-213">Dodaj folder o nazwie *Models* .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-213">Add a folder named *Models*.</span></span>

* <span data-ttu-id="3aa1f-214">Dodaj `TodoItem` do *Models* folderu klasę o następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-214">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3aa1f-215">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="3aa1f-215">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3aa1f-216">Kliknij prawym przyciskiem myszy projekt.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-216">Right-click the project.</span></span> <span data-ttu-id="3aa1f-217">Wybierz pozycję **Dodaj**  >  **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-217">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="3aa1f-218">Nazwij folder *Models* .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-218">Name the folder *Models*.</span></span>

  ![Nowy folder](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="3aa1f-220">Kliknij prawym przyciskiem myszy *Models* folder, a następnie wybierz pozycję **Dodaj** > **nowy plik** > **ogólna** > **pusta Klasa**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-220">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="3aa1f-221">Nazwij klasę *TodoItem*, a następnie kliknij pozycję **New (nowy**).</span><span class="sxs-lookup"><span data-stu-id="3aa1f-221">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="3aa1f-222">Zastąp kod szablonu następującym:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-222">Replace the template code with the following:</span></span>

---

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="3aa1f-223">`Id`Właściwość działa jako unikatowy klucz w relacyjnej bazie danych.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-223">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="3aa1f-224">Klasy modelu mogą przejść do dowolnego miejsca w projekcie, ale *Models* folder jest używany przez Konwencję.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-224">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="3aa1f-225">Dodawanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="3aa1f-225">Add a database context</span></span>

<span data-ttu-id="3aa1f-226">*Kontekst bazy danych* jest główną klasą, która koordynuje Entity Framework funkcji dla modelu danych.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-226">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="3aa1f-227">Ta klasa jest tworzona przez wyprowadzanie z <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> klasy.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-227">This class is created by deriving from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3aa1f-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3aa1f-228">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="3aa1f-229">Dodawanie pakietów NuGet</span><span class="sxs-lookup"><span data-stu-id="3aa1f-229">Add NuGet packages</span></span>

* <span data-ttu-id="3aa1f-230">W menu **Narzędzia** wybierz pozycję **menedżer pakietów NuGet > zarządzanie pakietami NuGet dla rozwiązania**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-230">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="3aa1f-231">Wybierz kartę **Przeglądaj** , a następnie wprowadź `Microsoft.EntityFrameworkCore.InMemory` w polu wyszukiwania.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-231">Select the **Browse** tab, and then enter `Microsoft.EntityFrameworkCore.InMemory` in the search box.</span></span>
* <span data-ttu-id="3aa1f-232">Wybierz `Microsoft.EntityFrameworkCore.InMemory` w okienku po lewej stronie.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-232">Select `Microsoft.EntityFrameworkCore.InMemory` in the left pane.</span></span>
* <span data-ttu-id="3aa1f-233">Zaznacz pole wyboru **projekt** w prawym okienku, a następnie wybierz pozycję **Zainstaluj**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-233">Select the **Project** check box in the right pane and then select **Install**.</span></span>

![Menedżer pakietów NuGet](first-web-api/_static/5/vsNuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="3aa1f-235">Dodawanie kontekstu bazy danych TodoContext</span><span class="sxs-lookup"><span data-stu-id="3aa1f-235">Add the TodoContext database context</span></span>

* <span data-ttu-id="3aa1f-236">Kliknij prawym przyciskiem myszy *Models* folder i wybierz polecenie **Dodaj**  >  **klasę**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-236">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="3aa1f-237">Nadaj klasie nazwę *TodoContext* i kliknij przycisk **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-237">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3aa1f-238">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="3aa1f-238">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="3aa1f-239">Dodaj `TodoContext` klasę do *Models* folderu.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-239">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="3aa1f-240">Wprowadź następujący kod:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-240">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="3aa1f-241">Rejestrowanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="3aa1f-241">Register the database context</span></span>

<span data-ttu-id="3aa1f-242">W ASP.NET Core usługi, takie jak kontekst bazy danych, muszą być zarejestrowane z kontenerem [iniekcji zależności (di)](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-242">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="3aa1f-243">Kontener udostępnia usługę kontrolerom.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-243">The container provides the service to controllers.</span></span>

<span data-ttu-id="3aa1f-244">Zaktualizuj *Startup.cs* przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-244">Update *Startup.cs* with the following code:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="3aa1f-245">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-245">The preceding code:</span></span>

* <span data-ttu-id="3aa1f-246">Usuwa wywołania struktury Swagger.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-246">Removes the Swagger calls.</span></span>
* <span data-ttu-id="3aa1f-247">Usuwa nieużywane `using` deklaracje.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-247">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="3aa1f-248">Dodaje kontekst bazy danych do kontenera DI.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-248">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="3aa1f-249">Określa, że kontekst bazy danych będzie używać bazy danych w pamięci.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-249">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="3aa1f-250">Tworzenie szkieletu kontrolera</span><span class="sxs-lookup"><span data-stu-id="3aa1f-250">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3aa1f-251">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3aa1f-251">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3aa1f-252">Kliknij prawym przyciskiem myszy folder *controllers* .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-252">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="3aa1f-253">Wybierz pozycję **Dodaj** > **nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-253">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="3aa1f-254">Wybierz pozycję **kontroler interfejsu API z akcjami, używając Entity Framework**, a następnie wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-254">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="3aa1f-255">Na stronie **Dodawanie kontrolera interfejsu API z akcjami przy użyciu Entity Framework** dialogowego:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-255">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="3aa1f-256">Wybierz pozycję **TodoItem (TodoApi. Models )** w **klasie model**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-256">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="3aa1f-257">Wybierz pozycję **TodoContext (TodoApi. Models )** w **klasie kontekstu danych**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-257">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="3aa1f-258">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-258">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3aa1f-259">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="3aa1f-259">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="3aa1f-260">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-260">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="3aa1f-261">Poprzednie polecenia:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-261">The preceding commands:</span></span>

* <span data-ttu-id="3aa1f-262">Dodaj pakiety NuGet wymagane do tworzenia szkieletów.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-262">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="3aa1f-263">Instaluje aparat tworzenia szkieletu ( `dotnet-aspnet-codegenerator` ).</span><span class="sxs-lookup"><span data-stu-id="3aa1f-263">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="3aa1f-264">Szkielety `TodoItemsController` .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-264">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="3aa1f-265">Wygenerowany kod:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-265">The generated code:</span></span>

* <span data-ttu-id="3aa1f-266">Oznacza klasę [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atrybutem.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-266">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="3aa1f-267">Ten atrybut wskazuje, że kontroler odpowiada na żądania interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-267">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="3aa1f-268">Aby uzyskać informacje o określonych zachowaniach, które włącza atrybut, zobacz <xref:web-api/index> .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-268">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="3aa1f-269">Używa funkcji DI do iniekcji kontekstu bazy danych ( `TodoContext` ) do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-269">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="3aa1f-270">Kontekst bazy danych jest używany w każdej z metod [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) w kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-270">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="3aa1f-271">Szablony ASP.NET Core dla:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-271">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="3aa1f-272">Kontrolery z widokami obejmują `[action]` szablon trasy.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-272">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="3aa1f-273">Kontrolery interfejsu API nie należą `[action]` do szablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-273">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="3aa1f-274">Gdy `[action]` token nie znajduje się w szablonie trasy, nazwa [akcji](xref:mvc/controllers/routing#action) jest wykluczona z trasy.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-274">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="3aa1f-275">Oznacza to, że nazwa metody skojarzonej z akcją nie jest używana w zgodnej trasie.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-275">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="update-the-posttodoitem-create-method"></a><span data-ttu-id="3aa1f-276">Aktualizowanie metody PostTodoItem Create</span><span class="sxs-lookup"><span data-stu-id="3aa1f-276">Update the PostTodoItem create method</span></span>

<span data-ttu-id="3aa1f-277">Zaktualizuj instrukcję return w, `PostTodoItem` Aby użyć operatora [nameof](/dotnet/csharp/language-reference/operators/nameof) :</span><span class="sxs-lookup"><span data-stu-id="3aa1f-277">Update the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="3aa1f-278">Poprzedni kod jest metodą POST protokołu HTTP, jak wskazano w [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) atrybucie.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-278">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="3aa1f-279">Metoda pobiera wartość elementu do wykonania z treści żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-279">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="3aa1f-280">Aby uzyskać więcej informacji, zobacz temat [Routing atrybutów z atrybutami http [Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="3aa1f-280">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="3aa1f-281"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>Metoda:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-281">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="3aa1f-282">W razie powodzenia zwraca [kod stanu HTTP 201](https://developer.mozilla.org/docs/Web/HTTP/Status/201) .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-282">Returns an [HTTP 201 status code](https://developer.mozilla.org/docs/Web/HTTP/Status/201) if successful.</span></span> <span data-ttu-id="3aa1f-283">HTTP 201 to standardowa odpowiedź dla metody POST protokołu HTTP, która tworzy nowy zasób na serwerze.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-283">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="3aa1f-284">Dodaje nagłówek [lokalizacji](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) do odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-284">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="3aa1f-285">`Location`Nagłówek określa [Identyfikator URI](https://developer.mozilla.org/docs/Glossary/URI) nowo utworzonego elementu do wykonania.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-285">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="3aa1f-286">Aby uzyskać więcej informacji, zobacz [10.2.2 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="3aa1f-286">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="3aa1f-287">Odwołuje się do `GetTodoItem` akcji tworzenia `Location` identyfikatora URI nagłówka.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-287">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="3aa1f-288">`nameof`Słowo kluczowe języka C# służy do zapobiegania twardemu kodowaniu nazwy akcji w `CreatedAtAction` wywołaniu.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-288">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="3aa1f-289">Zainstaluj program Poster</span><span class="sxs-lookup"><span data-stu-id="3aa1f-289">Install Postman</span></span>

<span data-ttu-id="3aa1f-290">Ten samouczek używa programu do testowania interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-290">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="3aa1f-291">Zainstaluj program [Poster](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="3aa1f-291">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="3aa1f-292">Uruchom aplikację internetową.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-292">Start the web app.</span></span>
* <span data-ttu-id="3aa1f-293">Uruchom wpis.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-293">Start Postman.</span></span>
* <span data-ttu-id="3aa1f-294">Wyłącz **weryfikację certyfikatu SSL**</span><span class="sxs-lookup"><span data-stu-id="3aa1f-294">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="3aa1f-295">W  obszarze > **Ustawienia** pliku (karta **Ogólne** ) Wyłącz **weryfikację certyfikatu SSL**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-295">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="3aa1f-296">Po przetestowaniu kontrolera ponownie Włącz weryfikację certyfikatu SSL.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-296">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="3aa1f-297">Test PostTodoItem za pomocą programu Poster</span><span class="sxs-lookup"><span data-stu-id="3aa1f-297">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="3aa1f-298">Utwórz nowe żądanie.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-298">Create a new request.</span></span>
* <span data-ttu-id="3aa1f-299">Ustaw metodę HTTP na `POST` .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-299">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="3aa1f-300">Ustaw identyfikator URI na `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-300">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="3aa1f-301">Na przykład `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-301">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="3aa1f-302">Wybierz kartę **Treść**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-302">Select the **Body** tab.</span></span>
* <span data-ttu-id="3aa1f-303">Wybierz przycisk radiowy **RAW** .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-303">Select the **raw** radio button.</span></span>
* <span data-ttu-id="3aa1f-304">Ustaw typ na **JSON (Application/JSON)**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-304">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="3aa1f-305">W treści żądania wprowadź kod JSON dla elementu do wykonania:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-305">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="3aa1f-306">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-306">Select **Send**.</span></span>

  ![Ogłoś przy użyciu żądania Create](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri"></a><span data-ttu-id="3aa1f-308">Testowanie identyfikatora URI nagłówka lokalizacji</span><span class="sxs-lookup"><span data-stu-id="3aa1f-308">Test the location header URI</span></span>

<span data-ttu-id="3aa1f-309">Identyfikator URI nagłówka lokalizacji może zostać przetestowany w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-309">The location header URI can be tested in the browser.</span></span> <span data-ttu-id="3aa1f-310">Skopiuj i wklej identyfikator URI nagłówka lokalizacji do przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-310">Copy and paste the location header URI into the browser.</span></span>

<span data-ttu-id="3aa1f-311">Aby przetestować w programie Poster:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-311">To test in Postman:</span></span>

* <span data-ttu-id="3aa1f-312">Wybierz kartę **nagłówki** w okienku **odpowiedź** .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-312">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="3aa1f-313">Skopiuj wartość nagłówka **lokalizacji** :</span><span class="sxs-lookup"><span data-stu-id="3aa1f-313">Copy the **Location** header value:</span></span>

  ![Karta nagłówki w konsoli programu Poster](first-web-api/_static/3/create.png)

* <span data-ttu-id="3aa1f-315">Ustaw metodę HTTP na `GET` .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-315">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="3aa1f-316">Ustaw identyfikator URI na `https://localhost:<port>/api/TodoItems/1` .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-316">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="3aa1f-317">Na przykład `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-317">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="3aa1f-318">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-318">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="3aa1f-319">Badanie metod GET</span><span class="sxs-lookup"><span data-stu-id="3aa1f-319">Examine the GET methods</span></span>

<span data-ttu-id="3aa1f-320">Zaimplementowane są dwa punkty końcowe GET:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-320">Two GET endpoints are implemented:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="3aa1f-321">Przetestuj aplikację, wywołując dwa punkty końcowe z przeglądarki lub wpisu.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-321">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="3aa1f-322">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-322">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="3aa1f-323">Odpowiedź podobna do poniższego jest generowana przez wywołanie `GetTodoItems` :</span><span class="sxs-lookup"><span data-stu-id="3aa1f-323">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="3aa1f-324">Test get przy użyciu programu Poster</span><span class="sxs-lookup"><span data-stu-id="3aa1f-324">Test Get with Postman</span></span>

* <span data-ttu-id="3aa1f-325">Utwórz nowe żądanie.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-325">Create a new request.</span></span>
* <span data-ttu-id="3aa1f-326">Ustaw metodę HTTP, aby **uzyskać**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-326">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="3aa1f-327">Ustaw identyfikator URI żądania na `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-327">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="3aa1f-328">Na przykład `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-328">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="3aa1f-329">Ustaw **dwa widoki okienka** w programie Poster.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-329">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="3aa1f-330">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-330">Select **Send**.</span></span>

<span data-ttu-id="3aa1f-331">Ta aplikacja używa bazy danych w pamięci.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-331">This app uses an in-memory database.</span></span> <span data-ttu-id="3aa1f-332">Jeśli aplikacja zostanie zatrzymana i uruchomiona, poprzednie żądanie GET nie zwróci żadnych danych.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-332">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="3aa1f-333">Jeśli nie zostaną zwrócone żadne dane, [Opublikuj](#post) dane w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-333">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="3aa1f-334">Ścieżki routingu i adresów URL</span><span class="sxs-lookup"><span data-stu-id="3aa1f-334">Routing and URL paths</span></span>

<span data-ttu-id="3aa1f-335">Ten [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) atrybut oznacza metodę, która reaguje na żądanie HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-335">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="3aa1f-336">Ścieżka adresu URL dla każdej metody jest zbudowana w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-336">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="3aa1f-337">Zacznij od ciągu szablonu w `Route` atrybucie kontrolera:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-337">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="3aa1f-338">Zastąp `[controller]` nazwą kontrolera, którą Konwencją jest nazwa klasy kontrolera minus sufiks "Controller".</span><span class="sxs-lookup"><span data-stu-id="3aa1f-338">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="3aa1f-339">Dla tego przykładu nazwa klasy kontrolera to **TodoItems** Controller, więc nazwa kontrolera to "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="3aa1f-339">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="3aa1f-340">W ASP.NET Core [routingu](xref:mvc/controllers/routing) jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-340">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="3aa1f-341">Jeśli `[HttpGet]` atrybut ma szablon trasy (na przykład `[HttpGet("products")]` ), Dodaj go do ścieżki.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-341">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="3aa1f-342">Ten przykład nie używa szablonu.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-342">This sample doesn't use a template.</span></span> <span data-ttu-id="3aa1f-343">Aby uzyskać więcej informacji, zobacz temat [Routing atrybutów z atrybutami http [Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="3aa1f-343">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="3aa1f-344">W poniższej `GetTodoItem` metodzie `"{id}"` jest zmienną zastępczą dla unikatowego identyfikatora elementu do wykonania.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-344">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="3aa1f-345">Gdy `GetTodoItem` jest wywoływana, wartość `"{id}"` w adresie URL jest podawana do metody w jej `id` parametrze.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-345">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="3aa1f-346">Wartości zwracane</span><span class="sxs-lookup"><span data-stu-id="3aa1f-346">Return values</span></span>

<span data-ttu-id="3aa1f-347">Zwracany typ `GetTodoItems` `GetTodoItem` metod i jest [ \<T> typem ActionResult](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="3aa1f-347">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="3aa1f-348">ASP.NET Core automatycznie serializować obiektu do [formatu JSON](https://www.json.org/) i zapisuje kod JSON w treści komunikatu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-348">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="3aa1f-349">Kod odpowiedzi dla tego typu zwracanego to [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), przy założeniu, że nie istnieją Nieobsłużone wyjątki.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-349">The response code for this return type is [200 OK](https://developer.mozilla.org/docs/Web/HTTP/Status/200), assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="3aa1f-350">Nieobsłużone wyjątki są tłumaczone na błędy 5xx.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-350">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="3aa1f-351">`ActionResult` typy zwracane mogą reprezentować szeroką gamę kodów stanu HTTP.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-351">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="3aa1f-352">Na przykład `GetTodoItem` może zwracać dwie różne wartości stanu:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-352">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="3aa1f-353">Jeśli żaden element nie jest zgodny z żądanym IDENTYFIKATORem, metoda zwraca kod błędu [stanu 404](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-353">If no item matches the requested ID, the method returns a [404 status](https://developer.mozilla.org/docs/Web/HTTP/Status/404) <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="3aa1f-354">W przeciwnym razie metoda zwraca 200 z treścią odpowiedzi JSON.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-354">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="3aa1f-355">Zwracanie `item` wyników w odpowiedzi HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-355">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="3aa1f-356">Metoda PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="3aa1f-356">The PutTodoItem method</span></span>

<span data-ttu-id="3aa1f-357">Przeanalizuj metodę `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-357">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="3aa1f-358">`PutTodoItem` jest podobny do `PostTodoItem` , z tą różnicą, że używa protokołu HTTP Put.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-358">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="3aa1f-359">Odpowiedź to [204 (brak zawartości)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="3aa1f-359">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="3aa1f-360">Zgodnie ze specyfikacją protokołu HTTP żądanie PUT wymaga, aby klient wysłał całą zaktualizowaną jednostkę, a nie tylko te zmiany.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-360">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="3aa1f-361">Aby zapewnić obsługę częściowych aktualizacji, użyj [poprawki http](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="3aa1f-361">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="3aa1f-362">Jeśli wystąpi błąd podczas wywoływania `PutTodoItem` , wywołaj, `GET` Aby upewnić się, że w bazie danych znajduje się element.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-362">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="3aa1f-363">Testowanie metody PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="3aa1f-363">Test the PutTodoItem method</span></span>

<span data-ttu-id="3aa1f-364">Ten przykład korzysta z bazy danych w pamięci, która musi zostać zainicjowana za każdym razem, gdy aplikacja zostanie uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-364">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="3aa1f-365">Przed wykonaniem wywołania PUT musi istnieć element w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-365">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="3aa1f-366">Przed wykonaniem wywołania PUT należy wywołać metodę GET, aby upewnić się, że istnieje element w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-366">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="3aa1f-367">Zaktualizuj element do wykonania o identyfikatorze 1 i ustaw jego nazwę na `"feed fish"` :</span><span class="sxs-lookup"><span data-stu-id="3aa1f-367">Update the to-do item that has Id = 1 and set its name to `"feed fish"`:</span></span>

```json
  {
    "Id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="3aa1f-368">Na poniższej ilustracji przedstawiono aktualizację programu Poster:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-368">The following image shows the Postman update:</span></span>

![Konsola programu Poster pokazująca odpowiedź 204 (brak zawartości)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="3aa1f-370">Metoda DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="3aa1f-370">The DeleteTodoItem method</span></span>

<span data-ttu-id="3aa1f-371">Przeanalizuj metodę `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-371">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="3aa1f-372">Testowanie metody DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="3aa1f-372">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="3aa1f-373">Użyj programu Poster, aby usunąć element do wykonania:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-373">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="3aa1f-374">Ustaw metodę na `DELETE` .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-374">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="3aa1f-375">Ustaw identyfikator URI obiektu do usunięcia (na przykład `https://localhost:5001/api/TodoItems/1` ).</span><span class="sxs-lookup"><span data-stu-id="3aa1f-375">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="3aa1f-376">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-376">Select **Send**.</span></span>

<a name="over-post-v5"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="3aa1f-377">Zapobiegaj za pośrednictwem księgowania</span><span class="sxs-lookup"><span data-stu-id="3aa1f-377">Prevent over-posting</span></span>

<span data-ttu-id="3aa1f-378">Obecnie Przykładowa aplikacja uwidacznia cały `TodoItem` obiekt.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-378">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="3aa1f-379">Aplikacje produkcyjne zwykle ograniczają dane wejściowe i zwracane przy użyciu podzestawu modelu.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-379">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="3aa1f-380">Istnieje wiele powodów związanych z tym, a zabezpieczenia są głównymi.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-380">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="3aa1f-381">Podzestaw modelu jest zwykle określany jako obiekt Transfer danych (DTO), model wejściowy lub model widoku.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-381">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="3aa1f-382">**DTO** jest używany w tym artykule.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-382">**DTO** is used in this article.</span></span>

<span data-ttu-id="3aa1f-383">DTO może służyć do:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-383">A DTO may be used to:</span></span>

* <span data-ttu-id="3aa1f-384">Zablokuj nadmierne księgowanie.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-384">Prevent over-posting.</span></span>
* <span data-ttu-id="3aa1f-385">Ukryj właściwości, które nie powinny być wyświetlane dla klientów.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-385">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="3aa1f-386">Pomiń niektóre właściwości, aby zmniejszyć rozmiar ładunku.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-386">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="3aa1f-387">Spłaszcz wykresy obiektów zawierające obiekty zagnieżdżone.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-387">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="3aa1f-388">Spłaszczone wykresy obiektów mogą być wygodniejsze dla klientów.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-388">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="3aa1f-389">Aby zademonstrować podejście DTO, zaktualizuj `TodoItem` klasę w celu uwzględnienia pola tajnego:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-389">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=8)]

<span data-ttu-id="3aa1f-390">Pole tajne musi być ukryte w tej aplikacji, ale aplikacja administracyjna mogła ją uwidocznić.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-390">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="3aa1f-391">Sprawdź, czy można opublikować i pobrać pole tajne.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-391">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="3aa1f-392">Utwórz model DTO:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-392">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="3aa1f-393">Zaktualizuj, `TodoItemsController` Aby użyć `TodoItemDTO` :</span><span class="sxs-lookup"><span data-stu-id="3aa1f-393">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/5.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="3aa1f-394">Upewnij się, że nie można opublikować lub pobrać pola tajnego.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-394">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="3aa1f-395">Wywoływanie interfejsu API sieci Web przy użyciu języka JavaScript</span><span class="sxs-lookup"><span data-stu-id="3aa1f-395">Call the web API with JavaScript</span></span>

<span data-ttu-id="3aa1f-396">Zobacz [Samouczek: wywoływanie interfejsu API sieci web ASP.NET Core przy użyciu języka JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="3aa1f-396">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="3aa1f-397">Ten samouczek zawiera informacje na temat wykonywania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-397">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3aa1f-398">Utwórz projekt interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-398">Create a web API project.</span></span>
> * <span data-ttu-id="3aa1f-399">Dodaj klasę modelu i kontekst bazy danych.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-399">Add a model class and a database context.</span></span>
> * <span data-ttu-id="3aa1f-400">Tworzy szkielet kontrolera z metodami CRUD.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-400">Scaffold a controller with CRUD methods.</span></span>
> * <span data-ttu-id="3aa1f-401">Skonfiguruj Routing, ścieżki URL i wartości zwracane.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-401">Configure routing, URL paths, and return values.</span></span>
> * <span data-ttu-id="3aa1f-402">Wywołaj interfejs API sieci Web za pomocą programu Poster.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-402">Call the web API with Postman.</span></span>

<span data-ttu-id="3aa1f-403">Na końcu znajduje się internetowy interfejs API, który może zarządzać elementami do wykonania przechowywanymi w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-403">At the end, you have a web API that can manage "to-do" items stored in a database.</span></span>

## <a name="overview"></a><span data-ttu-id="3aa1f-404">Omówienie</span><span class="sxs-lookup"><span data-stu-id="3aa1f-404">Overview</span></span>

<span data-ttu-id="3aa1f-405">Ten samouczek tworzy następujący interfejs API:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-405">This tutorial creates the following API:</span></span>

|<span data-ttu-id="3aa1f-406">Interfejs API</span><span class="sxs-lookup"><span data-stu-id="3aa1f-406">API</span></span> | <span data-ttu-id="3aa1f-407">Opis</span><span class="sxs-lookup"><span data-stu-id="3aa1f-407">Description</span></span> | <span data-ttu-id="3aa1f-408">Treść żądania</span><span class="sxs-lookup"><span data-stu-id="3aa1f-408">Request body</span></span> | <span data-ttu-id="3aa1f-409">Treść odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="3aa1f-409">Response body</span></span> |
|--- | ---- | ---- | ---- |
|`GET /api/TodoItems` | <span data-ttu-id="3aa1f-410">Pobierz wszystkie elementy do wykonania</span><span class="sxs-lookup"><span data-stu-id="3aa1f-410">Get all to-do items</span></span> | <span data-ttu-id="3aa1f-411">Brak</span><span class="sxs-lookup"><span data-stu-id="3aa1f-411">None</span></span> | <span data-ttu-id="3aa1f-412">Tablica elementów do wykonania</span><span class="sxs-lookup"><span data-stu-id="3aa1f-412">Array of to-do items</span></span>|
|`GET /api/TodoItems/{id}` | <span data-ttu-id="3aa1f-413">Pobieranie elementu według identyfikatora</span><span class="sxs-lookup"><span data-stu-id="3aa1f-413">Get an item by ID</span></span> | <span data-ttu-id="3aa1f-414">Brak</span><span class="sxs-lookup"><span data-stu-id="3aa1f-414">None</span></span> | <span data-ttu-id="3aa1f-415">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="3aa1f-415">To-do item</span></span>|
|`POST /api/TodoItems` | <span data-ttu-id="3aa1f-416">Dodaj nowy element</span><span class="sxs-lookup"><span data-stu-id="3aa1f-416">Add a new item</span></span> | <span data-ttu-id="3aa1f-417">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="3aa1f-417">To-do item</span></span> | <span data-ttu-id="3aa1f-418">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="3aa1f-418">To-do item</span></span> |
|`PUT /api/TodoItems/{id}` | <span data-ttu-id="3aa1f-419">Aktualizowanie istniejącego elementu &nbsp;</span><span class="sxs-lookup"><span data-stu-id="3aa1f-419">Update an existing item &nbsp;</span></span> | <span data-ttu-id="3aa1f-420">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="3aa1f-420">To-do item</span></span> | <span data-ttu-id="3aa1f-421">Brak</span><span class="sxs-lookup"><span data-stu-id="3aa1f-421">None</span></span> |
|<span data-ttu-id="3aa1f-422">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span><span class="sxs-lookup"><span data-stu-id="3aa1f-422">`DELETE /api/TodoItems/{id}` &nbsp; &nbsp;</span></span> | <span data-ttu-id="3aa1f-423">Usuń element &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="3aa1f-423">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="3aa1f-424">Brak</span><span class="sxs-lookup"><span data-stu-id="3aa1f-424">None</span></span> | <span data-ttu-id="3aa1f-425">Brak</span><span class="sxs-lookup"><span data-stu-id="3aa1f-425">None</span></span>|

<span data-ttu-id="3aa1f-426">Na poniższym diagramie przedstawiono projekt aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-426">The following diagram shows the design of the app.</span></span>

![Klient jest reprezentowany przez pole po lewej stronie.](first-web-api/_static/architecture.png)

## <a name="prerequisites"></a><span data-ttu-id="3aa1f-432">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="3aa1f-432">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3aa1f-433">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3aa1f-433">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="3aa1f-434">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3aa1f-434">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3aa1f-435">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="3aa1f-435">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="3aa1f-436">Tworzenie projektu sieci Web</span><span class="sxs-lookup"><span data-stu-id="3aa1f-436">Create a web project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3aa1f-437">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3aa1f-437">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3aa1f-438">Z menu **plik** wybierz pozycję **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-438">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="3aa1f-439">Wybierz szablon **aplikacja sieci Web ASP.NET Core** a następnie kliknij przycisk **dalej**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-439">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="3aa1f-440">Nazwij projekt *TodoApi* i kliknij pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-440">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="3aa1f-441">W oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** upewnij się, że wybrano opcję **.net Core** i **ASP.NET Core 3,1** .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-441">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="3aa1f-442">Wybierz szablon **interfejsu API** i kliknij przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-442">Select the **API** template and click **Create**.</span></span>

![Okno dialogowe programu VS New Project](first-web-api/_static/vs3.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="3aa1f-444">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3aa1f-444">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3aa1f-445">Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="3aa1f-445">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="3aa1f-446">Zmień katalog ( `cd` ) do folderu, który będzie zawierać folder projektu.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-446">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="3aa1f-447">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-447">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   cd TodoApi
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   code -r ../TodoApi
   ```

* <span data-ttu-id="3aa1f-448">Gdy zostanie wyświetlone okno dialogowe z pytaniem, czy chcesz dodać wymagane zasoby do projektu, wybierz opcję **tak**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-448">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

  <span data-ttu-id="3aa1f-449">Poprzednie polecenia:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-449">The preceding commands:</span></span>

  * <span data-ttu-id="3aa1f-450">Tworzy nowy projekt internetowego interfejsu API i otwiera go w Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-450">Creates a new web API project and opens it in Visual Studio Code.</span></span>
  * <span data-ttu-id="3aa1f-451">Dodaje pakiety NuGet, które są wymagane w następnej sekcji.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-451">Adds the NuGet packages which are required in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3aa1f-452">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="3aa1f-452">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3aa1f-453">Wybierz pozycję **plik** > **nowe rozwiązanie**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-453">Select **File** > **New Solution**.</span></span>

  ![macOS nowe rozwiązanie](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="3aa1f-455">W Visual Studio dla komputerów Mac starszej niż wersja 8,6 Wybierz pozycję  >    >  **interfejs API** aplikacji .NET Core  >  .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-455">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="3aa1f-456">W wersji 8,6 lub nowszej wybierz pozycję   >    >  **interfejs API**  >  aplikacji sieci Web i konsoli.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-456">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>

  ![Wybór szablonu interfejsu API macOS](first-web-api-mac/_static/api_template.png)

* <span data-ttu-id="3aa1f-458">W oknie dialogowym **Konfigurowanie nowego interfejsu API sieci Web ASP.NET Core** wybierz najnowszą platformę **docelową**.NET Core 3. x.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-458">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 3.x **Target Framework**.</span></span> <span data-ttu-id="3aa1f-459">Wybierz opcję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-459">Select **Next**.</span></span>

* <span data-ttu-id="3aa1f-460">Wprowadź *TodoApi* jako **nazwę projektu** , a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-460">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![okno dialogowe konfiguracji](first-web-api-mac/_static/2.png)

[!INCLUDE[](~/includes/mac-terminal-access.md)]

<span data-ttu-id="3aa1f-462">Otwórz Terminal poleceń w folderze projektu i uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-462">Open a command terminal in the project folder and run the following command:</span></span>

   ```dotnetcli
   dotnet add package Microsoft.EntityFrameworkCore.InMemory
   ```

---

### <a name="test-the-api"></a><span data-ttu-id="3aa1f-463">Testowanie interfejsu API</span><span class="sxs-lookup"><span data-stu-id="3aa1f-463">Test the API</span></span>

<span data-ttu-id="3aa1f-464">Szablon projektu tworzy `WeatherForecast` interfejs API.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-464">The project template creates a `WeatherForecast` API.</span></span> <span data-ttu-id="3aa1f-465">Wywołaj `Get` metodę z przeglądarki, aby przetestować aplikację.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-465">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3aa1f-466">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3aa1f-466">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3aa1f-467">Naciśnij klawisze CTRL + F5, aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-467">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="3aa1f-468">Program Visual Studio uruchamia przeglądarkę i przechodzi do `https://localhost:<port>/WeatherForecast` lokalizacji, gdzie `<port>` jest losowo wybierany numer portu.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-468">Visual Studio launches a browser and navigates to `https://localhost:<port>/WeatherForecast`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="3aa1f-469">Jeśli zostanie wyświetlone okno dialogowe z pytaniem, czy należy zaufać certyfikatowi IIS Express, wybierz pozycję **tak**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-469">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="3aa1f-470">W wyświetlonym oknie dialogowym **ostrzeżenia o zabezpieczeniach** wybierz pozycję **tak**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-470">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3aa1f-471">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3aa1f-471">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3aa1f-472">Naciśnij klawisze CTRL + F5, aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-472">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="3aa1f-473">W przeglądarce przejdź do następującego adresu URL: `https://localhost:5001/WeatherForecast` .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-473">In a browser, go to following URL: `https://localhost:5001/WeatherForecast`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3aa1f-474">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="3aa1f-474">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="3aa1f-475">Wybierz pozycję **Uruchom**  >  **Rozpocznij debugowanie** , aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-475">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="3aa1f-476">Visual Studio dla komputerów Mac uruchamia przeglądarkę i nawiguje do `https://localhost:<port>` , gdzie `<port>` jest losowo wybranym numerem portu.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-476">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="3aa1f-477">Zwracany jest błąd HTTP 404 (nie znaleziono).</span><span class="sxs-lookup"><span data-stu-id="3aa1f-477">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="3aa1f-478">Dołącz `/WeatherForecast` do adresu URL (Zmień adres URL na `https://localhost:<port>/WeatherForecast` ).</span><span class="sxs-lookup"><span data-stu-id="3aa1f-478">Append `/WeatherForecast` to the URL (change the URL to `https://localhost:<port>/WeatherForecast`).</span></span>

---

<span data-ttu-id="3aa1f-479">Zwracany jest kod JSON podobny do następującego:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-479">JSON similar to the following is returned:</span></span>

```json
[
    {
        "date": "2019-07-16T19:04:05.7257911-06:00",
        "temperatureC": 52,
        "temperatureF": 125,
        "summary": "Mild"
    },
    {
        "date": "2019-07-17T19:04:05.7258461-06:00",
        "temperatureC": 36,
        "temperatureF": 96,
        "summary": "Warm"
    },
    {
        "date": "2019-07-18T19:04:05.7258467-06:00",
        "temperatureC": 39,
        "temperatureF": 102,
        "summary": "Cool"
    },
    {
        "date": "2019-07-19T19:04:05.7258471-06:00",
        "temperatureC": 10,
        "temperatureF": 49,
        "summary": "Bracing"
    },
    {
        "date": "2019-07-20T19:04:05.7258474-06:00",
        "temperatureC": -1,
        "temperatureF": 31,
        "summary": "Chilly"
    }
]
```

## <a name="add-a-model-class"></a><span data-ttu-id="3aa1f-480">Dodaj klasę modelu</span><span class="sxs-lookup"><span data-stu-id="3aa1f-480">Add a model class</span></span>

<span data-ttu-id="3aa1f-481">*Model* to zestaw klas, które reprezentują dane zarządzane przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-481">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="3aa1f-482">Model tej aplikacji jest pojedynczą `TodoItem` klasą.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-482">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3aa1f-483">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3aa1f-483">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3aa1f-484">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-484">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="3aa1f-485">Wybierz pozycję **Dodaj**  >  **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-485">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="3aa1f-486">Nazwij folder *Models* .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-486">Name the folder *Models*.</span></span>

* <span data-ttu-id="3aa1f-487">Kliknij prawym przyciskiem myszy *Models* folder i wybierz polecenie **Dodaj**  >  **klasę**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-487">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="3aa1f-488">Nadaj klasie nazwę *TodoItem* i wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-488">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="3aa1f-489">Zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-489">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3aa1f-490">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3aa1f-490">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3aa1f-491">Dodaj folder o nazwie *Models* .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-491">Add a folder named *Models*.</span></span>

* <span data-ttu-id="3aa1f-492">Dodaj `TodoItem` do *Models* folderu klasę o następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-492">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3aa1f-493">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="3aa1f-493">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3aa1f-494">Kliknij prawym przyciskiem myszy projekt.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-494">Right-click the project.</span></span> <span data-ttu-id="3aa1f-495">Wybierz pozycję **Dodaj**  >  **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-495">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="3aa1f-496">Nazwij folder *Models* .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-496">Name the folder *Models*.</span></span>

  ![Nowy folder](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="3aa1f-498">Kliknij prawym przyciskiem myszy *Models* folder, a następnie wybierz pozycję **Dodaj** > **nowy plik** > **ogólna** > **pusta Klasa**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-498">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="3aa1f-499">Nazwij klasę *TodoItem*, a następnie kliknij pozycję **New (nowy**).</span><span class="sxs-lookup"><span data-stu-id="3aa1f-499">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="3aa1f-500">Zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-500">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoItem.cs?name=snippet)]

<span data-ttu-id="3aa1f-501">`Id`Właściwość działa jako unikatowy klucz w relacyjnej bazie danych.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-501">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="3aa1f-502">Klasy modelu mogą przejść do dowolnego miejsca w projekcie, ale *Models* folder jest używany przez Konwencję.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-502">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context"></a><span data-ttu-id="3aa1f-503">Dodawanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="3aa1f-503">Add a database context</span></span>

<span data-ttu-id="3aa1f-504">*Kontekst bazy danych* jest główną klasą, która koordynuje Entity Framework funkcji dla modelu danych.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-504">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="3aa1f-505">Ta klasa jest tworzona przez wyprowadzanie z `Microsoft.EntityFrameworkCore.DbContext` klasy.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-505">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3aa1f-506">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3aa1f-506">Visual Studio</span></span>](#tab/visual-studio)

### <a name="add-nuget-packages"></a><span data-ttu-id="3aa1f-507">Dodawanie pakietów NuGet</span><span class="sxs-lookup"><span data-stu-id="3aa1f-507">Add NuGet packages</span></span>

* <span data-ttu-id="3aa1f-508">W menu **Narzędzia** wybierz pozycję **menedżer pakietów NuGet > zarządzanie pakietami NuGet dla rozwiązania**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-508">From the **Tools** menu, select **NuGet Package Manager > Manage NuGet Packages for Solution**.</span></span>
* <span data-ttu-id="3aa1f-509">Wybierz kartę **Przeglądaj** , a następnie w polu wyszukiwania wprowadź ciąg **Microsoft. EntityFrameworkCore. inMemory** .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-509">Select the **Browse** tab, and then enter **Microsoft.EntityFrameworkCore.InMemory** in the search box.</span></span>
* <span data-ttu-id="3aa1f-510">W lewym okienku wybierz pozycję **Microsoft. EntityFrameworkCore. inMemory** .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-510">Select **Microsoft.EntityFrameworkCore.InMemory** in the left pane.</span></span>
* <span data-ttu-id="3aa1f-511">Zaznacz pole wyboru **projekt** w prawym okienku, a następnie wybierz pozycję **Zainstaluj**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-511">Select the **Project** check box in the right pane and then select **Install**.</span></span>

![Menedżer pakietów NuGet](first-web-api/_static/vs3NuGet.png)

## <a name="add-the-todocontext-database-context"></a><span data-ttu-id="3aa1f-513">Dodawanie kontekstu bazy danych TodoContext</span><span class="sxs-lookup"><span data-stu-id="3aa1f-513">Add the TodoContext database context</span></span>

* <span data-ttu-id="3aa1f-514">Kliknij prawym przyciskiem myszy *Models* folder i wybierz polecenie **Dodaj**  >  **klasę**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-514">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="3aa1f-515">Nadaj klasie nazwę *TodoContext* i kliknij przycisk **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-515">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3aa1f-516">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="3aa1f-516">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="3aa1f-517">Dodaj `TodoContext` klasę do *Models* folderu.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-517">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="3aa1f-518">Wprowadź następujący kod:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-518">Enter the following code:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context"></a><span data-ttu-id="3aa1f-519">Rejestrowanie kontekstu bazy danych</span><span class="sxs-lookup"><span data-stu-id="3aa1f-519">Register the database context</span></span>

<span data-ttu-id="3aa1f-520">W ASP.NET Core usługi, takie jak kontekst bazy danych, muszą być zarejestrowane z kontenerem [iniekcji zależności (di)](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-520">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="3aa1f-521">Kontener udostępnia usługę kontrolerom.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-521">The container provides the service to controllers.</span></span>

<span data-ttu-id="3aa1f-522">Zaktualizuj *Startup.cs* o następujący wyróżniony kod:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-522">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Startup.cs?highlight=7-8,23-24&name=snippet_all)]

<span data-ttu-id="3aa1f-523">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-523">The preceding code:</span></span>

* <span data-ttu-id="3aa1f-524">Usuwa nieużywane `using` deklaracje.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-524">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="3aa1f-525">Dodaje kontekst bazy danych do kontenera DI.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-525">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="3aa1f-526">Określa, że kontekst bazy danych będzie używać bazy danych w pamięci.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-526">Specifies that the database context will use an in-memory database.</span></span>

## <a name="scaffold-a-controller"></a><span data-ttu-id="3aa1f-527">Tworzenie szkieletu kontrolera</span><span class="sxs-lookup"><span data-stu-id="3aa1f-527">Scaffold a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3aa1f-528">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3aa1f-528">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3aa1f-529">Kliknij prawym przyciskiem myszy folder *controllers* .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-529">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="3aa1f-530">Wybierz pozycję **Dodaj** > **nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-530">Select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="3aa1f-531">Wybierz pozycję **kontroler interfejsu API z akcjami, używając Entity Framework**, a następnie wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-531">Select **API Controller with actions, using Entity Framework**, and then select **Add**.</span></span>
* <span data-ttu-id="3aa1f-532">Na stronie **Dodawanie kontrolera interfejsu API z akcjami przy użyciu Entity Framework** dialogowego:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-532">In the **Add API Controller with actions, using Entity Framework** dialog:</span></span>

  * <span data-ttu-id="3aa1f-533">Wybierz pozycję **TodoItem (TodoApi. Models )** w **klasie model**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-533">Select **TodoItem (TodoApi.Models)** in the **Model class**.</span></span>
  * <span data-ttu-id="3aa1f-534">Wybierz pozycję **TodoContext (TodoApi. Models )** w **klasie kontekstu danych**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-534">Select **TodoContext (TodoApi.Models)** in the **Data context class**.</span></span>
  * <span data-ttu-id="3aa1f-535">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-535">Select **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3aa1f-536">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="3aa1f-536">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="3aa1f-537">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-537">Run the following commands:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet tool update -g Dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator controller -name TodoItemsController -async -api -m TodoItem -dc TodoContext -outDir Controllers
```

<span data-ttu-id="3aa1f-538">Poprzednie polecenia:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-538">The preceding commands:</span></span>

* <span data-ttu-id="3aa1f-539">Dodaj pakiety NuGet wymagane do tworzenia szkieletów.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-539">Add NuGet packages required for scaffolding.</span></span>
* <span data-ttu-id="3aa1f-540">Instaluje aparat tworzenia szkieletu ( `dotnet-aspnet-codegenerator` ).</span><span class="sxs-lookup"><span data-stu-id="3aa1f-540">Installs the scaffolding engine (`dotnet-aspnet-codegenerator`).</span></span>
* <span data-ttu-id="3aa1f-541">Szkielety `TodoItemsController` .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-541">Scaffolds the `TodoItemsController`.</span></span>

---

<span data-ttu-id="3aa1f-542">Wygenerowany kod:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-542">The generated code:</span></span>

* <span data-ttu-id="3aa1f-543">Oznacza klasę [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atrybutem.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-543">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="3aa1f-544">Ten atrybut wskazuje, że kontroler odpowiada na żądania interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-544">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="3aa1f-545">Aby uzyskać informacje o określonych zachowaniach, które włącza atrybut, zobacz <xref:web-api/index> .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-545">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="3aa1f-546">Używa funkcji DI do iniekcji kontekstu bazy danych ( `TodoContext` ) do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-546">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="3aa1f-547">Kontekst bazy danych jest używany w każdej z metod [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) w kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-547">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<span data-ttu-id="3aa1f-548">Szablony ASP.NET Core dla:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-548">The ASP.NET Core templates for:</span></span>

* <span data-ttu-id="3aa1f-549">Kontrolery z widokami obejmują `[action]` szablon trasy.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-549">Controllers with views include `[action]` in the route template.</span></span>
* <span data-ttu-id="3aa1f-550">Kontrolery interfejsu API nie należą `[action]` do szablonu trasy.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-550">API controllers don't include `[action]` in the route template.</span></span>

<span data-ttu-id="3aa1f-551">Gdy `[action]` token nie znajduje się w szablonie trasy, nazwa [akcji](xref:mvc/controllers/routing#action) jest wykluczona z trasy.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-551">When the `[action]` token isn't in the route template, the [action](xref:mvc/controllers/routing#action) name is excluded from the route.</span></span> <span data-ttu-id="3aa1f-552">Oznacza to, że nazwa metody skojarzonej z akcją nie jest używana w zgodnej trasie.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-552">That is, the action's associated method name isn't used in the matching route.</span></span>

## <a name="examine-the-posttodoitem-create-method"></a><span data-ttu-id="3aa1f-553">Badanie metody PostTodoItem Create</span><span class="sxs-lookup"><span data-stu-id="3aa1f-553">Examine the PostTodoItem create method</span></span>

<span data-ttu-id="3aa1f-554">Zastąp instrukcję return w, `PostTodoItem` Aby użyć operatora [nameof](/dotnet/csharp/language-reference/operators/nameof) :</span><span class="sxs-lookup"><span data-stu-id="3aa1f-554">Replace the return statement in the `PostTodoItem` to use the [nameof](/dotnet/csharp/language-reference/operators/nameof) operator:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Create)]

<span data-ttu-id="3aa1f-555">Poprzedni kod jest metodą POST protokołu HTTP, jak wskazano w [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) atrybucie.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-555">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="3aa1f-556">Metoda pobiera wartość elementu do wykonania z treści żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-556">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="3aa1f-557">Aby uzyskać więcej informacji, zobacz temat [Routing atrybutów z atrybutami http [Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="3aa1f-557">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="3aa1f-558"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>Metoda:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-558">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method:</span></span>

* <span data-ttu-id="3aa1f-559">W razie powodzenia zwraca kod stanu HTTP 201.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-559">Returns an HTTP 201 status code if successful.</span></span> <span data-ttu-id="3aa1f-560">HTTP 201 to standardowa odpowiedź dla metody POST protokołu HTTP, która tworzy nowy zasób na serwerze.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-560">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="3aa1f-561">Dodaje nagłówek [lokalizacji](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) do odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-561">Adds a [Location](https://developer.mozilla.org/docs/Web/HTTP/Headers/Location) header to the response.</span></span> <span data-ttu-id="3aa1f-562">`Location`Nagłówek określa [Identyfikator URI](https://developer.mozilla.org/docs/Glossary/URI) nowo utworzonego elementu do wykonania.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-562">The `Location` header specifies the [URI](https://developer.mozilla.org/docs/Glossary/URI) of the newly created to-do item.</span></span> <span data-ttu-id="3aa1f-563">Aby uzyskać więcej informacji, zobacz [10.2.2 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="3aa1f-563">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="3aa1f-564">Odwołuje się do `GetTodoItem` akcji tworzenia `Location` identyfikatora URI nagłówka.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-564">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="3aa1f-565">`nameof`Słowo kluczowe języka C# służy do zapobiegania twardemu kodowaniu nazwy akcji w `CreatedAtAction` wywołaniu.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-565">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

### <a name="install-postman"></a><span data-ttu-id="3aa1f-566">Zainstaluj program Poster</span><span class="sxs-lookup"><span data-stu-id="3aa1f-566">Install Postman</span></span>

<span data-ttu-id="3aa1f-567">Ten samouczek używa programu do testowania interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-567">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="3aa1f-568">Zainstaluj program [Poster](https://www.getpostman.com/downloads/)</span><span class="sxs-lookup"><span data-stu-id="3aa1f-568">Install [Postman](https://www.getpostman.com/downloads/)</span></span>
* <span data-ttu-id="3aa1f-569">Uruchom aplikację internetową.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-569">Start the web app.</span></span>
* <span data-ttu-id="3aa1f-570">Uruchom wpis.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-570">Start Postman.</span></span>
* <span data-ttu-id="3aa1f-571">Wyłącz **weryfikację certyfikatu SSL**</span><span class="sxs-lookup"><span data-stu-id="3aa1f-571">Disable **SSL certificate verification**</span></span>
  * <span data-ttu-id="3aa1f-572">W  obszarze > **Ustawienia** pliku (karta **Ogólne** ) Wyłącz **weryfikację certyfikatu SSL**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-572">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>
    > [!WARNING]
    > <span data-ttu-id="3aa1f-573">Po przetestowaniu kontrolera ponownie Włącz weryfikację certyfikatu SSL.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-573">Re-enable SSL certificate verification after testing the controller.</span></span>

<a name="post"></a>

### <a name="test-posttodoitem-with-postman"></a><span data-ttu-id="3aa1f-574">Test PostTodoItem za pomocą programu Poster</span><span class="sxs-lookup"><span data-stu-id="3aa1f-574">Test PostTodoItem with Postman</span></span>

* <span data-ttu-id="3aa1f-575">Utwórz nowe żądanie.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-575">Create a new request.</span></span>
* <span data-ttu-id="3aa1f-576">Ustaw metodę HTTP na `POST` .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-576">Set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="3aa1f-577">Ustaw identyfikator URI na `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-577">Set the URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="3aa1f-578">Na przykład `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-578">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="3aa1f-579">Wybierz kartę **Treść**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-579">Select the **Body** tab.</span></span>
* <span data-ttu-id="3aa1f-580">Wybierz przycisk radiowy **RAW** .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-580">Select the **raw** radio button.</span></span>
* <span data-ttu-id="3aa1f-581">Ustaw typ na **JSON (Application/JSON)**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-581">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="3aa1f-582">W treści żądania wprowadź kod JSON dla elementu do wykonania:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-582">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="3aa1f-583">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-583">Select **Send**.</span></span>

  ![Ogłoś przy użyciu żądania Create](first-web-api/_static/3/create.png)

### <a name="test-the-location-header-uri-with-postman"></a><span data-ttu-id="3aa1f-585">Testowanie identyfikatora URI nagłówka lokalizacji za pomocą programu Poster</span><span class="sxs-lookup"><span data-stu-id="3aa1f-585">Test the location header URI with Postman</span></span>

* <span data-ttu-id="3aa1f-586">Wybierz kartę **nagłówki** w okienku **odpowiedź** .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-586">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="3aa1f-587">Skopiuj wartość nagłówka **lokalizacji** :</span><span class="sxs-lookup"><span data-stu-id="3aa1f-587">Copy the **Location** header value:</span></span>

  ![Karta nagłówki w konsoli programu Poster](first-web-api/_static/3/create.png)

* <span data-ttu-id="3aa1f-589">Ustaw metodę HTTP na `GET` .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-589">Set the HTTP method to `GET`.</span></span>
* <span data-ttu-id="3aa1f-590">Ustaw identyfikator URI na `https://localhost:<port>/api/TodoItems/1` .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-590">Set the URI to `https://localhost:<port>/api/TodoItems/1`.</span></span> <span data-ttu-id="3aa1f-591">Na przykład `https://localhost:5001/api/TodoItems/1`.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-591">For example, `https://localhost:5001/api/TodoItems/1`.</span></span>
* <span data-ttu-id="3aa1f-592">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-592">Select **Send**.</span></span>

## <a name="examine-the-get-methods"></a><span data-ttu-id="3aa1f-593">Badanie metod GET</span><span class="sxs-lookup"><span data-stu-id="3aa1f-593">Examine the GET methods</span></span>

<span data-ttu-id="3aa1f-594">Te metody implementują dwa punkty końcowe GET:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-594">These methods implement two GET endpoints:</span></span>

* `GET /api/TodoItems`
* `GET /api/TodoItems/{id}`

<span data-ttu-id="3aa1f-595">Przetestuj aplikację, wywołując dwa punkty końcowe z przeglądarki lub wpisu.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-595">Test the app by calling the two endpoints from a browser or Postman.</span></span> <span data-ttu-id="3aa1f-596">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-596">For example:</span></span>

* `https://localhost:5001/api/TodoItems`
* `https://localhost:5001/api/TodoItems/1`

<span data-ttu-id="3aa1f-597">Odpowiedź podobna do poniższego jest generowana przez wywołanie `GetTodoItems` :</span><span class="sxs-lookup"><span data-stu-id="3aa1f-597">A response similar to the following is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

### <a name="test-get-with-postman"></a><span data-ttu-id="3aa1f-598">Test get przy użyciu programu Poster</span><span class="sxs-lookup"><span data-stu-id="3aa1f-598">Test Get with Postman</span></span>

* <span data-ttu-id="3aa1f-599">Utwórz nowe żądanie.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-599">Create a new request.</span></span>
* <span data-ttu-id="3aa1f-600">Ustaw metodę HTTP, aby **uzyskać**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-600">Set the HTTP method to **GET**.</span></span>
* <span data-ttu-id="3aa1f-601">Ustaw identyfikator URI żądania na `https://localhost:<port>/api/TodoItems` .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-601">Set the request URI to `https://localhost:<port>/api/TodoItems`.</span></span> <span data-ttu-id="3aa1f-602">Na przykład `https://localhost:5001/api/TodoItems`.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-602">For example, `https://localhost:5001/api/TodoItems`.</span></span>
* <span data-ttu-id="3aa1f-603">Ustaw **dwa widoki okienka** w programie Poster.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-603">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="3aa1f-604">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-604">Select **Send**.</span></span>

<span data-ttu-id="3aa1f-605">Ta aplikacja używa bazy danych w pamięci.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-605">This app uses an in-memory database.</span></span> <span data-ttu-id="3aa1f-606">Jeśli aplikacja zostanie zatrzymana i uruchomiona, poprzednie żądanie GET nie zwróci żadnych danych.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-606">If the app is stopped and started, the preceding GET request will not return any data.</span></span> <span data-ttu-id="3aa1f-607">Jeśli nie zostaną zwrócone żadne dane, [Opublikuj](#post) dane w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-607">If no data is returned, [POST](#post) data to the app.</span></span>

## <a name="routing-and-url-paths"></a><span data-ttu-id="3aa1f-608">Ścieżki routingu i adresów URL</span><span class="sxs-lookup"><span data-stu-id="3aa1f-608">Routing and URL paths</span></span>

<span data-ttu-id="3aa1f-609">Ten [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) atrybut oznacza metodę, która reaguje na żądanie HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-609">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="3aa1f-610">Ścieżka adresu URL dla każdej metody jest zbudowana w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-610">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="3aa1f-611">Zacznij od ciągu szablonu w `Route` atrybucie kontrolera:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-611">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=TodoController&highlight=1)]

* <span data-ttu-id="3aa1f-612">Zastąp `[controller]` nazwą kontrolera, którą Konwencją jest nazwa klasy kontrolera minus sufiks "Controller".</span><span class="sxs-lookup"><span data-stu-id="3aa1f-612">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="3aa1f-613">Dla tego przykładu nazwa klasy kontrolera to **TodoItems** Controller, więc nazwa kontrolera to "TodoItems".</span><span class="sxs-lookup"><span data-stu-id="3aa1f-613">For this sample, the controller class name is **TodoItems** Controller, so the controller name is "TodoItems".</span></span> <span data-ttu-id="3aa1f-614">W ASP.NET Core [routingu](xref:mvc/controllers/routing) jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-614">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="3aa1f-615">Jeśli `[HttpGet]` atrybut ma szablon trasy (na przykład `[HttpGet("products")]` ), Dodaj go do ścieżki.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-615">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="3aa1f-616">Ten przykład nie używa szablonu.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-616">This sample doesn't use a template.</span></span> <span data-ttu-id="3aa1f-617">Aby uzyskać więcej informacji, zobacz temat [Routing atrybutów z atrybutami http [Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="3aa1f-617">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="3aa1f-618">W poniższej `GetTodoItem` metodzie `"{id}"` jest zmienną zastępczą dla unikatowego identyfikatora elementu do wykonania.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-618">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="3aa1f-619">Gdy `GetTodoItem` jest wywoływana, wartość `"{id}"` w adresie URL jest podawana do metody w jej `id` parametrze.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-619">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its `id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values"></a><span data-ttu-id="3aa1f-620">Wartości zwracane</span><span class="sxs-lookup"><span data-stu-id="3aa1f-620">Return values</span></span> 

<span data-ttu-id="3aa1f-621">Zwracany typ `GetTodoItems` `GetTodoItem` metod i jest [ \<T> typem ActionResult](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="3aa1f-621">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="3aa1f-622">ASP.NET Core automatycznie serializować obiektu do [formatu JSON](https://www.json.org/) i zapisuje kod JSON w treści komunikatu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-622">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="3aa1f-623">Kod odpowiedzi dla tego typu zwracanego to 200, przy założeniu, że nie istnieją Nieobsłużone wyjątki.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-623">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="3aa1f-624">Nieobsłużone wyjątki są tłumaczone na błędy 5xx.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-624">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="3aa1f-625">`ActionResult` typy zwracane mogą reprezentować szeroką gamę kodów stanu HTTP.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-625">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="3aa1f-626">Na przykład `GetTodoItem` może zwracać dwie różne wartości stanu:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-626">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="3aa1f-627">Jeśli żaden element nie jest zgodny z żądanym IDENTYFIKATORem, metoda zwróci <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> Kod błędu 404.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-627">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="3aa1f-628">W przeciwnym razie metoda zwraca 200 z treścią odpowiedzi JSON.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-628">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="3aa1f-629">Zwracanie `item` wyników w odpowiedzi HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-629">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="the-puttodoitem-method"></a><span data-ttu-id="3aa1f-630">Metoda PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="3aa1f-630">The PutTodoItem method</span></span>

<span data-ttu-id="3aa1f-631">Przeanalizuj metodę `PutTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-631">Examine the `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Update)]

<span data-ttu-id="3aa1f-632">`PutTodoItem` jest podobny do `PostTodoItem` , z tą różnicą, że używa protokołu HTTP Put.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-632">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="3aa1f-633">Odpowiedź to [204 (brak zawartości)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="3aa1f-633">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="3aa1f-634">Zgodnie ze specyfikacją protokołu HTTP żądanie PUT wymaga, aby klient wysłał całą zaktualizowaną jednostkę, a nie tylko te zmiany.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-634">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="3aa1f-635">Aby zapewnić obsługę częściowych aktualizacji, użyj [poprawki http](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="3aa1f-635">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="3aa1f-636">Jeśli wystąpi błąd podczas wywoływania `PutTodoItem` , wywołaj, `GET` Aby upewnić się, że w bazie danych znajduje się element.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-636">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method"></a><span data-ttu-id="3aa1f-637">Testowanie metody PutTodoItem</span><span class="sxs-lookup"><span data-stu-id="3aa1f-637">Test the PutTodoItem method</span></span>

<span data-ttu-id="3aa1f-638">Ten przykład korzysta z bazy danych w pamięci, która musi zostać zainicjowana za każdym razem, gdy aplikacja zostanie uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-638">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="3aa1f-639">Przed wykonaniem wywołania PUT musi istnieć element w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-639">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="3aa1f-640">Przed wykonaniem wywołania PUT należy wywołać metodę GET, aby upewnić się, że istnieje element w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-640">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="3aa1f-641">Zaktualizuj element do wykonania o identyfikatorze 1 i ustaw jego nazwę na "Źródło danych":</span><span class="sxs-lookup"><span data-stu-id="3aa1f-641">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="3aa1f-642">Na poniższej ilustracji przedstawiono aktualizację programu Poster:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-642">The following image shows the Postman update:</span></span>

![Konsola programu Poster pokazująca odpowiedź 204 (brak zawartości)](first-web-api/_static/3/pmcput.png)

## <a name="the-deletetodoitem-method"></a><span data-ttu-id="3aa1f-644">Metoda DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="3aa1f-644">The DeleteTodoItem method</span></span>

<span data-ttu-id="3aa1f-645">Przeanalizuj metodę `DeleteTodoItem`:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-645">Examine the `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApi/Controllers/TodoItemsController.cs?name=snippet_Delete)]

### <a name="test-the-deletetodoitem-method"></a><span data-ttu-id="3aa1f-646">Testowanie metody DeleteTodoItem</span><span class="sxs-lookup"><span data-stu-id="3aa1f-646">Test the DeleteTodoItem method</span></span>

<span data-ttu-id="3aa1f-647">Użyj programu Poster, aby usunąć element do wykonania:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-647">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="3aa1f-648">Ustaw metodę na `DELETE` .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-648">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="3aa1f-649">Ustaw identyfikator URI obiektu do usunięcia (na przykład `https://localhost:5001/api/TodoItems/1` ).</span><span class="sxs-lookup"><span data-stu-id="3aa1f-649">Set the URI of the object to delete (for example `https://localhost:5001/api/TodoItems/1`).</span></span>
* <span data-ttu-id="3aa1f-650">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-650">Select **Send**.</span></span>

<a name="over-post"></a>
<a name="over-post-v3"></a>

## <a name="prevent-over-posting"></a><span data-ttu-id="3aa1f-651">Zapobiegaj za pośrednictwem księgowania</span><span class="sxs-lookup"><span data-stu-id="3aa1f-651">Prevent over-posting</span></span>

<span data-ttu-id="3aa1f-652">Obecnie Przykładowa aplikacja uwidacznia cały `TodoItem` obiekt.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-652">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="3aa1f-653">Aplikacje produkcyjne zwykle ograniczają dane wejściowe i zwracane przy użyciu podzestawu modelu.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-653">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="3aa1f-654">Istnieje wiele powodów związanych z tym, a zabezpieczenia są głównymi.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-654">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="3aa1f-655">Podzestaw modelu jest zwykle określany jako obiekt Transfer danych (DTO), model wejściowy lub model widoku.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-655">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="3aa1f-656">**DTO** jest używany w tym artykule.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-656">**DTO** is used in this article.</span></span>

<span data-ttu-id="3aa1f-657">DTO może służyć do:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-657">A DTO may be used to:</span></span>

* <span data-ttu-id="3aa1f-658">Zablokuj nadmierne księgowanie.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-658">Prevent over-posting.</span></span>
* <span data-ttu-id="3aa1f-659">Ukryj właściwości, które nie powinny być wyświetlane dla klientów.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-659">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="3aa1f-660">Pomiń niektóre właściwości, aby zmniejszyć rozmiar ładunku.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-660">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="3aa1f-661">Spłaszcz wykresy obiektów zawierające obiekty zagnieżdżone.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-661">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="3aa1f-662">Spłaszczone wykresy obiektów mogą być wygodniejsze dla klientów.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-662">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="3aa1f-663">Aby zademonstrować podejście DTO, zaktualizuj `TodoItem` klasę w celu uwzględnienia pola tajnego:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-663">To demonstrate the DTO approach, update the `TodoItem` class to include a secret field:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItem.cs?name=snippet&highlight=6)]

<span data-ttu-id="3aa1f-664">Pole tajne musi być ukryte w tej aplikacji, ale aplikacja administracyjna mogła ją uwidocznić.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-664">The secret field needs to be hidden from this app, but an administrative app could choose to expose it.</span></span>

<span data-ttu-id="3aa1f-665">Sprawdź, czy można opublikować i pobrać pole tajne.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-665">Verify you can post and get the secret field.</span></span>

<span data-ttu-id="3aa1f-666">Utwórz model DTO:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-666">Create a DTO model:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Models/TodoItemDTO.cs?name=snippet)]

<span data-ttu-id="3aa1f-667">Zaktualizuj, `TodoItemsController` Aby użyć `TodoItemDTO` :</span><span class="sxs-lookup"><span data-stu-id="3aa1f-667">Update the `TodoItemsController` to use `TodoItemDTO`:</span></span>

[!code-csharp[](first-web-api/samples/3.0/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet)]

<span data-ttu-id="3aa1f-668">Upewnij się, że nie można opublikować lub pobrać pola tajnego.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-668">Verify you can't post or get the secret field.</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="3aa1f-669">Wywoływanie interfejsu API sieci Web przy użyciu języka JavaScript</span><span class="sxs-lookup"><span data-stu-id="3aa1f-669">Call the web API with JavaScript</span></span>

<span data-ttu-id="3aa1f-670">Zobacz [Samouczek: wywoływanie interfejsu API sieci web ASP.NET Core przy użyciu języka JavaScript](xref:tutorials/web-api-javascript).</span><span class="sxs-lookup"><span data-stu-id="3aa1f-670">See [Tutorial: Call an ASP.NET Core web API with JavaScript](xref:tutorials/web-api-javascript).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3aa1f-671">Ten samouczek zawiera informacje na temat wykonywania następujących czynności:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-671">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3aa1f-672">Utwórz projekt interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-672">Create a web API project.</span></span>
> * <span data-ttu-id="3aa1f-673">Dodaj klasę modelu i kontekst bazy danych.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-673">Add a model class and a database context.</span></span>
> * <span data-ttu-id="3aa1f-674">Dodaj kontroler.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-674">Add a controller.</span></span>
> * <span data-ttu-id="3aa1f-675">Dodaj metody CRUD.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-675">Add CRUD methods.</span></span>
> * <span data-ttu-id="3aa1f-676">Skonfiguruj ścieżki routingu i adresów URL.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-676">Configure routing and URL paths.</span></span>
> * <span data-ttu-id="3aa1f-677">Określ wartości zwracane.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-677">Specify return values.</span></span>
> * <span data-ttu-id="3aa1f-678">Wywołaj interfejs API sieci Web za pomocą programu Poster.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-678">Call the web API with Postman.</span></span>
> * <span data-ttu-id="3aa1f-679">Wywołaj interfejs API sieci Web za pomocą języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-679">Call the web API with JavaScript.</span></span>

<span data-ttu-id="3aa1f-680">Na końcu znajduje się internetowy interfejs API, który może zarządzać elementami do wykonania przechowywanymi w relacyjnej bazie danych.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-680">At the end, you have a web API that can manage "to-do" items stored in a relational database.</span></span>

## <a name="overview-21"></a><span data-ttu-id="3aa1f-681">Przegląd 2,1</span><span class="sxs-lookup"><span data-stu-id="3aa1f-681">Overview 2.1</span></span>

<span data-ttu-id="3aa1f-682">Ten samouczek tworzy następujący interfejs API:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-682">This tutorial creates the following API:</span></span>

|<span data-ttu-id="3aa1f-683">Interfejs API</span><span class="sxs-lookup"><span data-stu-id="3aa1f-683">API</span></span> | <span data-ttu-id="3aa1f-684">Opis</span><span class="sxs-lookup"><span data-stu-id="3aa1f-684">Description</span></span> | <span data-ttu-id="3aa1f-685">Treść żądania</span><span class="sxs-lookup"><span data-stu-id="3aa1f-685">Request body</span></span> | <span data-ttu-id="3aa1f-686">Treść odpowiedzi</span><span class="sxs-lookup"><span data-stu-id="3aa1f-686">Response body</span></span> |
|--- | ---- | ---- | ---- |
|<span data-ttu-id="3aa1f-687">Pobierz/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="3aa1f-687">GET /api/TodoItems</span></span> | <span data-ttu-id="3aa1f-688">Pobierz wszystkie elementy do wykonania</span><span class="sxs-lookup"><span data-stu-id="3aa1f-688">Get all to-do items</span></span> | <span data-ttu-id="3aa1f-689">Brak</span><span class="sxs-lookup"><span data-stu-id="3aa1f-689">None</span></span> | <span data-ttu-id="3aa1f-690">Tablica elementów do wykonania</span><span class="sxs-lookup"><span data-stu-id="3aa1f-690">Array of to-do items</span></span>|
|<span data-ttu-id="3aa1f-691">Pobierz/api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="3aa1f-691">GET /api/TodoItems/{id}</span></span> | <span data-ttu-id="3aa1f-692">Pobieranie elementu według identyfikatora</span><span class="sxs-lookup"><span data-stu-id="3aa1f-692">Get an item by ID</span></span> | <span data-ttu-id="3aa1f-693">Brak</span><span class="sxs-lookup"><span data-stu-id="3aa1f-693">None</span></span> | <span data-ttu-id="3aa1f-694">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="3aa1f-694">To-do item</span></span>|
|<span data-ttu-id="3aa1f-695">Opublikuj/api/TodoItems</span><span class="sxs-lookup"><span data-stu-id="3aa1f-695">POST /api/TodoItems</span></span> | <span data-ttu-id="3aa1f-696">Dodaj nowy element</span><span class="sxs-lookup"><span data-stu-id="3aa1f-696">Add a new item</span></span> | <span data-ttu-id="3aa1f-697">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="3aa1f-697">To-do item</span></span> | <span data-ttu-id="3aa1f-698">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="3aa1f-698">To-do item</span></span> |
|<span data-ttu-id="3aa1f-699">Umieść/api/TodoItems/{id}</span><span class="sxs-lookup"><span data-stu-id="3aa1f-699">PUT /api/TodoItems/{id}</span></span> | <span data-ttu-id="3aa1f-700">Aktualizowanie istniejącego elementu &nbsp;</span><span class="sxs-lookup"><span data-stu-id="3aa1f-700">Update an existing item &nbsp;</span></span> | <span data-ttu-id="3aa1f-701">Element do wykonania</span><span class="sxs-lookup"><span data-stu-id="3aa1f-701">To-do item</span></span> | <span data-ttu-id="3aa1f-702">Brak</span><span class="sxs-lookup"><span data-stu-id="3aa1f-702">None</span></span> |
|<span data-ttu-id="3aa1f-703">Usuń/api/TodoItems/{id} &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="3aa1f-703">DELETE /api/TodoItems/{id} &nbsp; &nbsp;</span></span> | <span data-ttu-id="3aa1f-704">Usuń element &nbsp;&nbsp;</span><span class="sxs-lookup"><span data-stu-id="3aa1f-704">Delete an item &nbsp; &nbsp;</span></span> | <span data-ttu-id="3aa1f-705">Brak</span><span class="sxs-lookup"><span data-stu-id="3aa1f-705">None</span></span> | <span data-ttu-id="3aa1f-706">Brak</span><span class="sxs-lookup"><span data-stu-id="3aa1f-706">None</span></span>|

<span data-ttu-id="3aa1f-707">Na poniższym diagramie przedstawiono projekt aplikacji.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-707">The following diagram shows the design of the app.</span></span>

![Klient jest reprezentowany przez pole po lewej stronie.](first-web-api/_static/architecture.png)

## <a name="prerequisites-21"></a><span data-ttu-id="3aa1f-713">Wymagania wstępne 2,1</span><span class="sxs-lookup"><span data-stu-id="3aa1f-713">Prerequisites 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3aa1f-714">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3aa1f-714">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="3aa1f-715">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3aa1f-715">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3aa1f-716">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="3aa1f-716">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project-21"></a><span data-ttu-id="3aa1f-717">Tworzenie projektu sieci Web 2,1</span><span class="sxs-lookup"><span data-stu-id="3aa1f-717">Create a web project 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3aa1f-718">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3aa1f-718">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3aa1f-719">Z menu **plik** wybierz pozycję **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-719">From the **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="3aa1f-720">Wybierz szablon **aplikacja sieci Web ASP.NET Core** a następnie kliknij przycisk **dalej**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-720">Select the **ASP.NET Core Web Application** template and click **Next**.</span></span>
* <span data-ttu-id="3aa1f-721">Nazwij projekt *TodoApi* i kliknij pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-721">Name the project *TodoApi* and click **Create**.</span></span>
* <span data-ttu-id="3aa1f-722">W oknie dialogowym **Tworzenie nowej ASP.NET Core aplikacji sieci Web** upewnij się, że wybrano opcję **.net Core** i **ASP.NET Core 2,2** .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-722">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 2.2** are selected.</span></span> <span data-ttu-id="3aa1f-723">Wybierz szablon **interfejsu API** i kliknij przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-723">Select the **API** template and click **Create**.</span></span> <span data-ttu-id="3aa1f-724">**Nie** zaznaczaj opcji **Włącz obsługę platformy Docker**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-724">**Don't** select **Enable Docker Support**.</span></span>

![Okno dialogowe programu VS New Project](first-web-api/_static/vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="3aa1f-726">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3aa1f-726">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3aa1f-727">Otwórz [zintegrowany terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="3aa1f-727">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="3aa1f-728">Zmień katalog ( `cd` ) do folderu, który będzie zawierać folder projektu.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-728">Change directories (`cd`) to the folder that will contain the project folder.</span></span>
* <span data-ttu-id="3aa1f-729">Uruchom następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-729">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapi -o TodoApi
   code -r TodoApi
   ```

  <span data-ttu-id="3aa1f-730">Te polecenia tworzą nowy projekt internetowego interfejsu API i otwierają nowe wystąpienie Visual Studio Code w nowym folderze projektu.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-730">These commands create a new web API project and open a new instance of Visual Studio Code in the new project folder.</span></span>

* <span data-ttu-id="3aa1f-731">Gdy zostanie wyświetlone okno dialogowe z pytaniem, czy chcesz dodać wymagane zasoby do projektu, wybierz opcję **tak**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-731">When a dialog box asks if you want to add required assets to the project, select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3aa1f-732">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="3aa1f-732">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3aa1f-733">Wybierz pozycję **plik** > **nowe rozwiązanie**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-733">Select **File** > **New Solution**.</span></span>

  ![macOS nowe rozwiązanie](first-web-api-mac/_static/sln.png)

* <span data-ttu-id="3aa1f-735">W Visual Studio dla komputerów Mac starszej niż wersja 8,6 Wybierz pozycję  >    >  **interfejs API** aplikacji .NET Core  >  .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-735">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **API** > **Next**.</span></span> <span data-ttu-id="3aa1f-736">W wersji 8,6 lub nowszej wybierz pozycję   >    >  **interfejs API**  >  aplikacji sieci Web i konsoli.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-736">In version 8.6 or later, select **Web and Console** > **App** > **API** > **Next**.</span></span>
  
* <span data-ttu-id="3aa1f-737">W oknie dialogowym **Konfigurowanie nowego interfejsu API sieci Web ASP.NET Core** wybierz najnowszą platformę **docelową** programu .NET Core 2. x.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-737">In the **Configure the new ASP.NET Core Web API** dialog, select the latest .NET Core 2.x **Target Framework**.</span></span> <span data-ttu-id="3aa1f-738">Wybierz opcję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-738">Select **Next**.</span></span>

* <span data-ttu-id="3aa1f-739">Wprowadź *TodoApi* jako **nazwę projektu** , a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-739">Enter *TodoApi* for the **Project Name** and then select **Create**.</span></span>

  ![okno dialogowe konfiguracji](first-web-api-mac/_static/2.png)

---

### <a name="test-the-api-21"></a><span data-ttu-id="3aa1f-741">Testowanie interfejsu API 2,1</span><span class="sxs-lookup"><span data-stu-id="3aa1f-741">Test the API 2.1</span></span>

<span data-ttu-id="3aa1f-742">Szablon projektu tworzy `values` interfejs API.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-742">The project template creates a `values` API.</span></span> <span data-ttu-id="3aa1f-743">Wywołaj `Get` metodę z przeglądarki, aby przetestować aplikację.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-743">Call the `Get` method from a browser to test the app.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3aa1f-744">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3aa1f-744">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3aa1f-745">Naciśnij klawisze CTRL + F5, aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-745">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="3aa1f-746">Program Visual Studio uruchamia przeglądarkę i przechodzi do `https://localhost:<port>/api/values` lokalizacji, gdzie `<port>` jest losowo wybierany numer portu.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-746">Visual Studio launches a browser and navigates to `https://localhost:<port>/api/values`, where `<port>` is a randomly chosen port number.</span></span>

<span data-ttu-id="3aa1f-747">Jeśli zostanie wyświetlone okno dialogowe z pytaniem, czy należy zaufać certyfikatowi IIS Express, wybierz pozycję **tak**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-747">If you get a dialog box that asks if you should trust the IIS Express certificate, select **Yes**.</span></span> <span data-ttu-id="3aa1f-748">W wyświetlonym oknie dialogowym **ostrzeżenia o zabezpieczeniach** wybierz pozycję **tak**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-748">In the **Security Warning** dialog that appears next, select **Yes**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3aa1f-749">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3aa1f-749">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="3aa1f-750">Naciśnij klawisze CTRL + F5, aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-750">Press Ctrl+F5 to run the app.</span></span> <span data-ttu-id="3aa1f-751">W przeglądarce przejdź do następującego adresu URL: `https://localhost:5001/api/values` .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-751">In a browser, go to following URL: `https://localhost:5001/api/values`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3aa1f-752">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="3aa1f-752">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="3aa1f-753">Wybierz pozycję **Uruchom**  >  **Rozpocznij debugowanie** , aby uruchomić aplikację.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-753">Select **Run** > **Start Debugging** to launch the app.</span></span> <span data-ttu-id="3aa1f-754">Visual Studio dla komputerów Mac uruchamia przeglądarkę i nawiguje do `https://localhost:<port>` , gdzie `<port>` jest losowo wybranym numerem portu.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-754">Visual Studio for Mac launches a browser and navigates to `https://localhost:<port>`, where `<port>` is a randomly chosen port number.</span></span> <span data-ttu-id="3aa1f-755">Zwracany jest błąd HTTP 404 (nie znaleziono).</span><span class="sxs-lookup"><span data-stu-id="3aa1f-755">An HTTP 404 (Not Found) error is returned.</span></span> <span data-ttu-id="3aa1f-756">Dołącz `/api/values` do adresu URL (Zmień adres URL na `https://localhost:<port>/api/values` ).</span><span class="sxs-lookup"><span data-stu-id="3aa1f-756">Append `/api/values` to the URL (change the URL to `https://localhost:<port>/api/values`).</span></span>

---

<span data-ttu-id="3aa1f-757">Zostanie zwrócony następujący kod JSON:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-757">The following JSON is returned:</span></span>

```json
["value1","value2"]
```

## <a name="add-a-model-class-21"></a><span data-ttu-id="3aa1f-758">Dodaj klasę modelu 2,1</span><span class="sxs-lookup"><span data-stu-id="3aa1f-758">Add a model class 2.1</span></span>

<span data-ttu-id="3aa1f-759">*Model* to zestaw klas, które reprezentują dane zarządzane przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-759">A *model* is a set of classes that represent the data that the app manages.</span></span> <span data-ttu-id="3aa1f-760">Model tej aplikacji jest pojedynczą `TodoItem` klasą.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-760">The model for this app is a single `TodoItem` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3aa1f-761">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3aa1f-761">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3aa1f-762">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-762">In **Solution Explorer**, right-click the project.</span></span> <span data-ttu-id="3aa1f-763">Wybierz pozycję **Dodaj**  >  **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-763">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="3aa1f-764">Nazwij folder *Models* .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-764">Name the folder *Models*.</span></span>

* <span data-ttu-id="3aa1f-765">Kliknij prawym przyciskiem myszy *Models* folder i wybierz polecenie **Dodaj**  >  **klasę**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-765">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="3aa1f-766">Nadaj klasie nazwę *TodoItem* i wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-766">Name the class *TodoItem* and select **Add**.</span></span>

* <span data-ttu-id="3aa1f-767">Zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-767">Replace the template code with the following code:</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="3aa1f-768">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3aa1f-768">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3aa1f-769">Dodaj folder o nazwie *Models* .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-769">Add a folder named *Models*.</span></span>

* <span data-ttu-id="3aa1f-770">Dodaj `TodoItem` do *Models* folderu klasę o następującym kodzie:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-770">Add a `TodoItem` class to the *Models* folder with the following code:</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="3aa1f-771">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="3aa1f-771">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3aa1f-772">Kliknij prawym przyciskiem myszy projekt.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-772">Right-click the project.</span></span> <span data-ttu-id="3aa1f-773">Wybierz pozycję **Dodaj**  >  **Nowy folder**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-773">Select **Add** > **New Folder**.</span></span> <span data-ttu-id="3aa1f-774">Nazwij folder *Models* .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-774">Name the folder *Models*.</span></span>

  ![Nowy folder](first-web-api-mac/_static/folder.png)

* <span data-ttu-id="3aa1f-776">Kliknij prawym przyciskiem myszy *Models* folder, a następnie wybierz pozycję **Dodaj** > **nowy plik** > **ogólna** > **pusta Klasa**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-776">Right-click the *Models* folder, and select **Add** > **New File** > **General** > **Empty Class**.</span></span>

* <span data-ttu-id="3aa1f-777">Nazwij klasę *TodoItem*, a następnie kliknij pozycję **New (nowy**).</span><span class="sxs-lookup"><span data-stu-id="3aa1f-777">Name the class *TodoItem*, and then click **New**.</span></span>

* <span data-ttu-id="3aa1f-778">Zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-778">Replace the template code with the following code:</span></span>

---

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoItem.cs)]

<span data-ttu-id="3aa1f-779">`Id`Właściwość działa jako unikatowy klucz w relacyjnej bazie danych.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-779">The `Id` property functions as the unique key in a relational database.</span></span>

<span data-ttu-id="3aa1f-780">Klasy modelu mogą przejść do dowolnego miejsca w projekcie, ale *Models* folder jest używany przez Konwencję.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-780">Model classes can go anywhere in the project, but the *Models* folder is used by convention.</span></span>

## <a name="add-a-database-context-21"></a><span data-ttu-id="3aa1f-781">Dodawanie kontekstu bazy danych 2,1</span><span class="sxs-lookup"><span data-stu-id="3aa1f-781">Add a database context 2.1</span></span>

<span data-ttu-id="3aa1f-782">*Kontekst bazy danych* jest główną klasą, która koordynuje Entity Framework funkcji dla modelu danych.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-782">The *database context* is the main class that coordinates Entity Framework functionality for a data model.</span></span> <span data-ttu-id="3aa1f-783">Ta klasa jest tworzona przez wyprowadzanie z `Microsoft.EntityFrameworkCore.DbContext` klasy.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-783">This class is created by deriving from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3aa1f-784">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3aa1f-784">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3aa1f-785">Kliknij prawym przyciskiem myszy *Models* folder i wybierz polecenie **Dodaj**  >  **klasę**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-785">Right-click the *Models* folder and select **Add** > **Class**.</span></span> <span data-ttu-id="3aa1f-786">Nadaj klasie nazwę *TodoContext* i kliknij przycisk **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-786">Name the class *TodoContext* and click **Add**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3aa1f-787">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="3aa1f-787">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="3aa1f-788">Dodaj `TodoContext` klasę do *Models* folderu.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-788">Add a `TodoContext` class to the *Models* folder.</span></span>

---

* <span data-ttu-id="3aa1f-789">Zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-789">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Models/TodoContext.cs)]

## <a name="register-the-database-context-21"></a><span data-ttu-id="3aa1f-790">Rejestrowanie kontekstu bazy danych 2,1</span><span class="sxs-lookup"><span data-stu-id="3aa1f-790">Register the database context 2.1</span></span>

<span data-ttu-id="3aa1f-791">W ASP.NET Core usługi, takie jak kontekst bazy danych, muszą być zarejestrowane z kontenerem [iniekcji zależności (di)](xref:fundamentals/dependency-injection) .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-791">In ASP.NET Core, services such as the DB context must be registered with the [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="3aa1f-792">Kontener udostępnia usługę kontrolerom.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-792">The container provides the service to controllers.</span></span>

<span data-ttu-id="3aa1f-793">Zaktualizuj *Startup.cs* o następujący wyróżniony kod:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-793">Update *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup1.cs?highlight=5,8,25-26&name=snippet_all)]

<span data-ttu-id="3aa1f-794">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-794">The preceding code:</span></span>

* <span data-ttu-id="3aa1f-795">Usuwa nieużywane `using` deklaracje.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-795">Removes unused `using` declarations.</span></span>
* <span data-ttu-id="3aa1f-796">Dodaje kontekst bazy danych do kontenera DI.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-796">Adds the database context to the DI container.</span></span>
* <span data-ttu-id="3aa1f-797">Określa, że kontekst bazy danych będzie używać bazy danych w pamięci.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-797">Specifies that the database context will use an in-memory database.</span></span>

## <a name="add-a-controller-21"></a><span data-ttu-id="3aa1f-798">Dodawanie kontrolera 2,1</span><span class="sxs-lookup"><span data-stu-id="3aa1f-798">Add a controller 2.1</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3aa1f-799">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3aa1f-799">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3aa1f-800">Kliknij prawym przyciskiem myszy folder *controllers* .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-800">Right-click the *Controllers* folder.</span></span>
* <span data-ttu-id="3aa1f-801">Wybierz pozycję **Dodaj** > **nowy element**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-801">Select **Add** > **New Item**.</span></span>
* <span data-ttu-id="3aa1f-802">W oknie dialogowym **Dodaj nowy element** wybierz szablon **Klasa kontrolera interfejsu API** .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-802">In the **Add New Item** dialog, select the **API Controller Class** template.</span></span>
* <span data-ttu-id="3aa1f-803">Nadaj klasie nazwę *TodoController* i wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-803">Name the class *TodoController*, and select **Add**.</span></span>

  ![Okno dialogowe Dodawanie nowego elementu z kontrolerem w polu wyszukiwania i wybranym kontrolerem interfejsu API sieci Web](first-web-api/_static/new_controller.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3aa1f-805">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="3aa1f-805">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="3aa1f-806">W folderze *controllers* Utwórz klasę o nazwie `TodoController` .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-806">In the *Controllers* folder, create a class named `TodoController`.</span></span>

---

* <span data-ttu-id="3aa1f-807">Zastąp kod szablonu następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-807">Replace the template code with the following code:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController2.cs?name=snippet_todo1)]

<span data-ttu-id="3aa1f-808">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-808">The preceding code:</span></span>

* <span data-ttu-id="3aa1f-809">Definiuje klasę kontrolera interfejsu API bez metod.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-809">Defines an API controller class without methods.</span></span>
* <span data-ttu-id="3aa1f-810">Oznacza klasę [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) atrybutem.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-810">Marks the class with the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute.</span></span> <span data-ttu-id="3aa1f-811">Ten atrybut wskazuje, że kontroler odpowiada na żądania interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-811">This attribute indicates that the controller responds to web API requests.</span></span> <span data-ttu-id="3aa1f-812">Aby uzyskać informacje o określonych zachowaniach, które włącza atrybut, zobacz <xref:web-api/index> .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-812">For information about specific behaviors that the attribute enables, see <xref:web-api/index>.</span></span>
* <span data-ttu-id="3aa1f-813">Używa funkcji DI do iniekcji kontekstu bazy danych ( `TodoContext` ) do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-813">Uses DI to inject the database context (`TodoContext`) into the controller.</span></span> <span data-ttu-id="3aa1f-814">Kontekst bazy danych jest używany w każdej z metod [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) w kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-814">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>
* <span data-ttu-id="3aa1f-815">Dodaje element o nazwie `Item1` do bazy danych, jeśli baza danych jest pusta.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-815">Adds an item named `Item1` to the database if the database is empty.</span></span> <span data-ttu-id="3aa1f-816">Ten kod znajduje się w konstruktorze, więc jest uruchamiany za każdym razem, gdy istnieje nowe żądanie HTTP.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-816">This code is in the constructor, so it runs every time there's a new HTTP request.</span></span> <span data-ttu-id="3aa1f-817">Jeśli usuniesz wszystkie elementy, Konstruktor zostanie ponownie utworzony `Item1` przy następnym wywołaniu metody interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-817">If you delete all items, the constructor creates `Item1` again the next time an API method is called.</span></span> <span data-ttu-id="3aa1f-818">Dlatego może wyglądać podobnie jak usunięcie nie zadziałało, gdy rzeczywiście zadziałało.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-818">So it may look like the deletion didn't work when it actually did work.</span></span>

## <a name="add-get-methods-21"></a><span data-ttu-id="3aa1f-819">Dodaj metody get 2,1</span><span class="sxs-lookup"><span data-stu-id="3aa1f-819">Add Get methods 2.1</span></span>

<span data-ttu-id="3aa1f-820">Aby udostępnić interfejs API, który pobiera elementy do wykonania, Dodaj następujące metody do `TodoController` klasy:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-820">To provide an API that retrieves to-do items, add the following methods to the `TodoController` class:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetAll)]

<span data-ttu-id="3aa1f-821">Te metody implementują dwa punkty końcowe GET:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-821">These methods implement two GET endpoints:</span></span>

* `GET /api/todo`
* `GET /api/todo/{id}`

<span data-ttu-id="3aa1f-822">Zatrzymaj aplikację, jeśli jest nadal uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-822">Stop the app if it's still running.</span></span> <span data-ttu-id="3aa1f-823">Następnie uruchom ją ponownie, aby uwzględnić najnowsze zmiany.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-823">Then run it again to include the latest changes.</span></span>

<span data-ttu-id="3aa1f-824">Przetestuj aplikację, wywołując dwa punkty końcowe z przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-824">Test the app by calling the two endpoints from a browser.</span></span> <span data-ttu-id="3aa1f-825">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-825">For example:</span></span>

* `https://localhost:<port>/api/todo`
* `https://localhost:<port>/api/todo/1`

<span data-ttu-id="3aa1f-826">Następująca odpowiedź HTTP jest generowana przez wywołanie `GetTodoItems` :</span><span class="sxs-lookup"><span data-stu-id="3aa1f-826">The following HTTP response is produced by the call to `GetTodoItems`:</span></span>

```json
[
  {
    "id": 1,
    "name": "Item1",
    "isComplete": false
  }
]
```

## <a name="routing-and-url-paths-21"></a><span data-ttu-id="3aa1f-827">Ścieżki routingu i adresu URL 2,1</span><span class="sxs-lookup"><span data-stu-id="3aa1f-827">Routing and URL paths 2.1</span></span>

<span data-ttu-id="3aa1f-828">Ten [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) atrybut oznacza metodę, która reaguje na żądanie HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-828">The [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute denotes a method that responds to an HTTP GET request.</span></span> <span data-ttu-id="3aa1f-829">Ścieżka adresu URL dla każdej metody jest zbudowana w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-829">The URL path for each method is constructed as follows:</span></span>

* <span data-ttu-id="3aa1f-830">Zacznij od ciągu szablonu w `Route` atrybucie kontrolera:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-830">Start with the template string in the controller's `Route` attribute:</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=TodoController&highlight=3)]

* <span data-ttu-id="3aa1f-831">Zastąp `[controller]` nazwą kontrolera, którą Konwencją jest nazwa klasy kontrolera minus sufiks "Controller".</span><span class="sxs-lookup"><span data-stu-id="3aa1f-831">Replace `[controller]` with the name of the controller, which by convention is the controller class name minus the "Controller" suffix.</span></span> <span data-ttu-id="3aa1f-832">W przypadku tego przykładu nazwa klasy kontrolera to kontroler do **zrobienia**, więc nazwa kontrolera to "do zrobienia".</span><span class="sxs-lookup"><span data-stu-id="3aa1f-832">For this sample, the controller class name is **Todo** Controller, so the controller name is "todo".</span></span> <span data-ttu-id="3aa1f-833">W ASP.NET Core [routingu](xref:mvc/controllers/routing) jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-833">ASP.NET Core [routing](xref:mvc/controllers/routing) is case insensitive.</span></span>
* <span data-ttu-id="3aa1f-834">Jeśli `[HttpGet]` atrybut ma szablon trasy (na przykład `[HttpGet("products")]` ), Dodaj go do ścieżki.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-834">If the `[HttpGet]` attribute has a route template (for example, `[HttpGet("products")]`), append that to the path.</span></span> <span data-ttu-id="3aa1f-835">Ten przykład nie używa szablonu.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-835">This sample doesn't use a template.</span></span> <span data-ttu-id="3aa1f-836">Aby uzyskać więcej informacji, zobacz temat [Routing atrybutów z atrybutami http [Verb]](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span><span class="sxs-lookup"><span data-stu-id="3aa1f-836">For more information, see [Attribute routing with Http[Verb] attributes](xref:mvc/controllers/routing#attribute-routing-with-httpverb-attributes).</span></span>

<span data-ttu-id="3aa1f-837">W poniższej `GetTodoItem` metodzie `"{id}"` jest zmienną zastępczą dla unikatowego identyfikatora elementu do wykonania.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-837">In the following `GetTodoItem` method, `"{id}"` is a placeholder variable for the unique identifier of the to-do item.</span></span> <span data-ttu-id="3aa1f-838">Gdy `GetTodoItem` jest wywoływana, wartość `"{id}"` w adresie URL jest podawana do metody w jej `id` parametrze.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-838">When `GetTodoItem` is invoked, the value of `"{id}"` in the URL is provided to the method in its`id` parameter.</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

## <a name="return-values-21"></a><span data-ttu-id="3aa1f-839">Wartości zwracane 2,1</span><span class="sxs-lookup"><span data-stu-id="3aa1f-839">Return values 2.1</span></span>

<span data-ttu-id="3aa1f-840">Zwracany typ `GetTodoItems` `GetTodoItem` metod i jest [ \<T> typem ActionResult](xref:web-api/action-return-types#actionresultt-type).</span><span class="sxs-lookup"><span data-stu-id="3aa1f-840">The return type of the `GetTodoItems` and `GetTodoItem` methods is [ActionResult\<T> type](xref:web-api/action-return-types#actionresultt-type).</span></span> <span data-ttu-id="3aa1f-841">ASP.NET Core automatycznie serializować obiektu do [formatu JSON](https://www.json.org/) i zapisuje kod JSON w treści komunikatu odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-841">ASP.NET Core automatically serializes the object to [JSON](https://www.json.org/) and writes the JSON into the body of the response message.</span></span> <span data-ttu-id="3aa1f-842">Kod odpowiedzi dla tego typu zwracanego to 200, przy założeniu, że nie istnieją Nieobsłużone wyjątki.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-842">The response code for this return type is 200, assuming there are no unhandled exceptions.</span></span> <span data-ttu-id="3aa1f-843">Nieobsłużone wyjątki są tłumaczone na błędy 5xx.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-843">Unhandled exceptions are translated into 5xx errors.</span></span>

<span data-ttu-id="3aa1f-844">`ActionResult` typy zwracane mogą reprezentować szeroką gamę kodów stanu HTTP.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-844">`ActionResult` return types can represent a wide range of HTTP status codes.</span></span> <span data-ttu-id="3aa1f-845">Na przykład `GetTodoItem` może zwracać dwie różne wartości stanu:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-845">For example, `GetTodoItem` can return two different status values:</span></span>

* <span data-ttu-id="3aa1f-846">Jeśli żaden element nie jest zgodny z żądanym IDENTYFIKATORem, metoda zwróci <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> Kod błędu 404.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-846">If no item matches the requested ID, the method returns a 404 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A> error code.</span></span>
* <span data-ttu-id="3aa1f-847">W przeciwnym razie metoda zwraca 200 z treścią odpowiedzi JSON.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-847">Otherwise, the method returns 200 with a JSON response body.</span></span> <span data-ttu-id="3aa1f-848">Zwracanie `item` wyników w odpowiedzi HTTP 200.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-848">Returning `item` results in an HTTP 200 response.</span></span>

## <a name="test-the-gettodoitems-method-21"></a><span data-ttu-id="3aa1f-849">Testowanie metody GetTodoItems 2,1</span><span class="sxs-lookup"><span data-stu-id="3aa1f-849">Test the GetTodoItems method 2.1</span></span>

<span data-ttu-id="3aa1f-850">Ten samouczek używa programu do testowania interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-850">This tutorial uses Postman to test the web API.</span></span>

* <span data-ttu-id="3aa1f-851">Zainstaluj program [Poster](https://www.getpostman.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="3aa1f-851">Install [Postman](https://www.getpostman.com/downloads/).</span></span>
* <span data-ttu-id="3aa1f-852">Uruchom aplikację internetową.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-852">Start the web app.</span></span>
* <span data-ttu-id="3aa1f-853">Uruchom wpis.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-853">Start Postman.</span></span>
* <span data-ttu-id="3aa1f-854">Wyłącz **weryfikację certyfikatu SSL**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-854">Disable **SSL certificate verification**.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3aa1f-855">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3aa1f-855">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3aa1f-856">W  obszarze > **Ustawienia** pliku (karta **Ogólne** ) Wyłącz **weryfikację certyfikatu SSL**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-856">From **File** > **Settings** (**General** tab), disable **SSL certificate verification**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="3aa1f-857">Visual Studio Code/Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="3aa1f-857">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="3aa1f-858">Z poziomu preferencji programu **Poster**  >   (karta **Ogólne** ) Wyłącz **weryfikację certyfikatu SSL**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-858">From **Postman** > **Preferences** (**General** tab), disable **SSL certificate verification**.</span></span> <span data-ttu-id="3aa1f-859">Alternatywnie wybierz klucz i wybierz pozycję **Ustawienia**, a następnie wyłącz weryfikację certyfikatu SSL.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-859">Alternatively, select the wrench and select **Settings**, then disable the SSL certificate verification.</span></span>

---
  
> [!WARNING]
> <span data-ttu-id="3aa1f-860">Po przetestowaniu kontrolera ponownie Włącz weryfikację certyfikatu SSL.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-860">Re-enable SSL certificate verification after testing the controller.</span></span>

* <span data-ttu-id="3aa1f-861">Utwórz nowe żądanie.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-861">Create a new request.</span></span>
  * <span data-ttu-id="3aa1f-862">Ustaw metodę HTTP, aby **uzyskać**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-862">Set the HTTP method to **GET**.</span></span>
  * <span data-ttu-id="3aa1f-863">Ustaw identyfikator URI żądania na `https://localhost:<port>/api/todo` .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-863">Set the request URI to `https://localhost:<port>/api/todo`.</span></span> <span data-ttu-id="3aa1f-864">Na przykład `https://localhost:5001/api/todo`.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-864">For example, `https://localhost:5001/api/todo`.</span></span>
* <span data-ttu-id="3aa1f-865">Ustaw **dwa widoki okienka** w programie Poster.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-865">Set **Two pane view** in Postman.</span></span>
* <span data-ttu-id="3aa1f-866">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-866">Select **Send**.</span></span>

![Ogłoś przy użyciu żądania GET](first-web-api/_static/2pv.png)

## <a name="add-a-create-method-21"></a><span data-ttu-id="3aa1f-868">Dodawanie metody Create 2,1</span><span class="sxs-lookup"><span data-stu-id="3aa1f-868">Add a Create method 2.1</span></span>

<span data-ttu-id="3aa1f-869">Dodaj następującą `PostTodoItem` metodę w obszarze *controllers/TodoController. cs*:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-869">Add the following `PostTodoItem` method inside of *Controllers/TodoController.cs*:</span></span> 

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Create)]

<span data-ttu-id="3aa1f-870">Poprzedni kod jest metodą POST protokołu HTTP, jak wskazano w [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) atrybucie.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-870">The preceding code is an HTTP POST method, as indicated by the [`[HttpPost]`](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute) attribute.</span></span> <span data-ttu-id="3aa1f-871">Metoda pobiera wartość elementu do wykonania z treści żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-871">The method gets the value of the to-do item from the body of the HTTP request.</span></span>

<span data-ttu-id="3aa1f-872">`CreatedAtAction`Metoda:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-872">The `CreatedAtAction` method:</span></span>

* <span data-ttu-id="3aa1f-873">Zwraca kod stanu HTTP 201, jeśli powodzenie.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-873">Returns an HTTP 201 status code, if successful.</span></span> <span data-ttu-id="3aa1f-874">HTTP 201 to standardowa odpowiedź dla metody POST protokołu HTTP, która tworzy nowy zasób na serwerze.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-874">HTTP 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span>
* <span data-ttu-id="3aa1f-875">Dodaje `Location` nagłówek do odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-875">Adds a `Location` header to the response.</span></span> <span data-ttu-id="3aa1f-876">`Location`Nagłówek określa identyfikator URI nowo utworzonego elementu do wykonania.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-876">The `Location` header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="3aa1f-877">Aby uzyskać więcej informacji, zobacz [10.2.2 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="3aa1f-877">For more information, see [10.2.2 201 Created](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
* <span data-ttu-id="3aa1f-878">Odwołuje się do `GetTodoItem` akcji tworzenia `Location` identyfikatora URI nagłówka.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-878">References the `GetTodoItem` action to create the `Location` header's URI.</span></span> <span data-ttu-id="3aa1f-879">`nameof`Słowo kluczowe języka C# służy do zapobiegania twardemu kodowaniu nazwy akcji w `CreatedAtAction` wywołaniu.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-879">The C# `nameof` keyword is used to avoid hard-coding the action name in the `CreatedAtAction` call.</span></span>

  [!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_GetByID&highlight=1-2)]

### <a name="test-the-posttodoitem-method-21"></a><span data-ttu-id="3aa1f-880">Testowanie metody PostTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="3aa1f-880">Test the PostTodoItem method 2.1</span></span>

* <span data-ttu-id="3aa1f-881">Skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-881">Build the project.</span></span>
* <span data-ttu-id="3aa1f-882">W programie Poster ustaw metodę HTTP na `POST` .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-882">In Postman, set the HTTP method to `POST`.</span></span>
* <span data-ttu-id="3aa1f-883">Ustaw identyfikator URI na `https://localhost:<port>/api/Todo` .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-883">Set the URI to `https://localhost:<port>/api/Todo`.</span></span> <span data-ttu-id="3aa1f-884">Na przykład `https://localhost:5001/api/Todo`.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-884">For example, `https://localhost:5001/api/Todo`.</span></span>
* <span data-ttu-id="3aa1f-885">Wybierz kartę **Treść**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-885">Select the **Body** tab.</span></span>
* <span data-ttu-id="3aa1f-886">Wybierz przycisk radiowy **RAW** .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-886">Select the **raw** radio button.</span></span>
* <span data-ttu-id="3aa1f-887">Ustaw typ na **JSON (Application/JSON)**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-887">Set the type to **JSON (application/json)**.</span></span>
* <span data-ttu-id="3aa1f-888">W treści żądania wprowadź kod JSON dla elementu do wykonania:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-888">In the request body enter JSON for a to-do item:</span></span>

    ```json
    {
      "name":"walk dog",
      "isComplete":true
    }
    ```

* <span data-ttu-id="3aa1f-889">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-889">Select **Send**.</span></span>

  ![Ogłoś przy użyciu żądania Create](first-web-api/_static/create.png)

  <span data-ttu-id="3aa1f-891">Jeśli wystąpi błąd 405 metody niedozwolonej, jest to prawdopodobnie wynik niekompilowania projektu po dodaniu `PostTodoItem` metody.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-891">If you get a 405 Method Not Allowed error, it's probably the result of not compiling the project after adding the `PostTodoItem` method.</span></span>

### <a name="test-the-location-header-uri-21"></a><span data-ttu-id="3aa1f-892">Testowanie identyfikatora URI nagłówka lokalizacji 2,1</span><span class="sxs-lookup"><span data-stu-id="3aa1f-892">Test the location header URI 2.1</span></span>

* <span data-ttu-id="3aa1f-893">Wybierz kartę **nagłówki** w okienku **odpowiedź** .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-893">Select the **Headers** tab in the **Response** pane.</span></span>
* <span data-ttu-id="3aa1f-894">Skopiuj wartość nagłówka **lokalizacji** :</span><span class="sxs-lookup"><span data-stu-id="3aa1f-894">Copy the **Location** header value:</span></span>

  ![Karta nagłówki w konsoli programu Poster](first-web-api/_static/pmc2.png)

* <span data-ttu-id="3aa1f-896">Ustaw metodę, aby uzyskać.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-896">Set the method to GET.</span></span>
* <span data-ttu-id="3aa1f-897">Ustaw identyfikator URI na `https://localhost:<port>/api/TodoItems/2` .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-897">Set the URI to `https://localhost:<port>/api/TodoItems/2`.</span></span> <span data-ttu-id="3aa1f-898">Na przykład `https://localhost:5001/api/TodoItems/2`.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-898">For example, `https://localhost:5001/api/TodoItems/2`.</span></span>
* <span data-ttu-id="3aa1f-899">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-899">Select **Send**.</span></span>

## <a name="add-a-puttodoitem-method-21"></a><span data-ttu-id="3aa1f-900">Dodawanie metody PutTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="3aa1f-900">Add a PutTodoItem method 2.1</span></span>

<span data-ttu-id="3aa1f-901">Dodaj następującą `PutTodoItem` metodę:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-901">Add the following `PutTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Update)]

<span data-ttu-id="3aa1f-902">`PutTodoItem` jest podobny do `PostTodoItem` , z tą różnicą, że używa protokołu HTTP Put.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-902">`PutTodoItem` is similar to `PostTodoItem`, except it uses HTTP PUT.</span></span> <span data-ttu-id="3aa1f-903">Odpowiedź to [204 (brak zawartości)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="3aa1f-903">The response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span> <span data-ttu-id="3aa1f-904">Zgodnie ze specyfikacją protokołu HTTP żądanie PUT wymaga, aby klient wysłał całą zaktualizowaną jednostkę, a nie tylko te zmiany.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-904">According to the HTTP specification, a PUT request requires the client to send the entire updated entity, not just the changes.</span></span> <span data-ttu-id="3aa1f-905">Aby zapewnić obsługę częściowych aktualizacji, użyj [poprawki http](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span><span class="sxs-lookup"><span data-stu-id="3aa1f-905">To support partial updates, use [HTTP PATCH](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute).</span></span>

<span data-ttu-id="3aa1f-906">Jeśli wystąpi błąd podczas wywoływania `PutTodoItem` , wywołaj, `GET` Aby upewnić się, że w bazie danych znajduje się element.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-906">If you get an error calling `PutTodoItem`, call `GET` to ensure there's an item in the database.</span></span>

### <a name="test-the-puttodoitem-method-21"></a><span data-ttu-id="3aa1f-907">Testowanie metody PutTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="3aa1f-907">Test the PutTodoItem method 2.1</span></span>

<span data-ttu-id="3aa1f-908">Ten przykład korzysta z bazy danych w pamięci, która musi zostać zainicjowana za każdym razem, gdy aplikacja zostanie uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-908">This sample uses an in-memory database that must be initialized each time the app is started.</span></span> <span data-ttu-id="3aa1f-909">Przed wykonaniem wywołania PUT musi istnieć element w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-909">There must be an item in the database before you make a PUT call.</span></span> <span data-ttu-id="3aa1f-910">Przed wykonaniem wywołania PUT należy wywołać metodę GET, aby upewnić się, że istnieje element w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-910">Call GET to ensure there's an item in the database before making a PUT call.</span></span>

<span data-ttu-id="3aa1f-911">Zaktualizuj element do wykonania o identyfikatorze 1 i ustaw jego nazwę na "Źródło danych":</span><span class="sxs-lookup"><span data-stu-id="3aa1f-911">Update the to-do item that has Id = 1 and set its name to "feed fish":</span></span>

```json
  {
    "id":1,
    "name":"feed fish",
    "isComplete":true
  }
```

<span data-ttu-id="3aa1f-912">Na poniższej ilustracji przedstawiono aktualizację programu Poster:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-912">The following image shows the Postman update:</span></span>

![Konsola programu Poster pokazująca odpowiedź 204 (brak zawartości)](first-web-api/_static/pmcput.png)

## <a name="add-a-deletetodoitem-method-21"></a><span data-ttu-id="3aa1f-914">Dodawanie metody DeleteTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="3aa1f-914">Add a DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="3aa1f-915">Dodaj następującą `DeleteTodoItem` metodę:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-915">Add the following `DeleteTodoItem` method:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Controllers/TodoController.cs?name=snippet_Delete)]

<span data-ttu-id="3aa1f-916">`DeleteTodoItem`Odpowiedź to [204 (brak zawartości)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span><span class="sxs-lookup"><span data-stu-id="3aa1f-916">The `DeleteTodoItem` response is [204 (No Content)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html).</span></span>

### <a name="test-the-deletetodoitem-method-21"></a><span data-ttu-id="3aa1f-917">Testowanie metody DeleteTodoItem 2,1</span><span class="sxs-lookup"><span data-stu-id="3aa1f-917">Test the DeleteTodoItem method 2.1</span></span>

<span data-ttu-id="3aa1f-918">Użyj programu Poster, aby usunąć element do wykonania:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-918">Use Postman to delete a to-do item:</span></span>

* <span data-ttu-id="3aa1f-919">Ustaw metodę na `DELETE` .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-919">Set the method to `DELETE`.</span></span>
* <span data-ttu-id="3aa1f-920">Ustaw identyfikator URI obiektu do usunięcia (na przykład `https://localhost:5001/api/todo/1` ).</span><span class="sxs-lookup"><span data-stu-id="3aa1f-920">Set the URI of the object to delete (for example, `https://localhost:5001/api/todo/1`).</span></span>
* <span data-ttu-id="3aa1f-921">Wybierz pozycję **Wyślij**.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-921">Select **Send**.</span></span>

<span data-ttu-id="3aa1f-922">Przykładowa aplikacja umożliwia usunięcie wszystkich elementów.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-922">The sample app allows you to delete all the items.</span></span> <span data-ttu-id="3aa1f-923">Jednak po usunięciu ostatniego elementu jest on tworzony przez konstruktora klasy modelu przy następnym wywołaniu interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-923">However, when the last item is deleted, a new one is created by the model class constructor the next time the API is called.</span></span>

## <a name="call-the-web-api-with-javascript-21"></a><span data-ttu-id="3aa1f-924">Wywoływanie interfejsu API sieci Web przy użyciu języka JavaScript 2,1</span><span class="sxs-lookup"><span data-stu-id="3aa1f-924">Call the web API with JavaScript 2.1</span></span>

<span data-ttu-id="3aa1f-925">W tej sekcji zostanie dodana strona HTML, która używa języka JavaScript do wywoływania internetowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-925">In this section, an HTML page is added that uses JavaScript to call the web API.</span></span> <span data-ttu-id="3aa1f-926">jQuery inicjuje żądanie.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-926">jQuery initiates the request.</span></span> <span data-ttu-id="3aa1f-927">Język JavaScript aktualizuje stronę ze szczegółowymi informacjami z odpowiedzi internetowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-927">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="3aa1f-928">Skonfiguruj aplikację do [obsługi plików statycznych](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) i [Włącz domyślne mapowanie plików](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) , aktualizując *Startup.cs* z następującym wyróżnionym kodem:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-928">Configure the app to [serve static files](xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A) and [enable default file mapping](xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles%2A) by updating *Startup.cs* with the following highlighted code:</span></span>

[!code-csharp[](first-web-api/samples/2.2/TodoApi/Startup.cs?highlight=14-15&name=snippet_configure)]

<span data-ttu-id="3aa1f-929">Utwórz folder *wwwroot* w katalogu projektu.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-929">Create a *wwwroot* folder in the project directory.</span></span>

<span data-ttu-id="3aa1f-930">Dodaj plik HTML o nazwie *index.html* do katalogu *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-930">Add an HTML file named *index.html* to the *wwwroot* directory.</span></span> <span data-ttu-id="3aa1f-931">Zastąp jego zawartość następującym znacznikiem:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-931">Replace its contents with the following markup:</span></span>

[!code-html[](first-web-api/samples/2.2/TodoApi/wwwroot/index.html)]

<span data-ttu-id="3aa1f-932">Dodaj plik języka JavaScript o nazwie *site.js* do katalogu *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-932">Add a JavaScript file named *site.js* to the *wwwroot* directory.</span></span> <span data-ttu-id="3aa1f-933">Zastąp zawartość poniższym kodem:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-933">Replace its contents with the following code:</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_SiteJs)]

<span data-ttu-id="3aa1f-934">Zmiana ustawień uruchamiania projektu ASP.NET Core może być wymagana do lokalnego przetestowania strony HTML:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-934">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

* <span data-ttu-id="3aa1f-935">Otwórz *Properties\launchSettings.jsna*.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-935">Open *Properties\launchSettings.json*.</span></span>
* <span data-ttu-id="3aa1f-936">Usuń `launchUrl` Właściwość, aby wymusić otwieranie przez aplikację w *index.html* &mdash; domyślnego pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-936">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="3aa1f-937">Ten przykład wywołuje wszystkie metody CRUD internetowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-937">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="3aa1f-938">Poniżej znajdują się wyjaśnienia wywołań interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-938">Following are explanations of the calls to the API.</span></span>

### <a name="get-a-list-of-to-do-items-21"></a><span data-ttu-id="3aa1f-939">Pobierz listę elementów do wykonania 2,1</span><span class="sxs-lookup"><span data-stu-id="3aa1f-939">Get a list of to-do items 2.1</span></span>

<span data-ttu-id="3aa1f-940">jQuery wysyła żądanie HTTP GET do internetowego interfejsu API, który zwraca kod JSON reprezentujący tablicę elementów do wykonania.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-940">jQuery sends an HTTP GET request to the web API, which returns JSON representing an array of to-do items.</span></span> <span data-ttu-id="3aa1f-941">`success`Funkcja wywołania zwrotnego jest wywoływana, jeśli żądanie zakończy się pomyślnie.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-941">The `success` callback function is invoked if the request succeeds.</span></span> <span data-ttu-id="3aa1f-942">W wywołaniu zwrotnym model DOM jest aktualizowany przy użyciu informacji o tym do wykonania.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-942">In the callback, the DOM is updated with the to-do information.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_GetData)]

### <a name="add-a-to-do-item-21"></a><span data-ttu-id="3aa1f-943">Dodaj element do wykonania 2,1</span><span class="sxs-lookup"><span data-stu-id="3aa1f-943">Add a to-do item 2.1</span></span>

<span data-ttu-id="3aa1f-944">jQuery wysyła żądanie HTTP POST z elementem do wykonania w treści żądania.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-944">jQuery sends an HTTP POST request with the to-do item in the request body.</span></span> <span data-ttu-id="3aa1f-945">`accepts`Opcje i `contentType` są ustawione na, aby `application/json` określić typ nośnika odbierany i wysyłany.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-945">The `accepts` and `contentType` options are set to `application/json` to specify the media type being received and sent.</span></span> <span data-ttu-id="3aa1f-946">Element do wykonania jest konwertowany na format JSON przy użyciu [formatu JSON. stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span><span class="sxs-lookup"><span data-stu-id="3aa1f-946">The to-do item is converted to JSON by using [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify).</span></span> <span data-ttu-id="3aa1f-947">Gdy interfejs API zwraca kod stanu pomyślnego, `getData` Funkcja jest wywoływana w celu zaktualizowania tabeli HTML.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-947">When the API returns a successful status code, the `getData` function is invoked to update the HTML table.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AddItem)]

### <a name="update-a-to-do-item-21"></a><span data-ttu-id="3aa1f-948">Aktualizowanie elementu do wykonania 2,1</span><span class="sxs-lookup"><span data-stu-id="3aa1f-948">Update a to-do item 2.1</span></span>

<span data-ttu-id="3aa1f-949">Aktualizowanie elementu do wykonania jest podobne do dodawania jednego.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-949">Updating a to-do item is similar to adding one.</span></span> <span data-ttu-id="3aa1f-950">`url`Zmiany mające na celu dodanie unikatowego identyfikatora elementu i `type` is `PUT` .</span><span class="sxs-lookup"><span data-stu-id="3aa1f-950">The `url` changes to add the unique identifier of the item, and the `type` is `PUT`.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxPut)]

### <a name="delete-a-to-do-item-21"></a><span data-ttu-id="3aa1f-951">Usuń element do wykonania 2,1</span><span class="sxs-lookup"><span data-stu-id="3aa1f-951">Delete a to-do item 2.1</span></span>

<span data-ttu-id="3aa1f-952">Usuwanie elementu do wykonania jest realizowane przez ustawienie `type` w WYWOŁANIU AJAX `DELETE` i określenie unikatowego identyfikatora elementu w adresie URL.</span><span class="sxs-lookup"><span data-stu-id="3aa1f-952">Deleting a to-do item is accomplished by setting the `type` on the AJAX call to `DELETE` and specifying the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/2.2/TodoApi/wwwroot/site.js?name=snippet_AjaxDelete)]

::: moniker-end

<a name="auth"></a>

## <a name="add-authentication-support-to-a-web-api-21"></a><span data-ttu-id="3aa1f-953">Dodawanie obsługi uwierzytelniania do internetowego interfejsu API 2,1</span><span class="sxs-lookup"><span data-stu-id="3aa1f-953">Add authentication support to a web API 2.1</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

## <a name="additional-resources-21"></a><span data-ttu-id="3aa1f-954">Dodatkowe zasoby 2,1</span><span class="sxs-lookup"><span data-stu-id="3aa1f-954">Additional resources 2.1</span></span>

<span data-ttu-id="3aa1f-955">[Wyświetl lub Pobierz przykładowy kod dla tego samouczka](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/first-web-api/samples).</span><span class="sxs-lookup"><span data-stu-id="3aa1f-955">[View or download sample code for this tutorial](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/first-web-api/samples).</span></span> <span data-ttu-id="3aa1f-956">Zobacz artykuł [jak pobrać](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="3aa1f-956">See [how to download](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="3aa1f-957">Więcej informacji można znaleźć w następujących zasobach:</span><span class="sxs-lookup"><span data-stu-id="3aa1f-957">For more information, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:data/ef-rp/intro>
* <xref:mvc/controllers/routing>
* <xref:web-api/action-return-types>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/index>
* [<span data-ttu-id="3aa1f-958">Wersja tego samouczka usługi YouTube</span><span class="sxs-lookup"><span data-stu-id="3aa1f-958">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=TTkhEyGBfAk)
* [<span data-ttu-id="3aa1f-959">Microsoft Learn: Tworzenie interfejsu API sieci Web za pomocą ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3aa1f-959">Microsoft Learn: Create a web API with ASP.NET Core</span></span>](/learn/modules/build-web-api-aspnet-core/)
