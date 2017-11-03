---
title: "Solução filtragem no OMS | Microsoft Docs"
description: "Filtragem de solução é uma funcionalidade no Operations Management Suite (OMS) que lhe permite limitar as soluções de gestão a um conjunto específico de agentes.  Este artigo descreve como criar uma configuração de âmbito e aplicá-la para uma solução."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1f054a4e-6243-4a66-a62a-0031adb750d8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2017
ms.author: bwren
ms.openlocfilehash: cb73a2d7ae57a5a11869259dbe913ae83ffb2b01
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-solution-targeting-in-operations-management-suite-oms-to-scope-management-solutions-to-specific-agents-preview"></a>Utilizar a solução filtragem no Operations Management Suite (OMS) para soluções de gestão do âmbito para agentes específicos (pré-visualização)
Quando adiciona uma solução para OMS, é automaticamente implementada por predefinição para todos os agentes Windows e Linux ligado à sua área de trabalho de análise de registos.  Poderá querer gerir os custos e limitar a quantidade de dados recolhidos para uma solução, limitando-la a um conjunto específico de agentes.  Este artigo descreve como utilizar **solução filtragem** que é uma funcionalidade do OMS que lhe permite aplicar um âmbito para as suas soluções.

## <a name="how-to-target-a-solution"></a>Como uma solução de destino
Existem três passos para uma solução de filtragem, tal como descrito nas secções seguintes.  Tenha em atenção que, terá do portal do OMS e portal do Azure para outros passos.


### <a name="1-create-a-computer-group"></a>1. Criar um grupo de computadores
Especifique os computadores que pretende incluir num âmbito criando um [grupo de computadores](../log-analytics/log-analytics-computer-groups.md) na análise de registos.  O grupo de computadores pode ser baseado numa pesquisa de registo ou importado a partir de outras origens, tais como grupos do Active Directory ou o WSUS. Como [descrito abaixo](#solutions-and-agents-that-cant-be-targeted), apenas os computadores que estejam ligados diretamente ao Log Analytics serão incluídos no âmbito.

Assim que tiver o grupo de computadores criado na sua área de trabalho, em seguida, irá incluir numa configuração de âmbito que pode ser aplicada a um ou mais soluções.
 
 
 ### <a name="2-create-a-scope-configuration"></a>2. Criar uma configuração de âmbito
 A **configuração de âmbito** inclui um ou mais grupos de computadores e podem ser aplicadas a um ou mais soluções. 
 
 Crie uma configuração de âmbito com o processo seguinte.  

 1. No portal do Azure, navegue para **Log Analytics** e selecione a sua área de trabalho.
 2. Nas propriedades para a área de trabalho em **origens de dados da área de trabalho** selecione **configurações de âmbito**.
 3. Clique em **adicionar** para criar uma nova configuração de âmbito.
 4. Escreva um **nome** para a configuração do âmbito.
 5. Clique em **selecionar grupos de computadores**.
 6. Selecione o grupo de computadores que criou e, opcionalmente, quaisquer outros grupos para adicionar à configuração.  Clique em **Selecionar**.  
 6. Clique em **OK** para criar a configuração do âmbito. 


 ### <a name="3-apply-the-scope-configuration-to-a-solution"></a>3. Aplica a configuração de âmbito para uma solução.
Assim que tiver uma configuração de âmbito, pode aplicá-la para soluções de um ou mais.  Tenha em atenção que enquanto uma configuração de âmbito único pode ser utilizada com soluções de vários, cada solução só pode utilizar uma configuração de âmbito.

Aplica uma configuração de âmbito com o processo seguinte.  

 1. No portal do Azure, navegue para **Log Analytics** e selecione a sua área de trabalho.
 2. Nas propriedades para a área de trabalho selecione **soluções**.
 3. Clique na solução de que pretende âmbito.
 4. Nas propriedades para a solução em **origens de dados da área de trabalho** selecione **solução filtragem**.  Se a opção não estiver disponível, em seguida, [esta solução não pode ser segmentada](#solutions-and-agents-that-cant-be-targeted).
 5. Clique em **Adicionar configuração de âmbito**.  Se já tiver uma configuração aplicada a esta solução, em seguida, esta opção estará indisponível.  Tem de remover a configuração existente antes de adicionar outro.
 6. Clique na configuração de âmbito que criou.
 7. Veja o **estado** da configuração para se certificar de que mostra **com êxito**.  Se o estado indica um erro, em seguida, clique em reticências à direita da configuração e selecionar **Editar configuração de âmbito** para fazer alterações.

## <a name="solutions-and-agents-that-cant-be-targeted"></a>Soluções e os agentes que não podem ser visados
Seguem-se os critérios para os agentes e soluções que não podem ser utilizadas com a segmentação de solução.

- Filtragem de solução aplica-se apenas para soluções que implementar agentes.
- Solução filtragem apenas se aplica a soluções fornecidas pela Microsoft.  Não é aplicável a soluções [criado por si ou parceiros](operations-management-suite-solutions-creating.md).
- Só pode filtrar os agentes que se ligam diretamente ao Log Analytics.  Soluções irão implementar automaticamente a quaisquer agentes que fazem parte de um grupo de gestão do Operations Manager ligado ou não qual estão incluídos numa configuração de âmbito.

### <a name="exceptions"></a>Exceções
Não é possível utilizar a filtragem de solução com as seguintes soluções, apesar de se ajustar os critérios declarados.

- Avaliação de estado de funcionamento do agente

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre as soluções de gestão, incluindo as soluções que estão disponíveis para instalação no seu ambiente no [soluções de gestão do Log Analytics do Azure adicionar à sua área de trabalho](../log-analytics/log-analytics-add-solutions.md).
- Saiba mais sobre como criar grupos de computadores em [pesquisas de registo de grupos de computadores na análise de registos](../log-analytics/log-analytics-computer-groups.md).