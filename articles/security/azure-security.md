---
title: "Introdução à segurança do Azure | Microsoft Docs"
description: "Saiba mais sobre a segurança do Azure, os respetivos serviços e como funciona."
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
ms.openlocfilehash: b22ad29e593a9293a98acebca065f19ab2d9d5be
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2017
---
# <a name="introduction-to-azure-security"></a>Introdução à segurança do Azure
## <a name="overview"></a>Descrição geral
Sabemos que a segurança está tarefa um na nuvem e como importante é que encontrará exatas e atempadas informações sobre a segurança do Azure. Uma das razões melhor a utilizar o Azure para as suas aplicações e serviços é tirar partido do respetivo grade diversidade de capacidades e ferramentas de segurança. Estas ferramentas e capacidades ajudam possibilitam a criar soluções seguras na plataforma do Azure segura. O Microsoft Azure oferece confidencialidade, integridade e disponibilidade dos dados de cliente, permitindo também accountability transparente.

Para o ajudar a compreender melhor a coleção de segurança controlos implementados no Microsoft Azure de perspetivas do cliente e do Microsoft operations, este documento técnico, "Introdução à segurança do Azure", é escrito para fornecer um aspeto abrangente em segurança disponível com o Microsoft Azure.

### <a name="azure-platform"></a>Plataforma do Azure
Azure é uma plataforma de serviço de nuvem pública que suporta uma seleção abrangente dos sistemas operativos, programação idiomas, estruturas, ferramentas, bases de dados e dispositivos. Pode ser executado contentores de Linux com a integração do Docker; criar aplicações com JavaScript, Python, .NET, PHP, Java e Node.js; compilação back-ends para iOS, Android e Windows dispositivos.

Serviços em nuvem pública do Azure suportam as mesmas tecnologias milhões de programadores e profissionais de TI já dependem e de confiança. Quando criar ou migrar os recursos IT à, um fornecedor de serviços de nuvem pública são depender capacidades da organização para proteger as suas aplicações e dados com os serviços e os controlos fornecem para gerir a segurança dos seus recursos baseados na nuvem.

Infraestrutura do Azure foi concebida de instalação de aplicações para o alojamento de milhões de clientes em simultâneo, e fornece uma base de confiança no qual as empresas podem cumprir os seus requisitos de segurança.

Além disso, Azure disponibiliza uma vasta gama de opções de segurança configuráveis e a capacidade de controlá-las, de modo a que possa personalizar a segurança para satisfazer os requisitos exclusivos de implementações da sua organização. Este documento ajuda-o a compreender a segurança do Azure como capacidades podem ajudar a satisfazer estes requisitos.

