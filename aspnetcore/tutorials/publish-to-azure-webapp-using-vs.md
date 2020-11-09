---
title: Publikowanie aplikacji ASP.NET Core na platformie Azure przy użyciu programu Visual Studio
author: rick-anderson
description: Dowiedz się, jak opublikować aplikację ASP.NET Core, aby Azure App Service przy użyciu programu Visual Studio.
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 07/10/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: 817169503a80a771354e32123d65ba2bf388aa2d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060225"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a><span data-ttu-id="bb6e2-103">Publikowanie aplikacji ASP.NET Core na platformie Azure przy użyciu programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bb6e2-103">Publish an ASP.NET Core app to Azure with Visual Studio</span></span>

<span data-ttu-id="bb6e2-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="bb6e2-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>
::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

::: moniker-end


<span data-ttu-id="bb6e2-105">Zobacz [publikowanie aplikacji sieci Web, aby Azure App Service przy użyciu Visual Studio dla komputerów Mac](/visualstudio/mac/publish-app-svc?view=vsmac-2019) , jeśli pracujesz na macOS.</span><span class="sxs-lookup"><span data-stu-id="bb6e2-105">See [Publish a Web app to Azure App Service using Visual Studio for Mac](/visualstudio/mac/publish-app-svc?view=vsmac-2019) if you are working on macOS.</span></span>

<span data-ttu-id="bb6e2-106">Aby rozwiązać problem z wdrożeniem App Service, zobacz <xref:test/troubleshoot-azure-iis> .</span><span class="sxs-lookup"><span data-stu-id="bb6e2-106">To troubleshoot an App Service deployment issue, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="set-up"></a><span data-ttu-id="bb6e2-107">Konfiguruj</span><span class="sxs-lookup"><span data-stu-id="bb6e2-107">Set up</span></span>

* <span data-ttu-id="bb6e2-108">Otwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/dotnet/) , jeśli go nie masz.</span><span class="sxs-lookup"><span data-stu-id="bb6e2-108">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span> 

## <a name="create-a-web-app"></a><span data-ttu-id="bb6e2-109">Tworzenie aplikacji internetowej</span><span class="sxs-lookup"><span data-stu-id="bb6e2-109">Create a web app</span></span>

<span data-ttu-id="bb6e2-110">Na stronie startowej programu Visual Studio wybierz pozycję **plik > nowy > projekt...**</span><span class="sxs-lookup"><span data-stu-id="bb6e2-110">In the Visual Studio Start Page, select **File > New > Project...**</span></span>

