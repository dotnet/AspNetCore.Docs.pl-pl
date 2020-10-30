---
title: 'Dodawanie, pobieranie i usuwanie danych użytkownika do :::no-loc(Identity)::: w projekcie ASP.NET Core'
author: rick-anderson
description: 'Dowiedz się, jak dodać niestandardowe dane użytkownika do :::no-loc(Identity)::: projektu ASP.NET Core. Usuń dane na Rodo.'
ms.author: riande
ms.date: 03/26/2020
ms.custom: mvc, seodec18
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/authentication/add-user-data
ms.openlocfilehash: a4e1fd780947cfa5f09fb1e03964595fa09f0f18
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061421"
---
# <a name="add-download-and-delete-custom-user-data-to-no-locidentity-in-an-aspnet-core-project"></a><span data-ttu-id="80acf-104">Dodawanie, pobieranie i usuwanie niestandardowych danych użytkownika do programu :::no-loc(Identity)::: w projekcie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="80acf-104">Add, download, and delete custom user data to :::no-loc(Identity)::: in an ASP.NET Core project</span></span>

<span data-ttu-id="80acf-105">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="80acf-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="80acf-106">W tym artykule pokazano, jak:</span><span class="sxs-lookup"><span data-stu-id="80acf-106">This article shows how to:</span></span>

* <span data-ttu-id="80acf-107">Dodawanie niestandardowych danych użytkownika do aplikacji sieci Web ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="80acf-107">Add custom user data to an ASP.NET Core web app.</span></span>
* <span data-ttu-id="80acf-108">Oznacz niestandardowy model danych użytkownika <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PersonalDataAttribute> atrybutami, aby był on automatycznie dostępny do pobrania i usunięcia.</span><span class="sxs-lookup"><span data-stu-id="80acf-108">Mark the custom user data model with the <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PersonalDataAttribute> attribute so it's automatically available for download and deletion.</span></span> <span data-ttu-id="80acf-109">Aby można było pobrać i usunąć dane, można spełnić wymagania [Rodo](xref:security/gdpr) .</span><span class="sxs-lookup"><span data-stu-id="80acf-109">Making the data able to be downloaded and deleted helps meet [GDPR](xref:security/gdpr) requirements.</span></span>

