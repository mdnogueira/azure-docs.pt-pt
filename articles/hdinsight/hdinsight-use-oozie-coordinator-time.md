---
title: Utilizar baseados no tempo coordenador de Oozie do Hadoop no HDInsight | Microsoft Docs
description: "Utilize baseados no tempo coordenador de Oozie do Hadoop no HDInsight, um serviço de macrodados. Saiba como definir Oozie fluxos de trabalho e os coordenadores e submeter as tarefas."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 00c3a395-d51a-44ff-af2d-1f116c4b1c83
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: 0fa8e3630610913d909a75bf76236d120c8f1a2b
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2017
---
# <a name="use-time-based-oozie-coordinator-with-hadoop-in-hdinsight-to-define-workflows-and-coordinate-jobs"></a>Utilize o coordenador de Oozie baseado no tempo com o Hadoop no HDInsight para definir fluxos de trabalho e coordenar as tarefas
Neste artigo, irá aprender como definir fluxos de trabalho e os coordenadores e como acionar as tarefas de coordenador, com base no tempo. É útil seguir [Oozie de utilização com o HDInsight] [ hdinsight-use-oozie] antes de ler este artigo. Para além do Oozie, também pode agendar tarefas utilizando o Azure Data Factory. Para obter o Azure Data Factory, consulte [Pig de utilização e o Hive com o Data Factory](../data-factory/transform-data.md).

> [!NOTE]
> Este artigo necessita de um cluster do HDInsight baseados em Windows. Para obter informações sobre como utilizar o Oozie, incluindo tarefas baseados no tempo, num cluster baseado em Linux, consulte [Oozie de utilização com o Hadoop para definir e executar um fluxo de trabalho no HDInsight baseado em Linux](hdinsight-use-oozie-linux-mac.md)

## <a name="what-is-oozie"></a>O que é Oozie
O Apache Oozie é um sistema de coordenação/fluxo de trabalho que gere as tarefas do Hadoop. Está integrado com a pilha do Hadoop e suporta tarefas do Hadoop para Apache MapReduce, Apache Pig, Apache Hive e Apache Sqoop. Também pode ser utilizado para agendar tarefas específicas para um sistema, tais como programas de Java ou scripts de shell.

A imagem seguinte mostra o fluxo de trabalho que implementará:

![Diagrama de fluxo de trabalho][img-workflow-diagram]

O fluxo de trabalho contém duas ações:

1. Uma ação de ramo de registo executa um script de HiveQL para contagem de ocorrências de cada tipo de nível de registo num ficheiro de registo log4j. Cada registo de log4j é constituída por uma linha de campos que contém um campo [nível de registo] para mostrar o tipo e a gravidade, por exemplo:

        2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
        2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
        2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
        ...

    O resultado do script de ramo de registo é semelhante a:

        [DEBUG] 434
        [ERROR] 3
        [FATAL] 1
        [INFO]  96
        [TRACE] 816
        [WARN]  4

    Para obter mais informações sobre o Hive, consulte [Utilizar o Hive com o HDInsight][hdinsight-use-hive].
2. Uma ação de Sqoop exporta o resultado da ação de HiveQL para uma tabela numa base de dados SQL do Azure. Para obter mais informações sobre Sqoop, consulte [utilize Sqoop com o HDInsight][hdinsight-use-sqoop].

> [!NOTE]
> Para versões suportadas do Oozie nos clusters do HDInsight, consulte [Novidades nas versões do cluster fornecidas pelo HDInsight?] [hdinsight-versions].
>
>

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, tem de ter o seguinte:

* **Uma estação de trabalho com o Azure PowerShell**.

    > [!IMPORTANT]
    > O suporte do Azure PowerShell para gerir recursos do HDInsight com o Azure Service Manager foi **preterido** e será removido até 1 de janeiro de 2017. Os passos neste documento utilizam os novos cmdlets do HDInsight que funcionam com o Azure Resource Manager.
    >
    > Siga os passos em [Instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs) para instalar a versão mais recente do Azure PowerShell. Se tiver scripts que tenham de ser modificados para utilizar os novos cmdlets que funcionam com o Azure Resource Manager, veja [Migrar para as ferramentas de desenvolvimento baseadas no Azure Resource Manager para clusters do HDInsight](hdinsight-hadoop-development-using-azure-resource-manager.md) para obter mais informações.

