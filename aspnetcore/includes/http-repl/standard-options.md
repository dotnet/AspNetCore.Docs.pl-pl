* `-F|--no-formatting`

  <span data-ttu-id="696d3-101">Flaga, której obecność pomija formatowanie odpowiedzi HTTP.</span><span class="sxs-lookup"><span data-stu-id="696d3-101">A flag whose presence suppresses HTTP response formatting.</span></span>

* `-h|--header`

  <span data-ttu-id="696d3-102">Ustawia nagłówek żądania HTTP.</span><span class="sxs-lookup"><span data-stu-id="696d3-102">Sets an HTTP request header.</span></span> <span data-ttu-id="696d3-103">Obsługiwane są następujące dwa formaty wartości:</span><span class="sxs-lookup"><span data-stu-id="696d3-103">The following two value formats are supported:</span></span>

  * `{header}={value}`
  * `{header}:{value}`

* `--response:body`

  <span data-ttu-id="696d3-104">Określa plik, do którego powinna zostać zapisywana treść odpowiedzi HTTP.</span><span class="sxs-lookup"><span data-stu-id="696d3-104">Specifies a file to which the HTTP response body should be written.</span></span> <span data-ttu-id="696d3-105">Na przykład `--response:body "C:\response.json"`.</span><span class="sxs-lookup"><span data-stu-id="696d3-105">For example, `--response:body "C:\response.json"`.</span></span> <span data-ttu-id="696d3-106">Plik zostanie utworzony, jeśli nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="696d3-106">The file is created if it doesn't exist.</span></span>

* `--response:headers`

  <span data-ttu-id="696d3-107">Określa plik, do którego mają być zapisywane nagłówki odpowiedzi HTTP.</span><span class="sxs-lookup"><span data-stu-id="696d3-107">Specifies a file to which the HTTP response headers should be written.</span></span> <span data-ttu-id="696d3-108">Na przykład `--response:headers "C:\response.txt"`.</span><span class="sxs-lookup"><span data-stu-id="696d3-108">For example, `--response:headers "C:\response.txt"`.</span></span> <span data-ttu-id="696d3-109">Plik zostanie utworzony, jeśli nie istnieje.</span><span class="sxs-lookup"><span data-stu-id="696d3-109">The file is created if it doesn't exist.</span></span>

* `-s|--streaming`

  <span data-ttu-id="696d3-110">Flaga, której obecność umożliwia przesyłanie strumieniowe odpowiedzi HTTP.</span><span class="sxs-lookup"><span data-stu-id="696d3-110">A flag whose presence enables streaming of the HTTP response.</span></span>
