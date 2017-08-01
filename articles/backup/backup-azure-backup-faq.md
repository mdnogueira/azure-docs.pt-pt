
---
title: FAQ sobre o Backup do Azure | Microsoft Docs
description: "Respostas a perguntas comuns sobre: funcionalidades do Azure Backup incluindo os cofres dos Serviços de Recuperação, que cópias de segurança podem criar, como funcionam, a encriptação e os limites. "
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "recuperação de cópia de segurança e após desastres; serviço de cópia de segurança"
ms.assetid: 1011bdd6-7a64-434f-abd7-2783436668d7
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 7/21/2017
ms.author: markgal;arunak;trinadhk;
ms.translationtype: HT
ms.sourcegitcommit: 22aa82e5cbce5b00f733f72209318c901079b665
ms.openlocfilehash: 6f204f2f0dca33e68b9853af3c94934af65ca1f9
ms.contentlocale: pt-pt
ms.lasthandoff: 07/24/2017

---
# <a name="questions-about-the-azure-backup-service"></a>Perguntas sobre o serviço Azure Backup
Este artigo tem respostas a perguntas comuns para o ajudar a compreender rapidamente os componentes do Azure Backup. Em algumas das respostas, existem ligações para os artigos que incluem informação abrangente. Pode fazer perguntas sobre o Azure Backup ao clicar em **Comentários** (à direita). Os comentários aparecem na parte inferior do artigo. É necessária uma conta Livefyre para o comentário. Também pode publicar perguntas sobre o serviço de Backup do Azure no [fórum de debate](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

Para analisar rapidamente as secções neste artigo, utilize as ligações à direita, em **Neste artigo**.


## <a name="recovery-services-vault"></a>Cofre dos serviços de recuperação

### <a name="is-there-any-limit-on-the-number-of-vaults-that-can-be-created-in-each-azure-subscription-br"></a>Existe algum limite ao número de cofres que podem ser criados em cada subscrição do Azure? <br/>
Sim. A partir de setembro de 2016, pode criar 25 cofres de Serviços de Recuperação e cópia de segurança por subscrição. Pode criar até 25 cofres de Serviços de Recuperação, por região suportada do Azure Backup, por subscrição. Se precisar de mais cofres, crie uma subscrição adicional.

### <a name="are-there-limits-on-the-number-of-serversmachines-that-can-be-registered-against-each-vault-br"></a>Existem limites no número de servidores/máquinas que podem ser registados em relação a cada cofre? <br/>
Sim, pode registar até 50 máquinas por cofre. Em máquinas virtuais de IaaS do Azure, o limite são 200 VMs por cofre. Se precisar de registar mais máquinas, crie outro cofre.

### <a name="if-my-organization-has-one-vault-how-can-i-isolate-one-servers-data-from-another-server-when-restoring-databr"></a>Se a minha organização tiver um cofre, como posso isolar os dados de um servidor de outro servidor quando restaurar os dados?<br/>
Todos os servidores registados no mesmo cofre podem recuperar os dados de cópias de segurança de outros servidores *que utilizam a mesma frase de acesso*. Se tiver servidores cujos dados de cópia de segurança pretende isolar de outros servidores na sua organização, utilize uma frase de acesso designada para esses servidores. Por exemplo, os servidores de recursos humanos podem utilizar uma frase de acesso de encriptação, os servidores de gestão de contas outra e os servidores de armazenamento uma terceira.

### <a name="can-i-migrate-my-backup-data-or-vault-between-subscriptions-br"></a>Posso "migrar" os meus dados de cópia de segurança ou o meu cofre entre subscrições? <br/>
Não. O cofre é criado um nível de subscrição e não pode ser reatribuído para outra subscrição quando for criado.

### <a name="recovery-services-vaults-are-resource-manager-based-are-backup-vaults-classic-mode-still-supported-br"></a>Os cofres dos Serviços de Recuperação baseiam-se no Resource Manager. Os cofres do Backup (modo clássico) ainda são suportados? <br/>
Todos os cofres do Backup existentes no [portal clássico](https://manage.windowsazure.com) continuam a ser suportados. No entanto, já não pode utilizar o portal clássico para implementar novos cofres do Backup. A Microsoft recomenda a utilização de cofres de Serviços de Recuperação para todas as implementações, uma vez que os melhoramentos futuros se aplicam apenas a cofres de Serviços de Recuperação. Se tentar criar um cofre do Backup no portal de clássico, será redirecionado para o [portal do Azure](https://portal.azure.com).

### <a name="can-i-migrate-a-backup-vault-to-a-recovery-services-vault-br"></a>Posso migrar um cofre da Cópia de Segurança para um cofre dos Serviços de Recuperação? <br/>
Infelizmente não, não pode migrar os conteúdos de um cofre da Cópia de Segurança para um cofre dos Serviços de Recuperação. Estamos a trabalhar no sentido de adicionar esta funcionalidade, mas não está disponível atualmente.

### <a name="i-backed-up-my-classic-vms-in-a-backup-vault-can-i-migrate-my-vms-from-classic-mode-to-resource-manager-mode-and-protect-them-in-a-recovery-services-vault"></a>Criei uma cópia de segurança das minhas VMs clássicas num cofre do Backup. Posso migrar as VMs do modo clássico para o modo Resource Manager e protegê-las num cofre dos Serviços de Recuperação?
Os pontos de recuperação das VMs clássicas nos cofres de cópias de segurança não migram diretamente para um cofre dos Serviços de Recuperação quando move as VMs do modo clássico para o modo Resource Manager. Siga estes passos para transferir as cópias de segurança das VMs:

1. No cofre do Backup, aceda ao separador **Itens Protegidos** e selecione a VM. Clique em [Parar Proteção](backup-azure-manage-vms-classic.md#stop-protecting-virtual-machines). Deixe a opção *Eliminar dados de cópia de segurança associados* **desmarcada**.
2. Migre a máquina virtual do modo clássico para o modo do Resource Manager. Certifique-se de que as informações de armazenamento e rede correspondentes à máquina virtual também são migradas para o modo do Resource Manager.
3. Crie um cofre dos Serviços de Recuperação e configure a cópia de segurança na máquina virtual migrada com a ação **Cópia de Segurança** no dashboard do cofre. Para obter informações detalhadas sobre a criação de cópias de segurança de VMs para um cofre dos Serviços de Recuperação, veja o artigo [Proteger VMs do Azure com um cofre dos Serviços de Recuperação](backup-azure-vms-first-look-arm.md).

## <a name="azure-backup-agent"></a>Agente do Backup do Azure
Está disponível uma lista detalhada de perguntas em [FAQ on Azure file-folder backup](backup-azure-file-folder-backup-faq.md) (FAQ sobre a cópia de segurança de ficheiros/pastas do Azure).

## <a name="azure-vm-backup"></a>Cópias de segurança de VMs do Azure
Está disponível uma lista detalhada de perguntas em [FAQ on Azure VM backup](backup-azure-vm-backup-faq.md) (FAQ sobre as cópias de segurança de VMs do Azure).

## <a name="back-up-vmware-servers"></a>Fazer cópia de segurança dos servidores VMware

### <a name="can-i-back-up-vmware-vcenter-servers-to-azure"></a>Posso criar cópias de segurança dos servidores VMware vCenter para o Azure?

Sim. Pode utilizar o Azure Backup Server para criar cópias de segurança VMware vCenter e ESXi para o Azure. Para obter informações sobre a versão suportada do VMware, consulte o artigo [matriz de proteção do Azure Backup Server](backup-mabs-protection-matrix.md). Para obter instruções passo a passo, consulte [utilizar Azure Backup Server para criar cópia de segurança de um servidor VMware](backup-azure-backup-server-vmware.md).


## <a name="azure-backup-server-and-system-center-data-protection-manager"></a>Azure Backup Server e System Center Data Protection Manager
### <a name="can-i-use-azure-backup-server-to-create-a-bare-metal-recovery-bmr-backup-for-a-physical-server-br"></a>Posso utilizar o Servidor do Backup do Azure para criar uma cópia de segurança de Recuperação Bare-metal (BMR) para um servidor físico? <br/>
Sim.

### <a name="can-i-register-my-dpm-server-to-multiple-vaults-br"></a>Posso registar o meu Servidor DPM para diversos cofres? <br/>
Não. Um servidor DPM ou MABS pode ser registado para apenas um cofre.

### <a name="which-version-of-system-center-data-protection-manager-is-supported-br"></a>Que versão do System Center Data Protection Manager é suportada? <br/>
Recomendamos que instale o agente do Azure Backup [mais recente](http://aka.ms/azurebackup_agent) no último update rollup (UR) do System Center Data Protection Manager (DPM). A partir de agosto de 2016, o Update Rollup 11 é a atualização mais recente.

### <a name="i-have-installed-azure-backup-agent-to-protect-my-files-and-folders-can-i-now-install-system-center-dpm-to-work-with-azure-backup-agent-to-protect-on-premises-applicationvm-workloads-to-azure-br"></a>Instalei o agente do Backup do Azure para proteger os meus ficheiros e pastas. Posso agora a instalar o System Center DPM para funcionar com o agente do Azure Backup para proteger cargas de trabalho de VM/aplicações no local para o Azure? <br/>
Para utilizar o Azure Backup com o System Center Data Protection Manager (DPM), instale o DPM primeiro e, em seguida, o agente do Azure Backup. Instalar os componentes do Azure Backup por esta ordem garante que o agente do Azure Backup funciona com o DPM. Instalar o agente do Azure Backup antes do DPM não é aconselhável nem suportado.


## <a name="how-azure-backup-works"></a>Como funciona o Azure Backup
### <a name="if-i-cancel-a-backup-job-once-it-has-started-is-the-transferred-backup-data-deleted-br"></a>Se cancelar uma tarefa de cópia de segurança depois de ser iniciada, os dados de cópia de segurança transferidos são eliminados? <br/>
Não. Todos os dados transferidos para o cofre, antes da tarefa de cópia de segurança ser cancelada, permanecem no cofre. O Backup do Azure utiliza um mecanismo de ponto de verificação para adicionar, ocasionalmente, pontos de verificação aos dados de cópia de segurança durante a cópia de segurança. Por existirem pontos de verificação nos dados de cópia de segurança, o processo de cópia de segurança seguinte pode validar a integridade dos ficheiros. A tarefa de cópia de segurança seguinte será incremental face aos dados para os quais foi criada uma cópia de segurança anteriormente. As cópias de segurança incrementais só transferem dados novos ou alterados, o que se traduz numa melhor utilização da largura de banda.

Se cancelar uma tarefa de cópia de segurança para uma VM do Azure, os dados transferidos são ignorados. A próxima tarefa de cópia de segurança transfere os dados incrementais desde a última tarefa de cópia de segurança bem-sucedida.

### <a name="are-there-limits-on-when-or-how-many-times-a-backup-job-can-be-scheduledbr"></a>Existem limites para quando ou para o número de vezes que uma tarefa de cópia de segurança pode ser agendada?<br/>
Sim. Pode executar tarefas de cópia de segurança no Windows Server ou em estações de trabalho Windows até três vezes por dia. Pode executar tarefas de cópia de segurança no System Center DPM até duas vezes por dia. Pode executar uma tarefa de cópia de segurança para as VMs do IaaS uma vez por dia. Pode utilizar a política de agendamento para o Windows Server ou a estação de trabalho Windows para especificar agendamentos diários ou semanais. Ao utilizar o System Center DPM, pode especificar agendamentos diários, semanais, mensais e anuais.

### <a name="why-is-the-size-of-the-data-transferred-to-the-recovery-services-vault-smaller-than-the-data-i-backed-upbr"></a>Por que motivo o tamanho dos dados transferido para o cofre dos Serviços de Recuperação é inferior aos dados dos quais fiz uma cópia de segurança?<br/>
 Todos os dados para os quais são criadas cópias de segurança a partir do Agente do Azure Backup, do SCDPM ou do Azure Backup Server são comprimidos e encriptados antes de serem transferidos. Uma vez aplicada a compressão e a encriptação, os dados no cofre de cópia de segurança são 30-40% mais reduzidos.

## <a name="what-can-i-back-up"></a>Do que posso fazer uma cópia de segurança
### <a name="which-operating-systems-do-azure-backup-support-br"></a>Quais os sistemas operativos suportados pelo Azure Backup? <br/>
O Azure Backup suporta a lista seguinte de sistemas operativos para criar cópias de segurança: ficheiros e pastas, e aplicações de carga de trabalho protegidas com o Azure Backup Server e o System Center Data Protection Manager (DPM).

| Sistema Operativo | Plataforma | SKU |
|:--- | --- |:--- |
| Windows 8 e SPs mais recentes |64 bits |Enterprise, Pro |
| Windows 7 e SPs mais recentes |64 bits |Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter |
| Windows 8.1 e SPs mais recentes |64 bits |Enterprise, Pro |
| Windows 10 |64 bits |Enterprise, Pro, Home |
| Windows Server 2016 |64 bits |Standard, Datacenter, Essentials |
| Windows Server 2012 R2 SPs mais recentes |64 bits |Standard, Datacenter, Foundation |
| Windows Server 2012 e SPs mais recentes |64 bits |Datacenter, Foundation, Standard |
| Windows Storage Server 2016 e SPs mais recentes |64 bits |Standard, Workgroup | 
| Windows Storage Server 2012 R2 e SPs mais recentes |64 bits |Standard, Workgroup |
| Windows Storage Server 2012 e SPs mais recentes |64 bits |Standard, Workgroup |
| Windows Server 2012 R2 SPs mais recentes |64 bits |Essencial |
| Windows Server 2008 R2 SP1 |64 bits |Standard, Enterprise, Datacenter, Foundation |
| Windows Server 2008 SP2 |64 bits |Standard, Enterprise, Datacenter, Foundation |

**Para cópias de segurança de VMs do Azure:**

* **Linux**: o Azure Backup suporta [uma lista de distribuições apoiadas pelo Azure](../virtual-machines/linux/endorsed-distros.md), exceto Core OS Linux.  Outras distribuições “Bring-Your-Own-Linux” poderão também funcionar, desde que o agente de VM esteja disponível na máquina virtual e haja suporte para Python.
* **Windows Server**: não são suportadas as versões mais antigas do que o Windows Server 2008 R2.


### <a name="is-there-a-limit-on-the-size-of-each-data-source-being-backed-up-br"></a>Existe um limite no tamanho de cada origem de dados para uma cópia de segurança? <br/>
Não existe limite relativamente à quantidade de dados para os quais podem ser criadas cópias de segurança para um cofre. O Azure Backup restringe o tamanho máximo da origem de dados. Contudo, estes limites são grandes. A partir de agosto de 2015, o tamanho máximo das origens de dados para os sistemas operativos suportados é:

| S.No | Sistema operativo | Tamanho máximo da origem de dados |
|:---:|:--- |:--- |
| 1 |Windows Server 2012 ou posterior |54 400 GB |
| 2 |Windows 8 ou posterior |54 400 GB |
| 3 |Windows Server 2008, Windows Server 2008 R2 |1700 GB |
| 4 |Windows 7 |1700 GB |

A tabela seguinte explica a forma como é determinado cada tamanho da origem de dados.

| Origem de dados | Detalhes |
|:---:|:--- |
| Volume |A quantidade de dados para a cópia de segurança a partir de único volume de um servidor ou máquina cliente |
| Máquina virtual do Hyper-V |Soma dos dados de todos os VHDs da máquina virtual para a cópia de segurança |
| Base dados do Microsoft SQL Server |Tamanho da SQL Database única para a cópia de segurança |
| Microsoft SharePoint |Soma das bases de dados de conteúdo e da configuração dentro de um farm do SharePoint para a cópia de segurança |
| Microsoft Exchange |Soma de todas as bases de dados do Exchange num servidor Exchange para a cópia de segurança |
| Estado do Sistema/BMR |Cada cópia individual da BMR ou estado do sistema da máquina para a cópia de segurança |

Nas cópias de segurança de VMs, cada VM pode ter até 16 discos de dados, cada um de tamanho inferior ou igual a 1023 GB. 

## <a name="retention-policy-and-recovery-points"></a>Pontos de recuperação e política de retenção
### <a name="is-there-a-difference-between-the-retention-policy-for-dpm-and-windows-serverclient-that-is-on-windows-server-without-dpmbr"></a>Existe alguma diferença entre a política de retenção para o DPM e o Windows Server/cliente Windows (ou seja, no Windows Server sem DPM)?<br/>
Não, o DPM e o Windows Server/cliente Windows têm políticas de retenção diárias, semanais, mensais e anuais.

### <a name="can-i-configure-my-retention-policies-selectively--ie-configure-weekly-and-daily-but-not-yearly-and-monthlybr"></a>Posso configurar as minhas políticas de retenção seletivamente – por exemplo, configurar políticas de retenção semanais e diárias, mas não anuais e mensais?<br/>
Sim, a estrutura de retenção do Backup do Azure permite-lhe ter total flexibilidade na definição da política de retenção, de acordo com os seus requisitos.

### <a name="can-i-schedule-a-backup-at-6pm-and-specify-retention-policies-at-a-different-timebr"></a>Posso "agendar uma cópia de segurança" para as 18:00 e especificar políticas de retenção noutra hora?<br/>
Não. Só podem ser aplicadas políticas de retenção em pontos de cópia de segurança. Na imagem seguinte, a política de retenção é especificada para cópias de segurança criadas às 12:00 e 18:00. <br/>

![Agendar Cópia de Segurança e Retenção](./media/backup-azure-backup-faq/Schedule.png)
<br/>

### <a name="if-a-backup-is-retained-for-a-long-duration-does-it-take-more-time-to-recover-an-older-data-point-br"></a>Se uma cópia de segurança for mantida durante um longo período de tempo, demora mais tempo para recuperar um ponto de dados mais antigo? <br/>
Não – o tempo para recuperar o ponto mais antigo ou mais recente é o mesmo. Cada ponto de recuperação funciona como um ponto completo.

### <a name="if-each-recovery-point-is-like-a-full-point-does-it-impact-the-total-billable-backup-storagebr"></a>Se cada ponto de recuperação é como um ponto completo, afeta o armazenamento de cópia de segurança faturável total?<br/>
Os produtos de ponto de retenção de longa duração típicos armazenam cópias de segurança como pontos completos. Os pontos completos são *ineficazes* ao nível do armazenamento, mas é mais rápido e mais fácil restaurá-los. As cópias incrementais são *eficientes* ao nível do armazenamento, mas necessitam que restaure uma cadeia de dados, que tem impacto no seu tempo de recuperação. A arquitetura de armazenamento do Backup do Azure dá-lhe o melhor dos dois mundos ao armazenar da melhor maneira dados para restauros rápidos e incorrer em custos de armazenamento reduzido reduzidos. Esta abordagem de armazenamento de dados garante que a largura de banda de entrada e de saída é utilizada com eficácia. A quantidade do armazenamento de dados e o tempo necessário para recuperar os dados são mantidos num mínimo. Saiba mais sobre como as [cópias de segurança incrementais](https://azure.microsoft.com/blog/microsoft-azure-backup-save-on-long-term-storage/) são eficientes.

### <a name="is-there-a-limit-on-the-number-of-recovery-points-that-can-be-createdbr"></a>Existe um limite no número de pontos de recuperação que podem ser criados?<br/>
Pode criar até 9999 pontos de recuperação por instância protegida. Uma instância protegida é um computador, servidor (físico ou virtual) ou carga de trabalho configurados para criar cópias de segurança para o Azure. Para obter mais informações, consulte as explicações de [Cópia de segurança e retenção](./backup-introduction-to-azure-backup.md#backup-and-retention), e [O que é uma instância protegida](./backup-introduction-to-azure-backup.md#what-is-a-protected-instance)?

### <a name="how-many-recoveries-can-i-perform-on-the-data-that-is-backed-up-to-azurebr"></a>Quantas recuperações posso efetuar nos dados da cópia de segurança para o Azure?<br/>
Não existe limite no número de recuperações do Backup do Azure.

### <a name="when-restoring-data-do-i-pay-for-the-egress-traffic-from-azure-br"></a>Quando restaurar os dados, pago pelo tráfego de saída do Azure? <br/>
Não. As recuperações são gratuitas e não lhe é cobrado o tráfego de saída.

## <a name="azure-backup-encryption"></a>Encriptação do Azure Backup
### <a name="is-the-data-sent-to-azure-encrypted-br"></a>Os dados enviados para o Azure são encriptados? <br/>
Sim. Os dados são encriptados na máquina SCDPM/cliente/servidor no local utilizando AES256 e os dados são enviados através de uma ligação HTTPS segura.

### <a name="is-the-backup-data-on-azure-encrypted-as-wellbr"></a>Os dados da cópia de segurança no Azure também são encriptados?<br/>
Sim. Os dados enviados para o Azure permanecem encriptados (inativos). A Microsoft não desencripta os dados da cópia de segurança em momento algum. Ao criar uma cópia de segurança de uma VM do Azure, o Azure Backup depende da encriptação da máquina virtual. Por exemplo, se a VM for encriptada com o Azure Disk Encryption ou com outra tecnologia de encriptação, o Azure Backup utiliza essa encriptação para proteger os dados.

### <a name="what-is-the-minimum-length-of-encryption-key-used-to-encrypt-backup-data-br"></a>Qual é o comprimento mínimo da chave de encriptação utilizado para encriptar os dados da cópia de segurança? <br/>
A chave de encriptação deve ter, pelo menos, 16 caracteres se estiver a utilizar o agente do Azure Backup. Nas VMs do Azure, as chaves utilizadas pelo Azure Key Vault não têm limite de comprimento. 

### <a name="what-happens-if-i-misplace-the-encryption-key-can-i-recover-the-data-or-can-microsoft-recover-the-data-br"></a>O que acontece se perder a chave de encriptação? Posso recuperar os dados (ou) pode a Microsoft recuperar os dados? <br/>
A chave utilizada para encriptar os dados da cópia de segurança está presente apenas no local do cliente. A Microsoft não mantém uma cópia no Azure e não tem qualquer acesso à chave. Se o cliente perder a chave, a Microsoft não pode recuperar os dados da cópia de segurança.

