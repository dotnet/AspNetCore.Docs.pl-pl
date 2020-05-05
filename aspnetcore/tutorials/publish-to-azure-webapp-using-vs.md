---
title: Publikowanie aplikacji ASP.NET Core na platformie Azure przy użyciu programu Visual Studio
author: rick-anderson
description: Dowiedz się, jak opublikować aplikację ASP.NET Core, aby Azure App Service przy użyciu programu Visual Studio.
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: 634fb821ef0478dbcf57fdbb991a2e8bbb9402f1
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777088"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a><span data-ttu-id="492d5-103">Publikowanie aplikacji ASP.NET Core na platformie Azure przy użyciu programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="492d5-103">Publish an ASP.NET Core app to Azure with Visual Studio</span></span>

<span data-ttu-id="492d5-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="492d5-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>
::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

::: moniker-end


<span data-ttu-id="492d5-105">Zobacz [publikowanie aplikacji sieci Web, aby Azure App Service przy użyciu Visual Studio dla komputerów Mac](https://docs.microsoft.com/visualstudio/mac/publish-app-svc?view=vsmac-2019) , jeśli pracujesz na macOS.</span><span class="sxs-lookup"><span data-stu-id="492d5-105">See [Publish a Web app to Azure App Service using Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/publish-app-svc?view=vsmac-2019) if you are working on macOS.</span></span>

<span data-ttu-id="492d5-106">Aby rozwiązać problem z wdrożeniem App Service, <xref:test/troubleshoot-azure-iis>Zobacz.</span><span class="sxs-lookup"><span data-stu-id="492d5-106">To troubleshoot an App Service deployment issue, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="set-up"></a><span data-ttu-id="492d5-107">Konfigurowanie</span><span class="sxs-lookup"><span data-stu-id="492d5-107">Set up</span></span>

* <span data-ttu-id="492d5-108">Otwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/dotnet/) , jeśli go nie masz.</span><span class="sxs-lookup"><span data-stu-id="492d5-108">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span> 

## <a name="create-a-web-app"></a><span data-ttu-id="492d5-109">Tworzenie aplikacji internetowej</span><span class="sxs-lookup"><span data-stu-id="492d5-109">Create a web app</span></span>

<span data-ttu-id="492d5-110">Na stronie startowej programu Visual Studio wybierz pozycję **plik > nowy > projekt...**</span><span class="sxs-lookup"><span data-stu-id="492d5-110">In the Visual Studio Start Page, select **File > New > Project...**</span></span>

