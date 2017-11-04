---
title: "Utilizar um Windows VM de resolução de problemas no portal do Azure | Microsoft Docs"
description: "Saiba como resolver problemas de máquina virtual do Windows no Azure ao ligar o disco de SO para uma VM com o portal do Azure de recuperação"
services: virtual-machines-windows
documentationCenter: 
authors: genlin
manager: timlt
editor: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: c1ccd165f9652a8e5e96652937667add6825db8c
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Resolver problemas de uma VM do Windows ao anexar o disco de SO para uma VM com o portal do Azure de recuperação
Se a máquina virtual (VM) do Windows no Azure encontra um erro de arranque ou de disco, poderá ter de efetuar os passos de resolução de problemas no disco de rígido virtual. Um exemplo comum seria uma atualização da aplicação com falhas que impede a VM de ser capazes de arrancar com êxito. Este artigo fornece detalhes sobre como utilizar o portal do Azure para ligar o seu disco rígido virtual a outra VM do Windows para corrigir os eventuais erros, em seguida, voltar a criar a VM original.

## <a name="recovery-process-overview"></a>Descrição geral do processo de recuperação
O processo de resolução de problemas é o seguinte:

1. Elimine a VM problemas com, mantendo os discos rígidos virtuais.
2. Anexe e montar o disco rígido virtual para outro VM do Windows para fins de resolução de problemas.
3. Ligue à VM da resolução de problemas. Editar ficheiros ou executar quaisquer ferramentas para corrigir problemas no disco rígido virtual original.
4. Desmonte e desanexe o disco rígido virtual da VM de resolução de problemas.
5. Crie uma VM utilizando o disco rígido virtual original.


## <a name="determine-boot-issues"></a>Determinar os problemas de arranque
Para determinar o motivo pelo qual a VM não é capaz de arranque corretamente, examine o diagnóstico de arranque captura de ecrã da VM. Um exemplo comum teria de ser uma atualização da aplicação que falhou, ou um subjacente disco de rígido virtual que está a ser eliminado ou movido.

Selecione a VM no portal e, em seguida, desloque para baixo até o **suporte + resolução de problemas** secção. Clique em **diagnóstico de arranque** para ver a captura de ecrã. Tenha em atenção quaisquer mensagens de erro específicas ou códigos de erro para ajudar a determinar por que razão a VM está a encontrar um problema. O exemplo seguinte mostra uma VM aguardar a resposta de parar os serviços:

![Visualizar VM diagnóstico de arranque consola registos](./media/troubleshoot-recovery-disks-portal/screenshot-error.png)

Também pode clicar em **captura de ecrã** para transferir uma captura de captura de ecrã VM.


## <a name="view-existing-virtual-hard-disk-details"></a>Ver detalhes de disco rígido virtual existente
Antes de pode anexar o disco rígido virtual a outra VM, tem de identificar o nome do disco rígido virtual (VHD). 

Selecione o grupo de recursos a partir do portal, em seguida, selecione a sua conta de armazenamento. Clique em **Blobs**, como no exemplo seguinte:

![Selecione os blobs de armazenamento](./media/troubleshoot-recovery-disks-portal/storage-account-overview.png)

Normalmente, tem um contentor com o nome **vhds** que armazena os discos rígidos virtuais. Selecione o contentor para ver uma lista de discos rígidos virtuais. Tome nota do nome do seu VHD (o prefixo é, normalmente, o nome da sua VM):

![Identificar o VHD no contentor de armazenamento](./media/troubleshoot-recovery-disks-portal/storage-container.png)

Selecione o disco rígido virtual existente na lista e copie o URL para utilização nos passos seguintes:

![Copie o URL de disco rígido virtual existente](./media/troubleshoot-recovery-disks-portal/copy-vhd-url.png)


