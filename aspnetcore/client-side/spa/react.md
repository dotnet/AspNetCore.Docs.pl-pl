---
title: Użyj szablonu projektu React z ASP.NET Core
author: SteveSandersonMS
description: Dowiedz się, jak rozpocząć pracę z szablonem projektu ASP.NET Core Single Page Application (SPA) dla aplikacji React i create-react-app.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 03/07/2019
uid: spa/react
ms.openlocfilehash: 9703a62eb7f779974382fe0fb01702d9fcd37d64
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664963"
---
# <a name="use-the-react-project-template-with-aspnet-core"></a>Użyj szablonu projektu React z ASP.NET Core

Zaktualizowany szablon projektu React zapewnia wygodny punkt wyjścia dla aplikacji ASP.NET Core przy użyciu konwencji React i create-react-app (CRA) w celu zaimplementowania bogatego interfejsu użytkownika po stronie klienta.The updated React project template provides a convenient starting point for ASP.NET Core apps using React and [create-react-app](https://github.com/facebookincubator/create-react-app) (CRA) conventions to implement a rich, client-side user interface (UI).

Szablon jest odpowiednikiem tworzenia zarówno projektu ASP.NET Core do działania jako zaplecze interfejsu API, jak i standardowego projektu CRA React, aby działać jako interfejs użytkownika, ale z wygodą hostingu zarówno w jednym projekcie aplikacji, który może być zbudowany i opublikowany jako pojedyncza jednostka.

Szablon projektu React nie jest przeznaczony do renderowania po stronie serwera (SSR). Dla SSR z React i Node.js, należy wziąć pod uwagę [Next.js](https://github.com/zeit/next.js/) lub [Razzle](https://github.com/jaredpalmer/razzle).

## <a name="create-a-new-app"></a>Tworzenie nowej aplikacji

Jeśli masz ASP.NET zainstalowanym core 2.1, nie ma potrzeby instalowania szablonu projektu React.

Utwórz nowy projekt z wiersza `dotnet new react` polecenia za pomocą polecenia w pustym katalogu. Na przykład następujące polecenia tworzą aplikację w katalogu *my-new-app* i przełączają się do tego katalogu:

```dotnetcli
dotnet new react -o my-new-app
cd my-new-app
```

Uruchom aplikację z programu Visual Studio lub interfejsu wiersza polecenia .NET Core:

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio)

Otwórz wygenerowany plik *csproj* i uruchom aplikację normalnie stamtąd.

Proces kompilacji przywraca zależności npm przy pierwszym uruchomieniu, co może potrwać kilka minut. Kolejne kompilacje są znacznie szybsze.

# <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/netcore-cli)

Upewnij się, że `ASPNETCORE_Environment` masz zmienną środowiskową o nazwie o wartości `Development`. W systemie Windows (w monitach innych `SET ASPNETCORE_Environment=Development`niż powershell) uruchom program . W systemie Linux lub `export ASPNETCORE_Environment=Development`macOS uruchom plik .

Uruchom [kompilację dotnet,](/dotnet/core/tools/dotnet-build) aby poprawnie zweryfikować kompilacje aplikacji. Przy pierwszym uruchomieniu proces kompilacji przywraca zależności npm, co może potrwać kilka minut. Kolejne kompilacje są znacznie szybsze.

Uruchom [dotnet uruchom,](/dotnet/core/tools/dotnet-run) aby uruchomić aplikację.

---

Szablon projektu tworzy aplikację ASP.NET Core i aplikację React. Aplikacja ASP.NET Core jest przeznaczona do użycia w celu uzyskania dostępu do danych, autoryzacji i innych problemów po stronie serwera. Aplikacja React, zamieszkała w podkatalogu *ClientApp,* jest przeznaczona do użycia dla wszystkich problemów z interfejsem użytkownika.

## <a name="add-pages-images-styles-modules-etc"></a>Dodaj strony, obrazy, style, moduły itp.

Katalog *ClientApp* jest standardową aplikacją CRA React. Więcej informacji można znaleźć w oficjalnej [dokumentacji CRA.](https://create-react-app.dev/docs/getting-started/)

Istnieją niewielkie różnice między aplikacją React utworzoną przez ten szablon a aplikacją stworzoną przez samą cra; jednak możliwości aplikacji pozostają niezmienione. Aplikacja utworzona przez szablon zawiera układ oparty na [bootstrap](https://getbootstrap.com/)i przykład podstawowego routingu.

## <a name="install-npm-packages"></a>Instalowanie pakietów npm

Aby zainstalować pakiety npm innych firm, użyj wiersza polecenia w podkatalogu *ClientApp.* Przykład:

```console
cd ClientApp
npm install --save <package_name>
```

## <a name="publish-and-deploy"></a>Publikowanie i wdrażanie

W rozwoju aplikacja działa w trybie zoptymalizowanym pod kątem wygody dewelopera. Na przykład pakiety JavaScript zawierają mapy źródłowe (dzięki czemu podczas debugowania można zobaczyć oryginalny kod źródłowy). Aplikacja obserwuje zmiany plików JavaScript, HTML i CSS na dysku i automatycznie ponownie kompiluje i ładuje się, gdy widzi, że te pliki się zmieniają.

W produkcji wyświetlaj wersję aplikacji zoptymalizowaną pod kątem wydajności. Jest to skonfigurowane tak, aby odbywało się automatycznie. Podczas publikowania konfiguracji kompilacji emituje wbudowane, transpiled kompilacji kodu po stronie klienta. W przeciwieństwie do kompilacji deweloperów kompilacja produkcyjna nie wymaga instalowania node.js na serwerze.

Można użyć standardowych [ASP.NET metody hostingu i wdrażania Core](xref:host-and-deploy/index).

## <a name="run-the-cra-server-independently"></a>Uruchamianie serwera CRA niezależnie

Projekt jest skonfigurowany do uruchamiania własnego wystąpienia serwera dewelopera CRA w tle, gdy aplikacja ASP.NET Core zostanie uruchomiony w trybie programowania. Jest to wygodne, ponieważ oznacza to, że nie trzeba ręcznie uruchamiać oddzielnego serwera.

Istnieje wada tej domyślnej konfiguracji. Za każdym razem, gdy modyfikujesz kod C#, a aplikacja ASP.NET Core musi zostać ponownie uruchomiona, serwer CRA zostanie ponownie uruchomiony. Do uruchomienia kopii zapasowej wymagane jest kilka sekund. Jeśli wprowadzasz częste zmiany kodu języka C# i nie chcesz czekać na ponowne uruchomienie serwera CRA, uruchom serwer USŁUGI CRA zewnętrznie, niezależnie od procesu ASP.NET Core. W tym celu:

1. Dodaj plik *.env* do podkatalogu *ClientApp* z następującym ustawieniem:

    ```
    BROWSER=none
    ```

    Zapobiegnie to otwarciu przeglądarki internetowej podczas uruchamiania serwera CRA na zewnątrz.

2. W wierszu polecenia przełącz się do podkatalogu *ClientApp* i uruchom serwer deweloperski USŁUGI CRA:

    ```console
    cd ClientApp
    npm start
    ```

3. Zmodyfikuj aplikację ASP.NET Core, aby użyć zewnętrznego wystąpienia serwera CRA zamiast uruchamiać własne. W klasie *uruchamiania* `spa.UseReactDevelopmentServer` zastąp wywołanie następującymi:

    ```csharp
    spa.UseProxyToSpaDevelopmentServer("http://localhost:3000");
    ```

Po uruchomieniu aplikacji ASP.NET Core nie uruchomi ona serwera CRA. Zamiast tego używane jest wystąpienie uruchomione ręcznie. Dzięki temu można go uruchomić i uruchomić szybciej. Nie czeka już na odbudowanie aplikacji React za każdym razem.

> [!IMPORTANT]
> "Renderowanie po stronie serwera" nie jest obsługiwaną funkcją tego szablonu. Naszym celem w tym szablonie jest spełnienie parytetu z "create-react-app". W związku z tym scenariusze i funkcje, które nie zostały uwzględnione w projekcie "create-react-app" (takim jak SSR) nie są obsługiwane i pozostają jako ćwiczenie dla użytkownika.

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:security/authentication/identity/spa>
