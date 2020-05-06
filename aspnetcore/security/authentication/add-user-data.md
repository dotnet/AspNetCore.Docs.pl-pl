---
title: Dodawanie, pobieranie i usuwanie danych użytkownika do Identity w projekcie ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak dodać niestandardowe dane Identity użytkownika do projektu ASP.NET Core. Usuń dane na Rodo.
ms.author: riande
ms.date: 03/26/2020
ms.custom: mvc, seodec18
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/add-user-data
ms.openlocfilehash: 29c23e10d11eb1042b64fc071c221a9ead857fcc
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777335"
---
# <a name="add-download-and-delete-custom-user-data-to-identity-in-an-aspnet-core-project"></a><span data-ttu-id="ff18b-104">Dodawanie, pobieranie i usuwanie niestandardowych danych użytkownika do tożsamości w projekcie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ff18b-104">Add, download, and delete custom user data to Identity in an ASP.NET Core project</span></span>

<span data-ttu-id="ff18b-105">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ff18b-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="ff18b-106">W tym artykule pokazano, jak:</span><span class="sxs-lookup"><span data-stu-id="ff18b-106">This article shows how to:</span></span>

* <span data-ttu-id="ff18b-107">Dodawanie niestandardowych danych użytkownika do aplikacji sieci Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="ff18b-107">Add custom user data to an ASP.NET Core web app.</span></span>
* <span data-ttu-id="ff18b-108">Oznacz niestandardowy model danych użytkownika <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> atrybutami, aby był on automatycznie dostępny do pobrania i usunięcia.</span><span class="sxs-lookup"><span data-stu-id="ff18b-108">Mark the custom user data model with the <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> attribute so it's automatically available for download and deletion.</span></span> <span data-ttu-id="ff18b-109">Aby można było pobrać i usunąć dane, można spełnić wymagania [Rodo](xref:security/gdpr) .</span><span class="sxs-lookup"><span data-stu-id="ff18b-109">Making the data able to be downloaded and deleted helps meet [GDPR](xref:security/gdpr) requirements.</span></span>

<span data-ttu-id="ff18b-110">Przykład projektu jest tworzony na podstawie Razor Pages aplikacji sieci Web, ale instrukcje są podobne do ASP.NET Core aplikacji sieci Web MVC.</span><span class="sxs-lookup"><span data-stu-id="ff18b-110">The project sample is created from a Razor Pages web app, but the instructions are similar for a ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="ff18b-111">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ff18b-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ff18b-112">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="ff18b-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-razor-web-app"></a><span data-ttu-id="ff18b-113">Tworzenie aplikacji sieci Web Razor</span><span class="sxs-lookup"><span data-stu-id="ff18b-113">Create a Razor web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ff18b-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ff18b-114">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="ff18b-115">Z menu **plik** programu Visual Studio wybierz pozycję **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="ff18b-115">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="ff18b-116">Nadaj projektowi nazwę **WebApp1** , jeśli chcesz dopasować ją do przestrzeni nazw [przykładowego kodu pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) .</span><span class="sxs-lookup"><span data-stu-id="ff18b-116">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="ff18b-117">Wybierz pozycję > **ASP.NET Core aplikację sieci Web** **OK**</span><span class="sxs-lookup"><span data-stu-id="ff18b-117">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="ff18b-118">Na liście rozwijanej wybierz pozycję **ASP.NET Core 3,0**</span><span class="sxs-lookup"><span data-stu-id="ff18b-118">Select **ASP.NET Core 3.0** in the dropdown</span></span>
* <span data-ttu-id="ff18b-119">> Wybierz **aplikację sieci Web** **OK**</span><span class="sxs-lookup"><span data-stu-id="ff18b-119">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="ff18b-120">Skompiluj i Uruchom projekt.</span><span class="sxs-lookup"><span data-stu-id="ff18b-120">Build and run the project.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="ff18b-121">Z menu **plik** programu Visual Studio wybierz pozycję **Nowy** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="ff18b-121">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="ff18b-122">Nadaj projektowi nazwę **WebApp1** , jeśli chcesz dopasować ją do przestrzeni nazw [przykładowego kodu pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) .</span><span class="sxs-lookup"><span data-stu-id="ff18b-122">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="ff18b-123">Wybierz pozycję > **ASP.NET Core aplikację sieci Web** **OK**</span><span class="sxs-lookup"><span data-stu-id="ff18b-123">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="ff18b-124">Na liście rozwijanej wybierz pozycję **ASP.NET Core 2,2**</span><span class="sxs-lookup"><span data-stu-id="ff18b-124">Select **ASP.NET Core 2.2** in the dropdown</span></span>
* <span data-ttu-id="ff18b-125">> Wybierz **aplikację sieci Web** **OK**</span><span class="sxs-lookup"><span data-stu-id="ff18b-125">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="ff18b-126">Skompiluj i Uruchom projekt.</span><span class="sxs-lookup"><span data-stu-id="ff18b-126">Build and run the project.</span></span>

