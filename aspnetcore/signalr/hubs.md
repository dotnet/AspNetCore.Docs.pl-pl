---
title: Korzystanie z centrów w ASP.NET Core SignalR
author: bradygaster
description: Dowiedz się, jak korzystać z centrów w ASP.NET Core SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
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
uid: signalr/hubs
ms.openlocfilehash: 71ca0896bc645b7625f60c3a9e8fe321079d524a
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88631280"
---
# <a name="use-hubs-in-no-locsignalr-for-aspnet-core"></a><span data-ttu-id="d228b-103">Korzystanie z koncentratorów w programie SignalR dla ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d228b-103">Use hubs in SignalR for ASP.NET Core</span></span>

<span data-ttu-id="d228b-104">Autor [Rachel Appel](https://twitter.com/rachelappel) i [Jan Griffin](https://twitter.com/1kevgriff)</span><span class="sxs-lookup"><span data-stu-id="d228b-104">By [Rachel Appel](https://twitter.com/rachelappel) and [Kevin Griffin](https://twitter.com/1kevgriff)</span></span>

<span data-ttu-id="d228b-105">[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubs/sample/ ) [(jak pobrać)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="d228b-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubs/sample/ ) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="what-is-a-no-locsignalr-hub"></a><span data-ttu-id="d228b-106">Co to jest SignalR centrum</span><span class="sxs-lookup"><span data-stu-id="d228b-106">What is a SignalR hub</span></span>

<span data-ttu-id="d228b-107">SignalRInterfejs API centrów umożliwia wywoływanie metod na podłączonych klientach z serwera.</span><span class="sxs-lookup"><span data-stu-id="d228b-107">The SignalR Hubs API enables you to call methods on connected clients from the server.</span></span> <span data-ttu-id="d228b-108">W kodzie serwera należy zdefiniować metody, które są wywoływane przez klienta.</span><span class="sxs-lookup"><span data-stu-id="d228b-108">In the server code, you define methods that are called by client.</span></span> <span data-ttu-id="d228b-109">W kodzie klienta należy zdefiniować metody, które są wywoływane z serwera programu.</span><span class="sxs-lookup"><span data-stu-id="d228b-109">In the client code, you define methods that are called from the server.</span></span> <span data-ttu-id="d228b-110">SignalR zajmuje się wszystkimi wszystkimi scenami, które zapewniają możliwość komunikacji między klientem i serwerem w czasie rzeczywistym.</span><span class="sxs-lookup"><span data-stu-id="d228b-110">SignalR takes care of everything behind the scenes that makes real-time client-to-server and server-to-client communications possible.</span></span>

## <a name="configure-no-locsignalr-hubs"></a><span data-ttu-id="d228b-111">Konfigurowanie SignalR centrów</span><span class="sxs-lookup"><span data-stu-id="d228b-111">Configure SignalR hubs</span></span>

<span data-ttu-id="d228b-112">SignalROprogramowanie pośredniczące wymaga pewnych usług, które są konfigurowane przez wywołanie metody `services.AddSignalR` .</span><span class="sxs-lookup"><span data-stu-id="d228b-112">The SignalR middleware requires some services, which are configured by calling `services.AddSignalR`.</span></span>

[!code-csharp[Configure service](hubs/sample/startup.cs?range=38)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d228b-113">Podczas dodawania SignalR funkcji do aplikacji ASP.NET Core, skonfiguruj SignalR trasy, wywołując wywołanie `endpoint.MapHub` `Startup.Configure` metody `app.UseEndpoints` wywołania zwrotnego.</span><span class="sxs-lookup"><span data-stu-id="d228b-113">When adding SignalR functionality to an ASP.NET Core app, setup SignalR routes by calling `endpoint.MapHub` in the `Startup.Configure` method's `app.UseEndpoints` callback.</span></span>

```csharp
app.UseRouting();
app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/chathub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="d228b-114">Podczas dodawania SignalR funkcji do aplikacji ASP.NET Core, skonfiguruj SignalR trasy, wywołując `app.UseSignalR` `Startup.Configure` metodę.</span><span class="sxs-lookup"><span data-stu-id="d228b-114">When adding SignalR functionality to an ASP.NET Core app, setup SignalR routes by calling `app.UseSignalR` in the `Startup.Configure` method.</span></span>

[!code-csharp[Configure routes to hubs](hubs/sample/startup.cs?range=57-60)]

::: moniker-end

## <a name="create-and-use-hubs"></a><span data-ttu-id="d228b-115">Tworzenie i używanie centrów</span><span class="sxs-lookup"><span data-stu-id="d228b-115">Create and use hubs</span></span>

<span data-ttu-id="d228b-116">Utwórz centrum, deklarując klasę, która dziedziczy z `Hub` , i Dodaj do niej metody publiczne.</span><span class="sxs-lookup"><span data-stu-id="d228b-116">Create a hub by declaring a class that inherits from `Hub`, and add public methods to it.</span></span> <span data-ttu-id="d228b-117">Klienci mogą wywoływać metody, które są zdefiniowane jako `public` .</span><span class="sxs-lookup"><span data-stu-id="d228b-117">Clients can call methods that are defined as `public`.</span></span>

```csharp
public class ChatHub : Hub
{
    public Task SendMessage(string user, string message)
    {
        return Clients.All.SendAsync("ReceiveMessage", user, message);
    }
}
```

<span data-ttu-id="d228b-118">Można określić typ zwracany i parametry, w tym typy złożone i tablice, tak jak w przypadku dowolnej metody języka C#.</span><span class="sxs-lookup"><span data-stu-id="d228b-118">You can specify a return type and parameters, including complex types and arrays, as you would in any C# method.</span></span> <span data-ttu-id="d228b-119">SignalR obsługuje serializacji i deserializacji złożonych obiektów i tablic w parametrach i zwracanych wartości.</span><span class="sxs-lookup"><span data-stu-id="d228b-119">SignalR handles the serialization and deserialization of complex objects and arrays in your parameters and return values.</span></span>

> [!NOTE]
> <span data-ttu-id="d228b-120">Centra są przejściowe:</span><span class="sxs-lookup"><span data-stu-id="d228b-120">Hubs are transient:</span></span>
>
> * <span data-ttu-id="d228b-121">Nie przechowuj stanu we właściwości klasy centrum.</span><span class="sxs-lookup"><span data-stu-id="d228b-121">Don't store state in a property on the hub class.</span></span> <span data-ttu-id="d228b-122">Każde wywołanie metody centrum jest wykonywane na nowym wystąpieniu centrum.</span><span class="sxs-lookup"><span data-stu-id="d228b-122">Every hub method call is executed on a new hub instance.</span></span>
> * <span data-ttu-id="d228b-123">Używane `await` podczas wywoływania metod asynchronicznych, które są zależne od utrzymywania aktywności centrum.</span><span class="sxs-lookup"><span data-stu-id="d228b-123">Use `await` when calling asynchronous methods that depend on the hub staying alive.</span></span> <span data-ttu-id="d228b-124">Na przykład metoda, taka jak `Clients.All.SendAsync(...)` może zakończyć się niepowodzeniem, jeśli jest wywoływana bez `await` i zakończy się przed `SendAsync` zakończeniem.</span><span class="sxs-lookup"><span data-stu-id="d228b-124">For example, a method such as `Clients.All.SendAsync(...)` can fail if it's called without `await` and the hub method completes before `SendAsync` finishes.</span></span>

## <a name="the-context-object"></a><span data-ttu-id="d228b-125">Obiekt kontekstu</span><span class="sxs-lookup"><span data-stu-id="d228b-125">The Context object</span></span>

<span data-ttu-id="d228b-126">`Hub`Klasa ma `Context` Właściwość, która zawiera następujące właściwości z informacjami o połączeniu:</span><span class="sxs-lookup"><span data-stu-id="d228b-126">The `Hub` class has a `Context` property that contains the following properties with information about the connection:</span></span>

| <span data-ttu-id="d228b-127">Właściwość</span><span class="sxs-lookup"><span data-stu-id="d228b-127">Property</span></span> | <span data-ttu-id="d228b-128">Opis</span><span class="sxs-lookup"><span data-stu-id="d228b-128">Description</span></span> |
| ------ | ----------- |
| `ConnectionId` | <span data-ttu-id="d228b-129">Pobiera unikatowy identyfikator połączenia, przypisany przez SignalR .</span><span class="sxs-lookup"><span data-stu-id="d228b-129">Gets the unique ID for the connection, assigned by SignalR.</span></span> <span data-ttu-id="d228b-130">Istnieje jeden identyfikator połączenia dla każdego połączenia.</span><span class="sxs-lookup"><span data-stu-id="d228b-130">There is one connection ID for each connection.</span></span>|
| `UserIdentifier` | <span data-ttu-id="d228b-131">Pobiera [Identyfikator użytkownika](xref:signalr/groups).</span><span class="sxs-lookup"><span data-stu-id="d228b-131">Gets the [user identifier](xref:signalr/groups).</span></span> <span data-ttu-id="d228b-132">Domyślnie program SignalR używa programu `ClaimTypes.NameIdentifier` ze `ClaimsPrincipal` skojarzonego z połączeniem jako identyfikator użytkownika.</span><span class="sxs-lookup"><span data-stu-id="d228b-132">By default, SignalR uses the `ClaimTypes.NameIdentifier` from the `ClaimsPrincipal` associated with the connection as the user identifier.</span></span> |
| `User` | <span data-ttu-id="d228b-133">Pobiera `ClaimsPrincipal` skojarzone z bieżącym użytkownikiem.</span><span class="sxs-lookup"><span data-stu-id="d228b-133">Gets the `ClaimsPrincipal` associated with the current user.</span></span> |
| `Items` | <span data-ttu-id="d228b-134">Pobiera kolekcję klucz/wartość, której można użyć do udostępniania danych w zakresie tego połączenia.</span><span class="sxs-lookup"><span data-stu-id="d228b-134">Gets a key/value collection that can be used to share data within the scope of this connection.</span></span> <span data-ttu-id="d228b-135">Dane można przechowywać w tej kolekcji i będzie ona trwała dla połączenia między różnymi wywołaniami metody centrum.</span><span class="sxs-lookup"><span data-stu-id="d228b-135">Data can be stored in this collection and it will persist for the connection across different hub method invocations.</span></span> |
| `Features` | <span data-ttu-id="d228b-136">Pobiera kolekcję funkcji dostępnych w ramach połączenia.</span><span class="sxs-lookup"><span data-stu-id="d228b-136">Gets the collection of features available on the connection.</span></span> <span data-ttu-id="d228b-137">Na razie ta kolekcja nie jest wymagana w większości scenariuszy, więc nie jest jeszcze udokumentowana.</span><span class="sxs-lookup"><span data-stu-id="d228b-137">For now, this collection isn't needed in most scenarios, so it isn't documented in detail yet.</span></span> |
| `ConnectionAborted` | <span data-ttu-id="d228b-138">Pobiera `CancellationToken` powiadomienie, gdy połączenie zostanie przerwane.</span><span class="sxs-lookup"><span data-stu-id="d228b-138">Gets a `CancellationToken` that notifies when the connection is aborted.</span></span> |

<span data-ttu-id="d228b-139">`Hub.Context` zawiera również następujące metody:</span><span class="sxs-lookup"><span data-stu-id="d228b-139">`Hub.Context` also contains the following methods:</span></span>

| <span data-ttu-id="d228b-140">Metoda</span><span class="sxs-lookup"><span data-stu-id="d228b-140">Method</span></span> | <span data-ttu-id="d228b-141">Opis</span><span class="sxs-lookup"><span data-stu-id="d228b-141">Description</span></span> |
| ------ | ----------- |
| `GetHttpContext` | <span data-ttu-id="d228b-142">Zwraca `HttpContext` dla połączenia lub `null` Jeśli połączenie nie jest skojarzone z żądaniem http.</span><span class="sxs-lookup"><span data-stu-id="d228b-142">Returns the `HttpContext` for the connection, or `null` if the connection is not associated with an HTTP request.</span></span> <span data-ttu-id="d228b-143">W przypadku połączeń HTTP można użyć tej metody, aby uzyskać informacje takie jak nagłówki HTTP i ciągi zapytań.</span><span class="sxs-lookup"><span data-stu-id="d228b-143">For HTTP connections, you can use this method to get information such as HTTP headers and query strings.</span></span> |
| `Abort` | <span data-ttu-id="d228b-144">Przerywa połączenie.</span><span class="sxs-lookup"><span data-stu-id="d228b-144">Aborts the connection.</span></span> |

## <a name="the-clients-object"></a><span data-ttu-id="d228b-145">Obiekt clients</span><span class="sxs-lookup"><span data-stu-id="d228b-145">The Clients object</span></span>

<span data-ttu-id="d228b-146">`Hub`Klasa ma `Clients` Właściwość, która zawiera następujące właściwości komunikacji między serwerem i klientem:</span><span class="sxs-lookup"><span data-stu-id="d228b-146">The `Hub` class has a `Clients` property that contains the following properties for communication between server and client:</span></span>

| <span data-ttu-id="d228b-147">Właściwość</span><span class="sxs-lookup"><span data-stu-id="d228b-147">Property</span></span> | <span data-ttu-id="d228b-148">Opis</span><span class="sxs-lookup"><span data-stu-id="d228b-148">Description</span></span> |
| ------ | ----------- |
| `All` | <span data-ttu-id="d228b-149">Wywołuje metodę na wszystkich połączonych klientach</span><span class="sxs-lookup"><span data-stu-id="d228b-149">Calls a method on all connected clients</span></span> |
| `Caller` | <span data-ttu-id="d228b-150">Wywołuje metodę na kliencie, który wywołał metodę Hub</span><span class="sxs-lookup"><span data-stu-id="d228b-150">Calls a method on the client that invoked the hub method</span></span> |
| `Others` | <span data-ttu-id="d228b-151">Wywołuje metodę na wszystkich połączonych klientach z wyjątkiem klienta, który wywołał metodę</span><span class="sxs-lookup"><span data-stu-id="d228b-151">Calls a method on all connected clients except the client that invoked the method</span></span> |

<span data-ttu-id="d228b-152">`Hub.Clients` zawiera również następujące metody:</span><span class="sxs-lookup"><span data-stu-id="d228b-152">`Hub.Clients` also contains the following methods:</span></span>

| <span data-ttu-id="d228b-153">Metoda</span><span class="sxs-lookup"><span data-stu-id="d228b-153">Method</span></span> | <span data-ttu-id="d228b-154">Opis</span><span class="sxs-lookup"><span data-stu-id="d228b-154">Description</span></span> |
| ------ | ----------- |
| `AllExcept` | <span data-ttu-id="d228b-155">Wywołuje metodę na wszystkich połączonych klientach z wyjątkiem określonych połączeń</span><span class="sxs-lookup"><span data-stu-id="d228b-155">Calls a method on all connected clients except for the specified connections</span></span> |
| `Client` | <span data-ttu-id="d228b-156">Wywołuje metodę na określonym podłączonym kliencie</span><span class="sxs-lookup"><span data-stu-id="d228b-156">Calls a method on a specific connected client</span></span> |
| `Clients` | <span data-ttu-id="d228b-157">Wywołuje metodę na określonych połączonych klientach</span><span class="sxs-lookup"><span data-stu-id="d228b-157">Calls a method on specific connected clients</span></span> |
| `Group` | <span data-ttu-id="d228b-158">Wywołuje metodę dla wszystkich połączeń w określonej grupie</span><span class="sxs-lookup"><span data-stu-id="d228b-158">Calls a method on all connections in the specified group</span></span>  |
| `GroupExcept` | <span data-ttu-id="d228b-159">Wywołuje metodę dla wszystkich połączeń w określonej grupie, z wyjątkiem określonych połączeń</span><span class="sxs-lookup"><span data-stu-id="d228b-159">Calls a method on all connections in the specified group, except the specified connections</span></span> |
| `Groups` | <span data-ttu-id="d228b-160">Wywołuje metodę dla wielu grup połączeń</span><span class="sxs-lookup"><span data-stu-id="d228b-160">Calls a method on multiple groups of connections</span></span>  |
| `OthersInGroup` | <span data-ttu-id="d228b-161">Wywołuje metodę w grupie połączeń z wyłączeniem klienta, który wywołał metodę Hub</span><span class="sxs-lookup"><span data-stu-id="d228b-161">Calls a method on a group of connections, excluding the client that invoked the hub method</span></span>  |
| `User` | <span data-ttu-id="d228b-162">Wywołuje metodę dla wszystkich połączeń skojarzonych z określonym użytkownikiem</span><span class="sxs-lookup"><span data-stu-id="d228b-162">Calls a method on all connections associated with a specific user</span></span> |
| `Users` | <span data-ttu-id="d228b-163">Wywołuje metodę dla wszystkich połączeń skojarzonych z określonymi użytkownikami</span><span class="sxs-lookup"><span data-stu-id="d228b-163">Calls a method on all connections associated with the specified users</span></span> |

<span data-ttu-id="d228b-164">Każda właściwość lub metoda w powyższych tabelach zwraca obiekt z `SendAsync` metodą.</span><span class="sxs-lookup"><span data-stu-id="d228b-164">Each property or method in the preceding tables returns an object with a `SendAsync` method.</span></span> <span data-ttu-id="d228b-165">`SendAsync`Metoda umożliwia podanie nazwy i parametrów metody klienta do wywołania.</span><span class="sxs-lookup"><span data-stu-id="d228b-165">The `SendAsync` method allows you to supply the name and parameters of the client method to call.</span></span>

## <a name="send-messages-to-clients"></a><span data-ttu-id="d228b-166">Wysyłanie komunikatów do klientów</span><span class="sxs-lookup"><span data-stu-id="d228b-166">Send messages to clients</span></span>

<span data-ttu-id="d228b-167">Aby wykonać wywołania do określonych klientów, użyj właściwości `Clients` obiektu.</span><span class="sxs-lookup"><span data-stu-id="d228b-167">To make calls to specific clients, use the properties of the `Clients` object.</span></span> <span data-ttu-id="d228b-168">W poniższym przykładzie istnieją trzy metody centralne:</span><span class="sxs-lookup"><span data-stu-id="d228b-168">In the following example, there are three Hub methods:</span></span>

* <span data-ttu-id="d228b-169">`SendMessage` wysyła komunikat do wszystkich połączonych klientów przy użyciu programu `Clients.All` .</span><span class="sxs-lookup"><span data-stu-id="d228b-169">`SendMessage` sends a message to all connected clients, using `Clients.All`.</span></span>
* <span data-ttu-id="d228b-170">`SendMessageToCaller` wysyła komunikat z powrotem do obiektu wywołującego za pomocą polecenia `Clients.Caller` .</span><span class="sxs-lookup"><span data-stu-id="d228b-170">`SendMessageToCaller` sends a message back to the caller, using `Clients.Caller`.</span></span>
* <span data-ttu-id="d228b-171">`SendMessageToGroups` wysyła komunikat do wszystkich klientów w `SignalR Users` grupie.</span><span class="sxs-lookup"><span data-stu-id="d228b-171">`SendMessageToGroups` sends a message to all clients in the `SignalR Users` group.</span></span>

[!code-csharp[Send messages](hubs/sample/hubs/chathub.cs?name=HubMethods)]

## <a name="strongly-typed-hubs"></a><span data-ttu-id="d228b-172">Centra o jednoznacznie określonym typie</span><span class="sxs-lookup"><span data-stu-id="d228b-172">Strongly typed hubs</span></span>

<span data-ttu-id="d228b-173">Wadą używania jest to `SendAsync` , że opiera się na ciągu Magic, aby określić metodę klienta, która ma zostać wywołana.</span><span class="sxs-lookup"><span data-stu-id="d228b-173">A drawback of using `SendAsync` is that it relies on a magic string to specify the client method to be called.</span></span> <span data-ttu-id="d228b-174">Spowoduje to pozostawienie kodu otwartego na błędy środowiska uruchomieniowego, jeśli nazwa metody jest błędnie wpisana lub nie została podana w kliencie.</span><span class="sxs-lookup"><span data-stu-id="d228b-174">This leaves code open to runtime errors if the method name is misspelled or missing from the client.</span></span>

<span data-ttu-id="d228b-175">Alternatywą dla użycia `SendAsync` jest silna wartość typu `Hub` with <xref:Microsoft.AspNetCore.SignalR.Hub%601> .</span><span class="sxs-lookup"><span data-stu-id="d228b-175">An alternative to using `SendAsync` is to strongly type the `Hub` with <xref:Microsoft.AspNetCore.SignalR.Hub%601>.</span></span> <span data-ttu-id="d228b-176">W poniższym przykładzie `ChatHub` metody klienta zostały wyodrębnione do interfejsu o nazwie `IChatClient` .</span><span class="sxs-lookup"><span data-stu-id="d228b-176">In the following example, the `ChatHub` client methods have been extracted out into an interface called `IChatClient`.</span></span>

[!code-csharp[Interface for IChatClient](hubs/sample/hubs/ichatclient.cs?name=snippet_IChatClient)]

<span data-ttu-id="d228b-177">Ten interfejs może służyć do refaktoryzacji poprzedniego `ChatHub` przykładu.</span><span class="sxs-lookup"><span data-stu-id="d228b-177">This interface can be used to refactor the preceding `ChatHub` example.</span></span>

[!code-csharp[Strongly typed ChatHub](hubs/sample/hubs/StronglyTypedChatHub.cs?range=8-18,36)]

<span data-ttu-id="d228b-178">Użycie `Hub<IChatClient>` umożliwia sprawdzenie w czasie kompilacji metod klienta.</span><span class="sxs-lookup"><span data-stu-id="d228b-178">Using `Hub<IChatClient>` enables compile-time checking of the client methods.</span></span> <span data-ttu-id="d228b-179">Zapobiega to problemom spowodowanym użyciem ciągów Magic, ponieważ `Hub<T>` może zapewnić tylko dostęp do metod zdefiniowanych w interfejsie.</span><span class="sxs-lookup"><span data-stu-id="d228b-179">This prevents issues caused by using magic strings, since `Hub<T>` can only provide access to the methods defined in the interface.</span></span>

<span data-ttu-id="d228b-180">Użycie silnie określonego typu `Hub<T>` wyłącza możliwość użycia `SendAsync` .</span><span class="sxs-lookup"><span data-stu-id="d228b-180">Using a strongly typed `Hub<T>` disables the ability to use `SendAsync`.</span></span> <span data-ttu-id="d228b-181">Wszelkie metody zdefiniowane w interfejsie mogą być nadal zdefiniowane jako asynchroniczne.</span><span class="sxs-lookup"><span data-stu-id="d228b-181">Any methods defined on the interface can still be defined as asynchronous.</span></span> <span data-ttu-id="d228b-182">W rzeczywistości każda z tych metod powinna zwrócić `Task` .</span><span class="sxs-lookup"><span data-stu-id="d228b-182">In fact, each of these methods should return a `Task`.</span></span> <span data-ttu-id="d228b-183">Ponieważ jest to interfejs, nie używaj `async` słowa kluczowego.</span><span class="sxs-lookup"><span data-stu-id="d228b-183">Since it's an interface, don't use the `async` keyword.</span></span> <span data-ttu-id="d228b-184">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="d228b-184">For example:</span></span>

```csharp
public interface IClient
{
    Task ClientMethod();
}
```

> [!NOTE]
> <span data-ttu-id="d228b-185">`Async`Sufiks nie jest usuwany z nazwy metody.</span><span class="sxs-lookup"><span data-stu-id="d228b-185">The `Async` suffix isn't stripped from the method name.</span></span> <span data-ttu-id="d228b-186">Chyba że metoda klienta nie jest zdefiniowana z `.on('MyMethodAsync')` , nie należy używać `MyMethodAsync` jako nazwy.</span><span class="sxs-lookup"><span data-stu-id="d228b-186">Unless your client method is defined with `.on('MyMethodAsync')`, you shouldn't use `MyMethodAsync` as a name.</span></span>

## <a name="change-the-name-of-a-hub-method"></a><span data-ttu-id="d228b-187">Zmień nazwę metody centrum</span><span class="sxs-lookup"><span data-stu-id="d228b-187">Change the name of a hub method</span></span>

<span data-ttu-id="d228b-188">Domyślnie nazwa metody centrum serwera jest nazwą metody .NET.</span><span class="sxs-lookup"><span data-stu-id="d228b-188">By default, a server hub method name is the name of the .NET method.</span></span> <span data-ttu-id="d228b-189">Można jednak użyć atrybutu [HubMethodName](xref:Microsoft.AspNetCore.SignalR.HubMethodNameAttribute) , aby zmienić to ustawienie domyślne, i ręcznie określić nazwę dla metody.</span><span class="sxs-lookup"><span data-stu-id="d228b-189">However, you can use the [HubMethodName](xref:Microsoft.AspNetCore.SignalR.HubMethodNameAttribute) attribute to change this default and manually specify a name for the method.</span></span> <span data-ttu-id="d228b-190">Klient powinien używać tej nazwy zamiast nazwy metody .NET podczas wywoływania metody.</span><span class="sxs-lookup"><span data-stu-id="d228b-190">The client should use this name, instead of the .NET method name, when invoking the method.</span></span>

[!code-csharp[HubMethodName attribute](hubs/sample/hubs/chathub.cs?name=HubMethodName&highlight=1)]

## <a name="handle-events-for-a-connection"></a><span data-ttu-id="d228b-191">Obsługa zdarzeń dla połączenia</span><span class="sxs-lookup"><span data-stu-id="d228b-191">Handle events for a connection</span></span>

<span data-ttu-id="d228b-192">SignalRInterfejs API centrów zapewnia `OnConnectedAsync` `OnDisconnectedAsync` metody wirtualne do zarządzania połączeniami i ich śledzenia.</span><span class="sxs-lookup"><span data-stu-id="d228b-192">The SignalR Hubs API provides the `OnConnectedAsync` and `OnDisconnectedAsync` virtual methods to manage and track connections.</span></span> <span data-ttu-id="d228b-193">Zastąp `OnConnectedAsync` metodę wirtualną, aby wykonać akcje, gdy klient łączy się z centrum, na przykład dodając go do grupy.</span><span class="sxs-lookup"><span data-stu-id="d228b-193">Override the `OnConnectedAsync` virtual method to perform actions when a client connects to the Hub, such as adding it to a group.</span></span>

[!code-csharp[Handle connection](hubs/sample/hubs/chathub.cs?name=OnConnectedAsync)]

<span data-ttu-id="d228b-194">Zastąp `OnDisconnectedAsync` metodę wirtualną, aby wykonać akcje po rozłączeniu klienta.</span><span class="sxs-lookup"><span data-stu-id="d228b-194">Override the `OnDisconnectedAsync` virtual method to perform actions when a client disconnects.</span></span> <span data-ttu-id="d228b-195">Jeśli klient odłączy się celowo ( `connection.stop()` na przykład przez wywołanie), `exception` parametr będzie `null` .</span><span class="sxs-lookup"><span data-stu-id="d228b-195">If the client disconnects intentionally (by calling `connection.stop()`, for example), the `exception` parameter will be `null`.</span></span> <span data-ttu-id="d228b-196">Jeśli jednak klient zostanie rozłączony z powodu błędu (takiego jak awaria sieci), `exception` parametr będzie zawierać wyjątek opisujący błąd.</span><span class="sxs-lookup"><span data-stu-id="d228b-196">However, if the client is disconnected due to an error (such as a network failure), the `exception` parameter will contain an exception describing the failure.</span></span>

[!code-csharp[Handle disconnection](hubs/sample/hubs/chathub.cs?name=OnDisconnectedAsync)]

[!INCLUDE[](~/includes/connectionid-signalr.md)]

## <a name="handle-errors"></a><span data-ttu-id="d228b-197">Obsługa błędów</span><span class="sxs-lookup"><span data-stu-id="d228b-197">Handle errors</span></span>

<span data-ttu-id="d228b-198">Wyjątki zgłoszone w metodach centrum są wysyłane do klienta, który wywołał metodę.</span><span class="sxs-lookup"><span data-stu-id="d228b-198">Exceptions thrown in your hub methods are sent to the client that invoked the method.</span></span> <span data-ttu-id="d228b-199">W kliencie JavaScript `invoke` Metoda zwraca [obietnicę języka JavaScript](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises).</span><span class="sxs-lookup"><span data-stu-id="d228b-199">On the JavaScript client, the `invoke` method returns a [JavaScript Promise](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises).</span></span> <span data-ttu-id="d228b-200">Gdy klient otrzymuje błąd z obsługą dołączoną do obietnicy przy użyciu `catch` , jest wywoływana i przenoszona jako obiekt JavaScript `Error` .</span><span class="sxs-lookup"><span data-stu-id="d228b-200">When the client receives an error with a handler attached to the promise using `catch`, it's invoked and passed as a JavaScript `Error` object.</span></span>

[!code-javascript[Error](hubs/sample/wwwroot/js/chat.js?range=23)]

<span data-ttu-id="d228b-201">Jeśli centrum zgłosi wyjątek, połączenia nie są zamknięte.</span><span class="sxs-lookup"><span data-stu-id="d228b-201">If your Hub throws an exception, connections aren't closed.</span></span> <span data-ttu-id="d228b-202">Domyślnie program SignalR zwraca ogólny komunikat o błędzie do klienta.</span><span class="sxs-lookup"><span data-stu-id="d228b-202">By default, SignalR returns a generic error message to the client.</span></span> <span data-ttu-id="d228b-203">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="d228b-203">For example:</span></span>

```
Microsoft.AspNetCore.SignalR.HubException: An unexpected error occurred invoking 'MethodName' on the server.
```

<span data-ttu-id="d228b-204">Nieoczekiwane wyjątki często zawierają informacje poufne, takie jak nazwa serwera bazy danych w wyjątku wyzwalanym w przypadku niepowodzenia połączenia z bazą danych.</span><span class="sxs-lookup"><span data-stu-id="d228b-204">Unexpected exceptions often contain sensitive information, such as the name of a database server in an exception triggered when the database connection fails.</span></span> <span data-ttu-id="d228b-205">SignalR Domyślnie nie uwidacznia tych szczegółowych komunikatów o błędach jako miary zabezpieczeń.</span><span class="sxs-lookup"><span data-stu-id="d228b-205">SignalR doesn't expose these detailed error messages by default as a security measure.</span></span> <span data-ttu-id="d228b-206">Aby uzyskać więcej informacji o tym, dlaczego szczegóły wyjątku są pomijane, zobacz artykuł dotyczący [zagadnień dotyczących zabezpieczeń](xref:signalr/security#exceptions) .</span><span class="sxs-lookup"><span data-stu-id="d228b-206">See the [Security considerations article](xref:signalr/security#exceptions) for more information on why exception details are suppressed.</span></span>

<span data-ttu-id="d228b-207">Jeśli *masz wyjątkowe warunki, które chcesz* propagować do klienta, możesz użyć `HubException` klasy.</span><span class="sxs-lookup"><span data-stu-id="d228b-207">If you have an exceptional condition you *do* want to propagate to the client, you can use the `HubException` class.</span></span> <span data-ttu-id="d228b-208">W przypadku zgłoszenia `HubException` z poziomu metody centrum program SignalR **will** wyśle do klienta cały komunikat, który nie został zmodyfikowany.</span><span class="sxs-lookup"><span data-stu-id="d228b-208">If you throw a `HubException` from your hub method, SignalR **will** send the entire message to the client, unmodified.</span></span>

[!code-csharp[ThrowHubException](hubs/sample/hubs/chathub.cs?name=ThrowHubException&highlight=3)]

> [!NOTE]
> <span data-ttu-id="d228b-209">SignalR tylko wysyła `Message` Właściwość wyjątku do klienta.</span><span class="sxs-lookup"><span data-stu-id="d228b-209">SignalR only sends the `Message` property of the exception to the client.</span></span> <span data-ttu-id="d228b-210">Ślad stosu i inne właściwości tego wyjątku nie są dostępne dla klienta.</span><span class="sxs-lookup"><span data-stu-id="d228b-210">The stack trace and other properties on the exception aren't available to the client.</span></span>

## <a name="related-resources"></a><span data-ttu-id="d228b-211">Powiązane zasoby</span><span class="sxs-lookup"><span data-stu-id="d228b-211">Related resources</span></span>

* [<span data-ttu-id="d228b-212">Wprowadzenie do ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="d228b-212">Intro to ASP.NET Core SignalR</span></span>](xref:signalr/introduction)
* [<span data-ttu-id="d228b-213">Klient JavaScript</span><span class="sxs-lookup"><span data-stu-id="d228b-213">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="d228b-214">Publikowanie na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="d228b-214">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
