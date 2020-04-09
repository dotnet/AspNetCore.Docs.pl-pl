---
title: Co nowego w ASP.NET Core 3.0
author: rick-anderson
description: Dowiedz się więcej o nowych funkcjach w ASP.NET Core 3.0.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- SignalR
uid: aspnetcore-3.0
ms.openlocfilehash: 4886673a9b16b8be8d9a0b0d5c7002a91760544e
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80976979"
---
# <a name="whats-new-in-aspnet-core-30"></a><span data-ttu-id="7c777-103">Co nowego w ASP.NET Core 3.0</span><span class="sxs-lookup"><span data-stu-id="7c777-103">What's new in ASP.NET Core 3.0</span></span>

<span data-ttu-id="7c777-104">W tym artykule przedstawiono najważniejsze zmiany w ASP.NET Core 3.0 z łączami do odpowiedniej dokumentacji.</span><span class="sxs-lookup"><span data-stu-id="7c777-104">This article highlights the most significant changes in ASP.NET Core 3.0 with links to relevant documentation.</span></span>

## Blazor

Blazor<span data-ttu-id="7c777-105">to nowa struktura w ASP.NET Core do tworzenia interaktywnego interfejsu użytkownika sieci web po stronie klienta za pomocą platformy .NET:</span><span class="sxs-lookup"><span data-stu-id="7c777-105"> is a new framework in ASP.NET Core for building interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="7c777-106">Tworzenie rozbudowanych interaktywnych interfejsów użytkownika przy użyciu języka C# zamiast javascript.</span><span class="sxs-lookup"><span data-stu-id="7c777-106">Create rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="7c777-107">Udostępnianie logiki aplikacji po stronie serwera i po stronie klienta napisanej w programie .NET.</span><span class="sxs-lookup"><span data-stu-id="7c777-107">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="7c777-108">Renderuj interfejs użytkownika jako HTML i CSS, aby uzyskać szeroką obsługę przeglądarki, w tym przeglądarek mobilnych.</span><span class="sxs-lookup"><span data-stu-id="7c777-108">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>

Blazor<span data-ttu-id="7c777-109">scenariuszy obsługiwanych przez ramy prawne:</span><span class="sxs-lookup"><span data-stu-id="7c777-109"> framework supported scenarios:</span></span>

* <span data-ttu-id="7c777-110">Komponenty interfejsu użytkownika wielokrotnego rozrządu wielokrotnego rozrządu (komponenty razor)</span><span class="sxs-lookup"><span data-stu-id="7c777-110">Reusable UI components (Razor components)</span></span>
* <span data-ttu-id="7c777-111">Routing po stronie klienta</span><span class="sxs-lookup"><span data-stu-id="7c777-111">Client-side routing</span></span>
* <span data-ttu-id="7c777-112">Układy komponentów</span><span class="sxs-lookup"><span data-stu-id="7c777-112">Component layouts</span></span>
* <span data-ttu-id="7c777-113">Obsługa iniekcji zależności</span><span class="sxs-lookup"><span data-stu-id="7c777-113">Support for dependency injection</span></span>
* <span data-ttu-id="7c777-114">Formularze i walidacja</span><span class="sxs-lookup"><span data-stu-id="7c777-114">Forms and validation</span></span>
* <span data-ttu-id="7c777-115">Tworzenie bibliotek składników za pomocą bibliotek klas Razor</span><span class="sxs-lookup"><span data-stu-id="7c777-115">Build component libraries with Razor class libraries</span></span>
* <span data-ttu-id="7c777-116">Międzyoperacyjność w języku JavaScript</span><span class="sxs-lookup"><span data-stu-id="7c777-116">JavaScript interop</span></span>

<span data-ttu-id="7c777-117">Aby uzyskać więcej informacji, zobacz <xref:blazor/index>.</span><span class="sxs-lookup"><span data-stu-id="7c777-117">For more information, see <xref:blazor/index>.</span></span>

### <a name="opno-locblazor-server"></a>Blazor<span data-ttu-id="7c777-118">Serwera</span><span class="sxs-lookup"><span data-stu-id="7c777-118"> Server</span></span>

Blazor<span data-ttu-id="7c777-119">oddziela logikę renderowania składników od sposobu stosowania aktualizacji interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="7c777-119"> decouples component rendering logic from how UI updates are applied.</span></span> Blazor<span data-ttu-id="7c777-120">Serwer zapewnia obsługę hostowania składników Razor na serwerze w aplikacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7c777-120"> Server provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="7c777-121">Aktualizacje interfejsu użytkownika są SignalR obsługiwane przez połączenie.</span><span class="sxs-lookup"><span data-stu-id="7c777-121">UI updates are handled over a SignalR connection.</span></span> Blazor<span data-ttu-id="7c777-122">Serwer jest obsługiwany w ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="7c777-122"> Server is supported in ASP.NET Core 3.0.</span></span>

### <a name="opno-locblazor-webassembly-preview"></a>Blazor<span data-ttu-id="7c777-123">WebAssembly (wersja zapoznawcza)</span><span class="sxs-lookup"><span data-stu-id="7c777-123"> WebAssembly (Preview)</span></span>

Blazor<span data-ttu-id="7c777-124">aplikacje można również uruchamiać bezpośrednio w przeglądarce przy użyciu środowiska uruchomieniowego .NET opartego na webassembly.</span><span class="sxs-lookup"><span data-stu-id="7c777-124"> apps can also be run directly in the browser using a WebAssembly-based .NET runtime.</span></span> Blazor<span data-ttu-id="7c777-125">WebAssembly jest w wersji zapoznawczej i *nie* jest obsługiwany w ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="7c777-125"> WebAssembly is in preview and *not* supported in ASP.NET Core 3.0.</span></span> Blazor<span data-ttu-id="7c777-126">WebAssembly będzie obsługiwany w przyszłej wersji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="7c777-126"> WebAssembly will be supported in a future release of ASP.NET Core.</span></span>

### <a name="razor-components"></a><span data-ttu-id="7c777-127">Elementy maszynki do golenia</span><span class="sxs-lookup"><span data-stu-id="7c777-127">Razor components</span></span>

Blazor<span data-ttu-id="7c777-128">aplikacje są zbudowane z komponentów.</span><span class="sxs-lookup"><span data-stu-id="7c777-128"> apps are built from components.</span></span> <span data-ttu-id="7c777-129">Składniki są samodzielnymi fragmentami interfejsu użytkownika ,, takimi jak strona, okno dialogowe lub formularz.</span><span class="sxs-lookup"><span data-stu-id="7c777-129">Components are self-contained chunks of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="7c777-130">Składniki są normalne klasy .NET, które definiują logikę renderowania interfejsu użytkownika i obsługi zdarzeń po stronie klienta.</span><span class="sxs-lookup"><span data-stu-id="7c777-130">Components are normal .NET classes that define UI rendering logic and client-side event handlers.</span></span> <span data-ttu-id="7c777-131">Możesz tworzyć bogate interaktywne aplikacje internetowe bez javascriptu.</span><span class="sxs-lookup"><span data-stu-id="7c777-131">You can create rich interactive web apps without JavaScript.</span></span>

<span data-ttu-id="7c777-132">Składniki Blazor są zazwyczaj tworzone przy użyciu składni Razor, naturalne połączenie HTML i C#.</span><span class="sxs-lookup"><span data-stu-id="7c777-132">Components in Blazor are typically authored using Razor syntax, a natural blend of HTML and C#.</span></span> <span data-ttu-id="7c777-133">Komponenty brzytwy są podobne do stron Razor i widoków MVC, ponieważ obie używają Razor.</span><span class="sxs-lookup"><span data-stu-id="7c777-133">Razor components are similar to Razor Pages and MVC views in that they both use Razor.</span></span> <span data-ttu-id="7c777-134">W przeciwieństwie do stron i widoków, które są oparte na modelu żądanie odpowiedź, składniki są używane specjalnie do obsługi kompozycji interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="7c777-134">Unlike pages and views, which are based on a request-response model, components are used specifically for handling UI composition.</span></span>

## <a name="grpc"></a><span data-ttu-id="7c777-135">gRPC</span><span class="sxs-lookup"><span data-stu-id="7c777-135">gRPC</span></span>

