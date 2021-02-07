---
title: Część 2, dodawanie kontrolera do aplikacji ASP.NET Core MVC
author: rick-anderson
description: Część 2 z serii samouczków na ASP.NET Core MVC.
ms.author: riande
ms.date: 01/23/2021
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
uid: tutorials/first-mvc-app/adding-controller
ms.custom: contperf-fy21q3
ms.openlocfilehash: 1f8291cca1923d42eeb1ce689b268a23b60537ba
ms.sourcegitcommit: 19a004ff2be73876a9ef0f1ac44d0331849ad159
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2021
ms.locfileid: "99804668"
---
# <a name="part-2-add-a-controller-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="83719-103">Część 2, dodawanie kontrolera do aplikacji ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="83719-103">Part 2, add a controller to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="83719-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="83719-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="83719-105">Wzorzec architektoniczny Model-View-Controller (MVC) oddziela aplikację do trzech głównych składników: **M** odelu, **V** każ i **C** ontroller.</span><span class="sxs-lookup"><span data-stu-id="83719-105">The Model-View-Controller (MVC) architectural pattern separates an app into three main components: **M** odel, **V** iew, and **C** ontroller.</span></span> <span data-ttu-id="83719-106">Wzorzec MVC pomaga tworzyć aplikacje, które są bardziej weryfikowalne i łatwiej aktualizować niż tradycyjne aplikacje monolityczne.</span><span class="sxs-lookup"><span data-stu-id="83719-106">The MVC pattern helps you create apps that are more testable and easier to update than traditional monolithic apps.</span></span>

<span data-ttu-id="83719-107">Aplikacje oparte na MVC zawierają:</span><span class="sxs-lookup"><span data-stu-id="83719-107">MVC-based apps contain:</span></span>

* <span data-ttu-id="83719-108">**M** Odels: klasy reprezentujące dane aplikacji.</span><span class="sxs-lookup"><span data-stu-id="83719-108">**M** odels: Classes that represent the data of the app.</span></span> <span data-ttu-id="83719-109">Klasy modeli używają logiki walidacji, aby wymusić reguły biznesowe dla tych danych.</span><span class="sxs-lookup"><span data-stu-id="83719-109">The model classes use validation logic to enforce business rules for that data.</span></span> <span data-ttu-id="83719-110">Zazwyczaj obiekty modelu pobierają i przechowują stan modelu w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="83719-110">Typically, model objects retrieve and store model state in a database.</span></span> <span data-ttu-id="83719-111">W tym samouczku `Movie` model pobiera dane filmu z bazy danych, udostępnia je widokowi lub aktualizuje.</span><span class="sxs-lookup"><span data-stu-id="83719-111">In this tutorial, a `Movie` model retrieves movie data from a database, provides it to the view or updates it.</span></span> <span data-ttu-id="83719-112">Zaktualizowane dane są zapisywane w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="83719-112">Updated data is written to a database.</span></span>
* <span data-ttu-id="83719-113">**V** Iews: widoki są składnikami, które wyświetlają interfejs użytkownika aplikacji.</span><span class="sxs-lookup"><span data-stu-id="83719-113">**V** iews: Views are the components that display the app's user interface (UI).</span></span> <span data-ttu-id="83719-114">Zazwyczaj ten interfejs użytkownika wyświetla dane modelu.</span><span class="sxs-lookup"><span data-stu-id="83719-114">Generally, this UI displays the model data.</span></span>
* <span data-ttu-id="83719-115">**C** Ontrollers: klasy, które:</span><span class="sxs-lookup"><span data-stu-id="83719-115">**C** ontrollers: Classes that:</span></span>
  * <span data-ttu-id="83719-116">Obsługa żądań przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="83719-116">Handle browser requests.</span></span>
  * <span data-ttu-id="83719-117">Pobieranie danych modelu.</span><span class="sxs-lookup"><span data-stu-id="83719-117">Retrieve model data.</span></span>
  * <span data-ttu-id="83719-118">Szablony wyświetlania wywołań, które zwracają odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="83719-118">Call view templates that return a response.</span></span>

<span data-ttu-id="83719-119">W aplikacji MVC widok wyświetla tylko informacje.</span><span class="sxs-lookup"><span data-stu-id="83719-119">In an MVC app, the view only displays information.</span></span> <span data-ttu-id="83719-120">Kontroler obsługuje i reaguje na dane wejściowe użytkownika i interakcje.</span><span class="sxs-lookup"><span data-stu-id="83719-120">The controller handles and responds to user input and interaction.</span></span> <span data-ttu-id="83719-121">Na przykład kontroler obsługuje segmenty adresu URL i wartości ciągu zapytania i przekazuje te wartości do modelu.</span><span class="sxs-lookup"><span data-stu-id="83719-121">For example, the controller handles URL segments and query-string values, and passes these values to the model.</span></span> <span data-ttu-id="83719-122">Model może używać tych wartości do wykonywania zapytań w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="83719-122">The model might use these values to query the database.</span></span> <span data-ttu-id="83719-123">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="83719-123">For example:</span></span>

* <span data-ttu-id="83719-124">`Https://localhost:5001/Home/Privacy`: określa `Home`  kontroler i `Privacy` akcję.</span><span class="sxs-lookup"><span data-stu-id="83719-124">`Https://localhost:5001/Home/Privacy`: specifies the `Home`  controller  and the `Privacy` action.</span></span>
* <span data-ttu-id="83719-125">`Https://localhost:5001/Movies/Edit/5`: to żądanie edycji filmu o IDENTYFIKATORze 5 przy użyciu `Movies` kontrolera i `Edit` akcji, które opisano w dalszej części tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="83719-125">`Https://localhost:5001/Movies/Edit/5`: is a request to edit the movie with ID=5 using the `Movies` controller and the `Edit` action, which are detailed later in the tutorial.</span></span>

<span data-ttu-id="83719-126">Dane trasy są wyjaśnione w dalszej części samouczka.</span><span class="sxs-lookup"><span data-stu-id="83719-126">Route data is explained later in the tutorial.</span></span>

