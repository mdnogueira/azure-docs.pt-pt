---
title: "Utilizar o servidor de cópia de segurança do Azure para fazer cópias de segurança de cargas de trabalho do Azure | Microsoft Docs"
description: "Utilize o servidor de cópia de segurança do Azure para proteger ou cópia de segurança de cargas de trabalho para o portal do Azure."
services: backup
documentationcenter: 
author: PVRK
manager: shivamg
editor: 
keywords: "servidor do backup do Azure; proteger cargas de trabalho; cópia de segurança de cargas de trabalho"
ms.assetid: e7fb1907-9dc1-4ca1-8c61-50423d86540c
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/20/2017
ms.author: masaran;trinadhk;pullabhk;markgal
ms.openlocfilehash: c54468d71e0b383916e49847576a98303d659d38
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="preparing-to-back-up-workloads-using-azure-backup-server"></a>Preparar a criação de cópias de segurança de cargas de trabalho com o Azure Backup Server
> [!div class="op_single_selector"]
> * [Azure Backup Server](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
>
>

Este artigo explica como preparar o ambiente para fazer cópias de segurança de cargas de trabalho utilizando o servidor de cópia de segurança do Azure. Com o servidor de cópia de segurança do Azure, pode proteger cargas de trabalho de aplicações como VMs de Hyper-V, Microsoft SQL Server, SharePoint Server, Microsoft Exchange e clientes Windows de uma única consola.

> [!NOTE]
> Servidor de cópia de segurança do Azure agora pode proteger as VMs VMware e fornece capacidades de segurança melhorada. Instalação do produto, conforme explicado nas secções abaixo; aplica atualização 1 e o agente de cópia de segurança mais recente do Azure. Para saber mais sobre a cópia de segurança de servidores do VMware com o servidor de cópia de segurança do Azure, consulte o artigo [utilizar servidor de cópia de segurança do Azure para cópia de segurança de um servidor VMware](backup-azure-backup-server-vmware.md). Para saber mais sobre as capacidades de segurança, consulte [documentação de funcionalidades de segurança de cópia de segurança do Azure](backup-azure-security-feature.md).
>
>

Também pode proteger a infraestrutura como um cargas de trabalho do serviço (IaaS) como VMs no Azure.

> [!NOTE]
> O Azure tem dois modelos de implementação para criar e trabalhar com recursos: [Resource Manager e clássico](../azure-resource-manager/resource-manager-deployment-model.md). Este artigo fornece as informações e procedimentos para restaurar VMs implementadas utilizando o modelo do Resource Manager.
>
>

Servidor do Backup do Azure herda muito a funcionalidade de cópia de segurança da carga de trabalho do Data Protection Manager (DPM). Ligações neste artigo, a documentação do DPM para explicar alguns da funcionalidade partilhada. Embora o servidor de cópia de segurança do Azure partilha muito a mesma funcionalidade que o DPM. Servidor de cópia de segurança do Azure não fazer cópias de segurança em banda, nem é feita a integração com o System Center.

## <a name="1-choose-an-installation-platform"></a>1. Escolha uma plataforma de instalação
Para obter o servidor de cópia de segurança do Azure, cópias de segurança e em execução, o primeiro passo é configurar um servidor do Windows. O servidor pode ser no Azure ou no local.

### <a name="using-a-server-in-azure"></a>Utilizar um servidor no Azure
Ao escolher um servidor para executar o servidor de cópia de segurança do Azure, recomenda-se que começar com uma imagem de galeria do Windows Server 2012 R2 Datacenter. O artigo [criar a sua primeira máquina virtual do Windows no portal do Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), fornece um tutorial para começar a trabalhar com o recomendado da máquina virtual no Azure, mesmo se nunca tiver utilizado o Azure. Os requisitos mínimos recomendados para a máquina virtual (VM) do servidor devem ser: A2 padrão com dois núcleos e 3.5 GB de RAM.

Proteger cargas de trabalho com o servidor de cópia de segurança do Azure tem muitas nuances. O artigo [instalar o DPM como uma máquina virtual do Azure](https://technet.microsoft.com/library/jj852163.aspx), ajuda a explica estes nuances. Antes de implementar a máquina, leia este artigo completamente.

### <a name="using-an-on-premises-server"></a>Utilizar um servidor no local
Se não pretender executar o servidor de base no Azure, pode executar o servidor de uma VM de Hyper-V, uma VM de VMware ou um anfitrião físico. Os requisitos mínimos recomendados para o hardware de servidor são dois núcleos e 4 GB de RAM. Os sistemas operativos suportados estão listados na seguinte tabela:

| Sistema Operativo | Plataforma | SKU |
|:--- | --- |:--- |
| Windows Server 2012 R2 SPs mais recentes |64 bits |Standard, Datacenter, Foundation |
| Windows Server 2012 e SPs mais recentes |64 bits |Datacenter, Foundation, Standard |
| Windows Storage Server 2012 R2 e SPs mais recentes |64 bits |Standard, Workgroup |
| Windows Storage Server 2012 e SPs mais recentes |64 bits |Standard, Workgroup |

Pode eliminar a duplicados do armazenamento do DPM utiliza a eliminação de duplicados do Windows Server. Saiba mais sobre como [DPM e eliminação de duplicados](https://technet.microsoft.com/library/dn891438.aspx) funcionam em conjunto, quando implementados em VMs de Hyper-V.

> [!NOTE]
> Servidor de cópia de segurança do Azure foi concebido para ser executado num servidor dedicado e especializado. Não é possível instalar o servidor de cópia de segurança do Azure em:
> - Um computador a executar como um controlador de domínio
> - Um computador no qual a função de servidor de aplicações está instalada
> - Um computador que seja um servidor de gestão do System Center Operations Manager
> - Um computador no qual o Exchange Server está em execução
> - Um computador que seja um nó de um cluster

Sempre associe o servidor de cópia de segurança do Azure a um domínio. Se planear mover o servidor a um domínio diferente, é recomendado que associe o servidor para o novo domínio antes de instalar o servidor de cópia de segurança do Azure. Mover uma máquina do servidor de cópia de segurança do Azure existente para um novo domínio após a conclusão da implementação *não suportado*.

## <a name="2-recovery-services-vault"></a>2. Cofre dos Serviços de Recuperação
Quer enviar dados de cópia de segurança para o Azure ou mantenha-o localmente, o software tem de ser ligado ao Azure. Para ser mais específicos, a máquina do servidor de cópia de segurança do Azure tem de ser registado com um cofre de serviços de recuperação.

Para criar um cofre dos serviços de recuperação:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. No menu Hub, clique em **Procurar** e na lista de recursos, escreva **Serviços de Recuperação**. À medida que começa a escrever, a lista filtra com base na sua entrada. Clique em **Cofre dos Serviços de Recuperação**.

    ![Passo 1 da Criação de um Cofre dos Serviços de Recuperação](./media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png) <br/>

    É apresentada a lista de cofres dos serviços de recuperação.
3. No menu **Cofres dos Serviços de Recuperação**, clique em **Adicionar**.

    ![Passo 2 da Criação do Cofre dos Serviços de Recuperação](./media/backup-azure-microsoft-azure-backup/rs-vault-menu.png)

    O painel do cofre dos Serviços de Recuperação Cofre abre-se, pedindo-lhe que forneça um **Nome**, **Subscrição**, **Grupo de recursos** e **Localização**.

    ![Passo 5 da Criação do cofre dos Serviços de Recuperação](./media/backup-azure-microsoft-azure-backup/rs-vault-attributes.png)
4. Para o **Nome**, introduza um nome amigável para identificar o cofre. O nome tem de ser exclusivo para a subscrição do Azure. Escreva um nome que contenha entre 2 e 50 carateres. Tem de começar com uma letra e pode conter apenas letras, números e hífenes.
5. Clique em **Subscrição** para ver a lista de subscrições disponíveis. Se não tiver a certeza de que subscrição utilizar, utilize a subscrição predefinida (ou sugerida). Terá várias escolhas apenas se a sua conta organizacional estiver associada a várias subscrições do Azure.
6. Clique em **Grupo de recursos** para ver a lista de Grupos de recursos disponíveis ou clique em **Novo** para criar um novo Grupo de recursos. Para mais informações mais completas sobre os grupos de Recursos, veja [Descrição geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)
7. Clique em **Localização** para selecionar a região geográfica do cofre.
8. Clique em **Criar**. Pode demorar algum tempo até que o cofre dos Serviços de Recuperação seja criado. Monitorize as notificações de estado na área superior direita no portal.
   Assim que o Cofre for criado, abre-se no portal.

### <a name="set-storage-replication"></a>Definir Replicação de Armazenamento
A opção de replicação de armazenamento permite-lhe escolher entre o armazenamento georredundante e o armazenamento localmente redundante. Por predefinição, o seu cofre tem um armazenamento georredundante. Se este cofre é o seu Cofre primário, deixe a opção de armazenamento definida para o armazenamento georredundante. Escolha armazenamento localmente redundante se pretende uma opção mais barata que não é tão durável. Leia mais sobre as opções de armazenamento [georredundante](../storage/common/storage-redundancy.md#geo-redundant-storage) e [localmente redundante](../storage/common/storage-redundancy.md#locally-redundant-storage) na [descrição geral da replicação de Armazenamento do Azure](../storage/common/storage-redundancy.md).

Para editar a definição de replicação de armazenamento:

1. Selecione o cofre para abrir o dashboard do cofre e o painel de Definições. Se o painel **Definições** não abrir, clique em **Todas as definições** no dashboard do cofre.
2. No painel **Definições**, clique em **Infraestrutura de Cópia de Segurança** > **Configuração de Cópia de Segurança** para abrir o painel **Configuração de Cópia de Segurança**. No painel **Configuração de Cópia de Segurança**, escolha a opção de replicação de armazenamento para o cofre.

    ![Lista de cofres de cópia de segurança](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

    Após escolher a opção de armazenamento para o cofre, está pronto para associar a VM com o cofre. Para começar a associação, detete e registe as máquinas virtuais do Azure.

## <a name="3-software-package"></a>3. Pacote de software
### <a name="downloading-the-software-package"></a>Transferir o pacote de software
1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Se já tiver um cofre de serviços de recuperação aberto, avance para o passo 3. Se não tiver um cofre dos Serviços de Recuperação aberto, mas está no portal do Azure, no menu Hub, clique em **Procurar**.

   * Na lista de recursos, escreva **Serviços de Recuperação**.
   * À medida que começa a escrever, irá filtrar a lista com base na sua entrada. Quando vir **Cofres dos Serviços de Recuperação**, clique no mesmo.

     ![Passo 1 da Criação de um Cofre dos Serviços de Recuperação](./media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

     É apresentada a lista dos cofres dos Serviços de Recuperação.
   * Na lista dos cofres dos Serviços de Recuperação, selecione um cofre.

     O dashboard do cofre selecionado é aberto.

     ![Abrir painel do cofre](./media/backup-azure-microsoft-azure-backup/vault-dashboard.png)
3. O **definições** painel abre-se por predefinição. Se estiver fechado, clique em **definições** para abrir o painel de definições.

    ![Abrir painel do cofre](./media/backup-azure-microsoft-azure-backup/vault-setting.png)
4. Clique em **cópia de segurança** para abrir o Assistente de introdução.

    ![Introdução de cópia de segurança](./media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

    No **introdução à cópia de segurança** painel que abre, **objetivos de cópia de segurança** serão automaticamente selecionadas.

    ![Abrir de cópia de segurança objetivos predefinido](./media/backup-azure-microsoft-azure-backup/getting-started.png)

5. No **objetivo de cópia de segurança** painel, do **onde está a carga de trabalho em execução** menu, selecione **no local**.

    ![no local e cargas de trabalho como objetivos](./media/backup-azure-microsoft-azure-backup/backup-goals-azure-backup-server.png)

    Do **que itens pretende cópia de segurança?** menu pendente, selecione as cargas de trabalho que pretende proteger com o servidor de cópia de segurança do Azure e, em seguida, clique em **OK**.

    O **introdução à cópia de segurança** assistente comutadores o **preparar a infraestrutura** opção para efetuar cópias de segurança de cargas de trabalho para o Azure.

   > [!NOTE]
   > Se apenas pretender fazer uma cópia de segurança de ficheiros e pastas, recomenda-se com o agente de cópia de segurança do Azure e seguir as orientações no artigo, [primeiras impressões: cópia de segurança ficheiros e pastas](backup-try-azure-backup-in-10-mins.md). Se pretender proteger mais de ficheiros e pastas ou estiver a planear expandir as necessidades de proteção no futuro, selecione essas cargas de trabalho.
   >
   >

    ![Introdução ao Assistente de introdução de alterações](./media/backup-azure-microsoft-azure-backup/getting-started-prep-infra.png)

6. No **preparar a infraestrutura** painel apresentada, clique em de **transferir** ligações para instalar o servidor de cópia de segurança do Azure e as credenciais do Cofre de transferência. Utilize as credenciais do cofre durante o registo do servidor de cópia de segurança do Azure para o Cofre de serviços de recuperação. As ligações remetem o utilizador para o Centro de transferências onde o pacote de software pode ser transferido.

    ![Preparar a infraestrutura de cópia de segurança do servidor do Azure](./media/backup-azure-microsoft-azure-backup/azure-backup-server-prep-infra.png)

7. Selecione todos os ficheiros e clique em **seguinte**. Transferir todos os ficheiros feitos página de transferência da cópia de segurança do Microsoft Azure e coloque todos os ficheiros na mesma pasta.

    ![1 do Centro de transferências](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    Uma vez que o tamanho de transferência dos ficheiros em conjunto > 3G, um 10 Mbps transferi-ligação pode demorar até 60 minutos para a transferência concluir.

### <a name="extracting-the-software-package"></a>Extrair o pacote de software
Depois de transferir todos os ficheiros, clique em **MicrosoftAzureBackupInstaller.exe**. Isto iniciará a **Assistente de configuração de cópia de segurança do Microsoft Azure** para extrair os ficheiros de configuração para uma localização especificada por si. Continuar com o assistente e clique em de **extrair** botão para iniciar o processo de extração.

> [!WARNING]
> É necessário, pelo menos, 4GB de espaço livre para extrair os ficheiros de configuração.
>
>

![Assistente de configuração de cópia de segurança do Microsoft Azure](./media/backup-azure-microsoft-azure-backup/extract/03.png)

Depois do processo de extração completo, selecione a caixa para iniciar a raiz extraídos *setup.exe* para começar a instalar o servidor de cópia de segurança do Microsoft Azure e clique em de **concluir** botão.

### <a name="installing-the-software-package"></a>Instalar o pacote de software
1. Clique em **cópia de segurança do Microsoft Azure** para iniciar o Assistente de configuração.

    ![Assistente de configuração de cópia de segurança do Microsoft Azure](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)
2. No ecrã de boas-vindas, clique o **seguinte** botão. Isto leva-o para o *verificações de pré-requisitos* secção. Neste ecrã, clique em **verifique** para determinar se tiverem sido cumpridos os pré-requisitos de hardware e software para o servidor de cópia de segurança do Azure. Se todos os pré-requisitos são cumpridos com êxito, verá uma mensagem a indicar que o computador cumpre os requisitos. Clique em de **seguinte** botão.

    ![Verificação do servidor de cópia de segurança do Azure - boas-vindas e pré-requisitos](./media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)
3. Servidor de cópia de segurança do Microsoft Azure requer o SQL Server Standard e o pacote de instalação do servidor de cópia de segurança do Azure integra integrados nos binários do SQL Server adequados necessários. Ao iniciar com uma nova instalação do servidor de cópia de segurança do Azure, deve escolher a opção **instalar um novo instância do SQL Server com esta configuração** e clique em de **verificar e instalar** botão. Depois dos pré-requisitos estão instalados com êxito, clique em **seguinte**.

    ![Servidor de cópia de segurança do Azure - verificação do SQL Server](./media/backup-azure-microsoft-azure-backup/sql/01.png)

    Se ocorrer uma falha com uma recomendação para reiniciar a máquina, fazê-lo e clique em **verifique novamente**.

   > [!NOTE]
   > Servidor de cópia de segurança do Azure não irá funcionar com uma instância remota do SQL Server. A instância que está a ser utilizada pelo servidor de cópia de segurança do Azure tem de ser local.
   >
   >
4. Forneça uma localização para a instalação de ficheiros do servidor de cópia de segurança do Microsoft Azure e clique em **seguinte**.

    ![PreReq2 de cópia de segurança do Microsoft Azure](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    A localização scratch é um requisito para cópia de segurança em do Azure. Certifique-se de que a localização de scratch é, pelo menos, 5% dos dados a ser planeados a cópia de segurança para a nuvem. Para a proteção de disco, discos separados tem de ser configurado depois de concluída a instalação. Para obter mais informações sobre agrupamentos de armazenamento, consulte [configurar agrupamentos de armazenamento e armazenamento em disco](https://technet.microsoft.com/library/hh758075.aspx).
5. Forneça uma palavra-passe segura para contas de utilizador locais restritas e clique em **seguinte**.

    ![PreReq2 de cópia de segurança do Microsoft Azure](./media/backup-azure-microsoft-azure-backup/security-screen.png)
6. Selecione se pretende utilizar *Microsoft Update* para procurar atualizações e clique em **seguinte**.

   > [!NOTE]
   > Recomenda-se ter o Windows Update redirecionar para o Microsoft Update oferece segurança e atualizações importantes para o Windows e outros produtos, como o servidor de cópia de segurança do Microsoft Azure.
   >
   >

    ![PreReq2 de cópia de segurança do Microsoft Azure](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)
7. Reveja o *resumo de definições* e clique em **instalar**.

    ![PreReq2 de cópia de segurança do Microsoft Azure](./media/backup-azure-microsoft-azure-backup/summary-screen.png)
8. A instalação ocorre em fases. Na primeira fase, o Microsoft Azure Recovery Services Agent está instalado no servidor. O assistente também verifica a conectividade à Internet. Se a conectividade à Internet está disponível para poder continuar com a instalação, caso contrário, é necessário fornecer os detalhes do proxy para estabelecer ligação à Internet.

    O passo seguinte consiste em configurar o Microsoft Azure Recovery Services Agent. Como parte da configuração, terá de fornecer as credenciais do cofre para registar a máquina no Cofre de serviços de recuperação. Também irá fornecer uma frase de acesso para encriptar/desencriptar os dados enviados entre o Azure e o local. Automaticamente pode gerar uma frase de acesso ou fornecer a suas próprias mínimo frase de acesso de 16 caracteres. Continue com o assistente até que o agente tiver sido configurado.

    ![PreReq2 Serer de cópia de segurança do Azure](./media/backup-azure-microsoft-azure-backup/mars/04.png)
9. Após a conclusão com êxito do registo do servidor de cópia de segurança do Microsoft Azure, o Assistente de configuração geral prossegue para a instalação e configuração do SQL Server e os componentes de servidor de cópia de segurança do Azure. Uma vez concluída a instalação de componentes do SQL Server, os componentes de servidor de cópia de segurança do Azure estão instalados.

    ![Servidor do Backup do Azure](./media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

Quando concluir o passo de instalação, os ícones de ambiente de trabalho do produto irão ter sido criados, bem como. Apenas, faça duplo clique no ícone para iniciar o produto.

### <a name="add-backup-storage"></a>Adicionar armazenamento de cópia de segurança
A primeira cópia de segurança é mantida no armazenamento ligado à máquina do servidor de cópia de segurança do Azure. Para obter mais informações sobre a adição de discos, consulte [configurar agrupamentos de armazenamento e armazenamento em disco](https://technet.microsoft.com/library/hh758075.aspx).

> [!NOTE]
> Terá de adicionar o armazenamento de cópia de segurança mesmo que se pretende enviar dados para o Azure. A arquitetura atual do servidor de cópia de segurança do Azure, o Cofre de cópia de segurança do Azure mantém o *segundo* cópia dos dados enquanto o armazenamento local contém a cópia de segurança primeiro (e obrigatória).
>
>

## <a name="4-network-connectivity"></a>4. Conectividade de rede
Servidor de cópia de segurança do Azure necessita de conectividade para o serviço de cópia de segurança do Azure para o produto a funcionar com êxito. Para validar se o computador tem conectividade para o Azure, utilize o ```Get-DPMCloudConnection``` cmdlet na consola do PowerShell de servidor de cópia de segurança do Azure. Se a saída do cmdlet for TRUE, em seguida, existe a conectividade, caso contrário, há sem conectividade.

Ao mesmo tempo, a subscrição do Azure tem de estar em bom estado de funcionamento. Para saber o estado da sua subscrição e geri-lo, inicie sessão para o [portal subscrição](https://account.windowsazure.com/Subscriptions).

Depois de saber o estado da conetividade do Azure e da subscrição do Azure, pode utilizar a tabela abaixo para descobrir o impacto sobre a funcionalidade de cópia de segurança/restauro fornecida.

| Estado de conectividade | Subscrição do Azure | Criar uma cópia de segurança do Azure | Cópia de segurança para disco | Restaurar a partir do Azure | Restaurar a partir do disco |
| --- | --- | --- | --- | --- | --- |
| Ligado |Ativa |Permitido |Permitido |Permitido |Permitido |
| Ligado |Expirou |Parada |Parada |Permitido |Permitido |
| Ligado |Desaprovisionada |Parada |Parada |Pontos de recuperação de paragem e o Azure eliminados |Parada |
| Conectividade perdida > 15 dias |Ativa |Parada |Parada |Permitido |Permitido |
| Conectividade perdida > 15 dias |Expirou |Parada |Parada |Permitido |Permitido |
| Conectividade perdida > 15 dias |Desaprovisionada |Parada |Parada |Pontos de recuperação de paragem e o Azure eliminados |Parada |

### <a name="recovering-from-loss-of-connectivity"></a>Recuperar a partir de perda de conectividade
Se tiver uma firewall ou um proxy que está a impedir o acesso ao Azure, terá de lista branca os seguintes endereços de domínio no perfil da firewall/proxy:

* www.msftncsi.com
* \*.Microsoft.com
* \*.WindowsAzure.com
* \*.microsoftonline.com
* \*.windows.net

Depois de conectividade para o Azure foi restaurada para a máquina do servidor de cópia de segurança do Azure, as operações que podem ser efetuadas são determinadas pelo Estado de subscrição do Azure. A tabela acima tem os detalhes sobre as operações permitidos depois da máquina é "ligada".

### <a name="handling-subscription-states"></a>Processamento de Estados de subscrição
É possível efetuar uma subscrição do Azure de um *expirado* ou *Deprovisioned* estado para o *Active Directory* estado. No entanto isto tem algumas implicações sobre o comportamento do produto enquanto o estado não é *Active Directory*:

* A *Deprovisioned* subscrição perde a funcionalidade para o período em que é desaprovisionada. No ativar *Active Directory*, a funcionalidade de produto de cópia de segurança/restauro é revived. Os dados de cópia de segurança no disco local também podem ser obtidos se de que foi guardado com um período de retenção suficientemente grande. No entanto, os dados de cópia de segurança no Azure são irretrievably perdidos depois da subscrição introduz o *Deprovisioned* estado.
* Um *expirado* subscrição apenas perde a funcionalidade para até que foi efetuada *Active Directory* novamente. As cópias de segurança agendadas para o período de que a subscrição foi *expirado* não será executado.

## <a name="troubleshooting"></a>Resolução de problemas
Se o servidor de cópia de segurança do Microsoft Azure falhar com erros durante a fase de configuração (ou cópia de segurança ou restauro), consulte este [documento de códigos de erro](https://support.microsoft.com/kb/3041338) para obter mais informações.
Também pode consultar [perguntas mais frequentes relacionadas com a cópia de segurança do Azure](backup-azure-backup-faq.md)

## <a name="next-steps"></a>Passos seguintes
Pode obter informações detalhadas [preparar o ambiente para o DPM](https://technet.microsoft.com/library/hh758176.aspx) no site Microsoft TechNet. Também contém informações sobre configurações suportadas em que servidor de cópia de segurança do Azure podem ser implementado e utilizado.

Pode utilizar estes artigos para obter uma compreensão mais aprofundada da proteção de carga de trabalho utilizando o servidor de cópia de segurança do Microsoft Azure.

* [Cópia de segurança do SQL Server](backup-azure-backup-sql.md)
* [Cópia de segurança do SharePoint server](backup-azure-backup-sharepoint.md)
* [Cópia de segurança do servidor alternativo](backup-azure-alternate-dpm-server.md)
