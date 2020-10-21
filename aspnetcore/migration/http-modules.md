---
title: Migrowanie programów obsługi i modułów HTTP do ASP.NET Core oprogramowania pośredniczącego
author: rick-anderson
description: ''
ms.author: riande
ms.date: 12/07/2016
no-loc:
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
uid: migration/http-modules
ms.openlocfilehash: 808215d103db9c5d63fe63b6875a222e6b0ba1fa
ms.sourcegitcommit: b5ebaf42422205d212e3dade93fcefcf7f16db39
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92326617"
---
# <a name="migrate-http-handlers-and-modules-to-aspnet-core-middleware"></a>Migrowanie programów obsługi i modułów HTTP do ASP.NET Core oprogramowania pośredniczącego

W tym artykule przedstawiono sposób migrowania istniejących [modułów i programów ASP.net http z programu System. WebServer](/iis/configuration/system.webserver/) do ASP.NET Core [oprogramowania pośredniczącego](xref:fundamentals/middleware/index).

## <a name="modules-and-handlers-revisited"></a>Moduły i programy obsługi oglądany

Przed przystąpieniem do ASP.NET Core oprogramowania pośredniczącego najpierw podsumowanie sposób działania modułów i programów obsługi HTTP:

![Procedura obsługi modułów](http-modules/_static/moduleshandlers.png)

**Programy obsługi:**

* Klasy implementujące [IHttpHandler](/dotnet/api/system.web.ihttphandler)

* Służy do obsługi żądań o danej nazwie pliku lub rozszerzeniu, takich jak *. Report*

* [Skonfigurowane](/iis/configuration/system.webserver/handlers/) w *Web.config*

**Moduły to:**

* Klasy implementujące [IHttpModule](/dotnet/api/system.web.ihttpmodule)

* Wywołane dla każdego żądania

* Możliwość krótkiego obwodu (zaprzestanie przetwarzania żądania)

* Można dodać do odpowiedzi HTTP lub utworzyć własne

* [Skonfigurowane](/iis/configuration/system.webserver/modules/) w *Web.config*

**Kolejność, w której moduły przetwarzają żądania przychodzące, jest określana na podstawie:**

1. <https://docs.microsoft.com/previous-versions/ms227673(v=vs.140)>, Czyli zdarzenia serii wywoływane przez ASP.NET: [BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest)itd. Każdy moduł może utworzyć procedurę obsługi dla jednego lub wielu zdarzeń.

2. Dla tego samego zdarzenia kolejność, w jakiej są skonfigurowane w *Web.config*.

Oprócz modułów można dodać programy obsługi dla zdarzeń cyklu życia do pliku *Global.asax.cs* . Te programy obsługi są uruchamiane po programach obsługi w skonfigurowanych modułach.

## <a name="from-handlers-and-modules-to-middleware"></a>Z programów obsługi i modułów do oprogramowania pośredniczącego

**Oprogramowanie pośredniczące jest prostsze niż moduły HTTP i programy obsługi:**

* Moduły, programy obsługi, *Global.asax.cs*, *Web.config* (z wyjątkiem konfiguracji usług IIS) i cykl życia aplikacji zostały utracone

* Role obu modułów i programów obsługi zostały przejęte przez oprogramowanie pośredniczące

* Oprogramowanie pośredniczące jest konfigurowane przy użyciu kodu, a nie w *Web.config*

::: moniker range=">= aspnetcore-3.0"

