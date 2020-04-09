---
title: ASP.NET Core Web API strony pomocy z Swagger / OpenAPI
author: RicoSuter
description: Ten samouczek zawiera instruktaż dodawania Swagger do generowania dokumentacji i stron pomocy dla aplikacji interfejsu API sieci Web.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/07/2019
uid: tutorials/web-api-help-pages-using-swagger
ms.openlocfilehash: 4408e02996b958bf009903aa1e4eeda9ad4f457c
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658474"
---
# <a name="aspnet-core-web-api-help-pages-with-swagger--openapi"></a><span data-ttu-id="be68a-103">ASP.NET Core web API strony pomocy z Swagger / OpenAPI</span><span class="sxs-lookup"><span data-stu-id="be68a-103">ASP.NET Core web API help pages with Swagger / OpenAPI</span></span>

<span data-ttu-id="be68a-104">Christoph [Nienaber](https://twitter.com/zuckerthoben) i [Rico Suter](https://blog.rsuter.com/)</span><span class="sxs-lookup"><span data-stu-id="be68a-104">By [Christoph Nienaber](https://twitter.com/zuckerthoben) and [Rico Suter](https://blog.rsuter.com/)</span></span>

<span data-ttu-id="be68a-105">Podczas korzystania z interfejsu API sieci Web, zrozumienie jego różnych metod może być trudne dla dewelopera.</span><span class="sxs-lookup"><span data-stu-id="be68a-105">When consuming a Web API, understanding its various methods can be challenging for a developer.</span></span> <span data-ttu-id="be68a-106">[Swagger](https://swagger.io/), znany również jako [OpenAPI,](https://www.openapis.org/)rozwiązuje problem generowania przydatnych dokumentów i stron pomocy dla interfejsów API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="be68a-106">[Swagger](https://swagger.io/), also known as [OpenAPI](https://www.openapis.org/), solves the problem of generating useful documentation and help pages for Web APIs.</span></span> <span data-ttu-id="be68a-107">Zapewnia korzyści, takie jak interaktywna dokumentacja, generowanie sdk klienta i wykrywalność interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="be68a-107">It provides benefits such as interactive documentation, client SDK generation, and API discoverability.</span></span>

<span data-ttu-id="be68a-108">W tym artykule, [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) i [NSwag](https://github.com/RicoSuter/NSwag) .NET Swagger implementacje są prezentowane:</span><span class="sxs-lookup"><span data-stu-id="be68a-108">In this article, the [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) and [NSwag](https://github.com/RicoSuter/NSwag) .NET Swagger implementations are showcased:</span></span>

* <span data-ttu-id="be68a-109">**Swashbuckle.AspNetCore** to projekt open source do generowania dokumentów Swagger dla ASP.NET Core Web API.</span><span class="sxs-lookup"><span data-stu-id="be68a-109">**Swashbuckle.AspNetCore** is an open source project for generating Swagger documents for ASP.NET Core Web APIs.</span></span>

* <span data-ttu-id="be68a-110">**NSwag** to kolejny projekt open source do generowania dokumentów Swagger i integracji [Swagger UI](https://swagger.io/swagger-ui/) lub [ReDoc](https://github.com/Rebilly/ReDoc) w ASP.NET Core interfejsów API sieci.</span><span class="sxs-lookup"><span data-stu-id="be68a-110">**NSwag** is another open source project for generating Swagger documents and integrating [Swagger UI](https://swagger.io/swagger-ui/) or [ReDoc](https://github.com/Rebilly/ReDoc) into ASP.NET Core web APIs.</span></span> <span data-ttu-id="be68a-111">Ponadto NSwag oferuje podejścia do generowania kodu klienta języka C# i TypeScript dla interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="be68a-111">Additionally, NSwag offers approaches to generate C# and TypeScript client code for your API.</span></span>

## <a name="what-is-swagger--openapi"></a><span data-ttu-id="be68a-112">Co to jest Swagger / OpenAPI?</span><span class="sxs-lookup"><span data-stu-id="be68a-112">What is Swagger / OpenAPI?</span></span>

<span data-ttu-id="be68a-113">Swagger jest specyfikacją niezależną od języka do opisywania interfejsów API [REST.](https://en.wikipedia.org/wiki/Representational_state_transfer)</span><span class="sxs-lookup"><span data-stu-id="be68a-113">Swagger is a language-agnostic specification for describing [REST](https://en.wikipedia.org/wiki/Representational_state_transfer) APIs.</span></span> <span data-ttu-id="be68a-114">Projekt Swagger został przekazany na [rzecz Inicjatywy OpenAPI,](https://www.openapis.org/)gdzie jest teraz określany jako OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="be68a-114">The Swagger project was donated to the [OpenAPI Initiative](https://www.openapis.org/), where it's now referred to as OpenAPI.</span></span> <span data-ttu-id="be68a-115">Obie nazwy są używane zamiennie; jednak OpenAPI jest preferowany.</span><span class="sxs-lookup"><span data-stu-id="be68a-115">Both names are used interchangeably; however, OpenAPI is preferred.</span></span> <span data-ttu-id="be68a-116">Umożliwia zarówno komputerom, jak i ludziom zrozumienie możliwości usługi bez bezpośredniego dostępu do implementacji (kod źródłowy, dostęp do sieci, dokumentacja).</span><span class="sxs-lookup"><span data-stu-id="be68a-116">It allows both computers and humans to understand the capabilities of a service without any direct access to the implementation (source code, network access, documentation).</span></span> <span data-ttu-id="be68a-117">Jednym z celów jest zminimalizowanie ilości pracy potrzebnej do połączenia usług rozłączonych.</span><span class="sxs-lookup"><span data-stu-id="be68a-117">One goal is to minimize the amount of work needed to connect disassociated services.</span></span> <span data-ttu-id="be68a-118">Innym celem jest skrócenie czasu potrzebnego do dokładnego udokumentowania usługi.</span><span class="sxs-lookup"><span data-stu-id="be68a-118">Another goal is to reduce the amount of time needed to accurately document a service.</span></span>

## <a name="swagger-specification-swaggerjson"></a><span data-ttu-id="be68a-119">Specyfikacja Swaggera (swagger.json)</span><span class="sxs-lookup"><span data-stu-id="be68a-119">Swagger specification (swagger.json)</span></span>

<span data-ttu-id="be68a-120">Rdzeniem przepływu Swagger jest specyfikacja&mdash;Swagger domyślnie, dokument o nazwie *swagger.json*.</span><span class="sxs-lookup"><span data-stu-id="be68a-120">The core to the Swagger flow is the Swagger specification&mdash;by default, a document named *swagger.json*.</span></span> <span data-ttu-id="be68a-121">Jest generowany przez łańcuch narzędzi Swagger (lub implementacje innych firm) na podstawie usługi.</span><span class="sxs-lookup"><span data-stu-id="be68a-121">It's generated by the Swagger tool chain (or third-party implementations of it) based on your service.</span></span> <span data-ttu-id="be68a-122">Opisano w nim możliwości interfejsu API i sposób uzyskiwania do niego dostępu za pomocą protokołu HTTP.</span><span class="sxs-lookup"><span data-stu-id="be68a-122">It describes the capabilities of your API and how to access it with HTTP.</span></span> <span data-ttu-id="be68a-123">Napędza interfejs użytkownika Swagger i jest używany przez łańcuch narzędzi, aby umożliwić odnajdowanie i generowanie kodu klienta.</span><span class="sxs-lookup"><span data-stu-id="be68a-123">It drives the Swagger UI and is used by the tool chain to enable discovery and client code generation.</span></span> <span data-ttu-id="be68a-124">Oto przykład specyfikacji Swagger, zredukowane dla zwięzłości:</span><span class="sxs-lookup"><span data-stu-id="be68a-124">Here's an example of a Swagger specification, reduced for brevity:</span></span>

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

## <a name="swagger-ui"></a><span data-ttu-id="be68a-125">Interfejs użytkownika swagger</span><span class="sxs-lookup"><span data-stu-id="be68a-125">Swagger UI</span></span>

<span data-ttu-id="be68a-126">[Interfejs użytkownika Swagger](https://swagger.io/swagger-ui/) oferuje interfejs użytkownika oparty na sieci Web, który dostarcza informacji o usłudze przy użyciu wygenerowanej specyfikacji Swagger.</span><span class="sxs-lookup"><span data-stu-id="be68a-126">[Swagger UI](https://swagger.io/swagger-ui/) offers a web-based UI that provides information about the service, using the generated Swagger specification.</span></span> <span data-ttu-id="be68a-127">Zarówno Swashbuckle i NSwag zawierają osadzoną wersję interfejsu użytkownika Swagger, dzięki czemu może być hostowany w aplikacji ASP.NET Core przy użyciu wywołania rejestracji oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="be68a-127">Both Swashbuckle and NSwag include an embedded version of Swagger UI, so that it can be hosted in your ASP.NET Core app using a middleware registration call.</span></span> <span data-ttu-id="be68a-128">Interfejs użytkownika sieci Web wygląda następująco:</span><span class="sxs-lookup"><span data-stu-id="be68a-128">The web UI looks like this:</span></span>

![Interfejs użytkownika swagger](web-api-help-pages-using-swagger/_static/swagger-ui.png)

<span data-ttu-id="be68a-130">Każda metoda akcji publicznej w kontrolerach można przetestować z interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="be68a-130">Each public action method in your controllers can be tested from the UI.</span></span> <span data-ttu-id="be68a-131">Kliknij nazwę metody, aby rozwinąć sekcję.</span><span class="sxs-lookup"><span data-stu-id="be68a-131">Click a method name to expand the section.</span></span> <span data-ttu-id="be68a-132">Dodaj wszystkie niezbędne parametry i kliknij przycisk **Wypróbuj!**.</span><span class="sxs-lookup"><span data-stu-id="be68a-132">Add any necessary parameters, and click **Try it out!**.</span></span>

![Przykład testu Swagger GET](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> <span data-ttu-id="be68a-134">Wersja interfejsu użytkownika Swagger używana do zrzutów ekranu jest w wersji 2.</span><span class="sxs-lookup"><span data-stu-id="be68a-134">The Swagger UI version used for the screenshots is version 2.</span></span> <span data-ttu-id="be68a-135">Przykład w wersji 3 można znaleźć w [przykładzie sklepu petstore.](https://petstore.swagger.io/)</span><span class="sxs-lookup"><span data-stu-id="be68a-135">For a version 3 example, see [Petstore example](https://petstore.swagger.io/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="be68a-136">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="be68a-136">Next steps</span></span>

* [<span data-ttu-id="be68a-137">Wprowadzenie do pakietu Swashbuckle</span><span class="sxs-lookup"><span data-stu-id="be68a-137">Get started with Swashbuckle</span></span>](xref:tutorials/get-started-with-swashbuckle)
* [<span data-ttu-id="be68a-138">Wprowadzenie do łańcucha narzędzi NSwag</span><span class="sxs-lookup"><span data-stu-id="be68a-138">Get started with NSwag</span></span>](xref:tutorials/get-started-with-nswag)
