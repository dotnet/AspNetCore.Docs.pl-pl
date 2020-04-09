---
title: Ciągłe wdrażanie na platformie Azure za pomocą programu Visual Studio i rozwiązania Git na platformie ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak utworzyć aplikację sieci web ASP.NET Core za pomocą programu Visual Studio i wdrożyć ją w usłudze Azure App Service przy użyciu git do ciągłego wdrażania.
ms.author: riande
ms.custom: mvc
ms.date: 12/06/2018
uid: host-and-deploy/azure-apps/azure-continuous-deployment
ms.openlocfilehash: 3b344505739bb4292ed1683c73ff314b6e4e01e9
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78660854"
---
# <a name="continuous-deployment-to-azure-with-visual-studio-and-git-with-aspnet-core"></a>Ciągłe wdrażanie na platformie Azure za pomocą programu Visual Studio i rozwiązania Git na platformie ASP.NET Core

Przez [Erik Reitan](https://github.com/Erikre)

[!INCLUDE [Azure App Service Preview Notice](../../includes/azure-apps-preview-notice.md)]

W tym samouczku pokazano, jak utworzyć aplikację sieci web ASP.NET Core przy użyciu programu Visual Studio i wdrożyć ją z programu Visual Studio do usługi Azure App Service przy użyciu ciągłego wdrażania.

Zobacz też [Tworzenie pierwszego potoku za pomocą usługi Azure Pipelines](/azure/devops/pipelines/get-started-yaml), który pokazuje, jak skonfigurować przepływ pracy dostarczania ciągłego (CD) dla [usługi Azure App Service](/azure/app-service/app-service-web-overview) przy użyciu usług Azure DevOps. Potoki platformy Azure (usługa Azure DevOps Services) upraszczają konfigurowanie niezawodnego potoku wdrażania w celu publikowania aktualizacji dla aplikacji hostowanych w usłudze Azure App Service. Potok można skonfigurować z witryny Azure Portal do tworzenia, uruchamiania testów, wdrażania w miejscu przejściowym, a następnie wdrażania w środowiskach produkcyjnych.

> [!NOTE]
> Aby ukończyć ten samouczek, wymagane jest konto platformy Microsoft Azure. Aby uzyskać konto, [aktywuj korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A261C142F) lub [zarejestruj się, aby skorzystać z bezpłatnej wersji próbnej.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku przyjęto założenie, że jest zainstalowane następujące oprogramowanie:

* [Program Visual Studio](https://visualstudio.microsoft.com)
* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]
* [Git](https://git-scm.com/downloads) dla Systemu Windows

## <a name="create-an-aspnet-core-web-app"></a>Tworzenie aplikacji internetowej ASP.NET Core

1. Uruchom program Visual Studio.

1. Z menu **Plik** wybierz polecenie **Nowy** > **projekt**.

1. Wybierz szablon projektu **ASP.NET Core Web Application.** Pojawia się w obszarze **Zainstalowane** > **szablony** > **Visual C#** > **.NET Core**. Nazwij `SampleWebAppDemo`projekt . Wybierz opcję **Utwórz nowe repozytorium Git** i kliknij przycisk **OK**.

   ![Okno dialogowe Nowy projekt](azure-continuous-deployment/_static/01-new-project.png)

1. W oknie dialogowym **Nowy ASP.NET Podstawowy projekt** wybierz ASP.NET **szablonu Puste rdzenie,** a następnie kliknij przycisk **OK**.

   ![Nowe okno dialogowe projektu ASP.NET Core](azure-continuous-deployment/_static/02-web-site-template.png)

> [!NOTE]
> Najnowsza wersja .NET Core to 2.0.

### <a name="running-the-web-app-locally"></a>Lokalne uruchamianie aplikacji internetowej

1. Po zakończeniu tworzenia aplikacji przez program Visual Studio uruchom aplikację, wybierając pozycję **Debugowanie** > **startowe debugowania**. Alternatywnie naciśnij klawisz **F5**.

   Zainicjowanie programu Visual Studio i nowej aplikacji może zająć trochę czasu. Po jego zakończeniu przeglądarka pokazuje uruchomiającą aplikację.

   ![Okno przeglądarki z uruchomiona aplikacją wyświetlającego "Hello World!".](azure-continuous-deployment/_static/04-browser-runapp.png)

1. Po przejrzeniu uruchomionej aplikacji sieci Web zamknij przeglądarkę i wybierz ikonę "Zatrzymaj debugowanie" na pasku narzędzi programu Visual Studio, aby zatrzymać aplikację.

## <a name="create-a-web-app-in-the-azure-portal"></a>Tworzenie aplikacji sieci Web w witrynie Azure Portal

Następujące kroki tworzenia aplikacji sieci web w witrynie Azure Portal:

1. Zaloguj się do [portalu Azure](https://portal.azure.com).

1. Wybierz **POZYCJĘ NOWY** w lewym górnym rogu interfejsu portalu.

1. Wybierz **pozycję Web + Mobile** > **Web App**.

   ![Microsoft Azure Portal: Nowy przycisk: Sieć Web + Mobile w obszarze Marketplace: Przycisk aplikacji sieci Web w obszarze Polecane aplikacje](azure-continuous-deployment/_static/05-azure-newwebapp.png)

1. W bloku **aplikacji sieci Web** wprowadź unikatową wartość nazwy usługi **app**.

   ![Blok aplikacji sieci Web](azure-continuous-deployment/_static/06-azure-newappblade.png)

   > [!NOTE]
   > Nazwa **usługi aplikacji** musi być unikatowa. Portal wymusza tę regułę, gdy nazwa jest podana. Jeśli podasz inną wartość, zastąp tę wartość dla każdego wystąpienia **SampleWebAppDemo** w tym samouczku.

   Również w bloku **aplikacji sieci Web** wybierz istniejący plan usługi **aplikacji/lokalizację** lub utwórz nowy. Jeśli tworzysz nowy plan, wybierz warstwę cenową, lokalizację i inne opcje. Aby uzyskać więcej informacji na temat planów usługi App Service, zobacz [szczegółowe omówienie planów usługi Azure App Service.](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview)

1. Wybierz **pozycję Utwórz**. Platforma Azure będzie aprowizować i uruchomić aplikację sieci web.

   ![Usługa Azure Portal: przykładowy blok Podstawowych wersji aplikacji sieci Web 01](azure-continuous-deployment/_static/07-azure-webappblade.png)

## <a name="enable-git-publishing-for-the-new-web-app"></a>Włącz publikowanie git dla nowej aplikacji sieci web

Git to rozproszony system kontroli wersji, który może służyć do wdrażania aplikacji sieci web usługi Azure App Service. Kod aplikacji sieci Web jest przechowywany w lokalnym repozytorium Git, a kod jest wdrażany na platformie Azure przez wypychanie do zdalnego repozytorium.

1. Zaloguj się do [portalu Azure](https://portal.azure.com).

1. Wybierz **usługi aplikacji,** aby wyświetlić listę usług aplikacji skojarzonych z subskrypcją platformy Azure.

1. Wybierz aplikację sieci web utworzoną w poprzedniej sekcji tego samouczka.

1. W bloku **Wdrażanie** wybierz **pozycję Opcje** > wdrażania**Wybierz źródło** > **lokalne repozytorium Git**.

   ![Blok ustawień: Blok źródła wdrożenia: wybierz lemiesz źródłowy](azure-continuous-deployment/_static/deployment-options.png)

1. Kliknij przycisk **OK**.

1. Jeśli poświadczenia wdrażania do publikowania aplikacji sieci web lub innej aplikacji usługi App Service nie zostały wcześniej skonfigurowane, skonfiguruj je teraz:

   * Wybierz **pozycję Poświadczenia** > **wdrażania**ustawień . Zostanie wyświetlony **blok Ustaw poświadczenia wdrożenia.**
   * Utwórz nazwę użytkownika i hasło. Zapisz hasło do późniejszego użycia podczas konfigurowania Git.
   * Wybierz **pozycję Zapisz**.

1. W bloku **Aplikacji sieci Web** wybierz pozycję**Właściwości** **ustawień** > . Adres URL zdalnego repozytorium Git do wdrożenia jest wyświetlany pod **adresem URL GIT**.

1. Skopiuj wartość **adresu URL GIT** do późniejszego użycia w samouczku.

   ![Azure Portal: blok właściwości aplikacji](azure-continuous-deployment/_static/09-azure-giturl.png)

## <a name="publish-the-web-app-to-azure-app-service"></a>Publikowanie aplikacji internetowej w usłudze Azure App Service

W tej sekcji utwórz lokalne repozytorium Git przy użyciu programu Visual Studio i wypchnij z tego repozytorium na platformę Azure, aby wdrożyć aplikację sieci web. Kroki, o które chodzi, są następujące:

* Dodaj ustawienie zdalnego repozytorium przy użyciu wartości adresu URL GIT, aby lokalne repozytorium można było wdrożyć na platformie Azure.
* Zatwierdzanie zmian w projekcie.
* Wypychanie zmian projektu z repozytorium lokalnego do zdalnego repozytorium na platformie Azure.

1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy **pozycję Rozwiązanie "SampleWebAppDemo"** i wybierz pozycję **Zatwierdź**. Zostanie wyświetlone okno **Team Explorer**.

   ![Karta Łączenie eksploratora zespołu](azure-continuous-deployment/_static/10-team-explorer.png)

1. W **Eksploratorze zespołu**wybierz pozycję **Strona główna** (ikona główna) >**ustawieniami repozytorium** **ustawień** > .

1. W sekcji **Elementy zdalne** obszaru **Ustawienia repozytorium** wybierz pozycję **Dodaj**. Zostanie wyświetlone okno dialogowe **Dodawanie elementu zdalnego**.

1. Ustaw **nazwę** pilota na **Azure-SampleApp**.

1. Ustaw wartość **pobierania** na **adres URL Git,** który został skopiowany z platformy Azure wcześniej w tym samouczku. Należy zauważyć, że jest to adres URL, który kończy się **na .git**.

   ![Okno dialogowe Edytowanie zdalnego](azure-continuous-deployment/_static/11-add-remote.png)

   > [!NOTE]
   > Alternatywnie należy określić repozytorium zdalne z **okna polecenia,** otwierając **okno polecenia**, zmieniając katalog projektu i wprowadzając polecenie. Przykład:
   >
   > `git remote add Azure-SampleApp https://me@sampleapp.scm.azurewebsites.net:443/SampleApp.git`

1. Wybierz **ikonę Strona główna** (ikona głównej) >**Ustawienia globalne** **ustawień** > . Upewnij się, że nazwa i adres e-mail zostały ustawione. W razie potrzeby wybierz **pozycję Aktualizuj.**

1. Wybierz **pozycję Zmiany w domu,** > **Changes** aby powrócić do widoku **Zmiany.**

1. Wprowadź komunikat zatwierdzenia, na przykład **początkowe #1 wypychania** i wybierz pozycję **Zatwierdź**. Ta akcja tworzy *zatwierdzenie* lokalnie.

   ![Karta Łączenie eksploratora zespołu](azure-continuous-deployment/_static/12-initial-commit.png)

   > [!NOTE]
   > Alternatywnie zatwierdź zmiany z **okna polecenia,** otwierając **okno polecenia**, zmieniając katalog projektu i wprowadzając polecenia git. Przykład:
   >
   > `git add .`
   >
   > `git commit -am "Initial Push #1"`

1. Wybierz **pozycję Otwieranie** > **wiersza polecenia****Akcji** > **synchronizacji** > domowej . Wiersz polecenia zostanie otwarty w katalogu projektu.

1. W oknie polecenia wprowadź następujące polecenie:

   `git push -u Azure-SampleApp master`

1. Wprowadź hasło **poświadczeń wdrożenia** platformy Azure utworzone wcześniej na platformie Azure.

   To polecenie rozpoczyna proces wypychania lokalnych plików projektu na platformę Azure. Dane wyjściowe z powyższego polecenia kończy się komunikatem, że wdrożenie zakończyło się pomyślnie.

   ```
   remote: Finished successfully.
   remote: Running post deployment command(s)...
   remote: Deployment successful.
   To https://username@samplewebappdemo01.scm.azurewebsites.net:443/SampleWebAppDemo01.git
   * [new branch]      master -> master
   Branch master set up to track remote branch master from Azure-SampleApp.
   ```

   > [!NOTE]
   > Jeśli wymagana jest współpraca nad projektem, należy rozważyć wypychanie do [usługi GitHub](https://github.com) przed wypychaniem na platformę Azure.
 
### <a name="verify-the-active-deployment"></a>Sprawdź aktywne wdrażanie

Sprawdź, czy transfer aplikacji sieci web ze środowiska lokalnego na platformę Azure zakończy się pomyślnie.

W witrynie [Azure Portal](https://portal.azure.com)wybierz aplikację sieci web. Wybierz**opcje wdrażania** **wdrożenia** > .

![Azure Portal: Blok ustawień: blok wdrożeń z pomyślnym wdrożeniem](azure-continuous-deployment/_static/13-verify-deployment.png)

## <a name="run-the-app-in-azure"></a>Uruchamianie aplikacji na platformie Azure

Teraz, gdy aplikacja sieci web zostanie wdrożona na platformie Azure, uruchom aplikację.

Można to osiągnąć na dwa sposoby:

* W witrynie Azure Portal znajdź blok aplikacji sieci web dla aplikacji sieci web. Wybierz **pozycję Przeglądaj,** aby wyświetlić aplikację w domyślnej przeglądarce.
* Otwórz przeglądarkę i wprowadź adres URL aplikacji internetowej. Przykład: `http://SampleWebAppDemo.azurewebsites.net`

## <a name="update-the-web-app-and-republish"></a>Aktualizowanie aplikacji sieci Web i ponowne publikowanie

Po wszczęciem zmian w kodzie lokalnym ponownie opublikuj:

1. W **Eksploratorze rozwiązań** programu Visual Studio otwórz plik *Startup.cs.*

1. W `Configure` metodzie zmodyfikuj `Response.WriteAsync` metodę tak, aby była wyświetlana w następujący sposób:

   ```csharp
   await context.Response.WriteAsync("Hello World! Deploy to Azure.");
   ```

1. Zapisz zmiany w *Startup.cs*.

1. W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy **pozycję Rozwiązanie "SampleWebAppDemo"** i wybierz polecenie **Zatwierdź**. Zostanie wyświetlone okno **Team Explorer**.

1. Wprowadź komunikat zatwierdzania, `Update #2`na przykład .

1. Naciśnij przycisk **Zatwierdzj,** aby zatwierdzić zmiany projektu.

1. Wybierz **pozycję Push** > **Akcje** > **synchronizacji** > **domowej**.

> [!NOTE]
> Alternatywnie należy wypchnąć zmiany z **okna polecenia,** otwierając **okno polecenia**, zmieniając katalog projektu i wprowadzając polecenie git. Przykład:
> 
> `git push -u Azure-SampleApp master`

## <a name="view-the-updated-web-app-in-azure"></a>Wyświetlanie zaktualizowanej aplikacji sieci Web na platformie Azure

Wyświetl zaktualizowaną aplikację sieci web, wybierając **pozycję Przeglądaj** z bloku aplikacji sieci Web w witrynie Azure Portal lub otwierając przeglądarkę i wprowadzając adres URL aplikacji sieci web. Przykład: `http://SampleWebAppDemo.azurewebsites.net`

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Tworzenie pierwszego potoku za pomocą usługi Azure Pipelines](/azure/devops/pipelines/get-started-yaml)
* [Projekt Kudu](https://github.com/projectkudu/kudu/wiki)
* <xref:host-and-deploy/visual-studio-publish-profiles>
