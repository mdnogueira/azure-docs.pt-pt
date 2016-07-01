<properties
    pageTitle="Introdução ao Explorador de Armazenamento (Pré-visualização) | Microsoft Azure"
    description="Gerir os recursos do Storage do Azure com o Explorador de Armazenamento (Pré-visualização)"
    services="visual-studio-online"
    documentationCenter="na"
    authors="TomArcher"
    manager="douge"
    editor="" />

 <tags
    ms.service="visual-studio-online"
    ms.devlang="multiple"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="06/05/2016"
    ms.author="tarcher" />

# Introdução ao Explorador de Armazenamento (Pré-visualização)

## Descrição geral 

O Explorador de Armazenamento do Microsoft Azure (Pré-visualização) é uma aplicação autónoma que lhe permite trabalhar facilmente com dados do Storage do Azure no Windows, OSX e Linux. Neste artigo, irá aprender as várias formas de ligar e gerir as contas do Storage do Azure.

## Pré-requisitos

- [Transfira e instale o Explorador de Armazenamento (pré-visualização)](http://go.microsoft.com/fwlink/?LinkId=708343)

## Ligar a um serviço ou a uma conta do Storage

Explorador de Armazenamento (Pré-visualização) fornece um conjunto de formas para ligar às contas do Storage. Isto inclui ligar a contas do Storage associadas às subscrições Azure, ligar a contas do Storage e serviços partilhados de outras subscrições do Azure e até ligar e gerir o armazenamento local utilizando o Emulador do Storage do Azure:

- [Ligar a uma subscrição Azure](#connect-to-an-azure-subscription) – Gira os recursos de armazenamento que pertencem à sua subscrição do Azure.
- [Ligar ao armazenamento local](#connect-to-local-storage) – Gira o armazenamento local utilizando o Emulador do Storage do Azure. 
- [Anexar ao armazenamento externo](#attach-or-detach-an-external-storage-account) – Gira recursos de armazenamento que pertencem a outra subscrição do Azure com a chave e o nome da conta do Storage.
- [Anexar conta através da SAS](#attach-account-using-sas) – Gira recursos de armazenamento que pertencem a outra subscrição Azure com uma SAS.
- [Anexar serviço através da SAS](#attach-service-using-sas) – Gira um serviço de armazenamento específico (contentor de blobs, filas ou tabelas) que pertence a outra subscrição do Azure com uma SAS.

## Ligar a uma subscrição do Azure

> [AZURE.NOTE] Se não tiver uma conta do Azure, pode [inscrever-se numa avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) ou [ativar os benefícios de subscritor do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

1. Inicie o Explorador de Armazenamento (Pré-visualização). 

1. Se estiver a executar o Explorador de Armazenamento (Pré-visualização) pela primeira vez ou se já tiver executado o Explorador de Armazenamento (Pré-visualização) antes, mas ainda não se ligou a uma conta do Azure, verá uma barra de informações que lhe permite ligar a uma conta do Azure.

    ![][0]
    
1. Selecione **Ligar ao Microsoft Azure** e siga as caixas de diálogo para iniciar sessão com uma conta Microsoft que está associada a, pelo menos, uma subscrição Azure.

Quando inicia sessão com êxito com uma conta Microsoft, o painel esquerdo do Explorador de Armazenamento (Pré-visualização) será preenchido com todas as contas do Storage associadas a todas as subscrições Azure associadas à conta Microsoft.

### Filtrar as subscrições Azure

Explorador de Armazenamento (Pré-visualização) permite-lhe filtrar as subscrições Azure associadas à sua conta que terão as respetivas contas do Storage listadas no painel esquerdo.

1. Selecione o ícone (de roda dentada) **Definições**.

    ![][1]   

1.  Na parte superior do painel esquerdo, irá ver uma lista pendente que contém todas as contas Microsoft nas quais tem sessão iniciada. 

    ![][3]
     
1.  Selecione a seta para baixo junto à lista pendente para ver todas as contas Microsoft com sessão iniciada, bem como uma ligação para adicionar (iniciar sessão nas) contas adicionais da Microsoft.

    ![][4]

1.  Selecione a conta Microsoft pretendida na lista pendente.

1.  O painel esquerdo apresenta todas as subscrições Azure associadas à conta Microsoft selecionada.
A caixa de verificação à esquerda de cada subscrição Azure permite-lhe especificar se pretende ou não que o Explorador de Armazenamento (Pré-visualização) liste todas as contas do Storage associadas a essas subscrições Azure. Marque/desmarque **Todas as subscrições** alterna entre selecionar todas ou nenhuma das subscrições Azure listadas.

    ![][2]  

1.  Quando terminar de selecionar as subscrições do Azure que pretende gerir, selecione **Aplicar**. O painel esquerdo será atualizado para listar todas as contas do Storage para cada subscrição do Azure selecionada para a conta Microsoft atual. 

### Adicionar mais contas Microsoft

Os seguintes passos guiá-lo-ão ao longo do processo de ligação a contas adicionais da Microsoft para ver as subscrições Azure e as contas do Storage de cada conta.

1.  Selecione o ícone (de roda dentada) **Definições**.

    ![][1]   

1.  Na parte superior do painel esquerdo, irá ver uma lista pendente que contém todas as contas Microsoft nas quais tem sessão iniciada.

    ![][3]
     
1.  Selecione a seta para baixo junto à lista pendente para ver todas as contas Microsoft com sessão iniciada, bem como uma ligação para adicionar (iniciar sessão nas) contas adicionais da Microsoft.

    ![][4]

1.  Selecione **Adicionar uma conta** e siga as caixas de diálogo para iniciar sessão numa conta associada a pelo menos uma subscrição Azure ativa.

1.  Selecione as caixas de verificação para as subscrições Azure que pretende procurar. 

    ![][2]  

1.  Selecione **Aplicar**.

### Alternar entre contas Microsoft

Enquanto pode ligar a várias contas Microsoft, o painel esquerdo apresenta apenas as contas do Storage associadas às subscrições para uma única conta Microsoft (atual). Se ligar a várias contas Microsoft, pode alternar entre as contas, efetuando os seguintes passos:

1.  Selecione o ícone (de roda dentada) **Definições**.

    ![][1]   

1.  Na parte superior do painel esquerdo, irá ver uma lista pendente que contém todas as contas Microsoft nas quais tem sessão iniciada.

    ![][3]
     
1.  Selecione a seta para baixo junto à lista pendente para ver todas as contas Microsoft com sessão iniciada, bem como uma ligação para adicionar (iniciar sessão nas) contas adicionais da Microsoft.

    ![][4]

1.  Selecione a conta Microsoft pretendida.

1.  Selecione as caixas de verificação para as subscrições Azure que pretende procurar. 

    ![][2]  

1.  Selecione **Aplicar**.
  
## Ligar ao armazenamento local

O Explorador de Armazenamento (Pré-visualização) permite-lhe funcionar com o armazenamento local utilizando o Emulador do Storage do Azure. Isto permite-lhe escrever código contra e testar o armazenamento sem ter necessariamente uma conta do Storage implementada no Azure (uma vez que a conta do Storage está a ser emulada pelo Emulador do Storage do Azure).

>[AZURE.NOTE] Atualmente, o Emulador do Storage do Azure é suportado apenas para o Windows. 

1. Inicie o Explorador de Armazenamento (Pré-visualização). 

1. No painel esquerdo, expanda o nó **(Desenvolvimento)**.

    ![][21]

1. Se ainda não instalou o Emulador do Storage do Azure, será solicitado a fazê-lo através de uma barra de informações. Se a barra de informações for apresentada, selecione **Transferir a versão mais recente** e instale o emulador. 

    ![][22]

1. Assim que o emulador estiver instalado, terá a possibilidade de criar e de trabalhar com tabelas, filas e blobs locais. Para saber como trabalhar com cada tipo de conta do Storage, selecione na ligação adequada abaixo:

    - [Gerir recursos do Blob Storage do Azure](./vs-azure-tools-storage-explorer-blobs.md)
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

    ![][5]
    
1.  No painel **Chaves de acesso**, copie os valores **NOME DA CONTA DO STORAGE** e **CHAVE 1** para utilização quando anexar à conta do Storage. 

    ![][6]

### Anexar a uma conta do Storage externo

1.  No Explorador de Armazenamento (Pré-visualização), clique com o botão direito do rato em **Contas do Storage** e, no menu de contexto, selecione **Anexar Armazenamento Externo**.

    ![][7]
    
1.  A secção *Obter as credenciais da conta do Storage* explica como obter os valores nome da conta do Storage e chave 1. Esses valores serão utilizados neste passo. Na caixa de diálogo **Anexar Armazenamento Externo**, introduza o nome da conta do Storage na caixa **Nome da conta** e o valor de Chave 1 na caixa **Chave da conta**. Selecione **OK** quando terminar. 

    ![][8]

    Depois de anexada, será apresentada a conta do Storage externo com o texto **(Externo)** acrescentado ao nome da conta do Storage. 

    ![][9]

### Desanexar de uma conta do Storage externo

1.  Clique com o botão direito do rato na conta do Storage externo que pretende desanexar e, no menu de contexto, selecione **Desanexar**.

    ![][10]

1.  Quando for apresentada a caixa de mensagem de confirmação, selecione **Sim** para confirmar a desanexação da conta do Storage externo.

    ![][12]

## Anexar conta através da SAS

Uma SAS (Assinatura de Acesso Partilhado) fornece ao administrador de uma subscrição Azure a capacidade de conceder acesso a uma conta do Storage de forma temporária sem ter de fornecer as credenciais de subscrição Azure. 

Para ilustrar isto, vamos supor que o Utilizador A é um administrador de uma subscrição Azure e o Utilizador A pretende permitir que o Utilizador B aceda a uma conta do Storage por um período limitado de tempo com determinadas permissões:

1. O Utilizador A gera uma SAS (que consiste em cadeia de ligação para a conta do Storage) durante um período de tempo específico e com as permissões pretendidas.
1. O Utilizador A partilha o SAS com a pessoa que pretende acesso à conta do Storage – o Utilizador B, no nosso exemplo.  
1. O Utilizador B utiliza o Explorador de Armazenamento (Pré-visualização) para anexar à conta que pertence ao Utilizador A através da SAS fornecida. 

### Obter uma SAS para a conta que pretende partilhar

1.  Abrir o Explorador de Armazenamento (Pré-visualização).
1.  No painel esquerdo, clique com o botão direito na conta do Storage que pretende partilhar e, no menu de contexto, selecione **Obter Assinatura de Acesso Partilhado**.

    ![][13]

1. Na caixa de diálogo **Assinatura de Acesso Partilhado**, especifique o período de tempo e as permissões que pretende para a conta e selecione **Criar**.

    ![][14]
 
1. Uma segunda caixa de diálogo **Assinatura de Acesso Partilhado** aparecerá a apresentar a SAS. Selecione **Copiar** junto a **Cadeia de Ligação** para a copiar para a área de transferência. Selecione **Fechar** para dispensar a caixa de diálogo.

### Ligar à conta partilhada utilizando a SAS

1.  Abrir o Explorador de Armazenamento (Pré-visualização).
1.  No painel esquerdo, clique com o botão direito em **Contas do Storage** e, no menu de contexto, selecione **Ligar conta através da SAS**.
    ![][15]

1. Na caixa de diálogo **Anexar Conta através da SAS**:

    - **Nome da Conta** – introduza o nome que pretende associado a esta conta. **NOTA:** o nome da conta não tem de corresponder ao nome da conta do Storage original para o qual a SAS foi gerada. 
    - **Cadeia de Ligação** – cole a cadeia de ligação que copiou anteriormente.
    - Selecione **OK** quando terminar.
    
    ![][16]

Depois de anexada, a conta do Storage será apresentada com o texto (SAS) acrescentado ao nome da conta que forneceu.

![][17]

## Anexar serviço através da SAS

A secção [Anexar conta através da SAS](#attach-account-using-sas) ilustra como um administrador da subscrição Azure pode conceder acesso temporário a uma conta do Storage ao gerar (e partilhar) uma SAS para a conta do Storage. Do mesmo modo, é possível gerar uma SAS para um serviço específico (contentor de blobs, filas ou tabelas) dentro de uma conta do Storage.  

### Gerar uma SAS para o serviço que pretende partilhar

Neste contexto, um serviço pode ser um contentor de blobs, de filas ou tabelas. As secções seguintes explicam como gerar a SAS para o serviço listado:

- [Obter a SAS para um contentor de blobs](./vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)
- Obter a SAS para uma fila - *Brevemente*
- Obter a SAS para uma tabela - *Brevemente*

### Anexar ao serviço da conta partilhada através da SAS

1.  Abrir o Explorador de Armazenamento (Pré-visualização).
1.  No painel esquerdo, clique com o botão direito em **Contas do Storage** e, no menu de contexto, selecione **Anexar serviço através da SAS**.
    ![][18]

1. Na caixa de diálogo **Anexar Conta através da SAS**, cole o URI de SAS que copiou anteriormente e selecione **OK**.

    ![][19]

Quando estiver anexado, o serviço anexado recentemente será apresentado sob o nó **(Serviço SAS)**. 

![][20]

## Procurar contas do Storage

Se tiver uma longa lista de contas do Storage, uma forma rápida de localizar uma conta do Storage específico consiste em utilizar a caixa de pesquisa na parte superior do painel esquerdo. 

Ao escrever na caixa de pesquisa, o painel esquerdo irá apresentar apenas as contas do Storage que correspondem ao valor de pesquisa que introduziu até esse ponto. A captura de ecrã seguinte ilustra um exemplo em que pesquisei todas as contas do Storage em que o nome da conta do Storage contém o texto “tarcher”.

![][11]
    
Para limpar a pesquisa, selecione o botão **x** na caixa de pesquisa.

## Passos seguintes
- [Gerir os recursos do Blob Storage do Azure com o Explorador de Armazenamento (Pré-visualização)](./vs-azure-tools-storage-explorer-blobs.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/settings-gear.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/filter-subscriptions.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/filter-accounts.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/accounts-drop-down.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys.png
[6]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys-copy.png
[7]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-external-storage.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-external-storage-dlg.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/external-storage-account.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-account-search.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage-confirmation.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-context-menu.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-dlg1.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-account-using-sas-context-menu.png
[16]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-account-using-sas-dlg.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-account-using-sas-finished.png
[18]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-service-using-sas-context-menu.png
[19]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-service-using-sas-dlg.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-service-using-sas-finished.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/local-storage-drop-down.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/download-storage-emulator.png



<!--HONumber=Jun16_HO2-->


