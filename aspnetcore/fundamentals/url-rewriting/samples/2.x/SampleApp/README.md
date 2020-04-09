# <a name="aspnet-core-url-rewriting-sample"></a><span data-ttu-id="3a86e-101">Przykładowy przepisanie adresów URL ASP.NET</span><span class="sxs-lookup"><span data-stu-id="3a86e-101">ASP.NET Core URL Rewriting Sample</span></span>

<span data-ttu-id="3a86e-102">W tym przykładzie przedstawiono użycie oprogramowania pośredniczącego ASP.NET Core URL Rewriting.</span><span class="sxs-lookup"><span data-stu-id="3a86e-102">This sample illustrates usage of ASP.NET Core URL Rewriting Middleware.</span></span> <span data-ttu-id="3a86e-103">Aplikacja pokazuje opcje przekierowania adresów URL i przepisywania adresów URL.</span><span class="sxs-lookup"><span data-stu-id="3a86e-103">The app demonstrates URL redirect and URL rewriting options.</span></span>

<span data-ttu-id="3a86e-104">Podczas uruchamiania przykładu odpowiedzi bez pliku zwracają przepisany lub przekierowany adres URL, gdy jedna z reguł jest stosowana do adresu URL żądania.</span><span class="sxs-lookup"><span data-stu-id="3a86e-104">When running the sample, non-file responses return the rewritten or redirected URL when one of the rules is applied to a request URL.</span></span> <span data-ttu-id="3a86e-105">W przykładach pliku XML i pliku tekstowego oprogramowanie pośredniczące plików statycznych obsługuje plik po przepisaniu adresu URL żądania przez oprogramowanie pośredniczące.</span><span class="sxs-lookup"><span data-stu-id="3a86e-105">For the XML and text file examples, Static File Middleware serves the file after the request URL is rewritten by the middleware.</span></span>

## <a name="examples-in-this-sample"></a><span data-ttu-id="3a86e-106">Przykłady w tym przykładzie</span><span class="sxs-lookup"><span data-stu-id="3a86e-106">Examples in this sample</span></span>

* `AddRedirect("redirect-rule/(.*)", "redirected/$1")`
  - <span data-ttu-id="3a86e-107">Kod stanu sukcesu: 302 (Znaleziono)</span><span class="sxs-lookup"><span data-stu-id="3a86e-107">Success status code: 302 (Found)</span></span>
  - <span data-ttu-id="3a86e-108">Przykład (przekierowanie): **/redirect-rule/{capture_group}** do **/redirected/{capture_group}**</span><span class="sxs-lookup"><span data-stu-id="3a86e-108">Example (redirect): **/redirect-rule/{capture_group}** to **/redirected/{capture_group}**</span></span>
* `AddRewrite(@"^rewrite-rule/(\d+)/(\d+)", "rewritten?var1=$1&var2=$2", skipRemainingRules: true)`
  - <span data-ttu-id="3a86e-109">Kod stanu sukcesu: 200 (OK)</span><span class="sxs-lookup"><span data-stu-id="3a86e-109">Success status code: 200 (OK)</span></span>
  - <span data-ttu-id="3a86e-110">Przykład (przepisanie): **/rewrite-rule/{capture_group_1}/{capture_group_2}** do **/rewritten?var1={capture_group_1}&var2={capture_group_2}**</span><span class="sxs-lookup"><span data-stu-id="3a86e-110">Example (rewrite): **/rewrite-rule/{capture_group_1}/{capture_group_2}** to **/rewritten?var1={capture_group_1}&var2={capture_group_2}**</span></span>
* `AddApacheModRewrite(env.ContentRootFileProvider, "ApacheModRewrite.txt")`
  - <span data-ttu-id="3a86e-111">Kod stanu sukcesu: 302 (Znaleziono)</span><span class="sxs-lookup"><span data-stu-id="3a86e-111">Success status code: 302 (Found)</span></span>
  - <span data-ttu-id="3a86e-112">Przykład (przekierowanie): **/apache-mod-rules-redirect/{capture_group}** do **/redirected?id={capture_group}**</span><span class="sxs-lookup"><span data-stu-id="3a86e-112">Example (redirect): **/apache-mod-rules-redirect/{capture_group}** to **/redirected?id={capture_group}**</span></span>
* `AddIISUrlRewrite(env.ContentRootFileProvider, "IISUrlRewrite.xml")`
  - <span data-ttu-id="3a86e-113">Kod stanu sukcesu: 200 (OK)</span><span class="sxs-lookup"><span data-stu-id="3a86e-113">Success status code: 200 (OK)</span></span>
  - <span data-ttu-id="3a86e-114">Przykład (przepisanie): **/iis-rules-rewrite/{capture_group}** na **/rewritten?id={capture_group}**</span><span class="sxs-lookup"><span data-stu-id="3a86e-114">Example (rewrite): **/iis-rules-rewrite/{capture_group}** to **/rewritten?id={capture_group}**</span></span>
* `Add(RedirectXmlFileRequests)`
  - <span data-ttu-id="3a86e-115">Kod statusu sukcesu: 301 (przeniesiony na stałe)</span><span class="sxs-lookup"><span data-stu-id="3a86e-115">Success status code: 301 (Moved Permanently)</span></span>
  - <span data-ttu-id="3a86e-116">Przykład (przekierowanie): **/file.xml** do **/xmlfiles/file.xml**</span><span class="sxs-lookup"><span data-stu-id="3a86e-116">Example (redirect): **/file.xml** to **/xmlfiles/file.xml**</span></span>
