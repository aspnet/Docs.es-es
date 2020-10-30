---
title: ASP.NET Core protección de datos
author: rick-anderson
description: Obtenga información sobre el concepto de protección de datos y los principios de diseño de las API de protección de datos de ASP.NET Core.
ms.author: riande
ms.custom: mvc
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
uid: security/data-protection/introduction
ms.openlocfilehash: 5fd5676b286e758f0648d78bf8cb4171e7a98f60
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051699"
---
# <a name="aspnet-core-data-protection"></a>ASP.NET Core protección de datos

Las aplicaciones web suelen necesitar almacenar datos confidenciales de seguridad. Windows proporciona DPAPI para las aplicaciones de escritorio, pero no es adecuado para las aplicaciones Web. La ASP.NET Core pila de protección de datos proporciona una API criptográfica sencilla y fácil de usar que un desarrollador puede usar para proteger los datos, incluida la administración y la rotación de claves.

La ASP.NET Core pila de protección de datos está diseñada para servir como el reemplazo a largo plazo para el &lt; &gt; elemento machineKey en ASP.net 1. x-4. x. Se diseñó para abordar muchas de las deficiencias de la pila criptográfica anterior y, al mismo tiempo, proporciona una solución integrada para la mayoría de los casos de uso que es probable que se encuentren las aplicaciones modernas.

## <a name="problem-statement"></a>Declaración del problema

La declaración del problema general se puede indicar sucintamente en una sola frase: necesito conservar información de confianza para su recuperación posterior, pero no confío en el mecanismo de persistencia. En términos Web, esto podría escribirse como "Necesito un estado de confianza de ida y vuelta a través de un cliente que no es de confianza".

El ejemplo canónico de esto es un token de autenticación cookie o portador. El servidor genera un token "soy Groot y tiene permisos XYZ" y lo entrega al cliente. En una fecha futura, el cliente presentará ese token de vuelta al servidor, pero el servidor necesita algún tipo de garantía de que el cliente no haya falsificado el token. Por lo tanto, el primer requisito: autenticidad (también conocido como integridad, prueba de alteración).

Puesto que el servidor confía en el estado persistente, se prevé que este estado puede contener información específica del entorno operativo. Puede ser una ruta de acceso de archivo, un permiso, un identificador u otra referencia indirecta, o algún otro tipo de datos específicos del servidor. Por lo general, esta información no se debe divulgar a un cliente que no sea de confianza. Por lo tanto, el segundo requisito: confidencialidad.

Por último, como los componentes de las aplicaciones modernas, lo que hemos visto es que los componentes individuales querrán aprovechar este sistema sin tener en cuenta otros componentes del sistema. Por ejemplo, si un componente de token de portador usa esta pila, debe funcionar sin interferencias de un mecanismo anti-CSRF que también podría estar usando la misma pila. Por lo tanto, el requisito final: aislamiento.

Podemos proporcionar más restricciones para limitar el ámbito de nuestros requisitos. Damos por hecho que todos los servicios que operan dentro de los Cryptosystem son de confianza y que no es necesario que los datos se generen o consuman fuera de los servicios bajo nuestro control directo. Además, es necesario que las operaciones sean lo más rápidas posible, ya que cada solicitud al servicio web puede pasar por el Cryptosystem una o varias veces. Esto hace que la criptografía simétrica sea ideal para nuestro escenario, y podemos usar la criptografía asimétrica hasta el momento en que sea necesario.

## <a name="design-philosophy"></a>Filosofía de diseño

Comenzamos por identificar problemas con la pila existente. Una vez hecho esto, se ha reparado el panorama de las soluciones existentes y se ha concluido que ninguna solución existente tiene las funcionalidades que buscamos. A continuación, hemos diseñado una solución basada en varios principios de GUID.

* El sistema debe ofrecer simplicidad de configuración. Idealmente, el sistema sería de configuración cero y los desarrolladores podían llegar al suelo. En situaciones en las que los desarrolladores necesitan configurar un aspecto específico (como el repositorio de claves), se debe tener en cuenta que las configuraciones específicas son sencillas.

* Ofrezca una API sencilla orientada al consumidor. Las API deben ser fáciles de usar correctamente y difíciles de usar de forma incorrecta.

* Los desarrolladores no deben aprender principios de administración de claves. El sistema debe controlar la selección del algoritmo y la duración de la clave en nombre del desarrollador. Idealmente, el desarrollador nunca debería tener acceso al material de clave sin procesar.

* Las claves deben protegerse en reposo siempre que sea posible. El sistema debe averiguar un mecanismo de protección predeterminado adecuado y aplicarlo automáticamente.