* **Um cluster do HDInsight**. Para obter informações sobre como criar um cluster do HDInsight, consulte [criar clusters do HDInsight][hdinsight-provision], ou [introdução ao HDInsight][hdinsight-get-started]. Terá dos dados para seguir o tutorial:

    <table border = "1">
    <tr><th>Propriedade do cluster</th><th>Nome da variável do Windows PowerShell</th><th>Valor</th><th>Descrição</th></tr>
    <tr><td>Nome de cluster do HDInsight</td><td>$clusterName</td><td></td><td>O cluster do HDInsight em que serão executadas neste tutorial.</td></tr>
    <tr><td>O nome de utilizador do HDInsight cluster</td><td>$clusterUsername</td><td></td><td>O nome de utilizador de cluster do HDInsight. </td></tr>
    <tr><td>Palavra-passe do utilizador de cluster do HDInsight </td><td>$clusterPassword</td><td></td><td>A HDInsight cluster utilizador palavra-passe.</td></tr>
    <tr><td>Nome da conta de armazenamento do Azure</td><td>$storageAccountName</td><td></td><td>Uma conta de armazenamento do Azure disponível para o cluster do HDInsight. Para este tutorial, utilize a conta do storage predefinida que especificou durante o processo de aprovisionamento de cluster.</td></tr>
    <tr><td>Nome do contentor de Blob do Azure</td><td>$containerName</td><td></td><td>Neste exemplo, utilize o contentor de armazenamento de Blobs do Azure que é utilizado para o sistema de ficheiros do cluster de HDInsight predefinido. Por predefinição, tem o mesmo nome que o cluster do HDInsight.</td></tr>
    </table>

* **Uma base de dados SQL do Azure**. Tem de configurar uma regra de firewall para o servidor de base de dados do SQL Server permitir o acesso da sua estação de trabalho. Para obter instruções sobre como criar uma base de dados SQL do Azure e a configuração da firewall, consulte [começar a utilizar a SQL database do Azure][sqldatabase-get-started]. Este artigo fornece um script do Windows PowerShell para criar a tabela de base de dados SQL do Azure que precisa para este tutorial.

    <table border = "1">
    <tr><th>Propriedade de base de dados do SQL Server</th><th>Nome da variável do Windows PowerShell</th><th>Valor</th><th>Descrição</th></tr>
    <tr><td>Nome de servidor de base de dados do SQL Server</td><td>$sqlDatabaseServer</td><td></td><td>O servidor de base de dados SQL à qual Sqoop irá exportar dados. </td></tr>
    <tr><td>Nome de início de sessão do SQL Server da base de dados</td><td>$sqlDatabaseLogin</td><td></td><td>Nome de início de sessão da base de dados SQL.</td></tr>
    <tr><td>Palavra-passe de início de sessão do SQL Server da base de dados</td><td>$sqlDatabaseLoginPassword</td><td></td><td>Palavra-passe de início de sessão de base de dados SQL.</td></tr>
    <tr><td>Nome da base de dados SQL</td><td>$sqlDatabaseName</td><td></td><td>A base de dados SQL do Azure para o qual Sqoop irá exportar dados. </td></tr>
    </table>

  > [!NOTE]
  > Por predefinição uma base de dados SQL do Azure permite ligações de serviços do Azure, como o Azure HDInsight. Se esta definição de firewall estiver desativada, deve ativá-la a partir do Portal do Azure. Para instruções sobre como criar uma base de dados do SQL Server e configurar regras de firewall, consulte [criar e configurar a base de dados do SQL][sqldatabase-get-started].

> [!NOTE]
> Fill-in os valores nas tabelas. Será útil para percorrer neste tutorial.

## <a name="define-oozie-workflow-and-the-related-hiveql-script"></a>Definir o fluxo de trabalho do Oozie e o script de HiveQL relacionado
Definições de fluxos de trabalho do Oozie são escritas no hPDL (uma linguagem de definição XML do processo). O nome de ficheiro de fluxo de trabalho predefinido é *workflow.xml*.  Irá guardar o ficheiro de fluxo de trabalho localmente e, em seguida, implementá-la para o cluster do HDInsight ao utilizar o Azure PowerShell mais tarde no tutorial.

A ação de ramo de registo no fluxo de trabalho chama um ficheiro de script de HiveQL. Este ficheiro de script contém três declarações HiveQL:

1. **A instrução DROP TABLE** elimina a tabela de Hive log4j se existir.
2. **A instrução CREATE TABLE** cria uma tabela de externa da Hive log4j, que aponta para a localização do ficheiro de registo log4j;
3. **A localização do ficheiro de registo de log4j**. O delimitador de campos é ",". O delimitador de linha predefinido é "\n". Uma tabela externa do ramo de registo é utilizada para evitar o ficheiro de dados a serem removido da localização original, no caso de que pretende executar o fluxo de trabalho do Oozie várias vezes.
4. **A instrução INSERT substituir** contagens de ocorrências de cada tipo de nível de registo da tabela do Hive de log4j e guarda a saída para uma localização de armazenamento de Blobs do Azure.

> [!NOTE]
> Não há um problema de caminho conhecido do ramo de registo. Será executado para este problema quando submete uma tarefa de Oozie. As instruções para corrigir o problema podem ser encontradas no TechNet Wiki: [do Hive do HDInsight erro: não é possível mudar o nome][technetwiki-hive-error].

