---
title: Co nowego w ASP.NET Core 3,0
author: rick-anderson
description: Dowiedz się więcej o nowych funkcjach w ASP.NET Core 3,0.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: aspnetcore-3.0
ms.openlocfilehash: aa0b2b51632cff729449018f9823c38cc26ce612
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774096"
---
# <a name="whats-new-in-aspnet-core-30"></a><span data-ttu-id="9caf9-103">Co nowego w ASP.NET Core 3,0</span><span class="sxs-lookup"><span data-stu-id="9caf9-103">What's new in ASP.NET Core 3.0</span></span>

<span data-ttu-id="9caf9-104">W tym artykule przedstawiono najbardziej znaczące zmiany w ASP.NET Core 3,0 z linkami do odpowiedniej dokumentacji.</span><span class="sxs-lookup"><span data-stu-id="9caf9-104">This article highlights the most significant changes in ASP.NET Core 3.0 with links to relevant documentation.</span></span>

## Blazor

Blazor<span data-ttu-id="9caf9-105">jest nową strukturą w ASP.NET Core tworzenia interakcyjnego interfejsu użytkownika sieci Web po stronie klienta przy użyciu platformy .NET:</span><span class="sxs-lookup"><span data-stu-id="9caf9-105"> is a new framework in ASP.NET Core for building interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="9caf9-106">Twórz rozbudowane interaktywne interfejsów użytkownika przy użyciu języka C# zamiast języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="9caf9-106">Create rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="9caf9-107">Udostępnianie logiki aplikacji po stronie serwera i klienta zapisaną w środowisku .NET.</span><span class="sxs-lookup"><span data-stu-id="9caf9-107">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="9caf9-108">Renderuj interfejs użytkownika jako HTML i CSS, aby obsługiwał szeroką przeglądarkę, w tym przeglądarki dla urządzeń przenośnych.</span><span class="sxs-lookup"><span data-stu-id="9caf9-108">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>

Blazor<span data-ttu-id="9caf9-109">scenariusze obsługiwane przez platformę:</span><span class="sxs-lookup"><span data-stu-id="9caf9-109"> framework supported scenarios:</span></span>

* <span data-ttu-id="9caf9-110">Składniki interfejsu użytkownika wielokrotnegoRazor użytku (składniki)</span><span class="sxs-lookup"><span data-stu-id="9caf9-110">Reusable UI components (Razor components)</span></span>
* <span data-ttu-id="9caf9-111">Routing po stronie klienta</span><span class="sxs-lookup"><span data-stu-id="9caf9-111">Client-side routing</span></span>
* <span data-ttu-id="9caf9-112">Układy składników</span><span class="sxs-lookup"><span data-stu-id="9caf9-112">Component layouts</span></span>
* <span data-ttu-id="9caf9-113">Obsługa iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="9caf9-113">Support for dependency injection</span></span>
* <span data-ttu-id="9caf9-114">Formularze i walidacja</span><span class="sxs-lookup"><span data-stu-id="9caf9-114">Forms and validation</span></span>
* <span data-ttu-id="9caf9-115">Kompiluj biblioteki składników przy Razor użyciu bibliotek klas</span><span class="sxs-lookup"><span data-stu-id="9caf9-115">Build component libraries with Razor class libraries</span></span>
* <span data-ttu-id="9caf9-116">Międzyoperacyjność w języku JavaScript</span><span class="sxs-lookup"><span data-stu-id="9caf9-116">JavaScript interop</span></span>

<span data-ttu-id="9caf9-117">Aby uzyskać więcej informacji, zobacz <xref:blazor/index>.</span><span class="sxs-lookup"><span data-stu-id="9caf9-117">For more information, see <xref:blazor/index>.</span></span>

### <a name="blazor-server"></a>Blazor<span data-ttu-id="9caf9-118">Server</span><span class="sxs-lookup"><span data-stu-id="9caf9-118"> Server</span></span>

Blazor<span data-ttu-id="9caf9-119">oddziela logikę renderowania składników od sposobu stosowania aktualizacji interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="9caf9-119"> decouples component rendering logic from how UI updates are applied.</span></span> Blazor<span data-ttu-id="9caf9-120">Serwer zapewnia obsługę składników hostingu Razor na serwerze w aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9caf9-120"> Server provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="9caf9-121">Aktualizacje interfejsu użytkownika są obsługiwane przez SignalR połączenie.</span><span class="sxs-lookup"><span data-stu-id="9caf9-121">UI updates are handled over a SignalR connection.</span></span> Blazor<span data-ttu-id="9caf9-122">Serwer jest obsługiwany w ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="9caf9-122"> Server is supported in ASP.NET Core 3.0.</span></span>

### <a name="blazor-webassembly-preview"></a>Blazor<span data-ttu-id="9caf9-123">Webassembly (wersja zapoznawcza)</span><span class="sxs-lookup"><span data-stu-id="9caf9-123"> WebAssembly (Preview)</span></span>

Blazor<span data-ttu-id="9caf9-124">Aplikacje można również uruchamiać bezpośrednio w przeglądarce przy użyciu środowiska uruchomieniowego .NET opartego na zestawie.</span><span class="sxs-lookup"><span data-stu-id="9caf9-124"> apps can also be run directly in the browser using a WebAssembly-based .NET runtime.</span></span> Blazor<span data-ttu-id="9caf9-125">Zestaw webassembly jest w wersji zapoznawczej i *nie* jest obsługiwany w ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="9caf9-125"> WebAssembly is in preview and *not* supported in ASP.NET Core 3.0.</span></span> Blazor<span data-ttu-id="9caf9-126">Zestaw webassembly będzie obsługiwany w przyszłej wersji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="9caf9-126"> WebAssembly will be supported in a future release of ASP.NET Core.</span></span>

### <a name="razor-components"></a>Razor<span data-ttu-id="9caf9-127">komponentów</span><span class="sxs-lookup"><span data-stu-id="9caf9-127"> components</span></span>

Blazor<span data-ttu-id="9caf9-128">aplikacje są zbudowane ze składników.</span><span class="sxs-lookup"><span data-stu-id="9caf9-128"> apps are built from components.</span></span> <span data-ttu-id="9caf9-129">Składniki to samodzielne fragmenty interfejsu użytkownika (UI), takie jak strona, okno dialogowe lub formularz.</span><span class="sxs-lookup"><span data-stu-id="9caf9-129">Components are self-contained chunks of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="9caf9-130">Składniki to normalne klasy .NET, które definiują logikę renderowania interfejsu użytkownika i obsługę zdarzeń po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="9caf9-130">Components are normal .NET classes that define UI rendering logic and client-side event handlers.</span></span> <span data-ttu-id="9caf9-131">Możesz tworzyć rozbudowane interaktywne aplikacje sieci Web bez języka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="9caf9-131">You can create rich interactive web apps without JavaScript.</span></span>

<span data-ttu-id="9caf9-132">Składniki w Blazor programie są zwykle tworzone przy Razor użyciu składni, naturalnej mieszanki języka HTML i języka C#.</span><span class="sxs-lookup"><span data-stu-id="9caf9-132">Components in Blazor are typically authored using Razor syntax, a natural blend of HTML and C#.</span></span> Razor<span data-ttu-id="9caf9-133">składniki są podobne do Razor stron i widoków MVC w tym, że oba Razorsą używane.</span><span class="sxs-lookup"><span data-stu-id="9caf9-133"> components are similar to Razor Pages and MVC views in that they both use Razor.</span></span> <span data-ttu-id="9caf9-134">W przeciwieństwie do stron i widoków, które są oparte na modelu odpowiedzi na żądanie, składniki są używane do obsługi kompozycji interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="9caf9-134">Unlike pages and views, which are based on a request-response model, components are used specifically for handling UI composition.</span></span>

## <a name="grpc"></a><span data-ttu-id="9caf9-135">gRPC</span><span class="sxs-lookup"><span data-stu-id="9caf9-135">gRPC</span></span>

