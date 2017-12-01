---
title: "Melhores práticas para o dimensionamento automático | Microsoft Docs"
description: "Padrões de dimensionamento automático no Azure para aplicações Web, conjuntos de dimensionamento da Máquina Virtual e serviços Cloud"
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 9fa2b94b-dfa5-4106-96ff-74fd1fba4657
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2017
ms.author: ancav
ms.openlocfilehash: 4b0232db1cfe2d6a7cefd07a8194a88a84a4ffb4
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2017
---
# <a name="best-practices-for-autoscale"></a>Melhores práticas do Dimensionamento Automático
Este artigo informa melhores práticas para dimensionar automaticamente no Azure. Dimensionamento automático de Monitor do Azure aplicam-se apenas ao [conjuntos de dimensionamento de Máquina Virtual](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [serviços em nuvem](https://azure.microsoft.com/services/cloud-services/), e [Web Apps do App Service -](https://azure.microsoft.com/services/app-service/web/). Outros serviços do Azure utilizam diferentes métodos de dimensionamento.

## <a name="autoscale-concepts"></a>Conceitos de dimensionamento automático
* Um recurso pode ter apenas *um* definição de dimensionamento automático
* Uma definição de dimensionamento automático pode ter um ou mais perfis e cada perfil podem ter uma ou mais regras de dimensionamento automático.
* Uma definição de dimensionamento automático dimensiona instâncias horizontalmente, que é *saída* aumento as instâncias e *no* ao reduzir o número de instâncias.
  Uma definição de dimensionamento automático tem um valor predefinido de instâncias, máximo e mínimo.
* Uma tarefa de dimensionamento automático sempre lê a métrica associada ao dimensionar por verificar se ultrapassou o limiar configurado de escalamento horizontal ou na escala. Pode ver uma lista das métricas que dimensionamento automático pode dimensionar por em [métricas comuns de dimensionamento automático de Monitor de Azure](insights-autoscale-common-metrics.md).
* Todos os limiares são calculados a um nível de instância. Por exemplo, "escala terminar por 1 instância quando média da CPU > 80% quando a contagem de instâncias é 2", significa escalável quando a CPU média em todas as instâncias é superior a 80%.
* Sempre receber notificações de falhas por e-mail. Especificamente, o proprietário, contribuinte e leitores de recurso de destino recebem correio eletrónico. Receberá também sempre um *recuperação* e-mail quando recupera a partir de uma falha de dimensionamento automático e começa a funcionar normalmente.
* Pode optar ativamente por participar no receber uma notificação de ação de dimensionamento com êxito através de e-mail e webhooks.

## <a name="autoscale-best-practices"></a>Melhores práticas de dimensionamento automático
Utilize as seguintes melhores práticas, como utilizar o dimensionamento automático.

### <a name="ensure-the-maximum-and-minimum-values-are-different-and-have-an-adequate-margin-between-them"></a>Certifique-se de que os valores mínimos e máximo são diferentes e de ter uma margem suficiente entre os mesmos
Se tiver uma definição que tiver mínimo = 2, máximo = 2 e atual de instâncias é 2, não pode ocorrer nenhuma ação de dimensionamento. Manter uma margem adequada entre o número máximo e mínimo de instâncias, que estão incluídos. Dimensionamento automático sempre dimensiona entre estes limites.

### <a name="manual-scaling-is-reset-by-autoscale-min-and-max"></a>Dimensionamento manual é reposto pela dimensionamento automático min e max
Se atualizar manualmente a contagem de instâncias para um valor acima ou abaixo o número máximo, o motor de dimensionamento automático automaticamente dimensiona de volta para o mínimo (se abaixo) ou o número máximo (se acima). Por exemplo, defina o intervalo entre 3 e 6. Se tiver uma instância em execução, o motor de dimensionamento automático dimensiona 3 instâncias no respetiva próxima execução. Da mesma forma, este seria escala-in 8 instâncias de volta para 6 no respetiva próxima execução.  Dimensionamento manual é muito temporário, a menos que repor, bem como as regras de dimensionamento automático.

### <a name="always-use-a-scale-out-and-scale-in-rule-combination-that-performs-an-increase-and-decrease"></a>Utilize sempre uma combinação de escalamento horizontal e escala na regra que executa um aumentar e diminuir
Se utilizar apenas uma parte de combinação, dimensionamento automático escala-in que único out ou, até que o máximo ou mínimo, foi atingido.

### <a name="do-not-switch-between-the-azure-portal-and-the-azure-classic-portal-when-managing-autoscale"></a>Não mudar entre o portal do Azure e o portal clássico do Azure ao gerir o dimensionamento automático
Para serviços em nuvem e de serviços de aplicações (aplicações Web), utilize o portal do Azure (portal.azure.com) para criar e gerir definições de dimensionamento automático. Para conjuntos de dimensionamento de Máquina Virtual utilizar o PowerShell, a CLI ou a REST API para criar e gerir a definição de dimensionamento automático. Não alternar entre o portal clássico do Azure (manage.windowsazure.com) e o portal do Azure (portal.azure.com) quando gerir configurações de dimensionamento automático. Portal clássico do Azure e o back-end subjacente tem limitações. Mover para o portal do Azure para gerir o dimensionamento automático utilizando uma interface gráfica do utilizador. As opções são utilizar o dimensionamento automático do PowerShell, a CLI ou a API de REST (através do Explorador de recursos do Azure).

### <a name="choose-the-appropriate-statistic-for-your-diagnostics-metric"></a>Escolha a estatística adequada para a métrica de diagnóstico
Métricas de diagnóstico, pode optar entre *médio*, *mínimo*, *máximo* e *Total* como uma métrica de dimensionamento pelo. É a mais comum estatística *médio*.

### <a name="choose-the-thresholds-carefully-for-all-metric-types"></a>Escolha os limiares cuidadosamente para todos os tipos de métricos
Recomendamos que escolher cuidadosamente limiares diferentes de escalamento horizontal e escala-com base em situações práticas.

Iremos *não é recomendada a* definições de dimensionamento automático, como os exemplos abaixo com os valores de limiar mesmo ou é muito semelhante para out e nas condições:

* Aumentar instâncias por 1 contagem quando contagem de threads < = 600
* Diminuir instâncias por 1 contagem quando contagem de threads > = 600

Vamos ver um exemplo do que pode levar a um comportamento que pode parecer confuso. Considere a seguinte sequência.

1. Assuma existem 2 instâncias lugar e, em seguida, o número médio de threads por instância cresce para 625.
2. Dimensiona terminar de adicionar uma instância 3rd de dimensionamento automático.
3. Em seguida, partem do princípio de que a contagem de threads médio entre a instância fica para 575.
4. Antes de dimensionamento para baixo, tenta de dimensionamento automático para estimar que o estado final serão se-ampliada no. Por exemplo, 575 x 3 (contagem atual de instâncias) = 1,725 / 2 (final número de instâncias quando ampliada) = 862.5 threads. Isto significa que seria tem imediatamente escalável novamente, mesmo depois de escalado, se a contagem média de thread permaneça a mesma ou mesmo recai apenas uma pequena quantidade de dimensionamento automático. No entanto, se for expandidos de novo, todo o processo seriam repetir, originando um ciclo infinito.
5. Para evitar esta situação (denominado padrão "flapping"), dimensionamento automático não reduzir verticalmente de todo. Em vez disso, ignora e reevaluates a condição novamente da próxima vez que executa a tarefa do serviço. Isto foi confunda muitas pessoas porque o dimensionamento automático não são apresentados funcionar quando o número de threads médio foi 575.

Estimativa durante uma escala destina-se para evitar em "oscilação" situações, onde ações em escala e Escalamento horizontal continuamente aceda novamente e estabelecido. Mantenha este comportamento em mente quando escolhe os mesmos limiares de escalamento horizontal e em.

Recomendamos que escolha uma margem adequada entre o Escalamento horizontal e em limiares. Por exemplo, considere a seguinte combinação melhor regra.

* Aumentar instâncias por 1 quando contagem de CPU de % > = 80
* Diminuir instâncias por 1 contagem quando % de CPU < = 60

Neste caso  

1. Assumir que existem começar com 2 instâncias.
2. Se a % de CPU média em instâncias ficar para 80, dimensionamento automático aumenta horizontalmente de forma a adição de uma instância terceira.
3. Agora partem do princípio que ao longo do tempo de CPU de % recai e 60.
4. Regra de dimensionamento do dimensionamento automático calcula o estado final se fosse para dimensionamento. Por exemplo, 60 x 3 (contagem atual de instâncias) = 180 / 2 (final número de instâncias quando ampliada) = 90. Por isso, dimensionamento automático não escala sessão porque esta teria de escalamento horizontal novamente de imediato. Em vez disso, ignora dimensionamento para baixo.
5. Verifica a próximo hora de dimensionamento automático a CPU continua a pertencer a 50 carateres. Novo - calcula instância 50 x 3 = 150 / 2 instâncias = 75, que é inferior ao limiar de escalamento horizontal de 80, pelo que esta reduz horizontalmente com êxito para 2 instâncias.

### <a name="considerations-for-scaling-threshold-values-for-special-metrics"></a>Considerações de dimensionamento de valores de limiar com base nas métricas especiais
 Para obter métricas especiais, tais como a métrica de comprimento de armazenamento ou de fila do Service Bus, o limiar é o número médio de mensagens em fila disponíveis por número atual de instâncias. Escolha com atenção o valor de limiar para esta métrica.

Vamos ilustrar com um exemplo para se certificar de que compreende o comportamento melhor.

* Aumentar instâncias por 1 contagem quando contagem de mensagens de fila de armazenamento > = 50
* Diminuir instâncias por 1 contagem quando a contagem de mensagens de fila de armazenamento < = 10

Considere a seguinte sequência:

1. Existem 2 instâncias de fila de armazenamento.
2. Mantém futuras mensagens e ao analisar a fila de armazenamento, a contagem total lê 50. Pode assumir que dimensionamento automático deverá iniciar uma ação de escalamento horizontal. No entanto, tenha em atenção que, ainda é 50/2 = 25 mensagens por instância. Por isso, escalamento horizontal não ocorrer. Para o primeiro escalável acontecer, a contagem total de mensagens na fila de armazenamento deve ser 100.
3. Em seguida, partem do princípio de que a contagem total de mensagens atinge a 100.
4. Uma instância de fila de armazenamento 3rd é adicionada devido a uma ação de escalamento horizontal.  A próxima ação de escalamento horizontal não acontece até que a contagem total de mensagens na fila atinja 150 porque 150/3 = 50.
5. Agora, o número de mensagens na fila obtém mais pequeno. Com 3 instâncias, a primeira ação de dimensionamento no acontece quando o total de mensagens em todas as filas adicionar até 30 porque 30/3 = 10 mensagens por instância, que é o limiar de escala.

### <a name="considerations-for-scaling-when-multiple-profiles-are-configured-in-an-autoscale-setting"></a>Considerações de dimensionamento quando vários perfis são configurados na definição de dimensionamento automático
Na definição de dimensionamento automático, pode escolher um perfil predefinido, que é sempre aplicado sem qualquer dependência sobre agenda ou o tempo, ou pode escolher um perfil periódico ou um perfil para um período fixo com um intervalo de data e hora.

Quando o serviço de dimensionamento automático processa-los, verifica sempre pela seguinte ordem:

1. Perfil de data fixo
2. Perfil periódica
3. Predefinição perfil ("sempre")

Se for cumprida uma condição de perfil, dimensionamento automático não verifica se a condição de perfil seguinte abaixo do mesmo. Dimensionamento automático processa apenas um perfil de cada vez. Isto significa que o se pretender incluir também uma condição de processamento de um perfil da camada inferior, tem de incluir essas regras, bem como o perfil actual.

Vamos rever este utilizando um exemplo:

A imagem abaixo mostra uma definição de dimensionamento automático com um perfil predefinido de instâncias mínimas de instâncias de 2 e máximas = = 10. Neste exemplo, as regras são configuradas para aumentar horizontalmente quando a contagem de mensagens na fila é maior que 10 e escala-in quando a contagem de mensagens na fila é inferior a 3. Por isso, agora pode dimensionar o recurso entre instâncias de 2 e 10.

Além disso, é um perfil recorrente, definido para a segunda. Está definido para instâncias mínimo = 3 e o máximas de instâncias = 10. Isto significa na segunda-feira, verifica a primeira hora de dimensionamento automático para esta condição, se a contagem de instâncias é 2, dimensiona novo mínimo de 3. Desde que o dimensionamento automático continua a encontrar esta condição de perfil correspondência (segunda-feira), só processa as CPU regras baseadas em escala-out/in configuradas para este perfil. Neste momento, esta não verificar o comprimento da fila. No entanto, se pretender também a condição de comprimento de fila para ser verificado, deve incluir essas regras do perfil predefinido, bem como no seu perfil segunda-feira.

Da mesma forma, quando o dimensionamento automático muda para o perfil predefinido, este verifica primeiro se forem satisfeitas as condições mínimas e máxima. Se o número de instâncias no momento 12, esta reduz horizontalmente para 10, o máximo permitido para o perfil predefinido.

![Definições de dimensionamento automático](./media/insights-autoscale-best-practices/insights-autoscale-best-practices-2.png)

### <a name="considerations-for-scaling-when-multiple-rules-are-configured-in-a-profile"></a>Considerações de dimensionamento quando várias regras estiverem configuradas num perfil
Existem casos em que poderá ter de definir várias regras num perfil. O seguinte conjunto de regras de dimensionamento automático são utilizada pelos serviços quando várias regras estiverem definidas.

No *aumentar horizontalmente*, dimensionamento automático é executado se forem satisfeita nenhuma regra.
No *na escala*, todas as regras a serem satisfeitos necessitam de dimensionamento automático.

Para ilustrar, partem do princípio de que tem as seguintes regras de dimensionamento automático 4:

* Se CPU < 30%, escala-in por 1
* Se memória < 50%, escala-in por 1
* Se CPU > 75%, escalável por 1
* Se memória > 75%, escalável por 1

Em seguida, ocorre a seguir:

* Se a CPU está 76% e a memória é de 50%, iremos Escalamento horizontal.
* Se a CPU corresponde a 50% e a memória é de 76% iremos Escalamento horizontal.

Por outro lado, se CPU é 25% e a memória é de dimensionamento automático de 51% **não** na escala. Para escala-no, CPU tem de ser 29% e a memória 49%.

### <a name="always-select-a-safe-default-instance-count"></a>Selecionar sempre uma contagem de instâncias predefinida seguro
A contagem de instâncias predefinida é importante de dimensionamento automático ajusta o seu serviço para esse contagem quando métricas não estão disponíveis. Por conseguinte, selecione uma contagem de instâncias predefinida que é segura para as cargas de trabalho.

### <a name="configure-autoscale-notifications"></a>Configurar notificações de dimensionamento automático
Dimensionamento automático notifica os administradores e os contribuintes do recurso por correio eletrónico se qualquer uma das condições seguintes ocorrem:

* serviço de dimensionamento automático não consegue efetuar uma ação.
* Métricas não estão disponíveis para o serviço de dimensionamento automático tomar uma decisão de escala.
* Métricas estão disponível (recuperação) novamente para tomar uma decisão de escala.
  Para além das condições acima, pode configurar as notificações de e-mail ou webhook para ser notificado para ações de dimensionamento com êxito.
  
Também pode utilizar um alerta de registo de atividade para monitorizar o estado de funcionamento do motor de dimensionamento automático. Seguem-se exemplos para [criar um alerta de registo de atividade para monitorizar todas as operações de motor de dimensionamento automático na sua subscrição](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert) ou [criar um alerta de registo de atividade para monitorizar todos os escala de dimensionamento automático falhou em / aumentar horizontalmente operações no seu subscrição](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert).

## <a name="next-steps"></a>Passos Seguintes
- [Crie um alerta de registo de atividade para monitorizar todas as operações de motor de dimensionamento automático na sua subscrição.](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Criar um alerta de registo de atividade para monitorizar todos os escala de dimensionamento automático falhou em / aumentar horizontalmente operações na sua subscrição](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)
