---
title: Obsługa błędów w ASP.NET Core
author: rick-anderson
description: Odkryj, jak obsługiwać błędy w aplikacjach ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: fundamentals/error-handling
ms.openlocfilehash: c8174c7e253a596d02dbc6cec183453b3723bc24
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060472"
---
# <a name="handle-errors-in-aspnet-core"></a>Obsługa błędów w ASP.NET Core

::: moniker range=">= aspnetcore-5.0"

Autorzy [Kirka Larkin](https://twitter.com/serpent5), [Tomasz Dykstra](https://github.com/tdykstra/)i [Steve Smith](https://ardalis.com/)

W tym artykule opisano typowe podejścia do obsługi błędów w aplikacjach sieci Web ASP.NET Core. Zobacz, <xref:web-api/handle-errors> Aby poznać interfejsy API sieci Web.

[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples). ([Jak pobrać](xref:index#how-to-download-a-sample)) Karta Sieć w narzędziach deweloperskich w przeglądarce F12 jest przydatna podczas testowania przykładowej aplikacji.

## <a name="developer-exception-page"></a>Strona wyjątków dla deweloperów

Na *stronie wyjątku dla deweloperów* są wyświetlane szczegółowe informacje o wyjątkach żądania. Szablony ASP.NET Core generują następujący kod:

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=3-6)]

Poprzedni wyróżniony kod włącza stronę wyjątków dla deweloperów, gdy aplikacja jest uruchomiona w [środowisku deweloperskim](xref:fundamentals/environments).

Szablony są umieszczane <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> wcześnie w potoku pośredniczącym, dzięki czemu mogą przechwytywać wyjątki zgłoszone w oprogramowaniu pośredniczącym.

Poprzedni kod włącza stronę wyjątków dla deweloperów * **tylko** wtedy, gdy aplikacja działa w środowisku deweloperskim. Szczegółowe informacje o wyjątku nie powinny być wyświetlane publicznie, gdy aplikacja jest uruchamiana w środowisku produkcyjnym. Aby uzyskać więcej informacji na temat konfigurowania środowisk, zobacz <xref:fundamentals/environments> .

Na stronie wyjątku dla deweloperów znajdują się następujące informacje o wyjątku i żądaniu:

_ Ślad stosu
* Parametry ciągu zapytania, jeśli istnieją
* Cookies, jeśli jakieś
* Nagłówki

## <a name="exception-handler-page"></a>Strona obsługi wyjątków

Aby skonfigurować niestandardową stronę obsługi błędów dla [środowiska produkcyjnego](xref:fundamentals/environments), wywołaj polecenie <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> . Ten wyjątek obsługuje oprogramowanie pośredniczące:

* Przechwytuje i rejestruje wyjątki.
* Wykonuje ponowne żądanie w alternatywnym potoku przy użyciu wskazanej ścieżki. Żądanie nie jest wykonywane w przypadku rozpoczęcia odpowiedzi. Kod wygenerowany przez szablon ponownie wykonuje żądanie przy użyciu `/Error` ścieżki.

W poniższym przykładzie <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> dodaje wyjątek obsługujący oprogramowanie pośredniczące w środowiskach innych niż programistyczne:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

RazorSzablon aplikacji strony zawiera stronę błędów ( *. cshtml* ) i <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> klasę ( `ErrorModel` ) w folderze *Pages* . W przypadku aplikacji MVC szablon projektu zawiera `Error` metodę akcji i widok błędów dla kontrolera macierzystego.

Nie zaznaczaj metody akcji procedury obsługi błędów z atrybutami metody HTTP, takimi jak `HttpGet` . Jawne czasowniki uniemożliwiają niektórym żądaniem osiągnięcie metody akcji. Zezwalaj na anonimowy dostęp do metody, jeśli nieuwierzytelnieni użytkownicy powinni zobaczyć widok błędów.

### <a name="access-the-exception"></a>Uzyskaj dostęp do wyjątku

Użyj, <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> Aby uzyskać dostęp do wyjątku i oryginalnej ścieżki żądania w programie obsługi błędów. Poniższy kod dodaje `ExceptionMessage` do domyślnych *stron/Error. cshtml. cs* wygenerowanych przez szablony ASP.NET Core:

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet)]

