---
title: "Descrição geral do Operations Management Suite (OMS) | Microsoft Docs"
description: "O Microsoft Operations Management Suite (OMS) é a solução de gestão de TI baseada na nuvem da Microsoft que o ajuda a gerir e a proteger a sua infraestrutura no local e na nuvem.  Este artigo descreve o valor do OMS, identifica os diferentes serviços e ofertas incluídos e disponibiliza ligações para os conteúdos detalhados desses serviços e ofertas."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 9dc437b9-e83c-45da-917c-cb4f4d8d6333
ms.service: operations-management-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2017
ms.author: bwren
ms.openlocfilehash: 452dd602387db6db04ca87f6834c9e8606185484
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-operations-management-suite-oms"></a>O que é o Operations Management Suite (OMS)?
Este artigo disponibiliza uma introdução ao Operations Management Suite (OMS), incluindo uma breve descrição geral do valor comercial que proporciona, os serviços e soluções de gestão que inclui e as ofertas que englobam diferentes serviços e soluções.  Estão incluídas ligações para a documentação detalhada sobre a implementação e utilização de cada serviço e solução.

## <a name="from-on-premises-to-the-cloud"></a>Do local para a cloud
A Microsoft disponibiliza há muito tempo produtos para gerir ambientes empresariais.  Em 2007, muitos produtos foram consolidados no conjunto de produtos de gestão System Center.  Este conjunto incluía o Configuration Manager, que proporciona funcionalidades como a distribuição e inventário de software, o Operations Manager, que oferece monitorização pró-ativa de sistemas e aplicações, o Orchestrator, que contém runbooks para automatizar processos manuais, e o Data Protection Manager, para criar cópias de segurança e recuperar dados críticos.

Com mais recursos de computação a migrar para a cloud, o os produtos do System Center ganharam mais funcionalidades baseadas na cloud, como o Operations Manager e o Orchestrator, para gerir recursos no Azure.  Contudo, ainda eram essencialmente concebidos como soluções no local e exigiam um investimento significativo na implementação e manutenção do ambiente de gestão no local.  Para tirar todo o proveito da cloud e suportar aplicações futuras, era necessária uma nova abordagem à gestão.

## <a name="introducing-operations-management-suite"></a>Introdução ao Operations Management Suite
O Operations Management Suite (também conhecido como OMS) é uma coleção de serviços de gestão concebidos desde o início para a cloud.  Em vez de implementar e gerir recursos no local, os componentes do OMS estão totalmente alojados no Azure.  A configuração é mínima e pode começar a utilizá-lo numa questão de minutos.  

- **Custo e complexidade da implementação mínimos.**  Uma vez que todos os componentes e os dados do OMS estão alojados no Azure, pode começar a utilizar os serviços em pouco tempo, sem a complexidade e o investimento em componentes no local.
- **Dimensionar para níveis da cloud.**  Não tem de se preocupar em pagar recursos de computação de que não precisa, nem em ficar sem espaço de armazenamento, pois, com a cloud, pode pagar apenas pelo que efetivamente utilizar e dimensionar de imediato para qualquer carga necessária.  Pode começar por gerir alguns recursos e, em seguida, aumentar verticalmente para todo o ambiente.
- **Tirar partido das funcionalidades mais recentes.**  São constantemente adicionadas e atualizadas as funcionalidades dos serviços do OMS.  Tem acesso contínuo às mais recentes funcionalidades sem ter de implementar atualizações.
- **Serviços integrados.**  Embora todos os serviços do OMS proporcionem valor significativo só por si, podem funcionar em conjunto para resolver cenários de gestão complexos.  Por exemplo, um runbook da Automatização do Azure pode originar um processo de ativação pós-falha com o Azure Site Recovery e, em seguida, registar informações no Log Analytics, para gerar um alerta.
- **Conhecimento global.**  As soluções de gestão do OMS têm acesso permanente às informações mais recentes.  Por exemplo, a solução Segurança e Auditoria pode fazer análises de segurança, mediante a utilização das últimas ameaças detetadas em todo o mundo.
- **Acesso em qualquer local.**  Aceda ao seu ambiente de gestão em qualquer local onde tenha um browser.  Instale a aplicação OMS no seu smartphone para ter acesso imediato aos seus dados de monitorização.

