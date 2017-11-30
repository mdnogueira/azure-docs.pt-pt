---
title: Blueprint de processamento do pagamento para ambientes em conformidade de PCI DSS
description: Requisito de PCI DSS
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 2f1e00a8-0dd6-477f-9453-75424d06a1df
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2017
ms.author: frasim
ms.openlocfilehash: 7f85c8b0377e57f08044bac41dbddbbedb7a4f55
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2017
---
# <a name="azure-blueprint-automation-payment-processing-for-pci-dss-compliant-environments"></a>Blueprint a automatização do Azure: Para ambientes em conformidade de PCI DSS de processamento de pagamento

## <a name="overview"></a>Descrição geral

O processamento do pagamento para ambientes de PCI DSS compatíveis fornece orientações para a implementação de um ambiente de conformidade de PCI DSS plataforma-como-um-serviço (PaaS) adequado para processar dados de cartão de pagamento confidenciais. Este showcases uma arquitetura de referência comuns e foi concebido para simplificar a adoção do Microsoft Azure. Este blueprint ilustra uma solução ponto a ponto para satisfazer as necessidades das organizações para procurar uma abordagem baseada na nuvem para reduzir o custo de implementação e o fardo.

Este blueprint foi concebido para ajudar a cumprir os requisitos de rigorosos pagamento cartão dados segurança as normas do sector (PCI DSS 3.2) para recolha, armazenamento e obtenção de dados do cartão de pagamento. -Demonstra o processamento adequado de dados do cartão de crédito (incluindo dados de verificação, a expiração e número de cartão) num ambiente de várias camado seguro, conforme implementado como um ponto-a-ponto solução PaaS baseado no Azure. Para obter mais informações sobre requisitos de PCI DSS 3.2 e esta solução, consulte [PCI DSS requisitos - descrição geral de High-Level](pci-dss-requirements-overview.md).

Este blueprint destina-se para servir como base para os clientes compreender melhor os requisitos específicos e não devem ser utilizado como-é num ambiente de produção. Não é suficiente para completamente cumprir os requisitos de uma solução de conformidade de PCI DSS para uma solução personalizada implementar uma aplicação para este ambiente sem modificação. Tenha em atenção o seguinte:
- Este blueprint fornece uma linha de base para ajudar os clientes utilizam o Microsoft Azure de uma forma de conformidade de PCI DSS.
- Atingirem a total conformidade de PCI DSS requer que um autorizada qualificado segurança Assessor (QSA) certificar uma solução de cliente de produção.
- Os clientes são responsáveis para realizar a segurança adequada e revisões de conformidade de qualquer solução incorporada com esta arquitetura dos, como os requisitos podem variar com base nas especificações de implementação e na geografia cada cliente.  

