---
title: "Restaurar dados a um servidor do Windows ou cliente Windows a partir do Azure utilizando o modelo de implementação clássica | Microsoft Docs"
description: Saiba como restaurar a partir de um servidor do Windows ou cliente Windows.
services: backup
documentationcenter: 
author: saurabhsensharma
manager: shivamg
editor: 
ms.assetid: 85585dfc-c764-4e8c-8f0e-40b969640ac2
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: saurse;trinadhk;markgal;
ms.openlocfilehash: 300b2b17b44e21ed446fd63d572a2461e2fc1343
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="restore-files-to-a-windows-server-or-windows-client-machine-using-the-classic-deployment-model"></a>Restaurar ficheiros para um servidor Windows ou para um computador cliente Windows com o modelo de implementação clássica
> [!div class="op_single_selector"]
> * [Portal clássico](backup-azure-restore-windows-server-classic.md)
> * [Portal do Azure](backup-azure-restore-windows-server.md)
>
>

Este artigo explica como recuperar dados a partir de um cofre de cópia de segurança e restaurá-lo para um servidor ou computador. A partir de Março de 2017, já não pode criar cofres de cópia de segurança no portal clássico.

> [!IMPORTANT]
> Agora pode atualizar os cofres de Cópia de Segurança para cofres dos Serviços de Recuperação. Para obter detalhes, veja o artigo [Atualizar um cofre de Cópia de Segurança para um cofre dos Serviços de Recuperação](backup-azure-upgrade-backup-to-recovery-services.md). Recomendamos que atualize os cofres de Cópia de Segurança para cofres dos Serviços de Recuperação.<br/> **15 de outubro de 2017**, não será possível continuar a utilizar o PowerShell para criar cofres de Cópia de segurança. <br/> **A partir de 1 de novembro de 2017**:
>- Quaisquer cofres de Cópia e Segurança restantes serão atualizados automaticamente para cofres dos Serviços de Recuperação.
>- Não vai conseguir aceder aos dados de cópia de segurança no portal clássico. Em vez disso, utilize o portal do Azure para aceder aos dados de cópia de segurança em cofres dos Serviços de Recuperação.
>

Para restaurar dados, pode utilizar o Assistente de recuperar dados do agente do serviços de recuperação do Azure (MARS) da Microsoft. Quando restaurar dados, é possível:

* Restaure dados para a mesma máquina a partir da qual foram efetuadas as cópias de segurança.
* Restaure dados para uma máquina alternativa.

Em Janeiro de 2017, a Microsoft lançou uma atualização de pré-visualização para o agente MARS. Juntamente com correções de erros, esta atualização permite restaurar instantâneas, permitindo a montar um instantâneo de ponto de recuperação gravável como um volume de recuperação. Em seguida, pode explorar os ficheiros de volume e cópia de recuperação para um computador local, deste modo, seletivamente restaurar os ficheiros.

