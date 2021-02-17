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
ms.openlocfilehash: 1161f7731898221e51a4c7f9f246269b83c6ae80
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552407"
---
::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="fa648-101">Uruchamianie Identity szkieletu:</span><span class="sxs-lookup"><span data-stu-id="fa648-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fa648-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fa648-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="fa648-103">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt > **Dodaj** > **nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="fa648-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="fa648-104">W lewym okienku okna dialogowego **Dodawanie szkieletu** wybierz pozycję **Identity** > **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="fa648-104">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="fa648-105">W oknie **dialogowym Identity Dodawanie** wybierz odpowiednie opcje.</span><span class="sxs-lookup"><span data-stu-id="fa648-105">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="fa648-106">Wybierz istniejącą stronę układu, aby plik układu nie został zastąpiony nieprawidłowym znacznikiem.</span><span class="sxs-lookup"><span data-stu-id="fa648-106">Select your existing layout page so your layout file isn't overwritten with incorrect markup.</span></span> <span data-ttu-id="fa648-107">Gdy zostanie wybrany istniejący plik *\_ Layout. cshtml* , **nie** zostanie on nadpisany.</span><span class="sxs-lookup"><span data-stu-id="fa648-107">When an existing *\_Layout.cshtml* file is selected, it is **not** overwritten.</span></span> <span data-ttu-id="fa648-108">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="fa648-108">For example:</span></span>
    * <span data-ttu-id="fa648-109">`~/Pages/Shared/_Layout.cshtml` dla Razor stron lub Blazor Server projektów z istniejącą Razor infrastrukturą stron</span><span class="sxs-lookup"><span data-stu-id="fa648-109">`~/Pages/Shared/_Layout.cshtml` for Razor Pages or Blazor Server projects with existing Razor Pages infrastructure</span></span>
    * <span data-ttu-id="fa648-110">`~/Views/Shared/_Layout.cshtml` dla projektów MVC lub Blazor Server projektów z istniejącą infrastrukturą MVC</span><span class="sxs-lookup"><span data-stu-id="fa648-110">`~/Views/Shared/_Layout.cshtml` for MVC projects or Blazor Server projects with existing MVC infrastructure</span></span>
* <span data-ttu-id="fa648-111">Aby użyć istniejącego kontekstu danych, wybierz co najmniej jeden plik do przesłonięcia.</span><span class="sxs-lookup"><span data-stu-id="fa648-111">To use your existing data context, select at least one file to override.</span></span> <span data-ttu-id="fa648-112">Musisz wybrać co najmniej jeden plik, aby dodać kontekst danych.</span><span class="sxs-lookup"><span data-stu-id="fa648-112">You must select at least one file to add your data context.</span></span>
  * <span data-ttu-id="fa648-113">Wybierz klasę kontekstu danych.</span><span class="sxs-lookup"><span data-stu-id="fa648-113">Select your data context class.</span></span>
  * <span data-ttu-id="fa648-114">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="fa648-114">Select **Add**.</span></span>
* <span data-ttu-id="fa648-115">Aby utworzyć nowy kontekst użytkownika i ewentualnie utworzyć niestandardową klasę użytkownika dla Identity :</span><span class="sxs-lookup"><span data-stu-id="fa648-115">To create a new user context and possibly create a custom user class for Identity:</span></span>
  * <span data-ttu-id="fa648-116">Wybierz **+** przycisk, aby utworzyć nową **klasę kontekstu danych**.</span><span class="sxs-lookup"><span data-stu-id="fa648-116">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="fa648-117">Zaakceptuj wartość domyślną lub określ klasę (na przykład `MyApplication.Data.ApplicationDbContext` ).</span><span class="sxs-lookup"><span data-stu-id="fa648-117">Accept the default value or specify a class (for example, `MyApplication.Data.ApplicationDbContext`).</span></span>
  * <span data-ttu-id="fa648-118">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="fa648-118">Select **Add**.</span></span>

