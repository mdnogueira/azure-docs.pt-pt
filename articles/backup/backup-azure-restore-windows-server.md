---
title: Restaurar dados no Azure para um Windows Server ou o computador com o Windows | Microsoft Docs
description: Saiba como restaurar os dados armazenados no Azure para um Windows Server ou o computador Windows.
services: backup
documentationcenter: 
author: saurabhsensharma
manager: shivamg
editor: 
ms.assetid: 742f4b9e-c0ab-4eeb-8e22-ee29b83c22c4
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 8/16/2017
ms.author: saurse;trinadhk;markgal;
ms.openlocfilehash: 231dd61f95267b3a504ed70e9b3a5abc470b69b2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="restore-files-to-a-windows-server-or-windows-client-machine-using-resource-manager-deployment-model"></a>Restaurar ficheiros para um servidor Windows ou para um computador cliente Windows com o modelo de implementação Resource Manager
> [!div class="op_single_selector"]
> * [Portal do Azure](backup-azure-restore-windows-server.md)
> * [Portal clássico](backup-azure-restore-windows-server-classic.md)
>
>

Este artigo explica como restaurar dados a partir de um cofre de cópia de segurança. Para restaurar dados, pode utilizar o Assistente de recuperar dados do agente do serviços de recuperação do Azure (MARS) da Microsoft. Quando restaurar dados, é possível:

* Restaure dados para a mesma máquina a partir da qual foram efetuadas as cópias de segurança.
* Restaure dados para uma máquina alternativa.

Em Janeiro de 2017, a Microsoft lançou uma atualização de pré-visualização para o agente MARS. Juntamente com correções de erros, esta atualização permite restaurar instantâneas, permitindo a montar um instantâneo de ponto de recuperação gravável como um volume de recuperação. Em seguida, pode explorar os ficheiros de volume e cópia de recuperação para um computador local, deste modo, seletivamente restaurar os ficheiros.

