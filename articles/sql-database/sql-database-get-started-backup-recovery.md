---
title: "Introdução à cópia de segurança e ao restauro de bases de dados SQL do Azure para proteção e recuperação de dados | Microsoft Docs"
description: "Este tutorial mostra como restaurar a partir de cópias de segurança automáticas para um ponto anterior no tempo, armazenar cópias de segurança automáticas no cofre dos Serviços de Recuperação do Azure e restaurar a partir do cofre dos Serviços de Recuperação do Azure"
keywords: tutorial de base de dados sql
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: aeb8c4c3-6ae2-45f7-b2c3-fa13e3752eed
ms.service: sql-database
ms.custom: business continuity
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 12/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: a9adc82faab977ad5da1c9dc281d45db3101d8c8
ms.openlocfilehash: 3fb97f80d5012e993f92eb592d877faabc94c43e


---
# <a name="get-started-with-backup-and-restore-for-data-protection-and-recovery"></a>Introdução à Cópia de Segurança e ao Restauro para Proteção e Recuperação de Dados
Neste tutorial de introdução irá aprender a utilizar o portal do Azure para:

- Ver as cópias de segurança existentes de uma base de dados
- Restaurar uma base de dados para um ponto anterior no tempo
- Configurar a retenção de longa duração de um ficheiro de cópia de segurança de base de dados no cofre dos Serviços de Recuperação do Azure
- Restaurar uma base de dados a partir do cofre dos Serviços de Recuperação do Azure

**Estimativa de tempo**: a conclusão deste tutorial demora, aproximadamente, 30 minutos (partindo do princípio de que já cumpriu os pré-requisitos).

> [!TIP]
> Pode realizar estas mesmas tarefas num tutorial de introdução utilizando o [PowerShell](sql-database-get-started-backup-recovery-powershell.md).
>

## <a name="prerequisites"></a>Pré-requisitos

* Precisa de uma conta do Azure. Pode [abrir uma conta do Azure gratuita](/pricing/free-trial/?WT.mc_id=A261C142F) ou [Ativar as vantagens de subscritor do Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). 

* Tem de conseguir ligar ao portal do Azure com uma conta que seja membro do proprietário da subscrição ou da função de contribuinte. Para obter mais informações sobre o controlo de acesso baseado em funções (RBAC), veja o artigo [Introdução à gestão de acesso no portal do Azure](../active-directory/role-based-access-control-what-is.md).

* Concluiu a [Introdução aos servidores, bases de dados e regras de firewall da Base de Dados SQL do Azure através do portal do Azure e do SQL Server Management Studio](sql-database-get-started.md) ou a [versão do PowerShell](sql-database-get-started-powershell.md) equivalente deste tutorial. Se não o tiver feito, conclua este tutorial de pré-requisitos ou execute o script do PowerShell no final da [versão do PowerShell](sql-database-get-started-powershell.md) deste tutorial antes de continuar.


> [!NOTE]
> Este tutorial ajuda-o a aprender os conteúdos dos tópicos de aprendizagem seguintes: [SQL Database backups (Cópias de segurança da Base de Dados SQL)](sql-database-automated-backups.md), [Long-term backup retention (Retenção de cópias de segurança a longo prazo)](sql-database-long-term-retention.md) e [Recover an Azure SQL database using automated database backups (Recupera bases de dados SQL do Azure com cópias de segurança de bases de dados automáticas)](sql-database-recovery-using-backups.md).
>  

