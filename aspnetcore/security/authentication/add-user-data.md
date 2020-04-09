---
title: Dodawanie, pobieranie i usuwanie danych użytkownika do tożsamości w projekcie ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak dodać niestandardowe dane użytkownika do tożsamości w projekcie ASP.NET Core. Usuń dane zgodnie z RODO.
ms.author: riande
ms.date: 03/26/2020
ms.custom: mvc, seodec18
uid: security/authentication/add-user-data
ms.openlocfilehash: 76b83df22381429feab80056c36dbdac1e5f20c7
ms.sourcegitcommit: 1d8f1396ccc66a0c3fcb5e5f36ea29b50db6d92a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80501227"
---
# <a name="add-download-and-delete-custom-user-data-to-identity-in-an-aspnet-core-project"></a><span data-ttu-id="a6122-104">Dodawanie, pobieranie i usuwanie niestandardowych danych użytkownika do tożsamości w projekcie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a6122-104">Add, download, and delete custom user data to Identity in an ASP.NET Core project</span></span>

<span data-ttu-id="a6122-105">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a6122-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="a6122-106">W tym artykule pokazano, jak:</span><span class="sxs-lookup"><span data-stu-id="a6122-106">This article shows how to:</span></span>

* <span data-ttu-id="a6122-107">Dodawanie niestandardowych danych użytkownika do aplikacji sieci web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a6122-107">Add custom user data to an ASP.NET Core web app.</span></span>
* <span data-ttu-id="a6122-108">Oznacz niestandardowy model danych <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> użytkownika za pomocą atrybutu, aby był automatycznie dostępny do pobrania i usunięcia.</span><span class="sxs-lookup"><span data-stu-id="a6122-108">Mark the custom user data model with the <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> attribute so it's automatically available for download and deletion.</span></span> <span data-ttu-id="a6122-109">Możliwość pobierania i usuwania danych pomaga spełnić wymagania [RODO.](xref:security/gdpr)</span><span class="sxs-lookup"><span data-stu-id="a6122-109">Making the data able to be downloaded and deleted helps meet [GDPR](xref:security/gdpr) requirements.</span></span>

<span data-ttu-id="a6122-110">Przykład projektu jest tworzony z aplikacji sieci Web Razor Pages, ale instrukcje są podobne dla aplikacji sieci web ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="a6122-110">The project sample is created from a Razor Pages web app, but the instructions are similar for a ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="a6122-111">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a6122-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a6122-112">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="a6122-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-razor-web-app"></a><span data-ttu-id="a6122-113">Tworzenie aplikacji internetowej Razor</span><span class="sxs-lookup"><span data-stu-id="a6122-113">Create a Razor web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a6122-114">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a6122-114">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="a6122-115">Z menu **Plik** programu Visual Studio wybierz polecenie **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="a6122-115">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="a6122-116">Nazwij projekt **WebApp1,** jeśli chcesz, aby był zgodny z obszarem nazw przykładowego kodu [pobierania.](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data)</span><span class="sxs-lookup"><span data-stu-id="a6122-116">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="a6122-117">Wybierz **ASP.NET Core Web Application** > **OK**</span><span class="sxs-lookup"><span data-stu-id="a6122-117">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="a6122-118">Wybierz **ASP.NET Core 3.0** w rozwijanej</span><span class="sxs-lookup"><span data-stu-id="a6122-118">Select **ASP.NET Core 3.0** in the dropdown</span></span>
* <span data-ttu-id="a6122-119">Wybierz **aplikację** > sieci Web **OK**</span><span class="sxs-lookup"><span data-stu-id="a6122-119">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="a6122-120">Tworzenie i uruchamianie projektu.</span><span class="sxs-lookup"><span data-stu-id="a6122-120">Build and run the project.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="a6122-121">Z menu **Plik** programu Visual Studio wybierz polecenie **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="a6122-121">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="a6122-122">Nazwij projekt **WebApp1,** jeśli chcesz, aby był zgodny z obszarem nazw przykładowego kodu [pobierania.](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data)</span><span class="sxs-lookup"><span data-stu-id="a6122-122">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="a6122-123">Wybierz **ASP.NET Core Web Application** > **OK**</span><span class="sxs-lookup"><span data-stu-id="a6122-123">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="a6122-124">Wybierz **ASP.NET Core 2.2** w rozwijanej</span><span class="sxs-lookup"><span data-stu-id="a6122-124">Select **ASP.NET Core 2.2** in the dropdown</span></span>
* <span data-ttu-id="a6122-125">Wybierz **aplikację** > sieci Web **OK**</span><span class="sxs-lookup"><span data-stu-id="a6122-125">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="a6122-126">Tworzenie i uruchamianie projektu.</span><span class="sxs-lookup"><span data-stu-id="a6122-126">Build and run the project.</span></span>

