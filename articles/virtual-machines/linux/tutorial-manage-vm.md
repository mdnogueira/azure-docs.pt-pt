---
title: Criar e gerir VMs com Linux com a CLI do Azure | Microsoft Docs
description: Tutorial - criar e gerir VMs com Linux com a CLI do Azure
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
ms.openlocfilehash: bef7f6ef13f6d31c16d40deb46f168ae52a9e61b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-manage-linux-vms-with-the-azure-cli"></a>Criar e gerir VMs com Linux com a CLI do Azure

Máquinas virtuais do Azure fornecem um ambiente informático totalmente configurável e flexível. Este tutorial abrange itens de implementação de máquinas virtuais do Azure básica, tais como selecionar um tamanho VM, selecionar uma imagem de VM e implementar uma VM. Saiba como:

> [!div class="checklist"]
> * Criar e ligar a uma VM
> * Selecione e utilizar imagens da VM
> * Ver e utilizar específicos tamanhos de VM
> * Redimensionar uma VM
> * Visualizar e compreender o estado de VM


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este tutorial, necessita que está a executar a CLI do Azure versão 2.0.4 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="create-resource-group"></a>Criar grupo de recursos

Crie um grupo de recursos com o comando [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create). 

Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. Um grupo de recursos tem de ser criado antes de uma máquina virtual. Neste exemplo, um grupo de recursos denominado *myResourceGroupVM* é criado no *eastus* região. 

```azurecli-interactive 
az group create --name myResourceGroupVM --location eastus
```

O grupo de recursos é especificado ao criar ou modificar uma VM, que pode ser vista ao longo deste tutorial.

## <a name="create-virtual-machine"></a>Criar a máquina virtual

