---
title: Publikowanie aplikacji ASP.NET Core na platformie Azure za pomocą programu Visual Studio
author: rick-anderson
description: Dowiedz się, jak opublikować aplikację ASP.NET Core w usłudze Azure App Service za pomocą programu Visual Studio.
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: 7fc3644df3dcb957f2537538aaa9506c6b38a480
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78662205"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a><span data-ttu-id="ea836-103">Publikowanie aplikacji ASP.NET Core na platformie Azure za pomocą programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ea836-103">Publish an ASP.NET Core app to Azure with Visual Studio</span></span>

<span data-ttu-id="ea836-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ea836-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>
::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

::: moniker-end


<span data-ttu-id="ea836-105">Zobacz [Publikowanie aplikacji sieci Web w usłudze Azure App Service przy użyciu programu Visual Studio dla komputerów Mac,](https://docs.microsoft.com/visualstudio/mac/publish-app-svc?view=vsmac-2019) jeśli pracujesz z systemem macOS.</span><span class="sxs-lookup"><span data-stu-id="ea836-105">See [Publish a Web app to Azure App Service using Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/publish-app-svc?view=vsmac-2019) if you are working on macOS.</span></span>

<span data-ttu-id="ea836-106">Aby rozwiązać problem z wdrażaniem usługi App Service, zobacz <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="ea836-106">To troubleshoot an App Service deployment issue, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="set-up"></a><span data-ttu-id="ea836-107">Konfiguruj</span><span class="sxs-lookup"><span data-stu-id="ea836-107">Set up</span></span>

* <span data-ttu-id="ea836-108">Otwórz [bezpłatne konto platformy Azure,](https://azure.microsoft.com/free/dotnet/) jeśli go nie masz.</span><span class="sxs-lookup"><span data-stu-id="ea836-108">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span> 

## <a name="create-a-web-app"></a><span data-ttu-id="ea836-109">Tworzenie aplikacji internetowej</span><span class="sxs-lookup"><span data-stu-id="ea836-109">Create a web app</span></span>

<span data-ttu-id="ea836-110">Na stronie startowej programu Visual Studio wybierz pozycję **Plik > nowy projekt >...**</span><span class="sxs-lookup"><span data-stu-id="ea836-110">In the Visual Studio Start Page, select **File > New > Project...**</span></span>

![Menu Plik](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

<span data-ttu-id="ea836-112">Ukończ okno dialogowe **Nowy projekt:**</span><span class="sxs-lookup"><span data-stu-id="ea836-112">Complete the **New Project** dialog:</span></span>

* <span data-ttu-id="ea836-113">W lewym okienku wybierz pozycję **.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="ea836-113">In the left pane, select **.NET Core**.</span></span>
* <span data-ttu-id="ea836-114">W środkowym okienku wybierz pozycję **ASP.NET Core Web Application**.</span><span class="sxs-lookup"><span data-stu-id="ea836-114">In the center pane, select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="ea836-115">Kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="ea836-115">Select **OK**.</span></span>

![Okno dialogowe Nowy projekt](publish-to-azure-webapp-using-vs/_static/new_prj.png)

<span data-ttu-id="ea836-117">W oknie dialogowym **Nowa ASP.NET Podstawowa aplikacja sieci Web:**</span><span class="sxs-lookup"><span data-stu-id="ea836-117">In the **New ASP.NET Core Web Application** dialog:</span></span>

* <span data-ttu-id="ea836-118">Wybierz **aplikację sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="ea836-118">Select **Web Application**.</span></span>
* <span data-ttu-id="ea836-119">Wybierz pozycję **Zmień uwierzytelnianie**.</span><span class="sxs-lookup"><span data-stu-id="ea836-119">Select **Change Authentication**.</span></span>

![Okno dialogowe Nowy projekt](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

<span data-ttu-id="ea836-121">Zostanie wyświetlone okno dialogowe **Uwierzytelnianie zmian.**</span><span class="sxs-lookup"><span data-stu-id="ea836-121">The **Change Authentication** dialog appears.</span></span> 

* <span data-ttu-id="ea836-122">Wybierz **pozycję Indywidualne konta użytkowników**.</span><span class="sxs-lookup"><span data-stu-id="ea836-122">Select **Individual User Accounts**.</span></span>
* <span data-ttu-id="ea836-123">Wybierz **przycisk OK,** aby powrócić do **nowej ASP.NET podstawowej aplikacji sieci Web,** a następnie ponownie wybierz przycisk **OK.**</span><span class="sxs-lookup"><span data-stu-id="ea836-123">Select **OK** to return to the **New ASP.NET Core Web Application**, then select **OK** again.</span></span>

![Nowe okno dialogowe uwierzytelniania sieci Web ASP.NET Core](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

<span data-ttu-id="ea836-125">Visual Studio tworzy rozwiązanie.</span><span class="sxs-lookup"><span data-stu-id="ea836-125">Visual Studio creates the solution.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="ea836-126">Uruchomienie aplikacji</span><span class="sxs-lookup"><span data-stu-id="ea836-126">Run the app</span></span>

* <span data-ttu-id="ea836-127">Naciśnij klawisze CTRL+F5, aby uruchomić projekt.</span><span class="sxs-lookup"><span data-stu-id="ea836-127">Press CTRL+F5 to run the project.</span></span>
* <span data-ttu-id="ea836-128">Testowanie łączy **Informacje** i **Kontakt.**</span><span class="sxs-lookup"><span data-stu-id="ea836-128">Test the **About** and **Contact** links.</span></span>

![Aplikacja sieci Web otwarta w programie Microsoft Edge na stronie localhost](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a><span data-ttu-id="ea836-130">Zarejestruj użytkownika</span><span class="sxs-lookup"><span data-stu-id="ea836-130">Register a user</span></span>

* <span data-ttu-id="ea836-131">Wybierz **zarejestruj się** i zarejestruj nowego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="ea836-131">Select **Register** and register a new user.</span></span> <span data-ttu-id="ea836-132">Możesz użyć fikcyjnego adresu e-mail.</span><span class="sxs-lookup"><span data-stu-id="ea836-132">You can use a fictitious email address.</span></span> <span data-ttu-id="ea836-133">Podczas przesyłania na stronie wyświetlany jest następujący błąd:</span><span class="sxs-lookup"><span data-stu-id="ea836-133">When you submit, the page displays the following error:</span></span>

    <span data-ttu-id="ea836-134">*"Wewnętrzny błąd serwera: Operacja bazy danych nie powiodła się podczas przetwarzania żądania. Wyjątek SQL: Nie można otworzyć bazy danych. Zastosowanie istniejących migracji dla kontekstu bazy danych aplikacji może rozwiązać ten problem."*</span><span class="sxs-lookup"><span data-stu-id="ea836-134">*"Internal Server Error: A database operation failed while processing the request. SQL exception: Cannot open the database. Applying existing migrations for Application DB context may resolve this issue."*</span></span>
* <span data-ttu-id="ea836-135">Wybierz **pozycję Zastosuj migracje,** a po zaktualizowaniu strony odśwież stronę.</span><span class="sxs-lookup"><span data-stu-id="ea836-135">Select **Apply Migrations** and, once the page updates, refresh the page.</span></span>

![Wewnętrzny błąd serwera: Operacja bazy danych nie powiodła się podczas przetwarzania żądania.](publish-to-azure-webapp-using-vs/_static/mig.png)

<span data-ttu-id="ea836-139">Aplikacja wyświetla wiadomość e-mail służącą do rejestracji nowego użytkownika i łącze **Wyloguj.**</span><span class="sxs-lookup"><span data-stu-id="ea836-139">The app displays the email used to register the new user and a **Log out** link.</span></span>

![Aplikacja sieci Web otwarta w programie Microsoft Edge.](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a><span data-ttu-id="ea836-142">Wdrażanie aplikacji na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="ea836-142">Deploy the app to Azure</span></span>

<span data-ttu-id="ea836-143">Kliknij prawym przyciskiem myszy projekt w Eksploratorze rozwiązań i wybierz pozycję **Publikuj...**.</span><span class="sxs-lookup"><span data-stu-id="ea836-143">Right-click on the project in Solution Explorer and select **Publish...**.</span></span>

![Menu kontekstowe otwarte z wyróżnionym łączem Publikowania](publish-to-azure-webapp-using-vs/_static/pub.png)

<span data-ttu-id="ea836-145">W oknie dialogowym **Publikowanie:**</span><span class="sxs-lookup"><span data-stu-id="ea836-145">In the **Publish** dialog:</span></span>

* <span data-ttu-id="ea836-146">Wybierz usługę **Microsoft Azure App Service**.</span><span class="sxs-lookup"><span data-stu-id="ea836-146">Select **Microsoft Azure App Service**.</span></span>
* <span data-ttu-id="ea836-147">Wybierz ikonę koła zębatego, a następnie wybierz pozycję **Utwórz profil**.</span><span class="sxs-lookup"><span data-stu-id="ea836-147">Select the gear icon and then select **Create Profile**.</span></span>
* <span data-ttu-id="ea836-148">Wybierz **pozycję Utwórz profil**.</span><span class="sxs-lookup"><span data-stu-id="ea836-148">Select **Create Profile**.</span></span>

![Okno dialogowe Publikowania](publish-to-azure-webapp-using-vs/_static/maas1.png)

### <a name="create-azure-resources"></a><span data-ttu-id="ea836-150">Tworzenie zasobów platformy Azure</span><span class="sxs-lookup"><span data-stu-id="ea836-150">Create Azure resources</span></span>

<span data-ttu-id="ea836-151">Zostanie wyświetlone okno dialogowe **Tworzenie usługi app service:**</span><span class="sxs-lookup"><span data-stu-id="ea836-151">The **Create App Service** dialog appears:</span></span>

* <span data-ttu-id="ea836-152">Wprowadź subskrypcję.</span><span class="sxs-lookup"><span data-stu-id="ea836-152">Enter your subscription.</span></span>
* <span data-ttu-id="ea836-153">Pola **wpisu Nazwa aplikacji,** **Grupa zasobów**i Plan usługi **aplikacji** są wypełniane.</span><span class="sxs-lookup"><span data-stu-id="ea836-153">The **App Name**, **Resource Group**, and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="ea836-154">Możesz zachować te nazwy lub je zmienić.</span><span class="sxs-lookup"><span data-stu-id="ea836-154">You can keep these names or change them.</span></span>

![Okno dialogowe Usługa aplikacji](publish-to-azure-webapp-using-vs/_static/newrg1.png)

* <span data-ttu-id="ea836-156">Wybierz kartę **Usługi,** aby utworzyć nową bazę danych.</span><span class="sxs-lookup"><span data-stu-id="ea836-156">Select the **Services** tab to create a new database.</span></span>

* <span data-ttu-id="ea836-157">Wybierz zieloną **+** ikonę, aby utworzyć nową bazę danych SQL</span><span class="sxs-lookup"><span data-stu-id="ea836-157">Select the green **+** icon to create a new SQL Database</span></span>

![Nowa baza danych SQL Database](publish-to-azure-webapp-using-vs/_static/sql.png)

* <span data-ttu-id="ea836-159">Wybierz **pozycję Nowy...** w oknie dialogowym **Konfigurowanie bazy danych SQL,** aby utworzyć nową bazę danych.</span><span class="sxs-lookup"><span data-stu-id="ea836-159">Select **New...** on the **Configure SQL Database** dialog to create a new database.</span></span>

![Nowa baza danych i serwer SQL](publish-to-azure-webapp-using-vs/_static/conf.png)

<span data-ttu-id="ea836-161">Zostanie wyświetlone okno dialogowe **Konfiguruj program SQL Server.**</span><span class="sxs-lookup"><span data-stu-id="ea836-161">The **Configure SQL Server** dialog appears.</span></span>

* <span data-ttu-id="ea836-162">Wprowadź nazwę użytkownika i hasło administratora, a następnie wybierz **przycisk OK**.</span><span class="sxs-lookup"><span data-stu-id="ea836-162">Enter an administrator user name and password, and then select **OK**.</span></span> <span data-ttu-id="ea836-163">Można zachować domyślną **nazwę serwera**.</span><span class="sxs-lookup"><span data-stu-id="ea836-163">You can keep the default **Server Name**.</span></span> 

> [!NOTE]
> <span data-ttu-id="ea836-164">"admin" nie jest dozwolony jako nazwa użytkownika administratora.</span><span class="sxs-lookup"><span data-stu-id="ea836-164">"admin" isn't allowed as the administrator user name.</span></span>

![Okno dialogowe Konfigurowanie programu SQL Server](publish-to-azure-webapp-using-vs/_static/conf_servername.png)

* <span data-ttu-id="ea836-166">Kliknij przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="ea836-166">Select **OK**.</span></span>

<span data-ttu-id="ea836-167">Visual Studio powraca do okna dialogowego **Tworzenie usługi aplikacji.**</span><span class="sxs-lookup"><span data-stu-id="ea836-167">Visual Studio returns to the **Create App Service** dialog.</span></span>

* <span data-ttu-id="ea836-168">Wybierz **pozycję Utwórz** w oknie dialogowym **Tworzenie usługi app service.**</span><span class="sxs-lookup"><span data-stu-id="ea836-168">Select **Create** on the **Create App Service** dialog.</span></span>

![Okno dialogowe Konfigurowanie bazy danych SQL](publish-to-azure-webapp-using-vs/_static/conf_final.png)

<span data-ttu-id="ea836-170">Program Visual Studio tworzy aplikację sieci Web i program SQL Server na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="ea836-170">Visual Studio creates the Web app and SQL Server on Azure.</span></span> <span data-ttu-id="ea836-171">Ten krok może potrwać kilka minut.</span><span class="sxs-lookup"><span data-stu-id="ea836-171">This step can take a few minutes.</span></span> <span data-ttu-id="ea836-172">Aby uzyskać informacje na temat utworzonych zasobów, zobacz [Dodatkowe zasoby](#additional-resources).</span><span class="sxs-lookup"><span data-stu-id="ea836-172">For information on the resources created, see [Additional resources](#additional-resources).</span></span>

<span data-ttu-id="ea836-173">Po zakończeniu wdrażania wybierz **pozycję Ustawienia:**</span><span class="sxs-lookup"><span data-stu-id="ea836-173">When deployment completes, select **Settings**:</span></span>

![Okno dialogowe Konfigurowanie programu SQL Server](publish-to-azure-webapp-using-vs/_static/set.png)

<span data-ttu-id="ea836-175">Na stronie **Ustawienia** okna dialogowego **Publikowanie:**</span><span class="sxs-lookup"><span data-stu-id="ea836-175">On the **Settings** page of the **Publish** dialog:</span></span>

* <span data-ttu-id="ea836-176">Rozwiń **pozycję Bazy danych** i zaznacz pole Wyboru Użyj tego ciągu połączenia w czasie **wykonywania**.</span><span class="sxs-lookup"><span data-stu-id="ea836-176">Expand **Databases** and check **Use this connection string at runtime**.</span></span>
* <span data-ttu-id="ea836-177">Rozwiń **migracje platformowe i** zaznacz pole **Wyboru Zastosuj tę migrację podczas publikowania**.</span><span class="sxs-lookup"><span data-stu-id="ea836-177">Expand **Entity Framework Migrations** and check **Apply this migration on publish**.</span></span>

* <span data-ttu-id="ea836-178">Wybierz **pozycję Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="ea836-178">Select **Save**.</span></span> <span data-ttu-id="ea836-179">Visual Studio powraca do okna dialogowego **Publikowania.**</span><span class="sxs-lookup"><span data-stu-id="ea836-179">Visual Studio returns to the **Publish** dialog.</span></span> 

![Okno dialogowe Publikowanie: Panel Ustawienia](publish-to-azure-webapp-using-vs/_static/pubs.png)

<span data-ttu-id="ea836-181">Kliknij przycisk **Opublikuj**.</span><span class="sxs-lookup"><span data-stu-id="ea836-181">Click **Publish**.</span></span> <span data-ttu-id="ea836-182">Program Visual Studio publikuje aplikację na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="ea836-182">Visual Studio publishes your app to Azure.</span></span> <span data-ttu-id="ea836-183">Po zakończeniu wdrażania aplikacja jest otwierana w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="ea836-183">When the deployment completes, the app is opened in a browser.</span></span>

### <a name="test-your-app-in-azure"></a><span data-ttu-id="ea836-184">Testowanie aplikacji na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="ea836-184">Test your app in Azure</span></span>

* <span data-ttu-id="ea836-185">Testowanie łączy **Informacje** i **Kontakt**</span><span class="sxs-lookup"><span data-stu-id="ea836-185">Test the **About** and **Contact** links</span></span>

* <span data-ttu-id="ea836-186">Zarejestruj nowego użytkownika</span><span class="sxs-lookup"><span data-stu-id="ea836-186">Register a new user</span></span>

![Aplikacja sieci Web otwarta w przeglądarce Microsoft Edge w usłudze Azure App Service](publish-to-azure-webapp-using-vs/_static/register.png)

### <a name="update-the-app"></a><span data-ttu-id="ea836-188">Aktualizowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="ea836-188">Update the app</span></span>

* <span data-ttu-id="ea836-189">Edytuj stronę *Pages/About.cshtml* Razor i zmień jej zawartość.</span><span class="sxs-lookup"><span data-stu-id="ea836-189">Edit the *Pages/About.cshtml* Razor page and change its contents.</span></span> <span data-ttu-id="ea836-190">Na przykład można zmodyfikować akapit, aby powiedzieć "Hello ASP.NET Core!":</span><span class="sxs-lookup"><span data-stu-id="ea836-190">For example, you can modify the paragraph to say "Hello ASP.NET Core!":</span></span>

    [!code-html[About](publish-to-azure-webapp-using-vs/sample/about.cshtml?highlight=9&range=1-9)]

* <span data-ttu-id="ea836-191">Kliknij prawym przyciskiem myszy projekt i wybierz ponownie **pozycję Publikuj.**</span><span class="sxs-lookup"><span data-stu-id="ea836-191">Right-click on the project and select **Publish...** again.</span></span>

![Menu kontekstowe otwarte z wyróżnionym łączem Publikowania](publish-to-azure-webapp-using-vs/_static/pub.png)

* <span data-ttu-id="ea836-193">Po opublikowaniu aplikacji sprawdź, czy wprowadzone zmiany są dostępne na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="ea836-193">After the app is published, verify the changes you made are available on Azure.</span></span>

![Sprawdź, czy zadanie jest ukończone](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a><span data-ttu-id="ea836-195">Czyszczenie</span><span class="sxs-lookup"><span data-stu-id="ea836-195">Clean up</span></span>

<span data-ttu-id="ea836-196">Po zakończeniu testowania aplikacji przejdź do [witryny Azure portal](https://portal.azure.com/) i usuń aplikację.</span><span class="sxs-lookup"><span data-stu-id="ea836-196">When you have finished testing the app, go to the [Azure portal](https://portal.azure.com/) and delete the app.</span></span>

* <span data-ttu-id="ea836-197">Wybierz **pozycję Grupy zasobów**, a następnie wybierz utworzoną grupę zasobów.</span><span class="sxs-lookup"><span data-stu-id="ea836-197">Select **Resource groups**, then select the resource group you created.</span></span>

![Usługa Azure Portal: grupy zasobów w menu paska bocznego](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* <span data-ttu-id="ea836-199">Na stronie **Grupy zasobów** wybierz pozycję **Usuń**.</span><span class="sxs-lookup"><span data-stu-id="ea836-199">In the **Resource groups** page, select **Delete**.</span></span>

![Strona Azure Portal: Grupy zasobów](publish-to-azure-webapp-using-vs/_static/rgd.png)

* <span data-ttu-id="ea836-201">Wprowadź nazwę grupy zasobów i wybierz pozycję **Usuń**.</span><span class="sxs-lookup"><span data-stu-id="ea836-201">Enter the name of the resource group and select **Delete**.</span></span> <span data-ttu-id="ea836-202">Aplikacja i wszystkie inne zasoby utworzone w tym samouczku są teraz usuwane z platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="ea836-202">Your app and all other resources created in this tutorial are now deleted from Azure.</span></span>

### <a name="next-steps"></a><span data-ttu-id="ea836-203">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="ea836-203">Next steps</span></span>

* <xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a><span data-ttu-id="ea836-204">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="ea836-204">Additional resources</span></span>

* <span data-ttu-id="ea836-205">W polu Visual Studio Code zobacz [Publikowanie profili](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).</span><span class="sxs-lookup"><span data-stu-id="ea836-205">For Visual Studio Code, see [Publish profiles](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).</span></span>
* [<span data-ttu-id="ea836-206">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="ea836-206">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="ea836-207">Grupy zasobów platformy Azure</span><span class="sxs-lookup"><span data-stu-id="ea836-207">Azure resource groups</span></span>](/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [<span data-ttu-id="ea836-208">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="ea836-208">Azure SQL Database</span></span>](/azure/sql-database/)
* <xref:host-and-deploy/visual-studio-publish-profiles>
* <xref:test/troubleshoot-azure-iis>
