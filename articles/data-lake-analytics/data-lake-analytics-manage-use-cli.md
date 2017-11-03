---
title: Gerir o Azure Data Lake Analytics utilizando a Interface de linha de comandos do Azure | Microsoft Docs
description: Saiba como gerir contas de Data Lake Analytics, origens de dados, tarefas e os utilizadores com a CLI do Azure
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: 4e5a3a0a-6d7f-43ed-aeb5-c3b3979a1e0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: edmaca
ms.openlocfilehash: f90bada3572c0ed40b07d76ec02c1b499bbd1428
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-azure-data-lake-analytics-using-azure-command-line-interface-cli"></a>Gerir o Azure Data Lake Analytics utilizando a Interface de linha de comandos do Azure (CLI)
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Saiba como gerir contas de Azure Data Lake Analytics, origens de dados, os utilizadores e tarefas utilizando a CLI do Azure. Para ver tópicos de gestão, utilizando outras ferramentas, clique em Selecionar o separador acima.


**Pré-requisitos**

Antes de começar este tutorial, tem de ter o seguinte:

* **Uma subscrição do Azure**. Veja [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **CLI do Azure**. Consulte [instalar e configurar a CLI do Azure](../cli-install-nodejs.md).
  * Transfira e instale o **pré-lançamento** [Ferramentas CLI do Azure](https://github.com/MicrosoftBigData/AzureDataLake/releases) para concluir esta demonstração.
* **Autenticação**, utilizando o seguinte comando:
  
        azure login
    Para mais informações sobre a autenticação através de uma conta escolar ou profissional, consulte [Ligar a uma subscrição do Azure a partir da CLI do Azure](../xplat-cli-connect.md).
* **Mude para o modo Azure Resource Manager**, utilizando o seguinte comando:
  
        azure config mode arm

**Para listar os comandos de Data Lake Store e do Data Lake Analytics:**

    azure datalake store
    azure datalake analytics

<!-- ################################ -->
<!-- ################################ -->
## <a name="manage-accounts"></a>Gerir contas
Antes de executar as tarefas de Data Lake Analytics, tem de ter uma conta de Data Lake Analytics. Ao contrário do Azure HDInsight, que não paga por uma conta de análise quando não está em execução uma tarefa.  Apenas paga a hora quando se encontra em execução uma tarefa.  Para obter mais informações, consulte [descrição geral do Azure Data Lake Analytics](data-lake-analytics-overview.md).  

### <a name="create-accounts"></a>Criar contas
      azure datalake analytics account create "<Data Lake Analytics Account Name>" "<Azure Location>" "<Resource Group Name>" "<Default Data Lake Account Name>"


### <a name="update-accounts"></a>Atualizar contas
O seguinte comando atualiza as propriedades de uma conta existente do Data Lake Analytics

    azure datalake analytics account set "<Data Lake Analytics Account Name>"


### <a name="list-accounts"></a>Contas de lista
Contas de análise do Data Lake da lista 

    azure datalake analytics account list

Contas de análise do Data Lake da lista dentro de um grupo de recurso específico

    azure datalake analytics account list -g "<Azure Resource Group Name>"

Obter os detalhes de uma conta de Data Lake Analytics específica

    azure datalake analytics account show -g "<Azure Resource Group Name>" -n "<Data Lake Analytics Account Name>"

### <a name="delete-data-lake-analytics-accounts"></a>Eliminar contas de Data Lake Analytics
      azure datalake analytics account delete "<Data Lake Analytics Account Name>"


<!-- ################################ -->
<!-- ################################ -->
## <a name="manage-account-data-sources"></a>Gerir origens de dados de conta
O Data Lake Analytics, atualmente, suporta as seguintes origens de dados:

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Armazenamento do Azure](../storage/common/storage-introduction.md)

Quando cria uma conta de análise, tem de designar uma conta de armazenamento do Azure Data Lake para ser a conta do storage predefinida. A conta do storage predefinida ADL é utilizada para armazenar metadados e a tarefa de auditoria os registos da tarefa. Depois de criar uma conta de análise, pode adicionar mais contas de armazenamento do Data Lake e/ou a conta de armazenamento do Azure. 

### <a name="find-the-default-adl-storage-account"></a>Localizar a conta do storage predefinida ADL
    azure datalake analytics account show "<Data Lake Analytics Account Name>"

O valor está listado em propriedades: datalakeStoreAccount:name.

### <a name="add-additional-azure-blob-storage-accounts"></a>Adicionar mais contas de armazenamento de Blobs do Azure
      azure datalake analytics account datasource add -n "<Data Lake Analytics Account Name>" -b "<Azure Blob Storage Account Short Name>" -k "<Azure Storage Account Key>"

> [!NOTE]
> São suportados apenas Blob storage nomes abreviados.  Não utilize o FQDN, por exemplo "myblob.blob.core.windows.net".
> 
> 

### <a name="add-additional-data-lake-store-accounts"></a>Adicionar mais contas de Data Lake Store
      azure datalake analytics account datasource add -n "<Data Lake Analytics Account Name>" -l "<Data Lake Store Account Name>" [-d]

[-d] é um parâmetro opcional para indicar se a Data Lake a ser adicionado é a conta de Data Lake predefinida. 

### <a name="update-existing-data-source"></a>Atualizar a origem de dados existente
Para definir uma conta existente do Data Lake Store para ser a predefinição:

      azure datalake analytics account datasource set -n "<Data Lake Analytics Account Name>" -l "<Azure Data Lake Store Account Name>" -d

Para atualizar uma chave de conta de armazenamento de Blob existente:

      azure datalake analytics account datasource set -n "<Data Lake Analytics Account Name>" -b "<Blob Storage Account Name>" -k "<New Blob Storage Account Key>"

### <a name="list-data-sources"></a>Lista de origens de dados:
    azure datalake analytics account show "<Data Lake Analytics Account Name>"

![Origem de dados do Data Lake Analytics lista](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-data-source.png)

### <a name="delete-data-sources"></a>Elimine as origens de dados:
Para eliminar uma conta de Data Lake Store:

      azure datalake analytics account datasource delete "<Data Lake Analytics Account Name>" "<Azure Data Lake Store Account Name>"

Para eliminar uma conta de armazenamento de BLOBs:

      azure datalake analytics account datasource delete "<Data Lake Analytics Account Name>" "<Blob Storage Account Name>"

## <a name="manage-jobs"></a>Gerir tarefas
Tem de ter uma conta de Data Lake Analytics antes de poder criar uma tarefa.  Para obter mais informações, consulte [contas de gerir a análise do Data Lake](#manage-accounts).

### <a name="list-jobs"></a>Lista de tarefas
      azure datalake analytics job list -n "<Data Lake Analytics Account Name>"

![Origem de dados do Data Lake Analytics lista](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-jobs.png)

### <a name="get-job-details"></a>Obter os detalhes da tarefa
      azure datalake analytics job show -n "<Data Lake Analytics Account Name>" -j "<Job ID>"

### <a name="submit-jobs"></a>Submeter tarefas
> [!NOTE]
> A prioridade de predefinido de uma tarefa é 1000 e o grau de predefinição de paralelismo de uma tarefa é 1.
> 
> 

    azure datalake analytics job create  "<Data Lake Analytics Account Name>" "<Job Name>" "<Script>"

### <a name="cancel-jobs"></a>Cancelar tarefas
Utilize o comando de lista para localizar o id da tarefa e, em seguida, Cancelar para cancelar a tarefa.

      azure datalake analytics job list -n "<Data Lake Analytics Account Name>"
      azure datalake analytics job cancel "<Data Lake Analytics Account Name>" "<Job ID>"

## <a name="manage-catalog"></a>Gerir o catálogo
O catálogo U-SQL é utilizado para a estrutura de dados e o código para que possa ser partilhados por scripts U-SQL. O catálogo permite que o desempenho mais elevado possível com os dados no Azure Data Lake. Para obter mais informações, veja [Utilizar catálogo U-SQL](data-lake-analytics-use-u-sql-catalog.md).

### <a name="list-catalog-items"></a>Lista os itens do catálogo
    #List databases
    azure datalake analytics catalog list -n "<Data Lake Analytics Account Name>" -t database

    #List tables
    azure datalake analytics catalog list -n "<Data Lake Analytics Account Name>" -t table

Os tipos de incluem a base de dados, esquema, assembly, origem de dados externa, tabela, a função de valor de tabela ou estatísticas da tabela.

<!-- ################################ -->
<!-- ################################ -->
## <a name="use-arm-groups"></a>Utilizar grupos ARM
As aplicações são geralmente constituídas por diversos componentes, por exemplo, uma aplicação Web, base de dados, servidor de base de dados, armazenamento e serviços de terceiros. O Azure Resource Manager (ARM) permite trabalhar com os recursos na sua aplicação como um grupo, referido como um Grupo de Recursos do Azure. Pode implementar, atualizar, monitorizar ou eliminar todos os recursos da sua aplicação numa operação única e coordenada. Utiliza um modelo para a implementação e esse modelo pode funcionar para ambientes diferentes, como de teste e produção. Pode clarificar a faturação da sua organização ao visualizar os custos agregados do grupo inteiro. Para obter mais informações, veja [Descrição geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). 

Um serviço do Data Lake Analytics pode incluir os seguintes componentes:

* Conta do Azure Data Lake Analytics
* Conta de armazenamento do Azure Data Lake predefinida necessária
* Contas de armazenamento adicionais do Azure Data Lake
* Contas de armazenamento do Azure adicionais

Pode criar um grupo de ARM para os tornar mais fácil de gerir todos estes componentes.

![Conta do Azure Data Lake Analytics e armazenamento](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

Uma conta de Data Lake Analytics e as contas do storage dependente devem ser colocadas no mesmo centro de dados do Azure.
O grupo ARM no entanto pode estar localizado num centro de dados diferentes.  

## <a name="see-also"></a>Consultar também
* [Descrição geral do Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Introdução ao Data Lake Analytics com o Portal do Azure](data-lake-analytics-get-started-portal.md)
* [Gerir o Azure Data Lake Analytics com o Portal do Azure](data-lake-analytics-manage-use-portal.md)
* [Monitorizar e resolver problemas das tarefas de Azure Data Lake Analytics com o Portal do Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

