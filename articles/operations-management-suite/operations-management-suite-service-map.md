---
title: "Utilizar a solução de mapa de serviço no Operations Management Suite | Microsoft Docs"
description: "Mapa de serviço é uma solução de Operations Management Suite que Deteta os componentes da aplicação em sistemas Windows e Linux e mapeia a comunicação entre os serviços automaticamente. Este artigo fornece detalhes para implementar o mapa de serviço no seu ambiente e utilizá-la numa variedade de cenários."
services: operations-management-suite
documentationcenter: 
author: daveirwin1
manager: jwhit
editor: tysonn
ms.assetid: 3ceb84cc-32d7-4a7a-a916-8858ef70c0bd
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/22/2016
ms.author: daseidma;bwren;dairwin
ms.openlocfilehash: f7109a3b21feac396d8c20c7e72a8987f72a909a
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="use-the-service-map-solution-in-operations-management-suite"></a>Utilizar a solução de mapa de serviço no Operations Management Suite
O Mapa de Serviço deteta automaticamente componentes de aplicações em sistemas Windows e Linux e mapeia a comunicação entre serviços. Com o mapa de serviço, pode ver os servidores da forma que pensa que deles: como interligados sistemas que fornecem serviços críticos. Mapa de serviço mostra as ligações entre servidores, processos, e portas em qualquer arquitetura ligados de TCP, sem qualquer configuração necessária à instalação de um agente.

Este artigo descreve os detalhes de utilização de mapa de serviço. Para obter informações sobre como configurar o mapa de serviço e os agentes de integração, consulte [solução de mapa de serviço de configuração no Operations Management Suite](operations-management-suite-service-map-configure.md).


## <a name="use-cases-make-your-it-processes-dependency-aware"></a>Casos de utilização: tornar o departamento de TI processa dependência com suporte para

### <a name="discovery"></a>Deteção
Mapa de serviço cria automaticamente um mapa de referência comuns das dependências entre os servidores, processos e serviços de terceiros. Deteta e mapas de todas as dependências TCP, identificar surprise ligações, sistemas de terceiros remotos que depende e dependências para áreas escuro tradicionais da sua rede, como o Active Directory. Mapa de serviço detetam ligações de rede com falha que sistemas geridos estão a tentar efetuar, ajudando-o a identificar potenciais servidor uma configuração incorreta, interrupção do serviço e problemas de rede.

### <a name="incident-management"></a>Gestão de incidentes
Mapa de serviço de ajuda a eliminar o guesswork de isolamento de problema que mostra como os sistemas estão ligados e afetar entre si. Para além de identificar ligações falhadas, ajuda a identificar os balanceadores de carga configurados incorretamente, uma carga excessiva ou surprising no serviços críticos e não autorizados clientes, tais como máquinas de programador falar com sistemas de produção. Ao utilizar fluxos de trabalho integrados com o controlo do Operations Management Suite alterar, também pode ver se um evento de alteração numa máquina de back-end ou serviço explica a causa de raiz de um incidente.

### <a name="migration-assurance"></a>Garantia de migração
Ao utilizar o mapa de serviço, pode eficazmente planear, acelerar e validar migrações do Azure, que ajuda a garantir que nada for deixado e surprise não ocorrerem. Pode detetar todos os sistemas interdependentes que necessitam de migração em conjunto, avaliar a configuração do sistema e a capacidade de identificar se ainda está a servir os utilizadores de um sistema em execução ou é uma candidata para desativar em vez de migração. Depois de concluída a movimentação, pode verificar na carga de cliente e para verificar se os sistemas de teste e os clientes conseguem estabelecer ligação. Se as definições de planeamento e de firewall de sub-rede tiverem problemas, ligações com falhas no maps de mapa de serviço do ponto os sistemas que precisa de conectividade.

