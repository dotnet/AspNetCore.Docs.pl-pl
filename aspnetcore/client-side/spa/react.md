---
title: Użyj szablonu projektu reagowanie z ASP.NET Core
author: SteveSandersonMS
description: Dowiedz się, jak rozpocząć pracę z szablonem projektu aplikacji jednostronicowej (SPA) ASP.NET Core na potrzeby reakcji i tworzenia aplikacji.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 03/07/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: spa/react
ms.openlocfilehash: e9f3bbe2248d1c2fc9030745febb69177c301f7e
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88013128"
---
# <a name="use-the-react-project-template-with-aspnet-core"></a>Użyj szablonu projektu reagowanie z ASP.NET Core

Zaktualizowany szablon projektu dotyczącego reakcji umożliwia korzystanie z dogodnych punktów początkowych dla ASP.NET Core aplikacji przy użyciu konwencji reagowania i [tworzenia aplikacji](https://github.com/facebookincubator/create-react-app) (CRA) w celu zaimplementowania rozbudowanego interfejsu użytkownika po stronie klienta.

Szablon jest równoznaczny z tworzeniem projektu ASP.NET Core, który działa jako zaplecze interfejsu API, a standardowy projekt reakcji CRA reaguje na działanie jako interfejs użytkownika, ale z wygodą hostingu zarówno w jednym projekcie aplikacji, który można skompilować i opublikować jako pojedynczą jednostkę.

Szablon projektu reaguje nie jest przeznaczony do renderowania po stronie serwera (SSR). W przypadku programu SSR z reagowaniem na Node.js należy rozważyć [Next.js](https://github.com/zeit/next.js/) lub [Razzle](https://github.com/jaredpalmer/razzle).

## <a name="create-a-new-app"></a>Tworzenie nowej aplikacji

Jeśli zainstalowano ASP.NET Core 2,1, nie ma potrzeby instalowania szablonu projektu z reagowaniem.

Utwórz nowy projekt z wiersza polecenia przy użyciu polecenia `dotnet new react` w pustym katalogu. Na przykład następujące polecenia tworzą aplikację w katalogu *My-New-App* i przełączają się do tego katalogu:

```dotnetcli
dotnet new react -o my-new-app
cd my-new-app
```

Uruchom aplikację z poziomu programu Visual Studio lub interfejs wiersza polecenia platformy .NET Core:

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Otwórz wygenerowany plik *csproj* i uruchom aplikację w zwykły sposób.

Proces kompilacji przywraca zależności npm w pierwszym przebiegu, co może potrwać kilka minut. Kolejne kompilacje są znacznie szybsze.

# <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/netcore-cli)

Upewnij się, że masz zmienną środowiskową o nazwie `ASPNETCORE_Environment` z wartością `Development` . W systemie Windows (w komunikatach innych niż programu PowerShell) Uruchom polecenie `SET ASPNETCORE_Environment=Development` . W systemie Linux lub macOS Uruchom polecenie `export ASPNETCORE_Environment=Development` .

Uruchom [kompilację dotnet](/dotnet/core/tools/dotnet-build) , aby sprawdzić, czy aplikacja jest poprawnie skompilowana. W pierwszym uruchomieniu proces kompilacji przywraca zależności npm, co może potrwać kilka minut. Kolejne kompilacje są znacznie szybsze.

Uruchom [uruchomienie programu dotnet](/dotnet/core/tools/dotnet-run) , aby uruchomić aplikację.

---

Szablon projektu tworzy aplikację ASP.NET Core i aplikację do reagowania. Aplikacja ASP.NET Core jest przeznaczona do użycia na potrzeby dostępu do danych, autoryzacji i innych zagadnień po stronie serwera. Aplikacja do reagowania znajdująca się w podkatalogu *ClientApp* jest przeznaczona do użycia w przypadku wszystkich problemów z interfejsem użytkownika.

## <a name="add-pages-images-styles-modules-etc"></a>Dodawanie stron, obrazów, stylów, modułów itp.

Katalog *ClientApp* jest standardową aplikacją do reagowania w CRA. Aby uzyskać więcej informacji, zobacz oficjalną [dokumentację CRA](https://create-react-app.dev/docs/getting-started/) .

Istnieją niewielkie różnice między aplikacją reakcji utworzoną przez ten szablon a tym utworzonym przez CRA. jednak możliwości aplikacji nie są zmieniane. Aplikacja utworzona przez szablon zawiera układ oparty na [Bootstrap](https://getbootstrap.com/)i podstawowy przykład routingu.

## <a name="install-npm-packages"></a>Zainstaluj pakiety npm

Aby zainstalować pakiety npm innych firm, należy użyć wiersza polecenia w podkatalogu *ClientApp* . Przykład:

```console
cd ClientApp
npm install --save <package_name>
```

## <a name="publish-and-deploy"></a>Publikowanie i wdrażanie

W trakcie opracowywania aplikacja jest uruchamiana w trybie zoptymalizowanym pod kątem wygody dla deweloperów. Na przykład pakiety JavaScript zawierają mapy źródłowe (w przypadku debugowania można zobaczyć oryginalny kod źródłowy). Aplikacja obserwuje zmiany plików JavaScript, HTML i CSS na dysku, a następnie automatycznie ponownie kompiluje i ładuje je, gdy zobaczy zmiany tych plików.

W środowisku produkcyjnym można korzystać z wersji aplikacji zoptymalizowanej pod kątem wydajności. To jest skonfigurowane tak, aby były wykonywane automatycznie. Podczas publikowania, konfiguracja kompilacji emituje zminimalizowanego, przenoszącą wbudowaną kompilację kodu po stronie klienta. W przeciwieństwie do kompilacji deweloperskiej kompilacja produkcyjna nie wymaga zainstalowania Node.js na serwerze.

Można użyć standardowych [ASP.NET Core metod hostingu i wdrażania](xref:host-and-deploy/index).

## <a name="run-the-cra-server-independently"></a>Uruchom serwer CRA niezależnie

Projekt jest skonfigurowany tak, aby uruchamiał własne wystąpienie serwera CRA Development w tle podczas uruchamiania aplikacji ASP.NET Core w trybie tworzenia. Jest to wygodne, ponieważ oznacza to, że nie trzeba ręcznie uruchamiać oddzielnego serwera.

Istnieje zwrot do tej konfiguracji domyślnej. Za każdym razem, gdy modyfikujesz kod w języku C#, a aplikacja ASP.NET Core wymaga ponownego uruchomienia, serwer CRA zostanie uruchomiony ponownie. Aby rozpocząć tworzenie kopii zapasowej, wymagane są kilka sekund. Jeśli tworzysz częste edycje kodu w języku C# i nie chcesz czekać na ponowne uruchomienie serwera CRA, uruchom serwer CRA na zewnątrz niezależnie od procesu ASP.NET Core. W tym celu:

1. Dodaj plik *ENV* do podkatalogu *ClientApp* przy użyciu następującego ustawienia:

    ```
    BROWSER=none
    ```

    Uniemożliwi to otwieranie przeglądarki sieci Web podczas zewnętrznego uruchamiania serwera CRA.

2. W wierszu polecenia przejdź do podkatalogu *ClientApp* i uruchom serwer deweloperski CRA:

    ```console
    cd ClientApp
    npm start
    ```

3. Zmodyfikuj aplikację ASP.NET Core tak, aby korzystała z zewnętrznego wystąpienia serwera CRA, zamiast uruchamiania jednego z nich. W klasie *uruchomieniowej* Zastąp `spa.UseReactDevelopmentServer` wywołanie następującym:

    ```csharp
    spa.UseProxyToSpaDevelopmentServer("http://localhost:3000");
    ```

Po uruchomieniu aplikacji ASP.NET Core nie zostanie uruchomiony serwer CRA. Wystąpienie, które zostało uruchomione ręcznie, jest używane w zamian. Pozwala to na szybkie uruchamianie i ponowne uruchamianie. Nie czeka już na odbudowanie aplikacji z reagowaniem za każdym razem.

> [!IMPORTANT]
> "Renderowanie po stronie serwera" nie jest obsługiwaną funkcją tego szablonu. Naszym celem tego szablonu jest spełnienie warunków dotyczących "Tworzenie — reagowanie aplikacji". W związku z tym scenariusze i funkcje, które nie są uwzględnione w projekcie "Create-rereagować na aplikacje" (na przykład SSR), nie są obsługiwane i pozostają jako ćwiczenie dla użytkownika.

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:security/authentication/identity/spa>