Para uma descrição geral de como funciona esta solução, veja este [vídeo](https://aka.ms/pciblueprintvideo) explicar e demonstrar a implementação.

## <a name="solution-components"></a>Componentes da solução

A arquitetura dos inclui os seguintes componentes:

- **Diagrama da arquitetura**. O diagrama mostra a arquitetura de referência utilizada para a solução de Contoso Webstore.
- **Modelos de implementação**. Nesta implementação, [modelos Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview#template-deployment) são utilizados para implementar automaticamente os componentes da arquitetura no Microsoft Azure, especificando os parâmetros de configuração durante a configuração.
- **Scripts de implementação de automatizada**. Estes scripts ajudam a implementar a solução ponto-a-ponto. Os scripts consistem em:
    - Uma instalação do módulo e [administrador global](/azure/active-directory/active-directory-assign-admin-roles-azure-portal) script de configuração é utilizada para instalar e certifique-se de que funções de administrador global e de módulos do PowerShell necessários estão configuradas corretamente.
    - Uma instalação de script do PowerShell é utilizada para implementar a solução ponto-a-ponto, fornecida através de um ficheiro. zip e um ficheiro de bacpac que contêm uma aplicação web de demonstração pré-criadas com [exemplo de base de dados do SQL Server](https://github.com/Microsoft/azure-sql-security-sample). conteúdo. O código de origem para esta solução está disponível para revisão [repositório de código Blueprint de processamento do pagamento][code-repo]. 

## <a name="architectural-diagram"></a>Diagrama da arquitetura

![](images/pci-architectural-diagram.png)

## <a name="user-scenario"></a>Cenário de utilizador

O blueprint aborda o caso de utilização abaixo.

> Este cenário ilustra como um webstore fictício mover o seu cartão de pagamento processar uma solução de PaaS baseado no Azure. A solução processa a recolha de informações de utilizador básico, incluindo os dados de pagamento. A solução não processar pagamentos com estes dados cardholder; Depois dos dados são recolhidos, os clientes são responsáveis por iniciar e concluir as transações com um processador de pagamento. Para obter mais informações, consulte o ["Revisão e orientações para a implementação"](https://aka.ms/pciblueprintprocessingoverview).

### <a name="use-case"></a>Caso de utilização
Um pequeno webstore chamado *Contoso Webstore* está pronto para mudar o respetivo sistema de pagamento para a nuvem. Se tem selecionado o Microsoft Azure para alojar o processo de compra e para permitir que um clerk recolher pagamentos de cartão de crédito dos seus clientes.

O administrador está à procura de uma solução que pode ser rapidamente implementada para atingir os objetivos para uma solução born de nuvem. Ele utilizará esta prova-do-conceito (POC) para discutir com os intervenientes, como o Azure pode ser utilizado para recolher, armazenar e obter dados do cartão de pagamento enquanto complying com os requisitos e Payment Card Industry Data segurança (PCI DSS) rigorosos.

> Será responsável por realizar a segurança adequada e revisões de conformidade de qualquer solução criadas com a arquitetura utilizada por esta POC, como os requisitos podem variar com base nas especificações da sua implementação e de geografia. PCI DSS requer que trabalhe diretamente com um Assessor segurança qualificado autorizada para certificar a sua solução prontos para produção.

### <a name="elements-of-the-foundational-architecture"></a>Elementos da arquitetura do básico

A arquitetura dos foi concebida com os seguintes elementos fictícios:

Site de domínio`contosowebstore.com`

Funções de utilizador utilizadas para ilustrar o caso de utilização e obter informações sobre a interface de utilizador.

#### <a name="role-site-and-subscription-admin"></a>Função: Administração de sites e de subscrição

|Item      |Exemplo|
|----------|------|
|Nome de Utilizador: |`adminXX@contosowebstore.com`|
| Nome: |`Global Admin Azure PCI Samples`|
|Tipo de utilizador:| `Subscription Administrator and Azure Active Directory Global Administrator`|

- A conta de administrador não é possível ler as informações de cartão de crédito desmacaradas. Todas as ações são registadas.
- A conta de administrador não é possível gerir ou iniciar sessão na base de dados SQL.
- A conta de administrador pode gerir subscrições e do Active Directory.

#### <a name="role-sql-administrator"></a>Função: Administrador do SQL Server

|Item      |Exemplo|
|----------|------|
|Nome de Utilizador: |`sqlAdmin@contosowebstore.com`|
| Nome: |`SQLADAdministrator PCI Samples`|
| Nome próprio: |`SQL AD Administrator`|
|Apelido: |`PCI Samples`|
|Tipo de utilizador:| `Administrator`|

- A conta de sqladmin não é possível ver informações de cartão de crédito e não filtradas. Todas as ações são registadas.
- A conta de sqladmin pode gerir a base de dados SQL.

#### <a name="role-clerk"></a>Função: Clerk

|Item      |Exemplo|
|----------|------|
|Nome de Utilizador:| `receptionist_EdnaB@contosowebstore.com`|
| Nome: |`Edna Benson`|
| Nome próprio:| `Edna`|
|Apelido:| `Benson`|
| Tipo de utilizador: |`Member`|

Edna Benson é o Gestor de rececionista e empresariais. Ela é responsável por assegurar que as informações de cliente são precisos e faturação está concluída. Edna é o utilizador iniciado sessão para todas as interações com o site de demonstração Contoso Webstore. Edna tem os seguintes direitos de: 

- Edna pode criar e ler informações de cliente
- Edna pode modificar informações de cliente.
- Edna pode substituir ou substitua o número de cartão de crédito, a expiração e as informações de CVV.

> Contoso Webstore, o utilizador é automaticamente como o **Edna** utilizador para testar as capacidades do ambiente implementado.

### <a name="contoso-webstore---estimated-pricing"></a>Contoso Webstore - estimada de preços

Esta arquitetura básico e a aplicação web de exemplo tem uma estrutura de taxa mensal e um custo de utilização por hora que tem de ser considerado ao dimensionar a solução. Estes custos podem ser estimados utilizando o [Calculadora de custos do Azure](https://azure.microsoft.com/pricing/calculator/). A partir de Setembro de 2017, o custo estimado mensalmente para esta solução é ~ $2500 Isto inclui um custo de utilização de $1000/mo para ASE v2. Estes custos irão variar consoante a quantidade de utilização e estão sujeitos a alterações. É incumbent no cliente para calcular os custos estimados mensais no momento da implementação de uma estimativa mais exata. 

Esta solução utilizados os seguintes serviços do Azure. Detalhes da arquitetura de implementação estão localizados no [arquitetura de implementação](#deployment-architecture) secção.

>- Gateway de Aplicação
>- Azure Active Directory
>- V2 de ambiente de serviço de aplicações
>- Análise de registos do OMS
>- Azure Key Vault
>- Grupos de Segurança de Rede
>- BD SQL do Azure
>- Azure Load Balancer
>- Application Insights
>- Centro de Segurança do Azure
>- Aplicação Web do Azure
>- Automatização do Azure
>- Runbooks de automatização do Azure
>- DNS do Azure
>- Rede Virtual do Azure
>- Máquina Virtual do Azure
>- Grupo de recursos do Azure e políticas
>- Armazenamento de Blobs do Azure
>- Controlo de acesso baseado em funções do Azure Active Directory (RBAC)

## <a name="deployment-architecture"></a>Arquitetura de implementação

A secção seguinte descreve em detalhe os elementos de desenvolvimento e implementação.

### <a name="network-segmentation-and-security"></a>Segmentação de rede e segurança

![](images/pci-tiers-diagram.png)

#### <a name="application-gateway"></a>Gateway de Aplicação

A arquitetura dos reduz o risco de vulnerabilidades de segurança utilizando um Gateway de aplicação com firewall de aplicações web (WAF) e o ruleset OWASP ativada. Capacidades adicionais incluem:

- [Fim para final SSL](/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Ativar [descarga de SSL](/azure/application-gateway/application-gateway-ssl-portal)
- Desativar [TLS v 1.0 e v 1.1](/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Firewall de aplicações Web](/azure/application-gateway/application-gateway-webapplicationfirewall-overview) (modo WAF)
- [Modo de prevenção](/azure/application-gateway/application-gateway-web-application-firewall-portal) com ruleset OWASP 3.0
- Ativar [registo de diagnóstico](/azure/application-gateway/application-gateway-diagnostics)
- [Sondas de estado de funcionamento personalizado](/azure/application-gateway/application-gateway-create-gateway-portal)
- [Centro de segurança do Azure](https://azure.microsoft.com/services/security-center) e [Azure Advisor](/azure/advisor/advisor-security-recommendations) fornecer proteção adicional e notificações. Centro de segurança do Azure também fornece um sistema de reputação de URLs.

#### <a name="virtual-network"></a>Rede virtual

A arquitetura dos define uma rede privada virtual com um espaço de endereços de 10.0.0.0/16.

#### <a name="network-security-groups"></a>Grupos de segurança de rede

Cada uma das camadas de rede tem um grupo de segurança de rede dedicado (NSG):
- Um grupo de segurança de rede DMZ para Gateway de aplicação WAF e firewall
- Um NSG para gestão jumpbox (anfitrião de bastion)
- Um NSG para o ambiente de serviço de aplicações

Cada um dos NSGs ter específicas portas e protocolos abertos para a operação segura e correta da solução. Para obter mais informações, consulte [orientações de PCI - grupos de segurança de rede](#network-security-groups).

Cada um dos NSGs ter específicas portas e protocolos abertos para o trabalho seguro e correto da solução. Além disso, as configurações seguintes estão ativadas para cada NSG:
- Ativado [eventos e registos de diagnóstico](/azure/virtual-network/virtual-network-nsg-manage-log) são armazenados na conta de armazenamento 
- Ligado a análise de registos do OMS para o [diagnóstico do NSG](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

#### <a name="subnets"></a>Sub-redes
 Certifique-se de que cada sub-rede está associada a respetiva NSG correspondente.

#### <a name="custom-domain-ssl-certificates"></a>Certificados SSL de domínio personalizado
 Tráfego HTTPS está ativado para utilizar um certificado SSL de domínio personalizado.

### <a name="data-at-rest"></a>Dados Inativos

A arquitetura protege os dados Inativos através da utilização de encriptação, a auditoria de base de dados e de outras medidas.

#### <a name="azure-storage"></a>Storage do Azure

Para satisfazer os requisitos de dados em rest encriptados, todos os [Storage do Azure](https://azure.microsoft.com/services/storage/) utiliza [encriptação do serviço de armazenamento](/azure/storage/storage-service-encryption).

#### <a name="azure-sql-database"></a>Base de Dados SQL do Azure

A instância de SQL Database do Azure utiliza as seguintes medidas de segurança da base de dados:

- [Autorização e autenticação do AD](/azure/sql-database/sql-database-aad-authentication)
- [Auditoria de base de dados SQL](/azure/sql-database/sql-database-auditing-get-started)
- [Encriptação transparente de dados](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)
- [Regras de firewall](/azure/sql-database/sql-database-firewall-configure), que permite para a gestão de IP de cliente e os conjuntos de trabalho ASE
- [Deteção de ameaças do SQL Server](/azure/sql-database/sql-database-threat-detection-get-started)
- [Sempre encriptadas colunas](/azure/sql-database/sql-database-always-encrypted-azure-key-vault)
- [Máscara de dados dinâmicos da base de dados SQL](/azure/sql-database/sql-database-dynamic-data-masking-get-started), utilizando o script do PowerShell de pós-implementação

### <a name="logging-and-auditing"></a>Registo e auditoria

[Operations Management Suite (OMS)](/azure/operations-management-suite/) pode fornecer o Contoso Webstore com o registo de um vasto conjunto de toda a atividade de utilizador e de sistema, incluem registo de dados de cardholder. As alterações podem ser revistas e podem ser verificadas em termos de exatidão. 

- **Registos de atividade:**[registos de atividade](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fornecem informações aprofundadas as operações que foram executadas no recursos na sua subscrição.
- **Os registos de diagnóstico:**[registos de diagnóstico](/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) são emitidos por cada recurso de todos os registos. Estes registos incluem registos de sistema de eventos do Windows, armazenamento de Blobs do Azure, tabelas e registos de fila.
- **Registos de firewall:** o Gateway de aplicação fornece completa diagnóstico e aceder a registos. Estão disponíveis recursos de Gateway de aplicação que tenham WAF ativada de registos de firewall.
- **Arquivo de registo:** todos os registos de diagnóstico são configurados para escrever uma conta de armazenamento do Azure centralizada e encriptados para arquivo com um período de retenção definido (2 dias). Os registos, em seguida, estão ligados ao Log Analytics do Azure para o processamento, armazenamento e dashboarding. [Análise de registo](https://azure.microsoft.com/services/log-analytics) é um serviço do OMS que ajuda a recolher e analisar dados gerados pelos recursos na sua nuvem e no local ambientes.

### <a name="encryption-and-secrets-management"></a>Gestão de encriptação e segredos

O Contoso Webstore encripta todos os dados de cartão de crédito e utiliza o Cofre de chaves do Azure para gerir chaves, impedir a obtenção de CHD.

- [O Cofre de chaves do Azure](https://azure.microsoft.com/services/key-vault/) ajuda a salvaguardar as chaves criptográficas e segredos utilizados pelas aplicações em nuvem e de serviços. 
- [SQL TDE](/sql/relational-databases/security/encryption/transparent-data-encryption) é utilizado para encriptar todos os dados do cliente cardholder, data de expiração e CVV.
- Os dados são armazenados no disco utilizando [Azure Disk Encryption](/azure/security/azure-security-disk-encryption) e o BitLocker.

### <a name="identity-management"></a>Gestão de identidades

As seguintes tecnologias de fornecem a identidade de capacidades de gestão no ambiente do Azure.
- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) é multi-inquilino baseado na nuvem diretório e identidade do serviço de gestão da Microsoft. Todos os utilizadores para a solução foram criados no Azure Active Directory, incluindo os utilizadores que acedem a base de dados do SQL Server.
- Autenticação para a aplicação é executada a utilizar o Azure AD. Para obter mais informações, consulte [integrar aplicações com o Azure Active Directory](/azure/active-directory/develop/active-directory-integrating-applications). Além disso, a encriptação de coluna de base de dados também utiliza o Azure AD para autenticar a aplicação a SQL Database do Azure. Para obter mais informações, consulte [sempre encriptados: proteger os dados sensíveis na base de dados SQL](/azure/sql-database/sql-database-always-encrypted-azure-key-vault). 
- [Azure Active Directory Identity Protection](/azure/active-directory/active-directory-identityprotection) Deteta potenciais vulnerabilidades que afetam as identidades da organização, configura as respostas automatizadas detetadas ações suspeitas relacionada com as identidades da organização, e investiga incidentes suspeitas e executa uma ação adequada resolvê-los.
- [Azure baseada em funções controlo de acesso (RBAC)](/azure/active-directory/role-based-access-control-configure) permite precisamente direcionadas para gestão de acesso ao Azure. Acesso de subscrição está limitado ao administrador da subscrição e o acesso do Cofre de chaves do Azure é restringido a todos os utilizadores.

Para saber mais sobre como utilizar as funcionalidades de segurança da base de dados do Azure SQL, consulte o [aplicação de demonstração do Contoso Clinic](https://github.com/Microsoft/azure-sql-security-sample) exemplo.
   
### <a name="web-and-compute-resources"></a>Recursos de computação e de Web

#### <a name="app-service-environment"></a>Ambiente do Serviço de Aplicações

[App Service do Azure](/azure/app-service/) é um serviço gerido para a implementação de aplicações web. A aplicação Contoso Webstore é implementada como um [aplicação Web do App Service](/azure/app-service-web/app-service-web-overview).

[Ambiente de serviço de aplicações do Azure (ASE v2)](/azure/app-service/app-service-environment/intro) é uma funcionalidade do serviço de aplicações que fornece um ambiente completamente isolado e dedicado para execução segura de aplicações do App Service numa escala elevada. é um plano de serviço Premium utilizado por esta arquitetura para ativar a conformidade de PCI DSS.

ASEs isoladas-se de que apenas as aplicações de um único cliente em execução e sempre são implementadas numa rede virtual. Os clientes não têm controlo detalhado sobre o tráfego de rede de aplicação de entrada e saída e as aplicações podem estabelecer ligações seguras de alta velocidade através de redes virtuais aos recursos da empresa no local.

Utilização de ASEs para esta arquitetura permitida para controlos/configurações seguintes:

- Alojar dentro de uma rede Virtual e uma rede protegida regras de segurança
- ASE configurado com o certificado autoassinado ILB para comunicação HTTPS
- [Modo de balanceamento de carga interno](/azure/app-service-web/app-service-environment-with-internal-load-balancer) (modo 3)
- Desativar [TLS 1.0](/azure/app-service-web/app-service-app-service-environment-custom-settings) -um protocolo TLS, que é preterido a partir de um ponto de vista de PCI DSS
- Alteração [cifras TLS](/azure/app-service-web/app-service-app-service-environment-custom-settings)
- Controlo [portas de N/W de tráfego de entrada](/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic) 
- [WAF – restringir dados](/azure/app-service-web/app-service-app-service-environment-web-application-firewall)
- Permitir [tráfego de base de dados SQL](/azure/app-service-web/app-service-app-service-environment-network-architecture-overview)


#### <a name="jumpbox-bastion-host"></a>Jumpbox (anfitrião de bastion)

Como o ambiente de serviço de aplicações está protegido e bloqueado, tem de haver um mecanismo para permitir qualquer DevOps versões ou as alterações que poderão ser necessárias, tais como a capacidade de monitorizar a aplicação web utilizando o Kudu. Máquina virtual está protegida por trás do Balanceador de carga de NAT que lhe permite ligar a VM numa porta diferente de TCP 3389. 

Uma máquina virtual foi criada como um jumpbox (anfitrião de bastion) com as seguintes configurações:

-   [Extensão de antimalware](/azure/security/azure-security-antimalware)
-   [Extensão do OMS](/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Extensão de diagnóstico do Azure](/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Azure Disk Encryption](/azure/security/azure-security-disk-encryption) com o Cofre de chaves do Azure (respeita Azure Government, PCI DSS, HIPAA e outros requisitos).
-   Um [política de encerramento automático](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) reduzir o consumo de recursos de máquina virtual quando não está em utilização.

### <a name="security-and-malware-protection"></a>Proteção de segurança e software maligno

[Centro de segurança do Azure](https://azure.microsoft.com/services/security-center/) fornece uma vista centralizada de estado de segurança dos seus recursos do Azure. De forma rápida, pode verificar que os controlos de segurança adequados são cumpridos e corretamente configurado e que possa identificar rapidamente quaisquer recursos que necessitam da atenção.  

[Azure Advisor](/azure/advisor/advisor-overview) é um consultor de nuvem personalizado que o ajuda a seguir as melhores práticas para otimizar as implementações do Azure. Este analisa a telemetria de utilização e configuração do recurso e, em seguida, recomenda soluções que podem ajudar a melhorar a eficácia de custo, desempenho, elevada disponibilidade e segurança dos seus recursos Azure.

[Microsoft Antimalware](/azure/security/azure-security-antimalware) para máquinas virtuais e serviços Cloud do Azure é a capacidade de proteção em tempo real, que ajuda a identificar e remover vírus, spyware e outro software malicioso, com alertas configuráveis ao conhecido malicioso ou indesejável software tenta instalar-se ou executar nos seus sistemas do Azure.

### <a name="operations-management"></a>Gestão de operações

#### <a name="application-insights"></a>Application Insights

Utilize [Application Insights](https://azure.microsoft.com/services/application-insights/) para obter conhecimentos acionáveis através da gestão de desempenho de aplicações e a análise instantânea.

#### <a name="log-analytics"></a>Log analytics

[Análise de registo](https://azure.microsoft.com/services/log-analytics/) é um serviço no Operations Management Suite (OMS) que o ajuda a recolher e analisar dados gerados pelos recursos na sua nuvem e no local ambientes.

#### <a name="oms-solutions"></a>Soluções do OMS

Estas soluções adicionais do OMS devem ser consideradas e configuradas:
- [Log Analytics da Atividade](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)
- [Análise de Redes do Azure](/azure/log-analytics/log-analytics-azure-networking-analytics?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [Análise SQL do Azure](/azure/log-analytics/log-analytics-azure-sql)
- [Monitorização de Alterações](/azure/log-analytics/log-analytics-change-tracking?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [Análise do Cofre de Chaves](/azure/log-analytics/log-analytics-azure-key-vault?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [Mapa do Serviço](/azure/operations-management-suite/operations-management-suite-service-map)
- [Auditoria e segurança](https://www.microsoft.com/cloud-platform/security-and-compliance)
- [Antimalware](/azure/log-analytics/log-analytics-malware?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [Gestão de Atualizações](/azure/operations-management-suite/oms-solution-update-management)

### <a name="security-center-integration"></a>Integração do Centro de segurança

Destina-se a implementação predefinida para fornecer uma linha de base das recomendações do Centro de segurança, que indica um Estado de bom estado de funcionamento e segura de configuração. Pode ativar a recolha de dados a partir do Centro de segurança do Azure. Para obter mais informações, consulte [Centro de segurança do Azure - introdução](/azure/security-center/security-center-get-started).

## <a name="deploy-the-solution"></a>Implementar a solução

Os componentes para implementar esta solução estão disponíveis no [repositório de código de PCI Blueprint][code-repo]. A implementação da arquitetura dos requer vários passos executados através do Microsoft PowerShell v5. Para ligar ao Web site, tem de fornecer um nome de domínio personalizado (tal como contoso.com). Isto for especificado utilizando o `-customHostName` comutador no passo 2. Para obter mais informações, consulte [comprar um nome de domínio personalizado para Web Apps do Azure](/azure/app-service-web/custom-dns-web-site-buydomains-web-app). Um nome de domínio personalizado não é necessário para implementar e executar a solução com êxito, mas não será possível ligar ao Web site para fins de demonstração.

Os scripts de adicionar utilizadores de domínio para o inquilino do Azure AD que especificar. Recomendamos que crie um novo inquilino do Azure AD para utilizar como um teste.

Se ocorrerem problemas durante a implementação, consulte [FAQ e resolução de problemas](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/pci-faq.md).

É altamente recomendável que uma instalação limpa do PowerShell utilizados para implementar a solução. Em alternativa, certifique-se de que está a utilizar os mais recentes módulos necessários para execução adequada dos scripts de instalação. Neste exemplo, inicie sessão no jumpbox (anfitrião de bastion) e execute os seguintes comandos. Tenha em atenção que isto permite que o comando de domínio personalizado.


1. Instalar módulos necessários e configurar corretamente as funções de administrador.
 
    ```powershell
     .\0-Setup-AdministrativeAccountAndPermission.ps1 
        -azureADDomainName contosowebstore.com
        -tenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
        -subscriptionId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
        -configureGlobalAdmin 
        -installModules
    ```
    Para obter instruções de utilização detalhada, consulte [Script instruções - conta administrativa do programa de configuração e permissão](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/0-Setup-AdministrativeAccountAndPermission.md).
    
2. Instalar a gestão de atualizações de solução 
 
    ```powershell
    .\1-DeployAndConfigureAzureResources.ps1 
        -resourceGroupName contosowebstore
        -globalAdminUserName adminXX@contosowebstore.com 
        -globalAdminPassword **************
        -azureADDomainName contosowebstore.com 
        -subscriptionID XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX 
        -suffix PCIcontosowebstore
        -customHostName contosowebstore.com
        -sqlTDAlertEmailAddress edna@contosowebstore.com 
        -enableSSL
        -enableADDomainPasswordPolicy 
    ```
    
    Para obter instruções de utilização detalhada, consulte [instruções de Script – implementar e configurar recursos do Azure](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/1-DeployAndConfigureAzureResources.md).
    
3. OMS registo e monitorização. Assim que a solução for implementada, uma [Microsoft Operations Management Suite (OMS)](/azure/operations-management-suite/operations-management-suite-overview) pode ser aberta a área de trabalho e os modelos de exemplo fornecidos no repositório de solução podem ser utilizados para ilustrar a forma como pode ser um dashboard de monitorização configurado. Para os modelos do OMS de exemplo, consulte o [omsDashboards pasta](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/1-DeployAndConfigureAzureResources.md). Tenha em atenção que os dados devem ser recolhidos no OMS para modelos para implementar corretamente. Isto pode demorar até uma hora ou mais, dependendo da atividade do site.
 
    Quando configurar o registo do OMS, considere incluindo estes recursos:
 
    - Microsoft.Network/applicationGateways
    - Microsoft.Network/NetworkSecurityGroups
    - Microsoft.Web/serverFarms
    - Microsoft.Sql/servers/databases
    - Microsoft.Compute/virtualMachines
    - Microsoft.Web/sites
    - Microsoft.KeyVault/Vaults
    - Microsoft.Automation/automationAccounts
 

    
## <a name="threat-model"></a>Modelo de ameaça

Um diagrama de fluxo de dados (DFD) e o modelo de ameaça de exemplo para a Contoso Webstore [modelo de ameaça Blueprint de processamento de pagamento](https://aka.ms/pciblueprintthreatmodel).

![](images/pci-threat-model.png)



## <a name="customer-responsibility-matrix"></a>Matriz de responsabilidade do cliente

Os clientes são responsáveis por manter uma cópia do [matriz de resumo de responsabilidade](https://aka.ms/pciblueprintcrm32), que descreve os requisitos de PCI DSS são da responsabilidade do cliente e os que são da responsabilidade do Microsoft Azure.

## <a name="pci-compliance-review"></a>Revisão de conformidade de PCI 

A solução foi revista pela Coalfire sistemas, Inc. (PCI-DSS qualificado segurança Assessors). O [Reveja de conformidade de PCI e as diretrizes para implementação](https://aka.ms/pciblueprintprocessingoverview) fornece rever um auditor da solução e considerações para transformar blueprint para uma implementação de prontos para produção.

## <a name="disclaimer-and-acknowledgements"></a>Exclusão de responsabilidade e confirmações

*Setembro de 2017*

- Este documento destina-se apenas a fins informativos. MICROSOFT E AVYAN NÃO TORNAR NÃO OFERECE NENHUMA GARANTIA, EXPRESSA, IMPLÍCITA OU ESTATUTARIAMENTE AS INFORMAÇÕES NESTE DOCUMENTO. Este documento é fornecido "como-está." As informações e opiniões expressados neste documento, incluindo URLs e outras referências de Web site da Internet, podem ser alteradas sem aviso prévio. Os clientes ler este documento da sua responsabilidade utilizá-la.  
- Este documento não fornece aos clientes com quaisquer direitos legais a nenhuma propriedade intelectual em qualquer produto da Microsoft ou Avyan ou soluções.  
- Os clientes podem copiar e utilizar este documento para efeitos de referência interno.  

  > [!NOTE]
  > Algumas recomendações neste documento poderão resultar numa maior dados, a rede ou a utilização de recursos de computação no Azure e podem aumentar os custos de licenciamento ou de subscrição do Azure do cliente.  

- A solução este documento destina-se como uma arquitetura básico e não podem ser utilizada como-é para fins de produção. Atingirem a total conformidade de PCI requer que os clientes Consulte com os respetivos qualificado Assessor de segurança.  
- Todos os nomes de cliente, os registos de transações e quaisquer dados relacionados nesta página são fictícios, criado com esta arquitetura fundamentais sobre o objetivo e fornecido para fins de ilustração apenas. Nenhuma associação ou ligação destina-se e deve ser inferido nenhum.  
- Esta solução foi desenvolvida jointly pela Microsoft e consultadoria Avyan e está disponível na [licença MIT](https://opensource.org/licenses/MIT).
- Esta solução tem foram revista por Coalfire, auditor de PCI-DSS da Microsoft. O [Reveja de conformidade de PCI](https://aka.ms/pciblueprintcrm32) fornece uma revisão independente, independente da solução e componentes que precisam de ser corrigidos. 

### <a name="document-authors"></a>Autores de documentos

- *O Frank Simorjay (Microsoft)*  
- *Gururaj Pandurangi (consultadoria Avyan)*


[code-repo]: https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms "Repositório de código"