### <a name="is-it-just-for-the-cloud"></a>É apenas para a cloud?
Lá porque os serviços do OMS são executados na cloud, não significa que não consigam gerir eficazmente o seu ambiente no local.  Coloque um agente em qualquer computador Windows ou Linux no seu datacenter e este enviará dados ao Log Analytics, onde podem ser analisados, juntamente com todos os outros dados recolhidos pelos serviços cloud ou no local.  Utilize o Azure Backup e o Azure Site Recovery para tirar partido da cloud para criar cópias de segurança e proporcionar elevada disponibilidade aos recursos no local.  
Normalmente, os runbooks na cloud não podem aceder aos recursos no local, mas pode também instalar um agente num ou mais computadores que alojarão os runbooks no seu datacenter.  Quando inicia um runbook, basta especificar se quer que seja executado na cloud ou numa função de trabalho.

## <a name="hybrid-management-with-system-center"></a>Gestão híbrida com o System Center
Se tiver uma instalação existente do System Center, pode integrar esses componentes nos serviços do OMS, para oferecer uma solução híbrida aos seus ambientes no local e na cloud, beneficiando das especialidades relativas de cada produto.  Ligue o seu grupo de gestão existente do Operations Manager ao Log Analytics para analisar os agentes geridos na cloud.  Utilize o seu processo de cópias de segurança atual com o Data Protecion Manager para criar cópias de segurança dos dados na cloud.  


## <a name="oms-services"></a>Serviços do OMS
A funcionalidade principal do OMS é disponibilizada por um conjunto de serviços que são executados no Azure.  Cada serviço proporciona uma função de gestão específica e pode combinar serviços para alcançar cenários de gestão diferentes.

|| Serviço | Descrição |
|:--|:--|:--|
| ![Log Analytics](media/operations-management-suite-overview/icon-log-analytics.png) | Log Analytics | Monitorizar e analisar a disponibilidade e o desempenho de diferentes recursos, incluindo computadores e máquinas virtuais. |
| ![Automatização do Azure](media/operations-management-suite-overview/icon-automation.png) | Automatização | Automatizar processos manuais e impor configurações para computadores e máquinas virtuais. |
| ![Azure Backup](media/operations-management-suite-overview/icon-backup.png) | Cópia de segurança | Criar cópias de segurança e restaurar dados críticos. |
| ![Azure Site Recovery](media/operations-management-suite-overview/icon-site-recovery.png) | Site Recovery | Providenciar elevada disponibilidade para aplicações críticas. |

### <a name="log-analytics"></a>Log Analytics
O [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) oferece serviços de monitorização para o OMS, ao recolher dados de recursos geridos num repositório central.  Estes dados podem incluir dados de eventos ou de desempenho ou dados personalizados fornecidos através da API. Depois de recolhidos, os dados estão disponíveis para alertas, análises e exportação.  Este método permite-lhe consolidar os dados a partir de uma variedade de origens, de modo a que possa combinar dados dos seus serviços do Azure com o seu ambiente no local existente.  Também separa claramente a recolha dos dados das ações tomadas em relação aos mesmos, para que todas as ações estejam disponíveis para todos os tipos de dados.  

![Descrição geral do Log Analytics](media/operations-management-suite-overview/overview-log-analytics.png)

#### <a name="collecting-data"></a>Recolher daos
Existem várias formas de colocar dados no repositório, para que sejam analisados pelo Log Analytics.

- **Computadores Windows ou Linux e máquinas virtuais.**  Instale o Microsoft Monitoring Agent em computadores [Windows](../log-analytics/log-analytics-windows-agents.md) e [Linux](../log-analytics/log-analytics-linux-agents.md) e máquinas virtuais dos quais quer recolher dados.  O agente vai transferir automaticamente a partir da configuração do Log Analytics que define os dados de eventos e desempenho a recolher.  Pode utilizar o portal do Azure para instalar facilmente o agente em máquinas virtuais em execução no Azure.  Se tiver um ambiente existente do Operations Manager, pode ligar o grupo de gestão ao Log Analytics e iniciar automaticamente a recolha de dados de todos os agentes existentes.
- **Serviços do Azure.**  O Log Analytics recolhe telemetria dos [Diagnósticos do Azure e da Monitorização do Azure](../log-analytics/log-analytics-azure-storage.md) no repositório, para que possa monitorizar os recursos do Azure.
- **API do Recoletor de Dados.**  O Log Analytics tem uma [API REST para preencher dados de qualquer cliente](../log-analytics/log-analytics-data-collector-api.md).  Desta forma, pode recolher dados de aplicações de terceiros ou implementar cenários de gestão personalizados.  Um método comum é utilizar um runbook na Automatização do Azure para recolher os dados e, em seguida, utilizar a API do Recoletor de Dados para escrevê-los no repositório.

