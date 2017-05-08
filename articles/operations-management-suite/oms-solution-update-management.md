---
title: "Solução de Gestão de Atualizações no OMS | Microsoft Docs"
description: "Este artigo destina-se a ajudá-lo a saber como utilizar esta solução para gerir atualizações aos seus computadores Windows e Linux."
services: operations-management-suite
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: e33ce6f9-d9b0-4a03-b94e-8ddedcc595d2
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/02/2017
ms.author: magoedte
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 03a6c1f20632691c08f5de4afe74eacc6f79608e
ms.contentlocale: pt-pt
ms.lasthandoff: 05/03/2017


---
# <a name="update-management-solution-in-oms"></a>Solução de Gestão de Atualizações no OMS
A solução de Gestão de Atualizações no OMS permite-lhe gerir atualizações para os seus computadores Windows e Linux.  Pode rapidamente avaliar o estado das atualizações disponíveis em todos os computadores agente e iniciar o processo de instalação de atualizações necessárias para os servidores. 

## <a name="solution-components"></a>Componentes da solução

Os computadores geridos pelo OMS utilizam o seguinte para fazer a implementação de avaliações e atualizações: 

* Agente do OMS para Windows ou Linux
* Configuração de Estado Pretendido do PowerShell (DSC) para Linux 
* Função de Trabalho de Runbook Híbrida da Automatização 
* Microsoft Update ou Windows Server Update Services para computadores Windows

Os diagramas seguintes mostram uma vista conceptual do comportamento e do fluxo de dados com os quais a solução avalia e aplica as atualizações a todos os computadores Windows Server e Linux ligados numa área de trabalho.    

#### <a name="windows-server"></a>Windows Server
![Fluxo do processo de gestão de atualizações do Windows Server](media/oms-solution-update-management/update-mgmt-windows-updateworkflow.png)

#### <a name="linux"></a>Linux
![Fluxo do processo de gestão de atualizações do Linux](media/oms-solution-update-management/update-mgmt-linux-updateworkflow.png)

Depois de o computador realizar uma análise relativamente à conformidade da atualização, o agente do OMS reencaminha as informações em massa para o OMS. Em computadores Windows, a análise de conformidade é realizada de 12 em 12 horas, por predefinição.  Para além da agenda da análise, a análise da conformidade da atualização é iniciada ao fim de 15 minutos, se o Microsoft Monitoring Agent (MMA) for reiniciado, antes da instalação da atualização e após a instalação da mesma.  Em computadores Linux, a análise de conformidade é realizada de três em três horas por predefinição e são iniciadas ao fim de 15 minutos após o agente MMA ser reiniciado.  

Depois, as informações de conformidade são processadas e resumidas nos dashboards que estão incluídos na solução ou pesquisáveis mediante a utilização de consultas definidas pelo utilizador ou predefinidas.  A solução reporta até que ponto o computador está atualizado com base na origem com a qual está configurado para sincronizar.  Se o computador Windows estiver configurado para reportar para o WSUS, dependendo da última vez que o WSUS se sincronizou com o Microsoft Update, os resultados poderão ser diferentes face àquilo que o Microsoft Update mostra.  O mesmo se aplica aos computadores Linux que estão configurados para reportar para um repositório local versus um repositório público.   

Pode criar uma implementação agendada para implementar e instalar atualizações de software em computadores que precisam das atualizações.  As atualizações classificadas como *Opcionais* não estão incluídas no âmbito da implementação para computadores Windows, apenas as atualizações obrigatórias.  A implementação agendada define que computadores de destino vão receber as atualizações aplicáveis, ao especificar explicitamente os computadores ou ao selecionar um [grupo de computadores](../log-analytics/log-analytics-computer-groups.md) que tenha por base pesquisas de registos de um determinado conjunto de computadores.  Também pode especificar uma agenda para aprovar e designar um período de tempo durante o qual as atualizações estão autorizadas a ser instaladas.  As atualizações são instaladas por runbooks na Automatização do Azure.  Não pode ver estes runbooks, que não requerem nenhuma configuração.  Quando é criada uma Implementação de Atualização, é criada uma agenda que inicia um runbook de atualização principal num momento especificado nos computadores incluídos.  Este runbook principal inicia um runbook subordinado em cada agente que efetua a instalação das atualizações obrigatórias.       

