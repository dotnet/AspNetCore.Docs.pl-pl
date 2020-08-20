---
title: Obsługa żądań z kontrolerami w ASP.NET Core MVC
author: ardalis
description: ''
ms.author: riande
ms.date: 12/05/2019
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
uid: mvc/controllers/actions
ms.openlocfilehash: 9542a7c0fd16c00f46ee69c5873878a7c70ef626
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88630331"
---
# <a name="handle-requests-with-controllers-in-aspnet-core-mvc"></a>Obsługa żądań z kontrolerami w ASP.NET Core MVC

[Steve Kowalski](https://ardalis.com/) i [Scott Addie](https://github.com/scottaddie)

Kontrolery, akcje i wyniki akcji są podstawową częścią sposobu, w jaki deweloperzy tworzą aplikacje przy użyciu ASP.NET Core MVC.

## <a name="what-is-a-controller"></a>Co to jest kontroler?

Kontroler służy do definiowania i grupowania zestawu akcji. Akcja (lub *Metoda akcji*) to metoda na kontrolerze, który obsługuje żądania. Kontrolery logicznie grupują podobne działania jednocześnie. Ta agregacja akcji umożliwia stosowanie wspólnych zestawów reguł, takich jak routing, buforowanie i autoryzacja, które mają być stosowane zbiorczo. Żądania są mapowane na akcje za poorednictwem [routingu](xref:mvc/controllers/routing).

Według Konwencji, klasy kontrolera:

* Znajduje się w folderze głównych *kontrolerów* poziomu projektu.
* Dziedzicz od `Microsoft.AspNetCore.Mvc.Controller` .

Kontroler jest klasą instantiable, w której spełniony jest co najmniej jeden z następujących warunków:

* Nazwa klasy jest sufiksem `Controller` .
* Klasa dziedziczy z klasy, której nazwa jest sufiksem `Controller` .
* Ten `[Controller]` atrybut jest stosowany do klasy.

Klasa kontrolera nie może mieć skojarzonego `[NonController]` atrybutu.

Kontrolery powinny przestrzegać [zasad jawnych zależności](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies). Istnieje kilka podejścia do implementowania tej zasady. Jeśli wiele akcji kontrolera wymaga tej samej usługi, rozważ użycie [iniekcji konstruktora](xref:mvc/controllers/dependency-injection#constructor-injection) , aby zażądać tych zależności. Jeśli usługa jest wymagana tylko przez pojedynczą metodę akcji, należy rozważyć użycie [iniekcji akcji](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices) , aby zażądać zależności.

W przypadku wzorca **M**odelu-**V**każ-**C**kontroler jest odpowiedzialny za wstępne przetwarzanie żądania i tworzenie wystąpienia modelu. Ogólnie rzecz biorąc, decyzje biznesowe powinny być wykonywane w ramach modelu.

Kontroler pobiera wynik przetwarzania modelu (jeśli istnieje) i zwraca odpowiedni widok oraz powiązane z nim dane widoku lub wynik wywołania interfejsu API. Dowiedz się więcej na [temat ASP.NET Core MVC](xref:mvc/overview) i [rozpocznij pracę z ASP.NET Core MVC i Visual Studio](xref:tutorials/first-mvc-app/start-mvc).

Kontroler jest abstrakcją *poziomu interfejsu użytkownika* . Celem jest zapewnienie, że dane żądania są prawidłowe i można wybrać widok (lub wynik dla interfejsu API), który ma zostać zwrócony. W przypadku aplikacji z obsługą stałej nie obejmuje ona bezpośrednio dostępu do danych ani logiki biznesowej. Zamiast tego kontroler delegowany do usług obsługujących te obowiązki.

## <a name="defining-actions"></a>Definiowanie akcji

Metody publiczne na kontrolerze, z wyjątkiem tych z `[NonAction]` atrybutem, są akcjami. Parametry w akcjach są powiązane z danymi żądania i są weryfikowane przy użyciu [powiązania modelu](xref:mvc/models/model-binding). Walidacja modelu odbywa się dla wszystkich elementów, które są powiązane z modelem. `ModelState.IsValid`Wartość właściwości wskazuje, czy powiązanie modelu i walidacja zakończyły się powodzeniem.

Metody akcji powinny zawierać logikę mapowania żądania do zagadnienia biznesowego. Kwestie biznesowe powinny być zwykle reprezentowane jako usługi, do których kontroler uzyskuje dostęp poprzez [iniekcję zależności](xref:mvc/controllers/dependency-injection). Następnie akcje mapują wynik akcji biznesowej do stanu aplikacji.

Akcje mogą zwracać wszystko, ale często zwracają wystąpienie `IActionResult` (lub `Task<IActionResult>` dla metod asynchronicznych), które generuje odpowiedź. Metoda akcji jest odpowiedzialna za wybór *rodzaju odpowiedzi*. Wynik akcji *wykonuje odpowiedź*.

### <a name="controller-helper-methods"></a>Metody pomocnika kontrolera

Kontrolery zwykle dziedziczą z [kontrolera](/dotnet/api/microsoft.aspnetcore.mvc.controller), chociaż nie jest to wymagane. Wyprowadzanie z `Controller` programu zapewnia dostęp do trzech kategorii metod pomocnika:

#### <a name="1-methods-resulting-in-an-empty-response-body"></a>1. metody z wynikiem pustej treści odpowiedzi

Brak `Content-Type` nagłówka odpowiedzi HTTP, ponieważ treść odpowiedzi nie zawiera treści do opisania.

W tej kategorii istnieją dwa typy wyników: redirect i kod stanu HTTP.

* **Kod stanu HTTP**

    Ten typ zwraca kod stanu HTTP. Kilka metod pomocnika tego typu to `BadRequest` , `NotFound` , i `Ok` . Na przykład program `return BadRequest();` generuje kod stanu 400 po wykonaniu. Gdy metody takie jak `BadRequest` , `NotFound` i `Ok` są przeciążone, nie kwalifikują się do odpowiedzi na kod stanu HTTP, ponieważ odbywa się negocjowanie zawartości.

* **Przekierowanie**

    Ten typ zwraca przekierowanie do akcji lub lokalizacji docelowej (przy użyciu `Redirect` , `LocalRedirect` , `RedirectToAction` lub `RedirectToRoute` ). Na przykład `return RedirectToAction("Complete", new {id = 123});` przekierowuje do `Complete` , przekazując obiekt anonimowy.

    Typ wyniku przekierowania różni się od typu kodu stanu HTTP przede wszystkim przy dodawaniu `Location` nagłówka odpowiedzi HTTP.

#### <a name="2-methods-resulting-in-a-non-empty-response-body-with-a-predefined-content-type"></a>2. metody, które w wyniku niepustej treści odpowiedzi ze wstępnie zdefiniowanym typem zawartości

Większość metod pomocniczych w tej kategorii zawiera `ContentType` Właściwość, co pozwala na określenie `Content-Type` nagłówka odpowiedzi opisującego treść odpowiedzi.

W tej kategorii istnieją dwa typy wyników: [Wyświetl](xref:mvc/views/overview) i [sformatowaną odpowiedź](xref:web-api/advanced/formatting).

* **Wyświetlanie**

    Ten typ zwraca widok, który używa modelu do renderowania kodu HTML. Na przykład `return View(customer);` przekazuje model do widoku dla powiązania danych.

* **Sformatowana odpowiedź**

    Ten typ zwraca kod JSON lub podobny format wymiany danych, aby reprezentować obiekt w określony sposób. Na przykład `return Json(customer);` Serializacja podanego obiektu w formacie JSON.
    
    Inne popularne metody tego typu obejmują `File` i `PhysicalFile` . Na przykład `return PhysicalFile(customerFilePath, "text/xml");` zwraca [PhysicalFileResult](/dotnet/api/microsoft.aspnetcore.mvc.physicalfileresult).

#### <a name="3-methods-resulting-in-a-non-empty-response-body-formatted-in-a-content-type-negotiated-with-the-client"></a>3. metody powodujące niepustą treść odpowiedzi sformatowaną w typie zawartości negocjowanej z klientem

Ta kategoria jest lepiej znana jako **negocjowanie zawartości**. [Negocjowanie zawartości](xref:web-api/advanced/formatting#content-negotiation) ma zastosowanie zawsze, gdy akcja zwraca typ [ObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.objectresult) lub coś innego niż implementacja [IActionResult](/dotnet/api/microsoft.aspnetcore.mvc.iactionresult) . Akcja zwracająca brak `IActionResult` implementacji (na przykład `object` ) zwraca również sformatowaną odpowiedź.

Niektóre metody pomocnika tego typu obejmują `BadRequest` , `CreatedAtRoute` , i `Ok` . Przykłady tych metod obejmują `return BadRequest(modelState);` odpowiednio, `return CreatedAtRoute("routename", values, newobject);` i `return Ok(value);` . Należy pamiętać, że `BadRequest` i `Ok` przeprowadzić negocjację zawartości tylko wtedy, gdy przekazała wartość; bez przekazywania wartości, zamiast tego jako typy wyników kodu stanu HTTP. Z `CreatedAtRoute` drugiej strony Metoda zawsze wykonuje negocjację zawartości, ponieważ jej przeciążenia wymagają, aby wartość została przekazana.

### <a name="cross-cutting-concerns"></a>Zagadnienia dotyczące wycinania

Aplikacje zwykle udostępniają części ich przepływu pracy. Przykładem może być aplikacja wymagająca uwierzytelniania w celu uzyskania dostępu do koszyka lub aplikacja, która przechowuje dane na niektórych stronach. Aby wykonać logikę przed lub po metodzie akcji, użyj *filtru*. Użycie [filtrów](xref:mvc/controllers/filters) na temat zagadnień związanych z rozcinaniem może zmniejszyć liczbę operacji duplikowania.

Większość atrybutów filtru, takich jak `[Authorize]` , może być stosowana na poziomie kontrolera lub akcji w zależności od wymaganego poziomu szczegółowości.

Obsługa błędów i buforowanie odpowiedzi często są związane z obcinaniem:
* [Obsługa błędów](xref:mvc/controllers/filters#exception-filters)
* [Buforowanie odpowiedzi](xref:performance/caching/response)

Wiele zagadnień związanych z wycinaniem można obsłużyć za pomocą filtrów lub niestandardowych programów [pośredniczących](xref:fundamentals/middleware/index).
