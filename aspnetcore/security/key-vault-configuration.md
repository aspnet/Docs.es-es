---
title: Azure Key Vault proveedor de configuración en ASP.NET Core
author: rick-anderson
description: Aprenda a usar el proveedor de configuración de Azure Key Vault para configurar una aplicación mediante pares de nombre y valor cargados en tiempo de ejecución.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, devx-track-azurecli
ms.date: 02/07/2020
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
uid: security/key-vault-configuration
ms.openlocfilehash: 4b035fe59b8576eb387ddce67943386ccab55492
ms.sourcegitcommit: 8dfcd2b4be936950c228b4d98430622a04254cd7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/26/2020
ms.locfileid: "97792088"
---
# <a name="azure-key-vault-configuration-provider-in-aspnet-core"></a><span data-ttu-id="a7701-103">Azure Key Vault proveedor de configuración en ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a7701-103">Azure Key Vault Configuration Provider in ASP.NET Core</span></span>

<span data-ttu-id="a7701-104">Por [Andrew Stanton-enfermera](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="a7701-104">By [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a7701-105">En este documento se explica cómo usar el proveedor de configuración de [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para cargar los valores de configuración de la aplicación de Azure Key Vault secretos.</span><span class="sxs-lookup"><span data-stu-id="a7701-105">This document explains how to use the [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="a7701-106">Azure Key Vault es un servicio basado en la nube que ayuda a proteger las claves criptográficas y los secretos que usan las aplicaciones y los servicios.</span><span class="sxs-lookup"><span data-stu-id="a7701-106">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="a7701-107">Entre los escenarios comunes para usar Azure Key Vault con ASP.NET Core aplicaciones se incluyen:</span><span class="sxs-lookup"><span data-stu-id="a7701-107">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="a7701-108">Controlar el acceso a los datos de configuración confidenciales.</span><span class="sxs-lookup"><span data-stu-id="a7701-108">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="a7701-109">Cumplir los requisitos de los módulos de seguridad de hardware (HSM) de FIPS 140-2 nivel 2 validados al almacenar los datos de configuración.</span><span class="sxs-lookup"><span data-stu-id="a7701-109">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="a7701-110">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a7701-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="a7701-111">Paquetes</span><span class="sxs-lookup"><span data-stu-id="a7701-111">Packages</span></span>

<span data-ttu-id="a7701-112">Agregue las referencias de paquete para los siguientes paquetes:</span><span class="sxs-lookup"><span data-stu-id="a7701-112">Add package references for the following packages:</span></span>

* [<span data-ttu-id="a7701-113">Azure.Extensions.AspNetCore.Configuration.Secrets</span><span class="sxs-lookup"><span data-stu-id="a7701-113">Azure.Extensions.AspNetCore.Configuration.Secrets</span></span>](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.Configuration.Secrets)
* <span data-ttu-id="a7701-114">[Azure.Identity](https://www.nuget.org/packages/Azure.Identity)</span><span class="sxs-lookup"><span data-stu-id="a7701-114">[Azure.Identity](https://www.nuget.org/packages/Azure.Identity)</span></span>

## <a name="sample-app"></a><span data-ttu-id="a7701-115">Aplicación de ejemplo</span><span class="sxs-lookup"><span data-stu-id="a7701-115">Sample app</span></span>

<span data-ttu-id="a7701-116">La aplicación de ejemplo se ejecuta en uno de los dos modos determinados por la instrucción que se `#define` encuentra en la parte superior del archivo *Program.CS* :</span><span class="sxs-lookup"><span data-stu-id="a7701-116">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="a7701-117">`Certificate`: Muestra el uso de un Azure Key Vault ID. de cliente y un certificado X. 509 para tener acceso a los secretos almacenados en Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="a7701-117">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="a7701-118">Esta versión del ejemplo se puede ejecutar desde cualquier ubicación, implementada en Azure App Service o cualquier host capaz de servir a una aplicación ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a7701-118">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="a7701-119">`Managed`: Muestra cómo usar [identidades administradas para los recursos de Azure](/azure/active-directory/managed-identities-azure-resources/overview) para autenticar la aplicación en Azure Key Vault con la autenticación de Azure ad sin credenciales almacenadas en el código o la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a7701-119">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="a7701-120">Al usar identidades administradas para autenticar, no se requieren un identificador de aplicación Azure AD y una contraseña (secreto de cliente).</span><span class="sxs-lookup"><span data-stu-id="a7701-120">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="a7701-121">La `Managed` versión del ejemplo debe implementarse en Azure.</span><span class="sxs-lookup"><span data-stu-id="a7701-121">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="a7701-122">Siga las instrucciones de la sección [uso de las identidades administradas para los recursos de Azure](#use-managed-identities-for-azure-resources) .</span><span class="sxs-lookup"><span data-stu-id="a7701-122">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="a7701-123">Para obtener más información sobre cómo configurar una aplicación de ejemplo mediante directivas de preprocesador ( `#define` ), vea <xref:index#preprocessor-directives-in-sample-code> .</span><span class="sxs-lookup"><span data-stu-id="a7701-123">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="a7701-124">Almacenamiento de secretos en el entorno de desarrollo</span><span class="sxs-lookup"><span data-stu-id="a7701-124">Secret storage in the Development environment</span></span>

<span data-ttu-id="a7701-125">Establezca secretos localmente mediante la [herramienta de administrador de secretos](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="a7701-125">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="a7701-126">Cuando la aplicación de ejemplo se ejecuta en el equipo local en el entorno de desarrollo, los secretos se cargan desde el almacén de secretos del usuario local.</span><span class="sxs-lookup"><span data-stu-id="a7701-126">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local user secrets store.</span></span>

<span data-ttu-id="a7701-127">La herramienta Administrador de secretos requiere una `<UserSecretsId>` propiedad en el archivo de proyecto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a7701-127">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="a7701-128">Establezca el valor de propiedad ( `{GUID}` ) en cualquier GUID único:</span><span class="sxs-lookup"><span data-stu-id="a7701-128">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="a7701-129">Los secretos se crean como pares de nombre y valor.</span><span class="sxs-lookup"><span data-stu-id="a7701-129">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="a7701-130">Los valores jerárquicos (secciones de configuración) usan un `:` signo (dos puntos) como separador en ASP.net Core nombres de clave de [configuración](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="a7701-130">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="a7701-131">El administrador de secretos se usa desde un shell de comandos abierto a la [raíz del contenido](xref:fundamentals/index#content-root)del proyecto, donde `{SECRET NAME}` es el nombre y `{SECRET VALUE}` es el valor:</span><span class="sxs-lookup"><span data-stu-id="a7701-131">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="a7701-132">Ejecute los siguientes comandos en un shell de comandos desde la [raíz del contenido](xref:fundamentals/index#content-root) del proyecto para establecer los secretos de la aplicación de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="a7701-132">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="a7701-133">Cuando estos secretos se almacenan en Azure Key Vault en el [almacenamiento de secretos en el entorno de producción con Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) sección, el `_dev` sufijo se cambia a `_prod` .</span><span class="sxs-lookup"><span data-stu-id="a7701-133">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="a7701-134">El sufijo proporciona una indicación visual en la salida de la aplicación que indica el origen de los valores de configuración.</span><span class="sxs-lookup"><span data-stu-id="a7701-134">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="a7701-135">Almacenamiento secreto en el entorno de producción con Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="a7701-135">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="a7701-136">Las instrucciones que se proporcionan en la guía de [Inicio rápido: establecer y recuperar un secreto de Azure Key Vault con CLI de Azure](/azure/key-vault/quick-create-cli) tema se resumen aquí para crear un Azure Key Vault y almacenar secretos usados por la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="a7701-136">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="a7701-137">Consulte el tema para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="a7701-137">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="a7701-138">Abra Azure Cloud Shell con cualquiera de los métodos siguientes en el [Azure portal](https://portal.azure.com/):</span><span class="sxs-lookup"><span data-stu-id="a7701-138">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="a7701-139">Seleccione **Pruébelo** en la esquina superior derecha de un bloque de código.</span><span class="sxs-lookup"><span data-stu-id="a7701-139">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="a7701-140">Use la cadena de búsqueda "CLI de Azure" en el cuadro de texto.</span><span class="sxs-lookup"><span data-stu-id="a7701-140">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="a7701-141">Abra Cloud Shell en el explorador con el botón **iniciar Cloud Shell** .</span><span class="sxs-lookup"><span data-stu-id="a7701-141">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="a7701-142">Seleccione el botón **Cloud Shell** en el menú de la esquina superior derecha del Azure portal.</span><span class="sxs-lookup"><span data-stu-id="a7701-142">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="a7701-143">Para obtener más información, vea [CLI de Azure](/cli/azure/) e [información general de Azure Cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="a7701-143">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="a7701-144">Si aún no está autenticado, inicie sesión con el `az login` comando.</span><span class="sxs-lookup"><span data-stu-id="a7701-144">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="a7701-145">Cree un grupo de recursos con el siguiente comando, donde `{RESOURCE GROUP NAME}` es el nombre del grupo de recursos para el nuevo grupo de recursos y `{LOCATION}` es la región de Azure (Datacenter):</span><span class="sxs-lookup"><span data-stu-id="a7701-145">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="a7701-146">Cree un almacén de claves en el grupo de recursos con el siguiente comando, donde `{KEY VAULT NAME}` es el nombre del nuevo almacén de claves y `{LOCATION}` es la región de Azure (Datacenter):</span><span class="sxs-lookup"><span data-stu-id="a7701-146">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="a7701-147">Cree secretos en el almacén de claves como pares de nombre y valor.</span><span class="sxs-lookup"><span data-stu-id="a7701-147">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="a7701-148">Azure Key Vault los nombres de los secretos se limitan a caracteres alfanuméricos y guiones.</span><span class="sxs-lookup"><span data-stu-id="a7701-148">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="a7701-149">Los valores jerárquicos (secciones de configuración) usan `--` (dos guiones) como separador.</span><span class="sxs-lookup"><span data-stu-id="a7701-149">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="a7701-150">Los dos puntos, que se suelen usar para delimitar una sección de una subclave en [ASP.net Core configuración](xref:fundamentals/configuration/index), no se permiten en los nombres de secreto del almacén de claves.</span><span class="sxs-lookup"><span data-stu-id="a7701-150">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="a7701-151">Por lo tanto, se usan dos guiones y se intercambian por dos puntos cuando se cargan los secretos en la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a7701-151">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="a7701-152">Los siguientes secretos son para su uso con la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="a7701-152">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="a7701-153">Los valores incluyen un `_prod` sufijo para distinguirlos de los `_dev` valores de sufijo cargados en el entorno de desarrollo de los secretos de usuario.</span><span class="sxs-lookup"><span data-stu-id="a7701-153">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="a7701-154">Reemplace `{KEY VAULT NAME}` por el nombre del almacén de claves que creó en el paso anterior:</span><span class="sxs-lookup"><span data-stu-id="a7701-154">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="a7701-155">Usar el identificador de aplicación y el certificado X. 509 para aplicaciones no hospedadas en Azure</span><span class="sxs-lookup"><span data-stu-id="a7701-155">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="a7701-156">Configure Azure AD, Azure Key Vault y la aplicación para que use un identificador de aplicación Azure Active Directory y un certificado X. 509 para autenticarse en un almacén de claves **cuando la aplicación se hospeda fuera de Azure**.</span><span class="sxs-lookup"><span data-stu-id="a7701-156">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="a7701-157">Para más información, consulte el artículo [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates) (Claves, secretos y certificados).</span><span class="sxs-lookup"><span data-stu-id="a7701-157">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="a7701-158">Aunque el uso de un identificador de aplicación y un certificado X. 509 es compatible con las aplicaciones hospedadas en Azure, se recomienda usar [identidades administradas para los recursos de Azure](#use-managed-identities-for-azure-resources) al hospedar una aplicación en Azure.</span><span class="sxs-lookup"><span data-stu-id="a7701-158">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="a7701-159">Las identidades administradas no requieren el almacenamiento de un certificado en la aplicación o en el entorno de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="a7701-159">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="a7701-160">La aplicación de ejemplo usa un identificador de aplicación y un certificado X. 509 cuando la `#define` instrucción en la parte superior del archivo *Program.CS* se establece en `Certificate` .</span><span class="sxs-lookup"><span data-stu-id="a7701-160">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="a7701-161">Cree un certificado de archivo PKCS # 12 (*. pfx*).</span><span class="sxs-lookup"><span data-stu-id="a7701-161">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="a7701-162">Las opciones para crear certificados incluyen [Makecert en Windows](/windows/desktop/seccrypto/makecert) y [OpenSSL](https://www.openssl.org/).</span><span class="sxs-lookup"><span data-stu-id="a7701-162">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="a7701-163">Instale el certificado en el almacén de certificados personales del usuario actual.</span><span class="sxs-lookup"><span data-stu-id="a7701-163">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="a7701-164">Marcar la clave como exportable es opcional.</span><span class="sxs-lookup"><span data-stu-id="a7701-164">Marking the key as exportable is optional.</span></span> <span data-ttu-id="a7701-165">Anote la huella digital del certificado, que se usa más adelante en este proceso.</span><span class="sxs-lookup"><span data-stu-id="a7701-165">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="a7701-166">Exporte el certificado de archivo PKCS # 12 (*. pfx*) como un certificado codificado en der (*. cer*).</span><span class="sxs-lookup"><span data-stu-id="a7701-166">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="a7701-167">Registre la aplicación con Azure AD (**registros de aplicaciones**).</span><span class="sxs-lookup"><span data-stu-id="a7701-167">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="a7701-168">Cargue el certificado codificado en DER (*. cer*) en Azure ad:</span><span class="sxs-lookup"><span data-stu-id="a7701-168">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="a7701-169">Seleccione la aplicación en Azure AD.</span><span class="sxs-lookup"><span data-stu-id="a7701-169">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="a7701-170">Vaya a **certificados & Secrets**.</span><span class="sxs-lookup"><span data-stu-id="a7701-170">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="a7701-171">Seleccione **upload Certificate (cargar certificado** ) para cargar el certificado, que contiene la clave pública.</span><span class="sxs-lookup"><span data-stu-id="a7701-171">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="a7701-172">Un certificado *. cer*, *. pem* o *. CRT* es aceptable.</span><span class="sxs-lookup"><span data-stu-id="a7701-172">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="a7701-173">Almacene el nombre del almacén de claves, el identificador de la aplicación y la huella digital del certificado en el archivo de la aplicación *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="a7701-173">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="a7701-174">Vaya a **almacenes de claves** en el Azure portal.</span><span class="sxs-lookup"><span data-stu-id="a7701-174">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="a7701-175">Seleccione el almacén de claves que creó en el [almacenamiento de secretos en el entorno de producción con Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) sección.</span><span class="sxs-lookup"><span data-stu-id="a7701-175">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="a7701-176">Seleccione **Directivas de acceso**.</span><span class="sxs-lookup"><span data-stu-id="a7701-176">Select **Access policies**.</span></span>
1. <span data-ttu-id="a7701-177">Seleccione **Agregar directiva de acceso**.</span><span class="sxs-lookup"><span data-stu-id="a7701-177">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="a7701-178">Abra los **permisos de secreto** y proporcione la aplicación con los permisos **Get** y **List** .</span><span class="sxs-lookup"><span data-stu-id="a7701-178">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="a7701-179">Seleccione **seleccionar entidad** de seguridad y seleccione la aplicación registrada por nombre.</span><span class="sxs-lookup"><span data-stu-id="a7701-179">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="a7701-180">Seleccione el botón **Seleccionar**.</span><span class="sxs-lookup"><span data-stu-id="a7701-180">Select the **Select** button.</span></span>
1. <span data-ttu-id="a7701-181">Seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="a7701-181">Select **OK**.</span></span>
1. <span data-ttu-id="a7701-182">Seleccione **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="a7701-182">Select **Save**.</span></span>
1. <span data-ttu-id="a7701-183">Implemente la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a7701-183">Deploy the app.</span></span>

<span data-ttu-id="a7701-184">La `Certificate` aplicación de ejemplo obtiene sus valores de configuración de `IConfigurationRoot` con el mismo nombre que el secreto:</span><span class="sxs-lookup"><span data-stu-id="a7701-184">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="a7701-185">Valores no jerárquicos: el valor de `SecretName` se obtiene con `config["SecretName"]` .</span><span class="sxs-lookup"><span data-stu-id="a7701-185">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="a7701-186">Valores jerárquicos (secciones): notación de uso `:` (dos puntos) o el `GetSection` método de extensión.</span><span class="sxs-lookup"><span data-stu-id="a7701-186">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="a7701-187">Use cualquiera de estos métodos para obtener el valor de configuración:</span><span class="sxs-lookup"><span data-stu-id="a7701-187">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="a7701-188">El certificado X. 509 se administra mediante el sistema operativo.</span><span class="sxs-lookup"><span data-stu-id="a7701-188">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="a7701-189">La aplicación llama a **AddAzureKeyVault** con los valores proporcionados por el *appsettings.json* archivo:</span><span class="sxs-lookup"><span data-stu-id="a7701-189">The app calls **AddAzureKeyVault** with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=46-49)]

<span data-ttu-id="a7701-190">Valores de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="a7701-190">Example values:</span></span>

* <span data-ttu-id="a7701-191">Nombre del almacén de claves: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="a7701-191">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="a7701-192">IDENTIFICADOR de la aplicación: `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="a7701-192">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="a7701-193">Huella digital del certificado: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="a7701-193">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="a7701-194">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="a7701-194">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/3.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="a7701-195">Al ejecutar la aplicación, se muestran los valores de secreto cargados en una página web.</span><span class="sxs-lookup"><span data-stu-id="a7701-195">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="a7701-196">En el entorno de desarrollo, los valores secretos se cargan con el `_dev` sufijo.</span><span class="sxs-lookup"><span data-stu-id="a7701-196">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="a7701-197">En el entorno de producción, los valores se cargan con el `_prod` sufijo.</span><span class="sxs-lookup"><span data-stu-id="a7701-197">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="a7701-198">Uso de identidades administradas para los recursos de Azure</span><span class="sxs-lookup"><span data-stu-id="a7701-198">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="a7701-199">**Una aplicación implementada en Azure** puede aprovechar las [identidades administradas para los recursos de Azure](/azure/active-directory/managed-identities-azure-resources/overview), lo que permite que la aplicación se autentique con Azure Key Vault mediante la autenticación de Azure ad sin credenciales (identificador de aplicación y contraseña/secreto de cliente) almacenadas en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a7701-199">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="a7701-200">La aplicación de ejemplo usa identidades administradas para los recursos de Azure cuando la `#define` instrucción que se encuentra en la parte superior del archivo *Program.CS* se establece en `Managed` .</span><span class="sxs-lookup"><span data-stu-id="a7701-200">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="a7701-201">Escriba el nombre del almacén en el archivo de la aplicación *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="a7701-201">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="a7701-202">La aplicación de ejemplo no requiere un identificador de aplicación y una contraseña (secreto de cliente) cuando se establece en la `Managed` versión, por lo que puede omitir esas entradas de configuración.</span><span class="sxs-lookup"><span data-stu-id="a7701-202">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="a7701-203">La aplicación se implementa en Azure y Azure autentica la aplicación para acceder a Azure Key Vault solo mediante el nombre del almacén almacenado en el *appsettings.json* archivo.</span><span class="sxs-lookup"><span data-stu-id="a7701-203">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="a7701-204">Implemente la aplicación de ejemplo en Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="a7701-204">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="a7701-205">Una aplicación implementada en Azure App Service se registra automáticamente con Azure AD cuando se crea el servicio.</span><span class="sxs-lookup"><span data-stu-id="a7701-205">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="a7701-206">Obtenga el identificador de objeto de la implementación para usarlo en el siguiente comando.</span><span class="sxs-lookup"><span data-stu-id="a7701-206">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="a7701-207">El identificador de objeto se muestra en el Azure Portal en el **Identity** Panel de la App Service.</span><span class="sxs-lookup"><span data-stu-id="a7701-207">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="a7701-208">Con CLI de Azure y el identificador de objeto de la aplicación, proporcione la aplicación con los `list` `get` permisos y para acceder al almacén de claves:</span><span class="sxs-lookup"><span data-stu-id="a7701-208">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="a7701-209">**Reinicie la aplicación** con CLI de Azure, PowerShell o el Azure portal.</span><span class="sxs-lookup"><span data-stu-id="a7701-209">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="a7701-210">La aplicación de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="a7701-210">The sample app:</span></span>

* <span data-ttu-id="a7701-211">Crea una instancia de la `DefaultAzureCredential` clase, la credencial intenta obtener un token de acceso del entorno para los recursos de Azure.</span><span class="sxs-lookup"><span data-stu-id="a7701-211">Creates an instance of the `DefaultAzureCredential` class, the credential attempts to obtain an access token from environment for Azure resources.</span></span>
* <span data-ttu-id="a7701-212">[`Azure.Security.KeyVault.Secrets.Secrets`](/dotnet/api/azure.security.keyvault.secrets)Se crea un nuevo con la `DefaultAzureCredential` instancia de.</span><span class="sxs-lookup"><span data-stu-id="a7701-212">A new [`Azure.Security.KeyVault.Secrets.Secrets`](/dotnet/api/azure.security.keyvault.secrets) is created with the `DefaultAzureCredential` instance.</span></span>
* <span data-ttu-id="a7701-213">La `Azure.Security.KeyVault.Secrets.Secrets` instancia se utiliza con una implementación predeterminada de `Azure.Extensions.AspNetCore.Configuration.Secrets` que carga todos los valores secretos y reemplaza los dobles guiones ( `--` ) por dos puntos ( `:` ) en los nombres de clave.</span><span class="sxs-lookup"><span data-stu-id="a7701-213">The `Azure.Security.KeyVault.Secrets.Secrets` instance is used with a default implementation of `Azure.Extensions.AspNetCore.Configuration.Secrets` that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/3.x/SampleApp/Program.cs?name=snippet2&highlight=12-14)]

<span data-ttu-id="a7701-214">Valor de ejemplo de nombre del almacén de claves: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="a7701-214">Key vault name example value: `contosovault`</span></span>

<span data-ttu-id="a7701-215">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="a7701-215">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="a7701-216">Al ejecutar la aplicación, se muestran los valores de secreto cargados en una página web.</span><span class="sxs-lookup"><span data-stu-id="a7701-216">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="a7701-217">En el entorno de desarrollo, los valores secretos tienen el `_dev` sufijo porque son proporcionados por los secretos del usuario.</span><span class="sxs-lookup"><span data-stu-id="a7701-217">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="a7701-218">En el entorno de producción, los valores se cargan con el `_prod` sufijo porque son proporcionados por Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="a7701-218">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="a7701-219">Si recibe un `Access denied` error, confirme que la aplicación está registrada con Azure ad y que proporciona acceso al almacén de claves.</span><span class="sxs-lookup"><span data-stu-id="a7701-219">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="a7701-220">Confirme que ha reiniciado el servicio en Azure.</span><span class="sxs-lookup"><span data-stu-id="a7701-220">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="a7701-221">Para obtener información sobre el uso del proveedor con una identidad administrada y una canalización DevOps de Azure, consulte [creación de una conexión de servicio Azure Resource Manager a una máquina virtual con una identidad de servicio administrada](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="a7701-221">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="configuration-options"></a><span data-ttu-id="a7701-222">Opciones de configuración</span><span class="sxs-lookup"><span data-stu-id="a7701-222">Configuration options</span></span>

<span data-ttu-id="a7701-223">AddAzureKeyVault puede aceptar un AzureKeyVaultConfigurationOptions:</span><span class="sxs-lookup"><span data-stu-id="a7701-223">AddAzureKeyVault can accept an AzureKeyVaultConfigurationOptions:</span></span>

```csharp
config.AddAzureKeyVault(new SecretClient(new URI("Your Key Vault Endpoint"), new DefaultAzureCredential()),
                        new AzureKeyVaultConfigurationOptions())
    {
        ...
    });
```

| <span data-ttu-id="a7701-224">Propiedad</span><span class="sxs-lookup"><span data-stu-id="a7701-224">Property</span></span>         | <span data-ttu-id="a7701-225">Descripción</span><span class="sxs-lookup"><span data-stu-id="a7701-225">Description</span></span> |
| ---------------- | ----------- |
| `Manager`        | <span data-ttu-id="a7701-226">`Azure.Extensions.AspNetCore.Configuration.Secrets` instancia usada para controlar la carga de secretos.</span><span class="sxs-lookup"><span data-stu-id="a7701-226">`Azure.Extensions.AspNetCore.Configuration.Secrets` instance used to control secret loading.</span></span> |
| `ReloadInterval` | <span data-ttu-id="a7701-227">`Timespan` para esperar entre los intentos de sondeo del almacén de claves en busca de cambios.</span><span class="sxs-lookup"><span data-stu-id="a7701-227">`Timespan` to wait between attempts at polling the key vault for changes.</span></span> <span data-ttu-id="a7701-228">El valor predeterminado es `null` (la configuración no se recarga).</span><span class="sxs-lookup"><span data-stu-id="a7701-228">The default value is `null` (configuration isn't reloaded).</span></span> |

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="a7701-229">Usar un prefijo de nombre de clave</span><span class="sxs-lookup"><span data-stu-id="a7701-229">Use a key name prefix</span></span>

<span data-ttu-id="a7701-230">AddAzureKeyVault proporciona una sobrecarga que acepta una implementación de `Azure.Extensions.AspNetCore.Configuration.Secrets` , que le permite controlar cómo se convierten los secretos del almacén de claves en claves de configuración.</span><span class="sxs-lookup"><span data-stu-id="a7701-230">AddAzureKeyVault provides an overload that accepts an implementation of `Azure.Extensions.AspNetCore.Configuration.Secrets`, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="a7701-231">Por ejemplo, puede implementar la interfaz para cargar valores de secreto basados en un valor de prefijo que se proporciona al inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a7701-231">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="a7701-232">Esto le permite, por ejemplo, cargar secretos en función de la versión de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a7701-232">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="a7701-233">No use prefijos en secretos del almacén de claves para colocar secretos para varias aplicaciones en el mismo almacén de claves o para colocar secretos de entorno (por ejemplo, *desarrollo* frente a secretos de *producción* ) en el mismo almacén.</span><span class="sxs-lookup"><span data-stu-id="a7701-233">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="a7701-234">Se recomienda que las distintas aplicaciones y entornos de desarrollo y producción usen almacenes de claves independientes para aislar los entornos de aplicación para obtener el máximo nivel de seguridad.</span><span class="sxs-lookup"><span data-stu-id="a7701-234">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="a7701-235">En el siguiente ejemplo, se establece un secreto en el almacén de claves (y el uso de la herramienta Administrador de secretos para el entorno de desarrollo) para `5000-AppSecret` (no se permiten puntos en los nombres de secreto del almacén de claves).</span><span class="sxs-lookup"><span data-stu-id="a7701-235">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="a7701-236">Este secreto representa un secreto de aplicación para la versión 5.0.0.0 de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a7701-236">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="a7701-237">Para otra versión de la aplicación, 5.1.0.0, se agrega un secreto al almacén de claves (y con la herramienta de administración de secretos) para `5100-AppSecret` .</span><span class="sxs-lookup"><span data-stu-id="a7701-237">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="a7701-238">Cada versión de la aplicación carga su valor de secreto con versión en su configuración como, lo que `AppSecret` elimina la versión mientras carga el secreto.</span><span class="sxs-lookup"><span data-stu-id="a7701-238">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="a7701-239">Se llama a AddAzureKeyVault con un personalizado `Azure.Extensions.AspNetCore.Configuration.Secrets` :</span><span class="sxs-lookup"><span data-stu-id="a7701-239">AddAzureKeyVault is called with a custom `Azure.Extensions.AspNetCore.Configuration.Secrets`:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="a7701-240">La `Azure.Extensions.AspNetCore.Configuration.Secrets` implementación reacciona a los prefijos de versión de los secretos para cargar el secreto adecuado en la configuración:</span><span class="sxs-lookup"><span data-stu-id="a7701-240">The `Azure.Extensions.AspNetCore.Configuration.Secrets` implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="a7701-241">`Load` carga un secreto cuando su nombre comienza con el prefijo.</span><span class="sxs-lookup"><span data-stu-id="a7701-241">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="a7701-242">No se cargan otros secretos.</span><span class="sxs-lookup"><span data-stu-id="a7701-242">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="a7701-243">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="a7701-243">`GetKey`:</span></span>
  * <span data-ttu-id="a7701-244">Quita el prefijo del nombre del secreto.</span><span class="sxs-lookup"><span data-stu-id="a7701-244">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="a7701-245">Reemplaza dos guiones en cualquier nombre por `KeyDelimiter` , que es el delimitador usado en la configuración (normalmente un signo de dos puntos).</span><span class="sxs-lookup"><span data-stu-id="a7701-245">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="a7701-246">Azure Key Vault no permite un signo de dos puntos en los nombres de secreto.</span><span class="sxs-lookup"><span data-stu-id="a7701-246">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="a7701-247">`Load`Un algoritmo de proveedor llama al método que recorre en iteración los secretos del almacén para encontrar los que tienen el prefijo de versión.</span><span class="sxs-lookup"><span data-stu-id="a7701-247">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="a7701-248">Cuando se encuentra un prefijo de versión con `Load` , el algoritmo utiliza el `GetKey` método para devolver el nombre de configuración del nombre de secreto.</span><span class="sxs-lookup"><span data-stu-id="a7701-248">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="a7701-249">Elimina el prefijo de versión del nombre del secreto y devuelve el resto del nombre de secreto para cargarlo en los pares de nombre y valor de configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a7701-249">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="a7701-250">Cuando se implementa este enfoque:</span><span class="sxs-lookup"><span data-stu-id="a7701-250">When this approach is implemented:</span></span>

1. <span data-ttu-id="a7701-251">La versión de la aplicación especificada en el archivo de proyecto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a7701-251">The app's version specified in the app's project file.</span></span> <span data-ttu-id="a7701-252">En el ejemplo siguiente, la versión de la aplicación se establece en `5.0.0.0` :</span><span class="sxs-lookup"><span data-stu-id="a7701-252">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="a7701-253">Confirme que una `<UserSecretsId>` propiedad está presente en el archivo de proyecto de la aplicación, donde `{GUID}` es un GUID proporcionado por el usuario:</span><span class="sxs-lookup"><span data-stu-id="a7701-253">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="a7701-254">Guarde los siguientes secretos localmente con la [herramienta de administración de secretos](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="a7701-254">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="a7701-255">Los secretos se guardan en Azure Key Vault mediante los siguientes comandos de CLI de Azure:</span><span class="sxs-lookup"><span data-stu-id="a7701-255">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="a7701-256">Cuando se ejecuta la aplicación, se cargan los secretos del almacén de claves.</span><span class="sxs-lookup"><span data-stu-id="a7701-256">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="a7701-257">El secreto de cadena de `5000-AppSecret` se corresponde con la versión de la aplicación especificada en el archivo de proyecto de la aplicación ( `5.0.0.0` ).</span><span class="sxs-lookup"><span data-stu-id="a7701-257">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="a7701-258">La versión, `5000` (con el guion), se elimina del nombre de la clave.</span><span class="sxs-lookup"><span data-stu-id="a7701-258">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="a7701-259">En toda la aplicación, la lectura de la configuración con la clave `AppSecret` carga el valor del secreto.</span><span class="sxs-lookup"><span data-stu-id="a7701-259">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="a7701-260">Si se cambia la versión de la aplicación en el archivo de proyecto a `5.1.0.0` y se vuelve a ejecutar la aplicación, el valor secreto devuelto está `5.1.0.0_secret_value_dev` en el entorno de desarrollo y `5.1.0.0_secret_value_prod` en producción.</span><span class="sxs-lookup"><span data-stu-id="a7701-260">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="a7701-261">También puede proporcionar su propia <xref:Azure.Security.KeyVault.Secrets.SecretClient> implementación a AddAzureKeyVault.</span><span class="sxs-lookup"><span data-stu-id="a7701-261">You can also provide your own <xref:Azure.Security.KeyVault.Secrets.SecretClient> implementation to AddAzureKeyVault.</span></span> <span data-ttu-id="a7701-262">Un cliente personalizado permite compartir una única instancia del cliente a través de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a7701-262">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="a7701-263">Enlace de una matriz a una clase</span><span class="sxs-lookup"><span data-stu-id="a7701-263">Bind an array to a class</span></span>

<span data-ttu-id="a7701-264">El proveedor es capaz de leer valores de configuración en una matriz para enlazar a una matriz POCO.</span><span class="sxs-lookup"><span data-stu-id="a7701-264">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="a7701-265">Al leer desde un origen de configuración que permite que las claves contengan separadores de dos puntos ( `:` ), se usa un segmento de clave numérica para distinguir las claves que componen una matriz ( `:0:` , `:1:` , &hellip; `:{n}:` ).</span><span class="sxs-lookup"><span data-stu-id="a7701-265">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="a7701-266">Para obtener más información, vea [Configuración: enlazar una matriz a una clase](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="a7701-266">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="a7701-267">Azure Key Vault teclas no pueden usar un signo de dos puntos como separador.</span><span class="sxs-lookup"><span data-stu-id="a7701-267">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="a7701-268">El enfoque que se describe en este tema utiliza los guiones dobles ( `--` ) como separador de valores jerárquicos (secciones).</span><span class="sxs-lookup"><span data-stu-id="a7701-268">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="a7701-269">Las claves de matriz se almacenan en Azure Key Vault con dobles guiones y segmentos de clave numéricos ( `--0--` , `--1--` , &hellip; `--{n}--` ).</span><span class="sxs-lookup"><span data-stu-id="a7701-269">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="a7701-270">Examine la siguiente configuración de proveedor de registro de [Serilog](https://serilog.net/) proporcionada por un archivo JSON.</span><span class="sxs-lookup"><span data-stu-id="a7701-270">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="a7701-271">Hay dos literales de objeto definidos en la `WriteTo` matriz que reflejan dos *receptores* de Serilog, que describen los destinos para el registro de la salida:</span><span class="sxs-lookup"><span data-stu-id="a7701-271">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

```json
"Serilog": {
  "WriteTo": [
    {
      "Name": "AzureTableStorage",
      "Args": {
        "storageTableName": "logs",
        "connectionString": "DefaultEnd...ountKey=Eby8...GMGw=="
      }
    },
    {
      "Name": "AzureDocumentDB",
      "Args": {
        "endpointUrl": "https://contoso.documents.azure.com:443",
        "authorizationKey": "Eby8...GMGw=="
      }
    }
  ]
}
```

<span data-ttu-id="a7701-272">La configuración que se muestra en el archivo JSON anterior se almacena en Azure Key Vault mediante la notación de doble guión ( `--` ) y los segmentos numéricos:</span><span class="sxs-lookup"><span data-stu-id="a7701-272">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="a7701-273">Clave</span><span class="sxs-lookup"><span data-stu-id="a7701-273">Key</span></span> | <span data-ttu-id="a7701-274">Valor</span><span class="sxs-lookup"><span data-stu-id="a7701-274">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="a7701-275">Volver a cargar los secretos</span><span class="sxs-lookup"><span data-stu-id="a7701-275">Reload secrets</span></span>

<span data-ttu-id="a7701-276">Los secretos se almacenan en caché hasta que `IConfigurationRoot.Reload()` se llama a.</span><span class="sxs-lookup"><span data-stu-id="a7701-276">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="a7701-277">La aplicación no respeta los secretos expirados, deshabilitados y actualizados en el almacén de claves hasta que `Reload` se ejecute.</span><span class="sxs-lookup"><span data-stu-id="a7701-277">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="a7701-278">Secretos deshabilitados y expirados</span><span class="sxs-lookup"><span data-stu-id="a7701-278">Disabled and expired secrets</span></span>

<span data-ttu-id="a7701-279">Los secretos deshabilitados y caducados producen una <xref:Azure.RequestFailedException> .</span><span class="sxs-lookup"><span data-stu-id="a7701-279">Disabled and expired secrets throw a <xref:Azure.RequestFailedException>.</span></span> <span data-ttu-id="a7701-280">Para evitar que se inicie la aplicación, proporcione la configuración mediante un proveedor de configuración diferente o actualice el secreto deshabilitado o expirado.</span><span class="sxs-lookup"><span data-stu-id="a7701-280">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="a7701-281">Solución de problemas</span><span class="sxs-lookup"><span data-stu-id="a7701-281">Troubleshoot</span></span>

<span data-ttu-id="a7701-282">Cuando la aplicación no carga la configuración mediante el proveedor, se escribe un mensaje de error en la [infraestructura de registro de ASP.net Core](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="a7701-282">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="a7701-283">Las siguientes condiciones impedirán que se cargue la configuración:</span><span class="sxs-lookup"><span data-stu-id="a7701-283">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="a7701-284">La aplicación o el certificado no están configurados correctamente en Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="a7701-284">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="a7701-285">El almacén de claves no existe en Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="a7701-285">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="a7701-286">La aplicación no está autorizada para acceder al almacén de claves.</span><span class="sxs-lookup"><span data-stu-id="a7701-286">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="a7701-287">La Directiva de acceso no `Get` incluye `List` los permisos y.</span><span class="sxs-lookup"><span data-stu-id="a7701-287">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="a7701-288">En el almacén de claves, los datos de configuración (par nombre-valor) tienen un nombre incorrecto, faltan, están deshabilitados o han expirado.</span><span class="sxs-lookup"><span data-stu-id="a7701-288">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="a7701-289">La aplicación tiene un nombre de almacén de claves incorrecto ( `KeyVaultName` ), Azure ad ID. de la aplicación ( `AzureADApplicationId` ) o Azure ad huella digital del certificado ( `AzureADCertThumbprint` ) o Azure ad DirectoryId ( `AzureADDirectoryId` ).</span><span class="sxs-lookup"><span data-stu-id="a7701-289">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`), or Azure AD DirectoryId (`AzureADDirectoryId`).</span></span>
* <span data-ttu-id="a7701-290">La clave de configuración (nombre) es incorrecta en la aplicación para el valor que está intentando cargar.</span><span class="sxs-lookup"><span data-stu-id="a7701-290">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="a7701-291">Al agregar la Directiva de acceso de la aplicación al almacén de claves, la Directiva se creó, pero no se seleccionó el botón **Guardar** en la interfaz de usuario de **directivas de acceso** .</span><span class="sxs-lookup"><span data-stu-id="a7701-291">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a7701-292">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="a7701-292">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="a7701-293">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="a7701-293">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="a7701-294">Microsoft Azure: documentación de Key Vault</span><span class="sxs-lookup"><span data-stu-id="a7701-294">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="a7701-295">Cómo generar y transferir claves protegidas con HSM para Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="a7701-295">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="a7701-296">Clase KeyVaultClient</span><span class="sxs-lookup"><span data-stu-id="a7701-296">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="a7701-297">Guía de inicio rápido: Establecimiento y recuperación de un secreto de Azure Key Vault mediante una aplicación web de .NET</span><span class="sxs-lookup"><span data-stu-id="a7701-297">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="a7701-298">Tutorial: Uso de Azure Key Vault con una máquina virtual Windows en .NET</span><span class="sxs-lookup"><span data-stu-id="a7701-298">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a7701-299">En este documento se explica cómo usar el proveedor de configuración de [key Vault de Microsoft Azure](https://azure.microsoft.com/services/key-vault/) para cargar los valores de configuración de la aplicación de Azure Key Vault secretos.</span><span class="sxs-lookup"><span data-stu-id="a7701-299">This document explains how to use the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) Configuration Provider to load app configuration values from Azure Key Vault secrets.</span></span> <span data-ttu-id="a7701-300">Azure Key Vault es un servicio basado en la nube que ayuda a proteger las claves criptográficas y los secretos que usan las aplicaciones y los servicios.</span><span class="sxs-lookup"><span data-stu-id="a7701-300">Azure Key Vault is a cloud-based service that assists in safeguarding cryptographic keys and secrets used by apps and services.</span></span> <span data-ttu-id="a7701-301">Entre los escenarios comunes para usar Azure Key Vault con ASP.NET Core aplicaciones se incluyen:</span><span class="sxs-lookup"><span data-stu-id="a7701-301">Common scenarios for using Azure Key Vault with ASP.NET Core apps include:</span></span>

* <span data-ttu-id="a7701-302">Controlar el acceso a los datos de configuración confidenciales.</span><span class="sxs-lookup"><span data-stu-id="a7701-302">Controlling access to sensitive configuration data.</span></span>
* <span data-ttu-id="a7701-303">Cumplir los requisitos de los módulos de seguridad de hardware (HSM) de FIPS 140-2 nivel 2 validados al almacenar los datos de configuración.</span><span class="sxs-lookup"><span data-stu-id="a7701-303">Meeting the requirement for FIPS 140-2 Level 2 validated Hardware Security Modules (HSM's) when storing configuration data.</span></span>

<span data-ttu-id="a7701-304">[Vea o descargue el código de ejemplo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a7701-304">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/key-vault-configuration/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="packages"></a><span data-ttu-id="a7701-305">Paquetes</span><span class="sxs-lookup"><span data-stu-id="a7701-305">Packages</span></span>

<span data-ttu-id="a7701-306">Agregue una referencia de paquete al [Microsoft.Extensions.Configprimario. Paquete AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) .</span><span class="sxs-lookup"><span data-stu-id="a7701-306">Add a package reference to the [Microsoft.Extensions.Configuration.AzureKeyVault](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureKeyVault/) package.</span></span>

## <a name="sample-app"></a><span data-ttu-id="a7701-307">Aplicación de ejemplo</span><span class="sxs-lookup"><span data-stu-id="a7701-307">Sample app</span></span>

<span data-ttu-id="a7701-308">La aplicación de ejemplo se ejecuta en uno de los dos modos determinados por la instrucción que se `#define` encuentra en la parte superior del archivo *Program.CS* :</span><span class="sxs-lookup"><span data-stu-id="a7701-308">The sample app runs in either of two modes determined by the `#define` statement at the top of the *Program.cs* file:</span></span>

* <span data-ttu-id="a7701-309">`Certificate`: Muestra el uso de un Azure Key Vault ID. de cliente y un certificado X. 509 para tener acceso a los secretos almacenados en Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="a7701-309">`Certificate`: Demonstrates the use of an Azure Key Vault Client ID and X.509 certificate to access secrets stored in Azure Key Vault.</span></span> <span data-ttu-id="a7701-310">Esta versión del ejemplo se puede ejecutar desde cualquier ubicación, implementada en Azure App Service o cualquier host capaz de servir a una aplicación ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a7701-310">This version of the sample can be run from any location, deployed to Azure App Service or any host capable of serving an ASP.NET Core app.</span></span>
* <span data-ttu-id="a7701-311">`Managed`: Muestra cómo usar [identidades administradas para los recursos de Azure](/azure/active-directory/managed-identities-azure-resources/overview) para autenticar la aplicación en Azure Key Vault con la autenticación de Azure ad sin credenciales almacenadas en el código o la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a7701-311">`Managed`: Demonstrates how to use [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview) to authenticate the app to Azure Key Vault with Azure AD authentication without credentials stored in the app's code or configuration.</span></span> <span data-ttu-id="a7701-312">Al usar identidades administradas para autenticar, no se requieren un identificador de aplicación Azure AD y una contraseña (secreto de cliente).</span><span class="sxs-lookup"><span data-stu-id="a7701-312">When using managed identities to authenticate, an Azure AD Application ID and Password (Client Secret) aren't required.</span></span> <span data-ttu-id="a7701-313">La `Managed` versión del ejemplo debe implementarse en Azure.</span><span class="sxs-lookup"><span data-stu-id="a7701-313">The `Managed` version of the sample must be deployed to Azure.</span></span> <span data-ttu-id="a7701-314">Siga las instrucciones de la sección [uso de las identidades administradas para los recursos de Azure](#use-managed-identities-for-azure-resources) .</span><span class="sxs-lookup"><span data-stu-id="a7701-314">Follow the guidance in the [Use the Managed identities for Azure resources](#use-managed-identities-for-azure-resources) section.</span></span>

<span data-ttu-id="a7701-315">Para obtener más información sobre cómo configurar una aplicación de ejemplo mediante directivas de preprocesador ( `#define` ), vea <xref:index#preprocessor-directives-in-sample-code> .</span><span class="sxs-lookup"><span data-stu-id="a7701-315">For more information on how to configure a sample app using preprocessor directives (`#define`), see <xref:index#preprocessor-directives-in-sample-code>.</span></span>

## <a name="secret-storage-in-the-development-environment"></a><span data-ttu-id="a7701-316">Almacenamiento de secretos en el entorno de desarrollo</span><span class="sxs-lookup"><span data-stu-id="a7701-316">Secret storage in the Development environment</span></span>

<span data-ttu-id="a7701-317">Establezca secretos localmente mediante la [herramienta de administrador de secretos](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="a7701-317">Set secrets locally using the [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="a7701-318">Cuando la aplicación de ejemplo se ejecuta en el equipo local en el entorno de desarrollo, los secretos se cargan desde el almacén de secretos del usuario local.</span><span class="sxs-lookup"><span data-stu-id="a7701-318">When the sample app runs on the local machine in the Development environment, secrets are loaded from the local user secrets store.</span></span>

<span data-ttu-id="a7701-319">La herramienta Administrador de secretos requiere una `<UserSecretsId>` propiedad en el archivo de proyecto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a7701-319">The Secret Manager tool requires a `<UserSecretsId>` property in the app's project file.</span></span> <span data-ttu-id="a7701-320">Establezca el valor de propiedad ( `{GUID}` ) en cualquier GUID único:</span><span class="sxs-lookup"><span data-stu-id="a7701-320">Set the property value (`{GUID}`) to any unique GUID:</span></span>

```xml
<PropertyGroup>
  <UserSecretsId>{GUID}</UserSecretsId>
</PropertyGroup>
```

<span data-ttu-id="a7701-321">Los secretos se crean como pares de nombre y valor.</span><span class="sxs-lookup"><span data-stu-id="a7701-321">Secrets are created as name-value pairs.</span></span> <span data-ttu-id="a7701-322">Los valores jerárquicos (secciones de configuración) usan un `:` signo (dos puntos) como separador en ASP.net Core nombres de clave de [configuración](xref:fundamentals/configuration/index) .</span><span class="sxs-lookup"><span data-stu-id="a7701-322">Hierarchical values (configuration sections) use a `:` (colon) as a separator in [ASP.NET Core configuration](xref:fundamentals/configuration/index) key names.</span></span>

<span data-ttu-id="a7701-323">El administrador de secretos se usa desde un shell de comandos abierto a la [raíz del contenido](xref:fundamentals/index#content-root)del proyecto, donde `{SECRET NAME}` es el nombre y `{SECRET VALUE}` es el valor:</span><span class="sxs-lookup"><span data-stu-id="a7701-323">The Secret Manager is used from a command shell opened to the project's [content root](xref:fundamentals/index#content-root), where `{SECRET NAME}` is the name and `{SECRET VALUE}` is the value:</span></span>

```dotnetcli
dotnet user-secrets set "{SECRET NAME}" "{SECRET VALUE}"
```

<span data-ttu-id="a7701-324">Ejecute los siguientes comandos en un shell de comandos desde la [raíz del contenido](xref:fundamentals/index#content-root) del proyecto para establecer los secretos de la aplicación de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="a7701-324">Execute the following commands in a command shell from the project's [content root](xref:fundamentals/index#content-root) to set the secrets for the sample app:</span></span>

```dotnetcli
dotnet user-secrets set "SecretName" "secret_value_1_dev"
dotnet user-secrets set "Section:SecretName" "secret_value_2_dev"
```

<span data-ttu-id="a7701-325">Cuando estos secretos se almacenan en Azure Key Vault en el [almacenamiento de secretos en el entorno de producción con Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) sección, el `_dev` sufijo se cambia a `_prod` .</span><span class="sxs-lookup"><span data-stu-id="a7701-325">When these secrets are stored in Azure Key Vault in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section, the `_dev` suffix is changed to `_prod`.</span></span> <span data-ttu-id="a7701-326">El sufijo proporciona una indicación visual en la salida de la aplicación que indica el origen de los valores de configuración.</span><span class="sxs-lookup"><span data-stu-id="a7701-326">The suffix provides a visual cue in the app's output indicating the source of the configuration values.</span></span>

## <a name="secret-storage-in-the-production-environment-with-azure-key-vault"></a><span data-ttu-id="a7701-327">Almacenamiento secreto en el entorno de producción con Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="a7701-327">Secret storage in the Production environment with Azure Key Vault</span></span>

<span data-ttu-id="a7701-328">Las instrucciones que se proporcionan en la guía de [Inicio rápido: establecer y recuperar un secreto de Azure Key Vault con CLI de Azure](/azure/key-vault/quick-create-cli) tema se resumen aquí para crear un Azure Key Vault y almacenar secretos usados por la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="a7701-328">The instructions provided by the [Quickstart: Set and retrieve a secret from Azure Key Vault using Azure CLI](/azure/key-vault/quick-create-cli) topic are summarized here for creating an Azure Key Vault and storing secrets used by the sample app.</span></span> <span data-ttu-id="a7701-329">Consulte el tema para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="a7701-329">Refer to the topic for further details.</span></span>

1. <span data-ttu-id="a7701-330">Abra Azure Cloud Shell con cualquiera de los métodos siguientes en el [Azure portal](https://portal.azure.com/):</span><span class="sxs-lookup"><span data-stu-id="a7701-330">Open Azure Cloud shell using any one of the following methods in the [Azure portal](https://portal.azure.com/):</span></span>

   * <span data-ttu-id="a7701-331">Seleccione **Pruébelo** en la esquina superior derecha de un bloque de código.</span><span class="sxs-lookup"><span data-stu-id="a7701-331">Select **Try It** in the upper-right corner of a code block.</span></span> <span data-ttu-id="a7701-332">Use la cadena de búsqueda "CLI de Azure" en el cuadro de texto.</span><span class="sxs-lookup"><span data-stu-id="a7701-332">Use the search string "Azure CLI" in the text box.</span></span>
   * <span data-ttu-id="a7701-333">Abra Cloud Shell en el explorador con el botón **iniciar Cloud Shell** .</span><span class="sxs-lookup"><span data-stu-id="a7701-333">Open Cloud Shell in your browser with the **Launch Cloud Shell** button.</span></span>
   * <span data-ttu-id="a7701-334">Seleccione el botón **Cloud Shell** en el menú de la esquina superior derecha del Azure portal.</span><span class="sxs-lookup"><span data-stu-id="a7701-334">Select the **Cloud Shell** button on the menu in the upper-right corner of the Azure portal.</span></span>

   <span data-ttu-id="a7701-335">Para obtener más información, vea [CLI de Azure](/cli/azure/) e [información general de Azure Cloud Shell](/azure/cloud-shell/overview).</span><span class="sxs-lookup"><span data-stu-id="a7701-335">For more information, see [Azure CLI](/cli/azure/) and [Overview of Azure Cloud Shell](/azure/cloud-shell/overview).</span></span>

1. <span data-ttu-id="a7701-336">Si aún no está autenticado, inicie sesión con el `az login` comando.</span><span class="sxs-lookup"><span data-stu-id="a7701-336">If you aren't already authenticated, sign in with the `az login` command.</span></span>

1. <span data-ttu-id="a7701-337">Cree un grupo de recursos con el siguiente comando, donde `{RESOURCE GROUP NAME}` es el nombre del grupo de recursos para el nuevo grupo de recursos y `{LOCATION}` es la región de Azure (Datacenter):</span><span class="sxs-lookup"><span data-stu-id="a7701-337">Create a resource group with the following command, where `{RESOURCE GROUP NAME}` is the resource group name for the new resource group and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az group create --name "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="a7701-338">Cree un almacén de claves en el grupo de recursos con el siguiente comando, donde `{KEY VAULT NAME}` es el nombre del nuevo almacén de claves y `{LOCATION}` es la región de Azure (Datacenter):</span><span class="sxs-lookup"><span data-stu-id="a7701-338">Create a key vault in the resource group with the following command, where `{KEY VAULT NAME}` is the name for the new key vault and `{LOCATION}` is the Azure region (datacenter):</span></span>

   ```azurecli
   az keyvault create --name {KEY VAULT NAME} --resource-group "{RESOURCE GROUP NAME}" --location {LOCATION}
   ```

1. <span data-ttu-id="a7701-339">Cree secretos en el almacén de claves como pares de nombre y valor.</span><span class="sxs-lookup"><span data-stu-id="a7701-339">Create secrets in the key vault as name-value pairs.</span></span>

   <span data-ttu-id="a7701-340">Azure Key Vault los nombres de los secretos se limitan a caracteres alfanuméricos y guiones.</span><span class="sxs-lookup"><span data-stu-id="a7701-340">Azure Key Vault secret names are limited to alphanumeric characters and dashes.</span></span> <span data-ttu-id="a7701-341">Los valores jerárquicos (secciones de configuración) usan `--` (dos guiones) como separador.</span><span class="sxs-lookup"><span data-stu-id="a7701-341">Hierarchical values (configuration sections) use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="a7701-342">Los dos puntos, que se suelen usar para delimitar una sección de una subclave en [ASP.net Core configuración](xref:fundamentals/configuration/index), no se permiten en los nombres de secreto del almacén de claves.</span><span class="sxs-lookup"><span data-stu-id="a7701-342">Colons, which are normally used to delimit a section from a subkey in [ASP.NET Core configuration](xref:fundamentals/configuration/index), aren't allowed in key vault secret names.</span></span> <span data-ttu-id="a7701-343">Por lo tanto, se usan dos guiones y se intercambian por dos puntos cuando se cargan los secretos en la configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a7701-343">Therefore, two dashes are used and swapped for a colon when the secrets are loaded into the app's configuration.</span></span>

   <span data-ttu-id="a7701-344">Los siguientes secretos son para su uso con la aplicación de ejemplo.</span><span class="sxs-lookup"><span data-stu-id="a7701-344">The following secrets are for use with the sample app.</span></span> <span data-ttu-id="a7701-345">Los valores incluyen un `_prod` sufijo para distinguirlos de los `_dev` valores de sufijo cargados en el entorno de desarrollo de los secretos de usuario.</span><span class="sxs-lookup"><span data-stu-id="a7701-345">The values include a `_prod` suffix to distinguish them from the `_dev` suffix values loaded in the Development environment from User Secrets.</span></span> <span data-ttu-id="a7701-346">Reemplace `{KEY VAULT NAME}` por el nombre del almacén de claves que creó en el paso anterior:</span><span class="sxs-lookup"><span data-stu-id="a7701-346">Replace `{KEY VAULT NAME}` with the name of the key vault that you created in the prior step:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "SecretName" --value "secret_value_1_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "Section--SecretName" --value "secret_value_2_prod"
   ```

## <a name="use-application-id-and-x509-certificate-for-non-azure-hosted-apps"></a><span data-ttu-id="a7701-347">Usar el identificador de aplicación y el certificado X. 509 para aplicaciones no hospedadas en Azure</span><span class="sxs-lookup"><span data-stu-id="a7701-347">Use Application ID and X.509 certificate for non-Azure-hosted apps</span></span>

<span data-ttu-id="a7701-348">Configure Azure AD, Azure Key Vault y la aplicación para que use un identificador de aplicación Azure Active Directory y un certificado X. 509 para autenticarse en un almacén de claves **cuando la aplicación se hospeda fuera de Azure**.</span><span class="sxs-lookup"><span data-stu-id="a7701-348">Configure Azure AD, Azure Key Vault, and the app to use an Azure Active Directory Application ID and X.509 certificate to authenticate to a key vault **when the app is hosted outside of Azure**.</span></span> <span data-ttu-id="a7701-349">Para más información, consulte el artículo [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates) (Claves, secretos y certificados).</span><span class="sxs-lookup"><span data-stu-id="a7701-349">For more information, see [About keys, secrets, and certificates](/azure/key-vault/about-keys-secrets-and-certificates).</span></span>

> [!NOTE]
> <span data-ttu-id="a7701-350">Aunque el uso de un identificador de aplicación y un certificado X. 509 es compatible con las aplicaciones hospedadas en Azure, se recomienda usar [identidades administradas para los recursos de Azure](#use-managed-identities-for-azure-resources) al hospedar una aplicación en Azure.</span><span class="sxs-lookup"><span data-stu-id="a7701-350">Although using an Application ID and X.509 certificate is supported for apps hosted in Azure, we recommend using [Managed identities for Azure resources](#use-managed-identities-for-azure-resources) when hosting an app in Azure.</span></span> <span data-ttu-id="a7701-351">Las identidades administradas no requieren el almacenamiento de un certificado en la aplicación o en el entorno de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="a7701-351">Managed identities don't require storing a certificate in the app or in the development environment.</span></span>

<span data-ttu-id="a7701-352">La aplicación de ejemplo usa un identificador de aplicación y un certificado X. 509 cuando la `#define` instrucción en la parte superior del archivo *Program.CS* se establece en `Certificate` .</span><span class="sxs-lookup"><span data-stu-id="a7701-352">The sample app uses an Application ID and X.509 certificate when the `#define` statement at the top of the *Program.cs* file is set to `Certificate`.</span></span>

1. <span data-ttu-id="a7701-353">Cree un certificado de archivo PKCS # 12 (*. pfx*).</span><span class="sxs-lookup"><span data-stu-id="a7701-353">Create a PKCS#12 archive (*.pfx*) certificate.</span></span> <span data-ttu-id="a7701-354">Las opciones para crear certificados incluyen [Makecert en Windows](/windows/desktop/seccrypto/makecert) y [OpenSSL](https://www.openssl.org/).</span><span class="sxs-lookup"><span data-stu-id="a7701-354">Options for creating certificates include [MakeCert on Windows](/windows/desktop/seccrypto/makecert) and [OpenSSL](https://www.openssl.org/).</span></span>
1. <span data-ttu-id="a7701-355">Instale el certificado en el almacén de certificados personales del usuario actual.</span><span class="sxs-lookup"><span data-stu-id="a7701-355">Install the certificate into the current user's personal certificate store.</span></span> <span data-ttu-id="a7701-356">Marcar la clave como exportable es opcional.</span><span class="sxs-lookup"><span data-stu-id="a7701-356">Marking the key as exportable is optional.</span></span> <span data-ttu-id="a7701-357">Anote la huella digital del certificado, que se usa más adelante en este proceso.</span><span class="sxs-lookup"><span data-stu-id="a7701-357">Note the certificate's thumbprint, which is used later in this process.</span></span>
1. <span data-ttu-id="a7701-358">Exporte el certificado de archivo PKCS # 12 (*. pfx*) como un certificado codificado en der (*. cer*).</span><span class="sxs-lookup"><span data-stu-id="a7701-358">Export the PKCS#12 archive (*.pfx*) certificate as a DER-encoded certificate (*.cer*).</span></span>
1. <span data-ttu-id="a7701-359">Registre la aplicación con Azure AD (**registros de aplicaciones**).</span><span class="sxs-lookup"><span data-stu-id="a7701-359">Register the app with Azure AD (**App registrations**).</span></span>
1. <span data-ttu-id="a7701-360">Cargue el certificado codificado en DER (*. cer*) en Azure ad:</span><span class="sxs-lookup"><span data-stu-id="a7701-360">Upload the DER-encoded certificate (*.cer*) to Azure AD:</span></span>
   1. <span data-ttu-id="a7701-361">Seleccione la aplicación en Azure AD.</span><span class="sxs-lookup"><span data-stu-id="a7701-361">Select the app in Azure AD.</span></span>
   1. <span data-ttu-id="a7701-362">Vaya a **certificados & Secrets**.</span><span class="sxs-lookup"><span data-stu-id="a7701-362">Navigate to **Certificates & secrets**.</span></span>
   1. <span data-ttu-id="a7701-363">Seleccione **upload Certificate (cargar certificado** ) para cargar el certificado, que contiene la clave pública.</span><span class="sxs-lookup"><span data-stu-id="a7701-363">Select **Upload certificate** to upload the certificate, which contains the public key.</span></span> <span data-ttu-id="a7701-364">Un certificado *. cer*, *. pem* o *. CRT* es aceptable.</span><span class="sxs-lookup"><span data-stu-id="a7701-364">A *.cer*, *.pem*, or *.crt* certificate is acceptable.</span></span>
1. <span data-ttu-id="a7701-365">Almacene el nombre del almacén de claves, el identificador de la aplicación y la huella digital del certificado en el archivo de la aplicación *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="a7701-365">Store the key vault name, Application ID, and certificate thumbprint in the app's *appsettings.json* file.</span></span>
1. <span data-ttu-id="a7701-366">Vaya a **almacenes de claves** en el Azure portal.</span><span class="sxs-lookup"><span data-stu-id="a7701-366">Navigate to **Key vaults** in the Azure portal.</span></span>
1. <span data-ttu-id="a7701-367">Seleccione el almacén de claves que creó en el [almacenamiento de secretos en el entorno de producción con Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) sección.</span><span class="sxs-lookup"><span data-stu-id="a7701-367">Select the key vault that you created in the [Secret storage in the Production environment with Azure Key Vault](#secret-storage-in-the-production-environment-with-azure-key-vault) section.</span></span>
1. <span data-ttu-id="a7701-368">Seleccione **Directivas de acceso**.</span><span class="sxs-lookup"><span data-stu-id="a7701-368">Select **Access policies**.</span></span>
1. <span data-ttu-id="a7701-369">Seleccione **Agregar directiva de acceso**.</span><span class="sxs-lookup"><span data-stu-id="a7701-369">Select **Add Access Policy**.</span></span>
1. <span data-ttu-id="a7701-370">Abra los **permisos de secreto** y proporcione la aplicación con los permisos **Get** y **List** .</span><span class="sxs-lookup"><span data-stu-id="a7701-370">Open **Secret permissions** and provide the app with **Get** and **List** permissions.</span></span>
1. <span data-ttu-id="a7701-371">Seleccione **seleccionar entidad** de seguridad y seleccione la aplicación registrada por nombre.</span><span class="sxs-lookup"><span data-stu-id="a7701-371">Select **Select principal** and select the registered app by name.</span></span> <span data-ttu-id="a7701-372">Seleccione el botón **Seleccionar**.</span><span class="sxs-lookup"><span data-stu-id="a7701-372">Select the **Select** button.</span></span>
1. <span data-ttu-id="a7701-373">Seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="a7701-373">Select **OK**.</span></span>
1. <span data-ttu-id="a7701-374">Seleccione **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="a7701-374">Select **Save**.</span></span>
1. <span data-ttu-id="a7701-375">Implemente la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a7701-375">Deploy the app.</span></span>

<span data-ttu-id="a7701-376">La `Certificate` aplicación de ejemplo obtiene sus valores de configuración de `IConfigurationRoot` con el mismo nombre que el secreto:</span><span class="sxs-lookup"><span data-stu-id="a7701-376">The `Certificate` sample app obtains its configuration values from `IConfigurationRoot` with the same name as the secret name:</span></span>

* <span data-ttu-id="a7701-377">Valores no jerárquicos: el valor de `SecretName` se obtiene con `config["SecretName"]` .</span><span class="sxs-lookup"><span data-stu-id="a7701-377">Non-hierarchical values: The value for `SecretName` is obtained with `config["SecretName"]`.</span></span>
* <span data-ttu-id="a7701-378">Valores jerárquicos (secciones): notación de uso `:` (dos puntos) o el `GetSection` método de extensión.</span><span class="sxs-lookup"><span data-stu-id="a7701-378">Hierarchical values (sections): Use `:` (colon) notation or the `GetSection` extension method.</span></span> <span data-ttu-id="a7701-379">Use cualquiera de estos métodos para obtener el valor de configuración:</span><span class="sxs-lookup"><span data-stu-id="a7701-379">Use either of these approaches to obtain the configuration value:</span></span>
  * `config["Section:SecretName"]`
  * `config.GetSection("Section")["SecretName"]`

<span data-ttu-id="a7701-380">El certificado X. 509 se administra mediante el sistema operativo.</span><span class="sxs-lookup"><span data-stu-id="a7701-380">The X.509 certificate is managed by the OS.</span></span> <span data-ttu-id="a7701-381">La aplicación llama a <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> con los valores proporcionados por el *appsettings.json* archivo:</span><span class="sxs-lookup"><span data-stu-id="a7701-381">The app calls <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> with values supplied by the *appsettings.json* file:</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=20-23)]

<span data-ttu-id="a7701-382">Valores de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="a7701-382">Example values:</span></span>

* <span data-ttu-id="a7701-383">Nombre del almacén de claves: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="a7701-383">Key vault name: `contosovault`</span></span>
* <span data-ttu-id="a7701-384">IDENTIFICADOR de la aplicación: `627e911e-43cc-61d4-992e-12db9c81b413`</span><span class="sxs-lookup"><span data-stu-id="a7701-384">Application ID: `627e911e-43cc-61d4-992e-12db9c81b413`</span></span>
* <span data-ttu-id="a7701-385">Huella digital del certificado: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span><span class="sxs-lookup"><span data-stu-id="a7701-385">Certificate thumbprint: `fe14593dd66b2406c5269d742d04b6e1ab03adb1`</span></span>

<span data-ttu-id="a7701-386">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="a7701-386">*appsettings.json*:</span></span>

[!code-json[](key-vault-configuration/samples/2.x/SampleApp/appsettings.json?highlight=10-12)]

<span data-ttu-id="a7701-387">Al ejecutar la aplicación, se muestran los valores de secreto cargados en una página web.</span><span class="sxs-lookup"><span data-stu-id="a7701-387">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="a7701-388">En el entorno de desarrollo, los valores secretos se cargan con el `_dev` sufijo.</span><span class="sxs-lookup"><span data-stu-id="a7701-388">In the Development environment, secret values load with the `_dev` suffix.</span></span> <span data-ttu-id="a7701-389">En el entorno de producción, los valores se cargan con el `_prod` sufijo.</span><span class="sxs-lookup"><span data-stu-id="a7701-389">In the Production environment, the values load with the `_prod` suffix.</span></span>

## <a name="use-managed-identities-for-azure-resources"></a><span data-ttu-id="a7701-390">Uso de identidades administradas para los recursos de Azure</span><span class="sxs-lookup"><span data-stu-id="a7701-390">Use Managed identities for Azure resources</span></span>

<span data-ttu-id="a7701-391">**Una aplicación implementada en Azure** puede aprovechar las [identidades administradas para los recursos de Azure](/azure/active-directory/managed-identities-azure-resources/overview), lo que permite que la aplicación se autentique con Azure Key Vault mediante la autenticación de Azure ad sin credenciales (identificador de aplicación y contraseña/secreto de cliente) almacenadas en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a7701-391">**An app deployed to Azure** can take advantage of [Managed identities for Azure resources](/azure/active-directory/managed-identities-azure-resources/overview), which allows the app to authenticate with Azure Key Vault using Azure AD authentication without credentials (Application ID and Password/Client Secret) stored in the app.</span></span>

<span data-ttu-id="a7701-392">La aplicación de ejemplo usa identidades administradas para los recursos de Azure cuando la `#define` instrucción que se encuentra en la parte superior del archivo *Program.CS* se establece en `Managed` .</span><span class="sxs-lookup"><span data-stu-id="a7701-392">The sample app uses Managed identities for Azure resources when the `#define` statement at the top of the *Program.cs* file is set to `Managed`.</span></span>

<span data-ttu-id="a7701-393">Escriba el nombre del almacén en el archivo de la aplicación *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="a7701-393">Enter the vault name into the app's *appsettings.json* file.</span></span> <span data-ttu-id="a7701-394">La aplicación de ejemplo no requiere un identificador de aplicación y una contraseña (secreto de cliente) cuando se establece en la `Managed` versión, por lo que puede omitir esas entradas de configuración.</span><span class="sxs-lookup"><span data-stu-id="a7701-394">The sample app doesn't require an Application ID and Password (Client Secret) when set to the `Managed` version, so you can ignore those configuration entries.</span></span> <span data-ttu-id="a7701-395">La aplicación se implementa en Azure y Azure autentica la aplicación para acceder a Azure Key Vault solo mediante el nombre del almacén almacenado en el *appsettings.json* archivo.</span><span class="sxs-lookup"><span data-stu-id="a7701-395">The app is deployed to Azure, and Azure authenticates the app to access Azure Key Vault only using the vault name stored in the *appsettings.json* file.</span></span>

<span data-ttu-id="a7701-396">Implemente la aplicación de ejemplo en Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="a7701-396">Deploy the sample app to Azure App Service.</span></span>

<span data-ttu-id="a7701-397">Una aplicación implementada en Azure App Service se registra automáticamente con Azure AD cuando se crea el servicio.</span><span class="sxs-lookup"><span data-stu-id="a7701-397">An app deployed to Azure App Service is automatically registered with Azure AD when the service is created.</span></span> <span data-ttu-id="a7701-398">Obtenga el identificador de objeto de la implementación para usarlo en el siguiente comando.</span><span class="sxs-lookup"><span data-stu-id="a7701-398">Obtain the Object ID from the deployment for use in the following command.</span></span> <span data-ttu-id="a7701-399">El identificador de objeto se muestra en el Azure Portal en el **Identity** Panel de la App Service.</span><span class="sxs-lookup"><span data-stu-id="a7701-399">The Object ID is shown in the Azure portal on the **Identity** panel of the App Service.</span></span>

<span data-ttu-id="a7701-400">Con CLI de Azure y el identificador de objeto de la aplicación, proporcione la aplicación con los `list` `get` permisos y para acceder al almacén de claves:</span><span class="sxs-lookup"><span data-stu-id="a7701-400">Using Azure CLI and the app's Object ID, provide the app with `list` and `get` permissions to access the key vault:</span></span>

```azurecli
az keyvault set-policy --name {KEY VAULT NAME} --object-id {OBJECT ID} --secret-permissions get list
```

<span data-ttu-id="a7701-401">**Reinicie la aplicación** con CLI de Azure, PowerShell o el Azure portal.</span><span class="sxs-lookup"><span data-stu-id="a7701-401">**Restart the app** using Azure CLI, PowerShell, or the Azure portal.</span></span>

<span data-ttu-id="a7701-402">La aplicación de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="a7701-402">The sample app:</span></span>

* <span data-ttu-id="a7701-403">Crea una instancia de la `AzureServiceTokenProvider` clase sin una cadena de conexión.</span><span class="sxs-lookup"><span data-stu-id="a7701-403">Creates an instance of the `AzureServiceTokenProvider` class without a connection string.</span></span> <span data-ttu-id="a7701-404">Cuando no se proporciona una cadena de conexión, el proveedor intenta obtener un token de acceso de identidades administradas para los recursos de Azure.</span><span class="sxs-lookup"><span data-stu-id="a7701-404">When a connection string isn't provided, the provider attempts to obtain an access token from Managed identities for Azure resources.</span></span>
* <span data-ttu-id="a7701-405"><xref:Microsoft.Azure.KeyVault.KeyVaultClient>Se crea un nuevo con la `AzureServiceTokenProvider` devolución de llamada del token de instancia.</span><span class="sxs-lookup"><span data-stu-id="a7701-405">A new <xref:Microsoft.Azure.KeyVault.KeyVaultClient> is created with the `AzureServiceTokenProvider` instance token callback.</span></span>
* <span data-ttu-id="a7701-406">La <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instancia se utiliza con una implementación predeterminada de <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> que carga todos los valores secretos y reemplaza los dobles guiones ( `--` ) por dos puntos ( `:` ) en los nombres de clave.</span><span class="sxs-lookup"><span data-stu-id="a7701-406">The <xref:Microsoft.Azure.KeyVault.KeyVaultClient> instance is used with a default implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> that loads all secret values and replaces double-dashes (`--`) with colons (`:`) in key names.</span></span>

[!code-csharp[](key-vault-configuration/samples/2.x/SampleApp/Program.cs?name=snippet2&highlight=13-21)]

<span data-ttu-id="a7701-407">Valor de ejemplo de nombre del almacén de claves: `contosovault`</span><span class="sxs-lookup"><span data-stu-id="a7701-407">Key vault name example value: `contosovault`</span></span>
    
<span data-ttu-id="a7701-408">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="a7701-408">*appsettings.json*:</span></span>

```json
{
  "KeyVaultName": "Key Vault Name"
}
```

<span data-ttu-id="a7701-409">Al ejecutar la aplicación, se muestran los valores de secreto cargados en una página web.</span><span class="sxs-lookup"><span data-stu-id="a7701-409">When you run the app, a webpage shows the loaded secret values.</span></span> <span data-ttu-id="a7701-410">En el entorno de desarrollo, los valores secretos tienen el `_dev` sufijo porque son proporcionados por los secretos del usuario.</span><span class="sxs-lookup"><span data-stu-id="a7701-410">In the Development environment, secret values have the `_dev` suffix because they're provided by User Secrets.</span></span> <span data-ttu-id="a7701-411">En el entorno de producción, los valores se cargan con el `_prod` sufijo porque son proporcionados por Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="a7701-411">In the Production environment, the values load with the `_prod` suffix because they're provided by Azure Key Vault.</span></span>

<span data-ttu-id="a7701-412">Si recibe un `Access denied` error, confirme que la aplicación está registrada con Azure ad y que proporciona acceso al almacén de claves.</span><span class="sxs-lookup"><span data-stu-id="a7701-412">If you receive an `Access denied` error, confirm that the app is registered with Azure AD and provided access to the key vault.</span></span> <span data-ttu-id="a7701-413">Confirme que ha reiniciado el servicio en Azure.</span><span class="sxs-lookup"><span data-stu-id="a7701-413">Confirm that you've restarted the service in Azure.</span></span>

<span data-ttu-id="a7701-414">Para obtener información sobre el uso del proveedor con una identidad administrada y una canalización DevOps de Azure, consulte [creación de una conexión de servicio Azure Resource Manager a una máquina virtual con una identidad de servicio administrada](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span><span class="sxs-lookup"><span data-stu-id="a7701-414">For information on using the provider with a managed identity and an Azure DevOps pipeline, see [Create an Azure Resource Manager service connection to a VM with a managed service identity](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-to-a-vm-with-a-managed-service-identity).</span></span>

## <a name="use-a-key-name-prefix"></a><span data-ttu-id="a7701-415">Usar un prefijo de nombre de clave</span><span class="sxs-lookup"><span data-stu-id="a7701-415">Use a key name prefix</span></span>

<span data-ttu-id="a7701-416"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> proporciona una sobrecarga que acepta una implementación de <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> , que permite controlar cómo se convierten los secretos del almacén de claves en claves de configuración.</span><span class="sxs-lookup"><span data-stu-id="a7701-416"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> provides an overload that accepts an implementation of <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>, which allows you to control how key vault secrets are converted into configuration keys.</span></span> <span data-ttu-id="a7701-417">Por ejemplo, puede implementar la interfaz para cargar valores de secreto basados en un valor de prefijo que se proporciona al inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a7701-417">For example, you can implement the interface to load secret values based on a prefix value you provide at app startup.</span></span> <span data-ttu-id="a7701-418">Esto le permite, por ejemplo, cargar secretos en función de la versión de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a7701-418">This allows you, for example, to load secrets based on the version of the app.</span></span>

> [!WARNING]
> <span data-ttu-id="a7701-419">No use prefijos en secretos del almacén de claves para colocar secretos para varias aplicaciones en el mismo almacén de claves o para colocar secretos de entorno (por ejemplo, *desarrollo* frente a secretos de *producción* ) en el mismo almacén.</span><span class="sxs-lookup"><span data-stu-id="a7701-419">Don't use prefixes on key vault secrets to place secrets for multiple apps into the same key vault or to place environmental secrets (for example, *development* versus *production* secrets) into the same vault.</span></span> <span data-ttu-id="a7701-420">Se recomienda que las distintas aplicaciones y entornos de desarrollo y producción usen almacenes de claves independientes para aislar los entornos de aplicación para obtener el máximo nivel de seguridad.</span><span class="sxs-lookup"><span data-stu-id="a7701-420">We recommend that different apps and development/production environments use separate key vaults to isolate app environments for the highest level of security.</span></span>

<span data-ttu-id="a7701-421">En el siguiente ejemplo, se establece un secreto en el almacén de claves (y el uso de la herramienta Administrador de secretos para el entorno de desarrollo) para `5000-AppSecret` (no se permiten puntos en los nombres de secreto del almacén de claves).</span><span class="sxs-lookup"><span data-stu-id="a7701-421">In the following example, a secret is established in the key vault (and using the Secret Manager tool for the Development environment) for `5000-AppSecret` (periods aren't allowed in key vault secret names).</span></span> <span data-ttu-id="a7701-422">Este secreto representa un secreto de aplicación para la versión 5.0.0.0 de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a7701-422">This secret represents an app secret for version 5.0.0.0 of the app.</span></span> <span data-ttu-id="a7701-423">Para otra versión de la aplicación, 5.1.0.0, se agrega un secreto al almacén de claves (y con la herramienta de administración de secretos) para `5100-AppSecret` .</span><span class="sxs-lookup"><span data-stu-id="a7701-423">For another version of the app, 5.1.0.0, a secret is added to the key vault (and using the Secret Manager tool) for `5100-AppSecret`.</span></span> <span data-ttu-id="a7701-424">Cada versión de la aplicación carga su valor de secreto con versión en su configuración como, lo que `AppSecret` elimina la versión mientras carga el secreto.</span><span class="sxs-lookup"><span data-stu-id="a7701-424">Each app version loads its versioned secret value into its configuration as `AppSecret`, stripping off the version as it loads the secret.</span></span>

<span data-ttu-id="a7701-425"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> se llama a con un personalizado <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> :</span><span class="sxs-lookup"><span data-stu-id="a7701-425"><xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> is called with a custom <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager>:</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Program.cs)]

<span data-ttu-id="a7701-426">La <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementación reacciona a los prefijos de versión de los secretos para cargar el secreto adecuado en la configuración:</span><span class="sxs-lookup"><span data-stu-id="a7701-426">The <xref:Microsoft.Extensions.Configuration.AzureKeyVault.IKeyVaultSecretManager> implementation reacts to the version prefixes of secrets to load the proper secret into configuration:</span></span>

* <span data-ttu-id="a7701-427">`Load` carga un secreto cuando su nombre comienza con el prefijo.</span><span class="sxs-lookup"><span data-stu-id="a7701-427">`Load` loads a secret when its name starts with the prefix.</span></span> <span data-ttu-id="a7701-428">No se cargan otros secretos.</span><span class="sxs-lookup"><span data-stu-id="a7701-428">Other secrets aren't loaded.</span></span>
* <span data-ttu-id="a7701-429">`GetKey`:</span><span class="sxs-lookup"><span data-stu-id="a7701-429">`GetKey`:</span></span>
  * <span data-ttu-id="a7701-430">Quita el prefijo del nombre del secreto.</span><span class="sxs-lookup"><span data-stu-id="a7701-430">Removes the prefix from the secret name.</span></span>
  * <span data-ttu-id="a7701-431">Reemplaza dos guiones en cualquier nombre por `KeyDelimiter` , que es el delimitador usado en la configuración (normalmente un signo de dos puntos).</span><span class="sxs-lookup"><span data-stu-id="a7701-431">Replaces two dashes in any name with the `KeyDelimiter`, which is the delimiter used in configuration (usually a colon).</span></span> <span data-ttu-id="a7701-432">Azure Key Vault no permite un signo de dos puntos en los nombres de secreto.</span><span class="sxs-lookup"><span data-stu-id="a7701-432">Azure Key Vault doesn't allow a colon in secret names.</span></span>

[!code-csharp[](key-vault-configuration/samples_snapshot/Startup.cs)]

<span data-ttu-id="a7701-433">`Load`Un algoritmo de proveedor llama al método que recorre en iteración los secretos del almacén para encontrar los que tienen el prefijo de versión.</span><span class="sxs-lookup"><span data-stu-id="a7701-433">The `Load` method is called by a provider algorithm that iterates through the vault secrets to find the ones that have the version prefix.</span></span> <span data-ttu-id="a7701-434">Cuando se encuentra un prefijo de versión con `Load` , el algoritmo utiliza el `GetKey` método para devolver el nombre de configuración del nombre de secreto.</span><span class="sxs-lookup"><span data-stu-id="a7701-434">When a version prefix is found with `Load`, the algorithm uses the `GetKey` method to return the configuration name of the secret name.</span></span> <span data-ttu-id="a7701-435">Elimina el prefijo de versión del nombre del secreto y devuelve el resto del nombre de secreto para cargarlo en los pares de nombre y valor de configuración de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a7701-435">It strips off the version prefix from the secret's name and returns the rest of the secret name for loading into the app's configuration name-value pairs.</span></span>

<span data-ttu-id="a7701-436">Cuando se implementa este enfoque:</span><span class="sxs-lookup"><span data-stu-id="a7701-436">When this approach is implemented:</span></span>

1. <span data-ttu-id="a7701-437">La versión de la aplicación especificada en el archivo de proyecto de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a7701-437">The app's version specified in the app's project file.</span></span> <span data-ttu-id="a7701-438">En el ejemplo siguiente, la versión de la aplicación se establece en `5.0.0.0` :</span><span class="sxs-lookup"><span data-stu-id="a7701-438">In the following example, the app's version is set to `5.0.0.0`:</span></span>

   ```xml
   <PropertyGroup>
     <Version>5.0.0.0</Version>
   </PropertyGroup>
   ```

1. <span data-ttu-id="a7701-439">Confirme que una `<UserSecretsId>` propiedad está presente en el archivo de proyecto de la aplicación, donde `{GUID}` es un GUID proporcionado por el usuario:</span><span class="sxs-lookup"><span data-stu-id="a7701-439">Confirm that a `<UserSecretsId>` property is present in the app's project file, where `{GUID}` is a user-supplied GUID:</span></span>

   ```xml
   <PropertyGroup>
     <UserSecretsId>{GUID}</UserSecretsId>
   </PropertyGroup>
   ```

   <span data-ttu-id="a7701-440">Guarde los siguientes secretos localmente con la [herramienta de administración de secretos](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="a7701-440">Save the following secrets locally with the [Secret Manager tool](xref:security/app-secrets):</span></span>

   ```dotnetcli
   dotnet user-secrets set "5000-AppSecret" "5.0.0.0_secret_value_dev"
   dotnet user-secrets set "5100-AppSecret" "5.1.0.0_secret_value_dev"
   ```

1. <span data-ttu-id="a7701-441">Los secretos se guardan en Azure Key Vault mediante los siguientes comandos de CLI de Azure:</span><span class="sxs-lookup"><span data-stu-id="a7701-441">Secrets are saved in Azure Key Vault using the following Azure CLI commands:</span></span>

   ```azurecli
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5000-AppSecret" --value "5.0.0.0_secret_value_prod"
   az keyvault secret set --vault-name {KEY VAULT NAME} --name "5100-AppSecret" --value "5.1.0.0_secret_value_prod"
   ```

1. <span data-ttu-id="a7701-442">Cuando se ejecuta la aplicación, se cargan los secretos del almacén de claves.</span><span class="sxs-lookup"><span data-stu-id="a7701-442">When the app is run, the key vault secrets are loaded.</span></span> <span data-ttu-id="a7701-443">El secreto de cadena de `5000-AppSecret` se corresponde con la versión de la aplicación especificada en el archivo de proyecto de la aplicación ( `5.0.0.0` ).</span><span class="sxs-lookup"><span data-stu-id="a7701-443">The string secret for `5000-AppSecret` is matched to the app's version specified in the app's project file (`5.0.0.0`).</span></span>

1. <span data-ttu-id="a7701-444">La versión, `5000` (con el guion), se elimina del nombre de la clave.</span><span class="sxs-lookup"><span data-stu-id="a7701-444">The version, `5000` (with the dash), is stripped from the key name.</span></span> <span data-ttu-id="a7701-445">En toda la aplicación, la lectura de la configuración con la clave `AppSecret` carga el valor del secreto.</span><span class="sxs-lookup"><span data-stu-id="a7701-445">Throughout the app, reading configuration with the key `AppSecret` loads the secret value.</span></span>

1. <span data-ttu-id="a7701-446">Si se cambia la versión de la aplicación en el archivo de proyecto a `5.1.0.0` y se vuelve a ejecutar la aplicación, el valor secreto devuelto está `5.1.0.0_secret_value_dev` en el entorno de desarrollo y `5.1.0.0_secret_value_prod` en producción.</span><span class="sxs-lookup"><span data-stu-id="a7701-446">If the app's version is changed in the project file to `5.1.0.0` and the app is run again, the secret value returned is `5.1.0.0_secret_value_dev` in the Development environment and `5.1.0.0_secret_value_prod` in Production.</span></span>

> [!NOTE]
> <span data-ttu-id="a7701-447">También puede proporcionar su propia <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementación a <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*> .</span><span class="sxs-lookup"><span data-stu-id="a7701-447">You can also provide your own <xref:Microsoft.Azure.KeyVault.KeyVaultClient> implementation to <xref:Microsoft.Extensions.Configuration.AzureKeyVaultConfigurationExtensions.AddAzureKeyVault*>.</span></span> <span data-ttu-id="a7701-448">Un cliente personalizado permite compartir una única instancia del cliente a través de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a7701-448">A custom client permits sharing a single instance of the client across the app.</span></span>

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="a7701-449">Enlace de una matriz a una clase</span><span class="sxs-lookup"><span data-stu-id="a7701-449">Bind an array to a class</span></span>

<span data-ttu-id="a7701-450">El proveedor es capaz de leer valores de configuración en una matriz para enlazar a una matriz POCO.</span><span class="sxs-lookup"><span data-stu-id="a7701-450">The provider is capable of reading configuration values into an array for binding to a POCO array.</span></span>

<span data-ttu-id="a7701-451">Al leer desde un origen de configuración que permite que las claves contengan separadores de dos puntos ( `:` ), se usa un segmento de clave numérica para distinguir las claves que componen una matriz ( `:0:` , `:1:` , &hellip; `:{n}:` ).</span><span class="sxs-lookup"><span data-stu-id="a7701-451">When reading from a configuration source that allows keys to contain colon (`:`) separators, a numeric key segment is used to distinguish the keys that make up an array (`:0:`, `:1:`, &hellip; `:{n}:`).</span></span> <span data-ttu-id="a7701-452">Para obtener más información, vea [Configuración: enlazar una matriz a una clase](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span><span class="sxs-lookup"><span data-stu-id="a7701-452">For more information, see [Configuration: Bind an array to a class](xref:fundamentals/configuration/index#bind-an-array-to-a-class).</span></span>

<span data-ttu-id="a7701-453">Azure Key Vault teclas no pueden usar un signo de dos puntos como separador.</span><span class="sxs-lookup"><span data-stu-id="a7701-453">Azure Key Vault keys can't use a colon as a separator.</span></span> <span data-ttu-id="a7701-454">El enfoque que se describe en este tema utiliza los guiones dobles ( `--` ) como separador de valores jerárquicos (secciones).</span><span class="sxs-lookup"><span data-stu-id="a7701-454">The approach described in this topic uses double dashes (`--`) as a separator for hierarchical values (sections).</span></span> <span data-ttu-id="a7701-455">Las claves de matriz se almacenan en Azure Key Vault con dobles guiones y segmentos de clave numéricos ( `--0--` , `--1--` , &hellip; `--{n}--` ).</span><span class="sxs-lookup"><span data-stu-id="a7701-455">Array keys are stored in Azure Key Vault with double dashes and numeric key segments (`--0--`, `--1--`, &hellip; `--{n}--`).</span></span>

<span data-ttu-id="a7701-456">Examine la siguiente configuración de proveedor de registro de [Serilog](https://serilog.net/) proporcionada por un archivo JSON.</span><span class="sxs-lookup"><span data-stu-id="a7701-456">Examine the following [Serilog](https://serilog.net/) logging provider configuration provided by a JSON file.</span></span> <span data-ttu-id="a7701-457">Hay dos literales de objeto definidos en la `WriteTo` matriz que reflejan dos *receptores* de Serilog, que describen los destinos para el registro de la salida:</span><span class="sxs-lookup"><span data-stu-id="a7701-457">There are two object literals defined in the `WriteTo` array that reflect two Serilog *sinks*, which describe destinations for logging output:</span></span>

```json
"Serilog": {
  "WriteTo": [
    {
      "Name": "AzureTableStorage",
      "Args": {
        "storageTableName": "logs",
        "connectionString": "DefaultEnd...ountKey=Eby8...GMGw=="
      }
    },
    {
      "Name": "AzureDocumentDB",
      "Args": {
        "endpointUrl": "https://contoso.documents.azure.com:443",
        "authorizationKey": "Eby8...GMGw=="
      }
    }
  ]
}
```

<span data-ttu-id="a7701-458">La configuración que se muestra en el archivo JSON anterior se almacena en Azure Key Vault mediante la notación de doble guión ( `--` ) y los segmentos numéricos:</span><span class="sxs-lookup"><span data-stu-id="a7701-458">The configuration shown in the preceding JSON file is stored in Azure Key Vault using double dash (`--`) notation and numeric segments:</span></span>

| <span data-ttu-id="a7701-459">Clave</span><span class="sxs-lookup"><span data-stu-id="a7701-459">Key</span></span> | <span data-ttu-id="a7701-460">Valor</span><span class="sxs-lookup"><span data-stu-id="a7701-460">Value</span></span> |
| --- | ----- |
| `Serilog--WriteTo--0--Name` | `AzureTableStorage` |
| `Serilog--WriteTo--0--Args--storageTableName` | `logs` |
| `Serilog--WriteTo--0--Args--connectionString` | `DefaultEnd...ountKey=Eby8...GMGw==` |
| `Serilog--WriteTo--1--Name` | `AzureDocumentDB` |
| `Serilog--WriteTo--1--Args--endpointUrl` | `https://contoso.documents.azure.com:443` |
| `Serilog--WriteTo--1--Args--authorizationKey` | `Eby8...GMGw==` |

## <a name="reload-secrets"></a><span data-ttu-id="a7701-461">Volver a cargar los secretos</span><span class="sxs-lookup"><span data-stu-id="a7701-461">Reload secrets</span></span>

<span data-ttu-id="a7701-462">Los secretos se almacenan en caché hasta que `IConfigurationRoot.Reload()` se llama a.</span><span class="sxs-lookup"><span data-stu-id="a7701-462">Secrets are cached until `IConfigurationRoot.Reload()` is called.</span></span> <span data-ttu-id="a7701-463">La aplicación no respeta los secretos expirados, deshabilitados y actualizados en el almacén de claves hasta que `Reload` se ejecute.</span><span class="sxs-lookup"><span data-stu-id="a7701-463">Expired, disabled, and updated secrets in the key vault are not respected by the app until `Reload` is executed.</span></span>

```csharp
Configuration.Reload();
```

## <a name="disabled-and-expired-secrets"></a><span data-ttu-id="a7701-464">Secretos deshabilitados y expirados</span><span class="sxs-lookup"><span data-stu-id="a7701-464">Disabled and expired secrets</span></span>

<span data-ttu-id="a7701-465">Los secretos deshabilitados y caducados producen una <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException> .</span><span class="sxs-lookup"><span data-stu-id="a7701-465">Disabled and expired secrets throw a <xref:Microsoft.Azure.KeyVault.Models.KeyVaultErrorException>.</span></span> <span data-ttu-id="a7701-466">Para evitar que se inicie la aplicación, proporcione la configuración mediante un proveedor de configuración diferente o actualice el secreto deshabilitado o expirado.</span><span class="sxs-lookup"><span data-stu-id="a7701-466">To prevent the app from throwing, provide the configuration using a different configuration provider or update the disabled or expired secret.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="a7701-467">Solución de problemas</span><span class="sxs-lookup"><span data-stu-id="a7701-467">Troubleshoot</span></span>

<span data-ttu-id="a7701-468">Cuando la aplicación no carga la configuración mediante el proveedor, se escribe un mensaje de error en la [infraestructura de registro de ASP.net Core](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="a7701-468">When the app fails to load configuration using the provider, an error message is written to the [ASP.NET Core Logging infrastructure](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="a7701-469">Las siguientes condiciones impedirán que se cargue la configuración:</span><span class="sxs-lookup"><span data-stu-id="a7701-469">The following conditions will prevent configuration from loading:</span></span>

* <span data-ttu-id="a7701-470">La aplicación o el certificado no están configurados correctamente en Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="a7701-470">The app or certificate isn't configured correctly in Azure Active Directory.</span></span>
* <span data-ttu-id="a7701-471">El almacén de claves no existe en Azure Key Vault.</span><span class="sxs-lookup"><span data-stu-id="a7701-471">The key vault doesn't exist in Azure Key Vault.</span></span>
* <span data-ttu-id="a7701-472">La aplicación no está autorizada para acceder al almacén de claves.</span><span class="sxs-lookup"><span data-stu-id="a7701-472">The app isn't authorized to access the key vault.</span></span>
* <span data-ttu-id="a7701-473">La Directiva de acceso no `Get` incluye `List` los permisos y.</span><span class="sxs-lookup"><span data-stu-id="a7701-473">The access policy doesn't include `Get` and `List` permissions.</span></span>
* <span data-ttu-id="a7701-474">En el almacén de claves, los datos de configuración (par nombre-valor) tienen un nombre incorrecto, faltan, están deshabilitados o han expirado.</span><span class="sxs-lookup"><span data-stu-id="a7701-474">In the key vault, the configuration data (name-value pair) is incorrectly named, missing, disabled, or expired.</span></span>
* <span data-ttu-id="a7701-475">La aplicación tiene un nombre de almacén de claves incorrecto ( `KeyVaultName` ), Azure ad ID. de la aplicación ( `AzureADApplicationId` ) o Azure ad huella digital del certificado ( `AzureADCertThumbprint` ).</span><span class="sxs-lookup"><span data-stu-id="a7701-475">The app has the wrong key vault name (`KeyVaultName`), Azure AD Application Id (`AzureADApplicationId`), or Azure AD certificate thumbprint (`AzureADCertThumbprint`).</span></span>
* <span data-ttu-id="a7701-476">La clave de configuración (nombre) es incorrecta en la aplicación para el valor que está intentando cargar.</span><span class="sxs-lookup"><span data-stu-id="a7701-476">The configuration key (name) is incorrect in the app for the value you're trying to load.</span></span>
* <span data-ttu-id="a7701-477">Al agregar la Directiva de acceso de la aplicación al almacén de claves, la Directiva se creó, pero no se seleccionó el botón **Guardar** en la interfaz de usuario de **directivas de acceso** .</span><span class="sxs-lookup"><span data-stu-id="a7701-477">When adding the access policy for the app to the key vault, the policy was created, but the **Save** button wasn't selected in the **Access policies** UI.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a7701-478">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="a7701-478">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="a7701-479">Microsoft Azure: Key Vault</span><span class="sxs-lookup"><span data-stu-id="a7701-479">Microsoft Azure: Key Vault</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="a7701-480">Microsoft Azure: documentación de Key Vault</span><span class="sxs-lookup"><span data-stu-id="a7701-480">Microsoft Azure: Key Vault Documentation</span></span>](/azure/key-vault/)
* [<span data-ttu-id="a7701-481">Cómo generar y transferir claves protegidas con HSM para Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="a7701-481">How to generate and transfer HSM-protected keys for Azure Key Vault</span></span>](/azure/key-vault/key-vault-hsm-protected-keys)
* [<span data-ttu-id="a7701-482">Clase KeyVaultClient</span><span class="sxs-lookup"><span data-stu-id="a7701-482">KeyVaultClient Class</span></span>](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)
* [<span data-ttu-id="a7701-483">Guía de inicio rápido: Establecimiento y recuperación de un secreto de Azure Key Vault mediante una aplicación web de .NET</span><span class="sxs-lookup"><span data-stu-id="a7701-483">Quickstart: Set and retrieve a secret from Azure Key Vault by using a .NET web app</span></span>](/azure/key-vault/quick-create-net)
* [<span data-ttu-id="a7701-484">Tutorial: Uso de Azure Key Vault con una máquina virtual Windows en .NET</span><span class="sxs-lookup"><span data-stu-id="a7701-484">Tutorial: How to use Azure Key Vault with Azure Windows Virtual Machine in .NET</span></span>](/azure/key-vault/tutorial-net-windows-virtual-machine)

::: moniker-end
