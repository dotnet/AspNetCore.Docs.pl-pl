---
title: Pozyskiwanie biblioteki po stronie klienta w ASP.NET Core z LibMan
author: scottaddie
description: Dowiedz się, jak zainstalować zasoby biblioteki po stronie klienta w projekcie ASP.NET Core przy użyciu programu Library Manager (LibMan).
ms.author: scaddie
ms.custom: mvc
ms.date: 08/14/2018
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
uid: client-side/libman/index
ms.openlocfilehash: 62b6859b0a8ad0f98a2684f21c0f68dbbd67c4c1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054661"
---
# <a name="client-side-library-acquisition-in-aspnet-core-with-libman"></a>Pozyskiwanie biblioteki po stronie klienta w ASP.NET Core z LibMan

Przez [Scott Addie](https://twitter.com/Scott_Addie)

Library Manager (LibMan) to lekkie narzędzie do pozyskiwania bibliotek po stronie klienta. LibMan pobiera popularne biblioteki i struktury z systemu plików lub z usługi [Content Delivery Network (CDN)](https://wikipedia.org/wiki/Content_delivery_network). Obsługiwane sieci CDN obejmują [CDNJS](https://cdnjs.com/), [jsDelivr](https://www.jsdelivr.com/)i [unpkg](https://unpkg.com/#/). Wybrane pliki biblioteki są pobierane i umieszczane w odpowiedniej lokalizacji w ramach projektu ASP.NET Core.

## <a name="libman-use-cases"></a>LibMan przypadki użycia

LibMan oferuje następujące korzyści:

* Pobierane są tylko pliki biblioteki, które są potrzebne.
* Dodatkowe narzędzia, takie jak [Node.js](https://nodejs.org), [npm](https://www.npmjs.com)i [WebPack](https://webpack.js.org), nie są niezbędne do uzyskania podzestawu plików w bibliotece.
* Pliki można umieścić w określonej lokalizacji bez konieczności tworzenia zadań kompilacji ani ręcznego kopiowania plików.

Aby uzyskać więcej informacji na temat korzyści z programu LibMan, Obejrzyj [nowoczesne projektowanie sieci Web frontonu w programie Visual Studio 2017: LibMan segment](https://channel9.msdn.com/Events/Build/2017/B8073#time=43m34s).

LibMan nie jest system zarządzania pakietami. Jeśli już używasz Menedżera pakietów, takiego jak npm lub [przędza](https://yarnpkg.com), Kontynuuj. Nie opracowano LibMan do zastępowania tych narzędzi.

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:client-side/libman/libman-vs>
* <xref:client-side/libman/libman-cli>
* [Repozytorium GitHub LibMan](https://github.com/aspnet/LibraryManager)
