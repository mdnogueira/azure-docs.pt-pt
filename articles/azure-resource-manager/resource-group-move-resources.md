---
title: "Mover recursos do Azure para a nova subscrição ou grupo de recursos | Microsoft Docs"
description: "Utilize o Gestor de recursos do Azure para mover recursos para um novo grupo de recursos ou subscrição."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ab7d42bd-8434-4026-a892-df4a97b60a9b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2017
ms.author: tomfitz
ms.openlocfilehash: 5a28914d967e77d6c8881cd6e56b798269d3df3e
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2017
---
# <a name="move-resources-to-new-resource-group-or-subscription"></a>Mover recursos para o novo grupo de recursos ou subscrição

Este artigo mostra como mover recursos para uma nova subscrição ou um novo grupo de recursos na mesma subscrição. Pode utilizar o portal, PowerShell, CLI do Azure ou a API REST para mover recursos. As operações de movimentação neste artigo estão disponíveis para si sem qualquer assistência de suporte do Azure.

Quando move recursos, o grupo de origem e o grupo de destino estão bloqueados durante a operação. Escrever e operações de eliminação são bloqueados nos grupos de recurso, até concluir a movimentação. Este bloqueio significa não é possível adicionar, atualizar ou eliminar recursos nos grupos de recursos, mas não significa que os recursos são interrompidos. Por exemplo, se mover um SQL Server e a respetiva base de dados para um novo grupo de recursos, uma aplicação que utiliza a base de dados não ocorre nenhum período de indisponibilidade. Pode ainda ler e escrever na base de dados.

Não é possível alterar a localização do recurso. Mover um recurso só move-o para um novo grupo de recursos. O novo grupo de recursos pode ter uma localização diferente, mas que não altere a localização do recurso.

> [!NOTE]
> Este artigo descreve como mover recursos dentro de um Azure existente conta offering. Se, na verdade, pretender alterar a sua conta do Azure offering (como atualizar do pay as you go previamente pagar) ao continuar a trabalhar com os recursos existentes, consulte [mudar a sua subscrição do Azure para outra oferta](../billing/billing-how-to-switch-azure-offer.md).
>
>

## <a name="checklist-before-moving-resources"></a>Lista de verificação antes de mover os recursos

Antes de mover um recurso, é necessário realizar alguns passos importantes. Ao confirmar estas condições, pode evitar erros.

1. As subscrições de origem e de destino tem de existir no mesmo [inquilino do Azure Active Directory](../active-directory/active-directory-howto-tenant.md). Para verificar que ambas as subscrições têm o mesmo ID de inquilino, utilize o Azure PowerShell ou a CLI do Azure.

  Para o Azure PowerShell, utilize:

  ```powershell
  (Get-AzureRmSubscription -SubscriptionName <your-source-subscription>).TenantId
  (Get-AzureRmSubscription -SubscriptionName <your-destination-subscription>).TenantId
  ```

  Para a CLI do Azure, utilize:

  ```azurecli-interactive
  az account show --subscription <your-source-subscription> --query tenantId
  az account show --subscription <your-destination-subscription> --query tenantId
  ```

  Se o inquilino IDs para as subscrições de origem e de destino não forem iguais, tem de contactar o [suporta](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) para mover os recursos para um novo inquilino.

2. O serviço tem de permitir a capacidade de mover recursos. Este artigo apresenta uma lista que serviços ativar mover recursos e os serviços não ative a mover recursos.
3. A subscrição de destino tem de estar registada no fornecedor de recursos do recurso a ser movido. Se não, receberá um erro a indicar que o **subscrição não está registada para um tipo de recurso**. Poderá encontrar este problema ao mover um recurso para uma nova subscrição, mas em que esta nunca foi utilizada com esse tipo de recurso.

  Para o PowerShell, utilize os seguintes comandos para obter o estado do registo:

  ```powershell
  Set-AzureRmContext -Subscription <destination-subscription-name-or-id>
  Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
  ```

  Para registar um fornecedor de recursos, utilize:

  ```powershell
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
  ```

  Para a CLI do Azure, utilize os seguintes comandos para obter o estado do registo:

  ```azurecli-interactive
  az account set -s <destination-subscription-name-or-id>
  az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
  ```

  Para registar um fornecedor de recursos, utilize:

  ```azurecli-interactive
  az provider register --namespace Microsoft.Batch
  ```