<span data-ttu-id="7c777-136">[gRPC](https://grpc.io/):</span><span class="sxs-lookup"><span data-stu-id="7c777-136">[gRPC](https://grpc.io/):</span></span>

* <span data-ttu-id="7c777-137">Jest popularną, wysokowydajną strukturą RPC (zdalne wywołanie procedury).</span><span class="sxs-lookup"><span data-stu-id="7c777-137">Is a popular, high-performance RPC (remote procedure call) framework.</span></span>
* <span data-ttu-id="7c777-138">Oferuje opinionated umowy pierwszy podejście do tworzenia interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="7c777-138">Offers an opinionated contract-first approach to API development.</span></span>
* <span data-ttu-id="7c777-139">Wykorzystuje nowoczesne technologie, takie jak:</span><span class="sxs-lookup"><span data-stu-id="7c777-139">Uses modern technologies such as:</span></span>

  * <span data-ttu-id="7c777-140">HTTP/2 dla transportu.</span><span class="sxs-lookup"><span data-stu-id="7c777-140">HTTP/2 for transport.</span></span>
  * <span data-ttu-id="7c777-141">Bufory protokołu jako język opisu interfejsu.</span><span class="sxs-lookup"><span data-stu-id="7c777-141">Protocol Buffers as the interface description language.</span></span>
  * <span data-ttu-id="7c777-142">Binarny format serializacji.</span><span class="sxs-lookup"><span data-stu-id="7c777-142">Binary serialization format.</span></span>
* <span data-ttu-id="7c777-143">Udostępnia funkcje, takie jak:</span><span class="sxs-lookup"><span data-stu-id="7c777-143">Provides features such as:</span></span>

  * <span data-ttu-id="7c777-144">Authentication</span><span class="sxs-lookup"><span data-stu-id="7c777-144">Authentication</span></span>
  * <span data-ttu-id="7c777-145">Dwukierunkowe przesyłanie strumieniowe i sterowanie przepływem.</span><span class="sxs-lookup"><span data-stu-id="7c777-145">Bidirectional streaming and flow control.</span></span>
  * <span data-ttu-id="7c777-146">Anulowanie i limity czasu.</span><span class="sxs-lookup"><span data-stu-id="7c777-146">Cancellation and timeouts.</span></span>

<span data-ttu-id="7c777-147">Funkcja gRPC w ASP.NET Core 3.0 obejmuje:</span><span class="sxs-lookup"><span data-stu-id="7c777-147">gRPC functionality in ASP.NET Core 3.0 includes:</span></span>

* <span data-ttu-id="7c777-148">[Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) &ndash; ASP.NET podstawową strukturą do hostingu usług gRPC.</span><span class="sxs-lookup"><span data-stu-id="7c777-148">[Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) &ndash; An ASP.NET Core framework for hosting gRPC services.</span></span> <span data-ttu-id="7c777-149">GRPC na ASP.NET Core integruje się ze standardowymi ASP.NET podstawowe funkcje, takie jak rejestrowanie, iniekcja zależności (DI), uwierzytelnianie i autoryzacja.</span><span class="sxs-lookup"><span data-stu-id="7c777-149">gRPC on ASP.NET Core integrates with standard ASP.NET Core features like logging, dependency injection (DI), authentication, and authorization.</span></span>
* <span data-ttu-id="7c777-150">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) &ndash; Klient gRPC dla .NET Core, `HttpClient`który opiera się na znanych . .</span><span class="sxs-lookup"><span data-stu-id="7c777-150">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) &ndash; A gRPC client for .NET Core that builds upon the familiar `HttpClient`.</span></span>
* <span data-ttu-id="7c777-151">[Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) &ndash; gRPC integracji klienta z `HttpClientFactory`.</span><span class="sxs-lookup"><span data-stu-id="7c777-151">[Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) &ndash; gRPC client integration with `HttpClientFactory`.</span></span>

<span data-ttu-id="7c777-152">Aby uzyskać więcej informacji, zobacz <xref:grpc/index>.</span><span class="sxs-lookup"><span data-stu-id="7c777-152">For more information, see <xref:grpc/index>.</span></span>

## SignalR

