---
title: "Introdução ao Explorador de Armazenamento (Pré-visualização) | Microsoft Docs"
description: "Gerir os recursos do Storage do Azure com o Explorador de Armazenamento (Pré-visualização)"
services: storage
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: 1ed0f096-494d-49c4-ab71-f4164ee19ec8
ms.service: storage
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/18/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: 07b62cd6f6deb0cf3ff1c806204ebc26c773a164
ms.lasthandoff: 04/13/2017


---
# <a name="getting-started-with-storage-explorer-preview"></a>Introdução ao Explorador de Armazenamento (Pré-visualização)
## <a name="overview"></a>Descrição geral
O Explorador de Armazenamento do Microsoft Azure (Pré-visualização) é uma aplicação autónoma que lhe permite trabalhar facilmente com dados do Armazenamento do Azure no Windows, macOS e Linux. Neste artigo, irá aprender as várias formas de ligar e gerir as contas de armazenamento do Azure.

![Explorador de Armazenamento do Microsoft Azure (pré-visualização)][15]

## <a name="prerequisites"></a>Pré-requisitos
* [Transfira e instale o Explorador de Armazenamento (pré-visualização)](http://www.storageexplorer.com)

## <a name="connect-to-a-storage-account-or-service"></a>Ligar a um serviço ou a uma conta do Storage
O Explorador de Armazenamento (Pré-visualização) fornece várias formas de ligar às contas de armazenamento. Isto inclui ligar a contas de armazenamento associadas às subscrições do Azure, ligar a contas de armazenamento e serviços partilhados de outras subscrições do Azure e até ligar e gerir o armazenamento local com o Emulador de Armazenamento do Azure. Além disso, pode trabalhar com contas de armazenamento no Azure global e nacional:

* [Ligar a uma subscrição Azure](#connect-to-an-azure-subscription) – Gira os recursos de armazenamento que pertencem à sua subscrição do Azure.
* [Trabalhar com o armazenamento de desenvolvimento local](#work-with-local-development-storage) – Faça a gestão de armazenamento local utilizando o Emulador de armazenamento do Microsoft Azure.
* [Anexar ao armazenamento externo](#attach-or-detach-an-external-storage-account) – Gira recursos de armazenamento que pertencem a outra subscrição do Azure ou a nuvens do Azure nacional com a chave, o nome e os pontos finais da conta de armazenamento.
* [Anexar conta de armazenamento através da SAS](#attach-storage-account-using-sas) – Faça a gestão de recursos de armazenamento que pertencem a outra subscrição Azure com uma SAS.
* [Anexar serviço através da SAS](#attach-service-using-sas) – Gira um serviço de armazenamento específico (contentor de blobs, filas ou tabelas) que pertence a outra subscrição do Azure com uma SAS.

## <a name="connect-to-an-azure-subscription"></a>Ligar a uma subscrição do Azure
> [!NOTE]
> Se não tiver uma conta do Azure, pode [inscrever-se numa avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) ou [ativar os benefícios de subscritor do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).
>
>

1. No Explorador de Armazenamento (pré-visualização), selecione **Definições de conta do Azure**.

    ![Definições de conta do Azure][0]
2. O painel esquerdo apresenta todas as contas Microsoft nas quais tenha sessão iniciada. Para ligar a outra conta, selecione **Adicionar uma conta** e siga as caixas de diálogo para iniciar sessão com uma conta Microsoft que esteja associada a, pelo menos, uma subscrição Azure ativa.
> [!NOTE]
>Atualmente, não pode ligar ao Azure nacional, como o Black Forest Azure, o Fairfax Azure e o Mooncake Azure através do início de sessão. Consulte a secção **Attach or detach an external storage account (Anexar ou desanexar uma conta de armazenamento externa)**, sobre como ligar a contas de armazenamento do Azure nacional.

3. Quando inicia sessão com êxito numa conta Microsoft, o painel esquerdo será preenchido com as subscrições do Azure associadas a essa conta. Selecione as subscrições do Azure com as quais pretende trabalhar e, em seguida, selecione **Aplicar**. (Selecionar **Todas as subscrições** alterna entre selecionar todas ou nenhuma das subscrições Azure listadas.)

    ![Selecionar subscrições do Azure][3]
4. O painel esquerdo apresenta as contas de armazenamento associadas às subscrições do Azure selecionadas.

    ![Subscrições do Azure selecionadas][4]

## <a name="connect-to-an-azure-stack-subscription"></a>Ligar a uma subscrição do Azure Stack

1. A ligação VPN é necessária para o Explorador de Armazenamento aceder à subscrição do Azure Stack remotamente. Para saber mais sobre como configurar a ligação VPN ao Azure Stack, consulte [Ligar o Azure Stack ao VPN](azure-stack/azure-stack-connect-azure-stack.md#connect-with-vpn)

2. Para o POC do Azure Stack, terá de exportar o certificado de raiz da autoridade do Azure Stack. Abra o `mmc.exe` no MAS-CON01, a máquina de anfitrião ou a máquina local do Azure Stack com uma ligação de VPN para o Azure Stack. No **Ficheiro**, selecione **Adicionar/Remover Snap-in**, adicione **Certificados** para gerir a **Conta de computador** do **Computador Local**.

   ![Carregue o certificado de raiz do Azure stack através de mmc.exe][25]   

   Encontre **AzureStackCertificationAuthority** em **Raiz da Consola\Certificado (Computador Local)\Autoridades de Certificação de Raiz Fidedigna\Certificados**. Clique com o botão direito do rato no item, selecione **Todas as tarefas -> Exportação**. Em seguida, siga as caixas de diálogo para exportar o certificado com **X.509 codificado com Base-64 (.CER)**. O certificado exportado será utilizado no próximo passo.   

   ![Exporte o certificado de raiz da autoridade do Azure stack de raiz][26]   

3. No Explorador de Armazenamento (Pré-visualização), selecione o menu **Editar** e, em seguida, **Certificados SSL** e **Importar Certificados**. Utilize a caixa de diálogo do selecionador de ficheiros para encontrar e abrir o certificado explorado no passo anterior. Depois de importar ser-lhe-á solicitado para reiniciar o Explorador de Armazenamento.

   ![Importe o certificado para o Explorador de Armazenamento (Pré-visualização)][27]

4. Depois de reiniciar o Explorador de Armazenamento (Pré-visualização), selecione o menu **Editar** e certifique-se de que o **Azure Stack de destino** está selecionado. Caso contrário, assinale-o e reinicie o Explorador de Armazenamento para que a alteração entre em vigor. Esta configuração é necessária para ser compatível com o seu ambiente do Azure Stack.

   ![Certifique-se de que o Azure Stack de destino está selecionado][28]

5. Na barra lateral esquerda, selecione **Gerir Contas**. O painel esquerdo apresenta todas as contas Microsoft nas quais tem sessão iniciada. Para ligar à conta do Azure Stack, selecione **Adicionar uma conta**.

   ![Adicionar uma conta do Azure Stack][29]

6. Escolha **Criar Ambiente Personalizado** em **Ambiente do Azure** na caixa de diálogo **Adicionar nova conta** e, em seguida, clique em **Seguinte**.

7. Introduza todas as informações necessárias do ambiente personalizado do Azure Stack e, em seguida, clique em **Iniciar sessão**.  Preencha a caixa de diálogo **Iniciar sessão num ambiente de Cloud Personalizado** para iniciar sessão com a conta do Azure Stack que está associada a, pelo menos, uma subscrição ativa do Azure Stack. Os detalhes para cada campo na caixa de diálogo são os seguintes:

    * **Nome do ambiente** – o campo pode ser personalizado por utilizador.
    * **Autoridade** – o valor deve ser https://login.windows.net. Para o Azure China (Mooncake), utilize https://login.chinacloudapi.cn.
    * **ID de recurso de início de sessão** – obtenha o valor ao executar o PowerShell seguinte:

    Se é Administrador da Cloud:

    ```powershell
    PowerShell (Invoke-RestMethod -Uri https://adminmanagement.local.azurestack.external/metadata/endpoints?api-version=1.0 -Method Get).authentication.audiences[0]
    ```

    Se é Inquilino:

    ```powershell
    PowerShell (Invoke-RestMethod -Uri https://management.local.azurestack.external/metadata/endpoints?api-version=1.0 -Method Get).authentication.audiences[0]
    ```

    * **Ponto final do gráfico** – o valor deve ser https://graph.windows.net. Para o Azure China (Mooncake), utilize https://graph.chinacloudapi.cn.
    * **Id do recurso ARM** – utilize o mesmo valor que o ID de recurso de início de sessão.
    * **Ponto final do recurso ARM** – os exemplos do ponto final do recurso ARM:

    Para o Administrador da Cloud: https://adminmanagement.local.azurestack.external   
    Para o Inquilino: https://management.local.azurestack.external
 
    * **Ids de Inquilino** – opcional. O valor é atribuído apenas quando o diretório tem de ser especificado.

8. Quando inicia sessão com êxito numa conta do Azure Stack, o painel esquerdo será preenchido com as subscrições do Azure Stack associadas a essa conta. Selecione as subscrições do Azure Stack com as quais pretende trabalhar e, em seguida, selecione **Aplicar**. (Selecionar **Todas as subscrições** alterna entre selecionar todas ou nenhuma das subscrições Azure Stack listadas.)

   ![Selecione as subscrições do Azure stack depois de preencher a caixa de diálogo de Ambiente de Cloud Personalizado][30]

9. O painel esquerdo apresenta as contas de armazenamento associadas às subscrições do Azure Stack selecionadas.

   ![Lista de contas de armazenamento, incluindo as contas de subscrição do Azure stack][31]

## <a name="work-with-local-development-storage"></a>Trabalhar com armazenamento de desenvolvimento local
O Explorador de Armazenamento (Pré-visualização) permite-lhe funcionar com o armazenamento local utilizando o Emulador do Storage do Azure. Isto permite-lhe escrever código contra e testar o armazenamento sem ter necessariamente uma conta do Storage implementada no Azure (uma vez que a conta do Storage está a ser emulada pelo Emulador do Storage do Azure).

> [!NOTE]
> Atualmente, o Emulador do Storage do Azure é suportado apenas para o Windows.
>
>

1. No painel da esquerda do Explorador de Armazenamento (pré-visualização), expanda o nó **(Contas de Armazenamento** > **Locais e Anexadas** > **(Desenvolvimento)**.

    ![Nó de desenvolvimento local][21]
2. Se ainda não instalou o Emulador de Armazenamento do Azure, será solicitado a fazê-lo através de uma barra de informações. Se a barra de informações for apresentada, selecione **Transferir a versão mais recente** e instale o emulador.

    ![Transferir o pedido de Emulador de Armazenamento do Azure][22]
3. Assim que o emulador estiver instalado, tem a possibilidade de criar e de trabalhar com tabelas, filas e blobs locais. Para saber como trabalhar com cada tipo de conta de armazenamento, selecione uma das ligações seguintes:

   * [Gerir recursos do armazenamento de blobs do Azure](vs-azure-tools-storage-explorer-blobs.md)
   * Gerir recursos de armazenamento de partilha de ficheiros do Azure - *Brevemente*
   * Gerir recursos de armazenamento de filas do Azure – *Brevemente*
   * Gerir recursos de Table Storage do Azure – *Brevemente*

## <a name="attach-or-detach-an-external-storage-account"></a>Anexar ou desanexar uma conta do Storage externo
Explorador de Armazenamento (Pré-visualização) fornece a capacidade para anexar a contas do Storage externo para que as contas do Storage possam ser partilhadas facilmente. Esta secção explica como anexar (e desanexar) de contas do Storage externo.

### <a name="get-the-storage-account-credentials"></a>Obter as credenciais da conta do Storage
Para partilhar uma conta de armazenamento externa, o proprietário dessa conta tem primeiro de obter as credenciais – nome de conta e chave – para a conta e, em seguida, partilhar essas informações com a pessoa que pretende anexar a essa conta (externa). Obter as credenciais da conta do Storage pode ser efetuado através do Portal do Azure ao seguir estes passos:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Selecione **Procurar**.
3. Selecione **Contas do Storage**.
4. No painel **Contas do Storage**, selecione a conta do Storage pretendida.
5. No painel **Definições** para a conta do Storage selecionada, selecione **Chaves de acesso**.

   ![Opção Chaves de acesso][5]
6. No painel **Chaves de acesso**, copie os valores **NOME DA CONTA DO STORAGE** e **CHAVE 1** para utilização quando anexar à conta do Storage.

   ![Chaves de acesso][6]

### <a name="attach-to-an-external-storage-account"></a>Anexar a uma conta do Storage externo
Para anexar a uma conta de armazenamento externa, é necessário o nome e a chave da conta. A secção *Obter as credenciais da conta de armazenamento* explica como obter estes valores a partir do portal do Azure. No entanto, tenha em atenção que no portal a chave de conta é designada por «chave 1», por isso se o Explorador de Armazenamento (Pré-visualização) pedir-lhe uma chave de conta, terá de introduzir (ou colar) o valor de "chave 1".

1. No Explorador de Armazenamento (Pré-visualização), selecione **Ligar ao armazenamento do Azure**.

   ![Ligar à opção de armazenamento do Azure][23]
2. Na caixa de diálogo **Ligar ao armazenamento do Azure**, especifique a chave de conta (valor «chave 1» do portal do Azure) e, em seguida, selecione **Seguinte**.
> [!NOTE]
> Pode introduzir a cadeia de Ligação de Armazenamento a partir de uma conta de armazenamento no Azure nacional. Por exemplo, introduza cadeias de ligação semelhantes ao seguinte para ligar a contas de armazenamento do Azure Black Forest: DefaultEndpointsProtocol=https;AccountName=cawatest03;AccountKey=<storage_account_key>;EndpointSuffix=core.cloudapi.de; Obtém a cadeia de ligação a partir do portal do Azure da mesma forma, tal como descrito na secção **Get the storage account credentials (Obter as credenciais da conta de armazenamento)**

   ![Ligar ao diálogo de armazenamento do Azure][24]

3. Na caixa de diálogo **Anexar Armazenamento Externo**, introduza o nome da conta de armazenamento na caixa **Nome da conta**, especifique as definições pretendidas e selecione **Seguinte** quando terminar.

   ![Anexar a caixa de diálogo de armazenamento externo][8]
4. Na caixa de diálogo **Resumo da Ligação**, verifique as informações. Se quiser alterar alguma informação, selecione **Voltar** e reintroduza as definições pretendidas. Depois de terminar, selecione **Ligar**.
5. Depois de ligada, a conta de armazenamento externa será apresentada com o texto **(Externo)** anexado ao nome da conta de armazenamento.

   ![Resultado da ligação a uma conta de armazenamento externo][9]

### <a name="detach-from-an-external-storage-account"></a>Desanexar de uma conta do Storage externo
1. Clique com o botão direito do rato na conta do Storage externo que pretende desanexar e, no menu de contexto, selecione **Desanexar**.

   ![Anular a exposição da opção de armazenamento][10]
2. Quando for apresentada a caixa de mensagem de confirmação, selecione **Sim** para confirmar a desanexação da conta do Storage externo.

## <a name="attach-storage-account-using-sas"></a>Anexar conta de armazenamento através da SAS
Uma [SAS (Assinatura de Acesso Partilhado)](storage/storage-dotnet-shared-access-signature-part-1.md) fornece ao administrador de uma subscrição Azure a capacidade de conceder acesso a uma conta de armazenamento de forma temporária sem ter de fornecer as credenciais de subscrição do Azure.

Para ilustrar isto, vamos supor que o Utilizador A é um administrador de uma subscrição Azure e o Utilizador A pretende permitir que o Utilizador B aceda a uma conta do Storage por um período limitado de tempo com determinadas permissões:

1. O Utilizador A gera uma SAS (que consiste em cadeia de ligação para a conta do Storage) durante um período de tempo específico e com as permissões pretendidas.
2. O Utilizador A partilha o SAS com a pessoa que pretende acesso à conta do Storage – o Utilizador B, no nosso exemplo.  
3. O Utilizador B utiliza o Explorador de Armazenamento (Pré-visualização) para anexar à conta que pertence ao Utilizador A através da SAS fornecida.

### <a name="get-a-sas-for-the-account-you-want-to-share"></a>Obter uma SAS para a conta que pretende partilhar
1. No Explorar de Armazenamento (Pré-visualização), clique com o botão direito na conta de armazenamento que pretende partilhar e, no menu de contexto, selecione **Obter Assinatura de Acesso Partilhado**.

   ![Obter a opção de menu de contexto SAS][13]
2. Na caixa de diálogo **Assinatura de Acesso Partilhado**, especifique o período de tempo e as permissões que pretende para a conta e selecione **Criar**.

    ![Obter a caixa de diálogo SAS][14]
3. Uma segunda caixa de diálogo **Assinatura de Acesso Partilhado** apresenta a SAS. Selecione **Copiar** junto a **Cadeia de Ligação** para a copiar para a área de transferência. Selecione **Fechar** para dispensar a caixa de diálogo.

### <a name="attach-to-the-shared-account-using-the-sas"></a>Ligar à conta partilhada utilizando a SAS
1. No Explorador de Armazenamento (Pré-visualização), selecione **Ligar ao armazenamento do Azure**.

   ![Ligar à opção de armazenamento do Azure][23]
2. Na caixa de diálogo **Ligar ao armazenamento do Azure**, especifique a cadeia de ligação e, em seguida, selecione **Seguinte**.

   ![Ligar ao diálogo de armazenamento do Azure][24]
3. Na caixa de diálogo **Resumo da Ligação**, verifique as informações. Se quiser alterar alguma informação, selecione **Voltar** e reintroduza as definições pretendidas. Depois de terminar, selecione **Ligar**.
4. Depois de anexada, a conta de armazenamento é apresentada com o texto (SAS) acrescentado ao nome da conta que forneceu.

   ![Resultado do anexado a uma conta utilizando SAS][17]

## <a name="attach-service-using-sas"></a>Anexar serviço através da SAS
A secção [Anexar conta de armazenamento através da SAS](#attach-storage-account-using-sas) ilustra como um administrador da subscrição Azure pode conceder acesso temporário a uma conta de armazenamento ao gerar (e partilhar) uma SAS para a conta de armazenamento. Do mesmo modo, é possível gerar uma SAS para um serviço específico (contentor de blobs, filas ou tabelas) dentro de uma conta do Storage.  

### <a name="generate-a-sas-for-the-service-you-want-to-share"></a>Gerar uma SAS para o serviço que pretende partilhar
Neste contexto, um serviço pode ser um contentor de blobs, de filas ou tabelas. As secções seguintes explicam como gerar a SAS para o serviço listado:

* [Obter a SAS para um contentor de blobs](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)
* Obter a SAS para uma partilha de ficheiro - *Brevemente*
* Obter a SAS para uma fila - *Brevemente*
* Obter a SAS para uma tabela - *Brevemente*

### <a name="attach-to-the-shared-account-service-using-the-sas"></a>Anexar ao serviço da conta partilhada através da SAS
1. No Explorador de Armazenamento (Pré-visualização), selecione **Ligar ao armazenamento do Azure**.

   ![Ligar à opção de armazenamento do Azure][23]
2. Na caixa de diálogo **Ligar ao armazenamento do Azure**, especifique a URI da SAS e, em seguida, selecione **Seguinte**.

   ![Ligar ao diálogo de armazenamento do Azure][24]
3. Na caixa de diálogo **Resumo da Ligação**, verifique as informações. Se quiser alterar alguma informação, selecione **Voltar** e reintroduza as definições pretendidas. Depois de terminar, selecione **Ligar**.
4. Depois de anexado, o serviço anexado recentemente é apresentado no nó **(Serviço SAS)**.

   ![Resultado da anexação a um serviço partilhado através de SAS][20]

## <a name="search-for-storage-accounts"></a>Procurar contas do Storage
Se tiver uma longa lista de contas do Storage, uma forma rápida de localizar uma conta do Storage específico consiste em utilizar a caixa de pesquisa na parte superior do painel esquerdo.

Ao escrever na caixa de pesquisa, o painel esquerdo apresenta apenas as contas de armazenamento que correspondem ao valor de pesquisa que introduziu até esse ponto. A captura de ecrã seguinte ilustra um exemplo em que pesquisei todas as contas do Storage em que o nome da conta do Storage contém o texto “tarcher”.

![Procurar conta de armazenamento][11]

Para limpar a pesquisa, selecione o botão **x** na caixa de pesquisa.

## <a name="next-steps"></a>Passos seguintes
* [Gerir os recursos do armazenamento de blobs do Azure com o Explorador de Armazenamento (Pré-visualização)](vs-azure-tools-storage-explorer-blobs.md)

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

