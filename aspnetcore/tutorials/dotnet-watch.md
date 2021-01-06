---
title: Opracowywanie aplikacji ASP.NET Core przy użyciu obserwatora plików
author: rick-anderson
description: W tym samouczku pokazano, jak zainstalować i używać narzędzia obserwatora plików interfejs wiersza polecenia platformy .NET Core (czujka dotnet) w aplikacji ASP.NET Core.
ms.author: riande
ms.date: 05/31/2018
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
uid: tutorials/dotnet-watch
ms.openlocfilehash: 27420fe00ba6375e15b67fb359be06df055eff1f
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "93060043"
---
# <a name="develop-aspnet-core-apps-using-a-file-watcher"></a><span data-ttu-id="16789-103">Opracowywanie aplikacji ASP.NET Core przy użyciu obserwatora plików</span><span class="sxs-lookup"><span data-stu-id="16789-103">Develop ASP.NET Core apps using a file watcher</span></span>

<span data-ttu-id="16789-104">Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT) i [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span><span class="sxs-lookup"><span data-stu-id="16789-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span></span>

<span data-ttu-id="16789-105">`dotnet watch` jest narzędziem, które uruchamia [interfejs wiersza polecenia platformy .NET Core](/dotnet/core/tools) polecenie, gdy pliki źródłowe zmieniają się.</span><span class="sxs-lookup"><span data-stu-id="16789-105">`dotnet watch` is a tool that runs a [.NET Core CLI](/dotnet/core/tools) command when source files change.</span></span> <span data-ttu-id="16789-106">Na przykład zmiana pliku może wyzwolić kompilację, wykonanie testu lub wdrożenie.</span><span class="sxs-lookup"><span data-stu-id="16789-106">For example, a file change can trigger compilation, test execution, or deployment.</span></span>

<span data-ttu-id="16789-107">W tym samouczku jest wykorzystywany istniejący internetowy interfejs API z dwoma punktami końcowymi: jeden, który zwraca sumę i jeden z nich zwraca produkt.</span><span class="sxs-lookup"><span data-stu-id="16789-107">This tutorial uses an existing web API with two endpoints: one that returns a sum and one that returns a product.</span></span> <span data-ttu-id="16789-108">Metoda produktu zawiera usterkę, która została rozwiązana w tym samouczku.</span><span class="sxs-lookup"><span data-stu-id="16789-108">The product method has a bug, which is fixed in this tutorial.</span></span>

<span data-ttu-id="16789-109">Pobierz [przykładową aplikację](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample).</span><span class="sxs-lookup"><span data-stu-id="16789-109">Download the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample).</span></span> <span data-ttu-id="16789-110">Składa się z dwóch projektów: *webapp* (ASP.NET Core Web API) i *WebAppTests* (testy jednostkowe dla internetowego interfejsu API).</span><span class="sxs-lookup"><span data-stu-id="16789-110">It consists of two projects: *WebApp* (an ASP.NET Core web API) and *WebAppTests* (unit tests for the web API).</span></span>

<span data-ttu-id="16789-111">W powłoce poleceń przejdź do folderu *webapp* .</span><span class="sxs-lookup"><span data-stu-id="16789-111">In a command shell, navigate to the *WebApp* folder.</span></span> <span data-ttu-id="16789-112">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="16789-112">Run the following command:</span></span>

```dotnetcli
dotnet run
```

> [!NOTE]
> <span data-ttu-id="16789-113">Możesz użyć, `dotnet run --project <PROJECT>` Aby określić projekt do uruchomienia.</span><span class="sxs-lookup"><span data-stu-id="16789-113">You can use `dotnet run --project <PROJECT>` to specify a project to run.</span></span> <span data-ttu-id="16789-114">Na przykład uruchomienie `dotnet run --project WebApp` z poziomu głównego przykładowej aplikacji spowoduje również uruchomienie projektu *webapp* .</span><span class="sxs-lookup"><span data-stu-id="16789-114">For example, running `dotnet run --project WebApp` from the root of the sample app will also run the *WebApp* project.</span></span>