Teniendo en cuenta estos principios, desarrollamos una pila de protección de datos sencilla y [fácil de usar](xref:security/data-protection/using-data-protection) .

Las API de protección de datos de ASP.NET Core no están pensadas principalmente para la persistencia indefinida de cargas confidenciales. Otras tecnologías, como [Windows CNG DPAPI](/windows/win32/seccng/cng-dpapi) y [Azure Rights Management](/rights-management/) , son más adecuadas para el escenario de almacenamiento indefinido, y tienen una sólida funcionalidad de administración de claves. Dicho esto, no hay nada que prohíba a un desarrollador usar las API de protección de datos ASP.NET Core para la protección a largo plazo de datos confidenciales.

## <a name="audience"></a>Público

El sistema de protección de datos se divide en cinco paquetes principales. Varios aspectos de estas API se dirigen a tres audiencias principales;

1. La [información general sobre las API de consumidor](xref:security/data-protection/consumer-apis/overview) se dirige a los desarrolladores de aplicaciones y marcos.

   "No deseo obtener información sobre cómo funciona la pila o sobre cómo se configura. Simplemente deseo realizar alguna operación de la manera más sencilla posible con una alta probabilidad de usar las API correctamente ".

2. Las [API de configuración](xref:security/data-protection/configuration/overview) se dirigen a los desarrolladores de aplicaciones y administradores del sistema.

   "Necesito indicar al sistema de protección de datos que mi entorno requiere rutas de acceso o valores no predeterminados".

3. Las API de extensibilidad se dirigen a los desarrolladores a cargo de la implementación de una directiva personalizada. El uso de estas API se limitaría a situaciones poco frecuentes y a desarrolladores con reconocimiento de seguridad experimentados.

   "Necesito reemplazar un componente completo en el sistema porque tengo requisitos de comportamiento realmente únicos. Estoy dispuesto a aprender partes que no se usan de forma habitual de la superficie de la API para crear un complemento que satisfaga mis requisitos ".

## <a name="package-layout"></a>Diseño del paquete

La pila de protección de datos consta de cinco paquetes.

* [Microsoft. AspNetCore. nonprotection. Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Abstractions/) contiene las <xref:Microsoft.AspNetCore.DataProtection.IDataProtectionProvider> <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> interfaces y para crear servicios de protección de datos. También contiene métodos de extensión útiles para trabajar con estos tipos (por ejemplo, [IDataProtector. Protect](xref:Microsoft.AspNetCore.DataProtection.DataProtectionCommonExtensions.Protect*)). Si se crea una instancia del sistema de protección de datos en otra parte y está consumiendo la API, consulte `Microsoft.AspNetCore.DataProtection.Abstractions` .

* [Microsoft. AspNetCore. PROTECCION](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection/) contiene la implementación básica del sistema de protección de datos, incluidas las operaciones criptográficas principales, la administración de claves, la configuración y la extensibilidad. Para crear una instancia del sistema de protección de datos (por ejemplo, agregarlo a un <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> ) o modificar o ampliar su comportamiento, consulte `Microsoft.AspNetCore.DataProtection` .

* [Microsoft. AspNetCore. PROTECCION. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) contiene API adicionales que los desarrolladores pueden encontrar útiles pero que no pertenecen al paquete principal. Por ejemplo, este paquete contiene métodos de generador para crear instancias del sistema de protección de datos para almacenar las claves en una ubicación del sistema de archivos sin la inserción de dependencias (vea <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider> ). También contiene métodos de extensión para limitar la duración de las cargas protegidas (vea <xref:Microsoft.AspNetCore.DataProtection.ITimeLimitedDataProtector> ).

* [Microsoft.AspNetCore.DataProtection.SystemWeb](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.SystemWeb/) se puede instalar en una aplicación ASP.net 4. x existente para redirigir sus `<machineKey>` operaciones con el fin de usar la nueva pila de protección de datos ASP.net Core. Para obtener más información, vea <xref:security/data-protection/compatibility/replacing-machinekey>.

* [Microsoft. AspNetCore. Cryptography. derivación](https://www.nuget.org/packages/Microsoft.AspNetCore.Cryptography.KeyDerivation/) proporciona una implementación de la rutina de hash de contraseña PBKDF2 y la pueden usar los sistemas que deben administrar las contraseñas de usuario de forma segura. Para obtener más información, vea <xref:security/data-protection/consumer-apis/password-hashing>.

## <a name="additional-resources"></a>Recursos adicionales

<xref:host-and-deploy/web-farm>