![Menu Plik](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

<span data-ttu-id="492d5-112">Wypełnij okno dialogowe **Nowy projekt** :</span><span class="sxs-lookup"><span data-stu-id="492d5-112">Complete the **New Project** dialog:</span></span>

* <span data-ttu-id="492d5-113">W lewym okienku wybierz pozycję **.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="492d5-113">In the left pane, select **.NET Core**.</span></span>
* <span data-ttu-id="492d5-114">W środkowym okienku wybierz pozycję **aplikacja sieci Web ASP.NET Core**.</span><span class="sxs-lookup"><span data-stu-id="492d5-114">In the center pane, select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="492d5-115">Wybierz przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="492d5-115">Select **OK**.</span></span>

![Okno dialogowe Nowy projekt](publish-to-azure-webapp-using-vs/_static/new_prj.png)

<span data-ttu-id="492d5-117">W oknie dialogowym **Nowa aplikacja sieci Web ASP.NET Core** :</span><span class="sxs-lookup"><span data-stu-id="492d5-117">In the **New ASP.NET Core Web Application** dialog:</span></span>

* <span data-ttu-id="492d5-118">Wybierz pozycję **aplikacja sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="492d5-118">Select **Web Application**.</span></span>
* <span data-ttu-id="492d5-119">Wybierz pozycję **Zmień uwierzytelnianie**.</span><span class="sxs-lookup"><span data-stu-id="492d5-119">Select **Change Authentication**.</span></span>

![Okno dialogowe Nowy projekt](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

<span data-ttu-id="492d5-121">Zostanie wyświetlone okno dialogowe **Zmienianie uwierzytelniania** .</span><span class="sxs-lookup"><span data-stu-id="492d5-121">The **Change Authentication** dialog appears.</span></span> 

* <span data-ttu-id="492d5-122">Wybierz pozycję **indywidualne konta użytkowników**.</span><span class="sxs-lookup"><span data-stu-id="492d5-122">Select **Individual User Accounts**.</span></span>
* <span data-ttu-id="492d5-123">Wybierz **przycisk OK** , aby powrócić do **nowej aplikacji sieci Web ASP.NET Core**, a następnie ponownie wybierz przycisk **OK** .</span><span class="sxs-lookup"><span data-stu-id="492d5-123">Select **OK** to return to the **New ASP.NET Core Web Application**, then select **OK** again.</span></span>

![Nowe okno dialogowe uwierzytelniania w sieci Web ASP.NET Core](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

<span data-ttu-id="492d5-125">Program Visual Studio tworzy rozwiązanie.</span><span class="sxs-lookup"><span data-stu-id="492d5-125">Visual Studio creates the solution.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="492d5-126">Uruchomienie aplikacji</span><span class="sxs-lookup"><span data-stu-id="492d5-126">Run the app</span></span>

* <span data-ttu-id="492d5-127">Naciśnij klawisze CTRL + F5, aby uruchomić projekt.</span><span class="sxs-lookup"><span data-stu-id="492d5-127">Press CTRL+F5 to run the project.</span></span>
* <span data-ttu-id="492d5-128">Przetestuj linki **informacje** i **kontakt** .</span><span class="sxs-lookup"><span data-stu-id="492d5-128">Test the **About** and **Contact** links.</span></span>

![Aplikacja sieci Web otwarta w przeglądarce Microsoft Edge na hoście lokalnym](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a><span data-ttu-id="492d5-130">Rejestrowanie użytkownika</span><span class="sxs-lookup"><span data-stu-id="492d5-130">Register a user</span></span>

* <span data-ttu-id="492d5-131">Wybierz pozycję **zarejestruj** i Zarejestruj nowego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="492d5-131">Select **Register** and register a new user.</span></span> <span data-ttu-id="492d5-132">Możesz użyć fikcyjnego adresu e-mail.</span><span class="sxs-lookup"><span data-stu-id="492d5-132">You can use a fictitious email address.</span></span> <span data-ttu-id="492d5-133">Gdy przesyłasz, na stronie jest wyświetlany następujący błąd:</span><span class="sxs-lookup"><span data-stu-id="492d5-133">When you submit, the page displays the following error:</span></span>

    <span data-ttu-id="492d5-134">*"Wewnętrzny błąd serwera: operacja bazy danych nie powiodła się podczas przetwarzania żądania. Wyjątek SQL: nie można otworzyć bazy danych. Zastosowanie istniejących migracji dla kontekstu bazy danych aplikacji może rozwiązać ten problem.*</span><span class="sxs-lookup"><span data-stu-id="492d5-134">*"Internal Server Error: A database operation failed while processing the request. SQL exception: Cannot open the database. Applying existing migrations for Application DB context may resolve this issue."*</span></span>
* <span data-ttu-id="492d5-135">Wybierz pozycję **Zastosuj migracje** , a po aktualizacji strony Odśwież stronę.</span><span class="sxs-lookup"><span data-stu-id="492d5-135">Select **Apply Migrations** and, once the page updates, refresh the page.</span></span>

![Wewnętrzny błąd serwera: operacja bazy danych nie powiodła się podczas przetwarzania żądania.](publish-to-azure-webapp-using-vs/_static/mig.png)

<span data-ttu-id="492d5-139">W aplikacji zostanie wyświetlona wiadomość e-mail służąca do zarejestrowania nowego użytkownika i linku **wylogowywania** .</span><span class="sxs-lookup"><span data-stu-id="492d5-139">The app displays the email used to register the new user and a **Log out** link.</span></span>

![Aplikacja sieci Web otwarta w przeglądarce Microsoft Edge.](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a><span data-ttu-id="492d5-142">Wdrażanie aplikacji na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="492d5-142">Deploy the app to Azure</span></span>

<span data-ttu-id="492d5-143">Kliknij prawym przyciskiem myszy projekt w Eksplorator rozwiązań i wybierz pozycję **Publikuj...**.</span><span class="sxs-lookup"><span data-stu-id="492d5-143">Right-click on the project in Solution Explorer and select **Publish...**.</span></span>

![Menu kontekstowe otwierane z wyróżnionym linkiem publikowania](publish-to-azure-webapp-using-vs/_static/pub.png)

<span data-ttu-id="492d5-145">W oknie dialogowym **publikowania** :</span><span class="sxs-lookup"><span data-stu-id="492d5-145">In the **Publish** dialog:</span></span>

* <span data-ttu-id="492d5-146">Wybierz **App Service Microsoft Azure**.</span><span class="sxs-lookup"><span data-stu-id="492d5-146">Select **Microsoft Azure App Service**.</span></span>
* <span data-ttu-id="492d5-147">Wybierz ikonę koła zębatego, a następnie wybierz pozycję **Utwórz profil**.</span><span class="sxs-lookup"><span data-stu-id="492d5-147">Select the gear icon and then select **Create Profile**.</span></span>
* <span data-ttu-id="492d5-148">Wybierz pozycję **Utwórz profil**.</span><span class="sxs-lookup"><span data-stu-id="492d5-148">Select **Create Profile**.</span></span>

![Okno dialogowe publikowania](publish-to-azure-webapp-using-vs/_static/maas1.png)

### <a name="create-azure-resources"></a><span data-ttu-id="492d5-150">Tworzenie zasobów platformy Azure</span><span class="sxs-lookup"><span data-stu-id="492d5-150">Create Azure resources</span></span>

<span data-ttu-id="492d5-151">Zostanie wyświetlone okno dialogowe **tworzenie App Service** :</span><span class="sxs-lookup"><span data-stu-id="492d5-151">The **Create App Service** dialog appears:</span></span>

* <span data-ttu-id="492d5-152">Wprowadź swoją subskrypcję.</span><span class="sxs-lookup"><span data-stu-id="492d5-152">Enter your subscription.</span></span>
* <span data-ttu-id="492d5-153">Pola **Nazwa aplikacji**, **Grupa zasobów**i zapis **planu App Service** są wypełniane.</span><span class="sxs-lookup"><span data-stu-id="492d5-153">The **App Name**, **Resource Group**, and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="492d5-154">Te nazwy można zachować lub zmienić.</span><span class="sxs-lookup"><span data-stu-id="492d5-154">You can keep these names or change them.</span></span>

![Okno dialogowe App Service](publish-to-azure-webapp-using-vs/_static/newrg1.png)

* <span data-ttu-id="492d5-156">Wybierz kartę **usługi** , aby utworzyć nową bazę danych.</span><span class="sxs-lookup"><span data-stu-id="492d5-156">Select the **Services** tab to create a new database.</span></span>

* <span data-ttu-id="492d5-157">Wybierz zieloną **+** ikonę, aby utworzyć nowy SQL Database</span><span class="sxs-lookup"><span data-stu-id="492d5-157">Select the green **+** icon to create a new SQL Database</span></span>

![Nowa baza danych SQL Database](publish-to-azure-webapp-using-vs/_static/sql.png)

* <span data-ttu-id="492d5-159">Wybierz pozycję **Nowy...** w oknie dialogowym **Konfigurowanie SQL Database** , aby utworzyć nową bazę danych.</span><span class="sxs-lookup"><span data-stu-id="492d5-159">Select **New...** on the **Configure SQL Database** dialog to create a new database.</span></span>

![Nowe SQL Database i serwer](publish-to-azure-webapp-using-vs/_static/conf.png)

<span data-ttu-id="492d5-161">Zostanie wyświetlone okno dialogowe **konfigurowanie SQL Server** .</span><span class="sxs-lookup"><span data-stu-id="492d5-161">The **Configure SQL Server** dialog appears.</span></span>

* <span data-ttu-id="492d5-162">Wprowadź nazwę użytkownika i hasło administratora, a następnie wybierz przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="492d5-162">Enter an administrator user name and password, and then select **OK**.</span></span> <span data-ttu-id="492d5-163">Można zachować domyślną **nazwę serwera**.</span><span class="sxs-lookup"><span data-stu-id="492d5-163">You can keep the default **Server Name**.</span></span> 

> [!NOTE]
> <span data-ttu-id="492d5-164">"Administrator" nie jest dozwolony jako nazwa użytkownika administratora.</span><span class="sxs-lookup"><span data-stu-id="492d5-164">"admin" isn't allowed as the administrator user name.</span></span>

![Skonfiguruj okno dialogowe SQL Server](publish-to-azure-webapp-using-vs/_static/conf_servername.png)

* <span data-ttu-id="492d5-166">Wybierz przycisk **OK**.</span><span class="sxs-lookup"><span data-stu-id="492d5-166">Select **OK**.</span></span>

<span data-ttu-id="492d5-167">Program Visual Studio powróci do okna dialogowego **tworzenie App Service** .</span><span class="sxs-lookup"><span data-stu-id="492d5-167">Visual Studio returns to the **Create App Service** dialog.</span></span>

* <span data-ttu-id="492d5-168">Wybierz pozycję **Utwórz** w oknie dialogowym **Tworzenie App Service** .</span><span class="sxs-lookup"><span data-stu-id="492d5-168">Select **Create** on the **Create App Service** dialog.</span></span>

![Skonfiguruj okno dialogowe SQL Database](publish-to-azure-webapp-using-vs/_static/conf_final.png)

<span data-ttu-id="492d5-170">Program Visual Studio utworzy aplikację sieci Web i SQL Server na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="492d5-170">Visual Studio creates the Web app and SQL Server on Azure.</span></span> <span data-ttu-id="492d5-171">Ten krok może potrwać kilka minut.</span><span class="sxs-lookup"><span data-stu-id="492d5-171">This step can take a few minutes.</span></span> <span data-ttu-id="492d5-172">Aby uzyskać informacje o utworzonych zasobach, zobacz [dodatkowe zasoby](#additional-resources).</span><span class="sxs-lookup"><span data-stu-id="492d5-172">For information on the resources created, see [Additional resources](#additional-resources).</span></span>

<span data-ttu-id="492d5-173">Po zakończeniu wdrażania wybierz pozycję **Ustawienia**:</span><span class="sxs-lookup"><span data-stu-id="492d5-173">When deployment completes, select **Settings**:</span></span>

![Skonfiguruj okno dialogowe SQL Server](publish-to-azure-webapp-using-vs/_static/set.png)

<span data-ttu-id="492d5-175">Na stronie **Ustawienia** okna dialogowego **Publikowanie** :</span><span class="sxs-lookup"><span data-stu-id="492d5-175">On the **Settings** page of the **Publish** dialog:</span></span>

* <span data-ttu-id="492d5-176">Rozwiń węzeł **bazy danych** i zaznacz pole wyboru **Użyj tych parametrów połączenia w czasie wykonywania**.</span><span class="sxs-lookup"><span data-stu-id="492d5-176">Expand **Databases** and check **Use this connection string at runtime**.</span></span>
* <span data-ttu-id="492d5-177">Rozwiń węzeł **Entity Framework migracje** i zaznacz opcję **Zastosuj tę migrację przy publikowaniu**.</span><span class="sxs-lookup"><span data-stu-id="492d5-177">Expand **Entity Framework Migrations** and check **Apply this migration on publish**.</span></span>

* <span data-ttu-id="492d5-178">Wybierz pozycję **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="492d5-178">Select **Save**.</span></span> <span data-ttu-id="492d5-179">Program Visual Studio wraca do okna dialogowego **Publikowanie** .</span><span class="sxs-lookup"><span data-stu-id="492d5-179">Visual Studio returns to the **Publish** dialog.</span></span> 

![Okno dialogowe publikowania: Panel ustawień](publish-to-azure-webapp-using-vs/_static/pubs.png)

<span data-ttu-id="492d5-181">Kliknij przycisk **Opublikuj**.</span><span class="sxs-lookup"><span data-stu-id="492d5-181">Click **Publish**.</span></span> <span data-ttu-id="492d5-182">Program Visual Studio publikuje aplikację na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="492d5-182">Visual Studio publishes your app to Azure.</span></span> <span data-ttu-id="492d5-183">Po zakończeniu wdrażania aplikacja zostanie otwarta w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="492d5-183">When the deployment completes, the app is opened in a browser.</span></span>

### <a name="test-your-app-in-azure"></a><span data-ttu-id="492d5-184">Testowanie aplikacji na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="492d5-184">Test your app in Azure</span></span>

* <span data-ttu-id="492d5-185">Testowanie linków **about** i **Contact**</span><span class="sxs-lookup"><span data-stu-id="492d5-185">Test the **About** and **Contact** links</span></span>

* <span data-ttu-id="492d5-186">Rejestrowanie nowego użytkownika</span><span class="sxs-lookup"><span data-stu-id="492d5-186">Register a new user</span></span>

![Aplikacja sieci Web otwarta w przeglądarce Microsoft Edge na Azure App Service](publish-to-azure-webapp-using-vs/_static/register.png)

### <a name="update-the-app"></a><span data-ttu-id="492d5-188">Aktualizowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="492d5-188">Update the app</span></span>

* <span data-ttu-id="492d5-189">Edytuj stronę *strony/informacje. cshtml* Razor i zmień jej zawartość.</span><span class="sxs-lookup"><span data-stu-id="492d5-189">Edit the *Pages/About.cshtml* Razor page and change its contents.</span></span> <span data-ttu-id="492d5-190">Na przykład można zmodyfikować akapit, aby powiedzieć "Hello ASP.NET Core!":</span><span class="sxs-lookup"><span data-stu-id="492d5-190">For example, you can modify the paragraph to say "Hello ASP.NET Core!":</span></span>

    [!code-html[About](publish-to-azure-webapp-using-vs/sample/about.cshtml?highlight=9&range=1-9)]

* <span data-ttu-id="492d5-191">Kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Opublikuj...** ponownie.</span><span class="sxs-lookup"><span data-stu-id="492d5-191">Right-click on the project and select **Publish...** again.</span></span>

![Menu kontekstowe otwierane z wyróżnionym linkiem publikowania](publish-to-azure-webapp-using-vs/_static/pub.png)

* <span data-ttu-id="492d5-193">Po opublikowaniu aplikacji Sprawdź, czy wprowadzone zmiany są dostępne na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="492d5-193">After the app is published, verify the changes you made are available on Azure.</span></span>

![Sprawdzanie, czy zadanie zostało ukończone](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a><span data-ttu-id="492d5-195">Czyszczenie</span><span class="sxs-lookup"><span data-stu-id="492d5-195">Clean up</span></span>

<span data-ttu-id="492d5-196">Po zakończeniu testowania aplikacji przejdź do [Azure Portal](https://portal.azure.com/) i Usuń aplikację.</span><span class="sxs-lookup"><span data-stu-id="492d5-196">When you have finished testing the app, go to the [Azure portal](https://portal.azure.com/) and delete the app.</span></span>

* <span data-ttu-id="492d5-197">Wybierz pozycję **grupy zasobów**, a następnie wybierz utworzoną grupę zasobów.</span><span class="sxs-lookup"><span data-stu-id="492d5-197">Select **Resource groups**, then select the resource group you created.</span></span>

![Witryna Azure Portal: grupy zasobów w menu paska bocznego](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* <span data-ttu-id="492d5-199">Na stronie **grupy zasobów** wybierz pozycję **Usuń**.</span><span class="sxs-lookup"><span data-stu-id="492d5-199">In the **Resource groups** page, select **Delete**.</span></span>

![Witryna Azure Portal: Strona grupy zasobów](publish-to-azure-webapp-using-vs/_static/rgd.png)

* <span data-ttu-id="492d5-201">Wprowadź nazwę grupy zasobów i wybierz pozycję **Usuń**.</span><span class="sxs-lookup"><span data-stu-id="492d5-201">Enter the name of the resource group and select **Delete**.</span></span> <span data-ttu-id="492d5-202">Twoja aplikacja i wszystkie inne zasoby utworzone w tym samouczku zostaną usunięte z platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="492d5-202">Your app and all other resources created in this tutorial are now deleted from Azure.</span></span>

### <a name="next-steps"></a><span data-ttu-id="492d5-203">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="492d5-203">Next steps</span></span>

* <xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a><span data-ttu-id="492d5-204">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="492d5-204">Additional resources</span></span>

* <span data-ttu-id="492d5-205">Aby uzyskać Visual Studio Code, zobacz temat [Publikowanie profilów](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).</span><span class="sxs-lookup"><span data-stu-id="492d5-205">For Visual Studio Code, see [Publish profiles](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).</span></span>
* [<span data-ttu-id="492d5-206">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="492d5-206">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="492d5-207">Grupa zasobów platformy Azure</span><span class="sxs-lookup"><span data-stu-id="492d5-207">Azure resource groups</span></span>](/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [<span data-ttu-id="492d5-208">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="492d5-208">Azure SQL Database</span></span>](/azure/sql-database/)
* <xref:host-and-deploy/visual-studio-publish-profiles>
* <xref:test/troubleshoot-azure-iis>
