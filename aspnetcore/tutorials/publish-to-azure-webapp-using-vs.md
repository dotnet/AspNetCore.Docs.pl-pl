---
<span data-ttu-id="bd386-101">title: Autor: Opis: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="bd386-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bd386-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bd386-102">'Blazor'</span></span>
- <span data-ttu-id="bd386-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bd386-103">'Identity'</span></span>
- <span data-ttu-id="bd386-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bd386-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="bd386-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bd386-105">'Razor'</span></span>
- <span data-ttu-id="bd386-106">SignalRIdentyfikator UID:</span><span class="sxs-lookup"><span data-stu-id="bd386-106">'SignalR' uid:</span></span> 

---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a><span data-ttu-id="bd386-107">Publikowanie aplikacji ASP.NET Core na platformie Azure przy użyciu programu Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd386-107">Publish an ASP.NET Core app to Azure with Visual Studio</span></span>

<span data-ttu-id="bd386-108">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="bd386-108">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>
::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

::: moniker-end


<span data-ttu-id="bd386-109">Zobacz [publikowanie aplikacji sieci Web, aby Azure App Service przy użyciu Visual Studio dla komputerów Mac](https://docs.microsoft.com/visualstudio/mac/publish-app-svc?view=vsmac-2019) , jeśli pracujesz na macOS.</span><span class="sxs-lookup"><span data-stu-id="bd386-109">See [Publish a Web app to Azure App Service using Visual Studio for Mac](https://docs.microsoft.com/visualstudio/mac/publish-app-svc?view=vsmac-2019) if you are working on macOS.</span></span>

<span data-ttu-id="bd386-110">Aby rozwiązać problem z wdrożeniem App Service, zobacz <xref:test/troubleshoot-azure-iis> .</span><span class="sxs-lookup"><span data-stu-id="bd386-110">To troubleshoot an App Service deployment issue, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="set-up"></a><span data-ttu-id="bd386-111">Konfiguruj</span><span class="sxs-lookup"><span data-stu-id="bd386-111">Set up</span></span>

* <span data-ttu-id="bd386-112">Otwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/dotnet/) , jeśli go nie masz.</span><span class="sxs-lookup"><span data-stu-id="bd386-112">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span> 

## <a name="create-a-web-app"></a><span data-ttu-id="bd386-113">Tworzenie aplikacji internetowej</span><span class="sxs-lookup"><span data-stu-id="bd386-113">Create a web app</span></span>

<span data-ttu-id="bd386-114">Na stronie startowej programu Visual Studio wybierz pozycję **plik > nowy > projekt...**</span><span class="sxs-lookup"><span data-stu-id="bd386-114">In the Visual Studio Start Page, select **File > New > Project...**</span></span>

