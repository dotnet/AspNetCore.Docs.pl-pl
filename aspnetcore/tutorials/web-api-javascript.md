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
# <a name="tutorial-call-an-aspnet-core-web-api-with-javascript"></a><span data-ttu-id="9fc89-103">Samouczek: Wywołanie ASP.NET core internet api z JavaScript</span><span class="sxs-lookup"><span data-stu-id="9fc89-103">Tutorial: Call an ASP.NET Core web API with JavaScript</span></span>

<span data-ttu-id="9fc89-104">Autor: [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9fc89-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="9fc89-105">W tym samouczku pokazano, jak wywołać ASP.NET Core web API z JavaScript, przy użyciu [interfejsu API pobierania](https://developer.mozilla.org/docs/Web/API/Fetch_API).</span><span class="sxs-lookup"><span data-stu-id="9fc89-105">This tutorial shows how to call an ASP.NET Core web API with JavaScript, using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API).</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9fc89-106">Aby uzyskać ASP.NET Core 2.2, zobacz wersję 2.2 [Call the web API z JavaScript](xref:tutorials/first-web-api#call-the-web-api-with-javascript).</span><span class="sxs-lookup"><span data-stu-id="9fc89-106">For ASP.NET Core 2.2, see the 2.2 version of [Call the web API with JavaScript](xref:tutorials/first-web-api#call-the-web-api-with-javascript).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="9fc89-107">Wymagania wstępne</span><span class="sxs-lookup"><span data-stu-id="9fc89-107">Prerequisites</span></span>

* <span data-ttu-id="9fc89-108">Kompletny [samouczek: Tworzenie internetowego interfejsu API](xref:tutorials/first-web-api)</span><span class="sxs-lookup"><span data-stu-id="9fc89-108">Complete [Tutorial: Create a web API](xref:tutorials/first-web-api)</span></span>
* <span data-ttu-id="9fc89-109">Znajomość CSS, HTML i JavaScript</span><span class="sxs-lookup"><span data-stu-id="9fc89-109">Familiarity with CSS, HTML, and JavaScript</span></span>

## <a name="call-the-web-api-with-javascript"></a><span data-ttu-id="9fc89-110">Wywoływanie internetowego interfejsu API za pomocą języka JavaScript</span><span class="sxs-lookup"><span data-stu-id="9fc89-110">Call the web API with JavaScript</span></span>

<span data-ttu-id="9fc89-111">W tej sekcji dodasz stronę HTML zawierającą formularze do tworzenia elementów do wykonania i zarządzania nimi.</span><span class="sxs-lookup"><span data-stu-id="9fc89-111">In this section, you'll add an HTML page containing forms for creating and managing to-do items.</span></span> <span data-ttu-id="9fc89-112">Programy obsługi zdarzeń są dołączone do elementów na stronie.</span><span class="sxs-lookup"><span data-stu-id="9fc89-112">Event handlers are attached to elements on the page.</span></span> <span data-ttu-id="9fc89-113">Programy obsługi zdarzeń powodują żądania HTTP do metod akcji interfejsu API sieci web.</span><span class="sxs-lookup"><span data-stu-id="9fc89-113">The event handlers result in HTTP requests to the web API's action methods.</span></span> <span data-ttu-id="9fc89-114">`fetch` Funkcja interfejsu API pobierania inicjuje każde żądanie HTTP.</span><span class="sxs-lookup"><span data-stu-id="9fc89-114">The Fetch API's `fetch` function initiates each HTTP request.</span></span>

<span data-ttu-id="9fc89-115">Funkcja `fetch` zwraca [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) obiektu, który zawiera odpowiedź HTTP `Response` reprezentowane jako obiekt.</span><span class="sxs-lookup"><span data-stu-id="9fc89-115">The `fetch` function returns a [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) object, which contains an HTTP response represented as a `Response` object.</span></span> <span data-ttu-id="9fc89-116">Typowym wzorcem jest wyodrębnienie treści odpowiedzi `json` JSON `Response` przez wywołanie funkcji na obiekcie.</span><span class="sxs-lookup"><span data-stu-id="9fc89-116">A common pattern is to extract the JSON response body by invoking the `json` function on the `Response` object.</span></span> <span data-ttu-id="9fc89-117">JavaScript aktualizuje stronę ze szczegółami z odpowiedzi internetowego interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="9fc89-117">JavaScript updates the page with the details from the web API's response.</span></span>

<span data-ttu-id="9fc89-118">Najprostsze `fetch` wywołanie akceptuje pojedynczy parametr reprezentujący trasę.</span><span class="sxs-lookup"><span data-stu-id="9fc89-118">The simplest `fetch` call accepts a single parameter representing the route.</span></span> <span data-ttu-id="9fc89-119">Drugi parametr, znany `init` jako obiekt, jest opcjonalny.</span><span class="sxs-lookup"><span data-stu-id="9fc89-119">A second parameter, known as the `init` object, is optional.</span></span> <span data-ttu-id="9fc89-120">`init`służy do konfigurowania żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="9fc89-120">`init` is used to configure the HTTP request.</span></span>

1. <span data-ttu-id="9fc89-121">Skonfiguruj aplikację do [obsługi plików statycznych](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) i [włącz domyślne mapowanie plików](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="9fc89-121">Configure the app to [serve static files](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [enable default file mapping](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="9fc89-122">W `Configure` metodzie *Startup.cs*potrzebny jest następujący podświetlony kod:</span><span class="sxs-lookup"><span data-stu-id="9fc89-122">The following highlighted code is needed in the `Configure` method of *Startup.cs*:</span></span>

    [!code-csharp[](first-web-api/samples/3.0/TodoApi/StartupJavaScript.cs?highlight=8-9&name=snippet_configure)]

1. <span data-ttu-id="9fc89-123">Utwórz folder *wwwroot* w katalogu głównym projektu.</span><span class="sxs-lookup"><span data-stu-id="9fc89-123">Create a *wwwroot* folder in the project root.</span></span>

1. <span data-ttu-id="9fc89-124">Utwórz folder *js* wewnątrz folderu *wwwroot.*</span><span class="sxs-lookup"><span data-stu-id="9fc89-124">Create a *js* folder inside of the *wwwroot* folder.</span></span>

1. <span data-ttu-id="9fc89-125">Dodaj plik HTML o nazwie *index.html* do folderu *wwwroot.*</span><span class="sxs-lookup"><span data-stu-id="9fc89-125">Add an HTML file named *index.html* to the *wwwroot* folder.</span></span> <span data-ttu-id="9fc89-126">Zastąp zawartość *index.html* następującymi znacznikami:</span><span class="sxs-lookup"><span data-stu-id="9fc89-126">Replace the contents of *index.html* with the following markup:</span></span>

    [!code-html[](first-web-api/samples/3.0/TodoApi/wwwroot/index.html)]

1. <span data-ttu-id="9fc89-127">Dodaj plik JavaScript o nazwie *site.js* do folderu *wwwroot/js.*</span><span class="sxs-lookup"><span data-stu-id="9fc89-127">Add a JavaScript file named *site.js* to the *wwwroot/js* folder.</span></span> <span data-ttu-id="9fc89-128">Zastąp zawartość *site.js* następującym kodem:</span><span class="sxs-lookup"><span data-stu-id="9fc89-128">Replace the contents of *site.js* with the following code:</span></span>

    [!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_SiteJs)]

<span data-ttu-id="9fc89-129">Aby przetestować stronę HTML lokalnie, może być wymagana zmiana ustawień uruchamiania projektu ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="9fc89-129">A change to the ASP.NET Core project's launch settings may be required to test the HTML page locally:</span></span>

1. <span data-ttu-id="9fc89-130">Otwórz *właściwości\launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="9fc89-130">Open *Properties\launchSettings.json*.</span></span>
1. <span data-ttu-id="9fc89-131">Usuń `launchUrl` właściwość, aby wymusić otwarcie aplikacji w *pliku index.html*&mdash;domyślnego pliku projektu.</span><span class="sxs-lookup"><span data-stu-id="9fc89-131">Remove the `launchUrl` property to force the app to open at *index.html*&mdash;the project's default file.</span></span>

<span data-ttu-id="9fc89-132">W tym przykładzie wywołuje wszystkie metody CRUD interfejsu API sieci web.</span><span class="sxs-lookup"><span data-stu-id="9fc89-132">This sample calls all of the CRUD methods of the web API.</span></span> <span data-ttu-id="9fc89-133">Poniżej przedstawiono objaśnienia żądań interfejsu API sieci web.</span><span class="sxs-lookup"><span data-stu-id="9fc89-133">Following are explanations of the web API requests.</span></span>

### <a name="get-a-list-of-to-do-items"></a><span data-ttu-id="9fc89-134">Wyświetlanie listy elementów do wykonania</span><span class="sxs-lookup"><span data-stu-id="9fc89-134">Get a list of to-do items</span></span>

<span data-ttu-id="9fc89-135">W poniższym kodzie żądanie HTTP GET jest wysyłane do trasy *api/TodoItems:*</span><span class="sxs-lookup"><span data-stu-id="9fc89-135">In the following code, an HTTP GET request is sent to the *api/TodoItems* route:</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_GetItems)]

<span data-ttu-id="9fc89-136">Gdy internetowy interfejs API zwraca kod `_displayItems` stanu pomyślnego, funkcja jest wywoływana.</span><span class="sxs-lookup"><span data-stu-id="9fc89-136">When the web API returns a successful status code, the `_displayItems` function is invoked.</span></span> <span data-ttu-id="9fc89-137">Każdy element do wykonania w parametrze `_displayItems` tablicy akceptowanym przez jest dodawany do tabeli za pomocą przycisków **Edytuj** i **Usuń.**</span><span class="sxs-lookup"><span data-stu-id="9fc89-137">Each to-do item in the array parameter accepted by `_displayItems` is added to a table with **Edit** and **Delete** buttons.</span></span> <span data-ttu-id="9fc89-138">Jeśli żądanie internetowego interfejsu API nie powiedzie się, błąd jest rejestrowany w konsoli przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="9fc89-138">If the web API request fails, an error is logged to the browser's console.</span></span>

### <a name="add-a-to-do-item"></a><span data-ttu-id="9fc89-139">Dodawanie elementu do wykonania</span><span class="sxs-lookup"><span data-stu-id="9fc89-139">Add a to-do item</span></span>

<span data-ttu-id="9fc89-140">W poniższym kodzie:</span><span class="sxs-lookup"><span data-stu-id="9fc89-140">In the following code:</span></span>

* <span data-ttu-id="9fc89-141">Zmienna `item` jest zadeklarowana do konstruowania obiektu dosłownej reprezentacji elementu do wykonania.</span><span class="sxs-lookup"><span data-stu-id="9fc89-141">An `item` variable is declared to construct an object literal representation of the to-do item.</span></span>
* <span data-ttu-id="9fc89-142">Żądanie pobierania jest skonfigurowane z następującymi opcjami:</span><span class="sxs-lookup"><span data-stu-id="9fc89-142">A Fetch request is configured with the following options:</span></span>
  * <span data-ttu-id="9fc89-143">`method`&mdash;określa zlecenie akcji POST HTTP.</span><span class="sxs-lookup"><span data-stu-id="9fc89-143">`method`&mdash;specifies the POST HTTP action verb.</span></span>
  * <span data-ttu-id="9fc89-144">`body`&mdash;określa reprezentację JSON treści żądania.</span><span class="sxs-lookup"><span data-stu-id="9fc89-144">`body`&mdash;specifies the JSON representation of the request body.</span></span> <span data-ttu-id="9fc89-145">JSON jest produkowany przez przekazanie dosłownego obiektu przechowywane w `item` funkcji [JSON.stringify.](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify)</span><span class="sxs-lookup"><span data-stu-id="9fc89-145">The JSON is produced by passing the object literal stored in `item` to the [JSON.stringify](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify) function.</span></span>
  * <span data-ttu-id="9fc89-146">`headers`&mdash;określa nagłówki `Accept` `Content-Type` żądań i HTTP.</span><span class="sxs-lookup"><span data-stu-id="9fc89-146">`headers`&mdash;specifies the `Accept` and `Content-Type` HTTP request headers.</span></span> <span data-ttu-id="9fc89-147">Oba nagłówki są `application/json` ustawione tak, aby określić typ nośnika odbierany i wysyłany odpowiednio.</span><span class="sxs-lookup"><span data-stu-id="9fc89-147">Both headers are set to `application/json` to specify the media type being received and sent, respectively.</span></span>
* <span data-ttu-id="9fc89-148">Żądanie HTTP POST jest wysyłane do trasy *api/TodoItems.*</span><span class="sxs-lookup"><span data-stu-id="9fc89-148">An HTTP POST request is sent to the *api/TodoItems* route.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_AddItem)]

