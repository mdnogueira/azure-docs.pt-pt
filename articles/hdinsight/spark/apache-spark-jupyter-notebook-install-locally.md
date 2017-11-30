---
title: Instalar o Jupyter localmente e ligar a um cluster do Azure HDInsight Spark | Microsoft Docs
description: Saiba como instalar o bloco de notas do Jupyter localmente no seu computador e ligue-o a um cluster do Apache Spark no Azure HDInsight.
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 48593bdf-4122-4f2e-a8ec-fdc009e47c16
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: 1508faf7a05461de65b7a4c2f68e2ef9bbd7e19d
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-on-hdinsight"></a>Instalar o bloco de notas do Jupyter no seu computador e ligue ao Apache Spark no HDInsight

Este artigo irá aprender a instalar o bloco de notas do Jupyter com o PySpark personalizado (para o Python) e kernels Spark (para Scala) com a magia do Spark e ligar o bloco de notas para um cluster do HDInsight. Pode existir uma série de razões para instalar o Jupyter no computador local e só pode haver alguns desafios bem. Para mais informações, consulte a secção [por que motivo deve instalar Jupyter no meu computador](#why-should-i-install-jupyter-on-my-computer) no final deste artigo.

Existem três chaves passos envolvidos na instalação Jupyter e a magia do Spark no seu computador.

* Instalar o bloco de notas do Jupyter
* Instalar os kernels PySpark e Spark com a magia do Spark
* Configurar a magia do Spark para aceder a um cluster do Spark no HDInsight

Para obter mais informações sobre os kernels personalizados e a magia de Spark disponível para blocos de notas do Jupyter com clusters do HDInsight, consulte [clusters Kernels disponíveis para blocos de notas do Jupyter com o Linux do Apache Spark no HDInsight](apache-spark-jupyter-notebook-kernels.md).

## <a name="prerequisites"></a>Pré-requisitos
Não são os pré-requisitos listados aqui para instalar o Jupyter. Estes são para ligar o bloco de notas do Jupyter para um cluster do HDInsight assim que o bloco de notas é instalado.

* Uma subscrição do Azure. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Um cluster do Apache Spark no HDInsight. Para obter instruções, consulte [clusters do Apache Spark criar no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="install-jupyter-notebook-on-your-computer"></a>Instalar o bloco de notas do Jupyter no seu computador

Tem de instalar o Python antes de poder instalar blocos de notas do Jupyter. Python e Jupyter estão disponíveis como parte do [distribuição Anaconda](https://www.continuum.io/downloads). Quando instala o Anaconda, instale uma distribuição do Python. Depois de instalado o Anaconda, adicione a instalação do Jupyter executando os comandos apropriados.

1. Transferir o [instalador Anaconda](https://www.continuum.io/downloads) para a sua plataforma e executar o programa de configuração. Ao executar o Assistente de configuração, certifique-se de que seleciona a opção para adicionar Anaconda à sua variável de caminho.
2. Execute o seguinte comando para instalar o Jupyter.

        conda install jupyter

    Para obter mais informações sobre como instalar o Jupyter, consulte [Jupyter instalar utilizando Anaconda](http://jupyter.readthedocs.io/en/latest/install.html).

## <a name="install-the-kernels-and-spark-magic"></a>Instalar o kernels e a magia do Spark

Para obter instruções sobre como instalar a magia do Spark, kernels PySpark e Spark, siga as instruções de instalação no [sparkmagic documentação](https://github.com/jupyter-incubator/sparkmagic#installation) no GitHub. O primeiro passo na documentação de mágica do Spark pede-lhe instalar magic de Spark. Substitua os comandos seguintes, dependendo da versão do cluster do HDInsight que irá ligar a esse primeiro passo na ligação. Depois disso, siga os passos restantes na documentação de mágica do Spark. Se pretender instalar os kernels diferentes, tem de efetuar o passo 3 na secção de instruções de instalação mágica do Spark.

* Para clusters v3.4, instale sparkmagic 0.2.3 executando`pip install sparkmagic==0.2.3`

* Para clusters 3.5 e v3.6, instalar sparkmagic 0.11.2 executando`pip install sparkmagic==0.11.2`

## <a name="configure-spark-magic-to-connect-to-hdinsight-spark-cluster"></a>Configurar a magia do Spark para ligar ao cluster do HDInsight Spark

Nesta secção, configurar a magia de Spark instalado anteriormente para ligar a um cluster do Apache Spark tem já tenha criado no Azure HDInsight.

1. As informações de configuração do Jupyter normalmente são armazenadas no diretório raiz de utilizadores. Para localizar o diretório raiz em qualquer plataforma de SO, escreva os comandos seguintes.

    Inicie a shell do Python. Na janela de comandos, escreva o seguinte:

        python

    Na shell do Python, introduza o seguinte comando para determinar o diretório de raiz.

        import os
        print(os.path.expanduser('~'))

2. Navegue para o diretório de raiz e crie uma pasta denominada **.sparkmagic** se já existir.
3. Dentro da pasta, crie um ficheiro chamado **config** e adicione o fragmento JSON seguinte dentro do mesmo.

        {
          "kernel_python_credentials" : {
            "username": "{USERNAME}",
            "base64_password": "{BASE64ENCODEDPASSWORD}",
            "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
          },
          "kernel_scala_credentials" : {
            "username": "{USERNAME}",
            "base64_password": "{BASE64ENCODEDPASSWORD}",
            "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
          }
        }

4. Substitute **{USERNAME}**, **{CLUSTERDNSNAME}**, e **{BASE64ENCODEDPASSWORD}** com os valores adequados. Pode utilizar um número de utilitários na linguagem de programação favorita ou online para gerar uma palavra-passe de codificado em base64 para a sua palavra-passe real.

5. Configurar as definições de Heartbeat à direita numa `config.json`. Deve adicionar estas definições no mesmo nível, como o `kernel_python_credentials` e `kernel_scala_credentials` fragmentos sua adicionado anteriormente. Para um exemplo sobre como e onde pode adicionar as definições de heartbeat, consulte este [exemplo config](https://github.com/jupyter-incubator/sparkmagic/blob/master/sparkmagic/example_config.json).

    * Para `sparkmagic 0.2.3` (clusters v3.4), incluem:

            "should_heartbeat": true,
            "heartbeat_refresh_seconds": 5,
            "heartbeat_retry_seconds": 1

    * Para `sparkmagic 0.11.2` (clusters 3.5 e v3.6) incluem:

            "heartbeat_refresh_seconds": 5,
            "livy_server_heartbeat_timeout_seconds": 60,
            "heartbeat_retry_seconds": 1

    >[!TIP]
    >Para garantir sessões não podem fugir os heartbeats são enviados. Quando um computador entra em suspensão ou é encerrado, o heartbeat não é enviado, causando a sessão que está a ser limpa. Para clusters v3.4, se pretender desativar este comportamento, pode definir a configuração de Livy `livy.server.interactive.heartbeat.timeout` para `0` da IU do Ambari. Para clusters 3.5, se definir a configuração 3.5 acima, a sessão não será eliminada.

6. Inicie o Jupyter. Utilize o seguinte comando na linha de comandos.

        jupyter notebook

7. Certifique-se de que pode ligar para o cluster utilizando o bloco de notas do Jupyter e que pode utilizar a magia de Spark disponível com os kernels. Execute os seguintes passos.

    a. Crie um novo bloco de notas. No canto direito, clique em **novo**. Deverá ver o kernel predefinido **Python2** e os dois kernels novo que instalar, **PySpark** e **Spark**. Clique em **PySpark**.

    ![Kernels no bloco de notas do Jupyter](./media/apache-spark-jupyter-notebook-install-locally/jupyter-kernels.png "Kernels no bloco de notas do Jupyter")

    b. Execute o seguinte fragmento de código.

        %%sql
        SELECT * FROM hivesampletable LIMIT 5

    Se com êxito pode obter o resultado, a ligação ao cluster do HDInsight é testada.

    >[!TIP]
    >Se pretender atualizar a configuração do bloco de notas para ligar a um cluster diferente, atualize o Config com o novo conjunto de valores, conforme mostrado no passo 3 acima.

## <a name="why-should-i-install-jupyter-on-my-computer"></a>Por que motivo deve instalar Jupyter no meu computador?
Pode ser um número dos motivos por que razão poderá pretender instalar o Jupyter no seu computador e, em seguida, ligue-o para um cluster do Spark no HDInsight.

* Apesar dos blocos de notas do Jupyter já estão disponíveis no cluster do Spark no Azure HDInsight, instalar o Jupyter no seu computador fornece que a opção para criar os seus blocos de notas localmente, testar a aplicação contra um cluster em execução e, em seguida, carregue os blocos de notas para o cluster. Para carregar os blocos de notas para o cluster, pode carregá-los utilizando o bloco de notas do Jupyter que está em execução ou o cluster, ou guardá-las para a pasta de /HdiNotebooks na conta de armazenamento associada com o cluster. Para obter mais informações sobre como os blocos de notas são armazenados no cluster, consulte [são blocos de notas do Jupyter armazenar](apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)?
* Com os blocos de notas disponíveis localmente, pode ligar a clusters do Spark diferentes com base na sua requisito da aplicação.
* Pode utilizar o GitHub para implementar um sistema de controlo de origem e o controlo de versão para os blocos de notas. Também pode ter um ambiente de colaboração, onde os vários utilizadores podem trabalhar com o mesmo bloco de notas.
* Pode trabalhar com blocos de notas localmente sem mesmo ter um cluster de cópia de segurança. Só precisa de um cluster para testar os blocos de notas contra, gerir manualmente os blocos de notas ou num ambiente de desenvolvimento.
* Poderá ser mais fácil de configurar o seu ambiente de desenvolvimento local que está a configurar a instalação do Jupyter no cluster.  Pode tirar partido de todas as software que tiver instalado localmente sem configurar um ou mais clusters remotos.

> [!WARNING]
> Com o Jupyter instalado no seu computador local, vários utilizadores podem executar o mesmo bloco de notas no mesmo cluster Spark ao mesmo tempo. Numa situação, são criadas vários Livy sessões. Se depare com um problema e pretende depurar que, será uma tarefa complexa para controlar qual sessão Livy pertence a que utilizador.
>
>

## <a name="seealso"></a>Ver também
* [Descrição geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Cenários
* [Spark com BI: Efetuar uma análise de dados interativa com o Spark no HDInsight com ferramentas do BI](apache-spark-use-bi-tools.md)
* [Spark com Machine Learning: Utilizar o Spark no HDInsight para analisar a temperatura do edifício com dados de AVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Spark com Machine Learning: Utilizar o Spark no HDInsight para prever resultados de inspeções alimentares](apache-spark-machine-learning-mllib-ipython.md)
* [Transmissão em Fluxo do Spark: Utilizar o Spark no HDInsight para criar aplicações de transmissão em fluxo em tempo real](apache-spark-eventhub-streaming.md)
* [Análise de registos de sites com o Spark no HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicações
* [Criar uma aplicação autónoma com o Scala](apache-spark-create-standalone-application.md)
* [Executar tarefas remotamente num cluster do Spark com o Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Utilizar o Plug-in das Ferramentas do HDInsight para o IntelliJ IDEA para criar e submeter aplicações do Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Utilizar o Plug-in das Ferramentas do HDInsight para o IntelliJ IDEA para depurar aplicações do Spark remotamente](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utilizar blocos de notas do Zeppelin com um cluster do Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o bloco de notas do Jupyter no cluster do Spark para o HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utilizar pacotes externos com blocos de notas do Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)

### <a name="manage-resources"></a>Gerir recursos
* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)
