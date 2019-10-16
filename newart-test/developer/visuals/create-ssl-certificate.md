---
title: Creación de un certificado SSL
description: Instrucciones de la solución alternativa para crear certificados manualmente en el servidor para desarrolladores
author: KesemSharabi
ms.author: kesharab
manager: rkarlin
ms.reviewer: sranins
ms.service: powerbi
ms.subservice: powerbi-custom-visuals
ms.topic: tutorial
ms.date: 06/18/2019
ms.openlocfilehash: c96489e6577f4887d2f22a9e81ea50f46cc9a5a3
ms.sourcegitcommit: a1c994bc8fa5f072fce13a6f35079e87d45f00f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/16/2019
ms.locfileid: "72424278"
---
# <a name="create-an-ssl-certificate"></a><span data-ttu-id="eadbe-103">Creación de un certificado SSL</span><span class="sxs-lookup"><span data-stu-id="eadbe-103">Create an SSL certificate</span></span>

<span data-ttu-id="eadbe-104">En este artículo se describe cómo crear un certificado SSL.</span><span class="sxs-lookup"><span data-stu-id="eadbe-104">This article describes how to create an SSL certificate.</span></span>

<span data-ttu-id="eadbe-105">Para generar el certificado mediante el cmdlet `New-SelfSignedCertificate` de PowerShell en Windows 8 o versiones posteriores, ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="eadbe-105">To generate the certificate by using the PowerShell `New-SelfSignedCertificate` cmdlet on Windows 8 or later, run the following command:</span></span>

```cmd
pbiviz --create-cert
```

<span data-ttu-id="eadbe-106">La herramienta requiere una instalación de OpenSSL para Windows 7.</span><span class="sxs-lookup"><span data-stu-id="eadbe-106">The tool requires an OpenSSL installation for Windows 7.</span></span> <span data-ttu-id="eadbe-107">La utilidad OpenSSL debe estar disponible desde la línea de comandos.</span><span class="sxs-lookup"><span data-stu-id="eadbe-107">The OpenSSL utility must be available from the command line.</span></span>