## <a name="delete-existing-vm"></a>Eliminar VM existente
Os discos rígidos virtuais e as VMs são dois recursos diferentes do Azure. Um disco rígido virtual é onde o sistema operativo, as aplicações e configurações são armazenadas. A própria VM é apenas metadados, que definem o tamanho ou localização e referencia recursos, tais como um disco rígido virtual ou o cartão de interface de rede virtual (NIC). Cada disco rígido virtual tem uma concessão atribuída quando ligado a uma VM. Embora os discos de dados possam ser anexados e desanexados mesmo se a VM estiver a ser executada, o disco do SO só pode ser desanexado se o recurso da VM for eliminado. A concessão continua a associar o disco de SO com uma VM, mesmo quando se encontra num estado parado e desalocado essa VM.

É o primeiro passo para recuperar a VM para eliminar o recurso VM em si. Com a eliminação, os discos rígidos virtuais permanecem na sua conta de armazenamento. Depois da VM é eliminada, anexar o disco rígido virtual a outra VM para resolver os erros.

Selecione a VM no portal, em seguida, clique em **eliminar**:

![VM arranque diagnóstico captura de ecrã com erro de arranque](./media/troubleshoot-recovery-disks-portal/stop-delete-vm.png)

Aguarde até que a VM terminou a eliminação antes de anexar o disco rígido virtual a outra VM. A concessão no disco rígido virtual que associa-a VM tem de ser libertado antes de pode anexar o disco rígido virtual a outra VM.


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Anexar o disco rígido virtual existente para outra VM
Para os próximos passos, utilize outra VM para fins de resolução de problemas. Anexar o disco rígido virtual existente para esta VM de resolução de problemas para conseguir a procurar e editar o conteúdo do disco. Este processo permite-lhe corrigir os eventuais erros de configuração ou reveja aplicação adicional ou ficheiros de registo do sistema, por exemplo. Escolha ou crie outra VM a utilizar para fins de resolução de problemas.

1. Selecione o grupo de recursos a partir do portal, em seguida, selecione a VM de resolução de problemas. Selecione **discos** e, em seguida, clique em **anexar existente**:

    ![Anexar o disco existente no portal](./media/troubleshoot-recovery-disks-portal/attach-existing-disk.png)

2. Para selecionar o disco rígido virtual existente, clique em **Ficheiro VHD**:

    ![Procurar o VHD existente](./media/troubleshoot-recovery-disks-portal/select-vhd-location.png)

3. Selecionar a sua conta do storage e um contentor, em seguida, clique o VHD existente. Clique em de **selecione** botão para confirmar a sua preferência:

    ![Selecionar o seu VHD existente](./media/troubleshoot-recovery-disks-portal/select-vhd.png)

4. Com o VHD selecionado agora, clique em **OK** para anexar o disco rígido virtual existente:

    ![Confirme a anexar o disco rígido virtual existente](./media/troubleshoot-recovery-disks-portal/attach-disk-confirm.png)

5. Após alguns segundos, o **discos** painel para a VM lista o disco rígido virtual ligado como um disco de dados:

    ![Disco rígido virtual já existente anexado como disco de dados](./media/troubleshoot-recovery-disks-portal/attached-disk.png)


## <a name="mount-the-attached-data-disk"></a>Montar o disco de dados anexados

1. Abra uma ligação de ambiente de trabalho remoto à VM. Selecione a VM no portal e clique **Connect**. Transfira e abra o ficheiro de ligação de RDP. Introduza as suas credenciais para iniciar sessão na sua VM da seguinte forma:

    ![Inicie sessão na sua VM utilizando o ambiente de trabalho remoto](./media/troubleshoot-recovery-disks-portal/open-remote-desktop.png)

2. Abra **Gestor de servidor**, em seguida, selecione **File and Storage Services**. 

    ![Selecione o ficheiro e serviços de armazenamento no Gestor de servidor](./media/troubleshoot-recovery-disks-portal/server-manager-select-storage.png)

