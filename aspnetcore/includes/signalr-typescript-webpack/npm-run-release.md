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
ms.openlocfilehash: 05c94351ee4747813cfa8dc2318a6fc02c3a46bf
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551702"
---
```console
npm run release
```

<span data-ttu-id="16ded-101">To polecenie generuje zasoby po stronie klienta, które mają być obsługiwane podczas uruchamiania aplikacji.</span><span class="sxs-lookup"><span data-stu-id="16ded-101">This command generates the client-side assets to be served when running the app.</span></span> <span data-ttu-id="16ded-102">Elementy zawartości są umieszczane w folderze *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="16ded-102">The assets are placed in the *wwwroot* folder.</span></span>

<span data-ttu-id="16ded-103">Pakiet WebPack ukończył następujące zadania:</span><span class="sxs-lookup"><span data-stu-id="16ded-103">Webpack completed the following tasks:</span></span>

* <span data-ttu-id="16ded-104">Przeczyszczanie zawartości katalogu *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="16ded-104">Purged the contents of the *wwwroot* directory.</span></span>
* <span data-ttu-id="16ded-105">Przekonwertowane TypeScript na JavaScript w procesie znanym jako *transpilation*.</span><span class="sxs-lookup"><span data-stu-id="16ded-105">Converted the TypeScript to JavaScript in a process known as *transpilation*.</span></span>
* <span data-ttu-id="16ded-106">Zniekształcona wygenerowany kod JavaScript, aby zmniejszyć rozmiar pliku w procesie znanym jako *minifikacja*.</span><span class="sxs-lookup"><span data-stu-id="16ded-106">Mangled the generated JavaScript to reduce file size in a process known as *minification*.</span></span>
* <span data-ttu-id="16ded-107">Skopiowano przetworzone pliki JavaScript, CSS i HTML z pliku *src* do katalogu *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="16ded-107">Copied the processed JavaScript, CSS, and HTML files from *src* to the *wwwroot* directory.</span></span>
* <span data-ttu-id="16ded-108">Następujące elementy zostały dodane do pliku *wwwroot/index.html* :</span><span class="sxs-lookup"><span data-stu-id="16ded-108">Injected the following elements into the *wwwroot/index.html* file:</span></span>
  * <span data-ttu-id="16ded-109">`<link>`Tag, który odwołuje się do elementu *wwwroot/Main. \<hash\> . plik CSS* .</span><span class="sxs-lookup"><span data-stu-id="16ded-109">A `<link>` tag, referencing the *wwwroot/main.\<hash\>.css* file.</span></span> <span data-ttu-id="16ded-110">Ten tag jest umieszczany bezpośrednio przed tagiem zamykającym `</head>` .</span><span class="sxs-lookup"><span data-stu-id="16ded-110">This tag is placed immediately before the closing `</head>` tag.</span></span>
  * <span data-ttu-id="16ded-111">`<script>`Tag, który odwołuje się do zminimalizowanego *wwwroot/Main. \<hash\> . plik js* .</span><span class="sxs-lookup"><span data-stu-id="16ded-111">A `<script>` tag, referencing the minified *wwwroot/main.\<hash\>.js* file.</span></span> <span data-ttu-id="16ded-112">Ten tag jest umieszczany bezpośrednio przed tagiem zamykającym `</body>` .</span><span class="sxs-lookup"><span data-stu-id="16ded-112">This tag is placed immediately before the closing `</body>` tag.</span></span>