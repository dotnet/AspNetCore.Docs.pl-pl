# <a name="aspnet-core-url-rewriting-sample"></a><span data-ttu-id="cb4b1-101">Przykład ponownego zapisywania adresu URL ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cb4b1-101">ASP.NET Core URL Rewriting Sample</span></span>

<span data-ttu-id="cb4b1-102">Ten przykład ilustruje użycie ASP.NET Core ponownego zapisywania oprogramowania pośredniczącego w adresie URL.</span><span class="sxs-lookup"><span data-stu-id="cb4b1-102">This sample illustrates usage of ASP.NET Core URL Rewriting Middleware.</span></span> <span data-ttu-id="cb4b1-103">Aplikacja demonstruje opcje przekierowywania adresów URL i zapisywania adresów URL.</span><span class="sxs-lookup"><span data-stu-id="cb4b1-103">The app demonstrates URL redirect and URL rewriting options.</span></span>

<span data-ttu-id="cb4b1-104">Podczas uruchamiania przykładu odpowiedzi nie związane z plikami zwracają ponownie wpisany lub przekierowany adres URL, gdy jedna z reguł jest stosowana do adresu URL żądania.</span><span class="sxs-lookup"><span data-stu-id="cb4b1-104">When running the sample, non-file responses return the rewritten or redirected URL when one of the rules is applied to a request URL.</span></span> <span data-ttu-id="cb4b1-105">W przypadku przykładowych plików XML i tekstowych, oprogramowanie pośredniczące plików statycznych zachowuje plik po przepisaniu adresu URL żądania przez oprogramowanie pośredniczące.</span><span class="sxs-lookup"><span data-stu-id="cb4b1-105">For the XML and text file examples, Static File Middleware serves the file after the request URL is rewritten by the middleware.</span></span>

## <a name="examples-in-this-sample"></a><span data-ttu-id="cb4b1-106">Przykłady w tym przykładzie</span><span class="sxs-lookup"><span data-stu-id="cb4b1-106">Examples in this sample</span></span>

* `AddRedirect("redirect-rule/(.*)", "redirected/$1")`
  - <span data-ttu-id="cb4b1-107">Kod stanu sukcesu: 302 (znaleziono)</span><span class="sxs-lookup"><span data-stu-id="cb4b1-107">Success status code: 302 (Found)</span></span>
  - <span data-ttu-id="cb4b1-108">Przykład (redirect): **/redirect-rule/{capture_group}** do **/redirected/{capture_group}**</span><span class="sxs-lookup"><span data-stu-id="cb4b1-108">Example (redirect): **/redirect-rule/{capture_group}** to **/redirected/{capture_group}**</span></span>
* `AddRewrite(@"^rewrite-rule/(\d+)/(\d+)", "rewritten?var1=$1&var2=$2", skipRemainingRules: true)`
  - <span data-ttu-id="cb4b1-109">Kod stanu sukcesu: 200 (OK)</span><span class="sxs-lookup"><span data-stu-id="cb4b1-109">Success status code: 200 (OK)</span></span>
  - <span data-ttu-id="cb4b1-110">Przykład (Zapisz ponownie): **/rewrite-rule/{capture_group_1}/{capture_group_2}** do **/Rewritten? var1 = {capture_group_1} &var2 = {capture_group_2}**</span><span class="sxs-lookup"><span data-stu-id="cb4b1-110">Example (rewrite): **/rewrite-rule/{capture_group_1}/{capture_group_2}** to **/rewritten?var1={capture_group_1}&var2={capture_group_2}**</span></span>
* `AddApacheModRewrite(env.ContentRootFileProvider, "ApacheModRewrite.txt")`
  - <span data-ttu-id="cb4b1-111">Kod stanu sukcesu: 302 (znaleziono)</span><span class="sxs-lookup"><span data-stu-id="cb4b1-111">Success status code: 302 (Found)</span></span>
  - <span data-ttu-id="cb4b1-112">Przykład (redirect): **/apache-mod-rules-redirect/{capture_group}** do **/redirected? id = {capture_group}**</span><span class="sxs-lookup"><span data-stu-id="cb4b1-112">Example (redirect): **/apache-mod-rules-redirect/{capture_group}** to **/redirected?id={capture_group}**</span></span>
* `AddIISUrlRewrite(env.ContentRootFileProvider, "IISUrlRewrite.xml")`
  - <span data-ttu-id="cb4b1-113">Kod stanu sukcesu: 200 (OK)</span><span class="sxs-lookup"><span data-stu-id="cb4b1-113">Success status code: 200 (OK)</span></span>
  - <span data-ttu-id="cb4b1-114">Przykład (Zapisz ponownie): **/iis-rules-rewrite/{capture_group}** do **/Rewritten? id = {capture_group}**</span><span class="sxs-lookup"><span data-stu-id="cb4b1-114">Example (rewrite): **/iis-rules-rewrite/{capture_group}** to **/rewritten?id={capture_group}**</span></span>
* `Add(RedirectXmlFileRequests)`
  - <span data-ttu-id="cb4b1-115">Kod stanu sukcesu: 301 (trwale przeniesiony)</span><span class="sxs-lookup"><span data-stu-id="cb4b1-115">Success status code: 301 (Moved Permanently)</span></span>
  - <span data-ttu-id="cb4b1-116">Przykład (redirect): **/file.xml** do **/XmlFiles/file.xml**</span><span class="sxs-lookup"><span data-stu-id="cb4b1-116">Example (redirect): **/file.xml** to **/xmlfiles/file.xml**</span></span>