### <a name="business-continuity"></a>Continuidade do negócio
Se estiver a utilizar o Azure Site Recovery e tem de definir a sequência de recuperação para o seu ambiente de aplicação, o mapa de serviço de ajuda pode automaticamente mostram-lhe como sistemas baseiam-se entre si para se certificar de que o plano de recuperação é fiável. Ao escolher um servidor crítico ou grupo e visualizar os seus clientes, pode identificar os sistemas de front-end para recuperar após o servidor foi restaurada e está disponível. Por outro lado, ao observar as dependências de back-end críticos servidores, pode identificar que sistemas recuperar antes dos sistemas de foco são restaurados.

### <a name="patch-management"></a>Gestão de correção
Mapa de serviço melhora a utilização de avaliação de atualização de sistema do Operations Management Suite para mostrar que outras equipas e servidores dependem do seu serviço, pelo que pode notificá-los antecipadamente antes de efetuar para baixo os sistemas para aplicação de patches. Mapa de serviço melhora também a gestão de correção no Operations Management Suite para mostrar se os serviços são disponível e corretamente ligado após estão corrigidos e reiniciados.


## <a name="mapping-overview"></a>Descrição geral de mapeamento
Agentes de mapa de serviço recolher informações sobre todos os processos de ligação de TCP no servidor onde estão instaladas e detalhes sobre as ligações de entrada e saída para cada processo. Na lista no painel esquerdo, pode selecionar máquinas ou grupos que têm agentes de mapa de serviço para visualizar as respetivas dependências através de um intervalo de tempo especificado. Dependência de máquina mapeia foco num computador específico e mostrarem todas as máquinas que são os clientes TCP diretos ou servidores de que a máquina.  Mapas de grupo máquina mostram conjuntos de servidores e as respetivas dependências.

![Descrição geral do mapa de serviço](media/oms-service-map/service-map-overview.png)

As máquinas podem ser expandidas no mapa para mostrar os processos em execução com ligações de rede do Active Directory durante o intervalo de tempo selecionado. Quando um computador remoto com um agente de mapa de serviço é expandido para mostrar detalhes do processo, são apresentados apenas desses processos que comunicam com a máquina de foco. A contagem de máquinas de front-end sem agente que estabelecer ligação com a máquina de foco é indicada no lado esquerdo dos processos que se ligam ao. Se a máquina de foco está a efetuar uma ligação a uma máquina de back-end que tenha sem agente, o servidor de back-end está incluído num grupo de porta do servidor, juntamente com outras ligações para o mesmo número de porta.

Por predefinição, o maps de mapa de serviço mostram últimos 30 minutos de informações de dependência. Ao utilizar os controlos de tempo no canto superior esquerdo, pode consultar o maps para intervalos de tempo históricos de até uma hora para mostrar como dependências comparados no passado (por exemplo, durante um incidente ou antes de uma alteração ocorreu). Dados de mapa de serviço são armazenados durante 30 dias em áreas de trabalho pagas e para áreas de trabalho gratuitas de 7 dias.

## <a name="status-badges-and-border-coloring"></a>Destaques de estado e o limite coloração
Na parte inferior de cada servidor no mapa pode ser uma lista de destaques Estado conveying informações de estado sobre o servidor. Os destaques indicarem que há algumas informações relevantes para o servidor a partir de um integrações de solução Operations Management Suite. Ao clicar num destaque leva-o diretamente para os detalhes do Estado no painel direito. Destaques de estado atualmente disponíveis incluem alertas, suporte técnico, as alterações, segurança e atualizações.

Consoante a gravidade do destaques Estado, os limites do nó de computador podem colorido vermelho (crítico), amarelo (aviso) ou azul (informativos). A cor representa o estado de qualquer um dos destaques estado mais grave. Um limite cinzento indica um nó que não tenha nenhum indicadores de estado.

![Destaques de estado](media/oms-service-map/status-badges.png)

