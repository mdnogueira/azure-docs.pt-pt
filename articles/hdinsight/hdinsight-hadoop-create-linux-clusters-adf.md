---
title: Criar clusters do Hadoop a pedido utilizando o Data Factory - Azure HDInsight | Microsoft Docs
description: Saiba como criar clusters do Hadoop a pedido no HDInsight com o Azure Data Factory.
services: hdinsight
documentationcenter: 
tags: azure-portal
author: spelluru
manager: jhubbard
editor: cgronlun
ms.assetid: 1f3b3a78-4d16-4d99-ba6e-06f7bb185d6a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/20/2017
ms.author: spelluru
ms.openlocfilehash: b9b73f6691af957e42236ef9a223411a0296f96f
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="create-on-demand-hadoop-clusters-in-hdinsight-using-azure-data-factory"></a>Criar clusters do Hadoop a pedido no HDInsight com o Azure Data Factory
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

[O Azure Data Factory](../data-factory/introduction.md) é um serviço de integração de dados baseado na nuvem que orquestra e automatiza o movimento e a transformação de dados. Pode criar um Hadoop do HDInsight cluster just-in-time para processar um setor de dados de entrada e eliminar o cluster quando o processamento é concluído. Algumas das vantagens da utilização de um cluster de Hadoop do HDInsight a pedido são:

- Apenas pagamento para a tarefa de tempo está em execução no cluster do HDInsight Hadoop (mais um breve tempo de inatividade configurável). A faturação para clusters do HDInsight é calculada por minuto, quer esteja a utilizá-los ou não. Quando utiliza um serviço de ligado de HDInsight a pedido na fábrica de dados, os clusters são criados a pedido. E os clusters são eliminados automaticamente quando as tarefas estão concluídas. Por conseguinte, apenas paga a tarefa com o tempo e o tempo de inatividade breve (definição de time-to-live).
- Pode criar um fluxo de trabalho com um pipeline do Data Factory. Por exemplo, pode ter o pipeline para copiar dados de um servidor de SQL no local para um armazenamento de Blobs do Azure, processar os dados ao executar um script de ramo de registo e um script Pig num cluster do Hadoop do HDInsight a pedido. Em seguida, copie os dados de resultado para um armazém de dados SQL do Azure para aplicações de BI consumir.
- Pode agendar o fluxo de trabalho é executada periodicamente (hora a hora, diariamente, semanalmente, mensalmente, etc.).

No Azure Data Factory, uma fábrica de dados pode ter um ou mais pipelines de dados. Um pipeline de dados tem uma ou mais atividades. Existem dois tipos de atividades: [atividades de movimentos de dados](../data-factory/copy-activity-overview.md) e [atividades de transformação de dados](../data-factory/transform-data.md). Utilize atividades de movimentos de dados (atualmente, apenas atividade de cópia) para mover dados de um arquivo de dados de origem para um arquivo de dados de destino. Utilize atividades de transformação de dados para a transformação/processar dados. Atividade do ramo de registo do HDInsight é uma das atividades de transformação suportadas pela fábrica de dados. Utilize a atividade de transformação do ramo de registo neste tutorial.

Pode configurar uma atividade do ramo de registo para utilizar o seu próprio cluster do HDInsight Hadoop ou um cluster de Hadoop do HDInsight a pedido. Neste tutorial, a atividade do ramo de registo no pipeline de fábrica de dados está configurada para utilizar um cluster do HDInsight a pedido. Por conseguinte, quando a atividade é executada para processar um setor de dados, eis o que acontece:

1. Um cluster de Hadoop do HDInsight é criado automaticamente para just-in-time processar o setor.  
2. Os dados de entrada são processados, executando um script de HiveQL no cluster.
3. O cluster de Hadoop do HDInsight é eliminado após o processamento é concluído e o cluster estiver inativo durante o período de tempo (definição de timeToLive) configurado. Se o setor de dados seguinte está disponível para o processamento no tempo de inatividade deste timeToLive, mesmo cluster é utilizado para processar o setor.  

Neste tutorial, o script de HiveQL associado com a atividade do ramo de registo efetua as seguintes ações:

1. Cria uma tabela externa que faça referência a dados de registo em bruto web armazenados no armazenamento de Blobs do Azure.
2. Os dados não processados por ano e mês de partições.
3. Armazena os dados particionados no blob storage do Azure.