Criar uma máquina virtual com o [az vm criar](https://docs.microsoft.com/cli/azure/vm#az_vm_create) comando. 

Ao criar uma máquina virtual, várias opções estão disponíveis, tais como a imagem do sistema operativo, as credenciais administrativas e dimensionamento do disco. Neste exemplo, é criada uma máquina virtual com um nome de *myVM* Ubuntu Server a executar. 

```azurecli-interactive 
az vm create --resource-group myResourceGroupVM --name myVM --image UbuntuLTS --generate-ssh-keys
```

Pode demorar alguns minutos para criar a VM. Quando tiver sido criada a VM, a CLI do Azure produz informações sobre a VM. Tome nota do `publicIpAddress`, este endereço pode ser utilizado para aceder à máquina virtual... 

```azurecli-interactive 
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroupVM"
}
```

## <a name="connect-to-vm"></a>Ligar à VM

Agora pode ligar à VM com o SSH na Shell de nuvem do Azure ou do seu computador local. Substituir o endereço IP de exemplo com o `publicIpAddress` que anotou no passo anterior.

```bash
ssh 52.174.34.95
```

Assim que tiver sessão iniciada na VM, pode instalar e configurar as aplicações. Quando tiver terminado, feche a sessão SSH como normal:

```bash
exit
```

## <a name="understand-vm-images"></a>Compreender as imagens VM

No Azure marketplace inclui várias imagens que podem ser utilizadas para criar as VMs. Nos passos anteriores, uma máquina virtual foi criada utilizando uma imagem de Ubuntu. Neste passo, a CLI do Azure é utilizada para procurar no marketplace para uma imagem do CentOS, em seguida, é utilizada para implementar uma segunda máquina virtual.  

Para ver uma lista de mais frequentemente utilizadas imagens, utilize o [lista de imagens de vm az](/cli/azure/vm/image#list) comando.

```azurecli-interactive 
az vm image list --output table
```

A saída do comando devolve as imagens VM mais populares no Azure.

```bash
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
WindowsServer  MicrosoftWindowsServer  2016-Datacenter     MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest     Win2016Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
CentOS         OpenLogic               7.3                 OpenLogic:CentOS:7.3:latest                                     CentOS               latest
openSUSE-Leap  SUSE                    42.2                SUSE:openSUSE-Leap:42.2:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7.3                 RedHat:RHEL:7.3:latest                                          RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
```

Uma lista completa pode ser vista adicionando o `--all` argumento. Também é possível filtrar a lista de imagens por `--publisher` ou `–-offer`. Neste exemplo, a lista está filtrada para todas as imagens com uma oferta que corresponda ao *CentOS*. 

```azurecli-interactive 
az vm image list --offer CentOS --all --output table
```

Saída parcial:

```azurecli-interactive 
Offer             Publisher         Sku   Urn                                     Version
----------------  ----------------  ----  --------------------------------------  -----------
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.201501         6.5.201501
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.201503         6.5.201503
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.201506         6.5.201506
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.20150904       6.5.20150904
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.20160309       6.5.20160309
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.20170207       6.5.20170207
```

Para implementar uma VM utilizando a imagem específica, tome nota do valor do *Urn* coluna. Ao especificar a imagem, o número de versão da imagem pode ser substituído por "mais recente", que seleciona a versão mais recente da distribuição. Neste exemplo, o `--image` argumento é utilizado para especificar a versão mais recente de uma imagem do CentOS 6.5.  

```azurecli-interactive 
az vm create --resource-group myResourceGroupVM --name myVM2 --image OpenLogic:CentOS:6.5:latest --generate-ssh-keys
```

## <a name="understand-vm-sizes"></a>Compreender os tamanhos de VM

Um tamanho de máquina virtual determina a quantidade de recursos de computação, tais como CPU, memória e GPU que ficam disponíveis para a máquina virtual. Máquinas virtuais devem ser dimensionados de forma adequadamente para a carga de trabalho esperado. Se a carga de trabalho aumenta, podem ser redimensionada uma máquina virtual existente.

### <a name="vm-sizes"></a>Tamanhos de VM

A tabela seguinte categoriza tamanhos em casos de utilização.  

| Tipo                     | Tamanhos           |    Descrição       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Fins gerais](sizes-general.md)         |Dsv3, Dv3, série DSv2, Dv2, DS, D, Av2, A0 7| Equilibrada CPU para memória. Ideal para dev / teste e pequena a médias soluções de aplicações e dados.  |
| [Com otimização de computação](sizes-compute.md)   | FS, F             | Elevada da CPU para memória. Boa para aplicações de tráfego média, os dispositivos de rede e processos de batch.        |
| [Com otimização de memória](../virtual-machines-windows-sizes-memory.md)    | Esv3, Ev3, M, GS, G, série DSv2, DS, Dv2, D   | Elevada memória-para-core. Excelente para bases de dados relacionais, caches médias e grandes e análise de memória.                 |
| [Com otimização de armazenamento](../virtual-machines-windows-sizes-storage.md)      | Ls                | Débito e E/S de disco elevados. Ideal para bases de dados de Macrodados, SQL e NoSQL.                                                         |
| [GPU](sizes-gpu.md)          | NV, NC            | VMs especializadas visadas para a composição de gráfico pesada e edição de vídeo.       |
| [Elevado desempenho](sizes-hpc.md) | H, A8-11          | A nossa VMs de CPU mais poderosas com interfaces de rede de alto débito opcional (RDMA). 


### <a name="find-available-vm-sizes"></a>Localizar os tamanhos VM disponíveis

Para ver uma lista de tamanhos VM disponíveis numa região específica, utilize o [az lista-tamanhos de vm](/cli/azure/vm#list-sizes) comando. 

```azurecli-interactive 
az vm list-sizes --location eastus --output table
```

Saída parcial:

```azurecli-interactive 
  MaxDataDiskCount    MemoryInMb  Name                      NumberOfCores    OsDiskSizeInMb    ResourceDiskSizeInMb
------------------  ------------  ----------------------  ---------------  ----------------  ----------------------
                 2          3584  Standard_DS1                          1           1047552                    7168
                 4          7168  Standard_DS2                          2           1047552                   14336
                 8         14336  Standard_DS3                          4           1047552                   28672
                16         28672  Standard_DS4                          8           1047552                   57344
                 4         14336  Standard_DS11                         2           1047552                   28672
                 8         28672  Standard_DS12                         4           1047552                   57344
                16         57344  Standard_DS13                         8           1047552                  114688
                32        114688  Standard_DS14                        16           1047552                  229376
                 1           768  Standard_A0                           1           1047552                   20480
                 2          1792  Standard_A1                           1           1047552                   71680
                 4          3584  Standard_A2                           2           1047552                  138240
                 8          7168  Standard_A3                           4           1047552                  291840
                 4         14336  Standard_A5                           2           1047552                  138240
                16         14336  Standard_A4                           8           1047552                  619520
                 8         28672  Standard_A6                           4           1047552                  291840
                16         57344  Standard_A7                           8           1047552                  619520
```

### <a name="create-vm-with-specific-size"></a>Criar a VM com tamanho específico

No exemplo anterior para a criação de VM, um tamanho não foi fornecido, resultando num tamanho predefinido. Pode ser selecionado um tamanho VM ao tempo de criação utilizando [az vm criar](/cli/azure/vm#create) e `--size` argumento. 

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroupVM \
    --name myVM3 \
    --image UbuntuLTS \
    --size Standard_F4s \
    --generate-ssh-keys
```

### <a name="resize-a-vm"></a>Redimensionar uma VM

Depois de implementar uma VM, pode ser redimensionada para aumentar ou diminuir a alocação de recursos. Pode ver atual do tamanho de uma VM com [mostrar de vm az](/cli/azure/vm#show):

```azurecli-interactive
az vm show --resource-group myResourceGroupVM --name myVM --query hardwareProfile.vmSize
```

Antes de redimensionar uma VM, verifique se o tamanho pretendido está disponível no cluster do Azure atual. O [az vm-vm-redimensionamento-opções de lista](/cli/azure/vm#list-vm-resize-options) comando devolve a lista de tamanhos. 

```azurecli-interactive 
az vm list-vm-resize-options --resource-group myResourceGroupVM --name myVM --query [].name
```
Se o tamanho pretendido estiver disponível, a VM pode ser redimensionada partir de um estado ligado no, no entanto, é reiniciado durante a operação. Utilize o [az vm redimensionar]( /cli/azure/vm#resize) comando para efetuar o redimensionamento.

```azurecli-interactive 
az vm resize --resource-group myResourceGroupVM --name myVM --size Standard_DS4_v2
```

Se o tamanho pretendido não está no cluster atual, a VM tem de ser desalocada antes da operação de redimensionamento pode ocorrer. Utilize o [az vm desalocar]( /cli/azure/vm#deallocate) comando para parar e Desalocação da VM. Tenha em atenção, quando a VM seja ativada anterior, quaisquer dados no disco temporário poderão ser removidos. O endereço IP público também altera a menos que está a ser utilizado um endereço IP estático. 

```azurecli-interactive 
az vm deallocate --resource-group myResourceGroupVM --name myVM
```

Depois de anulada, pode ocorrer o redimensionamento. 

```azurecli-interactive 
az vm resize --resource-group myResourceGroupVM --name myVM --size Standard_GS1
```

Depois do redimensionamento, a VM pode ser iniciada.

```azurecli-interactive 
az vm start --resource-group myResourceGroupVM --name myVM
```

## <a name="vm-power-states"></a>Estados de energia VM

VM do Azure pode ter um dos vários Estados de energia. Este estado representa o estado atual da VM de ponto de vista do hipervisor. 

### <a name="power-states"></a>Estados de energia

| Estado de energia | Descrição
|----|----|
| A Iniciar | Indica que a máquina virtual está a ser iniciada. |
| A Executar | Indica que a máquina virtual está em execução. |
| A Parar | Indica se a máquina virtual está a ser parada. | 
| Parada | Indica se a máquina virtual está parada. Máquinas virtuais no estado de paragem ainda implicar custos de computação.  |
| Desalocação | Indica que a máquina virtual está a ser desalocada. |
| Desalocar | Indica que a máquina virtual é removido do hipervisor, mas continuará disponível no plane controlo. Máquinas virtuais no Estado Deallocated não implicar custos de computação. |
| - | Indica que o estado de energia da máquina virtual é desconhecido. |

### <a name="find-power-state"></a>Determinar o estado de energia

Para obter o estado de uma VM específica, utilize o [az vm obter vista de instância](/cli/azure/vm#get-instance-view) comando. Lembre-se de que especifique um nome válido para uma máquina virtual e o grupo de recursos. 

```azurecli-interactive 
az vm get-instance-view \
    --name myVM \
    --resource-group myResourceGroupVM \
    --query instanceView.statuses[1] --output table
```

Saída:

```azurecli-interactive 
ode                DisplayStatus    Level
------------------  ---------------  -------
PowerState/running  VM running       Info
```

## <a name="management-tasks"></a>Tarefas de gestão

Durante o ciclo de vida de uma máquina virtual, poderá querer executar tarefas de gestão, tais como iniciar, parar ou eliminar uma máquina virtual. Além disso, pode querer criar scripts para automatizar tarefas repetitivas ou complexas. Utilizar a CLI do Azure, muitas tarefas comuns de gestão podem ser executadas na linha de comandos ou em scripts. 

### <a name="get-ip-address"></a>Obter o endereço IP

Este comando devolve os endereços IP privados e públicos de uma máquina virtual.  

```azurecli-interactive 
az vm list-ip-addresses --resource-group myResourceGroupVM --name myVM --output table
```

### <a name="stop-virtual-machine"></a>Parar a máquina virtual

```azurecli-interactive 
az vm stop --resource-group myResourceGroupVM --name myVM
```

### <a name="start-virtual-machine"></a>Iniciar a máquina virtual

```azurecli-interactive 
az vm start --resource-group myResourceGroupVM --name myVM
```

### <a name="delete-resource-group"></a>Eliminar grupo de recursos

Também eliminar um grupo de recursos elimina todos os recursos contidos, tais como a VM, a rede virtual e o disco. O `--no-wait` parâmetro devolve controlo ao pedido sem aguardar a conclusão da operação. O `--yes` parâmetro, confirma que pretende eliminar os recursos sem uma linha de comandos adicional para fazê-lo.

```azurecli-interactive 
az group delete --name myResourceGroupVM --no-wait --yes
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu sobre a criação de VM básica e gestão tais como:

> [!div class="checklist"]
> * Criar e ligar a uma VM
> * Selecione e utilizar imagens da VM
> * Ver e utilizar específicos tamanhos de VM
> * Redimensionar uma VM
> * Visualizar e compreender o estado de VM

Avançar para o próximo tutorial para saber mais sobre os discos VM.  

> [!div class="nextstepaction"]
> [Criar e gerir VM discos](./tutorial-manage-disks.md)
