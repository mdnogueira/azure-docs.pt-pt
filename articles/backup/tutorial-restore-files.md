---
title: Restaurar ficheiros para uma VM com o Backup do Azure | Microsoft Docs
description: "Saiba como efetuar restauros ao nível do ficheiro numa VM do Azure com cópia de segurança e dos serviços de recuperação."
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
ms.date: 09/29/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: e1bbae56b70c50fcf691db47efd9dc587686e7da
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="restore-files-to-a-virtual-machine-in-azure"></a>Restaurar ficheiros para uma máquina virtual no Azure
Cópia de segurança do Azure cria pontos de recuperação que estão armazenados no cofres de recuperação com redundância geográfica. Quando restaurar a partir de um ponto de recuperação, pode restaurar a VM todo ou ficheiros individuais. Este artigo fornece detalhes sobre como restaurar ficheiros individuais. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Lista e pontos de recuperação selecione
> * Ligar um ponto de recuperação para uma VM
> * Restaurar ficheiros a partir de um ponto de recuperação

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este tutorial, necessita que está a executar a CLI do Azure versão 2.0.18 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [instalar o Azure CLI 2.0](/cli/azure/install-azure-cli). 


## <a name="prerequisites"></a>Pré-requisitos
Este tutorial requer uma VM com Linux que tenha sido protegido com o Backup do Azure. Para simular um processo de recuperação e a eliminação acidental de ficheiro, elimine uma página de um servidor web. Se precisar de uma VM com Linux que executa um servidor Web e tiver sido protegido com o Backup do Azure, consulte [cópia de segurança uma máquina virtual no Azure com a CLI](quick-backup-vm-cli.md).


## <a name="backup-overview"></a>Descrição geral da Cópia de Segurança
Quando o Azure inicia uma cópia de segurança, a extensão de cópia de segurança na VM tira um instantâneo de ponto no tempo. A extensão de cópia de segurança está instalada na VM quando é solicitada a primeira cópia de segurança. Cópia de segurança do Azure também pode tirar um instantâneo de armazenamento subjacente se a VM não está em execução quando a cópia de segurança.

Por predefinição, cópia de segurança do Azure cria uma cópia de segurança ficheiros do consistentes do sistema. Assim que a cópia de segurança do Azure assume o instantâneo, os dados são transferidos para o Cofre dos serviços de recuperação. Para maximizar a eficiência, cópia de segurança do Azure identifica e transfere apenas os blocos de dados que foram alterados desde a cópia de segurança anterior.

Quando a transferência de dados estiver concluída, o instantâneo é removido e é criado um ponto de recuperação.


## <a name="delete-a-file-from-a-vm"></a>Eliminar um ficheiro a partir de uma VM
Se acidentalmente eliminar ou efetuar alterações a um ficheiro, pode restaurar ficheiros individuais a partir de um ponto de recuperação. Este processo permite-lhe procurar os ficheiros de cópia de segurança num ponto de recuperação e restaurar apenas os ficheiros que precisa. Neste exemplo, é possível eliminar um ficheiro de um servidor web para demonstrar o processo de recuperação ao nível dos ficheiros.