* [Rozgałęzianie potokowe](xref:fundamentals/middleware/index#branch-the-middleware-pipeline) umożliwia wysyłanie żądań do określonego oprogramowania pośredniczącego, w oparciu o nie tylko adres URL, ale również w nagłówkach żądań, ciągach zapytań itd.

::: moniker-end
::: moniker range="< aspnetcore-3.0"

* [Rozgałęzianie potokowe](xref:fundamentals/middleware/index#use-run-and-map) umożliwia wysyłanie żądań do określonego oprogramowania pośredniczącego, w oparciu o nie tylko adres URL, ale również w nagłówkach żądań, ciągach zapytań itd.

::: moniker-end

**Oprogramowanie pośredniczące jest bardzo podobne do modułów:**

* Wywoływane w zasadzie dla każdego żądania

* Możliwość krótkiego obwodu żądania przez [nie przekazanie żądania do następnego oprogramowania pośredniczącego](#http-modules-shortcircuiting-middleware)

* Możliwość utworzenia własnej odpowiedzi HTTP

**Oprogramowanie pośredniczące i moduły są przetwarzane w innej kolejności:**

* Kolejność oprogramowania pośredniczącego opiera się na kolejności, w której są wstawiane do potoku żądania, natomiast kolejność modułów opiera się głównie na <https://docs.microsoft.com/previous-versions/ms227673(v=vs.140)> zdarzeniach

* Kolejność oprogramowania pośredniczącego w odniesieniu do odpowiedzi to odwrotność od żądania, natomiast kolejność modułów jest taka sama dla żądań i odpowiedzi

* Zobacz [Tworzenie potoku oprogramowania pośredniczącego za pomocą IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)

![Oprogramowanie pośredniczące](http-modules/_static/middleware.png)

Należy zwrócić uwagę na to, jak na powyższym obrazie oprogramowanie pośredniczące uwierzytelniania krótko obobwóduje żądanie.

## <a name="migrating-module-code-to-middleware"></a>Migrowanie kodu modułu do oprogramowania pośredniczącego

Istniejący moduł HTTP będzie wyglądać podobnie do tego:

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyModule.cs?highlight=6,8,24,31)]

Jak pokazano na stronie [oprogramowania pośredniczącego](xref:fundamentals/middleware/index) , ASP.NET Core oprogramowanie pośredniczące jest klasą, która udostępnia `Invoke` metodę pobierającą `HttpContext` i zwracającą wynik `Task` . Nowe oprogramowanie pośredniczące będzie wyglądać następująco:

<a name="http-modules-usemiddleware"></a>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddleware.cs?highlight=9,13,20,24,28,30,32)]

Poprzedni szablon oprogramowania pośredniczącego został pobrany z sekcji podczas [pisania oprogramowania pośredniczącego](xref:fundamentals/middleware/write).

Klasa pomocnika *MyMiddlewareExtensions* ułatwia konfigurowanie oprogramowania pośredniczącego w `Startup` klasie. `UseMyMiddleware`Metoda dodaje klasę oprogramowania pośredniczącego do potoku żądania. Usługi wymagane przez oprogramowanie pośredniczące są wprowadzane w konstruktorze oprogramowania pośredniczącego.

<a name="http-modules-shortcircuiting-middleware"></a>

Moduł może zakończyć żądanie, na przykład jeśli użytkownik nie ma autoryzacji:

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyTerminatingModule.cs?highlight=9,10,11,12,13&name=snippet_Terminate)]

Oprogramowanie pośredniczące obsługuje to, nie wywołując `Invoke` w następnym oprogramowaniu pośredniczącym w potoku. Należy pamiętać, że to nie przerywa w pełni żądania, ponieważ poprzednie middlewares nadal będą wywoływane, gdy odpowiedź przejdzie do tyłu przez potok.

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyTerminatingMiddleware.cs?highlight=7,8&name=snippet_Terminate)]

Po przeprowadzeniu migracji funkcjonalności modułu do nowego oprogramowania pośredniczącego, może się okazać, że Twój kod nie kompiluje się, ponieważ `HttpContext` Klasa została znacząco zmieniona w ASP.NET Core. [Później](#migrating-to-the-new-httpcontext)zobaczysz, jak przeprowadzić migrację do nowego ASP.NET Core HttpContext.

## <a name="migrating-module-insertion-into-the-request-pipeline"></a>Migrowanie wstawiania modułu do potoku żądania

Moduły HTTP są zazwyczaj dodawane do potoku żądania przy użyciu *Web.config*:

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32-33,36,43,50,101)]

Przekształć to, [dodając nowe oprogramowanie pośredniczące](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) do potoku żądania w `Startup` klasie:

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=16)]

Dokładne miejsce w potoku, w którym wstawiasz nowe oprogramowanie pośredniczące, zależy od zdarzenia, które zostało obsłużone jako moduł ( `BeginRequest` , `EndRequest` itp.) i jego kolejność na liście modułów w *Web.config*.

Jak wspomniano wcześniej, nie ma cyklu życia aplikacji w ASP.NET Core i kolejności, w której odpowiedzi są przetwarzane przez oprogramowanie pośredniczące, różnią się od kolejności używanej przez moduły. Może to spowodować, że decyzje dotyczące porządkowania są bardziej trudne.

