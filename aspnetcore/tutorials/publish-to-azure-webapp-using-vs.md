---
title: Publikowanie aplikacji ASP.NET Core na platformie Azure przy użyciu programu Visual Studio
author: rick-anderson
description: Dowiedz się, jak opublikować aplikację ASP.NET Core, aby Azure App Service przy użyciu programu Visual Studio.
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 07/10/2019
no-loc:
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
uid: tutorials/publish-to-azure-webapp-using-vs
ms.openlocfilehash: ebfe89d68725d597d6975015bbf84414ff818829
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722913"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a>Publikowanie aplikacji ASP.NET Core na platformie Azure przy użyciu programu Visual Studio

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)
::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

::: moniker-end


Zobacz [publikowanie aplikacji sieci Web, aby Azure App Service przy użyciu Visual Studio dla komputerów Mac](/visualstudio/mac/publish-app-svc?view=vsmac-2019) , jeśli pracujesz na macOS.

Aby rozwiązać problem z wdrożeniem App Service, zobacz <xref:test/troubleshoot-azure-iis> .

## <a name="set-up"></a>Konfiguruj

* Otwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/dotnet/) , jeśli go nie masz. 

## <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej

Na stronie startowej programu Visual Studio wybierz pozycję **plik > nowy > projekt...**

