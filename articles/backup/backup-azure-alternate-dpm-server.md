---
title: "Recuperar dados a partir de um servidor de cópia de segurança do Azure | Microsoft Docs"
description: "Recupere os dados protegido por si para um cofre dos serviços de recuperação a partir de qualquer servidor de cópia de segurança do Azure registados que cofre."
services: backup
documentationcenter: 
author: nkolli1
manager: shreeshd
editor: 
ms.assetid: a55f8c6b-3627-42e1-9d25-ed3e4ab17b1f
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/18/2017
ms.author: adigan;giridham;trinadhk;markgal
ms.openlocfilehash: 688d155b68bc2d76d53f78d251bc2f659582845f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="recover-data-from-azure-backup-server"></a>Recuperar dados do Azure Backup Server
Pode utilizar o servidor de cópia de segurança do Azure para recuperar os dados que criou uma cópia de segurança para um cofre dos serviços de recuperação. O processo para fazê-lo, por isso, está integrado na consola de gestão do servidor de cópia de segurança do Azure e é semelhante para o fluxo de trabalho de recuperação para outros componentes do Backup do Azure.

> [!NOTE]
> Este artigo é aplicável para [System Center Data Protection Manager 2012 R2 com UR7 ou posterior] (https://support.microsoft.com/en-us/kb/3065246), juntamente com o [agente de cópia de segurança do Azure mais recente](http://aka.ms/azurebackup_agent).
>
>

Para recuperar dados de um servidor de cópia de segurança do Azure:

1. Do **recuperação** separador da consola de gestão do servidor de cópia de segurança do Azure, clique em **'Adicionar DPM externo'** (na parte superior esquerda do ecrã).   
    ![Adicionar DPM externo](./media/backup-azure-alternate-dpm-server/add-external-dpm.png)
2. Transferência novo **credenciais do cofre** do cofre associado a **servidor de cópia de segurança do Azure** onde os dados estão a ser recuperados, escolha a registar o servidor de cópia de segurança do Azure da lista de servidores de cópia de segurança do Azure com os serviços de recuperação cofre e fornecer o **frase de acesso de encriptação** associadas ao servidor cujos dados está a ser recuperados.

    ![Credenciais do DPM externo](./media/backup-azure-alternate-dpm-server/external-dpm-credentials.png)

   > [!NOTE]
   > Apenas servidores de cópia de segurança do Azure associado ao mesmo Cofre de registo pode recuperar os dados entre si.
   >
   >

    Assim que o servidor de cópia de segurança do Azure externo é adicionado com êxito, pode procurar os dados do servidor externo e o servidor de cópia de segurança local do Azure do **recuperação** separador.
3. Procurar a lista de servidores de produção protegidas pelo servidor de cópia de segurança externos do Azure disponíveis e selecione a origem de dados adequada.

    ![Procurar servidor do DPM externo](./media/backup-azure-alternate-dpm-server/browse-external-dpm.png)
4. Selecione **no mês e ano** do **pontos de recuperação** pendente, selecione o necessário **Data Recovery** para quando o ponto de recuperação foi criado e selecione o **Hora da recuperação**.

    É apresentada uma lista de ficheiros e pastas no painel inferior, que pode ser navegado e recuperar para qualquer localização.

    ![Pontos de recuperação de servidor de DPM externo](./media/backup-azure-alternate-dpm-server/external-dpm-recoverypoint.png)
5. Clique com o botão direito do rato no item adequado e clique em **recuperar**.

    ![Recuperação do DPM externa](./media/backup-azure-alternate-dpm-server/recover.png)
6. Reveja o **recuperar seleção**. Certifique-se de que os dados e hora da cópia de segurança a ser recuperada, bem como a origem a partir do qual foi criada a cópia de segurança. Se a seleção está incorreta, clique em **Cancelar** para navegar para o separador de recuperação para selecionar o ponto de recuperação adequadas. Se a seleção está correta, clique em **seguinte**.

    ![Recuperação do DPM externa resumo](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-summary.png)
7. Selecione **recuperar para uma localização alternativa**. **Procurar** para a localização correta para a recuperação.

    ![Externa localização alternativa de recuperação do DPM](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-alternate-location.png)
8. Escolha a opção relacionados com **criar cópia**, **ignorar**, ou **substituir**.

   * **Criar cópia** -cria uma cópia do ficheiro se existir uma colisão de nomes.
   * **Ignorar** - se houver uma colisão de nomes, não é possível recuperar o ficheiro que deixa o ficheiro original.
   * **Substituir** - se houver uma colisão de nomes, substitui a cópia existente do ficheiro.

     Escolha a opção adequada para **restaurar segurança**. Pode aplicar as definições de segurança do computador de destino onde os dados estão a ser recuperados ou as definições de segurança que foram aplicáveis ao produto durante a que criação do ponto de recuperação.

     Identificar se um **notificação** é enviada, uma vez a recuperação concluída com êxito.

     ![Notificações de recuperação do DPM externo](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-notifications.png)
9. O **resumo** ecrã apresenta uma lista de opções escolhidas até ao momento. Assim que clicar em **'Recuperar'**, os dados são recuperados para a localização apropriada no local.

    ![Resumo de opções de recuperação do DPM externo](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-options-summary.png)

   > [!NOTE]
   > A tarefa de recuperação pode ser monitorizada no **monitorização** separador do servidor de cópia de segurança do Azure.
   >
   >

    ![Recuperação de monitorização](./media/backup-azure-alternate-dpm-server/monitoring-recovery.png)
10. Pode clicar em **limpar DPM externo** no **recuperação** separador do servidor do DPM para remover a vista do servidor do DPM externo.

    ![Limpar DPM externo](./media/backup-azure-alternate-dpm-server/clear-external-dpm.png)

## <a name="troubleshooting-error-messages"></a>Mensagens de erro de resolução de problemas
| Não. | Mensagem de erro | Passos de resolução de problemas |
|:---:|:--- |:--- |
| 1. |Este servidor não está registado no cofre especificado pelas credenciais do cofre. |**Causa:** este erro ocorre quando o ficheiro de credenciais do cofre selecionado não pertence ao Cofre de serviços de recuperação associado ao servidor de cópia de segurança do Azure no qual a recuperação é tentada. <br> **Resolução:** transferir o ficheiro de credenciais do cofre dos serviços de recuperação cofre para que o servidor de cópia de segurança do Azure está registado. |
| 2. |Os dados recuperáveis não estão disponíveis ou o servidor selecionado não é um servidor do DPM. |**Causa:** existem não existem outros servidores de cópia de segurança do Azure registado no Cofre de serviços de recuperação, os servidores não carregou ainda os metadados, ou o servidor selecionado não é um servidor de cópia de segurança do Azure (também conhecido como o Windows Server ou o cliente Windows). <br> **Resolução:** se existem outros servidores de cópia de segurança do Azure registado no Cofre de serviços de recuperação, certifique-se de que o agente de cópia de segurança do Azure mais recente está instalado. <br>Se existirem que outros servidores de cópia de segurança do Azure registado no Cofre de serviços de recuperação, aguarde por um dia após a instalação para iniciar o processo de recuperação. A tarefa noturna irá carregar os metadados para todas as cópias de segurança protegidos na nuvem. Os dados estarão disponíveis para recuperação. |
| 3. |Nenhum outro servidor DPM está registado neste cofre. |**Causa:** existem não existem outros servidores do Azure cópia de segurança que são registados no cofre a partir da qual a recuperação está a ser tentada.<br>**Resolução:** se existem outros servidores de cópia de segurança do Azure registado no Cofre de serviços de recuperação, certifique-se de que o agente de cópia de segurança do Azure mais recente está instalado.<br>Se existirem que outros servidores de cópia de segurança do Azure registado no Cofre de serviços de recuperação, aguarde por um dia após a instalação para iniciar o processo de recuperação. A tarefa noturna carrega os metadados para todas as cópias de segurança protegidos na nuvem. Os dados estarão disponíveis para recuperação. |
| 4. |O frase de acesso de encriptação fornecida não corresponde à frase de acesso associada ao seguinte servidor:**<server name>** |**Causa:** o frase de acesso de encriptação utilizada no processo de encriptação de dados a partir dos dados do servidor de cópia de segurança do Azure que está a ser recuperados não coincide com o frase de acesso de encriptação fornecida. O agente não é possível desencriptar os dados. Por conseguinte, a recuperação falhará.<br>**Resolução:** , forneça a exato mesmo encriptação frase de acesso associada ao servidor de cópia de segurança do Azure cujos dados está a ser recuperados. |

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="why-cant-i-add-an-external-dpm-server-after-installing-ur7-and-latest-azure-backup-agent"></a>Por que motivo não é possível adicionar um servidor DPM externo após a instalação UR7 e o agente de cópia de segurança do Azure mais recente?

Para os servidores do DPM com origens de dados que estão protegidos para a nuvem (utilizando um update rollup anterior ao Update Rollup 7), tem de aguardar pelo menos um dia depois de instalar o agente de cópia de segurança do Azure mais recente e UR7 para iniciar **servidor adicionar DPM externo**. É necessário o período de tempo de um dia para carregar os metadados dos grupos de proteção DPM no Azure. Metadados de grupo de proteção é carregado a primeira através de uma tarefa noturna.

### <a name="what-is-the-minimum-version-of-the-microsoft-azure-recovery-services-agent-needed"></a>O que é a versão mínima do agente dos serviços de recuperação do Microsoft Azure necessária?

A versão mínima do agente dos serviços de recuperação do Microsoft Azure ou o agente de cópia de segurança do Azure, necessário para ativar esta funcionalidade é 2.0.8719.0.  Para ver a versão do agente: Abra o painel de controlo  **>**  itens do painel de controlo de todos os  **>**  programas e funcionalidades  **>**  Agente do Microsoft Azure Recovery Services. Se a versão é inferior a 2.0.8719.0, transfira e instale o [agente de cópia de segurança do Azure mais recente](https://go.microsoft.com/fwLink/?LinkID=288905).

![Limpar DPM externo](./media/backup-azure-alternate-dpm-server/external-dpm-azurebackupagentversion.png)

## <a name="next-steps"></a>Passos seguintes:
• [FAQ sobre a cópia de segurança do azure](backup-azure-backup-faq.md)
