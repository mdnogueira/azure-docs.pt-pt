---
title: "Introdução ao Explorador do Armazenamento (Pré-visualização) | Microsoft Docs"
description: "Gerir os recursos do Storage do Azure com o Explorador de Armazenamento (Pré-visualização)"
services: storage
documentationcenter: na
author: cawa
manager: paulyuk
editor: 
ms.assetid: 1ed0f096-494d-49c4-ab71-f4164ee19ec8
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2017
ms.author: cawa
ms.openlocfilehash: 58ab8a9c5864ce0cb505b78fd087df2973a7e0d8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-storage-explorer-preview"></a>Introdução ao Explorador de Armazenamento (Pré-visualização)
## <a name="overview"></a>Descrição geral
O Explorador de Armazenamento do Azure (Pré-visualização) é uma aplicação autónoma que lhe permite trabalhar facilmente com dados do Armazenamento do Azure em Windows, macOS e Linux. Neste artigo, irá aprender as várias formas de ligar e gerir as contas de armazenamento do Azure.

![Explorador de Armazenamento do Microsoft Azure (pré-visualização)][15]

## <a name="prerequisites"></a>Pré-requisitos
* [Transferir e instalar o Explorador de Armazenamento (Pré-visualização)](http://www.storageexplorer.com)

## <a name="connect-to-a-storage-account-or-service"></a>Ligar a um serviço ou a uma conta do Storage
O Explorador de Armazenamento (Pré-visualização) proporciona várias formas de ligar a contas de armazenamento. Pode, por exemplo:
* Ligar a contas de armazenamento associadas às suas subscrições do Azure.
* Ligar a contas e serviços de armazenamento que são partilhados com outras subscrições do Azure.
* Ligar e gerir o armazenamento local através do Emulador do Armazenamento do Azure. 

Além disso, pode trabalhar com contas de armazenamento no Azure global e nacional:

* [Ligar a uma subscrição do Azure](#connect-to-an-azure-subscription): faça a gestão dos recursos de armazenamento que pertencem à sua subscrição do Azure.
* [Trabalhar com o armazenamento de desenvolvimento local](#work-with-local-development-storage): faça a gestão do armazenamento local com o Emulador do Armazenamento do Azure.
* [Anexar ao armazenamento externo](#attach-or-detach-an-external-storage-account): faça a gestão dos recursos de armazenamento que pertencem a outra subscrição do Azure ou a clouds nacionais do Azure com o nome, a chave e os pontos finais da conta de armazenamento.
* [Anexar uma conta de armazenamento com SAS](#attach-storage-account-using-sas): faça a gestão dos recursos de armazenamento que pertencem a outra subscrição do Azure com uma assinatura de acesso partilhado (SAS).
* [Anexar um serviço com SAS](#attach-service-using-sas): faça a gestão de um serviço de armazenamento específico (contentor de blobs, filas ou tabelas) que pertence a outra subscrição do Azure com SAS.
* [Ligar a uma conta de base de dados do Azure Cosmos utilizando uma cadeia de ligação](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string): Gerir BD do Cosmos conta através da utilização de uma cadeia de ligação.

## <a name="connect-to-an-azure-subscription"></a>Ligar a uma subscrição do Azure
> [!NOTE]
> Se não tiver uma conta do Azure, pode [inscrever-se numa avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) ou [ativar os benefícios de subscritor do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).
>
>

1. No Explorador de Armazenamento (pré-visualização), selecione **Definições de conta do Azure**.

    ![Definições de conta do Azure][0]

2. O painel esquerdo apresenta todas as contas Microsoft nas quais tem sessão iniciada. Para ligar a outra conta, selecione **Adicionar uma conta** e siga as instruções para iniciar sessão com uma conta Microsoft que esteja associada a, pelo menos, uma subscrição do Azure ativa.

    >[!NOTE]
    >Atualmente, não é suportada a ligação ao Azure de países (como o Azure Alemanha, o Azure Government e o Azure China através de início de sessão). Veja a secção “Anexar ou desanexar uma conta de armazenamento externa” para saber como ligar a contas de armazenamento do Azure de países.

3. Depois de iniciar sessão com êxito numa conta Microsoft, o painel esquerdo é preenchido com as subscrições do Azure associadas a essa conta. Selecione as subscrições do Azure com as quais pretende trabalhar e, em seguida, selecione **Aplicar**. (Selecionar **Todas as subscrições** alterna entre selecionar todas ou nenhuma das subscrições Azure listadas.)

    ![Selecionar subscrições do Azure][3]  
    O painel esquerdo apresenta as contas de armazenamento associadas às subscrições do Azure selecionadas.

    ![Subscrições do Azure selecionadas][4]

## <a name="connect-to-an-azure-stack-subscription"></a>Ligar a uma subscrição do Azure Stack

Para obter informações sobre como ligar a uma subscrição do Azure Stack, veja [Connect Storage Explorer to an Azure Stack subscription](azure-stack/user/azure-stack-storage-connect-se.md) (Ligar o Explorador de Armazenamento a uma subscrição do Azure Stack).

## <a name="work-with-local-development-storage"></a>Trabalhar com armazenamento de desenvolvimento local
Com o Explorador de Armazenamento (Pré-visualização), pode trabalhar com o armazenamento local mediante a utilização do Emulador do Armazenamento do Azure. Esta abordagem permite-lhe escrever código e testar o armazenamento sem ter necessariamente uma conta de armazenamento implementada no Azure, pois esta está a ser emulada pelo Emulador do Armazenamento do Azure.

> [!NOTE]
> Atualmente, o Emulador do Storage do Azure é suportado apenas para o Windows.
>
>

1. No painel esquerdo do Explorador de Armazenamento (Pré-visualização), expanda o nó **(Locais e Anexadas)**  > **Contas de Armazenamento** > **(Desenvolvimento)**.

    ![Nó de desenvolvimento local][21]

2. Se ainda não instalou o Emulador de Armazenamento do Azure, será solicitado a fazê-lo através de uma barra de informações. Se a barra de informações for apresentada, selecione **Transferir a versão mais recente** e instale o emulador.

    ![Transferir o pedido de Emulador de Armazenamento do Azure][22]

3. Quando o emulador estiver instalado, pode criar e trabalhar com tabelas, filas e blobs locais. Para saber como trabalhar com cada tipo de conta de armazenamento, veja um dos artigos seguintes:

    * [Gerir recursos do armazenamento de blobs do Azure](vs-azure-tools-storage-explorer-blobs.md)
    * Manage Azure file share storage resources: *Coming soon* (Gerir recursos de armazenamento de partilha de ficheiros do Azure: Brevemente)
    * Manage Azure queue storage resources: *Coming soon* (Gerir recursos de armazenamento de filas do Azure: Brevemente)
    * Manage Azure table storage resources: *Coming soon* (Gerir recursos de armazenamento de tabelas do Azure: Brevemente)

## <a name="attach-or-detach-an-external-storage-account"></a>Anexar ou desanexar uma conta do Storage externo
Com o Explorador de Armazenamento (Pré-visualização), pode anexar a contas de armazenamento externas, para que estas possam ser partilhadas facilmente. Esta secção explica como anexar (e desanexar) de contas do Storage externo.

### <a name="get-the-storage-account-credentials"></a>Obter as credenciais da conta do Storage
Para partilhar uma conta de armazenamento externa, o proprietário dessa conta tem primeiro de obter as credenciais (nome e chave da conta) da mesma e, em seguida, partilhar essas informações com a pessoa que se quer anexar a essa conta (externa). Pode obter as credenciais da conta de armazenamento através do portal do Azure, fazendo o seguinte:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

2. Selecione **Procurar**.

3. Selecione **Contas do Storage**.

4. No painel **Contas de Armazenamento**, selecione a conta de armazenamento pretendida.

5. No painel **Definições** da conta de armazenamento selecionada, selecione **Chaves de acesso**.

    ![Opção Chaves de acesso][5]

6. No painel **Chaves de acesso**, copie os valores **Nome da conta de armazenamento** e **Key1** para utilização quando anexar à conta de armazenamento.

    ![Chaves de acesso][6]

### <a name="attach-to-an-external-storage-account"></a>Anexar a uma conta do Storage externo
Para anexar a uma conta de armazenamento externa, é necessário o nome e a chave da conta. A secção “Obter as credenciais da conta de armazenamento” explica como obter estes valores a partir do portal do Azure. No entanto, no portal, a chave de conta é designada por **key1**. Assim, quando o Explorador de Armazenamento (Pré-visualização) lhe pedir uma chave de conta, introduza o valor **key1**.

1. No Explorador de Armazenamento (Pré-visualização), selecione **Ligar ao armazenamento do Azure**.

    ![Ligar à opção de armazenamento do Azure][23]

2. Na caixa de diálogo **Ligar ao Armazenamento do Azure**, especifique a chave da conta (o valor **key1** no portal do Azure) e selecione **Seguinte**.

    > [!NOTE]
    > Pode introduzir a cadeia de ligação do armazenamento a partir de uma conta de armazenamento num Azure nacional. Por exemplo, para ligar a contas de armazenamento do Azure Alemanha, introduza as cadeias de ligação, de forma semelhante à seguinte: 
    >
    >* DefaultEndpointsProtocol=https
    >* AccountName=cawatest03
    >* AccountKey=<storage_account_key>
    >* EndpointSuffix=core.cloudapi.de
    
    >Pode obter a cadeia de ligação no portal do Azure da forma que foi descrita na secção “Obter as credenciais da conta de armazenamento”.

    ![Caixa de diálogo Ligar ao armazenamento do Azure][24]

3. Na caixa de diálogo **Anexar Armazenamento Externo**, na caixa **Nome da conta**, introduza o nome da conta de armazenamento, especifique outras definições pretendidas e selecione **Seguinte**.

    ![Caixa de diálogo Anexar armazenamento externo][8]

4. Na caixa de diálogo **Resumo da Ligação**, verifique as informações. Se quiser alterar alguma informação, selecione **Voltar** e reintroduza as definições pretendidas. 

5. Selecione **Ligar**.

6. Quando estiver ligada, a conta de armazenamento externa é apresentada com **(Externo)** anexado ao respetivo nome.

    ![Resultado da ligação a uma conta de armazenamento externo][9]

### <a name="detach-from-an-external-storage-account"></a>Desanexar de uma conta do Storage externo
1. Clique com o botão direito do rato na conta de armazenamento externo que pretende desanexar e selecione **Desanexar**.

    ![Anular a exposição da opção de armazenamento][10]

2. Na mensagem de confirmação, selecione **Sim** para confirmar a desanexação da conta de armazenamento externo.

## <a name="attach-a-storage-account-by-using-an-sas"></a>Anexar uma conta de armazenamento com SAS
As [SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) permitem que os administradores de subscrições do Azure concedam acesso temporário a uma conta de armazenamento sem que seja necessário indicar as credenciais dessas subscrições.

Para ilustrar este cenário, vamos supor que o UtilizadorA é um administrador de uma subscrição do Azure e que pretende permitir que o UtilizadorB aceda a uma conta de armazenamento por um período limitado de tempo com determinadas permissões:

1. O UtilizadorA gera uma SAS (que consiste na cadeia de ligação da conta de armazenamento) durante um período de tempo específico e com as permissões pretendidas.

2. O UtilizadorA partilha a SAS com a pessoa (UtilizadorB, no nosso exemplo) que quer aceder à conta de armazenamento.  

3. O UtilizadorB utiliza o Explorador de Armazenamento (Pré-visualização) para anexar à conta que pertence ao UtilizadorA através da SAS fornecida.

### <a name="get-an-sas-for-the-account-you-want-to-share"></a>Obter uma SAS para a conta que pretende partilhar
1. No Explorar de Armazenamento (Pré-visualização), clique com o botão direito na conta de armazenamento que pretende partilhar e selecione **Obter Assinatura de Acesso Partilhado**.

    ![Obter a opção de menu de contexto SAS][13]

2. Na caixa de diálogo **Assinatura de Acesso Partilhado**, especifique o período de tempo e as permissões que pretende para a conta e selecione **Criar**.

    ![Caixa de diálogo Obter SAS][14]  
    É apresentada a caixa de diálogo **Assinatura de Acesso Partilhado**, que mostra a SAS.

3. Junto a **Cadeia de Ligação**, selecione **Copiar** para copiá-la para a área de transferência e selecione **Fechar**.

### <a name="attach-to-the-shared-account-by-using-the-sas"></a>Ligar à conta partilhada com a SAS
1. No Explorador de Armazenamento (Pré-visualização), selecione **Ligar ao armazenamento do Azure**.

    ![Ligar à opção de armazenamento do Azure][23]

2. Na caixa de diálogo **Ligar ao Armazenamento do Azure**, especifique a cadeia de ligação e selecione **Seguinte**.

    ![Caixa de diálogo Ligar ao armazenamento do Azure][24]

3. Na caixa de diálogo **Resumo da Ligação**, verifique as informações. Para fazer alterações, selecione **Regressar** e introduza as definições que pretende. 

4. Selecione **Ligar**.

5. Depois de anexada, a conta de armazenamento é apresentada com **(SAS)** anexado ao nome da conta que indicou.

    ![Resultado de anexação a uma conta com SAS][17]

## <a name="attach-a-service-by-using-an-sas"></a>Anexar um serviço com SAS
A secção “Anexar uma conta de armazenamento com SAS” explica como é que os administradores de subscrições do Azure podem conceder acesso temporário a contas de armazenamento mediante a geração e a partilha de uma SAS para essas contas. Do mesmo modo, é possível gerar uma SAS para um serviço específico (contentor de blobs, filas ou tabelas) dentro de uma conta de armazenamento.  

### <a name="generate-an-sas-for-the-service-that-you-want-to-share"></a>Gerar uma SAS para o serviço que pretende partilhar
Neste contexto, um serviço pode ser um contentor de blobs, de filas ou tabelas. Para gerar a SAS para um serviço listado, veja:

* [Obter a SAS para um contentor de blobs](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)
* Get the SAS for a file share: *Coming soon* (Obter a SAS para uma partilha de ficheiros: Brevemente)
* Get the SAS for a queue: *Coming soon* (Obter a SAS para uma fila: Brevemente)
* Get the SAS for a table: *Coming soon* (Obter a SAS para uma tabela: Brevemente)

### <a name="attach-to-the-shared-account-service-by-using-the-sas"></a>Anexar ao serviço de conta partilhada com a SAS
1. No Explorador de Armazenamento (Pré-visualização), selecione **Ligar ao armazenamento do Azure**.

    ![Ligar à opção de armazenamento do Azure][23]

2. Na caixa de diálogo **Ligar ao Armazenamento do Azure**, especifique o URI da SAS e selecione **Seguinte**.

    ![Caixa de diálogo Ligar ao armazenamento do Azure][24]

3. Na caixa de diálogo **Resumo da Ligação**, verifique as informações. Para fazer alterações, selecione **Regressar** e introduza as definições que pretende. 

4. Selecione **Ligar**.

5. Quando estiver anexado, o serviço anexado recentemente é apresentado no nó **(SAS do Serviço)**.

    ![Resultado da anexação a um serviço partilhado com SAS][20]

## <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Ligar a uma conta de base de dados do Azure Cosmos utilizando uma cadeia de ligação
Besides gerir contas de base de dados do Azure Cosmos através de subscrição do Azure, uma maneira de ligar a uma base de dados do Azure Cosmos consiste em utilizar uma cadeia de ligação. Utilize os seguintes passos para ligar através de uma cadeia de ligação.

1. Localizar **locais e anexadas** na árvore à esquerda, clique com botão direito **contas de base de dados do Azure Cosmos**, escolha **ligar à base de dados do Azure Cosmos...**

    ![ligar à base de dados do Azure Cosmos pela cadeia de ligação][33]

2. Escolha a API do Azure Cosmos DB, cole o **cadeia de ligação**e, em seguida, clique em **OK** para ligar a conta de base de dados do Azure Cosmos. Para informações sobre como obter a cadeia de ligação, consulte [obter a cadeia de ligação](https://docs.microsoft.com/en-us/azure/cosmos-db/manage-account#get-the--connection-string).

    ![connection-string][32]

## <a name="search-for-storage-accounts"></a>Procurar contas do Storage
Se tiver uma longa lista de contas do Storage, uma forma rápida de localizar uma conta do Storage específico consiste em utilizar a caixa de pesquisa na parte superior do painel esquerdo.

Enquanto escreve na caixa de pesquisa, o painel esquerdo apresenta as contas de armazenamento que correspondem ao valor de pesquisa que introduziu até esse ponto. Por exemplo, é apresentada na captura de ecrã abaixo uma pesquisa por todas as contas de armazenamento cujo nome contenha **tarcher**.

![Procurar conta de armazenamento][11]

## <a name="next-steps"></a>Passos seguintes
* [Manage Azure Blob Storage resources with Storage Explorer (Preview)](vs-azure-tools-storage-explorer-blobs.md) [Gerir recursos do Armazenamento de Blobs do Azure com o Explorador de Armazenamento (Pré-visualização)]
* [Gerir o Azure Cosmos DB no Explorador de armazenamento do Azure (pré-visualização)](./cosmos-db/tutorial-documentdb-and-mongodb-in-storage-explorer.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/settings-icon.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/add-account-link.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/subscriptions-list.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-accounts-list.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys.png
[6]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys-copy.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-external-storage-dlg.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/external-storage-account.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-account-search.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage-confirmation.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-context-menu.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-dlg1.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/mase.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-account-using-sas-finished.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-service-using-sas-finished.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/local-storage-drop-down.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/download-storage-emulator.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-icon.png
[24]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-next.png
[25]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/add-certificate-azure-stack.png
[26]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/export-root-cert-azure-stack.png
[27]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/import-azure-stack-cert-storage-explorer.png
[28]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/select-target-azure-stack.png
[29]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/add-azure-stack-account.png
[30]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/select-accounts-azure-stack.png
[31]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/azure-stack-storage-account-list.png
[32]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string.PNG
[33]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-db-by-connection-string.PNG