## <a name="machine-groups"></a>Grupos de computador
Grupos de computador permitem-lhe ver mapas centrados em torno de um conjunto de servidores, não apenas um para que possa ver todos os membros de um cluster de servidor ou aplicação multicamado no mapa de um.

Os utilizadores selecionar os servidores ao quais pertencem um grupo em conjunto e escolha um nome para o grupo de.  Em seguida, pode escolher ver o grupo com todos os seus processos e ligações ou visualizar apenas com os processos e ligações diretamente relacionados com os outros membros do grupo.

![Grupo de máquinas](media/oms-service-map/machine-group.png)

### <a name="creating-a-machine-group"></a>Criar um grupo de máquinas
Para criar um grupo, selecione o computador ou máquinas que pretende nas máquinas lista e clique em **adicionar ao grupo**.

![Criar grupo](media/oms-service-map/machine-groups-create.png)

Aqui, pode escolher **criar nova** e atribua o grupo de um nome.

![Nome do grupo](media/oms-service-map/machine-groups-name.png)

>[!NOTE]
>Os grupos de computador são actualmente limitados ao 10 servidores, mas planeamos aumentar este limite em breve.

### <a name="viewing-a-group"></a>Visualizar um grupo
Depois de criar alguns grupos, pode visualizá-los, selecionando o separador de grupos.

![Separador grupos](media/oms-service-map/machine-groups-tab.png)

Em seguida, selecione o nome do grupo para ver o mapa para esse grupo de máquina.
![Grupo de máquinas](media/oms-service-map/machine-group.png) as máquinas que pertençam ao grupo são descritas em branco no mapa.

Expandir o grupo irá listar as máquinas que compõem o grupo de máquina.

![Máquinas do grupo de máquinas](media/oms-service-map/machine-groups-machines.png)

### <a name="filter-by-processes"></a>Filtrar por processos
Pode alternar a vista do mapa entre que mostra todas as ligações de processos e o grupo e apenas aqueles que referem-se diretamente para o grupo de máquina.  A vista predefinida é a mostrar todos os processos.  Pode alterar a vista clicando no ícone de filtro acima do mapa.

![Filtro grupo](media/oms-service-map/machine-groups-filter.png)

Quando **todos os processos** é selecionado, o mapa irá incluir todos os processos e ligações em cada uma das máquinas do grupo.

![Grupo de máquinas todos os processos](media/oms-service-map/machine-groups-all.png)

Se alterar a vista para mostrar apenas **grupo ligado processos**, o mapa será narrowed para baixo para apenas os processos e ligações que estejam diretamente ligadas ao outras máquinas do grupo, como criar uma vista simplificada.

![Grupo de máquina filtrado processos](media/oms-service-map/machine-groups-filtered.png)
 
### <a name="adding-machines-to-a-group"></a>Adicionar máquinas a um grupo
Ao adicionar computadores a um grupo existente, marque as caixas junto as máquinas que pretende e, em seguida, clique em **adicionar ao grupo**.  Em seguida, selecione o grupo que pretende adicionar as máquinas.
 
### <a name="removing-machines-from-a-group"></a>Remover máquinas de um grupo
Na lista de grupos, expanda o nome do grupo para listar as máquinas do grupo de máquina.  Em seguida, clique no menu de botão de reticências junto a máquina que pretende remover e escolha **remover**.

![Remover máquina do grupo](media/oms-service-map/machine-groups-remove.png)

### <a name="removing-or-renaming-a-group"></a>Remover ou mudar o nome de um grupo
Clique no menu de botão de reticências junto ao nome do grupo na lista de grupos.

![Menu do grupo de máquinas](media/oms-service-map/machine-groups-menu.png)


## <a name="role-icons"></a>Ícones de função
Determinados processos servem funções específicas em máquinas: web servidores, servidores de aplicações, base de dados e assim sucessivamente. Mapa de serviço annotates caixas de processos e o computadores com ícones de função para ajudar a identificar rapidamente a função de um processo ou desempenhada por do servidor.

