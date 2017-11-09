---
title: "Cópia de segurança do Azure: Restaurar estado do sistema para um servidor do Windows | Microsoft Docs"
description: "Passo por explicação passo para restaurar o estado do sistema do Windows Server a partir de uma cópia de segurança no Azure."
services: backup
documentationcenter: 
author: saurabhsensharma
manager: shivamg
editor: 
ms.assetid: 
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 8/18/2017
ms.author: saurse;trinadhk;markgal;
ms.openlocfilehash: 971f7979ca27bce5d147a9d479248c808dc658e3
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="restore-system-state-to-windows-server"></a>Restaurar estado do sistema para o Windows Server

Este artigo explica como restaurar cópias de segurança do Estado do sistema do Windows Server a partir de um cofre dos serviços de recuperação do Azure. Para restaurar estado do sistema, tem de ter uma cópia de segurança do Estado do sistema (criada utilizando as instruções em [cópia de segurança do Estado do sistema](backup-azure-system-state.md#back-up-windows-server-system-state)e certifique-se de que instalou o [versão mais recente do Microsoft Azure Recovery Services (MARS) agente](http://aka.ms/azurebackup_agent). Recuperar dados de estado do sistema do Windows Server a partir de um cofre dos serviços de recuperação do Azure é um processo de dois passos:

1. Restaure estado do sistema como ficheiros de cópia de segurança do Azure. Ao restaurar o estado do sistema de ficheiros de cópia de segurança do Azure, pode:
  * Restaurar estado do sistema do mesmo servidor em que foram efetuadas as cópias de segurança, ou
  * Ficheiro de estado do sistema de restauro para um servidor alternativo.

2. Aplicam-se os ficheiros de estado do sistema restaurados para um servidor do Windows.


## <a name="recover-system-state-files-to-the-same-server"></a>Ficheiros de estado do sistema de recuperação para o mesmo servidor
Os passos seguintes explicam como reverter a configuração do Windows Server para um estado anterior. A reverter a configuração do servidor para um estado conhecido, estável, pode ser extremamente valioso. Os seguintes passos restaurar estado do sistema do servidor a partir de um cofre dos serviços de recuperação. 

1. Abra o **cópia de segurança do Microsoft Azure** snap-in. Se não souber qual o snap-in foi instalado, procure o computador ou servidor para **cópia de segurança do Microsoft Azure**.

    A aplicação de ambiente de trabalho deve aparecer nos resultados da pesquisa.

2. Clique em **recuperar dados** para iniciar o assistente.

    ![Recuperar dados](./media/backup-azure-restore-windows-server/recover.png)

3. No **introdução** painel, para restaurar os dados no mesmo servidor ou computador, selecione **neste servidor (`<server name>`)** e clique em **seguinte**.

    ![Escolha esta opção de servidor para restaurar os dados para a mesma máquina](./media/backup-azure-restore-system-state/samemachine.png)

4. No **selecionar modo de recuperação** painel, escolha **estado do sistema** e, em seguida, clique em **seguinte**.

    ![Procurar ficheiros](./media/backup-azure-restore-system-state/recover-type-selection.png)

5. No calendário no **data e selecione o Volume** painel, selecione uma recuperação do ponto. 

    Pode restaurar a partir de qualquer ponto de recuperação no tempo. As datas em **negrito** indicar a disponibilidade de, pelo menos, um ponto de recuperação. Depois de selecionar uma data, se vários pontos de recuperação disponíveis, selecione o ponto de recuperação específico do **tempo** menu pendente.

    ![Data e de volume](./media/backup-azure-restore-system-state/select-date.png)

6. Depois de escolher o ponto de recuperação para restaurar, clique em **seguinte**.

    Cópia de segurança do Azure monta o ponto de recuperação do local e utiliza-o como um volume de recuperação.

7. No painel seguinte, especifique o destino para os ficheiros recuperados do Estado do sistema e clique em **procurar** para abrir o Explorador do Windows e localizar os ficheiros e pastas que pretende. A opção **criar cópias de modo a que ambas as versões**, cria cópias dos ficheiros individuais num arquivo de ficheiros de estado do sistema existente em vez de criar a cópia do arquivo de estado do sistema completo.

    ![Opções de recuperação](./media/backup-azure-restore-system-state/recover-as-files.png)

8. Verifique os detalhes de recuperação no **confirmação** painel e clique em **recuperar**.

   ![Clique em recuperar para confirmar a ação de recuperação](./media/backup-azure-restore-system-state/confirm-recovery.png)

9. Copiar o *WindowsImageBackup* diretório no destino da recuperação para um volume não críticas do servidor. Normalmente, o volume de sistema operativo Windows é o volume crítico.

10. Depois da recuperação for bem sucedida, siga os passos na secção, [aplicar restaurar ficheiros de estado do sistema para o Windows Server](backup-azure-restore-system-state.md#apply-restored-system-state-files-to-the-windows-server), para concluir o processo de recuperação do Estado do sistema.

## <a name="recover-system-state-files-to-an-alternate-server"></a>Ficheiros de estado do sistema de recuperação para um servidor alternativo

Se o servidor do Windows está danificada ou estiver inacessível e que pretende restaurá-lo para um estado estável por recuperar o estado do sistema do Windows Server, pode restaurar o estado do sistema do servidor danificado de outro servidor. Utilize os seguintes passos para o restauro do Estado do sistema num servidor separado.  

Inclui a terminologia utilizada estes passos:

- *Máquina de origem* – a máquina original da qual foi efetuada a cópia de segurança e que não está atualmente disponível.
- *A máquina de destino* – a máquina para que os dados está a ser recuperados.
- *Cofre do exemplo* – dos serviços de recuperação do cofre para os quais o *máquina de origem* e *máquina de destino* estão registados. <br/>

> [!NOTE]
> Não não possível restaurar cópias de segurança criadas a partir de uma máquina a um computador com uma versão anterior do sistema operativo. Por exemplo, as cópias de segurança criadas a partir de uma máquina de Windows Server 2016 não não possível restaurar para o Windows Server 2012 R2. No entanto, o inverso é possível. Pode utilizar cópias de segurança do Windows Server 2012 R2 para restaurar o Windows Server 2016.
>

1. Abra o **cópia de segurança do Microsoft Azure** snap-in no *máquina de destino*.
2. Certifique-se de que o *máquina de destino* e *máquina de origem* estão registados no mesmo Cofre de serviços de recuperação.
3. Clique em **recuperar dados** para iniciar o fluxo de trabalho.

    ![Recuperar dados](./media/backup-azure-restore-windows-server-classic/recover.png)

4. Selecione **outro servidor**

    ![Outro servidor](./media/backup-azure-restore-system-state/anotherserver.png)

5. Disponibilize o ficheiro de credenciais do cofre que corresponde do *Cofre de exemplo*. Se o ficheiro de credenciais do Cofre inválido (ou expirados), transfira um novo ficheiro de credenciais do cofre do *Cofre de exemplo* no portal do Azure. Depois do ficheiro de credenciais do cofre for fornecido, é apresentado o Cofre de serviços de recuperação associado com o ficheiro de credenciais do cofre.

6. No painel selecionar servidor de cópia de segurança, selecione o *máquina de origem* da lista de máquinas apresentadas.

    ![Lista de máquinas](./media/backup-azure-restore-windows-server-classic/machinelist.png)

7. No painel de selecionar o modo de recuperação, selecione **estado do sistema** e clique em **seguinte**. 

    ![Pesquisa](./media/backup-azure-restore-system-state/recover-type-selection.png)

8. No calendário no **data e selecione o Volume** painel, selecione uma recuperação do ponto. Pode restaurar a partir de qualquer ponto de recuperação no tempo. As datas em **negrito** indicar a disponibilidade de, pelo menos, um ponto de recuperação. Depois de selecionar uma data, se vários pontos de recuperação disponíveis, selecione o ponto de recuperação específico do **tempo** menu pendente. 

    ![Itens de procura](./media/backup-azure-restore-system-state/select-date.png)

9. Depois de escolher o ponto de recuperação para restaurar, clique em **seguinte**.

10. No **selecionar modo de recuperação de estado de sistema** painel, especifique o destino onde pretende que os ficheiros de estado do sistema para ser recuperada, em seguida, clique em **seguinte**.

    ![Encriptação](./media/backup-azure-restore-system-state/recover-as-files.png)

    A opção **criar cópias de modo a que ambas as versões**, cria cópias dos ficheiros individuais num arquivo de ficheiros de estado do sistema existente em vez de criar a cópia do arquivo de estado do sistema completo.

11. Verifique os detalhes de recuperação no painel de confirmação e clique em **recuperar**. 

    ![Clique no botão de recuperação para confirmar o processo de recuperação](./media/backup-azure-restore-system-state/confirm-recovery.png)

12. Copiar o *WindowsImageBackup* diretório para um volume não críticas do servidor (por exemplo D:\). Normalmente, o volume de sistema operativo Windows é o volume crítico.

13. Para concluir o processo de recuperação, utilize a secção seguinte para [aplicam-se os ficheiros de estado do sistema restaurados num servidor Windows](backup-azure-restore-system-state.md#apply-restored-system-state-on-a-windows-server).




## <a name="apply-restored-system-state-on-a-windows-server"></a>Aplicar o restauro do Estado do sistema no Windows Server

Uma vez tiver recuperado o estado do sistema de ficheiros utilizando o Azure Recovery Services Agent, utilize o utilitário de cópia de segurança do Windows Server para aplicar o estado do sistema recuperada para o Windows Server. O utilitário de cópia de segurança do Windows Server já se encontra disponível no servidor. Os passos seguintes explicam como aplicar o estado do sistema recuperada.

1. Utilize os seguintes comandos de reiniciar o servidor no *modo de reparação de serviços de diretório*. Na linha de comandos elevada:

    ```
    PS C:\> Bcdedit /set safeboot dsrepair
    PS C:\> Shutdown /r /t 0
    ```

2. Após o reinício, abra o snap-in cópia de segurança do Windows Server. Se não souber qual o snap-in foi instalado, procure o computador ou servidor para **cópia de segurança do Windows Server**.

    A aplicação de ambiente de trabalho é apresentado nos resultados da pesquisa.

3. No snap-in, selecione **cópia de segurança Local**.

    ![Selecione a cópia de segurança Local para restaurar a partir daí](./media/backup-azure-restore-system-state/win-server-backup-local-backup.png)

4. Na consola de cópia de segurança Local, no **painel ações**, clique em **recuperar** para abrir o Assistente de recuperação.

5. Selecione a opção **uma cópia de segurança armazenada noutra localização**e clique em **seguinte**.

   ![optar por recuperar para um servidor diferente](./media/backup-azure-restore-system-state/backup-stored-in-diff-location.png)

6. Quando especificar o tipo de localização, selecione **remoto pasta partilhada** se a cópia de segurança do Estado do sistema foi recuperada para outro servidor. Se o estado do sistema foi recuperado localmente, em seguida, selecione **unidades locais**. 

    ![Selecione se a recuperação do servidor local ou outro](./media/backup-azure-restore-system-state/ss-recovery-remote-shared-folder.png)

7. Introduza o caminho para o *WindowsImageBackup* diretório, ou selecione a unidade local que contém este diretório (por exemplo, D:\WindowsImageBackup) recuperado como parte da recuperação de ficheiros de estado do sistema utilizando o Azure Recovery Services Agent e clique em **seguinte**.

    ![caminho para o ficheiro partilhado](./media/backup-azure-restore-system-state/ss-recovery-remote-folder.png)

8. Selecione a versão de estado do sistema que pretende restaurar e clique em **seguinte**.

9. No painel selecionar tipo de recuperação, selecione **estado do sistema** e clique em **seguinte**.

10. Para a localização da recuperação de estado do sistema, selecione **localização Original**e clique em **seguinte**.

11. Reveja os detalhes de confirmação, verifique as definições de reinício e clique em **recuperar** para applly o estado do sistema restaurada ficheiros.

    ![iniciar o restauro de ficheiros de estado do sistema](./media/backup-azure-restore-system-state/launch-ss-recovery.png)

## <a name="special-considerations-for-system-state-recovery-on-active-directory-server"></a>Considerações especiais sobre a recuperação de estado do sistema no servidor do Active Directory

Cópia de segurança do Estado do sistema inclui dados do Active Directory. Utilize os seguintes passos para restaurar os serviços de domínio do Active Directory (AD DS) do seu estado atual para um estado anterior.

1. Reinicie o controlador de domínio no serviços restaurar modo diretório (DSRM).
2. Siga os passos [aqui](https://technet.microsoft.com/en-us/library/cc794755(v=ws.10).aspx) utilizar cmdlets de cópia de segurança do Windows Server para recuperar o AD DS.


## <a name="troubleshoot-failed-system-state-restore"></a>Resolver problemas de restauro de estado do sistema com falhas

Se o processo anterior de aplicar o estado do sistema não for concluída com êxito, utilize o ambiente de recuperação do Windows (Win RE) para recuperar o servidor do Windows. Os passos seguintes explicam como recuperar utilizando Win RE. Utilize esta opção apenas se o Windows Server não se inicie normalmente após um restauro do Estado do sistema. O seguinte processo apague dados não pertencente ao sistema, tenha cuidado. 

1. Arranque o servidor do Windows no ambiente de recuperação do Windows (Win RE).

2. Selecione as opções disponíveis três resolução de problemas.

    ![menu abrir](./media/backup-azure-restore-system-state/winre-1.png)

3. Do **opções avançadas** ecrã, selecione **linha de comandos** e forneça o nome de utilizador de administrador de servidor e a palavra-passe.

   ![menu abrir](./media/backup-azure-restore-system-state/winre-2.png)

4. Forneça o nome de utilizador de administrador de servidor e a palavra-passe.

    ![menu abrir](./media/backup-azure-restore-system-state/winre-3.png)

5. Quando abrir a linha de comandos no modo de administrador, execute o seguinte comando para obter as versões de cópia de segurança do Estado do sistema.

    ```
    Wbadmin get versions -backuptarget:<Volume where WindowsImageBackup folder is copied>:
    ```
    ![obter versões de cópia de segurança do Estado do sistema](./media/backup-azure-restore-system-state/winre-4.png)

6. Execute o seguinte comando para obter todos os volumes disponíveis na cópia de segurança.

    ```
    Wbadmin get items -version:<copy version from above step> -backuptarget:<Backup volume>
    ```

    ![obter versões de cópia de segurança do Estado do sistema](./media/backup-azure-restore-system-state/winre-5.png)

7. O seguinte comando recupera todos os volumes que fazem parte da cópia de segurança de estado do sistema. Tenha em atenção que este passo recupera apenas os volumes críticos que fazem parte do Estado do sistema. Todos os dados de sistema não é eliminada.

    ```
    Wbadmin start recovery -items:C: -itemtype:Volume -version:<Backupversion> -backuptarget:<backup target volume>
    ```
     ![obter versões de cópia de segurança do Estado do sistema](./media/backup-azure-restore-system-state/winre-6.png)



## <a name="next-steps"></a>Passos seguintes
* Agora que recuperar os seus ficheiros e pastas, pode [gerir as cópias de segurança](backup-azure-manage-windows-server.md).
