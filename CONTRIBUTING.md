# <a name="contribute-to-the-aspnet-core-documentation"></a><span data-ttu-id="439fe-101">Współtworzenie dokumentacji ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="439fe-101">Contribute to the ASP.NET Core documentation</span></span>

<span data-ttu-id="439fe-102">Ten dokument obejmuje proces współtworzenia artykułów i przykładów kodu, które są hostowane w [witrynie dokumentacji ASP.NET](https://docs.microsoft.com/aspnet/).</span><span class="sxs-lookup"><span data-stu-id="439fe-102">This document covers the process for contributing to the articles and code samples that are hosted on the [ASP.NET documentation site](https://docs.microsoft.com/aspnet/).</span></span> <span data-ttu-id="439fe-103">Poprawki typo i nowe artykuły są mile widziane odpowiedzi.</span><span class="sxs-lookup"><span data-stu-id="439fe-103">Typo corrections and new articles are welcome contributions.</span></span>

## <a name="how-to-make-a-simple-correction-or-suggestion"></a><span data-ttu-id="439fe-104">Jak dokonać prostej korekty lub sugestii</span><span class="sxs-lookup"><span data-stu-id="439fe-104">How to make a simple correction or suggestion</span></span>

<span data-ttu-id="439fe-105">Artykuły są przechowywane w repozytorium jako pliki Markdown.</span><span class="sxs-lookup"><span data-stu-id="439fe-105">Articles are stored in the repository as Markdown files.</span></span> <span data-ttu-id="439fe-106">Proste zmiany w zawartości pliku Markdown są wprowadzane w przeglądarce, wybierając **łącze Edytuj** w prawym górnym rogu okna przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="439fe-106">Simple changes to the content of a Markdown file are made in the browser by selecting the **Edit** link in the upper-right corner of the browser window.</span></span> <span data-ttu-id="439fe-107">(W wąskim oknie przeglądarki rozwiń pasek **opcji,** aby wyświetlić łącze **Edytuj).** Postępuj zgodnie ze wskazówkami, aby utworzyć żądanie ściągnięcia (PR).</span><span class="sxs-lookup"><span data-stu-id="439fe-107">(In a narrow browser window, expand the **options** bar to see the **Edit** link.) Follow the directions to create a pull request (PR).</span></span> <span data-ttu-id="439fe-108">Przeanalizujemy PR i zaakceptujemy go lub zaproponujemy zmiany.</span><span class="sxs-lookup"><span data-stu-id="439fe-108">We will review the PR and accept it or suggest changes.</span></span>

## <a name="how-to-make-a-more-complex-submission"></a><span data-ttu-id="439fe-109">Jak złożyć bardziej złożone zgłoszenie</span><span class="sxs-lookup"><span data-stu-id="439fe-109">How to make a more complex submission</span></span>

<span data-ttu-id="439fe-110">Potrzebujesz podstawowej wiedzy o [Git i GitHub.com](https://guides.github.com/activities/hello-world/).</span><span class="sxs-lookup"><span data-stu-id="439fe-110">You need a basic understanding of [Git and GitHub.com](https://guides.github.com/activities/hello-world/).</span></span>

* <span data-ttu-id="439fe-111">Otwórz [problem](https://github.com/dotnet/AspNetCore.Docs/issues/new) opisujący, co chcesz zrobić, na przykład zmieniając istniejący artykuł lub tworząc nowy.</span><span class="sxs-lookup"><span data-stu-id="439fe-111">Open an [issue](https://github.com/dotnet/AspNetCore.Docs/issues/new) describing what you want to do, such as changing an existing article or creating a new one.</span></span> <span data-ttu-id="439fe-112">Często prosimy o zarys nowej sugestii tematu.</span><span class="sxs-lookup"><span data-stu-id="439fe-112">We often request an outline for a new topic suggestion.</span></span> <span data-ttu-id="439fe-113">Poczekaj na zatwierdzenie przez zespół, zanim zainwestujesz dużo czasu.</span><span class="sxs-lookup"><span data-stu-id="439fe-113">Wait for approval from the team before you invest much time.</span></span>
* <span data-ttu-id="439fe-114">Rozwidlić repozytorium [aspnet/Docs](https://github.com/dotnet/AspNetCore.Docs/) i utworzyć gałąź dla zmian.</span><span class="sxs-lookup"><span data-stu-id="439fe-114">Fork the [aspnet/Docs](https://github.com/dotnet/AspNetCore.Docs/) repo and create a branch for your changes.</span></span>
* <span data-ttu-id="439fe-115">Prześlij pr do wzorca ze zmianami.</span><span class="sxs-lookup"><span data-stu-id="439fe-115">Submit a PR to master with your changes.</span></span>
* <span data-ttu-id="439fe-116">Jeśli twój pr ma przypisaną etykietę "cla-required", [wypełnij umowę licencyjną na wkład (CLA).](https://cla.dotnetfoundation.org/)</span><span class="sxs-lookup"><span data-stu-id="439fe-116">If your PR has the label 'cla-required' assigned, [complete the Contribution License Agreement (CLA)](https://cla.dotnetfoundation.org/).</span></span>
* <span data-ttu-id="439fe-117">Odpowiadaj na opinie PR.</span><span class="sxs-lookup"><span data-stu-id="439fe-117">Respond to PR feedback.</span></span>

<span data-ttu-id="439fe-118">Na przykład, gdy ten proces doprowadził do publikacji nowego artykułu, zobacz [Problem &num;67](https://github.com/dotnet/docs/issues/67) i [Żądanie &num;ściągnięcia 798](https://github.com/dotnet/docs/pull/798) w repozytorium .NET Docs.</span><span class="sxs-lookup"><span data-stu-id="439fe-118">For an example where this process led to publication of a new article, see [Issue &num;67](https://github.com/dotnet/docs/issues/67) and [Pull Request &num;798](https://github.com/dotnet/docs/pull/798) in the .NET Docs repository.</span></span> <span data-ttu-id="439fe-119">Nowy artykuł jest [Dokumentowanie kodu](https://docs.microsoft.com/dotnet/articles/csharp/codedoc).</span><span class="sxs-lookup"><span data-stu-id="439fe-119">The new article is [Documenting your code](https://docs.microsoft.com/dotnet/articles/csharp/codedoc).</span></span>

## <a name="docs-authoring-pack-extension-in-visual-studio-code"></a><span data-ttu-id="439fe-120">Rozszerzenie pakietu tworzenia dokumentów w programie Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="439fe-120">Docs Authoring Pack extension in Visual Studio Code</span></span>

<span data-ttu-id="439fe-121">Jeśli używasz programu Visual Studio Code, aby przyczynić się do dokumentacji ASP.NET, można zwiększyć produktywność, instalując rozszerzenie [Pakietu tworzenia dokumentów.](https://marketplace.visualstudio.com/items?itemName=docsmsft.docs-authoring-pack)</span><span class="sxs-lookup"><span data-stu-id="439fe-121">If you use Visual Studio Code to contribute to the ASP.NET documentation, you can boost your productivity by installing the [Docs Authoring Pack](https://marketplace.visualstudio.com/items?itemName=docsmsft.docs-authoring-pack) extension.</span></span> <span data-ttu-id="439fe-122">Rozszerzenie zawiera wiele narzędzi, które pomaga w linting Markdown, sprawdzanie pisowni kodu i szablony artykułów.</span><span class="sxs-lookup"><span data-stu-id="439fe-122">The extension provides a variety of tools that helps with Markdown linting, code spell checking, and article templates.</span></span>

## <a name="markdown-syntax"></a><span data-ttu-id="439fe-123">Składnia znaczników</span><span class="sxs-lookup"><span data-stu-id="439fe-123">Markdown syntax</span></span>

<span data-ttu-id="439fe-124">Artykuły są napisane w [DocFx-flavored Markdown](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html), który jest nadzbiorem [GitHub-flavored Markdown (GFM)](https://guides.github.com/features/mastering-markdown/).</span><span class="sxs-lookup"><span data-stu-id="439fe-124">Articles are written in [DocFx-flavored Markdown](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html), which is a superset of [GitHub-flavored Markdown (GFM)](https://guides.github.com/features/mastering-markdown/).</span></span> <span data-ttu-id="439fe-125">Przykłady składni DFM dla funkcji interfejsu użytkownika często używanych w dokumentacji ASP.NET, zobacz [Metadane i Szablon znaczników](https://github.com/dotnet/docs/blob/master/styleguide/template.md) w przewodniku po stylu repozytorium .NET Docs.</span><span class="sxs-lookup"><span data-stu-id="439fe-125">For examples of DFM syntax for UI features commonly used in the ASP.NET documentation, see [Metadata and Markdown Template](https://github.com/dotnet/docs/blob/master/styleguide/template.md) in the .NET Docs repo style guide.</span></span> 

## <a name="folder-structure-conventions"></a><span data-ttu-id="439fe-126">Konwencje struktury folderów</span><span class="sxs-lookup"><span data-stu-id="439fe-126">Folder structure conventions</span></span>

<span data-ttu-id="439fe-127">Dla każdego pliku Markdown może istnieć folder obrazów i folder dla przykładowego kodu.</span><span class="sxs-lookup"><span data-stu-id="439fe-127">For each Markdown file, a folder for images and a folder for sample code may exist.</span></span> <span data-ttu-id="439fe-128">Jeśli artykuł jest [podstawy /configuration/index.md](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/fundamentals/configuration/index.md), obrazy są w [podstawy\_/ konfiguracja / indeks / statyczne](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/_static) i przykładowe pliki projektu aplikacji są w [podstawy / konfiguracja / indeks / próbka](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/sample).</span><span class="sxs-lookup"><span data-stu-id="439fe-128">If the article is [fundamentals/configuration/index.md](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/fundamentals/configuration/index.md), the images are in [fundamentals/configuration/index/\_static](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/_static) and the sample app project files are in [fundamentals/configuration/index/sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/sample).</span></span> <span data-ttu-id="439fe-129">Obraz w pliku *fundamentals/configuration/index.md* jest renderowany przez następującą markdownę:</span><span class="sxs-lookup"><span data-stu-id="439fe-129">An image in the *fundamentals/configuration/index.md* file is rendered by the following Markdown:</span></span>

```md
![description of image for alt attribute](configuration/index/_static/imagename.png)
```

<span data-ttu-id="439fe-130">Wszystkie obrazy powinny mieć [tekst alternatywny (alt).](https://wikipedia.org/wiki/Alt_attribute)</span><span class="sxs-lookup"><span data-stu-id="439fe-130">All images should have [alternative (alt) text](https://wikipedia.org/wiki/Alt_attribute).</span></span> <span data-ttu-id="439fe-131">Aby uzyskać porady dotyczące określania tekstu alternatywnego, zobacz zasoby online, takie jak [WebAIM: Tekst alternatywny](https://webaim.org/techniques/alttext/).</span><span class="sxs-lookup"><span data-stu-id="439fe-131">For advice on specifying alt text, see online resources, such as [WebAIM: Alternative Text](https://webaim.org/techniques/alttext/).</span></span>

<span data-ttu-id="439fe-132">Użyj małych liter dla nazw plików markdown i nazw plików obrazów.</span><span class="sxs-lookup"><span data-stu-id="439fe-132">Use lowercase for Markdown file names and image file names.</span></span>

## <a name="internal-links"></a><span data-ttu-id="439fe-133">Linki wewnętrzne</span><span class="sxs-lookup"><span data-stu-id="439fe-133">Internal links</span></span>

<span data-ttu-id="439fe-134">Linki wewnętrzne powinny `uid` używać artykułu docelowego z łączem odnośnika zewnętrznego (tekst łącza jest ustawiony na tytuł połączonej zawartości):</span><span class="sxs-lookup"><span data-stu-id="439fe-134">Internal links should use the `uid` of the target article with an xref link (link text is set to the linked content's title):</span></span>

```md
<xref:uid_of_the_topic>
```

<span data-ttu-id="439fe-135">Jeśli tytuł artykułu nie nadaje się do tekstu łącza (na przykład wyraz lub fraza w zdaniu jest tekstem łącza), określ łącze odnośnika i tekst łącza z następującymi:</span><span class="sxs-lookup"><span data-stu-id="439fe-135">If the title of the article is unsuitable for link text (for example, a word or phrase in a sentence is the link text), specify the xref link and link text with the following:</span></span>

```md
[link text](xref:uid_of_the_topic)
```

<span data-ttu-id="439fe-136">Aby uzyskać więcej informacji, zobacz [docFx odsyłacz](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#cross-reference).</span><span class="sxs-lookup"><span data-stu-id="439fe-136">For more information, see the [DocFX Cross Reference](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#cross-reference).</span></span>

## <a name="images-and-screenshots"></a><span data-ttu-id="439fe-137">Obrazy i zrzuty ekranu</span><span class="sxs-lookup"><span data-stu-id="439fe-137">Images and screenshots</span></span>

<span data-ttu-id="439fe-138">Nie dołączaj obrazów z artykułami, z wyjątkiem:</span><span class="sxs-lookup"><span data-stu-id="439fe-138">Don't include images with articles, except:</span></span>

* <span data-ttu-id="439fe-139">W podstawowych onboarding (początkujący) tutoriale.</span><span class="sxs-lookup"><span data-stu-id="439fe-139">In basic onboarding (beginner) tutorials.</span></span>
* <span data-ttu-id="439fe-140">Gdy obraz jest potrzebny do uzyskania przejrzystości.</span><span class="sxs-lookup"><span data-stu-id="439fe-140">When an image is needed for clarity.</span></span>

<span data-ttu-id="439fe-141">Te ograniczenia zmniejszają rozmiar repozytorium.</span><span class="sxs-lookup"><span data-stu-id="439fe-141">These restrictions reduce the size of the repository.</span></span>

<span data-ttu-id="439fe-142">Opcjonalnie upewnij się, że wszystkie obrazy i zrzuty ekranu używane w dokumentacji są kompresowane, co pomaga w wydajności ładowania pliku i rozmiaru strony.</span><span class="sxs-lookup"><span data-stu-id="439fe-142">As an optional step, ensure that any images and screenshots used in the documentation are compressed, which helps with file size and page load performance.</span></span> <span data-ttu-id="439fe-143">Kilka popularnych narzędzi to TinyPNG (za pomocą [strony tinypng](https://tinypng.com/) lub [TinyPNG API)](https://tinypng.com/developers)lub [rozszerzenie Image Optimizer](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.ImageOptimizer) Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="439fe-143">A few popular tools include TinyPNG (using the [TinyPNG website](https://tinypng.com/) or the [TinyPNG API](https://tinypng.com/developers)) or the [Image Optimizer](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.ImageOptimizer) Visual Studio extension.</span></span> 

## <a name="code-snippets"></a><span data-ttu-id="439fe-144">Fragmenty kodu</span><span class="sxs-lookup"><span data-stu-id="439fe-144">Code snippets</span></span>

<span data-ttu-id="439fe-145">Artykuły często zawierają fragmenty kodu, aby zilustrować punkty.</span><span class="sxs-lookup"><span data-stu-id="439fe-145">Articles frequently contain code snippets to illustrate points.</span></span> <span data-ttu-id="439fe-146">DFM umożliwia kopiowanie kodu do pliku Markdown lub odwoływanie się do oddzielnego pliku kodu.</span><span class="sxs-lookup"><span data-stu-id="439fe-146">DFM allows you to copy code into the Markdown file or refer to a separate code file.</span></span> <span data-ttu-id="439fe-147">Wolimy używać oddzielnych plików kodu, gdy tylko jest to możliwe, aby zminimalizować ryzyko błędów w kodzie.</span><span class="sxs-lookup"><span data-stu-id="439fe-147">We prefer to use separate code files whenever possible to minimize the chance of errors in the code.</span></span> <span data-ttu-id="439fe-148">Pliki kodu są przechowywane w repozytorium przy użyciu struktury folderów opisanej wcześniej dla przykładowych projektów.</span><span class="sxs-lookup"><span data-stu-id="439fe-148">The code files are stored in the repo using the folder structure described earlier for sample projects.</span></span> 

<span data-ttu-id="439fe-149">Poniższe przykłady ilustrują [składnię fragmentu kodu DFM](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#code-snippet) do użycia w pliku *configuration/index.md.*</span><span class="sxs-lookup"><span data-stu-id="439fe-149">The following examples illustrate [DFM code snippet syntax](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#code-snippet) for use in a *configuration/index.md* file.</span></span>

<span data-ttu-id="439fe-150">Aby renderować cały plik kodu jako fragment kodu:</span><span class="sxs-lookup"><span data-stu-id="439fe-150">To render an entire code file as a snippet:</span></span>

```md
[!code-csharp[](configuration/index/sample/Program.cs)]
```

<span data-ttu-id="439fe-151">Aby renderować część pliku jako fragment kodu przy użyciu numerów wierszy:</span><span class="sxs-lookup"><span data-stu-id="439fe-151">To render a portion of a file as a snippet by using line numbers:</span></span>

```md
[!code-csharp[](configuration/index/sample/Program.cs?range=1-10,20,30,40-50]
[!code-html[](configuration/index/sample/Views/Home/Index.cshtml?range=1-10,20,30,40-50]
```

<span data-ttu-id="439fe-152">W przypadku fragmentów kodu języka C# odwołaj się do [regionu C#](https://docs.microsoft.com/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region).</span><span class="sxs-lookup"><span data-stu-id="439fe-152">For C# snippets, reference a [C# region](https://docs.microsoft.com/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region).</span></span> <span data-ttu-id="439fe-153">Jeśli to możliwe, należy używać regionów, a nie numerów wierszy, ponieważ numery wierszy w pliku kodu mają tendencję do zmiany i stają się niezsynchronizowane z odwołaniami do numerów wierszy w Markdown.</span><span class="sxs-lookup"><span data-stu-id="439fe-153">Whenever possible, use regions rather than line numbers because line numbers in a code file tend to change and become out of sync with line number references in Markdown.</span></span> <span data-ttu-id="439fe-154">Regiony C# mogą być zagnieżdżone.</span><span class="sxs-lookup"><span data-stu-id="439fe-154">C# regions can be nested.</span></span> <span data-ttu-id="439fe-155">Jeśli odwołuje się do regionu zewnętrznego, wewnętrzne `#region` i `#endregion` dyrektywy nie są renderowane w urywek.</span><span class="sxs-lookup"><span data-stu-id="439fe-155">If referencing the outer region, the inner `#region` and `#endregion` directives aren't rendered in a snippet.</span></span> 

<span data-ttu-id="439fe-156">Aby renderować region C# o nazwie "snippet_Example":</span><span class="sxs-lookup"><span data-stu-id="439fe-156">To render a C# region named "snippet_Example":</span></span>

```md
[!code-csharp[](configuration/index/sample/Program.cs?name=snippet_Example)]
```

<span data-ttu-id="439fe-157">Aby wyróżnić zaznaczone linie w renderowanym urywku (zwykle renderowane jako żółty kolor tła):</span><span class="sxs-lookup"><span data-stu-id="439fe-157">To highlight selected lines in a rendered snippet (usually renders as yellow background color):</span></span>

```md
[!code-csharp[](configuration/index/sample/Program.cs?name=snippet_Example&highlight=1-3,10,20-25)]
[!code-csharp[](configuration/index/sample/Program.cs?range=10-20&highlight=1-3]
[!code-html[](configuration/index/sample/Views/Home/Index.cshtml?range=10-20&highlight=1-3]
[!code-javascript[](configuration/index/sample/UsingOptionsSample.csproj?range=10-20&highlight=1-3]
```

## <a name="test-changes-with-docfx"></a><span data-ttu-id="439fe-158">Zmiany testu z DocFX</span><span class="sxs-lookup"><span data-stu-id="439fe-158">Test changes with DocFX</span></span>

<span data-ttu-id="439fe-159">Przetestuj zmiany za pomocą [narzędzia wiersza polecenia DocFX,](https://dotnet.github.io/docfx/tutorial/docfx_getting_started.html#2-use-docfx-as-a-command-line-tool)które tworzy lokalnie hostowane wersje witryny.</span><span class="sxs-lookup"><span data-stu-id="439fe-159">Test your changes with the [DocFX command-line tool](https://dotnet.github.io/docfx/tutorial/docfx_getting_started.html#2-use-docfx-as-a-command-line-tool), which creates a locally hosted version of the site.</span></span> <span data-ttu-id="439fe-160">DocFX nie renderuje stylu i rozszerzeń lokacji utworzonych dla docs.microsoft.com.</span><span class="sxs-lookup"><span data-stu-id="439fe-160">DocFX doesn't render style and site extensions created for docs.microsoft.com.</span></span>

<span data-ttu-id="439fe-161">DocFX wymaga:</span><span class="sxs-lookup"><span data-stu-id="439fe-161">DocFX requires:</span></span>

* <span data-ttu-id="439fe-162">Program .NET Framework w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="439fe-162">.NET Framework on Windows.</span></span>
* <span data-ttu-id="439fe-163">Mono dla linuksa lub systemu macOS.</span><span class="sxs-lookup"><span data-stu-id="439fe-163">Mono for Linux or macOS.</span></span> 

### <a name="windows-instructions"></a><span data-ttu-id="439fe-164">Instrukcje dotyczące systemu Windows</span><span class="sxs-lookup"><span data-stu-id="439fe-164">Windows instructions</span></span>

* <span data-ttu-id="439fe-165">Pobierz i rozpatrzyj *docfx.zip* z [wydań DocFX](https://github.com/dotnet/docfx/releases).</span><span class="sxs-lookup"><span data-stu-id="439fe-165">Download and unzip *docfx.zip* from [DocFX releases](https://github.com/dotnet/docfx/releases).</span></span>
* <span data-ttu-id="439fe-166">Dodaj docFx do ścieżki.</span><span class="sxs-lookup"><span data-stu-id="439fe-166">Add DocFX to your PATH.</span></span>
* <span data-ttu-id="439fe-167">W powłoce poleceń przejdź do folderu zawierającego plik *docfx.json* *(aspnet* dla ASP.NET zawartości lub *aspnetcore* dla ASP.NET podstawowej zawartości) i uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="439fe-167">In a command shell, navigate to the folder that contains the *docfx.json* file (*aspnet* for ASP.NET content or *aspnetcore* for ASP.NET Core content) and run the following command:</span></span>

  ```console
  docfx --serve
  ```

* <span data-ttu-id="439fe-168">W przeglądarce przejdź `http://localhost:8080/group1-dest/`do pliku .</span><span class="sxs-lookup"><span data-stu-id="439fe-168">In a browser, navigate to `http://localhost:8080/group1-dest/`.</span></span>

### <a name="mono-instructions"></a><span data-ttu-id="439fe-169">Instrukcje mono</span><span class="sxs-lookup"><span data-stu-id="439fe-169">Mono instructions</span></span>

* <span data-ttu-id="439fe-170">Zainstaluj Mono przez Homebrew:</span><span class="sxs-lookup"><span data-stu-id="439fe-170">Install Mono via Homebrew:</span></span>

  ```console
  brew install mono
  ```

* <span data-ttu-id="439fe-171">Pobierz [najnowszą wersję DocFX](https://github.com/dotnet/docfx/releases).</span><span class="sxs-lookup"><span data-stu-id="439fe-171">Download the [latest version of DocFX](https://github.com/dotnet/docfx/releases).</span></span>
* <span data-ttu-id="439fe-172">Wyodrębnij archiwum, aby *$HOME/bin/docfx*.</span><span class="sxs-lookup"><span data-stu-id="439fe-172">Extract the archive to *$HOME/bin/docfx*.</span></span>
* <span data-ttu-id="439fe-173">Utwórz parę aliasów dla **docfx** w powłoce bash.</span><span class="sxs-lookup"><span data-stu-id="439fe-173">Create a pair of aliases for **docfx** in a bash shell.</span></span> <span data-ttu-id="439fe-174">Pierwszy alias jest używany do tworzenia dokumentacji.</span><span class="sxs-lookup"><span data-stu-id="439fe-174">The first alias is used to build the documentation.</span></span> <span data-ttu-id="439fe-175">Drugi alias jest używany do tworzenia i obsługi dokumentacji.</span><span class="sxs-lookup"><span data-stu-id="439fe-175">The second alias is used to build and serve the documentation.</span></span>

  ```console
  alias docfx='mono $HOME/bin/docfx/docfx.exe'
  alias docfx-serve='mono $HOME/bin/docfx/docfx.exe --serve'
  ```

* <span data-ttu-id="439fe-176">W powłoce poleceń przejdź do folderu zawierającego plik *docfx.json* *(aspnet* dla ASP.NET zawartości lub *aspnetcore* dla ASP.NET podstawowej zawartości) i uruchom następujące polecenie, aby zbudować i obsługiwać dokumenty za pośrednictwem aliasu:</span><span class="sxs-lookup"><span data-stu-id="439fe-176">In a command shell, navigate to the folder that contains the *docfx.json* file (*aspnet* for ASP.NET content or *aspnetcore* for ASP.NET Core content) and run the following command to build and serve the docs via its alias:</span></span>

  ```console
  docfx-serve
  ```

* <span data-ttu-id="439fe-177">W przeglądarce przejdź `http://localhost:8080/group1-dest/`do pliku .</span><span class="sxs-lookup"><span data-stu-id="439fe-177">In a browser, navigate to `http://localhost:8080/group1-dest/`.</span></span>

## <a name="voice-and-tone"></a><span data-ttu-id="439fe-178">Głos i ton</span><span class="sxs-lookup"><span data-stu-id="439fe-178">Voice and tone</span></span>

<span data-ttu-id="439fe-179">Naszym celem jest napisanie dokumentacji, która jest łatwo zrozumiała dla jak najszerszego grona odbiorców.</span><span class="sxs-lookup"><span data-stu-id="439fe-179">Our goal is to write documentation that is easily understandable by the widest possible audience.</span></span> <span data-ttu-id="439fe-180">W tym celu ustanowiliśmy wytyczne dotyczące stylu pisania, o które prosimy naszych współpracowników.</span><span class="sxs-lookup"><span data-stu-id="439fe-180">To that end, we established guidelines for writing style that we ask our contributors to follow.</span></span> <span data-ttu-id="439fe-181">Aby uzyskać więcej informacji, zobacz [wskazówki dotyczące głosu i tonu](https://github.com/dotnet/docs/blob/master/styleguide/voice-tone.md) w repozytorium .NET.</span><span class="sxs-lookup"><span data-stu-id="439fe-181">For more information, see [Voice and tone guidelines](https://github.com/dotnet/docs/blob/master/styleguide/voice-tone.md) in the .NET repo.</span></span>

## <a name="microsoft-writing-style-guide"></a><span data-ttu-id="439fe-182">Przewodnik redakcyjno-stylistyczny firmy Microsoft</span><span class="sxs-lookup"><span data-stu-id="439fe-182">Microsoft Writing Style Guide</span></span>

<span data-ttu-id="439fe-183">[Przewodnik Po stylach pisania firmy Microsoft](https://docs.microsoft.com/style-guide/welcome/) zawiera wskazówki dotyczące stylu pisania i terminologii dla wszystkich form komunikacji technologicznej, w tym dokumentacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="439fe-183">The [Microsoft Writing Style Guide](https://docs.microsoft.com/style-guide/welcome/) provides writing style and terminology guidance for all forms of technology communication, including the ASP.NET Core documentation.</span></span>

## <a name="redirects"></a><span data-ttu-id="439fe-184">Przekierowuje</span><span class="sxs-lookup"><span data-stu-id="439fe-184">Redirects</span></span>

<span data-ttu-id="439fe-185">Jeśli usuniesz artykuł, zmienisz jego nazwę lub przeniesiesz go do innego folderu, utwórz przekierowanie, aby osoby, które do zakładek utworzyły ten artykuł, nie otrzymały błędu *404 Nie znaleziono.*</span><span class="sxs-lookup"><span data-stu-id="439fe-185">If you delete an article, change its file name, or move it to a different folder, create a redirect so that people who bookmarked the article don't receive a *404 Not Found* error.</span></span> <span data-ttu-id="439fe-186">Dodaj przekierowania do [głównego pliku przekierowania](https://github.com/dotnet/AspNetCore.Docs/blob/master/.openpublishing.redirection.json).</span><span class="sxs-lookup"><span data-stu-id="439fe-186">Add redirects to the [master redirect file](https://github.com/dotnet/AspNetCore.Docs/blob/master/.openpublishing.redirection.json).</span></span>
