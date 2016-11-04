---
title: Criar a primeira fábrica de dados (modelo Resource Manager) | Microsoft Docs
description: Neste tutorial, vai criar um exemplo de pipeline do Azure Data Factory com um modelo do Azure Resource Manager.
services: data-factory
documentationcenter: ''
author: spelluru
manager: jhubbard
editor: monicar

ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 08/01/2016
ms.author: spelluru

---
# Tutorial: Criar a primeira fábrica de dados do Azure com o modelo Azure Resource Manager
> [!div class="op_single_selector"]
> * [Descrição geral e pré-requisitos](data-factory-build-your-first-pipeline.md)
> * [Portal do Azure](data-factory-build-your-first-pipeline-using-editor.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Modelo do Resource Manager](data-factory-build-your-first-pipeline-using-arm.md)
> * [API REST](data-factory-build-your-first-pipeline-using-rest-api.md)
> 
> 

Neste artigo, vai utilizar um modelo do Azure Resource Manager para criar a sua primeira fábrica de dados do Azure.

## Pré-requisitos
* Leia o artigo [Descrição Geral do Tutorial](data-factory-build-your-first-pipeline.md) e conclua os passos de **pré-requisitos**.
* Siga as instruções no artigo [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](../powershell-install-configure.md) para instalar a versão mais recente do Azure PowerShell no computador.
* Veja [Authoring Azure Resource Manager Templates (Criação de Modelos Azure Resource Manager)](../resource-group-authoring-templates.md) para saber mais sobre os modelos Azure Resource Manager. 

## Criar Modelo do Resource Manager
Nesta secção, vai criar as seguintes entidades do Data Factory: 

1. Uma **fábrica de dados** com o nome **TutorialDataFactoryARM**. Uma fábrica de dados pode ter um ou mais pipelines. Um pipeline pode conter uma atividade ou mais. Por exemplo, uma Atividade de Cópia para copiar dados de uma origem para um arquivo de dados de destino e uma Atividade Hive do HDInsight para executar o script de Hive para transformar dados de entrada. 
2. Dois **serviços ligados**: **StorageLinkedService** e **HDInsightOnDemandLinkedService**. Estes serviços ligados ligam a Conta de armazenamento do Azure e um cluster do Azure HDInsight a pedido à fábrica de dados. A conta de Armazenamento do Azure possui os dados de entrada e de saída do pipeline neste exemplo. Neste exemplo, o serviço ligado do HDInsight é utilizado para executar o Script de ramo de registo especificado na atividade do pipeline. Identifique os dados de arquivo de dados/serviços de computação que são utilizados no seu cenário e ligue esses serviços à fábrica de dados, criando serviços ligados. 
3. Dois **conjuntos de dados** (entrada/saída): **AzureBlobInput** e **AzureBlobOutput**. Estes conjuntos de dados representam os dados de entrada e de saída do processamento do Ramo de Registo. Estes conjuntos de dados referem-se ao **StorageLinkedService** que criou anteriormente neste tutorial. O serviço ligado aponta para uma Conta de armazenamento do Azure e os conjuntos de dados especificam um contentor, uma pasta, um nome de ficheiro no armazenamento que contém os dados de entrada e de saída.   

Clique no separador **Com o Editor do Data Factory** para passar para o artigo com detalhes sobre as propriedades JSON utilizadas neste modelo.

Crie um ficheiro JSON com o nome **ADFTutorialARM.json** na pasta **C:\ADFGetStarted** com o seguinte conteúdo:

