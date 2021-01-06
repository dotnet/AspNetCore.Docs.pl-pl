---
title: Używanie LibMan z ASP.NET Core w programie Visual Studio
author: scottaddie
description: Dowiedz się, jak używać LibMan w projekcie ASP.NET Core z programem Visual Studio.
ms.author: scaddie
ms.custom: mvc
ms.date: 08/20/2018
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
uid: client-side/libman/libman-vs
ms.openlocfilehash: 1c97f5d7fbf64c5043e6d2277091b9a477833bf1
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "93054713"
---
# <a name="use-libman-with-aspnet-core-in-visual-studio"></a>Używanie LibMan z ASP.NET Core w programie Visual Studio

Przez [Scott Addie](https://twitter.com/Scott_Addie)

Program Visual Studio ma wbudowaną obsługę [LibMan](xref:client-side/libman/index) w projektach ASP.NET Core, w tym:

* Obsługa konfigurowania i uruchamiania operacji przywracania LibMan podczas kompilacji.
* Elementy menu służące do wyzwalania operacji przywracania i czyszczenia LibMan.
* Okno dialogowe wyszukiwania służące do znajdowania bibliotek i dodawania plików do projektu.
* Edytowanie obsługi *libman.jsw* &mdash; pliku manifestu LibMan.

[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) [(jak pobrać)](xref:index#how-to-download-a-sample)

## <a name="prerequisites"></a>Wymagania wstępne

* [Program Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) z **ASP.NET i programowaniem aplikacji sieci Web**

## <a name="add-library-files"></a>Dodaj pliki biblioteki

Pliki bibliotek można dodać do projektu ASP.NET Core na dwa różne sposoby:

1. [Korzystanie z okna dialogowego Dodawanie biblioteki Client-Side](#use-the-add-client-side-library-dialog)
1. [Ręczne konfigurowanie wpisów pliku manifestu LibMan](#manually-configure-libman-manifest-file-entries)

### <a name="use-the-add-client-side-library-dialog"></a>Korzystanie z okna dialogowego Dodawanie biblioteki Client-Side

Wykonaj następujące kroki, aby zainstalować bibliotekę po stronie klienta:

* W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy folder projektu, w którym należy dodać pliki. Wybierz pozycję **Dodaj**  >  **bibliotekę po stronie klienta**. Zostanie wyświetlone okno dialogowe **dodawanie Client-Side biblioteki** :

  ![Okno dialogowe Dodawanie biblioteki Client-Side](_static/add-library-dialog.png)

* Wybierz dostawcę biblioteki z listy rozwijanej **dostawca** . CDNJS jest dostawcą domyślnym.
* Wpisz nazwę biblioteki do pobrania w polu tekstowym **Biblioteka** . Technologia IntelliSense udostępnia listę bibliotek zaczynających się od podanego tekstu.
* Wybierz bibliotekę z listy IntelliSense. Zwróć uwagę, że nazwa biblioteki jest poddana sufiksu `@` i najnowszej wersji stabilnej znanej dla wybranego dostawcy.
* Wybieranie plików do uwzględnienia:
  * Zaznacz przycisk radiowy **Dołącz wszystkie pliki bibliotek** , aby uwzględnić wszystkie pliki biblioteki.
  * Wybierz przycisk radiowy **Wybierz określone pliki** , aby dołączyć podzestaw plików biblioteki. Po wybraniu przycisku radiowego drzewo selektora plików jest włączone. Zaznacz pola po lewej stronie nazw plików do pobrania.
* Określ folder projektu do przechowywania plików w polu tekstowym **Lokalizacja docelowa** . Zgodnie z zaleceniami należy przechowywać każdą bibliotekę w osobnym folderze.

  Sugerowany folder **lokalizacji docelowej** jest oparty na lokalizacji, w której uruchomiono okno dialogowe:

  * Jeśli jest uruchamiany z poziomu głównego projektu:
    * plik *wwwroot/lib* jest używany, jeśli istnieje plik *wwwroot* .
    * *Biblioteka lib* jest używana, jeśli plik *wwwroot* nie istnieje.
  * W przypadku uruchomienia z folderu projektu zostanie użyta odpowiednia nazwa folderu.

  Sugestia folderu jest sufiksem z nazwą biblioteki. W poniższej tabeli przedstawiono sugestie dotyczące folderów podczas instalowania jQuery w Razor projekcie stron.
  
  |Lokalizacja uruchamiania                           |Sugerowany folder      |
  |------------------------------------------|----------------------|
  |Katalog główny projektu (Jeśli folder *wwwroot* istnieje)        |*wwwroot/lib/jQuery/* |
  |Katalog główny projektu (Jeśli folder *wwwroot* nie istnieje) |*lib/jQuery/*         |
  |Folder *stron* w projekcie                 |*Strony/jQuery/*       |

* Kliknij przycisk **Zainstaluj** , aby pobrać pliki, na konfigurację w *libman.jsna*.
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

Wszystkie operacje LibMan w programie Visual Studio opierają się na zawartości manifestu LibMan głównego projektu (*libman.json*). Możesz ręcznie edytować *libman.jsw* celu skonfigurowania plików biblioteki dla projektu. Program Visual Studio przywraca wszystkie pliki bibliotek, gdy *libman.json* jest zapisywany.

Aby otworzyć *libman.jsna* potrzeby edycji, dostępne są następujące opcje:

* Kliknij dwukrotnie *libman.jsw* pliku w **Eksplorator rozwiązań**.
* Kliknij prawym przyciskiem myszy projekt w **Eksplorator rozwiązań** i wybierz polecenie **zarządzaj bibliotekami Client-Side**. **&#8224;**
* Wybierz pozycję **Zarządzaj bibliotekami Client-Side** z menu **projektu** programu Visual Studio. **&#8224;**

**&#8224;** Jeśli *libman.jsw* pliku nie istnieje już w katalogu głównym projektu, zostanie utworzony z domyślną zawartością szablonu elementu.

Program Visual Studio oferuje zaawansowane funkcje edycji JSON, takie jak kolorowanie, formatowanie, IntelliSense i walidacja schematu. Schemat JSON manifestu LibMan został znaleziony w lokalizacji [https://json.schemastore.org/libman](https://json.schemastore.org/libman) .

Przy użyciu następującego pliku manifestu LibMan pobiera pliki zgodnie z konfiguracją zdefiniowaną we `libraries` właściwości. Wyjaśnienie literałów obiektów zdefiniowanych w `libraries` następujący sposób:

* Podzestaw [jQuery](https://jquery.com/) w wersji 3.3.1 jest pobierany z dostawcy CDNJS. Podzestaw jest zdefiniowany we `files` właściwościach &mdash; *jquery.min.js*, *jquery.js* i *jQuery. min. map*. Pliki są umieszczane w folderze *wwwroot/lib/jQuery* projektu.
* W [całości wersja 4.1.3](https://getbootstrap.com/) jest pobierana i umieszczana w folderze *wwwroot/lib/Bootstrap* . Właściwość literału obiektu `provider` zastępuje `defaultProvider` wartość właściwości. LibMan pobiera pliki Bootstrap z dostawcy unpkg.
* Podzbiór [Lodash](https://lodash.com/) został zatwierdzony przez organ regulujący w organizacji. Pliki *lodash.js* i *lodash.min.js* są pobierane z lokalnego systemu plików w lokalizacji *C: \\ temp \\ lodash \\*. Pliki są kopiowane do folderu *wwwroot/lib/lodash* projektu.

[!code-json[](samples/LibManSample/libman.json)]

> [!NOTE]
> LibMan obsługuje tylko jedną wersję każdej biblioteki z każdego dostawcy. Sprawdzanie poprawności schematu *libman.jsw* pliku kończy się niepowodzeniem, jeśli zawiera dwie biblioteki o tej samej nazwie biblioteki dla danego dostawcy.

## <a name="restore-library-files"></a>Przywróć pliki biblioteki

Aby przywrócić pliki bibliotek z programu Visual Studio, w katalogu głównym projektu musi znajdować się prawidłowy *libman.js* . Przywrócone pliki są umieszczane w projekcie w lokalizacji określonej dla każdej biblioteki.

Pliki bibliotek można przywrócić w projekcie ASP.NET Core na dwa sposoby:

1. [Przywróć pliki podczas kompilacji](#restore-files-during-build)
1. [Ręczne przywracanie plików](#restore-files-manually)

### <a name="restore-files-during-build"></a>Przywróć pliki podczas kompilacji

LibMan może przywrócić zdefiniowane pliki biblioteki w ramach procesu kompilacji. Domyślnie zachowanie funkcji *Przywróć przy kompilacji* jest wyłączone.

Aby włączyć i przetestować zachowanie funkcji przywracania po kompilacji:

* Kliknij prawym przyciskiem myszy pozycję *libman.jsna* **Eksplorator rozwiązań** , a następnie wybierz pozycję **Włącz przywracanie bibliotek Client-Side w kompilacji** z menu kontekstowego.
* Po wyświetleniu monitu o zainstalowanie pakietu NuGet kliknij przycisk **tak** . Pakiet NuGet [Microsoft. Web. librarymanager. Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) został dodany do projektu:

  [!code-xml[](samples/LibManSample/LibManSample.csproj?name=snippet_RestoreOnBuildPackage)]

* Skompiluj projekt, aby upewnić się, że następuje przywracanie pliku LibMan. `Microsoft.Web.LibraryManager.Build`Pakiet wstrzyknąć obiekt docelowy MSBuild, który uruchamia LibMan podczas operacji kompilowania projektu.
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

Gdy włączone jest zachowanie przywracania po kompilacji, *libman.jsw* menu kontekstowym zostanie wyświetlona opcja **Wyłącz Przywracanie Client-Side podczas kompilacji** . Wybranie tej opcji spowoduje usunięcie `Microsoft.Web.LibraryManager.Build` odwołania do pakietu z pliku projektu. W związku z tym biblioteki po stronie klienta nie są już przywracane dla każdej kompilacji.

Bez względu na ustawienie Przywróć na kompilację można ręcznie przywrócić w dowolnym momencie z *libman.jsw* menu kontekstowym. Aby uzyskać więcej informacji, zobacz [Przywracanie plików ręcznie](#restore-files-manually).

### <a name="restore-files-manually"></a>Ręczne przywracanie plików

Aby ręcznie przywrócić pliki biblioteki:

* Dla wszystkich projektów w rozwiązaniu:
  * Kliknij prawym przyciskiem myszy nazwę rozwiązania w **Eksplorator rozwiązań**.
  * Wybierz opcję **Przywróć biblioteki Client-Side** .
* Dla określonego projektu:
  * Kliknij prawym przyciskiem myszy *libman.jsw* pliku w **Eksplorator rozwiązań**.
  * Wybierz opcję **Przywróć biblioteki Client-Side** .

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

* Kliknij prawym przyciskiem myszy *libman.jsw* pliku w **Eksplorator rozwiązań**.
* Wybierz opcję **wyczyść Client-Side biblioteki** .

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

* Otwórz *libman.jsna*.
* Umieść karetkę wewnątrz odpowiedniego `libraries` literału obiektu.
* Kliknij ikonę żarówki, która pojawia się na lewym marginesie, a następnie wybierz pozycję **Odinstaluj \<library_name> @ \<library_version>**:

  ![Opcja menu kontekstowego odinstalowywania biblioteki](_static/uninstall-menu-option.png)

Alternatywnie można ręcznie edytować i zapisać manifest LibMan (*libman.json*). [Operacja przywracania](#restore-library-files) jest uruchamiana, gdy plik zostanie zapisany. Pliki bibliotek, które nie są już zdefiniowane w *libman.jsna* są usuwane z projektu.

## <a name="update-library-version"></a>Zaktualizuj wersję biblioteki

Aby sprawdzić dostępność zaktualizowanej wersji biblioteki:

* Otwórz *libman.jsna*.
* Umieść karetkę wewnątrz odpowiedniego `libraries` literału obiektu.
* Kliknij ikonę żarówki, która pojawia się na lewym marginesie. Umieść kursor nad **sprawdzaniem dostępności aktualizacji**.

LibMan sprawdza, czy wersja biblioteki jest nowsza niż zainstalowana wersja. Mogą wystąpić następujące wyniki:

* Jeśli Najnowsza wersja jest już zainstalowana, zostanie wyświetlony komunikat " **nie znaleziono aktualizacji** ".
* Najnowsza stabilna wersja jest wyświetlana, jeśli nie została jeszcze zainstalowana.

  ![Opcja menu kontekstowego wyszukiwania aktualizacji](_static/update-menu-option.png)

* Jeśli dostępna jest wersja wstępna nowsza niż zainstalowana, zostanie wyświetlona wersja wstępna.

Aby zmienić wersję na starszą dla starszej wersji biblioteki, ręcznie Edytuj *libman.js* pliku. Po zapisaniu pliku LibMan [operacji przywracania](#restore-library-files):

* Usuwa nadmiarowe pliki z poprzedniej wersji.
* Dodaje nowe i zaktualizowane pliki z nowej wersji.

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:client-side/libman/libman-cli>
* [Repozytorium GitHub LibMan](https://github.com/aspnet/LibraryManager)
