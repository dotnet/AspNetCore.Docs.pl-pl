---
title: Wdrażanie aplikacji w usłudze app service — DevOps z ASP.NET Core i platformą Azure
author: CamSoper
description: Wdrażanie aplikacji ASP.NET Core w usłudze Azure App Service, pierwszym kroku dla usługi DevOps z ASP.NET Core i platformą Azure.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/deploy-to-app-service
ms.openlocfilehash: df41f296e9c4e1eff6e31d45b29ec30ee1e20cf4
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657746"
---
# <a name="deploy-an-app-to-app-service"></a>Wdrażanie aplikacji w usłudze app service

[Usługa Azure App Service](/azure/app-service/) to platforma hostingowa platformy Azure. Wdrażanie aplikacji sieci web w usłudze Azure App Service można wykonać ręcznie lub za pomocą zautomatyzowanego procesu. W tej sekcji przewodnika omówiono metody wdrażania, które mogą być wyzwalane ręcznie lub za pomocą skryptu przy użyciu wiersza polecenia lub wyzwalane ręcznie przy użyciu programu Visual Studio.

W tej sekcji wykonasz następujące zadania:

* Pobierz i skompiluj przykładową aplikację.
* Utwórz aplikację sieci Web usługi Azure App Service przy użyciu usługi Azure Cloud Shell.
* Wdrażanie przykładowej aplikacji na platformie Azure przy użyciu git.
* Wdrażanie zmiany w aplikacji za pomocą programu Visual Studio.
* Dodaj miejsce przejściowe do aplikacji sieci web.
* Wdrażanie aktualizacji do miejsca przejściowego.
* Zamień miejsce przejściowe i miejsce produkcyjne.

## <a name="download-and-test-the-app"></a>Pobieranie i testowanie aplikacji