<span data-ttu-id="16789-115">W danych wyjściowych konsoli są wyświetlane komunikaty podobne do następujących (wskazuje, że aplikacja działa i oczekuje na żądania):</span><span class="sxs-lookup"><span data-stu-id="16789-115">The console output shows messages similar to the following (indicating that the app is running and awaiting requests):</span></span>

```console
$ dotnet run
Hosting environment: Development
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="16789-116">W przeglądarce internetowej przejdź do adresu `http://localhost:<port number>/api/math/sum?a=4&b=5`.</span><span class="sxs-lookup"><span data-stu-id="16789-116">In a web browser, navigate to `http://localhost:<port number>/api/math/sum?a=4&b=5`.</span></span> <span data-ttu-id="16789-117">Powinien zostać wyświetlony wynik `9` .</span><span class="sxs-lookup"><span data-stu-id="16789-117">You should see the result of `9`.</span></span>

<span data-ttu-id="16789-118">Przejdź do interfejsu API produktu ( `http://localhost:<port number>/api/math/product?a=4&b=5` ).</span><span class="sxs-lookup"><span data-stu-id="16789-118">Navigate to the product API (`http://localhost:<port number>/api/math/product?a=4&b=5`).</span></span> <span data-ttu-id="16789-119">Zwraca wartość `9` , `20` a nie zgodnie z oczekiwaniami.</span><span class="sxs-lookup"><span data-stu-id="16789-119">It returns `9`, not `20` as you'd expect.</span></span> <span data-ttu-id="16789-120">Ten problem został rozwiązany w dalszej części tego samouczka.</span><span class="sxs-lookup"><span data-stu-id="16789-120">That problem is fixed later in the tutorial.</span></span>

::: moniker range="<= aspnetcore-2.0"

## <a name="add-dotnet-watch-to-a-project"></a><span data-ttu-id="16789-121">Dodaj `dotnet watch` do projektu</span><span class="sxs-lookup"><span data-stu-id="16789-121">Add `dotnet watch` to a project</span></span>

<span data-ttu-id="16789-122">`dotnet watch`Narzędzie obserwator plików jest dołączone do wersji 2.1.300 zestaw .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="16789-122">The `dotnet watch` file watcher tool is included with version 2.1.300 of the .NET Core SDK.</span></span> <span data-ttu-id="16789-123">Poniższe kroki są wymagane w przypadku korzystania ze starszej wersji zestaw .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="16789-123">The following steps are required when using an earlier version of the .NET Core SDK.</span></span>

1. <span data-ttu-id="16789-124">Dodaj `Microsoft.DotNet.Watcher.Tools` odwołanie do pakietu do pliku *csproj* :</span><span class="sxs-lookup"><span data-stu-id="16789-124">Add a `Microsoft.DotNet.Watcher.Tools` package reference to the *.csproj* file:</span></span>

    ```xml
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
    </ItemGroup>
    ```

1. <span data-ttu-id="16789-125">Zainstaluj `Microsoft.DotNet.Watcher.Tools` pakiet, uruchamiając następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="16789-125">Install the `Microsoft.DotNet.Watcher.Tools` package by running the following command:</span></span>

    ```dotnetcli
    dotnet restore
    ```

::: moniker-end

## <a name="run-net-core-cli-commands-using-dotnet-watch"></a><span data-ttu-id="16789-126">Uruchamianie poleceń interfejs wiersza polecenia platformy .NET Core przy użyciu `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="16789-126">Run .NET Core CLI commands using `dotnet watch`</span></span>

