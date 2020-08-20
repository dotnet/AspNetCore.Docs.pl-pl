---
title: 'Samouczek: wywoływanie interfejsu API sieci Web ASP.NET Core przy użyciu języka JavaScript'
author: rick-anderson
description: Dowiedz się, jak wywołać interfejs API sieci Web ASP.NET Core przy użyciu języka JavaScript.
ms.author: riande
ms.custom: mvc
ms.date: 11/26/2019
no-loc:
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
uid: tutorials/web-api-javascript
ms.openlocfilehash: 3d097d724ac5e2313d24f1f06e47b881f29a4a98
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633802"
---
# <a name="tutorial-call-an-aspnet-core-web-api-with-javascript"></a>Samouczek: wywoływanie interfejsu API sieci Web ASP.NET Core przy użyciu języka JavaScript

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

W tym samouczku pokazano, jak wywołać interfejs API sieci Web ASP.NET Core przy użyciu [interfejsu API pobierania](https://developer.mozilla.org/docs/Web/API/Fetch_API).

::: moniker range="< aspnetcore-3.0"

Aby uzyskać ASP.NET Core 2,2, zobacz wersja 2,2 [wywołania interfejsu API sieci Web przy użyciu języka JavaScript](xref:tutorials/first-web-api#call-the-web-api-with-javascript).

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a>Wymagania wstępne

* Kompletny [Samouczek: Tworzenie internetowego interfejsu API](xref:tutorials/first-web-api)
* Znajomość stylów CSS, HTML i JavaScript

## <a name="call-the-web-api-with-javascript"></a>Wywoływanie interfejsu API sieci Web przy użyciu języka JavaScript

W tej sekcji dodasz stronę HTML zawierającą formularze do tworzenia elementów do wykonania i zarządzania nimi. Programy obsługi zdarzeń są dołączone do elementów na stronie. Programy obsługi zdarzeń powodują żądania HTTP do metod akcji internetowego interfejsu API. Funkcja pobierania interfejsu API `fetch` inicjuje każde żądanie HTTP.

`fetch`Funkcja zwraca obiekt [obietnicy](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) , który zawiera odpowiedź HTTP reprezentowane jako `Response` obiekt. Typowym wzorcem jest wyodrębnienie treści odpowiedzi JSON przez wywołanie `json` funkcji w `Response` obiekcie. Język JavaScript aktualizuje stronę ze szczegółowymi informacjami z odpowiedzi internetowego interfejsu API.

Najprostsze `fetch` wywołanie akceptuje pojedynczy parametr reprezentujący trasę. Drugi parametr, znany jako `init` obiekt, jest opcjonalny. `init` służy do konfigurowania żądania HTTP.

1. Skonfiguruj aplikację do [obsługi plików statycznych](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) i [Włącz domyślne mapowanie plików](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_). Następujący wyróżniony kod jest wymagany w `Configure` metodzie *Startup.cs*:

    [!code-csharp[](first-web-api/samples/3.0/TodoApi/StartupJavaScript.cs?highlight=8-9&name=snippet_configure)]

1. Utwórz folder *wwwroot* w katalogu głównym projektu.

1. Utwórz folder *js* w folderze *wwwroot* .

1. Dodaj plik HTML o nazwie *index.html* do folderu *wwwroot* . Zastąp zawartość *index.html* następującym znacznikiem:

    [!code-html[](first-web-api/samples/3.0/TodoApi/wwwroot/index.html)]

1. Dodaj plik języka JavaScript o nazwie *site.js* do folderu *wwwroot/js* . Zastąp zawartość *site.js* następującym kodem:

    [!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_SiteJs)]

Zmiana ustawień uruchamiania projektu ASP.NET Core może być wymagana do lokalnego przetestowania strony HTML:

1. Otwórz *Properties\launchSettings.jsna*.
1. Usuń `launchUrl` Właściwość, aby wymusić otwieranie przez aplikację w *index.html* &mdash; domyślnego pliku projektu.

Ten przykład wywołuje wszystkie metody CRUD internetowego interfejsu API. Poniżej znajdują się wyjaśnienia żądań interfejsu API sieci Web.

### <a name="get-a-list-of-to-do-items"></a>Pobierz listę elementów do wykonania

W poniższym kodzie żądanie HTTP GET jest wysyłane do trasy *API/TodoItems* :

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_GetItems)]

Gdy internetowy interfejs API zwraca kod stanu pomyślnego, `_displayItems` Funkcja jest wywoływana. Każdy element do wykonania w parametrze tablicy akceptowane przez `_displayItems` jest dodawany do tabeli za pomocą przycisków **Edytuj** i **Usuń** . Jeśli żądanie internetowego interfejsu API nie powiedzie się, zostanie zarejestrowany błąd w konsoli przeglądarki.

### <a name="add-a-to-do-item"></a>Dodaj element do wykonania

W poniższym kodzie:

* `item`Zmienna jest zadeklarowana do konstruowania reprezentacji literału obiektu elementu do wykonania.
* Żądanie pobrania jest konfigurowane z następującymi opcjami:
  * `method`&mdash;Określa czasownik akcji POST protokołu HTTP.
  * `body`&mdash;Określa reprezentację treści żądania w formacie JSON. KOD JSON jest generowany przez przekazanie literału obiektu przechowywanego w `item` funkcji [JSON. stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) .
  * `headers`&mdash;Określa `Accept` `Content-Type` nagłówki żądań HTTP i. Oba nagłówki są ustawione na `application/json` , aby określić typ nośnika, który jest odbierany i wysyłany odpowiednio.
* Żądanie HTTP POST jest wysyłane do trasy *API/TodoItems* .

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_AddItem)]

Gdy internetowy interfejs API zwraca kod stanu pomyślnego, `getItems` Funkcja jest wywoływana w celu zaktualizowania tabeli HTML. Jeśli żądanie internetowego interfejsu API nie powiedzie się, zostanie zarejestrowany błąd w konsoli przeglądarki.

### <a name="update-a-to-do-item"></a>Aktualizowanie elementu do wykonania

Aktualizowanie elementu do wykonania jest podobne do dodawania jednego z nich; Istnieją jednak dwie znaczące różnice:

* Trasa ma sufiks z unikatowym identyfikatorem elementu do zaktualizowania. Na przykład *API/TodoItems/1*.
* Zlecenie akcji HTTP jest UMIESZCZAne zgodnie z `method` opcją.

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_UpdateItem)]

### <a name="delete-a-to-do-item"></a>Usuń element do wykonania

Aby usunąć element do wykonania, ustaw `method` opcję żądania na `DELETE` i określ unikatowy identyfikator elementu w adresie URL.

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_DeleteItem)]

Przejdź do następnego samouczka, aby dowiedzieć się, jak generować strony pomocy interfejsu API sieci Web:

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>

::: moniker-end