> [!IMPORTANT]
> Altere os valores das variáveis **storageAccountName** e **storageAccountKey**. Altere ainda o **dataFactoryName** uma vez que o nome tem de ser exclusivo.
> 
> 

    {
        "contentVersion": "1.0.0.0",
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {
        },
        "variables": {
            "dataFactoryName":  "TutorialDataFactoryARM",
            "storageAccountName":  "<AZURE STORAGE ACCOUNT NAME>" ,
            "storageAccountKey":  "<AZURE STORAGE ACCOUNT KEY>",
            "apiVersion": "2015-10-01",
            "storageLinkedServiceName": "AzureStorageLinkedService",
            "hdInsightOnDemandLinkedServiceName": "HDInsightOnDemandLinkedService",
            "blobInputDataset": "AzureBlobInput",
            "blobOutputDataset": "AzureBlobOutput",
            "singleQuote": "'"
        },
        "resources": [
            {
                "name": "[variables('dataFactoryName')]",
                "apiVersion": "[variables('apiVersion')]",
                "type": "Microsoft.DataFactory/datafactories",
                "location": "westus",
                "resources": [
                    {
                        "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]" ],
                        "type": "linkedservices",
                        "name": "[variables('storageLinkedServiceName')]",
                        "apiVersion": "[variables('apiVersion')]",
                        "properties": {
                            "type": "AzureStorage",
                            "typeProperties": {
                                "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',variables('storageAccountName'),';AccountKey=',variables('storageAccountKey'))]"
                            }
                        }
                    },
                    {
                        "dependsOn": [
                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedservices/', variables('storageLinkedServiceName'))]"
                        ],
                        "type": "linkedservices",
                        "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
                        "apiVersion": "[variables('apiVersion')]",
                        "properties": {
                            "type": "HDInsightOnDemand",
                            "typeProperties": {
                                "clusterSize": 4,
                                "version":  "3.2",
                                "timeToLive": "00:05:00",
                                "osType": "windows",
                                "linkedServiceName": "[variables('storageLinkedServiceName')]",
                            }
                        }
                    },
                    {
                        "dependsOn": [
                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
                        ],
                        "type": "datasets",
                        "name": "[variables('blobInputDataset')]",
                        "apiVersion": "[variables('apiVersion')]",
                            "properties": {
                                "type": "AzureBlob",
                                "linkedServiceName": "[variables('storageLinkedServiceName')]",
                                "typeProperties": {
                                    "fileName": "input.log",
                                    "folderPath": "adfgetstarted/inputdata",
                                    "format": {
                                        "type": "TextFormat",
                                        "columnDelimiter": ","
                                    }
                                },
                                "availability": {
                                    "frequency": "Month",
                                    "interval": 1
                                },
                                "external": true,
                                "policy": {}
                            }
                        },
                    {
                        "dependsOn": [
                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
                        ],
                        "type": "datasets",
                        "name": "[variables('blobOutputDataset')]",
                        "apiVersion": "[variables('apiVersion')]",
                            "properties": {
                                "published": false,
                                "type": "AzureBlob",
                                "linkedServiceName": "[variables('storageLinkedServiceName')]",
                                "typeProperties": {
                                    "folderPath": "adfgetstarted/partitioneddata",
                                    "format": {
                                        "type": "TextFormat",
                                        "columnDelimiter": ","
                                    }
                                },
                                "availability": {
                                    "frequency": "Month",
                                    "interval": 1
                                }
                            }
                        },
                        {
                            "dependsOn": [
                                "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
                                "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]",
                                "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('hdInsightOnDemandLinkedServiceName'))]",
                                "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/datasets/', variables('blobInputDataset'))]",
                                "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/datasets/', variables('blobOutputDataset'))]"
                            ],
                            "type": "datapipelines",
                            "name": "[variables('dataFactoryName')]",
                            "apiVersion": "[variables('apiVersion')]",
                            "properties": {
                                "description": "My first Azure Data Factory pipeline",
                                "activities": [
                                    {
                                        "type": "HDInsightHive",
                                        "typeProperties": {
                                            "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                                            "scriptLinkedService": "[variables('storageLinkedServiceName')]",
                                            "defines": {
                                                "inputtable": "[concat('wasb://adfgetstarted@', variables('storageAccountName'), '.blob.core.windows.net/inputdata')]",
                                                "partitionedtable": "[concat('wasb://adfgetstarted@', variables('storageAccountName'), '.blob.core.windows.net/partitioneddata')]"
                                            }
                                        },
                                        "inputs": [
                                            {
                                                "name": "AzureBlobInput"
                                            }
                                        ],
                                        "outputs": [
                                            {
                                                "name": "AzureBlobOutput"
                                            }
                                        ],
                                        "policy": {
                                            "concurrency": 1,
                                            "retry": 3
                                        },
                                        "scheduler": {
                                            "frequency": "Month",
                                            "interval": 1
                                        },
                                        "name": "RunSampleHiveActivity",
                                        "linkedServiceName": "HDInsightOnDemandLinkedService"
                                    }
                                ],
                                "start": "2016-04-01T00:00:00Z",
                                "end": "2016-04-02T00:00:00Z",
                                "isPaused": false
                            }
                        }
                ]
            }
        ]
    }