<span data-ttu-id="9caf9-136">[gRPC](https://grpc.io/):</span><span class="sxs-lookup"><span data-stu-id="9caf9-136">[gRPC](https://grpc.io/):</span></span>

* <span data-ttu-id="9caf9-137">To popularne środowisko RPC o wysokiej wydajności (zdalne wywołanie procedury).</span><span class="sxs-lookup"><span data-stu-id="9caf9-137">Is a popular, high-performance RPC (remote procedure call) framework.</span></span>
* <span data-ttu-id="9caf9-138">Oferuje ceniona kontrakt do programowania interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="9caf9-138">Offers an opinionated contract-first approach to API development.</span></span>
* <span data-ttu-id="9caf9-139">Używa nowoczesnych technologii, takich jak:</span><span class="sxs-lookup"><span data-stu-id="9caf9-139">Uses modern technologies such as:</span></span>

  * <span data-ttu-id="9caf9-140">HTTP/2 do transportu.</span><span class="sxs-lookup"><span data-stu-id="9caf9-140">HTTP/2 for transport.</span></span>
  * <span data-ttu-id="9caf9-141">Bufory protokołu jako język opisu interfejsu.</span><span class="sxs-lookup"><span data-stu-id="9caf9-141">Protocol Buffers as the interface description language.</span></span>
  * <span data-ttu-id="9caf9-142">Binarny format serializacji.</span><span class="sxs-lookup"><span data-stu-id="9caf9-142">Binary serialization format.</span></span>
* <span data-ttu-id="9caf9-143">Udostępnia funkcje takie jak:</span><span class="sxs-lookup"><span data-stu-id="9caf9-143">Provides features such as:</span></span>

  * <span data-ttu-id="9caf9-144">Uwierzytelnianie</span><span class="sxs-lookup"><span data-stu-id="9caf9-144">Authentication</span></span>
  * <span data-ttu-id="9caf9-145">Dwukierunkowe przesyłanie strumieniowe i sterowanie przepływem.</span><span class="sxs-lookup"><span data-stu-id="9caf9-145">Bidirectional streaming and flow control.</span></span>
  * <span data-ttu-id="9caf9-146">Anulowanie i przekroczenie limitu czasu.</span><span class="sxs-lookup"><span data-stu-id="9caf9-146">Cancellation and timeouts.</span></span>

<span data-ttu-id="9caf9-147">funkcje gRPC w ASP.NET Core 3,0 obejmują:</span><span class="sxs-lookup"><span data-stu-id="9caf9-147">gRPC functionality in ASP.NET Core 3.0 includes:</span></span>

* <span data-ttu-id="9caf9-148">[GRPC. AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) &ndash; platformę ASP.NET Core do hostowania usług GRPC.</span><span class="sxs-lookup"><span data-stu-id="9caf9-148">[Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) &ndash; An ASP.NET Core framework for hosting gRPC services.</span></span> <span data-ttu-id="9caf9-149">gRPC na ASP.NET Core integruje się ze standardowymi funkcjami ASP.NET Core, takimi jak rejestrowanie, iniekcja zależności (DI), uwierzytelnianie i autoryzacja.</span><span class="sxs-lookup"><span data-stu-id="9caf9-149">gRPC on ASP.NET Core integrates with standard ASP.NET Core features like logging, dependency injection (DI), authentication, and authorization.</span></span>
* <span data-ttu-id="9caf9-150">[GRPC .NET. Client](https://www.nuget.org/packages/Grpc.Net.Client) &ndash; klient programu GRPC dla platformy .NET Core, który kompiluje `HttpClient`się na znanym czasie.</span><span class="sxs-lookup"><span data-stu-id="9caf9-150">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) &ndash; A gRPC client for .NET Core that builds upon the familiar `HttpClient`.</span></span>
* <span data-ttu-id="9caf9-151">[GRPC .NET. ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) &ndash; GRPC klienta integracji z `HttpClientFactory`programem.</span><span class="sxs-lookup"><span data-stu-id="9caf9-151">[Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) &ndash; gRPC client integration with `HttpClientFactory`.</span></span>

<span data-ttu-id="9caf9-152">Aby uzyskać więcej informacji, zobacz <xref:grpc/index>.</span><span class="sxs-lookup"><span data-stu-id="9caf9-152">For more information, see <xref:grpc/index>.</span></span>

## SignalR

<span data-ttu-id="9caf9-153">Zobacz [sekcję SignalR Aktualizowanie kodu](xref:migration/22-to-30#signalr) na potrzeby instrukcji migracji.</span><span class="sxs-lookup"><span data-stu-id="9caf9-153">See [Update SignalR code](xref:migration/22-to-30#signalr) for migration instructions.</span></span> SignalR<span data-ttu-id="9caf9-154">teraz używa `System.Text.Json` do serializacji/deserializacji komunikatów JSON.</span><span class="sxs-lookup"><span data-stu-id="9caf9-154"> now uses `System.Text.Json` to serialize/deserialize JSON messages.</span></span> <span data-ttu-id="9caf9-155">Aby uzyskać instrukcje dotyczące przywracania serializatora opartego na pliku `Newtonsoft.Json`, zobacz [Switch to Newtonsoft. JSON](xref:migration/22-to-30#switch-to-newtonsoftjson) .</span><span class="sxs-lookup"><span data-stu-id="9caf9-155">See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson) for instructions to restore the `Newtonsoft.Json`-based serializer.</span></span>

<span data-ttu-id="9caf9-156">W przypadku klientów z obsługą języka SignalRJavaScript i .NET dla programu dodano obsługę automatycznego ponownego łączenia.</span><span class="sxs-lookup"><span data-stu-id="9caf9-156">In the JavaScript and .NET Clients for SignalR, support was added for automatic reconnection.</span></span> <span data-ttu-id="9caf9-157">Domyślnie klient próbuje ponownie nawiązać połączenie, a następnie ponowić próbę po 2, 10 i 30 sekundach, jeśli jest to konieczne.</span><span class="sxs-lookup"><span data-stu-id="9caf9-157">By default, the client tries to reconnect immediately and retry after 2, 10, and 30 seconds if necessary.</span></span> <span data-ttu-id="9caf9-158">Jeśli klient pomyślnie nawiąże połączenie, otrzymuje nowy identyfikator połączenia.</span><span class="sxs-lookup"><span data-stu-id="9caf9-158">If the client successfully reconnects, it receives a new connection ID.</span></span> <span data-ttu-id="9caf9-159">Automatyczne ponowne łączenie jest zgodą:</span><span class="sxs-lookup"><span data-stu-id="9caf9-159">Automatic reconnect is opt-in:</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="9caf9-160">Interwały ponownego połączenia można określić, przekazując tablicę czasów trwania opartych na milisekundach:</span><span class="sxs-lookup"><span data-stu-id="9caf9-160">The reconnection intervals can be specified by passing an array of millisecond-based durations:</span></span>

```javascript
.withAutomaticReconnect([0, 3000, 5000, 10000, 15000, 30000])
//.withAutomaticReconnect([0, 2000, 10000, 30000]) The default intervals.
```

<span data-ttu-id="9caf9-161">Implementację niestandardową można przekazywać w celu zapewnienia pełnej kontroli nad interwałami ponownego połączenia.</span><span class="sxs-lookup"><span data-stu-id="9caf9-161">A custom implementation can be passed in for full control of the reconnection intervals.</span></span>

<span data-ttu-id="9caf9-162">Jeśli ponowne połączenie nie powiedzie się po ostatnim interwale ponownego połączenia:</span><span class="sxs-lookup"><span data-stu-id="9caf9-162">If the reconnection fails after the last reconnect interval:</span></span>

* <span data-ttu-id="9caf9-163">Klient uważa, że połączenie jest w trybie offline.</span><span class="sxs-lookup"><span data-stu-id="9caf9-163">The client considers the connection is offline.</span></span>
* <span data-ttu-id="9caf9-164">Klient przestanie próbować ponownie nawiązać połączenie.</span><span class="sxs-lookup"><span data-stu-id="9caf9-164">The client stops trying to reconnect.</span></span>

<span data-ttu-id="9caf9-165">Podczas próby ponownego połączenia zaktualizuj interfejs użytkownika aplikacji, aby powiadomić użytkownika o tym, że trwa próba ponownego połączenia.</span><span class="sxs-lookup"><span data-stu-id="9caf9-165">During reconnection attempts, update the app UI to notify the user that the reconnection is being attempted.</span></span>

<span data-ttu-id="9caf9-166">Aby zapewnić informacje zwrotne interfejsu użytkownika w przypadku przerwania SignalR połączenia, interfejs API klienta został rozszerzony w celu uwzględnienia następujących programów obsługi zdarzeń:</span><span class="sxs-lookup"><span data-stu-id="9caf9-166">To provide UI feedback when the connection is interrupted, the SignalR client API has been expanded to include the following event handlers:</span></span>

* <span data-ttu-id="9caf9-167">`onreconnecting`: Umożliwia deweloperom wyłączenie interfejsu użytkownika lub umożliwienie użytkownikom znajomości aplikacji w trybie offline.</span><span class="sxs-lookup"><span data-stu-id="9caf9-167">`onreconnecting`:  Gives developers an opportunity to disable UI or to let users know the app is offline.</span></span>
* <span data-ttu-id="9caf9-168">`onreconnected`: Daje deweloperom możliwość aktualizowania interfejsu użytkownika po jego nawiązaniu.</span><span class="sxs-lookup"><span data-stu-id="9caf9-168">`onreconnected`: Gives developers an opportunity to update the UI once the connection is reestablished.</span></span>

<span data-ttu-id="9caf9-169">Następujący kod używa `onreconnecting` do aktualizowania interfejsu użytkownika podczas próby nawiązania połączenia:</span><span class="sxs-lookup"><span data-stu-id="9caf9-169">The following code uses `onreconnecting` to update the UI while trying to connect:</span></span>

```javascript
connection.onreconnecting((error) => {
    const status = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messageInput").disabled = true;
    document.getElementById("sendButton").disabled = true;
    document.getElementById("connectionStatus").innerText = status;
});
```

<span data-ttu-id="9caf9-170">Następujący kod używa `onreconnected` do aktualizowania interfejsu użytkownika w połączeniu:</span><span class="sxs-lookup"><span data-stu-id="9caf9-170">The following code uses `onreconnected` to update the UI on connection:</span></span>

```javascript
connection.onreconnected((connectionId) => {
    const status = `Connection reestablished. Connected.`;
    document.getElementById("messageInput").disabled = false;
    document.getElementById("sendButton").disabled = false;
    document.getElementById("connectionStatus").innerText = status;
});
```

SignalR<span data-ttu-id="9caf9-171">3,0 i nowsze udostępnia zasób niestandardowy do obsługi autoryzacji, gdy metoda centrum wymaga autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="9caf9-171"> 3.0 and later provides a custom resource to authorization handlers when a hub method requires authorization.</span></span> <span data-ttu-id="9caf9-172">Zasób jest wystąpieniem `HubInvocationContext`.</span><span class="sxs-lookup"><span data-stu-id="9caf9-172">The resource is an instance of `HubInvocationContext`.</span></span> <span data-ttu-id="9caf9-173">`HubInvocationContext` Obejmuje:</span><span class="sxs-lookup"><span data-stu-id="9caf9-173">The `HubInvocationContext` includes the:</span></span>

* `HubCallerContext`
* <span data-ttu-id="9caf9-174">Nazwa wywoływanej metody centrum.</span><span class="sxs-lookup"><span data-stu-id="9caf9-174">Name of the hub method being invoked.</span></span>
* <span data-ttu-id="9caf9-175">Argumenty metody centrum.</span><span class="sxs-lookup"><span data-stu-id="9caf9-175">Arguments to the hub method.</span></span>

<span data-ttu-id="9caf9-176">Rozważmy następujący przykład aplikacji pokoju rozmów, która umożliwia logowanie w wielu organizacjach za pośrednictwem Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="9caf9-176">Consider the following example of a chat room app allowing multiple organization sign-in via Azure Active Directory.</span></span> <span data-ttu-id="9caf9-177">Każda osoba mająca konto Microsoft może zalogować się do programu chat, ale tylko członkowie organizacji będącej właścicielem mogą zakazać użytkowników lub wyświetlać historie rozmów użytkowników.</span><span class="sxs-lookup"><span data-stu-id="9caf9-177">Anyone with a Microsoft account can sign in to chat, but only members of the owning organization can ban users or view users' chat histories.</span></span> <span data-ttu-id="9caf9-178">Aplikacja może ograniczyć niektóre funkcje do określonych użytkowników.</span><span class="sxs-lookup"><span data-stu-id="9caf9-178">The app could restrict certain functionality from specific users.</span></span>

```csharp
public class DomainRestrictedRequirement :
    AuthorizationHandler<DomainRestrictedRequirement, HubInvocationContext>,
    IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
        DomainRestrictedRequirement requirement,
        HubInvocationContext resource)
    {
        if (context.User?.Identity?.Name == null)
        {
            return Task.CompletedTask;
        }

        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.Identity.Name))
        {
            context.Succeed(requirement);
        }

        return Task.CompletedTask;
    }

    private bool IsUserAllowedToDoThis(string hubMethodName, string currentUsername)
    {
        if (hubMethodName.Equals("banUser", StringComparison.OrdinalIgnoreCase))
        {
            return currentUsername.Equals("bob42@jabbr.net", StringComparison.OrdinalIgnoreCase);
        }

        return currentUsername.EndsWith("@jabbr.net", StringComparison.OrdinalIgnoreCase));
    }
}
```

<span data-ttu-id="9caf9-179">W poprzednim kodzie program `DomainRestrictedRequirement` służy jako niestandardowy. `IAuthorizationRequirement`</span><span class="sxs-lookup"><span data-stu-id="9caf9-179">In the preceding code, `DomainRestrictedRequirement` serves as a custom `IAuthorizationRequirement`.</span></span> <span data-ttu-id="9caf9-180">Ponieważ parametr `HubInvocationContext` zasobu jest przesyłany, wewnętrzna logika może:</span><span class="sxs-lookup"><span data-stu-id="9caf9-180">Because the `HubInvocationContext` resource parameter is being passed in, the internal logic can:</span></span>

* <span data-ttu-id="9caf9-181">Sprawdź kontekst, w którym jest wywoływany centrum.</span><span class="sxs-lookup"><span data-stu-id="9caf9-181">Inspect the context in which the Hub is being called.</span></span>
* <span data-ttu-id="9caf9-182">Podejmowanie decyzji na umożliwienie użytkownikowi wykonywania poszczególnych metod centrów.</span><span class="sxs-lookup"><span data-stu-id="9caf9-182">Make decisions on allowing the user to execute individual Hub methods.</span></span>

<span data-ttu-id="9caf9-183">Poszczególne metody centrów mogą być oznaczone nazwą zasad, które kod sprawdza w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="9caf9-183">Individual Hub methods can be marked with the name of the policy the code checks at run-time.</span></span> <span data-ttu-id="9caf9-184">Gdy klienci próbują wywołać poszczególne metody centrum, `DomainRestrictedRequirement` program obsługi i kontroluje dostęp do tych metod.</span><span class="sxs-lookup"><span data-stu-id="9caf9-184">As clients attempt to call individual Hub methods, the `DomainRestrictedRequirement` handler runs and controls access to the methods.</span></span> <span data-ttu-id="9caf9-185">W oparciu o sposób dostępu `DomainRestrictedRequirement` do formantów:</span><span class="sxs-lookup"><span data-stu-id="9caf9-185">Based on the way the `DomainRestrictedRequirement` controls access:</span></span>

* <span data-ttu-id="9caf9-186">Wszyscy zalogowani użytkownicy mogą wywoływać `SendMessage` metodę.</span><span class="sxs-lookup"><span data-stu-id="9caf9-186">All logged-in users can call the `SendMessage` method.</span></span>
* <span data-ttu-id="9caf9-187">Historie użytkowników mogą wyświetlać tylko użytkownicy, którzy `@jabbr.net` zalogowali się przy użyciu adresu e-mail.</span><span class="sxs-lookup"><span data-stu-id="9caf9-187">Only users who have logged in with a `@jabbr.net` email address can view users' histories.</span></span>
* <span data-ttu-id="9caf9-188">Mogą `bob42@jabbr.net` oni jedynie blokowanie użytkowników z pokoju rozmowy.</span><span class="sxs-lookup"><span data-stu-id="9caf9-188">Only `bob42@jabbr.net` can ban users from the chat room.</span></span>

```csharp
[Authorize]
public class ChatHub : Hub
{
    public void SendMessage(string message)
    {
    }

    [Authorize("DomainRestricted")]
    public void BanUser(string username)
    {
    }

    [Authorize("DomainRestricted")]
    public void ViewUserHistory(string username)
    {
    }
}
```

<span data-ttu-id="9caf9-189">Tworzenie `DomainRestricted` zasad może wymagać:</span><span class="sxs-lookup"><span data-stu-id="9caf9-189">Creating the `DomainRestricted` policy might involve:</span></span>

* <span data-ttu-id="9caf9-190">W *Startup.cs*Dodaj nowe zasady.</span><span class="sxs-lookup"><span data-stu-id="9caf9-190">In *Startup.cs*, adding the new policy.</span></span>
* <span data-ttu-id="9caf9-191">Podaj niestandardowe `DomainRestrictedRequirement` wymagania jako parametr.</span><span class="sxs-lookup"><span data-stu-id="9caf9-191">Provide the custom `DomainRestrictedRequirement` requirement as a parameter.</span></span>
* <span data-ttu-id="9caf9-192">Rejestrowanie `DomainRestricted` w oprogramowaniu pośredniczącym autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="9caf9-192">Registering `DomainRestricted` with the authorization middleware.</span></span>

```csharp
services
    .AddAuthorization(options =>
    {
        options.AddPolicy("DomainRestricted", policy =>
        {
            policy.Requirements.Add(new DomainRestrictedRequirement());
        });
    });
```

SignalR<span data-ttu-id="9caf9-193">Centra używają [routingu punktów końcowych](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="9caf9-193"> hubs use [Endpoint Routing](xref:fundamentals/routing).</span></span> SignalR<span data-ttu-id="9caf9-194">połączenie centrum zostało wcześniej wykonane jawnie:</span><span class="sxs-lookup"><span data-stu-id="9caf9-194"> hub connection was previously done explicitly:</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});
```

<span data-ttu-id="9caf9-195">W poprzedniej wersji deweloperzy musieli połączyć kontrolery, Razor strony i centra w różnych miejscach.</span><span class="sxs-lookup"><span data-stu-id="9caf9-195">In the previous version, developers needed to wire up controllers, Razor pages, and hubs in a variety of places.</span></span> <span data-ttu-id="9caf9-196">Jawne połączenie daje w wyniku serię niemal identycznych segmentów routingu:</span><span class="sxs-lookup"><span data-stu-id="9caf9-196">Explicit connection results in a series of nearly-identical routing segments:</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});

app.UseRouting(routes =>
{
    routes.MapRazorPages();
});
```

SignalR<span data-ttu-id="9caf9-197">Centra 3,0 można kierować za pośrednictwem routingu punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="9caf9-197"> 3.0 hubs can be routed via endpoint routing.</span></span> <span data-ttu-id="9caf9-198">Za pomocą routingu punktów końcowych, zazwyczaj wszystkie Routing można `UseRouting`skonfigurować w:</span><span class="sxs-lookup"><span data-stu-id="9caf9-198">With endpoint routing, typically all routing can be configured in `UseRouting`:</span></span>

```csharp
app.UseRouting(routes =>
{
    routes.MapRazorPages();
    routes.MapHub<ChatHub>("hubs/chat");
});
```

<span data-ttu-id="9caf9-199">Dodano ASP.NET Core SignalR 3,0:</span><span class="sxs-lookup"><span data-stu-id="9caf9-199">ASP.NET Core 3.0 SignalR added:</span></span>

<span data-ttu-id="9caf9-200">Przesyłanie strumieniowe klient-serwer.</span><span class="sxs-lookup"><span data-stu-id="9caf9-200">Client-to-server streaming.</span></span> <span data-ttu-id="9caf9-201">W przypadku przesyłania strumieniowego klient-serwer metody po stronie serwera mogą przyjmować wystąpienia `IAsyncEnumerable<T>` lub. `ChannelReader<T>`</span><span class="sxs-lookup"><span data-stu-id="9caf9-201">With client-to-server streaming, server-side methods can take instances of either an `IAsyncEnumerable<T>` or `ChannelReader<T>`.</span></span> <span data-ttu-id="9caf9-202">W poniższym przykładzie w języku C# `UploadStream` Metoda w centrum będzie odbierać strumienie ciągów od klienta:</span><span class="sxs-lookup"><span data-stu-id="9caf9-202">In the following C# sample, the `UploadStream` method on the Hub will receive a stream of strings from the client:</span></span>

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        // process content
    }
}
```

<span data-ttu-id="9caf9-203">Aplikacje klienckie platformy .NET mogą `IAsyncEnumerable<T>` przekazać albo `ChannelReader<T>` wystąpienie jako `stream` argument powyższej metody `UploadStream` centrum.</span><span class="sxs-lookup"><span data-stu-id="9caf9-203">.NET client apps can pass either an `IAsyncEnumerable<T>` or `ChannelReader<T>` instance as the `stream` argument of the `UploadStream` Hub method above.</span></span>

<span data-ttu-id="9caf9-204">Po zakończeniu `for` pętli, gdy funkcja lokalna zostanie zakończona, zostanie wysłany strumień:</span><span class="sxs-lookup"><span data-stu-id="9caf9-204">After the `for` loop has completed and the local function exits, the stream completion is sent:</span></span>

```csharp
async IAsyncEnumerable<string> clientStreamData()
{
    for (var i = 0; i < 5; i++)
    {
        var data = await FetchSomeData();
        yield return data;
    }
}

