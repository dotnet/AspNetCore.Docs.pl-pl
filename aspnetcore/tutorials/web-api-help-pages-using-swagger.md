---
title: ASP.NET Core dokumentację interfejsu API sieci Web za pomocą programu Swagger/OpenAPI
author: RicoSuter
description: Ten samouczek zawiera wskazówki dotyczące dodawania struktury Swagger w celu wygenerowania dokumentacji i stron pomocy dla aplikacji interfejsu API sieci Web.
ms.author: scaddie
ms.custom: mvc
ms.date: 10/29/2020
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
uid: tutorials/web-api-help-pages-using-swagger
ms.openlocfilehash: e5442c88048cf41e289fb476b4082cb6029b1b75
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "93062457"
---
# <a name="aspnet-core-web-api-documentation-with-swagger--openapi"></a><span data-ttu-id="dcbe0-103">ASP.NET Core dokumentację interfejsu API sieci Web za pomocą programu Swagger/OpenAPI</span><span class="sxs-lookup"><span data-stu-id="dcbe0-103">ASP.NET Core web API documentation with Swagger / OpenAPI</span></span>

<span data-ttu-id="dcbe0-104">[Christoph Nienaber](https://twitter.com/zuckerthoben) i [Portoryko Suter](https://blog.rsuter.com/)</span><span class="sxs-lookup"><span data-stu-id="dcbe0-104">By [Christoph Nienaber](https://twitter.com/zuckerthoben) and [Rico Suter](https://blog.rsuter.com/)</span></span>

<span data-ttu-id="dcbe0-105">Swagger (OpenAPI) to specyfikacja języka niezależny od opisująca opisywanie interfejsów API REST.</span><span class="sxs-lookup"><span data-stu-id="dcbe0-105">Swagger (OpenAPI) is a language-agnostic specification for describing REST APIs.</span></span> <span data-ttu-id="dcbe0-106">Dzięki temu zarówno komputery, jak i ludzie mogą zrozumieć możliwości interfejsu API REST bez bezpośredniego dostępu do kodu źródłowego.</span><span class="sxs-lookup"><span data-stu-id="dcbe0-106">It allows both computers and humans to understand the capabilities of a REST API without direct access to the source code.</span></span> <span data-ttu-id="dcbe0-107">Główne cele są następujące:</span><span class="sxs-lookup"><span data-stu-id="dcbe0-107">Its main goals are to:</span></span>

* <span data-ttu-id="dcbe0-108">Zminimalizuj ilość pracy wymaganej do połączenia z odłączonymi usługami.</span><span class="sxs-lookup"><span data-stu-id="dcbe0-108">Minimize the amount of work needed to connect decoupled services.</span></span>
* <span data-ttu-id="dcbe0-109">Skrócenie czasu wymaganego do dokładnego udokumentowania usługi.</span><span class="sxs-lookup"><span data-stu-id="dcbe0-109">Reduce the amount of time needed to accurately document a service.</span></span>

<span data-ttu-id="dcbe0-110">Dwa główne implementacje OpenAPI dla platformy .NET to [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) i [NSwag](https://github.com/RicoSuter/NSwag), zobacz:</span><span class="sxs-lookup"><span data-stu-id="dcbe0-110">The two main OpenAPI implementations for .NET are [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) and [NSwag](https://github.com/RicoSuter/NSwag), see:</span></span>

* [<span data-ttu-id="dcbe0-111">Wprowadzenie z Swashbuckle</span><span class="sxs-lookup"><span data-stu-id="dcbe0-111">Getting Started with Swashbuckle</span></span>](xref:tutorials/get-started-with-swashbuckle)
* [<span data-ttu-id="dcbe0-112">Wprowadzenie z NSwag</span><span class="sxs-lookup"><span data-stu-id="dcbe0-112">Getting Started with NSwag</span></span>](xref:tutorials/get-started-with-nswag)

## <a name="openapi-vs-swagger"></a><span data-ttu-id="dcbe0-113">OpenApi a Swagger</span><span class="sxs-lookup"><span data-stu-id="dcbe0-113">OpenApi vs. Swagger</span></span>

<span data-ttu-id="dcbe0-114">Projekt Swagger został odstawiony do inicjatywy OpenAPI w 2015 i został już określony jako OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="dcbe0-114">The Swagger project was donated to the OpenAPI Initiative in 2015 and has since been referred to as OpenAPI.</span></span> <span data-ttu-id="dcbe0-115">Obie nazwy są używane zamiennie.</span><span class="sxs-lookup"><span data-stu-id="dcbe0-115">Both names are used interchangeably.</span></span> <span data-ttu-id="dcbe0-116">Jednak "OpenAPI" odwołuje się do specyfikacji.</span><span class="sxs-lookup"><span data-stu-id="dcbe0-116">However, "OpenAPI" refers to the specification.</span></span> <span data-ttu-id="dcbe0-117">"Swagger" oznacza rodzinę produktów typu open source i komercyjnych z interfejsu SmartBear Ready, która współpracuje ze specyfikacją OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="dcbe0-117">"Swagger" refers to the family of open-source and commercial products from SmartBear that work with the OpenAPI Specification.</span></span> <span data-ttu-id="dcbe0-118">Kolejne produkty typu open source, takie jak [OpenAPIGenerator](https://github.com/OpenAPITools/openapi-generator), również należą do nazwy rodziny Swagger, mimo że nie zostały wydane przez interfejsu SmartBear Ready.</span><span class="sxs-lookup"><span data-stu-id="dcbe0-118">Subsequent open-source products, such as [OpenAPIGenerator](https://github.com/OpenAPITools/openapi-generator), also fall under the Swagger family name, despite not being released by SmartBear.</span></span>

<span data-ttu-id="dcbe0-119">Krótko mówiąc:</span><span class="sxs-lookup"><span data-stu-id="dcbe0-119">In short:</span></span>

* <span data-ttu-id="dcbe0-120">OpenAPI jest specyfikacją.</span><span class="sxs-lookup"><span data-stu-id="dcbe0-120">OpenAPI is a specification.</span></span>
* <span data-ttu-id="dcbe0-121">Struktura Swagger jest narzędziem korzystającym ze specyfikacji OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="dcbe0-121">Swagger is tooling that uses the OpenAPI specification.</span></span> <span data-ttu-id="dcbe0-122">Na przykład OpenAPIGenerator i SwaggerUI.</span><span class="sxs-lookup"><span data-stu-id="dcbe0-122">For example, OpenAPIGenerator and SwaggerUI.</span></span>

## <a name="openapi-specification-openapijson"></a><span data-ttu-id="dcbe0-123">OpenAPI — Specyfikacja (openapi.json)</span><span class="sxs-lookup"><span data-stu-id="dcbe0-123">OpenAPI specification (openapi.json)</span></span>

<span data-ttu-id="dcbe0-124">Specyfikacja OpenAPI jest dokumentem opisującym możliwości interfejsu API.</span><span class="sxs-lookup"><span data-stu-id="dcbe0-124">The OpenAPI specification is a document that describes the capabilities of your API.</span></span> <span data-ttu-id="dcbe0-125">Dokument jest oparty na adnotacjach XML i atrybutów w obrębie kontrolerów i modeli.</span><span class="sxs-lookup"><span data-stu-id="dcbe0-125">The document is based on the XML and attribute annotations within the controllers and models.</span></span> <span data-ttu-id="dcbe0-126">Jest ona podstawową częścią przepływu OpenAPI i służy do kierowania narzędzi, takich jak SwaggerUI.</span><span class="sxs-lookup"><span data-stu-id="dcbe0-126">It's the core part of the OpenAPI flow and is used to drive tooling such as SwaggerUI.</span></span> <span data-ttu-id="dcbe0-127">Domyślnie jest on o nazwie *openapi.json*.</span><span class="sxs-lookup"><span data-stu-id="dcbe0-127">By default, it's named *openapi.json*.</span></span> <span data-ttu-id="dcbe0-128">Oto przykład specyfikacji OpenAPI, zredukowany dla zwięzłości:</span><span class="sxs-lookup"><span data-stu-id="dcbe0-128">Here's an example of an OpenAPI specification, reduced for brevity:</span></span>

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

## <a name="swagger-ui"></a><span data-ttu-id="dcbe0-129">Interfejs użytkownika struktury Swagger</span><span class="sxs-lookup"><span data-stu-id="dcbe0-129">Swagger UI</span></span>

<span data-ttu-id="dcbe0-130">[Interfejs użytkownika struktury Swagger](https://swagger.io/swagger-ui/) oferuje interfejs użytkownika oparty na sieci Web, który zawiera informacje o usłudze, przy użyciu wygenerowanej specyfikacji openapi.</span><span class="sxs-lookup"><span data-stu-id="dcbe0-130">[Swagger UI](https://swagger.io/swagger-ui/) offers a web-based UI that provides information about the service, using the generated OpenAPI specification.</span></span> <span data-ttu-id="dcbe0-131">Zarówno Swashbuckle, jak i NSwag zawierają osadzoną wersję interfejsu użytkownika struktury Swagger, dzięki czemu mogą być hostowane w aplikacji ASP.NET Core przy użyciu wywołania rejestracji oprogramowania pośredniczącego.</span><span class="sxs-lookup"><span data-stu-id="dcbe0-131">Both Swashbuckle and NSwag include an embedded version of Swagger UI, so that it can be hosted in your ASP.NET Core app using a middleware registration call.</span></span> <span data-ttu-id="dcbe0-132">Interfejs użytkownika sieci Web wygląda następująco:</span><span class="sxs-lookup"><span data-stu-id="dcbe0-132">The web UI looks like this:</span></span>

![Interfejs użytkownika struktury Swagger](web-api-help-pages-using-swagger/_static/swagger-ui.png)

<span data-ttu-id="dcbe0-134">Każda publiczna Metoda działania w kontrolerach może być testowana z poziomu interfejsu użytkownika.</span><span class="sxs-lookup"><span data-stu-id="dcbe0-134">Each public action method in your controllers can be tested from the UI.</span></span> <span data-ttu-id="dcbe0-135">Wybierz nazwę metody, aby rozwinąć sekcję.</span><span class="sxs-lookup"><span data-stu-id="dcbe0-135">Select a method name to expand the section.</span></span> <span data-ttu-id="dcbe0-136">Dodaj wszelkie niezbędne parametry i wybierz pozycję **Wypróbuj!**.</span><span class="sxs-lookup"><span data-stu-id="dcbe0-136">Add any necessary parameters, and select **Try it out!**.</span></span>

![Przykład pobierania testów struktury Swagger](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> <span data-ttu-id="dcbe0-138">Wersja interfejsu użytkownika programu Swagger używana na potrzeby zrzutów ekranu jest w wersji 2.</span><span class="sxs-lookup"><span data-stu-id="dcbe0-138">The Swagger UI version used for the screenshots is version 2.</span></span> <span data-ttu-id="dcbe0-139">Aby zapoznać się z wersją 3 przykład, zobacz [przykład petstore](https://petstore.swagger.io/).</span><span class="sxs-lookup"><span data-stu-id="dcbe0-139">For a version 3 example, see [Petstore example](https://petstore.swagger.io/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="dcbe0-140">Następne kroki</span><span class="sxs-lookup"><span data-stu-id="dcbe0-140">Next steps</span></span>

* [<span data-ttu-id="dcbe0-141">Wprowadzenie do pakietu Swashbuckle</span><span class="sxs-lookup"><span data-stu-id="dcbe0-141">Get started with Swashbuckle</span></span>](xref:tutorials/get-started-with-swashbuckle)
* [<span data-ttu-id="dcbe0-142">Wprowadzenie do łańcucha narzędzi NSwag</span><span class="sxs-lookup"><span data-stu-id="dcbe0-142">Get started with NSwag</span></span>](xref:tutorials/get-started-with-nswag)
