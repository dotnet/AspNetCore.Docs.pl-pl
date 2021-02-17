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
ms.openlocfilehash: d05b0f327eb1670847125e6a73ef7277ebd069b5
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552726"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Naciśnij klawisze CTRL + F5, aby uruchomić bez debugera.

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Program Visual Studio jest uruchamiany [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) i uruchomi aplikację. Na pasku adresu są wyświetlane inne elementy `localhost:port#` , takie jak `example.com` . Wynika to z tego, że `localhost` jest standardową nazwą hosta dla komputera lokalnego. Host lokalny obsługuje tylko żądania sieci Web z komputera lokalnego. Podczas tworzenia projektu internetowego w programie Visual Studio dla serwera internetowego jest używany losowy port.
 
# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* Naciśnij **klawisze CTRL + F5** , aby uruchomić bez debugera.

  Visual Studio Code uruchamia [Kestrel](xref:fundamentals/servers/kestrel), uruchamia przeglądarkę i nawiguje do `http://localhost:5001` . Na pasku adresu są wyświetlane inne elementy `localhost:port#` , takie jak `example.com` . Wynika to z tego, że `localhost` jest standardową nazwą hosta dla komputera lokalnego. Host lokalny obsługuje tylko żądania sieci Web z komputera lokalnego.

  
# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* W programie Visual Studio naciśnij pozycję **opt-cmd-Return** , aby uruchomić bez debugera. Alternatywnie przejdź do paska menu i przejdź do pozycji **uruchom>Uruchom bez debugowania**.

  Program Visual Studio uruchamia [Kestrel](xref:fundamentals/servers/kestrel), uruchamia przeglądarkę i przechodzi do `http://localhost:5001` .

<!-- End of VS tabs -->

---