> [!WARNING]
> **Nie** należy podawać poufnych informacji o błędach do klientów. Obsługa błędów stanowi zagrożenie bezpieczeństwa.

Aby przetestować wyjątek w [aplikacji przykładowej](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):

* Ustaw środowisko na produkcyjne.
* Usuń Komentarze z `webBuilder.UseStartup<Startup>();` programu w *program.cs* .
* Na stronie głównej wybierz opcję **Wyzwól wyjątek** .

## <a name="exception-handler-lambda"></a>Procedura obsługi wyjątków lambda

Alternatywą dla [niestandardowej strony obsługi wyjątków](#exception-handler-page) jest dostarczanie wyrażenia lambda do <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> . Użycie wyrażenia lambda umożliwia dostęp do błędu przed zwróceniem odpowiedzi.

Poniższy kod używa wyrażenia lambda dla obsługi wyjątków:

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupLambda.cs?name=snippet)]

<!-- 
In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message. For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).
-->

> [!WARNING]
> **Nie** należy podawać poufnych informacji o błędach od <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> klientów ani do. Obsługa błędów stanowi zagrożenie bezpieczeństwa.

Aby przetestować obsługę wyjątków lambda w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):

* Ustaw środowisko na produkcyjne.
* Usuń Komentarze z `webBuilder.UseStartup<StartupLambda>();` programu w *program.cs* .
* Na stronie głównej wybierz opcję **Wyzwól wyjątek** .

## <a name="usestatuscodepages"></a>UseStatusCodePages

Domyślnie aplikacja ASP.NET Core nie udostępnia strony kodowej stanu dla kodów stanu błędu HTTP, takich jak *404 — nie znaleziono* . Gdy aplikacja napotka warunek błędu HTTP 400-499, który nie ma treści, zwraca kod stanu i pustą treść odpowiedzi. Aby zapewnić strony kodu stanu, użyj oprogramowania pośredniczącego stron kodowych. Aby włączyć obsługę tylko tekstu dla typowych kodów stanu błędu, wywołaj <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> `Startup.Configure` metodę:

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupUseStatusCodePages.cs?name=snippet&highlight=13)]

<!-- Review: 
When you comment out // UseExceptionHandler("/Error");`
you get a browser dependant error, not the codepage as I expected.
call /index/2 -> return StatusCode(500); -> you get the codepage 
-->

Wywołaj `UseStatusCodePages` przed zażądaniem obsługi oprogramowania pośredniczącego. Na przykład, wywołaj `UseStatusCodePages` przed plikami statycznymi i punktami końcowymi.

Gdy `UseStatusCodePages` nie jest używany, przechodzenie do adresu URL bez punktu końcowego zwraca zależny od przeglądarki komunikat o błędzie informujący o tym, że nie można znaleźć punktu końcowego. Na przykład przechodzenie do `Home/Privacy2` . Gdy `UseStatusCodePages` jest wywoływana, przeglądarka zwraca:

```html
Status Code: 404; Not Found
```

`UseStatusCodePages` zwykle nie jest używana w środowisku produkcyjnym, ponieważ zwraca komunikat, który nie jest przydatny dla użytkowników.

Aby przetestować `UseStatusCodePages` w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):

* Ustaw środowisko na produkcyjne.
* Usuń Komentarze z `webBuilder.UseStartup<StartupUseStatusCodePages>();` programu w *program.cs* .
* Wybierz linki na stronie głównej na stronie głównej.

### <a name="usestatuscodepages-with-format-string"></a>UseStatusCodePages z ciągiem formatu

Aby dostosować typ zawartości odpowiedzi i tekst, Użyj przeciążenia <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> , które pobiera typ zawartości i ciąg formatu:

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupFormat.cs?name=snippet&highlight=13-14)]

W poprzednim kodzie, `{0}` jest symbolem zastępczym kodu błędu.

`UseStatusCodePages` ciąg formatu nie jest zazwyczaj używany w środowisku produkcyjnym, ponieważ zwraca komunikat, który nie jest przydatny dla użytkowników.