::: moniker-end


# <a name="net-core-cli"></a>[<span data-ttu-id="a6122-127">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="a6122-127">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-identity-scaffolder"></a><span data-ttu-id="a6122-128">Uruchamianie szkieletu tożsamości</span><span class="sxs-lookup"><span data-stu-id="a6122-128">Run the Identity scaffolder</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a6122-129">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a6122-129">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a6122-130">Z **Eksploratora rozwiązań**kliknij prawym przyciskiem myszy projekt > **Dodaj** > **nowy element rusztowania**.</span><span class="sxs-lookup"><span data-stu-id="a6122-130">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="a6122-131">Z lewego okienka okna dialogowego **Dodawanie szkieletu** wybierz pozycję**Dodaj** **tożsamość** > .</span><span class="sxs-lookup"><span data-stu-id="a6122-131">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="a6122-132">W oknie dialogowym **Dodawanie tożsamości** są dostępne następujące opcje:</span><span class="sxs-lookup"><span data-stu-id="a6122-132">In the **Add Identity** dialog, the following options:</span></span>
  * <span data-ttu-id="a6122-133">Wybierz istniejący plik układu *~/Pages/Shared/_Layout.cshtml*</span><span class="sxs-lookup"><span data-stu-id="a6122-133">Select the existing layout  file  *~/Pages/Shared/_Layout.cshtml*</span></span>
  * <span data-ttu-id="a6122-134">Wybierz następujące pliki do zastąpienia:</span><span class="sxs-lookup"><span data-stu-id="a6122-134">Select the following files to override:</span></span>
    * <span data-ttu-id="a6122-135">**Konto/zarejestruj się**</span><span class="sxs-lookup"><span data-stu-id="a6122-135">**Account/Register**</span></span>
    * <span data-ttu-id="a6122-136">**Konto/Zarządzanie/Indeks**</span><span class="sxs-lookup"><span data-stu-id="a6122-136">**Account/Manage/Index**</span></span>
  * <span data-ttu-id="a6122-137">Wybierz **+** przycisk, aby utworzyć nową **klasę kontekstu danych**.</span><span class="sxs-lookup"><span data-stu-id="a6122-137">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="a6122-138">Zaakceptuj typ **(WebApp1.Models.WebApp1Context,** jeśli projekt nosi nazwę **WebApp1**).</span><span class="sxs-lookup"><span data-stu-id="a6122-138">Accept the type (**WebApp1.Models.WebApp1Context** if the project is named **WebApp1**).</span></span>
  * <span data-ttu-id="a6122-139">Wybierz **+** przycisk, aby utworzyć nową **klasę użytkownika**.</span><span class="sxs-lookup"><span data-stu-id="a6122-139">Select the **+** button to create a new **User class**.</span></span> <span data-ttu-id="a6122-140">Zaakceptuj typ **(WebApp1Użytnik,** jeśli projekt nosi nazwę **WebApp1)**> **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="a6122-140">Accept the type (**WebApp1User** if the project is named **WebApp1**) > **Add**.</span></span>
* <span data-ttu-id="a6122-141">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="a6122-141">Select **Add**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="a6122-142">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="a6122-142">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="a6122-143">Jeśli nie zainstalowano wcześniej ASP.NET rdzenia, zainstaluj go teraz:</span><span class="sxs-lookup"><span data-stu-id="a6122-143">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="a6122-144">Dodaj odwołanie do pakietu do pliku [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) do pliku projektu (csproj).</span><span class="sxs-lookup"><span data-stu-id="a6122-144">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project (.csproj) file.</span></span> <span data-ttu-id="a6122-145">Uruchom następujące polecenie w katalogu projektu:</span><span class="sxs-lookup"><span data-stu-id="a6122-145">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="a6122-146">Uruchom następujące polecenie, aby wyświetlić listę opcji szkieletu tożsamości:</span><span class="sxs-lookup"><span data-stu-id="a6122-146">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="a6122-147">W folderze projektu uruchom rusztowanie tożsamości:</span><span class="sxs-lookup"><span data-stu-id="a6122-147">In the project folder, run the Identity scaffolder:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

