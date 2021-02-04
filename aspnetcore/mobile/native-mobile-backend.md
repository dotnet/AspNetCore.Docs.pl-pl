---
title: Tworzenie usług zaplecza dla natywnych aplikacji mobilnych za pomocą ASP.NET Core
author: ardalis
description: Dowiedz się, jak utworzyć usługi zaplecza przy użyciu ASP.NET Core MVC do obsługi natywnych aplikacji mobilnych.
ms.author: riande
ms.date: 12/05/2019
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
ms.openlocfilehash: 4e86241771e884ba9079bcdf9a09eebc6acd62c8
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99530219"
---
# <a name="create-backend-services-for-native-mobile-apps-with-aspnet-core"></a><span data-ttu-id="45763-103">Tworzenie usług zaplecza dla natywnych aplikacji mobilnych za pomocą ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="45763-103">Create backend services for native mobile apps with ASP.NET Core</span></span>

<span data-ttu-id="45763-104">Przez [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="45763-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="45763-105">Aplikacje mobilne mogą komunikować się z ASP.NET Core usług zaplecza.</span><span class="sxs-lookup"><span data-stu-id="45763-105">Mobile apps can communicate with ASP.NET Core backend services.</span></span> <span data-ttu-id="45763-106">Aby uzyskać instrukcje dotyczące łączenia lokalnych usług sieci Web z symulatorami systemu iOS i emulatorami systemu Android, zobacz [nawiązywanie połączenia z lokalnymi usługami sieci Web z symulatorów systemu iOS i emulatorów systemu Android](/xamarin/cross-platform/deploy-test/connect-to-local-web-services).</span><span class="sxs-lookup"><span data-stu-id="45763-106">For instructions on connecting local web services from iOS simulators and Android emulators, see [Connect to Local Web Services from iOS Simulators and Android Emulators](/xamarin/cross-platform/deploy-test/connect-to-local-web-services).</span></span>

[<span data-ttu-id="45763-107">Wyświetlanie lub Pobieranie przykładowego kodu usług wewnętrznej bazy danych</span><span class="sxs-lookup"><span data-stu-id="45763-107">View or download sample backend services code</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mobile/native-mobile-backend/sample)

## <a name="the-sample-native-mobile-app"></a><span data-ttu-id="45763-108">Przykładowa Natywna aplikacja mobilna</span><span class="sxs-lookup"><span data-stu-id="45763-108">The Sample Native Mobile App</span></span>

<span data-ttu-id="45763-109">W tym samouczku przedstawiono sposób tworzenia usług zaplecza przy użyciu ASP.NET Core MVC do obsługi natywnych aplikacji mobilnych.</span><span class="sxs-lookup"><span data-stu-id="45763-109">This tutorial demonstrates how to create backend services using ASP.NET Core MVC to support native mobile apps.</span></span> <span data-ttu-id="45763-110">Korzysta ona z [aplikacji Xamarin Forms ToDoRest](/xamarin/xamarin-forms/data-cloud/consuming/rest) jako klienta natywnego, w tym oddzielnych klientów natywnych dla urządzeń z systemami Android, iOS, uniwersalnym i Windows Phone.</span><span class="sxs-lookup"><span data-stu-id="45763-110">It uses the [Xamarin Forms ToDoRest app](/xamarin/xamarin-forms/data-cloud/consuming/rest) as its native client, which includes separate native clients for Android, iOS, Windows Universal, and Window Phone devices.</span></span> <span data-ttu-id="45763-111">Aby utworzyć aplikację natywną (i zainstalować niezbędne bezpłatne narzędzia Xamarin), możesz skorzystać z połączonego samouczka, a także pobrać przykładowe rozwiązanie Xamarin.</span><span class="sxs-lookup"><span data-stu-id="45763-111">You can follow the linked tutorial to create the native app (and install the necessary free Xamarin tools), as well as download the Xamarin sample solution.</span></span> <span data-ttu-id="45763-112">Przykład platformy Xamarin zawiera projekt usług ASP.NET Web API 2, który zastąpi aplikacja ASP.NET Core w tym artykule (bez zmian wymaganych przez klienta).</span><span class="sxs-lookup"><span data-stu-id="45763-112">The Xamarin sample includes an ASP.NET Web API 2 services project, which this article's ASP.NET Core app replaces (with no changes required by the client).</span></span>

![Aby aplikacja REST działała na urządzeniu smartphone z systemem Android](native-mobile-backend/_static/todo-android.png)

### <a name="features"></a><span data-ttu-id="45763-114">Funkcje</span><span class="sxs-lookup"><span data-stu-id="45763-114">Features</span></span>

<span data-ttu-id="45763-115">Aplikacja ToDoRest obsługuje wyświetlanie list, Dodawanie, usuwanie i aktualizowanie elementów To-Do.</span><span class="sxs-lookup"><span data-stu-id="45763-115">The ToDoRest app supports listing, adding, deleting, and updating To-Do items.</span></span> <span data-ttu-id="45763-116">Każdy element ma identyfikator, nazwę, notatki i Właściwość wskazującą, czy została jeszcze ukończona.</span><span class="sxs-lookup"><span data-stu-id="45763-116">Each item has an ID, a Name, Notes, and a property indicating whether it's been Done yet.</span></span>

<span data-ttu-id="45763-117">Główny widok elementów, jak pokazano powyżej, zawiera listę nazw poszczególnych elementów i wskazuje, czy jest on wykonany za pomocą znacznika wyboru.</span><span class="sxs-lookup"><span data-stu-id="45763-117">The main view of the items, as shown above, lists each item's name and indicates if it's done with a checkmark.</span></span>

<span data-ttu-id="45763-118">Naciśnięcie `+` ikony powoduje otwarcie okna dialogowego Dodawanie elementu:</span><span class="sxs-lookup"><span data-stu-id="45763-118">Tapping the `+` icon opens an add item dialog:</span></span>

![Okno dialogowe Dodawanie elementu](native-mobile-backend/_static/todo-android-new-item.png)

<span data-ttu-id="45763-120">Naciśnięcie elementu na głównym ekranie listy otwiera okno dialogowe edytowania, w którym można modyfikować nazwę elementu, notatki i gotowe ustawienia lub można usunąć element:</span><span class="sxs-lookup"><span data-stu-id="45763-120">Tapping an item on the main list screen opens up an edit dialog where the item's Name, Notes, and Done settings can be modified, or the item can be deleted:</span></span>

![Edytowanie elementu — okno dialogowe](native-mobile-backend/_static/todo-android-edit-item.png)

<span data-ttu-id="45763-122">Ten przykład jest domyślnie skonfigurowany do korzystania z usług zaplecza hostowanych w developer.xamarin.com, które zezwalają na operacje tylko do odczytu.</span><span class="sxs-lookup"><span data-stu-id="45763-122">This sample is configured by default to use backend services hosted at developer.xamarin.com, which allow read-only operations.</span></span> <span data-ttu-id="45763-123">Aby przetestować samodzielnie aplikację ASP.NET Core utworzoną w następnej sekcji działającej na komputerze, musisz zaktualizować `RestUrl` stałą aplikacji.</span><span class="sxs-lookup"><span data-stu-id="45763-123">To test it out yourself against the ASP.NET Core app created in the next section running on your computer, you'll need to update the app's `RestUrl` constant.</span></span> <span data-ttu-id="45763-124">Przejdź do `ToDoREST` projektu i Otwórz plik *Constants.cs* .</span><span class="sxs-lookup"><span data-stu-id="45763-124">Navigate to the `ToDoREST` project and open the *Constants.cs* file.</span></span> <span data-ttu-id="45763-125">Zastąp ciąg `RestUrl` adresem URL, który zawiera adres IP maszyny (nie localhost lub 127.0.0.1, ponieważ ten adres jest używany z emulatora urządzenia, a nie z komputera).</span><span class="sxs-lookup"><span data-stu-id="45763-125">Replace the `RestUrl` with a URL that includes your machine's IP address (not localhost or 127.0.0.1, since this address is used from the device emulator, not from your machine).</span></span> <span data-ttu-id="45763-126">Uwzględnij również numer portu (5000).</span><span class="sxs-lookup"><span data-stu-id="45763-126">Include the port number as well (5000).</span></span> <span data-ttu-id="45763-127">Aby sprawdzić, czy usługi działają z urządzeniem, upewnij się, że nie masz aktywnej zapory, która blokuje dostęp do tego portu.</span><span class="sxs-lookup"><span data-stu-id="45763-127">In order to test that your services work with a device, ensure you don't have an active firewall blocking access to this port.</span></span>

```csharp
// URL of REST service (Xamarin ReadOnly Service)
//public static string RestUrl = "http://developer.xamarin.com:8081/api/todoitems{0}";

// use your machine's IP address
public static string RestUrl = "http://192.168.1.207:5000/api/todoitems/{0}";
```

## <a name="creating-the-aspnet-core-project"></a><span data-ttu-id="45763-128">Tworzenie projektu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="45763-128">Creating the ASP.NET Core Project</span></span>

<span data-ttu-id="45763-129">Utwórz nową aplikację sieci Web ASP.NET Core w programie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="45763-129">Create a new ASP.NET Core Web Application in Visual Studio.</span></span> <span data-ttu-id="45763-130">Wybierz szablon internetowego interfejsu API i bez uwierzytelniania.</span><span class="sxs-lookup"><span data-stu-id="45763-130">Choose the Web API template and No Authentication.</span></span> <span data-ttu-id="45763-131">Nazwij projekt *ToDoApi*.</span><span class="sxs-lookup"><span data-stu-id="45763-131">Name the project *ToDoApi*.</span></span>

![Okno dialogowe Nowa aplikacja sieci Web ASP.NET z wybranym szablonem projektu interfejsu API sieci Web](native-mobile-backend/_static/web-api-template.png)

<span data-ttu-id="45763-133">Aplikacja powinna odpowiedzieć na wszystkie żądania kierowane do portu 5000.</span><span class="sxs-lookup"><span data-stu-id="45763-133">The application should respond to all requests made to port 5000.</span></span> <span data-ttu-id="45763-134">Zaktualizuj *program.cs* do dołączenia `.UseUrls("http://*:5000")` , aby to osiągnąć:</span><span class="sxs-lookup"><span data-stu-id="45763-134">Update *Program.cs* to include `.UseUrls("http://*:5000")` to achieve this:</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Program.cs?range=10-16&highlight=3)]

