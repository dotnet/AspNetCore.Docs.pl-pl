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
# <a name="develop-aspnet-core-apps-using-a-file-watcher"></a>Opracowywanie aplikacji ASP.NET Core przy użyciu obserwatora plików

Autorzy [Rick Anderson](https://twitter.com/RickAndMSFT) i [Victor Hurdugaci](https://twitter.com/victorhurdugaci)

`dotnet watch` jest narzędziem, które uruchamia [interfejs wiersza polecenia platformy .NET Core](/dotnet/core/tools) polecenie, gdy pliki źródłowe zmieniają się. Na przykład zmiana pliku może wyzwolić kompilację, wykonanie testu lub wdrożenie.

W tym samouczku jest wykorzystywany istniejący internetowy interfejs API z dwoma punktami końcowymi: jeden, który zwraca sumę i jeden z nich zwraca produkt. Metoda produktu zawiera usterkę, która została rozwiązana w tym samouczku.

Pobierz [przykładową aplikację](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample). Składa się z dwóch projektów: *webapp* (ASP.NET Core Web API) i *WebAppTests* (testy jednostkowe dla internetowego interfejsu API).

W powłoce poleceń przejdź do folderu *webapp* . Uruchom następujące polecenie:

```dotnetcli
dotnet run
```

> [!NOTE]
> Możesz użyć, `dotnet run --project <PROJECT>` Aby określić projekt do uruchomienia. Na przykład uruchomienie `dotnet run --project WebApp` z poziomu głównego przykładowej aplikacji spowoduje również uruchomienie projektu *webapp* .

W danych wyjściowych konsoli są wyświetlane komunikaty podobne do następujących (wskazuje, że aplikacja działa i oczekuje na żądania):

```console
$ dotnet run
Hosting environment: Development
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

W przeglądarce internetowej przejdź do adresu `http://localhost:<port number>/api/math/sum?a=4&b=5`. Powinien zostać wyświetlony wynik `9` .

Przejdź do interfejsu API produktu ( `http://localhost:<port number>/api/math/product?a=4&b=5` ). Zwraca wartość `9` , `20` a nie zgodnie z oczekiwaniami. Ten problem został rozwiązany w dalszej części tego samouczka.

::: moniker range="<= aspnetcore-2.0"

## <a name="add-dotnet-watch-to-a-project"></a>Dodaj `dotnet watch` do projektu

`dotnet watch`Narzędzie obserwator plików jest dołączone do wersji 2.1.300 zestaw .NET Core SDK. Poniższe kroki są wymagane w przypadku korzystania ze starszej wersji zestaw .NET Core SDK.

1. Dodaj `Microsoft.DotNet.Watcher.Tools` odwołanie do pakietu do pliku *csproj* :

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

## <a name="run-net-core-cli-commands-using-dotnet-watch"></a>Uruchamianie poleceń interfejs wiersza polecenia platformy .NET Core przy użyciu `dotnet watch`

Każde [polecenie interfejs wiersza polecenia platformy .NET Core](/dotnet/core/tools#cli-commands) można uruchomić za pomocą polecenia `dotnet watch` . Przykład:

| Polecenie | Polecenie z zegarkiem |
| ---- | ----- |
| dotnet run | uruchomienie czujka dotnet |
| uruchomienie dotnet-f netcoreapp 3.1 | uruchomienie czujka dotnet-f netcoreapp 3.1 |
| uruchomienie dotnet-f netcoreapp 3.1----arg1 | uruchomienie czujka dotnet-f netcoreapp 3.1----arg1 |
| dotnet test | Test czujki dotnet |

Uruchom `dotnet watch run` w folderze *webapp* . Dane wyjściowe konsoli wskazują, że `watch` uruchomiono.

::: moniker range=">= aspnetcore-5.0"
Uruchomienie `dotnet watch run` w aplikacji sieci Web powoduje uruchomienie przeglądarki, która przechodzi do adresu URL aplikacji po jej przygotowaniu. `dotnet watch` w tym celu należy odczytać dane wyjściowe konsoli aplikacji i oczekiwanie na komunikat o gotowości wyświetlany przez <xref:Microsoft.AspNetCore.WebHost> .

`dotnet watch` Odświeża przeglądarkę po wykryciu zmian w oglądanych plikach. W tym celu polecenie czujki wstawia oprogramowanie pośredniczące do aplikacji, która modyfikuje odpowiedzi HTML utworzone przez aplikację. Oprogramowanie pośredniczące dodaje blok skryptu JavaScript do strony, która umożliwia `dotnet watch` poinstruujenie przeglądarki w celu odświeżenia. Obecnie zmiany wszystkich obserwowanych plików, w tym zawartości statycznej, takiej jak pliki *. html* i *. css* , powodują ponowne skompilowanie aplikacji.

`dotnet watch`:

* Program domyślnie Obserwujący pliki, które mają wpływ na kompilacje.
* Wszystkie dodatkowo obserwowane pliki (za pośrednictwem konfiguracji) nadal będą miały miejsce w kompilacji.

Aby uzyskać więcej informacji na temat konfiguracji, zobacz [Konfiguracja dotnet-Obejrzyj konfigurację](#dotnet-watch-configuration) w tym dokumencie

::: moniker-end

> [!NOTE]
> Możesz użyć, `dotnet watch --project <PROJECT>` Aby określić projekt do obejrzenia. Na przykład uruchomienie `dotnet watch --project WebApp run` z poziomu głównego przykładowej aplikacji również uruchomi się i obejrzyj projekt *webapp* .

## <a name="make-changes-with-dotnet-watch"></a>Wprowadzanie zmian `dotnet watch`

Upewnij się, że `dotnet watch` jest uruchomiony program.

Usuń usterkę w `Product` metodzie *MathController.cs* , aby zwracała produkt, a nie sumę:

```csharp
public static int Product(int a, int b)
{
    return a * b;
}
```

Zapisz plik. Dane wyjściowe konsoli wskazują, że `dotnet watch` wykryto zmianę pliku i ponownie uruchom aplikację.

`http://localhost:<port number>/api/math/product?a=4&b=5`Funkcja verify zwraca prawidłowy wynik.

## <a name="run-tests-using-dotnet-watch"></a>Uruchom testy przy użyciu `dotnet watch`

1. Zmień `Product` metodę *MathController.cs* z powrotem, aby zwracać sumę. Zapisz plik.
1. W powłoce poleceń przejdź do folderu *WebAppTests* .
1. Uruchom [dotnet Restore](/dotnet/core/tools/dotnet-restore).
1. Uruchom polecenie `dotnet watch test`. Dane wyjściowe wskazują, że test zakończył się niepowodzeniem i że obserwator oczekuje na zmiany plików:

     ```console
     Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
     Test Run Failed.
     ```

1. Popraw `Product` Kod metody, aby zwracał produkt. Zapisz plik.

`dotnet watch` wykrywa zmianę pliku i ponownie uruchamia testy. Dane wyjściowe konsoli wskazują zakończono testy.

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

`dotnet-watch` można skonfigurować do ignorowania ustawień domyślnych. Aby zignorować określone pliki, Dodaj `Watch="false"` atrybut do definicji elementu w pliku *. csproj* :

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

`dotnet-watch` nie jest ograniczone do projektów języka C#. Niestandardowe projekty czujki mogą być tworzone w celu obsługi różnych scenariuszy. Rozważmy następujący układ projektu:

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

Aby rozpocząć obserwowanie plików w obu projektach, przejdź do folderu *testowego* . Uruchom następujące polecenie:

```dotnetcli
dotnet watch msbuild /t:Test
```

VSTest wykonuje się, gdy jakiekolwiek zmiany pliku w każdym projekcie testowym.

## <a name="dotnet-watch-configuration"></a>Konfiguracja monitorowania dotnet

Niektóre opcje konfiguracji mogą być przesyłane `dotnet watch` przez zmienne środowiskowe. Dostępne są następujące zmienne:

| Ustawienie  | Opis |
| ------------- | ------------- |
| `DOTNET_USE_POLLING_FILE_WATCHER`                | W przypadku ustawienia wartości "1" lub "true" program `dotnet watch` używa obserwatora pliku sondowania zamiast CoreFx `FileSystemWatcher` . Używane podczas oglądania plików w udziałach sieciowych lub zainstalowanych woluminów platformy Docker.                       |
| `DOTNET_WATCH_SUPPRESS_MSBUILD_INCREMENTALISM`   | Domyślnie program `dotnet watch` optymalizuje kompilację, unikając niektórych operacji, takich jak uruchamianie przywracania lub ponowne ocenianie zestawu obserwowanych plików podczas każdej zmiany pliku. W przypadku ustawienia wartości "1" lub "true" te optymalizacje są wyłączone. |
| `DOTNET_WATCH_SUPPRESS_LAUNCH_BROWSER`   | `dotnet watch run` próbuje uruchomić przeglądarki dla aplikacji sieci Web `launchBrowser` skonfigurowanych w *launchSettings.jsna*. W przypadku ustawienia wartości "1" lub "prawda" to zachowanie jest pomijane. |
| `DOTNET_WATCH_SUPPRESS_BROWSER_REFRESH`   | `dotnet watch run` próbuje odświeżyć przeglądarki po wykryciu zmian plików. W przypadku ustawienia wartości "1" lub "prawda" to zachowanie jest pomijane. To zachowanie jest również pomijane, jeśli `DOTNET_WATCH_SUPPRESS_LAUNCH_BROWSER` jest ustawiona. |

## <a name="dotnet-watch-in-github"></a>`dotnet-watch` w witrynie GitHub

`dotnet-watch` jest częścią [repozytorium dotnet/AspNetCore](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch)usługi GitHub.
