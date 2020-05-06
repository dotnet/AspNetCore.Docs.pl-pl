---
title: Opracowywanie aplikacji ASP.NET Core przy użyciu obserwatora plików
author: rick-anderson
description: W tym samouczku pokazano, jak zainstalować i używać narzędzia obserwatora plików interfejs wiersza polecenia platformy .NET Core (czujka dotnet) w aplikacji ASP.NET Core.
ms.author: riande
ms.date: 05/31/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/dotnet-watch
ms.openlocfilehash: 17fc7fd2d65fd314d9f6f9530db5d511af248569
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776594"
---
# <a name="develop-aspnet-core-apps-using-a-file-watcher"></a>Opracowywanie aplikacji ASP.NET Core przy użyciu obserwatora plików

Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT) i [Victor Hurdugaci](https://twitter.com/victorhurdugaci)

[czujka dotnet](https://www.nuget.org/packages/dotnet-watch) jest narzędziem, które uruchamia [interfejs wiersza polecenia platformy .NET Core](/dotnet/core/tools) polecenie, gdy pliki źródłowe zmieniają się. Na przykład zmiana pliku może wyzwolić kompilację, wykonanie testu lub wdrożenie.

W tym samouczku jest wykorzystywany istniejący internetowy interfejs API z dwoma punktami końcowymi: jeden, który zwraca sumę i jeden z nich zwraca produkt. Metoda produktu zawiera usterkę, która została rozwiązana w tym samouczku.

Pobierz [przykładową aplikację](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample). Składa się z dwóch projektów: *webapp* (ASP.NET Core Web API) i *WebAppTests* (testy jednostkowe dla internetowego interfejsu API).

W powłoce poleceń przejdź do folderu *webapp* . Uruchom następujące polecenie:

```dotnetcli
dotnet run
```

> [!NOTE]
> Możesz użyć `dotnet run --project <PROJECT>` , aby określić projekt do uruchomienia. Na przykład uruchomienie `dotnet run --project WebApp` z poziomu głównego przykładowej aplikacji spowoduje również uruchomienie projektu *webapp* .

W danych wyjściowych konsoli są wyświetlane komunikaty podobne do następujących (wskazuje, że aplikacja działa i oczekuje na żądania):

```console
$ dotnet run
Hosting environment: Development
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

W przeglądarce internetowej przejdź do adresu `http://localhost:<port number>/api/math/sum?a=4&b=5`. Powinien zostać wyświetlony wynik `9`.

Przejdź do interfejsu API produktu (`http://localhost:<port number>/api/math/product?a=4&b=5`). Zwraca wartość `9`, a `20` nie zgodnie z oczekiwaniami. Ten problem został rozwiązany w dalszej części tego samouczka.

::: moniker range="<= aspnetcore-2.0"

## <a name="add-dotnet-watch-to-a-project"></a>Dodaj `dotnet watch` do projektu

Narzędzie `dotnet watch` obserwator plików jest dołączone do wersji 2.1.300 zestaw .NET Core SDK. Poniższe kroki są wymagane w przypadku korzystania ze starszej wersji zestaw .NET Core SDK.

1. Dodaj odwołanie `Microsoft.DotNet.Watcher.Tools` do pakietu do pliku *csproj* :

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

## <a name="run-net-core-cli-commands-using-dotnet-watch"></a>Uruchamianie poleceń interfejs wiersza polecenia platformy .NET Core przy użyciu`dotnet watch`

Każde [polecenie interfejs wiersza polecenia platformy .NET Core](/dotnet/core/tools#cli-commands) można uruchomić za pomocą `dotnet watch`polecenia. Przykład:

| Polecenie | Polecenie z zegarkiem |
| ---- | ----- |
| dotnet run | uruchomienie czujka dotnet |
| uruchomienie dotnet-f netcoreapp 2.0 | uruchomienie czujka dotnet-f netcoreapp 2.0 |
| uruchomienie dotnet-f netcoreapp 2.0----arg1 | uruchomienie czujka dotnet-f netcoreapp 2.0----arg1 |
| dotnet test | Test czujki dotnet |

Uruchom `dotnet watch run` w folderze *webapp* . Dane wyjściowe konsoli wskazują `watch` , że uruchomiono.

> [!NOTE]
> Możesz użyć `dotnet watch --project <PROJECT>` , aby określić projekt do obejrzenia. Na przykład uruchomienie `dotnet watch --project WebApp run` z poziomu głównego przykładowej aplikacji również uruchomi się i obejrzyj projekt *webapp* .

## <a name="make-changes-with-dotnet-watch"></a>Wprowadzanie zmian`dotnet watch`

Upewnij się `dotnet watch` , że jest uruchomiony program.

Usuń usterkę w `Product` metodzie *MathController.cs* , aby zwracała produkt, a nie sumę:

```csharp
public static int Product(int a, int b)
{
    return a * b;
}
```

Zapisz plik. Dane wyjściowe konsoli wskazują, `dotnet watch` że wykryto zmianę pliku i ponownie uruchom aplikację.

Funkcja `http://localhost:<port number>/api/math/product?a=4&b=5` verify zwraca prawidłowy wynik.

## <a name="run-tests-using-dotnet-watch"></a>Uruchom testy przy użyciu`dotnet watch`

1. Zmień `Product` metodę *MathController.cs* z powrotem, aby zwracać sumę. Zapisz plik.
1. W powłoce poleceń przejdź do folderu *WebAppTests* .
1. Uruchom [dotnet Restore](/dotnet/core/tools/dotnet-restore).
1. Uruchom polecenie `dotnet watch test`. Dane wyjściowe wskazują, że test zakończył się niepowodzeniem i że obserwator oczekuje na zmiany plików:

     ```console
     Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
     Test Run Failed.
     ```

1. Popraw kod `Product` metody, aby zwracał produkt. Zapisz plik.

`dotnet watch`wykrywa zmianę pliku i ponownie uruchamia testy. Dane wyjściowe konsoli wskazują zakończono testy.

## <a name="customize-files-list-to-watch"></a>Dostosuj listę plików do czujki

Domyślnie program `dotnet-watch` śledzi wszystkie pliki zgodne z następującymi wzorcami globalizowania:

* `**/*.cs`
* `*.csproj`
* `**/*.resx`

Więcej elementów można dodać do listy obserwacje, edytując plik *csproj* . Elementy można określać pojedynczo lub przy użyciu wzorców globalizowania.

```xml
<ItemGroup>
    <!-- extends watching group to include *.js files -->
    <Watch Include="**\*.js" Exclude="node_modules\**\*;**\*.js.map;obj\**\*;bin\**\*" />
</ItemGroup>
```

## <a name="opt-out-of-files-to-be-watched"></a>Rezygnacja z plików, które mają być obserwowane

`dotnet-watch`można skonfigurować do ignorowania ustawień domyślnych. Aby zignorować określone pliki, Dodaj `Watch="false"` atrybut do definicji elementu w pliku *. csproj* :

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

## <a name="custom-watch-projects"></a>Niestandardowe projekty czujki

`dotnet-watch`nie jest ograniczone do projektów języka C#. Niestandardowe projekty czujki mogą być tworzone w celu obsługi różnych scenariuszy. Rozważmy następujący układ projektu:

* **badan**
  * *UnitTests/UnitTests. csproj*
  * *IntegrationTests/IntegrationTests. csproj*

Jeśli celem jest oglądanie obu projektów, Utwórz niestandardowy plik projektu, który został skonfigurowany do oglądania obu projektów:

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

Aby rozpocząć obserwowanie plików w obu projektach, przejdź do folderu *testowego* . Wykonaj następujące polecenie:

```dotnetcli
dotnet watch msbuild /t:Test
```

VSTest wykonuje się, gdy jakiekolwiek zmiany pliku w każdym projekcie testowym.

## <a name="dotnet-watch-in-github"></a>`dotnet-watch`w witrynie GitHub

`dotnet-watch`jest częścią [repozytorium dotnet/AspNetCore](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch)usługi GitHub.