Aby przetestować `UseStatusCodePages` w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), Usuń Komentarze z `webBuilder.UseStartup<StartupFormat>();` programu w *program.cs* .

### <a name="usestatuscodepages-with-lambda"></a>UseStatusCodePages z wyrażeniem lambda

Aby określić niestandardową obsługę błędów i kod do pisania w odpowiedzi, należy użyć przeciążenia, <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> które przyjmuje wyrażenie lambda:

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupStatusLambda.cs?name=snippet&highlight=13-20)]

`UseStatusCodePages` wyrażenie lambda nie jest zwykle używane w środowisku produkcyjnym, ponieważ zwraca komunikat, który nie jest przydatny dla użytkowników.

Aby przetestować `UseStatusCodePages` w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), Usuń Komentarze z `webBuilder.UseStartup<StartupStatusLambda>();` programu w *program.cs* .

### <a name="usestatuscodepageswithredirects"></a>UseStatusCodePagesWithRedirects

<xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A>Metoda rozszerzenia:

* Wysyła do klienta kod stanu [znaleziony przez 302](https://developer.mozilla.org/docs/Web/HTTP/Status/302) .
* Przekierowuje klienta do punktu końcowego obsługi błędu podanego w szablonie adresu URL. Punkt końcowy obsługi błędów zazwyczaj wyświetla informacje o błędach i zwraca HTTP 200.

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCredirect.cs?name=snippet&highlight=13)]

Szablon adresu URL może zawierać `{0}` symbol zastępczy kodu stanu, jak pokazano w powyższym kodzie. Jeśli szablon adresu URL zaczyna się od `~` (tylda), `~` zostanie zastąpiony przez aplikację `PathBase` . Podczas określania punktu końcowego w aplikacji Utwórz widok MVC lub Razor stronę dla punktu końcowego. Aby zapoznać się z Razor przykładami stron, zobacz [Pages/StatusCode. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).

Ta metoda jest często używana w przypadku aplikacji:

* Powinien przekierować klienta do innego punktu końcowego, zazwyczaj w przypadkach, gdy inna aplikacja przetwarza błąd. W przypadku usługi Web Apps pasek adresu przeglądarki klienta odzwierciedla przekierowany punkt końcowy.
* Nie należy zachować oryginalnego kodu stanu i zwrócić go do początkowej odpowiedzi przekierowania.

Aby przetestować `UseStatusCodePages` w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), Usuń Komentarze z `webBuilder.UseStartup<StartupSCredirect>();` programu w *program.cs* .

### <a name="usestatuscodepageswithreexecute"></a>UseStatusCodePagesWithReExecute

<xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A>Metoda rozszerzenia:

* Zwraca oryginalny kod stanu dla klienta.
* Generuje treść odpowiedzi przez ponowne wykonanie potoku żądania przy użyciu ścieżki alternatywnej.

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCreX.cs?name=snippet&highlight=13)]

Jeśli określono punkt końcowy w aplikacji, należy utworzyć widok MVC lub Razor stronę dla punktu końcowego. Upewnij `UseStatusCodePagesWithReExecute` się, że został umieszczony przed `UseRouting` , aby żądanie można było przekierować do strony stanu. Aby zapoznać się z Razor przykładami stron, zobacz [Pages/MyStatusCode2. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).

Ta metoda jest często używana, gdy aplikacja powinna:

* Przetwórz żądanie bez przekierowania do innego punktu końcowego. W przypadku usługi Web Apps pasek adresu przeglądarki klienta odzwierciedla pierwotnie żądany punkt końcowy.
* Zachowywanie i zwracanie oryginalnego kodu stanu z odpowiedzią.

Szablony adresu URL i ciągu zapytania mogą zawierać symbol zastępczy `{0}` kodu stanu. Szablon adresu URL musi zaczynać się od `/` .

<!-- Review: removing this. The sample code doesn't use @page "{code?}"
If you want that, it should be @page "{code:int?}"
but that's not required. Original text follows:

When using a placeholder in the path, confirm that the endpoint can process the path segment. For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:

```cshtml
@page "{code?}"
```
-->

