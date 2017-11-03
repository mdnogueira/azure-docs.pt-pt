---
title: "Tutorial de fábrica de dados: primeiro pipeline de dados | Microsoft Docs"
description: "Este tutorial do Azure Data Factory mostra como criar e agendar uma fábrica de dados que processa dados utilizando o script de ramo de registo num cluster de Hadoop."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: 
ms.assetid: 81f36c76-6e78-4d93-a3f2-0317b413f1d0
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: spelluru
robots: noindex
ms.openlocfilehash: a1a0679b91304df5cbc3dcaec14abfeaaa25c04f
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="tutorial-build-your-first-pipeline-to-transform-data-using-hadoop-cluster"></a>Tutorial: Criar o seu primeiro pipeline para transformar dados utilizando o cluster de Hadoop
> [!div class="op_single_selector"]
> * [Descrição geral e pré-requisitos](data-factory-build-your-first-pipeline.md)
> * [Portal do Azure](data-factory-build-your-first-pipeline-using-editor.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Modelo do Resource Manager](data-factory-build-your-first-pipeline-using-arm.md)
> * [API REST](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). Se estiver a utilizar a versão 2 do serviço do Data Factory, o que está em pré-visualização, consulte [início rápido: criar uma fábrica de dados utilizando o Azure Data Factory versão 2](../quickstart-create-data-factory-dot-net.md).

Neste tutorial, pode cria a primeira fábrica de dados do Azure com um pipeline de dados. O pipeline transforma os dados de entrada ao executar o script de ramo de registo num cluster do Azure HDInsight (Hadoop) para produzir os dados de saída.  

Este artigo fornece descrição geral e pré-requisitos para o tutorial. Depois de concluir os pré-requisitos, que pode fazer o tutorial, utilizando uma das ferramentas/SDKs seguintes: portal do Azure, o Visual Studio, o PowerShell, do modelo do Resource Manager, REST API. Selecione uma das opções na lista pendente no início (ou) hiperligações no fim deste artigo para fazer o tutorial, utilizando uma destas opções.    

## <a name="tutorial-overview"></a>Descrição geral do tutorial
Neste tutorial, vai executar os seguintes passos:

1. Criar um **fábrica de dados**. Uma fábrica de dados pode conter um ou mais pipelines de dados que moverem e transforme dados. 

    Neste tutorial, vai criar um pipeline na fábrica de dados. 
2. Criar um **pipeline**. Um pipeline pode ter uma ou mais atividades (exemplos: Atividade de Cópia, Atividade Hive do HDInsight). Este exemplo utiliza a atividade do ramo de registo do HDInsight que executa um script de ramo de registo num cluster do HDInsight Hadoop. O script cria uma tabela que faça referência a dados de registo em bruto web armazenados no blob storage do Azure pela primeira vez e, em seguida, particiona os dados não processados por ano e mês.

    Neste tutorial, o pipeline utiliza a atividade do ramo de registo para transformar dados ao executar uma consulta do Hive num cluster do Azure HDInsight Hadoop. 
3. Criar **serviços ligados**. Cria um serviço ligado para ligar um arquivo de dados ou um serviço de computação à fábrica de dados. Um arquivo de dados como o Armazenamento do Azure contém dados de entrada/saída de atividades no pipeline. Um serviço de computação, tais como o cluster do HDInsight Hadoop processos/transformações dados.

    Neste tutorial, criará dois serviços ligados: **Storage do Azure** e **Azure HDInsight**. Storage do Azure serviço ligado liga uma conta de armazenamento do Azure que contém os dados de entrada/saída à fábrica de dados. O Azure HDInsight serviço ligado liga um cluster do HDInsight do Azure que é utilizado para transformar dados à fábrica de dados. 
3. Criar **conjuntos de dados** de entrada e de saída. Um conjunto de dados de entrada representa a entrada de uma atividade no pipeline e um conjunto de dados de saída representa a saída da atividade.

    Neste tutorial, os conjuntos de dados de entrada e de saída, especifique as localizações de dados de entrada e de saída no armazenamento de Blobs do Azure. O serviço ligado do Storage do Azure Especifica que a conta de armazenamento do Azure é utilizado. Especifica um conjunto de dados de entrada onde se encontram os ficheiros de entrada e um conjunto de dados de saída, especifica onde são colocados ficheiros de saída. 


Consulte [introdução ao Azure Data Factory](data-factory-introduction.md) artigo para obter uma descrição detalhada do Azure Data Factory.
  
Eis o **vista de diagrama** da fábrica de dados de exemplo que cria neste tutorial. **MyFirstPipeline** tem uma atividade do tipo ramo de registo que consome **AzureBlobInput** conjunto de dados como uma entrada e produz **AzureBlobOutput** conjunto de dados como resultado. 

