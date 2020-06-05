::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4e543-101">Uruchom szkielet tożsamości:</span><span class="sxs-lookup"><span data-stu-id="4e543-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4e543-102">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4e543-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4e543-103">W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy projekt > **Dodaj** > **nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="4e543-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="4e543-104">W lewym okienku okna dialogowego **Dodawanie szkieletu** wybierz pozycję Dodaj **tożsamość** > **Add**.</span><span class="sxs-lookup"><span data-stu-id="4e543-104">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="4e543-105">W oknie dialogowym **Dodawanie tożsamości** wybierz odpowiednie opcje.</span><span class="sxs-lookup"><span data-stu-id="4e543-105">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="4e543-106">Wybierz istniejącą stronę układu, aby plik układu nie został zastąpiony nieprawidłowym znacznikiem.</span><span class="sxs-lookup"><span data-stu-id="4e543-106">Select your existing layout page so your layout file isn't overwritten with incorrect markup.</span></span> <span data-ttu-id="4e543-107">Gdy zostanie wybrany istniejący plik \* \_ Layout. cshtml\* , **nie** zostanie on nadpisany.</span><span class="sxs-lookup"><span data-stu-id="4e543-107">When an existing *\_Layout.cshtml* file is selected, it is **not** overwritten.</span></span> <span data-ttu-id="4e543-108">Przykład:</span><span class="sxs-lookup"><span data-stu-id="4e543-108">For example:</span></span>
    * <span data-ttu-id="4e543-109">`~/Pages/Shared/_Layout.cshtml`w przypadku Razor Pages lub projektów serwera Blazor z istniejącą infrastrukturą Razor Pages</span><span class="sxs-lookup"><span data-stu-id="4e543-109">`~/Pages/Shared/_Layout.cshtml` for Razor Pages or Blazor Server projects with existing Razor Pages infrastructure</span></span>
    * <span data-ttu-id="4e543-110">`~/Views/Shared/_Layout.cshtml`w przypadku projektów MVC lub projektów serwera Blazor z istniejącą infrastrukturą MVC</span><span class="sxs-lookup"><span data-stu-id="4e543-110">`~/Views/Shared/_Layout.cshtml` for MVC projects or Blazor Server projects with existing MVC infrastructure</span></span>
* <span data-ttu-id="4e543-111">Aby użyć istniejącego kontekstu danych, wybierz co najmniej jeden plik do przesłonięcia.</span><span class="sxs-lookup"><span data-stu-id="4e543-111">To use your existing data context, select at least one file to override.</span></span> <span data-ttu-id="4e543-112">Musisz wybrać co najmniej jeden plik, aby dodać kontekst danych.</span><span class="sxs-lookup"><span data-stu-id="4e543-112">You must select at least one file to add your data context.</span></span>
  * <span data-ttu-id="4e543-113">Wybierz klasę kontekstu danych.</span><span class="sxs-lookup"><span data-stu-id="4e543-113">Select your data context class.</span></span>
  * <span data-ttu-id="4e543-114">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="4e543-114">Select **Add**.</span></span>
* <span data-ttu-id="4e543-115">Aby utworzyć nowy kontekst użytkownika i ewentualnie utworzyć niestandardową klasę użytkownika dla tożsamości:</span><span class="sxs-lookup"><span data-stu-id="4e543-115">To create a new user context and possibly create a custom user class for Identity:</span></span>
  * <span data-ttu-id="4e543-116">Wybierz **+** przycisk, aby utworzyć nową **klasę kontekstu danych**.</span><span class="sxs-lookup"><span data-stu-id="4e543-116">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="4e543-117">Zaakceptuj wartość domyślną lub określ klasę (na przykład `MyApplication.Data.ApplicationDbContext` ).</span><span class="sxs-lookup"><span data-stu-id="4e543-117">Accept the default value or specify a class (for example, `MyApplication.Data.ApplicationDbContext`).</span></span>
  * <span data-ttu-id="4e543-118">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="4e543-118">Select **Add**.</span></span>

<span data-ttu-id="4e543-119">Uwaga: w przypadku tworzenia nowego kontekstu użytkownika nie trzeba wybierać pliku do przesłonięcia.</span><span class="sxs-lookup"><span data-stu-id="4e543-119">Note: If you're creating a new user context, you don't have to select a file to override.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="4e543-120">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="4e543-120">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="4e543-121">Jeśli jeszcze nie zainstalowano szkieletu ASP.NET Core, zainstaluj go teraz:</span><span class="sxs-lookup"><span data-stu-id="4e543-121">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="4e543-122">Dodaj wymagane odwołania do pakietu NuGet do pliku projektu (*. csproj*).</span><span class="sxs-lookup"><span data-stu-id="4e543-122">Add required NuGet package references to the project file (*.csproj*).</span></span> <span data-ttu-id="4e543-123">Uruchom następujące polecenia w katalogu projektu:</span><span class="sxs-lookup"><span data-stu-id="4e543-123">Run the following commands in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="4e543-124">Uruchom następujące polecenie, aby wyświetlić listę opcji szkieletu tożsamości:</span><span class="sxs-lookup"><span data-stu-id="4e543-124">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

