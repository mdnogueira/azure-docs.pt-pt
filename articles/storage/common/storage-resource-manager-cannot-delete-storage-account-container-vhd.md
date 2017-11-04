---
title: Resolver erros ao eliminar as contas do storage do Azure, contentores ou VHDs | Microsoft Docs
description: Resolver erros ao eliminar as contas do storage do Azure, contentores ou VHDs
services: storage
documentationcenter: 
author: passaree
manager: cshepard
editor: na
tags: storage
ms.assetid: 17403aa1-fe8d-45ec-bc33-2c0b61126286
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 7a3c9422887592c29c2eae8bd75cf960865808fd
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="troubleshoot-storage-delete-errors-in-resource-manager-deployment"></a>Resolver erros de eliminação de armazenamento na implementação do Resource Manager
Este artigo fornece orientação de resolução de problemas quando um dos seguintes erros ocorrer ao tentar eliminar a conta de armazenamento do Azure, contentor ou um blob na implementação do Azure Resource Manager.

>**Falha ao eliminar a conta de armazenamento 'StorageAccountName'. Erro: Não é possível eliminar a conta de armazenamento porque os artefactos estão a ser utilizados.**

>**Falha ao eliminar # fora # contentores:<br>vhds: não existe atualmente uma concessão no contentor e não foi especificado nenhum ID de concessão no pedido.**

>**Falha ao eliminar # fora de blobs de #:<br>BlobName.vhd: não existe atualmente uma concessão no blob e não foi especificado nenhum ID de concessão no pedido.**

Os discos rígidos virtuais utilizados em VMs do Azure são ficheiros. vhd armazenados como blobs de páginas numa conta do storage standard ou premium no Azure.  Podem encontrar mais informações acerca dos discos do Azure [aqui](../../virtual-machines/windows/about-disks-and-vhds.md). Azure impede que a eliminação de um disco que está ligado a uma VM para evitar danificar os. Também impede que a eliminação de contentores e contas de armazenamento que tem um blob de página que está ligado a uma VM. 

