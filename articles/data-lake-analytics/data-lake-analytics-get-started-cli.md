---
title: Introdução à Análise do Azure Data Lake, utilizando a Interface de Linha de Comandos do Azure | Microsoft Docs
description: 'Saiba como utilizar a Interface de Linha de Comandos do Azure para criar uma conta de Data Lake Store, criar uma tarefa de Data Lake Analytics, utilizando U-SQL, e submeter a tarefa. '
services: data-lake-analytics
documentationcenter: ''
author: edmacauley
manager: jhubbard
editor: cgronlun

ms.service: data-lake-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/16/2016
ms.author: edmaca

---
# Tutorial: introdução à Análise do Azure Data Lake, utilizando a Interface de Linha de Comandos do Azure (CLI)
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Saiba como utilizar a CLI do Azure para criar contas de Análise do Azure Data Lake, definir as tarefas de Data Lake Analytics em [U-SQL](data-lake-analytics-u-sql-get-started.md) e submeter as tarefas para contas de Data Lake Analytics. Para mais informações sobre a Data Lake Analytics, consulte [Descrição geral da Análise do Azure Data Lake](data-lake-analytics-overview.md).

Neste tutorial, irá desenvolver uma tarefa que lê um ficheiro de valores separados por tabulações (TSV) e converte-o num ficheiro de valores separados por vírgulas (CSV). Para seguir o mesmo tutorial, utilizando outras ferramentas suportadas, clique nos separadores na parte superior desta secção.

## Pré-requisitos
Antes de começar este tutorial, tem de ter o seguinte:

* **Uma subscrição do Azure**. Veja [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **CLI do Azure**. Consulte [instalar e configurar a CLI do Azure](../xplat-cli-install.md).
  * Transfira e instale o **pré-lançamento** [Ferramentas CLI do Azure](https://github.com/MicrosoftBigData/AzureDataLake/releases) para concluir esta demonstração.
* **Autenticação**, utilizando o seguinte comando:
  
        azure login
    Para mais informações sobre a autenticação através de uma conta escolar ou profissional, consulte [Ligar a uma subscrição do Azure a partir da CLI do Azure](../xplat-cli-connect.md).
* **Mude para o modo Azure Resource Manager**, utilizando o seguinte comando:
  
        azure config mode arm

## Criar conta de Data Lake Analytics
Tem de ter uma conta de Data Lake Analytics antes de poder executar quaisquer tarefas. Para criar uma conta de Data Lake Analytics, tem de especificar o seguinte:

* **Grupo de Recursos do Azure**: uma conta de Data Lake Analytics tem de ser criada dentro de um grupo de Recursos do Azure. O [Azure Resource Manager](../resource-group-overview.md) permite trabalhar com os recursos na aplicação como um grupo. Pode implementar, atualizar ou eliminar todos os recursos da aplicação numa operação única e coordenada.  
  
    Para enumerar os grupos de recursos na subscrição:
  
        azure group list 
  
    Para criar um novo grupo de recursos:
  
        azure group create -n "<Resource Group Name>" -l "<Azure Location>"
* **Nome da conta de Data Lake Analytics**
* **Localização**: um dos centros de dados do Azure que suportem a Data Lake Analytics.
* **Conta do Data Lake predefinida**: cada conta de Data Lake Analytics tem uma conta do Data Lake predefinida.
  
    Para listar a conta existente do Data Lake:
  
        azure datalake store account list
  
    Para criar uma nova conta do Data Lake:
  
        azure datalake store account create "<Data Lake Store Account Name>" "<Azure Location>" "<Resource Group Name>"
  
  > [!NOTE]
  > O nome de conta do Data Lake só pode conter minúsculas e números.
  > 
  > 

**Para criar uma conta de Data Lake Analytics**

        azure datalake analytics account create "<Data Lake Analytics Account Name>" "<Azure Location>" "<Resource Group Name>" "<Default Data Lake Account Name>"

        azure datalake analytics account list
        azure datalake analytics account show "<Data Lake Analytics Account Name>"          

![Mostrar conta de Data Lake Analytics](./media/data-lake-analytics-get-started-cli/data-lake-analytics-show-account-cli.png)

> [!NOTE]
> O nome de conta de Data Lake Analytics só pode conter minúsculas e números.
> 
> 

## Carregar dados para o Data Lake Store
Neste tutorial, irá processar alguns registos de pesquisa.  O registo de pesquisa pode ser armazenado no Data Lake Store ou no Armazenamento de Blobs do Azure. 

O Portal do Azure fornece uma interface de utilizador para copiar alguns ficheiros de dados de exemplo para a conta do Data Lake predefinida, que inclui um ficheiro de registo de pesquisa. Consulte [Preparar dados de origem](data-lake-analytics-get-started-portal.md#prepare-source-data) para carregar os dados para a conta de Data Lake Store predefinida.

Para carregar ficheiros através da cli, utilize o seguinte comando:

    azure datalake store filesystem import "<Data Lake Store Account Name>" "<Path>" "<Destination>"
    azure datalake store filesystem list "<Data Lake Store Account Name>" "<Path>"

A Data Lake Analytics também pode aceder ao armazenamento de Blobs do Azure.  Para carregar dados para o armazenamento de Blobs do Azure, consulte [Utilizar a CLI do Azure com o Armazenamento do Azure](../storage/storage-azure-cli.md).

## Submeter tarefas de Data Lake Analytics
As tarefas de Data Lake Analytics são escritas em linguagem U-SQL. Para saber mais sobre U-SQL, consulte [Introdução à linguagem U SQL](data-lake-analytics-u-sql-get-started.md) e [Referência de linguagem U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348).

**Para criar um script de tarefa de Data Lake Analytics**

* Crie um ficheiro de texto com o seguinte script de U-SQL e guarde o ficheiro de texto na estação de trabalho:
  
        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();
  
        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();
  
    Este script U-SQL lê o ficheiro de dados de origem, utilizando **Extractors.Tsv()**, e, em seguida, cria um ficheiro csv, utilizando **Outputters.Csv()**. 
  
    Não modifique os dois caminhos, exceto se copiar o ficheiro de origem para uma localização diferente.  A Data Lake Analytics cria a pasta de saída, se não existir.
  
    É mais simples utilizar caminhos relativos para ficheiros armazenados em contas do Data Lake predefinidas. Também pode utilizar caminhos absolutos.  Por exemplo 
  
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
  
    Tem de utilizar caminhos absolutos para aceder a ficheiros em contas de Armazenamento ligadas.  A sintaxe para ficheiros armazenados numa Conta de Armazenamento do Azure ligada é:
  
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv
  
  > [!NOTE]
  > O contentor de Blobs do Azure com blobs públicos ou permissões de acesso a contentores públicos não são atualmente suportados.      
  > 
  > 

**Para submeter a tarefa**

    azure datalake analytics job create  "<Data Lake Analytics Account Name>" "<Job Name>" "<Script>"


Os comandos seguintes podem ser utilizados para listar tarefas, obter detalhes de tarefas e cancelar tarefas:

    azure datalake analytics job cancel "<Data Lake Analytics Account Name>" "<Job Id>"
    azure datalake analytics job list "<Data Lake Analytics Account Name>"
    azure datalake analytics job show "<Data Lake Analytics Account Name>" "<Job Id>"

Depois de a tarefa ser concluída, pode utilizar os seguintes cmdlets para listar o ficheiro e transferi-lo:

    azure datalake store filesystem list "<Data Lake Store Account Name>" "/Output"
    azure datalake store filesystem export "<Data Lake Store Account Name>" "/Output/SearchLog-from-Data-Lake.csv" "<Destination>"
    azure datalake store filesystem read "<Data Lake Store Account Name>" "/Output/SearchLog-from-Data-Lake.csv" <Length> <Offset>

## Consulte também
* Para ver o mesmo tutorial, utilizando outras ferramentas, clique nos seletores de separador no topo da página.
* Para ver uma consulta mais complexa, consulte [Analisar registos de site através da Análise do Azure Data Lake](data-lake-analytics-analyze-weblogs.md).
* Para começar a desenvolver aplicações U-SQL, consulte [Desenvolver scripts SQL-U, utilizando as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* Para saber mais sobre U-SQL, consulte [Introdução à linguagem U-SQL da Análise do Azure Data Lake](data-lake-analytics-u-sql-get-started.md).
* Para tarefas de gestão, consulte [Gerir a Análise do Azure Data Lake através do Portal do Azure](data-lake-analytics-manage-use-portal.md).
* Para uma descrição geral da Data Lake Analytics, consulte [Descrição geral da Análise do Azure Data Lake](data-lake-analytics-overview.md).

<!--HONumber=Sep16_HO3-->


