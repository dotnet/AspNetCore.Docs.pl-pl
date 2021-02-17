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
# <a name="visual-studio"></a>[<span data-ttu-id="ea64b-101">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ea64b-101">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="ea64b-102">Tworzenie nowego projektu.</span><span class="sxs-lookup"><span data-stu-id="ea64b-102">Create a new project.</span></span>
1. <span data-ttu-id="ea64b-103">Wybierz pozycję **Usługa procesu roboczego**.</span><span class="sxs-lookup"><span data-stu-id="ea64b-103">Select **Worker Service**.</span></span> <span data-ttu-id="ea64b-104">Wybierz opcję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="ea64b-104">Select **Next**.</span></span>
1. <span data-ttu-id="ea64b-105">Podaj nazwę projektu w polu **Nazwa projektu** lub zaakceptuj nazwę domyślną projektu.</span><span class="sxs-lookup"><span data-stu-id="ea64b-105">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="ea64b-106">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="ea64b-106">Select **Create**.</span></span>
1. <span data-ttu-id="ea64b-107">W oknie dialogowym **Tworzenie nowej usługi procesu roboczego** wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="ea64b-107">In the **Create a new Worker service** dialog, select **Create**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ea64b-108">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="ea64b-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="ea64b-109">Tworzenie nowego projektu.</span><span class="sxs-lookup"><span data-stu-id="ea64b-109">Create a new project.</span></span>
1. <span data-ttu-id="ea64b-110">Na pasku bocznym wybierz pozycję **aplikacja** na **platformie .NET Core** .</span><span class="sxs-lookup"><span data-stu-id="ea64b-110">Select **App** under **.NET Core** in the sidebar.</span></span>
1. <span data-ttu-id="ea64b-111">Wybierz pozycję **proces roboczy** w obszarze **ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="ea64b-111">Select **Worker** under **ASP.NET Core**.</span></span> <span data-ttu-id="ea64b-112">Wybierz opcję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="ea64b-112">Select **Next**.</span></span>
1. <span data-ttu-id="ea64b-113">Wybierz **platformę .NET Core 3,0** lub nowszą dla **platformy docelowej**.</span><span class="sxs-lookup"><span data-stu-id="ea64b-113">Select **.NET Core 3.0** or later for the **Target Framework**.</span></span> <span data-ttu-id="ea64b-114">Wybierz opcję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="ea64b-114">Select **Next**.</span></span>
1. <span data-ttu-id="ea64b-115">Podaj nazwę w polu **Nazwa projektu** .</span><span class="sxs-lookup"><span data-stu-id="ea64b-115">Provide a name in the **Project Name** field.</span></span> <span data-ttu-id="ea64b-116">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="ea64b-116">Select **Create**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="ea64b-117">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="ea64b-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="ea64b-118">Użyj szablonu usługi procesu roboczego ( `worker` ) z poleceniem [dotnet New](/dotnet/core/tools/dotnet-new) z powłoki poleceń.</span><span class="sxs-lookup"><span data-stu-id="ea64b-118">Use the Worker Service (`worker`) template with the [dotnet new](/dotnet/core/tools/dotnet-new) command from a command shell.</span></span> <span data-ttu-id="ea64b-119">W poniższym przykładzie jest tworzona aplikacja usługi Worker o nazwie `ContosoWorker` .</span><span class="sxs-lookup"><span data-stu-id="ea64b-119">In the following example, a Worker Service app is created named `ContosoWorker`.</span></span> <span data-ttu-id="ea64b-120">Folder dla `ContosoWorker` aplikacji jest tworzony automatycznie podczas wykonywania polecenia.</span><span class="sxs-lookup"><span data-stu-id="ea64b-120">A folder for the `ContosoWorker` app is created automatically when the command is executed.</span></span>

```dotnetcli
dotnet new worker -o ContosoWorker
```

---
