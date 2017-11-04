---
title: Execute uma tarefa de Hadoop, utilizando a BD do Cosmos do Azure e o HDInsight | Microsoft Docs
description: Saiba como executar uma tarefa do Hive, Pig e MapReduce simples com base de dados do Azure Cosmos e o Azure HDInsight.
services: cosmos-db
author: dennyglee
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 06f0ea9d-07cb-4593-a9c5-ab912b62ac42
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 06/08/2017
ms.author: denlee
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3c8789f08a37466862120dda88a0bce7da3e9a91
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="Azure Cosmos DB-HDInsight"></a>Executar uma tarefa Apache Hive, Pig ou Hadoop, utilizando a BD do Cosmos do Azure e o HDInsight
Este tutorial mostra como executar [Apache Hive][apache-hive], [Apache Pig][apache-pig], e [do Apache Hadoop] [ apache-hadoop] As tarefas do MapReduce no Azure HDInsight com o conector de Hadoop da BD do Cosmos. Conector de Hadoop da BD do cosmos permite BD do Cosmos agir como uma origem e o sink para tarefas do Hive, Pig e MapReduce. Este tutorial irá utilizar BD do Cosmos como a origem de dados e de destino para tarefas do Hadoop.

Depois de concluir este tutorial, poderá responder às seguintes questões:

* Como carregar dados a partir da BD de Cosmos utilizando uma tarefa do Hive, Pig ou MapReduce?
* Como posso armazenar dados na base de dados do Cosmos utilizando uma tarefa do Hive, Pig ou MapReduce?

Recomendamos que comece por ver o vídeo seguinte, onde iremos executar através de uma tarefa de ramo de registo utilizando a BD do Cosmos e o HDInsight.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Use-Azure-DocumentDB-Hadoop-Connector-with-Azure-HDInsight/player]
>
>

Em seguida, regresse a este artigo, onde irá receber os detalhes completos na forma como pode executar tarefas de análise nos seus dados de base de dados do Cosmos.

> [!TIP]
> Este tutorial parte do princípio de que tem experiência anterior na utilização do Apache Hadoop, Hive e/ou Pig. Se estiver familiarizado com o Apache Hadoop, Hive e Pig, é recomendável visitando o [documentação do Apache Hadoop][apache-hadoop-doc]. Este tutorial também assume que tem experiência anterior com a base de dados do Cosmos e ter uma conta de base de dados do Cosmos. Se estiver familiarizado com a base de dados do Cosmos ou não dispõe de uma conta de base de dados do Cosmos, consulte a nossa [introdução] [ getting-started] página.
>
>

Não tem tempo para concluir o tutorial e apenas pretende obter os scripts do PowerShell de exemplo completo para o Hive, Pig e MapReduce? Não é um problema, obtenha-los [aqui][hdinsight-samples]. A transferência também contém os ficheiros hql, pig e java para estes exemplos.

## <a name="NewestVersion"></a>Versão mais recente
<table border='1'>
    <tr><th>Versão do conector de Hadoop</th>
        <td>1.2.0</td></tr>
    <tr><th>Uri de script</th>
        <td>https://portalcontent.blob.Core.Windows.NET/scriptaction/documentdb-hadoop-Installer-v04.ps1</td></tr>
    <tr><th>Data de modificação</th>
        <td>04/26/2016</td></tr>
    <tr><th>Versões do HDInsight suportados</th>
        <td>3.1, 3.2</td></tr>
    <tr><th>Registo de Alterações</th>
        <td>Atualizar do Azure Cosmos DB Java SDK para 1.6.0</br>
            Suporte adicionado para coleções particionadas como uma origem e o sink</br>
        </td></tr>
</table>

## <a name="Prerequisites"></a>Pré-requisitos
Antes de seguir as instruções neste tutorial, certifique-se de que tem o seguinte:

* Uma conta de base de dados do Cosmos, uma base de dados e uma coleção com documentos no interior. Para obter mais informações, consulte [introdução à base de dados do Cosmos][getting-started]. Importar dados de exemplo para a conta de base de dados do Cosmos com o [ferramenta de importação de BD do Cosmos][import-data].
* Débito. Lê e escreve do HDInsight serão contados tendo para as unidades de pedido atribuído para as suas coleções.
* Capacidade para um procedimento armazenado adicional dentro de cada coleção de saída. Os procedimentos armazenados são utilizados para a transferência de documentos resultantes.
* Capacidade para os documentos resultantes das tarefas do Hive, Pig ou MapReduce.
* [*Opcional*] capacidade para uma coleção adicional.

> [!WARNING]
> Para evitar a criação de uma nova coleção durante qualquer uma das tarefas, pode imprimir os resultados stdout, guarde o resultado para o contentor WASB ou, especifique uma coleção já existente. No caso de especificar uma coleção existente, serão criados novos documentos dentro da coleção e documentos já existentes apenas serão afetados se houver um conflito nas *ids*. **O conector irá substituir automaticamente documentos existentes com conflitos de id**. Pode desativar esta funcionalidade, definindo a opção de upsert como false. Se upsert está definido como false e ocorre um conflito, que a tarefa de Hadoop falhará; um erro de conflitos de id de relatórios.
>
>

## <a name="ProvisionHDInsight"></a>Passo 1: Criar um novo cluster do HDInsight
Este tutorial utiliza a ação de Script do Portal do Azure para personalizar o cluster do HDInsight. Neste tutorial, utilizaremos o Portal do Azure para criar o cluster do HDInsight. Para obter instruções sobre como utilizar os cmdlets do PowerShell ou o SDK .NET do HDInsight, consulte o [clusters do HDInsight de personalizar através da ação de Script] [ hdinsight-custom-provision] artigo.

1. Iniciar sessão para o [Portal do Azure][azure-portal].
2. Clique em **+ novo** no topo do painel de navegação esquerdo, procure **HDInsight** na barra de pesquisa superior no painel de novo.
3. **HDInsight** publicados pelo **Microsoft** serão apresentados na parte superior dos resultados. Clique no mesmo e, em seguida, clique em **criar**.
4. No novo Cluster de HDInsight painel criar, introduza o **nome do Cluster** e selecione o **subscrição** que pretende aprovisionar este recurso em.

    <table border='1'>
        <tr><td>Nome do cluster</td><td>Nome do cluster.<br/>
Tem de nome de DNS iniciar e terminar com um caráter numérico alpha e pode conter traços.<br/>
O campo tem de ser uma cadeia entre 3 e 63 carateres.</td></tr>
        <tr><td>Nome da subscrição</td>
            <td>Se tiver mais do que uma subscrição do Azure, selecione a subscrição que irá alojar o cluster do HDInsight. </td></tr>
    </table>
5.Clique em **selecionar tipo de Cluster** e defina as propriedades seguintes aos valores especificados.

    <table border='1'>
        <tr><td>Tipo de cluster</td><td><strong>Hadoop</strong></td></tr>
        <tr><td>Camada de cluster</td><td><strong>Standard</strong></td></tr>
        <tr><td>Sistema Operativo</td><td><strong>Windows</strong></td></tr>
        <tr><td>Versão</td><td>versão mais recente</td></tr>
    </table>

    Agora, clique em **SELECIONE**.

    ![Fornecer os detalhes do cluster inicial de Hadoop HDInsight][image-customprovision-page1]
6. Clique em **credenciais** para definir o seu início de sessão e credenciais de acesso remoto. Escolha o **nome de utilizador de início de sessão do Cluster** e **palavra-passe de início de sessão do Cluster**.

    Se pretender que a remoto para o cluster, selecione *Sim* na parte inferior do painel e forneça um nome de utilizador e palavra-passe.
