---
title: Carregar o ficheiro VHD para o Azure DevTest Labs utilizando o Explorador de armazenamento do Microsoft Azure | Microsoft Docs
description: "Carregar o ficheiro VHD a conta de armazenamento do laboratório utilizando o Explorador de armazenamento do Microsoft Azure"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: tarcher
ms.openlocfilehash: 502e2536fb0fd2e9dfc4c7b85a6fb4e18202f38f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="upload-vhd-file-to-labs-storage-account-using-microsoft-azure-storage-explorer"></a>Carregar o ficheiro VHD a conta de armazenamento do laboratório utilizando o Explorador de armazenamento do Microsoft Azure

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

No Azure DevTest Labs, os ficheiros VHD podem ser utilizados para criar imagens personalizadas, que são utilizadas para aprovisionar as máquinas virtuais. Este artigo ilustra como utilizar [Explorador de armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) para carregar um ficheiro VHD para a conta de armazenamento de um laboratório. Depois de carregar o ficheiro VHD, o [passos secção](#next-steps) lista alguns artigos que mostram como criar uma imagem personalizada do ficheiro VHD carregado. Para obter mais informações sobre discos e VHDs no Azure, consulte [sobre discos e VHD para as máquinas virtuais](../virtual-machines/linux/about-disks-and-vhds.md)

## <a name="step-by-step-instructions"></a>Instruções passo a passo

Os seguintes passos guiá-lo através do carregar um ficheiro VHD utilizando DevTest Labs [Explorador de armazenamento do Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md).

1. [Transfira e instale a versão mais recente do Explorador de armazenamento do Microsoft Azure](http://www.storageexplorer.com).

1. Obter o nome do laboratório conta do storage no portal do Azure:

    1. Inicie sessão no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
    
    1. Selecione **Mais serviços**, e, em seguida, selecione **DevTest Labs** na lista.
    
    1. Na lista de laboratórios, selecione o laboratório pretendido.  
    
    1. No painel do laboratório, selecione **configuração**. 
    
    1. No laboratório **configuração** painel, selecione **imagens personalizadas (VHDs)**.
    
    1. No **imagens personalizadas** painel, selecione **+ adicionar**. 
    
    1. No **imagem personalizada** painel, selecione **VHD**.
    
    1. No **VHD** painel, selecione **carregar um VHD com o PowerShell**.
    
        ![Carregar o VHD com o PowerShell][0]
    
    1. O **carregar uma imagem através do PowerShell** painel mostra uma chamada para o **Add-AzureVhd** cmdlet. O primeiro parâmetro (*destino*) contém o nome de conta de armazenamento para o laboratório no seguinte formato:
    
        https://<STORAGE-ACCOUNT-name>.blob.Core.Windows.NET/Uploads/... 

    1. Anote o nome da conta de armazenamento porque está a ser utilizado em passos posteriores.
    
1. Ligar a uma conta de subscrição do Azure utilizando o Explorador de armazenamento.

    > [!TIP] 
    > 
    > Explorador de armazenamento suporta várias opções de ligação. Esta secção ilustra a ligação a uma conta de armazenamento associada à subscrição do Azure. Para ver as opções de ligação suportadas pelo Explorador de armazenamento, consulte o artigo, [introdução ao Explorador de armazenamento](../vs-azure-tools-storage-manage-with-storage-explorer.md).
 
    1. Abra o Explorador de armazenamento.
    
    1. No Explorador de armazenamento, selecione **definições de conta do Azure**. 
    
        ![Definições de conta do Azure][1]
    
    1. O painel esquerdo apresenta as contas Microsoft que tenha sessão iniciada. Para ligar a outra conta, selecione **Adicionar uma conta** e siga as caixas de diálogo para iniciar sessão com uma conta Microsoft que esteja associada a, pelo menos, uma subscrição Azure ativa.
    
        ![Adicionar uma conta][2]
    
    1. Quando inicia sessão com êxito numa conta Microsoft, o painel esquerdo será preenchido com as subscrições do Azure associadas a essa conta. Selecione as subscrições do Azure com as quais pretende trabalhar e, em seguida, selecione **Aplicar**. (Selecionar **todas as subscrições** activa/desactiva a seleção de todas ou nenhuma das subscrições Azure listadas.)
    
        ![Selecionar subscrições do Azure][3]
    
    1. O painel esquerdo apresenta as contas de armazenamento associadas às subscrições do Azure selecionadas.
    
        ![Subscrições do Azure selecionadas][4]

1. Localize a conta de armazenamento de laboratório:

    1. No painel esquerdo do Explorador de armazenamento, localize e expanda o nó para a subscrição do Azure que detém o laboratório.
    
    1. No nó da subscrição, expanda **contas do Storage**.

    1. Expanda o nó de conta de armazenamento de laboratório para revelar nós para **contentores de BLOBs**, **partilhas de ficheiros**, **filas**, e **tabelas**.
    
    1. Expanda o **contentores de BLOBs** nós.
    
    1. Selecione o contentor de blob carregamentos para apresentar o respetivo conteúdo no painel direito.
        
        ![Carregar o diretório][5]

1. Carregar o ficheiro VHD utilizando o Explorador de armazenamento:

    1. No painel da direita Explorador de armazenamento, deverá ver uma lista de blobs a **carrega** contentor do blob da conta de armazenamento de laboratório. Na barra de ferramentas do editor de blob, selecione **carregar** 
        
        ![Carregar no botão][6]
    
    1. Do **carregar** menu pendente, selecione **carregar ficheiros...** .
    
    1. No **carregar ficheiros** caixa de diálogo, selecione o botão de reticências.
        
        ![Selecione o ficheiro][8]  

    1. No **selecionar ficheiros para carregar** caixa de diálogo, procure o ficheiro VHD pretendido, selecione-o e, em seguida, selecione **abra**.
    
    1. Quando devolvido para o **carregar ficheiros** caixa de diálogo, alteração **Blob tipo** para **BLOBs de páginas**.
    
    1. Selecione **Upload**.

        ![Selecione o ficheiro][9]  
    
    1. O Explorador de armazenamento **registo de atividade** painel mostra o estado de transferência (juntamente com hiperligações para cancelar o carregamento). O processo de carregamento de um ficheiro VHD pode ser demorado, consoante o tamanho do ficheiro VHD e a velocidade da ligação. 

        ![Estado de carregamento do ficheiro][10]  

## <a name="next-steps"></a>Passos seguintes

- [Criar uma imagem personalizada no Azure DevTest Labs de um ficheiro VHD utilizando o portal do Azure](devtest-lab-create-template.md)
- [Criar uma imagem personalizada no Azure DevTest Labs de um ficheiro VHD utilizando o PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)

[0]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-image-using-psh.png
[1]: ./media/devtest-lab-upload-vhd-using-storage-explorer/settings-icon.png
[2]: ./media/devtest-lab-upload-vhd-using-storage-explorer/add-account-link.png
[3]: ./media/devtest-lab-upload-vhd-using-storage-explorer/subscriptions-list.png
[4]: ./media/devtest-lab-upload-vhd-using-storage-explorer/storage-accounts-list.png
[5]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-dir.png
[6]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-button.png
[7]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-files.png
[8]: ./media/devtest-lab-upload-vhd-using-storage-explorer/select-file.png
[9]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-file.png
[10]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-status.png