Aplikacja używana w tym przewodniku jest wstępnie zbudowany ASP.NET Core aplikacji, [Simple Feed Reader](https://github.com/Azure-Samples/simple-feed-reader/). Jest to aplikacja Razor Pages, `Microsoft.SyndicationFeed.ReaderWriter` która używa interfejsu API do pobierania kanału RSS/Atom i wyświetlania elementów wiadomości na liście.

Zapraszam do przeglądania kodu, ale ważne jest, aby zrozumieć, że nie ma nic specjalnego w tej aplikacji. To po prostu prosta aplikacja ASP.NET Core w celach ilustracyjnych.

Z powłoki poleceń pobierz kod, skompiluj projekt i uruchom go w następujący sposób.

> *Uwaga: Użytkownicy systemów Linux/macOS powinni wprowadzić odpowiednie zmiany w ścieżkach, np.`/``\`*

1. Sklonuj kod do folderu na komputerze lokalnym.

    ```console
    git clone https://github.com/Azure-Samples/simple-feed-reader/
    ```

2. Zmień folder roboczy na utworzony folder *prostego czytnika kanałów informacyjnych.*

    ```console
    cd .\simple-feed-reader\SimpleFeedReader
    ```

3. Przywróć pakiety i skompiluj rozwiązanie.

    ```dotnetcli
    dotnet build
    ```

4. Uruchom aplikację.

    ```dotnetcli
    dotnet run
    ```

    ![Polecenie dotnet run zakończyło się pomyślnie](./media/deploying-to-app-service/dotnet-run.png)

5. Otwórz przeglądarkę i przejdź pod adres `http://localhost:5000`. Aplikacja pozwala wpisać lub wkleić adres URL źródła danych zesłania i wyświetlić listę elementów wiadomości.

     ![Aplikacja wyświetlająca zawartość kanału RSS](./media/deploying-to-app-service/app-in-browser.png)

6. Po upewnieniu się, że aplikacja działa poprawnie, zamknij ją, naciskając **klawisz Ctrl**+**C** w powłoce poleceń.

## <a name="create-the-azure-app-service-web-app"></a>Tworzenie aplikacji sieci Web usługi Azure App Service

Aby wdrożyć aplikację, musisz utworzyć aplikację [sieci Web](/azure/app-service/app-service-web-overview)usługi app. Po utworzeniu aplikacji sieci Web, można wdrożyć do niego z komputera lokalnego przy użyciu git.

1. Zaloguj się do usługi [Azure Cloud Shell](https://shell.azure.com/bash). Uwaga: Gdy zalogujesz się po raz pierwszy, usługa Cloud Shell wyświetli monit o utworzenie konta magazynu dla plików konfiguracyjnych. Zaakceptuj ustawienia domyślne lub podaj unikatową nazwę.

2. Użyj powłoki chmury dla następujących kroków.

    a. Zadeklaruj zmienną do przechowywania nazwy aplikacji sieci web. Nazwa musi być unikatowa, aby była używana w domyślnym adresie URL. `$RANDOM` Za pomocą bash funkcji do konstruowania nazwy `webappname99999`gwarantuje unikatowość i wyniki w formacie .

    ```console
    webappname=mywebapp$RANDOM
    ```

    b. Utwórz grupę zasobów. Grupy zasobów zapewniają środki do agregowania zasobów platformy Azure, które mają być zarządzane jako grupa.

    ```azure-cli
    az group create --location centralus --name AzureTutorial
    ```

    Polecenie `az` wywołuje interfejsu [wiersza polecenia platformy Azure](/cli/azure/). Cli można uruchomić lokalnie, ale przy użyciu go w cloud shell oszczędza czas i konfigurację.

    d. Utwórz plan usługi app service w warstwie S1. Plan usługi aplikacji to grupowanie aplikacji sieci web, które mają tę samą warstwę cenową. Warstwa S1 nie jest bezpłatna, ale jest wymagana dla funkcji miejsca przejściowe.

    ```azure-cli
    az appservice plan create --name $webappname --resource-group AzureTutorial --sku S1
    ```

    d. Utwórz zasób aplikacji sieci Web przy użyciu planu usługi app service w tej samej grupie zasobów.

    ```azure-cli
    az webapp create --name $webappname --resource-group AzureTutorial --plan $webappname
    ```

    e. Ustaw poświadczenia wdrożenia. Te poświadczenia wdrożenia dotyczą wszystkich aplikacji sieci web w ramach subskrypcji. Nie używaj znaków specjalnych w nazwie użytkownika.

    ```azure-cli
    az webapp deployment user set --user-name REPLACE_WITH_USER_NAME --password REPLACE_WITH_PASSWORD
    ```

    f. Skonfiguruj aplikację internetową tak, aby akceptowała wdrożenia z lokalnego gita i wyświetlała *adres URL wdrożenia Gita*. **Zanotuj ten adres URL w celu późniejszego odniesienia**.

    ```azure-cli
    echo Git deployment URL: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --query url --output tsv)
    ```

    g. Wyświetl *adres URL aplikacji internetowej*. Przejdź do tego adresu URL, aby wyświetlić pustą aplikację internetową. **Zanotuj ten adres URL w celu późniejszego odniesienia**.

    ```console
    echo Web app URL: http://$webappname.azurewebsites.net
    ```

3. Korzystając z powłoki poleceń na komputerze lokalnym, przejdź do folderu projektu aplikacji sieci web (na przykład `.\simple-feed-reader\SimpleFeedReader`). Wykonaj następujące polecenia, aby skonfigurować git do wypychania do adresu URL wdrożenia:

    a. Dodaj zdalny adres URL do lokalnego repozytorium.

    ```console
    git remote add azure-prod GIT_DEPLOYMENT_URL
    ```

    b. Wypchnij lokalną gałąź *wzorcową* do gałęzi *głównej* pilota *azure-prod.*

    ```console
    git push azure-prod master
    ```

    Zostanie wyświetlony monit o poświadczenia wdrożenia utworzone wcześniej. Obserwować dane wyjściowe w powłoce poleceń. Platforma Azure zdalnie tworzy aplikację ASP.NET Core.

4. W przeglądarce przejdź do *adresu URL aplikacji sieci Web* i zanotuj, że aplikacja została sbudona i wdrożona. Dodatkowe zmiany mogą zostać wprowadzone do lokalnego repozytorium Git z . `git commit` Te zmiany są wypychane na `git push` platformę Azure za pomocą poprzedniego polecenia.

## <a name="deployment-with-visual-studio"></a>Wdrażanie za pomocą programu Visual Studio

> *Uwaga: Ta sekcja dotyczy tylko systemu Windows. Użytkownicy systemów Linux i macOS powinni wprowadzić zmiany opisane w kroku 2 poniżej. Zapisz plik i zatwierdź zmianę w lokalnym `git commit`repozytorium za pomocą pliku . Na koniec naciśnij zmianę `git push`za pomocą , jak w pierwszej sekcji.*

Aplikacja została już wdrożona z powłoki poleceń. Użyjmy zintegrowanych narzędzi programu Visual Studio do wdrożenia aktualizacji w aplikacji. W tle visual studio wykonuje to samo, co narzędzia wiersza polecenia, ale w programie Visual Studio znanego interfejsu użytkownika.

1. Otwórz *simplefeedreader.sln* w programie Visual Studio.
2. W Eksploratorze rozwiązań otwórz *stronę Pages\Index.cshtml*. Zmień `<h2>Simple Feed Reader</h2>` `<h2>Simple Feed Reader - V2</h2>`na .
3. Naciśnij **klawisze Ctrl**+**Shift**+**B,** aby utworzyć aplikację.
4. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy projekt i kliknij polecenie **Publikuj**.

    ![Zrzut ekranu przedstawiający kliknięcie prawym przyciskiem myszy, Opublikuj](./media/deploying-to-app-service/publish.png)
5. Visual Studio można utworzyć nowy zasób usługi App Service, ale ta aktualizacja zostanie opublikowana w istniejącym wdrożeniu. W oknie **dialogowym Wybieranie celu publikowania** wybierz pozycję **App Service** z listy po lewej stronie, a następnie wybierz pozycję **Wybierz istniejący**. Kliknij przycisk **Opublikuj**.
6. W oknie dialogowym **Usługi aplikacji** upewnij się, że konto microsoft lub konto organizacyjne używane do tworzenia subskrypcji platformy Azure jest wyświetlane w prawym górnym rogu. Jeśli tak nie jest, kliknij pozycję rozwijaną i dodaj ją.
7. Upewnij się, że wybrano poprawną **subskrypcję** platformy Azure. W **obszarze Widok**wybierz pozycję Grupa **zasobów**. Rozwiń grupę zasobów **AzureTutorial,** a następnie wybierz istniejącą aplikację sieci web. Kliknij przycisk **OK**.

    ![Zrzut ekranu przedstawiający okno dialogowe Publikowania usługi aplikacji](./media/deploying-to-app-service/publish-dialog.png)

Visual Studio tworzy i wdraża aplikację na platformie Azure. Przejdź do adresu URL aplikacji internetowej. Sprawdź, czy `<h2>` modyfikacja elementu jest na żywo.

![Aplikacja ze zmienionym tytułem](./media/deploying-to-app-service/app-v2.png)

## <a name="deployment-slots"></a>Miejsca wdrożenia

Gniazda wdrażania obsługują przemieszczania zmian bez wpływu na aplikację działającą w produkcji. Po etapowej wersji aplikacji jest sprawdzany przez zespół zapewnienia jakości, produkcji i przemieszczania slotów mogą być zamienione. Aplikacja w przemieszczania jest promowany do produkcji w ten sposób. Poniższe kroki tworzą miejsce przejściowe, wdrażają do niego pewne zmiany i zamieniaj miejsce przejściowe z produkcją po weryfikacji.

1. Zaloguj się do [usługi Azure Cloud Shell](https://shell.azure.com/bash), jeśli nie jest jeszcze zalogowany.
2. Utwórz miejsce przejściowe.

    a. Utwórz miejsce wdrożenia o nazwie *przemieszczania*.

    ```azure-cli
    az webapp deployment slot create --name $webappname --resource-group AzureTutorial --slot staging
    ```

    b. Skonfiguruj miejsce przejściowe do używania wdrożenia z lokalnego gita i uzyskaj adres URL wdrożenia **przejściowego.** **Zanotuj ten adres URL w celu późniejszego odniesienia**.

    ```azure-cli
    echo Git deployment URL for staging: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --slot staging --query url --output tsv)
    ```

    d. Wyświetl adres URL miejsca przemieszczania. Przejdź do adresu URL, aby wyświetlić puste miejsce przemieszczania. **Zanotuj ten adres URL w celu późniejszego odniesienia**.

    ```console
    echo Staging web app URL: http://$webappname-staging.azurewebsites.net
    ```

3. W edytorze tekstu lub programie Visual Studio ponownie zmodyfikuj *pages/index.cshtml,* aby `<h2>` element odczytywana `<h2>Simple Feed Reader - V3</h2>` i zapisać plik.

4. Zaagnalizuj plik do lokalnego repozytorium Git, używając strony **Zmiany** na karcie *Eksplorator zespołu* programu Visual Studio lub wprowadzając następujące elementy przy użyciu powłoki poleceń komputera lokalnego:

    ```console
    git commit -a -m "upgraded to V3"
    ```

5. Korzystając z powłoki poleceń komputera lokalnego, dodaj adres URL wdrożenia przemieszczania jako pilot Git i wypchnij zatwierdzone zmiany:

    a. Dodaj zdalny adres URL do przemieszczania do lokalnego repozytorium Git.

    ```console
    git remote add azure-staging <Git_staging_deployment_URL>
    ```

    b. Wypchnij lokalną gałąź *wzorcową* do gałęzi *głównej* pilota zdalnego *przemieszczania azure.*

    ```console
    git push azure-staging master
    ```

    Poczekaj, aż platforma Azure skompiluje i wdroży aplikację.

6. Aby sprawdzić, czy wersja 3 została wdrożona w miejscu przejściowym, otwórz dwa okna przeglądarki. W jednym oknie przejdź do oryginalnego adresu URL aplikacji sieci Web. W drugim oknie przejdź do adresu URL aplikacji sieci web przemieszczania. Produkcyjny adres URL obsługuje w wersji 2 aplikacji. Przemieszczania adres URL obsługuje V3 aplikacji.

    ![Zrzut ekranu porównujący okna przeglądarki](./media/deploying-to-app-service/ready-to-swap.png)

7. W usłudze Cloud Shell zamienić zweryfikowane/rozgrzane miejsce postoju na produkcję.

    ```azure-cli
    az webapp deployment slot swap --name $webappname --resource-group AzureTutorial --slot staging
    ```

8. Sprawdź, czy doszło do wymiany, odświeżając dwa okna przeglądarki.

    ![Porównywanie okien przeglądarki po wymianie](./media/deploying-to-app-service/swapped.png)

## <a name="summary"></a>Podsumowanie

W tej sekcji wykonano następujące zadania:

* Pobrano i sbudowa przykładową aplikację.
* Utworzono aplikację sieci Web usługi Azure App Service przy użyciu usługi Azure Cloud Shell.
* Wdrożono przykładową aplikację na platformie Azure przy użyciu git.
* Wdrożono zmianę w aplikacji przy użyciu programu Visual Studio.
* Dodano miejsce przejściowe do aplikacji internetowej.
* Wdrożono aktualizację miejsca przejściowego.
* Zamieniliśmy miejsca przemieszczania i produkcji.

W następnej sekcji dowiesz się, jak utworzyć potok DevOps za pomocą usługi Azure Pipelines.

## <a name="additional-reading"></a>Dodatkowa lektura

* [Omówienie aplikacji sieci Web](/azure/app-service/app-service-web-overview)
* [Tworzenie aplikacji internetowej platformy .NET Core i usługi SQL Database w usłudze Azure App Service](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [Konfigurowanie poświadczeń wdrażania usługi Azure App Service](/azure/app-service/app-service-deployment-credentials)
* [Konfigurowanie środowisk przejściowych w usłudze Azure App Service](/azure/app-service/web-sites-staged-publishing)