7. Clique em **origem de dados** para definir a sua localização principal para acesso a dados. Escolha o **método de seleção** e especifique uma conta de armazenamento já existente ou crie um novo.
8. No painel do mesmo, especifique um **contentor predefinido** e um **localização**. E, clique em **SELECIONE**.

   > [!NOTE]
   > Selecione uma localização perto a região da conta de base de dados do Cosmos para um melhor desempenho
   >
   >
9. Clique em **preços** para selecionar o número e tipo de nós. Pode manter a configuração predefinida e aumentar o número de nós de trabalho mais tarde.
10. Clique em **configuração opcional**, em seguida, **ações de Script** no painel de configuração opcional.

     Nas ações de Script, introduza as informações seguintes para personalizar o cluster do HDInsight.

     <table border='1'>
         <tr><th>Propriedade</th><th>Valor</th></tr>
         <tr><td>Nome</td>
             <td>Especifique um nome para a ação de script.</td></tr>
         <tr><td>URI de script</td>
             <td>Especifique o URI para o script que é invocado para personalizar o cluster.</br></br>
Introduza: </br> <strong>https://portalcontent.blob.Core.Windows.NET/scriptaction/documentdb-hadoop-Installer-v04.ps1</strong>.</td></tr>
         <tr><td>Head</td>
             <td>Clique na caixa de verificação para executar o script do PowerShell para o nó principal.</br></br>
             <strong>Selecione esta caixa de verificação</strong>.</td></tr>
         <tr><td>Worker</td>
             <td>Clique na caixa de verificação para executar o script do PowerShell para o nó de trabalho.</br></br>
             <strong>Selecione esta caixa de verificação</strong>.</td></tr>
         <tr><td>Zookeeper</td>
             <td>Clique na caixa de verificação para executar o script do PowerShell para o Zookeeper.</br></br>
             <strong>Não é necessária</strong>.
             </td></tr>
         <tr><td>Parâmetros</td>
             <td>Especifique os parâmetros, se necessário, o script.</br></br>
             <strong>Não existem parâmetros necessários</strong>.</td></tr>
     </table>
11.Criar uma nova **grupo de recursos** ou utilize um grupo de recursos existente na sua subscrição do Azure.
12. Agora, verifique **afixar ao dashboard** para controlar a implementação e clique em **criar**!

## <a name="InstallCmdlets"></a>Passo 2: Instalar e configurar o Azure PowerShell
1. Instale o Azure PowerShell. Pode encontrar instruções [aqui][powershell-install-configure].

   > [!NOTE]
   > Em alternativa, apenas para consultas do Hive, pode utilizar o Editor de ramo de registo online do HDInsight. Para tal, iniciar sessão para o [Portal do Azure][azure-portal], clique em **HDInsight** no painel da esquerda para ver uma lista dos seus clusters do HDInsight. Clique no cluster que pretende executar consultas do Hive e, em seguida, clique em **consulta consola**.
   >
   >
2. Abra o ambiente de script integrada do Azure PowerShell:

   * Num computador com Windows 8 ou Windows Server 2012 ou superior, pode utilizar a pesquisa incorporada. A partir do ecrã Iniciar, escreva **ise do powershell** e clique em **Enter**.
   * Num computador com uma versão anterior ao Windows 8 ou Windows Server 2012, utilize o menu de início. A partir do menu Iniciar, escreva **linha de comandos** na caixa de pesquisa e, em seguida, na lista de resultados, clique em **linha de comandos**. Na linha de comandos, escreva **powershell_ise** e clique em **Enter**.
3. Adicione a sua conta do Azure.

   1. No painel de consola, escreva **Add-AzureAccount** e clique em **Enter**.
   2. Escreva o endereço de correio eletrónico associado à sua subscrição do Azure e clique em **continuar**.
   3. Escreva a palavra-passe para a sua subscrição do Azure.
   4. Clique em **sessão**.
4. O diagrama seguinte identifica as partes importantes do seu ambiente de script do PowerShell do Azure.

    ![Diagrama para o Azure PowerShell][azure-powershell-diagram]

