---
title: "Criar um cluster do Azure Service Fabric autónomo | Microsoft Docs"
description: "Criar um cluster do Service Fabric do Azure em qualquer computador (físico ou virtual) com o Windows Server, quer seja local ou em nenhuma nuvem."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 31349169-de19-4be6-8742-ca20ac41eb9e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/10/2017
ms.author: chackdan;maburlik;dekapur
ms.openlocfilehash: 6aa2905a97ec6b8c125f2ab9572a8e40bf525b27
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-standalone-cluster-running-on-windows-server"></a>Criar um cluster autónomo em execução no Windows Server
Pode utilizar o Service Fabric do Azure para criar clusters de Service Fabric em qualquer máquinas virtuais ou computadores que executam o Windows Server. Isto significa que pode implementar e executar aplicações de Service Fabric em qualquer ambiente que contém um conjunto de computadores do Windows Server interligados, sê-lo no local ou com qualquer fornecedor de nuvem. O Service Fabric fornece um pacote de configuração para criar clusters de Service Fabric chamados o pacote do Windows Server autónomo.

Este artigo explica os passos para criar um cluster do Service Fabric autónomo.

> [!NOTE]
> Este pacote do Windows Server autónomo é disponibilizado comercialmente e pode ser utilizado para implementações de produção. Este pacote pode conter novas funcionalidades do Service Fabric que estão em "Pré-visualização". Desloque para baixo para "[incluídas neste pacote de funcionalidades de pré-visualização](#previewfeatures_anchor)." secção para obter a lista de funcionalidades da pré-visualização. Pode [transferir uma cópia do EULA](http://go.microsoft.com/fwlink/?LinkID=733084) agora.
> 
> 

<a id="getsupport"></a>

## <a name="get-support-for-the-service-fabric-for-windows-server-package"></a>Obter o suporte para o pacote de recursos de infraestrutura de serviço para o Windows Server
* Peça da Comunidade sobre o pacote de autónoma do Service Fabric para o Windows Server no [fórum do Azure Service Fabric](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureServiceFabric?).
* Abrir um pedido para [profissional de suporte para o Service Fabric](http://support.microsoft.com/oas/default.aspx?prid=16146).  Saiba mais sobre o profissional de suporte da Microsoft [aqui](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
* Também pode obter suporte para este pacote como parte de [suporte do Microsoft Premier](https://support.microsoft.com/en-us/premier).
* Para obter mais detalhes, consulte [as opções de suporte do Azure Service Fabric](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-support).
* Para recolher registos para fins de suporte, execute o [recoletor de registos de autónoma do Service Fabric](service-fabric-cluster-standalone-package-contents.md).

<a id="downloadpackage"></a>

## <a name="download-the-service-fabric-for-windows-server-package"></a>Transferir o pacote de recursos de infraestrutura de serviço para o Windows Server
Para criar o cluster, utilize o pacote de recursos de infraestrutura de serviço para o Windows Server (Windows Server 2012 R2 e mais recentes) encontrada aqui: <br>
[Transferir a ligação - pacote de autónomo do Service Fabric - Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690)

Encontrar os detalhes no conteúdo do pacote [aqui](service-fabric-cluster-standalone-package-contents.md).

O pacote de tempo de execução do Service Fabric é transferido automaticamente na hora de criação do cluster. Se implementar a partir de uma máquina não ligada à internet, transfira o pacote de tempo de execução fora de banda a partir daqui: <br>
[Transferir a ligação - tempo de execução do Service Fabric - Windows Server](https://go.microsoft.com/fwlink/?linkid=839354)

Localize exemplos de configuração de Cluster autónomo no: <br>
[Exemplos de configuração de Cluster autónomo](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

<a id="createcluster"></a>

## <a name="create-the-cluster"></a>Criar o cluster
Service Fabric pode ser implementada para um cluster de desenvolvimento de um computador, utilizando o *ClusterConfig.Unsecure.DevCluster.json* ficheiros incluídos no [amostras](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples).

Descompacte o pacote de autónoma ao seu computador, copie o ficheiro de configuração de exemplo para o computador local, em seguida, execute o *CreateServiceFabricCluster.ps1* script através de uma sessão do PowerShell de administrador, a partir da pasta de pacote autónomo :
### <a name="step-1a-create-an-unsecured-local-development-cluster"></a>Passo 1A: criar um cluster de desenvolvimento local não segura
```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```

Consulte a secção de configuração do ambiente [planear e preparar a implementação de cluster](service-fabric-cluster-standalone-deployment-preparation.md) para detalhes de resolução de problemas.

Se está a terminar em execução cenários de desenvolvimento, pode remover o cluster do Service Fabric da máquina ao referir-se aos passos na secção "[remover um cluster](#removecluster_anchor)". 

### <a name="step-1b-create-a-multi-machine-cluster"></a>Passo 1B: criar um cluster de múltiplos computador
Depois de já leu o planeamento e passos de preparação detalhadas a ligação, abaixo, está pronto para criar o cluster de produção, utilizando o ficheiro de configuração de cluster. <br>
[Planear e preparar a implementação de cluster](service-fabric-cluster-standalone-deployment-preparation.md)

1. Validar o ficheiro de configuração ter escrito executando o *TestConfiguration.ps1* script da pasta de pacote autónomo:  

    ```powershell
    .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.json
    ```

    Deverá ver um resultado, conforme mostrado abaixo. Se o campo de inferior "Passou" é devolvido como "True", sanity verificações passaram e o cluster tem de ser implementável com base na configuração de entrada.

    ```
    Trace folder already exists. Traces will be written to existing trace folder: C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer\DeploymentTraces
    Running Best Practices Analyzer...
    Best Practices Analyzer completed successfully.
    
    LocalAdminPrivilege        : True
    IsJsonValid                : True
    IsCabValid                 : True
    RequiredPortsOpen          : True
    RemoteRegistryAvailable    : True
    FirewallAvailable          : True
    RpcCheckPassed             : True
    NoConflictingInstallations : True
    FabricInstallable          : True
    Passed                     : True
    ```

2. Criar o cluster: executar o *CreateServiceFabricCluster.ps1* script para implementar o cluster do Service Fabric em cada máquina na configuração. 
    ```powershell
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -AcceptEULA
    ```

> [!NOTE]
> Os rastreios de implementação são escritos para a VM/máquina em que executou o script do CreateServiceFabricCluster.ps1 PowerShell. Pode encontrá-las na subpasta DeploymentTraces, com base no diretório do qual o script foi executado. Para ver se o Service Fabric foi corretamente implementado numa máquina, encontrar os ficheiros instalados no diretório FabricDataRoot, conforme detalhado no ficheiro de configuração de cluster FabricSettings secção (por predefinição c:\ProgramData\SF). Assim, os processos FabricHost.exe e Fabric.exe podem ser vistos em execução no Gestor de tarefas.
> 
> 

### <a name="step-1c-create-an-offline-internet-disconnected-cluster"></a>Passo 1C: criar um cluster offline (desligadas internet)
O pacote de tempo de execução do Service Fabric é transferido automaticamente durante a criação do cluster. Quando implementar um cluster para máquinas não ligadas à internet, terá de transferir o pacote de tempo de execução do Service Fabric em separado e forneça o caminho para a mesma durante a criação do cluster.
O pacote de tempo de execução pode ser transferido separadamente, de outro computador ligado à internet, [hiperligação Transferir - tempo de execução do serviço de recursos de infraestrutura - Windows Server](https://go.microsoft.com/fwlink/?linkid=839354). Copie o pacote de tempo de execução para onde está a implementar o cluster offline a partir e criar o cluster executando `CreateServiceFabricCluster.ps1` com o `-FabricRuntimePackagePath` parâmetro incluído, conforme mostrado abaixo: 

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -FabricRuntimePackagePath .\MicrosoftAzureServiceFabric.cab
```
onde `.\ClusterConfig.json` e `.\MicrosoftAzureServiceFabric.cab` são os caminhos para a configuração de cluster e o ficheiro. cab de tempo de execução, respetivamente.


### <a name="step-2-connect-to-the-cluster"></a>Passo 2: Ligar ao cluster
Para ligar a um cluster seguro, consulte [Service fabric ligar ao cluster segura](service-fabric-connect-to-secure-cluster.md).

Para ligar a um cluster não seguro, execute o seguinte comando do PowerShell:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <*IPAddressofaMachine*>:<Client connection end point port>
```
Exemplo:
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint 192.13.123.2345:19000
```
### <a name="step-3-bring-up-service-fabric-explorer"></a>Passo 3: Trazer Service Fabric Explorer
Agora pode ligar ao cluster com o Service Fabric Explorer a diretamente a partir de uma das máquinas http://localhost:19080/Explorer/index.html ou remotamente com http://&lt*IPAddressofaMachine*>: 19080/Explorer / Index.HTML.

## <a name="add-and-remove-nodes"></a>Adicionar e remover nós
Pode adicionar ou remover nós ao cluster do Service Fabric do autónomo conforme as necessidades da empresa alterar. Consulte [adicionar ou remover nós para um cluster do Service Fabric autónomo](service-fabric-cluster-windows-server-add-remove-nodes.md) para obter passos detalhados.

<a id="removecluster" name="removecluster_anchor"></a>
## <a name="remove-a-cluster"></a>Remover um cluster
Para remover um cluster, execute o script *RemoveServiceFabricCluster.ps1* do PowerShell a partir da pasta do pacote e passe no caminho para o ficheiro de configuração do JSON. Opcionalmente, pode especificar uma localização para o registo da eliminação.

Este script pode ser executado em qualquer computador que tenha acesso de administrador para todas as máquinas que estão listados como nós do ficheiro de configuração de cluster. A máquina em que este script é executado no tem de ser parte do cluster.

```
# Removes Service Fabric from each machine in the configuration
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -Force
```

```
# Removes Service Fabric from the current machine
.\CleanFabric.ps1
```

<a id="telemetry"></a>

## <a name="telemetry-data-collected-and-how-to-opt-out-of-it"></a>Os dados telemétricos recolhidos e como ativamente por não-la
Por predefinição, o produto recolhe telemetria a utilização de Service Fabric para melhorar o produto. O analisador de melhores práticas que é executado como parte da configuração verifica a existência de conectividade para [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1). Se não estiver acessível, o programa de configuração falhará, a menos que ativamente telemetria.

1. O pipeline de telemetria tenta carregar os dados seguintes para [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1) uma vez para cada dia. Este é um carregamento de melhor esforço e não tem impacto sobre a funcionalidade de cluster. A telemetria é enviada apenas a partir do nó que execute a ativação pós-falha Gestor primário. Não existem outros nós enviam telemetria.
2. A telemetria consiste no seguinte:

* Número de serviços
* Número de ServiceTypes
* Número de aplicações
* Número de ApplicationUpgrades
* Número de FailoverUnits
* Número de InBuildFailoverUnits
* Número de UnhealthyFailoverUnits
* Número de réplicas
* Número de InBuildReplicas
* Número de StandByReplicas
* Número de OfflineReplicas
* CommonQueueLength
* QueryQueueLength
* FailoverUnitQueueLength
* CommitQueueLength
* Número de nós
* IsContextComplete: True/False
* ClusterId: Este é um GUID gerado aleatoriamente para cada cluster
* ServiceFabricVersion
* Endereço IP da máquina virtual ou máquina a partir do qual é carregada a telemetria

Para desativar a telemetria, adicione o seguinte ao *propriedades* na sua configuração de cluster: *ativar telemetria: false*.

<a id="previewfeatures" name="previewfeatures_anchor"></a>

## <a name="preview-features-included-in-this-package"></a>Funcionalidades de pré-visualização incluídas no pacote
nenhum.


> [!NOTE]
> Começando com o novo [versão GA do cluster autónomo para o Windows Server (versão 5.3.204.x)](https://azure.microsoft.com/blog/azure-service-fabric-for-windows-server-now-ga/), pode atualizar o cluster para versões futuras, manual ou automaticamente. Consulte [atualizar uma versão de cluster do Service Fabric autónomo](service-fabric-cluster-upgrade-windows-server.md) documento para obter mais detalhes.
> 
> 

## <a name="next-steps"></a>Passos seguintes
* [Implementar e remover aplicações utilizando o PowerShell](service-fabric-deploy-remove-applications.md)
* [Definições de configuração do cluster do Windows autónomo](service-fabric-cluster-manifest.md)
* [Adicionar ou remover nós para um cluster do Service Fabric autónomo](service-fabric-cluster-windows-server-add-remove-nodes.md)
* [Atualizar uma versão de cluster do Service Fabric autónomo](service-fabric-cluster-upgrade-windows-server.md)
* [Criar um cluster do Service Fabric autónomo com as VMs do Azure com o Windows](service-fabric-cluster-creation-with-windows-azure-vms.md)
* [Proteger um cluster autónomo no Windows com segurança do Windows](service-fabric-windows-cluster-windows-security.md)
* [Proteger um cluster autónomo no Windows utilizando X509 certificados](service-fabric-windows-cluster-x509-security.md)

<!--Image references-->
[Trusted Zone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png
