---
title: "A proteção do DPM/Azure cópia de segurança de um farm do SharePoint para o Azure | Microsoft Docs"
description: "Este artigo fornece uma descrição geral da proteção do servidor de cópia de segurança do DPM/Azure de um farm do SharePoint para o Azure"
services: backup
documentationcenter: 
author: adigan
manager: Nkolli1
editor: 
ms.assetid: e0c0c252-dc1d-4072-b777-7222c13950b0
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2016
ms.author: adigan;giridham;jimpark;trinadhk;markgal
ms.openlocfilehash: 1bbf3233169fa9966e3dd0fac18ee448f26caa6b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="back-up-a-sharepoint-farm-to-azure"></a>Fazer cópia de segurança de um farm do SharePoint para o Azure
A cópia de segurança um farm do SharePoint para o Microsoft Azure utilizando o System Center Data Protection Manager (DPM) muito da mesma forma que a cópia de segurança de outras origens de dados. Cópia de segurança do Azure oferece flexibilidade na agenda de cópia de segurança para criar diariamente, pontos de cópia de segurança semana, mensal ou anual e dá-lhe opções de política de retenção para vários pontos de cópia de segurança. O DPM fornece a capacidade para armazenar cópias de disco local para rápido objetivos de tempo de recuperação (RTO) e para armazenar cópias para o Azure para a retenção de longo prazo, económica.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>Versões suportadas e relacionadas com cenários de proteção do SharePoint
O Backup do Azure para o DPM suporta os seguintes cenários:

| Carga de trabalho | Versão | Implementação de SharePoint | Tipo de implementação do DPM | DPM - System Center 2012 R2 | Proteção e recuperação |
| --- | --- | --- | --- | --- | --- |
| SharePoint |O SharePoint 2013, SharePoint 2010, SharePoint 2007, SharePoint 3.0 |SharePoint implementado como um servidor físico ou máquina virtual Hyper-V/VMware <br> -------------- <br> AlwaysOn SQL |Servidor ou no local Hyper-V virtual máquina física |Suporta a cópia de segurança do Azure do Update Rollup 5 |Opções de recuperação do Farm do SharePoint de proteger: farm de recuperação, a base de dados e o ficheiro ou item de lista de pontos de recuperação do disco.  Base de dados e de farm de recuperação pontos de recuperação do Azure. |

## <a name="before-you-start"></a>Antes de começar
Existem algumas coisas que necessita de confirmar antes de efetuar cópias de segurança um farm do SharePoint para o Azure.

