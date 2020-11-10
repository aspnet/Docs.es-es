---
title: Documentación de la API web de ASP.NET Core con Swagger/OpenAPI
author: RicoSuter
description: En este tutorial se proporciona una guía sobre cómo incorporar Swagger para generar documentación y páginas de ayuda para una aplicación de API web.
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
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93062459"
---
# <a name="aspnet-core-web-api-documentation-with-swagger--openapi"></a>Documentación de la API web de ASP.NET Core con Swagger/OpenAPI

Por [Christoph Nienaber](https://twitter.com/zuckerthoben) y [Rico Suter](https://blog.rsuter.com/)

Swagger (OpenAPI) es una especificación independiente del lenguaje que sirve para describir API REST. Permite a los equipos y a los usuarios comprender las capacidades de una API REST sin acceso directo al código fuente. Sus principales objetivos son los siguientes:

* Minimizar la cantidad de trabajo necesaria para conectar los servicios desacoplados.
* Reducir la cantidad de tiempo necesario para documentar un servicio con precisión.

Las dos implementaciones principales de OpenAPI para .NET son [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) y [NSwag](https://github.com/RicoSuter/NSwag), consulte:

* [Introducción a Swashbuckle](xref:tutorials/get-started-with-swashbuckle)
* [Introducción a NSwag](xref:tutorials/get-started-with-nswag)

## <a name="openapi-vs-swagger"></a>OpenApi y Swagger

El proyecto de Swagger se donó a la iniciativa OpenAPI en 2015 y desde entonces se conoce como OpenAPI. Ambos nombres se usan indistintamente. Sin embargo, "OpenAPI" hace referencia a la especificación. "Swagger" hace referencia a la familia de productos comerciales y de código abierto de Smartbear que funcionan con la especificación OpenAPI. Los siguientes productos de código abierto, como [OpenAPIGenerator](https://github.com/OpenAPITools/openapi-generator), también se encuentran bajo el nombre de la familia de Swagger, a pesar de no ser publicados por Smartbear.

En resumen:

* OpenAPI es una especificación.
* Swagger es una herramienta que usa la especificación OpenAPI. Por ejemplo, OpenAPIGenerator y SwaggerUI.

## <a name="openapi-specification-openapijson"></a>Especificación de OpenAPI (OpenAPI. JSON)

La especificación OpenAPI es un documento que describe las capacidades de la API. El documento se basa en las anotaciones de atributos y XML dentro de los controladores y modelos. Es la parte principal del flujo OpenAPI y se usa para impulsar herramientas como SwaggerUI. De forma predeterminada, se denomina *openapi.json*. Este es un ejemplo de especificación de OpenAPI (se ha reducido por motivos de brevedad):

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

Todos los métodos de acción públicos aplicados a los controladores se pueden probar desde la interfaz de usuario. Seleccione un nombre de método para expandir la sección. Agregue todos los parámetros necesarios y seleccione **Try it out!** (¡Pruébelo!).

![Ejemplo de prueba de acción GET de Swagger](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> La versión de interfaz de usuario de Swagger usada para las capturas de pantalla es la versión 2. Para obtener un ejemplo de la versión 3, vea el [ejemplo de Petstore](https://petstore.swagger.io/).

## <a name="next-steps"></a>Pasos siguientes

* [Get started with Swashbuckle](xref:tutorials/get-started-with-swashbuckle) (Introducción a Swashbuckle)
* [Get started with NSwag](xref:tutorials/get-started-with-nswag) (Introducción a NSwag)
