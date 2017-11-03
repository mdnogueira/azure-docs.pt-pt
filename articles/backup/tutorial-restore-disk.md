---
title: Restaurar um disco da VM com o Backup do Azure | Microsoft Docs
description: "Saiba como restaurar um disco e crie uma recuperar uma VM no Azure com cópia de segurança e dos serviços de recuperação."
services: backup, virtual-machines
documentationcenter: virtual-machines
author: markgalioto
manager: carmonm
editor: 
tags: azure-resource-manager, virtual-machine-backup
ms.assetid: 
ms.service: backup, virtual-machines
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/28/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 9bc6da13786eb9eb6186ceadf0432b3a3ec2c941
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="restore-a-disk-and-create-a-recovered-vm-in-azure"></a>Restaurar um disco e crie uma VM recuperada no Azure
Cópia de segurança do Azure cria pontos de recuperação que estão armazenados no cofres de recuperação com redundância geográfica. Quando restaurar a partir de um ponto de recuperação, pode restaurar a VM todo ou ficheiros individuais. Este artigo explica como restaurar uma VM concluída. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Lista e pontos de recuperação selecione
> * Restaurar um disco a partir de um ponto de recuperação
> * Criar uma VM a partir do disco restaurado

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este tutorial, necessita que está a executar a CLI do Azure versão 2.0.18 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0]( /cli/azure/install-azure-cli). 


## <a name="prerequisites"></a>Pré-requisitos
Este tutorial requer uma VM com Linux que tenha sido protegido com o Backup do Azure. Para simular um processo de recuperação e a eliminação acidental de VM, crie uma VM a partir de um disco num ponto de recuperação. Se precisar de uma VM com Linux que tenha sido protegido com o Backup do Azure, consulte [cópia de segurança uma máquina virtual no Azure com a CLI](quick-backup-vm-cli.md).


## <a name="backup-overview"></a>Descrição geral da Cópia de Segurança
Quando o Azure inicia uma cópia de segurança, a extensão de cópia de segurança na VM tira um instantâneo de ponto no tempo. A extensão de cópia de segurança está instalada na VM quando é solicitada a primeira cópia de segurança. Cópia de segurança do Azure também pode tirar um instantâneo de armazenamento subjacente se a VM não está em execução quando a cópia de segurança.

Por predefinição, cópia de segurança do Azure cria uma cópia de segurança ficheiros do consistentes do sistema. Assim que a cópia de segurança do Azure assume o instantâneo, os dados são transferidos para o Cofre dos serviços de recuperação. Para maximizar a eficiência, cópia de segurança do Azure identifica e transfere apenas os blocos de dados que foram alterados desde a cópia de segurança anterior.

Quando a transferência de dados estiver concluída, o instantâneo é removido e é criado um ponto de recuperação.


## <a name="list-available-recovery-points"></a>Pontos de recuperação disponíveis de lista
Para restaurar um disco, selecione um ponto de recuperação como origem de dados de recuperação. Como a política predefinida cria um ponto de recuperação por dia e mantém-los durante 30 dias, pode manter um conjunto de pontos de recuperação que permite-lhe selecionar um ponto específico no tempo de recuperação. 

Para ver uma lista de pontos de recuperação disponíveis, utilize [lista de cópia de segurança recoverypoint az](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az_backup_recoverypoint_list). O ponto de recuperação **nome** é usada para recuperar os discos. Neste tutorial, queremos o ponto de recuperação mais recente disponível. O `--query [0].name` parâmetro seleciona o nome do ponto de recuperação mais recente da seguinte forma:

```azurecli-interactive
az backup recoverypoint list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --query [0].name \
    --output tsv
```


## <a name="restore-a-vm-disk"></a>Restaurar um disco VM
Para restaurar o disco do ponto de recuperação, tem primeiro de criar uma conta de armazenamento do Azure. Esta conta de armazenamento é utilizada para armazenar o disco restaurado. Passos adicionais, o disco restaurado é utilizado para criar uma VM.

1. Para criar uma conta de armazenamento, utilize [criar conta de armazenamento az](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az_storage_account_create). O nome da conta de armazenamento tem de estar todo em minúsculas e ser globalmente exclusivo. Substitua *mystorageaccount* com o seu próprio nome exclusivo:

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. Restaurar o disco do ponto de recuperação com [discos de restauro de restaurar a cópia de segurança az](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az_backup_restore_restore_disks). Substitua *mystorageaccount* com o nome da conta de armazenamento que criou no comando anterior. Substitua *myRecoveryPointName* com o nome do ponto de recuperação que obteve na saída do anterior [lista de cópia de segurança recoverypoint az](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az_backup_recoverypoint_list) comando:

    ```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName
    ```


