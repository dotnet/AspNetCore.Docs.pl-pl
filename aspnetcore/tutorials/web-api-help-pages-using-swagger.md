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
# <a name="aspnet-core-web-api-help-pages-with-swagger--openapi"></a>ASP.NET Core web API strony pomocy z Swagger / OpenAPI

Christoph [Nienaber](https://twitter.com/zuckerthoben) i [Rico Suter](https://blog.rsuter.com/)

Podczas korzystania z interfejsu API sieci Web, zrozumienie jego różnych metod może być trudne dla dewelopera. [Swagger](https://swagger.io/), znany również jako [OpenAPI,](https://www.openapis.org/)rozwiązuje problem generowania przydatnych dokumentów i stron pomocy dla interfejsów API sieci Web. Zapewnia korzyści, takie jak interaktywna dokumentacja, generowanie sdk klienta i wykrywalność interfejsu API.

W tym artykule, [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) i [NSwag](https://github.com/RicoSuter/NSwag) .NET Swagger implementacje są prezentowane:

* **Swashbuckle.AspNetCore** to projekt open source do generowania dokumentów Swagger dla ASP.NET Core Web API.

* **NSwag** to kolejny projekt open source do generowania dokumentów Swagger i integracji [Swagger UI](https://swagger.io/swagger-ui/) lub [ReDoc](https://github.com/Rebilly/ReDoc) w ASP.NET Core interfejsów API sieci. Ponadto NSwag oferuje podejścia do generowania kodu klienta języka C# i TypeScript dla interfejsu API.

## <a name="what-is-swagger--openapi"></a>Co to jest Swagger / OpenAPI?

Swagger jest specyfikacją niezależną od języka do opisywania interfejsów API [REST.](https://en.wikipedia.org/wiki/Representational_state_transfer) Projekt Swagger został przekazany na [rzecz Inicjatywy OpenAPI,](https://www.openapis.org/)gdzie jest teraz określany jako OpenAPI. Obie nazwy są używane zamiennie; jednak OpenAPI jest preferowany. Umożliwia zarówno komputerom, jak i ludziom zrozumienie możliwości usługi bez bezpośredniego dostępu do implementacji (kod źródłowy, dostęp do sieci, dokumentacja). Jednym z celów jest zminimalizowanie ilości pracy potrzebnej do połączenia usług rozłączonych. Innym celem jest skrócenie czasu potrzebnego do dokładnego udokumentowania usługi.

## <a name="swagger-specification-swaggerjson"></a>Specyfikacja Swaggera (swagger.json)

Rdzeniem przepływu Swagger jest specyfikacja&mdash;Swagger domyślnie, dokument o nazwie *swagger.json*. Jest generowany przez łańcuch narzędzi Swagger (lub implementacje innych firm) na podstawie usługi. Opisano w nim możliwości interfejsu API i sposób uzyskiwania do niego dostępu za pomocą protokołu HTTP. Napędza interfejs użytkownika Swagger i jest używany przez łańcuch narzędzi, aby umożliwić odnajdowanie i generowanie kodu klienta. Oto przykład specyfikacji Swagger, zredukowane dla zwięzłości:

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

## <a name="swagger-ui"></a>Interfejs użytkownika swagger

[Interfejs użytkownika Swagger](https://swagger.io/swagger-ui/) oferuje interfejs użytkownika oparty na sieci Web, który dostarcza informacji o usłudze przy użyciu wygenerowanej specyfikacji Swagger. Zarówno Swashbuckle i NSwag zawierają osadzoną wersję interfejsu użytkownika Swagger, dzięki czemu może być hostowany w aplikacji ASP.NET Core przy użyciu wywołania rejestracji oprogramowania pośredniczącego. Interfejs użytkownika sieci Web wygląda następująco:

![Interfejs użytkownika swagger](web-api-help-pages-using-swagger/_static/swagger-ui.png)

Każda metoda akcji publicznej w kontrolerach można przetestować z interfejsu użytkownika. Kliknij nazwę metody, aby rozwinąć sekcję. Dodaj wszystkie niezbędne parametry i kliknij przycisk **Wypróbuj!**.

![Przykład testu Swagger GET](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> Wersja interfejsu użytkownika Swagger używana do zrzutów ekranu jest w wersji 2. Przykład w wersji 3 można znaleźć w [przykładzie sklepu petstore.](https://petstore.swagger.io/)

## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do pakietu Swashbuckle](xref:tutorials/get-started-with-swashbuckle)
* [Wprowadzenie do łańcucha narzędzi NSwag](xref:tutorials/get-started-with-nswag)
