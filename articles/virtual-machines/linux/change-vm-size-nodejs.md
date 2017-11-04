---
title: Como redimensionar uma VM com Linux com a CLI do Azure 1.0 | Microsoft Docs
description: "Como aumentar verticalmente ou reduzir verticalmente uma máquina virtual do Linux, ao alterar o tamanho da VM."
services: virtual-machines-linux
documentationcenter: na
author: mikewasson
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/16/2016
ms.author: mwasson
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 72f5a3cd6463befd5108040ed166984281bfc5f0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="resize-a-linux-vm-with-azure-cli-10"></a>Redimensionar uma VM com Linux com a CLI do Azure 1.0

## <a name="overview"></a>Descrição geral

Depois de aprovisionar uma máquina virtual (VM), pode aumentar ou reduzir verticalmente a VM alterando o [tamanho da VM][vm-sizes]. Em alguns casos, tem de Desalocação pela primeira vez a VM. Isto pode acontecer se o novo tamanho não está disponível no cluster de hardware que está a alojar a VM.

Este artigo mostra como redimensionar uma VM com Linux utilizando o [CLI do Azure][azure-cli].

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="cli-versions-to-complete-the-task"></a>Versões CLI para concluir a tarefa
Pode concluir a tarefa utilizando uma das seguintes versões CLI:

- [Azure CLI 1.0](#resize-a-linux-vm) – nosso CLI para os clássica e resource Gestão modelos de implementação (Este artigo)
- [CLI 2.0 do Azure](change-vm-size.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - CLI de próxima geração para o modelo de implementação de gestão de recursos


## <a name="resize-a-linux-vm"></a>Redimensionar uma VM com Linux
Redimensionar uma VM, execute os seguintes passos.

1. Execute o seguinte comando da CLI. Este comando lista os tamanhos VM que estão disponíveis no cluster de hardware em que a VM está alojada.
   
    ```azurecli
    azure vm sizes -g myResourceGroup --vm-name myVM
    ```
2. Se o tamanho pretendido estiver listado, execute o seguinte comando para redimensionar a VM.
   
    ```azurecli
    azure vm set -g myResourceGroup --vm-size <new-vm-size> -n myVM  \
        --enable-boot-diagnostics
        --boot-diagnostics-storage-uri https://mystorageaccount.blob.core.windows.net/ 
    ```
   
    A VM será reiniciada durante este processo. Após o reinício, serão possível remapear o SO existente e os discos de dados. Nada no disco temporário serão perdido.
   
    Utilize o `--enable-boot-diagnostics` opção ativa [diagnóstico de arranque][boot-diagnostics], para iniciar sessão quaisquer erros relacionados com o arranque.
3. Caso contrário, se o tamanho pretendido não estiver listado, execute os seguintes comandos ao anular atribuição de VM, redimensioná-la e, em seguida, reiniciar a VM.
   
    ```azurecli
    azure vm deallocate -g myResourceGroup myVM
    azure vm set -g myResourceGroup --vm-size <new-vm-size> -n myVM \
        --enable-boot-diagnostics --boot-diagnostics-storage-uri \
        https://mystorageaccount.blob.core.windows.net/ 
    azure vm start -g myResourceGroup myVM
    ```
   
   > [!WARNING]
   > Também Desalocação da VM versões quaisquer endereços IP dinâmicos atribuídos à VM. Os SO e discos de dados não são afetados.
   > 
   > 

## <a name="next-steps"></a>Passos seguintes
Para escalabilidade adicional, execute várias instâncias VM e aumentar horizontalmente. Para obter mais informações, consulte [Dimensionar automaticamente máquinas Linux de um conjunto de dimensionamento de Máquina Virtual][scale-set]. 

<!-- links -->

[azure-cli]:../../cli-install-nodejs.md
[boot-diagnostics]: https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md 
[vm-sizes]:sizes.md
