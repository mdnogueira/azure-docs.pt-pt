---
title: "Adicione um disco à VM com Linux utilizando a CLI do Azure | Microsoft Docs"
description: Saiba como adicionar um disco persistente na VM com Linux com a CLI do Azure 1.0 e 2.0.
keywords: "máquina virtual do Linux, adicionar o disco de recursos"
services: virtual-machines-linux
documentationcenter: 
author: rickstercdn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 3005a066-7a84-4dc5-bdaa-574c75e6e411
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 02/02/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9db7d300b745001906bdc38769dcbe6e4d7c7b83
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="add-a-disk-to-a-linux-vm"></a>Adicionar um disco a uma VM com Linux
Este artigo mostra como ligar um disco persistente à sua VM, de modo a que pode manter os dados -, mesmo se a VM é reaprovisionada devido a manutenção ou redimensionar. 


## <a name="use-managed-disks"></a>Utilizar discos geridos
Discos gerida do Azure simplifica a gestão de disco para as VMs do Azure ao gerir as contas de armazenamento associadas aos discos VM. Só tem de especificar o tipo (Premium ou Standard) e o tamanho do disco é necessário e Azure cria e gere o disco para si. Para obter mais informações, consulte [descrição geral de discos geridos](managed-disks-overview.md).


### <a name="attach-a-new-disk-to-a-vm"></a>Anexar um disco novo a uma VM
Se tiver apenas um novo disco na sua VM, utilize o [anexar o disco da vm az](/cli/azure/vm/disk?view=azure-cli-latest#az_vm_disk_attach) comando com o `--new` parâmetro. Se a VM está a ser uma zona de disponibilidade, o disco é criado automaticamente no mesmo horário da VM. Para obter mais informações, consulte [zonas de descrição geral da disponibilidade](../../availability-zones/az-overview.md). O exemplo seguinte cria um disco chamado *myDataDisk* que *50*tamanho Gb:

```azurecli
az vm disk attach -g myResourceGroup --vm-name myVM --disk myDataDisk \
  --new --size-gb 50
```

### <a name="attach-an-existing-disk"></a>Anexar um disco existente 
Em muitos casos. Anexe discos que já foram criados. Para anexar um disco existente, localize o ID de disco e passar o ID para o [anexar o disco da vm az](/cli/azure/vm/disk?view=azure-cli-latest#az_vm_disk_attach) comando. As seguintes consultas de exemplo para um disco chamado *myDataDisk* no *myResourceGroup*, em seguida, liga-lo para a VM com o nome *myVM*:

```azurecli
# find the disk id
diskId=$(az disk show -g myResourceGroup -n myDataDisk --query 'id' -o tsv)
az vm disk attach -g myResourceGroup --vm-name myVM --disk $diskId
```

O resultado é algo semelhante ao seguinte (pode utilizar o `-o table` opção para qualquer comando para formatar a saída no):

```json
{
  "accountType": "Standard_LRS",
  "creationData": {
    "createOption": "Empty",
    "imageReference": null,
    "sourceResourceId": null,
    "sourceUri": null,
    "storageAccountId": null
  },
  "diskSizeGb": 50,
  "encryptionSettings": null,
  "id": "/subscriptions/<guid>/resourceGroups/rasquill-script/providers/Microsoft.Compute/disks/myDataDisk",
  "location": "westus",
  "name": "myDataDisk",
  "osType": null,
  "ownerId": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "tags": null,
  "timeCreated": "2017-02-02T23:35:47.708082+00:00",
  "type": "Microsoft.Compute/disks"
}
```


## <a name="use-unmanaged-disks"></a>Utilizar discos não geridos
Os discos não geridos precisam overhead adicional para criar e gerir as contas de armazenamento subjacente. Os discos não geridos são criados na mesma conta de armazenamento como o disco do SO. Para criar e anexar um disco não gerido, utilize o [az unmanaged-disco da vm ligar](/cli/azure/vm/unmanaged-disk?view=azure-cli-latest#az_vm_unmanaged_disk_attach) comando. O exemplo seguinte anexa um *50*GB disco não gerido para a VM com o nome *myVM* no grupo de recursos denominado *myResourceGroup*:

```azurecli
az vm unmanaged-disk attach -g myResourceGroup -n myUnmanagedDisk --vm-name myVM \
  --new --size-gb 50
```


## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Ligar à VM do Linux para montar o disco novo
A partição, formatar e montar o disco novo para a VM com Linux pode utilizá-lo, SSH para a VM do Azure. Para obter mais informações, veja [como utilizar SSH com Linux no Azure](mac-create-ssh-keys.md). O exemplo seguinte estabelece ligação a uma VM com a entrada DNS pública do *mypublicdns.westus.cloudapp.azure.com* com o nome de utilizador *azureuser*: 

```bash
ssh azureuser@mypublicdns.westus.cloudapp.azure.com
```

Assim que estiver ligado à VM, está pronto para anexar um disco. Em primeiro lugar, localize a utilização de disco `dmesg` (pode variar o método utilizado para detetar o disco novo). O exemplo seguinte utiliza dmesg para filtrar *SCSI* discos:

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

Aqui, *sdc* é o disco que queremos. Particionar o disco com `fdisk`, certifique-se um disco principal na partição 1 e aceitar as outras predefinições. O exemplo seguinte inicia o `fdisk` processos no */dev/sdc*:

```bash
sudo fdisk /dev/sdc
```

O resultado é semelhante ao seguinte exemplo:

```bash
Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
Building a new DOS disklabel with disk identifier 0x2a59b123.
Changes will remain in memory only, until you decide to write them.
After that, of course, the previous content won't be recoverable.

Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-10485759, default 2048):
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-10485759, default 10485759):
Using default value 10485759
```

Crie a partição escrevendo `p` na linha de comandos da seguinte forma:

```bash
Command (m for help): p

Disk /dev/sdc: 5368 MB, 5368709120 bytes
255 heads, 63 sectors/track, 652 cylinders, total 10485760 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x2a59b123

   Device Boot      Start         End      Blocks   Id  System
/dev/sdc1            2048    10485759     5241856   83  Linux

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
```

Agora, escrever um sistema de ficheiros para a partição com o `mkfs` comando. Especifique o tipo de sistema de ficheiros e o nome do dispositivo. O exemplo seguinte cria um *ext4* no sistema de ficheiros a */dev/sdc1* partição que foi criada nos passos anteriores:

```bash
sudo mkfs -t ext4 /dev/sdc1
```

O resultado é semelhante ao seguinte exemplo:

```bash
mke2fs 1.42.9 (4-Feb-2014)
Discarding device blocks: done
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
327680 inodes, 1310464 blocks
65523 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=1342177280
40 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
    32768, 98304, 163840, 229376, 294912, 819200, 884736
Allocating group tables: done
Writing inode tables: done
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done
```

Agora, crie um diretório de montagem de sistema de ficheiros com `mkdir`. O exemplo seguinte cria um diretório em */datadrive*:

```bash
sudo mkdir /datadrive
```

Utilize `mount` para montar, em seguida, o sistema de ficheiros. O exemplo seguinte monta o */dev/sdc1* de partição para a */datadrive* ponto de montagem:

```bash
sudo mount /dev/sdc1 /datadrive
```

Para garantir que a unidade é remontadas da automaticamente após um reinício, tem de ser adicionado para o *etc/fstab* ficheiro. Também recomendamos vivamente que o UUID (universalmente Unique IDentifier) é utilizado numa *etc/fstab* referir-se a unidade em vez de apenas o nome de dispositivo (como, por exemplo */dev/sdc1*). Se o sistema operativo Deteta um erro de disco durante o arranque, utilizar o UUID evita a ser montado numa localização especificada de disco incorreta. Restante discos de dados iria, em seguida, ser atribuído os mesmos IDs de dispositivo. Para localizar o UUID da nova unidade, utilize o `blkid` utilitário:

```bash
sudo -i blkid
```

O resultado semelhante ao seguinte exemplo:

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

> [!NOTE]
> Editar incorretamente o **etc/fstab** ficheiros poderia resultar num sistema de arranque. Se não souber, consulte a documentação de distribuição para obter informações sobre como editar corretamente este ficheiro. Também é recomendável que uma cópia de segurança do ficheiro /etc/fstab foi criada antes de editar.

Em seguida, abra o *etc/fstab* ficheiro num editor de texto da seguinte forma:

```bash
sudo vi /etc/fstab
```

Neste exemplo, utilizamos o valor UUID para o */dev/sdc1* dispositivo que foi criado nos passos anteriores e pontodemontagem de */datadrive*. Adicione a seguinte linha ao final do *etc/fstab* ficheiro:

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

> [!NOTE]
> Mais tarde a remover um disco de dados sem necessitar de editar fstab pode fazer com que a VM falhar efetuar o arranque. A maioria das distribuições de fornecer um o *nofail* e/ou *nobootwait* fstab opções. Estas opções permitir que um sistema de arranque, mesmo se o disco não consegue montar no momento do arranque. Consulte a documentação de sua distribuição para obter mais informações sobre estes parâmetros.
> 
> O *nofail* opção garante que a VM entrar, mesmo se o sistema de ficheiros está danificado ou o disco não existe no momento do arranque. Sem esta opção, pode encontrar o comportamento, conforme descrito em [não é possível SSH para a VM com Linux devido a erros FSTAB](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/)

### <a name="trimunmap-support-for-linux-in-azure"></a>Suporte de cortar/UNMAP para Linux no Azure
Alguns kernels Linux suportam operações de cortar/UNMAP para eliminar os blocos no disco. Esta funcionalidade é útil principalmente no armazenamento standard para informar o Azure eliminado páginas já não são válido e pode ser eliminada e pode poupar dinheiro se criar ficheiros grandes e, em seguida, elimine-os.

Existem duas formas de ativar a limitação suportam na sua VM com Linux. Normalmente, consulte a distribuição para a abordagem recomendada:

* Utilize o `discard` montar opção na *etc/fstab*, por exemplo:

    ```bash
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2
    ```
* Em alguns casos, o `discard` opção pode ter implicações de desempenho. Em alternativa, pode executar o `fstrim` manualmente de comando na linha de comandos ou adicioná-lo à sua crontab regularmente a executar:
  
    **Ubuntu**
  
    ```bash
    sudo apt-get install util-linux
    sudo fstrim /datadrive
    ```
  
    **RHEL/CentOS**

    ```bash
    sudo yum install util-linux
    sudo fstrim /datadrive
    ```

## <a name="troubleshooting"></a>Resolução de problemas
[!INCLUDE [virtual-machines-linux-lunzero](../../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>Passos seguintes
* Lembre-se que o novo disco não está disponível para a VM se é reiniciado, a menos que escrever essas informações para o [fstab](http://en.wikipedia.org/wiki/Fstab) ficheiro.
* Para garantir a VM com Linux está configurada corretamente, consulte o [otimizar o desempenho do computador Linux](optimization.md) recomendações.
* Expandir a capacidade de armazenamento através da adição de discos adicionais e [configurar RAID](configure-raid.md) para um desempenho adicional.

