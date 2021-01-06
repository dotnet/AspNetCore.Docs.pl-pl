# <a name="aspnet-core-url-rewriting-sample"></a>Przykład ponownego zapisywania adresu URL ASP.NET Core

Ten przykład ilustruje użycie ASP.NET Core ponownego zapisywania oprogramowania pośredniczącego w adresie URL. Aplikacja demonstruje opcje przekierowywania adresów URL i zapisywania adresów URL.

Podczas uruchamiania przykładu odpowiedzi nie związane z plikami zwracają ponownie wpisany lub przekierowany adres URL, gdy jedna z reguł jest stosowana do adresu URL żądania. W przypadku przykładowych plików XML i tekstowych, oprogramowanie pośredniczące plików statycznych zachowuje plik po przepisaniu adresu URL żądania przez oprogramowanie pośredniczące.

## <a name="examples-in-this-sample"></a>Przykłady w tym przykładzie

* `AddRedirect("redirect-rule/(.*)", "redirected/$1")`
  - Kod stanu sukcesu: 302 (znaleziono)
  - Przykład (redirect): **/redirect-rule/{capture_group}** do **/redirected/{capture_group}**
* `AddRewrite(@"^rewrite-rule/(\d+)/(\d+)", "rewritten?var1=$1&var2=$2", skipRemainingRules: true)`
  - Kod stanu sukcesu: 200 (OK)
  - Przykład (Zapisz ponownie): **/rewrite-rule/{capture_group_1}/{capture_group_2}** do **/Rewritten? var1 = {capture_group_1} &var2 = {capture_group_2}**
* `AddApacheModRewrite(env.ContentRootFileProvider, "ApacheModRewrite.txt")`
  - Kod stanu sukcesu: 302 (znaleziono)
  - Przykład (redirect): **/apache-mod-rules-redirect/{capture_group}** do **/redirected? id = {capture_group}**
* `AddIISUrlRewrite(env.ContentRootFileProvider, "IISUrlRewrite.xml")`
  - Kod stanu sukcesu: 200 (OK)
  - Przykład (Zapisz ponownie): **/iis-rules-rewrite/{capture_group}** do **/Rewritten? id = {capture_group}**
* `Add(RedirectXmlFileRequests)`
  - Kod stanu sukcesu: 301 (trwale przeniesiony)
  - Przykład (redirect): **/file.xml** do **/XmlFiles/file.xml**
* `Add(RewriteTextFileRequests)`
  - Kod stanu sukcesu: 200 (OK)
  - Przykład (Zapisz ponownie): **/some_file.txt** do **/file.txt**
* `Add(new RedirectImageRequests(".png", "/png-images")))`<br>`Add(new RedirectImageRequests(".jpg", "/jpg-images")))`
  - Kod stanu sukcesu: 301 (trwale przeniesiony)
  - Przykład (redirect): **/image.png** do **/PNG-images/image.png**
  - Przykład (redirect): **/image.jpg** do **/jpg-images/image.jpg**

## <a name="use-a-physicalfileprovider"></a>Użyj PhysicalFileProvider

Możesz również uzyskać, tworząc element, `IFileProvider` `PhysicalFileProvider` Aby przekazać `AddApacheModRewrite()` `AddIISUrlRewrite()` metody i:

```csharp
using Microsoft.Extensions.FileProviders;
PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
```

## <a name="secure-redirection-extensions"></a>Rozszerzenia bezpiecznego przekierowania

Ten przykład obejmuje `WebHostBuilder` konfigurację aplikacji do korzystania z adresów URL ( `https://localhost:5001` , `https://localhost` ) i certyfikatu testowego (*testCert. pfx*) w celu ułatwienia eksplorowania metod bezpiecznego przekierowania. Jeśli na serwerze jest już przypisany lub używany port 443, to `https://localhost` przykład nie działa &mdash; Usuń `ListenOptions` port dla portu 443 w `CreateWebHostBuilder` metodzie pliku *Program.cs* lub Usuń powiązanie portu 443 na serwerze, aby Kestrel mógł używać portu.

| Metoda                           | Kod stanu |    Port    |
| -------------------------------- | :---------: | :--------: |
| `.AddRedirectToHttpsPermanent()` |     301     | wartość zerowa (465) |
| `.AddRedirectToHttps()`          |     302     | wartość zerowa (465) |
| `.AddRedirectToHttps(301)`       |     301     | wartość zerowa (465) |
| `.AddRedirectToHttps(301, 5001)` |     301     |    5001    |
