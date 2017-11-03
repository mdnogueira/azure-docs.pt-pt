---
title: "Azure melhores práticas de segurança operacional | Microsoft Docs"
description: "Este artigo fornece um conjunto de melhores práticas de segurança operacionais do Azure."
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
ms.date: 08/04/2017
ms.author: tomsh
ms.openlocfilehash: ed3c4bf2dcdda3e04a18682da568ae73f41bb37d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-operational-security-best-practices"></a>Azure melhores práticas de segurança operacional
Segurança operacionais do Azure refere-se os serviços, controlos e funcionalidades disponíveis para os utilizadores para proteger os seus dados, aplicações e outros recursos no Microsoft Azure. Segurança operacionais do Azure é construída numa estrutura que incorpora o conhecimento adquirido através de várias funcionalidades que são exclusivas para a Microsoft, incluindo o Microsoft Security Development Lifecycle (SDL), o programa Microsoft Security Response Center e a deteção profunda de ameaças atuais.

Neste artigo, discutimos a uma coleção de práticas recomendadas de segurança de base de dados do Azure. Estas melhores práticas são derivadas da nossa experiência com a segurança da base de dados do Azure e as experiências dos clientes, como por si.

Para cada melhor prática, vamos explicar:
-   O que é a melhor prática
-   Por que motivo que pretende ativar essa recomendado
-   Se falhar ativar a melhor prática, que poderá ser o resultado
- Como pode saber mais ativar a melhor prática

Este artigo Azure operacional melhores práticas de segurança baseia-se no opinião um consenso e capacidades da plataforma do Azure e conjuntos de funcionalidades, tal como existem no momento que este artigo foi escrito. Opinions e tecnologias alteram ao longo do tempo e este artigo será atualizado regularmente para refletir essas alterações.

Azure operacional melhores práticas de segurança abordadas neste artigo incluem:

-   Monitorizar, gerir e proteger a infraestrutura de nuvem
-   Gerir identidades e implementa início de sessão único (SSO)
-   Pedidos de rastreio, analisar tendências de utilização e diagnosticar problemas
-   Monitorizar os serviços com uma solução de monitorização centralizada
-   Evitar, detetar e responder a ameaças
-   Monitorização de rede com base no cenário de ponto a ponto
-   Implementação segura utilizando ferramentas de DevOps comprovadas

## <a name="monitor-manage-and-protect-cloud-infrastructure"></a>Monitorizar, gerir e proteger a infraestrutura de nuvem
Operações de TI é responsável por gerir a infraestrutura de centro de dados, aplicações e dados, incluindo a estabilidade e segurança destes sistemas. No entanto, a obter conhecimentos de segurança aprofundados em aumentar complexas ambientes de TI, muitas vezes, requer que as organizações cobble em conjunto de dados a partir de vários sistemas de gestão e segurança.

[Microsoft Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) é baseado na nuvem IT solução de gestão da Microsoft que o ajuda a gerir e proteger no local e a infraestrutura de nuvem.