> [!NOTE]
> <span data-ttu-id="45763-135">Upewnij się, że aplikacja jest uruchamiana bezpośrednio, a nie w IIS Express, co domyślnie ignoruje żądania nielokalne.</span><span class="sxs-lookup"><span data-stu-id="45763-135">Make sure you run the application directly, rather than behind IIS Express, which ignores non-local requests by default.</span></span> <span data-ttu-id="45763-136">Uruchom polecenie [dotnet Run](/dotnet/core/tools/dotnet-run) z wiersza polecenia lub wybierz profil nazwy aplikacji z listy rozwijanej cel debugowania na pasku narzędzi programu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="45763-136">Run [dotnet run](/dotnet/core/tools/dotnet-run) from a command prompt, or choose the application name profile from the Debug Target dropdown in the Visual Studio toolbar.</span></span>

<span data-ttu-id="45763-137">Dodaj klasę modelu, aby reprezentować elementy To-Do.</span><span class="sxs-lookup"><span data-stu-id="45763-137">Add a model class to represent To-Do items.</span></span> <span data-ttu-id="45763-138">Oznacz wymagane pola `[Required]` atrybutem:</span><span class="sxs-lookup"><span data-stu-id="45763-138">Mark required fields with the `[Required]` attribute:</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Models/ToDoItem.cs)]