Neste tutorial, o script de HiveQL associado com a atividade do ramo de registo cria uma tabela externa que faça referência os dados de registo em bruto web armazenados no armazenamento de Blobs do Azure. Seguem-se as linhas de exemplo para cada mês no ficheiro de entrada.

```
2014-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871
2014-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
2014-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
```

O script de HiveQL particiona os dados não processados por ano e mês. Cria três saída pastas com base na entrada anterior. Cada pasta contém um ficheiro com as entradas de cada mês.

```
adfgetstarted/partitioneddata/year=2014/month=1/000000_0
adfgetstarted/partitioneddata/year=2014/month=2/000000_0
adfgetstarted/partitioneddata/year=2014/month=3/000000_0
```

Para obter uma lista de atividades de transformação de dados do Data Factory para além de atividade do ramo de registo, consulte [transformar e analisar utilizando o Azure Data Factory](../data-factory/transform-data.md).

> [!NOTE]
> Atualmente, só pode criar clusters do HDInsight versão 3.2 do Azure Data Factory.

## <a name="prerequisites"></a>Pré-requisitos
Antes de iniciar as instruções neste artigo, tem de ter os seguintes itens:

* [Subscrição do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Azure PowerShell.

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell.md)]

### <a name="prepare-storage-account"></a>Preparar a conta de armazenamento
Pode utilizar até três contas de armazenamento neste cenário:

- conta do storage predefinida para o cluster do HDInsight
- conta de armazenamento para os dados de entrada
- conta de armazenamento para os dados de saída

Para simplificar o tutorial, pode utilizar uma conta de armazenamento para servir três efeitos. O script de exemplo do Azure PowerShell encontrado nesta secção efetua as seguintes tarefas:

1. Inicie sessão no Azure.
2. Criar um grupo de recursos do Azure.
3. Crie uma conta de armazenamento do Azure.
4. Criar um contentor de BLOBs na conta de armazenamento
5. Copie os seguintes dois ficheiros para o contentor do Blob:

   * Ficheiro de dados de entrada: [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log)
   * Script de HiveQL: [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql)

     Ambos os ficheiros são armazenados num contentor de Blob público.


**Para preparar o armazenamento e copiar os ficheiros com o Azure PowerShell:**
> [!IMPORTANT]
> Especifique nomes para o grupo de recursos do Azure e a conta de armazenamento do Azure que será criada pelo script.
> Anote **nome do grupo de recursos**, **nome da conta de armazenamento**, e **chave da conta de armazenamento** debitado pelo script. Tem a secção seguinte.

```powershell
$resourceGroupName = "<Azure Resource Group Name>"
$storageAccountName = "<Azure Storage Account Name>"
$location = "East US 2"

$sourceStorageAccountName = "hditutorialdata"  
$sourceContainerName = "adfhiveactivity"

$destStorageAccountName = $storageAccountName
$destContainerName = "adfgetstarted" # don't change this value.

####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
try{Get-AzureRmContext}
catch{Login-AzureRmAccount}
#endregion

####################################
# Create a resource group, storage, and container
####################################

#region - create Azure resources
Write-Host "`nCreating resource group, storage account and blob container ..." -ForegroundColor Green

New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
New-AzureRmStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName `
    -type Standard_LRS `
    -Location $location

$destStorageAccountKey = (Get-AzureRmStorageAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName)[0].Value

$sourceContext = New-AzureStorageContext `
    -StorageAccountName $sourceStorageAccountName `
    -Anonymous
$destContext = New-AzureStorageContext `
    -StorageAccountName $destStorageAccountName `
    -StorageAccountKey $destStorageAccountKey

New-AzureStorageContainer -Name $destContainerName -Context $destContext
#endregion

####################################
# Copy files
####################################
#region - copy files
Write-Host "`nCopying files ..." -ForegroundColor Green

$blobs = Get-AzureStorageBlob `
    -Context $sourceContext `
    -Container $sourceContainerName

$blobs|Start-AzureStorageBlobCopy `
    -DestContext $destContext `
    -DestContainer $destContainerName

Write-Host "`nCopied files ..." -ForegroundColor Green
Get-AzureStorageBlob -Context $destContext -Container $destContainerName
#endregion