await connection.SendAsync("UploadStream", clientStreamData());
```

<span data-ttu-id="9caf9-205">Aplikacje klienckie języka JavaScript używają SignalR `Subject` (lub [tematu RxJS](https://rxjs.dev/api/index/class/Subject)) dla `stream` argumentu powyższej metody `UploadStream` centrum.</span><span class="sxs-lookup"><span data-stu-id="9caf9-205">JavaScript client apps use the SignalR `Subject` (or an [RxJS Subject](https://rxjs.dev/api/index/class/Subject)) for the `stream` argument of the `UploadStream` Hub method above.</span></span>

```javascript
let subject = new signalR.Subject();
await connection.send("StartStream", "MyAsciiArtStream", subject);
```

<span data-ttu-id="9caf9-206">Kod JavaScript może używać `subject.next` metody do obsługi ciągów, które są przechwytywane i gotowe do wysłania do serwera.</span><span class="sxs-lookup"><span data-stu-id="9caf9-206">The JavaScript code could use the `subject.next` method to handle strings as they are captured and ready to be sent to the server.</span></span>

```javascript
subject.next("example");
subject.complete();
```

<span data-ttu-id="9caf9-207">Korzystając z kodu, takiego jak dwa poprzednie fragmenty, można utworzyć środowiska przesyłania strumieniowego w czasie rzeczywistym.</span><span class="sxs-lookup"><span data-stu-id="9caf9-207">Using code like the two preceding snippets, real-time streaming experiences can be created.</span></span>

## <a name="new-json-serialization"></a><span data-ttu-id="9caf9-208">Nowa Serializacja JSON</span><span class="sxs-lookup"><span data-stu-id="9caf9-208">New JSON serialization</span></span>

<span data-ttu-id="9caf9-209">ASP.NET Core 3,0 teraz domyślnie <xref:System.Text.Json> używa dla serializacji JSON:</span><span class="sxs-lookup"><span data-stu-id="9caf9-209">ASP.NET Core 3.0 now uses <xref:System.Text.Json> by default for JSON serialization:</span></span>

* <span data-ttu-id="9caf9-210">Odczytuje i zapisuje dane JSON asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="9caf9-210">Reads and writes JSON asynchronously.</span></span>
* <span data-ttu-id="9caf9-211">Jest zoptymalizowany pod kątem tekstu w formacie UTF-8.</span><span class="sxs-lookup"><span data-stu-id="9caf9-211">Is optimized for UTF-8 text.</span></span>
* <span data-ttu-id="9caf9-212">Zwykle wyższa wydajność niż `Newtonsoft.Json`.</span><span class="sxs-lookup"><span data-stu-id="9caf9-212">Typically higher performance than `Newtonsoft.Json`.</span></span>

<span data-ttu-id="9caf9-213">Aby dodać Json.NET do ASP.NET Core 3,0, zobacz [Dodawanie obsługi formatu JSON opartego na Newtonsoft. JSON](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span><span class="sxs-lookup"><span data-stu-id="9caf9-213">To add Json.NET to ASP.NET Core 3.0, see [Add Newtonsoft.Json-based JSON format support](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span></span>

## <a name="new-razor-directives"></a><span data-ttu-id="9caf9-214">Nowe Razor dyrektywy</span><span class="sxs-lookup"><span data-stu-id="9caf9-214">New Razor directives</span></span>

<span data-ttu-id="9caf9-215">Poniższa lista zawiera nowe Razor dyrektywy:</span><span class="sxs-lookup"><span data-stu-id="9caf9-215">The following list contains new Razor directives:</span></span>

* <span data-ttu-id="9caf9-216">[`@attribute`](xref:mvc/views/razor#attribute)&ndash; Dyrektywa stosuje dany atrybut do klasy wygenerowanej strony lub `@attribute` widoku.</span><span class="sxs-lookup"><span data-stu-id="9caf9-216">[`@attribute`](xref:mvc/views/razor#attribute) &ndash; The `@attribute` directive applies the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="9caf9-217">Na przykład `@attribute [Authorize]`.</span><span class="sxs-lookup"><span data-stu-id="9caf9-217">For example, `@attribute [Authorize]`.</span></span>
* <span data-ttu-id="9caf9-218">[`@implements`](xref:mvc/views/razor#implements)&ndash; Dyrektywa implementuje interfejs dla wygenerowanej `@implements` klasy.</span><span class="sxs-lookup"><span data-stu-id="9caf9-218">[`@implements`](xref:mvc/views/razor#implements) &ndash; The `@implements` directive implements an interface for the generated class.</span></span> <span data-ttu-id="9caf9-219">Na przykład `@implements IDisposable`.</span><span class="sxs-lookup"><span data-stu-id="9caf9-219">For example, `@implements IDisposable`.</span></span>

## <a name="identityserver4-supports-authentication-and-authorization-for-web-apis-and-spas"></a><span data-ttu-id="9caf9-220">Usługi identityserver4 obsługuje uwierzytelnianie i autoryzację dla interfejsów API sieci Web i aplikacji jednostronicowych</span><span class="sxs-lookup"><span data-stu-id="9caf9-220">IdentityServer4 supports authentication and authorization for web APIs and SPAs</span></span>

<span data-ttu-id="9caf9-221">ASP.NET Core 3,0 oferuje uwierzytelnianie w aplikacjach jednostronicowych (aplikacji jednostronicowych) przy użyciu obsługi autoryzacji interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="9caf9-221">ASP.NET Core 3.0 offers authentication in Single Page Apps (SPAs) using the support for web API authorization.</span></span> <span data-ttu-id="9caf9-222">ASP.NET Core Identity do uwierzytelniania i przechowywania użytkowników jest połączony z [usługi identityserver4](https://identityserver.io/) w celu zaimplementowania programu Open ID Connect.</span><span class="sxs-lookup"><span data-stu-id="9caf9-222">ASP.NET Core Identity for authenticating and storing users is combined with [IdentityServer4](https://identityserver.io/) for implementing Open ID Connect.</span></span>

<span data-ttu-id="9caf9-223">Usługi identityserver4 to struktura OpenID Connect Connect i OAuth 2,0 dla ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="9caf9-223">IdentityServer4 is an OpenID Connect and OAuth 2.0 framework for ASP.NET Core 3.0.</span></span> <span data-ttu-id="9caf9-224">Zapewnia następujące funkcje zabezpieczeń:</span><span class="sxs-lookup"><span data-stu-id="9caf9-224">It enables the following security features:</span></span>

* <span data-ttu-id="9caf9-225">Uwierzytelnianie jako usługa (AaaS)</span><span class="sxs-lookup"><span data-stu-id="9caf9-225">Authentication as a Service (AaaS)</span></span>
* <span data-ttu-id="9caf9-226">Logowanie jednokrotne (SSO) dla wielu typów aplikacji</span><span class="sxs-lookup"><span data-stu-id="9caf9-226">Single sign-on/off (SSO) over multiple application types</span></span>
* <span data-ttu-id="9caf9-227">Kontrola dostępu do interfejsów API</span><span class="sxs-lookup"><span data-stu-id="9caf9-227">Access control for APIs</span></span>
* <span data-ttu-id="9caf9-228">Brama federacyjna</span><span class="sxs-lookup"><span data-stu-id="9caf9-228">Federation Gateway</span></span>

<span data-ttu-id="9caf9-229">Aby uzyskać więcej informacji, zapoznaj [się z dokumentacją usługi identityserver4](http://docs.identityserver.io/en/latest/index.html) lub [uwierzytelnianiem i autoryzacją aplikacji jednostronicowych](xref:security/authentication/identity/spa).</span><span class="sxs-lookup"><span data-stu-id="9caf9-229">For more information, see [the IdentityServer4 documentation](http://docs.identityserver.io/en/latest/index.html) or [Authentication and authorization for SPAs](xref:security/authentication/identity/spa).</span></span>

## <a name="certificate-and-kerberos-authentication"></a><span data-ttu-id="9caf9-230">Certyfikat i uwierzytelnianie Kerberos</span><span class="sxs-lookup"><span data-stu-id="9caf9-230">Certificate and Kerberos authentication</span></span>

<span data-ttu-id="9caf9-231">Wymagane jest uwierzytelnianie certyfikatu:</span><span class="sxs-lookup"><span data-stu-id="9caf9-231">Certificate authentication requires:</span></span>

* <span data-ttu-id="9caf9-232">Konfigurowanie serwera do akceptowania certyfikatów.</span><span class="sxs-lookup"><span data-stu-id="9caf9-232">Configuring the server to accept certificates.</span></span>
* <span data-ttu-id="9caf9-233">Dodawanie oprogramowania pośredniczącego uwierzytelniania w `Startup.Configure`programie.</span><span class="sxs-lookup"><span data-stu-id="9caf9-233">Adding the authentication middleware in `Startup.Configure`.</span></span>
* <span data-ttu-id="9caf9-234">Dodawanie usługi uwierzytelniania certyfikatów w programie `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="9caf9-234">Adding the certificate authentication service in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
            .AddCertificate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