::: moniker-end


# <a name="net-core-cli"></a>[<span data-ttu-id="ff18b-127">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="ff18b-127">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-identity-scaffolder"></a><span data-ttu-id="ff18b-128">Uruchamianie szkieletu tożsamości</span><span class="sxs-lookup"><span data-stu-id="ff18b-128">Run the Identity scaffolder</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ff18b-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ff18b-129">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ff18b-130">W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy projekt > **Dodaj** > **nowy element szkieletowy**.</span><span class="sxs-lookup"><span data-stu-id="ff18b-130">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="ff18b-131">W lewym okienku okna dialogowego **Dodawanie szkieletu** wybierz pozycję Dodaj **tożsamość** > **Add**.</span><span class="sxs-lookup"><span data-stu-id="ff18b-131">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="ff18b-132">W oknie dialogowym **Dodawanie tożsamości** można wykonać następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="ff18b-132">In the **Add Identity** dialog, the following options:</span></span>
  * <span data-ttu-id="ff18b-133">Wybierz istniejący plik układu *~/Pages/Shared/_Layout. cshtml*</span><span class="sxs-lookup"><span data-stu-id="ff18b-133">Select the existing layout  file  *~/Pages/Shared/_Layout.cshtml*</span></span>
  * <span data-ttu-id="ff18b-134">Wybierz następujące pliki do przesłonięcia:</span><span class="sxs-lookup"><span data-stu-id="ff18b-134">Select the following files to override:</span></span>
    * <span data-ttu-id="ff18b-135">**Konto/rejestr**</span><span class="sxs-lookup"><span data-stu-id="ff18b-135">**Account/Register**</span></span>
    * <span data-ttu-id="ff18b-136">**Konto/Zarządzanie/indeks**</span><span class="sxs-lookup"><span data-stu-id="ff18b-136">**Account/Manage/Index**</span></span>
  * <span data-ttu-id="ff18b-137">Wybierz przycisk **+** , aby utworzyć nową **klasę kontekstu danych**.</span><span class="sxs-lookup"><span data-stu-id="ff18b-137">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="ff18b-138">Zaakceptuj typ (**WebApp1. models. WebApp1Context** , jeśli projekt jest nazwany **WebApp1**).</span><span class="sxs-lookup"><span data-stu-id="ff18b-138">Accept the type (**WebApp1.Models.WebApp1Context** if the project is named **WebApp1**).</span></span>
  * <span data-ttu-id="ff18b-139">Wybierz przycisk **+** , aby utworzyć nową **klasę użytkownika**.</span><span class="sxs-lookup"><span data-stu-id="ff18b-139">Select the **+** button to create a new **User class**.</span></span> <span data-ttu-id="ff18b-140">Zaakceptuj typ (**WebApp1User** , jeśli projekt ma nazwę **WebApp1**) > **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="ff18b-140">Accept the type (**WebApp1User** if the project is named **WebApp1**) > **Add**.</span></span>