Na data e na hora especificadas na implementação de atualização, os computadores de destino executam a implementação em paralelo.  Primeiro, é realizada uma análise para verificar se as atualizações ainda são obrigatórias, que são depois instaladas.  É importante ter em conta que, relativamente a computadores com o cliente WSUS, se as atualizações não forem aprovadas no WSUS, a implementação das atualizações irá falhar.  Os resultados das atualizações aplicadas são reencaminhados para o OMS para serem processados e resumidos nos dashboards ou mediante a pesquisa nos eventos.     

## <a name="prerequisites"></a>Pré-requisitos
* A solução suporta a realização de avaliações de atualização do Windows Server 2008 e versões posteriores e implementações de atualizações no Windows Server 2012 e versões posteriores.  Não são suportadas opções de instalação Server Core e Nano Server.
* Não são suportados os sistemas operativos cliente.  
* Os agentes do Windows têm de ser configurados para comunicar com um Windows Server Update Services (WSUS) ou de ter acesso ao Microsoft Update.  
  
    > [!NOTE]
    > O agente do Windows não pode ser gerido em simultâneo pelo System Center Configuration Manager.  
    >
* CentOS 6 (x86/x64) e 7 (x64)
* Red Hat Enterprise 6 (x86/x64) e 7 (x64)
* SUSE Linux Enterprise Server 11 (x86/x64) e 12 (x64)
* Ubuntu 12.04 LTS e mais recente x86/x64  
* Os agentes do Linux têm de ter acesso a um repositório de atualização.  

    > [!NOTE]
    > Esta solução não suporta um Agente do OSM para Linux configurado para reportar para várias áreas de trabalho do OMS.  
    > 

