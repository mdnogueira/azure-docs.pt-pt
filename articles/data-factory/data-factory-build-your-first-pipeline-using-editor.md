<properties
    pageTitle="Criar a primeira fábrica de dados (Portal do Azure) | Microsoft Azure"
    description="Neste tutorial, vai criar um exemplo de pipeline do Azure Data Factory com o Editor do Data Factory no Portal do Azure."
    services="data-factory"
    documentationCenter=""
    authors="spelluru"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="data-factory"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article" 
    ms.date="08/01/2016"
    ms.author="spelluru"/>

# Criar a primeira fábrica de dados do Azure com o Portal do Azure/Editor do Data Factory
> [AZURE.SELECTOR]
- [Descrição Geral do Tutorial](data-factory-build-your-first-pipeline.md)
- [Com o Editor do Data Factory](data-factory-build-your-first-pipeline-using-editor.md)
- [Com o PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Com o Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [Com o Modelo Azure Resource Manager](data-factory-build-your-first-pipeline-using-arm.md)

Neste artigo, vai aprender a utilizar o [Portal do Azure](https://portal.azure.com/) para criar a primeira fábrica de dados do Azure. 

## Pré-requisitos

1. **Deve** ler o artigo [Descrição Geral do Tutorial](data-factory-build-your-first-pipeline.md) e executar todos os passos de pré-requisitos antes de prosseguir.
2. Este artigo não fornece uma descrição geral conceptual do serviço Azure Data Factory. Recomendamos-lhe que leia o artigo [Introduction to Azure Data Factory (Introdução ao Azure Data Factory)](data-factory-introduction.md) para obter uma descrição geral detalhada do serviço.  

## Criar fábrica de dados
Uma fábrica de dados pode ter um ou mais pipelines. Um pipeline pode conter uma atividade ou mais. Por exemplo, uma Atividade de Cópia para copiar dados de uma origem para um arquivo de dados de destino e uma Atividade do ramo de registo do HDInsight para executar o Script de ramo de registo para transformar dados de entrada em dados de saída do produto. Comecemos este passo com a criação da fábrica de dados. 

1.  Depois de ter iniciado sessão no [Portal do Azure](https://portal.azure.com/), execute o seguinte procedimento:
    1.  Clique em **NOVO** no menu da esquerda. 
    2.  Clique em **Análise de dados** no painel **Criar**.
    3.  Clique em **Data Factory** no painel **Análise de dados**.

        ![Criar painel](./media/data-factory-build-your-first-pipeline-using-editor/create-blade.png)

2.  No painel **Nova fábrica de dados**, introduza **GetStartedDF** para o nome.

    ![Painel Nova fábrica de dados](./media/data-factory-build-your-first-pipeline-using-editor/new-data-factory-blade.png)

    > [AZURE.IMPORTANT] O nome do Azure Data Factory deve ser globalmente exclusivo. Se receber o erro: **Nome “GetStartedDF” da fábrica de dados não disponível**, altere o nome (por exemplo, seunomeGetStartedDF) e tente criar novamente. Veja o tópico [Data Factory – Naming Rules (Data Factory – Regras de Nomenclatura)](data-factory-naming-rules.md) para obter as regras de nomenclatura dos artefactos do Data Factory.
    > 
    > O nome da fábrica de dados pode ser registado como um nome DNS no futuro e, por conseguinte, ficar publicamente visível.
    > 
    > Para criar instâncias do Data Factory, tem de ser um contribuidor/administrador da subscrição do Azure


3.  Selecione a **Subscrição do Azure** onde pretende que seja criada a fábrica de dados. 
4.  Selecione o **grupo de recursos** existente ou crie um novo. Para este tutorial, crie um grupo de recursos com o nome **ADFGetStartedRG**.    
5.  Clique em **Criar** no painel **Nova fábrica de dados**.
6.  Verá a fábrica de dados a ser criada no **Startboard** do Portal do Azure da seguinte forma:   

    ![Estado de criação da fábrica de dados](./media/data-factory-build-your-first-pipeline-using-editor/creating-data-factory-image.png)
7. Parabéns! Criou com êxito a sua primeira fábrica de dados. Depois de ter criado a fábrica de dados com êxito, verá a página da fábrica de dados e o respetivo conteúdo.  

    ![Painel Data Factory](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-blade.png)

Antes de criar um pipeline, deve primeiro criar algumas entidades do Data Factory. Deve criar primeiro os serviços ligados para ligar os arquivos/computações de dados ao seu arquivo de dados, definir os conjuntos de dados de entrada e saída para representar os dados em arquivos de dados ligados e criar o pipeline com uma atividade que utiliza estes conjuntos de dados. 

## Criar serviços ligados
Neste passo, vai ligar a sua Conta de armazenamento do Azure e um cluster do Azure HDInsight a pedido à fábrica de dados. Neste exemplo, a Conta de armazenamento do Azure armazena os dados de entrada e de saída do pipeline. Neste exemplo, o serviço ligado do HDInsight é utilizado para executar o Script de ramo de registo especificado na atividade do pipeline. Tem de identificar que dados de armazenamento/serviços de computação são utilizados no seu cenário e ligar esses serviços à fábrica de dados criando serviços ligados.  

### Criar o serviço ligado do Storage do Azure
Neste passo, vai ligar a sua Conta de armazenamento do Azure à fábrica de dados. No âmbito deste tutorial, pode utilizar a mesma Conta de armazenamento do Azure para guardar os dados de entrada/saída e o ficheiro de script HQL. 

1.  Clique em **Criar e implementar** no painel **DATA FACTORY** para **GetStartedDF**. Esta ação inicia o Editor do Data Factory. 
     
    ![Mosaico Criar e implementar](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-author-deploy.png)
2.  Clique em **Novo arquivo de dados** e escolha **Armazenamento do Azure**.
    
    ![Serviço ligado do Storage do Azure](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

    Deverá ver o script JSON para criar um serviço ligado do Storage do Azure no editor. 
4. Substitua o **nome da conta** pelo nome da sua conta de armazenamento do Azure e a **chave da conta** pela chave de acesso da conta de armazenamento do Azure. Para saber como obter a chave de acesso ao armazenamento, veja [Ver, copiar e regenerar as chaves de acesso ao armazenamento](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
5. Clique em **Implementar** na barra de comandos para implementar o serviço ligado.

    ![Botão Implementar](./media/data-factory-build-your-first-pipeline-using-editor/deploy-button.png)

   Depois de implementar o serviço ligado com êxito, a janela **Rascunho-1** deve desaparecer e verá **AzureStorageLinkedService** na vista de árvore à esquerda. 
    ![Serviço Ligado do Storage no menu](./media/data-factory-build-your-first-pipeline-using-editor/StorageLinkedServiceInTree.png)   

 
### Criar o serviço ligado do Azure HDInsight
Neste passo, vai ligar um cluster do HDInsight a pedido à fábrica de dados. O cluster do HDInsight é criado automaticamente no tempo de execução e eliminado depois de ter sido processado e ficado inativo pelo período de tempo especificado. 

1. No **Editor do Data Factory**, clique em **Nova computação** na barra de comandos e selecione **Cluster do HDInsight a pedido**.

    ![Nova computação](./media/data-factory-build-your-first-pipeline-using-editor/new-compute-menu.png)
2. Copie e cole o fragmento abaixo na janela **Rascunho-1**. O fragmento JSON descreve as propriedades que serão utilizadas para criar o cluster do HDInsight a pedido. 

        {
          "name": "HDInsightOnDemandLinkedService",
          "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
              "version": "3.2",
              "clusterSize": 1,
              "timeToLive": "00:30:00",
              "linkedServiceName": "AzureStorageLinkedService"
            }
          }
        }
    
    A tabela que se segue fornece descrições para as propriedades JSON utilizadas no fragmento:
    
  	| Propriedade | Descrição |
  	| :------- | :---------- |
  	| Versão | Esta ação especifica que a versão do HDInsight deve ser a 3.2. | 
  	| ClusterSize | Esta ação cria um cluster do HDInsight com um nó. | 
  	| TimeToLive | Esta ação especifica o tempo de inatividade do cluster do HDInsight, antes de ser eliminado. |
  	| linkedServiceName | Esta ação especifica a conta de armazenamento que será utilizada para armazenar os registos que são gerados pelo HDInsight. |

    Tenha em atenção o seguinte: 
    
    - O Data Factory cria um cluster do HDInsight **baseado no Windows** com o JSON acima. Também pode fazer com que crie um cluster do HDInsight **baseado no Linux**. Veja [On-demand HDInsight Linked Service (Serviço Ligado do HDInsight a Pedido)](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obter detalhes. 
    - Também pode utilizar o **seu próprio cluster do HDInsight** em vez de utilizar um cluster do HDInsight a pedido. Veja [HDInsight Linked Service (Serviço Ligado do HDInsight)](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) para obter detalhes.
    - O cluster do HDInsight cria um **contentor predefinido** no armazenamento de blobs especificado no JSON (**linkedServiceName**). Quando o cluster é eliminado, o HDInsight não é eliminado deste contentor. Esta ação é propositada. Com o serviço ligado do HDInsight a pedido, é criado um cluster do HDInsight sempre que um setor tiver de ser processado, exceto se houver um cluster em direto (**timeToLive**) que será eliminado no fim do processamento.
    
        À medida que são processados cada vez mais setores, verá muitos contentores no armazenamento de blobs do Azure. Se não precisar deles para a resolução de problemas das tarefas, poderá eliminá-los para reduzir o custo de armazenamento. O nome destes contentores segue um padrão: “adf**nomedafábricadedados**-**nomedoserviçoligado**-datetimestamp”. Utilize ferramentas como o [Explorador de Armazenamento do Microsoft](http://storageexplorer.com/) para eliminar contentores no armazenamento de blobs do Azure.

    Veja [On-demand HDInsight Linked Service (Serviço Ligado do HDInsight a Pedido)](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obter detalhes.
3. Clique em **Implementar** na barra de comandos para implementar o serviço ligado. 
4. Confirme se vê **AzureStorageLinkedService** e **HDInsightOnDemandLinkedService** na vista de árvore à esquerda.

    ![Vista de árvore com serviços ligados](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-linked-services.png)

## Criar conjuntos de dados
Neste passo, vai criar conjuntos de dados para representar os dados de entrada e de saída do processamento do Ramo de Registo. Estes conjuntos de dados referem-se ao **AzureStorageLinkedService** que criou anteriormente neste tutorial. O serviço ligado aponta para uma Conta de armazenamento do Azure e os conjuntos de dados especificam um contentor, uma pasta, um nome de ficheiro no armazenamento que contém os dados de entrada e de saída.   

### Criar conjunto de dados de entrada

1. No **Editor do Data Factory**, clique em **Novo conjunto de dados** na barra de comandos e selecione **Blob Storage do Azure**.

    ![Novo conjunto de dados](./media/data-factory-build-your-first-pipeline-using-editor/new-data-set.png)
2. Copie e cole o fragmento abaixo na janela Rascunho-1. No fragmento JSON, está a criar um conjunto de dados denominado **AzureBlobInput**, que representa os dados de entrada de uma atividade no pipeline. Além disso, especifica que os dados de entrada estão localizados no contentor do blob denominado **adfgetstarted** e na pasta denominada **inputdata**.
        
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

    A tabela que se segue fornece descrições para as propriedades JSON utilizadas no fragmento:

  	| Propriedade | Descrição |
  	| :------- | :---------- |
  	| tipo | O tipo de propriedade é definido para AzureBlob, uma vez que os dados estão contidos no armazenamento de blobs do Azure. |  
  	| linkedServiceName | Refere-se ao AzureStorageLinkedService que criou anteriormente. |
  	| fileName | Esta propriedade é opcional. Se omitir esta propriedade, serão escolhidos todos os ficheiros em folderPath. Neste caso, apenas o input.log é processado. |
  	| tipo | Os ficheiros de registo estão no formato de texto, pelo que iremos utilizar TextFormat. | 
  	| columnDelimiter | As colunas nos ficheiros de registo são delimitadas por , (vírgula) |
  	| frequência/intervalo | A frequência definida para o Mês, sendo o intervalo 1, o que significa que os setores de entrada estão disponíveis mensalmente. | 
  	| externo | Esta propriedade é definida como verdadeira se os dados de entrada não forem gerados pelo serviço Data Factory. | 
      
    
3. Clique em **Implementar** na barra de comandos para implementar o conjunto de dados recentemente criado. Deverá ver o conjunto de dados na vista de árvore à esquerda. 


### Criar conjunto de dados de saída
Agora, vai criar o conjunto de dados de saída para representar os dados de saída armazenados no armazenamento de blobs do Azure. 

1. No **Editor do Data Factory**, clique em **Novo conjunto de dados** na barra de comandos e selecione **Blob Storage do Azure**.  
2. Copie e cole o fragmento abaixo na janela Rascunho-1. No fragmento JSON, está a criar um conjunto de dados denominado **AzureBlobOutput** e a especificar a estrutura dos dados que serão produzidos pelo Script de ramo de registo. Além disso, especifica que os resultados são armazenados no contentor do blob denominado **adfgetstarted** e na pasta denominada **partitioneddata**. A secção **disponibilidade** especifica que o conjunto de dados de saída é produzido mensalmente.
    
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

    Veja a secção **Criar o conjunto de dados de entrada** para obter as descrições destas propriedades. Não defina a propriedade externa num conjunto de dados de saída, uma vez que o conjunto de dados é produzido pelo serviço Data Factory.
3. Clique em **Implementar** na barra de comandos para implementar o conjunto de dados recentemente criado.
4. Verifique se o conjunto de dados foi criado com êxito.

    ![Vista de árvore com serviços ligados](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-data-set.png)

## Criar pipeline
Neste passo, vai criar o seu primeiro pipeline com uma atividade **HDInsightHive**. Tenha em atenção que o setor de entrada está disponível mensalmente (frequência: Mês, intervalo: 1), o setor de saída é produzido mensalmente e a propriedade do agendador da atividade também está definida para mensal (ver abaixo). As definições para o conjunto de dados de saída e o agendador de atividade têm de corresponder. Neste momento, o conjunto de dados de saída é o que pauta a agenda, pelo que deve criar um conjunto de dados de saída, mesmo se a atividade não produzir dados. Se a atividade não incluir entradas, pode ignorar a criação do conjunto de dados de entrada. No final desta secção, encontrará uma explicação sobre as propriedades utilizadas no seguinte JSON. 

1. No **Editor do Data Factory**, clique em **Reticências (…) Mais comandos** e, em seguida, clique em **Novo pipeline**.
    
    ![botão Novo pipeline](./media/data-factory-build-your-first-pipeline-using-editor/new-pipeline-button.png)
2. Copie e cole o fragmento abaixo na janela Rascunho-1.

    > [AZURE.IMPORTANT] Substitua **storageaccountname** pelo nome da sua conta de armazenamento no JSON.
        
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
                "start": "2016-04-01T00:00:00Z",
                "end": "2016-04-02T00:00:00Z",
                "isPaused": false
            }
        }
 
    No fragmento JSON, está a criar um pipeline que consiste numa única atividade que utiliza o Ramo de Registo para processar Dados num cluster do HDInsight.
    
    O ficheiro do Script de ramo de registo **partitionweblogs.hql** é armazenado na conta de armazenamento do Azure (especificada pelo scriptLinkedService, denominado **AzureStorageLinkedService**) e na pasta **script** no contentor **adfgetstarted**.

    A secção **define** é utilizada para especificar as definições do tempo de execução que serão transmitidas ao Script de ramo de registo como valores de configuração do Ramo de Registo (por exemplo, ${hiveconf:inputtable}, ${hiveconf:partitionedtable}).

    As propriedades **início** e **fim** do pipeline especificam o período ativo do pipeline.

    No JSON de atividade, especifique que o Script de ramo de registo é executado na computação especificada pelo **linkedServiceName** – **HDInsightOnDemandLinkedService**.

    > [AZURE.NOTE] Veja [Anatomy of a Pipeline (Anatomia de um Pipeline)](data-factory-create-pipelines.md#anatomy-of-a-pipeline) para obter detalhes sobre as propriedades JSON utilizadas no exemplo acima. 

3. Confirme se: 
    1. O ficheiro **input.log** existe na pasta **inputdata** do contentor **adfgetstarted** no armazenamento de blobs do Azure
    2. O ficheiro **partitionweblogs.hql** existe na pasta **script** do contentor **adfgetstarted** no armazenamento de blobs do Azure. Se não vir estes ficheiros, deverá concluir os passos dos pré-requisitos apresentados na [Descrição geral do Tutorial](data-factory-build-your-first-pipeline.md). 
    3. Confirme se substituiu **storageaccountname** pelo nome da sua conta de armazenamento no pipeline JSON. 
2. Clique em **Implementar** na barra de comandos para implementar o pipeline. Uma vez que as horas de **início** e **fim** são definidas no passado e **isPaused** é definido como falso, o pipeline (atividade no pipeline) é executado imediatamente depois da implementação. 
4. Verifique se vê o pipeline na vista de árvore.

    ![Vista de árvore com o pipeline](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-pipeline.png)
5. Parabéns, criou com êxito o seu primeiro pipeline!

## Monitorizar o pipeline

6. Clique em **X** para fechar os painéis do Editor do Data Factory e para regressar ao painel Data Factory e, em seguida, clique em **Diagrama**.
  
    ![Mosaico do diagrama](./media/data-factory-build-your-first-pipeline-using-editor/diagram-tile.png)
7. Na Vista de Diagrama, verá uma descrição geral dos pipelines e dos conjuntos de dados utilizados neste tutorial.
    
    ![Vista de Diagrama](./media/data-factory-build-your-first-pipeline-using-editor/diagram-view-2.png) 
8. Para ver todas as atividades no pipeline, clique com o botão direito do rato no pipeline no diagrama e, em seguida, clique em Abrir Pipeline. 

    ![Menu Abrir pipeline](./media/data-factory-build-your-first-pipeline-using-editor/open-pipeline-menu.png)
9. Confirme se vê a atividade HDInsightHive no pipeline. 
  
    ![Vista Abrir pipeline](./media/data-factory-build-your-first-pipeline-using-editor/open-pipeline-view.png)

    Para navegar de volta para a vista anterior, clique em **Fábrica de dados** no menu de navegação, na parte superior. 
10. Na **Vista de Diagrama**, faça duplo clique no conjunto de dados **AzureBlobInput**. Verifique se o setor se encontra no estado **Pronto**. Poderá demorar alguns minutos até o setor aparecer no estado Pronto. Se tal não acontecer ao fim de um certo tempo, verifique se colocou o ficheiro de entrada (input.log) no contentor (adfgetstarted) e na pasta (inputdata) adequados.

    ![Setor de entrada no estado pronto](./media/data-factory-build-your-first-pipeline-using-editor/input-slice-ready.png)
11. Clique em **X** para fechar o painel **AzureBlobInput**. 
12. Na **Vista de Diagrama**, faça duplo clique no conjunto de dados **AzureBlobOutput**. Verá o setor que está atualmente a ser processado.

    ![Conjunto de dados](./media/data-factory-build-your-first-pipeline-using-editor/dataset-blade.png)
9. Quando o processamento terminar, verá o setor no estado **Pronto**.
    >[AZURE.IMPORTANT] A criação de um cluster do HDInsight a pedido demora, por norma, algum tempo (cerca de 20 minutos).  

    ![Conjunto de dados](./media/data-factory-build-your-first-pipeline-using-editor/dataset-slice-ready.png) 
    
10. Quando o setor estiver no estado **Pronto**, verifique a pasta **partitioneddata** no contentor **adfgetstarted** do seu armazenamento de blobs para os dados de saída.  
 
    ![dados de saída](./media/data-factory-build-your-first-pipeline-using-editor/three-ouptut-files.png)

Consulte o artigo [Monitor and manage pipelines using Azure portal blades (Monitorizar e gerir pipelines com os painéis do Portal do Azure)](data-factory-monitor-manage-pipelines.md) para obter mais detalhes. 

> [AZURE.IMPORTANT] O ficheiro de entrada é eliminado quando o setor é processado com êxito. Por conseguinte, se pretender voltar a executar o setor ou repetir o tutorial, carregue o ficheiro de entrada (input.log) na pasta inputdata do contentor adfgetstarted.

## Resumo 
Neste tutorial, criou uma fábrica de dados do Azure para processar dados executando o Script de ramo de registo num cluster de hadoop do HDInsight. Utilizou o Editor do Data Factory no Portal do Azure para realizar os seguintes passos:  

1.  Criar uma **fábrica de dados** do Azure.
2.  Criar dois **serviços ligados**:
    1.  O serviço ligado do ** Storage do Azure** para ligar o seu armazenamento de blobs do Azure que contém ficheiros de entrada/saída da fábrica de dados.
    2.  O serviço ligado do **Azure HDInsight** a pedido para ligar um cluster de Hadoop do HDInsight a pedido à fábrica de dados. O Azure Data Factory cria um cluster de Hadoop do HDInsight para processar os dados de entrada e produzir dados de saída. 
3.  Criar dois **conjuntos de dados** que descrevem os dados de entrada e de saída da Atividade do ramo de registo do HDInsight no pipeline. 
4.  Criar um **pipeline** com uma atividade do **Ramo de Registo do HDInsight**. 

## Passos Seguintes
Neste artigo, criou um pipeline com uma atividade de transformação (Atividade do HDInsight) que executa um Script de ramo de registo num cluster do HDInsight a pedido. Para ver como utilizar uma Atividade de Cópia para copiar dados de um Blob do Azure para o Azure SQL, veja o [Tutorial: Copiar dados de um Blob do Azure para o Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## Veja também
| Tópico | Descrição |
| :---- | :---- |
| [Atividades de Transformação de Dados](data-factory-data-transformation-activities.md) | Este artigo fornece uma lista de atividades de transformação de dados (por exemplo, a transformação do Ramo de registo do HDInsight que utilizou neste tutorial) suportada pelo Azure Data Factory. | 
| [Agendamento e execução](data-factory-scheduling-and-execution.md) | Este artigo explica os aspetos de agendamento e execução do modelo da aplicação do Azure Data Factory. |
| [Pipelines](data-factory-create-pipelines.md) | Este artigo ajuda-o a compreender os pipelines e as atividades no Azure Data Factory e como tirar partido dos mesmos para construir fluxos de dados ponto a ponto condicionados por dados para o seu cenário ou empresa. |
| [Conjuntos de dados](data-factory-create-datasets.md) | Este artigo ajuda-o a compreender os conjuntos de dados no Azure Data Factory.
| [Monitorizar e gerir pipelines com a Aplicação de Monitorização](data-factory-monitor-manage-app.md) | Este artigo descreve como monitorizar, gerir e depurar pipelines com a Aplicação de Monitorização e Gestão. 

  




<!--HONumber=Aug16_HO1-->


