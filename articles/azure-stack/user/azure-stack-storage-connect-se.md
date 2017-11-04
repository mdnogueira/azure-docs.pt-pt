---
title: "Ligar o Explorador de armazenamento a uma subscrição de pilha do Azure"
description: "Saiba como ligar o armazenamento Exporer a uma subscrição de pilha do Azure"
services: azure-stack
documentationcenter: 
author: xiaofmao
manager: 
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: xiaofmao
ms.openlocfilehash: c7e6d70148d39fd74f6409a0a239833f8e9f7614
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/12/2017
---
# <a name="connect-storage-explorer-to-an-azure-stack-subscription"></a>Ligar o Explorador de armazenamento a uma subscrição de pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e Kit de desenvolvimento de pilha do Azure*

Explorador de armazenamento do Azure (pré-visualização) é uma aplicação autónoma que lhe permite trabalhar facilmente com dados de armazenamento de pilha do Azure no Windows, macOS e Linux. Existem várias ferramentas disponíveis para mover dados para e do armazenamento de pilha do Azure. Para obter mais informações, consulte [ferramentas para armazenamento de Azure pilha de transferência de dados](azure-stack-storage-transfer.md).

Neste artigo, irá aprender a estabelecer ligação às suas contas de armazenamento de pilha do Azure utilizando o Explorador de armazenamento. 

Se ainda não instalou o Explorador de armazenamento ainda, [transferir](http://www.storageexplorer.com/) e e instalá-la.

Depois de ligar à sua subscrição de pilha do Azure, pode utilizar o [artigos do Explorador de armazenamento do Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md) para trabalhar com os seus dados de pilha do Azure. 

## <a name="prepare-an-azure-stack-subscription"></a>Preparar uma subscrição de pilha do Azure

Precisa de acesso ao ambiente de trabalho da máquina de anfitrião de pilha do Azure ou uma ligação VPN para o Explorador de armazenamento para aceder à subscrição do Azure pilha. Para saber como configurar uma ligação VPN ao Azure Stack, veja [Connect to Azure Stack with VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) (Ligar ao Azure Stack com VPN).

Para o Kit de desenvolvimento de pilha do Azure, terá de exportar o certificado de raiz da autoridade de pilha do Azure.

### <a name="to-export-and-then-import-the-azure-stack-certificate"></a>Para exportar e, em seguida, importar o certificado de pilha do Azure

1. Abra `mmc.exe` uma máquina de anfitrião de pilha do Azure ou uma máquina local com uma ligação VPN à pilha do Azure. 

2. No **ficheiro**, selecione **Adicionar/Remover Snap-in**e, em seguida, adicione **certificados** para gerir **minha conta de utilizador**.



3. Em **consola Root\Certificated (computador Local) \Trusted Root Certification Authorities\Certificates** localizar **AzureStackSelfSignedRootCert**.

    ![Carregue o certificado de raiz do Azure stack através de mmc.exe][25]

4. O certificado com o botão direito, selecione **todas as tarefas** > **exportar**e, em seguida, siga as instruções para exportar o certificado com **x. 509 com codificação Base 64 (. CER)**.  

    O certificado exportado será utilizado no próximo passo.
5. Iniciar o Explorador de armazenamento (pré-visualização), e se vir o **ligar ao armazenamento de Azure** diálogo caixa, cancelá-lo.

6. No **editar** menu, aponte para **certificados SSL**e, em seguida, clique em **importar certificados**. Utilize a caixa de diálogo do selecionador de ficheiros para encontrar e abrir o certificado que foi exportado no passo anterior.

    Depois de importar, é-lhe pedido para reiniciar o Explorador de Armazenamento.

    ![Importe o certificado para o Explorador de Armazenamento (Pré-visualização)][27]

Agora, está pronto para estabelecer a ligação Explorador de armazenamento para uma subscrição de pilha do Azure.

### <a name="to-connect-an-azure-stack-subscription"></a>Ligar uma subscrição de pilha do Azure


1. Depois de reiniciar o Explorador de Armazenamento (Pré-visualização), selecione o menu **Editar** e confirme que está selecionado **Azure Stack de Destino**. Se não estiver, selecione-o e reinicie o Explorador de Armazenamento para que a alteração entre em vigor. Esta configuração é necessária para ser compatível com o seu ambiente do Azure Stack.

    ![Certifique-se de que o Azure Stack de destino está selecionado][28]

7. No painel esquerdo, selecione **Gerir Redes**.  
    São apresentadas todas as contas Microsoft em que tem sessão iniciada.

8. Para ligar à conta do Azure Stack, selecione **Adicionar uma conta**.

    ![Adicionar uma conta do Azure Stack][29]

9. No **ligar ao armazenamento de Azure** caixa de diálogo em **ambiente do Azure**, selecione **utilização do Azure pilha ambiente**e, em seguida, clique em **seguinte**.

10. Para iniciar sessão com a conta de pilha do Azure que está associada a pelo menos uma subscrição de pilha do Azure Active Directory, preencha o **inicie sessão no ambiente do Azure pilha** caixa de diálogo.  

    Os detalhes de cada campo são os seguintes:

    * **Nome do ambiente**: o campo pode ser personalizado pelo utilizador.
    * **Ponto final de recurso do ARM**: os exemplos dos pontos finais do recurso do Azure Resource Manager:

        * Para o operador de cloud:<br> https://adminmanagement.local.azurestack.external   
        * Para o inquilino:<br> https://Management.local.azurestack.external
 
    * **Id de inquilino**: opcional. O valor é atribuído apenas quando o diretório tem de ser especificado.

12. Depois de iniciar sessão com êxito numa conta do Azure Stack, o painel esquerdo é preenchido com as subscrições do Azure Stack associadas a essa conta. Selecione as subscrições do Azure Stack com as quais pretende trabalhar e, em seguida, selecione **Aplicar**. (Selecionar ou limpar a caixa de verificação **Todas as subscrições** alterna entre selecionar todas ou nenhuma das subscrições do Azure Stack listadas.)

    ![Selecione as subscrições do Azure Stack depois de preencher a caixa de diálogo de Ambiente de Cloud Personalizado][30]  
    O painel esquerdo apresenta as contas de armazenamento associadas às subscrições do Azure Stack selecionadas.

    ![Lista das contas de armazenamento, incluindo as contas de subscrição do Azure Stack][31]

## <a name="next-steps"></a>Passos seguintes
* [Introdução ao Explorador de armazenamento (pré-visualização)](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Armazenamento do Azure da pilha: diferenças e as considerações](azure-stack-acs-differences.md)


* Para saber mais sobre o Storage do Azure, consulte [introdução ao Storage do Microsoft Azure](../../storage/common/storage-introduction.md)

[25]: ./media/azure-stack-storage-connect-se/add-certificate-azure-stack.png
[26]: ./media/azure-stack-storage-connect-se/export-root-cert-azure-stack.png
[27]: ./media/azure-stack-storage-connect-se/import-azure-stack-cert-storage-explorer.png
[28]: ./media/azure-stack-storage-connect-se/select-target-azure-stack.png
[29]: ./media/azure-stack-storage-connect-se/add-azure-stack-account.png
[30]: ./media/azure-stack-storage-connect-se/select-accounts-azure-stack.png
[31]: ./media/azure-stack-storage-connect-se/azure-stack-storage-account-list.png
