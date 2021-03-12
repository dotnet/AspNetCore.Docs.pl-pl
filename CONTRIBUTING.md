# <a name="contribute-to-the-aspnet-core-documentation"></a>Współtworzenie dokumentacji ASP.NET Core

W tym dokumencie omówiono proces współtworzenia artykułów i przykładów kodu, które są hostowane w [witrynie dokumentacji ASP.NET](https://docs.microsoft.com/aspnet/). Poprawki błędów i nowe artykuły są wkładami powitalnymi.

## <a name="how-to-make-a-simple-correction-or-suggestion"></a>Jak wykonać prostą korektę lub sugestię

Artykuły są przechowywane w repozytorium jako pliki o promocji. Proste zmiany zawartości pliku promocji są wprowadzane w przeglądarce, wybierając link **Edytuj** w prawym górnym rogu okna przeglądarki. (W wąskim oknie przeglądarki Rozwiń pasek **opcji** , aby wyświetlić łącze **Edytuj** ). Postępuj zgodnie z instrukcjami, aby utworzyć żądanie ściągnięcia. Sprawdzimy żądanie ściągnięcia i zaakceptujemy je lub zasugerujemy zmiany.

## <a name="how-to-make-a-more-complex-submission"></a>Jak utworzyć bardziej złożone przesyłanie

Potrzebna jest podstawowa znajomość usługi [git i GitHub.com](https://guides.github.com/activities/hello-world/).

* Otwórz [problem](https://github.com/dotnet/AspNetCore.Docs/issues/new) z opisem tego, co chcesz zrobić, na przykład zmieniając istniejący artykuł lub Utwórz nowy. Często żądamy konspektu dla nowej sugestii tematu. Zaczekaj na zatwierdzenie przez zespół przed zainwestowaniem dużo czasu.
* Rozwidlenie repozytorium [ASPNET/docs](https://github.com/dotnet/AspNetCore.Docs/) i utworzenie gałęzi dla zmian.
* Prześlij żądanie ściągnięcia do *głównej* gałęzi ze zmianami.
* Jeśli do żądania ściągnięcia jest przypisana etykieta "CLA-Required", [Wypełnij umowę licencyjną dotyczącą udziału (CLA)](https://cla.dotnetfoundation.org/).
* Odpowiedź na opinię żądania ściągnięcia.

Przykład, w którym ten proces przeprowadził publikację nowego artykułu, zobacz artykuł [dotyczący problemu 1477](https://github.com/dotnet/docs/issues/1477) i [żądania ściągnięcia 18955](https://github.com/dotnet/docs/pull/18955) w repozytorium dokumentacji platformy .NET. W nowym artykule jest [używane pokrycie kodu dla testów jednostkowych](/dotnet/core/testing/unit-testing-code-coverage).

## <a name="docs-authoring-pack-extension-in-visual-studio-code"></a>Rozszerzenie docs Authoring Pack w Visual Studio Code

Jeśli używasz Visual Studio Code do współtworzenia dokumentacji ASP.NET, możesz zwiększyć produktywność, instalując rozszerzenie [docs Authoring Pack](https://marketplace.visualstudio.com/items?itemName=docsmsft.docs-authoring-pack) . Rozszerzenie zawiera wiele narzędzi, które ułatwiają Zaznaczanie błędów, sprawdzanie pisowni kodu i szablony artykułów.

## <a name="markdown-syntax"></a>Składnia promocji

Artykuły są zapisywane w [DocFxej promocji](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html), która jest nadzbiorem promocji z [obsługą usługi GitHub (GFM)](https://guides.github.com/features/mastering-markdown/). Aby zapoznać się z przykładami składni DFM dla funkcji interfejsu użytkownika często używanych w dokumentacji ASP.NET, zobacz artykuł [Metadata and promocji — szablon](https://github.com/dotnet/docs/blob/main/styleguide/template.md) w przewodniku po stylu repozytorium .NET docs. 

## <a name="folder-structure-conventions"></a>Konwencje struktury folderów

Dla każdego pliku z promocji, może istnieć folder obrazów i folderu dla przykładowego kodu. Jeśli artykuł ma [podstawowe znaczenie/konfiguracja/index. MD](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/fundamentals/configuration/index.md), obrazy znajdują się w tematach [podstawowe/konfiguracja/indeks/ \_ statyczny](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/index/_static) , a pliki projektu aplikacji przykładowych znajdują się w temacie [podstawy/konfiguracja/indeks/przykład](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/index/sample). Obraz w pliku *Fundamentals/Configuration/index. MD* jest renderowany przy użyciu następującej promocji:

```md
![description of image for alt attribute](configuration/index/_static/imagename.png)
```

Wszystkie obrazy powinny mieć [alternatywny tekst (Alt)](https://wikipedia.org/wiki/Alt_attribute). Aby uzyskać poradę dotyczącą określania tekstu alternatywnego, zobacz zasoby online, takie jak [WebAIM: tekst alternatywny](https://webaim.org/techniques/alttext/).

Używaj małych liter w nazwach plików i plikach obrazu z przeznaczeniem.

## <a name="internal-links"></a>Linki wewnętrzne

Linki wewnętrzne powinny korzystać z `uid` artykułu docelowego z linkiem linki XREF (tekst linku jest ustawiany na tytuł połączonej zawartości):

```md
<xref:uid_of_the_topic>
```

Jeśli tytuł artykułu jest nieodpowiedni dla tekstu łącza (na przykład słowo lub fraza w zdaniu jest tekstem łącza), określ link linki XREF i Połącz tekst z poniższymi:

```md
[link text](xref:uid_of_the_topic)
```

Aby uzyskać więcej informacji, zobacz [DocFX cross reference](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#cross-reference).

## <a name="images-and-screenshots"></a>Obrazy i zrzuty ekranu

Nie dołączaj obrazów do artykułów, z wyjątkiem:

* W podstawowych samouczkach dołączania (początkujących).
* Gdy obraz jest niezbędny do przejrzystości.

Ograniczenia te zmniejszają rozmiar repozytorium.

Opcjonalnym krokiem jest upewnienie się, że wszystkie obrazy i zrzuty ekranu używane w dokumentacji są skompresowane, co pomaga w zapewnieniu rozmiaru pliku i wydajności ładowania strony. Niektóre popularne narzędzia obejmują TinyPNG (przy użyciu [witryny sieci Web TinyPNG](https://tinypng.com/) lub [interfejsu API TinyPNG](https://tinypng.com/developers)) lub rozszerzenie programu Visual Studio dla [Optymalizatora obrazów](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.ImageOptimizer) . 

## <a name="code-snippets"></a>Fragmenty kodu

Artykuły często zawierają fragmenty kodu przedstawiające punkty. DFM umożliwia skopiowanie kodu do pliku o promocji lub zapoznaj się z osobnym plikiem kodu. Wolimy używać oddzielnych plików kodu, jeśli jest to możliwe, aby zminimalizować prawdopodobieństwo wystąpienia błędów w kodzie. Pliki kodu są przechowywane w repozytorium przy użyciu struktury folderów opisanej wcześniej dla przykładowych projektów. 

W poniższych przykładach przedstawiono [składnię fragmentu kodu DFM](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#code-snippet) do użycia w pliku *Configuration/index. MD* .

Aby renderować cały plik kodu jako fragment:

```md
[!code-csharp[](configuration/index/sample/Program.cs)]
```

Aby renderować część pliku jako fragment przy użyciu numerów wierszy:

```md
[!code-csharp[](configuration/index/sample/Program.cs?range=1-10,20,30,40-50]
[!code-html[](configuration/index/sample/Views/Home/Index.cshtml?range=1-10,20,30,40-50]
```

W przypadku fragmentów języka C# odwołuje się do [regionu języka c#](https://docs.microsoft.com/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region). Jeśli to możliwe, użyj regionów zamiast numerów wierszy, ponieważ numery wierszy w pliku kodu są zmieniane i nie są zsynchronizowane z odwołaniami do numerów wierszy w ramach promocji. Regiony języka C# mogą być zagnieżdżane. W przypadku odwoływania się do regionu zewnętrznego wewnętrzne `#region` i `#endregion` dyrektywy nie są renderowane w fragmencie kodu. 

Aby renderować region języka C# o nazwie "snippet_Example":

```md
[!code-csharp[](configuration/index/sample/Program.cs?name=snippet_Example)]
```

Aby wyróżnić wybrane wiersze w renderowanym fragmencie (zwykle jest to kolor żółtego tła):

```md
[!code-csharp[](configuration/index/sample/Program.cs?name=snippet_Example&highlight=1-3,10,20-25)]
[!code-csharp[](configuration/index/sample/Program.cs?range=10-20&highlight=1-3]
[!code-html[](configuration/index/sample/Views/Home/Index.cshtml?range=10-20&highlight=1-3]
[!code-javascript[](configuration/index/sample/UsingOptionsSample.csproj?range=10-20&highlight=1-3]
```

## <a name="test-changes-with-docfx"></a>Testowanie zmian za pomocą DocFX

Przetestuj zmiany za pomocą [narzędzia wiersza polecenia DocFX](https://dotnet.github.io/docfx/tutorial/docfx_getting_started.html#2-use-docfx-as-a-command-line-tool), które tworzy lokalnie hostowaną wersję lokacji. DocFX nie renderuje stylu i rozszerzeń witryny utworzonych dla docs.microsoft.com.

DocFX wymaga:

* .NET Framework w systemie Windows.
* Mono dla systemu Linux lub macOS. 

### <a name="windows-instructions"></a>Instrukcje dotyczące systemu Windows

* Pobierz i rozpakuj *docfx.zip* z [wersji DocFX](https://github.com/dotnet/docfx/releases).
* Dodaj DocFX do ścieżki.
* W powłoce poleceń przejdź do folderu, który zawiera *docfx.jsw* pliku (*ASPNET* for ASP.net content lub *aspnetcore* for ASP.NET Core Content) i uruchom następujące polecenie:

  ```console
  docfx --serve
  ```

* W przeglądarce przejdź do `http://localhost:8080/group1-dest/` .

### <a name="mono-instructions"></a>Instrukcje dotyczące narzędzia mono

* Zainstaluj program mono za pośrednictwem oprogramowania Homebrew:

  ```console
  brew install mono
  ```

* Pobierz [najnowszą wersję programu DocFX](https://github.com/dotnet/docfx/releases).
* Wyodrębnij archiwum do *$Home/bin/docfx*.
* Utwórz parę aliasów dla **docfx** w bash Shell. Pierwszy alias jest używany do kompilowania dokumentacji. Drugi alias jest używany do kompilowania i obsłużenia dokumentacji.

  ```console
  alias docfx='mono $HOME/bin/docfx/docfx.exe'
  alias docfx-serve='mono $HOME/bin/docfx/docfx.exe --serve'
  ```

* W powłoce poleceń przejdź do folderu, który zawiera *docfx.jsw* pliku (*ASPNET* for ASP.net content lub *aspnetcore* for ASP.NET Core Content) i uruchom następujące polecenie, aby skompilować i udostępnić dokumenty za pośrednictwem aliasu:

  ```console
  docfx-serve
  ```

* W przeglądarce przejdź do `http://localhost:8080/group1-dest/` .

## <a name="voice-and-tone"></a>Głos i dźwięk

Naszym celem jest pisanie dokumentacji, która jest łatwa do zrozumienia przez szerszego grona odbiorców. W tym celu wprowadziliśmy wskazówki dotyczące pisania stylu, który poprosił naszych współautorów. Aby uzyskać więcej informacji, zobacz [wytyczne dotyczące głosu i tonu](https://github.com/dotnet/docs/blob/main/styleguide/voice-tone.md) w repozytorium .NET.

## <a name="microsoft-writing-style-guide"></a>Przewodnik redakcyjno-stylistyczny firmy Microsoft

[Przewodnik po stylu pisania firmy Microsoft](https://docs.microsoft.com/style-guide/welcome/) zawiera styl pisania i wskazówki dotyczące terminologii dotyczącej wszystkich form komunikacji technologicznej, w tym dokumentacji ASP.NET Core.

## <a name="redirects"></a>Przekierowuje

W przypadku usunięcia artykułu zmień jego nazwę lub przenieś go do innego folderu, a następnie utwórz przekierowanie, aby osoby, które zakładkęą ten artykuł, nie otrzymają błędu *404* . Dodaj przekierowania do [głównego pliku przekierowania](https://github.com/dotnet/AspNetCore.Docs/blob/main/.openpublishing.redirection.json).
