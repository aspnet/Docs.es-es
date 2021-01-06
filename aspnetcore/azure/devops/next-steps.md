---
title: 'Pasos siguientes: DevOps con ASP.NET Core y Azure'
author: CamSoper
description: Rutas de aprendizaje adicionales para DevOps con ASP.NET Core y Azure.
ms.author: casoper
ms.custom: devx-track-csharp, mvc, seodec18
ms.date: 10/24/2018
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
uid: azure/devops/next-steps
ms.openlocfilehash: 35b0486611cc15f25b1c8b54584c264e4c1298c9
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2021
ms.locfileid: "93056600"
---
# <a name="next-steps"></a><span data-ttu-id="de96c-103">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="de96c-103">Next steps</span></span>

<span data-ttu-id="de96c-104">En esta guía, se ha creado una canalización de DevOps para una aplicación ASP.NET Core de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="de96c-104">In this guide, you created a DevOps pipeline for an ASP.NET Core sample app.</span></span> <span data-ttu-id="de96c-105">¡Enhorabuena!</span><span class="sxs-lookup"><span data-stu-id="de96c-105">Congratulations!</span></span> <span data-ttu-id="de96c-106">Esperamos que haya disfrutado de la información sobre cómo publicar aplicaciones web de ASP.NET Core en Azure App Service y automatizar la integración continua de los cambios.</span><span class="sxs-lookup"><span data-stu-id="de96c-106">We hope you enjoyed learning to publish ASP.NET Core web apps to Azure App Service and automate the continuous integration of changes.</span></span>

<span data-ttu-id="de96c-107">Además del hospedaje web y DevOps, Azure cuenta con una amplia variedad de servicios de plataforma como servicio (PaaS) útiles para los desarrolladores de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="de96c-107">Beyond web hosting and DevOps, Azure has a wide array of Platform-as-a-Service (PaaS) services useful to ASP.NET Core developers.</span></span> <span data-ttu-id="de96c-108">En esta sección se proporciona una breve descripción de algunos de los servicios que se usan con más frecuencia.</span><span class="sxs-lookup"><span data-stu-id="de96c-108">This section gives a brief overview of some of the most commonly used services.</span></span>

## <a name="storage-and-databases"></a><span data-ttu-id="de96c-109">Almacenamiento y bases de datos</span><span class="sxs-lookup"><span data-stu-id="de96c-109">Storage and databases</span></span>

<span data-ttu-id="de96c-110">[Redis Cache](/azure/redis-cache/) es el almacenamiento en caché de datos de baja latencia y alto rendimiento disponible como servicio.</span><span class="sxs-lookup"><span data-stu-id="de96c-110">[Redis Cache](/azure/redis-cache/) is high-throughput, low-latency data caching available as a service.</span></span> <span data-ttu-id="de96c-111">Se puede usar para almacenar en caché los resultados de la página, reducir las solicitudes de base de datos y proporcionar estado de sesión de ASP.NET Core en varias instancias de una aplicación.</span><span class="sxs-lookup"><span data-stu-id="de96c-111">It can be used for caching page output, reducing database requests, and providing ASP.NET Core session state across multiple instances of an app.</span></span>

<span data-ttu-id="de96c-112">[Azure Storage](/azure/storage/) es el almacenamiento en la nube escalable de forma masiva de Azure.</span><span class="sxs-lookup"><span data-stu-id="de96c-112">[Azure Storage](/azure/storage/) is Azure's massively scalable cloud storage.</span></span> <span data-ttu-id="de96c-113">Los desarrolladores pueden beneficiarse de [Queue Storage](/azure/storage/queues/storage-queues-introduction) para una puesta en cola fiable de mensajes, mientras que [Table Storage](/azure/storage/tables/table-storage-overview) se trata de un almacén de pares de clave-valor de NoSQL diseñado para un desarrollo rápido mediante el uso de conjuntos de datos semiestructurados masivos.</span><span class="sxs-lookup"><span data-stu-id="de96c-113">Developers can take advantage of [Queue Storage](/azure/storage/queues/storage-queues-introduction) for reliable message queuing, and [Table Storage](/azure/storage/tables/table-storage-overview) is a NoSQL key-value store designed for rapid development using massive, semi-structured data sets.</span></span>