<span data-ttu-id="45763-139">Metody interfejsu API wymagają pewnego sposobu pracy z danymi.</span><span class="sxs-lookup"><span data-stu-id="45763-139">The API methods require some way to work with data.</span></span> <span data-ttu-id="45763-140">Użyj tego samego `IToDoRepository` interfejsu, z którego korzysta oryginalny przykład platformy Xamarin:</span><span class="sxs-lookup"><span data-stu-id="45763-140">Use the same `IToDoRepository` interface the original Xamarin sample uses:</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Interfaces/IToDoRepository.cs)]

<span data-ttu-id="45763-141">Dla tego przykładu implementacja używa tylko prywatnej kolekcji elementów:</span><span class="sxs-lookup"><span data-stu-id="45763-141">For this sample, the implementation just uses a private collection of items:</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Services/ToDoRepository.cs)]

<span data-ttu-id="45763-142">Skonfiguruj implementację w programie *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="45763-142">Configure the implementation in *Startup.cs*:</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Startup.cs?highlight=6&range=29-35)]

<span data-ttu-id="45763-143">W tym momencie można przystąpić do tworzenia *ToDoItemsController*.</span><span class="sxs-lookup"><span data-stu-id="45763-143">At this point, you're ready to create the *ToDoItemsController*.</span></span>

