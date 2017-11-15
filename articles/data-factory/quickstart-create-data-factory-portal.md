---
title: "Criar uma fábrica de dados do Azure com o portal do Azure | Microsoft Docs"
description: "Crie uma fábrica de dados do Azure para copiar dados de um arquivo de dados na cloud (Armazenamento de Blobs do Azure) para outro arquivo de dados na cloud (Base de Dados SQL do Azure)."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 09/19/2017
ms.author: jingwang
ms.openlocfilehash: 851477331c5990f2e950b2aa83ef1d61e6174326
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="create-a-data-factory-using-the-azure-portal"></a>Criar uma fábrica de dados com o portal do Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versão 2 - Pré-visualização](quickstart-create-data-factory-portal.md)

O Azure Data Factory é um serviço de integração de dados baseado na cloud que lhe permite criar fluxos de trabalho condicionados por dados na cloud para orquestrar e automatizar o movimento e a transformação de dados. Com o Azure Data Factory, poderá criar e agendar fluxos de trabalho condicionados por dados (denominados pipelines) que são capazes de ingerir dados provenientes de diferentes arquivos de dados, processar/transformar os dados através de serviços de computação, tais como o Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics e Azure Machine Learning, e publicar os dados de saída em arquivos de dados como o Azure SQL Data Warehouse para serem consumidos por aplicações de business intelligence (BI). 

Neste início rápido, irá utilizar o portal do Azure para criar uma fábrica de dados. Depois de criar a fábrica de dados, terá de utilizar o PowerShell, o SDK .NET, o SDK Python ou a API REST, como noutros inícios rápidos, como um pipeline de dados que copia dados a partir de um arquivo de dados de origem para um arquivo de dados de destino. Atualmente, não pode criar pipelines numa fábrica de dados com o portal do Azure.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço Data Factory, que está disponível em geral (GA), veja a [introdução à versão 1 do Data Factory](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="log-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure
Inicie sessão no [Portal do Azure](https://portal.azure.com/).

## <a name="create-a-data-factory"></a>Criar uma fábrica de dados
Eis os passos que deve realizar como parte deste início rápido:
1. Clique em **Novo** no menu da esquerda, clique em **Dados + Análise** e, em seguida, em **Data Factory**. 
   
   ![Novo -> DataFactory](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)
2. No painel **Nova fábrica de dados**, introduza **ADFTutorialDataFactory** para o **nome**. 
      
     ![Painel Nova fábrica de dados](./media/quickstart-create-data-factory-portal/new-azure-data-factory.png)
 
   O nome do Azure Data Factory deve ser **globalmente exclusivo**. Se receber o seguinte erro, altere o nome da fábrica de dados (por exemplo, oseunomeADFTutorialDataFactory) e tente criá-la novamente. Veja o tópico [Data Factory – Naming Rules (Data Factory – Regras de Nomenclatura)](naming-rules.md) para obter as regras de nomenclatura dos artefactos do Data Factory.
  
       `Data factory name “ADFTutorialDataFactory” is not available`
3. Selecione a sua subscrição do **Azure** na qual pretende criar a fábrica de dados. 
4. No **Grupo de Recursos**, siga um destes passos:
     
      - Selecione **Utilizar existente** e selecione um grupo de recursos já existente na lista pendente. 
      - Selecione **Criar novo** e introduza o nome de um grupo de recursos.   
         
      Alguns dos passos deste início rápido pressupõem que utiliza o nome: **ADFTutorialResourceGroup** para o grupo de recursos. Para saber mais sobre os grupos de recursos, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../azure-resource-manager/resource-group-overview.md).  
4. Selecione **V2 (Pré-visualização)** para a **versão**.
5. Selecione a **localização** da fábrica de dados. Atualmente, o Data Factory V2 só permite criar fábricas de dados nas regiões E.U.A. Leste, E.U.A. Leste 2 e Europa Ocidental. Os arquivos de dados (Armazenamento do Azure, Base de Dados SQL do Azure, etc.) e as computações (HDInsight, etc.) utilizados pela fábrica de dados podem estar noutras regiões.
6. Selecione **Afixar ao dashboard**.     
7. Clique em **Criar**.
      
      > [!IMPORTANT]
      > Para criar instâncias do Data Factory, tem de ser um membro da função [Contribuinte do Data Factory](../active-directory/role-based-access-built-in-roles.md#data-factory-contributor) ao nível do grupo de recursos/subscrição.
      > 
      > O nome da fábrica de dados pode ser registado como um nome DNS no futuro e, por conseguinte, ficar publicamente visível.             
3. No painel, vai ver o mosaico seguinte com o estado **A implementar fábrica de dados**. 

    ![Mosaico “implementar a fábrica de dados”](media//quickstart-create-data-factory-portal/deploying-data-factory.png)
1. Depois de concluída a criação, vai ver o painel **Data Factory**, conforme mostrado na imagem.
   
   ![Home page da fábrica de dados](./media/quickstart-create-data-factory-portal/data-factory-home-page.png)


## <a name="next-steps"></a>Passos seguintes
O pipeline neste exemplo copia dados de uma localização para outra localização num armazenamento de blobs do Azure. Leia os [tutoriais](tutorial-copy-data-dot-net.md) para saber como utilizar o Data Factory em mais cenários. 