## <a name="when-to-call-support"></a>Quando contactar o suporte

Pode mover a maioria dos recursos através de operações de self-service apresentadas neste artigo. Utilize as operações de self-service para:

* Mover recursos do Resource Manager.
* Mover recursos clássicos de acordo com o [limitações de implementação clássica](#classic-deployment-limitations).

Contacte [suporta](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) quando precisa de:

* Mova os recursos para uma nova conta do Azure (e de inquilino do Azure Active Directory).
* Mover recursos clássicos mas estão a ter problemas com as limitações.

## <a name="services-that-enable-move"></a>Serviços que permitem mover

Os serviços que permitem mover para um novo grupo de recursos e de subscrição são:

* Gestão de API
* Aplicações de serviço de aplicações (aplicações web) - consulte [limitações do serviço de aplicações](#app-service-limitations)
* Application Insights
* Automatização
* Azure Cosmos DB
* Batch
* Bing Maps
* CDN
* Serviços em nuvem - consulte [limitações de implementação clássica](#classic-deployment-limitations)
* Serviços Cognitivos
* Content Moderator
* Catálogo de Dados
* Data Factory
* Data Lake Analytics
* Data Lake Store
* DNS
* Event Hubs
* Clusters do HDInsight - consulte [limitações do HDInsight](#hdinsight-limitations)
* Hubs IoT
* Cofre de Chaves
* Balanceadores de carga
* Aplicações Lógicas
* Machine Learning
* Serviços de Multimédia
* Mobile Engagement
* Hubs de Notificação
* Informações Operacionais
* Gestão de Operações
* Power BI
* Cache de Redis
* Scheduler
* Pesquisa
* Gestão de Servidores
* Service Bus
* Service Fabric
* Armazenamento
* Armazenamento (clássica) - consulte [limitações de implementação clássica](#classic-deployment-limitations)
* Estado do Stream Analytics - Stream Analytics não não possível mover as tarefas em execução.
* Base de dados do SQL server - base de dados e servidor têm de residir no mesmo grupo de recursos. Quando move um SQL server, todas as suas bases de dados também são movidas.
* Gestor de Tráfego
* Não é possível mover máquinas virtuais - VMs com discos geridos. Consulte [limitações de máquinas virtuais](#virtual-machines-limitations)
* Máquinas virtuais (clássicas) - consulte [limitações de implementação clássica](#classic-deployment-limitations)
* Conjuntos de dimensionamento de máquina virtual - Consulte [limitações de máquinas virtuais](#virtual-machines-limitations)
* Redes virtuais - Consulte [limitações de redes virtuais](#virtual-networks-limitations)
* Gateway de VPN

## <a name="services-that-do-not-enable-move"></a>Serviços que não ative a mover

Os serviços que atualmente não permitem mover um recurso são:

* Serviços de domínio do AD
* Serviço de estado de funcionamento de AD híbrido
* Gateway de Aplicação
* Serviços BizTalk
* Serviço de Contentor
* ExpressRoute
* DevTest Labs - vá para o novo grupo de recursos na mesma subscrição está ativada, mas mover subscrição cruzada não está ativada.
* Dynamics LCS
* Aplicações Geridas
* Discos geridos - Consulte [limitações de máquinas virtuais](#virtual-machines-limitations)
* Cofre dos serviços de recuperação - também efetue não mover os recursos de computação, rede e armazenamento associados ao Cofre de serviços de recuperação, consulte [limitações de serviços de recuperação](#recovery-services-limitations).
* Segurança
* Gestor de dispositivos do StorSimple
* Redes virtuais (clássicas) - consulte [limitações de implementação clássica](#classic-deployment-limitations)

## <a name="virtual-machines-limitations"></a>Limitações de máquinas virtuais

Discos geridos não suportam a movimentação. Esta restrição significa que vários recursos relacionados não podem ser movidos demasiado. Não é possível mover:

* Managed disks
* Máquinas virtuais com os discos geridos
* Imagens de criada a partir de discos geridos
* Instantâneos criados a partir de discos geridos
* Conjuntos de disponibilidade com máquinas virtuais com discos geridos

Não é possível mover máquinas virtuais criadas a partir dos recursos de mercado nas subscrições. Desaprovisionar a máquina virtual na subscrição atual e implementar novamente a nova subscrição.

Máquinas virtuais com certificado armazenado no Cofre de chaves podem ser movidas para um novo grupo de recursos na mesma subscrição, mas não nas subscrições.

## <a name="virtual-networks-limitations"></a>Limitações de redes virtuais

Para mover uma rede virtual em modo de peering, primeiro tem de desativar o peering de rede virtual. Quando desativado, pode mover a rede virtual. Após a mudança, Reative o peering de rede virtual.

Não é possível mover uma rede virtual para uma subscrição diferente se a rede virtual contém uma sub-rede com ligações de navegação de recursos. Por exemplo, se um recurso de Cache de Redis está implementado para uma sub-rede, que tem uma ligação de navegação de recursos.

## <a name="app-service-limitations"></a>Limitações do serviço de aplicações

Ao trabalhar com aplicações do App Service, não é possível mover um plano de serviço aplicacional. Para mover as aplicações do App Service, as opções são:

* Mova o plano de serviço de aplicações e todos os outros recursos do serviço de aplicações nesse grupo de recursos para um novo grupo de recursos que ainda não tenham recursos do serviço de aplicações. Este requisito significa que tem de mover o mesmo os recursos do serviço de aplicações que não estão associados com o plano de serviço de aplicações.
* Mover as aplicações para um grupo de recursos diferente, mas manter todos os planos de serviço de aplicações no grupo de recursos original.

O plano de serviço aplicacional não tem de residir no mesmo grupo de recursos que a aplicação para a aplicação funcione corretamente.

Por exemplo, se o grupo de recursos contém:

* **Web-a** que se encontra associado **plano-a**
* **Web-b** que se encontra associado **plano-b**

As opções são:

* Mover **web-a**, **um plano**, **web-b**, e **plano-b**
* Mover **web-a** e **web-b**
* Mover **web-a**
* Mover **web-b**

Todas as outras combinações de envolvem deixando de um tipo de recurso que não pode ser deixado atrás, ao mover um plano de serviço de aplicações (qualquer tipo de recurso de serviço de aplicações).

Se a aplicação web reside num grupo de recursos diferente que o plano de serviço de aplicações, mas que pretende mover a um novo grupo de recursos, tem de efetuar a movimentação em dois passos. Por exemplo:

* **Web-a** reside no **web-grupo**
* **um plano** reside no **plano-grupo**
* Pretende **web-a** e **um plano** residir no **grupo combinados**

Para realizar esta mudança, execute duas operações de movimentação separado na seguinte sequência:

1. Mover o **web-a** para **plano-grupo**
2. Mover **web-a** e **um plano** para **grupo combinado**.

Pode mover um certificado de serviço de aplicações para um novo grupo de recursos ou subscrição sem quaisquer problemas. No entanto, se a sua aplicação web inclui um certificado SSL que comprou externamente e carregado para a aplicação, tem de eliminar o certificado antes de mover a aplicação web. Por exemplo, pode efetuar os seguintes passos:

1. Eliminar o certificado carregado a partir da aplicação web
2. Mova a aplicação web
3. Carregue o certificado para a aplicação web

## <a name="classic-deployment-limitations"></a>Limitações de implementação clássica

As opções para mover recursos implementados através do modelo clássico divergir com base em se estiver a mover os recursos numa subscrição ou para uma nova subscrição.

### <a name="same-subscription"></a>Mesma subscrição

Ao mover os recursos de um grupo de recursos para outro grupo de recursos dentro da mesma subscrição, aplicam as seguintes restrições:

* Não não possível mover a redes virtuais (clássica).
* Máquinas virtuais (clássicas) têm de ser movidas com o serviço em nuvem.
* Só é possível mover o serviço em nuvem quando a mudança inclui todas as respetivas máquinas virtuais.
* Serviço de uma nuvem apenas pode ser movido de cada vez.
* Apenas uma conta de armazenamento (clássica) pode ser movida de cada vez.
* Não é possível mover a conta de armazenamento (clássica) na mesma operação com uma máquina virtual ou um serviço em nuvem.

Para mover recursos clássicos para um novo grupo de recursos dentro da mesma subscrição, utilize as operações de movimentação padrão através de [portal](#use-portal), [Azure PowerShell](#use-powershell), [CLI do Azure](#use-azure-cli), ou [REST API](#use-rest-api). Utilize as mesmas operações como a utilizar para mover recursos do Resource Manager.

### <a name="new-subscription"></a>Nova subscrição

Ao mover os recursos para uma nova subscrição, aplicam as seguintes restrições:

* Todos os recursos clássicos na subscrição têm de ser movidos na mesma operação.
* A subscrição de destino não pode conter quaisquer outros recursos clássicos.
* A movimentação só pode ser pedida através de uma API de REST separado para move clássico. Os comandos de movimentação padrão do Gestor de recursos não funcionam quando mover recursos clássicos para uma nova subscrição.

Para mover recursos clássicos para uma nova subscrição, utilize as operações REST que são específicas para os recursos clássicos. Para utilizar REST, execute os seguintes passos:

1. Verifique se a subscrição de origem possa participar numa mudança entre subscrições. Utilize a seguinte operação:

  ```HTTP
  POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
  ```

     No corpo do pedido, incluem:

  ```json
  {
    "role": "source"
  }
  ```

     A resposta para a operação de validação está no seguinte formato:

  ```json
  {
    "status": "{status}",
    "reasons": [
      "reason1",
      "reason2"
    ]
  }
  ```

2. Verifique se a subscrição de destino possa participar numa mudança entre subscrições. Utilize a seguinte operação:

  ```HTTP
  POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
  ```

     No corpo do pedido, incluem:

  ```json
  {
    "role": "target"
  }
  ```

     A resposta tem do mesmo formato que a validação de subscrição de origem.
3. Se ambas as subscrições de passar pela validação, mova todos os recursos clássicos da subscrição de um para outra subscrição com a seguinte operação:

  ```HTTP
  POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01
  ```

    No corpo do pedido, incluem:

  ```json
  {
    "target": "/subscriptions/{target-subscription-id}"
  }
  ```

A operação pode ser executada durante vários minutos.

## <a name="recovery-services-limitations"></a>Limitações de serviços de recuperação

Mover não está ativada para armazenamento, rede, ou computação recursos utilizados para configurar a recuperação após desastre com o Azure Site Recovery.

Por exemplo, suponha que configurou a replicação das máquinas no local para uma conta de armazenamento (Storage1) e pretende máquina protegida para detetar um após a ativação pós-falha para o Azure como uma máquina virtual (VM1) ligada a uma rede virtual (Network1). Não é possível mover qualquer um destes recursos do Azure - Storage1, VM1 e Network1 - entre grupos de recursos dentro da mesma subscrição ou nas subscrições.

## <a name="hdinsight-limitations"></a>Limitações do HDInsight

Pode mover clusters do HDInsight para uma nova subscrição ou grupo de recursos. No entanto, não é possível mover entre subscrições os recursos de rede ligados ao cluster do HDInsight (por exemplo, a rede virtual, o NIC ou o Balanceador de carga). Além disso, não é possível mover a um novo grupo de recursos um NIC anexado a uma máquina virtual para o cluster.

Quando move um cluster do HDInsight para uma nova subscrição, mova primeiro outros recursos (como a conta de armazenamento). Em seguida, mova o cluster do HDInsight por si só.

## <a name="search-limitations"></a>Limitações de pesquisa

Não é possível mover vários recursos de pesquisa colocados em regiões diferentes, uma vez.
Nesse caso, tem de movê-los separadamente.

## <a name="use-portal"></a>Utilizar o portal

Para mover os recursos, selecione o grupo de recursos que contenha esses recursos e, em seguida, selecione o **mover** botão.

![Mover recursos](./media/resource-group-move-resources/select-move.png)

Selecione se estiver a mover os recursos para um novo grupo de recursos ou uma nova subscrição.

Selecione os recursos para mover e o grupo de recursos de destino. Confirmar que tem de atualizar os scripts para estes recursos e selecione **OK**. Se tiver selecionado o ícone de subscrição de edição no passo anterior, também tem de selecionar a subscrição de destino.

![Selecione um destino](./media/resource-group-move-resources/select-destination.png)

No **notificações**, verá que a operação de movimentação está em execução.

![Mostrar o estado de movimentação](./media/resource-group-move-resources/show-status.png)

Quando tiver concluído, será notificado do resultado.

![Mostrar resultado de movimentação](./media/resource-group-move-resources/show-result.png)

## <a name="use-powershell"></a>Utilizar o PowerShell

Para mover recursos existentes para outro grupo de recursos ou subscrição, utilize o [mover AzureRmResource](/powershell/module/azurerm.resources/move-azurermresource) comando. O exemplo seguinte mostra como mover vários recursos para um novo grupo de recursos.

```powershell
$webapp = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

Para mover para uma nova subscrição, incluir um valor para o `DestinationSubscriptionId` parâmetro.

## <a name="use-azure-cli"></a>Utilizar a CLI do Azure

Para mover recursos existentes para outro grupo de recursos ou subscrição, utilize o [movimentação de recurso az](/cli/azure/resource?view=azure-cli-latest#az_resource_move) comando. Forneça os IDs dos recursos para mover de recurso. O exemplo seguinte mostra como mover vários recursos para um novo grupo de recursos. No `--ids` parâmetro, forneça uma lista separada por espaço do recurso IDs mover.

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

Para mover para uma nova subscrição, forneça o `--destination-subscription-id` parâmetro.

## <a name="use-rest-api"></a>Utilizar API REST

Para mover recursos existentes para outro grupo de recursos ou subscrição, execute:

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

No corpo do pedido, especifique o grupo de recursos de destino e os recursos para mover. Para obter mais informações sobre a operação REST de movimentação, consulte [mover recursos](/rest/api/resources/Resources/MoveResources).

## <a name="next-steps"></a>Passos seguintes

* Para mais informações sobre cmdlets do PowerShell para gerir a sua subscrição, consulte o artigo [utilizar o Azure PowerShell com o Resource Manager](powershell-azure-resource-manager.md).
* Para saber mais sobre os comandos da CLI do Azure para gerir a sua subscrição, consulte [utilizando a CLI do Azure com o Resource Manager](xplat-cli-azure-resource-manager.md).
* Para saber mais sobre as funcionalidades de portais para gerir a sua subscrição, consulte [no portal do Azure para gerir os recursos](resource-group-portal.md).
* Para saber mais sobre a aplicação de uma organização lógica aos seus recursos, consulte [utilizando etiquetas para organizar os recursos](resource-group-using-tags.md).
