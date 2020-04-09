---
title: Obsługa błędów w ASP.NET Core
author: rick-anderson
description: Dowiedz się, jak radzić sobie z błędami w aplikacjach ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: fundamentals/error-handling
ms.openlocfilehash: 28b463bccfb8aff4d10b95aa9a984455b4f4b976
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658817"
---
# <a name="handle-errors-in-aspnet-core"></a>Obsługa błędów w ASP.NET Core

Przez [Tom Dykstra](https://github.com/tdykstra/) i [Steve Smith](https://ardalis.com/)

W tym artykule omówiono typowe podejścia do obsługi błędów w ASP.NET podstawowych aplikacji sieci web. Zobacz <xref:web-api/handle-errors> interfejsy API sieci Web.

[Wyświetl lub pobierz przykładowy kod](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples). (Jak[pobrać.)](xref:index#how-to-download-a-sample) Artykuł zawiera instrukcje dotyczące ustawiania dyrektyw preprocesora `#endif` `#define`(`#if`, , ) w przykładowej aplikacji, aby włączyć różne scenariusze.

## <a name="developer-exception-page"></a>Strona wyjątku dla deweloperów

Strona *wyjątków dewelopera* wyświetla szczegółowe informacje o wyjątkach żądań. Strona jest udostępniana przez pakiet [Microsoft.AspNetCore.Diagnostics,](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) który znajduje się w [metapakiecie Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app). Dodaj kod `Startup.Configure` do metody, aby włączyć stronę, gdy aplikacja jest uruchomiona w [środowisku programistycznym:](xref:fundamentals/environments)

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

Umieść wywołanie <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> przed każdym oprogramowaniem pośredniczącym, które chcesz złapać wyjątki.

> [!WARNING]
> Włącz stronę wyjątku dewelopera **tylko wtedy, gdy aplikacja jest uruchomiona w środowisku deweloperskim**. Nie chcesz udostępniać szczegółowych informacji o wyjątku publicznie, gdy aplikacja działa w produkcji. Aby uzyskać więcej informacji na <xref:fundamentals/environments>temat konfigurowania środowisk, zobacz .

Strona zawiera następujące informacje o wyjątku i żądaniu:

* Śledzenie stosu
* Parametry ciągu kwerendy (jeśli istnieją)
* Pliki cookie (jeśli istnieją)
* Nagłówki

Aby wyświetlić stronę wyjątków dla deweloperów w [przykładowej aplikacji,](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)użyj dyrektywy `DevEnvironment` preprocesora i wybierz pozycję **Wyzwalaj wyjątek** na stronie głównej.

## <a name="exception-handler-page"></a>Strona obsługi wyjątków

Aby skonfigurować stronę niestandardowej obsługi błędów dla środowiska produkcyjnego, należy użyć oprogramowania pośredniczącego obsługi wyjątków. Oprogramowanie pośredniczące:

* Połowy i dzienniki wyjątków.
* Ponownie wykonuje żądanie w alternatywnym potoku dla strony lub kontrolera wskazanego. Żądanie nie jest ponownie wykonywane, jeśli odpowiedź została uruchomiona.

W poniższym <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> przykładzie dodaje oprogramowanie pośredniczące obsługi wyjątków w środowiskach innych niż deweloperów:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

Szablon aplikacji Razor Pages zawiera stronę Błąd *(cshtml)* i <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> klasę (`ErrorModel`) w folderze *Strony.* W przypadku aplikacji MVC szablon projektu zawiera metodę akcji Błąd i widok błędu. Oto metoda działania:

```csharp
[AllowAnonymous]
public IActionResult Error()
{
    return View(new ErrorViewModel 
        { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
}
```

Nie oznaczaj metody akcji obsługi błędów atrybutami `HttpGet`metody HTTP, takimi jak . Jawne zlecenia uniemożliwiają niektóre żądania dotarcia do metody. Zezwalaj na anonimowy dostęp do metody, aby nieuwierzylicy użytkownicy mogli odbierać widok błędu.

### <a name="access-the-exception"></a>Dostęp do wyjątku

Służy <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> do uzyskiwania dostępu do wyjątku i oryginalnej ścieżki żądania w kontrolerze lub stronie obsługi błędów:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> **Nie należy** podawać poufnych informacji o błędach dla klientów. Błędy obsługi jest zagrożeniem bezpieczeństwa.

Aby wyświetlić stronę obsługi wyjątków w `ProdEnvironment` `ErrorHandlerPage` [przykładowej aplikacji,](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)użyj dyrektyw i preprocesora i wybierz **wyzwalanie wyjątku** na stronie głównej.

## <a name="exception-handler-lambda"></a>Obsługa wyjątków lambda

Alternatywą dla [niestandardowej strony obsługi wyjątków](#exception-handler-page) jest <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>zapewnienie lambda do . Za pomocą lambda umożliwia dostęp do błędu przed zwróceniem odpowiedzi.

Oto przykład użycia lambda do obsługi wyjątków:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

W poprzednim kodzie `await context.Response.WriteAsync(new string(' ', 512));` jest dodawany, więc przeglądarka Internet Explorer wyświetla komunikat o błędzie, a nie komunikat o błędzie IE. Aby uzyskać więcej informacji, zobacz [ten problem z githubem](https://github.com/dotnet/AspNetCore.Docs/issues/16144).

> [!WARNING]
> **Nie należy** podawać poufnych informacji o błędach od <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> klientów ani <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> do klientów. Błędy obsługi jest zagrożeniem bezpieczeństwa.

Aby wyświetlić wynik obsługi wyjątków lambda w `ProdEnvironment` [przykładowej aplikacji,](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)użyj dyrektyw i `ErrorHandlerLambda` preprocesora i wybierz **wyzwalacz wyjątek** na stronie głównej.

## <a name="usestatuscodepages"></a>Strony UseStatusCodePages

Domyślnie aplikacja ASP.NET Core nie udostępnia strony kodu stanu dla kodów stanu HTTP, takich jak *404 - Nie znaleziono*. Aplikacja zwraca kod stanu i pustą treść odpowiedzi. Aby udostępnić strony kodów stanu, użyj oprogramowania pośredniczącego Strony kodów stanu.

Oprogramowanie pośredniczące jest udostępniane przez pakiet [Microsoft.AspNetCore.Diagnostics,](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) który znajduje się w [metapakiecie Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).

Aby włączyć domyślne programy obsługi tekstu dla <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> typowych `Startup.Configure` kodów stanu błędów, wywołaj metodę:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

Wywołanie `UseStatusCodePages` przed żądaniem obsługi oprogramowania pośredniczącego (na przykład static file middleware i MVC Middleware).

Oto przykład tekstu wyświetlanego przez domyślne programy obsługi:

```
Status Code: 404; Not Found
```

Aby wyświetlić jeden z różnych formatów stron kodu stanu w [przykładowej aplikacji,](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)użyj `StatusCodePages`jednej z dyrektyw preprocesora, które zaczynają się od programu , i wybierz **pozycję Wyzwalaj 404** na stronie głównej.

## <a name="usestatuscodepages-with-format-string"></a>UseStatusCodePages z ciągiem formatu

Aby dostosować typ zawartości odpowiedzi i tekst, <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> użyj przeciążenia, które przyjmuje typ zawartości i ciąg formatu:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a>UseStatusCodePages z lambda

Aby określić niestandardowy kod obsługi błędów i <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> pisania odpowiedzi, należy użyć przeciążenia, które przyjmuje wyrażenie lambda:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a>UseStatusCodePagesWithRedirects

Metoda <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*> rozszerzenia:

* Wysyła *302 — znaleziony* kod stanu do klienta.
* Przekierowuje klienta do lokalizacji podanej w szablonie adresu URL.

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

Szablon adresu URL `{0}` może zawierać symbol zastępczy kodu stanu, jak pokazano w przykładzie. Jeśli szablon adresu URL zaczyna się od tyldy (~), tylda `PathBase`zostanie zastąpiona przez aplikację . Jeśli wskażesz punkt końcowy w aplikacji, utwórz widok MVC lub stronę Razor dla punktu końcowego. Przykład strony razor można znaleźć na *stronach/statuscode.cshtml* w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).

Ta metoda jest często używana, gdy aplikacja:

* Należy przekierować klienta do innego punktu końcowego, zwykle w przypadkach, gdy inna aplikacja przetwarza błąd. W przypadku aplikacji sieci web pasek adresu przeglądarki klienta odzwierciedla przekierowany punkt końcowy.
* Nie należy zachowywać i zwracać oryginalnego kodu stanu z początkową odpowiedzią przekierowania.

## <a name="usestatuscodepageswithreexecute"></a>UseStatusCodePagesWithReExecute

Metoda <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*> rozszerzenia:

* Zwraca oryginalny kod stanu do klienta.
* Generuje treść odpowiedzi, wykonując ponownie potok żądania przy użyciu ścieżki alternatywnej.

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

Jeśli wskażesz punkt końcowy w aplikacji, utwórz widok MVC lub stronę Razor dla punktu końcowego. Przykład strony razor można znaleźć na *stronach/statuscode.cshtml* w [przykładowej aplikacji](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).

Ta metoda jest często używana, gdy aplikacja powinna:

* Przetwórz żądanie bez przekierowywania do innego punktu końcowego. W przypadku aplikacji sieci web pasek adresu przeglądarki klienta odzwierciedla pierwotnie żądany punkt końcowy.
* Zachowaj i zwróć oryginalny kod stanu z odpowiedzią.

Szablony adresów URL i ciągów`{0}`zapytań mogą zawierać symbol zastępczy ( ) dla kodu stanu. Szablon adresu URL musi zaczynać się od ukośnika (`/`). Korzystając z symbolu zastępczego w ścieżce, upewnij się, że punkt końcowy (strona lub kontroler) może przetwarzać segment ścieżki. Na przykład razor page dla błędów należy zaakceptować `@page` wartość segmentu ścieżki opcjonalne z dyrektywy:

```cshtml
@page "{code?}"
```

Punkt końcowy, który przetwarza błąd, może uzyskać oryginalny adres URL, który wygenerował błąd, jak pokazano w poniższym przykładzie:

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a>Wyłączanie stron kodów stanu

Aby wyłączyć strony kodu stanu dla kontrolera MVC lub metody akcji, użyj atrybutu. [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute)

Aby wyłączyć strony kodu stanu dla określonych żądań w metodzie obsługi <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>stron Razor lub w kontrolerze MVC, należy użyć:

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a>Kod obsługi wyjątków

Kod w strony obsługi wyjątków można zgłaszać wyjątki. Często dobrym pomysłem jest, aby strony błędów produkcyjnych składały się z zawartości czysto statycznej.

### <a name="response-headers"></a>Nagłówki odpowiedzi

Po wysłaniu nagłówków odpowiedzi:

* Aplikacja nie może zmienić kodu stanu odpowiedzi.
* Nie można uruchomić żadnych stron wyjątków ani programów obsługi. Odpowiedź musi zostać zakończona lub połączenie zostało przerwane.

## <a name="server-exception-handling"></a>Obsługa wyjątków serwera

Oprócz logiki obsługi wyjątków w aplikacji [implementacja serwera HTTP](xref:fundamentals/servers/index) może obsługiwać niektóre wyjątki. Jeśli serwer złapie wyjątek przed wysłaniem nagłówków odpowiedzi, serwer wysyła *500 — Odpowiedź na wewnętrzny błąd serwera* bez treści odpowiedzi. Jeśli serwer złapie wyjątek po wysłaniu nagłówków odpowiedzi, serwer zamyka połączenie. Żądania, które nie są obsługiwane przez aplikację są obsługiwane przez serwer. Każdy wyjątek, który występuje, gdy serwer obsługuje żądanie jest obsługiwany przez obsługę wyjątków serwera. Niestandardowe strony błędów aplikacji, obsługa wyjątków i filtry nie wpływają na to zachowanie.

## <a name="startup-exception-handling"></a>Obsługa wyjątków uruchamiania

Tylko warstwa hostingu może obsługiwać wyjątki, które mają miejsce podczas uruchamiania aplikacji. Hosta można skonfigurować do [przechwytywania błędów uruchamiania](xref:fundamentals/host/web-host#capture-startup-errors) i [przechwytywania szczegółowych błędów](xref:fundamentals/host/web-host#detailed-errors).

Warstwa hostingu może wyświetlać stronę błędu dla przechwyconego błędu uruchamiania tylko wtedy, gdy błąd występuje po powiązaniu adresu/portu hosta. Jeśli powiązanie nie powiedzie się:

* Warstwa hostingu rejestruje wyjątek krytyczny.
* Proces dotnet ulega awarii.
* Nie jest wyświetlana strona błędu, gdy serwer HTTP to [Kestrel](xref:fundamentals/servers/kestrel).

Podczas uruchamiania na [usługach IIS](/iis) (lub usłudze Azure App Service) lub [usługach IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)błąd *502.5 — błąd procesu* jest zwracany przez moduł ASP.NET [Core,](xref:host-and-deploy/aspnet-core-module) jeśli nie można uruchomić procesu. Aby uzyskać więcej informacji, zobacz <xref:test/troubleshoot-azure-iis>.

## <a name="database-error-page"></a>Strona błędu bazy danych

Oprogramowanie pośredniczące strony błędów bazy danych przechwytuje wyjątki związane z bazą danych, które można rozwiązać przy użyciu migracji programu Entity Framework. Gdy wystąpią te wyjątki, jest generowana odpowiedź HTML ze szczegółami możliwych akcji w celu rozwiązania problemu. Ta strona powinna być włączona tylko w środowisku programistycznym. Włącz stronę, dodając `Startup.Configure`kod do:

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a>Filtry wyjątków

W aplikacjach MVC filtry wyjątków można skonfigurować globalnie lub na podstawie na kontroler lub na akcję. W aplikacjach Razor Pages można je skonfigurować globalnie lub na model strony. Filtry te obsługują wszelkie nieobsługiwał wyjątek, który występuje podczas wykonywania akcji kontrolera lub innego filtru. Aby uzyskać więcej informacji, zobacz <xref:mvc/controllers/filters#exception-filters>.

> [!TIP]
> Filtry wyjątków są przydatne w przypadku zalewkowania wyjątków, które występują w ramach akcji MVC, ale nie są tak elastyczne, jak oprogramowanie pośredniczące obsługi wyjątków. Zalecamy użycie oprogramowania pośredniczącego. Filtry należy używać tylko wtedy, gdy trzeba wykonać obsługę błędów inaczej na podstawie tego, na którym wybrano akcję MVC.

## <a name="model-state-errors"></a>Błędy stanu modelu

Aby uzyskać informacje dotyczące obsługi błędów stanu modelu, zobacz [Powiązanie modelu](xref:mvc/models/model-binding) i [Sprawdzanie poprawności modelu](xref:mvc/models/validation).

## <a name="additional-resources"></a>Zasoby dodatkowe

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
