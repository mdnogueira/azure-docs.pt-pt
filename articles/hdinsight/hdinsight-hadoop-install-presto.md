---
title: Instalar Presto nos clusters do Azure HDInsight com Linux | Microsoft Docs
description: "Saiba como instalar Presto e Airpal nos clusters do Hadoop do HDInsight baseado em Linux utilizando as ações de Script."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: af6bf1d69761520d20d0a0c5d872377793f6650b
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="install-and-use-presto-on-hdinsight-hadoop-clusters"></a>Instalar e utilizar Presto em clusters do HDInsight Hadoop

Neste tópico, irá aprender a instalar Presto em clusters do HDInsight Hadoop, utilizando a ação de Script. Também irá aprender a instalar Airpal num cluster Presto HDInsight existente.

> [!IMPORTANT]
> Os passos neste documento exigem um **cluster de Hadoop do HDInsight 3.5** que utiliza o Linux. O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, consulte [as versões do HDInsight](hdinsight-component-versioning.md).

## <a name="what-is-presto"></a>O que é Presto?
[Presto](https://prestodb.io/overview.html) é um rápida motor de consulta SQL distribuído para os macrodados. Presto é adequado para consultar interativa de petabytes de dados. Para obter mais informações sobre os componentes de Presto e como funcionam em conjunto, consulte [conceitos Presto](https://github.com/prestodb/presto/blob/master/presto-docs/src/main/sphinx/overview/concepts.rst).

> [!WARNING]
> Componentes fornecidos com o cluster do HDInsight são totalmente suportados e Support da Microsoft irá ajudá-lo para isolar e resolver problemas relacionados com estes componentes.
> 
> Componentes personalizados, tais como Presto, recebem suporte comercialmente razoável para ajudar a resolver o problema. Isto pode resultar na resolução do problema ou pedir-lhe para interagir com os canais disponíveis para as tecnologias de open source para onde se encontra profundo conhecimentos para que a tecnologia. Por exemplo, existem vários sites de Comunidade que podem ser utilizadas, como: [fórum do MSDN para o HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Também projetos do Apache tem sites de projeto no [http://apache.org](http://apache.org), por exemplo: [Hadoop](http://hadoop.apache.org/).
> 
> 


## <a name="install-presto-using-script-action"></a>Instalar Presto através da ação de script

Esta secção fornece instruções sobre como utilizar o script de exemplo, quando criar um novo cluster utilizando o portal do Azure. 

1. Começar a aprovisionar um cluster, utilizando os passos no [clusters do HDInsight baseado em Linux aprovisionar](hdinsight-hadoop-create-linux-clusters-portal.md). Certifique-se de que cria o cluster utilizando o **personalizada** fluxo de criação do cluster. Tem de se certificar de que o cluster que cria cumpre os seguintes requisitos.

    a. Tem de ser um cluster de Hadoop com o HDInsight versão 3.5.

    b. Tem de utilizar armazenamento do Azure como o arquivo de dados. Ainda, o utilizando Presto num cluster que utiliza o Azure Data Lake Store como a opção de armazenamento não é suportado. 

    ![Criação de clusters do HDInsight utilizando opções personalizadas](./media/hdinsight-hadoop-install-presto/hdinsight-install-custom.png)

2. No **definições avançadas** painel, selecione **ações de Script**e forneça as informações abaixo:
   
   * **NOME**: introduza um nome amigável para a ação de script.
   * **URI do script de Bash**: `https://raw.githubusercontent.com/hdinsight/presto-hdinsight/master/installpresto.sh`
   * **HEAD**: esta opção de verificação
   * **TRABALHO**: esta opção de verificação
   * **ZOOKEEPER**: desmarque esta caixa de verificação
   * **Os parâmetros**: deixe este campo em branco


3. Na parte inferior do **ações de Script** painel, clique em de **selecione** botão para guardar a configuração. Por fim, clique em de **selecione** na parte inferior do **definições avançadas** painel para guardar as informações de configuração.

4. Continuar o aprovisionamento de cluster, conforme descrito em [clusters do HDInsight baseado em Linux aprovisionar](hdinsight-hadoop-create-linux-clusters-portal.md).

    > [!NOTE]
    > O Azure PowerShell, a CLI do Azure, o SDK .NET do HDInsight ou modelos Azure Resource Manager também podem ser utilizados para aplicar ações de script. Também pode aplicar ações de script para clusters já em execução. Para obter mais informações, consulte [HDInsight personalizar clusters com ações de Script](hdinsight-hadoop-customize-cluster-linux.md).
    > 
    > 

## <a name="use-presto-with-hdinsight"></a>Utilizar Presto com o HDInsight

Execute os seguintes passos para utilizar Presto num HDInsight cluster depois de ter instalado, utilizando os passos descritos acima.

1. Ligar ao cluster do HDInsight com o SSH:
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    Para obter mais informações, veja [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).
     

2. Inicie o shell Presto utilizando o seguinte comando.
   
        presto --schema default

3. Executar uma consulta na tabela de exemplo, **hivesampletable**, que está disponível em todos os clusters do HDInsight por predefinição.
   
        select count (*) from hivesampletable;
   
    Por predefinição, [Hive](https://prestodb.io/docs/current/connector/hive.html) e [TPCH](https://prestodb.io/docs/current/connector/tpch.html) conectores para Presto já estão configurados. Conector do ramo de registo está configurado para utilizar a instalação do ramo de registo predefinido instalado, pelo que todas as tabelas do Hive serão automaticamente visíveis na Presto.

    Para obter uma descrição detalhada sobre como pode utilizar Presto, consulte [documentação Presto](https://prestodb.io/docs/current/index.html).

## <a name="use-airpal-with-presto"></a>Utilizar Airpal com Presto

[Airpal](https://github.com/airbnb/airpal#airpal) é uma interface de open source baseada na web consulta para Presto. Para obter mais informações sobre Airpal, consulte [Airpal documentação](https://github.com/airbnb/airpal#airpal).

Nesta secção, vamos ver os passos para **instalar Airpal o edgenode** de um cluster do HDInsight Hadoop, que já tenha Presto instalado. Isto garante que a interface de consulta Airpal web está disponível através da Internet.

1. Utilização do SSH, ligar a headnode do cluster do HDInsight Presto tenha instalado:
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    Para obter mais informações, veja [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Assim que estiver ligado, execute o seguinte comando.

        sudo slider registry  --name presto1 --getexp presto 
   
    Deve ver um resultado como o seguinte:

        {
            "coordinator_address" : [ {
                "value" : "10.0.0.12:9090",
                "level" : "application",
                "updatedTime" : "Mon Apr 03 20:13:41 UTC 2017"
        } ]

3. O resultado, tenha em atenção o valor para o **valor** propriedade. Irá precisar ao instalar Airpal no edgenode cluster. Da saída acima, o valor que irá precisar é **10.0.0.12:9090**.

4. Utilize o modelo  **[aqui](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2Fpresto-hdinsight%2Fmaster%2Fairpal-deploy.json)**  para criar um edgenode de cluster do HDInsight e forneça os valores, conforme mostrado na captura de ecrã seguinte.

    ![Instalação de HDInsight Airpal no Presto cluster](./media/hdinsight-hadoop-install-presto/hdinsight-install-airpal.png)

5. Clique em **Comprar**.

6. Depois das alterações são aplicadas à configuração de cluster, pode aceder a interface de web Airpal utilizando os seguintes passos.

    a. No painel do cluster, clique em **aplicações**.

    ![Lançamento de HDInsight Airpal no Presto cluster](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal.png)

    b. Do **aplicações instaladas** painel, clique em **Portal** contra airpal.

    ![Lançamento de HDInsight Airpal no Presto cluster](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal-1.png)

    c. Quando lhe for pedido, introduza as credenciais de administrador que especificou ao criar o cluster do HDInsight Hadoop.

## <a name="customize-a-presto-installation-on-hdinsight-cluster"></a>Personalizar uma instalação Presto num cluster do HDInsight

Depois de instalar Presto num cluster do HDInsight Hadoop, pode personalizar a instalação ao efetuar alterações, como atualizar as definições da memória, altere os conectores, etc. Para fazê-lo, execute os passos seguintes.

1. Utilização do SSH, ligar a headnode do cluster do HDInsight Presto tenha instalado:
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    Para obter mais informações, veja [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Efetue as alterações de configuração no ficheiro `/var/lib/presto/presto-hdinsight-master/appConfig-default.json`. Para obter mais informações sobre a configuração Presto, consulte [configuração Presto para clusters baseados em YARN](https://prestodb.io/presto-yarn/installation-yarn-configuration-options.html).

3. Parar e eliminar a instância em execução atual do Presto.

        sudo slider stop presto1 --force
        sudo slider destroy presto1 --force

4. Inicie uma nova instância do Presto com a personalização.

       sudo slider create presto1 --template /var/lib/presto/presto-hdinsight-master/appConfig-default.json --resources /var/lib/presto/presto-hdinsight-master/resources-default.json

5. Aguarde que a nova instância a ser preparado e tenha em atenção endereço presto coordenador.


       sudo slider registry --name presto1 --getexp presto

## <a name="generate-benchmark-data-for-hdinsight-clusters-that-run-presto"></a>Gerar dados benchmark para clusters do HDInsight que são executados Presto

TPC DS é a norma da indústria para medir o desempenho de vários sistemas de suporte de decisão, incluindo sistemas de macrodados. Pode utilizar Presto nos clusters do HDInsight para gerar dados e avaliar como compara com os seus próprios dados benchmark do HDInsight. Para obter mais informações, consulte [aqui](https://github.com/hdinsight/tpcds-datagen-as-hive-query/blob/master/README.md).



## <a name="see-also"></a>Consultar também
* [Instalar e utilizar Hue nos clusters do HDInsight](hdinsight-hadoop-hue-linux.md). Hue é uma IU da web do que torna mais fácil de criar, executar e guardar as tarefas do Pig e do Hive, bem como a procura o armazenamento de predefinido para o HDInsight cluster.

* [Instalar Giraph nos clusters do HDInsight](hdinsight-hadoop-giraph-install-linux.md). Utilize personalização de cluster para instalar Giraph em clusters do HDInsight Hadoop. Giraph permite-lhe efetuar o processamento através da utilização do Hadoop e pode ser utilizado com o Azure HDInsight.

* [Instalar Solr nos clusters do HDInsight](hdinsight-hadoop-solr-install-linux.md). Utilize personalização de cluster para instalar Solr em clusters do HDInsight Hadoop. Solr permite-lhe efetuar operações de pesquisa poderosas em dados armazenados.

[hdinsight-install-r]: hdinsight-hadoop-r-scripts-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
