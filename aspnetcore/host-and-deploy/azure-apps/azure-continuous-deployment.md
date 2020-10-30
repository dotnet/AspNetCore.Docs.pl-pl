---
title: Ciągłe wdrażanie na platformie Azure za pomocą programu Visual Studio i rozwiązania Git na platformie ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak utworzyć aplikację internetową ASP.NET Core przy użyciu programu Visual Studio i wdrożyć ją do Azure App Service przy użyciu narzędzia Git do ciągłego wdrażania.
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 12/06/2018
no-loc:
- appsettings.json
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
uid: host-and-deploy/azure-apps/azure-continuous-deployment
ms.openlocfilehash: 1485e1a97e51bb4d12d0d74ca1550e5cf18258d8
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053790"
---
# <a name="continuous-deployment-to-azure-with-visual-studio-and-git-with-aspnet-core"></a>Ciągłe wdrażanie na platformie Azure za pomocą programu Visual Studio i rozwiązania Git na platformie ASP.NET Core

Autor [Erik Reitan](https://github.com/Erikre)

[!INCLUDE [Azure App Service Preview Notice](../../includes/azure-apps-preview-notice.md)]

W tym samouczku pokazano, jak utworzyć aplikację internetową ASP.NET Core przy użyciu programu Visual Studio i wdrożyć ją z poziomu programu Visual Studio w celu Azure App Service korzystania z ciągłego wdrażania.

Zobacz również [Tworzenie pierwszego potoku za pomocą Azure Pipelines](/azure/devops/pipelines/get-started-yaml), który pokazuje, jak skonfigurować przepływ pracy ciągłego dostarczania (CD) dla [Azure App Service](/azure/app-service/app-service-web-overview) przy użyciu Azure DevOps Services. Azure Pipelines (usługa Azure DevOps Services) upraszcza Konfigurowanie niezawodnego potoku wdrażania w celu publikowania aktualizacji dla aplikacji hostowanych w Azure App Service. Potok można skonfigurować z poziomu Azure Portal, aby kompilować, uruchamiać testy, wdrażać w miejscu przejściowym, a następnie wdrażać je w środowisku produkcyjnym.

> [!NOTE]
> Do ukończenia tego samouczka wymagane jest konto Microsoft Azure. Aby uzyskać konto, [Aktywuj korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A261C142F) lub [zarejestruj się w celu uzyskania bezpłatnej wersji próbnej](https://azure.microsoft.com/free/dotnet/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku założono, że zainstalowano następujące oprogramowanie:

* [Visual Studio](https://visualstudio.microsoft.com)
* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]
* [Git](https://git-scm.com/downloads) dla systemu Windows

## <a name="create-an-aspnet-core-web-app"></a>Tworzenie aplikacji internetowej ASP.NET Core

1. Uruchom program Visual Studio.

1. Z menu **plik** wybierz pozycję **Nowy**  >  **projekt** .

1. Wybierz szablon projektu **aplikacji sieci Web ASP.NET Core** . Jest on wyświetlany w obszarze **zainstalowane**  >  **Szablony**  >  **Visual C#**  >  **.NET Core** . Nadaj nazwę projektowi `SampleWebAppDemo` . Wybierz opcję **Utwórz nowe repozytorium git** , a następnie kliknij przycisk **OK** .

   ![Okno dialogowe Nowy projekt](azure-continuous-deployment/_static/01-new-project.png)

1. W oknie dialogowym **Nowy projekt ASP.NET Core** wybierz ASP.NET Core **pusty** szablon, a następnie kliknij przycisk **OK** .

   ![Nowe okno dialogowe projektu ASP.NET Core](azure-continuous-deployment/_static/02-web-site-template.png)

> [!NOTE]
> Najnowsza wersja platformy .NET Core to 2,0.

### <a name="running-the-web-app-locally"></a>Lokalne uruchamianie aplikacji sieci Web

1. Po zakończeniu tworzenia aplikacji przez program Visual Studio Uruchom aplikację, wybierając pozycję **Debuguj**  >  **Rozpocznij debugowanie** . Alternatywnie naciśnij klawisz **F5** .

   Zainicjowanie programu Visual Studio i nowej aplikacji może zająć trochę czasu. Po zakończeniu przeglądarka wyświetli działającą aplikację.

   ![Okno przeglądarki z uruchomioną aplikacją, która wyświetla "Hello world!"](azure-continuous-deployment/_static/04-browser-runapp.png)

1. Po przejrzeniu działającej aplikacji sieci Web zamknij przeglądarkę i wybierz ikonę "Zatrzymaj debugowanie" na pasku narzędzi programu Visual Studio, aby zatrzymać aplikację.

## <a name="create-a-web-app-in-the-azure-portal"></a>Tworzenie aplikacji sieci Web w witrynie Azure Portal

Poniższe kroki tworzą aplikację sieci Web w witrynie Azure Portal:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).

1. Wybierz pozycję **Nowy** w lewym górnym rogu interfejsu portalu.

1. Wybierz pozycję **Sieć Web + aplikacje mobilne**  >  **aplikacji sieci Web** .

   ![Portal Microsoft Azure: nowy przycisk: Sieć Web + aplikacje mobilne na rynku Marketplace: przycisk aplikacji sieci Web w obszarze Polecane aplikacje](azure-continuous-deployment/_static/05-azure-newwebapp.png)

1. W bloku **aplikacja sieci Web** wprowadź unikatową wartość dla **nazwy App Service** .

   ![Blok aplikacji sieci Web](azure-continuous-deployment/_static/06-azure-newappblade.png)

   > [!NOTE]
   > Nazwa **App Service nazwa** musi być unikatowa. Portal wymusza tę regułę w przypadku podanej nazwy. W przypadku podania innej wartości należy zastąpić tę wartość dla każdego wystąpienia **SampleWebAppDemo** w tym samouczku.

   W bloku **aplikacja sieci Web** wybierz istniejący **Plan/lokalizację App Service** lub Utwórz nowy. W przypadku tworzenia nowego planu wybierz warstwę cenową, lokalizację i inne opcje. Więcej informacji o planach App Service można znaleźć [w temacie Azure App Service planach szczegółowych](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview).

1. Wybierz pozycję **Utwórz** . Na platformie Azure zostanie zainicjowana i uruchomiona aplikacja internetowa.

   ![Azure Portal: przykładowy Przykładowa aplikacja internetowa — Demonstracja 01 — blok](azure-continuous-deployment/_static/07-azure-webappblade.png)

## <a name="enable-git-publishing-for-the-new-web-app"></a>Włącz publikowanie git dla nowej aplikacji sieci Web

Git to rozproszony system kontroli wersji, którego można użyć do wdrożenia aplikacji sieci Web Azure App Service. Kod aplikacji sieci Web jest przechowywany w lokalnym repozytorium git, a kod jest wdrażany na platformie Azure przez wypychanie do repozytorium zdalnego.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).

1. Wybierz pozycję **App Services** , aby wyświetlić listę usług aplikacji skojarzonych z subskrypcją platformy Azure.

1. Wybierz aplikację sieci Web utworzoną w poprzedniej sekcji tego samouczka.

1. W bloku **wdrożenie** wybierz pozycję **Opcje wdrażania**  >  **Wybierz pozycję źródłowe**  >  **lokalne repozytorium git** .

   ![Blok ustawień: blok źródła wdrożenia: Wybierz blok źródłowy](azure-continuous-deployment/_static/deployment-options.png)

1. Wybierz pozycję **OK** .

1. Jeśli nie skonfigurowano wcześniej poświadczeń wdrażania do publikowania aplikacji internetowej lub innej aplikacji App Service, skonfiguruj je teraz:

   * Wybierz pozycję **Ustawienia**  >  **poświadczenia wdrożenia** . Zostanie wyświetlony blok **Ustawianie poświadczeń wdrożenia** .
   * Utwórz nazwę użytkownika i hasło. Zapisz hasło do późniejszego użycia podczas konfigurowania usługi git.
   * Wybierz pozycję **Zapisz** .

1. W bloku **aplikacja sieci Web** wybierz pozycję **Ustawienia**  >  **Właściwości** . Adres URL zdalnego repozytorium git do wdrożenia zostanie wyświetlony w obszarze **adres URL usługi git** .

1. Skopiuj wartość **adresu URL usługi git** do późniejszego użycia w samouczku.

   ![Azure Portal: blok właściwości aplikacji](azure-continuous-deployment/_static/09-azure-giturl.png)

## <a name="publish-the-web-app-to-azure-app-service"></a>Publikowanie aplikacji internetowej w usłudze Azure App Service

W tej sekcji Utwórz lokalne repozytorium git przy użyciu programu Visual Studio i wypchnij z tego repozytorium do platformy Azure, aby wdrożyć aplikację internetową. Obejmują one następujące czynności:

* Dodaj ustawienie repozytorium zdalne przy użyciu wartości adresu URL usługi GIT, aby można było wdrożyć repozytorium lokalne na platformie Azure.
* Zatwierdź zmiany projektu.
* Wypychanie zmian projektu z repozytorium lokalnego do zdalnego repozytorium na platformie Azure.

1. W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy **rozwiązanie "SampleWebAppDemo"** i wybierz pozycję **Zatwierdź** . Zostanie wyświetlone okno **Team Explorer** .

   ![Karta Team Explorer Connect](azure-continuous-deployment/_static/10-team-explorer.png)

1. W **Team Explorer** wybierz pozycję **Strona główna** (ikona główna) > **Settings**  >  **Ustawienia repozytorium** ustawień.

1. W sekcji **Elementy zdalne** obszaru **Ustawienia repozytorium** wybierz pozycję **Dodaj** . Zostanie wyświetlone okno dialogowe **Dodawanie elementu zdalnego** .

1. Ustaw **nazwę** zdalnego na **Azure-SampleApp** .

1. Ustaw wartość w polu **Pobierz** na **adres URL usługi git** , który został skopiowany z platformy Azure wcześniej w tym samouczku. Należy zauważyć, że jest to adres URL kończący się na **. git** .

   ![Edytowanie okna dialogowego zdalnego](azure-continuous-deployment/_static/11-add-remote.png)

   > [!NOTE]
   > Alternatywnie należy określić repozytorium zdalne z **okna polecenia** , otwierając **okno wiersza polecenia** , zmieniając katalog projektu i wprowadzając polecenie. Przykład:
   >
   > `git remote add Azure-SampleApp https://me@sampleapp.scm.azurewebsites.net:443/SampleApp.git`

1. Wybierz pozycję **Strona główna** (ikona główna) ustawienia > **Ustawienia**  >  **globalne** . Upewnij się, że nazwa i adres e-mail zostały ustawione. W razie potrzeby wybierz pozycję **Aktualizuj** .

1. Wybierz **Home** pozycję  >  **zmiany** główne, aby powrócić do widoku **zmian** .

1. Wprowadź wiadomość dotyczącą zatwierdzenia, taką jak **początkowa #1 wypychania** i wybierz pozycję **Zatwierdź** . Ta akcja tworzy *zatwierdzenie* lokalnie.

   ![Karta Team Explorer Connect](azure-continuous-deployment/_static/12-initial-commit.png)

   > [!NOTE]
   > Alternatywnie Zatwierdź zmiany z **okna polecenia** , otwierając **okno poleceń** , zmieniając katalog projektu i wprowadzając polecenia git. Przykład:
   >
   > `git add .`
   >
   > `git commit -am "Initial Push #1"`

1. Wybierz **Home** pozycję  >  **Sync**  >  **Akcje** synchronizacji głównej  >  **Otwórz wiersz polecenia** . Zostanie otwarty wiersz polecenia w katalogu projektu.

1. Wprowadź następujące polecenie w oknie polecenia:

   `git push -u Azure-SampleApp master`

1. Wprowadź hasło **poświadczeń wdrożenia** platformy Azure utworzone wcześniej na platformie Azure.

   To polecenie uruchamia proces wypychania plików projektu lokalnego do platformy Azure. Dane wyjściowe powyższego polecenia kończą się komunikatem, że wdrożenie zakończyło się pomyślnie.

   ```
   remote: Finished successfully.
   remote: Running post deployment command(s)...
   remote: Deployment successful.
   To https://username@samplewebappdemo01.scm.azurewebsites.net:443/SampleWebAppDemo01.git
   * [new branch]      master -> master
   Branch master set up to track remote branch master from Azure-SampleApp.
   ```

   > [!NOTE]
   > Jeśli wymagana jest współpraca w projekcie, przed rozpoczęciem wypychania do platformy Azure Rozważ przeprowadzenie wypychania do serwisu [GitHub](https://github.com) .
 
### <a name="verify-the-active-deployment"></a>Weryfikowanie aktywnego wdrożenia

Sprawdź, czy transfer aplikacji sieci Web ze środowiska lokalnego na platformę Azure zakończył się pomyślnie.

W [witrynie Azure Portal](https://portal.azure.com)wybierz aplikację internetową. Wybierz **Deployment** pozycję  >  **Opcje wdrażania** wdrożenia.

![Witryna Azure Portal: blok ustawień: blok wdrożenia przedstawiający pomyślne wdrożenie](azure-continuous-deployment/_static/13-verify-deployment.png)

## <a name="run-the-app-in-azure"></a>Uruchamianie aplikacji na platformie Azure

Teraz, gdy aplikacja sieci Web jest wdrożona na platformie Azure, uruchom aplikację.

Można to zrobić na dwa sposoby:

* W witrynie Azure Portal Znajdź blok aplikacji sieci Web dla aplikacji sieci Web. Wybierz pozycję **Przeglądaj** , aby wyświetlić aplikację w domyślnej przeglądarce.
* Otwórz przeglądarkę i wprowadź adres URL aplikacji sieci Web. Przykład: `http://SampleWebAppDemo.azurewebsites.net`

## <a name="update-the-web-app-and-republish"></a>Aktualizowanie aplikacji sieci Web i ponowne publikowanie

Po wprowadzeniu zmian w kodzie lokalnym należy ponownie opublikować:

1. W **Eksplorator rozwiązań** programu Visual Studio otwórz plik *Startup.cs* .

1. W `Configure` metodzie zmodyfikuj metodę, `Response.WriteAsync` tak aby była wyświetlana w następujący sposób:

   ```csharp
   await context.Response.WriteAsync("Hello World! Deploy to Azure.");
   ```

1. Zapisz zmiany w *Startup.cs* .

1. W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy **rozwiązanie "SampleWebAppDemo"** i wybierz pozycję **Zatwierdź** . Zostanie wyświetlone okno **Team Explorer** .

1. Wprowadź komunikat dotyczący zatwierdzenia, taki jak `Update #2` .

1. Naciśnij przycisk **Zatwierdź** , aby zatwierdzić zmiany projektu.

1. Wybierz **Home** pozycję  >  **Sync**  >  **Akcje** synchronizacji narzędzia  >  **wypychanie** .

> [!NOTE]
> Alternatywnie wypchnij zmiany z **okna polecenia** , otwierając **okno wiersza polecenia** , zmieniając katalog projektu i wprowadzając polecenie git. Przykład:
> 
> `git push -u Azure-SampleApp master`

## <a name="view-the-updated-web-app-in-azure"></a>Wyświetlanie zaktualizowanej aplikacji sieci Web na platformie Azure

Aby wyświetlić zaktualizowaną aplikację sieci Web, wybierz pozycję **Przeglądaj** w bloku aplikacji sieci Web w witrynie Azure Portal lub przez otwarcie przeglądarki i wprowadzenie adresu URL aplikacji sieci Web. Przykład: `http://SampleWebAppDemo.azurewebsites.net`

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Tworzenie pierwszego potoku za pomocą Azure Pipelines](/azure/devops/pipelines/get-started-yaml)
* [Projekt Kudu](https://github.com/projectkudu/kudu/wiki)
* <xref:host-and-deploy/visual-studio-publish-profiles>