<span data-ttu-id="83719-127">wzorzec architektury MVC oddziela aplikację do trzech głównych grup składników: modeli, widoków i kontrolerów.</span><span class="sxs-lookup"><span data-stu-id="83719-127">he MVC architectural pattern separates an app into three main groups of components: Models, Views, and Controllers.</span></span> <span data-ttu-id="83719-128">Ten wzorzec pozwala uzyskać rozdzielenie obaw: Logika interfejsu użytkownika należy do widoku.</span><span class="sxs-lookup"><span data-stu-id="83719-128">This pattern helps to achieve separation of concerns: The UI logic belongs in the view.</span></span> <span data-ttu-id="83719-129">Logika wejściowa należy do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="83719-129">Input logic belongs in the controller.</span></span> <span data-ttu-id="83719-130">Logika biznesowa należy do modelu.</span><span class="sxs-lookup"><span data-stu-id="83719-130">Business logic belongs in the model.</span></span> <span data-ttu-id="83719-131">Ta separacja ułatwia zarządzanie złożonością podczas kompilowania aplikacji, ponieważ umożliwia pracę w jednym aspekcie implementacji jednocześnie bez wpływu na kod innego.</span><span class="sxs-lookup"><span data-stu-id="83719-131">This separation helps manage complexity when building an app, because it enables work on one aspect of the implementation at a time without impacting the code of another.</span></span> <span data-ttu-id="83719-132">Na przykład można korzystać z kodu widoku bez w zależności od kodu logiki biznesowej.</span><span class="sxs-lookup"><span data-stu-id="83719-132">For example, you can work on the view code without depending on the business logic code.</span></span>

<span data-ttu-id="83719-133">Te koncepcje są wprowadzane i przedstawiane w tej serii samouczków podczas kompilowania aplikacji filmowej.</span><span class="sxs-lookup"><span data-stu-id="83719-133">These concepts are introduced and demonstrated in this tutorial series while building a movie app.</span></span> <span data-ttu-id="83719-134">Projekt MVC zawiera foldery dla *kontrolerów* i *widoków*.</span><span class="sxs-lookup"><span data-stu-id="83719-134">The MVC project contains folders for the *Controllers* and *Views*.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="83719-135">Dodawanie kontrolera</span><span class="sxs-lookup"><span data-stu-id="83719-135">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="83719-136">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="83719-136">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="83719-137">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy pozycję **Kontrolery > Dodaj kontroler >**.</span><span class="sxs-lookup"><span data-stu-id="83719-137">In the **Solution Explorer**, right-click **Controllers > Add > Controller**.</span></span>

![Eksplorator rozwiązań, kliknij prawym przyciskiem myszy kontrolery > Dodaj kontroler >](~/tutorials/first-mvc-app/adding-controller/_static/add_controllercopyVS19v16.9.png)

<span data-ttu-id="83719-139">W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **kontroler MVC — pusty**.</span><span class="sxs-lookup"><span data-stu-id="83719-139">In the **Add Scaffold** dialog box, select **MVC Controller - Empty**.</span></span>

![Dodaj kontroler MVC i nadaj mu nazwę](~/tutorials/first-mvc-app/adding-controller/_static/acCopyVS19v16.9.png)

<span data-ttu-id="83719-141">W **oknie dialogowym Dodaj nowy element — MvcMovie** wprowadź **HelloWorldController.cs** i wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="83719-141">In the **Add New Item - MvcMovie dialog**, enter **HelloWorldController.cs** and select **Add**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="83719-142">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="83719-142">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="83719-143">Wybierz ikonę **Eksploratora** , a następnie kliknij przycisk Control (kliknij prawym przyciskiem myszy) **Kontrolery > nowy plik** i Nadaj nowemu plikowi nazwę *HelloWorldController.cs*.</span><span class="sxs-lookup"><span data-stu-id="83719-143">Select the **EXPLORER** icon and then control-click (right-click) **Controllers > New File** and name the new file *HelloWorldController.cs*.</span></span>

