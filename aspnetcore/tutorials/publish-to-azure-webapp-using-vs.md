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
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio"></a>Publikowanie aplikacji ASP.NET Core na platformie Azure za pomocą programu Visual Studio

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)
::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

::: moniker-end


Zobacz [Publikowanie aplikacji sieci Web w usłudze Azure App Service przy użyciu programu Visual Studio dla komputerów Mac,](https://docs.microsoft.com/visualstudio/mac/publish-app-svc?view=vsmac-2019) jeśli pracujesz z systemem macOS.

Aby rozwiązać problem z wdrażaniem usługi App Service, zobacz <xref:test/troubleshoot-azure-iis>.

## <a name="set-up"></a>Konfiguruj

* Otwórz [bezpłatne konto platformy Azure,](https://azure.microsoft.com/free/dotnet/) jeśli go nie masz. 

## <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej

Na stronie startowej programu Visual Studio wybierz pozycję **Plik > nowy projekt >...**

![Menu Plik](publish-to-azure-webapp-using-vs/_static/file_new_project.png)

Ukończ okno dialogowe **Nowy projekt:**

* W lewym okienku wybierz pozycję **.NET Core**.
* W środkowym okienku wybierz pozycję **ASP.NET Core Web Application**.
* Kliknij przycisk **OK**.

![Okno dialogowe Nowy projekt](publish-to-azure-webapp-using-vs/_static/new_prj.png)

W oknie dialogowym **Nowa ASP.NET Podstawowa aplikacja sieci Web:**

* Wybierz **aplikację sieci Web**.
* Wybierz pozycję **Zmień uwierzytelnianie**.

![Okno dialogowe Nowy projekt](publish-to-azure-webapp-using-vs/_static/new_prj_2.png)

Zostanie wyświetlone okno dialogowe **Uwierzytelnianie zmian.** 

* Wybierz **pozycję Indywidualne konta użytkowników**.
* Wybierz **przycisk OK,** aby powrócić do **nowej ASP.NET podstawowej aplikacji sieci Web,** a następnie ponownie wybierz przycisk **OK.**

![Nowe okno dialogowe uwierzytelniania sieci Web ASP.NET Core](publish-to-azure-webapp-using-vs/_static/new_prj_auth.png) 

Visual Studio tworzy rozwiązanie.

## <a name="run-the-app"></a>Uruchomienie aplikacji

* Naciśnij klawisze CTRL+F5, aby uruchomić projekt.
* Testowanie łączy **Informacje** i **Kontakt.**

![Aplikacja sieci Web otwarta w programie Microsoft Edge na stronie localhost](publish-to-azure-webapp-using-vs/_static/show.png)

### <a name="register-a-user"></a>Zarejestruj użytkownika

* Wybierz **zarejestruj się** i zarejestruj nowego użytkownika. Możesz użyć fikcyjnego adresu e-mail. Podczas przesyłania na stronie wyświetlany jest następujący błąd:

    *"Wewnętrzny błąd serwera: Operacja bazy danych nie powiodła się podczas przetwarzania żądania. Wyjątek SQL: Nie można otworzyć bazy danych. Zastosowanie istniejących migracji dla kontekstu bazy danych aplikacji może rozwiązać ten problem."*
* Wybierz **pozycję Zastosuj migracje,** a po zaktualizowaniu strony odśwież stronę.

![Wewnętrzny błąd serwera: Operacja bazy danych nie powiodła się podczas przetwarzania żądania. Wyjątek SQL: Nie można otworzyć bazy danych. Zastosowanie istniejących migracji dla kontekstu bazy danych aplikacji może rozwiązać ten problem.](publish-to-azure-webapp-using-vs/_static/mig.png)

Aplikacja wyświetla wiadomość e-mail służącą do rejestracji nowego użytkownika i łącze **Wyloguj.**

![Aplikacja sieci Web otwarta w programie Microsoft Edge. Link Zarejestruj jest zastępowany email@domain.comtekstem Hello !](publish-to-azure-webapp-using-vs/_static/hello.png)

## <a name="deploy-the-app-to-azure"></a>Wdrażanie aplikacji na platformie Azure

Kliknij prawym przyciskiem myszy projekt w Eksploratorze rozwiązań i wybierz pozycję **Publikuj...**.

![Menu kontekstowe otwarte z wyróżnionym łączem Publikowania](publish-to-azure-webapp-using-vs/_static/pub.png)

W oknie dialogowym **Publikowanie:**

* Wybierz usługę **Microsoft Azure App Service**.
* Wybierz ikonę koła zębatego, a następnie wybierz pozycję **Utwórz profil**.
* Wybierz **pozycję Utwórz profil**.

![Okno dialogowe Publikowania](publish-to-azure-webapp-using-vs/_static/maas1.png)

### <a name="create-azure-resources"></a>Tworzenie zasobów platformy Azure

Zostanie wyświetlone okno dialogowe **Tworzenie usługi app service:**

* Wprowadź subskrypcję.
* Pola **wpisu Nazwa aplikacji,** **Grupa zasobów**i Plan usługi **aplikacji** są wypełniane. Możesz zachować te nazwy lub je zmienić.

![Okno dialogowe Usługa aplikacji](publish-to-azure-webapp-using-vs/_static/newrg1.png)

* Wybierz kartę **Usługi,** aby utworzyć nową bazę danych.

* Wybierz zieloną **+** ikonę, aby utworzyć nową bazę danych SQL

![Nowa baza danych SQL Database](publish-to-azure-webapp-using-vs/_static/sql.png)

* Wybierz **pozycję Nowy...** w oknie dialogowym **Konfigurowanie bazy danych SQL,** aby utworzyć nową bazę danych.

![Nowa baza danych i serwer SQL](publish-to-azure-webapp-using-vs/_static/conf.png)

Zostanie wyświetlone okno dialogowe **Konfiguruj program SQL Server.**

* Wprowadź nazwę użytkownika i hasło administratora, a następnie wybierz **przycisk OK**. Można zachować domyślną **nazwę serwera**. 

> [!NOTE]
> "admin" nie jest dozwolony jako nazwa użytkownika administratora.

![Okno dialogowe Konfigurowanie programu SQL Server](publish-to-azure-webapp-using-vs/_static/conf_servername.png)

* Kliknij przycisk **OK**.

Visual Studio powraca do okna dialogowego **Tworzenie usługi aplikacji.**

* Wybierz **pozycję Utwórz** w oknie dialogowym **Tworzenie usługi app service.**

![Okno dialogowe Konfigurowanie bazy danych SQL](publish-to-azure-webapp-using-vs/_static/conf_final.png)

Program Visual Studio tworzy aplikację sieci Web i program SQL Server na platformie Azure. Ten krok może potrwać kilka minut. Aby uzyskać informacje na temat utworzonych zasobów, zobacz [Dodatkowe zasoby](#additional-resources).

Po zakończeniu wdrażania wybierz **pozycję Ustawienia:**

![Okno dialogowe Konfigurowanie programu SQL Server](publish-to-azure-webapp-using-vs/_static/set.png)

Na stronie **Ustawienia** okna dialogowego **Publikowanie:**

* Rozwiń **pozycję Bazy danych** i zaznacz pole Wyboru Użyj tego ciągu połączenia w czasie **wykonywania**.
* Rozwiń **migracje platformowe i** zaznacz pole **Wyboru Zastosuj tę migrację podczas publikowania**.

* Wybierz **pozycję Zapisz**. Visual Studio powraca do okna dialogowego **Publikowania.** 

![Okno dialogowe Publikowanie: Panel Ustawienia](publish-to-azure-webapp-using-vs/_static/pubs.png)

Kliknij przycisk **Opublikuj**. Program Visual Studio publikuje aplikację na platformie Azure. Po zakończeniu wdrażania aplikacja jest otwierana w przeglądarce.

### <a name="test-your-app-in-azure"></a>Testowanie aplikacji na platformie Azure

* Testowanie łączy **Informacje** i **Kontakt**

* Zarejestruj nowego użytkownika

![Aplikacja sieci Web otwarta w przeglądarce Microsoft Edge w usłudze Azure App Service](publish-to-azure-webapp-using-vs/_static/register.png)

### <a name="update-the-app"></a>Aktualizowanie aplikacji

* Edytuj stronę *Pages/About.cshtml* Razor i zmień jej zawartość. Na przykład można zmodyfikować akapit, aby powiedzieć "Hello ASP.NET Core!":

    [!code-html[About](publish-to-azure-webapp-using-vs/sample/about.cshtml?highlight=9&range=1-9)]

* Kliknij prawym przyciskiem myszy projekt i wybierz ponownie **pozycję Publikuj.**

![Menu kontekstowe otwarte z wyróżnionym łączem Publikowania](publish-to-azure-webapp-using-vs/_static/pub.png)

* Po opublikowaniu aplikacji sprawdź, czy wprowadzone zmiany są dostępne na platformie Azure.

![Sprawdź, czy zadanie jest ukończone](publish-to-azure-webapp-using-vs/_static/final.png)

### <a name="clean-up"></a>Czyszczenie

Po zakończeniu testowania aplikacji przejdź do [witryny Azure portal](https://portal.azure.com/) i usuń aplikację.

* Wybierz **pozycję Grupy zasobów**, a następnie wybierz utworzoną grupę zasobów.

![Usługa Azure Portal: grupy zasobów w menu paska bocznego](publish-to-azure-webapp-using-vs/_static/portalrg.png)

* Na stronie **Grupy zasobów** wybierz pozycję **Usuń**.

![Strona Azure Portal: Grupy zasobów](publish-to-azure-webapp-using-vs/_static/rgd.png)

* Wprowadź nazwę grupy zasobów i wybierz pozycję **Usuń**. Aplikacja i wszystkie inne zasoby utworzone w tym samouczku są teraz usuwane z platformy Azure.

### <a name="next-steps"></a>Następne kroki

* <xref:host-and-deploy/azure-apps/azure-continuous-deployment>

## <a name="additional-resources"></a>Zasoby dodatkowe

* W polu Visual Studio Code zobacz [Publikowanie profili](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles).
* [Azure App Service](/azure/app-service/app-service-web-overview)
* [Grupy zasobów platformy Azure](/azure/azure-resource-manager/resource-group-overview#resource-groups)
* [Azure SQL Database](/azure/sql-database/)
* <xref:host-and-deploy/visual-studio-publish-profiles>
* <xref:test/troubleshoot-azure-iis>
