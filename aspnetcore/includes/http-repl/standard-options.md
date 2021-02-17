---
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
ms.openlocfilehash: 30c4c469453f0202fe5310dbe00b3d7214f49b5a
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551945"
---
* `-F|--no-formatting`

  <span data-ttu-id="d028c-101">Flaga, której obecność pomija formatowanie odpowiedzi HTTP.</span><span class="sxs-lookup"><span data-stu-id="d028c-101">A flag whose presence suppresses HTTP response formatting.</span></span>

* `-h|--header`

  <span data-ttu-id="d028c-102">Ustawia nagłówek żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="d028c-102">Sets an HTTP request header.</span></span> <span data-ttu-id="d028c-103">Obsługiwane są następujące dwa formaty wartości:</span><span class="sxs-lookup"><span data-stu-id="d028c-103">The following two value formats are supported:</span></span>

  * `{header}={value}`
  * `{header}:{value}`

* `--response:body`

  <span data-ttu-id="d028c-104">Określa plik, do którego powinna zostać zapisywana treść odpowiedzi HTTP.</span><span class="sxs-lookup"><span data-stu-id="d028c-104">Specifies a file to which the HTTP response body should be written.</span></span> <span data-ttu-id="d028c-105">Na przykład `--response:body "C:\response.json"`.</span><span class="sxs-lookup"><span data-stu-id="d028c-105">For example, `--response:body "C:\response.json"`.</span></span> <span data-ttu-id="d028c-106">Plik zostanie utworzony, jeśli nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="d028c-106">The file is created if it doesn't exist.</span></span>

* `--response:headers`

  <span data-ttu-id="d028c-107">Określa plik, do którego mają być zapisywane nagłówki odpowiedzi HTTP.</span><span class="sxs-lookup"><span data-stu-id="d028c-107">Specifies a file to which the HTTP response headers should be written.</span></span> <span data-ttu-id="d028c-108">Na przykład `--response:headers "C:\response.txt"`.</span><span class="sxs-lookup"><span data-stu-id="d028c-108">For example, `--response:headers "C:\response.txt"`.</span></span> <span data-ttu-id="d028c-109">Plik zostanie utworzony, jeśli nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="d028c-109">The file is created if it doesn't exist.</span></span>

* `-s|--streaming`

  <span data-ttu-id="d028c-110">Flaga, której obecność umożliwia przesyłanie strumieniowe odpowiedzi HTTP.</span><span class="sxs-lookup"><span data-stu-id="d028c-110">A flag whose presence enables streaming of the HTTP response.</span></span>