> [!TIP]
> <span data-ttu-id="45763-144">Dowiedz się więcej o tworzeniu interfejsów API sieci Web w temacie Tworzenie [pierwszego internetowego interfejsu API za pomocą ASP.NET Core MVC i Visual Studio](../tutorials/first-web-api.md).</span><span class="sxs-lookup"><span data-stu-id="45763-144">Learn more about creating web APIs in [Build your first Web API with ASP.NET Core MVC and Visual Studio](../tutorials/first-web-api.md).</span></span>

## <a name="creating-the-controller"></a><span data-ttu-id="45763-145">Tworzenie kontrolera</span><span class="sxs-lookup"><span data-stu-id="45763-145">Creating the Controller</span></span>

<span data-ttu-id="45763-146">Dodaj nowy kontroler do projektu, *ToDoItemsController*.</span><span class="sxs-lookup"><span data-stu-id="45763-146">Add a new controller to the project, *ToDoItemsController*.</span></span> <span data-ttu-id="45763-147">Powinien on dziedziczyć po elemencie Microsoft. AspNetCore. MVC. Controller.</span><span class="sxs-lookup"><span data-stu-id="45763-147">It should inherit from Microsoft.AspNetCore.Mvc.Controller.</span></span> <span data-ttu-id="45763-148">Dodaj `Route` atrybut, aby wskazać, że kontroler będzie obsługiwać żądania wysyłane do ścieżek zaczynających się od `api/todoitems` .</span><span class="sxs-lookup"><span data-stu-id="45763-148">Add a `Route` attribute to indicate that the controller will handle requests made to paths starting with `api/todoitems`.</span></span> <span data-ttu-id="45763-149">`[controller]`Token w marszrucie jest zastępowany nazwą kontrolera (z pominięciem `Controller` sufiksu) i jest szczególnie przydatny w przypadku tras globalnych.</span><span class="sxs-lookup"><span data-stu-id="45763-149">The `[controller]` token in the route is replaced by the name of the controller (omitting the `Controller` suffix), and is especially helpful for global routes.</span></span> <span data-ttu-id="45763-150">Dowiedz się więcej o [routingu](../fundamentals/routing.md).</span><span class="sxs-lookup"><span data-stu-id="45763-150">Learn more about [routing](../fundamentals/routing.md).</span></span>

<span data-ttu-id="45763-151">Kontroler wymaga `IToDoRepository` funkcji do; Zażądaj wystąpienia tego typu za pomocą konstruktora kontrolera.</span><span class="sxs-lookup"><span data-stu-id="45763-151">The controller requires an `IToDoRepository` to function; request an instance of this type through the controller's constructor.</span></span> <span data-ttu-id="45763-152">W czasie wykonywania to wystąpienie zostanie dostarczone przy użyciu obsługi platformy w celu [iniekcji zależności](../fundamentals/dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="45763-152">At runtime, this instance will be provided using the framework's support for [dependency injection](../fundamentals/dependency-injection.md).</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=1-17&highlight=9,14)]

<span data-ttu-id="45763-153">Ten interfejs API obsługuje cztery różne czasowniki HTTP do wykonywania operacji CRUD (tworzenie, odczytywanie, aktualizowanie i usuwanie) w źródle danych.</span><span class="sxs-lookup"><span data-stu-id="45763-153">This API supports four different HTTP verbs to perform CRUD (Create, Read, Update, Delete) operations on the data source.</span></span> <span data-ttu-id="45763-154">Najprostszą z nich jest operacja odczytu, która odnosi się do żądania HTTP GET.</span><span class="sxs-lookup"><span data-stu-id="45763-154">The simplest of these is the Read operation, which corresponds to an HTTP GET request.</span></span>

### <a name="reading-items"></a><span data-ttu-id="45763-155">Odczytywanie elementów</span><span class="sxs-lookup"><span data-stu-id="45763-155">Reading Items</span></span>

