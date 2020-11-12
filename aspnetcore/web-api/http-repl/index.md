---
title: Testowanie interfejsów API sieci Web za pomocą HttpRepl
author: scottaddie
description: Dowiedz się, jak przeglądać i testować ASP.NET Core internetowy interfejs API za pomocą narzędzia globalnego HttpRepl .NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc, devx-track-azurecli
ms.date: 11/11/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: web-api/http-repl
ms.openlocfilehash: df2d4e63a18471b4c5f4f1c9434921303bb1da8a
ms.sourcegitcommit: 202144092067ea81be1dbb229329518d781dbdfb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94550624"
---
# <a name="test-web-apis-with-the-httprepl"></a><span data-ttu-id="78662-103">Testowanie interfejsów API sieci Web za pomocą HttpRepl</span><span class="sxs-lookup"><span data-stu-id="78662-103">Test web APIs with the HttpRepl</span></span>

<span data-ttu-id="78662-104">Przez [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="78662-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="78662-105">Pętla HTTP Read-eval-Print (REPL) to:</span><span class="sxs-lookup"><span data-stu-id="78662-105">The HTTP Read-Eval-Print Loop (REPL) is:</span></span>

* <span data-ttu-id="78662-106">Lekkie, międzyplatformowe narzędzie wiersza polecenia, które jest obsługiwane wszędzie tam, gdzie jest obsługiwane środowisko .NET Core.</span><span class="sxs-lookup"><span data-stu-id="78662-106">A lightweight, cross-platform command-line tool that's supported everywhere .NET Core is supported.</span></span>
* <span data-ttu-id="78662-107">Służy do wykonywania żądań HTTP do testowania ASP.NET Core interfejsów API sieci Web (oraz non-ASP.NET podstawowych interfejsów API sieci Web) i wyświetlania ich wyników.</span><span class="sxs-lookup"><span data-stu-id="78662-107">Used for making HTTP requests to test ASP.NET Core web APIs (and non-ASP.NET Core web APIs) and view their results.</span></span>
* <span data-ttu-id="78662-108">Możliwość testowania interfejsów API sieci Web hostowanych w dowolnym środowisku, w tym hosta lokalnego i Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="78662-108">Capable of testing web APIs hosted in any environment, including localhost and Azure App Service.</span></span>

<span data-ttu-id="78662-109">Obsługiwane są następujące [czasowniki http](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) :</span><span class="sxs-lookup"><span data-stu-id="78662-109">The following [HTTP verbs](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) are supported:</span></span>

* [<span data-ttu-id="78662-110">USUNIĘTY</span><span class="sxs-lookup"><span data-stu-id="78662-110">DELETE</span></span>](#test-http-delete-requests)
* [<span data-ttu-id="78662-111">Pobierz</span><span class="sxs-lookup"><span data-stu-id="78662-111">GET</span></span>](#test-http-get-requests)
* [<span data-ttu-id="78662-112">MTP</span><span class="sxs-lookup"><span data-stu-id="78662-112">HEAD</span></span>](#test-http-head-requests)
* [<span data-ttu-id="78662-113">Opcje</span><span class="sxs-lookup"><span data-stu-id="78662-113">OPTIONS</span></span>](#test-http-options-requests)
* [<span data-ttu-id="78662-114">WYSŁANA</span><span class="sxs-lookup"><span data-stu-id="78662-114">PATCH</span></span>](#test-http-patch-requests)
* [<span data-ttu-id="78662-115">POUBOJOWEGO</span><span class="sxs-lookup"><span data-stu-id="78662-115">POST</span></span>](#test-http-post-requests)
* [<span data-ttu-id="78662-116">PUT</span><span class="sxs-lookup"><span data-stu-id="78662-116">PUT</span></span>](#test-http-put-requests)

<span data-ttu-id="78662-117">Aby wykonać te czynności, [Wyświetl lub Pobierz przykładowy ASP.NET Core internetowy interfejs API](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="78662-117">To follow along, [view or download the sample ASP.NET Core web API](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="78662-118">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="78662-118">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](~/includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="78662-119">Instalacja</span><span class="sxs-lookup"><span data-stu-id="78662-119">Installation</span></span>

<span data-ttu-id="78662-120">Aby zainstalować HttpRepl, uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="78662-120">To install the HttpRepl, run the following command:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.dotnet-httprepl
```

<span data-ttu-id="78662-121">[Narzędzie globalne platformy .NET Core](/dotnet/core/tools/global-tools#install-a-global-tool) jest instalowane z pakietu NuGet [Microsoft. dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) .</span><span class="sxs-lookup"><span data-stu-id="78662-121">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) NuGet package.</span></span>

## <a name="usage"></a><span data-ttu-id="78662-122">Użycie</span><span class="sxs-lookup"><span data-stu-id="78662-122">Usage</span></span>

<span data-ttu-id="78662-123">Po pomyślnej instalacji narzędzia Uruchom następujące polecenie, aby uruchomić HttpRepl:</span><span class="sxs-lookup"><span data-stu-id="78662-123">After successful installation of the tool, run the following command to start the HttpRepl:</span></span>

```console
httprepl
```

<span data-ttu-id="78662-124">Aby wyświetlić dostępne polecenia HttpRepl, Uruchom jedno z następujących poleceń:</span><span class="sxs-lookup"><span data-stu-id="78662-124">To view the available HttpRepl commands, run one of the following commands:</span></span>

```console
httprepl -h
```

```console
httprepl --help
```

<span data-ttu-id="78662-125">Wyświetlane są następujące dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="78662-125">The following output is displayed:</span></span>

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

<span data-ttu-id="78662-126">HttpRepl oferuje polecenie uzupełniania.</span><span class="sxs-lookup"><span data-stu-id="78662-126">The HttpRepl offers command completion.</span></span> <span data-ttu-id="78662-127">Naciśnięcie klawisza <kbd>Tab</kbd> wykonuje iterację na liście poleceń, które uzupełniają wpisane znaki lub punkt końcowy interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="78662-127">Pressing the <kbd>Tab</kbd> key iterates through the list of commands that complete the characters or API endpoint that you typed.</span></span> <span data-ttu-id="78662-128">W poniższych sekcjach znajduje się opis dostępnych poleceń interfejsu wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="78662-128">The following sections outline the available CLI commands.</span></span>

## <a name="connect-to-the-web-api"></a><span data-ttu-id="78662-129">Nawiązywanie połączenia z interfejsem API sieci Web</span><span class="sxs-lookup"><span data-stu-id="78662-129">Connect to the web API</span></span>

<span data-ttu-id="78662-130">Połącz się z interfejsem API sieci Web, uruchamiając następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="78662-130">Connect to a web API by running the following command:</span></span>

```console
httprepl <ROOT URI>
```

<span data-ttu-id="78662-131">`<ROOT URI>` jest podstawowym identyfikatorem URI dla internetowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="78662-131">`<ROOT URI>` is the base URI for the web API.</span></span> <span data-ttu-id="78662-132">Przykład:</span><span class="sxs-lookup"><span data-stu-id="78662-132">For example:</span></span>

```console
httprepl https://localhost:5001
```

<span data-ttu-id="78662-133">Alternatywnie Uruchom następujące polecenie w dowolnym momencie, gdy HttpRepl jest uruchomiona:</span><span class="sxs-lookup"><span data-stu-id="78662-133">Alternatively, run the following command at any time while the HttpRepl is running:</span></span>

```console
connect <ROOT URI>
```

<span data-ttu-id="78662-134">Przykład:</span><span class="sxs-lookup"><span data-stu-id="78662-134">For example:</span></span>

```console
(Disconnected)> connect https://localhost:5001
```

### <a name="manually-point-to-the-openapi-description-for-the-web-api"></a><span data-ttu-id="78662-135">Ręcznie wskaż opis OpenAPI dla internetowego interfejsu API</span><span class="sxs-lookup"><span data-stu-id="78662-135">Manually point to the OpenAPI description for the web API</span></span>

<span data-ttu-id="78662-136">Powyższe polecenie Connect podejmie próbę automatycznego znalezienia opisu OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="78662-136">The connect command above will attempt to find the OpenAPI description automatically.</span></span> <span data-ttu-id="78662-137">Jeśli z jakiegoś powodu nie można tego zrobić, możesz określić identyfikator URI opisu OpenAPI dla internetowego interfejsu API przy użyciu `--openapi` opcji:</span><span class="sxs-lookup"><span data-stu-id="78662-137">If for some reason it's unable to do so, you can specify the URI of the OpenAPI description for the web API by using the `--openapi` option:</span></span>

```console
connect <ROOT URI> --openapi <OPENAPI DESCRIPTION ADDRESS>
```

<span data-ttu-id="78662-138">Przykład:</span><span class="sxs-lookup"><span data-stu-id="78662-138">For example:</span></span>

```console
(Disconnected)> connect https://localhost:5001 --openapi /swagger/v1/swagger.json
```

### <a name="enable-verbose-output-for-details-on-openapi-description-searching-parsing-and-validation"></a><span data-ttu-id="78662-139">Włącz pełne dane wyjściowe, aby uzyskać szczegółowe informacje na temat wyszukiwania, analizowania i walidacji opisu OpenAPI</span><span class="sxs-lookup"><span data-stu-id="78662-139">Enable verbose output for details on OpenAPI description searching, parsing, and validation</span></span>

<span data-ttu-id="78662-140">Określenie `--verbose` opcji za pomocą `connect` polecenia spowoduje wygenerowanie większej ilości szczegółów, gdy narzędzie wyszukuje opis openapi, analizuje je i weryfikuje.</span><span class="sxs-lookup"><span data-stu-id="78662-140">Specifying the `--verbose` option with the `connect` command will produce more details when the tool searches for the OpenAPI description, parses, and validates it.</span></span>

```console
connect <ROOT URI> --verbose
```

<span data-ttu-id="78662-141">Przykład:</span><span class="sxs-lookup"><span data-stu-id="78662-141">For example:</span></span>

```console
(Disconnected)> connect https://localhost:5001 --verbose
Checking https://localhost:5001/swagger.json... 404 NotFound
Checking https://localhost:5001/swagger/v1/swagger.json... 404 NotFound
Checking https://localhost:5001/openapi.json... Found
Parsing... Successful (with warnings)
The field 'info' in 'document' object is REQUIRED [#/info]
The field 'paths' in 'document' object is REQUIRED [#/paths]
```

## <a name="navigate-the-web-api"></a><span data-ttu-id="78662-142">Nawigowanie po interfejsie API sieci Web</span><span class="sxs-lookup"><span data-stu-id="78662-142">Navigate the web API</span></span>

### <a name="view-available-endpoints"></a><span data-ttu-id="78662-143">Wyświetl dostępne punkty końcowe</span><span class="sxs-lookup"><span data-stu-id="78662-143">View available endpoints</span></span>

<span data-ttu-id="78662-144">Aby wyświetlić listę różnych punktów końcowych (kontrolerów) znajdujących się w bieżącej ścieżce adresu internetowego interfejsu API, uruchom `ls` `dir` polecenie lub:</span><span class="sxs-lookup"><span data-stu-id="78662-144">To list the different endpoints (controllers) at the current path of the web API address, run the `ls` or `dir` command:</span></span>

```console
https://localhot:5001/> ls
```

<span data-ttu-id="78662-145">Wyświetlany jest następujący format danych wyjściowych:</span><span class="sxs-lookup"><span data-stu-id="78662-145">The following output format is displayed:</span></span>

```console
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/>
```

<span data-ttu-id="78662-146">Powyższe dane wyjściowe wskazują, że dostępne są dwa kontrolery: `Fruits` i `People` .</span><span class="sxs-lookup"><span data-stu-id="78662-146">The preceding output indicates that there are two controllers available: `Fruits` and `People`.</span></span> <span data-ttu-id="78662-147">Oba kontrolery obsługują bez parametrów operacje GET i POST HTTP.</span><span class="sxs-lookup"><span data-stu-id="78662-147">Both controllers support parameterless HTTP GET and POST operations.</span></span>

<span data-ttu-id="78662-148">Przechodzenie do określonego kontrolera ujawnia więcej szczegółów.</span><span class="sxs-lookup"><span data-stu-id="78662-148">Navigating into a specific controller reveals more detail.</span></span> <span data-ttu-id="78662-149">Na przykład następujące dane wyjściowe polecenia pokazują `Fruits` kontroler obsługuje również operacje Get, PUT i DELETE protokołu HTTP.</span><span class="sxs-lookup"><span data-stu-id="78662-149">For example, the following command's output shows the `Fruits` controller also supports HTTP GET, PUT, and DELETE operations.</span></span> <span data-ttu-id="78662-150">Każda z tych operacji oczekuje `id` parametru w marszrucie:</span><span class="sxs-lookup"><span data-stu-id="78662-150">Each of these operations expects an `id` parameter in the route:</span></span>

```console
https://localhost:5001/fruits> ls
.      [get|post]
..     []
{id}   [get|put|delete]

https://localhost:5001/fruits>
```

<span data-ttu-id="78662-151">Alternatywnie można uruchomić `ui` polecenie, aby otworzyć stronę interfejsu użytkownika programu Swagger interfejsu API sieci Web w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="78662-151">Alternatively, run the `ui` command to open the web API's Swagger UI page in a browser.</span></span> <span data-ttu-id="78662-152">Przykład:</span><span class="sxs-lookup"><span data-stu-id="78662-152">For example:</span></span>

```console
https://localhost:5001/> ui
```

### <a name="navigate-to-an-endpoint"></a><span data-ttu-id="78662-153">Przejdź do punktu końcowego</span><span class="sxs-lookup"><span data-stu-id="78662-153">Navigate to an endpoint</span></span>

<span data-ttu-id="78662-154">Aby przejść do innego punktu końcowego w internetowym interfejsie API, uruchom `cd` polecenie:</span><span class="sxs-lookup"><span data-stu-id="78662-154">To navigate to a different endpoint on the web API, run the `cd` command:</span></span>

```console
https://localhost:5001/> cd people
```

<span data-ttu-id="78662-155">W ścieżce następującego `cd` polecenia jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="78662-155">The path following the `cd` command is case insensitive.</span></span> <span data-ttu-id="78662-156">Wyświetlany jest następujący format danych wyjściowych:</span><span class="sxs-lookup"><span data-stu-id="78662-156">The following output format is displayed:</span></span>

```console
/people    [get|post]

https://localhost:5001/people>
```

## <a name="customize-the-httprepl"></a><span data-ttu-id="78662-157">Dostosuj HttpRepl</span><span class="sxs-lookup"><span data-stu-id="78662-157">Customize the HttpRepl</span></span>

<span data-ttu-id="78662-158">Domyślne [kolory](#set-color-preferences) HttpRepl można dostosować.</span><span class="sxs-lookup"><span data-stu-id="78662-158">The HttpRepl's default [colors](#set-color-preferences) can be customized.</span></span> <span data-ttu-id="78662-159">Ponadto można zdefiniować [domyślny edytor tekstu](#set-the-default-text-editor) .</span><span class="sxs-lookup"><span data-stu-id="78662-159">Additionally, a [default text editor](#set-the-default-text-editor) can be defined.</span></span> <span data-ttu-id="78662-160">Preferencje HttpRepl są utrwalane w bieżącej sesji i są honorowane w przyszłych sesjach.</span><span class="sxs-lookup"><span data-stu-id="78662-160">The HttpRepl preferences are persisted across the current session and are honored in future sessions.</span></span> <span data-ttu-id="78662-161">Po zmodyfikowaniu preferencje są przechowywane w następującym pliku:</span><span class="sxs-lookup"><span data-stu-id="78662-161">Once modified, the preferences are stored in the following file:</span></span>

# <a name="linux"></a>[<span data-ttu-id="78662-162">Linux</span><span class="sxs-lookup"><span data-stu-id="78662-162">Linux</span></span>](#tab/linux)

<span data-ttu-id="78662-163">*% HOME%/.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="78662-163">*%HOME%/.httpreplprefs*</span></span>

# <a name="macos"></a>[<span data-ttu-id="78662-164">macOS</span><span class="sxs-lookup"><span data-stu-id="78662-164">macOS</span></span>](#tab/macos)

<span data-ttu-id="78662-165">*% HOME%/.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="78662-165">*%HOME%/.httpreplprefs*</span></span>

# <a name="windows"></a>[<span data-ttu-id="78662-166">Windows</span><span class="sxs-lookup"><span data-stu-id="78662-166">Windows</span></span>](#tab/windows)

<span data-ttu-id="78662-167">*% USERPROFILE% \\ . httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="78662-167">*%USERPROFILE%\\.httpreplprefs*</span></span>

---

<span data-ttu-id="78662-168">Plik *. httpreplprefs* jest ładowany podczas uruchamiania i nie jest monitorowany pod kątem zmian w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="78662-168">The *.httpreplprefs* file is loaded on startup and not monitored for changes at runtime.</span></span> <span data-ttu-id="78662-169">Ręczne modyfikacje pliku zaczną obowiązywać dopiero po ponownym uruchomieniu narzędzia.</span><span class="sxs-lookup"><span data-stu-id="78662-169">Manual modifications to the file take effect only after restarting the tool.</span></span>

### <a name="view-the-settings"></a><span data-ttu-id="78662-170">Wyświetl ustawienia</span><span class="sxs-lookup"><span data-stu-id="78662-170">View the settings</span></span>

<span data-ttu-id="78662-171">Aby wyświetlić dostępne ustawienia, uruchom `pref get` polecenie.</span><span class="sxs-lookup"><span data-stu-id="78662-171">To view the available settings, run the `pref get` command.</span></span> <span data-ttu-id="78662-172">Przykład:</span><span class="sxs-lookup"><span data-stu-id="78662-172">For example:</span></span>

```console
https://localhost:5001/> pref get
```

<span data-ttu-id="78662-173">Poprzednie polecenie wyświetla dostępne pary klucz-wartość:</span><span class="sxs-lookup"><span data-stu-id="78662-173">The preceding command displays the available key-value pairs:</span></span>

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

### <a name="set-color-preferences"></a><span data-ttu-id="78662-174">Ustawianie preferencji koloru</span><span class="sxs-lookup"><span data-stu-id="78662-174">Set color preferences</span></span>

<span data-ttu-id="78662-175">Kolorowanie odpowiedzi jest obecnie obsługiwane tylko w przypadku formatu JSON.</span><span class="sxs-lookup"><span data-stu-id="78662-175">Response colorization is currently supported for JSON only.</span></span> <span data-ttu-id="78662-176">Aby dostosować domyślne kolorowanie narzędzi HttpRepl, Znajdź klucz odpowiadający kolorowi do zmiany.</span><span class="sxs-lookup"><span data-stu-id="78662-176">To customize the default HttpRepl tool coloring, locate the key corresponding to the color to be changed.</span></span> <span data-ttu-id="78662-177">Aby uzyskać instrukcje dotyczące znajdowania kluczy, zobacz sekcję [Wyświetlanie ustawień](#view-the-settings) .</span><span class="sxs-lookup"><span data-stu-id="78662-177">For instructions on how to find the keys, see the [View the settings](#view-the-settings) section.</span></span> <span data-ttu-id="78662-178">Na przykład zmień `colors.json` wartość klucza z na w `Green` `White` następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="78662-178">For example, change the `colors.json` key value from `Green` to `White` as follows:</span></span>

```console
https://localhost:5001/people> pref set colors.json White
```

<span data-ttu-id="78662-179">Można używać tylko [dozwolonych kolorów](https://github.com/dotnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) .</span><span class="sxs-lookup"><span data-stu-id="78662-179">Only the [allowed colors](https://github.com/dotnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) may be used.</span></span> <span data-ttu-id="78662-180">Kolejne żądania HTTP wyświetlają dane wyjściowe z nowym kolorem.</span><span class="sxs-lookup"><span data-stu-id="78662-180">Subsequent HTTP requests display output with the new coloring.</span></span>

<span data-ttu-id="78662-181">Jeśli określone klucze kolorów nie są ustawione, brane są więcej kluczy ogólnych.</span><span class="sxs-lookup"><span data-stu-id="78662-181">When specific color keys aren't set, more generic keys are considered.</span></span> <span data-ttu-id="78662-182">Aby zademonstrować to zachowanie rezerwowe, należy wziąć pod uwagę następujący przykład:</span><span class="sxs-lookup"><span data-stu-id="78662-182">To demonstrate this fallback behavior, consider the following example:</span></span>

* <span data-ttu-id="78662-183">Jeśli `colors.json.name` nie ma wartości, `colors.json.string` jest używana.</span><span class="sxs-lookup"><span data-stu-id="78662-183">If `colors.json.name` doesn't have a value, `colors.json.string` is used.</span></span>
* <span data-ttu-id="78662-184">Jeśli `colors.json.string` nie ma wartości, `colors.json.literal` jest używana.</span><span class="sxs-lookup"><span data-stu-id="78662-184">If `colors.json.string` doesn't have a value, `colors.json.literal` is used.</span></span>
* <span data-ttu-id="78662-185">Jeśli `colors.json.literal` nie ma wartości, `colors.json` jest używana.</span><span class="sxs-lookup"><span data-stu-id="78662-185">If `colors.json.literal` doesn't have a value, `colors.json` is used.</span></span> 
* <span data-ttu-id="78662-186">Jeśli `colors.json` nie ma wartości, używany jest domyślny kolor tekstu powłoki poleceń ( `AllowedColors.None` ).</span><span class="sxs-lookup"><span data-stu-id="78662-186">If `colors.json` doesn't have a value, the command shell's default text color (`AllowedColors.None`) is used.</span></span>

### <a name="set-indentation-size"></a><span data-ttu-id="78662-187">Ustaw rozmiar wcięcia</span><span class="sxs-lookup"><span data-stu-id="78662-187">Set indentation size</span></span>

<span data-ttu-id="78662-188">Dostosowanie rozmiaru wcięcia odpowiedzi jest obecnie obsługiwane tylko w przypadku formatu JSON.</span><span class="sxs-lookup"><span data-stu-id="78662-188">Response indentation size customization is currently supported for JSON only.</span></span> <span data-ttu-id="78662-189">Domyślny rozmiar to dwie spacje.</span><span class="sxs-lookup"><span data-stu-id="78662-189">The default size is two spaces.</span></span> <span data-ttu-id="78662-190">Przykład:</span><span class="sxs-lookup"><span data-stu-id="78662-190">For example:</span></span>

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

<span data-ttu-id="78662-191">Aby zmienić rozmiar domyślny, ustaw `formatting.json.indentSize` klucz.</span><span class="sxs-lookup"><span data-stu-id="78662-191">To change the default size, set the `formatting.json.indentSize` key.</span></span> <span data-ttu-id="78662-192">Na przykład, aby zawsze używać czterech spacji:</span><span class="sxs-lookup"><span data-stu-id="78662-192">For example, to always use four spaces:</span></span>

```console
pref set formatting.json.indentSize 4
```

<span data-ttu-id="78662-193">Kolejne odpowiedzi przestrzegają ustawień czterech spacji:</span><span class="sxs-lookup"><span data-stu-id="78662-193">Subsequent responses honor the setting of four spaces:</span></span>

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

### <a name="set-the-default-text-editor"></a><span data-ttu-id="78662-194">Ustawianie domyślnego edytora tekstu</span><span class="sxs-lookup"><span data-stu-id="78662-194">Set the default text editor</span></span>

<span data-ttu-id="78662-195">Domyślnie HttpRepl nie ma edytora tekstu skonfigurowanego do użycia.</span><span class="sxs-lookup"><span data-stu-id="78662-195">By default, the HttpRepl has no text editor configured for use.</span></span> <span data-ttu-id="78662-196">Aby przetestować metody interfejsu API sieci Web wymagające treści żądania HTTP, należy ustawić domyślny edytor tekstu.</span><span class="sxs-lookup"><span data-stu-id="78662-196">To test web API methods requiring an HTTP request body, a default text editor must be set.</span></span> <span data-ttu-id="78662-197">Narzędzie HttpRepl uruchamia skonfigurowany Edytor tekstów wyłącznie na potrzeby redagowania treści żądania.</span><span class="sxs-lookup"><span data-stu-id="78662-197">The HttpRepl tool launches the configured text editor for the sole purpose of composing the request body.</span></span> <span data-ttu-id="78662-198">Uruchom następujące polecenie, aby ustawić preferowany Edytor tekstu jako domyślny:</span><span class="sxs-lookup"><span data-stu-id="78662-198">Run the following command to set your preferred text editor as the default:</span></span>

```console
pref set editor.command.default "<EXECUTABLE>"
```

<span data-ttu-id="78662-199">W poprzednim poleceniu `<EXECUTABLE>` jest pełną ścieżką do pliku wykonywalnego edytora tekstu.</span><span class="sxs-lookup"><span data-stu-id="78662-199">In the preceding command, `<EXECUTABLE>` is the full path to the text editor's executable file.</span></span> <span data-ttu-id="78662-200">Na przykład uruchom następujące polecenie, aby ustawić Visual Studio Code jako domyślny edytor tekstu:</span><span class="sxs-lookup"><span data-stu-id="78662-200">For example, run the following command to set Visual Studio Code as the default text editor:</span></span>

# <a name="linux"></a>[<span data-ttu-id="78662-201">Linux</span><span class="sxs-lookup"><span data-stu-id="78662-201">Linux</span></span>](#tab/linux)

```console
pref set editor.command.default "/usr/bin/code"
```

# <a name="macos"></a>[<span data-ttu-id="78662-202">macOS</span><span class="sxs-lookup"><span data-stu-id="78662-202">macOS</span></span>](#tab/macos)

```console
pref set editor.command.default "/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code"
```

# <a name="windows"></a>[<span data-ttu-id="78662-203">Windows</span><span class="sxs-lookup"><span data-stu-id="78662-203">Windows</span></span>](#tab/windows)

```console
pref set editor.command.default "C:\Program Files\Microsoft VS Code\Code.exe"
```

---

<span data-ttu-id="78662-204">Aby uruchomić domyślny edytor tekstu z określonymi argumentami interfejsu wiersza polecenia, należy ustawić `editor.command.default.arguments` klucz.</span><span class="sxs-lookup"><span data-stu-id="78662-204">To launch the default text editor with specific CLI arguments, set the `editor.command.default.arguments` key.</span></span> <span data-ttu-id="78662-205">Załóżmy na przykład, że Visual Studio Code jest domyślnym edytorem tekstu i zawsze chcesz, aby HttpRepl się otwierać Visual Studio Code w nowej sesji z wyłączonymi rozszerzeniami.</span><span class="sxs-lookup"><span data-stu-id="78662-205">For example, assume Visual Studio Code is the default text editor and that you always want the HttpRepl to open Visual Studio Code in a new session with extensions disabled.</span></span> <span data-ttu-id="78662-206">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="78662-206">Run the following command:</span></span>

```console
pref set editor.command.default.arguments "--disable-extensions --new-window"
```

> [!TIP]
> <span data-ttu-id="78662-207">Jeśli domyślny edytor jest Visual Studio Code, zazwyczaj chcesz przekazać `-w` argument lub, `--wait` Aby wymusić Visual Studio Code, aby zaczekać, aż zamknie plik przed zwróceniem.</span><span class="sxs-lookup"><span data-stu-id="78662-207">If your default editor is Visual Studio Code, you'll usually want to pass the `-w` or `--wait` argument to force Visual Studio Code to wait for you to close the file before returning.</span></span>

### <a name="set-the-openapi-description-search-paths"></a><span data-ttu-id="78662-208">Ustaw ścieżki wyszukiwania opisu OpenAPI</span><span class="sxs-lookup"><span data-stu-id="78662-208">Set the OpenAPI Description search paths</span></span>

<span data-ttu-id="78662-209">Domyślnie HttpRepl ma zestaw ścieżek względnych, których używa, aby znaleźć opis OpenAPI podczas wykonywania `connect` polecenia bez `--openapi` opcji.</span><span class="sxs-lookup"><span data-stu-id="78662-209">By default, the HttpRepl has a set of relative paths that it uses to find the OpenAPI description when executing the `connect` command without the `--openapi` option.</span></span> <span data-ttu-id="78662-210">Ścieżki względne są łączone z ścieżkami głównymi i podstawowymi określonymi w `connect` poleceniu.</span><span class="sxs-lookup"><span data-stu-id="78662-210">These relative paths are combined with the root and base paths specified in the `connect` command.</span></span> <span data-ttu-id="78662-211">Domyślne ścieżki względne to:</span><span class="sxs-lookup"><span data-stu-id="78662-211">The default relative paths are:</span></span>

- <span data-ttu-id="78662-212">*swagger.jsna*</span><span class="sxs-lookup"><span data-stu-id="78662-212">*swagger.json*</span></span>
- <span data-ttu-id="78662-213">*Struktura Swagger/V1/swagger.jswłączona*</span><span class="sxs-lookup"><span data-stu-id="78662-213">*swagger/v1/swagger.json*</span></span>
- <span data-ttu-id="78662-214">*/swagger.jsna*</span><span class="sxs-lookup"><span data-stu-id="78662-214">*/swagger.json*</span></span>
- <span data-ttu-id="78662-215">*/Swagger/V1/swagger.jsna*</span><span class="sxs-lookup"><span data-stu-id="78662-215">*/swagger/v1/swagger.json*</span></span>
- <span data-ttu-id="78662-216">*openapi.jsna*</span><span class="sxs-lookup"><span data-stu-id="78662-216">*openapi.json*</span></span>
- <span data-ttu-id="78662-217">*/openapi.jsna*</span><span class="sxs-lookup"><span data-stu-id="78662-217">*/openapi.json*</span></span>

<span data-ttu-id="78662-218">Aby użyć innego zestawu ścieżek wyszukiwania w środowisku, ustaw `swagger.searchPaths` preferencję.</span><span class="sxs-lookup"><span data-stu-id="78662-218">To use a different set of search paths in your environment, set the `swagger.searchPaths` preference.</span></span> <span data-ttu-id="78662-219">Wartość musi być rozdzielaną potokami listą ścieżek względnych.</span><span class="sxs-lookup"><span data-stu-id="78662-219">The value must be a pipe-delimited list of relative paths.</span></span> <span data-ttu-id="78662-220">Przykład:</span><span class="sxs-lookup"><span data-stu-id="78662-220">For example:</span></span>

```console
pref set swagger.searchPaths "swagger/v2/swagger.json|swagger/v3/swagger.json"
```

<span data-ttu-id="78662-221">Zamiast całkowicie zastąpić listę domyślną, można również zmodyfikować listę, dodając lub usuwając ścieżki.</span><span class="sxs-lookup"><span data-stu-id="78662-221">Instead of replacing the default list altogether, the list can also be modified by adding or removing paths.</span></span>

<span data-ttu-id="78662-222">Aby dodać jedną lub więcej ścieżek wyszukiwania do listy domyślnej, ustaw `swagger.addToSearchPaths` preferencję.</span><span class="sxs-lookup"><span data-stu-id="78662-222">To add one or more search paths to the default list, set the `swagger.addToSearchPaths` preference.</span></span> <span data-ttu-id="78662-223">Wartość musi być rozdzielaną potokami listą ścieżek względnych.</span><span class="sxs-lookup"><span data-stu-id="78662-223">The value must be a pipe-delimited list of relative paths.</span></span> <span data-ttu-id="78662-224">Przykład:</span><span class="sxs-lookup"><span data-stu-id="78662-224">For example:</span></span>

```console
pref set swagger.addToSearchPaths "openapi/v2/openapi.json|openapi/v3/openapi.json"
```

<span data-ttu-id="78662-225">Aby usunąć jedną lub więcej ścieżek wyszukiwania z listy domyślnej, ustaw `swagger.addToSearchPaths` preferencję.</span><span class="sxs-lookup"><span data-stu-id="78662-225">To remove one or more search paths from the default list, set the `swagger.addToSearchPaths` preference.</span></span> <span data-ttu-id="78662-226">Wartość musi być rozdzielaną potokami listą ścieżek względnych.</span><span class="sxs-lookup"><span data-stu-id="78662-226">The value must be a pipe-delimited list of relative paths.</span></span> <span data-ttu-id="78662-227">Przykład:</span><span class="sxs-lookup"><span data-stu-id="78662-227">For example:</span></span>

```console
pref set swagger.removeFromSearchPaths "swagger.json|/swagger.json"
```

## <a name="test-http-get-requests"></a><span data-ttu-id="78662-228">Testuj żądania HTTP GET</span><span class="sxs-lookup"><span data-stu-id="78662-228">Test HTTP GET requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="78662-229">Streszczenie</span><span class="sxs-lookup"><span data-stu-id="78662-229">Synopsis</span></span>

```console
get <PARAMETER> [-F|--no-formatting] [-h|--header] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="78662-230">Argumenty</span><span class="sxs-lookup"><span data-stu-id="78662-230">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="78662-231">Parametr trasy, jeśli istnieje, oczekiwany przez skojarzoną metodę akcji kontrolera.</span><span class="sxs-lookup"><span data-stu-id="78662-231">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="78662-232">Opcje</span><span class="sxs-lookup"><span data-stu-id="78662-232">Options</span></span>

<span data-ttu-id="78662-233">Następujące opcje są dostępne dla `get` polecenia:</span><span class="sxs-lookup"><span data-stu-id="78662-233">The following options are available for the `get` command:</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="78662-234">Przykład</span><span class="sxs-lookup"><span data-stu-id="78662-234">Example</span></span>

<span data-ttu-id="78662-235">Aby wydać żądanie HTTP GET:</span><span class="sxs-lookup"><span data-stu-id="78662-235">To issue an HTTP GET request:</span></span>

1. <span data-ttu-id="78662-236">Uruchom `get` polecenie w punkcie końcowym, który go obsługuje:</span><span class="sxs-lookup"><span data-stu-id="78662-236">Run the `get` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people> get
    ```

    <span data-ttu-id="78662-237">Poprzednie polecenie wyświetla następujący format danych wyjściowych:</span><span class="sxs-lookup"><span data-stu-id="78662-237">The preceding command displays the following output format:</span></span>

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


    https://localhost:5001/people>
    ```

1. <span data-ttu-id="78662-238">Pobierz konkretny rekord, przekazując parametr do `get` polecenia:</span><span class="sxs-lookup"><span data-stu-id="78662-238">Retrieve a specific record by passing a parameter to the `get` command:</span></span>

    ```console
    https://localhost:5001/people> get 2
    ```

    <span data-ttu-id="78662-239">Poprzednie polecenie wyświetla następujący format danych wyjściowych:</span><span class="sxs-lookup"><span data-stu-id="78662-239">The preceding command displays the following output format:</span></span>

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


    https://localhost:5001/people>
    ```

## <a name="test-http-post-requests"></a><span data-ttu-id="78662-240">Testowanie żądań POST protokołu HTTP</span><span class="sxs-lookup"><span data-stu-id="78662-240">Test HTTP POST requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="78662-241">Streszczenie</span><span class="sxs-lookup"><span data-stu-id="78662-241">Synopsis</span></span>

```console
post <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="78662-242">Argumenty</span><span class="sxs-lookup"><span data-stu-id="78662-242">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="78662-243">Parametr trasy, jeśli istnieje, oczekiwany przez skojarzoną metodę akcji kontrolera.</span><span class="sxs-lookup"><span data-stu-id="78662-243">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="78662-244">Opcje</span><span class="sxs-lookup"><span data-stu-id="78662-244">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="78662-245">Przykład</span><span class="sxs-lookup"><span data-stu-id="78662-245">Example</span></span>

<span data-ttu-id="78662-246">Aby wydać żądanie HTTP POST:</span><span class="sxs-lookup"><span data-stu-id="78662-246">To issue an HTTP POST request:</span></span>

1. <span data-ttu-id="78662-247">Uruchom `post` polecenie w punkcie końcowym, który go obsługuje:</span><span class="sxs-lookup"><span data-stu-id="78662-247">Run the `post` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people> post -h Content-Type=application/json
    ```

    <span data-ttu-id="78662-248">W poprzednim poleceniu `Content-Type` nagłówek żądania HTTP jest ustawiany w taki sposób, aby wskazywał typ nośnika treści żądania JSON.</span><span class="sxs-lookup"><span data-stu-id="78662-248">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="78662-249">Domyślny edytor tekstu otwiera plik *. tmp* z szablonem JSON reprezentującym treść żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="78662-249">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="78662-250">Przykład:</span><span class="sxs-lookup"><span data-stu-id="78662-250">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="78662-251">Aby ustawić domyślny edytor tekstu, zobacz sekcję [Ustawianie domyślnego edytora tekstu](#set-the-default-text-editor) .</span><span class="sxs-lookup"><span data-stu-id="78662-251">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="78662-252">Zmodyfikuj szablon JSON, aby spełniał wymagania dotyczące weryfikacji modelu:</span><span class="sxs-lookup"><span data-stu-id="78662-252">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 0,
      "name": "Scott Addie"
    }
    ```

1. <span data-ttu-id="78662-253">Zapisz plik *. tmp* i Zamknij Edytor tekstu.</span><span class="sxs-lookup"><span data-stu-id="78662-253">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="78662-254">Następujące dane wyjściowe są wyświetlane w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="78662-254">The following output appears in the command shell:</span></span>

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


    https://localhost:5001/people>
    ```

## <a name="test-http-put-requests"></a><span data-ttu-id="78662-255">Testowanie żądań HTTP PUT</span><span class="sxs-lookup"><span data-stu-id="78662-255">Test HTTP PUT requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="78662-256">Streszczenie</span><span class="sxs-lookup"><span data-stu-id="78662-256">Synopsis</span></span>

```console
put <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="78662-257">Argumenty</span><span class="sxs-lookup"><span data-stu-id="78662-257">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="78662-258">Parametr trasy, jeśli istnieje, oczekiwany przez skojarzoną metodę akcji kontrolera.</span><span class="sxs-lookup"><span data-stu-id="78662-258">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="78662-259">Opcje</span><span class="sxs-lookup"><span data-stu-id="78662-259">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="78662-260">Przykład</span><span class="sxs-lookup"><span data-stu-id="78662-260">Example</span></span>

<span data-ttu-id="78662-261">Aby wydać żądanie HTTP PUT:</span><span class="sxs-lookup"><span data-stu-id="78662-261">To issue an HTTP PUT request:</span></span>

1. <span data-ttu-id="78662-262">*Opcjonalnie* : Uruchom `get` polecenie, aby wyświetlić dane przed zmodyfikowaniem:</span><span class="sxs-lookup"><span data-stu-id="78662-262">*Optional* : Run the `get` command to view the data before modifying it:</span></span>

    ```console
    https://localhost:5001/fruits> get
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

1. <span data-ttu-id="78662-263">Uruchom `put` polecenie w punkcie końcowym, który go obsługuje:</span><span class="sxs-lookup"><span data-stu-id="78662-263">Run the `put` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/fruits> put 2 -h Content-Type=application/json
    ```

    <span data-ttu-id="78662-264">W poprzednim poleceniu `Content-Type` nagłówek żądania HTTP jest ustawiany w taki sposób, aby wskazywał typ nośnika treści żądania JSON.</span><span class="sxs-lookup"><span data-stu-id="78662-264">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="78662-265">Domyślny edytor tekstu otwiera plik *. tmp* z szablonem JSON reprezentującym treść żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="78662-265">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="78662-266">Przykład:</span><span class="sxs-lookup"><span data-stu-id="78662-266">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="78662-267">Aby ustawić domyślny edytor tekstu, zobacz sekcję [Ustawianie domyślnego edytora tekstu](#set-the-default-text-editor) .</span><span class="sxs-lookup"><span data-stu-id="78662-267">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="78662-268">Zmodyfikuj szablon JSON, aby spełniał wymagania dotyczące weryfikacji modelu:</span><span class="sxs-lookup"><span data-stu-id="78662-268">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 2,
      "name": "Cherry"
    }
    ```

1. <span data-ttu-id="78662-269">Zapisz plik *. tmp* i Zamknij Edytor tekstu.</span><span class="sxs-lookup"><span data-stu-id="78662-269">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="78662-270">Następujące dane wyjściowe są wyświetlane w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="78662-270">The following output appears in the command shell:</span></span>

    ```console
    [main 2019-06-28T17:27:01.805Z] update#setState idle
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:28:21 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="78662-271">*Opcjonalne* : wydaj `get` polecenie, aby zobaczyć modyfikacje.</span><span class="sxs-lookup"><span data-stu-id="78662-271">*Optional* : Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="78662-272">Na przykład, jeśli wpisano "wiśnię" w edytorze tekstu, `get` zwraca następujące dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="78662-272">For example, if you typed "Cherry" in the text editor, a `get` returns the following output:</span></span>

    ```console
    https://localhost:5001/fruits> get
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


    https://localhost:5001/fruits>
    ```

## <a name="test-http-delete-requests"></a><span data-ttu-id="78662-273">Testowanie żądań HTTP DELETE</span><span class="sxs-lookup"><span data-stu-id="78662-273">Test HTTP DELETE requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="78662-274">Streszczenie</span><span class="sxs-lookup"><span data-stu-id="78662-274">Synopsis</span></span>

```console
delete <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="78662-275">Argumenty</span><span class="sxs-lookup"><span data-stu-id="78662-275">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="78662-276">Parametr trasy, jeśli istnieje, oczekiwany przez skojarzoną metodę akcji kontrolera.</span><span class="sxs-lookup"><span data-stu-id="78662-276">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="78662-277">Opcje</span><span class="sxs-lookup"><span data-stu-id="78662-277">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="78662-278">Przykład</span><span class="sxs-lookup"><span data-stu-id="78662-278">Example</span></span>

<span data-ttu-id="78662-279">Aby wydać żądanie HTTP DELETE:</span><span class="sxs-lookup"><span data-stu-id="78662-279">To issue an HTTP DELETE request:</span></span>

1. <span data-ttu-id="78662-280">*Opcjonalnie* : Uruchom `get` polecenie, aby wyświetlić dane przed zmodyfikowaniem:</span><span class="sxs-lookup"><span data-stu-id="78662-280">*Optional* : Run the `get` command to view the data before modifying it:</span></span>

    ```console
    https://localhost:5001/fruits> get
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

1. <span data-ttu-id="78662-281">Uruchom `delete` polecenie w punkcie końcowym, który go obsługuje:</span><span class="sxs-lookup"><span data-stu-id="78662-281">Run the `delete` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/fruits> delete 2
    ```

    <span data-ttu-id="78662-282">Poprzednie polecenie wyświetla następujący format danych wyjściowych:</span><span class="sxs-lookup"><span data-stu-id="78662-282">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:36:42 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="78662-283">*Opcjonalne* : wydaj `get` polecenie, aby zobaczyć modyfikacje.</span><span class="sxs-lookup"><span data-stu-id="78662-283">*Optional* : Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="78662-284">W tym przykładzie `get` zwraca następujące dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="78662-284">In this example, a `get` returns the following output:</span></span>

    ```console
    https://localhost:5001/fruits> get
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


    https://localhost:5001/fruits>
    ```

## <a name="test-http-patch-requests"></a><span data-ttu-id="78662-285">Testuj żądania poprawek HTTP</span><span class="sxs-lookup"><span data-stu-id="78662-285">Test HTTP PATCH requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="78662-286">Streszczenie</span><span class="sxs-lookup"><span data-stu-id="78662-286">Synopsis</span></span>

```console
patch <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="78662-287">Argumenty</span><span class="sxs-lookup"><span data-stu-id="78662-287">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="78662-288">Parametr trasy, jeśli istnieje, oczekiwany przez skojarzoną metodę akcji kontrolera.</span><span class="sxs-lookup"><span data-stu-id="78662-288">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="78662-289">Opcje</span><span class="sxs-lookup"><span data-stu-id="78662-289">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

## <a name="test-http-head-requests"></a><span data-ttu-id="78662-290">Testowanie żądań głównych HTTP</span><span class="sxs-lookup"><span data-stu-id="78662-290">Test HTTP HEAD requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="78662-291">Streszczenie</span><span class="sxs-lookup"><span data-stu-id="78662-291">Synopsis</span></span>

```console
head <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="78662-292">Argumenty</span><span class="sxs-lookup"><span data-stu-id="78662-292">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="78662-293">Parametr trasy, jeśli istnieje, oczekiwany przez skojarzoną metodę akcji kontrolera.</span><span class="sxs-lookup"><span data-stu-id="78662-293">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="78662-294">Opcje</span><span class="sxs-lookup"><span data-stu-id="78662-294">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="test-http-options-requests"></a><span data-ttu-id="78662-295">Testowe żądania opcji HTTP</span><span class="sxs-lookup"><span data-stu-id="78662-295">Test HTTP OPTIONS requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="78662-296">Streszczenie</span><span class="sxs-lookup"><span data-stu-id="78662-296">Synopsis</span></span>

```console
options <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="78662-297">Argumenty</span><span class="sxs-lookup"><span data-stu-id="78662-297">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="78662-298">Parametr trasy, jeśli istnieje, oczekiwany przez skojarzoną metodę akcji kontrolera.</span><span class="sxs-lookup"><span data-stu-id="78662-298">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="78662-299">Opcje</span><span class="sxs-lookup"><span data-stu-id="78662-299">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="set-http-request-headers"></a><span data-ttu-id="78662-300">Ustawianie nagłówków żądań HTTP</span><span class="sxs-lookup"><span data-stu-id="78662-300">Set HTTP request headers</span></span>

<span data-ttu-id="78662-301">Aby ustawić nagłówek żądania HTTP, należy użyć jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="78662-301">To set an HTTP request header, use one of the following approaches:</span></span>

* <span data-ttu-id="78662-302">Ustaw wartość inline z żądaniem HTTP.</span><span class="sxs-lookup"><span data-stu-id="78662-302">Set inline with the HTTP request.</span></span> <span data-ttu-id="78662-303">Przykład:</span><span class="sxs-lookup"><span data-stu-id="78662-303">For example:</span></span>

    ```console
    https://localhost:5001/people> post -h Content-Type=application/json
    ```
    
    <span data-ttu-id="78662-304">W przypadku wcześniejszego podejścia każdy unikatowy nagłówek żądania HTTP wymaga własnej `-h` opcji.</span><span class="sxs-lookup"><span data-stu-id="78662-304">With the preceding approach, each distinct HTTP request header requires its own `-h` option.</span></span>

* <span data-ttu-id="78662-305">Ustaw przed wysłaniem żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="78662-305">Set before sending the HTTP request.</span></span> <span data-ttu-id="78662-306">Przykład:</span><span class="sxs-lookup"><span data-stu-id="78662-306">For example:</span></span>

    ```console
    https://localhost:5001/people> set header Content-Type application/json
    ```
    
    <span data-ttu-id="78662-307">Podczas ustawiania nagłówka przed wysłaniem żądania nagłówek pozostaje ustawiony na czas trwania sesji powłoki poleceń.</span><span class="sxs-lookup"><span data-stu-id="78662-307">When setting the header before sending a request, the header remains set for the duration of the command shell session.</span></span> <span data-ttu-id="78662-308">Aby wyczyścić nagłówek, podaj wartość pustą.</span><span class="sxs-lookup"><span data-stu-id="78662-308">To clear the header, provide an empty value.</span></span> <span data-ttu-id="78662-309">Przykład:</span><span class="sxs-lookup"><span data-stu-id="78662-309">For example:</span></span>
    
    ```console
    https://localhost:5001/people> set header Content-Type
    ```

## <a name="test-secured-endpoints"></a><span data-ttu-id="78662-310">Testuj zabezpieczone punkty końcowe</span><span class="sxs-lookup"><span data-stu-id="78662-310">Test secured endpoints</span></span>

<span data-ttu-id="78662-311">HttpRepl obsługuje testowanie zabezpieczonych punktów końcowych w następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="78662-311">The HttpRepl supports the testing of secured endpoints in the following ways:</span></span>

* <span data-ttu-id="78662-312">Za pośrednictwem domyślnych poświadczeń zalogowanego użytkownika.</span><span class="sxs-lookup"><span data-stu-id="78662-312">Via the default credentials of the logged in user.</span></span>
* <span data-ttu-id="78662-313">Za pomocą nagłówków żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="78662-313">Through the use of HTTP request headers.</span></span>

### <a name="default-credentials"></a><span data-ttu-id="78662-314">Poświadczenia domyślne</span><span class="sxs-lookup"><span data-stu-id="78662-314">Default credentials</span></span>

<span data-ttu-id="78662-315">Rozważmy internetowy interfejs API, który jest hostowany w usługach IIS i zabezpieczony przy użyciu uwierzytelniania systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="78662-315">Consider a web API you're testing that's hosted in IIS and secured with Windows authentication.</span></span> <span data-ttu-id="78662-316">Użytkownik chce, aby poświadczenia użytkownika uruchomiły narzędzie, aby przepływać do testowanych punktów końcowych HTTP.</span><span class="sxs-lookup"><span data-stu-id="78662-316">You want the credentials of the user running the tool to flow across to the HTTP endpoints being tested.</span></span> <span data-ttu-id="78662-317">Aby przekazać domyślne poświadczenia zalogowanego użytkownika:</span><span class="sxs-lookup"><span data-stu-id="78662-317">To pass the default credentials of the logged in user:</span></span>

1. <span data-ttu-id="78662-318">Ustaw `httpClient.useDefaultCredentials` preferencję na `true` :</span><span class="sxs-lookup"><span data-stu-id="78662-318">Set the `httpClient.useDefaultCredentials` preference to `true`:</span></span>

    ```console
    pref set httpClient.useDefaultCredentials true
    ```

1. <span data-ttu-id="78662-319">Zamknij narzędzie i uruchom je ponownie przed wysłaniem kolejnego żądania do internetowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="78662-319">Exit and restart the tool before sending another request to the web API.</span></span>
 
### <a name="default-proxy-credentials"></a><span data-ttu-id="78662-320">Domyślne poświadczenia serwera proxy</span><span class="sxs-lookup"><span data-stu-id="78662-320">Default proxy credentials</span></span>

<span data-ttu-id="78662-321">Rozważmy scenariusz, w którym testowany internetowy interfejs API znajduje się za serwerem proxy zabezpieczonym przy użyciu uwierzytelniania systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="78662-321">Consider a scenario in which the web API you're testing is behind a proxy secured with Windows authentication.</span></span> <span data-ttu-id="78662-322">Chcesz, aby poświadczenia użytkownika uruchomiły narzędzie do przepływu na serwerze proxy.</span><span class="sxs-lookup"><span data-stu-id="78662-322">You want the credentials of the user running the tool to flow to the proxy.</span></span> <span data-ttu-id="78662-323">Aby przekazać domyślne poświadczenia zalogowanego użytkownika:</span><span class="sxs-lookup"><span data-stu-id="78662-323">To pass the default credentials of the logged in user:</span></span>

1. <span data-ttu-id="78662-324">Ustaw `httpClient.proxy.useDefaultCredentials` preferencję na `true` :</span><span class="sxs-lookup"><span data-stu-id="78662-324">Set the `httpClient.proxy.useDefaultCredentials` preference to `true`:</span></span>

    ```console
    pref set httpClient.proxy.useDefaultCredentials true
    ```

1. <span data-ttu-id="78662-325">Zamknij narzędzie i uruchom je ponownie przed wysłaniem kolejnego żądania do internetowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="78662-325">Exit and restart the tool before sending another request to the web API.</span></span>

### <a name="http-request-headers"></a><span data-ttu-id="78662-326">Nagłówki żądań HTTP</span><span class="sxs-lookup"><span data-stu-id="78662-326">HTTP request headers</span></span>

<span data-ttu-id="78662-327">Przykłady obsługiwanych schematów uwierzytelniania i autoryzacji obejmują:</span><span class="sxs-lookup"><span data-stu-id="78662-327">Examples of supported authentication and authorization schemes include:</span></span>

* <span data-ttu-id="78662-328">uwierzytelnianie podstawowe</span><span class="sxs-lookup"><span data-stu-id="78662-328">basic authentication</span></span>
* <span data-ttu-id="78662-329">Tokeny okaziciela JWT</span><span class="sxs-lookup"><span data-stu-id="78662-329">JWT bearer tokens</span></span>
* <span data-ttu-id="78662-330">Uwierzytelnianie szyfrowane</span><span class="sxs-lookup"><span data-stu-id="78662-330">digest authentication</span></span>

<span data-ttu-id="78662-331">Na przykład można wysłać token okaziciela do punktu końcowego za pomocą następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="78662-331">For example, you can send a bearer token to an endpoint with the following command:</span></span>

```console
set header Authorization "bearer <TOKEN VALUE>"
```

<span data-ttu-id="78662-332">Aby uzyskać dostęp do punktu końcowego hostowanego na platformie Azure lub użyć [interfejsu API REST platformy Azure](/rest/api/azure/), potrzebujesz tokenu okaziciela.</span><span class="sxs-lookup"><span data-stu-id="78662-332">To access an Azure-hosted endpoint or to use the [Azure REST API](/rest/api/azure/), you need a bearer token.</span></span> <span data-ttu-id="78662-333">Wykonaj następujące kroki, aby uzyskać token okaziciela dla subskrypcji platformy Azure za pośrednictwem [interfejsu wiersza polecenia platformy Azure](/cli/azure/).</span><span class="sxs-lookup"><span data-stu-id="78662-333">Use the following steps to obtain a bearer token for your Azure subscription via the [Azure CLI](/cli/azure/).</span></span> <span data-ttu-id="78662-334">HttpRepl ustawia token okaziciela w nagłówku żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="78662-334">The HttpRepl sets the bearer token in an HTTP request header.</span></span> <span data-ttu-id="78662-335">Zostanie pobrana lista Web Apps Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="78662-335">A list of Azure App Service Web Apps is retrieved.</span></span>

1. <span data-ttu-id="78662-336">Zaloguj się do platformy Azure:</span><span class="sxs-lookup"><span data-stu-id="78662-336">Sign in to Azure:</span></span>

    ```azurecli
    az login
    ```

1. <span data-ttu-id="78662-337">Pobierz swój identyfikator subskrypcji za pomocą następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="78662-337">Get your subscription ID with the following command:</span></span>

    ```azurecli
    az account show --query id
    ```

1. <span data-ttu-id="78662-338">Skopiuj identyfikator subskrypcji i uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="78662-338">Copy your subscription ID and run the following command:</span></span>

    ```azurecli
    az account set --subscription "<SUBSCRIPTION ID>"
    ```

1. <span data-ttu-id="78662-339">Pobierz token okaziciela przy użyciu następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="78662-339">Get your bearer token with the following command:</span></span>

    ```azurecli
    az account get-access-token --query accessToken
    ```

1. <span data-ttu-id="78662-340">Nawiązywanie połączenia z interfejsem API REST platformy Azure za pośrednictwem HttpRepl:</span><span class="sxs-lookup"><span data-stu-id="78662-340">Connect to the Azure REST API via the HttpRepl:</span></span>

    ```console
    httprepl https://management.azure.com
    ```

1. <span data-ttu-id="78662-341">Ustawianie `Authorization` nagłówka żądania http:</span><span class="sxs-lookup"><span data-stu-id="78662-341">Set the `Authorization` HTTP request header:</span></span>

    ```console
    https://management.azure.com/> set header Authorization "bearer <ACCESS TOKEN>"
    ```

1. <span data-ttu-id="78662-342">Przejdź do subskrypcji:</span><span class="sxs-lookup"><span data-stu-id="78662-342">Navigate to the subscription:</span></span>

    ```console
    https://management.azure.com/> cd subscriptions/<SUBSCRIPTION ID>
    ```

1. <span data-ttu-id="78662-343">Pobierz listę Azure App Service subskrypcji Web Apps:</span><span class="sxs-lookup"><span data-stu-id="78662-343">Get a list of your subscription's Azure App Service Web Apps:</span></span>

    ```console
    https://management.azure.com/subscriptions/{SUBSCRIPTION ID}> get providers/Microsoft.Web/sites?api-version=2016-08-01
    ```

    <span data-ttu-id="78662-344">Zostanie wyświetlona następująca odpowiedź:</span><span class="sxs-lookup"><span data-stu-id="78662-344">The following response is displayed:</span></span>

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

## <a name="toggle-http-request-display"></a><span data-ttu-id="78662-345">Przełącz wyświetlanie żądań HTTP</span><span class="sxs-lookup"><span data-stu-id="78662-345">Toggle HTTP request display</span></span>

<span data-ttu-id="78662-346">Domyślnie wyświetlanie wysyłanego żądania HTTP jest pomijane.</span><span class="sxs-lookup"><span data-stu-id="78662-346">By default, display of the HTTP request being sent is suppressed.</span></span> <span data-ttu-id="78662-347">Istnieje możliwość zmiany odpowiedniego ustawienia dla czasu trwania sesji powłoki poleceń.</span><span class="sxs-lookup"><span data-stu-id="78662-347">It's possible to change the corresponding setting for the duration of the command shell session.</span></span>

### <a name="enable-request-display"></a><span data-ttu-id="78662-348">Włącz wyświetlanie żądań</span><span class="sxs-lookup"><span data-stu-id="78662-348">Enable request display</span></span>

<span data-ttu-id="78662-349">Wyświetl wysyłane żądanie HTTP, uruchamiając `echo on` polecenie.</span><span class="sxs-lookup"><span data-stu-id="78662-349">View the HTTP request being sent by running the `echo on` command.</span></span> <span data-ttu-id="78662-350">Przykład:</span><span class="sxs-lookup"><span data-stu-id="78662-350">For example:</span></span>

```console
https://localhost:5001/people> echo on
Request echoing is on
```

<span data-ttu-id="78662-351">Kolejne żądania HTTP w bieżącej sesji wyświetlają nagłówki żądań.</span><span class="sxs-lookup"><span data-stu-id="78662-351">Subsequent HTTP requests in the current session display the request headers.</span></span> <span data-ttu-id="78662-352">Przykład:</span><span class="sxs-lookup"><span data-stu-id="78662-352">For example:</span></span>

```console
https://localhost:5001/people> post

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


https://localhost:5001/people>
```

### <a name="disable-request-display"></a><span data-ttu-id="78662-353">Wyłącz wyświetlanie żądań</span><span class="sxs-lookup"><span data-stu-id="78662-353">Disable request display</span></span>

<span data-ttu-id="78662-354">Pomijaj wyświetlanie wysyłanego żądania HTTP przez uruchomienie `echo off` polecenia.</span><span class="sxs-lookup"><span data-stu-id="78662-354">Suppress display of the HTTP request being sent by running the `echo off` command.</span></span> <span data-ttu-id="78662-355">Przykład:</span><span class="sxs-lookup"><span data-stu-id="78662-355">For example:</span></span>

```console
https://localhost:5001/people> echo off
Request echoing is off
```

## <a name="run-a-script"></a><span data-ttu-id="78662-356">Uruchamianie skryptu</span><span class="sxs-lookup"><span data-stu-id="78662-356">Run a script</span></span>

<span data-ttu-id="78662-357">Jeśli często wykonujesz ten sam zestaw poleceń HttpRepl, Rozważ przechowywanie ich w pliku tekstowym.</span><span class="sxs-lookup"><span data-stu-id="78662-357">If you frequently execute the same set of HttpRepl commands, consider storing them in a text file.</span></span> <span data-ttu-id="78662-358">Polecenia w pliku przyjmują ten sam formularz, co polecenia wykonywane ręcznie w wierszu polecenia.</span><span class="sxs-lookup"><span data-stu-id="78662-358">Commands in the file take the same form as commands executed manually on the command line.</span></span> <span data-ttu-id="78662-359">Polecenia mogą być wykonywane w sposób wsadowy przy użyciu `run` polecenia.</span><span class="sxs-lookup"><span data-stu-id="78662-359">The commands can be executed in a batched fashion using the `run` command.</span></span> <span data-ttu-id="78662-360">Przykład:</span><span class="sxs-lookup"><span data-stu-id="78662-360">For example:</span></span>

1. <span data-ttu-id="78662-361">Utwórz plik tekstowy zawierający zestaw poleceń rozdzielanych znakami nowego wiersza.</span><span class="sxs-lookup"><span data-stu-id="78662-361">Create a text file containing a set of newline-delimited commands.</span></span> <span data-ttu-id="78662-362">Aby to zilustrować, rozważ plik *people-script.txt* zawierający następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="78662-362">To illustrate, consider a *people-script.txt* file containing the following commands:</span></span>

    ```text
    set base https://localhost:5001
    ls
    cd People
    ls
    get 1
    ```

1. <span data-ttu-id="78662-363">Wykonaj `run` polecenie, przekazując w ścieżce pliku tekstowego.</span><span class="sxs-lookup"><span data-stu-id="78662-363">Execute the `run` command, passing in the text file's path.</span></span> <span data-ttu-id="78662-364">Przykład:</span><span class="sxs-lookup"><span data-stu-id="78662-364">For example:</span></span>

    ```console
    https://localhost:5001/> run C:\http-repl-scripts\people-script.txt
    ```

    <span data-ttu-id="78662-365">Wyświetlane są następujące dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="78662-365">The following output appears:</span></span>

    ```console
    https://localhost:5001/> set base https://localhost:5001
    Using OpenAPI description at https://localhost:5001/swagger/v1/swagger.json
    
    https://localhost:5001/> ls
    .        []
    Fruits   [get|post]
    People   [get|post]
    
    https://localhost:5001/> cd People
    /People    [get|post]
    
    https://localhost:5001/People> ls
    .      [get|post]
    ..     []
    {id}   [get|put|delete]
    
    https://localhost:5001/People> get 1
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Date: Fri, 12 Jul 2019 19:20:10 GMT
    Server: Kestrel
    Transfer-Encoding: chunked
    
    {
      "id": 1,
      "name": "Scott Hunter"
    }
    
    
    https://localhost:5001/People>
    ```

## <a name="clear-the-output"></a><span data-ttu-id="78662-366">Wyczyść dane wyjściowe</span><span class="sxs-lookup"><span data-stu-id="78662-366">Clear the output</span></span>

<span data-ttu-id="78662-367">Aby usunąć wszystkie dane wyjściowe zapisywane do powłoki poleceń za pomocą narzędzia HttpRepl, uruchom `clear` polecenie lub `cls` .</span><span class="sxs-lookup"><span data-stu-id="78662-367">To remove all output written to the command shell by the HttpRepl tool, run the `clear` or `cls` command.</span></span> <span data-ttu-id="78662-368">Do zilustrowania, Załóżmy, że powłoka poleceń zawiera następujące dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="78662-368">To illustrate, imagine the command shell contains the following output:</span></span>

```console
httprepl https://localhost:5001
(Disconnected)> set base "https://localhost:5001"
Using OpenAPI description at https://localhost:5001/swagger/v1/swagger.json

https://localhost:5001/> ls
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/>
```

<span data-ttu-id="78662-369">Uruchom następujące polecenie, aby wyczyścić dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="78662-369">Run the following command to clear the output:</span></span>

```console
https://localhost:5001/> clear
```

<span data-ttu-id="78662-370">Po uruchomieniu poprzedniego polecenia powłoka poleceń zawiera tylko następujące dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="78662-370">After running the preceding command, the command shell contains only the following output:</span></span>

```console
https://localhost:5001/>
```

## <a name="additional-resources"></a><span data-ttu-id="78662-371">Dodatkowe zasoby</span><span class="sxs-lookup"><span data-stu-id="78662-371">Additional resources</span></span>

* [<span data-ttu-id="78662-372">Żądania interfejsu API REST</span><span class="sxs-lookup"><span data-stu-id="78662-372">REST API requests</span></span>](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)
* [<span data-ttu-id="78662-373">Repozytorium GitHub HttpRepl</span><span class="sxs-lookup"><span data-stu-id="78662-373">HttpRepl GitHub repository</span></span>](https://github.com/dotnet/HttpRepl)
