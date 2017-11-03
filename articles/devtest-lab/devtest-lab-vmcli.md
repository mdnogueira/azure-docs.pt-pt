---
title: "Criar e gerir máquinas virtuais no DevTest Labs com a CLI do Azure | Microsoft Docs"
description: "Saiba como utilizar o Azure DevTest Labs para criar e gerir máquinas virtuais com o Azure CLI 2.0"
services: devtest-lab,virtual-machines
documentationcenter: na
author: lisawong19
manager: douge
editor: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: liwong
ms.openlocfilehash: a3af12ba0598d60b55b3714ae1690fe3e5b54a42
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>Criar e gerir máquinas virtuais com DevTest Labs utilizando a CLI do Azure
Este guia de introdução irá ajudá-lo através da criação, a iniciar, ligar, atualizar e limpeza de um computador de desenvolvimento no laboratório. 

Antes de começar:

* Se não tiver sido criado um laboratório, pode encontrar instruções [aqui](devtest-lab-create-lab.md).

* [Instalar a CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). Para iniciar, execute o início de sessão az para criar uma ligação com o Azure. 

## <a name="create-and-verify-the-virtual-machine"></a>Criar e certifique-se a máquina virtual 
Crie uma VM a partir de uma imagem do marketplace com ssh autenticação.
```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```
> [!NOTE]
> Coloque o **grupo de recursos do laboratório** nome no parâmetro – grupo de recursos.
>

Se pretender criar uma VM utilizando um fórmula, utilize o - parâmetro fórmula no [az laboratório vm criar](https://docs.microsoft.com/cli/azure/lab/vm#az_lab_vm_create).


Certifique-se de que a VM está disponível.
```azurecli
az lab vm show --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup --expand 'properties($expand=ComputeVm,NetworkInterface)' --query '{status: computeVm.statuses[0].displayStatus, fqdn: fqdn, ipAddress: networkInterface.publicIpAddress}'
```
```json
{
  "fqdn": "lisalabvm.southcentralus.cloudapp.azure.com",
  "ipAddress": "13.85.228.112",
  "status": "Provisioning succeeded"
}
```

## <a name="start-and-connect-to-the-virtual-machine"></a>Iniciar e ligar à máquina virtual
Inicie uma VM.
```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```
> [!NOTE]
> Coloque o **grupo de recursos do laboratório** nome no parâmetro – grupo de recursos.
>

Ligar a uma VM: [SSH](../virtual-machines/linux/mac-create-ssh-keys.md) ou [ambiente de trabalho remoto](../virtual-machines/windows/connect-logon.md).
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>Atualizar a máquina virtual
Aplicam-se de artefactos para uma VM.
```azurecli
az lab vm apply-artifacts --lab-name  sampleLabName --name sampleVMName  --resource-group sampleResourceGroup  --artifacts @/artifacts.json
```

```json
[
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-java",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-install-nodejs",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-apt-package",
    "parameters": [
      {
        "name": "packages",
        "value": "abcd"
      },
      {
        "name": "update",
        "value": "true"
      },
      {
        "name": "options",
        "value": ""
      }
    ]
  } 
]
```

Lista os artefactos disponíveis no laboratório.
```azurecli
az lab vm show --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup --expand "properties(\$expand=artifacts)" --query 'artifacts[].{artifactId: artifactId, status: status}'
```
```json
{
  "artifactId": "/subscriptions/abcdeftgh1213123/resourceGroups/lisalab123RG822645/providers/Microsoft.DevTestLab/labs/lisalab123/artifactSources/public repo/artifacts/linux-install-nodejs",
  "status": "Succeeded"
}
```

## <a name="stop-and-delete-the-virtual-machine"></a>Parar e eliminar a máquina virtual    
Pare uma VM.
```azurecli
az lab vm stop --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

Elimine uma VM.
```azurecli
az lab vm delete --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]