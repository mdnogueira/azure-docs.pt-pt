---
title: "Segurança de cluster do Service Fabric: funções do cliente | Microsoft Docs"
description: "Este artigo descreve as funções do cliente de dois e as permissões fornecidas para as funções."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: coreysa
editor: 
ms.assetid: 7bc808d9-3609-46a1-ac12-b4f53bff98dd
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.openlocfilehash: 85935e60bba4b27972282700e2e9c9a22b403bdb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="role-based-access-control-for-service-fabric-clients"></a>Controlo de acesso baseado em funções para clientes de Service Fabric
Recursos de infraestrutura de serviço do Azure suporta dois tipos de controlo de acesso diferentes para clientes que estão ligados a um cluster do Service Fabric: administrador e utilizador. Controlo de acesso permite ao administrador de cluster limitar o acesso a determinadas operações de cluster para diferentes grupos de utilizadores, tornando o cluster mais segura.  

**Os administradores** têm acesso total às capacidades de gestão (incluindo as capacidades de leitura/escrita). Por predefinição, **utilizadores** apenas tem acesso de leitura às capacidades de gestão (por exemplo, capacidades de consulta) e a capacidade para resolver a aplicações e serviços.

Especifique as funções de cliente de dois (administrador e cliente) no momento da criação do cluster ao fornecer certificados separados para cada. Consulte [segurança de cluster do Service Fabric](service-fabric-cluster-security.md) para obter detalhes sobre como configurar um cluster do Service Fabric seguro.

## <a name="default-access-control-settings"></a>Definições de controlo de acesso predefinida
O tipo de controlo de acesso de administrador tem acesso total a todos os APIs de FabricClient. Pode efetuar qualquer leituras e escritas no cluster de Service Fabric, incluindo as seguintes operações:

### <a name="application-and-service-operations"></a>Operações de serviço e aplicação
* **CreateService**: a criação do serviço                             
* **CreateServiceFromTemplate**: a criação do modelo do serviço                             
* **UpdateService**: atualizações de serviço                             
* **DeleteService**: eliminação de serviço                             
* **ProvisionApplicationType**: aprovisionamento de tipo de aplicação                             
* **CreateApplication**: criação de aplicações                               
* **DeleteApplication**: eliminação de aplicação                             
* **UpgradeApplication**: a iniciar ou interromper as atualizações de aplicações                             
* **UnprovisionApplicationType**: tipo aplicação de anulação de aprovisionamento                             
* **MoveNextUpgradeDomain**: a retomar as atualizações de aplicações com um domínio de atualização explícita                             
* **ReportUpgradeHealth**: a retomar as atualizações de aplicações com o progresso da atualização atual                             
* **ReportHealth**: relatório de estado de funcionamento                             
* **PredeployPackageToNode**: API de pré-implementação                            
* **CodePackageControl**: reiniciar pacotes de código                             
* **RecoverPartition**: recuperação de uma partição                             
* **RecoverPartitions**: recuperar partições                             
* **RecoverServicePartitions**: recuperar partições de serviço                             
* **RecoverSystemPartitions**: recuperar as partições de serviço do sistema                             

### <a name="cluster-operations"></a>Operações de cluster
* **ProvisionFabric**: cluster / MSI manifesto aprovisionamento                             
* **UpgradeFabric**: a iniciar as atualizações de cluster                             
* **UnprovisionFabric**: cluster / MSI manifesto anulação de aprovisionamento                         
* **MoveNextFabricUpgradeDomain**: a retomar a atualizações de cluster com um domínio de atualização explícita                             
* **ReportFabricUpgradeHealth**: a retomar a atualizações de cluster com o progresso da atualização atual                             
* **StartInfrastructureTask**: iniciar tarefas de infraestrutura                             
* **FinishInfrastructureTask**: concluir tarefas de infraestrutura                             
* **InvokeInfrastructureCommand**: comandos de gestão de tarefas de infraestrutura                              
* **ActivateNode**: ativar um nó                             
* **DeactivateNode**: desativar um nó                             
* **DeactivateNodesBatch**: desativar vários nós                             
* **RemoveNodeDeactivations**: desativação de reversão em vários nós                             
* **GetNodeDeactivationStatus**: a verificar o estado de Desativação                             
* **NodeStateRemoved**: relatório de estado do nó removido                             
* **ReportFault**: relatório de falhas                             
* **FileContent**: a imagem de transferência de ficheiros de cliente de arquivo (externamente em relação ao cluster)                             
* **FileDownload**: imagem arquivo cliente ficheiro transferência iniciação (externo para cluster)                             
* **InternalList**: a imagem de arquivo de ficheiro lista a operação de cliente (interno)                             
* **Eliminar**: a imagem de operação de eliminação do arquivo de cliente                              
* **Carregar**: a operação de carregamento de cliente de arquivo de imagem                             
* **NodeControl**: a iniciar, parar e reiniciar nós                             
* **MoveReplicaControl**: mover as réplicas de um nó para outro                             

### <a name="miscellaneous-operations"></a>Operações diversas
* **Ping**: pings de cliente                             
* **Consulta**: todas as consultas permitidas
* **NameExists**: verificações de existência de URI de nomenclatura                             

O tipo de controlo de acesso de utilizador é, por predefinição, limitada para as seguintes operações: 

* **EnumerateSubnames**: atribuição de nomes de enumeração de URI                             
* **EnumerateProperties**: atribuição de nomes de enumeração da propriedade                             
* **PropertyReadBatch**: operações de leitura de atribuição de nome de propriedade                             
* **GetServiceDescription**: descrições de serviço de notificações de serviço de inquérito longo e leitura                             
* **ResolveService**: Resolução de serviço com base em conformidade                             
* **ResolveNameOwner**: Resolução proprietário URI de nomenclatura                             
* **ResolvePartition**: resolver serviços do sistema                             
* **ServiceNotifications**: notificações de serviço com base em eventos                             
* **GetUpgradeStatus**: consulta o estado de atualização da aplicação                             
* **GetFabricUpgradeStatus**: consulta o estado de atualização de cluster                             
* **InvokeInfrastructureQuery**: consultar as tarefas de infraestrutura                             
* **Lista**: operação de lista de ficheiros de cliente de arquivo de imagem                             
* **ResetPartitionLoad**: repor a carga de uma unidade de ativação pós-falha                             
* **ToggleVerboseServicePlacementHealthReporting**: ativando ou desativando a relatórios de estado de funcionamento de posicionamento do serviço verboso                             

O controlo de acesso de administrador também tem acesso para as operações anteriores.

## <a name="changing-default-settings-for-client-roles"></a>Alterar as predefinições para as funções do cliente
O ficheiro de manifesto do cluster, pode fornecer capacidades de administrador para o cliente se for necessário. Pode alterar as predefinições, acedendo ao **definições de recursos de infraestrutura** opção durante [criação de cluster](service-fabric-cluster-creation-via-portal.md)e fornecer as definições anteriores no **nome**, **admin**, **utilizador**, e **valor** campos.

## <a name="next-steps"></a>Passos seguintes
[Segurança de cluster do Service Fabric](service-fabric-cluster-security.md)

[Criação de cluster do Service Fabric](service-fabric-cluster-creation-via-portal.md)

