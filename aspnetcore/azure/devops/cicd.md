---
title: Ciągła integracja i wdrażanie — DevOps z użyciem ASP.NET Core i platformy Azure
author: CamSoper
description: Ciągła integracja i wdrażanie w programie DevOps z usługami ASP.NET Core i Azure
ms.author: scaddie
ms.date: 10/24/2018
ms.custom: devx-track-csharp, mvc, seodec18
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
uid: azure/devops/cicd
ms.openlocfilehash: 3632f1c4bd419aae08105005de3d81fc2cb9e410
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625885"
---
# <a name="continuous-integration-and-deployment"></a>Ciągła integracja i ciągłe wdrażanie

W poprzednim rozdziale utworzono lokalne repozytorium git dla aplikacji czytnika prostego źródła danych. W tym rozdziale opublikujesz ten kod w repozytorium GitHub i utworzysz potok Azure DevOps Services przy użyciu Azure Pipelines. Potok umożliwia ciągłe kompilacje i wdrożenia aplikacji. Każde zatwierdzenie repozytorium GitHub wyzwala kompilację i wdrożenie w miejscu przejściowym aplikacji sieci Web platformy Azure.

W tej sekcji wykonasz następujące zadania:

* Opublikuj kod aplikacji w usłudze GitHub
* Rozłącz lokalne wdrożenie narzędzia Git
* Tworzenie organizacji usługi Azure DevOps
* Utwórz projekt zespołowy w Azure DevOps Services
* Tworzenie definicji kompilacji
* Tworzenie potoku wydania
* Zatwierdzanie zmian w usłudze GitHub i automatyczne wdrażanie na platformie Azure
* Sprawdzanie potoku Azure Pipelines

## <a name="publish-the-apps-code-to-github"></a>Opublikuj kod aplikacji w usłudze GitHub

1. Otwórz okno przeglądarki i przejdź do `https://github.com` .
1. Kliknij **+** listę rozwijaną w nagłówku i wybierz pozycję **nowe repozytorium**:

    ![Opcja nowego repozytorium usługi GitHub](media/cicd/github-new-repo.png)

1. Wybierz swoje konto z listy rozwijanej **właściciel** , a *następnie wprowadź tekst* w polu tekstowym **Nazwa repozytorium** .
1. Kliknij przycisk **Utwórz repozytorium** .
1. Otwórz powłokę poleceń komputera lokalnego. Przejdź do katalogu, w którym jest przechowywane repozytorium git programu *Simple-Reader* .
1. Zmień nazwę istniejącego *pochodzenia* zdalnego na *nadrzędny*. Wykonaj następujące polecenie:

    ```console
    git remote rename origin upstream
    ```

1. Dodaj nowe *Źródło* zdalne wskazujące swoją kopię repozytorium w serwisie GitHub. Wykonaj następujące polecenie:

    ```console
    git remote add origin https://github.com/<GitHub_username>/simple-feed-reader/
    ```

1. Opublikuj lokalne repozytorium Git w nowo utworzonym repozytorium GitHub. Wykonaj następujące polecenie:

    ```console
    git push -u origin master
    ```

1. Otwórz okno przeglądarki i przejdź do `https://github.com/<GitHub_username>/simple-feed-reader/` . Sprawdź, czy kod jest wyświetlany w repozytorium GitHub.

## <a name="disconnect-local-git-deployment"></a>Rozłącz lokalne wdrożenie narzędzia Git

Usuń lokalne wdrożenie narzędzia Git, wykonując poniższe kroki. Azure Pipelines (usługa Azure DevOps) zamienia i rozszerza te funkcje.

