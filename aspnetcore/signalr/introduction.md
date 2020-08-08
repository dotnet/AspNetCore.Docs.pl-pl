---
title: Wprowadzenie do ASP.NET CoreSignalR
author: bradygaster
description: Dowiedz się, w jaki sposób SignalR biblioteka ASP.NET Core upraszcza Dodawanie funkcji w czasie rzeczywistym do aplikacji.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/27/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/introduction
ms.openlocfilehash: 6f3cec83c9af5ec6e820db4a15061eddac613f36
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022176"
---
# <a name="introduction-to-aspnet-core-no-locsignalr"></a><span data-ttu-id="90953-103">Wprowadzenie do ASP.NET CoreSignalR</span><span class="sxs-lookup"><span data-stu-id="90953-103">Introduction to ASP.NET Core SignalR</span></span>

## <a name="what-is-no-locsignalr"></a><span data-ttu-id="90953-104">Co to jest SignalR ?</span><span class="sxs-lookup"><span data-stu-id="90953-104">What is SignalR?</span></span>

<span data-ttu-id="90953-105">ASP.NET Core SignalR to Biblioteka open source, która upraszcza Dodawanie funkcji sieci Web w czasie rzeczywistym do aplikacji.</span><span class="sxs-lookup"><span data-stu-id="90953-105">ASP.NET Core SignalR is an open-source library that simplifies adding real-time web functionality to apps.</span></span> <span data-ttu-id="90953-106">Funkcja sieci Web w czasie rzeczywistym umożliwia serwerowi natychmiastowe wypychanie zawartości do klientów.</span><span class="sxs-lookup"><span data-stu-id="90953-106">Real-time web functionality enables server-side code to push content to clients instantly.</span></span>

<span data-ttu-id="90953-107">Dobre kandydaci dla SignalR :</span><span class="sxs-lookup"><span data-stu-id="90953-107">Good candidates for SignalR:</span></span>

* <span data-ttu-id="90953-108">Aplikacje, które wymagają częstych aktualizacji z serwera.</span><span class="sxs-lookup"><span data-stu-id="90953-108">Apps that require high frequency updates from the server.</span></span> <span data-ttu-id="90953-109">Są to na przykład gry i aplikacje do obsługi sieci społecznościowych, głosowania, aukcji, map i nawigacji GPS.</span><span class="sxs-lookup"><span data-stu-id="90953-109">Examples are gaming, social networks, voting, auction, maps, and GPS apps.</span></span>
* <span data-ttu-id="90953-110">Pulpity nawigacyjne i aplikacje do monitorowania.</span><span class="sxs-lookup"><span data-stu-id="90953-110">Dashboards and monitoring apps.</span></span> <span data-ttu-id="90953-111">Przykładami mogą być firmowe pulpity nawigacyjne, błyskawiczne powiadomienia o sprzedaży lub alerty dotyczące podróży.</span><span class="sxs-lookup"><span data-stu-id="90953-111">Examples include company dashboards, instant sales updates, or travel alerts.</span></span>
* <span data-ttu-id="90953-112">Aplikacje do współpracy.</span><span class="sxs-lookup"><span data-stu-id="90953-112">Collaborative apps.</span></span> <span data-ttu-id="90953-113">Przykładami aplikacji do współpracy są aplikacje tablicy i oprogramowanie do spotkań zespołowych.</span><span class="sxs-lookup"><span data-stu-id="90953-113">Whiteboard apps and team meeting software are examples of collaborative apps.</span></span>
* <span data-ttu-id="90953-114">Aplikacje, które wymagają powiadomień.</span><span class="sxs-lookup"><span data-stu-id="90953-114">Apps that require notifications.</span></span> <span data-ttu-id="90953-115">Wiele aplikacji korzysta z powiadomień, w tym gry i aplikacje do obsługi sieci społecznościowych, poczty e-mail, czatów i alertów dotyczących podróży.</span><span class="sxs-lookup"><span data-stu-id="90953-115">Social networks, email, chat, games, travel alerts, and many other apps use notifications.</span></span>

