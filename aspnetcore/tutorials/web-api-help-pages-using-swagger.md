---
title: ASP.NET Core stronach pomocy interfejsu API sieci Web w programie Swagger/OpenAPI
author: RicoSuter
description: Ten samouczek zawiera wskazówki dotyczące dodawania struktury Swagger w celu wygenerowania dokumentacji i stron pomocy dla aplikacji interfejsu API sieci Web.
ms.author: scaddie
ms.custom: mvc
ms.date: 07/06/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/web-api-help-pages-using-swagger
ms.openlocfilehash: 66b8278e84df5ee56582254ebe2dc99ada98a9dc
ms.sourcegitcommit: fa89d6553378529ae86b388689ac2c6f38281bb9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060309"
---
# <a name="aspnet-core-web-api-help-pages-with-swagger--openapi"></a><span data-ttu-id="6f224-103">ASP.NET Core stronach pomocy interfejsu API sieci Web w programie Swagger/OpenAPI</span><span class="sxs-lookup"><span data-stu-id="6f224-103">ASP.NET Core web API help pages with Swagger / OpenAPI</span></span>

<span data-ttu-id="6f224-104">[Christoph Nienaber](https://twitter.com/zuckerthoben) i [Portoryko Suter](https://blog.rsuter.com/)</span><span class="sxs-lookup"><span data-stu-id="6f224-104">By [Christoph Nienaber](https://twitter.com/zuckerthoben) and [Rico Suter](https://blog.rsuter.com/)</span></span>

<span data-ttu-id="6f224-105">W przypadku korzystania z internetowego interfejsu API zrozumienie jego różnych metod może być trudne dla dewelopera.</span><span class="sxs-lookup"><span data-stu-id="6f224-105">When consuming a web API, understanding its various methods can be challenging for a developer.</span></span> <span data-ttu-id="6f224-106">Struktura [Swagger](https://swagger.io/), znana również jako [openapi](https://www.openapis.org/), rozwiązuje problem związany z generowaniem użytecznej dokumentacji i stron pomocy dla interfejsów API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="6f224-106">[Swagger](https://swagger.io/), also known as [OpenAPI](https://www.openapis.org/), solves the problem of generating useful documentation and help pages for web APIs.</span></span> <span data-ttu-id="6f224-107">Zapewnia takie korzyści, jak dokumentacja interaktywna, generowanie zestawu SDK klienta i możliwość odnajdywania interfejsów API.</span><span class="sxs-lookup"><span data-stu-id="6f224-107">It provides benefits such as interactive documentation, client SDK generation, and API discoverability.</span></span>

<span data-ttu-id="6f224-108">W tym artykule opisano implementacje [Swashbuckle. AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) i [NSwag](https://github.com/RicoSuter/NSwag) .NET Swagger:</span><span class="sxs-lookup"><span data-stu-id="6f224-108">In this article, the [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) and [NSwag](https://github.com/RicoSuter/NSwag) .NET Swagger implementations are showcased:</span></span>

* <span data-ttu-id="6f224-109">**Swashbuckle. AspNetCore** to projekt Open Source służący do generowania dokumentów struktury Swagger dla ASP.NET Core interfejsów API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="6f224-109">**Swashbuckle.AspNetCore** is an open source project for generating Swagger documents for ASP.NET Core Web APIs.</span></span>

* <span data-ttu-id="6f224-110">**NSwag** jest innym projektem Open Source na potrzeby generowania dokumentów struktury Swagger i INTEGROWANIA [interfejsu użytkownika struktury Swagger](https://swagger.io/swagger-ui/) lub [ReDoc](https://github.com/Rebilly/ReDoc) do ASP.NET Core interfejsów API sieci Web.</span><span class="sxs-lookup"><span data-stu-id="6f224-110">**NSwag** is another open source project for generating Swagger documents and integrating [Swagger UI](https://swagger.io/swagger-ui/) or [ReDoc](https://github.com/Rebilly/ReDoc) into ASP.NET Core web APIs.</span></span> <span data-ttu-id="6f224-111">Ponadto NSwag oferuje podejścia do generowania kodu klienta C# i języka TypeScript dla interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="6f224-111">Additionally, NSwag offers approaches to generate C# and TypeScript client code for your API.</span></span>

## <a name="what-is-swagger--openapi"></a><span data-ttu-id="6f224-112">Co to jest Swagger/OpenAPI?</span><span class="sxs-lookup"><span data-stu-id="6f224-112">What is Swagger / OpenAPI?</span></span>

<span data-ttu-id="6f224-113">Swagger to specyfikacja języka niezależny od do opisywania interfejsów API [rest](https://en.wikipedia.org/wiki/Representational_state_transfer) .</span><span class="sxs-lookup"><span data-stu-id="6f224-113">Swagger is a language-agnostic specification for describing [REST](https://en.wikipedia.org/wiki/Representational_state_transfer) APIs.</span></span> <span data-ttu-id="6f224-114">Projekt Swagger został przekazano do [inicjatywy openapi](https://www.openapis.org/), w której jest teraz określany jako openapi.</span><span class="sxs-lookup"><span data-stu-id="6f224-114">The Swagger project was donated to the [OpenAPI Initiative](https://www.openapis.org/), where it's now referred to as OpenAPI.</span></span> <span data-ttu-id="6f224-115">Obie nazwy są używane zamiennie; OpenAPI jest jednak preferowane.</span><span class="sxs-lookup"><span data-stu-id="6f224-115">Both names are used interchangeably; however, OpenAPI is preferred.</span></span> <span data-ttu-id="6f224-116">Pozwala to komputerom i ludziom na zrozumienie możliwości usługi bez bezpośredniego dostępu do implementacji (kod źródłowy, dostęp do sieci, dokumentacja).</span><span class="sxs-lookup"><span data-stu-id="6f224-116">It allows both computers and humans to understand the capabilities of a service without any direct access to the implementation (source code, network access, documentation).</span></span> <span data-ttu-id="6f224-117">Jednym z celów jest zminimalizowanie ilości pracy wymaganej do nawiązania połączenia z nieskojarzonymi usługami.</span><span class="sxs-lookup"><span data-stu-id="6f224-117">One goal is to minimize the amount of work needed to connect disassociated services.</span></span> <span data-ttu-id="6f224-118">Innym celem jest skrócenie czasu wymaganego do dokładnego udokumentowania usługi.</span><span class="sxs-lookup"><span data-stu-id="6f224-118">Another goal is to reduce the amount of time needed to accurately document a service.</span></span>

## <a name="openapi-specification-openapijson"></a><span data-ttu-id="6f224-119">OpenAPI — Specyfikacja (openapi.json)</span><span class="sxs-lookup"><span data-stu-id="6f224-119">OpenAPI specification (openapi.json)</span></span>

<span data-ttu-id="6f224-120">Rdzeń do przepływu OpenAPI jest domyślnie specyfikacją &mdash; dokumentu o nazwie *openapi.jsna*.</span><span class="sxs-lookup"><span data-stu-id="6f224-120">The core to the OpenAPI flow is the specification&mdash;by default, a document named *openapi.json*.</span></span> <span data-ttu-id="6f224-121">Jest on generowany przez łańcuch narzędzi OpenAPI (lub implementacje innych firm) na podstawie Twojej usługi.</span><span class="sxs-lookup"><span data-stu-id="6f224-121">It's generated by the OpenAPI tool chain (or third-party implementations of it) based on your service.</span></span> <span data-ttu-id="6f224-122">Opisuje możliwości interfejsu API i sposób uzyskiwania dostępu do niego przy użyciu protokołu HTTP.</span><span class="sxs-lookup"><span data-stu-id="6f224-122">It describes the capabilities of your API and how to access it with HTTP.</span></span> <span data-ttu-id="6f224-123">Służy on do tworzenia interfejsu użytkownika struktury Swagger i jest używany przez łańcuch narzędzi do włączania odnajdywania i generowania kodu klienta.</span><span class="sxs-lookup"><span data-stu-id="6f224-123">It drives the Swagger UI and is used by the tool chain to enable discovery and client code generation.</span></span> <span data-ttu-id="6f224-124">Oto przykład specyfikacji OpenAPI, zredukowany dla zwięzłości:</span><span class="sxs-lookup"><span data-stu-id="6f224-124">Here's an example of an OpenAPI specification, reduced for brevity:</span></span>

```json
{
  "openapi": "3.0.1",
  "info": {
    "title": "API V1",
    "version": "v1"
  },
  "paths": {
    "/api/Todo": {
      "get": {
        "tags": [
          "Todo"
        ],
        "operationId": "ApiTodoGet",
        "responses": {
          "200": {
            "description": "Success",
            "content": {
              "text/plain": {
                "schema": {
                  "type": "array",
                  "items": {
                    "$ref": "#/components/schemas/ToDoItem"
                  }
                }
              },
              "application/json": {
                "schema": {
                  "type": "array",
                  "items": {
                    "$ref": "#/components/schemas/ToDoItem"
                  }
                }
              },
              "text/json": {
                "schema": {
                  "type": "array",
                  "items": {
                    "$ref": "#/components/schemas/ToDoItem"
                  }
                }
              }
            }
          }
        }
      },
      "post": {
        …
      }
    },
    "/api/Todo/{id}": {
      "get": {
        …
      },
      "put": {
        …
      },
      "delete": {
        …
      }
    }
  },
  "components": {
    "schemas": {
      "ToDoItem": {
        "type": "object",
        "properties": {
          "id": {
            "type": "integer",
            "format": "int32"
          },
          "name": {
            "type": "string",
            "nullable": true
          },
          "isCompleted": {
            "type": "boolean"
          }
        },
        "additionalProperties": false
      }
    }
  }
}
```

## <a name="swagger-ui"></a><span data-ttu-id="6f224-125">Interfejs użytkownika struktury Swagger</span><span class="sxs-lookup"><span data-stu-id="6f224-125">Swagger UI</span></span>

<span data-ttu-id="6f224-126">[Interfejs użytkownika struktury Swagger](https://swagger.io/swagger-ui/) oferuje interfejs użytkownika oparty na sieci Web, który zawiera informacje o usłudze, przy użyciu wygenerowanej specyfikacji openapi.</span><span class="sxs-lookup"><span data-stu-id="6f224-126">[Swagger UI](https://swagger.io/swagger-ui/) offers a web-based UI that provides information about the service, using the generated OpenAPI specification.</span></span> <span data-ttu-id="6f224-127">Zarówno Swashbuckle, jak i NSwag zawierają osadzoną wersję interfejsu użytkownika struktury Swagger, dzięki czemu mogą być hostowane w aplikacji ASP.NET Core przy użyciu wywołania rejestracji oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="6f224-127">Both Swashbuckle and NSwag include an embedded version of Swagger UI, so that it can be hosted in your ASP.NET Core app using a middleware registration call.</span></span> <span data-ttu-id="6f224-128">Interfejs użytkownika sieci Web wygląda następująco:</span><span class="sxs-lookup"><span data-stu-id="6f224-128">The web UI looks like this:</span></span>

![Interfejs użytkownika struktury Swagger](web-api-help-pages-using-swagger/_static/swagger-ui.png)

<span data-ttu-id="6f224-130">Każda publiczna Metoda działania w kontrolerach może być testowana z poziomu interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="6f224-130">Each public action method in your controllers can be tested from the UI.</span></span> <span data-ttu-id="6f224-131">Kliknij nazwę metody, aby rozwinąć sekcję.</span><span class="sxs-lookup"><span data-stu-id="6f224-131">Click a method name to expand the section.</span></span> <span data-ttu-id="6f224-132">Dodaj wszelkie niezbędne parametry i kliknij przycisk **Wypróbuj!**.</span><span class="sxs-lookup"><span data-stu-id="6f224-132">Add any necessary parameters, and click **Try it out!**.</span></span>

![Przykład pobierania testów struktury Swagger](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> <span data-ttu-id="6f224-134">Wersja interfejsu użytkownika programu Swagger używana na potrzeby zrzutów ekranu jest w wersji 2.</span><span class="sxs-lookup"><span data-stu-id="6f224-134">The Swagger UI version used for the screenshots is version 2.</span></span> <span data-ttu-id="6f224-135">Aby zapoznać się z wersją 3 przykład, zobacz [przykład petstore](https://petstore.swagger.io/).</span><span class="sxs-lookup"><span data-stu-id="6f224-135">For a version 3 example, see [Petstore example](https://petstore.swagger.io/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="6f224-136">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="6f224-136">Next steps</span></span>

* [<span data-ttu-id="6f224-137">Wprowadzenie do pakietu Swashbuckle</span><span class="sxs-lookup"><span data-stu-id="6f224-137">Get started with Swashbuckle</span></span>](xref:tutorials/get-started-with-swashbuckle)
* [<span data-ttu-id="6f224-138">Wprowadzenie do łańcucha narzędzi NSwag</span><span class="sxs-lookup"><span data-stu-id="6f224-138">Get started with NSwag</span></span>](xref:tutorials/get-started-with-nswag)