## <a name="RunHive"></a>Passo 3: Executar uma tarefa de ramo de registo utilizando a BD do Cosmos e o HDInsight
> [!IMPORTANT]
> Todas as variáveis indicadas pelo < > tem de ser preenchidas utilizando as definições de configuração.
>
>

1. Defina as seguintes variáveis no seu painel de Script do PowerShell.

        # Provide Azure subscription name, the Azure Storage account and container that is used for the default HDInsight file system.
        $subscriptionName = "<SubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"
        $containerName = "<AzureStorageContainerName>"

        # Provide the HDInsight cluster name where you want to run the Hive job.
        $clusterName = "<HDInsightClusterName>"
2. <p>Vamos começar a construir a cadeia de consulta. Iremos irá escrever uma consulta do Hive, que demora a todos os documentos gerada pelo sistema carimbos (_ts) e os ids exclusivos (_rid) de uma coleção de base de dados do Azure Cosmos, tallies todos os documentos por minuto e, em seguida, armazena os resultados novamente para uma nova coleção de BD do Cosmos do Azure.</p>

    <p>Em primeiro lugar, vamos criar uma tabela do Hive da nossa coleção da base de dados do Azure Cosmos. Adicione o seguinte fragmento de código para o painel de Script do PowerShell <strong>depois</strong> o fragmento de código de #1. Certifique-se de que incluem o t de parâmetro de DocumentDB.query opcional compactação nosso documentos _ts apenas e _rid.</p>

   > [!NOTE]
   > **Nomenclatura DocumentDB.inputCollections não era um erro.** Sim, iremos permitir a adição de várias coleções como entrada: </br>
   >
   >

        '*DocumentDB.inputCollections*' = '*\<DocumentDB Input Collection Name 1\>*,*\<DocumentDB Input Collection Name 2\>*' A1A</br> The collection names are separated without spaces, using only a single comma.

        # Create a Hive table using data from DocumentDB. Pass DocumentDB the query to filter transferred data to _rid and _ts.
        $queryStringPart1 = "drop table DocumentDB_timestamps; "  +
                            "create external table DocumentDB_timestamps(id string, ts BIGINT) "  +
                            "stored by 'com.microsoft.azure.documentdb.hive.DocumentDBStorageHandler' "  +
                            "tblproperties ( " +
                                "'DocumentDB.endpoint' = '<DocumentDB Endpoint>', " +
                                "'DocumentDB.key' = '<DocumentDB Primary Key>', " +
                                "'DocumentDB.db' = '<DocumentDB Database Name>', " +
                                "'DocumentDB.inputCollections' = '<DocumentDB Input Collection Name>', " +
                                "'DocumentDB.query' = 'SELECT r._rid AS id, r._ts AS ts FROM root r' ); "

1. Em seguida, vamos criar uma tabela do Hive para a coleção de saída. As propriedades do documento de saída serão mês, dia, hora, minuto e o número total de ocorrências.

   > [!NOTE]
   > **Ainda novamente, a atribuição de nomes DocumentDB.outputCollections não era um erro.** Sim, iremos permitir a adição de várias coleções como uma saída: </br>
   > '*DocumentDB.outputCollections*'='*\<o nome da coleção de saída do DocumentDB 1\>*,*\<o nome da coleção de saída do DocumentDB 2\>* ' </br> Os nomes de colecção são separados sem espaços, com apenas uma único vírgula. </br></br>
   > Documentos será distribuído round-robin em várias coleções. Um lote de documentos será armazenado numa coleção, em seguida, um segundo lote de documentos será armazenado na seguinte coleção e assim sucessivamente.
   >
   >

       # Create a Hive table for the output data to DocumentDB.
       $queryStringPart2 = "drop table DocumentDB_analytics; " +
                             "create external table DocumentDB_analytics(Month INT, Day INT, Hour INT, Minute INT, Total INT) " +
                             "stored by 'com.microsoft.azure.documentdb.hive.DocumentDBStorageHandler' " +
                             "tblproperties ( " +
                                 "'DocumentDB.endpoint' = '<DocumentDB Endpoint>', " +
                                 "'DocumentDB.key' = '<DocumentDB Primary Key>', " +  
                                 "'DocumentDB.db' = '<DocumentDB Database Name>', " +
                                 "'DocumentDB.outputCollections' = '<DocumentDB Output Collection Name>' ); "