<span data-ttu-id="fa648-119">Uwaga: w przypadku tworzenia nowego kontekstu użytkownika nie trzeba wybierać pliku do przesłonięcia.</span><span class="sxs-lookup"><span data-stu-id="fa648-119">Note: If you're creating a new user context, you don't have to select a file to override.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="fa648-120">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="fa648-120">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="fa648-121">Jeśli jeszcze nie zainstalowano szkieletu ASP.NET Core, zainstaluj go teraz:</span><span class="sxs-lookup"><span data-stu-id="fa648-121">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="fa648-122">Dodaj wymagane odwołania do pakietu NuGet do pliku projektu (*. csproj*).</span><span class="sxs-lookup"><span data-stu-id="fa648-122">Add required NuGet package references to the project file (*.csproj*).</span></span> <span data-ttu-id="fa648-123">Uruchom następujące polecenia w katalogu projektu:</span><span class="sxs-lookup"><span data-stu-id="fa648-123">Run the following commands in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="fa648-124">Uruchom następujące polecenie, aby wyświetlić listę Identity opcji szkieletu:</span><span class="sxs-lookup"><span data-stu-id="fa648-124">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

<span data-ttu-id="fa648-125">W folderze projektu uruchom program do tworzenia Identity szkieletu z żądanymi opcjami.</span><span class="sxs-lookup"><span data-stu-id="fa648-125">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="fa648-126">Na przykład, aby skonfigurować tożsamość przy użyciu domyślnego interfejsu użytkownika i minimalnej liczby plików, uruchom następujące polecenie.</span><span class="sxs-lookup"><span data-stu-id="fa648-126">For example, to setup identity with the default UI and the minimum number of files, run the following command.</span></span> <span data-ttu-id="fa648-127">Użyj prawidłowej w pełni kwalifikowanej nazwy dla kontekstu bazy danych:</span><span class="sxs-lookup"><span data-stu-id="fa648-127">Use the correct fully qualified name for your DB context:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

