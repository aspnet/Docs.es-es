---
title: Almacenamiento seguro de secretos de aplicación en el desarrollo en ASP.NET Core
author: rick-anderson
description: Aprenda a almacenar y recuperar información confidencial como secretos de la aplicación durante el desarrollo de una aplicación ASP.NET Core.
ms.author: scaddie
ms.custom: mvc
ms.date: 4/20/2020
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
uid: security/app-secrets
ms.openlocfilehash: 174f831583c2ef6cb7f122a22fe855acc8fe3047
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056873"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a>Almacenamiento seguro de secretos de aplicación en el desarrollo en ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27)y [Scott Addie](https://github.com/scottaddie)

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))

En este documento se explican técnicas para almacenar y recuperar datos confidenciales durante el desarrollo de una aplicación ASP.NET Core en una máquina de desarrollo. Nunca almacene contraseñas u otros datos confidenciales en el código fuente. Los secretos de producción no deben usarse para el desarrollo o las pruebas. Los secretos no deben implementarse con la aplicación. En su lugar, los secretos deben estar disponibles en el entorno de producción a través de un medio controlado como variables de entorno, Azure Key Vault, etc. Puede almacenar y proteger los secretos de prueba y producción de Azure con el [proveedor de configuración de Azure Key Vault](xref:security/key-vault-configuration).

## <a name="environment-variables"></a>Variables de entorno

Las variables de entorno se usan para evitar el almacenamiento de secretos de aplicación en código o en archivos de configuración local. Las variables de entorno invalidan los valores de configuración de todos los orígenes de configuración especificados previamente.

Considere una ASP.NET Core aplicación web en la que está habilitada la seguridad de **cuentas de usuario individuales** . Se incluye una cadena de conexión de base de datos predeterminada en el archivo del proyecto *appsettings.json* con la clave `DefaultConnection` . La cadena de conexión predeterminada es para LocalDB, que se ejecuta en modo de usuario y no requiere una contraseña. Durante la implementación de la aplicación, el `DefaultConnection` valor de clave se puede invalidar con el valor de una variable de entorno. La variable de entorno puede almacenar la cadena de conexión completa con credenciales confidenciales.

> [!WARNING]
> Normalmente, las variables de entorno se almacenan en texto sin cifrar. Si el equipo o el proceso se ve comprometido, las entidades de entorno pueden tener acceso a las variables de entorno que no son de confianza. Pueden ser necesarias medidas adicionales para evitar la divulgación de secretos de usuario.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>Administrador de secretos

La herramienta Administrador de secretos almacena datos confidenciales durante el desarrollo de un proyecto de ASP.NET Core. En este contexto, una parte de la información confidencial es un secreto de la aplicación. Los secretos de la aplicación se almacenan en una ubicación independiente del árbol del proyecto. Los secretos de la aplicación se asocian a un proyecto específico o se comparten entre varios proyectos. Los secretos de la aplicación no se protegen en el control de código fuente.

> [!WARNING]
> La herramienta Administrador de secretos no cifra los secretos almacenados y no debe tratarse como un almacén de confianza. Solo es para fines de desarrollo. Las claves y los valores se almacenan en un archivo de configuración JSON en el directorio del perfil de usuario.

## <a name="how-the-secret-manager-tool-works"></a>Cómo funciona la herramienta Administrador de secretos

La herramienta Administrador de secretos abstrae los detalles de implementación, como dónde y cómo se almacenan los valores. Puede usar la herramienta sin conocer estos detalles de implementación. Los valores se almacenan en un archivo de configuración JSON en una carpeta de Perfil de usuario protegida por el sistema en el equipo local:

# <a name="windows"></a>[Windows](#tab/windows)

Ruta de acceso del sistema de archivos:

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

Ruta de acceso del sistema de archivos:

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

En las rutas de acceso de archivo anteriores, reemplace `<user_secrets_id>` por el `UserSecretsId` valor especificado en el archivo *. csproj* .