Jeśli porządkowanie stanie się problemem, można podzielić moduł na wiele składników oprogramowania pośredniczącego, które mogą być uporządkowane niezależnie.

## <a name="migrating-handler-code-to-middleware"></a>Migrowanie kodu programu obsługi do oprogramowania pośredniczącego

Procedura obsługi protokołu HTTP wygląda następująco:

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/HttpHandlers/ReportHandler.cs?highlight=5,7,13,14,15,16)]

W projekcie ASP.NET Core można je przetłumaczyć na oprogramowanie pośredniczące podobne do tego:

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/ReportHandlerMiddleware.cs?highlight=7,9,13,20,21,22,23,40,42,44)]

To oprogramowanie pośredniczące jest bardzo podobne do oprogramowania pośredniczącego odpowiadającego modułom. Jedyną rzeczywistą różnicą jest to, że nie jest to wywołanie `_next.Invoke(context)` . Ma to sens, ponieważ program obsługi znajduje się na końcu potoku żądania, więc nie będzie można wywołać następnego oprogramowania pośredniczącego.

## <a name="migrating-handler-insertion-into-the-request-pipeline"></a>Migrowanie wstawiania procedury obsługi do potoku żądania

Konfigurowanie obsługi protokołu HTTP odbywa się w *Web.config* i wygląda następująco:

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32,46-48,50,101)]

Można to przekonwertować, dodając nowe oprogramowanie pośredniczące programu obsługi do potoku żądania w `Startup` klasie, podobnie jak oprogramowanie pośredniczące konwertowane z modułów. Problem z tym podejściem polega na tym, że wyśle wszystkie żądania do nowego oprogramowania pośredniczącego programu obsługi. Jednak tylko żądania z danym rozszerzeniem mogą dotrzeć do oprogramowania pośredniczącego. Zapewnia to takie same funkcje, jak w przypadku programu obsługi protokołu HTTP.

Jednym z rozwiązań jest rozgałęzienie potoku dla żądań z danym rozszerzeniem przy użyciu `MapWhen` metody rozszerzenia. Należy to zrobić w tej samej `Configure` metodzie, w której można dodać inne oprogramowanie pośredniczące:

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=27-34)]

`MapWhen` przyjmuje następujące parametry:

1. Lambda, która pobiera `HttpContext` i zwraca, `true` Jeśli żądanie powinno przejść do gałęzi. Oznacza to, że można rozgałęziać żądania nie tylko na podstawie ich rozszerzenia, ale także w nagłówkach żądań, parametrach ciągu zapytania itd.

2. Lambda, która pobiera `IApplicationBuilder` i dodaje wszystkie oprogramowanie pośredniczące dla gałęzi. Oznacza to, że można dodać dodatkowe oprogramowanie pośredniczące do gałęzi przed programem obsługi oprogramowania pośredniczącego.

Oprogramowanie pośredniczące dodane do potoku, zanim gałąź zostanie wywołana na wszystkich żądaniach; Rozgałęzienie nie będzie miało wpływu na te elementy.

## <a name="loading-middleware-options-using-the-options-pattern"></a>Ładowanie opcji oprogramowania pośredniczącego przy użyciu wzorca opcji

Niektóre moduły i programy obsługi mają opcje konfiguracji, które są przechowywane w *Web.config*. Jednak w ASP.NET Core jest używany nowy model konfiguracji zamiast *Web.config*.

Nowy [system konfiguracji](xref:fundamentals/configuration/index) zapewnia następujące opcje:

* Bezpośrednio wstrzyknąć opcje do oprogramowania pośredniczącego, jak pokazano w [następnej sekcji](#loading-middleware-options-through-direct-injection).

* Użyj [wzorca opcji](xref:fundamentals/configuration/options):

1. Utwórz klasę zawierającą Opcje oprogramowania pośredniczącego, na przykład:

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Options)]

2. Przechowywanie wartości opcji

   System konfiguracji umożliwia przechowywanie wartości opcji w dowolnym miejscu. Jednak większość lokacji używa *appsettings.jsna*, więc zajmiemy się tym podejściem:

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,14-18)]

   *MyMiddlewareOptionsSection* tutaj to nazwa sekcji. Nie musi być taka sama jak nazwa klasy Options.

