---
title: Gerir o Azure Cosmos DB no Explorador de armazenamento do Azure
description: Saiba como gerir a base de dados do Azure Cosmos no Explorador de armazenamento do Azure.
Keywords: Azure Cosmos DB, Explorador de armazenamento do Azure, o DocumentDB, MongoDB, o DocumentDB
services: cosmos-db
documentationcenter: 
author: Jiaj-Li
manager: omafnan
editor: 
tags: Azure Cosmos DB
ms.assetid: 
ms.service: cosmos-db
ms.custom: Azure Cosmos DB active
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: Jiaj-Li
ms.openlocfilehash: e695cdd7c51e18a386764ab8444d3336366ae265
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="manage-azure-cosmos-db-in-azure-storage-explorer-preview"></a>Gerir o Azure Cosmos DB no Explorador de armazenamento do Azure (pré-visualização)

Utilizar base de dados do Azure Cosmos no Explorador de armazenamento do Azure permite aos utilizadores gerir entidades de base de dados do Azure Cosmos, manipular dados, atualizar e acionadores juntamente com outras entidades do Azure como armazenamento de blobs e filas de procedimentos armazenados. Agora pode utilizar a mesma ferramenta para gerir a sua do Azure de entidades diferentes num único local. Neste momento, o Explorador de armazenamento do Azure suporta SQL (DocumentDB) e contas de MongoDB.

Neste artigo, pode saber como utilizar o Explorador de armazenamento para gerir a base de dados do Azure Cosmos.


## <a name="prerequisites"></a>Pré-requisitos

Uma conta de base de dados do Azure Cosmos para um SQL Server (DocumentDB) ou a base de dados de MongoDB. Se não tiver uma conta, pode criar uma no portal do Azure, conforme descrito em [BD do Azure Cosmos: criar uma aplicação de web API do DocumentDB com .NET e o portal do Azure](create-documentdb-dotnet.md).

## <a name="installation"></a>Instalação

Instalar os bits Explorador de armazenamento do Azure mais recentes aqui: [Explorador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/), agora suportamos a versão do Windows, Linux e MAC.

## <a name="connect-to-an-azure-subscription"></a>Ligar a uma subscrição do Azure

1. Depois de instalar o **Explorador de armazenamento do Azure**, clique em de **Plug-in** ícone à esquerda, conforme mostrado na imagem seguinte.
       
   ![Plug-in ícone](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/plug-in-icon.png)
 
2. Selecione **adicionar uma conta do Azure**e, em seguida, clique em **início de sessão**.

   ![Ligar à subscrição do Azure](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/connect-to-azure-subscription.png)

2. No **início de sessão do Azure** caixa de diálogo, selecione **sessão**e, em seguida, introduza as suas credenciais do Azure.

    ![Iniciar sessão](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/sign-in.png)

3. Selecione a sua subscrição da lista e, em seguida, clique em **aplicar**.

    ![Registe-se](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/apply-subscription.png)

    O painel do Explorador de atualizações e apresenta as contas na subscrição selecionada.

    ![Lista de contas](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/account-list.png)

    Se ligou com êxito a sua **conta de base de dados do Azure Cosmos** à sua subscrição do Azure.

## <a name="connect-to-azure-cosmos-db-by-using-a-connection-string"></a>Ligar à base de dados do Azure Cosmos através da utilização de uma cadeia de ligação

Uma maneira de ligar a uma base de dados do Azure Cosmos consiste em utilizar uma cadeia de ligação. Utilize os seguintes passos para ligar através de uma cadeia de ligação.

1. Localizar **locais e anexadas** na árvore à esquerda, clique com botão direito **contas de base de dados do Azure Cosmos**, escolha **ligar à base de dados do Azure Cosmos...**

    ![ligar à base de dados do Azure Cosmos pela cadeia de ligação](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/connect-to-db-by-connection-string.png)

