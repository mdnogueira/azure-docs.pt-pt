---
title: Gerir discos do Azure com a CLI do Azure | Microsoft Docs
description: Tutorial - Gerir discos do Azure com a CLI do Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/02/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 882446ba32252490e27056c7c5c9a8f755e26ee6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-azure-disks-with-the-azure-cli"></a>Gerir discos do Azure com a CLI do Azure

Máquinas virtuais do Azure utilizar discos para armazenar as VMs do sistema operativo, aplicações e dados. Quando cria uma VM é importante escolher um tamanho de disco e a configuração adequada para a carga de trabalho esperada. Este tutorial abrange a implementação e gestão de discos VM. Pode obter informações sobre:

> [!div class="checklist"]
> * Discos de SO e discos temporários
> * discos de dados
> * Standard e discos Premium
> * Desempenho de disco
> * Anexar e preparar os discos de dados
> * O redimensionamento de discos
> * Instantâneos de disco


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este tutorial, necessita que está a executar a CLI do Azure versão 2.0.4 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="default-azure-disks"></a>Predefinição discos do Azure

Quando é criada uma máquina virtual do Azure, dois discos são automaticamente anexados à máquina virtual. 

**Disco do sistema operativo** -discos de sistema de operativo podem ser dimensionados de forma até 1 terabyte e aloja o sistema de operativo VMs. O disco de SO assinalada como */dev/sda* por predefinição. O disco de configuração do disco de SO a colocação em cache está otimizado para desempenho do SO. Devido a esta configuração, o disco de SO **não devem** alojar aplicações ou dados. Para aplicações e dados, utilize os discos de dados, que estão descritos neste artigo. 

**Disco temporário** -temporários discos utilizam uma unidade de estado sólido que está localizada no mesmo anfitrião da VM do Azure. Discos temporários são altamente performant e podem ser utilizados para operações, tais como o processamento de dados temporária. No entanto, se a VM for movida para um novo anfitrião, todos os dados armazenados num disco temporário são removidos. O tamanho do disco temporário é determinado pelo tamanho da VM. Discos temporários são etiquetados */dev/sdb* e ter uma pontodemontagem de */mnt*.

### <a name="temporary-disk-sizes"></a>Tamanhos de disco temporário

| Tipo | Tamanho da VM | Tamanho máximo de disco temporário (GB) |
|----|----|----|
| [Fins gerais](sizes-general.md) | A e a série de D | 800 |
| [Com otimização de computação](sizes-compute.md) | Série F | 800 |
| [Com otimização de memória](../virtual-machines-windows-sizes-memory.md) | Série de D e G | 6144 |
| [Com otimização de armazenamento](../virtual-machines-windows-sizes-storage.md) | Série de L | 5630 |
| [GPU](sizes-gpu.md) | Série N | 1440 |
| [Elevado desempenho](sizes-hpc.md) | A e a série de H | 2000 |

## <a name="azure-data-disks"></a>Discos de dados do Azure

Discos de dados adicionais podem ser adicionados para instalar aplicações e armazenar dados. Os discos de dados devem ser utilizados em qualquer situação em que o armazenamento de dados durável e reativa for pretendido. Cada disco de dados tem uma capacidade máxima de 1 terabyte. O tamanho da máquina virtual determina quantos discos de dados podem ser anexados a uma VM. Para cada núcleo VM, podem ser anexados a discos de dados de dois. 

### <a name="max-data-disks-per-vm"></a>Discos de dados de máx. por VM

| Tipo | Tamanho da VM | Discos de dados de máx. por VM |
|----|----|----|
| [Fins gerais](sizes-general.md) | A e a série de D | 32 |
| [Com otimização de computação](sizes-compute.md) | Série F | 32 |
| [Com otimização de memória](../virtual-machines-windows-sizes-memory.md) | Série de D e G | 64 |
| [Com otimização de armazenamento](../virtual-machines-windows-sizes-storage.md) | Série de L | 64 |
| [GPU](sizes-gpu.md) | Série N | 48 |
| [Elevado desempenho](sizes-hpc.md) | A e a série de H | 32 |

## <a name="vm-disk-types"></a>Tipos de disco da VM

O Azure oferece dois tipos de disco.

### <a name="standard-disk"></a>Disco Standard

O Armazenamento Standard está protegido por HDDs e fornece armazenamento económico, mantendo o desempenho. Discos padrão são ideais para um dev económica e teste de carga de trabalho.

### <a name="premium-disk"></a>Disco Premium

Os discos Premium são apoiados por disco de elevado desempenho, baixa latência baseadas em SSD. Perfeita para as VMs com a carga de trabalho de produção. Armazenamento Premium suporta-série DS, série DSv2-série, série GS e série FS VMs. Os discos Premium são fornecidos em três tipos (P10, P20, P30), o tamanho do disco determina o tipo de disco. Quando selecionar, um tamanho de disco, o valor é arredondado para o tipo de próximo. Por exemplo, se o tamanho do disco é inferior a 128 GB, o tipo de disco é P10. Se o tamanho do disco entre 129 GB e 512 GB, o tamanho é um P20. Nada mais 512 GB, o tamanho é um P30.

