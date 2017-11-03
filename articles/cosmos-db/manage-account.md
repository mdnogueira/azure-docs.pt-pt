---
title: "Gerir uma conta de base de dados do Azure Cosmos através do Portal do Azure | Microsoft Docs"
description: "Saiba como gerir a sua conta de base de dados do Azure Cosmos através do Portal do Azure. Localize um guia sobre como utilizar o Portal do Azure para ver, copiar, eliminar e contas de acesso."
keywords: Portal do Azure, o documentdb, azure, do Microsoft azure
services: cosmos-db
documentationcenter: 
author: kirillg
manager: jhubbard
editor: cgronlun
ms.assetid: 00fc172f-f86c-44ca-8336-11998dcab45c
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: kirillg
ms.openlocfilehash: a0c6ec8d490e1adacc96758971ab91d8eaeab45c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-manage-an-azure-cosmos-db-account"></a>Como gerir uma conta de base de dados do Azure Cosmos
Saiba como definir consistência global, trabalhar com as chaves e eliminar uma conta de base de dados do Azure Cosmos no portal do Azure.

## <a id="consistency"></a>Gerir definições de consistência da base de dados do Azure Cosmos
Selecionar o nível de consistência direita depende da utilização da semântica da sua aplicação. Deverá familiarizar-se com os níveis de consistência disponíveis do BD Azure Cosmos lendo [com níveis de consistência para maximizar a disponibilidade e o desempenho na base de dados do Azure Cosmos][consistency]. BD do Azure do Cosmos fornece a consistência, disponibilidade e garantias de desempenho, em cada nível de consistência disponível para a sua conta de base de dados. Configurar a sua conta de base de dados com um nível de consistência de forte requer que os seus dados estão limitados a uma única região do Azure e globalmente disponíveis. Por outro lado, os níveis de consistência simples - obsoletismo, session ou eventual permitem-lhe associar qualquer número de regiões do Azure com a sua conta de base de dados. Os seguintes passos simples mostram como selecionar o nível de consistência predefinida para a sua conta de base de dados. 

