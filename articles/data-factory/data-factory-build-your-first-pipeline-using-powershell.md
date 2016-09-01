<properties
    pageTitle="Criar a primeira fábrica de dados (PowerShell) | Microsoft Azure"
    description="Neste tutorial, irá criar um exemplo de pipeline do Azure Data Factory com o Azure PowerShell."
    services="data-factory"
    documentationCenter=""
    authors="spelluru"
    manager="jhubbard"
    editor="monicar"
/>

<tags
    ms.service="data-factory"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="08/16/2016"
    ms.author="spelluru"/>

# Criar a primeira fábrica de dados do Azure com o Azure PowerShell
> [AZURE.SELECTOR]
- [Descrição Geral do Tutorial](data-factory-build-your-first-pipeline.md)
- [Com o Editor do Data Factory](data-factory-build-your-first-pipeline-using-editor.md)
- [Com o PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Com o Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [Com o Modelo Azure Resource Manager](data-factory-build-your-first-pipeline-using-arm.md)
- [Com a API REST](data-factory-build-your-first-pipeline-using-rest-api.md)


Neste artigo, irá aprender a utilizar o Azure PowerShell para criar a sua primeira fábrica de dados do Azure. 


## Pré-requisitos
Para além dos pré-requisitos listados no tópico Descrição Geral do Tutorial, tem de instalar o seguinte:

- **Deve** ler o artigo [Descrição Geral do Tutorial](data-factory-build-your-first-pipeline.md) e executar todos os passos de pré-requisitos antes de prosseguir.
- **Azure PowerShell**. Siga as instruções no artigo [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](../powershell-install-configure.md) para instalar a versão mais recente do Azure PowerShell no computador.
- (opcional) Este artigo não abrange todos os cmdlets do Data Factory. Veja [Referência dos Cmdlets do Data Factory](https://msdn.microsoft.com/library/dn820234.aspx) para obter a documentação completa sobre os cmdlets do Data Factory. 

Se estiver a utilizar uma **versão inferior às versão 1.0** do Azure PowerShell, terá de utilizar os cmdlets que estão documentados [aqui](https://msdn.microsoft.com/library/azure/dn820234.aspx). Também terá de executar os seguintes comandos antes de utilizar os cmdlets do Data Factory: 
 
1. Inicie o Azure PowerShell e execute os seguintes comandos. Mantenha o Azure PowerShell aberto até ao fim deste tutorial. Se o fechar e reabrir, terá de executar os comandos novamente.
    1. Execute **Add-AzureAccount** e introduza o nome de utilizador e a palavra-passe que utiliza para iniciar sessão no Portal do Azure.
    2. Execute **Get-AzureSubscription** para ver todas as subscrições para esta conta.
    3. Execute **Get AzureRmSubscription - SubscriptionName NameOfAzureSubscription | Set-AzureRmContext** para selecionar a subscrição com a qual pretende trabalhar. Substitua **NameOfAzureSubscription** pelo nome da sua subscrição do Azure.
4. Passe para o modo Azure Resource Manager, uma vez que os cmdlets do Azure Data Factory estão disponíveis neste modo: **Switch-AzureMode AzureResourceManager**.


## Criar fábrica de dados

Neste passo, utiliza o Azure PowerShell para criar um Azure Data Factory com o nome **FirstDataFactoryPSH**. Uma fábrica de dados pode ter um ou mais pipelines. Um pipeline pode conter uma atividade ou mais. Por exemplo, uma Atividade de Cópia para copiar dados de uma origem para um arquivo de dados de destino e uma atividade do Hive do HDInsight para executar o script do Hive para transformar dados de entrada em dados de saída do produto. Comecemos este passo com a criação da fábrica de dados. 

1. Inicie o Azure PowerShell e execute o seguinte comando. Mantenha o Azure PowerShell aberto até ao fim deste tutorial. Se o fechar e reabrir, terá de executar os comandos novamente.
    - Execute **Login-AzureRmAccount** e introduza o nome de utilizador e a palavra-passe que utiliza para iniciar sessão no Portal do Azure.  
    - Execute **Get-AzureRmSubscription** para ver todas as subscrições para esta conta.
    - Execute **Select-AzureRmSubscription<Name of the subscription>** para selecionar a subscrição com a qual pretende trabalhar. Esta subscrição deve ser idêntica à que utilizou no Portal do Azure.
3. Crie um grupo de recursos do Azure com o nome **ADFTutorialResourceGroup** executando o seguinte comando.

        New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

    Alguns dos passos deste tutorial pressupõe que utiliza o grupo de recursos com o nome ADFTutorialResourceGroup. Se utiliza um grupo de recursos diferente, terá de utilizá-lo em vez de ADFTutorialResourceGroup neste tutorial.
4. Execute o cmdlet **New-AzureRmDataFactory** para criar uma fábrica de dados com o nome **FirstDataFactoryPSH**.  

        New-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH –Location "West US"


Tenha em atenção o seguinte:
 
- O nome do Azure Data Factory deve ser globalmente exclusivo. Se receber o erro **Nome "FirstDataFactoryPSH" não disponível para a fábrica de dados**, altere o nome (por exemplo, seunomeFirstDataFactoryPSH). Utilize este nome em vez de ADFTutorialFactoryPSH ao realizar os passos deste tutorial. Veja o tópico [Data Factory – Naming Rules (Data Factory – Regras de Nomenclatura)](data-factory-naming-rules.md) para obter as regras de nomenclatura dos artefactos do Data Factory.
- Para criar instâncias do Data Factory, tem de ser um contribuidor/administrador da subscrição do Azure
- O nome da fábrica de dados pode ser registado como um nome DNS no futuro e, por conseguinte, ficar publicamente visível.
- Se receber o erro: "**Esta subscrição não está registada para utilizar o espaço de nomes Microsoft.DataFactory**", realize um dos seguintes procedimentos e tente publicar novamente: 

    - No Azure PowerShell, execute o seguinte comando para registar o fornecedor do Data Factory. 
        
            Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    
        Pode executar o seguinte comando para confirmar que o fornecedor do Data Factory está registado. 
    
            Get-AzureRmResourceProvider
    - Inicie sessão com a subscrição do Azure no [Portal do Azure](https://portal.azure.com) e navegue até um painel do Data Factory (ou) crie uma fábrica de dados no Portal do Azure. Este procedimento regista automaticamente o fornecedor por si.

Antes de criar um pipeline, deve primeiro criar algumas entidades do Data Factory. Deve criar primeiro os serviços ligados para ligar os arquivos/computações de dados ao seu arquivo de dados, definir os conjuntos de dados de entrada e saída para representar os dados em arquivos de dados ligados e criar o pipeline com uma atividade que utiliza estes conjuntos de dados. 

## Criar serviços ligados 
Neste passo, irá ligar a sua conta de Armazenamento do Azure e um cluster do Azure HDInsight a pedido à sua fábrica de dados. A conta de Armazenamento do Azure possui os dados de entrada e de saída do pipeline neste exemplo. Neste exemplo, o serviço ligado do HDInsight é utilizado para executar o script do Hive especificado na atividade do pipeline. Tem de identificar que dados de armazenamento/serviços de computação são utilizados no seu cenário e ligar esses serviços à fábrica de dados criando serviços ligados.

### Criar o serviço ligado do Storage do Azure
Neste passo, irá ligar a sua conta de Armazenamento do Azure à sua fábrica de dados. No âmbito deste tutorial, pode utilizar a mesma Conta de armazenamento do Azure para guardar os dados de entrada/saída e o ficheiro de script HQL.

1. Crie um ficheiro JSON com o nome StorageLinkedService.json na pasta C:\ADFGetStarted com o seguinte conteúdo. Se ainda não existir, crie a pasta ADFGetStarted.

        {
            "name": "StorageLinkedService",
            "properties": {
                "type": "AzureStorage",
                "description": "",
                "typeProperties": {
                    "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
                }
            }
        }

    Substitua o **nome da conta** pelo nome da sua conta de armazenamento do Azure e a **chave da conta** pela chave de acesso da conta de armazenamento do Azure. Para saber como obter a chave de acesso ao armazenamento, veja [Ver, copiar e regenerar as chaves de acesso ao armazenamento](https://azure.microsoft.com/documentation/articles/storage-create-storage-account/#view-copy-and-regenerate-storage-access-keys).

2. No Azure PowerShell, mude para a pasta ADFGetStarted.
3. Pode utilizar o cmdlet **New-AzureRmDataFactoryLinkedService** para criar um serviço ligado. Este cmdlet e os outros cmdlets do Data Factory que utilizar neste tutorial requerem que passe os valores para os parâmetros *ResourceGroupName* e *DataFactoryName*. Em alternativa, pode utilizar **Get-AzureRmDataFactory** para obter um objeto **DataFactory** e passar o objeto sem precisar escrever *ResourceGroupName* e *DataFactoryName* sempre que executar um cmdlet. Execute o seguinte comando para atribuir a saída do cmdlet **Get-AzureRmDataFactory** a uma variável **$df**.

        $df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH

4. Agora, execute o cmdlet **New-AzureRmDataFactoryLinkedService** para criar o serviço ligado **StorageLinkedService**.

        New-AzureRmDataFactoryLinkedService $df -File .\StorageLinkedService.json

    Se não tiver executado o cmdlet **Get-AzureRmDataFactory** e atribuído a saída à variável **$df**, terá de especificar os valores para os parâmetros *ResourceGroupName* e *DataFactoryName* da seguinte forma.

        New-AzureRmDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName FirstDataFactoryPSH -File .\StorageLinkedService.json

    Se fechar o Azure PowerShell a meio do tutorial, tem de executar o cmdlet **Get-AzureRmDataFactory** da próxima vez que iniciar o Azure PowerShell para concluir o tutorial.

### Criar o serviço ligado do Azure HDInsight
Neste passo, irá ligar um cluster do HDInsight a pedido à sua fábrica de dados. O cluster do HDInsight é criado automaticamente no tempo de execução e eliminado depois de ter sido processado e ficado inativo pelo período de tempo especificado. Também pode utilizar o seu próprio cluster do HDInsight em vez de utilizar um cluster do HDInsight a pedido. Veja [Compute Linked Services (Serviços Ligados de Computação)](data-factory-compute-linked-services.md) para obter detalhes.  

1. Crie um ficheiro JSON com o nome **HDInsightOnDemandLinkedService**.json na pasta **C:\ADFGetStarted** com o seguinte conteúdo.

        {
          "name": "HDInsightOnDemandLinkedService",
          "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
              "version": "3.2",
              "clusterSize": 1,
              "timeToLive": "00:30:00",
              "linkedServiceName": "StorageLinkedService"
            }
          }
        }

    A tabela que se segue fornece descrições para as propriedades JSON utilizadas no fragmento:

  	| Propriedade | Descrição |
  	| :------- | :---------- |
  	| Versão | Esta ação especifica que a versão do HDInsight deve ser a 3.2. | 
  	| ClusterSize | Esta ação cria um cluster do HDInsight com um nó. | 
  	| TimeToLive | Esta ação especifica o tempo de inatividade do cluster do HDInsight, antes de ser eliminado. |
  	| linkedServiceName | Esta ação especifica a conta de armazenamento que é utilizada para armazenar os registos que são gerados pelo HDInsight |

    Tenha em atenção o seguinte: 
    
    - O Data Factory cria um cluster do HDInsight **baseado no Windows** com o JSON acima. Também pode fazer com que crie um cluster do HDInsight **baseado no Linux**. Veja [On-demand HDInsight Linked Service (Serviço Ligado do HDInsight a Pedido)](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obter detalhes. 
    - Também pode utilizar o **seu próprio cluster do HDInsight** em vez de utilizar um cluster do HDInsight a pedido. Veja [HDInsight Linked Service (Serviço Ligado do HDInsight)](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) para obter detalhes.
    - O cluster do HDInsight cria um **contentor predefinido** no armazenamento de blobs especificado no JSON (**linkedServiceName**). Quando o cluster é eliminado, o HDInsight não é eliminado deste contentor. Esta ação é propositada. Com o serviço ligado do HDInsight a pedido, é criado um cluster do HDInsight sempre que um setor tiver de ser processado, exceto se houver um cluster em direto (**timeToLive**) que será eliminado no fim do processamento.
    
        À medida que são processados mais setores, verá muitos contentores no armazenamento de blobs do Azure. Se não precisar deles para a resolução de problemas das tarefas, poderá eliminá-los para reduzir o custo de armazenamento. Os nomes destes contentores seguem um padrão: "adf**nomedafábricadedados**-**nomedoserviçoligado**-carimbodedataehora". Utilize ferramentas como o [Explorador de Armazenamento do Microsoft](http://storageexplorer.com/) para eliminar contentores no armazenamento de blobs do Azure.

    Veja [On-demand HDInsight Linked Service (Serviço Ligado do HDInsight a Pedido)](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obter detalhes. 
2. Execute o cmdlet **New-AzureRmDataFactoryLinkedService** para criar o serviço ligado designado HDInsightOnDemandLinkedService.

        New-AzureRmDataFactoryLinkedService $df -File .\HDInsightOnDemandLinkedService.json


## Criar conjuntos de dados
Neste passo, irá criar conjuntos de dados para representar os dados de entrada e de saída do processamento do Hive. Estes conjuntos de dados referem-se ao **StorageLinkedService** que criou anteriormente neste tutorial. O serviço ligado aponta para uma Conta de armazenamento do Azure e os conjuntos de dados especificam um contentor, uma pasta, um nome de ficheiro no armazenamento que contém os dados de entrada e de saída.   

### Criar conjunto de dados de entrada
1. Crie um ficheiro JSON com o nome **InputTable.json** na pasta **C:\ADFGetStarted** com o seguinte conteúdo:

        {
            "name": "AzureBlobInput",
            "properties": {
                "type": "AzureBlob",
                "linkedServiceName": "StorageLinkedService",
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

    O JSON acima define um conjunto de dados com o nome **AzureBlobInput**, que representa dados de entrada para uma atividade no pipeline. Além disso, especifica que os dados de entrada estão localizados no contentor do blob denominado **adfgetstarted** e na pasta denominada **inputdata**.

    A tabela que se segue fornece descrições para as propriedades JSON utilizadas no fragmento:

  	| Propriedade | Descrição |
  	| :------- | :---------- |
  	| tipo | O tipo de propriedade é definido para AzureBlob, uma vez que os dados estão contidos no armazenamento de blobs do Azure. |  
  	| linkedServiceName | Refere-se ao StorageLinkedService que criou anteriormente. |
  	| fileName | Esta propriedade é opcional. Se omitir esta propriedade, serão escolhidos todos os ficheiros em folderPath. Neste caso, apenas o input.log é processado. |
  	| tipo | Os ficheiros de registo estão no formato de texto, pelo que iremos utilizar TextFormat. | 
  	| columnDelimiter | As colunas nos ficheiros de registo são delimitadas por vírgula (,). |
  	| frequência/intervalo | A frequência definida para o Mês, sendo o intervalo 1, o que significa que os setores de entrada estão disponíveis mensalmente. | 
  	| externo | Esta propriedade é definida como verdadeira se os dados de entrada não forem gerados pelo serviço Data Factory. | 

2. Execute o seguinte comando no Azure PowerShell para criar o conjunto de dados do Data Factory.

        New-AzureRmDataFactoryDataset $df -File .\InputTable.json

### Criar conjunto de dados de saída
Agora, irá criar o conjunto de dados de saída para representar os dados de saída armazenados no armazenamento de blobs do Azure.

1. Crie um ficheiro JSON com o nome **OutputTable.json** na pasta **C:\ADFGetStarted** com o seguinte conteúdo:

        {
          "name": "AzureBlobOutput",
          "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
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

    O JSON acima define um conjunto de dados com o nome **AzureBlobOutput**, que representa dados de saída para uma atividade no pipeline. Além disso, especifica que os dados de saída estão armazenados no contentor do blob denominado **adfgetstarted** e na pasta denominada **partitioneddata**. A secção **disponibilidade** especifica que o conjunto de dados de saída é produzido mensalmente.

2. Execute o seguinte comando no Azure PowerShell para criar o conjunto de dados do Data Factory.

        New-AzureRmDataFactoryDataset $df -File .\OutputTable.json

## Criar pipeline
Neste passo, irá criar o seu primeiro pipeline com uma atividade **HDInsightHive**. O setor de entrada está disponível mensalmente (frequência: Mês, intervalo: 1), o setor de saída é produzido mensalmente e a propriedade do agendador da atividade também está definida para mensal (ver abaixo). As definições para o conjunto de dados de saída e o agendador de atividade têm de corresponder. Atualmente, o conjunto de dados de saída é o que pauta a agenda, pelo que deve criar um conjunto de dados de saída, mesmo que a atividade não produza dados. Se a atividade não incluir entradas, pode ignorar a criação do conjunto de dados de entrada. No final desta secção, encontrará uma explicação sobre as propriedades utilizadas no seguinte JSON. 


1. Crie um ficheiro JSON com o nome MyFirstPipelinePSH.json na pasta C:\ADFGetStarted com o seguinte conteúdo:

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
                            "scriptLinkedService": "StorageLinkedService",
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

    No fragmento JSON, está a criar um pipeline que consiste numa única atividade que utiliza o Hive para processar Dados num cluster do HDInsight.
    
    O ficheiro do script do Hive **partitionweblogs.hql** é armazenado na conta de armazenamento do Azure (especificado pelo scriptLinkedService, denominado **StorageLinkedService**) e na pasta **script** no contentor **adfgetstarted**.

    A secção **define** é utilizada para especificar as definições do tempo de execução que são transmitidas ao script do Hive como valores de configuração do Hive(por exemplo, ${hiveconf:inputtable}, ${hiveconf:partitionedtable}).

    As propriedades **início** e **fim** do pipeline especificam o período ativo do pipeline.

    No JSON de atividade, especifique que o script do Hive é executado na computação especificada pelo **linkedServiceName** – **HDInsightOnDemandLinkedService**.

    > [ACOM.NOTE] Veja [Anatomy of a Pipeline (Anatomia de um Pipeline)](data-factory-create-pipelines.md#anatomy-of-a-pipeline) para obter detalhes sobre as propriedades JSON utilizadas no exemplo acima. 
2.  Confirme se vê o ficheiro **input.log** na pasta **adfgetstarted/inputdata** na armazenamento de blobs do Azure e execute o seguinte comando para implementar o pipeline. Uma vez que as horas de **início** e **fim** são definidas no passado e **isPaused** é definido como falso, o pipeline (atividade no pipeline) é executado imediatamente depois da implementação. 

        New-AzureRmDataFactoryPipeline $df -File .\MyFirstPipelinePSH.json
5. Parabéns, criou com êxito o seu primeiro pipeline com o Azure PowerShell!

## Monitorizar o pipeline
Neste passo, irá utilizar o Azure PowerShell para monitorizar os acontecimentos de uma fábrica de dados do Azure.

1. Execute **Get-AzureRmDataFactory** e atribua o resultado a uma variável **$df**.

        $df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH

2. Execute **Get-AzureRmDataFactorySlice** para obter detalhes sobre todos os setores do **EmpSQLTable**, que é a tabela de saída do pipeline.  

        Get-AzureRmDataFactorySlice $df -DatasetName AzureBlobOutput -StartDateTime 2014-02-01

    Repare que a StartDateTime que especificar aqui é a mesma hora especificada no JSON do pipeline. Deverá ver um resultado semelhante ao seguinte.

        ResourceGroupName : ADFTutorialResourceGroup
        DataFactoryName   : FirstDataFactoryPSH
        DatasetName       : AzureBlobOutput
        Start             : 2/1/2014 12:00:00 AM
        End               : 3/1/2014 12:00:00 AM
        RetryCount        : 0
        State             : InProgress
        SubState          :
        LatencyStatus     :
        LongRetryCount    : 0


3. Execute **Get-AzureRmDataFactoryRun** para obter os detalhes das execuções de um setor específico da atividade.

        Get-AzureRmDataFactoryRun $df -DatasetName AzureBlobOutput -StartDateTime 2014-02-01

    Deverá ver um resultado semelhante ao seguinte.
        
        Id                  : 0f6334f2-d56c-4d48-b427-d4f0fb4ef883_635268096000000000_635292288000000000_AzureBlobOutput
        ResourceGroupName   : ADFTutorialResourceGroup
        DataFactoryName     : FirstDataFactoryPSH
        DatasetName         : AzureBlobOutput
        ProcessingStartTime : 12/18/2015 4:50:33 AM
        ProcessingEndTime   : 12/31/9999 11:59:59 PM
        PercentComplete     : 0
        DataSliceStart      : 2/1/2014 12:00:00 AM
        DataSliceEnd        : 3/1/2014 12:00:00 AM
        Status              : AllocatingResources
        Timestamp           : 12/18/2015 4:50:33 AM
        RetryAttempt        : 0
        Properties          : {}
        ErrorMessage        :
        ActivityName        : RunSampleHiveActivity
        PipelineName        : MyFirstPipeline
        Type                : Script

    Pode continuar a executar este cmdlet até ver o setor no estado **Pronto** ou **Falhou**. Quando estiver no estado Pronto, verifique a pasta **partitioneddata** no contentor **adfgetstarted** do armazenamento de blobs dos dados de saída.  A criação de um cluster do HDInsight a pedido costuma ser um pouco demorada.

    ![dados de saída](./media/data-factory-build-your-first-pipeline-using-powershell/three-ouptut-files.png)


> [AZURE.IMPORTANT] O ficheiro de entrada é eliminado quando o setor é processado com êxito. Por conseguinte, se pretender voltar a executar o setor ou repetir o tutorial, carregue o ficheiro de entrada (input.log) na pasta inputdata do contentor adfgetstarted.

## Resumo 
Neste tutorial, criou uma fábrica de dados do Azure para processar dados executando o script do Hive num cluster de hadoop do HDInsight. Utilizou o Editor do Data Factory no Portal do Azure para realizar os seguintes passos:  

1.  Criou uma **fábrica de dados** do Azure.
2.  Criou dois **serviços ligados**:
    1.  O serviço ligado do ** Storage do Azure** para ligar o seu armazenamento de blobs do Azure que contém ficheiros de entrada/saída da fábrica de dados.
    2.  O serviço ligado do **Azure HDInsight** a pedido para ligar um cluster de Hadoop do HDInsight a pedido à fábrica de dados. O Azure Data Factory cria um cluster de Hadoop do HDInsight para processar os dados de entrada e produzir dados de saída. 
3.  Criou dois **conjuntos de dados** que descrevem os dados de entrada e de saída da atividade do Hive do HDInsight no pipeline. 
4.  Criou um **pipeline** com uma atividade do **Hive do HDInsight**. 

## Passos seguintes
Neste artigo, criou um pipeline com uma atividade de transformação (Atividade do HDInsight) que executa um script do Hive num cluster do Azure HDInsight a pedido. Para ver como utilizar uma Atividade de Cópia para copiar dados de um Blob do Azure para o Azure SQL, veja o [Tutorial: Copiar dados de um Blob do Azure para o Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## Veja também
| Tópico | Descrição |
| :---- | :---- |
| [Referência dos Cmdlets do Data Factory](https://msdn.microsoft.com/library/azure/dn820234.aspx) |  Consulte a documentação abrangente sobre os cmdlets do Data Factory |
| [Atividades de Transformação de Dados](data-factory-data-transformation-activities.md) | Este artigo fornece uma lista de atividades de transformação de dados (por exemplo, a transformação do Hive do HDInsight que utilizou neste tutorial) suportada pelo Azure Data Factory. |
| [Agendamento e Execução](data-factory-scheduling-and-execution.md) | Este artigo explica os aspetos de agendamento e execução do modelo da aplicação do Azure Data Factory. |
| [Pipelines](data-factory-create-pipelines.md) | Este artigo ajuda-o a compreender os pipelines e as atividades no Azure Data Factory e como os utilizar para construir fluxos de dados ponto a ponto condicionados por dados para o seu cenário ou empresa. |
| [Conjuntos de dados](data-factory-create-datasets.md) | Este artigo ajuda-o a compreender os conjuntos de dados no Azure Data Factory.
| [Monitorizar e Gerir Pipelines com os painéis do Portal do Azure](data-factory-monitor-manage-pipelines.md) | Este artigo descreve como monitorizar, gerir e depurar pipelines com os painéis do Portal do Azure. |
| [Monitorizar e gerir pipelines com a Aplicação de Monitorização](data-factory-monitor-manage-app.md) | Este artigo descreve como monitorizar, gerir e depurar pipelines com a Aplicação de Monitorização e Gestão. 




<!--HONumber=ago16_HO4-->