Punkt końcowy, który przetwarza błąd, może uzyskać oryginalny adres URL, który wygenerował błąd, jak pokazano w następującym przykładzie:

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/MyStatusCode2.cshtml.cs?name=snippet)]

Aby zapoznać się z Razor przykładami stron, zobacz [Pages/MyStatusCode2. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).

Aby przetestować `UseStatusCodePages` w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), Usuń Komentarze z `webBuilder.UseStartup<StartupSCreX>();` programu w *program.cs* .

## <a name="disable-status-code-pages"></a>Wyłącz strony kodu stanu

Aby wyłączyć strony kodowe stanu dla kontrolera MVC lub metody akcji, Użyj atrybutu [[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) .

Aby wyłączyć strony kodów stanu dla określonych żądań w Razor metodzie obsługi stron lub w KONTROLERZE MVC, użyj <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> :

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Privacy.cshtml.cs?name=snippet)]

## <a name="exception-handling-code"></a>Kod obsługi wyjątków

Kod na stronach obsługi wyjątków może również generować wyjątki. Strony błędów produkcyjnych należy dokładnie przetestować i zachować dodatkową ostrożność, aby uniknąć zgłaszania wyjątków.
<!-- Review: original, which is not realistic 
 > It's often a good idea for production error pages to consist of purely static content.

 comments: - after you catch the exception, you need code to log the details and perhaps dynamically create a string with an error message. 
-->

### <a name="response-headers"></a>Nagłówki odpowiedzi

Po wysłaniu nagłówków odpowiedzi:

* Aplikacja nie może zmienić kodu stanu odpowiedzi.
* Nie można uruchomić żadnych stron ani programów wyjątków. Odpowiedź musi zostać zakończona lub połączenie zostało przerwane.

## <a name="server-exception-handling"></a>Obsługa wyjątków serwera

Oprócz logiki obsługi wyjątków w aplikacji, [Implementacja serwera http](xref:fundamentals/servers/index) może obsłużyć wyjątki. Jeśli serwer przechwytuje wyjątek przed wysłaniem nagłówków odpowiedzi, serwer wysyła `500 - Internal Server Error` odpowiedź bez treści odpowiedzi. Jeśli serwer przechwytuje wyjątek po wysłaniu nagłówków odpowiedzi, serwer zamknie połączenie. Żądania, które nie są obsługiwane przez aplikację, są obsługiwane przez serwer. Każdy wyjątek, który występuje, gdy serwer obsługuje żądanie jest obsługiwany przez obsługę wyjątków serwera. Niestandardowe strony błędów aplikacji, obsługa wyjątków i filtry nie wpływają na to zachowanie.

## <a name="startup-exception-handling"></a>Obsługa wyjątków uruchamiania