* `Add(RewriteTextFileRequests)`
  - <span data-ttu-id="cb4b1-117">Kod stanu sukcesu: 200 (OK)</span><span class="sxs-lookup"><span data-stu-id="cb4b1-117">Success status code: 200 (OK)</span></span>
  - <span data-ttu-id="cb4b1-118">Przykład (Zapisz ponownie): **/some_file.txt** do **/file.txt**</span><span class="sxs-lookup"><span data-stu-id="cb4b1-118">Example (rewrite): **/some_file.txt** to **/file.txt**</span></span>
* `Add(new RedirectImageRequests(".png", "/png-images")))`<br>`Add(new RedirectImageRequests(".jpg", "/jpg-images")))`
  - <span data-ttu-id="cb4b1-119">Kod stanu sukcesu: 301 (trwale przeniesiony)</span><span class="sxs-lookup"><span data-stu-id="cb4b1-119">Success status code: 301 (Moved Permanently)</span></span>
  - <span data-ttu-id="cb4b1-120">Przykład (redirect): **/image.png** do **/PNG-images/image.png**</span><span class="sxs-lookup"><span data-stu-id="cb4b1-120">Example (redirect): **/image.png** to **/png-images/image.png**</span></span>
  - <span data-ttu-id="cb4b1-121">Przykład (redirect): **/image.jpg** do **/jpg-images/image.jpg**</span><span class="sxs-lookup"><span data-stu-id="cb4b1-121">Example (redirect): **/image.jpg** to **/jpg-images/image.jpg**</span></span>

## <a name="use-a-physicalfileprovider"></a><span data-ttu-id="cb4b1-122">Użyj PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="cb4b1-122">Use a PhysicalFileProvider</span></span>

<span data-ttu-id="cb4b1-123">Możesz również uzyskać, tworząc element, `IFileProvider` `PhysicalFileProvider` Aby przekazać `AddApacheModRewrite()` `AddIISUrlRewrite()` metody i:</span><span class="sxs-lookup"><span data-stu-id="cb4b1-123">You can also obtain an `IFileProvider` by creating a `PhysicalFileProvider` to pass into the `AddApacheModRewrite()` and `AddIISUrlRewrite()` methods:</span></span>

```csharp
using Microsoft.Extensions.FileProviders;
PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
```

## <a name="secure-redirection-extensions"></a><span data-ttu-id="cb4b1-124">Rozszerzenia bezpiecznego przekierowania</span><span class="sxs-lookup"><span data-stu-id="cb4b1-124">Secure redirection extensions</span></span>

<span data-ttu-id="cb4b1-125">Ten przykład obejmuje `WebHostBuilder` konfigurację aplikacji do korzystania z adresów URL ( `https://localhost:5001` , `https://localhost` ) i certyfikatu testowego (*testCert. pfx*) w celu ułatwienia eksplorowania metod bezpiecznego przekierowania.</span><span class="sxs-lookup"><span data-stu-id="cb4b1-125">This sample includes `WebHostBuilder` configuration for the app to use URLs (`https://localhost:5001`, `https://localhost`) and a test certificate (*testCert.pfx*) to assist in exploring the secure redirect methods.</span></span> <span data-ttu-id="cb4b1-126">Jeśli na serwerze jest już przypisany lub używany port 443, to `https://localhost` przykład nie działa &mdash; Usuń `ListenOptions` port dla portu 443 w `CreateWebHostBuilder` metodzie pliku *Program.cs* lub Usuń powiązanie portu 443 na serwerze, aby Kestrel mógł używać portu.</span><span class="sxs-lookup"><span data-stu-id="cb4b1-126">If the server already has port 443 assigned or in use, the `https://localhost` example doesn't work&mdash;remove the `ListenOptions` for port 443 in the `CreateWebHostBuilder` method of the *Program.cs* file or unbind port 443 on the server so that Kestrel can use the port.</span></span>

| <span data-ttu-id="cb4b1-127">Metoda</span><span class="sxs-lookup"><span data-stu-id="cb4b1-127">Method</span></span>                           | <span data-ttu-id="cb4b1-128">Kod stanu</span><span class="sxs-lookup"><span data-stu-id="cb4b1-128">Status Code</span></span> |    <span data-ttu-id="cb4b1-129">Port</span><span class="sxs-lookup"><span data-stu-id="cb4b1-129">Port</span></span>    |
| -------------------------------- | :---------: | :--------: |
| `.AddRedirectToHttpsPermanent()` |     <span data-ttu-id="cb4b1-130">301</span><span class="sxs-lookup"><span data-stu-id="cb4b1-130">301</span></span>     | <span data-ttu-id="cb4b1-131">wartość zerowa (465)</span><span class="sxs-lookup"><span data-stu-id="cb4b1-131">null (465)</span></span> |
| `.AddRedirectToHttps()`          |     <span data-ttu-id="cb4b1-132">302</span><span class="sxs-lookup"><span data-stu-id="cb4b1-132">302</span></span>     | <span data-ttu-id="cb4b1-133">wartość zerowa (465)</span><span class="sxs-lookup"><span data-stu-id="cb4b1-133">null (465)</span></span> |
| `.AddRedirectToHttps(301)`       |     <span data-ttu-id="cb4b1-134">301</span><span class="sxs-lookup"><span data-stu-id="cb4b1-134">301</span></span>     | <span data-ttu-id="cb4b1-135">wartość zerowa (465)</span><span class="sxs-lookup"><span data-stu-id="cb4b1-135">null (465)</span></span> |
| `.AddRedirectToHttps(301, 5001)` |     <span data-ttu-id="cb4b1-136">301</span><span class="sxs-lookup"><span data-stu-id="cb4b1-136">301</span></span>     |    <span data-ttu-id="cb4b1-137">5001</span><span class="sxs-lookup"><span data-stu-id="cb4b1-137">5001</span></span>    |