3. O disco de dados é automaticamente detetado e ligado. Para ver uma lista dos discos ligados, selecione **discos**. Pode selecionar o disco de dados para ver informações de volume, incluindo a letra de unidade. O exemplo seguinte mostra o disco de dados ligados e utilizar **f:**:

    ![Disco ligado e informações de volume no Gestor de servidor](./media/troubleshoot-recovery-disks-portal/server-manager-disk-attached.png)


## <a name="fix-issues-on-original-virtual-hard-disk"></a>Resolva os problemas no disco rígido virtual original
Com o disco rígido virtual existente montado, agora pode efetuar qualquer manutenção e passos de resolução de problemas, conforme necessário. Depois de corrigir esses problemas, avance para os passos seguintes.


## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Desmonte e desanexar o disco rígido virtual original
Depois dos erros serem resolvidos, anular a exposição do disco rígido virtual existente, da sua VM de resolução de problemas. Não é possível utilizar o disco rígido virtual com quaisquer outro VM até que a concessão anexar o disco rígido virtual para a VM de resolução de problemas é libertada.

1. A sessão do RDP para a VM, abra **Gestor de servidor**, em seguida, selecione **File and Storage Services**:

    ![Selecione o ficheiro e serviços de armazenamento no Gestor de servidor](./media/troubleshoot-recovery-disks-portal/server-manager-select-storage.png)

2. Selecione **discos** e, em seguida, selecione o disco de dados. Faça duplo clique no seu disco de dados e selecione **Colocar Offline**:

    ![Definir o disco de dados como offline no Gestor de servidor](./media/troubleshoot-recovery-disks-portal/server-manager-set-disk-offline.png)

3. Agora desanexe o disco rígido virtual da VM. Selecione a VM no portal do Azure e clique em **discos**. Selecione o disco rígido virtual existente e, em seguida, clique em **anulação de exposições**:

    ![Exposição do disco rígido virtual existente](./media/troubleshoot-recovery-disks-portal/detach-disk.png)

    Aguarde até que a VM com exposição anulada com êxito o disco de dados antes de continuar.

## <a name="create-vm-from-original-hard-disk"></a>Criar a VM de disco rígido original
Para criar uma VM a partir do seu disco rígido virtual original, utilize [este modelo Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-existing-vnet). O modelo implementa uma VM numa rede virtual existente, utilizando o URL de VHD do comando anterior. Clique em de **implementar no Azure** botão da seguinte forma:

![Implementar a VM a partir do modelo a partir do Github](./media/troubleshoot-recovery-disks-portal/deploy-template-from-github.png)

O modelo é carregado para o portal do Azure para a implementação. Introduza os nomes para a sua nova VM e os recursos do Azure existentes e cole o URL para o disco rígido virtual existente. Para iniciar a implementação, clique em **Compra**:

![Implementar a VM a partir do modelo](./media/troubleshoot-recovery-disks-portal/deploy-from-image.png)


## <a name="re-enable-boot-diagnostics"></a>Reativar o diagnóstico de arranque
Quando criar a VM a partir do disco rígido virtual existente, diagnóstico de arranque pode não ser ativado automaticamente. Para verificar o estado de diagnóstico de arranque e ative se for necessário, selecione a VM no portal. Em **monitorização**, clique em **as definições de diagnóstico**. Certifique-se de que o estado é **no**e a marca de verificação junto a **diagnóstico de arranque** está selecionada. Se efetuar alterações, clique em **guardar**:

![Atualizar as definições de diagnóstico de arranque](./media/troubleshoot-recovery-disks-portal/reenable-boot-diagnostics.png)

## <a name="next-steps"></a>Passos seguintes
Se estiver a ter problemas em ligar à VM, consulte [nas ligações RDP de resolver para uma VM do Azure](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Para problemas com a aceder a aplicações em execução numa VM, consulte [resolver problemas de conectividade de aplicação numa Windows VM](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Para obter mais informações sobre como utilizar o Gestor de recursos, consulte [descrição geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
