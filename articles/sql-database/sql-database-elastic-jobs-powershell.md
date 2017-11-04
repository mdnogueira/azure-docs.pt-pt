---
title: "Criar e gerir as tarefas elásticas com o PowerShell | Microsoft Docs"
description: PowerShell utilizado para gerir agrupamentos da SQL Database do Azure
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
ms.assetid: 737d8d13-5632-4e18-9cb0-4d3b8a19e495
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: ddove
ms.openlocfilehash: f9bdc28349c540ee68b421b7643e4bed099c9fdd
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="create-and-manage-sql-database-elastic-jobs-using-powershell-preview"></a>Criar e gerir tarefas elásticas da base de dados do SQL Server através do PowerShell (pré-visualização)

As APIs do PowerShell para **tarefas de bases de dados elásticas** (em pré-visualização), permitem-lhe definir um grupo de bases de dados relativamente ao qual vai executar scripts. Este artigo mostra como criar e gerir **tarefas de bases de dados elásticas** utilizando cmdlets do PowerShell. Consulte [descrição geral das tarefas elásticas](sql-database-elastic-jobs-overview.md). 

## <a name="prerequisites"></a>Pré-requisitos
* Uma subscrição do Azure. Para uma versão de avaliação gratuita, consulte [um mês avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Um conjunto de bases de dados criadas com as ferramentas de base de dados elásticas. Consulte [começar a utilizar as ferramentas de base de dados elástica](sql-database-elastic-scale-get-started.md).
* Azure PowerShell. Para informações detalhadas, consulte [Como instalar e configurar o Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).
* **As tarefas de base de dados elásticas** PowerShell pacote: consulte [tarefas de instalação de base de dados elástica](sql-database-elastic-jobs-service-installation.md)

### <a name="select-your-azure-subscription"></a>Selecione a sua subscrição do Azure
Para selecionar a subscrição tem do Id de subscrição (**- SubscriptionId**) ou o nome da subscrição (**- SubscriptionName**). Se tiver várias subscrições, pode executar o **Get-AzureRmSubscription** cmdlet e copie as informações de subscrição pretendida do resultado definido. Assim que tiver informações da sua subscrição, execute o commandlet seguinte para definir esta subscrição como predefinição, nomeadamente o destino para criar e gerir tarefas:

    Select-AzureRmSubscription -SubscriptionId {SubscriptionID}

O [ISE do PowerShell](https://technet.microsoft.com/library/dd315244.aspx) é recomendado para utilização desenvolver e executar scripts do PowerShell contra as tarefas de bases de dados elásticas.

## <a name="elastic-database-jobs-objects"></a>Objetos de tarefas de base de dados elásticos
A tabela seguinte lista os todos os tipos de objeto de **tarefas de bases de dados elásticas** juntamente com a descrição e APIs relevantes do PowerShell.

<table style="width:100%">
  <tr>
    <th>Tipo de objeto</th>
    <th>Descrição</th>
    <th>APIs de PowerShell relacionados</th>
  </tr>
  <tr>
    <td>Credencial</td>
    <td>Nome de utilizador e palavra-passe a utilizar quando ligar às bases de dados para aplicação de DACPACs ou execução de scripts. <p>A palavra-passe é encriptada antes de enviar para e armazenar na base de dados de tarefas de base de dados elásticas.  A palavra-passe é desencriptada pelo serviço de tarefas de base de dados elásticas através de credencial criado e carregado a partir do script de instalação.</td>
    <td><p>Get-AzureSqlJobCredential</p>
    <p>Novo AzureSqlJobCredential</p><p>Conjunto AzureSqlJobCredential</p></td></td>
  </tr>

  <tr>
    <td>Script</td>
    <td>Script de Transact-SQL para ser utilizada para execução em bases de dados.  O script deve ser criado para ser idempotent, uma vez que o serviço tentará a execução do script após falhas.
    </td>
    <td>
    <p>Get-AzureSqlJobContent</p>
    <p>Get-AzureSqlJobContentDefinition</p>
    <p>Novo AzureSqlJobContent</p>
    <p>Conjunto AzureSqlJobContentDefinition</p>
    </td>
  </tr>

  <tr>
    <td>DACPAC</td>
    <td><a href="https://msdn.microsoft.com/library/ee210546.aspx">Aplicação de camada de dados </a> pacote seja aplicado em bases de dados.
    </td>
    <td>
    <p>Get-AzureSqlJobContent</p>
    <p>Novo AzureSqlJobContent</p>
    <p>Conjunto AzureSqlJobContentDefinition</p>
    </td>
  </tr>
  <tr>
    <td>Destino de base de dados</td>
    <td>Nome da base de dados e o servidor de apontar para uma base de dados do SQL do Azure.
    </td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>Novo AzureSqlJobTarget</p>
    </td>
  </tr>
  <tr>
    <td>Destino de mapa de partições horizontais</td>
    <td>Combinação de um destino de base de dados e uma credencial a ser utilizado para determinar as informações armazenadas dentro de um mapa de partições horizontais bases de dados elásticas.
    </td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>Novo AzureSqlJobTarget</p>
    <p>Conjunto AzureSqlJobTarget</p>
    </td>
  </tr>
<tr>
    <td>Destino de recolha personalizada</td>
    <td>Definido grupo de bases de dados a utilizar coletivamente para execução.</td>
    <td>
    <p>Get-AzureSqlJobTarget</p>
    <p>Novo AzureSqlJobTarget</p>
    </td>
  </tr>
<tr>
    <td>Destino de subordinados de recolha personalizada</td>
    <td>Destino de base de dados que é referenciado a partir de uma coleção personalizada.</td>
    <td>
    <p>AzureSqlJobChildTarget adicionar</p>
    <p>Remover AzureSqlJobChildTarget</p>
    </td>
  </tr>

<tr>
    <td>Tarefa</td>
    <td>
    <p>Definição de parâmetros para uma tarefa que pode ser utilizado para acionar a execução, ou para realizar uma agenda.</p>
    </td>
    <td>
    <p>Get-AzureSqlJob</p>
    <p>Novo AzureSqlJob</p>
    <p>Conjunto AzureSqlJob</p>
    </td>
  </tr>

<tr>
    <td>Execução da tarefa</td>
    <td>
    <p>Contentor das tarefas necessárias para satisfazer a executar um script ou aplicar uma DACPAC para um destino utilizando as credenciais para ligações de base de dados com falhas processados accordance a uma política de execução.</p>
    </td>
    <td>
    <p>Get-AzureSqlJobExecution</p>
    <p>Início AzureSqlJobExecution</p>
    <p>Stop-AzureSqlJobExecution</p>
    <p>Espera-AzureSqlJobExecution</p>
  </tr>

<tr>
    <td>Tarefa a execução da tarefa</td>
    <td>
    <p>Única unidade de trabalho para realizar uma tarefa.</p>
    <p>Se uma tarefa com êxito não é capaz de executar, a mensagem de exceção resultante será registada e uma nova tarefa correspondente será criada e executada na accordance para a política de execução especificado.</p></p>
    </td>
    <td>
    <p>Get-AzureSqlJobExecution</p>
    <p>Início AzureSqlJobExecution</p>
    <p>Stop-AzureSqlJobExecution</p>
    <p>Espera-AzureSqlJobExecution</p>
  </tr>

<tr>
    <td>Política de execução da tarefa</td>
    <td>
    <p>Controlos da tarefa tempos limite de execução, os limites de repetição e os intervalos entre tentativas.</p>
    <p>As tarefas de base de dados elásticas inclui uma política de execução da tarefa ao predefinido que provocar essencialmente infinita tentativas de falhas de tarefas de tarefas com um término exponencial de intervalos entre cada tentativa.</p>
    </td>
    <td>
    <p>Get-AzureSqlJobExecutionPolicy</p>
    <p>Novo AzureSqlJobExecutionPolicy</p>
    <p>Conjunto AzureSqlJobExecutionPolicy</p>
    </td>
  </tr>

<tr>
    <td>Agenda</td>
    <td>
    <p>Hora baseada especificação para execução para que sejam efetuadas num intervalo reoccurring ou de uma única vez.</p>
    </td>
    <td>
    <p>Get-AzureSqlJobSchedule</p>
    <p>Novo AzureSqlJobSchedule</p>
    <p>Conjunto AzureSqlJobSchedule</p>
    </td>
  </tr>

<tr>
    <td>Aciona a tarefa</td>
    <td>
    <p>Um mapeamento entre uma tarefa e uma agenda a execução do acionador de tarefa, de acordo com a agenda.</p>
    </td>
    <td>
    <p>Novo AzureSqlJobTrigger</p>
    <p>Remover AzureSqlJobTrigger</p>
    </td>
  </tr>
</table>

## <a name="supported-elastic-database-jobs-group-types"></a>Tipos de grupo de tarefas de bases de dados elásticas suportadas
A tarefa executa os scripts de Transact-SQL (T-SQL) ou a aplicação de DACPACs entre um grupo de bases de dados. Quando for submetida uma tarefa para ser executada através de um grupo de bases de dados, a tarefa "expande" a para tarefas subordinadas em que cada uma desempenha a execução do pedido em relação a uma base de dados no grupo. 

Existem dois tipos de grupos que pode criar: 

* [Mapa de partições horizontais](sql-database-elastic-scale-shard-map-management.md) grupo: quando uma tarefa for submetida para um mapa de partições horizontais de destino, a tarefa consulta o mapa de partições horizontais para determinar o conjunto atual de shards e, em seguida, cria subordinado tarefas para cada partição horizontal no mapa de partições horizontais.
* Grupo de recolha personalizado: personalizadas um conjunto de bases de dados. Quando uma tarefa está direcionada para uma coleção personalizada, cria subordinado tarefas para cada base de dados atualmente na coleção personalizada.

## <a name="to-set-the-elastic-database-jobs-connection"></a>Para definir a base de dados elásticas tarefas de ligação
Uma ligação tem de ser definido como as tarefas *base de dados de controlo* antes de utilizar as APIs de tarefas. Executar este cmdlet aciona uma janela de credencial para aparecer pedir o nome de utilizador e palavra-passe criado ao instalar as tarefas de bases de dados elásticas. Todos os exemplos fornecidos neste tópico partem do princípio de que já foram realizado neste primeiro passo.

Abra uma ligação para as tarefas de bases de dados elásticas:

    Use-AzureSqlJobConnection -CurrentAzureSubscription 

## <a name="encrypted-credentials-within-the-elastic-database-jobs"></a>Credenciais encriptadas nas tarefas de base de dados elástica
As credenciais da base de dados podem ser inseridas nas tarefas *base de dados de controlo* com a respetiva palavra-passe encriptada. É necessário armazenar credenciais para ativar as tarefas ser executada num momento posterior, (utilizando as agendas de tarefas).

Encriptação funciona através de um certificado criado como parte do script de instalação. O script de instalação cria e carrega o certificado para o serviço de nuvem do Azure para a desencriptação das palavras-passe armazenadas encriptadas. O serviço de nuvem do Azure armazena mais tarde a chave pública nas tarefas *base de dados de controlo* que permite que a interface de API do PowerShell ou o Portal clássico do Azure encriptar uma palavra-passe fornecida sem necessidade do certificado seja localmente instalado.

As palavras-passe de credencial são encriptadas e seguros a partir de utilizadores com acesso só de leitura para objetos de tarefas de bases de dados elásticas. Mas é possível que um utilizador mal intencionado com acesso de leitura / escrita para objetos de tarefas de base de dados elásticas extrair uma palavra-passe. As credenciais foram concebidas para ser reutilizadas em execuções de tarefa. As credenciais são transmitidas para bases de dados de destino quando estabelecer ligações. Não existem atualmente sem restrições nas bases de dados de destino utilizados para cada credencial, o utilizador mal intencionado pode adicionar um destino de base de dados para uma base de dados sob o controlo de utilizador mal intencionado. O utilizador, subsequentemente, foi possível iniciar uma tarefa direcionada para esta base de dados para obter a palavra-passe da credencial.

Melhores práticas de segurança para tarefas de bases de dados elásticas incluem:

* Limitar a utilização das APIs para as pessoas fidedignas.
* As credenciais devem ter o mínimo de privilégios necessário para efetuar a tarefa.  Obter mais informações podem ser vistas nisto [autorização e permissões](https://msdn.microsoft.com/library/bb669084.aspx) artigo do MSDN do SQL Server.

### <a name="to-create-an-encrypted-credential-for-job-execution-across-databases"></a>Para criar uma credencial encriptada para execução da tarefa em bases de dados
Para criar uma nova credencial encriptada, o [ **cmdlet Get-Credential** ](https://technet.microsoft.com/library/hh849815.aspx) pede-lhe um nome de utilizador e palavra-passe que pode ser transmitido para o [ **cmdlet New-AzureSqlJobCredential** ](/powershell/module/elasticdatabasejobs/new-azuresqljobcredential).

    $credentialName = "{Credential Name}"
    $databaseCredential = Get-Credential
    $credential = New-AzureSqlJobCredential -Credential $databaseCredential -CredentialName $credentialName
    Write-Output $credential

### <a name="to-update-credentials"></a>Atualizar credenciais
Quando alterar as palavras-passe, utilize o [ **cmdlet Set-AzureSqlJobCredential** ](/powershell/module/elasticdatabasejobs/set-azuresqljobcredential) e defina o **CredentialName** parâmetro.

    $credentialName = "{Credential Name}"
    Set-AzureSqlJobCredential -CredentialName $credentialName -Credential $credential 

## <a name="to-define-an-elastic-database-shard-map-target"></a>Para definir um destino de mapa de partições horizontais de base de dados elástica
Para executar uma tarefa em todas as bases de dados de um conjunto de partições horizontais (criado utilizando [biblioteca de clientes de base de dados elástica](sql-database-elastic-database-client-library.md)), utilize um mapa de partições horizontais como destino de base de dados. Este exemplo requer uma aplicação em partição horizontal criada utilizando a biblioteca de clientes de base de dados elásticas. Consulte [introdução com exemplo de ferramentas de base de dados elástica](sql-database-elastic-scale-get-started.md).

A base de dados do Gestor de mapa do ID de partição horizontal tem de ser definido como um destino de base de dados e, em seguida, o mapa de partições horizontais específico tem de ser especificado como um destino.

    $shardMapCredentialName = "{Credential Name}"
    $shardMapDatabaseName = "{ShardMapDatabaseName}" #example: ElasticScaleStarterKit_ShardMapManagerDb
    $shardMapDatabaseServerName = "{ShardMapServerName}"
    $shardMapName = "{MyShardMap}" #example: CustomerIDShardMap
    $shardMapDatabaseTarget = New-AzureSqlJobTarget -DatabaseName $shardMapDatabaseName -ServerName $shardMapDatabaseServerName
    $shardMapTarget = New-AzureSqlJobTarget -ShardMapManagerCredentialName $shardMapCredentialName -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapDatabaseServerName -ShardMapName $shardMapName
    Write-Output $shardMapTarget

## <a name="create-a-t-sql-script-for-execution-across-databases"></a>Criar um Script T-SQL para execução em bases de dados
Ao criar scripts T-SQL para execução, recomenda-se vivamente que sejam criar [idempotent](https://en.wikipedia.org/wiki/Idempotence) e resilientes contra falhas. As tarefas de base de dados elásticas irão repetir a execução de um script sempre que a execução detetar uma falha, independentemente da classificação da falha.

Utilize o [ **cmdlet New-AzureSqlJobContent** ](/powershell/module/elasticdatabasejobs/new-azuresqljobcontent) para criar e guardar um script para execução e defina o **- ContentName** e **- CommandText** parâmetros.

    $scriptName = "Create a TestTable"

    $scriptCommandText = "
    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
    BEGIN
        CREATE TABLE TestTable(
            TestTableId INT PRIMARY KEY IDENTITY,
            InsertionTime DATETIME2
        );
    END
    GO
    INSERT INTO TestTable(InsertionTime) VALUES (sysutcdatetime());
    GO"

    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script

### <a name="create-a-new-script-from-a-file"></a>Criar um script novo a partir de um ficheiro
Se o script T-SQL está definido no âmbito de um ficheiro, utilizá-lo para importar o script:

    $scriptName = "My Script Imported from a File"
    $scriptPath = "{Path to SQL File}"
    $scriptCommandText = Get-Content -Path $scriptPath
    $script = New-AzureSqlJobContent -ContentName $scriptName -CommandText $scriptCommandText
    Write-Output $script

### <a name="to-update-a-t-sql-script-for-execution-across-databases"></a>Para atualizar um script T-SQL para execução em bases de dados
Este script do PowerShell atualiza o texto do comando T-SQL para um script existente.

Defina as seguintes variáveis para refletir a definição de script pretendido para ser definida:

    $scriptName = "Create a TestTable"
    $scriptUpdateComment = "Adding AdditionalInformation column to TestTable"
    $scriptCommandText = "
    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
    BEGIN
    CREATE TABLE TestTable(
        TestTableId INT PRIMARY KEY IDENTITY,
        InsertionTime DATETIME2
    );
    END
    GO

    IF NOT EXISTS (SELECT columns.name FROM sys.columns INNER JOIN sys.tables on columns.object_id = tables.object_id WHERE tables.name = 'TestTable' AND columns.name = 'AdditionalInformation')
    BEGIN
    ALTER TABLE TestTable
    ADD AdditionalInformation NVARCHAR(400);
    END
    GO

    INSERT INTO TestTable(InsertionTime, AdditionalInformation) VALUES (sysutcdatetime(), 'test');
    GO"

### <a name="to-update-the-definition-to-an-existing-script"></a>Atualizar a definição para um script existente
    Set-AzureSqlJobContentDefinition -ContentName $scriptName -CommandText $scriptCommandText -Comment $scriptUpdateComment 

## <a name="to-create-a-job-to-execute-a-script-across-a-shard-map"></a>Para criar uma tarefa para executar um script através de um mapa de partições horizontais
Este script do PowerShell inicia uma tarefa para execução de um script em cada partição horizontal um mapa de partições horizontais horizontal elástico.

Defina as seguintes variáveis para refletir o script pretendida e de destino:

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $shardMapServerName = "{Shard Map Server Name}"
    $shardMapDatabaseName = "{Shard Map Database Name}"
    $shardMapName = "{Shard Map Name}"
    $credentialName = "{Credential Name}"
    $shardMapTarget = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName 
    $job = New-AzureSqlJob -ContentName $scriptName -CredentialName $credentialName -JobName $jobName -TargetId $shardMapTarget.TargetId
    Write-Output $job

## <a name="to-execute-a-job"></a>Para executar uma tarefa
Este script do PowerShell executa uma tarefa existente:

Atualize a seguinte variável para refletir o nome da tarefa pretendida para ter executada:

    $jobName = "{Job Name}"
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName 
    Write-Output $jobExecution

## <a name="to-retrieve-the-state-of-a-single-job-execution"></a>Para obter o estado de execução de uma única tarefa
Utilize o [ **cmdlet Get-AzureSqlJobExecution** ](/powershell/module/elasticdatabasejobs/get-azuresqljobexecution) e defina o **JobExecutionId** parâmetro para ver o estado de execução da tarefa.

    $jobExecutionId = "{Job Execution Id}"
    $jobExecution = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId
    Write-Output $jobExecution

Utilizar o mesmo **Get-AzureSqlJobExecution** cmdlet com o **includechildren exige** parâmetro para ver o estado de execuções de tarefa subordinada, nomeadamente o estado específico para cada execução da tarefa em relação a cada base de dados visada pela tarefa.

    $jobExecutionId = "{Job Execution Id}"
    $jobExecutions = Get-AzureSqlJobExecution -JobExecutionId $jobExecutionId -IncludeChildren
    Write-Output $jobExecutions 

## <a name="to-view-the-state-across-multiple-job-executions"></a>Para ver o estado em várias execuções de tarefa
O [ **cmdlet Get-AzureSqlJobExecution** ](/powershell/module/elasticdatabasejobs/new-azuresqljob) tem vários parâmetros opcionais que podem ser utilizados para apresentar várias execuções de tarefa, filtradas através dos parâmetros fornecidos. O seguinte demonstra Algumas das formas possíveis para Utilize Get-AzureSqlJobExecution:

Obter todas as execuções de tarefas ativas de nível superior:

    Get-AzureSqlJobExecution

Obter todas as execuções de tarefa de nível superior, incluindo as execuções de tarefa inativo:

    Get-AzureSqlJobExecution -IncludeInactive

Obter todas as execuções de tarefa de subordinados de um ID de execução da tarefa fornecida, incluindo execuções de tarefa inativo:

    $parentJobExecutionId = "{Job Execution Id}"
    Get-AzureSqlJobExecution -AzureSqlJobExecution -JobExecutionId $parentJobExecutionId -IncludeInactive -IncludeChildren

Obter todas as execuções de tarefa criadas com base numa agenda / combinação, incluindo tarefas Inativas da tarefa:

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Get-AzureSqlJobExecution -JobName $jobName -ScheduleName $scheduleName -IncludeInactive

Obter todas as tarefas direcionada para um mapa de partições horizontais especificado, incluindo tarefas Inativas:

    $shardMapServerName = "{Shard Map Server Name}"
    $shardMapDatabaseName = "{Shard Map Database Name}"
    $shardMapName = "{Shard Map Name}"
    $target = Get-AzureSqlJobTarget -ShardMapManagerDatabaseName $shardMapDatabaseName -ShardMapManagerServerName $shardMapServerName -ShardMapName $shardMapName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive

Obter todas as tarefas direcionada para uma coleção personalizada especificada, incluindo tarefas Inativas:

    $customCollectionName = "{Custom Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    Get-AzureSqlJobExecution -TargetId $target.TargetId -IncludeInactive

Obter a lista de execuções de tarefas da tarefa em execução uma tarefa específica:

    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Write-Output $jobTaskExecutions 

Obter os detalhes de tarefa de execução da tarefa:

O seguinte script do PowerShell pode ser utilizado para ver os detalhes de uma execução de tarefas da tarefa, que é particularmente útil quando a depuração de falhas de execução.

    $jobTaskExecutionId = "{Job Task Execution Id}"
    $jobTaskExecution = Get-AzureSqlJobTaskExecution -JobTaskExecutionId $jobTaskExecutionId
    Write-Output $jobTaskExecution

## <a name="to-retrieve-failures-within-job-task-executions"></a>Para obter falhas num execuções de tarefas da tarefa
O **JobTaskExecution objeto** inclui uma propriedade para o ciclo de vida da tarefa, juntamente com uma propriedade de mensagem. Se uma execução de tarefas da tarefa falhou, a propriedade de ciclo de vida será definida para *falha* e a propriedade da mensagem será definida para a mensagem de exceção resultante e a pilha. Se uma tarefa não foi concluída com êxito, é importante ver os detalhes das tarefas que não foram concluída com êxito para uma determinada tarefa.

    $jobExecutionId = "{Job Execution Id}"
    $jobTaskExecutions = Get-AzureSqlJobTaskExecution -JobExecutionId $jobExecutionId
    Foreach($jobTaskExecution in $jobTaskExecutions) 
        {
        if($jobTaskExecution.Lifecycle -ne 'Succeeded')
            {
            Write-Output $jobTaskExecution
            }
        }

## <a name="to-wait-for-a-job-execution-to-complete"></a>A aguardar a conclusão da execução tarefa
O seguinte script do PowerShell pode ser utilizado para aguardar a tarefa concluir:

    $jobExecutionId = "{Job Execution Id}"
    Wait-AzureSqlJobExecution -JobExecutionId $jobExecutionId 

## <a name="create-a-custom-execution-policy"></a>Criar uma política de execução personalizado
As tarefas de base de dados elásticas suporta a criação de políticas de execução personalizadas que podem ser aplicadas ao iniciar trabalhos.

Atualmente permitem definir políticas de execução:

* Nome: Identificador para a política de execução.
* Tempo limite de tarefa: Total de tempo antes de uma tarefa será cancelada pelo tarefas de base de dados elásticas.
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

    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 10
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $executionPolicy = New-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval 
    -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $executionPolicy

### <a name="update-a-custom-execution-policy"></a>Atualizar uma política de execução personalizado
Atualize a política de execução pretendido para atualizar:

    $executionPolicyName = "{Execution Policy Name}"
    $initialRetryInterval = New-TimeSpan -Seconds 15
    $jobTimeout = New-TimeSpan -Minutes 30
    $maximumAttempts = 999999
    $maximumRetryInterval = New-TimeSpan -Minutes 1
    $retryIntervalBackoffCoefficient = 1.5
    $updatedExecutionPolicy = Set-AzureSqlJobExecutionPolicy -ExecutionPolicyName $executionPolicyName -InitialRetryInterval $initialRetryInterval -JobTimeout $jobTimeout -MaximumAttempts $maximumAttempts -MaximumRetryInterval $maximumRetryInterval -RetryIntervalBackoffCoefficient $retryIntervalBackoffCoefficient
    Write-Output $updatedExecutionPolicy

## <a name="cancel-a-job"></a>Cancelar uma tarefa
As tarefas de base de dados elásticas suporta pedidos de cancelamento de tarefas.  Se as tarefas de base de dados elásticas Deteta um pedido de cancelamento de uma tarefa atualmente a ser executado, irá tentar parar a tarefa.

Existem duas formas diferentes, que as tarefas de base de dados elásticas podem executar um cancelamento:

1. Cancelar tarefas a executar atualmente: se for detetado um cancelamento enquanto uma tarefa está atualmente em execução, será tentado a um cancelamento dentro do aspeto atualmente em execução da tarefa.  Por exemplo: se houver uma consulta de execução longa atualmente a ser efetuada quando é tentado um cancelamento, existirá uma tentativa para cancelar a consulta.
2. As repetições de tarefas a cancelar: se um cancelamento é detetado pelo thread controlo antes de uma tarefa é iniciada para execução, o thread de controlo será evitar a iniciar a tarefa e declarar o pedido como cancelada.

Se for pedido um cancelamento de tarefas para uma tarefa principal, o pedido de cancelamento será cumprido para a tarefa principal e para todos os respetivos tarefas subordinadas.

Para submeter um pedido de cancelamento, utilize o [ **cmdlet Stop-AzureSqlJobExecution** ](/powershell/module/elasticdatabasejobs/stop-azuresqljobexecution) e defina o **JobExecutionId** parâmetro.

    $jobExecutionId = "{Job Execution Id}"
    Stop-AzureSqlJobExecution -JobExecutionId $jobExecutionId

## <a name="to-delete-a-job-and-job-history-asynchronously"></a>Para eliminar uma tarefa e histórico da tarefa assíncrona
As tarefas de base de dados elásticas suporta eliminação assíncrona de tarefas. Uma tarefa pode ser marcada para eliminação e o sistema irá eliminar a tarefa e todos os respetivo histórico de tarefas depois de concluir todas as execuções de tarefa para a tarefa. O sistema não cancelará automaticamente execuções de tarefas ativas.  

Invocar [ **Stop-AzureSqlJobExecution** ](/powershell/module/elasticdatabasejobs/stop-azuresqljobexecution) Cancelar execuções de tarefas ativas.

Para acionar a eliminação da tarefa, utilize o [ **cmdlet Remove-AzureSqlJob** ](/powershell/module/elasticdatabasejobs/remove-azuresqljob) e defina o **JobName** parâmetro.

    $jobName = "{Job Name}"
    Remove-AzureSqlJob -JobName $jobName

## <a name="to-create-a-custom-database-target"></a>Para criar um destino de base de dados personalizada
Pode definir os destinos de base de dados personalizada para execução direta ou para inclusão dentro de um grupo de base de dados personalizada. Por exemplo, porque **conjuntos elásticos** são ainda não diretamente suportada através das APIs do PowerShell, pode criar um destino de base de dados personalizada e o destino de coleção de base de dados personalizada que abrange todas as bases de dados no conjunto.

Defina as seguintes variáveis para refletir as informações da base de dados pretendido:

    $databaseName = "{Database Name}"
    $databaseServerName = "{Server Name}"
    New-AzureSqlJobTarget -DatabaseName $databaseName -ServerName $databaseServerName 

## <a name="to-create-a-custom-database-collection-target"></a>Para criar uma base de dados personalizada recolha de destino
Utilize o [ **New-AzureSqlJobTarget** ](/powershell/module/elasticdatabasejobs/new-azuresqljobtarget) cmdlet para definir um destino de coleção da base de dados personalizada para permitir a execução em vários destinos de base de dados definido. Depois de criar um grupo de base de dados, as bases de dados podem ser associados ao destino coleção personalizada.

Defina as seguintes variáveis para refletir a configuração de destino pretendida coleção personalizada:

    $customCollectionName = "{Custom Database Collection Name}"
    New-AzureSqlJobTarget -CustomCollectionName $customCollectionName 

### <a name="to-add-databases-to-a-custom-database-collection-target"></a>Para adicionar bases de dados para um destino de coleção da base de dados personalizada
Para adicionar uma base de dados a utilizar uma coleção personalizada específica a [ **adicionar AzureSqlJobChildTarget** ](/powershell/module/elasticdatabasejobs/add-azuresqljobchildtarget) cmdlet.

    $databaseServerName = "{Database Server Name}"
    $databaseName = "{Database Name}"
    $customCollectionName = "{Custom Database Collection Name}"
    Add-AzureSqlJobChildTarget -CustomCollectionName $customCollectionName -DatabaseName $databaseName -ServerName $databaseServerName 

#### <a name="review-the-databases-within-a-custom-database-collection-target"></a>Reveja as bases de dados dentro de um destino de coleção da base de dados personalizada
Utilize o [ **Get-AzureSqlJobTarget** ](/powershell/module/elasticdatabasejobs/new-azuresqljobtarget) cmdlet para obter as bases de dados do subordinado dentro de uma base de dados personalizada recolha de destino. 

    $customCollectionName = "{Custom Database Collection Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $childTargets = Get-AzureSqlJobTarget -ParentTargetId $target.TargetId
    Write-Output $childTargets

### <a name="create-a-job-to-execute-a-script-across-a-custom-database-collection-target"></a>Criar uma tarefa para executar um script através de um destino de coleção da base de dados personalizada
Utilize o [ **New-AzureSqlJob** ](/powershell/module/elasticdatabasejobs/new-azuresqljob) cmdlet para criar uma tarefa em relação a um grupo de bases de dados definidas por um destino de coleção da base de dados personalizada. As tarefas de base de dados elásticas irão expandir a tarefa para várias tarefas de subordinados cada correspondente a uma base de dados associada com a base de dados personalizada recolha de destino e certifique-se de que o script é executado em relação a cada base de dados. Novamente, é importante que os scripts estão idempotent para sejam resilientes a tentativas.

    $jobName = "{Job Name}"
    $scriptName = "{Script Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob -JobName $jobName -CredentialName $credentialName -ContentName $scriptName -TargetId $target.TargetId
    Write-Output $job

## <a name="data-collection-across-databases"></a>Recolha de dados entre bases de dados
Pode utilizar uma tarefa para executar uma consulta entre um grupo de bases de dados e enviar os resultados para uma tabela específica. A tabela pode ser consultada após o facto para ver os resultados da consulta de cada base de dados. Esta opção fornece um método assíncrono para executar uma consulta em muitas bases de dados. Tentativas falhadas são processadas automaticamente através de tentativas.

A tabela de destino especificado será criada automaticamente se ainda não existir. A nova tabela corresponde ao esquema do conjunto de resultados devolvido. Se um script devolver vários conjuntos de resultados, tarefas de bases de dados elásticas enviar apenas o primeiro para a tabela de destino.

O seguinte script do PowerShell executa um script e recolhe os respetivos resultados numa tabela especificada. Este script parte do princípio de que foi criado um script T-SQL que produz um conjunto de resultados único e que foi criado uma base de dados personalizada recolha de destino.

Este script utiliza o [ **Get-AzureSqlJobTarget** ](/powershell/module/elasticdatabasejobs/new-azuresqljobtarget) cmdlet. Defina os parâmetros do script, credenciais e o destino de execução:

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

### <a name="to-create-and-start-a-job-for-data-collection-scenarios"></a>Para criar e iniciar uma tarefa para cenários de recolha de dados
Este script utiliza o [ **início AzureSqlJobExecution** ](/powershell/module/elasticdatabasejobs/start-azuresqljobexecution) cmdlet.

    $job = New-AzureSqlJob -JobName $jobName 
    -CredentialName $executionCredentialName 
    -ContentName $scriptName 
    -ResultSetDestinationServerName $destinationServerName 
    -ResultSetDestinationDatabaseName $destinationDatabaseName 
    -ResultSetDestinationSchemaName $destinationSchemaName 
    -ResultSetDestinationTableName $destinationTableName 
    -ResultSetDestinationCredentialName $destinationCredentialName 
    -TargetId $target.TargetId
    Write-Output $job
    $jobExecution = Start-AzureSqlJobExecution -JobName $jobName
    Write-Output $jobExecution

## <a name="to-schedule-a-job-execution-trigger"></a>Agendar um acionador de execução da tarefa
O seguinte script do PowerShell pode ser utilizado para criar uma agenda periódica. Este script utiliza um intervalo em minuto, mas [ **New-AzureSqlJobSchedule** ](/powershell/module/elasticdatabasejobs/new-azuresqljobschedule) também suporta os parâmetros - DayInterval, - HourInterval, - MonthInterval e - WeekInterval. As agendas que executar apenas uma vez que podem ser criadas por transmitir - OneTime.

Crie uma nova agenda:

    $scheduleName = "Every one minute"
    $minuteInterval = 1
    $startTime = (Get-Date).ToUniversalTime()
    $schedule = New-AzureSqlJobSchedule 
    -MinuteInterval $minuteInterval 
    -ScheduleName $scheduleName 
    -StartTime $startTime 
    Write-Output $schedule

### <a name="to-trigger-a-job-executed-on-a-time-schedule"></a>Para acionar uma tarefa executada num horário
Um acionador da tarefa pode ser definido para que uma tarefa executada segundo uma agenda de tempo. O seguinte script do PowerShell pode ser utilizado para criar um acionador da tarefa.

Utilize [New-AzureSqlJobTrigger](/powershell/module/elasticdatabasejobs/new-azuresqljobtrigger) e defina as seguintes variáveis de forma a corresponderem da tarefa pretendida e a agenda:

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    $jobTrigger = New-AzureSqlJobTrigger
    -ScheduleName $scheduleName
    -JobName $jobName
    Write-Output $jobTrigger

### <a name="to-remove-a-scheduled-association-to-stop-job-from-executing-on-schedule"></a>Para remover uma associação agendada para parar a tarefa de execução numa agenda
Para descontinuar a execução da tarefa a ocorrer através de um acionador da tarefa, o acionador da tarefa pode ser removido. Remover um acionador da tarefa para parar uma tarefa de que está a ser executado, de acordo com uma agenda a utilizar o [ **cmdlet Remove-AzureSqlJobTrigger**](/powershell/module/elasticdatabasejobs/remove-azuresqljobtrigger).

    $jobName = "{Job Name}"
    $scheduleName = "{Schedule Name}"
    Remove-AzureSqlJobTrigger 
    -ScheduleName $scheduleName 
    -JobName $jobName

### <a name="retrieve-job-triggers-bound-to-a-time-schedule"></a>Obter os acionadores de tarefa vinculados a uma agenda de tempo
O seguinte script do PowerShell pode ser utilizado para obter e apresentar os acionadores de tarefa registados para uma agenda de tempo específico.

    $scheduleName = "{Schedule Name}"
    $jobTriggers = Get-AzureSqlJobTrigger -ScheduleName $scheduleName
    Write-Output $jobTriggers

### <a name="to-retrieve-job-triggers-bound-to-a-job"></a>Para obter os acionadores de tarefa vinculada a uma tarefa
Utilize [Get-AzureSqlJobTrigger](/powershell/module/elasticdatabasejobs/get-azuresqljobtrigger) para obter e apresentar agendas que contém uma tarefa registada.

    $jobName = "{Job Name}"
    $jobTriggers = Get-AzureSqlJobTrigger -JobName $jobName
    Write-Output $jobTriggers

## <a name="to-create-a-data-tier-application-dacpac-for-execution-across-databases"></a>Para criar uma aplicação de camada de dados (DACPAC) para execução em bases de dados
Para criar um DACPAC, consulte [aplicações de camada de dados](https://msdn.microsoft.com/library/ee210546.aspx). Para implementar um DACPAC, utilize o [cmdlet New-AzureSqlJobContent](/powershell/module/elasticdatabasejobs/new-azuresqljobcontent). O DACPAC tem de estar acessível para o serviço. Recomenda-se para carregar um DACPAC criado para armazenamento do Azure e criar um [assinatura de acesso partilhado](../storage/common/storage-dotnet-shared-access-signature-part-1.md) para o DACPAC.

    $dacpacUri = "{Uri}"
    $dacpacName = "{Dacpac Name}"
    $dacpac = New-AzureSqlJobContent -DacpacUri $dacpacUri -ContentName $dacpacName 
    Write-Output $dacpac

### <a name="to-update-a-data-tier-application-dacpac-for-execution-across-databases"></a>Para atualizar uma aplicação de camada de dados (DACPAC) para execução em bases de dados
DACPACs existentes registados dentro de tarefas de base de dados elásticas podem ser atualizadas para que apontem para os URIs de novo. Utilize o [ **cmdlet Set-AzureSqlJobContentDefinition** ](/powershell/module/elasticdatabasejobs/set-azuresqljobcontentdefinition) atualizar o URI de DACPAC no existente registado DACPAC:

    $dacpacName = "{Dacpac Name}"
    $newDacpacUri = "{Uri}"
    $updatedDacpac = Set-AzureSqlJobDacpacDefinition -ContentName $dacpacName -DacpacUri $newDacpacUri
    Write-Output $updatedDacpac

## <a name="to-create-a-job-to-apply-a-data-tier-application-dacpac-across-databases"></a>Para criar uma tarefa para aplicar uma aplicação de camada de dados (DACPAC) em bases de dados
Depois de ter sido criado um DACPAC dentro de tarefas de base de dados elásticas, pode ser criada uma tarefa para aplicar o DACPAC entre um grupo de bases de dados. O seguinte script do PowerShell pode ser utilizado para criar uma tarefa DACPAC através de uma coleção personalizada das bases de dados:

    $jobName = "{Job Name}"
    $dacpacName = "{Dacpac Name}"
    $customCollectionName = "{Custom Collection Name}"
    $credentialName = "{Credential Name}"
    $target = Get-AzureSqlJobTarget 
    -CustomCollectionName $customCollectionName
    $job = New-AzureSqlJob 
    -JobName $jobName 
    -CredentialName $credentialName 
    -ContentName $dacpacName -TargetId $target.TargetId
    Write-Output $job 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-powershell/cmd-prompt.png
[2]: ./media/sql-database-elastic-jobs-powershell/portal.png
<!--anchors-->
