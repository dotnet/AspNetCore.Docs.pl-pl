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
ms.openlocfilehash: ed6de823b8b860c7d27e932e9ece40d8b43b0893
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552027"
---
<span data-ttu-id="2c80c-101">Uruchamianie Identity szkieletu:</span><span class="sxs-lookup"><span data-stu-id="2c80c-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2c80c-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c80c-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2c80c-103">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt > **Dodaj**  >  **nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="2c80c-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="2c80c-104">W lewym okienku okna dialogowego **Dodaj nowy element szkieletowy** wybierz pozycję **Identity**  >  **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="2c80c-104">From the left pane of the **Add New Scaffolded Item** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="2c80c-105">W oknie **dialogowym Identity Dodawanie** wybierz odpowiednie opcje.</span><span class="sxs-lookup"><span data-stu-id="2c80c-105">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="2c80c-106">Wybierz istniejącą stronę układu lub plik układu zostanie zastąpiony nieprawidłowym znacznikiem:</span><span class="sxs-lookup"><span data-stu-id="2c80c-106">Select your existing layout page, or your layout file will be overwritten with incorrect markup:</span></span>
    * <span data-ttu-id="2c80c-107">`~/Pages/Shared/_Layout.cshtml` dla Razor stron</span><span class="sxs-lookup"><span data-stu-id="2c80c-107">`~/Pages/Shared/_Layout.cshtml` for Razor Pages</span></span>
    * <span data-ttu-id="2c80c-108">`~/Views/Shared/_Layout.cshtml` dla projektów MVC</span><span class="sxs-lookup"><span data-stu-id="2c80c-108">`~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
    * <span data-ttu-id="2c80c-109">Blazor Server aplikacje utworzone na podstawie Blazor Server szablonu ( `blazorserver` ) nie są domyślnie skonfigurowane dla Razor stron lub MVC.</span><span class="sxs-lookup"><span data-stu-id="2c80c-109">Blazor Server apps created from the Blazor Server template (`blazorserver`) aren't configured for Razor Pages or MVC by default.</span></span> <span data-ttu-id="2c80c-110">Pozostaw pusty wpis strony układu.</span><span class="sxs-lookup"><span data-stu-id="2c80c-110">Leave the layout page entry blank.</span></span>
  * <span data-ttu-id="2c80c-111">Wybierz **+** przycisk, aby utworzyć nową **klasę kontekstu danych**.</span><span class="sxs-lookup"><span data-stu-id="2c80c-111">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="2c80c-112">Zaakceptuj wartość domyślną lub określ klasę (na przykład `MyApplication.Data.ApplicationDbContext` ).</span><span class="sxs-lookup"><span data-stu-id="2c80c-112">Accept the default value or specify a class (for example, `MyApplication.Data.ApplicationDbContext`).</span></span>
* <span data-ttu-id="2c80c-113">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="2c80c-113">Select **Add**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="2c80c-114">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="2c80c-114">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="2c80c-115">Jeśli jeszcze nie zainstalowano szkieletu ASP.NET Core, zainstaluj go teraz:</span><span class="sxs-lookup"><span data-stu-id="2c80c-115">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="2c80c-116">Dodaj wymagane odwołania do pakietu NuGet do pliku projektu (*. csproj*).</span><span class="sxs-lookup"><span data-stu-id="2c80c-116">Add required NuGet package references to the project file (*.csproj*).</span></span> <span data-ttu-id="2c80c-117">Uruchom następujące polecenia w katalogu projektu:</span><span class="sxs-lookup"><span data-stu-id="2c80c-117">Run the following commands in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="2c80c-118">Uruchom następujące polecenie, aby wyświetlić listę Identity opcji szkieletu:</span><span class="sxs-lookup"><span data-stu-id="2c80c-118">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

<span data-ttu-id="2c80c-119">W folderze projektu uruchom program do tworzenia Identity szkieletu z żądanymi opcjami.</span><span class="sxs-lookup"><span data-stu-id="2c80c-119">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="2c80c-120">Na przykład, aby skonfigurować tożsamość przy użyciu domyślnego interfejsu użytkownika i minimalnej liczby plików, uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="2c80c-120">For example, to setup identity with the default UI and the minimum number of files, run the following command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---