Write-host "`nYou will use the following values:" -ForegroundColor Green
write-host "`nResource group name: $resourceGroupName"
Write-host "Storage Account Name: $destStorageAccountName"
write-host "Storage Account Key: $destStorageAccountKey"

Write-host "`nScript completed" -ForegroundColor Green
```

Se precisar de ajuda com o script do PowerShell, consulte [utilizando o Azure PowerShell com o Storage do Azure](../storage/common/storage-powershell-guide-full.md). Se pretende utilizar a CLI do Azure em vez disso, consulte o [apêndice](#appendix) secção para o script da CLI do Azure.

**Para examinar a conta de armazenamento e o conteúdo**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Clique em **grupos de recursos** no painel esquerdo.
3. Faça duplo clique o nome do grupo de recursos que criou no seu script do PowerShell. Utilize o filtro se tem demasiados grupos de recursos listados.
4. No **recursos** mosaico, deverá ter um recurso listado, a menos que partilhar o grupo de recursos com outros projetos. Esse recurso é a conta de armazenamento com o nome que especificou anteriormente. Clique no nome de conta de armazenamento.
5. Clique em de **Blobs** mosaicos.
6. Clique em de **adfgetstarted** contentor. Pode ver duas pastas: **inputdata** e **script**.
7. Abra a pasta e verifique os ficheiros nas pastas. O inputdata contém o ficheiro de input.log com dados de entrada e a pasta de script contém o ficheiro de script de HiveQL.

## <a name="create-a-data-factory-using-resource-manager-template"></a>Criar uma fábrica de dados utilizando o modelo do Resource Manager
A conta de armazenamento, os dados de entrada e o script de HiveQL preparado, está pronto para criar um Azure data factory. Existem vários métodos para criar a fábrica de dados. Neste tutorial, pode criar uma fábrica de dados ao implementar um modelo Azure Resource Manager no portal do Azure. Também pode implementar um modelo do Resource Manager, utilizando [CLI do Azure](../azure-resource-manager/resource-group-template-deploy-cli.md) e [Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md#deploy-local-template). Para outros métodos de criação do factory de dados, consulte [Tutorial: criar a primeira fábrica de dados](../data-factory/quickstart-create-data-factory-dot-net.md).

1. Clique na imagem seguinte para iniciar sessão no Azure e abrir o modelo do Azure Resource Manager no Portal do Azure. O modelo está localizado em https://hditutorialdata.blob.core.windows.net/adfhiveactivity/data-factory-hdinsight-on-demand.json. Consulte o [entidades do Data Factory no modelo](#data-factory-entities-in-the-template) secção para obter informações detalhadas sobre entidades definida no modelo. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fadfhiveactivity%2Fdata-factory-hdinsight-on-demand.json" target="_blank"><img src="./media/hdinsight-hadoop-create-linux-clusters-adf/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. Selecione **utilização existente** opção para o **grupo de recursos** definição e selecione o nome do grupo de recursos que criou no passo anterior (utilizando o script do PowerShell).
3. Introduza um nome para a fábrica de dados (**nome da fábrica de dados**). Este nome tem de ser globalmente exclusivo.
4. Introduza o **nome da conta de armazenamento** e **chave da conta de armazenamento** que escreveu para baixo no passo anterior.
5. Selecione **concordo com os termos e condições** declarados acima depois de ler **termos e condições**.
6. Selecione **afixar ao dashboard** opção.
6. Clique em **compra/criar**. Verá um mosaico no Dashboard chamado **implementação do modelo de implementação**. Aguarde até que o **grupo de recursos** abre painel para o grupo de recursos. Também pode clicar no mosaico intitulado como o nome do grupo de recursos para abrir o painel do grupo de recursos.
6. Clique no mosaico para abrir o grupo de recursos, se o painel do grupo de recursos não esteja ainda aberto. Agora deverá ver um recurso de fábrica de dados mais listado além o recurso de conta de armazenamento.
7. Clique no nome da fábrica de dados (valor que especificou para o **nome da fábrica de dados** parâmetro).
8. No painel Data Factory, clique o **diagrama** mosaico. O diagrama mostra uma atividade com um conjunto de dados de entrada e um conjunto de dados de saída:

    ![Azure Data Factory HDInsight a pedido do Hive atividade pipeline diagrama](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-pipeline-diagram.png)

    Os nomes são definidos no modelo do Resource Manager.
9. Faça duplo clique em **AzureBlobOutput**.
10. No **Recent atualizado setores**, deverá ver um setor. Se o estado for **em curso**, aguarde até que este é alterado para **pronto**. Normalmente, demora **20 minutos** para criar um cluster do HDInsight.

### <a name="check-the-data-factory-output"></a>Verifique a saída da fábrica de dados

1. Utilize o mesmo procedimento a última sessão para verificar os contentores do contentor adfgetstarted. Existem dois novos contentores além **adfgetsarted**:

   * Um contentor com o nome que se segue o padrão: `adf<yourdatafactoryname>-linkedservicename-datetimestamp`. Este contentor é o contentor predefinido para o cluster do HDInsight.
   * adfjobs: este contentor é o contentor para os registos de tarefa do ADF.

     A saída da fábrica de dados é armazenada em **afgetstarted** conforme configurado no modelo do Resource Manager.
2. Clique em **adfgetstarted**.
3. Faça duplo clique em **partitioneddata**. Verá um **ano = 2014** pasta porque todos os registos de web são com a data no ano de 2014.

    ![Azure Data Factory HDInsight a pedido do Hive pipeline saída da atividade](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-output-year.png)

    Se desagregar a lista, deverá ver três pastas para Janeiro, Fevereiro e Março. E não existe um registo de cada mês.

    ![Azure Data Factory HDInsight a pedido do Hive pipeline saída da atividade](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-output-month.png)

## <a name="data-factory-entities-in-the-template"></a>Entidades do Data Factory no modelo
Eis como o modelo do Resource Manager nível superior para uma fábrica de dados aspeto:

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": { ...
    },
    "variables": { ...
    },
    "resources": [
        {
            "name": "[parameters('dataFactoryName')]",
            "apiVersion": "[variables('apiVersion')]",
            "type": "Microsoft.DataFactory/datafactories",
            "location": "westus",
            "resources": [
                { ... },
                { ... },
                { ... },
                { ... }
            ]
        }
    ]
}
```