> [!NOTE]
> O [Janeiro de 2017 atualização de cópia de segurança do Azure](https://support.microsoft.com/en-us/help/3216528?preview) é necessário se pretender utilizar o restauro instantâneas para restaurar dados. Também os dados de cópia de segurança devem ser protegidos no cofres em regiões listados no artigo de suporte. Consulte o [Janeiro de 2017 atualização de cópia de segurança do Azure](https://support.microsoft.com/en-us/help/3216528?preview) para a lista de regiões que suportam o restauro instantâneas mais recente. Restauro instantâneas **não** atualmente disponível em todas as regiões.
>

Restauro instantânea está disponível para utilização em cofres dos serviços de recuperação no portal do Azure e cofres de cópia de segurança no portal clássico. Se pretender utilizar a restaurar instantâneas, transferir a atualização de MARS e siga os procedimentos que mencionem restaurar instantânea.


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
    > Se não clicar Unmount, o Volume de recuperação irá permanecer montado durante seis horas desde o momento quando foi montado. Não existem operações de cópia de segurança serão executado enquanto o volume está montado. Todas as operações de cópia de segurança agendadas para execução durante a hora quando o volume está montado, será executado após a desmontados o volume de recuperação.
    >


## <a name="recover-data-to-the-same-machine"></a>Recuperar dados para a mesma máquina
Se acidentalmente eliminado um ficheiro e pretende restaurá-lo para a mesma máquina (partir da qual a cópia de segurança foi efetuada), os passos seguintes irão ajudá-lo recuperar os dados.

1. Abra o **cópia de segurança do Microsoft Azure** ajuste no.
2. Clique em **recuperar dados** para iniciar o fluxo de trabalho.

    ![Recuperar dados](./media/backup-azure-restore-windows-server-classic/recover.png)
3. Selecione o  **neste servidor (*yourmachinename*) * * opção para restaurar a cópia de segurança de ficheiros na mesma máquina.

    ![Mesmo computador](./media/backup-azure-restore-windows-server-classic/samemachine.png)
4. Optar por **procurar ficheiros** ou **procurar ficheiros**.

    Deixe a opção predefinida se planear restaurar um ou mais ficheiros cujo caminho é conhecido. Se não tem a certeza sobre a estrutura de pastas, mas pretende pesquisar para um ficheiro, escolha o **procurar ficheiros** opção. Para efeitos de nesta secção, iremos irá prosseguir com a opção predefinida.

    ![Procurar ficheiros](./media/backup-azure-restore-windows-server-classic/browseandsearch.png)
5. Selecione o volume a partir do qual pretende restaurar o ficheiro.

    Pode restaurar a partir de qualquer ponto no tempo. Datas que aparecem na **negrito** no controlo de calendário indicar a disponibilidade de um ponto de restauro. Depois de uma data é seleccionada, com base na sua agenda de cópia de segurança (e a taxa de êxito de uma operação de cópia de segurança), pode selecionar um ponto no tempo do **tempo** pendente.

    ![Data e de volume](./media/backup-azure-restore-windows-server-classic/volanddate.png)
6. Selecione os itens para recuperar. Pode selecionar vários pastas/ficheiros que pretende restaurar.

    ![Selecionar ficheiros](./media/backup-azure-restore-windows-server-classic/selectfiles.png)
7. Especifique os parâmetros de recuperação.

    ![Opções de recuperação](./media/backup-azure-restore-windows-server-classic/recoveroptions.png)

   * Tem uma opção de restaurar para a localização original (em que o ficheiro/pasta será substituído) ou para outra localização no mesmo computador.
   * Se o ficheiro/pasta que pretende restaurar existe na localização de destino, pode criar cópias (duas versões do mesmo ficheiro), substituir os ficheiros na localização de destino ou ignorar a recuperação dos ficheiros de que existe no destino.
   * Recomenda-se vivamente que deixe a opção predefinida de restauro de ACLs de ficheiros que estão a ser recuperada.
8. Depois destas entradas são fornecidas, clique em **seguinte**. O fluxo de trabalho da recuperação, que restaura os ficheiros a esta máquina, irá iniciar.

## <a name="recover-to-an-alternate-machine"></a>Recuperar para uma máquina alternativa
Se o servidor completo se tenha perdido, pode continuar a recuperar dados de cópia de segurança do Azure para um computador diferente. Os passos seguintes mostram o fluxo de trabalho.  

Inclui a terminologia utilizada estes passos:

* *Máquina de origem* – a máquina original da qual foi efetuada a cópia de segurança e que não está atualmente disponível.
* *A máquina de destino* – a máquina para que os dados está a ser recuperados.
* *Cofre do exemplo* – Cofre de cópia de segurança a para o qual o *máquina de origem* e *máquina de destino* estão registados. <br/>

> [!NOTE]
> Não não possível restaurar cópias de segurança criadas a partir de uma máquina num computador que esteja a executar uma versão anterior do sistema operativo. Por exemplo, se são efetuadas cópias de segurança a partir de uma máquina Windows 7, este pode ser restaurada no Windows 8 ou superior a máquina. No entanto, o vice-versa-se não tiver a VERDADEIRO.
>
>

1. Abra o **cópia de segurança do Microsoft Azure** ajuste no *máquina de destino*.
2. Certifique-se de que o *máquina de destino* e *máquina de origem* estão registados no mesmo Cofre de cópia de segurança.
3. Clique em **recuperar dados** para iniciar o fluxo de trabalho.

    ![Recuperar dados](./media/backup-azure-restore-windows-server-classic/recover.png)
4. Selecione **outro servidor**

    ![Outro servidor](./media/backup-azure-restore-windows-server-classic/anotherserver.png)
5. Disponibilize o ficheiro de credenciais do cofre que corresponde do *Cofre de exemplo*. Se o ficheiro de credenciais do Cofre é inválido (ou expirados) Transfira um novo ficheiro de credenciais do cofre do *Cofre de exemplo* no portal clássico do Azure. Depois do ficheiro de credenciais do cofre for fornecido, é apresentado o Cofre de cópia de segurança contra o ficheiro de credenciais do cofre.
6. Selecione o *máquina de origem* da lista de máquinas apresentadas.

    ![Lista de máquinas](./media/backup-azure-restore-windows-server-classic/machinelist.png)
7. Selecione o **procurar ficheiros** ou **procurar ficheiros** opção. Para efeitos de nesta secção, iremos utilizar o **procurar ficheiros** opção.

    ![Pesquisa](./media/backup-azure-restore-windows-server-classic/search.png)
8. Selecione o volume e a data no seguinte ecrã. Procure o nome de pasta/ficheiro que pretende restaurar.

    ![Itens de procura](./media/backup-azure-restore-windows-server-classic/searchitems.png)
9. Selecione a localização onde os ficheiros têm de ser restaurados.

    ![Restaurar localização](./media/backup-azure-restore-windows-server-classic/restorelocation.png)
10. Forneça o frase de acesso de encriptação que foi fornecida durante *máquina de origem* no registo de *Cofre de exemplo*.

    ![Encriptação](./media/backup-azure-restore-windows-server-classic/encryption.png)
11. Assim que a entrada é fornecida, clique em **recuperar**, que aciona o restauro dos ficheiros de cópias de segurança para o destino fornecido.

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
    > Se não clicar Unmount, o Volume de recuperação irá permanecer montado durante seis horas desde o momento quando foi montado. Não existem operações de cópia de segurança serão executado enquanto o volume está montado. Todas as operações de cópia de segurança agendadas para execução durante a hora quando o volume está montado, será executado após a desmontados o volume de recuperação.
    >


## <a name="next-steps"></a>Passos seguintes
* [FAQ sobre a cópia de segurança do Azure](backup-azure-backup-faq.md)
* Visite o [fórum de cópia de segurança do Azure](http://go.microsoft.com/fwlink/p/?LinkId=290933).

## <a name="learn-more"></a>Saiba mais
* [Descrição geral de cópia de segurança do Azure](http://go.microsoft.com/fwlink/p/?LinkId=222425)
* [Cópia de segurança máquinas virtuais do Azure](backup-azure-vms-introduction.md)
* [Cópia de segurança segurança de cargas de trabalho da Microsoft](backup-azure-dpm-introduction.md)
