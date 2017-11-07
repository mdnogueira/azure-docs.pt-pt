---
title: "Adicionar ou remover nós para um cluster do Service Fabric autónomo | Microsoft Docs"
description: "Saiba como adicionar ou remover nós num cluster do Azure Service Fabric numa máquina física ou virtual com o Windows Server, que pode ser no local ou em nenhuma nuvem."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: bc6b8fc0-d2af-42f8-a164-58538be38d02
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: dekapur
ms.openlocfilehash: 252dcdf0ff9e1fecd6665808bfe7978a4417018b
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="add-or-remove-nodes-to-a-standalone-service-fabric-cluster-running-on-windows-server"></a>Adicionar ou remover nós para um cluster do Service Fabric autónomo em execução no Windows Server
Depois de ter [criado o seu cluster do Service Fabric autónomo nas máquinas do Windows Server](service-fabric-cluster-creation-for-windows-server.md), podem alterar a suas necessidades (empresariais) e terá de adicionar ou remover nós ao cluster. Este artigo fornece os passos detalhados de alcançar isto. Tenha em atenção que adicionar/remover a funcionalidade de nó não é suportada em clusters de desenvolvimento local.

## <a name="add-nodes-to-your-cluster"></a>Adicionar nós ao cluster

1. Preparar a VM/máquina que pretende adicionar ao cluster, seguindo os passos descritos em [planear e preparar a implementação de cluster do Service Fabric](service-fabric-cluster-creation-for-windows-server.md)
2. Identificar o domínio de falhas e pretender adicionar esta VM máquina para o domínio de atualização
3. Ambiente de trabalho remoto (RDP) para a VM/máquina em que pretende adicionar ao cluster
4. Copiar ou [transferir o pacote de autónomo para recursos de infraestrutura de serviço para o Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) para a VM/máquina e deszipe o pacote
5. Executar o Powershell com privilégios elevados e navegue para a localização do pacote deszipada
6. Execute o *AddNode.ps1* script com os parâmetros que descrevem o novo nó para adicionar. O exemplo abaixo adiciona um novo nó chamado VM5, com o tipo NodeType0 e endereços IP 182.17.34.52, UD1 e DF: / dc1/r0. O *ExistingClusterConnectionEndPoint* é um ponto final de ligação para um nó do cluster existente, já que pode ser o endereço IP do *qualquer* os nós do cluster.

    ```
    .\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain fd:/dc1/r0 -AcceptEULA
    ```
    Assim que o script estiver concluído em execução, pode verificar se o novo nó foi adicionado ao executar o [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) cmdlet.

7. Para assegurar a consistência entre diferentes nós do cluster, tem de iniciar uma atualização da configuração. Executar [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) para obter o ficheiro de configuração mais recente e adicionar o nó recentemente adicionado à secção "Nós". Recomenda-se também tenham sempre a configuração de cluster mais recente disponível no caso de que precisa de redploy um cluster com a mesma configuração.

    ```
        {
            "nodeName": "vm5",
            "iPAddress": "182.17.34.52",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD1"
        }
    ```
8. Executar [início ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) para iniciar a atualização.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

    ```
    Pode monitorizar o progresso da atualização no Service Fabric Explorer. Em alternativa, pode executar [Get ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

### <a name="add-nodes-to-clusters-configured-with-windows-security-using-gmsa"></a>Adicionar nós nos clusters configurados com a segurança do Windows utilizam a gMSA
Para clusters configurados com o grupo gerido serviço Account(gMSA) (https://technet.microsoft.com/library/hh831782.aspx), um novo nó pode ser adicionado através de uma atualização da configuração:
1. Executar [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) em qualquer um de nós existentes para obter o ficheiro de configuração mais recente e adicione os detalhes sobre o novo nó que pretende adicionar na secção "Nós". Certifique-se que o novo nó faz parte da mesma conta geridas de grupo. Esta conta deve ser um administrador em todas as máquinas.

    ```
        {
            "nodeName": "vm5",
            "iPAddress": "182.17.34.52",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD1"
        }
    ```
2. Executar [início ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) para iniciar a atualização.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
    ```
    Pode monitorizar o progresso da atualização no Service Fabric Explorer. Em alternativa, pode executar [Get ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

### <a name="add-node-types-to-your-cluster"></a>Adicionar tipos de nó ao cluster
Para poder adicionar um novo tipo de nó, modifique a sua configuração para incluir o novo tipo de nó "NodeTypes" nos secção em "Propriedades" e começar a uma configuração de fazer a actualização utilizando [início ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps). Uma vez concluída a atualização, pode adicionar novos nós ao cluster com este tipo de nó.

## <a name="remove-nodes-from-your-cluster"></a>Remover nós do cluster
Um nó pode ser removido de um cluster através de uma atualização de configuração, da seguinte forma:

1. Executar [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) para obter o ficheiro de configuração mais recente e *remover* o nó da secção "Nós".
Adicione o parâmetro de "NodesToBeRemoved" para a secção "Configurar" dentro "FabricSettings" secção. "Valor" deve ser uma lista separada por vírgulas de nomes de nó de nós que tenham de ser removidas.

    ```
         "fabricSettings": [
            {
            "name": "Setup",
            "parameters": [
                {
                "name": "FabricDataRoot",
                "value": "C:\\ProgramData\\SF"
                },
                {
                "name": "FabricLogRoot",
                "value": "C:\\ProgramData\\SF\\Log"
                },
                {
                "name": "NodesToBeRemoved",
                "value": "vm0, vm1"
                }
            ]
            }
        ]
    ```
2. Executar [início ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) para iniciar a atualização.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

    ```
    Pode monitorizar o progresso da atualização no Service Fabric Explorer. Em alternativa, pode executar [Get ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)

> [!NOTE]
> Remoção de nós, pode iniciar várias atualizações. Alguns nós são marcados com `IsSeedNode=”true”` Etiquetar e podem ser identificados consultando o cluster de manifesto utilizando `Get-ServiceFabricClusterManifest`. Remoção de nós pode demorar mais do que outras pessoas, uma vez que os nós seed tem de ser movidos em torno no tais cenários. O cluster tem de manter um mínimo de 3 nós de tipo de nó principal.
> 
> 

### <a name="remove-node-types-from-your-cluster"></a>Remover tipos de nó do cluster
Antes de remover um tipo de nó, verifique duplo se existem quaisquer nós a referenciar o tipo de nó. Remova estes nós antes de remover o tipo de nó correspondente. Depois de todos os nós correspondentes são removidos, pode remover o NodeType da configuração do cluster e iniciar uma configuração de fazer a actualização utilizando [início ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps).


### <a name="replace-primary-nodes-of-your-cluster"></a>Substitua primários nós do cluster
A substituição de nós principais deve ser executado um nó após a outra, em vez de remover e, em seguida, adicionar em lotes.


## <a name="next-steps"></a>Passos seguintes
* [Definições de configuração do cluster do Windows autónomo](service-fabric-cluster-manifest.md)
* [Proteger um cluster autónomo no Windows utilizando X509 certificados](service-fabric-windows-cluster-x509-security.md)
* [Criar um cluster do Service Fabric autónomo com as VMs do Azure com o Windows](service-fabric-cluster-creation-with-windows-azure-vms.md)