> [!NOTE]
> O [Janeiro de 2017 atualização de cópia de segurança do Azure](https://support.microsoft.com/en-us/help/3216528?preview) é necessário se pretender utilizar o restauro instantâneas para restaurar dados. Também os dados de cópia de segurança devem ser protegidos no cofres em regiões listados no artigo de suporte. Consulte o [Janeiro de 2017 atualização de cópia de segurança do Azure](https://support.microsoft.com/en-us/help/3216528?preview) para a lista de regiões que suportam o restauro instantâneas mais recente. Restauro instantâneas **não** atualmente disponível em todas as regiões.
>

Restauro instantânea está disponível para utilização em cofres dos serviços de recuperação no portal do Azure e cofres de cópia de segurança no portal clássico. Se pretender utilizar a restaurar instantâneas, transferir a atualização de MARS e siga os procedimentos que mencionem restaurar instantânea.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="use-instant-restore-to-recover-data-to-the-same-machine"></a>Utilize Restore instantâneas para recuperar dados para a mesma máquina

Se acidentalmente eliminado um ficheiro e pretende restaurá-lo para a mesma máquina (partir da qual a cópia de segurança foi efetuada), os passos seguintes irão ajudá-lo recuperar os dados.

1. Abra o **cópia de segurança do Microsoft Azure** ajuste no. Se não souber onde o snap foi instalado, procure o computador ou servidor para **cópia de segurança do Microsoft Azure**.

    A aplicação de ambiente de trabalho deve aparecer nos resultados da pesquisa.

2. Clique em **recuperar dados** para iniciar o assistente.

    ![Recuperar dados](./media/backup-azure-restore-windows-server/recover.png)

3. No **introdução** painel, para restaurar os dados no mesmo servidor ou computador, selecione **neste servidor (`<server name>`)** e clique em **seguinte**.

    ![Escolha esta opção de servidor para restaurar os dados para a mesma máquina](./media/backup-azure-restore-windows-server/samemachine_gettingstarted_instantrestore.png)

4. No **selecionar modo de recuperação** painel, escolha **ficheiros e pastas individuais** e, em seguida, clique em **seguinte**.

    ![Procurar ficheiros](./media/backup-azure-restore-windows-server/samemachine_selectrecoverymode_instantrestore.png)

5. No **data e selecione o Volume** painel, selecione o volume que contém os ficheiros de e/ou pastas que pretende restaurar.

    No calendário, selecione um ponto de recuperação. Pode restaurar a partir de qualquer ponto de recuperação no tempo. As datas em **negrito** indicar a disponibilidade de, pelo menos, um ponto de recuperação. Depois de selecionar uma data, se vários pontos de recuperação disponíveis, selecione o ponto de recuperação específico do **tempo** menu pendente.

    ![Data e de volume](./media/backup-azure-restore-windows-server/samemachine_selectvolumedate_instantrestore.png)

6. Depois de escolher o ponto de recuperação para restaurar, clique em **montar**.

    Cópia de segurança do Azure monta o ponto de recuperação do local e utiliza-o como um volume de recuperação.

7. No **procurar e recuperar ficheiros** painel, clique em **procurar** para abrir o Explorador do Windows e localizar os ficheiros e pastas que pretende.

    ![Opções de recuperação](./media/backup-azure-restore-windows-server/samemachine_browserecover_instantrestore.png)


8. No Explorador do Windows, copie os ficheiros de e/ou pastas que pretende restaurar e cole-os para qualquer localização local para o servidor ou computador. Pode abrir ou transmitir os ficheiros diretamente a partir do volume de recuperação e certifique-se de que as versões corretas são recuperadas.

    ![Copie e cole os ficheiros e pastas do volume montado para localização local](./media/backup-azure-restore-windows-server/samemachine_copy_instantrestore.png)

9. Quando tiver terminado de restauro de ficheiros e pastas, no **procurar e ficheiros de recuperação** painel, clique em **Unmount**. Em seguida, clique em **Sim** para confirmar que pretende que o desmontar o volume.

    ![Desmontar o volume e confirme](./media/backup-azure-restore-windows-server/samemachine_unmount_instantrestore.png)

    > [!Important]
    > Se não clicar Unmount, o Volume de recuperação irá permanecer montado para 6 horas desde o momento quando foi montado. No entanto, o tempo de montagem é expandido até um máximo de 24 horas em caso de uma cópia de ficheiros em curso. Não existem operações de cópia de segurança serão executado enquanto o volume está montado. Todas as operações de cópia de segurança agendadas para execução durante a hora quando o volume está montado, será executado após a desmontados o volume de recuperação.
    >


## <a name="use-instant-restore-to-restore-data-to-an-alternate-machine"></a>Utilize a restaurar instantâneas para restaurar dados para uma máquina alternativa
Se o servidor completo se tenha perdido, pode continuar a recuperar dados de cópia de segurança do Azure para um computador diferente. Os passos seguintes mostram o fluxo de trabalho.


Inclui a terminologia utilizada estes passos:

* *Máquina de origem* – a máquina original da qual foi efetuada a cópia de segurança e que não está atualmente disponível.
* *A máquina de destino* – a máquina para que os dados está a ser recuperados.
* *Cofre do exemplo* – dos serviços de recuperação do cofre para os quais o *máquina de origem* e *máquina de destino* estão registados. <br/>

> [!NOTE]
> Não não possível restaurar cópias de segurança a um computador de destino com uma versão anterior do sistema operativo. Por exemplo, uma restaurar cópia de segurança obtida a partir de um computador pode ser do Windows 7 no Windows 8 ou posterior, computador. Não é possível restaurar uma cópia de segurança obtida a partir de um computador Windows 8 num computador Windows 7.
>
>

1. Abra o **cópia de segurança do Microsoft Azure** ajuste no *máquina de destino*.

2. Certifique-se a *máquina de destino* e *máquina de origem* estão registados no mesmo Cofre de serviços de recuperação.

3. Clique em **recuperar dados** para abrir o **assistente recuperar dados**.

    ![Recuperar dados](./media/backup-azure-restore-windows-server/recover.png)

4. No **introdução** painel, selecione **outro servidor**

    ![Outro servidor](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

5. Disponibilize o ficheiro de credenciais do cofre que corresponde do *Cofre de exemplo*e clique em **seguinte**.

    Se o ficheiro de credenciais do Cofre inválido (ou expirados), transfira um novo ficheiro de credenciais do cofre do *Cofre de exemplo* no portal do Azure. Depois de fornecer uma credencial de cofre válida, é apresentado o nome do Cofre de cópia de segurança correspondente.


6. No **selecionar servidor de cópia de segurança** painel, selecione o *máquina de origem* da lista de máquinas apresentadas e forneça o frase de acesso. Clique depois em **Seguinte**.

    ![Lista de máquinas](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

7. No **selecionar modo de recuperação** painel, selecione **ficheiros e pastas individuais** e clique em **seguinte**.

    ![Pesquisa](./media/backup-azure-restore-windows-server/alternatemachine_selectrecoverymode_instantrestore.png)

8. No **data e selecione o Volume** painel, selecione o volume que contém os ficheiros de e/ou pastas que pretende restaurar.

    No calendário, selecione um ponto de recuperação. Pode restaurar a partir de qualquer ponto de recuperação no tempo. As datas em **negrito** indicar a disponibilidade de, pelo menos, um ponto de recuperação. Depois de selecionar uma data, se vários pontos de recuperação disponíveis, selecione o ponto de recuperação específico do **tempo** menu pendente.

    ![Itens de procura](./media/backup-azure-restore-windows-server/alternatemachine_selectvolumedate_instantrestore.png)

9. Clique em **montar** localmente montar o ponto de recuperação como um volume de recuperação no seu *máquina de destino*.

10. No **procurar e recuperar ficheiros** painel, clique em **procurar** para abrir o Explorador do Windows e localizar os ficheiros e pastas que pretende.

    ![Encriptação](./media/backup-azure-restore-windows-server/alternatemachine_browserecover_instantrestore.png)

11. No Explorador do Windows, copie os ficheiros de e/ou pastas do volume de recuperação e cole-os para o *máquina de destino* localização. Pode abrir ou transmitir os ficheiros diretamente a partir do volume de recuperação e certifique-se de que as versões corretas são recuperadas.

    ![Encriptação](./media/backup-azure-restore-windows-server/alternatemachine_copy_instantrestore.png)

12. Quando tiver terminado de restauro de ficheiros e pastas, no **procurar e ficheiros de recuperação** painel, clique em **Unmount**. Em seguida, clique em **Sim** para confirmar que pretende que o desmontar o volume.

    ![Encriptação](./media/backup-azure-restore-windows-server/alternatemachine_unmount_instantrestore.png)

    > [!Important]
    > Se não clicar Unmount, o Volume de recuperação irá permanecer montado para 6 horas desde o momento quando foi montado. No entanto, o tempo de montagem é expandido até um máximo de 24 horas em caso de uma cópia de ficheiros em curso. Não existem operações de cópia de segurança serão executado enquanto o volume está montado. Todas as operações de cópia de segurança agendadas para execução durante a hora quando o volume está montado, será executado após a desmontados o volume de recuperação.
    >

## <a name="troubleshooting"></a>Resolução de problemas
Se a cópia de segurança do Azure não com êxito montar o volume de recuperação, mesmo após alguns minutos de clicar em **montar** ou falha ao montar o volume de recuperação com um ou mais erros, siga os passos abaixo para começar a recuperar normalmente.

1.  Cancele o processo de montagem em curso no caso de tem estado em execução durante vários minutos.

2.  Certifique-se de que está na versão mais recente do agente do Backup do Azure. Para obter as informações de versão do agente do Backup do Azure, clique em **sobre o Microsoft Azure Recovery Services Agent** no **ações** painel da cópia de segurança do Microsoft Azure da consola e certifique-se de que o **Versão** número é igual ou superior à versão do mencionados [neste artigo](https://go.microsoft.com/fwlink/?linkid=229525). Pode transferir a versão mais recente do [aqui](https://go.microsoft.com/fwLink/?LinkID=288905)

3.  Aceda a **Gestor de dispositivos** -> **controladores de armazenamento** e certifique-se de que consegue localizar **Iniciador do Microsoft iSCSI**. Se podem localizá-lo, aceda diretamente para o passo 7 abaixo. 

4.  Se não é possível localizar o serviço Iniciador do Microsoft iSCSI, tal como mencionado no passo 3, verifique se pode encontrar uma entrada em **Gestor de dispositivos** -> **controladores de armazenamento** chamado  **Dispositivo desconhecido** com o ID de Hardware **ROOT\ISCSIPRT**.

5.  Clique em **dispositivo desconhecido** e selecione **atualização controlador Software**.

6.  Atualizar o controlador, selecionando a opção para **procurar automaticamente o software de controladores atualizados**. Conclusão da atualização deve ser alterado **dispositivo desconhecido** para **Iniciador do Microsoft iSCSI** conforme mostrado abaixo. 

    ![Encriptação](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7.  Aceda a **Gestor de tarefas** -> **serviços (Local)** -> **o serviço Iniciador do Microsoft iSCSI**. 

    ![Encriptação](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)
    
8.  Reinicie o serviço Iniciador do Microsoft iSCSI clicando com o serviço, clicando no **parar** e ainda mais o botão direito do rato clicar novamente e clicar no **iniciar**.

9.  Repita a recuperação utilizando a restaurar instantânea. 

Se a recuperação continuar a falhar, reinicie o servidor/cliente. Se não é necessário um reinício ou a recuperação continuar a falhar, mesmo após o reinício do servidor, tente recuperar a partir de uma máquina alternativo e contacte o suporte do Azure acedendo a [Portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) e submeter um pedido de suporte.

## <a name="next-steps"></a>Passos seguintes
* Agora que recuperar os seus ficheiros e pastas, pode [gerir as cópias de segurança](backup-azure-manage-windows-server.md).
