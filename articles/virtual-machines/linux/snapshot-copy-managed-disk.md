---
title: "Criar um instantâneo de um VHD no Azure | Microsoft Docs"
description: "Saiba como criar uma cópia de um VHD no Azure como uma cópia de segurança ou de resolução de problemas."
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/09/2017
ms.author: cynthn
ms.openlocfilehash: da00c48f7da5a9be146f4fdb626c93db746c0f9b
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2017
---
# <a name="create-a-snapshot"></a>Criar um instantâneo 

Tire um instantâneo de um disco de SO ou dados problemas de VHD para cópia de segurança ou para resolver problemas de VM. Um instantâneo é uma cópia completa só de leitura de um VHD. 

## <a name="use-azure-cli-20-to-take-a-snapshot"></a>Utilizar o Azure CLI 2.0 para criar um instantâneo

O exemplo seguinte requer o 2.0 do CLI do Azure instalado e registado na sua conta do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli). 

Os passos seguintes mostram como tirar um instantâneo, utilizando o `az snapshot create` comando com o `--source-disk` parâmetro. O exemplo seguinte parte do princípio de que não há uma VM chamada `myVM` criado com um disco de SO gerido no `myResourceGroup` grupo de recursos.

```azure-cli
# take the disk id with which to create a snapshot
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create -g myResourceGroup --source "$osDiskId" --name osDisk-backup
```

O resultado deverá ter um aspeto semelhante ao seguinte:

```json
{
  "accountType": "Standard_LRS",
  "creationData": {
    "createOption": "Copy",
    "imageReference": null,
    "sourceResourceId": null,
    "sourceUri": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/disks/osdisk_6NexYgkFQU",
    "storageAccountId": null
  },
  "diskSizeGb": 30,
  "encryptionSettings": null,
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/osDisk-backup",
  "location": "westus",
  "name": "osDisk-backup",
  "osType": "Linux",
  "ownerId": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "tags": null,
  "timeCreated": "2017-02-06T21:27:10.172980+00:00",
  "type": "Microsoft.Compute/snapshots"
}
```

## <a name="use-azure-portal-to-take-a-snapshot"></a>Utilize o portal do Azure para criar um instantâneo 

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. A partir do canto superior esquerdo, clique em **novo** e procure **instantâneo**.
3. No painel do instantâneo, clique em **criar**.
4. Introduza um **nome** para o instantâneo.
5. Selecione um [Grupo de recursos](../../azure-resource-manager/resource-group-overview.md#resource-groups) ou escreva o nome para um novo. 
6. Selecione um localização do datacenter do Azure.  
7. Para **disco de origem**, selecione o disco de gerido para instantâneo.
8. Selecione o **tipo de conta** utilizar para armazenar o instantâneo. Recomendamos **Standard_LRS** , a menos que o ficheiro necessário armazenados num disco elevado desempenho.
9. Clique em **Criar**.

Se planear utilizar o instantâneo para criar um disco gerido e ligá-lo uma VM que tem de ser a execução elevada, utilize o parâmetro `--sku Premium_LRS` com o `az snapshot create` comando. Esta ação cria o instantâneo, de modo a que seja armazenada como um disco de gerido para Premium. Os discos Premium geridos melhor efetuar porque são unidades de estado sólido (SSDs), mas custo mais do que os discos padrão (HDDs).


## <a name="next-steps"></a>Passos seguintes

 Crie uma máquina virtual a partir de um instantâneo ao criar um disco gerido do instantâneo e, em seguida, anexar o disco novo gerido como disco do SO. Para obter mais informações, consulte o [criar uma VM a partir de um instantâneo](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json) script.

