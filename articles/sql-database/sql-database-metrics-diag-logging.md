---
title: "Registo de diagnóstico e de métricas de base de dados SQL do Azure | Microsoft Docs"
description: "Saiba mais sobre como configurar a base de dados do Azure SQL Server para armazenar a utilização de recursos, conectividade e estatísticas de execução da consulta."
services: sql-database
documentationcenter: 
author: veljko-msft
manager: jhubbard
editor: 
ms.assetid: 89c2a155-c2fb-4b67-bc19-9b4e03c6d3bc
ms.service: sql-database
ms.custom: monitor & tune
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/16/2017
ms.author: vvasic
ms.openlocfilehash: 9f201454d58dbc646923d0155ff41761d593ab7e
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2017
---
# <a name="azure-sql-database-metrics-and-diagnostics-logging"></a>Métricas de base de dados SQL do Azure e o registo de diagnóstico 
Base de dados SQL do Azure pode emitir métricas e diagnóstico registos para a monitorização mais fácil. Pode configurar a Base de Dados SQL para armazenar a utilização de recursos, funções de trabalho e sessões e a conectividade a um dos recursos do Azure seguintes:

* **Armazenamento do Azure**: utilizado para arquivar grandes quantidades de telemetria para um preço pequeno.
* **Os Hubs de eventos do Azure**: utilizado para integrar telemetria de base de dados SQL com a sua solução de monitorização personalizada ou pipelines frequente.
* **Análise de registos do Azure**: utilizado para uma solução de monitorização de out of box com relatórios, alertas e mitigar capacidades.

    ![Arquitetura](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="enable-logging"></a>Ativar registo

Métricas e registo de diagnóstico não está ativada por predefinição. Pode ativar e gerir as métricas e ao diagnóstico do registo utilizando um dos seguintes métodos:

- Portal do Azure
- PowerShell
- CLI do Azure
- API de REST de Monitor do Azure 
- Modelo Azure Resource Manager

Quando ativar métricas e registo de diagnóstico, terá de especificar o recurso do Azure onde são recolhidos os dados selecionados. As opções disponíveis incluem:

- Log Analytics
- Event Hubs
- Armazenamento 

Pode aprovisionar um novo recurso do Azure ou selecione um recurso existente. Depois de selecionar o recurso de armazenamento, tem de especificar os dados a recolher. As opções disponíveis incluem:

- [Todas as métricas](sql-database-metrics-diag-logging.md#all-metrics): percentagem de DTU contém, limite DTU, percentagem de CPU, percentagem de leitura de dados físicos, escrever o registo de percentagem, Successful/falhado/bloqueado por ligações de firewall, percentagem de sessões, percentagem workers, armazenamento, a percentagem de armazenamento e a percentagem de armazenamento XTP.
- [QueryStoreRuntimeStatistics](sql-database-metrics-diag-logging.md#query-store-runtime-statistics): contém informações sobre as estatísticas de tempo de execução de consulta, por exemplo, a duração de consulta e de utilização da CPU.
- [QueryStoreWaitStatistics](sql-database-metrics-diag-logging.md#query-store-wait-statistics): contém informações sobre as estatísticas de espera de consulta, que indica que as suas consultas aguardaram, tais como CPU, o registo e o bloqueio.
- [Erros](sql-database-metrics-diag-logging.md#errors-dataset): contém informações sobre erros do SQL Server que tenham acontecido nesta base de dados.
- [DatabaseWaitStatistics](sql-database-metrics-diag-logging.md#database-waits-dataset): contém informações sobre a quantidade de tempo uma base de dados despende a aguardar a resposta de espera de diferentes tipos.
- [Tempos limite](sql-database-metrics-diag-logging.md#timeouts-dataset): contém informações sobre tempos limite que tenham acontecido numa base de dados.
- [Blockings](sql-database-metrics-diag-logging.md#blockings-dataset): contém informações sobre eventos que aconteceram numa base de dados a bloquear.
- [SQLInsights](sql-database-metrics-diag-logging.md#intelligent-insights-dataset): contém informações inteligentes. [Saiba mais sobre as informações inteligente](sql-database-intelligent-insights.md).

Se selecionar os Event Hubs ou uma conta de armazenamento, pode especificar uma política de retenção. Esta política elimina os dados que é mais antigos do que um período de tempo selecionado. Se especificar a análise de registos, a política de retenção depende o escalão de preço selecionado. Para obter mais informações, consulte [preços de análise de registos](https://azure.microsoft.com/pricing/details/log-analytics/). 

Para saber como ativar o registo e compreender as categorias de métricas e registo que são suportadas pelos vários serviços do Azure, recomendamos que leia: 

* [Descrição geral das métricas no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
* [Descrição geral dos registos de diagnóstico do Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) 

### <a name="azure-portal"></a>Portal do Azure

1. Para ativar a recolha de registos de métricas e diagnósticos no portal, aceda à sua base de dados do SQL Server ou a página do conjunto elástico e, em seguida, selecione **as definições de diagnóstico**.

   ![Ativar no portal do Azure](./media/sql-database-metrics-diag-logging/enable-portal.png)

2. Crie uma nova ou editar as definições de diagnóstico existente, selecionando a telemetria e de destino.

   ![Definições de diagnóstico](./media/sql-database-metrics-diag-logging/diagnostics-portal.png)

### <a name="powershell"></a>PowerShell

Para ativar as métricas e ao diagnóstico do registo utilizando o PowerShell, utilize os seguintes comandos:

- Para ativar o armazenamento dos registos de diagnóstico numa conta do storage, utilize este comando:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   O ID de conta de armazenamento é o ID de recurso para a conta de armazenamento onde pretende enviar os registos.

- Para ativar a transmissão em fluxo de registos de diagnóstico para um hub de eventos, utilize este comando:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   O ID de regra de Service Bus do Azure é uma cadeia com este formato:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Para ativar o envio de registos de diagnóstico para uma área de trabalho de análise de registos, utilize este comando:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- Pode obter o ID de recurso da sua área de trabalho de análise de registos ao utilizar o seguinte comando:

   ```powershell
   (Get-AzureRmOperationalInsightsWorkspace).ResourceId
   ```

Pode combinar estes parâmetros para ativar várias opções de saída.

### <a name="to-configure-multiple-azure-resources"></a>Para configurar a vários recursos do Azure

Para suportar várias subscrições, utilize o script do PowerShell do [registo de métricas de recurso de ativar o Azure com o PowerShell](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/).

Forneça o ID de recurso da área de trabalho &lt;$WSID&gt; como um parâmetro ao executar o script (Enable-AzureRMDiagnostics.ps1) para enviar dados de diagnóstico de vários recursos para a área de trabalho. Para obter o ID da área de trabalho &lt;$WSID&gt; para o qual pretende enviar dados de diagnóstico, substitua &lt;subID&gt; com o ID de subscrição, substitua &lt;RG_NAME&gt; com o nome de grupo de recursos e substitua &lt;WS_NAME&gt; com o nome de área de trabalho no seguinte script.

- Para configurar a vários recursos do Azure, utilize os seguintes comandos:

    ```powershell
    PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
    PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
    ```

### <a name="azure-cli"></a>CLI do Azure

Para ativar as métricas e ao diagnóstico do registo utilizando a CLI do Azure, utilize os seguintes comandos:

- Para ativar o armazenamento dos registos de diagnóstico numa conta do storage, utilize este comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   O ID de conta de armazenamento é o ID de recurso para a conta de armazenamento onde pretende enviar os registos.

- Para ativar a transmissão em fluxo de registos de diagnóstico para um hub de eventos, utilize este comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   O ID de regra de Service Bus é uma cadeia com este formato:

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Para ativar o envio de registos de diagnóstico para uma área de trabalho de análise de registos, utilize este comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

Pode combinar estes parâmetros para ativar várias opções de saída.

### <a name="rest-api"></a>API REST

Leia sobre como [alterar as definições de diagnóstico ao através da API de REST do Azure Monitor](https://msdn.microsoft.com/library/azure/dn931931.aspx). 

### <a name="resource-manager-template"></a>Modelo do Resource Manager

Leia sobre como [ativar as definições de diagnóstico durante a criação de recursos através de um modelo do Resource Manager](../monitoring-and-diagnostics/monitoring-enable-diagnostic-logs-using-template.md). 

## <a name="stream-into-log-analytics"></a>Fluxo para análise de registos 
Os registos de métricas e diagnóstico da base de dados SQL podem ser transmissão em fluxo para análise de registos utilizando incorporada **enviar ao Log Analytics** opção no portal. Também pode ativar a análise de registos utilizando uma definição de diagnóstico através de cmdlets do PowerShell, a CLI do Azure ou a API de REST de Monitor do Azure.

### <a name="installation-overview"></a>Descrição geral da instalação

Monitorização de uma frota de base de dados SQL é simple com a análise de registos. São necessários três passos:

1. Crie um recurso de análise de registos.

2. Configure bases de dados do registos registos de métricas e diagnóstico para o recurso de análise de registos que criou.

3. Instalar o **análise do Azure SQL** solução a partir da Galeria de análise de registos.

### <a name="create-a-log-analytics-resource"></a>Crie um recurso de análise de registos

1. Selecione **novo** no menu à esquerda.

2. Selecione **monitorização + gestão**.

3. Selecione **Iniciar análise**.

4. Preencha o formulário de análise de registos com as informações adicionais que é necessários: nome de área de trabalho, subscrição, grupo de recursos, localização e escalão de preço.

   ![Log Analytics](./media/sql-database-metrics-diag-logging/log-analytics.png)

### <a name="configure-databases-to-record-metrics-and-diagnostics-logs"></a>Configurar bases de dados do registos de registos de métricas e diagnóstico

A forma mais fácil de configurar as métricas de registo de bases de dados de onde é efetuada através do portal do Azure. No portal, aceda ao seu recurso de base de dados SQL e selecione **as definições de diagnóstico**. 

### <a name="install-the-sql-analytics-solution-from-the-gallery"></a>Instalar a solução de análise de SQL na galeria do

1. Depois de criar o recurso de análise de registos e os seus dados é que fluem para, instale a solução de análise do SQL Server. Na home page do Operations Management Suite, no menu do lado, selecione **soluções galeria**. Na galeria, selecione o **análise do Azure SQL** solução e selecione **adicionar**.

   ![solução de monitorização](./media/sql-database-metrics-diag-logging/monitoring-solution.png)

2. Na home page do Operations Management Suite, o **análise do Azure SQL** apresentada no mosaico. Selecione este mosaico para abrir o dashboard de análise do SQL Server.

### <a name="use-the-sql-analytics-solution"></a>Utilizar a solução de análise do SQL Server

A análise do SQL Server é um dashboard hierárquico que permite-lhe mover através da hierarquia de recursos de base de dados SQL. Para saber como utilizar a solução de análise do SQL Server, consulte [base de dados do SQL Monitor utilizando a solução de análise do SQL Server](../log-analytics/log-analytics-azure-sql.md).

## <a name="stream-into-event-hubs"></a>Fluxo para os Event Hubs

Os registos de métricas e diagnóstico da base de dados SQL podem ser transmissão em fluxo para os Event Hubs utilizando incorporada **fluxo para um hub de eventos** opção no portal. Também pode ativar o ID de regra de Service Bus utilizando uma definição de diagnóstico através de cmdlets do PowerShell, a CLI do Azure ou a API de REST de Monitor do Azure. 

### <a name="what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs"></a>O que fazer com métricas e diagnóstico inicia sessão dos Event Hubs
Depois dos dados selecionados é transmitida em fluxo para os Event Hubs, está próximos ativar cenários de monitorização avançada. Os Event Hubs atuam como a porta da frente para um pipeline de eventos. Depois dos dados são recolhidos para um hub de eventos, podem ser transformado e armazenado através de qualquer fornecedor de análise em tempo real ou adaptadores de criação de batches/armazenamento. Os Event Hubs desacoplam a produção de um fluxo de eventos do consumo desses eventos. Desta forma, os consumidores de eventos podem aceder aos eventos na sua própria agenda. Para obter mais informações sobre os Event Hubs, consulte:

- [Quais são os Event Hubs do Azure?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Introdução ao Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)


Seguem-se algumas formas que poderá utilizar a capacidade de transmissão em fluxo:

* **Ver o estado de funcionamento do serviço através da transmissão em fluxo de dados-path para o Power BI**. Ao utilizar os Hubs de eventos, o Stream Analytics e o Power BI, pode facilmente transformar os dados de métricas e diagnóstico para quase em tempo real das informações no seus serviços do Azure. Para obter uma descrição geral de como configurar um hub de eventos, processamento de dados com o Stream Analytics e utilize o Power BI como resultado, consulte [Stream Analytics e o Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md).

* **Transmitir os registos para fluxos de registo e telemetria de terceiros**. Ao utilizar os Hubs de eventos de transmissão em fluxo, pode obter os registos de métricas e diagnóstico para diferentes soluções de análise de monitorização e registo de terceiros. 

* **Criar uma plataforma de registo e telemetria personalizada**. Se já tiver uma plataforma de telemetria personalizada ou estiver a considerar a criação de um, altamente dimensionável de publicação-subscrição natureza dos Event Hubs permite-lhe de forma flexível ingerir registos de diagnóstico. Consulte [guia de Dan Rosanova para utilizar os Hubs de eventos numa plataforma de telemetria de escala global](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="stream-into-storage"></a>Fluxo para o armazenamento

Os registos de métricas e diagnóstico da base de dados SQL podem ser armazenados no armazenamento utilizando o incorporado **arquivo para uma conta de armazenamento** opção no portal. Também pode ativar a armazenamento utilizando uma definição de diagnóstico através de cmdlets do PowerShell, a CLI do Azure ou a API de REST de Monitor do Azure.

### <a name="schema-of-metrics-and-diagnostics-logs-in-the-storage-account"></a>Esquema de métricas e diagnóstico inicia sessão na conta de armazenamento

Depois de configurar a recolha de registos de métricas e diagnóstico, um contentor de armazenamento é criado na conta do storage que selecionou quando as primeiras linhas de dados estão disponíveis. A estrutura destas blobs é:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```
    
Ou, mais simples:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Por exemplo, poderá ser um nome de blob para todas as métricas:

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Se pretender registar os dados do conjunto elástico, o nome do blob é um pouco diferente:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

### <a name="download-metrics-and-logs-from-storage"></a>Transferir os registos e as métricas do armazenamento

Saiba como [transferir os registos de métricas e diagnóstico do armazenamento](../storage/blobs/storage-dotnet-how-to-use-blobs.md#download-blobs).

## <a name="metrics-and-logs-available"></a>Métricas e registos disponíveis

### <a name="all-metrics"></a>Todas as métricas

|**Recurso**|**Métricas**|
|---|---|
|Base de Dados|Percentagem DTU, DTU utilizado, o limite DTU, percentagem de CPU, percentagem de leitura de dados físicos, escrever o registo de percentagem, Successful/falhado/bloqueado por ligações de firewall, percentagem de sessões, percentagem workers, armazenamento, percentagem de armazenamento, a percentagem de armazenamento XTP, e impasses |
|Conjunto elástico|percentagem de eDTU, eDTU utilizado, o limite de eDTU, percentagem de CPU, percentagem de leitura de dados físicos, registo escrever percentagem, percentagem de sessões, percentagem workers, armazenamento, a percentagem de armazenamento, limite de armazenamento, percentagem de armazenamento XTP |
|||

### <a name="query-store-runtime-statistics"></a>Estatísticas de tempo de execução do arquivo de consultas

|Propriedade|Descrição|
|---|---|
|TenantId|O ID do inquilino.|
|SourceSystem|Sempre: Azure|
|TimeGenerated [UTC]|Carimbo de hora quando o registo foi registado.|
|Tipo|Sempre: AzureDiagnostics|
|ResourceProvider|Nome do fornecedor de recursos. Sempre: MICROSOFT. SQL SERVER|
|Categoria|Nome da categoria. Sempre: QueryStoreRuntimeStatistics|
|OperationName|Nome da operação. Sempre: QueryStoreRuntimeStatisticsEvent|
|Recurso|Nome do recurso.|
|ResourceType|Nome do tipo de recurso. Sempre: Servidores/bases de dados|
|SubscriptionId|GUID que a base de dados pertence à subscrição.|
|ResourceGroup|Nome do grupo de recursos que a base de dados pertence.|
|LogicalServerName_s|Nome do servidor que a base de dados pertence.|
|ElasticPoolName_s|Nome do conjunto elástico que pertence a base de dados, se aplicável.|
|DatabaseName_s|Nome da base de dados.|
|ResourceId|URI do recurso.|
|query_hash_s|Consultar o hash.|
|query_plan_hash_s|Hash do plano de consulta.|
|statement_sql_handle_s|Identificador de sql da instrução.|
|interval_start_time_d|Inicie datetimeoffset do intervalo de número de ticks de 1900-1-1.|
|interval_end_time_d|Datetimeoffset de fim do intervalo diversas os batimentos de 1900-1-1.|
|logical_io_writes_d|Número total de escritas de e/s lógicas.|
|max_logical_io_writes_d|Número máx. de e/s lógica escreve por execução.|
|physical_io_reads_d|Número total de leituras de e/s físicos.|
|max_physical_io_reads_d|Número máx. de e/s lógica leituras por execução.|
|logical_io_reads_d|Número total de leituras de e/s lógicas.|
|max_logical_io_reads_d|Número máx. de e/s lógica leituras por execução.|
|execution_type_d|Tipo de execução.|
|count_executions_d|Número de execuções da consulta.|
|cpu_time_d|Tempo de CPU total consumido pela consulta em microssegundos.|
|max_cpu_time_d|Consumidor de tempo de CPU máx. por uma execução única em microssegundos.|
|dop_d|Soma de graus de paralelismo.|
|max_dop_d|Grau de máx. de paralelismo utilizado para execução única.|
|rowcount_d|Número total de linhas devolvidas.|
|max_rowcount_d|Número máx. de linhas devolvidas em execução única.|
|query_max_used_memory_d|Quantidade total de memória utilizada em KB.|
|max_query_max_used_memory_d|Quantidade máxima de memória utilizada por uma execução única em KB.|
|duration_d|Tempo de execução total em microssegundos.|
|max_duration_d|Tempo de execução máximo de uma execução única.|
|num_physical_io_reads_d|Número total de leituras físicas.|
|max_num_physical_io_reads_d|Número máx. de leituras físicas por execução.|
|log_bytes_used_d|Quantidade total de bytes de registo utilizados.|
|max_log_bytes_used_d|Quantidade de máximo de bytes de registo utilizados por execução.|
|query_id_d|ID da consulta no arquivo de consultas.|
|plan_id_d|ID do plano no arquivo de consultas.|

Saiba mais sobre [dados de estatísticas do arquivo de consultas runtime](https://docs.microsoft.com/en-us/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql).

### <a name="query-store-wait-statistics"></a>Estatísticas de espera de arquivo de consultas

|Propriedade|Descrição|
|---|---|
|TenantId|O ID do inquilino.|
|SourceSystem|Sempre: Azure|
|TimeGenerated [UTC]|Carimbo de hora quando o registo foi registado.|
|Tipo|Sempre: AzureDiagnostics|
|ResourceProvider|Nome do fornecedor de recursos. Sempre: MICROSOFT. SQL SERVER|
|Categoria|Nome da categoria. Sempre: QueryStoreWaitStatistics|
|OperationName|Nome da operação. Sempre: QueryStoreWaitStatisticsEvent|
|Recurso|Nome do recurso|
|ResourceType|Nome do tipo de recurso. Sempre: Servidores/bases de dados|
|SubscriptionId|GUID que a base de dados pertence à subscrição.|
|ResourceGroup|Nome do grupo de recursos que a base de dados pertence.|
|LogicalServerName_s|Nome do servidor que a base de dados pertence.|
|ElasticPoolName_s|Nome do conjunto elástico que pertence a base de dados, se aplicável.|
|DatabaseName_s|Nome da base de dados.|
|ResourceId|URI do recurso.|
|wait_category_s|Categoria da espera.|
|is_parameterizable_s|A consulta é parametrizável.|
|statement_type_s|Tipo de instrução.|
|statement_key_hash_s|Hash de chave de instrução.|
|exec_type_d|Tipo de execução.|
|total_query_wait_time_ms_d|Tempo de espera total da consulta na categoria do espera específico.|
|max_query_wait_time_ms_d|Tempo de espera máximo da consulta em execução individuais na categoria do espera específico.|
|query_param_type_d|0|
|query_hash_s|Consultar o hash no arquivo de consultas.|
|query_plan_hash_s|Hash do plano de consulta no arquivo de consultas.|
|statement_sql_handle_s|Identificador de instrução no arquivo de consultas.|
|interval_start_time_d|Inicie datetimeoffset do intervalo de número de ticks de 1900-1-1.|
|interval_end_time_d|Datetimeoffset de fim do intervalo diversas os batimentos de 1900-1-1.|
|count_executions_d|Número de execuções da consulta.|
|query_id_d|ID da consulta no arquivo de consultas.|
|plan_id_d|ID do plano no arquivo de consultas.|

Saiba mais sobre [dados de estatísticas de espera de arquivo de consultas](https://docs.microsoft.com/en-us/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql).

### <a name="errors-dataset"></a>Conjunto de dados de erros

|Propriedade|Descrição|
|---|---|
|TenantId|O ID do inquilino.|
|SourceSystem|Sempre: Azure|
|TimeGenerated [UTC]|Carimbo de hora quando o registo foi registado.|
|Tipo|Sempre: AzureDiagnostics|
|ResourceProvider|Nome do fornecedor de recursos. Sempre: MICROSOFT. SQL SERVER|
|Categoria|Nome da categoria. Sempre: erros|
|OperationName|Nome da operação. Sempre: ErrorEvent|
|Recurso|Nome do recurso|
|ResourceType|Nome do tipo de recurso. Sempre: Servidores/bases de dados|
|SubscriptionId|GUID que a base de dados pertence à subscrição.|
|ResourceGroup|Nome do grupo de recursos que a base de dados pertence.|
|LogicalServerName_s|Nome do servidor que a base de dados pertence.|
|ElasticPoolName_s|Nome do conjunto elástico que pertence a base de dados, se aplicável.|
|DatabaseName_s|Nome da base de dados.|
|ResourceId|URI do recurso.|
|Mensagem|Mensagem de erro em texto simples.|
|user_defined_b|É o bit do erro definidas pelo utilizador.|
|error_number_d|Código de erro.|
|Gravidade|Gravidade do erro.|
|state_d|Estado do erro.|
|query_hash_s|Hash de consulta da consulta com falhas, se disponível.|
|query_plan_hash_s|Hash do plano de consulta da consulta com falhas, se disponível.|

Saiba mais sobre [mensagens de erro do SQL Server](https://msdn.microsoft.com/en-us/library/cc645603.aspx).

### <a name="database-wait-statistics-dataset"></a>Conjunto de dados de estatísticas de espera de base de dados

|Propriedade|Descrição|
|---|---|
|TenantId|O ID do inquilino.|
|SourceSystem|Sempre: Azure|
|TimeGenerated [UTC]|Carimbo de hora quando o registo foi registado.|
|Tipo|Sempre: AzureDiagnostics|
|ResourceProvider|Nome do fornecedor de recursos. Sempre: MICROSOFT. SQL SERVER|
|Categoria|Nome da categoria. Sempre: DatabaseWaitStatistics|
|OperationName|Nome da operação. Sempre: DatabaseWaitStatisticsEvent|
|Recurso|Nome do recurso|
|ResourceType|Nome do tipo de recurso. Sempre: Servidores/bases de dados|
|SubscriptionId|GUID que a base de dados pertence à subscrição.|
|ResourceGroup|Nome do grupo de recursos que a base de dados pertence.|
|LogicalServerName_s|Nome do servidor que a base de dados pertence.|
|ElasticPoolName_s|Nome do conjunto elástico que pertence a base de dados, se aplicável.|
|DatabaseName_s|Nome da base de dados.|
|ResourceId|URI do recurso.|
|wait_type_s|Nome do tipo de espera.|
|start_utc_date_t [UTC]|Hora de início do período de medida.|
|end_utc_date_t [UTC]|Hora de fim do período de medida.|
|delta_max_wait_time_ms_d|Máx. aguardaram tempo por execução|
|delta_signal_wait_time_ms_d|Tempo de espera de sinal total.|
|delta_wait_time_ms_d|Tempo de espera total no período.|
|delta_waiting_tasks_count_d|Número de tarefas de espera.|

Saiba mais sobre [base de dados de estatísticas de espera](https://docs.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql).

### <a name="time-outs-dataset"></a>Conjunto de dados de tempos limite

|Propriedade|Descrição|
|---|---|
|TenantId|O ID do inquilino.|
|SourceSystem|Sempre: Azure|
|TimeGenerated [UTC]|Carimbo de hora quando o registo foi registado.|
|Tipo|Sempre: AzureDiagnostics|
|ResourceProvider|Nome do fornecedor de recursos. Sempre: MICROSOFT. SQL SERVER|
|Categoria|Nome da categoria. Sempre: tempos limite|
|OperationName|Nome da operação. Sempre: TimeoutEvent|
|Recurso|Nome do recurso|
|ResourceType|Nome do tipo de recurso. Sempre: Servidores/bases de dados|
|SubscriptionId|GUID que a base de dados pertence à subscrição.|
|ResourceGroup|Nome do grupo de recursos que a base de dados pertence.|
|LogicalServerName_s|Nome do servidor que a base de dados pertence.|
|ElasticPoolName_s|Nome do conjunto elástico que pertence a base de dados, se aplicável.|
|DatabaseName_s|Nome da base de dados.|
|ResourceId|URI do recurso.|
|error_state_d|Código de estado de erro.|
|query_hash_s|Consultar o hash, se disponível.|
|query_plan_hash_s|Consultar o hash do plano, se disponível.|

### <a name="blockings-dataset"></a>Blockings conjunto de dados

|Propriedade|Descrição|
|---|---|
|TenantId|O ID do inquilino.|
|SourceSystem|Sempre: Azure|
|TimeGenerated [UTC]|Carimbo de hora quando o registo foi registado.|
|Tipo|Sempre: AzureDiagnostics|
|ResourceProvider|Nome do fornecedor de recursos. Sempre: MICROSOFT. SQL SERVER|
|Categoria|Nome da categoria. Sempre: blocos|
|OperationName|Nome da operação. Sempre: BlockEvent|
|Recurso|Nome do recurso|
|ResourceType|Nome do tipo de recurso. Sempre: Servidores/bases de dados|
|SubscriptionId|GUID que a base de dados pertence à subscrição.|
|ResourceGroup|Nome do grupo de recursos que a base de dados pertence.|
|LogicalServerName_s|Nome do servidor que a base de dados pertence.|
|ElasticPoolName_s|Nome do conjunto elástico que pertence a base de dados, se aplicável.|
|DatabaseName_s|Nome da base de dados.|
|ResourceId|URI do recurso.|
|lock_mode_s|Modo de bloqueio utilizado pela consulta.|
|resource_owner_type_s|Proprietário do bloqueio.|
|blocked_process_filtered_s|Bloqueado processo relatório XML.|
|duration_d|Duração do bloqueio em microssegundos.|

### <a name="intelligent-insights-dataset"></a>Conjunto de dados de Insights inteligente
Saiba mais sobre o [formato de registo Insights inteligente](sql-database-intelligent-insights-use-diagnostics-log.md).

## <a name="next-steps"></a>Passos seguintes

Para saber como ativar o registo e compreender as categorias de métricas e registo suportadas pelos vários serviços do Azure, leia:

 * [Descrição geral das métricas no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
 * [Descrição geral dos registos de diagnóstico do Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)

Para saber mais sobre os Event Hubs, leia:

* [O que é Event Hubs do Azure?](../event-hubs/event-hubs-what-is-event-hubs.md)
* [Introdução ao Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Para saber mais sobre armazenamento, consulte como [transferir os registos de métricas e diagnóstico do armazenamento](../storage/blobs/storage-dotnet-how-to-use-blobs.md#download-blobs).