Clique no separador **Com o Editor do Data Factory** para passar para o artigo que contém os detalhes sobre as propriedades JSON utilizadas neste modelo.

Tenha em atenção o seguinte: 

* O Data Factory cria um cluster do HDInsight **baseado no Windows** com o JSON acima. Também pode fazer com que crie um cluster do HDInsight **baseado no Linux**. Veja [On-demand HDInsight Linked Service (Serviço Ligado do HDInsight a Pedido)](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obter detalhes. 
* Também pode utilizar o **seu próprio cluster do HDInsight** em vez de utilizar um cluster do HDInsight a pedido. Veja [HDInsight Linked Service (Serviço Ligado do HDInsight)](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) para obter detalhes.
* O cluster do HDInsight cria um **contentor predefinido** no armazenamento de blobs especificado no JSON (**linkedServiceName**). Quando o cluster é eliminado, o HDInsight não é eliminado deste contentor. Este comportamento é propositado. Com o serviço ligado do HDInsight a pedido, é criado um cluster do HDInsight sempre que um setor tiver de ser processado, exceto se houver um cluster em direto (**timeToLive**) que será eliminado no fim do processamento.
  
    À medida que são processados mais setores, verá muitos contentores no armazenamento de blobs do Azure. Se não precisar deles para a resolução de problemas das tarefas, poderá eliminá-los para reduzir o custo de armazenamento. Os nomes destes contentores seguem um padrão: "adf**nomedafábricadedados**-**nomedoserviçoligado**-carimbodedataehora". Utilize ferramentas como o [Explorador de Armazenamento do Microsoft](http://storageexplorer.com/) para eliminar contentores no armazenamento de blobs do Azure.

Veja [On-demand HDInsight Linked Service (Serviço Ligado do HDInsight a Pedido)](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obter detalhes.

> [!NOTE]
> Pode encontrar outro exemplo do modelo do Resource Manager para criar uma fábrica de dados do Azure no [Github](https://github.com/Azure/azure-quickstart-templates/blob/master/101-data-factory-blob-to-sql/azuredeploy.json).  
> 
> 

## Criar fábrica de dados
1. Inicie o **Azure PowerShell** e execute o seguinte comando: 
   * Execute `Login-AzureRmAccount` e introduza o nome de utilizador e a palavra-passe que utiliza para iniciar sessão no portal do Azure.  
   * Execute `Get-AzureRmSubscription` para ver todas as subscrições para esta conta.
   * Execute `Get-AzureRmSubscription -SubscriptionName <SUBSCRIPTION NAME> | Set-AzureRmContext` para selecionar a subscrição com a qual pretende trabalhar. Esta subscrição deve ser idêntica à que utilizou no Portal do Azure.
2. Execute o seguinte comando para implementar as entidades do Data Factory com o modelo do Resource Manager que criou no Passo 1. 
   
        New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFTutorialARM.json

## Monitorizar o pipeline
1. Depois de iniciar sessão no [portal do Azure](https://portal.azure.com/), clique em **Procurar** e selecione **Fábricas de dados**.
       ![Procurar->Fábricas de dados](./media/data-factory-build-your-first-pipeline-using-arm/BrowseDataFactories.png)
2. No painel **Fábricas de dados**, clique na fábrica de dados (**TutorialFactoryARM**) que criou.   
3. No painel **Data Factory** da fábrica de dados, clique em **Diagrama**.
       ![Mosaico do Diagrama](./media/data-factory-build-your-first-pipeline-using-arm/DiagramTile.png)
4. Na **Vista de Diagrama**, verá uma descrição geral dos pipelines e dos conjuntos de dados utilizados neste tutorial.
   
   ![Vista de Diagrama](./media/data-factory-build-your-first-pipeline-using-arm/DiagramView.png) 
5. Na Vista de Diagrama, faça duplo clique no conjunto de dados **AzureBlobOutput**. Verá o setor que está atualmente a ser processado.
   
    ![Conjunto de dados](./media/data-factory-build-your-first-pipeline-using-arm/AzureBlobOutput.png)
6. Quando o processamento terminar, verá o setor no estado **Pronto**. A criação de um cluster do HDInsight a pedido demora, por norma, algum tempo (cerca de 20 minutos). Assim, prevê-se que o pipeline demore **aproximadamente 30 minutos** a processar o setor.
   
    ![Conjunto de dados](./media/data-factory-build-your-first-pipeline-using-arm/SliceReady.png) 
7. Quando o setor estiver no estado **Pronto**, verifique a pasta **partitioneddata** no contentor **adfgetstarted** do seu armazenamento de blobs para os dados de saída.  

Veja [Monitor datasets and pipeline (Monitorizar os conjuntos de dados e o pipeline)](data-factory-monitor-manage-pipelines.md) para obter instruções sobre como utilizar os painéis do Portal do Azure para monitorizar o pipeline e os conjuntos de dados que criou neste tutorial.

Pode ainda utilizar a Aplicação de Monitorização e Gestão para monitorizar os seus pipelines de dados. Veja [Monitor and manage Azure Data Factory pipelines using Monitoring App (Monitorizar e gerir pipelines do Azure Data Factory com a Aplicação de Monitorização)](data-factory-monitor-manage-app.md) para obter detalhes sobre a utilização da aplicação. 

> [!IMPORTANT]
> O ficheiro de entrada é eliminado quando o setor é processado com êxito. Por conseguinte, se pretender voltar a executar o setor ou repetir o tutorial, carregue o ficheiro de entrada (input.log) na pasta inputdata do contentor adfgetstarted.
> 
> 

## Modelo do Resource Manager para criar um gateway
Eis o exemplo de um modelo do Resource Manager para criar um gateway lógico. Instale um gateway no computador no local ou na VM de IaaS do Azure e registe o gateway com o serviço do Data Factory com uma chave. Veja [Move data between on-premises and cloud (Mover dados entre o local e a nuvem)](data-factory-move-data-between-onprem-and-cloud.md) para obter detalhes.

    {
        "contentVersion": "1.0.0.0",
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {
        },
        "variables": {
            "dataFactoryName":  "GatewayUsingArmDF",
            "apiVersion": "2015-10-01",
            "singleQuote": "'"
        },
        "resources": [
            {
                "name": "[variables('dataFactoryName')]",
                "apiVersion": "[variables('apiVersion')]",
                "type": "Microsoft.DataFactory/datafactories",
                "location": "eastus",
                "resources": [
                    {
                        "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]" ],
                        "type": "gateways",
                        "apiVersion": "[variables('apiVersion')]",
                        "name": "GatewayUsingARM",
                        "properties": {
                            "description": "my gateway"
                        }
                    }            
                ]
            }
        ]
    }

Este modelo cria uma fábrica de dados com o nome GatewayUsingArmDF com um gateway designado: GatewayUsingARM. 

## Veja também
| Tópico | Descrição |
|:--- |:--- |
| [Atividades de Transformação de Dados](data-factory-data-transformation-activities.md) |Este artigo fornece uma lista de atividades de transformação de dados (por exemplo, a transformação do Ramo de registo do HDInsight que utilizou neste tutorial) suportada pelo Azure Data Factory. |
| [Agendamento e execução](data-factory-scheduling-and-execution.md) |Este artigo explica os aspetos de agendamento e execução do modelo da aplicação do Azure Data Factory. |
| [Pipelines](data-factory-create-pipelines.md) |Este artigo ajuda-o a compreender os pipelines e as atividades no Azure Data Factory e como os utilizar para construir fluxos de dados ponto a ponto condicionados por dados para o seu cenário ou empresa. |
| [Conjuntos de dados](data-factory-create-datasets.md) |Este artigo ajuda-o a compreender os conjuntos de dados no Azure Data Factory. |
| [Monitorizar e gerir pipelines com a Aplicação de Monitorização](data-factory-monitor-manage-app.md) |Este artigo descreve como monitorizar, gerir e depurar pipelines com a Aplicação de Monitorização e Gestão. |

<!--HONumber=Sep16_HO3-->


