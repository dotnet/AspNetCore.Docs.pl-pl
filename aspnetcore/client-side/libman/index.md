---
title: Pozyskiwanie biblioteki po stronie klienta w ASP.NET Core z LibMan
author: scottaddie
description: Dowiedz się, jak zainstalować zasoby biblioteki po stronie klienta w projekcie ASP.NET Core przy użyciu programu Library Manager (LibMan).
ms.author: scaddie
ms.custom: mvc
ms.date: 08/14/2018
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: client-side/libman/index
ms.openlocfilehash: a9b0f389303709ebf27e6c300ac2489d4e5a86a9
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85403084"
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

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:client-side/libman/libman-vs>
* <xref:client-side/libman/libman-cli>
* [Repozytorium GitHub LibMan](https://github.com/aspnet/LibraryManager)