> [!Note]
> O foco principal deste documento é em controlos de orientado para o cliente que pode utilizar para personalizar e aumentar a segurança para as suas aplicações e serviços.
>
> Podemos fornecer algumas informações de descrição geral, mas para obter informações detalhadas sobre como o Microsoft protege a própria plataforma do Azure, consulte as informações fornecidas a [Microsoft Trust Center](https://www.microsoft.com/TrustCenter/default.aspx).

### <a name="abstract"></a>Abstrato
Inicialmente, migrações de nuvem pública foram definidas por uma redução de custos e a agilidade para inovar. Segurança foi considerada uma preocupação principais para algum tempo e até mesmo um stopper Mostrar, para a migração de nuvem pública. No entanto, a segurança de nuvem pública tem transitou de uma preocupação principal para um dos controladores para migração para a nuvem. A lógica por detrás atrás esta é a capacidade superior dos fornecedores de serviços de nuvem pública grande de proteger aplicações e os dados de recursos baseados na nuvem.

A infraestrutura do Azure foi concebida a partir da facilidade que as aplicações têm para alojar milhões de clientes em simultâneo e proporciona uma fundação fidedigna com a qual as empresas podem satisfazer as suas necessidades de segurança. Além disso, o Azure disponibiliza uma vasta matriz das opções configuráveis de segurança e a capacidade de controlá-las, de modo a que possa personalizar a segurança para satisfazer os requisitos exclusivos das implementações para satisfazer o departamento de TI políticas de controlo e respeitar externo regulamentos.

Este documento descreve a abordagem da Microsoft para segurança numa plataforma de nuvem do Microsoft Azure:
* Funcionalidades de segurança implementadas pela Microsoft para proteger a infraestrutura do Azure, dados de clientes e aplicações.
* Serviços do Azure e as funcionalidades de segurança disponíveis para que possa gerir a segurança dos serviços e os dados nas suas subscrições do Azure.

## <a name="summary-azure-security-capabilities"></a>Capacidades de resumo de segurança do Azure
A seguinte tabela fornece uma breve descrição das funcionalidades de segurança implementado pela Microsoft para proteger a infraestrutura do Azure, dados de clientes e aplicações seguras.
### <a name="security-features-implemented-to-secure-the-azure-platform"></a>Funcionalidades de segurança implementadas para proteger a plataforma do Azure:
As funcionalidades apresentadas a seguir são capacidades, que pode rever para fornecer a garantia que a plataforma do Azure é gerida de forma segura. As ligações foram fornecidas para desagregar adicional na forma como o Microsoft endereços questões de fidedignidade dos clientes nas áreas de quatro: plataforma segura, privacidade & controlos, conformidade e transparência.


| [Plataforma segura](https://www.microsoft.com/en-us/trustcenter/Security/default.aspx)  | [Privacidade e controlos](https://www.microsoft.com/en-us/trustcenter/Privacy/default.aspx)  |[Conformidade](https://www.microsoft.com/en-us/trustcenter/Compliance/default.aspx)   | [Transparência](https://www.microsoft.com/en-us/trustcenter/Transparency/default.aspx) |
| :-- | :-- | :-- | :-- |
| [Ciclo de desenvolvimento de segurança](https://www.microsoft.com/en-us/sdl/), interno auditorias | [Gerir os seus dados sempre](https://www.microsoft.com/en-us/trustcenter/Privacy/You-own-your-data) | [Centro de Fidedignidade](https://www.microsoft.com/en-us/trustcenter/default.aspx) |[Como Microsoft protege os dados de cliente nos serviços do Azure](https://www.microsoft.com/en-us/trustcenter/Transparency/default.aspx) |
| [Formação de segurança obrigatória, verificações de fundo](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=2&cad=rja&uact=8&ved=0ahUKEwiwsOCpganRAhWqxVQKHUdiDsMQFghAMAE&url=https%3A%2F%2Fdownloads.cloudsecurityalliance.org%2Fstar%2Fself-assessment%2FStandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx&usg=AFQjCNEYvBky4zNeDQPN6YJGPFRZA7eeZg&sig2=2kkw1lOCP_kzLzgE9RS2Tg&bvm=bv.142059868,d.amc) |  [Controlar na localização de dados](https://www.microsoft.com/en-us/trustcenter/Privacy/Where-your-data-is-located) |  [Hub de controlos comuns](https://www.microsoft.com/en-us/trustcenter/Common-Controls-Hub) |[Como o Microsoft gerir a localização de dados nos serviços do Azure](http://azuredatacentermap.azurewebsites.net/)|
| [Testar a penetração](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=2&cad=rja&uact=8&ved=0ahUKEwiwsOCpganRAhWqxVQKHUdiDsMQFghAMAE&url=https%3A%2F%2Fdownloads.cloudsecurityalliance.org%2Fstar%2Fself-assessment%2FStandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx&usg=AFQjCNEYvBky4zNeDQPN6YJGPFRZA7eeZg&sig2=2kkw1lOCP_kzLzgE9RS2Tg&bvm=bv.142059868,d.amc), [deteção de intrusões, DDoS](https://www.microsoft.com/en-us/trustcenter/Security/ThreatManagement), [auditorias & registo](https://www.microsoft.com/en-us/trustcenter/Security/AuditingAndLogging) | [Fornecer acesso a dados à sua medida](https://www.microsoft.com/en-us/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms) |  [Os serviços em nuvem devido a lista de verificação Diligence](https://www.microsoft.com/en-us/trustcenter/Compliance/Due-Diligence-Checklist) |[Que a Microsoft pode aceder aos seus dados em que termos](https://www.microsoft.com/en-us/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms)|
| [Estado da última datacentre](https://www.microsoft.com/en-us/cloud-platform/global-datacenters), segurança física, [rede seguro](https://docs.microsoft.com/en-us/azure/security/security-network-overview) | [Responder a aplicação da lei](https://www.microsoft.com/en-us/trustcenter/Privacy/Responding-to-govt-agency-requests-for-customer-data) |  [Conformidade por localização & da indústria do serviço](https://www.microsoft.com/en-us/trustcenter/Compliance/default.aspx) |[Como Microsoft protege os dados de cliente nos serviços do Azure](https://www.microsoft.com/en-us/trustcenter/Transparency/default.aspx)|
|  [Resposta de incidente de segurança](http://aka.ms/SecurityResponsepaper), [responsabilidade partilhado](http://aka.ms/sharedresponsibility) |[Normas de privacidade rigorosos](https://www.microsoft.com/en-us/TrustCenter/Privacy/We-set-and-adhere-to-stringent-standards) |  | [Certificação de revisão para serviços do Azure, hub transparência](https://www.microsoft.com/en-us/trustcenter/Compliance/default.aspx)|



### <a name="security-features-offered-by-azure-to-secure-data-and-application"></a>Funcionalidades de segurança oferecidas pelo Azure para proteger dados e de aplicações
Consoante o modelo de serviço em nuvem, há variável responsabilidade para quem é responsável por gerir a segurança da aplicação ou serviço. As capacidades estão disponíveis na plataforma do Azure para ajudá-lo a cumprir estas responsabilidades através de funcionalidades integradas e através de soluções de parceiros que podem ser implementadas para uma subscrição do Azure.

As capacidades incorporadas são organizadas em seis (6) funcionais áreas: operações, aplicações, armazenamento, redes, computação e identidade. São fornecidos detalhes adicionais sobre as funcionalidades e capacidades disponíveis na plataforma do Azure nas seguintes áreas seis (6) através de informações de resumo.

## <a name="operations"></a>Operações
Esta secção fornece informações adicionais sobre as principais funcionalidades nas operações de segurança e informações de resumo sobre estas capacidades.

### <a name="operations-management-suite-security-and-audit-dashboard"></a>Segurança do Operations Management Suite e o Dashboard de auditoria
O [solução de auditoria e segurança do OMS](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) fornece uma vista abrangente da sua organização postura de segurança IT com [consultas de pesquisa incorporada](https://blogs.technet.microsoft.com/msoms/2016/01/21/easy-microsoft-operations-management-suite-search-queries/) para problemas relevantes que necessitam da sua atenção. O dashboard de [Segurança e Auditoria](https://technet.microsoft.com/library/mt484091.aspx) é o ecrã principal de tudo o que se relacione com a segurança no OMS. Fornece informações de alto nível para o estado de segurança dos seus computadores. Também inclui a capacidade de ver todos os eventos das últimas 24 horas, 7 dias ou qualquer outro intervalo de tempo personalizado.

Além disso, pode configurar a segurança do OMS & compatibilidade para [automaticamente realizar ações específicas](https://blogs.technet.microsoft.com/robdavies/2016/04/20/simple-look-at-oms-alert-remediation-with-runbooks-part-1/) quando é detetado um evento específico.

### <a name="azure-resource-manager"></a>Azure Resource Manager
[O Azure Resource Manager ](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) permite-lhe trabalhar com os recursos na sua solução como um grupo. Pode implementar, atualizar ou eliminar todos os recursos da sua solução numa operação única e coordenada. Utilizar um [modelo Azure Resource Manager](https://blogs.technet.microsoft.com/canitpro/2015/06/29/devops-basics-infrastructure-as-code-arm-templates/) para implementação e esse modelo podem funcionar para ambientes diferentes, como de teste, teste e produção. O Resource Manager fornece funcionalidades de segurança, auditoria e etiquetagem para o ajudar a gerir os recursos após a implementação.

Implementações de baseado no modelo de Gestor de recursos do Azure ajudam a melhorar a segurança das soluções de implementar no Azure, porque a controlar as definições de segurança padrão e podem ser integradas no implementações baseadas em modelo normalizadas. Isto reduz o risco de erros de configuração de segurança que podem ocorrer durante implementações do manuais.

### <a name="application-insights"></a>Application Insights
[Application Insights](https://docs.microsoft.com/azure/application-insights/) é um serviço de gestão de desempenho de aplicações (APM) extensível para programadores de web. Com o Application Insights, pode monitorizar as aplicações web em direto e detetar automaticamente anomalias de desempenho. Inclui as ferramentas de análise poderosas para ajudar a diagnosticar problemas e para saber que os utilizadores, na verdade, fazer com as suas aplicações. Monitoriza a aplicação sempre está em execução, durante os testes e depois de ter publicado ou implementado.

Application Insights cria gráficos e tabelas que mostram, por exemplo, as horas do dia, obter maior parte dos utilizadores, como responder a aplicação está e quão bem é servido por quaisquer serviços externos que depende.

Se existirem falhas, falhas ou problemas de desempenho, pode procurar os dados telemétricos em detalhe para diagnosticar a causa. E o serviço envia-lhe e-mails se existirem quaisquer alterações na disponibilidade e desempenho da sua aplicação. Informações de aplicação, por isso, torna-se uma ferramenta de segurança importantes porque ajuda com a disponibilidade nos confidencialidade, integridade e triad de segurança de disponibilidade.

### <a name="azure-monitor"></a>Azure Monitor
[Monitor do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) oferece visualização, consulta, encaminhamento, alertas, escala automática e automatização em ambos os dados da infraestrutura do Azure ([registo de atividade](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)) e cada recurso do Azure individual ([diagnóstico Regista](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)). Pode utilizar o Monitor do Azure para o alertar sobre eventos relacionados com segurança que são gerados nos registos do Azure.

### <a name="log-analytics"></a>Log Analytics
[Análise de registo](https://azure.microsoft.com/documentation/services/log-analytics/) fazem parte da [Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite) – fornece uma solução de gestão de IT para terceiros baseados na nuvem infraestrutura (por exemplo, o AWS), além dos recursos do Azure e no local. Dados de monitorização do Azure podem ser encaminhados diretamente ao Log Analytics para que possa ver as métricas e registos para todo o seu ambiente no local.

Análise de registos pode ser uma ferramenta úteis na análise de segurança de análise forense e outros, tal como a ferramenta permite-lhe procurar rapidamente grandes quantidades de entradas relacionadas com segurança com uma abordagem de consulta flexível. Além disso, no local [registos de firewall e proxy podem ser exportados no Azure e disponibilizados para utilizar a análise de registos de análise.](https://docs.microsoft.com/azure/log-analytics/log-analytics-proxy-firewall)

### <a name="azure-advisor"></a>Azure Advisor
[Azure Advisor](https://docs.microsoft.com/azure/advisor/) é um consultor de nuvem personalizado que ajuda a otimizar as implementações do Azure. Analisa a configuração do recurso e a telemetria de utilização. Em seguida, recomenda soluções para ajudar a melhorar o [desempenho](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations), [segurança](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations), e [elevada disponibilidade](https://docs.microsoft.com/azure/advisor/advisor-high-availability-recommendations) dos seus recursos ao procurar oportunidades para [reduzir o Azure geral gastam](https://docs.microsoft.com/azure/advisor/advisor-cost-recommendations). Azure Advisor fornece recomendações de segurança, que pode significativas melhorar a sua postura de segurança geral para soluções de que implementar no Azure. Estas recomendações são desenhadas a partir de análise de segurança efetuada pelo [Centro de segurança do Azure.](https://docs.microsoft.com/azure/security-center/security-center-intro)

### <a name="azure-security-center"></a>Centro de Segurança do Azure
O [Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) ajuda-o a evitar, detetar e responder a ameaças com uma maior visibilidade e controlo sobre a segurança dos seus recursos Azure. Fornece gestão de políticas e monitorização de segurança integrada nas suas subscrições do Azure, ajuda a detetar ameaças que caso contrário podem passar despercebidas e funciona com um ecossistema abrangente de soluções de segurança.

Além disso, o Centro de segurança do Azure ajuda-o com operações de segurança, fornecendo um único dashboard que alertas analisa e as recomendações que podem ser alteradas imediatamente. Muitas vezes, pode resolver problemas com um único clique dentro da consola do Centro de segurança do Azure.
## <a name="applications"></a>Aplicações
A secção fornece informações adicionais sobre as principais funcionalidades nas informações de segurança e o resumo de aplicações sobre estas capacidades.

### <a name="web-application-vulnerability-scanning"></a>Análise de vulnerabilidade de aplicação de Web
Uma das formas mais fácil para começar a testar para vulnerabilidades no seu [aplicação do app Service](https://docs.microsoft.com/azure/app-service/app-service-web-overview) consiste em utilizar o [integração com o Tinfoil Security](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) para efetuar a análise na sua aplicação de vulnerabilidade de um clique. Pode ver os resultados de teste num relatório fácil de compreender e saber como corrigir cada vulnerabilidade com instruções passo a passo.

### <a name="penetration-testing"></a>Testes de Penetração
Se preferir efetuar os seus próprios testes penetração ou pretende utilizar outro conjunto de scanner ou o fornecedor, tem de seguir a [penetração do Azure, teste o processo de aprovação](https://security-forms.azure.com/penetration-testing/terms) e obter aprovação antes de efetuar os testes de penetração pretendido.

### <a name="web-application-firewall"></a>Firewall de aplicação Web
Firewall de aplicação web (WAF) nas [Gateway de aplicação do Azure](https://azure.microsoft.com/services/application-gateway/) ajuda a proteger as aplicações web de ataques baseados na web comuns, como a injeção de SQL, ataques de scripts entre sites e hijacking de sessão. Vem pré-configurada com a proteção contra ameaças identificadas pelo [abra Web aplicação segurança projeto (OWASP) como as vulnerabilidades comuns 10 principais](https://msdn.microsoft.com/library/).

### <a name="authentication-and-authorization-in-azure-app-service"></a>Autenticação e autorização no Serviço de Aplicações do Azure
[Autenticação do serviço de aplicação / autorização](https://docs.microsoft.com/azure/app-service/app-service-authentication-overview) é uma funcionalidade que fornece uma forma para a sua aplicação iniciar sessão em utilizadores para que não tem de alterar o código no back-end da aplicação. Fornece uma forma fácil para proteger a sua aplicação e trabalhar com dados por utilizador.

### <a name="layered-security-architecture"></a>Arquitetura de segurança por camadas
Uma vez que [ambientes do App Service](https://docs.microsoft.com/azure/app-service/environment/app-service-app-service-environment-intro) disponibilizar um ambiente de tempo de execução isolada implementado para um [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview), os programadores podem criar uma arquitetura de segurança por camadas que fornecem níveis diferentes de acesso de rede para cada camada de aplicação. É um desejo comuns para ocultar API back-ends de geral acesso à Internet e permitir apenas APIs chamado através de aplicações web a montante. [Grupos de segurança (NSGs) de rede](https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/) podem ser utilizados em sub-redes da rede Virtual do Azure que contém os ambientes do App Service para restringir o acesso público ao aplicações API.

### <a name="web-server-diagnostics-and-application-diagnostics"></a>Diagnóstico do servidor Web e o application diagnostics
Web apps do App Service fornecem a funcionalidade de diagnóstico para informações de registo do servidor web e a aplicação web. Estes logicamente estão separadas em [diagnóstico do servidor de web](https://docs.microsoft.com/azure/app-service/web-sites-enable-diagnostic-log) e [diagnóstico de aplicação](https://technet.microsoft.com/library/hh530058(v=sc.12).aspx). Servidor Web inclui dois avanços principais diagnosticar e resolução de problemas de sites e aplicações.

A nova funcionalidade de primeiro é informações de estado em tempo real sobre os conjuntos aplicacionais, processos de trabalho, sites, domínios de aplicação e pedidos de execução. As vantagens de nova segundo estão os eventos de rastreio de detalhado que controlam um pedido em todo o processo de pedido e resposta completado.

Para ativar a recolha destes eventos de rastreio, IIS 7 pode ser configurado para capturar automaticamente registos de rastreio completa, no formato XML, para qualquer pedido específico baseado no tempo decorrido ou códigos de resposta de erro.

#### <a name="web-server-diagnostics"></a>Diagnóstico do servidor Web
Pode ativar ou desativar os seguintes tipos de registos:

-   Detalhadas de registo de erro - Erro informações detalhadas de códigos de estado HTTP que indiquem uma falha (código de estado 400 ou superior). Isto pode conter informações que podem ajudar a determinar por que motivo o servidor devolveu o código de erro.

-   Pedidos falhados - informações detalhadas sobre pedidos falhados, incluindo um rastreio de componentes IIS utilizada para processar o pedido e o tempo decorrido em cada componente. Isto pode ser útil se está a tentar melhorar o desempenho do site ou isolar o que está a causar um erro HTTP específico a ser devolvido.

-   Web Server registo - informações sobre transações de HTTP utilizando o formato de ficheiro de registo expandido de W3C. Isto é útil ao determinar as métricas de site geral, tais como o número de pedidos processados ou o número de pedidos está a partir de um endereço IP específico.

#### <a name="application-diagnostics"></a>Application diagnostics
[Diagnóstico de aplicação](https://docs.microsoft.com/azure/app-service/web-sites-enable-diagnostic-log) permite-lhe capturar informações produzidas por uma aplicação web. As aplicações do ASP.NET podem utilizar o [Trace](https://msdn.microsoft.com/library/system.diagnostics.trace) classe para registar informações no registo de diagnóstico de aplicação. No Application Diagnostics, existem dois tipos principais de eventos, os relacionados com o desempenho da aplicação e os relacionados com falhas de aplicações e erros. As falhas e os erros podem ser divididos adicional para problemas de conectividade, segurança e de falha. Problemas de falha estão normalmente relacionados com um problema com o código de aplicação.

No Application Diagnostics, pode ver eventos agrupados nas seguintes formas:

-   Todos (apresenta todos os eventos)
-   Erros de aplicações (apresenta eventos de exceção)
-   Desempenho (apresenta eventos de desempenho)

## <a name="storage"></a>Armazenamento
A secção fornece informações adicionais sobre as principais funcionalidades nas informações de segurança e o resumo de armazenamento do Azure sobre estas capacidades.

### <a name="role-based-access-control-rbac"></a>Controlo de Acesso Baseado em Funções (RBAC)
Pode proteger a sua conta de armazenamento com controlo de acesso baseado em funções (RBAC). Restringir o acesso com base no [precisa de saber](https://en.wikipedia.org/wiki/Need_to_know) e [menor privilégio](https://en.wikipedia.org/wiki/Principle_of_least_privilege) princípios de segurança é imperativo para as organizações que pretendem aplicar políticas de segurança para acesso a dados. Estes direitos de acesso são concedidos ao atribuir a função RBAC adequada aos grupos e aplicações num determinado âmbito. Pode utilizar [funções incorporadas do RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles), tais como contribuinte de conta de armazenamento, para atribuir os privilégios aos utilizadores. Acesso ao armazenamento de chaves para uma conta de armazenamento utilizando o [do Azure Resource Manager](https://docs.microsoft.com/azure/storage/storage-security-guide) modelo pode ser controlado através de controlo de acesso baseado em funções (RBAC).

### <a name="shared-access-signature"></a>Assinatura de acesso partilhado
As [assinaturas de acesso partilhado (SAS)](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) disponibilizam acesso delegado a recursos na sua conta de armazenamento. O SAS significa que pode conceder que um cliente limitada permissões para objetos na sua conta de armazenamento durante um período especificado e com um conjunto especificado de permissões. Pode conceder estas permissões limitadas sem ter de partilhar as chaves de acesso da conta.

### <a name="encryption-in-transit"></a>Encriptação em trânsito
A encriptação em trânsito é um mecanismo de proteção de dados quando transmitido através de redes. Com o Storage do Azure, pode proteger dados utilizando:
-   [Encriptação de nível de transporte](https://docs.microsoft.com/azure/storage/storage-security-guide#encryption-in-transit), por exemplo, HTTPS quando a transferência de dados ou a sair do armazenamento do Azure.

-   [Ligar a encriptação](https://docs.microsoft.com/azure/storage/storage-security-guide#using-encryption-during-transit-with-azure-file-shares), tais como [encriptação SMB 3.0](https://docs.microsoft.com/azure/storage/storage-security-guide) para [as partilhas de ficheiros de Azure](https://docs.microsoft.com/azure/storage/storage-dotnet-how-to-use-files).

-   Encriptação do lado do cliente, para encriptar os dados antes de é transferido para o armazenamento e para desencriptar os dados depois de serem transferidos fora do armazenamento.

### <a name="encryption-at-rest"></a>Encriptação inativa
Para muitas organizações, a encriptação de dados Inativos é um passo obrigatório para a privacidade dos dados, conformidade e soberania de dados. Existem três funcionalidades de segurança de armazenamento do Azure que fornecem encriptação de dados "Inativos":

-   [Encriptação do serviço de armazenamento](https://docs.microsoft.com/azure/storage/storage-service-encryption) permite-lhe solicitar que o serviço de armazenamento encriptar dados automaticamente quando escrita ao Storage do Azure.

-   [Encriptação do lado do cliente](https://docs.microsoft.com/azure/storage/storage-client-side-encryption) também fornece a funcionalidade de encriptação de inativos.

-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) permite-lhe encriptar os discos de SO e discos de dados utilizados pela máquina virtual IaaS.

### <a name="storage-analytics"></a>Análise de Armazenamento
[Análise de armazenamento do Azure](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) efetua o registo e fornece dados de métricas para uma conta de armazenamento. Pode utilizar estes dados para rastrear pedidos, analisar tendências de utilização e diagnosticar problemas relacionados com a sua conta de armazenamento. Análise de armazenamento regista informações detalhadas sobre pedidos com êxito e a um serviço de armazenamento. Estas informações podem ser utilizadas para monitorizar os pedidos individuais e diagnosticar problemas com um serviço de armazenamento. Pedidos são registados numa base de melhor esforço. Os seguintes tipos de pedidos autenticados são registados:
-   Pedidos com êxito.

-   Não foi possível pedidos, incluindo o tempo limite, limitação, rede, autorização e outros erros.

-   Pedidos utilizando um acesso assinatura partilhado (SAS), incluindo pedidos falhados e bem-sucedidas.

-   Pedidos de dados de análise.

### <a name="enabling-browser-based-clients-using-cors"></a>A ativação baseada no Browser clientes utilizando a CORS
[Partilha de recursos de várias origens (CORS)](https://docs.microsoft.com/rest/api/storageservices/fileservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) é um mecanismo que permite que os domínios entre si conceder permissão para aceder a recursos entre si. O agente de utilizador envia cabeçalhos adicionais para se certificar de que o código JavaScript carregado a partir de um determinado domínio está autorizado a aceder a recursos localizados no outro domínio. O domínio esta última opção, em seguida, responde com cabeçalhos extras permitir ou negar o acesso de domínio original aos respetivos recursos.

Serviços de armazenamento do Azure suportam agora a CORS para que depois de definir as regras CORS para o serviço, um pedido autenticado corretamente efetuado contra o serviço de outro domínio é avaliado para determinar se é permitido, de acordo com as regras que especificou.
## <a name="networking"></a>Redes
A secção fornece informações adicionais sobre as principais funcionalidades nas informações de segurança e o resumo de rede do Azure sobre estas capacidades.

### <a name="network-layer-controls"></a>Controlos de camada de rede
Controlo de acesso de rede é o ato de limitação de conectividade e de dispositivos específicos ou sub-redes e representa as principais de segurança de rede. O objetivo de controlo de acesso de rede é certificar-se de que as máquinas virtuais e serviços estão acessíveis apenas a utilizadores e dispositivos nos quais pretende-os acessíveis.

#### <a name="network-security-groups"></a>Grupos de Segurança de Rede
A [grupo de segurança de rede (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) é um pacote de monitorização de estado básico filtragem firewall e permite-lhe controlar o acesso com base num [5 cadeias de identificação](https://www.techopedia.com/definition/28190/5-tuple). Os NSGs não fornecem inspeção de camada de aplicação ou autenticado controlos de acesso. Podem ser utilizadas para controlar o tráfego de mover entre sub-redes dentro de uma rede Virtual do Azure e o tráfego entre uma rede Virtual do Azure e a Internet.

#### <a name="route-control-and-forced-tunneling"></a>Controlo de rota e a imposição do túnel
A capacidade para controlar o comportamento de encaminhamento nas redes virtuais do Azure é uma capacidade de controlo de acesso e segurança da rede críticos. Por exemplo, se pretender certificar-se de que todo o tráfego de e para a rede Virtual do Azure passa através desse dispositivo virtual segurança, terá de conseguir controlar e personalizar o comportamento de encaminhamento. Pode fazê-lo através da configuração de rotas definidas pelo utilizador no Azure.

[Rotas definidas pelo utilizador](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) permitem-lhe personalizar os caminhos de entrada e saídos para mover para o tráfego e fora de máquinas virtuais individuais ou sub-redes para assegurar a mais segura possível de rota. [Imposição do túnel](https://www.petri.com/azure-forced-tunneling) é um mecanismo que pode utilizar para se certificar de que os seus serviços não são permitidos para iniciar uma ligação para dispositivos na Internet.

Isto é diferente da que está a ser capaz de aceitar ligações de entrada e, em seguida, responder aos mesmos. Servidores web front-end tem de responder a pedidos de anfitriões da Internet e, por isso, obtido de Internet o tráfego é permitido de entrada a estes servidores web e os servidores web podem responder.

Imposição do túnel costuma é utilizada para forçar o tráfego de saída à Internet para passar por proxies de segurança no local e as firewalls.

#### <a name="virtual-network-security-appliances"></a>Aplicações de segurança de rede virtual
Enquanto os grupos de segurança de rede, rotas definidas pelo utilizador e a imposição do túnel fornecem-lhe um nível de segurança em camadas de rede e o transporte do [modelo OSI](https://en.wikipedia.org/wiki/OSI_model), poderão existir horas em que pretende que ative a segurança de níveis superiores do pilha. Pode aceder a estas funcionalidades de segurança de rede avançada, utilizando uma solução de aplicação de segurança de rede de parceiros do Azure. Pode encontrar a rede de parceiros do Azure mais recente soluções de segurança, visitando o [Azure Marketplace](https://azure.microsoft.com/marketplace/) e procurar por "segurança" e "" segurança de rede.

### <a name="azure-virtual-network"></a>Rede Virtual do Azure

Uma rede virtual do Azure (VNet) é uma representação da sua própria rede na nuvem. É um isolamento lógico dos recursos de infraestrutura de rede do Azure dedicado à sua subscrição. Pode controlar totalmente os blocos de endereços IP, as definições de DNS, as políticas de segurança e as tabelas de rotas dentro desta rede. Pode segmentar a sua VNet em sub-redes e colocar as máquinas virtuais (VMs) IaaS do Azure e/ou [serviços em nuvem (instâncias de função PaaS)](https://docs.microsoft.com/azure/cloud-services/cloud-services-choose-me) em redes virtuais do Azure.

Além disso, pode ligar a rede virtual à sua rede no local através de uma das [opções de conectividade](https://docs.microsoft.com/azure/vpn-gateway/) disponíveis no Azure. Essencialmente, pode expandir a sua rede para o Azure, com controlo total sobre blocos de endereços IP com a vantagem da escala empresarial que o Azure oferece.

Redes do Azure suportam vários cenários de acesso remoto seguro. Algumas destas incluem:

-   [Ligar as estações de trabalho individuais a uma rede Virtual do Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

-   [Estabelecer a ligação de rede no local a uma rede Virtual do Azure com uma VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)

-   [Estabelecer a ligação de rede no local a uma rede Virtual do Azure com uma ligação WAN dedicada](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)

-   [Ligar redes virtuais do Azure para si](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps)

### <a name="vpn-gateway"></a>Gateway de VPN
Para enviar tráfego de rede entre a rede Virtual do Azure e o seu site no local, tem de criar um gateway de VPN da rede Virtual do Azure. A [gateway de VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) é um tipo de gateway de rede virtual que envia o tráfego encriptado através de uma ligação pública. Também pode utilizar gateways de VPN para enviar tráfego entre redes virtuais do Azure através de recursos de infraestrutura de rede do Azure.

### <a name="express-route"></a>ExpressRoute
Microsoft Azure [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) uma ligação WAN dedicada que permite-lhe expandir as suas redes no local para a nuvem da Microsoft através de uma ligação privada dedicada facilitada por um fornecedor de conectividade.

![ExpressRoute](./media/azure-security/azure-security-fig1.png)

Com o ExpressRoute, pode estabelecer ligações aos serviços em nuvem da Microsoft, tais como o Microsoft Azure, Office 365 e o CRM Online. A conectividade pode ser a partir de uma rede qualquer a qualquer (VPN de IP), uma rede Ethernet de ponto a ponto ou uma ligação cruzada virtual através de um fornecedor de conectividade numa localização conjunta.

As ligações do ExpressRoute não passam para a Internet pública e, por conseguinte, podem ser consideradas mais seguras do que as soluções baseadas na VPN. Tal permite que as ligações do ExpressRoute ofereçam mais fiabilidade, velocidades superiores, latências inferiores e uma maior segurança do que as ligações típicas através da Internet.


### <a name="application-gateway"></a>Gateway de Aplicação
Microsoft [Gateway de aplicação do Azure](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) fornece um [controlador de entrega de aplicações (ADC)](https://en.wikipedia.org/wiki/Application_delivery_controller) como um oferta de serviço, as capacidades para a sua aplicação de balanceamento de carga de camada 7 vários.

![Gateway de Aplicação](./media/azure-security/azure-security-fig2.png)

Permite-lhe otimizar a produtividade do web farm ao descarregar a terminação SSL exigente em termos de CPU para o Gateway de aplicação (também conhecido como "Descarga de SSL" ou "Protocolo de bridge SSL"). Também fornece outras funcionalidades de encaminhamento de 7 camadas, incluindo round robin distribuição de tráfego de entrada, afinidade com base no cookie de sessão, URL com base no caminho de encaminhamento e a capacidade de alojar vários sites por trás de um único Gateway de aplicação. O Application Gateway do Azure é um balanceador de carga de 7 camadas.

Fornece ativação pós-falha, pedidos HTTP de encaminhamento de desempenho entre diversos servidores, quer estejam na nuvem ou no local.

Aplicação fornece muitas funcionalidades de controlador de entrega de aplicações (ADC), incluindo HTTP carregar balanceamento, com base no cookie de afinidade de sessão, [Secure Sockets Layer (SSL)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-powershell) descarga, sondas de estado de funcionamento personalizado, o suporte para vários sites, e muitas outras.

### <a name="web-application-firewall"></a>Firewall de Aplicação Web
Firewall de aplicação Web é uma funcionalidade do [Gateway de aplicação do Azure](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) que fornece proteção para aplicações web que utilizam o gateway de aplicação para funções de controlo de entrega de aplicações (ADC) padrão. A Firewall de aplicações Web fá-lo ao protegê-las contra a maioria das 10 principais vulnerabilidades Web da OWASP.

![Firewall de Aplicação Web](./media/azure-security/azure-security-fig1.png)

-   Proteção contra injeção de SQL

-   Proteção contra Ataques Web comuns, como, por exemplo, injeção de comandos, contrabando de pedidos HTTP, divisão de respostas HTTP e ataques remotos de inclusão de ficheiros

-   Proteção contra violações de protocolo HTTP

-   Proteção contra anomalias de protocolo HTTP, como agente de utilizador de anfitrião e cabeçalhos de aceitação em falta

-   Prevenção de contra bots, crawlers e scanners

-   Deteção de aplicação configurações incorretas comuns (ou seja, o Apache, IIS, etc.)


Uma firewall de aplicações Web centralizada contra ataques Web simplifica em muito a gestão da segurança e confere à aplicação uma maior garantia de proteção contra as ameaças de intrusão. Uma solução WAF também pode reagir mais rapidamente a uma ameaça de segurança ao corrigir uma vulnerabilidade conhecida numa localização central, em vez de proteger cada uma das aplicações Web individualmente. Os gateways de aplicação existentes podem ser convertidos num gateway de aplicação com a firewall de aplicações Web facilmente.
### <a name="traffic-manager"></a>Gestor de Tráfego
Microsoft [Traffic Manager do Azure](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview) permite-lhe controlar a distribuição de tráfego do utilizador para pontos finais do serviço em data centers diferentes. Pontos finais de serviço suportados pelo Gestor de tráfego incluem as VMs do Azure, as aplicações Web e serviços em nuvem. Também pode utilizar o Gestor de Tráfego com pontos finais externos, não pertencentes ao Azure. Gestor de tráfego utiliza o sistema de nomes de domínio (DNS) para direcionar os pedidos de cliente para o ponto de final mais adequado, com base num [método de encaminhamento de tráfego](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods) e o estado de funcionamento dos pontos finais.

Gestor de tráfego fornece uma variedade de métodos de encaminhamento de tráfego de acordo com as necessidades de outra aplicação, estado de funcionamento do ponto final [monitorização](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring)e ativação pós-falha automática. Gestor de tráfego seja resistente a falhas, incluindo a falha de uma região do Azure completa.
### <a name="azure-load-balancer"></a>Azure Load Balancer
O [Balanceador de Carga do Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) oferece elevada disponibilidade e elevado desempenho de rede às suas aplicações. É um balanceador de carga de camada 4 (TCP, UDP) que distribui o tráfego de entrada entre instâncias de bom estado de funcionamento dos serviços definidos um conjunto com balanceamento de carga. Balanceador de carga do Azure pode ser configurado para:

-   Carregar saldo tráfego de entrada à Internet para máquinas virtuais. Esta configuração é conhecida como [balanceamento de carga para a Internet](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview).

-   Tráfego de balanceamento de carga entre máquinas virtuais numa rede virtual, entre as máquinas virtuais nos serviços em nuvem ou entre computadores no local e as máquinas virtuais numa rede virtual em vários locais. Esta configuração é conhecida como [balanceamento de carga interna](https://docs.microsoft.com/azure/load-balancer/load-balancer-internal-overview). 

- Encaminhar o tráfego externo para uma máquina virtual específica

### <a name="internal-dns"></a>DNS interno
Pode gerir a lista de servidores DNS utilizados numa VNet no Portal de gestão ou no ficheiro de configuração de rede. Cliente pode adicionar até 12 servidores DNS para cada VNet. Ao especificar servidores DNS, é importante verificar a lista de servidores DNS do cliente na ordem correta para o ambiente do cliente. Listas de servidor DNS não funcionam round robin. São utilizados pela ordem que estão especificadas. Se o primeiro servidor DNS na lista é capaz de ser atingido, o cliente utiliza esse servidor DNS, independentemente se o servidor DNS está a funcionar corretamente ou não. Para alterar a ordem de servidor DNS para a rede virtual do cliente, remova os servidores DNS da lista e adicioná-las novamente na ordem desse cliente pretende. DNS suporta o aspeto de disponibilidade de triad de segurança "CIA".

### <a name="azure-dns"></a>DNS do Azure
O [sistema de nomes de domínio](https://technet.microsoft.com/library/bb629410.aspx), ou de DNS, é responsável por traduzir (ou resolver) um nome de Web site ou serviço para o endereço IP. [O DNS do Azure](https://docs.microsoft.com/azure/dns/dns-overview) é um serviço de alojamento de domínios DNS, fornecer a resolução do nome utilizando a infraestrutura do Microsoft Azure. Ao alojar os seus domínios no Azure, pode gerir os recursos DNS com as mesmas credenciais, APIs, ferramentas e faturação dos seus outros serviços do Azure. DNS suporta o aspeto de disponibilidade de triad de segurança "CIA".
### <a name="log-analytics-nsgs"></a>NSGs de análise do registo
Pode ativar as seguintes categorias de registo de diagnóstico para os NSGs:
-   Evento: Contém entradas para o NSG, as regras são aplicadas para as VMs e as funções de instância com base no endereço MAC. O estado para estas regras é recolhido a cada 60 segundos.

-   Contador de regras: contém entradas para o número de vezes cada regra NSG for aplicada a negar e permitir o tráfego.

### <a name="azure-security-center"></a>Centro de Segurança do Azure
Centro de segurança ajuda-o a evitar, detetar e responder a ameaças e fornece que maior visibilidade e controlo sobre a segurança dos seus recursos Azure. Fornece gestão integrada de políticas e monitorização de segurança nas suas subscrições do Azure, ajuda a detetar ameaças que caso contrário podem passar despercebidas e funciona com um ecossistema abrangente de soluções de segurança. Centro de recomendações de rede em torno firewalls, grupos de segurança de rede, configurar regras de tráfego de entrada e muito mais.

Recomendações de rede disponíveis são os seguintes:

-   [Adicionar uma Firewall da próxima geração](https://docs.microsoft.com/azure/security-center/security-center-add-next-generation-firewall) recomenda que adicionar uma Firewall seguinte de geração (NGFW) a partir de um parceiro da Microsoft para aumentar as proteções de segurança

-   [Encaminhar o tráfego através da NGFW apenas](https://docs.microsoft.com/azure/security-center/security-center-add-next-generation-firewall#route-traffic-through-ngfw-only) recomenda que configurar regras de grupo (NSG) de segurança que forçar o tráfego de entrada à VM através do seu NGFW de rede.

-   [Ativar grupos de segurança de rede em sub-redes ou máquinas virtuais](https://docs.microsoft.com/azure/security-center/security-center-enable-network-security-groups) recomenda que ativar NSGs em sub-redes ou VMs.

-   [Restringir o acesso através da Internet com o ponto final](https://docs.microsoft.com/azure/security-center/security-center-restrict-access-through-internet-facing-endpoints) recomenda que configurar regras de tráfego de entrada do NSGs.


## <a name="compute"></a>Computação

A secção fornece informações adicionais sobre as principais funcionalidades esta área e resumo informações sobre estas capacidades.

### <a name="antimalware--antivirus"></a>Antimalware & antivírus
Com a IaaS do Azure, pode utilizar o software antimalware dos fornecedores de segurança, tais como Microsoft, da Symantec, Micro de tendência, McAfee e Kaspersky para proteger as máquinas virtuais de ficheiros maliciosos, adware e outras ameaças. [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) para máquinas virtuais e serviços Cloud do Azure é uma funcionalidade de proteção que ajuda a identificar e remover vírus, spyware e outro software malicioso. Antimalware da Microsoft fornece configuráveis alertas ao conhecido software malicioso ou indesejável, tentar instalar-se ou executar nos seus sistemas do Azure. Antimalware da Microsoft também podem ser implementada utilizando o Centro de segurança do Azure

### <a name="hardware-security-module"></a>Módulo de hardware de segurança
Encriptação e autenticação não melhorar a segurança, a menos que as chaves próprios estão protegidas. Pode simplificar a gestão e segurança dos seus segredos críticos e chaves armazenando-os na [Cofre de chaves do Azure](https://docs.microsoft.com/azure/key-vault/key-vault-whatis). O Cofre de chaves fornece a opção para armazenar as suas chaves nos módulos de segurança de hardware (HSMs) com certificação FIPS 140-2 de nível 2 normas. Chaves de encriptação do SQL Server para cópia de segurança ou [encriptação transparente de dados](https://msdn.microsoft.com/library/bb934049.aspx) podem todos ser armazenados no Cofre de chaves com quaisquer chaves ou segredos das suas aplicações. As permissões e acesso a estes itens protegidos são geridos através de [do Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

### <a name="virtual-machine-backup"></a>Cópia de segurança da máquina virtual
[Cópia de segurança do Azure](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) é uma solução que protege os dados de aplicação com zero investimento de capital e mínimo operativo os custos. Erros de aplicações podem danificar os dados e erros humanos podem apresentar erros para as aplicações que podem originar problemas de segurança. Com a cópia de segurança do Azure, as máquinas virtuais que executam o Windows e Linux estão protegidas.

### <a name="azure-site-recovery"></a>Azure Site Recovery
Uma parte importante da sua organização [recuperação de desastre/continuidade de negócio (BCDR)](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) estratégia é perceber como manter aplicações e cargas de trabalho empresariais operacional quando planeada e falhas não planeadas ocorrerem. [O Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) ajuda a orquestrar a replicação, ativação pós-falha e recuperação de cargas de trabalho e aplicações para que estão disponíveis a partir de uma localização secundária se a sua localização principal ficar inativo.

### <a name="sql-vm-tde"></a>TDE DE VM DO SQL SERVER
[Encriptação de dados transparente (TDE)](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault) e encriptação de nível de coluna (CLE) são funcionalidades de encriptação do SQL server. Esta forma de encriptação requer que os clientes gerir e armazenar as chaves criptográficas que utiliza para a encriptação.

O serviço Cofre de chaves do Azure (AKV) foi concebido para melhorar a segurança e gestão destas chaves numa localização segura e altamente disponível. Conector do SQL Server permite que o SQL Server para utilizar estas chaves a partir do Cofre de chaves do Azure.

Se estiver a executar o SQL Server com máquinas no local, existem passos que pode seguir para aceder ao Cofre de chaves do Azure a partir do seu computador do SQL Server no local. Mas, para o SQL Server em VMs do Azure, pode poupar tempo ao utilizar a funcionalidade de integração do Cofre de chaves do Azure. Com alguns cmdlets Azure PowerShell para ativar esta funcionalidade, pode automatizar a configuração necessária para uma VM do SQL Server para o seu Cofre de chaves de acesso.

### <a name="vm-disk-encryption"></a>Encriptação de disco da VM
[Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) é uma nova capacidade que ajuda-o a encriptar os discos da máquina virtual do Windows e Linux IaaS. Aplica-se a funcionalidade do BitLocker de padrão da indústria do Windows e a funcionalidade de DM-Crypt do Linux para fornecer a encriptação de volume para o SO e os discos de dados. A solução é integrada com o Cofre de chaves do Azure para o ajudar a controlar e gerir as chaves de encriptação de disco e segredos na sua subscrição do Cofre de chaves. A solução também garante que todos os dados em discos de máquinas virtuais sejam encriptados Inativos no armazenamento do Azure.

### <a name="virtual-networking"></a>Redes virtuais
Máquinas virtuais precisa de conectividade de rede. Para suportar este requisito, o Azure requer que as máquinas virtuais ligadas a uma rede Virtual do Azure. Uma rede Virtual do Azure é uma construção lógica desenvolvida com os recursos de infraestrutura de rede do Azure física. Cada lógica [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) está isolado de todas as outras redes virtuais do Azure. Este isolamento ajuda a assegurar que o tráfego de rede das implementações não está acessível para outros clientes do Microsoft Azure.

### <a name="patch-updates"></a>Atualizações de correção
Atualizações de correção fornecer a base para localizar e corrigir problemas potenciais e simplificar o processo de gestão de atualizações de software, ao reduzir o número de atualizações de software que tem de implementar na sua empresa e ao aumentar a capacidade de monitorizar a conformidade.

### <a name="security-policy-management-and-reporting"></a>Gestão de políticas de segurança e de relatórios
[Centro de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) ajuda a evitar, detetar e responder a ameaças e fornece maior visibilidade e controlo sobre a segurança dos seus recursos Azure. Fornece gestão integrada de políticas e monitorização de segurança nas suas subscrições do Azure, ajuda a detetar ameaças que caso contrário podem passar despercebidas e funciona com um ecossistema abrangente de soluções de segurança.

### <a name="azure-security-center"></a>Centro de Segurança do Azure
O Centro de Segurança ajuda-o a evitar, detetar e responder a ameaças com uma maior visibilidade e controlo sobre a segurança dos seus recursos Azure. Fornece gestão de políticas e monitorização de segurança integrada nas suas subscrições do Azure, ajuda a detetar ameaças que caso contrário podem passar despercebidas e funciona com um ecossistema abrangente de soluções de segurança.

## <a name="identify-and-access-management"></a>Identificar e a gestão de acesso

Proteger os sistemas, aplicações e dados começa com controlos de acesso baseado na identidade. As funcionalidades de gestão de identidades e acessos incorporadas no negócio produtos e serviços Microsoft ajudam a proteger as suas informações pessoais e organização contra acesso não autorizado ao tornando a legítimos utilizadores sempre e onde quer que eles será necessário.

### <a name="secure-identity"></a>Identidade segura
A Microsoft utiliza várias práticas de segurança e tecnologias através dos seus produtos e serviços para gerir identidades e acessos.
-   [Autenticação Multifator](https://azure.microsoft.com/services/multi-factor-authentication/) exige que os utilizadores utilizar vários métodos para acesso, no local e na nuvem. Fornece autenticação forte com uma gama de opções de verificação fácil, enquanto para acomodar os utilizadores com um processo de início de sessão simples.

-   [Microsoft Authenticator](https://aka.ms/authenticator) fornece uma experiência amigável de utilizador do multi-factor Authentication que funciona com o Microsoft Azure Active Directory e as contas Microsoft e inclui suporte para wearables e aprovações com base na identificação digital.

-   [Imposição de políticas de palavra-passe](https://azure.microsoft.com/documentation/articles/active-directory-passwords-policy/) aumenta a segurança de palavras-passe tradicional por impor requisitos de comprimento e complexidade, forçados rotação periódica e o bloqueio da conta após tentativas de autenticação falhada.

-   [Autenticação baseada em tokens](https://azure.microsoft.com/documentation/articles/active-directory-authentication-scenarios/) permite a autenticação através de serviços de Federação do Active Directory (AD FS) ou sistemas de token seguros de terceiros.

-   [O controlo de acesso baseado em funções (RBAC)](https://azure.microsoft.com/documentation/articles/role-based-access-built-in-roles/) permite-lhe conceder acesso com base na função atribuída ao utilizador, facilitando a dar aos utilizadores apenas a quantidade de acesso que precisam para realizar os seus deveres de tarefa. Pode personalizar o RBAC por modelo de negócio da sua organização e tolerância ao risco.

-   [Gestão de identidade (identidade híbrida) integrada](https://azure.microsoft.com/documentation/articles/active-directory-hybrid-identity-design-considerations-overview/) permite-lhe manter o controlo de acesso dos utilizadores entre plataformas internas centros de dados e da nuvem, criar uma identidade de utilizador único para autenticação e autorização para todos os recursos.

### <a name="secure-apps-and-data"></a>Proteger aplicações e dados
[Azure Active Directory](https://azure.microsoft.com/services/active-directory/), uma solução abrangente de identidades e acessos gestão na nuvem, ajuda a proteger o acesso a dados em aplicações no site e na nuvem e simplifica a gestão de utilizadores e grupos. Combina os serviços de diretório de núcleos, avançadas governação de identidade, segurança e gestão de acesso de aplicações e torna mais fácil para que os programadores criem gestão de identidade baseada em política para as suas aplicações. Para melhorar o Azure Active Directory, pode adicionar capacidades pagas utilizando as edições básico do Azure Active Directory, Premium P1 e Premium P2.

| Funcionalidades livres / comuns     | Funcionalidades básicas    |Funcionalidades do Premium P1 |Funcionalidades do Premium P2 | Azure Active Directory associação – funcionalidades de relacionadas apenas Windows 10|
| :------------- | :------------- |:------------- |:------------- |:------------- |
|   [Objetos de diretório](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-editions#directory-objects), [utilizador/grupo de gestão (adicionar/atualizar/eliminar) / baseados no utilizador aprovisionamento, registo de dispositivos](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-editions#usergroup-management-addupdatedelete-user-based-provisioning-device-registration), [único Sign-On (SSO)](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-editions#single-sign-on-sso), [Self-Service Alteração de palavra-passe para os utilizadores de nuvem](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-editions#self-service-password-change-for-cloud-users), [Connect (motor de sincronização que expande os diretórios no local ao Azure Active Directory)](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-editions#connect-sync-engine-that-extends-on-premises-directories-to-azure-active-directory), [segurança / relatórios de utilização](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-editions#securityusage-reports)       |     [Gestão de acesso baseado no grupo / aprovisionamento](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-editions#group-based-access-managementprovisioning), [Repor palavra-passe Self-Service para utilizadores de nuvem](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-editions#self-service-password-reset-for-cloud-users), [(personalização do painel de páginas/acesso de início de sessão) de imagem corporativa da empresa](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-editions#company-branding-logon-pagesaccess-panel-customization), [ Proxy de aplicações](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-editions#application-proxy), [SLA de 99,9%](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-editions#sla-999) |  [Grupos Self-Service e aplicação de gestão/Self-Service aplicação adições/dinâmico grupos](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-editions#self-service-group), [palavra-passe reposição/alterar/desbloqueio do Self-Service, com o local de write-back](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-editions#self-service-password-resetchangeunlock-with-on-premises-write-back), [Multifator Autenticação (em nuvem e no local (servidor MFA))](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-editions#multi-factor-authentication-cloud-and-on-premises-mfa-server), [MIM CAL + servidor MIM](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-editions#mim-cal-mim-server), [Cloud App Discovery](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-editions#cloud-app-discovery), [Connect Health](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-editions#connect-health), [ Rollover de palavra-passe automática para as contas de grupo](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-editions#automatic-password-rollover-for-group-accounts)|     [Proteção de identidade](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-identityprotection), [Privileged Identity Management](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-privileged-identity-management-configure)|    [Associar um dispositivo ao Azure AD, SSO de ambiente de trabalho, o Microsoft Passport para o Azure AD, a recuperação do Bitlocker de administrador](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-editions#join-a-device-to-azure-ad-desktop-sso-microsoft-passport-for-azure-ad-administrator-bitlocker-recovery), [MDM-inscrição automática, a recuperação do Bitlocker Self-Service, administradores locais adicionais para dispositivos Windows 10 através do Azure AD Associação](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-editions#mdm-auto-enrollment)|


- [Cloud App Discovery](https://docs.microsoft.com/azure/active-directory/active-directory-cloudappdiscovery-whatis) é uma funcionalidade de premium do Azure Active Directory permite-lhe identificar aplicações em nuvem que são utilizadas pelos funcionários na sua organização.

- [Azure Active Directory Identity Protection](https://azure.microsoft.com/documentation/articles/active-directory-identityprotection/) é um serviço de segurança que utiliza as capacidades de deteção de anomalias do Azure Active Directory para fornecer uma vista consolidada sobre eventos de risco e potenciais vulnerabilidades que podem afetar o seu identidades da organização.

- [Os serviços de domínio do Active Directory do Azure](https://azure.microsoft.com/services/active-directory-ds/) permite-lhe associar as VMs do Azure a um domínio sem a necessidade de implementar controladores de domínio. Os utilizadores iniciar sessão para estas VMs utilizando as suas credenciais do Active Directory empresarias e totalmente integrada podem aceder aos recursos.

- [O Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) é um serviço de gestão de identidade de elevada disponibilidade, global para aplicações direcionadas para o consumidor que pode dimensionar para centenas de milhões de identidades e integrar nas mobile e web plataformas. Os seus clientes podem iniciar sessão todas as suas aplicações através de experiências personalizáveis que utilizar contas de redes sociais existentes, ou pode criar novas credenciais autónomo.

- [Azure Active Directory B2B colaboração](https://aka.ms/aad-b2b-collaboration) é uma solução de integração do parceiro segura que suporta as relações entre empresas, permitindo a parceiros aceder a aplicações e dados empresariais seletivamente, utilizando as respetivas autogerida identidades.

- [Associação do Active Directory do Azure](https://azure.microsoft.com/documentation/articles/active-directory-azureadjoin-overview/) permite-lhe expandir as capacidades de nuvem para dispositivos Windows 10 para uma gestão centralizada. Este torna possível para os utilizadores ligarem à nuvem da empresa ou organização através do Azure Active Directory e simplifica o acesso a aplicações e recursos.

- [Proxy de aplicações do Active Directory do Azure](https://azure.microsoft.com/documentation/articles/active-directory-application-proxy-get-started/) fornece SSO e acesso remoto seguro para aplicações web alojadas no local.

## <a name="next-steps"></a>Passos Seguintes
- [Introdução à segurança do Microsoft Azure](https://docs.microsoft.com/azure/security/azure-security-getting-started)

Serviços e funcionalidades do Azure que pode utilizar para o ajudar a proteger os seus serviços e dados no Azure

- [Centro de Segurança do Azure](https://azure.microsoft.com/services/security-center/)

Previna, detete e responda a ameaças com maior visibilidade e controlo sobre a segurança dos seus recursos do Azure

- [Monitorização de estado de funcionamento de segurança no Centro de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-monitoring)

Capacidades de monitorização no Centro de segurança do Azure para monitorizar a conformidade com as políticas.