Tylko warstwa hostingu może obsługiwać wyjątki, które odbywają się podczas uruchamiania aplikacji. Host można skonfigurować do [przechwytywania błędów uruchamiania](xref:fundamentals/host/web-host#capture-startup-errors) i [przechwytywania szczegółowych błędów](xref:fundamentals/host/web-host#detailed-errors).

W warstwie hostingu może zostać wyświetlona strona błędu przechwyconego błędu uruchomienia tylko wtedy, gdy błąd występuje po powiązaniu adresu hosta/portu. Jeśli powiązanie nie powiedzie się:

* Warstwa hostingu rejestruje wyjątek krytyczny.
* Proces dotnet ulega awarii.
* Nie zostanie wyświetlona strona błędu, gdy serwer HTTP jest [Kestrel](xref:fundamentals/servers/kestrel).

W przypadku uruchamiania [programu w usługach IIS](/iis) (lub Azure App Service) lub [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), [moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module) zwrócił *błąd 502,5-proces* , jeśli nie można uruchomić procesu. Aby uzyskać więcej informacji, zobacz <xref:test/troubleshoot-azure-iis>.

## <a name="database-error-page"></a>Strona błędu bazy danych

Filtr wyjątku strony dewelopera bazy danych `AddDatabaseDeveloperPageExceptionFilter` przechwytuje wyjątki związane z bazą danych, które można rozwiązać za pomocą migracji Entity Framework Core. Kiedy te wyjątki wystąpią, odpowiedź HTML jest generowana z szczegółowymi informacjami o możliwych akcjach w celu rozwiązania problemu. Ta strona jest włączona tylko w środowisku deweloperskim. Następujący kod został wygenerowany przez Razor Szablony stron ASP.NET Core po określeniu poszczególnych kont użytkowników:

[!code-csharp[](error-handling/samples/5.x/StartupDBexFilter.cs?name=snippet&highlight=6)]

## <a name="exception-filters"></a>Filtry wyjątków

W aplikacjach MVC filtry wyjątków można konfigurować globalnie lub na podstawie poszczególnych kontrolerów lub akcji. Na Razor stronach aplikacje można konfigurować je globalnie lub na modelu strony. Te filtry obsługują wszystkie Nieobsłużone wyjątki, które występują podczas wykonywania akcji kontrolera lub innego filtru. Aby uzyskać więcej informacji, zobacz <xref:mvc/controllers/filters#exception-filters>.

Filtry wyjątków są przydatne w przypadku zalewek wyjątków, które występują w akcjach MVC, ale nie są tak elastyczne jak wbudowany [wyjątek obsługujący oprogramowanie pośredniczące](https://github.com/dotnet/aspnetcore/blob/master/src/Middleware/Diagnostics/src/ExceptionHandler/ExceptionHandlerMiddleware.cs) `UseExceptionHandler` . Zalecamy używanie `UseExceptionHandler` , chyba że konieczna jest obsługa błędów w różny sposób, w zależności od wybranej akcji MVC.

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=9)]

## <a name="model-state-errors"></a>Błędy stanu modelu

