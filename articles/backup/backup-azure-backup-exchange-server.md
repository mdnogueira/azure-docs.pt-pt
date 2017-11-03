---
title: "Cópia de segurança num servidor do Exchange para cópia de segurança do Azure com o System Center 2012 R2 DPM | Microsoft Docs"
description: "Saiba como fazer cópias de segurança um Exchange server Backup do Azure utilizando o System Center 2012 R2 DPM"
services: backup
documentationcenter: 
author: MaanasSaran
manager: NKolli1
editor: 
ms.assetid: 13f32256-888e-416e-a78b-40c2a26a5939
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/08/2017
ms.author: masaran;jimpark;delhan;trinadhk;markgal
ms.openlocfilehash: 9dc0105034e0d354a6cbbd7ba70634bdff83c1bd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="back-up-an-exchange-server-to-azure-backup-with-system-center-2012-r2-dpm"></a>Fazer cópia de segurança de um servidor do Exchange para o Azure Backup com o System Center 2012 R2 DPM
Este artigo descreve como configurar um servidor do System Center 2012 R2 Data Protection Manager (DPM) para fazer cópias de segurança de um servidor do Microsoft Exchange a cópia de segurança do Azure.  

## <a name="updates"></a>Atualizações
Para registar com êxito o servidor do DPM com cópia de segurança do Azure, tem de instalar o update rollup mais recente para o System Center 2012 R2 DPM e a versão mais recente do agente do Backup do Azure. Obter o update rollup mais recente do [catálogo Microsoft](http://catalog.update.microsoft.com/v7/site/Search.aspx?q=System%20Center%202012%20R2%20Data%20protection%20manager).

> [!NOTE]
> Para os exemplos neste artigo, versão 2.0.8719.0 do agente do Backup do Azure está instalado e Update Rollup 6 está instalado no System Center 2012 R2 DPM.
>
>

## <a name="prerequisites"></a>Pré-requisitos
Antes de continuar, certifique-se de que todos os o [pré-requisitos](backup-azure-dpm-introduction.md#prerequisites) para utilizar a cópia de segurança do Microsoft Azure para proteger cargas de trabalho tiverem sido cumpridas. Estes pré-requisitos incluem o seguinte:

* Foi criado um cofre de cópia de segurança no site do Azure.
* As credenciais do cofre e o agente tenham sido transferidas para o servidor do DPM.
* O agente está instalado no servidor do DPM.
* Foram utilizadas as credenciais do cofre para registar o servidor do DPM.
* Se estiver a proteger o Exchange 2016, atualize para o DPM 2012 R2 UR9 ou posterior

## <a name="dpm-protection-agent"></a>Agente de proteção do DPM
Para instalar o agente de proteção do DPM no servidor do Exchange, siga estes passos:

1. Certifique-se de que as firewalls estão corretamente configuradas. Consulte [configurar exceções de firewall para o agente](https://technet.microsoft.com/library/Hh758204.aspx).
2. Instalar o agente no servidor do Exchange, clicando em **gestão > agentes > instalar** na consola do administrador do DPM. Consulte [instalar o agente de proteção do DPM](https://technet.microsoft.com/library/hh758186.aspx?f=255&MSPPError=-2147217396) para obter passos detalhados.

## <a name="create-a-protection-group-for-the-exchange-server"></a>Crie um grupo de proteção para o Exchange server
1. Na consola do administrador do DPM, clique em **proteção**e, em seguida, clique em **novo** no Friso de ferramentas para abrir o **criar novo grupo de proteção** assistente.
2. No **boas-vindas** ecrã do assistente, clique em **seguinte**.
3. No **selecionar tipo de grupo de proteção** ecrã, selecione **servidores** e clique em **seguinte**.
4. Selecione o Exchange server da base de dados que pretende proteger e clique em **seguinte**.

   > [!NOTE]
   > Se estiver a proteger o Exchange 2013, verifique o [pré-requisitos do Exchange 2013](https://technet.microsoft.com/library/dn751029.aspx).
   >
   >

    No exemplo seguinte, a base de dados do Exchange 2010 estiver selecionada.

    ![Selecionar Membros do grupo](./media/backup-azure-backup-exchange-server/select-group-members.png)
5. Selecione o método de proteção de dados.

    Nome do grupo de proteção e, em seguida, selecione ambas as opções seguintes:

   * Pretendo proteção a curto prazo utilizando o disco.
   * Pretendo proteção online.
6. Clique em **Seguinte**.
7. Selecione o **executar o Eseutil para verificar a integridade dos dados** opção se pretende verificar a integridade das bases de dados do Exchange Server.

    Depois de selecionar esta opção, a verificação de consistência de cópia de segurança irá ser executada no servidor do DPM para evitar o tráfego de e/s, que é gerado, executando o **eseutil** comando no servidor do Exchange.

   > [!NOTE]
   > Para utilizar esta opção, tem de copiar os ficheiros Ese.dll e Eseutil.exe para o diretório de C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin no servidor do DPM. Caso contrário, é acionado o seguinte erro:  
   > ![Erro de Eseutil](./media/backup-azure-backup-exchange-server/eseutil-error.png)
   >
   >
8. Clique em **Seguinte**.
9. Selecione a base de dados para **cópia de segurança**e, em seguida, clique em **seguinte**.

   > [!NOTE]
   > Se não selecionar "Cópia de segurança completa" para, pelo menos, um DAG cópia de uma base de dados, os registos não serão truncados.
   >
   >
10. Configurar os objetivos para **cópia de segurança de curta duração**e, em seguida, clique em **seguinte**.
11. Reveja o espaço em disco disponível e, em seguida, clique em **seguinte**.
12. Selecione a hora em que o servidor do DPM criar a replicação inicial e, em seguida, clique em **seguinte**.
13. Selecione as opções de verificação de consistência e, em seguida, clique em **seguinte**.
14. Escolha a base de dados que pretende fazer cópias de segurança do Azure e, em seguida, clique em **seguinte**. Por exemplo:

    ![Especificar dados da proteção online](./media/backup-azure-backup-exchange-server/specify-online-protection-data.png)
15. Definir a agenda para **cópia de segurança do Azure**e, em seguida, clique em **seguinte**. Por exemplo:

    ![Especifique a agenda de cópia de segurança online](./media/backup-azure-backup-exchange-server/specify-online-backup-schedule.png)

    > [!NOTE]
    > Tenha em atenção de pontos de recuperação Online baseiam-se no express completa pontos de recuperação. Por conseguinte, tem de agendar o ponto de recuperação online depois de o ponto de recuperação de tempo especificado para o completa rápida.
    >
    >
16. Configure a política de retenção para **cópia de segurança do Azure**e, em seguida, clique em **seguinte**.
17. Escolha uma opção de replicação online e clique em **seguinte**.

    Se tiver uma base de dados grande, pode demorar muito tempo a cópia de segurança inicial a ser criado através da rede. Para evitar este problema, pode criar uma cópia de segurança offline.  

    ![Especificar a política de retenção online](./media/backup-azure-backup-exchange-server/specify-online-retention-policy.png)
18. Confirme as definições e, em seguida, clique em **criar grupo**.
19. Clique em **Fechar**.

## <a name="recover-the-exchange-database"></a>Recuperar a base de dados do Exchange
1. Para recuperar uma base de dados do Exchange, clique em **recuperação** na consola do administrador do DPM.
2. Localize a base de dados do Exchange que pretende recuperar.
3. Selecione um ponto de recuperação online a partir de *hora da recuperação* na lista pendente.
4. Clique em **recuperar** para iniciar o **Assistente de recuperação**.

Para pontos de recuperação online, existem cinco tipos de recuperação:

* **Recuperar para a localização original do Exchange Server:** os dados serão recuperados para o Exchange server original.
* **Recuperar para outra base de dados num Exchange Server:** os dados serão recuperados para outra base de dados noutro servidor do Exchange.
* **Recuperar para uma base de dados de recuperação:** os dados serão recuperados para uma base de dados de recuperação do Exchange (RDB).
* **Copiar para uma pasta de rede:** os dados serão recuperados para uma pasta de rede.
* **Copiar para banda:** se tiver uma biblioteca de bandas ou unidade de banda autónoma ligado e configurado no servidor do DPM, o ponto de recuperação será copiado para uma banda livre.

    ![Escolha de replicação online](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>Passos seguintes
* [FAQ sobre a cópia de segurança do Azure](backup-azure-backup-faq.md)