### <a name="define-data-factory"></a>Definir fábrica de dados
Defina uma fábrica de dados no modelo do Resource Manager, conforme mostrado no exemplo seguinte:  

```json
"resources": [
{
    "name": "[parameters('dataFactoryName')]",
    "apiVersion": "[variables('apiVersion')]",
    "type": "Microsoft.DataFactory/datafactories",
    "location": "westus",
}
```
O dataFactoryName é o nome da fábrica de dados que especificou ao implementar o modelo. Fábrica de dados é atualmente suportado apenas nas regiões EUA leste, EUA oeste e Europa do Norte.

### <a name="defining-entities-within-the-data-factory"></a>Definir as entidades numa fábrica de dados
As seguintes entidades do Data Factory são definidas no modelo JSON:

* [Serviço ligado do Armazenamento do Azure](#azure-storage-linked-service)
* [Serviço ligado do HDInsight a pedido](#hdinsight-on-demand-linked-service)
* [Conjunto de dados de entrada do blobs do Azure](#azure-blob-input-dataset)
* [Conjunto de dados de saída do blob do Azure](#azure-blob-output-dataset)
* [Pipeline de dados com uma atividade de cópia](#data-pipeline)

#### <a name="azure-storage-linked-service"></a>Serviço ligado do Storage do Azure
O serviço ligado Armazenamento do Azure liga a sua conta do Armazenamento do Azure à fábrica de dados. Neste tutorial, a mesma conta de armazenamento é utilizada como a conta de armazenamento do HDInsight predefinida, o armazenamento de dados de entrada e o armazenamento de dados de saída. Por conseguinte, é possível definir apenas o serviço ligado do Storage do Azure um. Na definição do serviço ligado, especifique o nome e a chave da sua conta de armazenamento do Azure. Veja [Azure Storage linked service (Serviço ligado de Armazenamento do Azure)](../data-factory/connector-azure-blob-storage.md) para obter detalhes sobre as propriedades JSON utilizadas para definir um serviço ligado de Armazenamento do Azure.

```json
{
    "name": "[variables('storageLinkedServiceName')]",
    "type": "linkedservices",
    "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]" ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
        }
    }
}
```
A **connectionString** utiliza os parâmetros storageAccountName e storageAccountKey. Especifique valores para estes parâmetros ao implementar o modelo.  

#### <a name="hdinsight-on-demand-linked-service"></a>Serviço ligado do HDInsight a pedido
Na definição do serviço ligado de HDInsight a pedido, especifique valores para parâmetros de configuração que são utilizados pelo serviço Data Factory para criar um cluster do HDInsight Hadoop no tempo de execução. Veja o artigo [Serviços ligados de computação](../data-factory/compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obter detalhes sobre as propriedades JSON utilizadas para definir um serviço ligado do HDInsight a pedido.  

```json

{
    "type": "linkedservices",
    "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
    "dependsOn": [
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedservices/', variables('storageLinkedServiceName'))]"
    ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "sshUserName": "myuser",                            
            "sshPassword": "MyPassword!",
            "linkedServiceName": "[variables('storageLinkedServiceName')]"
        }
    }
}
```
Tenha em atenção os seguintes pontos:

* O Data Factory cria um **baseado em Linux** cluster do HDInsight.
* O cluster de Hadoop do HDInsight é criado na mesma região que a conta de armazenamento.
* Tenha em atenção o *timeToLive* definição. A fábrica de dados elimina automaticamente o cluster depois do cluster está a ser inativo durante 30 minutos.
* O cluster do HDInsight cria um **contentor predefinido** no armazenamento de blobs especificado no JSON (**linkedServiceName**). Quando o cluster é eliminado, o HDInsight não é eliminado deste contentor. Este comportamento é propositado. Com o serviço ligado do HDInsight a pedido, é criado um cluster do HDInsight sempre que um setor tiver de ser processado, exceto se houver um cluster em direto (**timeToLive**) que será eliminado no fim do processamento.

Veja [On-demand HDInsight Linked Service (Serviço Ligado do HDInsight a Pedido)](../data-factory/compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obter detalhes.

> [!IMPORTANT]
> À medida que são processados mais setores, verá muitos contentores no armazenamento de blobs do Azure. Se não precisar deles para a resolução de problemas das tarefas, poderá eliminá-los para reduzir o custo de armazenamento. Os nomes destes contentores seguem um padrão: "adf**nomedafábricadedados**-**nomedoserviçoligado**-carimbodedataehora". Utilize ferramentas como o [Explorador de Armazenamento do Microsoft](http://storageexplorer.com/) para eliminar contentores no armazenamento de blobs do Azure.

#### <a name="azure-blob-input-dataset"></a>Conjunto de dados de entrada de blobs do Azure
Na definição do conjunto de dados de entrada, especifique os nomes de contentor do blob, pasta e ficheiro que contém os dados de entrada. Veja [Azure Blob dataset properties (Propriedades do conjunto de dados de Blobs do Azure)](../data-factory/connector-azure-blob-storage.md) para obter detalhes sobre as propriedades JSON utilizadas para definir um conjunto de dados de Blobs do Azure.

```json

{
    "type": "datasets",
    "name": "[variables('blobInputDatasetName')]",
    "dependsOn": [
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
    ],
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
}

```

Tenha em atenção as seguintes definições específicas na definição de JSON:

```json
"fileName": "input.log",
"folderPath": "adfgetstarted/inputdata",
```

#### <a name="azure-blob-output-dataset"></a>Conjunto de dados de saída do Blob do Azure
Na definição do conjunto de dados de saída, especificar os nomes de contentor de blob e a pasta que contém os dados de saída. Veja [Azure Blob dataset properties (Propriedades do conjunto de dados de Blobs do Azure)](../data-factory/connector-azure-blob-storage.md) para obter detalhes sobre as propriedades JSON utilizadas para definir um conjunto de dados de Blobs do Azure.  

```json

{
    "type": "datasets",
    "name": "[variables('blobOutputDatasetName')]",
    "dependsOn": [
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
    ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
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
            "interval": 1,
            "style": "EndOfInterval"
        }
    }
}
```

FolderPath Especifica o caminho para a pasta que contém os dados de saída:

```json
"folderPath": "adfgetstarted/partitioneddata",
```

O [disponibilidade do conjunto de dados](../data-factory/concepts-datasets-linked-services.md) definição é o seguinte:

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "style": "EndOfInterval"
},
```

No Azure Data Factory, a disponibilidade do conjunto de dados de saída unidades do pipeline. Neste exemplo, o setor é produzido mensalmente no último dia do mês (EndOfInterval). 

#### <a name="data-pipeline"></a>Pipeline de dados
Definir um pipeline que transforma os dados ao executar o script de ramo de registo num cluster do Azure HDInsight a pedido. Veja [Pipeline JSON (JSON do Pipeline)](../data-factory/concepts-pipelines-activities.md) para obter descrições dos elementos JSON utilizados para definir um pipeline neste exemplo.

```json
{
    "type": "datapipelines",
    "name": "[parameters('dataFactoryName')]",
    "dependsOn": [
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedServices/', variables('hdInsightOnDemandLinkedServiceName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/datasets/', variables('blobInputDatasetName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/datasets/', variables('blobOutputDatasetName'))]"
    ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "description": "Azure Data Factory pipeline with an Hadoop Hive activity",
        "activities": [
            {
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "[variables('storageLinkedServiceName')]",
                    "defines": {
                        "inputtable": "[concat('wasb://adfgetstarted@', parameters('storageAccountName'), '.blob.core.windows.net/inputdata')]",
                        "partitionedtable": "[concat('wasb://adfgetstarted@', parameters('storageAccountName'), '.blob.core.windows.net/partitioneddata')]"
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
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ],
        "start": "2016-01-01T00:00:00Z",
        "end": "2016-01-31T00:00:00Z",
        "isPaused": false
    }
}
```

O pipeline contém uma atividade, atividade HDInsightHive. À medida que ambos começar e terminar datas são em Janeiro de 2016, dados de apenas um mês (um setor) é processado. Ambos *iniciar* e *final* da atividade tem uma data anterior, pelo que o Data Factory processa dados para o mês imediatamente. Se a extremidade é uma data futura, a fábrica de dados cria setor outra quando o tempo é fornecido. Para obter mais informações, consulte [fábrica de dados de agendamento e execução](../data-factory/v1/data-factory-scheduling-and-execution.md).

## <a name="clean-up-the-tutorial"></a>Limpar o tutorial

### <a name="delete-the-blob-containers-created-by-on-demand-hdinsight-cluster"></a>Eliminar os contentores de blob criados pelo cluster do HDInsight a pedido
Com o serviço de ligado de HDInsight a pedido, um cluster do HDInsight é criado sempre que um setor tiver de ser processado, exceto se houver um cluster existente em direto (timeToLive); e o cluster é eliminado quando o processamento é concluído. Para cada cluster, o Azure Data Factory cria um contentor do blob no armazenamento de Blobs do Azure utilizado como a conta de stroage predefinida para o cluster. Apesar do cluster do HDInsight é eliminado, o contentor de armazenamento de BLOBs predefinido e a conta de armazenamento associada não são eliminados. Este comportamento é propositado. À medida que são processados mais setores, verá muitos contentores no armazenamento de blobs do Azure. Se não precisar deles para a resolução de problemas das tarefas, poderá eliminá-los para reduzir o custo de armazenamento. Os nomes destes contentores seguem um padrão: `adfyourdatafactoryname-linkedservicename-datetimestamp`.

Eliminar o **adfjobs** e **adfyourdatafactoryname-linkedservicename-datetimestamp** pastas. O contentor de adfjobs contém os registos da tarefa de Azure Data Factory.

### <a name="delete-the-resource-group"></a>Eliminar o grupo de recursos
[O Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) é utilizado para implementar, gerir e monitorizar a sua solução como um grupo.  Eliminar um grupo de recursos elimina todos os componentes no interior do grupo.  

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Clique em **grupos de recursos** no painel esquerdo.
3. Clique no nome do grupo de recursos que criou no seu script do PowerShell. Utilize o filtro se tem demasiados grupos de recursos listados. É aberto o grupo de recursos num novo painel.
4. No **recursos** mosaico, deverá ter a conta do storage predefinida e a fábrica de dados listados, a menos que partilhar o grupo de recursos com outros projetos.
5. Clique em **eliminar** no topo do painel. Se o fizer, elimina a conta de armazenamento e os dados armazenados na conta de armazenamento.
6. Introduza o nome do grupo de recursos para confirmar a eliminação e, em seguida, clique em **eliminar**.

No caso de não pretender eliminar a conta de armazenamento ao eliminar o grupo de recursos, considere a seguinte arquitetura, separando os dados de negócio da conta do storage predefinida. Neste caso, tem um grupo de recursos para a conta de armazenamento com os dados de negócio e outro grupo de recursos para a conta de armazenamento predefinido para o HDInsight ligado a fábrica de dados e do serviço. Ao eliminar o segundo grupo de recursos, não afetar a conta de armazenamento de dados de negócio. Para tal:

* Adicione o seguinte para o grupo de recursos de nível superior, juntamente com o recurso de Microsoft.DataFactory/datafactories no seu modelo do Resource Manager. Cria uma conta de armazenamento:

    ```json
    {
        "name": "[parameters('defaultStorageAccountName')]",
        "type": "Microsoft.Storage/storageAccounts",
        "location": "[parameters('location')]",
        "apiVersion": "[variables('defaultApiVersion')]",
        "dependsOn": [ ],
        "tags": {

        },
        "properties": {
            "accountType": "Standard_LRS"
        }
    },
    ```
* Adicione um novo ponto de serviço ligado para a nova conta de armazenamento:

    ```json
    {
        "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]" ],
        "type": "linkedservices",
        "name": "[variables('defaultStorageLinkedServiceName')]",
        "apiVersion": "[variables('apiVersion')]",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('defaultStorageAccountName'),';AccountKey=',listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccountName')), variables('defaultApiVersion')).key1)]"
            }
        }
    },
    ```
* Configure o serviço de ondemand ligado do HDInsight com um dependsOn adicional e um additionalLinkedServiceNames:

    ```json
    {
        "dependsOn": [
            "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
            "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedservices/', variables('defaultStorageLinkedServiceName'))]",
            "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedservices/', variables('storageLinkedServiceName'))]"

        ],
        "type": "linkedservices",
        "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
        "apiVersion": "[variables('apiVersion')]",
        "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
                "version": "3.5",
                "clusterSize": 1,
                "timeToLive": "00:05:00",
                "osType": "Linux",
                "sshUserName": "myuser",                            
                "sshPassword": "MyPassword!",
                "linkedServiceName": "[variables('storageLinkedServiceName')]",
                "additionalLinkedServiceNames": "[variables('defaultStorageLinkedServiceName')]"
            }
        }
    },            
    ```
## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a utilizar o Azure Data Factory para criar o cluster do HDInsight a pedido para processar as tarefas do Hive. Para mais informações:

* [Tutorial do Hadoop: começar a utilizar o Hadoop baseado em Linux no HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Criar clusters do Hadoop baseados em Linux no HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Documentação do HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/)
* [Documentação da fábrica de dados](https://azure.microsoft.com/documentation/services/data-factory/)

## <a name="appendix"></a>Apêndice

### <a name="azure-cli-script"></a>Script CLI do Azure
Pode utilizar a CLI do Azure em vez de utilizar o Azure PowerShell para o tutorial. Para utilizar a CLI do Azure, instale primeiro CLI do Azure de acordo com as instruções seguintes:

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

#### <a name="use-azure-cli-to-prepare-the-storage-and-copy-the-files"></a>Utilizar a CLI do Azure para preparar o armazenamento e copiar os ficheiros

```
azure login

azure config mode arm

azure group create --name "<Azure Resource Group Name>" --location "East US 2"

azure storage account create --resource-group "<Azure Resource Group Name>" --location "East US 2" --type "LRS" <Azure Storage Account Name>

azure storage account keys list --resource-group "<Azure Resource Group Name>" "<Azure Storage Account Name>"
azure storage container create "adfgetstarted" --account-name "<Azure Storage AccountName>" --account-key "<Azure Storage Account Key>"

azure storage blob copy start "https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log" --dest-account-name "<Azure Storage Account Name>" --dest-account-key "<Azure Storage Account Key>" --dest-container "adfgetstarted"
azure storage blob copy start "https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql" --dest-account-name "<Azure Storage Account Name>" --dest-account-key "<Azure Storage Account Key>" --dest-container "adfgetstarted"
```

O nome do contentor é *adfgetstarted*. Mantenha-a como está. Caso contrário, terá de atualizar o modelo do Resource Manager. Se precisar de ajuda com este script CLI, consulte [utilizando a CLI do Azure com o Storage do Azure](../storage/common/storage-azure-cli.md).