<span data-ttu-id="45763-156">Żądanie listy elementów jest wykonywane z żądaniem GET do `List` metody.</span><span class="sxs-lookup"><span data-stu-id="45763-156">Requesting a list of items is done with a GET request to the `List` method.</span></span> <span data-ttu-id="45763-157">`[HttpGet]`Atrybut `List` metody wskazuje, że ta akcja powinna obsługiwać tylko żądania GET.</span><span class="sxs-lookup"><span data-stu-id="45763-157">The `[HttpGet]` attribute on the `List` method indicates that this action should only handle GET requests.</span></span> <span data-ttu-id="45763-158">Trasa dla tej akcji jest trasą określoną na kontrolerze.</span><span class="sxs-lookup"><span data-stu-id="45763-158">The route for this action is the route specified on the controller.</span></span> <span data-ttu-id="45763-159">Nie trzeba koniecznie używać nazwy akcji jako części trasy.</span><span class="sxs-lookup"><span data-stu-id="45763-159">You don't necessarily need to use the action name as part of the route.</span></span> <span data-ttu-id="45763-160">Wystarczy upewnić się, że każda akcja ma unikatową i jednoznaczną trasę.</span><span class="sxs-lookup"><span data-stu-id="45763-160">You just need to ensure each action has a unique and unambiguous route.</span></span> <span data-ttu-id="45763-161">Atrybuty routingu mogą być stosowane zarówno na poziomie kontrolera, jak i metody do tworzenia określonych tras.</span><span class="sxs-lookup"><span data-stu-id="45763-161">Routing attributes can be applied at both the controller and method levels to build up specific routes.</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=19-23)]

<span data-ttu-id="45763-162">`List`Metoda zwraca kod odpowiedzi 200 OK i wszystkie elementy do wykonania, zserializowane jako JSON.</span><span class="sxs-lookup"><span data-stu-id="45763-162">The `List` method returns a 200 OK response code and all of the ToDo items, serialized as JSON.</span></span>

<span data-ttu-id="45763-163">Nową metodę interfejsu API można testować przy użyciu różnych narzędzi, takich jak program [Poster](https://www.getpostman.com/docs/), jak pokazano tutaj:</span><span class="sxs-lookup"><span data-stu-id="45763-163">You can test your new API method using a variety of tools, such as [Postman](https://www.getpostman.com/docs/), shown here:</span></span>

![Konsola programu Poster pokazująca żądanie GET dla TodoItems i treść odpowiedzi przedstawiającą kod JSON dla trzech zwróconych elementów](native-mobile-backend/_static/postman-get.png)

### <a name="creating-items"></a><span data-ttu-id="45763-165">Tworzenie elementów</span><span class="sxs-lookup"><span data-stu-id="45763-165">Creating Items</span></span>

<span data-ttu-id="45763-166">Zgodnie z Konwencją tworzenie nowych elementów danych jest mapowane na zlecenie HTTP POST.</span><span class="sxs-lookup"><span data-stu-id="45763-166">By convention, creating new data items is mapped to the HTTP POST verb.</span></span> <span data-ttu-id="45763-167">`Create` `[HttpPost]` Do metody zastosowano atrybut i zaakceptowano `ToDoItem` wystąpienie.</span><span class="sxs-lookup"><span data-stu-id="45763-167">The `Create` method has an `[HttpPost]` attribute applied to it and accepts a `ToDoItem` instance.</span></span> <span data-ttu-id="45763-168">Ponieważ `item` argument jest przesyłany w treści wpisu, ten parametr określa `[FromBody]` atrybut.</span><span class="sxs-lookup"><span data-stu-id="45763-168">Since the `item` argument is passed in the body of the POST, this parameter specifies the `[FromBody]` attribute.</span></span>

<span data-ttu-id="45763-169">Wewnątrz metody element jest sprawdzany pod kątem ważności i wcześniejszej istnienia w magazynie danych, a jeśli nie wystąpią żadne problemy, zostanie dodany za pomocą repozytorium.</span><span class="sxs-lookup"><span data-stu-id="45763-169">Inside the method, the item is checked for validity and prior existence in the data store, and if no issues occur, it's added using the repository.</span></span> <span data-ttu-id="45763-170">Sprawdzanie `ModelState.IsValid` sprawdza [poprawność modelu](../mvc/models/validation.md)i należy je wykonać w każdej metodzie interfejsu API, która akceptuje dane wejściowe użytkownika.</span><span class="sxs-lookup"><span data-stu-id="45763-170">Checking `ModelState.IsValid` performs [model validation](../mvc/models/validation.md), and should be done in every API method that accepts user input.</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=25-46)]

<span data-ttu-id="45763-171">Przykład używa wyliczenia zawierającego kody błędów, które są przesyłane do klienta mobilnego:</span><span class="sxs-lookup"><span data-stu-id="45763-171">The sample uses an enum containing error codes that are passed to the mobile client:</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=91-99)]