## <a name="sign-in-to-the-azure-portal-using-your-azure-account"></a>Inicie sessão no portal do Azure com a sua conta do Azure
Com a [subscrição existente](https://account.windowsazure.com/Home/Index), siga estes passos para se ligar ao portal do Azure.

1. Abra o browser da sua preferência e ligue-se ao [Portal do Azure](https://portal.azure.com/).
2. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
3. Quando for apresentada a página **Iniciar sessão**, forneça as credenciais da sua subscrição.
   
   ![Iniciar sessão](./media/sql-database-get-started/login.png)

<a name="create-logical-server-bk"></a>

## <a name="view-the-oldest-restore-point-from-the-service-generated-backups-of-a-database"></a>Ver o ponto de restauro mais antigo de cópias de segurança geradas pelo serviço de uma base de dados

Nesta secção do tutorial, vê informações sobre o ponto de restauro mais antigo das [cópias de segurança automáticas geradas pelo serviço](sql-database-automated-backups.md) da base de dados. 

1. Abra o painel **Base de dados SQL** da base de dados, **sqldbtutorialdb**.

   ![painel da nova base de dados de exemplo](./media/sql-database-get-started/new-sample-db-blade.png)

2. Na barra de ferramentas, clique em **Restaurar**.

   ![barra de ferramentas de restauro](./media/sql-database-get-started-backup-recovery/restore-toolbar.png)

3. No painel Restauro, reveja o ponto de restauro mais antigo.

   ![ponto de restauro mais antigo](./media/sql-database-get-started-backup-recovery/oldest-restore-point.png)

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Restaurar uma base de dados para um ponto anterior no tempo

Nesta secção do tutorial irá restaure a base de dados para uma nova base de dados a partir de um ponto específico anterior no tempo.

1. No painel **Restaurar** da base de dados, reveja o nome predefinido para a nova base de dados para a qual pretende restaurar a base de dados para um ponto anterior no tempo (o nome é o nome da base de dados existente com um carimbo de data/hora acrescentado). Este nome é alterado para refletir a hora que especificar nos próximos passos.

   ![nome da base de dados restaurada](./media/sql-database-get-started-backup-recovery/restored-database-name.png)

2. Clique no ícone **calendário** da caixa de entrada **Ponto de restauro (UTC)**.

   ![ponto de restauro](./media/sql-database-get-started-backup-recovery/restore-point.png)

2. No calendário, selecione uma data dentro do período de retenção

   ![data do ponto de restauro](./media/sql-database-get-started-backup-recovery/restore-point-date.png)

3. Na caixa de entrada **Restaurar ponto (UTC)**, especifique a hora na data selecionada para a qual pretende restaurar os dados na base de dados a partir de cópias de segurança automáticas da base de dados.

   ![hora do ponto de restauro](./media/sql-database-get-started-backup-recovery/restore-point-time.png)

   >[!NOTE]
   >Repare que o nome da base de dados foi alterado para refletir a data e hora que selecionou. Tenha também em atenção que não é possível alterar o servidor para o qual está a restaurar para um ponto específico anterior no tempo. Para restaurar para um servidor diferente, utilize o [Georrestauro](sql-database-disaster-recovery.md#recover-using-geo-restore). Por fim, tenha em atenção que pode restaurar para um [conjunto elástico](sql-database-elastic-jobs-overview.md) ou para um escalão de preço diferente. 
   >

4. Clique em **OK** para restaurar a base de dados para um ponto anterior no tempo para a nova base de dados.

5. Na barra de ferramentas, clique no ícone de notificação para ver o estado da tarefa de restauro.

   ![progresso da tarefa de restauro](./media/sql-database-get-started-backup-recovery/restore-job-progress.png)

6. Quando a tarefa de restauro estiver concluída, abra o painel **Bases de dados SQL** para ver a base de dados recentemente restaurada.

   ![base de dados restaurada](./media/sql-database-get-started-backup-recovery/restored-database.png)

> [!NOTE]
> A partir daqui, pode ligar à base de dados restaurada através do o SQL Server Management Studio para efetuar tarefas necessárias, bem como para [extrair alguns dados da base de dados restaurada para copiá-los para a base de dados existente ou para eliminar a base de dados existente e mudar o nome da base de dados restaurada para o nome da base de dados existente](sql-database-recovery-using-backups.md#point-in-time-restore).
>

## <a name="configure-long-term-retention-of-automated-backups-in-an-azure-recovery-services-vault"></a>Configurar a retenção de longa duração das cópias de segurança automáticas num cofre dos Serviços de Recuperação do Azure 

Nesta secção do tutorial, irá [configurar um cofre dos Serviços de Recuperação do Azure para reter cópias de segurança automáticas](sql-database-long-term-retention.md) durante um período maior do que o período de retenção para a camada de serviços. 


> [!TIP]
> Para eliminar cópias de segurança, veja [Eliminar cópias de segurança de retenção de longa duração](sql-database-long-term-retention-delete.md).
>

1. Abra o painel **SQL Server** do seu servidor, **sqldbtutorialserver**.

   ![painel do servidor sql](./media/sql-database-get-started/sql-server-blade.png)

2. Clique em **Retenção de longa duração de cópia de segurança**.

   ![ligação de retenção de longa duração de cópia de segurança](./media/sql-database-get-started-backup-recovery/long-term-backup-retention-link.png)

3. No painel **sqldbtutorial - retenção de longa duração de cópia de segurança**, reveja e aceite os termos de pré-visualização (a menos que já o tenha feito - ou esta funcionalidade ficará indisponível para pré-visualização).

   ![aceite os termos de pré-visualização](./media/sql-database-get-started-backup-recovery/accept-the-preview-terms.png)

4. Para configurar o período de retenção de longa duração da cópia de segurança da base de dados sqldbtutorialdb, selecione essa base de dados na grelha e, em seguida, clique em **Configurar** na barra de ferramentas.

   ![selecionar base de dados para retenção de longa duração de cópia de segurança](./media/sql-database-get-started-backup-recovery/select-database-for-long-term-backup-retention.png)

5. No painel **Configurar**, clique em **Configurar definições necessárias** em **Cofre dos serviços de recuperação**.

   ![configurar ligação ao cofre](./media/sql-database-get-started-backup-recovery/configure-vault-link.png)

6. No painel **Cofre dos serviços de recuperação**, selecione um cofre existente, se aplicável. Caso contrário, se não for encontrado nenhum cofre dos serviços de recuperação para a sua subscrição, clique para sair do fluxo e criar um cofre dos serviços de recuperação.

   ![criar nova ligação ao cofre](./media/sql-database-get-started-backup-recovery/create-new-vault-link.png)

7. No painel **Cofres dos Serviços de Recuperação**, clique em **Adicionar**.

   ![adicionar nova ligação ao cofre](./media/sql-database-get-started-backup-recovery/add-new-vault-link.png)
   
8. No painel **Cofre dos Serviços de Recuperação**, forneça um nome válido para o novo cofre dos Serviços de Recuperação.

   ![nome do novo cofre](./media/sql-database-get-started-backup-recovery/new-vault-name.png)

9. Selecione a sua subscrição e o grupo de recursos e, em seguida, selecione a localização do cofre. Quando terminar, clique em **Guardar**.

   ![criar novo cofre](./media/sql-database-get-started-backup-recovery/create-new-vault.png)

   > [!IMPORTANT]
   > O cofre tem de estar localizado na mesma região do servidor lógico SQL do Azure e tem de utilizar o mesmo grupo de recursos do servidor lógico.
   >

10. Após a criação do novo cofre, execute os passos necessários para voltar ao painel **Cofre dos serviços de recuperação**.

11. No painel **Cofre dos serviços de recuperação**, clique no cofre e, em seguida, clique em **Selecionar**.

   ![selecionar o cofre existente](./media/sql-database-get-started-backup-recovery/select-existing-vault.png)

12. No painel **Configurar**, forneça um nome válido para a nova política de retenção, modifique a política de retenção predefinida conforme adequado e, em seguida, clique em **OK**.

   ![definir a política de retenção](./media/sql-database-get-started-backup-recovery/define-retention-policy.png)

13. No painel **sqldbtutorial - retenção de longa duração de cópia de segurança**, clique em **Guardar** e, em seguida, clique em **OK** para aplicar a política de retenção de longa duração de cópia de segurança a todas as bases de dados selecionadas.

   ![definir a política de retenção](./media/sql-database-get-started-backup-recovery/save-retention-policy.png)

14. Clique em **Guardar** para ativar a retenção de longa duração de cópia de segurança com esta nova política para o cofre dos Serviços de Recuperação do Azure que configurou.

   ![definir a política de retenção](./media/sql-database-get-started-backup-recovery/enable-long-term-retention.png)

15. Após ter sido ativada a retenção de longa duração de cópia de segurança, abra o painel **sqldbtutorialvault** (aceda a **Todos os recursos** e selecione-o na lista de recursos da sua subscrição).

   ![ver o cofre dos serviços de recuperação](./media/sql-database-get-started-backup-recovery/view-recovery-services-vault.png)

> [!IMPORTANT]
> Depois de configuradas, as cópias de segurança aparecem no cofre nos próximos sete dias. Não continue este tutorial até que as cópias de segurança apareçam no cofre.
>

## <a name="view-backups-in-long-term-retention"></a>Ver cópias de segurança em retenção de longa duração

Nesta secção do tutorial é possível ver informações sobre as cópias de segurança da base de dados em [retenção de longa duração de cópia de segurança](sql-database-long-term-retention.md). 

1. Abra o painel **sqldbtutorialvault** (aceda a **Todos os recursos** e selecione-o na lista de recursos da sua subscrição) para ver a quantidade de armazenamento utilizado pelas cópias de segurança da base de dados no cofre.

   ![ver o cofre dos serviços de recuperação com cópias de segurança](./media/sql-database-get-started-backup-recovery/view-recovery-services-vault-with-data.png)

2. Abra o painel **Base de dados SQL** da base de dados, **sqldbtutorialdb**.

   ![painel da nova base de dados de exemplo](./media/sql-database-get-started/new-sample-db-blade.png)

3. Na barra de ferramentas, clique em **Restaurar**.

   ![barra de ferramentas de restauro](./media/sql-database-get-started-backup-recovery/restore-toolbar.png)

4. No painel Restauro, clique em **Longa duração**.

5. Nas cópias de segurança do cofre do Azure, clique em **Selecionar uma cópia de segurança** para ver as cópias de segurança da base de dados disponíveis na retenção de longa duração de cópia de segurança.

   ![cópias de segurança no cofre](./media/sql-database-get-started-backup-recovery/view-backups-in-vault.png)

## <a name="restore-a-database-from-a-backup-in-long-term-backup-retention"></a>Restaurar uma base de dados a partir de uma cópia de segurança em retenção de longa duração de cópia de segurança

Nesta secção do tutorial, irá restaurar a base de dados para uma nova base de dados a partir de uma cópia de segurança no cofre dos Serviços de Recuperação do Azure.

1. No painel **Cópias de segurança do cofre do Azure**, clique na cópia de segurança a restaurar e, em seguida, clique em **Selecionar**.

   ![Selecione a cópia de segurança no cofre](./media/sql-database-get-started-backup-recovery/select-backup-in-vault.png)

2. Na caixa de texto **Nome da base de dados**, forneça o nome da base de dados restaurada.

   ![nome da nova base de dados](./media/sql-database-get-started-backup-recovery/new-database-name.png)

3. Clique em **OK** para restaurar a base de dados a partir da cópia de segurança no cofre para a nova base de dados.

4. Na barra de ferramentas, clique no ícone de notificação para ver o estado da tarefa de restauro.

   ![progresso da tarefa de restauro a partir do cofre](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

5. Quando a tarefa de restauro estiver concluída, abra o painel **Bases de dados SQL** para ver a base de dados recentemente restaurada.

   ![restaurar base de dados a partir do cofre](./media/sql-database-get-started-backup-recovery/restored-database-from-vault.png)

> [!NOTE]
> A partir daqui, pode ligar à base de dados restaurada através do o SQL Server Management Studio para efetuar tarefas necessárias, bem como para [extrair alguns dados da base de dados restaurada para copiá-los para a base de dados existente ou para eliminar a base de dados existente e mudar o nome da base de dados restaurada para o nome da base de dados existente](sql-database-recovery-using-backups.md#point-in-time-restore).
>

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre cópias de segurança automáticas geradas pelo serviço, veja [cópias de segurança automáticas](sql-database-automated-backups.md)
- Para saber mais sobre a retenção de longa duração de cópia de segurança, veja [retenção de longa duração de cópia de segurança](sql-database-long-term-retention.md)
- Para saber mais sobre o restauro de cópias de segurança, veja [restaurar a partir de cópia de segurança](sql-database-recovery-using-backups.md)



<!--HONumber=Jan17_HO2-->


