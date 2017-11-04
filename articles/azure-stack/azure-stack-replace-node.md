---
title: "Substituir um nó de unidade de escala num sistema de pilha do Azure integrado | Microsoft Docs"
description: "Saiba como substituir um nó de unidade de escala física num sistema de pilha do Azure integrado."
services: azure-stack
documentationcenter: 
author: troettinger
manager: byronr
editor: 
ms.assetid: f9434689-ee66-493c-a237-5c81e528e5de
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: twooley
ms.openlocfilehash: 63ba6cedd32bcf6ea3039bc80cc4a3f3407adfa7
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2017
---
# <a name="replace-a-scale-unit-node-on-an-azure-stack-integrated-system"></a>Substituir um nó de unidade de escala num sistema de pilha do Azure integrada

*Aplica-se a: Azure pilha integrado sistemas*

Este artigo descreve o processo geral para substituir um computador físico (também referido como um *nó de unidade de escala*) numa pilha do Azure integrado no sistema. Substituição de nó de unidade de escala real passos variam com base no seu fornecedor de hardware de fabricante de equipamento original (OEM). Consulte a documentação do fornecedor campo unidade substituível em (FRU) para obter passos detalhados que são específicas para o seu sistema.

O diagrama de fluxo seguinte mostra o processo FRU geral para substituir um nó de unidade de escala completa.

![Fluxograma de processo do nó de substituição](media/azure-stack-replace-node/ReplaceNodeFlow.PNG)

* Esta ação não pode ser necessária com base na condição do hardware física.

## <a name="review-alert-information"></a>Rever as informações de alerta

Se um nó de unidade de escala estiver em baixo, receberá todos os alertas críticos seguintes:

- Nó não ligado ao controlador de rede
- Nó inacessível para o posicionamento das máquinas virtuais
- Nó de unidade de escala está offline

![Lista de alertas para a unidade de escala para baixo](media/azure-stack-replace-node/NodeDownAlerts.PNG)

Se abrir o alerta "nó de unidade de escala é offline", a descrição do alerta contém o nó de unidade de escala que está inacessível. Poderá também receber alertas adicionais na solução de monitorização OEM específico que está a ser executado no anfitrião de ciclo de vida de hardware.

![Detalhes do alerta offline do nó](media/azure-stack-replace-node/NodeOffline.PNG)

## <a name="scale-unit-node-replacement-process"></a>Processo de substituição de nó de unidade de escala

Os seguintes passos são fornecidos como uma descrição geral de alto nível do processo de substituição do nó de unidade de escala. Consulte a documentação de FRU do seu fornecedor de hardware de OEM para obter passos detalhados que são específicas para o seu sistema. Siga estes passos sem referir-se a sua documentação fornecida pelo OEM.

1. Utilize o [drenar](azure-stack-node-actions.md#scale-unit-node-actions) ação para colocar o nó de unidade de escala em modo de manutenção. Esta ação não pode ser necessária com base na condição do hardware física.
2. Se o nó ainda está ligado, utilize o [desligar](azure-stack-node-actions.md#scale-unit-node-actions) ação. Esta ação não pode ser necessária com base na condição do hardware física.
 
   > [!NOTE]
   > No caso de pouco provável que a operação desligar ação não funciona, utilize a interface de web de controlador (BMC) de gestão de placas base em vez disso.

1. Substitua o computador físico. Normalmente, isto é feito ao seu fornecedor de hardware do OEM.
2. Utilize o [reparação](azure-stack-node-actions.md#scale-unit-node-actions) ação a adicionar o novo computador físico para a unidade de escala.
3. Utilizar o ponto final com privilégios para [verificar o estado de reparação de disco virtual](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair). Com unidades de dados nova, uma tarefa de reparação de armazenamento completa pode demorar várias horas, consoante a carga de sistema e consumidas espaço.
4. Após concluir a ação de reparação, confirme que todos os alertas ativos tem sido fechados automaticamente.

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre como substituir um disco físico de acesso frequente-swappable, consulte [substituir um disco](azure-stack-replace-disk.md). 
- Para obter informações sobre como substituir um componente de hardware não frequente-swappable, consulte [substituir um componente de hardware](azure-stack-replace-component.md). 