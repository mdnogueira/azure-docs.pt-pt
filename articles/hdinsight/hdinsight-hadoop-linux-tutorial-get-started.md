---
title: 'Tutorial do Linux: introdução ao Hadoop e ao Hive | Microsoft Docs'
description: Siga este tutorial do Linux para começar a utilizar o Hadoop no HDInsight. Saiba como aprovisionar clusters do Linux e consultar dados com o Hive.
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal

ms.service: hdinsight
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/14/2016
ms.author: jgao

---
# Tutorial do Hadoop: começar a utilizar o Hadoop baseado em Linux no HDInsight
> [!div class="op_single_selector"]
> * [Baseado em Linux](hdinsight-hadoop-linux-tutorial-get-started.md)
> * [Baseado em Windows](hdinsight-hadoop-tutorial-get-started-windows.md)
> 
> 

Saiba como criar clusters do [Hadoop](http://hadoop.apache.org/) baseados em Linux no HDInsight e como executar tarefas do Hive no HDInsight. O [Apache Hive](https://hive.apache.org/) é o componente mais popular do ecossistema Hadoop. Atualmente, o HDInsight inclui quatro tipos de cluster diferentes: [Hadoop](hdinsight-hadoop-introduction.md), [Spark](hdinsight-apache-spark-overview.md), [HBase](hdinsight-hbase-overview.md) e [Storm](hdinsight-storm-overview.md).  Cada tipo de cluster suporta um conjunto diferente de componentes. Todos os 4 tipos de cluster suportam o Hive. Para obter uma lista dos componentes suportados no HDInsight, consulte [Quais são as novidades nas versões de cluster do Hadoop fornecidas pelo HDInsight?](hdinsight-component-versioning.md)  

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

### Pré-requisitos
Antes de começar este tutorial, tem de ter os seguintes elementos:

* **Subscrição do Azure**: para criar uma conta de avaliação gratuita de um mês, aceda a [azure.microsoft.com/free](https://azure.microsoft.com/free).

## Criar cluster
A maioria das tarefas do Hadoop são tarefas de lote. Cria um cluster, executa algumas tarefas e, em seguida, elimina o cluster. Nesta secção, vai criar um cluster do Hadoop baseado em Linux no HDInsight com o [modelo do Azure Resource Manager](../resource-group-template-deploy.md). O modelo do Resource Manager é totalmente personalizável; desta forma, é fácil criar recursos do Azure como o HDInsight. Não precisa de ter experiência no modelo do Resource Manager para seguir este tutorial. Para conhecer outros métodos de criação de clusters e compreender as propriedades utilizadas neste tutorial, consulte [Criar clusters do HDInsight](hdinsight-hadoop-provision-linux-clusters.md). O modelo do Resource Manager utilizado neste tutorial está localizado num contentor de blobs público, [https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hadoop-cluster-in-hdinsight.json](https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hadoop-cluster-in-hdinsight.json). 

1. Clique na imagem seguinte para iniciar sessão no Azure e abrir o modelo do Azure Resource Manager no Portal do Azure. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hadoop-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. No painel **Parâmetros**, introduza o seguinte:
   
    ![Começar a utilizar o modelo do Resource Manager no HDInsight Linux no portal](./media/hdinsight-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-arm-template-on-portal.png).
   
   * **ClusterName**: introduza um nome para o cluster do Hadoop que irá criar.
   * **Nome e palavra-passe de início de sessão do cluster**: o nome de início de sessão predefinido é **admin**.
   * **Nome de utilizador e palavra-passe SSH**: o nome de utilizador predefinido é **sshuser**.  Pode alterá-lo. 
     
     Os restantes parâmetros são opcionais para seguir este tutorial. Pode deixá-los tal como estão. 
     
     Cada cluster tem uma dependência de conta do Blob Storage do Azure. Normalmente, é designada de conta do Storage predefinida. O cluster do HDInsight e a respetiva conta do Storage predefinida devem estar colocalizados na mesma região do Azure. A eliminação dos clusters não elimina a conta do Storage. No modelo, o nome da conta do Storage predefinida é definido como o nome do cluster com "store" anexado. 
3. Clique em **OK** para guardar os parâmetros.
4. No painel **Implementação personalizada**, introduza **Nome do novo grupo de recursos** para criar um novo grupo de recursos.  O grupo de recursos é um contentor que agrupa o cluster, a conta do Storage dependente e outros recursos. A localização do grupo de recursos pode ser diferente da localização do cluster.
5. Clique em **Termos legais** e em **Criar**.
6. Verifique se a caixa de verificação **Afixar ao dashboard** está selecionada e, em seguida, clique em **Criar**. Verá um novo mosaico intitulado **A implementar implementação de modelo**. A criação de um cluster demora cerca de 20 minutos. 
7. Assim que o cluster for criado, a legenda do mosaico é alterada para o nome do grupo de recursos que especificou. Além disso, o portal abre automaticamente dois painéis com o cluster e as definições do cluster. 
   
   ![Introdução às definições do cluster no HDInsight com Linux](./media/hdinsight-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-cluster-settings.png).
   
   Aparecem dois recursos na lista, o cluster e a conta do Storage predefinida.

## Executar consultas do Hive
O [Apache Hive](hdinsight-use-hive.md) é o componente mais popular utilizado no HDInsight. Existem várias formas de executar tarefas do Hive no HDInsight. Neste tutorial, irá utilizar a vista Ambari Hive do portal para executar algumas tarefas do Hive. Para conhecer outros métodos de submissão de tarefas do Hive, consulte [Utilizar o Hive no HDInsight](hdinsight-use-hive.md).

1. Aceda a **https://&lt;ClusterName >. azurehdinsight.net**, onde &lt;ClusterName > é o cluster que criou na secção anterior para abrir o Ambari.
2. Introduza o nome de utilizador e a palavra-passe do Hadoop que especificou na secção anterior. O nome de utilizador predefinido é **admin**.
3. Abra a **Vista do Hive** conforme mostrado na captura de ecrã seguinte:
   
    ![Selecionar vistas do Ambari](./media/hdinsight-hadoop-linux-tutorial-get-started/selecthiveview.png).
4. Na secção **Editor de consultas** da página, cole as seguintes declarações HiveQL na folha de cálculo:
   
        SHOW TABLES;
   
   > [!NOTE]
   > O Hive requer a utilização do ponto e vírgula.       
   > 
   > 
5. Clique em **Executar**. Deve ser apresentada uma secção **Resultados do Processo de Consulta** por baixo do Editor de Consultas, que apresenta informações sobre a tarefa. 
   
    Assim que a consulta estiver concluída, a secção **Resultados do Processo de Consulta** irá apresentar os resultados da operação. Deverá ver uma tabela denomizada **hivesampletable**. Esta tabela do Hive de exemplo inclui todos os clusters do HDInsight.
   
    ![Vistas do Hive do HDInsight](./media/hdinsight-hadoop-linux-tutorial-get-started/hiveview.png).
6. Repita os passos 4 e 5 para executar a seguinte consulta:
   
        SELECT * FROM hivesampletable;
   
   > [!TIP]
   > Tenha atenção ao menu pendente **Guardar resultados** no canto superior esquerdo da secção **Resultados do Processo de Consulta**; pode utilizá-lo para transferir os resultados ou guardá-los no armazenamento do HDInsight como um ficheiro CSV.
   > 
   > 
7. Clique em **Histórico** para obter uma lista das tarefas.

Depois de concluir uma tarefa deo Hive, pode [exportar os resultados para a SQL Database do Azure ou a base de dados do SQL Server](hdinsight-use-sqoop-mac-linux.md). Do mesmo modo, pode [visualizar os resultados com o Excel](hdinsight-connect-excel-power-query.md). Para obter mais informações sobre como utilizar o Hive no HDInsight, consulte [Utilizar o Hive e o HiveQL com o Hadoop no HDInsight para analisar um ficheiro Apache log4j de exemplo](hdinsight-use-hive.md).

## Limpar o tutorial
Depois de concluir o tutorial, pode pretender eliminar o cluster. Com o HDInsight, os dados são armazenados no Storage do Azure, pelo que pode eliminar um cluster em segurança quando este não está a ser utilizado. Também lhe é cobrado o valor de um cluster do HDInsight mesmo quando não o está a utilizar. Uma vez que os custos do cluster são muito superiores aos custos do armazenamento, faz sentido do ponto de vista económico eliminar os clusters quando não estiverem a ser utilizados. 

> [!NOTE]
> Com o [Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md), pode criar clusters do HDInsight a pedido e configurar uma definição de TimeToLive para eliminar os clusters automaticamente. 
> 
> 

**Para eliminar o cluster e/ou a conta do Storage predefinida**

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. No dashboard do portal, clique no mosaico com o nome do grupo de recursos que utilizou quando criou o cluster.
3. Clique em **Eliminar** no painel de recursos para eliminar o grupo de recursos que contém o cluster e a conta do Storage predefinida. Em alternativa, clique no nome do cluster no mosaico **Recursos** e, em seguida, clique em **Eliminar** no painel do cluster. Tenha em atenção que a eliminação do grupo de recursos elimina a conta do Storage. Se pretender manter a conta do Storage, opte por eliminar apenas o cluster.

## Passos seguintes
Neste tutorial, aprendeu a criar um cluster do HDInsight baseado em Linux com um modelo do Resource Manager, bem como a executar consultas básicas do Hive.

Para saber mais sobre como analisar os dados com o HDInsight, consulte os seguintes recursos:

* Para saber mais sobre como utilizar o Hive com o HDInsight, incluindo como executar consultas do Hive a partir do Visual Studio, consulte [Utilizar o Hive com o HDInsight][hdinsight-use-hive].
* Para saber mais sobre o Pig, uma linguagem utilizada para transformar dados, consulte [Utilizar o Pig com o HDInsight][hdinsight-use-pig].
* Para saber mais sobre o MapReduce, uma forma de escrever programas que processam dados no Hadoop, consulte [Utilizar o MapReduce com o HDInsight][hdinsight-use-mapreduce].
* Para saber mais sobre como utilizar as Ferramentas do HDInsight para o Visual Studio para analisar dados no HDInsight, consulte [Começar a utilizar as ferramentas Hadoop do Visual Studio para o HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md).

Se estiver pronto para começar a trabalhar com os seus próprios dados e precisar de saber mais sobre a forma como o HDInsight armazena os dados ou como carregar dados para o HDInsight, consulte os seguintes recursos:

* Para obter informações sobre como o HDInsight utiliza o Blob Storage do Azure, consulte [Utilizar o Blob Storage do Azure com o HDInsight](hdinsight-hadoop-use-blob-storage.md).
* Para obter informações sobre como carregar dados para o HDInsight, consulte [Carregar dados para o HDInsight][hdinsight-upload-data].

Se gostaria de saber mais sobre como criar ou gerir um cluster do HDInsight, consulte os seguintes recursos:

* Para saber mais sobre a gestão do seu cluster do HDInsight baseado em Linux, consulte [Gerir clusters do HDInsight com o Ambari](hdinsight-hadoop-manage-ambari.md).
* Para saber mais sobre as opções que pode selecionar ao criar um cluster do HDInsight, consulte [Criar HDInsight no Linux utilizando opções personalizadas](hdinsight-hadoop-provision-linux-clusters.md).
* Se estiver familiarizado com o Linux e o Hadoop, mas pretender obter informações específicas sobre o Hadoop no HDInsight, consulte [Trabalhar com o HDInsight no Linux](hdinsight-hadoop-linux-information.md). Este artigo fornece informações como:
  
  * URLs para serviços alojados no cluster, como Ambari e WebHCat
  * A localização de ficheiros do Hadoop e exemplos no sistema de ficheiros local
  * A utilização do Storage do Azure (WASB) em vez do HDFS como o arquivo de dados predefinido

[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: powershell-install-configure.md
[powershell-open]: powershell-install-configure.md#Install

[img-hdi-dashboard]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.png
[img-hdi-dashboard-query-select-result-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.output.png
[img-hdi-dashboard-query-browse-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.browse.output.png
[image-hdi-clusterstatus]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.ClusterStatus.png
[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData2.png



<!--HONumber=Sep16_HO3-->


