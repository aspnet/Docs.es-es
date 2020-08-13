---
title: Páginas de ayuda de ASP.NET Core Web API con Swagger/Open API
author: RicoSuter
description: En este tutorial se proporciona una guía sobre cómo incorporar Swagger para generar documentación y páginas de ayuda para una aplicación de API web.
ms.author: scaddie
ms.custom: mvc
ms.date: 07/06/2020
no-loc:
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
ms.openlocfilehash: a3b8efcdb1f196fc878e0ad29580d305bb24f50a
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021983"
---
# <a name="aspnet-core-web-api-help-pages-with-swagger--openapi"></a>Páginas de ayuda de ASP.NET Core Web API con Swagger/Open API

Por [Christoph Nienaber](https://twitter.com/zuckerthoben) y [Rico Suter](https://blog.rsuter.com/)

Cuando un desarrollador usa una API web, puede que le resulte complicado comprender sus diversos métodos. [Swagger](https://swagger.io/), también conocido como [OpenAPI](https://www.openapis.org/), resuelve el problema de generar páginas útiles de ayuda y documentación relativas a las API web. Así, reporta ventajas como una documentación interactiva, la generación de SDK de cliente y la detectabilidad de API.

En este artículo, nos centraremos en las implementaciones de Swagger .NET [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) y [NSwag](https://github.com/RicoSuter/NSwag):

* **Swashbuckle.AspNetCore** es un proyecto de código abierto para generar documentos de Swagger para las API web de ASP.NET Core.

* **NSwag** es otro proyecto de código abierto para generar documentos de Swagger y que sirve para integrar la [interfaz de usuario de Swagger](https://swagger.io/swagger-ui/) o [ReDoc](https://github.com/Rebilly/ReDoc) en las API web de ASP.NET Core. De manera opcional, NSwag ofrece métodos para generar código de cliente de C# y TypeScript para la API.

## <a name="what-is-swagger--openapi"></a>¿Qué es Swagger/OpenAPI?

Swagger es una especificación independiente del lenguaje que sirve para describir API de [REST](https://en.wikipedia.org/wiki/Representational_state_transfer). El proyecto de Swagger se donó a la [iniciativa OpenAPI](https://www.openapis.org/), donde ahora se conoce como OpenAPI. Ambos nombres se usan indistintamente, aunque se prefiere OpenAPI. Gracias a esta API, tanto los equipos como los personas podrán conocer las funciones de un servicio sin necesidad de obtener acceso directo a la implementación (código fuente, acceso a la red, documentación). Uno de los objetivos consiste en reducir al mínimo la cantidad de trabajo necesario para conectar servicios que no están asociados. Otro es reducir la cantidad de tiempo necesario para documentar un servicio con precisión.

## <a name="openapi-specification-openapijson"></a>Especificación de OpenAPI (OpenAPI. JSON)

El elemento principal del flujo de OpenAPI es la especificación &mdash;, que es de forma predeterminada un documento llamado *openapi.json*. Lo genera la cadena de herramientas de OpenAPI (o las implementaciones de terceros de dicha cadena) en función de su servicio. Describe las funciones de su API y cómo tener acceso a ella con HTTP. Esta especificación también controla la interfaz de usuario de Swagger, y la cadena de herramientas la usa para permitir la detección y generación de código de cliente. Este es un ejemplo de especificación de OpenAPI (se ha reducido por motivos de brevedad):

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

## <a name="swagger-ui"></a>Interfaz de usuario de Swagger

La [interfaz de usuario de OpenAPI](https://swagger.io/swagger-ui/) es una interfaz de usuario basada en Internet que proporciona información sobre el servicio por medio de la especificación de Swagger generada. Swashbuckle y NSwag incluyen una versión insertada de la interfaz de usuario de Swagger, de modo que se puede hospedar en una aplicación ASP.NET Core realizando una llamada de registro de middleware. La interfaz de usuario web tiene este aspecto:

![Interfaz de usuario de Swagger](web-api-help-pages-using-swagger/_static/swagger-ui.png)

Todos los métodos de acción públicos aplicados a los controladores se pueden probar desde la interfaz de usuario. Haga clic en un nombre de método para expandir la sección. Agregue todos los parámetros necesarios y haga clic en **Try it out!** (¡Pruébelo!).

![Ejemplo de prueba de acción GET de Swagger](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> La versión de interfaz de usuario de Swagger usada para las capturas de pantalla es la versión 2. Para obtener un ejemplo de la versión 3, vea el [ejemplo de Petstore](https://petstore.swagger.io/).

## <a name="next-steps"></a>Pasos siguientes

* [Get started with Swashbuckle](xref:tutorials/get-started-with-swashbuckle) (Introducción a Swashbuckle)
* [Get started with NSwag](xref:tutorials/get-started-with-nswag) (Introducción a NSwag)