<span data-ttu-id="90953-116">SignalRudostępnia interfejs API służący do tworzenia [zdalnych wywołań procedur serwer-klient (RPC)](https://wikipedia.org/wiki/Remote_procedure_call).</span><span class="sxs-lookup"><span data-stu-id="90953-116">SignalR provides an API for creating server-to-client [remote procedure calls (RPC)](https://wikipedia.org/wiki/Remote_procedure_call).</span></span> <span data-ttu-id="90953-117">Wywołania RPC wywołują funkcje JavaScript na klientach z kodu .NET Core po stronie serwera.</span><span class="sxs-lookup"><span data-stu-id="90953-117">The RPCs call JavaScript functions on clients from server-side .NET Core code.</span></span>

<span data-ttu-id="90953-118">Poniżej przedstawiono niektóre funkcje programu SignalR dla programu ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="90953-118">Here are some features of SignalR for ASP.NET Core:</span></span>

* <span data-ttu-id="90953-119">Obsługuje automatyczne zarządzanie połączeniami.</span><span class="sxs-lookup"><span data-stu-id="90953-119">Handles connection management automatically.</span></span>
* <span data-ttu-id="90953-120">Wysyła komunikaty do wszystkich połączonych klientów jednocześnie.</span><span class="sxs-lookup"><span data-stu-id="90953-120">Sends messages to all connected clients simultaneously.</span></span> <span data-ttu-id="90953-121">Na przykład pokój rozmów.</span><span class="sxs-lookup"><span data-stu-id="90953-121">For example, a chat room.</span></span>
* <span data-ttu-id="90953-122">Wysyła komunikaty do określonych klientów lub grup klientów.</span><span class="sxs-lookup"><span data-stu-id="90953-122">Sends messages to specific clients or groups of clients.</span></span>
* <span data-ttu-id="90953-123">Skaluje się do obsługi zwiększania ruchu.</span><span class="sxs-lookup"><span data-stu-id="90953-123">Scales to handle increasing traffic.</span></span>

<span data-ttu-id="90953-124">Źródło jest hostowane w [ SignalR repozytorium w serwisie GitHub](https://github.com/dotnet/AspNetCore/tree/master/src/SignalR).</span><span class="sxs-lookup"><span data-stu-id="90953-124">The source is hosted in a [SignalR repository on GitHub](https://github.com/dotnet/AspNetCore/tree/master/src/SignalR).</span></span>

## <a name="transports"></a><span data-ttu-id="90953-125">Transporty</span><span class="sxs-lookup"><span data-stu-id="90953-125">Transports</span></span>

<span data-ttu-id="90953-126">SignalRobsługuje następujące techniki obsługi komunikacji w czasie rzeczywistym (w kolejności bezpiecznego powrotu):</span><span class="sxs-lookup"><span data-stu-id="90953-126">SignalR supports the following techniques for handling real-time communication (in order of graceful fallback):</span></span>

* [<span data-ttu-id="90953-127">Protokoły WebSocket</span><span class="sxs-lookup"><span data-stu-id="90953-127">WebSockets</span></span>](https://tools.ietf.org/html/rfc7118)
* <span data-ttu-id="90953-128">Zdarzenia wysłane przez serwer</span><span class="sxs-lookup"><span data-stu-id="90953-128">Server-Sent Events</span></span>
* <span data-ttu-id="90953-129">Długotrwałe sondowanie</span><span class="sxs-lookup"><span data-stu-id="90953-129">Long Polling</span></span>

<span data-ttu-id="90953-130">SignalRautomatycznie wybiera najlepszą metodę transportu, która znajduje się w możliwościach serwera i klienta.</span><span class="sxs-lookup"><span data-stu-id="90953-130">SignalR automatically chooses the best transport method that is within the capabilities of the server and client.</span></span>

## <a name="hubs"></a><span data-ttu-id="90953-131">Centra</span><span class="sxs-lookup"><span data-stu-id="90953-131">Hubs</span></span>

<span data-ttu-id="90953-132">SignalRużywa *centrów* do komunikacji między klientami a serwerami.</span><span class="sxs-lookup"><span data-stu-id="90953-132">SignalR uses *hubs* to communicate between clients and servers.</span></span>

<span data-ttu-id="90953-133">Koncentrator jest potokiem wysokiego poziomu, który umożliwia klientowi i serwerowi wywoływanie metod ze sobą.</span><span class="sxs-lookup"><span data-stu-id="90953-133">A hub is a high-level pipeline that allows a client and server to call methods on each other.</span></span> <span data-ttu-id="90953-134">SignalRobsługuje automatyczne wysyłanie między granicami maszyn, umożliwiając klientom wywoływanie metod na serwerze i odwrotnie.</span><span class="sxs-lookup"><span data-stu-id="90953-134">SignalR handles the dispatching across machine boundaries automatically, allowing clients to call methods on the server and vice versa.</span></span> <span data-ttu-id="90953-135">Parametry z jednoznacznie określonymi typami można przekazać do metod, które umożliwiają powiązanie modelu.</span><span class="sxs-lookup"><span data-stu-id="90953-135">You can pass strongly-typed parameters to methods, which enables model binding.</span></span> <span data-ttu-id="90953-136">SignalRProgram udostępnia dwa wbudowane protokoły centrum: protokół tekstowy oparty na formacie JSON i protokół binarny oparty na [MessagePack](https://msgpack.org/).</span><span class="sxs-lookup"><span data-stu-id="90953-136">SignalR provides two built-in hub protocols: a text protocol based on JSON and a binary protocol based on [MessagePack](https://msgpack.org/).</span></span>  <span data-ttu-id="90953-137">MessagePack zazwyczaj tworzy mniejsze komunikaty w porównaniu z formatem JSON.</span><span class="sxs-lookup"><span data-stu-id="90953-137">MessagePack generally creates smaller messages compared to JSON.</span></span> <span data-ttu-id="90953-138">Starsze przeglądarki muszą obsługiwać [XHR Level 2](https://caniuse.com/#feat=xhr2) , aby zapewnić obsługę protokołu MessagePack.</span><span class="sxs-lookup"><span data-stu-id="90953-138">Older browsers must support [XHR level 2](https://caniuse.com/#feat=xhr2) to provide MessagePack protocol support.</span></span>

<span data-ttu-id="90953-139">Centra wywołują kod po stronie klienta, wysyłając komunikaty zawierające nazwę i parametry metody po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="90953-139">Hubs call client-side code by sending messages that contain the name and parameters of the client-side method.</span></span> <span data-ttu-id="90953-140">Obiekty wysyłane jako parametry metody są deserializowane przy użyciu skonfigurowanego protokołu.</span><span class="sxs-lookup"><span data-stu-id="90953-140">Objects sent as method parameters are deserialized using the configured protocol.</span></span> <span data-ttu-id="90953-141">Klient próbuje dopasować nazwę do metody w kodzie po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="90953-141">The client tries to match the name to a method in the client-side code.</span></span> <span data-ttu-id="90953-142">Gdy klient znajdzie dopasowanie, wywołuje metodę i przekazuje do niej dane parametrów, które są deserializowane.</span><span class="sxs-lookup"><span data-stu-id="90953-142">When the client finds a match, it calls the method and passes to it the deserialized parameter data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="90953-143">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="90953-143">Additional resources</span></span>

* [<span data-ttu-id="90953-144">Wprowadzenie SignalR do ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="90953-144">Get started with SignalR for ASP.NET Core</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="90953-145">Obsługiwane platformy</span><span class="sxs-lookup"><span data-stu-id="90953-145">Supported Platforms</span></span>](xref:signalr/supported-platforms)
* [<span data-ttu-id="90953-146">Centra</span><span class="sxs-lookup"><span data-stu-id="90953-146">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="90953-147">Klient JavaScript</span><span class="sxs-lookup"><span data-stu-id="90953-147">JavaScript client</span></span>](xref:signalr/javascript-client)
