---
title: Wdrażanie aplikacji w usłudze App Service DevOps przy użyciu ASP.NET Core i platformy Azure
author: CamSoper
description: Wdróż aplikację ASP.NET Core do Azure App Service, pierwszy krok dla DevOps z ASP.NET Core i platformą Azure.
ms.author: casoper
ms.custom: devx-track-csharp, mvc, seodec18
ms.date: 10/24/2018
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: azure/devops/deploy-to-app-service
ms.openlocfilehash: f27c33d692512130ba0a36e9d00d46dffe90da16
ms.sourcegitcommit: ba4872dd5a93780fe6cfacb2711ec1e69e0df92c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/12/2020
ms.locfileid: "88130239"
---
# <a name="deploy-an-app-to-app-service"></a>Wdróż aplikację w App Service

[Azure App Service](/azure/app-service/) to platforma hostingu w sieci Web platformy Azure. Wdrażanie aplikacji sieci Web do Azure App Service można wykonać ręcznie lub przez proces zautomatyzowany. W tej części przewodnika omówiono metody wdrażania, które mogą być wyzwalane ręcznie lub przez skrypt przy użyciu wiersza polecenia lub wyzwalane ręcznie przy użyciu programu Visual Studio.

W tej sekcji zostaną wykonane następujące zadania:

* Pobierz aplikację przykładową i skompiluj ją.
* Utwórz Azure App Service aplikację sieci Web przy użyciu Azure Cloud Shell.
* Wdróż przykładową aplikację na platformie Azure przy użyciu narzędzia Git.
* Wdróż zmianę w aplikacji przy użyciu programu Visual Studio.
* Dodaj miejsce przejściowe do aplikacji sieci Web.
* Wdróż aktualizację do miejsca przejściowego.
* Zamień miejsce przejściowe i miejsce produkcyjne.

## <a name="download-and-test-the-app"></a>Pobieranie i testowanie aplikacji

