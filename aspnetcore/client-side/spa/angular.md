---
title: Użyj szablonu projektu kątowego z ASP.NET Core
author: SteveSandersonMS
description: Dowiedz się, jak rozpocząć pracę z szablonem projektu ASP.NET Core Single Page Application (SPA) dla angular i angular CLI.
monikerRange: '>= aspnetcore-2.1'
ms.author: stevesa
ms.custom: mvc
ms.date: 02/06/2020
uid: spa/angular
ms.openlocfilehash: fee872ff237e14cbe491efed9b320809df4c5654
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657634"
---
# <a name="use-the-angular-project-template-with-aspnet-core"></a>Użyj szablonu projektu kątowego z ASP.NET Core

Zaktualizowany szablon projektu Angular zapewnia wygodny punkt wyjścia dla aplikacji core ASP.NET przy użyciu Angular i Angular CLI do zaimplementowania bogatego interfejsu użytkownika po stronie klienta (UI).

Szablon jest odpowiednikiem tworzenia projektu ASP.NET Core do działania jako zaplecza interfejsu API i angular cli projektu do działania jako interfejsu użytkownika. Szablon oferuje wygodę hostingu obu typów projektów w jednym projekcie aplikacji. W związku z tym projekt aplikacji mogą być tworzone i publikowane jako pojedyncza jednostka.

## <a name="create-a-new-app"></a>Tworzenie nowej aplikacji

Jeśli masz zainstalowany ASP.NET Core 2.1, nie ma potrzeby instalowania szablonu projektu angular.

Utwórz nowy projekt z wiersza `dotnet new angular` polecenia za pomocą polecenia w pustym katalogu. Na przykład następujące polecenia tworzą aplikację w katalogu *my-new-app* i przełączają się do tego katalogu:

```dotnetcli
dotnet new angular -o my-new-app
cd my-new-app
```

Uruchom aplikację z programu Visual Studio lub interfejsu wiersza polecenia .NET Core:

# <a name="visual-studio"></a>[Program Visual Studio](#tab/visual-studio/)

Otwórz wygenerowany plik *csproj* i uruchom aplikację normalnie stamtąd.

Proces kompilacji przywraca zależności npm przy pierwszym uruchomieniu, co może potrwać kilka minut. Kolejne kompilacje są znacznie szybsze.

# <a name="net-core-cli"></a>[Interfejs wiersza polecenia platformy .NET Core](#tab/netcore-cli/)

Upewnij się, że `ASPNETCORE_Environment` masz zmienną `Development`środowiskową o nazwie o wartości . W systemie Windows (w monitach innych `SET ASPNETCORE_Environment=Development`niż powershell) uruchom program . W systemie Linux lub `export ASPNETCORE_Environment=Development`macOS uruchom plik .

Uruchom [kompilację dotnet,](/dotnet/core/tools/dotnet-build) aby poprawnie sprawdzić, czy aplikacja zostanie poprawnie zbudowana. Przy pierwszym uruchomieniu proces kompilacji przywraca zależności npm, co może potrwać kilka minut. Kolejne kompilacje są znacznie szybsze.

Uruchom [dotnet uruchom,](/dotnet/core/tools/dotnet-run) aby uruchomić aplikację. Rejestrowany jest komunikat podobny do następującego:

```console
Now listening on: http://localhost:<port>
```

Przejdź do tego adresu URL w przeglądarce.

> [!WARNING]
> Aplikacja uruchamia wystąpienie angular serwera interfejsu wiersza polecenia w tle. Rejestrowany jest komunikat podobny do następującego: *NG Live Development&lt;Server&gt;nasłuchuje na http://localhost:&ltlocalhost: otherport , otwórz przeglądarkę na ;otherport&gt;*. Zignoruj ten komunikat,&mdash;nie jest to adres URL połączonej aplikacji ASP.NET Core i Angular CLI. **not**

---

Szablon projektu tworzy aplikację ASP.NET Core i aplikację Angular. Aplikacja ASP.NET Core jest przeznaczona do użycia w celu uzyskania dostępu do danych, autoryzacji i innych problemów po stronie serwera. Aplikacja Angular, zamieszkała w podkatalogu *ClientApp,* jest przeznaczona do użycia dla wszystkich problemów interfejsu użytkownika.

## <a name="add-pages-images-styles-modules-etc"></a>Dodaj strony, obrazy, style, moduły itp.