<span data-ttu-id="9caf9-235">Opcje uwierzytelniania certyfikatów obejmują:</span><span class="sxs-lookup"><span data-stu-id="9caf9-235">Options for certificate authentication include the ability to:</span></span>

* <span data-ttu-id="9caf9-236">Zaakceptuj certyfikaty z podpisem własnym.</span><span class="sxs-lookup"><span data-stu-id="9caf9-236">Accept self-signed certificates.</span></span>
* <span data-ttu-id="9caf9-237">Sprawdź, czy certyfikaty są odwoływane.</span><span class="sxs-lookup"><span data-stu-id="9caf9-237">Check for certificate revocation.</span></span>
* <span data-ttu-id="9caf9-238">Sprawdź, czy certyfikat proffered ma odpowiednie flagi użycia.</span><span class="sxs-lookup"><span data-stu-id="9caf9-238">Check that the proffered certificate has the right usage flags in it.</span></span>

<span data-ttu-id="9caf9-239">Domyślny podmiot zabezpieczeń użytkownika jest konstruowany ze wszystkich właściwości certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="9caf9-239">A default user principal is constructed from the certificate properties.</span></span> <span data-ttu-id="9caf9-240">Nazwa główna użytkownika zawiera zdarzenie, które umożliwia uzupełnianie lub zastępowanie podmiotu zabezpieczeń.</span><span class="sxs-lookup"><span data-stu-id="9caf9-240">The user principal contains an event that enables supplementing or replacing the principal.</span></span> <span data-ttu-id="9caf9-241">Aby uzyskać więcej informacji, zobacz <xref:security/authentication/certauth>.</span><span class="sxs-lookup"><span data-stu-id="9caf9-241">For more information, see <xref:security/authentication/certauth>.</span></span>