Aplikacja używana w tym przewodniku jest wstępnie zbudowaną aplikacją ASP.NET Core, [prostym czytnikiem strumieniowego źródła danych](https://github.com/Azure-Samples/simple-feed-reader/). Jest to Razor aplikacja ze stronami, która używa `Microsoft.SyndicationFeed.ReaderWriter` interfejsu API do pobierania źródła danych RSS/Atom i wyświetlania elementów wiadomości na liście.

Możesz przejrzeć kod, ale ważne jest, aby zrozumieć, że nie ma żadnych specjalnych informacji o tej aplikacji. Jest to tylko prosta aplikacja ASP.NET Core do celów informacyjnych.

Z poziomu powłoki poleceń Pobierz kod, Skompiluj projekt i uruchom go w następujący sposób.

> *Uwaga: Użytkownicy systemu Linux/macOS powinni wprowadzać odpowiednie zmiany w ścieżkach, np. przy użyciu ukośnika ( `/` ), a nie ukośnika odwrotnego ( `\` ).*

1. Sklonuj kod do folderu na komputerze lokalnym.

    ```console
    git clone https://github.com/Azure-Samples/simple-feed-reader/
    ```

2. Zmień folder roboczy na utworzony folder *czytnika źródła danych* .

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

    ![Pomyślnie uruchomiono polecenie dotnet](./media/deploying-to-app-service/dotnet-run.png)

5. Otwórz przeglądarkę i przejdź pod adres `http://localhost:5000`. Aplikacja umożliwia wpisanie lub wklejenie adresu URL źródła zespolonego oraz wyświetlenie listy elementów wiadomości.

     ![Aplikacja wyświetlająca zawartość kanału informacyjnego RSS](./media/deploying-to-app-service/app-in-browser.png)

6. Po upewnieniu się, że aplikacja działa prawidłowo, zamknij ją, naciskając klawisz **Ctrl** + **C** w powłoce poleceń.

## <a name="create-the-azure-app-service-web-app"></a>Tworzenie aplikacji sieci Web Azure App Service

Aby wdrożyć aplikację, musisz utworzyć App Service [aplikację sieci Web](/azure/app-service/app-service-web-overview). Po utworzeniu aplikacji sieci Web zostanie ona wdrożona na komputerze lokalnym za pomocą usługi git.

1. Zaloguj się do usługi [Azure Cloud Shell](https://shell.azure.com/bash). Uwaga: po pierwszym zalogowaniu Cloud Shell zostanie wyświetlony komunikat z prośbą o utworzenie konta magazynu dla plików konfiguracji. Zaakceptuj wartości domyślne lub podaj unikatową nazwę.

2. Użyj Cloud Shell, aby wykonać następujące czynności.

    a. Zadeklaruj zmienną do przechowywania nazwy aplikacji sieci Web. Nazwa musi być unikatowa, aby można jej było używać w domyślnym adresie URL. Użycie `$RANDOM` funkcji bash do konstruowania nazwy gwarantuje unikatowość i wyniki w formacie `webappname99999` .

    ```console
    webappname=mywebapp$RANDOM
    ```

    b. Utwórz grupę zasobów. Grupy zasobów zapewniają metodę agregowania zasobów platformy Azure, która ma być zarządzana jako Grupa.

    ```azurecli
    az group create --location centralus --name AzureTutorial
    ```

    `az`Polecenie wywołuje [interfejs wiersza polecenia platformy Azure](/cli/azure/). Interfejs wiersza polecenia może być uruchamiany lokalnie, ale jego użycie w Cloud Shell pozwala zaoszczędzić czas i konfigurację.

    c. Utwórz plan App Service w warstwie S1. Plan App Service jest grupą aplikacji sieci Web, które współużytkują tę samą warstwę cenową. Warstwa S1 nie jest bezpłatna, ale jest wymagana w przypadku funkcji miejsc przejściowych.

    ```azurecli
    az appservice plan create --name $webappname --resource-group AzureTutorial --sku S1
    ```

    d. Utwórz zasób aplikacji sieci Web przy użyciu planu App Service w tej samej grupie zasobów.

    ```azurecli
    az webapp create --name $webappname --resource-group AzureTutorial --plan $webappname
    ```

    e. Ustaw poświadczenia wdrożenia. Te poświadczenia wdrażania dotyczą wszystkich aplikacji sieci Web w Twojej subskrypcji. Nie używaj znaków specjalnych w nazwie użytkownika.

    ```azurecli
    az webapp deployment user set --user-name REPLACE_WITH_USER_NAME --password REPLACE_WITH_PASSWORD
    ```

    f. Skonfiguruj aplikację sieci Web tak, aby akceptowała wdrożenia z lokalnego narzędzia Git i wyświetlała *adres URL wdrożenia narzędzia Git*. **Zanotuj ten adres URL później**.

    ```azurecli
    echo Git deployment URL: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --query url --output tsv)
    ```

    przykład Wyświetl *adres URL aplikacji sieci Web*. Przejdź do tego adresu URL, aby wyświetlić pustą aplikację sieci Web. **Zanotuj ten adres URL później**.

    ```console
    echo Web app URL: http://$webappname.azurewebsites.net
    ```

3. Przy użyciu powłoki poleceń na komputerze lokalnym przejdź do folderu projektu aplikacji sieci Web (na przykład `.\simple-feed-reader\SimpleFeedReader` ). Wykonaj następujące polecenia, aby skonfigurować usługę git do wypychania do adresu URL wdrożenia:

    a. Dodaj zdalny adres URL do repozytorium lokalnego.

    ```console
    git remote add azure-prod GIT_DEPLOYMENT_URL
    ```

    b. Wypchnij lokalną gałąź *główną* do *głównej* gałęzi *Azure-prod* zdalnego.

    ```console
    git push azure-prod master
    ```

    Zostanie wyświetlony monit o utworzenie wcześniej utworzonych poświadczeń wdrożenia. Obserwuj dane wyjściowe w powłoce poleceń. Platforma Azure kompiluje aplikację ASP.NET Core zdalnie.

4. W przeglądarce przejdź do *adresu URL aplikacji sieci Web* i zanotuj, że aplikacja została skompilowana i wdrożona. Dodatkowe zmiany można zatwierdzić w lokalnym repozytorium git przy użyciu programu `git commit` . Te zmiany są przekazywane do platformy Azure przy użyciu poprzedniego `git push` polecenia.

## <a name="deployment-with-visual-studio"></a>Wdrażanie za pomocą programu Visual Studio

> *Uwaga: Ta sekcja dotyczy tylko systemu Windows. Użytkownicy systemów Linux i macOS powinni wprowadzić zmiany opisane w kroku 2 poniżej. Zapisz plik i zatwierdź zmianę w repozytorium lokalnym za pomocą programu `git commit` . Na koniec wypchnij zmiany z `git push` , jak w pierwszej sekcji.*

Aplikacja została już wdrożona z poziomu powłoki poleceń. Użyjmy zintegrowanych narzędzi programu Visual Studio, aby wdrożyć aktualizację do aplikacji. W tle program Visual Studio wykonuje te same czynności co w przypadku narzędzi wiersza polecenia, ale w znanym interfejsie użytkownika programu Visual Studio.

1. Otwórz *SimpleFeedReader. sln* w programie Visual Studio.
2. W Eksplorator rozwiązań Otwórz *Pages\Index.cshtml*. Zmień `<h2>Simple Feed Reader</h2>` na `<h2>Simple Feed Reader - V2</h2>` .
3. Naciśnij **klawisze CTRL** + **SHIFT** + **B** , aby skompilować aplikację.
4. W Eksplorator rozwiązań kliknij prawym przyciskiem myszy projekt, a następnie kliknij pozycję **Publikuj**.

    ![Zrzut ekranu przedstawiający kliknięcie prawym przyciskiem myszy, Publikuj](./media/deploying-to-app-service/publish.png)
5. Program Visual Studio może utworzyć nowy zasób App Service, ale ta aktualizacja zostanie opublikowana w ramach istniejącego wdrożenia. W oknie dialogowym **Wybieranie elementu docelowego publikowania** wybierz pozycję **App Service** z listy po lewej stronie, a następnie wybierz pozycję **Wybierz istniejące**. Kliknij przycisk **Opublikuj**.
6. W oknie dialogowym **App Service** upewnij się, że konto Microsoft lub organizacyjne używane do tworzenia subskrypcji platformy Azure jest wyświetlane w prawym górnym rogu. Jeśli nie, kliknij listę rozwijaną i Dodaj ją.
7. Upewnij się, że wybrano poprawną **subskrypcję** platformy Azure. W obszarze **Widok**wybierz pozycję **Grupa zasobów**. Rozwiń grupę zasobów **AzureTutorial** , a następnie wybierz istniejącą aplikację sieci Web. Kliknij przycisk **OK**.

    ![Zrzut ekranu przedstawiający okno dialogowe publikowania App Service](./media/deploying-to-app-service/publish-dialog.png)

Program Visual Studio kompiluje i wdraża aplikację na platformie Azure. Przejdź do adresu URL aplikacji sieci Web. Sprawdź, czy `<h2>` Modyfikacja elementu jest na żywo.

![Aplikacja ze zmienionym tytułem](./media/deploying-to-app-service/app-v2.png)

## <a name="deployment-slots"></a>Miejsca wdrożenia

Miejsca wdrożenia obsługują przemieszczanie zmian bez wpływu na działanie aplikacji w środowisku produkcyjnym. Po sprawdzeniu przez zespół zapewnienia jakości przygotowanej wersji aplikacji można wymienić miejsca produkcyjne i przejściowe. W ten sposób aplikacja do przemieszczania jest podwyższana do produkcji. Poniższe kroki tworzą miejsce przejściowe, wdrażają w nim pewne zmiany i wymieniają miejsce przejściowe w środowisku produkcyjnym po weryfikacji.

1. Zaloguj się do [Azure Cloud Shell](https://shell.azure.com/bash), jeśli jeszcze nie jest zalogowany.
2. Utwórz miejsce przejściowe.

    a. Utwórz miejsce wdrożenia o nazwie *tymczasowej*.

    ```azurecli
    az webapp deployment slot create --name $webappname --resource-group AzureTutorial --slot staging
    ```

    b. Skonfiguruj miejsce przejściowe tak, aby korzystało z wdrożenia z lokalnego narzędzia Git i uzyskać adres URL wdrożenia **przemieszczania** . **Zanotuj ten adres URL później**.

    ```azurecli
    echo Git deployment URL for staging: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --slot staging --query url --output tsv)
    ```

    c. Wyświetl adres URL miejsca przemieszczania. Przejdź do adresu URL, aby wyświetlić puste miejsce przejściowe. **Zanotuj ten adres URL później**.

    ```console
    echo Staging web app URL: http://$webappname-staging.azurewebsites.net
    ```

3. W edytorze tekstu lub programie Visual Studio zmodyfikuj ponownie *strony/index. cshtml* , aby element był `<h2>` odczytywany `<h2>Simple Feed Reader - V3</h2>` i zapisywał plik.

4. Zatwierdź plik w lokalnym repozytorium git, używając strony **zmiany** w karcie *Team Explorer* w programie Visual Studio lub wprowadzając następujące polecenie przy użyciu powłoki poleceń komputera lokalnego:

    ```console
    git commit -a -m "upgraded to V3"
    ```

5. Przy użyciu powłoki poleceń komputera lokalnego Dodaj adres URL wdrożenia przemieszczania jako zdalny element git i wypchnij zatwierdzone zmiany:

    a. Dodaj zdalny adres URL na potrzeby przemieszczania do lokalnego repozytorium git.

    ```console
    git remote add azure-staging <Git_staging_deployment_URL>
    ```

    b. Wypchnij lokalną gałąź *główną* do *głównej* gałęzi zdalnej *platformy Azure* .

    ```console
    git push azure-staging master
    ```

    Zaczekaj, aż platforma Azure utworzy i wdroży aplikację.

6. Aby sprawdzić, czy wersja 3 została wdrożona w miejscu przejściowym, Otwórz dwa okna przeglądarki. W jednym oknie przejdź do oryginalnego adresu URL aplikacji sieci Web. W drugim oknie przejdź do adresu URL aplikacji sieci Web przemieszczania. Produkcyjny adres URL służy do wdrożenia wersji 2 aplikacji. Przejściowy adres URL służy do działania wersji 3 aplikacji.

    ![Zrzut ekranu porównujący okna przeglądarki](./media/deploying-to-app-service/ready-to-swap.png)

7. W Cloud Shell zamienić zweryfikowane i rozgrzane miejsce przejściowe na środowisko produkcyjne.

    ```azurecli
    az webapp deployment slot swap --name $webappname --resource-group AzureTutorial --slot staging
    ```

8. Sprawdź, czy nastąpiło zastępowanie, odświeżając dwa okna przeglądarki.

    ![Porównywanie okien przeglądarki po wymianie](./media/deploying-to-app-service/swapped.png)

## <a name="summary"></a>Podsumowanie

W tej sekcji zostały wykonane następujące zadania:

* Pobrano i skompilowano przykładową aplikację.
* Utworzono aplikację internetową Azure App Service przy użyciu Azure Cloud Shell.
* Wdrożono przykładową aplikację na platformie Azure przy użyciu narzędzia Git.
* Wdrożono zmianę aplikacji przy użyciu programu Visual Studio.
* Dodano miejsce przejściowe do aplikacji sieci Web.
* Wdrożono aktualizację do miejsca przejściowego.
* Zamienione miejsca przejściowe i produkcyjne.

W następnej sekcji dowiesz się, jak utworzyć potok DevOps za pomocą Azure Pipelines.

## <a name="additional-reading"></a>Materiały uzupełniające

* [Przegląd Web Apps](/azure/app-service/app-service-web-overview)
* [Tworzenie aplikacji internetowej platformy .NET Core i usługi SQL Database w usłudze Azure App Service](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [Skonfiguruj poświadczenia wdrażania dla Azure App Service](/azure/app-service/app-service-deployment-credentials)
* [Konfigurowanie środowisk przejściowych w usłudze Azure App Service](/azure/app-service/web-sites-staged-publishing)