Katalog *ClientApp* zawiera standardową aplikację Angular CLI. Więcej informacji można znaleźć w oficjalnej [dokumentacji kątowej.](https://angular.io)

Istnieją niewielkie różnice między aplikacją Angular stworzoną przez ten szablon a `ng new`aplikacją stworzoną przez samą interfejsu angular CLI (za pośrednictwem ); jednak możliwości aplikacji pozostają niezmienione. Aplikacja utworzona przez szablon zawiera układ oparty na [bootstrap](https://getbootstrap.com/)i przykład podstawowego routingu.

## <a name="run-ng-commands"></a>Uruchamianie poleceń ng

W wierszu polecenia przełącz się do podkatalogu *ClientApp:*

```console
cd ClientApp
```

Jeśli `ng` narzędzie jest zainstalowane globalnie, można uruchomić dowolne z jego poleceń. Na przykład można `ng lint`uruchomić `ng test`, lub dowolne inne [polecenia kątowego interfejsu wiersza polecenia](https://angular.io/cli). Nie ma jednak potrzeby `ng serve` uruchamiania, ponieważ aplikacja ASP.NET Core zajmuje się obsługiwania zarówno części aplikacji po stronie serwera, jak i klienta. Wewnętrznie, używa `ng serve` w rozwoju.

Jeśli nie masz zainstalowanego `ng` narzędzia, `npm run ng` uruchom zamiast tego. Na przykład można `npm run ng lint` uruchomić `npm run ng test`lub .

## <a name="install-npm-packages"></a>Instalowanie pakietów npm

Aby zainstalować pakiety npm innych firm, użyj wiersza polecenia w podkatalogu *ClientApp.* Przykład:

```console
cd ClientApp
npm install --save <package_name>
```

## <a name="publish-and-deploy"></a>Publikowanie i wdrażanie

W rozwoju aplikacja działa w trybie zoptymalizowanym pod kątem wygody dewelopera. Na przykład pakiety JavaScript zawierają mapy źródłowe (dzięki czemu podczas debugowania można zobaczyć oryginalny kod TypeScript). Aplikacja obserwuje zmiany plików TypeScript, HTML i CSS na dysku i automatycznie ponownie kompiluje i ładuje się, gdy widzi, że te pliki się zmieniają.

W produkcji wyświetlaj wersję aplikacji zoptymalizowaną pod kątem wydajności. Jest to skonfigurowane tak, aby odbywało się automatycznie. Podczas publikowania konfiguracji kompilacji emituje wstępnie, z wyprzedzeniem (AoT) skompilowany kompilacji kodu po stronie klienta. W przeciwieństwie do kompilacji deweloperskiej kompilacja produkcyjna nie wymaga instalowania node.js na serwerze (chyba że włączono renderowanie po stronie serwera (SSR)).

Można użyć standardowych [ASP.NET metody hostingu i wdrażania Core](xref:host-and-deploy/index).

## <a name="run-ng-serve-independently"></a>Uruchom "ng służyć" niezależnie

Projekt jest skonfigurowany do uruchamiania własnego wystąpienia angular serwera interfejsu wiersza polecenia w tle, gdy aplikacja ASP.NET Core uruchamia się w trybie rozwoju. Jest to wygodne, ponieważ nie trzeba ręcznie uruchamiać oddzielnego serwera.

Istnieje wada tej domyślnej konfiguracji. Za każdym razem, gdy modyfikujesz kod języka C#, a aplikacja ASP.NET Core musi zostać ponownie uruchomiona, serwer angular CLI zostanie ponownie uruchomiony. Do uruchomienia kopii zapasowej wymagane jest około 10 sekund. Jeśli wprowadzasz częste zmiany kodu języka C# i nie chcesz czekać na ponowne uruchomienie interfejsu wiersza polecenia angular, uruchom serwer Angular CLI zewnętrznie, niezależnie od procesu ASP.NET Core. W tym celu:

1. W wierszu polecenia przełącz się do podkatalogu *ClientApp* i uruchom serwer programistyczny interfejsu wiersza polecenia Angular:

    ```console
    cd ClientApp
    npm start
    ```

    > [!IMPORTANT]
    > Służy `npm start` do uruchamiania serwera programistycznego `ng serve`interfejsu wiersza polecenia, a nie , tak aby konfiguracja w *pliku package.json* była przestrzegana. Aby przekazać dodatkowe parametry do serwera Angular CLI, dodaj je do odpowiedniego `scripts` wiersza w pliku *package.json.*

2. Zmodyfikuj aplikację ASP.NET Core, aby użyć zewnętrznego wystąpienia interfejsu wiersza polecenia angular zamiast uruchamiania własnego. W klasie *uruchamiania* `spa.UseAngularCliServer` zastąp wywołanie następującymi:

    ```csharp
    spa.UseProxyToSpaDevelopmentServer("http://localhost:4200");
    ```

Po uruchomieniu aplikacji ASP.NET Core nie uruchomi ona kanciastego serwera interfejsu wiersza polecenia. Zamiast tego używane jest wystąpienie uruchomione ręcznie. Dzięki temu można go uruchomić i uruchomić szybciej. Nie czeka już na Angular CLI odbudować aplikację kliencką za każdym razem.

### <a name="pass-data-from-net-code-into-typescript-code"></a>Przekazywanie danych z kodu .NET do kodu TypeScript

Podczas SSR możesz chcieć przekazać dane na żądanie z aplikacji ASP.NET Core do aplikacji Angular. Na przykład można przekazać informacje o plikach cookie lub coś odczytanego z bazy danych. Aby to zrobić, edytuj klasę *uruchamiania.* W wywołaniu `UseSpaPrerendering`zwrotnym dla `options.SupplyData` , ustaw wartość dla takich jak:

```csharp
options.SupplyData = (context, data) =>
{
    // Creates a new value called isHttpsRequest that's passed to TypeScript code
    data["isHttpsRequest"] = context.Request.IsHttps;
};
```

Wywołanie zwrotne `SupplyData` umożliwia przekazywanie dowolnych danych na żądanie, serializable JSON (na przykład ciągów, wartości logicznych lub liczb). Kod *main.server.ts* odbiera `params.data`to jako . Na przykład poprzedni przykładowy kod przekazuje wartość `params.data.isHttpsRequest` logiczną jako wywołanie zwrotne. `createServerRenderer` Możesz przekazać to do innych części aplikacji w dowolny sposób obsługiwany przez Angular. Na przykład zobacz, jak *main.server.ts* przekazuje `BASE_URL` wartość do dowolnego składnika, którego konstruktor jest zadeklarowany do jego odbioru.

### <a name="drawbacks-of-ssr"></a>Wady SSR

Nie wszystkie aplikacje korzystają z SSR. Główną korzyścią jest postrzegana wydajność. Osoby odwiedzające, które docierają do aplikacji za pośrednictwem wolnego połączenia sieciowego lub na wolnych urządzeniach przenośnych, szybko widzą początkowy interfejs użytkownika, nawet jeśli pobieranie lub analizowania pakietów JavaScript zajmuje trochę czasu. Jednak wiele OSO są używane głównie przez szybkie, wewnętrzne sieci firmowe na szybkich komputerach, gdzie aplikacja pojawia się niemal natychmiast.

Jednocześnie istnieją znaczne wady włączenia SSR. Zwiększa złożoność procesu tworzenia. Kod musi działać w dwóch różnych środowiskach: po stronie klienta i po stronie serwera (w środowisku Node.js wywoływanym z ASP.NET Core). Oto kilka rzeczy, o których należy pamiętać:

* SSR wymaga instalacji Node.js na serwerach produkcyjnych. Jest to automatycznie w przypadku niektórych scenariuszy wdrażania, takich jak usługi azure app services, ale nie dla innych, takich jak sieci szkieletowej usługi Azure.
* Włączenie flagi `BuildServerSideRenderer` kompilacji *powoduje,* że katalog node_modules do opublikowania. Ten folder zawiera ponad 20 000 plików, co wydłuża czas wdrażania.
* Aby uruchomić kod w środowisku Node.js, nie może polegać na istnieniu interfejsów `window` `localStorage`API javascript specyficznych dla przeglądarki, takich jak lub . Jeśli kod (lub niektóre biblioteki innych firm, do których się odwołujesz) próbuje użyć tych interfejsów API, otrzymasz błąd podczas SSR. Na przykład nie należy używać jQuery, ponieważ odwołuje się do interfejsów API specyficznych dla przeglądarki w wielu miejscach. Aby zapobiec błędom, należy unikać SSR lub unikać interfejsów API lub bibliotek specyficznych dla przeglądarki. Można zawinąć wszelkie wywołania do takich interfejsów API w kontroli, aby upewnić się, że nie są wywoływane podczas SSR. Na przykład użyj czeku, takiego jak poniższy w kodzie JavaScript lub TypeScript:

    ```javascript
    if (typeof window !== 'undefined') {
        // Call browser-specific APIs here
    }
    ```

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:security/authentication/identity/spa>