* `Add(RewriteTextFileRequests)`
  - <span data-ttu-id="3a86e-117">Kod stanu sukcesu: 200 (OK)</span><span class="sxs-lookup"><span data-stu-id="3a86e-117">Success status code: 200 (OK)</span></span>
  - <span data-ttu-id="3a86e-118">Przykład (przepisanie): **/some_file.txt** do **/file.txt**</span><span class="sxs-lookup"><span data-stu-id="3a86e-118">Example (rewrite): **/some_file.txt** to **/file.txt**</span></span>
* `Add(new RedirectImageRequests(".png", "/png-images")))`<br>`Add(new RedirectImageRequests(".jpg", "/jpg-images")))`
  - <span data-ttu-id="3a86e-119">Kod statusu sukcesu: 301 (przeniesiony na stałe)</span><span class="sxs-lookup"><span data-stu-id="3a86e-119">Success status code: 301 (Moved Permanently)</span></span>
  - <span data-ttu-id="3a86e-120">Przykład (przekierowanie): **/image.png** do **/png-images/image.png**</span><span class="sxs-lookup"><span data-stu-id="3a86e-120">Example (redirect): **/image.png** to **/png-images/image.png**</span></span>
  - <span data-ttu-id="3a86e-121">Przykład (przekierowanie): **/image.jpg** do **/jpg-images/image.jpg**</span><span class="sxs-lookup"><span data-stu-id="3a86e-121">Example (redirect): **/image.jpg** to **/jpg-images/image.jpg**</span></span>

## <a name="use-a-physicalfileprovider"></a><span data-ttu-id="3a86e-122">Używanie pliku fizycznegoProvider</span><span class="sxs-lookup"><span data-stu-id="3a86e-122">Use a PhysicalFileProvider</span></span>

<span data-ttu-id="3a86e-123">`IFileProvider` Można również uzyskać, tworząc `PhysicalFileProvider` a przekazać `AddApacheModRewrite()` do `AddIISUrlRewrite()` i metody:</span><span class="sxs-lookup"><span data-stu-id="3a86e-123">You can also obtain an `IFileProvider` by creating a `PhysicalFileProvider` to pass into the `AddApacheModRewrite()` and `AddIISUrlRewrite()` methods:</span></span>

```csharp
using Microsoft.Extensions.FileProviders;
PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
```

## <a name="secure-redirection-extensions"></a><span data-ttu-id="3a86e-124">Bezpieczne rozszerzenia przekierowania</span><span class="sxs-lookup"><span data-stu-id="3a86e-124">Secure redirection extensions</span></span>

<span data-ttu-id="3a86e-125">Ten przykład `WebHostBuilder` zawiera konfigurację aplikacji do`https://localhost:5001` `https://localhost`używania adresów URL ( , ) i certyfikat testowy *(testCert.pfx*), aby pomóc w eksplorowaniu metod bezpiecznego przekierowania.</span><span class="sxs-lookup"><span data-stu-id="3a86e-125">This sample includes `WebHostBuilder` configuration for the app to use URLs (`https://localhost:5001`, `https://localhost`) and a test certificate (*testCert.pfx*) to assist in exploring the secure redirect methods.</span></span> <span data-ttu-id="3a86e-126">Jeśli serwer ma już przypisany port 443 `https://localhost` lub używany,&mdash;przykład `ListenOptions` nie działa usunąć dla `CreateWebHostBuilder` portu 443 w metodzie pliku *Program.cs* lub unbind portu 443 na serwerze, tak aby Kestrel mógł korzystać z portu.</span><span class="sxs-lookup"><span data-stu-id="3a86e-126">If the server already has port 443 assigned or in use, the `https://localhost` example doesn't work&mdash;remove the `ListenOptions` for port 443 in the `CreateWebHostBuilder` method of the *Program.cs* file or unbind port 443 on the server so that Kestrel can use the port.</span></span>

| <span data-ttu-id="3a86e-127">Metoda</span><span class="sxs-lookup"><span data-stu-id="3a86e-127">Method</span></span>                           | <span data-ttu-id="3a86e-128">Kod stanu</span><span class="sxs-lookup"><span data-stu-id="3a86e-128">Status Code</span></span> |    <span data-ttu-id="3a86e-129">Port</span><span class="sxs-lookup"><span data-stu-id="3a86e-129">Port</span></span>    |
| -------------------------------- | :---------: | :--------: |
| `.AddRedirectToHttpsPermanent()` |     <span data-ttu-id="3a86e-130">301</span><span class="sxs-lookup"><span data-stu-id="3a86e-130">301</span></span>     | <span data-ttu-id="3a86e-131">null (465)</span><span class="sxs-lookup"><span data-stu-id="3a86e-131">null (465)</span></span> |
| `.AddRedirectToHttps()`          |     <span data-ttu-id="3a86e-132">302</span><span class="sxs-lookup"><span data-stu-id="3a86e-132">302</span></span>     | <span data-ttu-id="3a86e-133">null (465)</span><span class="sxs-lookup"><span data-stu-id="3a86e-133">null (465)</span></span> |
| `.AddRedirectToHttps(301)`       |     <span data-ttu-id="3a86e-134">301</span><span class="sxs-lookup"><span data-stu-id="3a86e-134">301</span></span>     | <span data-ttu-id="3a86e-135">null (465)</span><span class="sxs-lookup"><span data-stu-id="3a86e-135">null (465)</span></span> |
| `.AddRedirectToHttps(301, 5001)` |     <span data-ttu-id="3a86e-136">301</span><span class="sxs-lookup"><span data-stu-id="3a86e-136">301</span></span>     |    <span data-ttu-id="3a86e-137">5001</span><span class="sxs-lookup"><span data-stu-id="3a86e-137">5001</span></span>    |
