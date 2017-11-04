---
title: "Introdução às tarefas de bases de dados elásticas | Microsoft Docs"
description: "Utilize tarefas de bases de dados elásticas para executar scripts T-SQL que abrangem várias bases de dados."
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
ms.assetid: 2540de0e-2235-4cdd-9b6a-b841adba00e5
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2016
ms.author: ddove
ms.openlocfilehash: d985008bf4aa6710f3aae89f13fc7e36ac0c176b
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="getting-started-with-elastic-database-jobs"></a>Introdução às tarefas de base de dados elástica
As tarefas de base de dados elásticas (pré-visualização) para a SQL Database do Azure permite-lhe fiabilidade executar scripts T-SQL que abrangem várias bases de dados ao repetir e fornecer garantias de conclusão de uma eventual automaticamente. Para obter mais informações sobre a funcionalidade de tarefa de bases de dados elásticas, consulte [as tarefas elásticas](sql-database-elastic-jobs-overview.md).

Este artigo expande o exemplo encontrado no [introdução com ferramentas de base de dados elástica](sql-database-elastic-scale-get-started.md). Quando concluído, irá aprender a criar e gerir tarefas Gerir um grupo de bases de dados relacionados. Não é necessário utilizar as ferramentas do dimensionamento elástico para tirar partido dos benefícios das tarefas elásticas.

## <a name="prerequisites"></a>Pré-requisitos
Transfira e execute o [introdução com exemplo de ferramentas de base de dados elástica](sql-database-elastic-scale-get-started.md).

## <a name="create-a-shard-map-manager-using-the-sample-app"></a>Criar um ID de partição horizontal manager mapa utilizando a aplicação de exemplo
Aqui, criar um mapa de partições horizontais manager juntamente com vários shards, seguido de inserção de dados para os shards. Se já tiver shards configurado com a dados fragmentados nos mesmos, pode ignorar os passos seguintes e mover para a secção seguinte.

