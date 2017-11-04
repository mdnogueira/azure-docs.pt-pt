---
title: "Utilize uma VM com o 2.0 CLI do Azure de resolução de problemas de Linux | Microsoft Docs"
description: "Saiba como resolver problemas de VM com Linux, ligando-se o disco de SO para uma recuperação VM utilizando o 2.0 CLI do Azure"
services: virtual-machines-linux
documentationCenter: 
authors: iainfoulds
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/16/2017
ms.author: iainfou
ms.openlocfilehash: 7a28accce1bd328b2b486b588c44d91b03e42122
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-with-the-azure-cli-20"></a>Resolver problemas de uma VM com Linux ao anexar o disco de SO para uma VM com o 2.0 CLI do Azure de recuperação
Se a máquina virtual (VM) do Linux encontra um erro de arranque ou de disco, poderá ter de efetuar os passos de resolução de problemas no disco de rígido virtual. Um exemplo comum é uma entrada inválida no `/etc/fstab` que impede a VM de ser capazes de arrancar com êxito. Este artigo fornece detalhes sobre como utilizar o 2.0 CLI do Azure para ligar o seu disco rígido virtual a outra VM com Linux para corrigir os eventuais erros, em seguida, voltar a criar a VM original. Também pode efetuar estes passos com a [CLI 1.0 do Azure](troubleshoot-recovery-disks-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="recovery-process-overview"></a>Descrição geral do processo de recuperação
O processo de resolução de problemas é o seguinte:

1. Elimine a VM problemas com, mantendo os discos rígidos virtuais.
2. Anexe e montar o disco rígido virtual para outro VM com Linux para fins de resolução de problemas.
3. Ligue à VM da resolução de problemas. Editar ficheiros ou executar quaisquer ferramentas para corrigir problemas no disco rígido virtual original.
4. Desmonte e desanexe o disco rígido virtual da VM de resolução de problemas.
5. Crie uma VM utilizando o disco rígido virtual original.

Para efetuar estes passos de resolução de problemas, terá da versão mais recente [Azure CLI 2.0](/cli/azure/install-az-cli2) instalado e registado para uma conta do Azure utilizando [início de sessão az](/cli/azure/#login).

Nos exemplos a seguir, substitua os nomes de parâmetros com os seus próprios valores. Os nomes dos parâmetros de exemplo incluem `myResourceGroup`, `mystorageaccount`, e `myVM`.


## <a name="determine-boot-issues"></a>Determinar os problemas de arranque
Examine a saída de série para determinar o motivo pelo qual a VM não se encontra capaz de arrancar corretamente. Um exemplo comum é uma entrada inválida no `/etc/fstab`, ou subjacente disco rígido virtual que está a ser eliminado ou movido.

Obter os registos de arranque com [az vm diagnóstico de arranque get-arranque-registo](/cli/azure/vm/boot-diagnostics#get-boot-log). O exemplo seguinte obtém o resultado de série de VM com o nome `myVM` no grupo de recursos denominado `myResourceGroup`:

```azurecli
az vm boot-diagnostics get-boot-log --resource-group myResourceGroup --name myVM
```

Reveja o resultado de série para determinar o motivo pelo qual a VM está a falhar efetuar o arranque. Se o resultado da série não está a fornecer qualquer indicação, poderá ter de consultar os ficheiros de registo na `/var/log` assim que tiver o disco rígido virtual ligado a uma VM de resolução de problemas.


## <a name="view-existing-virtual-hard-disk-details"></a>Ver detalhes de disco rígido virtual existente
Antes de pode anexar o disco rígido virtual (VHD) para outra VM, tem de identificar o URI do disco de SO. 

Ver informações sobre a VM com [mostrar de vm az](/cli/azure/vm#show). Utilize o `--query` sinalizador para extrair o URI para o disco de SO. O exemplo seguinte obtém informações do disco para a VM com o nome `myVM` no grupo de recursos denominado `myResourceGroup`:

```azurecli
az vm show --resource-group myResourceGroup --name myVM \
    --query [storageProfile.osDisk.vhd.uri] --output tsv
```

O URI é semelhante à **https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd**.

## <a name="delete-existing-vm"></a>Eliminar VM existente
Os discos rígidos virtuais e as VMs são dois recursos diferentes do Azure. Um disco rígido virtual é onde o sistema operativo, as aplicações e configurações são armazenadas. A própria VM é apenas metadados, que definem o tamanho ou localização e referencia recursos, tais como um disco rígido virtual ou o cartão de interface de rede virtual (NIC). Cada disco rígido virtual tem uma concessão atribuída quando ligado a uma VM. Embora os discos de dados possam ser anexados e desanexados mesmo se a VM estiver a ser executada, o disco do SO só pode ser desanexado se o recurso da VM for eliminado. A concessão continua a associar o disco de SO com uma VM, mesmo quando se encontra num estado parado e desalocado essa VM.

É o primeiro passo para recuperar a VM para eliminar o recurso VM em si. Com a eliminação, os discos rígidos virtuais permanecem na sua conta de armazenamento. Depois da VM é eliminada, anexar o disco rígido virtual a outra VM para resolver os erros.

Eliminar a VM com [az vm eliminar](/cli/azure/vm#delete). O exemplo seguinte elimina a VM com o nome `myVM` do grupo de recursos denominado `myResourceGroup`:

```azurecli
az vm delete --resource-group myResourceGroup --name myVM 
```

Aguarde até que a VM terminou a eliminação antes de anexar o disco rígido virtual a outra VM. A concessão no disco rígido virtual que associa-a VM tem de ser libertado antes de pode anexar o disco rígido virtual a outra VM.


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Anexar o disco rígido virtual existente para outra VM
Para os próximos passos, utilize outra VM para fins de resolução de problemas. Anexar o disco rígido virtual existente para esta VM de resolução de problemas para procurar e editar o conteúdo do disco. Este processo permite-lhe corrigir os eventuais erros de configuração ou reveja aplicação adicional ou ficheiros de registo do sistema, por exemplo. Escolha ou crie outra VM a utilizar para fins de resolução de problemas.

Anexar o disco rígido virtual existente com [az unmanaged-disco da vm ligar](/cli/azure/vm/unmanaged-disk#attach). Ao anexar o disco rígido virtual existente, especifique o URI para o disco que obteve no precedente `az vm show` comando. O exemplo seguinte anexa um disco rígido virtual existente para a VM de resolução de problemas com o nome `myVMRecovery` no grupo de recursos denominado `myResourceGroup`:

```azurecli
az vm unmanaged-disk attach --resource-group myResourceGroup --vm-name myVMRecovery \
    --vhd-uri https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd
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

2. Agora desanexe o disco rígido virtual da VM. Sair sessão SSH para a VM de resolução de problemas. Lista os discos de dados anexados à sua VM de resolução de problemas com [az vm unmanaged-lista de discos](/cli/azure/vm/unmanaged-disk#list). O exemplo seguinte lista os discos de dados ligados à VM com o nome `myVMRecovery` no grupo de recursos denominado `myResourceGroup`:

    ```azurecli
    azure vm unmanaged-disk list --resource-group myResourceGroup --vm-name myVMRecovery \
        --query '[].{Disk:vhd.uri}' --output table
    ```

    Tenha em atenção o nome para o disco rígido virtual existente. Por exemplo, o nome de um disco com o URI de **https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd** é **myVHD**. 

    Desligar o disco de dados da sua VM [az unmanaged-disco da vm desanexar](/cli/azure/vm/unmanaged-disk#detach). O exemplo seguinte desanexa o disco com o nome `myVHD` da VM com o nome `myVMRecovery` no `myResourceGroup` grupo de recursos:

    ```azurecli
    az vm unmanaged-disk detach --resource-group myResourceGroup --vm-name myVMRecovery \
        --name myVHD
    ```


## <a name="create-vm-from-original-hard-disk"></a>Criar a VM de disco rígido original
Para criar uma VM a partir do seu disco rígido virtual original, utilize [este modelo Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd). O modelo JSON é a seguinte hiperligação:

- https://RAW.githubusercontent.com/Azure/Azure-QuickStart-Templates/Master/201-VM-Specialized-VHD/azuredeploy.JSON

O modelo implementa uma VM utilizando o URI do VHD do comando anterior. Implementar o modelo com [criar a implementação do grupo az](/cli/azure/group/deployment#create). Forneça o URI para o VHD original e, em seguida, especifique o tipo de SO, o tamanho da VM e o nome VM da seguinte forma:

```azurecli
az group deployment create --resource-group myResourceGroup --name myDeployment \
  --parameters '{"osDiskVhdUri": {"value": "https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd"},
    "osType": {"value": "Linux"},
    "vmSize": {"value": "Standard_DS1_v2"},
    "vmName": {"value": "myDeployedVM"}}' \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd/azuredeploy.json
```

## <a name="re-enable-boot-diagnostics"></a>Reativar o diagnóstico de arranque
Quando criar a VM a partir do disco rígido virtual existente, diagnóstico de arranque pode não ser ativado automaticamente. Ativar o diagnóstico de arranque com [ativar o diagnóstico de arranque do vm az](/cli/azure/vm/boot-diagnostics#enable). O exemplo seguinte ativa a extensão de diagnóstico da VM com o nome `myDeployedVM` no grupo de recursos denominado `myResourceGroup`:

```azurecli
az vm boot-diagnostics enable --resource-group myResourceGroup --name myDeployedVM
```

## <a name="next-steps"></a>Passos seguintes
Se estiver a ter problemas em ligar à VM, consulte [resolver problemas de SSH ligações a uma VM do Azure](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Para problemas com a aceder a aplicações em execução numa VM, consulte [resolver problemas de conectividade de aplicação numa VM com Linux](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).