2. Por fim, vamos os documentos de contabilização por mês, dia, hora e minutos e inserir os resultados de volta a saída do tabela do Hive.

        # GROUP BY minute, COUNT entries for each, INSERT INTO output Hive table.
        $queryStringPart3 = "INSERT INTO table DocumentDB_analytics " +
                              "SELECT month(from_unixtime(ts)) as Month, day(from_unixtime(ts)) as Day, " +
                              "hour(from_unixtime(ts)) as Hour, minute(from_unixtime(ts)) as Minute, " +
                              "COUNT(*) AS Total " +
                              "FROM DocumentDB_timestamps " +
                              "GROUP BY month(from_unixtime(ts)), day(from_unixtime(ts)), " +
                              "hour(from_unixtime(ts)) , minute(from_unixtime(ts)); "
3. Adicione o seguinte fragmento de script para criar uma definição de tarefa do Hive da consulta anterior.

        # Create a Hive job definition.
        $queryString = $queryStringPart1 + $queryStringPart2 + $queryStringPart3
        $hiveJobDefinition = New-AzureHDInsightHiveJobDefinition -Query $queryString

    Também pode utilizar o - comutador de ficheiro para especificar um ficheiro de script de HiveQL no HDFS.
4. Adicione o fragmento seguinte para guardar a hora de início e submeter a tarefa do Hive.

        # Save the start time and submit the job to the cluster.
        $startTime = Get-Date
        Select-AzureSubscription $subscriptionName
        $hiveJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $hiveJobDefinition
5. Adicione o seguinte ao aguardar a conclusão da tarefa do Hive.

        # Wait for the Hive job to complete.
        Wait-AzureHDInsightJob -Job $hiveJob -WaitTimeoutInSeconds 3600
6. Adicione o seguinte para a saída padrão e os tempos de início e de fim de impressão.

        # Print the standard error, the standard output of the Hive job, and the start and end time.
        $endTime = Get-Date
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardOutput
        Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green
7. **Executar** o script novo! **Clique em** botão verde executar.
8. Verificar os resultados. Inicie sessão no [Portal do Azure][azure-portal].

   1. Clique em <strong>procurar</strong> no painel da esquerda. </br>
   2. Clique em <strong>tudo</strong> na parte superior direita do painel de navegação. </br>
   3. Localize e clique em <strong>contas de base de dados do Azure Cosmos</strong>. </br>
   4. Em seguida, localize o <strong>conta de base de dados do Azure Cosmos</strong>, em seguida, <strong>base de dados do Azure Cosmos DB</strong> e os seus <strong>Azure Cosmos DB coleção</strong> associados à coleção de saída especificada na sua Consulta do Hive.</br>
   5. Por fim, clique em <strong>Explorador de documentos</strong> por baixo <strong>ferramentas de programador</strong>.</br></p>

   Verá os resultados da sua consulta do Hive.

   ![Resultados da consulta do Hive][image-hive-query-results]

## <a name="RunPig"></a>Passo 4: Executar uma tarefa de Pig utilizando a BD do Cosmos e o HDInsight
> [!IMPORTANT]
> Todas as variáveis indicadas pelo < > tem de ser preenchidas utilizando as definições de configuração.
>
>

1. Defina as seguintes variáveis no seu painel de Script do PowerShell.

        # Provide Azure subscription name.
        $subscriptionName = "Azure Subscription Name"

        # Provide HDInsight cluster name where you want to run the Pig job.
        $clusterName = "Azure HDInsight Cluster Name"
