---
title: Ciągła integracja i wdrażanie — devops z ASP.NET Core i azure
author: CamSoper
description: Ciągła integracja i wdrażanie w programach DevOps dzięki ASP.NET Core i platformie Azure
ms.author: scaddie
ms.date: 10/24/2018
ms.custom: mvc, seodec18
uid: azure/devops/cicd
ms.openlocfilehash: 5fdf52235b49119503885f92c370dc588e809ffe
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78655835"
---
# <a name="continuous-integration-and-deployment"></a>Ciągła integracja i ciągłe wdrażanie

W poprzednim rozdziale utworzono lokalne repozytorium Git dla aplikacji Simple Feed Reader. W tym rozdziale opublikujesz ten kod w repozytorium Usługi GitHub i skonstruujesz potok usług Azure DevOps przy użyciu potoków platformy Azure. Potok umożliwia ciągłe kompilacje i wdrożenia aplikacji. Każde zatwierdzenie do repozytorium GitHub wyzwala kompilację i wdrożenie w miejscu przejściowym aplikacji Azure Web App.

W tej sekcji wykonasz następujące zadania:

* Publikowanie kodu aplikacji w usłudze GitHub
* Odłączanie lokalnego wdrożenia git
* Tworzenie organizacji usługi Azure DevOps
* Tworzenie projektu zespołowego w usługach Azure DevOps
* Tworzenie definicji kompilacji
* Tworzenie potoku wydania
* Zatwierdzanie zmian w usłudze GitHub i automatyczne wdrażanie na platformie Azure
* Sprawdzanie potoku potoku azure

## <a name="publish-the-apps-code-to-github"></a>Publikowanie kodu aplikacji w usłudze GitHub

1. Otwórz okno przeglądarki i `https://github.com`przejdź do pliku .
1. Kliknij **+** pozycję rozwijaną w nagłówku, a następnie wybierz pozycję **Nowe repozytorium:**

    ![GitHub Nowa opcja repozytorium](media/cicd/github-new-repo.png)

1. Wybierz swoje konto z listy rozwijanej **Właściciel** i wprowadź *czytnik prostego kanału informacyjnego* w polach tekstowych **nazwa repozytorium.**
1. Kliknij przycisk **Utwórz repozytorium.**
1. Otwórz powłokę poleceń komputera lokalnego. Przejdź do katalogu, w którym przechowywane jest repozytorium Git *z prostym czytnikiem kanałów.*
1. Zmień nazwę istniejącego *pilota pochodzenia* na *nadrzędny*. Wykonaj następujące polecenie:

    ```console
    git remote rename origin upstream
    ```

1. Dodaj nowy pilot *pochodzenia* wskazujący kopię repozytorium w usłudze GitHub. Wykonaj następujące polecenie:

    ```console
    git remote add origin https://github.com/<GitHub_username>/simple-feed-reader/
    ```

1. Opublikuj lokalne repozytorium Git w nowo utworzonym repozytorium GitHub. Wykonaj następujące polecenie:

    ```console
    git push -u origin master
    ```

1. Otwórz okno przeglądarki i `https://github.com/<GitHub_username>/simple-feed-reader/`przejdź do pliku . Sprawdź, czy kod jest wyświetlany w repozytorium Usługi GitHub.

## <a name="disconnect-local-git-deployment"></a>Odłączanie lokalnego wdrożenia git

Usuń lokalne wdrożenie Git z następującymi krokami. Usługi Azure Pipelines (usługa Azure DevOps) zastępuje i rozszerza tę funkcjonalność.