1. Otwórz [Azure Portal](https://portal.azure.com/)i przejdź do aplikacji sieci Web *przemieszczania (mywebapp \<unique_number\> /Staging)* . Aplikację sieci Web można szybko zlokalizować *, wprowadzając w* polu wyszukiwania portalu:

    ![termin wyszukiwania aplikacji sieci Web](media/cicd/portal-search-box.png)

1. Kliknij pozycję **centrum wdrażania**. Zostanie wyświetlony nowy panel. Kliknij przycisk **Rozłącz** , aby usunąć konfigurację lokalnej kontroli źródła git, która została dodana w poprzednim rozdziale. Potwierdź operację usuwania, klikając przycisk **tak** .
1. Przejdź do *mywebapp<unique_number>* App Service. Za pomocą pola wyszukiwania portalu można szybko zlokalizować App Service.
1. Kliknij pozycję **centrum wdrażania**. Zostanie wyświetlony nowy panel. Kliknij przycisk **Rozłącz** , aby usunąć konfigurację lokalnej kontroli źródła git, która została dodana w poprzednim rozdziale. Potwierdź operację usuwania, klikając przycisk **tak** .

## <a name="create-an-azure-devops-organization"></a>Tworzenie organizacji usługi Azure DevOps

1. Otwórz przeglądarkę i przejdź do [strony tworzenie organizacji usługi Azure DevOps](https://go.microsoft.com/fwlink/?LinkId=307137).
1. Wpisz unikatową nazwę w polu tekstowym **Wybierz zapamiętaną nazwę** , aby utworzyć adres URL do uzyskiwania dostępu do organizacji usługi Azure DevOps.
1. Wybierz przycisk radiowy **git** , ponieważ kod jest hostowany w repozytorium GitHub.
1. Kliknij przycisk **Kontynuuj**. Po krótkim czasie oczekiwania tworzone jest konto i projekt zespołowy o nazwie *MyFirstProject*.

    ![Strona tworzenia organizacji usługi Azure DevOps](media/cicd/vsts-account-creation.png)

1. Otwórz wiadomość e-mail z potwierdzeniem wskazującą, że organizacja i projekt usługi Azure DevOps są gotowe do użycia. Kliknij przycisk **Rozpocznij projekt** :

    ![Przycisk Rozpocznij projekt](media/cicd/vsts-start-project.png)

1. Zostanie otwarta przeglądarka * \<account_name\> . VisualStudio.com*. Kliknij link *MyFirstProject* , aby rozpocząć konfigurowanie potoku DevOps projektu.

## <a name="configure-the-azure-pipelines-pipeline"></a>Konfigurowanie potoku Azure Pipelines

Istnieją trzy różne czynności do wykonania. Wykonanie kroków opisanych w poniższych trzech sekcjach powoduje wystąpienie działającego potoku DevOps.

### <a name="grant-azure-devops-access-to-the-github-repository"></a>Przyznaj usłudze Azure DevOps dostęp do repozytorium GitHub

1. Rozwiń **lub Skompiluj kod z repozytorium zewnętrznego** . Kliknij przycisk **kompilacja instalacji** :

    ![Przycisk Kompilacja instalacji](media/cicd/vsts-setup-build.png)

1. Wybierz opcję **GitHub** z sekcji **Wybierz źródło** :

    ![Wybierz źródło — GitHub](media/cicd/vsts-select-source.png)

1. Autoryzacja jest wymagana, aby usługa Azure DevOps mogła uzyskać dostęp do Twojego repozytorium GitHub. Wprowadź *<GitHub_username> połączenia GitHub* w polu tekstowym **Nazwa połączenia** . Na przykład:

    ![Nazwa połączenia GitHub](media/cicd/vsts-repo-authz.png)

1. Jeśli na koncie usługi GitHub jest włączone uwierzytelnianie dwuskładnikowe, wymagany jest osobisty token dostępu. W takim przypadku kliknij link **Autoryzuj przy użyciu osobistego tokenu dostępu usługi GitHub** . Zobacz [oficjalne instrukcje tworzenia tokenu dostępu osobistego usługi GitHub](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) , aby uzyskać pomoc. Wymagany jest tylko zakres uprawnień do *repozytorium* . W przeciwnym razie kliknij przycisk **Autoryzuj przy użyciu protokołu OAuth** .
1. Po wyświetleniu monitu zaloguj się do konta usługi GitHub. Następnie wybierz pozycję Autoryzuj, aby udzielić dostępu do Twojej organizacji usługi Azure DevOps. Jeśli to się powiedzie, zostanie utworzony nowy punkt końcowy usługi.
1. Kliknij przycisk wielokropka obok przycisku **repozytorium** . Wybierz z listy *<GitHub_username> repozytorium/Simple-Feed-Reader* . Kliknij przycisk **Wybierz** .
1. Wybierz gałąź *główną* z **gałęzi domyślnej dla listy rozwijanej ręczne i zaplanowane kompilacje** . Kliknij przycisk **Kontynuuj**. Zostanie wyświetlona strona Wybieranie szablonu.

### <a name="create-the-build-definition"></a>Tworzenie definicji kompilacji

1. Na stronie Wybór szablonu wprowadź *ASP.NET Core* w polu wyszukiwania:

    ![ASP.NET Core wyszukiwanie na stronie szablonu](media/cicd/vsts-template-selection.png)

1. Zostaną wyświetlone wyniki wyszukiwania szablonu. Umieść kursor nad szablonem **ASP.NET Core** i kliknij przycisk **Zastosuj** .
1. Zostanie wyświetlona karta **zadania** w definicji kompilacji. Kliknij kartę **wyzwalacze** .
1. Zaznacz pole wyboru **Włącz ciągłą integrację** . W sekcji **filtry gałęzi** upewnij się, że na liście rozwijanej **typ** jest ustawiona wartość *include*. Ustaw listę rozwijaną **Specyfikacja gałęzi** z *główną*.

    ![Włącz ustawienia ciągłej integracji](media/cicd/vsts-enable-ci.png)

    Te ustawienia powodują, że kompilacja jest wyzwalana, gdy jakakolwiek zmiana jest wypychana do *głównej* gałęzi repozytorium GitHub. Ciągła integracja jest testowana w usłudze [GitHub i automatycznie wdrażana](#commit-changes-to-github-and-automatically-deploy-to-azure) w usłudze Azure.

1. Kliknij przycisk **zapisz & kolejkę** i wybierz opcję **Zapisz** :

    ![Przycisk Zapisz](media/cicd/vsts-save-build.png)

1. Wyświetlane jest następujące modalne okno dialogowe:

    ![Zapisz definicję kompilacji — modalne okno dialogowe](media/cicd/vsts-save-modal.png)

    Użyj folderu domyślnego *\\* , a następnie kliknij przycisk **Zapisz** .

### <a name="create-the-release-pipeline"></a>Tworzenie potoku wydania

1. Kliknij kartę **wydania** w projekcie zespołowym. Kliknij przycisk **nowe potoku** .

    ![Karta wydania — przycisk Nowa definicja](media/cicd/vsts-new-release-definition.png)

    Zostanie wyświetlone okienko wybór szablonu.

1. Na stronie Wybór szablonu wprowadź *App Service* w polu wyszukiwania:

    ![Pole wyszukiwania szablonu potoku wydania](media/cicd/vsts-release-template-search.png)

1. Zostaną wyświetlone wyniki wyszukiwania szablonu. Umieść kursor nad **wdrożeniem Azure App Service z** szablonem miejsca, a następnie kliknij przycisk **Zastosuj** . Zostanie wyświetlona karta **potok** w potoku wydania.

    ![Karta potoku wydania potoku](media/cicd/vsts-release-definition-pipeline.png)

1. Kliknij przycisk **Dodaj** w polu **artefakty** . Zostanie wyświetlony panel **Dodaj artefakt** :

    ![Potok wydania — Dodawanie panelu artefaktów](media/cicd/vsts-release-add-artifact.png)

1. Wybierz kafelek **kompilacja** z sekcji **Typ źródła** . Ten typ umożliwia łączenie potoku wydania z definicją kompilacji.
1. Wybierz pozycję *MyFirstProject* z listy rozwijanej **projekt** .
1. Wybierz nazwę definicji kompilacji, *MyFirstProject-ASP.NET Core-Ci*, z listy rozwijanej **Źródło (definicja kompilacji)** .
1. Wybierz pozycję *Najnowsza* z listy rozwijanej **wersja domyślna** . Ta opcja kompiluje artefakty wytwarzane przez ostatnie uruchomienie definicji kompilacji.
1. Zastąp tekst w polu tekstowym **aliasu źródła** obiektem *Drop*.
1. Kliknij przycisk **Dodaj**. Sekcja **artefakty** jest aktualizowana w celu wyświetlenia zmian.
1. Kliknij ikonę błyskawicy, aby włączyć ciągłe wdrożenia:

    ![Artefakty potoku wydania — ikona błyskawicy](media/cicd/vsts-artifacts-lightning-bolt.png)

    Po włączeniu tej opcji wdrożenie odbywa się za każdym razem, gdy dostępna jest nowa kompilacja.
1. Po prawej stronie zostanie wyświetlony panel **wyzwalacz ciągłego wdrażania** . Kliknij przycisk Przełącz, aby włączyć tę funkcję. Nie jest konieczne włączenie **wyzwalacza żądania ściągnięcia**.
1. Kliknij przycisk **Dodaj** listę rozwijaną w sekcji **filtry gałęzi kompilacji** . Wybierz opcję **domyślne rozgałęzienie definicji kompilacji** . Ten filtr powoduje, że wersja jest wyzwalana tylko dla kompilacji z *głównej* gałęzi repozytorium GitHub.
1. Kliknij przycisk **Zapisz**. Kliknij przycisk **OK** w oknie dialogowym **zapisuje** modalne okno dialogowe.
1. Kliknij pole **środowisko 1** . Panel **środowiska** pojawia się po prawej stronie. Zmień tekst *środowiska 1* w polu tekstowym **Nazwa środowiska** na środowisko *produkcyjne*.

   ![Potok wydania — pole tekstowe nazwy środowiska](media/cicd/vsts-environment-name-textbox.png)

1. Kliknij link **1 fazy, 2 zadania** w polu **produkcja** :

    ![Potok wydania — środowisko produkcyjne link.png](media/cicd/vsts-production-link.png)

    Zostanie wyświetlona karta **zadania** środowiska.
1. Kliknij zadanie **wdróż Azure App Service w miejscu** . Ustawienia są wyświetlane w panelu po prawej stronie.
1. Wybierz subskrypcję platformy Azure skojarzoną z App Serviceą z listy rozwijanej **subskrypcja platformy Azure** . Po wybraniu kliknij przycisk **Autoryzuj** .
1. Wybierz pozycję *aplikacja sieci Web* z listy rozwijanej **Typ aplikacji** .
1. Wybierz pozycję *mywebapp/<unique_number/>* z listy rozwijanej **nazwa usługi App Service** .
1. Wybierz pozycję *AzureTutorial* z listy rozwijanej **Grupa zasobów** .
1. Wybierz pozycję *przemieszczanie* z listy rozwijanej **miejsce** .
1. Kliknij przycisk **Zapisz**.
1. Umieść kursor nad domyślną nazwą potoku wydania. Kliknij ikonę ołówka, aby ją edytować. Użyj *MyFirstProject-ASP.NET Core-CD* jako nazwy.

    ![Nazwa potoku wydania](media/cicd/vsts-release-definition-name.png)

1. Kliknij przycisk **Zapisz**.

## <a name="commit-changes-to-github-and-automatically-deploy-to-azure"></a>Zatwierdzanie zmian w usłudze GitHub i automatyczne wdrażanie na platformie Azure

1. Otwórz *SimpleFeedReader. sln* w programie Visual Studio.
1. W Eksplorator rozwiązań Otwórz *Pages\Index.cshtml*. Zmień `<h2>Simple Feed Reader - V3</h2>` na `<h2>Simple Feed Reader - V4</h2>` .
1. Naciśnij **klawisze CTRL** + **SHIFT** + **B** , aby skompilować aplikację.
1. Zatwierdź plik do repozytorium GitHub. Użyj strony **zmiany** w karcie *Team Explorer* programu Visual Studio lub wykonaj następujące czynności przy użyciu powłoki poleceń komputera lokalnego:

    ```console
    git commit -a -m "upgraded to V4"
    ```

1. Wypchnij zmiany w gałęzi *głównej* do lokalizacji zdalnej *źródła* repozytorium GitHub:

    ```console
    git push origin master
    ```

    Zatwierdzenie pojawi się w gałęzi *głównej* repozytorium GitHub:

    ![Zatwierdzenie GitHub w gałęzi głównej](media/cicd/github-commit.png)

    Kompilacja jest wyzwalana, ponieważ na karcie **wyzwalacze** definicji kompilacji jest włączona Integracja ciągła:

    ![Włącz integrację ciągłą](media/cicd/enable-ci.png)

1. Przejdź do karty z **kolejką** na **Azure Pipelines**  >  stronie**kompilacje** Azure Pipelines w Azure DevOps Services. Kompilacja w kolejce pokazuje gałąź i zatwierdzenie, które wyzwoliły kompilację:

    ![Kompilacja umieszczona w kolejce](media/cicd/build-queued.png)

1. Gdy kompilacja zakończy się powodzeniem, wystąpi wdrożenie na platformie Azure. Przejdź do aplikacji w przeglądarce. Zwróć uwagę, że tekst "v4" pojawia się w nagłówku:

    ![zaktualizowana aplikacja](media/cicd/updated-app-v4.png)

## <a name="examine-the-azure-pipelines-pipeline"></a>Sprawdzanie potoku Azure Pipelines

### <a name="build-definition"></a>Definicja kompilacji

Definicja kompilacji została utworzona przy użyciu nazwy *MyFirstProject-ASP.NET Core-Ci*. Po zakończeniu kompilacja tworzy plik *zip* , w tym zasoby do opublikowania. Potok wydania wdraża te zasoby na platformie Azure.

Na karcie **zadania** definicji kompilacji są wyświetlane poszczególne etapy, które są używane. Istnieje pięć zadań kompilacji.

![zadania definicji kompilacji](media/cicd/build-definition-tasks.png)

1. **Przywróć** &mdash; Wykonuje `dotnet restore` polecenie, aby przywrócić pakiety NuGet aplikacji. Domyślnym źródłem pakietów jest nuget.org.
1. **Kompilacja** &mdash; Wykonuje `dotnet build --configuration release` polecenie, aby skompilować kod aplikacji. Ta `--configuration` opcja służy do tworzenia zoptymalizowanej wersji kodu, która jest odpowiednia do wdrożenia w środowisku produkcyjnym. Zmodyfikuj zmienną *BuildConfiguration* na karcie **zmienne** definicji kompilacji, jeśli na przykład wymagana jest Konfiguracja debugowania.
1. **Test** &mdash; Wykonuje `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` polecenie, aby uruchomić testy jednostkowe aplikacji. Testy jednostkowe są wykonywane w dowolnym projekcie języka C# pasującym do `**/*Tests/*.csproj` wzorca globalizowania. Wyniki testu są zapisywane w pliku *. TRX* w lokalizacji określonej przez `--results-directory` opcję. Jeśli jakiekolwiek testy zakończą się niepowodzeniem, kompilacja nie powiedzie się i nie zostanie wdrożona.

    > [!NOTE]
    > Aby sprawdzić, czy testy jednostkowe działają, zmodyfikuj *SimpleFeedReader. Tests\Services\NewsServiceTests.cs* na celowo całkowicie Przerwij jeden z testów. Na przykład zmień `Assert.True(result.Count > 0);` na `Assert.False(result.Count > 0);` w `Returns_News_Stories_Given_Valid_Uri` metodzie. Zatwierdź i wypchnij zmianę do usługi GitHub. Kompilacja jest wyzwalana i kończy się niepowodzeniem. Stan potoku kompilacji zmieni się na **Niepowodzenie**. Przywróć zmiany, Zatwierdź i wypchnij ponownie. Kompilacja powiodła się.

1. **Publikuj** &mdash; Wykonuje `dotnet publish --configuration release --output <local_path_on_build_agent>` polecenie, aby utworzyć plik *zip* z artefaktami, które mają zostać wdrożone. `--output`Opcja określa lokalizację publikacji pliku *zip* . Ta lokalizacja jest określona przez przekazanie [wstępnie zdefiniowanej zmiennej](/azure/devops/pipelines/build/variables) o nazwie `$(build.artifactstagingdirectory)` . Ta zmienna powiększa się do ścieżki lokalnej, takiej jak *c:\agent \_ work\1\a*, na agencie kompilacji.
1. **Publikuj artefakt** &mdash; Publikuje plik *zip* utworzony przez zadanie **publikowania** . Zadanie przyjmuje lokalizację pliku *. zip* jako parametr, który jest wstępnie zdefiniowaną zmienną `$(build.artifactstagingdirectory)` . Plik *zip* jest publikowany jako folder o nazwie *Drop*.

Kliknij link **podsumowania** definicji kompilacji, aby wyświetlić historię kompilacji z definicją:

![Zrzut ekranu przedstawiający historię definicji kompilacji](media/cicd/build-definition-summary.png)

Na stronie wyników kliknij link odpowiadający unikatowemu numerowi kompilacji:

![Zrzut ekranu przedstawiający stronę podsumowania definicji kompilacji](media/cicd/build-definition-completed.png)

Zostanie wyświetlona podsumowanie tej konkretnej kompilacji. Kliknij kartę **artefakty** i zwróć uwagę na to, że folder *upuszczania* utworzony przez kompilację zostanie wyświetlony na liście:

![Zrzut ekranu przedstawiający artefakty definicji kompilacji — folder docelowy](media/cicd/build-definition-artifacts.png)

Użyj linków **pobierania** i **eksplorowania** , aby sprawdzić opublikowane artefakty.

### <a name="release-pipeline"></a>Potok wydania

Potok wydania został utworzony przy użyciu nazwy *MyFirstProject-ASP.NET Core-CD*:

![Zrzut ekranu przedstawiający przegląd potoku wydania](media/cicd/release-definition-overview.png)

Dwa główne składniki potoku wydania to **artefakty** i **środowiska**. Kliknięcie pola w sekcji **artefakty** ujawnia następujący panel:

![Zrzut ekranu przedstawiający artefakty potoku wydania](media/cicd/release-definition-artifacts.png)

Wartość **źródłowa (definicji kompilacji)** reprezentuje definicję kompilacji, z którą połączony jest potok wersji. Plik *zip* utworzony przez pomyślne uruchomienie definicji kompilacji jest dostarczany do środowiska *produkcyjnego* w celu wdrożenia na platformie Azure. Kliknij link *1 fazy, 2 zadania* w polu środowisko *produkcyjne* , aby wyświetlić zadania potoku wydania:

![Zrzut ekranu przedstawiający zadania potoku wydania](media/cicd/release-definition-tasks.png)

Potok wersji składa się z dwóch zadań: *wdróż Azure App Service w gnieździe* i *Zarządzaj wymianą między gniazdami Azure App Service*. Kliknięcie pierwszego zadania wykazuje następującą konfigurację zadania:

![Zrzut ekranu przedstawiający zadanie wdrażania potoku wydania](media/cicd/release-definition-task1.png)

Subskrypcja platformy Azure, typ usługi, nazwa aplikacji sieci Web, Grupa zasobów i miejsce wdrożenia są zdefiniowane w zadaniu wdrażania. Pole tekstowe **pakiet lub folder** zawiera ścieżkę pliku *. zip* , która ma zostać wyodrębniona i wdrożona w miejscu *przejściowym* aplikacji sieci Web *mywebapp \<unique_number\> * .

Kliknięcie zadania wymiany gniazd powoduje wyświetlenie następującej konfiguracji zadania:

![Zrzut ekranu przedstawiający zadanie wymiany miejsca potoku wydania](media/cicd/release-definition-task2.png)

Podane są szczegóły subskrypcji, grupy zasobów, typu usługi, nazwy aplikacji sieci Web i miejsca wdrożenia. Pole wyboru **Zamień z produkcją** jest zaznaczone. W związku z tym bity wdrożone w miejscu *przejściowym* są wymieniane w środowisku produkcyjnym.

## <a name="additional-reading"></a>Materiały uzupełniające

* [Tworzenie pierwszego potoku za pomocą Azure Pipelines](/azure/devops/pipelines/get-started-yaml)
* [Kompilacja i projekt .NET Core](/azure/devops/pipelines/languages/dotnet-core)
* [Wdrażanie aplikacji sieci Web za pomocą Azure Pipelines](/azure/devops/pipelines/targets/webapp)