2. <p>Vamos começar a construir a cadeia de consulta. Iremos irá escrever uma consulta de Pig que demora a todos os documentos gerada pelo sistema carimbos (_ts) e os ids exclusivos (_rid) de uma coleção de base de dados do Azure Cosmos, tallies todos os documentos por minuto e, em seguida, armazena os resultados novamente para uma nova coleção de BD do Cosmos do Azure.</p>
    <p>Em primeiro lugar, carregar documentos da base de dados do Cosmos para o HDInsight. Adicione o seguinte fragmento de código para o painel de Script do PowerShell <strong>depois</strong> o fragmento de código de #1. Certifique-se de que adiciona uma consulta do DocumentDB para o parâmetro de consulta do DocumentDB opcional cortar nosso documentos _ts apenas e _rid.</p>

   > [!NOTE]
   > Sim, iremos permitir a adição de várias coleções como entrada: </br>
   > '*\<o nome da coleção de entrada do DocumentDB 1\>*,*\<o nome da coleção de entrada do DocumentDB 2\>*'</br> Os nomes de colecção são separados sem espaços, com apenas uma único vírgula. </b>
   >
   >

    Documentos será distribuído round-robin em várias coleções. Um lote de documentos será armazenado numa coleção, em seguida, um segundo lote de documentos será armazenado na seguinte coleção e assim sucessivamente.

        # Load data from Cosmos DB. Pass DocumentDB query to filter transferred data to _rid and _ts.
        $queryStringPart1 = "DocumentDB_timestamps = LOAD '<DocumentDB Endpoint>' USING com.microsoft.azure.documentdb.pig.DocumentDBLoader( " +
                                                        "'<DocumentDB Primary Key>', " +
                                                        "'<DocumentDB Database Name>', " +
                                                        "'<DocumentDB Input Collection Name>', " +
                                                        "'SELECT r._rid AS id, r._ts AS ts FROM root r' ); "
3. Em seguida, vamos contabilização os documentos por mês, dia, hora, minuto e o número total de ocorrências.

       # GROUP BY minute and COUNT entries for each.
       $queryStringPart2 = "timestamp_record = FOREACH DocumentDB_timestamps GENERATE `$0#'id' as id:int, ToDate((long)(`$0#'ts') * 1000) as timestamp:datetime; " +
                           "by_minute = GROUP timestamp_record BY (GetYear(timestamp), GetMonth(timestamp), GetDay(timestamp), GetHour(timestamp), GetMinute(timestamp)); " +
                           "by_minute_count = FOREACH by_minute GENERATE FLATTEN(group) as (Year:int, Month:int, Day:int, Hour:int, Minute:int), COUNT(timestamp_record) as Total:int; "
4. Por fim, vamos armazenar os resultados na nossa nova coleção de saída.

   > [!NOTE]
   > Sim, iremos permitir a adição de várias coleções como uma saída: </br>
   > '\<o nome da coleção de saída do DocumentDB 1\>,\<o nome da coleção de saída do DocumentDB 2\>'</br> Os nomes de colecção são separados sem espaços, com apenas uma único vírgula.</br>
   > Documentos será distribuído round-robin em várias coleções. Um lote de documentos será armazenado numa coleção, em seguida, um segundo lote de documentos será armazenado na seguinte coleção e assim sucessivamente.
   >
   >

        # Store output data to Cosmos DB.
        $queryStringPart3 = "STORE by_minute_count INTO '<DocumentDB Endpoint>' " +
                            "USING com.microsoft.azure.documentdb.pig.DocumentDBStorage( " +
                                "'<DocumentDB Primary Key>', " +
                                "'<DocumentDB Database Name>', " +
                                "'<DocumentDB Output Collection Name>'); "
5. Adicione o seguinte fragmento de script para criar uma definição de tarefa Pig da consulta anterior.

        # Create a Pig job definition.
        $queryString = $queryStringPart1 + $queryStringPart2 + $queryStringPart3
        $pigJobDefinition = New-AzureHDInsightPigJobDefinition -Query $queryString -StatusFolder $statusFolder

    Também pode utilizar o - comutador de ficheiro para especificar um ficheiro de script Pig no HDFS.
