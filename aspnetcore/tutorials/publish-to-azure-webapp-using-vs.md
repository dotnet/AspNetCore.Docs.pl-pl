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
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a>Publikowanie aplikacji ASP.NET Core na platformie Azure przy użyciu programu Visual Studio

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)
::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

::: moniker-end


Zobacz [publikowanie aplikacji sieci Web, aby Azure App Service przy użyciu Visual Studio dla komputerów Mac](https://docs.microsoft.com/visualstudio/mac/publish-app-svc?view=vsmac-2019) , jeśli pracujesz na macOS.

Aby rozwiązać problem z wdrożeniem App Service, <xref:test/troubleshoot-azure-iis>Zobacz.

## <a name="set-up"></a>Konfigurowanie

* Otwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/dotnet/) , jeśli go nie masz. 

## <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej

Na stronie startowej programu Visual Studio wybierz pozycję **plik > nowy > projekt...**

![Menu Plik](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

Wypełnij okno dialogowe **Nowy projekt** :

* W lewym okienku wybierz pozycję **.NET Core**.
* W środkowym okienku wybierz pozycję **aplikacja sieci Web ASP.NET Core**.
* Wybierz przycisk **OK**.

![Okno dialogowe Nowy projekt](publish-to-azure-webapp-using-vs/_static/new_prj.png)

W oknie dialogowym **Nowa aplikacja sieci Web ASP.NET Core** :

* Wybierz pozycję **aplikacja sieci Web**.
* Wybierz pozycję **Zmień uwierzytelnianie**.

![Okno dialogowe Nowy projekt](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

Zostanie wyświetlone okno dialogowe **Zmienianie uwierzytelniania** . 

* Wybierz pozycję **indywidualne konta użytkowników**.
* Wybierz **przycisk OK** , aby powrócić do **nowej aplikacji sieci Web ASP.NET Core**, a następnie ponownie wybierz przycisk **OK** .

![Nowe okno dialogowe uwierzytelniania w sieci Web ASP.NET Core](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

Program Visual Studio tworzy rozwiązanie.

## <a name="run-the-app"></a>Uruchomienie aplikacji

* Naciśnij klawisze CTRL + F5, aby uruchomić projekt.
* Przetestuj linki **informacje** i **kontakt** .

![Aplikacja sieci Web otwarta w przeglądarce Microsoft Edge na hoście lokalnym](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a>Rejestrowanie użytkownika

* Wybierz pozycję **zarejestruj** i Zarejestruj nowego użytkownika. Możesz użyć fikcyjnego adresu e-mail. Gdy przesyłasz, na stronie jest wyświetlany następujący błąd:

    *"Wewnętrzny błąd serwera: operacja bazy danych nie powiodła się podczas przetwarzania żądania. Wyjątek SQL: nie można otworzyć bazy danych. Zastosowanie istniejących migracji dla kontekstu bazy danych aplikacji może rozwiązać ten problem.*
* Wybierz pozycję **Zastosuj migracje** , a po aktualizacji strony Odśwież stronę.

![Wewnętrzny błąd serwera: operacja bazy danych nie powiodła się podczas przetwarzania żądania. Wyjątek SQL: nie można otworzyć bazy danych. Zastosowanie istniejących migracji dla kontekstu bazy danych aplikacji może rozwiązać ten problem.](publish-to-azure-webapp-using-vs/_static/mig.png)

W aplikacji zostanie wyświetlona wiadomość e-mail służąca do zarejestrowania nowego użytkownika i linku **wylogowywania** .

![Aplikacja sieci Web otwarta w przeglądarce Microsoft Edge. Link Register został zastąpiony tekstem Hello email@domain.com!](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a>Wdrażanie aplikacji na platformie Azure

Kliknij prawym przyciskiem myszy projekt w Eksplorator rozwiązań i wybierz pozycję **Publikuj...**.

![Menu kontekstowe otwierane z wyróżnionym linkiem publikowania](publish-to-azure-webapp-using-vs/_static/pub.png)

W oknie dialogowym **publikowania** :

* Wybierz **App Service Microsoft Azure**.
* Wybierz ikonę koła zębatego, a następnie wybierz pozycję **Utwórz profil**.
* Wybierz pozycję **Utwórz profil**.

![Okno dialogowe publikowania](publish-to-azure-webapp-using-vs/_static/maas1.png)

### <a name="create-azure-resources"></a>Tworzenie zasobów platformy Azure

Zostanie wyświetlone okno dialogowe **tworzenie App Service** :

* Wprowadź swoją subskrypcję.
* Pola **Nazwa aplikacji**, **Grupa zasobów**i zapis **planu App Service** są wypełniane. Te nazwy można zachować lub zmienić.

![Okno dialogowe App Service](publish-to-azure-webapp-using-vs/_static/newrg1.png)

* Wybierz kartę **usługi** , aby utworzyć nową bazę danych.

* Wybierz zieloną **+** ikonę, aby utworzyć nowy SQL Database

![Nowa baza danych SQL Database](publish-to-azure-webapp-using-vs/_static/sql.png)

* Wybierz pozycję **Nowy...** w oknie dialogowym **Konfigurowanie SQL Database** , aby utworzyć nową bazę danych.

![Nowe SQL Database i serwer](publish-to-azure-webapp-using-vs/_static/conf.png)

Zostanie wyświetlone okno dialogowe **konfigurowanie SQL Server** .

* Wprowadź nazwę użytkownika i hasło administratora, a następnie wybierz przycisk **OK**. Można zachować domyślną **nazwę serwera**. 

> [!NOTE]
> "Administrator" nie jest dozwolony jako nazwa użytkownika administratora.

![Skonfiguruj okno dialogowe SQL Server](publish-to-azure-webapp-using-vs/_static/conf_servername.png)

* Wybierz przycisk **OK**.

Program Visual Studio powróci do okna dialogowego **tworzenie App Service** .

* Wybierz pozycję **Utwórz** w oknie dialogowym **Tworzenie App Service** .

![Skonfiguruj okno dialogowe SQL Database](publish-to-azure-webapp-using-vs/_static/conf_final.png)

Program Visual Studio utworzy aplikację sieci Web i SQL Server na platformie Azure. Ten krok może potrwać kilka minut. Aby uzyskać informacje o utworzonych zasobach, zobacz [dodatkowe zasoby](#additional-resources).

Po zakończeniu wdrażania wybierz pozycję **Ustawienia**:

![Skonfiguruj okno dialogowe SQL Server](publish-to-azure-webapp-using-vs/_static/set.png)

Na stronie **Ustawienia** okna dialogowego **Publikowanie** :

* Rozwiń węzeł **bazy danych** i zaznacz pole wyboru **Użyj tych parametrów połączenia w czasie wykonywania**.
* Rozwiń węzeł **Entity Framework migracje** i zaznacz opcję **Zastosuj tę migrację przy publikowaniu**.

* Wybierz pozycję **Zapisz**. Program Visual Studio wraca do okna dialogowego **Publikowanie** . 

![Okno dialogowe publikowania: Panel ustawień](publish-to-azure-webapp-using-vs/_static/pubs.png)

Kliknij przycisk **Opublikuj**. Program Visual Studio publikuje aplikację na platformie Azure. Po zakończeniu wdrażania aplikacja zostanie otwarta w przeglądarce.

### <a name="test-your-app-in-azure"></a>Testowanie aplikacji na platformie Azure

* Testowanie linków **about** i **Contact**

* Rejestrowanie nowego użytkownika

![Aplikacja sieci Web otwarta w przeglądarce Microsoft Edge na Azure App Service](publish-to-azure-webapp-using-vs/_static/register.png)

### <a name="update-the-app"></a>Aktualizowanie aplikacji

* Edytuj stronę *strony/informacje. cshtml* Razor i zmień jej zawartość. Na przykład można zmodyfikować akapit, aby powiedzieć "Hello ASP.NET Core!":

    [!code-html[About](publish-to-azure-webapp-using-vs/sample/about.cshtml?highlight=9&range=1-9)]

* Kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Opublikuj...** ponownie.

![Menu kontekstowe otwierane z wyróżnionym linkiem publikowania](publish-to-azure-webapp-using-vs/_static/pub.png)

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