<span data-ttu-id="4e543-125">W folderze projektu uruchom program do tworzenia szkieletu tożsamości z żądanymi opcjami.</span><span class="sxs-lookup"><span data-stu-id="4e543-125">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="4e543-126">Na przykład, aby skonfigurować tożsamość przy użyciu domyślnego interfejsu użytkownika i minimalnej liczby plików, uruchom następujące polecenie.</span><span class="sxs-lookup"><span data-stu-id="4e543-126">For example, to setup identity with the default UI and the minimum number of files, run the following command.</span></span> <span data-ttu-id="4e543-127">Użyj prawidłowej w pełni kwalifikowanej nazwy dla kontekstu bazy danych:</span><span class="sxs-lookup"><span data-stu-id="4e543-127">Use the correct fully qualified name for your DB context:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

<span data-ttu-id="4e543-128">Program PowerShell używa średnika jako separatora poleceń.</span><span class="sxs-lookup"><span data-stu-id="4e543-128">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="4e543-129">W przypadku korzystania z programu PowerShell wpisz średniki na liście plików lub Umieść listę plików w podwójnych cudzysłowach.</span><span class="sxs-lookup"><span data-stu-id="4e543-129">When using PowerShell, escape the semi-colons in the file list or put the file list in double quotes.</span></span> <span data-ttu-id="4e543-130">Przykład:</span><span class="sxs-lookup"><span data-stu-id="4e543-130">For example:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="4e543-131">W przypadku uruchomienia szkieletu tożsamości bez określenia `--files` flagi lub `--useDefaultUI` flagi wszystkie dostępne strony interfejsu użytkownika tożsamości zostaną utworzone w projekcie.</span><span class="sxs-lookup"><span data-stu-id="4e543-131">If you run the Identity scaffolder without specifying the `--files` flag or the `--useDefaultUI` flag, all the available Identity UI pages will be created in your project.</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4e543-132">Uruchom szkielet tożsamości:</span><span class="sxs-lookup"><span data-stu-id="4e543-132">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4e543-133">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4e543-133">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4e543-134">W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy projekt > **Dodaj** > **nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="4e543-134">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="4e543-135">W lewym okienku okna dialogowego **Dodawanie szkieletu** wybierz pozycję Dodaj **tożsamość** > **Add**.</span><span class="sxs-lookup"><span data-stu-id="4e543-135">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="4e543-136">W oknie dialogowym **Dodawanie tożsamości** wybierz odpowiednie opcje.</span><span class="sxs-lookup"><span data-stu-id="4e543-136">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="4e543-137">Wybierz istniejącą stronę układu lub plik układu zostanie zastąpiony nieprawidłowym znacznikiem.</span><span class="sxs-lookup"><span data-stu-id="4e543-137">Select your existing layout page, or your layout file will be overwritten with incorrect markup.</span></span> <span data-ttu-id="4e543-138">Gdy zostanie wybrany istniejący plik \* \_ Layout. cshtml\* , **nie** zostanie on nadpisany.</span><span class="sxs-lookup"><span data-stu-id="4e543-138">When an existing *\_Layout.cshtml* file is selected, it is **not** overwritten.</span></span> <span data-ttu-id="4e543-139">Przykład:</span><span class="sxs-lookup"><span data-stu-id="4e543-139">For example:</span></span>
    * <span data-ttu-id="4e543-140">`~/Pages/Shared/_Layout.cshtml`dla Razor Pages</span><span class="sxs-lookup"><span data-stu-id="4e543-140">`~/Pages/Shared/_Layout.cshtml` for Razor Pages</span></span>
    * <span data-ttu-id="4e543-141">`~/Views/Shared/_Layout.cshtml`dla projektów MVC</span><span class="sxs-lookup"><span data-stu-id="4e543-141">`~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
* <span data-ttu-id="4e543-142">Aby użyć istniejącego kontekstu danych, wybierz co najmniej jeden plik do przesłonięcia.</span><span class="sxs-lookup"><span data-stu-id="4e543-142">To use your existing data context, select at least one file to override.</span></span> <span data-ttu-id="4e543-143">Musisz wybrać co najmniej jeden plik, aby dodać kontekst danych.</span><span class="sxs-lookup"><span data-stu-id="4e543-143">You must select at least one file to add your data context.</span></span>
  * <span data-ttu-id="4e543-144">Wybierz klasę kontekstu danych.</span><span class="sxs-lookup"><span data-stu-id="4e543-144">Select your data context class.</span></span>
  * <span data-ttu-id="4e543-145">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="4e543-145">Select **Add**.</span></span>
* <span data-ttu-id="4e543-146">Aby utworzyć nowy kontekst użytkownika i ewentualnie utworzyć niestandardową klasę użytkownika dla tożsamości:</span><span class="sxs-lookup"><span data-stu-id="4e543-146">To create a new user context and possibly create a custom user class for Identity:</span></span>
  * <span data-ttu-id="4e543-147">Wybierz **+** przycisk, aby utworzyć nową **klasę kontekstu danych**.</span><span class="sxs-lookup"><span data-stu-id="4e543-147">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="4e543-148">Zaakceptuj wartość domyślną lub określ klasę (na przykład `MyApplication.Data.ApplicationDbContext` ).</span><span class="sxs-lookup"><span data-stu-id="4e543-148">Accept the default value or specify a class (for example, `MyApplication.Data.ApplicationDbContext`).</span></span>
  * <span data-ttu-id="4e543-149">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="4e543-149">Select **Add**.</span></span>

<span data-ttu-id="4e543-150">Uwaga: w przypadku tworzenia nowego kontekstu użytkownika nie trzeba wybierać pliku do przesłonięcia.</span><span class="sxs-lookup"><span data-stu-id="4e543-150">Note: If you're creating a new user context, you don't have to select a file to override.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="4e543-151">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="4e543-151">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="4e543-152">Jeśli jeszcze nie zainstalowano szkieletu ASP.NET Core, zainstaluj go teraz:</span><span class="sxs-lookup"><span data-stu-id="4e543-152">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="4e543-153">Dodaj odwołanie do pakietu [Microsoft. VisualStudio. Web. CodeGeneration. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) do pliku projektu (*. csproj*).</span><span class="sxs-lookup"><span data-stu-id="4e543-153">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project file (*.csproj*).</span></span> <span data-ttu-id="4e543-154">Uruchom następujące polecenia w katalogu projektu:</span><span class="sxs-lookup"><span data-stu-id="4e543-154">Run the following commands in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="4e543-155">Uruchom następujące polecenie, aby wyświetlić listę opcji szkieletu tożsamości:</span><span class="sxs-lookup"><span data-stu-id="4e543-155">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="4e543-156">W folderze projektu uruchom program do tworzenia szkieletu tożsamości z żądanymi opcjami.</span><span class="sxs-lookup"><span data-stu-id="4e543-156">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="4e543-157">Na przykład, aby skonfigurować tożsamość przy użyciu domyślnego interfejsu użytkownika i minimalnej liczby plików, uruchom następujące polecenie.</span><span class="sxs-lookup"><span data-stu-id="4e543-157">For example, to setup identity with the default UI and the minimum number of files, run the following command.</span></span> <span data-ttu-id="4e543-158">Użyj prawidłowej w pełni kwalifikowanej nazwy dla kontekstu bazy danych:</span><span class="sxs-lookup"><span data-stu-id="4e543-158">Use the correct fully qualified name for your DB context:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

<span data-ttu-id="4e543-159">Program PowerShell używa średnika jako separatora poleceń.</span><span class="sxs-lookup"><span data-stu-id="4e543-159">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="4e543-160">W przypadku korzystania z programu PowerShell wpisz średniki na liście plików lub Umieść listę plików w podwójnych cudzysłowach.</span><span class="sxs-lookup"><span data-stu-id="4e543-160">When using PowerShell, escape the semi-colons in the file list or put the file list in double quotes.</span></span> <span data-ttu-id="4e543-161">Przykład:</span><span class="sxs-lookup"><span data-stu-id="4e543-161">For example:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="4e543-162">W przypadku uruchomienia szkieletu tożsamości bez określenia `--files` flagi lub `--useDefaultUI` flagi wszystkie dostępne strony interfejsu użytkownika tożsamości zostaną utworzone w projekcie.</span><span class="sxs-lookup"><span data-stu-id="4e543-162">If you run the Identity scaffolder without specifying the `--files` flag or the `--useDefaultUI` flag, all the available Identity UI pages will be created in your project.</span></span>

---

::: moniker-end
