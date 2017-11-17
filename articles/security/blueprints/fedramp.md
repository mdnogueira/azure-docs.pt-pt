---
title: "Automatização do Azure Blueprint - aplicações Web para FedRAMP"
description: "Automatização do Azure Blueprint - aplicações Web para FedRAMP"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 8fe47cff-9c24-49e0-aa11-06ff9892a468
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: d0521d68bab8bd0b7db53a512da6d37033abd85e
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="azure-blueprint-automation---web-applications-for-fedramp"></a>Automatização do Azure Blueprint - aplicações Web para FedRAMP

## <a name="overview"></a>Descrição geral

O [risco Federal e o programa de gestão de autorização (FedRAMP)](https://www.fedramp.gov), é um programa de toda a government E.U.A. que fornece uma abordagem padronizada para avaliação de segurança, autorização e monitorização contínua para produtos de nuvem e serviços. Este Blueprint a automatização do Azure - aplicações Web para FedRAMP fornece orientações para a implementação de uma infraestrutura em conformidade FedRAMP como um ambiente de serviço (IaaS) adequado para uma simple aplicação de web para a Internet. Esta solução automatiza a implementação e configuração de recursos do Azure para uma arquitetura de referência comum, que demonstra formas em que os clientes podem cumprir os requisitos específicos de segurança e conformidade e funciona como uma foundation para que os clientes criem e Configure as suas próprias soluções no Azure. A solução implementa um subconjunto dos controlos da linha de base FedRAMP elevado, com base no SP do NIST 800-53. Para obter mais informações sobre requisitos de FedRAMP alta e esta solução, consulte [FedRAMP elevada requisitos - descrição geral de High-Level](fedramp-controls-overview.md). ***Nota: Esta solução implementa Azure Government.***

Esta arquitetura destina-se para servir como base para os clientes ajustar os seus requisitos específicos e não devem ser utilizada como-é num ambiente de produção. Não é suficiente para completamente cumprir os requisitos de linha de base FedRAMP alta implementar uma aplicação para este ambiente sem modificação. Tenha em atenção o seguinte:
- Esta arquitetura fornece uma linha de base para ajudar os clientes a utilizar o Azure de forma FedRAMP compatíveis.
- Os clientes são responsáveis para realizar a segurança adequada e avaliação de compatibilidade de qualquer solução incorporada com esta arquitetura, como os requisitos podem variar com base nas especificações de implementação de cada cliente. 

Para uma descrição geral de como funciona esta solução, veja este [vídeo](https://aka.ms/fedrampblueprintvideo) explicar e demonstrar a implementação.

Clique em [aqui](https://aka.ms/fedrampblueprintrepo) para obter instruções de implementação.

## <a name="solution-components"></a>Componentes da solução

Esta automatização do Azure Blueprint implementa automaticamente uma arquitetura de referência de aplicação web IaaS com controlos de segurança pré-configurada para ajudar os clientes obter a conformidade com os requisitos de FedRAMP. A solução é constituído por modelos Azure Resource Manager e scripts do PowerShell que guiam de implementação de recursos e a configuração. Que acompanha Azure Blueprint [documentação de conformidade](#compliance-documentation) é fornecido com a indicação de herança de controlo de segurança do Azure e os recursos implementados e configurações que se alinham com SP do NIST 800-53 controlos de segurança, deste modo, permitir que as organizações fast-controlar obrigações de conformidade.

## <a name="architecture-diagram"></a>Diagrama da arquitetura

Esta solução implementa uma arquitetura de referência para uma aplicação web de IaaS com um back-end de base de dados. A arquitetura inclui uma camada web, dados de camada de infraestrutura do Active Directory, o gateway de aplicação e o Balanceador de carga. Máquinas virtuais implementadas para os escalões web e os dados estão configuradas num conjunto de disponibilidade e instâncias do SQL Server estão configuradas num grupo de Disponibilidade AlwaysOn para elevada disponibilidade. Máquinas virtuais estão associados a um domínio e as políticas de grupo do Active Directory são utilizadas para impor a segurança e conformidade configurações ao nível do sistema operativo. Um jumpbox de gestão (anfitrião de bastion) fornece uma ligação segura para os administradores de recursos de acesso implementado.

![texto alternativo](images/fedramp-architectural-diagram.png?raw=true "IaaS web automatização Blueprint de aplicação para ambientes em conformidade FedRAMP")

Esta solução utiliza os seguintes serviços do Azure. Detalhes da arquitetura de implementação estão localizados no [arquitetura de implementação](#deployment-architecture) secção.

* **Máquinas virtuais do Azure**
    - (1) gestão/bastion (Windows Server 2016 Datacenter)
    - (2) controlador de domínio o active Directory (Windows Server 2016 Datacenter)
    - (2) nó de cluster do SQL Server (SQL Server 2016 no Windows Server 2012 R2)
    - (1) testemunho do SQL Server (Windows Server 2016 Datacenter)
    - (2) web/IIS (Centro de dados do Windows Server 2016)
* **Conjuntos de Disponibilidade**
    - (1) controladores de domínio o active Directory
    - (1) nós de cluster do SQL Server e da testemunha
    - (1) web/IIS
* **Rede Virtual do Azure**
    - redes virtuais /16 (1)
    - sub-redes /24 (5)
    - As definições de DNS estão definidas em ambos os controladores de domínio
* **Azure Load Balancer**
    - (1) Balanceador de carga do SQL Server
* **Gateway de aplicação do Azure**
    - (1) Gateway de aplicação WAF ativada
      - Modo de firewall: prevenção
      - Conjunto de regras: OWASP 3.0
      - Serviço de escuta: Porta 443
* **Armazenamento do Azure**
    - (7) as contas de armazenamento georredundante
* **Azure Backup**
    - (1) o Cofre de serviços de recuperação
* **Cofre de Chaves do Azure**
    - (1) a chave Cofre
* **Azure Active Directory**
* **Azure Resource Manager**
* **Log Analytics do Azure**
* **Automatização do Azure**
    - (1) conta de automatização
* **Operations Management Suite**
    - (1) área de trabalho OMS

## <a name="deployment-architecture"></a>Arquitetura de implementação

A secção seguinte descreve em detalhe os elementos de desenvolvimento e implementação.

### <a name="network-segmentation-and-security"></a>Segmentação de rede e segurança

#### <a name="application-gateway"></a>Gateway de Aplicação

A arquitetura reduz o risco de vulnerabilidades de segurança utilizando um Gateway de aplicação com firewall de aplicações web (WAF) e o ruleset OWASP ativada. Capacidades adicionais incluem:

- [Fim para final SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Ativar [descarga de SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-portal)
- Desativar [TLS v 1.0 e v 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Firewall de aplicações Web](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (modo WAF)
- [Modo de prevenção](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) com ruleset OWASP 3.0

#### <a name="virtual-network"></a>Rede virtual

A arquitetura define uma rede privada virtual com um espaço de endereços de 10.200.0.0/16.

#### <a name="network-security-groups"></a>Grupos de segurança de rede

Esta solução implementa recursos uma arquitetura com uma sub-rede separada web, uma sub-rede de base de dados, sub-rede do Active Directory e sub-rede de gestão dentro de uma rede virtual. Sub-redes logicamente estão separadas por regras de grupos de segurança de rede aplicadas às sub-redes individuais para restringir o tráfego entre sub-redes de forma a que só é necessário para a funcionalidade de gestão e de sistema.

Consulte a configuração para [grupos de segurança de rede](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) implementado com esta solução. As organizações podem configurar grupos de segurança de rede editando o ficheiro acima utilizando [esta documentação](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) como guia.

Cada um das sub-redes tem um grupo de segurança de rede dedicado (NSG):
- 1 NSG para Gateway de aplicação (LBNSG)
- 1 NSG para Jumpbox (MGTNSG)
- 1 NSG principais e cópia de segurança para controladores de domínio (ADNSG)
- 1 NSG para servidores SQL e o testemunho de partilha de ficheiros (SQLNSG)
- 1 NSG para a camada Web (WEBNSG)

#### <a name="subnets"></a>Sub-redes

Cada sub-rede está associado a respetiva NSG correspondente.

### <a name="data-at-rest"></a>Dados Inativos

A arquitetura protege os dados Inativos utilizando várias medidas de encriptação.

#### <a name="azure-storage"></a>Storage do Azure

Para satisfazer os requisitos de encriptação de dados em rest, todas as contas de armazenamento utilizam [encriptação do serviço de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

#### <a name="sql-database"></a>Base de Dados SQL

Base de dados do SQL Server está configurado para utilizar [encriptação de dados transparente (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption), que efetua a encriptação em tempo real e a desencriptação de dados e registo de ficheiros para proteger informações inativos. TDE fornece assurance que armazenados os dados não foi sujeito acesso não autorizado. 

#### <a name="azure-disk-encryption"></a>Azure Disk Encryption

Encriptação de disco do Azure é utilizado para encriptados discos da máquina virtual IaaS do Windows. [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) aproveita a funcionalidade de BitLocker do Windows para fornecer a encriptação de volume para o SO e discos de dados. A solução é integrada com o Cofre de chaves do Azure para ajudar a controlar e gerir as chaves de encriptação de disco.

### <a name="logging-and-auditing"></a>Registo e auditoria

[Operations Management Suite (OMS)](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) fornece extenso registo de atividade de utilizador e de sistema, bem como o estado de funcionamento do sistema. 

- **Registos de atividade:**[registos de atividade](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fornecem informações aprofundadas as operações que foram executadas no recursos na sua subscrição.
- **Os registos de diagnóstico:**[registos de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) são emitidos por cada recurso de todos os registos. Estes registos incluem registos de sistema de eventos do Windows, os registos de armazenamento do Azure, os registos de auditoria do Cofre de chaves e os registos de acesso e de firewall de Gateway de aplicação.
- **Arquivo de registo:** registos de atividade do Azure e os registos de diagnóstico podem ser ligados ao Log Analytics do Azure para processamento, armazenamento e dashboarding. Período de retenção é configurável pelo utilizador cópias de segurança para dia 730 satisfazer os requisitos de retenção específico da organização.

### <a name="secrets-management"></a>Gestão de segredos

A solução utiliza o Cofre de chaves do Azure para gerir as chaves e segredos.

- [O Cofre de chaves do Azure](https://azure.microsoft.com/services/key-vault/) ajuda a salvaguardar as chaves criptográficas e segredos utilizados pelas aplicações em nuvem e de serviços. 
- A solução é integrada com o Cofre de chaves do Azure para gerir chaves de encriptação de disco de máquina virtual IaaS e segredos.

### <a name="identity-management"></a>Gestão de identidades

As seguintes tecnologias de fornecem a identidade de capacidades de gestão no ambiente do Azure.
- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) é multi-inquilino baseado na nuvem diretório e identidade do serviço de gestão da Microsoft.
- Autenticação para uma aplicação web implementada de cliente pode ser efetuada utilizando o Azure AD. Para obter mais informações, consulte [integrar aplicações com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).  
- [Azure baseada em funções controlo de acesso (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) permite precisamente direcionadas para gestão de acesso ao Azure. Acesso de subscrição está limitado ao administrador da subscrição e acesso a recursos pode ser limitado com base na função de utilizador.
- Uma instância do Active Directory IaaS implementada fornece gestão de identidades ao nível do SO para as máquinas virtuais do IaaS implementadas.
   
### <a name="compute-resources"></a>Recursos de computação

#### <a name="web-tier"></a>Camada Web

A solução implementa máquinas de virtuais de camada web num [do conjunto de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Conjuntos de disponibilidade Certifique-se de que as máquinas virtuais estão distribuídas por vários clusters de hardware isolado para melhorar a disponibilidade.

#### <a name="database-tier"></a>Camada de base de dados

A solução implementa máquinas virtuais de camada de base de dados num conjunto de disponibilidade como um [grupo de Disponibilidade AlwaysOn](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview). Fornece a funcionalidade de grupo Always On disponibilidade para as capacidades de elevada disponibilidade e recuperação após desastre. 

#### <a name="active-directory"></a>Active Directory

Todas as máquinas virtuais implementadas pela solução estão associados a um domínio e as políticas de grupo do Active Directory são utilizadas para impor a segurança e conformidade configurações ao nível do sistema operativo. Máquinas de virtuais do Active Directory são implementadas num conjunto de disponibilidade.


#### <a name="jumpbox-bastion-host"></a>Jumpbox (anfitrião de bastion)

Um jumpbox de gestão (anfitrião de bastion) fornece uma ligação segura para os administradores de recursos de acesso implementado. O NSG associado à sub-rede de gestão onde está localizada a máquina virtual jumpbox permite ligações só TCP na porta 3389 para RDP. 

### <a name="malware-protection"></a>Proteção contra software maligno

[Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) para máquinas virtuais fornece a capacidade de proteção em tempo real que ajuda a identificar e remover vírus, spyware e outro software malicioso, com alertas configuráveis ao conhecido software malicioso ou indesejável tenta instalar ou executar em máquinas virtuais protegidas.

### <a name="patch-management"></a>Gestão de correção

Máquinas virtuais do Windows implementadas por esta automatização Blueprint estão configuradas por predefinição para receber as atualizações automáticas do serviço de atualização do Windows. Esta solução também implementa a solução de automatização do Azure de OMS através do qual as implementações de atualização podem ser criadas para implementar patches nos servidores do Windows quando for necessário.

### <a name="operations-management"></a>Gestão de operações

#### <a name="log-analytics"></a>Log analytics

[Análise de registo](https://azure.microsoft.com/services/log-analytics/) é um serviço no Operations Management Suite (OMS) que permite a recolha e análise de dados gerados pelos recursos no Azure e ambientes no local.

#### <a name="oms-solutions"></a>Soluções do OMS

As seguintes soluções OMS previamente são instaladas como parte desta solução:
- [Avaliação do AD](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment)
- [Avaliação de Antimalware](https://docs.microsoft.com/azure/log-analytics/log-analytics-malware)
- [Automatização do Azure](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker)
- [Auditoria e segurança](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started)
- [Avaliação do SQL](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment)
- [Gestão de Atualizações](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management)
- [Agente de Funcionamento de Agente](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth)
- [Registos de atividade do Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity)
- [Monitorização de Alterações](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity)

## <a name="compliance-documentation"></a>Documentação de conformidade

### <a name="customer-responsibility-matrix"></a>Matriz de responsabilidade do cliente

O [matriz de responsabilidades do cliente](https://aka.ms/blueprinthighcrm) (livro do Excel) apresenta uma lista de todos os controlos de segurança necessários para a linha de base FedRAMP elevado. A matriz indica, para cada controlo (ou subpart de controlo), se a implementação de forma responsável para o controlo é da responsabilidade do Microsoft, o cliente, ou partilhados entre os dois. 

### <a name="control-implementation-matrix"></a>Matriz de implementação do controlo

O [matriz de implementação do controlo](https://aka.ms/blueprintwacim) (livro do Excel) apresenta uma lista de todos os controlos de segurança necessários para a linha de base FedRAMP elevado. Indica que a matriz, para cada controlo (ou controlo subpart) que é designado uma cliente-forma responsável na matriz de responsabilidades do cliente, 1) se a automatização de Blueprint implementa o controlo e 2) se uma descrição de como a implementação está alinhada com o requirement(s) de controlo. Este conteúdo está também disponível [aqui](fedramp-controls-overview.md).

## <a name="deploy-the-solution"></a>Implementar a solução

Esta solução Blueprint do Azure é composta por ficheiros de configuração JSON e scripts do PowerShell que são processados pelo serviço de API do Azure Resource Manager para implementar recursos no Azure. Estão disponíveis instruções de implementação detalhados [aqui](https://aka.ms/fedrampblueprintrepo). ***Nota: Esta solução implementa Azure Government.***

#### <a name="quickstart"></a>Início Rápido
1. Clonar ou transferir [isto](https://aka.ms/fedrampblueprintrepo) repositório do GitHub para a estação de trabalho local.

2. Execute o script do PowerShell de pré-implementação: azure-blueprint/predeploy/Orchestration_InitialSetup.ps1.

3. Clique no botão abaixo, inicie sessão no portal do Azure, introduza os parâmetros do modelo ARM necessários e clique em **Compra**.

    [![Implementar no Azure](http://azuredeploy.net/AzureGov.png)](https://portal.azure.us/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Ffedramp-iaas-webapp%2Fmaster%2Fazuredeploy.json)

## <a name="disclaimer"></a>Exclusão de Responsabilidade

- Este documento destina-se apenas a fins informativos. MICROSOFT FAZ COM QUE NÃO OFERECE NENHUMA GARANTIA, EXPRESSAS, IMPLÍCITA OU ESTATUTARIAMENTE AS INFORMAÇÕES NESTE DOCUMENTO. Este documento é fornecido "como-está." As informações e opiniões expressados neste documento, incluindo URLs e outras referências de Web site da Internet, podem ser alteradas sem aviso prévio. Os clientes ler este documento da sua responsabilidade utilizá-la.  
- Este documento não fornece aos clientes com quaisquer direitos legais a nenhuma propriedade intelectual de qualquer produto da Microsoft ou de soluções.  
- Os clientes podem copiar e utilizar este documento para efeitos de referência interno.  
- Algumas recomendações neste documento poderão resultar numa maior dados, a rede ou a utilização de recursos de computação no Azure e podem aumentar os custos de licenciamento ou de subscrição do Azure do cliente.  
- Esta arquitetura destina-se para servir como base para os clientes ajustar os seus requisitos específicos e não devem ser utilizada como-é num ambiente de produção.
- Este documento é desenvolvido como uma referência e não deve ser utilizado para definir todas as formas através do qual um cliente pode cumpre os requisitos de conformidade específico e regulamentos. Os clientes devem procurar suporte legal da respetiva organização em implementações de cliente aprovados.
