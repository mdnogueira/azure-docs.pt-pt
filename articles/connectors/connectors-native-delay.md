---
title: Adicionar um atraso nas logic apps | Microsoft Docs
description: "Descrição geral do atraso e atraso-até ações e como utilizá-los com uma aplicação lógica do Azure."
services: 
documentationcenter: 
author: jeffhollan
manager: erikre
editor: 
tags: connectors
ms.assetid: 915f48bf-3bd8-4656-be73-91a941d0afcd
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: jehollan
ms.openlocfilehash: 5f4f7052d48b4ca4ed91212d970551141e78e852
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-the-delay-and-delay-until-actions"></a>Começar a utilizar o retardamento e o atraso-até ações
Utilizando o atraso e "atraso-até" ações, pode concluir cenários de fluxo de trabalho.

Pode, por exemplo:

* Aguarde até que um dia da semana para enviar uma atualização do Estado através de correio eletrónico.
* Atrase o fluxo de trabalho até que uma chamada HTTP tem tempo a concluir antes da saída deste Estado e obter o resultado.

Para começar a utilizar a ação de atraso numa aplicação lógica, consulte o artigo [criar uma aplicação lógica](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="use-the-delay-actions"></a>Utilizar as ações de atraso
Uma ação é uma operação que é efetuada pelo fluxo de trabalho que está definido uma aplicação lógica. [Saiba mais sobre as ações](connectors-overview.md).

Eis um exemplo de sequência de como utilizar um passo de atraso numa aplicação lógica:

1. Depois de adicionar um acionador, clique em **novo passo** para adicionar uma ação.
2. Procurar **atraso** para trazer as ações de atraso. Neste exemplo, vamos selecionará **atraso**.
   
    ![Ações de atraso](./media/connectors-native-delay/using-action-1.png)
3. Conclua quaisquer propriedades da ação para configurar o atraso.
   
    ![Configuração de atraso](./media/connectors-native-delay/using-action-2.png)
4. Clique em **guardar** para publicar e ativar a aplicação lógica.

## <a name="action-details"></a>Detalhes de ação
O acionador de recorrência tem as seguintes propriedades que podem ser configuradas.

### <a name="delay-action"></a>Ação de atraso
Esta ação atrasa a execução de um determinado intervalo de tempo.
A * significa que é um campo obrigatório.

| Nome a apresentar | Nome da propriedade | Descrição |
| --- | --- | --- |
| Contagem * |Contagem |O número de unidades de tempo de atraso |
| Unidade * |unidade |A unidade de tempo: `Second`, `Minute`, `Hour`, ou`Day` |

<br>

### <a name="delay-until-action"></a>Atraso-até ação
Esta ação atrasa a execução até uma data/hora especificada.
A * significa que é um campo obrigatório.

| Nome a apresentar | Nome da propriedade | Descrição |
| --- | --- | --- |
| Ano * |carimbo de data/hora |O ano de atraso até (GMT) |
| Mês * |carimbo de data/hora |O mês de atraso até (GMT) |
| Dia * |carimbo de data/hora |O dia de atraso até (GMT) |

<br>

## <a name="next-steps"></a>Passos seguintes
Agora, experimente a plataforma e [criar uma aplicação lógica](../logic-apps/logic-apps-create-a-logic-app.md). Pode explorar os outros conectores disponíveis nas logic apps observando nosso [lista APIs](apis-list.md).