6. Adicione o fragmento seguinte para guardar a hora de início e submeter a tarefa Pig.

        # Save the start time and submit the job to the cluster.
        $startTime = Get-Date
        Select-AzureSubscription $subscriptionName
        $pigJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $pigJobDefinition
7. Adicione o seguinte ao aguardar a conclusão da tarefa Pig.

        # Wait for the Pig job to complete.
        Wait-AzureHDInsightJob -Job $pigJob -WaitTimeoutInSeconds 3600
8. Adicione o seguinte para a saída padrão e os tempos de início e de fim de impressão.

        # Print the standard error, the standard output of the Hive job, and the start and end time.
        $endTime = Get-Date
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardOutput
        Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green
9. **Executar** o script novo! **Clique em** botão verde executar.
10. Verificar os resultados. Inicie sessão no [Portal do Azure][azure-portal].

    1. Clique em <strong>procurar</strong> no painel da esquerda. </br>
    2. Clique em <strong>tudo</strong> na parte superior direita do painel de navegação. </br>
    3. Localize e clique em <strong>contas de base de dados do Azure Cosmos</strong>. </br>
    4. Em seguida, localize o <strong>conta de base de dados do Azure Cosmos</strong>, em seguida, <strong>base de dados do Azure Cosmos DB</strong> e os seus <strong>Azure Cosmos DB coleção</strong> associados à coleção de saída especificada na sua Consulta de PIg.</br>
    5. Por fim, clique em <strong>Explorador de documentos</strong> por baixo <strong>ferramentas de programador</strong>.</br></p>

    Verá os resultados da sua consulta Pig.

    ![Resultados da consulta PIg][image-pig-query-results]

## <a name="RunMapReduce"></a>Passo 5: Executar uma tarefa de MapReduce utilizando a BD do Cosmos do Azure e o HDInsight
1. Defina as seguintes variáveis no seu painel de Script do PowerShell.

        $subscriptionName = "<SubscriptionName>"   # Azure subscription name
        $clusterName = "<ClusterName>"             # HDInsight cluster name
2. Iremos irá executar uma tarefa de MapReduce tallies o número de ocorrências para cada propriedade de documento da sua coleção de BD do Cosmos do Azure. Adicionar este fragmento do script **depois** fragmento acima.

        # Define the MapReduce job.
        $TallyPropertiesJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/TallyProperties-v01.jar" -ClassName "TallyProperties" -Arguments "<DocumentDB Endpoint>","<DocumentDB Primary Key>", "<DocumentDB Database Name>","<DocumentDB Input Collection Name>","<DocumentDB Output Collection Name>","<[Optional] DocumentDB Query>"

   > [!NOTE]
   > TallyProperties v01.jar é fornecido com a instalação personalizada do conector de Hadoop da BD do Cosmos.
   >
   >
3. Adicione o seguinte comando para submeter a tarefa de MapReduce.

        # Save the start time and submit the job.
        $startTime = Get-Date
        Select-AzureSubscription $subscriptionName
        $TallyPropertiesJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $TallyPropertiesJobDefinition | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600  

    Para além da definição de tarefa de MapReduce, também fornecer o nome do cluster de HDInsight onde pretende executar a tarefa de MapReduce e as credenciais. A iniciar-AzureHDInsightJob é uma chamada asynchronized. Para verificar a conclusão da tarefa, utilize o *espera AzureHDInsightJob* cmdlet.
4. Adicione o seguinte comando para verificar os erros com executando a tarefa de MapReduce.

        # Get the job output and print the start and end time.
        $endTime = Get-Date
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $TallyPropertiesJob.JobId -StandardError
        Write-Host "Start: " $startTime ", End: " $endTime -ForegroundColor Green