![Vista de diagrama no tutorial do Data Factory](media/data-factory-build-your-first-pipeline/data-factory-tutorial-diagram-view.png)


Neste tutorial, **inputdata** pasta o **adfgetstarted** contentor de Blobs do Azure contém um ficheiro com o nome input.log. Este ficheiro de registo tem entradas de três meses: Janeiro, Fevereiro e Março de 2016. Seguem-se as linhas de exemplo para cada mês no ficheiro de entrada. 

```
2016-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871 
2016-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
2016-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
```

Quando o ficheiro é processado pelo pipeline com a Atividade Hive do HDInsight, a atividade executa um script Hive no cluster do HDInsight que cria partições dos dados de entrada por ano e mês. O script cria três pastas de saída que contêm um ficheiro com as entradas de cada mês.  

```
adfgetstarted/partitioneddata/year=2016/month=1/000000_0
adfgetstarted/partitioneddata/year=2016/month=2/000000_0
adfgetstarted/partitioneddata/year=2016/month=3/000000_0
```

De linhas de exemplo mostradas acima, a uma primeira (com 2016-01-01) é escrita no ficheiro 000000_0 no mês = 1 pasta. Do mesmo modo, a segunda é escrita no ficheiro na pasta month=2 e a terceira é escrita no ficheiro na pasta month=3.  

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, tem de ter os seguintes pré-requisitos:

1. **Subscrição do Azure** - Se não tiver uma subscrição do Azure, pode criar uma conta de avaliação gratuita em apenas alguns minutos. Veja o artigo [Avaliação Gratuita](https://azure.microsoft.com/pricing/free-trial/) sobre como poderá obter uma conta de avaliação gratuita.
2. **Armazenamento do Azure** – Utilize uma conta de armazenamento do Azure para armazenar os dados deste tutorial. Se não tiver uma conta de armazenamento do Azure, veja o artigo [Criar uma conta de armazenamento](../../storage/common/storage-create-storage-account.md#create-a-storage-account). Depois de ter criado a conta de armazenamento, tome nota do **nome da conta** e **chave de acesso**. Veja [Ver, copiar e voltar a gerar chaves de acesso ao armazenamento](../../storage/common/storage-create-storage-account.md#view-and-copy-storage-access-keys).
3. Transferir e rever o ficheiro de consulta do Hive (**HQL**), localizado em: [https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql](https://adftutorialfiles.blob.core.windows.net/hivetutorial/partitionweblogs.hql). Esta consulta transforma os dados de entrada para produzir os dados de saída. 
4. Transferir e rever o ficheiro de entrada de exemplo (**input.log**), localizado em: [https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log](https://adftutorialfiles.blob.core.windows.net/hivetutorial/input.log)
5. Criar um contentor do blob denominado **adfgetstarted** no armazenamento de Blobs do Azure. 
6. Carregar **partitionweblogs.hql** do ficheiro para o **script** pasta o **adfgetstarted** contentor. Utilize ferramentas como [Explorador de armazenamento do Microsoft Azure](http://storageexplorer.com/). 
7. Carregar **input.log** do ficheiro para o **inputdata** pasta o **adfgetstarted** contentor. 

Depois de concluir os pré-requisitos, selecione uma das seguintes ferramentas/SDKs para fazer o tutorial: 

- [Portal do Azure](data-factory-build-your-first-pipeline-using-editor.md)
- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Modelo do Resource Manager](data-factory-build-your-first-pipeline-using-arm.md)
- [API REST](data-factory-build-your-first-pipeline-using-rest-api.md)

Portal do Azure e o Visual Studio fornecem a forma de GUI da criação do seu fábricas de dados. Enquanto, opções do PowerShell, o modelo do Resource Manager e REST API forma de scripting/programação de criação do seu fábricas de dados.

> [!NOTE]
> O pipeline de dados neste tutorial transforma os dados de entrada para produzirem dados de saída. Não copia dados de um de dados de origem para um arquivo de dados de destino. Para um tutorial sobre como copiar dados com o Azure Data Factory, consulte [Tutorial: Copy data from Blob Storage to SQL Database (Tutorial: copiar dados do Armazenamento de Blobs para a Base de Dados SQL)](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> Pode encadear duas atividades (executar uma atividade após a outra) ao definir o conjunto de dados de saída de uma atividade como o conjunto de dados de entrada da outra atividade. Consulte [Scheduling and execution in Data Factory (Agendamento e execução no Data Factory)](data-factory-scheduling-and-execution.md) para obter informações detalhadas. 





  