| Ícone de função | Descrição |
|:--|:--|
| ![Servidor Web](media/oms-service-map/role-web-server.png) | Servidor Web |
| ![Servidor de aplicações](media/oms-service-map/role-application-server.png) | Servidor de aplicações |
| ![Servidor de base de dados](media/oms-service-map/role-database.png) | Servidor de base de dados |
| ![Servidor LDAP](media/oms-service-map/role-ldap.png) | Servidor LDAP |
| ![Servidor SMB](media/oms-service-map/role-smb.png) | Servidor SMB |

![Ícones de função](media/oms-service-map/role-icons.png)


## <a name="failed-connections"></a>Falha de ligações
Ligações de falha são mostradas no maps de mapa de serviço para os processos e computadores, com uma linha vermelha tracejada que indica que um sistema de cliente está a conseguir alcançar um processo ou a porta. Falha de ligações são comunicadas a partir do qualquer sistema com um agente de mapa de serviço implementado se esse sistema é a tentar a falha de ligação. Mapa de serviço mede este processo, observar os sockets TCP que falham para estabelecer uma ligação. Esta falha pode resultar de uma firewall, uma configuração incorreta no cliente ou servidor ou um serviço remoto está indisponível.

![Falha de ligações](media/oms-service-map/failed-connections.png)

Noções sobre ligações falhadas podem ajudar a resolver problemas, validação de migração, a análise de segurança e a compreensão geral da arquitetura. Falha de ligações, por vezes, são inofensivas, mas são, muitas vezes, ponto diretamente a um problema, tal como num ambiente de ativação pós-falha subitamente a tornar-se inacessível ou duas camadas da aplicação serem incapazes de comunicar com após a migração para a nuvem.

## <a name="client-groups"></a>Grupos de clientes
Os grupos de cliente são caixas no mapa que representam computadores cliente que não têm agentes de dependência. Um único grupo de cliente representa os clientes para um processo individuais ou a máquina.

![Grupos de clientes](media/oms-service-map/client-groups.png)

Para ver os endereços IP dos servidores de um grupo de cliente, selecione o grupo. O conteúdo do grupo está listado no **propriedades do grupo de cliente** painel.

![Propriedades do grupo de cliente](media/oms-service-map/client-group-properties.png)

## <a name="server-port-groups"></a>Grupos de portas de servidor
Os grupos de porta do servidor são caixas que representam as portas de servidor em servidores que não têm agentes de dependência. A caixa contém a porta do servidor e uma contagem do número de servidores com ligações a essa porta. Expanda a caixa para ver as ligações e servidores individuais. Se houver apenas um servidor na caixa, o nome ou endereço IP é listado.

![Grupos de portas de servidor](media/oms-service-map/server-port-groups.png)

## <a name="context-menu"></a>Menu Contexto
Ao clicar no botão de reticências (…) na parte superior direita de qualquer servidor apresenta o menu de contexto para esse servidor.

![Falha de ligações](media/oms-service-map/context-menu.png)

### <a name="load-server-map"></a>Mapa de servidor de carga
Ao clicar em **carga servidor mapa** leva-o para um mapa de novo com o servidor selecionado como a nova máquina foco.

### <a name="show-self-links"></a>Mostrar auto-ligações
Ao clicar em **Mostrar Self-Links** redesenha o nó de servidor, incluindo quaisquer auto-ligações, que são as ligações de TCP que começam e terminam em processos no servidor. Se auto-ligações são apresentados, as alterações do comando de menu para **ocultar Self-Links**, para que pode desativá-los.

## <a name="computer-summary"></a>Resumo do computador
O **máquina resumo** painel inclui uma descrição geral do sistema operativo de um servidor, contagens de dependência e dados a partir de outras soluções de Operations Management Suite. Esses dados incluem métricas de desempenho, pedidos de suporte técnico do serviço, o registo de alterações, segurança e atualizações.

