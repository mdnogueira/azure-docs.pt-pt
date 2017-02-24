
---
title: FAQ sobre o Backup do Azure | Microsoft Docs
description: "Respostas a perguntas mais frequentes sobre o serviço de cópia de segurança, o agente de cópia de segurança, a cópia de segurança e a retenção, recuperação, segurança e outras perguntas comuns sobre a Cópia de Segurança e a recuperação após desastre."
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
ms.date: 2/8/2017
ms.author: markgal;giridham;arunak;markgal;trinadhk;
translationtype: Human Translation
ms.sourcegitcommit: d842d0a7e6a99a0a0a67b7cf6c695aba16f83d8f
ms.openlocfilehash: 72cd97798df4e63da1e3d1dc167714f6033d2c86


---
# <a name="azure-backup-service--faq"></a>Serviço de Backup do Azure – FAQ
Este artigo é uma lista de perguntas mais frequentes (e as respetivas respostas) sobre o serviço de Backup do Azure. A nossa comunidade responde rapidamente e se uma pergunta é colocada frequentemente, adicionamo-la a este artigo. Normalmente, as respostas a perguntas fornecem referência ou informações de suporte. Pode fazer perguntas sobre o Azure Backup ao clicar em **Comentários** (à direita). Os comentários aparecem na parte inferior do artigo. É necessária uma conta Livefyre para o comentário. Também pode publicar perguntas sobre o serviço de Backup do Azure no [fórum de debate](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

## <a name="what-is-the-list-of-supported-operating-systems-from-which-i-can-back-up-to-azure-using-azure-backup-br"></a>Qual é a lista dos sistemas operativos suportados a partir dos quais posso fazer uma cópia de segurança no Azure através do Backup do Azure? <br/>
O Azure Backup suporta a lista seguinte de sistemas operativos para criar cópias de segurança: ficheiros e pastas e aplicações de carga de trabalho protegidas com o Azure Backup Server e o SCDPM.

| Sistema Operativo | Plataforma | SKU |
|:--- | --- |:--- |
| Windows 8 e SPs mais recentes |64 bits |Enterprise, Pro |
| Windows 7 e SPs mais recentes |64 bits |Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter |
| Windows 8.1 e SPs mais recentes |64 bits |Enterprise, Pro |
| Windows 10 |64 bits |Enterprise, Pro, Home |
| Windows Server 2016 |64 bits |Standard, Datacenter, Essentials |
| Windows Server 2012 R2 SPs mais recentes |64 bits |Standard, Datacenter, Foundation |
| Windows Server 2012 e SPs mais recentes |64 bits |Datacenter, Foundation, Standard |
| Windows Storage Server 2012 R2 e SPs mais recentes |64 bits |Standard, Workgroup |
| Windows Storage Server 2012 e SPs mais recentes |64 bits |Standard, Workgroup |
| Windows Server 2012 R2 SPs mais recentes |64 bits |Essencial |
| Windows Server 2008 R2 SP1 |64 bits |Standard, Enterprise, Datacenter, Foundation |
| Windows Server 2008 SP2 |64 bits |Standard, Enterprise, Datacenter, Foundation |

Relativamente a cópias de segurança de VMs do Azure, 

* **Linux**: o Azure Backup suporta [uma lista de distribuições apoiadas pelo Azure](../virtual-machines/virtual-machines-linux-endorsed-distros.md), exceto Core OS Linux.  Outras distribuições “Bring-Your-Own-Linux” poderão também funcionar, desde que o agente de VM esteja disponível na máquina virtual e haja suporte para Python.
* **Windows Server**: não são suportadas as versões mais antigas do que o Windows Server 2008 R2.

## <a name="where-can-i-download-the-latest-azure-backup-agent-br"></a>Onde posso transferir o agente do Backup do Azure mais recente? <br/>
Pode transferir o agente mais recente para fazer a cópia de segurança do Windows Server, do System Center DPM ou do cliente Windows [aqui](http://aka.ms/azurebackup_agent). Se pretender efetuar a cópia de segurança de uma máquina virtual, utilize o Agente da VM (que instala automaticamente a extensão correta). O Agente da VM já está presente nas máquinas virtuais criadas a partir da galeria do Azure.

## <a name="which-version-of-scdpm-server-is-supported-br"></a>Qual é a versão suportada do servidor SCDPM? <br/>
Recomendamos que instale o agente do Azure Backup [mais recente](http://aka.ms/azurebackup_agent) no último update rollup do SCDPM (UR11, em agosto de 2016)

## <a name="when-configuring-the-azure-backup-agent-i-am-prompted-to-enter-the-vault-credentials-do-vault-credentials-expire"></a>Quando configuro o agente do Azure Backup, é-me pedido que introduza as credenciais do cofre. As credenciais do cofre expiram?
Sim, as credenciais do cofre expiram após 48 horas. Se o ficheiro expirar, inicie sessão no portal do Azure e transfira os ficheiros de credenciais do cofre a partir do seu cofre.

## <a name="is-there-any-limit-on-the-number-of-vaults-that-can-be-created-in-each-azure-subscription-br"></a>Existe algum limite ao número de cofres que podem ser criados em cada subscrição do Azure? <br/>
Sim. A partir de Setembro de 2016, pode criar 25 cofres de cópia de segurança por subscrição. Pode criar até 25 cofres de Serviços de Recuperação por região suportada do Azure Backup por subscrição. Se precisar de mais cofres, crie uma subscrição adicional.

## <a name="are-there-any-limits-on-the-number-of-serversmachines-that-can-be-registered-against-each-vault-br"></a>Existem limites no número de servidores/máquinas que podem ser registados em relação a cada cofre? <br/>
Sim, pode registar até 50 máquinas por cofre. Em máquinas virtuais de IaaS do Azure, o limite são 200 VMs por cofre. Se precisar de registar mais máquinas, crie outro cofre.

## <a name="how-do-i-register-my-server-to-another-datacenterbr"></a>Como registar o meu servidor para outro datacenter?<br/>
Os dados de cópia de segurança são enviados para o datacenter do cofre no qual está registado. A forma mais fácil de alterar o datacenter é desinstalar o agente e reinstalá-lo e registá-lo num cofre novo que pertença ao datacenter pretendido.

## <a name="what-happens-if-i-rename-a-windows-server-that-is-backing-up-data-to-azurebr"></a>O que acontece se mudar o nome de um servidor do Windows que está a efetuar a cópia de segurança dos dados para o Azure?<br/>
Quando mudar o nome de um servidor, todas as cópias de segurança atualmente configuradas são paradas.
Registe o nome novo do servidor no cofre do Backup. Quando registar o nome novo no cofre, a primeira operação de cópia de segurança é uma cópia de segurança *completa*. Se precisar de recuperar dados de cópias de segurança no cofre com o nome do servidor antigo, utilize a opção [**Outro servidor**](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine) no assistente **Recuperar Dados**.

## <a name="what-types-of-drives-can-i-back-up-files-and-folders-from-br"></a>A partir de que tipos de unidades posso criar cópias de segurança de ficheiros e pastas? <br/>
Não consegue realizar uma cópia de segurança das seguintes unidades/volumes:

* Suporte de Dados Amovível: todas as origens de item de cópia de segurança devem ser comunicadas como fixas.
* Volumes de Só de Leitura: o volume tem de ser gravável para o serviço de cópia sombra de volumes (VSS) funcionar.
* Volumes Offline: o volume tem de estar online para que o VSS funcione.
* Partilha de rede: o volume tem de ser local no servidor para ser efetuada uma cópia de segurança utilizando a cópia de segurança online.
* Volumes protegidos de BitLocker: o volume tem de ser desbloqueado antes de a cópia de segurança ocorrer.
* Identificação do Sistema de Ficheiros: o NTFS é o único sistema de ficheiros suportado.

## <a name="what-file-and-folder-types-can-i-back-up-from-my-serverbr"></a>De que tipos de ficheiros e pastas posso criar cópias de segurança a partir do meu servidor?<br/>
São suportados os seguintes tipos:

* Encriptados
* Comprimidos
* Dispersos
* Comprimidos + dispersos
* Hiperligações: não suportadas, ignoradas
* Ponto de Nova Análise: não suportados, ignorados
* Encriptados + dispersos: não suportados, ignorados
* Fluxo Comprimido: não suportado, ignorado
* Fluxo Disperso: não suportado, ignorado

## <a name="whats-the-minimum-size-requirement-for-the-cache-folder-br"></a>Qual é o requisito de tamanho mínimo para a pasta da cache? <br/>
O tamanho da pasta cache determina a quantidade de dados para a cópia de segurança. A pasta cache deve ser 5% do espaço necessário para o armazenamento de dados.

## <a name="if-my-organization-has-one-vault-how-can-i-isolate-one-servers-data-from-another-server-when-restoring-databr"></a>Se a minha organização tiver um cofre, como posso isolar os dados de um servidor de outro servidor quando restaurar os dados?<br/>
Todos os servidores registados no mesmo cofre podem recuperar os dados de cópias de segurança de outros servidores *que utilizam a mesma frase de acesso*. Se tiver servidores cujos dados de cópia de segurança pretende isolar de outros servidores na sua organização, utilize uma frase de acesso designada para esses servidores. Por exemplo, os servidores de recursos humanos podem utilizar uma frase de acesso de encriptação, os servidores de gestão de contas outra e os servidores de armazenamento uma terceira.

## <a name="can-i-migrate-my-backup-data-or-vault-between-subscriptions-br"></a>Posso "migrar" os meus dados de cópia de segurança ou o meu cofre entre subscrições? <br/>
Não. O cofre é criado um nível de subscrição e não pode ser reatribuído para outra subscrição quando for criado.

## <a name="does-the-azure-backup-agent-work-on-a-server-that-uses-windows-server-2012-deduplication-br"></a>O Agente do Backup do Azure funciona num servidor que utiliza a eliminação de duplicados do Windows Server 2012? <br/>
Sim. O serviço do agente converte os dados com duplicados eliminados em dados normais quando prepara a operação de cópia de segurança. Em seguida, otimiza os dados para a cópia de segurança, encripta os dados e, em seguida, envia os dados encriptados para o serviço de cópia de segurança online.

## <a name="if-i-cancel-a-backup-job-once-it-has-started-is-the-transferred-backup-data-deleted-br"></a>Se cancelar uma tarefa de cópia de segurança depois de ser iniciada, os dados de cópia de segurança transferidos são eliminados? <br/>
Não. Todos os dados transferidos para o cofre, antes da tarefa de cópia de segurança ser cancelada, permanecem no cofre. O Backup do Azure utiliza um mecanismo de ponto de verificação para adicionar, ocasionalmente, pontos de verificação aos dados de cópia de segurança durante a cópia de segurança. Por existirem pontos de verificação nos dados de cópia de segurança, o processo de cópia de segurança seguinte pode validar a integridade dos ficheiros. A tarefa de cópia de segurança seguinte será incremental face aos dados para os quais foi criada uma cópia de segurança anteriormente. As cópias de segurança incrementais só transferem dados novos ou alterados, o que se traduz numa melhor utilização da largura de banda.

Se cancelar uma tarefa de cópia de segurança para uma VM do Azure, os dados transferidos são ignorados. A próxima tarefa de cópia de segurança transfere os dados incrementais desde a última tarefa de cópia de segurança bem-sucedida.

## <a name="why-am-i-seeing-the-warning-azure-backups-have-not-been-configured-for-this-server-even-though-i-had-scheduled-regular-backups-previously-br"></a>Por que razão estou a ver o aviso de "As Cópias de Segurança do Azure não foram configuradas para este servidor", embora tenha agendado cópias de segurança regulares anteriormente? <br/>
Este aviso ocorre quando as definições do agendamento de cópias de segurança armazenadas no servidor local não são as mesmas que as definições armazenadas no cofre de cópias de segurança. Quando o servidor ou as definições tenham sido recuperadas para um bom estado conhecido, as agendas de cópia de segurança podem perder a sincronização. Se receber este aviso, [reconfigure a política de cópia de segurança](backup-azure-manage-windows-server.md) e, em seguida, **Execute a Cópia de Segurança Agora** para ressincronizar o servidor local com o Azure.

## <a name="what-firewall-rules-should-be-configured-for-azure-backup-br"></a>Que regras de firewall devem ser configuradas para o Backup do Azure? <br/>
Para uma proteção totalmente integrada de dados no local para o Azure e da carga de trabalho para o Azure, recomenda-se que autorize a firewall a comunicar com os seguintes URLs:

* www.msftncsi.com
* \*.Microsoft.com
* \*.WindowsAzure.com
* \*.microsoftonline.com
* \*.windows.net

## <a name="can-i-install-the-azure-backup-agent-on-an-azure-vm-already-backed-by-the-azure-backup-service-using-the-vm-extension-br"></a>Pode instalar o agente do Backup do Azure numa VM do Azure cuja cópia de segurança já foi efetuada pelo serviço de Backup do Azure através da extensão da VM? <br/>
Com certeza. O Backup do Azure fornece uma cópia de segurança ao nível da VM para VMs do Azure com a extensão da VM. Para proteger ficheiros e pastas no SO Windows convidado, instale o agente do Azure Backup no SO Windows convidado.

## <a name="can-i-install-the-azure-backup-agent-on-an-azure-vm-to-back-up-files-and-folders-present-on-temporary-storage-provided-by-the-azure-vm-br"></a>Posso instalar o agente do Backup do Azure numa VM do Azure para efetuar uma cópia de segurança de ficheiros e pastas presentes no armazenamento temporário fornecido pela VM do Azure? <br/>
Sim. Instale o agente do Azure Backup no SO Windows convidado e crie uma cópia de segurança de ficheiros e pastas no armazenamento temporário. Tenha em atenção que as cópias de segurança falham depois de os dados de armazenamento temporário serem eliminados. Além disso, se os dados de armazenamento temporário tiverem sido eliminados, só pode restaurar para o armazenamento não volátil.

## <a name="i-have-installed-azure-backup-agent-to-protect-my-files-and-folders-can-i-now-install-scdpm-to-work-with-azure-backup-agent-to-protect-on-premises-applicationvm-workloads-to-azure-br"></a>Instalei o agente do Backup do Azure para proteger os meus ficheiros e pastas. Posso agora a instalar o SCDPM para funcionar com o agente do Backup do Azure para proteger cargas de trabalho de VM/aplicações no local para o Azure? <br/>
Para utilizar o Azure Backup com o System Center Data Protection Manager (DPM), instale o DPM primeiro e, em seguida, o agente do Azure Backup. Instalar os componentes do Azure Backup por esta ordem garante que o agente do Azure Backup funciona com o DPM. Instalar o agente do Azure Backup antes do DPM não é aconselhável nem suportado.

## <a name="what-is-the-length-of-file-path-that-can-be-specified-as-part-of-azure-backup-policy-using-azure-backup-agent-br"></a>Qual é o comprimento do caminho do ficheiro que pode ser especificado como parte da política do Backup do Azure com o agente do Backup do Azure? <br/>
O agente do Backup do Azure depende do NTFS. A [especificação do comprimento do caminho do ficheiro está limitada pela API do Windows](https://msdn.microsoft.com/library/aa365247.aspx#fully_qualified_vs._relative_paths). Se os ficheiros que pretende proteger têm um comprimento de caminho de ficheiro maior do que o permitido pela API do Windows, crie uma cópia de segurança da pasta principal ou da unidade de disco.  

## <a name="what-characters-are-allowed-in-file-path-of-azure-backup-policy-using-azure-backup-agent-br"></a>Que carateres são permitidos no caminho de ficheiro da política do Backupdo Azure com o agente do Backup do Azure? <br>
 O agente do Backup do Azure depende do NTFS. Permite a [carateres suportados pelo NTFS](https://msdn.microsoft.com/library/aa365247.aspx#naming_conventions) como parte da especificação do ficheiro.  

## <a name="can-i-use-azure-backup-server-to-create-a-bare-metal-recovery-bmr-backup-for-a-physical-server-br"></a>Posso utilizar o Servidor do Backup do Azure para criar uma cópia de segurança de Recuperação Bare-metal (BMR) para um servidor físico? <br/>
Sim.

## <a name="can-i-configure-the-backup-service-to-send-mail-if-a-backup-job-fails-br"></a>Pode configurar o serviço de Cópia de Segurança para enviar correio eletrónico se uma tarefa de cópia de segurança falhar? <br/>
Sim, o serviço de Cópia de Segurança tem vários alertas com base em eventos que podem ser utilizados com um script do PowerShell. Para obter uma descrição completa, consulte [Configurar notificações](backup-azure-monitor-vms.md#configure-notifications)

## <a name="is-there-a-limit-on-the-size-of-each-data-source-being-backed-up-br"></a>Existe um limite no tamanho de cada origem de dados para uma cópia de segurança? <br/>
Não existe limite relativamente à quantidade de dados para os quais podem ser criadas cópias de segurança para um cofre. O Azure Backup restringe o tamanho máximo da origem de dados. Contudo, estes limites são grandes. A partir de agosto de 2015, o tamanho máximo das origens de dados para os sistemas operativos suportados é:

| S.No | Sistema operativo | Tamanho máximo da origem de dados |
|:---:|:--- |:--- |
| 1 |Windows Server 2012 ou posterior |54&400; GB |
| 2 |Windows 8 ou posterior |54&400; GB |
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

## <a name="are-there-limits-on-the-number-of-times-a-backup-job-can-be-scheduled-per-daybr"></a>Existem limites no número de vezes que pode ser agendada uma tarefa de cópia de segurança por dia?<br/>
Sim, pode executar tarefas de cópia de segurança no Windows Server ou o cliente Windows três vezes por dia. Pode executar tarefas de cópia de segurança no System Center DPM até duas vezes por dia. Pode executar uma tarefa de cópia de segurança para as VMs do IaaS uma vez por dia.

## <a name="is-there-a-difference-between-the-scheduling-policy-for-dpm-and-windows-server-ie-on-windows-server-without-dpm-br"></a>Existe uma diferença entre a política de agendamento para o DPM e o Windows Server (ou seja, o Windows Server do DPM)? <br/>
Sim. Ao utilizar o DPM, pode especificar agendas diárias, semanais, mensais e anuais. O Windows Server (sem o DPM) permite-lhe especificar apenas agendas diárias e semanais.

## <a name="is-there-a-difference-between-the-retention-policy-for-dpm-and-windows-serverclient-ie-on-windows-server-without-dpmbr"></a>Existe alguma diferença entre a política de retenção para o DPM e o Windows Server/cliente Windows (ou seja, no Windows Server sem DPM)?<br/>
Não, o DPM e o Windows Server/cliente Windows têm políticas de retenção diárias, semanais, mensais e anuais.

## <a name="can-i-configure-my-retention-policies-selectively--ie-configure-weekly-and-daily-but-not-yearly-and-monthlybr"></a>Posso configurar as minhas políticas de retenção seletivamente – por exemplo, configurar políticas de retenção semanais e diárias, mas não anuais e mensais?<br/>
Sim, a estrutura de retenção do Backup do Azure permite-lhe ter total flexibilidade na definição da política de retenção, de acordo com os seus requisitos.

## <a name="can-i-schedule-a-backup-at-6pm-and-specify-retention-policies-at-a-different-timebr"></a>Posso "agendar uma cópia de segurança" para as 18:00 e especificar "políticas de retenção" noutra hora?<br/>
Não. Só podem ser aplicadas políticas de retenção em pontos de cópia de segurança. Na imagem seguinte, a política de retenção é especificada para cópias de segurança criadas às 12:00 e 18:00. <br/>

![Agendar Cópia de Segurança e Retenção](./media/backup-azure-backup-faq/Schedule.png)
<br/>

## <a name="is-an-incremental-copy-transferred-for-the-retention-policies-scheduled-br"></a>Está agendada uma cópia incremental transferida para as políticas de retenção? <br/>
Não, a cópia incremental é enviada com base na hora referida na página da agenda da cópia de segurança. Os pontos que podem ser mantidos são determinados com base na política de retenção.

## <a name="if-a-backup-is-retained-for-a-long-duration-does-it-take-more-time-to-recover-an-older-data-point-br"></a>Se uma cópia de segurança for mantida durante um longo período de tempo, demora mais tempo para recuperar um ponto de dados mais antigo? <br/>
 Não – o tempo para recuperar o ponto mais antigo ou mais recente é o mesmo. Cada ponto de recuperação funciona como um ponto completo.

## <a name="if-each-recovery-point-is-like-a-full-point-does-it-impact-the-total-billable-backup-storagebr"></a>Se cada ponto de recuperação é como um ponto completo, afeta o armazenamento de cópia de segurança faturável total?<br/>
Os produtos de ponto de retenção de longa duração típicos armazenam cópias de segurança como pontos completos. Os pontos completos são *ineficazes* ao nível do armazenamento, mas é mais rápido e mais fácil restaurá-los. As cópias incrementais são *eficientes* ao nível do armazenamento, mas necessitam que restaure uma cadeia de dados, que tem impacto no seu tempo de recuperação. A arquitetura de armazenamento do Backup do Azure dá-lhe o melhor dos dois mundos ao armazenar da melhor maneira dados para restauros rápidos e incorrer em custos de armazenamento reduzido reduzidos. Esta abordagem de armazenamento de dados garante que a largura de banda de entrada e de saída é utilizada com eficácia. A quantidade do armazenamento de dados e o tempo necessário para recuperar os dados são mantidos num mínimo. Saiba mais sobre como as [cópias de segurança incrementais](https://azure.microsoft.com/blog/microsoft-azure-backup-save-on-long-term-storage/) são eficientes.

## <a name="is-there-a-limit-on-the-number-of-recovery-points-that-can-be-createdbr"></a>Existe um limite no número de pontos de recuperação que podem ser criados?<br/>
Pode criar até 9999 pontos de recuperação por instância protegida. Uma instância protegida é um computador, servidor (físico ou virtual) ou carga de trabalho configurados para criar cópias de segurança para o Azure. Não existe limite no número de instâncias protegidas por cofre de cópia de segurança. Para obter mais informações, consulte as explicações de [Cópia de segurança e retenção](./backup-introduction-to-azure-backup.md#backup-and-retention), e [O que é uma instância protegida](./backup-introduction-to-azure-backup.md#what-is-a-protected-instance)?

## <a name="why-is-the-amount-of-data-transferred-in-backup-not-equal-to-the-amount-of-data-i-backed-upbr"></a>Por que motivo é a quantidade de dados transferidos na cópia de segurança diferente da quantidade de dados da minha cópia de segurança?<br/>
 Todos os dados para os quais são criadas cópias de segurança a partir do Agente do Azure Backup, do SCDPM ou do Azure Backup Server são comprimidos e encriptados antes de serem transferidos. Uma vez aplicada a compressão e a encriptação, os dados no cofre de cópia de segurança são 30-40% mais reduzidos.

## <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-by-the-backup-servicebr"></a>Existe alguma forma de ajustar a quantidade de largura de banda utilizada pelo serviço de Cópia de Segurança?<br/>
 Sim, utilize a opção **Alterar Propriedades** no Agente do Backup para ajustar a largura de banda. Pode ajustar a quantidade de largura de banda e as horas em que a utiliza. Para obter instruções passo a passo, veja **[Enable network throttling (Ativar a limitação de rede)](backup-configure-vault.md#enable-network-throttling)** no artigo Back up a Windows Server or client to Azure using the Resource Manager deployment model (Criar uma cópia de segurança do Windows Server ou de um cliente para o Azure com o modelo de implementação do Resource Manager).

## <a name="my-internet-bandwidth-is-limited-for-the-amount-of-data-i-need-to-back-up-is-there-a-way-i-can-move-data-to-a-certain-location-with-a-large-network-pipe-and-push-that-data-into-azure-br"></a>A minha largura de banda da Internet está limitada à quantidade de dados de que preciso para a cópia de segurança. Existe alguma forma de mover dados para uma determinada localização com um encaminhamento de rede grande e enviar esses dados para o Azure? <br/>
Pode criar cópias de segurança de dados no Azure através do processo de cópia de segurança online standard ou pode utilizar o serviço Importar/Exportar do Azure para transferir dados para o armazenamento de blobs no Azure. Não existem formas adicionais de obter a data da cópia de segurança para o armazenamento do Azure. Para obter informações sobre como utilizar o serviço Importar/Exportar do Azure com o Backup do Azure, consulte o artigo [Offline Backup workflow (Fluxo de trabalho da Cópia de Segurança Offline)](backup-azure-backup-import-export.md).

## <a name="how-many-recoveries-can-i-perform-on-the-data-that-is-backed-up-to-azurebr"></a>Quantas recuperações posso efetuar nos dados da cópia de segurança para o Azure?<br/>
Não existe limite no número de recuperações do Backup do Azure.

## <a name="do-i-have-to-pay-for-the-egress-traffic-from-azure-data-center-during-recoveriesbr"></a>É necessário pagar o tráfego de saída do datacenter do Azure durante as recuperações?<br/>
 Não. As recuperações são gratuitas e não lhe é cobrado o tráfego de saída.

## <a name="is-the-data-sent-to-azure-encrypted-br"></a>Os dados enviados para o Azure são encriptados? <br/>
Sim. Os dados são encriptados na máquina SCDPM/cliente/servidor no local utilizando AES256 e os dados são enviados através de uma ligação HTTPS segura.

## <a name="is-the-backup-data-on-azure-encrypted-as-wellbr"></a>Os dados da cópia de segurança no Azure também são encriptados?<br/>
 Sim. Os dados enviados para o Azure permanecem encriptados (inativos). A Microsoft não desencripta os dados da cópia de segurança em momento algum. Ao criar uma cópia de segurança de uma VM do Azure, o Azure Backup depende da encriptação da máquina virtual. Por exemplo, se a VM for encriptada com o Azure Disk Encryption ou com outra tecnologia de encriptação, o Azure Backup utiliza essa encriptação para proteger os dados.

## <a name="what-is-the-minimum-length-of-encryption-key-used-to-encrypt-backup-data-br"></a>Qual é o comprimento mínimo da chave de encriptação utilizado para encriptar os dados da cópia de segurança? <br/>
 A chave de encriptação deve ter pelo menos 16 carateres.

## <a name="what-happens-if-i-misplace-the-encryption-key-can-i-recover-the-data-or-can-microsoft-recover-the-data-br"></a>O que acontece se perder a chave de encriptação? Posso recuperar os dados (ou) pode a Microsoft recuperar os dados? <br/>
A chave utilizada para encriptar os dados da cópia de segurança está presente apenas no local do cliente. A Microsoft não mantém uma cópia no Azure e não tem qualquer acesso à chave. Se o cliente perder a chave, a Microsoft não pode recuperar os dados da cópia de segurança.

## <a name="how-do-i-change-the-cache-location-specified-for-the-azure-backup-agentbr"></a>Como posso alterar a localização da cache especificada para o agente do Backup do Azure?<br/>
 Percorra sequencialmente a lista com marcas seguinte para alterar a localização da cache.

* Pare o motor de Cópia de Segurança executando o seguinte comando numa linha de comandos elevada:

  ```PS C:\> Net stop obengine```
* Não mova os ficheiros. Em vez disso, copie a pasta do espaço na cache para outra unidade com espaço suficiente. O espaço da cache original pode ser removido após confirmar que as cópias de segurança estão a trabalhar com o novo espaço da cache.
* Atualize as seguintes entradas de registo com o caminho para a nova pasta de espaço da cache.<br/>

| Caminho do registo | Chave do Registo | Valor |
| --- | --- | --- |
| `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Nova localização da pasta cache* |
| `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Nova localização da pasta cache* |

* Reinicie o motor do Backup com o comando seguinte numa linha de comandos elevada:

  ```PS C:\> Net start obengine```

  Assim que a criação da cópia de segurança tiver sido concluída com êxito na nova localização da cache, pode remover a pasta cache original.

## <a name="where-can-i-put-the-cache-folder-for-the-azure-backup-agent-to-work-as-expectedbr"></a>Onde posso colocar a pasta cache para o Agente do Backup do Azure para funcionar como esperado?<br/>
As seguintes localizações para a pasta cache não são recomendadas:

* Suporte de Dados Amovível ou Partilha de Rede: a pasta cache tem de ser local no servidor que necessita da cópia de segurança utilizando a cópia de segurança online. As localizações de rede ou suportes de dados amovíveis como unidades USB não são suportadas.
* Volumes Offline: a pasta cache tem de estar online para a cópia de segurança esperada utilizando o Agente do Backup.

## <a name="are-there-any-attributes-of-the-cache-folder-that-are-not-supportedbr"></a>Existem atributos da pasta cache que não são suportados?<br/>
 Não são suportados os seguintes atributos ou as respetivas combinações para a pasta cache:

* Encriptados
* Duplicados eliminados
* Comprimidos
* Dispersos
* Ponto de Reanálise

Nem a pasta de cache nem o VHD de metadados têm os atributos necessários para o agente do Azure Backup.

## <a name="recovery-services-vaults-are-resource-manager-based-are-backup-vaults-classic-mode-still-supported-br"></a>Os cofres dos Serviços de Recuperação baseiam-se no Resource Manager. Os cofres do Backup (modo clássico) ainda são suportados? <br/>
Sim, os cofres da Cópia de Segurança ainda são suportados. Crie cofres da Cópia de Segurança no [Portal Clássico](https://manage.windowsazure.com). Crie cofres dos Serviços de Recuperação no [portal do Azure](https://portal.azure.com). Recomendamos vivamente que crie cofres dos Serviços de Recuperação, dado que todas as futuras melhorias serão apenas para os cofres dos Serviços de Recuperação.

## <a name="can-i-migrate-a-backup-vault-to-a-recovery-services-vault-br"></a>Posso migrar um cofre da Cópia de Segurança para um cofre dos Serviços de Recuperação? <br/>
Infelizmente não, não pode migrar os conteúdos de um cofre da Cópia de Segurança para um cofre dos Serviços de Recuperação. Estamos a trabalhar no sentido de adicionar esta funcionalidade, mas não está disponível atualmente.

## <a name="do-recovery-services-vaults-support-classic-vms-or-resource-manager-based-vms-br"></a>Os cofres dos Serviços de Recuperação suportam VMs clássicas ou VMs no Resource Manager? <br/>
Os cofres dos Serviços de Recuperação suportam ambos os modelos.  Pode criar uma cópia de segurança de uma VM clássica (criada no Portal clássico) ou de uma VM do Resource Manager (criada no portal do Azure) num cofre dos Serviços de Recuperação.

## <a name="i-have-backed-up-my-classic-vms-in-a-backup-vault-can-i-migrate-my-vms-from-classic-mode-to-resource-manager-mode-and-protect-them-in-a-recovery-services-vault"></a>Criei uma cópia de segurança das minhas VMs clássicas num cofre de cópia de segurança. Posso migrar as VMs do modo clássico para o modo Resource Manager e protegê-las num cofre dos Serviços de Recuperação?
Os pontos de recuperação das VMs clássicas nos cofres de cópias de segurança não migram diretamente para um cofre dos Serviços de Recuperação quando move as VMs do modo clássico para o modo Resource Manager. Siga estes passos para transferir as cópias de segurança das VMs:

1. No cofre de cópias de segurança, aceda ao separador **Itens Protegidos** e selecione a VM. Clique em [Parar Proteção](backup-azure-manage-vms-classic.md#stop-protecting-virtual-machines). Deixe a opção *Eliminar dados de cópia de segurança associados* **desmarcada**.
2. Migre a máquina virtual do modo clássico para o modo do Resource Manager. Certifique-se de que o armazenamento e a rede correspondentes à máquina virtual também são migrados para o modo do Resource Manager.
3. Crie um cofre dos Serviços de Recuperação e configure a cópia de segurança na máquina virtual migrada com a ação **Cópia de Segurança** no dashboard do cofre. Para obter informações detalhadas sobre a criação de cópias de segurança de VMs para um cofre dos Serviços de Recuperação, veja o artigo [Proteger VMs do Azure com um cofre dos Serviços de Recuperação](backup-azure-vms-first-look-arm.md).



<!--HONumber=Feb17_HO2-->


