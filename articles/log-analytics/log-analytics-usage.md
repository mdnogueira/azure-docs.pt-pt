---
title: "Analisar a utilização de dados do Log Analytics | Microsoft Docs"
description: "Utilize o dashboard Utilização do Log Analytics para ver a quantidade de dados que estão a ser enviados para o serviço Log Analytics e resolver problemas relativos aos motivos por que estão a ser enviadas grandes quantidades."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 74d0adcb-4dc2-425e-8b62-c65537cef270
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/21/2017
ms.author: magoedte
ms.translationtype: HT
ms.sourcegitcommit: 54774252780bd4c7627681d805f498909f171857
ms.openlocfilehash: 9a4709f298131722e9c473a19f7eee0aebf7e1e6
ms.contentlocale: pt-pt
ms.lasthandoff: 07/27/2017

---
# <a name="analyze-data-usage-in-log-analytics"></a>Analisar a utilização de dados do Log Analytics
O Log Analytics inclui informações sobre a quantidade de dados recolhidos, que computadores os enviaram e os diferentes tipos de dados enviados.  Utilize o dashboard **Utilização do Log Analytics** para ver a quantidade de dados enviados para o serviço Log Analytics. O dashboard mostra quantos dados são recolhidos por cada solução e a quantidade de dados que os seus computadores estão a enviar.

## <a name="understand-the-usage-dashboard"></a>Compreender o dashboard Utilização
O dashboard **Utilização do Log Analytics** apresenta as informações seguintes:

- Volume de dados
    - Volume de dados ao longo do tempo (baseado no seu âmbito de período de tempo atual)
    - Volume de dados por solução
    - Dados não associados a um computador
- Computadores
    - Computadores que estão a enviar dados
    - Computadores sem dados nas últimas 24 horas
- Ofertas
    - Nós Insight e Análise de Dados
    - Nós de Automatização e Controlo
    - Nós de segurança
- Desempenho
    - Tempo decorrido para recolher e indexar dados
- Lista de consultas

![dashboard de utilização](./media/log-analytics-usage/usage-dashboard01.png)

