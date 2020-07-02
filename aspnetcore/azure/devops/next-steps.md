---
title: 'Pasos siguientes: DevOps con ASP.NET Core y Azure'
author: CamSoper
description: Rutas de aprendizaje adicionales para DevOps con ASP.NET Core y Azure.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: azure/devops/next-steps
ms.openlocfilehash: 6e7f46a0c78575e52a8182829f7b3d2bddec70f6
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85400341"
---
# <a name="next-steps"></a>Pasos siguientes

En esta guía, se ha creado una canalización de DevOps para una aplicación ASP.NET Core de ejemplo. ¡Enhorabuena! Esperamos que haya disfrutado de la información sobre cómo publicar aplicaciones web de ASP.NET Core en Azure App Service y automatizar la integración continua de los cambios.

Además del hospedaje web y DevOps, Azure cuenta con una amplia variedad de servicios de plataforma como servicio (PaaS) útiles para los desarrolladores de ASP.NET Core. En esta sección se proporciona una breve descripción de algunos de los servicios que se usan con más frecuencia.

## <a name="storage-and-databases"></a>Almacenamiento y bases de datos

[Redis Cache](/azure/redis-cache/) es el almacenamiento en caché de datos de baja latencia y alto rendimiento disponible como servicio. Se puede usar para almacenar en caché los resultados de la página, reducir las solicitudes de base de datos y proporcionar estado de sesión de ASP.NET Core en varias instancias de una aplicación.

[Azure Storage](/azure/storage/) es el almacenamiento en la nube escalable de forma masiva de Azure. Los desarrolladores pueden beneficiarse de [Queue Storage](/azure/storage/queues/storage-queues-introduction) para una puesta en cola fiable de mensajes, mientras que [Table Storage](/azure/storage/tables/table-storage-overview) se trata de un almacén de pares de clave-valor de NoSQL diseñado para un desarrollo rápido mediante el uso de conjuntos de datos semiestructurados masivos.

[Azure SQL Database](/azure/sql-database/) proporciona capacidad de base de datos relacional como servicio mediante el motor de Microsoft SQL Server.

[Cosmos DB](/azure/cosmos-db/) es un servicio de base de datos NoSQL multimodelo distribuido globalmente. Hay disponibles varias API, entre las que se incluyen la API de SQL (anteriormente denominada DocumentDB), Cassandra y MongoDB.

## Identity

[Azure Active Directory](/azure/active-directory/) y [Azure Active Directory B2C](/azure/active-directory-b2c/) son servicios de identidad. Azure Active Directory está diseñado para escenarios empresariales y permite colaboración Azure AD B2B (de negocio a negocio), mientras que la finalidad de Azure Active Directory B2C son los escenarios de negocio a cliente, incluida la información de inicio de sesión de redes sociales.

## <a name="mobile"></a>Móvil

[Notification Hubs](/azure/notification-hubs/) es un motor de inserción de notificaciones multiplataforma y escalable para enviar rápidamente millones de mensajes a aplicaciones que se ejecutan en varios tipos de dispositivos.

## <a name="web-infrastructure"></a>Infraestructura web

[Azure Container Service](/azure/aks/) permite administrar el entorno hospedado de Kubernetes, lo que hace que sea fácil y rápido implementar y administrar aplicaciones en contenedores sin necesidad de tener conocimientos de orquestación de contenedores.

[Azure Search](/azure/search/) se usa para crear una solución de búsqueda empresarial sobre contenido privado y heterogéneo.

[Service Fabric](/azure/service-fabric/) es una plataforma de sistemas distribuidos que facilita el empaquetado, la implementación y la administración de microservicios y contenedores escalables y fiables.