[Solução de auditoria e segurança do OMS](https://docs.microsoft.com/azure/operations-management-suite/oms-security-monitoring-resources) permite a TI ativamente monitorizar todos os recursos, o que podem ajudar a minimiza o impacto de incidentes de segurança. Auditoria e segurança do OMS tem domínios de segurança que podem ser utilizados para monitorizar os recursos.

Para mais informações sobre o OMS, leia o artigo [Operations Management Suite](https://technet.microsoft.com/library/mt484091.aspx).

Para o ajudar a evitar, detetar e responder a ameaças, [segurança Operations Management Suite (OMS) e a solução de auditoria](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) recolhe e processa dados sobre os recursos, que inclui:

-   Registo de eventos de segurança
-   Eventos de Rastreio de Eventos para o Windows (ETW)
-   Eventos de auditoria do AppLocker
-   Registo de Firewall do Windows
-   Eventos de Análise de Ameaças Avançada
-   Resultados da avaliação de linha de base
-   Resultados da avaliação de antimalware
-   Resultados da avaliação de atualização/correção
-   Fluxos de syslog explicitamente estão ativados no agente


## <a name="manage-identity-and-implement-single-sign-on"></a>Gerir identidades e implementar o início de sessão único
[Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) é o diretório de nuvem multi-inquilino, com base da Microsoft e o serviço de gestão de identidade.

[Azure AD](https://azure.microsoft.com/services/active-directory/) também inclui um conjunto completo de [gestão de identidades](https://docs.microsoft.com/azure/security/security-identity-management-overview) capacidades, incluindo [autenticação multifator](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication), registo de dispositivos, gestão de palavra-passe self-service, gestão de grupos self-service, gestão de contas com privilégios, controlo de acesso baseado em funções, monitorização, avançada de auditoria e segurança monitorização e alertas de utilização da aplicação.

As seguintes capacidades podem ajudar a proteger aplicações baseado na nuvem, simplificar processos de TI, cortar os custos e ajudar a garantir que são cumpridos os objetivos de conformidade empresarial:

-   Gestão de identidades e acessos para a cloud
-   Simplificar o acesso dos utilizadores a qualquer aplicação na cloud
-   Proteger aplicações e dados confidenciais
-   Proporcionar autonomia aos empregados
-   Integrar com o Azure Active Directory

### <a name="identity-and-access-management-for-the-cloud"></a>Gestão de identidades e acessos para a cloud
Azure Active Directory (Azure AD) é um abrangentes [solução de nuvem de gestão de identidades e acessos](https://www.microsoft.com/cloud-platform/identity-management), que lhe oferece um conjunto robusto de funcionalidades para gerir utilizadores e grupos. Ajuda a proteger o acesso no local e nuvem aplicações, incluindo os serviços de web do Microsoft como o Office 365 e o software que não sejam da Microsoft como um aplicações de serviço (SaaS).
Para mais informações como ativar a proteção de identidade no Azure AD, consulte [ativar o Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-enable).

### <a name="simplify-user-access-to-any-cloud-app"></a>Simplificar o acesso dos utilizadores a qualquer aplicação na cloud
[Ativar o início de sessão único](https://docs.microsoft.com/azure/active-directory/active-directory-sso-integrate-saas-apps) para simplificar o acesso de utilizador para milhares de aplicações em nuvem de dispositivos Windows, Mac, Android e iOS. Os utilizadores podem iniciar aplicações a partir de um painel de acesso personalizados baseados na web ou aplicação móvel, utilizando as credenciais da empresa. Utilize o módulo de Proxy de aplicações do Azure AD para ponha aplicações SaaS e publicar aplicações web no local, para fornecer acesso remoto altamente seguro e o início de sessão único.

### <a name="protect-sensitive-data-and-applications"></a>Proteger aplicações e dados confidenciais
Ativar [Azure multi-factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) para impedir o acesso não autorizado no local e as aplicações em nuvem, fornecendo um nível adicional de autenticação. Proteja o seu negócio e atenue potenciais ameaças com monitorização de segurança, alertas e relatórios baseados em aprendizagem automática que identificam padrões de acesso inconsistentes.

### <a name="enable-self-service-for-your-employees"></a>Proporcionar autonomia aos empregados
Delegue tarefas importantes aos seus funcionários, como a reposição de palavras-passe e a criação e gestão de grupos. [Ativar a alteração de palavra-passe self-service](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password), repor e self-service grupo de gestão com o Azure AD.

### <a name="integrate-with-azure-active-directory"></a>Integrar com o Azure Active Directory
Expandir [do Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-to-integrate) e qualquer outro local diretórios para o Azure AD para ativar o início de sessão para todas as aplicações baseadas na nuvem. Os atributos do utilizador podem ser sincronizados automaticamente com o diretório na cloud a partir de variadíssimos tipos de diretórios no local.

Para saber mais sobre a integração do Azure Active Directory e como ativá-la, leia o artigo [integrar os diretórios no local ao Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

## <a name="trace-requests-analyze-usage-trends-and-diagnose-issues"></a>Pedidos de rastreio, analisar tendências de utilização e diagnosticar problemas
[Análise de armazenamento do Azure](https://docs.microsoft.com/azure/storage/storage-analytics) efetua o registo e fornece dados de métricas para uma conta de armazenamento. Pode utilizar estes dados para rastrear pedidos, analisar tendências de utilização e diagnosticar problemas relacionados com a sua conta de armazenamento.

As métricas do Storage Analytics estão ativadas por predefinição para novas contas de armazenamento. Pode ativar o registo e configurar as métricas e registo no portal do Azure; Para obter mais informações, consulte [monitorizar uma conta de armazenamento no portal do Azure](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account). Também pode ativar a análise de armazenamento através de programação através da API REST ou biblioteca de clientes. Utilize a operação definir propriedades de serviço para ativar a análise de armazenamento individualmente para cada serviço.

Para obter um guia aprofundado sobre a utilização de análise de armazenamento e outras ferramentas para identificar, diagnosticar e resolver problemas relacionados com o Storage do Azure, consulte [monitorizar, diagnosticar e resolver problemas de armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/storage/storage-monitoring-diagnosing-troubleshooting).

Para saber mais sobre a integração do Azure Active Directory e como ativá-la, leia o artigo [ativar e configurar a análise de armazenamento](https://docs.microsoft.com/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics?redirectedfrom=MSDN).

## <a name="monitoring-services"></a>Serviços de monitorização
Aplicações em nuvem são complexas com várias partes mover. A monitorização fornece dados para se certificar de que a aplicação permanece cópias de segurança e em execução em bom estado. Também o ajuda a stave desativar potenciais problemas ou resolver passado aqueles. Além disso, pode utilizar dados de monitorização para obter conhecimentos aprofundados sobre a sua aplicação. Conhecimentos podem ajudar a melhorar o desempenho da aplicação ou maintainability ou automatizar ações que caso contrário necessitem intervenção manual.

### <a name="monitor-azure-resources"></a>Monitorizar os recursos do Azure
[Monitor do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started) é o serviço de plataforma que fornece uma única origem para monitorizar os recursos do Azure. Com o Azure Monitor, pode visualizar, consultar, encaminhar, arquivar e tomar medidas relativamente a métricas e registos provenientes de recursos no Azure. Pode trabalhar com estes dados utilizando o painel do portal do Monitor, [Cmdlets do PowerShell do Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-powershell-samples), a [CLI de Várias Plataformas](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-cli-samples)ou [APIs REST do Azure Monitor](https://msdn.microsoft.com/library/dn931943.aspx).

### <a name="enable-autoscale-with-azure-monitor"></a>Ativar o dimensionamento automático com a monitorização do Azure
Ativar [dimensionamento automático de Monitor do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-autoscale-get-started) só se aplica a conjuntos de dimensionamento de máquina virtual (VMSS), cloud services, planos de serviço de aplicações e ambientes do app service.

### <a name="manage-roles-permissions-and-security"></a>Gerir funções de segurança e permissões
Várias equipas necessário estritamente [regular o acesso à monitorização](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security) dados e definições. Por exemplo, se tiver os membros da equipa que trabalham exclusivamente no monitorização (engenheiros de suporte, os engenheiros de devops) ou se utilizar um fornecedor de serviço geridas, poderá querer conceder-lhes acesso a dados de monitorização apenas ao restringir a capacidade de criar, modificar ou eliminar recursos.

Isto mostra como rapidamente aplicam-se uma função RBAC monitorização incorporada a um utilizador no Azure ou criar a sua própria função personalizada para um utilizador que tem permissões de monitorização limitadas. Em seguida, explica como considerações de segurança para os recursos relacionados com o Monitor do Azure e como pode limitar o acesso a dados que contêm.

## <a name="prevent-detect-and-respond-to-threats"></a>Evitar, detetar e responder a ameaças
A deteção de ameaças do Centro de segurança funciona através da recolha automática de informações de segurança de recursos do Azure, rede e soluções de parceiros ligadas. -Analyses desta informação, muitas vezes correlacionando informações de várias origens, para identificar ameaças. Os alertas de segurança são prioritários no Centro de Segurança, juntamente com recomendações sobre como remediar a ameaça.

-   [Configurar uma política de segurança](https://docs.microsoft.com/azure/security-center/security-center-policies) para a sua subscrição do Azure.
-   Utilize o [recomendações no Centro de segurança](https://docs.microsoft.com/azure/security-center/security-center-recommendations) para o ajudar a proteger os seus recursos do Azure.
-   Reveja e gerir o seu atual [alertas de segurança](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts).

O [Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) ajuda-o a evitar, detetar e responder a ameaças com uma maior visibilidade e controlo sobre a segurança dos seus recursos Azure. Fornece gestão de políticas e monitorização de segurança integrada nas suas subscrições do Azure, ajuda a detetar ameaças que caso contrário podem passar despercebidas e funciona com um ecossistema abrangente de soluções de segurança.

O Centro de Segurança proporciona uma prevenção e deteção de ameaças eficiente e de fácil utilização, bem como capacidades de resposta que estão incorporadas no Azure. As principais capacidades são:

-   Compreender o estado de segurança da nuvem
-   Controle a segurança da cloud
-   Implemente facilmente soluções de segurança da cloud integradas
-   Detete ameaças e responda com rapidez

### <a name="understand-cloud-security-state"></a>Compreender o estado de segurança da nuvem
Utilize o Centro de Segurança do Azure para obter uma visão central do estado de segurança de todos os seus recursos do Azure. De forma rápida, certifique-se de que os controlos de segurança adequados são cumpridos e corretamente configurado e identificar rapidamente quaisquer recursos, que requerem atenção.

### <a name="take-control-of-cloud-security"></a>Controle a segurança da cloud
Definir [políticas de segurança](https://docs.microsoft.com/azure/security-center/security-center-policies) para as suas subscrições do Azure, de acordo com as necessidades de segurança de nuvem da sua empresa, e adaptada para o tipo de aplicações ou sensibilidade dos dados em cada subscrição. Utilize recomendações orientadas por políticas para guiar os proprietários de recursos ao longo do processo de implementação dos controlos necessários: elimine da segurança da cloud as conjeturas.

### <a name="easily-deploy-integrated-cloud-security-solutions"></a>Implemente facilmente soluções de segurança da cloud integradas
[Permitir soluções de segurança](https://docs.microsoft.com/azure/security-center/security-center-partner-integration) da Microsoft e respetivos parceiros, incluindo antimalware e firewalls líder da indústria. Utilize um aprovisionamento simplificado para implementar soluções de segurança. Até mesmo as alterações de rede são configuradas para si. Os seus eventos de segurança de soluções de parceiros são automaticamente recolhidos para análise e alerta.

### <a name="detect-threats-and-respond-fast"></a>Detete ameaças e responda com rapidez
Esteja um passo à frente das ameaças atuais e emergentes que surgem na cloud com uma abordagem integrada e voltada para a análise. Ao combinar o Microsoft global [ameaça intelligence](https://docs.microsoft.com/azure/security-center/security-center-detection-capabilities) e conhecimentos, com informações sobre eventos relacionados com a segurança da nuvem entre as implementações do Azure, o Centro de segurança ajuda-o a detetar ameaças reais numa fase inicial e reduzir os falsos positivos. Alertas de segurança de nuvem dão-lhe informações sobre a campanha de ataque, incluindo eventos relacionados e os recursos afetados e sugerir formas de resolver problemas e a recuperação rápida.

## <a name="end-to-end-scenario-based-network-monitoring"></a>Monitorização de rede com base no cenário de ponto a ponto
Os clientes criar uma rede ponto a ponto no Azure através da orquestração e composição vários recursos de rede individuais, tais como VNet, ExpressRoute, Gateway de aplicação, balanceadores de carga e muito mais. A monitorização está disponível em cada um dos recursos da rede.

[Observador de rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) é um serviço regional que lhe permite monitorizar e diagnosticar condições num cenário de rede nível, para e do Azure. Diagnóstico de rede e ferramentas de visualização disponíveis com o observador de rede ajudam-na compreender, diagnosticar e obter informações sobre a sua rede no Azure.

### <a name="automate-remote-network-monitoring-with-packet-capture"></a>Automatize a monitorização remota de redes com a captura de pacotes
Monitorize e diagnostique problemas de rede sem ter de iniciar sessão nas suas máquinas virtuais (VMs) com o Observador de Rede. Acionador [captura de pacotes](https://docs.microsoft.com/azure/network-watcher/network-watcher-alert-triggered-packet-capture) ao definir alertas e obter acesso a informações de desempenho em tempo real ao nível do pacote. Quando se deparar com um problema, pode investigar em pormenor para obter melhores diagnósticos.

### <a name="gain-insight-into-your-network-traffic-using-flow-logs"></a>Obtenha informações sobre o seu tráfego de rede com os registos de fluxo
Criar uma compreensão mais aprofundada do seu tráfego de rede padrão com [registos de fluxo do grupo de segurança de rede](https://docs.microsoft.com/en-us/azure/network-watcher/network-watcher-nsg-flow-logging-overview). Informações fornecidas pelo fluxo registos ajudam a recolher dados de conformidade, auditoria e monitorização do seu perfil de segurança de rede.

### <a name="diagnose-vpn-connectivity-issues"></a>Diagnostique problemas da conectividade VPN
Observador de rede fornece a capacidade de [diagnosticar os problemas mais comuns de Gateway de VPN e ligações](https://docs.microsoft.com/azure/network-watcher/network-watcher-diagnose-on-premises-connectivity). Permitir que não só para identificar o problema, mas também para utilizar os registos de detalhado criados para o ajudar a continuar a investigar.

Para obter mais informações sobre como configurar o observador de rede e como ativá-la, leia o artigo [configurar observador de rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-create).

## <a name="secure-deployment-using-proven-devops-tools"></a>Implementação segura utilizando ferramentas de DevOps comprovadas
Estas são algumas da lista de Azure DevOps práticas neste espaço Microsoft Cloud, o que faz com que as empresas e equipas produtivos e eficiente.

-   **Infraestrutura como código (IaC):** infraestrutura como o código é um conjunto de técnicas e práticas, que ajudam os profissionais de TI remova a carga associada a compilação do dia a dia e a gestão da infraestrutura modular. Permite que os profissionais de TI criar e manter o respetivo ambiente server moderno de uma forma que é semelhante a forma como os programadores de software criar e manter o código da aplicação. Para o Azure, temos [do Azure Resource Manager]( https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) permite-lhe aprovisionar das suas aplicações com um modelo declarativo. Num único modelo, pode implementar vários serviços, bem como as respetivas dependências. Pode utilizar o mesmo modelo para implementar repetidamente a sua aplicação durante cada fase do ciclo de vida da aplicação.
-   **A integração e a implementação contínua:** pode configurar os Visual Studio Online projetos de equipa para [criar e implementar automaticamente](https://www.visualstudio.com/docs/build/overview) para aplicações web do Azure ou serviços em nuvem. VSO implementa automaticamente os binários depois de efetuar uma compilação para o Azure após cada dar entrada no código. O processo de criação de pacote descrito aqui é equivalente ao comando de pacote no Visual Studio e os passos de publicação são equivalentes ao comando Publicar no Visual Studio.
-   **Gestão de versões:** Visual Studio [versão gestão](https://msdn.microsoft.com/library/vs/alm/release/overview) é uma excelente solução para automatizar a implementação de vários fase e gerir o processo de versão. Crie pipelines geridos a implementação contínua para libertar rapidamente, facilmente e frequentemente. Com a gestão de versão, é muito pode automatizar o processo nossa versão e pode ter predefinidas fluxos de trabalho de aprovação. Implementar no local e na nuvem, expandir e personalizar conforme necessário.
-   **Monitorização de desempenho da aplicação:** detetar problemas, resolver problemas e melhorar continuamente as suas aplicações. Diagnostique rapidamente quaisquer problemas na sua aplicação em direto. Compreenda o que os utilizadores fazem com isso. A configuração é fácil independentemente de adicionar código JS e uma entrada de webconfig e verá resultados dentro de minutos no portal com todos os detalhes. [Insights aplicação](https://azure.microsoft.com/documentation/articles/app-insights-start-monitoring-app-health-usage/) ajuda as empresas deteção mais rápidos de problemas de & remediação.
-   **Carregar teste & dimensionamento automático:** pode encontrar problemas de desempenho na nossa aplicação para melhorar a qualidade de implementação e para se certificar de que a nossa aplicação está sempre disponível para se adaptar a empresa ou se precisa. Certifique-se a que sua aplicação pode processar o tráfego para a sua campanha de início ou de marketing seguinte. Iniciar a execução de baseado na nuvem [carregar testes](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing) em quase sem tempo com o Visual Studio Online.

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [segurança operacionais do Azure](https://docs.microsoft.com/azure/security/azure-operational-security).
- Para saber mais [Operations Management Suite | Segurança e conformidade](https://www.microsoft.com/cloud-platform/security-and-compliance).
- [Introdução ao Operations Management Suite segurança e a solução de auditoria](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started).