<span data-ttu-id="9caf9-242">[Uwierzytelnianie systemu Windows](/windows-server/security/windows-authentication/windows-authentication-overview) zostało rozszerzone na system Linux i macOS.</span><span class="sxs-lookup"><span data-stu-id="9caf9-242">[Windows Authentication](/windows-server/security/windows-authentication/windows-authentication-overview) has been extended onto Linux and macOS.</span></span> <span data-ttu-id="9caf9-243">W poprzednich wersjach uwierzytelnianie systemu Windows było ograniczone do [usług IIS](xref:host-and-deploy/iis/index) i [HttpSys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="9caf9-243">In previous versions, Windows Authentication was limited to [IIS](xref:host-and-deploy/iis/index) and [HttpSys](xref:fundamentals/servers/httpsys).</span></span> <span data-ttu-id="9caf9-244">W ASP.NET Core 3,0 [Kestrel](xref:fundamentals/servers/kestrel) ma możliwość używania [protokołów](/windows-server/security/kerberos/kerberos-authentication-overview)Negotiate, Kerberos i [NTLM w systemach Windows](/windows-server/security/kerberos/ntlm-overview), Linux i macOS dla hostów przyłączonych do domeny systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="9caf9-244">In ASP.NET Core 3.0, [Kestrel](xref:fundamentals/servers/kestrel) has the ability to use Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview), and [NTLM on Windows](/windows-server/security/kerberos/ntlm-overview), Linux, and macOS for Windows domain-joined hosts.</span></span> <span data-ttu-id="9caf9-245">Kestrel obsługa tych schematów uwierzytelniania jest udostępniana przez pakiet [NuGet Microsoft. AspNetCore. Authentication. Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) .</span><span class="sxs-lookup"><span data-stu-id="9caf9-245">Kestrel support of these authentication schemes is provided by the [Microsoft.AspNetCore.Authentication.Negotiate NuGet](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) package.</span></span> <span data-ttu-id="9caf9-246">Podobnie jak w przypadku innych usług uwierzytelniania, skonfiguruj aplikację uwierzytelniania Wide, a następnie skonfiguruj usługę:</span><span class="sxs-lookup"><span data-stu-id="9caf9-246">As with the other authentication services, configure authentication app wide, then configure the service:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
        .AddNegotiate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

<span data-ttu-id="9caf9-247">Wymagania dotyczące hosta:</span><span class="sxs-lookup"><span data-stu-id="9caf9-247">Host requirements:</span></span>

* <span data-ttu-id="9caf9-248">Hosty z systemem Windows muszą mieć dodane [główne nazwy usług](/windows/win32/ad/service-principal-names) (SPN) do konta użytkownika obsługującego aplikację.</span><span class="sxs-lookup"><span data-stu-id="9caf9-248">Windows hosts must have [Service Principal Names](/windows/win32/ad/service-principal-names) (SPNs) added to the user account hosting the app.</span></span>
* <span data-ttu-id="9caf9-249">Maszyny z systemami Linux i macOS muszą być przyłączone do domeny.</span><span class="sxs-lookup"><span data-stu-id="9caf9-249">Linux and macOS machines must be joined to the domain.</span></span>
  * <span data-ttu-id="9caf9-250">Dla procesu sieci Web należy utworzyć nazwy SPN.</span><span class="sxs-lookup"><span data-stu-id="9caf9-250">SPNs must be created for the web process.</span></span>
  * <span data-ttu-id="9caf9-251">Na komputerze hosta muszą być generowane i skonfigurowane [pliki plik KEYTAB](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) .</span><span class="sxs-lookup"><span data-stu-id="9caf9-251">[Keytab files](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) must be generated and configured on the host machine.</span></span>

<span data-ttu-id="9caf9-252">Aby uzyskać więcej informacji, zobacz <xref:security/authentication/windowsauth>.</span><span class="sxs-lookup"><span data-stu-id="9caf9-252">For more information, see <xref:security/authentication/windowsauth>.</span></span>

## <a name="template-changes"></a><span data-ttu-id="9caf9-253">Zmiany szablonu</span><span class="sxs-lookup"><span data-stu-id="9caf9-253">Template changes</span></span>

<span data-ttu-id="9caf9-254">Szablony interfejsu użytkownika sieci WebRazor (strony, MVC z kontrolerem i widokami) mają następujące usunięte:</span><span class="sxs-lookup"><span data-stu-id="9caf9-254">The web UI templates (Razor Pages, MVC with controller and views) have the following removed:</span></span>