No Escriba código que dependa de la ubicación o el formato de los datos guardados con la herramienta Administrador de secretos. Estos detalles de implementación pueden cambiar. Por ejemplo, los valores de secreto no están cifrados, pero podrían estar en el futuro.

## <a name="enable-secret-storage"></a>Habilitar el almacenamiento de secretos

La herramienta Administrador de secretos funciona en opciones de configuración específicas del proyecto almacenadas en el perfil de usuario.

La herramienta Administrador de secretos incluye un `init` comando en SDK de .net Core 3.0.100 o posterior. Para usar los secretos de usuario, ejecute el siguiente comando en el directorio del proyecto:

```dotnetcli
dotnet user-secrets init
```

El comando anterior agrega un `UserSecretsId` elemento dentro `PropertyGroup` del archivo *. csproj* . De forma predeterminada, el texto interno de `UserSecretsId` es un GUID. El texto interno es arbitrario, pero es único para el proyecto.

[!code-xml[](app-secrets/samples/3.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

En Visual Studio, haga clic con el botón derecho en el proyecto en Explorador de soluciones y seleccione **administrar secretos de usuario** en el menú contextual. Este gesto agrega un `UserSecretsId` elemento, rellenado con un GUID, al archivo *. csproj* .

## <a name="set-a-secret"></a>Establecer un secreto

Defina un secreto de la aplicación que conste de una clave y su valor. El secreto está asociado con el valor del proyecto `UserSecretsId` . Por ejemplo, ejecute el siguiente comando desde el directorio en el que existe el archivo *. csproj* :

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

En el ejemplo anterior, el signo de dos puntos denota que `Movies` es un literal de objeto con una `ServiceApiKey` propiedad.

La herramienta Administrador de secretos también se puede usar desde otros directorios. Use la `--project` opción para proporcionar la ruta de acceso del sistema de archivos en la que existe el archivo *. csproj* . Por ejemplo:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>Simplificación de la estructura de JSON en Visual Studio

El gesto **administrar secretos de usuario** de Visual Studio abre un *secrets.jsen* el archivo en el editor de texto. Reemplace el contenido de *secrets.js* por los pares clave-valor que se van a almacenar. Por ejemplo:

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

La estructura JSON se acopla después de las modificaciones a través `dotnet user-secrets remove` de o `dotnet user-secrets set` . Por ejemplo, si se ejecuta, se `dotnet user-secrets remove "Movies:ConnectionString"` contrae el `Movies` literal de objeto. El archivo modificado es similar al siguiente:

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>Establecer varios secretos

Un lote de secretos se puede establecer mediante el JSON de canalización para el `set` comando. En el ejemplo siguiente, el *input.jsen* el contenido del archivo se canaliza al `set` comando.

# <a name="windows"></a>[Windows](#tab/windows)

Abra un shell de comandos y ejecute el siguiente comando:

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

Abra un shell de comandos y ejecute el siguiente comando:

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>Acceder a un secreto

La [API de configuración de ASP.net Core](xref:fundamentals/configuration/index) proporciona acceso a los secretos del administrador de secretos.

El origen de configuración de secretos de usuario se agrega automáticamente en modo de desarrollo cuando el proyecto llama <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> a para inicializar una nueva instancia del host con los valores predeterminados preconfigurados. `CreateDefaultBuilder` llama a <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> cuando <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> es <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development> :

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

Cuando `CreateDefaultBuilder` no se llama a, agregue el origen de configuración de secretos de usuario explícitamente mediante una llamada a <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> . Llame `AddUserSecrets` solo cuando la aplicación se ejecute en el entorno de desarrollo, como se muestra en el ejemplo siguiente:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program2.cs?name=snippet_Host&highlight=6-9)]

Los secretos de usuario se pueden recuperar a través de la `Configuration` API:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a>Asignación de secretos a un POCO