3. Skojarz wartości opcji z klasą opcji

    Wzorzec opcji używa struktury wstrzykiwania zależności ASP.NET Core do kojarzenia typu opcji (na przykład `MyMiddlewareOptions` ) z `MyMiddlewareOptions` obiektem, który ma rzeczywiste opcje.

    Aktualizowanie `Startup` klasy:

   1. Jeśli używasz *appsettings.json*, Dodaj go do konstruktora konfiguracji w `Startup` konstruktorze:

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Ctor&highlight=5-6)]

   2. Skonfiguruj usługę opcji:

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

   3. Skojarz swoje opcje z klasą opcji:

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=6-8)]

4. Wsuń opcje do konstruktora oprogramowania pośredniczącego. Jest to podobne do iniekcji opcji do kontrolera.

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_MiddlewareWithParams&highlight=4,7,10,15-16)]

   Metoda rozszerzenia [UseMiddleware](#http-modules-usemiddleware) , która dodaje oprogramowanie pośredniczące do `IApplicationBuilder` opieki nad iniekcją zależności.

   Nie jest to ograniczone do `IOptions` obiektów. Każdy inny obiekt, którego potrzebuje oprogramowanie pośredniczące, można wprowadzić w ten sposób.

## <a name="loading-middleware-options-through-direct-injection"></a>Ładowanie opcji oprogramowania pośredniczącego za pośrednictwem bezpośredniego wtrysku

Wzorzec opcji ma zalety tworzenia swobodnego sprzężenia między wartościami opcji i ich konsumentami. Po skojarzeniu klasy opcji z rzeczywistymi wartościami opcji każda inna Klasa może uzyskać dostęp do opcji za pomocą struktury iniekcji zależności. Nie ma potrzeby przekazywania żadnych wartości opcji.

Ten podział działa inaczej, jeśli chcesz użyć tego samego oprogramowania pośredniczącego dwa razy, z innymi opcjami. Na przykład oprogramowanie pośredniczące autoryzacji używane w różnych gałęziach, które zezwalają na różne role. Nie można skojarzyć dwóch różnych obiektów Options z jedną klasą opcji.

Rozwiązaniem jest uzyskanie obiektów Options z rzeczywistymi wartościami opcji w `Startup` klasie i przekazywanie ich bezpośrednio do każdego wystąpienia oprogramowania pośredniczącego.

1. Dodaj drugi klucz do *appsettings.js*

   Aby dodać drugi zestaw opcji do *appsettings.jsw* pliku, Użyj nowego klucza w celu jego jednoznacznej identyfikacji:

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,10-18&highlight=2-5)]

2. Pobierz wartości opcji i przekaż je do oprogramowania pośredniczącego. `Use...`Metoda rozszerzająca (która dodaje oprogramowanie pośredniczące do potoku) jest miejscem logicznym do przekazania w wartościach opcji: 

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=20-23)]

3. Włącz oprogramowanie pośredniczące, aby pobrać parametr Options. Podaj Przeciążenie `Use...` metody rozszerzenia (która przyjmuje parametr Options i przekazuje go do `UseMiddleware` ). Gdy `UseMiddleware` jest wywoływana z parametrami, przekazuje parametry do konstruktora oprogramowania pośredniczącego podczas tworzenia wystąpienia obiektu pośredniczącego.

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Extensions&highlight=9-14)]

   Zwróć uwagę, jak to zawija obiekt Options w `OptionsWrapper` obiekcie. To implementuje `IOptions` , zgodnie z oczekiwaniami w konstruktorze oprogramowania pośredniczącego.

## <a name="migrating-to-the-new-httpcontext"></a>Migrowanie do nowego obiektu HttpContext

Wcześniej zawarto, że `Invoke` Metoda w oprogramowaniu pośredniczącym przyjmuje parametr typu `HttpContext` :

```csharp
public async Task Invoke(HttpContext context)
```

`HttpContext` został znacząco zmieniony w ASP.NET Core. W tej sekcji pokazano, jak przetłumaczyć najczęściej używane właściwości elementu [System. Web. HttpContext](/dotnet/api/system.web.httpcontext) na nowy `Microsoft.AspNetCore.Http.HttpContext` .

### <a name="httpcontext"></a>HttpContext

**Element HttpContext. Items** Wykonuje translację do:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Items)]

**Unikatowy identyfikator żądania (brak odpowiednika system. Web. HttpContext)**