2. Escolha apropriados **experiência predefinido** para o tipo de conta qualquer um dos **DocumentDB** ou **MongoDB**, colar na sua **cadeia de ligação**e, em seguida, clique em **OK** para ligar a conta de base de dados do Azure Cosmos. Para informações sobre como obter a cadeia de ligação, consulte [obter a cadeia de ligação](https://docs.microsoft.com/en-us/azure/cosmos-db/manage-account#get-the--connection-string).

    ![cadeia de ligação](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/connection-string.png)

## <a name="azure-cosmos-db-resource-management"></a>Gestão de recursos do Azure do Cosmos DB

Pode gerir uma conta de base de dados do Azure Cosmos praticando seguintes operações:
* Abra a conta no portal do Azure
* Adicione o recurso a lista de acesso rápido
* Recursos de pesquisa e atualização
* Criar e eliminar bases de dados
* Criar e eliminar coleções
* Criar, editar, eliminar e filtrar os documentos
* Gerir procedimentos armazenados, acionadores e funções definidas pelo utilizador

### <a name="quick-access-tasks"></a>Tarefas de acesso rápido

Ao clicar em subscrições no painel do Explorador, pode realizar muitas tarefas de ação rápida:

* Faça duplo clique uma conta de base de dados do Azure Cosmos ou uma base de dados, pode escolher **aberto no Portal** e gerir recursos no browser no portal do Azure.

     ![Abrir no portal](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/open-in-portal.png)

* Também pode adicionar a conta de base de dados do Azure Cosmos, base de dados, coleção para **acesso rápido**.
* **Pesquisar a partir daqui** pesquisa de palavra-chave permite sob o caminho selecionado.

    ![pesquisar a partir daqui](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/search-from-here.png) 

### <a name="database-and-collection-management"></a>Gestão de base de dados e coleção
#### <a name="create-a-database"></a>Criar uma base de dados 
Clique com o botão direito a conta de base de dados do Azure Cosmos, escolha **Create Database**, entrada de nome de base de dados e prima **Enter** para concluir.

![Criar base de dados](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/create-database.png) 

#### <a name="delete-a-database"></a>Eliminar uma base de dados
Clique com o botão direito na base de dados, clique em **eliminar a base de dados**e clique em **Sim** na janela de pop-up. O nó de base de dados é eliminado e a conta de base de dados do Azure Cosmos é atualizada automaticamente.

![Eliminar database1](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/delete-database1.png)  

![Eliminar database2](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/delete-database2.png) 

#### <a name="create-a-collection"></a>Criar uma coleção
Clique com o botão direito a base de dados, escolha **criar coleção**e, em seguida, forneça as seguintes informações como **ID de coleção**, **capacidade de armazenamento**, etc. Clique em **OK** para concluir. Para obter informações sobre definições de chave de partição, consulte [estrutura para criação de partições](partition-data.md#designing-for-partitioning).

Se uma chave de partição é utilizada quando criar uma coleção, depois de concluída a criação, o valor da chave de partição não é possível alterar a coleção.

![Criar collection1](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/create-collection.png)

![Criar collection2](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/create-collection2.png) 

#### <a name="delete-a-collection"></a>Elimina uma coleção
Faça duplo clique na coleção, clique em **eliminar coleção**e, em seguida, clique em **Sim** na janela de pop-up. 

O nó de coleção é eliminado e a base de dados é atualizada automaticamente.  

![Eliminar a coleção](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/delete-collection.png) 

### <a name="document-management"></a>Gestão de documentos

#### <a name="create-and-modify-documents"></a>Criar e modificar os documentos
Para criar um novo documento, abra **documentos** na janela da esquerda, clique em **novo documento**, edite o conteúdo no painel direito, em seguida, clique em **guardar**. Pode também atualizar um documento existente e, em seguida, clique em **guardar**. Alterações podem ser eliminadas, clicando em **rejeitar**.

![Documento](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/document.png)

#### <a name="delete-a-document"></a>Eliminar um documento
Clique em de **eliminar** botão para eliminar o documento selecionado.
#### <a name="query-for-documents"></a>Consulta de documentos
Editar o filtro de documento introduzindo um [consulta SQL](documentdb-sql-query.md) e, em seguida, clique em **aplicar**.

![Filtro](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/filter.png)

### <a name="manage-stored-procedures-triggers-and-udfs"></a>Gerir e procedimentos armazenados, acionadores, UDFs
* Para criar um procedimento armazenado, na árvore da esquerda, clique com botão direito **procedimento armazenado**, escolha **Criar procedimento armazenado**, introduza um nome no lado esquerdo, escreva os scripts de procedimento armazenado na janela da direita e, em seguida, Clique em **criar**. 
* Também pode editar existentes procedimentos armazenados, fazendo duplo clique, efetuar a atualização e, em seguida, clicando **atualizar** para guardar ou clique em **rejeitar** para cancelar o altere.

![Procedimento armazenado](./media/tutorial-documentdb-and-mongodb-in-storage-explorer/stored-procedure.png)

* As operações de **Acionadores** e **UDF** são semelhantes às de **procedimentos armazenados**.

## <a name="next-steps"></a>Passos seguintes

* Veja o vídeo seguinte para ver como utilizar a base de dados do Azure Cosmos no Explorador de armazenamento do Azure: [utilizar o Azure Cosmos DB no Explorador de armazenamento do Azure](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be).
* Saiba mais sobre o Explorador de armazenamento e ligue-se mais serviços [introdução ao Explorador de armazenamento (pré-visualização)](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-manage-with-storage-explorer).