La asignación de un literal de objeto completo a un POCO (una clase .NET simple con propiedades) es útil para agregar propiedades relacionadas.

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Para asignar los secretos anteriores a un POCO, use la `Configuration` característica de [enlace del gráfico de objetos](xref:fundamentals/configuration/index#bind-to-an-object-graph) de la API. El código siguiente enlaza a un poco personalizado `MovieSettings` y obtiene acceso al valor de la `ServiceApiKey` propiedad:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

Los `Movies:ConnectionString` `Movies:ServiceApiKey` secretos y se asignan a las propiedades correspondientes en `MovieSettings` :

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>Reemplazo de cadenas con secretos

Almacenar las contraseñas en texto sin formato no es seguro. Por ejemplo, una cadena de conexión de base de datos almacenada en *appsettings.json* puede incluir una contraseña para el usuario especificado:

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

Un enfoque más seguro consiste en almacenar la contraseña como un secreto. Por ejemplo:

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

Quite el `Password` par clave-valor de la cadena de conexión de *appsettings.json* . Por ejemplo:

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings.json?highlight=3)]

El valor del secreto se puede establecer en la <xref:System.Data.SqlClient.SqlConnectionStringBuilder> propiedad de un objeto <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> para completar la cadena de conexión:

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a>Enumeración de los secretos

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Ejecute el siguiente comando desde el directorio en el que existe el archivo *. csproj* :

```dotnetcli
dotnet user-secrets list
```

Se mostrará la siguiente salida:

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

En el ejemplo anterior, un signo de dos puntos en los nombres de clave denota la jerarquía de objetos dentro de *secrets.js* .

## <a name="remove-a-single-secret"></a>Quitar un único secreto

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Ejecute el siguiente comando desde el directorio en el que existe el archivo *. csproj* :

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

Se modificó el *secrets.jsde* la aplicación en el archivo para quitar el par clave-valor asociado a la `MoviesConnectionString` clave:

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

`dotnet user-secrets list` muestra el siguiente mensaje:

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>Quitar todos los secretos

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Ejecute el siguiente comando desde el directorio en el que existe el archivo *. csproj* :

```dotnetcli
dotnet user-secrets clear
```

Se han eliminado todos los secretos de usuario de la aplicación del *secrets.jsen* el archivo:

```json
{}
```

Al ejecutar, se `dotnet user-secrets list` muestra el siguiente mensaje:

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>Recursos adicionales