![Painel de resumo de máquina](media/oms-service-map/machine-summary.png)

## <a name="computer-and-process-properties"></a>Propriedades do computador e o processo
Quando navega um mapa de mapa de serviço, pode selecionar máquinas e processos para obter o contexto adicional sobre as respetivas propriedades. Máquinas fornecem informações sobre DNS nome, endereços IPv4, CPU e memória capacidade, tipo da VM, sistema operativo e versão reiniciar pela última vez e os IDs dos agentes do Operations Management Suite e mapa de serviço.

![Painel de propriedades da máquina](media/oms-service-map/machine-properties.png)

Pode reunir os detalhes do processo do sistema operativo metadados sobre a execução de processos, incluindo o nome do processo, descrição do processo, nome de utilizador e domínio (no Windows), o nome da empresa, nome de produto, versão do produto, diretório de trabalho, linha de comandos e processo hora de início.

![Painel de propriedades do processo](media/oms-service-map/process-properties.png)

O **resumo do processo** painel fornece informações adicionais acerca da conetividade do processo, incluindo a portas vinculadas, ligações de entrada e saídas e ligações de falha.

![Painel de resumo do processo](media/oms-service-map/process-summary.png)

## <a name="operations-management-suite-alerts-integration"></a>Integração do Operations Management Suite alertas
Mapa de serviço é integrado com o Operations Management Suite alertas para mostrar alertas desencadeadas para o servidor selecionado no intervalo de tempo selecionado. O servidor apresenta um ícone, se existirem alertas atuais e o **máquina alertas** painel apresenta os alertas.

![Painel de alertas do computador](media/oms-service-map/machine-alerts.png)

Para ativar o mapa de serviço apresentar os alertas pertinentes, crie uma regra de alerta é acionado para um computador específico. Para criar alertas adequadas:
- Incluir uma cláusula ao grupo por computador (por exemplo, **por 1 minuto do intervalo de computador**).
- Optar por um alerta com base numa métrica medida.

![Configuração de alerta](media/oms-service-map/alert-configuration.png)


## <a name="operations-management-suite-log-events-integration"></a>Integração de eventos de registo do Operations Management Suite
Mapa de serviço integra-se a pesquisa de registo para mostrar uma contagem de todos os eventos de registo disponível para o servidor selecionado durante o intervalo de tempo selecionado. Pode clicar em qualquer linha na lista de contagens de eventos para ir para o registo de pesquisa e ver os eventos de registo individuais.

![Painel de eventos de registo de máquina](media/oms-service-map/log-events.png)