![Menu Plik](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

Wypełnij okno dialogowe **Nowy projekt** :

* Wybierz **ASP.NET Core aplikacji sieci Web**.
* Wybierz pozycję **Dalej**.

![Okno dialogowe Nowy projekt](publish-to-azure-webapp-using-vs/_static/new_prj.png)

W oknie dialogowym **Nowa aplikacja sieci Web ASP.NET Core** :

* Wybierz pozycję **aplikacja sieci Web**.
* Wybierz pozycję **Zmień** w obszarze Uwierzytelnianie.

![Nowe okno dialogowe sieci Web ASP.NET Core](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

Zostanie wyświetlone okno dialogowe **Zmienianie uwierzytelniania** . 

* Wybierz pozycję **indywidualne konta użytkowników**.
* Wybierz **przycisk OK** , aby powrócić do **nowej aplikacji sieci Web ASP.NET Core**, a następnie wybierz pozycję **Utwórz**.

![Nowe okno dialogowe uwierzytelniania w sieci Web ASP.NET Core](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

Program Visual Studio tworzy rozwiązanie.

## <a name="run-the-app"></a>Uruchamianie aplikacji

* Naciśnij klawisze CTRL + F5, aby uruchomić projekt.
* Przetestuj łącze **prywatność** .

![Aplikacja sieci Web otwarta w przeglądarce Microsoft Edge na hoście lokalnym](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a>Rejestrowanie użytkownika

* Wybierz pozycję **zarejestruj** i Zarejestruj nowego użytkownika. Możesz użyć fikcyjnego adresu e-mail. Gdy przesyłasz, na stronie jest wyświetlany następujący błąd:

    *"Operacja bazy danych nie powiodła się podczas przetwarzania żądania. Zastosowanie istniejących migracji dla kontekstu bazy danych aplikacji może rozwiązać ten problem.*
* Wybierz pozycję **Zastosuj migracje** , a po aktualizacji strony Odśwież stronę.

![Operacja bazy danych nie powiodła się podczas przetwarzania żądania. Zastosowanie istniejących migracji dla kontekstu bazy danych aplikacji może rozwiązać ten problem.](publish-to-azure-webapp-using-vs/_static/mig.png)

Aplikacja wyświetli wiadomość e-mail użytą do zarejestrowania nowego użytkownika i linku do **wylogowania** .

![Aplikacja sieci Web otwarta w przeglądarce Microsoft Edge. Link Register został zastąpiony tekstem Hello user1@example.com !](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a>Wdrażanie aplikacji na platformie Azure

Kliknij prawym przyciskiem myszy projekt w Eksplorator rozwiązań i wybierz pozycję **Publikuj...**.

![Menu kontekstowe otwierane z wyróżnionym linkiem publikowania](publish-to-azure-webapp-using-vs/_static/pub.png)

W oknie dialogowym **publikowania** :

* Wybierz pozycję **Azure**.
* Wybierz pozycję **Dalej**.

![Okno dialogowe publikowania](publish-to-azure-webapp-using-vs/_static/maas1.png)

W oknie dialogowym **publikowania** :

* Wybierz pozycję **Azure App Service (Linux)**.
* Wybierz pozycję **Dalej**.

![Okno dialogowe publikowania: Wybieranie usługi platformy Azure](publish-to-azure-webapp-using-vs/_static/maas2.png)

W oknie dialogowym **Publikowanie** wybierz pozycję **Utwórz nowy Azure App Service...**

![Okno dialogowe publikowania: wybierz wystąpienie usługi platformy Azure](publish-to-azure-webapp-using-vs/_static/maas3.png)

Zostanie wyświetlone okno dialogowe **tworzenie App Service** :

* Pola **Nazwa aplikacji**, **Grupa zasobów**i zapis **planu App Service** są wypełniane. Te nazwy można zachować lub zmienić.
* Wybierz pozycję **Utwórz**.

![Okno dialogowe Tworzenie usługi App Service](publish-to-azure-webapp-using-vs/_static/newrg1.png)

Po zakończeniu tworzenia okno dialogowe zostanie automatycznie zamknięte, a okno dialogowe **publikowania** ponownie przyniesie fokus:

* Nowo utworzone wystąpienie jest automatycznie wybierane.
* Wybierz pozycję **Zakończ**.

![Okno dialogowe publikowania: wybierz wystąpienie App Service](publish-to-azure-webapp-using-vs/_static/select_as.png)

Następnie zostanie wyświetlona strona **Podsumowanie profilu publikowania** . Program Visual Studio wykrył, że ta aplikacja wymaga bazy danych SQL Server i prosi o jej konfigurację. Wybierz pozycję **Konfiguruj**.

![Strona podsumowania profilu publikowania: Konfigurowanie zależności SQL Server](publish-to-azure-webapp-using-vs/_static/sql.png)

Zostanie wyświetlone okno dialogowe **Konfigurowanie zależności** :

* Wybierz **Azure SQL Database**.
* Wybierz pozycję **Dalej**.

![Konfigurowanie okna dialogowego zależności SQL Server](publish-to-azure-webapp-using-vs/_static/sql1.png)

W oknie dialogowym **Konfigurowanie usługi Azure SQL Database** wybierz pozycję **Utwórz SQL Database**

![Skonfiguruj okno dialogowe Azure SQL Database](publish-to-azure-webapp-using-vs/_static/sql2.png)

Zostanie wyświetlona **Azure SQL Database tworzenia** :

* Pola **Nazwa bazy danych**, **Grupa zasobów**, **serwer bazy danych** i zapis **planu App Service** są wypełniane. Te wartości można zachować lub zmienić.
* Wprowadź **nazwę użytkownika** i **hasło administratora** bazy danych dla wybranego **serwera bazy danych** (Zwróć uwagę na używane konto musi mieć uprawnienia niezbędne do utworzenia nowej bazy danych Azure SQL Database).
* Wybierz pozycję **Utwórz**.

![Nowe okno dialogowe Azure SQL Database](publish-to-azure-webapp-using-vs/_static/sql_create.png)

Po zakończeniu tworzenia okno dialogowe jest automatycznie zamykane, a okno dialogowe **konfigurowanie Azure SQL Database** ponownie przyniesie fokus:

* Nowo utworzone wystąpienie jest automatycznie wybierane.
* Wybierz pozycję **Dalej**.

![Skonfiguruj okno dialogowe Azure SQL Database](publish-to-azure-webapp-using-vs/_static/sql_select.png)

W następnym kroku okna dialogowego **konfigurowanie Azure SQL Database** :

* Wprowadź wartość w polach **Nazwa użytkownika** i **hasło połączenia** bazy danych. Są to szczegóły używane przez aplikację do łączenia się z bazą danych w czasie wykonywania. Najlepszym rozwiązaniem jest unikanie używania tych samych informacji, co nazwa użytkownika administratora & hasło użyte w poprzednim kroku.
* Wybierz pozycję **Zakończ**.

![Konfigurowanie okna dialogowego Azure SQL Database, szczegóły parametrów połączenia](publish-to-azure-webapp-using-vs/_static/sql_connection.png)

Na stronie **Podsumowanie profilu publikowania** wybierz pozycję **Ustawienia**:

![Strona podsumowania profilu publikowania: Edycja ustawień](publish-to-azure-webapp-using-vs/_static/pp_configured.png)

Na stronie **Ustawienia** okna dialogowego **Publikowanie** :

* Rozwiń węzeł **bazy danych** i zaznacz pole wyboru **Użyj tych parametrów połączenia w czasie wykonywania**.
* Rozwiń węzeł **Entity Framework migracje** i zaznacz opcję **Zastosuj tę migrację przy publikowaniu**.

* Wybierz pozycję **Zapisz**. Program Visual Studio wraca do okna dialogowego **Publikowanie** . 

![Okno dialogowe publikowania: Panel ustawień](publish-to-azure-webapp-using-vs/_static/pp_settings.png)

Kliknij przycisk **Opublikuj**. Program Visual Studio publikuje aplikację na platformie Azure. Po zakończeniu wdrażania aplikacja zostanie otwarta w przeglądarce.

![Okno dialogowe publikowania: Panel ustawień](publish-to-azure-webapp-using-vs/_static/pp_publish.png)

### <a name="update-the-app"></a>Aktualizowanie aplikacji

* Edytuj stronę *Pages/index. cshtml* Razor i zmień jej zawartość. Na przykład można zmodyfikować akapit, aby powiedzieć "Hello ASP.NET Core!":

    [!code-html[Index](publish-to-azure-webapp-using-vs/sample/index.cshtml?highlight=10&range=1-12)]

* Na stronie **Podsumowanie profilu publikowania** ponownie wybierz pozycję **Opublikuj** .

![Strona podsumowania profilu publikowania](publish-to-azure-webapp-using-vs/_static/pp_publish.png)

* Po opublikowaniu aplikacji Sprawdź, czy wprowadzone zmiany są dostępne na platformie Azure.

![Sprawdzanie, czy zadanie zostało ukończone](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a>Czyszczenie

Po zakończeniu testowania aplikacji przejdź do [Azure Portal](https://portal.azure.com/) i Usuń aplikację.

* Wybierz pozycję **grupy zasobów**, a następnie wybierz utworzoną grupę zasobów.

![Witryna Azure Portal: grupy zasobów w menu paska bocznego](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* Na stronie **grupy zasobów** wybierz pozycję **Usuń**.

![Witryna Azure Portal: Strona grupy zasobów](publish-to-azure-webapp-using-vs/_static/rgd.png)

* Wprowadź nazwę grupy zasobów i wybierz pozycję **Usuń**. Twoja aplikacja i wszystkie inne zasoby utworzone w tym samouczku zostaną usunięte z platformy Azure.

### <a name="next-steps"></a>Następne kroki

* <xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a>Zasoby dodatkowe

* Aby uzyskać Visual Studio Code, zobacz temat [Publikowanie profilów](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).
* [Azure App Service](/azure/app-service/app-service-web-overview)
* [Grupa zasobów platformy Azure](/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [Azure SQL Database](/azure/sql-database/)
* <xref:host-and-deploy/visual-studio-publish-profiles>
* <xref:test/troubleshoot-azure-iis>