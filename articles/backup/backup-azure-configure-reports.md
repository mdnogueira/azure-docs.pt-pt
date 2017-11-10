---
title: "Configurar relatórios para cópia de segurança do Azure"
description: "Este artigo aborda configurar relatórios do Power BI para a cópia de segurança do Azure com o Cofre de serviços de recuperação."
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: 86e465f1-8996-4a40-b582-ccf75c58ab87
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/10/2017
ms.author: pajosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 40433df5ebe90aec3a9294f2c5a6083c4567b161
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2017
---
# <a name="configure-azure-backup-reports"></a>Configurar relatórios do Azure Backup
Este artigo aborda os passos para configurar os relatórios para cópia de segurança do Azure com o Cofre de serviços de recuperação e para aceder a estes relatórios através do Power BI. Depois de efetuar estes passos, pode ir diretamente para o Power BI para ver todos os relatórios, personalizar e criar relatórios. 

## <a name="supported-scenarios"></a>Cenários suportados
1. Relatórios de cópia de segurança do Azure são suportados para cópia de segurança da máquina virtual do Azure e cópia de segurança do ficheiro/pasta para a nuvem utilizando o Azure Recovery Services Agent.
2. Relatórios de SQL do Azure, o DPM e o servidor de cópia de segurança do Azure não são suportados neste momento.
3. Pode visualizar relatórios em cofres e em subscrições, se a mesma conta de armazenamento está configurada para cada uma dos cofres. Conta de armazenamento selecionada deve ser na mesma região que o Cofre de serviços de recuperação.
4. A frequência de atualização agendada para os relatórios é de 24 horas no Power BI. Também pode efetuar uma atualização do ad-hoc dos relatórios no Power BI, na qual maiúsculas dados mais recentes na conta de armazenamento do cliente são utilizados para composição de relatórios. 
5. Relatórios de cópia de segurança do Azure não são atualmente suportados em nuvens nacionais.

