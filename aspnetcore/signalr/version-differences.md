---
title: Różnice między SignalR i ASP.NET Core SignalR
author: bradygaster
description: Różnice między SignalR i ASP.NET Core SignalR
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.date: 11/21/2019
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
uid: signalr/version-differences
ms.openlocfilehash: 965fbb3d8007cb64aaf51d82d87ed7a3a5298e9b
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2020
ms.locfileid: "90721791"
---
# <a name="differences-between-aspnet-no-locsignalr-and-aspnet-core-no-locsignalr"></a>Różnice między ASP.NET SignalR i ASP.NET Core SignalR

ASP.NET Core SignalR nie jest zgodny z klientami lub serwerami dla ASP.NET SignalR . Ten artykuł zawiera szczegółowe informacje o funkcjach, które zostały usunięte lub zmienione w programie ASP.NET Core SignalR .

## <a name="how-to-identify-the-no-locsignalr-version"></a>Jak zidentyfikować SignalR wersję

::: moniker range=">= aspnetcore-3.0"

|                      | ASP.NET SignalR | ASP.NET Core SignalR |
| -------------------- | --------------- | -------------------- |
| **Pakiet NuGet serwera** | [Microsoft. AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | Brak. Uwzględnione w udostępnionej platformie [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) . |
| **Pakiety NuGet klienta** | [Microsoft. AspNet. SignalR . Klient](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[Microsoft. AspNet. SignalR . JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [Microsoft. AspNetCore. SignalR . Klient](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| **Pakiet npm klienta języka JavaScript** | [SignalR](https://www.npmjs.com/package/signalr) | [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) |
| **Klient Java** | [Repozytorium GitHub](https://github.com/SignalR/java-client) (przestarzałe)  | Maven pakietu [com. Microsoft. Signal](https://search.maven.org/artifact/com.microsoft.signalr/signalr) |
| **Typ aplikacji serwera** | ASP.NET (System. Web) lub samoobsługowy OWIN | ASP.NET Core |
| **Obsługiwane platformy serwera** | .NET Framework 4,5 lub nowszy | .NET Core 3,0 lub nowszy |

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

|                      | ASP.NET SignalR | ASP.NET Core SignalR |
| -------------------- | --------------- | -------------------- |
| **Pakiet NuGet serwera** | [Microsoft. AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | [Microsoft. AspNetCore. app](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)<br>[Microsoft. AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) (.NET Framework) |
| **Pakiety NuGet klienta** | [Microsoft. AspNet. SignalR . Klient](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[Microsoft. AspNet. SignalR . JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [Microsoft. AspNetCore. SignalR . Klient](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| **Pakiet npm klienta języka JavaScript** | [SignalR](https://www.npmjs.com/package/signalr) | [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) |
| **Klient Java** | [Repozytorium GitHub](https://github.com/SignalR/java-client) (przestarzałe)  | Maven pakietu [com. Microsoft. Signal](https://search.maven.org/artifact/com.microsoft.signalr/signalr) |
| **Typ aplikacji serwera** | ASP.NET (System. Web) lub samoobsługowy OWIN | ASP.NET Core |
| **Obsługiwane platformy serwera** | .NET Framework 4,5 lub nowszy | .NET Framework 4.6.1 lub nowsze<br>.NET Core 2,1 lub nowszy |

::: moniker-end

## <a name="feature-differences"></a>Różnice w funkcjach

### <a name="automatic-reconnects"></a>Automatyczne ponowne łączenie

::: moniker range=">= aspnetcore-3.0"

W ASP.NET SignalR :

* Domyślnie program SignalR próbuje ponownie nawiązać połączenie z serwerem, jeśli połączenie zostało porzucone. 

W ASP.NET Core SignalR :

* Automatyczne ponowne łączenie jest zgodą na [klienta .NET](xref:signalr/dotnet-client#automatically-reconnect) i [klienta JavaScript](xref:signalr/javascript-client#automatically-reconnect):

```csharp
HubConnection connection = new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect()
    .Build();
```

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Przed ASP.NET Core 3,0 program SignalR nie obsługuje automatycznego ponownego nawiązywania połączeń. Jeśli klient zostanie odłączony, użytkownik musi jawnie rozpocząć nowe połączenie, aby ponownie nawiązać połączenie. W programie SignalR ASP.NET SignalR próbuje ponownie nawiązać połączenie z serwerem, jeśli połączenie zostało porzucone.

::: moniker-end

### <a name="protocol-support"></a>Obsługa protokołów

ASP.NET Core SignalR obsługuje kod JSON, a także nowy protokół binarny na podstawie [MessagePack](xref:signalr/messagepackhubprotocol). Dodatkowo można utworzyć niestandardowe protokoły.

### <a name="transports"></a>Transporty

Transport ramki bez ograniczeń nie jest obsługiwany w ASP.NET Core SignalR .

## <a name="differences-on-the-server"></a>Różnice na serwerze

ASP.NET Core SignalR biblioteki po stronie serwera są zawarte w [pakiecie Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), który jest używany w szablonie **ASP.NET Core aplikacji sieci Web** dla Razor projektów obu i MVC.

ASP.NET Core SignalR to ASP.NET Core oprogramowanie pośredniczące. Należy ją skonfigurować przez wywołanie metody <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddSignalR%2A> w `Startup.ConfigureServices` .

```csharp
services.AddSignalR()
```

::: moniker range=">= aspnetcore-3.0"

Aby skonfigurować Routing, Mapuj trasy do centrów wewnątrz <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> wywołania metody w `Startup.Configure` metodzie.

```csharp
app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Aby skonfigurować Routing, Mapuj trasy do centrów wewnątrz <xref:Microsoft.AspNetCore.Builder.SignalRAppBuilderExtensions.UseSignalR%2A> wywołania metody w `Startup.Configure` metodzie.

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

### <a name="sticky-sessions"></a>Sesje programu Sticky

Model skalowania dla ASP.NET SignalR umożliwia klientom Ponowne nawiązywanie połączenia i wysyłanie komunikatów do dowolnego serwera w farmie. W ASP.NET Core SignalR Klient musi korzystać z tego samego serwera na czas trwania połączenia. W przypadku skalowania przy użyciu Redis, oznacza to, że są wymagane sesje programu Sticky. W przypadku skalowania przy użyciu [ SignalR usługi platformy Azure](/azure/azure-signalr/)sesje nie są wymagane, ponieważ usługa obsługuje połączenia z klientami.

### <a name="single-hub-per-connection"></a>Jedno centrum na połączenie

W ASP.NET Core SignalR model połączenia został uproszczony. Połączenia są nawiązywane bezpośrednio w jednym centrum, a nie za pomocą jednego połączenia, które jest używane do udostępniania dostępu do wielu centrów.

### <a name="streaming"></a>Przesyłanie strumieniowe

ASP.NET Core SignalR teraz obsługuje [przesyłanie strumieniowe danych](xref:signalr/streaming) z centrum do klienta programu.

### <a name="state"></a>Stan

Możliwość przekazania dowolnego stanu między klientami a centrum (często wywoływanymi `HubState` ) została usunięta, a także do obsługi komunikatów o postępie. W tej chwili nie ma żadnego odpowiednika serwerów proxy centrum.

### <a name="persistentconnection-removal"></a>Usuwanie PersistentConnection

W ASP.NET Core SignalR Klasa [PersistentConnection](/previous-versions/aspnet/jj919047(v=vs.118)) została usunięta.

### <a name="globalhost"></a>GlobalHost

ASP.NET Core ma iniekcję zależności (DI) wbudowaną w strukturę. Usługi mogą używać DI do uzyskiwania dostępu do [HubContext](xref:signalr/hubcontext). `GlobalHost`Obiekt, który jest używany w ASP.NET SignalR do pobrania, `HubContext` nie istnieje w ASP.NET Core SignalR .

### <a name="hubpipeline"></a>HubPipeline

ASP.NET Core SignalR nie obsługuje `HubPipeline` modułów.

## <a name="differences-on-the-client"></a>Różnice dotyczące klienta

### <a name="typescript"></a>TypeScript

Klient ASP.NET Core SignalR jest zapisywana w języku [TypeScript](https://www.typescriptlang.org/). Podczas korzystania z [klienta JavaScript](xref:signalr/javascript-client)można pisać w języku JavaScript lub TypeScript.

### <a name="the-javascript-client-is-hosted-at-npm"></a>Klient JavaScript jest hostowany w npm

::: moniker range=">= aspnetcore-3.0"

W wersjach ASP.NET klient JavaScript został uzyskany za pomocą pakietu NuGet w programie Visual Studio. W wersjach ASP.NET Core [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) pakiet npm zawiera biblioteki JavaScript. Ten pakiet nie jest uwzględniony w szablonie **ASP.NET Core aplikacji sieci Web** . Użyj npm, aby uzyskać i zainstalować `@microsoft/signalr` pakiet npm.

```console
npm init -y
npm install @microsoft/signalr
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

W wersjach ASP.NET klient JavaScript został uzyskany za pomocą pakietu NuGet w programie Visual Studio. W wersjach ASP.NET Core [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) pakiet npm zawiera biblioteki JavaScript. Ten pakiet nie jest uwzględniony w szablonie **ASP.NET Core aplikacji sieci Web** . Użyj npm, aby uzyskać i zainstalować `@aspnet/signalr` pakiet npm.

```console
npm init -y
npm install @aspnet/signalr
```

::: moniker-end

### <a name="jquery"></a>jQuery

Zależność od jQuery została usunięta, jednak projekty nadal mogą korzystać z jQuery.

### <a name="internet-explorer-support"></a>Obsługa programu Internet Explorer

ASP.NET Core SignalR wymaga programu Microsoft Internet Explorer 11 lub nowszego (ASP.NET SignalR obsługiwany program Microsoft Internet Explorer 8 lub nowszy).

### <a name="javascript-client-method-syntax"></a>Składnia metody klienta JavaScript

::: moniker range=">= aspnetcore-3.0"

Składnia języka JavaScript została zmieniona z wersji ASP.NET systemu SignalR . Zamiast używać `$connection` obiektu, Utwórz połączenie przy użyciu interfejsu API [HubConnectionBuilder](/javascript/api/@aspnet/signalr/hubconnectionbuilder) .

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

Użyj metody [on](/javascript/api/@microsoft/signalr/HubConnection#on) , aby określić metody klienta, które mogą być wywoływane przez centrum.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Składnia języka JavaScript została zmieniona z wersji ASP.NET systemu SignalR . Zamiast używać `$connection` obiektu, Utwórz połączenie przy użyciu interfejsu API [HubConnectionBuilder](/javascript/api/@microsoft/signalr/hubconnectionbuilder) .

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

Użyj metody [on](/javascript/api/@aspnet/signalr/HubConnection#on) , aby określić metody klienta, które mogą być wywoływane przez centrum.

::: moniker-end

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = `${user} says ${msg}`;
    console.log(encodedMsg);
});
```

Po utworzeniu metody klienta należy uruchomić połączenie z centrum. Tworzenie łańcucha metod [przechwytywania](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) w celu rejestrowania lub obsługi błędów.

```javascript
connection.start().catch(err => console.error(err));
```

### <a name="hub-proxies"></a>Serwery proxy centrum

::: moniker range=">= aspnetcore-3.0"

Serwery proxy centrum nie są już generowane automatycznie. Zamiast tego nazwa metody jest przenoszona do [wywoływania](/javascript/api/@microsoft/signalr/hubconnection#invoke) API jako ciąg.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Serwery proxy centrum nie są już generowane automatycznie. Zamiast tego nazwa metody jest przenoszona do [wywoływania](/javascript/api/@aspnet/signalr/hubconnection#invoke) API jako ciąg.

::: moniker-end

### <a name="net-and-other-clients"></a>.NET i inni klienci

[Microsoft. AspNetCore. SignalR .. ](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client)Pakiet NuGet klienta zawiera biblioteki klienckie platformy .NET dla ASP.NET Core SignalR .

Użyj, <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> Aby utworzyć i skompilować wystąpienie połączenia z koncentratorem.

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a>Różnice skalowania

ASP.NET SignalR obsługuje SQL Server i Redis. ASP.NET Core SignalR obsługuje usługi platformy Azure SignalR i Redis.

### <a name="aspnet"></a>ASP.NET

* [SignalR skalowania z Azure Service Bus](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)
* [SignalR skalowania z Redis](/aspnet/signalr/overview/performance/scaleout-with-redis)
* [SignalR skalowania z SQL Server](/aspnet/signalr/overview/performance/scaleout-with-sql-server)

### <a name="aspnet-core"></a>ASP.NET Core

* [Usługa platformy Azure SignalR](/azure/azure-signalr/)
* [Redis](xref:signalr/redis-backplane)

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Centra](xref:signalr/hubs)
* [Klient JavaScript](xref:signalr/javascript-client)
* [Klient .NET](xref:signalr/dotnet-client)
* [Obsługiwane platformy](xref:signalr/supported-platforms)