### <a name="to-specify-the-default-consistency-for-an-azure-cosmos-db-account"></a>Para especificar a consistência predefinida para uma conta de base de dados do Azure Cosmos
1. No [portal do Azure](https://portal.azure.com/), aceder à sua conta de base de dados do Azure Cosmos.
2. No painel de conta, clique em **predefinido consistência**.
3. No **consistência predefinida** painel, selecione o novo nível de consistência e clique em **guardar**.
    ![Sessão de consistência predefinida][5]

## <a id="keys"></a>Ver, copiar e voltar a gerar chaves de acesso
Quando cria uma conta de base de dados do Azure Cosmos, o serviço gera duas chaves de acesso principal que podem ser utilizadas para autenticação quando a conta de base de dados do Azure Cosmos é acedida. Ao fornecer duas chaves de acesso, base de dados do Azure Cosmos permite-lhe voltar a gerar as chaves sem qualquer interrupção à sua conta de base de dados do Azure Cosmos. 

No [portal do Azure](https://portal.azure.com/), acesso a **chaves** painel no menu de recurso no **conta de base de dados do Azure Cosmos** painel para ver, copiar e regenerar as chaves de acesso que são utilizadas para aceder à sua conta de base de dados do Azure Cosmos.

![Azure Portal captura de ecrã, painel chaves](./media/manage-account/keys.png)

> [!NOTE]
> O **chaves** painel também inclui cadeias de ligação primária e secundária que podem ser utilizadas para ligar à sua conta do [ferramenta de migração de dados](import-data.md).
> 
> 

As chaves de só de leitura também estão disponíveis neste painel. Operações de leitura e as consultas são operações só de leitura, tempo cria, elimina, e substitui não é.

### <a name="copy-an-access-key-in-the-azure-portal"></a>Copiar uma chave de acesso no Portal do Azure
No **chaves** painel, clique em de **cópia** botão à direita da chave que pretende copiar.

![Ver e copiar uma chave de acesso no Portal do Azure, o painel chaves](./media/manage-account/copykeys.png)

### <a name="regenerate-access-keys"></a>Voltar a gerar chaves de acesso
Deverá alterar as chaves de acesso à sua conta de base de dados do Azure Cosmos periodicamente para ajudar a manter as suas ligações mais segura. São atribuídas duas chaves de acesso que lhe permite manter as ligações para a conta de base de dados do Azure Cosmos utilizando uma chave de acesso enquanto volta a gerar a chave de acesso.

> [!WARNING]
> A regenerar as chaves de acesso afeta todas as aplicações que dependem da chave atual. Todos os clientes que utilizam a chave de acesso para aceder à conta de base de dados do Azure Cosmos tem de ser atualizados para utilizar a nova chave.
> 
> 

Se tiver aplicações ou serviços em nuvem com a conta de base de dados do Azure Cosmos, perderá as ligações se voltar a gerar chaves, a menos que reverta as chaves. Os passos seguintes descrevem o processo envolvidos na implementação as suas chaves.

1. Atualize a chave de acesso no código da aplicação para fazer referência à chave de acesso secundária da conta de base de dados do Azure Cosmos.
2. Regenere a chave de acesso primária para a sua conta de base de dados do Azure Cosmos. No [Portal do Azure](https://portal.azure.com/), aceder à sua conta de base de dados do Azure Cosmos.
3. No **conta de base de dados do Azure Cosmos** painel, clique em **chaves**.
4. No **chaves** painel, clique no botão regenerar, em seguida, clique em **Ok** para confirmar que pretende gerar uma nova chave.
    ![Voltar a gerar chaves de acesso](./media/manage-account/regenerate-keys.png)
5. Depois de ter verificado que a nova chave está disponível para utilização (cerca de 5 minutos após a regeneração da), atualize a chave de acesso no código da aplicação para fazer referência a nova chave de acesso primária.
6. Volte a gerar a chave de acesso secundária.
   
    ![Voltar a gerar chaves de acesso](./media/manage-account/regenerate-secondary-key.png)

> [!NOTE]
> Pode demorar vários minutos antes de uma chave recentemente criada pode ser utilizada para aceder à sua conta de base de dados do Azure Cosmos.
> 
> 

## <a name="get-the--connection-string"></a>Obter a cadeia de ligação
Para obter a cadeia de ligação, efetue o seguinte: 

1. No [portal do Azure](https://portal.azure.com), aceder à sua conta de base de dados do Azure Cosmos.
2. No menu de recursos, clique em **chaves**.
3. Clique em de **cópia** junto ao **cadeia de ligação principal** ou **cadeia de ligação secundária** caixa. 

Se estiver a utilizar a cadeia de ligação no [ferramenta de migração de bases de dados do Azure Cosmos DB](import-data.md), acrescentar o nome de base de dados ao fim da cadeia de ligação. `AccountEndpoint=< >;AccountKey=< >;Database=< >`.

## <a id="delete"></a>Eliminar uma conta de base de dados do Azure Cosmos
Para remover uma conta de base de dados do Azure Cosmos do Portal do Azure que já não está a utilizar, faça duplo clique o nome da conta e clique em **eliminar conta**.

![Como eliminar uma conta de base de dados do Azure Cosmos no Portal do Azure](./media/manage-account/deleteaccount.png)

1. No [portal do Azure](https://portal.azure.com/), aceder à conta de base de dados do Azure Cosmos que pretende eliminar.
2. No **conta de base de dados do Azure Cosmos** painel, faça duplo clique a conta e, em seguida, clique em **eliminar conta**. 
3. No painel de resultante confirmação, escreva o nome da conta de base de dados do Azure Cosmos para confirmar que pretende eliminar a conta.
4. Clique em de **eliminar** botão.

![Como eliminar uma conta de base de dados do Azure Cosmos no Portal do Azure](./media/manage-account/delete-account-confirm.png)

## <a id="next"></a>Passos seguintes
Saiba como [começar com a sua conta de base de dados do Azure Cosmos](http://go.microsoft.com/fwlink/p/?LinkId=402364).

<!--Image references-->
[5]: ./media/manage-account/documentdb_change_consistency-1.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: consistency-levels.md
[azureregions]: https://azure.microsoft.com/regions/#services
[offers]: https://azure.microsoft.com/pricing/details/cosmos-db/