5. **Executar** o script novo! **Clique em** botão verde executar.
6. Verificar os resultados. Inicie sessão no [Portal do Azure][azure-portal].

   1. Clique em <strong>procurar</strong> no painel da esquerda.
   2. Clique em <strong>tudo</strong> na parte superior direita do painel de navegação.
   3. Localize e clique em <strong>contas de base de dados do Azure Cosmos</strong>.
   4. Em seguida, localize o <strong>conta de base de dados do Azure Cosmos</strong>, em seguida, <strong>base de dados do Azure Cosmos DB</strong> e os seus <strong>Azure Cosmos DB coleção</strong> associados à coleção de saída especificada na sua Tarefa de MapReduce.
   5. Por fim, clique em <strong>Explorador de documentos</strong> por baixo <strong>ferramentas de programador</strong>.

      Verá os resultados da sua tarefa de MapReduce.

      ![Resultados da consulta de MapReduce][image-mapreduce-query-results]

## <a name="NextSteps"></a>Passos Seguintes
Parabéns! Executou apenas as tarefas do Hive, Pig e MapReduce primeiro utilizando a BD do Cosmos do Azure e o HDInsight.

Temos open tenham origem a nossa conector Hadoop. Se estiver interessado, pode contribuir no [GitHub][github].

Para obter mais informações, consulte os artigos seguintes:

* [Desenvolver uma aplicação de Java com o Documentdb][documentdb-java-application]
* [Desenvolver programas de Java MapReduce para o Hadoop no HDInsight][hdinsight-develop-deploy-java-mapreduce]
* [Introdução à utilização do Hadoop com o Hive no HDInsight para analisar a utilização de handset móveis][hdinsight-get-started]
* [Utilizar o MapReduce com o HDInsight][hdinsight-use-mapreduce]
* [Use Hive with HDInsight (Utilizar o Hive com o HDInsight)][hdinsight-use-hive]
* [Use Pig with HDInsight (Utilizar o Pig com o HDInsight)][hdinsight-use-pig]
* [Personalizar clusters do HDInsight através da ação de Script][hdinsight-hadoop-customize-cluster]

[apache-hadoop]: http://hadoop.apache.org/
[apache-hadoop-doc]: http://hadoop.apache.org/docs/current/
[apache-hive]: http://hive.apache.org/
[apache-pig]: http://pig.apache.org/
[getting-started]: documentdb-get-started.md

[azure-portal]: https://portal.azure.com/
[azure-powershell-diagram]: ./media/run-hadoop-with-hdinsight/azurepowershell-diagram-med.png

[hdinsight-samples]: http://portalcontent.blob.core.windows.net/samples/documentdb-hdinsight-samples.zip
[github]: https://github.com/Azure/azure-documentdb-hadoop
[documentdb-java-application]: documentdb-java-application.md
[import-data]: import-data.md

[hdinsight-custom-provision]: ../hdinsight/hdinsight-provision-clusters.md
[hdinsight-develop-deploy-java-mapreduce]:../hdinsight/hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md
[hdinsight-hadoop-customize-cluster]: ../hdinsight/hdinsight-hadoop-customize-cluster.md
[hdinsight-get-started]: ../hdinsight/hdinsight-hadoop-tutorial-get-started-windows.md
[hdinsight-storage]: ../hdinsight/hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]:../hdinsight/hadoop/hdinsight-use-hive.md
[hdinsight-use-mapreduce]:../hdinsight/hadoop/hdinsight-use-mapreduce.md
[hdinsight-use-pig]:../hdinsight/hadoop/hdinsight-use-pig.md

[image-customprovision-page1]: ./media/run-hadoop-with-hdinsight/customprovision-page1.png
[image-hive-query-results]: ./media/run-hadoop-with-hdinsight/hivequeryresults.PNG
[image-mapreduce-query-results]: ./media/run-hadoop-with-hdinsight/mapreducequeryresults.PNG
[image-pig-query-results]: ./media/run-hadoop-with-hdinsight/pigqueryresults.PNG

[powershell-install-configure]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.0.0
