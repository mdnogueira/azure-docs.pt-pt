<properties
    pageTitle="Introdução ao Explorador de Armazenamento (Pré-visualização) | Microsoft Azure"
    description="Gerir os recursos do Storage do Azure com o Explorador de Armazenamento (Pré-visualização)"
    services="storage"
    documentationCenter="na"
    authors="TomArcher"
    manager="douge"
    editor="" />

 <tags
    ms.service="storage"
    ms.devlang="multiple"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="08/17/2016"
    ms.author="tarcher" />


# Introdução ao Explorador de Armazenamento (Pré-visualização)

## Descrição geral 

O Explorador de Armazenamento do Microsoft Azure (Pré-visualização) é uma aplicação autónoma que lhe permite trabalhar facilmente com dados do Storage do Azure no Windows, OS X e Linux. Neste artigo, irá aprender as várias formas de ligar e gerir as contas do Storage do Azure.

![Explorador de Armazenamento do Microsoft Azure (pré-visualização)][15]

## Pré-requisitos

- [Transfira e instale o Explorador de Armazenamento (pré-visualização)](http://www.storageexplorer.com)

## Ligar a um serviço ou a uma conta do Storage

Explorador de Armazenamento (Pré-visualização) fornece um conjunto de formas para ligar às contas do Storage. Isto inclui ligar a contas do Storage associadas às subscrições Azure, ligar a contas do Storage e serviços partilhados de outras subscrições do Azure e até ligar e gerir o armazenamento local utilizando o Emulador do Storage do Azure:

- [Ligar a uma subscrição Azure](#connect-to-an-azure-subscription) – Gira os recursos de armazenamento que pertencem à sua subscrição do Azure.
- [Trabalhar com o armazenamento de desenvolvimento local](#work-with-local-development-storage) – Faça a gestão de armazenamento local utilizando o Emulador de armazenamento do Microsoft Azure. 
- [Anexar ao armazenamento externo](#attach-or-detach-an-external-storage-account) – Gira recursos de armazenamento que pertencem a outra subscrição do Azure com a chave e o nome da conta do Storage.
- [Anexar conta de armazenamento através da SAS](#attach-storage-account-using-sas) – Faça a gestão de recursos de armazenamento que pertencem a outra subscrição Azure com uma SAS.
- [Anexar serviço através da SAS](#attach-service-using-sas) – Gira um serviço de armazenamento específico (contentor de blobs, filas ou tabelas) que pertence a outra subscrição do Azure com uma SAS.

## Ligar a uma subscrição do Azure

> [AZURE.NOTE] Se não tiver uma conta do Azure, pode [inscrever-se numa avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) ou [ativar os benefícios de subscritor do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

1. No Explorador de Armazenamento (pré-visualização), selecione **Definições de conta do Azure**. 

    ![Definições de conta do Azure][0]

1. O painel esquerdo irá apresentar agora todas as contas Microsoft nas quais tenha sessão iniciada. Para ligar a outra conta, selecione **Adicionar uma conta** e siga as caixas de diálogo para iniciar sessão com uma conta Microsoft que esteja associada a, pelo menos, uma subscrição Azure ativa.

    ![Adicionar uma conta][1]

1. Quando inicia a sessão com êxito numa conta Microsoft, o painel da esquerda será preenchido com as subscrições do Azure associadas a essa conta. Selecione as subscrições do Azure com as quais pretende trabalhar e, em seguida, selecione **Aplicar**. (Selecionar **Todas as subscrições** alterna entre selecionar todas ou nenhuma das subscrições Azure listadas.)

    ![Selecionar subscrições do Azure][3]

1. O painel esquerdo apresenta as contas de armazenamento associadas às subscrições do Azure selecionadas.

    ![Subscrições do Azure selecionadas][4]

## Trabalhar com armazenamento de desenvolvimento local

O Explorador de Armazenamento (Pré-visualização) permite-lhe funcionar com o armazenamento local utilizando o Emulador do Storage do Azure. Isto permite-lhe escrever código contra e testar o armazenamento sem ter necessariamente uma conta do Storage implementada no Azure (uma vez que a conta do Storage está a ser emulada pelo Emulador do Storage do Azure).

>[AZURE.NOTE] Atualmente, o Emulador do Storage do Azure é suportado apenas para o Windows. 

1. No painel da esquerda do Explorador de Armazenamento (pré-visualização), expanda o nó **(Contas de Armazenamento ** > **Locais e Anexadas** > **(Desenvolvimento)**.

    ![Nó de desenvolvimento local][21]

1. Se ainda não instalou o Emulador do Storage do Azure, será solicitado a fazê-lo através de uma barra de informações. Se a barra de informações for apresentada, selecione **Transferir a versão mais recente** e instale o emulador. 

    ![Transferir o pedido de Emulador de Armazenamento do Azure][22]

1. Assim que o emulador estiver instalado, terá a possibilidade de criar e de trabalhar com tabelas, filas e blobs locais. Para saber como trabalhar com cada tipo de conta do Storage, selecione na ligação adequada abaixo:

    - [Gerir recursos do Blob Storage do Azure](./vs-azure-tools-storage-explorer-blobs.md)
    - Gerir recursos de armazenamento de partilha de ficheiros do Azure - *Brevemente*
    - Gerir recursos de armazenamento de filas do Azure – *Brevemente*
    - Gerir recursos de Table Storage do Azure – *Brevemente*

## Anexar ou desanexar uma conta do Storage externo

Explorador de Armazenamento (Pré-visualização) fornece a capacidade para anexar a contas do Storage externo para que as contas do Storage possam ser partilhadas facilmente. Esta secção explica como anexar (e desanexar) de contas do Storage externo.

### Obter as credenciais da conta do Storage

Para partilhar uma conta do Storage externo, o proprietário dessa conta tem primeiro de obter as credenciais – nome de conta e chave – para a conta e, em seguida, partilhar essas informações com a pessoa que pretende anexar a essa conta (externa). Obter as credenciais da conta do Storage pode ser efetuado através do Portal do Azure ao seguir estes passos: 

1.  Inicie sessão no [Portal do Azure](https://portal.azure.com).
1.  Selecione **Procurar**.
1.  Selecione **Contas do Storage**.
1.  No painel **Contas do Storage**, selecione a conta do Storage pretendida.
1.  No painel **Definições** para a conta do Storage selecionada, selecione **Chaves de acesso**.

    ![Opção Chaves de acesso][5]
    
1.  No painel **Chaves de acesso**, copie os valores **NOME DA CONTA DO STORAGE** e **CHAVE 1** para utilização quando anexar à conta do Storage. 

    ![Chaves de acesso][6]

### Anexar a uma conta do Storage externo
Para anexar a uma conta de armazenamento externo, é necessário o nome e a chave da conta. A secção *Obter as credenciais da conta de armazenamento* explica como obter estes valores a partir do portal do Azure. No entanto, tenha em atenção que no portal a chave de conta é designada por «chave 1», por isso se o Explorador de Armazenamento (Pré-visualização) pedir-lhe uma chave de conta, terá de introduzir (ou colar) o valor de "chave 1". 
 
1.  No Explorador de Armazenamento (Pré-visualização), selecione **Ligar ao armazenamento do Azure**.

    ![Ligar à opção de armazenamento do Azure][23]

1.  Na caixa de diálogo **Ligar ao armazenamento do Azure**, especifique a chave de conta (valor «chave 1» do portal do Azure) e, em seguida, selecione **Seguinte**.

    ![Ligar ao diálogo de armazenamento do Azure][24] 

1.  Na caixa de diálogo **Anexar Armazenamento Externo**, introduza o nome da conta de armazenamento na caixa **Nome da conta**, especifique as definições pretendidas e selecione **Seguinte** quando terminar. 

    ![Anexar a caixa de diálogo de armazenamento externo][8]

1.  Na caixa de diálogo **Resumo da Ligação**, verifique as informações. Se pretender alterar alguma informação, selecione **Voltar** e reintroduza as definições pretendidas. Depois de terminar, selecione **Ligar**.

1.  Depois de anexada, a conta de armazenamento externo será apresentada com o texto **(Externo)** anexado ao nome da conta de armazenamento. 

    ![Resultado da ligação a uma conta de armazenamento externo][9]

### Desanexar de uma conta do Storage externo

1.  Clique com o botão direito do rato na conta do Storage externo que pretende desanexar e, no menu de contexto, selecione **Desanexar**.

    ![Anular a exposição da opção de armazenamento][10]

1.  Quando for apresentada a caixa de mensagem de confirmação, selecione **Sim** para confirmar a desanexação da conta do Storage externo.

## Anexar conta de armazenamento através da SAS

Uma [SAS (Assinatura de Acesso Partilhado)](storage/storage-dotnet-shared-access-signature-part-1.md) fornece ao administrador de uma subscrição Azure a capacidade de conceder acesso a uma conta de armazenamento de forma temporária sem ter de fornecer as credenciais de subscrição do Azure. 

Para ilustrar isto, vamos supor que o Utilizador A é um administrador de uma subscrição Azure e o Utilizador A pretende permitir que o Utilizador B aceda a uma conta do Storage por um período limitado de tempo com determinadas permissões:

1. O Utilizador A gera uma SAS (que consiste em cadeia de ligação para a conta do Storage) durante um período de tempo específico e com as permissões pretendidas.
1. O Utilizador A partilha o SAS com a pessoa que pretende acesso à conta do Storage – o Utilizador B, no nosso exemplo.  
1. O Utilizador B utiliza o Explorador de Armazenamento (Pré-visualização) para anexar à conta que pertence ao Utilizador A através da SAS fornecida. 

### Obter uma SAS para a conta que pretende partilhar

1.  No Explorar de Armazenamento (Pré-visualização), clique com o botão direito na conta de armazenamento que pretende partilhar e, no menu de contexto, selecione **Obter Assinatura de Acesso Partilhado**.

    ![Obter a opção de menu de contexto SAS][13]

1. Na caixa de diálogo **Assinatura de Acesso Partilhado**, especifique o período de tempo e as permissões que pretende para a conta e selecione **Criar**.

    ![Obter a caixa de diálogo SAS][14]
 
1. Uma segunda caixa de diálogo **Assinatura de Acesso Partilhado** aparecerá a apresentar a SAS. Selecione **Copiar** junto a **Cadeia de Ligação** para a copiar para a área de transferência. Selecione **Fechar** para dispensar a caixa de diálogo.

### Ligar à conta partilhada utilizando a SAS

1.  No Explorador de Armazenamento (Pré-visualização), selecione **Ligar ao armazenamento do Azure**.

    ![Ligar à opção de armazenamento do Azure][23]

1.  Na caixa de diálogo **Ligar ao armazenamento do Azure**, especifique a cadeia de ligação e, em seguida, selecione **Seguinte**.

    ![Ligar ao diálogo de armazenamento do Azure][24] 

1.  Na caixa de diálogo **Resumo da Ligação**, verifique as informações. Se pretender alterar alguma informação, selecione **Voltar** e reintroduza as definições pretendidas. Depois de terminar, selecione **Ligar**.

1.  Depois de anexada, a conta do Storage será apresentada com o texto (SAS) acrescentado ao nome da conta que forneceu.

    ![Resultado do anexado a uma conta utilizando SAS][17]

## Anexar serviço através da SAS

A secção [Anexar conta de armazenamento através da SAS](#attach-storage-account-using-sas) ilustra como um administrador da subscrição Azure pode conceder acesso temporário a uma conta de armazenamento ao gerar (e partilhar) uma SAS para a conta de armazenamento. Do mesmo modo, é possível gerar uma SAS para um serviço específico (contentor de blobs, filas ou tabelas) dentro de uma conta do Storage.  

### Gerar uma SAS para o serviço que pretende partilhar

Neste contexto, um serviço pode ser um contentor de blobs, de filas ou tabelas. As secções seguintes explicam como gerar a SAS para o serviço listado:

- [Obter a SAS para um contentor de blobs](./vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)
- Obter a SAS para uma partilha de ficheiro - *Brevemente*
- Obter a SAS para uma fila - *Brevemente*
- Obter a SAS para uma tabela - *Brevemente*

### Anexar ao serviço da conta partilhada através da SAS

1.  No Explorador de Armazenamento (Pré-visualização), selecione **Ligar ao armazenamento do Azure**.

    ![Ligar à opção de armazenamento do Azure][23]

1.  Na caixa de diálogo **Ligar ao armazenamento do Azure**, especifique a URI da SAS e, em seguida, selecione **Seguinte**.

    ![Ligar ao diálogo de armazenamento do Azure][24] 

1.  Na caixa de diálogo **Resumo da Ligação**, verifique as informações. Se pretender alterar alguma informação, selecione **Voltar** e reintroduza as definições pretendidas. Depois de terminar, selecione **Ligar**.

1.  Quando estiver anexado, o serviço anexado recentemente será apresentado sob o nó **(Serviço SAS)**.

    ![Resultado da anexação a um serviço partilhado através de SAS][20]

## Procurar contas do Storage

Se tiver uma longa lista de contas do Storage, uma forma rápida de localizar uma conta do Storage específico consiste em utilizar a caixa de pesquisa na parte superior do painel esquerdo. 

Ao escrever na caixa de pesquisa, o painel esquerdo irá apresentar apenas as contas do Storage que correspondem ao valor de pesquisa que introduziu até esse ponto. A captura de ecrã seguinte ilustra um exemplo em que pesquisei todas as contas do Storage em que o nome da conta do Storage contém o texto “tarcher”.

![Procurar conta de armazenamento][11]
    
Para limpar a pesquisa, selecione o botão **x** na caixa de pesquisa.

## Passos seguintes
- [Gerir os recursos do Blob Storage do Azure com o Explorador de Armazenamento (Pré-visualização)](./vs-azure-tools-storage-explorer-blobs.md)

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



<!--HONumber=Sep16_HO3-->


