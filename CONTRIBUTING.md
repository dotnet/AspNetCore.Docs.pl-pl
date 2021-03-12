# <a name="contribute-to-the-aspnet-core-documentation"></a><span data-ttu-id="cdf0b-101">Współtworzenie dokumentacji ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cdf0b-101">Contribute to the ASP.NET Core documentation</span></span>

<span data-ttu-id="cdf0b-102">W tym dokumencie omówiono proces współtworzenia artykułów i przykładów kodu, które są hostowane w [witrynie dokumentacji ASP.NET](https://docs.microsoft.com/aspnet/).</span><span class="sxs-lookup"><span data-stu-id="cdf0b-102">This document covers the process for contributing to the articles and code samples that are hosted on the [ASP.NET documentation site](https://docs.microsoft.com/aspnet/).</span></span> <span data-ttu-id="cdf0b-103">Poprawki błędów i nowe artykuły są wkładami powitalnymi.</span><span class="sxs-lookup"><span data-stu-id="cdf0b-103">Typo corrections and new articles are welcome contributions.</span></span>

## <a name="how-to-make-a-simple-correction-or-suggestion"></a><span data-ttu-id="cdf0b-104">Jak wykonać prostą korektę lub sugestię</span><span class="sxs-lookup"><span data-stu-id="cdf0b-104">How to make a simple correction or suggestion</span></span>

<span data-ttu-id="cdf0b-105">Artykuły są przechowywane w repozytorium jako pliki o promocji.</span><span class="sxs-lookup"><span data-stu-id="cdf0b-105">Articles are stored in the repository as Markdown files.</span></span> <span data-ttu-id="cdf0b-106">Proste zmiany zawartości pliku promocji są wprowadzane w przeglądarce, wybierając link **Edytuj** w prawym górnym rogu okna przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="cdf0b-106">Simple changes to the content of a Markdown file are made in the browser by selecting the **Edit** link in the upper-right corner of the browser window.</span></span> <span data-ttu-id="cdf0b-107">(W wąskim oknie przeglądarki Rozwiń pasek **opcji** , aby wyświetlić łącze **Edytuj** ). Postępuj zgodnie z instrukcjami, aby utworzyć żądanie ściągnięcia.</span><span class="sxs-lookup"><span data-stu-id="cdf0b-107">(In a narrow browser window, expand the **options** bar to see the **Edit** link.) Follow the directions to create a pull request (PR).</span></span> <span data-ttu-id="cdf0b-108">Sprawdzimy żądanie ściągnięcia i zaakceptujemy je lub zasugerujemy zmiany.</span><span class="sxs-lookup"><span data-stu-id="cdf0b-108">We will review the PR and accept it or suggest changes.</span></span>

## <a name="how-to-make-a-more-complex-submission"></a><span data-ttu-id="cdf0b-109">Jak utworzyć bardziej złożone przesyłanie</span><span class="sxs-lookup"><span data-stu-id="cdf0b-109">How to make a more complex submission</span></span>

<span data-ttu-id="cdf0b-110">Potrzebna jest podstawowa znajomość usługi [git i GitHub.com](https://guides.github.com/activities/hello-world/).</span><span class="sxs-lookup"><span data-stu-id="cdf0b-110">You need a basic understanding of [Git and GitHub.com](https://guides.github.com/activities/hello-world/).</span></span>

* <span data-ttu-id="cdf0b-111">Otwórz [problem](https://github.com/dotnet/AspNetCore.Docs/issues/new) z opisem tego, co chcesz zrobić, na przykład zmieniając istniejący artykuł lub Utwórz nowy.</span><span class="sxs-lookup"><span data-stu-id="cdf0b-111">Open an [issue](https://github.com/dotnet/AspNetCore.Docs/issues/new) describing what you want to do, such as changing an existing article or creating a new one.</span></span> <span data-ttu-id="cdf0b-112">Często żądamy konspektu dla nowej sugestii tematu.</span><span class="sxs-lookup"><span data-stu-id="cdf0b-112">We often request an outline for a new topic suggestion.</span></span> <span data-ttu-id="cdf0b-113">Zaczekaj na zatwierdzenie przez zespół przed zainwestowaniem dużo czasu.</span><span class="sxs-lookup"><span data-stu-id="cdf0b-113">Wait for approval from the team before you invest much time.</span></span>
* <span data-ttu-id="cdf0b-114">Rozwidlenie repozytorium [ASPNET/docs](https://github.com/dotnet/AspNetCore.Docs/) i utworzenie gałęzi dla zmian.</span><span class="sxs-lookup"><span data-stu-id="cdf0b-114">Fork the [aspnet/Docs](https://github.com/dotnet/AspNetCore.Docs/) repo and create a branch for your changes.</span></span>
* <span data-ttu-id="cdf0b-115">Prześlij żądanie ściągnięcia do *głównej* gałęzi ze zmianami.</span><span class="sxs-lookup"><span data-stu-id="cdf0b-115">Submit a PR to the *main* branch with your changes.</span></span>
* <span data-ttu-id="cdf0b-116">Jeśli do żądania ściągnięcia jest przypisana etykieta "CLA-Required", [Wypełnij umowę licencyjną dotyczącą udziału (CLA)](https://cla.dotnetfoundation.org/).</span><span class="sxs-lookup"><span data-stu-id="cdf0b-116">If your PR has the label 'cla-required' assigned, [complete the Contribution License Agreement (CLA)](https://cla.dotnetfoundation.org/).</span></span>
* <span data-ttu-id="cdf0b-117">Odpowiedź na opinię żądania ściągnięcia.</span><span class="sxs-lookup"><span data-stu-id="cdf0b-117">Respond to PR feedback.</span></span>

<span data-ttu-id="cdf0b-118">Przykład, w którym ten proces przeprowadził publikację nowego artykułu, zobacz artykuł [dotyczący problemu 1477](https://github.com/dotnet/docs/issues/1477) i [żądania ściągnięcia 18955](https://github.com/dotnet/docs/pull/18955) w repozytorium dokumentacji platformy .NET.</span><span class="sxs-lookup"><span data-stu-id="cdf0b-118">For an example where this process led to publication of a new article, see [Issue 1477](https://github.com/dotnet/docs/issues/1477) and [Pull Request 18955](https://github.com/dotnet/docs/pull/18955) in the .NET Docs repository.</span></span> <span data-ttu-id="cdf0b-119">W nowym artykule jest [używane pokrycie kodu dla testów jednostkowych](/dotnet/core/testing/unit-testing-code-coverage).</span><span class="sxs-lookup"><span data-stu-id="cdf0b-119">The new article is [Use code coverage for unit testing](/dotnet/core/testing/unit-testing-code-coverage).</span></span>

## <a name="docs-authoring-pack-extension-in-visual-studio-code"></a><span data-ttu-id="cdf0b-120">Rozszerzenie docs Authoring Pack w Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="cdf0b-120">Docs Authoring Pack extension in Visual Studio Code</span></span>

<span data-ttu-id="cdf0b-121">Jeśli używasz Visual Studio Code do współtworzenia dokumentacji ASP.NET, możesz zwiększyć produktywność, instalując rozszerzenie [docs Authoring Pack](https://marketplace.visualstudio.com/items?itemName=docsmsft.docs-authoring-pack) .</span><span class="sxs-lookup"><span data-stu-id="cdf0b-121">If you use Visual Studio Code to contribute to the ASP.NET documentation, you can boost your productivity by installing the [Docs Authoring Pack](https://marketplace.visualstudio.com/items?itemName=docsmsft.docs-authoring-pack) extension.</span></span> <span data-ttu-id="cdf0b-122">Rozszerzenie zawiera wiele narzędzi, które ułatwiają Zaznaczanie błędów, sprawdzanie pisowni kodu i szablony artykułów.</span><span class="sxs-lookup"><span data-stu-id="cdf0b-122">The extension provides a variety of tools that helps with Markdown linting, code spell checking, and article templates.</span></span>

## <a name="markdown-syntax"></a><span data-ttu-id="cdf0b-123">Składnia promocji</span><span class="sxs-lookup"><span data-stu-id="cdf0b-123">Markdown syntax</span></span>

<span data-ttu-id="cdf0b-124">Artykuły są zapisywane w [DocFxej promocji](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html), która jest nadzbiorem promocji z [obsługą usługi GitHub (GFM)](https://guides.github.com/features/mastering-markdown/).</span><span class="sxs-lookup"><span data-stu-id="cdf0b-124">Articles are written in [DocFx-flavored Markdown](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html), which is a superset of [GitHub-flavored Markdown (GFM)](https://guides.github.com/features/mastering-markdown/).</span></span> <span data-ttu-id="cdf0b-125">Aby zapoznać się z przykładami składni DFM dla funkcji interfejsu użytkownika często używanych w dokumentacji ASP.NET, zobacz artykuł [Metadata and promocji — szablon](https://github.com/dotnet/docs/blob/main/styleguide/template.md) w przewodniku po stylu repozytorium .NET docs.</span><span class="sxs-lookup"><span data-stu-id="cdf0b-125">For examples of DFM syntax for UI features commonly used in the ASP.NET documentation, see [Metadata and Markdown Template](https://github.com/dotnet/docs/blob/main/styleguide/template.md) in the .NET Docs repo style guide.</span></span> 

## <a name="folder-structure-conventions"></a><span data-ttu-id="cdf0b-126">Konwencje struktury folderów</span><span class="sxs-lookup"><span data-stu-id="cdf0b-126">Folder structure conventions</span></span>

<span data-ttu-id="cdf0b-127">Dla każdego pliku z promocji, może istnieć folder obrazów i folderu dla przykładowego kodu.</span><span class="sxs-lookup"><span data-stu-id="cdf0b-127">For each Markdown file, a folder for images and a folder for sample code may exist.</span></span> <span data-ttu-id="cdf0b-128">Jeśli artykuł ma [podstawowe znaczenie/konfiguracja/index. MD](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/fundamentals/configuration/index.md), obrazy znajdują się w tematach [podstawowe/konfiguracja/indeks/ \_ statyczny](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/index/_static) , a pliki projektu aplikacji przykładowych znajdują się w temacie [podstawy/konfiguracja/indeks/przykład](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/index/sample).</span><span class="sxs-lookup"><span data-stu-id="cdf0b-128">If the article is [fundamentals/configuration/index.md](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/fundamentals/configuration/index.md), the images are in [fundamentals/configuration/index/\_static](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/index/_static) and the sample app project files are in [fundamentals/configuration/index/sample](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/configuration/index/sample).</span></span> <span data-ttu-id="cdf0b-129">Obraz w pliku *Fundamentals/Configuration/index. MD* jest renderowany przy użyciu następującej promocji:</span><span class="sxs-lookup"><span data-stu-id="cdf0b-129">An image in the *fundamentals/configuration/index.md* file is rendered by the following Markdown:</span></span>

```md
![description of image for alt attribute](configuration/index/_static/imagename.png)
```

<span data-ttu-id="cdf0b-130">Wszystkie obrazy powinny mieć [alternatywny tekst (Alt)](https://wikipedia.org/wiki/Alt_attribute).</span><span class="sxs-lookup"><span data-stu-id="cdf0b-130">All images should have [alternative (alt) text](https://wikipedia.org/wiki/Alt_attribute).</span></span> <span data-ttu-id="cdf0b-131">Aby uzyskać poradę dotyczącą określania tekstu alternatywnego, zobacz zasoby online, takie jak [WebAIM: tekst alternatywny](https://webaim.org/techniques/alttext/).</span><span class="sxs-lookup"><span data-stu-id="cdf0b-131">For advice on specifying alt text, see online resources, such as [WebAIM: Alternative Text](https://webaim.org/techniques/alttext/).</span></span>

<span data-ttu-id="cdf0b-132">Używaj małych liter w nazwach plików i plikach obrazu z przeznaczeniem.</span><span class="sxs-lookup"><span data-stu-id="cdf0b-132">Use lowercase for Markdown file names and image file names.</span></span>

## <a name="internal-links"></a><span data-ttu-id="cdf0b-133">Linki wewnętrzne</span><span class="sxs-lookup"><span data-stu-id="cdf0b-133">Internal links</span></span>

<span data-ttu-id="cdf0b-134">Linki wewnętrzne powinny korzystać z `uid` artykułu docelowego z linkiem linki XREF (tekst linku jest ustawiany na tytuł połączonej zawartości):</span><span class="sxs-lookup"><span data-stu-id="cdf0b-134">Internal links should use the `uid` of the target article with an xref link (link text is set to the linked content's title):</span></span>

```md
<xref:uid_of_the_topic>
```

<span data-ttu-id="cdf0b-135">Jeśli tytuł artykułu jest nieodpowiedni dla tekstu łącza (na przykład słowo lub fraza w zdaniu jest tekstem łącza), określ link linki XREF i Połącz tekst z poniższymi:</span><span class="sxs-lookup"><span data-stu-id="cdf0b-135">If the title of the article is unsuitable for link text (for example, a word or phrase in a sentence is the link text), specify the xref link and link text with the following:</span></span>

```md
[link text](xref:uid_of_the_topic)
```

<span data-ttu-id="cdf0b-136">Aby uzyskać więcej informacji, zobacz [DocFX cross reference](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#cross-reference).</span><span class="sxs-lookup"><span data-stu-id="cdf0b-136">For more information, see the [DocFX Cross Reference](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#cross-reference).</span></span>

## <a name="images-and-screenshots"></a><span data-ttu-id="cdf0b-137">Obrazy i zrzuty ekranu</span><span class="sxs-lookup"><span data-stu-id="cdf0b-137">Images and screenshots</span></span>

<span data-ttu-id="cdf0b-138">Nie dołączaj obrazów do artykułów, z wyjątkiem:</span><span class="sxs-lookup"><span data-stu-id="cdf0b-138">Don't include images with articles, except:</span></span>

* <span data-ttu-id="cdf0b-139">W podstawowych samouczkach dołączania (początkujących).</span><span class="sxs-lookup"><span data-stu-id="cdf0b-139">In basic onboarding (beginner) tutorials.</span></span>
* <span data-ttu-id="cdf0b-140">Gdy obraz jest niezbędny do przejrzystości.</span><span class="sxs-lookup"><span data-stu-id="cdf0b-140">When an image is needed for clarity.</span></span>

<span data-ttu-id="cdf0b-141">Ograniczenia te zmniejszają rozmiar repozytorium.</span><span class="sxs-lookup"><span data-stu-id="cdf0b-141">These restrictions reduce the size of the repository.</span></span>

<span data-ttu-id="cdf0b-142">Opcjonalnym krokiem jest upewnienie się, że wszystkie obrazy i zrzuty ekranu używane w dokumentacji są skompresowane, co pomaga w zapewnieniu rozmiaru pliku i wydajności ładowania strony.</span><span class="sxs-lookup"><span data-stu-id="cdf0b-142">As an optional step, ensure that any images and screenshots used in the documentation are compressed, which helps with file size and page load performance.</span></span> <span data-ttu-id="cdf0b-143">Niektóre popularne narzędzia obejmują TinyPNG (przy użyciu [witryny sieci Web TinyPNG](https://tinypng.com/) lub [interfejsu API TinyPNG](https://tinypng.com/developers)) lub rozszerzenie programu Visual Studio dla [Optymalizatora obrazów](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.ImageOptimizer) .</span><span class="sxs-lookup"><span data-stu-id="cdf0b-143">A few popular tools include TinyPNG (using the [TinyPNG website](https://tinypng.com/) or the [TinyPNG API](https://tinypng.com/developers)) or the [Image Optimizer](https://marketplace.visualstudio.com/items?itemName=MadsKristensen.ImageOptimizer) Visual Studio extension.</span></span> 

## <a name="code-snippets"></a><span data-ttu-id="cdf0b-144">Fragmenty kodu</span><span class="sxs-lookup"><span data-stu-id="cdf0b-144">Code snippets</span></span>

<span data-ttu-id="cdf0b-145">Artykuły często zawierają fragmenty kodu przedstawiające punkty.</span><span class="sxs-lookup"><span data-stu-id="cdf0b-145">Articles frequently contain code snippets to illustrate points.</span></span> <span data-ttu-id="cdf0b-146">DFM umożliwia skopiowanie kodu do pliku o promocji lub zapoznaj się z osobnym plikiem kodu.</span><span class="sxs-lookup"><span data-stu-id="cdf0b-146">DFM allows you to copy code into the Markdown file or refer to a separate code file.</span></span> <span data-ttu-id="cdf0b-147">Wolimy używać oddzielnych plików kodu, jeśli jest to możliwe, aby zminimalizować prawdopodobieństwo wystąpienia błędów w kodzie.</span><span class="sxs-lookup"><span data-stu-id="cdf0b-147">We prefer to use separate code files whenever possible to minimize the chance of errors in the code.</span></span> <span data-ttu-id="cdf0b-148">Pliki kodu są przechowywane w repozytorium przy użyciu struktury folderów opisanej wcześniej dla przykładowych projektów.</span><span class="sxs-lookup"><span data-stu-id="cdf0b-148">The code files are stored in the repo using the folder structure described earlier for sample projects.</span></span> 

<span data-ttu-id="cdf0b-149">W poniższych przykładach przedstawiono [składnię fragmentu kodu DFM](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#code-snippet) do użycia w pliku *Configuration/index. MD* .</span><span class="sxs-lookup"><span data-stu-id="cdf0b-149">The following examples illustrate [DFM code snippet syntax](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html#code-snippet) for use in a *configuration/index.md* file.</span></span>

<span data-ttu-id="cdf0b-150">Aby renderować cały plik kodu jako fragment:</span><span class="sxs-lookup"><span data-stu-id="cdf0b-150">To render an entire code file as a snippet:</span></span>

```md
[!code-csharp[](configuration/index/sample/Program.cs)]
```

<span data-ttu-id="cdf0b-151">Aby renderować część pliku jako fragment przy użyciu numerów wierszy:</span><span class="sxs-lookup"><span data-stu-id="cdf0b-151">To render a portion of a file as a snippet by using line numbers:</span></span>

```md
[!code-csharp[](configuration/index/sample/Program.cs?range=1-10,20,30,40-50]
[!code-html[](configuration/index/sample/Views/Home/Index.cshtml?range=1-10,20,30,40-50]
```

<span data-ttu-id="cdf0b-152">W przypadku fragmentów języka C# odwołuje się do [regionu języka c#](https://docs.microsoft.com/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region).</span><span class="sxs-lookup"><span data-stu-id="cdf0b-152">For C# snippets, reference a [C# region](https://docs.microsoft.com/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region).</span></span> <span data-ttu-id="cdf0b-153">Jeśli to możliwe, użyj regionów zamiast numerów wierszy, ponieważ numery wierszy w pliku kodu są zmieniane i nie są zsynchronizowane z odwołaniami do numerów wierszy w ramach promocji.</span><span class="sxs-lookup"><span data-stu-id="cdf0b-153">Whenever possible, use regions rather than line numbers because line numbers in a code file tend to change and become out of sync with line number references in Markdown.</span></span> <span data-ttu-id="cdf0b-154">Regiony języka C# mogą być zagnieżdżane.</span><span class="sxs-lookup"><span data-stu-id="cdf0b-154">C# regions can be nested.</span></span> <span data-ttu-id="cdf0b-155">W przypadku odwoływania się do regionu zewnętrznego wewnętrzne `#region` i `#endregion` dyrektywy nie są renderowane w fragmencie kodu.</span><span class="sxs-lookup"><span data-stu-id="cdf0b-155">If referencing the outer region, the inner `#region` and `#endregion` directives aren't rendered in a snippet.</span></span> 

<span data-ttu-id="cdf0b-156">Aby renderować region języka C# o nazwie "snippet_Example":</span><span class="sxs-lookup"><span data-stu-id="cdf0b-156">To render a C# region named "snippet_Example":</span></span>

```md
[!code-csharp[](configuration/index/sample/Program.cs?name=snippet_Example)]
```

<span data-ttu-id="cdf0b-157">Aby wyróżnić wybrane wiersze w renderowanym fragmencie (zwykle jest to kolor żółtego tła):</span><span class="sxs-lookup"><span data-stu-id="cdf0b-157">To highlight selected lines in a rendered snippet (usually renders as yellow background color):</span></span>

```md
[!code-csharp[](configuration/index/sample/Program.cs?name=snippet_Example&highlight=1-3,10,20-25)]
[!code-csharp[](configuration/index/sample/Program.cs?range=10-20&highlight=1-3]
[!code-html[](configuration/index/sample/Views/Home/Index.cshtml?range=10-20&highlight=1-3]
[!code-javascript[](configuration/index/sample/UsingOptionsSample.csproj?range=10-20&highlight=1-3]
```

## <a name="test-changes-with-docfx"></a><span data-ttu-id="cdf0b-158">Testowanie zmian za pomocą DocFX</span><span class="sxs-lookup"><span data-stu-id="cdf0b-158">Test changes with DocFX</span></span>

<span data-ttu-id="cdf0b-159">Przetestuj zmiany za pomocą [narzędzia wiersza polecenia DocFX](https://dotnet.github.io/docfx/tutorial/docfx_getting_started.html#2-use-docfx-as-a-command-line-tool), które tworzy lokalnie hostowaną wersję lokacji.</span><span class="sxs-lookup"><span data-stu-id="cdf0b-159">Test your changes with the [DocFX command-line tool](https://dotnet.github.io/docfx/tutorial/docfx_getting_started.html#2-use-docfx-as-a-command-line-tool), which creates a locally hosted version of the site.</span></span> <span data-ttu-id="cdf0b-160">DocFX nie renderuje stylu i rozszerzeń witryny utworzonych dla docs.microsoft.com.</span><span class="sxs-lookup"><span data-stu-id="cdf0b-160">DocFX doesn't render style and site extensions created for docs.microsoft.com.</span></span>

<span data-ttu-id="cdf0b-161">DocFX wymaga:</span><span class="sxs-lookup"><span data-stu-id="cdf0b-161">DocFX requires:</span></span>

* <span data-ttu-id="cdf0b-162">.NET Framework w systemie Windows.</span><span class="sxs-lookup"><span data-stu-id="cdf0b-162">.NET Framework on Windows.</span></span>
* <span data-ttu-id="cdf0b-163">Mono dla systemu Linux lub macOS.</span><span class="sxs-lookup"><span data-stu-id="cdf0b-163">Mono for Linux or macOS.</span></span> 

### <a name="windows-instructions"></a><span data-ttu-id="cdf0b-164">Instrukcje dotyczące systemu Windows</span><span class="sxs-lookup"><span data-stu-id="cdf0b-164">Windows instructions</span></span>

* <span data-ttu-id="cdf0b-165">Pobierz i rozpakuj *docfx.zip* z [wersji DocFX](https://github.com/dotnet/docfx/releases).</span><span class="sxs-lookup"><span data-stu-id="cdf0b-165">Download and unzip *docfx.zip* from [DocFX releases](https://github.com/dotnet/docfx/releases).</span></span>
* <span data-ttu-id="cdf0b-166">Dodaj DocFX do ścieżki.</span><span class="sxs-lookup"><span data-stu-id="cdf0b-166">Add DocFX to your PATH.</span></span>
* <span data-ttu-id="cdf0b-167">W powłoce poleceń przejdź do folderu, który zawiera *docfx.jsw* pliku (*ASPNET* for ASP.net content lub *aspnetcore* for ASP.NET Core Content) i uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="cdf0b-167">In a command shell, navigate to the folder that contains the *docfx.json* file (*aspnet* for ASP.NET content or *aspnetcore* for ASP.NET Core content) and run the following command:</span></span>

  ```console
  docfx --serve
  ```

* <span data-ttu-id="cdf0b-168">W przeglądarce przejdź do `http://localhost:8080/group1-dest/` .</span><span class="sxs-lookup"><span data-stu-id="cdf0b-168">In a browser, navigate to `http://localhost:8080/group1-dest/`.</span></span>

### <a name="mono-instructions"></a><span data-ttu-id="cdf0b-169">Instrukcje dotyczące narzędzia mono</span><span class="sxs-lookup"><span data-stu-id="cdf0b-169">Mono instructions</span></span>

* <span data-ttu-id="cdf0b-170">Zainstaluj program mono za pośrednictwem oprogramowania Homebrew:</span><span class="sxs-lookup"><span data-stu-id="cdf0b-170">Install Mono via Homebrew:</span></span>

  ```console
  brew install mono
  ```

* <span data-ttu-id="cdf0b-171">Pobierz [najnowszą wersję programu DocFX](https://github.com/dotnet/docfx/releases).</span><span class="sxs-lookup"><span data-stu-id="cdf0b-171">Download the [latest version of DocFX](https://github.com/dotnet/docfx/releases).</span></span>
* <span data-ttu-id="cdf0b-172">Wyodrębnij archiwum do *$Home/bin/docfx*.</span><span class="sxs-lookup"><span data-stu-id="cdf0b-172">Extract the archive to *$HOME/bin/docfx*.</span></span>
* <span data-ttu-id="cdf0b-173">Utwórz parę aliasów dla **docfx** w bash Shell.</span><span class="sxs-lookup"><span data-stu-id="cdf0b-173">Create a pair of aliases for **docfx** in a bash shell.</span></span> <span data-ttu-id="cdf0b-174">Pierwszy alias jest używany do kompilowania dokumentacji.</span><span class="sxs-lookup"><span data-stu-id="cdf0b-174">The first alias is used to build the documentation.</span></span> <span data-ttu-id="cdf0b-175">Drugi alias jest używany do kompilowania i obsłużenia dokumentacji.</span><span class="sxs-lookup"><span data-stu-id="cdf0b-175">The second alias is used to build and serve the documentation.</span></span>

  ```console
  alias docfx='mono $HOME/bin/docfx/docfx.exe'
  alias docfx-serve='mono $HOME/bin/docfx/docfx.exe --serve'
  ```

* <span data-ttu-id="cdf0b-176">W powłoce poleceń przejdź do folderu, który zawiera *docfx.jsw* pliku (*ASPNET* for ASP.net content lub *aspnetcore* for ASP.NET Core Content) i uruchom następujące polecenie, aby skompilować i udostępnić dokumenty za pośrednictwem aliasu:</span><span class="sxs-lookup"><span data-stu-id="cdf0b-176">In a command shell, navigate to the folder that contains the *docfx.json* file (*aspnet* for ASP.NET content or *aspnetcore* for ASP.NET Core content) and run the following command to build and serve the docs via its alias:</span></span>

  ```console
  docfx-serve
  ```

* <span data-ttu-id="cdf0b-177">W przeglądarce przejdź do `http://localhost:8080/group1-dest/` .</span><span class="sxs-lookup"><span data-stu-id="cdf0b-177">In a browser, navigate to `http://localhost:8080/group1-dest/`.</span></span>

## <a name="voice-and-tone"></a><span data-ttu-id="cdf0b-178">Głos i dźwięk</span><span class="sxs-lookup"><span data-stu-id="cdf0b-178">Voice and tone</span></span>

<span data-ttu-id="cdf0b-179">Naszym celem jest pisanie dokumentacji, która jest łatwa do zrozumienia przez szerszego grona odbiorców.</span><span class="sxs-lookup"><span data-stu-id="cdf0b-179">Our goal is to write documentation that is easily understandable by the widest possible audience.</span></span> <span data-ttu-id="cdf0b-180">W tym celu wprowadziliśmy wskazówki dotyczące pisania stylu, który poprosił naszych współautorów.</span><span class="sxs-lookup"><span data-stu-id="cdf0b-180">To that end, we established guidelines for writing style that we ask our contributors to follow.</span></span> <span data-ttu-id="cdf0b-181">Aby uzyskać więcej informacji, zobacz [wytyczne dotyczące głosu i tonu](https://github.com/dotnet/docs/blob/main/styleguide/voice-tone.md) w repozytorium .NET.</span><span class="sxs-lookup"><span data-stu-id="cdf0b-181">For more information, see [Voice and tone guidelines](https://github.com/dotnet/docs/blob/main/styleguide/voice-tone.md) in the .NET repo.</span></span>

## <a name="microsoft-writing-style-guide"></a><span data-ttu-id="cdf0b-182">Przewodnik redakcyjno-stylistyczny firmy Microsoft</span><span class="sxs-lookup"><span data-stu-id="cdf0b-182">Microsoft Writing Style Guide</span></span>

<span data-ttu-id="cdf0b-183">[Przewodnik po stylu pisania firmy Microsoft](https://docs.microsoft.com/style-guide/welcome/) zawiera styl pisania i wskazówki dotyczące terminologii dotyczącej wszystkich form komunikacji technologicznej, w tym dokumentacji ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cdf0b-183">The [Microsoft Writing Style Guide](https://docs.microsoft.com/style-guide/welcome/) provides writing style and terminology guidance for all forms of technology communication, including the ASP.NET Core documentation.</span></span>

## <a name="redirects"></a><span data-ttu-id="cdf0b-184">Przekierowuje</span><span class="sxs-lookup"><span data-stu-id="cdf0b-184">Redirects</span></span>

<span data-ttu-id="cdf0b-185">W przypadku usunięcia artykułu zmień jego nazwę lub przenieś go do innego folderu, a następnie utwórz przekierowanie, aby osoby, które zakładkęą ten artykuł, nie otrzymają błędu *404* .</span><span class="sxs-lookup"><span data-stu-id="cdf0b-185">If you delete an article, change its file name, or move it to a different folder, create a redirect so that people who bookmarked the article don't receive a *404 Not Found* error.</span></span> <span data-ttu-id="cdf0b-186">Dodaj przekierowania do [głównego pliku przekierowania](https://github.com/dotnet/AspNetCore.Docs/blob/main/.openpublishing.redirection.json).</span><span class="sxs-lookup"><span data-stu-id="cdf0b-186">Add redirects to the [main redirect file](https://github.com/dotnet/AspNetCore.Docs/blob/main/.openpublishing.redirection.json).</span></span>
