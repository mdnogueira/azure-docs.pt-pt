---
title: Desligar um disco de dados de uma VM com Linux - Azure | Microsoft Docs
description: "Saiba como desligar um disco de dados de uma máquina virtual no Azure utilizando o CLI 2.0 ou no portal do Azure."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.topic: article
ms.date: 03/21/2017
ms.author: cynthn
ms.openlocfilehash: de0222d897ed2cf94be98501c39385ac88f866fc
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="how-to-detach-a-data-disk-from-a-linux-virtual-machine"></a>Como desligar um disco de dados de uma máquina virtual Linux

Quando já não precisar de um disco de dados que esteja ligado a uma máquina virtual, pode desligá-lo facilmente. Remove o disco da máquina virtual, mas não o remover do armazenamento. 

> [!WARNING]
> Se desanexar um disco que não é eliminado automaticamente. Se tiver subscrito para o Premium storage, continuará a implicar custos de armazenamento para o disco. Para obter mais informações, consulte [preços e faturação quando utilizar o Premium Storage](../windows/premium-storage.md#pricing-and-billing). 
> 
> 

Se pretender voltar a utilizar os dados existentes no disco, pode voltar a ligá-lo à mesma máquina virtual ou a outra.  

## <a name="detach-a-data-disk-using-cli-20"></a>Desligar um disco de dados utilizando a CLI 2.0

```azurecli
az vm disk detach -g myResourceGroup --vm-name myVm -n myDataDisk
```

O disco permanece no armazenamento, mas já não está ligado a uma máquina virtual.


## <a name="detach-a-data-disk-using-the-portal"></a>Desanexar um disco de dados com o portal
1. No portal hub, selecione **máquinas virtuais**.
2. Selecione a máquina virtual que tenha o disco de dados que pretende desanexar e clique em **parar** ao anular atribuição de VM.
3. No painel da máquina virtual, selecione **discos**.
4. Na parte superior do **discos** painel, selecione **editar**.
5. No **discos** painel, na extremidade direita do disco de dados que pretende desanexar, clique em de ![imagem do botão de anulação de exposições](./media/detach-disk/detach.png) exposição do botão.
5. Depois do disco foi removido, clique em Guardar na parte superior do painel.
6. No painel da máquina virtual, clique em **descrição geral** e, em seguida, clique em de **iniciar** botão na parte superior do painel para reiniciar a VM.

O disco permanece no armazenamento, mas já não está ligado a uma máquina virtual.








## <a name="next-steps"></a>Passos seguintes
Se pretender reutilizar o disco de dados, pode apenas [anexe-o para outra VM](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

