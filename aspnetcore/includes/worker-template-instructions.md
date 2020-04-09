# <a name="visual-studio"></a>[<span data-ttu-id="f7cec-101">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f7cec-101">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="f7cec-102">Tworzenie nowego projektu.</span><span class="sxs-lookup"><span data-stu-id="f7cec-102">Create a new project.</span></span>
1. <span data-ttu-id="f7cec-103">Wybierz **pozycję Usługa pracownika**.</span><span class="sxs-lookup"><span data-stu-id="f7cec-103">Select **Worker Service**.</span></span> <span data-ttu-id="f7cec-104">Wybierz **pozycję Dalej**.</span><span class="sxs-lookup"><span data-stu-id="f7cec-104">Select **Next**.</span></span>
1. <span data-ttu-id="f7cec-105">Podaj nazwę projektu w polu **Nazwa projektu** lub zaakceptuj domyślną nazwę projektu.</span><span class="sxs-lookup"><span data-stu-id="f7cec-105">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="f7cec-106">Wybierz **pozycję Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="f7cec-106">Select **Create**.</span></span>
1. <span data-ttu-id="f7cec-107">W oknie **dialogowym Tworzenie nowej usługi pracownik** wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="f7cec-107">In the **Create a new Worker service** dialog, select **Create**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f7cec-108">Visual Studio dla komputerów Mac</span><span class="sxs-lookup"><span data-stu-id="f7cec-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="f7cec-109">Tworzenie nowego projektu.</span><span class="sxs-lookup"><span data-stu-id="f7cec-109">Create a new project.</span></span>
1. <span data-ttu-id="f7cec-110">Wybierz **pozycję Aplikacja** w obszarze **.NET Core** na pasku bocznym.</span><span class="sxs-lookup"><span data-stu-id="f7cec-110">Select **App** under **.NET Core** in the sidebar.</span></span>
1. <span data-ttu-id="f7cec-111">Wybierz **pozycję Pracownik** w obszarze ASP.NET **rdzeń**.</span><span class="sxs-lookup"><span data-stu-id="f7cec-111">Select **Worker** under **ASP.NET Core**.</span></span> <span data-ttu-id="f7cec-112">Wybierz **pozycję Dalej**.</span><span class="sxs-lookup"><span data-stu-id="f7cec-112">Select **Next**.</span></span>
1. <span data-ttu-id="f7cec-113">Wybierz **.NET Core 3.0** lub nowszą dla **struktury docelowej**.</span><span class="sxs-lookup"><span data-stu-id="f7cec-113">Select **.NET Core 3.0** or later for the **Target Framework**.</span></span> <span data-ttu-id="f7cec-114">Wybierz **pozycję Dalej**.</span><span class="sxs-lookup"><span data-stu-id="f7cec-114">Select **Next**.</span></span>
1. <span data-ttu-id="f7cec-115">Podaj nazwę w polu **Nazwa projektu.**</span><span class="sxs-lookup"><span data-stu-id="f7cec-115">Provide a name in the **Project Name** field.</span></span> <span data-ttu-id="f7cec-116">Wybierz **pozycję Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="f7cec-116">Select **Create**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f7cec-117">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="f7cec-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="f7cec-118">Użyj szablonu`worker`Usługa robocza ( ) z nowym poleceniem [dotnet](/dotnet/core/tools/dotnet-new) z powłoki poleceń.</span><span class="sxs-lookup"><span data-stu-id="f7cec-118">Use the Worker Service (`worker`) template with the [dotnet new](/dotnet/core/tools/dotnet-new) command from a command shell.</span></span> <span data-ttu-id="f7cec-119">W poniższym przykładzie zostanie utworzona `ContosoWorker`aplikacja Usługi procesu roboczego o nazwie .</span><span class="sxs-lookup"><span data-stu-id="f7cec-119">In the following example, a Worker Service app is created named `ContosoWorker`.</span></span> <span data-ttu-id="f7cec-120">Folder `ContosoWorker` aplikacji jest tworzony automatycznie po wykonaniu polecenia.</span><span class="sxs-lookup"><span data-stu-id="f7cec-120">A folder for the `ContosoWorker` app is created automatically when the command is executed.</span></span>

```dotnetcli
dotnet new worker -o ContosoWorker
```

---
