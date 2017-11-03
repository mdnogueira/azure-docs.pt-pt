---
title: Repor uma Windows palavra-passe local sem agente do Azure | Microsoft Docs
description: "Como repor a palavra-passe de uma conta de utilizador do Windows local quando o agente convidado do Azure não está instalado nem funcionar numa VM"
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: cf353dd3-89c9-47f6-a449-f874f0957013
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/07/2017
ms.author: iainfou
ms.openlocfilehash: 880f5e5967298401fc2522124af3746d9906ffa8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-reset-local-windows-password-for-azure-vm"></a>Como repor a palavra-passe do Windows local para a VM do Azure
Pode repor o Windows palavra-passe local de uma VM no Azure com o [portal do Azure ou do Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) fornecido está instalado o agente convidado do Azure. Este método é a forma de principal para repor uma palavra-passe para uma VM do Azure. Se ocorrerem problemas com o agente convidado do Azure não responde ou falhar instalar depois de carregar uma imagem personalizada, pode manualmente repor uma palavra-passe do Windows. Este artigo fornece detalhes sobre como repor uma palavra-passe de conta local ao anexar o disco virtual de origem SO para outra VM. 

> [!WARNING]
> Só utilize este processo como último recurso. Sempre tentar repor a palavra-passe a utilizar o [portal do Azure ou do Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) primeiro.
> 
> 

## <a name="overview-of-the-process"></a>Descrição geral do processo
Os passos de núcleos para efetuar uma palavra-passe local reposta para uma VM do Windows no Azure quando há sem acesso para o agente convidado do Azure é o seguinte:

* Elimine a VM de origem. Os discos virtuais são retidos.
* Anexe o disco do SO da VM de origem para outra VM na mesma localização dentro da sua subscrição do Azure. Esta VM é referida como a VM de resolução de problemas.
* Utilizar a resolução de problemas de VM, crie alguns ficheiros de configuração no disco do SO da VM de origem.
* Exposição do disco do SO da VM na VM resolução de problemas.
* Utilize um modelo do Resource Manager para criar uma VM, utilizando o disco virtual original.
* Quando inicia a nova VM, os ficheiros de configuração que cria atualizar a palavra-passe do utilizador necessária.

## <a name="detailed-steps"></a>Passos detalhados
Sempre tentar repor a palavra-passe a utilizar o [portal do Azure ou do Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) antes de tentar os seguintes passos. Certifique-se de que tem uma cópia de segurança da sua VM antes de começar. 

1. Elimine a VM afetada no portal do Azure. Apenas a eliminação da VM elimina os metadados, a referência da VM no Azure. Os discos virtuais são mantidos quando a VM é eliminada:
   
   * Selecione a VM no portal do Azure, clique em *eliminar*:
     
     ![Eliminar VM existente](./media/reset-local-password-without-agent/delete_vm.png)
2. Anexe o disco do SO da VM de origem para a VM de resolução de problemas. A resolução de problemas VM tem de estar na mesma região que o disco do SO da VM de origem (tais como `West US`):
   
   * Selecione a VM de resolução de problemas no portal do Azure. Clique em *discos* | *anexar existente*:
     
     ![Anexar o disco existente](./media/reset-local-password-without-agent/disks_attach_existing.png)
     
     Selecione *ficheiro VHD* e, em seguida, selecione a conta de armazenamento que contém a VM de origem:
     
     ![Selecionar a conta de armazenamento](./media/reset-local-password-without-agent/disks_select_storageaccount.PNG)
     
     Selecione o contentor de origem. O contentor de origem é normalmente *vhds*:
     
     ![Selecione o contentor de armazenamento](./media/reset-local-password-without-agent/disks_select_container.png)
     
     Selecione o vhd de SO para anexar. Clique em *selecione* para concluir o processo:
     
     ![Selecione o disco virtual de origem](./media/reset-local-password-without-agent/disks_select_source_vhd.png)
3. Ligar para a VM de resolução de problemas com o ambiente de trabalho remoto e certifique-se de que o disco do SO da VM de origem está visível:
   
   * Selecione a VM de resolução de problemas no portal do Azure e clique em *Connect*.
   * Abra o ficheiro RDP que transfere. Introduza o nome de utilizador e palavra-passe da VM resolução de problemas.
   * No Explorador de ficheiros, procure o disco de dados anexou. Se a origem do VHD da VM é o disco de dados apenas ligado à VM resolução de problemas, deve ser f: unidade:
     
     ![Ver o disco de dados anexados](./media/reset-local-password-without-agent/troubleshooting_vm_fileexplorer.png)