<span data-ttu-id="45763-172">Przetestuj Dodawanie nowych elementów przy użyciu programu Poster, wybierając pozycję POST Verb dostarczającą nowy obiekt w formacie JSON w treści żądania.</span><span class="sxs-lookup"><span data-stu-id="45763-172">Test adding new items using Postman by choosing the POST verb providing the new object in JSON format in the Body of the request.</span></span> <span data-ttu-id="45763-173">Należy również dodać nagłówek żądania określający `Content-Type` `application/json` .</span><span class="sxs-lookup"><span data-stu-id="45763-173">You should also add a request header specifying a `Content-Type` of `application/json`.</span></span>

![Konsola programu Poster pokazująca wpis i odpowiedź](native-mobile-backend/_static/postman-post.png)

<span data-ttu-id="45763-175">Metoda zwraca nowo utworzony element w odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="45763-175">The method returns the newly created item in the response.</span></span>

### <a name="updating-items"></a><span data-ttu-id="45763-176">Aktualizowanie elementów</span><span class="sxs-lookup"><span data-stu-id="45763-176">Updating Items</span></span>

<span data-ttu-id="45763-177">Modyfikowanie rekordów odbywa się przy użyciu żądań HTTP PUT.</span><span class="sxs-lookup"><span data-stu-id="45763-177">Modifying records is done using HTTP PUT requests.</span></span> <span data-ttu-id="45763-178">Poza tą zmianą `Edit` Metoda jest niemal identyczna z `Create` .</span><span class="sxs-lookup"><span data-stu-id="45763-178">Other than this change, the `Edit` method is almost identical to `Create`.</span></span> <span data-ttu-id="45763-179">Należy pamiętać, że jeśli rekord nie zostanie znaleziony, `Edit` Akcja zwróci `NotFound` odpowiedź (404).</span><span class="sxs-lookup"><span data-stu-id="45763-179">Note that if the record isn't found, the `Edit` action will return a `NotFound` (404) response.</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=48-69)]

<span data-ttu-id="45763-180">Aby przetestować przy użyciu programu Poster, Zmień czasownik na wartość PUT.</span><span class="sxs-lookup"><span data-stu-id="45763-180">To test with Postman, change the verb to PUT.</span></span> <span data-ttu-id="45763-181">Określ zaktualizowane dane obiektu w treści żądania.</span><span class="sxs-lookup"><span data-stu-id="45763-181">Specify the updated object data in the Body of the request.</span></span>

![Konsola programu post z informacjami o UMIESZCZENIU i odpowiedzi](native-mobile-backend/_static/postman-put.png)

<span data-ttu-id="45763-183">Ta metoda zwraca `NoContent` (204) odpowiedź po pomyślnym, aby zapewnić spójność z istniejącym interfejsem API.</span><span class="sxs-lookup"><span data-stu-id="45763-183">This method returns a `NoContent` (204) response when successful, for consistency with the pre-existing API.</span></span>

### <a name="deleting-items"></a><span data-ttu-id="45763-184">Usuwanie elementów</span><span class="sxs-lookup"><span data-stu-id="45763-184">Deleting Items</span></span>

<span data-ttu-id="45763-185">Usuwanie rekordów jest realizowane przez wykonywanie żądań usuwania do usługi i przekazywanie identyfikatora elementu do usunięcia.</span><span class="sxs-lookup"><span data-stu-id="45763-185">Deleting records is accomplished by making DELETE requests to the service, and passing the ID of the item to be deleted.</span></span> <span data-ttu-id="45763-186">Podobnie jak w przypadku aktualizacji, żądania dla elementów, które nie istnieją, będą otrzymywać `NotFound` odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="45763-186">As with updates, requests for items that don't exist will receive `NotFound` responses.</span></span> <span data-ttu-id="45763-187">W przeciwnym razie pomyślne żądanie otrzyma `NoContent` odpowiedź (204).</span><span class="sxs-lookup"><span data-stu-id="45763-187">Otherwise, a successful request will get a `NoContent` (204) response.</span></span>