![Menu kontekstowe](~/tutorials/first-mvc-app-xplat/adding-controller/_static/new_fileVSC1.51.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="83719-145">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="83719-145">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="83719-146">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy pozycję **kontrolery > Dodaj > nowy plik**.</span><span class="sxs-lookup"><span data-stu-id="83719-146">In **Solution Explorer**, right-click **Controllers > Add > New File**.</span></span>

![Menu kontekstowe](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)

<span data-ttu-id="83719-148">Wybierz **ASP.NET Core** i **klasy kontrolera**.</span><span class="sxs-lookup"><span data-stu-id="83719-148">Select **ASP.NET Core** and **Controller Class**.</span></span>

<span data-ttu-id="83719-149">Nadaj nazwę kontrolerowi **HelloWorldController**.</span><span class="sxs-lookup"><span data-stu-id="83719-149">Name the controller **HelloWorldController**.</span></span>

![Dodaj kontroler MVC i nadaj mu nazwę](~/tutorials/first-mvc-app-mac/adding-controller/_static/ac.png)

---

<span data-ttu-id="83719-151">Zastąp zawartość *controllers/HelloWorldController. cs* następującymi kwestiami:</span><span class="sxs-lookup"><span data-stu-id="83719-151">Replace the contents of *Controllers/HelloWorldController.cs* with the following:</span></span>

  [!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

<span data-ttu-id="83719-152">Każda `public` Metoda w kontrolerze jest wywoływana jako punkt końcowy HTTP.</span><span class="sxs-lookup"><span data-stu-id="83719-152">Every `public` method in a controller is callable as an HTTP endpoint.</span></span> <span data-ttu-id="83719-153">W powyższym przykładzie obie metody zwracają ciąg.</span><span class="sxs-lookup"><span data-stu-id="83719-153">In the sample above, both methods return a string.</span></span> <span data-ttu-id="83719-154">Zwróć uwagę na komentarze poprzedzające każdą metodę.</span><span class="sxs-lookup"><span data-stu-id="83719-154">Note the comments preceding each method.</span></span>

<span data-ttu-id="83719-155">Punkt końcowy HTTP:</span><span class="sxs-lookup"><span data-stu-id="83719-155">An HTTP endpoint:</span></span>

* <span data-ttu-id="83719-156">Jest docelowym adresem URL w aplikacji sieci Web, na przykład `https://localhost:5001/HelloWorld` .</span><span class="sxs-lookup"><span data-stu-id="83719-156">Is a targetable URL in the web application, such as `https://localhost:5001/HelloWorld`.</span></span>
* <span data-ttu-id="83719-157">Wiąż</span><span class="sxs-lookup"><span data-stu-id="83719-157">Combines:</span></span>
  * <span data-ttu-id="83719-158">Używany protokół: `HTTPS` .</span><span class="sxs-lookup"><span data-stu-id="83719-158">The protocol used: `HTTPS`.</span></span>
  * <span data-ttu-id="83719-159">Lokalizacja sieciowa serwera sieci Web, w tym port TCP: `localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="83719-159">The network location of the web server, including the TCP port: `localhost:5001`.</span></span>
  * <span data-ttu-id="83719-160">Docelowy identyfikator URI: `HelloWorld` .</span><span class="sxs-lookup"><span data-stu-id="83719-160">The target URI: `HelloWorld`.</span></span>

<span data-ttu-id="83719-161">Pierwszy komentarz wskazuje, że jest to metoda [http Get](https://developer.mozilla.org/docs/Web/HTTP/Methods/GET) , która jest wywoływana przez dołączenie `/HelloWorld/` do podstawowego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="83719-161">The first comment states this is an [HTTP GET](https://developer.mozilla.org/docs/Web/HTTP/Methods/GET) method that's invoked by appending `/HelloWorld/` to the base URL.</span></span>

<span data-ttu-id="83719-162">Drugi komentarz określa metodę [http Get](https://developer.mozilla.org/docs/Web/HTTP/Methods) , która jest wywoływana przez dołączenie `/HelloWorld/Welcome/` do adresu URL.</span><span class="sxs-lookup"><span data-stu-id="83719-162">The second comment specifies an [HTTP GET](https://developer.mozilla.org/docs/Web/HTTP/Methods) method that's invoked by appending `/HelloWorld/Welcome/` to the URL.</span></span> <span data-ttu-id="83719-163">W dalszej części tego samouczka aparat szkieletu służy do generowania `HTTP POST` metod, które aktualizują dane.</span><span class="sxs-lookup"><span data-stu-id="83719-163">Later on in the tutorial, the scaffolding engine is used to generate `HTTP POST` methods, which update data.</span></span>

<span data-ttu-id="83719-164">Uruchom aplikację bez debugera.</span><span class="sxs-lookup"><span data-stu-id="83719-164">Run the app without the debugger.</span></span>

<span data-ttu-id="83719-165">Dołącz "HelloWorld" do ścieżki na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="83719-165">Append "HelloWorld" to the path in the address bar.</span></span> <span data-ttu-id="83719-166">`Index`Metoda zwraca ciąg.</span><span class="sxs-lookup"><span data-stu-id="83719-166">The `Index` method returns a string.</span></span>

![Okno przeglądarki pokazujące odpowiedź aplikacji to moja domyślna akcja](~/tutorials/first-mvc-app/adding-controller/_static/hell1.png)

<span data-ttu-id="83719-168">MVC wywołuje klasy kontrolera i metody akcji w nich, w zależności od przychodzącego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="83719-168">MVC invokes controller classes, and the action methods within them, depending on the incoming URL.</span></span> <span data-ttu-id="83719-169">Domyślna [logika routingu adresów URL](xref:mvc/controllers/routing) używana przez MVC korzysta z formatu, który jest wywoływany przez kod:</span><span class="sxs-lookup"><span data-stu-id="83719-169">The default [URL routing logic](xref:mvc/controllers/routing) used by MVC, uses a format like this to determine what code to invoke:</span></span>

`/[Controller]/[ActionName]/[Parameters]`

<span data-ttu-id="83719-170">Format routingu jest ustawiany w `Configure` metodzie w pliku *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="83719-170">The routing format is set in the `Configure` method in *Startup.cs* file.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

<span data-ttu-id="83719-171">Gdy przejdziesz do aplikacji i nie podasz żadnych segmentów adresu URL, domyślnym kontrolerem "Home" i metodą "index" określoną w wierszu szablonu wyróżnionym powyżej.</span><span class="sxs-lookup"><span data-stu-id="83719-171">When you browse to the app and don't supply any URL segments, it defaults to the "Home" controller and the "Index" method specified in the template line highlighted above.</span></span>  <span data-ttu-id="83719-172">W poprzednich segmentach adresów URL:</span><span class="sxs-lookup"><span data-stu-id="83719-172">In the preceding URL segments:</span></span>

* <span data-ttu-id="83719-173">Pierwszy segment adresu URL określa klasę kontrolera do uruchomienia.</span><span class="sxs-lookup"><span data-stu-id="83719-173">The first URL segment determines the controller class to run.</span></span> <span data-ttu-id="83719-174">Dlatego `localhost:5001/HelloWorld` mapuje do klasy kontrolera **HelloWorld**.</span><span class="sxs-lookup"><span data-stu-id="83719-174">So `localhost:5001/HelloWorld` maps to the **HelloWorld** Controller class.</span></span>
* <span data-ttu-id="83719-175">Druga część segmentu adresu URL określa metodę akcji klasy.</span><span class="sxs-lookup"><span data-stu-id="83719-175">The second part of the URL segment determines the action method on the class.</span></span> <span data-ttu-id="83719-176">Powoduje to, że `localhost:5001/HelloWorld/Index` `Index` Metoda `HelloWorldController` klasy zostanie uruchomiona.</span><span class="sxs-lookup"><span data-stu-id="83719-176">So `localhost:5001/HelloWorld/Index` causes the `Index` method of the `HelloWorldController` class to run.</span></span> <span data-ttu-id="83719-177">Zwróć uwagę, że trzeba tylko przeglądać do `localhost:5001/HelloWorld` i `Index` Metoda została wywołana domyślnie.</span><span class="sxs-lookup"><span data-stu-id="83719-177">Notice that you only had to browse to `localhost:5001/HelloWorld` and the `Index` method was called by default.</span></span> <span data-ttu-id="83719-178">`Index` jest domyślną metodą, która będzie wywoływana na kontrolerze, jeśli nazwa metody nie jest jawnie określona.</span><span class="sxs-lookup"><span data-stu-id="83719-178">`Index` is the default method that will be called on a controller if a method name isn't explicitly specified.</span></span>
* <span data-ttu-id="83719-179">Trzecia część segmentu URL ( `id` ) jest dla danych trasy.</span><span class="sxs-lookup"><span data-stu-id="83719-179">The third part of the URL segment ( `id`) is for route data.</span></span> <span data-ttu-id="83719-180">Dane trasy są wyjaśnione w dalszej części samouczka.</span><span class="sxs-lookup"><span data-stu-id="83719-180">Route data is explained later in the tutorial.</span></span>

<span data-ttu-id="83719-181">Przejdź do: `https://localhost:{PORT}/HelloWorld/Welcome` .</span><span class="sxs-lookup"><span data-stu-id="83719-181">Browse to: `https://localhost:{PORT}/HelloWorld/Welcome`.</span></span> <span data-ttu-id="83719-182">Zamień `{PORT}` na numer portu.</span><span class="sxs-lookup"><span data-stu-id="83719-182">Replace `{PORT}` with your port number.</span></span>

<span data-ttu-id="83719-183">`Welcome`Metoda jest uruchamiana i zwraca ciąg `This is the Welcome action method...` .</span><span class="sxs-lookup"><span data-stu-id="83719-183">The `Welcome` method runs and returns the string `This is the Welcome action method...`.</span></span> <span data-ttu-id="83719-184">Dla tego adresu URL kontroler jest `HelloWorld` i `Welcome` jest metodą akcji.</span><span class="sxs-lookup"><span data-stu-id="83719-184">For this URL, the controller is `HelloWorld` and `Welcome` is the action method.</span></span> <span data-ttu-id="83719-185">Nie użyto `[Parameters]` jeszcze części adresu URL.</span><span class="sxs-lookup"><span data-stu-id="83719-185">You haven't used the `[Parameters]` part of the URL yet.</span></span>

![Okno przeglądarki pokazujące odpowiedź aplikacji jest to metoda akcji powitalnej](~/tutorials/first-mvc-app/adding-controller/_static/welcome.png)

<span data-ttu-id="83719-187">Zmodyfikuj kod, aby przekazać do kontrolera informacje o parametrach z adresu URL.</span><span class="sxs-lookup"><span data-stu-id="83719-187">Modify the code to pass some parameter information from the URL to the controller.</span></span> <span data-ttu-id="83719-188">Na przykład `/HelloWorld/Welcome?name=Rick&numtimes=4`.</span><span class="sxs-lookup"><span data-stu-id="83719-188">For example, `/HelloWorld/Welcome?name=Rick&numtimes=4`.</span></span>

<span data-ttu-id="83719-189">Zmień `Welcome` metodę, tak aby obejmowała dwa parametry, jak pokazano w poniższym kodzie.</span><span class="sxs-lookup"><span data-stu-id="83719-189">Change the `Welcome` method to include two parameters as shown in the following code.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

<span data-ttu-id="83719-190">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="83719-190">The preceding code:</span></span>

* <span data-ttu-id="83719-191">Używa funkcji opcjonalnego parametru języka C#, aby wskazać, że `numTimes` parametr domyślnie przyjmuje wartość 1, jeśli dla tego parametru nie jest przenoszona żadna wartooć.</span><span class="sxs-lookup"><span data-stu-id="83719-191">Uses the C# optional-parameter feature to indicate that the `numTimes` parameter defaults to 1 if no value is passed for that parameter.</span></span>
* <span data-ttu-id="83719-192">Program używa programu `HtmlEncoder.Default.Encode` do ochrony aplikacji przed złośliwymi danymi wejściowymi, na przykład za pośrednictwem języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="83719-192">Uses `HtmlEncoder.Default.Encode` to protect the app from malicious input, such as through JavaScript.</span></span>
* <span data-ttu-id="83719-193">Używa [interpolowanych ciągów](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) w `$"Hello {name}, NumTimes is: {numTimes}"` .</span><span class="sxs-lookup"><span data-stu-id="83719-193">Uses [Interpolated Strings](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) in `$"Hello {name}, NumTimes is: {numTimes}"`.</span></span>

<span data-ttu-id="83719-194">Uruchom aplikację i przejdź do: `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4` .</span><span class="sxs-lookup"><span data-stu-id="83719-194">Run the app and browse to: `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`.</span></span> <span data-ttu-id="83719-195">Zamień `{PORT}` na numer portu.</span><span class="sxs-lookup"><span data-stu-id="83719-195">Replace `{PORT}` with your port number.</span></span>

<span data-ttu-id="83719-196">Spróbuj użyć różnych wartości dla `name` i `numtimes` w adresie URL.</span><span class="sxs-lookup"><span data-stu-id="83719-196">Try different values for `name` and `numtimes` in the URL.</span></span> <span data-ttu-id="83719-197">System [powiązania modelu](xref:mvc/models/model-binding) MVC automatycznie mapuje nazwane parametry z ciągu zapytania na parametry w metodzie.</span><span class="sxs-lookup"><span data-stu-id="83719-197">The MVC [model binding](xref:mvc/models/model-binding) system automatically maps the named parameters from the query string to parameters in the method.</span></span> <span data-ttu-id="83719-198">Aby uzyskać więcej informacji, zobacz [powiązanie modelu](xref:mvc/models/model-binding) .</span><span class="sxs-lookup"><span data-stu-id="83719-198">See [Model Binding](xref:mvc/models/model-binding) for more information.</span></span>

![Okno przeglądarki pokazujące odpowiedź aplikacji Hello Rick, NumTimes jest \: 4](~/tutorials/first-mvc-app/adding-controller/_static/rick4.png)

<span data-ttu-id="83719-200">Na poprzedniej ilustracji:</span><span class="sxs-lookup"><span data-stu-id="83719-200">In the previous image:</span></span>

* <span data-ttu-id="83719-201">Segment adresu URL `Parameters` nie jest używany.</span><span class="sxs-lookup"><span data-stu-id="83719-201">The URL segment `Parameters` isn't used.</span></span>
* <span data-ttu-id="83719-202">`name`Parametry i `numTimes` są przesyłane w [ciągu zapytania](https://wikipedia.org/wiki/Query_string).</span><span class="sxs-lookup"><span data-stu-id="83719-202">The `name` and `numTimes` parameters are passed in the [query string](https://wikipedia.org/wiki/Query_string).</span></span>
* <span data-ttu-id="83719-203">`?`(Znak zapytania) w powyższym adresie URL jest separatorem, a ciąg zapytania znajduje się poniżej.</span><span class="sxs-lookup"><span data-stu-id="83719-203">The `?` (question mark) in the above URL is a separator, and the query string follows.</span></span>
* <span data-ttu-id="83719-204">`&`Znak oddziela pary pól i wartości.</span><span class="sxs-lookup"><span data-stu-id="83719-204">The `&` character separates field-value pairs.</span></span>

<span data-ttu-id="83719-205">Zastąp metodę `Welcome` poniższym kodem:</span><span class="sxs-lookup"><span data-stu-id="83719-205">Replace the `Welcome` method with the following code:</span></span>

  [!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

<span data-ttu-id="83719-206">Uruchom aplikację i wprowadź następujący adres URL: `https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`</span><span class="sxs-lookup"><span data-stu-id="83719-206">Run the app and enter the following URL: `https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`</span></span>

<span data-ttu-id="83719-207">W powyższym adresie URL:</span><span class="sxs-lookup"><span data-stu-id="83719-207">In the preceding URL:</span></span>

* <span data-ttu-id="83719-208">Trzeci segment adresu URL pasuje do parametru Route `id` .</span><span class="sxs-lookup"><span data-stu-id="83719-208">The third URL segment matched the route parameter `id`.</span></span> 
* <span data-ttu-id="83719-209">`Welcome`Metoda zawiera parametr `id` , który jest zgodny z szablonem adresu URL w `MapControllerRoute` metodzie.</span><span class="sxs-lookup"><span data-stu-id="83719-209">The `Welcome` method contains a parameter `id` that matched the URL template in the `MapControllerRoute` method.</span></span>
* <span data-ttu-id="83719-210">Końcowe `?` (w `id?` ) wskazuje, że `id` parametr jest opcjonalny.</span><span class="sxs-lookup"><span data-stu-id="83719-210">The trailing `?` (in `id?`) indicates the `id` parameter is optional.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie5/Startup.cs?name=snippet_route&highlight=5)]

<span data-ttu-id="83719-211">W powyższym przykładzie:</span><span class="sxs-lookup"><span data-stu-id="83719-211">In the preceding example:</span></span>

* <span data-ttu-id="83719-212">Trzeci segment adresu URL pasuje do parametru Route `id` .</span><span class="sxs-lookup"><span data-stu-id="83719-212">The third URL segment matched the route parameter `id`.</span></span>
* <span data-ttu-id="83719-213">`Welcome`Metoda zawiera parametr `id` , który jest zgodny z szablonem adresu URL w `MapControllerRoute` metodzie.</span><span class="sxs-lookup"><span data-stu-id="83719-213">The `Welcome` method contains a parameter `id` that matched the URL template in the `MapControllerRoute` method.</span></span>
* <span data-ttu-id="83719-214">Końcowe `?` (w `id?` ) wskazuje, że `id` parametr jest opcjonalny.</span><span class="sxs-lookup"><span data-stu-id="83719-214">The trailing `?` (in `id?`) indicates the `id` parameter is optional.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="83719-215">[Poprzedni:](start-mvc.md) 
>  wprowadzenie [Dalej: Dodawanie widoku](adding-view.md)</span><span class="sxs-lookup"><span data-stu-id="83719-215">[Previous: Get Started](start-mvc.md)
[Next: Add a View](adding-view.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="83719-216">Wzorzec architektoniczny Model-View-Controller (MVC) oddziela aplikację do trzech głównych składników: **M** odelu, **V** każ i **C** ontroller.</span><span class="sxs-lookup"><span data-stu-id="83719-216">The Model-View-Controller (MVC) architectural pattern separates an app into three main components: **M** odel, **V** iew, and **C** ontroller.</span></span> <span data-ttu-id="83719-217">Wzorzec MVC pomaga tworzyć aplikacje, które są bardziej weryfikowalne i łatwiej aktualizować niż tradycyjne aplikacje monolityczne.</span><span class="sxs-lookup"><span data-stu-id="83719-217">The MVC pattern helps you create apps that are more testable and easier to update than traditional monolithic apps.</span></span> <span data-ttu-id="83719-218">Aplikacje oparte na MVC zawierają:</span><span class="sxs-lookup"><span data-stu-id="83719-218">MVC-based apps contain:</span></span>

* <span data-ttu-id="83719-219">**M** Odels: klasy reprezentujące dane aplikacji.</span><span class="sxs-lookup"><span data-stu-id="83719-219">**M** odels: Classes that represent the data of the app.</span></span> <span data-ttu-id="83719-220">Klasy modeli używają logiki walidacji, aby wymusić reguły biznesowe dla tych danych.</span><span class="sxs-lookup"><span data-stu-id="83719-220">The model classes use validation logic to enforce business rules for that data.</span></span> <span data-ttu-id="83719-221">Zazwyczaj obiekty modelu pobierają i przechowują stan modelu w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="83719-221">Typically, model objects retrieve and store model state in a database.</span></span> <span data-ttu-id="83719-222">W tym samouczku `Movie` model pobiera dane filmu z bazy danych, udostępnia je widokowi lub aktualizuje.</span><span class="sxs-lookup"><span data-stu-id="83719-222">In this tutorial, a `Movie` model retrieves movie data from a database, provides it to the view or updates it.</span></span> <span data-ttu-id="83719-223">Zaktualizowane dane są zapisywane w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="83719-223">Updated data is written to a database.</span></span>

* <span data-ttu-id="83719-224">**V** Iews: widoki są składnikami, które wyświetlają interfejs użytkownika aplikacji.</span><span class="sxs-lookup"><span data-stu-id="83719-224">**V** iews: Views are the components that display the app's user interface (UI).</span></span> <span data-ttu-id="83719-225">Zazwyczaj ten interfejs użytkownika wyświetla dane modelu.</span><span class="sxs-lookup"><span data-stu-id="83719-225">Generally, this UI displays the model data.</span></span>

* <span data-ttu-id="83719-226">**C** Ontrollers: klasy, które obsługują żądania przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="83719-226">**C** ontrollers: Classes that handle browser requests.</span></span> <span data-ttu-id="83719-227">Pobierają one dane modelu i szablony widoków wywołań, które zwracają odpowiedź.</span><span class="sxs-lookup"><span data-stu-id="83719-227">They retrieve model data and call view templates that return a response.</span></span> <span data-ttu-id="83719-228">W aplikacji MVC widok wyświetla tylko informacje; kontroler obsługuje i reaguje na dane wejściowe użytkownika i interakcje.</span><span class="sxs-lookup"><span data-stu-id="83719-228">In an MVC app, the view only displays information; the controller handles and responds to user input and interaction.</span></span> <span data-ttu-id="83719-229">Na przykład kontroler obsługuje dane tras i wartości ciągu zapytania i przekazuje te wartości do modelu.</span><span class="sxs-lookup"><span data-stu-id="83719-229">For example, the controller handles route data and query-string values, and passes these values to the model.</span></span> <span data-ttu-id="83719-230">Model może używać tych wartości do wykonywania zapytań w bazie danych.</span><span class="sxs-lookup"><span data-stu-id="83719-230">The model might use these values to query the database.</span></span> <span data-ttu-id="83719-231">Na przykład `https://localhost:5001/Home/About` ma dane trasy `Home` (kontroler) i `About` (Metoda akcji do wywołania na kontrolerze głównym).</span><span class="sxs-lookup"><span data-stu-id="83719-231">For example, `https://localhost:5001/Home/About` has route data of `Home` (the controller) and `About` (the action method to call on the home controller).</span></span> <span data-ttu-id="83719-232">`https://localhost:5001/Movies/Edit/5` jest żądaniem edycji filmu o IDENTYFIKATORze 5 przy użyciu kontrolera filmu.</span><span class="sxs-lookup"><span data-stu-id="83719-232">`https://localhost:5001/Movies/Edit/5` is a request to edit the movie with ID=5 using the movie controller.</span></span> <span data-ttu-id="83719-233">Dane trasy są wyjaśnione w dalszej części samouczka.</span><span class="sxs-lookup"><span data-stu-id="83719-233">Route data is explained later in the tutorial.</span></span>

<span data-ttu-id="83719-234">Wzorzec MVC ułatwia tworzenie aplikacji, które oddzielają różne aspekty aplikacji (logiki wejściowej, logiki biznesowej i logiki interfejsu użytkownika), jednocześnie zapewniając swobodny sprzężenie między tymi elementami.</span><span class="sxs-lookup"><span data-stu-id="83719-234">The MVC pattern helps you create apps that separate the different aspects of the app (input logic, business logic, and UI logic), while providing a loose coupling between these elements.</span></span> <span data-ttu-id="83719-235">Wzorzec określa, gdzie poszczególne rodzaje logiki powinny znajdować się w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="83719-235">The pattern specifies where each kind of logic should be located in the app.</span></span> <span data-ttu-id="83719-236">Logika interfejsu użytkownika należy do widoku.</span><span class="sxs-lookup"><span data-stu-id="83719-236">The UI logic belongs in the view.</span></span> <span data-ttu-id="83719-237">Logika wejściowa należy do kontrolera.</span><span class="sxs-lookup"><span data-stu-id="83719-237">Input logic belongs in the controller.</span></span> <span data-ttu-id="83719-238">Logika biznesowa należy do modelu.</span><span class="sxs-lookup"><span data-stu-id="83719-238">Business logic belongs in the model.</span></span> <span data-ttu-id="83719-239">Ta separacja ułatwia zarządzanie złożonością podczas kompilowania aplikacji, ponieważ umożliwia pracę nad jednym aspektem implementacji jednocześnie bez wpływu na kod innego.</span><span class="sxs-lookup"><span data-stu-id="83719-239">This separation helps you manage complexity when you build an app, because it enables you to work on one aspect of the implementation at a time without impacting the code of another.</span></span> <span data-ttu-id="83719-240">Na przykład można korzystać z kodu widoku bez w zależności od kodu logiki biznesowej.</span><span class="sxs-lookup"><span data-stu-id="83719-240">For example, you can work on the view code without depending on the business logic code.</span></span>

<span data-ttu-id="83719-241">Omawiamy te koncepcje w tej serii samouczków i pokazano, jak używać ich do kompilowania aplikacji filmowej.</span><span class="sxs-lookup"><span data-stu-id="83719-241">We cover these concepts in this tutorial series and show you how to use them to build a movie app.</span></span> <span data-ttu-id="83719-242">Projekt MVC zawiera foldery dla *kontrolerów* i *widoków*.</span><span class="sxs-lookup"><span data-stu-id="83719-242">The MVC project contains folders for the *Controllers* and *Views*.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="83719-243">Dodawanie kontrolera</span><span class="sxs-lookup"><span data-stu-id="83719-243">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="83719-244">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="83719-244">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="83719-245">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy pozycję **Kontrolery > Dodaj kontroler >**</span><span class="sxs-lookup"><span data-stu-id="83719-245">In **Solution Explorer**, right-click **Controllers > Add > Controller**</span></span>

  ![Menu kontekstowe](~/tutorials/first-mvc-app/adding-controller/_static/add_controller.png)

* <span data-ttu-id="83719-247">W oknie dialogowym **Dodawanie szkieletu** wybierz pozycję **kontroler MVC — pusty**</span><span class="sxs-lookup"><span data-stu-id="83719-247">In the **Add Scaffold** dialog box, select **MVC Controller - Empty**</span></span>

  ![Dodaj kontroler MVC i nadaj mu nazwę](~/tutorials/first-mvc-app/adding-controller/_static/ac.png)

* <span data-ttu-id="83719-249">W **oknie dialogowym Dodaj pusty kontroler MVC** wpisz **HelloWorldController** i wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="83719-249">In the **Add Empty MVC Controller dialog**, enter **HelloWorldController** and select **ADD**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="83719-250">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="83719-250">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="83719-251">Wybierz ikonę **Eksploratora** , a następnie kliknij przycisk Control (kliknij prawym przyciskiem myszy) **Kontrolery > nowy plik** i Nadaj nowemu plikowi nazwę *HelloWorldController.cs*.</span><span class="sxs-lookup"><span data-stu-id="83719-251">Select the **EXPLORER** icon and then control-click (right-click) **Controllers > New File** and name the new file *HelloWorldController.cs*.</span></span>

  ![Menu kontekstowe](~/tutorials/first-mvc-app-xplat/adding-controller/_static/new_file.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="83719-253">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="83719-253">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="83719-254">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy pozycję **kontrolery > Dodaj > nowy plik**.</span><span class="sxs-lookup"><span data-stu-id="83719-254">In **Solution Explorer**, right-click **Controllers > Add > New File**.</span></span>

![Menu kontekstowe](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)

<span data-ttu-id="83719-256">Wybierz klasę **ASP.NET Core** i **kontrolera MVC**.</span><span class="sxs-lookup"><span data-stu-id="83719-256">Select **ASP.NET Core** and **MVC Controller Class**.</span></span>

<span data-ttu-id="83719-257">Nadaj nazwę kontrolerowi **HelloWorldController**.</span><span class="sxs-lookup"><span data-stu-id="83719-257">Name the controller **HelloWorldController**.</span></span>

![Dodaj kontroler MVC i nadaj mu nazwę](~/tutorials/first-mvc-app-mac/adding-controller/_static/ac.png)

---

<span data-ttu-id="83719-259">Zastąp zawartość *controllers/HelloWorldController. cs* następującymi kwestiami:</span><span class="sxs-lookup"><span data-stu-id="83719-259">Replace the contents of *Controllers/HelloWorldController.cs* with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

<span data-ttu-id="83719-260">Każda `public` Metoda w kontrolerze jest wywoływana jako punkt końcowy HTTP.</span><span class="sxs-lookup"><span data-stu-id="83719-260">Every `public` method in a controller is callable as an HTTP endpoint.</span></span> <span data-ttu-id="83719-261">W powyższym przykładzie obie metody zwracają ciąg.</span><span class="sxs-lookup"><span data-stu-id="83719-261">In the sample above, both methods return a string.</span></span> <span data-ttu-id="83719-262">Zwróć uwagę na komentarze poprzedzające każdą metodę.</span><span class="sxs-lookup"><span data-stu-id="83719-262">Note the comments preceding each method.</span></span>

<span data-ttu-id="83719-263">Punkt końcowy HTTP to docelowy adres URL w aplikacji sieci Web, na przykład `https://localhost:5001/HelloWorld` i łączy używany protokół: `HTTPS` , lokalizację sieciową serwera sieci Web (w tym port TCP): `localhost:5001` i docelowy identyfikator URI `HelloWorld` .</span><span class="sxs-lookup"><span data-stu-id="83719-263">An HTTP endpoint is a targetable URL in the web application, such as `https://localhost:5001/HelloWorld`, and combines the protocol used: `HTTPS`, the network location of the web server (including the TCP port): `localhost:5001` and the target URI `HelloWorld`.</span></span>

<span data-ttu-id="83719-264">Pierwszy komentarz wskazuje, że jest to metoda [http Get](https://www.w3schools.com/tags/ref_httpmethods.asp) , która jest wywoływana przez dołączenie `/HelloWorld/` do podstawowego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="83719-264">The first comment states this is an [HTTP GET](https://www.w3schools.com/tags/ref_httpmethods.asp) method that's invoked by appending `/HelloWorld/` to the base URL.</span></span> <span data-ttu-id="83719-265">Drugi komentarz określa metodę [http Get](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) , która jest wywoływana przez dołączenie `/HelloWorld/Welcome/` do adresu URL.</span><span class="sxs-lookup"><span data-stu-id="83719-265">The second comment specifies an [HTTP GET](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) method that's invoked by appending `/HelloWorld/Welcome/` to the URL.</span></span> <span data-ttu-id="83719-266">W dalszej części tego samouczka aparat szkieletu służy do generowania `HTTP POST` metod, które aktualizują dane.</span><span class="sxs-lookup"><span data-stu-id="83719-266">Later on in the tutorial the scaffolding engine is used to generate `HTTP POST` methods which update data.</span></span>

<span data-ttu-id="83719-267">Uruchom aplikację w trybie innym niż debugowanie i Dołącz do niej plik "HelloWorld" do ścieżki na pasku adresu.</span><span class="sxs-lookup"><span data-stu-id="83719-267">Run the app in non-debug mode and append "HelloWorld" to the path in the address bar.</span></span> <span data-ttu-id="83719-268">`Index`Metoda zwraca ciąg.</span><span class="sxs-lookup"><span data-stu-id="83719-268">The `Index` method returns a string.</span></span>

![Okno przeglądarki pokazujące odpowiedź aplikacji to moja domyślna akcja](~/tutorials/first-mvc-app/adding-controller/_static/hell1.png)

<span data-ttu-id="83719-270">MVC wywołuje klasy kontrolera (i w nich metody akcji) w zależności od przychodzącego adresu URL.</span><span class="sxs-lookup"><span data-stu-id="83719-270">MVC invokes controller classes (and the action methods within them) depending on the incoming URL.</span></span> <span data-ttu-id="83719-271">Domyślna [logika routingu adresów URL](xref:mvc/controllers/routing) używana przez MVC korzysta z formatu takiego jak ten, aby określić kod, który ma zostać wywołany:</span><span class="sxs-lookup"><span data-stu-id="83719-271">The default [URL routing logic](xref:mvc/controllers/routing) used by MVC uses a format like this to determine what code to invoke:</span></span>

`/[Controller]/[ActionName]/[Parameters]`

<span data-ttu-id="83719-272">Format routingu jest ustawiany w `Configure` metodzie w pliku *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="83719-272">The routing format is set in the `Configure` method in *Startup.cs* file.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

<!-- 
Add link to explain lambda.
Remove link for simplified tutorial.
-->

<span data-ttu-id="83719-273">Gdy przejdziesz do aplikacji i nie podasz żadnych segmentów adresu URL, domyślnym kontrolerem "Home" i metodą "index" określoną w wierszu szablonu wyróżnionym powyżej.</span><span class="sxs-lookup"><span data-stu-id="83719-273">When you browse to the app and don't supply any URL segments, it defaults to the "Home" controller and the "Index" method specified in the template line highlighted above.</span></span>

<span data-ttu-id="83719-274">Pierwszy segment adresu URL określa klasę kontrolera do uruchomienia.</span><span class="sxs-lookup"><span data-stu-id="83719-274">The first URL segment determines the controller class to run.</span></span> <span data-ttu-id="83719-275">Dlatego `localhost:{PORT}/HelloWorld` mapuje do `HelloWorldController` klasy.</span><span class="sxs-lookup"><span data-stu-id="83719-275">So `localhost:{PORT}/HelloWorld` maps to the `HelloWorldController` class.</span></span> <span data-ttu-id="83719-276">Druga część segmentu adresu URL określa metodę akcji klasy.</span><span class="sxs-lookup"><span data-stu-id="83719-276">The second part of the URL segment determines the action method on the class.</span></span> <span data-ttu-id="83719-277">`localhost:{PORT}/HelloWorld/Index`Mogłoby to spowodować `Index` uruchomienie metody `HelloWorldController` klasy.</span><span class="sxs-lookup"><span data-stu-id="83719-277">So `localhost:{PORT}/HelloWorld/Index` would cause the `Index` method of the `HelloWorldController` class to run.</span></span> <span data-ttu-id="83719-278">Zwróć uwagę, że trzeba tylko przeglądać do `localhost:{PORT}/HelloWorld` i `Index` Metoda została wywołana domyślnie.</span><span class="sxs-lookup"><span data-stu-id="83719-278">Notice that you only had to browse to `localhost:{PORT}/HelloWorld` and the `Index` method was called by default.</span></span> <span data-ttu-id="83719-279">Jest tak dlatego `Index` , że jest to metoda domyślna, która będzie wywoływana na kontrolerze, jeśli nazwa metody nie jest jawnie określona.</span><span class="sxs-lookup"><span data-stu-id="83719-279">This is because `Index` is the default method that will be called on a controller if a method name isn't explicitly specified.</span></span> <span data-ttu-id="83719-280">Trzecia część segmentu URL ( `id` ) jest dla danych trasy.</span><span class="sxs-lookup"><span data-stu-id="83719-280">The third part of the URL segment ( `id`) is for route data.</span></span> <span data-ttu-id="83719-281">Dane trasy są wyjaśnione w dalszej części samouczka.</span><span class="sxs-lookup"><span data-stu-id="83719-281">Route data is explained later in the tutorial.</span></span>

<span data-ttu-id="83719-282">Przejdź na stronę `https://localhost:{PORT}/HelloWorld/Welcome`.</span><span class="sxs-lookup"><span data-stu-id="83719-282">Browse to `https://localhost:{PORT}/HelloWorld/Welcome`.</span></span> <span data-ttu-id="83719-283">`Welcome`Metoda jest uruchamiana i zwraca ciąg `This is the Welcome action method...` .</span><span class="sxs-lookup"><span data-stu-id="83719-283">The `Welcome` method runs and returns the string `This is the Welcome action method...`.</span></span> <span data-ttu-id="83719-284">Dla tego adresu URL kontroler jest `HelloWorld` i `Welcome` jest metodą akcji.</span><span class="sxs-lookup"><span data-stu-id="83719-284">For this URL, the controller is `HelloWorld` and `Welcome` is the action method.</span></span> <span data-ttu-id="83719-285">Nie użyto `[Parameters]` jeszcze części adresu URL.</span><span class="sxs-lookup"><span data-stu-id="83719-285">You haven't used the `[Parameters]` part of the URL yet.</span></span>

![Okno przeglądarki pokazujące odpowiedź aplikacji jest to metoda akcji powitalnej](~/tutorials/first-mvc-app/adding-controller/_static/welcome.png)

<span data-ttu-id="83719-287">Zmodyfikuj kod, aby przekazać do kontrolera informacje o parametrach z adresu URL.</span><span class="sxs-lookup"><span data-stu-id="83719-287">Modify the code to pass some parameter information from the URL to the controller.</span></span> <span data-ttu-id="83719-288">Na przykład `/HelloWorld/Welcome?name=Rick&numtimes=4`.</span><span class="sxs-lookup"><span data-stu-id="83719-288">For example, `/HelloWorld/Welcome?name=Rick&numtimes=4`.</span></span> <span data-ttu-id="83719-289">Zmień `Welcome` metodę, tak aby obejmowała dwa parametry, jak pokazano w poniższym kodzie.</span><span class="sxs-lookup"><span data-stu-id="83719-289">Change the `Welcome` method to include two parameters as shown in the following code.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

<span data-ttu-id="83719-290">Powyższy kod ma następujące działanie:</span><span class="sxs-lookup"><span data-stu-id="83719-290">The preceding code:</span></span>

* <span data-ttu-id="83719-291">Używa funkcji opcjonalnego parametru języka C#, aby wskazać, że `numTimes` parametr domyślnie przyjmuje wartość 1, jeśli dla tego parametru nie jest przenoszona żadna wartooć.</span><span class="sxs-lookup"><span data-stu-id="83719-291">Uses the C# optional-parameter feature to indicate that the `numTimes` parameter defaults to 1 if no value is passed for that parameter.</span></span> <!-- remove for simplified -->
* <span data-ttu-id="83719-292">Program używa programu `HtmlEncoder.Default.Encode` do ochrony aplikacji przed złośliwymi danymi wejściowymi (tj. JavaScript).</span><span class="sxs-lookup"><span data-stu-id="83719-292">Uses `HtmlEncoder.Default.Encode` to protect the app from malicious input (namely JavaScript).</span></span>
* <span data-ttu-id="83719-293">Używa [interpolowanych ciągów](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) w `$"Hello {name}, NumTimes is: {numTimes}"` .</span><span class="sxs-lookup"><span data-stu-id="83719-293">Uses [Interpolated Strings](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) in `$"Hello {name}, NumTimes is: {numTimes}"`.</span></span> <!-- remove for simplified -->

<span data-ttu-id="83719-294">Uruchom aplikację i przejdź do:</span><span class="sxs-lookup"><span data-stu-id="83719-294">Run the app and browse to:</span></span>

   `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="83719-295">(Zamień `{PORT}` na numer portu). Możesz wypróbować różne wartości dla `name` i `numtimes` w adresie URL.</span><span class="sxs-lookup"><span data-stu-id="83719-295">(Replace `{PORT}` with your port number.) You can try different values for `name` and `numtimes` in the URL.</span></span> <span data-ttu-id="83719-296">System [powiązania modelu](xref:mvc/models/model-binding) MVC automatycznie mapuje nazwane parametry z ciągu zapytania na pasku adresu na parametry w metodzie.</span><span class="sxs-lookup"><span data-stu-id="83719-296">The MVC [model binding](xref:mvc/models/model-binding) system automatically maps the named parameters from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="83719-297">Aby uzyskać więcej informacji, zobacz [powiązanie modelu](xref:mvc/models/model-binding) .</span><span class="sxs-lookup"><span data-stu-id="83719-297">See [Model Binding](xref:mvc/models/model-binding) for more information.</span></span>

![Okno przeglądarki pokazujące odpowiedź aplikacji Hello Rick, NumTimes jest \: 4](~/tutorials/first-mvc-app/adding-controller/_static/rick4.png)

<span data-ttu-id="83719-299">Na powyższym obrazie segment adresu URL ( `Parameters` ) nie jest używany, `name` a `numTimes` Parametry i są przesyłane w [ciągu zapytania](https://wikipedia.org/wiki/Query_string).</span><span class="sxs-lookup"><span data-stu-id="83719-299">In the image above, the URL segment (`Parameters`) isn't used, the `name` and `numTimes` parameters are passed in the [query string](https://wikipedia.org/wiki/Query_string).</span></span> <span data-ttu-id="83719-300">`?`(Znak zapytania) w powyższym adresie URL jest separatorem, a ciąg zapytania znajduje się poniżej.</span><span class="sxs-lookup"><span data-stu-id="83719-300">The `?` (question mark) in the above URL is a separator, and the query string follows.</span></span> <span data-ttu-id="83719-301">`&`Znak oddziela pary pól i wartości.</span><span class="sxs-lookup"><span data-stu-id="83719-301">The `&` character separates field-value pairs.</span></span>

<span data-ttu-id="83719-302">Zastąp metodę `Welcome` poniższym kodem:</span><span class="sxs-lookup"><span data-stu-id="83719-302">Replace the `Welcome` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

<span data-ttu-id="83719-303">Uruchom aplikację i wprowadź następujący adres URL: `https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`</span><span class="sxs-lookup"><span data-stu-id="83719-303">Run the app and enter the following URL: `https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`</span></span>

<span data-ttu-id="83719-304">Tym razem trzeci segment adresu URL pasuje do parametru Route `id` .</span><span class="sxs-lookup"><span data-stu-id="83719-304">This time the third URL segment matched the route parameter `id`.</span></span> <span data-ttu-id="83719-305">`Welcome`Metoda zawiera parametr `id` , który jest zgodny z szablonem adresu URL w `MapRoute` metodzie.</span><span class="sxs-lookup"><span data-stu-id="83719-305">The `Welcome` method contains a parameter `id` that matched the URL template in the `MapRoute` method.</span></span> <span data-ttu-id="83719-306">Końcowe `?` (w `id?` ) wskazuje, że `id` parametr jest opcjonalny.</span><span class="sxs-lookup"><span data-stu-id="83719-306">The trailing `?` (in `id?`) indicates the `id` parameter is optional.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

<span data-ttu-id="83719-307">W tych przykładach kontroler wykonywał część "VC" składnika MVC, czyli działania widoku i kontrolera.</span><span class="sxs-lookup"><span data-stu-id="83719-307">In these examples the controller has been doing the "VC" portion of MVC - that is, the view and controller work.</span></span> <span data-ttu-id="83719-308">Kontroler zwraca bezpośrednio kod HTML.</span><span class="sxs-lookup"><span data-stu-id="83719-308">The controller is returning HTML directly.</span></span> <span data-ttu-id="83719-309">Zazwyczaj nie ma potrzeby, aby kontrolery zwracające kod HTML bezpośrednio, ponieważ staną się bardzo nieskomplikowane w kodzie i obsłudze.</span><span class="sxs-lookup"><span data-stu-id="83719-309">Generally you don't want controllers returning HTML directly, since that becomes very cumbersome to code and maintain.</span></span> <span data-ttu-id="83719-310">Zamiast tego zwykle używasz oddzielnego Razor pliku szablonu widoku, aby ułatwić generowanie odpowiedzi html.</span><span class="sxs-lookup"><span data-stu-id="83719-310">Instead you typically use a separate Razor view template file to help generate the HTML response.</span></span> <span data-ttu-id="83719-311">Należy to zrobić w następnym samouczku.</span><span class="sxs-lookup"><span data-stu-id="83719-311">You do that in the next tutorial.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="83719-312">[Poprzedni](start-mvc.md) 
>  [Dalej](adding-view.md)</span><span class="sxs-lookup"><span data-stu-id="83719-312">[Previous](start-mvc.md)
[Next](adding-view.md)</span></span>

::: moniker-end
