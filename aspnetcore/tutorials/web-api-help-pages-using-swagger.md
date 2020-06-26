---
title: ASP.NET Core stronach pomocy interfejsu API sieci Web w programie Swagger/OpenAPI
author: RicoSuter
description: Ten samouczek zawiera wskazówki dotyczące dodawania struktury Swagger w celu wygenerowania dokumentacji i stron pomocy dla aplikacji interfejsu API sieci Web.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/07/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/web-api-help-pages-using-swagger
ms.openlocfilehash: 815581bbee3169f04f1da67227f6fa8c7275071b
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408815"
---
# <a name="aspnet-core-web-api-help-pages-with-swagger--openapi"></a><span data-ttu-id="ba346-103">ASP.NET Core stronach pomocy interfejsu API sieci Web w programie Swagger/OpenAPI</span><span class="sxs-lookup"><span data-stu-id="ba346-103">ASP.NET Core web API help pages with Swagger / OpenAPI</span></span>

<span data-ttu-id="ba346-104">[Christoph Nienaber](https://twitter.com/zuckerthoben) i [Portoryko Suter](https://blog.rsuter.com/)</span><span class="sxs-lookup"><span data-stu-id="ba346-104">By [Christoph Nienaber](https://twitter.com/zuckerthoben) and [Rico Suter](https://blog.rsuter.com/)</span></span>

<span data-ttu-id="ba346-105">W przypadku korzystania z internetowego interfejsu API zrozumienie jego różnych metod może być trudne dla dewelopera.</span><span class="sxs-lookup"><span data-stu-id="ba346-105">When consuming a Web API, understanding its various methods can be challenging for a developer.</span></span> <span data-ttu-id="ba346-106">Struktura [Swagger](https://swagger.io/), znana również jako [openapi](https://www.openapis.org/), rozwiązuje problem związany z generowaniem użytecznej dokumentacji i stron pomocy dla interfejsów API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="ba346-106">[Swagger](https://swagger.io/), also known as [OpenAPI](https://www.openapis.org/), solves the problem of generating useful documentation and help pages for Web APIs.</span></span> <span data-ttu-id="ba346-107">Zapewnia takie korzyści, jak dokumentacja interaktywna, generowanie zestawu SDK klienta i możliwość odnajdywania interfejsów API.</span><span class="sxs-lookup"><span data-stu-id="ba346-107">It provides benefits such as interactive documentation, client SDK generation, and API discoverability.</span></span>

<span data-ttu-id="ba346-108">W tym artykule opisano implementacje [Swashbuckle. AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) i [NSwag](https://github.com/RicoSuter/NSwag) .NET Swagger:</span><span class="sxs-lookup"><span data-stu-id="ba346-108">In this article, the [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) and [NSwag](https://github.com/RicoSuter/NSwag) .NET Swagger implementations are showcased:</span></span>

* <span data-ttu-id="ba346-109">**Swashbuckle. AspNetCore** to projekt Open Source służący do generowania dokumentów struktury Swagger dla ASP.NET Core interfejsów API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="ba346-109">**Swashbuckle.AspNetCore** is an open source project for generating Swagger documents for ASP.NET Core Web APIs.</span></span>

* <span data-ttu-id="ba346-110">**NSwag** jest innym projektem Open Source na potrzeby generowania dokumentów struktury Swagger i INTEGROWANIA [interfejsu użytkownika struktury Swagger](https://swagger.io/swagger-ui/) lub [ReDoc](https://github.com/Rebilly/ReDoc) do ASP.NET Core interfejsów API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="ba346-110">**NSwag** is another open source project for generating Swagger documents and integrating [Swagger UI](https://swagger.io/swagger-ui/) or [ReDoc](https://github.com/Rebilly/ReDoc) into ASP.NET Core web APIs.</span></span> <span data-ttu-id="ba346-111">Ponadto NSwag oferuje podejścia do generowania kodu klienta C# i języka TypeScript dla interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="ba346-111">Additionally, NSwag offers approaches to generate C# and TypeScript client code for your API.</span></span>

## <a name="what-is-swagger--openapi"></a><span data-ttu-id="ba346-112">Co to jest Swagger/OpenAPI?</span><span class="sxs-lookup"><span data-stu-id="ba346-112">What is Swagger / OpenAPI?</span></span>

<span data-ttu-id="ba346-113">Swagger to specyfikacja języka niezależny od do opisywania interfejsów API [rest](https://en.wikipedia.org/wiki/Representational_state_transfer) .</span><span class="sxs-lookup"><span data-stu-id="ba346-113">Swagger is a language-agnostic specification for describing [REST](https://en.wikipedia.org/wiki/Representational_state_transfer) APIs.</span></span> <span data-ttu-id="ba346-114">Projekt Swagger został przekazano do [inicjatywy openapi](https://www.openapis.org/), w której jest teraz określany jako openapi.</span><span class="sxs-lookup"><span data-stu-id="ba346-114">The Swagger project was donated to the [OpenAPI Initiative](https://www.openapis.org/), where it's now referred to as OpenAPI.</span></span> <span data-ttu-id="ba346-115">Obie nazwy są używane zamiennie; OpenAPI jest jednak preferowane.</span><span class="sxs-lookup"><span data-stu-id="ba346-115">Both names are used interchangeably; however, OpenAPI is preferred.</span></span> <span data-ttu-id="ba346-116">Pozwala to komputerom i ludziom na zrozumienie możliwości usługi bez bezpośredniego dostępu do implementacji (kod źródłowy, dostęp do sieci, dokumentacja).</span><span class="sxs-lookup"><span data-stu-id="ba346-116">It allows both computers and humans to understand the capabilities of a service without any direct access to the implementation (source code, network access, documentation).</span></span> <span data-ttu-id="ba346-117">Jednym z celów jest zminimalizowanie ilości pracy wymaganej do nawiązania połączenia z nieskojarzonymi usługami.</span><span class="sxs-lookup"><span data-stu-id="ba346-117">One goal is to minimize the amount of work needed to connect disassociated services.</span></span> <span data-ttu-id="ba346-118">Innym celem jest skrócenie czasu wymaganego do dokładnego udokumentowania usługi.</span><span class="sxs-lookup"><span data-stu-id="ba346-118">Another goal is to reduce the amount of time needed to accurately document a service.</span></span>

## <a name="swagger-specification-swaggerjson"></a><span data-ttu-id="ba346-119">Specyfikacja struktury Swagger (swagger.jswłączona)</span><span class="sxs-lookup"><span data-stu-id="ba346-119">Swagger specification (swagger.json)</span></span>

<span data-ttu-id="ba346-120">Rdzeń do przepływu Swagger jest domyślnie specyfikacją Swagger &mdash; dokumentu o nazwie *swagger.jsna*.</span><span class="sxs-lookup"><span data-stu-id="ba346-120">The core to the Swagger flow is the Swagger specification&mdash;by default, a document named *swagger.json*.</span></span> <span data-ttu-id="ba346-121">Jest on generowany przez łańcuch narzędzi programu Swagger (lub implementacje innych firm) na podstawie Twojej usługi.</span><span class="sxs-lookup"><span data-stu-id="ba346-121">It's generated by the Swagger tool chain (or third-party implementations of it) based on your service.</span></span> <span data-ttu-id="ba346-122">Opisuje możliwości interfejsu API i sposób uzyskiwania dostępu do niego przy użyciu protokołu HTTP.</span><span class="sxs-lookup"><span data-stu-id="ba346-122">It describes the capabilities of your API and how to access it with HTTP.</span></span> <span data-ttu-id="ba346-123">Służy on do tworzenia interfejsu użytkownika struktury Swagger i jest używany przez łańcuch narzędzi do włączania odnajdywania i generowania kodu klienta.</span><span class="sxs-lookup"><span data-stu-id="ba346-123">It drives the Swagger UI and is used by the tool chain to enable discovery and client code generation.</span></span> <span data-ttu-id="ba346-124">Oto przykład specyfikacji struktury Swagger zredukowany dla zwięzłości:</span><span class="sxs-lookup"><span data-stu-id="ba346-124">Here's an example of a Swagger specification, reduced for brevity:</span></span>

```json
{
   "swagger": "2.0",
   "info": {
       "version": "v1",
       "title": "API V1"
   },
   "basePath": "/",
   "paths": {
       "/api/Todo": {
           "get": {
               "tags": [
                   "Todo"
               ],
               "operationId": "ApiTodoGet",
               "consumes": [],
               "produces": [
                   "text/plain",
                   "application/json",
                   "text/json"
               ],
               "responses": {
                   "200": {
                       "description": "Success",
                       "schema": {
                           "type": "array",
                           "items": {
                               "$ref": "#/definitions/TodoItem"
                           }
                       }
                   }
                }
           },
           "post": {
               ...
           }
       },
       "/api/Todo/{id}": {
           "get": {
               ...
           },
           "put": {
               ...
           },
           "delete": {
               ...
   },
   "definitions": {
       "TodoItem": {
           "type": "object",
            "properties": {
                "id": {
                    "format": "int64",
                    "type": "integer"
                },
                "name": {
                    "type": "string"
                },
                "isComplete": {
                    "default": false,
                    "type": "boolean"
                }
            }
       }
   },
   "securityDefinitions": {}
}
```

## <a name="swagger-ui"></a><span data-ttu-id="ba346-125">Interfejs użytkownika struktury Swagger</span><span class="sxs-lookup"><span data-stu-id="ba346-125">Swagger UI</span></span>

<span data-ttu-id="ba346-126">[Interfejs użytkownika struktury Swagger](https://swagger.io/swagger-ui/) oferuje interfejs użytkownika oparty na sieci Web, który zawiera informacje o usłudze, przy użyciu wygenerowanej specyfikacji struktury Swagger.</span><span class="sxs-lookup"><span data-stu-id="ba346-126">[Swagger UI](https://swagger.io/swagger-ui/) offers a web-based UI that provides information about the service, using the generated Swagger specification.</span></span> <span data-ttu-id="ba346-127">Zarówno Swashbuckle, jak i NSwag zawierają osadzoną wersję interfejsu użytkownika struktury Swagger, dzięki czemu mogą być hostowane w aplikacji ASP.NET Core przy użyciu wywołania rejestracji oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="ba346-127">Both Swashbuckle and NSwag include an embedded version of Swagger UI, so that it can be hosted in your ASP.NET Core app using a middleware registration call.</span></span> <span data-ttu-id="ba346-128">Interfejs użytkownika sieci Web wygląda następująco:</span><span class="sxs-lookup"><span data-stu-id="ba346-128">The web UI looks like this:</span></span>

![Interfejs użytkownika struktury Swagger](web-api-help-pages-using-swagger/_static/swagger-ui.png)

<span data-ttu-id="ba346-130">Każda publiczna Metoda działania w kontrolerach może być testowana z poziomu interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="ba346-130">Each public action method in your controllers can be tested from the UI.</span></span> <span data-ttu-id="ba346-131">Kliknij nazwę metody, aby rozwinąć sekcję.</span><span class="sxs-lookup"><span data-stu-id="ba346-131">Click a method name to expand the section.</span></span> <span data-ttu-id="ba346-132">Dodaj wszelkie niezbędne parametry i kliknij przycisk **Wypróbuj!**.</span><span class="sxs-lookup"><span data-stu-id="ba346-132">Add any necessary parameters, and click **Try it out!**.</span></span>

![Przykład pobierania testów struktury Swagger](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> <span data-ttu-id="ba346-134">Wersja interfejsu użytkownika programu Swagger używana na potrzeby zrzutów ekranu jest w wersji 2.</span><span class="sxs-lookup"><span data-stu-id="ba346-134">The Swagger UI version used for the screenshots is version 2.</span></span> <span data-ttu-id="ba346-135">Aby zapoznać się z wersją 3 przykład, zobacz [przykład petstore](https://petstore.swagger.io/).</span><span class="sxs-lookup"><span data-stu-id="ba346-135">For a version 3 example, see [Petstore example](https://petstore.swagger.io/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="ba346-136">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="ba346-136">Next steps</span></span>

* [<span data-ttu-id="ba346-137">Wprowadzenie do pakietu Swashbuckle</span><span class="sxs-lookup"><span data-stu-id="ba346-137">Get started with Swashbuckle</span></span>](xref:tutorials/get-started-with-swashbuckle)
* [<span data-ttu-id="ba346-138">Wprowadzenie do łańcucha narzędzi NSwag</span><span class="sxs-lookup"><span data-stu-id="ba346-138">Get started with NSwag</span></span>](xref:tutorials/get-started-with-nswag)
