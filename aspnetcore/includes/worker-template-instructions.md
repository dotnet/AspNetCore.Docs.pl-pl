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
ms.openlocfilehash: 34e12afd6bc7015e4609fbdf773259ed5545809b
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551844"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Tworzenie nowego projektu.
1. Wybierz pozycję **Usługa procesu roboczego**. Wybierz opcję **Dalej**.
1. Podaj nazwę projektu w polu **Nazwa projektu** lub zaakceptuj nazwę domyślną projektu. Wybierz przycisk **Utwórz**.
1. W oknie dialogowym **Tworzenie nowej usługi procesu roboczego** wybierz pozycję **Utwórz**.

# <a name="visual-studio-for-mac"></a>[Visual Studio dla komputerów Mac](#tab/visual-studio-mac)

1. Tworzenie nowego projektu.
1. Na pasku bocznym wybierz pozycję **aplikacja** na **platformie .NET Core** .
1. Wybierz pozycję **proces roboczy** w obszarze **ASP.NET Core**. Wybierz opcję **Dalej**.
1. Wybierz **platformę .NET Core 3,0** lub nowszą dla **platformy docelowej**. Wybierz opcję **Dalej**.
1. Podaj nazwę w polu **Nazwa projektu** . Wybierz przycisk **Utwórz**.

# <a name="net-core-cli"></a>[interfejs wiersza polecenia programu .NET Core](#tab/netcore-cli)

Użyj szablonu usługi procesu roboczego ( `worker` ) z poleceniem [dotnet New](/dotnet/core/tools/dotnet-new) z powłoki poleceń. W poniższym przykładzie jest tworzona aplikacja usługi Worker o nazwie `ContosoWorker` . Folder dla `ContosoWorker` aplikacji jest tworzony automatycznie podczas wykonywania polecenia.

```dotnetcli
dotnet new worker -o ContosoWorker
```

---