1. Para ligar à VM, obter o endereço IP da VM com [mostrar de vm az](/cli/azure/vm?view=azure-cli-latest#az_vm_show):

     ```azurecli-interactive
     az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
     ```

2. Para confirmar que funciona atualmente o web site, abra um browser para o endereço IP público da sua VM. Deixe a janela do browser aberta.

    ![NGINX predefinido de página web](./media/tutorial-restore-files/nginx-working.png)

3. Ligar à VM com o SSH. Substitua *publicIpAddress* com o endereço IP público que obteve num comando anterior:

    ```bash
    ssh publicIpAddress
    ```

4. Elimine a página predefinida do servidor web em */var/www/html/index.nginx-debian.html* da seguinte forma:

    ```bash
    sudo rm /var/www/html/index.nginx-debian.html
    ```

5. No seu browser, atualize a página web. O web site já não carrega a página, conforme mostrado no exemplo seguinte:

    ![NGINX web site já não carrega a página predefinida](./media/tutorial-restore-files/nginx-broken.png)

6. Feche a sessão SSH para a VM da seguinte forma:

    ```bash
    exit
    ```


## <a name="generate-file-recovery-script"></a>Gerar o script de recuperação de ficheiros
Para restaurar os ficheiros, a cópia de segurança do Azure fornece um script seja executado na sua VM que liga o ponto de recuperação como uma unidade local. Pode procurar esta unidade local, restaurar os ficheiros para a própria VM e desligue o ponto de recuperação. Cópia de segurança do Azure continua a fazer cópias de segurança os dados com base na política de agendamento e de retenção atribuída.

1. Para pontos de recuperação da lista para a VM, utilize [lista de cópia de segurança recoverypoint az](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az_backup_recoverypoint_list). Neste exemplo, vamos selecione o ponto de recuperação mais recente para a VM com o nome *myVM* que está protegido na *myRecoveryServicesVault*:

    ```azurecli-interactive
    az backup recoverypoint list \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --query [0].name \
        --output tsv
    ```

2. Para obter o script que se liga, ou monta, o ponto de recuperação para a VM, utilize [rp de montagem de ficheiros de restauro de cópia de segurança az](https://docs.microsoft.com/cli/azure/backup/restore/files?view=azure-cli-latest#az_backup_restore_files_mount_rp). O exemplo seguinte obtém o script para a VM com o nome *myVM* que está protegido na *myRecoveryServicesVault*.

    Substitua *myRecoveryPointName* com o nome dos pontos de recuperação que obteve no comando anterior:

    ```azurecli-interactive
    az backup restore files mount-rp \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --rp-name myRecoveryPointName
    ```

    O script é transferido e uma palavra-passe é apresentada, como no exemplo seguinte:

    ```
    File downloaded: myVM_we_1571974050985163527.sh. Use password c068a041ce12465
    ```

3. Para transferir o script para a VM, utilize cópia Secure (SCP). Forneça o nome do seu script transferido e substitua *publicIpAddress* com o endereço IP público da sua VM. Certifique-se de que incluiu `:` no fim do SCP comando da seguinte forma:

    ```bash
    scp myVM_we_1571974050985163527.sh 52.174.241.110:
    ```


## <a name="restore-file-to-your-vm"></a>Restaurar ficheiros para a VM
Com o script de recuperação copiado para a VM, pode agora ligar o ponto de recuperação e restaurar ficheiros.

1. Ligar à VM com o SSH. Substitua *publicIpAddress* com o endereço IP público da sua VM da seguinte forma:

    ```bash
    ssh publicIpAddress
    ```

2. Para permitir que o script a ser executado corretamente, adicione de executar permissões com **chmod**. Introduza o nome do seu próprio script:

    ```bash
    chmod +x myVM_we_1571974050985163527.sh
    ```

3. Para montar o ponto de recuperação, execute o script. Introduza o nome do seu próprio script:

    ```bash
    ./myVM_we_1571974050985163527.sh
    ```

    Como o script é executado, lhe for pedido que introduza uma palavra-passe para aceder ao ponto de recuperação. Introduza a palavra-passe apresentada no resultado do anterior [rp de montagem de ficheiros de restauro de cópia de segurança az](https://docs.microsoft.com/cli/azure/backup/restore/files?view=azure-cli-latest#az_backup_restore_files_mount_rp) comando que gerou o script de recuperação.

    O resultado do script dá-lhe o caminho para o ponto de recuperação. O seguinte resultado de exemplo mostra que o ponto de recuperação está montado */home/azureuser/myVM-20170919213536/Volume1*:

    ```
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
    Please enter the password as shown on the portal to securely connect to the recovery point. : c068a041ce12465
    
    Connecting to recovery point using ISCSI service...
    
    Connection succeeded!
    
    Please wait while we attach volumes of the recovery point to this machine...
    
    ************ Volumes of the recovery point and their mount paths on this machine ************
    
    Sr.No.  |  Disk  |  Volume  |  MountPath
    
    1)  | /dev/sdc  |  /dev/sdc1  |  /home/azureuser/myVM-20170919213536/Volume1
    
    ************ Open File Explorer to browse for files. ************
    ```

4. Utilize **cp** para copiar o NGINX predefinido de página web no ponto de recuperação montados de volta para a localização do ficheiro original. Substitua o */home/azureuser/myVM-20170919213536/Volume1* ponto com a sua própria localização de montagem:

    ```bash
    sudo cp /home/azureuser/myVM-20170919213536/Volume1/var/www/html/index.nginx-debian.html /var/www/html/
    ```

6. No seu browser, atualize a página web. O web site agora carrega corretamente novamente, conforme mostrado no exemplo seguinte:

    ![Web site do NGINX agora carrega corretamente](./media/tutorial-restore-files/nginx-restored.png)

7. Feche a sessão SSH para a VM da seguinte forma:

    ```bash
    exit
    ```

8. Desmonte o ponto de recuperação da VM com [restaurar cópia de segurança az ficheiros rp desmonte](https://docs.microsoft.com/cli/azure/backup/restore/files?view=azure-cli-latest#az_backup_restore_files_unmount_rp). O exemplo seguinte unmounts o ponto de recuperação da VM com o nome *myVM* no *myRecoveryServicesVault*.

    Substitua *myRecoveryPointName* com o nome do seu ponto de recuperação que obteve no comandos anteriores:
    
    ```azurecli-interactive
    az backup restore files unmount-rp \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --rp-name myRecoveryPointName
    ```

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, ligados um ponto de recuperação para uma VM e restaurar os ficheiros para um servidor web. Aprendeu a:

> [!div class="checklist"]
> * Lista e pontos de recuperação selecione
> * Ligar um ponto de recuperação para uma VM
> * Restaurar ficheiros a partir de um ponto de recuperação

Avançar para o próximo tutorial para saber mais sobre como fazer cópias de segurança do Windows Server para o Azure.

> [!div class="nextstepaction"]
> [Criar cópias de segurança do Windows Server para o Azure](tutorial-backup-windows-server-to-azure.md)

