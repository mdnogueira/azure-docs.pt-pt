---
title: "Azure Service Fabric Docker Compose pré-visualização | Microsoft Docs"
description: "Azure Service Fabric aceita formato Docker Compose para tornar mais fácil orquestrar a contentores exsiting utilizando o Service Fabric. Este suporte está atualmente em pré-visualização."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.openlocfilehash: cf7b0dd3a81c35be4907dbba85b72ce4f87e3a9f
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/12/2017
---
# <a name="using-volume-plugins-and-logging-drivers-in-your-container"></a>Utilizar o plug-ins de volume e registo de controladores no contentor de

Suporta a especificação de Service Fabric [plug-ins de volume do Docker](https://docs.docker.com/engine/extend/plugins_volume/) e [controladores de registo de Docker](https://docs.docker.com/engine/admin/logging/overview/) para o seu serviço de contentor. 

## <a name="install-volumelogging-driver"></a>Instalar o controlador do volume/registo

Se o controlador de volume/registo de Docker não está instalado no computador, instale manualmente através de RDP/SSH-utilize no computador ou através de um script de arranque VMSS. Por exemplo, na ordem para instalar o controlador de Volume do Docker, SSH para a máquina e execute:

```bash
docker plugin install --alias azure --grant-all-permissions docker4x/17.09.0-ce-azure1  \
    CLOUD_PLATFORM=AZURE \
    AZURE_STORAGE_ACCOUNT="[MY-STORAGE-ACCOUNT-NAME]" \
    AZURE_STORAGE_ACCOUNT_KEY="[MY-STORAGE-ACCOUNT-KEY]" \
    DEBUG=1
```

## <a name="specify-the-plugin-or-driver-in-the-manifest"></a>Especifique o plug-in ou controlador no manifesto
Os plug-ins são especificados no manifesto da aplicação, conforme mostrado no manifesto seguinte:

```xml
?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WinNodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <Description>Calculator Application</Description>
    <Parameters>
        <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
      <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
    </Parameters>
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
     <Policies>
       <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv"> 
        <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
        <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
        <LogConfig Driver="etwlogs" >
          <DriverOption Name="test" Value="vale"/>
        </LogConfig>
        <Volume Source="c:\workspace" Destination="c:\testmountlocation1" IsReadOnly="false"></Volume>
        <Volume Source="d:\myfolder" Destination="c:\testmountlocation2" IsReadOnly="true"> </Volume>
        <Volume Source="myvolume1" Destination="c:\testmountlocation2" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
        </Volume>
       </ContainerHostPolicies>
   </Policies>
    </ServiceManifestImport>
    <ServiceTemplates>
        <StatelessService ServiceTypeName="StatelessNodeService" InstanceCount="5">
            <SingletonPartition></SingletonPartition>
        </StatelessService>
    </ServiceTemplates>
</ApplicationManifest>
```

No exemplo anterior, o `Source` etiqueta para o `Volume` refere-se para a pasta de origem. A pasta de origem pode ser uma pasta na VM que aloja os contentores ou um arquivo persistente remoto. O `Destination` etiquetas é a localização que o `Source` está mapeado para no contentor em execução. 

Quando especificar um plug-in de volume, o Service Fabric cria automaticamente o volume utilizando os parâmetros especificados. O `Source` etiqueta é o nome do volume e o `Driver` tag Especifica o plug-in de controlador do volume. Podem ser especificadas opções utilizando o `DriverOption` tag conforme mostrado no seguinte fragmento:

```xml
<Volume Source="myvolume1" Destination="c:\testmountlocation4" Driver="azurefile" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
</Volume>
```

Se um controlador de registo do Docker for especificado, é necessário implementar agentes (ou contentores) para processar os registos no cluster.  O `DriverOption` etiqueta pode ser utilizada para especificar opções de controlador de registo também.

Consulte os artigos seguintes para implementar contentores para um cluster do Service Fabric:


[Implementar um contentor no Service Fabric](service-fabric-deploy-container.md)

