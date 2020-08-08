---
title: Testowanie interfejsów API sieci Web przy użyciu protokołu HTTP REPL
author: scottaddie
description: Dowiedz się, jak przeglądać i testować ASP.NET Core internetowy interfejs API przy użyciu globalnego narzędzia REPL .NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 05/20/2020
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
uid: web-api/http-repl
ms.openlocfilehash: 0fb19aa19703e68812b83f0631f029dd66a3d64e
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021331"
---
# <a name="test-web-apis-with-the-http-repl"></a>Testowanie interfejsów API sieci Web przy użyciu protokołu HTTP REPL

Przez [Scott Addie](https://twitter.com/Scott_Addie)

Pętla HTTP Read-eval-Print (REPL) to:

* Lekkie, międzyplatformowe narzędzie wiersza polecenia, które jest obsługiwane wszędzie tam, gdzie jest obsługiwane środowisko .NET Core.
* Służy do wykonywania żądań HTTP do testowania ASP.NET Core interfejsów API sieci Web (oraz non-ASP.NET podstawowych interfejsów API sieci Web) i wyświetlania ich wyników.
* Możliwość testowania interfejsów API sieci Web hostowanych w dowolnym środowisku, w tym hosta lokalnego i Azure App Service.

Obsługiwane są następujące [czasowniki http](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) :

* [USUNIĘTY](#test-http-delete-requests)
* [GET](#test-http-get-requests)
* [HEAD](#test-http-head-requests)
* [Opcje](#test-http-options-requests)
* [WYSŁANA](#test-http-patch-requests)
* [POST](#test-http-post-requests)
* [PUT](#test-http-put-requests)

Aby wykonać te czynności, [Wyświetl lub Pobierz przykładowy ASP.NET Core internetowy interfejs API](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([jak pobrać](xref:index#how-to-download-a-sample)).

## <a name="prerequisites"></a>Wymagania wstępne

* [!INCLUDE [2.1-SDK](~/includes/2.1-SDK.md)]

## <a name="installation"></a>Instalacja

Aby zainstalować REPL HTTP, uruchom następujące polecenie:

```dotnetcli
dotnet tool install -g Microsoft.dotnet-httprepl
```

[Narzędzie globalne platformy .NET Core](/dotnet/core/tools/global-tools#install-a-global-tool) jest instalowane z pakietu NuGet [Microsoft. dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) .

## <a name="usage"></a>Użycie

Po pomyślnej instalacji narzędzia Uruchom następujące polecenie, aby uruchomić REPL HTTP:

```console
httprepl
```

Aby wyświetlić dostępne polecenia HTTP REPL, Uruchom jedno z następujących poleceń:

```console
httprepl -h
```

```console
httprepl --help
```

Wyświetlane są następujące dane wyjściowe:

```console
Usage:
  httprepl [<BASE_ADDRESS>] [options]

Arguments:
  <BASE_ADDRESS> - The initial base address for the REPL.

Options:
  -h|--help - Show help information.

Once the REPL starts, these commands are valid:

Setup Commands:
Use these commands to configure the tool for your API server

connect        Configures the directory structure and base address of the api server
set header     Sets or clears a header for all requests. e.g. `set header content-type application/json`

HTTP Commands:
Use these commands to execute requests against your application.

GET            get - Issues a GET request
POST           post - Issues a POST request
PUT            put - Issues a PUT request
DELETE         delete - Issues a DELETE request
PATCH          patch - Issues a PATCH request
HEAD           head - Issues a HEAD request
OPTIONS        options - Issues a OPTIONS request

Navigation Commands:
The REPL allows you to navigate your URL space and focus on specific APIs that you are working on.

set base       Set the base URI. e.g. `set base http://locahost:5000`
ls             Show all endpoints for the current path
cd             Append the given directory to the currently selected path, or move up a path when using `cd ..`

Shell Commands:
Use these commands to interact with the REPL shell.

clear          Removes all text from the shell
echo [on/off]  Turns request echoing on or off, show the request that was made when using request commands
exit           Exit the shell

REPL Customization Commands:
Use these commands to customize the REPL behavior.

pref [get/set] Allows viewing or changing preferences, e.g. 'pref set editor.command.default 'C:\\Program Files\\Microsoft VS Code\\Code.exe'`
run            Runs the script at the given path. A script is a set of commands that can be typed with one command per line
ui             Displays the Swagger UI page, if available, in the default browser

Use `help <COMMAND>` for more detail on an individual command. e.g. `help get`.
For detailed tool info, see https://aka.ms/http-repl-doc.
```

REPL HTTP oferuje polecenie uzupełniania. Naciśnięcie klawisza <kbd>Tab</kbd> wykonuje iterację na liście poleceń, które uzupełniają wpisane znaki lub punkt końcowy interfejsu API. W poniższych sekcjach znajduje się opis dostępnych poleceń interfejsu wiersza polecenia.

## <a name="connect-to-the-web-api"></a>Nawiązywanie połączenia z interfejsem API sieci Web

Połącz się z interfejsem API sieci Web, uruchamiając następujące polecenie:

```console
httprepl <ROOT URI>
```

`<ROOT URI>`jest podstawowym identyfikatorem URI dla internetowego interfejsu API. Przykład:

```console
httprepl https://localhost:5001
```

Alternatywnie Uruchom następujące polecenie w dowolnym momencie podczas działania REPL HTTP:

```console
connect <ROOT URI>
```

Przykład:

```console
(Disconnected)~ connect https://localhost:5001
```

## <a name="manually-point-to-the-swagger-document-for-the-web-api"></a>Ręcznie wskaż dokument struktury Swagger dla internetowego interfejsu API

Powyższe polecenie Connect podejmie próbę automatycznego znalezienia dokumentu struktury Swagger. Jeśli z jakiegoś powodu nie można tego zrobić, możesz określić identyfikator URI dokumentu struktury Swagger dla internetowego interfejsu API przy użyciu `--swagger` opcji:

```console
connect <ROOT URI> --swagger <SWAGGER URI>
```

Przykład:

```console
(Disconnected)~ connect https://localhost:5001 --swagger /swagger/v1/swagger.json
```

## <a name="navigate-the-web-api"></a>Nawigowanie po interfejsie API sieci Web

### <a name="view-available-endpoints"></a>Wyświetl dostępne punkty końcowe

Aby wyświetlić listę różnych punktów końcowych (kontrolerów) znajdujących się w bieżącej ścieżce adresu internetowego interfejsu API, uruchom `ls` `dir` polecenie lub:

```console
https://localhot:5001/~ ls
```

Wyświetlany jest następujący format danych wyjściowych:

```console
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/~
```

Powyższe dane wyjściowe wskazują, że dostępne są dwa kontrolery: `Fruits` i `People` . Oba kontrolery obsługują bez parametrów operacje GET i POST HTTP.

Przechodzenie do określonego kontrolera ujawnia więcej szczegółów. Na przykład następujące dane wyjściowe polecenia pokazują `Fruits` kontroler obsługuje również operacje Get, PUT i DELETE protokołu HTTP. Każda z tych operacji oczekuje `id` parametru w marszrucie:

```console
https://localhost:5001/fruits~ ls
.      [get|post]
..     []
{id}   [get|put|delete]

https://localhost:5001/fruits~
```

Alternatywnie można uruchomić `ui` polecenie, aby otworzyć stronę interfejsu użytkownika programu Swagger interfejsu API sieci Web w przeglądarce. Przykład:

```console
https://localhost:5001/~ ui
```

### <a name="navigate-to-an-endpoint"></a>Przejdź do punktu końcowego

Aby przejść do innego punktu końcowego w internetowym interfejsie API, uruchom `cd` polecenie:

```console
https://localhost:5001/~ cd people
```

W ścieżce następującego `cd` polecenia jest rozróżniana wielkość liter. Wyświetlany jest następujący format danych wyjściowych:

```console
/people    [get|post]

https://localhost:5001/people~
```

## <a name="customize-the-http-repl"></a>Dostosowywanie REPL HTTP

Domyślne [kolory](#set-color-preferences) REPL http można dostosować. Ponadto można zdefiniować [domyślny edytor tekstu](#set-the-default-text-editor) . Preferencje HTTP REPL są utrwalane w bieżącej sesji i są honorowane w przyszłych sesjach. Po zmodyfikowaniu preferencje są przechowywane w następującym pliku:

# <a name="linux"></a>[Linux](#tab/linux)

*% HOME%/.httpreplprefs*

# <a name="macos"></a>[macOS](#tab/macos)

*% HOME%/.httpreplprefs*

# <a name="windows"></a>[Windows](#tab/windows)

*% USERPROFILE% \\ . httpreplprefs*

---

Plik *. httpreplprefs* jest ładowany podczas uruchamiania i nie jest monitorowany pod kątem zmian w czasie wykonywania. Ręczne modyfikacje pliku zaczną obowiązywać dopiero po ponownym uruchomieniu narzędzia.

### <a name="view-the-settings"></a>Wyświetl ustawienia

Aby wyświetlić dostępne ustawienia, uruchom `pref get` polecenie. Przykład:

```console
https://localhost:5001/~ pref get
```

Poprzednie polecenie wyświetla dostępne pary klucz-wartość:

```console
colors.json=Green
colors.json.arrayBrace=BoldCyan
colors.json.comma=BoldYellow
colors.json.name=BoldMagenta
colors.json.nameSeparator=BoldWhite
colors.json.objectBrace=Cyan
colors.protocol=BoldGreen
colors.status=BoldYellow
```

### <a name="set-color-preferences"></a>Ustawianie preferencji koloru

Kolorowanie odpowiedzi jest obecnie obsługiwane tylko w przypadku formatu JSON. Aby dostosować domyślne kolorowanie narzędzi HTTP REPL, Znajdź klucz odpowiadający kolorowi do zmiany. Aby uzyskać instrukcje dotyczące znajdowania kluczy, zobacz sekcję [Wyświetlanie ustawień](#view-the-settings) . Na przykład zmień `colors.json` wartość klucza z na w `Green` `White` następujący sposób:

```console
https://localhost:5001/people~ pref set colors.json White
```

Można używać tylko [dozwolonych kolorów](https://github.com/dotnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) . Kolejne żądania HTTP wyświetlają dane wyjściowe z nowym kolorem.

Jeśli określone klucze kolorów nie są ustawione, brane są więcej kluczy ogólnych. Aby zademonstrować to zachowanie rezerwowe, należy wziąć pod uwagę następujący przykład:

* Jeśli `colors.json.name` nie ma wartości, `colors.json.string` jest używana.
* Jeśli `colors.json.string` nie ma wartości, `colors.json.literal` jest używana.
* Jeśli `colors.json.literal` nie ma wartości, `colors.json` jest używana. 
* Jeśli `colors.json` nie ma wartości, używany jest domyślny kolor tekstu powłoki poleceń ( `AllowedColors.None` ).

### <a name="set-indentation-size"></a>Ustaw rozmiar wcięcia

Dostosowanie rozmiaru wcięcia odpowiedzi jest obecnie obsługiwane tylko w przypadku formatu JSON. Domyślny rozmiar to dwie spacje. Przykład:

```json
[
  {
    "id": 1,
    "name": "Apple"
  },
  {
    "id": 2,
    "name": "Orange"
  },
  {
    "id": 3,
    "name": "Strawberry"
  }
]
```

Aby zmienić rozmiar domyślny, ustaw `formatting.json.indentSize` klucz. Na przykład, aby zawsze używać czterech spacji:

```console
pref set formatting.json.indentSize 4
```

Kolejne odpowiedzi przestrzegają ustawień czterech spacji:

```json
[
    {
        "id": 1,
        "name": "Apple"
    },
    {
        "id": 2,
        "name": "Orange"
    },
    {
        "id": 3,
        "name": "Strawberry"
    }
]
```

### <a name="set-the-default-text-editor"></a>Ustawianie domyślnego edytora tekstu

Domyślnie REPL HTTP nie ma edytora tekstu skonfigurowanego do użycia. Aby przetestować metody interfejsu API sieci Web wymagające treści żądania HTTP, należy ustawić domyślny edytor tekstu. Narzędzie HTTP REPL uruchamia skonfigurowany Edytor tekstów wyłącznie na potrzeby redagowania treści żądania. Uruchom następujące polecenie, aby ustawić preferowany Edytor tekstu jako domyślny:

```console
pref set editor.command.default "<EXECUTABLE>"
```

W poprzednim poleceniu `<EXECUTABLE>` jest pełną ścieżką do pliku wykonywalnego edytora tekstu. Na przykład uruchom następujące polecenie, aby ustawić Visual Studio Code jako domyślny edytor tekstu:

# <a name="linux"></a>[Linux](#tab/linux)

```console
pref set editor.command.default "/usr/bin/code"
```

# <a name="macos"></a>[macOS](#tab/macos)

```console
pref set editor.command.default "/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code"
```

# <a name="windows"></a>[Windows](#tab/windows)

```console
pref set editor.command.default "C:\Program Files\Microsoft VS Code\Code.exe"
```

---

Aby uruchomić domyślny edytor tekstu z określonymi argumentami interfejsu wiersza polecenia, należy ustawić `editor.command.default.arguments` klucz. Załóżmy na przykład, że Visual Studio Code jest domyślnym edytorem tekstu i zawsze chcesz, aby REPL HTTP mógł otworzyć Visual Studio Code w nowej sesji z wyłączonymi rozszerzeniami. Uruchom następujące polecenie:

```console
pref set editor.command.default.arguments "--disable-extensions --new-window"
```

### <a name="set-the-swagger-search-paths"></a>Ustawianie ścieżek wyszukiwania struktury Swagger

Domyślnie REPL HTTP ma zestaw ścieżek względnych, których używa do znajdowania dokumentu struktury Swagger podczas wykonywania `connect` polecenia bez `--swagger` opcji. Ścieżki względne są łączone z ścieżkami głównymi i podstawowymi określonymi w `connect` poleceniu. Domyślne ścieżki względne to:

- *swagger.jsna*
- *Struktura Swagger/V1/swagger.jswłączona*
- */swagger.jsna*
- */Swagger/V1/swagger.jsna*

Aby użyć innego zestawu ścieżek wyszukiwania w środowisku, ustaw `swagger.searchPaths` preferencję. Wartość musi być rozdzielaną potokami listą ścieżek względnych. Przykład:

```console
pref set swagger.searchPaths "swagger/v2/swagger.json|swagger/v3/swagger.json"
```

## <a name="test-http-get-requests"></a>Testuj żądania HTTP GET

### <a name="synopsis"></a>Streszczenie

```console
get <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Argumenty

`PARAMETER`

Parametr trasy, jeśli istnieje, oczekiwany przez skojarzoną metodę akcji kontrolera.

### <a name="options"></a>Opcje

Następujące opcje są dostępne dla `get` polecenia:

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a>Przykład

Aby wydać żądanie HTTP GET:

1. Uruchom `get` polecenie w punkcie końcowym, który go obsługuje:

    ```console
    https://localhost:5001/people~ get
    ```

    Poprzednie polecenie wyświetla następujący format danych wyjściowych:

    ```console
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 21 Jun 2019 03:38:45 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "name": "Scott Hunter"
      },
      {
        "id": 2,
        "name": "Scott Hanselman"
      },
      {
        "id": 3,
        "name": "Scott Guthrie"
      }
    ]


    https://localhost:5001/people~
    ```

1. Pobierz konkretny rekord, przekazując parametr do `get` polecenia:

    ```console
    https://localhost:5001/people~ get 2
    ```

    Poprzednie polecenie wyświetla następujący format danych wyjściowych:

    ```console
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 21 Jun 2019 06:17:57 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 2,
        "name": "Scott Hanselman"
      }
    ]


    https://localhost:5001/people~
    ```

## <a name="test-http-post-requests"></a>Testowanie żądań POST protokołu HTTP

### <a name="synopsis"></a>Streszczenie

```console
post <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Argumenty

`PARAMETER`

Parametr trasy, jeśli istnieje, oczekiwany przez skojarzoną metodę akcji kontrolera.

### <a name="options"></a>Opcje

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a>Przykład

Aby wydać żądanie HTTP POST:

1. Uruchom `post` polecenie w punkcie końcowym, który go obsługuje:

    ```console
    https://localhost:5001/people~ post -h Content-Type=application/json
    ```

    W poprzednim poleceniu `Content-Type` nagłówek żądania HTTP jest ustawiany w taki sposób, aby wskazywał typ nośnika treści żądania JSON. Domyślny edytor tekstu otwiera plik *. tmp* z szablonem JSON reprezentującym treść żądania HTTP. Przykład:

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > Aby ustawić domyślny edytor tekstu, zobacz sekcję [Ustawianie domyślnego edytora tekstu](#set-the-default-text-editor) .

1. Zmodyfikuj szablon JSON, aby spełniał wymagania dotyczące weryfikacji modelu:

    ```json
    {
      "id": 0,
      "name": "Scott Addie"
    }
    ```

1. Zapisz plik *. tmp* i Zamknij Edytor tekstu. Następujące dane wyjściowe są wyświetlane w powłoce poleceń:

    ```console
    HTTP/1.1 201 Created
    Content-Type: application/json; charset=utf-8
    Date: Thu, 27 Jun 2019 21:24:18 GMT
    Location: https://localhost:5001/people/4
    Server: Kestrel
    Transfer-Encoding: chunked

    {
      "id": 4,
      "name": "Scott Addie"
    }


    https://localhost:5001/people~
    ```

## <a name="test-http-put-requests"></a>Testowanie żądań HTTP PUT

### <a name="synopsis"></a>Streszczenie

```console
put <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Argumenty

`PARAMETER`

Parametr trasy, jeśli istnieje, oczekiwany przez skojarzoną metodę akcji kontrolera.

### <a name="options"></a>Opcje

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a>Przykład

Aby wydać żądanie HTTP PUT:

1. *Opcjonalnie*: Uruchom `get` polecenie, aby wyświetlić dane przed zmodyfikowaniem:

    ```console
    https://localhost:5001/fruits~ get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:07:32 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Orange"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]
    ```

1. Uruchom `put` polecenie w punkcie końcowym, który go obsługuje:

    ```console
    https://localhost:5001/fruits~ put 2 -h Content-Type=application/json
    ```

    W poprzednim poleceniu `Content-Type` nagłówek żądania HTTP jest ustawiany w taki sposób, aby wskazywał typ nośnika treści żądania JSON. Domyślny edytor tekstu otwiera plik *. tmp* z szablonem JSON reprezentującym treść żądania HTTP. Przykład:

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > Aby ustawić domyślny edytor tekstu, zobacz sekcję [Ustawianie domyślnego edytora tekstu](#set-the-default-text-editor) .

1. Zmodyfikuj szablon JSON, aby spełniał wymagania dotyczące weryfikacji modelu:

    ```json
    {
      "id": 2,
      "name": "Cherry"
    }
    ```

1. Zapisz plik *. tmp* i Zamknij Edytor tekstu. Następujące dane wyjściowe są wyświetlane w powłoce poleceń:

    ```console
    [main 2019-06-28T17:27:01.805Z] update#setState idle
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:28:21 GMT
    Server: Kestrel
    ```

1. *Opcjonalne*: wydaj `get` polecenie, aby zobaczyć modyfikacje. Na przykład, jeśli wpisano "wiśnię" w edytorze tekstu, `get` zwraca następujące polecenie:

    ```console
    https://localhost:5001/fruits~ get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:08:20 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Cherry"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]


    https://localhost:5001/fruits~
    ```

## <a name="test-http-delete-requests"></a>Testowanie żądań HTTP DELETE

### <a name="synopsis"></a>Streszczenie

```console
delete <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Argumenty

`PARAMETER`

Parametr trasy, jeśli istnieje, oczekiwany przez skojarzoną metodę akcji kontrolera.

### <a name="options"></a>Opcje

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a>Przykład

Aby wydać żądanie HTTP DELETE:

1. *Opcjonalnie*: Uruchom `get` polecenie, aby wyświetlić dane przed zmodyfikowaniem:

    ```console
    https://localhost:5001/fruits~ get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:07:32 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 2,
        "data": "Orange"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]
    ```

1. Uruchom `delete` polecenie w punkcie końcowym, który go obsługuje:

    ```console
    https://localhost:5001/fruits~ delete 2
    ```

    Poprzednie polecenie wyświetla następujący format danych wyjściowych:

    ```console
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:36:42 GMT
    Server: Kestrel
    ```

1. *Opcjonalne*: wydaj `get` polecenie, aby zobaczyć modyfikacje. W tym przykładzie `get` zwraca następujące polecenie:

    ```console
    https://localhost:5001/fruits~ get
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Sat, 22 Jun 2019 00:16:30 GMT
    Server: Kestrel
    Transfer-Encoding: chunked

    [
      {
        "id": 1,
        "data": "Apple"
      },
      {
        "id": 3,
        "data": "Strawberry"
      }
    ]


    https://localhost:5001/fruits~
    ```

## <a name="test-http-patch-requests"></a>Testuj żądania poprawek HTTP

### <a name="synopsis"></a>Streszczenie

```console
patch <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Argumenty

`PARAMETER`

Parametr trasy, jeśli istnieje, oczekiwany przez skojarzoną metodę akcji kontrolera.

### <a name="options"></a>Opcje

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

## <a name="test-http-head-requests"></a>Testowanie żądań głównych HTTP

### <a name="synopsis"></a>Streszczenie

```console
head <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Argumenty

`PARAMETER`

Parametr trasy, jeśli istnieje, oczekiwany przez skojarzoną metodę akcji kontrolera.

### <a name="options"></a>Opcje

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="test-http-options-requests"></a>Testowe żądania opcji HTTP

### <a name="synopsis"></a>Streszczenie

```console
options <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a>Argumenty

`PARAMETER`

Parametr trasy, jeśli istnieje, oczekiwany przez skojarzoną metodę akcji kontrolera.

### <a name="options"></a>Opcje

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="set-http-request-headers"></a>Ustawianie nagłówków żądań HTTP

Aby ustawić nagłówek żądania HTTP, należy użyć jednej z następujących metod:

* Ustaw wartość inline z żądaniem HTTP. Przykład:

    ```console
    https://localhost:5001/people~ post -h Content-Type=application/json
    ```
    
    W przypadku wcześniejszego podejścia każdy unikatowy nagłówek żądania HTTP wymaga własnej `-h` opcji.

* Ustaw przed wysłaniem żądania HTTP. Przykład:

    ```console
    https://localhost:5001/people~ set header Content-Type application/json
    ```
    
    Podczas ustawiania nagłówka przed wysłaniem żądania nagłówek pozostaje ustawiony na czas trwania sesji powłoki poleceń. Aby wyczyścić nagłówek, podaj wartość pustą. Przykład:
    
    ```console
    https://localhost:5001/people~ set header Content-Type
    ```

## <a name="test-secured-endpoints"></a>Testuj zabezpieczone punkty końcowe

REPL HTTP obsługuje testowanie zabezpieczonych punktów końcowych na dwa sposoby: za pośrednictwem domyślnych poświadczeń zalogowanego użytkownika lub za pomocą nagłówków żądań HTTP. 

### <a name="default-credentials"></a>Poświadczenia domyślne

Rozważmy scenariusz, w którym testowa internetowy interfejs API jest hostowany w usługach IIS i jest zabezpieczony przy użyciu uwierzytelniania systemu Windows. Użytkownik chce, aby poświadczenia użytkownika uruchomiły narzędzie, aby przepływać do testowanych punktów końcowych HTTP. Aby przekazać domyślne poświadczenia zalogowanego użytkownika:

1. Ustaw `httpClient.useDefaultCredentials` preferencję na `true` :

    ```console
    pref set httpClient.useDefaultCredentials true
    ```

1. Zamknij narzędzie i uruchom je ponownie przed wysłaniem kolejnego żądania do internetowego interfejsu API.

### <a name="http-request-headers"></a>Nagłówki żądań HTTP

Przykłady obsługiwanych schematów uwierzytelniania i autoryzacji obejmują uwierzytelnianie podstawowe, tokeny okaziciela JWT i uwierzytelnianie szyfrowane. Na przykład można wysłać token okaziciela do punktu końcowego za pomocą następującego polecenia:

```console
set header Authorization "bearer <TOKEN VALUE>"
```

Aby uzyskać dostęp do punktu końcowego hostowanego na platformie Azure lub użyć [interfejsu API REST platformy Azure](/rest/api/azure/), potrzebujesz tokenu okaziciela. Wykonaj następujące kroki, aby uzyskać token okaziciela dla subskrypcji platformy Azure za pośrednictwem [interfejsu wiersza polecenia platformy Azure](/cli/azure/). REPL HTTP ustawia token okaziciela w nagłówku żądania HTTP i pobiera listę Web Apps Azure App Service.

1. Zaloguj się do platformy Azure:

    ```azurecli
    az login
    ```

1. Pobierz swój identyfikator subskrypcji za pomocą następującego polecenia:

    ```azurecli
    az account show --query id
    ```

1. Skopiuj identyfikator subskrypcji i uruchom następujące polecenie:

    ```azurecli
    az account set --subscription "<SUBSCRIPTION ID>"
    ```

1. Pobierz token okaziciela przy użyciu następującego polecenia:

    ```azurecli
    az account get-access-token --query accessToken
    ```

1. Połącz się z interfejsem API REST platformy Azure za pośrednictwem REPL HTTP:

    ```console
    httprepl https://management.azure.com
    ```

1. Ustawianie `Authorization` nagłówka żądania http:

    ```console
    https://management.azure.com/> set header Authorization "bearer <ACCESS TOKEN>"
    ```

1. Przejdź do subskrypcji:

    ```console
    https://management.azure.com/> cd subscriptions/<SUBSCRIPTION ID>
    ```

1. Pobierz listę Azure App Service subskrypcji Web Apps:

    ```console
    https://management.azure.com/subscriptions/{SUBSCRIPTION ID}> get providers/Microsoft.Web/sites?api-version=2016-08-01
    ```

    Zostanie wyświetlona następująca odpowiedź:

    ```console
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 35948
    Content-Type: application/json; charset=utf-8
    Date: Thu, 19 Sep 2019 23:04:03 GMT
    Expires: -1
    Pragma: no-cache
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    X-Content-Type-Options: nosniff
    x-ms-correlation-request-id: <em>xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</em>
    x-ms-original-request-ids: <em>xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx;xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</em>
    x-ms-ratelimit-remaining-subscription-reads: 11999
    x-ms-request-id: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    x-ms-routing-request-id: WESTUS:xxxxxxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx
    {
      "value": [
        <AZURE RESOURCES LIST>
      ]
    }
    ```

## <a name="toggle-http-request-display"></a>Przełącz wyświetlanie żądań HTTP

Domyślnie wyświetlanie wysyłanego żądania HTTP jest pomijane. Istnieje możliwość zmiany odpowiedniego ustawienia dla czasu trwania sesji powłoki poleceń.

### <a name="enable-request-display"></a>Włącz wyświetlanie żądań

Wyświetl wysyłane żądanie HTTP, uruchamiając `echo on` polecenie. Przykład:

```console
https://localhost:5001/people~ echo on
Request echoing is on
```

Kolejne żądania HTTP w bieżącej sesji wyświetlają nagłówki żądań. Przykład:

```console
https://localhost:5001/people~ post

[main 2019-06-28T18:50:11.930Z] update#setState idle
Request to https://localhost:5001...

POST /people HTTP/1.1
Content-Length: 41
Content-Type: application/json
User-Agent: HTTP-REPL

{
  "id": 0,
  "name": "Scott Addie"
}

Response from https://localhost:5001...

HTTP/1.1 201 Created
Content-Type: application/json; charset=utf-8
Date: Fri, 28 Jun 2019 18:50:21 GMT
Location: https://localhost:5001/people/4
Server: Kestrel
Transfer-Encoding: chunked

{
  "id": 4,
  "name": "Scott Addie"
}


https://localhost:5001/people~
```

### <a name="disable-request-display"></a>Wyłącz wyświetlanie żądań

Pomijaj wyświetlanie wysyłanego żądania HTTP przez uruchomienie `echo off` polecenia. Przykład:

```console
https://localhost:5001/people~ echo off
Request echoing is off
```

## <a name="run-a-script"></a>Uruchamianie skryptu

Jeśli często wykonujesz ten sam zestaw poleceń HTTP REPL, Rozważ przechowywanie ich w pliku tekstowym. Polecenia w pliku mają taki sam formularz jak te wykonywane ręcznie w wierszu polecenia. Polecenia mogą być wykonywane w sposób wsadowy przy użyciu `run` polecenia. Przykład:

1. Utwórz plik tekstowy zawierający zestaw poleceń rozdzielanych znakami nowego wiersza. Aby to zilustrować, rozważ plik *people-script.txt* zawierający następujące polecenia:

    ```text
    set base https://localhost:5001
    ls
    cd People
    ls
    get 1
    ```

1. Wykonaj `run` polecenie, przekazując w ścieżce pliku tekstowego. Przykład:

    ```console
    https://localhost:5001/~ run C:\http-repl-scripts\people-script.txt
    ```

    Wyświetlane są następujące dane wyjściowe:

    ```console
    https://localhost:5001/~ set base https://localhost:5001
    Using swagger metadata from https://localhost:5001/swagger/v1/swagger.json
    
    https://localhost:5001/~ ls
    .        []
    Fruits   [get|post]
    People   [get|post]
    
    https://localhost:5001/~ cd People
    /People    [get|post]
    
    https://localhost:5001/People~ ls
    .      [get|post]
    ..     []
    {id}   [get|put|delete]
    
    https://localhost:5001/People~ get 1
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 12 Jul 2019 19:20:10 GMT
    Server: Kestrel
    Transfer-Encoding: chunked
    
    {
      "id": 1,
      "name": "Scott Hunter"
    }
    
    
    https://localhost:5001/People~
    ```

## <a name="clear-the-output"></a>Wyczyść dane wyjściowe

Aby usunąć wszystkie dane wyjściowe zapisywane do powłoki poleceń za pomocą narzędzia HTTP REPL, uruchom `clear` polecenie lub `cls` . Do zilustrowania, Załóżmy, że powłoka poleceń zawiera następujące dane wyjściowe:

```console
httprepl https://localhost:5001
(Disconnected)~ set base "https://localhost:5001"
Using swagger metadata from https://localhost:5001/swagger/v1/swagger.json

https://localhost:5001/~ ls
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/~
```

Uruchom następujące polecenie, aby wyczyścić dane wyjściowe:

```console
https://localhost:5001/~ clear
```

Po uruchomieniu poprzedniego polecenia powłoka poleceń zawiera tylko następujące dane wyjściowe:

```console
https://localhost:5001/~
```

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Żądania interfejsu API REST](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)
* [Repozytorium usługi GitHub HTTP REPL](https://github.com/dotnet/HttpRepl)
