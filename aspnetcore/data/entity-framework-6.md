---
title: ASP.NET Core y Entity Framework 6
author: rick-anderson
description: Entity Framework 6.3 y versiones posteriores funcionan con ASP.NET Core 3.1 y versiones posteriores.
ms.author: riande
ms.custom: mvc
ms.date: 7/14/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/entity-framework-6
ms.openlocfilehash: 64d420d7076f1da453ee423cc4a3732eeb47b221
ms.sourcegitcommit: 4df148cbbfae9ec8d377283ee71394944a284051
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/26/2020
ms.locfileid: "88876703"
---
# <a name="aspnet-core-and-entity-framework-6"></a>ASP.NET Core y Entity Framework 6
::: moniker range=">= aspnetcore-3.0"

Por [Patrick Goode](https://github.com/attrib75)

## <a name="using-entity-framework-6-with-aspnet-core"></a>Uso de Entity Framework 6 con ASP.NET Core

[Entity Framework Core](/ef/) se debe usar para desarrollos nuevo. En el [ejemplo para descargar](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/3.xsample) se usa [Entity Framework 6 (EF6)](/ef/ef6), que se puede utilizar para migrar aplicaciones existentes a ASP.NET Core.

## <a name="additional-resources"></a>Recursos adicionales

* [Entity Framework - Code-Based Configuration](/ef/ef6/fundamentals/configuring/code-based) (Entity Framework: configuración basada en código)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Por [Paweł Grudzień](https://github.com/pgrudzien12), [Damien Pontifex](https://github.com/DamienPontifex) y [Tom Dykstra](https://github.com/tdykstra)

En este artículo se muestra cómo usar Entity Framework 6 en una aplicación ASP.NET Core.    

## <a name="overview"></a>Información general 

Para usar Entity Framework 6, el proyecto se tiene que compilar con .NET Framework, dado que Entity Framework 6 no es compatible con .NET Core. Si necesita usar características multiplataforma, debe actualizar a [Entity Framework Core](/ef/).  

La manera recomendada de usar Entity Framework 6 en una aplicación ASP.NET Core es colocar el contexto y las clases de modelo de EF6 en un proyecto de biblioteca de clases cuyo destino sea .NET Framework. Agregue una referencia a la biblioteca de clases desde el proyecto de ASP.NET Core. Vea el ejemplo [Visual Studio solution with EF6 and ASP.NET Core projects](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/) (Solución de Visual Studio con proyectos de EF6 y ASP.NET Core).  

No se puede colocar un contexto de EF6 en un proyecto de ASP.NET Core porque los proyectos de .NET Core no admiten toda la funcionalidad que requieren los comandos de EF6 como *Enable-Migrations*.    

Independientemente del tipo de proyecto en el que localice el contexto de EF6, solo las herramientas de línea de comandos de EF6 funcionan con un contexto de EF6. Por ejemplo, `Scaffold-DbContext` solo está disponible en Entity Framework Core. Si necesita la utilización de técnicas de ingeniería inversa para una base de datos en un modelo de EF6, vea <https://docs.microsoft.com/ef/ef6/modeling/code-first/workflows/existing-database> (Code First para una base de datos existente).    

## <a name="reference-full-framework-and-ef6-in-the-aspnet-core-project"></a>Marco de referencia completo y EF6 en el proyecto de ASP.NET Core 

El proyecto de ASP.NET Core debe destinarse a .NET Framework y hacer referencia a EF6. Por ejemplo, el archivo *.csproj* del proyecto ASP.NET Core tendrá un aspecto similar al ejemplo siguiente (solo se muestran las partes relevantes del archivo).    

[!code-xml[](entity-framework-6/sample/MVCCore/MVCCore.csproj?range=3-9&highlight=2)]   

Al crear un proyecto, use la plantilla **Aplicación web ASP.NET Core (.NET Framework)** .    

## <a name="handle-connection-strings"></a>Controlar las cadenas de conexión    

Las herramientas de línea de comandos de EF6 que se van a usar en el proyecto de biblioteca de clases de EF6 requieren un constructor predeterminado para poder crear instancias del contexto. Pero, probablemente querrá especificar la cadena de conexión que se va a usar en el proyecto de ASP.NET Core, en cuyo caso el constructor de contexto debe tener un parámetro que permita pasar la cadena de conexión. Este es un ejemplo.   

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContext.cs?name=snippet_Constructor)]   

