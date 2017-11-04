---
title: "Utilize um Linux VM de resolução de problemas no portal do Azure | Microsoft Docs"
description: "Saiba como resolver problemas de máquina virtual do Linux, ligando-se o disco de SO para uma VM com o portal do Azure de recuperação"
services: virtual-machines-linux
documentationCenter: 
authors: iainfoulds
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/14/2016
ms.author: iainfou
ms.openlocfilehash: 808a6c3ea4cb26dbb91752d13e2118d081b10668
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Resolver problemas de uma VM com Linux ao anexar o disco de SO para uma VM com o portal do Azure de recuperação
Se a máquina virtual (VM) do Linux encontra um erro de arranque ou de disco, poderá ter de efetuar os passos de resolução de problemas no disco de rígido virtual. Um exemplo comum é uma entrada inválida no `/etc/fstab` que impede a VM de ser capazes de arrancar com êxito. Este artigo fornece detalhes sobre como utilizar o portal do Azure para ligar o seu disco rígido virtual a outra VM com Linux para corrigir os eventuais erros, em seguida, voltar a criar a VM original.

## <a name="recovery-process-overview"></a>Descrição geral do processo de recuperação
O processo de resolução de problemas é o seguinte:

1. Elimine a VM problemas com, mantendo os discos rígidos virtuais.
2. Anexe e montar o disco rígido virtual para outro VM com Linux para fins de resolução de problemas.
3. Ligue à VM da resolução de problemas. Editar ficheiros ou executar quaisquer ferramentas para corrigir problemas no disco rígido virtual original.
4. Desmonte e desanexe o disco rígido virtual da VM de resolução de problemas.
5. Crie uma VM utilizando o disco rígido virtual original.


## <a name="determine-boot-issues"></a>Determinar os problemas de arranque
Examine o diagnóstico de arranque e a captura de ecrã VM para determinar o motivo pelo qual a VM não se encontra capaz de arrancar corretamente. Um exemplo comum é uma entrada inválida no `/etc/fstab`, ou um subjacente disco de rígido virtual que está a ser eliminado ou movido.

Selecione a VM no portal e, em seguida, desloque para baixo até o **suporte + resolução de problemas** secção. Clique em **diagnóstico de arranque** para ver as mensagens de consola transmissão em fluxo a partir da VM. Reveja os registos de consola para ver se pode determinar o motivo pelo qual a VM é encontrar um problema. O exemplo seguinte mostra que uma VM bloqueada no modo de manutenção que necessite de interação manual:

![Visualizar VM diagnóstico de arranque consola registos](./media/troubleshoot-recovery-disks-portal/boot-diagnostics-error.png)

Também pode clicar em **captura de ecrã** na parte superior do registo de diagnóstico de arranque para transferir uma captura de captura de ecrã VM.


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

> [!NOTE]
> Os exemplos seguintes de detalhe os passos necessários numa VM com Ubuntu. Se estiver a utilizar um distro diferente do Linux, como Red Hat Enterprise Linux ou SUSE, o registo de localizações de ficheiro e `mount` os comandos podem ser ligeiramente diferentes. Consulte a documentação do seu distro específica para as alterações adequadas nos comandos.

1. SSH para a VM de resolução de problemas utilizando as credenciais adequadas. Se este disco é o primeiro disco de dados ligado à sua VM de resolução de problemas, provavelmente, está ligado à `/dev/sdc`. Utilize `dmseg` para listar os discos ligados:

    ```bash
    dmesg | grep SCSI
    ```
    O resultado é semelhante ao seguinte exemplo:

    ```bash
    [    0.294784] SCSI subsystem initialized
    [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
    [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
    [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
    [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
    ```

    No exemplo anterior, o disco do SO é `/dev/sda` e o disco temporário fornecido para cada VM está em `/dev/sdb`. Se tiver vários discos de dados, devem estar no `/dev/sdd`, `/dev/sde`, e assim sucessivamente.

2. Crie um diretório para montar o disco rígido virtual existente. O exemplo seguinte cria um diretório com o nome `troubleshootingdisk`:

    ```bash
    sudo mkdir /mnt/troubleshootingdisk
    ```

