---
title: O Backup do Azure para cargas de trabalho do SQL Server utilizando o DPM | Microsoft Docs
description: "Uma introdução à cópia de segurança de bases de dados do SQL Server utilizando o serviço de cópia de segurança do Azure"
services: backup
documentationcenter: 
author: adigan
manager: Nkolli
editor: 
ms.assetid: 59df5bec-d959-457d-8731-7b20f7f1013e
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: adigan;giridham;jimpark;markgal;trinadhk
ms.openlocfilehash: c9edc066ea2edc9cd4b8453047d5584a588174dc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="back-up-sql-server-to-azure-as-a-dpm-workload"></a>Criar cópias de segurança do SQL Server para o Azure como uma carga de trabalho do DPM
Este artigo orienta-o através dos passos de configuração para cópia de segurança das bases de dados do SQL Server utilizando a cópia de segurança do Azure.

Para fazer uma cópia de segurança de bases de dados do SQL Server para o Azure, terá de uma conta do Azure. Se não tiver uma conta, pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

A gestão da cópia de segurança do SQL Server da base de dados para recuperação a partir do Azure e Azure envolve três passos:

1. Crie uma política de cópia de segurança para proteger as bases de dados do SQL Server para o Azure.
2. Crie cópias de segurança a pedido para o Azure.
3. Recupere a base de dados do Azure.

