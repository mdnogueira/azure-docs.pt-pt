---
title: Alertas de compreender no Log Analytics do Azure | Microsoft Docs
description: "Alertas na análise de registos identificar informações importantes no seu repositório do OMS e podem proativamente notificá-lo de problemas ou da invocação ações para tentar corrigir as entradas.  Este artigo descreve os diferentes tipos de regras de alertas e como são definidos."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 6cfd2a46-b6a2-4f79-a67b-08ce488f9a91
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/13/2017
ms.author: bwren
ms.openlocfilehash: ee11f64484a66fad06b6536a18f9b3e239fa40d5
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2017
---
# <a name="understanding-alerts-in-log-analytics"></a>Alertas de compreender na análise de registos

Alertas na análise de registos identificam informações importantes no seu repositório de análise de registos.  Este artigo fornece detalhes de regras de alerta como em projetos de análise de registos e descreve as diferenças entre os diferentes tipos de regras de alertas.

Para o processo de criação de regras de alertas, consulte os artigos seguintes:

- Criar regras de alertas utilizando [portal do Azure](log-analytics-alerts-creating.md)
- Criar regras de alertas utilizando [modelo do Resource Manager](../operations-management-suite/operations-management-suite-solutions-resources-searches-alerts.md)
- Criar regras de alertas utilizando [REST API](log-analytics-api-alerts.md)


## <a name="alert-rules"></a>Regras de alertas

Os alertas são criados pelas regras de alerta que execute automaticamente pesquisas de registo em intervalos regulares.  Se os resultados da pesquisa de registo correspondentes aos critérios de específicos, em seguida, é criado um registo de alerta.  A regra pode, em seguida, execute automaticamente uma ou mais ações para notificá-lo do alerta proativamente ou da invocação do outro processo.  Diferentes tipos de regras de alerta utilizam lógica diferente para efetuar esta análise.

![Alertas do Log Analytics](media/log-analytics-alerts/overview.png)

Regras de alerta são definidas pelos seguintes detalhes:

- **Pesquisa de registo**.  A consulta que é executada sempre que a regra de alerta é acionado.  Os registos devolvidos por esta consulta é utilizado para determinar se é criado um alerta.
- **Janela de tempo**.  Especifica o intervalo de tempo para a consulta.  A consulta devolve apenas os registos que foram criados dentro deste intervalo da hora atual.  Isto pode ser qualquer valor entre 5 minutos e 24 horas. Por exemplo, se a janela de tempo é definida como 60 minutos e a consulta é executada em 1:15 PM, é devolvido apenas os registos criados entre 12:15 PM e 1:15 PM.
- **Frequência**.  Especifica a frequência com que a consulta deve ser executada. Pode ser qualquer valor entre 5 minutos e 24 horas. Deve ser igual ou menor do que a janela de tempo.  Se o valor é maior do que a janela de tempo, em seguida, o risco de registos que está a ser omitidos.<br>Por exemplo, considere uma janela de tempo de 30 minutos e uma frequência de 60 minutos.  Se a consulta é executada em 1:00, devolve registos entre 12:30 e 1:00 PM.  Da próxima vez que executar a consulta é 2:00 quando devolvam registos entre 1:30 e 2:00.  Nunca deverá ser avaliados de quaisquer registos criados entre 1:00 e 1:30.
- **Limiar**.  Os resultados da pesquisa de registo são avaliados para determinar se deve ser criado um alerta.  O limiar é diferente para os diferentes tipos de regras de alertas.

Cada regra de alerta no Log Analytics é um dos dois tipos.  Cada um destes tipos é descrita detalhadamente nas secções que se seguem.

