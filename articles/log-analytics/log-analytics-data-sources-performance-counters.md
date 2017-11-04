---
title: Recolher e analisar os contadores de desempenho Log Analytics do Azure | Microsoft Docs
description: "Contadores de desempenho são recolhidos através da análise de registos para analisar o desempenho em agentes Windows e Linux.  Este artigo descreve como configurar a recolha de contadores de desempenho para ambas as janelas e agentes do Linux, os detalhes do são armazenados no repositório OMS e como analisá-las no portal do OMS."
services: log-analytics
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 20e145e4-2ace-4cd9-b252-71fb4f94099e
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2017
ms.author: magoedte
ms.openlocfilehash: d0345155b2c13bd0b4341ce53272e7d84cd233fb
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2017
---
# <a name="windows-and-linux-performance-data-sources-in-log-analytics"></a>Windows e Linux origens de dados de desempenho na análise de registos
Os contadores de desempenho do Windows e Linux fornecem informações aprofundadas o desempenho dos componentes de hardware, sistemas operativos e aplicações.  Análise de registos pode recolher contadores de desempenho em intervalos frequentes para análise de quase em Tempo Real (NRT) para além de relatórios e agregar dados de desempenho para uma análise mais longo prazo.

![Contadores de desempenho](media/log-analytics-data-sources-performance-counters/overview.png)