<span data-ttu-id="9fc89-149">Gdy internetowy interfejs API zwraca kod `getItems` stanu pomyślny, funkcja jest wywoływana w celu zaktualizowania tabeli HTML.</span><span class="sxs-lookup"><span data-stu-id="9fc89-149">When the web API returns a successful status code, the `getItems` function is invoked to update the HTML table.</span></span> <span data-ttu-id="9fc89-150">Jeśli żądanie internetowego interfejsu API nie powiedzie się, błąd jest rejestrowany w konsoli przeglądarki.</span><span class="sxs-lookup"><span data-stu-id="9fc89-150">If the web API request fails, an error is logged to the browser's console.</span></span>

### <a name="update-a-to-do-item"></a><span data-ttu-id="9fc89-151">Aktualizowanie elementu do wykonania</span><span class="sxs-lookup"><span data-stu-id="9fc89-151">Update a to-do item</span></span>

<span data-ttu-id="9fc89-152">Aktualizowanie elementu do wykonania jest podobne do dodawania jednego; istnieją jednak dwie istotne różnice:</span><span class="sxs-lookup"><span data-stu-id="9fc89-152">Updating a to-do item is similar to adding one; however, there are two significant differences:</span></span>

* <span data-ttu-id="9fc89-153">Trasa jest sufiksem z unikatowym identyfikatorem elementu do zaktualizowania.</span><span class="sxs-lookup"><span data-stu-id="9fc89-153">The route is suffixed with the unique identifier of the item to update.</span></span> <span data-ttu-id="9fc89-154">Na przykład *api/TodoItems/1*.</span><span class="sxs-lookup"><span data-stu-id="9fc89-154">For example, *api/TodoItems/1*.</span></span>
* <span data-ttu-id="9fc89-155">Zlecenie akcji HTTP to PUT, zgodnie `method` z opcją.</span><span class="sxs-lookup"><span data-stu-id="9fc89-155">The HTTP action verb is PUT, as indicated by the `method` option.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_UpdateItem)]

### <a name="delete-a-to-do-item"></a><span data-ttu-id="9fc89-156">Usuwanie elementu do wykonania</span><span class="sxs-lookup"><span data-stu-id="9fc89-156">Delete a to-do item</span></span>

<span data-ttu-id="9fc89-157">Aby usunąć element do wykonania, ustaw `method` opcję `DELETE` żądania i określ unikatowy identyfikator elementu w adresie URL.</span><span class="sxs-lookup"><span data-stu-id="9fc89-157">To delete a to-do item, set the request's `method` option to `DELETE` and specify the item's unique identifier in the URL.</span></span>

[!code-javascript[](first-web-api/samples/3.0/TodoApi/wwwroot/js/site.js?name=snippet_DeleteItem)]

<span data-ttu-id="9fc89-158">Przejdź do następnego samouczka, aby dowiedzieć się, jak wygenerować strony pomocy interfejsu API sieci Web:</span><span class="sxs-lookup"><span data-stu-id="9fc89-158">Advance to the next tutorial to learn how to generate web API help pages:</span></span>

> [!div class="nextstepaction"]
> <xref:tutorials/get-started-with-swashbuckle>

::: moniker-end
