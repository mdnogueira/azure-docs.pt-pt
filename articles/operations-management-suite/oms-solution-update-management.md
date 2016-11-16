---
title: "Solução de Gestão de Atualizações no OMS | Microsoft Docs"
description: "Este artigo destina-se a ajudá-lo a saber como utilizar esta solução para gerir atualizações aos seus computadores Windows e Linux."
services: operations-management-suite
documentationcenter: 
author: MGoedtel
manager: jwhit
editor: 
ms.assetid: e33ce6f9-d9b0-4a03-b94e-8ddedcc595d2
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/14/2016
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 4bd1e84fd9af1273f95f70d941c3a4535984c8a9


---
# <a name="update-management-solution-in-omsmediaomssolutionupdatemanagementupdatemanagementsolutioniconpng-update-management-solution-in-oms"></a>![Solução de Gestão de Atualizações no OMS](./media/oms-solution-update-management/update-management-solution-icon.png) Solução de Gestão de Atualizações no OMS
A solução de Gestão de Atualizações no OMS permite-lhe gerir atualizações para os seus computadores Windows e Linux.  Pode rapidamente avaliar o estado das atualizações disponíveis em todos os computadores agente e iniciar o processo de instalação de atualizações necessárias para os servidores. 

## <a name="prerequisites"></a>Pré-requisitos
* Os agentes do Windows têm de ser configurados para comunicar com um Windows Server Update Services (WSUS) ou de ter acesso ao Microsoft Update.  
  
  > [!NOTE]
  > O agente do Windows não pode ser gerido em simultâneo pelo System Center Configuration Manager.  
  > 
  > 
