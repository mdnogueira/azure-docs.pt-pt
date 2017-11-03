---
title: "Como criar agendas complexas e periodicidade avançada com o agendador do Azure"
description: "Como criar agendas complexas e periodicidade avançada com o agendador do Azure"
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: 
ms.assetid: 5c124986-9f29-4cbc-ad5a-c667b37fbe5a
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/18/2016
ms.author: deli
ms.openlocfilehash: 20c3e3c1cb85308cad47054c2efa87f61cae0f22
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-build-complex-schedules-and-advanced-recurrence-with-azure-scheduler"></a>Como criar agendas complexas e periodicidade avançada com o agendador do Azure
## <a name="overview"></a>Descrição geral
No Centro de um agendador do Azure é a tarefa de *agenda*. A agenda determina quando e como o programador executa a tarefa.

O agendador do Azure permite-lhe especificar diferentes agendamentos periódicos e única para uma tarefa. *Uso* agendas acionados uma vez a uma determinada hora – efetivamente, são *periódica* agendas executar apenas uma vez. Agendamentos periódicos acionados numa frequência predeterminada.

Esta flexibilidade, o agendador do Azure permite-lhe suportar uma grande variedade de cenários empresariais:

* Limpeza periódica de dados – por exemplo, todos os dias, elimine todos os tweets com mais de 3 meses
* Arquivo – por exemplo, todos os meses, push fatura histórico ao serviço de cópia de segurança
* Pedidos de dados externos – por exemplo, a cada 15 minutos, solicitar a nova ski boletim meteorológico partir NOAA
* Imagem de processamento – por exemplo, cada dia da semana, horas de ponta, utilize a comprimir imagens carregado nesse dia de informática na cloud