4. Criar `gpt.ini` no `\Windows\System32\GroupPolicy` no disco da VM de origem (se existir gpt.ini, mude o nome para gpt.ini.bak):
   
   > [!WARNING]
   > Certifique-se de que não acidentalmente crie os seguintes ficheiros em C:\Windows, a unidade de SO para a VM de resolução de problemas. Crie os seguintes ficheiros na unidade de SO para a VM está ligado como um disco de dados de origem.
   > 
   > 
   
   * Adicione as seguintes linhas para o `gpt.ini` ficheiro que criou:
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     ![Criar gpt.ini](./media/reset-local-password-without-agent/create_gpt_ini.png)
5. Criar `scripts.ini` no `\Windows\System32\GroupPolicy\Machine\Scripts`. Certifique-se, pastas ocultas são apresentadas. Se necessário, crie o `Machine` ou `Scripts` pastas.
   
   * Adicione as seguintes linhas de `scripts.ini` ficheiro que criou:
     
     ```
     [Startup]
     0CmdLine=C:\Windows\System32\FixAzureVM.cmd
     0Parameters=
     ```
     
     ![Criar scripts.ini](./media/reset-local-password-without-agent/create_scripts_ini.png)
6. Criar `FixAzureVM.cmd` no `\Windows\System32` com o seguinte conteúdo, substituindo `<username>` e `<newpassword>` com os seus próprios valores:
   
    ```
    net user <username> <newpassword> /add
    net localgroup administrators <username> /add
    net localgroup "remote desktop users" <username> /add

    ```

    ![Criar FixAzureVM.cmd](./media/reset-local-password-without-agent/create_fixazure_cmd.png)
   
    Tem de cumprir os requisitos de complexidade de palavra-passe configurado para a VM quando definir a palavra-passe nova.
7. No portal do Azure, desligue o disco da VM resolução de problemas:
   
   * Selecione a VM de resolução de problemas no portal do Azure, clique em *discos*.
   * Selecione o disco de dados ligado no passo 2, clique em *anulação de exposições*:
     
     ![Exposição do disco](./media/reset-local-password-without-agent/detach_disk.png)
8. Antes de criar uma VM, obter o URI para o disco de SO de origem:
   
   * Selecione a conta do storage no portal do Azure, clique em *Blobs*.
   * Selecione o contentor. O contentor de origem é normalmente *vhds*:
     
     ![Selecione o blob de conta de armazenamento](./media/reset-local-password-without-agent/select_storage_details.png)
     
     Selecione a VHD de SO de VM de origem e clique em de *cópia* junto ao *URL* nome:
     
     ![Copie o disco URI](./media/reset-local-password-without-agent/copy_source_vhd_uri.png)
9. Crie uma VM a partir do disco do SO da VM de origem:
   
   * Utilize [este modelo Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd) para criar uma VM a partir de um VHD especializado. Clique em de `Deploy to Azure` botão para abrir o portal do Azure com os detalhes transformada em modelo preenchidos para si.
   * Se pretender manter todas as definições anteriores para a VM, selecione *Editar modelo* para fornecer aos VNet existente, a sub-rede, a placa de rede ou o IP público.
   * No `OSDISKVHDURI` caixa de texto do parâmetro, cole o URI da sua origem de VHD obter no passo anterior:
     
     ![Criar uma VM a partir do modelo](./media/reset-local-password-without-agent/create_new_vm_from_template.png)
10. Depois da nova VM está em execução, ligar à VM utilizando o ambiente de trabalho remoto com a nova palavra-passe que especificou no `FixAzureVM.cmd` script.
11. Da sessão remota para a nova VM, remova os ficheiros seguintes para limpar o ambiente:
    
    * De %windir%\System32
      * remover FixAzureVM.cmd
    * De %windir%\System32\GroupPolicy\Machine\
      * Remover scripts.ini
    * De %windir%\System32\GroupPolicy
      * remover gpt.ini (se previamente existente gpt.ini e é mudado para gpt.ini.bak, mudar o nome de ficheiro. bak de volta para gpt.ini)

## <a name="next-steps"></a>Passos seguintes
Se ainda não é possível estabelecer ligação utilizando o ambiente de trabalho remoto, consulte o [guia de resolução de problemas de RDP](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). O [RDP guia de resolução de problemas de detalhado](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) observa métodos em vez dos passos específicos de resolução de problemas. Também pode [abrir um pedido de suporte do Azure](https://azure.microsoft.com/support/options/) para obter assistência prática.