## <a name="before-you-start"></a>Antes de começar
Antes de começar, certifique-se de que todos os o [pré-requisitos](backup-azure-dpm-introduction.md#prerequisites) para utilizar a cópia de segurança do Microsoft Azure para proteger cargas de trabalho tiverem sido cumpridas. Os pré-requisitos abrangem tarefas, tais como: criar um cofre de cópia de segurança, transferir as credenciais do cofre, instalar o agente de cópia de segurança do Azure e registar o servidor com o cofre.

## <a name="create-a-backup-policy-to-protect-sql-server-databases-to-azure"></a>Criar uma política de cópia de segurança para proteger as bases de dados do SQL Server para o Azure
1. No servidor do DPM, clique em de **proteção** área de trabalho.
2. No Friso, clique em **novo** para criar um novo grupo de proteção.

    ![Criar grupo de proteção](./media/backup-azure-backup-sql/protection-group.png)
3. O DPM mostra o ecrã Iniciar com a documentação de orientação sobre a criação de um **grupo de proteção**. Clique em **Seguinte**.
4. Selecione **servidores**.

    ![Selecione o tipo de grupo de proteção - 'Servidores'](./media/backup-azure-backup-sql/pg-servers.png)
5. Expanda a máquina do SQL Server onde as bases de dados para cópia de segurança estão presentes. O DPM mostra várias origens de dados que podem ser uma cópia de segurança a partir desse servidor. Expanda o **todas as partilhas de SQL** e selecione as bases de dados (neste caso, selecionamos ReportServer$ MSDPM2012 e ReportServer$ MSDPM2012TempDB) a cópia de segurança. Clique em **Seguinte**.

    ![Selecione a base de dados SQL](./media/backup-azure-backup-sql/pg-databases.png)
6. Forneça um nome para o grupo de proteção e selecione o **pretendo proteção online** caixa de verificação.

    ![Método de proteção de dados - disco de curto prazo e Online do Azure](./media/backup-azure-backup-sql/pg-name.png)
7. No **especificar objetivos a curto prazo** ecrã, incluir entradas necessárias para criar pontos de cópia de segurança para disco.

    Aqui Vemos que **período de retenção** está definido como *5 dias*, **frequência de sincronização** está definido como uma vez cada *15 minutos* que é a frequência com que foi efetuada a cópia de segurança. **Cópia de segurança completa rápida** está definido como *8:00 P.M*.

    ![Objetivos de curto prazo](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > Às 8:00 PM (de acordo com a entrada de ecrã) é criado um ponto de cópia de segurança diariamente, transferindo os dados que tem sido modificados a partir do ponto de cópia de segurança de 8:00 PM o dia anterior. Este processo é denominado **cópia de segurança completa rápida**. Enquanto a transação registos são sincronizados a cada 15 minutos, se for necessário para recuperar a base de dados ao 9:00 PM –, o ponto é criado pelo replaying os registos dos últimos express ponto de cópia de segurança completa (de 8 horas neste caso).
   >
   >

8. Clique em **Seguinte**

    O DPM mostra o espaço de armazenamento geral disponível e a utilização de espaço em disco potencial.

    ![Alocação de disco](./media/backup-azure-backup-sql/pg-storage.png)

    Por predefinição, o DPM cria um volume por origem de dados (SQL Server da base de dados) que é utilizado para a cópia de segurança inicial. Através desta abordagem, o Gestor de discos lógicos (LDM) limita a proteção do DPM para origens de dados de 300 (bases de dados do SQL Server). Para contornar esta limitação, selecione o **colocalizar dados no agrupamento de armazenamento do DPM**, opção. Se utilizar esta opção, o DPM utiliza um único volume de várias origens de dados, que permite que o DPM pode proteger até 2000 bases de dados do SQL Server.

    Se **aumentar automaticamente os volumes** opção estiver selecionada, o DPM pode conta para o volume de cópia de segurança maior à medida que aumenta os dados de produção. Se **aumentar automaticamente os volumes** opção não estiver selecionada, o DPM limita o armazenamento de cópia de segurança utilizado para as origens de dados do grupo de proteção.
9. Os administradores são dada a opção de transferência esta cópia de segurança inicial manualmente (desativado rede) para evitar congestionamento de largura de banda ou através da rede. Pode também configurar a hora em que pode acontecer a transferência inicial. Clique em **Seguinte**.

    ![Método de replicação inicial](./media/backup-azure-backup-sql/pg-manual.png)

    A cópia de segurança inicial necessita de transferência da origem de dados completo (base de dados do SQL Server) do servidor de produção (máquina do SQL Server) para o servidor do DPM. Estes dados poderão ser grandes e transferir os dados através da rede foi exceder a largura de banda. Por este motivo, os administradores podem optar por transferir a cópia de segurança inicial: **manualmente** (utilizando o suporte de dados amovível) para evitar congestionamento de largura de banda, ou **automaticamente através da rede** (a uma determinada hora).

    Assim que a cópia de segurança inicial estiver concluída, o resto das cópias de segurança são as cópias de segurança incrementais a cópia de segurança inicial. Cópias de segurança incrementais tendem a ser pequeno e são facilmente transferidas através da rede.
10. Escolha se pretende que a verificação de consistência para executar e clique em **seguinte**.

    ![Verificação de consistência](./media/backup-azure-backup-sql/pg-consistent.png)

    O DPM pode efetuar uma consistência verificação para verificação a integridade do ponto de cópia de segurança. Calcula a soma de verificação do ficheiro de cópia de segurança no servidor de produção (máquina do SQL Server neste cenário) e os dados de cópia de segurança para que o ficheiro no DPM. No caso de conflito, presume-se que o ficheiro de cópia de segurança do DPM está danificado. O DPM rectifies os dados de cópia de segurança, enviando os blocos correspondente para o erro de correspondência de soma de verificação. Como a verificação de consistência é uma operação intensiva de desempenho, os administradores têm a opção de agendamento da verificação de consistência ou executar automaticamente.
11. Para especificar a proteção online das origens de dados, selecione as bases de dados protegidos do Azure, clique em **seguinte**.

    ![Selecione as origens de dados](./media/backup-azure-backup-sql/pg-sqldatabases.png)
12. Os administradores podem optar agendas de cópia de segurança e as políticas de retenção que melhor corresponda às respetivas políticas da organização.

    ![Agenda e retenção](./media/backup-azure-backup-sql/pg-schedule.png)

    Neste exemplo, as cópias de segurança são efetuadas uma vez por dia às 12:00 PM e 8 PM (parte inferior do ecrã)

    > [!NOTE]
    > É uma boa prática ter alguns pontos de recuperação de curta duração em disco para a recuperação rápida. Estes pontos de recuperação são utilizados para "recuperação operacional". Azure funciona como uma localização de boa retirada do local com SLAs superiores e garantida disponibilidade.
    >
    >

    **Melhor prática**: Certifique-se de que estão agendadas de cópias de segurança do Azure após a conclusão de cópias de segurança de disco local utilizando o DPM. Isto permite que a cópia de segurança de disco mais recente ser copiado para o Azure.

13. Escolha o agendamento de política de retenção. Os detalhes sobre como funciona a política de retenção são fornecidos ao [utilize Azure Backup para substituir o seu artigo de infraestrutura da banda](backup-azure-backup-cloud-as-tape.md).

    ![Política de retenção](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    Neste exemplo:

    * As cópias de segurança são efetuadas uma vez por dia às 12:00 PM e 8 PM (parte inferior do ecrã) e são mantidas durante 180 dias.
    * A cópia de segurança no Sábado às 12:00 são mantidas durante 104 semanas
    * A cópia de segurança no último Sábado às 12:00 são mantidas durante 60 meses
    * A cópia de segurança no último Sábado de Março às 12:00 é mantido para 10 anos
14. Clique em **seguinte** e selecione a opção adequada para transferir a cópia de segurança inicial para o Azure. Pode escolher **automaticamente através da rede** ou **cópia de segurança Offline**.

    * **Automaticamente através da rede** transfere os dados de cópia de segurança para o Azure, de acordo com a agenda escolhida para cópia de segurança.
    * Como **cópia de segurança Offline** funciona é explicada no [fluxo de trabalho de cópia de segurança Offline no Backup do Azure](backup-azure-backup-import-export.md).

    Escolha o mecanismo de transferência relevantes para enviar a cópia de segurança inicial para o Azure e clique em **seguinte**.
15. Depois de rever os detalhes da política no **resumo** ecrã, clique em de **criar grupo** botão para concluir o fluxo de trabalho. Pode clicar no **fechar** botão e monitorizar o progresso da tarefa na área de trabalho de monitorização.

    ![Criação do grupo de proteção em curso](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="on-demand-backup-of-a-sql-server-database"></a>Cópia de segurança a pedido de uma base de dados do SQL Server
Enquanto os passos anteriores criada uma política de cópia de segurança, é criado um "ponto de recuperação" apenas quando ocorre a primeira cópia de segurança. Em vez de aguardar que o programador iniciar, os passos abaixo acionador a criação de uma recuperação ponto manualmente.

1. Aguarde até que mostra o estado do grupo de proteção **OK** para a base de dados antes de criar o ponto de recuperação.

    ![Membros do grupo de proteção](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
2. Clique com o botão direito na base de dados e selecione **criar ponto de recuperação**.

    ![Criar ponto de recuperação Online](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
3. Escolha **proteção Online** no menu pendente e clique **OK**. Esta ação inicia a criação de um ponto de recuperação no Azure.

    ![Criar ponto de recuperação](./media/backup-azure-backup-sql/sqlbackup-azure.png)
4. Pode ver o progresso da tarefa no **monitorização** onde poderá encontrar um em curso da área de trabalho da tarefa, tal como aquele representados na figura seguinte.

    ![Consola de monitorização](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Recuperar uma base de dados do SQL Server a partir do Azure
Os seguintes passos são necessários para recuperar uma entidade protegida (base de dados do SQL Server) a partir do Azure.

1. Abra a consola de gestão do servidor do DPM. Navegue para **recuperação** área de trabalho onde pode ver os servidores de cópias de segurança do DPM. Procure a base de dados necessária (neste cenário ReportServer$ MSDPM2012). Selecione um **recuperação a partir de** tempo que termina com **Online**.

    ![Selecione o ponto de recuperação](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
2. O nome de base de dados com o botão direito e clique em **recuperar**.

    ![Recuperar a partir do Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
3. O DPM mostra os detalhes dos pontos de recuperação. Clique em **Seguinte**. Para substituir a base de dados, selecione o tipo de recuperação **recuperar na instância original do SQL Server**. Clique em **Seguinte**.

    ![Recuperar para a localização Original](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    Neste exemplo, o DPM permite a recuperação da base de dados noutra instância do SQL Server ou para uma pasta de rede autónomo.
4. No **opções especificar recuperação** ecrã, pode selecionar as opções de recuperação, como a limitação da utilização de largura de banda de rede para limitar a largura de banda utilizada pela recuperação. Clique em **Seguinte**.
5. No **resumo** ecrã, pode ver todas as configurações de recuperação até ao momento fornecidas. Clique em **recuperar**.

    O estado de recuperação mostra a base de dados a ser recuperada. Pode clicar em **fechar** para fechar o assistente e ver o progresso no **monitorização** área de trabalho.

    ![Iniciar o processo de recuperação](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    Após a conclusão da recuperação, a base de dados restaurada é consistente de aplicação.

### <a name="next-steps"></a>Passos Seguintes:
• [FAQ sobre a cópia de segurança do azure](backup-azure-backup-faq.md)