Neste artigo, iremos guiá-lo através de tarefas de exemplo que pode criar agendador do Azure. Podemos fornecer os dados JSON que descreve cada agenda. Se utilizar o [API REST do agendador](https://msdn.microsoft.com/library/mt629143.aspx), pode utilizar este JSON para o mesmo [criar uma tarefa do agendador do Azure](https://msdn.microsoft.com/library/mt629145.aspx).

## <a name="supported-scenarios"></a>Cenários suportados
Os exemplos muitos neste tópico mostram o leque de cenários que suporta o agendador do Azure. No geral, estes exemplos mostram como criar agendas para muitos padrões de comportamento, incluindo aqueles abaixo:

* Executar uma vez numa determinada data e hora
* Executar e repetir um número de vezes explícitas
* Executar imediatamente e repetir
* Executar e repetir cada  *n*  minutos, horas, dias, semanas ou meses, começando uma hora específica
* Executar e repetir semanal ou mensal frequência, mas apenas em dias específicos, específicos dias da semana ou dias específicos do mês
* Executar e repetir em várias vezes num período – por exemplo, a última sexta-feira e a segunda de cada mês, ou em 5 15 da manhã e as 17:15:00 todos os dias

## <a name="dates-and-datetimes"></a>As datas e DateTime
Siga as datas nas tarefas do agendador do Azure a [ISO-8601 especificação](http://en.wikipedia.org/wiki/ISO_8601) e incluir apenas a data.

Siga as referências de data / hora nas tarefas do agendador do Azure a [ISO-8601 especificação](http://en.wikipedia.org/wiki/ISO_8601) e inclua as partes de data e hora. Uma data / hora em que não especifica um desvio UTC é prestada UTC.  

## <a name="how-to-use-json-and-rest-api-for-creating-schedules"></a>Como: Utilizar JSON e a REST API para criar agendas
Para criar uma agenda simples utilizando o [API de REST do agendador do Azure](https://msdn.microsoft.com/library/mt629143), primeiro [registar a sua subscrição com um fornecedor de recursos](https://msdn.microsoft.com/library/azure/dn790548.aspx) (o nome do fornecedor do programador é *Microsoft.Scheduler*), em seguida, [criar uma coleção de tarefas](https://msdn.microsoft.com/library/mt629159.aspx)e, finalmente, [criar uma tarefa](https://msdn.microsoft.com/library/mt629145.aspx). Quando cria uma tarefa, pode especificar o agendamento e de periodicidade utilizando JSON como excerpted abaixo:

    {
        "startTime": "2012-08-04T00:00Z", // optional
         …
        "recurrence":                     // optional
        {
            "frequency": "week",     // can be "year" "month" "day" "week" "hour" "minute"
            "interval": 1,                // optional, how often to fire (default to 1)
            "schedule":                   // optional (advanced scheduling specifics)
            {
                "weekDays": ["monday", "wednesday", "friday"],
                "hours": [10, 22]                      
            },
            "count": 10,                  // optional (default to recur infinitely)
            "endTime": "2012-11-04",      // optional (default to recur infinitely)
        },
        …
    }

## <a name="overview-job-schema-basics"></a>Descrição geral: Noções básicas de esquema de tarefa
A tabela seguinte fornece uma descrição dos elementos principais relacionadas com a periodicidade e agendar uma tarefa de alto nível:

| **Nome JSON** | **Descrição** |
|:--- |:--- |
| ***startTime*** |*startTime* é uma data / hora. Para agendas simples, *startTime* é a primeira ocorrência e para agendas complexas, a tarefa começará não define que *startTime*. |
| ***Periodicidade*** |O *periodicidade* objeto Especifica as regras de periodicidade para a tarefa e a periodicidade a tarefa será executado com. O objeto de periodicidade suporta os elementos *frequência, intervalo, endTime, count,* e *agenda*. Se *periodicidade* estiver definida, *frequência* é necessário; os outros elementos do *periodicidade* são opcionais. |
| ***frequência*** |O *frequência* cadeia que representa a unidade de frequência com que a tarefa de voltar a ocorrer. Os valores suportados são *"minutos", "horas", "dia", "semanas",* ou *"mês".* |
| ***intervalo*** |O *intervalo* é um número inteiro positivo e indica que o intervalo para o *frequência* que determina a frequência com que a tarefa será executada. Por exemplo, se *intervalo* é 3 e *frequência* é "semanas", a tarefa de voltar a ocorrer todos os 3 semanas. O agendador do Azure suporta um máximo *intervalo* de 18 meses para mensal frequência, 78 semanas para a frequência de semanal ou 548 dias para a frequência diária. Para a hora e a frequência de minuto, o intervalo suportado é 1 < = *intervalo* < = 1000. |
| ***endTime*** |O *endTime* cadeia Especifica passado data / hora que não deve executar a tarefa. Não é válido para ter um *endTime* no passado. Se não *endTime* ou contagem for especificada, a tarefa será executada infinitamente. Ambos *endTime* e *contagem* não pode ser incluído na mesma tarefa. |
| ***Contagem*** |<p>O *contagem* é um número inteiro positivo (maior que zero) que especifica o número de vezes que esta tarefa deve ser executada antes de concluir.</p><p>O *contagem* representa o número de vezes que a tarefa é executada antes da determinar como concluído. Por exemplo, para uma tarefa que é executada diariamente *contagem* 5 e iniciar a data da segunda, a tarefa é concluída após a execução no sexta-feira. Se a data de início é no passado, a primeira execução é calculada desde o momento de criação.</p><p>Se não *endTime* ou *contagem* for especificado, a tarefa será executada infinitamente. Ambos *endTime* e *contagem* não pode ser incluído na mesma tarefa.</p> |
| ***agenda*** |Uma tarefa com uma frequência especificada altera a periodicidade com base numa agenda de periodicidade. A *agenda* contém modificações com base em minutos, horas, dias da semana, os dias do mês e o número da semana. |

## <a name="overview-job-schema-defaults-limits-and-examples"></a>Descrição geral: As predefinições de esquema de tarefa, os limites e exemplos
Após esta descrição geral, vamos discutir sobre cada um destes elementos em detalhe.

| **Nome JSON** | **Tipo de valor** | **Necessário?** | **Valor predefinido** | **Valores válidos** | **Exemplo** |
|:--- |:--- |:--- |:--- |:--- |:--- |
| ***startTime*** |Cadeia |Não |Nenhuma |Datas-Horas ISO 8601 |<code>"startTime" : "2013-01-09T09:30:00-08:00"</code> |
| ***Periodicidade*** |Objeto |Não |Nenhuma |Objeto de periodicidade |<code>"recurrence" : { "frequency" : "monthly", "interval" : 1 }</code> |
| ***frequência*** |Cadeia |Sim |Nenhuma |"minutos", "horas", "dia", "semanas", "meses" |<code>"frequency" : "hour"</code> |
| ***intervalo*** |Número |Não |1 |1 a 1000. |<code>"interval":10</code> |
| ***endTime*** |Cadeia |Não |Nenhuma |Valor de data-hora que representa uma hora no futuro |<code>"endTime" : "2013-02-09T09:30:00-08:00"</code> |
| ***Contagem*** |Número |Não |Nenhuma |>= 1 |<code>"count": 5</code> |
| ***agenda*** |Objeto |Não |Nenhuma |Objeto da agenda |<code>"schedule" : { "minute" : [30], "hour" : [8,17] }</code> |

## <a name="deep-dive-starttime"></a>Descrição detalhada da: *startTime*
A tabela seguinte capturas como *startTime* controla a forma de execução de uma tarefa.

| **valor de startTime** | **Sem periodicidade** | **Periodicidade. Nenhuma agenda** | **Periodicidade com base numa agenda** |
|:--- |:--- |:--- |:--- |
| **Hora de início não** |Executar imediatamente uma vez |Execute imediatamente uma vez. Execute as execuções subsequentes com base em calcular a partir do último tempo de execução |<p>Executar imediatamente uma vez</p><p>Executa as execuções subsequentes com base na agenda de periodicidade.</p> |
| **Hora de início no passado** |Executar imediatamente uma vez |<p>Calcular o tempo de execução futuro primeiro após a hora de início e executadas a essa hora</p><p>Executar oncalculating execuções subsequentes com base na hora da última execução</p><p>Consulte o exemplo após esta tabela para obter uma explicação mais</p> |<p>Tarefa começa *não sooner que* a hora de início especificada. A primeira ocorrência é com base na agenda calculada a partir da hora de início</p><p>Executa as execuções subsequentes com base na agenda de periodicidade.</p> |
| **Hora de início no futuro ou no presente** |Executar uma vez à hora de início especificada |<p>Executar uma vez à hora de início especificada</p><p>Execute as execuções subsequentes com base em calcular a partir do último tempo de execução</p> |<p>Tarefa começa *não sooner que* a hora de início especificada. A primeira ocorrência é com base na agenda calculada a partir da hora de início</p><p>Executa as execuções subsequentes com base na agenda de periodicidade.</p> |

Vamos ver um exemplo do que acontece onde *startTime* está no passado, com *periodicidade* mas nenhuma *agenda*.  Partem do princípio de que a hora atual é 2015-04-08 13:00, *startTime* é 2015-04-07 14:00, e *periodicidade* é de 2 dias (definido com *frequência*: dia e *intervalo*: 2.) Tenha em atenção que o *startTime* está no passado e ocorre antes da hora atual

Nas seguintes condições, o *primeira execução* serão 2015-04-09 no 14:00\. O motor do Scheduler calcula as ocorrências de execução a partir da hora de início.  Quaisquer instâncias no passado são eliminadas. O motor utiliza a instância seguinte que ocorre no futuro.  Por isso, neste caso, *startTime* é 2015-04-07 2:00 pm, pelo que a instância seguinte é 2 dias a esse tempo, o que é 2015-04-09 às 2:00 pm.

Tenha em atenção que a primeira execução deverá ser o mesmo se, mesmo que a startTime 2015-04-05 14:00 ou 14:00\ 2015-04-01. Após a primeira execução, as execuções subsequentes são calculadas utilizando agendada –, de modo que seria em 2015-04-11 às 2:00 pm, em seguida, 2015-04-13 às 2:00 pm, em seguida, 2015-04-15 em 2:00 pm, etc.

Por fim, quando uma tarefa tem uma agenda, se horas e/ou minutos não se encontram definidos na agenda, são a predefinição de horas e minutos da primeira execução, respetivamente.

## <a name="deep-dive-schedule"></a>Descrição detalhada da: *agenda*
Um lado, um *agenda* pode *limite* o número de execuções de tarefa.  Por exemplo, se uma tarefa com uma frequência de "mês" tem um *agenda* que é executado num único dia 31, a tarefa é executada em apenas os meses que tenham um 31<sup>st</sup> dia.

Por outro lado, um *agenda* também pode *expanda* o número de execuções de tarefa. Por exemplo, se uma tarefa com uma frequência de "mês" tem um *agenda* que é executado em dias do mês, 1 e 2, a tarefa é executada no 1<sup>st</sup> e 2<sup>nd</sup> dias do mês em vez de uma só vez por mês.

Se forem especificados vários elementos de agendamento, a ordem de avaliação é desde a maior à mais pequena – semana number, dia do mês, dia da semana, horas e minutos.

A tabela seguinte descreve *agenda* elementos em detalhe.

| **Nome JSON** | **Descrição** | **Valores válidos** |
|:--- |:--- |:--- |
| **minutos** |Minutos da hora em que a tarefa será executada |<ul><li>Número inteiro, ou</li><li>Matriz de números inteiros</li></ul> |
| **horas** |Horas do dia em que a tarefa será executada |<ul><li>Número inteiro, ou</li><li>Matriz de números inteiros</li></ul> |
| **dias da semana** |Dias da semana a tarefa será executada. Só podem ser especificados com uma frequência semanal. |<ul><li>"Segunda-feira", "Terça-feira", "Quarta-feira", "Quinta-feira", "Sexta-feira", "Sábado" ou "Domingo"</li><li>Matriz de qualquer um dos valores acima (tamanho da matriz máximo 7)</li></ul>*Não* maiúsculas e minúsculas |
| **monthlyOccurrences** |Determina os dias do mês em que a tarefa será executada. Só podem ser especificadas com uma frequência semanal. |<ul><li>Matriz de objetos de monthlyOccurrence:</li></ul> <pre>{ "day": *day*,<br />  "occurrence": *occurrence*<br />}</pre><p> *dia* é o dia da semana em que a tarefa será executada, por exemplo, {Domingo} é cada Domingo do mês. Necessário.</p><p>A ocorrência é *ocorrência* do dia durante o mês, por exemplo, Domingo, -1 é Domingo último do mês. Opcional.</p> |
| **dias do mês** |Dia do mês que a tarefa será executada. Só podem ser especificadas com uma frequência semanal. |<ul><li>Qualquer valor < = -1 e > =-31.</li><li>Qualquer valor > = 1 e < = 31.</li><li>Uma matriz dos valores acima</li></ul> |

## <a name="examples-recurrence-schedules"></a>Exemplos: As agendas de periodicidade
Seguem-se vários exemplos de agendas de periodicidade – concentrar-se o objeto de agenda e os respetivos elementos secundárias.

As agendas abaixo todas partem do princípio de que o *intervalo* está definido para 1\. Além disso, um têm de assumir a frequência da direita em accordance para o que está a ser o *agenda* – por exemplo, não é possível utilizar a frequência de "dia" e ter uma modificação de "dias do mês" na agenda. Estas restrições descritas acima.

| **Exemplo** | **Descrição** |
|:--- |:--- |
| <code>{"hours":[5]}</code> |Executar em 5 AM todos os dias. O agendador do Azure corresponde à cópia de segurança cada valor na "horas", com cada valor em "minutos", um por um, para criar uma lista de todas as vezes que a tarefa está a ser executada. |
| <code>{"minutes":[15], "hours":[5]}</code> |Executar em 5:15 AM todos os dias |
| <code>{"minutes":[15], "hours":[5,17]}</code> |Executar em 5 15 da Manhã e as 17:15:00 todos os dias |
| <code>{"minutes":[15,45], "hours":[5,17]}</code> |Executar em 5:15 AM, 5:45 AM, as 17:15:00 e as 17:45:00 todos os dias |
| <code>{"minutes":[0,15,30,45]}</code> |Execute a cada 15 minutos |
| <code>{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}</code> |Execute a cada hora. Esta tarefa é executada a cada hora. O minuto é controlado pelo *startTime*, se for especificado, ou se for especificado nenhum, tempo de criação. Por exemplo, se a hora de início ou a hora de criação (que se aplica) for 12:25 PM, a tarefa será executada às 00:25, 01:25, 02:25,..., 23:25. A agenda é equivalente ao facto de uma tarefa com *frequência* de "horas", uma *intervalo* de 1 e não *agenda*. A diferença é que este agendamento pode ser utilizado com diferentes *frequência* e *intervalo* para criar outras tarefas demasiado. Por exemplo, se o *frequência* foram "mês", a agenda executaria uma só vez por mês, em vez de todos os dias se *frequência* foram "dia" |
| <code>{minutes:[0]}</code> |Execute a cada hora em hora. Esta tarefa também é executada a cada hora, mas na hora (por exemplo, 12: 00, 1: 00, 2 AM, etc.) Isto é equivalente para uma tarefa com frequência de "horas", uma startTime com zero minutos e não agendamento se a frequência foram "dia", mas se a frequência "semana" ou "mês", a agenda iria executar apenas um dia, uma semana ou um dia, mês, respetivamente. |
| <code>{"minutes":[15]}</code> |Executar em 15 minutos decorridos desde a hora de cada hora. É executada de hora em hora, com início às 00:15, 01:15, 02:15, etc., e fim às 22:15 e 23:15. |
| <code>{"hours":[17], "weekDays":["saturday"]}</code> |Execute as de 17: 00 em Saturdays todas as semanas |
| <code>{hours":[17], "weekDays":["monday", "wednesday", "friday"]}</code> |Execute as de 17: 00 na segunda-feira, Quartas e sexta-feira todas as semanas |
| <code>{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}</code> |Execute as 17:15:00 e as 17:45:00 na segunda-feira, Quartas e sexta-feira todas as semanas |
| <code>{"hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}</code> |Executar em AM de 5 e 5 PM na segunda-feira, Quartas e sexta-feira todas as semanas |
| <code>{"minutes":[15,45], "hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}</code> |Executar em 5:15 AM, 5:45 AM, as 17:15:00 e as 17:45:00 na segunda-feira, Quartas e sexta-feira todas as semanas |
| <code>{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}</code> |É executada a cada 15 minutos nos dias de semana |
| <code>{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}</code> |Execute a cada 15 minutos em dias da semana entre as 09: 00 e 4:45 PM |
| <code>{"weekDays":["sunday"]}</code> |Executar em Sundays na hora de início |
| <code>{"weekDays":["tuesday", "thursday"]}</code> |Executar em executado às Terças e às quintas-hora de início |
| <code>{"minutes":[0], "hours":[6], "monthDays":[28]}</code> |Executar às 6: 00 em 28th dia de cada mês (pressupondo frequência do mês) |
| <code>{"minutes":[0], "hours":[6], "monthDays":[-1]}</code> |Execute às 6: 00 no último dia do mês. Se gostaria de executar uma tarefa no último dia de um mês, utilize -1 em vez de dia 28, 29, 30 ou 31. |
| <code>{"minutes":[0], "hours":[6], "monthDays":[1,-1]}</code> |Executar às 6: 00 no primeiro e último dia de cada mês |
| <code>{monthDays":[1,-1]}</code> |Executar no primeiro e último dia de cada mês da hora de início |
| <code>{monthDays":[1,14]}</code> |Executar no primeiro e Fourteenth dia de cada mês da hora de início |
| <code>{monthDays":[2]}</code> |Executar no segundo dia do mês na hora de início |
| <code>{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}</code> |Executar na primeira sexta-feira de cada mês às 5 AM |
| <code>{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}</code> |: Executar na primeira sexta-feira de cada mês da hora de início |
| <code>{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}</code> |Executar no terceiro sexta-feira do fim do mês, todos os meses, a hora de início |
| <code>{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}</code> |Executar na primeira e última sexta-feira de cada mês às 5:15 AM |
| <code>{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}</code> |Executado na primeira e última sexta-feira de cada mês na hora de início |
| <code>{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}</code> |Execute o quinto sexta-feira de cada mês da hora de início. Se não houver nenhuma sexta-feira quinta num mês, isto não é executado, desde que foi agendada para execução num só quinta sextas. Pode considerar utilizar -1 em vez de 5 para a ocorrência se pretender executar a tarefa em ocorrer última sexta-feira do mês. |
| <code>{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}</code> |Execute a cada 15 minutos na última sexta-feira do mês |
| <code>{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}</code> |Executar em 5:15 AM, 5:45 AM, as 17:15:00 e 5:45 PM o 3rd quarta-feira de cada mês |

## <a name="see-also"></a>Veja Também
 [O que é o Scheduler?](scheduler-intro.md)

 [Conceitos, terminologia e hierarquia de entidades do Azure Scheduler](scheduler-concepts-terms.md)

 [Começar a utilizar o Scheduler no portal do Azure](scheduler-get-started-portal.md)

 [Planos e faturação no Azure Scheduler](scheduler-plans-billing.md)

 [Referência da API REST do Azure Scheduler](https://msdn.microsoft.com/library/mt629143)

 [Referência de cmdlets do PowerShell do Azure Scheduler](scheduler-powershell-reference.md)

 [Elevada disponibilidade e fiabilidade do Azure Scheduler](scheduler-high-availability-reliability.md)

 [Limites, predefinições e códigos de erro do Azure Scheduler](scheduler-limits-defaults-errors.md)

 [Autenticação de saída do Azure Scheduler](scheduler-outbound-authentication.md)

