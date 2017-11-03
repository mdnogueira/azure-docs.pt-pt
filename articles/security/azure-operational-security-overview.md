---
title: "Descrição geral de segurança operacionais do Azure | Microsoft Docs"
description: "Este artigo fornece uma descrição geral da segurança operacional do Azure."
services: security
documentationcenter: na
author: unifycloud
manager: swadhwa
editor: tomsh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2017
ms.author: tomsh
ms.openlocfilehash: f2153e783adb955cf9055b09ba9aa2592f51e4b4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-operational-security-overview"></a>Descrição geral de segurança operacionais do Azure
Segurança operacionais do Azure refere-se os serviços, controlos e funcionalidades disponíveis para os utilizadores para proteger os seus dados, aplicações e outros recursos no Microsoft Azure. [Segurança operacionais do Azure](https://docs.microsoft.com/azure/security/azure-operational-security) é uma estrutura que incorpora o conhecimento adquirida através de uma variedade de capacidades que são exclusivos para a Microsoft, incluindo o Microsoft Security Development Lifecycle (SDL), a resposta de segurança da Microsoft Programa do System Center e a deteção de ameaças de segurança informático.

Este artigo de descrição geral de segurança operacionais do Azure concentra-se nas seguintes áreas:

- Azure Operations Management Suite
-   Centro de Segurança do Azure
-   Azure Monitor
-   Observador de rede do Azure
-   Análise de armazenamento do Azure
-   Do Azure Active Directory

## <a name="azure-operations-management-suite"></a>Azure Operations Management Suite
Operações de TI é responsável por gerir a infraestrutura de centro de dados, aplicações e dados, incluindo a estabilidade e segurança destes sistemas. No entanto, a obter conhecimentos de segurança aprofundados em aumentar complexas ambientes de TI, muitas vezes, requer que as organizações cobble em conjunto de dados a partir de vários sistemas de gestão e segurança.

[Microsoft Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) é baseado na nuvem IT solução de gestão da Microsoft que o ajuda a gerir e proteger no local e a infraestrutura de nuvem.

OMS é uma solução de gestão baseado na nuvem IT com ofertas muitos, tais como a automatização de TI, segurança e conformidade, análise de registos e cópia de segurança e recuperação. Como tal, é um auxiliar perfeita para gerir e proteger a sua infraestrutura de TI — no local e na nuvem.

A funcionalidade principal do OMS é disponibilizada por um conjunto de serviços que são executados no Azure. Cada serviço proporciona uma função de gestão específica e pode combinar serviços para alcançar cenários de gestão diferentes. Que inclui:

-   Log analytics
-   Automatização
-   Cópia de segurança
-   Site Recovery

### <a name="log-analytics"></a>Log Analytics
O [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) oferece serviços de monitorização para o OMS, ao recolher dados de recursos geridos num repositório central. Estes dados podem incluir dados de eventos ou de desempenho ou dados personalizados fornecidos através da API. Depois de recolhidos, os dados estão disponíveis para alertas, análises e exportação. Este método permite-lhe consolidar os dados a partir de uma variedade de origens, de modo a que possa combinar dados dos seus serviços do Azure com o seu ambiente no local existente. Também separa claramente a recolha dos dados das ações tomadas em relação aos mesmos, para que todas as ações estejam disponíveis para todos os tipos de dados.

### <a name="automation"></a>Automatização
Microsoft [da automatização do Azure](https://docs.microsoft.com/azure/automation/automation-intro) fornece uma forma dos utilizadores automatizarem as tarefas manuais, execução longa, propensas ao erro e frequentemente repetidas que normalmente são executadas num ambiente de nuvem e empresa. Poupa tempo, aumenta a fiabilidade das tarefas administrativas normais e agenda-as para serem executadas automaticamente em intervalos regulares. Pode automatizar os processos utilizando runbooks ou automatizar a gestão de configuração utilizando a Configuração de Estado Pretendido.

### <a name="backup"></a>Cópia de segurança
[Cópia de segurança do Azure](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) é o serviço baseado no Azure, pode utilizar para criar cópias de segurança (ou proteger) e restaurar os dados na nuvem da Microsoft. O Azure Backup substitui a solução de cópia de segurança no local ou fora das instalações por uma solução baseada na nuvem que é fiável, segura e competitiva em termos de custos. O Azure Backup oferece vários componentes que são transferidos e implementados no computador ou servidor adequado, ou na nuvem. O componente ou o agente que implementar depende do que pretende proteger. Todos os componentes do Azure Backup (independentemente de estar a proteger dados no local ou na cloud) podem ser utilizados para criar cópias de segurança para um cofre dos Serviços de Recuperação do Azure. Consulte o [tabela de componentes do Backup do Azure](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup#which-azure-backup-components-should-i-use).

### <a name="site-recovery"></a>Recuperação de sites
O [Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) proporciona continuidade de negócio ao orquestrar a replicação de computadores e máquinas virtuais no local para o Azure ou para um site secundário. Se o site primário não estiver disponível, é feita a ativação pós-falha para a localização secundária, para que os utilizadores possam continuar a trabalhar, e é feita a reativação pós-falha quando os sistemas estiverem a funcionar novamente. Deteção de ameaças eficiente e inteligente.

## <a name="azure-active-directory"></a>Azure Active Directory
[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-enable-sso-scenario) é identidade abrangente da Microsoft como uma solução de serviço (IDaaS) que:

-   Permite IAM como um serviço em nuvem
-   Fornece gestão de acesso central, início de sessão único (SSO) e relatórios
-   Suporta a gestão de acesso integrada para [milhares de aplicações](https://azure.microsoft.com/marketplace/active-directory/) na Galeria de aplicações, incluindo o Salesforce, Google Apps, caixa, Concur e muito mais.

Azure AD também inclui um conjunto completo de [capacidades de gestão de identidade](https://docs.microsoft.com/azure/security/security-identity-management-overview#security-monitoring-alerts-and-machine-learning-based-reports) incluindo [autenticação multifator](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication), [registo de dispositivos]( https://docs.microsoft.com/azure/active-directory/active-directory-device-registration-overview), [ gestão de palavra-passe self-service](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/), [gestão de grupos self-service](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password), [privilegiadas de gestão de contas](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure), [controlo de acesso baseado em funções](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is), [monitorização da utilização de aplicações](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health), [auditoria avançada](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs), e [monitorização e alertas de segurança](https://docs.microsoft.com/azure/operations-management-suite/oms-security-responding-alerts).

Com o Azure Active Directory, todas as aplicações publicar para os parceiros e clientes (ou empresariais consumidor) possui a mesma identidade capacidades de gestão de acesso. Isto permite-lhe reduzir significativamente os custos operacionais.

## <a name="azure-security-center"></a>Centro de Segurança do Azure
O [Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-get-started) ajuda-o a evitar, detetar e responder a ameaças com uma maior visibilidade e controlo sobre a segurança dos seus recursos Azure. Fornece gestão de políticas e monitorização de segurança integrada nas suas subscrições, ajuda a detetar ameaças que caso contrário podem passar despercebidas e funciona com um ecossistema abrangente de soluções de segurança.

[Centro de segurança](https://docs.microsoft.com/azure/security-center/security-center-linux-virtual-machine) ajuda a salvaguardar dados da máquina virtual no Azure através da monitorização de ameaças e fornecendo visibilidade para as definições de segurança da sua máquina virtual. O Centro de Segurança pode monitorizar as máquinas virtuais relativamente a:

-   Definições de segurança do Sistema Operativo (OS) com as regras de configuração recomendadas
-   Segurança do sistema e atualizações críticas em falta
-   Recomendações do Endpoint protection
-   Validação de encriptação do disco
-   Ataques baseados na rede

Centro de segurança do Azure utiliza [controlo de acesso baseado em funções (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure), que fornece [funções incorporadas](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) que podem ser atribuídos a utilizadores, grupos e serviços no Azure.

Centro de segurança avalia a configuração dos seus recursos para identificar problemas de segurança e vulnerabilidades. No Centro de segurança, verá apenas informações relacionadas com a um recurso quando são atribuídas a função de proprietário, Contribuidor ou leitor para a subscrição ou grupo de recursos que um recurso pertence.

>[!Note]
>Consulte [permissões no Centro de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-permissions) para saber mais sobre as funções e as ações permitidas no Centro de segurança.

Centro de segurança utiliza o Microsoft Monitoring Agent – este é o mesmo agente utilizado pelo serviço do Operations Management Suite e análise de registos. Dados recolhidos a partir deste agente estão armazenados em qualquer uma análise de registos existente [área de trabalho](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access) associado à sua subscrição do Azure ou um workspace(s) nova, tendo em conta a geolocalização da VM.

## <a name="azure-monitor"></a>Azure Monitor
Problemas de desempenho na sua aplicação em nuvem podem afetar a sua empresa. Com os vários componentes interligados e versões frequentes, degradations pode acontecer em qualquer altura. E se estiver a desenvolver uma aplicação, os utilizadores, geralmente, detetar problemas que não foi encontrado no teste. Deve conhecer sobre estes problemas imediatamente e tem as ferramentas para diagnosticar e corrigir os problemas.

[Monitor do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) ferramenta básico para monitorizar os serviços em execução no Azure. Proporciona ao nível da infraestrutura dados sobre o débito de um serviço e o ambiente surrounding. Se estiver a gerir as suas aplicações tudo no Azure, decidir se pretende aumentar ou reduzir verticalmente de recursos, em seguida, o Monitor de Azure dá-lhe a utilizar para iniciar.

Além disso, pode utilizar dados de monitorização para obter conhecimentos aprofundados sobre a sua aplicação. Conhecimentos podem ajudar a melhorar o desempenho da aplicação ou maintainability ou automatizar ações que caso contrário necessitem intervenção manual. Inclui:

-   Registo de atividade do Azure
-   Registos de diagnóstico do Azure
-   Métricas
-   Diagnóstico do Azure

### <a name="azure-activity-log"></a>Registo de atividade do Azure
É um registo que fornece informações sobre as operações que foram executadas no recursos na sua subscrição. O [registo de atividade](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) era anteriormente conhecida como "Registos de auditoria" ou "Registos operacionais," uma vez que os relatórios de eventos de controlo plane para as suas subscrições.

### <a name="azure-diagnostic-logs"></a>Registos de diagnóstico do Azure
[Os registos de diagnóstico do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) são emitidos por um recurso e fornecer dados avançados, frequentes sobre o funcionamento desse recurso. O conteúdo estes registos varia consoante o tipo de recurso.

Por exemplo, registos de sistema de eventos do Windows são uma categoria de registo de diagnóstico para VMs e blob, tabela e fila registos são categorias de registos de diagnóstico para contas de armazenamento.

Registos de diagnóstico é diferente do [registo de atividade (anteriormente conhecido como registo de auditoria ou registo operacional)](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). O registo de atividade fornece informações sobre as operações que foram executadas no recursos na sua subscrição. Registos de diagnóstico fornecem informações aprofundadas operations que o seu recurso efetuadas em si.

### <a name="metrics"></a>Métricas
Monitor do Azure permite-lhe consumir telemetria ganhar visibilidade sobre o desempenho e estado de funcionamento das suas cargas de trabalho no Azure. O tipo de dados de telemetria do Azure mais importante é as métricas (também denominadas de contadores de desempenho) emitidas pelo Azure mais recursos. Monitor do Azure fornece várias formas de configurar e consumir estes [métricas](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) para monitorização e resolução de problemas.

### <a name="azure-diagnostics"></a>Diagnóstico do Azure
É a capacidade no Azure que permite a recolha de dados de diagnóstico sobre uma aplicação implementada. Pode utilizar a extensão de diagnóstico de várias origens diferentes. São atualmente suportados [Web de serviço de nuvem do Azure e funções de trabalho](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service), [Virtual Machines do Azure](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service) com o Microsoft Windows, e [Service Fabric](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics).


## <a name="network-watcher"></a>Observador de Rede
Os clientes criar uma rede ponto a ponto no Azure através da orquestração e composição vários recursos de rede individuais, tais como VNet, ExpressRoute, Gateway de aplicação, balanceadores de carga e muito mais. A monitorização está disponível em cada um dos recursos da rede.

Rede ponto a ponto pode ter configurações complexas e as interações entre os recursos, criar cenários complexos que necessitam de baseada em cenários de monitorização através do observador de rede.

[Observador de rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) será simplifica a monitorização e diagnóstico da rede do Azure. Ferramentas de diagnóstico e visualização disponíveis com a ativação do observador de rede que seja remoto pacote captura uma Máquina Virtual no Azure, obter informações sobre a sua rede através de registos de fluxo de tráfego e diagnosticar ligações e Gateway de VPN.

Observador de rede atualmente tem as seguintes capacidades:

- [Topologia](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview) -fornece uma vista de nível de rede com as várias interconnections e as associações entre recursos de rede num grupo de recursos.
-   [Captura de pacotes variável](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) -captura de dados do pacote e para uma máquina virtual. Opções de filtragem avançadas e controlos otimizados, tais como a capacidade de definir a hora e limitações de tamanho fornecem versatility. Os dados de pacote podem ser armazenados num arquivo de blob ou no disco local no formato .cap.
-   [Certifique-se de fluxos IP](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) -verifica se a um pacote é permitido ou negado com base nos parâmetros de pacotes de 5 cadeias de identificação do fluxo informações (IP de destino, IP de origem, porta de destino, porta de origem e protocolo). Se o pacote é negado por um grupo de segurança, a regra e o grupo negado o pacote é devolvido.
-   [Próximo salto](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) -determina o salto seguinte para pacotes a ser encaminhados na infraestrutura de rede do Azure, permitindo-lhe diagnosticar qualquer configurada incorretamente rotas definidas pelo utilizador.
-   [Vista do grupo de segurança](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview) -obtém as regras de segurança eficaz e aplicados que são aplicadas numa VM.
-   [O registo de fluxo de NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) -registos de fluxo para grupos de segurança de rede permitem-lhe capturar registos relacionados com o tráfego que são permitidas ou negadas pelas regras de segurança no grupo. O fluxo é definido por uma informação de 5 cadeias de identificação – IP de origem, IP de destino, porta de origem, porta de destino e protocolo.
-   [Gateway de rede virtual e a resolução de problemas de ligação](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest) -fornece a capacidade de resolução de problemas de Gateways da Virtual Network e ligações.
-   [Limites de subscrição de rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) -permite-lhe ver utilização de recursos de rede contra os limites.
-   [Configurar registo de diagnóstico](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) – disponibiliza um painel único para ativar ou desativar os registos de diagnóstico para recursos de rede num grupo de recursos.

Para obter mais informações, como configurar a rede, consulte observador [configurar observador de rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-create).

## <a name="developer-operations-devops"></a>Operações de programador (DevOps)
Antes de desenvolvimento de aplicações de DevOps, equipas foram responsável pela recolha necessidades comerciais para um programa de software e escrever código. Em seguida, uma equipa separada de pergunta e resposta testa o programa num ambiente de desenvolvimento isolado, se os requisitos foram satisfeitos e liberta o código para operações de implementar. As equipas de implementação são mais fragmentadas em grupos siloed como redes e da base de dados. Sempre que um programa de software é "emitido através do padrão de fundo" para uma equipa independente adiciona congestionamentos.

[DevOps](https://www.visualstudio.com/learn/what-is-devops/) permite equipas para fornecer soluções mais seguras, superior qualidade mais rápidas e mais barata. Os clientes esperam uma experiência fiável e dinâmica quando a consumir os serviços e software.  Equipas tem iterar rapidamente as atualizações de software, o impacto das atualizações e responder rapidamente com novo desenvolvimento iterações para resolver problemas ou forneça mais valor.  Plataformas de cloud, como o Microsoft Azure tem removido congestionamentos tradicionais e ajudou commoditize infraestrutura. Software reigns em todas as empresas como o principal diferenciador e fator os resultados de negócios. Nenhuma organização, programador ou de trabalho IT pode ou deve evitar o movimento de DevOps.

Verão de DevOps madura adotar várias as seguintes práticas. Estas práticas [envolvem pessoas](https://www.visualstudio.com/learn/what-is-devops-culture/) para estratégias de formulário com base nos cenários de negócios.  Ferramentas podem ajudá-lo a automatizar os vários recomendados:

-   [Gestão de planeamento e projeto seja ágil](https://www.visualstudio.com/learn/what-is-agile/) técnicas são utilizadas para planear e isolar o trabalho em sprints, gerir a capacidade de equipa e ajudar equipas adaptar rapidamente às necessidades empresarias.
-   [Controlo de versão, normalmente, com o Git](https://www.visualstudio.com/learn/what-is-git/), permite que as equipas localizadas em qualquer local no mundo de partilhar a origem e integrar com ferramentas de desenvolvimento de software para automatizar o pipeline de versão.
-   [Integração contínua](https://www.visualstudio.com/learn/what-is-continuous-integration/) unidades a intercalação em curso e o teste de código, o que conduz a localizar defeitos antecipadamente.  Outras vantagens incluem menos tempo desperdiçado no fighting problemas de intercalação e rápido comentários para as equipas de desenvolvimento.
-   [Entrega contínua](https://www.visualstudio.com/learn/what-is-continuous-delivery/) das soluções de software para produção e teste de ambientes de ajuda as organizações rapidamente corrigir os erros e responder a alguma alteração requisitos de negócio.
-   [Monitorização](https://www.visualstudio.com/learn/what-is-monitoring/) das aplicações, incluindo ambientes de produção para o estado de funcionamento de aplicações como em execução bem como o formulário do cliente utilização ajuda as organizações uma hipótese e rapidamente validar ou disprove estratégias.  Dados avançada são capturados e armazenados em vários formatos de registo.
-   [Infraestrutura como código (IaC)](https://www.visualstudio.com/learn/what-is-infrastructure-as-code/) é uma prática recomendada, que permite a automatização e a validação da criação e teardown de redes e máquinas virtuais para ajudar a entrega de aplicações seguras e estável plataformas de alojamento.
-   [Micro-serviços](https://www.visualstudio.com/learn/what-are-microservices/) arquitetura é utilizada para isolar os casos de utilização empresarial para os serviços de reutilizáveis pequenos.  Esta arquitetura ativa escalabilidade e a eficiência.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre a solução de auditoria e segurança do OMS, consulte os artigos seguintes:

- [Operations Management Suite | Segurança e conformidade](https://www.microsoft.com/cloud-platform/security-and-compliance).
- [Monitorizar e responder a alertas de segurança no Operations Management Suite segurança e a solução de auditoria](https://docs.microsoft.com/en-us/azure/operations-management-suite/oms-security-responding-alerts).
- [A monitorização de recursos no Operations Management Suite segurança e a solução de auditoria](https://docs.microsoft.com/en-us/azure/operations-management-suite/oms-security-monitoring-resources).
