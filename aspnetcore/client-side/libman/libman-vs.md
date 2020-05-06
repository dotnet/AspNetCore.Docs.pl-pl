---
title: Używanie LibMan z ASP.NET Core w programie Visual Studio
author: scottaddie
description: Dowiedz się, jak używać LibMan w projekcie ASP.NET Core z programem Visual Studio.
ms.author: scaddie
ms.custom: mvc
ms.date: 08/20/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: client-side/libman/libman-vs
ms.openlocfilehash: 45f81cbc713e7e7c1f335aef49360992d2297a81
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82770096"
---
# <a name="use-libman-with-aspnet-core-in-visual-studio"></a>Używanie LibMan z ASP.NET Core w programie Visual Studio

Przez [Scott Addie](https://twitter.com/Scott_Addie)

Program Visual Studio ma wbudowaną obsługę [LibMan](xref:client-side/libman/index) w projektach ASP.NET Core, w tym:

* Obsługa konfigurowania i uruchamiania operacji przywracania LibMan podczas kompilacji.
* Elementy menu służące do wyzwalania operacji przywracania i czyszczenia LibMan.
* Okno dialogowe wyszukiwania służące do znajdowania bibliotek i dodawania plików do projektu.
* Edytowanie obsługi pliku manifestu Libman *Libman. JSON*&mdash;.

[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) [(jak pobrać)](xref:index#how-to-download-a-sample)

## <a name="prerequisites"></a>Wymagania wstępne

* [Program Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) z **ASP.NET i programowaniem aplikacji sieci Web**

## <a name="add-library-files"></a>Dodaj pliki biblioteki

Pliki bibliotek można dodać do projektu ASP.NET Core na dwa różne sposoby:

1. [Korzystanie z okna dialogowego Dodawanie biblioteki po stronie klienta](#use-the-add-client-side-library-dialog)
1. [Ręczne konfigurowanie wpisów pliku manifestu LibMan](#manually-configure-libman-manifest-file-entries)

### <a name="use-the-add-client-side-library-dialog"></a>Korzystanie z okna dialogowego Dodawanie biblioteki po stronie klienta

Wykonaj następujące kroki, aby zainstalować bibliotekę po stronie klienta:

* W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy folder projektu, w którym należy dodać pliki. Wybierz pozycję **Dodaj** > **bibliotekę po stronie klienta**. Zostanie wyświetlone okno dialogowe **Dodawanie biblioteki po stronie klienta** :

  ![Okno dialogowe Dodawanie biblioteki po stronie klienta](_static/add-library-dialog.png)

* Wybierz dostawcę biblioteki z listy rozwijanej **dostawca** . CDNJS jest dostawcą domyślnym.
* Wpisz nazwę biblioteki do pobrania w polu tekstowym **Biblioteka** . Technologia IntelliSense udostępnia listę bibliotek zaczynających się od podanego tekstu.
* Wybierz bibliotekę z listy IntelliSense. Zwróć uwagę, że nazwa biblioteki jest poddana `@` sufiksu i najnowszej wersji stabilnej znanej dla wybranego dostawcy.
* Wybieranie plików do uwzględnienia:
  * Zaznacz przycisk radiowy **Dołącz wszystkie pliki bibliotek** , aby uwzględnić wszystkie pliki biblioteki.
  * Wybierz przycisk radiowy **Wybierz określone pliki** , aby dołączyć podzestaw plików biblioteki. Po wybraniu przycisku radiowego drzewo selektora plików jest włączone. Zaznacz pola po lewej stronie nazw plików do pobrania.
* Określ folder projektu do przechowywania plików w polu tekstowym **Lokalizacja docelowa** . Zgodnie z zaleceniami należy przechowywać każdą bibliotekę w osobnym folderze.

  Sugerowany folder **lokalizacji docelowej** jest oparty na lokalizacji, w której uruchomiono okno dialogowe:

  * Jeśli jest uruchamiany z poziomu głównego projektu:
    * plik *wwwroot/lib* jest używany, jeśli istnieje plik *wwwroot* .
    * *Biblioteka lib* jest używana, jeśli plik *wwwroot* nie istnieje.
  * W przypadku uruchomienia z folderu projektu zostanie użyta odpowiednia nazwa folderu.

  Sugestia folderu jest sufiksem z nazwą biblioteki. W poniższej tabeli przedstawiono sugestie dotyczące folderów podczas instalowania jQuery w projekcie Razor Pages.
  
  |Lokalizacja uruchamiania                           |Sugerowany folder      |
  |------------------------------------------|----------------------|
  |Katalog główny projektu (Jeśli folder *wwwroot* istnieje)        |*wwwroot/lib/jQuery/* |
  |Katalog główny projektu (Jeśli folder *wwwroot* nie istnieje) |*lib/jQuery/*         |
  |Folder *stron* w projekcie                 |*Strony/jQuery/*       |

* Kliknij przycisk **Instaluj** , aby pobrać pliki zgodnie z konfiguracją w pliku *Libman. JSON*.
* Zapoznaj się z informacjami dotyczącymi instalacji w oknie **danych wyjściowych** programu **Library Manager** . Przykład:

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

### <a name="manually-configure-libman-manifest-file-entries"></a>Ręczne konfigurowanie wpisów pliku manifestu LibMan

Wszystkie operacje LibMan w programie Visual Studio są oparte na zawartości manifestu LibMan elementu głównego projektu (*LibMan. JSON*). Można ręcznie edytować plik *Libman. JSON* w celu skonfigurowania plików biblioteki dla projektu. Program Visual Studio przywraca wszystkie pliki bibliotek po zapisaniu pliku *Libman. JSON* .

Aby otworzyć plik *Libman. JSON* do edycji, istnieją następujące opcje:

* Kliknij dwukrotnie plik *Libman. JSON* w **Eksplorator rozwiązań**.
* Kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań** i wybierz pozycję **Zarządzaj bibliotekami po stronie klienta**. **&#8224;**
* Wybierz pozycję **Zarządzaj bibliotekami po stronie klienta** z menu **projektu** programu Visual Studio. **&#8224;**

**&#8224;** Jeśli plik *Libman. JSON* nie istnieje już w katalogu głównym projektu, zostanie utworzony przy użyciu domyślnej zawartości szablonu elementu.

Program Visual Studio oferuje zaawansowane funkcje edycji JSON, takie jak kolorowanie, formatowanie, IntelliSense i walidacja schematu. Schemat JSON manifestu LibMan został znaleziony w lokalizacji [https://json.schemastore.org/libman](https://json.schemastore.org/libman).

Przy użyciu następującego pliku manifestu LibMan pobiera pliki zgodnie z konfiguracją zdefiniowaną we `libraries` właściwości. Wyjaśnienie literałów obiektów zdefiniowanych w `libraries` następujący sposób:

* Podzestaw [jQuery](https://jquery.com/) w wersji 3.3.1 jest pobierany z dostawcy CDNJS. Podzestaw jest `files` zdefiniowany w właściwości&mdash;*jQuery. min. js*, *jQuery. js*i *jQuery. min. map*. Pliki są umieszczane w folderze *wwwroot/lib/jQuery* projektu.
* W [całości wersja 4.1.3](https://getbootstrap.com/) jest pobierana i umieszczana w folderze *wwwroot/lib/Bootstrap* . `provider` Właściwość literału obiektu zastępuje wartość `defaultProvider` właściwości. LibMan pobiera pliki Bootstrap z dostawcy unpkg.
* Podzbiór [Lodash](https://lodash.com/) został zatwierdzony przez organ regulujący w organizacji. *Lodash. js* i *lodash. min. js* są pobierane z lokalnego systemu plików w lokalizacji *C:\\temp\\lodash\\*. Pliki są kopiowane do folderu *wwwroot/lib/lodash* projektu.

[!code-json[](samples/LibManSample/libman.json)]

> [!NOTE]
> LibMan obsługuje tylko jedną wersję każdej biblioteki z każdego dostawcy. Walidacja schematu pliku *Libman. JSON* kończy się niepowodzeniem, jeśli zawiera dwie biblioteki o tej samej nazwie biblioteki dla danego dostawcy.

## <a name="restore-library-files"></a>Przywróć pliki biblioteki

Aby przywrócić pliki bibliotek z programu Visual Studio, w katalogu głównym projektu musi istnieć prawidłowy plik *Libman. JSON* . Przywrócone pliki są umieszczane w projekcie w lokalizacji określonej dla każdej biblioteki.

Pliki bibliotek można przywrócić w projekcie ASP.NET Core na dwa sposoby:

1. [Przywróć pliki podczas kompilacji](#restore-files-during-build)
1. [Ręczne przywracanie plików](#restore-files-manually)

### <a name="restore-files-during-build"></a>Przywróć pliki podczas kompilacji

LibMan może przywrócić zdefiniowane pliki biblioteki w ramach procesu kompilacji. Domyślnie zachowanie funkcji *Przywróć przy kompilacji* jest wyłączone.

Aby włączyć i przetestować zachowanie funkcji przywracania po kompilacji:

* Kliknij prawym przyciskiem myszy plik *Libman. JSON* w **Eksplorator rozwiązań** i wybierz opcję **Włącz przywracanie bibliotek po stronie klienta w kompilacji** z menu kontekstowego.
* Po wyświetleniu monitu o zainstalowanie pakietu NuGet kliknij przycisk **tak** . Pakiet NuGet [Microsoft. Web. librarymanager. Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) został dodany do projektu:

  [!code-xml[](samples/LibManSample/LibManSample.csproj?name=snippet_RestoreOnBuildPackage)]

* Skompiluj projekt, aby upewnić się, że następuje przywracanie pliku LibMan. `Microsoft.Web.LibraryManager.Build` Pakiet wstrzyknąć obiekt docelowy MSBuild, który uruchamia LibMan podczas operacji kompilowania projektu.
* Przejrzyj źródło danych **kompilacji** w oknie **danych wyjściowych** dla dziennika aktywności LibMan:

  ```console
  1>------ Build started: Project: LibManSample, Configuration: Debug Any CPU ------
  1>
  1>Restore operation started...
  1>Restoring library jquery@3.3.1...
  1>Restoring library bootstrap@4.1.3...
  1>
  1>2 libraries restored in 10.66 seconds
  1>LibManSample -> C:\LibManSample\bin\Debug\netcoreapp2.1\LibManSample.dll
  ========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
  ```

Gdy włączone jest zachowanie przywracania po kompilacji, w menu kontekstowym *Libman. JSON* zostanie wyświetlona opcja **Wyłącz Przywracanie bibliotek po stronie klienta w ramach kompilacji** . Wybranie tej opcji spowoduje usunięcie `Microsoft.Web.LibraryManager.Build` odwołania do pakietu z pliku projektu. W związku z tym biblioteki po stronie klienta nie są już przywracane dla każdej kompilacji.

Niezależnie od ustawienia Przywróć na kompilację można ręcznie przywrócić w dowolnym momencie z menu kontekstowego *Libman. JSON* . Aby uzyskać więcej informacji, zobacz [Przywracanie plików ręcznie](#restore-files-manually).

### <a name="restore-files-manually"></a>Ręczne przywracanie plików

Aby ręcznie przywrócić pliki biblioteki:

* Dla wszystkich projektów w rozwiązaniu:
  * Kliknij prawym przyciskiem myszy nazwę rozwiązania w **Eksplorator rozwiązań**.
  * Wybierz opcję **Przywróć biblioteki po stronie klienta** .
* Dla określonego projektu:
  * Kliknij prawym przyciskiem myszy plik *Libman. JSON* w **Eksplorator rozwiązań**.
  * Wybierz opcję **Przywróć biblioteki po stronie klienta** .

Podczas gdy operacja przywracania jest uruchomiona:

* Ikona centrum stanu zadań (TSC) na pasku stanu programu Visual Studio zostanie animowana i zostanie *rozpoczęta operacja przywracania*. Kliknięcie ikony powoduje otwarcie etykietki narzędzia zawierającego listę znanych zadań w tle.
* Komunikaty będą wysyłane do paska stanu i źródła danych programu **Library Manager** okna **danych wyjściowych** . Przykład:

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

## <a name="delete-library-files"></a>Usuń pliki biblioteki

Aby wykonać operację *czyszczenia* , która spowoduje usunięcie plików biblioteki, które zostały wcześniej przywrócone w programie Visual Studio:

* Kliknij prawym przyciskiem myszy plik *Libman. JSON* w **Eksplorator rozwiązań**.
* Wybierz opcję **Wyczyść biblioteki po stronie klienta** .

Aby zapobiec przypadkowemu usunięciu plików nienależących do biblioteki, operacja czyszczenia nie usuwa całych katalogów. Usuwa tylko te pliki, które zostały uwzględnione w poprzednim przywracaniu.

Podczas gdy operacja czyszczenia jest uruchomiona:

* Ikona TSC na pasku stanu programu Visual Studio będzie animowana i zostanie *rozpoczęta operacja odczytywania bibliotek klienckich*. Kliknięcie ikony powoduje otwarcie etykietki narzędzia zawierającego listę znanych zadań w tle.
* Komunikaty są wysyłane do paska stanu i źródła danych programu **Library Manager** okna **danych wyjściowych** . Przykład:

```console
Clean libraries operation started...
Clean libraries operation completed
2 libraries were successfully deleted in 1.91 secs
```

Operacja czyszczenia usuwa tylko pliki z projektu. Pliki biblioteki znajdują się w pamięci podręcznej w celu szybszego pobierania podczas przyszłych operacji przywracania. Aby zarządzać plikami biblioteki przechowywanymi w pamięci podręcznej komputera lokalnego, użyj [interfejsu wiersza polecenia LibMan](xref:client-side/libman/libman-cli).

## <a name="uninstall-library-files"></a>Odinstaluj pliki biblioteki

Aby odinstalować pliki biblioteki:

* Otwórz plik *Libman. JSON*.
* Umieść karetkę wewnątrz odpowiedniego `libraries` literału obiektu.
* Kliknij ikonę żarówki, która pojawia się na lewym marginesie, a następnie wybierz pozycję ** \<Odinstaluj\<library_name> @ library_version>**:

  ![Opcja menu kontekstowego odinstalowywania biblioteki](_static/uninstall-menu-option.png)

Alternatywnie możesz ręcznie edytować i zapisać manifest LibMan (*LibMan. JSON*). [Operacja przywracania](#restore-library-files) jest uruchamiana, gdy plik zostanie zapisany. Pliki bibliotek, które nie są już zdefiniowane w *Libman. JSON* , są usuwane z projektu.

## <a name="update-library-version"></a>Zaktualizuj wersję biblioteki

Aby sprawdzić dostępność zaktualizowanej wersji biblioteki:

* Otwórz plik *Libman. JSON*.
* Umieść karetkę wewnątrz odpowiedniego `libraries` literału obiektu.
* Kliknij ikonę żarówki, która pojawia się na lewym marginesie. Umieść kursor nad **sprawdzaniem dostępności aktualizacji**.

LibMan sprawdza, czy wersja biblioteki jest nowsza niż zainstalowana wersja. Mogą wystąpić następujące wyniki:

* Jeśli Najnowsza wersja jest już zainstalowana, zostanie wyświetlony komunikat " **nie znaleziono aktualizacji** ".
* Najnowsza stabilna wersja jest wyświetlana, jeśli nie została jeszcze zainstalowana.

  ![Opcja menu kontekstowego wyszukiwania aktualizacji](_static/update-menu-option.png)

* Jeśli dostępna jest wersja wstępna nowsza niż zainstalowana, zostanie wyświetlona wersja wstępna.

Aby dokonać obniżenia poziomu do starszej wersji biblioteki, ręcznie Edytuj plik *Libman. JSON* . Po zapisaniu pliku LibMan [operacji przywracania](#restore-library-files):

* Usuwa nadmiarowe pliki z poprzedniej wersji.
* Dodaje nowe i zaktualizowane pliki z nowej wersji.

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:client-side/libman/libman-cli>
* [Repozytorium GitHub LibMan](https://github.com/aspnet/LibraryManager)