Como el contexto de EF6 no tiene un constructor sin parámetros, el proyecto de EF6 tiene que proporcionar una implementación de <https://docs.microsoft.com/dotnet/api/system.data.entity.infrastructure.idbcontextfactory-1?view=entity-framework-6.2.0>. Las herramientas de línea de comandos de EF6 buscarán y usarán esa implementación para poder crear instancias del contexto. Este es un ejemplo.   

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContextFactory.cs?name=snippet_IDbContextFactory)]  

En este ejemplo de código, la implementación de `IDbContextFactory` pasa una cadena de conexión codificada de forma rígida. Se trata de la cadena de conexión que van a usar las herramientas de línea de comandos. Querrá implementar una estrategia para asegurarse de que la biblioteca de clases usa la misma cadena de conexión que la aplicación que realiza la llamada. Por ejemplo, podría obtener el valor de una variable de entorno en los dos proyectos.   

## <a name="set-up-dependency-injection-in-the-aspnet-core-project"></a>Configurar la inserción de dependencias en el proyecto de ASP.NET Core  

En el archivo *Startup.cs* del proyecto de Core, establezca el contexto de EF6 para la inserción de dependencias (DI) en `ConfigureServices`. La duración de cada solicitud se debe configurar como ámbito de los objetos de contexto de EF.   

[!code-csharp[](entity-framework-6/sample/MVCCore/Startup.cs?name=snippet_ConfigureServices&highlight=5)]   

Después, puede obtener una instancia del contexto en los controladores mediante DI. El código es similar al que escribiría para un contexto de EF Core:    

[!code-csharp[](entity-framework-6/sample/MVCCore/Controllers/StudentsController.cs?name=snippet_ContextInController)]  

## <a name="sample-application"></a>Aplicación de ejemplo   

Para obtener una aplicación de ejemplo funcional, vea la [solución de Visual Studio de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/) que se incluye en este artículo.  

Este ejemplo se puede crear desde cero mediante los pasos siguientes en Visual Studio:    

* Cree una solución.    

* **Agregar** > **Nuevo proyecto** > **Web** > **Aplicación web ASP.NET Core**    
  * En el cuadro de diálogo de selección de la plantilla de proyecto, seleccione la API y .NET Framework en la lista desplegable. 

* **Agregar** > **Nuevo proyecto** > **Escritorio de Windows** > **Biblioteca de clases (.NET Framework)**  

* En la **Consola del Administrador de paquetes** (PMC) para ambos proyectos, ejecute el comando `Install-Package Entityframework`.    

* En el proyecto de biblioteca de clases, cree clases de modelo de datos, una clase de contexto y una implementación de `IDbContextFactory`.    

* En PMC para el proyecto de biblioteca de clases, ejecute los comandos `Enable-Migrations` y `Add-Migration Initial`. Si ha configurado el proyecto de ASP.NET Core como proyecto de inicio, agregue `-StartupProjectName EF6` a estos comandos. 

* En el proyecto de Core, agregue una referencia de proyecto al proyecto de biblioteca de clases.    

* En el proyecto de Core, en *Startup.cs*, registre el contexto para DI.    

* En el proyecto de Core, en *appsettings.json*, agregue la cadena de conexión.    

* En el proyecto de Core, agregue un controlador y vistas para comprobar que puede leer y escribir datos. (Tenga en cuenta que el scaffolding de ASP.NET Core MVC no funcionará con el contexto de EF6 al que se hace referencia desde la biblioteca de clases).

::: moniker-end