* Vea [este problema](https://github.com/dotnet/AspNetCore.Docs/issues/16328) para obtener información sobre cómo obtener acceso a Secret Manager desde IIS.
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Por [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27)y [Scott Addie](https://github.com/scottaddie)

[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))

En este documento se explican técnicas para almacenar y recuperar datos confidenciales durante el desarrollo de una aplicación ASP.NET Core en una máquina de desarrollo. Nunca almacene contraseñas u otros datos confidenciales en el código fuente. Los secretos de producción no deben usarse para el desarrollo o las pruebas. Los secretos no deben implementarse con la aplicación. En su lugar, los secretos deben estar disponibles en el entorno de producción a través de un medio controlado como variables de entorno, Azure Key Vault, etc. Puede almacenar y proteger los secretos de prueba y producción de Azure con el [proveedor de configuración de Azure Key Vault](xref:security/key-vault-configuration).

## <a name="environment-variables"></a>Variables de entorno

Las variables de entorno se usan para evitar el almacenamiento de secretos de aplicación en código o en archivos de configuración local. Las variables de entorno invalidan los valores de configuración de todos los orígenes de configuración especificados previamente.

Considere una ASP.NET Core aplicación web en la que está habilitada la seguridad de **cuentas de usuario individuales** . Se incluye una cadena de conexión de base de datos predeterminada en el archivo del proyecto *appsettings.json* con la clave `DefaultConnection` . La cadena de conexión predeterminada es para LocalDB, que se ejecuta en modo de usuario y no requiere una contraseña. Durante la implementación de la aplicación, el `DefaultConnection` valor de clave se puede invalidar con el valor de una variable de entorno. La variable de entorno puede almacenar la cadena de conexión completa con credenciales confidenciales.

> [!WARNING]
> Normalmente, las variables de entorno se almacenan en texto sin cifrar. Si el equipo o el proceso se ve comprometido, las entidades de entorno pueden tener acceso a las variables de entorno que no son de confianza. Pueden ser necesarias medidas adicionales para evitar la divulgación de secretos de usuario.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a>Administrador de secretos

La herramienta Administrador de secretos almacena datos confidenciales durante el desarrollo de un proyecto de ASP.NET Core. En este contexto, una parte de la información confidencial es un secreto de la aplicación. Los secretos de la aplicación se almacenan en una ubicación independiente del árbol del proyecto. Los secretos de la aplicación se asocian a un proyecto específico o se comparten entre varios proyectos. Los secretos de la aplicación no se protegen en el control de código fuente.

> [!WARNING]
> La herramienta Administrador de secretos no cifra los secretos almacenados y no debe tratarse como un almacén de confianza. Solo es para fines de desarrollo. Las claves y los valores se almacenan en un archivo de configuración JSON en el directorio del perfil de usuario.

## <a name="how-the-secret-manager-tool-works"></a>Cómo funciona la herramienta Administrador de secretos

La herramienta Administrador de secretos abstrae los detalles de implementación, como dónde y cómo se almacenan los valores. Puede usar la herramienta sin conocer estos detalles de implementación. Los valores se almacenan en un archivo de configuración JSON en una carpeta de Perfil de usuario protegida por el sistema en el equipo local:

# <a name="windows"></a>[Windows](#tab/windows)

Ruta de acceso del sistema de archivos:

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

Ruta de acceso del sistema de archivos:

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

En las rutas de acceso de archivo anteriores, reemplace `<user_secrets_id>` por el `UserSecretsId` valor especificado en el archivo *. csproj* .

No Escriba código que dependa de la ubicación o el formato de los datos guardados con la herramienta Administrador de secretos. Estos detalles de implementación pueden cambiar. Por ejemplo, los valores de secreto no están cifrados, pero podrían estar en el futuro.

## <a name="enable-secret-storage"></a>Habilitar el almacenamiento de secretos

La herramienta Administrador de secretos funciona en opciones de configuración específicas del proyecto almacenadas en el perfil de usuario.

Para usar secretos de usuario, defina un `UserSecretsId` elemento dentro `PropertyGroup` del archivo *. csproj* . El texto interno de `UserSecretsId` es arbitrario, pero es único para el proyecto. Normalmente, los desarrolladores generan un GUID para `UserSecretsId` .

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> En Visual Studio, haga clic con el botón derecho en el proyecto en Explorador de soluciones y seleccione **administrar secretos de usuario** en el menú contextual. Este gesto agrega un `UserSecretsId` elemento, rellenado con un GUID, al archivo *. csproj* .

## <a name="set-a-secret"></a>Establecer un secreto

Defina un secreto de la aplicación que conste de una clave y su valor. El secreto está asociado con el valor del proyecto `UserSecretsId` . Por ejemplo, ejecute el siguiente comando desde el directorio en el que existe el archivo *. csproj* :

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

En el ejemplo anterior, el signo de dos puntos denota que `Movies` es un literal de objeto con una `ServiceApiKey` propiedad.

La herramienta Administrador de secretos también se puede usar desde otros directorios. Use la `--project` opción para proporcionar la ruta de acceso del sistema de archivos en la que existe el archivo *. csproj* . Por ejemplo:

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a>Simplificación de la estructura de JSON en Visual Studio

El gesto **administrar secretos de usuario** de Visual Studio abre un *secrets.jsen* el archivo en el editor de texto. Reemplace el contenido de *secrets.js* por los pares clave-valor que se van a almacenar. Por ejemplo:

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

La estructura JSON se acopla después de las modificaciones a través `dotnet user-secrets remove` de o `dotnet user-secrets set` . Por ejemplo, si se ejecuta, se `dotnet user-secrets remove "Movies:ConnectionString"` contrae el `Movies` literal de objeto. El archivo modificado es similar al siguiente:

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a>Establecer varios secretos

Un lote de secretos se puede establecer mediante el JSON de canalización para el `set` comando. En el ejemplo siguiente, el *input.jsen* el contenido del archivo se canaliza al `set` comando.

# <a name="windows"></a>[Windows](#tab/windows)

Abra un shell de comandos y ejecute el siguiente comando:

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[Linux/macOS](#tab/linux+macos)

Abra un shell de comandos y ejecute el siguiente comando:

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a>Acceder a un secreto

La [API de configuración de ASP.net Core](xref:fundamentals/configuration/index) proporciona acceso a los secretos del administrador de secretos.

Si el proyecto tiene como destino .NET Framework, instale el [Microsoft.Extensions.Configprimario. ](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) Paquete NuGet de UserSecrets.

En ASP.NET Core 2,0 o posterior, el origen de configuración de los secretos de usuario se agrega automáticamente en modo de desarrollo cuando el proyecto llama <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> a para inicializar una nueva instancia del host con los valores predeterminados preconfigurados. `CreateDefaultBuilder` llama a <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> cuando <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> es <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development> :

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

Cuando `CreateDefaultBuilder` no se llama a, se agrega explícitamente el origen de configuración de secretos de usuario mediante una llamada a <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> en el `Startup` constructor. Llame `AddUserSecrets` solo cuando la aplicación se ejecute en el entorno de desarrollo, como se muestra en el ejemplo siguiente:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_StartupConstructor&highlight=12)]

