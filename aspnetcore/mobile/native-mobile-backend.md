---
title: Tworzenie usług zaplecza dla natywnych aplikacji mobilnych za pomocą ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak utworzyć usługi zaplecza przy użyciu ASP.NET Core MVC do obsługi natywnych aplikacji mobilnych.
ms.author: riande
ms.date: 2/12/2021
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
uid: mobile/native-mobile-backend
ms.openlocfilehash: e496b7811cc534b6f0f6dfdb857f6e462b38049e
ms.sourcegitcommit: f77a7467651bab61b24261da9dc5c1dd75fc1fa9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100564019"
---
# <a name="create-backend-services-for-native-mobile-apps-with-aspnet-core"></a><span data-ttu-id="6af47-103">Tworzenie usług zaplecza dla natywnych aplikacji mobilnych za pomocą ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6af47-103">Create backend services for native mobile apps with ASP.NET Core</span></span>

<span data-ttu-id="6af47-104">Za pomocą [Kuba Montemagno](https://twitter.com/JamesMontemagno)</span><span class="sxs-lookup"><span data-stu-id="6af47-104">By [James Montemagno](https://twitter.com/JamesMontemagno)</span></span>

<span data-ttu-id="6af47-105">Aplikacje mobilne mogą komunikować się z ASP.NET Core usług zaplecza.</span><span class="sxs-lookup"><span data-stu-id="6af47-105">Mobile apps can communicate with ASP.NET Core backend services.</span></span> <span data-ttu-id="6af47-106">Aby uzyskać instrukcje dotyczące łączenia lokalnych usług sieci Web z symulatorami systemu iOS i emulatorami systemu Android, zobacz [nawiązywanie połączenia z lokalnymi usługami sieci Web z symulatorów systemu iOS i emulatorów systemu Android](/xamarin/cross-platform/deploy-test/connect-to-local-web-services).</span><span class="sxs-lookup"><span data-stu-id="6af47-106">For instructions on connecting local web services from iOS simulators and Android emulators, see [Connect to Local Web Services from iOS Simulators and Android Emulators](/xamarin/cross-platform/deploy-test/connect-to-local-web-services).</span></span>

[<span data-ttu-id="6af47-107">Wyświetlanie lub Pobieranie przykładowego kodu usług wewnętrznej bazy danych</span><span class="sxs-lookup"><span data-stu-id="6af47-107">View or download sample backend services code</span></span>](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST)

## <a name="the-sample-native-mobile-app"></a><span data-ttu-id="6af47-108">Przykładowa Natywna aplikacja mobilna</span><span class="sxs-lookup"><span data-stu-id="6af47-108">The Sample Native Mobile App</span></span>

<span data-ttu-id="6af47-109">W tym samouczku przedstawiono sposób tworzenia usług zaplecza przy użyciu ASP.NET Core do obsługi natywnych aplikacji mobilnych.</span><span class="sxs-lookup"><span data-stu-id="6af47-109">This tutorial demonstrates how to create backend services using ASP.NET Core to support native mobile apps.</span></span> <span data-ttu-id="6af47-110">Korzysta ona z [aplikacji Xamarin. Forms TodoRest](/xamarin/xamarin-forms/data-cloud/consuming/rest) jako klienta natywnego, w tym oddzielnych klientów natywnych dla systemów Android, iOS i Windows.</span><span class="sxs-lookup"><span data-stu-id="6af47-110">It uses the [Xamarin.Forms TodoRest app](/xamarin/xamarin-forms/data-cloud/consuming/rest) as its native client, which includes separate native clients for Android, iOS, and Windows.</span></span> <span data-ttu-id="6af47-111">Aby utworzyć aplikację natywną (i zainstalować niezbędne bezpłatne narzędzia Xamarin), możesz skorzystać z połączonego samouczka, a także pobrać przykładowe rozwiązanie Xamarin.</span><span class="sxs-lookup"><span data-stu-id="6af47-111">You can follow the linked tutorial to create the native app (and install the necessary free Xamarin tools), as well as download the Xamarin sample solution.</span></span> <span data-ttu-id="6af47-112">Przykład platformy Xamarin zawiera projekt usług internetowego interfejsu API ASP.NET Core, którego aplikacja ASP.NET Core w tym artykule zastępuje (bez zmian wymaganych przez klienta).</span><span class="sxs-lookup"><span data-stu-id="6af47-112">The Xamarin sample includes an ASP.NET Core Web API services project, which this article's ASP.NET Core app replaces (with no changes required by the client).</span></span>

![Aby aplikacja REST działała na urządzeniu smartphone z systemem Android](native-mobile-backend/_static/todo-android.png)

### <a name="features"></a><span data-ttu-id="6af47-114">Funkcje</span><span class="sxs-lookup"><span data-stu-id="6af47-114">Features</span></span>

<span data-ttu-id="6af47-115">[Aplikacja TodoREST](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST) obsługuje wyświetlanie list, Dodawanie, usuwanie i aktualizowanie elementów To-Do.</span><span class="sxs-lookup"><span data-stu-id="6af47-115">The [TodoREST app](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST) supports listing, adding, deleting, and updating To-Do items.</span></span> <span data-ttu-id="6af47-116">Każdy element ma identyfikator, nazwę, notatki i Właściwość wskazującą, czy została jeszcze ukończona.</span><span class="sxs-lookup"><span data-stu-id="6af47-116">Each item has an ID, a Name, Notes, and a property indicating whether it's been Done yet.</span></span>

<span data-ttu-id="6af47-117">Główny widok elementów, jak pokazano powyżej, zawiera listę nazw poszczególnych elementów i wskazuje, czy jest on wykonany za pomocą znacznika wyboru.</span><span class="sxs-lookup"><span data-stu-id="6af47-117">The main view of the items, as shown above, lists each item's name and indicates if it's done with a checkmark.</span></span>

<span data-ttu-id="6af47-118">Naciśnięcie `+` ikony powoduje otwarcie okna dialogowego Dodawanie elementu:</span><span class="sxs-lookup"><span data-stu-id="6af47-118">Tapping the `+` icon opens an add item dialog:</span></span>

![Okno dialogowe Dodawanie elementu](native-mobile-backend/_static/todo-android-new-item.png)

<span data-ttu-id="6af47-120">Naciśnięcie elementu na głównym ekranie listy otwiera okno dialogowe edytowania, w którym można modyfikować nazwę elementu, notatki i gotowe ustawienia lub można usunąć element:</span><span class="sxs-lookup"><span data-stu-id="6af47-120">Tapping an item on the main list screen opens up an edit dialog where the item's Name, Notes, and Done settings can be modified, or the item can be deleted:</span></span>

![Edytowanie elementu — okno dialogowe](native-mobile-backend/_static/todo-android-edit-item.png)

<span data-ttu-id="6af47-122">Aby przetestować aplikację ASP.NET Core utworzoną w następnej sekcji działającej na komputerze, zaktualizuj [`RestUrl`](https://github.com/xamarin/xamarin-forms-samples/blob/master/WebServices/TodoREST/TodoREST/Constants.cs#L13) stałą aplikacji.</span><span class="sxs-lookup"><span data-stu-id="6af47-122">To test it out yourself against the ASP.NET Core app created in the next section running on your computer, update the app's [`RestUrl`](https://github.com/xamarin/xamarin-forms-samples/blob/master/WebServices/TodoREST/TodoREST/Constants.cs#L13) constant.</span></span>

<span data-ttu-id="6af47-123">Emulatory systemu Android nie są uruchamiane na maszynie lokalnej i używają adresu IP sprzężenia zwrotnego (10.0.2.2) do komunikacji z maszyną lokalną.</span><span class="sxs-lookup"><span data-stu-id="6af47-123">Android emulators do not run on the local machine and use a loopback IP (10.0.2.2) to communicate with the local machine.</span></span> <span data-ttu-id="6af47-124">Aby wykryć, w jaki sposób działa system, aby użyć poprawnego adresu URL, Skorzystaj z platformy [Xamarin. Essentials DeviceInfo](/xamarin/essentials/device-information/) .</span><span class="sxs-lookup"><span data-stu-id="6af47-124">Leverage [Xamarin.Essentials DeviceInfo](/xamarin/essentials/device-information/) to detect what operating the system is running to use the correct URL.</span></span>

<span data-ttu-id="6af47-125">Przejdź do [`TodoREST`](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST/TodoREST) projektu i Otwórz [`Constants.cs`](https://github.com/xamarin/xamarin-forms-samples/blob/master/WebServices/TodoREST/TodoREST/Constants.cs) plik.</span><span class="sxs-lookup"><span data-stu-id="6af47-125">Navigate to the [`TodoREST`](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST/TodoREST) project and open the [`Constants.cs`](https://github.com/xamarin/xamarin-forms-samples/blob/master/WebServices/TodoREST/TodoREST/Constants.cs) file.</span></span> <span data-ttu-id="6af47-126">Plik *Constants.cs* zawiera następującą konfigurację.</span><span class="sxs-lookup"><span data-stu-id="6af47-126">The *Constants.cs* file contains the following configuration.</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoREST/Constants.cs" highlight="13":::

<span data-ttu-id="6af47-127">Opcjonalnie można wdrożyć usługę sieci Web w usłudze w chmurze, takiej jak Azure, i zaktualizować `RestUrl` .</span><span class="sxs-lookup"><span data-stu-id="6af47-127">You can optionally deploy the web service to a cloud service such as Azure and update the `RestUrl`.</span></span>

## <a name="creating-the-aspnet-core-project"></a><span data-ttu-id="6af47-128">Tworzenie projektu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6af47-128">Creating the ASP.NET Core Project</span></span>

<span data-ttu-id="6af47-129">Utwórz nową aplikację sieci Web ASP.NET Core w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="6af47-129">Create a new ASP.NET Core Web Application in Visual Studio.</span></span> <span data-ttu-id="6af47-130">Wybierz szablon internetowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="6af47-130">Choose the Web API template.</span></span> <span data-ttu-id="6af47-131">Nazwij projekt *TodoAPI*.</span><span class="sxs-lookup"><span data-stu-id="6af47-131">Name the project *TodoAPI*.</span></span>

![Okno dialogowe Nowa aplikacja sieci Web ASP.NET z wybranym szablonem projektu interfejsu API sieci Web](native-mobile-backend/_static/web-api-template.png)

<span data-ttu-id="6af47-133">Aplikacja powinna odpowiedzieć na wszystkie żądania kierowane do portu 5000, w tym zwykły tekst http dla naszego klienta mobilnego.</span><span class="sxs-lookup"><span data-stu-id="6af47-133">The app should respond to all requests made to port 5000 including clear-text http traffic for our mobile client.</span></span> <span data-ttu-id="6af47-134">Aktualizacja *Startup.cs* <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> nie zostanie uruchomiona w programie Development:</span><span class="sxs-lookup"><span data-stu-id="6af47-134">Update *Startup.cs* so <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A> doesn't run in development:</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Startup.cs" id="snippet" highlight="7-11":::

> [!NOTE]
> <span data-ttu-id="6af47-135">Uruchom aplikację bezpośrednio, a nie za IIS Express.</span><span class="sxs-lookup"><span data-stu-id="6af47-135">Run the app directly, rather than behind IIS Express.</span></span> <span data-ttu-id="6af47-136">IIS Express domyślnie ignoruje żądania nielokalne.</span><span class="sxs-lookup"><span data-stu-id="6af47-136">IIS Express ignores non-local requests by default.</span></span> <span data-ttu-id="6af47-137">Uruchom polecenie [dotnet Run](/dotnet/core/tools/dotnet-run) z wiersza polecenia lub wybierz profil nazwy aplikacji z listy rozwijanej cel debugowania na pasku narzędzi programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="6af47-137">Run [dotnet run](/dotnet/core/tools/dotnet-run) from a command prompt, or choose the app name profile from the Debug Target dropdown in the Visual Studio toolbar.</span></span>

<span data-ttu-id="6af47-138">Dodaj klasę modelu, aby reprezentować elementy To-Do.</span><span class="sxs-lookup"><span data-stu-id="6af47-138">Add a model class to represent To-Do items.</span></span> <span data-ttu-id="6af47-139">Oznacz wymagane pola `[Required]` atrybutem:</span><span class="sxs-lookup"><span data-stu-id="6af47-139">Mark required fields with the `[Required]` attribute:</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Models/TodoItem.cs":::

<span data-ttu-id="6af47-140">Metody interfejsu API wymagają pewnego sposobu pracy z danymi.</span><span class="sxs-lookup"><span data-stu-id="6af47-140">The API methods require some way to work with data.</span></span> <span data-ttu-id="6af47-141">Użyj tego samego `ITodoRepository` interfejsu, z którego korzysta oryginalny przykład platformy Xamarin:</span><span class="sxs-lookup"><span data-stu-id="6af47-141">Use the same `ITodoRepository` interface the original Xamarin sample uses:</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Interfaces/ITodoRepository.cs":::

<span data-ttu-id="6af47-142">Dla tego przykładu implementacja używa tylko prywatnej kolekcji elementów:</span><span class="sxs-lookup"><span data-stu-id="6af47-142">For this sample, the implementation just uses a private collection of items:</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Services/TodoRepository.cs":::

<span data-ttu-id="6af47-143">Skonfiguruj implementację w programie *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="6af47-143">Configure the implementation in *Startup.cs*:</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Startup.cs" id="snippet2" highlight="3":::

## <a name="creating-the-controller"></a><span data-ttu-id="6af47-144">Tworzenie kontrolera</span><span class="sxs-lookup"><span data-stu-id="6af47-144">Creating the Controller</span></span>

<span data-ttu-id="6af47-145">Dodaj nowy kontroler do projektu, [TodoItemsController](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs).</span><span class="sxs-lookup"><span data-stu-id="6af47-145">Add a new controller to the project, [TodoItemsController](https://github.com/xamarin/xamarin-forms-samples/tree/master/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs).</span></span> <span data-ttu-id="6af47-146">Powinien dziedziczyć po <xref:Microsoft.AspNetCore.Mvc.ControllerBase> .</span><span class="sxs-lookup"><span data-stu-id="6af47-146">It should inherit from <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="6af47-147">Dodaj `Route` atrybut, aby wskazać, że kontroler będzie obsługiwać żądania wysyłane do ścieżek zaczynających się od `api/todoitems` .</span><span class="sxs-lookup"><span data-stu-id="6af47-147">Add a `Route` attribute to indicate that the controller will handle requests made to paths starting with `api/todoitems`.</span></span> <span data-ttu-id="6af47-148">`[controller]`Token w marszrucie jest zastępowany nazwą kontrolera (z pominięciem `Controller` sufiksu) i jest szczególnie przydatny w przypadku tras globalnych.</span><span class="sxs-lookup"><span data-stu-id="6af47-148">The `[controller]` token in the route is replaced by the name of the controller (omitting the `Controller` suffix), and is especially helpful for global routes.</span></span> <span data-ttu-id="6af47-149">Dowiedz się więcej o [routingu](../fundamentals/routing.md).</span><span class="sxs-lookup"><span data-stu-id="6af47-149">Learn more about [routing](../fundamentals/routing.md).</span></span>

<span data-ttu-id="6af47-150">Kontroler wymaga `ITodoRepository` funkcji do; Zażądaj wystąpienia tego typu za pomocą konstruktora kontrolera.</span><span class="sxs-lookup"><span data-stu-id="6af47-150">The controller requires an `ITodoRepository` to function; request an instance of this type through the controller's constructor.</span></span> <span data-ttu-id="6af47-151">W czasie wykonywania to wystąpienie zostanie dostarczone przy użyciu obsługi platformy w celu [iniekcji zależności](../fundamentals/dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="6af47-151">At runtime, this instance will be provided using the framework's support for [dependency injection](../fundamentals/dependency-injection.md).</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetDI":::

<span data-ttu-id="6af47-152">Ten interfejs API obsługuje cztery różne czasowniki HTTP do wykonywania operacji CRUD (tworzenie, odczytywanie, aktualizowanie i usuwanie) w źródle danych.</span><span class="sxs-lookup"><span data-stu-id="6af47-152">This API supports four different HTTP verbs to perform CRUD (Create, Read, Update, Delete) operations on the data source.</span></span> <span data-ttu-id="6af47-153">Najprostszą z nich jest operacja odczytu, która odnosi się do żądania HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="6af47-153">The simplest of these is the Read operation, which corresponds to an HTTP GET request.</span></span>

### <a name="reading-items"></a><span data-ttu-id="6af47-154">Odczytywanie elementów</span><span class="sxs-lookup"><span data-stu-id="6af47-154">Reading Items</span></span>

<span data-ttu-id="6af47-155">Żądanie listy elementów jest wykonywane z żądaniem GET do `List` metody.</span><span class="sxs-lookup"><span data-stu-id="6af47-155">Requesting a list of items is done with a GET request to the `List` method.</span></span> <span data-ttu-id="6af47-156">`[HttpGet]`Atrybut `List` metody wskazuje, że ta akcja powinna obsługiwać tylko żądania GET.</span><span class="sxs-lookup"><span data-stu-id="6af47-156">The `[HttpGet]` attribute on the `List` method indicates that this action should only handle GET requests.</span></span> <span data-ttu-id="6af47-157">Trasa dla tej akcji jest trasą określoną na kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="6af47-157">The route for this action is the route specified on the controller.</span></span> <span data-ttu-id="6af47-158">Nie trzeba koniecznie używać nazwy akcji jako części trasy.</span><span class="sxs-lookup"><span data-stu-id="6af47-158">You don't necessarily need to use the action name as part of the route.</span></span> <span data-ttu-id="6af47-159">Wystarczy upewnić się, że każda akcja ma unikatową i jednoznaczną trasę.</span><span class="sxs-lookup"><span data-stu-id="6af47-159">You just need to ensure each action has a unique and unambiguous route.</span></span> <span data-ttu-id="6af47-160">Atrybuty routingu mogą być stosowane zarówno na poziomie kontrolera, jak i metody do tworzenia określonych tras.</span><span class="sxs-lookup"><span data-stu-id="6af47-160">Routing attributes can be applied at both the controller and method levels to build up specific routes.</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippet":::

<span data-ttu-id="6af47-161">`List`Metoda zwraca kod odpowiedzi 200 OK i wszystkie elementy do wykonania, zserializowane jako JSON.</span><span class="sxs-lookup"><span data-stu-id="6af47-161">The `List` method returns a 200 OK response code and all of the Todo items, serialized as JSON.</span></span>

<span data-ttu-id="6af47-162">Nową metodę interfejsu API można testować przy użyciu różnych narzędzi, takich jak program [Poster](https://www.getpostman.com/docs/), jak pokazano tutaj:</span><span class="sxs-lookup"><span data-stu-id="6af47-162">You can test your new API method using a variety of tools, such as [Postman](https://www.getpostman.com/docs/), shown here:</span></span>

![Konsola programu Poster pokazująca żądanie GET dla TodoItems i treść odpowiedzi przedstawiającą kod JSON dla trzech zwróconych elementów](native-mobile-backend/_static/postman-get.png)

### <a name="creating-items"></a><span data-ttu-id="6af47-164">Tworzenie elementów</span><span class="sxs-lookup"><span data-stu-id="6af47-164">Creating Items</span></span>

<span data-ttu-id="6af47-165">Zgodnie z Konwencją tworzenie nowych elementów danych jest mapowane na zlecenie HTTP POST.</span><span class="sxs-lookup"><span data-stu-id="6af47-165">By convention, creating new data items is mapped to the HTTP POST verb.</span></span> <span data-ttu-id="6af47-166">`Create` `[HttpPost]` Do metody zastosowano atrybut i zaakceptowano `TodoItem` wystąpienie.</span><span class="sxs-lookup"><span data-stu-id="6af47-166">The `Create` method has an `[HttpPost]` attribute applied to it and accepts a `TodoItem` instance.</span></span> <span data-ttu-id="6af47-167">Ponieważ `item` argument jest przesyłany w treści wpisu, ten parametr określa `[FromBody]` atrybut.</span><span class="sxs-lookup"><span data-stu-id="6af47-167">Since the `item` argument is passed in the body of the POST, this parameter specifies the `[FromBody]` attribute.</span></span>

<span data-ttu-id="6af47-168">Wewnątrz metody element jest sprawdzany pod kątem ważności i wcześniejszej istnienia w magazynie danych, a jeśli nie wystąpią żadne problemy, zostanie dodany za pomocą repozytorium.</span><span class="sxs-lookup"><span data-stu-id="6af47-168">Inside the method, the item is checked for validity and prior existence in the data store, and if no issues occur, it's added using the repository.</span></span> <span data-ttu-id="6af47-169">Sprawdzanie `ModelState.IsValid` sprawdza [poprawność modelu](../mvc/models/validation.md)i należy je wykonać w każdej metodzie interfejsu API, która akceptuje dane wejściowe użytkownika.</span><span class="sxs-lookup"><span data-stu-id="6af47-169">Checking `ModelState.IsValid` performs [model validation](../mvc/models/validation.md), and should be done in every API method that accepts user input.</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetCreate":::

<span data-ttu-id="6af47-170">W przykładzie używane `enum` są zawierające kody błędów, które są przesyłane do klienta mobilnego:</span><span class="sxs-lookup"><span data-stu-id="6af47-170">The sample uses an `enum` containing error codes that are passed to the mobile client:</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetErrorCode":::

<span data-ttu-id="6af47-171">Przetestuj Dodawanie nowych elementów przy użyciu programu Poster, wybierając pozycję POST Verb dostarczającą nowy obiekt w formacie JSON w treści żądania.</span><span class="sxs-lookup"><span data-stu-id="6af47-171">Test adding new items using Postman by choosing the POST verb providing the new object in JSON format in the Body of the request.</span></span> <span data-ttu-id="6af47-172">Należy również dodać nagłówek żądania określający `Content-Type` `application/json` .</span><span class="sxs-lookup"><span data-stu-id="6af47-172">You should also add a request header specifying a `Content-Type` of `application/json`.</span></span>

![Konsola programu Poster pokazująca wpis i odpowiedź](native-mobile-backend/_static/postman-post.png)

<span data-ttu-id="6af47-174">Metoda zwraca nowo utworzony element w odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="6af47-174">The method returns the newly created item in the response.</span></span>

### <a name="updating-items"></a><span data-ttu-id="6af47-175">Aktualizowanie elementów</span><span class="sxs-lookup"><span data-stu-id="6af47-175">Updating Items</span></span>

<span data-ttu-id="6af47-176">Modyfikowanie rekordów odbywa się przy użyciu żądań HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="6af47-176">Modifying records is done using HTTP PUT requests.</span></span> <span data-ttu-id="6af47-177">Poza tą zmianą `Edit` Metoda jest niemal identyczna z `Create` .</span><span class="sxs-lookup"><span data-stu-id="6af47-177">Other than this change, the `Edit` method is almost identical to `Create`.</span></span> <span data-ttu-id="6af47-178">Należy pamiętać, że jeśli rekord nie zostanie znaleziony, `Edit` Akcja zwróci `NotFound` odpowiedź (404).</span><span class="sxs-lookup"><span data-stu-id="6af47-178">Note that if the record isn't found, the `Edit` action will return a `NotFound` (404) response.</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetEdit":::

<span data-ttu-id="6af47-179">Aby przetestować przy użyciu programu Poster, Zmień czasownik na wartość PUT.</span><span class="sxs-lookup"><span data-stu-id="6af47-179">To test with Postman, change the verb to PUT.</span></span> <span data-ttu-id="6af47-180">Określ zaktualizowane dane obiektu w treści żądania.</span><span class="sxs-lookup"><span data-stu-id="6af47-180">Specify the updated object data in the Body of the request.</span></span>

![Konsola programu post z informacjami o UMIESZCZENIU i odpowiedzi](native-mobile-backend/_static/postman-put.png)

<span data-ttu-id="6af47-182">Ta metoda zwraca `NoContent` (204) odpowiedź po pomyślnym, aby zapewnić spójność z istniejącym interfejsem API.</span><span class="sxs-lookup"><span data-stu-id="6af47-182">This method returns a `NoContent` (204) response when successful, for consistency with the pre-existing API.</span></span>

### <a name="deleting-items"></a><span data-ttu-id="6af47-183">Usuwanie elementów</span><span class="sxs-lookup"><span data-stu-id="6af47-183">Deleting Items</span></span>

<span data-ttu-id="6af47-184">Usuwanie rekordów jest realizowane przez wykonywanie żądań usuwania do usługi i przekazywanie identyfikatora elementu do usunięcia.</span><span class="sxs-lookup"><span data-stu-id="6af47-184">Deleting records is accomplished by making DELETE requests to the service, and passing the ID of the item to be deleted.</span></span> <span data-ttu-id="6af47-185">Podobnie jak w przypadku aktualizacji, żądania dla elementów, które nie istnieją, będą otrzymywać `NotFound` odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="6af47-185">As with updates, requests for items that don't exist will receive `NotFound` responses.</span></span> <span data-ttu-id="6af47-186">W przeciwnym razie pomyślne żądanie otrzyma `NoContent` odpowiedź (204).</span><span class="sxs-lookup"><span data-stu-id="6af47-186">Otherwise, a successful request will get a `NoContent` (204) response.</span></span>

:::code language="csharp" source="~/../xamarin-forms-samples/WebServices/TodoREST/TodoAPI/TodoAPI/Controllers/TodoItemsController.cs" id="snippetDelete":::

<span data-ttu-id="6af47-187">Należy pamiętać, że podczas testowania funkcji usuwania w treści żądania nic nie jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="6af47-187">Note that when testing the delete functionality, nothing is required in the Body of the request.</span></span>

![Konsola Poster pokazująca usuwanie i odpowiedź](native-mobile-backend/_static/postman-delete.png)

## <a name="prevent-over-posting"></a><span data-ttu-id="6af47-189">Zapobiegaj za pośrednictwem księgowania</span><span class="sxs-lookup"><span data-stu-id="6af47-189">Prevent over-posting</span></span>

<span data-ttu-id="6af47-190">Obecnie Przykładowa aplikacja uwidacznia cały `TodoItem` obiekt.</span><span class="sxs-lookup"><span data-stu-id="6af47-190">Currently the sample app exposes the entire `TodoItem` object.</span></span> <span data-ttu-id="6af47-191">Aplikacje produkcyjne zwykle ograniczają dane wejściowe i zwracane przy użyciu podzestawu modelu.</span><span class="sxs-lookup"><span data-stu-id="6af47-191">Production apps typically limit the data that's input and returned using a subset of the model.</span></span> <span data-ttu-id="6af47-192">Istnieje wiele powodów związanych z tym, a zabezpieczenia są głównymi.</span><span class="sxs-lookup"><span data-stu-id="6af47-192">There are multiple reasons behind this and security is a major one.</span></span> <span data-ttu-id="6af47-193">Podzestaw modelu jest zwykle określany jako obiekt Transfer danych (DTO), model wejściowy lub model widoku.</span><span class="sxs-lookup"><span data-stu-id="6af47-193">The subset of a model is usually referred to as a Data Transfer Object (DTO), input model, or view model.</span></span> <span data-ttu-id="6af47-194">**DTO** jest używany w tym artykule.</span><span class="sxs-lookup"><span data-stu-id="6af47-194">**DTO** is used in this article.</span></span>

<span data-ttu-id="6af47-195">DTO może służyć do:</span><span class="sxs-lookup"><span data-stu-id="6af47-195">A DTO may be used to:</span></span>

* <span data-ttu-id="6af47-196">Zablokuj nadmierne księgowanie.</span><span class="sxs-lookup"><span data-stu-id="6af47-196">Prevent over-posting.</span></span>
* <span data-ttu-id="6af47-197">Ukryj właściwości, które nie powinny być wyświetlane dla klientów.</span><span class="sxs-lookup"><span data-stu-id="6af47-197">Hide properties that clients are not supposed to view.</span></span>
* <span data-ttu-id="6af47-198">Pomiń niektóre właściwości, aby zmniejszyć rozmiar ładunku.</span><span class="sxs-lookup"><span data-stu-id="6af47-198">Omit some properties in order to reduce payload size.</span></span>
* <span data-ttu-id="6af47-199">Spłaszcz wykresy obiektów zawierające obiekty zagnieżdżone.</span><span class="sxs-lookup"><span data-stu-id="6af47-199">Flatten object graphs that contain nested objects.</span></span> <span data-ttu-id="6af47-200">Spłaszczone wykresy obiektów mogą być wygodniejsze dla klientów.</span><span class="sxs-lookup"><span data-stu-id="6af47-200">Flattened object graphs can be more convenient for clients.</span></span>

<span data-ttu-id="6af47-201">Aby zademonstrować podejście DTO, zobacz [zapobieganie nadmiernemu księgowaniu](xref:tutorials/first-web-api#prevent-over-posting)</span><span class="sxs-lookup"><span data-stu-id="6af47-201">To demonstrate the DTO approach, see [Prevent over-posting](xref:tutorials/first-web-api#prevent-over-posting)</span></span>

## <a name="common-web-api-conventions"></a><span data-ttu-id="6af47-202">Konwencje wspólnych interfejsów API sieci Web</span><span class="sxs-lookup"><span data-stu-id="6af47-202">Common Web API Conventions</span></span>

<span data-ttu-id="6af47-203">Podczas tworzenia usług zaplecza dla aplikacji, należy utworzyć spójny zestaw Konwencji lub zasad służących do obsługi zagadnień związanych z rozwojem.</span><span class="sxs-lookup"><span data-stu-id="6af47-203">As you develop the backend services for your app, you will want to come up with a consistent set of conventions or policies for handling cross-cutting concerns.</span></span> <span data-ttu-id="6af47-204">Na przykład w podanej powyżej usłudze żądania dla określonych rekordów, które nie zostały znalezione, dotarły do `NotFound` odpowiedzi, a nie `BadRequest` odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="6af47-204">For example, in the service shown above, requests for specific records that weren't found received a `NotFound` response, rather than a `BadRequest` response.</span></span> <span data-ttu-id="6af47-205">Podobnie polecenia wykonywane do tej usługi, które zostały spełnione w typach powiązanych przez model, są zawsze zaznaczone `ModelState.IsValid` i zwracane `BadRequest` dla nieprawidłowych typów modeli.</span><span class="sxs-lookup"><span data-stu-id="6af47-205">Similarly, commands made to this service that passed in model bound types always checked `ModelState.IsValid` and returned a `BadRequest` for invalid model types.</span></span>

<span data-ttu-id="6af47-206">Po zidentyfikowaniu wspólnych zasad dla interfejsów API można zwykle hermetyzować je w [filtrze](../mvc/controllers/filters.md).</span><span class="sxs-lookup"><span data-stu-id="6af47-206">Once you've identified a common policy for your APIs, you can usually encapsulate it in a [filter](../mvc/controllers/filters.md).</span></span> <span data-ttu-id="6af47-207">Dowiedz się więcej o [sposobie hermetyzacji wspólnych zasad interfejsu API w aplikacjach ASP.NET Core MVC](/archive/msdn-magazine/2016/august/asp-net-core-real-world-asp-net-core-mvc-filters).</span><span class="sxs-lookup"><span data-stu-id="6af47-207">Learn more about [how to encapsulate common API policies in ASP.NET Core MVC applications](/archive/msdn-magazine/2016/august/asp-net-core-real-world-asp-net-core-mvc-filters).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6af47-208">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="6af47-208">Additional resources</span></span>

- [<span data-ttu-id="6af47-209">Xamarin. Forms: uwierzytelnianie usługi sieci Web</span><span class="sxs-lookup"><span data-stu-id="6af47-209">Xamarin.Forms: Web Service Authentication</span></span>](/xamarin/xamarin-forms/data-cloud/authentication/)
- [<span data-ttu-id="6af47-210">Xamarin. Forms: korzystanie z usługi sieci Web RESTful</span><span class="sxs-lookup"><span data-stu-id="6af47-210">Xamarin.Forms: Consume a RESTful Web Service</span></span>](/xamarin/xamarin-forms/data-cloud/web-services/rest)
- [<span data-ttu-id="6af47-211">Microsoft Learn: korzystanie z usług sieci Web REST w aplikacjach platformy Xamarin</span><span class="sxs-lookup"><span data-stu-id="6af47-211">Microsoft Learn: Consume REST web services in Xamarin Apps</span></span>](/learn/modules/consume-rest-services/)
- [<span data-ttu-id="6af47-212">Microsoft Learn: Tworzenie interfejsu API sieci Web za pomocą ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6af47-212">Microsoft Learn: Create a web API with ASP.NET Core</span></span>](/learn/modules/build-web-api-aspnet-core/)
