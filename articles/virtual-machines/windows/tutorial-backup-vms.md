---
title: "Cópia de segurança de VMs do Azure Windows | Microsoft Docs"
description: "Proteger as VMs do Windows através de cópias de segurança utilizando a cópia de segurança do Azure."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 8e58a2290e5034ef393f65cbcddb86e18cf4a6ec
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="back-up-windows-virtual-machines-in-azure"></a>Cópia de segurança de máquinas virtuais do Windows no Azure

Pode criar cópias de segurança em intervalos regulares para manter os seus dados protegidos. Cópia de segurança do Azure cria pontos de recuperação que estão armazenados no cofres de recuperação com redundância geográfica. Quando restaurar a partir de um ponto de recuperação, pode restaurar VM todo ou ficheiros apenas específicos. Este artigo explica como restaurar um ficheiro único para uma VM com o Windows Server e o IIS. Se ainda não tiver uma VM a utilizar, pode criar um utilizando o [início rápido do Windows](quick-create-portal.md). Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma cópia de segurança de uma VM
> * Agendar uma cópia de segurança diária
> * Restaurar um ficheiro a partir de uma cópia de segurança




## <a name="backup-overview"></a>Descrição geral da Cópia de Segurança

Quando o serviço de cópia de segurança do Azure inicia uma tarefa de cópia de segurança, aciona a extensão de cópia de segurança para criar um instantâneo de ponto no tempo. As utilizações do serviço de cópia de segurança do Azure a _VMSnapshot_ extensão. A extensão é instalada durante a primeira cópia de segurança VM se a VM está em execução. Se a VM não está em execução, o serviço de cópia de segurança tira um instantâneo de armazenamento subjacente (uma vez que não existem escritas de aplicação ocorrem enquanto a VM está parada).

Quando um instantâneo de VMs do Windows, o serviço de cópia de segurança coordena com o serviço do Volume de cópia de sombra de volumes (VSS) para obter um instantâneo consistente dos discos da máquina virtual. Assim que o serviço de cópia de segurança do Azure assume o instantâneo, os dados são transferidos para o cofre. Para maximizar a eficiência, o serviço identifica, transfere apenas os blocos de dados que foram alterados desde a cópia de segurança anterior.

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

## <a name="recover-a-file"></a>Recuperar um ficheiro

Se eliminar acidentalmente ou efetuar alterações a um ficheiro, pode utilizar a recuperação de ficheiros para recuperar o ficheiro do seu Cofre de cópia de segurança. Recuperação de ficheiros utiliza um script que é executado na VM, para o ponto de recuperação como unidade local de montagem. Estas unidades permanecerá montadas para 12 horas para que possa copiar os ficheiros do ponto de recuperação e restaurá-las para a VM.  

Neste exemplo, mostramos como recuperar o ficheiro de imagem que é utilizado na página web predefinido do IIS. 

1. Abra um browser e ligar para o endereço IP da VM para mostrar a página predefinida de IIS.

    ![IIS predefinido de página web](./media/tutorial-backup-vms/iis-working.png)

2. Ligar à VM.
3. Na VM, abra **Explorador de ficheiros** e navegue para \inetpub\wwwroot e elimine o ficheiro **iisstart.png**.
4. No computador local, atualize o browser para ver que a imagem na página IIS predefinido é removida.

    ![IIS predefinido de página web](./media/tutorial-backup-vms/iis-broken.png)

5. No computador local, abra um novo separador e aceda ao [portal do Azure](https://portal.azure.com).
6. No menu à esquerda, selecione **máquinas virtuais** e selecione a forma VM na lista.
8. No painel de VM, no **definições** secção, clique em **cópia de segurança**. O **cópia de segurança** abre o painel. 
9. No menu na parte superior do painel, selecione **recuperação de ficheiros**. O **recuperação de ficheiros** abre o painel.
10. No **passo 1: selecione o ponto de recuperação**, selecione um ponto de recuperação da lista pendente.
11. No **passo 2: Transferir o script para procurar e recuperar ficheiros**, clique em de **transferir executável** botão. Guarde o ficheiro para o **transfere** pasta.
12. No seu computador local, abra **Explorador de ficheiros** e navegue até à sua **transfere** pasta e copie o ficheiro de .exe transferido. O nome de ficheiro será adicionado como prefixo pelo seu nome VM. 
13. Na sua VM (através da ligação de RDP) cole o ficheiro de .exe no ambiente de trabalho da sua VM. 
14. Navegue para o ambiente de trabalho da sua VM e faça duplo clique no .exe. Este procedimento irá iniciar uma linha de comandos e, em seguida, Monte o ponto de recuperação como uma partilha de ficheiros que pode aceder. Quando terminar a criar a partilha, escreva **q** para fechar a linha de comandos.
15. Na sua VM, abra **Explorador de ficheiros** e navegue para a letra de unidade que foi utilizada para a partilha de ficheiros.
16. Navegue para \inetpub\wwwroot e copie **iisstart.png** do ficheiro partilhar e cole-o \inetpub\wwwroot. Por exemplo, copie F:\inetpub\wwwroot\iisstart.png e cole-o c:\inetpub\wwwroot para recuperar o ficheiro.
17. No computador local, abra o separador do browser onde estão ligados ao endereço IP da VM que mostra a página predefinida do IIS. Prima CTRL + F5 para atualizar a página de browser. Deverá ver a imagem foi restaurada.
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









