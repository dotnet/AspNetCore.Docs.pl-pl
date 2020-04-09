# <a name="aspnet-core-url-rewriting-sample"></a>Przykładowy przepisanie adresów URL ASP.NET

W tym przykładzie przedstawiono użycie oprogramowania pośredniczącego ASP.NET Core URL Rewriting. Aplikacja pokazuje opcje przekierowania adresów URL i przepisywania adresów URL.

Podczas uruchamiania przykładu odpowiedzi bez pliku zwracają przepisany lub przekierowany adres URL, gdy jedna z reguł jest stosowana do adresu URL żądania. W przykładach pliku XML i pliku tekstowego oprogramowanie pośredniczące plików statycznych obsługuje plik po przepisaniu adresu URL żądania przez oprogramowanie pośredniczące.

## <a name="examples-in-this-sample"></a>Przykłady w tym przykładzie

* `AddRedirect("redirect-rule/(.*)", "redirected/$1")`
  - Kod stanu sukcesu: 302 (Znaleziono)
  - Przykład (przekierowanie): **/redirect-rule/{capture_group}** do **/redirected/{capture_group}**
* `AddRewrite(@"^rewrite-rule/(\d+)/(\d+)", "rewritten?var1=$1&var2=$2", skipRemainingRules: true)`
  - Kod stanu sukcesu: 200 (OK)
  - Przykład (przepisanie): **/rewrite-rule/{capture_group_1}/{capture_group_2}** do **/rewritten?var1={capture_group_1}&var2={capture_group_2}**
* `AddApacheModRewrite(env.ContentRootFileProvider, "ApacheModRewrite.txt")`
  - Kod stanu sukcesu: 302 (Znaleziono)
  - Przykład (przekierowanie): **/apache-mod-rules-redirect/{capture_group}** do **/redirected?id={capture_group}**
* `AddIISUrlRewrite(env.ContentRootFileProvider, "IISUrlRewrite.xml")`
  - Kod stanu sukcesu: 200 (OK)
  - Przykład (przepisanie): **/iis-rules-rewrite/{capture_group}** na **/rewritten?id={capture_group}**
* `Add(RedirectXmlFileRequests)`
  - Kod statusu sukcesu: 301 (przeniesiony na stałe)
  - Przykład (przekierowanie): **/file.xml** do **/xmlfiles/file.xml**
* `Add(RewriteTextFileRequests)`
  - Kod stanu sukcesu: 200 (OK)
  - Przykład (przepisanie): **/some_file.txt** do **/file.txt**
* `Add(new RedirectImageRequests(".png", "/png-images")))`<br>`Add(new RedirectImageRequests(".jpg", "/jpg-images")))`
  - Kod statusu sukcesu: 301 (przeniesiony na stałe)
  - Przykład (przekierowanie): **/image.png** do **/png-images/image.png**
  - Przykład (przekierowanie): **/image.jpg** do **/jpg-images/image.jpg**

## <a name="use-a-physicalfileprovider"></a>Używanie pliku fizycznegoProvider

`IFileProvider` Można również uzyskać, tworząc `PhysicalFileProvider` a przekazać `AddApacheModRewrite()` do `AddIISUrlRewrite()` i metody:

```csharp
using Microsoft.Extensions.FileProviders;
PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
```

## <a name="secure-redirection-extensions"></a>Bezpieczne rozszerzenia przekierowania

Ten przykład `WebHostBuilder` zawiera konfigurację aplikacji do`https://localhost:5001` `https://localhost`używania adresów URL ( , ) i certyfikat testowy *(testCert.pfx*), aby pomóc w eksplorowaniu metod bezpiecznego przekierowania. Jeśli serwer ma już przypisany port 443 `https://localhost` lub używany,&mdash;przykład `ListenOptions` nie działa usunąć dla `CreateWebHostBuilder` portu 443 w metodzie pliku *Program.cs* lub unbind portu 443 na serwerze, tak aby Kestrel mógł korzystać z portu.

| Metoda                           | Kod stanu |    Port    |
| -------------------------------- | :---------: | :--------: |
| `.AddRedirectToHttpsPermanent()` |     301     | null (465) |
| `.AddRedirectToHttps()`          |     302     | null (465) |
| `.AddRedirectToHttps(301)`       |     301     | null (465) |
| `.AddRedirectToHttps(301, 5001)` |     301     |    5001    |