![Menu Plik](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

<span data-ttu-id="bd386-116">Wypełnij okno dialogowe **Nowy projekt** :</span><span class="sxs-lookup"><span data-stu-id="bd386-116">Complete the **New Project** dialog:</span></span>

* <span data-ttu-id="bd386-117">Wybierz **ASP.NET Core aplikacji sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="bd386-117">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="bd386-118">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="bd386-118">Select **Next**.</span></span>

![Okno dialogowe Nowy projekt](publish-to-azure-webapp-using-vs/_static/new_prj.png)

<span data-ttu-id="bd386-120">W oknie dialogowym **Nowa aplikacja sieci Web ASP.NET Core** :</span><span class="sxs-lookup"><span data-stu-id="bd386-120">In the **New ASP.NET Core Web Application** dialog:</span></span>

* <span data-ttu-id="bd386-121">Wybierz pozycję **aplikacja sieci Web**.</span><span class="sxs-lookup"><span data-stu-id="bd386-121">Select **Web Application**.</span></span>
* <span data-ttu-id="bd386-122">Wybierz pozycję **Zmień** w obszarze Uwierzytelnianie.</span><span class="sxs-lookup"><span data-stu-id="bd386-122">Select **Change** under Authentication.</span></span>

![Nowe okno dialogowe sieci Web ASP.NET Core](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

<span data-ttu-id="bd386-124">Zostanie wyświetlone okno dialogowe **Zmienianie uwierzytelniania** .</span><span class="sxs-lookup"><span data-stu-id="bd386-124">The **Change Authentication** dialog appears.</span></span> 

* <span data-ttu-id="bd386-125">Wybierz pozycję **indywidualne konta użytkowników**.</span><span class="sxs-lookup"><span data-stu-id="bd386-125">Select **Individual User Accounts**.</span></span>
* <span data-ttu-id="bd386-126">Wybierz **przycisk OK** , aby powrócić do **nowej aplikacji sieci Web ASP.NET Core**, a następnie wybierz pozycję **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="bd386-126">Select **OK** to return to the **New ASP.NET Core Web Application**, then select **Create**.</span></span>

![Nowe okno dialogowe uwierzytelniania w sieci Web ASP.NET Core](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

<span data-ttu-id="bd386-128">Program Visual Studio tworzy rozwiązanie.</span><span class="sxs-lookup"><span data-stu-id="bd386-128">Visual Studio creates the solution.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="bd386-129">Uruchomienie aplikacji</span><span class="sxs-lookup"><span data-stu-id="bd386-129">Run the app</span></span>

* <span data-ttu-id="bd386-130">Naciśnij klawisze CTRL + F5, aby uruchomić projekt.</span><span class="sxs-lookup"><span data-stu-id="bd386-130">Press CTRL+F5 to run the project.</span></span>
* <span data-ttu-id="bd386-131">Przetestuj łącze **prywatność** .</span><span class="sxs-lookup"><span data-stu-id="bd386-131">Test the **Privacy** link.</span></span>

![Aplikacja sieci Web otwarta w przeglądarce Microsoft Edge na hoście lokalnym](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a><span data-ttu-id="bd386-133">Rejestrowanie użytkownika</span><span class="sxs-lookup"><span data-stu-id="bd386-133">Register a user</span></span>

* <span data-ttu-id="bd386-134">Wybierz pozycję **zarejestruj** i Zarejestruj nowego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="bd386-134">Select **Register** and register a new user.</span></span> <span data-ttu-id="bd386-135">Możesz użyć fikcyjnego adresu e-mail.</span><span class="sxs-lookup"><span data-stu-id="bd386-135">You can use a fictitious email address.</span></span> <span data-ttu-id="bd386-136">Gdy przesyłasz, na stronie jest wyświetlany następujący błąd:</span><span class="sxs-lookup"><span data-stu-id="bd386-136">When you submit, the page displays the following error:</span></span>

    <span data-ttu-id="bd386-137">*"Operacja bazy danych nie powiodła się podczas przetwarzania żądania. Zastosowanie istniejących migracji dla kontekstu bazy danych aplikacji może rozwiązać ten problem.*</span><span class="sxs-lookup"><span data-stu-id="bd386-137">*"A database operation failed while processing the request. Applying existing migrations for Application DB context may resolve this issue."*</span></span>
* <span data-ttu-id="bd386-138">Wybierz pozycję **Zastosuj migracje** , a po aktualizacji strony Odśwież stronę.</span><span class="sxs-lookup"><span data-stu-id="bd386-138">Select **Apply Migrations** and, once the page updates, refresh the page.</span></span>

![Operacja bazy danych nie powiodła się podczas przetwarzania żądania.](publish-to-azure-webapp-using-vs/_static/mig.png)

<span data-ttu-id="bd386-141">Aplikacja wyświetli wiadomość e-mail użytą do zarejestrowania nowego użytkownika i linku do **wylogowania** .</span><span class="sxs-lookup"><span data-stu-id="bd386-141">The app displays the email used to register the new user and a **Logout** link.</span></span>

![Aplikacja sieci Web otwarta w przeglądarce Microsoft Edge.](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a><span data-ttu-id="bd386-144">Wdrażanie aplikacji na platformie Azure</span><span class="sxs-lookup"><span data-stu-id="bd386-144">Deploy the app to Azure</span></span>

<span data-ttu-id="bd386-145">Kliknij prawym przyciskiem myszy projekt w Eksplorator rozwiązań i wybierz pozycję **Publikuj...**.</span><span class="sxs-lookup"><span data-stu-id="bd386-145">Right-click on the project in Solution Explorer and select **Publish...**.</span></span>

![Menu kontekstowe otwierane z wyróżnionym linkiem publikowania](publish-to-azure-webapp-using-vs/_static/pub.png)

<span data-ttu-id="bd386-147">W oknie dialogowym **publikowania** :</span><span class="sxs-lookup"><span data-stu-id="bd386-147">In the **Publish** dialog:</span></span>

* <span data-ttu-id="bd386-148">Wybierz pozycję **Azure**.</span><span class="sxs-lookup"><span data-stu-id="bd386-148">Select **Azure**.</span></span>
* <span data-ttu-id="bd386-149">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="bd386-149">Select **Next**.</span></span>

![Okno dialogowe publikowania](publish-to-azure-webapp-using-vs/_static/maas1.png)

<span data-ttu-id="bd386-151">W oknie dialogowym **publikowania** :</span><span class="sxs-lookup"><span data-stu-id="bd386-151">In the **Publish** dialog:</span></span>

* <span data-ttu-id="bd386-152">Wybierz pozycję **Azure App Service (Linux)**.</span><span class="sxs-lookup"><span data-stu-id="bd386-152">Select **Azure App Service (Linux)**.</span></span>
* <span data-ttu-id="bd386-153">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="bd386-153">Select **Next**.</span></span>

![Okno dialogowe publikowania: Wybieranie usługi platformy Azure](publish-to-azure-webapp-using-vs/_static/maas2.png)

<span data-ttu-id="bd386-155">W oknie dialogowym **Publikowanie** wybierz pozycję **Utwórz nowy Azure App Service...**</span><span class="sxs-lookup"><span data-stu-id="bd386-155">In the **Publish** dialog select **Create a new Azure App Service...**</span></span>

![Okno dialogowe publikowania: wybierz wystąpienie usługi platformy Azure](publish-to-azure-webapp-using-vs/_static/maas3.png)

<span data-ttu-id="bd386-157">Zostanie wyświetlone okno dialogowe **tworzenie App Service** :</span><span class="sxs-lookup"><span data-stu-id="bd386-157">The **Create App Service** dialog appears:</span></span>

* <span data-ttu-id="bd386-158">Pola **Nazwa aplikacji**, **Grupa zasobów**i zapis **planu App Service** są wypełniane.</span><span class="sxs-lookup"><span data-stu-id="bd386-158">The **App Name**, **Resource Group**, and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="bd386-159">Te nazwy można zachować lub zmienić.</span><span class="sxs-lookup"><span data-stu-id="bd386-159">You can keep these names or change them.</span></span>
* <span data-ttu-id="bd386-160">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="bd386-160">Select **Create**.</span></span>

![Okno dialogowe Tworzenie usługi App Service](publish-to-azure-webapp-using-vs/_static/newrg1.png)

<span data-ttu-id="bd386-162">Po zakończeniu tworzenia okno dialogowe zostanie automatycznie zamknięte, a okno dialogowe **publikowania** ponownie przyniesie fokus:</span><span class="sxs-lookup"><span data-stu-id="bd386-162">After creation is completed the dialog is automatically closed and the **Publish** dialog gets focus again:</span></span>

* <span data-ttu-id="bd386-163">Nowo utworzone wystąpienie jest automatycznie wybierane.</span><span class="sxs-lookup"><span data-stu-id="bd386-163">The new instance that was just created is automatically selected.</span></span>
* <span data-ttu-id="bd386-164">Wybierz pozycję **Zakończ**.</span><span class="sxs-lookup"><span data-stu-id="bd386-164">Select **Finish**.</span></span>

![Okno dialogowe publikowania: wybierz wystąpienie App Service](publish-to-azure-webapp-using-vs/_static/select_as.png)

<span data-ttu-id="bd386-166">Następnie zostanie wyświetlona strona **Podsumowanie profilu publikowania** .</span><span class="sxs-lookup"><span data-stu-id="bd386-166">Next you see the **Publish Profile summary** page.</span></span> <span data-ttu-id="bd386-167">Program Visual Studio wykrył, że ta aplikacja wymaga bazy danych SQL Server i prosi o jej konfigurację.</span><span class="sxs-lookup"><span data-stu-id="bd386-167">Visual Studio has detected that this application requires a SQL Server database and it's asking you to configure it.</span></span> <span data-ttu-id="bd386-168">Wybierz pozycję **Konfiguruj**.</span><span class="sxs-lookup"><span data-stu-id="bd386-168">Select **Configure**.</span></span>

![Strona podsumowania profilu publikowania: Konfigurowanie zależności SQL Server](publish-to-azure-webapp-using-vs/_static/sql.png)

<span data-ttu-id="bd386-170">Zostanie wyświetlone okno dialogowe **Konfigurowanie zależności** :</span><span class="sxs-lookup"><span data-stu-id="bd386-170">The **Configure dependency** dialog appears:</span></span>

* <span data-ttu-id="bd386-171">Wybierz **Azure SQL Database**.</span><span class="sxs-lookup"><span data-stu-id="bd386-171">Select **Azure SQL Database**.</span></span>
* <span data-ttu-id="bd386-172">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="bd386-172">Select **Next**.</span></span>

![Konfigurowanie okna dialogowego zależności SQL Server](publish-to-azure-webapp-using-vs/_static/sql1.png)

<span data-ttu-id="bd386-174">W oknie dialogowym **Konfigurowanie usługi Azure SQL Database** wybierz pozycję **Utwórz SQL Database**</span><span class="sxs-lookup"><span data-stu-id="bd386-174">In the **Configure Azure SQL database** dialog select **Create a SQL Database**</span></span>

![Skonfiguruj okno dialogowe Azure SQL Database](publish-to-azure-webapp-using-vs/_static/sql2.png)

<span data-ttu-id="bd386-176">Zostanie wyświetlona **Azure SQL Database tworzenia** :</span><span class="sxs-lookup"><span data-stu-id="bd386-176">The **Create Azure SQL Database** appears:</span></span>

* <span data-ttu-id="bd386-177">Pola **Nazwa bazy danych**, **Grupa zasobów**, **serwer bazy danych** i zapis **planu App Service** są wypełniane.</span><span class="sxs-lookup"><span data-stu-id="bd386-177">The **Database name**, **Resource Group**, **Database server** and **App Service Plan** entry fields are populated.</span></span> <span data-ttu-id="bd386-178">Te wartości można zachować lub zmienić.</span><span class="sxs-lookup"><span data-stu-id="bd386-178">You can keep these values or change them.</span></span>
* <span data-ttu-id="bd386-179">Wprowadź **nazwę użytkownika** i **hasło administratora** bazy danych dla wybranego **serwera bazy danych** (Zwróć uwagę na używane konto musi mieć uprawnienia niezbędne do utworzenia nowej bazy danych Azure SQL Database).</span><span class="sxs-lookup"><span data-stu-id="bd386-179">Enter the **Database administrator username** and **Database administrator password** for the selected **Database server** (note the account you use must have the necessary permissions to create the new Azure SQL database)</span></span>
* <span data-ttu-id="bd386-180">Wybierz przycisk **Utwórz**.</span><span class="sxs-lookup"><span data-stu-id="bd386-180">Select **Create**.</span></span>

![Nowe okno dialogowe Azure SQL Database](publish-to-azure-webapp-using-vs/_static/sql_create.png)

<span data-ttu-id="bd386-182">Po zakończeniu tworzenia okno dialogowe jest automatycznie zamykane, a okno dialogowe **konfigurowanie Azure SQL Database** ponownie przyniesie fokus:</span><span class="sxs-lookup"><span data-stu-id="bd386-182">After creation is completed the dialog is automatically closed and the **Configure Azure SQL Database** dialog gets focus again:</span></span>

* <span data-ttu-id="bd386-183">Nowo utworzone wystąpienie jest automatycznie wybierane.</span><span class="sxs-lookup"><span data-stu-id="bd386-183">The new instance that was just created is automatically selected.</span></span>
* <span data-ttu-id="bd386-184">Wybierz pozycję **Dalej**.</span><span class="sxs-lookup"><span data-stu-id="bd386-184">Select **Next**.</span></span>

![Skonfiguruj okno dialogowe Azure SQL Database](publish-to-azure-webapp-using-vs/_static/sql_select.png)

<span data-ttu-id="bd386-186">W następnym kroku okna dialogowego **konfigurowanie Azure SQL Database** :</span><span class="sxs-lookup"><span data-stu-id="bd386-186">In the next step of the **Configure Azure SQL Database** dialog:</span></span>

* <span data-ttu-id="bd386-187">Wprowadź wartość w polach **Nazwa użytkownika** i **hasło połączenia** bazy danych.</span><span class="sxs-lookup"><span data-stu-id="bd386-187">Enter the **Database connection user name** and **Database connection password** fields.</span></span> <span data-ttu-id="bd386-188">Są to szczegóły używane przez aplikację do łączenia się z bazą danych w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="bd386-188">These are the details your application will use to connect to the database at runtime.</span></span> <span data-ttu-id="bd386-189">Najlepszym rozwiązaniem jest unikanie używania tych samych informacji, co nazwa użytkownika administratora & hasło użyte w poprzednim kroku.</span><span class="sxs-lookup"><span data-stu-id="bd386-189">Best practice is to avoid using the same details as the admin username & password used in the previous step.</span></span>
* <span data-ttu-id="bd386-190">Wybierz pozycję **Zakończ**.</span><span class="sxs-lookup"><span data-stu-id="bd386-190">Select **Finish**.</span></span>

![Konfigurowanie okna dialogowego Azure SQL Database, szczegóły parametrów połączenia](publish-to-azure-webapp-using-vs/_static/sql_connection.png)

<span data-ttu-id="bd386-192">Na stronie **Podsumowanie profilu publikowania** wybierz pozycję **Ustawienia**:</span><span class="sxs-lookup"><span data-stu-id="bd386-192">In the **Publish Profile summary** page select **Settings**:</span></span>

![Strona podsumowania profilu publikowania: Edycja ustawień](publish-to-azure-webapp-using-vs/_static/pp_configured.png)

<span data-ttu-id="bd386-194">Na stronie **Ustawienia** okna dialogowego **Publikowanie** :</span><span class="sxs-lookup"><span data-stu-id="bd386-194">On the **Settings** page of the **Publish** dialog:</span></span>

* <span data-ttu-id="bd386-195">Rozwiń węzeł **bazy danych** i zaznacz pole wyboru **Użyj tych parametrów połączenia w czasie wykonywania**.</span><span class="sxs-lookup"><span data-stu-id="bd386-195">Expand **Databases** and check **Use this connection string at runtime**.</span></span>
* <span data-ttu-id="bd386-196">Rozwiń węzeł **Entity Framework migracje** i zaznacz opcję **Zastosuj tę migrację przy publikowaniu**.</span><span class="sxs-lookup"><span data-stu-id="bd386-196">Expand **Entity Framework Migrations** and check **Apply this migration on publish**.</span></span>

* <span data-ttu-id="bd386-197">Wybierz pozycję **Zapisz**.</span><span class="sxs-lookup"><span data-stu-id="bd386-197">Select **Save**.</span></span> <span data-ttu-id="bd386-198">Program Visual Studio wraca do okna dialogowego **Publikowanie** .</span><span class="sxs-lookup"><span data-stu-id="bd386-198">Visual Studio returns to the **Publish** dialog.</span></span> 

![Okno dialogowe publikowania: Panel ustawień](publish-to-azure-webapp-using-vs/_static/pp_settings.png)

<span data-ttu-id="bd386-200">Kliknij przycisk **Opublikuj**.</span><span class="sxs-lookup"><span data-stu-id="bd386-200">Click **Publish**.</span></span> <span data-ttu-id="bd386-201">Program Visual Studio publikuje aplikację na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="bd386-201">Visual Studio publishes your app to Azure.</span></span> <span data-ttu-id="bd386-202">Po zakończeniu wdrażania aplikacja zostanie otwarta w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="bd386-202">When the deployment completes, the app is opened in a browser.</span></span>

![Okno dialogowe publikowania: Panel ustawień](publish-to-azure-webapp-using-vs/_static/pp_publish.png)

### <a name="update-the-app"></a><span data-ttu-id="bd386-204">Aktualizowanie aplikacji</span><span class="sxs-lookup"><span data-stu-id="bd386-204">Update the app</span></span>

* <span data-ttu-id="bd386-205">Edytuj stronę *Pages/index. cshtml* Razor i zmień jej zawartość.</span><span class="sxs-lookup"><span data-stu-id="bd386-205">Edit the *Pages/Index.cshtml* Razor page and change its contents.</span></span> <span data-ttu-id="bd386-206">Na przykład można zmodyfikować akapit, aby powiedzieć "Hello ASP.NET Core!":</span><span class="sxs-lookup"><span data-stu-id="bd386-206">For example, you can modify the paragraph to say "Hello ASP.NET Core!":</span></span>

    [!code-html[Index](publish-to-azure-webapp-using-vs/sample/index.cshtml?highlight=10&range=1-12)]

* <span data-ttu-id="bd386-207">Na stronie **Podsumowanie profilu publikowania** ponownie wybierz pozycję **Opublikuj** .</span><span class="sxs-lookup"><span data-stu-id="bd386-207">Select **Publish** from the **Publish Profile summary** page again.</span></span>

![Strona podsumowania profilu publikowania](publish-to-azure-webapp-using-vs/_static/pp_publish.png)

* <span data-ttu-id="bd386-209">Po opublikowaniu aplikacji Sprawdź, czy wprowadzone zmiany są dostępne na platformie Azure.</span><span class="sxs-lookup"><span data-stu-id="bd386-209">After the app is published, verify the changes you made are available on Azure.</span></span>

![Sprawdzanie, czy zadanie zostało ukończone](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a><span data-ttu-id="bd386-211">Czyszczenie</span><span class="sxs-lookup"><span data-stu-id="bd386-211">Clean up</span></span>

<span data-ttu-id="bd386-212">Po zakończeniu testowania aplikacji przejdź do [Azure Portal](https://portal.azure.com/) i Usuń aplikację.</span><span class="sxs-lookup"><span data-stu-id="bd386-212">When you have finished testing the app, go to the [Azure portal](https://portal.azure.com/) and delete the app.</span></span>

* <span data-ttu-id="bd386-213">Wybierz pozycję **grupy zasobów**, a następnie wybierz utworzoną grupę zasobów.</span><span class="sxs-lookup"><span data-stu-id="bd386-213">Select **Resource groups**, then select the resource group you created.</span></span>

![Witryna Azure Portal: grupy zasobów w menu paska bocznego](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* <span data-ttu-id="bd386-215">Na stronie **grupy zasobów** wybierz pozycję **Usuń**.</span><span class="sxs-lookup"><span data-stu-id="bd386-215">In the **Resource groups** page, select **Delete**.</span></span>

![Witryna Azure Portal: Strona grupy zasobów](publish-to-azure-webapp-using-vs/_static/rgd.png)

* <span data-ttu-id="bd386-217">Wprowadź nazwę grupy zasobów i wybierz pozycję **Usuń**.</span><span class="sxs-lookup"><span data-stu-id="bd386-217">Enter the name of the resource group and select **Delete**.</span></span> <span data-ttu-id="bd386-218">Twoja aplikacja i wszystkie inne zasoby utworzone w tym samouczku zostaną usunięte z platformy Azure.</span><span class="sxs-lookup"><span data-stu-id="bd386-218">Your app and all other resources created in this tutorial are now deleted from Azure.</span></span>

### <a name="next-steps"></a><span data-ttu-id="bd386-219">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="bd386-219">Next steps</span></span>

* <xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a><span data-ttu-id="bd386-220">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="bd386-220">Additional resources</span></span>

* <span data-ttu-id="bd386-221">Aby uzyskać Visual Studio Code, zobacz temat [Publikowanie profilów](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).</span><span class="sxs-lookup"><span data-stu-id="bd386-221">For Visual Studio Code, see [Publish profiles](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).</span></span>
* [<span data-ttu-id="bd386-222">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="bd386-222">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="bd386-223">Grupa zasobów platformy Azure</span><span class="sxs-lookup"><span data-stu-id="bd386-223">Azure resource groups</span></span>](/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [<span data-ttu-id="bd386-224">Azure SQL Database</span><span class="sxs-lookup"><span data-stu-id="bd386-224">Azure SQL Database</span></span>](/azure/sql-database/)
* <xref:host-and-deploy/visual-studio-publish-profiles>
* <xref:test/troubleshoot-azure-iis>
