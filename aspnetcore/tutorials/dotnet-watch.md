---
title: Tworzenie aplikacji ASP.NET Core przy użyciu obserwatora plików
author: rick-anderson
description: W tym samouczku pokazano, jak zainstalować i używać narzędzia do pilnowania plików interfejsu .NET Core w aplikacji ASP.NET Core.
ms.author: riande
ms.date: 05/31/2018
uid: tutorials/dotnet-watch
ms.openlocfilehash: bedb3e6a65839db915ca7bc821a267a14d34bf30
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667413"
---
# <a name="develop-aspnet-core-apps-using-a-file-watcher"></a><span data-ttu-id="18d8c-103">Tworzenie aplikacji ASP.NET Core przy użyciu obserwatora plików</span><span class="sxs-lookup"><span data-stu-id="18d8c-103">Develop ASP.NET Core apps using a file watcher</span></span>

<span data-ttu-id="18d8c-104">Przez [Rick Anderson](https://twitter.com/RickAndMSFT) i Victor [Hurdugaci](https://twitter.com/victorhurdugaci)</span><span class="sxs-lookup"><span data-stu-id="18d8c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span></span>

<span data-ttu-id="18d8c-105">[dotnet watch](https://www.nuget.org/packages/dotnet-watch) to narzędzie, które uruchamia polecenie [.NET Core CLI](/dotnet/core/tools) po zmianie plików źródłowych.</span><span class="sxs-lookup"><span data-stu-id="18d8c-105">[dotnet watch](https://www.nuget.org/packages/dotnet-watch) is a tool that runs a [.NET Core CLI](/dotnet/core/tools) command when source files change.</span></span> <span data-ttu-id="18d8c-106">Na przykład zmiana pliku może wyzwolić kompilacji, wykonanie testu lub wdrożenia.</span><span class="sxs-lookup"><span data-stu-id="18d8c-106">For example, a file change can trigger compilation, test execution, or deployment.</span></span>

<span data-ttu-id="18d8c-107">W tym samouczku użyto istniejącego interfejsu API sieci web z dwoma punktami końcowymi: jednym, który zwraca sumę, a drugim zwracanym produktem.</span><span class="sxs-lookup"><span data-stu-id="18d8c-107">This tutorial uses an existing web API with two endpoints: one that returns a sum and one that returns a product.</span></span> <span data-ttu-id="18d8c-108">Metoda produktu ma błąd, który został naprawiony w tym samouczku.</span><span class="sxs-lookup"><span data-stu-id="18d8c-108">The product method has a bug, which is fixed in this tutorial.</span></span>

<span data-ttu-id="18d8c-109">Pobierz [przykładową aplikację](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample).</span><span class="sxs-lookup"><span data-stu-id="18d8c-109">Download the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample).</span></span> <span data-ttu-id="18d8c-110">Składa się z dwóch projektów: *WebApp* (ASP.NET Core web API) i *WebAppTests* (testy jednostkowe dla internetowego interfejsu API).</span><span class="sxs-lookup"><span data-stu-id="18d8c-110">It consists of two projects: *WebApp* (an ASP.NET Core web API) and *WebAppTests* (unit tests for the web API).</span></span>

<span data-ttu-id="18d8c-111">W powłoce poleceń przejdź do folderu *WebApp.*</span><span class="sxs-lookup"><span data-stu-id="18d8c-111">In a command shell, navigate to the *WebApp* folder.</span></span> <span data-ttu-id="18d8c-112">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="18d8c-112">Run the following command:</span></span>

```dotnetcli
dotnet run
```

> [!NOTE]
> <span data-ttu-id="18d8c-113">Można użyć `dotnet run --project <PROJECT>` do określenia projektu do uruchomienia.</span><span class="sxs-lookup"><span data-stu-id="18d8c-113">You can use `dotnet run --project <PROJECT>` to specify a project to run.</span></span> <span data-ttu-id="18d8c-114">Na przykład `dotnet run --project WebApp` uruchomienie z katalogu głównego przykładowej aplikacji spowoduje również uruchomienie projektu *WebApp.*</span><span class="sxs-lookup"><span data-stu-id="18d8c-114">For example, running `dotnet run --project WebApp` from the root of the sample app will also run the *WebApp* project.</span></span>

<span data-ttu-id="18d8c-115">Dane wyjściowe konsoli zawiera komunikaty podobne do następujących (wskazujące, że aplikacja jest uruchomiona i oczekuje na żądania):</span><span class="sxs-lookup"><span data-stu-id="18d8c-115">The console output shows messages similar to the following (indicating that the app is running and awaiting requests):</span></span>

```console
$ dotnet run
Hosting environment: Development
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="18d8c-116">W przeglądarce internetowej przejdź do adresu `http://localhost:<port number>/api/math/sum?a=4&b=5`.</span><span class="sxs-lookup"><span data-stu-id="18d8c-116">In a web browser, navigate to `http://localhost:<port number>/api/math/sum?a=4&b=5`.</span></span> <span data-ttu-id="18d8c-117">Powinien zostać wyświetlony `9`wynik .</span><span class="sxs-lookup"><span data-stu-id="18d8c-117">You should see the result of `9`.</span></span>

<span data-ttu-id="18d8c-118">Przejdź do interfejsu`http://localhost:<port number>/api/math/product?a=4&b=5`API produktu ( ).</span><span class="sxs-lookup"><span data-stu-id="18d8c-118">Navigate to the product API (`http://localhost:<port number>/api/math/product?a=4&b=5`).</span></span> <span data-ttu-id="18d8c-119">Powraca `9`, `20` a nie tak, jak można się spodziewać.</span><span class="sxs-lookup"><span data-stu-id="18d8c-119">It returns `9`, not `20` as you'd expect.</span></span> <span data-ttu-id="18d8c-120">Ten problem został rozwiązany w dalszej części samouczka.</span><span class="sxs-lookup"><span data-stu-id="18d8c-120">That problem is fixed later in the tutorial.</span></span>

::: moniker range="<= aspnetcore-2.0"

## <a name="add-dotnet-watch-to-a-project"></a><span data-ttu-id="18d8c-121">Dodawanie `dotnet watch` do projektu</span><span class="sxs-lookup"><span data-stu-id="18d8c-121">Add `dotnet watch` to a project</span></span>

<span data-ttu-id="18d8c-122">Narzędzie `dotnet watch` do obserwowanie plików jest dołączone do wersji 2.1.300 zestawu .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="18d8c-122">The `dotnet watch` file watcher tool is included with version 2.1.300 of the .NET Core SDK.</span></span> <span data-ttu-id="18d8c-123">Poniższe kroki są wymagane podczas korzystania z wcześniejszej wersji .NET Core SDK.</span><span class="sxs-lookup"><span data-stu-id="18d8c-123">The following steps are required when using an earlier version of the .NET Core SDK.</span></span>

1. <span data-ttu-id="18d8c-124">Dodaj `Microsoft.DotNet.Watcher.Tools` odwołanie do pakietu do pliku *csproj:*</span><span class="sxs-lookup"><span data-stu-id="18d8c-124">Add a `Microsoft.DotNet.Watcher.Tools` package reference to the *.csproj* file:</span></span>

    ```xml
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
    </ItemGroup>
    ```

1. <span data-ttu-id="18d8c-125">Zainstaluj `Microsoft.DotNet.Watcher.Tools` pakiet, uruchamiając następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="18d8c-125">Install the `Microsoft.DotNet.Watcher.Tools` package by running the following command:</span></span>

    ```dotnetcli
    dotnet restore
    ```

::: moniker-end

## <a name="run-net-core-cli-commands-using-dotnet-watch"></a><span data-ttu-id="18d8c-126">Uruchamianie poleceń interfejsu wiersza polecenia .NET Core przy użyciu`dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="18d8c-126">Run .NET Core CLI commands using `dotnet watch`</span></span>

<span data-ttu-id="18d8c-127">Za pomocą polecenia .NET Core `dotnet watch`CLI można uruchomić dowolną [polecenie .NET Core CLI.](/dotnet/core/tools#cli-commands)</span><span class="sxs-lookup"><span data-stu-id="18d8c-127">Any [.NET Core CLI command](/dotnet/core/tools#cli-commands) can be run with `dotnet watch`.</span></span> <span data-ttu-id="18d8c-128">Przykład:</span><span class="sxs-lookup"><span data-stu-id="18d8c-128">For example:</span></span>

| <span data-ttu-id="18d8c-129">Polecenie</span><span class="sxs-lookup"><span data-stu-id="18d8c-129">Command</span></span> | <span data-ttu-id="18d8c-130">Polecenie z zegarkiem</span><span class="sxs-lookup"><span data-stu-id="18d8c-130">Command with watch</span></span> |
| ---- | ----- |
| <span data-ttu-id="18d8c-131">dotnet run</span><span class="sxs-lookup"><span data-stu-id="18d8c-131">dotnet run</span></span> | <span data-ttu-id="18d8c-132">zegarek dotnet uruchomić</span><span class="sxs-lookup"><span data-stu-id="18d8c-132">dotnet watch run</span></span> |
| <span data-ttu-id="18d8c-133">dotnet run -f netcoreapp2.0</span><span class="sxs-lookup"><span data-stu-id="18d8c-133">dotnet run -f netcoreapp2.0</span></span> | <span data-ttu-id="18d8c-134">zegarek dotnet uruchomić -f netcoreapp2.0</span><span class="sxs-lookup"><span data-stu-id="18d8c-134">dotnet watch run -f netcoreapp2.0</span></span> |
| <span data-ttu-id="18d8c-135">dotnet run -f netcoreapp2.0 -- --arg1</span><span class="sxs-lookup"><span data-stu-id="18d8c-135">dotnet run -f netcoreapp2.0 -- --arg1</span></span> | <span data-ttu-id="18d8c-136">zegarek dotnet uruchomić -f netcoreapp2.0 -- --arg1</span><span class="sxs-lookup"><span data-stu-id="18d8c-136">dotnet watch run -f netcoreapp2.0 -- --arg1</span></span> |
| <span data-ttu-id="18d8c-137">dotnet test</span><span class="sxs-lookup"><span data-stu-id="18d8c-137">dotnet test</span></span> | <span data-ttu-id="18d8c-138">test zegarka dotnet</span><span class="sxs-lookup"><span data-stu-id="18d8c-138">dotnet watch test</span></span> |

<span data-ttu-id="18d8c-139">Uruchom `dotnet watch run` w folderze *WebApp.*</span><span class="sxs-lookup"><span data-stu-id="18d8c-139">Run `dotnet watch run` in the *WebApp* folder.</span></span> <span data-ttu-id="18d8c-140">Dane wyjściowe `watch` konsoli wskazuje, że został uruchomiony.</span><span class="sxs-lookup"><span data-stu-id="18d8c-140">The console output indicates `watch` has started.</span></span>

> [!NOTE]
> <span data-ttu-id="18d8c-141">Można użyć `dotnet watch --project <PROJECT>` do określenia projektu do oglądania.</span><span class="sxs-lookup"><span data-stu-id="18d8c-141">You can use `dotnet watch --project <PROJECT>` to specify a project to watch.</span></span> <span data-ttu-id="18d8c-142">Na przykład `dotnet watch --project WebApp run` uruchamianie z katalogu głównego przykładowej aplikacji będzie również uruchamiać i oglądać projekt *WebApp.*</span><span class="sxs-lookup"><span data-stu-id="18d8c-142">For example, running `dotnet watch --project WebApp run` from the root of the sample app will also run and watch the *WebApp* project.</span></span>

## <a name="make-changes-with-dotnet-watch"></a><span data-ttu-id="18d8c-143">Wprowadzanie zmian za pomocą`dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="18d8c-143">Make changes with `dotnet watch`</span></span>

<span data-ttu-id="18d8c-144">Upewnij `dotnet watch` się, że jest uruchomiony.</span><span class="sxs-lookup"><span data-stu-id="18d8c-144">Make sure `dotnet watch` is running.</span></span>

<span data-ttu-id="18d8c-145">Napraw błąd w `Product` metodzie *MathController.cs,* aby zwracał produkt, a nie sumę:</span><span class="sxs-lookup"><span data-stu-id="18d8c-145">Fix the bug in the `Product` method of *MathController.cs* so it returns the product and not the sum:</span></span>

```csharp
public static int Product(int a, int b)
{
    return a * b;
}
```

<span data-ttu-id="18d8c-146">Zapisz plik.</span><span class="sxs-lookup"><span data-stu-id="18d8c-146">Save the file.</span></span> <span data-ttu-id="18d8c-147">Dane wyjściowe konsoli `dotnet watch` wskazują, że wykryła zmianę pliku i ponownie uruchomiła aplikację.</span><span class="sxs-lookup"><span data-stu-id="18d8c-147">The console output indicates that `dotnet watch` detected a file change and restarted the app.</span></span>

<span data-ttu-id="18d8c-148">Verify `http://localhost:<port number>/api/math/product?a=4&b=5` zwraca poprawny wynik.</span><span class="sxs-lookup"><span data-stu-id="18d8c-148">Verify `http://localhost:<port number>/api/math/product?a=4&b=5` returns the correct result.</span></span>

## <a name="run-tests-using-dotnet-watch"></a><span data-ttu-id="18d8c-149">Uruchamianie testów przy użyciu`dotnet watch`</span><span class="sxs-lookup"><span data-stu-id="18d8c-149">Run tests using `dotnet watch`</span></span>

1. <span data-ttu-id="18d8c-150">Zmień `Product` metodę *MathController.cs* z powrotem do zwracania sumy.</span><span class="sxs-lookup"><span data-stu-id="18d8c-150">Change the `Product` method of *MathController.cs* back to returning the sum.</span></span> <span data-ttu-id="18d8c-151">Zapisz plik.</span><span class="sxs-lookup"><span data-stu-id="18d8c-151">Save the file.</span></span>
1. <span data-ttu-id="18d8c-152">W powłoce poleceń przejdź do folderu *WebAppTests.*</span><span class="sxs-lookup"><span data-stu-id="18d8c-152">In a command shell, navigate to the *WebAppTests* folder.</span></span>
1. <span data-ttu-id="18d8c-153">Uruchom [przywracanie dotnet](/dotnet/core/tools/dotnet-restore).</span><span class="sxs-lookup"><span data-stu-id="18d8c-153">Run [dotnet restore](/dotnet/core/tools/dotnet-restore).</span></span>
1. <span data-ttu-id="18d8c-154">Uruchom polecenie `dotnet watch test`.</span><span class="sxs-lookup"><span data-stu-id="18d8c-154">Run `dotnet watch test`.</span></span> <span data-ttu-id="18d8c-155">Jego dane wyjściowe wskazują, że test nie powiódł się i że obserwator oczekuje na zmiany pliku:</span><span class="sxs-lookup"><span data-stu-id="18d8c-155">Its output indicates that a test failed and that the watcher is awaiting file changes:</span></span>

     ```console
     Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
     Test Run Failed.
     ```

1. <span data-ttu-id="18d8c-156">Napraw `Product` kod metody, aby zwracał produkt.</span><span class="sxs-lookup"><span data-stu-id="18d8c-156">Fix the `Product` method code so it returns the product.</span></span> <span data-ttu-id="18d8c-157">Zapisz plik.</span><span class="sxs-lookup"><span data-stu-id="18d8c-157">Save the file.</span></span>

<span data-ttu-id="18d8c-158">`dotnet watch`wykrywa zmianę pliku i ponownie przeprowadza testy.</span><span class="sxs-lookup"><span data-stu-id="18d8c-158">`dotnet watch` detects the file change and reruns the tests.</span></span> <span data-ttu-id="18d8c-159">Dane wyjściowe konsoli wskazują, że testy zostały przeprowadzone.</span><span class="sxs-lookup"><span data-stu-id="18d8c-159">The console output indicates the tests passed.</span></span>

## <a name="customize-files-list-to-watch"></a><span data-ttu-id="18d8c-160">Dostosowywanie listy plików do oglądania</span><span class="sxs-lookup"><span data-stu-id="18d8c-160">Customize files list to watch</span></span>

<span data-ttu-id="18d8c-161">Domyślnie `dotnet-watch` śledzi wszystkie pliki pasujące do następujących wzorców glob:</span><span class="sxs-lookup"><span data-stu-id="18d8c-161">By default, `dotnet-watch` tracks all files matching the following glob patterns:</span></span>

* `**/*.cs`
* `*.csproj`
* `**/*.resx`

<span data-ttu-id="18d8c-162">Więcej elementów można dodać do listy obserwowanych, edytując plik *csproj.*</span><span class="sxs-lookup"><span data-stu-id="18d8c-162">More items can be added to the watch list by editing the *.csproj* file.</span></span> <span data-ttu-id="18d8c-163">Elementy można określić indywidualnie lub za pomocą wzorców glob.</span><span class="sxs-lookup"><span data-stu-id="18d8c-163">Items can be specified individually or by using glob patterns.</span></span>

```xml
<ItemGroup>
    <!-- extends watching group to include *.js files -->
    <Watch Include="**\*.js" Exclude="node_modules\**\*;**\*.js.map;obj\**\*;bin\**\*" />
</ItemGroup>
```

## <a name="opt-out-of-files-to-be-watched"></a><span data-ttu-id="18d8c-164">Rezygnacja z plików do obejrzenia</span><span class="sxs-lookup"><span data-stu-id="18d8c-164">Opt-out of files to be watched</span></span>

<span data-ttu-id="18d8c-165">`dotnet-watch`można skonfigurować tak, aby ignorować jego ustawienia domyślne.</span><span class="sxs-lookup"><span data-stu-id="18d8c-165">`dotnet-watch` can be configured to ignore its default settings.</span></span> <span data-ttu-id="18d8c-166">Aby zignorować określone `Watch="false"` pliki, dodaj atrybut do definicji elementu w pliku *csproj:*</span><span class="sxs-lookup"><span data-stu-id="18d8c-166">To ignore specific files, add the `Watch="false"` attribute to an item's definition in the *.csproj* file:</span></span>

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

## <a name="custom-watch-projects"></a><span data-ttu-id="18d8c-167">Niestandardowe projekty zegarków</span><span class="sxs-lookup"><span data-stu-id="18d8c-167">Custom watch projects</span></span>

<span data-ttu-id="18d8c-168">`dotnet-watch`nie jest ograniczona do projektów języka C#.</span><span class="sxs-lookup"><span data-stu-id="18d8c-168">`dotnet-watch` isn't restricted to C# projects.</span></span> <span data-ttu-id="18d8c-169">Niestandardowe projekty zegarka można utworzyć do obsługi różnych scenariuszy.</span><span class="sxs-lookup"><span data-stu-id="18d8c-169">Custom watch projects can be created to handle different scenarios.</span></span> <span data-ttu-id="18d8c-170">Należy wziąć pod uwagę następujący układ projektu:</span><span class="sxs-lookup"><span data-stu-id="18d8c-170">Consider the following project layout:</span></span>

* <span data-ttu-id="18d8c-171">**test/**</span><span class="sxs-lookup"><span data-stu-id="18d8c-171">**test/**</span></span>
  * <span data-ttu-id="18d8c-172">*Testy jednostek/unittestów.csproj*</span><span class="sxs-lookup"><span data-stu-id="18d8c-172">*UnitTests/UnitTests.csproj*</span></span>
  * <span data-ttu-id="18d8c-173">*IntegrationTests/IntegrationTests.csproj*</span><span class="sxs-lookup"><span data-stu-id="18d8c-173">*IntegrationTests/IntegrationTests.csproj*</span></span>

<span data-ttu-id="18d8c-174">Jeśli celem jest obserwowanie obu projektów, utwórz niestandardowy plik projektu skonfigurowany do oglądania obu projektów:</span><span class="sxs-lookup"><span data-stu-id="18d8c-174">If the goal is to watch both projects, create a custom project file configured to watch both projects:</span></span>

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

<span data-ttu-id="18d8c-175">Aby rozpocząć oglądanie plików w obu projektach, zmień folder *testowy.*</span><span class="sxs-lookup"><span data-stu-id="18d8c-175">To start file watching on both projects, change to the *test* folder.</span></span> <span data-ttu-id="18d8c-176">Wykonaj następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="18d8c-176">Execute the following command:</span></span>

```dotnetcli
dotnet watch msbuild /t:Test
```

<span data-ttu-id="18d8c-177">VSTest jest wykonywany, gdy wszystkie zmiany pliku w każdym projekcie testowym.</span><span class="sxs-lookup"><span data-stu-id="18d8c-177">VSTest executes when any file changes in either test project.</span></span>

## <a name="dotnet-watch-in-github"></a><span data-ttu-id="18d8c-178">`dotnet-watch`w gitHub</span><span class="sxs-lookup"><span data-stu-id="18d8c-178">`dotnet-watch` in GitHub</span></span>

<span data-ttu-id="18d8c-179">`dotnet-watch`jest częścią [repozytorium Dotnet/AspNetCore](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch)GitHub .</span><span class="sxs-lookup"><span data-stu-id="18d8c-179">`dotnet-watch` is part of the GitHub [dotnet/AspNetCore repository](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch).</span></span>