<span data-ttu-id="de96c-114">[Azure SQL Database](/azure/sql-database/) proporciona capacidad de base de datos relacional como servicio mediante el motor de Microsoft SQL Server.</span><span class="sxs-lookup"><span data-stu-id="de96c-114">[Azure SQL Database](/azure/sql-database/) provides familiar relational database functionality as a service using the Microsoft SQL Server Engine.</span></span>

<span data-ttu-id="de96c-115">[Cosmos DB](/azure/cosmos-db/) es un servicio de base de datos NoSQL multimodelo distribuido globalmente.</span><span class="sxs-lookup"><span data-stu-id="de96c-115">[Cosmos DB](/azure/cosmos-db/) globally distributed, multi-model NoSQL database service.</span></span> <span data-ttu-id="de96c-116">Hay disponibles varias API, entre las que se incluyen la API de SQL (anteriormente denominada DocumentDB), Cassandra y MongoDB.</span><span class="sxs-lookup"><span data-stu-id="de96c-116">Multiple APIs are available, including SQL API (formerly called DocumentDB), Cassandra, and MongoDB.</span></span>

## Identity

<span data-ttu-id="de96c-117">[Azure Active Directory](/azure/active-directory/) y [Azure Active Directory B2C](/azure/active-directory-b2c/) son servicios de identidad.</span><span class="sxs-lookup"><span data-stu-id="de96c-117">[Azure Active Directory](/azure/active-directory/) and [Azure Active Directory B2C](/azure/active-directory-b2c/) are both identity services.</span></span> <span data-ttu-id="de96c-118">Azure Active Directory está diseñado para escenarios empresariales y permite colaboración Azure AD B2B (de negocio a negocio), mientras que la finalidad de Azure Active Directory B2C son los escenarios de negocio a cliente, incluida la información de inicio de sesión de redes sociales.</span><span class="sxs-lookup"><span data-stu-id="de96c-118">Azure Active Directory is designed for enterprise scenarios and enables Azure AD B2B (business-to-business) collaboration, while Azure Active Directory B2C is intended business-to-customer scenarios, including social network sign-in.</span></span>

## <a name="mobile"></a><span data-ttu-id="de96c-119">Móvil</span><span class="sxs-lookup"><span data-stu-id="de96c-119">Mobile</span></span>

<span data-ttu-id="de96c-120">[Notification Hubs](/azure/notification-hubs/) es un motor de inserción de notificaciones multiplataforma y escalable para enviar rápidamente millones de mensajes a aplicaciones que se ejecutan en varios tipos de dispositivos.</span><span class="sxs-lookup"><span data-stu-id="de96c-120">[Notification Hubs](/azure/notification-hubs/) is a multi-platform, scalable push-notification engine to quickly send millions of messages to apps running on various types of devices.</span></span>

## <a name="web-infrastructure"></a><span data-ttu-id="de96c-121">Infraestructura web</span><span class="sxs-lookup"><span data-stu-id="de96c-121">Web infrastructure</span></span>

<span data-ttu-id="de96c-122">[Azure Container Service](/azure/aks/) permite administrar el entorno hospedado de Kubernetes, lo que hace que sea fácil y rápido implementar y administrar aplicaciones en contenedores sin necesidad de tener conocimientos de orquestación de contenedores.</span><span class="sxs-lookup"><span data-stu-id="de96c-122">[Azure Container Service](/azure/aks/) manages your hosted Kubernetes environment, making it quick and easy to deploy and manage containerized apps without container orchestration expertise.</span></span>

<span data-ttu-id="de96c-123">[Azure Search](/azure/search/) se usa para crear una solución de búsqueda empresarial sobre contenido privado y heterogéneo.</span><span class="sxs-lookup"><span data-stu-id="de96c-123">[Azure Search](/azure/search/) is used to create an enterprise search solution over private, heterogenous content.</span></span>

<span data-ttu-id="de96c-124">[Service Fabric](/azure/service-fabric/) es una plataforma de sistemas distribuidos que facilita el empaquetado, la implementación y la administración de microservicios y contenedores escalables y fiables.</span><span class="sxs-lookup"><span data-stu-id="de96c-124">[Service Fabric](/azure/service-fabric/) is a distributed systems platform that makes it easy to package, deploy, and manage scalable and reliable microservices and containers.</span></span>
