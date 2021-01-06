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
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "93062459"
---
# <a name="aspnet-core-web-api-documentation-with-swagger--openapi"></a><span data-ttu-id="ea35c-103">Documentación de la API web de ASP.NET Core con Swagger/OpenAPI</span><span class="sxs-lookup"><span data-stu-id="ea35c-103">ASP.NET Core web API documentation with Swagger / OpenAPI</span></span>

<span data-ttu-id="ea35c-104">Por [Christoph Nienaber](https://twitter.com/zuckerthoben) y [Rico Suter](https://blog.rsuter.com/)</span><span class="sxs-lookup"><span data-stu-id="ea35c-104">By [Christoph Nienaber](https://twitter.com/zuckerthoben) and [Rico Suter](https://blog.rsuter.com/)</span></span>

<span data-ttu-id="ea35c-105">Swagger (OpenAPI) es una especificación independiente del lenguaje que sirve para describir API REST.</span><span class="sxs-lookup"><span data-stu-id="ea35c-105">Swagger (OpenAPI) is a language-agnostic specification for describing REST APIs.</span></span> <span data-ttu-id="ea35c-106">Permite a los equipos y a los usuarios comprender las capacidades de una API REST sin acceso directo al código fuente.</span><span class="sxs-lookup"><span data-stu-id="ea35c-106">It allows both computers and humans to understand the capabilities of a REST API without direct access to the source code.</span></span> <span data-ttu-id="ea35c-107">Sus principales objetivos son los siguientes:</span><span class="sxs-lookup"><span data-stu-id="ea35c-107">Its main goals are to:</span></span>

* <span data-ttu-id="ea35c-108">Minimizar la cantidad de trabajo necesaria para conectar los servicios desacoplados.</span><span class="sxs-lookup"><span data-stu-id="ea35c-108">Minimize the amount of work needed to connect decoupled services.</span></span>
* <span data-ttu-id="ea35c-109">Reducir la cantidad de tiempo necesario para documentar un servicio con precisión.</span><span class="sxs-lookup"><span data-stu-id="ea35c-109">Reduce the amount of time needed to accurately document a service.</span></span>

<span data-ttu-id="ea35c-110">Las dos implementaciones principales de OpenAPI para .NET son [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) y [NSwag](https://github.com/RicoSuter/NSwag), consulte:</span><span class="sxs-lookup"><span data-stu-id="ea35c-110">The two main OpenAPI implementations for .NET are [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) and [NSwag](https://github.com/RicoSuter/NSwag), see:</span></span>

* [<span data-ttu-id="ea35c-111">Introducción a Swashbuckle</span><span class="sxs-lookup"><span data-stu-id="ea35c-111">Getting Started with Swashbuckle</span></span>](xref:tutorials/get-started-with-swashbuckle)
* [<span data-ttu-id="ea35c-112">Introducción a NSwag</span><span class="sxs-lookup"><span data-stu-id="ea35c-112">Getting Started with NSwag</span></span>](xref:tutorials/get-started-with-nswag)

## <a name="openapi-vs-swagger"></a><span data-ttu-id="ea35c-113">OpenApi y Swagger</span><span class="sxs-lookup"><span data-stu-id="ea35c-113">OpenApi vs. Swagger</span></span>

<span data-ttu-id="ea35c-114">El proyecto de Swagger se donó a la iniciativa OpenAPI en 2015 y desde entonces se conoce como OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="ea35c-114">The Swagger project was donated to the OpenAPI Initiative in 2015 and has since been referred to as OpenAPI.</span></span> <span data-ttu-id="ea35c-115">Ambos nombres se usan indistintamente.</span><span class="sxs-lookup"><span data-stu-id="ea35c-115">Both names are used interchangeably.</span></span> <span data-ttu-id="ea35c-116">Sin embargo, "OpenAPI" hace referencia a la especificación.</span><span class="sxs-lookup"><span data-stu-id="ea35c-116">However, "OpenAPI" refers to the specification.</span></span> <span data-ttu-id="ea35c-117">"Swagger" hace referencia a la familia de productos comerciales y de código abierto de Smartbear que funcionan con la especificación OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="ea35c-117">"Swagger" refers to the family of open-source and commercial products from SmartBear that work with the OpenAPI Specification.</span></span> <span data-ttu-id="ea35c-118">Los siguientes productos de código abierto, como [OpenAPIGenerator](https://github.com/OpenAPITools/openapi-generator), también se encuentran bajo el nombre de la familia de Swagger, a pesar de no ser publicados por Smartbear.</span><span class="sxs-lookup"><span data-stu-id="ea35c-118">Subsequent open-source products, such as [OpenAPIGenerator](https://github.com/OpenAPITools/openapi-generator), also fall under the Swagger family name, despite not being released by SmartBear.</span></span>

<span data-ttu-id="ea35c-119">En resumen:</span><span class="sxs-lookup"><span data-stu-id="ea35c-119">In short:</span></span>

* <span data-ttu-id="ea35c-120">OpenAPI es una especificación.</span><span class="sxs-lookup"><span data-stu-id="ea35c-120">OpenAPI is a specification.</span></span>
* <span data-ttu-id="ea35c-121">Swagger es una herramienta que usa la especificación OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="ea35c-121">Swagger is tooling that uses the OpenAPI specification.</span></span> <span data-ttu-id="ea35c-122">Por ejemplo, OpenAPIGenerator y SwaggerUI.</span><span class="sxs-lookup"><span data-stu-id="ea35c-122">For example, OpenAPIGenerator and SwaggerUI.</span></span>

## <a name="openapi-specification-openapijson"></a><span data-ttu-id="ea35c-123">Especificación de OpenAPI (OpenAPI. JSON)</span><span class="sxs-lookup"><span data-stu-id="ea35c-123">OpenAPI specification (openapi.json)</span></span>

<span data-ttu-id="ea35c-124">La especificación OpenAPI es un documento que describe las capacidades de la API.</span><span class="sxs-lookup"><span data-stu-id="ea35c-124">The OpenAPI specification is a document that describes the capabilities of your API.</span></span> <span data-ttu-id="ea35c-125">El documento se basa en las anotaciones de atributos y XML dentro de los controladores y modelos.</span><span class="sxs-lookup"><span data-stu-id="ea35c-125">The document is based on the XML and attribute annotations within the controllers and models.</span></span> <span data-ttu-id="ea35c-126">Es la parte principal del flujo OpenAPI y se usa para impulsar herramientas como SwaggerUI.</span><span class="sxs-lookup"><span data-stu-id="ea35c-126">It's the core part of the OpenAPI flow and is used to drive tooling such as SwaggerUI.</span></span> <span data-ttu-id="ea35c-127">De forma predeterminada, se denomina *openapi.json*.</span><span class="sxs-lookup"><span data-stu-id="ea35c-127">By default, it's named *openapi.json*.</span></span> <span data-ttu-id="ea35c-128">Este es un ejemplo de especificación de OpenAPI (se ha reducido por motivos de brevedad):</span><span class="sxs-lookup"><span data-stu-id="ea35c-128">Here's an example of an OpenAPI specification, reduced for brevity:</span></span>

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

## <a name="swagger-ui"></a><span data-ttu-id="ea35c-129">Interfaz de usuario de Swagger</span><span class="sxs-lookup"><span data-stu-id="ea35c-129">Swagger UI</span></span>

<span data-ttu-id="ea35c-130">La [interfaz de usuario de OpenAPI](https://swagger.io/swagger-ui/) es una interfaz de usuario basada en Internet que proporciona información sobre el servicio por medio de la especificación de Swagger generada.</span><span class="sxs-lookup"><span data-stu-id="ea35c-130">[Swagger UI](https://swagger.io/swagger-ui/) offers a web-based UI that provides information about the service, using the generated OpenAPI specification.</span></span> <span data-ttu-id="ea35c-131">Swashbuckle y NSwag incluyen una versión insertada de la interfaz de usuario de Swagger, de modo que se puede hospedar en una aplicación ASP.NET Core realizando una llamada de registro de middleware.</span><span class="sxs-lookup"><span data-stu-id="ea35c-131">Both Swashbuckle and NSwag include an embedded version of Swagger UI, so that it can be hosted in your ASP.NET Core app using a middleware registration call.</span></span> <span data-ttu-id="ea35c-132">La interfaz de usuario web tiene este aspecto:</span><span class="sxs-lookup"><span data-stu-id="ea35c-132">The web UI looks like this:</span></span>

![Interfaz de usuario de Swagger](web-api-help-pages-using-swagger/_static/swagger-ui.png)

<span data-ttu-id="ea35c-134">Todos los métodos de acción públicos aplicados a los controladores se pueden probar desde la interfaz de usuario.</span><span class="sxs-lookup"><span data-stu-id="ea35c-134">Each public action method in your controllers can be tested from the UI.</span></span> <span data-ttu-id="ea35c-135">Seleccione un nombre de método para expandir la sección.</span><span class="sxs-lookup"><span data-stu-id="ea35c-135">Select a method name to expand the section.</span></span> <span data-ttu-id="ea35c-136">Agregue todos los parámetros necesarios y seleccione **Try it out!** (¡Pruébelo!).</span><span class="sxs-lookup"><span data-stu-id="ea35c-136">Add any necessary parameters, and select **Try it out!**.</span></span>

![Ejemplo de prueba de acción GET de Swagger](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

> [!NOTE]
> <span data-ttu-id="ea35c-138">La versión de interfaz de usuario de Swagger usada para las capturas de pantalla es la versión 2.</span><span class="sxs-lookup"><span data-stu-id="ea35c-138">The Swagger UI version used for the screenshots is version 2.</span></span> <span data-ttu-id="ea35c-139">Para obtener un ejemplo de la versión 3, vea el [ejemplo de Petstore](https://petstore.swagger.io/).</span><span class="sxs-lookup"><span data-stu-id="ea35c-139">For a version 3 example, see [Petstore example](https://petstore.swagger.io/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="ea35c-140">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="ea35c-140">Next steps</span></span>

* <span data-ttu-id="ea35c-141">[Get started with Swashbuckle](xref:tutorials/get-started-with-swashbuckle) (Introducción a Swashbuckle)</span><span class="sxs-lookup"><span data-stu-id="ea35c-141">[Get started with Swashbuckle](xref:tutorials/get-started-with-swashbuckle)</span></span>
* <span data-ttu-id="ea35c-142">[Get started with NSwag](xref:tutorials/get-started-with-nswag) (Introducción a NSwag)</span><span class="sxs-lookup"><span data-stu-id="ea35c-142">[Get started with NSwag](xref:tutorials/get-started-with-nswag)</span></span>