[!code-csharp[](native-mobile-backend/sample/ToDoApi/src/ToDoApi/Controllers/ToDoItemsController.cs?range=71-88)]

<span data-ttu-id="45763-188">Należy pamiętać, że podczas testowania funkcji usuwania w treści żądania nic nie jest wymagane.</span><span class="sxs-lookup"><span data-stu-id="45763-188">Note that when testing the delete functionality, nothing is required in the Body of the request.</span></span>

![Konsola Poster pokazująca usuwanie i odpowiedź](native-mobile-backend/_static/postman-delete.png)

## <a name="common-web-api-conventions"></a><span data-ttu-id="45763-190">Konwencje wspólnych interfejsów API sieci Web</span><span class="sxs-lookup"><span data-stu-id="45763-190">Common Web API Conventions</span></span>

<span data-ttu-id="45763-191">Podczas tworzenia usług zaplecza dla aplikacji, należy utworzyć spójny zestaw Konwencji lub zasad służących do obsługi zagadnień związanych z rozwojem.</span><span class="sxs-lookup"><span data-stu-id="45763-191">As you develop the backend services for your app, you will want to come up with a consistent set of conventions or policies for handling cross-cutting concerns.</span></span> <span data-ttu-id="45763-192">Na przykład w podanej powyżej usłudze żądania dla określonych rekordów, które nie zostały znalezione, dotarły do `NotFound` odpowiedzi, a nie `BadRequest` odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="45763-192">For example, in the service shown above, requests for specific records that weren't found received a `NotFound` response, rather than a `BadRequest` response.</span></span> <span data-ttu-id="45763-193">Podobnie polecenia wykonywane do tej usługi, które zostały spełnione w typach powiązanych przez model, są zawsze zaznaczone `ModelState.IsValid` i zwracane `BadRequest` dla nieprawidłowych typów modeli.</span><span class="sxs-lookup"><span data-stu-id="45763-193">Similarly, commands made to this service that passed in model bound types always checked `ModelState.IsValid` and returned a `BadRequest` for invalid model types.</span></span>

<span data-ttu-id="45763-194">Po zidentyfikowaniu wspólnych zasad dla interfejsów API można zwykle hermetyzować je w [filtrze](../mvc/controllers/filters.md).</span><span class="sxs-lookup"><span data-stu-id="45763-194">Once you've identified a common policy for your APIs, you can usually encapsulate it in a [filter](../mvc/controllers/filters.md).</span></span> <span data-ttu-id="45763-195">Dowiedz się więcej o [sposobie hermetyzacji wspólnych zasad interfejsu API w aplikacjach ASP.NET Core MVC](/archive/msdn-magazine/2016/august/asp-net-core-real-world-asp-net-core-mvc-filters).</span><span class="sxs-lookup"><span data-stu-id="45763-195">Learn more about [how to encapsulate common API policies in ASP.NET Core MVC applications](/archive/msdn-magazine/2016/august/asp-net-core-real-world-asp-net-core-mvc-filters).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="45763-196">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="45763-196">Additional resources</span></span>

- [<span data-ttu-id="45763-197">Xamarin. Forms: uwierzytelnianie usługi sieci Web</span><span class="sxs-lookup"><span data-stu-id="45763-197">Xamarin.Forms: Web Service Authentication</span></span>](/xamarin/xamarin-forms/data-cloud/authentication/)
- [<span data-ttu-id="45763-198">Xamarin. Forms: korzystanie z usługi sieci Web RESTful</span><span class="sxs-lookup"><span data-stu-id="45763-198">Xamarin.Forms: Consume a RESTful Web Service</span></span>](/xamarin/xamarin-forms/data-cloud/web-services/rest)
- [<span data-ttu-id="45763-199">Microsoft Learn: korzystanie z usług sieci Web REST w aplikacjach platformy Xamarin</span><span class="sxs-lookup"><span data-stu-id="45763-199">Microsoft Learn: Consume REST web services in Xamarin Apps</span></span>](/learn/modules/consume-rest-services/)
- [<span data-ttu-id="45763-200">Microsoft Learn: Tworzenie interfejsu API sieci Web za pomocą ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="45763-200">Microsoft Learn: Create a web API with ASP.NET Core</span></span>](/learn/modules/build-web-api-aspnet-core/)