### <a name="prerequisites"></a>Pré-requisitos
Antes de continuar, certifique-se de que cumpriu todos os [pré-requisitos para utilizar o Microsoft Azure Backup](backup-azure-dpm-introduction.md#prerequisites) proteger cargas de trabalho. Algumas tarefas de pré-requisitos incluem: criar um cofre de cópia de segurança, transferir as credenciais do cofre, instalar o agente de cópia de segurança do Azure e registar o servidor de cópia de segurança do DPM/do Azure com o cofre.

### <a name="dpm-agent"></a>Agente do DPM
O agente do DPM tem de estar instalado no servidor que está a executar o SharePoint, os servidores que estejam a executar o SQL Server e todos os outros servidores que fazem parte do farm do SharePoint. Para mais informações sobre como configurar o agente de proteção, consulte [a configuração do agente de proteção](https://technet.microsoft.com/library/hh758034\(v=sc.12\).aspx).  A única exceção é que instalar o agente apenas no servidor web único front-end (WFE). O DPM precisa do agente num único servidor WFE apenas para servir o ponto de entrada para proteção.

### <a name="sharepoint-farm"></a>Farm do SharePoint
Para cada 10 milhões de itens no farm, tem de existir pelo menos 2 GB de espaço no volume onde está localizada a pasta do DPM. Este espaço é necessário para a geração do catálogo. Para o DPM recuperar os itens específicos (coleções de sites, sites, listas, bibliotecas de documentos, pastas, documentos individuais e itens de lista), a geração do catálogo cria uma lista dos URLs que estão contidas dentro de cada base de dados de conteúdo. Pode ver a lista de URLs no painel de itens recuperáveis no **recuperação** área da consola do administrador do DPM de tarefas.

### <a name="sql-server"></a>SQL Server
O DPM for executado como uma conta LocalSystem. Fazer cópias de segurança de bases de dados do SQL Server, o DPM precisa de privilégios sysadmin nessa conta para o servidor que está a executar o SQL Server. Definir NT AUTHORITY\SYSTEM *sysadmin* no servidor que está a executar o SQL Server antes de faça uma cópia de.

Se o farm do SharePoint tem bases de dados do SQL Server configuradas com aliases do SQL Server, instale os componentes de cliente do SQL Server no servidor Web front-end que o DPM vai proteger.

### <a name="sharepoint-server"></a>SharePoint Server
Enquanto o desempenho depende de vários fatores, tais como o tamanho do farm do SharePoint, um servidor do DPM pode proteger um farm do SharePoint 25 TB como orientação geral.

### <a name="dpm-update-rollup-5"></a>Update Rollup 5 do DPM
Para iniciar a proteção de um farm do SharePoint para o Azure, terá de instalar o DPM Update Rollup 5 ou posterior. O Update Rollup 5 fornece a capacidade de proteger um farm do SharePoint para o Azure, se o farm é configurado utilizando o SQL AlwaysOn.
Para obter mais informações, consulte o blogue post apresenta [DPM Update Rollup 5](http://blogs.technet.com/b/dpm/archive/2015/02/11/update-rollup-5-for-system-center-2012-r2-data-protection-manager-is-now-available.aspx)

### <a name="whats-not-supported"></a>O que não é suportado
* O DPM protege um farm do SharePoint não protege os índices de pesquisa ou bases de dados de serviço de aplicações. Terá de configurar a proteção destas bases de dados em separado.
* O DPM não fornece cópia de segurança das bases de dados do SQL Server do SharePoint que estão alojados em partilhas de servidor (SOFS) de ficheiros de escalamento horizontal.

## <a name="configure-sharepoint-protection"></a>Configurar a proteção do SharePoint
Antes de poder utilizar o DPM para proteger o SharePoint, tem de configurar o serviço de escritor de VSS do SharePoint (serviço WSS Writer) utilizando **ConfigureSharePoint.exe**.

Pode encontrar **ConfigureSharePoint.exe** na pasta \bin [caminho de instalação do DPM] no servidor web front-end. Esta ferramenta fornece o agente de proteção com as credenciais do farm do SharePoint. Executá-la num único servidor WFE. Se tiver múltiplos servidores WFE, selecione apenas um quando configurar um grupo de proteção.

### <a name="to-configure-the-sharepoint-vss-writer-service"></a>Para configurar o serviço de escritor de VSS do SharePoint
1. No servidor WFE, na linha de comandos, aceda a \bin\. [localização de instalação do DPM]
2. Introduza o comando ConfigureSharePoint - EnableSharePointProtection.
3. Introduza as credenciais de administrador do farm. Esta conta deve ser um membro do grupo de administrador local no servidor WFE. Se o administrador do farm não for uma local admin conceda as seguintes permissões no servidor WFE:
   * Conceda WSS_Admin_WPG controlo total ao grupo para a pasta do DPM (% programa Files%\Microsoft Data Protection Manager\DPM).
   * Conceda o acesso de leitura de grupo WSS_Admin_WPG à chave de registo do DPM (HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager).

> [!NOTE]
> Terá de executar novamente o ConfigureSharePoint.exe sempre que exista uma alteração nas credenciais de administrador de farm do SharePoint.
> 
> 

## <a name="back-up-a-sharepoint-farm-by-using-dpm"></a>Fazer cópias de segurança de um farm do SharePoint utilizando o DPM
Após a configuração do DPM e o farm do SharePoint, conforme explicado anteriormente, o SharePoint pode ser protegido pelo DPM.

### <a name="to-protect-a-sharepoint-farm"></a>Para proteger um farm do SharePoint
1. Do **proteção** separador da consola do administrador do DPM, clique em **novo**.
    ![Novo separador de proteção](./media/backup-azure-backup-sharepoint/dpm-new-protection-tab.png)
2. No **selecionar tipo de grupo de proteção** página do **criar novo grupo de proteção** assistente, selecione **servidores**e, em seguida, clique em **seguinte**.
   
    ![Tipo de selecionar o grupo de proteção](./media/backup-azure-backup-sharepoint/select-protection-group-type.png)
3. No **selecionar membros do grupo** ecrã, selecione a caixa de verificação para o servidor do SharePoint que pretende proteger e clique em **seguinte**.
   
    ![Selecionar Membros do grupo](./media/backup-azure-backup-sharepoint/select-group-members2.png)
   
   > [!NOTE]
   > Com o agente DPM instalado, pode ver que o servidor no assistente. O DPM também mostra a estrutura. Uma vez que executou o ConfigureSharePoint.exe, o DPM comunica com o serviço de escritor de VSS do SharePoint e respetivas bases de dados do SQL Server correspondentes e reconhece a estrutura de farm do SharePoint, as bases de dados de conteúdo associados e quaisquer itens correspondentes.
   > 
   > 
4. No **selecionar método de proteção de dados** página, introduza o nome do **grupo de proteção**e selecione o preferencial *métodos de proteção*. Clique em **Seguinte**.
   
    ![Selecione o método de proteção de dados](./media/backup-azure-backup-sharepoint/select-data-protection-method1.png)
   
   > [!NOTE]
   > O método de proteção de disco ajuda a cumprir objetivos de tempo de recuperação de curtos. Azure é um destino de proteção de longa duração, económico em comparação comparado as bandas. Para obter mais informações, consulte [utilize Azure Backup para substituir a infraestrutura da banda](https://azure.microsoft.com/documentation/articles/backup-azure-backup-cloud-as-tape/)
   > 
   > 
5. No **especificar objetivos a curto prazo** página, selecione o preferencial **período de retenção** e identificar quando pretender que as cópias de segurança ocorrer.
   
    ![Especificar objetivos a curto prazo](./media/backup-azure-backup-sharepoint/specify-short-term-goals2.png)
   
   > [!NOTE]
   > Uma vez recuperação é frequentemente necessária para dados é inferior a cinco dias, é selecionado um período de retenção de cinco dias no disco e certificar-se de que a cópia de segurança ocorre durante o horário de não produção, para este exemplo.
   > 
   > 
6. Reveja o agrupamento de armazenamento espaço em disco atribuído para o grupo de proteção e, em seguida, clique **seguinte**.
7. Para cada grupo de proteção, o DPM atribui espaço no disco para armazenar e gerir réplicas. Neste momento, o DPM deve criar uma cópia dos dados selecionados. Selecione como e quando pretende a criação de réplica e, em seguida, clique em **seguinte**.
   
    ![Escolher método de criação de réplica](./media/backup-azure-backup-sharepoint/choose-replica-creation-method.png)
   
   > [!NOTE]
   > Para se certificar de que o tráfego de rede não é afetado, selecione uma hora fora do horário de produção.
   > 
   > 
8. O DPM assegura a integridade dos dados, efetuando as verificações de consistência na réplica. Existem duas opções disponíveis. Pode definir uma agenda para executar as verificações de consistência ou o DPM pode executar verificações de consistência automaticamente na réplica sempre que o se tornar inconsistente. Selecione a sua opção preferida e, em seguida, clique em **seguinte**.
   
    ![Verificação de consistência](./media/backup-azure-backup-sharepoint/consistency-check.png)
9. No **especificar dados da proteção Online** página, selecione o farm do SharePoint que pretende proteger e, em seguida, clique em **seguinte**.
   
    ![O DPM SharePoint Protection1](./media/backup-azure-backup-sharepoint/select-online-protection1.png)
10. No **Especificar agenda de cópia de segurança Online** página, selecione o horário da sua preferência e, em seguida, clique em **seguinte**.
    
    ![Online_backup_schedule](./media/backup-azure-backup-sharepoint/specify-online-backup-schedule.png)
    
    > [!NOTE]
    > O DPM fornece um máximo de duas cópias de segurança diárias no Azure em alturas diferentes. Cópia de segurança do Azure também pode controlar a quantidade de largura de banda WAN, que pode ser utilizada para cópias de segurança no pico e horas de ponta utilizando [limitação de rede de cópia de segurança do Azure](https://azure.microsoft.com/en-in/documentation/articles/backup-configure-vault/#enable-network-throttling).
    > 
    > 
11. Consoante a agenda de cópia de segurança que selecionou, no **especificar política de retenção Online** página, selecione a política de retenção para os pontos de cópia de segurança diárias, semanais, mensais e anuais.
    
    ![Online_retention_policy](./media/backup-azure-backup-sharepoint/specify-online-retention.png)
    
    > [!NOTE]
    > O DPM utiliza um esquema de retenção do avô-pai-filho na qual pode ser selecionada uma política de retenção diferentes para diferentes pontos de cópia de segurança.
    > 
    > 
12. Semelhante ao disco, uma réplica de ponto inicial de referência tem de ser criada no Azure. Selecione a sua opção preferida para criar uma cópia de segurança inicial para o Azure e, em seguida, clique em **seguinte**.
    
    ![Online_replica](./media/backup-azure-backup-sharepoint/online-replication.png)
13. Reveja as definições selecionadas no **resumo** página e, em seguida, clique em **criar grupo**. Verá uma mensagem de êxito depois de criar o grupo de proteção.
    
    ![Resumo](./media/backup-azure-backup-sharepoint/summary.png)

## <a name="restore-a-sharepoint-item-from-disk-by-using-dpm"></a>Restaurar um item do SharePoint do disco utilizando o DPM
No exemplo seguinte, o *SharePoint recuperar item* foi eliminado acidentalmente e precisam de ser recuperados.
![O DPM SharePoint Protection4](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. Abra o **consola de administrador do DPM**. Todos os farms do SharePoint que estão protegidos pelo DPM são apresentadas as **proteção** separador.
   
    ![O DPM SharePoint Protection3](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)
2. Para começar a recuperar o item, selecione o **recuperação** separador.
   
    ![O DPM SharePoint Protection5](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)
3. Pode pesquisar o SharePoint para *SharePoint recuperar item* utilizando uma pesquisa com base no caráter universal dentro de uma recuperação do ponto de intervalo.
   
    ![O DPM SharePoint Protection6](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)
4. Selecione o ponto de recuperação adequadas os resultados da pesquisa, faça duplo clique o item e, em seguida, selecione **recuperar**.
5. Também pode procurar através de vários pontos de recuperação e selecione uma base de dados ou um item para recuperar. Selecione **data > hora da recuperação**e, em seguida, selecione o correto **base de dados > farm do SharePoint > ponto de recuperação > Item**.
   
    ![O DPM SharePoint Protection7](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)
6. O item com o botão direito e, em seguida, selecione **recuperar** para abrir o **Assistente de recuperação**. Clique em **Seguinte**.
   
    ![Rever seleção de recuperação](./media/backup-azure-backup-sharepoint/review-recovery-selection.png)
7. Selecione o tipo de recuperação que pretende efetuar e, em seguida, clique em **seguinte**.
   
    ![Tipo de recuperação](./media/backup-azure-backup-sharepoint/select-recovery-type.png)
   
   > [!NOTE]
   > A seleção de **recuperar para original** no exemplo recupera o item para o site do SharePoint original.
   > 
   > 
8. Selecione o **processo de recuperação** que pretende utilizar.
   
   * Selecione **recuperar sem utilizar um farm de recuperação** se o farm do SharePoint não foi alterado e é o mesmo que o ponto de recuperação que está a ser restaurado.
   * Selecione **recuperar um farm de recuperação a utilizar** se o farm do SharePoint foi alterada desde que o ponto de recuperação foi criado.
     
     ![Processo de recuperação](./media/backup-azure-backup-sharepoint/recovery-process.png)
9. Forneça uma localização de instância do SQL Server transição recuperar temporariamente a base de dados e fornecer uma partilha de ficheiros de teste do servidor do DPM e no servidor que está a executar o SharePoint para recuperar o item.
   
    ![Transição Location1](./media/backup-azure-backup-sharepoint/staging-location1.png)
   
    O DPM anexa a base de dados de conteúdo que está a alojar o item do SharePoint para a instância do SQL Server temporário. A partir de base de dados do conteúdo, o servidor do DPM recupera o item e coloca-o na localização do ficheiro de teste no servidor do DPM. O item recuperado que se encontra agora a localização de transição do servidor DPM tem de ser exportado para a localização de transição no farm do SharePoint.
   
    ![Transição Location2](./media/backup-azure-backup-sharepoint/staging-location2.png)
10. Selecione **especificar opções de recuperação**e aplicar definições de segurança para o farm do SharePoint ou aplicar as definições de segurança dos pontos de recuperação. Clique em **Seguinte**.
    
    ![Opções de recuperação](./media/backup-azure-backup-sharepoint/recovery-options.png)
    
    > [!NOTE]
    > Pode escolher limitar a utilização de largura de banda de rede. Isto minimiza o impacto no servidor de produção durante o horário de produção.
    > 
    > 
11. Reveja as informações de resumo e, em seguida, clique em **recuperar** para iniciar a recuperação do ficheiro.
    
    ![Resumo de recuperação](./media/backup-azure-backup-sharepoint/recovery-summary.png)
12. Agora selecionar o **monitorização** separador o **consola de administrador do DPM** para ver o **estado** da recuperação.
    
    ![Estado de recuperação](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)
    
    > [!NOTE]
    > O ficheiro agora é restaurado. Pode atualizar o site do SharePoint para verificar o ficheiro restaurado.
    > 
    > 

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>Restaurar uma base de dados do SharePoint a partir do Azure utilizando o DPM
1. Para recuperar uma base de dados de conteúdo SharePoint, navegue através de vários pontos de recuperação (conforme mostrado anteriormente) e selecione o ponto de recuperação que pretende restaurar.
   
    ![O DPM SharePoint Protection8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Faça duplo clique o ponto de recuperação do SharePoint para mostrar as informações de catálogo disponíveis do SharePoint.
   
   > [!NOTE]
   > Porque o farm do SharePoint está protegido para a retenção de longa duração no Azure, não existem informações de catálogo (metadados) estão disponíveis no servidor do DPM. Como resultado, sempre que uma base de dados ponto no tempo do conteúdo do SharePoint tem de ser recuperados, terá de catálogo novamente o farm do SharePoint.
   > 
   > 
3. Clique em **recatalogação**.
   
    ![O DPM SharePoint Protection10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)
   
    O **nuvem Recatalogar** é aberta a janela de estado.
   
    ![O DPM SharePoint Protection11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)
   
    Depois de concluída a cataloging, o estado é alterado para *êxito*. Clique em **Fechar**.
   
    ![O DPM SharePoint Protection12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Clique no objeto de SharePoint mostrado no DPM **recuperação** separador para obter a estrutura de base de dados de conteúdo. O item com o botão direito e, em seguida, clique em **recuperar**.
   
    ![O DPM SharePoint Protection13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. Neste momento, siga o [passos de recuperação anteriores no artigo](#restore-a-sharepoint-item-from-disk-using-dpm) para recuperar uma base de dados de conteúdo SharePoint a partir do disco.

## <a name="faqs"></a>Perguntas mais frequentes
P: quais as versões do DPM suportam o SQL Server 2014 e SQL Server 2012 (SP2)?<br>
R: O DPM 2012 R2 com Update Rollup 4 suporta.

P: posso recuperar um item do SharePoint para a localização original se SharePoint estiver configurado utilizando o SQL Server AlwaysOn (com a proteção no disco)?<br>
R: Sim, o item pode ser recuperado para o site do SharePoint original.

P: posso recuperar uma base de dados do SharePoint para a localização original se SharePoint estiver configurado utilizando o SQL Server AlwaysOn?<br>
R: porque as bases de dados do SharePoint são configuradas no SQL Server AlwaysOn, não pode ser modificados a menos que o grupo de disponibilidade é removido. Como resultado, o DPM não é possível restaurar uma base de dados para a localização original. Pode recuperar uma base de dados do SQL Server para outra instância do SQL Server.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre o DPM proteção do SharePoint - consulte [vídeo série - DPM proteção do SharePoint](http://channel9.msdn.com/Series/Azure-Backup/Microsoft-SCDPM-Protection-of-SharePoint-1-of-2-How-to-create-a-SharePoint-Protection-Group)
* Reveja [notas de versão do System Center 2012 - Data Protection Manager](https://technet.microsoft.com/library/jj860415.aspx)
* Reveja [notas de versão para o Data Protection Manager no System Center 2012 SP1](https://technet.microsoft.com/library/jj860394.aspx)