## <a name="solution"></a>Solução
O processo para eliminar uma conta de armazenamento, um contentor ou um blob quando recebe um dos erros acima é: 
1. [Identificar os blobs ligados a uma VM](#step-1-identify-blobs-attached-to-a-vm)
2. [VMs de eliminação com ligado **disco do SO**](#step-2-delete-vm-to-detach-os-disk)
3. [Desligue todos os **discos de dados** da VM restantes](#step-3-detach-data-disk-from-the-vm)

Repita a eliminar conta de armazenamento, contentor ou blob depois de concluídos os passos acima.

### <a name="step-1-identify-blob-attached-to-a-vm"></a>Passo 1: Identificar blob ligado a uma VM

#### <a name="scenario-1-deleting-a-blob--identify-attached-vm"></a>Cenário 1: Eliminar um blob – identificar VM anexada
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. No Hub menu, selecione **todos os recursos**. Vá para a conta de armazenamento em **serviço Blob** selecione **contentores** e navegue para o blob será eliminada.
3. Se o blob **estado de concessão** é **Leased**, clique com o botão direito e selecione **Editar metadados** para abrir o painel de metadados de Blob. 

    ![Captura de ecrã do portal, com o armazenamento de blobs de conta e certo > hilighted "Editar metadados"](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/utd-edit-metadata-sm.png)

4. No painel de metadados de Blob, verifique e registe o valor para **MicrosoftAzureCompute_VMName**. Este valor é o nome da VM em que o VHD está anexado. (Consulte **importante** se este campo não existir)
5. No painel de metadados de Blob, verifique e registe o valor de **MicrosoftAzureCompute_DiskType**. Esta identifica se o disco ligado ao disco de SO ou dados (consulte **importante** se este campo não existir). 

     ![Captura de ecrã do portal, com o painel de "Metadados do Blob" armazenamento aberto](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/utd-blob-metadata-sm.png)

6. Se o tipo de disco do blob é **OSDisk** siga [passo 2: eliminar VM anular a exposição do disco do SO](#step-2-delete-vm-to-detach-os-disk). Caso contrário, se o tipo de disco do blob é **DataDisk** siga os passos no [passo 3: exposição do disco de dados da VM](#step-3-detach-data-disk-from-the-vm). 

> [!IMPORTANT]
> Se **MicrosoftAzureCompute_VMName** e **MicrosoftAzureCompute_DiskType** não aparecem nos metadados do blob, ele indica que o blob é explicitamente concedido e não está ligado a uma VM. Não não possível eliminar blobs em leasing sem ultrapassar o período de concessão primeiro. Para interromper a concessão, clique no blob e selecione **concessão quebra**. Os blobs em leasing que não estão ligados a uma VM evitar a eliminação do blob mas não impedir a eliminação da conta de armazenamento ou de contentor.

#### <a name="scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms"></a>Cenário 2: Eliminar um contentor - identificar todos os blob(s) dentro do contentor que está anexados às VMs
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. No Hub menu, selecione **todos os recursos**. Vá para a conta de armazenamento em **serviço Blob** selecione **contentores** e localizar o contentor ser eliminado.
3. Clique para abrir o contentor e será apresentada a lista de blobs dentro do mesmo. Identifique todos os blobs com o tipo de Blob = **BLOBs de páginas** e estado de concessão = **Leased** desta lista. Siga [cenário 1](#step-1-identify-blobs-attached-to-a-vm) para identificar a VM associada a cada um destes blobs.

    ![Captura de ecrã do portal, com os blobs de conta de armazenamento e o "concessão estado" com "Leased" realçado](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/utd-disks-sm.png)

4. Siga [passo 2](#step-2-delete-vm-to-detach-os-disk) e [passo 3](#step-3-detach-data-disk-from-the-vm) ao eliminar a VM com **OSDisk** e desanexar **DataDisk**. 

#### <a name="scenario-3-deleting-storage-account---identify-all-blobs-within-storage-account-that-are-attached-to-vms"></a>Cenário 3: A eliminar o armazenamento conta – identificar todos os blob(s) na conta do storage que está anexados às VMs
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. No Hub menu, selecione **todos os recursos**. Vá para a conta de armazenamento em **serviço Blob** selecione **contentores**.

    ![Captura de ecrã do portal, com os contentores de conta de armazenamento e o "concessão estado" com "Leased" realçado](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/utd-containers-sm.png)

3. No **contentores** painel, identifique todos os contentores onde **estado de concessão** é **Leased** e siga [cenário 2](#scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms) para cada  **Nos concessionados** contentor.
4. Siga [passo 2](#step-2-delete-vm-to-detach-os-disk) e [passo 3](#step-3-detach-data-disk-from-the-vm) ao eliminar a VM com **OSDisk** e desanexar **DataDisk**. 

### <a name="step-2-delete-vm-to-detach-os-disk"></a>Passo 2: Eliminar VM anular a exposição do disco do SO
Se o VHD é um disco de SO, tem de eliminar a VM para que o VHD ligado pode ser eliminado. Nenhuma ação adicional serão necessária para os discos de dados ligados à VM mesma depois de concluídos estes passos:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. No Hub menu, selecione **máquinas virtuais**.
3. Selecione a que o VHD se encontra ligado à VM.
4. Certifique-se de que nada ativamente está a utilizar a máquina virtual, e que já não necessita da máquina virtual.
5. Na parte superior do **detalhes da Máquina Virtual** painel, selecione **eliminar**e, em seguida, clique em **Sim** para confirmar.
6. A VM deve ser eliminada, mas o VHD pode ser mantido. No entanto, o VHD já não deve ser ligado a uma VM ou ter uma concessão no mesmo. Pode demorar alguns minutos para que concessão ser libertado. Para verificar que a concessão for lançada, navegue para a localização de blob e, no **Blob propriedades** painel, o **estado de concessão** deve ser **disponível**.

### <a name="step-3-detach-data-disk-from-the-vm"></a>Passo 3: Exposição do disco de dados da VM
Se o VHD é um disco de dados, desligue o VHD da VM para remover o período de concessão:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. No Hub menu, selecione **máquinas virtuais**.
3. Selecione a que o VHD se encontra ligado à VM.
4. Selecione **discos** no **detalhes da Máquina Virtual** painel.
5. Selecione o disco de dados a eliminar o VHD está anexado. Pode determinar que blob está ligado no disco, verificando o URL do VHD.
6. Pode verificar a localização de blob clicando no disco para verificar o caminho **URI de VHD** campo.
7. Selecione **editar** de **discos** painel.
8. Clique em **desanexar ícone** do disco de dados será eliminada.

     ![Captura de ecrã do portal, com o painel de "Metadados do Blob" armazenamento aberto](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/utd-vm-disks-edit.png)

9. Selecione **Guardar**. Agora deverá possível desanexar o disco da VM, e o VHD já não deve ter uma concessão no mesmo. Pode demorar alguns minutos para que concessão ser libertado. Para verificar que a concessão foi lançada, navegue para a localização de blob e, no **Blob propriedades** painel, o **estado de concessão** o valor deve ser **Unlocked** ou **Disponíveis**.

## <a name="next-steps"></a>Passos seguintes
Repita a eliminação de objeto de armazenamento que tinha falhado anteriormente.

