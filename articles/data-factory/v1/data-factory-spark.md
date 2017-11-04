---
title: Invocar programas de Spark de Azure Data Factory | Microsoft Docs
description: Saiba como invocar programas de Spark de um Azure data factory com a atividade de MapReduce.
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: fd98931c-cab5-4d66-97cb-4c947861255c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2017
ms.author: spelluru
robots: noindex
ms.openlocfilehash: 0eff48ec65a01a2fc3fa9f7652dd8e1a0fc8dd2a
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="invoke-spark-programs-from-azure-data-factory-pipelines"></a>Invocar programas de Spark do Azure Data Factory pipelines

> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Atividade do ramo de registo](data-factory-hive-activity.md)
> * [Atividade do PIg](data-factory-pig-activity.md)
> * [Atividade de MapReduce](data-factory-map-reduce.md)
> * [Atividade de transmissão em fluxo do Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Atividade do Spark](data-factory-spark.md)
> * [Atividade de Execução em Lote do Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Atividade de Recursos de Atualização de Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Atividade de Procedimento Armazenado](data-factory-stored-proc-activity.md)
> * [Atividade de U-SQL do Data Lake Analytics](data-factory-usql-activity.md)
> * [Atividade personalizada do .NET](data-factory-use-custom-activities.md)

> [!NOTE]
> Este artigo aplica-se versão 1 do Azure Data Factory, que é geralmente disponível (GA). Se estiver a utilizar a versão 2 do serviço do Data Factory, o que está em pré-visualização, consulte [transformar dados utilizando a atividade de spark na versão 2 do Data Factory](../transform-data-using-spark.md).

## <a name="introduction"></a>Introdução
Atividade de Spark é uma do [atividades de transformação de dados](data-factory-data-transformation-activities.md) suportado pelo Azure Data Factory. Esta atividade executa o programa de Spark especificado no cluster do Apache Spark no Azure HDInsight.    

> [!IMPORTANT]
> - Atividade de Spark não suporta clusters do HDInsight Spark que utilizam um Azure Data Lake Store, como armazenamento principal.
> - Atividade de Spark suporta apenas existente (os seus próprios) clusters do HDInsight Spark. Não suporta um serviço de ligado de HDInsight a pedido.

## <a name="walkthrough-create-a-pipeline-with-spark-activity"></a>Instruções: criar um pipeline com a atividade do Spark
Eis os passos típicos para criar um pipeline do Data Factory com uma atividade de Spark.  

1. Criar uma fábrica de dados.
2. Crie um serviço ligado do Storage do Azure para ligar o seu armazenamento do Azure que está associado ao seu cluster do HDInsight Spark à fábrica de dados.     
2. Crie um serviço ligado do Azure HDInsight para ligar o seu cluster do Apache Spark no HDInsight do Azure à fábrica de dados.
3. Crie um conjunto de dados refere-se ao serviço ligado do Storage do Azure. Atualmente, tem de especificar um conjunto de dados de saída para uma atividade, mesmo se não houver nenhuma saída que está a ser produzida.  
4. Crie um pipeline com atividade de Spark refere-se ao serviço ligado de HDInsight criado no #2. A atividade está configurada com o conjunto de dados que criou no passo anterior como um conjunto de dados de saída. O conjunto de dados de saída é o que pauta a agenda (hora a hora, diária, etc..). Por conseguinte, tem de especificar o conjunto de dados de saída, apesar da atividade não produzir realmente uma saída.