<span data-ttu-id="7c777-153">Zobacz [ SignalR Aktualizowanie kodu,](xref:migration/22-to-30#signalr) aby uzyskać instrukcje dotyczące migracji.</span><span class="sxs-lookup"><span data-stu-id="7c777-153">See [Update SignalR code](xref:migration/22-to-30#signalr) for migration instructions.</span></span> SignalR<span data-ttu-id="7c777-154">teraz używa `System.Text.Json` do serializacji/deserializacji komunikatów JSON.</span><span class="sxs-lookup"><span data-stu-id="7c777-154"> now uses `System.Text.Json` to serialize/deserialize JSON messages.</span></span> <span data-ttu-id="7c777-155">Zobacz [Switch do Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson) instrukcje `Newtonsoft.Json`przywracania serializatora opartego na.</span><span class="sxs-lookup"><span data-stu-id="7c777-155">See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson) for instructions to restore the `Newtonsoft.Json`-based serializer.</span></span>

<span data-ttu-id="7c777-156">W językach JavaScript i SignalR.NET Klienci dla , dodano obsługę automatycznego ponownego połączenia.</span><span class="sxs-lookup"><span data-stu-id="7c777-156">In the JavaScript and .NET Clients for SignalR, support was added for automatic reconnection.</span></span> <span data-ttu-id="7c777-157">Domyślnie klient próbuje natychmiast ponownie połączyć się i ponowić próbę po 2, 10 i 30 sekund, jeśli to konieczne.</span><span class="sxs-lookup"><span data-stu-id="7c777-157">By default, the client tries to reconnect immediately and retry after 2, 10, and 30 seconds if necessary.</span></span> <span data-ttu-id="7c777-158">Jeśli klient pomyślnie połączy się ponownie, otrzyma nowy identyfikator połączenia.</span><span class="sxs-lookup"><span data-stu-id="7c777-158">If the client successfully reconnects, it receives a new connection ID.</span></span> <span data-ttu-id="7c777-159">Automatyczne ponowne łączenie jest zgłodne:</span><span class="sxs-lookup"><span data-stu-id="7c777-159">Automatic reconnect is opt-in:</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

<span data-ttu-id="7c777-160">Interwały ponownego połączenia można określić, przekazując tablicę milowych czasów trwania:</span><span class="sxs-lookup"><span data-stu-id="7c777-160">The reconnection intervals can be specified by passing an array of millisecond-based durations:</span></span>

```javascript
.withAutomaticReconnect([0, 3000, 5000, 10000, 15000, 30000])
//.withAutomaticReconnect([0, 2000, 10000, 30000]) The default intervals.
```

<span data-ttu-id="7c777-161">Implementacja niestandardowa może być przekazywana w celu pełnej kontroli interwałów ponownego połączenia.</span><span class="sxs-lookup"><span data-stu-id="7c777-161">A custom implementation can be passed in for full control of the reconnection intervals.</span></span>

<span data-ttu-id="7c777-162">Jeśli ponowne połączenie nie powiedzie się po ostatnim interwale ponownego nawiązania połączenia:</span><span class="sxs-lookup"><span data-stu-id="7c777-162">If the reconnection fails after the last reconnect interval:</span></span>

* <span data-ttu-id="7c777-163">Klient uważa, że połączenie jest w trybie offline.</span><span class="sxs-lookup"><span data-stu-id="7c777-163">The client considers the connection is offline.</span></span>
* <span data-ttu-id="7c777-164">Klient przestaje próbować ponownie nawiązać połączenie.</span><span class="sxs-lookup"><span data-stu-id="7c777-164">The client stops trying to reconnect.</span></span>

<span data-ttu-id="7c777-165">Podczas prób ponownego połączenia zaktualizuj interfejs użytkownika aplikacji, aby powiadomić użytkownika o próbie ponownego połączenia.</span><span class="sxs-lookup"><span data-stu-id="7c777-165">During reconnection attempts, update the app UI to notify the user that the reconnection is being attempted.</span></span>

<span data-ttu-id="7c777-166">Aby zapewnić opinie interfejsu użytkownika po przerwaniu połączenia, interfejs API SignalR klienta został rozwinięty w celu uwzględnienia następujących programów obsługi zdarzeń:</span><span class="sxs-lookup"><span data-stu-id="7c777-166">To provide UI feedback when the connection is interrupted, the SignalR client API has been expanded to include the following event handlers:</span></span>

* <span data-ttu-id="7c777-167">`onreconnecting`: Daje deweloperom możliwość wyłączenia interfejsu użytkownika lub poinformowania użytkowników, że aplikacja jest w trybie offline.</span><span class="sxs-lookup"><span data-stu-id="7c777-167">`onreconnecting`:  Gives developers an opportunity to disable UI or to let users know the app is offline.</span></span>
* <span data-ttu-id="7c777-168">`onreconnected`: Daje deweloperom możliwość aktualizacji interfejsu użytkownika po przywróceniu połączenia.</span><span class="sxs-lookup"><span data-stu-id="7c777-168">`onreconnected`: Gives developers an opportunity to update the UI once the connection is reestablished.</span></span>

<span data-ttu-id="7c777-169">Następujący kod używa `onreconnecting` do aktualizacji interfejsu użytkownika podczas próby połączenia:</span><span class="sxs-lookup"><span data-stu-id="7c777-169">The following code uses `onreconnecting` to update the UI while trying to connect:</span></span>

```javascript
connection.onreconnecting((error) => {
    const status = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messageInput").disabled = true;
    document.getElementById("sendButton").disabled = true;
    document.getElementById("connectionStatus").innerText = status;
});
```

<span data-ttu-id="7c777-170">Następujący kod służy `onreconnected` do aktualizowania interfejsu użytkownika w połączeniu:</span><span class="sxs-lookup"><span data-stu-id="7c777-170">The following code uses `onreconnected` to update the UI on connection:</span></span>

```javascript
connection.onreconnected((connectionId) => {
    const status = `Connection reestablished. Connected.`;
    document.getElementById("messageInput").disabled = false;
    document.getElementById("sendButton").disabled = false;
    document.getElementById("connectionStatus").innerText = status;
});
```

SignalR<span data-ttu-id="7c777-171">3.0 i nowsze udostępnia niestandardowy zasób do obsługi autoryzacji, gdy metoda koncentratora wymaga autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="7c777-171"> 3.0 and later provides a custom resource to authorization handlers when a hub method requires authorization.</span></span> <span data-ttu-id="7c777-172">Zasób jest `HubInvocationContext`wystąpieniem pliku .</span><span class="sxs-lookup"><span data-stu-id="7c777-172">The resource is an instance of `HubInvocationContext`.</span></span> <span data-ttu-id="7c777-173">Obejmuje `HubInvocationContext` ona:</span><span class="sxs-lookup"><span data-stu-id="7c777-173">The `HubInvocationContext` includes the:</span></span>

* `HubCallerContext`
* <span data-ttu-id="7c777-174">Nazwa wywoływanej metody koncentratora.</span><span class="sxs-lookup"><span data-stu-id="7c777-174">Name of the hub method being invoked.</span></span>
* <span data-ttu-id="7c777-175">Argumenty do metody koncentratora.</span><span class="sxs-lookup"><span data-stu-id="7c777-175">Arguments to the hub method.</span></span>

<span data-ttu-id="7c777-176">Rozważmy poniższy przykład aplikacji pokoju rozmów umożliwiającej logowanie się do wielu organizacji za pośrednictwem usługi Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="7c777-176">Consider the following example of a chat room app allowing multiple organization sign-in via Azure Active Directory.</span></span> <span data-ttu-id="7c777-177">Każda osoba posiadająca konto Microsoft może zalogować się na czacie, ale tylko członkowie organizacji stanowiącej właścicielki mogą zablokować użytkowników lub wyświetlić historie czatów użytkowników.</span><span class="sxs-lookup"><span data-stu-id="7c777-177">Anyone with a Microsoft account can sign in to chat, but only members of the owning organization can ban users or view users' chat histories.</span></span> <span data-ttu-id="7c777-178">Aplikacja może ograniczyć niektóre funkcje od określonych użytkowników.</span><span class="sxs-lookup"><span data-stu-id="7c777-178">The app could restrict certain functionality from specific users.</span></span>

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

<span data-ttu-id="7c777-179">W poprzednim kodzie `DomainRestrictedRequirement` służy jako `IAuthorizationRequirement`niestandardowy .</span><span class="sxs-lookup"><span data-stu-id="7c777-179">In the preceding code, `DomainRestrictedRequirement` serves as a custom `IAuthorizationRequirement`.</span></span> <span data-ttu-id="7c777-180">Ponieważ `HubInvocationContext` parametr zasobu jest przekazywany, logika wewnętrzna może:</span><span class="sxs-lookup"><span data-stu-id="7c777-180">Because the `HubInvocationContext` resource parameter is being passed in, the internal logic can:</span></span>

* <span data-ttu-id="7c777-181">Sprawdź kontekst, w którym jest wywoływana hub.</span><span class="sxs-lookup"><span data-stu-id="7c777-181">Inspect the context in which the Hub is being called.</span></span>
* <span data-ttu-id="7c777-182">Podejmuj decyzje dotyczące zezwalania użytkownikowi na wykonywanie poszczególnych metod centrum.</span><span class="sxs-lookup"><span data-stu-id="7c777-182">Make decisions on allowing the user to execute individual Hub methods.</span></span>

<span data-ttu-id="7c777-183">Poszczególne metody centrum mogą być oznaczone nazwą zasad sprawdza kod w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="7c777-183">Individual Hub methods can be marked with the name of the policy the code checks at run-time.</span></span> <span data-ttu-id="7c777-184">Gdy klienci próbują wywołać poszczególne `DomainRestrictedRequirement` metody centrum, program obsługi uruchamia i kontroluje dostęp do metod.</span><span class="sxs-lookup"><span data-stu-id="7c777-184">As clients attempt to call individual Hub methods, the `DomainRestrictedRequirement` handler runs and controls access to the methods.</span></span> <span data-ttu-id="7c777-185">W zależności od `DomainRestrictedRequirement` sposobu, w jaki kontroluje dostęp:</span><span class="sxs-lookup"><span data-stu-id="7c777-185">Based on the way the `DomainRestrictedRequirement` controls access:</span></span>

* <span data-ttu-id="7c777-186">Wszyscy zalogowani użytkownicy mogą `SendMessage` wywołać tę metodę.</span><span class="sxs-lookup"><span data-stu-id="7c777-186">All logged-in users can call the `SendMessage` method.</span></span>
* <span data-ttu-id="7c777-187">Tylko użytkownicy, którzy zalogowali się przy za pomocą adresu `@jabbr.net` e-mail, mogą wyświetlać historie użytkowników.</span><span class="sxs-lookup"><span data-stu-id="7c777-187">Only users who have logged in with a `@jabbr.net` email address can view users' histories.</span></span>
* <span data-ttu-id="7c777-188">Tylko `bob42@jabbr.net` może zablokować użytkowników z pokoju rozmów.</span><span class="sxs-lookup"><span data-stu-id="7c777-188">Only `bob42@jabbr.net` can ban users from the chat room.</span></span>

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

<span data-ttu-id="7c777-189">Tworzenie `DomainRestricted` zasad może obejmować:</span><span class="sxs-lookup"><span data-stu-id="7c777-189">Creating the `DomainRestricted` policy might involve:</span></span>

* <span data-ttu-id="7c777-190">W *Startup.cs*, dodając nową politykę.</span><span class="sxs-lookup"><span data-stu-id="7c777-190">In *Startup.cs*, adding the new policy.</span></span>
* <span data-ttu-id="7c777-191">Podaj `DomainRestrictedRequirement` wymagania niestandardowe jako parametr.</span><span class="sxs-lookup"><span data-stu-id="7c777-191">Provide the custom `DomainRestrictedRequirement` requirement as a parameter.</span></span>
* <span data-ttu-id="7c777-192">Rejestracja `DomainRestricted` w oprogramowaniu pośredniczącym autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="7c777-192">Registering `DomainRestricted` with the authorization middleware.</span></span>

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

SignalR<span data-ttu-id="7c777-193">koncentratorów korzysta z [usługi Endpoint Routing](xref:fundamentals/routing).</span><span class="sxs-lookup"><span data-stu-id="7c777-193"> hubs use [Endpoint Routing](xref:fundamentals/routing).</span></span> SignalR<span data-ttu-id="7c777-194">połączenie koncentratora zostało wcześniej wykonane jawnie:</span><span class="sxs-lookup"><span data-stu-id="7c777-194"> hub connection was previously done explicitly:</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});
```

<span data-ttu-id="7c777-195">W poprzedniej wersji deweloperzy musieli podłączyć kontrolery, strony Razor i koncentratory w różnych miejscach.</span><span class="sxs-lookup"><span data-stu-id="7c777-195">In the previous version, developers needed to wire up controllers, Razor pages, and hubs in a variety of places.</span></span> <span data-ttu-id="7c777-196">Jawne połączenie powoduje serię niemal identycznych segmentów routingu:</span><span class="sxs-lookup"><span data-stu-id="7c777-196">Explicit connection results in a series of nearly-identical routing segments:</span></span>

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

SignalR<span data-ttu-id="7c777-197">Koncentratory 3.0 mogą być kierowane za pośrednictwem routingu punktu końcowego.</span><span class="sxs-lookup"><span data-stu-id="7c777-197"> 3.0 hubs can be routed via endpoint routing.</span></span> <span data-ttu-id="7c777-198">W celu prowadzenia routingu w punktach końcowych `UseRouting`zazwyczaj wszystkie routing można skonfigurować w :</span><span class="sxs-lookup"><span data-stu-id="7c777-198">With endpoint routing, typically all routing can be configured in `UseRouting`:</span></span>

```csharp
app.UseRouting(routes =>
{
    routes.MapRazorPages();
    routes.MapHub<ChatHub>("hubs/chat");
});
```

<span data-ttu-id="7c777-199">ASP.NET Core 3.0 SignalR dodał:</span><span class="sxs-lookup"><span data-stu-id="7c777-199">ASP.NET Core 3.0 SignalR added:</span></span>

<span data-ttu-id="7c777-200">Przesyłanie strumieniowe między klientami.</span><span class="sxs-lookup"><span data-stu-id="7c777-200">Client-to-server streaming.</span></span> <span data-ttu-id="7c777-201">W przypadku przesyłania strumieniowego między klientami metody po stronie `IAsyncEnumerable<T>` serwera `ChannelReader<T>`mogą przyjmować wystąpienia typu "od klienta do serwera" lub .</span><span class="sxs-lookup"><span data-stu-id="7c777-201">With client-to-server streaming, server-side methods can take instances of either an `IAsyncEnumerable<T>` or `ChannelReader<T>`.</span></span> <span data-ttu-id="7c777-202">W poniższym przykładzie `UploadStream` języka C# metoda w centrum otrzyma strumień ciągów od klienta:</span><span class="sxs-lookup"><span data-stu-id="7c777-202">In the following C# sample, the `UploadStream` method on the Hub will receive a stream of strings from the client:</span></span>

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        // process content
    }
}
```