### <a name="premium-disk-performance"></a>Desempenho do disco Premium

|Tipo de disco de armazenamento Premium | P10 | P20 | P30 |
| --- | --- | --- | --- |
| Tamanho do disco (arredondar) | 128 GB | 512 GB | 1.024 GB (1 TB) |
| IOPs Máx por disco | 500 | 2,300 | 5,000 |
Débito por disco | 100 MB/s | 150 MB/s | 200 MB/s |

Enquanto a tabela acima identifica IOPS máximo por disco, um nível mais elevado de desempenho pode ser alcançado ao striping vários discos de dados. Por exemplo, uma VM Standard_GS5 pode alcançar um máximo de 80.000 IOPS. Para obter informações detalhadas sobre o IOPS máximo por VM, consulte [tamanhos de VM com Linux](sizes.md).

## <a name="create-and-attach-disks"></a>Criar e anexar discos

Os discos de dados podem ser criados e ligados no momento de criação de VM ou para uma VM existente.

### <a name="attach-disk-at-vm-creation"></a>Anexar o disco durante a criação de VM

Crie um grupo de recursos com o comando [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create). 

```azurecli-interactive 
az group create --name myResourceGroupDisk --location eastus
```

Criar uma VM utilizando o [az vm criar]( /cli/azure/vm#create) comando. O `--datadisk-sizes-gb` argumento é utilizado para especificar que um disco adicional deve ser criado e ligado à máquina virtual. Para criar e anexar mais de um disco, utilize uma lista delimitada por espaços de valores de tamanho de disco. No exemplo seguinte, é criada uma VM com discos de dados de dois, ambos os 128 GB. Porque os tamanhos de disco são 128 GB, estes discos são configurados como P10s, que fornecem IOPS máximo de 500 por disco.

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroupDisk \
  --name myVM \
  --image UbuntuLTS \
  --size Standard_DS2_v2 \
  --data-disk-sizes-gb 128 128 \
  --generate-ssh-keys
```

### <a name="attach-disk-to-existing-vm"></a>Anexar o disco à VM existente

Para criar e anexar um disco novo a uma máquina virtual existente, utilize o [anexar o disco da vm az](/cli/azure/vm/disk#attach) comando. O exemplo seguinte cria um disco premium, 128 gigabytes de tamanho e liga-o para a VM criada no último passo.

```azurecli-interactive 
az vm disk attach --vm-name myVM --resource-group myResourceGroupDisk --disk myDataDisk --size-gb 128 --sku Premium_LRS --new 
```

## <a name="prepare-data-disks"></a>Preparar os discos de dados

Depois de um disco tem foi anexado à máquina virtual, o sistema operativo tem de ser configurado para utilizar o disco. O exemplo seguinte mostra como configurar manualmente um disco. Este processo também pode ser automatizado utilizando nuvem-init, que é abordada um [tutorial posterior](./tutorial-automate-vm-deployment.md).

### <a name="manual-configuration"></a>Configuração manual

Crie uma ligação SSH com a máquina virtual. Substitua o endereço IP de exemplo com o IP público da máquina virtual.

```azurecli-interactive 
ssh 52.174.34.95
```

Particionar o disco com `fdisk`.

```bash
(echo n; echo p; echo 1; echo ; echo ; echo w) | sudo fdisk /dev/sdc
```

Gravar um sistema de ficheiros para a partição utilizando o `mkfs` comando.

```bash
sudo mkfs -t ext4 /dev/sdc1
```

Monte o disco novo para que seja acessível no sistema operativo.

```bash
sudo mkdir /datadrive && sudo mount /dev/sdc1 /datadrive
```

O disco pode agora ser acedido através de *datadrive* pontodemontagem, que pode ser verificada executando a `df -h` comando. 

```bash
df -h
```

O resultado mostra o novo disco montado em */datadrive*.

```bash
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        30G  1.4G   28G   5% /
/dev/sdb1       6.8G   16M  6.4G   1% /mnt
/dev/sdc1        50G   52M   47G   1% /datadrive
```

Para garantir que a unidade é remontadas da após um reinício, tem de ser adicionado para o *etc/fstab* ficheiro. Para tal, obter o UUID do disco com o `blkid` utilitário.

```bash
sudo -i blkid
```

A saída apresenta o UUID da unidade, `/dev/sdc1` neste caso.

```bash
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

Adicionar uma linha semelhante à seguinte para o *etc/fstab* ficheiro. Também em atenção que escrever barreiras as eficazes pode ser desativada através de *barreira = 0*, esta configuração pode melhorar o desempenho de disco. 

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive  ext4    defaults,nofail,barrier=0   1  2
```

Agora que o disco tiver sido configurado, feche a sessão SSH.

```bash
exit
```

## <a name="resize-vm-disk"></a>Redimensionar disco da VM

Assim que tiver sido implementada uma VM, o disco do sistema operativo ou qualquer discos de dados anexados podem ser aumentados de tamanho. Aumentar o tamanho de um disco é vantajoso quando necessitar de mais espaço de armazenamento ou de um nível mais elevado de desempenho (P10 P20, P30). Tenha em atenção de que não podem ser diminuídos discos de tamanho.

Antes de aumentar o tamanho do disco, é necessário o Id ou nome do disco. Utilize o [lista de discos az](/cli/azure/disk#az_disk_list) comando para devolver todos os discos num grupo de recursos. Tome nota do nome do disco que gostaria de redimensionamento.

```azurecli-interactive 
az disk list -g myResourceGroupDisk --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' --output table
```

Também tem de ser desalocada a VM. Utilize o [az vm desalocar]( /cli/azure/vm#deallocate) comando para parar e Desalocação da VM.

```azurecli-interactive 
az vm deallocate --resource-group myResourceGroupDisk --name myVM
```

Utilize o [atualização de disco az](/cli/azure/vm/disk#update) comando para redimensionar o disco. Neste exemplo redimensiona um disco chamado *myDataDisk* a 1 terabyte.

```azurecli-interactive 
az disk update --name myDataDisk --resource-group myResourceGroupDisk --size-gb 1023
```

Depois de concluída a operação de redimensionamento, inicie a VM.

```azurecli-interactive 
az vm start --resource-group myResourceGroupDisk --name myVM
```

Se tiver redimensionado disco do sistema operativo, a partição é automaticamente expandido. Se tiver redimensionado um disco de dados, quaisquer partições atuais tem de ser expandido no sistema de operativo VMs.

## <a name="snapshot-azure-disks"></a>Instantâneos de discos do Azure

Criar um instantâneo de disco cria uma cópia apenas, o ponto no tempo de leitura do disco. Instantâneos VM do Azure são úteis para guardar rapidamente o estado de uma VM antes de efetuar alterações de configuração. No caso das alterações de configuração provar ser indesejado, estado de VM é possível restaurar o instantâneo. Quando uma VM tem mais de um disco, é obtido um instantâneo de cada disco independentemente de outras pessoas. Para fazer cópias de segurança da aplicação, considere a parar a VM antes de tirar instantâneos de disco. Em alternativa, utilize o [serviço de cópia de segurança do Azure](/azure/backup/), que lhe permite efetuar cópias de segurança automatizadas enquanto a VM está em execução.

### <a name="create-snapshot"></a>Criar instantâneo

Antes de criar um instantâneo de disco da máquina virtual, é necessário o Id ou nome do disco. Utilize o [mostrar de vm az](https://docs.microsoft.com/en-us/cli/azure/vm#az_vm_show) comando para devolver o id de disco. Neste exemplo, o id de disco é armazenado numa variável, para que possa ser utilizado num passo posterior.

```azurecli-interactive 
osdiskid=$(az vm show -g myResourceGroupDisk -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
```

Agora que tem o id do disco de máquina virtual, o comando seguinte cria um instantâneo do disco.

```azurcli
az snapshot create -g myResourceGroupDisk --source "$osdiskid" --name osDisk-backup
```

### <a name="create-disk-from-snapshot"></a>Criar disco de instantâneo

Este instantâneo, em seguida, pode ser convertido para um disco, o que pode ser utilizado para recriar a máquina virtual.

```azurecli-interactive 
az disk create --resource-group myResourceGroupDisk --name mySnapshotDisk --source osDisk-backup
```

### <a name="restore-virtual-machine-from-snapshot"></a>Restauro do instantâneo de máquina virtual

Para demonstrar a recuperação da máquina virtual, elimine a máquina virtual existente. 

```azurecli-interactive 
az vm delete --resource-group myResourceGroupDisk --name myVM
```

Crie uma nova máquina virtual a partir do disco de instantâneo.

```azurecli-interactive 
az vm create --resource-group myResourceGroupDisk --name myVM --attach-os-disk mySnapshotDisk --os-type linux
```

### <a name="reattach-data-disk"></a>Reattach disco de dados

Todos os discos de dados tem de ser novamente ligado à máquina virtual.

Primeiro localizar o disco de dados nome utilizando o [lista de discos az](https://docs.microsoft.com/cli/azure/disk#az_disk_list) comando. Neste exemplo coloca o nome do disco numa variável designada *datadisk*, que é utilizada no próximo passo.

```azurecli-interactive 
datadisk=$(az disk list -g myResourceGroupDisk --query "[?contains(name,'myVM')].[name]" -o tsv)
```

Utilize o [anexar o disco da vm az](https://docs.microsoft.com/cli/azure/vm/disk#az_vm_disk_attach) comando para anexar o disco.

```azurecli-interactive 
az vm disk attach –g myResourceGroupDisk –-vm-name myVM –-disk $datadisk
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu sobre os tópicos de discos VM, tais como:

> [!div class="checklist"]
> * Discos de SO e discos temporários
> * discos de dados
> * Standard e discos Premium
> * Desempenho de disco
> * Anexar e preparar os discos de dados
> * O redimensionamento de discos
> * Instantâneos de disco

Avançar para o próximo tutorial para saber mais sobre automatizar a configuração de VM.

> [!div class="nextstepaction"]
> [Automatizar a configuração da VM](./tutorial-automate-vm-deployment.md)
