---
title: Pruebas de carga y de esfuerzo de ASP.NET Core
author: Jeremy-Meng
description: Obtenga información sobre varias destacables herramientas y enfoques para realizar pruebas de carga y de esfuerzo en aplicaciones ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: test/loadtests
ms.openlocfilehash: 56f5a5caeea7581e26f8d8cec9662f439cd24b9e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060721"
---
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="d69b7-103">Pruebas de carga y de esfuerzo de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d69b7-103">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="d69b7-104">Las pruebas de carga y las de esfuerzo son importantes para garantizar que una aplicación web es eficaz y escalable.</span><span class="sxs-lookup"><span data-stu-id="d69b7-104">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="d69b7-105">Sus objetivos son diferentes, aunque con frecuencia comparten pruebas similares.</span><span class="sxs-lookup"><span data-stu-id="d69b7-105">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="d69b7-106">**Pruebas de carga** : prueban si la aplicación puede controlar una determinada carga de usuarios para un escenario concreto y, a la vez, satisfacer el objetivo de respuesta.</span><span class="sxs-lookup"><span data-stu-id="d69b7-106">**Load tests** : Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="d69b7-107">La aplicación se ejecuta en condiciones normales.</span><span class="sxs-lookup"><span data-stu-id="d69b7-107">The app is run under normal conditions.</span></span>

<span data-ttu-id="d69b7-108">**Pruebas de esfuerzo** : prueban la estabilidad de la aplicación al ejecutarse en condiciones extremas, a menudo durante un largo período de tiempo.</span><span class="sxs-lookup"><span data-stu-id="d69b7-108">**Stress tests** : Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="d69b7-109">Las pruebas colocan en la aplicación una gran carga de usuarios, ya sea picos o una carga que aumenta gradualmente, o limitan los recursos informáticos de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="d69b7-109">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="d69b7-110">Las pruebas de esfuerzo determinan si una aplicación sometida a un esfuerzo puede recuperarse de un error y volver correctamente al comportamiento esperado.</span><span class="sxs-lookup"><span data-stu-id="d69b7-110">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="d69b7-111">Bajo esfuerzo, la aplicación no se ejecuta en condiciones normales.</span><span class="sxs-lookup"><span data-stu-id="d69b7-111">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="d69b7-112">Visual Studio 2019 ha anunciado planes para [dejar de usar pruebas de carga](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).</span><span class="sxs-lookup"><span data-stu-id="d69b7-112">Visual Studio 2019 announced plans to [deprecate the load testing](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).</span></span> <span data-ttu-id="d69b7-113">El servicio de pruebas de carga basadas en la nube de Azure DevOps correspondiente se ha cerrado.</span><span class="sxs-lookup"><span data-stu-id="d69b7-113">The corresponding Azure DevOps cloud-based load testing service has been closed.</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="d69b7-114">Herramientas de terceros</span><span class="sxs-lookup"><span data-stu-id="d69b7-114">Third-party tools</span></span>

<span data-ttu-id="d69b7-115">La lista siguiente contiene herramientas de rendimiento web de terceros con diversos conjuntos de características:</span><span class="sxs-lookup"><span data-stu-id="d69b7-115">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="d69b7-116">Apache JMeter</span><span class="sxs-lookup"><span data-stu-id="d69b7-116">Apache JMeter</span></span>](https://jmeter.apache.org/)
* [<span data-ttu-id="d69b7-117">ApacheBench (ab)</span><span class="sxs-lookup"><span data-stu-id="d69b7-117">ApacheBench (ab)</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [<span data-ttu-id="d69b7-118">Gatling</span><span class="sxs-lookup"><span data-stu-id="d69b7-118">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="d69b7-119">k6</span><span class="sxs-lookup"><span data-stu-id="d69b7-119">k6</span></span>](https://k6.io)
* [<span data-ttu-id="d69b7-120">Locust</span><span class="sxs-lookup"><span data-stu-id="d69b7-120">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="d69b7-121">West Wind WebSurge</span><span class="sxs-lookup"><span data-stu-id="d69b7-121">West Wind WebSurge</span></span>](https://websurge.west-wind.com/)
* [<span data-ttu-id="d69b7-122">Netling</span><span class="sxs-lookup"><span data-stu-id="d69b7-122">Netling</span></span>](https://github.com/hallatore/Netling)
* [<span data-ttu-id="d69b7-123">Vegeta</span><span class="sxs-lookup"><span data-stu-id="d69b7-123">Vegeta</span></span>](https://github.com/tsenart/vegeta)