<span data-ttu-id="eadbe-108">Para instalar OpenSSL, vaya al sitio de [OpenSSL](https://www.openssl.org) o de [OpenSSL Binaries](https://wiki.openssl.org/index.php/Binaries).</span><span class="sxs-lookup"><span data-stu-id="eadbe-108">To install OpenSSL, go to the [OpenSSL](https://www.openssl.org) or [OpenSSL Binaries](https://wiki.openssl.org/index.php/Binaries) site.</span></span>



## <a name="create-a-certificate-mac-os-x"></a><span data-ttu-id="eadbe-109">Creación de un certificado (Mac OS X)</span><span class="sxs-lookup"><span data-stu-id="eadbe-109">Create a certificate (Mac OS X)</span></span>

<span data-ttu-id="eadbe-110">Normalmente, la utilidad OpenSSL está disponible en el sistema operativo Linux o Mac OS X.</span><span class="sxs-lookup"><span data-stu-id="eadbe-110">Usually, the OpenSSL utility is available in the Linux or Mac OS X operating system.</span></span>

<span data-ttu-id="eadbe-111">También puede instalar la utilidad si ejecuta alguno de los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="eadbe-111">You can also install the utility by running either of the following commands:</span></span>
* <span data-ttu-id="eadbe-112">En el administrador de paquetes *Brew*:</span><span class="sxs-lookup"><span data-stu-id="eadbe-112">From the *Brew* package manager:</span></span>

    ```cmd
    brew install openssl
    brew link openssl --force
    ```

* <span data-ttu-id="eadbe-113">Mediante *MacPorts*:</span><span class="sxs-lookup"><span data-stu-id="eadbe-113">By using *MacPorts*:</span></span>

    ```cmd
    sudo port install openssl
    ```

<span data-ttu-id="eadbe-114">Después de instalar la utilidad OpenSSL para generar un nuevo certificado, ejecute el siguiente comando:</span><span class="sxs-lookup"><span data-stu-id="eadbe-114">After you install the OpenSSL utility for generating a new certificate, run the following command:</span></span>

```cmd
pbiviz --create-cert
```

## <a name="create-a-certificate-linux"></a><span data-ttu-id="eadbe-115">Creación de un certificado (Linux)</span><span class="sxs-lookup"><span data-stu-id="eadbe-115">Create a certificate (Linux)</span></span>

<span data-ttu-id="eadbe-116">La utilidad OpenSSL no está disponible en el sistema operativo Linux, pero puede instalarla con uno de los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="eadbe-116">If the OpenSSL utility isn't available in your Linux operating system, you can install it by using one of the following commands:</span></span>

* <span data-ttu-id="eadbe-117">Para el administrador de paquetes *APT*:</span><span class="sxs-lookup"><span data-stu-id="eadbe-117">For *APT* package manager:</span></span>

    ```cmd
    sudo apt-get install openssl
    ```

* <span data-ttu-id="eadbe-118">Para *Yellowdog Updater*:</span><span class="sxs-lookup"><span data-stu-id="eadbe-118">For *Yellowdog Updater*:</span></span>

    ```cmd
    yum install openssl
    ```

* <span data-ttu-id="eadbe-119">Para *Redhat Package Manager*:</span><span class="sxs-lookup"><span data-stu-id="eadbe-119">For *Redhat Package Manager*:</span></span>

    ```cmd
    rpm install openssl
    ```

<span data-ttu-id="eadbe-120">Si la utilidad OpenSSL ya está disponible en el sistema operativo, ejecute el siguiente comando para generar un nuevo certificado:</span><span class="sxs-lookup"><span data-stu-id="eadbe-120">If the OpenSSL utility is already available in your operating system, generate a new certificate by running the following command:</span></span>

```cmd
pbiviz --create-cert
```

<span data-ttu-id="eadbe-121">También puede obtener la utilidad OpenSSL en el sitio de [OpenSSL](https://www.openssl.org) u [OpenSSL Binaries](https://wiki.openssl.org/index.php/Binaries).</span><span class="sxs-lookup"><span data-stu-id="eadbe-121">Or you can get the OpenSSL utility by going to the [OpenSSL](https://www.openssl.org) or [OpenSSL Binaries](https://wiki.openssl.org/index.php/Binaries) site.</span></span>

## <a name="generate-the-certificate-manually"></a><span data-ttu-id="eadbe-122">Generación manual del certificado</span><span class="sxs-lookup"><span data-stu-id="eadbe-122">Generate the certificate manually</span></span>

<span data-ttu-id="eadbe-123">Puede especificar que los certificados se generen mediante cualquier herramienta.</span><span class="sxs-lookup"><span data-stu-id="eadbe-123">You can specify that your certificates be generated by any tools.</span></span>

<span data-ttu-id="eadbe-124">Si la utilidad OpenSSL ya está instalada en el sistema, ejecute los siguientes comandos para generar un nuevo certificado:</span><span class="sxs-lookup"><span data-stu-id="eadbe-124">If the OpenSSL utility is already installed in your system, generate a new certificate by running the following commands:</span></span>

```cmd
openssl req -x509 -newkey rsa:4096 -keyout PowerBICustomVisualTest_private.key -out PowerBICustomVisualTest_public.crt -days 365
```

<span data-ttu-id="eadbe-125">Normalmente, puede encontrar los certificados de servidor web correspondientes a PowerBI-visuals-tools si ejecuta uno de los siguientes comandos:</span><span class="sxs-lookup"><span data-stu-id="eadbe-125">You can usually find the PowerBI-visuals-tools web server certificates by running one the following:</span></span>

* <span data-ttu-id="eadbe-126">Para la instancia global de las herramientas:</span><span class="sxs-lookup"><span data-stu-id="eadbe-126">For the global instance of the tools:</span></span>

    ```cmd
    %appdata%\npm\node_modules\PowerBI-visuals-tools\certs
    ```

* <span data-ttu-id="eadbe-127">Para la instancia local de las herramientas:</span><span class="sxs-lookup"><span data-stu-id="eadbe-127">For the local instance of the tools:</span></span>

    ```cmd
    <custom visual project root>\node_modules\PowerBI-visuals-tools\certs
    ```

<span data-ttu-id="eadbe-128">Si usa el formato PEM, guarde el archivo de certificado como *PowerBICustomVisualTest_public.crt* y guarde el archivo privateKey como *PowerBICustomVisualTest_public.key*.</span><span class="sxs-lookup"><span data-stu-id="eadbe-128">If you use the PEM format, save the certificate file as *PowerBICustomVisualTest_public.crt*, and save privateKey as *PowerBICustomVisualTest_public.key*.</span></span>

<span data-ttu-id="eadbe-129">Si usa el formato PFX, guarde el archivo de certificado como *PowerBICustomVisualTest_public.pfx*.</span><span class="sxs-lookup"><span data-stu-id="eadbe-129">If you use the PFX format, save the certificate file as *PowerBICustomVisualTest_public.pfx*.</span></span>

<span data-ttu-id="eadbe-130">Si el archivo de certificado PFX requiere una frase de contraseña, haga lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="eadbe-130">If your PFX certificate file requires a passphrase, do the following:</span></span>
1. <span data-ttu-id="eadbe-131">En el archivo de configuración, especifique:</span><span class="sxs-lookup"><span data-stu-id="eadbe-131">In the config file, specify:</span></span>

    ```cmd
    \PowerBI-visuals-tools\config.json
    ```

1. <span data-ttu-id="eadbe-132">En la sección `server`, especifique la frase de contraseña; para ello, reemplace el marcador de posición "*YOUR PASSPHRASE*":</span><span class="sxs-lookup"><span data-stu-id="eadbe-132">In the `server` section, specify the passphrase by replacing the "*YOUR PASSPHRASE*" placeholder:</span></span>

    ```cmd
    "server":{
        "root":"webRoot",
        "assetsRoute":"/assets",
        "privateKey":"certs/PowerBICustomVisualTest_private.key",
        "certificate":"certs/PowerBICustomVisualTest_public.crt",
        "pfx":"certs/PowerBICustomVisualTest_public.pfx",
        "port":"8080",
        "passphrase":"YOUR PASSPHRASE"
    }
    ```
