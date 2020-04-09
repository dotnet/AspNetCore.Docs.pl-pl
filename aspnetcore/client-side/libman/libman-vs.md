---
title: Używanie programu LibMan z ASP.NET Core w programie Visual Studio
author: scottaddie
description: Dowiedz się, jak używać programu LibMan w projekcie ASP.NET Core w programie Visual Studio.
ms.author: scaddie
ms.custom: mvc
ms.date: 08/20/2018
uid: client-side/libman/libman-vs
ms.openlocfilehash: e92e6bc28ec58b26785dd6c79e71512368202a26
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658313"
---
# <a name="use-libman-with-aspnet-core-in-visual-studio"></a>Używanie programu LibMan z ASP.NET Core w programie Visual Studio

Przez [Scott Addie](https://twitter.com/Scott_Addie)

Visual Studio ma wbudowaną obsługę [libman](xref:client-side/libman/index) w projektach ASP.NET Core, w tym:

* Obsługa konfigurowania i uruchamiania operacji przywracania LibMan na kompilacji.
* Elementy menu do wyzwalania libman przywracania i czyste operacje.
* Wyszukaj okno dialogowe znajdowania bibliotek i dodawania plików do projektu.
* Obsługa edycji pliku manifestu *libman.json.*&mdash;

[Wyświetlanie lub pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) [(jak pobrać)](xref:index#how-to-download-a-sample)

## <a name="prerequisites"></a>Wymagania wstępne

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) z **obciążeniem ASP.NET i tworzenia stron internetowych**

## <a name="add-library-files"></a>Dodawanie plików biblioteki

Pliki bibliotek można dodawać do projektu ASP.NET Core na dwa różne sposoby:

1. [Okno dialogowe Dodawanie biblioteki po stronie klienta](#use-the-add-client-side-library-dialog)
1. [Ręczne konfigurowanie wpisów plików manifestu LibMan](#manually-configure-libman-manifest-file-entries)

### <a name="use-the-add-client-side-library-dialog"></a>Okno dialogowe Dodawanie biblioteki po stronie klienta

Aby zainstalować bibliotekę po stronie klienta, wykonaj następujące czynności:

* W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy folder projektu, w którym należy dodać pliki. Wybierz **pozycję Dodaj** > **bibliotekę po stronie klienta**. Zostanie wyświetlone okno dialogowe **Dodaj bibliotekę po stronie klienta:**

  ![Okno dialogowe Dodawanie biblioteki po stronie klienta](_static/add-library-dialog.png)

* Wybierz dostawcę biblioteki z listy rozwijanej **Dostawca.** Usługa CDNJS jest dostawcą domyślnym.
* Wpisz nazwę biblioteki do pobrania w polu tekstowym **Biblioteka.** IntelliSense udostępnia listę bibliotek, począwszy od podanego tekstu.
* Wybierz bibliotekę z listy IntelliSense. Zwróć uwagę, że nazwa biblioteki `@` jest sufiksem z symbolem i najnowszą stabilną wersją znaną wybranemu dostawcy.
* Zdecyduj, które pliki mają być dołączane:
  * Wybierz przycisk opcji **Dołącz wszystkie pliki biblioteki,** aby uwzględnić wszystkie pliki biblioteki.
  * Wybierz przycisk opcji **Wybierz określone pliki,** aby dołączyć podzbiór plików biblioteki. Po wybraniu przycisku opcji jest włączone drzewo selektora plików. Zaznacz pola po lewej stronie nazw plików do pobrania.
* Określ folder projektu do przechowywania plików w polu **tekstowym Lokalizacja docelowa.** Jako zalecenie należy przechowywać każdą bibliotekę w osobnym folderze.

  Sugerowany folder **Lokalizacja docelowa** jest oparty na lokalizacji, z której uruchomiono okno dialogowe:

  * Jeśli zostanie uruchomiony z katalogu głównego projektu:
    * *wwwroot/lib* jest używany, jeśli *wwwroot* istnieje.
    * *lib* jest używany, jeśli *wwwroot* nie istnieje.
  * Jeśli zostanie uruchomiony z folderu projektu, używana jest odpowiednia nazwa folderu.

  Sugestia folderu jest sufiksem z nazwą biblioteki. W poniższej tabeli przedstawiono sugestie folderów podczas instalowania jQuery w projekcie Razor Pages.
  
  |Miejsce uruchomienia                           |Sugerowany folder      |
  |------------------------------------------|----------------------|
  |główny projekt (jeśli *wwwroot* istnieje)        |*wwwroot/lib/jquery/* |
  |główny projekt (jeśli *wwwroot* nie istnieje) |*lib/jquery/*         |
  |*Folder Strony* w projekcie                 |*Strony/jquery/*       |

* Kliknij przycisk **Zainstaluj,** aby pobrać pliki, według konfiguracji w *libman.json*.
* Przejrzyj kanał informacyjny **Menedżera bibliotek** okna **Dane wyjściowe,** aby uzyskać szczegółowe informacje na temat instalacji. Przykład:

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

### <a name="manually-configure-libman-manifest-file-entries"></a>Ręczne konfigurowanie wpisów plików manifestu LibMan

Wszystkie operacje LibMana w programie Visual Studio są oparte na zawartości manifestu LibMan głównego projektu (*libman.json*). Można ręcznie edytować *libman.json,* aby skonfigurować pliki biblioteki dla projektu. Program Visual Studio przywraca wszystkie pliki biblioteki po *zapisaniu pliku libman.json.*

Aby otworzyć *libman.json* do edycji, istnieją następujące opcje:

* Kliknij dwukrotnie plik *libman.json* w **Eksploratorze rozwiązań**.
* Kliknij prawym przyciskiem myszy projekt w **Eksploratorze rozwiązań** i wybierz pozycję **Zarządzaj bibliotekami po stronie klienta**. **&#8224;**
* Z menu Programu Visual Studio **Project** wybierz polecenie **Zarządzaj bibliotekami po stronie klienta.** **&#8224;**

**&#8224;** Jeśli plik *libman.json* jeszcze nie istnieje w katalogu głównym projektu, zostanie utworzony przy zachowaniu domyślnej zawartości szablonu elementu.

Program Visual Studio oferuje bogatą obsługę edycji JSON, taką jak kolorowanie, formatowanie, intellisense i sprawdzanie poprawności schematu. Schemat JSON manifestu LibMana znajduje [https://json.schemastore.org/libman](https://json.schemastore.org/libman)się w pliku .

Z następującym plikiem manifestu LibMan pobiera pliki `libraries` dla konfiguracji zdefiniowanej we właściwości. Wyjaśnienie literałów obiektu zdefiniowanych `libraries` w następujący sposób:

* Podzbiór [jQuery](https://jquery.com/) w wersji 3.3.1 jest pobierany od dostawcy usługi CDNJS. Podzbiór jest `files` zdefiniowany w właściwości&mdash;*jquery.min.js*, *jquery.js*i *jquery.min.map*. Pliki są umieszczane w folderze *wwwroot/lib/jquery* projektu.
* Całość wersji 4.1.3 [Bootstrap](https://getbootstrap.com/) jest pobierana i umieszczana w folderze *wwwroot/lib/bootstrap.* `provider` Właściwość dosłownego obiektu zastępuje `defaultProvider` wartość właściwości. LibMan pobiera pliki Bootstrap z dostawcy unpkg.
* Podzbiór [Lodash](https://lodash.com/) został zatwierdzony przez organ zarządzający w organizacji. Pliki *lodash.js* i *lodash.min.js* są pobierane z lokalnego systemu plików w *C:\\temp\\lodash\\*. Pliki są kopiowane do folderu *wwwroot/lib/lodash* projektu.

[!code-json[](samples/LibManSample/libman.json)]

> [!NOTE]
> LibMan obsługuje tylko jedną wersję każdej biblioteki od każdego dostawcy. Plik *libman.json* nie zostanie zawiedzieny schematu, jeśli zawiera dwie biblioteki o tej samej nazwie biblioteki dla danego dostawcy.

## <a name="restore-library-files"></a>Przywracanie plików biblioteki

Aby przywrócić pliki biblioteki z poziomu programu Visual Studio, w katalogu głównym projektu musi znajdować się prawidłowy plik *libman.json.* Przywrócone pliki są umieszczane w projekcie w lokalizacji określonej dla każdej biblioteki.

Pliki bibliotek można przywrócić w projekcie ASP.NET Core na dwa sposoby:

1. [Przywracanie plików podczas kompilacji](#restore-files-during-build)
1. [Ręczne przywracanie plików](#restore-files-manually)

### <a name="restore-files-during-build"></a>Przywracanie plików podczas kompilacji

LibMan można przywrócić zdefiniowane pliki biblioteki w ramach procesu kompilacji. Domyślnie zachowanie *przywracania na kompilacji* jest wyłączone.

Aby włączyć i przetestować zachowanie przywracania przy kompilacji:

* Kliknij prawym przyciskiem myszy *libman.json* w **Eksploratorze rozwiązań** i wybierz **polecenie Włącz przywracanie bibliotek po stronie klienta w menu** kontekstowym.
* Kliknij przycisk **Tak** po wyświetleniu monitu o zainstalowanie pakietu NuGet. Pakiet [Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) NuGet jest dodawany do projektu:

  [!code-xml[](samples/LibManSample/LibManSample.csproj?name=snippet_RestoreOnBuildPackage)]

* Tworzenie projektu, aby potwierdzić przywrócenie pliku LibMan występuje. Pakiet `Microsoft.Web.LibraryManager.Build` wstrzykuje obiekt docelowy MSBuild, który uruchamia LibMan podczas operacji kompilacji projektu.
* Przejrzyj **źródło** danych kompilacji w oknie **dane wyjściowe** dla dziennika aktywności LibMan:

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

Gdy zachowanie przywracania na kompilacji jest włączone, w menu kontekstowym *libman.json* jest wyświetlana opcja **Wyłącz przywracanie bibliotek po stronie klienta w kompilacji.** Wybranie tej opcji `Microsoft.Web.LibraryManager.Build` powoduje usunięcie odwołania do pakietu z pliku projektu. W związku z tym biblioteki po stronie klienta nie są już przywracane na każdej kompilacji.

Niezależnie od ustawienia przywracania na kompilacji, można ręcznie przywrócić w dowolnym momencie z menu kontekstowego *libman.json.* Aby uzyskać więcej informacji, zobacz [Ręczne przywracanie plików](#restore-files-manually).

### <a name="restore-files-manually"></a>Ręczne przywracanie plików

Aby ręcznie przywrócić pliki biblioteki:

* Dla wszystkich projektów w rozwiązaniu:
  * Kliknij prawym przyciskiem myszy nazwę rozwiązania w **Eksploratorze rozwiązań**.
  * Wybierz opcję **Przywróć biblioteki po stronie klienta.**
* Dla określonego projektu:
  * Kliknij prawym przyciskiem myszy plik *libman.json* w **Eksploratorze rozwiązań**.
  * Wybierz opcję **Przywróć biblioteki po stronie klienta.**

Gdy operacja przywracania jest uruchomiona:

* Ikona Centrum stanu zadań (TSC) na pasku stanu programu Visual Studio zostanie animowana i zostanie uruchomiona *operacja Przywracanie*. Kliknięcie ikony powoduje otwarcie etykietki narzędzia zawierającej listę znanych zadań w tle.
* Wiadomości będą wysyłane do paska stanu i kanału informacyjnego **Menedżera bibliotek** w oknie **Dane wyjściowe.** Przykład:

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

## <a name="delete-library-files"></a>Usuwanie plików biblioteki

Aby wykonać *czystą* operację, która usuwa pliki biblioteki wcześniej przywrócone w programie Visual Studio:

* Kliknij prawym przyciskiem myszy plik *libman.json* w **Eksploratorze rozwiązań**.
* Wybierz opcję **Wyczyść biblioteki po stronie klienta.**

Aby zapobiec niezamierzonemu usunięciu plików spoza biblioteki, czysta operacja nie powoduje usunięcia całych katalogów. Usuwa tylko pliki, które zostały uwzględnione w poprzednim przywracaniu.

Gdy czysta operacja jest uruchomiona:

* Ikona TSC na pasku stanu programu Visual Studio zostanie animowana i zostanie *uruchomiona operacja bibliotek klienta.* Kliknięcie ikony powoduje otwarcie etykietki narzędzia zawierającej listę znanych zadań w tle.
* Wiadomości są wysyłane do paska stanu i kanału informacyjnego **Menedżera bibliotek** w oknie **Dane wyjściowe.** Przykład:

```console
Clean libraries operation started...
Clean libraries operation completed
2 libraries were successfully deleted in 1.91 secs
```

Czysta operacja usuwa tylko pliki z projektu. Pliki biblioteki pozostają w pamięci podręcznej, aby przyspieszyć pobieranie przyszłych operacji przywracania. Aby zarządzać plikami bibliotek przechowywanymi w pamięci podręcznej komputera lokalnego, należy użyć [interfejsu wiersza polecenia LibMan](xref:client-side/libman/libman-cli).

## <a name="uninstall-library-files"></a>Odinstalowywanie plików biblioteki

Aby odinstalować pliki biblioteki:

* Otwórz *libman.json*.
* Umieść cieszę `libraries` wewnątrz odpowiedniego obiektu dosłownego.
* Kliknij ikonę żarówki wyświetlaną na lewym marginesie, a następnie wybierz **pozycję Odinstaluj \<library_name>@\<library_version>: **

  ![Opcja menu kontekstowego biblioteki odinstalowywania](_static/uninstall-menu-option.png)

Alternatywnie można ręcznie edytować i zapisywać manifest LibMana (*libman.json*). [Operacja przywracania](#restore-library-files) jest uruchamiana po zapisaniu pliku. Pliki biblioteki, które nie są już zdefiniowane w *libman.json* są usuwane z projektu.

## <a name="update-library-version"></a>Aktualizowanie wersji biblioteki

Aby sprawdzić dostępność zaktualizowanej wersji biblioteki:

* Otwórz *libman.json*.
* Umieść cieszę `libraries` wewnątrz odpowiedniego obiektu dosłownego.
* Kliknij ikonę żarówki, która pojawi się na lewym marginesie. Umieść wskaźnik myszy **na czeku pod kątem aktualizacji**.

LibMan sprawdza, czy wersja biblioteki jest nowsza niż zainstalowana wersja. Mogą wystąpić następujące wyniki:

* Komunikat **Brak znalezienia aktualizacji** jest wyświetlany, jeśli najnowsza wersja jest już zainstalowana.
* Najnowsza stabilna wersja jest wyświetlana, jeśli nie jest jeszcze zainstalowana.

  ![Sprawdź, czy opcja menu kontekstowego aktualizacji](_static/update-menu-option.png)

* Jeśli dostępna jest wersja wstępna nowsza niż zainstalowana wersja, zostanie wyświetlona wersja wstępna.

Aby przejść na starszą wersję biblioteki, należy ręcznie edytować plik *libman.json.* Po zapisaniu pliku operacja [przywracania](#restore-library-files)LibMan:

* Usuwa nadmiarowe pliki z poprzedniej wersji.
* Dodaje nowe i zaktualizowane pliki z nowej wersji.

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:client-side/libman/libman-cli>
* [Repozytorium LibMan GitHub](https://github.com/aspnet/LibraryManager)
