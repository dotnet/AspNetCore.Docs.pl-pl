---
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
ms.openlocfilehash: 05c94351ee4747813cfa8dc2318a6fc02c3a46bf
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551702"
---
```console
npm run release
```

To polecenie generuje zasoby po stronie klienta, które mają być obsługiwane podczas uruchamiania aplikacji. Elementy zawartości są umieszczane w folderze *wwwroot* .

Pakiet WebPack ukończył następujące zadania:

* Przeczyszczanie zawartości katalogu *wwwroot* .
* Przekonwertowane TypeScript na JavaScript w procesie znanym jako *transpilation*.
* Zniekształcona wygenerowany kod JavaScript, aby zmniejszyć rozmiar pliku w procesie znanym jako *minifikacja*.
* Skopiowano przetworzone pliki JavaScript, CSS i HTML z pliku *src* do katalogu *wwwroot* .
* Następujące elementy zostały dodane do pliku *wwwroot/index.html* :
  * `<link>`Tag, który odwołuje się do elementu *wwwroot/Main. \<hash\> . plik CSS* . Ten tag jest umieszczany bezpośrednio przed tagiem zamykającym `</head>` .
  * `<script>`Tag, który odwołuje się do zminimalizowanego *wwwroot/Main. \<hash\> . plik js* . Ten tag jest umieszczany bezpośrednio przed tagiem zamykającym `</body>` .