* <span data-ttu-id="ff18b-141">Wybierz pozycję **Dodaj**.</span><span class="sxs-lookup"><span data-stu-id="ff18b-141">Select **Add**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="ff18b-142">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="ff18b-142">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="ff18b-143">Jeśli jeszcze nie zainstalowano szkieletu ASP.NET Core, zainstaluj go teraz:</span><span class="sxs-lookup"><span data-stu-id="ff18b-143">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="ff18b-144">Dodaj odwołanie do pakietu do [Microsoft. VisualStudio. Web. CodeGeneration. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) do pliku projektu (. csproj).</span><span class="sxs-lookup"><span data-stu-id="ff18b-144">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project (.csproj) file.</span></span> <span data-ttu-id="ff18b-145">Uruchom następujące polecenie w katalogu projektu:</span><span class="sxs-lookup"><span data-stu-id="ff18b-145">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="ff18b-146">Uruchom następujące polecenie, aby wyświetlić listę opcji szkieletu tożsamości:</span><span class="sxs-lookup"><span data-stu-id="ff18b-146">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="ff18b-147">W folderze projektu uruchom program Identity rusztowaer:</span><span class="sxs-lookup"><span data-stu-id="ff18b-147">In the project folder, run the Identity scaffolder:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

<span data-ttu-id="ff18b-148">Postępuj zgodnie z instrukcjami w sekcji [migracje, UseAuthentication i układ](xref:security/authentication/scaffold-identity#efm) , aby wykonać następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="ff18b-148">Follow the instruction in [Migrations, UseAuthentication, and layout](xref:security/authentication/scaffold-identity#efm) to perform the following steps:</span></span>

* <span data-ttu-id="ff18b-149">Utwórz migrację i zaktualizuj bazę danych.</span><span class="sxs-lookup"><span data-stu-id="ff18b-149">Create a migration and update the database.</span></span>
* <span data-ttu-id="ff18b-150">Add `UseAuthentication` to `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="ff18b-150">Add `UseAuthentication` to `Startup.Configure`.</span></span>
* <span data-ttu-id="ff18b-151">Dodaj `<partial name="_LoginPartial" />` do pliku układu.</span><span class="sxs-lookup"><span data-stu-id="ff18b-151">Add `<partial name="_LoginPartial" />` to the layout file.</span></span>
* <span data-ttu-id="ff18b-152">Przetestuj aplikację:</span><span class="sxs-lookup"><span data-stu-id="ff18b-152">Test the app:</span></span>
  * <span data-ttu-id="ff18b-153">Rejestrowanie użytkownika</span><span class="sxs-lookup"><span data-stu-id="ff18b-153">Register a user</span></span>
  * <span data-ttu-id="ff18b-154">Wybierz nową nazwę użytkownika (obok linku **wylogowania** ).</span><span class="sxs-lookup"><span data-stu-id="ff18b-154">Select the new user name (next to the **Logout** link).</span></span> <span data-ttu-id="ff18b-155">Może być konieczne rozszerzenie okna lub wybranie ikony paska nawigacyjnego, aby wyświetlić nazwę użytkownika i inne linki.</span><span class="sxs-lookup"><span data-stu-id="ff18b-155">You might need to expand the window or select the navigation bar icon to show the user name and other links.</span></span>
  * <span data-ttu-id="ff18b-156">Wybierz kartę **dane osobowe** .</span><span class="sxs-lookup"><span data-stu-id="ff18b-156">Select the **Personal Data** tab.</span></span>
  * <span data-ttu-id="ff18b-157">Wybierz przycisk **Pobierz** i zbadaj plik *personaldata. JSON* .</span><span class="sxs-lookup"><span data-stu-id="ff18b-157">Select the **Download** button and examined the *PersonalData.json* file.</span></span>
  * <span data-ttu-id="ff18b-158">Przetestuj przycisk **Usuń** , który spowoduje usunięcie zalogowanego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="ff18b-158">Test the **Delete** button, which deletes the logged on user.</span></span>

## <a name="add-custom-user-data-to-the-identity-db"></a><span data-ttu-id="ff18b-159">Dodawanie niestandardowych danych użytkownika do bazy danych tożsamości</span><span class="sxs-lookup"><span data-stu-id="ff18b-159">Add custom user data to the Identity DB</span></span>

<span data-ttu-id="ff18b-160">Zaktualizuj klasę `IdentityUser` pochodną o właściwościach niestandardowych.</span><span class="sxs-lookup"><span data-stu-id="ff18b-160">Update the `IdentityUser` derived class with custom properties.</span></span> <span data-ttu-id="ff18b-161">Jeśli nazwa projektu WebApp1, plik ma nazwę *obszary/tożsamość/Data/WebApp1User. cs*.</span><span class="sxs-lookup"><span data-stu-id="ff18b-161">If you named the project WebApp1, the file is named *Areas/Identity/Data/WebApp1User.cs*.</span></span> <span data-ttu-id="ff18b-162">Zaktualizuj plik przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="ff18b-162">Update the file with the following code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

<span data-ttu-id="ff18b-163">Właściwości z atrybutem [personaldata](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) są następujące:</span><span class="sxs-lookup"><span data-stu-id="ff18b-163">Properties with the [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) attribute are:</span></span>

* <span data-ttu-id="ff18b-164">Usunięte, gdy zostanie wywołana `UserManager.Delete`Strona Razor *obszary/tożsamość/strony/konto/Zarządzanie/DeletePersonalData. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="ff18b-164">Deleted when the *Areas/Identity/Pages/Account/Manage/DeletePersonalData.cshtml* Razor Page calls `UserManager.Delete`.</span></span>
* <span data-ttu-id="ff18b-165">Zawarte w pobranych danych przez stronę *obszary/tożsamość/strony/konto/Zarządzanie/DownloadPersonalData. cshtml* Razor.</span><span class="sxs-lookup"><span data-stu-id="ff18b-165">Included in the downloaded data by the *Areas/Identity/Pages/Account/Manage/DownloadPersonalData.cshtml* Razor Page.</span></span>

### <a name="update-the-accountmanageindexcshtml-page"></a><span data-ttu-id="ff18b-166">Aktualizowanie strony account/Manage/index. cshtml</span><span class="sxs-lookup"><span data-stu-id="ff18b-166">Update the Account/Manage/Index.cshtml page</span></span>

<span data-ttu-id="ff18b-167">Zaktualizuj elementy `InputModel` in *Areas/Identity/Pages/Account/Manage/index. cshtml. cs* przy użyciu następującego wyróżnionego kodu:</span><span class="sxs-lookup"><span data-stu-id="ff18b-167">Update the `InputModel` in *Areas/Identity/Pages/Account/Manage/Index.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

<span data-ttu-id="ff18b-168">Zaktualizuj *obszary/tożsamość/strony/konto/Zarządzanie/index. cshtml* przy użyciu następującego wyróżnionego znacznika:</span><span class="sxs-lookup"><span data-stu-id="ff18b-168">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

<span data-ttu-id="ff18b-169">Zaktualizuj *obszary/tożsamość/strony/konto/Zarządzanie/index. cshtml* przy użyciu następującego wyróżnionego znacznika:</span><span class="sxs-lookup"><span data-stu-id="ff18b-169">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a><span data-ttu-id="ff18b-170">Aktualizowanie strony account/register. cshtml</span><span class="sxs-lookup"><span data-stu-id="ff18b-170">Update the Account/Register.cshtml page</span></span>

<span data-ttu-id="ff18b-171">Zaktualizuj elementy `InputModel` in *Areas/Identity/Pages/Account/register. cshtml. cs* przy użyciu następującego wyróżnionego kodu:</span><span class="sxs-lookup"><span data-stu-id="ff18b-171">Update the `InputModel` in *Areas/Identity/Pages/Account/Register.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

<span data-ttu-id="ff18b-172">Zaktualizuj *obszary/tożsamość/strony/account/register. cshtml* przy użyciu następującego wyróżnionego znacznika:</span><span class="sxs-lookup"><span data-stu-id="ff18b-172">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

<span data-ttu-id="ff18b-173">Zaktualizuj *obszary/tożsamość/strony/account/register. cshtml* przy użyciu następującego wyróżnionego znacznika:</span><span class="sxs-lookup"><span data-stu-id="ff18b-173">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


<span data-ttu-id="ff18b-174">Skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="ff18b-174">Build the project.</span></span>

### <a name="add-a-migration-for-the-custom-user-data"></a><span data-ttu-id="ff18b-175">Dodawanie migracji niestandardowych danych użytkownika</span><span class="sxs-lookup"><span data-stu-id="ff18b-175">Add a migration for the custom user data</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ff18b-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ff18b-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ff18b-177">W **konsoli Menedżera pakietów**programu Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="ff18b-177">In the Visual Studio **Package Manager Console**:</span></span>

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="ff18b-178">Interfejs wiersza polecenia platformy .NET Core</span><span class="sxs-lookup"><span data-stu-id="ff18b-178">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a><span data-ttu-id="ff18b-179">Testowanie tworzenia, wyświetlania, pobierania, usuwania niestandardowych danych użytkownika</span><span class="sxs-lookup"><span data-stu-id="ff18b-179">Test create, view, download, delete custom user data</span></span>

<span data-ttu-id="ff18b-180">Przetestuj aplikację:</span><span class="sxs-lookup"><span data-stu-id="ff18b-180">Test the app:</span></span>

* <span data-ttu-id="ff18b-181">Zarejestruj nowego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="ff18b-181">Register a new user.</span></span>
* <span data-ttu-id="ff18b-182">Wyświetlanie niestandardowych danych użytkownika na `/Identity/Account/Manage` stronie.</span><span class="sxs-lookup"><span data-stu-id="ff18b-182">View the custom user data on the `/Identity/Account/Manage` page.</span></span>
* <span data-ttu-id="ff18b-183">Pobierz i Przejrzyj dane osobowe użytkowników ze `/Identity/Account/Manage/PersonalData` strony.</span><span class="sxs-lookup"><span data-stu-id="ff18b-183">Download and view the users personal data from the `/Identity/Account/Manage/PersonalData` page.</span></span>

## <a name="add-claims-to-identity-using-iuserclaimsprincipalfactoryapplicationuser"></a><span data-ttu-id="ff18b-184">Dodawanie oświadczeń do Identity korzystania z IUserClaimsPrincipalFactory<ApplicationUser></span><span class="sxs-lookup"><span data-stu-id="ff18b-184">Add claims to Identity using IUserClaimsPrincipalFactory<ApplicationUser></span></span>

<span data-ttu-id="ff18b-185">Dodatkowe oświadczenia można dodać do ASP.NET Core Identity za pomocą `IUserClaimsPrincipalFactory<T>` interfejsu.</span><span class="sxs-lookup"><span data-stu-id="ff18b-185">Additional claims can be added to ASP.NET Core Identity by using the `IUserClaimsPrincipalFactory<T>` interface.</span></span> <span data-ttu-id="ff18b-186">Tę klasę można dodać do aplikacji w `Startup.ConfigureServices` metodzie.</span><span class="sxs-lookup"><span data-stu-id="ff18b-186">This class can be added to the app in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="ff18b-187">Dodaj niestandardową implementację klasy w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="ff18b-187">Add the custom implementation of the class as follows:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddScoped<IUserClaimsPrincipalFactory<ApplicationUser>, 
        AdditionalUserClaimsPrincipalFactory>();
```

<span data-ttu-id="ff18b-188">Kod demonstracyjny używa `ApplicationUser` klasy.</span><span class="sxs-lookup"><span data-stu-id="ff18b-188">The demo code uses the `ApplicationUser` class.</span></span> <span data-ttu-id="ff18b-189">Ta klasa dodaje `IsAdmin` właściwość, która jest używana do dodawania dodatkowego żądania.</span><span class="sxs-lookup"><span data-stu-id="ff18b-189">This class adds an `IsAdmin` property which is used to add the additional claim.</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public bool IsAdmin { get; set; }
}
```

<span data-ttu-id="ff18b-190">`AdditionalUserClaimsPrincipalFactory` Implementuje `UserClaimsPrincipalFactory` interfejs.</span><span class="sxs-lookup"><span data-stu-id="ff18b-190">The `AdditionalUserClaimsPrincipalFactory` implements the `UserClaimsPrincipalFactory` interface.</span></span> <span data-ttu-id="ff18b-191">Do `ClaimsPrincipal`. zostanie dodane nowe zastrzeżenie roli.</span><span class="sxs-lookup"><span data-stu-id="ff18b-191">A new role claim is added to the `ClaimsPrincipal`.</span></span>

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

<span data-ttu-id="ff18b-192">Dodatkowe zgłoszenie można następnie użyć w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="ff18b-192">The additional claim can then be used in the app.</span></span> <span data-ttu-id="ff18b-193">Na Razor stronie `IAuthorizationService` wystąpienie może być używane w celu uzyskania dostępu do wartości żądania.</span><span class="sxs-lookup"><span data-stu-id="ff18b-193">In a Razor Page, the `IAuthorizationService` instance can be used to access the claim value.</span></span>

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