Zapewnia unikatowy identyfikator dla każdego żądania. Bardzo przydatne do uwzględnienia w dziennikach.

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Trace)]

### <a name="httpcontextrequest"></a>Element HttpContext. Request

Element **HttpContext. Request. HttpMethod** Wykonuje translację na:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Method)]

Element **HttpContext. Request. QueryString** tłumaczy na:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Query)]

**Właściwość HttpContext. Request. URL** i **HttpContext. Request. RawUrl** są tłumaczone na:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Url)]

Element **HttpContext. Request. IsSecureConnection** Wykonuje translację na:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Secure)]

Element **HttpContext. Request. UserHostAddress** Wykonuje translację na:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Host)]

Element **HttpContext. Request. Cookie s** tłumaczy na:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Cookies)]

Element **HttpContext. Request. RequestContext. RouteData** tłumaczy na:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Route)]

Element **HttpContext. Request. Heads** Wykonuje translację na:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Headers)]

Element **HttpContext. Request. userAgent** Wykonuje translację na:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Agent)]

Element **HttpContext. Request. UrlReferrer** Wykonuje translację na:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Referrer)]

Element **HttpContext. Request. ContentType** tłumaczy na:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Type)]

Element **HttpContext. Request. form** tłumaczy na:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Form)]

> [!WARNING]
> Odczytaj wartości formularza tylko wtedy, gdy podtyp zawartości to *x-www-form-urlencoded* lub *form-Data*.

Element **HttpContext. Request. InputStream** Wykonuje translację na:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Input)]

> [!WARNING]
> Użyj tego kodu tylko w oprogramowaniu pośredniczącym typu programu obsługi, na końcu potoku.
>
>Nieprzetworzoną treść można odczytać, jak pokazano powyżej tylko raz dla każdego żądania. Oprogramowanie pośredniczące próbujące odczytać treść po pierwszym odczytaniu odczyta pustą treść.
>
>Nie dotyczy to odczytywania formularza jak pokazano wcześniej, ponieważ jest to wykonywane z bufora.

### <a name="httpcontextresponse"></a>HttpContext. Response

**Właściwość HttpContext. Response. status** i **HttpContext. Response. StatusDescription** są tłumaczone na:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Status)]

**Właściwość HttpContext. Response. ContentEncoding** i **HttpContext. Response. ContentType** przekładają się na:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespType)]

**Właściwość HttpContext. Response. ContentType** jest również tłumaczona na:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespTypeOnly)]

Element **HttpContext. Response. Output** tłumaczy na:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Output)]

**HttpContext. Response. TransmitFile**

Obsługa pliku jest omówiona w temacie [oprogramowanie pośredniczące i funkcje żądania](xref:fundamentals/request-features#middleware-and-request-features).

**HttpContext. Response. Headers**

Wysyłanie nagłówków odpowiedzi jest skomplikowane przez fakt, że jeśli ustawisz je po zapisaniu jakichkolwiek elementów w treści odpowiedzi, nie będą one wysyłane.

Rozwiązanie polega na ustawieniu metody wywołania zwrotnego, która będzie wywoływana w prawo przed rozpoczęciem zapisywania do odpowiedzi. Jest to najlepsze rozwiązanie na początku `Invoke` metody w oprogramowaniu pośredniczącym. Jest to metoda wywołania zwrotnego, która ustawia nagłówki odpowiedzi.

Poniższy kod ustawia metodę wywołania zwrotnego o nazwie `SetHeaders` :

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

`SetHeaders`Metoda wywołania zwrotnego będzie wyglądać następująco:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetHeaders)]

**HttpContext. Response. Cookie wolumin**

Cookies przenoszone do przeglądarki w nagłówku *zestawu Cookie * odpowiedzi. W związku z tym wysyłanie cookie s wymaga tego samego wywołania zwrotnego, które jest używane do wysyłania nagłówków odpowiedzi:

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetCookies, state: httpContext);
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

`SetCookies`Metoda wywołania zwrotnego będzie wyglądać następująco:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetCookies)]

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Obsługa protokołu HTTP i moduły HTTP — Omówienie](/iis/configuration/system.webserver/)
* [Konfiguracja](xref:fundamentals/configuration/index)
* [Uruchamianie aplikacji](xref:fundamentals/startup)
* [Oprogramowanie pośredniczące](xref:fundamentals/middleware/index)