<span data-ttu-id="fa648-128">Program PowerShell używa średnika jako separatora poleceń.</span><span class="sxs-lookup"><span data-stu-id="fa648-128">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="fa648-129">W przypadku korzystania z programu PowerShell wpisz średniki na liście plików lub Umieść listę plików w podwójnych cudzysłowach.</span><span class="sxs-lookup"><span data-stu-id="fa648-129">When using PowerShell, escape the semi-colons in the file list or put the file list in double quotes.</span></span> <span data-ttu-id="fa648-130">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="fa648-130">For example:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="fa648-131">W przypadku uruchomienia Identity szkieletu bez określenia `--files` flagi lub `--useDefaultUI` flagi wszystkie dostępne Identity strony interfejsu użytkownika zostaną utworzone w projekcie.</span><span class="sxs-lookup"><span data-stu-id="fa648-131">If you run the Identity scaffolder without specifying the `--files` flag or the `--useDefaultUI` flag, all the available Identity UI pages will be created in your project.</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="fa648-132">Uruchamianie Identity szkieletu:</span><span class="sxs-lookup"><span data-stu-id="fa648-132">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fa648-133">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fa648-133">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="fa648-134">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt > **Dodaj** > **nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="fa648-134">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="fa648-135">W lewym okienku okna dialogowego **Dodawanie szkieletu** wybierz pozycję **Identity** > **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="fa648-135">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="fa648-136">W oknie **dialogowym Identity Dodawanie** wybierz odpowiednie opcje.</span><span class="sxs-lookup"><span data-stu-id="fa648-136">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="fa648-137">Wybierz istniejącą stronę układu lub plik układu zostanie zastąpiony nieprawidłowym znacznikiem.</span><span class="sxs-lookup"><span data-stu-id="fa648-137">Select your existing layout page, or your layout file will be overwritten with incorrect markup.</span></span> <span data-ttu-id="fa648-138">Gdy zostanie wybrany istniejący plik *\_ Layout. cshtml* , **nie** zostanie on nadpisany.</span><span class="sxs-lookup"><span data-stu-id="fa648-138">When an existing *\_Layout.cshtml* file is selected, it is **not** overwritten.</span></span> <span data-ttu-id="fa648-139">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="fa648-139">For example:</span></span>
    * <span data-ttu-id="fa648-140">`~/Pages/Shared/_Layout.cshtml` dla Razor stron</span><span class="sxs-lookup"><span data-stu-id="fa648-140">`~/Pages/Shared/_Layout.cshtml` for Razor Pages</span></span>
    * <span data-ttu-id="fa648-141">`~/Views/Shared/_Layout.cshtml` dla projektów MVC</span><span class="sxs-lookup"><span data-stu-id="fa648-141">`~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
* <span data-ttu-id="fa648-142">Aby użyć istniejącego kontekstu danych, wybierz co najmniej jeden plik do przesłonięcia.</span><span class="sxs-lookup"><span data-stu-id="fa648-142">To use your existing data context, select at least one file to override.</span></span> <span data-ttu-id="fa648-143">Musisz wybrać co najmniej jeden plik, aby dodać kontekst danych.</span><span class="sxs-lookup"><span data-stu-id="fa648-143">You must select at least one file to add your data context.</span></span>
  * <span data-ttu-id="fa648-144">Wybierz klasę kontekstu danych.</span><span class="sxs-lookup"><span data-stu-id="fa648-144">Select your data context class.</span></span>
  * <span data-ttu-id="fa648-145">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="fa648-145">Select **Add**.</span></span>
* <span data-ttu-id="fa648-146">Aby utworzyć nowy kontekst użytkownika i ewentualnie utworzyć niestandardową klasę użytkownika dla Identity :</span><span class="sxs-lookup"><span data-stu-id="fa648-146">To create a new user context and possibly create a custom user class for Identity:</span></span>
  * <span data-ttu-id="fa648-147">Wybierz **+** przycisk, aby utworzyć nową **klasę kontekstu danych**.</span><span class="sxs-lookup"><span data-stu-id="fa648-147">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="fa648-148">Zaakceptuj wartość domyślną lub określ klasę (na przykład `MyApplication.Data.ApplicationDbContext` ).</span><span class="sxs-lookup"><span data-stu-id="fa648-148">Accept the default value or specify a class (for example, `MyApplication.Data.ApplicationDbContext`).</span></span>
  * <span data-ttu-id="fa648-149">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="fa648-149">Select **Add**.</span></span>

<span data-ttu-id="fa648-150">Uwaga: w przypadku tworzenia nowego kontekstu użytkownika nie trzeba wybierać pliku do przesłonięcia.</span><span class="sxs-lookup"><span data-stu-id="fa648-150">Note: If you're creating a new user context, you don't have to select a file to override.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="fa648-151">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="fa648-151">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="fa648-152">Jeśli jeszcze nie zainstalowano szkieletu ASP.NET Core, zainstaluj go teraz:</span><span class="sxs-lookup"><span data-stu-id="fa648-152">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="fa648-153">Dodaj odwołanie do pakietu [Microsoft. VisualStudio. Web. CodeGeneration. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) do pliku projektu (*. csproj*).</span><span class="sxs-lookup"><span data-stu-id="fa648-153">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project file (*.csproj*).</span></span> <span data-ttu-id="fa648-154">Uruchom następujące polecenia w katalogu projektu:</span><span class="sxs-lookup"><span data-stu-id="fa648-154">Run the following commands in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="fa648-155">Uruchom następujące polecenie, aby wyświetlić listę Identity opcji szkieletu:</span><span class="sxs-lookup"><span data-stu-id="fa648-155">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="fa648-156">W folderze projektu uruchom program do tworzenia Identity szkieletu z żądanymi opcjami.</span><span class="sxs-lookup"><span data-stu-id="fa648-156">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="fa648-157">Na przykład, aby skonfigurować tożsamość przy użyciu domyślnego interfejsu użytkownika i minimalnej liczby plików, uruchom następujące polecenie.</span><span class="sxs-lookup"><span data-stu-id="fa648-157">For example, to setup identity with the default UI and the minimum number of files, run the following command.</span></span> <span data-ttu-id="fa648-158">Użyj prawidłowej w pełni kwalifikowanej nazwy dla kontekstu bazy danych:</span><span class="sxs-lookup"><span data-stu-id="fa648-158">Use the correct fully qualified name for your DB context:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

<span data-ttu-id="fa648-159">Program PowerShell używa średnika jako separatora poleceń.</span><span class="sxs-lookup"><span data-stu-id="fa648-159">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="fa648-160">W przypadku korzystania z programu PowerShell wpisz średniki na liście plików lub Umieść listę plików w podwójnych cudzysłowach.</span><span class="sxs-lookup"><span data-stu-id="fa648-160">When using PowerShell, escape the semi-colons in the file list or put the file list in double quotes.</span></span> <span data-ttu-id="fa648-161">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="fa648-161">For example:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="fa648-162">W przypadku uruchomienia Identity szkieletu bez określenia `--files` flagi lub `--useDefaultUI` flagi wszystkie dostępne Identity strony interfejsu użytkownika zostaną utworzone w projekcie.</span><span class="sxs-lookup"><span data-stu-id="fa648-162">If you run the Identity scaffolder without specifying the `--files` flag or the `--useDefaultUI` flag, all the available Identity UI pages will be created in your project.</span></span>

---

::: moniker-end
