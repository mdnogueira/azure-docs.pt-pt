---
title: "Ferramentas do HDInsight do Azure - utilização Visual Studio Code para o ramo de registo, LLAP ou pySpark | Microsoft Docs"
description: Saiba como utilizar as ferramentas do Azure HDInsight para Visual Studio Code para criar, submeter consultas e scripts.
Keywords: VScode, as ferramentas do HDInsight do Azure, do Hive, Python, PySpark, Spark, HDInsight, Hadoop, LLAP, interativo do Hive, consulta interativa
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
ms.openlocfilehash: 36ce117076ed5c15ddff850485d8f8912ec53caf
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="use-azure-hdinsight-tool-for-visual-studio-code"></a>Utilizar a ferramenta do Azure HDInsight para Visual Studio Code

Saiba como utilizar as ferramentas do Azure HDInsight para Visual Studio Code (VSCode) para criar, submeter tarefas de lote do ramo de registo, consultas do Hive interativas e scripts do pySpark. As ferramentas do Azure HDInsight pode ser instaladas nas plataformas suportadas pelo VSCode, incluindo Windows, Linux e MacOS. Pode encontrar os pré-requisitos para plataformas diferentes.


## <a name="prerequisites"></a>Pré-requisitos

Os itens seguintes são necessários para concluir este artigo:

- Um cluster do HDInsight.  Para criar um cluster, consulte [introdução ao HDInsight]( hdinsight-hadoop-linux-tutorial-get-started.md).
- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx).
- [Mono](http://www.mono-project.com/docs/getting-started/install/). Mono só é necessário para Linux e MacOS.

## <a name="install-the-hdinsight-tools"></a>Instalar as ferramentas do HDInsight
   
Depois de ter instalado os pré-requisitos, pode instalar as ferramentas do HDInsight do Azure para VSCode. 

**Para instalar o Azure HDInsight ferramentas**

1. Abra **Visual Studio Code**.
2. Clique em **extensões** no painel esquerdo. Introduza **HDInsight** na caixa de pesquisa.
3. Clique em **instalar** junto a **as ferramentas do Azure HDInsight**. Após alguns segundos, o **instalar** botão será alterado para **recarregar**.
4. Clique em **recarregar** para ativar o **as ferramentas do Azure HDInsight** extensão.
5. Clique em **recarregar janela** para confirmar. Pode ver **as ferramentas do Azure HDInsight** no painel de extensões.

   ![HDInsight para instalação de Python de código do Visual Studio](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

## <a name="open-hdinsight-workspace"></a>Abra a área de trabalho do HDInsight

É necessário criar uma área de trabalho VSCode antes de poder ligar ao Azure.

**Para abrir uma área de trabalho**

1. Do **ficheiro** menu, clique em **Abrir pasta**, especifique uma pasta existente ou crie uma nova pasta como a pasta de trabalho. A pasta é apresentado no painel esquerdo.
2. No painel esquerdo, clique o **novo ficheiro** ícone junto a pasta de trabalho.

   ![Novo ficheiro](./media/hdinsight-for-vscode/new-file.png)
3. Nome do novo ficheiro com o .hql (consultas do Hive) ou a extensão de ficheiro. PY (script de Spark). Tenha em atenção um **XXXX_hdi_settings.json** ficheiro de configuração é adicionado automaticamente à pasta de trabalho.
4. Abra **XXXX_hdi_settings.json** de **EXPLORADOR**, ou clique com o botão direito no editor de scripts para selecionar **definir configuração**. Pode configurar a entrada de início de sessão, predefinido do cluster e parâmetros de submissão da tarefa, conforme mostrado no exemplo no ficheiro. Também pode deixar os restantes parâmetros vazio.

## <a name="connect-to-azure"></a>Ligar ao Azure

Para poder submeter scripts para clusters do HDInsight a partir de VSCode, precisa de ligar à sua conta do Azure.

**Para ligar ao Azure**

1. Se não tiver uma, crie uma nova pasta de trabalho e um novo ficheiro de script.
2. O editor de scripts com o botão direito e, em seguida, selecione **HDInsight: início de sessão** no menu de contexto. Também pode premir **CTRL + SHIFT + P** e introduzindo **HDInsight: início de sessão**.

    ![O início de sessão de ferramentas do HDInsight para Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)
3. Siga as instruções de início de sessão no **saída** painel para iniciar sessão.

    **Azure:** ![ferramentas do HDInsight para as informações de início de sessão do Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-Azurelogin-info.png)

    Assim que estiver ligado, o nome da sua conta do Azure é apresentado na barra de estado no lado esquerdo-na parte inferior da janela VSCode. 

    > [!NOTE]
    > Abra o browser com o modo privado ou incognito devido a um problema conhecido authentication do Azure. Se a sua conta do Azure tem dois fatores ativados, recomenda-se para utilizar a autenticação de telefone em vez de Pin.
  

4. Clique com botão direito a edição de script para abrir o menu de contexto. No menu de contexto, pode realizar as seguintes tarefas:

    - terminar sessão
    - Lista de clusters
    - Conjunto predefinido do cluster
    - Submeter consultas do Hive interativas
    - Submeter o script de batch de ramo de registo
    - Submeter consultas interativas do PySpark
    - Submeter o script de comandos do PySpark
    - Configuração de conjunto

## <a name="list-hdinsight-clusters"></a>Lista de clusters do HDInsight

Para testar a ligação, pode listar os clusters do HDInsight:

**Lista de clusters do HDInsight na sua subscrição do Azure**
1. Abra a área de trabalho e ligar ao Azure. Consulte [área de trabalho do HDInsight abra](#open-hdinsight-workspace) e [ligar ao Azure](#connect-to-azure).
2. O editor de scripts com o botão direito e, em seguida, selecione **HDInsight: lista Cluster** no menu de contexto. 
3. Os clusters do Hive e Spark aparecem no **saída** painel.

    ![configuração de cluster predefinida do conjunto](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-default-cluster"></a>Conjunto predefinido do cluster
1. Abra a área de trabalho e ligar ao Azure. Consulte [área de trabalho do HDInsight abra](#open-hdinsight-workspace) e [ligar ao Azure](#connect-to-azure).
2. O editor de scripts com o botão direito e, em seguida, clique em **HDInsight: Definir Cluster predefinido**. 
3. Selecione um cluster como predefinido do cluster para o ficheiro de script atual. As ferramentas atualizar automaticamente o ficheiro de configuração, **XXXX_hdi_settings.json**. 

   ![configuração de cluster predefinida do conjunto](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="set-azure-environment"></a>Definir o ambiente do Azure 
1. Abra a paleta de comando premindo **CTRL + SHIFT + P**.
2. Introduza **HDInsight: definir o ambiente do Azure**.
3. Selecione uma forma de Azure e AzureChina como a entrada de início de sessão predefinido.
4. Entretanto, nosso ferramenta guardado já tiver selecionado a entrada de início de sessão predefinida para **XXXX_hdi_settings.json**. Pode também diretamente atualizá-lo neste ficheiro de configuração. 

   ![configuração de entrada de início de sessão de predefinida do conjunto](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="submit-interactive-hive-queries"></a>Submeter consultas do Hive interativas

Ferramentas do HDInsight para VSCode permitem-lhe submeter consultas do Hive interativas para os clusters do HDInsight de consulta interativa.

1. Se não tiver uma, crie uma nova pasta de trabalho e um novo ficheiro de script de ramo de registo.
2. Ligar à sua conta do Azure e, em seguida, configurar o cluster de predefinido se não tiver feito.
3. Copie e cole o seguinte código no seu ficheiro de ramo de registo e, em seguida, guarde-o.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
3. O editor de scripts com o botão direito e, em seguida, clique em **HDInsight: ramo de registo interativo** submeter a consulta. As ferramentas também permitem-lhe submeter um bloco de código em vez do ficheiro de script de toda utilizando o menu de contexto. Logo depois, o resultado da consulta é apresentado num novo separador:

   ![resultado do ramo de registo interativo](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - **RESULTADOS** painel: pode guardar o resultado todo como CSV, o JSON, o EXCEL para o caminho local ou apenas selecionar várias linhas.
    - **MENSAGENS** painel: clicar em **linha** número, salta para a primeira linha do script em execução.

A comparação com [em execução uma tarefa de lote do Hive](#submit-hive-batch-scripts), a consulta interativa demora muito menos tempo.

## <a name="submit-hive-batch-scripts"></a>Submeter scripts de batch de ramo de registo

1. Se não tiver uma, crie uma nova pasta de trabalho e um novo ficheiro de script de ramo de registo.
2. Ligar à sua conta do Azure e, em seguida, configurar o cluster de predefinido se não tiver feito.
3. Copie e cole o seguinte código no seu ficheiro de ramo de registo e, em seguida, guarde-o.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
3. O editor de scripts com o botão direito e, em seguida, clique em **HDInsight: Hive Batch** para submeter uma tarefa do Hive. 
4. Selecione um cluster para submeter para onde pretende.  

    Depois de submeter uma tarefa do Hive, as informações de êxito de submissão e o jobid é apresentado na **saída** painel. E é aberta **WEB BROWSER** que inicia a tarefa em tempo real e o estado apresentado na.

   ![submeter o resultado da tarefa do Hive](./media/hdinsight-for-vscode/submit-Hivejob-result.png)

A comparação com [submeter consultas do Hive interativas](#submit-interactive-hive-queries), a tarefa de lote demora muito mais tempo.

## <a name="submit-interactive-pyspark-queries"></a>Submeter consultas interativas do PySpark
Ferramentas do HDInsight para VSCode também permitem-lhe submeter consultas interativas do PySpark para os clusters do Spark.
1. Crie uma nova pasta de trabalho e um novo ficheiro de script com a extensão. PY se não tiver uma.
2. Ligar à sua conta do Azure, se ainda não o feito.
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
4. Realce estes scripts e o editor de scripts com o botão direito, em seguida, clique em **HDInsight: PySpark interativa**.
5. Clique em seguintes **instalar** botão se não tiver instalado **Python** extensão na VSCode.
    ![HDInsight para instalação de Python de código do Visual Studio](./media/hdinsight-for-vscode/hdinsight-vscode-install-python.png)

6. Configure o ambiente de python no seu sistema se não instalar. 
   - Para o windows, transfira e instale [Python](https://www.python.org/downloads/). Certifique-se `Python` e `pip` no seu sistema de caminho.
   - A instrução para MacOS e Linux, consulte [definir segurança PySpark interativa ambiente para o Visual Studio Code](set-up-pyspark-interactive-environment.md).
7. Selecione um cluster para submeter a consulta do PySpark. Logo depois, o resultado da consulta é apresentado no separador de novo à direita:

   ![submeter o resultado da tarefa de python](./media/hdinsight-for-vscode/pyspark-interactive-result.png) 
8. A nossa ferramenta também suporta a consulta a **SQL cláusula**.

   ![submeter o resultado da tarefa de python](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png) apresenta o estado de submissão no lado esquerdo da barra de estado na parte inferior, quando executar consultas. Não é possível submeter outras consultas ao estado é **Kernel do PySpark (ocupado)**, caso contrário, a execução é hang.
9. A nossa clusters podem manter uma sessão. Por exemplo, **um = 100**, já manter esta sessão no cluster, agora, execute apenas **imprimir um** ao cluster.
 

## <a name="submit-pyspark-batch-job"></a>Submeter a tarefa de lote do PySpark

1. Crie uma nova pasta de trabalho e um novo ficheiro de script com a extensão. PY se não tiver uma.
2. Ligar à sua conta do Azure, se ainda não o feito.
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
4. O editor de scripts com o botão direito e, em seguida, clique em **HDInsight: PySpark Batch**. 
5. Selecione um cluster para submeter a tarefa do PySpark. 

   ![submeter o resultado da tarefa de python](./media/hdinsight-for-vscode/submit-pythonjob-result.png) 

Depois de submeter uma tarefa de python, registos de submissão é apresentado na **saída** janela no VSCode. O **Spark IU URL** e **URL de IU do Yarn** são também apresentados. Pode abrir o URL num browser para controlar o estado da tarefa.


## <a name="additional-features"></a>Funcionalidades adicionais

O HDInsight para VSCode suporta as seguintes funcionalidades:

- **O IntelliSense conclusão automática**. Sugestões são popped cópias de segurança em torno da palavra-chave, o método, variáveis, etc. Ícones diferentes representam os diferentes tipos de objetos:

    ![Ferramentas do HDInsight para tipos de objeto IntelliSense de código do Visual Studio](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **Marcador de erro IntelliSense**. O serviço de linguagem underlines os erros de edição para o script de ramo de registo.     
- **Destaques de sintaxe**. O serviço de linguagem utiliza cores diferentes para diferenciar as variáveis, palavras-chave, tipo de dados, funções, etc. 

    ![Ferramentas do HDInsight para Visual Studio Code sintaxe destaques](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="next-steps"></a>Passos seguintes

### <a name="demo"></a>Demonstração
* HDInsight para VScode: [vídeo](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Configurar o ambiente interativa PySpark para Visual Studio Code](set-up-pyspark-interactive-environment.md)
* [Utilize o Toolkit do Azure para o IntelliJ para criar e submeter aplicações do Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Utilize o Toolkit do Azure para o IntelliJ para depurar aplicações do Spark remotamente através de SSH](hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Utilize o Toolkit do Azure para o IntelliJ para depurar aplicações do Spark remotamente através da VPN](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utilizar as ferramentas do HDInsight na Azure Toolkit para Eclipse para criar Spark aplicações](hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Utilize as ferramentas do HDInsight para o IntelliJ com Hortonworks Sandbox](hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Utilizar blocos de notas do Zeppelin com um cluster do Spark no HDInsight](hdinsight-apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o bloco de notas do Jupyter no cluster do Spark para o HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Utilizar pacotes externos com blocos de notas do Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](hdinsight-apache-spark-jupyter-notebook-install-locally.md)
* [Visualizar dados do Hive com o Microsoft Power BI no Azure HDInsight](./hdinsight-connect-hive-power-bi.md).
* [Utilize Zeppelin para executar consultas do Hive no Azure HDInsight ](./hdinsight-connect-hive-zeppelin.md).

### <a name="scenarios"></a>Cenários
* [Spark com BI: Efetuar uma análise de dados interativa com o Spark no HDInsight com ferramentas do BI](hdinsight-apache-spark-use-bi-tools.md)
* [Spark com Machine Learning: Utilizar o Spark no HDInsight para analisar a temperatura do edifício com dados de AVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark com Machine Learning: Utilizar o Spark no HDInsight para prever resultados de inspeções alimentares](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Transmissão em fluxo do Spark: Utilizar o Spark no HDInsight para a criação em tempo real que aplicações de transmissão em fluxo](hdinsight-apache-spark-eventhub-streaming.md)
* [Análise de registos de sites com o Spark no HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Criar e executar aplicações
* [Criar uma aplicação autónoma com o Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Executar tarefas remotamente num cluster do Spark com o Livy](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="managing-resources"></a>Gestão de recursos
* [Gerir recursos para o cluster do Apache Spark no Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Controlar e depurar tarefas em execução num cluster do Apache Spark do HDInsight](hdinsight-apache-spark-job-debugging.md)