## <a name="prerequisites"></a>Pré-requisitos
1. Criar um [conta do storage do Azure](../storage/common/storage-create-storage-account.md#create-a-storage-account) configurá-la para relatórios. Esta conta de armazenamento é utilizada para armazenar dados relacionados de relatórios.
2. [Criar uma conta do Power BI](https://powerbi.microsoft.com/landing/signin/) para visualizar, personalizar e criar os seus próprios relatórios através do portal do Power BI.
3. Registar o fornecedor de recursos **insights** se não registado já, com a subscrição da conta de armazenamento e também com a subscrição do Cofre de serviços de recuperação para ativar relatórios dados fluam para o armazenamento conta. Para o fazer, tem de ir ao portal do Azure > subscrição > fornecedores de recursos e verificação para este fornecedor para o registar. 

## <a name="configure-storage-account-for-reports"></a>Configurar a conta de armazenamento para relatórios
Utilize os seguintes passos para configurar a conta de armazenamento para o Cofre de serviços de recuperação através do portal do Azure. Esta é uma configuração única e depois de ter configurada a conta de armazenamento, pode aceder ao Power BI diretamente para ver o pacote de conteúdos e tirar partido dos relatórios.
1. Se já tiver um cofre dos serviços de recuperação, abrir, avance para o passo seguinte. Se não tiver um cofre dos Serviços de Recuperação aberto, mas está no portal do Azure, no menu Hub, clique em **Procurar**.

   * Na lista de recursos, escreva **Serviços de Recuperação**.
   * À medida que começa a escrever, a lista filtra com base na sua entrada. Quando vir **Cofres dos Serviços de Recuperação**, clique no mesmo.

      ![Passo 1 da Criação de um Cofre dos Serviços de Recuperação](./media/backup-azure-vms-encryption/browse-to-rs-vaults.png) <br/>

     É apresentada a lista dos cofres dos Serviços de Recuperação. Na lista dos cofres dos Serviços de Recuperação, selecione um cofre.

     O dashboard do cofre selecionado é aberto.
2. Na lista de itens que é apresentado em cofre, clique em **relatórios de cópia de segurança** na secção monitorização e relatórios para configurar a conta de armazenamento para relatórios.

      ![Selecione os relatórios de cópia de segurança menu item passo 2](./media/backup-azure-configure-reports/backup-reports-settings.PNG)
3. No painel de relatórios de cópia de segurança, clique em **as definições de diagnóstico** ligação. Esta ação abre as IU que é utilizado para enviar dados para a conta de armazenamento do cliente de definições de diagnóstico.

      ![Ative o diagnóstico passo 3](./media/backup-azure-configure-reports/backup-azure-configure-reports.png)
4. Clique na ligação **ative os diagnósticos**. Abre a IU para configurar a conta de armazenamento. 

      ![Ativar o passo de diagnóstico 4](./media/backup-azure-configure-reports/enable-diagnostics.png)
5. Introduza o nome da definição no campo **nome** e selecione **arquivo para uma conta de armazenamento** caixa de verificação, para que os relatórios de dados podem começar a fluir em à conta de armazenamento.

      ![Ative o diagnóstico passo 5](./media/backup-azure-configure-reports/select-setting-name.png)
6. Clique em selecionador de conta de armazenamento e selecione a conta de armazenamento e de subscrição relevante na lista para armazenar dados de relatórios e clique em **OK**.

      ![Selecione o passo de conta de armazenamento 6](./media/backup-azure-configure-reports/select-subscription-sa.png)
7. Selecione **AzureBackupReport** caixa na secção de registo de verificação e mova o controlo de deslize para o período de retenção Selecione para este dados de relatórios. Relatórios de dados na conta de armazenamento é mantido durante o período selecionado utilizar este controlo de deslize.

      ![Guardar o passo de conta de armazenamento 7](./media/backup-azure-configure-reports/save-diagnostic-settings.png)
8. Reveja a todas as alterações e clique em **guardar** botão na parte superior, conforme mostrado na figura acima. Esta ação garante que todas as alterações são guardadas e conta de armazenamento está agora configurada para armazenar dados de relatórios.

9. A tabela de definições de diagnóstico deve agora mostrar a nova definição ativada para o cofre. Se este não for apresentada, atualize a tabela para ver a definição atualizada.

      ![Passo 9 da definição de diagnóstico de vista](./media/backup-azure-configure-reports/diagnostic-setting-row.png)

> [!NOTE]
> Depois de configurar os relatórios ao guardar a conta de armazenamento, deve **Aguarde 24 horas** para enviar por push dados iniciais concluir. Deve importar o pacote de conteúdos de cópia de segurança do Azure no Power BI apenas após esse tempo. Consulte [secção das FAQ](#frequently-asked-questions) para obter mais detalhes. 
>
>

## <a name="view-reports-in-power-bi"></a>Ver relatórios no Power BI 
Depois de configurar a conta de armazenamento para os relatórios com o Cofre de serviços de recuperação, demora cerca de 24 horas para os dados de relatórios para começar a fluir. Após 24 horas de configuração de conta de armazenamento, utilize os seguintes passos para ver relatórios no Power BI:
1. [Inicie sessão no](https://powerbi.microsoft.com/landing/signin/) ao Power BI.
2. Clique em **obter dados** e clique em Get em **serviços** na biblioteca de pacote de conteúdos. Utilize os passos mencionados [documentação do Power BI para aceder ao pacote de conteúdos](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-packs-services/).

     ![Importar o pacote de conteúdos](./media/backup-azure-configure-reports/content-pack-import.png)
3. Tipo **cópia de segurança do Azure** na barra de pesquisa e clique em **obtê-lo agora**.

      ![Obter o pacote de conteúdos](./media/backup-azure-configure-reports/content-pack-get.png)
4. Introduza o nome da conta de armazenamento configurado no passo 5 acima e clique em **seguinte** botão.

    ![Introduza o nome de conta de armazenamento](./media/backup-azure-configure-reports/content-pack-storage-account-name.png)    
5. Introduza a chave de conta de armazenamento para esta conta de armazenamento. Pode [ver e copiar chaves de acesso de armazenamento](../storage/common/storage-create-storage-account.md#manage-your-storage-account) navegando para a sua conta do storage no portal do Azure. 

     ![Introduza a conta de armazenamento](./media/backup-azure-configure-reports/content-pack-storage-account-key.png) <br/>
     
6. Clique em **sessão** botão. Após o início de sessão for bem sucedida, obter **importar dados** notificação.

    ![Importar o pacote de conteúdos](./media/backup-azure-configure-reports/content-pack-importing-data.png) <br/>
    
    Após algum tempo, obter **êxito** notificação após a importação estiver concluída. Poderá demorar pouco mais para importar o pacote de conteúdos, se existir uma grande quantidade de dados na conta de armazenamento.
    
    ![Importar o pacote de conteúdos de êxito](./media/backup-azure-configure-reports/content-pack-import-success.png) <br/>
    
7. Assim que os dados são importados com êxito, **cópia de segurança do Azure** pacote de conteúdos é visível na **aplicações** no painel de navegação. A lista mostra agora o dashboard de cópia de segurança do Azure, relatórios e conjunto de dados com uma estrela amarela indicar relatórios recentemente importados. 

     ![Pacote de conteúdos de cópia de segurança do Azure](./media/backup-azure-configure-reports/content-pack-azure-backup.png) <br/>
     
8. Clique em **cópia de segurança do Azure** em Dashboards, que mostra um conjunto de relatórios de chaves afixados.

      ![Dashboard de cópia de segurança do Azure](./media/backup-azure-configure-reports/azure-backup-dashboard.png) <br/>
9. Para ver o conjunto completo de relatórios, clique em qualquer relatório no dashboard.

      ![Integridade de tarefa de cópia de segurança do Azure](./media/backup-azure-configure-reports/azure-backup-job-health.png) <br/>
10. Clique em cada separador Relatórios para ver relatórios dessa área.

      ![Separadores de relatórios de cópia de segurança do Azure](./media/backup-azure-configure-reports/reports-tab-view.png)


## <a name="frequently-asked-questions"></a>Perguntas mais frequentes
1. **Como verificar se os dados de relatórios foi iniciado que circulam na conta de armazenamento?**
    
    Pode ir para a conta de armazenamento configurada e selecionar contentores. Se o contentor tem uma entrada para azurebackupreport de registos de informações, ele indica que dados de relatórios iniciou fluir em.

2. **O que é a frequência de push de dados para a conta de armazenamento e o pacote de conteúdos de cópia de segurança do Azure no Power BI?**

   Para os utilizadores de dia 0, seria demora cerca de 24 horas para enviar dados para a conta de armazenamento. Depois desta push inicial é compelete, os dados são atualizados com a seguinte frequência mostrada na imagem abaixo. 
      * Dados relacionados com **tarefas, alertas, itens de cópia de segurança, cofres, servidores protegidos e políticas** é enviada para a conta de armazenamento de cliente como e quando é registado.
      * Dados relacionados com **armazenamento** é enviada para a conta de armazenamento do cliente a cada 24 horas.
   
    ![Frequência de push de dados de relatórios de cópia de segurança do Azure](./media/backup-azure-configure-reports/reports-data-refresh-cycle.png)

  Power BI tem um [atualização agendada uma vez por dia](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/#what-can-be-refreshed). Pode efetuar uma atualização manual dos dados no Power BI para o pacote de conteúdos.

3. **Quanto pode manter os relatórios?** 

   Ao configurar a conta de armazenamento, pode selecionar o período de retenção de dados de relatórios na conta de armazenamento (utilizando o passo 6 na configuração da conta de armazenamento para a secção de relatórios acima). Além disso, pode [relatórios analisar no excel](https://powerbi.microsoft.com/documentation/powerbi-service-analyze-in-excel/) e guardá-las para um período de retenção mais longo, de acordo com as necessidades. 

4. **Irá ver todos os meus dados nos relatórios depois de configurar a conta de armazenamento?**

   Todos os dados gerados após **"Configurar a conta de armazenamento"** será enviada para a conta de armazenamento e de estar disponível nos relatórios. No entanto, **em curso tarefas não são enviadas por push** para os relatórios. Depois da tarefa concluir ou falha, é enviada aos relatórios.

5. **Se já posso ter configurado a conta de armazenamento para ver relatórios, posso alterar a configuração para utilizar outra conta de armazenamento?** 

   Sim, pode alterar a configuração para apontar para uma conta de armazenamento diferente. Deve utilizar a conta de armazenamento recentemente configurados durante a ligação ao pacote de conteúdos de cópia de segurança do Azure. Além disso, depois de ter configurada uma conta de armazenamento diferente, novo seriam do fluxo de dados nesta conta de armazenamento. Mas dados antigos (antes de alterar a configuração) ainda permanecerá na conta de armazenamento mais antiga.

6. **Posso visualizar relatórios em cofres e entre subscrições?** 

   Sim, pode configurar a mesma conta de armazenamento entre vários cofres para ver relatórios de cofre cruzada. Além disso, pode configurar a mesma conta de armazenamento para cofres nas subscrições. Em seguida, pode utilizar esta conta de armazenamento ao ligar ao pacote de conteúdos de cópia de segurança do Azure no Power BI para ver os relatórios. No entanto, a conta de armazenamento selecionada deve ser na mesma região que o Cofre de serviços de recuperação.
   
## <a name="troubleshooting-errors"></a>Resolução de problemas de erros
| Detalhes do erro | Resolução |
| --- | --- |
| Depois de configurar a conta de armazenamento para os relatórios de cópia de segurança, **conta de armazenamento** continua a mostrar **não configurado**. | Se tiver configurado a conta de armazenamento com êxito, os dados de relatórios irão fluir no, apesar deste problema. Para resolver este problema, aceda ao portal do Azure > serviços mais > definições de diagnóstico > RS cofre > Editar definição. Elimine a definição configurada anteriormente e criar uma nova definição a partir do mesmo painel. Este tempo, defina o campo **nome** para **serviço**. Isto deve mostrar a conta de armazenamento configurados. |
|Depois de importar a cópia de segurança do Azure pack no Power BI, o erro de conteúdo **não é possível localizar o contentor de 404** é apresentada. | Como sugerido neste documento, tem de aguardar 24 horas depois de configurar relatórios no Cofre de serviços de recuperação para vê-los corretamente no Power BI. Se tentar aceder aos relatórios antes de 24 horas, irá obter este erro, uma vez que os dados completos ainda não estão presentes para mostrar relatórios válidos. |

## <a name="next-steps"></a>Passos seguintes
Agora que configurou a conta de armazenamento e a importar pacote de conteúdos de cópia de segurança do Azure, o passo seguinte é personalizar estes relatórios e utilizar o modelo de dados de relatórios para criar relatórios. Consulte os artigos seguintes para obter mais detalhes.

* [Utilizando o modelo de dados de relatórios do Azure Backup](backup-azure-reports-data-model.md)
* [Filtragem de relatórios no Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
* [A criação de relatórios no Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)