Aby uzyskać informacje o sposobie obsługi błędów stanu modelu, zobacz [powiązanie modelu](xref:mvc/models/model-binding) i [Walidacja modelu](xref:mvc/models/validation).

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Autorzy  [Dykstra](https://github.com/tdykstra/)i [Steve Smith](https://ardalis.com/)

W tym artykule opisano typowe podejścia do obsługi błędów w aplikacjach sieci Web ASP.NET Core. Zobacz, <xref:web-api/handle-errors> Aby poznać interfejsy API sieci Web.

[Wyświetlanie lub Pobieranie przykładowego kodu](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples). ([Jak pobrać](xref:index#how-to-download-a-sample))

## <a name="developer-exception-page"></a>Strona wyjątków dla deweloperów

Na *stronie wyjątku dla deweloperów* są wyświetlane szczegółowe informacje o wyjątkach żądania. Szablony ASP.NET Core generują następujący kod:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

Poprzedni kod włącza stronę wyjątków dla deweloperów, gdy aplikacja jest uruchomiona w [środowisku deweloperskim](xref:fundamentals/environments).

Szablony należy umieścić <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> przed jakimkolwiek oprogramowanie pośredniczące, dlatego wyjątki są przechwytywane w oprogramowaniu pośredniczącym poniżej.

Poprzedni kod włącza stronę wyjątku dewelopera tylko wtedy, **gdy aplikacja jest uruchomiona w środowisku deweloperskim** . Szczegółowe informacje o wyjątku nie powinny być wyświetlane publicznie, gdy aplikacja jest uruchamiana w środowisku produkcyjnym. Aby uzyskać więcej informacji na temat konfigurowania środowisk, zobacz <xref:fundamentals/environments> .

Na stronie wyjątku dla deweloperów znajdują się następujące informacje o wyjątku i żądaniu:

* Ślad stosu
* Parametry ciągu zapytania, jeśli istnieją
* Cookies, jeśli jakieś
* Nagłówki

## <a name="exception-handler-page"></a>Strona obsługi wyjątków

Aby skonfigurować niestandardową stronę obsługi błędów dla środowiska produkcyjnego, należy użyć wyjątku obsługi oprogramowania pośredniczącego. Oprogramowanie pośredniczące:

* Przechwytuje i rejestruje wyjątki.
* Wykonuje ponowne żądanie w alternatywnym potoku dla wskazanej strony lub kontrolera. Żądanie nie jest wykonywane w przypadku rozpoczęcia odpowiedzi. Kod wygenerowany przez szablon ponownie wykonuje żądanie do `/Error` .

W poniższym przykładzie <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> dodaje wyjątek obsługujący oprogramowanie pośredniczące w środowiskach innych niż programistyczne:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

RazorSzablon aplikacji strony zawiera stronę błędów ( *. cshtml* ) i <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> klasę ( `ErrorModel` ) w folderze *Pages* . W przypadku aplikacji MVC szablon projektu zawiera metodę akcji błędu i widok błędów w kontrolerze głównym.

Nie zaznaczaj metody akcji procedury obsługi błędów z atrybutami metody HTTP, takimi jak `HttpGet` . Jawne czasowniki uniemożliwiają niektórym żądaniem osiągnięcie metody. Zezwalaj na anonimowy dostęp do metody, jeśli nieuwierzytelnieni użytkownicy powinni zobaczyć widok błędów.

### <a name="access-the-exception"></a>Uzyskaj dostęp do wyjątku

Użyj, <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> Aby uzyskać dostęp do wyjątku i oryginalnej ścieżki żądania w kontrolerze lub stronie procedury obsługi błędów:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/MyFolder/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> **Nie** należy podawać poufnych informacji o błędach do klientów. Obsługa błędów stanowi zagrożenie bezpieczeństwa.

Aby wyzwolić stronę obsługi przed wyjątkami, należy ustawić środowisko na produkcje i wymusić wyjątek.

## <a name="exception-handler-lambda"></a>Procedura obsługi wyjątków lambda

Alternatywą dla [niestandardowej strony obsługi wyjątków](#exception-handler-page) jest dostarczanie wyrażenia lambda do <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> . Użycie wyrażenia lambda umożliwia dostęp do błędu przed zwróceniem odpowiedzi.

Oto przykład użycia wyrażenia lambda dla obsługi wyjątków:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

W powyższym kodzie `await context.Response.WriteAsync(new string(' ', 512));` zostanie dodany, aby przeglądarka Internet Explorer wyświetlała komunikat o błędzie zamiast komunikatu o błędzie programu IE. Aby uzyskać więcej informacji, zobacz [ten problem](https://github.com/dotnet/AspNetCore.Docs/issues/16144)w serwisie GitHub.

> [!WARNING]
> **Nie** należy podawać poufnych informacji o błędach od <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> klientów ani do. Obsługa błędów stanowi zagrożenie bezpieczeństwa.

Aby zobaczyć wynik obsługi wyjątku lambda w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), użyj `ProdEnvironment` `ErrorHandlerLambda` dyrektyw i preprocesora, a następnie wybierz **Wyzwól wyjątek** na stronie głównej.

## <a name="usestatuscodepages"></a>UseStatusCodePages

Domyślnie aplikacja ASP.NET Core nie udostępnia strony kodowej stanu dla kodów stanu HTTP, takich jak *404 — nie znaleziono* . Aplikacja zwraca kod stanu i pustą treść odpowiedzi. Aby udostępnić strony kodu stanu, użyj oprogramowania pośredniczącego stron kodowych.

Oprogramowanie pośredniczące jest udostępniane przez pakiet [Microsoft. AspNetCore. Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) .

Aby włączyć obsługę tylko tekstu dla typowych kodów stanu błędu, wywołaj <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> `Startup.Configure` metodę:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

Wywołaj `UseStatusCodePages` przed zażądaniem obsługi oprogramowania pośredniczącego (na przykład statycznych plików oprogramowania pośredniczącego i oprogramowania MVC).

Gdy `UseStatusCodePages` nie jest używany, przechodzenie do adresu URL bez punktu końcowego zwraca zależny od przeglądarki komunikat o błędzie informujący o tym, że nie można znaleźć punktu końcowego. Na przykład przechodzenie do `Home/Privacy2` . Gdy `UseStatusCodePages` jest wywoływana, przeglądarka zwraca:

```
Status Code: 404; Not Found
```

## <a name="usestatuscodepages-with-format-string"></a>UseStatusCodePages z ciągiem formatu

Aby dostosować typ zawartości odpowiedzi i tekst, Użyj przeciążenia <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> , które pobiera typ zawartości i ciąg formatu:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a>UseStatusCodePages z wyrażeniem lambda

Aby określić niestandardową obsługę błędów i kod do pisania w odpowiedzi, należy użyć przeciążenia, <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> które przyjmuje wyrażenie lambda:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a>UseStatusCodePagesWithRedirects

<xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A>Metoda rozszerzenia:

* Wysyła do klienta kod stanu *znaleziony przez 302* .
* Przekierowuje klienta do lokalizacji podanej w szablonie adresu URL.

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

Szablon adresu URL może zawierać `{0}` symbol zastępczy kodu stanu, jak pokazano w przykładzie. Jeśli szablon adresu URL zaczyna się od `~` (tylda), `~` zostanie zastąpiony przez aplikację `PathBase` . Jeśli wskażesz punkt końcowy w aplikacji, Utwórz widok MVC lub Razor stronę dla punktu końcowego. Aby zapoznać się z Razor przykładami stron, zobacz *Pages/StatusCode. cshtml* w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).

Ta metoda jest często używana w przypadku aplikacji:

* Powinien przekierować klienta do innego punktu końcowego, zazwyczaj w przypadkach, gdy inna aplikacja przetwarza błąd. W przypadku usługi Web Apps pasek adresu przeglądarki klienta odzwierciedla przekierowany punkt końcowy.
* Nie należy zachować oryginalnego kodu stanu i zwrócić go do początkowej odpowiedzi przekierowania.

## <a name="usestatuscodepageswithreexecute"></a>UseStatusCodePagesWithReExecute

<xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A>Metoda rozszerzenia:

* Zwraca oryginalny kod stanu dla klienta.
* Generuje treść odpowiedzi przez ponowne wykonanie potoku żądania przy użyciu ścieżki alternatywnej.

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

Jeśli wskażesz punkt końcowy w aplikacji, Utwórz widok MVC lub Razor stronę dla punktu końcowego. Upewnij `UseStatusCodePagesWithReExecute` się, że został umieszczony przed `UseRouting` , aby żądanie można było przekierować do strony stanu. Aby zapoznać się z Razor przykładami stron, zobacz *Pages/StatusCode. cshtml* w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).

Ta metoda jest często używana, gdy aplikacja powinna:

* Przetwórz żądanie bez przekierowania do innego punktu końcowego. W przypadku usługi Web Apps pasek adresu przeglądarki klienta odzwierciedla pierwotnie żądany punkt końcowy.
* Zachowywanie i zwracanie oryginalnego kodu stanu z odpowiedzią.

Szablony adresu URL i ciągu zapytania mogą zawierać symbol zastępczy ( `{0}` ) dla kodu stanu. Szablon adresu URL musi rozpoczynać się od ukośnika ( `/` ). W przypadku używania symbolu zastępczego w ścieżce upewnij się, że punkt końcowy (strona lub kontroler) może przetworzyć segment ścieżki. Na przykład Razor Strona dla błędów powinna akceptować opcjonalną wartość segmentu ścieżki przy użyciu `@page` dyrektywy:

```cshtml
@page "{code?}"
```

Punkt końcowy, który przetwarza błąd, może uzyskać oryginalny adres URL, który wygenerował błąd, jak pokazano w następującym przykładzie:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a>Wyłącz strony kodu stanu

Aby wyłączyć strony kodowe stanu dla kontrolera MVC lub metody akcji, użyj [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) atrybutu.

Aby wyłączyć strony kodów stanu dla określonych żądań w Razor metodzie obsługi stron lub w KONTROLERZE MVC, użyj <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> :

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a>Kod obsługi wyjątków

Kod na stronach obsługi wyjątków może generować wyjątki. Często dobrym pomysłem jest, aby strony błędów produkcyjnych obejmowały czysto statyczną zawartość.

### <a name="response-headers"></a>Nagłówki odpowiedzi

Po wysłaniu nagłówków odpowiedzi:

* Aplikacja nie może zmienić kodu stanu odpowiedzi.
* Nie można uruchomić żadnych stron ani programów wyjątków. Odpowiedź musi zostać zakończona lub połączenie zostało przerwane.

## <a name="server-exception-handling"></a>Obsługa wyjątków serwera

Oprócz logiki obsługi wyjątków w aplikacji, [Implementacja serwera http](xref:fundamentals/servers/index) może obsłużyć wyjątki. Jeśli serwer przechwytuje wyjątek przed wysłaniem nagłówków odpowiedzi, serwer wysyła do *500 — wewnętrzny błąd serwera* bez treści odpowiedzi. Jeśli serwer przechwytuje wyjątek po wysłaniu nagłówków odpowiedzi, serwer zamknie połączenie. Żądania, które nie są obsługiwane przez aplikację, są obsługiwane przez serwer. Każdy wyjątek, który występuje, gdy serwer obsługuje żądanie jest obsługiwany przez obsługę wyjątków serwera. Niestandardowe strony błędów aplikacji, obsługa wyjątków i filtry nie wpływają na to zachowanie.

## <a name="startup-exception-handling"></a>Obsługa wyjątków uruchamiania

Tylko warstwa hostingu może obsługiwać wyjątki, które odbywają się podczas uruchamiania aplikacji. Host można skonfigurować do [przechwytywania błędów uruchamiania](xref:fundamentals/host/web-host#capture-startup-errors) i [przechwytywania szczegółowych błędów](xref:fundamentals/host/web-host#detailed-errors).

W warstwie hostingu może zostać wyświetlona strona błędu przechwyconego błędu uruchomienia tylko wtedy, gdy błąd występuje po powiązaniu adresu hosta/portu. Jeśli powiązanie nie powiedzie się:

* Warstwa hostingu rejestruje wyjątek krytyczny.
* Proces dotnet ulega awarii.
* Nie zostanie wyświetlona strona błędu, gdy serwer HTTP jest [Kestrel](xref:fundamentals/servers/kestrel).

W przypadku uruchamiania [programu w usługach IIS](/iis) (lub Azure App Service) lub [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), [moduł ASP.NET Core](xref:host-and-deploy/aspnet-core-module) zwrócił *błąd 502,5-proces* , jeśli nie można uruchomić procesu. Aby uzyskać więcej informacji, zobacz <xref:test/troubleshoot-azure-iis>.

## <a name="database-error-page"></a>Strona błędu bazy danych

Strona błędu bazy danych oprogramowanie pośredniczące przechwytuje wyjątki związane z bazą danych, które można rozwiązać za pomocą migracji Entity Framework. Kiedy te wyjątki wystąpią, zostanie wygenerowana odpowiedź HTML z szczegółowymi działaniami umożliwiającymi rozwiązanie problemu. Ta strona powinna być włączona tylko w środowisku deweloperskim. Włącz stronę, dodając kod do `Startup.Configure` :

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> wymaga pakietu NuGet [Microsoft. AspNetCore. Diagnostics. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) .

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a>Filtry wyjątków

W aplikacjach MVC filtry wyjątków można konfigurować globalnie lub na podstawie poszczególnych kontrolerów lub akcji. Na Razor stronach aplikacje można konfigurować je globalnie lub na modelu strony. Te filtry obsługują wszystkie Nieobsłużone wyjątki, które występują podczas wykonywania akcji kontrolera lub innego filtru. Aby uzyskać więcej informacji, zobacz <xref:mvc/controllers/filters#exception-filters>.

> [!TIP]
> Filtry wyjątków są przydatne w przypadku zalewek wyjątków, które występują w akcjach MVC, ale nie są tak elastyczne, jak wyjątek obsługujący oprogramowanie pośredniczące. Zalecamy używanie oprogramowania pośredniczącego. Używaj filtrów tylko wtedy, gdy musisz wykonać obsługę błędów w różny sposób, w zależności od wybranej akcji MVC.

## <a name="model-state-errors"></a>Błędy stanu modelu

Aby uzyskać informacje o sposobie obsługi błędów stanu modelu, zobacz [powiązanie modelu](xref:mvc/models/model-binding) i [Walidacja modelu](xref:mvc/models/validation).

## <a name="additional-resources"></a>Dodatkowe zasoby

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end
