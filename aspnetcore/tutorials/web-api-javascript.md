---
title: 'Samouczek: Wywołanie ASP.NET core internet api z JavaScript'
author: rick-anderson
description: Dowiedz się, jak wywołać ASP.NET Core web API za pomocą języka JavaScript.
ms.author: riande
ms.custom: mvc
ms.date: 11/26/2019
uid: tutorials/web-api-javascript
ms.openlocfilehash: 2a19a7d16ca8b8f5d6ac8eb99ad919b89f1e368b
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78655254"
---
# <a name="tutorial-call-an-aspnet-core-web-api-with-javascript"></a>Samouczek: Wywołanie ASP.NET core internet api z JavaScript

Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)

W tym samouczku pokazano, jak wywołać ASP.NET Core web API z JavaScript, przy użyciu [interfejsu API pobierania](https://developer.mozilla.org/docs/Web/API/Fetch_API).

::: moniker range="< aspnetcore-3.0"

Aby uzyskać ASP.NET Core 2.2, zobacz wersję 2.2 [Call the web API z JavaScript](xref:tutorials/first-web-api#call-the-web-api-with-javascript).

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a>Wymagania wstępne

* Kompletny [samouczek: Tworzenie internetowego interfejsu API](xref:tutorials/first-web-api)
* Znajomość CSS, HTML i JavaScript

## <a name="call-the-web-api-with-javascript"></a>Wywoływanie internetowego interfejsu API za pomocą języka JavaScript

W tej sekcji dodasz stronę HTML zawierającą formularze do tworzenia elementów do wykonania i zarządzania nimi. Programy obsługi zdarzeń są dołączone do elementów na stronie. Programy obsługi zdarzeń powodują żądania HTTP do metod akcji interfejsu API sieci web. `fetch` Funkcja interfejsu API pobierania inicjuje każde żądanie HTTP.

Funkcja `fetch` zwraca [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) obiektu, który zawiera odpowiedź HTTP `Response` reprezentowane jako obiekt. Typowym wzorcem jest wyodrębnienie treści odpowiedzi `json` JSON `Response` przez wywołanie funkcji na obiekcie. JavaScript aktualizuje stronę ze szczegółami z odpowiedzi internetowego interfejsu API.

Najprostsze `fetch` wywołanie akceptuje pojedynczy parametr reprezentujący trasę. Drugi parametr, znany `init` jako obiekt, jest opcjonalny. `init`służy do konfigurowania żądania HTTP.

1. Skonfiguruj aplikację do [obsługi plików statycznych](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) i [włącz domyślne mapowanie plików](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_). W `Configure` metodzie *Startup.cs*potrzebny jest następujący podświetlony kod:

    [!code-csharp[](first-web-api/samples/3.0/TodoApi/StartupJavaScript.cs?highlight=8-9&name=snippet_configure)]

1. Utwórz folder *wwwroot* w katalogu głównym projektu.

1. Utwórz folder *js* wewnątrz folderu *wwwroot.*

1. Dodaj plik HTML o nazwie *index.html* do folderu *wwwroot.* Zastąp zawartość *index.html* następującymi znacznikami:

    [!code-html[](first-web-api/samples/3.0/TodoApi/wwwroot/index.html)]

1. Dodaj plik JavaScript o nazwie *site.js* do folderu *wwwroot/js.* Zastąp zawartość *site.js* następującym kodem:

    [!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_SiteJs)]

Aby przetestować stronę HTML lokalnie, może być wymagana zmiana ustawień uruchamiania projektu ASP.NET Core:

1. Otwórz *właściwości\launchSettings.json*.
1. Usuń `launchUrl` właściwość, aby wymusić otwarcie aplikacji w *pliku index.html*&mdash;domyślnego pliku projektu.

W tym przykładzie wywołuje wszystkie metody CRUD interfejsu API sieci web. Poniżej przedstawiono objaśnienia żądań interfejsu API sieci web.

### <a name="get-a-list-of-to-do-items"></a>Wyświetlanie listy elementów do wykonania

W poniższym kodzie żądanie HTTP GET jest wysyłane do trasy *api/TodoItems:*

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_GetItems)]

Gdy internetowy interfejs API zwraca kod `_displayItems` stanu pomyślnego, funkcja jest wywoływana. Każdy element do wykonania w parametrze `_displayItems` tablicy akceptowanym przez jest dodawany do tabeli za pomocą przycisków **Edytuj** i **Usuń.** Jeśli żądanie internetowego interfejsu API nie powiedzie się, błąd jest rejestrowany w konsoli przeglądarki.

### <a name="add-a-to-do-item"></a>Dodawanie elementu do wykonania

W poniższym kodzie:

* Zmienna `item` jest zadeklarowana do konstruowania obiektu dosłownej reprezentacji elementu do wykonania.
* Żądanie pobierania jest skonfigurowane z następującymi opcjami:
  * `method`&mdash;określa zlecenie akcji POST HTTP.
  * `body`&mdash;określa reprezentację JSON treści żądania. JSON jest produkowany przez przekazanie dosłownego obiektu przechowywane w `item` funkcji [JSON.stringify.](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify)
  * `headers`&mdash;określa nagłówki `Accept` `Content-Type` żądań i HTTP. Oba nagłówki są `application/json` ustawione tak, aby określić typ nośnika odbierany i wysyłany odpowiednio.
* Żądanie HTTP POST jest wysyłane do trasy *api/TodoItems.*

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_AddItem)]

Gdy internetowy interfejs API zwraca kod `getItems` stanu pomyślny, funkcja jest wywoływana w celu zaktualizowania tabeli HTML. Jeśli żądanie internetowego interfejsu API nie powiedzie się, błąd jest rejestrowany w konsoli przeglądarki.

### <a name="update-a-to-do-item"></a>Aktualizowanie elementu do wykonania

Aktualizowanie elementu do wykonania jest podobne do dodawania jednego; istnieją jednak dwie istotne różnice:

* Trasa jest sufiksem z unikatowym identyfikatorem elementu do zaktualizowania. Na przykład *api/TodoItems/1*.
* Zlecenie akcji HTTP to PUT, zgodnie `method` z opcją.

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_UpdateItem)]

### <a name="delete-a-to-do-item"></a>Usuwanie elementu do wykonania

Aby usunąć element do wykonania, ustaw `method` opcję `DELETE` żądania i określ unikatowy identyfikator elementu w adresie URL.

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_DeleteItem)]

Przejdź do następnego samouczka, aby dowiedzieć się, jak wygenerować strony pomocy interfejsu API sieci Web:

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>

::: moniker-end
