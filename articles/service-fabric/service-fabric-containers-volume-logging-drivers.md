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
ms.openlocfilehash: 955f84e5656bbf568234cbaf69faa4dd0a741206
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2017
---
# <a name="using-volume-plugins-and-logging-drivers-in-your-container"></a>Utilizar o plug-ins de volume e registo de controladores no contentor de
Suporta a especificação de Service Fabric [plug-ins de volume do Docker](https://docs.docker.com/engine/extend/plugins_volume/) e [controladores de registo de Docker](https://docs.docker.com/engine/admin/logging/overview/) para o seu serviço de contentor.  Isto permitirá manter os dados no [ficheiros do Azure](https://azure.microsoft.com/en-us/services/storage/files/) mesmo contentor é movida ou reiniciada num anfitrião diferente.

Atualmente, existem apenas os controladores de volume para contentores de Linux, como mostrado abaixo.  Se estiver a utilizar contentores do Windows, é possível mapear um volume para ficheiros de Azure [SMB3 partilha](https://blogs.msdn.microsoft.com/clustering/2017/08/10/container-storage-support-with-cluster-shared-volumes-csv-storage-spaces-direct-s2d-smb-global-mapping/) sem um controlador de volume com a versão mais recente do 1709 do Windows Server. Isto seria necessário atualizar as máquinas virtuais no cluster para a versão do Windows Server 1709.


## <a name="install-volumelogging-driver"></a>Instalar o controlador do volume/registo

Se o controlador de volume/registo de Docker não está instalado no computador, instalá-lo manualmente através de RDP/SSH-utilize no computador, através de um [script de arranque VMSS](https://azure.microsoft.com/en-us/resources/templates/201-vmss-custom-script-windows/) ou utilizando um [SetupEntryPoint](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-application-model#describe-a-service) script. Escolher um dos métodos mencionados, pode escrever um script para instalar o [Docker controladores de Volume para o Azure](https://docs.docker.com/docker-for-azure/persistent-data-volumes/):


```bash
docker plugin install --alias azure --grant-all-permissions docker4x/cloudstor:17.09.0-ce-azure1  \
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

No exemplo anterior, o `Source` etiqueta para o `Volume` refere-se para a pasta de origem. A pasta de origem pode ser uma pasta na VM que aloja os contentores ou um arquivo persistente remoto. O `Destination` etiquetas é a localização que o `Source` está mapeado para no contentor em execução.  Assim, o destino não pode ser uma localização dentro do contentor já existente.

Quando especificar um plug-in de volume, o Service Fabric cria automaticamente o volume utilizando os parâmetros especificados. O `Source` etiqueta é o nome do volume e o `Driver` tag Especifica o plug-in de controlador do volume. Podem ser especificadas opções utilizando o `DriverOption` tag conforme mostrado no seguinte fragmento:

```xml
<Volume Source="myvolume1" Destination="c:\testmountlocation4" Driver="azure" IsReadOnly="true">
           <DriverOption Name="share" Value="models"/>
</Volume>
```
Se um controlador de registo do Docker for especificado, é necessário implementar agentes (ou contentores) para processar os registos no cluster.  O `DriverOption` etiqueta pode ser utilizada para especificar opções de controlador de registo também.

Consulte os artigos seguintes para implementar contentores para um cluster do Service Fabric:


[Implementar um contentor no Service Fabric](service-fabric-deploy-container.md)