### <a name="prerequisites"></a>Pré-requisitos
1. Criar um **conta do Storage do Azure para fins gerais** ao seguir as instruções a explicação passo a passo: [criar uma conta de armazenamento](../../storage/common/storage-create-storage-account.md#create-a-storage-account).  
2. Criar um **cluster do Apache Spark no Azure HDInsight** ao seguir as instruções do tutorial: [cluster Criar Apache Spark no Azure HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Associe a conta de armazenamento do Azure que criou no passo #1 com este cluster.  
3. Transferir e rever o ficheiro de script de python **test.py** localizado em: [https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py](https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py).  
3.  Carregar **test.py** para o **pyFiles** pasta o **adfspark** contentor do Blob storage do Azure. Crie o contentor e a pasta, caso não existam.

### <a name="create-data-factory"></a>Criar fábrica de dados
Comecemos este passo com a criação da fábrica de dados.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Clique em **NOVO**, no menu da esquerda, clique em **Dados + Análise** e, em seguida, em **Data Factory**.
3. No **nova fábrica de dados** painel, introduza **SparkDF** para o nome.

   > [!IMPORTANT]
   > O nome do Azure Data Factory deve ser **globalmente exclusivo**. Se vir o erro: **nome da fábrica de dados "SparkDF" não está disponível**. Alterar o nome da fábrica de dados (por exemplo, yournameSparkDFdate e tente criar novamente. Veja o tópico [Data Factory – Naming Rules (Data Factory – Regras de Nomenclatura)](data-factory-naming-rules.md) para obter as regras de nomenclatura dos artefactos do Data Factory.   
4. Selecione a **Subscrição do Azure** onde pretende que seja criada a fábrica de dados.
5. Selecione um existente **grupo de recursos** ou crie um grupo de recursos do Azure.
6. Selecione **afixar ao dashboard** opção.  
6. Clique em **Criar** no painel **Nova fábrica de dados**.

   > [!IMPORTANT]
   > Para criar instâncias do Data Factory, tem de ser um membro da função [Contribuinte do Data Factory](../../active-directory/role-based-access-built-in-roles.md#data-factory-contributor) ao nível do grupo de recursos/subscrição.
7. Consulte a fábrica de dados a ser criada no **dashboard** do portal do Azure da seguinte forma:   
8. Depois de ter criado a fábrica de dados com êxito, vê a página da fábrica de dados e o respetivo conteúdo. Se não vir a página da fábrica de dados, clique no mosaico da fábrica de dados no dashboard.

    ![Painel Data Factory](./media/data-factory-spark/data-factory-blade.png)

### <a name="create-linked-services"></a>Criar serviços ligados
Neste passo, vai criar dois serviços ligados, uma para ligar o seu cluster do Spark para a fábrica de dados e outra para ligar o seu armazenamento do Azure à fábrica de dados.  

#### <a name="create-azure-storage-linked-service"></a>Criar o serviço ligado do Storage do Azure
Neste passo, vai ligar a sua conta de Armazenamento do Azure à fábrica de dados. Um conjunto de dados a que criar num passo posterior nestas instruções refere-se a este serviço ligado. O serviço ligado do HDInsight que definem no próximo passo refere-se demasiado para este serviço ligado.  

1. Clique em **autor e implementar** no **Data Factory** painel para a fábrica de dados. Deverá ver o Editor do Data Factory.
2. Clique em **Novo arquivo de dados** e escolha **Armazenamento do Azure**.

   ![Menu Novo armazenamento de dados – Armazenamento do Azure](./media/data-factory-spark/new-data-store-azure-storage-menu.png)
3. Deverá ver o **JSON script** para criar um Storage do Azure de serviço no editor de ligado.

   ![Serviço ligado do Storage do Azure](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)
4. Substitua **nome da conta** e **chave da conta** com a chave de acesso e o nome da sua conta de armazenamento do Azure. Para saber como obter a sua chave de acesso ao armazenamento, veja as informações sobre como ver, copiar e regenerar as chaves de acesso de armazenamento em [Gerir a conta do Armazenamento](../../storage/common/storage-create-storage-account.md#manage-your-storage-account).
5. Para implementar o serviço ligado, clique em **implementar** na barra de comandos. Depois de implementar o serviço ligado com êxito, a janela **Rascunho-1** deve desaparecer e verá **AzureStorageLinkedService** na vista de árvore à esquerda.

#### <a name="create-hdinsight-linked-service"></a>Criar o serviço ligado do HDInsight
Neste passo, vai criar o serviço ligado do Azure HDInsight para ligar o seu cluster do HDInsight Spark à fábrica de dados. O cluster do HDInsight é utilizado para executar o programa de Spark especificado na atividade Spark do pipeline neste exemplo.  

1. Clique em **... Mais** na barra de ferramentas, clique em **nova computação**e, em seguida, clique em **cluster do HDInsight**.

    ![Criar o serviço ligado do HDInsight](media/data-factory-spark/new-hdinsight-linked-service.png)
2. Copie e cole o fragmento seguinte na janela **Rascunho-1**. No editor de JSON, efetue os seguintes passos:
    1. Especifique o **URI** para o cluster do HDInsight Spark. Por exemplo: `https://<sparkclustername>.azurehdinsight.net/`.
    2. Especifique o nome do **utilizador** quem tem acesso para o cluster do Spark.
    3. Especifique o **palavra-passe** para o utilizador.
    4. Especifique o **serviço ligado do Storage do Azure** que está associado ao cluster do HDInsight Spark. Neste exemplo, é: **AzureStorageLinkedService**.

    ```json
    {
        "name": "HDInsightLinkedService",
        "properties": {
            "type": "HDInsight",
            "typeProperties": {
                "clusterUri": "https://<sparkclustername>.azurehdinsight.net/",
                "userName": "admin",
                "password": "**********",
                "linkedServiceName": "AzureStorageLinkedService"
            }
        }
    }
    ```

    > [!IMPORTANT]
    > - Atividade de Spark não suporta clusters do HDInsight Spark que utilizam um Azure Data Lake Store, como armazenamento principal.
    > - Atividade de Spark suporta apenas existente (o seus próprios) cluster do HDInsight Spark. Não suporta um serviço de ligado de HDInsight a pedido.

    Consulte [serviço ligado de HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) para obter detalhes sobre o HDInsight serviço ligado.
3.  Para implementar o serviço ligado, clique em **implementar** na barra de comandos.  

### <a name="create-output-dataset"></a>Criar conjunto de dados de saída
O conjunto de dados de saída é o que pauta a agenda (hora a hora, diária, etc..). Por conseguinte, tem de especificar um conjunto de dados de saída para a atividade do spark no pipeline, apesar da atividade realmente não produzir qualquer saída. A especificação de um conjunto de dados de entrada para a atividade é opcional.

1. No **Data Factory Editor**, clique em **... Mais**, na barra de comandos, clique em **Novo conjunto de dados** e selecione **Armazenamento de Blobs do Azure**.  
2. Copie e cole o fragmento seguinte na janela Rascunho-1. O fragmento JSON define um conjunto de dados denominado **OutputDataset**. Além disso, especifica que os resultados são armazenados no contentor do blob denominado **adfspark** e na pasta denominada **pyFiles/saída**. Conforme mencionado anteriormente, este conjunto de dados é um conjunto de dados fictício. O programa de Spark neste exemplo não produzir qualquer saída. O **disponibilidade** secção especifica que o conjunto de dados de saída é produzido diariamente.  

    ```json
    {
        "name": "OutputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "sparkoutput.txt",
                "folderPath": "adfspark/pyFiles/output",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": "\t"
                }
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }
    ```
3. Para implementar o conjunto de dados, clique em **implementar** na barra de comandos.


### <a name="create-pipeline"></a>Criar pipeline
Neste passo, cria um pipeline com uma **HDInsightSpark** atividade. Atualmente, o conjunto de dados de saída é o que pauta a agenda, pelo que deve criar um conjunto de dados de saída, mesmo que a atividade não produza dados. Se a atividade não incluir entradas, pode ignorar a criação do conjunto de dados de entrada. Por conseguinte, nenhum conjunto de dados de entrada é especificado neste exemplo.

1. No **Editor do Data Factory**, clique em **... Mais** na barra de comando e, em seguida, clique em **novo pipeline**.
2. Substitua o script na janela rascunho-1 com o seguinte script:

    ```json
    {
        "name": "SparkPipeline",
        "properties": {
            "activities": [
                {
                    "type": "HDInsightSpark",
                    "typeProperties": {
                        "rootPath": "adfspark\\pyFiles",
                        "entryFilePath": "test.py",
                        "getDebugInfo": "Always"
                    },
                    "outputs": [
                        {
                            "name": "OutputDataset"
                        }
                    ],
                    "name": "MySparkActivity",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2017-02-05T00:00:00Z",
            "end": "2017-02-06T00:00:00Z"
        }
    }
    ```
    Tenha em atenção os seguintes pontos:
    - O **tipo** propriedade está definida como **HDInsightSpark**.
    - O **rootPath** está definido como **adfspark\\pyFiles** onde adfspark é o contentor de Blob do Azure e pyFiles é bem pasta no contentor. Neste exemplo, armazenamento de Blobs do Azure é o que está associado um cluster do Spark. Pode carregar o ficheiro para um armazenamento do Azure diferente. Se o fizer, crie um serviço ligado do Storage do Azure para ligar essa conta de armazenamento à fábrica de dados. Em seguida, especifique o nome do serviço ligado como um valor para o **sparkJobLinkedService** propriedade. Consulte [propriedades da atividade de Spark](#spark-activity-properties) para obter detalhes sobre esta propriedade e outras propriedades suportadas pela atividade de Spark.  
    - O **entryFilePath** está definido como o **test.py**, que é o ficheiro de python.
    - O **getDebugInfo** propriedade está definida como **sempre**, que significa que os ficheiros de registo são sempre gerada (êxito ou falha).

        > [!IMPORTANT]
        > Recomendamos que não definir esta propriedade como `Always` num ambiente de produção, a menos que esteja a resolver um problema.
    - O **produz** secção tem um conjunto de dados de saída. Tem de especificar um conjunto de dados de saída, mesmo que o programa de spark não produzir qualquer saída. O conjunto de dados de saída pauta a agenda para o pipeline (hora a hora, diária, etc..).  

        Para obter detalhes sobre as propriedades suportadas por atividade do Spark, consulte [Spark propriedades da atividade](#spark-activity-properties) secção.
3. Para implementar o pipeline, clique em **implementar** na barra de comandos.

### <a name="monitor-pipeline"></a>Monitorizar o pipeline
1. Clique em **X** para fechar os painéis do Editor do Data Factory e navegue de volta para a home page da fábrica de dados. Clique em **monitorizar e gerir** para iniciar a aplicação de monitorização no outro separador.

    ![Monitorizar e gerir o mosaico](media/data-factory-spark/monitor-and-manage-tile.png)
2. Alterar o **hora de início** filtro na parte superior para **1/2/2017**e clique em **aplicar**.
3. Deverá ver apenas uma janela de atividade que apenas um dia entre o início (2017-02-01) e a hora de fim (2017-02-02) do pipeline. Confirme que o setor de dados está no **pronto** estado.

    ![Monitorizar o pipeline](media/data-factory-spark/monitor-and-manage-app.png)    
4. Selecione o **janela de atividade** para ver detalhes sobre a execução da atividade. Se existir um erro, consulte os detalhes acerca do mesmo no painel direito.

### <a name="verify-the-results"></a>Verificar os resultados

1. Iniciar **bloco de notas do Jupyter** para o cluster do HDInsight Spark, navegando até: https://CLUSTERNAME.azurehdinsight.net/jupyter. Pode também iniciar o dashboard do cluster para o cluster do HDInsight Spark e, em seguida, iniciar **bloco de notas do Jupyter**.
2. Clique em **novo** -> **PySpark** para iniciar um novo bloco de notas.

    ![Novo bloco de notas Jupyter](media/data-factory-spark/jupyter-new-book.png)
3. Execute o seguinte comando ao copiar/colar o texto e premir **SHIFT + ENTER** no fim da segunda instrução.  

    ```sql
    %%sql

    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```
4. Confirme que vê os dados da tabela de AVAC:  

    ![Resultados de consulta do Jupyter](media/data-factory-spark/jupyter-notebook-results.png)

<!-- Removed bookmark #run-a-hive-query-using-spark-sql since it doesn't exist in the target article -->
Consulte [executar uma consulta de Spark SQL](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md) secção para obter instruções detalhadas. 

### <a name="troubleshooting"></a>Resolução de problemas
Uma vez que definiu **getDebugInfo** para **sempre**, verá um **registo** subpasta a **pyFiles** pasta no contentor de Blob do Azure. O ficheiro de registo na pasta de registo fornece detalhes adicionais. Este ficheiro de registo é especialmente útil quando existe um erro. Num ambiente de produção, poderá configurá-lo para **falha**.

Para resolução de problemas, efetue os seguintes passos:


1. Navegue para `https://<CLUSTERNAME>.azurehdinsight.net/yarnui/hn/cluster`.

    ![Aplicação de IU do YARN](media/data-factory-spark/yarnui-application.png)  
2. Clique em **registos** para um a execução tenta.

    ![Página de aplicações](media/data-factory-spark/yarn-applications.png)
3. Deverá ver informações de erro adicionais na página de registo.

    ![Erro de registo](media/data-factory-spark/yarnui-application-error.png)

As secções seguintes fornecem informações sobre entidades do Data Factory para utilizar o cluster do Apache Spark e a atividade do Spark na fábrica de dados.

## <a name="spark-activity-properties"></a>Propriedades da atividade do Spark
Segue-se a definição de JSON de exemplo de um pipeline com atividade de Spark:    

```json
{
    "name": "SparkPipeline",
    "properties": {
        "activities": [
            {
                "type": "HDInsightSpark",
                "typeProperties": {
                    "rootPath": "adfspark\\pyFiles",
                    "entryFilePath": "test.py",
                    "arguments": [ "arg1", "arg2" ],
                    "sparkConfig": {
                        "spark.python.worker.memory": "512m"
                    }
                    "getDebugInfo": "Always"
                },
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ],
                "name": "MySparkActivity",
                "description": "This activity invokes the Spark program",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-02-01T00:00:00Z",
        "end": "2017-02-02T00:00:00Z"
    }
}
```

A tabela seguinte descreve as propriedades JSON utilizadas na definição de JSON:

| Propriedade | Descrição | Necessário |
| -------- | ----------- | -------- |
| nome | Nome da atividade no pipeline. | Sim |
| descrição | Texto que descreve o que faz a atividade. | Não |
| tipo | Esta propriedade tem de ser definida para HDInsightSpark. | Sim |
| linkedServiceName | Nome do serviço ligado do HDInsight em que executa o programa de Spark. | Sim |
| rootPath | O contentor de Blob do Azure e a pasta que contém o ficheiro de Spark. O nome de ficheiro é maiúsculas e minúsculas. | Sim |
| entryFilePath | Caminho relativo para a pasta raiz do Spark/pacote do código. | Sim |
| className | Classe principal de Java/Spark da aplicação | Não |
| Argumentos | Uma lista de argumentos da linha de comandos para o programa de Spark. | Não |
| proxyUser | A conta de utilizador para representar a execução do programa de Spark | Não |
| sparkConfig | Especifique os valores de propriedades de configuração do Spark listados no tópico: [a configuração do Spark – propriedades da aplicação](https://spark.apache.org/docs/latest/configuration.html#available-properties). | Não |
| getDebugInfo | Especifica se os ficheiros de registo do Spark são copiados para o armazenamento do Azure utilizado pelo cluster do HDInsight (ou) especificado por sparkJobLinkedService. Valores permitidos: None, sempre ou falha. Valor predefinido: nenhuma. | Não |
| sparkJobLinkedService | Storage do Azure ligado serviço que detém o Spark registos, dependências e ficheiro de tarefa.  Se não especificar um valor para esta propriedade, o armazenamento associado ao cluster do HDInsight é utilizado. | Não |

## <a name="folder-structure"></a>Estrutura de pastas
A atividade de Spark não suporta um script de na linha como Pig e fazer de atividades de ramo de registo. Tarefas do Spark também são mais extensíveis que as tarefas do Pig/Hive. Para tarefas do Spark, pode fornecer múltiplas dependências, tal como jar pacotes (colocadas no java CLASSPATH), ficheiros de python (colocados a PYTHONPATH) e outros ficheiros.

Crie a seguinte estrutura de pasta no armazenamento de Blobs do Azure referenciado pelo serviço ligado de HDInsight. Em seguida, carregue ficheiros dependentes para as pastas sub adequado na pasta raiz representado pelo **entryFilePath**. Por exemplo, carregar o python para a subpasta pyFiles e ficheiros jar para a subpasta v7 da pasta raiz. Em runtime, o serviço fábrica de dados espera a seguinte estrutura de pasta do Blob storage do Azure:     

| Caminho | Descrição | Necessário | Tipo |
| ---- | ----------- | -------- | ---- |
| . | O caminho da raiz da tarefa do Spark no serviço ligado de armazenamento  | Sim | Pasta |
| &lt;definido pelo utilizador&gt; | O caminho que apontam para o ficheiro de entrada da tarefa de Spark | Sim | Ficheiro |
| . / jars | Todos os ficheiros desta pasta são carregados e colocados a classpath java do cluster | Não | Pasta |
| . / pyFiles | Todos os ficheiros desta pasta são carregados e colocados PYTHONPATH do cluster | Não | Pasta |
| . / ficheiros | Todos os ficheiros desta pasta são carregados e colocados no diretório de trabalho de executor | Não | Pasta |
| . / arquiva | Todos os ficheiros desta pasta são descomprimidos | Não | Pasta |
| . / registos | A pasta onde estão armazenados os registos do cluster do Spark.| Não | Pasta |

Eis um exemplo de um armazenamento que contém dois ficheiros de tarefa do Spark no armazenamento de Blobs do Azure referenciada pelo serviço ligado de HDInsight.

```
SparkJob1
    main.jar
    files
        input1.txt
        input2.txt
    jars
        package1.jar
        package2.jar
    logs

SparkJob2
    main.py
    pyFiles
        scrip1.py
        script2.py
    logs
```