- **[Número de resultados](#number-of-results-alert-rules)**. Alerta único criada quando os registos de número devolvidos pela pesquisa de registo excedem um número especificado.
- **[Medida de métrica](#metric-measurement-alert-rules)**.  Alerta criada para cada objeto no resultado da pesquisa de registo com valores que excedem o limiar especificado.

As diferenças entre os tipos de regra de alerta são os seguintes.

- **Número de resultados** regra de alerta sempre criará um tempo de alerta único **medida métrica** regra de alerta cria um alerta para cada objeto que é superior ao limiar.
- **Número de resultados** regras de alerta criar um alerta quando o limiar for excedido uma única vez. **Medida de métrica** regras de alertas podem criar um alerta quando o limiar for excedido um determinado número de vezes ao longo de um intervalo de tempo específico.

## <a name="number-of-results-alert-rules"></a>Número de regras de alerta de resultados
**Número de resultados** regras de alerta criar um único alerta quando o número de registos devolvidos pela consulta pesquisa excede o limiar especificado.

### <a name="threshold"></a>Limiar
O limiar para um **número de resultados** regra de alerta é simplesmente maior ou menor do que um valor específico.  Se o número de registos devolvidos pela pesquisa de registo corresponde este critério, é criado um alerta.

### <a name="scenarios"></a>Cenários

#### <a name="events"></a>Eventos
Este tipo de regra de alerta é ideal para trabalhar com eventos, tais como registos de eventos do Windows, Syslog, e os registos personalizados.  Poderá pretender criar um alerta quando um evento de erro específico é criado ou quando são criados vários eventos de erro dentro de uma janela de tempo específico.

Para o alerta sobre um único evento, defina o número de resultados para um valor superior a 0 e a frequência e a janela de tempo para 5 minutos.  A consulta que executa cada 5 minutos e procurar a ocorrência de um único evento que foi criada desde a última vez que a consulta foi executada.  Uma frequência de tempo pode atrasar o tempo entre o evento que está a ser recolhidos e o alerta a ser criada.

Algumas aplicações podem iniciar um erro de ocasional necessariamente não deve emitir um alerta.  Por exemplo, a aplicação pode repetir o processo que criou o evento de erro e, em seguida, êxito da próxima vez.  Neste caso, não poderá criar o alerta, a menos que são criados vários eventos dentro de uma janela de tempo específico.  

Em alguns casos, poderá pretender criar um alerta na ausência de um evento.  Por exemplo, um processo pode registar eventos regulares para indicar que está a funcionar corretamente.  Se não registar um estes eventos dentro de uma janela de tempo específico, deverá ser criado um alerta.  Neste caso, deverá definir o limiar **inferior a 1**.

#### <a name="performance-alerts"></a>Alertas de desempenho
[Dados de desempenho](log-analytics-data-sources-performance-counters.md) são armazenadas como registos no repositório de OMS eventos semelhante.  Se pretender um alerta quando um contador de desempenho excede um limiar específico, esse limiar deve ser incluída na consulta.

Por exemplo, se pretendesse para o alertar quando o processador é executada mais de 90%, utilizaria uma consulta semelhante ao seguinte com o limiar para a regra de alerta **maior que 0**.

    Perf | where ObjectName=="Processor" and CounterName=="% Processor Time" and CounterValue>90

    

Se quisesse para o alertar quando o processador apresentou uma média de mais de 90% de uma janela de tempo específico, teria de utilizar uma consulta utilizando o [medir comando](log-analytics-search-reference.md#commands) com o seguinte com o limiar para a regra de alerta **maior que 0**.

    Perf | where ObjectName=="Processor" and CounterName=="% Processor Time" | summarize avg(CounterValue) by Computer | where CounterValue>90

    
>[!NOTE]
> Se a sua área de trabalho ainda não foram atualizada para o [idioma de consulta de análise de registos nova](log-analytics-log-search-upgrade.md), em seguida, as consultas acima alteraria o seguinte:`Type=Perf ObjectName=Processor CounterName="% Processor Time" CounterValue>90`
> `Type=Perf ObjectName=Processor CounterName="% Processor Time" | measure avg(CounterValue) by Computer | where AggregatedValue>90`


## <a name="metric-measurement-alert-rules"></a>Regras de alerta de métrica de medida

>[!NOTE]
> Regras de alerta de medida métrica estão atualmente em pré-visualização pública.

**Medida de métrica** regras de alerta criar um alerta para cada objeto numa consulta com um valor que excede um limiar especificado.  Têm as seguintes diferenças distintas de **número de resultados** regras de alertas.

#### <a name="log-search"></a>Pesquisas de registos
Apesar de poder utilizar qualquer consulta para um **número de resultados** regra de alerta, existem requisitos específicos a consulta para uma regra de alerta de métrica de medida.  Tem de incluir um [medir comando](log-analytics-search-reference.md#commands) para agrupar os resultados num campo específico. Este comando tem de incluir os seguintes elementos.

- **A função de agregação**.  Determina o cálculo que é executado e potencialmente uma numérica campo a agregar.  Por exemplo, **existente** irá devolver o número de registos da consulta, **avg(CounterValue)** irá devolver a média do campo CounterValue ao longo do intervalo.
- **Campo de grupo**.  É criado um registo com um valor de agregados para cada instância deste campo e pode ser gerado um alerta para cada.  Por exemplo, se pretendesse gerar um alerta para cada computador, teria de utilizar **por computador**.   
- **Intervalo**.  Define o intervalo de tempo durante o qual os dados são agregados.  Por exemplo, se tiver especificado **5minutes**, seria possível criar um registo para cada instância do campo Grupo agregada em intervalos de 5 minutos durante a janela de tempo especificada para o alerta.

#### <a name="threshold"></a>Limiar
O limiar para regras de alerta de métrica de medida é definido por um valor de agregação e um número de violações.  Se qualquer ponto de dados na pesquisa de registo exceder este valor, tem considerado uma violação.  Se o número de falhas para qualquer objeto no resultado excede o valor especificado, é criado um alerta para esse objeto.

#### <a name="example"></a>Exemplo
Considere um cenário em que pretendia um alerta se a qualquer computador excedeu a utilização do processador de 90% três vezes mais de 30 minutos.  Poderá criar uma regra de alerta com os seguintes detalhes.  

**Consulta:** desempenho | onde ObjectName = = "Processador" e CounterName = = "% de tempo do processador" | resumir AggregatedValue = avg(CounterValue) por bin (TimeGenerated, 5 m), do computador<br>
**Janela de tempo:** 30 minutos<br>
**Frequência de alerta:** 5 minutos<br>
**Valor de agregação:** excelente a 90<br>
**Alerta de Acionador com base na:** Total de falhas maior 5<br>

A consulta criaria um valor médio para cada computador em intervalos de 5 minutos.  Esta consulta iria ser executada a cada 5 minutos para os dados recolhidos através de 30 minutos anteriores.  Dados de exemplo são mostrados abaixo para três computadores.

![Resultados da consulta de exemplo](media/log-analytics-alerts/metrics-measurement-sample-graph.png)

Neste exemplo, seriam possível criar alertas separadas para srv02 e srv03, uma vez que estes infringido o limiar de 90% 3 vezes durante a janela de tempo.  Se o **alerta acionador com base na:** foram alterados para **Consecutive** , em seguida, seria possível criar um alerta apenas para srv03, uma vez que o se infringido o limiar para 3 amostras consecutivas.

## <a name="alert-records"></a>Registos de alerta
Alerta registos criados pelas regras de alerta na análise de registos tem um **tipo** de **alerta** e um **SourceSystem** de **OMS**.  Têm as propriedades na tabela seguinte.

| Propriedade | Descrição |
|:--- |:--- |
| Tipo |*Alerta* |
| SourceSystem |*OMS* |
| *Objeto*  | [Alertas de medida métrica](#metric-measurement-alert-rules) terá uma propriedade para o campo de grupo.  Por exemplo, se a pesquisa de registo de grupos no computador, o registo de alerta com tem um campo de computador com o nome do computador como o valor.
| AlertName |Nome do alerta. |
| AlertSeverity |Nível de gravidade do alerta. |
| LinkToSearchResults |Ligar a pesquisa de registo de análise de registos que devolve os registos da consulta que criou o alerta. |
| Consulta |Texto da consulta que foi executada. |
| QueryExecutionEndTime |Fim do intervalo de tempo para a consulta. |
| QueryExecutionStartTime |Início do intervalo de tempo para a consulta. |
| ThresholdOperator | Operador que foi utilizado pela regra de alerta. |
| ThresholdValue | Valor que foi utilizado pela regra de alerta. |
| TimeGenerated |Data e hora em que o alerta foi criado. |

Existem outros tipos de alerta registos criados pelo [solução de gestão de alerta](log-analytics-solution-alert-management.md) e [exporta do Power BI](log-analytics-powerbi.md).  Estes têm um **tipo** de **alerta** , mas são distinguidos pelo respetivo **SourceSystem**.


## <a name="next-steps"></a>Passos seguintes
* Instalar o [solução de gestão de alertas](log-analytics-solution-alert-management.md) para analisar alertas criados na análise de registos, juntamente com os alertas recolhidas a partir de System Center Operations Manager.
* Leia mais sobre [pesquisas de registo](log-analytics-log-searches.md) que pode gerar alertas.
* Conclua uma explicação passo a passo para [configurar um webhook](log-analytics-alerts-webhooks.md) com uma regra de alerta.  
* Aprender a escrever [runbooks na automatização do Azure](https://azure.microsoft.com/documentation/services/automation) para remediar problemas identificados através de alertas.
