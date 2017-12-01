---
title: "Quotas e limites de serviço para o Azure Batch | Microsoft Docs"
description: "Saiba mais sobre quotas do Azure Batch predefinidas, os limites e restrições e aumenta como pedir quota"
services: batch
documentationcenter: 
author: v-dotren
manager: timlt
editor: 
ms.assetid: 28998df4-8693-431d-b6ad-974c2f8db5fb
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 210ba4a90f24ce9b0b55c4565028232c2b7fd7cc
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2017
---
# <a name="batch-service-quotas-and-limits"></a>Quotas e limites do serviço Batch

Como com outros serviços do Azure, existem limites no certos recursos associados com o serviço Batch. Muitas destes limites são quotas predefinidas aplicadas pelo Azure da subscrição ou o nível de conta. Este artigo aborda as predefinições e como pode pedir quota aumenta.

Ter destas quotas em mente como a estrutura e a escala se as cargas de trabalho do Batch. Por exemplo, se o conjunto não alcance o número de destino de nós de computação que especificou, poderá atingiu o limite de quota de núcleos para a sua conta do Batch.

Pode executar várias cargas de trabalho do Batch numa única conta do Batch ou distribuí-las entre contas do Batch que estejam na mesma subscrição, mas em diferentes regiões do Azure.

Se planear executar cargas de trabalho de produção no Batch, poderá ter de aumentar a um ou mais das quotas acima a predefinição. Se pretender emitir uma quota, pode abrir um online [pedido de suporte ao cliente](#increase-a-quota) , sem encargos.

> [!NOTE]
> Uma quota é um limite de crédito, não é uma garantia de capacidade. Se tiver necessidades de capacidade em grande escala, contacte o suporte do Azure.
> 
> 

## <a name="resource-quotas"></a>Quotas de recursos
[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

### <a name="quotas-in-user-subscription-mode"></a>Quotas no modo de subscrição de utilizador

Se utilizou uma versão anterior da API do Batch para criar uma conta do Batch com o modo de atribuição de agrupamento definido como **subscrição de utilizador**, quotas são aplicadas de forma diferente. Neste modo, o que já não é recomendado, VMs de lote e a outros recursos são criados diretamente na sua subscrição quando é criado um conjunto. A quota de núcleos do Azure Batch não é aplicável a uma conta criada neste modo. Em vez disso, as quotas na sua subscrição para regionais compute núcleos e outros recursos são aplicados. Saiba mais sobre estas quotas no [subscrição do Azure e limites de serviço, quotas e restrições](../azure-subscription-service-limits.md).

## <a name="other-limits"></a>Outros limites
| **Recurso** | **Limite Máximo** |
| --- | --- |
| [Tarefas simultâneas](batch-parallel-node-tasks.md) por nó de computação |4 x número de núcleos de nó |
| [Aplicações](batch-application-packages.md) por conta do Batch |20 |
| Pacotes de aplicações por aplicação |40 |
| Tamanho do pacote de aplicação (cada) |Aprox 195GB<sup>1</sup> |
| Tamanho de tarefa de início máximo | 32768 carateres<sup>2</sup> |
| Duração máxima de tarefas | 7 dias<sup>3</sup> |

<sup>1</sup> limite de armazenamento do azure para o tamanho do blob de bloco máximo<br />
<sup>2</sup> inclui ficheiros de recursos e as variáveis de ambiente<br />
<sup>3</sup> a duração máxima de uma tarefa, a partir da quando é adicionada à tarefa para quando estiver concluída, é de 7 dias. Tarefas de conclusão manter indefinidamente; dados de tarefas não foi concluída dentro da duração máxima não estão acessíveis.


## <a name="view-batch-quotas"></a>Ver as quotas de Batch
Ver as quotas de conta do Batch no [portal do Azure][portal].

1. Selecione **contas do Batch** no portal, em seguida, selecione a conta do Batch estiver interessado em.
2. Selecione **Quotas** no menu da conta do Batch.
3. Ver as quotas atualmente aplicadas à conta do Batch
   
    ![Quotas de conta do batch][account_quotas]



## <a name="increase-a-quota"></a>Aumentar uma quota
Siga estes passos para pedir uma quota de aumentam a sua conta do Batch ou a sua subscrição utilizando o [portal do Azure][portal]. O tipo de aumento de quota depende o modo de atribuição de agrupamento da sua conta do Batch.

### <a name="increase-a-batch-cores-quota"></a>Aumentar uma quota de núcleos de Batch 

1. Selecione o **ajuda + suporte** mosaico no dashboard do portal do ou o ponto de interrogação (**?**) no canto superior direito do portal.
2. Selecione **novo pedido de suporte** > **Noções básicas**.
3. No **Noções básicas**:
   
    a. **Emitir tipo** > **Quota**
   
    b. Selecione a sua subscrição.
   
    c. **Tipo de quota** > **Batch**
   
    d. **Plano de suporte** > **suporte de Quota - incluído**
   
    Clique em **Seguinte**.
4. No **problema**:
   
    a. Selecione um **gravidade** acordo com seu [impacto comercial][support_sev].
   
    b. No **detalhes**, especifique cada quota que pretende alterar, o nome de conta do Batch e o novo limite.
   
    Clique em **Seguinte**.
5. No **informações de contacto**:
   
    a. Selecione um **método de contacto preferencial**.
   
    b. Certifique-se e introduza os detalhes de contactos necessários.
   
    Clique em **Criar** para submeter o pedido de suporte.

Depois de ter submetido o pedido de suporte, o suporte do Azure irá contactá-lo. Tenha em atenção que a concluir o pedido pode demorar até 2 dias úteis.


## <a name="related-topics"></a>Tópicos relacionados
* [Criar uma conta do Azure Batch no portal do Azure](batch-account-create-portal.md)
* [Descrição geral da funcionalidade do Azure Batch](batch-api-basics.md)
* [Subscrição do Azure e limites de serviço, quotas e restrições](../azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: http://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.png
