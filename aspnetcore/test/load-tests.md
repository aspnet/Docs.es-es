---
title: Pruebas de carga y de esfuerzo de ASP.NET Core
author: Jeremy-Meng
description: Obtenga información sobre varias destacables herramientas y enfoques para realizar pruebas de carga y de esfuerzo en aplicaciones ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: test/loadtests
ms.openlocfilehash: 5df2dd906d52aaec4fc13b07f3d92c87c802f37f
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406516"
---
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="f187c-103">Pruebas de carga y de esfuerzo de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f187c-103">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="f187c-104">Las pruebas de carga y las de esfuerzo son importantes para garantizar que una aplicación web es eficaz y escalable.</span><span class="sxs-lookup"><span data-stu-id="f187c-104">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="f187c-105">Sus objetivos son diferentes, aunque con frecuencia comparten pruebas similares.</span><span class="sxs-lookup"><span data-stu-id="f187c-105">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="f187c-106">**Pruebas de carga**: prueban si la aplicación puede controlar una determinada carga de usuarios para un escenario concreto y, a la vez, satisfacer el objetivo de respuesta.</span><span class="sxs-lookup"><span data-stu-id="f187c-106">**Load tests**: Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="f187c-107">La aplicación se ejecuta en condiciones normales.</span><span class="sxs-lookup"><span data-stu-id="f187c-107">The app is run under normal conditions.</span></span>

<span data-ttu-id="f187c-108">**Pruebas de esfuerzo**: prueban la estabilidad de la aplicación al ejecutarse en condiciones extremas, a menudo durante un largo período de tiempo.</span><span class="sxs-lookup"><span data-stu-id="f187c-108">**Stress tests**: Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="f187c-109">Las pruebas colocan en la aplicación una gran carga de usuarios, ya sea picos o una carga que aumenta gradualmente, o limitan los recursos informáticos de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="f187c-109">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="f187c-110">Las pruebas de esfuerzo determinan si una aplicación sometida a un esfuerzo puede recuperarse de un error y volver correctamente al comportamiento esperado.</span><span class="sxs-lookup"><span data-stu-id="f187c-110">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="f187c-111">Bajo esfuerzo, la aplicación no se ejecuta en condiciones normales.</span><span class="sxs-lookup"><span data-stu-id="f187c-111">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="f187c-112">Visual Studio 2019 ha anunciado planes para [dejar de usar pruebas de carga](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).</span><span class="sxs-lookup"><span data-stu-id="f187c-112">Visual Studio 2019 announced plans to [deprecate the load testing](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).</span></span> <span data-ttu-id="f187c-113">El servicio de pruebas de carga basadas en la nube de Azure DevOps correspondiente se ha cerrado.</span><span class="sxs-lookup"><span data-stu-id="f187c-113">The corresponding Azure DevOps cloud-based load testing service has been closed.</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="f187c-114">Herramientas de terceros</span><span class="sxs-lookup"><span data-stu-id="f187c-114">Third-party tools</span></span>

<span data-ttu-id="f187c-115">La lista siguiente contiene herramientas de rendimiento web de terceros con diversos conjuntos de características:</span><span class="sxs-lookup"><span data-stu-id="f187c-115">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="f187c-116">Apache JMeter</span><span class="sxs-lookup"><span data-stu-id="f187c-116">Apache JMeter</span></span>](https://jmeter.apache.org/)
* [<span data-ttu-id="f187c-117">ApacheBench (ab)</span><span class="sxs-lookup"><span data-stu-id="f187c-117">ApacheBench (ab)</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [<span data-ttu-id="f187c-118">Gatling</span><span class="sxs-lookup"><span data-stu-id="f187c-118">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="f187c-119">k6</span><span class="sxs-lookup"><span data-stu-id="f187c-119">k6</span></span>](https://k6.io)
* [<span data-ttu-id="f187c-120">Locust</span><span class="sxs-lookup"><span data-stu-id="f187c-120">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="f187c-121">West Wind WebSurge</span><span class="sxs-lookup"><span data-stu-id="f187c-121">West Wind WebSurge</span></span>](https://websurge.west-wind.com/)
* [<span data-ttu-id="f187c-122">Netling</span><span class="sxs-lookup"><span data-stu-id="f187c-122">Netling</span></span>](https://github.com/hallatore/Netling)
* [<span data-ttu-id="f187c-123">Vegeta</span><span class="sxs-lookup"><span data-stu-id="f187c-123">Vegeta</span></span>](https://github.com/tsenart/vegeta)