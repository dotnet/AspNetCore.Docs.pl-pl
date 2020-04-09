# <a name="contribute-to-the-aspnet-core-documentation"></a>Współtworzenie dokumentacji ASP.NET Core

Ten dokument obejmuje proces współtworzenia artykułów i przykładów kodu, które są hostowane w [witrynie dokumentacji ASP.NET](https://docs.microsoft.com/aspnet/). Poprawki typo i nowe artykuły są mile widziane odpowiedzi.

## <a name="how-to-make-a-simple-correction-or-suggestion"></a>Jak dokonać prostej korekty lub sugestii

Artykuły są przechowywane w repozytorium jako pliki Markdown. Proste zmiany w zawartości pliku Markdown są wprowadzane w przeglądarce, wybierając **łącze Edytuj** w prawym górnym rogu okna przeglądarki. (W wąskim oknie przeglądarki rozwiń pasek **opcji,** aby wyświetlić łącze **Edytuj).** Postępuj zgodnie ze wskazówkami, aby utworzyć żądanie ściągnięcia (PR). Przeanalizujemy PR i zaakceptujemy go lub zaproponujemy zmiany.

## <a name="how-to-make-a-more-complex-submission"></a>Jak złożyć bardziej złożone zgłoszenie

Potrzebujesz podstawowej wiedzy o [Git i GitHub.com](https://guides.github.com/activities/hello-world/).

* Otwórz [problem](https://github.com/dotnet/AspNetCore.Docs/issues/new) opisujący, co chcesz zrobić, na przykład zmieniając istniejący artykuł lub tworząc nowy. Często prosimy o zarys nowej sugestii tematu. Poczekaj na zatwierdzenie przez zespół, zanim zainwestujesz dużo czasu.
* Rozwidlić repozytorium [aspnet/Docs](https://github.com/dotnet/AspNetCore.Docs/) i utworzyć gałąź dla zmian.
* Prześlij pr do wzorca ze zmianami.
* Jeśli twój pr ma przypisaną etykietę "cla-required", [wypełnij umowę licencyjną na wkład (CLA).](https://cla.dotnetfoundation.org/)
* Odpowiadaj na opinie PR.

Na przykład, gdy ten proces doprowadził do publikacji nowego artykułu, zobacz [Problem &num;67](https://github.com/dotnet/docs/issues/67) i [Żądanie &num;ściągnięcia 798](https://github.com/dotnet/docs/pull/798) w repozytorium .NET Docs. Nowy artykuł jest [Dokumentowanie kodu](https://docs.microsoft.com/dotnet/articles/csharp/codedoc).

## <a name="docs-authoring-pack-extension-in-visual-studio-code"></a>Rozszerzenie pakietu tworzenia dokumentów w programie Visual Studio Code

Jeśli używasz programu Visual Studio Code, aby przyczynić się do dokumentacji ASP.NET, można zwiększyć produktywność, instalując rozszerzenie [Pakietu tworzenia dokumentów.](https://marketplace.visualstudio.com/items?itemName=docsmsft.docs-authoring-pack) Rozszerzenie zawiera wiele narzędzi, które pomaga w linting Markdown, sprawdzanie pisowni kodu i szablony artykułów.

## <a name="markdown-syntax"></a>Składnia znaczników

Artykuły są napisane w [DocFx-flavored Markdown](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html), który jest nadzbiorem [GitHub-flavored Markdown (GFM)](https://guides.github.com/features/mastering-markdown/). Przykłady składni DFM dla funkcji interfejsu użytkownika często używanych w dokumentacji ASP.NET, zobacz [Metadane i Szablon znaczników](https://github.com/dotnet/docs/blob/master/styleguide/template.md) w przewodniku po stylu repozytorium .NET Docs. 

## <a name="folder-structure-conventions"></a>Konwencje struktury folderów

Dla każdego pliku Markdown może istnieć folder obrazów i folder dla przykładowego kodu. Jeśli artykuł jest [podstawy /configuration/index.md](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/fundamentals/configuration/index.md), obrazy są w [podstawy\_/ konfiguracja / indeks / statyczne](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/_static) i przykładowe pliki projektu aplikacji są w [podstawy / konfiguracja / indeks / próbka](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/sample). Obraz w pliku *fundamentals/configuration/index.md* jest renderowany przez następującą markdownę:

```md
![description of image for alt attribute](configuration/index/_static/imagename.png)
```

Wszystkie obrazy powinny mieć [tekst alternatywny (alt).](https://wikipedia.org/wiki/Alt_attribute) Aby uzyskać porady dotyczące określania tekstu alternatywnego, zobacz zasoby online, takie jak [WebAIM: Tekst alternatywny](https://webaim.org/techniques/alttext/).

Użyj małych liter dla nazw plików markdown i nazw plików obrazów.

## <a name="internal-links"></a>Linki wewnętrzne

Linki wewnętrzne powinny `uid` używać artykułu docelowego z łączem odnośnika zewnętrznego (tekst łącza jest ustawiony na tytuł połączonej zawartości):

```md
<xref:uid_of_the_topic>
```

Jeśli tytuł artykułu nie nadaje się do tekstu łącza (na przykład wyraz lub fraza w zdaniu jest tekstem łącza), określ łącze odnośnika i tekst łącza z następującymi:

```md
[link text](xref:uid_of_the_topic)
```

Aby uzyskać więcej informacji, zobacz [docFx odsyłacz](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#cross-reference).

## <a name="images-and-screenshots"></a>Obrazy i zrzuty ekranu

Nie dołączaj obrazów z artykułami, z wyjątkiem:

* W podstawowych onboarding (początkujący) tutoriale.
* Gdy obraz jest potrzebny do uzyskania przejrzystości.

Te ograniczenia zmniejszają rozmiar repozytorium.

Opcjonalnie upewnij się, że wszystkie obrazy i zrzuty ekranu używane w dokumentacji są kompresowane, co pomaga w wydajności ładowania pliku i rozmiaru strony. Kilka popularnych narzędzi to TinyPNG (za pomocą [strony tinypng](https://tinypng.com/) lub [TinyPNG API)](https://tinypng.com/developers)lub [rozszerzenie Image Optimizer](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.ImageOptimizer) Visual Studio. 

## <a name="code-snippets"></a>Fragmenty kodu

Artykuły często zawierają fragmenty kodu, aby zilustrować punkty. DFM umożliwia kopiowanie kodu do pliku Markdown lub odwoływanie się do oddzielnego pliku kodu. Wolimy używać oddzielnych plików kodu, gdy tylko jest to możliwe, aby zminimalizować ryzyko błędów w kodzie. Pliki kodu są przechowywane w repozytorium przy użyciu struktury folderów opisanej wcześniej dla przykładowych projektów. 

Poniższe przykłady ilustrują [składnię fragmentu kodu DFM](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#code-snippet) do użycia w pliku *configuration/index.md.*

Aby renderować cały plik kodu jako fragment kodu:

```md
[!code-csharp[](configuration/index/sample/Program.cs)]
```

Aby renderować część pliku jako fragment kodu przy użyciu numerów wierszy:

```md
[!code-csharp[](configuration/index/sample/Program.cs?range=1-10,20,30,40-50]
[!code-html[](configuration/index/sample/Views/Home/Index.cshtml?range=1-10,20,30,40-50]
```

W przypadku fragmentów kodu języka C# odwołaj się do [regionu C#](https://docs.microsoft.com/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region). Jeśli to możliwe, należy używać regionów, a nie numerów wierszy, ponieważ numery wierszy w pliku kodu mają tendencję do zmiany i stają się niezsynchronizowane z odwołaniami do numerów wierszy w Markdown. Regiony C# mogą być zagnieżdżone. Jeśli odwołuje się do regionu zewnętrznego, wewnętrzne `#region` i `#endregion` dyrektywy nie są renderowane w urywek. 

Aby renderować region C# o nazwie "snippet_Example":

```md
[!code-csharp[](configuration/index/sample/Program.cs?name=snippet_Example)]
```

Aby wyróżnić zaznaczone linie w renderowanym urywku (zwykle renderowane jako żółty kolor tła):

```md
[!code-csharp[](configuration/index/sample/Program.cs?name=snippet_Example&highlight=1-3,10,20-25)]
[!code-csharp[](configuration/index/sample/Program.cs?range=10-20&highlight=1-3]
[!code-html[](configuration/index/sample/Views/Home/Index.cshtml?range=10-20&highlight=1-3]
[!code-javascript[](configuration/index/sample/UsingOptionsSample.csproj?range=10-20&highlight=1-3]
```

## <a name="test-changes-with-docfx"></a>Zmiany testu z DocFX

Przetestuj zmiany za pomocą [narzędzia wiersza polecenia DocFX,](https://dotnet.github.io/docfx/tutorial/docfx_getting_started.html#2-use-docfx-as-a-command-line-tool)które tworzy lokalnie hostowane wersje witryny. DocFX nie renderuje stylu i rozszerzeń lokacji utworzonych dla docs.microsoft.com.

DocFX wymaga:

* Program .NET Framework w systemie Windows.
* Mono dla linuksa lub systemu macOS. 

### <a name="windows-instructions"></a>Instrukcje dotyczące systemu Windows

* Pobierz i rozpatrzyj *docfx.zip* z [wydań DocFX](https://github.com/dotnet/docfx/releases).
* Dodaj docFx do ścieżki.
* W powłoce poleceń przejdź do folderu zawierającego plik *docfx.json* *(aspnet* dla ASP.NET zawartości lub *aspnetcore* dla ASP.NET podstawowej zawartości) i uruchom następujące polecenie:

  ```console
  docfx --serve
  ```

* W przeglądarce przejdź `http://localhost:8080/group1-dest/`do pliku .

### <a name="mono-instructions"></a>Instrukcje mono

* Zainstaluj Mono przez Homebrew:

  ```console
  brew install mono
  ```

* Pobierz [najnowszą wersję DocFX](https://github.com/dotnet/docfx/releases).
* Wyodrębnij archiwum, aby *$HOME/bin/docfx*.
* Utwórz parę aliasów dla **docfx** w powłoce bash. Pierwszy alias jest używany do tworzenia dokumentacji. Drugi alias jest używany do tworzenia i obsługi dokumentacji.

  ```console
  alias docfx='mono $HOME/bin/docfx/docfx.exe'
  alias docfx-serve='mono $HOME/bin/docfx/docfx.exe --serve'
  ```

* W powłoce poleceń przejdź do folderu zawierającego plik *docfx.json* *(aspnet* dla ASP.NET zawartości lub *aspnetcore* dla ASP.NET podstawowej zawartości) i uruchom następujące polecenie, aby zbudować i obsługiwać dokumenty za pośrednictwem aliasu:

  ```console
  docfx-serve
  ```

* W przeglądarce przejdź `http://localhost:8080/group1-dest/`do pliku .

## <a name="voice-and-tone"></a>Głos i ton

Naszym celem jest napisanie dokumentacji, która jest łatwo zrozumiała dla jak najszerszego grona odbiorców. W tym celu ustanowiliśmy wytyczne dotyczące stylu pisania, o które prosimy naszych współpracowników. Aby uzyskać więcej informacji, zobacz [wskazówki dotyczące głosu i tonu](https://github.com/dotnet/docs/blob/master/styleguide/voice-tone.md) w repozytorium .NET.

## <a name="microsoft-writing-style-guide"></a>Przewodnik redakcyjno-stylistyczny firmy Microsoft

[Przewodnik Po stylach pisania firmy Microsoft](https://docs.microsoft.com/style-guide/welcome/) zawiera wskazówki dotyczące stylu pisania i terminologii dla wszystkich form komunikacji technologicznej, w tym dokumentacji ASP.NET Core.

## <a name="redirects"></a>Przekierowuje

Jeśli usuniesz artykuł, zmienisz jego nazwę lub przeniesiesz go do innego folderu, utwórz przekierowanie, aby osoby, które do zakładek utworzyły ten artykuł, nie otrzymały błędu *404 Nie znaleziono.* Dodaj przekierowania do [głównego pliku przekierowania](https://github.com/dotnet/AspNetCore.Docs/blob/master/.openpublishing.redirection.json).
