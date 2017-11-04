---
title: "Resolução de problemas de falha de alocação do serviço em nuvem | Microsoft Docs"
description: "Resolução de problemas com a falha de atribuição quando implementar os Serviços Cloud no Azure"
services: azure-service-management, cloud-services
documentationcenter: 
author: simonxjx
manager: felixwu
editor: 
tags: top-support-issue
ms.assetid: 529157eb-e4a1-4388-aa2b-09e8b923af74
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/03/2017
ms.author: v-six
ms.openlocfilehash: 33d017d0e09e9b288b0514e85c8865f83a8a2fa1
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="troubleshooting-allocation-failure-when-you-deploy-cloud-services-in-azure"></a>Resolução de problemas com a falha de atribuição quando implementar os Serviços Cloud no Azure
## <a name="summary"></a>Resumo
Quando implementar instâncias de um serviço em nuvem ou adicionar novo web ou instâncias de função de trabalho, o Microsoft Azure aloca recursos de computação. Ocasionalmente, poderá receber erros ao efetuar estas operações mesmo antes de atingir os limites de subscrição do Azure. Este artigo explica as causas de algumas das falhas de alocação comuns e sugere possíveis remediação. As informações também podem ser úteis quando planear a implementação dos seus serviços.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

### <a name="background--how-allocation-works"></a>Em segundo plano – como funciona a alocação
Os servidores nos centros de dados do Azure são divididos em partições em clusters. Um novo pedido de alocação do serviço de nuvem é tentado em vários clusters. Quando a primeira instância for implementada para um serviço em nuvem (no teste ou de produção), que o serviço em nuvem obtém afixada para um cluster. Quaisquer implementações adicionais para o serviço em nuvem irão acontecer no mesmo cluster. Neste artigo, iremos irá consultar este como "afixado para um cluster". Diagrama 1 abaixo ilustra o caso de uma alocação normal, que é tentada em vários clusters; Diagrama 2 ilustra o caso de uma alocação que tenha afixado ao Cluster 2 uma vez que é o local onde está alojada a CS_1 de serviço em nuvem existente.

![Diagrama de atribuição](./media/cloud-services-allocation-failure/Allocation1.png)

### <a name="why-allocation-failure-happens"></a>Por que motivo ocorre a falha de alocação
Quando um pedido de alocação está afixado para um cluster, não há uma maior possibilidade de conseguir encontrar recursos livres, uma vez que o agrupamento de recursos disponíveis está limitado a um cluster. Além disso, se o pedido de alocação está afixado para um cluster, mas o tipo de recurso pedido não é suportado nesse cluster, o pedido irá falhar mesmo se o cluster tem recurso gratuito. Diagrama 3 abaixo ilustra o caso em que uma alocação afixada falha porque o cluster de candidatos só tem de libertar recursos. Diagrama 4 ilustra as maiúsculas e minúsculas onde uma alocação afixada falha porque o cluster de candidatos apenas não suporta o tamanho da VM pedido, apesar do cluster tem de libertar recursos.

![Falha na alocação afixado](./media/cloud-services-allocation-failure/Allocation2.png)

## <a name="troubleshooting-allocation-failure-for-cloud-services"></a>Resolução de problemas de falha de alocação para serviços em nuvem
### <a name="error-message"></a>Mensagem de erro
Poderá ver a seguinte mensagem de erro:

    "Azure operation '{operation id}' failed with code Compute.ConstrainedAllocationFailed. Details: Allocation failed; unable to satisfy constraints in request. The requested new service deployment is bound to an Affinity Group, or it targets a Virtual Network, or there is an existing deployment under this hosted service. Any of these conditions constrains the new deployment to specific Azure resources. Please retry later or try reducing the VM size or number of role instances. Alternatively, if possible, remove the aforementioned constraints or try deploying to a different region."

### <a name="common-issues"></a>Problemas Comuns
Seguem-se os cenários comuns de alocação que fazer com que um pedido de alocação para ser afixado para um único cluster.

* Implementar a ranhura de teste - se um serviço em nuvem tem uma implementação em qualquer um dos ranhura, em seguida, o serviço em nuvem todo está afixado para um cluster específico.  Isto significa que se uma implementação já existe na ranhura de produção, em seguida, uma nova implementação de teste pode apenas ser alocada no mesmo cluster que a ranhura de produção. Se o cluster está prestes a capacidade, poderão falhar o pedido.
* Dimensionamento - adicionar novas instâncias num serviço em nuvem existente tem de alocar no mesmo cluster.  Pedidos de dimensionamento pequeno normalmente, pode ser atribuído, mas nem sempre. Se o cluster está prestes a capacidade, poderão falhar o pedido.
* Grupo de afinidade - uma nova implementação num serviço em nuvem em branco pode ser atribuída pelos recursos de infraestrutura em qualquer cluster nessa região, a menos que o serviço em nuvem está afixado a um grupo de afinidade. As implementações para o mesmo grupo de afinidade de serão tentadas no mesmo cluster. Se o cluster está prestes a capacidade, poderão falhar o pedido.
* Grupo de afinidade vNet - redes virtuais antigos foram associadas a grupos de afinidade em vez de regiões e serviços em nuvem nestas redes virtuais seriam fixados para o cluster do grupo de afinidade. As implementações para este tipo de rede virtual serão tentadas no cluster afixado. Se o cluster está prestes a capacidade, poderão falhar o pedido.

## <a name="solutions"></a>Soluções
1. Volte a implementar um novo serviço em nuvem - esta solução é provável que seja mais com êxito como permite que a plataforma para a escolha de todos os clusters nessa região.

   * Implementar a carga de trabalho para um novo serviço em nuvem  
   * Atualizar o CNAME ou um registo para apontar o tráfego para o novo serviço em nuvem
   * Assim que vai zero tráfego do site antigo, pode eliminar o serviço em nuvem antigo. Esta solução deverá cobrado qualquer período de indisponibilidade.
2. Eliminar produção e teste ranhuras - esta solução irá manter o seu nome DNS existente, mas irá fazer com que o período de indisponibilidade para a aplicação.

   * Eliminar a produção e teste ranhuras de um serviço em nuvem existente para que o serviço em nuvem está vazio e, em seguida,
   * Crie uma nova implementação do serviço em nuvem existente. Isto tentará novamente a alocação em todos os clusters na região. Certifique-se de que o serviço em nuvem não está associado a um grupo de afinidade.
3. IP reservado - esta solução irá preservar os IP existente de endereços, mas irá fazer com que o período de indisponibilidade para a aplicação.  

   * Criar um ReservedIP para a implementação existente com o Powershell

     ```
     New-AzureReservedIP -ReservedIPName {new reserved IP name} -Location {location} -ServiceName {existing service name}
     ```
   * Siga #2 from above, certificar-se de que especifique o novo ReservedIP no CSCFG do serviço.
4. Remova grupo de afinidade para novas implementações - grupos de afinidades já não são recomendadas. Siga os passos para #1 acima para implementar um novo serviço em nuvem. Certifique-se de que o serviço em nuvem não está num grupo de afinidade.
5. Converter a uma rede Virtual Regional - consulte [como migrar a partir de grupos de afinidade para uma rede Virtual Regional (VNet)](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).