Para obter mais informações sobre como instalar o Agente do OMS para Linux e transferir a versão mais recente, consulte [Operations Management Suite Agent for Linux](https://github.com/microsoft/oms-agent-for-linux) (Agente do Operations Management Suite para Linux).  Para obter informações sobre como instalar o Agente do OMS para Windows, consultar [Operations Management Suite Agent for Windows](../log-analytics/log-analytics-windows-agents.md) (Agente do Operations Management Suite para Windows).  

## <a name="solution-components"></a>Componentes da solução
Esta solução consiste nos recursos seguintes que são adicionados à sua conta de Automatização e a agentes ligados diretamente ou a grupos de gestão ligados do Operations Manager. 

### <a name="management-packs"></a>Pacotes de gestão
Se o seu grupo de gestão do System Center Operations Manager estiver ligado a uma área de trabalho do OMS, são instalados os pacotes de gestão seguintes no Operations Manager.  Estes pacotes de gestão também são instalados em computadores Windows ligados diretamente após adicionar esta solução. Estes pacotes de gestão não envolvem qualquer configuração ou gestão. 

* Pacote de Informações de Avaliação de Atualização do Microsoft System Center Advisor (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Pacote de Gestão de Implementação de Atualização

Para obter mais informações sobre como são atualizados os pacotes de gestão da solução, veja [Connect Operations Manager to Log Analytics (Ligar o Operations Manager ao Log Analytics)](../log-analytics/log-analytics-om-agents.md).

### <a name="hybrid-worker-groups"></a>Grupos de Função de Trabalho Híbrida
Depois de ativar esta solução, qualquer computador Windows ligado diretamente à sua área de trabalho do OMS é configurado automaticamente como uma Função de Trabalho de Runbook Híbrida para suportar os runbooks que estão incluídos nesta solução.  Cada computador Windows gerido pela solução será listado no painel Grupos de Função de Trabalho de Runbook Híbrida da conta de Automatização, seguindo a convenção de nomenclatura *Hostname FQDN_GUID*.  Não pode segmentar estes grupos com runbooks na sua conta, caso contrário, falharão. Estes grupos destinam-se apenas a suportar a solução de gestão.   

No entanto, pode adicionar os computadores Windows a um grupo de Função de Trabalho de Runbook Híbrida na conta de Automatização para suportar runbooks de Automatização, desde que esteja a utilizar a mesma conta para a solução e a subscrição do grupo de Função de Trabalho de Runbook Híbrida.  Esta funcionalidade foi adicionada à versão 7.2.12024.0 da Função de Trabalho de Runbook Híbrida.  

## <a name="configuration"></a>Configuração
Execute os seguintes passos para adicionar a solução de Gestão de Atualizações à sua área de trabalho do OMS e confirmar que os agentes estão a reportar. Os agentes do Windows já ligados à área de trabalho são adicionados automaticamente, sem configurações adicionais. 

É possível implementar a solução utilizando os seguintes métodos:

* A partir do Azure Marketplace no portal do Azure, ao selecionar a oferta de Automatização e Controlo ou a solução Gestão de Atualizações
* A partir da Galeria de Soluções OMS na área de trabalho OMS

Se já tiver associado uma conta de Automatização a uma área de trabalho do OMS no mesmo grupo de recursos e na mesma região, selecionar Automatização e Controlo verificará a sua configuração e instalará apenas a solução e configurá-la-á em ambos os serviços.  Selecionar a solução Gestão de Atualizações no Azure Marketplace proporciona o mesmo comportamento.  Se não tiver nenhum dos serviços implementado na sua subscrição, siga os passos no painel **Criar Nova Solução** e confirme que pretende instalar as outras soluções pré-selecionadas recomendadas.  Opcionalmente, pode adicionar a solução Gestão de Atualizações à sua área de trabalho do OMS através dos passos descritos em [Add OMS solutions](../log-analytics/log-analytics-add-solutions.md) (Adicionar soluções do OMS), na Galeria de Soluções.  

### <a name="confirm-oms-agents-and-operations-manager-management-group-connected-to-oms"></a>Confirmar os agentes OMS e o grupo de gestão do Operations Manager ligados ao OMS

Para confirmar que o Agente do OMS ligado diretamente para Linux e Windows está a comunicar com o OMS, pode executar a pesquisa de registos seguinte ao fim de alguns minutos:

* Linux - `Type=Heartbeat OSType=Linux | top 500000 | dedup SourceComputerId | Sort Computer | display Table`.  

* Windows - `Type=Heartbeat OSType=Windows | top 500000 | dedup SourceComputerId | Sort Computer | display Table`

Num computador Windows, pode rever o seguinte para verificar a conectividade do agente ao OMS:

1.  Abra o Microsoft Monitoring Agent no Painel de Controlo e, no separador **Azure Log Analytics (OMS)**, o agente apresenta uma mensagem que diz: **O Microsoft Monitoring Agent ligou-se com êxito ao serviço Microsoft Operations Management Suite**.   
2.  Abra o Registo de Eventos do Windows, navegue para **Registos de Aplicações e Serviços\Operations Manager** e procure os IDs de Evento 3000 e 5002 no Conector de Serviço de origem.  Estes eventos indicam que o computador foi registado na área de trabalho do OMS e que está a receber a configuração.  

Se o agente não conseguir comunicar com o serviço OMS e estiver configurado para comunicar com a Internet através de uma firewall ou de um servidor proxy, confirme que a firewall ou o servidor proxy está devidamente configurado, revendo [Configurar definições de proxy e de firewall do Log Analytics](../log-analytics/log-analytics-proxy-firewall.md).
  
Os agentes do Linux adicionados recentemente mostrarão o estado **Atualizado** depois de ser realizada a avaliação.  Este processo pode demorar até seis horas. 

Para confirmar se um grupo de gestão do Operations Management está a comunicar com o OMS, veja [Validate Operations Manager Integration with OMS](../log-analytics/log-analytics-om-agents.md#validate-operations-manager-integration-with-oms) (Validar a Integração do Operations Manager no OMS).

## <a name="data-collection"></a>Recolha de dados
### <a name="supported-agents"></a>Agentes suportados
A tabela seguinte descreve as origens ligadas que são suportadas por esta solução.

| Origem Ligada | Suportado | Descrição |
| --- | --- | --- |
| Agentes do Windows |Sim |A solução recolhe informações sobre atualizações do sistema de agentes do Windows e inicia a instalação das atualizações necessárias. |
| Agentes do Linux |Sim |A solução recolhe informações sobre atualizações de sistema a partir dos agentes do Linux e inicia a instalação das atualizações obrigatórias em distribuições suportadas. |
| Grupo de gestão do Operations Manager |Sim |A solução recolhe informações sobre atualizações do sistema de agentes num grupo de gestão ligado.<br>Não é necessária uma ligação direta a partir do agente do Operations Manager ao Log Analytics. Os dados são reencaminhados do grupo de gestão para o repositório do OMS. |
| Conta de armazenamento do Azure |Não |O armazenamento do Azure não inclui informações sobre atualizações do sistema. |

### <a name="collection-frequency"></a>Frequência da recolha
Em cada computador Windows gerido, é feita uma análise duas vezes por dia. A cada 15 minutos, a API do Windows é chamada para consultar a hora da última atualização para determinar se o estado foi alterado e, se for esse o caso, é iniciada uma análise de conformidade.  Em cada computador Linux gerido, é feita uma análise de três em três horas. 

O dashboard pode demorar entre 30 minutos a seis horas a apresentar os dados atualizados a partir dos computadores geridos.   

## <a name="using-the-solution"></a>Utilizar a solução
Quando adiciona a solução de Gestão de Atualizações à sua área de trabalho do OMS, é adicionado o mosaico **Gestão de Atualizações** ao dashboard do OMS. Este mosaico apresenta uma contagem e uma representação gráfica do número de computadores no seu ambiente e a respetiva conformidade de atualização.<br><br>
![Mosaico de Resumo da Gestão de Atualizações](media/oms-solution-update-management/update-management-summary-tile.png)  


## <a name="viewing-update-assessments"></a>Ver avaliações de atualizações
Clique no mosaico **Gestão de Atualizações** para abrir o dashboard **Gestão de Atualizações**.<br><br> ![Dashboard de Resumo de Gestão de Atualizações](./media/oms-solution-update-management/update-management-dashboard.png)<br> 

Este dashboard mostra uma análise detalhada dos estados de atualizações categorizados por tipo de sistema operativo e classificação de atualização - crítica, segurança ou outra (como uma atualização de definição). O mosaico **Implementações de Atualização**, quando selecionado, redireciona-o para a página Implementações de Atualização, onde pode ver agendas, implementações que estão a ser executadas, implementações concluídas ou agendar implementações novas.  

Pode executar uma pesquisa de registos que devolve todos os registos, clicando no mosaico específico, ou, para executar uma consulta de uma categoria específica e com critérios predefinidos, selecione uma da lista disponível na coluna **Consultas de Atualização Comuns**.    

## <a name="installing-updates"></a>Instalar as atualizações
Depois de terem sido avaliadas as atualizações para todos os computadores Linux e Windows na sua área de trabalho, pode instar as atualizações obrigatórias ao criar uma *Implementação de Atualização*.  Uma implementação de atualização é uma instalação agendada de atualizações necessárias num ou mais computadores.  Especifique a data e a hora da implementação, bem como um computador ou grupo de computadores que devem ser incluídos no âmbito da mesma.  Para saber mais sobre grupos de computadores, veja [Computer groups in Log Analytics](../log-analytics/log-analytics-computer-groups.md) (Grupos de computadores no Log Analytics).  Se incluir grupos de computadores na sua implementação de atualizações, a adesão ao grupo é avaliada apenas uma vez no momento da criação da agenda.  As alterações subsequentes a um grupo não são refletidas.  Para contornar este problema, elimine a implementação da atualização agendada e recrie-a. 

> [!NOTE]
> Por predefinição, as VMs do Windows implementadas a partir do Azure Marketplace estão definidas para receber atualizações automáticas do Serviço Windows Update.  Este comportamento não se altera depois de adicionar esta solução ou VMs do Windows à sua área de trabalho.  Se não gerir atualizações ativamente com esta solução, será aplicado o comportamento predefinido (aplicar atualizações automaticamente).  

Para máquinas virtuais criadas a partir de imagens de Red Hat Enterprise Linux (RHEL) a pedido disponíveis no Azure Marketplace, estão registadas para aceder à [Infraestrutura de Atualização do Red Hat (RHUI)](../virtual-machines/virtual-machines-linux-update-infrastructure-redhat.md) implementada no Azure.  Qualquer outra distribuição de Linux tem de ser atualizada a partir do repositório de ficheiros online distros, de acordo com os respetivos métodos suportados.  

### <a name="viewing-update-deployments"></a>Ver as implementações de atualizações
Clique no mosaico **Implementação de Atualização** para ver a lista de implementações de atualizações existentes.  Estão agrupadas por estado – **Agendada**, **Em Execução** e **Concluída**.<br><br> ![Página de Agenda de Implementações de Atualizações](./media/oms-solution-update-management/update-updatedeployment-schedule-page.png)<br>  

As propriedades apresentadas para cada Implementação de Atualização são descritas na tabela seguinte.

| Propriedade | Descrição |
| --- | --- |
| Nome |Nome da Implementação de Atualização. |
| Agenda |Tipo de agenda.  As opções disponíveis são *Uma Vez*, *Periódica Semanal* ou *Periódica Mensal*. |
| Hora de Início |A data e a hora em que a Implementação de Atualização está agendada para ser iniciada. |
| Duração |O número de minutos durante os quais a Implementação de Atualização tem permissão para ser executada.  Se não forem instaladas durante este período todas as atualizações, as atualizações restantes têm de aguardar até à implementação de atualizações seguinte. |
| Servidores |O número de computadores afetados pela implementação de atualizações.  |
| Estado |O estado atual da Implementação de Atualizações.<br><br>Os valores possíveis são:<br>- Não Iniciada<br>- Em Execução<br>- Concluída |

Selecione uma Implementação de Atualizações para ver o ecrã de detalhes, que inclui as colunas da tabela seguinte.  Estas colunas não serão preenchidas se a Implementação de Atualizações ainda não tiver começado.<br><br> ![Descrição Geral dos Resultados da Implementação de Atualizações](./media/oms-solution-update-management/update-management-deploymentresults-dashboard.png)

| Coluna | Descrição |
| --- | --- |
| **Vista Computadores** | |
| Computadores Windows |Mostra o número de computadores Windows da Implementação de Atualizações por estado.  Clique num estado para executar uma consulta de registos que devolva todos os registos de atualizações com esse estado na Implementação de Atualizações. |
| Computadores Linux |Mostra o número de computadores Linux da Implementação de Atualizações por estado.  Clique num estado para executar uma consulta de registos que devolva todos os registos de atualizações com esse estado na Implementação de Atualizações. |
| Estado da Instalação do Computador |Apresenta os computadores envolvidos na Implementação de Atualizações e a percentagem de atualizações que foram instaladas com êxito. Clique numa das entradas para executar uma pesquisa de registos que devolva todas as atualizações críticas ou em falta. |
| **Vista Atualizações** | |
| Atualizações do Windows |Mostra as atualizações do Windows incluídas na Implementação de Atualizações e o respetivo estado de instalação por cada atualização.  Selecione uma atualização para executar uma pesquisa de registos que devolva todos os registos de atualização dessa atualização específica ou clique no estado para executar uma pesquisa de registos que devolva todos os registos de atualização da implementação. | 
| Atualizações do Linux |Mostra as atualizações do Linux incluídas na Implementação de Atualizações e o respetivo estado de instalação por cada atualização.  Selecione uma atualização para executar uma pesquisa de registos que devolva todos os registos de atualização dessa atualização específica ou clique no estado para executar uma pesquisa de registos que devolva todos os registos de atualização da implementação. | 

### <a name="creating-an-update-deployment"></a>Criar uma Implementação de Atualizações
Crie uma Implementação de Atualizações nova ao clicar no botão **Adicionar**, na parte superior do ecrã, para abrir a página **Nova Implementação de Atualizações**.  Tem de indicar valores para as propriedades da tabela seguinte.

| Propriedade | Descrição |
| --- | --- |
| Nome |O nome exclusivo para identificar a implementação de atualizações. |
| Fuso Horário |O fuso horário a utilizar na hora de início. |
| Tipo de Agenda | Tipo de agenda.  As opções disponíveis são *Uma Vez*, *Periódica Semanal* ou *Periódica Mensal*.  
| Hora de Início |A data e a hora para iniciar a implementação de atualizações. **Nota:** se precisar de implementar imediatamente, o mais cedo possível que as implementações podem ser executadas é meia hora face à hora atual. |
| Duração |O número de minutos durante os quais a Implementação de Atualização tem permissão para ser executada.  Se não forem instaladas durante este período todas as atualizações, as atualizações restantes têm de aguardar até à implementação de atualizações seguinte. |
| Computadores |Os nomes dos computadores ou dos grupos de computadores a incluir e segmentar na Implementação de Atualizações.  Selecione uma ou mais entradas na lista pendente. |

<br><br> ![Página Nova Implementação de Atualizações](./media/oms-solution-update-management/update-newupdaterun-page.png)

### <a name="time-range"></a>Intervalo de tempo
Por predefinição, o âmbito dos dados analisados na solução de Gestão de Atualizações são todos os grupos de gestão ligados e gerados durante o último um dia. 

Para alterar o intervalo de tempo dos dados, selecione **Dados com base em**, na parte superior do dashboard. Pode selecionar registos criados ou atualizados nos últimos sete dias, um dia ou seis horas. Também pode selecionar **Personalizado** e especificar um intervalo de datas personalizado.

## <a name="log-analytics-records"></a>Registos do Log Analytics
A solução de Gestão de Atualizações cria dois tipos de registos no repositório do OMS.

### <a name="update-records"></a>Registos de atualizações
É criado um registo com um tipo de **Atualização** para cada atualização que esteja instalada ou que seja necessária para cada computador. Os registos de atualizações têm as propriedades da tabela seguinte.

| Propriedade | Descrição |
| --- | --- |
| Tipo |*Atualização* |
| SourceSystem |A origem que aprovou a instalação da atualização.<br>Os valores possíveis são:<br>- Microsoft Update<br>- Windows Update<br>- SCCM<br>- Servidores Linux (obtidos a partir dos Gestores de Pacotes) |
| Aprovado |Especifica se a atualização foi aprovada para instalação.<br> Para servidores Linux, é atualmente opcional, porque a aplicação de patches não é gerida pelo OMS. |
| Classificação para Windows |Classificação da atualização.<br>Os valores possíveis são:<br>-    Aplicações<br>- Atualizações Criticas<br>- Atualizações de Definições<br>- Pacotes de Funcionalidades<br>- Atualizações de Segurança<br>- Service Packs<br>- Update Rollups<br>- Atualizações |
| Classificação para Linux |Classificação da atualização.<br>Os valores possíveis são:<br>- Atualizações Criticas<br>- Atualizações de Segurança<br>- Outras Atualizações |
| Computador |O nome do computador. |
| InstallTimeAvailable |Especifica se a hora de instalação está disponível a partir de outros agentes que instalaram a mesma atualização. |
| InstallTimePredictionSeconds |O tempo de instalação estimado em segundos, com base nos outros agentes que instalaram a mesma atualização. |
| KBID |O ID do artigo KB que descreve a atualização. |
| ManagementGroupName |O nome do grupo de gestão para agentes do SCOM.  Para outros agentes, é AOI-<workspace ID>. |
| MSRCBulletinID |O ID do boletim de segurança da Microsoft que descreve o problema. |
| MSRCSeverity |A gravidade do boletim de segurança da Microsoft.<br>Os valores possíveis são:<br>- Crítica<br>- Importante<br>- Moderada |
| Opcional |Especifica se a atualização é opcional. |
| Produto |O nome do produto ao qual se destina a atualização.  Clique em **Ver** para abrir o artigo num browser. |
| PackageSeverity |A gravidade da vulnerabilidade corrigida nesta atualização, conforme comunicado pelos fornecedores de distribuições Linux. |
| PublishDate |A data e a hora em que a atualização foi instalada. |
| RebootBehavior |Especifica se a atualização força um reinício.<br>Os valores possíveis são:<br>- canrequestreboot<br>- neverreboots |
| RevisionNumber |O número de revisão da atualização. |
| SourceComputerId |O GUID para identificar exclusivamente o computador. |
| TimeGenerated |A data e a hora em que o registo foi atualizado pela última vez. |
| Título |O nome da atualização. |
| UpdateID |O GUID para identificar exclusivamente a atualização. |
| UpdateState |Especifica se a atualização está instalada neste computador.<br>Os valores possíveis são:<br>- Instalada - a atualização está instalada neste computador.<br>- Necessária - a atualização não está instalada e é necessária neste computador. |

Quando faz uma pesquisa de registos que devolve registos com um tipo de **Atualização**, pode selecionar a vista **Atualizações**, que apresenta um conjunto de mosaicos que resumem as atualizações devolvidas pela pesquisa. Pode clicar nas entradas nos mosaicos **Atualizações em falta e aplicadas** e **Atualizações necessárias e opcionais** para limitar a vista a esses conjuntos de atualizações. Selecione a vista **Lista** ou **Tabela** para devolver os registos individuais.<br> 

![Vista de Atualizações de Pesquisa de Registos com o Tipo de Registo de Atualização](./media/oms-solution-update-management/update-la-view-updates.png)  

Na vista **Tabela**, pode clicar no **KBID** relativo a um registo para abrir um browser com o artigo KB. Isto permite-lhe ler rapidamente os detalhes dessa atualização em particular.<br> 

![Vista de Tabela da Pesquisa de Registos com Mosaicos Tipo de Registos de Atualização](./media/oms-solution-update-management/update-la-view-table.png)

Na vista **Lista**, clique na ligação **Vista**, junto ao KBID, para abrir o artigo KB.<br>

![Vista de Lista da Pesquisa de Registos com o Mosaico Tipo de Registo de Atualização](./media/oms-solution-update-management/update-la-view-list.png)

### <a name="updatesummary-records"></a>Registos UpdateSummary
É criado um registo com o tipo de **UpdateSummary** em cada computador agente do Windows. Este registo é atualizado sempre que são analisadas atualizações no computador. Os registos **UpdateSummary** têm as propriedades da tabela seguinte.

| Propriedade | Descrição |
| --- | --- |
| Tipo |UpdateSummary |
| SourceSystem |OpsManager |
| Computador |O nome do computador. |
| CriticalUpdatesMissing |O número de atualizações críticas em falta no computador. |
| ManagementGroupName |O nome do grupo de gestão para agentes do SCOM. Para outros agentes, é AOI-<workspace ID>. |
| NETRuntimeVersion |A versão do tempo de execução .NET instalado no computador. |
| OldestMissingSecurityUpdateBucket |Bucket para categorizar o período de tempo desde que a atualização de segurança em falta mais antiga neste computador foi publicada.<br>Os valores possíveis são:<br>- Mais antiga<br>-    Há 180 dias<br>- Há 150 dias<br>-    Há 120 dias<br>- Há 90 dias<br>- Há 60 dias<br>-    Há 30 dias<br>-    Recente |
| OldestMissingSecurityUpdateInDays |O número de dias desde que a atualização de segurança em falta mais antiga neste computador foi publicada. |
| OsVersion |A versão do sistema operativo instalado no computador. |
| OtherUpdatesMissing |O número de outras atualizações em falta no computador. |
| SecurityUpdatesMissing |O número de atualizações de segurança em falta no computador. |
| SourceComputerId |O GUID para identificar exclusivamente o computador. |
| TimeGenerated |A data e a hora em que o registo foi atualizado pela última vez. |
| TotalUpdatesMissing |O número total de atualizações em falta no computador. |
| WindowsUpdateAgentVersion |O número da versão do agente do Windows Update no computador |
| WindowsUpdateSetting |A definição que define como o computador vai instalar as atualizações importantes.<br>Os valores possíveis são:<br>- Desativado<br>- Notificar antes da instalação<br>- Instalação agendada |
| WSUSServer |O URL do servidor WSUS, se o computador estiver configurado para o utilizar. |

## <a name="sample-log-searches"></a>Pesquisas de registo de exemplo
A tabela seguinte disponibiliza pesquisas de registos de exemplo para registos de atualizações que esta solução recolhe. 

| Consulta | Descrição |
| --- | --- |
|Computadores servidores baseados no Windows que precisam de atualizações |“Type:Update OSType!=Linux UpdateState=Needed Optional=false Approved!=false | measure count() by Computer” |
|Servidores Linux que precisam de atualizações | “Type:Update OSType=Linux UpdateState!=’Not needed’ | measure count() by Computer” |
| Todos os computadores com atualizações em falta |“Type=Update UpdateState=Needed Optional=false | select Computer,Title,KBID,Classification,UpdateSeverity,PublishedDate” |
| Atualizações em falta num computador específico (substitua pelo nome do seu computador) |“Type=Update UpdateState=Needed Optional=false Computer=’COMPUTER01.contoso.com’ | select Computer,Title,KBID,Product,UpdateSeverity,PublishedDate” |
| Todos os computadores com atualizações críticas ou de segurança em falta |`Type=Update UpdateState=Needed Optional=false (Classification="Security Updates" OR Classification="Critical Updates"`) |
| Atualizações críticas ou de segurança necessárias por parte dos computadores nos quais as atualizações são aplicadas manualmente |“Type=Update UpdateState=Needed Optional=false (Classification="Security Updates" OR Classification="Critical Updates") Computer IN {Type=UpdateSummary WindowsUpdateSetting=Manual | Distinct Computer} | Distinct KBID” |
| Eventos de erro de computadores que têm atualizações críticas ou de segurança necessárias em falta |“Type=Event EventLevelName=error Computer IN {Type=Update (Classification=’Security Updates’ OR Classification=’Critical Updates’) UpdateState=Needed Optional=false | Distinct Computer}” |
| Todos os computadores com update rollups em falta |“Type=Update Optional=false Classification=’Update Rollups’ UpdateState=Needed| select Computer,Title,KBID,Classification,UpdateSeverity,PublishedDate” |
| Atualizações em falta distintas em todos os computadores |“Type=Update UpdateState=Needed Optional=false | Distinct Title” |
| Computador servidor baseado no Windows com as atualizações que falharam numa execução de atualizações | “Type:UpdateRunProgress InstallationStatus=failed | measure count() by Computer, Title, UpdateRunName” |
| Servidor Linux com atualizações que falharam numa execução de atualizações |“Type:UpdateRunProgress InstallationStatus=failed | measure count() by Computer, Product, UpdateRunName” |
| Associação de computador WSUS |“Type=UpdateSummary | measure count() by WSUSServer” |
| Configuração de atualizações automáticas |“Type=UpdateSummary | measure count() by WindowsUpdateSetting” |
| Computadores com atualizações automáticas desativadas |`Type=UpdateSummary WindowsUpdateSetting=Manual` |
| Lista de todos os computadores Linux que têm atualizações de pacotes disponíveis |“Type=Update and OSType=Linux and UpdateState!=’Not needed’ | measure count() by Computer” |
| Lista de todos os computadores Linux que têm atualizações de pacotes disponíveis, as quais se destinam a vulnerabilidades Críticas ou de Segurança |“Type=Update and OSType=Linux and UpdateState!=’Not needed’ and (Classification=’Critical Updates’ OR Classification=’Security Updates’) | measure count() by Computer” |
| Lista de todos os pacotes que têm uma atualização disponível |Tipo = Update e OSType = Linux e UpdateState! = "Não necessárias" |
| Lista de todos os pacotes que têm atualizações disponíveis, as quais se destinam a vulnerabilidades Críticas ou de Segurança |`Type=Update  and OSType=Linux and UpdateState!="Not needed" and (Classification="Critical Updates" OR Classification="Security Updates")` |
| Listar o que as implementações de atualizações modificaram nos computadores |“Type:UpdateRunProgress | measure Count() by UpdateRunName” |
|Computadores que foram atualizados nesta execução de atualizações (substitua o valor pelo nome da sua Implementação de Atualizações) |“Type:UpdateRunProgress UpdateRunName=’DeploymentName’ | measure Count() by Computer” |
| Lista de todos os computadores “Ubuntu” com eventuais atualizações disponíveis |“Type=Update and OSType=Linux and OSName = Ubuntu &| measure count() by Computer” |

## <a name="troubleshooting"></a>Resolução de problemas 

Esta secção disponibiliza informações para ajudar a resolver problemas com a solução Gestão de Atualizações.  

### <a name="how-do-i-troubleshoot-update-deployments"></a>Como posso resolver problemas com as implementações de atualizações?
Pode ver os resultados do runbook responsável pela implementação das atualizações incluídas na implementação de atualizações agendada no painel Tarefas da sua conta de Automatização que esteja associada à área de trabalho do OMS que suporte esta solução.  O runbook **Patch-MicrosoftOMSComputer** é um runbook subordinado que segmenta um computador gerido específico e rever o Fluxo verboso apresentará informações detalhadas relativas a essa implementação.  O resultado apresentará que atualizações são aplicáveis, o estado da transferência, o estado da instalação e detalhes adicionais.<br><br> ![Estado da tarefa de Implementação de Atualizações](media/oms-solution-update-management/update-la-patchrunbook-outputstream.png)<br>

Para obter mais informações, veja [Automation runbook output and messages](../automation/automation-runbook-output-and-messages.md) (Resultado e mensagens do runbook de Automatização).   
  
## <a name="next-steps"></a>Passos seguintes
* Utilizar as Pesquisas de Registos no [Log Analytics](../log-analytics/log-analytics-log-searches.md) para ver dados de atualizações detalhados.
* [Criar os seus próprios dashboards](../log-analytics/log-analytics-dashboards.md), que mostram a conformidade das atualizações para os seus computadores geridos.
* [Criar alertas](../log-analytics/log-analytics-alerts.md) para quando são detetadas atualizações críticas em falta nos computadores ou quando um computador tiver as atualizações automáticas desativadas.  


