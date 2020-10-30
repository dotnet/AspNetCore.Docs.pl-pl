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
ms.sourcegitcommit: 0d40fc4932531ce13fc4ee9432144584e03c2f1c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93062457"
---
# <a name="aspnet-core-web-api-documentation-with-swagger--openapi"></a>ASP.NET Core dokumentację interfejsu API sieci Web za pomocą programu Swagger/OpenAPI

[Christoph Nienaber](https://twitter.com/zuckerthoben) i [Portoryko Suter](https://blog.rsuter.com/)

Swagger (OpenAPI) to specyfikacja języka niezależny od opisująca opisywanie interfejsów API REST. Dzięki temu zarówno komputery, jak i ludzie mogą zrozumieć możliwości interfejsu API REST bez bezpośredniego dostępu do kodu źródłowego. Główne cele są następujące:

* Zminimalizuj ilość pracy wymaganej do połączenia z odłączonymi usługami.
* Skrócenie czasu wymaganego do dokładnego udokumentowania usługi.

Dwa główne implementacje OpenAPI dla platformy .NET to [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) i [NSwag](https://github.com/RicoSuter/NSwag), zobacz:

* [Wprowadzenie z Swashbuckle](xref:tutorials/get-started-with-swashbuckle)
* [Wprowadzenie z NSwag](xref:tutorials/get-started-with-nswag)

## <a name="openapi-vs-swagger"></a>OpenApi a Swagger

Projekt Swagger został odstawiony do inicjatywy OpenAPI w 2015 i został już określony jako OpenAPI. Obie nazwy są używane zamiennie. Jednak "OpenAPI" odwołuje się do specyfikacji. "Swagger" oznacza rodzinę produktów typu open source i komercyjnych z interfejsu SmartBear Ready, która współpracuje ze specyfikacją OpenAPI. Kolejne produkty typu open source, takie jak [OpenAPIGenerator](https://github.com/OpenAPITools/openapi-generator), również należą do nazwy rodziny Swagger, mimo że nie zostały wydane przez interfejsu SmartBear Ready.

Krótko mówiąc:

* OpenAPI jest specyfikacją.
* Struktura Swagger jest narzędziem korzystającym ze specyfikacji OpenAPI. Na przykład OpenAPIGenerator i SwaggerUI.

## <a name="openapi-specification-openapijson"></a>OpenAPI — Specyfikacja (openapi.json)

Specyfikacja OpenAPI jest dokumentem opisującym możliwości interfejsu API. Dokument jest oparty na adnotacjach XML i atrybutów w obrębie kontrolerów i modeli. Jest ona podstawową częścią przepływu OpenAPI i służy do kierowania narzędzi, takich jak SwaggerUI. Domyślnie jest on o nazwie *openapi.json* . Oto przykład specyfikacji OpenAPI, zredukowany dla zwięzłości:

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

## <a name="swagger-ui"></a>Interfejs użytkownika struktury Swagger

[Interfejs użytkownika struktury Swagger](https://swagger.io/swagger-ui/) oferuje interfejs użytkownika oparty na sieci Web, który zawiera informacje o usłudze, przy użyciu wygenerowanej specyfikacji openapi. Zarówno Swashbuckle, jak i NSwag zawierają osadzoną wersję interfejsu użytkownika struktury Swagger, dzięki czemu mogą być hostowane w aplikacji ASP.NET Core przy użyciu wywołania rejestracji oprogramowania pośredniczącego. Interfejs użytkownika sieci Web wygląda następująco:

![Interfejs użytkownika struktury Swagger](web-api-help-pages-using-swagger/_static/swagger-ui.png)

Każda publiczna Metoda działania w kontrolerach może być testowana z poziomu interfejsu użytkownika. Wybierz nazwę metody, aby rozwinąć sekcję. Dodaj wszelkie niezbędne parametry i wybierz pozycję **Wypróbuj!** .

![Przykład pobierania testów struktury Swagger](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> Wersja interfejsu użytkownika programu Swagger używana na potrzeby zrzutów ekranu jest w wersji 2. Aby zapoznać się z wersją 3 przykład, zobacz [przykład petstore](https://petstore.swagger.io/).

## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do pakietu Swashbuckle](xref:tutorials/get-started-with-swashbuckle)
* [Wprowadzenie do łańcucha narzędzi NSwag](xref:tutorials/get-started-with-nswag)