<span data-ttu-id="7c777-203">Aplikacje klienckie platformy `IAsyncEnumerable<T>` `ChannelReader<T>` .NET `stream` mogą przekazać `UploadStream` wystąpienie lub wystąpienie jako argument metody Hub powyżej.</span><span class="sxs-lookup"><span data-stu-id="7c777-203">.NET client apps can pass either an `IAsyncEnumerable<T>` or `ChannelReader<T>` instance as the `stream` argument of the `UploadStream` Hub method above.</span></span>

<span data-ttu-id="7c777-204">Po `for` zakończeniu pętli i zamknięciu funkcji lokalnej zostanie wysłana zakończenie strumienia:</span><span class="sxs-lookup"><span data-stu-id="7c777-204">After the `for` loop has completed and the local function exits, the stream completion is sent:</span></span>

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

<span data-ttu-id="7c777-205">Aplikacje klienckie JavaScript SignalR `Subject` używają (lub [RxJS Subject)](https://rxjs.dev/api/index/class/Subject)dla `stream` argumentu `UploadStream` hub metody powyżej.</span><span class="sxs-lookup"><span data-stu-id="7c777-205">JavaScript client apps use the SignalR `Subject` (or an [RxJS Subject](https://rxjs.dev/api/index/class/Subject)) for the `stream` argument of the `UploadStream` Hub method above.</span></span>

```javascript
let subject = new signalR.Subject();
await connection.send("StartStream", "MyAsciiArtStream", subject);
```

<span data-ttu-id="7c777-206">Kod JavaScript może `subject.next` używać metody do obsługi ciągów, ponieważ są one przechwytywane i gotowe do wysłania do serwera.</span><span class="sxs-lookup"><span data-stu-id="7c777-206">The JavaScript code could use the `subject.next` method to handle strings as they are captured and ready to be sent to the server.</span></span>

```javascript
subject.next("example");
subject.complete();
```

<span data-ttu-id="7c777-207">Za pomocą kodu, podobnie jak dwa poprzednie fragmenty kodu, można utworzyć środowisko przesyłania strumieniowego w czasie rzeczywistym.</span><span class="sxs-lookup"><span data-stu-id="7c777-207">Using code like the two preceding snippets, real-time streaming experiences can be created.</span></span>

## <a name="new-json-serialization"></a><span data-ttu-id="7c777-208">Nowa serializacja JSON</span><span class="sxs-lookup"><span data-stu-id="7c777-208">New JSON serialization</span></span>

<span data-ttu-id="7c777-209">ASP.NET Core 3.0 używa <xref:System.Text.Json> teraz domyślnie serializacji JSON:</span><span class="sxs-lookup"><span data-stu-id="7c777-209">ASP.NET Core 3.0 now uses <xref:System.Text.Json> by default for JSON serialization:</span></span>

* <span data-ttu-id="7c777-210">Odczytuje i zapisuje JSON asynchronicznie.</span><span class="sxs-lookup"><span data-stu-id="7c777-210">Reads and writes JSON asynchronously.</span></span>
* <span data-ttu-id="7c777-211">Jest zoptymalizowany pod kątem tekstu UTF-8.</span><span class="sxs-lookup"><span data-stu-id="7c777-211">Is optimized for UTF-8 text.</span></span>
* <span data-ttu-id="7c777-212">Zazwyczaj wyższa wydajność `Newtonsoft.Json`niż .</span><span class="sxs-lookup"><span data-stu-id="7c777-212">Typically higher performance than `Newtonsoft.Json`.</span></span>

<span data-ttu-id="7c777-213">Aby dodać Json.NET do ASP.NET Core 3.0, zobacz [Dodawanie obsługi formatu JSON opartego na newtonsoft.Json](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span><span class="sxs-lookup"><span data-stu-id="7c777-213">To add Json.NET to ASP.NET Core 3.0, see [Add Newtonsoft.Json-based JSON format support](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span></span>

## <a name="new-razor-directives"></a><span data-ttu-id="7c777-214">Nowe dyrektywy razor</span><span class="sxs-lookup"><span data-stu-id="7c777-214">New Razor directives</span></span>

<span data-ttu-id="7c777-215">Poniższa lista zawiera nowe dyrektywy Razor:</span><span class="sxs-lookup"><span data-stu-id="7c777-215">The following list contains new Razor directives:</span></span>

* <span data-ttu-id="7c777-216">[`@attribute`](xref:mvc/views/razor#attribute)&ndash; Dyrektywa `@attribute` stosuje dany atrybut do klasy wygenerowanej strony lub widoku.</span><span class="sxs-lookup"><span data-stu-id="7c777-216">[`@attribute`](xref:mvc/views/razor#attribute) &ndash; The `@attribute` directive applies the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="7c777-217">Na przykład `@attribute [Authorize]`.</span><span class="sxs-lookup"><span data-stu-id="7c777-217">For example, `@attribute [Authorize]`.</span></span>
* <span data-ttu-id="7c777-218">[`@implements`](xref:mvc/views/razor#implements)&ndash; Dyrektywa `@implements` implementuje interfejs dla wygenerowanej klasy.</span><span class="sxs-lookup"><span data-stu-id="7c777-218">[`@implements`](xref:mvc/views/razor#implements) &ndash; The `@implements` directive implements an interface for the generated class.</span></span> <span data-ttu-id="7c777-219">Na przykład `@implements IDisposable`.</span><span class="sxs-lookup"><span data-stu-id="7c777-219">For example, `@implements IDisposable`.</span></span>

## <a name="identityserver4-supports-authentication-and-authorization-for-web-apis-and-spas"></a><span data-ttu-id="7c777-220">IdentityServer4 obsługuje uwierzytelnianie i autoryzację interfejsów API sieci Web i oso</span><span class="sxs-lookup"><span data-stu-id="7c777-220">IdentityServer4 supports authentication and authorization for web APIs and SPAs</span></span>

<span data-ttu-id="7c777-221">ASP.NET Core 3.0 oferuje uwierzytelnianie w aplikacjach jednostronicowych (SPA) przy użyciu obsługi autoryzacji interfejsu API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="7c777-221">ASP.NET Core 3.0 offers authentication in Single Page Apps (SPAs) using the support for web API authorization.</span></span> <span data-ttu-id="7c777-222">ASP.NET podstawową tożsamość do uwierzytelniania i przechowywania użytkowników jest połączona z [IdentityServer4](https://identityserver.io/) w celu wdrożenia Open ID Connect.</span><span class="sxs-lookup"><span data-stu-id="7c777-222">ASP.NET Core Identity for authenticating and storing users is combined with [IdentityServer4](https://identityserver.io/) for implementing Open ID Connect.</span></span>

<span data-ttu-id="7c777-223">IdentityServer4 to rama OpenID Connect i OAuth 2.0 dla ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="7c777-223">IdentityServer4 is an OpenID Connect and OAuth 2.0 framework for ASP.NET Core 3.0.</span></span> <span data-ttu-id="7c777-224">Umożliwia następujące funkcje zabezpieczeń:</span><span class="sxs-lookup"><span data-stu-id="7c777-224">It enables the following security features:</span></span>

* <span data-ttu-id="7c777-225">Uwierzytelnianie jako usługa (AaaS)</span><span class="sxs-lookup"><span data-stu-id="7c777-225">Authentication as a Service (AaaS)</span></span>
* <span data-ttu-id="7c777-226">Logowanie jednokrotne (Logowanie jednokrotne) w wielu typach aplikacji</span><span class="sxs-lookup"><span data-stu-id="7c777-226">Single sign-on/off (SSO) over multiple application types</span></span>
* <span data-ttu-id="7c777-227">Kontrola dostępu dla interfejsów API</span><span class="sxs-lookup"><span data-stu-id="7c777-227">Access control for APIs</span></span>
* <span data-ttu-id="7c777-228">Brama federacji</span><span class="sxs-lookup"><span data-stu-id="7c777-228">Federation Gateway</span></span>

<span data-ttu-id="7c777-229">Aby uzyskać więcej informacji, zobacz [dokumentację IdentityServer4](http://docs.identityserver.io/en/latest/index.html) lub [Uwierzytelnianie i autoryzacja dla OSO](xref:security/authentication/identity/spa).</span><span class="sxs-lookup"><span data-stu-id="7c777-229">For more information, see [the IdentityServer4 documentation](http://docs.identityserver.io/en/latest/index.html) or [Authentication and authorization for SPAs](xref:security/authentication/identity/spa).</span></span>

## <a name="certificate-and-kerberos-authentication"></a><span data-ttu-id="7c777-230">Uwierzytelnianie certyfikatu i protokołu Kerberos</span><span class="sxs-lookup"><span data-stu-id="7c777-230">Certificate and Kerberos authentication</span></span>

<span data-ttu-id="7c777-231">Uwierzytelnianie certyfikatu wymaga:</span><span class="sxs-lookup"><span data-stu-id="7c777-231">Certificate authentication requires:</span></span>

* <span data-ttu-id="7c777-232">Konfigurowanie serwera do akceptowania certyfikatów.</span><span class="sxs-lookup"><span data-stu-id="7c777-232">Configuring the server to accept certificates.</span></span>
* <span data-ttu-id="7c777-233">Dodawanie oprogramowania pośredniczącego uwierzytelniania w `Startup.Configure`pliku .</span><span class="sxs-lookup"><span data-stu-id="7c777-233">Adding the authentication middleware in `Startup.Configure`.</span></span>
* <span data-ttu-id="7c777-234">Dodawanie usługi uwierzytelniania `Startup.ConfigureServices`certyfikatów w pliku .</span><span class="sxs-lookup"><span data-stu-id="7c777-234">Adding the certificate authentication service in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="7c777-235">Opcje uwierzytelniania certyfikatów obejmują możliwość:</span><span class="sxs-lookup"><span data-stu-id="7c777-235">Options for certificate authentication include the ability to:</span></span>

* <span data-ttu-id="7c777-236">Akceptowanie certyfikatów z podpisem własnym.</span><span class="sxs-lookup"><span data-stu-id="7c777-236">Accept self-signed certificates.</span></span>
* <span data-ttu-id="7c777-237">Sprawdź, czy nie ma odwołania certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="7c777-237">Check for certificate revocation.</span></span>
* <span data-ttu-id="7c777-238">Sprawdź, czy certyfikat oferty jest w nim odpowiedni.</span><span class="sxs-lookup"><span data-stu-id="7c777-238">Check that the proffered certificate has the right usage flags in it.</span></span>

<span data-ttu-id="7c777-239">Domyślny podmiot zabezpieczeń użytkownika jest konstruowany z właściwości certyfikatu.</span><span class="sxs-lookup"><span data-stu-id="7c777-239">A default user principal is constructed from the certificate properties.</span></span> <span data-ttu-id="7c777-240">Podmiot zabezpieczeń użytkownika zawiera zdarzenie, które umożliwia uzupełnienie lub zastąpienie podmiotu.</span><span class="sxs-lookup"><span data-stu-id="7c777-240">The user principal contains an event that enables supplementing or replacing the principal.</span></span> <span data-ttu-id="7c777-241">Aby uzyskać więcej informacji, zobacz <xref:security/authentication/certauth>.</span><span class="sxs-lookup"><span data-stu-id="7c777-241">For more information, see <xref:security/authentication/certauth>.</span></span>

<span data-ttu-id="7c777-242">[Uwierzytelnianie systemu Windows](/windows-server/security/windows-authentication/windows-authentication-overview) zostało rozszerzone na systemy Linux i macOS.</span><span class="sxs-lookup"><span data-stu-id="7c777-242">[Windows Authentication](/windows-server/security/windows-authentication/windows-authentication-overview) has been extended onto Linux and macOS.</span></span> <span data-ttu-id="7c777-243">W poprzednich wersjach uwierzytelnianie systemu Windows było ograniczone do [iIS](xref:host-and-deploy/iis/index) i [HttpSys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="7c777-243">In previous versions, Windows Authentication was limited to [IIS](xref:host-and-deploy/iis/index) and [HttpSys](xref:fundamentals/servers/httpsys).</span></span> <span data-ttu-id="7c777-244">W ASP.NET Core 3.0 [Kestrel](xref:fundamentals/servers/kestrel) ma możliwość używania hostów Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview)i [NTLM w systemach Windows,](/windows-server/security/kerberos/ntlm-overview)Linux i macOS dla hostów przyłączonych do domeny systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="7c777-244">In ASP.NET Core 3.0, [Kestrel](xref:fundamentals/servers/kestrel) has the ability to use Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview), and [NTLM on Windows](/windows-server/security/kerberos/ntlm-overview), Linux, and macOS for Windows domain-joined hosts.</span></span> <span data-ttu-id="7c777-245">Obsługa pustułki tych schematów uwierzytelniania jest dostarczana przez pakiet [Microsoft.AspNetCore.Authentication.Negotiate NuGet.](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate)</span><span class="sxs-lookup"><span data-stu-id="7c777-245">Kestrel support of these authentication schemes is provided by the [Microsoft.AspNetCore.Authentication.Negotiate NuGet](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) package.</span></span> <span data-ttu-id="7c777-246">Podobnie jak w przypadku innych usług uwierzytelniania, skonfiguruj aplikację uwierzytelniającą w szerokim zakresie, a następnie skonfiguruj usługę:</span><span class="sxs-lookup"><span data-stu-id="7c777-246">As with the other authentication services, configure authentication app wide, then configure the service:</span></span>

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

<span data-ttu-id="7c777-247">Wymagania dotyczące hosta:</span><span class="sxs-lookup"><span data-stu-id="7c777-247">Host requirements:</span></span>

* <span data-ttu-id="7c777-248">Hosty systemu Windows muszą mieć dodane [nazwy główne usług](/windows/win32/ad/service-principal-names) (SPN) do konta użytkownika obsługującego aplikację.</span><span class="sxs-lookup"><span data-stu-id="7c777-248">Windows hosts must have [Service Principal Names](/windows/win32/ad/service-principal-names) (SPNs) added to the user account hosting the app.</span></span>
* <span data-ttu-id="7c777-249">Do domeny należy dołączyć komputery z systemem Linux i macOS.</span><span class="sxs-lookup"><span data-stu-id="7c777-249">Linux and macOS machines must be joined to the domain.</span></span>
  * <span data-ttu-id="7c777-250">SPN muszą być tworzone dla procesu sieci web.</span><span class="sxs-lookup"><span data-stu-id="7c777-250">SPNs must be created for the web process.</span></span>
  * <span data-ttu-id="7c777-251">[Pliki keytab](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) muszą być generowane i konfigurowane na komputerze-hoście.</span><span class="sxs-lookup"><span data-stu-id="7c777-251">[Keytab files](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) must be generated and configured on the host machine.</span></span>

<span data-ttu-id="7c777-252">Aby uzyskać więcej informacji, zobacz <xref:security/authentication/windowsauth>.</span><span class="sxs-lookup"><span data-stu-id="7c777-252">For more information, see <xref:security/authentication/windowsauth>.</span></span>

## <a name="template-changes"></a><span data-ttu-id="7c777-253">Zmiany szablonu</span><span class="sxs-lookup"><span data-stu-id="7c777-253">Template changes</span></span>

<span data-ttu-id="7c777-254">Szablony interfejsu użytkownika sieci Web (Razor Pages, MVC z kontrolerem i widokami) zostały usunięte:</span><span class="sxs-lookup"><span data-stu-id="7c777-254">The web UI templates (Razor Pages, MVC with controller and views) have the following removed:</span></span>

* <span data-ttu-id="7c777-255">Interfejs użytkownika zgody na pliki cookie nie jest już uwzględniony.</span><span class="sxs-lookup"><span data-stu-id="7c777-255">The cookie consent UI is no longer included.</span></span> <span data-ttu-id="7c777-256">Aby włączyć funkcję zgody na pliki cookie w aplikacji wygenerowanej przez <xref:security/gdpr>szablon core 3.0 ASP.NET Core, zobacz .</span><span class="sxs-lookup"><span data-stu-id="7c777-256">To enable the cookie consent feature in an ASP.NET Core 3.0 template-generated app, see <xref:security/gdpr>.</span></span>
* <span data-ttu-id="7c777-257">Skrypty i powiązane zasoby statyczne są teraz odwoływane jako pliki lokalne zamiast używania sieci CDN.</span><span class="sxs-lookup"><span data-stu-id="7c777-257">Scripts and related static assets are now referenced as local files instead of using CDNs.</span></span> <span data-ttu-id="7c777-258">Aby uzyskać więcej informacji, zobacz [Skrypty i powiązane zasoby statyczne są teraz odwoływane jako pliki lokalne zamiast używania sieci CDN opartych na bieżącym środowisku (aspnet/AspNetCore.Docs #14350)](https://github.com/dotnet/AspNetCore.Docs/issues/14350).</span><span class="sxs-lookup"><span data-stu-id="7c777-258">For more information, see [Scripts and related static assets are now referenced as local files instead of using CDNs based on the current environment (aspnet/AspNetCore.Docs #14350)](https://github.com/dotnet/AspNetCore.Docs/issues/14350).</span></span>

<span data-ttu-id="7c777-259">Szablon kątowy zaktualizowany do używania angular 8.</span><span class="sxs-lookup"><span data-stu-id="7c777-259">The Angular template updated to use Angular 8.</span></span>

<span data-ttu-id="7c777-260">Szablon biblioteki klas Razor (RCL) domyślnie domyślnie jest owy do tworzenia komponentów Razor.</span><span class="sxs-lookup"><span data-stu-id="7c777-260">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="7c777-261">Nowa opcja szablonu w programie Visual Studio zapewnia obsługę szablonów dla stron i widoków.</span><span class="sxs-lookup"><span data-stu-id="7c777-261">A new template option in Visual Studio provides template support for pages and views.</span></span> <span data-ttu-id="7c777-262">Podczas tworzenia listy RCL z szablonu w `--support-pages-and-views` powłoce poleceń należy przekazać tę opcję (`dotnet new razorclasslib --support-pages-and-views`).</span><span class="sxs-lookup"><span data-stu-id="7c777-262">When creating an RCL from the template in a command shell, pass the `--support-pages-and-views` option (`dotnet new razorclasslib --support-pages-and-views`).</span></span>

## <a name="generic-host"></a><span data-ttu-id="7c777-263">Host ogólny</span><span class="sxs-lookup"><span data-stu-id="7c777-263">Generic Host</span></span>

<span data-ttu-id="7c777-264">Używane są szablony ASP.NET Core 3.0 <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="7c777-264">The ASP.NET Core 3.0 templates use <xref:fundamentals/host/generic-host>.</span></span> <span data-ttu-id="7c777-265">Poprzednie wersje <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>używane .</span><span class="sxs-lookup"><span data-stu-id="7c777-265">Previous versions used <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>.</span></span> <span data-ttu-id="7c777-266">Korzystanie z hosta .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) zapewnia lepszą integrację aplikacji ASP.NET Core z innymi scenariuszami serwera, które nie są specyficzne dla sieci Web.</span><span class="sxs-lookup"><span data-stu-id="7c777-266">Using the .NET Core Generic Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) provides better integration of ASP.NET Core apps with other server scenarios that aren't web-specific.</span></span> <span data-ttu-id="7c777-267">Aby uzyskać więcej informacji, zobacz [HostBuilder zastępuje WebHostBuilder](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).</span><span class="sxs-lookup"><span data-stu-id="7c777-267">For more information, see [HostBuilder replaces WebHostBuilder](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).</span></span>

### <a name="host-configuration"></a><span data-ttu-id="7c777-268">Konfiguracja hosta</span><span class="sxs-lookup"><span data-stu-id="7c777-268">Host configuration</span></span>

<span data-ttu-id="7c777-269">Przed wydaniem ASP.NET Core 3.0, zmienne środowiskowe poprzedzone `ASPNETCORE_` zostały załadowane do konfiguracji hosta hosta sieci Web Host.</span><span class="sxs-lookup"><span data-stu-id="7c777-269">Prior to the release of ASP.NET Core 3.0, environment variables prefixed with `ASPNETCORE_` were loaded for host configuration of the Web Host.</span></span> <span data-ttu-id="7c777-270">W 3.0, `AddEnvironmentVariables` służy do ładowania zmiennych `DOTNET_` środowiskowych poprzedzonych do konfiguracji hosta z `CreateDefaultBuilder`.</span><span class="sxs-lookup"><span data-stu-id="7c777-270">In 3.0, `AddEnvironmentVariables` is used to load environment variables prefixed with `DOTNET_` for host configuration with `CreateDefaultBuilder`.</span></span>

### <a name="changes-to-startup-constructor-injection"></a><span data-ttu-id="7c777-271">Zmiany w iniekcji konstruktora uruchamiania</span><span class="sxs-lookup"><span data-stu-id="7c777-271">Changes to Startup constructor injection</span></span>

<span data-ttu-id="7c777-272">Host ogólny obsługuje tylko następujące `Startup` typy iniekcji konstruktora:</span><span class="sxs-lookup"><span data-stu-id="7c777-272">The Generic Host only supports the following types for `Startup` constructor injection:</span></span>

* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="7c777-273">Wszystkie usługi mogą być wstrzykiwane bezpośrednio `Startup.Configure` jako argumenty do metody.</span><span class="sxs-lookup"><span data-stu-id="7c777-273">All services can still be injected directly as arguments to the `Startup.Configure` method.</span></span> <span data-ttu-id="7c777-274">Aby uzyskać więcej informacji, zobacz [Generic Host ogranicza iniekcję konstruktora uruchamiania (aspnet/Anonse #353)](https://github.com/aspnet/Announcements/issues/353).</span><span class="sxs-lookup"><span data-stu-id="7c777-274">For more information, see [Generic Host restricts Startup constructor injection (aspnet/Announcements #353)](https://github.com/aspnet/Announcements/issues/353).</span></span>

## <a name="kestrel"></a><span data-ttu-id="7c777-275">Kestrel</span><span class="sxs-lookup"><span data-stu-id="7c777-275">Kestrel</span></span>

* <span data-ttu-id="7c777-276">Konfiguracja pustułki została zaktualizowana w celu migracji do hosta ogólnego.</span><span class="sxs-lookup"><span data-stu-id="7c777-276">Kestrel configuration has been updated for the migration to the Generic Host.</span></span> <span data-ttu-id="7c777-277">W 3.0 Kestrel jest skonfigurowany na konstruktorze hosta internetowego dostarczonego przez `ConfigureWebHostDefaults`.</span><span class="sxs-lookup"><span data-stu-id="7c777-277">In 3.0, Kestrel is configured on the web host builder provided by `ConfigureWebHostDefaults`.</span></span>
* <span data-ttu-id="7c777-278">Karty połączeń zostały usunięte z Kestrel i zastąpione oprogramowaniem pośredniczącym połączeń, które jest podobne do oprogramowania pośredniczącego HTTP w potoku ASP.NET Core, ale dla połączeń niższego poziomu.</span><span class="sxs-lookup"><span data-stu-id="7c777-278">Connection Adapters have been removed from Kestrel and replaced with Connection Middleware, which is similar to HTTP Middleware in the ASP.NET Core pipeline but for lower-level connections.</span></span>
* <span data-ttu-id="7c777-279">Warstwa transportowa Kestrel została ujawniona `Connections.Abstractions`jako interfejs publiczny w .</span><span class="sxs-lookup"><span data-stu-id="7c777-279">The Kestrel transport layer has been exposed as a public interface in `Connections.Abstractions`.</span></span>
* <span data-ttu-id="7c777-280">Niejednoznaczność między nagłówkami i przyczepami została rozwiązana przez przeniesienie nagłówków końcowych do nowej kolekcji.</span><span class="sxs-lookup"><span data-stu-id="7c777-280">Ambiguity between headers and trailers has been resolved by moving trailing headers to a new collection.</span></span>
* <span data-ttu-id="7c777-281">Synchroniczne interfejsy API we/wy, takie jak `HttpRequest.Body.Read`, są częstym źródłem głodu wątku prowadzącego do awarii aplikacji.</span><span class="sxs-lookup"><span data-stu-id="7c777-281">Synchronous I/O APIs, such as `HttpRequest.Body.Read`, are a common source of thread starvation leading to app crashes.</span></span> <span data-ttu-id="7c777-282">W 3.0 `AllowSynchronousIO` jest domyślnie wyłączona.</span><span class="sxs-lookup"><span data-stu-id="7c777-282">In 3.0, `AllowSynchronousIO` is disabled by default.</span></span>

<span data-ttu-id="7c777-283">Aby uzyskać więcej informacji, zobacz <xref:migration/22-to-30#kestrel>.</span><span class="sxs-lookup"><span data-stu-id="7c777-283">For more information, see <xref:migration/22-to-30#kestrel>.</span></span>

## <a name="http2-enabled-by-default"></a><span data-ttu-id="7c777-284">Http/2 domyślnie włączony</span><span class="sxs-lookup"><span data-stu-id="7c777-284">HTTP/2 enabled by default</span></span>

<span data-ttu-id="7c777-285">Protokół HTTP/2 jest domyślnie włączony w kestrel dla punktów końcowych HTTPS.</span><span class="sxs-lookup"><span data-stu-id="7c777-285">HTTP/2 is enabled by default in Kestrel for HTTPS endpoints.</span></span> <span data-ttu-id="7c777-286">Obsługa protokołu HTTP/2 dla systemów IIS lub HTTP.sys jest włączona, gdy jest obsługiwana przez system operacyjny.</span><span class="sxs-lookup"><span data-stu-id="7c777-286">HTTP/2 support for IIS or HTTP.sys is enabled when supported by the operating system.</span></span>

## <a name="eventcounters-on-request"></a><span data-ttu-id="7c777-287">EventCounters na życzenie</span><span class="sxs-lookup"><span data-stu-id="7c777-287">EventCounters on request</span></span>

<span data-ttu-id="7c777-288">Hosting EventSource, `Microsoft.AspNetCore.Hosting`emituje następujące <xref:System.Diagnostics.Tracing.EventCounter> nowe typy związane z przychodzące żądania:</span><span class="sxs-lookup"><span data-stu-id="7c777-288">The Hosting EventSource, `Microsoft.AspNetCore.Hosting`, emits the following new <xref:System.Diagnostics.Tracing.EventCounter> types related to incoming requests:</span></span>

* `requests-per-second`
* `total-requests`
* `current-requests`
* `failed-requests`

## <a name="endpoint-routing"></a><span data-ttu-id="7c777-289">Routing punktu końcowego</span><span class="sxs-lookup"><span data-stu-id="7c777-289">Endpoint routing</span></span>

<span data-ttu-id="7c777-290">Routing punktu końcowego, który umożliwia struktur (na przykład MVC) do pracy dobrze z oprogramowaniem pośredniczącym, jest zwiększona:</span><span class="sxs-lookup"><span data-stu-id="7c777-290">Endpoint Routing, which allows frameworks (for example, MVC) to work well with middleware, is enhanced:</span></span>

* <span data-ttu-id="7c777-291">Kolejność oprogramowania pośredniczącego i punktów końcowych jest konfigurowana w potoku przetwarzania żądań `Startup.Configure`programu .</span><span class="sxs-lookup"><span data-stu-id="7c777-291">The order of middleware and endpoints is configurable in the request processing pipeline of `Startup.Configure`.</span></span>
* <span data-ttu-id="7c777-292">Punkty końcowe i oprogramowanie pośredniczące dobrze komponują się z innymi ASP.NET oparte na technologiach opartych na rdzeniu, takich jak kontrole kondycji.</span><span class="sxs-lookup"><span data-stu-id="7c777-292">Endpoints and middleware compose well with other ASP.NET Core-based technologies, such as Health Checks.</span></span>
* <span data-ttu-id="7c777-293">Punkty końcowe można zaimplementować zasady, takie jak CORS lub autoryzacji, zarówno w oprogramowaniu pośredniczącym i MVC.</span><span class="sxs-lookup"><span data-stu-id="7c777-293">Endpoints can implement a policy, such as CORS or authorization, in both middleware and MVC.</span></span>
* <span data-ttu-id="7c777-294">Filtry i atrybuty mogą być umieszczane na metodach w kontrolerach.</span><span class="sxs-lookup"><span data-stu-id="7c777-294">Filters and attributes can be placed on methods in controllers.</span></span>

<span data-ttu-id="7c777-295">Aby uzyskać więcej informacji, zobacz <xref:fundamentals/routing#routing-basics>.</span><span class="sxs-lookup"><span data-stu-id="7c777-295">For more information, see <xref:fundamentals/routing#routing-basics>.</span></span>

## <a name="health-checks"></a><span data-ttu-id="7c777-296">Kontrole kondycji</span><span class="sxs-lookup"><span data-stu-id="7c777-296">Health Checks</span></span>

<span data-ttu-id="7c777-297">Kontrole kondycji używają routingu punktu końcowego z hostem ogólnym.</span><span class="sxs-lookup"><span data-stu-id="7c777-297">Health Checks use endpoint routing with the Generic Host.</span></span> <span data-ttu-id="7c777-298">W `Startup.Configure`programie `MapHealthChecks` wywołaj konstruktora punktów końcowych z adresem URL punktu końcowego lub ścieżką względną:</span><span class="sxs-lookup"><span data-stu-id="7c777-298">In `Startup.Configure`, call `MapHealthChecks` on the endpoint builder with the endpoint URL or relative path:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

<span data-ttu-id="7c777-299">Punkty końcowe kontroli kondycji mogą:</span><span class="sxs-lookup"><span data-stu-id="7c777-299">Health Checks endpoints can:</span></span>

* <span data-ttu-id="7c777-300">Określ co najmniej jeden dozwolony host/porty.</span><span class="sxs-lookup"><span data-stu-id="7c777-300">Specify one or more permitted hosts/ports.</span></span>
* <span data-ttu-id="7c777-301">Wymagaj autoryzacji.</span><span class="sxs-lookup"><span data-stu-id="7c777-301">Require authorization.</span></span>
* <span data-ttu-id="7c777-302">Wymagają CORS.</span><span class="sxs-lookup"><span data-stu-id="7c777-302">Require CORS.</span></span>

<span data-ttu-id="7c777-303">Aby uzyskać więcej informacji zobacz następujące artykuły:</span><span class="sxs-lookup"><span data-stu-id="7c777-303">For more information, see the following articles:</span></span>

* <xref:migration/22-to-30#health-checks>
* <xref:host-and-deploy/health-checks>

## <a name="pipes-on-httpcontext"></a><span data-ttu-id="7c777-304">Potoki na httpContext</span><span class="sxs-lookup"><span data-stu-id="7c777-304">Pipes on HttpContext</span></span>

<span data-ttu-id="7c777-305">Teraz można odczytać treść żądania i napisać treść odpowiedzi <xref:System.IO.Pipelines> przy użyciu interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="7c777-305">It's now possible to read the request body and write the response body using the <xref:System.IO.Pipelines> API.</span></span> <span data-ttu-id="7c777-306">Dla zasobu</span><span class="sxs-lookup"><span data-stu-id="7c777-306">The</span></span> <!-- <xref:Microsoft.AspNetCore.Http.HttpRequest.BodyReader> --> <span data-ttu-id="7c777-307">`HttpRequest.BodyReader`właściwość <xref:System.IO.Pipelines.PipeReader> zapewnia, który może służyć do odczytu treści żądania.</span><span class="sxs-lookup"><span data-stu-id="7c777-307">`HttpRequest.BodyReader` property provides a <xref:System.IO.Pipelines.PipeReader> that can be used to read the request body.</span></span> <span data-ttu-id="7c777-308">Dla zasobu</span><span class="sxs-lookup"><span data-stu-id="7c777-308">The</span></span> <!-- <xref:Microsoft.AspNetCore.Http.> --> <span data-ttu-id="7c777-309">`HttpResponse.BodyWriter`właściwość <xref:System.IO.Pipelines.PipeWriter> zapewnia, że może służyć do zapisu treści odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="7c777-309">`HttpResponse.BodyWriter` property provides a <xref:System.IO.Pipelines.PipeWriter> that can be used to write the response body.</span></span> <span data-ttu-id="7c777-310">`HttpRequest.BodyReader`jest analogiem strumienia. `HttpRequest.Body`</span><span class="sxs-lookup"><span data-stu-id="7c777-310">`HttpRequest.BodyReader` is an analogue of the `HttpRequest.Body` stream.</span></span> <span data-ttu-id="7c777-311">`HttpResponse.BodyWriter`jest analogiem strumienia. `HttpResponse.Body`</span><span class="sxs-lookup"><span data-stu-id="7c777-311">`HttpResponse.BodyWriter` is an analogue of the `HttpResponse.Body` stream.</span></span>

<!-- indirectly related, https://github.com/dotnet/docs/pull/14414 won't be published by 9/23  -->

## <a name="improved-error-reporting-in-iis"></a><span data-ttu-id="7c777-312">Ulepszone raportowanie błędów w ujm iis</span><span class="sxs-lookup"><span data-stu-id="7c777-312">Improved error reporting in IIS</span></span>

<span data-ttu-id="7c777-313">Błędy uruchamiania podczas hostowania aplikacji ASP.NET Core w usługach IIS teraz generują bogatsze dane diagnostyczne.</span><span class="sxs-lookup"><span data-stu-id="7c777-313">Startup errors when hosting ASP.NET Core apps in IIS now produce richer diagnostic data.</span></span> <span data-ttu-id="7c777-314">Te błędy są zgłaszane do dziennika zdarzeń systemu Windows ze śladami stosu wszędzie tam, gdzie ma to zastosowanie.</span><span class="sxs-lookup"><span data-stu-id="7c777-314">These errors are reported to the Windows Event Log with stack traces wherever applicable.</span></span> <span data-ttu-id="7c777-315">Ponadto wszystkie ostrzeżenia, błędy i nieobsługiwały wyjątki są rejestrowane w dzienniku zdarzeń systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="7c777-315">In addition, all warnings, errors, and unhandled exceptions are logged to the Windows Event Log.</span></span>

## <a name="worker-service-and-worker-sdk"></a><span data-ttu-id="7c777-316">SDK obsługi pracownika i pracownika</span><span class="sxs-lookup"><span data-stu-id="7c777-316">Worker Service and Worker SDK</span></span>

<span data-ttu-id="7c777-317">Program .NET Core 3.0 wprowadza nowy szablon aplikacji usługi worker service.</span><span class="sxs-lookup"><span data-stu-id="7c777-317">.NET Core 3.0 introduces the new Worker Service app template.</span></span> <span data-ttu-id="7c777-318">Ten szablon stanowi punkt wyjścia do pisania usług długo działających w .NET Core.</span><span class="sxs-lookup"><span data-stu-id="7c777-318">This template provides a starting point for writing long running services in .NET Core.</span></span>

<span data-ttu-id="7c777-319">Aby uzyskać więcej informacji, zobacz:</span><span class="sxs-lookup"><span data-stu-id="7c777-319">For more information, see:</span></span>

* [<span data-ttu-id="7c777-320">Core Pracownicy platformy .NET jako usługi systemu Windows</span><span class="sxs-lookup"><span data-stu-id="7c777-320">.NET Core Workers as Windows Services</span></span>](https://devblogs.microsoft.com/aspnet/net-core-workers-as-windows-services/)
* <xref:fundamentals/host/hosted-services>
* <xref:host-and-deploy/windows-service>

## <a name="forwarded-headers-middleware-improvements"></a><span data-ttu-id="7c777-321">Ulepszenia oprogramowania pośredniczącego nagłówków przesyłanych dalej</span><span class="sxs-lookup"><span data-stu-id="7c777-321">Forwarded Headers Middleware improvements</span></span>

<span data-ttu-id="7c777-322">W poprzednich wersjach ASP.NET Core, <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> wywoływanie i były problematyczne podczas wdrażania na platformie Azure Linux lub za dowolnym odwrotnym serwerem proxy innym niż IIS.</span><span class="sxs-lookup"><span data-stu-id="7c777-322">In previous versions of ASP.NET Core, calling <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> and  <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> were problematic when deployed to an Azure Linux or behind any reverse proxy other than IIS.</span></span> <span data-ttu-id="7c777-323">Poprawka dla poprzednich wersji jest udokumentowana w [Forward schemat dla linuksa i odwrotnych serwerów proxy innych niż IIS](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies).</span><span class="sxs-lookup"><span data-stu-id="7c777-323">The fix for previous versions is documented in [Forward the scheme for Linux and non-IIS reverse proxies](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies).</span></span>

<span data-ttu-id="7c777-324">Ten scenariusz został ustalony w ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="7c777-324">This scenario is fixed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="7c777-325">Host włącza [oprogramowanie pośredniczące nagłówków przesyłanych dalej,](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) gdy zmienna środowiskowa `ASPNETCORE_FORWARDEDHEADERS_ENABLED` jest ustawiona na `true`.</span><span class="sxs-lookup"><span data-stu-id="7c777-325">The host enables the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options) when the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span> <span data-ttu-id="7c777-326">`ASPNETCORE_FORWARDEDHEADERS_ENABLED`jest ustawiona na `true` naszych obrazach kontenerów.</span><span class="sxs-lookup"><span data-stu-id="7c777-326">`ASPNETCORE_FORWARDEDHEADERS_ENABLED` is set to `true` in our container images.</span></span>

## <a name="performance-improvements"></a><span data-ttu-id="7c777-327">Ulepszenia wydajności</span><span class="sxs-lookup"><span data-stu-id="7c777-327">Performance improvements</span></span>

<span data-ttu-id="7c777-328">ASP.NET Core 3.0 zawiera wiele ulepszeń, które zmniejszają użycie pamięci i zwiększają przepustowość:</span><span class="sxs-lookup"><span data-stu-id="7c777-328">ASP.NET Core 3.0 includes many improvements that reduce memory usage and improve throughput:</span></span>

* <span data-ttu-id="7c777-329">Zmniejszenie użycia pamięci podczas korzystania z wbudowanego kontenera iniekcji zależności dla usług o określonym zakresie.</span><span class="sxs-lookup"><span data-stu-id="7c777-329">Reduction in memory usage when using the built-in dependency injection container for scoped services.</span></span>
* <span data-ttu-id="7c777-330">Zmniejszenie alokacji w ramach, w tym scenariuszy oprogramowania pośredniczącego i routingu.</span><span class="sxs-lookup"><span data-stu-id="7c777-330">Reduction in allocations across the framework, including middleware scenarios and routing.</span></span>
* <span data-ttu-id="7c777-331">Zmniejszenie użycia pamięci dla połączeń WebSocket.</span><span class="sxs-lookup"><span data-stu-id="7c777-331">Reduction in memory usage for WebSocket connections.</span></span>
* <span data-ttu-id="7c777-332">Ulepszenia redukcji pamięci i przepływności dla połączeń HTTPS.</span><span class="sxs-lookup"><span data-stu-id="7c777-332">Memory reduction and throughput improvements for HTTPS connections.</span></span>
* <span data-ttu-id="7c777-333">Nowy zoptymalizowany i w pełni asynchroniczne serializator JSON.</span><span class="sxs-lookup"><span data-stu-id="7c777-333">New optimized and fully asynchronous JSON serializer.</span></span>
* <span data-ttu-id="7c777-334">Zmniejszenie użycia pamięci i ulepszenia przepływności w analizowaniu formularzy.</span><span class="sxs-lookup"><span data-stu-id="7c777-334">Reduction in memory usage and throughput improvements in form parsing.</span></span>

## <a name="aspnet-core-30-only-runs-on-net-core-30"></a><span data-ttu-id="7c777-335">ASP.NET Core 3.0 działa tylko na .NET Core 3.0</span><span class="sxs-lookup"><span data-stu-id="7c777-335">ASP.NET Core 3.0 only runs on .NET Core 3.0</span></span>

<span data-ttu-id="7c777-336">Od ASP.NET Core 3.0 program .NET Framework nie jest już obsługiwaną platformą docelową.</span><span class="sxs-lookup"><span data-stu-id="7c777-336">As of ASP.NET Core 3.0, .NET Framework is no longer a supported target framework.</span></span> <span data-ttu-id="7c777-337">Projekty ukierunkowane na program .NET Framework mogą być kontynuowane w pełni obsługiwany sposób za pomocą [wersji .NET Core 2.1 LTS](https://dotnet.microsoft.com/download/dotnet-core/2.1).</span><span class="sxs-lookup"><span data-stu-id="7c777-337">Projects targeting .NET Framework can continue in a fully supported fashion using the [.NET Core 2.1 LTS release](https://dotnet.microsoft.com/download/dotnet-core/2.1).</span></span> <span data-ttu-id="7c777-338">Większość pakietów związanych z ASP.NET core 2.1.x będzie obsługiwana przez czas nieokreślony, poza trzyletnim okresem LTS dla platformy .NET Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="7c777-338">Most ASP.NET Core 2.1.x related packages will be supported indefinitely, beyond the three-year LTS period for .NET Core 2.1.</span></span>

<span data-ttu-id="7c777-339">Aby uzyskać informacje o migracji, zobacz [Port kodu z programu .NET Framework do platformy .NET Core](/dotnet/core/porting/).</span><span class="sxs-lookup"><span data-stu-id="7c777-339">For migration information, see [Port your code from .NET Framework to .NET Core](/dotnet/core/porting/).</span></span>

## <a name="use-the-aspnet-core-shared-framework"></a><span data-ttu-id="7c777-340">Korzystanie z udostępnionej struktury ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7c777-340">Use the ASP.NET Core shared framework</span></span>

<span data-ttu-id="7c777-341">ASP.NET framework udostępniony Core 3.0, zawarte w [metapakiecie Microsoft.AspNetCore.App,](xref:fundamentals/metapackage-app)nie `<PackageReference />` wymaga już jawnego elementu w pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="7c777-341">The ASP.NET Core 3.0 shared framework, contained in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), no longer requires an explicit `<PackageReference />` element in the project file.</span></span> <span data-ttu-id="7c777-342">Udostępnione framework jest automatycznie odwołuje się `Microsoft.NET.Sdk.Web` podczas korzystania z SDK w pliku projektu:</span><span class="sxs-lookup"><span data-stu-id="7c777-342">The shared framework is automatically referenced when using the `Microsoft.NET.Sdk.Web` SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

## <a name="assemblies-removed-from-the-aspnet-core-shared-framework"></a><span data-ttu-id="7c777-343">Zestawy usunięte z udostępnionej struktury ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7c777-343">Assemblies removed from the ASP.NET Core shared framework</span></span>

<span data-ttu-id="7c777-344">Najbardziej znaczące zestawy usunięte z ASP.NET frameworka współużytkowanego Core 3.0 to:</span><span class="sxs-lookup"><span data-stu-id="7c777-344">The most notable assemblies removed from the ASP.NET Core 3.0 shared framework are:</span></span>

* <span data-ttu-id="7c777-345">[Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) (Json.NET).</span><span class="sxs-lookup"><span data-stu-id="7c777-345">[Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) (Json.NET).</span></span> <span data-ttu-id="7c777-346">Aby dodać Json.NET do ASP.NET Core 3.0, zobacz [Dodawanie obsługi formatu JSON opartego na newtonsoft.Json](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span><span class="sxs-lookup"><span data-stu-id="7c777-346">To add Json.NET to ASP.NET Core 3.0, see [Add Newtonsoft.Json-based JSON format support](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).</span></span> <span data-ttu-id="7c777-347">ASP.NET Core 3.0 wprowadza `System.Text.Json` do czytania i pisania JSON.</span><span class="sxs-lookup"><span data-stu-id="7c777-347">ASP.NET Core 3.0 introduces `System.Text.Json` for reading and writing JSON.</span></span> <span data-ttu-id="7c777-348">Aby uzyskać więcej informacji, zobacz [Nowa serializacja JSON](#new-json-serialization) w tym dokumencie.</span><span class="sxs-lookup"><span data-stu-id="7c777-348">For more information, see [New JSON serialization](#new-json-serialization) in this document.</span></span>
* [<span data-ttu-id="7c777-349">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="7c777-349">Entity Framework Core</span></span>](/ef/core/)

<span data-ttu-id="7c777-350">Aby uzyskać pełną listę zestawów usuniętych z udostępnionej struktury, zobacz [Zestawy usuwane z pliku Microsoft.AspNetCore.App 3.0](https://github.com/dotnet/AspNetCore/issues/3755).</span><span class="sxs-lookup"><span data-stu-id="7c777-350">For a complete list of assemblies removed from the shared framework, see [Assemblies being removed from Microsoft.AspNetCore.App 3.0](https://github.com/dotnet/AspNetCore/issues/3755).</span></span> <span data-ttu-id="7c777-351">Aby uzyskać więcej informacji na temat motywacji tej zmiany, zobacz [Przerywanie zmian w witrynie Microsoft.AspNetCore.App w 3.0](https://github.com/aspnet/Announcements/issues/325) i [Pierwsze spojrzenie na zmiany nadchodzące w ASP.NET Core 3.0](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span><span class="sxs-lookup"><span data-stu-id="7c777-351">For more information on the motivation for this change, see [Breaking changes to Microsoft.AspNetCore.App in 3.0](https://github.com/aspnet/Announcements/issues/325) and [A first look at changes coming in ASP.NET Core 3.0](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span></span>

<!-- 
## Additional information
For the complete list of changes, see the [ASP.NET Core 3.0 Release Notes](WHERE IS THIS????).
-->
 