![Menu Plik](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

<span data-ttu-id="bb6e2-112">Wypełnij okno dialogowe **Nowy projekt** :</span><span class="sxs-lookup"><span data-stu-id="bb6e2-112">Complete the **New Project** dialog:</span></span>

* <span data-ttu-id="bb6e2-113">Wybierz **ASP.NET Core aplikacji sieci Web** .</span><span class="sxs-lookup"><span data-stu-id="bb6e2-113">Select **ASP.NET Core Web Application** .</span></span>
* <span data-ttu-id="bb6e2-114">Wybierz opcję **Dalej** .</span><span class="sxs-lookup"><span data-stu-id="bb6e2-114">Select **Next** .</span></span>

![Okno dialogowe Nowy projekt](publish-to-azure-webapp-using-vs/_static/new_prj.png)

<span data-ttu-id="bb6e2-116">W oknie dialogowym **Nowa aplikacja sieci Web ASP.NET Core** :</span><span class="sxs-lookup"><span data-stu-id="bb6e2-116">In the **New ASP.NET Core Web Application** dialog:</span></span>

* <span data-ttu-id="bb6e2-117">Wybierz pozycję **aplikacja sieci Web** .</span><span class="sxs-lookup"><span data-stu-id="bb6e2-117">Select **Web Application** .</span></span>
* <span data-ttu-id="bb6e2-118">Wybierz pozycję **Zmień** w obszarze Uwierzytelnianie.</span><span class="sxs-lookup"><span data-stu-id="bb6e2-118">Select **Change** under Authentication.</span></span>

![Nowe okno dialogowe sieci Web ASP.NET Core](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

<span data-ttu-id="bb6e2-120">Zostanie wyświetlone okno dialogowe **Zmienianie uwierzytelniania** .</span><span class="sxs-lookup"><span data-stu-id="bb6e2-120">The **Change Authentication** dialog appears.</span></span> 

* <span data-ttu-id="bb6e2-121">Wybierz pozycję **indywidualne konta użytkowników** .</span><span class="sxs-lookup"><span data-stu-id="bb6e2-121">Select **Individual User Accounts** .</span></span>
* <span data-ttu-id="bb6e2-122">Wybierz **przycisk OK** , aby powrócić do **nowej aplikacji sieci Web ASP.NET Core** , a następnie wybierz pozycję **Utwórz** .</span><span class="sxs-lookup"><span data-stu-id="bb6e2-122">Select **OK** to return to the **New ASP.NET Core Web Application** , then select **Create** .</span></span>

![Nowe okno dialogowe uwierzytelniania w sieci Web ASP.NET Core](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

<span data-ttu-id="bb6e2-124">Program Visual Studio tworzy rozwiązanie.</span><span class="sxs-lookup"><span data-stu-id="bb6e2-124">Visual Studio creates the solution.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="bb6e2-125">Uruchamianie aplikacji</span><span class="sxs-lookup"><span data-stu-id="bb6e2-125">Run the app</span></span>

* <span data-ttu-id="bb6e2-126">Naciśnij klawisze CTRL + F5, aby uruchomić projekt.</span><span class="sxs-lookup"><span data-stu-id="bb6e2-126">Press CTRL+F5 to run the project.</span></span>
* <span data-ttu-id="bb6e2-127">Przetestuj łącze **prywatność** .</span><span class="sxs-lookup"><span data-stu-id="bb6e2-127">Test the **Privacy** link.</span></span>

![Aplikacja sieci Web otwarta w przeglądarce Microsoft Edge na hoście lokalnym](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a><span data-ttu-id="bb6e2-129">Rejestrowanie użytkownika</span><span class="sxs-lookup"><span data-stu-id="bb6e2-129">Register a user</span></span>

* <span data-ttu-id="bb6e2-130">Wybierz pozycję **zarejestruj** i Zarejestruj nowego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="bb6e2-130">Select **Register** and register a new user.</span></span> <span data-ttu-id="bb6e2-131">Możesz użyć fikcyjnego adresu e-mail.</span><span class="sxs-lookup"><span data-stu-id="bb6e2-131">You can use a fictitious email address.</span></span> <span data-ttu-id="bb6e2-132">Gdy przesyłasz, na stronie jest wyświetlany następujący błąd:</span><span class="sxs-lookup"><span data-stu-id="bb6e2-132">When you submit, the page displays the following error:</span></span>

    <span data-ttu-id="bb6e2-133">*"Operacja bazy danych nie powiodła się podczas przetwarzania żądania. Zastosowanie istniejących migracji dla kontekstu bazy danych aplikacji może rozwiązać ten problem.*</span><span class="sxs-lookup"><span data-stu-id="bb6e2-133">*"A database operation failed while processing the request. Applying existing migrations for Application DB context may resolve this issue."*</span></span>
* <span data-ttu-id="bb6e2-134">Wybierz pozycję **Zastosuj migracje** , a po aktualizacji strony Odśwież stronę.</span><span class="sxs-lookup"><span data-stu-id="bb6e2-134">Select **Apply Migrations** and, once the page updates, refresh the page.</span></span>

![Operacja bazy danych nie powiodła się podczas przetwarzania żądania.](publish-to-azure-webapp-using-vs/_static/mig.png)

<span data-ttu-id="bb6e2-137">Aplikacja wyświetli wiadomość e-mail użytą do zarejestrowania nowego użytkownika i linku do **wylogowania** .</span><span class="sxs-lookup"><span data-stu-id="bb6e2-137">The app displays the email used to register the new user and a **Logout** link.</span></span>

![Aplikacja sieci Web otwarta w przeglądarce Microsoft Edge.](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a><span data-ttu-id="bb6e2-140">Wdrażanie aplikacji na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="bb6e2-140">Deploy the app to Azure</span></span>

<span data-ttu-id="bb6e2-141">Kliknij prawym przyciskiem myszy projekt w Eksplorator rozwiązań i wybierz pozycję **Publikuj...** .</span><span class="sxs-lookup"><span data-stu-id="bb6e2-141">Right-click on the project in Solution Explorer and select **Publish...** .</span></span>

![Menu kontekstowe otwierane z wyróżnionym linkiem publikowania](publish-to-azure-webapp-using-vs/_static/pub.png)

<span data-ttu-id="bb6e2-143">W oknie dialogowym **publikowania** :</span><span class="sxs-lookup"><span data-stu-id="bb6e2-143">In the **Publish** dialog:</span></span>

* <span data-ttu-id="bb6e2-144">Wybierz pozycję **Azure** .</span><span class="sxs-lookup"><span data-stu-id="bb6e2-144">Select **Azure** .</span></span>
* <span data-ttu-id="bb6e2-145">Wybierz opcję **Dalej** .</span><span class="sxs-lookup"><span data-stu-id="bb6e2-145">Select **Next** .</span></span>

![Okno dialogowe publikowania](publish-to-azure-webapp-using-vs/_static/maas1.png)

<span data-ttu-id="bb6e2-147">W oknie dialogowym **publikowania** :</span><span class="sxs-lookup"><span data-stu-id="bb6e2-147">In the **Publish** dialog:</span></span>

* <span data-ttu-id="bb6e2-148">Wybierz pozycję **Azure App Service (Linux)** .</span><span class="sxs-lookup"><span data-stu-id="bb6e2-148">Select **Azure App Service (Linux)** .</span></span>
* <span data-ttu-id="bb6e2-149">Wybierz opcję **Dalej** .</span><span class="sxs-lookup"><span data-stu-id="bb6e2-149">Select **Next** .</span></span>

![Okno dialogowe publikowania: Wybieranie usługi platformy Azure](publish-to-azure-webapp-using-vs/_static/maas2.png)

<span data-ttu-id="bb6e2-151">W oknie dialogowym **Publikowanie** wybierz pozycję **Utwórz nowy Azure App Service...**</span><span class="sxs-lookup"><span data-stu-id="bb6e2-151">In the **Publish** dialog select **Create a new Azure App Service...**</span></span>

![Okno dialogowe publikowania: wybierz wystąpienie usługi platformy Azure](publish-to-azure-webapp-using-vs/_static/maas3.png)

<span data-ttu-id="bb6e2-153">Zostanie wyświetlone okno dialogowe **tworzenie App Service** :</span><span class="sxs-lookup"><span data-stu-id="bb6e2-153">The **Create App Service** dialog appears:</span></span>

* <span data-ttu-id="bb6e2-154">Pola **Nazwa aplikacji** , **Grupa zasobów** i zapis **planu App Service** są wypełniane.</span><span class="sxs-lookup"><span data-stu-id="bb6e2-154">The **App Name** , **Resource Group** , and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="bb6e2-155">Te nazwy można zachować lub zmienić.</span><span class="sxs-lookup"><span data-stu-id="bb6e2-155">You can keep these names or change them.</span></span>
* <span data-ttu-id="bb6e2-156">Wybierz pozycję **Utwórz** .</span><span class="sxs-lookup"><span data-stu-id="bb6e2-156">Select **Create** .</span></span>

![Okno dialogowe Tworzenie usługi App Service](publish-to-azure-webapp-using-vs/_static/newrg1.png)

<span data-ttu-id="bb6e2-158">Po zakończeniu tworzenia okno dialogowe zostanie automatycznie zamknięte, a okno dialogowe **publikowania** ponownie przyniesie fokus:</span><span class="sxs-lookup"><span data-stu-id="bb6e2-158">After creation is completed the dialog is automatically closed and the **Publish** dialog gets focus again:</span></span>

* <span data-ttu-id="bb6e2-159">Nowo utworzone wystąpienie jest automatycznie wybierane.</span><span class="sxs-lookup"><span data-stu-id="bb6e2-159">The new instance that was just created is automatically selected.</span></span>
* <span data-ttu-id="bb6e2-160">Wybierz pozycję **Zakończ** .</span><span class="sxs-lookup"><span data-stu-id="bb6e2-160">Select **Finish** .</span></span>

![Okno dialogowe publikowania: wybierz wystąpienie App Service](publish-to-azure-webapp-using-vs/_static/select_as.png)

<span data-ttu-id="bb6e2-162">Następnie zostanie wyświetlona strona **Podsumowanie profilu publikowania** .</span><span class="sxs-lookup"><span data-stu-id="bb6e2-162">Next you see the **Publish Profile summary** page.</span></span> <span data-ttu-id="bb6e2-163">Program Visual Studio wykrył, że ta aplikacja wymaga bazy danych SQL Server i prosi o jej konfigurację.</span><span class="sxs-lookup"><span data-stu-id="bb6e2-163">Visual Studio has detected that this application requires a SQL Server database and it's asking you to configure it.</span></span> <span data-ttu-id="bb6e2-164">Wybierz pozycję **Konfiguruj** .</span><span class="sxs-lookup"><span data-stu-id="bb6e2-164">Select **Configure** .</span></span>

![Strona podsumowania profilu publikowania: Konfigurowanie zależności SQL Server](publish-to-azure-webapp-using-vs/_static/sql.png)

<span data-ttu-id="bb6e2-166">Zostanie wyświetlone okno dialogowe **Konfigurowanie zależności** :</span><span class="sxs-lookup"><span data-stu-id="bb6e2-166">The **Configure dependency** dialog appears:</span></span>

* <span data-ttu-id="bb6e2-167">Wybierz **Azure SQL Database** .</span><span class="sxs-lookup"><span data-stu-id="bb6e2-167">Select **Azure SQL Database** .</span></span>
* <span data-ttu-id="bb6e2-168">Wybierz opcję **Dalej** .</span><span class="sxs-lookup"><span data-stu-id="bb6e2-168">Select **Next** .</span></span>

![Konfigurowanie okna dialogowego zależności SQL Server](publish-to-azure-webapp-using-vs/_static/sql1.png)

<span data-ttu-id="bb6e2-170">W oknie dialogowym **Konfigurowanie usługi Azure SQL Database** wybierz pozycję **Utwórz SQL Database**</span><span class="sxs-lookup"><span data-stu-id="bb6e2-170">In the **Configure Azure SQL database** dialog select **Create a SQL Database**</span></span>

![Skonfiguruj okno dialogowe Azure SQL Database](publish-to-azure-webapp-using-vs/_static/sql2.png)

<span data-ttu-id="bb6e2-172">Zostanie wyświetlona **Azure SQL Database tworzenia** :</span><span class="sxs-lookup"><span data-stu-id="bb6e2-172">The **Create Azure SQL Database** appears:</span></span>

* <span data-ttu-id="bb6e2-173">Pola **Nazwa bazy danych** , **Grupa zasobów** , **serwer bazy danych** i zapis **planu App Service** są wypełniane.</span><span class="sxs-lookup"><span data-stu-id="bb6e2-173">The **Database name** , **Resource Group** , **Database server** and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="bb6e2-174">Te wartości można zachować lub zmienić.</span><span class="sxs-lookup"><span data-stu-id="bb6e2-174">You can keep these values or change them.</span></span>
* <span data-ttu-id="bb6e2-175">Wprowadź **nazwę użytkownika** i **hasło administratora** bazy danych dla wybranego **serwera bazy danych** (Zwróć uwagę na używane konto musi mieć uprawnienia niezbędne do utworzenia nowej bazy danych Azure SQL Database).</span><span class="sxs-lookup"><span data-stu-id="bb6e2-175">Enter the **Database administrator username** and **Database administrator password** for the selected **Database server** (note the account you use must have the necessary permissions to create the new Azure SQL database)</span></span>
* <span data-ttu-id="bb6e2-176">Wybierz pozycję **Utwórz** .</span><span class="sxs-lookup"><span data-stu-id="bb6e2-176">Select **Create** .</span></span>

![Nowe okno dialogowe Azure SQL Database](publish-to-azure-webapp-using-vs/_static/sql_create.png)

<span data-ttu-id="bb6e2-178">Po zakończeniu tworzenia okno dialogowe jest automatycznie zamykane, a okno dialogowe **konfigurowanie Azure SQL Database** ponownie przyniesie fokus:</span><span class="sxs-lookup"><span data-stu-id="bb6e2-178">After creation is completed the dialog is automatically closed and the **Configure Azure SQL Database** dialog gets focus again:</span></span>

* <span data-ttu-id="bb6e2-179">Nowo utworzone wystąpienie jest automatycznie wybierane.</span><span class="sxs-lookup"><span data-stu-id="bb6e2-179">The new instance that was just created is automatically selected.</span></span>
* <span data-ttu-id="bb6e2-180">Wybierz opcję **Dalej** .</span><span class="sxs-lookup"><span data-stu-id="bb6e2-180">Select **Next** .</span></span>

![Skonfiguruj okno dialogowe Azure SQL Database](publish-to-azure-webapp-using-vs/_static/sql_select.png)

<span data-ttu-id="bb6e2-182">W następnym kroku okna dialogowego **konfigurowanie Azure SQL Database** :</span><span class="sxs-lookup"><span data-stu-id="bb6e2-182">In the next step of the **Configure Azure SQL Database** dialog:</span></span>

* <span data-ttu-id="bb6e2-183">Wprowadź wartość w polach **Nazwa użytkownika** i **hasło połączenia** bazy danych.</span><span class="sxs-lookup"><span data-stu-id="bb6e2-183">Enter the **Database connection user name** and **Database connection password** fields.</span></span> <span data-ttu-id="bb6e2-184">Są to szczegóły używane przez aplikację do łączenia się z bazą danych w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="bb6e2-184">These are the details your application will use to connect to the database at runtime.</span></span> <span data-ttu-id="bb6e2-185">Najlepszym rozwiązaniem jest unikanie używania tych samych informacji, co nazwa użytkownika administratora & hasło użyte w poprzednim kroku.</span><span class="sxs-lookup"><span data-stu-id="bb6e2-185">Best practice is to avoid using the same details as the admin username & password used in the previous step.</span></span>
* <span data-ttu-id="bb6e2-186">Wybierz pozycję **Zakończ** .</span><span class="sxs-lookup"><span data-stu-id="bb6e2-186">Select **Finish** .</span></span>

![Konfigurowanie okna dialogowego Azure SQL Database, szczegóły parametrów połączenia](publish-to-azure-webapp-using-vs/_static/sql_connection.png)

<span data-ttu-id="bb6e2-188">Na stronie **Podsumowanie profilu publikowania** wybierz pozycję **Ustawienia** :</span><span class="sxs-lookup"><span data-stu-id="bb6e2-188">In the **Publish Profile summary** page select **Settings** :</span></span>

![Strona podsumowania profilu publikowania: Edycja ustawień](publish-to-azure-webapp-using-vs/_static/pp_configured.png)

<span data-ttu-id="bb6e2-190">Na stronie **Ustawienia** okna dialogowego **Publikowanie** :</span><span class="sxs-lookup"><span data-stu-id="bb6e2-190">On the **Settings** page of the **Publish** dialog:</span></span>

* <span data-ttu-id="bb6e2-191">Rozwiń węzeł **bazy danych** i zaznacz pole wyboru **Użyj tych parametrów połączenia w czasie wykonywania** .</span><span class="sxs-lookup"><span data-stu-id="bb6e2-191">Expand **Databases** and check **Use this connection string at runtime** .</span></span>
* <span data-ttu-id="bb6e2-192">Rozwiń węzeł **Entity Framework migracje** i zaznacz opcję **Zastosuj tę migrację przy publikowaniu** .</span><span class="sxs-lookup"><span data-stu-id="bb6e2-192">Expand **Entity Framework Migrations** and check **Apply this migration on publish** .</span></span>

* <span data-ttu-id="bb6e2-193">Wybierz pozycję **Zapisz** .</span><span class="sxs-lookup"><span data-stu-id="bb6e2-193">Select **Save** .</span></span> <span data-ttu-id="bb6e2-194">Program Visual Studio wraca do okna dialogowego **Publikowanie** .</span><span class="sxs-lookup"><span data-stu-id="bb6e2-194">Visual Studio returns to the **Publish** dialog.</span></span> 

![Okno dialogowe publikowania: Panel ustawień](publish-to-azure-webapp-using-vs/_static/pp_settings.png)

<span data-ttu-id="bb6e2-196">Kliknij przycisk **Opublikuj** .</span><span class="sxs-lookup"><span data-stu-id="bb6e2-196">Click **Publish** .</span></span> <span data-ttu-id="bb6e2-197">Program Visual Studio publikuje aplikację na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="bb6e2-197">Visual Studio publishes your app to Azure.</span></span> <span data-ttu-id="bb6e2-198">Po zakończeniu wdrażania aplikacja zostanie otwarta w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="bb6e2-198">When the deployment completes, the app is opened in a browser.</span></span>

![Okno dialogowe publikowania: Panel ustawień](publish-to-azure-webapp-using-vs/_static/pp_publish.png)

### <a name="update-the-app"></a><span data-ttu-id="bb6e2-200">Aktualizowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="bb6e2-200">Update the app</span></span>

* <span data-ttu-id="bb6e2-201">Edytuj stronę *Pages/index. cshtml* Razor i zmień jej zawartość.</span><span class="sxs-lookup"><span data-stu-id="bb6e2-201">Edit the *Pages/Index.cshtml* Razor page and change its contents.</span></span> <span data-ttu-id="bb6e2-202">Na przykład można zmodyfikować akapit, aby powiedzieć "Hello ASP.NET Core!":</span><span class="sxs-lookup"><span data-stu-id="bb6e2-202">For example, you can modify the paragraph to say "Hello ASP.NET Core!":</span></span>

    [!code-html[Index](publish-to-azure-webapp-using-vs/sample/index.cshtml?highlight=10&range=1-12)]

* <span data-ttu-id="bb6e2-203">Na stronie **Podsumowanie profilu publikowania** ponownie wybierz pozycję **Opublikuj** .</span><span class="sxs-lookup"><span data-stu-id="bb6e2-203">Select **Publish** from the **Publish Profile summary** page again.</span></span>

![Strona podsumowania profilu publikowania](publish-to-azure-webapp-using-vs/_static/pp_publish.png)

* <span data-ttu-id="bb6e2-205">Po opublikowaniu aplikacji Sprawdź, czy wprowadzone zmiany są dostępne na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="bb6e2-205">After the app is published, verify the changes you made are available on Azure.</span></span>

![Sprawdzanie, czy zadanie zostało ukończone](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a><span data-ttu-id="bb6e2-207">Czyszczenie</span><span class="sxs-lookup"><span data-stu-id="bb6e2-207">Clean up</span></span>

<span data-ttu-id="bb6e2-208">Po zakończeniu testowania aplikacji przejdź do [Azure Portal](https://portal.azure.com/) i Usuń aplikację.</span><span class="sxs-lookup"><span data-stu-id="bb6e2-208">When you have finished testing the app, go to the [Azure portal](https://portal.azure.com/) and delete the app.</span></span>

* <span data-ttu-id="bb6e2-209">Wybierz pozycję **grupy zasobów** , a następnie wybierz utworzoną grupę zasobów.</span><span class="sxs-lookup"><span data-stu-id="bb6e2-209">Select **Resource groups** , then select the resource group you created.</span></span>

![Witryna Azure Portal: grupy zasobów w menu paska bocznego](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* <span data-ttu-id="bb6e2-211">Na stronie **grupy zasobów** wybierz pozycję **Usuń** .</span><span class="sxs-lookup"><span data-stu-id="bb6e2-211">In the **Resource groups** page, select **Delete** .</span></span>

![Witryna Azure Portal: Strona grupy zasobów](publish-to-azure-webapp-using-vs/_static/rgd.png)

* <span data-ttu-id="bb6e2-213">Wprowadź nazwę grupy zasobów i wybierz pozycję **Usuń** .</span><span class="sxs-lookup"><span data-stu-id="bb6e2-213">Enter the name of the resource group and select **Delete** .</span></span> <span data-ttu-id="bb6e2-214">Twoja aplikacja i wszystkie inne zasoby utworzone w tym samouczku zostaną usunięte z platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="bb6e2-214">Your app and all other resources created in this tutorial are now deleted from Azure.</span></span>

### <a name="next-steps"></a><span data-ttu-id="bb6e2-215">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="bb6e2-215">Next steps</span></span>

* <xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a><span data-ttu-id="bb6e2-216">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="bb6e2-216">Additional resources</span></span>

* <span data-ttu-id="bb6e2-217">Aby uzyskać Visual Studio Code, zobacz temat [Publikowanie profilów](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).</span><span class="sxs-lookup"><span data-stu-id="bb6e2-217">For Visual Studio Code, see [Publish profiles](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).</span></span>
* [<span data-ttu-id="bb6e2-218">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="bb6e2-218">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="bb6e2-219">Grupa zasobów platformy Azure</span><span class="sxs-lookup"><span data-stu-id="bb6e2-219">Azure resource groups</span></span>](/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [<span data-ttu-id="bb6e2-220">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="bb6e2-220">Azure SQL Database</span></span>](/azure/sql-database/)
* <xref:host-and-deploy/visual-studio-publish-profiles>
* <xref:test/troubleshoot-azure-iis>