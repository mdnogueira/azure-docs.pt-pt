---
title: Microsoft Toolkit cognitivos com o Azure HDInsight Spark para aprender profunda | Microsoft Docs
description: Saiba como um modelo treinado do learning profunda Toolkit cognitivos Microsoft pode ser aplicado a um conjunto de dados utilizando a API de Python do Spark num cluster Azure HDInsight Spark.
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: e4bf0b176817daf3c128f26f407cde2ce1adba4c
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="use-microsoft-cognitive-toolkit-deep-learning-model-with-azure-hdinsight-spark-cluster"></a>Utilizar cognitivos Toolkit de modelo com o cluster Azure HDInsight Spark profunda

Neste artigo, efetue os seguintes passos.

1. Execute um script personalizado para instalar o Toolkit de cognitivos num cluster do Azure HDInsight Spark.

2. Carregar um bloco de notas do Jupyter para o cluster do Spark para ver como aplicar um modelo treinado do learning profunda Microsoft cognitivos Toolkit para ficheiros de uma conta de armazenamento de Blobs do Azure utilizando o [Spark Python API (PySpark)](https://spark.apache.org/docs/0.9.0/python-programming-guide.html)

## <a name="prerequisites"></a>Pré-requisitos

* **Uma subscrição do Azure**. Antes de começar este tutorial, tem de ter uma subscrição do Azure. Veja o artigo [Crie hoje a sua conta do Azure gratuita](https://azure.microsoft.com/free).

* **Cluster do HDInsight Spark do Azure**. Para este artigo, crie um cluster do Spark 2.0. Para obter instruções, consulte [cluster Criar Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="how-does-this-solution-flow"></a>Como fluxo esta solução?

Esta solução é dividida entre este artigo e um bloco de notas do Jupyter que carregar como parte deste tutorial. Neste artigo, conclua os seguintes passos:

* Execute uma ação de script num cluster do HDInsight Spark para instalar o Toolkit de cognitivos e pacotes de Python.
* Carregue o bloco de notas do Jupyter que executa a solução para o cluster do HDInsight Spark.

O bloco de notas do Jupyter são abrangidos os seguintes passos restantes.

- Carregar imagens de exemplo para um conjunto de dados do Spark Resiliant distribuída ou RDD
   - Carregar módulos e definir predefinições
   - Transferir o conjunto de dados localmente no cluster do Spark
   - Converter o conjunto de dados para um RDD
- As imagens através de um modelo treinado do Toolkit cognitivos de pontuação
   - Transferir o modelo treinado do Toolkit cognitivos para o cluster do Spark
   - Definir funções para ser utilizada por nós de trabalho
   - As imagens de pontuação em nós de trabalho
   - Avaliar a precisão do modelo


## <a name="install-microsoft-cognitive-toolkit"></a>Instalar o Toolkit de cognitivos

Pode instalar o Toolkit de cognitivos num cluster do Spark através da ação de script. Ação de script utiliza scripts personalizados para instalar os componentes no cluster que não estão disponíveis por predefinição. Pode utilizar o script personalizado do Portal do Azure, utilizando o SDK .NET do HDInsight, ou com o Azure PowerShell. Também pode utilizar o script para instalar o toolkit como parte da criação do cluster ou depois do cluster se encontra em execução. 

Neste artigo, vamos utilizar o portal para instalar o toolkit, após o cluster foi criado. Para outras formas de executar o script personalizado, consulte [clusters do HDInsight de personalizar através da ação de Script](../hdinsight-hadoop-customize-cluster-linux.md).

### <a name="using-the-azure-portal"></a>Utilizar o Portal do Azure

Para obter instruções sobre como utilizar o Portal do Azure para executar a ação de script, consulte [clusters do HDInsight de personalizar através da ação de Script](../hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation). Certifique-se de que fornece as seguintes entradas para instalar o Toolkit de cognitivos.

* Forneça um valor para o nome da ação de script.

* Para **Bash script URI**, introduza `https://raw.githubusercontent.com/Azure-Samples/hdinsight-pyspark-cntk-integration/master/cntk-install.sh`.

* Certifique-se de que execute o script apenas em nós head e de trabalho e desmarque todas as outras caixas de verificação.

* Clique em **Criar**.

## <a name="upload-the-jupyter-notebook-to-azure-hdinsight-spark-cluster"></a>Carregar o bloco de notas do Jupyter para o cluster Azure HDInsight Spark

Para utilizar o Toolkit de cognitivos com o cluster Azure HDInsight Spark, tem de carregar o bloco de notas do Jupyter **CNTK_model_scoring_on_Spark_walkthrough.ipynb** para o cluster Azure HDInsight Spark. Este bloco de notas está disponível no GitHub em [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration).

1. Clone o repositório GitHub [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration). Para obter instruções clonar, consulte [um repositório de clonagem](https://help.github.com/articles/cloning-a-repository/).

2. A partir do Portal do Azure, abra o painel do cluster Spark que já aprovisionada, clique em **Cluster Dashboard**e, em seguida, clique em **bloco de notas do Jupyter**.

    Também pode iniciar o bloco de notas do Jupyter acedendo ao URL `https://<clustername>.azurehdinsight.net/jupyter/`. Substitua \<clustername > com o nome do cluster do HDInsight.

3. O bloco de notas do Jupyter, clique em **carregar** na parte superior direita canto e, em seguida, navegue para a localização onde clonou o repositório do GitHub.

    ![Carregar o bloco de notas do Jupyter para o cluster Azure HDInsight Spark](./media/apache-spark-microsoft-cognitive-toolkit/hdinsight-microsoft-cognitive-toolkit-load-jupyter-notebook.png "bloco de notas do Jupyter carregar para o cluster Azure HDInsight Spark")

4. Clique em **carregar** novamente.

5. Após o bloco de notas, clique no nome do bloco de notas e, em seguida, siga as instruções no bloco de notas propriamente dito sobre como carregar o conjunto de dados e executar o tutorial.

## <a name="see-also"></a>Consultar também
* [Descrição geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Cenários
* [Spark com BI: Efetuar uma análise de dados interativa com o Spark no HDInsight com ferramentas do BI](apache-spark-use-bi-tools.md)
* [Spark com Machine Learning: Utilizar o Spark no HDInsight para analisar a temperatura do edifício com dados de AVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Spark com Machine Learning: Utilizar o Spark no HDInsight para prever resultados de inspeções alimentares](apache-spark-machine-learning-mllib-ipython.md)
* [Transmissão em Fluxo do Spark: Utilizar o Spark no HDInsight para criar aplicações de transmissão em fluxo em tempo real](apache-spark-eventhub-streaming.md)
* [Análise de registos de sites com o Spark no HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Análise de dados telemétricos do Application Insight com o Spark no HDInsight](apache-spark-analyze-application-insight-logs.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicações
* [Criar uma aplicação autónoma com o Scala](apache-spark-create-standalone-application.md)
* [Executar tarefas remotamente num cluster do Spark com o Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Utilizar o Plug-in das Ferramentas do HDInsight para o IntelliJ IDEA para criar e submeter aplicações do Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Utilizar o Plug-in das Ferramentas do HDInsight para o IntelliJ IDEA para depurar aplicações do Spark remotamente](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utilizar blocos de notas do Zeppelin com um cluster do Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o bloco de notas do Jupyter no cluster do Spark para o HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utilizar pacotes externos com blocos de notas do Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerir recursos
* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]:../../storage/common/storage-create-storage-account.md
