---
title: "Utilize uma VM com a CLI do Azure 1.0 de resolução de problemas de Linux | Microsoft Docs"
description: "Saiba como resolver problemas de VM com Linux, ligando-se o disco de SO para uma VM utilizando a CLI do Azure 1.0 de recuperação"
services: virtual-machines-linux
documentationCenter: 
authors: iainfoulds
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/09/2017
ms.author: iainfou
ms.openlocfilehash: d817358211f123c96d899c5cff88cc47aeb5c9c1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-cli-10"></a>Resolver problemas de uma VM com Linux ao anexar o disco de SO para uma VM utilizando a CLI do Azure 1.0 de recuperação
Se a máquina virtual (VM) do Linux encontra um erro de arranque ou de disco, poderá ter de efetuar os passos de resolução de problemas no disco de rígido virtual. Um exemplo comum é uma entrada inválida no `/etc/fstab` que impede a VM de ser capazes de arrancar com êxito. Este artigo fornece detalhes sobre como utilizar a CLI do Azure 1.0 para ligar o seu disco rígido virtual a outra VM com Linux para corrigir os eventuais erros, em seguida, voltar a criar a VM original.


## <a name="cli-versions-to-complete-the-task"></a>Versões CLI para concluir a tarefa
Pode concluir a tarefa utilizando uma das seguintes versões CLI:

- [Azure CLI 1.0](#recovery-process-overview) – nosso CLI para os clássica e resource Gestão modelos de implementação (Este artigo)
- [CLI 2.0 do Azure](../windows/troubleshoot-recovery-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - CLI de próxima geração para o modelo de implementação de gestão de recursos


## <a name="recovery-process-overview"></a>Descrição geral do processo de recuperação
O processo de resolução de problemas é o seguinte:

1. Elimine a VM problemas com, mantendo os discos rígidos virtuais.
2. Anexe e montar o disco rígido virtual para outro VM com Linux para fins de resolução de problemas.
3. Ligue à VM da resolução de problemas. Editar ficheiros ou executar quaisquer ferramentas para corrigir problemas no disco rígido virtual original.
4. Desmonte e desanexe o disco rígido virtual da VM de resolução de problemas.
5. Crie uma VM utilizando o disco rígido virtual original.

Certifique-se de que tem [mais recente do Azure CLI 1.0](../../cli-install-nodejs.md) instalado e a sessão e utilizar o modo Resource Manager:

```azurecli
azure config mode arm
```

Nos exemplos a seguir, substitua os nomes de parâmetros com os seus próprios valores. Os nomes dos parâmetros de exemplo incluem `myResourceGroup`, `mystorageaccount`, e `myVM`.


## <a name="determine-boot-issues"></a>Determinar os problemas de arranque
Examine a saída de série para determinar o motivo pelo qual a VM não se encontra capaz de arrancar corretamente. Um exemplo comum é uma entrada inválida no `/etc/fstab`, ou subjacente disco rígido virtual que está a ser eliminado ou movido.

O exemplo seguinte obtém o resultado de série de VM com o nome `myVM` no grupo de recursos denominado `myResourceGroup`:

```azurecli
azure vm get-serial-output --resource-group myResourceGroup --name myVM
```

Reveja o resultado de série para determinar o motivo pelo qual a VM está a falhar efetuar o arranque. Se o resultado da série não está a fornecer qualquer indicação, poderá ter de consultar os ficheiros de registo na `/var/log` assim que tiver o disco rígido virtual ligado a uma VM de resolução de problemas.


## <a name="view-existing-virtual-hard-disk-details"></a>Ver detalhes de disco rígido virtual existente
Antes de pode anexar o disco rígido virtual a outra VM, tem de identificar o nome do disco rígido virtual (VHD). 

O exemplo seguinte obtém as informações para a VM com o nome `myVM` no grupo de recursos denominado `myResourceGroup`:

```azurecli
azure vm show --resource-group myResourceGroup --name myVM
```

Procure `Vhd URI` na saída do comando anterior. O seguinte truncado saída de exemplo mostra o `Vhd URI` na última linha:

```azurecli
info:    Executing command vm show
+ Looking up the VM "myVM"
+ Looking up the NIC "myNic"
+ Looking up the public ip "myPublicIP"
...
data:
data:      OS Disk:
data:        OSType                      :Linux
data:        Name                        :myVM
data:        Caching                     :ReadWrite
data:        CreateOption                :FromImage
data:        Vhd:
data:          Uri                       :https://mystorageaccount.blob.core.windows.net/vhds/myVM201610292712.vhd
```


## <a name="delete-existing-vm"></a>Eliminar VM existente
Os discos rígidos virtuais e as VMs são dois recursos diferentes do Azure. Um disco rígido virtual é onde o sistema operativo, as aplicações e configurações são armazenadas. A própria VM é apenas metadados, que definem o tamanho ou localização e referencia recursos, tais como um disco rígido virtual ou o cartão de interface de rede virtual (NIC). Cada disco rígido virtual tem uma concessão atribuída quando ligado a uma VM. Embora os discos de dados possam ser anexados e desanexados mesmo se a VM estiver a ser executada, o disco do SO só pode ser desanexado se o recurso da VM for eliminado. A concessão continua a associar o disco de SO com uma VM, mesmo quando se encontra num estado parado e desalocado essa VM.

É o primeiro passo para recuperar a VM para eliminar o recurso VM em si. Com a eliminação, os discos rígidos virtuais permanecem na sua conta de armazenamento. Depois da VM é eliminada, anexar o disco rígido virtual a outra VM para resolver os erros.

O exemplo seguinte elimina a VM com o nome `myVM` do grupo de recursos denominado `myResourceGroup`:

```azurecli
azure vm delete --resource-group myResourceGroup --name myVM 
```

Aguarde até que a VM terminou a eliminação antes de anexar o disco rígido virtual a outra VM. A concessão no disco rígido virtual que associa-a VM tem de ser libertado antes de pode anexar o disco rígido virtual a outra VM.


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Anexar o disco rígido virtual existente para outra VM
Para os próximos passos, utilize outra VM para fins de resolução de problemas. Anexar o disco rígido virtual existente para esta VM de resolução de problemas para procurar e editar o conteúdo do disco. Este processo permite-lhe corrigir os eventuais erros de configuração ou reveja aplicação adicional ou ficheiros de registo do sistema, por exemplo. Escolha ou crie outra VM a utilizar para fins de resolução de problemas.

Ao anexar o disco rígido virtual existente, especifique o URL para o disco que obteve no precedente `azure vm show` comando. O exemplo seguinte anexa um disco rígido virtual existente para a VM de resolução de problemas com o nome `myVMRecovery` no grupo de recursos denominado `myResourceGroup`:

```azurecli
azure vm disk attach --resource-group myResourceGroup --name myVMRecovery \
    --vhd-url https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd
```


## <a name="mount-the-attached-data-disk"></a>Montar o disco de dados anexados

> [!NOTE]
> Os exemplos seguintes de detalhe os passos necessários numa VM com Ubuntu. Se estiver a utilizar um distro diferente do Linux, como Red Hat Enterprise Linux ou SUSE, o registo de localizações de ficheiro e `mount` os comandos podem ser ligeiramente diferentes. Consulte a documentação do seu distro específica para as alterações adequadas nos comandos.

1. SSH para a VM de resolução de problemas utilizando as credenciais adequadas. Se este disco é o primeiro disco de dados ligado à sua VM de resolução de problemas, o disco, provavelmente, está ligado a `/dev/sdc`. Utilize `dmseg` para ver os discos ligados:

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
Depois dos erros serem resolvidos, desmontar e desanexar o disco rígido virtual existente a VM de resolução de problemas. Não é possível utilizar o disco rígido virtual com quaisquer outro VM até que a concessão anexar o disco rígido virtual para a VM de resolução de problemas é libertada.

1. Da sessão SSH para a VM de resolução de problemas, desmonte o disco rígido virtual existente. Altere primeiro fora do diretório principal do ponto de montagem:

    ```bash
    cd /
    ```

    Desmonte agora o disco rígido virtual existente. O exemplo seguinte unmounts dispositivo em `/dev/sdc1`:

    ```bash
    sudo umount /dev/sdc1
    ```

2. Agora desanexe o disco rígido virtual da VM. Sair sessão SSH para a VM de resolução de problemas. Na CLI do Azure, lista primeiro os discos de dados anexados à sua VM de resolução de problemas. O exemplo seguinte lista os discos de dados ligados à VM com o nome `myVMRecovery` no grupo de recursos denominado `myResourceGroup`:

    ```azurecli
    azure vm disk list --resource-group myResourceGroup --vm-name myVMRecovery
    ```

    Tenha em atenção o `Lun` valor para o disco rígido virtual existente. Resultado do comando de exemplo seguinte mostra o disco virtual existente, ligado ao LUN 0:

    ```azurecli
    info:    Executing command vm disk list
    + Looking up the VM "myVMRecovery"
    data:    Name              Lun  DiskSizeGB  Caching  URI
    data:    ------            ---  ----------  -------  ------------------------------------------------------------------------
    data:    myVM              0                None     https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd
    info:    vm disk list command OK
    ```

    Desligar o disco de dados da sua VM utilizando o aplicável `Lun` valor:

    ```azurecli
    azure vm disk detach --resource-group myResourceGroup --vm-name myVMRecovery \
        --lun 0
    ```


## <a name="create-vm-from-original-hard-disk"></a>Criar a VM de disco rígido original
Para criar uma VM a partir do seu disco rígido virtual original, utilize [este modelo Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd). O modelo JSON é a seguinte hiperligação:

- https://RAW.githubusercontent.com/Azure/Azure-QuickStart-Templates/Master/201-VM-Specialized-VHD/azuredeploy.JSON

O modelo implementa uma VM numa rede virtual existente, utilizando o URL de VHD do comando anterior. O exemplo seguinte implementa o modelo para o grupo de recursos com o nome `myResourceGroup`:

```azurecli
azure group deployment create --resource-group myResourceGroup --name myDeployment \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd/azuredeploy.json
```

Responder os pedidos para o modelo, tais como o nome da VM (`myDeployedVM` o exemplo seguinte), tipo de SO (`Linux`) e o tamanho da VM (`Standard_DS1_v2`). O `osDiskVhdUri` é o mesmo utilizado como anteriormente ao anexar o disco rígido virtual existente para a VM de resolução de problemas. Segue-se um exemplo da saída do comando e avisos:

```azurecli
info:    Executing command group deployment create
info:    Supply values for the following parameters
vmName:  myDeployedVM
osType:  Linux
osDiskVhdUri:  https://mystorageaccount.blob.core.windows.net/vhds/myVM201610292712.vhd
vmSize:  Standard_DS1_v2
existingVirtualNetworkName:  myVnet
existingVirtualNetworkResourceGroup:  myResourceGroup
subnetName:  mySubnet
dnsNameForPublicIP:  mypublicipdeployed
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "mydeployment"
+ Waiting for deployment to complete
+
```


## <a name="re-enable-boot-diagnostics"></a>Reativar o diagnóstico de arranque

Quando criar a VM a partir do disco rígido virtual existente, diagnóstico de arranque pode não ser ativado automaticamente. O exemplo seguinte ativa a extensão de diagnóstico da VM com o nome `myDeployedVM` no grupo de recursos denominado `myResourceGroup`:

```azurecli
azure vm enable-diag --resource-group myResourceGroup --name myDeployedVM
```

## <a name="next-steps"></a>Passos seguintes
Se estiver a ter problemas em ligar à VM, consulte [resolver problemas de SSH ligações a uma VM do Azure](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Para problemas com a aceder a aplicações em execução numa VM, consulte [resolver problemas de conectividade de aplicação numa VM com Linux](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).