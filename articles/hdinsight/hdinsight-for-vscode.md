---
title: "Ferramentas do HDInsight do Azure - utilização Visual Studio Code para o ramo de registo, LLAP ou pySpark | Microsoft Docs"
description: Saiba como utilizar as ferramentas do Azure HDInsight para Visual Studio Code para criar e submeter consultas e scripts.
Keywords: VS Code,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interactive Hive,Interactive Query
services: HDInsight
documentationcenter: 
author: jejiang
manager: 
editor: jgao
tags: azure-portal
ms.assetid: 
ms.service: HDInsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/27/2017
ms.author: jejiang
ms.openlocfilehash: d6ca3bcb91261a863444bc331c78adf44844be56
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2017
---
# <a name="use-azure-hdinsight-tools-for-visual-studio-code"></a>Utilizar as ferramentas do Azure HDInsight para Visual Studio Code

Saiba como utilizar as ferramentas do Azure HDInsight para Visual Studio Code (VS Code) para criar e submeter tarefas de lote do ramo de registo, consultas do Hive interativas e scripts do pySpark. As ferramentas do Azure HDInsight pode ser instaladas nas plataformas suportadas pelo VS Code. Estes incluem-se ao Windows, Linux e macOS. Pode encontrar os pré-requisitos para plataformas diferentes.


## <a name="prerequisites"></a>Pré-requisitos

Os itens seguintes são necessários para concluir os passos neste artigo:

- Um cluster do HDInsight.  Para criar um cluster, consulte [introdução ao HDInsight]( hdinsight-hadoop-linux-tutorial-get-started.md).
- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx).
- [Mono](http://www.mono-project.com/docs/getting-started/install/). Mono só é necessário para Linux e macOS.

## <a name="install-the-hdinsight-tools"></a>Instalar as ferramentas do HDInsight
   
Depois de ter instalado os pré-requisitos, pode instalar as ferramentas do Azure HDInsight para o VS Code. 

**Para instalar o Azure HDInsight ferramentas**

1. Abra o Visual Studio Code.

2. No painel esquerdo, selecione **extensões**. Na caixa de pesquisa, introduza **HDInsight**.

3. Junto a **as ferramentas do Azure HDInsight**, selecione **instalar**. Após alguns segundos, o **instalar** botão muda para **recarregar**.

4. Selecione **recarregar** para ativar o **as ferramentas do Azure HDInsight** extensão.

5. Selecione **recarregar janela** para confirmar. Pode ver **as ferramentas do Azure HDInsight** no **extensões** painel.

   ![HDInsight para instalação de Python de código do Visual Studio](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

## <a name="open-hdinsight-workspace"></a>Abra a área de trabalho do HDInsight

Crie uma área de trabalho no VS Code antes de poder ligar ao Azure.

**Para abrir uma área de trabalho**

1. No **ficheiro** menu, selecione **Abrir pasta**. Em seguida, designar uma pasta existente como a pasta de trabalho ou crie um novo. A pasta é apresentado no painel esquerdo.

2. No painel esquerdo, selecione o **novo ficheiro** ícone junto a pasta de trabalho.

   ![Novo ficheiro](./media/hdinsight-for-vscode/new-file.png)

3. Nome do novo ficheiro com o .hql (consultas do Hive) ou a extensão de ficheiro. PY (script de Spark). Tenha em atenção que um **XXXX_hdi_settings.json** ficheiro de configuração é adicionado automaticamente à pasta de trabalho.

4. Abra **XXXX_hdi_settings.json** de **EXPLORADOR**, ou clique com o botão direito do editor de scripts para selecionar **definir configuração**. Pode configurar a entrada de início de sessão, predefinido do cluster e parâmetros de submissão da tarefa, conforme mostrado no exemplo no ficheiro. Também pode deixar os restantes parâmetros vazio.

## <a name="connect-to-azure"></a>Ligar ao Azure

Para poder submeter scripts para clusters do HDInsight a partir do código de VS, precisa de ligar à sua conta do Azure.

**Para ligar ao Azure**

1. Se ainda não tivê-los, crie uma nova pasta de trabalho e um novo ficheiro de script.

2. O editor de scripts com o botão direito e, em seguida, no menu de contexto, selecione **HDInsight: início de sessão**. Também pode introduzir **Ctrl + Shift + P**e, em seguida, introduza **HDInsight: início de sessão**.

    ![O início de sessão de ferramentas do HDInsight para Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

3. Para iniciar sessão, siga as instruções de início de sessão no **saída** painel.

    **Azure:** ![ferramentas do HDInsight para as informações de início de sessão do Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-Azurelogin-info.png)

    Depois de se estiver ligado, o nome da sua conta do Azure é apresentado na barra de estado à esquerda na parte inferior da janela VS Code. 

    > [!NOTE]
    > Devido a um problema conhecido authentication do Azure, terá de abrir um browser no modo privado ou incognito. Se a sua conta do Azure tem dois fatores ativadas, recomendamos a utilizar a autenticação de telefone em vez de autenticação do PIN.
  

4. Clique no editor de scripts para abrir o menu de contexto. No menu de contexto, pode realizar as seguintes tarefas:

    - Terminar sessão
    - Lista de clusters
    - Conjunto predefinido clusters
    - Submeter consultas do Hive interativas
    - Submeter scripts de batch de ramo de registo
    - Submeter consultas interativas do PySpark
    - Submeter PySpark scripts de batch
    - Configurações de conjunto

## <a name="list-hdinsight-clusters"></a>Lista de clusters do HDInsight

Para testar a ligação, pode listar os clusters do HDInsight:

**Lista de clusters do HDInsight na sua subscrição do Azure**
1. Abra a área de trabalho e, em seguida, ligar ao Azure. Para obter mais informações, consulte [área de trabalho do HDInsight abra](#open-hdinsight-workspace) e [ligar ao Azure](#connect-to-azure).

2. O editor de scripts com o botão direito e, em seguida, selecione **HDInsight: lista Cluster** no menu de contexto. 

3. Os clusters do Hive e Spark aparecem no **saída** painel.

    ![Definir uma configuração de cluster predefinido](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-a-default-cluster"></a>Definir um predefinido do cluster
1. Abra a área de trabalho e ligar ao Azure. Consulte [área de trabalho do HDInsight abra](#open-hdinsight-workspace) e [ligar ao Azure](#connect-to-azure).

2. O editor de scripts com o botão direito e, em seguida, selecione **HDInsight: Definir Cluster predefinido**. 

3. Selecione um cluster do cluster predefinido para o ficheiro de script atual. As ferramentas atualizar automaticamente o ficheiro de configuração **XXXX_hdi_settings.json**. 

   ![configuração de cluster predefinida do conjunto](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="set-the-azure-environment"></a>Definir o ambiente do Azure 
1. Abra a paleta de comando selecionando **CTRL + SHIFT + P**.

2. Introduza **HDInsight: definir o ambiente do Azure**.

3. Selecione uma forma de Azure e AzureChina como a entrada de início de sessão predefinido.

4. Entretanto, a ferramenta já tem guardados a entrada de início de sessão predefinida na **XXXX_hdi_settings.json**. Pode também diretamente atualizá-lo neste ficheiro de configuração. 

   ![configuração de entrada de início de sessão de predefinida do conjunto](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="submit-interactive-hive-queries"></a>Submeter consultas do Hive interativas

Com ferramentas do HDInsight para o VS Code, pode submeter consultas do Hive interativas para clusters do HDInsight consulta interativo.

1. Crie uma nova pasta de trabalho e um novo ficheiro de script de ramo de registo, se ainda não tivê-los.

2. Ligar à sua conta do Azure e, em seguida, configure o predefinido do cluster, se ainda não o tiver feito.

3. Copie e cole o seguinte código no seu ficheiro de ramo de registo e, em seguida, guardá-lo.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
3. O editor de scripts com o botão direito e, em seguida, selecione **HDInsight: ramo de registo interativo** submeter a consulta. As ferramentas também permitem-lhe submeter um bloco de código em vez do ficheiro de script de toda utilizando o menu de contexto. Logo, os resultados da consulta são apresentados num novo separador.

   ![resultado do ramo de registo interativo](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - **RESULTADOS** painel: pode guardar o resultado todo como ficheiro CSV, JSON ou Excel para o caminho local ou apenas selecionar várias linhas.

    - **MENSAGENS** painel: Quando seleciona **linha** número, salta para a primeira linha do script em execução.

A execução da consulta interativa demora muito menos tempo que [em execução uma tarefa de lote do Hive](#submit-hive-batch-scripts).

## <a name="submit-hive-batch-scripts"></a>Submeter scripts de batch de ramo de registo

1. Crie uma nova pasta de trabalho e um novo ficheiro de script de ramo de registo, se ainda não tivê-los.

2. Ligar à sua conta do Azure e, em seguida, configure o predefinido do cluster, se ainda não o tiver feito.

3. Copie e cole o seguinte código no seu ficheiro de ramo de registo e, em seguida, guardá-lo.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
3. O editor de scripts com o botão direito e, em seguida, selecione **HDInsight: Hive Batch** para submeter uma tarefa do Hive. 

4. Selecione o cluster para o qual pretende submeter.  

    Depois de submeter uma tarefa do Hive, as informações de êxito de submissão e o jobid é apresentado no **saída** painel. A tarefa do Hive é também aberta **WEB BROWSER**, que mostra o estado e os registos da tarefa em tempo real.

   ![submeter o resultado da tarefa do Hive](./media/hdinsight-for-vscode/submit-Hivejob-result.png)

[Submeter consultas do Hive interativas](#submit-interactive-hive-queries) demora muito menos tempo que submete uma tarefa de lote.

## <a name="submit-interactive-pyspark-queries"></a>Submeter consultas interativas do PySpark
Ferramentas do HDInsight para o VS Code também permite-lhe submeter consultas interativas do PySpark para os clusters do Spark.
1. Crie uma nova pasta de trabalho e um novo ficheiro de script com a extensão. PY se ainda não tivê-los.

2. Ligar à sua conta do Azure se ainda não o ainda feito.

3. Copie e cole o seguinte código no ficheiro de script:
   ```python
   from operator import add
   lines = spark.read.text("/HdiSamples/HdiSamples/FoodInspectionData/README").rdd.map(lambda r: r[0])
   counters = lines.flatMap(lambda x: x.split(' ')) \
                .map(lambda x: (x, 1)) \
                .reduceByKey(add)

   coll = counters.collect()
   sortedCollection = sorted(coll, key = lambda r: r[1], reverse = True)

   for i in range(0, 5):
        print(sortedCollection[i])
   ```
4. Realce estes scripts. Em seguida, o editor de scripts com o botão direito e selecione **HDInsight: PySpark interativa**.

5. Se ainda não instalou o **Python** extensão no VS Code, selecione o **instalar** botão conforme mostrado na ilustração seguinte:

    ![HDInsight para instalação de Python de código do Visual Studio](./media/hdinsight-for-vscode/hdinsight-vscode-install-python.png)

6. Instale o ambiente do Python no seu sistema, se ainda não o fez. 
   - Para o Windows, transfira e instale [Python](https://www.python.org/downloads/). Certifique-se `Python` e `pip` estão no seu sistema de caminho.

   - Para obter instruções para macOS e Linux, consulte [configurar o ambiente interativa PySpark para Visual Studio Code](set-up-pyspark-interactive-environment.md).

7. Selecione um cluster para o qual pretende submeter a consulta do PySpark. Em breve depois, o resultado da consulta é apresentado no separador direita novo:

   ![Submeter o resultado da tarefa de Python](./media/hdinsight-for-vscode/pyspark-interactive-result.png) 
8. A ferramenta também suporta o **SQL cláusula** consulta.

   ![Submeter o resultado da tarefa de Python](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png) é apresentado o estado de submissão à esquerda da barra quando estiver a executar consultas de estado na parte inferior. Não submeter outras consultas quando o estado é **Kernel do PySpark (ocupado)**. 

>[!NOTE]
>Os clusters podem manter informações de sessão. A variável definida, a função e o valores correspondentes são mantidos na sessão, pelo que pode ser referenciadas em várias chamadas de serviço para o mesmo cluster. 
 

## <a name="submit-pyspark-batch-job"></a>Submeter a tarefa de lote do PySpark

1. Crie uma nova pasta de trabalho e um novo ficheiro de script com a extensão. PY se ainda não tivê-los.

2. Ligar à sua conta do Azure, se ainda não o tiver feito.

3. Copie e cole o seguinte código no ficheiro de script:

    ```python
    from __future__ import print_function
    import sys
    from operator import add
    from pyspark.sql import SparkSession
    if __name__ == "__main__":
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
    
        lines = spark.read.text('/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv').rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' '))\
                    .map(lambda x: (x, 1))\
                    .reduceByKey(add)
        output = counts.collect()
        for (word, count) in output:
            print("%s: %i" % (word, count))
        spark.stop()
    ```
4. O editor de scripts com o botão direito e, em seguida, selecione **HDInsight: PySpark Batch**. 

5. Selecione um cluster para o qual pretende submeter a tarefa do PySpark. 

   ![Submeter o resultado da tarefa de Python](./media/hdinsight-for-vscode/submit-pythonjob-result.png) 

Depois de submeter uma tarefa de Python, registos de submissão apresentado o **saída** janela no VS Code. O **Spark IU URL** e **URL de IU do Yarn** são também apresentados. Pode abrir o URL num browser para controlar o estado da tarefa.


## <a name="additional-features"></a>Funcionalidades adicionais

HDInsight para o VS Code suporta as seguintes funcionalidades:

- **O IntelliSense conclusão automática**. Sugestões aparecer para palavra-chave, os métodos, variáveis e assim sucessivamente. Ícones diferentes representam os diferentes tipos de objetos.

    ![Ferramentas do HDInsight para tipos de objeto IntelliSense de código do Visual Studio](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **Marcador de erro IntelliSense**. O serviço de linguagem underlines os erros de edição para o script de ramo de registo.     
- **Destaques de sintaxe**. O serviço de linguagem utiliza cores diferentes para diferenciar variáveis, palavras-chave, tipo de dados, as funções e assim sucessivamente. 

    ![Ferramentas do HDInsight para Visual Studio Code sintaxe destaques](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="next-steps"></a>Passos seguintes

### <a name="demo"></a>Demonstração
* HDInsight para o código de VS: [vídeo](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Ferramentas e extensões

* [Utilize o Toolkit do Azure para o IntelliJ para depurar aplicações do Spark remotamente através da VPN](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utilize o Toolkit do Azure para o IntelliJ para depurar aplicações do Spark remotamente através de SSH](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Utilize as ferramentas do HDInsight para o IntelliJ com Hortonworks Sandbox](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Utilizar as ferramentas do HDInsight na Azure Toolkit para Eclipse para criar Spark aplicações](spark/apache-spark-eclipse-tool-plugin.md)
* [Utilizar blocos de notas do Zeppelin com um cluster do Spark no HDInsight](spark/apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o bloco de notas do Jupyter no cluster do Spark para o HDInsight](spark/apache-spark-jupyter-notebook-kernels.md)
* [Utilizar pacotes externos com blocos de notas do Jupyter](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Visualizar dados do Hive com o Microsoft Power BI no Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Configurar o ambiente interativa PySpark para Visual Studio Code](set-up-pyspark-interactive-environment.md)
* [Utilize Zeppelin para executar consultas do Hive no Azure HDInsight](./hdinsight-connect-hive-zeppelin.md)

### <a name="scenarios"></a>Cenários
* [Spark com BI: Efetuar uma análise de dados interativa com o Spark no HDInsight com ferramentas do BI](spark/apache-spark-use-bi-tools.md)
* [Spark com Machine Learning: Utilizar o Spark no HDInsight para analisar a temperatura do edifício com dados de AVAC](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Spark com Machine Learning: Utilizar o Spark no HDInsight para prever resultados de inspeções alimentares](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Transmissão em fluxo do Spark: Utilizar o Spark no HDInsight para a criação em tempo real que aplicações de transmissão em fluxo](spark/apache-spark-eventhub-streaming.md)
* [Análise de registos de sites com o Spark no HDInsight](spark/apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-running-applications"></a>Criar e executar aplicações
* [Criar uma aplicação autónoma com o Scala](spark/apache-spark-create-standalone-application.md)
* [Executar tarefas remotamente num cluster do Spark com o Livy](spark/apache-spark-livy-rest-interface.md)

### <a name="manage-resources"></a>Gerir recursos
* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](spark/apache-spark-resource-manager.md)
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](spark/apache-spark-job-debugging.md)



