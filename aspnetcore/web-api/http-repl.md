---
title: Testowanie interfejsów API sieci Web przy użyciu protokołu HTTP REPL
author: scottaddie
description: Dowiedz się, jak przeglądać i testować ASP.NET Core internetowy interfejs API przy użyciu globalnego narzędzia REPL .NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 05/20/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/http-repl
ms.openlocfilehash: ead745ae8843173bb25b94672005cc6ce295db2e
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85403381"
---
# <a name="test-web-apis-with-the-http-repl"></a><span data-ttu-id="cb94c-103">Testowanie interfejsów API sieci Web przy użyciu protokołu HTTP REPL</span><span class="sxs-lookup"><span data-stu-id="cb94c-103">Test web APIs with the HTTP REPL</span></span>

<span data-ttu-id="cb94c-104">Przez [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="cb94c-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="cb94c-105">Pętla HTTP Read-eval-Print (REPL) to:</span><span class="sxs-lookup"><span data-stu-id="cb94c-105">The HTTP Read-Eval-Print Loop (REPL) is:</span></span>

* <span data-ttu-id="cb94c-106">Lekkie, międzyplatformowe narzędzie wiersza polecenia, które jest obsługiwane wszędzie tam, gdzie jest obsługiwane środowisko .NET Core.</span><span class="sxs-lookup"><span data-stu-id="cb94c-106">A lightweight, cross-platform command-line tool that's supported everywhere .NET Core is supported.</span></span>
* <span data-ttu-id="cb94c-107">Służy do wykonywania żądań HTTP do testowania ASP.NET Core interfejsów API sieci Web (oraz non-ASP.NET podstawowych interfejsów API sieci Web) i wyświetlania ich wyników.</span><span class="sxs-lookup"><span data-stu-id="cb94c-107">Used for making HTTP requests to test ASP.NET Core web APIs (and non-ASP.NET Core web APIs) and view their results.</span></span>
* <span data-ttu-id="cb94c-108">Możliwość testowania interfejsów API sieci Web hostowanych w dowolnym środowisku, w tym hosta lokalnego i Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="cb94c-108">Capable of testing web APIs hosted in any environment, including localhost and Azure App Service.</span></span>

<span data-ttu-id="cb94c-109">Obsługiwane są następujące [czasowniki http](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) :</span><span class="sxs-lookup"><span data-stu-id="cb94c-109">The following [HTTP verbs](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods) are supported:</span></span>

* [<span data-ttu-id="cb94c-110">USUNIĘTY</span><span class="sxs-lookup"><span data-stu-id="cb94c-110">DELETE</span></span>](#test-http-delete-requests)
* [<span data-ttu-id="cb94c-111">Pobierz</span><span class="sxs-lookup"><span data-stu-id="cb94c-111">GET</span></span>](#test-http-get-requests)
* [<span data-ttu-id="cb94c-112">MTP</span><span class="sxs-lookup"><span data-stu-id="cb94c-112">HEAD</span></span>](#test-http-head-requests)
* [<span data-ttu-id="cb94c-113">Opcje</span><span class="sxs-lookup"><span data-stu-id="cb94c-113">OPTIONS</span></span>](#test-http-options-requests)
* [<span data-ttu-id="cb94c-114">WYSŁANA</span><span class="sxs-lookup"><span data-stu-id="cb94c-114">PATCH</span></span>](#test-http-patch-requests)
* [<span data-ttu-id="cb94c-115">POUBOJOWEGO</span><span class="sxs-lookup"><span data-stu-id="cb94c-115">POST</span></span>](#test-http-post-requests)
* [<span data-ttu-id="cb94c-116">Ubrani</span><span class="sxs-lookup"><span data-stu-id="cb94c-116">PUT</span></span>](#test-http-put-requests)

<span data-ttu-id="cb94c-117">Aby wykonać te czynności, [Wyświetl lub Pobierz przykładowy ASP.NET Core internetowy interfejs API](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([jak pobrać](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="cb94c-117">To follow along, [view or download the sample ASP.NET Core web API](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/http-repl/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="cb94c-118">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="cb94c-118">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](~/includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="cb94c-119">Instalacja</span><span class="sxs-lookup"><span data-stu-id="cb94c-119">Installation</span></span>

<span data-ttu-id="cb94c-120">Aby zainstalować REPL HTTP, uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="cb94c-120">To install the HTTP REPL, run the following command:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.dotnet-httprepl
```

<span data-ttu-id="cb94c-121">[Narzędzie globalne platformy .NET Core](/dotnet/core/tools/global-tools#install-a-global-tool) jest instalowane z pakietu NuGet [Microsoft. dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) .</span><span class="sxs-lookup"><span data-stu-id="cb94c-121">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.dotnet-httprepl](https://www.nuget.org/packages/Microsoft.dotnet-httprepl) NuGet package.</span></span>

## <a name="usage"></a><span data-ttu-id="cb94c-122">Użycie</span><span class="sxs-lookup"><span data-stu-id="cb94c-122">Usage</span></span>

<span data-ttu-id="cb94c-123">Po pomyślnej instalacji narzędzia Uruchom następujące polecenie, aby uruchomić REPL HTTP:</span><span class="sxs-lookup"><span data-stu-id="cb94c-123">After successful installation of the tool, run the following command to start the HTTP REPL:</span></span>

```console
httprepl
```

<span data-ttu-id="cb94c-124">Aby wyświetlić dostępne polecenia HTTP REPL, Uruchom jedno z następujących poleceń:</span><span class="sxs-lookup"><span data-stu-id="cb94c-124">To view the available HTTP REPL commands, run one of the following commands:</span></span>

```console
httprepl -h
```

```console
httprepl --help
```

<span data-ttu-id="cb94c-125">Wyświetlane są następujące dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="cb94c-125">The following output is displayed:</span></span>

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

<span data-ttu-id="cb94c-126">REPL HTTP oferuje polecenie uzupełniania.</span><span class="sxs-lookup"><span data-stu-id="cb94c-126">The HTTP REPL offers command completion.</span></span> <span data-ttu-id="cb94c-127">Naciśnięcie klawisza <kbd>Tab</kbd> wykonuje iterację na liście poleceń, które uzupełniają wpisane znaki lub punkt końcowy interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="cb94c-127">Pressing the <kbd>Tab</kbd> key iterates through the list of commands that complete the characters or API endpoint that you typed.</span></span> <span data-ttu-id="cb94c-128">W poniższych sekcjach znajduje się opis dostępnych poleceń interfejsu wiersza polecenia.</span><span class="sxs-lookup"><span data-stu-id="cb94c-128">The following sections outline the available CLI commands.</span></span>

## <a name="connect-to-the-web-api"></a><span data-ttu-id="cb94c-129">Nawiązywanie połączenia z interfejsem API sieci Web</span><span class="sxs-lookup"><span data-stu-id="cb94c-129">Connect to the web API</span></span>

<span data-ttu-id="cb94c-130">Połącz się z interfejsem API sieci Web, uruchamiając następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="cb94c-130">Connect to a web API by running the following command:</span></span>

```console
httprepl <ROOT URI>
```

<span data-ttu-id="cb94c-131">`<ROOT URI>`jest podstawowym identyfikatorem URI dla internetowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="cb94c-131">`<ROOT URI>` is the base URI for the web API.</span></span> <span data-ttu-id="cb94c-132">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="cb94c-132">For example:</span></span>

```console
httprepl https://localhost:5001
```

<span data-ttu-id="cb94c-133">Alternatywnie Uruchom następujące polecenie w dowolnym momencie podczas działania REPL HTTP:</span><span class="sxs-lookup"><span data-stu-id="cb94c-133">Alternatively, run the following command at any time while the HTTP REPL is running:</span></span>

```console
connect <ROOT URI>
```

<span data-ttu-id="cb94c-134">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="cb94c-134">For example:</span></span>

```console
(Disconnected)~ connect https://localhost:5001
```

## <a name="manually-point-to-the-swagger-document-for-the-web-api"></a><span data-ttu-id="cb94c-135">Ręcznie wskaż dokument struktury Swagger dla internetowego interfejsu API</span><span class="sxs-lookup"><span data-stu-id="cb94c-135">Manually point to the Swagger document for the web API</span></span>

<span data-ttu-id="cb94c-136">Powyższe polecenie Connect podejmie próbę automatycznego znalezienia dokumentu struktury Swagger.</span><span class="sxs-lookup"><span data-stu-id="cb94c-136">The connect command above will attempt to find the Swagger document automatically.</span></span> <span data-ttu-id="cb94c-137">Jeśli z jakiegoś powodu nie można tego zrobić, możesz określić identyfikator URI dokumentu struktury Swagger dla internetowego interfejsu API przy użyciu `--swagger` opcji:</span><span class="sxs-lookup"><span data-stu-id="cb94c-137">If for some reason it is unable to do so, you can specify the URI of the Swagger document for the web API by using the `--swagger` option:</span></span>

```console
connect <ROOT URI> --swagger <SWAGGER URI>
```

<span data-ttu-id="cb94c-138">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="cb94c-138">For example:</span></span>

```console
(Disconnected)~ connect https://localhost:5001 --swagger /swagger/v1/swagger.json
```

## <a name="navigate-the-web-api"></a><span data-ttu-id="cb94c-139">Nawigowanie po interfejsie API sieci Web</span><span class="sxs-lookup"><span data-stu-id="cb94c-139">Navigate the web API</span></span>

### <a name="view-available-endpoints"></a><span data-ttu-id="cb94c-140">Wyświetl dostępne punkty końcowe</span><span class="sxs-lookup"><span data-stu-id="cb94c-140">View available endpoints</span></span>

<span data-ttu-id="cb94c-141">Aby wyświetlić listę różnych punktów końcowych (kontrolerów) znajdujących się w bieżącej ścieżce adresu internetowego interfejsu API, uruchom `ls` `dir` polecenie lub:</span><span class="sxs-lookup"><span data-stu-id="cb94c-141">To list the different endpoints (controllers) at the current path of the web API address, run the `ls` or `dir` command:</span></span>

```console
https://localhot:5001/~ ls
```

<span data-ttu-id="cb94c-142">Wyświetlany jest następujący format danych wyjściowych:</span><span class="sxs-lookup"><span data-stu-id="cb94c-142">The following output format is displayed:</span></span>

```console
.        []
Fruits   [get|post]
People   [get|post]

https://localhost:5001/~
```

<span data-ttu-id="cb94c-143">Powyższe dane wyjściowe wskazują, że dostępne są dwa kontrolery: `Fruits` i `People` .</span><span class="sxs-lookup"><span data-stu-id="cb94c-143">The preceding output indicates that there are two controllers available: `Fruits` and `People`.</span></span> <span data-ttu-id="cb94c-144">Oba kontrolery obsługują bez parametrów operacje GET i POST HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb94c-144">Both controllers support parameterless HTTP GET and POST operations.</span></span>

<span data-ttu-id="cb94c-145">Przechodzenie do określonego kontrolera ujawnia więcej szczegółów.</span><span class="sxs-lookup"><span data-stu-id="cb94c-145">Navigating into a specific controller reveals more detail.</span></span> <span data-ttu-id="cb94c-146">Na przykład następujące dane wyjściowe polecenia pokazują `Fruits` kontroler obsługuje również operacje Get, PUT i DELETE protokołu HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb94c-146">For example, the following command's output shows the `Fruits` controller also supports HTTP GET, PUT, and DELETE operations.</span></span> <span data-ttu-id="cb94c-147">Każda z tych operacji oczekuje `id` parametru w marszrucie:</span><span class="sxs-lookup"><span data-stu-id="cb94c-147">Each of these operations expects an `id` parameter in the route:</span></span>

```console
https://localhost:5001/fruits~ ls
.      [get|post]
..     []
{id}   [get|put|delete]

https://localhost:5001/fruits~
```

<span data-ttu-id="cb94c-148">Alternatywnie można uruchomić `ui` polecenie, aby otworzyć stronę interfejsu użytkownika programu Swagger interfejsu API sieci Web w przeglądarce.</span><span class="sxs-lookup"><span data-stu-id="cb94c-148">Alternatively, run the `ui` command to open the web API's Swagger UI page in a browser.</span></span> <span data-ttu-id="cb94c-149">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="cb94c-149">For example:</span></span>

```console
https://localhost:5001/~ ui
```

### <a name="navigate-to-an-endpoint"></a><span data-ttu-id="cb94c-150">Przejdź do punktu końcowego</span><span class="sxs-lookup"><span data-stu-id="cb94c-150">Navigate to an endpoint</span></span>

<span data-ttu-id="cb94c-151">Aby przejść do innego punktu końcowego w internetowym interfejsie API, uruchom `cd` polecenie:</span><span class="sxs-lookup"><span data-stu-id="cb94c-151">To navigate to a different endpoint on the web API, run the `cd` command:</span></span>

```console
https://localhost:5001/~ cd people
```

<span data-ttu-id="cb94c-152">W ścieżce następującego `cd` polecenia jest rozróżniana wielkość liter.</span><span class="sxs-lookup"><span data-stu-id="cb94c-152">The path following the `cd` command is case insensitive.</span></span> <span data-ttu-id="cb94c-153">Wyświetlany jest następujący format danych wyjściowych:</span><span class="sxs-lookup"><span data-stu-id="cb94c-153">The following output format is displayed:</span></span>

```console
/people    [get|post]

https://localhost:5001/people~
```

## <a name="customize-the-http-repl"></a><span data-ttu-id="cb94c-154">Dostosowywanie REPL HTTP</span><span class="sxs-lookup"><span data-stu-id="cb94c-154">Customize the HTTP REPL</span></span>

<span data-ttu-id="cb94c-155">Domyślne [kolory](#set-color-preferences) REPL http można dostosować.</span><span class="sxs-lookup"><span data-stu-id="cb94c-155">The HTTP REPL's default [colors](#set-color-preferences) can be customized.</span></span> <span data-ttu-id="cb94c-156">Ponadto można zdefiniować [domyślny edytor tekstu](#set-the-default-text-editor) .</span><span class="sxs-lookup"><span data-stu-id="cb94c-156">Additionally, a [default text editor](#set-the-default-text-editor) can be defined.</span></span> <span data-ttu-id="cb94c-157">Preferencje HTTP REPL są utrwalane w bieżącej sesji i są honorowane w przyszłych sesjach.</span><span class="sxs-lookup"><span data-stu-id="cb94c-157">The HTTP REPL preferences are persisted across the current session and are honored in future sessions.</span></span> <span data-ttu-id="cb94c-158">Po zmodyfikowaniu preferencje są przechowywane w następującym pliku:</span><span class="sxs-lookup"><span data-stu-id="cb94c-158">Once modified, the preferences are stored in the following file:</span></span>

# <a name="linux"></a>[<span data-ttu-id="cb94c-159">Linux</span><span class="sxs-lookup"><span data-stu-id="cb94c-159">Linux</span></span>](#tab/linux)

<span data-ttu-id="cb94c-160">*% HOME%/.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="cb94c-160">*%HOME%/.httpreplprefs*</span></span>

# <a name="macos"></a>[<span data-ttu-id="cb94c-161">macOS</span><span class="sxs-lookup"><span data-stu-id="cb94c-161">macOS</span></span>](#tab/macos)

<span data-ttu-id="cb94c-162">*% HOME%/.httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="cb94c-162">*%HOME%/.httpreplprefs*</span></span>

# <a name="windows"></a>[<span data-ttu-id="cb94c-163">Windows</span><span class="sxs-lookup"><span data-stu-id="cb94c-163">Windows</span></span>](#tab/windows)

<span data-ttu-id="cb94c-164">*% USERPROFILE% \\ . httpreplprefs*</span><span class="sxs-lookup"><span data-stu-id="cb94c-164">*%USERPROFILE%\\.httpreplprefs*</span></span>

---

<span data-ttu-id="cb94c-165">Plik *. httpreplprefs* jest ładowany podczas uruchamiania i nie jest monitorowany pod kątem zmian w czasie wykonywania.</span><span class="sxs-lookup"><span data-stu-id="cb94c-165">The *.httpreplprefs* file is loaded on startup and not monitored for changes at runtime.</span></span> <span data-ttu-id="cb94c-166">Ręczne modyfikacje pliku zaczną obowiązywać dopiero po ponownym uruchomieniu narzędzia.</span><span class="sxs-lookup"><span data-stu-id="cb94c-166">Manual modifications to the file take effect only after restarting the tool.</span></span>

### <a name="view-the-settings"></a><span data-ttu-id="cb94c-167">Wyświetl ustawienia</span><span class="sxs-lookup"><span data-stu-id="cb94c-167">View the settings</span></span>

<span data-ttu-id="cb94c-168">Aby wyświetlić dostępne ustawienia, uruchom `pref get` polecenie.</span><span class="sxs-lookup"><span data-stu-id="cb94c-168">To view the available settings, run the `pref get` command.</span></span> <span data-ttu-id="cb94c-169">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="cb94c-169">For example:</span></span>

```console
https://localhost:5001/~ pref get
```

<span data-ttu-id="cb94c-170">Poprzednie polecenie wyświetla dostępne pary klucz-wartość:</span><span class="sxs-lookup"><span data-stu-id="cb94c-170">The preceding command displays the available key-value pairs:</span></span>

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

### <a name="set-color-preferences"></a><span data-ttu-id="cb94c-171">Ustawianie preferencji koloru</span><span class="sxs-lookup"><span data-stu-id="cb94c-171">Set color preferences</span></span>

<span data-ttu-id="cb94c-172">Kolorowanie odpowiedzi jest obecnie obsługiwane tylko w przypadku formatu JSON.</span><span class="sxs-lookup"><span data-stu-id="cb94c-172">Response colorization is currently supported for JSON only.</span></span> <span data-ttu-id="cb94c-173">Aby dostosować domyślne kolorowanie narzędzi HTTP REPL, Znajdź klucz odpowiadający kolorowi do zmiany.</span><span class="sxs-lookup"><span data-stu-id="cb94c-173">To customize the default HTTP REPL tool coloring, locate the key corresponding to the color to be changed.</span></span> <span data-ttu-id="cb94c-174">Aby uzyskać instrukcje dotyczące znajdowania kluczy, zobacz sekcję [Wyświetlanie ustawień](#view-the-settings) .</span><span class="sxs-lookup"><span data-stu-id="cb94c-174">For instructions on how to find the keys, see the [View the settings](#view-the-settings) section.</span></span> <span data-ttu-id="cb94c-175">Na przykład zmień `colors.json` wartość klucza z na w `Green` `White` następujący sposób:</span><span class="sxs-lookup"><span data-stu-id="cb94c-175">For example, change the `colors.json` key value from `Green` to `White` as follows:</span></span>

```console
https://localhost:5001/people~ pref set colors.json White
```

<span data-ttu-id="cb94c-176">Można używać tylko [dozwolonych kolorów](https://github.com/dotnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) .</span><span class="sxs-lookup"><span data-stu-id="cb94c-176">Only the [allowed colors](https://github.com/dotnet/HttpRepl/blob/01d5c3c3373e98fe566ff5ef8a17c571de880293/src/Microsoft.Repl/ConsoleHandling/AllowedColors.cs) may be used.</span></span> <span data-ttu-id="cb94c-177">Kolejne żądania HTTP wyświetlają dane wyjściowe z nowym kolorem.</span><span class="sxs-lookup"><span data-stu-id="cb94c-177">Subsequent HTTP requests display output with the new coloring.</span></span>

<span data-ttu-id="cb94c-178">Jeśli określone klucze kolorów nie są ustawione, brane są więcej kluczy ogólnych.</span><span class="sxs-lookup"><span data-stu-id="cb94c-178">When specific color keys aren't set, more generic keys are considered.</span></span> <span data-ttu-id="cb94c-179">Aby zademonstrować to zachowanie rezerwowe, należy wziąć pod uwagę następujący przykład:</span><span class="sxs-lookup"><span data-stu-id="cb94c-179">To demonstrate this fallback behavior, consider the following example:</span></span>

* <span data-ttu-id="cb94c-180">Jeśli `colors.json.name` nie ma wartości, `colors.json.string` jest używana.</span><span class="sxs-lookup"><span data-stu-id="cb94c-180">If `colors.json.name` doesn't have a value, `colors.json.string` is used.</span></span>
* <span data-ttu-id="cb94c-181">Jeśli `colors.json.string` nie ma wartości, `colors.json.literal` jest używana.</span><span class="sxs-lookup"><span data-stu-id="cb94c-181">If `colors.json.string` doesn't have a value, `colors.json.literal` is used.</span></span>
* <span data-ttu-id="cb94c-182">Jeśli `colors.json.literal` nie ma wartości, `colors.json` jest używana.</span><span class="sxs-lookup"><span data-stu-id="cb94c-182">If `colors.json.literal` doesn't have a value, `colors.json` is used.</span></span> 
* <span data-ttu-id="cb94c-183">Jeśli `colors.json` nie ma wartości, używany jest domyślny kolor tekstu powłoki poleceń ( `AllowedColors.None` ).</span><span class="sxs-lookup"><span data-stu-id="cb94c-183">If `colors.json` doesn't have a value, the command shell's default text color (`AllowedColors.None`) is used.</span></span>

### <a name="set-indentation-size"></a><span data-ttu-id="cb94c-184">Ustaw rozmiar wcięcia</span><span class="sxs-lookup"><span data-stu-id="cb94c-184">Set indentation size</span></span>

<span data-ttu-id="cb94c-185">Dostosowanie rozmiaru wcięcia odpowiedzi jest obecnie obsługiwane tylko w przypadku formatu JSON.</span><span class="sxs-lookup"><span data-stu-id="cb94c-185">Response indentation size customization is currently supported for JSON only.</span></span> <span data-ttu-id="cb94c-186">Domyślny rozmiar to dwie spacje.</span><span class="sxs-lookup"><span data-stu-id="cb94c-186">The default size is two spaces.</span></span> <span data-ttu-id="cb94c-187">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="cb94c-187">For example:</span></span>

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

<span data-ttu-id="cb94c-188">Aby zmienić rozmiar domyślny, ustaw `formatting.json.indentSize` klucz.</span><span class="sxs-lookup"><span data-stu-id="cb94c-188">To change the default size, set the `formatting.json.indentSize` key.</span></span> <span data-ttu-id="cb94c-189">Na przykład, aby zawsze używać czterech spacji:</span><span class="sxs-lookup"><span data-stu-id="cb94c-189">For example, to always use four spaces:</span></span>

```console
pref set formatting.json.indentSize 4
```

<span data-ttu-id="cb94c-190">Kolejne odpowiedzi przestrzegają ustawień czterech spacji:</span><span class="sxs-lookup"><span data-stu-id="cb94c-190">Subsequent responses honor the setting of four spaces:</span></span>

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

### <a name="set-the-default-text-editor"></a><span data-ttu-id="cb94c-191">Ustawianie domyślnego edytora tekstu</span><span class="sxs-lookup"><span data-stu-id="cb94c-191">Set the default text editor</span></span>

<span data-ttu-id="cb94c-192">Domyślnie REPL HTTP nie ma edytora tekstu skonfigurowanego do użycia.</span><span class="sxs-lookup"><span data-stu-id="cb94c-192">By default, the HTTP REPL has no text editor configured for use.</span></span> <span data-ttu-id="cb94c-193">Aby przetestować metody interfejsu API sieci Web wymagające treści żądania HTTP, należy ustawić domyślny edytor tekstu.</span><span class="sxs-lookup"><span data-stu-id="cb94c-193">To test web API methods requiring an HTTP request body, a default text editor must be set.</span></span> <span data-ttu-id="cb94c-194">Narzędzie HTTP REPL uruchamia skonfigurowany Edytor tekstów wyłącznie na potrzeby redagowania treści żądania.</span><span class="sxs-lookup"><span data-stu-id="cb94c-194">The HTTP REPL tool launches the configured text editor for the sole purpose of composing the request body.</span></span> <span data-ttu-id="cb94c-195">Uruchom następujące polecenie, aby ustawić preferowany Edytor tekstu jako domyślny:</span><span class="sxs-lookup"><span data-stu-id="cb94c-195">Run the following command to set your preferred text editor as the default:</span></span>

```console
pref set editor.command.default "<EXECUTABLE>"
```

<span data-ttu-id="cb94c-196">W poprzednim poleceniu `<EXECUTABLE>` jest pełną ścieżką do pliku wykonywalnego edytora tekstu.</span><span class="sxs-lookup"><span data-stu-id="cb94c-196">In the preceding command, `<EXECUTABLE>` is the full path to the text editor's executable file.</span></span> <span data-ttu-id="cb94c-197">Na przykład uruchom następujące polecenie, aby ustawić Visual Studio Code jako domyślny edytor tekstu:</span><span class="sxs-lookup"><span data-stu-id="cb94c-197">For example, run the following command to set Visual Studio Code as the default text editor:</span></span>

# <a name="linux"></a>[<span data-ttu-id="cb94c-198">Linux</span><span class="sxs-lookup"><span data-stu-id="cb94c-198">Linux</span></span>](#tab/linux)

```console
pref set editor.command.default "/usr/bin/code"
```

# <a name="macos"></a>[<span data-ttu-id="cb94c-199">macOS</span><span class="sxs-lookup"><span data-stu-id="cb94c-199">macOS</span></span>](#tab/macos)

```console
pref set editor.command.default "/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code"
```

# <a name="windows"></a>[<span data-ttu-id="cb94c-200">Windows</span><span class="sxs-lookup"><span data-stu-id="cb94c-200">Windows</span></span>](#tab/windows)

```console
pref set editor.command.default "C:\Program Files\Microsoft VS Code\Code.exe"
```

---

<span data-ttu-id="cb94c-201">Aby uruchomić domyślny edytor tekstu z określonymi argumentami interfejsu wiersza polecenia, należy ustawić `editor.command.default.arguments` klucz.</span><span class="sxs-lookup"><span data-stu-id="cb94c-201">To launch the default text editor with specific CLI arguments, set the `editor.command.default.arguments` key.</span></span> <span data-ttu-id="cb94c-202">Załóżmy na przykład, że Visual Studio Code jest domyślnym edytorem tekstu i zawsze chcesz, aby REPL HTTP mógł otworzyć Visual Studio Code w nowej sesji z wyłączonymi rozszerzeniami.</span><span class="sxs-lookup"><span data-stu-id="cb94c-202">For example, assume Visual Studio Code is the default text editor and that you always want the HTTP REPL to open Visual Studio Code in a new session with extensions disabled.</span></span> <span data-ttu-id="cb94c-203">Uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="cb94c-203">Run the following command:</span></span>

```console
pref set editor.command.default.arguments "--disable-extensions --new-window"
```

### <a name="set-the-swagger-search-paths"></a><span data-ttu-id="cb94c-204">Ustawianie ścieżek wyszukiwania struktury Swagger</span><span class="sxs-lookup"><span data-stu-id="cb94c-204">Set the Swagger search paths</span></span>

<span data-ttu-id="cb94c-205">Domyślnie REPL HTTP ma zestaw ścieżek względnych, których używa do znajdowania dokumentu struktury Swagger podczas wykonywania `connect` polecenia bez `--swagger` opcji.</span><span class="sxs-lookup"><span data-stu-id="cb94c-205">By default, the HTTP REPL has a set of relative paths that it uses to find the Swagger document when executing the `connect` command without the `--swagger` option.</span></span> <span data-ttu-id="cb94c-206">Ścieżki względne są łączone z ścieżkami głównymi i podstawowymi określonymi w `connect` poleceniu.</span><span class="sxs-lookup"><span data-stu-id="cb94c-206">These relative paths are combined with the root and base paths specified in the `connect` command.</span></span> <span data-ttu-id="cb94c-207">Domyślne ścieżki względne to:</span><span class="sxs-lookup"><span data-stu-id="cb94c-207">The default relative paths are:</span></span>

- <span data-ttu-id="cb94c-208">*swagger.jsna*</span><span class="sxs-lookup"><span data-stu-id="cb94c-208">*swagger.json*</span></span>
- <span data-ttu-id="cb94c-209">*Struktura Swagger/V1/swagger.jswłączona*</span><span class="sxs-lookup"><span data-stu-id="cb94c-209">*swagger/v1/swagger.json*</span></span>
- <span data-ttu-id="cb94c-210">*/swagger.jsna*</span><span class="sxs-lookup"><span data-stu-id="cb94c-210">*/swagger.json*</span></span>
- <span data-ttu-id="cb94c-211">*/Swagger/V1/swagger.jsna*</span><span class="sxs-lookup"><span data-stu-id="cb94c-211">*/swagger/v1/swagger.json*</span></span>

<span data-ttu-id="cb94c-212">Aby użyć innego zestawu ścieżek wyszukiwania w środowisku, ustaw `swagger.searchPaths` preferencję.</span><span class="sxs-lookup"><span data-stu-id="cb94c-212">To use a different set of search paths in your environment, set the `swagger.searchPaths` preference.</span></span> <span data-ttu-id="cb94c-213">Wartość musi być rozdzielaną potokami listą ścieżek względnych.</span><span class="sxs-lookup"><span data-stu-id="cb94c-213">The value must be a pipe-delimited list of relative paths.</span></span> <span data-ttu-id="cb94c-214">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="cb94c-214">For example:</span></span>

```console
pref set swagger.searchPaths "swagger/v2/swagger.json|swagger/v3/swagger.json"
```

## <a name="test-http-get-requests"></a><span data-ttu-id="cb94c-215">Testuj żądania HTTP GET</span><span class="sxs-lookup"><span data-stu-id="cb94c-215">Test HTTP GET requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="cb94c-216">Streszczenie</span><span class="sxs-lookup"><span data-stu-id="cb94c-216">Synopsis</span></span>

```console
get <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="cb94c-217">Argumenty</span><span class="sxs-lookup"><span data-stu-id="cb94c-217">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="cb94c-218">Parametr trasy, jeśli istnieje, oczekiwany przez skojarzoną metodę akcji kontrolera.</span><span class="sxs-lookup"><span data-stu-id="cb94c-218">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="cb94c-219">Opcje</span><span class="sxs-lookup"><span data-stu-id="cb94c-219">Options</span></span>

<span data-ttu-id="cb94c-220">Następujące opcje są dostępne dla `get` polecenia:</span><span class="sxs-lookup"><span data-stu-id="cb94c-220">The following options are available for the `get` command:</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="cb94c-221">Przykład</span><span class="sxs-lookup"><span data-stu-id="cb94c-221">Example</span></span>

<span data-ttu-id="cb94c-222">Aby wydać żądanie HTTP GET:</span><span class="sxs-lookup"><span data-stu-id="cb94c-222">To issue an HTTP GET request:</span></span>

1. <span data-ttu-id="cb94c-223">Uruchom `get` polecenie w punkcie końcowym, który go obsługuje:</span><span class="sxs-lookup"><span data-stu-id="cb94c-223">Run the `get` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people~ get
    ```

    <span data-ttu-id="cb94c-224">Poprzednie polecenie wyświetla następujący format danych wyjściowych:</span><span class="sxs-lookup"><span data-stu-id="cb94c-224">The preceding command displays the following output format:</span></span>

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

1. <span data-ttu-id="cb94c-225">Pobierz konkretny rekord, przekazując parametr do `get` polecenia:</span><span class="sxs-lookup"><span data-stu-id="cb94c-225">Retrieve a specific record by passing a parameter to the `get` command:</span></span>

    ```console
    https://localhost:5001/people~ get 2
    ```

    <span data-ttu-id="cb94c-226">Poprzednie polecenie wyświetla następujący format danych wyjściowych:</span><span class="sxs-lookup"><span data-stu-id="cb94c-226">The preceding command displays the following output format:</span></span>

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

## <a name="test-http-post-requests"></a><span data-ttu-id="cb94c-227">Testowanie żądań POST protokołu HTTP</span><span class="sxs-lookup"><span data-stu-id="cb94c-227">Test HTTP POST requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="cb94c-228">Streszczenie</span><span class="sxs-lookup"><span data-stu-id="cb94c-228">Synopsis</span></span>

```console
post <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="cb94c-229">Argumenty</span><span class="sxs-lookup"><span data-stu-id="cb94c-229">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="cb94c-230">Parametr trasy, jeśli istnieje, oczekiwany przez skojarzoną metodę akcji kontrolera.</span><span class="sxs-lookup"><span data-stu-id="cb94c-230">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="cb94c-231">Opcje</span><span class="sxs-lookup"><span data-stu-id="cb94c-231">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="cb94c-232">Przykład</span><span class="sxs-lookup"><span data-stu-id="cb94c-232">Example</span></span>

<span data-ttu-id="cb94c-233">Aby wydać żądanie HTTP POST:</span><span class="sxs-lookup"><span data-stu-id="cb94c-233">To issue an HTTP POST request:</span></span>

1. <span data-ttu-id="cb94c-234">Uruchom `post` polecenie w punkcie końcowym, który go obsługuje:</span><span class="sxs-lookup"><span data-stu-id="cb94c-234">Run the `post` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/people~ post -h Content-Type=application/json
    ```

    <span data-ttu-id="cb94c-235">W poprzednim poleceniu `Content-Type` nagłówek żądania HTTP jest ustawiany w taki sposób, aby wskazywał typ nośnika treści żądania JSON.</span><span class="sxs-lookup"><span data-stu-id="cb94c-235">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="cb94c-236">Domyślny edytor tekstu otwiera plik *. tmp* z szablonem JSON reprezentującym treść żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb94c-236">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="cb94c-237">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="cb94c-237">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="cb94c-238">Aby ustawić domyślny edytor tekstu, zobacz sekcję [Ustawianie domyślnego edytora tekstu](#set-the-default-text-editor) .</span><span class="sxs-lookup"><span data-stu-id="cb94c-238">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="cb94c-239">Zmodyfikuj szablon JSON, aby spełniał wymagania dotyczące weryfikacji modelu:</span><span class="sxs-lookup"><span data-stu-id="cb94c-239">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 0,
      "name": "Scott Addie"
    }
    ```

1. <span data-ttu-id="cb94c-240">Zapisz plik *. tmp* i Zamknij Edytor tekstu.</span><span class="sxs-lookup"><span data-stu-id="cb94c-240">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="cb94c-241">Następujące dane wyjściowe są wyświetlane w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="cb94c-241">The following output appears in the command shell:</span></span>

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

## <a name="test-http-put-requests"></a><span data-ttu-id="cb94c-242">Testowanie żądań HTTP PUT</span><span class="sxs-lookup"><span data-stu-id="cb94c-242">Test HTTP PUT requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="cb94c-243">Streszczenie</span><span class="sxs-lookup"><span data-stu-id="cb94c-243">Synopsis</span></span>

```console
put <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="cb94c-244">Argumenty</span><span class="sxs-lookup"><span data-stu-id="cb94c-244">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="cb94c-245">Parametr trasy, jeśli istnieje, oczekiwany przez skojarzoną metodę akcji kontrolera.</span><span class="sxs-lookup"><span data-stu-id="cb94c-245">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="cb94c-246">Opcje</span><span class="sxs-lookup"><span data-stu-id="cb94c-246">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

### <a name="example"></a><span data-ttu-id="cb94c-247">Przykład</span><span class="sxs-lookup"><span data-stu-id="cb94c-247">Example</span></span>

<span data-ttu-id="cb94c-248">Aby wydać żądanie HTTP PUT:</span><span class="sxs-lookup"><span data-stu-id="cb94c-248">To issue an HTTP PUT request:</span></span>

1. <span data-ttu-id="cb94c-249">*Opcjonalnie*: Uruchom `get` polecenie, aby wyświetlić dane przed zmodyfikowaniem:</span><span class="sxs-lookup"><span data-stu-id="cb94c-249">*Optional*: Run the `get` command to view the data before modifying it:</span></span>

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

1. <span data-ttu-id="cb94c-250">Uruchom `put` polecenie w punkcie końcowym, który go obsługuje:</span><span class="sxs-lookup"><span data-stu-id="cb94c-250">Run the `put` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/fruits~ put 2 -h Content-Type=application/json
    ```

    <span data-ttu-id="cb94c-251">W poprzednim poleceniu `Content-Type` nagłówek żądania HTTP jest ustawiany w taki sposób, aby wskazywał typ nośnika treści żądania JSON.</span><span class="sxs-lookup"><span data-stu-id="cb94c-251">In the preceding command, the `Content-Type` HTTP request header is set to indicate a request body media type of JSON.</span></span> <span data-ttu-id="cb94c-252">Domyślny edytor tekstu otwiera plik *. tmp* z szablonem JSON reprezentującym treść żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb94c-252">The default text editor opens a *.tmp* file with a JSON template representing the HTTP request body.</span></span> <span data-ttu-id="cb94c-253">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="cb94c-253">For example:</span></span>

    ```json
    {
      "id": 0,
      "name": ""
    }
    ```

    > [!TIP]
    > <span data-ttu-id="cb94c-254">Aby ustawić domyślny edytor tekstu, zobacz sekcję [Ustawianie domyślnego edytora tekstu](#set-the-default-text-editor) .</span><span class="sxs-lookup"><span data-stu-id="cb94c-254">To set the default text editor, see the [Set the default text editor](#set-the-default-text-editor) section.</span></span>

1. <span data-ttu-id="cb94c-255">Zmodyfikuj szablon JSON, aby spełniał wymagania dotyczące weryfikacji modelu:</span><span class="sxs-lookup"><span data-stu-id="cb94c-255">Modify the JSON template to satisfy model validation requirements:</span></span>

    ```json
    {
      "id": 2,
      "name": "Cherry"
    }
    ```

1. <span data-ttu-id="cb94c-256">Zapisz plik *. tmp* i Zamknij Edytor tekstu.</span><span class="sxs-lookup"><span data-stu-id="cb94c-256">Save the *.tmp* file, and close the text editor.</span></span> <span data-ttu-id="cb94c-257">Następujące dane wyjściowe są wyświetlane w powłoce poleceń:</span><span class="sxs-lookup"><span data-stu-id="cb94c-257">The following output appears in the command shell:</span></span>

    ```console
    [main 2019-06-28T17:27:01.805Z] update#setState idle
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:28:21 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="cb94c-258">*Opcjonalne*: wydaj `get` polecenie, aby zobaczyć modyfikacje.</span><span class="sxs-lookup"><span data-stu-id="cb94c-258">*Optional*: Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="cb94c-259">Na przykład, jeśli wpisano "wiśnię" w edytorze tekstu, `get` zwraca następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="cb94c-259">For example, if you typed "Cherry" in the text editor, a `get` returns the following:</span></span>

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

## <a name="test-http-delete-requests"></a><span data-ttu-id="cb94c-260">Testowanie żądań HTTP DELETE</span><span class="sxs-lookup"><span data-stu-id="cb94c-260">Test HTTP DELETE requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="cb94c-261">Streszczenie</span><span class="sxs-lookup"><span data-stu-id="cb94c-261">Synopsis</span></span>

```console
delete <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="cb94c-262">Argumenty</span><span class="sxs-lookup"><span data-stu-id="cb94c-262">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="cb94c-263">Parametr trasy, jeśli istnieje, oczekiwany przez skojarzoną metodę akcji kontrolera.</span><span class="sxs-lookup"><span data-stu-id="cb94c-263">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="cb94c-264">Opcje</span><span class="sxs-lookup"><span data-stu-id="cb94c-264">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

### <a name="example"></a><span data-ttu-id="cb94c-265">Przykład</span><span class="sxs-lookup"><span data-stu-id="cb94c-265">Example</span></span>

<span data-ttu-id="cb94c-266">Aby wydać żądanie HTTP DELETE:</span><span class="sxs-lookup"><span data-stu-id="cb94c-266">To issue an HTTP DELETE request:</span></span>

1. <span data-ttu-id="cb94c-267">*Opcjonalnie*: Uruchom `get` polecenie, aby wyświetlić dane przed zmodyfikowaniem:</span><span class="sxs-lookup"><span data-stu-id="cb94c-267">*Optional*: Run the `get` command to view the data before modifying it:</span></span>

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

1. <span data-ttu-id="cb94c-268">Uruchom `delete` polecenie w punkcie końcowym, który go obsługuje:</span><span class="sxs-lookup"><span data-stu-id="cb94c-268">Run the `delete` command on an endpoint that supports it:</span></span>

    ```console
    https://localhost:5001/fruits~ delete 2
    ```

    <span data-ttu-id="cb94c-269">Poprzednie polecenie wyświetla następujący format danych wyjściowych:</span><span class="sxs-lookup"><span data-stu-id="cb94c-269">The preceding command displays the following output format:</span></span>

    ```console
    HTTP/1.1 204 No Content
    Date: Fri, 28 Jun 2019 17:36:42 GMT
    Server: Kestrel
    ```

1. <span data-ttu-id="cb94c-270">*Opcjonalne*: wydaj `get` polecenie, aby zobaczyć modyfikacje.</span><span class="sxs-lookup"><span data-stu-id="cb94c-270">*Optional*: Issue a `get` command to see the modifications.</span></span> <span data-ttu-id="cb94c-271">W tym przykładzie `get` zwraca następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="cb94c-271">In this example, a `get` returns the following:</span></span>

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

## <a name="test-http-patch-requests"></a><span data-ttu-id="cb94c-272">Testuj żądania poprawek HTTP</span><span class="sxs-lookup"><span data-stu-id="cb94c-272">Test HTTP PATCH requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="cb94c-273">Streszczenie</span><span class="sxs-lookup"><span data-stu-id="cb94c-273">Synopsis</span></span>

```console
patch <PARAMETER> [-c|--content] [-f|--file] [-h|--header] [--no-body] [-F|--no-formatting] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="cb94c-274">Argumenty</span><span class="sxs-lookup"><span data-stu-id="cb94c-274">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="cb94c-275">Parametr trasy, jeśli istnieje, oczekiwany przez skojarzoną metodę akcji kontrolera.</span><span class="sxs-lookup"><span data-stu-id="cb94c-275">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="cb94c-276">Opcje</span><span class="sxs-lookup"><span data-stu-id="cb94c-276">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

[!INCLUDE [HTTP request body CLI options](~/includes/http-repl/requires-body-options.md)]

## <a name="test-http-head-requests"></a><span data-ttu-id="cb94c-277">Testowanie żądań głównych HTTP</span><span class="sxs-lookup"><span data-stu-id="cb94c-277">Test HTTP HEAD requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="cb94c-278">Streszczenie</span><span class="sxs-lookup"><span data-stu-id="cb94c-278">Synopsis</span></span>

```console
head <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="cb94c-279">Argumenty</span><span class="sxs-lookup"><span data-stu-id="cb94c-279">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="cb94c-280">Parametr trasy, jeśli istnieje, oczekiwany przez skojarzoną metodę akcji kontrolera.</span><span class="sxs-lookup"><span data-stu-id="cb94c-280">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="cb94c-281">Opcje</span><span class="sxs-lookup"><span data-stu-id="cb94c-281">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="test-http-options-requests"></a><span data-ttu-id="cb94c-282">Testowe żądania opcji HTTP</span><span class="sxs-lookup"><span data-stu-id="cb94c-282">Test HTTP OPTIONS requests</span></span>

### <a name="synopsis"></a><span data-ttu-id="cb94c-283">Streszczenie</span><span class="sxs-lookup"><span data-stu-id="cb94c-283">Synopsis</span></span>

```console
options <PARAMETER> [-F|--no-formatting] [-h|--header] [--response] [--response:body] [--response:headers] [-s|--streaming]
```

### <a name="arguments"></a><span data-ttu-id="cb94c-284">Argumenty</span><span class="sxs-lookup"><span data-stu-id="cb94c-284">Arguments</span></span>

`PARAMETER`

<span data-ttu-id="cb94c-285">Parametr trasy, jeśli istnieje, oczekiwany przez skojarzoną metodę akcji kontrolera.</span><span class="sxs-lookup"><span data-stu-id="cb94c-285">The route parameter, if any, expected by the associated controller action method.</span></span>

### <a name="options"></a><span data-ttu-id="cb94c-286">Opcje</span><span class="sxs-lookup"><span data-stu-id="cb94c-286">Options</span></span>

[!INCLUDE [standard CLI options](~/includes/http-repl/standard-options.md)]

## <a name="set-http-request-headers"></a><span data-ttu-id="cb94c-287">Ustawianie nagłówków żądań HTTP</span><span class="sxs-lookup"><span data-stu-id="cb94c-287">Set HTTP request headers</span></span>

<span data-ttu-id="cb94c-288">Aby ustawić nagłówek żądania HTTP, należy użyć jednej z następujących metod:</span><span class="sxs-lookup"><span data-stu-id="cb94c-288">To set an HTTP request header, use one of the following approaches:</span></span>

* <span data-ttu-id="cb94c-289">Ustaw wartość inline z żądaniem HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb94c-289">Set inline with the HTTP request.</span></span> <span data-ttu-id="cb94c-290">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="cb94c-290">For example:</span></span>

    ```console
    https://localhost:5001/people~ post -h Content-Type=application/json
    ```
    
    <span data-ttu-id="cb94c-291">W przypadku wcześniejszego podejścia każdy unikatowy nagłówek żądania HTTP wymaga własnej `-h` opcji.</span><span class="sxs-lookup"><span data-stu-id="cb94c-291">With the preceding approach, each distinct HTTP request header requires its own `-h` option.</span></span>

* <span data-ttu-id="cb94c-292">Ustaw przed wysłaniem żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb94c-292">Set before sending the HTTP request.</span></span> <span data-ttu-id="cb94c-293">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="cb94c-293">For example:</span></span>

    ```console
    https://localhost:5001/people~ set header Content-Type application/json
    ```
    
    <span data-ttu-id="cb94c-294">Podczas ustawiania nagłówka przed wysłaniem żądania nagłówek pozostaje ustawiony na czas trwania sesji powłoki poleceń.</span><span class="sxs-lookup"><span data-stu-id="cb94c-294">When setting the header before sending a request, the header remains set for the duration of the command shell session.</span></span> <span data-ttu-id="cb94c-295">Aby wyczyścić nagłówek, podaj wartość pustą.</span><span class="sxs-lookup"><span data-stu-id="cb94c-295">To clear the header, provide an empty value.</span></span> <span data-ttu-id="cb94c-296">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="cb94c-296">For example:</span></span>
    
    ```console
    https://localhost:5001/people~ set header Content-Type
    ```

## <a name="test-secured-endpoints"></a><span data-ttu-id="cb94c-297">Testuj zabezpieczone punkty końcowe</span><span class="sxs-lookup"><span data-stu-id="cb94c-297">Test secured endpoints</span></span>

<span data-ttu-id="cb94c-298">REPL HTTP obsługuje testowanie zabezpieczonych punktów końcowych na dwa sposoby: za pośrednictwem domyślnych poświadczeń zalogowanego użytkownika lub za pomocą nagłówków żądań HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb94c-298">The HTTP REPL supports the testing of secured endpoints in two ways: via the default credentials of the logged in user or through the use of HTTP request headers.</span></span> 

### <a name="default-credentials"></a><span data-ttu-id="cb94c-299">Poświadczenia domyślne</span><span class="sxs-lookup"><span data-stu-id="cb94c-299">Default credentials</span></span>

<span data-ttu-id="cb94c-300">Rozważmy scenariusz, w którym testowa internetowy interfejs API jest hostowany w usługach IIS i jest zabezpieczony przy użyciu uwierzytelniania systemu Windows.</span><span class="sxs-lookup"><span data-stu-id="cb94c-300">Consider a scenario in which the web API you're testing is hosted in IIS and is secured with Windows authentication.</span></span> <span data-ttu-id="cb94c-301">Użytkownik chce, aby poświadczenia użytkownika uruchomiły narzędzie, aby przepływać do testowanych punktów końcowych HTTP.</span><span class="sxs-lookup"><span data-stu-id="cb94c-301">You want the credentials of the user running the tool to flow across to the HTTP endpoints being tested.</span></span> <span data-ttu-id="cb94c-302">Aby przekazać domyślne poświadczenia zalogowanego użytkownika:</span><span class="sxs-lookup"><span data-stu-id="cb94c-302">To pass the default credentials of the logged in user:</span></span>

1. <span data-ttu-id="cb94c-303">Ustaw `httpClient.useDefaultCredentials` preferencję na `true` :</span><span class="sxs-lookup"><span data-stu-id="cb94c-303">Set the `httpClient.useDefaultCredentials` preference to `true`:</span></span>

    ```console
    pref set httpClient.useDefaultCredentials true
    ```

1. <span data-ttu-id="cb94c-304">Zamknij narzędzie i uruchom je ponownie przed wysłaniem kolejnego żądania do internetowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="cb94c-304">Exit and restart the tool before sending another request to the web API.</span></span>

### <a name="http-request-headers"></a><span data-ttu-id="cb94c-305">Nagłówki żądań HTTP</span><span class="sxs-lookup"><span data-stu-id="cb94c-305">HTTP request headers</span></span>

<span data-ttu-id="cb94c-306">Przykłady obsługiwanych schematów uwierzytelniania i autoryzacji obejmują uwierzytelnianie podstawowe, tokeny okaziciela JWT i uwierzytelnianie szyfrowane.</span><span class="sxs-lookup"><span data-stu-id="cb94c-306">Examples of supported authentication and authorization schemes include basic authentication, JWT bearer tokens, and digest authentication.</span></span> <span data-ttu-id="cb94c-307">Na przykład można wysłać token okaziciela do punktu końcowego za pomocą następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="cb94c-307">For example, you can send a bearer token to an endpoint with the following command:</span></span>

```console
set header Authorization "bearer <TOKEN VALUE>"
```

<span data-ttu-id="cb94c-308">Aby uzyskać dostęp do punktu końcowego hostowanego na platformie Azure lub użyć [interfejsu API REST platformy Azure](/rest/api/azure/), potrzebujesz tokenu okaziciela.</span><span class="sxs-lookup"><span data-stu-id="cb94c-308">To access an Azure-hosted endpoint or to use the [Azure REST API](/rest/api/azure/), you need a bearer token.</span></span> <span data-ttu-id="cb94c-309">Wykonaj następujące kroki, aby uzyskać token okaziciela dla subskrypcji platformy Azure za pośrednictwem [interfejsu wiersza polecenia platformy Azure](/cli/azure/).</span><span class="sxs-lookup"><span data-stu-id="cb94c-309">Use the following steps to obtain a bearer token for your Azure subscription via the [Azure CLI](/cli/azure/).</span></span> <span data-ttu-id="cb94c-310">REPL HTTP ustawia token okaziciela w nagłówku żądania HTTP i pobiera listę Web Apps Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="cb94c-310">The HTTP REPL sets the bearer token in an HTTP request header and retrieves a list of Azure App Service Web Apps.</span></span>

1. <span data-ttu-id="cb94c-311">Zaloguj się do platformy Azure:</span><span class="sxs-lookup"><span data-stu-id="cb94c-311">Log in to Azure:</span></span>

    ```azurecli
    az login
    ```

1. <span data-ttu-id="cb94c-312">Pobierz swój identyfikator subskrypcji za pomocą następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="cb94c-312">Get your subscription ID with the following command:</span></span>

    ```azurecli
    az account show --query id
    ```

1. <span data-ttu-id="cb94c-313">Skopiuj identyfikator subskrypcji i uruchom następujące polecenie:</span><span class="sxs-lookup"><span data-stu-id="cb94c-313">Copy your subscription ID and run the following command:</span></span>

    ```azurecli
    az account set --subscription "<SUBSCRIPTION ID>"
    ```

1. <span data-ttu-id="cb94c-314">Pobierz token okaziciela przy użyciu następującego polecenia:</span><span class="sxs-lookup"><span data-stu-id="cb94c-314">Get your bearer token with the following command:</span></span>

    ```azurecli
    az account get-access-token --query accessToken
    ```

1. <span data-ttu-id="cb94c-315">Połącz się z interfejsem API REST platformy Azure za pośrednictwem REPL HTTP:</span><span class="sxs-lookup"><span data-stu-id="cb94c-315">Connect to the Azure REST API via the HTTP REPL:</span></span>

    ```console
    httprepl https://management.azure.com
    ```

1. <span data-ttu-id="cb94c-316">Ustawianie `Authorization` nagłówka żądania http:</span><span class="sxs-lookup"><span data-stu-id="cb94c-316">Set the `Authorization` HTTP request header:</span></span>

    ```console
    https://management.azure.com/> set header Authorization "bearer <ACCESS TOKEN>"
    ```

1. <span data-ttu-id="cb94c-317">Przejdź do subskrypcji:</span><span class="sxs-lookup"><span data-stu-id="cb94c-317">Navigate to the subscription:</span></span>

    ```console
    https://management.azure.com/> cd subscriptions/<SUBSCRIPTION ID>
    ```

1. <span data-ttu-id="cb94c-318">Pobierz listę Azure App Service subskrypcji Web Apps:</span><span class="sxs-lookup"><span data-stu-id="cb94c-318">Get a list of your subscription's Azure App Service Web Apps:</span></span>

    ```console
    https://management.azure.com/subscriptions/{SUBSCRIPTION ID}> get providers/Microsoft.Web/sites?api-version=2016-08-01
    ```

    <span data-ttu-id="cb94c-319">Zostanie wyświetlona następująca odpowiedź:</span><span class="sxs-lookup"><span data-stu-id="cb94c-319">The following response is displayed:</span></span>

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

## <a name="toggle-http-request-display"></a><span data-ttu-id="cb94c-320">Przełącz wyświetlanie żądań HTTP</span><span class="sxs-lookup"><span data-stu-id="cb94c-320">Toggle HTTP request display</span></span>

<span data-ttu-id="cb94c-321">Domyślnie wyświetlanie wysyłanego żądania HTTP jest pomijane.</span><span class="sxs-lookup"><span data-stu-id="cb94c-321">By default, display of the HTTP request being sent is suppressed.</span></span> <span data-ttu-id="cb94c-322">Istnieje możliwość zmiany odpowiedniego ustawienia dla czasu trwania sesji powłoki poleceń.</span><span class="sxs-lookup"><span data-stu-id="cb94c-322">It's possible to change the corresponding setting for the duration of the command shell session.</span></span>

### <a name="enable-request-display"></a><span data-ttu-id="cb94c-323">Włącz wyświetlanie żądań</span><span class="sxs-lookup"><span data-stu-id="cb94c-323">Enable request display</span></span>

<span data-ttu-id="cb94c-324">Wyświetl wysyłane żądanie HTTP, uruchamiając `echo on` polecenie.</span><span class="sxs-lookup"><span data-stu-id="cb94c-324">View the HTTP request being sent by running the `echo on` command.</span></span> <span data-ttu-id="cb94c-325">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="cb94c-325">For example:</span></span>

```console
https://localhost:5001/people~ echo on
Request echoing is on
```

<span data-ttu-id="cb94c-326">Kolejne żądania HTTP w bieżącej sesji wyświetlają nagłówki żądań.</span><span class="sxs-lookup"><span data-stu-id="cb94c-326">Subsequent HTTP requests in the current session display the request headers.</span></span> <span data-ttu-id="cb94c-327">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="cb94c-327">For example:</span></span>

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

### <a name="disable-request-display"></a><span data-ttu-id="cb94c-328">Wyłącz wyświetlanie żądań</span><span class="sxs-lookup"><span data-stu-id="cb94c-328">Disable request display</span></span>

<span data-ttu-id="cb94c-329">Pomijaj wyświetlanie wysyłanego żądania HTTP przez uruchomienie `echo off` polecenia.</span><span class="sxs-lookup"><span data-stu-id="cb94c-329">Suppress display of the HTTP request being sent by running the `echo off` command.</span></span> <span data-ttu-id="cb94c-330">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="cb94c-330">For example:</span></span>

```console
https://localhost:5001/people~ echo off
Request echoing is off
```

## <a name="run-a-script"></a><span data-ttu-id="cb94c-331">Uruchamianie skryptu</span><span class="sxs-lookup"><span data-stu-id="cb94c-331">Run a script</span></span>

<span data-ttu-id="cb94c-332">Jeśli często wykonujesz ten sam zestaw poleceń HTTP REPL, Rozważ przechowywanie ich w pliku tekstowym.</span><span class="sxs-lookup"><span data-stu-id="cb94c-332">If you frequently execute the same set of HTTP REPL commands, consider storing them in a text file.</span></span> <span data-ttu-id="cb94c-333">Polecenia w pliku mają taki sam formularz jak te wykonywane ręcznie w wierszu polecenia.</span><span class="sxs-lookup"><span data-stu-id="cb94c-333">Commands in the file take the same form as those executed manually on the command line.</span></span> <span data-ttu-id="cb94c-334">Polecenia mogą być wykonywane w sposób wsadowy przy użyciu `run` polecenia.</span><span class="sxs-lookup"><span data-stu-id="cb94c-334">The commands can be executed in a batched fashion using the `run` command.</span></span> <span data-ttu-id="cb94c-335">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="cb94c-335">For example:</span></span>

1. <span data-ttu-id="cb94c-336">Utwórz plik tekstowy zawierający zestaw poleceń rozdzielanych znakami nowego wiersza.</span><span class="sxs-lookup"><span data-stu-id="cb94c-336">Create a text file containing a set of newline-delimited commands.</span></span> <span data-ttu-id="cb94c-337">Aby to zilustrować, rozważ plik *people-script.txt* zawierający następujące polecenia:</span><span class="sxs-lookup"><span data-stu-id="cb94c-337">To illustrate, consider a *people-script.txt* file containing the following commands:</span></span>

    ```text
    set base https://localhost:5001
    ls
    cd People
    ls
    get 1
    ```

1. <span data-ttu-id="cb94c-338">Wykonaj `run` polecenie, przekazując w ścieżce pliku tekstowego.</span><span class="sxs-lookup"><span data-stu-id="cb94c-338">Execute the `run` command, passing in the text file's path.</span></span> <span data-ttu-id="cb94c-339">Na przykład:</span><span class="sxs-lookup"><span data-stu-id="cb94c-339">For example:</span></span>

    ```console
    https://localhost:5001/~ run C:\http-repl-scripts\people-script.txt
    ```

    <span data-ttu-id="cb94c-340">Zostaną wyświetlone następujące dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="cb94c-340">The following output appears:</span></span>

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

## <a name="clear-the-output"></a><span data-ttu-id="cb94c-341">Wyczyść dane wyjściowe</span><span class="sxs-lookup"><span data-stu-id="cb94c-341">Clear the output</span></span>

<span data-ttu-id="cb94c-342">Aby usunąć wszystkie dane wyjściowe zapisywane do powłoki poleceń za pomocą narzędzia HTTP REPL, uruchom `clear` polecenie lub `cls` .</span><span class="sxs-lookup"><span data-stu-id="cb94c-342">To remove all output written to the command shell by the HTTP REPL tool, run the `clear` or `cls` command.</span></span> <span data-ttu-id="cb94c-343">Do zilustrowania, Załóżmy, że powłoka poleceń zawiera następujące dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="cb94c-343">To illustrate, imagine the command shell contains the following output:</span></span>

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

<span data-ttu-id="cb94c-344">Uruchom następujące polecenie, aby wyczyścić dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="cb94c-344">Run the following command to clear the output:</span></span>

```console
https://localhost:5001/~ clear
```

<span data-ttu-id="cb94c-345">Po uruchomieniu poprzedniego polecenia powłoka poleceń zawiera tylko następujące dane wyjściowe:</span><span class="sxs-lookup"><span data-stu-id="cb94c-345">After running the preceding command, the command shell contains only the following output:</span></span>

```console
https://localhost:5001/~
```

## <a name="additional-resources"></a><span data-ttu-id="cb94c-346">Zasoby dodatkowe</span><span class="sxs-lookup"><span data-stu-id="cb94c-346">Additional resources</span></span>

* [<span data-ttu-id="cb94c-347">Żądania interfejsu API REST</span><span class="sxs-lookup"><span data-stu-id="cb94c-347">REST API requests</span></span>](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#74-supported-methods)
* [<span data-ttu-id="cb94c-348">Repozytorium usługi GitHub HTTP REPL</span><span class="sxs-lookup"><span data-stu-id="cb94c-348">HTTP REPL GitHub repository</span></span>](https://github.com/dotnet/HttpRepl)
