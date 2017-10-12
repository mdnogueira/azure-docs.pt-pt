---
title: "Discos de Dados Anexados a Conjuntos de Dimensionamento de Máquinas Virtuais do Azure | Microsoft Docs"
description: "Saiba como utilizar discos de dados anexados com conjuntos de dimensionamento de máquinas virtuais"
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 4/25/2017
ms.author: guybo
ms.openlocfilehash: 22c7e589efa9a9f401549ec9b95c58c4eaf07b94
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-vm-scale-sets-and-attached-data-disks"></a>Conjuntos de dimensionamento de VMs do Azure e discos de dados anexados
Agora, os [conjuntos de dimensionamento de máquinas virtuais](/azure/virtual-machine-scale-sets/) do Azure suportam máquinas virtuais com discos de dados anexados. Pode definir os discos de dados no perfil de armazenamento dos conjuntos de dimensionamento que foram criados com os Managed Disks do Azure. Anteriormente, as únicas opções de armazenamento diretamente anexadas disponíveis com as VMs em conjuntos de dimensionamento eram a unidade de SO e as unidades temporárias.

> [!NOTE]
>  Quando cria um conjunto de dimensionamento com discos de dados anexados definidos, continua a ter de montar e formatar os discos a partir de uma VM para poder utilizá-los (tal como acontece com VMs do Azure autónomas). Uma forma conveniente de fazer isto consiste em utilizar uma extensão de script personalizado que chama um script padrão para particionar e formatar todos os discos de dados numa VM.

