---
title: "Criar a primeira fábrica de dados (portal do Azure) | Microsoft Docs"
description: Neste tutorial, vai criar um exemplo de pipeline do Azure Data Factory com o Editor do Data Factory no portal do Azure.
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: d5b14e9e-e358-45be-943c-5297435d402d
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 07/10/2017
ms.author: spelluru
robots: noindex
ms.openlocfilehash: 19f9686de9face1e53fc84eac23381eadc9fb5cd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-build-your-first-azure-data-factory-using-azure-portal"></a>Tutorial: Criar a primeira fábrica de dados do Azure com o portal do Azure
> [!div class="op_single_selector"]
> * [Descrição geral e pré-requisitos](data-factory-build-your-first-pipeline.md)
> * [Portal do Azure](data-factory-build-your-first-pipeline-using-editor.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Modelo do Resource Manager](data-factory-build-your-first-pipeline-using-arm.md)
> * [API REST](data-factory-build-your-first-pipeline-using-rest-api.md)


Neste artigo, aprende a utilizar o [portal do Azure](https://portal.azure.com/) para criar a primeira fábrica de dados do Azure. Para fazer o tutorial com outras ferramentas/SDKs, selecione uma das opções na lista pendente. 

O pipeline neste tutorial tem uma atividade: **atividade do HDInsight Hive**. Esta atividade executa um script de ramo de registo num cluster do Azure HDInsight que transforma os dados de entrada para produzir os dados de saída. O pipeline está agendado para ser executado uma vez por mês entre as horas de início e de fim especificadas. 

> [!NOTE]
> O pipeline de dados neste tutorial transforma os dados de entrada para produzirem dados de saída. Para um tutorial sobre como copiar dados com o Azure Data Factory, consulte [Tutorial: Copy data from Blob Storage to SQL Database (Tutorial: copiar dados do Armazenamento de Blobs para a Base de Dados SQL)](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> Um pipeline pode ter mais de uma atividade. Além disso, pode encadear duas atividades (executar uma atividade após a outra) ao definir o conjunto de dados de saída de uma atividade como o conjunto de dados de entrada da outra. Para obter mais informações, veja [scheduling and execution in Data Factory](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) (agendamento e execução no Data Factory).

## <a name="prerequisites"></a>Pré-requisitos
1. Leia o artigo [Descrição Geral do Tutorial](data-factory-build-your-first-pipeline.md) e conclua os passos de **pré-requisitos**.
2. Este artigo não fornece uma descrição geral conceptual do serviço Azure Data Factory. Recomendamos-lhe que leia o artigo [Introduction to Azure Data Factory (Introdução ao Azure Data Factory)](data-factory-introduction.md) para obter uma descrição geral detalhada do serviço.  

## <a name="create-data-factory"></a>Criar fábrica de dados
Uma fábrica de dados pode ter um ou mais pipelines. Um pipeline pode conter uma atividade ou mais. Por exemplo, uma Atividade de Cópia para copiar dados de uma origem para um arquivo de dados de destino e uma Atividade do Ramo de Registo do HDInsight para executar um Script de Ramo de Registo para transformar dados de entrada em dados de saída do produto. Comecemos este passo com a criação da fábrica de dados.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Clique em **NOVO**, no menu da esquerda, clique em **Dados + Análise** e, em seguida, em **Data Factory**.

   ![Criar painel](./media/data-factory-build-your-first-pipeline-using-editor/create-blade.png)
3. No painel **Nova fábrica de dados**, introduza **GetStartedDF** para o nome.

   ![Painel Nova fábrica de dados](./media/data-factory-build-your-first-pipeline-using-editor/new-data-factory-blade.png)

   > [!IMPORTANT]
   > O nome do Azure Data Factory deve ser **globalmente exclusivo**. Se receber o erro: **O nome da fábrica de dados "GetStartedDF" não está disponível**. Altere o nome da fábrica de dados (por exemplo, oseunomeGetStartedDF) e tente criá-la novamente. Veja o tópico [Data Factory – Naming Rules (Data Factory – Regras de Nomenclatura)](data-factory-naming-rules.md) para obter as regras de nomenclatura dos artefactos do Data Factory.
   >
   > O nome da fábrica de dados pode ser registado como um nome **DNS** no futuro e, por conseguinte, ficar publicamente visível.
   >
   >
4. Selecione a **Subscrição do Azure** onde pretende que seja criada a fábrica de dados.
5. Selecione o **grupo de recursos** existente ou crie um grupo de recursos. Para este tutorial, crie um grupo de recursos com o nome: **ADFGetStartedRG**.
6. Selecione a **localização** da fábrica de dados. A lista pendente só mostra as regiões que o serviço Data Factory suporta.
7. Selecione **Afixar ao dashboard**. 
8. Clique em **Criar** no painel **Nova fábrica de dados**.

   > [!IMPORTANT]
   > Para criar instâncias do Data Factory, tem de ser um membro da função [Contribuinte do Data Factory](../../active-directory/role-based-access-built-in-roles.md#data-factory-contributor) ao nível do grupo de recursos/subscrição.
   >
   >
7. No painel, vai ver o mosaico seguinte com o estado: A implementar fábrica de dados.    

   ![Estado de criação da fábrica de dados](./media/data-factory-build-your-first-pipeline-using-editor/creating-data-factory-image.png)
8. Parabéns! Criou com êxito a sua primeira fábrica de dados. Depois de ter criado a fábrica de dados com êxito, vê a página da fábrica de dados e o respetivo conteúdo.     

    ![Painel Data Factory](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-blade.png)

Antes de criar um pipeline na fábrica de dados, deve primeiro criar algumas entidades do Data Factory. Deve criar primeiro os serviços ligados para ligar os arquivos/computações de dados ao seu arquivo de dados, definir os conjuntos de dados de entrada e saída para representar os dados de entrada e saída em arquivos de dados ligados e criar o pipeline com uma atividade que utiliza estes conjuntos de dados.

## <a name="create-linked-services"></a>Criar serviços ligados
Neste passo, vai ligar a sua conta de Armazenamento do Azure e um cluster do Azure HDInsight a pedido à fábrica de dados. A conta de Armazenamento do Azure possui os dados de entrada e de saída do pipeline neste exemplo. Neste exemplo, o serviço ligado do HDInsight serve para executar um script do Hive especificado na atividade do pipeline. Identifique que [arquivo de dados](data-factory-data-movement-activities.md)/[serviços de computação](data-factory-compute-linked-services.md) são utilizados no cenário e ligue esses serviços à fábrica de dados através da criação de serviços ligados.  

### <a name="create-azure-storage-linked-service"></a>Criar o serviço ligado do Storage do Azure
Neste passo, vai ligar a sua conta de Armazenamento do Azure à fábrica de dados. Neste tutorial, vai utilizar a mesma conta de Armazenamento do Azure para guardar os dados de entrada/saída e o ficheiro de script HQL.

1. Clique em **Criar e implementar** no painel **DATA FACTORY** para **GetStartedDF**. Deverá ver o Editor do Data Factory.

   ![Mosaico Criar e implementar](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-author-deploy.png)
2. Clique em **Novo arquivo de dados** e escolha **Armazenamento do Azure**.

   ![Menu Novo armazenamento de dados – Armazenamento do Azure](./media/data-factory-build-your-first-pipeline-using-editor/new-data-store-azure-storage-menu.png)
3. Deverá ver o script JSON para criar um serviço ligado do Storage do Azure no editor.

   ![Serviço ligado do Storage do Azure](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)
4. Substitua o **nome da conta** pelo nome da sua conta de armazenamento do Azure e a **chave da conta** pela chave de acesso da conta de armazenamento do Azure. Para saber como obter a sua chave de acesso ao armazenamento, veja as informações sobre como ver, copiar e regenerar as chaves de acesso de armazenamento em [Gerir a conta do Armazenamento](../../storage/common/storage-create-storage-account.md#manage-your-storage-account).
5. Clique em **Implementar** na barra de comandos para implementar o serviço ligado.

    ![Botão Implementar](./media/data-factory-build-your-first-pipeline-using-editor/deploy-button.png)

   Depois de implementar o serviço ligado com êxito, a janela **Rascunho-1** deve desaparecer e verá **AzureStorageLinkedService** na vista de árvore à esquerda.

    ![Serviço Ligado do Storage no menu](./media/data-factory-build-your-first-pipeline-using-editor/StorageLinkedServiceInTree.png)    

### <a name="create-azure-hdinsight-linked-service"></a>Criar o serviço ligado do Azure HDInsight
Neste passo, irá ligar um cluster do HDInsight a pedido à sua fábrica de dados. O cluster do HDInsight é criado automaticamente no tempo de execução e eliminado depois de ter sido processado e ficado inativo pelo período de tempo especificado.

1. No **Data Factory Editor**, clique em **... Mais**, em **Nova computação** e selecione **Cluster de HDInsight a Pedido**.

    ![Nova computação](./media/data-factory-build-your-first-pipeline-using-editor/new-compute-menu.png)
2. Copie e cole o fragmento seguinte na janela **Rascunho-1**. O fragmento JSON descreve as propriedades que são utilizadas para criar o cluster HDInsight a pedido.

    ```JSON
    {
        "name": "HDInsightOnDemandLinkedService",
        "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
                "version": "3.5",
                "clusterSize": 1,
                "timeToLive": "00:05:00",
                "osType": "Linux",
                "linkedServiceName": "AzureStorageLinkedService"
            }
        }
    }
    ```

    A tabela que se segue fornece descrições para as propriedades JSON utilizadas no fragmento:

   | Propriedade | Descrição |
   |:--- |:--- |
   | ClusterSize |Especifica o tamanho do cluster HDInsight. |
   | TimeToLive | Especifica o tempo de inatividade do cluster do HDInsight, antes de ser eliminado. |
   | linkedServiceName | Especifica a conta de armazenamento que é utilizada para armazenar os registos que são gerados pelo HDInsight. |

    Tenha em atenção os seguintes pontos:

   * O Data Factory cria um cluster do HDInsight **baseado no Linux** com o JSON. Veja [On-demand HDInsight Linked Service (Serviço Ligado do HDInsight a Pedido)](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obter detalhes.
   * Também pode utilizar o **seu próprio cluster do HDInsight** em vez de utilizar um cluster do HDInsight a pedido. Veja [HDInsight Linked Service (Serviço Ligado do HDInsight)](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) para obter detalhes.
   * O cluster do HDInsight cria um **contentor predefinido** no armazenamento de blobs especificado no JSON (**linkedServiceName**). Quando o cluster é eliminado, o HDInsight não é eliminado deste contentor. Este comportamento é propositado. Com o serviço ligado do HDInsight a pedido, é criado um cluster do HDInsight sempre que é processado um setor, exceto se houver um cluster em direto (**timeToLive**). O cluster é eliminado automaticamente quando o processamento é concluído.

       À medida que são processados mais setores, verá muitos contentores no armazenamento de blobs do Azure. Se não precisar deles para a resolução de problemas das tarefas, poderá eliminá-los para reduzir o custo de armazenamento. Os nomes destes contentores seguem um padrão: "adf**nomedafábricadedados**-**nomedoserviçoligado**-carimbodedataehora". Utilize ferramentas como o [Explorador de Armazenamento do Microsoft](http://storageexplorer.com/) para eliminar contentores no armazenamento de blobs do Azure.

     Veja [On-demand HDInsight Linked Service (Serviço Ligado do HDInsight a Pedido)](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obter detalhes.
3. Clique em **Implementar** na barra de comandos para implementar o serviço ligado.

    ![Implementar o serviço ligado de HDInsight a pedido](./media/data-factory-build-your-first-pipeline-using-editor/ondemand-hdinsight-deploy.png)
4. Confirme se vê **AzureStorageLinkedService** e **HDInsightOnDemandLinkedService** na vista de árvore à esquerda.

    ![Vista de árvore com serviços ligados](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-linked-services.png)

## <a name="create-datasets"></a>Criar conjuntos de dados
Neste passo, vai criar conjuntos de dados para representar os dados de entrada e de saída do processamento do Hive. Estes conjuntos de dados referem-se ao **AzureStorageLinkedService** que criou anteriormente neste tutorial. O serviço ligado aponta para uma Conta de armazenamento do Azure e os conjuntos de dados especificam um contentor, uma pasta, um nome de ficheiro no armazenamento que contém os dados de entrada e de saída.   

### <a name="create-input-dataset"></a>Criar conjunto de dados de entrada
1. No **Data Factory Editor**, clique em **... Mais**, na barra de comandos, clique em **Novo conjunto de dados** e selecione **Armazenamento de Blobs do Azure**.

    ![Novo conjunto de dados](./media/data-factory-build-your-first-pipeline-using-editor/new-data-set.png)
2. Copie e cole o fragmento seguinte na janela Rascunho-1. No fragmento JSON, está a criar um conjunto de dados denominado **AzureBlobInput**, que representa os dados de entrada de uma atividade no pipeline. Além disso, especifica que os dados de entrada estão localizados no contentor do blob denominado **adfgetstarted** e na pasta denominada **inputdata**.

    ```JSON
    {
        "name": "AzureBlobInput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
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
    }
    ```
    A tabela que se segue fornece descrições para as propriedades JSON utilizadas no fragmento:

   | Propriedade | Descrição |
   |:--- |:--- |
   | tipo |O tipo de propriedade é definido como **AzureBlob**, uma vez que os dados residem num armazenamento de blobs do Azure. |
   | linkedServiceName |Refere-se ao **AzureStorageLinkedService** que criou anteriormente. |
   | folderPath | Especifica o **contentor** de blobs e a **pasta** que contém os blobs de entrada. | 
   | fileName |Esta propriedade é opcional. Se omitir esta propriedade, serão escolhidos todos os ficheiros em folderPath. Neste tutorial, apenas o **input.log** é processado. |
   | tipo |Os ficheiros de registo estão no formato de texto, pelo que vamos utilizar **TextFormat**. |
   | columnDelimiter |As colunas nos ficheiros de registo são delimitadas por **vírgula (`,`)** |
   | frequência/intervalo |A frequência definida para o **Mês**, sendo o intervalo **1**, o que significa que os setores de entrada estão disponíveis mensalmente. |
   | externo | Esta propriedade está definida como **verdadeira** se os dados de entrada não forem gerados por este pipeline. Neste tutorial, o ficheiro input.log não é gerado por este pipeline, por isso definimos a propriedade como true. |

    Para obter mais informações sobre estas propriedades JSON, veja [Azure Blob connector](data-factory-azure-blob-connector.md#dataset-properties) (Conector de Blobs do Azure).
3. Clique em **Implementar** na barra de comandos para implementar o conjunto de dados recentemente criado. Deverá ver o conjunto de dados na vista de árvore à esquerda.

### <a name="create-output-dataset"></a>Criar conjunto de dados de saída
Agora, irá criar o conjunto de dados de saída para representar os dados de saída armazenados no armazenamento de blobs do Azure.

1. No **Data Factory Editor**, clique em **... Mais**, na barra de comandos, clique em **Novo conjunto de dados** e selecione **Armazenamento de Blobs do Azure**.  
2. Copie e cole o fragmento seguinte na janela Rascunho-1. No fragmento JSON, está a criar um conjunto de dados denominado **AzureBlobOutput** e a especificar a estrutura dos dados que são produzidos pelo script de Hive. Além disso, especifica que os resultados são armazenados no contentor do blob denominado **adfgetstarted** e na pasta denominada **partitioneddata**. A secção **disponibilidade** especifica que o conjunto de dados de saída é produzido mensalmente.

    ```JSON
    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
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
    }
    ```
    Veja a secção **Criar o conjunto de dados de entrada** para obter as descrições destas propriedades. Não defina a propriedade externa num conjunto de dados de saída, uma vez que o conjunto de dados é produzido pelo serviço Data Factory.
3. Clique em **Implementar** na barra de comandos para implementar o conjunto de dados recentemente criado.
4. Verifique se o conjunto de dados foi criado com êxito.

    ![Vista de árvore com serviços ligados](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-data-set.png)

## <a name="create-pipeline"></a>Criar pipeline
Neste passo, irá criar o seu primeiro pipeline com uma atividade **HDInsightHive**. O setor de entrada está disponível mensalmente (frequência: mês, intervalo: 1), o setor de saída é produzido mensalmente e a propriedade do agendador da atividade também está definida como mensal. As definições para o conjunto de dados de saída e o agendador de atividade têm de corresponder. Atualmente, o conjunto de dados de saída é o que pauta a agenda, pelo que deve criar um conjunto de dados de saída, mesmo que a atividade não produza dados. Se a atividade não incluir entradas, pode ignorar a criação do conjunto de dados de entrada. No final desta secção, encontrará uma explicação sobre as propriedades utilizadas no seguinte JSON.

1. No **Editor do Data Factory**, clique nas **reticências (…) Mais comandos** e, em seguida, clique em **Novo pipeline**.

    ![botão Novo pipeline](./media/data-factory-build-your-first-pipeline-using-editor/new-pipeline-button.png)
2. Copie e cole o fragmento seguinte na janela Rascunho-1.

   > [!IMPORTANT]
   > Substitua **storageaccountname** pelo nome da sua conta de armazenamento no JSON.
   >
   >

    ```JSON
    {
        "name": "MyFirstPipeline",
        "properties": {
            "description": "My first Azure Data Factory pipeline",
            "activities": [
                {
                    "type": "HDInsightHive",
                    "typeProperties": {
                        "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                        "scriptLinkedService": "AzureStorageLinkedService",
                        "defines": {
                            "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                            "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
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
            "start": "2017-07-01T00:00:00Z",
            "end": "2017-07-02T00:00:00Z",
            "isPaused": false
        }
    }
    ```

    No fragmento JSON, está a criar um pipeline que consiste numa única atividade que utiliza o Ramo de Registo para processar Dados num cluster do HDInsight.

    O ficheiro do Script de ramo de registo **partitionweblogs.hql** é armazenado na conta de armazenamento do Azure (especificada pelo scriptLinkedService, denominado **AzureStorageLinkedService**) e na pasta **script** no contentor **adfgetstarted**.

    A secção **define** é utilizada para especificar as definições do tempo de execução que são transmitidas ao script de Hive como valores de configuração do Hive (por exemplo, ${hiveconf:inputtable}, ${hiveconf:partitionedtable}).

    As propriedades **início** e **fim** do pipeline especificam o período ativo do pipeline.

    No JSON de atividade, especifique que o Script de ramo de registo é executado na computação especificada pelo **linkedServiceName** – **HDInsightOnDemandLinkedService**.

   > [!NOTE]
   > Veja "JSON do Pipeline" em [Pipelines and activities in Azure Data Factory (Pipelines e atividades no Azure Data Factory)](data-factory-create-pipelines.md) para obter detalhes das propriedades JSON utilizadas no exemplo.
   >
   >
3. Confirme se:

   1. O ficheiro **input.log** existe na pasta **inputdata** do contentor **adfgetstarted** no armazenamento de blobs do Azure
   2. O ficheiro **partitionweblogs.hql** existe na pasta **script** do contentor **adfgetstarted** no armazenamento de blobs do Azure. Se não vir estes ficheiros, conclua os passos dos pré-requisitos apresentados na [Descrição geral do Tutorial](data-factory-build-your-first-pipeline.md).
   3. Confirme se substituiu **storageaccountname** pelo nome da sua conta de armazenamento no pipeline JSON.
4. Clique em **Implementar** na barra de comandos para implementar o pipeline. Uma vez que as horas de **início** e **fim** são definidas no passado e **isPaused** é definido como falso, o pipeline (atividade no pipeline) é executado imediatamente depois da implementação.
5. Verifique se vê o pipeline na vista de árvore.

    ![Vista de árvore com o pipeline](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-pipeline.png)
6. Parabéns, criou com êxito o seu primeiro pipeline!

## <a name="monitor-pipeline"></a>Monitorizar o pipeline
### <a name="monitor-pipeline-using-diagram-view"></a>Monitorizar o pipeline com a Vista de Diagrama
1. Clique em **X** para fechar os painéis do Editor do Data Factory e para regressar ao painel Data Factory e, em seguida, clique em **Diagrama**.

    ![Mosaico do diagrama](./media/data-factory-build-your-first-pipeline-using-editor/diagram-tile.png)
2. Na Vista de Diagrama, verá uma descrição geral dos pipelines e dos conjuntos de dados utilizados neste tutorial.

    ![Vista de Diagrama](./media/data-factory-build-your-first-pipeline-using-editor/diagram-view-2.png)
3. Para ver todas as atividades no pipeline, clique com o botão direito do rato no pipeline no diagrama e, em seguida, clique em Abrir Pipeline.

    ![Menu Abrir pipeline](./media/data-factory-build-your-first-pipeline-using-editor/open-pipeline-menu.png)
4. Confirme se vê a atividade HDInsightHive no pipeline.

    ![Vista Abrir pipeline](./media/data-factory-build-your-first-pipeline-using-editor/open-pipeline-view.png)

    Para navegar de volta para a vista anterior, clique em **Fábrica de dados** no menu de navegação, na parte superior.
5. Na **Vista de Diagrama**, faça duplo clique no conjunto de dados **AzureBlobInput**. Verifique se o setor se encontra no estado **Pronto**. Poderá demorar alguns minutos até o setor aparecer no estado Pronto. Se tal não acontecer ao fim de um certo tempo, verifique se colocou o ficheiro de entrada (input.log) no contentor (adfgetstarted) e na pasta (inputdata) adequados.

   ![Setor de entrada no estado pronto](./media/data-factory-build-your-first-pipeline-using-editor/input-slice-ready.png)
6. Clique em **X** para fechar o painel **AzureBlobInput**.
7. Na **Vista de Diagrama**, faça duplo clique no conjunto de dados **AzureBlobOutput**. Verá o setor que está atualmente a ser processado.

   ![Conjunto de dados](./media/data-factory-build-your-first-pipeline-using-editor/dataset-blade.png)
8. Quando o processamento terminar, verá o setor no estado **Pronto**.

   ![Conjunto de dados](./media/data-factory-build-your-first-pipeline-using-editor/dataset-slice-ready.png)  

   > [!IMPORTANT]
   > A criação de um cluster do HDInsight a pedido demora, por norma, algum tempo (cerca de 20 minutos). Assim, prevê-se que o pipeline demore **aproximadamente 30 minutos** a processar o setor.
   >
   >

9. Quando o setor estiver no estado **Pronto**, verifique a pasta **partitioneddata** no contentor **adfgetstarted** do seu armazenamento de blobs para os dados de saída.  

   ![dados de saída](./media/data-factory-build-your-first-pipeline-using-editor/three-ouptut-files.png)
10. Clique no setor para ver os respetivos detalhes no painel **Setor de dados**.

   ![Detalhes do setor de dados](./media/data-factory-build-your-first-pipeline-using-editor/data-slice-details.png)  
11. Clique numa atividade em execução na **Lista de execuções de atividades** para ver detalhes sobre a execução de uma atividade (atividade do Hive no nosso cenário) numa janela **Detalhes de execução da atividade**.   

   ![Detalhes da execução da atividade](./media/data-factory-build-your-first-pipeline-using-editor/activity-window-blade.png)    

   Nos ficheiros de registo, pode ver a consulta do Hive que foi executada e as informações sobre o estado. Estes registos são úteis para resolver eventuais problemas.
   Consulte o artigo [Monitor and manage pipelines using Azure portal blades (Monitorizar e gerir pipelines com os painéis do Portal do Azure)](data-factory-monitor-manage-pipelines.md) para obter mais detalhes.

> [!IMPORTANT]
> O ficheiro de entrada é eliminado quando o setor é processado com êxito. Por conseguinte, se pretender voltar a executar o setor ou repetir o tutorial, carregue o ficheiro de entrada (input.log) na pasta inputdata do contentor adfgetstarted.
>
>

### <a name="monitor-pipeline-using-monitor--manage-app"></a>Monitorizar o pipeline com a Aplicação de Monitorização e Gestão
Pode ainda utilizar a aplicação de Monitorização e Gestão para monitorizar os seus pipelines. Para obter detalhes sobre a utilização desta aplicação, veja [Monitor and manage Azure Data Factory pipelines using Monitoring and Management App (Monitorizar e gerir pipelines do Azure Data Factory com a Aplicação de Monitorização e Gestão)](data-factory-monitor-manage-app.md).

1. Clique no mosaico **Monitorizar e Gerir** na home page do data factory.

    ![Mosaico Monitorizar e Gerir](./media/data-factory-build-your-first-pipeline-using-editor/monitor-and-manage-tile.png)
2. Deverá ver a **Aplicação de Monitorização e Gestão**. Altere a **Hora de início** e a **Hora de fim** para corresponder às horas de início e de fim do seu pipeline e clique em **Aplicar**.

    ![Aplicação Monitorizar e Gerir](./media/data-factory-build-your-first-pipeline-using-editor/monitor-and-manage-app.png)
3. Selecione uma janela de atividade na lista **Janelas de Atividade** para ver os respetivos detalhes.

    ![Detalhes da janela de atividade](./media/data-factory-build-your-first-pipeline-using-editor/activity-window-details.png)

## <a name="summary"></a>Resumo
Neste tutorial, criou uma fábrica de dados do Azure para processar dados executando o Script de ramo de registo num cluster de hadoop do HDInsight. Utilizou o Editor do Data Factory no portal do Azure para realizar os seguintes passos:  

1. Criou uma **fábrica de dados** do Azure.
2. Criar dois **serviços ligados**:
   1. O serviço ligado do **Storage do Azure** para ligar o seu armazenamento de blobs do Azure que contém ficheiros de entrada/saída da fábrica de dados.
   2. O serviço ligado do **Azure HDInsight** a pedido para ligar um cluster de Hadoop do HDInsight a pedido à fábrica de dados. O Azure Data Factory cria um cluster de Hadoop do HDInsight para processar os dados de entrada e produzir dados de saída.
3. Criar dois **conjuntos de dados**, que descrevem os dados de entrada e de saída da atividade do HDInsight Hive no pipeline.
4. Criar um **pipeline** com uma atividade do **Ramo de Registo do HDInsight**.

## <a name="next-steps"></a>Passos Seguintes
Neste artigo, criou um pipeline com uma atividade de transformação (Atividade do HDInsight) que executa um Script de ramo de registo num cluster do HDInsight a pedido. Para ver como utilizar uma Atividade de Cópia para copiar dados de um Blob do Azure para o Azure SQL, veja o [Tutorial: Copiar dados de um Blob do Azure para o Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="see-also"></a>Veja também
| Tópico | Descrição |
|:--- |:--- |
| [Pipelines](data-factory-create-pipelines.md) |Este artigo ajuda-o a compreender os pipelines e as atividades no Azure Data Factory e como os utilizar para construir fluxos de dados ponto a ponto condicionados por dados para o seu cenário ou empresa. |
| [Conjuntos de dados](data-factory-create-datasets.md) |Este artigo ajuda-o a compreender os conjuntos de dados no Azure Data Factory. |
| [Agendamento e execução](data-factory-scheduling-and-execution.md) |Este artigo explica os aspetos de agendamento e execução do modelo da aplicação do Azure Data Factory. |
| [Monitorizar e gerir pipelines com a Aplicação de Monitorização](data-factory-monitor-manage-app.md) |Este artigo descreve como monitorizar, gerir e depurar pipelines com a Aplicação de Monitorização e Gestão. |