**Para definir o ficheiro de script de HiveQL para ser chamados pelo fluxo de trabalho**

1. Crie um ficheiro de texto com o seguinte conteúdo:

        DROP TABLE ${hiveTableName};
        CREATE EXTERNAL TABLE ${hiveTableName}(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
        INSERT OVERWRITE DIRECTORY '${hiveOutputFolder}' SELECT t4 AS sev, COUNT(*) AS cnt FROM ${hiveTableName} WHERE t4 LIKE '[%' GROUP BY t4;

    Existem três variáveis utilizadas no script:

   * ${hiveTableName}
   * ${hiveDataFolder}
   * ${hiveOutputFolder}

     O ficheiro de definição de fluxo de trabalho (workflow.xml neste tutorial) irá passar estes valores para este script de HiveQL no tempo de execução.
2. Guarde o ficheiro como **C:\Tutorials\UseOozie\useooziewf.hql** utilizando a codificação ANSI (ASCII). (Utilizar o bloco de notas se seu editor de texto não fornecer esta opção.) Este ficheiro de script será implementado para o cluster do HDInsight mais tarde no tutorial.

**Para definir um fluxo de trabalho**

1. Crie um ficheiro de texto com o seguinte conteúdo:

    ```xml
    <workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
        <start to = "RunHiveScript"/>

        <action name="RunHiveScript">
            <hive xmlns="uri:oozie:hive-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                    <property>
                        <name>mapred.job.queue.name</name>
                        <value>${queueName}</value>
                    </property>
                </configuration>
                <script>${hiveScript}</script>
                <param>hiveTableName=${hiveTableName}</param>
                <param>hiveDataFolder=${hiveDataFolder}</param>
                <param>hiveOutputFolder=${hiveOutputFolder}</param>
            </hive>
            <ok to="RunSqoopExport"/>
            <error to="fail"/>
        </action>

        <action name="RunSqoopExport">
            <sqoop xmlns="uri:oozie:sqoop-action:0.2">
                <job-tracker>${jobTracker}</job-tracker>
                <name-node>${nameNode}</name-node>
                <configuration>
                    <property>
                        <name>mapred.compress.map.output</name>
                        <value>true</value>
                    </property>
                </configuration>
            <arg>export</arg>
            <arg>--connect</arg>
            <arg>${sqlDatabaseConnectionString}</arg>
            <arg>--table</arg>
            <arg>${sqlDatabaseTableName}</arg>
            <arg>--export-dir</arg>
            <arg>${hiveOutputFolder}</arg>
            <arg>-m</arg>
            <arg>1</arg>
            <arg>--input-fields-terminated-by</arg>
            <arg>"\001"</arg>
            </sqoop>
            <ok to="end"/>
            <error to="fail"/>
        </action>

        <kill name="fail">
            <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
        </kill>

        <end name="end"/>
    </workflow-app>
    ```

    Existem duas ações definidas no fluxo de trabalho. A ação de início é *RunHiveScript*. Se executar a ação *OK*, é a próxima ação *RunSqoopExport*.

    O RunHiveScript tem várias variáveis. Irá passar os valores ao submeter a tarefa de Oozie da sua estação de trabalho com o Azure PowerShell.

    Variáveis de fluxo de trabalho

    <table border = "1">
    <tr><th>Variáveis de fluxo de trabalho</th><th>Descrição</th></tr>
    <tr><td>${jobTracker}</td><td>Especifique o URL de controlador de tarefa Hadoop. Utilize <strong>jobtrackerhost:9010</strong> no HDInsight versão 3.0 e 2.0 do cluster.</td></tr>
    <tr><td>${nameNode}</td><td>Especifique o URL do nó de nome do Hadoop. Utilize o wasb de sistema de ficheiros predefinido: / / endereço, por exemplo, <i>wasb: / /&lt;containerName&gt;@&lt;storageAccountName&gt;. w</i>.</td></tr>
    <tr><td>${queueName}</td><td>Especifica que a tarefa irá ser submetida para o nome da fila. Utilize <strong>predefinido</strong>.</td></tr>
    </table>

    Variáveis de ação de ramo de registo

    <table border = "1">
    <tr><th>Variável de ação de ramo de registo</th><th>Descrição</th></tr>
    <tr><td>${hiveDataFolder}</td><td>O diretório de origem para o comando de ramo de registo Create Table.</td></tr>
    <tr><td>${hiveOutputFolder}</td><td>A pasta de saída para a instrução INSERT substituir.</td></tr>
    <tr><td>${hiveTableName}</td><td>O nome da tabela de Hive que faça referência os ficheiros de dados de log4j.</td></tr>
    </table>

    Variáveis de ação Sqoop

    <table border = "1">
    <tr><th>Variável de ação de Sqoop</th><th>Descrição</th></tr>
    <tr><td>${sqlDatabaseConnectionString}</td><td>Cadeia de ligação de base de dados SQL.</td></tr>
    <tr><td>${sqlDatabaseTableName}</td><td>A tabela de base de dados SQL do Azure para onde os dados serão exportados.</td></tr>
    <tr><td>${hiveOutputFolder}</td><td>A pasta de saída para a instrução INSERT substituir o ramo de registo. Esta é a mesma pasta para a exportação de Sqoop (export-dir).</td></tr>
    </table>

    Para obter mais informações sobre o fluxo de trabalho do Oozie e utilizando as ações de fluxo de trabalho, consulte [documentação do Apache Oozie 4.0] [ apache-oozie-400] (para clusters do HDInsight versão 3.0) ou [documentação do Apache Oozie 3.3.2 ] [ apache-oozie-332] (para clusters do HDInsight versão 2.1).

1. Guarde o ficheiro como **C:\Tutorials\UseOozie\workflow.xml** utilizando a codificação ANSI (ASCII). (Utilizar o bloco de notas se seu editor de texto não fornecer esta opção.)

**Para definir coordenador**

1. Crie um ficheiro de texto com o seguinte conteúdo:

    ```xml
    <coordinator-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
        <action>
            <workflow>
                <app-path>${wfPath}</app-path>
            </workflow>
        </action>
    </coordinator-app>
    ```

    Existem cinco variáveis utilizadas no ficheiro de definição:

   | Variável | Descrição |
   | --- | --- |
   | ${coordFrequency} |Tempo de interrupção da tarefa. Frequência sempre é expresso em minutos. |
   | ${coordStart} |Hora de início da tarefa. |
   | ${coordEnd} |Hora de fim da tarefa. |
   | ${coordTimezone} |Oozie processa as tarefas de coordenador num fuso horário fixo com sem hora de Verão (normalmente representada utilizando a UTC). Este fuso horário é referido como o "processamento de Oozie fuso horário." |
   | ${wfPath} |O caminho para o workflow.xml.  Se o nome de ficheiro de fluxo de trabalho não é o nome de ficheiro predefinido (workflow.xml), têm de especificá-lo. |
2. Guarde o ficheiro como **C:\Tutorials\UseOozie\coordinator.xml** utilizando a codificação de ANSI (ASCII). (Utilizar o bloco de notas se seu editor de texto não fornecer esta opção.)

## <a name="deploy-the-oozie-project-and-prepare-the-tutorial"></a>Implementar o projeto de Oozie e preparar o tutorial
Irá executar um script do PowerShell do Azure para efetuar o seguinte:

* Copie o script de HiveQL (useoozie.hql) para o Blob storage do Azure, wasb:///tutorials/useoozie/useoozie.hql.
* Copie workflow.xml para wasb:///tutorials/useoozie/workflow.xml.
* Copie coordinator.xml para wasb:///tutorials/useoozie/coordinator.xml.
* Copie o ficheiro de dados (/ example/data/sample.log) para wasb:///tutorials/useoozie/data/sample.log.
* Crie uma tabela de base de dados SQL do Azure para armazenar dados de exportação Sqoop. O nome da tabela é *log4jLogCount*.

**Compreender o armazenamento do HDInsight**

HDInsight utiliza o Blob storage do Azure para armazenamento de dados. wasb: / / é a implementação da Microsoft do sistema de ficheiros distribuído do Hadoop (HDFS) no armazenamento de Blobs do Azure. Para obter mais informações, consulte [utilize Blob storage do Azure com o HDInsight][hdinsight-storage].

Quando aprovisionar um cluster do HDInsight, uma conta de armazenamento de Blobs do Azure e um contentor específico dessa conta é designado como o sistema de ficheiros, como no HDFS. Para além desta conta de armazenamento, pode adicionar mais contas do storage da mesma subscrição do Azure ou a partir de diferentes subscrições do Azure durante o processo de aprovisionamento. Para obter instruções sobre como adicionar mais contas do storage, consulte [aprovisionar clusters do HDInsight][hdinsight-provision]. Para simplificar o script do PowerShell do Azure utilizado neste tutorial, todos os ficheiros são armazenados no contentor de sistema de ficheiros predefinido localizado em */tutoriais/useoozie*. Por predefinição, este contentor tem o mesmo nome que o nome de cluster do HDInsight.
A sintaxe é:

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [!NOTE]
> Apenas o *wasb: / /* sintaxe é suportada em clusters do HDInsight versão 3.0. Mais antigo *asv: / /* sintaxe é suportada no HDInsight 2.1 e 1.6 clusters, mas não é suportado em clusters de HDInsight 3.0.
>
> O wasb: / / caminho é um caminho virtual. Para obter mais informações consulte [utilize Blob storage do Azure com o HDInsight][hdinsight-storage].

Um ficheiro que é armazenado no contentor do sistema de ficheiros predefinido pode ser acedido a partir do HDInsight utilizando uma dos URIs seguintes (estiver a utilizar o workflow.xml como um exemplo):

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/workflow.xml
    wasb:///tutorials/useoozie/workflow.xml
    /tutorials/useoozie/workflow.xml

Se pretender aceder ao ficheiro diretamente a partir da conta de armazenamento, o nome do blob para o ficheiro é:

    tutorials/useoozie/workflow.xml

**Compreender a tabelas do Hive de internas e externas**

Existem algumas coisas que precisa de saber sobre tabelas do Hive de internas e externas:

* O comando CREATE TABLE cria uma tabela interna, também conhecido como uma tabela gerida. O ficheiro de dados tem de estar localizado no contentor predefinido.
* O comando CREATE TABLE move o ficheiro de dados para o /hiveArmazém/<TableName> pasta no contentor predefinido.
* O comando Criar tabela externa cria uma tabela externa. O ficheiro de dados pode ser localizado fora do contentor predefinido.
* O comando Criar tabela externa não move o ficheiro de dados.
* O comando Criar tabela externa não permite que todas as subpastas na pasta que é especificado na cláusula de localização. É este o motivo por que motivo o tutorial efetua uma cópia do ficheiro sample.log.

Para obter mais informações, consulte [HDInsight: Hive internos e externos de tabelas de introdução][cindygross-hive-tables].

**Para preparar o tutorial**

1. Abra o ISE do Windows PowerShell (no ecrã Iniciar do Windows 8, escreva **PowerShell_ISE**e, em seguida, clique em **ISE do Windows PowerShell**. Para obter mais informações, consulte [iniciar o Windows PowerShell no Windows 8 e Windows][powershell-start]).
2. No painel inferior, execute o seguinte comando para ligar à sua subscrição do Azure:

    ```powershell
    Add-AzureAccount
    ```

    Será solicitado para introduzir as credenciais da conta do Azure. Este método de adição de uma ligação de subscrição expira e decorridas 12 horas, terá de executar o cmdlet novamente.

   > [!NOTE]
   > Se tiver várias subscrições do Azure e a subscrição predefinida não é o que pretende utilizar, utilize o <strong>Select-AzureSubscription</strong> cmdlet para selecionar uma subscrição.

3. Copie o seguinte script no painel de script e, em seguida, defina as primeiras seis variáveis:

    ```powershell
    # WASB variables
    $storageAccountName = "<StorageAccountName>"
    $containerName = "<BlobStorageContainerName>"

    # SQL database variables
    $sqlDatabaseServer = "<SQLDatabaseServerName>"
    $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
    $sqlDatabaseLoginPassword = "SQLDatabaseLoginPassword>"
    $sqlDatabaseName = "<SQLDatabaseName>"
    $sqlDatabaseTableName = "log4jLogsCount"

    # Oozie files for the tutorial
    $hiveQLScript = "C:\Tutorials\UseOozie\useooziewf.hql"
    $workflowDefinition = "C:\Tutorials\UseOozie\workflow.xml"
    $coordDefinition =  "C:\Tutorials\UseOozie\coordinator.xml"

    # WASB folder for storing the Oozie tutorial files.
    $destFolder = "tutorials/useoozie"  # Do NOT use the long path here
    ```

    Para obter mais descrições das variáveis, consulte o [pré-requisitos](#prerequisites) secção neste tutorial.

4. Acrescente o seguinte script no painel de script:

    ```powershell
    # Create a storage context object
    $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

    function uploadOozieFiles()
    {
        Write-Host "Copy HiveQL script, workflow definition and coordinator definition ..." -ForegroundColor Green
        Set-AzureStorageBlobContent -File $hiveQLScript -Container $containerName -Blob "$destFolder/useooziewf.hql" -Context $destContext
        Set-AzureStorageBlobContent -File $workflowDefinition -Container $containerName -Blob "$destFolder/workflow.xml" -Context $destContext
        Set-AzureStorageBlobContent -File $coordDefinition -Container $containerName -Blob "$destFolder/coordinator.xml" -Context $destContext
    }

    function prepareHiveDataFile()
    {
        Write-Host "Make a copy of the sample.log file ... " -ForegroundColor Green
        Start-CopyAzureStorageBlob -SrcContainer $containerName -SrcBlob "example/data/sample.log" -Context $destContext -DestContainer $containerName -destBlob "$destFolder/data/sample.log" -DestContext $destContext
    }

    function prepareSQLDatabase()
    {
        # SQL query string for creating log4jLogsCount table
        $cmdCreateLog4jCountTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
                [Level] [nvarchar](10) NOT NULL,
                [Total] float,
            CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED
            (
            [Level] ASC
            )
            )"

        #Create the log4jLogsCount table
        Write-Host "Create Log4jLogsCount table ..." -ForegroundColor Green
        $conn = New-Object System.Data.SqlClient.SqlConnection
        $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabaseLoginPassword;Encrypt=true;Trusted_Connection=false;"
        $conn.open()
        $cmd = New-Object System.Data.SqlClient.SqlCommand
        $cmd.connection = $conn
        $cmd.commandtext = $cmdCreateLog4jCountTable
        $cmd.executenonquery()

        $conn.close()
    }

    # upload workflow.xml, coordinator.xml, and ooziewf.hql
    uploadOozieFiles;

    # make a copy of example/data/sample.log to example/data/log4j/sample.log
    prepareHiveDataFile;

    # create log4jlogsCount table on SQL database
    prepareSQLDatabase;
    ```

5. Clique em **executar Script** ou prima **F5** para executar o script. O resultado será semelhante a:

    ![Saída de preparação do tutorial][img-preparation-output]

## <a name="run-the-oozie-project"></a>Executar o projeto de Oozie
O Azure PowerShell atualmente não fornece quaisquer cmdlets para definir as tarefas de Oozie. Pode utilizar o **Invoke-RestMethod** cmdlet invocar Oozie os serviços web. A API de serviços web do Oozie é uma API de JSON de REST de HTTP. Para mais informações sobre os serviços web de Oozie API, consulte [documentação do Apache Oozie 4.0] [ apache-oozie-400] (para clusters do HDInsight versão 3.0) ou [documentação do Apache Oozie 3.3.2] [ apache-oozie-332] (para clusters do HDInsight versão 2.1).

**Para submeter uma tarefa de Oozie**

1. Abra o ISE do Windows PowerShell (no ecrã Iniciar do Windows 8, escreva **PowerShell_ISE**e, em seguida, clique em **ISE do Windows PowerShell**. Para obter mais informações, consulte [iniciar o Windows PowerShell no Windows 8 e Windows][powershell-start]).
2. Copie o seguinte script no painel de script e, em seguida, definir as variáveis primeiro fourteen (no entanto, ignorar **$storageUri**).

    ```powershell
    #HDInsight cluster variables
    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterUserPassword>"

    #Azure Blob storage (WASB) variables
    $storageAccountName = "<StorageAccountName>"
    $storageContainerName = "<BlobContainerName>"
    $storageUri="wasb://$storageContainerName@$storageAccountName.blob.core.windows.net"

    #Azure SQL database variables
    $sqlDatabaseServer = "<SQLDatabaseServerName>"
    $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
    $sqlDatabaseLoginPassword = "<SQLDatabaseloginPassword>"
    $sqlDatabaseName = "<SQLDatabaseName>"

    #Oozie WF/coordinator variables
    $coordStart = "2014-03-21T13:45Z"
    $coordEnd = "2014-03-21T13:45Z"
    $coordFrequency = "1440"    # in minutes, 24h x 60m = 1440m
    $coordTimezone = "UTC"    #UTC/GMT

    $oozieWFPath="$storageUri/tutorials/useoozie"  # The default name is workflow.xml. And you don't need to specify the file name.
    $waitTimeBetweenOozieJobStatusCheck=10

    #Hive action variables
    $hiveScript = "$storageUri/tutorials/useoozie/useooziewf.hql"
    $hiveTableName = "log4jlogs"
    $hiveDataFolder = "$storageUri/tutorials/useoozie/data"
    $hiveOutputFolder = "$storageUri/tutorials/useoozie/output"

    #Sqoop action variables
    $sqlDatabaseConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServer;password=$sqlDatabaseLoginPassword;database=$sqlDatabaseName"  
    $sqlDatabaseTableName = "log4jLogsCount"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)
    ```

    Para obter mais descrições das variáveis, consulte o [pré-requisitos](#prerequisites) secção neste tutorial.

    $coordstart $coordend, sendo o fluxo de trabalho inicial e final do tempo. Para obter a hora UTC/GMT, procure "Hora utc" no bing.com. O $coordFrequency é a frequência em minutos que pretende executar o fluxo de trabalho.
3. Acrescente o seguinte para o script. Esta parte define o payload de Oozie:

    ```powershell
    #OoziePayload used for Oozie web service submission
    $OoziePayload =  @"
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>

        <property>
            <name>nameNode</name>
            <value>$storageUrI</value>
        </property>

        <property>
            <name>jobTracker</name>
            <value>jobtrackerhost:9010</value>
        </property>

        <property>
            <name>queueName</name>
            <value>default</value>
        </property>

        <property>
            <name>oozie.use.system.libpath</name>
            <value>true</value>
        </property>

        <property>
            <name>oozie.coord.application.path</name>
            <value>$oozieWFPath</value>
        </property>

        <property>
            <name>wfPath</name>
            <value>$oozieWFPath</value>
        </property>

        <property>
            <name>coordStart</name>
            <value>$coordStart</value>
        </property>

        <property>
            <name>coordEnd</name>
            <value>$coordEnd</value>
        </property>

        <property>
            <name>coordFrequency</name>
            <value>$coordFrequency</value>
        </property>

        <property>
            <name>coordTimezone</name>
            <value>$coordTimezone</value>
        </property>

        <property>
            <name>hiveScript</name>
            <value>$hiveScript</value>
        </property>

        <property>
            <name>hiveTableName</name>
            <value>$hiveTableName</value>
        </property>

        <property>
            <name>hiveDataFolder</name>
            <value>$hiveDataFolder</value>
        </property>

        <property>
            <name>hiveOutputFolder</name>
            <value>$hiveOutputFolder</value>
        </property>

        <property>
            <name>sqlDatabaseConnectionString</name>
            <value>&quot;$sqlDatabaseConnectionString&quot;</value>
        </property>

        <property>
            <name>sqlDatabaseTableName</name>
            <value>$SQLDatabaseTableName</value>
        </property>

        <property>
            <name>user.name</name>
            <value>admin</value>
        </property>

    </configuration>
    "@
    ```

   > [!NOTE]
   > A principal diferença em comparação comparada o ficheiro de payload de submissão de fluxo de trabalho é a variável **oozie.coord.application.path**. Quando submete uma tarefa de fluxo de trabalho, utilize **oozie.wf.application.path** em vez disso.

4. Acrescente o seguinte para o script. Esta parte verifica o estado do serviço web Oozie:

    ```powershell
    function checkOozieServerStatus()
    {
        Write-Host "Checking Oozie server status..." -ForegroundColor Green
        $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/admin/status"
        $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds -OutVariable $OozieServerStatus

        $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
        $oozieServerSatus = $jsonResponse[0].("systemMode")
        Write-Host "Oozie server status is $oozieServerSatus..."

        if($oozieServerSatus -notmatch "NORMAL")
        {
            Write-Host "Oozie server status is $oozieServerSatus...cannot submit Oozie jobs. Check the server status and re-run the job."
        }
    }
    ```

5. Acrescente o seguinte para o script. Esta parte cria uma tarefa de Oozie:

    ```powershell
    function createOozieJob()
    {
        # create Oozie job
        Write-Host "Sending the following Payload to the cluster:" -ForegroundColor Green
        Write-Host "`n--------`n$OoziePayload`n--------"
        $clusterUriCreateJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/jobs"
        $response = Invoke-RestMethod -Method Post -Uri $clusterUriCreateJob -Credential $creds -Body $OoziePayload -ContentType "application/xml" -OutVariable $OozieJobName -debug -Verbose

        $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
        $oozieJobId = $jsonResponse[0].("id")
        Write-Host "Oozie job id is $oozieJobId..."

        return $oozieJobId
    }
    ```

   > [!NOTE]
   > Quando submete uma tarefa de fluxo de trabalho, tem de se outra chamada para iniciar a tarefa depois da tarefa é criada do serviço web. Neste caso, a tarefa de coordenador é acionada por hora. A tarefa será iniciada automaticamente.

6. Acrescente o seguinte para o script. Esta parte verifica o estado da tarefa Oozie:

    ```powershell
    function checkOozieJobStatus($oozieJobId)
    {
        # get job status
        Write-Host "Sleeping for $waitTimeBetweenOozieJobStatusCheck seconds until the job metadata is populated in the Oozie metastore..." -ForegroundColor Green
        Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck

        Write-Host "Getting job status and waiting for the job to complete..." -ForegroundColor Green
        $clusterUriGetJobStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?show=info"
        $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $creds
        $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
        $JobStatus = $jsonResponse[0].("status")

        while($JobStatus -notmatch "SUCCEEDED|KILLED")
        {
            Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state...waiting $waitTimeBetweenOozieJobStatusCheck seconds for the job to complete..."
            Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck
            $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $creds
            $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
            $JobStatus = $jsonResponse[0].("status")
        }

        Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state!"
        if($JobStatus -notmatch "SUCCEEDED")
        {
            Write-Host "Check logs at http://headnode0:9014/cluster for detais."
        }
    }
    ```

7. (Opcional) Acrescente o seguinte para o script.

    ```powershell
    function listOozieJobs()
    {
        Write-Host "Listing Oozie jobs..." -ForegroundColor Green
        $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/jobs"
        $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds

        write-host "Job ID                                   App Name        Status      Started                         Ended"
        write-host "----------------------------------------------------------------------------------------------------------------------------------"
        foreach($job in $response.workflows)
        {
            Write-Host $job.id "`t" $job.appName "`t" $job.status "`t" $job.startTime "`t" $job.endTime
        }
    }

    function ShowOozieJobLog($oozieJobId)
    {
        Write-Host "Showing Oozie job info..." -ForegroundColor Green
        $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/$oozieJobId" + "?show=log"
        $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds
        write-host $response
    }

    function killOozieJob($oozieJobId)
    {
        Write-Host "Killing the Oozie job $oozieJobId..." -ForegroundColor Green
        $clusterUriStartJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?action=kill" #Valid values for the 'action' parameter are 'start', 'suspend', 'resume', 'kill', 'dryrun', 'rerun', and 'change'.
        $response = Invoke-RestMethod -Method Put -Uri $clusterUriStartJob -Credential $creds | Format-Table -HideTableHeaders -debug
    }
    ```

8. Acrescente o seguinte ao script:

    ```powershell
    checkOozieServerStatus
    # listOozieJobs
    $oozieJobId = createOozieJob($oozieJobId)
    checkOozieJobStatus($oozieJobId)
    # ShowOozieJobLog($oozieJobId)
    # killOozieJob($oozieJobId)
    ```

Remova os sinais de # se pretender executar as funções adicionais.

9. Se o cluster do HDinsight é uma versão 2.1, substitua "https://$clusterName.azurehdinsight.net:443/oozie/v2/" com "https://$clusterName.azurehdinsight.net:443/oozie/v1/". Clusters do HDInsight versão 2.1 não não suporta a versão 2 dos serviços web.
10. Clique em **executar Script** ou prima **F5** para executar o script. O resultado será semelhante a:

     ![Tutorial executar saída de fluxo de trabalho][img-runworkflow-output]
11. Ligar à base de dados SQL para ver os dados exportados.

**Para verificar o registo de erros da tarefa**

Para resolver um fluxo de trabalho, o ficheiro de registo do Oozie pode encontrar no C:\apps\dist\oozie-3.3.2.1.3.2.0-05\oozie-win-distro\logs\Oozie.log headnode o cluster. Para obter informações sobre o RDP, consulte [clusters do HDInsight de administrar com o portal do Azure][hdinsight-admin-portal].

**Para voltar a executar o tutorial**

Para voltar a executar o fluxo de trabalho, tem de efetuar as seguintes tarefas:

* Elimine o ficheiro de saída do script de ramo de registo.
* Elimine os dados na tabela log4jLogsCount.

Eis um exemplo de script de Windows PowerShell que pode utilizar:

```powershell
$storageAccountName = "<AzureStorageAccountName>"
$containerName = "<ContainerName>"

#SQL database variables
$sqlDatabaseServer = "<SQLDatabaseServerName>"
$sqlDatabaseLogin = "<SQLDatabaseLoginName>"
$sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>"
$sqlDatabaseName = "<SQLDatabaseName>"
$sqlDatabaseTableName = "log4jLogsCount"

Write-host "Delete the Hive script output file ..." -ForegroundColor Green
$storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey
Remove-AzureStorageBlob -Context $destContext -Blob "tutorials/useoozie/output/000000_0" -Container $containerName

Write-host "Delete all the records from the log4jLogsCount table ..." -ForegroundColor Green
$conn = New-Object System.Data.SqlClient.SqlConnection
$conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabaseLoginPassword;Encrypt=true;Trusted_Connection=false;"
$conn.open()
$cmd = New-Object System.Data.SqlClient.SqlCommand
$cmd.connection = $conn
$cmd.commandtext = "delete from $sqlDatabaseTableName"
$cmd.executenonquery()

$conn.close()
```

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, aprendeu como definir um fluxo de trabalho do Oozie e coordenador de Oozie e como executar uma tarefa de coordenador de Oozie com o Azure PowerShell. Para obter mais informações, consulte os artigos seguintes:

* [Introdução ao HDInsight][hdinsight-get-started]
* [Utilizar o Blob storage do Azure com o HDInsight][hdinsight-storage]
* [Administrar HDInsight ao utilizar o Azure PowerShell][hdinsight-admin-powershell]
* [Upload data to HDInsight (Carregar dados para o HDInsight)][hdinsight-upload-data]
* [Utilizar o Sqoop com o HDInsight][hdinsight-use-sqoop]
* [Use Hive with HDInsight (Utilizar o Hive com o HDInsight)][hdinsight-use-hive]
* [Use Pig with HDInsight (Utilizar o Pig com o HDInsight)][hdinsight-use-pig]
* [Desenvolver programas de Java MapReduce para o HDInsight][hdinsight-develop-java-mapreduce]

[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563

[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md

[hdinsight-use-sqoop]:hadoop/hdinsight-use-sqoop.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-develop-java-mapreduce]:hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md

[sqldatabase-get-started]: ../sql-database/sql-database-get-started.md

[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]:../storage/common/storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://azure.microsoft.com/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.Preparation.Output1.png
[img-runworkflow-output]: ./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.RunCoord.Output.png

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
