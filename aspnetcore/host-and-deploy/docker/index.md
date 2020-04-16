---
title: Host ASP.NET Core w kontenerach platformy Docker
author: rick-anderson
description: Odnajduj łącza do zasobów, aby dowiedzieć się, jak hostować aplikacje ASP.NET Core w kontenerach platformy Docker.
ms.author: riande
ms.custom: mvc
ms.date: 01/08/2018
uid: host-and-deploy/docker/index
ms.openlocfilehash: 78eb6f467c7e779e1bf998e8ac8e917a90895db9
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440730"
---
# <a name="host-aspnet-core-in-docker-containers"></a>Host ASP.NET Core w kontenerach platformy Docker

Następujące artykuły są dostępne do nauki o hostingu aplikacji ASP.NET Core w platformie Docker:

[Wprowadzenie do kontenerów i platformy Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index)  
Zobacz, jak konteneryzacja jest podejście do tworzenia oprogramowania, w którym aplikacja lub usługa, jego zależności i jego konfiguracji są pakowane razem jako obraz kontenera. Obraz można przetestować, a następnie wdrożyć na hoście.

[Co to jest docker](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-defined)  
Dowiedz się, jak docker jest projektem typu open source do automatyzacji wdrażania aplikacji jako przenośnych, samowystarczalnych kontenerów, które mogą działać w chmurze lub lokalnie.

[Terminologia platformy Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-terminology)  
Poznaj terminy i definicje technologii Platformy Docker.

[Kontenery, obrazy i rejestry platformy Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-containers-images-registries)  
Dowiedz się, jak obrazy kontenerów platformy Docker są przechowywane w rejestrze obrazów w celu spójnego wdrażania w różnych środowiskach.

<xref:host-and-deploy/docker/building-net-docker-images>Dowiedz się, jak tworzyć i dockerize aplikacji ASP.NET Core. Eksploruj obrazy platformy Docker obsługiwane przez firmę Microsoft i badaj przypadki użycia.

[Narzędzia kontenerów programu Visual Studio](xref:host-and-deploy/docker/visual-studio-tools-for-docker)  
Dowiedz się, jak program Visual Studio obsługuje tworzenie, debugowanie i uruchamianie aplikacji ASP.NET Core przeznaczonych dla programu .NET Framework lub .NET Core w platformie Docker dla systemu Windows. Obsługiwane są kontenery zarówno systemu Windows, jak i Linux.

[Publikowanie w usłudze Azure Container Registry](/azure/vs-azure-tools-docker-hosting-web-apps-in-docker)  
Dowiedz się, jak użyć rozszerzenia Narzędzia kontenerów programu Visual Studio do wdrożenia aplikacji ASP.NET Core na hoście platformy Docker na platformie Azure przy użyciu programu PowerShell.

[Konfigurowanie ASP.NET Core do pracy z serwerami proxy i modułami równoważenia obciążenia](xref:host-and-deploy/proxy-load-balancer)  
Dodatkowa konfiguracja może być wymagana dla aplikacji hostowanych za serwerami proxy i modułami równoważenia obciążenia. Przekazywanie żądań za pośrednictwem serwera proxy często zasłania informacje o oryginalnym żądaniu, takie jak schemat i adres IP klienta. Może być konieczne przekazanie niektórych informacji o żądaniu ręcznie do aplikacji.

[GC przy użyciu platformy Docker i małych kontenerów](xref:performance/memory#sc) W tym artykule omówiono wybór GC z małymi pojemnikami.