<span data-ttu-id="80acf-110">Przykład projektu jest tworzony na podstawie :::no-loc(Razor)::: strony aplikacji sieci Web, ale instrukcje są podobne dla aplikacji sieci web ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="80acf-110">The project sample is created from a :::no-loc(Razor)::: Pages web app, but the instructions are similar for a ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="80acf-111">[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([jak pobrać](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="80acf-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="80acf-112">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="80acf-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-no-locrazor-web-app"></a><span data-ttu-id="80acf-113">Tworzenie :::no-loc(Razor)::: aplikacji sieci Web</span><span class="sxs-lookup"><span data-stu-id="80acf-113">Create a :::no-loc(Razor)::: web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="80acf-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="80acf-114">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="80acf-115">Z menu **plik** programu Visual Studio wybierz pozycję **Nowy**  >  **projekt** .</span><span class="sxs-lookup"><span data-stu-id="80acf-115">From the Visual Studio **File** menu, select **New** > **Project** .</span></span> <span data-ttu-id="80acf-116">Nadaj projektowi nazwę **WebApp1** , jeśli chcesz dopasować ją do przestrzeni nazw [przykładowego kodu pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) .</span><span class="sxs-lookup"><span data-stu-id="80acf-116">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="80acf-117">Wybierz pozycję **ASP.NET Core aplikację sieci Web** > **OK**</span><span class="sxs-lookup"><span data-stu-id="80acf-117">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="80acf-118">Na liście rozwijanej wybierz pozycję **ASP.NET Core 3,0**</span><span class="sxs-lookup"><span data-stu-id="80acf-118">Select **ASP.NET Core 3.0** in the dropdown</span></span>
* <span data-ttu-id="80acf-119">Wybierz **aplikację sieci Web** > **OK**</span><span class="sxs-lookup"><span data-stu-id="80acf-119">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="80acf-120">Skompiluj i Uruchom projekt.</span><span class="sxs-lookup"><span data-stu-id="80acf-120">Build and run the project.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="80acf-121">Z menu **plik** programu Visual Studio wybierz pozycję **Nowy**  >  **projekt** .</span><span class="sxs-lookup"><span data-stu-id="80acf-121">From the Visual Studio **File** menu, select **New** > **Project** .</span></span> <span data-ttu-id="80acf-122">Nadaj projektowi nazwę **WebApp1** , jeśli chcesz dopasować ją do przestrzeni nazw [przykładowego kodu pobierania](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) .</span><span class="sxs-lookup"><span data-stu-id="80acf-122">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="80acf-123">Wybierz pozycję **ASP.NET Core aplikację sieci Web** > **OK**</span><span class="sxs-lookup"><span data-stu-id="80acf-123">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="80acf-124">Na liście rozwijanej wybierz pozycję **ASP.NET Core 2,2**</span><span class="sxs-lookup"><span data-stu-id="80acf-124">Select **ASP.NET Core 2.2** in the dropdown</span></span>
* <span data-ttu-id="80acf-125">Wybierz **aplikację sieci Web** > **OK**</span><span class="sxs-lookup"><span data-stu-id="80acf-125">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="80acf-126">Skompiluj i Uruchom projekt.</span><span class="sxs-lookup"><span data-stu-id="80acf-126">Build and run the project.</span></span>

::: moniker-end


# <a name="net-core-cli"></a>[<span data-ttu-id="80acf-127">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="80acf-127">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-no-locidentity-scaffolder"></a><span data-ttu-id="80acf-128">Uruchamianie :::no-loc(Identity)::: szkieletu</span><span class="sxs-lookup"><span data-stu-id="80acf-128">Run the :::no-loc(Identity)::: scaffolder</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="80acf-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="80acf-129">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="80acf-130">W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt > **Dodaj**  >  **nowy element szkieletowy** .</span><span class="sxs-lookup"><span data-stu-id="80acf-130">From **Solution Explorer** , right-click on the project > **Add** > **New Scaffolded Item** .</span></span>
* <span data-ttu-id="80acf-131">W lewym okienku okna dialogowego **Dodawanie szkieletu** wybierz pozycję **:::no-loc(Identity):::**  >  **Dodaj** .</span><span class="sxs-lookup"><span data-stu-id="80acf-131">From the left pane of the **Add Scaffold** dialog, select **:::no-loc(Identity):::** > **Add** .</span></span>
* <span data-ttu-id="80acf-132">W oknie **dialogowym :::no-loc(Identity)::: Dodawanie** następujące opcje:</span><span class="sxs-lookup"><span data-stu-id="80acf-132">In the **Add :::no-loc(Identity):::** dialog, the following options:</span></span>
  * <span data-ttu-id="80acf-133">Wybierz istniejący plik układu  *~/Pages/Shared/_Layout. cshtml*</span><span class="sxs-lookup"><span data-stu-id="80acf-133">Select the existing layout  file  *~/Pages/Shared/_Layout.cshtml*</span></span>
  * <span data-ttu-id="80acf-134">Wybierz następujące pliki do przesłonięcia:</span><span class="sxs-lookup"><span data-stu-id="80acf-134">Select the following files to override:</span></span>
    * <span data-ttu-id="80acf-135">**Konto/rejestr**</span><span class="sxs-lookup"><span data-stu-id="80acf-135">**Account/Register**</span></span>
    * <span data-ttu-id="80acf-136">**Konto/Zarządzanie/indeks**</span><span class="sxs-lookup"><span data-stu-id="80acf-136">**Account/Manage/Index**</span></span>
  * <span data-ttu-id="80acf-137">Wybierz **+** przycisk, aby utworzyć nową **klasę kontekstu danych** .</span><span class="sxs-lookup"><span data-stu-id="80acf-137">Select the **+** button to create a new **Data context class** .</span></span> <span data-ttu-id="80acf-138">Zaakceptuj typ ( **WebApp1. models. WebApp1Context** , jeśli projekt jest nazwany **WebApp1** ).</span><span class="sxs-lookup"><span data-stu-id="80acf-138">Accept the type ( **WebApp1.Models.WebApp1Context** if the project is named **WebApp1** ).</span></span>
  * <span data-ttu-id="80acf-139">Wybierz **+** przycisk, aby utworzyć nową **klasę użytkownika** .</span><span class="sxs-lookup"><span data-stu-id="80acf-139">Select the **+** button to create a new **User class** .</span></span> <span data-ttu-id="80acf-140">Zaakceptuj typ ( **WebApp1User** , jeśli projekt ma nazwę **WebApp1** ) > **Dodaj** .</span><span class="sxs-lookup"><span data-stu-id="80acf-140">Accept the type ( **WebApp1User** if the project is named **WebApp1** ) > **Add** .</span></span>
* <span data-ttu-id="80acf-141">Wybierz pozycję **Dodaj** .</span><span class="sxs-lookup"><span data-stu-id="80acf-141">Select **Add** .</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="80acf-142">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="80acf-142">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="80acf-143">Jeśli jeszcze nie zainstalowano szkieletu ASP.NET Core, zainstaluj go teraz:</span><span class="sxs-lookup"><span data-stu-id="80acf-143">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="80acf-144">Dodaj odwołanie do pakietu do [Microsoft. VisualStudio. Web. CodeGeneration. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) do pliku projektu (. csproj).</span><span class="sxs-lookup"><span data-stu-id="80acf-144">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project (.csproj) file.</span></span> <span data-ttu-id="80acf-145">Uruchom następujące polecenie w katalogu projektu:</span><span class="sxs-lookup"><span data-stu-id="80acf-145">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="80acf-146">Uruchom następujące polecenie, aby wyświetlić listę :::no-loc(Identity)::: opcji szkieletu:</span><span class="sxs-lookup"><span data-stu-id="80acf-146">Run the following command to list the :::no-loc(Identity)::: scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="80acf-147">W folderze projektu uruchom program :::no-loc(Identity)::: szkieletowy:</span><span class="sxs-lookup"><span data-stu-id="80acf-147">In the project folder, run the :::no-loc(Identity)::: scaffolder:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

<span data-ttu-id="80acf-148">Postępuj zgodnie z instrukcjami w sekcji [migracje, UseAuthentication i układ](xref:security/authentication/scaffold-identity#efm) , aby wykonać następujące czynności:</span><span class="sxs-lookup"><span data-stu-id="80acf-148">Follow the instruction in [Migrations, UseAuthentication, and layout](xref:security/authentication/scaffold-identity#efm) to perform the following steps:</span></span>

* <span data-ttu-id="80acf-149">Utwórz migrację i zaktualizuj bazę danych.</span><span class="sxs-lookup"><span data-stu-id="80acf-149">Create a migration and update the database.</span></span>
* <span data-ttu-id="80acf-150">Add `UseAuthentication` to `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="80acf-150">Add `UseAuthentication` to `Startup.Configure`.</span></span>
* <span data-ttu-id="80acf-151">Dodaj `<partial name="_LoginPartial" />` do pliku układu.</span><span class="sxs-lookup"><span data-stu-id="80acf-151">Add `<partial name="_LoginPartial" />` to the layout file.</span></span>
* <span data-ttu-id="80acf-152">Przetestuj aplikację:</span><span class="sxs-lookup"><span data-stu-id="80acf-152">Test the app:</span></span>
  * <span data-ttu-id="80acf-153">Rejestrowanie użytkownika</span><span class="sxs-lookup"><span data-stu-id="80acf-153">Register a user</span></span>
  * <span data-ttu-id="80acf-154">Wybierz nową nazwę użytkownika (obok linku **wylogowania** ).</span><span class="sxs-lookup"><span data-stu-id="80acf-154">Select the new user name (next to the **Logout** link).</span></span> <span data-ttu-id="80acf-155">Może być konieczne rozszerzenie okna lub wybranie ikony paska nawigacyjnego, aby wyświetlić nazwę użytkownika i inne linki.</span><span class="sxs-lookup"><span data-stu-id="80acf-155">You might need to expand the window or select the navigation bar icon to show the user name and other links.</span></span>
  * <span data-ttu-id="80acf-156">Wybierz kartę **dane osobowe** .</span><span class="sxs-lookup"><span data-stu-id="80acf-156">Select the **Personal Data** tab.</span></span>
  * <span data-ttu-id="80acf-157">Wybierz przycisk **Pobierz** i zbadaj *PersonalData.js* pliku.</span><span class="sxs-lookup"><span data-stu-id="80acf-157">Select the **Download** button and examined the *PersonalData.json* file.</span></span>
  * <span data-ttu-id="80acf-158">Przetestuj przycisk **Usuń** , który spowoduje usunięcie zalogowanego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="80acf-158">Test the **Delete** button, which deletes the logged on user.</span></span>

## <a name="add-custom-user-data-to-the-no-locidentity-db"></a><span data-ttu-id="80acf-159">Dodawanie niestandardowych danych użytkownika do :::no-loc(Identity)::: bazy danych</span><span class="sxs-lookup"><span data-stu-id="80acf-159">Add custom user data to the :::no-loc(Identity)::: DB</span></span>

<span data-ttu-id="80acf-160">Zaktualizuj `:::no-loc(Identity):::User` klasę pochodną o właściwościach niestandardowych.</span><span class="sxs-lookup"><span data-stu-id="80acf-160">Update the `:::no-loc(Identity):::User` derived class with custom properties.</span></span> <span data-ttu-id="80acf-161">Jeśli nazwa projektu WebApp1, plik ma nazwę *obszary/ :::no-loc(Identity)::: /Data/WebApp1User.cs* .</span><span class="sxs-lookup"><span data-stu-id="80acf-161">If you named the project WebApp1, the file is named *Areas/:::no-loc(Identity):::/Data/WebApp1User.cs* .</span></span> <span data-ttu-id="80acf-162">Zaktualizuj plik przy użyciu następującego kodu:</span><span class="sxs-lookup"><span data-stu-id="80acf-162">Update the file with the following code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/:::no-loc(Identity):::/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/:::no-loc(Identity):::/Data/WebApp1User.cs)]

::: moniker-end

<span data-ttu-id="80acf-163">Właściwości z atrybutem [personaldata](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) są następujące:</span><span class="sxs-lookup"><span data-stu-id="80acf-163">Properties with the [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) attribute are:</span></span>

* <span data-ttu-id="80acf-164">Usuwane, gdy wywołują się strony *Areas/ :::no-loc(Identity)::: /Pages/Account/Manage/DeletePersonalData.cshtml* :::no-loc(Razor)::: `UserManager.Delete` .</span><span class="sxs-lookup"><span data-stu-id="80acf-164">Deleted when the *Areas/:::no-loc(Identity):::/Pages/Account/Manage/DeletePersonalData.cshtml* :::no-loc(Razor)::: Page calls `UserManager.Delete`.</span></span>
* <span data-ttu-id="80acf-165">Uwzględnione w pobranych danych przez stronę *obszary/ :::no-loc(Identity)::: /Pages/Account/Manage/DownloadPersonalData.cshtml* :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="80acf-165">Included in the downloaded data by the *Areas/:::no-loc(Identity):::/Pages/Account/Manage/DownloadPersonalData.cshtml* :::no-loc(Razor)::: Page.</span></span>

### <a name="update-the-accountmanageindexcshtml-page"></a><span data-ttu-id="80acf-166">Aktualizowanie strony account/Manage/index. cshtml</span><span class="sxs-lookup"><span data-stu-id="80acf-166">Update the Account/Manage/Index.cshtml page</span></span>

<span data-ttu-id="80acf-167">Zaktualizuj `InputModel` w *obszarach/ :::no-loc(Identity)::: /Pages/Account/Manage/index.cshtml.cs* następujący wyróżniony kod:</span><span class="sxs-lookup"><span data-stu-id="80acf-167">Update the `InputModel` in *Areas/:::no-loc(Identity):::/Pages/Account/Manage/Index.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/:::no-loc(Identity):::/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

<span data-ttu-id="80acf-168">Zaktualizuj *obszary/ :::no-loc(Identity)::: /Pages/Account/Manage/index.cshtml* przy użyciu następującego wyróżnionego znacznika:</span><span class="sxs-lookup"><span data-stu-id="80acf-168">Update the *Areas/:::no-loc(Identity):::/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/:::no-loc(Identity):::/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/:::no-loc(Identity):::/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

<span data-ttu-id="80acf-169">Zaktualizuj *obszary/ :::no-loc(Identity)::: /Pages/Account/Manage/index.cshtml* przy użyciu następującego wyróżnionego znacznika:</span><span class="sxs-lookup"><span data-stu-id="80acf-169">Update the *Areas/:::no-loc(Identity):::/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/:::no-loc(Identity):::/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a><span data-ttu-id="80acf-170">Aktualizowanie strony account/register. cshtml</span><span class="sxs-lookup"><span data-stu-id="80acf-170">Update the Account/Register.cshtml page</span></span>

<span data-ttu-id="80acf-171">Zaktualizuj `InputModel` w *obszarach/ :::no-loc(Identity)::: /Pages/Account/register.cshtml.cs* następujący wyróżniony kod:</span><span class="sxs-lookup"><span data-stu-id="80acf-171">Update the `InputModel` in *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

<span data-ttu-id="80acf-172">Zaktualizuj *obszary/ :::no-loc(Identity)::: /Pages/Account/register.cshtml* przy użyciu następującego wyróżnionego znacznika:</span><span class="sxs-lookup"><span data-stu-id="80acf-172">Update the *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

<span data-ttu-id="80acf-173">Zaktualizuj *obszary/ :::no-loc(Identity)::: /Pages/Account/register.cshtml* przy użyciu następującego wyróżnionego znacznika:</span><span class="sxs-lookup"><span data-stu-id="80acf-173">Update the *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


<span data-ttu-id="80acf-174">Skompiluj projekt.</span><span class="sxs-lookup"><span data-stu-id="80acf-174">Build the project.</span></span>

### <a name="add-a-migration-for-the-custom-user-data"></a><span data-ttu-id="80acf-175">Dodawanie migracji niestandardowych danych użytkownika</span><span class="sxs-lookup"><span data-stu-id="80acf-175">Add a migration for the custom user data</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="80acf-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="80acf-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="80acf-177">W **konsoli Menedżera pakietów** programu Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="80acf-177">In the Visual Studio **Package Manager Console** :</span></span>

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="80acf-178">interfejs wiersza polecenia programu .NET Core</span><span class="sxs-lookup"><span data-stu-id="80acf-178">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a><span data-ttu-id="80acf-179">Testowanie tworzenia, wyświetlania, pobierania, usuwania niestandardowych danych użytkownika</span><span class="sxs-lookup"><span data-stu-id="80acf-179">Test create, view, download, delete custom user data</span></span>

<span data-ttu-id="80acf-180">Przetestuj aplikację:</span><span class="sxs-lookup"><span data-stu-id="80acf-180">Test the app:</span></span>

* <span data-ttu-id="80acf-181">Zarejestruj nowego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="80acf-181">Register a new user.</span></span>
* <span data-ttu-id="80acf-182">Wyświetlanie niestandardowych danych użytkownika na `/:::no-loc(Identity):::/Account/Manage` stronie.</span><span class="sxs-lookup"><span data-stu-id="80acf-182">View the custom user data on the `/:::no-loc(Identity):::/Account/Manage` page.</span></span>
* <span data-ttu-id="80acf-183">Pobierz i Przejrzyj dane osobowe użytkowników ze `/:::no-loc(Identity):::/Account/Manage/PersonalData` strony.</span><span class="sxs-lookup"><span data-stu-id="80acf-183">Download and view the users personal data from the `/:::no-loc(Identity):::/Account/Manage/PersonalData` page.</span></span>

## <a name="add-claims-to-no-locidentity-using-iuserclaimsprincipalfactoryapplicationuser"></a><span data-ttu-id="80acf-184">Dodawanie oświadczeń do :::no-loc(Identity)::: korzystania z IUserClaimsPrincipalFactory<ApplicationUser></span><span class="sxs-lookup"><span data-stu-id="80acf-184">Add claims to :::no-loc(Identity)::: using IUserClaimsPrincipalFactory<ApplicationUser></span></span>

> [!NOTE]
> <span data-ttu-id="80acf-185">Ta sekcja nie jest rozszerzeniem poprzedniego samouczka.</span><span class="sxs-lookup"><span data-stu-id="80acf-185">This section isn't an extension of the previous tutorial.</span></span> <span data-ttu-id="80acf-186">Aby zastosować następujące kroki do aplikacji skompilowanej przy użyciu samouczka, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/18797)w usłudze GitHub.</span><span class="sxs-lookup"><span data-stu-id="80acf-186">To apply the following steps to the app built using the tutorial, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/18797).</span></span>

<span data-ttu-id="80acf-187">Dodatkowe oświadczenia można dodać do programu przy :::no-loc(ASP.NET Core Identity)::: użyciu `IUserClaimsPrincipalFactory<T>` interfejsu.</span><span class="sxs-lookup"><span data-stu-id="80acf-187">Additional claims can be added to :::no-loc(ASP.NET Core Identity)::: by using the `IUserClaimsPrincipalFactory<T>` interface.</span></span> <span data-ttu-id="80acf-188">Tę klasę można dodać do aplikacji w `Startup.ConfigureServices` metodzie.</span><span class="sxs-lookup"><span data-stu-id="80acf-188">This class can be added to the app in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="80acf-189">Dodaj niestandardową implementację klasy w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="80acf-189">Add the custom implementation of the class as follows:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Add:::no-loc(Identity):::<ApplicationUser, :::no-loc(Identity):::Role>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddScoped<IUserClaimsPrincipalFactory<ApplicationUser>, 
        AdditionalUserClaimsPrincipalFactory>();
```

<span data-ttu-id="80acf-190">Kod demonstracyjny używa `ApplicationUser` klasy.</span><span class="sxs-lookup"><span data-stu-id="80acf-190">The demo code uses the `ApplicationUser` class.</span></span> <span data-ttu-id="80acf-191">Ta klasa dodaje `IsAdmin` Właściwość, która jest używana do dodawania dodatkowego żądania.</span><span class="sxs-lookup"><span data-stu-id="80acf-191">This class adds an `IsAdmin` property which is used to add the additional claim.</span></span>

```csharp
public class ApplicationUser : :::no-loc(Identity):::User
{
    public bool IsAdmin { get; set; }
}
```

<span data-ttu-id="80acf-192">`AdditionalUserClaimsPrincipalFactory`Implementuje `UserClaimsPrincipalFactory` interfejs.</span><span class="sxs-lookup"><span data-stu-id="80acf-192">The `AdditionalUserClaimsPrincipalFactory` implements the `UserClaimsPrincipalFactory` interface.</span></span> <span data-ttu-id="80acf-193">Do. zostanie dodane nowe zastrzeżenie roli `ClaimsPrincipal` .</span><span class="sxs-lookup"><span data-stu-id="80acf-193">A new role claim is added to the `ClaimsPrincipal`.</span></span>

```csharp
public class AdditionalUserClaimsPrincipalFactory 
        : UserClaimsPrincipalFactory<ApplicationUser, :::no-loc(Identity):::Role>
{
    public AdditionalUserClaimsPrincipalFactory( 
        UserManager<ApplicationUser> userManager,
        RoleManager<:::no-loc(Identity):::Role> roleManager, 
        IOptions<:::no-loc(Identity):::Options> optionsAccessor) 
        : base(userManager, roleManager, optionsAccessor)
    {}

    public async override Task<ClaimsPrincipal> CreateAsync(ApplicationUser user)
    {
        var principal = await base.CreateAsync(user);
        var identity = (Claims:::no-loc(Identity):::)principal.:::no-loc(Identity):::;

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

<span data-ttu-id="80acf-194">Dodatkowe zgłoszenie można następnie użyć w aplikacji.</span><span class="sxs-lookup"><span data-stu-id="80acf-194">The additional claim can then be used in the app.</span></span> <span data-ttu-id="80acf-195">Na :::no-loc(Razor)::: stronie `IAuthorizationService` wystąpienie może być używane w celu uzyskania dostępu do wartości żądania.</span><span class="sxs-lookup"><span data-stu-id="80acf-195">In a :::no-loc(Razor)::: Page, the `IAuthorizationService` instance can be used to access the claim value.</span></span>

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
