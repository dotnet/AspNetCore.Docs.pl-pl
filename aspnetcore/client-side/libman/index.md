---
title: Przejęcie biblioteki po stronie klienta w ASP.NET Core z LibMan
author: scottaddie
description: Dowiedz się, jak zainstalować zasoby biblioteki po stronie klienta w projekcie ASP.NET Core przy użyciu Menedżera bibliotek (LibMan).
ms.author: scaddie
ms.custom: mvc
ms.date: 08/14/2018
uid: client-side/libman/index
ms.openlocfilehash: 87987446b7f2c625da90951510e697e06569ba36
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664669"
---
# <a name="client-side-library-acquisition-in-aspnet-core-with-libman"></a>Przejęcie biblioteki po stronie klienta w ASP.NET Core z LibMan

Przez [Scott Addie](https://twitter.com/Scott_Addie)

Menedżer biblioteki (LibMan) jest lekkim narzędziem do pozyskiwania bibliotek po stronie klienta. LibMan pobiera popularne biblioteki i struktury z systemu plików lub z [sieci dostarczania zawartości (CDN).](https://wikipedia.org/wiki/Content_delivery_network) Obsługiwane sieci CDN obejmują [CDNJS](https://cdnjs.com/), [jsDelivr](https://www.jsdelivr.com/)i [unpkg](https://unpkg.com/#/). Wybrane pliki biblioteki są pobierane i umieszczane w odpowiednim miejscu w projekcie ASP.NET Core.

## <a name="libman-use-cases"></a>Przypadki użycia LibMana

LibMan oferuje następujące korzyści:

* Pobierane są tylko potrzebne pliki biblioteki.
* Dodatkowe narzędzia, takie jak [Node.js](https://nodejs.org), [npm](https://www.npmjs.com)i [WebPack,](https://webpack.js.org)nie są konieczne do uzyskania podzbioru plików w bibliotece.
* Pliki można umieszczać w określonej lokalizacji bez uciekania się do tworzenia zadań lub ręcznego kopiowania plików.

Aby uzyskać więcej informacji na temat korzyści libman, obejrzyj [nowoczesne tworzenie stron internetowych front-end w programie Visual Studio 2017: segment LibMan](https://channel9.msdn.com/Events/Build/2017/B8073#time=43m34s).

LibMan nie jest systemem zarządzania pakietami. Jeśli korzystasz już z menedżera pakietów, takiego jak npm lub [przędza,](https://yarnpkg.com)kontynuuj ten problem. LibMan nie został opracowany, aby zastąpić te narzędzia.

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:client-side/libman/libman-vs>
* <xref:client-side/libman/libman-cli>
* [Repozytorium LibMan GitHub](https://github.com/aspnet/LibraryManager)