## <a name="monitor-the-restore-job"></a>Monitorizar a tarefa de restauro
Para monitorizar o estado da tarefa de restauro, utilize [lista de tarefas de cópia de segurança de az](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az_backup_job_list):

```azurecli-interactive 
az backup job list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --output table
```

O resultado será semelhante ao exemplo seguinte, que mostra a tarefa de restauro está *em curso*:

```
Name      Operation        Status      Item Name    Start Time UTC       Duration
--------  ---------------  ----------  -----------  -------------------  --------------
7f2ad916  Restore          InProgress  myvm         2017-09-19T19:39:52  0:00:34.520850
a0a8e5e6  Backup           Completed   myvm         2017-09-19T03:09:21  0:15:26.155212
fe5d0414  ConfigureBackup  Completed   myvm         2017-09-19T03:03:57  0:00:31.191807
```

Quando o *estado* dos relatórios de tarefa restauro *concluído*, o disco foi restaurado para a conta de armazenamento.


## <a name="convert-the-restored-disk-to-a-managed-disk"></a>Converter disco restaurado para um disco gerido
A tarefa de restauro cria um disco não gerido. Para criar uma VM a partir do disco, este tem primeiro de ser convertida para um disco gerido.

1. Obter as informações de ligação para a sua conta de armazenamento com [cadeia de ligação de mostrar de conta de armazenamento az](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az_storage_account_show_connection_string). Substitua *mystorageaccount* com o nome do seu armazenamento conta da seguinte forma:
    
    ```azurecli-interactive
    export AZURE_STORAGE_CONNECTION_STRING=$( az storage account show-connection-string \
        --resource-group myResourceGroup \
        --output tsv \
        --name mystorageaccount )
    ```

2. O disco não gerido está protegido na conta de armazenamento. Os seguintes comandos obter informações sobre o disco não gerido e criar uma variável com o nome *uri* que é utilizada no próximo passo, ao criar o disco geridos.

    ```azurecli-interactive
    container=$(az storage container list --query [0].name -o tsv)
    blob=$(az storage blob list --container-name $container --query [0].name -o tsv)
    uri=$(az storage blob url --container-name $container --name $blob -o tsv)
    ```

3. Agora pode criar um disco gerido a partir do seu disco recuperado com [criar disco de az](https://docs.microsoft.com/cli/azure/disk?view=azure-cli-latest#az_disk_create). O *uri* variável do passo anterior é utilizada como origem para o disco geridos.

    ```azurecli-interactive
    az disk create \
        --resource-group myResourceGroup \
        --name myRestoredDisk \
        --source $uri
    ```

4. Agora que tem um disco gerido do seu disco restaurado, limpar a conta de armazenamento e de disco não gerida com [eliminar a conta de armazenamento az](/cli/azure/storage/account?view=azure-cli-latest#az_storage_account_delete). Substitua *mystorageaccount* com o nome do seu armazenamento conta da seguinte forma:

    ```azurecli-interactive
    az storage account delete \
        --resource-group myResourceGroup \
        --name mystorageaccount
    ```


## <a name="create-a-vm-from-the-restored-disk"></a>Criar uma VM a partir do disco restaurado
É o último passo para criar uma VM a partir do disco geridos.

1. Criar uma VM a partir do seu disco geridos com [az vm criar](/cli/azure/vm?view=azure-cli-latest#az_vm_create) da seguinte forma:

    ```azurecli-interactive
    az vm create \
        --resource-group myResourceGroup \
        --name myRestoredVM \
        --attach-os-disk myRestoredDisk \
        --os-type linux
    ```

2. Para confirmar que a VM foi criada a partir do disco recuperado, lista as VMs no seu grupo de recursos com [lista de vm az](/cli/azure/vm?view=azure-cli-latest#az_vm_list) da seguinte forma:

    ```azurecli-interactive
    az vm list --resource-group myResourceGroup --output table
    ```


## <a name="next-steps"></a>Passos seguintes
Neste tutorial, restauro de um disco a partir de um ponto de recuperação e, em seguida, criar uma VM do disco. Aprendeu a:

> [!div class="checklist"]
> * Lista e pontos de recuperação selecione
> * Restaurar um disco a partir de um ponto de recuperação
> * Criar uma VM a partir do disco restaurado

Avançar para o próximo tutorial para saber mais sobre o restauro de ficheiros individuais a partir de um ponto de recuperação.

> [!div class="nextstepaction"]
> [Restaurar ficheiros para uma máquina virtual no Azure](tutorial-restore-files.md)