## <a name="create-a-scale-set-with-attached-data-disks"></a>Criar um conjunto de dimensionamento com discos de dados anexados
Uma forma simples de criar um conjunto de dimensionamento com discos anexados é utilizar o comando _vmss create_ da [CLI do Azure](https://github.com/Azure/azure-cli). O exemplo seguinte cria um grupo de recursos do Azure e um conjunto de dimensionamento de VM de 10 VMs Ubuntu, cada uma com dois discos de dados anexados, de 50 GB e 100 GB, respetivamente.
```bash
az group create -l southcentralus -n dsktest
az vmss create -g dsktest -n dskvmss --image ubuntults --instance-count 10 --data-disk-sizes-gb 50 100
```
Tenha em atenção que o comando _vmss create_ repõe as predefinições de certos valores de configuração, caso não os especifique. Para ver as opções disponíveis que pode ignorar, tente:
```bash
az vmss create --help
```
Outra forma de criar um conjunto de dimensionamento com discos de dados anexados será definir um conjunto de dimensionamento num modelo do Azure Resource Manager, incluir uma secção _dataDisks_ no _storageProfile_ e implementar o modelo. O exemplo acima dos discos de 50 GB e 100 GB seria definido desta forma no modelo:
```json
"dataDisks": [
    {
    "lun": 1,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 50
    },
    {
    "lun": 2,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 100
    }
]
```
Pode ver um exemplo de modelo de conjunto de dimensionamento com um disco anexado completo e pronto a utilizar definido aqui: [https://github.com/chagarw/MDPP/tree/master/101-vmss-os-data](https://github.com/chagarw/MDPP/tree/master/101-vmss-os-data).

## <a name="adding-a-data-disk-to-an-existing-scale-set"></a>Adicionar um disco de dados a um conjunto de dimensionamento existente
> [!NOTE]
>  Apenas pode anexar discos de dados a um conjunto de dimensionamento que foi criado com os [Azure Managed Disks](./virtual-machine-scale-sets-managed-disks.md).

Pode adicionar um disco de dados a um conjunto de dimensionamento de VM com o comando _az vmss disk attach_ da CLI do Azure. Certifique-se de que especifica um lun que não esteja já em utilização. O seguinte exemplo da CLI adiciona uma unidade de 50 GB ao lun 3:
```bash
az vmss disk attach -g dsktest -n dskvmss --size-gb 50 --lun 3
```

O seguinte exemplo do PowerShell adiciona uma unidade de 50 GB ao lun 3:
```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName myvmssrg -VMScaleSetName myvmss
$vmss = Add-AzureRmVmssDataDisk -VirtualMachineScaleSet $vmss -Lun 3 -Caching 'ReadWrite' -CreateOption Empty -DiskSizeGB 50 -StorageAccountType StandardLRS
Update-AzureRmVmss -ResourceGroupName myvmssrg -Name myvmss -VirtualMachineScaleSet $vmss
```

> [!NOTE]
> Diferentes tamanhos de VM têm limites diferentes relativamente ao número de unidades anexadas que suportam. Verifique as [características do tamanho da máquina virtual](../virtual-machines/windows/sizes.md) antes de adicionar um novo disco.

Também pode adicionar um disco ao criar uma nova entrada na propriedade _dataDisks_ no _storageProfile_ de uma definição de conjunto de dimensionamento e ao aplicar essa alteração. Para testar isto, localize a definição de um conjunto de dimensionamento existente no [Explorador de Recursos do Azure](https://resources.azure.com/). Selecione _Editar_ e adicione um novo disco à lista de discos de dados. Por exemplo, com o exemplo acima:
```json
"dataDisks": [
    {
    "lun": 1,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 50
    },
    {
    "lun": 2,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 100
    },
    {
    "lun": 3,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 20
    }          
]
```

Em seguida, selecione _PUT_ para aplicar as alterações ao seu conjunto de dimensionamento. Este exemplo funciona desde que utilize um tamanho de VM que suporte mais do que dois discos de dados anexados.

> [!NOTE]
> Quando altera uma definição do conjunto de dimensionamento, como adicionar ou remover um disco de dados, a alteração é aplicada a todas as VMs criadas recentemente, mas apenas afeta as VMs existentes se a propriedade _upgradePolicy_ estiver definida como "Automático". Se estiver definida como "Manual", tem de aplicar manualmente o novo modelo às VMs existentes. Pode fazer isto no portal, através do comando _Update-AzureRmVmssInstance_ do PowerShell ou do comando _az vmss update-instances_ da CLI.

## <a name="adding-pre-populated-data-disks-to-an-existent-scale-set"></a>Adicionar discos de dados pré-preenchidos a um conjunto de dimensionamento existente 
> Quando adicionar discos a um modelo de conjunto de dimensionamento existente, por predefinição, o disco será sempre criado vazio. Este cenário também inclui novas instâncias criadas pelo conjunto de dimensionamento. Este comportamento acontece porque a definição do conjunto de dimensionamento tem um disco de dados vazio. Para criar unidades de dados pré-preenchidas para um modelo de conjunto de dimensionamento existente, pode escolher entre duas opções:

* Copie dados da VM 0 da instância para o(s) disco(s) de dados nas outras VMs, executando um script personalizado.
* Crie uma imagem gerida com o disco do SO e o disco de dados (com os dados necessários) e crie um novo conjunto de dimensionamento com a imagem. Desta forma, cada nova VM criada terá um disco de dados que é fornecido na definição do conjunto de dimensionamento. Uma vez que esta definição irá referir-se a uma imagem com um disco de dados que tem dados personalizados, cada máquina virtual no conjunto de dimensionamento irá surgir automaticamente com estas alterações.

> A forma de criar uma imagem personalizada pode ser encontrada aqui: [Criar uma imagem gerida de uma VM generalizada no Azure](/azure/virtual-machines/windows/capture-image-resource/) 

> O utilizador tem de capturar a VM 0 da instância que tem os dados requeridos e, em seguida, utilizar o vhd dessa definição de imagem.

## <a name="removing-a-data-disk-from-a-scale-set"></a>Remover um disco de dados de um conjunto de dimensionamento
Pode remover um disco de dados de um conjunto de dimensionamento de VM com o comando _az vmss disk detach_ da CLI do Azure. Por exemplo, o seguinte comando remove o disco definido no lun 2:
```bash
az vmss disk detach -g dsktest -n dskvmss --lun 2
```  
Da mesma forma, para remover um disco de um conjunto de dimensionamento, pode também remover uma entrada da propriedade _dataDisks_ no _storageProfile_ e aplicar a alteração. 

## <a name="additional-notes"></a>Notas adicionais
O suporte para o Azure Managed Disks e para discos de dados anexados a conjuntos de dimensionamento está disponível na versão da API [_2016-04-30-preview_](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-compute/2016-04-30-preview/swagger/compute.json) ou posterior da API Microsoft.Compute.

Na implementação inicial do suporte para discos anexados para conjuntos de dimensionamento, não pode anexar nem desanexar discos de dados a/de VMs individuais num conjunto de dimensionamento.

O suporte do portal do Azure para discos de dados anexados em conjuntos de dimensionamento é limitado inicialmente. Dependendo das suas necessidades, pode utilizar modelos do Azure, a CLI, o PowerShell, SDKs e a API REST para gerir discos anexados.


