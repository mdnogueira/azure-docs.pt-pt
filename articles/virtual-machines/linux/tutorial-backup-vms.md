---
title: "Cópia de segurança de VMs com Linux do Azure | Microsoft Docs"
description: "Proteger as VMs com Linux através de cópias de segurança utilizando a cópia de segurança do Azure."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 0e659c1906c99415ab1b53785a606330ef9068c8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="back-up-linux--virtual-machines-in-azure"></a>Cópia de segurança de computadores virtuais Linux no Azure

Pode criar cópias de segurança em intervalos regulares para manter os seus dados protegidos. Cópia de segurança do Azure cria pontos de recuperação que estão armazenados no cofres de recuperação com redundância geográfica. Quando restaurar a partir de um ponto de recuperação, pode restaurar VM todo ou ficheiros apenas específicos. Este artigo explica como restaurar um ficheiro único para uma VM com Linux em execução nginx. Se ainda não tiver uma VM a utilizar, pode criar um utilizando o [início rápido do Linux](quick-create-cli.md). Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma cópia de segurança de uma VM
> * Agendar uma cópia de segurança diária
> * Restaurar um ficheiro a partir de uma cópia de segurança



## <a name="backup-overview"></a>Descrição geral da Cópia de Segurança

Quando o serviço de cópia de segurança do Azure inicia uma cópia de segurança, aciona a extensão de cópia de segurança para criar um instantâneo de ponto no tempo. As utilizações do serviço de cópia de segurança do Azure a _VMSnapshotLinux_ extensão no Linux. A extensão é instalada durante a primeira cópia de segurança VM se a VM está em execução. Se a VM não está em execução, o serviço de cópia de segurança tira um instantâneo de armazenamento subjacente (uma vez que não existem escritas de aplicação ocorrem enquanto a VM está parada).

Por predefinição, cópia de segurança do Azure assume uma sistema consistente cópia de segurança para a VM com Linux, mas pode ser configurado para tirar [aplicação cópia de segurança consistente utilizando o pré- script de e script pós-implementação framework](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent). Assim que o serviço de cópia de segurança do Azure assume o instantâneo, os dados são transferidos para o cofre. Para maximizar a eficiência, o serviço identifica, transfere apenas os blocos de dados que foram alterados desde a cópia de segurança anterior.

Quando a transferência de dados estiver concluída, o instantâneo é removido e é criado um ponto de recuperação.


## <a name="create-a-backup"></a>Criar uma cópia de segurança
Crie uma cópia de segurança diária simples para um Cofre dos Serviços de Recuperação. 

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. No menu do lado esquerdo, selecione **Máquinas virtuais**. 
3. Na lista, selecione uma VM da qual pretende criar uma cópia de segurança.
4. No painel de VM, no **definições** secção, clique em **cópia de segurança**. O **ativar cópia de segurança** abre o painel.
5. No **cofre dos serviços de recuperação**, clique em **criar nova** e forneça o nome para o novo cofre. Um novo cofre é criado no mesmo grupo de recursos e localização que a máquina virtual.
6. Clique em **política de cópia de segurança**. Neste exemplo, mantenha as predefinições e clique em **OK**.
7. No **ativar cópia de segurança** painel, clique em **ativar a cópia de segurança**. Esta ação cria uma cópia de segurança diária, com base na agenda predefinida.
10. Para criar um ponto de recuperação inicial, o **cópia de segurança** painel clique **cópia de segurança agora**.
11. No **cópia de segurança agora** painel, clique no ícone de calendário, utilize o controlo de calendário para selecionar o último dia deste ponto de recuperação é mantido e clique em **cópia de segurança**.
12. No **cópia de segurança** painel para a VM, verá que o número de pontos de recuperação que estão concluídos.

    ![Pontos de recuperação](./media/tutorial-backup-vms/backup-complete.png)

A primeira cópia de segurança demora cerca de 20 minutos. Avance para a parte seguinte do tutorial depois de concluída a cópia de segurança.

## <a name="restore-a-file"></a>Restaurar um ficheiro

Se eliminar acidentalmente ou efetuar alterações a um ficheiro, pode utilizar a recuperação de ficheiros para recuperar o ficheiro do seu Cofre de cópia de segurança. Recuperação de ficheiros utiliza um script que é executado na VM, para o ponto de recuperação como unidade local de montagem. Estas unidades permanecerá montadas para 12 horas para que possa copiar os ficheiros do ponto de recuperação e restaurá-las para a VM.  

