---
title: ASP.NET Core stronach pomocy interfejsu API sieci Web w programie Swagger/OpenAPI
author: RicoSuter
description: Ten samouczek zawiera wskazówki dotyczące dodawania struktury Swagger w celu wygenerowania dokumentacji i stron pomocy dla aplikacji interfejsu API sieci Web.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/07/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/web-api-help-pages-using-swagger
ms.openlocfilehash: bde38fcbc11ef36c42523acb182fc62a934821c3
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774525"
---
# <a name="aspnet-core-web-api-help-pages-with-swagger--openapi"></a>ASP.NET Core stronach pomocy interfejsu API sieci Web w programie Swagger/OpenAPI

[Christoph Nienaber](https://twitter.com/zuckerthoben) i [Portoryko Suter](https://blog.rsuter.com/)

W przypadku korzystania z internetowego interfejsu API zrozumienie jego różnych metod może być trudne dla dewelopera. Struktura [Swagger](https://swagger.io/), znana również jako [openapi](https://www.openapis.org/), rozwiązuje problem związany z generowaniem użytecznej dokumentacji i stron pomocy dla interfejsów API sieci Web. Zapewnia takie korzyści, jak dokumentacja interaktywna, generowanie zestawu SDK klienta i możliwość odnajdywania interfejsów API.

W tym artykule opisano implementacje [Swashbuckle. AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) i [NSwag](https://github.com/RicoSuter/NSwag) .NET Swagger:

* **Swashbuckle. AspNetCore** to projekt Open Source służący do generowania dokumentów struktury Swagger dla ASP.NET Core interfejsów API sieci Web.

* **NSwag** jest innym projektem Open Source na potrzeby generowania dokumentów struktury Swagger i INTEGROWANIA [interfejsu użytkownika struktury Swagger](https://swagger.io/swagger-ui/) lub [ReDoc](https://github.com/Rebilly/ReDoc) do ASP.NET Core interfejsów API sieci Web. Ponadto NSwag oferuje podejścia do generowania kodu klienta C# i języka TypeScript dla interfejsu API.

## <a name="what-is-swagger--openapi"></a>Co to jest Swagger/OpenAPI?

Swagger to specyfikacja języka niezależny od do opisywania interfejsów API [rest](https://en.wikipedia.org/wiki/Representational_state_transfer) . Projekt Swagger został przekazano do [inicjatywy openapi](https://www.openapis.org/), w której jest teraz określany jako openapi. Obie nazwy są używane zamiennie; OpenAPI jest jednak preferowane. Pozwala to komputerom i ludziom na zrozumienie możliwości usługi bez bezpośredniego dostępu do implementacji (kod źródłowy, dostęp do sieci, dokumentacja). Jednym z celów jest zminimalizowanie ilości pracy wymaganej do nawiązania połączenia z nieskojarzonymi usługami. Innym celem jest skrócenie czasu wymaganego do dokładnego udokumentowania usługi.

## <a name="swagger-specification-swaggerjson"></a>Specyfikacja struktury Swagger (Swagger. JSON)

Rdzeń do przepływu struktury Swagger jest domyślnie specyfikacją&mdash;Swagger dokumentu o nazwie *Swagger. JSON*. Jest on generowany przez łańcuch narzędzi programu Swagger (lub implementacje innych firm) na podstawie Twojej usługi. Opisuje możliwości interfejsu API i sposób uzyskiwania dostępu do niego przy użyciu protokołu HTTP. Służy on do tworzenia interfejsu użytkownika struktury Swagger i jest używany przez łańcuch narzędzi do włączania odnajdywania i generowania kodu klienta. Oto przykład specyfikacji struktury Swagger zredukowany dla zwięzłości:

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

## <a name="swagger-ui"></a>Interfejs użytkownika struktury Swagger

[Interfejs użytkownika struktury Swagger](https://swagger.io/swagger-ui/) oferuje interfejs użytkownika oparty na sieci Web, który zawiera informacje o usłudze, przy użyciu wygenerowanej specyfikacji struktury Swagger. Zarówno Swashbuckle, jak i NSwag zawierają osadzoną wersję interfejsu użytkownika struktury Swagger, dzięki czemu mogą być hostowane w aplikacji ASP.NET Core przy użyciu wywołania rejestracji oprogramowania pośredniczącego. Interfejs użytkownika sieci Web wygląda następująco:

![Interfejs użytkownika struktury Swagger](web-api-help-pages-using-swagger/_static/swagger-ui.png)

Każda publiczna Metoda działania w kontrolerach może być testowana z poziomu interfejsu użytkownika. Kliknij nazwę metody, aby rozwinąć sekcję. Dodaj wszelkie niezbędne parametry i kliknij przycisk **Wypróbuj!**.

![Przykład pobierania testów struktury Swagger](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> Wersja interfejsu użytkownika programu Swagger używana na potrzeby zrzutów ekranu jest w wersji 2. Aby zapoznać się z wersją 3 przykład, zobacz [przykład petstore](https://petstore.swagger.io/).

## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do pakietu Swashbuckle](xref:tutorials/get-started-with-swashbuckle)
* [Wprowadzenie do łańcucha narzędzi NSwag](xref:tutorials/get-started-with-nswag)