* <span data-ttu-id="9caf9-255">Interfejs użytkownika zgody na plik cookie nie jest już uwzględniony.</span><span class="sxs-lookup"><span data-stu-id="9caf9-255">The cookie consent UI is no longer included.</span></span> <span data-ttu-id="9caf9-256">Aby włączyć funkcję wyrażania zgody plików cookie w aplikacji wygenerowanej przez szablon ASP.NET Core <xref:security/gdpr>3,0, zobacz.</span><span class="sxs-lookup"><span data-stu-id="9caf9-256">To enable the cookie consent feature in an ASP.NET Core 3.0 template-generated app, see <xref:security/gdpr>.</span></span>
* <span data-ttu-id="9caf9-257">Skrypty i powiązane zasoby statyczne są teraz przywoływane jako pliki lokalne zamiast używać sieci CDN.</span><span class="sxs-lookup"><span data-stu-id="9caf9-257">Scripts and related static assets are now referenced as local files instead of using CDNs.</span></span> <span data-ttu-id="9caf9-258">Aby uzyskać więcej informacji, zobacz [skrypty i powiązane zasoby statyczne są teraz odwołujące się do plików lokalnych zamiast używania sieci CDN w oparciu o bieżące środowisko (ASPNET/AspNetCore. Docs #14350)](https://github.com/dotnet/AspNetCore.Docs/issues/14350).</span><span class="sxs-lookup"><span data-stu-id="9caf9-258">For more information, see [Scripts and related static assets are now referenced as local files instead of using CDNs based on the current environment (aspnet/AspNetCore.Docs #14350)](https://github.com/dotnet/AspNetCore.Docs/issues/14350).</span></span>

<span data-ttu-id="9caf9-259">Szablon kątowy został zaktualizowany do użycia kątowy 8.</span><span class="sxs-lookup"><span data-stu-id="9caf9-259">The Angular template updated to use Angular 8.</span></span>

<span data-ttu-id="9caf9-260">Domyślnie Razor szablon biblioteki klas (RCL) jest domyślnym Razor programowaniem składników.</span><span class="sxs-lookup"><span data-stu-id="9caf9-260">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="9caf9-261">Nowa opcja szablonu w programie Visual Studio zapewnia obsługę szablonów dla stron i widoków.</span><span class="sxs-lookup"><span data-stu-id="9caf9-261">A new template option in Visual Studio provides template support for pages and views.</span></span> <span data-ttu-id="9caf9-262">Podczas tworzenia RCL z szablonu w powłoce poleceń, należy przekazać `--support-pages-and-views` opcję (`dotnet new razorclasslib --support-pages-and-views`).</span><span class="sxs-lookup"><span data-stu-id="9caf9-262">When creating an RCL from the template in a command shell, pass the `--support-pages-and-views` option (`dotnet new razorclasslib --support-pages-and-views`).</span></span>

## <a name="generic-host"></a><span data-ttu-id="9caf9-263">Host ogólny</span><span class="sxs-lookup"><span data-stu-id="9caf9-263">Generic Host</span></span>

<span data-ttu-id="9caf9-264">Szablony ASP.NET Core 3,0 używają <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="9caf9-264">The ASP.NET Core 3.0 templates use <xref:fundamentals/host/generic-host>.</span></span> <span data-ttu-id="9caf9-265">Użyto <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>poprzednich wersji.</span><span class="sxs-lookup"><span data-stu-id="9caf9-265">Previous versions used <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>.</span></span> <span data-ttu-id="9caf9-266">Korzystanie z hosta ogólnego platformy .NET Core<xref:Microsoft.Extensions.Hosting.HostBuilder>() zapewnia lepszą integrację aplikacji ASP.NET Core z innymi scenariuszami serwera, które nie są specyficzne dla sieci Web.</span><span class="sxs-lookup"><span data-stu-id="9caf9-266">Using the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) provides better integration of ASP.NET Core apps with other server scenarios that aren't web-specific.</span></span> <span data-ttu-id="9caf9-267">Aby uzyskać więcej informacji, zobacz [HostBuilder zastępuje WebHostBuilder](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="9caf9-267">For more information, see [HostBuilder replaces WebHostBuilder](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).</span></span>

### <a name="host-configuration"></a><span data-ttu-id="9caf9-268">Konfiguracja hosta</span><span class="sxs-lookup"><span data-stu-id="9caf9-268">Host configuration</span></span>

<span data-ttu-id="9caf9-269">Przed wydaniem ASP.NET Core 3,0, zmienne środowiskowe poprzedzone prefiksem `ASPNETCORE_` zostały załadowane na potrzeby konfiguracji hosta hosta sieci Web.</span><span class="sxs-lookup"><span data-stu-id="9caf9-269">Prior to the release of ASP.NET Core 3.0, environment variables prefixed with `ASPNETCORE_` were loaded for host configuration of the Web Host.</span></span> <span data-ttu-id="9caf9-270">W 3,0 służy `AddEnvironmentVariables` do ładowania zmiennych środowiskowych, które są poprzedzone prefiksem `DOTNET_` konfiguracji `CreateDefaultBuilder`hosta w programie.</span><span class="sxs-lookup"><span data-stu-id="9caf9-270">In 3.0, `AddEnvironmentVariables` is used to load environment variables prefixed with `DOTNET_` for host configuration with `CreateDefaultBuilder`.</span></span>

### <a name="changes-to-startup-constructor-injection"></a><span data-ttu-id="9caf9-271">Zmiany w iniekcji konstruktorów uruchomieniowych</span><span class="sxs-lookup"><span data-stu-id="9caf9-271">Changes to Startup constructor injection</span></span>

<span data-ttu-id="9caf9-272">Host generyczny obsługuje tylko następujące typy dla `Startup` iniekcji konstruktora:</span><span class="sxs-lookup"><span data-stu-id="9caf9-272">The Generic Host only supports the following types for `Startup` constructor injection:</span></span>

* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="9caf9-273">Wszystkie usługi można nadal dodawać bezpośrednio jako argumenty `Startup.Configure` metody.</span><span class="sxs-lookup"><span data-stu-id="9caf9-273">All services can still be injected directly as arguments to the `Startup.Configure` method.</span></span> <span data-ttu-id="9caf9-274">Aby uzyskać więcej informacji, zobacz [host ogólny ogranicza iniekcje konstruktorów uruchamiania (ASPNET/anonse #353)](https://github.com/aspnet/Announcements/issues/353).</span><span class="sxs-lookup"><span data-stu-id="9caf9-274">For more information, see [Generic Host restricts Startup constructor injection (aspnet/Announcements #353)](https://github.com/aspnet/Announcements/issues/353).</span></span>

## <a name="kestrel"></a><span data-ttu-id="9caf9-275">Kestrel</span><span class="sxs-lookup"><span data-stu-id="9caf9-275">Kestrel</span></span>

* <span data-ttu-id="9caf9-276">Konfiguracja Kestrel została zaktualizowana na potrzeby migracji do hosta ogólnego.</span><span class="sxs-lookup"><span data-stu-id="9caf9-276">Kestrel configuration has been updated for the migration to the Generic Host.</span></span> <span data-ttu-id="9caf9-277">W 3,0 Kestrel jest skonfigurowany w konstruktorze hosta sieci Web dostarczonym `ConfigureWebHostDefaults`przez program.</span><span class="sxs-lookup"><span data-stu-id="9caf9-277">In 3.0, Kestrel is configured on the web host builder provided by `ConfigureWebHostDefaults`.</span></span>
* <span data-ttu-id="9caf9-278">Adaptery połączeń zostały usunięte z usługi Kestrel i zastąpione przez oprogramowanie pośredniczące połączenia, które jest podobne do oprogramowania pośredniczącego HTTP w potoku ASP.NET Core ale dla połączeń niższego poziomu.</span><span class="sxs-lookup"><span data-stu-id="9caf9-278">Connection Adapters have been removed from Kestrel and replaced with Connection Middleware, which is similar to HTTP Middleware in the ASP.NET Core pipeline but for lower-level connections.</span></span>
* <span data-ttu-id="9caf9-279">Warstwa transportu Kestrel została udostępniona jako interfejs publiczny w programie `Connections.Abstractions`.</span><span class="sxs-lookup"><span data-stu-id="9caf9-279">The Kestrel transport layer has been exposed as a public interface in `Connections.Abstractions`.</span></span>
* <span data-ttu-id="9caf9-280">Niejednoznaczność między nagłówkami a przyczepami została rozwiązana przez przeniesienie końcowych nagłówków do nowej kolekcji.</span><span class="sxs-lookup"><span data-stu-id="9caf9-280">Ambiguity between headers and trailers has been resolved by moving trailing headers to a new collection.</span></span>
* <span data-ttu-id="9caf9-281">Synchroniczne interfejsy operacji we/wy, takie `HttpRequest.Body.Read`jak, są typowym źródłem zablokowania wątków prowadzącego do awarii aplikacji.</span><span class="sxs-lookup"><span data-stu-id="9caf9-281">Synchronous I/O APIs, such as `HttpRequest.Body.Read`, are a common source of thread starvation leading to app crashes.</span></span> <span data-ttu-id="9caf9-282">W 3,0, `AllowSynchronousIO` jest domyślnie wyłączona.</span><span class="sxs-lookup"><span data-stu-id="9caf9-282">In 3.0, `AllowSynchronousIO` is disabled by default.</span></span>

<span data-ttu-id="9caf9-283">Aby uzyskać więcej informacji, zobacz <xref:migration/22-to-30#kestrel>.</span><span class="sxs-lookup"><span data-stu-id="9caf9-283">For more information, see <xref:migration/22-to-30#kestrel>.</span></span>

## <a name="http2-enabled-by-default"></a><span data-ttu-id="9caf9-284">Protokół HTTP/2 włączony domyślnie</span><span class="sxs-lookup"><span data-stu-id="9caf9-284">HTTP/2 enabled by default</span></span>

<span data-ttu-id="9caf9-285">Protokół HTTP/2 jest domyślnie włączony w Kestrel dla punktów końcowych HTTPS.</span><span class="sxs-lookup"><span data-stu-id="9caf9-285">HTTP/2 is enabled by default in Kestrel for HTTPS endpoints.</span></span> <span data-ttu-id="9caf9-286">Obsługa protokołu HTTP/2 dla usług IIS lub HTTP. sys jest włączona, gdy jest ona obsługiwana przez system operacyjny.</span><span class="sxs-lookup"><span data-stu-id="9caf9-286">HTTP/2 support for IIS or HTTP.sys is enabled when supported by the operating system.</span></span>

## <a name="eventcounters-on-request"></a><span data-ttu-id="9caf9-287">EventCounters na żądanie</span><span class="sxs-lookup"><span data-stu-id="9caf9-287">EventCounters on request</span></span>

<span data-ttu-id="9caf9-288">Hosting EventSource, `Microsoft.AspNetCore.Hosting`, emituje następujące nowe <xref:System.Diagnostics.Tracing.EventCounter> typy związane z żądaniami przychodzącymi:</span><span class="sxs-lookup"><span data-stu-id="9caf9-288">The Hosting EventSource, `Microsoft.AspNetCore.Hosting`, emits the following new <xref:System.Diagnostics.Tracing.EventCounter> types related to incoming requests:</span></span>

* `requests-per-second`
* `total-requests`
* `current-requests`
* `failed-requests`

## <a name="endpoint-routing"></a><span data-ttu-id="9caf9-289">Routing punktów końcowych</span><span class="sxs-lookup"><span data-stu-id="9caf9-289">Endpoint routing</span></span>

<span data-ttu-id="9caf9-290">Routing punktów końcowych, który umożliwia platformom (na przykład MVC) współdziałanie z oprogramowanie pośredniczące, jest ulepszony:</span><span class="sxs-lookup"><span data-stu-id="9caf9-290">Endpoint Routing, which allows frameworks (for example, MVC) to work well with middleware, is enhanced:</span></span>

* <span data-ttu-id="9caf9-291">Kolejność programów pośredniczących i punktów końcowych można skonfigurować w potoku przetwarzania żądań `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="9caf9-291">The order of middleware and endpoints is configurable in the request processing pipeline of `Startup.Configure`.</span></span>
* <span data-ttu-id="9caf9-292">Punkty końcowe i oprogramowanie pośredniczące dobrze tworzą inne technologie ASP.NET Core, na przykład kontrole kondycji.</span><span class="sxs-lookup"><span data-stu-id="9caf9-292">Endpoints and middleware compose well with other ASP.NET Core-based technologies, such as Health Checks.</span></span>
* <span data-ttu-id="9caf9-293">Punkty końcowe mogą implementować zasady, takie jak CORS lub Authorization, zarówno w oprogramowaniu pośredniczącym, jak i MVC.</span><span class="sxs-lookup"><span data-stu-id="9caf9-293">Endpoints can implement a policy, such as CORS or authorization, in both middleware and MVC.</span></span>
* <span data-ttu-id="9caf9-294">Filtry i atrybuty mogą być umieszczane na metodach w kontrolerach.</span><span class="sxs-lookup"><span data-stu-id="9caf9-294">Filters and attributes can be placed on methods in controllers.</span></span>

<span data-ttu-id="9caf9-295">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/routing#routing-basics>.</span><span class="sxs-lookup"><span data-stu-id="9caf9-295">For more information, see <xref:fundamentals/routing#routing-basics>.</span></span>

## <a name="health-checks"></a><span data-ttu-id="9caf9-296">Kontrole kondycji</span><span class="sxs-lookup"><span data-stu-id="9caf9-296">Health Checks</span></span>

<span data-ttu-id="9caf9-297">Kontrole kondycji korzystają z routingu punktu końcowego z hostem ogólnym.</span><span class="sxs-lookup"><span data-stu-id="9caf9-297">Health Checks use endpoint routing with the Generic Host.</span></span> <span data-ttu-id="9caf9-298">W `Startup.Configure`programie Wywołaj `MapHealthChecks` program Endpoint Builder z adresem URL punktu końcowego lub ścieżką względną:</span><span class="sxs-lookup"><span data-stu-id="9caf9-298">In `Startup.Configure`, call `MapHealthChecks` on the endpoint builder with the endpoint URL or relative path:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

<span data-ttu-id="9caf9-299">Punkty końcowe sprawdzania kondycji mogą:</span><span class="sxs-lookup"><span data-stu-id="9caf9-299">Health Checks endpoints can:</span></span>

* <span data-ttu-id="9caf9-300">Określ jeden lub więcej dozwolonych hostów/portów.</span><span class="sxs-lookup"><span data-stu-id="9caf9-300">Specify one or more permitted hosts/ports.</span></span>
* <span data-ttu-id="9caf9-301">Wymagaj autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="9caf9-301">Require authorization.</span></span>
* <span data-ttu-id="9caf9-302">Wymagaj mechanizmu CORS.</span><span class="sxs-lookup"><span data-stu-id="9caf9-302">Require CORS.</span></span>

<span data-ttu-id="9caf9-303">Aby uzyskać więcej informacji zobacz następujące artykuły:</span><span class="sxs-lookup"><span data-stu-id="9caf9-303">For more information, see the following articles:</span></span>

* <xref:migration/22-to-30#health-checks>
* <xref:host-and-deploy/health-checks>

## <a name="pipes-on-httpcontext"></a><span data-ttu-id="9caf9-304">Potoki w obiekcie HttpContext</span><span class="sxs-lookup"><span data-stu-id="9caf9-304">Pipes on HttpContext</span></span>

<span data-ttu-id="9caf9-305">Teraz można odczytać treść żądania i napisać treść odpowiedzi przy użyciu <xref:System.IO.Pipelines> interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="9caf9-305">It's now possible to read the request body and write the response body using the <xref:System.IO.Pipelines> API.</span></span> <span data-ttu-id="9caf9-306">Dla zasobu</span><span class="sxs-lookup"><span data-stu-id="9caf9-306">The</span></span> <!-- <xref:Microsoft.AspNetCore.Http.HttpRequest.BodyReader> --> <span data-ttu-id="9caf9-307">`HttpRequest.BodyReader`Właściwość zawiera wartość <xref:System.IO.Pipelines.PipeReader> , która może być używana do odczytywania treści żądania.</span><span class="sxs-lookup"><span data-stu-id="9caf9-307">`HttpRequest.BodyReader` property provides a <xref:System.IO.Pipelines.PipeReader> that can be used to read the request body.</span></span> <span data-ttu-id="9caf9-308">Dla zasobu</span><span class="sxs-lookup"><span data-stu-id="9caf9-308">The</span></span> <!-- <xref:Microsoft.AspNetCore.Http.> --> <span data-ttu-id="9caf9-309">`HttpResponse.BodyWriter`Właściwość zawiera wartość <xref:System.IO.Pipelines.PipeWriter> , która może być używana do pisania treści odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="9caf9-309">`HttpResponse.BodyWriter` property provides a <xref:System.IO.Pipelines.PipeWriter> that can be used to write the response body.</span></span> <span data-ttu-id="9caf9-310">`HttpRequest.BodyReader`jest analogiczny do `HttpRequest.Body` strumienia.</span><span class="sxs-lookup"><span data-stu-id="9caf9-310">`HttpRequest.BodyReader` is an analogue of the `HttpRequest.Body` stream.</span></span> <span data-ttu-id="9caf9-311">`HttpResponse.BodyWriter`jest analogiczny do `HttpResponse.Body` strumienia.</span><span class="sxs-lookup"><span data-stu-id="9caf9-311">`HttpResponse.BodyWriter` is an analogue of the `HttpResponse.Body` stream.</span></span>

<!-- indirectly related, https://github.com/dotnet/docs/pull/14414 won't be published by 9/23  -->

## <a name="improved-error-reporting-in-iis"></a><span data-ttu-id="9caf9-312">Udoskonalone raportowanie błędów w usługach IIS</span><span class="sxs-lookup"><span data-stu-id="9caf9-312">Improved error reporting in IIS</span></span>

<span data-ttu-id="9caf9-313">Błędy uruchamiania podczas hostowania aplikacji ASP.NET Core w usługach IIS teraz generują bogatsze dane diagnostyczne.</span><span class="sxs-lookup"><span data-stu-id="9caf9-313">Startup errors when hosting ASP.NET Core apps in IIS now produce richer diagnostic data.</span></span> <span data-ttu-id="9caf9-314">Te błędy są zgłaszane w dzienniku zdarzeń systemu Windows przy użyciu śladów stosu wszędzie tam, gdzie ma to zastosowanie.</span><span class="sxs-lookup"><span data-stu-id="9caf9-314">These errors are reported to the Windows Event Log with stack traces wherever applicable.</span></span> <span data-ttu-id="9caf9-315">Ponadto wszystkie ostrzeżenia, błędy i Nieobsłużone wyjątki są rejestrowane w dzienniku zdarzeń systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="9caf9-315">In addition, all warnings, errors, and unhandled exceptions are logged to the Windows Event Log.</span></span>

## <a name="worker-service-and-worker-sdk"></a><span data-ttu-id="9caf9-316">Zestaw SDK usługi i procesu roboczego</span><span class="sxs-lookup"><span data-stu-id="9caf9-316">Worker Service and Worker SDK</span></span>

<span data-ttu-id="9caf9-317">W programie .NET Core 3,0 wprowadzono nowy szablon aplikacji usługi Worker.</span><span class="sxs-lookup"><span data-stu-id="9caf9-317">.NET Core 3.0 introduces the new Worker Service app template.</span></span> <span data-ttu-id="9caf9-318">Ten szablon zawiera punkt początkowy do pisania długotrwałych usług w programie .NET Core.</span><span class="sxs-lookup"><span data-stu-id="9caf9-318">This template provides a starting point for writing long running services in .NET Core.</span></span>

<span data-ttu-id="9caf9-319">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="9caf9-319">For more information, see:</span></span>

* [<span data-ttu-id="9caf9-320">Pracownicy .NET Core jako usługi systemu Windows</span><span class="sxs-lookup"><span data-stu-id="9caf9-320">.NET Core Workers as Windows Services</span></span>](https://devblogs.microsoft.com/aspnet/net-core-workers-as-windows-services/)
* <xref:fundamentals/host/hosted-services>
* <xref:host-and-deploy/windows-service>

## <a name="forwarded-headers-middleware-improvements"></a><span data-ttu-id="9caf9-321">Podniesienie — ulepszenia oprogramowania pośredniczącego</span><span class="sxs-lookup"><span data-stu-id="9caf9-321">Forwarded Headers Middleware improvements</span></span>

<span data-ttu-id="9caf9-322">W poprzednich wersjach ASP.NET Core wywoływanie <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> i <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> problemy występujące podczas wdrażania w systemie Linux systemu Azure lub poza serwerem proxy innych niż usługi IIS.</span><span class="sxs-lookup"><span data-stu-id="9caf9-322">In previous versions of ASP.NET Core, calling <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> and  <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> were problematic when deployed to an Azure Linux or behind any reverse proxy other than IIS.</span></span> <span data-ttu-id="9caf9-323">Poprawka dla poprzednich wersji została udokumentowana w [tym samym schemacie dla systemu Linux i zwrotnych serwerów proxy innych niż IIS](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies).</span><span class="sxs-lookup"><span data-stu-id="9caf9-323">The fix for previous versions is documented in [Forward the scheme for Linux and non-IIS reverse proxies](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies).</span></span>

<span data-ttu-id="9caf9-324">Ten scenariusz jest ustalony w ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="9caf9-324">This scenario is fixed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="9caf9-325">Host włącza program [pośredniczący z przekierowanymi nagłówkami](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) , `ASPNETCORE_FORWARDEDHEADERS_ENABLED` gdy zmienna środowiskowa jest ustawiona na `true`.</span><span class="sxs-lookup"><span data-stu-id="9caf9-325">The host enables the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) when the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span> <span data-ttu-id="9caf9-326">`ASPNETCORE_FORWARDEDHEADERS_ENABLED`jest ustawiona na `true` w naszym obrazach kontenera.</span><span class="sxs-lookup"><span data-stu-id="9caf9-326">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` is set to `true` in our container images.</span></span>

## <a name="performance-improvements"></a><span data-ttu-id="9caf9-327">Ulepszenia wydajności</span><span class="sxs-lookup"><span data-stu-id="9caf9-327">Performance improvements</span></span>

<span data-ttu-id="9caf9-328">ASP.NET Core 3,0 zawiera wiele ulepszeń, które zmniejszają wykorzystanie pamięci i zwiększają przepływność:</span><span class="sxs-lookup"><span data-stu-id="9caf9-328">ASP.NET Core 3.0 includes many improvements that reduce memory usage and improve throughput:</span></span>

* <span data-ttu-id="9caf9-329">Zmniejszenie użycia pamięci w przypadku korzystania z wbudowanego kontenera iniekcji zależności dla usług objętych zakresem.</span><span class="sxs-lookup"><span data-stu-id="9caf9-329">Reduction in memory usage when using the built-in dependency injection container for scoped services.</span></span>
* <span data-ttu-id="9caf9-330">Zmniejszenie alokacji w całym środowisku, w tym scenariusze i Routing oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="9caf9-330">Reduction in allocations across the framework, including middleware scenarios and routing.</span></span>
* <span data-ttu-id="9caf9-331">Zmniejszenie użycia pamięci dla połączeń protokołu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="9caf9-331">Reduction in memory usage for WebSocket connections.</span></span>
* <span data-ttu-id="9caf9-332">Zmniejszenie ilości pamięci i ulepszenia przepływności dla połączeń HTTPS.</span><span class="sxs-lookup"><span data-stu-id="9caf9-332">Memory reduction and throughput improvements for HTTPS connections.</span></span>
* <span data-ttu-id="9caf9-333">Nowy, zoptymalizowany i w pełni asynchroniczny serializator JSON.</span><span class="sxs-lookup"><span data-stu-id="9caf9-333">New optimized and fully asynchronous JSON serializer.</span></span>
* <span data-ttu-id="9caf9-334">Zmniejszenie użycia pamięci i ulepszeń przepływności podczas analizowania formularzy.</span><span class="sxs-lookup"><span data-stu-id="9caf9-334">Reduction in memory usage and throughput improvements in form parsing.</span></span>

## <a name="aspnet-core-30-only-runs-on-net-core-30"></a><span data-ttu-id="9caf9-335">ASP.NET Core 3,0 działa tylko na platformie .NET Core 3,0</span><span class="sxs-lookup"><span data-stu-id="9caf9-335">ASP.NET Core 3.0 only runs on .NET Core 3.0</span></span>

<span data-ttu-id="9caf9-336">Począwszy od ASP.NET Core 3,0, .NET Framework nie jest już obsługiwaną platformą docelową.</span><span class="sxs-lookup"><span data-stu-id="9caf9-336">As of ASP.NET Core 3.0, .NET Framework is no longer a supported target framework.</span></span> <span data-ttu-id="9caf9-337">Projekty ukierunkowane na .NET Framework mogą być nadal w pełni obsługiwane przy użyciu programu [.NET Core 2,1 LTS Release](https://dotnet.microsoft.com/download/dotnet-core/2.1).</span><span class="sxs-lookup"><span data-stu-id="9caf9-337">Projects targeting .NET Framework can continue in a fully supported fashion using the [.NET Core 2.1 LTS release](https://dotnet.microsoft.com/download/dotnet-core/2.1).</span></span> <span data-ttu-id="9caf9-338">Większość pakietów pokrewnych ASP.NET Core 2.1. x będzie obsługiwana przez czas dłuższy niż rok LTS dla platformy .NET Core 2,1.</span><span class="sxs-lookup"><span data-stu-id="9caf9-338">Most ASP.NET Core 2.1.x related packages will be supported indefinitely, beyond the three-year LTS period for .NET Core 2.1.</span></span>

<span data-ttu-id="9caf9-339">Informacje o migracji znajdują się w temacie [Porting Code from .NET Framework to .NET Core](/dotnet/core/porting/).</span><span class="sxs-lookup"><span data-stu-id="9caf9-339">For migration information, see [Port your code from .NET Framework to .NET Core](/dotnet/core/porting/).</span></span>

## <a name="use-the-aspnet-core-shared-framework"></a><span data-ttu-id="9caf9-340">Użyj ASP.NET Core udostępnionej platformy</span><span class="sxs-lookup"><span data-stu-id="9caf9-340">Use the ASP.NET Core shared framework</span></span>

<span data-ttu-id="9caf9-341">Współdzielona struktura ASP.NET Core 3,0, która znajduje się w [pakiecie Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), nie wymaga już jawnego `<PackageReference />` elementu w pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="9caf9-341">The ASP.NET Core 3.0 shared framework, contained in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), no longer requires an explicit `<PackageReference />` element in the project file.</span></span> <span data-ttu-id="9caf9-342">W przypadku korzystania z `Microsoft.NET.Sdk.Web` zestawu SDK w pliku projektu automatycznie występuje odwołanie do udostępnionej struktury:</span><span class="sxs-lookup"><span data-stu-id="9caf9-342">The shared framework is automatically referenced when using the `Microsoft.NET.Sdk.Web` SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

## <a name="assemblies-removed-from-the-aspnet-core-shared-framework"></a><span data-ttu-id="9caf9-343">Zestawy usunięte z ASP.NET Core udostępnionej platformy</span><span class="sxs-lookup"><span data-stu-id="9caf9-343">Assemblies removed from the ASP.NET Core shared framework</span></span>

<span data-ttu-id="9caf9-344">Najbardziej istotnymi zestawami usuniętymi ze współdzielonej platformy ASP.NET Core 3,0 są:</span><span class="sxs-lookup"><span data-stu-id="9caf9-344">The most notable assemblies removed from the ASP.NET Core 3.0 shared framework are:</span></span>

* <span data-ttu-id="9caf9-345">[Newtonsoft. JSON](https://www.nuget.org/packages/Newtonsoft.Json/) (JSON.NET).</span><span class="sxs-lookup"><span data-stu-id="9caf9-345">[Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) (Json.NET).</span></span> <span data-ttu-id="9caf9-346">Aby dodać Json.NET do ASP.NET Core 3,0, zobacz [Dodawanie obsługi formatu JSON opartego na Newtonsoft. JSON](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span><span class="sxs-lookup"><span data-stu-id="9caf9-346">To add Json.NET to ASP.NET Core 3.0, see [Add Newtonsoft.Json-based JSON format support](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span></span> <span data-ttu-id="9caf9-347">ASP.NET Core 3,0 wprowadza `System.Text.Json` do odczytu i zapisu JSON.</span><span class="sxs-lookup"><span data-stu-id="9caf9-347">ASP.NET Core 3.0 introduces `System.Text.Json` for reading and writing JSON.</span></span> <span data-ttu-id="9caf9-348">Aby uzyskać więcej informacji, zobacz [Nowa Serializacja kodu JSON](#new-json-serialization) w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="9caf9-348">For more information, see [New JSON serialization](#new-json-serialization) in this document.</span></span>
* [<span data-ttu-id="9caf9-349">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="9caf9-349">Entity Framework Core</span></span>](/ef/core/)

<span data-ttu-id="9caf9-350">Aby uzyskać pełną listę zestawów usuniętych z udostępnionej platformy, zobacz [zestawy usuwane z Microsoft. AspNetCore. App 3,0](https://github.com/dotnet/AspNetCore/issues/3755).</span><span class="sxs-lookup"><span data-stu-id="9caf9-350">For a complete list of assemblies removed from the shared framework, see [Assemblies being removed from Microsoft.AspNetCore.App 3.0](https://github.com/dotnet/AspNetCore/issues/3755).</span></span> <span data-ttu-id="9caf9-351">Aby uzyskać więcej informacji na temat motywacji dotyczącej tej zmiany, zobacz artykuł [dotyczący zmiany w firmie Microsoft. AspNetCore. App w 3,0](https://github.com/aspnet/Announcements/issues/325) i [pierwsze spojrzenie na zmiany wprowadzone w ASP.NET Core 3,0](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span><span class="sxs-lookup"><span data-stu-id="9caf9-351">For more information on the motivation for this change, see [Breaking changes to Microsoft.AspNetCore.App in 3.0](https://github.com/aspnet/Announcements/issues/325) and [A first look at changes coming in ASP.NET Core 3.0](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span></span>

<!-- 
## Additional information
For the complete list of changes, see the [ASP.NET Core 3.0 Release Notes](WHERE IS THIS????).
-->
 