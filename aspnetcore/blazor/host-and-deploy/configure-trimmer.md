---
title: Skonfiguruj element dostosowujący dla ASP.NET Core Blazor
author: guardrex
description: Dowiedz się, jak kontrolować konsolidator języka pośredniego (IL) podczas kompilowania Blazor aplikacji.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/14/2020
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
uid: blazor/host-and-deploy/configure-trimmer
ms.openlocfilehash: 2923f76c586465e4e6044763f18527a7d36ad57c
ms.sourcegitcommit: 600666440398788db5db25dc0496b9ca8fe50915
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2020
ms.locfileid: "90080865"
---
# <a name="configure-the-trimmer-for-aspnet-core-no-locblazor"></a>Skonfiguruj element dostosowujący dla ASP.NET Core Blazor

Autor [Pranav Krishnamoorthy](https://github.com/pranavkm)

Blazor WebAssembly wykonuje przycinanie [języka pośredniego (IL)](/dotnet/standard/managed-code#intermediate-language--execution) , aby zmniejszyć rozmiar opublikowanych danych wyjściowych.

Przycinanie aplikacji jest zoptymalizowane pod kątem rozmiaru, ale mogą one mieć szkodliwe skutki. Aplikacje korzystające z odbicia lub powiązane funkcje dynamiczne mogą być przerywane po przycięciu, ponieważ element dostosowujący nie wie o zachowaniu dynamicznego i nie może w ogóle określić, które typy są wymagane do odbicia w czasie wykonywania. Aby przyciąć takie aplikacje, należy uzyskać informacje o wszelkich typach wymaganych przez odbicie w kodzie oraz w pakietach lub strukturach, od których zależy aplikacja.

Aby upewnić się, że przycięta aplikacja działa prawidłowo po wdrożeniu, ważne jest, aby regularnie testować publikowane dane wyjściowe.

Przycinanie aplikacji .NET można wyłączyć, ustawiając właściwość programu `PublishTrimmed` MSBuild na wartość `false` w pliku projektu aplikacji:

```xml
<PropertyGroup>
  <PublishTrimmed>false</PublishTrimmed>
</PropertyGroup>
```

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Przycinanie samodzielnych wdrożeń i plików wykonywalnych](/dotnet/core/deploying/trim-self-contained)
* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-trimming>