## <a name="operations-management-suite-service-desk-integration"></a>Integração do Operations Management Suite suporte técnico
Integração de mapa de serviço com o conector de gestão do serviço de TI é automática quando ambas as soluções são ativadas e configuradas na sua área de trabalho do Operations Management Suite. A integração no mapa de serviço com o nome "Suporte técnico." Para obter mais informações, consulte [gerir centralmente a itens de trabalho ITSM utilizando o conector de gestão do serviço de TI](https://docs.microsoft.com/azure/log-analytics/log-analytics-itsmc-overview).

O **máquina suporte técnico** painel apresenta uma lista de todos os eventos de gestão do serviço de TI para o servidor selecionado no intervalo de tempo selecionado. O servidor apresenta um ícone se existirem itens atuais e listas de painel de suporte técnico do serviço de Machine-los.

![Painel de suporte técnico da máquina](media/oms-service-map/service-desk.png)

Para abrir o item na sua solução ITSM ligada, clique em **Item de trabalho de vista**.

Para ver os detalhes do item no registo de pesquisa, clique em **Mostrar na pesquisa de registo**.


## <a name="operations-management-suite-change-tracking-integration"></a>Integração do Operations Management Suite Alterar controlo
Integração de mapa de serviço com o controlo de alterações é automática quando ambas as soluções são ativadas e configuradas na sua área de trabalho do Operations Management Suite.

O **máquina Alterar controlo** painel apresenta uma lista de todas as alterações, com a primeira mais recente, juntamente com uma ligação para desagregar para pesquisa de registo para obter detalhes adicionais.

![Painel de controlo de alterações do computador](media/oms-service-map/change-tracking.png)

A imagem seguinte é uma vista detalhada de um evento de ConfigurationChange que poderá ver depois de selecionar **Mostrar na análise de registos**.

![ConfigurationChange eventos](media/oms-service-map/configuration-change-event.png)


## <a name="operations-management-suite-performance-integration"></a>Integração de desempenho do Operations Management Suite
O **o desempenho da máquina** painel apresenta métricas de desempenho padrão para o servidor selecionado. As métricas incluem a utilização da CPU, utilização da memória, os bytes de rede enviados e recebidos e uma lista dos processos principais por bytes de rede enviados e recebidos.

![Painel de desempenho do computador](media/oms-service-map/machine-performance.png)

Para ver dados de desempenho, poderá ser necessário [ativar os contadores de desempenho de análise de registos adequados](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-performance-counters).  Os contadores que pretende ativar:

Windows:
- Processor(*)\% de tempo do processador
- Memória\% dos Bytes consolidados em utilização
- Rede adaptador (*) \Bytes enviados/seg
- Rede adaptador (*) \Bytes recebidos/seg

Linux:
- Processor(*)\% de tempo do processador
- Memory(*)\% de memória utilizada
- Rede adaptador (*) \Bytes enviados/seg
- Rede adaptador (*) \Bytes recebidos/seg

Para obter os dados de desempenho de rede, tem também tiver ativado a solução de transmissão dados 2.0 no Operations Management Suite.
 
## <a name="operations-management-suite-security-integration"></a>Integração do Operations Management Suite segurança
Integração de mapa de serviço com segurança e de auditoria é automática quando ambas as soluções são ativadas e configuradas na sua área de trabalho do Operations Management Suite.

O **máquina segurança** painel mostra os dados da solução de auditoria e segurança do Operations Management Suite para o servidor selecionado. Painel de lista um resumo de quaisquer problemas de segurança pendentes para o servidor durante o intervalo de tempo selecionado. Ao clicar em qualquer um dos simulações de problemas de segurança para baixo para uma pesquisa de registo para obter detalhes sobre os mesmos.

![Painel de segurança da máquina](media/oms-service-map/machine-security.png)


## <a name="operations-management-suite-updates-integration"></a>Integração do Operations Management Suite atualizações
Integração de mapa de serviço com a gestão de atualização for automática quando ambas as soluções são ativadas e configuradas na sua área de trabalho do Operations Management Suite.

O **atualizações máquina** painel apresenta os dados da solução de gestão de atualização do Operations Management Suite para o servidor selecionado. Painel de lista um resumo de todas as atualizações em falta para o servidor durante o intervalo de tempo selecionado.

![Painel de controlo de alterações do computador](media/oms-service-map/machine-updates.png)

## <a name="log-analytics-records"></a>Registos do Log Analytics
Dados de inventário de computador e o processo de mapa de serviço estão disponíveis para [pesquisa](../log-analytics/log-analytics-log-searches.md) na análise de registos. Pode aplicar estes dados para os cenários que incluem o planeamento da migração, análise da capacidade, de deteção e a resolução de problemas de desempenho a pedido.

Um registo é gerado por hora para cada computador exclusivo e o processo, além dos registos que são gerados quando um processo ou o computador inicia ou está integrada com o mapa de serviço. Estes registos tem as propriedades nas tabelas seguintes. Os campos e os valores no mapa de eventos de ServiceMapComputer_CL aos campos do recurso de máquina na API ServiceMap do Azure Resource Manager. Os campos e valores nos eventos ServiceMapProcess_CL mapeiam para os campos do recurso processo na API ServiceMap do Azure Resource Manager. O campo de ResourceName_s corresponda o campo de nome de recurso do Resource Manager correspondente. 

>[!NOTE]
>À medida que aumentam a funcionalidades de mapa de serviço, estes campos estão sujeitos a alterações.

Existem propriedades geradas internamente que pode utilizar para identificar processos exclusivos e computadores:

- Computador: Utilize ResourceId ou ResourceName_s para identificar exclusivamente um computador dentro de uma área de trabalho do Operations Management Suite.
- Processo: ResourceId de utilização para identificar exclusivamente um processo dentro de uma área de trabalho do Operations Management Suite. ResourceName_s é exclusivo no contexto da máquina em que o processo está em execução (MachineResourceName_s) 

Porque podem existir vários registos para um processo especificado e o computador de um intervalo de tempo especificado, as consultas podem devolver mais do que um registo para o mesmo computador ou processo. Para incluir apenas o registo mais recente, adicione "| a eliminação de duplicados ResourceId"para a consulta.

### <a name="servicemapcomputercl-records"></a>Registos de ServiceMapComputer_CL
Registos com um tipo de *ServiceMapComputer_CL* ter dados de inventário para servidores com agentes de mapa de serviço. Estes registos de ter as propriedades na tabela seguinte:

| Propriedade | Descrição |
|:--|:--|
| Tipo | *ServiceMapComputer_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | O identificador exclusivo para o computador da área de trabalho |
| ResourceName_s | O identificador exclusivo para o computador da área de trabalho |
| ComputerName_s | O FQDN do computador |
| Ipv4Addresses_s | Endereços de uma lista de IPv4 do servidor |
| Ipv6Addresses_s | Endereços de uma lista de IPv6 do servidor |
| DnsNames_s | Uma matriz de nomes DNS |
| OperatingSystemFamily_s | Windows ou Linux |
| OperatingSystemFullName_s | O nome completo do sistema operativo  |
| Bitness_s | O número de bits da máquina (32 bits ou 64 bits)  |
| PhysicalMemory_d | A memória física em MB |
| Cpus_d | O número de CPUs |
| CpuSpeed_d | A velocidade da CPU em MHz|
| VirtualizationState_s | *desconhecido*, *físico*, *virtual*, *hipervisor* |
| VirtualMachineType_s | *Hyper-v*, *vmware*, e assim sucessivamente |
| VirtualMachineNativeMachineId_g | O ID de VM conforme atribuído pelo respetivo hipervisor |
| VirtualMachineName_s | O nome da VM |
| BootTime_t | O tempo de arranque |



### <a name="servicemapprocesscl-type-records"></a>Registos do tipo ServiceMapProcess_CL
Registos com um tipo de *ServiceMapProcess_CL* ter dados de inventário para processos de ligação de TCP em servidores com agentes de mapa de serviço. Estes registos de ter as propriedades na tabela seguinte:

| Propriedade | Descrição |
|:--|:--|
| Tipo | *ServiceMapProcess_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | O identificador único para um processo na área de trabalho |
| ResourceName_s | O identificador único para um processo na máquina em que está a ser executado|
| MachineResourceName_s | O nome do recurso da máquina |
| ExecutableName_s | O nome do executável de processo |
| StartTime_t | A hora de início do conjunto de processos |
| FirstPid_d | O PID primeiro no conjunto de processos |
| Description_s | A descrição do processo |
| CompanyName_s | O nome da empresa |
| InternalName_s | O nome interno |
| ProductName_s | O nome do produto |
| ProductVersion_s | A versão do produto |
| FileVersion_s | A versão do ficheiro |
| CommandLine_s | A linha de comandos |
| ExecutablePath _s | O caminho para o ficheiro executável |
| WorkingDirectory_s | O diretório de trabalho |
| Nome de utilizador | A conta sob a qual o processo está a executar |
| UserDomain | O domínio em que o processo está a executar |


## <a name="sample-log-searches"></a>Pesquisas de registo de exemplo

### <a name="list-all-known-machines"></a>Lista todas as máquinas conhecidas
Tipo = ServiceMapComputer_CL | a eliminação de duplicados ResourceId

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>Liste a capacidade de memória física de todos os computadores geridos.
Tipo = ServiceMapComputer_CL | Selecione PhysicalMemory_d, ComputerName_s | ResourceId de eliminação de duplicados

### <a name="list-computer-name-dns-ip-and-os"></a>Nome de computador da lista, DNS, IP e SO.
Tipo = ServiceMapComputer_CL | Selecione ComputerName_s, OperatingSystemFullName_s, DnsNames_s, IPv4Addresses_s | a eliminação de duplicados ResourceId

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Localizar todos os processos com "sql" na linha de comandos
Tipo = ServiceMapProcess_CL CommandLine_s = \*sql\* | ResourceId da eliminação de duplicados

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>Localizar uma máquina (registo mais recente) por nome de recurso
Tipo = ServiceMapComputer_CL "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | a eliminação de duplicados ResourceId

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>Localizar uma máquina (registo mais recente) por endereço IP
Tipo = ServiceMapComputer_CL "10.229.243.232" | a eliminação de duplicados ResourceId

### <a name="list-all-known-processes-on-a-specified-machine"></a>Listar todos os processos num computador especificado
Tipo = ServiceMapProcess_CL MachineResourceName_s="m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | a eliminação de duplicados ResourceId

### <a name="list-all-computers-running-sql"></a>Listar todos os computadores com SQL Server
Tipo = ServiceMapComputer_CL ResourceName_s em {tipo = ServiceMapProcess_CL \*sql\* | MachineResourceName_s distintos} | a eliminação de duplicados ResourceId | ComputerName_s distintos

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>Lista todas as versões de produto exclusiva do curl no meu Centro de dados
Tipo = ServiceMapProcess_CL ExecutableName_s = curl | ProductVersion_s distintos

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Criar um grupo de computadores de todos os computadores com CentOS
Tipo = ServiceMapComputer_CL OperatingSystemFullName_s = \*CentOS\* | ComputerName_s distintos


## <a name="rest-api"></a>API REST
Todos os dados de servidor, o processo e dependência no mapa de serviço estão disponíveis através de [API de REST do mapa de serviço](https://docs.microsoft.com/rest/api/servicemap/).


## <a name="diagnostic-and-usage-data"></a>dados de diagnóstico e utilização
A Microsoft recolhe automaticamente dados de utilização e desempenho através da utilização do serviço de mapa de serviço. A Microsoft utiliza estes dados para fornecer e melhorar a qualidade, segurança e integridade do serviço de mapa de serviço. Para fornecer capacidades de resolução de problemas exatas e eficientes, os dados incluem informações sobre a configuração do seu software, tais como o sistema operativo e o versão, o endereço IP, o nome DNS e o nome da estação de trabalho. Microsoft não recolhe nomes, moradas ou outras informações de contacto.

Para obter mais informações sobre a recolha de dados e de utilização, consulte o [declaração de privacidade do Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).


## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [pesquisas de registo](../log-analytics/log-analytics-log-searches.md) na análise de registos para obter os dados recolhidos pelo mapa de serviço.


## <a name="troubleshooting"></a>Resolução de problemas
Consulte o [secção do documento de configuração de mapa de serviço de resolução de problemas](operations-management-suite-service-map-configure.md#troubleshooting).


## <a name="feedback"></a>Comentários
Tem quaisquer comentários para-nos sobre o mapa de serviço ou esta documentação?  Visite a nossa [página de voz do utilizador](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map), onde pode sugerir funcionalidades ou votar segurança sugestões existentes.