Neste exemplo, mostramos como recuperar a predefinição nginx página web /var/www/html/index.nginx-debian.html. O endereço IP público do nosso VM neste exemplo é *13.69.75.209*. Pode encontrar o endereço IP da vm utilizando:

 ```bash 
 az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
 ```

 
1. No seu computador local, abra um browser e escreva o endereço IP público da sua VM para ver a página predefinida de web nginx.

    ![Nginx predefinido de página web](./media/tutorial-backup-vms/nginx-working.png)

1. SSH para a VM.

    ```bash
    ssh 13.69.75.209
    ```
2. Elimine /var/www/html/index.nginx-debian.html.

    ```bash
    sudo rm /var/www/html/index.nginx-debian.html
    ```
    
4. No computador local, atualize o browser por atingir CTRL + F5 para ver que essa página predefinida de nginx é removida.

    ![Nginx predefinido de página web](./media/tutorial-backup-vms/nginx-broken.png)
    
1. No seu computador local, inicie sessão para o [portal do Azure](https://portal.azure.com/).
6. No menu do lado esquerdo, selecione **Máquinas virtuais**. 
7. Na lista, selecione a VM.
8. No painel de VM, no **definições** secção, clique em **cópia de segurança**. O **cópia de segurança** abre o painel. 
9. No menu na parte superior do painel, selecione **recuperação de ficheiros**. O **recuperação de ficheiros** abre o painel.
10. No **passo 1: selecione o ponto de recuperação**, selecione um ponto de recuperação da lista pendente.
11. No **passo 2: Transferir o script para procurar e recuperar ficheiros**, clique em de **transferir executável** botão. Guarde o ficheiro transferido no computador local.
7. Clique em **transferir o script** para transferir o ficheiro de script localmente.
8. Abra uma linha de Bash e escreva o seguinte, substituindo *Linux_myVM_05-05-2017.sh* com o caminho correto e o nome de ficheiro de script que transferiu, *azureuser* com o nome de utilizador para a VM e *13.69.75.209* com o endereço IP público para a VM.
    
    ```bash
    scp Linux_myVM_05-05-2017.sh azureuser@13.69.75.209:
    ```
    
9. No seu computador local, abra uma ligação SSH para a VM.

    ```bash
    ssh 13.69.75.209
    ```
    
10. Na sua VM, adicionar permissões para o ficheiro de script de execução.

    ```bash
    chmod +x Linux_myVM_05-05-2017.sh
    ```
    
11. Na sua VM, execute o script para o ponto de recuperação como um sistema de ficheiros de montagem.

    ```bash
    ./Linux_myVM_05-05-2017.sh
    ```
    
12. O resultado do script dá-lhe o caminho para o ponto de montagem. O resultado semelhante a isto:

    ```bash
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
                          
    Connecting to recovery point using ISCSI service...
    
    Connection succeeded!
    
    Please wait while we attach volumes of the recovery point to this machine...
                         
    ************ Volumes of the recovery point and their mount paths on this machine ************

    Sr.No.  |  Disk  |  Volume  |  MountPath 

    1)  | /dev/sdc  |  /dev/sdc1  |  /home/azureuser/myVM-20170505191055/Volume1

    ************ Open File Explorer to browse for files. ************

    After recovery, to remove the disks and close the connection to the recovery point, please click 'Unmount Disks' in step 3 of the portal.

    Please enter 'q/Q' to exit...
    ```

12. Na sua VM, copie a página de web nginx predefinida do ponto de montagem para onde tenha eliminado o ficheiro.

    ```bash
    sudo cp ~/myVM-20170505191055/Volume1/var/www/html/index.nginx-debian.html /var/www/html/
    ```
    
17. No computador local, abra o separador do browser onde estão ligados ao endereço IP da VM que mostra a página predefinida de nginx. Prima CTRL + F5 para atualizar a página de browser. Deverá ver a página predefinida está a funcionar novamente.

    ![Nginx predefinido de página web](./media/tutorial-backup-vms/nginx-working.png)

18. No seu computador local, regressar ao separador do browser para o portal do Azure e no **passo 3: desmontar os discos após a recuperação** clique o **desmonte discos** botão. Caso se esqueça efetuar este passo, a ligação para a pontodemontagem é fechar automaticamente após 12 horas. Depois dessas 12 horas, terá de transferir um script novo para criar uma novo pontodemontagem.


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar uma cópia de segurança de uma VM
> * Agendar uma cópia de segurança diária
> * Restaurar um ficheiro a partir de uma cópia de segurança

Avançar para o próximo tutorial para saber mais sobre a monitorização de máquinas virtuais.

> [!div class="nextstepaction"]
> [Monitorizar máquinas virtuais](tutorial-monitoring.md)