1. Otwórz [witrynę Azure portal](https://portal.azure.com/)i przejdź do *aplikacji przejściowej (mywebapp\<unique_number\>/staging).* Aplikację sieci Web można szybko zlokalizować, wprowadzając *przemieszczania* w polu wyszukiwania portalu:

    ![przemieszczania terminu wyszukiwania aplikacji sieci Web](media/cicd/portal-search-box.png)

1. Kliknij pozycję **Centrum wdrażania**. Pojawi się nowy panel. Kliknij **przycisk Rozłącz,** aby usunąć lokalną konfigurację kontroli źródła Git, która została dodana w poprzednim rozdziale. Potwierdź operację usuwania, klikając przycisk **Tak.**
1. Przejdź do *usługi mywebapp<unique_number>* App Service. Przypominamy, że pole wyszukiwania portalu może służyć do szybkiego zlokalizowania usługi app service.
1. Kliknij pozycję **Centrum wdrażania**. Pojawi się nowy panel. Kliknij **przycisk Rozłącz,** aby usunąć lokalną konfigurację kontroli źródła Git, która została dodana w poprzednim rozdziale. Potwierdź operację usuwania, klikając przycisk **Tak.**

## <a name="create-an-azure-devops-organization"></a>Tworzenie organizacji usługi Azure DevOps

1. Otwórz przeglądarkę i przejdź do [strony tworzenia organizacji Azure DevOps](https://go.microsoft.com/fwlink/?LinkId=307137).
1. Wpisz unikatową nazwę w pola **tekstowym Wybierz nazwę z zapamiętywaniem,** aby utworzyć adres URL dostępu do organizacji Azure DevOps.
1. Wybierz przycisk opcji **Git,** ponieważ kod jest hostowany w repozytorium GitHub.
1. Kliknij przycisk **Kontynuuj**. Po krótkim oczekiwaniu tworzone jest konto i projekt zespołowy o nazwie *MyFirstProject.*

    ![Strona tworzenia organizacji Azure DevOps](media/cicd/vsts-account-creation.png)

1. Otwórz wiadomość e-mail z potwierdzeniem wskazującą, że organizacja i projekt Azure DevOps są gotowe do użycia. Kliknij przycisk **Rozpocznij projekt:**

    ![Przycisk Rozpocznij projekt](media/cicd/vsts-start-project.png)

1. Przeglądarka otwiera się * \<account_name\>.visualstudio.com*. Kliknij łącze *MyFirstProject,* aby rozpocząć konfigurowanie potoku DevOps projektu.

## <a name="configure-the-azure-pipelines-pipeline"></a>Konfigurowanie potoku potoku azure

Istnieją trzy różne kroki do wykonania. Wykonanie kroków w kolejnych trzech sekcjach powoduje operacyjne potok DevOps.

### <a name="grant-azure-devops-access-to-the-github-repository"></a>Udzielanie dostępu do usługi Azure DevOps do repozytorium Usługi GitHub

1. Rozwiń **kod lub zbuduj z zewnętrznego akordeonu repozytorium.** Kliknij przycisk **Kompilacja instalacji:**

    ![Przycisk Kompilacja instalacji](media/cicd/vsts-setup-build.png)

1. Wybierz opcję **GitHub** z sekcji **Wybierz źródło:**

    ![Wybieranie źródła - GitHub](media/cicd/vsts-select-source.png)

1. Autoryzacja jest wymagana, zanim usługa Azure DevOps będzie mogła uzyskać dostęp do repozytorium Usługi GitHub. Wprowadź *<GitHub_username> połączeniu GitHub* w polach tekstowych Nazwa **połączenia.** Przykład:

    ![Nazwa połączenia github](media/cicd/vsts-repo-authz.png)

1. Jeśli uwierzytelnianie dwuskładnikowe jest włączone na koncie Usługi GitHub, wymagany jest token dostępu osobistego. W takim przypadku kliknij link **Autoryzuj za pomocą osobistego tokenu dostępu GitHub.** Aby uzyskać pomoc, zapoznaj się z [oficjalnymi instrukcjami tworzenia tokenów dostępu osobistego GitHub.](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) Potrzebne jest tylko zakres *uprawnień repozytorium.* W przeciwnym razie kliknij przycisk **Autoryzuj przy użyciu OAuth.**
1. Po wyświetleniu monitu zaloguj się na swoje konto Usługi GitHub. Następnie wybierz pozycję Autoryzuj, aby udzielić dostępu do organizacji Azure DevOps. W przypadku powodzenia tworzony jest nowy punkt końcowy usługi.
1. Kliknij przycisk wielokropka obok przycisku **Repozytorium.** Wybierz z listy repozytorium *<GitHub_username>/simple-feed-reader.* Kliknij przycisk **Wybierz.**
1. Wybierz gałąź *główną* z **gałęzi Domyślna dla ręcznego i zaplanowanego tworzenia listy** rozwijanej. Kliknij przycisk **Kontynuuj**. Zostanie wyświetlona strona wyboru szablonu.

### <a name="create-the-build-definition"></a>Tworzenie definicji kompilacji

1. Na stronie wyboru szablonu wprowadź *ASP.NET Core* w polu wyszukiwania:

    ![wyszukiwanie ASP.NET Core na stronie szablonu](media/cicd/vsts-template-selection.png)

1. Zostaną wyświetlone wyniki wyszukiwania szablonu. Umieść wskaźnik myszy na szablonie **ASP.NET Core** i kliknij przycisk **Zastosuj.**
1. Pojawi się karta **Zadania** definicji kompilacji. Kliknij kartę **Wyzwalacze.**
1. Zaznacz pole **Włącz ciągłą integrację.** W sekcji **Filtry gałęzi** upewnij się, że z listy rozwijanej **Typ** jest ustawiona na *Uwzględnij*. Ustaw z listy rozwijanej **Specyfikacja gałęzi** do *wzorca*.

    ![Włączanie ustawień ciągłej integracji](media/cicd/vsts-enable-ci.png)

    Te ustawienia powodują, że kompilacja wyzwalać, gdy wszelkie zmiany są wypychane do gałęzi *głównej* repozytorium GitHub. Ciągła integracja jest testowana w sekcji [Zatwierdzanie zmian w usłudze GitHub i automatycznie wdrażana w](#commit-changes-to-github-and-automatically-deploy-to-azure) usłudze Azure.

1. Kliknij przycisk **Zapisz & kolejki,** a następnie wybierz opcję **Zapisz:**

    ![Przycisk Save (Zapisz)](media/cicd/vsts-save-build.png)

1. Zostanie wyświetlone następujące modalne okno dialogowe:

    ![Zapisz definicję kompilacji — modalne okno dialogowe](media/cicd/vsts-save-modal.png)

    Użyj folderu *\\*domyślnego programu i kliknij przycisk **Zapisz.**

### <a name="create-the-release-pipeline"></a>Tworzenie potoku wydania

1. Kliknij kartę **Wersje** projektu zespołowego. Kliknij przycisk **Nowy potok.**

    ![Karta Zwalnia — przycisk Nowa definicja](media/cicd/vsts-new-release-definition.png)

    Zostanie wyświetlene okienko wyboru szablonu.

1. Na stronie wyboru szablonu wprowadź usługę *App Service* w polu wyszukiwania:

    ![Zwolnij pole wyszukiwania szablonu potoku](media/cicd/vsts-release-template-search.png)

1. Zostaną wyświetlone wyniki wyszukiwania szablonu. Umieść wskaźnik myszy na **wdrożeniu usługi Azure App Service za pomocą szablonu Slot** i kliknij przycisk **Zastosuj.** Pojawi się karta **Potok** potoku wydania.

    ![Karta Potok potoku wydania](media/cicd/vsts-release-definition-pipeline.png)

1. Kliknij przycisk **Dodaj** w polu **Artefakty.** Pojawi się panel **Dodaj artefakt:**

    ![Potok wydania — dodawanie panelu artefaktów](media/cicd/vsts-release-add-artifact.png)

1. Wybierz kafelek **Kompilacja** z sekcji **Typ źródła.** Ten typ umożliwia łączenie potoku wydania z definicją kompilacji.
1. Wybierz *pozycję MyFirstProject* z listy rozwijanej **Projekt.**
1. Wybierz nazwę definicji kompilacji, *MyFirstProject-ASP.NET Core-CI*, z listy rozwijanej **Źródło (definicja kompilacji).**
1. Z listy rozwijanej **Wersja domyślna** wybierz pozycję *Najnowsze.* Ta opcja tworzy artefakty produkowane przez najnowsze uruchomienie definicji kompilacji.
1. Zastąp tekst w polach **tekstowych Alias źródłowy** *przycisku Upuść*.
1. Kliknij przycisk **Dodaj**. Sekcja **Artefakty** zostanie zaktualizowana w celu wyświetlenia zmian.
1. Kliknij ikonę błyskawicy, aby włączyć ciągłe wdrożenia:

    ![Zwolnij potok Artefakty - ikona błyskawicy](media/cicd/vsts-artifacts-lightning-bolt.png)

    Po włączeniu tej opcji wdrożenie odbywa się za każdym razem, gdy dostępna jest nowa kompilacja.
1. Po prawej stronie pojawi się panel **wyzwalacza ciągłego wdrażania.** Kliknij przycisk przełączania, aby włączyć operację. Nie jest konieczne, aby włączyć **wyzwalacz żądania ściągania**.
1. Kliknij pozycję listy rozwijanej **Dodaj** w sekcji **Filtry gałęzi kompilacji.** Wybierz **domyślną opcję gałęzi definicji kompilacji.** Ten filtr powoduje, że wyzwalanie wersji tylko dla kompilacji z gałęzi *głównej* repozytorium GitHub.
1. Kliknij przycisk **Zapisz**. Kliknij przycisk **OK** w wynikowym oknie dialogowym **Zapisz** modalne.
1. Kliknij pole **Środowisko 1.** Po prawej stronie pojawi się panel **Środowisko.** Zmień tekst *Środowiska 1* w pola tekstowym **Nazwa środowiska** na *Produkcja*.

   ![Potok wydania — pole tekstowe nazwa środowiska](media/cicd/vsts-environment-name-textbox.png)

1. Kliknij **łącze 1 faza, 2 zadania** w polu **Produkcja:**

    ![Potok wydania — środowisko produkcji link.png](media/cicd/vsts-production-link.png)

    Pojawi się karta **Zadania** w środowisku.
1. Kliknij zadanie **Wdrażanie usługi Azure App Service w miejscu.** Jego ustawienia są wyświetlane w panelu po prawej stronie.
1. Wybierz subskrypcję platformy Azure skojarzoną z usługą app service z listy rozwijanej **subskrypcji platformy Azure.** Po wybraniu tej opcji kliknij przycisk **Autoryzuj.**
1. Wybierz *aplikację sieci Web* z listy rozwijanej Typ **aplikacji.**
1. Z listy rozwijanej Nazwa **usługi aplikacji** *wybierz mywebapp/<unique_number/>.*
1. Wybierz *AzureTutorial* z listy rozwijanej **Grupa zasobów.**
1. Wybierz *tymczasową* z listy rozwijanej **Slot.**
1. Kliknij przycisk **Zapisz**.
1. Umieść wskaźnik myszy na domyślnej nazwie potoku wydania. Kliknij ikonę ołówka, aby ją edytować. Użyj *MyFirstProject-ASP.NET Core-CD* jako nazwy.

    ![Nazwa potoku wydania](media/cicd/vsts-release-definition-name.png)

1. Kliknij przycisk **Zapisz**.

## <a name="commit-changes-to-github-and-automatically-deploy-to-azure"></a>Zatwierdzanie zmian w usłudze GitHub i automatyczne wdrażanie na platformie Azure

1. Otwórz *simplefeedreader.sln* w programie Visual Studio.
1. W Eksploratorze rozwiązań otwórz *stronę Pages\Index.cshtml*. Zmień `<h2>Simple Feed Reader - V3</h2>` `<h2>Simple Feed Reader - V4</h2>`na .
1. Naciśnij **klawisze Ctrl**+**Shift**+**B,** aby utworzyć aplikację.
1. Zaagnaj plik do repozytorium GitHub. Użyj strony **Zmiany** na karcie *Eksplorator zespołu* programu Visual Studio lub wykonaj następujące czynności przy użyciu powłoki poleceń komputera lokalnego:

    ```console
    git commit -a -m "upgraded to V4"
    ```

1. Wypchnij zmianę w gałęzi *głównej* do *źródła* zdalnego repozytorium GitHub:

    ```console
    git push origin master
    ```

    Zatwierdzenie pojawia się w gałęzi *głównej* repozytorium GitHub:

    ![Zatwierdzanie gitHub w gałęzi głównej](media/cicd/github-commit.png)

    Kompilacja jest wyzwalana, ponieważ ciągła integracja jest włączona na karcie Wyzwalacze definicji **kompilacji:**

    ![umożliwić ciągłą integrację](media/cicd/enable-ci.png)

1. Przejdź do karty **w kolejce** na stronie**Kompilacje** **potoków** > platformy Azure w usługach Azure DevOps. Kompilacja w kolejce pokazuje gałąź i zatwierdzenie, które wyzwoliło kompilację:

    ![kompilacja w kolejce](media/cicd/build-queued.png)

1. Po pomyślnym uruchomieniu kompilacji na platformie Azure występuje wdrożenie. Przejdź do aplikacji w przeglądarce. Zwróć uwagę, że tekst "V4" pojawia się w nagłówku:

    ![zaktualizowana aplikacja](media/cicd/updated-app-v4.png)

## <a name="examine-the-azure-pipelines-pipeline"></a>Sprawdzanie potoku potoku azure

### <a name="build-definition"></a>Definicja kompilacji

Definicja kompilacji została utworzona pod nazwą *MyFirstProject-ASP.NET Core-CI*. Po zakończeniu kompilacji tworzy plik *.zip,* w tym zasoby, które mają zostać opublikowane. Potok wydania wdraża te zasoby na platformie Azure.

Na karcie Zadania definicji **kompilacji** wymieniono poszczególne kroki używane. Istnieje pięć zadań kompilacji.

![tworzenie zadań definicji](media/cicd/build-definition-tasks.png)

1. **Przywróć** &mdash; `dotnet restore` wykonuje polecenie, aby przywrócić pakiety NuGet aplikacji. Domyślny kanał pakietu używany jest nuget.org.
1. **Kompilacja** &mdash; `dotnet build --configuration release` wykonuje polecenie do skompilowania kodu aplikacji. Ta `--configuration` opcja jest używana do tworzenia zoptymalizowanej wersji kodu, który jest odpowiedni do wdrożenia w środowisku produkcyjnym. Zmodyfikuj zmienną *BuildConfiguration* na karcie Zmienne definicji **kompilacji,** jeśli na przykład potrzebna jest konfiguracja debugowania.
1. **Test** &mdash; wykonuje `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` polecenie uruchamiania testów jednostkowych aplikacji. Testy jednostkowe są wykonywane w `**/*Tests/*.csproj` ramach dowolnego projektu Języka C# pasującego do wzorca glob. Wyniki testów są zapisywane w pliku *.trx* w lokalizacji określonej `--results-directory` przez tę opcję. Jeśli wszystkie testy nie powiodą się, kompilacja zakończy się niepowodzeniem i nie jest wdrażana.

    > [!NOTE]
    > Aby sprawdzić, czy testy jednostkowe działają, zmodyfikuj *SimpleFeedReader.Tests\Services\NewsServiceTests.cs,* aby celowo przerwać jeden z testów. Na przykład `Assert.True(result.Count > 0);` zmień `Assert.False(result.Count > 0);` na `Returns_News_Stories_Given_Valid_Uri` metodę. Zaagnaj i wypychaj zmianę do GitHub. Kompilacja jest wyzwalana i kończy się niepowodzeniem. Stan potoku kompilacji zmienia się na **nie powiódł**się. Przywróć zmianę, zaobróć i wypchnij ponownie. Kompilacja powiedzie się.

1. **Publish** &mdash; Wykonuje `dotnet publish --configuration release --output <local_path_on_build_agent>` polecenie do produkcji pliku *zip* z artefaktami do wdrożenia. Opcja `--output` określa lokalizację publikowania pliku *zip.* Ta lokalizacja jest określona przez przekazanie `$(build.artifactstagingdirectory)` [wstępnie zdefiniowanej zmiennej](/azure/devops/pipelines/build/variables) o nazwie . Ta zmienna rozwija się do ścieżki lokalnej, takiej jak *c:\agent\_work\1\a*, w agencie kompilacji.
1. **Publikuj artefakt** &mdash; Publikuje plik *zip* wyprodukowany przez zadanie **Publikowania.** Zadanie akceptuje lokalizację pliku *zip* jako parametr, który jest wstępnie `$(build.artifactstagingdirectory)`zdefiniowaną zmienną . Plik *zip* jest publikowany jako folder o nazwie *drop*.

Kliknij łącze Podsumowanie definicji **kompilacji,** aby wyświetlić historię kompilacji z definicją:

![Zrzut ekranu przedstawiający historię definicji kompilacji](media/cicd/build-definition-summary.png)

Na wynikowej stronie kliknij łącze odpowiadające unikatowemu numerowi kompilacji:

![Zrzut ekranu przedstawiający stronę podsumowania definicji kompilacji](media/cicd/build-definition-completed.png)

Zostanie wyświetlone podsumowanie tej konkretnej kompilacji. Kliknij kartę **Artefakty** i zwróć uwagę, że folder *upuszczania* wyprodukowany przez kompilację jest wymieniony:

![Zrzut ekranu przedstawiający artefakty definicji kompilacji — folder upuszczania](media/cicd/build-definition-artifacts.png)

Użyj **łącza Pobierz** i **Eksploruj,** aby sprawdzić opublikowane artefakty.

### <a name="release-pipeline"></a>Potok wydania

Potok wydania został utworzony pod nazwą *MyFirstProject-ASP.NET Core-CD:*

![Zrzut ekranu przedstawiający omówienie potoku wydania](media/cicd/release-definition-overview.png)

Dwa główne składniki potoku wydania są **artefakty** i **środowiska**. Kliknięcie pola w sekcji **Artefakty** jest następujące:

![Zrzut ekranu przedstawiający artefakty potoku wydania](media/cicd/release-definition-artifacts.png)

**Wartość Source (Build definition)** reprezentuje definicję kompilacji, z którą połączony jest ten potok wydania. Plik *zip* wyprodukowany przez pomyślne uruchomienie definicji kompilacji jest dostarczany do środowiska *produkcyjnego* do wdrożenia na platformie Azure. Kliknij *łącze 1 faza, 2 zadania* w polu Środowisko *produkcyjne,* aby wyświetlić zadania potoku wydania:

![Zrzut ekranu przedstawiający zadania potoku wydania](media/cicd/release-definition-tasks.png)

Potok wydania składa się z dwóch zadań: *Wdrażanie usługi Azure App Service na gnieździe* i *zarządzanie usługą Azure App Service — zamiana gniazd*. Kliknięcie pierwszego zadania jest następujące:

![Zrzut ekranu przedstawiający zadanie wdrażania potoku wydania](media/cicd/release-definition-task1.png)

Subskrypcja platformy Azure, typ usługi, nazwa aplikacji sieci web, grupa zasobów i gniazdo wdrażania są zdefiniowane w zadaniu wdrażania. Pole tekstowe **Pakiet lub folder** zawiera ścieżkę pliku *zip,* która ma zostać wyodrębniona i wdrożona w miejscu *przejściowym* aplikacji sieci web *\<mywebapp unique_number.\> *

Kliknięcie zadania zamiany gniazda ujawnia następującą konfigurację zadania:

![Zrzut ekranu przedstawiający zadanie wymiany gniazda potoku wydania](media/cicd/release-definition-task2.png)

Podano subskrypcję, grupę zasobów, typ usługi, nazwę aplikacji sieci web i szczegóły gniazda wdrożenia. Pole wyboru **Zamiana z produkcją** jest zaznaczone. W związku z tym bity wdrożone w miejscu *przejściowym* są zamieniane w środowisku produkcyjnym.

## <a name="additional-reading"></a>Dodatkowa lektura

* [Tworzenie pierwszego potoku za pomocą usługi Azure Pipelines](/azure/devops/pipelines/get-started-yaml)
* [Tworzenie i projekt .NET Core](/azure/devops/pipelines/languages/dotnet-core)
* [Wdrażanie aplikacji sieci Web za pomocą usługi Azure Pipelines](/azure/devops/pipelines/targets/webapp)