Los secretos de usuario se pueden recuperar a través de la `Configuration` API:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a>Asignación de secretos a un POCO

La asignación de un literal de objeto completo a un POCO (una clase .NET simple con propiedades) es útil para agregar propiedades relacionadas.

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Para asignar los secretos anteriores a un POCO, use la `Configuration` característica de [enlace del gráfico de objetos](xref:fundamentals/configuration/index#bind-to-an-object-graph) de la API. El código siguiente enlaza a un poco personalizado `MovieSettings` y obtiene acceso al valor de la `ServiceApiKey` propiedad:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

Los `Movies:ConnectionString` `Movies:ServiceApiKey` secretos y se asignan a las propiedades correspondientes en `MovieSettings` :

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a>Reemplazo de cadenas con secretos

Almacenar las contraseñas en texto sin formato no es seguro. Por ejemplo, una cadena de conexión de base de datos almacenada en *appsettings.json* puede incluir una contraseña para el usuario especificado:

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

Un enfoque más seguro consiste en almacenar la contraseña como un secreto. Por ejemplo:

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

Quite el `Password` par clave-valor de la cadena de conexión de *appsettings.json* . Por ejemplo:

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

El valor del secreto se puede establecer en la <xref:System.Data.SqlClient.SqlConnectionStringBuilder> propiedad de un objeto <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> para completar la cadena de conexión:

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a>Enumeración de los secretos

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Ejecute el siguiente comando desde el directorio en el que existe el archivo *. csproj* :

```dotnetcli
dotnet user-secrets list
```

Se mostrará la siguiente salida:

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

En el ejemplo anterior, un signo de dos puntos en los nombres de clave denota la jerarquía de objetos dentro de *secrets.js* .

## <a name="remove-a-single-secret"></a>Quitar un único secreto

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Ejecute el siguiente comando desde el directorio en el que existe el archivo *. csproj* :

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

Se modificó el *secrets.jsde* la aplicación en el archivo para quitar el par clave-valor asociado a la `MoviesConnectionString` clave:

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

Al ejecutar, se `dotnet user-secrets list` muestra el siguiente mensaje:

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a>Quitar todos los secretos

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

Ejecute el siguiente comando desde el directorio en el que existe el archivo *. csproj* :

```dotnetcli
dotnet user-secrets clear
```

Se han eliminado todos los secretos de usuario de la aplicación del *secrets.jsen* el archivo:

```json
{}
```

Al ejecutar, se `dotnet user-secrets list` muestra el siguiente mensaje:

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a>Recursos adicionales

* Vea [este problema](https://github.com/dotnet/AspNetCore.Docs/issues/16328) para obtener información sobre cómo obtener acceso a Secret Manager desde IIS.
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end