<span data-ttu-id="16789-127">Każde [polecenie interfejs wiersza polecenia platformy .NET Core](/dotnet/core/tools#cli-commands) można uruchomić za pomocą polecenia `dotnet watch` .</span><span class="sxs-lookup"><span data-stu-id="16789-127">Any [.NET Core CLI command](/dotnet/core/tools#cli-commands) can be run with `dotnet watch`.</span></span> <span data-ttu-id="16789-128">Przykład:</span><span class="sxs-lookup"><span data-stu-id="16789-128">For example:</span></span>

| <span data-ttu-id="16789-129">Polecenie</span><span class="sxs-lookup"><span data-stu-id="16789-129">Command</span></span> | <span data-ttu-id="16789-130">Polecenie z zegarkiem</span><span class="sxs-lookup"><span data-stu-id="16789-130">Command with watch</span></span> |
| ---- | ----- |
| <span data-ttu-id="16789-131">dotnet run</span><span class="sxs-lookup"><span data-stu-id="16789-131">dotnet run</span></span> | <span data-ttu-id="16789-132">uruchomienie czujka dotnet</span><span class="sxs-lookup"><span data-stu-id="16789-132">dotnet watch run</span></span> |
| <span data-ttu-id="16789-133">uruchomienie dotnet-f netcoreapp 3.1</span><span class="sxs-lookup"><span data-stu-id="16789-133">dotnet run -f netcoreapp3.1</span></span> | <span data-ttu-id="16789-134">uruchomienie czujka dotnet-f netcoreapp 3.1</span><span class="sxs-lookup"><span data-stu-id="16789-134">dotnet watch run -f netcoreapp3.1</span></span> |
| <span data-ttu-id="16789-135">uruchomienie dotnet-f netcoreapp 3.1----arg1</span><span class="sxs-lookup"><span data-stu-id="16789-135">dotnet run -f netcoreapp3.1 -- --arg1</span></span> | <span data-ttu-id="16789-136">uruchomienie czujka dotnet-f netcoreapp 3.1----arg1</span><span class="sxs-lookup"><span data-stu-id="16789-136">dotnet watch run -f netcoreapp3.1 -- --arg1</span></span> |
| <span data-ttu-id="16789-137">dotnet test</span><span class="sxs-lookup"><span data-stu-id="16789-137">dotnet test</span></span> | <span data-ttu-id="16789-138">Test czujki dotnet</span><span class="sxs-lookup"><span data-stu-id="16789-138">dotnet watch test</span></span> |

<span data-ttu-id="16789-139">Uruchom `dotnet watch run` w folderze *webapp* .</span><span class="sxs-lookup"><span data-stu-id="16789-139">Run `dotnet watch run` in the *WebApp* folder.</span></span> <span data-ttu-id="16789-140">Dane wyjściowe konsoli wskazują, że `watch` uruchomiono.</span><span class="sxs-lookup"><span data-stu-id="16789-140">The console output indicates `watch` has started.</span></span>

::: moniker range=">= aspnetcore-5.0"
<span data-ttu-id="16789-141">Uruchomienie `dotnet watch run` w aplikacji sieci Web powoduje uruchomienie przeglądarki, która przechodzi do adresu URL aplikacji po jej przygotowaniu.</span><span class="sxs-lookup"><span data-stu-id="16789-141">Running `dotnet watch run` on a web app launches a browser that navigates to the app's URL once ready.</span></span> <span data-ttu-id="16789-142">`dotnet watch` w tym celu należy odczytać dane wyjściowe konsoli aplikacji i oczekiwanie na komunikat o gotowości wyświetlany przez <xref:Microsoft.AspNetCore.WebHost> .</span><span class="sxs-lookup"><span data-stu-id="16789-142">`dotnet watch` does this by reading the app's console output and waiting for the the ready message displayed by <xref:Microsoft.AspNetCore.WebHost>.</span></span>

<span data-ttu-id="16789-143">`dotnet watch` Odświeża przeglądarkę po wykryciu zmian w oglądanych plikach.</span><span class="sxs-lookup"><span data-stu-id="16789-143">`dotnet watch` refreshes the browser when it detects changes to watched files.</span></span> <span data-ttu-id="16789-144">W tym celu polecenie czujki wstawia oprogramowanie pośredniczące do aplikacji, która modyfikuje odpowiedzi HTML utworzone przez aplikację.</span><span class="sxs-lookup"><span data-stu-id="16789-144">To do this, the watch command injects a middleware to the app that modifies HTML responses created by the app.</span></span> <span data-ttu-id="16789-145">Oprogramowanie pośredniczące dodaje blok skryptu JavaScript do strony, która umożliwia `dotnet watch` poinstruujenie przeglądarki w celu odświeżenia.</span><span class="sxs-lookup"><span data-stu-id="16789-145">The middleware adds a JavaScript script block to the page that allows `dotnet watch` to instruct the browser to refresh.</span></span> <span data-ttu-id="16789-146">Obecnie zmiany wszystkich obserwowanych plików, w tym zawartości statycznej, takiej jak pliki *. html* i *. css* , powodują ponowne skompilowanie aplikacji.</span><span class="sxs-lookup"><span data-stu-id="16789-146">Currently, changes to all watched files, including static content such as *.html* and *.css* files cause the app to be rebuilt.</span></span>

<span data-ttu-id="16789-147">`dotnet watch`:</span><span class="sxs-lookup"><span data-stu-id="16789-147">`dotnet watch`:</span></span>

* <span data-ttu-id="16789-148">Program domyślnie Obserwujący pliki, które mają wpływ na kompilacje.</span><span class="sxs-lookup"><span data-stu-id="16789-148">Only watches files that impact builds by default.</span></span>
* <span data-ttu-id="16789-149">Wszystkie dodatkowo obserwowane pliki (za pośrednictwem konfiguracji) nadal będą miały miejsce w kompilacji.</span><span class="sxs-lookup"><span data-stu-id="16789-149">Any additionally watched files (via configuration) still results in a build taking place.</span></span>

<span data-ttu-id="16789-150">Aby uzyskać więcej informacji na temat konfiguracji, zobacz [Konfiguracja dotnet-Obejrzyj konfigurację](#dotnet-watch-configuration) w tym dokumencie</span><span class="sxs-lookup"><span data-stu-id="16789-150">For more information on configuration, see [dotnet-watch configuration](#dotnet-watch-configuration) in this document</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="16789-151">Możesz użyć, `dotnet watch --project <PROJECT>` Aby określić projekt do obejrzenia.</span><span class="sxs-lookup"><span data-stu-id="16789-151">You can use `dotnet watch --project <PROJECT>` to specify a project to watch.</span></span> <span data-ttu-id="16789-152">Na przykład uruchomienie `dotnet watch --project WebApp run` z poziomu głównego przykładowej aplikacji również uruchomi się i obejrzyj projekt *webapp* .</span><span class="sxs-lookup"><span data-stu-id="16789-152">For example, running `dotnet watch --project WebApp run` from the root of the sample app will also run and watch the *WebApp* project.</span></span>

## <a name="make-changes-with-dotnet-watch"></a><span data-ttu-id="16789-153">Wprowadzanie zmian `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="16789-153">Make changes with `dotnet watch`</span></span>

<span data-ttu-id="16789-154">Upewnij się, że `dotnet watch` jest uruchomiony program.</span><span class="sxs-lookup"><span data-stu-id="16789-154">Make sure `dotnet watch` is running.</span></span>

<span data-ttu-id="16789-155">Usuń usterkę w `Product` metodzie *MathController.cs* , aby zwracała produkt, a nie sumę:</span><span class="sxs-lookup"><span data-stu-id="16789-155">Fix the bug in the `Product` method of *MathController.cs* so it returns the product and not the sum:</span></span>

```csharp
public static int Product(int a, int b)
{
    return a * b;
}
```

<span data-ttu-id="16789-156">Zapisz plik.</span><span class="sxs-lookup"><span data-stu-id="16789-156">Save the file.</span></span> <span data-ttu-id="16789-157">Dane wyjściowe konsoli wskazują, że `dotnet watch` wykryto zmianę pliku i ponownie uruchom aplikację.</span><span class="sxs-lookup"><span data-stu-id="16789-157">The console output indicates that `dotnet watch` detected a file change and restarted the app.</span></span>

<span data-ttu-id="16789-158">`http://localhost:<port number>/api/math/product?a=4&b=5`Funkcja verify zwraca prawidłowy wynik.</span><span class="sxs-lookup"><span data-stu-id="16789-158">Verify `http://localhost:<port number>/api/math/product?a=4&b=5` returns the correct result.</span></span>

## <a name="run-tests-using-dotnet-watch"></a><span data-ttu-id="16789-159">Uruchom testy przy użyciu `dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="16789-159">Run tests using `dotnet watch`</span></span>

1. <span data-ttu-id="16789-160">Zmień `Product` metodę *MathController.cs* z powrotem, aby zwracać sumę.</span><span class="sxs-lookup"><span data-stu-id="16789-160">Change the `Product` method of *MathController.cs* back to returning the sum.</span></span> <span data-ttu-id="16789-161">Zapisz plik.</span><span class="sxs-lookup"><span data-stu-id="16789-161">Save the file.</span></span>
1. <span data-ttu-id="16789-162">W powłoce poleceń przejdź do folderu *WebAppTests* .</span><span class="sxs-lookup"><span data-stu-id="16789-162">In a command shell, navigate to the *WebAppTests* folder.</span></span>
1. <span data-ttu-id="16789-163">Uruchom [dotnet Restore](/dotnet/core/tools/dotnet-restore).</span><span class="sxs-lookup"><span data-stu-id="16789-163">Run [dotnet restore](/dotnet/core/tools/dotnet-restore).</span></span>
1. <span data-ttu-id="16789-164">Uruchom polecenie `dotnet watch test`.</span><span class="sxs-lookup"><span data-stu-id="16789-164">Run `dotnet watch test`.</span></span> <span data-ttu-id="16789-165">Dane wyjściowe wskazują, że test zakończył się niepowodzeniem i że obserwator oczekuje na zmiany plików:</span><span class="sxs-lookup"><span data-stu-id="16789-165">Its output indicates that a test failed and that the watcher is awaiting file changes:</span></span>

     ```console
     Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
     Test Run Failed.
     ```

1. <span data-ttu-id="16789-166">Popraw `Product` Kod metody, aby zwracał produkt.</span><span class="sxs-lookup"><span data-stu-id="16789-166">Fix the `Product` method code so it returns the product.</span></span> <span data-ttu-id="16789-167">Zapisz plik.</span><span class="sxs-lookup"><span data-stu-id="16789-167">Save the file.</span></span>

<span data-ttu-id="16789-168">`dotnet watch` wykrywa zmianę pliku i ponownie uruchamia testy.</span><span class="sxs-lookup"><span data-stu-id="16789-168">`dotnet watch` detects the file change and reruns the tests.</span></span> <span data-ttu-id="16789-169">Dane wyjściowe konsoli wskazują zakończono testy.</span><span class="sxs-lookup"><span data-stu-id="16789-169">The console output indicates the tests passed.</span></span>

## <a name="customize-files-list-to-watch"></a><span data-ttu-id="16789-170">Dostosuj listę plików do czujki</span><span class="sxs-lookup"><span data-stu-id="16789-170">Customize files list to watch</span></span>

<span data-ttu-id="16789-171">Domyślnie program `dotnet-watch` śledzi wszystkie pliki zgodne z następującymi wzorcami globalizowania:</span><span class="sxs-lookup"><span data-stu-id="16789-171">By default, `dotnet-watch` tracks all files matching the following glob patterns:</span></span>

* `**/*.cs`
* `*.csproj`
* `**/*.resx`

<span data-ttu-id="16789-172">Więcej elementów można dodać do listy obserwacje, edytując plik *csproj* .</span><span class="sxs-lookup"><span data-stu-id="16789-172">More items can be added to the watch list by editing the *.csproj* file.</span></span> <span data-ttu-id="16789-173">Elementy można określać pojedynczo lub przy użyciu wzorców globalizowania.</span><span class="sxs-lookup"><span data-stu-id="16789-173">Items can be specified individually or by using glob patterns.</span></span>

```xml
<ItemGroup>
    <!-- extends watching group to include *.js files -->
    <Watch Include="**\*.js" Exclude="node_modules\**\*;**\*.js.map;obj\**\*;bin\**\*" />
</ItemGroup>
```

## <a name="opt-out-of-files-to-be-watched"></a><span data-ttu-id="16789-174">Rezygnacja z plików, które mają być obserwowane</span><span class="sxs-lookup"><span data-stu-id="16789-174">Opt-out of files to be watched</span></span>

<span data-ttu-id="16789-175">`dotnet-watch` można skonfigurować do ignorowania ustawień domyślnych.</span><span class="sxs-lookup"><span data-stu-id="16789-175">`dotnet-watch` can be configured to ignore its default settings.</span></span> <span data-ttu-id="16789-176">Aby zignorować określone pliki, Dodaj `Watch="false"` atrybut do definicji elementu w pliku *. csproj* :</span><span class="sxs-lookup"><span data-stu-id="16789-176">To ignore specific files, add the `Watch="false"` attribute to an item's definition in the *.csproj* file:</span></span>

```xml
<ItemGroup>
    <!-- exclude Generated.cs from dotnet-watch -->
    <Compile Include="Generated.cs" Watch="false" />

    <!-- exclude Strings.resx from dotnet-watch -->
    <EmbeddedResource Include="Strings.resx" Watch="false" />

    <!-- exclude changes in this referenced project -->
    <ProjectReference Include="..\ClassLibrary1\ClassLibrary1.csproj" Watch="false" />
</ItemGroup>
```

## <a name="custom-watch-projects"></a><span data-ttu-id="16789-177">Niestandardowe projekty czujki</span><span class="sxs-lookup"><span data-stu-id="16789-177">Custom watch projects</span></span>

<span data-ttu-id="16789-178">`dotnet-watch` nie jest ograniczone do projektów języka C#.</span><span class="sxs-lookup"><span data-stu-id="16789-178">`dotnet-watch` isn't restricted to C# projects.</span></span> <span data-ttu-id="16789-179">Niestandardowe projekty czujki mogą być tworzone w celu obsługi różnych scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="16789-179">Custom watch projects can be created to handle different scenarios.</span></span> <span data-ttu-id="16789-180">Rozważmy następujący układ projektu:</span><span class="sxs-lookup"><span data-stu-id="16789-180">Consider the following project layout:</span></span>

* <span data-ttu-id="16789-181">**badan**</span><span class="sxs-lookup"><span data-stu-id="16789-181">**test/**</span></span>
  * <span data-ttu-id="16789-182">*UnitTests/UnitTests. csproj*</span><span class="sxs-lookup"><span data-stu-id="16789-182">*UnitTests/UnitTests.csproj*</span></span>
  * <span data-ttu-id="16789-183">*IntegrationTests/IntegrationTests. csproj*</span><span class="sxs-lookup"><span data-stu-id="16789-183">*IntegrationTests/IntegrationTests.csproj*</span></span>

<span data-ttu-id="16789-184">Jeśli celem jest oglądanie obu projektów, Utwórz niestandardowy plik projektu, który został skonfigurowany do oglądania obu projektów:</span><span class="sxs-lookup"><span data-stu-id="16789-184">If the goal is to watch both projects, create a custom project file configured to watch both projects:</span></span>

```xml
<Project>
    <ItemGroup>
        <TestProjects Include="**\*.csproj" />
        <Watch Include="**\*.cs" />
    </ItemGroup>

    <Target Name="Test">
        <MSBuild Targets="VSTest" Projects="@(TestProjects)" />
    </Target>

    <Import Project="$(MSBuildExtensionsPath)\Microsoft.Common.targets" />
</Project>
```

<span data-ttu-id="16789-185">Aby rozpocząć obserwowanie plików w obu projektach, przejdź do folderu *testowego* .</span><span class="sxs-lookup"><span data-stu-id="16789-185">To start file watching on both projects, change to the *test* folder.</span></span> <span data-ttu-id="16789-186">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="16789-186">Execute the following command:</span></span>

```dotnetcli
dotnet watch msbuild /t:Test
```

<span data-ttu-id="16789-187">VSTest wykonuje się, gdy jakiekolwiek zmiany pliku w każdym projekcie testowym.</span><span class="sxs-lookup"><span data-stu-id="16789-187">VSTest executes when any file changes in either test project.</span></span>

## <a name="dotnet-watch-configuration"></a><span data-ttu-id="16789-188">Konfiguracja monitorowania dotnet</span><span class="sxs-lookup"><span data-stu-id="16789-188">dotnet-watch configuration</span></span>

<span data-ttu-id="16789-189">Niektóre opcje konfiguracji mogą być przesyłane `dotnet watch` przez zmienne środowiskowe.</span><span class="sxs-lookup"><span data-stu-id="16789-189">Some configuration options can be passed to `dotnet watch` through environment variables.</span></span> <span data-ttu-id="16789-190">Dostępne są następujące zmienne:</span><span class="sxs-lookup"><span data-stu-id="16789-190">The available variables are:</span></span>

| <span data-ttu-id="16789-191">Ustawienie</span><span class="sxs-lookup"><span data-stu-id="16789-191">Setting</span></span>  | <span data-ttu-id="16789-192">Opis</span><span class="sxs-lookup"><span data-stu-id="16789-192">Description</span></span> |
| ------------- | ------------- |
| `DOTNET_USE_POLLING_FILE_WATCHER`                | <span data-ttu-id="16789-193">W przypadku ustawienia wartości "1" lub "true" program `dotnet watch` używa obserwatora pliku sondowania zamiast CoreFx `FileSystemWatcher` .</span><span class="sxs-lookup"><span data-stu-id="16789-193">If set to "1" or "true", `dotnet watch` uses a polling file watcher instead of CoreFx's `FileSystemWatcher`.</span></span> <span data-ttu-id="16789-194">Używane podczas oglądania plików w udziałach sieciowych lub zainstalowanych woluminów platformy Docker.</span><span class="sxs-lookup"><span data-stu-id="16789-194">Used when watching files on network shares or Docker mounted volumes.</span></span>                       |
| `DOTNET_WATCH_SUPPRESS_MSBUILD_INCREMENTALISM`   | <span data-ttu-id="16789-195">Domyślnie program `dotnet watch` optymalizuje kompilację, unikając niektórych operacji, takich jak uruchamianie przywracania lub ponowne ocenianie zestawu obserwowanych plików podczas każdej zmiany pliku.</span><span class="sxs-lookup"><span data-stu-id="16789-195">By default, `dotnet watch` optimizes the build by avoiding certain operations such as running restore or re-evaluating the set of watched files on every file change.</span></span> <span data-ttu-id="16789-196">W przypadku ustawienia wartości "1" lub "true" te optymalizacje są wyłączone.</span><span class="sxs-lookup"><span data-stu-id="16789-196">If set to "1" or "true",  these optimizations are disabled.</span></span> |
| `DOTNET_WATCH_SUPPRESS_LAUNCH_BROWSER`   | <span data-ttu-id="16789-197">`dotnet watch run` próbuje uruchomić przeglądarki dla aplikacji sieci Web `launchBrowser` skonfigurowanych w *launchSettings.jsna*.</span><span class="sxs-lookup"><span data-stu-id="16789-197">`dotnet watch run` attempts to launch browsers for web apps with `launchBrowser` configured in *launchSettings.json*.</span></span> <span data-ttu-id="16789-198">W przypadku ustawienia wartości "1" lub "prawda" to zachowanie jest pomijane.</span><span class="sxs-lookup"><span data-stu-id="16789-198">If set to "1" or "true", this behavior is suppressed.</span></span> |
| `DOTNET_WATCH_SUPPRESS_BROWSER_REFRESH`   | <span data-ttu-id="16789-199">`dotnet watch run` próbuje odświeżyć przeglądarki po wykryciu zmian plików.</span><span class="sxs-lookup"><span data-stu-id="16789-199">`dotnet watch run` attempts to refresh browsers when it detects file changes.</span></span> <span data-ttu-id="16789-200">W przypadku ustawienia wartości "1" lub "prawda" to zachowanie jest pomijane.</span><span class="sxs-lookup"><span data-stu-id="16789-200">If set to "1" or "true", this behavior is suppressed.</span></span> <span data-ttu-id="16789-201">To zachowanie jest również pomijane, jeśli `DOTNET_WATCH_SUPPRESS_LAUNCH_BROWSER` jest ustawiona.</span><span class="sxs-lookup"><span data-stu-id="16789-201">This behavior is also suppressed if `DOTNET_WATCH_SUPPRESS_LAUNCH_BROWSER` is set.</span></span> |

## <a name="dotnet-watch-in-github"></a><span data-ttu-id="16789-202">`dotnet-watch` w witrynie GitHub</span><span class="sxs-lookup"><span data-stu-id="16789-202">`dotnet-watch` in GitHub</span></span>

<span data-ttu-id="16789-203">`dotnet-watch` jest częścią [repozytorium dotnet/AspNetCore](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch)usługi GitHub.</span><span class="sxs-lookup"><span data-stu-id="16789-203">`dotnet-watch` is part of the GitHub [dotnet/AspNetCore repository](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch).</span></span>
