---
title: Skonfiguruj element dostosowujący dla ASP.NET Core Blazor
author: guardrex
description: Dowiedz się, jak kontrolować konsolidator języka pośredniego (IL) podczas kompilowania Blazor aplikacji.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/08/2021
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
uid: blazor/host-and-deploy/configure-trimmer
ms.openlocfilehash: 41887638f13a08d375075e8377da19d1d0098c4b
ms.sourcegitcommit: ef8d8c79993a6608bf597ad036edcf30b231843f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2021
ms.locfileid: "99975221"
---
# <a name="configure-the-trimmer-for-aspnet-core-blazor"></a>Skonfiguruj element dostosowujący dla ASP.NET Core Blazor

Blazor WebAssembly wykonuje przycinanie [języka pośredniego (IL)](/dotnet/standard/managed-code#intermediate-language--execution) , aby zmniejszyć rozmiar opublikowanych danych wyjściowych. Domyślnie przycinanie odbywa się podczas publikowania aplikacji.

Przycinanie może mieć szkodliwe skutki. W aplikacjach, które używają odbicia, element dostosowujący często nie może określić typów wymaganych do odbicia w czasie wykonywania. Aby przyciąć aplikacje używające odbicia, należy uzyskać informacje o typach wymaganych do odbicia w kodzie aplikacji oraz w pakietach lub strukturach, od których zależy aplikacja. Element dostosowujący nie może również reagować na dynamiczne zachowanie aplikacji w czasie wykonywania. Aby upewnić się, że przycięta aplikacja działa prawidłowo po wdrożeniu, należy regularnie testować opublikowane dane wyjściowe.

Aby skonfigurować element dostosowujący, zobacz artykuł [Opcje przycinania](/dotnet/core/deploying/trimming-options) w dokumentacji .NET podstawy, która zawiera wskazówki dotyczące następujących tematów:

* Wyłącz przycinanie dla całej aplikacji z `<PublishTrimmed>` właściwością w pliku projektu.
* Kontroluj, jak agresywnie nieużywane IL jest odrzucane przez element dostosowujący.
* Zatrzymaj element dostosowujący z przycinania określonych zestawów.
* Zestawy "główne" do przycinania.
* Ostrzeżenia powierzchni dla typów odbite przez ustawienie `<SuppressTrimAnalysisWarnings>` właściwości na `false` w pliku projektu.
* Obcinanie symboli formantów i degugger.
* Ustaw funkcje dostosowujące dla funkcji biblioteki struktury przycinania.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Przycinanie samodzielnych wdrożeń i plików wykonywalnych](/dotnet/core/deploying/trim-self-contained)
* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-trimming>
