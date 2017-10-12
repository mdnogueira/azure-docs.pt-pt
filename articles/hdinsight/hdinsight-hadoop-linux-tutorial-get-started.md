---
title: "Introdução ao Hadoop e ao Hive no Azure HDInsight | Microsoft Docs"
description: Saiba como criar clusters do HDInsight e consultar dados com o Hive.
keywords: "guia de introdução do hadoop, hadoop linux, início rápido do hadoop, introdução do hive, início rápido do hive"
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 6a12ed4c-9d49-4990-abf5-0a79fdfca459
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/23/2017
ms.author: jgao
ms.openlocfilehash: fd9e6dcea6524f55c1bd06da35f02be5670bf95f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="hadoop-tutorial-get-started-using-hadoop-in-hdinsight"></a>Tutorial do Hadoop: Introdução ao Hadoop no HDInsight

Saiba como criar clusters do [Hadoop](http://hadoop.apache.org/) no HDInsight e como executar tarefas do Hive no HDInsight. O [Apache Hive](https://hive.apache.org/) é o componente mais popular do ecossistema Hadoop. Atualmente, o HDInsight inclui [sete tipos diferentes de cluster](hdinsight-hadoop-introduction.md#overview). Cada tipo de cluster suporta um conjunto diferente de componentes. Todos os tipos de cluster suportam o Hive. Para obter uma lista dos componentes suportados no HDInsight, consulte [Quais são as novidades nas versões de cluster do Hadoop fornecidas pelo HDInsight?](hdinsight-component-versioning.md)  

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, tem de ter os seguintes elementos:

* **Subscrição do Azure**: para criar uma conta de avaliação gratuita de um mês, aceda a [azure.microsoft.com/free](https://azure.microsoft.com/free).

## <a name="create-cluster"></a>Criar cluster

A maioria das tarefas do Hadoop são tarefas de lote. Cria um cluster, executa algumas tarefas e, em seguida, elimina o cluster. Nesta secção, vai criar um cluster do Hadoop no HDInsight com um [modelo do Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md). Não precisa de ter experiência no modelo do Resource Manager para seguir este tutorial. Para conhecer outros métodos de criação de clusters e compreender as propriedades utilizadas neste tutorial, consulte [Criar clusters do HDInsight](hdinsight-hadoop-provision-linux-clusters.md). Utilize o seletor no topo da página para escolher as suas opções de criação do cluster.

O modelo do Resource Manager utilizado neste tutorial está localizado no [GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-password/). 

1. Clique na imagem seguinte para iniciar sessão no Azure e abrir o modelo do Azure Resource Manager no Portal do Azure. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-ssh-password%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-hadoop-linux-tutorial-get-started/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. Introduza ou selecione os seguintes valores:
   
    ![Introdução ao modelo do Resource Manager no Portal no HDInsight com Linux](./media/hdinsight-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-arm-template-on-portal.png "Implementar o cluster do Hadoop no HDInsight com o portal do Azure e um modelo do gestor do grupo de recursos").
   
    * **Subscrição**: selecione a sua subscrição do Azure.
    * **Grupo de recursos**: crie um grupo de recursos ou selecione um existente.  Um grupo de recursos é um contentor de componentes do Azure.  Neste caso, o grupo de recursos contém o cluster do HDInsight e a conta de armazenamento do Azure dependente. 
    * **Localização**: selecione a localização do Azure onde pretende criar o cluster.  Selecione uma localização mais próxima de si para obter um melhor desempenho. 
    * **Tipo de cluster**: selecione **hadoop** para este tutorial.
    * **Nome do Cluster**: introduza um nome para o cluster do Hadoop.
    * **Nome e palavra-passe de início de sessão do cluster**: o nome de início de sessão predefinido é **admin**.
    * **Nome de utilizador e palavra-passe SSH**: o nome de utilizador predefinido é **sshuser**.  Pode alterá-lo. 
     
    Algumas propriedades foram codificadas no modelo.  Pode configurar estes valores a partir do modelo.

    * **Localização**: a localização do cluster e a conta de armazenamento dependente partilham a mesma localização que o grupo de recursos.
    * **Versão do cluster**: 3.5
    * **Tipo de SO**: Linux
    * **Número de nós de trabalho**: 2

     Cada cluster tem uma dependência de [conta do Armazenamento do Azure](hdinsight-hadoop-use-blob-storage.md) ou de [conta do Azure Data Lake](hdinsight-hadoop-use-data-lake-store.md). É designada de conta de armazenamento predefinida. O cluster do HDInsight e a respetiva conta do Storage predefinida devem estar colocalizados na mesma região do Azure. A eliminação dos clusters não elimina a conta de armazenamento. 
     
     Para obter mais explicações sobre estas propriedades, veja [Criar clusters Hadoop no HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

3. Selecione **Concordo com os termos e condições indicados acima** e **Afixar ao dashboard** e, em seguida, clique em **Comprar**. Verá um novo mosaico intitulado **A implementar implementação de Modelo** no dashboard do portal. A criação de um cluster demora cerca de 20 minutos. Assim que o cluster for criado, a legenda do mosaico é alterada para o nome do grupo de recursos que especificou. E o portal abre automaticamente o grupo de recursos num novo painel. Pode ver o cluster e o armazenamento predefinidos listados.
   
    ![Introdução ao grupo de recursos no HDInsight com Linux](./media/hdinsight-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-resource-group.png "Grupo de recursos do cluster no Azure HDInsight").

4. Clique no nome do cluster para abrir o cluster num novo painel.

   ![Introdução às definições do cluster no HDInsight com Linux](./media/hdinsight-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-cluster-settings.png "Propriedades do cluster no HDInsight")


## <a name="run-hive-queries"></a>Executar consultas do Hive
O [Apache Hive](hdinsight-use-hive.md) é o componente mais popular utilizado no HDInsight. Existem várias formas de executar tarefas do Hive no HDInsight. Neste tutorial, vai utilizar a vista Ambari Hive do portal. Para conhecer outros métodos de submissão de tarefas do Hive, consulte [Utilizar o Hive no HDInsight](hdinsight-use-hive.md).

1. Na captura de ecrã anterior, clique em **Dashboard do Cluster** e, em seguida, clique em **Dashboard do Cluster do HDInsight**.  Também pode procurar em **https://&lt;ClusterName >. azurehdinsight.net**, onde &lt;ClusterName > é o cluster que criou na secção anterior para abrir o Ambari.
2. Introduza o nome de utilizador e a palavra-passe do Hadoop que especificou na secção anterior. O nome de utilizador predefinido é **admin**.
3. Abra a **Vista do Hive** conforme mostrado na captura de ecrã seguinte:
   
    ![Selecionar vistas Ambari](./media/hdinsight-hadoop-linux-tutorial-get-started/selecthiveview.png "Menu do Ambari Hive View").
4. Na secção **Editor de consultas** da página, cole as seguintes declarações HiveQL na folha de cálculo:
   
        SHOW TABLES;
   
   > [!NOTE]
   > O Hive requer a utilização do ponto e vírgula.       
   > 
   > 
5. Clique em **Executar**. Deve ser apresentada uma secção **Resultados do Processo de Consulta** por baixo do Editor de Consultas, que apresenta informações sobre a tarefa. 
   
    Assim que a consulta estiver concluída, a secção **Resultados do Processo de Consulta** apresenta os resultados da operação. Deverá ver uma tabela denomizada **hivesampletable**. Esta tabela do Hive de exemplo inclui todos os clusters do HDInsight.
   
    ![Vistas do Hive do HDInsight](./media/hdinsight-hadoop-linux-tutorial-get-started/hiveview.png "Editor de Consulta do Hive View do HDInsight").
6. Repita os passos 4 e 5 para executar a seguinte consulta:
   
        SELECT * FROM hivesampletable;
   
   > [!TIP]
   > Tenha atenção ao menu pendente **Guardar resultados** no canto superior esquerdo da secção **Resultados do Processo de Consulta**; pode utilizá-lo para transferir os resultados ou guardá-los no armazenamento do HDInsight como um ficheiro CSV.
   > 
   > 
7. Clique em **Histórico** para obter uma lista das tarefas.

Depois de concluir uma tarefa deo Hive, pode [exportar os resultados para a SQL Database do Azure ou a base de dados do SQL Server](hdinsight-use-sqoop-mac-linux.md). Do mesmo modo, pode [visualizar os resultados com o Excel](hdinsight-connect-excel-power-query.md). Para obter mais informações sobre como utilizar o Hive no HDInsight, consulte [Utilizar o Hive e o HiveQL com o Hadoop no HDInsight para analisar um ficheiro Apache log4j de exemplo](hdinsight-use-hive.md).

## <a name="clean-up-the-tutorial"></a>Limpar o tutorial
Depois de concluir o tutorial, pode pretender eliminar o cluster. Com o HDInsight, os dados são armazenados no Storage do Azure, pelo que pode eliminar um cluster em segurança quando este não está a ser utilizado. Também lhe é cobrado o valor de um cluster do HDInsight mesmo quando não o está a utilizar. Uma vez que os custos do cluster são muito superiores aos custos do armazenamento, faz sentido do ponto de vista económico eliminar os clusters quando não estiverem a ser utilizados. 

> [!NOTE]
> Com o [Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md), pode criar clusters do HDInsight a pedido e configurar uma definição de TimeToLive para eliminar os clusters automaticamente. 
> 
> 

**Para eliminar o cluster e/ou a conta de armazenamento predefinida**

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. No dashboard do portal, clique no mosaico com o nome do grupo de recursos que utilizou quando criou o cluster.
3. Clique em **Eliminar** no painel de recursos para eliminar o grupo de recursos que contém o cluster e a conta de Armazenamento predefinida. Em alternativa, clique no nome do cluster no mosaico **Recursos** e, em seguida, clique em **Eliminar** no painel do cluster. Tenha em atenção que a eliminação do grupo de recursos elimina a conta de armazenamento. Se pretender manter a conta do Storage, opte por eliminar apenas o cluster.

## <a name="troubleshoot"></a>Resolução de problemas

Caso se depare com problemas com a criação de clusters do HDInsight, veja [aceder aos requisitos de controlo](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, aprendeu a criar um cluster do HDInsight baseado em Linux com um modelo do Resource Manager, bem como a executar consultas básicas do Hive.

Saiba mais sobre como analisar os dados com o HDInsight, veja os seguintes artigos:

* Para saber mais sobre como utilizar o Hive com o HDInsight, incluindo como executar consultas do Hive a partir do Visual Studio, consulte [Utilizar o Hive com o HDInsight][hdinsight-use-hive].
* Para saber mais sobre o Pig, uma linguagem utilizada para transformar dados, consulte [Utilizar o Pig com o HDInsight][hdinsight-use-pig].
* Para saber mais sobre o MapReduce, uma forma de escrever programas que processam dados no Hadoop, consulte [Utilizar o MapReduce com o HDInsight][hdinsight-use-mapreduce].
* Para saber mais sobre como utilizar as Ferramentas do HDInsight para o Visual Studio para analisar dados no HDInsight, consulte [Começar a utilizar as ferramentas Hadoop do Visual Studio para o HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md).

Se estiver pronto para começar a trabalhar com os seus próprios dados e precisar de saber mais sobre a forma como o HDInsight armazena os dados ou como carregar dados para o HDInsight, consulte os seguintes recursos:

* Para obter informações sobre como o HDInsight utiliza o Armazenamento do Azure, consulte [Utilizar o Armazenamento do Azure com o HDInsight](hdinsight-hadoop-use-blob-storage.md).
* Para obter informações sobre como carregar dados para o HDInsight, consulte [Carregar dados para o HDInsight][hdinsight-upload-data].

Se gostaria de saber mais sobre como criar ou gerir um cluster do HDInsight, consulte os seguintes recursos:

* Para saber mais sobre a gestão do seu cluster do HDInsight baseado em Linux, consulte [Gerir clusters do HDInsight com o Ambari](hdinsight-hadoop-manage-ambari.md).
* Para saber mais sobre as opções que pode selecionar ao criar um cluster do HDInsight, consulte [Criar HDInsight no Linux utilizando opções personalizadas](hdinsight-hadoop-provision-linux-clusters.md).
* Se estiver familiarizado com o Linux e o Hadoop, mas pretender obter informações específicas sobre o Hadoop no HDInsight, consulte [Trabalhar com o HDInsight no Linux](hdinsight-hadoop-linux-information.md). Este artigo disponibiliza informações como:
  
  * URLs para serviços alojados no cluster, como Ambari e WebHCat
  * A localização de ficheiros do Hadoop e exemplos no sistema de ficheiros local
  * A utilização do Storage do Azure (WASB) em vez do HDFS como o arquivo de dados predefinido

[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md