<span data-ttu-id="a6122-148">Postępuj zgodnie z instrukcjami w [migracje, UseAuthentication i układ,](xref:security/authentication/scaffold-identity#efm) aby wykonać następujące kroki:</span><span class="sxs-lookup"><span data-stu-id="a6122-148">Follow the instruction in [Migrations, UseAuthentication, and layout](xref:security/authentication/scaffold-identity#efm) to perform the following steps:</span></span>

* <span data-ttu-id="a6122-149">Utwórz migrację i zaktualizuj bazę danych.</span><span class="sxs-lookup"><span data-stu-id="a6122-149">Create a migration and update the database.</span></span>
* <span data-ttu-id="a6122-150">Add `UseAuthentication` to `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="a6122-150">Add `UseAuthentication` to `Startup.Configure`.</span></span>
* <span data-ttu-id="a6122-151">Dodaj `<partial name="_LoginPartial" />` do pliku układu.</span><span class="sxs-lookup"><span data-stu-id="a6122-151">Add `<partial name="_LoginPartial" />` to the layout file.</span></span>
* <span data-ttu-id="a6122-152">Przetestuj aplikację:</span><span class="sxs-lookup"><span data-stu-id="a6122-152">Test the app:</span></span>
  * <span data-ttu-id="a6122-153">Zarejestruj użytkownika</span><span class="sxs-lookup"><span data-stu-id="a6122-153">Register a user</span></span>
  * <span data-ttu-id="a6122-154">Wybierz nową nazwę użytkownika (obok łącza **Wyloguj).**</span><span class="sxs-lookup"><span data-stu-id="a6122-154">Select the new user name (next to the **Logout** link).</span></span> <span data-ttu-id="a6122-155">Może być konieczne rozwinięcie okna lub wybranie ikony paska nawigacyjnego, aby wyświetlić nazwę użytkownika i inne łącza.</span><span class="sxs-lookup"><span data-stu-id="a6122-155">You might need to expand the window or select the navigation bar icon to show the user name and other links.</span></span>
  * <span data-ttu-id="a6122-156">Wybierz kartę **Dane osobowe.**</span><span class="sxs-lookup"><span data-stu-id="a6122-156">Select the **Personal Data** tab.</span></span>
  * <span data-ttu-id="a6122-157">Wybierz przycisk **Pobierz** i przeanalizuj plik *PersonalData.json.*</span><span class="sxs-lookup"><span data-stu-id="a6122-157">Select the **Download** button and examined the *PersonalData.json* file.</span></span>
  * <span data-ttu-id="a6122-158">Przetestuj przycisk **Usuń,** który usuwa zalogowanego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="a6122-158">Test the **Delete** button, which deletes the logged on user.</span></span>

## <a name="add-custom-user-data-to-the-identity-db"></a><span data-ttu-id="a6122-159">Dodawanie niestandardowych danych użytkownika do bazy danych tożsamości</span><span class="sxs-lookup"><span data-stu-id="a6122-159">Add custom user data to the Identity DB</span></span>

<span data-ttu-id="a6122-160">Zaktualizuj klasę pochodną `IdentityUser` za pomocą właściwości niestandardowych.</span><span class="sxs-lookup"><span data-stu-id="a6122-160">Update the `IdentityUser` derived class with custom properties.</span></span> <span data-ttu-id="a6122-161">Jeśli projekt został nazwany WebApp1, plik nosi nazwę *Obszary/Tożsamość/Dane/WebApp1User.cs*.</span><span class="sxs-lookup"><span data-stu-id="a6122-161">If you named the project WebApp1, the file is named *Areas/Identity/Data/WebApp1User.cs*.</span></span> <span data-ttu-id="a6122-162">Zaktualizuj plik za pomocą następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="a6122-162">Update the file with the following code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

<span data-ttu-id="a6122-163">Właściwości z atrybutem [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) to:</span><span class="sxs-lookup"><span data-stu-id="a6122-163">Properties with the [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) attribute are:</span></span>

* <span data-ttu-id="a6122-164">Usunięte po wywołaniu *strony Obszary/Tożsamość/Strony/Konto/Zarządzaj/UsuńPersonaldata.cshtml* Razor Page `UserManager.Delete`.</span><span class="sxs-lookup"><span data-stu-id="a6122-164">Deleted when the *Areas/Identity/Pages/Account/Manage/DeletePersonalData.cshtml* Razor Page calls `UserManager.Delete`.</span></span>
* <span data-ttu-id="a6122-165">Uwzględnione w pobranych danych przez *obszary / tożsamość / strony / konto / Manage / DownloadPersonalData.cshtml* Razor Page.</span><span class="sxs-lookup"><span data-stu-id="a6122-165">Included in the downloaded data by the *Areas/Identity/Pages/Account/Manage/DownloadPersonalData.cshtml* Razor Page.</span></span>

### <a name="update-the-accountmanageindexcshtml-page"></a><span data-ttu-id="a6122-166">Aktualizowanie strony Konto/Zarządzanie/Indeks.cshtml</span><span class="sxs-lookup"><span data-stu-id="a6122-166">Update the Account/Manage/Index.cshtml page</span></span>

<span data-ttu-id="a6122-167">Zaktualizuj `InputModel` w *obszarze/tożsamości/pages/konto/zarządzaj/index.cshtml.cs* z podświetlonym kodem:</span><span class="sxs-lookup"><span data-stu-id="a6122-167">Update the `InputModel` in *Areas/Identity/Pages/Account/Manage/Index.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

<span data-ttu-id="a6122-168">Zaktualizuj *obszary/tożsamość/strony/konto/zarządzanie/indeks.cshtml* za pomocą następujących wyróżnionych znaczników:</span><span class="sxs-lookup"><span data-stu-id="a6122-168">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

<span data-ttu-id="a6122-169">Zaktualizuj *obszary/tożsamość/strony/konto/zarządzanie/indeks.cshtml* za pomocą następujących wyróżnionych znaczników:</span><span class="sxs-lookup"><span data-stu-id="a6122-169">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a><span data-ttu-id="a6122-170">Aktualizowanie strony Konto/Register.cshtml</span><span class="sxs-lookup"><span data-stu-id="a6122-170">Update the Account/Register.cshtml page</span></span>

<span data-ttu-id="a6122-171">Zaktualizuj `InputModel` w *obszarze/tożsamości/pages/konto/Register.cshtml.cs* z następującym wyróżnionym kodem:</span><span class="sxs-lookup"><span data-stu-id="a6122-171">Update the `InputModel` in *Areas/Identity/Pages/Account/Register.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

<span data-ttu-id="a6122-172">Zaktualizuj *obszary/tożsamość/strony/konto/register.cshtml* za pomocą następujących wyróżnionych znaczników:</span><span class="sxs-lookup"><span data-stu-id="a6122-172">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

<span data-ttu-id="a6122-173">Zaktualizuj *obszary/tożsamość/strony/konto/register.cshtml* za pomocą następujących wyróżnionych znaczników:</span><span class="sxs-lookup"><span data-stu-id="a6122-173">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


<span data-ttu-id="a6122-174">Skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="a6122-174">Build the project.</span></span>

### <a name="add-a-migration-for-the-custom-user-data"></a><span data-ttu-id="a6122-175">Dodawanie migracji dla niestandardowych danych użytkownika</span><span class="sxs-lookup"><span data-stu-id="a6122-175">Add a migration for the custom user data</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a6122-176">Program Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a6122-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a6122-177">W konsoli **Menedżera pakietów**programu Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="a6122-177">In the Visual Studio **Package Manager Console**:</span></span>

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="a6122-178">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="a6122-178">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a><span data-ttu-id="a6122-179">Testowanie tworzenia, przeglądania, pobierania, usuwania niestandardowych danych użytkownika</span><span class="sxs-lookup"><span data-stu-id="a6122-179">Test create, view, download, delete custom user data</span></span>

<span data-ttu-id="a6122-180">Przetestuj aplikację:</span><span class="sxs-lookup"><span data-stu-id="a6122-180">Test the app:</span></span>

* <span data-ttu-id="a6122-181">Zarejestruj nowego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="a6122-181">Register a new user.</span></span>
* <span data-ttu-id="a6122-182">Wyświetl niestandardowe dane `/Identity/Account/Manage` użytkownika na stronie.</span><span class="sxs-lookup"><span data-stu-id="a6122-182">View the custom user data on the `/Identity/Account/Manage` page.</span></span>
* <span data-ttu-id="a6122-183">Pobierz i wyświetl dane osobowe `/Identity/Account/Manage/PersonalData` użytkowników ze strony.</span><span class="sxs-lookup"><span data-stu-id="a6122-183">Download and view the users personal data from the `/Identity/Account/Manage/PersonalData` page.</span></span>

## <a name="add-claims-to-identity-using-iuserclaimsprincipalfactoryapplicationuser"></a><span data-ttu-id="a6122-184">Dodaj oświadczenia do tożsamości przy użyciu IUserClaimsPrincipalFactory<ApplicationUser></span><span class="sxs-lookup"><span data-stu-id="a6122-184">Add claims to Identity using IUserClaimsPrincipalFactory<ApplicationUser></span></span>

<span data-ttu-id="a6122-185">Dodatkowe oświadczenia można dodać do ASP.NET tożsamości podstawowej `IUserClaimsPrincipalFactory<T>` przy użyciu interfejsu.</span><span class="sxs-lookup"><span data-stu-id="a6122-185">Additional claims can be added to ASP.NET Core Identity by using the `IUserClaimsPrincipalFactory<T>` interface.</span></span> <span data-ttu-id="a6122-186">Ta klasa może zostać dodana `Startup.ConfigureServices` do aplikacji w metodzie.</span><span class="sxs-lookup"><span data-stu-id="a6122-186">This class can be added to the app in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="a6122-187">Dodaj niestandardową implementację klasy w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="a6122-187">Add the custom implementation of the class as follows:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddScoped<IUserClaimsPrincipalFactory<ApplicationUser>, 
        AdditionalUserClaimsPrincipalFactory>();
```

<span data-ttu-id="a6122-188">Kod demonstracyjny `ApplicationUser` używa klasy.</span><span class="sxs-lookup"><span data-stu-id="a6122-188">The demo code uses the `ApplicationUser` class.</span></span> <span data-ttu-id="a6122-189">Ta klasa `IsAdmin` dodaje właściwość, która jest używana do dodawania dodatkowego oświadczenia.</span><span class="sxs-lookup"><span data-stu-id="a6122-189">This class adds an `IsAdmin` property which is used to add the additional claim.</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public bool IsAdmin { get; set; }
}
```

<span data-ttu-id="a6122-190">Implementuje `AdditionalUserClaimsPrincipalFactory` `UserClaimsPrincipalFactory` interfejs.</span><span class="sxs-lookup"><span data-stu-id="a6122-190">The `AdditionalUserClaimsPrincipalFactory` implements the `UserClaimsPrincipalFactory` interface.</span></span> <span data-ttu-id="a6122-191">Nowe oświadczenie roli jest `ClaimsPrincipal`dodawane do .</span><span class="sxs-lookup"><span data-stu-id="a6122-191">A new role claim is added to the `ClaimsPrincipal`.</span></span>

```csharp
public class AdditionalUserClaimsPrincipalFactory 
        : UserClaimsPrincipalFactory<ApplicationUser, IdentityRole>
{
    public AdditionalUserClaimsPrincipalFactory( 
        UserManager<ApplicationUser> userManager,
        RoleManager<IdentityRole> roleManager, 
        IOptions<IdentityOptions> optionsAccessor) 
        : base(userManager, roleManager, optionsAccessor)
    {}

    public async override Task<ClaimsPrincipal> CreateAsync(ApplicationUser user)
    {
        var principal = await base.CreateAsync(user);
        var identity = (ClaimsIdentity)principal.Identity;

        var claims = new List<Claim>();
        if (user.IsAdmin)
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "admin"));
        }
        else
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "user"));
        }

        identity.AddClaims(claims);
        return principal;
    }
}
```

<span data-ttu-id="a6122-192">Dodatkowe oświadczenie można następnie użyć w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="a6122-192">The additional claim can then be used in the app.</span></span> <span data-ttu-id="a6122-193">Na stronie Razor `IAuthorizationService` wystąpienie może służyć do uzyskania dostępu do wartości oświadczenia.</span><span class="sxs-lookup"><span data-stu-id="a6122-193">In a Razor Page, the `IAuthorizationService` instance can be used to access the claim value.</span></span>

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

@if ((await AuthorizationService.AuthorizeAsync(User, "IsAdmin")).Succeeded)
{
    <ul class="mr-auto navbar-nav">
        <li class="nav-item">
            <a class="nav-link" asp-controller="Admin" asp-action="Index">ADMIN</a>
        </li>
    </ul>
}
```
