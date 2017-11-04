---
title: "Filtros de tópico de barramento de serviço do Azure | Microsoft Docs"
description: "Filtrar tópicos de Service Bus do Azure"
services: service-bus-messaging
documentationcenter: 
author: clemensv
manager: timlt
editor: 
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2017
ms.author: sethm
ms.openlocfilehash: b3fe467b7d6ae9b207956ece4980bf558a69761f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="topic-filters-and-actions"></a>Filtros de tópico e ações

Os subscritores podem definir quais as mensagens que pretendem receber de um tópico. Estas mensagens são especificadas sob a forma de uma ou várias regras de subscrição com nome. Cada regra é composta por uma condição que seleciona mensagens específicas e uma ação que annotates a mensagem selecionada. Para cada condição de regra correspondente, a subscrição produz uma cópia da mensagem, que pode ser anotada de forma diferente para cada regra correspondente.

Cada subscrição de tópico recém-criado tem uma regra de subscrição inicial predefinido. Se não especificar explicitamente uma condição de filtro para a regra, é o filtro aplicado a **verdadeiro** filtro que permite que todas as mensagens de ser selecionada para a subscrição. A regra predefinida não tem nenhuma ação de anotação associado.

Service Bus suporta três condições de filtragem:

-   *Filtros booleanos* - **TrueFilter** e **FalseFilter** a fazer com que todas as mensagens atrasadas (**verdadeiro**) ou nenhuma das mensagens atrasadas (**falso**) para ser selecionado para a subscrição.

-   *Os filtros do SQL* - um **SqlFilter** contém uma expressão condicional como o SQL Server que é avaliada no Mediador contra as mensagens atrasadas propriedades definidas pelo utilizador e as propriedades do sistema. Todas as propriedades do sistema tem de ter o prefixo `sys.` na expressão condicional. O [subconjunto de idiomas do SQL Server para condições de filtro](service-bus-messaging-sql-filter.md) testes a existência de propriedades (EXISTS), bem como para (IS NULL) de valores nulos, operadores lógicos não/e/ou, relacionais, aritmética numérica simple e padrão de texto simples correspondência com semelhante.

-   *Filtros de correlação* - um **CorrelationFilter** contém um conjunto de condições são comparado com uma ou mais das propriedades de utilizador e do sistema de uma mensagem atrasada. É uma utilização comum para correspondência de **CorrelationId** propriedade, mas a aplicação também pode optar por correspondência **ContentType**, **etiqueta**,  **MessageId**, **ReplyTo**, **ReplyToSessionId**, **SessionId**, **para**e quaisquer definido pelo utilizador Propriedades. Existe uma correspondência quando o valor de uma mensagem atrasada para uma propriedade de é igual ao valor especificado no filtro de correlação. Para as expressões de cadeia, a comparação é maiúsculas e minúsculas. Quando especificar várias propriedades de correspondência, o filtro combina-los como condição e lógica, que significa que para o filtro corresponder, todas as condições têm de corresponder.

Todos os filtros avaliar as propriedades da mensagem. Os filtros não é possível avaliar o corpo da mensagem.

Regras do filtro complexas requerem a capacidade de processamento. Em particular, a utilização de regras de filtro SQL resulta num débito de mensagem global inferior ao nível da subscrição, o tópico e o espaço de nomes. Sempre que possível, as aplicações devem escolher filtros de correlação através de filtros como o SQL Server, uma vez que estes são muito mais eficientes no processamento, pelo que tem menos impacto no débito.

## <a name="actions"></a>Ações

Com condições de filtro do SQL Server e apenas com os, pode definir uma ação que pode anotar a mensagem por adicionar, remover ou substituir as propriedades e os respetivos valores. A ação [utiliza uma expressão de tipo SQL,](service-bus-messaging-sql-filter.md) que aproximadamente ligado leans sobre a sintaxe de declaração de ATUALIZAÇÃO do SQL Server. A ação é executada na mensagem após é tem sido correspondência e antes da mensagem está selecionada para o tópico. As alterações às propriedades de mensagem estão privadas para a mensagem copiada para a subscrição.

## <a name="usage-patterns"></a>Padrões de utilização

O cenário de utilização mais simples para um tópico é que cada subscrição recebe uma cópia de cada mensagem enviada para um tópico, que permite que um padrão de difusão.

Filtros e as ações ativar dois grupos adicionais de padrões: criação de partições e o encaminhamento.

Criação de partições utiliza filtros para distribuir mensagens entre várias subscrições existentes de forma previsível e mutuamente exclusiva. O padrão de criação de partições é utilizado quando um sistema é aumentado horizontalmente para lidar com vários contextos diferentes nos compartimentos funcionalmente idênticos que cada conter um subconjunto de dados globais; Por exemplo, informações de perfil de cliente. Com a criação de partições, um publicador envia a mensagem para um tópico, sem necessidade de quaisquer dados de conhecimento do modelo de criação de partições. Em seguida, a mensagem é movida para a subscrição correta a partir da qual pode, em seguida, ser obtida pelo processador de mensagens a partição.

Encaminhamento utiliza filtros para distribuir mensagens entre subscrições de tópico de forma previsível, mas não necessariamente exclusivo. Em conjunto com o [reencaminhamento automática](service-bus-auto-forwarding.md) funcionalidade, tópico podem ser utilizados filtros para criar encaminhamentos complexos gráficos dentro de um espaço de nomes do Service Bus para distribuição de mensagem dentro de uma região do Azure. Com as funções do Azure ou do Azure Logic Apps funcionam como uma ponte entre os espaços de nomes do Service Bus do Azure, pode criar topologias global complexas com integração direta na linha de aplicações empresariais.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre mensagens do Service Bus, consulte os tópicos seguintes:

* [Noções básicas sobre o Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Sintaxe de SQLFilter](service-bus-messaging-sql-filter.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)