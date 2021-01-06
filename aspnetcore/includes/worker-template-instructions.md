# <a name="visual-studio"></a>[<span data-ttu-id="11a8c-101">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="11a8c-101">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="11a8c-102">Tworzenie nowego projektu.</span><span class="sxs-lookup"><span data-stu-id="11a8c-102">Create a new project.</span></span>
1. <span data-ttu-id="11a8c-103">Wybierz pozycję **Usługa procesu roboczego**.</span><span class="sxs-lookup"><span data-stu-id="11a8c-103">Select **Worker Service**.</span></span> <span data-ttu-id="11a8c-104">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="11a8c-104">Select **Next**.</span></span>
1. <span data-ttu-id="11a8c-105">Podaj nazwę projektu w polu **Nazwa projektu** lub zaakceptuj nazwę domyślną projektu.</span><span class="sxs-lookup"><span data-stu-id="11a8c-105">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="11a8c-106">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="11a8c-106">Select **Create**.</span></span>
1. <span data-ttu-id="11a8c-107">W oknie dialogowym **Tworzenie nowej usługi procesu roboczego** wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="11a8c-107">In the **Create a new Worker service** dialog, select **Create**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="11a8c-108">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="11a8c-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="11a8c-109">Tworzenie nowego projektu.</span><span class="sxs-lookup"><span data-stu-id="11a8c-109">Create a new project.</span></span>
1. <span data-ttu-id="11a8c-110">Na pasku bocznym wybierz pozycję **aplikacja** na **platformie .NET Core** .</span><span class="sxs-lookup"><span data-stu-id="11a8c-110">Select **App** under **.NET Core** in the sidebar.</span></span>
1. <span data-ttu-id="11a8c-111">Wybierz pozycję **proces roboczy** w obszarze **ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="11a8c-111">Select **Worker** under **ASP.NET Core**.</span></span> <span data-ttu-id="11a8c-112">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="11a8c-112">Select **Next**.</span></span>
1. <span data-ttu-id="11a8c-113">Wybierz **platformę .NET Core 3,0** lub nowszą dla **platformy docelowej**.</span><span class="sxs-lookup"><span data-stu-id="11a8c-113">Select **.NET Core 3.0** or later for the **Target Framework**.</span></span> <span data-ttu-id="11a8c-114">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="11a8c-114">Select **Next**.</span></span>
1. <span data-ttu-id="11a8c-115">Podaj nazwę w polu **Nazwa projektu** .</span><span class="sxs-lookup"><span data-stu-id="11a8c-115">Provide a name in the **Project Name** field.</span></span> <span data-ttu-id="11a8c-116">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="11a8c-116">Select **Create**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="11a8c-117">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="11a8c-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="11a8c-118">Użyj szablonu usługi procesu roboczego ( `worker` ) z poleceniem [dotnet New](/dotnet/core/tools/dotnet-new) z powłoki poleceń.</span><span class="sxs-lookup"><span data-stu-id="11a8c-118">Use the Worker Service (`worker`) template with the [dotnet new](/dotnet/core/tools/dotnet-new) command from a command shell.</span></span> <span data-ttu-id="11a8c-119">W poniższym przykładzie jest tworzona aplikacja usługi Worker o nazwie `ContosoWorker` .</span><span class="sxs-lookup"><span data-stu-id="11a8c-119">In the following example, a Worker Service app is created named `ContosoWorker`.</span></span> <span data-ttu-id="11a8c-120">Folder dla `ContosoWorker` aplikacji jest tworzony automatycznie podczas wykonywania polecenia.</span><span class="sxs-lookup"><span data-stu-id="11a8c-120">A folder for the `ContosoWorker` app is created automatically when the command is executed.</span></span>

```dotnetcli
dotnet new worker -o ContosoWorker
```

---