### <a name="to-work-with-usage-data"></a>Trabalhar com dados de utilização
1. Se ainda não o fez, inicie sessão no [portal do Azure](https://portal.azure.com) através da sua subscrição do Azure.
2. No menu **Hub**, clique em **Mais serviços** e, na lista de recursos, escreva **Log Analytics**. À medida que começa a escrever, a lista filtra com base na sua entrada. Clique em **Log Analytics**.  
    ![Hub do Azure](./media/log-analytics-usage/hub.png)
3. O dashboard do **Log Analytics** mostra uma lista das suas áreas de trabalho. Selecione uma área de trabalho.
4. No dashboard da *área de trabalho*, clique em **Utilização do Log Analytics**.
5. No dashboard **Utilização do Log Analytics**, clique em **Hora: últimas 24 horas**, para alterar o intervalo de tempo.  
    ![intervalo de tempo](./media/log-analytics-usage/time.png)
6. Veja os painéis da categoria de utilização que mostra áreas em que está interessado. Escolha um painel e clique num item no mesmo para ver mais detalhes na [Pesquisa de Registos](log-analytics-log-searches.md).  
    ![exemplo de painel de utilização de dados](./media/log-analytics-usage/blade.png)
7. No dashboard da Pesquisa de Registos, reveja os resultados devolvidos pela pesquisa.  
    ![exemplo de pesquisa de registos de utilização](./media/log-analytics-usage/usage-log-search.png)

## <a name="create-an-alert-when-data-collection-is-higher-than-expected"></a>Criar um alerta quando a recolha de dados for superior ao esperado
Esta secção descreve como criar um alerta se:
- O volume de dados exceder uma determinada quantidade.
- Se previr que o volume de dados vai exceder uma determinada quantidade.

Os [alertas](log-analytics-alerts-creating.md) do Log Analytics utilizam consultas de pesquisa. A consulta seguinte tem um resultado quando são recolhidos mais de 100 GB de dados nas últimas 24 horas:

`Type=Usage QuantityUnit=MBytes IsBillable=true | measure sum(div(Quantity,1024)) as DataGB by Type | where DataGB > 100`

A consulta seguinte utiliza uma fórmula simples para prever quando é que vão ser enviados mais de 100 GB de dados num dia: 

`Type=Usage QuantityUnit=MBytes IsBillable=true | measure sum(div(mul(Quantity,8),1024)) as EstimatedGB by Type | where EstimatedGB > 100`

Para alertar relativamente a volumes de dados diferentes, altere o 100 nas consultas para o número de GB para o qual quer receber o alerta.

Utilize os passos descritos em [Create an alert rule](log-analytics-alerts-creating.md#create-an-alert-rule) (Criar uma regra de alerta) para ser notificado de quando a recolha de dados for superior ao esperado.

Quando criar o alerta para a primeira consulta – quando existem mais de 100 GB de dados em 24 horas –, defina:
- O **nome** como *Volume de dados maior do que 100 GB em 24 horas*
- A **gravidade** como *Aviso*
- A **consulta de pesquisa** como `Type=Usage QuantityUnit=MBytes IsBillable=true | measure sum(div(Quantity,1024)) as DataGB by Type | where DataGB > 100`
- A **Janela de tempo** como *24 Horas*.
- A **frequência do alerta** como uma hora, pois os dados de utilização só são atualizados uma vez por hora.
- **Gerar alerta com base em** como o *número de resultados*
- O **número de resultados** como *Superior a 0*

Utilize os passos descritos em [Add actions to alert rules](log-analytics-alerts-actions.md) (Adicionar ações a regras de alertas) para configurar uma ação de e-mail, webhook ou runbook para a regra de alerta.

Quando criar o alerta para a segunda consulta – quando se previr que vai haver mais de 100 GB de dados em 24 horas –, defina:
- O **nome** como *Previsto volume de dados maior do que 100 GB em 24 horas*
- A **gravidade** como *Aviso*
- A **consulta de pesquisa** como `Type=Usage QuantityUnit=MBytes IsBillable=true | measure sum(div(mul(Quantity,8),1024)) as EstimatedGB by Type | where EstimatedGB > 100`
- A **Janela de tempo** como *3 Horas*.
- A **frequência do alerta** como uma hora, pois os dados de utilização só são atualizados uma vez por hora.
- **Gerar alerta com base em** como o *número de resultados*
- O **número de resultados** como *Superior a 0*

Quando receber um alerta, utilize os passos da secção seguinte para resolver o motivo pelo qual a utilização é superior ao esperado.

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>Resolver o motivo pelo qual a utilização é superior ao esperado
O dashboard de utilização ajuda-o a identificar o porquê de a utilização (e consequentemente o custo) ser superior ao que estava à espera.

A utilização superior deve-se a um ou a ambos os motivos abaixo:
- Estão a ser enviados para o Log Analytics mais dados do que o esperado
- Estão a ser enviados dados para o Log Analytics por mais nós do que o esperado

### <a name="check-if-there-is-more-data-than-expected"></a>Verificar se há mais dados do que o esperado 
Há duas secções principais da página de utilização que ajudam a identificar o porquê de estarem a ser recolhidos mais dados.

O gráfico *Volume de dados ao longo do tempo* mostra o volume total de dados enviados e os computadores que enviam mais dados. O gráfico na parte superior permite-lhe ver se a utilização de dados global está a crescer, permanece estável ou diminui. A lista de computadores mostra os dez computadores que estão a enviar mais dados.

O gráfico *Volume de dados por solução* mostra o volume de dados que são enviados por cada solução e as soluções que estão a enviar mais dados. O gráfico na parte superior mostra o volume total de dados que são enviados por cada solução ao longo do tempo. Com estas informações, pode determinar se uma solução está a enviar mais dados, mais ou menos a mesma quantidade de dados ou menos dados ao longo do tempo. A lista de soluções mostra as dez soluções que estão a enviar mais dados. 

Estes dois gráficos apresentam todos os dados. Alguns dados estão sujeitos a faturação, enquanto outros dados são gratuitos. Para se centrar apenas nos dados sujeitos a faturação, modifique a consulta na página de pesquisa de forma a incluir `IsBillable=true`.  

![gráficos de volumes de dados](./media/log-analytics-usage/log-analytics-usage-data-volume.png)

Observe o gráfico *Volume de dados ao longo do tempo*. Para ver as soluções e os tipos de dados que estão a enviar mais dados relativamente a um determinado computador, clique no nome do computador. Clique no nome do primeiro computador na lista.

Na captura de ecrã seguinte, o tipo de dados *Gestão de Registos / Desempenho* está a enviar mais dados para o computador. 

![volume de dados de um computador](./media/log-analytics-usage/log-analytics-usage-data-volume-computer.png)

Em seguida, regresse ao dashboard *Utilização* e veja o gráfico *Volume de dados por solução*. Para ver que computadores estão a enviar mais dados relativamente a uma solução, clique no nome da solução na lista. Clique no nome da primeira solução na lista. 

Na captura de ecrã seguinte, confirma-se que é o computador *acmetomcat* que está a enviar mais dados para a solução Gestão de Registos.

![volume de dados de uma solução](./media/log-analytics-usage/log-analytics-usage-data-volume-solution.png)

Se for necessário, execute uma análise adicional para identificar volumes de grandes dimensões dentro de uma solução ou tipo de dados. As consultas de exemplo incluem:

+ Solução de **Segurança**
  - `Type=SecurityEvent | measure count() by EventID`
+ Solução de **Gestão de Registos**
  - `Type=Usage Solution=LogManagement IsBillable=true | measure count() by DataType`
+ Tipo de dados de **Desempenho**
  - `Type=Perf | measure count() by CounterPath`
  - `Type=Perf | measure count() by CounterName`
+ Tipo de dados de **Evento**
  - `Type=Event | measure count() by EventID`
  - `Type=Event | measure count() by EventLog, EventLevelName`
+ Tipo de dados de **Syslog**
  - `Type=Syslog | measure count() by Facility, SeverityLevel`
  - `Type=Syslog | measure count() by ProcessName`
+ Tipo de dados de **AzureDiagnostics**
  - `Type=AzureDiagnostics | measure count() by ResourceProvider, ResourceId`

Utilize os seguintes passos para reduzir o volume de registos recolhidos:

| Origem do volume de dados elevado | Como reduzir o volume de dados |
| -------------------------- | ------------------------- |
| Eventos de segurança            | Selecione [eventos de segurança comuns ou mínimos](https://blogs.technet.microsoft.com/msoms/2016/11/08/filter-the-security-events-the-oms-security-collects/) <br> Altere a política de auditoria de segurança para recolher apenas os eventos necessários. Em particular, reveja a necessidade de recolher eventos para <br> - [auditar a plataforma de filtragem](https://technet.microsoft.com/library/dd772749(WS.10).aspx) <br> - [auditar o registo](https://docs.microsoft.com/windows/device-security/auditing/audit-registry)<br> - [auditar o sistema de ficheiros](https://docs.microsoft.com/windows/device-security/auditing/audit-file-system)<br> - [auditar o objeto de kernel](https://docs.microsoft.com/windows/device-security/auditing/audit-kernel-object)<br> - [auditar a manipulação de identificadores](https://docs.microsoft.com/windows/device-security/auditing/audit-handle-manipulation)<br> - [auditar o armazenamento amovível](https://docs.microsoft.com/windows/device-security/auditing/audit-removable-storage) |
| Contadores de desempenho       | Altere a [configuração do contador de desempenho](log-analytics-data-sources-performance-counters.md) para: <br> - Reduzir a frequência da recolha <br> - Reduzir o número de contadores de desempenho |
| Registos de eventos                 | Altere a [configuração do registo de eventos](log-analytics-data-sources-windows-events.md) para: <br> - Reduzir o número de registos de eventos recolhidos <br> - Recolher apenas níveis de eventos necessários. Por exemplo, não recolher eventos de nível *Informação* |
| Syslog                     | Altere a [configuração do syslog](log-analytics-data-sources-syslog.md) para: <br> - Reduzir o número de instalações recolhidas <br> - Recolher apenas níveis de eventos necessários. Por exemplo, não recolher eventos de nível *Informação* e *Depuração* |
| AzureDiagnostics           | Alterar a coleção de registo de recursos para: <br> - Reduzir o número de registos de envio de recursos do Log Analytics <br> - Recolher apenas registos necessários |
| Dados de solução de computadores que não precisam da solução | Utilize a [segmentação de soluções](../operations-management-suite/operations-management-suite-solution-targeting.md) para recolher dados apenas de grupos de computadores necessários. |

### <a name="check-if-there-are-more-nodes-than-expected"></a>Verificar se há mais nós do que o esperado
Se estiver no escalão de preço *por nó (OMS)*, é cobrado com base no número de nós e soluções que utilizar. Pode ver quantos nós de cada oferta estão a ser utilizados na secção *ofertas* do dashboard de utilização.

![dashboard de utilização](./media/log-analytics-usage/log-analytics-usage-offerings.png)

Clique em **Ver todos...** para ver a lista completa dos computadores que estão a enviar dados para a oferta selecionada.

Utilize a [segmentação de soluções](../operations-management-suite/operations-management-suite-solution-targeting.md) para recolher dados apenas de grupos de computadores necessários.


## <a name="next-steps"></a>Passos seguintes
* Veja [Pesquisas de registos no Log Analytics](log-analytics-log-searches.md) para aprender a utilizar a linguagem de pesquisa. Pode utilizar as consultas de pesquisa para executar análises adicionais aos dados de utilização.
* Utilize os passos descritos em [Create an alert rule](log-analytics-alerts-creating.md#create-an-alert-rule) (Criar uma regra de alerta) para ser notificado de quando um critério de pesquisa for cumprido.
* Utilize a [segmentação de soluções](../operations-management-suite/operations-management-suite-solution-targeting.md) para recolher dados apenas de grupos de computadores necessários
* Selecione [eventos de segurança comuns ou mínimos](https://blogs.technet.microsoft.com/msoms/2016/11/08/filter-the-security-events-the-oms-security-collects/)
* Altere a [configuração do contador de desempenho](log-analytics-data-sources-performance-counters.md)
* Altere a [configuração do registo de eventos](log-analytics-data-sources-windows-events.md)
* Altere a [configuração do syslog](log-analytics-data-sources-syslog.md)