## <a name="configuring-performance-counters"></a>Configurar os contadores de desempenho
Configurar os contadores de desempenho no portal do OMS do [menu dados nas definições de análise do registo](log-analytics-data-sources.md#configuring-data-sources).

Quando configura o Windows ou Linux desempenho contadores para uma nova área de trabalho do OMS pela primeira vez, é-lhe dada a opção para criar rapidamente vários contadores comuns.  São listados com uma caixa de verificação junto a cada.  Certifique-se de que os contadores que pretende criar inicialmente são verificados e, em seguida, clique em **adicionar os contadores de desempenho selecionados**.

Para contadores de desempenho do Windows, pode escolher uma instância específica para cada contador de desempenho. Para contadores de desempenho do Linux, a instância de cada contador que escolher se aplica a todos os contadores de subordinados do contador principal. A tabela seguinte mostra as instâncias comuns disponíveis para os contadores de desempenho de Linux e Windows.

| Nome da instância | Descrição |
| --- | --- |
| \_Total |Total de todas as instâncias |
| \* |Todas as instâncias |
| (/ &#124; / var) |Corresponde a instâncias com o nome: / ou /var |

### <a name="windows-performance-counters"></a>Contadores de desempenho do Windows

![Configurar os contadores de desempenho do Windows](media/log-analytics-data-sources-performance-counters/configure-windows.png)

Siga este procedimento para adicionar um novo contador de desempenho do Windows para recolher.

1. Escreva o nome do contador na caixa de texto no formato *objeto (instância) \counter*.  Quando começar a escrever, é-lhe apresentada uma lista dos contadores comuns correspondente.  Pode selecionar um contador na lista ou escreva um dos seus.  Também pode devolver todas as instâncias para um determinado contador especificando *object\counter*.  

    Aquando da recolha de contadores de desempenho de SQL Server de instâncias nomeadas, todos os com o nome início de contadores de instância com *MSSQL$* e seguido do nome da instância.  Por exemplo, para recolher o contador de rácio de acertos na Cache de registo para todas as bases de dados do objeto de desempenho da base de dados SQL com o nome da instância INST2, especificar `MSSQL$INST2:Databases(*)\Log Cache Hit Ratio`.

2. Clique em  **+**  ou prima **Enter** para adicionar o contador à lista.
3. Quando adiciona um contador, utiliza a predefinição de 10 segundos para o respetivo **intervalo de amostra**.  Pode alterar isto para um valor mais alto de segundos até 1800 (30 minutos) se quiser reduzir os requisitos de armazenamento dos dados de desempenho recolhidos.
4. Quando tiver terminado a adição de contadores, clique em de **guardar** botão na parte superior do ecrã, para guardar a configuração.

### <a name="linux-performance-counters"></a>Contadores de desempenho do Linux

![Configurar os contadores de desempenho do Linux](media/log-analytics-data-sources-performance-counters/configure-linux.png)

Siga este procedimento para adicionar um novo contador de desempenho do Linux para recolher.

1. Por predefinição, todas as alterações de configuração são automaticamente enviadas por push para todos os agentes.  Para agentes Linux, é enviado um ficheiro de configuração para o recoletor de dados Fluentd.  Se pretender modificar este ficheiro manualmente em cada agente do Linux, em seguida, desmarque a caixa *aplicar configuração abaixo aos meus computadores Linux* e siga as instruções abaixo.
2. Escreva o nome do contador na caixa de texto no formato *objeto (instância) \counter*.  Quando começar a escrever, é-lhe apresentada uma lista dos contadores comuns correspondente.  Pode selecionar um contador na lista ou escreva um dos seus.  
3. Clique em  **+**  ou prima **Enter** para adicionar o contador para a lista de outros contadores para o objeto.
4. Todos os contadores para um objeto de utilizar a mesma **intervalo de amostra**.  A predefinição é 10 segundos.  Alterar isto para um valor mais alto de segundos até 1800 (30 minutos) se quiser reduzir os requisitos de armazenamento dos dados de desempenho recolhidos.
5. Quando tiver terminado a adição de contadores, clique em de **guardar** botão na parte superior do ecrã, para guardar a configuração.

#### <a name="configure-linux-performance-counters-in-configuration-file"></a>Configurar os contadores de desempenho do Linux no ficheiro de configuração
Em vez de configurar os contadores de desempenho de Linux através do portal do OMS, tem a opção de editar ficheiros de configuração no agente Linux.  Métricas de desempenho para recolher são controladas mediante a configuração no **/etc/optar ativamente por participar/microsoft/omsagent/\<id da área de trabalho\>/conf/omsagent.conf**.

Cada objeto ou a categoria de métricas de desempenho para recolher deve ser definida no ficheiro de configuração como um único `<source>` elemento. A sintaxe segue o padrão abaixo.

    <source>
      type oms_omi  
      object_name "Processor"
      instance_regex ".*"
      counter_name_regex ".*"
      interval 30s
    </source>


Os parâmetros neste elemento são descritos na seguinte tabela.

| Parâmetros | Descrição |
|:--|:--|
| objeto\_nome | Nome do objeto para a coleção. |
| instância\_regex |  A *expressão regular* definir as instâncias para recolher. O valor: `.*` Especifica todas as instâncias. Para recolher a métrica de processador para apenas o \_Total instância, pode especificar `_Total`. Para recolher a métrica de processo para apenas as instâncias crond ou sshd, pode especificar: ' (crond\|sshd)'. |
| contador\_nome\_regex | A *expressão regular* definir que contadores (para o objeto) para recolher. Para recolher todos os contadores para o objeto, especifique: `.*`. Para recolher contadores para espaço de comutação apenas para o objeto de memória, por exemplo, pode especificar:`.+Swap.+` |
| intervalo | Frequência com que são recolhidos contadores do objeto. |


A tabela seguinte lista os objetos e os contadores que pode especificar no ficheiro de configuração.  Há contadores adicionais disponíveis para determinadas aplicações conforme descrito em [recolher contadores de desempenho de aplicações do Linux na análise de registos](log-analytics-data-sources-linux-applications.md).

| Nome do objeto | Nome do contador |
|:--|:--|
| Disco lógico | % De Inodes livres |
| Disco lógico | % De espaço livre |
| Disco lógico | % De Inodes utilizados |
| Disco lógico | % De espaço utilizado |
| Disco lógico | Bytes lidos/seg de disco |
| Disco lógico | Leituras de disco/seg |
| Disco lógico | Transferência de disco/seg |
| Disco lógico | Bytes de escrita de disco/seg |
| Disco lógico | Escritas de disco/seg |
| Disco lógico | Megabytes livres |
| Disco lógico | Bytes de disco lógico/seg |
| Memória | % De memória disponível |
| Memória | % De espaço de comutação disponível |
| Memória | % De memória utilizada |
| Memória | % De espaço de comutação utilizado |
| Memória | Memória disponível em MBytes |
| Memória | Comutação em MBytes disponíveis |
| Memória | Leituras de paginações/seg |
| Memória | Páginas escritas/seg |
| Memória | Páginas/seg |
| Memória | Espaço de comutação \ MBytes disponíveis |
| Memória | Memória utilizada em MBytes |
| Rede | Total de Bytes transmitidos |
| Rede | Total de Bytes recebidos |
| Rede | Total de Bytes |
| Rede | Total de pacotes transmitidos |
| Rede | Total de pacotes recebidos |
| Rede | Erros de total Rx |
| Rede | Erros de total Tx |
| Rede | Colisões totais |
| Disco físico | Média Disco seg/leitura |
| Disco físico | Média Disco seg/transferência |
| Disco físico | Média Disco seg/escrita |
| Disco físico | Bytes de disco físico/seg |
| Processo | Tempo privilegiado PCT |
| Processo | Tempo de utilizador PCT |
| Processo | KBytes de memória utilizada |
| Processo | Memória partilhada virtual |
| Processador | % De tempo DPC |
| Processador | % De tempo inativo |
| Processador | % De tempo de interrupção |
| Processador | % De tempo de espera de e/s |
| Processador | % De tempo nice |
| Processador | % De tempo privilegiado |
| Processador | % Tempo do processador |
| Processador | % De tempo de utilizador |
| Sistema | Memória física livre |
| Sistema | Espaço livre em ficheiros de paginação |
| Sistema | Memória livre do Virtual |
| Sistema | Processos |
| Sistema | Tamanho armazenado em ficheiros de paginação |
| Sistema | Tempo de atividade |
| Sistema | Utilizadores |


Segue-se a configuração predefinida para as métricas do desempenho.

    <source>
      type oms_omi
      object_name "Physical Disk"
      instance_regex ".*"
      counter_name_regex ".*"
      interval 5m
    </source>

    <source>
      type oms_omi
      object_name "Logical Disk"
      instance_regex ".*
      counter_name_regex ".*"
      interval 5m
    </source>

    <source>
      type oms_omi
      object_name "Processor"
      instance_regex ".*
      counter_name_regex ".*"
      interval 30s
    </source>

    <source>
      type oms_omi
      object_name "Memory"
      instance_regex ".*"
      counter_name_regex ".*"
      interval 30s
    </source>

## <a name="data-collection"></a>Recolha de dados
Análise de registos recolhe todos os contadores de desempenho especificado no respetivo intervalo de amostra especificado em todos os agentes que tenham que contador instalado.  Os dados não são agregados e os dados não processados estão disponíveis em todas as vistas de pesquisa de registo para a duração especificada pela sua subscrição do OMS.

## <a name="performance-record-properties"></a>Propriedades de registo de desempenho
Registos de desempenho de ter um tipo de **desempenho** e ter as propriedades na tabela seguinte.

| Propriedade | Descrição |
|:--- |:--- |
| Computador |Computador que o evento foi recolhido a partir de. |
| CounterName |Nome do contador de desempenho |
| CounterPath |Caminho completo do contador sob a forma de \\ \\ \<computador >\\object(instance)\\contador. |
| CounterValue |Valor numérico do contador. |
| InstanceName |Nome da instância do evento.  Vazia se não existe uma instância. |
| ObjectName |Nome do objeto de desempenho |
| SourceSystem |Tipo de agente que foi recolhidos os dados a partir de. <br><br>Ligar OpsManager – o agente do Windows, é direta ou SCOM <br> Linux – todos os agentes Linux  <br> AzureStorage – diagnóstico do Azure |
| TimeGenerated |Data e hora que de dados foi o objeto de amostragem. |

## <a name="sizing-estimates"></a>Estimativas de dimensionamento
 Uma estimativa aproximada para a coleção de um contador específico em intervalos de 10 segundo é cerca de 1 MB por dia por instância.  Pode calcular os requisitos de armazenamento de um contador com a seguinte fórmula específico.

    1 MB x (number of counters) x (number of agents) x (number of instances)

## <a name="log-searches-with-performance-records"></a>Registo de pesquisas com registos de desempenho
A tabela seguinte fornece exemplos diferentes de pesquisas de registo que obter registos de desempenho.

| Consulta | Descrição |
|:--- |:--- |
| Desempenho |Todos os dados de desempenho |
| Desempenho &#124; onde computador = = "MyComputer" |Todos os dados de desempenho de um computador específico |
| Desempenho &#124; onde CounterName = = "Comprimento de fila de disco atual" |Todos os dados de desempenho para um determinado contador |
| Desempenho &#124; onde ObjectName = = "Processador" e CounterName = = "% tempo do processador" e InstanceName = = total"&#124; resumir AVGCPU = avg(Average) por computador |Utilização média da CPU em todos os computadores |
| Desempenho &#124; onde CounterName = = "% tempo do processador" &#124; resumir AggregatedValue = max(Max) por computador |Máximo de utilização da CPU em todos os computadores |
| Desempenho &#124; onde ObjectName = = "Disco lógico" e CounterName = = "Comprimento de fila de disco atual" e o computador = = "MyComputerName" &#124; resumir AggregatedValue = avg(Average) por InstanceName |Duração média da fila de disco atual em todas as instâncias de um computador específico |
| Desempenho &#124; onde CounterName = = "DiskTransfers/seg" &#124; resumir AggregatedValue = percentil (médio, 95) por computador |95th percentil de transferências de disco/seg em todos os computadores |
| Desempenho &#124; onde CounterName = = "% tempo do processador" e InstanceName = = total"&#124; resumir AggregatedValue = avg(CounterValue) por bin (TimeGenerated, 1h), do computador |Média de hora a hora de utilização da CPU em todos os computadores |
| Desempenho &#124; onde computador = = "MyComputer" e CounterName startswith_cs "%" e InstanceName = = total"&#124; resumir AggregatedValue = percentil (CounterValue, 70) por bin (TimeGenerated, 1h), CounterName | Percentil 70 por hora de cada contador de percentagem de % para um computador específico |
| Desempenho &#124; onde CounterName = = "% tempo do processador" e InstanceName = = total"e o computador = ="MyComputer"&#124; resumir ["min(CounterValue)"] = min(CounterValue), ["avg(CounterValue)"] = avg(CounterValue), ["percentile75(CounterValue)"] = percentil (CounterValue, 75), ["max(CounterValue)"] = max(CounterValue) por bin (TimeGenerated, 1h), do computador |Hora a hora média, mínimo, máxima e 75 percentil utilização da CPU para um computador específico |
| Desempenho &#124; onde ObjectName = = "MSSQL$ INST2: bases de dados" e InstanceName = = "principal" | Todos os dados de desempenho do objeto de desempenho da base de dados para a base de dados mestra do SQL Server com o nome de instância INST2.  

## <a name="viewing-performance-data"></a>Visualizar dados de desempenho
Quando executa uma pesquisa de registo de dados de desempenho, a **lista** vista é apresentada por predefinição.  Para ver os dados no formulário gráfico, clique em **métricas**.  Para uma visualização gráfica detalhada, clique o  **+**  junto de um contador.  

![Vista de métricas fechada](media/log-analytics-data-sources-performance-counters/metricscollapsed.png)

Agregar dados de desempenho uma pesquisa de registo, consulte [agregação métrica a pedido e visualização no OMS](http://blogs.technet.microsoft.com/msoms/2016/02/26/on-demand-metric-aggregation-and-visualization-in-oms/).


## <a name="next-steps"></a>Passos seguintes
* [Recolher contadores de desempenho de aplicações de Linux](log-analytics-data-sources-linux-applications.md) incluindo MySQL e Apache HTTP Server.
* Saiba mais sobre [pesquisas de registo](log-analytics-log-searches.md) para analisar os dados recolhidos a partir de origens de dados e soluções.  
* Exportar os dados recolhidos para [Power BI](log-analytics-powerbi.md) para análise e visualizações adicionais.
