---
title: "Substituir um componente de hardware num nó de unidade de escala de pilha do Azure | Microsoft Docs"
description: Saiba como substituir um componente de hardware num sistema de pilha do Azure integrado.
services: azure-stack
documentationcenter: 
author: troettinger
manager: byronr
editor: 
ms.assetid: c6e036bf-8c80-48b5-b2d2-aa7390c1b7c9
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: twooley
ms.openlocfilehash: 430274025bc6cd657a733fb80feb6cd7b39a36f3
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2017
---
# <a name="replace-a-hardware-component-on-an-azure-stack-scale-unit-node"></a>Substituir um componente de hardware num nó de unidade de escala de pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas*

Este artigo descreve o processo geral para substituir os componentes de hardware não frequente-swappable. Substituição real passos variam com base no seu fornecedor de hardware de fabricante de equipamento original (OEM). Consulte a documentação do fornecedor campo unidade substituível em (FRU) para obter passos detalhados que são específicas para o sistema de pilha do Azure integrado.

Componentes não frequente-swappable incluem o seguinte:

- CPU *
- Memória *
- O controlador de gestão de motherboard/placa base (BMC) / vídeo cartão
- Adaptador de barramento do disco controlador/anfitrião (HBA) / backplane
- Adaptador de rede (NIC)
- Disco do sistema operativo *
- Unidades de dados (unidades que não suportam a troca frequente, por exemplo PCI-i suplemento cartões) *

* Estes componentes podem suportar a troca frequente, mas podem variar com base na implementação do fornecedor. Consulte a documentação de FRU do fornecedor OEM para obter passos detalhados.

O diagrama de fluxo seguinte mostra o processo FRU geral para substituir um componente de hardware não frequente-swappable.

![Diagrama de fluxo que mostra o fluxo de substituição de componente](media/azure-stack-replace-component/ReplaceComponentFlow.PNG)

* Esta ação não pode ser necessária com base na condição do hardware física.

* * Se o fornecedor do hardware OEM efetua a substituição de componente e atualiza o firmware podem variar com base no suporta o contrato.

## <a name="review-alert-information"></a>Rever as informações de alerta

O estado de funcionamento de pilha do Azure e o sistema de monitorização monitoriza o estado de funcionamento de unidades de dados que são controladas pela direta de espaços de armazenamento e de adaptadores de rede. Não monitoriza outros componentes de hardware. Para todos os outros componentes de hardware, os alertas são gerados no hardware específicos do fornecedor de solução que é executado no anfitrião de ciclo de vida de hardware de monitorização.

## <a name="component-replacement-process"></a>Processo de substituição de componente

Os seguintes passos são fornecidos como uma descrição geral de alto nível do processo de substituição de componente. Não siga estes passos sem referir-se a sua documentação de FRU fornecida pelo OEM.

1. Utilize o [drenar](azure-stack-node-actions.md#scale-unit-node-actions) ação para colocar o nó de unidade de escala em modo de manutenção. Esta ação não pode ser necessária com base na condição do hardware física.
2. Depois do nó de unidade de escala está no modo de manutenção, utilize o [desligar](azure-stack-node-actions.md#scale-unit-node-actions) ação. Esta ação não pode ser necessária com base na condição do hardware física.
 
   > [!NOTE]
   > No caso de pouco provável que a operação desligar ação não funciona, utilize a interface de web de controlador (BMC) de gestão de placas base em vez disso.

3. Substitua o componente de hardware danificado. Se o fornecedor do hardware OEM efetua a substituição de componente foi variam com base no seu contrato de suporte.  
4. Atualize o firmware. Siga o processo de atualização de firmware de um fabricante específico utilizando o anfitrião de ciclo de vida de hardware para se certificar de que o componente de hardware substituídos tem o firmware aprovado, nível aplicado. Se o fornecedor do hardware OEM efetua este passo pode variar com base no seu contrato de suporte.  
5. Utilize o [reparação](azure-stack-node-actions.md#scale-unit-node-actions) ação para colocar o nó de unidade de escala de volta para a unidade de escala.
6. Utilizar o ponto final com privilégios para [verificar o estado de reparação de disco virtual](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair). Com unidades de dados nova, uma tarefa de reparação de armazenamento completa pode demorar várias horas, consoante a carga de sistema e consumidas espaço.
7. Após concluir a ação de reparação, confirme que todos os alertas ativos tem sido fechados automaticamente.

## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre como substituir um disco físico de acesso frequente-swappable, consulte [substituir um disco](azure-stack-replace-disk.md).
- Para obter informações sobre como substituir um nó físico, consulte [substituir um nó de unidade de escala](azure-stack-replace-node.md). 