#### <a name="reporting-and-analyzing-data"></a>Analisar os dados e criar relatórios
O Log Analytics inclui uma poderosa linguagem de consultas, para extrair os dados armazenados no repositório.  Uma vez que os dados de todas as origens são armazenados como registos, pode analisar dados de várias origens numa única consulta.
  
Para além da análise ad hoc, o Log Analytics disponibiliza várias formas de analisar dados e criar relatórios a partir de consultas.

- **Vistas e dashboards.**  As [vistas](../log-analytics/log-analytics-view-designer.md) e os [dashboards](../log-analytics/log-analytics-dashboards.md) visualizam os resultados das consultas no portal.  Regra geral, as soluções de gestão incluirão vistas que analisam os dados dessas mesmas soluções.  Também pode criar as suas próprias vistas personalizadas, para analisar dados e disponibilizá-los de imediato no seu portal personalizado.
- **Exportar.**  Tem a opção de exportar os resultados de qualquer consulta, de modo a que possa analisá-los fora do Log Analytics.  Pode, inclusivamente, agendar uma exportação regular para o [Power BI](../log-analytics/log-analytics-powerbi.md), que proporciona funcionalidades de visualização e análise significativas.
- **API de Pesquisa de Registos.**  O Log Analytics tem uma [API REST para recolher dados de qualquer cliente](../log-analytics/log-analytics-log-search-api.md).  Desta forma, pode trabalhar programaticamente com dados recolhidos no repositório ou aceder aos mesmos com outra ferramenta de monitorização.

#### <a name="alerting"></a>Alertas
O Log Analytics pode [alertar pró-ativamente](../log-analytics/log-analytics-alerts.md) os utilizadores ou tomar medidas corretivas quando deteta problemas.  Tal como todas as outras análises do Log Analytics, os alertas são feitos com pesquisas de registos.  Estas pesquisas são executadas regularmente e, se os resultados corresponderem a determinados critérios, é criado um alerta.

![Alertas do Log Analytics](media/operations-management-suite-overview/overview-alerts.png)

Para além de criarem um registo de alertas no repositório do Log Analytics, os alertas podem realizar as ações seguintes:

- **Enviar e-mail.**  Enviar um e-mail pró-ativamente a notificá-lo de que foi detetado um problema.
- **Runbook.**  Um alerta do Log Analytics pode iniciar um runbook na Automatização do Azure.  Isto acontece, geralmente, para tentar corrigir o problema detetado.  O runbook pode ser iniciado na cloud, em caso de problema no Azure ou noutra cloud ou pode ser iniciado num agente local, em caso de problema num computador ou numa máquina virtual.
- **Webhook.**  Um alerta pode iniciar um webhook e transmitir-lhe dados a partir dos resultados da pesquisa de registos.  Desta forma, é possível integrar com sistemas externos, como um sistema de alertas alternativo, ou pode tentar tomar medidas corretivas em Websites externos.