* Os agentes do Linux têm de ter acesso a um repositório de atualização.  O agente do OMS para Linux pode ser transferido a partir do [GitHub](https://github.com/microsoft/oms-agent-for-linux). 

## <a name="configuration"></a>Configuração
Execute os seguintes passos para adicionar a solução de Gestão de Atualizações à sua área de trabalho do OMS e adicionar agentes do Linux.  Os agentes do Windows são adicionados automaticamente, sem configurações adicionais.

1. Adicione a solução de Gestão de Atualizações à sua área de trabalho do OMS através do processo descrito em [Add OMS solutions (Adicionar soluções do OMS)](../log-analytics/log-analytics-add-solutions.md), na Galeria de Soluções.  
2. No portal do OMS, selecione **Definições** e, em seguida, **Origens Ligadas**.  Tenha em atenção o **ID da Área de Trabalho** e a **Chave Primária** ou a **Chave Secundária**.
3. Execute os seguintes passos para cada computador Linux.
   
   a.    Execute os comandos seguintes para instalar a versão mais recente do agente do OMS para Linux.  Substitua <Workspace ID> pelo ID da Área de Trabalho e <Key> pela Chave Primária ou pela Chave Secundária.
   
     cd ~   wget https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/v1.2.0-75/omsagent-1.2.0-75.universal.x64.sh   sudo bash omsagent-1.2.0-75.universal.x64.sh --upgrade -w <Workspace ID> -s <Key>
   
   b. Para remover o agente, execute o seguinte comando.
   
     sudo bash omsagent-1.2.0-75.universal.x64.sh --purge

## <a name="management-packs"></a>Pacotes de gestão
Se o seu grupo de gestão do System Center Configuration Manager estiver ligado à sua área de trabalho do OMS, os seguintes pacotes de gestão serão instalados no Operations Manager quando adicionar esta solução. Estes pacotes de gestão não precisam de configurações nem de manutenção. 

* Pacote de Informações de Avaliação de Atualização do Microsoft System Center Advisor (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Pacote de Gestão de Implementação de Atualização

Para obter mais informações sobre como são atualizados os pacotes de gestão da solução, veja [Connect Operations Manager to Log Analytics (Ligar o Operations Manager ao Log Analytics)](../log-analytics/log-analytics-om-agents.md).

## <a name="data-collection"></a>Recolha de dados
### <a name="supported-agents"></a>Agentes suportados
A tabela seguinte descreve as origens ligadas que são suportadas por esta solução.

| Origem Ligada | Suportado | Descrição |
| --- | --- | --- |
| Agentes do Windows |Sim |A solução recolhe informações sobre atualizações do sistema de agentes do Windows e inicia a instalação das atualizações necessárias. |
| Agentes do Linux |Sim |A solução recolhe informações sobre atualizações do sistema de agentes de Linux. |
| Grupo de gestão do Operations Manager |Sim |A solução recolhe informações sobre atualizações do sistema de agentes num grupo de gestão ligado.<br>Não é necessária uma ligação direta a partir do agente do Operations Manager ao Log Analytics. Os dados são reencaminhados do grupo de gestão para o repositório do OMS. |
| Conta de armazenamento do Azure |Não |O armazenamento do Azure não inclui informações sobre atualizações do sistema. |

### <a name="collection-frequency"></a>Frequência da recolha
Em cada computador Windows gerido, é feita uma análise duas vezes por dia.  Ao instalar uma atualização, as informação da mesma são atualizadas em 15 minutos.  

Em cada computador Linux gerido, é feita uma análise de três em três horas.  

## <a name="using-the-solution"></a>Utilizar a solução
Quando adiciona a solução de Gestão de Atualizações à sua área de trabalho do OMS, é adicionado o mosaico **Gestão de Atualizações** ao dashboard do OMS. Este mosaico apresenta uma contagem e uma representação gráfica do número de computadores no seu ambiente que precisam de atualizações do sistema nesse momento.<br><br>
![Mosaico de Resumo da Gestão de Atualizações](media/oms-solution-update-management/update-management-summary-tile.png)  

## <a name="viewing-update-assessments"></a>Ver Avaliações de Atualizações
Clique no mosaico **Gestão de Atualizações** para abrir o dashboard **Gestão de Atualizações**. O dashboard inclui as colunas da tabela seguinte. Cada coluna apresenta uma lista de até dez itens que correspondem aos critérios dessa coluna para o âmbito e o intervalo de tempo especificados. Pode executar uma pesquisa de registos que devolve todos os registos ao clicar em **Ver tudo**, na parte inferior da coluna, ou ao clicar no cabeçalho da coluna.

| Coluna | Descrição |
| --- | --- |
| **Computadores Com Atualizações em Falta** | |
| Atualizações Críticas ou de Segurança |Mostra os dez primeiros computadores que têm atualizações em falta, ordenados pelo número de atualizações que têm em falta. Clique no nome de um computador para executar uma pesquisa de registos que devolva todos os registos de atualizações desse computador. |
| Atualizações Críticas ou de Segurança com mais de 30 dias |Identifica o número de computadores que têm atualizações críticas ou de segurança em falta, agrupados pelo período de tempo desde que as atualizações foram publicadas. Clique numa das entradas para executar uma pesquisa de registos que devolva todas as atualizações críticas ou em falta. |
| **Atualizações Necessárias em Falta** | |
| Atualizações Críticas ou de Segurança |Mostra classificações de atualizações que estão em falta nos computadores, ordenadas pelo número de computadores nos quais as atualizações estão em falta com base na categoria. Clique numa classificação para executar uma pesquisa de registos que devolva todos os registos de atualizações dessa classificação. |
| **Implementações de Atualizações** | |
| Implementações de Atualizações |O número de implementações de atualizações agendadas atualmente e a duração até à próxima execução agendada.  Clique neste mosaico para ver atualizações agendadas, em execução atualmente e concluídas ou para agendar uma nova. |

<br>  
![Dashboard de Resumo de Gestão de Atualizações](./media/oms-solution-update-management/update-management-deployment-dashboard.png)<br>  
<br>
![Vista de Computador do Dashboard de Gestão de Atualizações](./media/oms-solution-update-management/update-management-assessment-computer-view.png)<br>  
<br>
![Vista de Pacote do Dashboard de Gestão de Atualizações](./media/oms-solution-update-management/update-management-assessment-package-view.png)<br>  

## <a name="installing-updates"></a>Instalar as atualizações
Depois de terem sido avaliadas as atualizações para todos os computadores Windows no seu ambiente, pode instar as atualizações necessárias ao criar uma *Implementação de Atualização*.  Uma implementação de atualização é uma instalação agendada de atualizações necessárias num ou mais computadores Windows.  Especifique a data e a hora da implementação, bem como um computador ou grupo de computadores que devem ser incluídos.  

As atualizações são instaladas por runbooks na Automatização do Azure.  Atualmente, não pode ver estes runbooks, que não requerem nenhuma configuração.  Quando é criada uma Implementação de Atualização, é criada uma agenda que inicia um runbook de atualização principal num momento especificado nos computadores incluídos.  Este runbook principal inicia um runbook subordinado em cada agente do Windows que efetua a instalação das atualizações necessárias.  

### <a name="viewing-update-deployments"></a>Ver as implementações de atualizações
Clique no mosaico **Implementação de Atualização** para ver a lista de implementações de atualizações existentes.  Estão agrupadas por estado – **Agendada**, **Em Execução** e **Concluída**.<br><br> ![Página de Agenda de Implementações de Atualizações](./media/oms-solution-update-management/update-updatedeployment-schedule-page.png)<br>  

As propriedades apresentadas para cada Implementação de Atualização são descritas na tabela seguinte.

| Propriedade | Descrição |
| --- | --- |
| Nome |Nome da Implementação de Atualização. |
| Agenda |Tipo de agenda.  *OneTime* é único valor possível nesta altura. |
| Hora de Início |A data e a hora em que a Implementação de Atualização está agendada para ser iniciada. |
| Duração |O número de minutos durante os quais a Implementação de Atualização tem permissão para ser executada.  Se não forem instaladas durante este período todas as atualizações, as atualizações restantes têm de aguardar até à implementação de atualizações seguinte. |
| Servidores |O número de computadores afetados pela implementação de atualizações. |
| Estado |O estado atual da Implementação de Atualizações.<br><br>Os valores possíveis são:<br>-    Não Iniciada<br>- Em Execução<br>- Concluída |

Clique numa Implementação de Atualizações para ver o respetivo ecrã de detalhes, que inclui as colunas da tabela seguinte.  Estas colunas não serão preenchidas se a Implementação de Atualizações ainda não tiver começado.<br>

| Coluna | Descrição |
| --- | --- |
| **Resultados do Computador** | |
| Concluído com Êxito |Mostra o número de computadores da Implementação de Atualizações por estado.  Clique num estado para executar uma consulta de registos que devolva todos os registos de atualizações com esse estado na Implementação de Atualizações. |
| Estado da Instalação do Computador |Apresenta os computadores envolvidos na Implementação de Atualizações e a percentagem de atualizações que foram instaladas com êxito. Clique numa das entradas para executar uma pesquisa de registos que devolva todas as atualizações críticas ou em falta. |
| **Resultados da Instância de Atualização** | |
| Estado da Instalação da Instância |Mostra classificações de atualizações que estão em falta nos computadores, ordenadas pelo número de computadores nos quais as atualizações estão em falta com base na categoria. Clique num computador para executar uma pesquisa de registos que devolva todos os registos de atualizações desse computador. |

<br><br> ![Descrição Geral dos Resultados da Implementação de Atualizações](./media/oms-solution-update-management/update-la-updaterunresults-page.png)

### <a name="creating-an-update-deployment"></a>Criar uma Implementação de Atualizações
Crie uma Implementação de Atualizações nova ao clicar no botão **Adicionar**, na parte superior do ecrã, para abrir a página **Nova Implementação de Atualizações**.  Tem de indicar valores para as propriedades da tabela seguinte.

| Propriedade | Descrição |
| --- | --- |
| Nome |O nome exclusivo para identificar a implementação de atualizações. |
| Fuso Horário |O fuso horário a utilizar na hora de início. |
| Hora de Início |A data e a hora para iniciar a implementação de atualizações. |
| Duração |O número de minutos durante os quais a Implementação de Atualização tem permissão para ser executada.  Se não forem instaladas durante este período todas as atualizações, as atualizações restantes têm de aguardar até à implementação de atualizações seguinte. |
| Computadores |Os nomes dos computadores ou dos grupos de computadores a incluir na Implementação de Atualizações.  Selecione uma ou mais entradas na lista pendente. |

<br><br> ![Página Nova Implementação de Atualizações](./media/oms-solution-update-management/update-newupdaterun-page.png)

### <a name="time-range"></a>Intervalo de tempo
Por predefinição, o âmbito dos dados analisados na solução de Gestão de Atualizações são todos os grupos de gestão ligados e gerados durante o último um dia. 

Para alterar o intervalo de tempo dos dados, selecione **Dados com base em**, na parte superior do dashboard. Pode selecionar registos criados ou atualizados nos últimos sete dias, um dia ou seis horas. Também pode selecionar **Personalizado** e especificar um intervalo de datas personalizado.<br><br> ![Opção de Intervalo de Tempo Personalizado](./media/oms-solution-update-management/update-la-time-range-scope-databasedon.png)  

## <a name="log-analytics-records"></a>Registos do Log Analytics
A solução de Gestão de Atualizações cria dois tipos de registos no repositório do OMS.

### <a name="update-records"></a>Registos de atualizações
É criado um registo com um tipo de **Atualização** para cada atualização que esteja instalada ou que seja necessária para cada computador. Os registos de atualizações têm as propriedades da tabela seguinte.

| Propriedade | Descrição |
| --- | --- |
| Tipo |*Atualização* |
| SourceSystem |A origem que aprovou a instalação da atualização.<br>Os valores possíveis são:<br>- Microsoft Update<br>-    Windows Update<br>-    SCCM<br>- Servidores Linux (obtidos a partir dos Gestores de Pacotes) |
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

<br>
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
| Todos os computadores com atualizações em falta |Type=Update UpdateState=Needed Optional=false &#124; select Computer,Title,KBID,Classification,UpdateSeverity,PublishedDate |
| Atualizações em falta no computador "COMPUTER01.contoso.com" (substitua pelo nome do seu computador) |Type=Update UpdateState=Needed Optional=false Computer="COMPUTER01.contoso.com" &#124; select Computer,Title,KBID,Product,UpdateSeverity,PublishedDate |
| Todos os computadores com atualizações críticas ou de segurança em falta |Type=Update UpdateState=Needed Optional=false (Classification="Security Updates" OR Classification="Critical Updates") |
| Atualizações críticas ou de segurança necessárias por parte dos computadores nos quais as atualizações são aplicadas manualmente |Type=Update UpdateState=Needed Optional=false (Classification="Security Updates" OR Classification="Critical Updates") Computer IN {Type=UpdateSummary WindowsUpdateSetting=Manual &#124; Distinct Computer} &#124; Distinct KBID |
| Eventos de erro de computadores que têm atualizações críticas ou de segurança necessárias em falta |Type=Event EventLevelName=error Computer IN {Type=Update (Classification="Security Updates" OR Classification="Critical Updates") UpdateState=Needed Optional=false &#124; Distinct Computer} |
| Todos os computadores com update rollups em falta |Type=Update Optional=false Classification="Update Rollups" UpdateState=Needed &#124; select Computer,Title,KBID,Classification,UpdateSeverity,PublishedDate |
| Atualizações em falta distintas em todos os computadores |Type=Update UpdateState=Needed Optional=false &#124; Distinct Title |
| Associação de computador WSUS |Type=UpdateSummary &#124; measure count() by WSUSServer |
| Configuração de atualizações automáticas |Type=UpdateSummary &#124; measure count() by WindowsUpdateSetting |
| Computadores com atualizações automáticas desativadas |Type=UpdateSummary WindowsUpdateSetting=Manual |
| Lista de todos os computadores Linux que têm atualizações de pacotes disponíveis |Type=Update and OSType=Linux and UpdateState!="Not needed" &#124; measure count() by Computer |
| Lista de todos os computadores Linux que têm atualizações de pacotes disponíveis, as quais se destinam a vulnerabilidades Críticas ou de Segurança |Type=Update and OSType=Linux and UpdateState!="Not needed" and (Classification="Critical Updates" OR Classification="Security Updates") &#124; measure count() by Computer |
| Lista de todos os pacotes que têm uma atualização disponível |Tipo = Update e OSType = Linux e UpdateState! = "Não necessárias" |
| Lista de todos os pacotes que têm atualizações disponíveis, as quais se destinam a vulnerabilidades Críticas ou de Segurança |Type=Update  and OSType=Linux and UpdateState!="Not needed" and (Classification="Critical Updates" OR Classification="Security Updates") |
| Lista de todos os computadores “Ubuntu” com eventuais atualizações disponíveis |Type=Update and OSType=Linux and OSName = Ubuntu &#124; measure count() by Computer |

## <a name="next-steps"></a>Passos seguintes
* Utilizar as Pesquisas de Registos no [Log Analytics](../log-analytics/log-analytics-log-searches.md) para ver dados de atualizações detalhados.
* [Criar os seus próprios dashboards](../log-analytics/log-analytics-dashboards.md), que mostram a conformidade das atualizações para os seus computadores geridos.
* [Criar alertas](../log-analytics/log-analytics-alerts.md) para quando são detetadas atualizações críticas em falta nos computadores ou quando um computador tiver as atualizações automáticas desativadas.  




<!--HONumber=Nov16_HO2-->


