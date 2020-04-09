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
# <a name="develop-aspnet-core-apps-using-a-file-watcher"></a>Tworzenie aplikacji ASP.NET Core przy użyciu obserwatora plików

Przez [Rick Anderson](https://twitter.com/RickAndMSFT) i Victor [Hurdugaci](https://twitter.com/victorhurdugaci)

[dotnet watch](https://www.nuget.org/packages/dotnet-watch) to narzędzie, które uruchamia polecenie [.NET Core CLI](/dotnet/core/tools) po zmianie plików źródłowych. Na przykład zmiana pliku może wyzwolić kompilacji, wykonanie testu lub wdrożenia.

W tym samouczku użyto istniejącego interfejsu API sieci web z dwoma punktami końcowymi: jednym, który zwraca sumę, a drugim zwracanym produktem. Metoda produktu ma błąd, który został naprawiony w tym samouczku.

Pobierz [przykładową aplikację](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample). Składa się z dwóch projektów: *WebApp* (ASP.NET Core web API) i *WebAppTests* (testy jednostkowe dla internetowego interfejsu API).

W powłoce poleceń przejdź do folderu *WebApp.* Uruchom następujące polecenie:

```dotnetcli
dotnet run
```

> [!NOTE]
> Można użyć `dotnet run --project <PROJECT>` do określenia projektu do uruchomienia. Na przykład `dotnet run --project WebApp` uruchomienie z katalogu głównego przykładowej aplikacji spowoduje również uruchomienie projektu *WebApp.*

Dane wyjściowe konsoli zawiera komunikaty podobne do następujących (wskazujące, że aplikacja jest uruchomiona i oczekuje na żądania):

```console
$ dotnet run
Hosting environment: Development
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

W przeglądarce internetowej przejdź do adresu `http://localhost:<port number>/api/math/sum?a=4&b=5`. Powinien zostać wyświetlony `9`wynik .

Przejdź do interfejsu`http://localhost:<port number>/api/math/product?a=4&b=5`API produktu ( ). Powraca `9`, `20` a nie tak, jak można się spodziewać. Ten problem został rozwiązany w dalszej części samouczka.

::: moniker range="<= aspnetcore-2.0"

## <a name="add-dotnet-watch-to-a-project"></a>Dodawanie `dotnet watch` do projektu

Narzędzie `dotnet watch` do obserwowanie plików jest dołączone do wersji 2.1.300 zestawu .NET Core SDK. Poniższe kroki są wymagane podczas korzystania z wcześniejszej wersji .NET Core SDK.

1. Dodaj `Microsoft.DotNet.Watcher.Tools` odwołanie do pakietu do pliku *csproj:*

    ```xml
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
    </ItemGroup>
    ```

1. Zainstaluj `Microsoft.DotNet.Watcher.Tools` pakiet, uruchamiając następujące polecenie:

    ```dotnetcli
    dotnet restore
    ```

::: moniker-end

## <a name="run-net-core-cli-commands-using-dotnet-watch"></a>Uruchamianie poleceń interfejsu wiersza polecenia .NET Core przy użyciu`dotnet watch`

Za pomocą polecenia .NET Core `dotnet watch`CLI można uruchomić dowolną [polecenie .NET Core CLI.](/dotnet/core/tools#cli-commands) Przykład:

| Polecenie | Polecenie z zegarkiem |
| ---- | ----- |
| dotnet run | zegarek dotnet uruchomić |
| dotnet run -f netcoreapp2.0 | zegarek dotnet uruchomić -f netcoreapp2.0 |
| dotnet run -f netcoreapp2.0 -- --arg1 | zegarek dotnet uruchomić -f netcoreapp2.0 -- --arg1 |
| dotnet test | test zegarka dotnet |

Uruchom `dotnet watch run` w folderze *WebApp.* Dane wyjściowe `watch` konsoli wskazuje, że został uruchomiony.

> [!NOTE]
> Można użyć `dotnet watch --project <PROJECT>` do określenia projektu do oglądania. Na przykład `dotnet watch --project WebApp run` uruchamianie z katalogu głównego przykładowej aplikacji będzie również uruchamiać i oglądać projekt *WebApp.*

## <a name="make-changes-with-dotnet-watch"></a>Wprowadzanie zmian za pomocą`dotnet watch`

Upewnij `dotnet watch` się, że jest uruchomiony.

Napraw błąd w `Product` metodzie *MathController.cs,* aby zwracał produkt, a nie sumę:

```csharp
public static int Product(int a, int b)
{
    return a * b;
}
```

Zapisz plik. Dane wyjściowe konsoli `dotnet watch` wskazują, że wykryła zmianę pliku i ponownie uruchomiła aplikację.

Verify `http://localhost:<port number>/api/math/product?a=4&b=5` zwraca poprawny wynik.

## <a name="run-tests-using-dotnet-watch"></a>Uruchamianie testów przy użyciu`dotnet watch`

1. Zmień `Product` metodę *MathController.cs* z powrotem do zwracania sumy. Zapisz plik.
1. W powłoce poleceń przejdź do folderu *WebAppTests.*
1. Uruchom [przywracanie dotnet](/dotnet/core/tools/dotnet-restore).
1. Uruchom polecenie `dotnet watch test`. Jego dane wyjściowe wskazują, że test nie powiódł się i że obserwator oczekuje na zmiany pliku:

     ```console
     Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
     Test Run Failed.
     ```

1. Napraw `Product` kod metody, aby zwracał produkt. Zapisz plik.

`dotnet watch`wykrywa zmianę pliku i ponownie przeprowadza testy. Dane wyjściowe konsoli wskazują, że testy zostały przeprowadzone.

## <a name="customize-files-list-to-watch"></a>Dostosowywanie listy plików do oglądania

Domyślnie `dotnet-watch` śledzi wszystkie pliki pasujące do następujących wzorców glob:

* `**/*.cs`
* `*.csproj`
* `**/*.resx`

Więcej elementów można dodać do listy obserwowanych, edytując plik *csproj.* Elementy można określić indywidualnie lub za pomocą wzorców glob.

```xml
<ItemGroup>
    <!-- extends watching group to include *.js files -->
    <Watch Include="**\*.js" Exclude="node_modules\**\*;**\*.js.map;obj\**\*;bin\**\*" />
</ItemGroup>
```

## <a name="opt-out-of-files-to-be-watched"></a>Rezygnacja z plików do obejrzenia

`dotnet-watch`można skonfigurować tak, aby ignorować jego ustawienia domyślne. Aby zignorować określone `Watch="false"` pliki, dodaj atrybut do definicji elementu w pliku *csproj:*

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

## <a name="custom-watch-projects"></a>Niestandardowe projekty zegarków

`dotnet-watch`nie jest ograniczona do projektów języka C#. Niestandardowe projekty zegarka można utworzyć do obsługi różnych scenariuszy. Należy wziąć pod uwagę następujący układ projektu:

* **test/**
  * *Testy jednostek/unittestów.csproj*
  * *IntegrationTests/IntegrationTests.csproj*

Jeśli celem jest obserwowanie obu projektów, utwórz niestandardowy plik projektu skonfigurowany do oglądania obu projektów:

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

Aby rozpocząć oglądanie plików w obu projektach, zmień folder *testowy.* Wykonaj następujące polecenie:

```dotnetcli
dotnet watch msbuild /t:Test
```

VSTest jest wykonywany, gdy wszystkie zmiany pliku w każdym projekcie testowym.

## <a name="dotnet-watch-in-github"></a>`dotnet-watch`w gitHub

`dotnet-watch`jest częścią [repozytorium Dotnet/AspNetCore](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch)GitHub .
