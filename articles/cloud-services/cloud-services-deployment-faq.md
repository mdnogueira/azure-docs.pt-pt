---
title: "Problemas de implementação do Microsoft Azure Cloud Services FAQ | Microsoft Docs"
description: "Este artigo apresenta uma lista de perguntas mais frequentes sobre a implementação de serviços de nuvem do Microsoft Azure."
services: cloud-services
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 9/20/2017
ms.author: genli
ms.openlocfilehash: 755b8e7414f6e77d0013d2678e8d4228091e1e4d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deployment-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Problemas de implementação do Cloud Services do Azure: Perguntas mais frequentes sobre (FAQ)

Este artigo inclui perguntas mais frequentes sobre problemas de implementação para [dos serviços de nuvem do Microsoft Azure](https://azure.microsoft.com/services/cloud-services). Também pode consultar o [página de tamanho de VM de serviços em nuvem](cloud-services-sizes-specs.md) para obter informações de tamanho.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-does-deploying-a-cloud-service-to-the-staging-slot-sometimes-fail-with-a-resource-allocation-error-if-there-is-already-an-existing-deployment-in-the-production-slot"></a>Por que motivo implementar um serviço em nuvem para o bloco de transição, por vezes, falha com um erro de alocação de recursos se já existir uma implementação existente na ranhura de produção?
Se um serviço em nuvem uma implementação de um bloco, o serviço em nuvem completo está afixado para um cluster específico. Isto significa que se uma implementação já existe na ranhura de produção, uma nova implementação de teste pode apenas ser alocada no mesmo cluster que a ranhura de produção.

Falhas de atribuição ocorrerem quando o cluster onde está localizado o seu serviço em nuvem não tem recursos suficientes computação física para satisfazer o pedido de implementação.

Para ajudar a mitigar essas falhas de atribuição, consulte [falha de alocação do serviço em nuvem: soluções](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-scaling-up-or-scaling-out-a-cloud-service-deployment-sometimes-result-in-allocation-failure"></a>Por que motivo é como aumentar verticalmente ou fora de uma implementação de serviço de nuvem, por vezes, resultar numa falha de alocação de dimensionamento?
Quando um serviço em nuvem é implementado, este normalmente obtém fixado para um cluster específico. Isto significa que o dimensionamento de segurança/fora de um serviço em nuvem existente terá de atribuir novas instâncias no mesmo cluster. Se o cluster está prestes a capacidade ou o tamanho/tipo pretendido VM não está disponível, poderá falhar o pedido.

Para ajudar a mitigar essas falhas de atribuição, consulte [falha de alocação do serviço em nuvem: soluções](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-deploying-a-cloud-service-into-an-affinity-group-sometimes-result-in-allocation-failure"></a>Por que motivo implementar um serviço em nuvem para um grupo de afinidade, por vezes, resulta numa falha de alocação?
Uma nova implementação num serviço em nuvem em branco pode ser atribuída pelos recursos de infraestrutura em qualquer cluster nessa região, a menos que o serviço em nuvem está afixado a um grupo de afinidade. As implementações para o mesmo grupo de afinidade de serão tentadas no mesmo cluster. Se o cluster está prestes a capacidade, poderão falhar o pedido.

Para ajudar a mitigar essas falhas de atribuição, consulte [falha de alocação do serviço em nuvem: soluções](cloud-services-allocation-failures.md#solutions).

## <a name="why-does-changing-vm-size-or-adding-a-new-vm-to-an-existing-cloud-service-sometimes-result-in-allocation-failure"></a>Por que motivo alterar o tamanho da VM ou adicionar uma nova VM a um serviço em nuvem existente, por vezes, resulta numa falha de alocação?
Os clusters no Centro de dados podem ter diferentes configurações dos tipos de máquina (por exemplo, uma série, série Av2, série de D, série Dv2, série de G, série H, etc.). Mas nem todos os clusters necessariamente teria todos os tipos de VMs. Por exemplo, se tentar adicionar uma série de D VM a um serviço em nuvem que já tenha sido implementado num cluster A só de série, irá ocorrer uma falha de alocação. Isto também acontecerá se tentar alterar que tamanhos de VM SKU (por exemplo, mudar de uma série de à uma série de D).

Para ajudar a mitigar essas falhas de atribuição, consulte [falha de alocação do serviço em nuvem: soluções](cloud-services-allocation-failures.md#solutions).

Para verificar os tamanhos disponíveis na sua região, consulte [Microsoft Azure: os produtos por região](https://azure.microsoft.com/regions/services).

## <a name="why-does-deploying-a-cloud-service-sometime-fail-due-to-limitsquotasconstraints-on-my-subscription-or-service"></a>Por que motivo implementar um serviço em nuvem algum tempo falha devido a limites/quotas/restrições na minha subscrição ou o serviço?
Implementação de um serviço em nuvem poderá falhar se os recursos necessários a ser alocados a excedem a quota máxima permitida para o seu serviço ao nível da região/datacenter ou predefinido. Para obter mais informações, consulte [limita a serviços em nuvem](../azure-subscription-service-limits.md#cloud-services-limits).

Também pode controlar o atual/quota de utilização para a sua subscrição no portal do: portal do Azure = > subscrições = > \<adequado a subscrição > = > "Utilização + quota".

Informações de/consumo-relacionados com a utilização de recursos também podem ser obtidas através das APIs de faturação do Azure. Consulte [utilização de recursos do Azure API (pré-visualização)](../billing/billing-usage-rate-card-overview.md#azure-resource-usage-api-preview).

## <a name="how-can-i-change-the-size-of-a-deployed-cloud-service-vm-without-redeploying-it"></a>Como alterar o tamanho de uma VM de serviço implementado na nuvem sem reimplementá-lo?
Não é possível alterar o tamanho da VM de um serviço em nuvem implementado se desejá-lo. O tamanho da VM está incorporado no CSDEF, que só pode ser atualizada com uma volte a implementar.

Para obter mais informações, consulte [como atualizar um serviço em nuvem](cloud-services-update-azure-service.md).

## <a name="why-am-i-not-able-to-deploy-cloud-services-through-service-management-apis-or-powershell-when-using-azure-resource-manager-storage-account"></a>Por que razão estou posso não conseguir implementar serviços em nuvem através de APIs de gestão de serviço ou o PowerShell ao utilizar a conta do Storage do Azure Resource Manager? 

Uma vez que o serviço em nuvem é um recurso de clássico que não é diretamente compatível com o modelo Azure Resource Manager, não pode associá-lo com as contas do Storage do Azure Resource Manager. Seguem-se algumas opções: 
 
- Implementação através da REST API.

    Quando implementa através da API de REST de gestão do serviço, foi contornar a limitação, especificando um URL SAS para o armazenamento de BLOBs, que irá funcionar com uma conta clássica e do armazenamento do Azure Resource Manager. Saiba mais sobre a propriedade 'PackageUrl' [aqui](https://msdn.microsoft.com/library/azure/ee460813.aspx).
  
- Implementar através de [portal do Azure](https://portal.azure.com).

    Isto irá trabalhar a partir de [portal do Azure](https://portal.azure.com) a chamada realiza uma proxy/shim que permite a comunicação entre os recursos do Azure Resource Manager e clássico. 
 
