---
title: "A deteção de ameaças de avançadas do Azure | Microsoft Docs"
description: "Saiba mais sobre proteção de identidade e as respetivas capacidades."
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: d2fab26d8ff9f006cfed82685a738b791d0b0624
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2017
---
# <a name="azure-advanced-threat-detection"></a>Deteção de ameaças avançadas do Azure
## <a name="introduction"></a>Introdução

### <a name="overview"></a>Descrição geral

Microsoft desenvolveu uma série de documentos técnicos, descrições gerais de segurança, as melhores práticas e listas de verificação para o ajudar a clientes do Azure sobre as diversas relacionadas com segurança capacidades disponíveis no e envolvente a plataforma do Azure. Os tópicos em termos de volume e a profundidade de intervalo e são atualizados periodicamente. Este documento faz parte da série, conforme resumido na secção seguinte abstracta.

### <a name="azure-platform"></a>Plataforma do Azure

Azure é uma plataforma de serviço de nuvem pública que suporte a seleção mais amplas de sistemas operativos, programação idiomas, estruturas, ferramentas, bases de dados e dispositivos.
Suporta as seguintes linguagens de programação:
-   Execute os contentores de Linux com a integração do Docker.
-   Criar aplicações com JavaScript, Python, .NET, PHP, Java e Node.js
-   Compilação back-ends para iOS, Android e Windows dispositivos.

Serviços em nuvem pública do Azure suportam as mesmas tecnologias milhões de programadores e profissionais de TI já dependem e de confiança.

Quando estiver a migrar para uma nuvem pública com uma organização que organização é da responsabilidade de proteger os seus dados e fornecer governação em torno do sistema e segurança.

A infraestrutura do Azure foi concebida a partir da facilidade que as aplicações têm para alojar milhões de clientes em simultâneo e proporciona uma fundação fidedigna com a qual as empresas podem satisfazer as suas necessidades de segurança. O Azure oferece uma grade diversidade de opções para configurar e personalizar a segurança para cumprir os requisitos das suas implementações de aplicações. Este documento ajuda-o a cumpra estes requisitos.

### <a name="abstract"></a>Abstrato

Incorporada na funcionalidade de deteção de ameaças avançadas através dos serviços de ofertas de Microsoft Azure, como o Azure Active Directory, Azure Operations Management Suite (OMS) e o Centro de segurança do Azure. Esta coleção de serviços de segurança e as capacidades de uma forma simples e rápida para compreender o que acontece no seu implementações do Azure.

Este documento técnico irá ajudá-lo a "Microsoft Azure se aproxima" para o diagnóstico de vulnerabilidade de ameaças e analysing o risco associadas com as atividades maliciosas direcionadas contra servidores e outros recursos do Azure. Isto ajuda a identificar os métodos de gestão de identificação e a vulnerabilidade com soluções otimizadas pela plataforma do Azure e os orientado para o cliente de serviços de segurança e as tecnologias.

Este documento técnico centra-se na tecnologia de plataforma do Azure e controlos de orientado para o cliente e não tenta endereço SLA, considerações de prática de DevOps e modelos de preços.

## <a name="azure-active-directory-identity-protection"></a>Azure Active Directory Identity Protection

![Azure Active Directory Identity Protection](./media/azure-threat-detection/azure-threat-detection-fig1.png)


[Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) é uma funcionalidade do [do Azure AD Premium P2](https://docs.microsoft.com/azure/active-directory/active-directory-editions) edição que fornece uma descrição geral dos eventos de risco e potenciais vulnerabilidades que afetam as identidades da organização. Microsoft tem sido proteger identidades baseado na nuvem para através de um decade e com o Azure AD Identity Protection, Microsoft está efetuar estes sistemas de proteção mesmo disponíveis para os clientes empresariais. A proteção de identidade utiliza as capacidades de deteção de anomalias existente do Azure AD estão disponíveis através de [relatórios de atividade anómala do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-view-access-usage-reports#anomalous-activity-reports)e apresenta novos tipos de eventos de risco que consegue de detetar anomalias em tempo real.

Proteção de identidade utiliza algoritmos de aprendizagem adaptável e heurística para detetar anomalias e o risco de eventos que podem indicar que uma identidade foi comprometida. Utilizando estes dados, Identity Protection gera relatórios e alertas que permitem-lhe investigar estes eventos de risco e efetuar remediação adequada ou a ação de mitigação.

Mas o Azure Active Directory Identity Protection é maior do que uma ferramenta de monitorização e relatórios. Com base em eventos de risco, Identity Protection calcula um nível de risco de utilizador para cada utilizador, permitindo-lhe configurar as políticas baseadas em risco para proteger automaticamente as identidades da sua organização.

Estas políticas baseadas em risco, além de outros [controlos de acesso condicional](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access) fornecidos pelo Azure Active Directory e [EMS](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access), automaticamente pode bloquear ou oferecem ações de remediação adaptável que incluem reposições de palavra-passe e a imposição de autenticação multifator.

### <a name="identity-protections-capabilities"></a>Funcionalidades de Identity Protection

Azure Active Directory Identity Protection é maior do que uma monitorização e a ferramenta de relatórios. Para proteger as identidades da organização, pode configurar as políticas baseadas em risco que respondam automaticamente a problemas detetados quando for atingido a um nível de risco especificado. Automaticamente estas políticas, além de outros controlos de acesso condicional fornecidas pelo Azure Active Directory e o EMS, podem bloquear ou iniciar ações de remediação adaptável repõe a palavra-passe, incluindo e imposição de autenticação multifator.

Exemplos de algumas das formas à proteção de identidade do Azure pode ajudar a proteger as contas e identidades incluem:

[Detetar eventos de risco e contas de risco:](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#detection)
-   Detetar seis tipos de eventos de risco utilizando regras heurística e de aprendizagem
-   A calcular níveis de risco do utilizador
-   Fornecer recomendações personalizadas para melhorar a postura de segurança geral por realce vulnerabilidades

[Investigar os eventos de risco:](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#investigation)
-   Enviar notificações para eventos de risco
-   Investigar os eventos de risco utilizando informações relevantes e contextuais
-   Fornecer os fluxos de trabalho básicos para controlar as investigações
-   Fornecer o facilitar o acesso para ações de remediação, tais como a reposição de palavra-passe

[Políticas de acesso condicional baseado em risco:](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection#risky-sign-ins)
-   Política para mitigar o risco de inícios de sessão por bloquear inícios de sessão ou exigindo desafios de autenticação multifator.
-   Política para bloquear ou contas de utilizador de risco segura
-   Política para exigir que os utilizadores para se registar para autenticação multifator

### <a name="azure-ad-privileged-identity-management-pim"></a>Azure AD Privileged Identity Management (PIM)

Com [do Azure Active Directory (AD) Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure),

![Azure AD Privileged Identity Management](./media/azure-threat-detection/azure-threat-detection-fig2.png)

Pode gerir, controlar e monitorizar o acesso na sua organização. Isto inclui o acesso a recursos no Azure AD e outros serviços online da Microsoft, como o Office 365 ou o Microsoft Intune.

O Azure AD Privileged Identity Management ajuda-o:

-   Obter um alerta e elaborar relatórios sobre os administradores do Azure AD e "just in time" obter acesso administrativo ao Microsoft Online Services como o Office 365 e o Intune

-   Obter relatórios sobre o histórico de acesso de administrador e as alterações em atribuições de administrador

-   Obtenha alertas sobre o acesso a uma função com privilégios

## <a name="microsoft-operations-management-suite-oms"></a>Microsoft Operations Management Suite (OMS)

[Microsoft Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) é baseado na nuvem IT solução de gestão da Microsoft que o ajuda a gerir e proteger no local e a infraestrutura de nuvem. Quando o OMS estiver implementado como um serviço baseado na nuvem, pode colocá-lo em execução de imediato com o mínimo investimento em termos de serviços de infraestrutura. Novas funcionalidades de segurança são fornecidas automaticamente, guardar o seu manutenções correntes e atualizar os custos.

Além de fornecer serviços importantes no seu próprio, OMS pode integrar com componentes do System Center, tais como [System Center Operations Manager](https://blogs.technet.microsoft.com/cbernier/2013/10/23/monitoring-windows-azure-with-system-center-operations-manager-2012-get-me-started/) para expandir a sua segurança existente investimentos de gestão para a nuvem. O System Center e o OMS podem trabalhar em conjunto para fornecer uma experiência de gestão híbrida completa.

### <a name="holistic-security-and-compliance-posture"></a>A postura de conformidade e segurança holística

O [dashboard de auditoria e segurança do OMS](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) fornece uma vista abrangente da sua organização postura de segurança IT com consultas de pesquisa incorporadas para problemas relevantes que necessitam da sua atenção. O dashboard e auditoria de segurança é o ecrã principal para tudo relacionados com segurança no OMS. Fornece informações de alto nível sobre o estado de segurança dos computadores. Também inclui a capacidade de ver todos os eventos das últimas 24 horas, 7 dias ou qualquer outro intervalo de tempo personalizado.

Dashboards do OMS ajudá-lo rapidamente e compreender facilmente a postura de segurança global de qualquer outro ambiente, tudo dentro do contexto das operações de TI, incluindo: avaliação de atualização de software, avaliação de antimalware e linhas de base de configuração. Além disso, os dados de registo de segurança estão prontamente acessíveis para simplificar os processos de auditoria de segurança e conformidade.

O dashboard de Segurança e Auditoria do OMS está organizado em quatro categorias principais:

![Dashboard de Segurança e Auditoria do OMS](./media/azure-threat-detection/azure-threat-detection-fig3.jpg)

-   **Domínios de segurança:** nesta área, conseguirá explorar ainda mais os registos de segurança ao longo do tempo, aceder a avaliação de malware, atualizar a avaliação, segurança de rede, informações de identidade e acesso, os computadores com eventos de segurança e tivessem acesso rápido ao dashboard do Centro de segurança do Azure.

-   **Problemas relevantes:** esta opção permite-lhe identificar rapidamente o número de problemas de Active Directory e a gravidade destes problemas.

-   **As deteções (pré-visualização):** permite-lhe identificar padrões de ataque ao visualizar os alertas de segurança que possam ocorrer relativamente aos seus recursos.

-   **Ameaças:** permite-lhe identificar padrões de ataque ao visualizar o número total de servidores com tráfego IP malicioso de saída, o tipo de ameaça malicioso e um mapa que mostra onde são feitos destes IPs.

-   **Consultas de segurança comuns:** esta opção fornece uma lista de consultas de segurança mais comuns que pode utilizar para monitorizar o seu ambiente. Ao clicar destas consultas, é aberto o painel pesquisa com os resultados para essa consulta.

### <a name="insight-and-analytics"></a>Conhecimentos aprofundados e análise
No centro da [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) consiste no repositório do OMS, que está alojado na nuvem do Azure.

![Conhecimentos aprofundados e análise](./media/azure-threat-detection/azure-threat-detection-fig4.png)

Os dados são recolhidos no repositório a partir de origens ligadas mediante a configuração das origens de dados e da adição de soluções à sua subscrição.

![subscrição](./media/azure-threat-detection/azure-threat-detection-fig5.png)

As origens de dados e as soluções criarão, cada uma, diferentes tipos de registos que têm os seus próprios conjuntos de propriedades, mas que podem ser analisados em conjunto em consultas feitas no repositório. Desta forma, pode utilizar as mesmas ferramentas e métodos para trabalhar com diversos tipos de dados recolhidos por diferentes origens.


A maior parte da sua interação com a análise de registos é através do portal do OMS, que é executado em qualquer browser e fornece-lhe acesso a definições de configuração e várias ferramentas para analisar e atuar sobre dados recolhidos. No portal, pode utilizar [pesquisas de registo](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-searches) onde construir consultas para analisar os dados recolhidos, [dashboards](https://docs.microsoft.com/azure/log-analytics/log-analytics-dashboards), que pode personalizar com vistas gráficas da sua pesquisas mais importantes, e [soluções](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions), que fornecem ferramentas de análise e funcionalidades adicionais.

![ferramentas de análise](./media/azure-threat-detection/azure-threat-detection-fig6.png)

As soluções acrescentam funcionalidades ao Log Analytics. São fundamentalmente executadas na nuvem e proporcionam a análise dos dados recolhidos no repositório do OMS. Podem também definir novos tipos de registos a serem recolhidos e que podem ser analisados com as Pesquisas de Registos ou através de uma interface de utilizador adicional disponibilizada pela solução no dashboard do OMS.
Auditoria de segurança e é um exemplo destes tipos de soluções.



### <a name="automation--control-alert-on-security-configuration-drifts"></a>Automatização e controlo: drifts do alerta na configuração de segurança

A automatização do Azure automatiza processos administrativos com os runbooks que são baseados no PowerShell e execute na nuvem do Azure. Os runbooks podem ser também executados no seu centro de dados local para gerir recursos locais. A automatização do Azure fornece gestão de configuração com o PowerShell DSC (configuração de estado pretendido).

![Automatização do Azure](./media/azure-threat-detection/azure-threat-detection-fig7.png)

Pode criar e gerir recursos de DSC alojados no Azure e aplicá-los para a nuvem e sistemas no local para definir e impor a respetiva configuração automaticamente ou obter relatórios sobre o que se desviam para ajudar a assegurar que as configurações de segurança permanecem na política.

## <a name="azure-security-center"></a>Centro de Segurança do Azure

Centro de segurança do Azure ajuda a proteger os seus recursos do Azure. Fornece gestão de políticas e monitorização de segurança integrada nas suas subscrições do Azure. No âmbito do serviço, que é possível definir as políticas não apenas em relação a suas subscrições do Azure, mas também contra [grupos de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal), pelo que pode ser mais granular.

![Centro de Segurança do Azure](./media/azure-threat-detection/azure-threat-detection-fig8.png)

Os investigadores de segurança da Microsoft estão constantemente atentos a ameaças. Estes têm acesso a um conjunto amplo de telemetria obtida através da presença global da Microsoft na nuvem e no local. Esta coleção abrangente e diversificada de conjuntos de dados permite à Microsoft descobrir novos padrões de ataque e tendências dos seus produtos de consumidor e empresariais no local, assim como os seus serviços online.

Assim, o Centro de segurança pode atualizar rapidamente os respetivos algoritmos de deteção, que os atacantes disponibilizam novas e cada vez mais sofisticadas explorações. Esta abordagem ajuda a manter o ritmo com um ambiente de ameaças fast-mover.

![Centro de Segurança](./media/azure-threat-detection/azure-threat-detection-fig9.jpg)

A deteção de ameaças do Centro de Segurança funciona através da recolha automática de informações de segurança a partir dos seus recursos do Azure, da rede e das soluções de parceiros ligadas.  Analisa esta informação, correlacionando informações de várias origens, para identificar ameaças.
Os alertas de segurança são prioritários no Centro de Segurança, juntamente com recomendações sobre como remediar a ameaça.

O Centro de Segurança emprega análises de segurança avançadas, que ultrapassam as abordagens baseadas na assinatura. Avanços na macrodados e [machine learning](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) tecnologias são utilizadas para avaliar eventos em todos os recursos de infraestrutura de nuvem completo – detetar ameaças que seriam impossíveis de identificar através de abordagens manuais e previsão da evolução de ataques. Estas análises de segurança inclui o seguinte.

### <a name="threat-intelligence"></a>Informações sobre Ameaças

A Microsoft tem uma grande quantidade de informações sobre ameaças globais.
A telemetria provém de várias origens, como o Azure, Office 365, Microsoft CRM online, Microsoft Dynamics AX, outlook.com, MSN.com, a Microsoft unidade de Crimes digitais (DCU) e Microsoft Security Response Center (MSRC).

![Informações sobre Ameaças](./media/azure-threat-detection/azure-threat-detection-fig10.jpg)

Os investigadores também recebem informações sobre ameaças que são partilhadas entre os grandes fornecedores de serviços em nuvem e subscrevem feeds de informações sobre ameaças de terceiros. O Centro de Segurança do Azure pode utilizar estas informações para alertá-lo de ameaças de pessoas mal-intencionadas conhecidas. Alguns exemplos incluem:

-   **Harnessing a energia de Machine Learning -** Centro de segurança do Azure tem acesso a uma grande quantidade de dados sobre a atividade de rede na nuvem, o que pode ser utilizada para detetar ameaças direcionada para as implementações do Azure. Por exemplo:

-   **Força bruta deteções -** Machine learning é utilizado para criar um padrão histórico de tentativas de acesso remoto, que permite detetar ataques de força bruta contra portas SSH, o RDP e o SQL Server.

-   **Saída DDoS e deteção de Botnet** -um objetivo comuns de ataques de filtragem de recursos de nuvem consiste em utilizar a capacidade de computação destes recursos para executar outros ataques.

-   **Novos servidores de análise comportamental e VMs -** depois de um servidor ou a máquina virtual esteja comprometida, os atacantes empregam uma ampla variedade de técnicas para executar código malicioso nesse sistema ao evitando deteção, garantindo a persistência e evitando controlos de segurança.

-   **Deteção de ameaças do base de dados SQL do Azure -** a deteção de ameaças para a SQL Database do Azure, que identifica as atividades de base de dados anómalas, indicando invulgar e potencialmente prejudicial tenta aceder ao ou exploram bases de dados.

### <a name="behavioral-analytics"></a>Análise comportamental

A análise comportamental é uma técnica que analisa e compara os dados para uma coleção de padrões conhecidos. No entanto, estes padrões não são simples assinaturas. São determinados através de algoritmos complexos de machine learning aplicados a conjuntos de dados gigantescos.

![Análise comportamental](./media/azure-threat-detection/azure-threat-detection-fig11.jpg)


Também são determinados através de uma análise cuidada de comportamentos maliciosos pelos analistas especialistas. Centro de segurança do Azure pode utilizar a análise comportamental para identificar recursos comprometidos com base na análise de registos de máquina virtual, registos de dispositivos de rede virtual, registos de recursos de infraestrutura, informações de falhas e outras origens.

Além disso, existe uma correlação com outros sinais para procurar provas de uma campanha ampla. Esta correlação ajuda a identificar eventos que são consistentes com indicadores estabelecidos de comprometimento.

Alguns exemplos incluem:
-   **Execução do processo suspeito:** os atacantes empregam várias técnicas para executar o software malicioso sem deteção. Por exemplo, um atacante pode dar a software maligno os mesmos nomes de ficheiros de sistema legítimos mas colocar esses ficheiros numa localização alternativa, utilize um nome que é muito como um ficheiro benigno ou mascarar a verdadeira extensão do ficheiro. Os modelos do Centro de Segurança processam comportamentos de processos e monitorizam as execuções dos processos para detetar valores atípicos como estes.

-   **Oculto tentativas de software maligno e exploração:** software maligno pode capacidade de se esconder produtos de antimalware tradicionais ao nunca escrever no disco ou encriptar componentes de software armazenados no disco. No entanto, esse software maligno pode ser detetado com a análise de memória, como o software maligno tem de deixar rastreios na memória para a função. Quando o software falha, uma informação de falha de sistema captura uma parte da memória no momento da falha. Ao analisar a memória na informação de falha, o Centro de segurança do Azure pode detetar técnicas utilizadas para explora vulnerabilidades no software, aceder a dados confidenciais e persistir clandestinamente na máquina comprometida sem afetar o desempenho da sua máquina.

-   **Movimento lateral e reconhecimento interno:** para se manter num dados valiosos comprometidos e localizar/recolher de rede, os atacantes frequentemente tentam mover-se lateralmente da máquina comprometida para outras pessoas dentro da mesma rede. Centro de segurança monitoriza as atividades de início de sessão e processos para detetar as tentativas para expandir a sua presença de um atacante dentro da rede, como a execução do comando remoto, a pesquisa de rede e enumeração de contas.

-   **Scripts do PowerShell maliciosos:** PowerShell pode ser utilizado pelos atacantes para executar código malicioso em máquinas virtuais de destino para fins de vários. O Centro da Segurança inspeciona a atividade do PowerShell quanto a provas de atividade suspeita.

-   **Ataques de saída:** os atacantes frequentemente nos recursos da nuvem com o objetivo de utilizar os mesmos para preparar ataques adicionais. As máquinas virtuais comprometidas, por exemplo, poderá ser utilizadas para iniciar ataques de força bruta contra outras máquinas virtuais, enviar SPAM ou analisar portas abertas e outros dispositivos na Internet. Ao aplicar o machine learning ao tráfego de rede, o Centro de Segurança pode detetar quando as comunicações da rede de saída excedem o normal. Quando lhe enviar SPAM, o Centro de segurança correlaciona também o tráfego de e-mail invulgar com informações do Office 365 para determinar se o correio é provável que possui conteúdo nefasto ou o resultado de uma campanha de correio eletrónico legítimo.

### <a name="anomaly-detection"></a>Deteção de anomalias

O Centro de Segurança do Azure utiliza também a deteção de anomalias para identificar ameaças. Contrariamente à análise comportamental (que depende de padrões conhecidos derivados de grandes conjuntos de dados), a deteção de anomalias é mais “personalizada” e concentra-se nas linhas de base específicas das suas implementações. O machine Learning é utilizado para determinar a atividade normal das suas implementações e, em seguida, são geradas regras para definir condições de valores atípicos que podem representar um evento de segurança. Segue-se um exemplo:

-   **Ataques de força bruta RDP/SSH de entrada:** as implementações podem ter máquinas virtuais ocupadas com muitos inícios de sessão cada dia e outras máquinas virtuais que têm alguns ou de qualquer inícios de sessão. Centro de segurança do Azure pode determinar a atividade de início de sessão de linha de base para estas máquinas virtuais e utilizar o machine learning para definir em torno as atividades de início de sessão normal. Se não houver qualquer discrepância com a linha de base definida para características, relacionados com o início de sessão, em seguida, pode ser gerado um alerta. Novamente, o machine learning determina o que é significativo.

### <a name="continuous-threat-intelligence-monitoring"></a>Informações sobre ameaças contínua de monitorização

Centro de segurança do Azure funciona com segurança dados ciência equipas de investigação e em todo o mundo que monitorizam continuamente as alterações no campo das ameaças. Isto inclui as seguintes iniciativas:

-   **Monitorização:** ameaças incluem mecanismos, indicadores, implicações e conselhos acionáveis sobre ameaças existentes ou emergentes. Esta informação é partilhada na comunidade de segurança e a Microsoft monitoriza continuamente os feeds de informações sobre ameaças de origens internas e externas.

-   **Partilha de sinal:** Insights das equipas de segurança em portefólio abrangente da Microsoft de nuvem e no local dispositivos de ponto final de serviços, servidores e cliente são partilhados e analisados.

-   **Especialistas de segurança da Microsoft:** um envolvimento contínuo com equipas da Microsoft que trabalham em campos de segurança especializados, tal como forense e deteção de ataques de web.

-   **Otimização da deteção:** algoritmos são executados em conjuntos de dados de clientes reais e os investigadores de segurança trabalham com os clientes para validar os resultados. Os verdadeiros e falsos positivos são utilizados para refinar os algoritmos do machine learning.

Estes esforços combinados culminam em novas e melhoradas deteções das quais pode beneficiar de imediato, sem que seja precisa qualquer ação da sua parte.

## <a name="advanced-threat-detection-features---other-azure-services"></a>Funcionalidades de deteção de ameaças avançadas - outros serviços do Azure

### <a name="virtual-machine-microsoft-antimalware"></a>Máquina virtual: Antimalware da Microsoft

[Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) para o Azure é uma solução de agente único para aplicações e ambientes de inquilino, concebido para ser executado em segundo plano, sem qualquer intervenção humana. Pode implementar a proteção com base nas necessidades das cargas de trabalho aplicações, com o básico secure-por-predefinição ou avançadas de configuração personalizada, incluindo a monitorização de antimalware. Antimalware do Azure é uma opção de segurança para máquinas virtuais do Azure e é instalado automaticamente em todas as máquinas virtuais do Azure PaaS.

**Funcionalidades do Azure para implementar e ativar o Antimalware da Microsoft para as suas aplicações**

#### <a name="microsoft-antimalware-core-features"></a>Funcionalidades de núcleos de Antimalware da Microsoft

-   **Proteção em tempo real -** monitoriza as atividades nos serviços em nuvem e em máquinas virtuais para detetar e bloquear a execução de software maligno.

-   **Agendar análise -** periodicamente efetua a análise de destino para detetar software maligno, incluindo ativamente em execução programas.

-   **Remediação de software maligno -** executa automaticamente ação no software maligno detetado, tais como eliminar ou quarentena ficheiros maliciosos e limpeza de entradas do registo malicioso.

-   **Atualizações de assinatura -** instala automaticamente as assinaturas de proteção mais recentes (definições de vírus) para garantir a proteção está atualizada numa frequência previamente determinada.

-   **Atualizações do motor antimalware -** atualiza automaticamente o motor Antimalware da Microsoft.

-   **Atualizações de plataforma de antimalware –** atualiza automaticamente a plataforma de Antimalware da Microsoft.

-   **A proteção ativa -** relatórios metadados de telemetria sobre ameaças detetadas e os recursos suspeitos ao Microsoft Azure para garantir que a resposta rápida para a evolução das ameaças e ativar a entrega de assinatura síncrono em tempo real através do Microsoft Active Protection System (MAPS).

-   **Amostras de relatórios -** fornece e relatórios de exemplos para o serviço de Antimalware da Microsoft para ajudar a otimizar o serviço e ative a resolução de problemas.

-   **Exclusões –** permite que a aplicação e administradores de serviço configurar a determinados ficheiros, processos e as unidades para os excluir da proteção e ao analisar o desempenho e/ou outros motivos.

-   **Recolha de eventos de antimalware -** regista o estado de funcionamento do serviço antimalware, atividades suspeitas e as ações de remediação executadas no registo de eventos de sistema operativo e recolhe-los para a conta de armazenamento do Azure do cliente.

### <a name="azure-sql-database-threat-detection"></a>Deteção de ameaças de base de dados SQL do Azure

[Deteção de ameaças de base de dados SQL do Azure](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/) é uma nova funcionalidade de intelligence de segurança incorporada para o serviço SQL Database do Azure. Trabalhar cerca de clock para saber mais, perfis e detetar atividades de base de dados anómalas, a deteção de ameaças de base de dados de SQL do Azure identifica potenciais ameaças à base de dados.

Officers de segurança ou outros administradores designados podem obter uma notificação imediata sobre atividades suspeitas da base de dados à medida que ocorrem. Cada notificação fornece detalhes da atividade suspeita e recomenda como ainda mais a investigar e mitigar a ameaça.

Atualmente, a deteção de ameaças de base de dados de SQL do Azure Deteta potenciais vulnerabilidades e ataques de injeção de SQL e padrões de acesso de base de dados anómalas.

Após receber a notificação de correio eletrónico de deteção de ameaças, os utilizadores são capazes de navegar e ver os registos de auditoria relevantes utilizando a ligação avançada no correio que abre um Visualizador de auditoria e/ou pré-configurada auditoria modelo do Excel que mostra os registos de auditoria relevantes à volta da hora do evento suspeita, de acordo com o seguinte:
-   Armazenamento de auditoria para o base de dados do servidor com as atividades de base de dados anómalas

-   Tabela de armazenamento de auditoria relevantes que foi utilizada no momento do evento para escrever o registo de auditoria

-   Uma vez que ocorre o evento de auditoria registos da hora seguinte.

-   Registos de auditoria com o ID de evento semelhante no momento do evento (opcional para algumas detetores)

Detetores de ameaças de base de dados do SQL Server utilize um dos metodologias de deteção seguinte:

-   **A deteção determinista –** Deteta padrões suspeitos (regras com base) nas consultas de cliente do SQL Server que correspondem aos ataques conhecidos. Este metodologia tem deteção alta e baixo positivos FALSO, no entanto limitada cobertura porque ficarem incluídos a categoria de "deteções atómicas".

-   **Deteção behavioural –** defeitos atividade anómala que é o comportamento anormal para a base de dados que não foi visualizada durante os últimos 30 dias.  Um exemplo de atividade anómala de cliente de SQL Server pode ser um pico de pedidos de inícios de sessão falhadas/consultas, elevado volume de dados que está a ser extraídos, consultas canónicos invulgares e familiarizados endereços IP utilizados para aceder à base de dados

### <a name="application-gateway-web-application-firewall"></a>Firewall de aplicação de Web de Gateway de aplicação

[Firewall de aplicação Web](../app-service/environment/app-service-app-service-environment-web-application-firewall.md) é uma funcionalidade do [Gateway de aplicação do Azure](https://docs.microsoft.com/azure/application-gateway/application-gateway-webapplicationfirewall-overview) que fornece proteção para aplicações web que utilizam o gateway de aplicação para standard [controlo de entrega de aplicação](https://kemptechnologies.com/in/application-delivery-controllers) funções. Firewall de aplicações Web efetua este procedimento, protegendo-os em relação a maioria do [OWASP principais 10 vulnerabilidades web comuns](https://www.owasp.org/index.php/Top_10_2010-Main)

![Aplicação de Web de Gateway de aplicação Firewally](./media/azure-threat-detection/azure-threat-detection-fig13.png)

-   Proteção contra injeção de SQL

-   Proteção contra scripting entre sites

-   Proteção contra Ataques Web comuns, como, por exemplo, injeção de comandos, contrabando de pedidos HTTP, divisão de respostas HTTP e ataques remotos de inclusão de ficheiros

-   Proteção contra violações de protocolo HTTP

-   Proteção contra anomalias de protocolo HTTP, como agente de utilizador de anfitrião e cabeçalhos de aceitação em falta

-   Prevenção de contra bots, crawlers e scanners

-   Deteção de aplicação configurações incorretas comuns (ou seja, o Apache, IIS, etc.)

Configurar WAF no Gateway de aplicação fornece as seguintes vantagens para si:

-   Protege a sua aplicação Web de vulnerabilidades e ataques Web sem modificar o código de back-end.

-   Protege várias aplicações Web em simultâneo por trás de um gateway de aplicação. O gateway de aplicação suporta o alojamento de até 20 sites por trás de um único gateway, o que permite protegê-los a todos contra ataques Web.

-   Monitoriza a sua aplicação Web contra ataques através de relatórios em tempo real gerados pelos registos WAF do gateway de aplicação.

-   Determinados controlos de conformidade requerem que todos os pontos finais com acesso à Internet sejam protegidos por uma solução WAF. Ao utilizar o gateway de aplicação com a WAF ativada, pode satisfazer estes requisitos de conformidade.

### <a name="anomaly-detection--an-api-built-with-azure-machine-learning"></a>Deteção de anomalias – uma API criada com o Azure Machine Learning

Deteção de anomalias é uma API criada com o Azure Machine Learning que é útil para detetar tipos diferentes de padrões anómalos nos seus dados de séries de tempo. A API atribui uma classificação de anomalias para cada ponto de dados na série de tempo, o que pode ser utilizado para gerar alertas, através dos dashboards de monitorização ou a ligação com os sistemas de emissão de permissões.

O [API de deteção de anomalias](https://docs.microsoft.com/azure/machine-learning/machine-learning-apps-anomaly-detection-api) pode detetar os seguintes tipos de anomalias nos dados de séries de tempo:

-   **Picos e Dips:** por exemplo, ao monitorizar o número de falhas de início de sessão para um serviço ou o número de checkouts um site de comércio eletrónico, picos invulgares dips foi indicar ataques de segurança ou interrupções de serviço.

-   **As tendências positivos e negativos:** ao monitorizar a utilização de memória em informática, por exemplo, reduzir o tamanho de memória livre é indicativa de uma potencial fuga de memória; ao monitorizar o comprimento da fila de serviço, uma tendência ascendente persistente pode indicar um problema de software subjacente.

-   **Nível de alterações e alterações no intervalo dinâmico de valores:** por exemplo, alterações a nível na latências de um serviço depois de um serviço de atualização ou níveis de exceções de menor após a atualização pode ser interessante para monitorizar.

A aprendizagem baseada em API que permite:

-   **Deteção flexível robusta e responsiva:** os modelos de deteção de anomalias permitem aos utilizadores configurar as definições de sensibilidade e detetar anomalias entre sazonais e não sazonais conjuntos de dados. Os utilizadores podem ajustar o modelo de deteção de anomalias para efetuar a deteção de API mais ou menos sensível, de acordo com as suas necessidades. Isto seria significa a deteção de anomalias visível mais ou menos no dados com e sem sazonais padrões.

-   **Deteção atempada e escalável:** a forma tradicional de monitorização com limiares presentes definidos pelo conhecimento do domínio dos especialistas são dispendiosas e não dimensionável para milhões de forma dinâmica alterar conjuntos de dados. Os modelos de deteção de anomalias nesta API adquiridos e modelos são otimizados automaticamente a partir dos dados históricos e em tempo real.

-   **A deteção proativa e acionável:** tendência lenta e deteção de alteração do nível de podem ser aplicadas para deteção de anomalias antecipado. Os sinais anormais antecipados detetadas podem ser utilizadas para direcionar humans para investigar e atuar sobre as áreas de problema.  Além disso, os modelos de análise de causa de raiz e ferramentas de alertas podem ser desenvolvidas por cima deste serviço de API de deteção de anomalias.

A deteção de anomalias API é uma solução eficaz e eficiente para uma vasta gama de cenários como o estado de funcionamento do serviço & KPI monitorização, IoT, a monitorização de desempenho e a monitorização de tráfego de rede. Seguem-se alguns cenários populares onde esta API pode ser úteis:
- Os departamentos de TI precisam de ferramentas para controlar eventos, o código de erro, o registo de utilização e o desempenho (CPU, memória e assim sucessivamente) de uma forma atempada.

-   Sites de comércio online queira controlar a cliente atividades, vistas de página, cliques e assim sucessivamente.

-   Utilitários de empresas pretendem controlar o consumo de níveis máximos, gás, eletricidade e outros recursos.

-   Serviços de gestão de instalações/edifício pretendem monitorizar a temperatura, residual, o tráfego e assim sucessivamente.

-   IoT/fabricantes pretende utilizar dados de sensores na série de tempo para o fluxo de trabalho de monitorização, qualidade e assim sucessivamente.

-   Fornecedores de serviços, tais como centros de chamada tem de monitorizar tendências de pedido de serviço, volume de incidente, o comprimento da fila de espera e assim sucessivamente.

-   Grupos de análise de negócio que pretende monitorizar os KPIs de empresas (por exemplo, o volume de venda sentiments de cliente, preços) movimento anormal em tempo real.

### <a name="cloud-app-security"></a>Cloud App Security

[Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) é um componente fundamental da pilha de segurança de nuvem da Microsoft. É uma solução abrangente que pode ajudar a sua organização como mover para tirar partido da promessa das aplicações em nuvem, mas manter-se no controlo, através de visibilidade melhorada para a atividade. Também ajuda a aumentar a proteção de dados críticos entre aplicações em nuvem.

Com ferramentas que ajudam a Desvende shadow IT, a avaliar o risco, a impor políticas, a investigar atividades e a parar ameaças, organização pode mover mais segura para a nuvem e manter o controlo de dados críticos.

<table style="width:100%">
 <tr>
   <td>Descobrir</td>
   <td>Desvende shadow IT com o Cloud App Security. Ganhe visibilidade ao detetar as aplicações, atividades, utilizadores, dados e ficheiros no seu ambiente na nuvem. Detete as aplicações de terceiros que estejam ligadas à sua nuvem.</td>
 </tr>
 <tr>
   <td>Investigar</td>
   <td>Investigue as suas aplicações em nuvem utilizando ferramentas forenses de nuvem para descrição profunda em aplicações arriscadas, utilizadores específicos e os ficheiros na sua rede. Localize padrões nos dados recolhidos a partir da nuvem. Gere relatórios para monitorizar a sua nuvem.</td>

 </tr>
 <tr>
   <td>Controlo</td>
   <td>Mitigar o risco através da definição de políticas e alertas para alcançar o máximo controlo sobre o tráfego de rede de nuvem. Utilize o Cloud App Security para migrar os seus utilizadores para aplicações em nuvem aprovadas, seguro alternativas.</td>

 </tr>
 <tr>
   <td>Proteger</td>
   <td>Utilize o Cloud App Security para aprovar ou proibir aplicações, aplicar prevenção de perda de dados, controlo e permissões de partilha e gerar relatórios e alertas personalizados.</td>

 </tr>
 <tr>
   <td>Controlo</td>
   <td>Mitigar o risco através da definição de políticas e alertas para alcançar o máximo controlo sobre o tráfego de rede de nuvem. Utilize o Cloud App Security para migrar os seus utilizadores para aplicações em nuvem aprovadas, seguro alternativas.</td>

 </tr>
</table>


![Cloud App Security](./media/azure-threat-detection/azure-threat-detection-fig14.png)

O cloud App Security integra-se a visibilidade com a sua nuvem por

-   Com a Cloud Discovery para mapear e identificar o seu ambiente na nuvem e as aplicações em nuvem organização está a utilizar.


-   Funciona a aprovação e prohibiting aplicações na nuvem.



-   Utilizar conectores de aplicações fácil de implementar que tirar partido do fornecedor de APIs, para visibilidade e governação de aplicações que ligam à.

-   Ajudando-o a ter controlo contínuo por definição e, em seguida, fine-tuning continuamente, as políticas.

No recolher dados a partir destas origens, o Cloud App Security executa uma análise sofisticada nos dados. Imediatamente alerta-o para atividades anómalas e dá-lhe visibilidade detalhada para o seu ambiente de nuvem. Pode configurar uma política no Cloud App Security e utilizá-la para proteger todo o seu ambiente na nuvem.

## <a name="third-party-atd-capabilities-through-azure-marketplace"></a>Capacidades ATD de terceiros através do Azure Marketplace

### <a name="web-application-firewall"></a>Firewall de Aplicação Web

Firewall de aplicações Web inspeciona carregamentos de software maligno de blocos injections de SQL, processamento de scripts entre sites e o tráfego de entrada web & aplicação DDoS e outros ataques direcionados às suas aplicações web. É também inspeciona as respostas de servidores web de back-end para prevenção de perda de dados (DLP). O motor de controlo de acesso integrada permite que os administradores a criar políticas de controlo de acesso granulares para autenticação, autorização e gestão de contas (AAA), que fornece autenticação forte organizações e controlo de utilizador.

**Destaques:**
-   Deteta e bloqueia SQL injections, processamento de scripts entre sites, carregamentos de software maligno, aplicações DDoS ou quaisquer outros ataques contra a sua aplicação.

-   Autenticação e controlo de acesso.

-   Analisa o tráfego de saída para detetar dados confidenciais e pode mascarar ou bloquear as informações a fuga de.

-   Acelera a entrega de conteúdos de aplicações web, utilizando as funcionalidades, tais como a colocação em cache, compressão e outras otimizações de tráfego.

Exemplo de firewalls de aplicação Web disponíveis no Azure Marketplace seguem-se:

[Firewall de aplicações Web barracuda, Firewall de aplicação de Web Virtual do Brocade (Brocade vWAF), Imperva SecureSphere e a Firewall do IP ThreatSTOP.](https://azure.microsoft.com/marketplace/partners/brocade_communications/brocade-virtual-web-application-firewall-templatevtmcluster/)

## <a name="next-steps"></a>Passos Seguintes

- [Capacidades de deteção do Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-detection-capabilities)

Capacidades de deteção avançadas do Centro de segurança do Azure ajuda a identificar ameaças ativas que ameaçam os seus recursos do Microsoft Azure e fornece-lhe as informações necessárias para responder rapidamente.

- [Deteção de ameaças de base de dados SQL do Azure](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/)

Deteção de ameaças de base de dados SQL do Azure ajudou a respetiva preocupações potenciais ameaças à respetiva base de dados de endereços.
