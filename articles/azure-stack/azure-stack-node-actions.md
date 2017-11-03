---
title: "Ações de nó de unidade de escala na pilha do Azure | Microsoft Docs"
description: "Saiba como ver o estado do nó e utilizar a ativação, desligar, drenagem e retomar ações de nó num sistema de pilha do Azure integrado."
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: dbb68b10-c721-4188-aa07-584d0cd63138
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/09/2017
ms.author: twooley
ms.openlocfilehash: 3696cd0da0859bebf001f7749ac8874efd574046
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="scale-unit-node-actions-in-azure-stack"></a>Ações de nó de unidade de escala na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas*

Este artigo descreve como visualizar o estado de uma unidade de escala e respetivos nós associadas e como utilizar as ações de nó disponível. Ações de nó incluem ligar, energia desativar, drenar, retomam e reparar. Normalmente, utilize estas ações de nó durante a substituição de campo de partes, ou para cenários de recuperação de nó.

## <a name="view-the-status-of-a-scale-unit-and-its-nodes"></a>Ver o estado de uma unidade de escala e respetivos nós

No portal do administrador, pode visualizar facilmente o estado de uma unidade de escala e respetivos nós associado.

Para ver o estado de uma unidade de escala:

1. No **gestão região** mosaico, selecione a região.
2. No lado esquerdo, em **recursos de infraestrutura**, selecione **unidades de escala**.
3. Nos resultados, selecione a unidade de escala.
 
Aqui, pode ver as seguintes informações:

- nome de região
- tipo de sistema
- núcleos lógicos totais
- total de memória
- a lista de nós individuais e o respetivo estado; encontra-se em execução ou parado.

![Mosaico de unidade de escala que mostra o estado de execução para cada nó](media/azure-stack-node-actions/ScaleUnitStatus.PNG)

## <a name="view-information-about-a-scale-unit-node"></a>Ver informações sobre um nó de unidade de escala

Se selecionar um nó individual, pode ver as seguintes informações:

- nome de região
- modelo de servidor
- Endereço IP do controlador de gestão de placas base (BMC)
- Estado operacional
- Número total de núcleos
- quantidade total de memória
 
![Mosaico de unidade de escala que mostra o estado de execução para cada nó](media/azure-stack-node-actions/NodeActions.PNG)

Também pode efetuar ações de nó de unidade de escala a partir daqui.

## <a name="scale-unit-node-actions"></a>Ações de nó de unidade de escala

Ao visualizar informações sobre um nó de unidade de escala, também pode efetuar ações de nó, tais como:

- ligar e desligar
- drenagem e retomar
- reparar

Estado operacional do nó determina quais as opções estão disponíveis.

### <a name="power-off"></a>Desligar

O **desligar** ação desativa o nó. É o mesmo que se premir o botão de energia. Faz **não** envia um sinal de encerramento para o sistema operativo. Para planeada desligar operações, certifique-se de que primeiro drenar um nó de unidade de escala.

Esta ação é normalmente utilizada quando um nó está num estado bloqueado e já não responde a pedidos.  

Para executar a operação desligar ação através do PowerShell:

  ````PowerShell
  Stop-AzsScaleUnitNode -Region <RegionName> -Name <NodeName>
  ```` 

No caso de pouco provável que a operação desligar ação não funciona, utilize a interface de web BMC em vez disso.

### <a name="power-on"></a>Ligar a

O **ligar** ativa a ação no nó. É o mesmo que se premir o botão de energia. 

Para executar a potência na ação através do PowerShell:

  ````PowerShell
  Start-AzsScaleUnitNode -Region <RegionName> -Name <NodeName>
  ````

No caso de pouco provável que a ligar a ação não funciona, utilize a interface de web BMC em vez disso.

### <a name="drain"></a>Drenagem

O **drenar** ação evacuates todas as cargas de trabalho de Active Directory por distribuição de entre os nós restantes nessa unidade de escala específico.

Esta ação é normalmente utilizada durante a substituição de campo de peças, tais como a substituição de um nó de todo.

> [!IMPORTANT]
> Certifique-se de que drenar um nó apenas durante uma janela de manutenção planeada, onde os utilizadores notificados. Em algumas condições, cargas de trabalho de Active Directory podem deparar-interrupções.

Para executar a ação de drenagem através do PowerShell:

  ````PowerShell
  Disable-AzsScaleUnitNode -Region <RegionName> -Name <NodeName>
  ````

### <a name="resume"></a>Retomar

O **retomar** ação de retoma de um nó drained e marca Active Directory para o posicionamento de cargas de trabalho. As cargas de trabalho anteriores que estavam em execução no nó não falhar novamente. (Se drenar um nó e, em seguida, desligar, ao energia o nó voltar, não está marcado como Active Directory para o posicionamento de cargas de trabalho. Quando estiver pronto, tem de utilizar a ação de retoma para marcar o nó como o Active Directory.)

Para executar a ação de retoma através do PowerShell:

  ````PowerShell
  Enable-AzsScaleUnitNode -Region <RegionName> -Name <NodeName>
  ````

### <a name="repair"></a>Reparação

O **reparação** ação repara um nó. Utilizá-lo apenas para qualquer um dos seguintes cenários:

- Substituição de nó completo (com ou sem novos discos de dados)
- Após a falha de componente de hardware e de substituição (se que aconselhado na documentação de (FRU) de unidade substituível em campo).

> [!IMPORTANT]
> Consulte a documentação de FRU do seu fornecedor de hardware de OEM para os passos exatos para quando é necessário substituir um nó ou componentes de hardware individuais. A documentação de FRU vai ser especificados se tiver de executar a ação de reparação depois de substituir um componente de hardware.  

Quando executar a ação de reparação, terá de especificar o endereço IP de BMC. 

Para executar a ação de reparação através do PowerShell:

  ````PowerShell
  Repair-AzsScaleUnitNode -Region <RegionName> -Name <NodeName> -BMCIPAddress <BMCIPAddress>
  ````