### <a name="azure-automation"></a>Automatização do Azure
A [Automatização do Azure](http://azure.microsoft.com/documentation/services/automation) confere automatização de processos e gestão de configurações ao OMS.  Automatiza os processos manuais e ajuda a impor configurações para computadores e máquinas virtuais.  

#### <a name="process-automation"></a>Automatização de Processos
Para automatizar os processos manuais, a Automatização do Azure utiliza [runbooks](../automation/automation-runbook-types.md), que se baseiam em scripts do PowerShell ou no fluxo de trabalho do PowerShell.  Também inclui recursos que suportam runbooks, como variáveis que podem ser partilhadas entre vários runbooks e credenciais e ligações que lhe permitem armazenar informações encriptadas que possam ser necessárias para autenticação por parte de um runbook.
Os runbooks oferecem automatização de processos para os outros serviços do conjunto.  Uma vez que é possível aceder a cada um dos outros serviços com o PowerShell ou uma API REST, pode criar runbooks para realizar determinadas funções, como recolher dados de gestão no Log Analytics ou iniciar uma cópia de segurança com o Azure Backup.

##### <a name="accessing-resources"></a>Aceder aos recursos
Visto que os runbooks são baseados no PowerShell, podem gerir qualquer recurso que possa ser acedido com cmdlets do PowerShell.  Quando [carrega um módulo](../automation/automation-integration-modules.md) para a sua conta de Automatização, aquele fica disponível para todos os runbooks da conta. 
 
Quando os runbooks são executados na cloud, podem aceder a todos os recursos acessíveis a partir da mesma.  Estes podem ser recursos na sua subscrição do Azure, noutra cloud, como o Amazon Web Services (AWS) ou um serviço acessível através de uma API REST.  Os runbooks na cloud não são executados com credenciais, mas podem tirar partido de recursos da Automatização, como credenciais, ligações e certificados, para se autenticarem nos recursos a que acedem.

É muito provável que os recursos no seu datacenter não possam ser acedidos a partir de um runbook em execução na cloud.  Contudo, pode instalar uma ou mais [Funções de Trabalho de Runbook Híbridas](../automation/automation-hybrid-runbook-worker.md) no datacenter, para executar runbooks que exigem acesso a recursos locais.  Quando iniciar um runbook, especifica se este deve ser executado na cloud ou numa função de trabalho específica.

![Runbooks da Automatização do Azure](media/operations-management-suite-overview/overview-runbooks.png)

##### <a name="starting-a-runbook"></a>Iniciar um runbook
Os runbooks podem ser [iniciados de várias formas](../automation/automation-starting-a-runbook.md), para que possam ser incluídos em diversos cenários de gestão.  

- **Portal do Azure.**  À semelhança de outros serviços do Azure, a Automatização do Azure pode ser gerida no portal do Azure.  Para além de iniciar runbooks, pode importá-los ou criar os seus próprios.
- **Agendados.**  Pode agendar os runbooks para que se iniciem em intervalos regulares.  Desta forma, pode repetir automaticamente um processo de gestão regular ou recolher dados para o Log Analytics.
- **PowerShell e API.**  Pode iniciar runbooks e transmitir-lhes informações de parâmetros necessários através de um cmdlet do PowerShell ou da API REST da Automatização do Azure.  
- **Webhook.**  Pode ser criado um webhook para qualquer runbook que permita que aquele seja iniciado a partir de aplicações ou Websites externos.
- **Alerta do Log Analytics.**  Um alerta do Log Analytics pode iniciar automaticamente um runbook para tentar corrigir o problema identificado por esse alerta.

#### <a name="configuration-management"></a>Gestão da Configuração
O [PowerShell Desired State Configuration (DSC)](../automation/automation-dsc-overview.md) é uma plataforma de gestão do Windows PowerShell que lhe permite implementar e impor a configuração dos computadores e das máquinas virtuais.  A Automatização do Azure gere as configurações do DSC e disponibiliza um servidor pull na cloud ao qual os agentes podem aceder para obter as configurações necessárias.

![Azure Automation DSC](media/operations-management-suite-overview/overview-dsc.png)

### <a name="azure-backup-and-azure-site-recovery"></a>Azure Backup e Azure Site Recovery
O Azure Backup e o Azure Site Recovery contribuem para a continuidade de negócio e para a recuperação após desastre.  Ambos têm funcionalidades que o ajudam a garantir que as aplicações permanecem disponíveis em caso de indisponibilidade e que retomam o funcionamento normal quando os sistemas estiverem novamente online.  Tanto um, como o outro, contribuem para os objetivos de ponto de recuperação (RPO) e para os objetivos de tempo de recuperação (RTO) definidos para a sua organização. O RPO define o limite aceitável durante o qual os dados não estão disponíveis no decorrer de uma indisponibilidade, ao passo que o RTO limita o período de tempo aceitável durante o qual um serviço ou aplicação não está disponível no decorrer de uma indisponibilidade.

#### <a name="azure-backup"></a>Azure Backup
O [Azure Backup](http://azure.microsoft.com/documentation/services/backup) proporciona serviços de criação de cópias de segurança e de restauro de dados no OMS.  Protege os dados de aplicação e mantém-nos durante anos sem qualquer investimento de capital e com um mínimo de custos operacionais.  Pode criar cópias de segurança de dados a partir de servidores Windows físicos e virtuais, além de cargas de trabalho de aplicações como o SQL Server e o SharePoint.  Também pode ser utilizado pelo System Center Data Protection Manager (DPM) para replicar dados protegidos para o Azure para redundância e armazenamento de longa duração.

Os dados protegidos no Azure Backup são armazenados num cofre de cópias de segurança localizado numa região geográfica específica. Os dados são replicados na mesma região e, dependendo do tipo de cofre, também podem ser replicados para outra região, para uma maior resiliência.

A Cópia de Segurança do Azure tem três cenários fundamentais.

- **Computador Windows com agente do Azure Backup.** Crie cópias de segurança de ficheiros e pastas a partir de qualquer servidor ou cliente Windows diretamente para o seu cofre de cópias de segurança do Azure.<br><br>![Computador Windows com agente do Azure Backup](media/operations-management-suite-overview/overview-backup-01.png)
- **System Center Data Protection Manager (DPM) ou Microsoft Azure Backup Server.** Tire partido do DPM ou do Microsoft Azure Backup Server para criar cópias de segurança de ficheiros e pastas, além de cargas de trabalho de aplicações, como o SQL e o SharePoint, para o armazenamento local e, em seguida, replicar para o cofre de cópias de segurança do Azure. Suporta máquinas virtuais do Windows e do Linux no Hyper-V ou no VMware.<br><br>![System Center Data Protection Manager (DPM) ou Microsoft Azure Backup Server.](media/operations-management-suite-overview/overview-backup-02.png)
- **Extensões da Máquina Virtual do Azure.** Crie cópias de segurança de máquinas virtuais do Windows ou do Linux no Azure para o seu cofre de cópias de segurança do Azure.<br><br>![Extensões da Máquina Virtual do Azure](media/operations-management-suite-overview/overview-backup-03.png)



#### <a name="azure-site-recovery"></a>Azure Site Recovery
O [Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) proporciona continuidade de negócio ao orquestrar a replicação de computadores e máquinas virtuais no local para o Azure ou para um site secundário. Se o site primário não estiver disponível, é feita a ativação pós-falha para a localização secundária, para que os utilizadores possam continuar a trabalhar, e é feita a reativação pós-falha quando os sistemas estiverem a funcionar novamente. 

O Azure Site Recovery proporciona elevada disponibilidade para aplicações e servidores.  Contribui para a sua estratégia de continuidade do negócio e recuperação após desastre (BCDR) ao orquestrar a replicação, a ativação pós-falha e a recuperação de máquinas virtuais de Hyper-V no local, máquinas virtuais de VMware e servidores Windows/Linux físicos. Pode replicar máquinas para um centro de dados secundária ou expandir o seu centro de dados replicando-os para o Azure. O Site Recovery também fornece ativação pós-falha e recuperação simples para cargas de trabalho. Integra-se com mecanismos de recuperação após desastres, como o SQL Server AlwaysOn e fornece planos de recuperação para ativação pós-falha fácil de cargas de trabalho que estão em camadas em várias máquinas.

O Azure Site Recovery tem três cenários de replicação fundamentais.

- **Replicação de máquinas virtuais de Hyper-V.**  Se as máquinas virtuais de Hyper-V forem geridas nas nuvens VMM, pode replicar para um centro de dados secundária ou para o armazenamento do Azure. A replicação no Azure é efetuada através de uma ligação à Internet segura. A replicação para um centro de dados secundário é através da LAN.  Se as máquinas virtuais de Hyper-V não forem geridas pelo VMM, pode replicar apenas para o armazenamento do Azure. A replicação no Azure é efetuada através de uma ligação à Internet segura.<br><br>![Replicação de máquinas virtuais de Hyper-V](media/operations-management-suite-overview/overview-siterecovery-hyperv.png)
- **Replicação de máquinas virtuais de VMWare.**  Pode replicar máquinas virtuais VMware num centro de dados secundário que execute o VMware ou para o armazenamento do Azure. A replicação para o Azure pode ocorrer através de uma VPN de site para site, através do ExpressRoute do Azure ou através de uma ligação à Internet segura. Ocorre uma replicação para um centro de dados secundário através do canal de dados do InMage Scout.<br><br>![Replicação de máquinas virtuais de VMWare](media/operations-management-suite-overview/overview-siterecovery-vmware.png)
- **Replicação de servidores Windows e Linux físicos**  É possível replicar servidores físicos para um centro de dados secundário ou para o armazenamento do Azure. A replicação para o Azure pode ocorrer através de uma VPN de site para site, através do ExpressRoute do Azure ou através de uma ligação à Internet segura. Ocorre uma replicação para um centro de dados secundário através do canal de dados do InMage Scout. O Azure Site Recovery tem uma solução de OMS que apresenta algumas estatísticas, mas tem de utilizar o portal do Azure para quaisquer operações.<br><br>![Replicação de servidores físicos do Windows e Linux](media/operations-management-suite-overview/overview-siterecovery-physical.png)


O Site Recovery armazena metadados em cofres localizados numa região geográfica específica do Azure. O serviço Site Recovery não armazena dados replicados.

## <a name="management-solutions"></a>Soluções de Gestão
As [Soluções de Gestão](operations-management-suite-solutions.md) são conjuntos pré-embalados de lógica que implementam um determinado cenário de gestão que tira partido de um ou mais serviços do OMS.  Estão disponíveis diferentes soluções da Microsoft e de parceiros, que podem ser adicionados facilmente à sua subscrição do Azure para aumentar o valor acrescentado do investimento feito no OMS.  Como parceiro, pode criar as suas próprias soluções para suportar os seus serviços e aplicações e disponibilizá-las aos utilizadores através do Azure Marketplace ou de Modelos Quickstart.

Um bom exemplo de uma solução que tira proveito de vários serviços para proporcionar funcionalidade adicional é a [solução Gestão de Atualizações](oms-solution-update-management.md).  Esta solução utiliza o agente do Log Analytics para Windows e Linux, de modo a recolher informações sobre atualizações necessárias em cada agente.  Escreve estes dados no repositório do Log Analytics, onde pode analisá-los com um dashboard incluído.  Quando cria uma implementação, os runbooks na Automatização do Azure são utilizados para instalar atualizações necessárias.  Todo este processo é gerido no portal e não tem de se preocupar com os detalhes subjacentes.

![Solução](media/operations-management-suite-overview/overview-solution.png)

A maioria das soluções podem realizar uma ou mais das ações seguintes.

- Recolher informações adicionais.  O Log Analytics recolhe diversos dados a partir de clientes e serviços, incluindo dados de eventos e de desempenho.  Uma solução de gestão pode recolher informações adicionais que não estão disponíveis noutras origens de dados, utilizando, muitas vezes, runbooks da Automatização do Azure.
- Disponibilizar análises adicionais das informações recolhidas.  As soluções de gestão incluem dashboards e vistas que proporcionam análises e visualização dos dados.  Estas análises e visualizações têm ligações para pesquisas de registos predefinidas, que lhe permitem analisar os dados detalhados.  Também podem fazer análises em dados que já tenham sido recolhidos no repositório, como, por exemplo, pesquisar, em eventos de segurança, padrões que indiquem uma ameaça.
- Acrescentar funcionalidade.  Algumas soluções disponibilizadas pela Microsoft podem ter como base as capacidades dos serviços principais, para proporcionar funcionalidade adicional.  Por exemplo, o Mapa de Serviço disponibiliza a sua própria consola para descobrir componentes de aplicações e mapeia dependências de servidor e processos em tempo real.
A Microsoft e os parceiros adicionam soluções regularmente ao OMS, o que lhe permite aumentar continuamente o valor do seu investimento.  Pode procurar e instalar soluções da Microsoft através do Catálogo de Soluções no portal do OMS ou procurar e instalar soluções da Microsoft e de parceiros no Azure Marketplace no portal do Azure.  

![Galeria de Soluções](media/operations-management-suite-overview/solution-gallery.png)


## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre o [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics).
* Saiba mais sobre o [Automatização do Azure](../automation/automation-intro.md).
* Saiba mais sobre o [Azure Backup](http://azure.microsoft.com/documentation/services/backup).
* Saiba mais sobre o [Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery).
* Descubra as [soluções disponíveis](../log-analytics/log-analytics-add-solutions.md) nas diferentes ofertas do OMS. 