3. Se tiver várias partições no seu disco rígido virtual existente, monte a partição necessária. O exemplo seguinte monta a primeira partição primária em `/dev/sdc1`:

    ```bash
    sudo mount /dev/sdc1 /mnt/troubleshootingdisk
    ```

    > [!NOTE]
    > É melhor prática montar discos de dados em VMs no Azure utilizando o identificador exclusivo universalmente (UUID) do disco rígido virtual. Para este cenário de resolução de problemas curto, montar o disco rígido virtual com o UUID não é necessário. No entanto, na utilização normal, editar `/etc/fstab` para montar discos rígidos virtuais com o nome de dispositivo, em vez de UUID pode fazer com que a VM falhar efetuar o arranque.


## <a name="fix-issues-on-original-virtual-hard-disk"></a>Resolva os problemas no disco rígido virtual original
Com o disco rígido virtual existente montado, agora pode efetuar qualquer manutenção e passos de resolução de problemas, conforme necessário. Depois de corrigir esses problemas, avance para os passos seguintes.

## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Desmonte e desanexar o disco rígido virtual original
Depois dos erros serem resolvidos, anular a exposição do disco rígido virtual existente, da sua VM de resolução de problemas. Não é possível utilizar o disco rígido virtual com quaisquer outro VM até que a concessão anexar o disco rígido virtual para a VM de resolução de problemas é libertada.

1. Da sessão SSH para a VM de resolução de problemas, desmonte o disco rígido virtual existente. Altere primeiro fora do diretório principal do ponto de montagem:

    ```bash
    cd /
    ```

    Desmonte agora o disco rígido virtual existente. O exemplo seguinte unmounts dispositivo em `/dev/sdc1`:

    ```bash
    sudo umount /dev/sdc1
    ```

2. Agora desanexe o disco rígido virtual da VM. Selecione a VM no portal e clique **discos**. Selecione o disco rígido virtual existente e, em seguida, clique em **anulação de exposições**:

    ![Exposição do disco rígido virtual existente](./media/troubleshoot-recovery-disks-portal/detach-disk.png)

    Aguarde até que a VM com exposição anulada com êxito o disco de dados antes de continuar.

## <a name="create-vm-from-original-hard-disk"></a>Criar a VM de disco rígido original
Para criar uma VM a partir do seu disco rígido virtual original, utilize [este modelo Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-existing-vnet). O modelo implementa uma VM numa rede virtual existente, utilizando o URL de VHD do comando anterior. Clique em de **implementar no Azure** botão da seguinte forma:

![Implementar a VM a partir do modelo a partir do GitHub](./media/troubleshoot-recovery-disks-portal/deploy-template-from-github.png)

O modelo é carregado para o portal do Azure para a implementação. Introduza os nomes para a sua nova VM e os recursos do Azure existentes e cole o URL para o disco rígido virtual existente. Para iniciar a implementação, clique em **Compra**:

![Implementar a VM a partir do modelo](./media/troubleshoot-recovery-disks-portal/deploy-from-image.png)


## <a name="re-enable-boot-diagnostics"></a>Reativar o diagnóstico de arranque
Quando criar a VM a partir do disco rígido virtual existente, diagnóstico de arranque pode não ser ativado automaticamente. Para verificar o estado de diagnóstico de arranque e ative se for necessário, selecione a VM no portal. Em **monitorização**, clique em **as definições de diagnóstico**. Certifique-se de que o estado é **no**e a marca de verificação junto a **diagnóstico de arranque** está selecionada. Se efetuar alterações, clique em **guardar**:

![Atualizar as definições de diagnóstico de arranque](./media/troubleshoot-recovery-disks-portal/reenable-boot-diagnostics.png)

## <a name="next-steps"></a>Passos seguintes
Se estiver a ter problemas em ligar à VM, consulte [resolver problemas de SSH ligações a uma VM do Azure](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Para problemas com a aceder a aplicações em execução numa VM, consulte [resolver problemas de conectividade de aplicação numa VM com Linux](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Para obter mais informações sobre como utilizar o Gestor de recursos, consulte [descrição geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