1. Compilar e executar o **introdução com ferramentas de base de dados elástica** aplicação de exemplo. Siga os passos até que o passo 7 na secção [transferir e executar a aplicação de exemplo](sql-database-elastic-scale-get-started.md#download-and-run-the-sample-app). No final do passo 7, consulte a seguinte linha de comandos:

   ![linha de comandos](./media/sql-database-elastic-query-getting-started/cmd-prompt.png)

2. Na janela de comandos, escreva "1" e prima **Enter**. Isto cria as partições horizontais Gestor de mapa e adiciona dois shards para o servidor. Em seguida, escreva "3" e prima **Enter**; repetir esta ação quatro vezes. Isto insere linhas de dados de exemplo na sua shards.
3. O [portal do Azure](https://portal.azure.com) deve mostrar três bases de dados nova:

   ![Confirmação do Visual Studio](./media/sql-database-elastic-query-getting-started/portal.png)

   Neste momento, iremos criar uma coleção de base de dados personalizada que reflete todas as bases de dados no mapa de partições horizontais. Isto permite-nos criar e executar uma tarefa que adiciona uma nova tabela em partições horizontais.

Aqui iremos normalmente criaria um mapa de partições horizontais de destino, utilizando o **New-AzureSqlJobTarget** cmdlet. A base de dados do Gestor de mapa do ID de partição horizontal tem de ser definido como um destino de base de dados e, em seguida, o mapa de partições horizontais específico está especificado como um destino. Em vez disso, vamos enumerar todas as bases de dados no servidor e adicionar as bases de dados para a nova coleção personalizada com a exceção de base de dados mestra.

## <a name="creates-a-custom-collection-and-add-all-databases-in-the-server-to-the-custom-collection-target-with-the-exception-of-master"></a>Cria uma coleção personalizada e adicione todas as bases de dados no servidor de destino coleções personalizadas com a exceção mestre.
   ```
    $customCollectionName = "dbs_in_server"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $ResourceGroupName = "ddove_samples"
    $ServerName = "samples"
    $dbsinserver = Get-AzureRMSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName
    $dbsinserver | %{
    $currentdb = $_.DatabaseName
    $ErrorActionPreference = "Stop"
    Write-Output ""

    Try
    {
       New-AzureSqlJobTarget -ServerName $ServerName -DatabaseName $currentdb | Write-Output
    }
    Catch
    {
        $ErrorMessage = $_.Exception.Message
        $ErrorCategory = $_.CategoryInfo.Reason

        if ($ErrorCategory -eq 'UniqueConstraintViolatedException')
        {
             Write-Host $currentdb "is already a database target."
        }

        else
        {
            throw $_
        }

    }

    Try
    {
        if ($currentdb -eq "master")
        {
            Write-Host $currentdb "will not be added custom collection target" $CustomCollectionName "."
        }

        else
        {
            Add-AzureSqlJobChildTarget -CustomCollectionName $CustomCollectionName -ServerName $ServerName -DatabaseName $currentdb
            Write-Host $currentdb "was added to" $CustomCollectionName "."
        }

    }
    Catch
    {
        $ErrorMessage = $_.Exception.Message
        $ErrorCategory = $_.CategoryInfo.Reason

        if ($ErrorCategory -eq 'UniqueConstraintViolatedException')
        {
             Write-Host $currentdb "is already in the custom collection target" $CustomCollectionName"."
        }

        else
        {
            throw $_
        }
    }
    $ErrorActionPreference = "Continue"
   }
   ```
## <a name="create-a-t-sql-script-for-execution-across-databases"></a>Criar um Script T-SQL para execução em bases de dados
   ```
    $scriptName = "NewTable"
    $scriptCommandText = "
    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'Test')
    BEGIN
        CREATE TABLE Test(
            TestId INT PRIMARY KEY IDENTITY,
            InsertionTime DATETIME2
        );
    END
    GO
    INSERT INTO Test(InsertionTime) VALUES (sysutcdatetime());
    GO"

    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script
   ```

## <a name="create-the-job-to-execute-a-script-across-the-custom-group-of-databases"></a>Criar a tarefa para executar um script entre o grupo de bases de dados personalizado

   ```
    $jobName = "create on server dbs"
    $scriptName = "NewTable"
    $customCollectionName = "dbs_in_server"
    $credentialName = "ddove66"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job
   ```

## <a name="execute-the-job"></a>Executar a tarefa
O seguinte script do PowerShell pode ser utilizado para executar uma tarefa existente:

Atualize a seguinte variável para refletir o nome da tarefa pretendida para ter executada:

   ```
    $jobName = "create on server dbs"
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName
    Write-Output $jobExecution
   ```

## <a name="retrieve-the-state-of-a-single-job-execution"></a>Obter o estado de execução de uma única tarefa
Utilizar o mesmo **Get-AzureSqlJobExecution** cmdlet com o **includechildren exige** parâmetro para ver o estado de execuções de tarefa subordinada, nomeadamente o estado específico para cada execução da tarefa em relação a cada base de dados visada pela tarefa.

   ```
    $jobExecutionId = "{Job Execution Id}"
    $jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
    Write-Output $jobExecutions
   ```

## <a name="view-the-state-across-multiple-job-executions"></a>Ver o estado em várias execuções de tarefa
O **Get-AzureSqlJobExecution** cmdlet tem vários parâmetros opcionais que podem ser utilizados para apresentar várias execuções de tarefa, filtradas através dos parâmetros fornecidos. O seguinte demonstra Algumas das formas possíveis para Utilize Get-AzureSqlJobExecution:

Obter todas as execuções de tarefa de nível superior de Active Directory:

   ```
    Get-AzureSqlJobExecution
   ```

Obter todas as execuções de tarefa de nível superior, incluindo as execuções de tarefa inativo:

   ```
    Get-AzureSqlJobExecution -IncludeInactive
   ```

Obter todas as execuções de tarefa de subordinados de um ID de execução da tarefa fornecida, incluindo execuções de tarefa inativo:

   ```
    $parentJobExecutionId = "{Job Execution Id}"
    Get-AzureSqlJobExecution -AzureSqlJobExecution -JobExecutionId $parentJobExecutionId -IncludeInactive -IncludeChildren
   ```

Obter todas as execuções de tarefa criadas com base numa agenda / combinação, incluindo tarefas Inativas da tarefa:

   ```
    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Get-AzureSqlJobExecution -JobName $jobName -ScheduleName $scheduleName -IncludeInactive
   ```

Obter todas as tarefas direcionada para um mapa de partições horizontais especificado, incluindo tarefas Inativas:

   ```
    $shardMapServerName = "{Shard Map Server Name}"
    $shardMapDatabaseName = "{Shard Map Database Name}"
    $shardMapName = "{Shard Map Name}"
    $target = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive
   ```

Obter todas as tarefas direcionada para uma coleção personalizada especificada, incluindo tarefas Inativas:

   ```
    $customCollectionName = "{Custom Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive
   ```

Obter a lista de execuções de tarefas da tarefa em execução uma tarefa específica:

   ```
    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Write-Output $jobTaskExecutions
   ```

Obter os detalhes de tarefa de execução da tarefa:

O seguinte script do PowerShell pode ser utilizado para ver os detalhes de uma execução de tarefas da tarefa, que é particularmente útil quando a depuração de falhas de execução.
   ```
    $jobTaskExecutionId = "{Job Task Execution Id}"
    $jobTaskExecution = Get-AzureSqlJobTaskExecution -JobTaskExecutionId $jobTaskExecutionId
    Write-Output $jobTaskExecution
   ```

## <a name="retrieve-failures-within-job-task-executions"></a>Obter falhas num execuções de tarefas da tarefa
O objeto de JobTaskExecution inclui uma propriedade para o ciclo de vida da tarefa, juntamente com uma propriedade de mensagem. Se uma execução de tarefas da tarefa falhou, o ciclo de vida for definida como *falha* e a propriedade da mensagem está definida para a mensagem de exceção resultante e a pilha. Se uma tarefa não foi concluída com êxito, é importante ver os detalhes das tarefas que não foram concluída com êxito para uma determinada tarefa.

   ```
    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Foreach($jobTaskExecution in $jobTaskExecutions)
        {
        if($jobTaskExecution.Lifecycle -ne 'Succeeded')
            {
            Write-Output $jobTaskExecution
            }
        }
   ```

## <a name="waiting-for-a-job-execution-to-complete"></a>A aguardar a conclusão da execução tarefa
O seguinte script do PowerShell pode ser utilizado para aguardar a tarefa concluir:

   ```
    $jobExecutionId = "{Job Execution Id}"
    Wait-AzureSqlJobExecution -JobExecutionId $jobExecutionId
   ```

## <a name="create-a-custom-execution-policy"></a>Criar uma política de execução personalizado
As tarefas de base de dados elásticas suporta a criação de políticas de execução personalizadas que podem ser aplicadas ao iniciar trabalhos.

Atualmente permitem definir políticas de execução:

* Nome: Identificador para a política de execução.
* Tempo limite de tarefa: Total de tempo antes de uma tarefa foi cancelada por tarefas de base de dados elásticas.
* Intervalo de repetição inicial: Intervalo a aguardar antes de repetir primeiro.
* Intervalo máximo de tentativas: Limite de intervalos de repetição para utilizar.
* Repetir coeficiente de término de intervalo: Coeficiente utilizado para calcular o próximo intervalo entre tentativas.  É utilizada a seguinte fórmula: (intervalo de Repita inicial) * Math.pow ((coeficiente de término de intervalo), (número de tentativas) - 2).
* Número máximo de tentativas: O número máximo de repetições tenta executar dentro de uma tarefa.

A política de execução predefinido utiliza os seguintes valores:

* Nome: Política de execução de predefinida
* Tempo limite de tarefa: 1 semana
* Intervalo de repetição inicial: 100 milissegundos
* Intervalo máximo de tentativas: 30 minutos
* Repita o coeficiente de intervalo: 2
* Número máximo de tentativas: 2 147 483 647

Crie a política de execução pretendido:

   ```
    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 10
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $executionPolicy = New-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $executionPolicy
   ```

### <a name="update-a-custom-execution-policy"></a>Atualizar uma política de execução personalizado
Atualize a política de execução pretendido para atualizar:

   ```
    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 15
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $updatedExecutionPolicy = Set-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $updatedExecutionPolicy
   ```

## <a name="cancel-a-job"></a>Cancelar uma tarefa
As tarefas de base de dados elásticas suporta pedidos de cancelamento de tarefas.  Se as tarefas de base de dados elásticas Deteta um pedido de cancelamento de uma tarefa atualmente a ser executado, este tenta parar a tarefa.

Existem duas formas diferentes, que as tarefas de base de dados elásticas podem executar um cancelamento:

1. Atualmente a executar a cancelar tarefas: se for detetado um cancelamento enquanto uma tarefa está atualmente em execução, um cancelamento é tentado dentro do aspeto atualmente em execução da tarefa.  Por exemplo: se houver uma consulta de execução longa atualmente a ser efetuada quando é tentado um cancelamento, houver uma tentativa para cancelar a consulta.
2. Cancelamento de tarefas de tentativas: Se um cancelamento é detetado pelo thread controlo antes de uma tarefa é iniciada para execução, o thread de controlo evita a iniciar a tarefa e declarar o pedido como cancelada.

Se for pedido um cancelamento de tarefas para uma tarefa principal, o pedido de cancelamento é honrado a tarefa principal de e para todos os respetivos tarefas subordinadas.

Para submeter um pedido de cancelamento, utilize o **Stop-AzureSqlJobExecution** cmdlet e defina o **JobExecutionId** parâmetro.

   ```
    $jobExecutionId = "{Job Execution Id}"
    Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId
   ```

## <a name="delete-a-job-by-name-and-the-jobs-history"></a>Eliminar uma tarefa de por nome e o histórico da tarefa
As tarefas de base de dados elásticas suporta eliminação assíncrona de tarefas. Uma tarefa pode ser marcada para eliminação e o sistema elimina a tarefa e todos os respetivo histórico de tarefas depois de concluir todas as execuções de tarefa para a tarefa. O sistema não cancelar automaticamente execuções de tarefas ativas.  

Em vez disso, Stop-AzureSqlJobExecution tem de ser invocado para cancelar execuções de tarefas ativas.

Para acionar a eliminação da tarefa, utilize o **remover AzureSqlJob** cmdlet e defina o **JobName** parâmetro.

   ```
    $jobName = "{Job Name}"
    Remove-AzureSqlJob -JobName $jobName
   ```

## <a name="create-a-custom-database-target"></a>Criar um destino de base de dados personalizada
Destinos de base de dados personalizada podem ser definidos nas tarefas de bases de dados elásticas que podem ser utilizadas para execução diretamente ou para inclusão dentro de um grupo de base de dados personalizada. Uma vez que **conjuntos elásticos** não são ainda diretamente suportadas através das APIs do PowerShell, simplesmente cria um destino de base de dados personalizada e o destino de coleção de base de dados personalizada que abrange todas as bases de dados no conjunto.

Defina as seguintes variáveis para refletir as informações da base de dados pretendido:

   ```
    $databaseName = "{Database Name}"
    $databaseServerName = "{Server Name}"
    New-AzureSqlJobDatabaseTarget -DatabaseName $databaseName -ServerName $databaseServerName
   ```

## <a name="create-a-custom-database-collection-target"></a>Criar uma base de dados personalizada recolha de destino
Uma base de dados personalizada recolha de destino pode ser definido para permitir a execução em vários destinos de base de dados definido. Depois de criar um grupo de base de dados, as bases de dados podem ser associados ao destino coleção personalizada.

Defina as seguintes variáveis para refletir a configuração de destino pretendida coleção personalizada:

   ```
    $customCollectionName = "{Custom Database Collection Name}"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName
   ```

### <a name="add-databases-to-a-custom-database-collection-target"></a>Adicionar bases de dados para um destino de coleção da base de dados personalizada
Destinos de base de dados podem ser associados com destinos de coleção da base de dados personalizada para criar um grupo de bases de dados. Sempre que é criada uma tarefa que está direcionada para um destino de coleção da base de dados personalizada, esteja expandido para as bases de dados associadas ao grupo no momento da execução de destino.

Adicione a base de dados pretendido para uma coleção personalizada específica:

   ```
    $serverName = "{Database Server Name}"
    $databaseName = "{Database Name}"
    $customCollectionName = "{Custom Database Collection Name}"
    Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName
   ```

#### <a name="review-the-databases-within-a-custom-database-collection-target"></a>Reveja as bases de dados dentro de um destino de coleção da base de dados personalizada
Utilize o **Get-AzureSqlJobTarget** cmdlet para obter as bases de dados do subordinado dentro de uma base de dados personalizada recolha de destino.

   ```
    $customCollectionName = "{Custom Database Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
    Write-Output $childTargets
   ```

### <a name="create-a-job-to-execute-a-script-across-a-custom-database-collection-target"></a>Criar uma tarefa para executar um script através de um destino de coleção da base de dados personalizada
Utilize o **New-AzureSqlJob** cmdlet para criar uma tarefa em relação a um grupo de bases de dados definidas por um destino de coleção da base de dados personalizada. As tarefas de base de dados elásticas expande a tarefa para várias tarefas de subordinados cada correspondente a uma base de dados associada com a base de dados personalizada recolha de destino e certifique-se de que o script é executado em relação a cada base de dados. Novamente, é importante que os scripts estão idempotent para sejam resilientes a tentativas.

   ```
    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job
   ```

## <a name="data-collection-across-databases"></a>Recolha de dados entre bases de dados
**As tarefas de base de dados elásticas** suporta a executar uma consulta entre um grupo de bases de dados e envia os resultados à tabela de uma especificado da base de dados. A tabela pode ser consultada após o facto para ver os resultados da consulta de cada base de dados. Esta opção fornece um mecanismo assíncrono para executar uma consulta em muitas bases de dados. Cenários de falha, tais como uma das bases de dados está temporariamente indisponível são processados automaticamente através de tentativas.

A tabela de destino especificado é criada automaticamente se ainda existir, o esquema do conjunto de resultados devolvido de correspondência. Se uma execução de script devolver vários conjuntos de resultados, tarefas de bases de dados elásticas envia apenas o um primeiro para a tabela de destino fornecido.

O seguinte script do PowerShell pode ser utilizado para executar um script recolher os resultados para uma tabela especificada. Este script assume que foi criado um script T-SQL que produz um conjunto de resultados único e foi criado um destino de coleção da base de dados personalizada.

Defina o seguinte para refletir o script pretendido, o credenciais e o destino de execução:

   ```
    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $executionCredentialName = "{Execution Credential Name}"
    $customCollectionName = "{Custom Collection Name}"
    $destinationCredentialName = "{Destination Credential Name}"
    $destinationServerName = "{Destination Server Name}"
    $destinationDatabaseName = "{Destination Database Name}"
    $destinationSchemaName = "{Destination Schema Name}"
    $destinationTableName = "{Destination Table Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
   ```

### <a name="create-and-start-a-job-for-data-collection-scenarios"></a>Criar e iniciar uma tarefa para cenários de recolha de dados
   ```
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $executionCredentialName -ContentName $scriptName -ResultSetDestinationServerName $destinationServerName -ResultSetDestinationDatabaseName $destinationDatabaseName -ResultSetDestinationSchemaName $destinationSchemaName -ResultSetDestinationTableName $destinationTableName -ResultSetDestinationCredentialName $destinationCredentialName -TargetId $target.TargetId
    Write-Output $job
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName
    Write-Output $jobExecution
   ```

## <a name="create-a-schedule-for-job-execution-using-a-job-trigger"></a>Criar um agendamento para execução da tarefa com um acionador da tarefa
O seguinte script do PowerShell pode ser utilizado para criar uma agenda reoccurring. Este script utiliza um intervalo de um minuto, mas New-AzureSqlJobSchedule também suporta os parâmetros - DayInterval, - HourInterval, - MonthInterval e - WeekInterval. As agendas que executar apenas uma vez que podem ser criadas por transmitir - OneTime.

Crie uma nova agenda:
   ```
    $scheduleName = "Every one minute"
    $minuteInterval = 1
    $startTime = (Get-Date).ToUniversalTime()
    $schedule = New-AzureSqlJobSchedule -MinuteInterval $minuteInterval -ScheduleName $scheduleName -StartTime $startTime
    Write-Output $schedule
   ```

### <a name="create-a-job-trigger-to-have-a-job-executed-on-a-time-schedule"></a>Criar um acionador da tarefa para que uma tarefa executada num horário
Um acionador da tarefa pode ser definido para que uma tarefa executada segundo uma agenda de tempo. O seguinte script do PowerShell pode ser utilizado para criar um acionador da tarefa.

Defina as seguintes variáveis de forma a corresponderem da tarefa pretendida e a agenda:

   ```
    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    $jobTrigger = New-AzureSqlJobTrigger -ScheduleName $scheduleName -JobName $jobName
    Write-Output $jobTrigger
   ```

### <a name="remove-a-scheduled-association-to-stop-job-from-executing-on-schedule"></a>Remover uma associação agendada para parar a tarefa de execução numa agenda
Para descontinuar a execução da tarefa a ocorrer através de um acionador da tarefa, o acionador da tarefa pode ser removido.
Remover um acionador da tarefa para parar uma tarefa de que está a ser executado, de acordo com uma agenda a utilizar o **remover AzureSqlJobTrigger** cmdlet.

   ```
    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Remove-AzureSqlJobTrigger -ScheduleName $scheduleName -JobName $jobName
   ```

## <a name="import-elastic-database-query-results-to-excel"></a>Importar os resultados da consulta de base de dados elástica para Excel
 Pode importar os resultados de uma consulta para um ficheiro do Excel.

1. Inicie o Excel 2013.
2. Navegue para o **dados** Friso.
3. Clique em **de outras origens** e clique em **do SQL Server**.

   ![Importação de Excel de outras origens](./media/sql-database-elastic-query-getting-started/exel-sources.png)

4. No **Assistente de ligação de dados** escreva as credenciais de início de sessão e nome do servidor. Clique depois em **Seguinte**.
5. Na caixa de diálogo **selecione a base de dados que contém os dados que pretende**, selecione o **ElasticDBQuery** base de dados.
6. Selecione o **clientes** uma tabela na vista de lista e clique em **seguinte**. Em seguida, clique em **concluir**.
7. No **importar dados** formulário em **Selecione como pretende ver estes dados no seu livro**, selecione **tabela** e clique em **OK**.

Todas as linhas da **clientes** tabela, armazenada em diferentes shards preencher a folha do Excel.

## <a name="next-steps"></a>Passos seguintes
Agora, pode utilizar as funções do dados do Excel. Utilize a cadeia de ligação com o nome do servidor, nome de base de dados e as credenciais para ligar as ferramentas de integração do BI e os dados na base de dados elásticas consulta. Certifique-se de que o SQL Server é suportada como uma origem de dados para a ferramenta. Consulte a base de dados elásticas consultas e tabelas externas, tal como qualquer outra base de dados do SQL Server e tabelas de SQL Server que seria se ligam a com a ferramenta.

### <a name="cost"></a>Custo
Não há sem encargos adicionais para utilizar a funcionalidade de consulta de base de dados elásticas. No entanto, neste momento, esta funcionalidade só está disponível no bases de dados premium como um ponto final, mas os shards podem ser de qualquer camada de serviço.

Para obter informações sobre preços, consulte [detalhes de preços de base de dados do SQL Server](https://azure.microsoft.com/pricing/details/sql-database/).

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-getting-started/cmd-prompt.png
[2]: ./media/sql-database-elastic-query-getting-started/portal.png
[3]: ./media/sql-database-elastic-query-getting-started/tiers.png
[4]: ./media/sql-database-elastic-query-getting-started/details.png
[5]: ./media/sql-database-elastic-query-getting-started/exel-sources.png
<!--anchors-->
