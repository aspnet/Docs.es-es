---
title: Pruebas de carga y de esfuerzo de ASP.NET Core
author: Jeremy-Meng
description: Obtenga información sobre varias destacables herramientas y enfoques para realizar pruebas de carga y de esfuerzo en aplicaciones ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
no-loc:
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
uid: test/loadtests
ms.openlocfilehash: 8e3ca41312922cbf44361601c38e455b342e9fe1
ms.sourcegitcommit: b5ebaf42422205d212e3dade93fcefcf7f16db39
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2020
ms.locfileid: "88632816"
---
# <a name="aspnet-core-loadstress-testing"></a>Pruebas de carga y de esfuerzo de ASP.NET Core

Las pruebas de carga y las de esfuerzo son importantes para garantizar que una aplicación web es eficaz y escalable. Sus objetivos son diferentes, aunque con frecuencia comparten pruebas similares.

**Pruebas de carga**: prueban si la aplicación puede controlar una determinada carga de usuarios para un escenario concreto y, a la vez, satisfacer el objetivo de respuesta. La aplicación se ejecuta en condiciones normales.

**Pruebas de esfuerzo**: prueban la estabilidad de la aplicación al ejecutarse en condiciones extremas, a menudo durante un largo período de tiempo. Las pruebas colocan en la aplicación una gran carga de usuarios, ya sea picos o una carga que aumenta gradualmente, o limitan los recursos informáticos de la aplicación.

Las pruebas de esfuerzo determinan si una aplicación sometida a un esfuerzo puede recuperarse de un error y volver correctamente al comportamiento esperado. Bajo esfuerzo, la aplicación no se ejecuta en condiciones normales.

Visual Studio 2019 ha anunciado planes para [dejar de usar pruebas de carga](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/). El servicio de pruebas de carga basadas en la nube de Azure DevOps correspondiente se ha cerrado.

## <a name="third-party-tools"></a>Herramientas de terceros

La lista siguiente contiene herramientas de rendimiento web de terceros con diversos conjuntos de características:

* [Apache JMeter](https://jmeter.apache.org/)
* [ApacheBench (ab)](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [Gatling](https://gatling.io/)
* [k6](https://k6.io)
* [Locust](https://locust.io/)
* [West Wind WebSurge](https://websurge.west-wind.com/)
* [Netling](https://github.com/hallatore/Netling)
* [Vegeta](https://github.com/tsenart/vegeta)