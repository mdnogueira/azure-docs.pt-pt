---
title: Azure Blueprint de processamento de pagamento - requisitos de palavra-passe
description: Requisito de PCI DSS 2
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 0df24870-6156-4415-a608-dd385b6ae807
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 4ae9fc7d5b53d33f9feb98c450970e0560afa2af
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="password-requirements-for-pci-dss-compliant-environments"></a>Requisitos de palavra-passe para ambientes em conformidade de PCI DSS 
## <a name="pci-dss-requirement-2"></a>Requisito de PCI DSS 2

**Não utilize as predefinições fornecida pelo fabricante para palavras-passe do sistema e outros parâmetros de segurança**

> [!NOTE]
> Estes requisitos são definidos pelo [Council de normas de segurança da indústria de cartão de pagamento (PCI)](https://www.pcisecuritystandards.org/pci_security/) como parte do [PCI dados segurança Standard (DSS) versão 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Consulte o PCI DSS para obter informações sobre testes de procedimentos e as diretrizes para cada requisito.

Indivíduos maliciosos (externos e internos para uma entidade) utilizam frequentemente palavras-passe do fornecedor predefinida e outras definições do fornecedor predefinido para comprometer sistemas. Estas palavras-passe e as definições são bem conhecidas por Comunidades hacker e facilmente são determinadas através de informações públicas.

## <a name="pci-dss-requirement-21"></a>Requisito de PCI DSS 2.1
 
**2.1** sempre alterar as predefinições de-fornecida pelo fabricante e remover nem desativar contas predefinidas desnecessários **antes** instalar um sistema na rede.
Isto aplica-se a todas as palavras-passe predefinidas, incluindo, mas não se limitando a utilizadas por sistemas operativos, software, que fornece serviços de segurança, aplicações e do sistema de contas, terminals do ponto de venda (POS), de Comunidade Simple Network Management Protocol (SNMP) as cadeias, etc.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Requisitos de política de palavras-passe do Microsoft Azure Active Directory são impostos para novas palavras-passe fornecidas pelos clientes no AADUX portal. Alterações de iniciada pelo cliente passe self-service necessitam de validação de palavra-passe anterior. As palavras-passe de reposição de administrador são necessárias para ser alterada após o início de sessão subsequente. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore requer que os utilizadores utilizem o conjunto de palavras-passe segura para todos os utilizadores. Não existem contas de exemplo ou convidadas são ativadas da demonstração de.<br /><br />Redes sem fios e SNMP não são implementados na solução.|



### <a name="pci-dss-requirement-211"></a>Requisito de PCI DSS 2.1.1

**2.1.1** para ambientes sem fios ligados ao ambiente de cardholder de dados ou dados de cardholder transmissão, altere todas as predefinições do fabricante sem fios durante a instalação, incluindo mas não limitado a chaves de encriptação sem fios predefinidas, palavras-passe, e Cadeias de Comunidade SNMP.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Redes sem fios e SNMP não são implementados na solução.|



## <a name="pci-dss-requirement-22"></a>Requisito de PCI DSS 2.2

**2.2** desenvolver as normas de configuração de todos os componentes de sistema. Garantir que estes padrões todas as vulnerabilidades de segurança conhecidos de endereços e são consistentes com as normas de proteção do sistema foi aceite da indústria.
Origens de padrões de proteção do sistema foi aceite da indústria podem incluir, mas não estão limitadas a:
- Centro de segurança de Internet (IC)
- Organização internacional para uniformização (ISO)
- Institute de segurança (SANS) de rede de auditoria de administrador do sistema
- National Institute of Standards Technology (NIST)

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Para o Microsoft Azure, a equipa de serviços de segurança técnica OSSC desenvolvidas pela organização as normas de configuração de segurança para sistemas no ambiente do Microsoft Azure que são consistentes com aceites da indústria normas de proteção. Estas configurações estão documentadas em linhas de base do sistema e as alterações de configuração relevantes são comunicadas ao equipas afetadas (por exemplo, a equipa de IPAK). Os procedimentos são implementados para monitorizar a conformidade com as normas de configuração de segurança. As normas de configuração de segurança para sistemas no ambiente do Microsoft Azure são consistentes com as normas de proteção foi aceite da indústria e são revistas, pelo menos, anual. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore fornece proteção de todos os serviços no âmbito de ambiente de dados cardholder (CDE). <br /><br />A Contoso Webstore também implementa o [Centro de segurança do Azure](https://azure.microsoft.com/services/security-center/), que fornece uma vista de estado de segurança dos seus recursos do Azure centralizada. De forma rápida, pode verificar que os controlos de segurança adequados são cumpridos e corretamente configurado e que possa identificar rapidamente quaisquer recursos que necessitam da atenção.<br /><br />O Contoso Webstore utiliza o Operations Management Suite para registar todas as alterações de sistema. [Operations Management Suite (OMS)](/azure/operations-management-suite/) fornece registos um vasto conjunto de alterações. As alterações podem ser revistas e podem ser verificadas em termos de exatidão. Para obter instruções mais específicas, consulte [orientações de PCI - Operations Management Suite](payment-processing-blueprint.md#logging-and-auditing).|



### <a name="pci-dss-requirement-221"></a>Requisito de PCI DSS 2.2.1

**2.2.1** implementar apenas uma função primária por servidor impedir as funções que requerem segurança diferentes níveis de conjuntamente existentes no mesmo servidor. (Por exemplo, DNS, servidores de base de dados e servidores web devem ser implementados em servidores separados.) 

> [!NOTE]
> Em que as tecnologias de Virtualização estão em utilização, implemente apenas uma função primária por componente de sistema virtual.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Serviços de contoso Webstore estão implementados como serviços PaaS. Todos os serviços estão isolados e segmentados a utilização da segmentação de rede.<br /><br />A Contoso Webstore também utiliza um [ambiente de serviço de aplicações (ASE)](/azure/app-service-web/app-service-app-service-environment-intro) impor práticas chaves. Para obter mais informações, consulte [orientações de PCI - ambiente de serviço de aplicações](payment-processing-blueprint.md#app-service-environment).|



### <a name="pci-dss-requirement-222"></a>Requisito de PCI DSS 2.2.2

**2.2.2** ativar serviços só é necessários, protocolos, daemons, etc., conforme necessário para a função do sistema.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Configurações de hardware e software do Microsoft Azure são revistas trimestralmente, pelo menos, para identificar e eliminar quaisquer funções desnecessárias, portas, protocolos e serviços. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Serviços de contoso Webstore estão implementados como serviços PaaS. Todos os serviços estão isolados e segmentados a utilização da segmentação de rede.<br /><br />A Contoso Webstore também utiliza um [ambiente de serviço de aplicações (ASE)](/azure/app-service-web/app-service-app-service-environment-intro) impor práticas chaves. Para obter mais informações, consulte [orientações de PCI - ambiente de serviço de aplicações](payment-processing-blueprint.md#app-service-environment).|



### <a name="pci-dss-requirement-223"></a>Requisito de PCI DSS 2.2.3

**2.2.3** implementar funcionalidades adicionais de segurança para os serviços necessários, protocolos ou daemons que são consideradas como sendo inseguras. 

> [!NOTE]
> Onde é utilizado o SSL antecipado TLS, os requisitos no anexo A2 têm de ser concluídos.


**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Serviços de contoso Webstore estão implementados como serviços PaaS. Todos os serviços estão isolados e segmentados a utilização da segmentação de rede. A implementação também fornece proteção de todos os serviços no âmbito do CDE. <br /><br />A Contoso Webstore também implementa o [Centro de segurança do Azure](https://azure.microsoft.com/services/security-center/), que fornece uma vista de estado de segurança dos seus recursos do Azure centralizada. De forma rápida, pode verificar que os controlos de segurança adequados são cumpridos e corretamente configurado e que possa identificar rapidamente quaisquer recursos que necessitam da atenção.<br /><br />A Contoso Webstore também utiliza um [ambiente de serviço de aplicações (ASE)](/azure/app-service-web/app-service-app-service-environment-intro) impor práticas chaves. Para obter mais informações, consulte [orientações de PCI - ambiente de serviço de aplicações](payment-processing-blueprint.md#app-service-environment).|



### <a name="pci-dss-requirement-224"></a>Requisito de PCI DSS 2.2.4

**2.2.4** configurar parâmetros de segurança do sistema para impedir a utilização indevida.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Azure garante que apenas a empregados autorizados são capazes de configurar os controlos de segurança de plataforma do Azure, utilizando os controlos de acesso multifator e a necessidade de negócio documentado. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | O Contoso Webstore utiliza AAD e AD RBAC para gerir os parâmetros de segurança são implementadas corretamente. Para obter mais informações, consulte [orientações de PCI - gestão de identidades](payment-processing-blueprint.md#identity-management).|



### <a name="pci-dss-requirement-225"></a>Requisito de PCI DSS 2.2.5

**2.2.5** remover todas as funcionalidades desnecessárias, por exemplo, scripts, controladores, funcionalidades, subsistemas, sistemas de ficheiros e servidores web desnecessária.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore fornece documentação na forma como os limites são estabelecidos. Modelo de ameaça da Contoso e diagrama de fluxo de dados mostram todos os serviços utilizados e controlos ativados.|



## <a name="pci-dss-requirement-23"></a>Requisito de PCI DSS 2.3

**2.3** encriptar todo não consola o acesso administrativo a utilização de criptografia segura. 

> [!NOTE]
> Onde é utilizado o SSL antecipado TLS, os requisitos no anexo A2 têm de ser concluídos.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure garante que a utilização de criptografia segura é imposta quando aceder a infraestrutura de hipervisor. Microsoft Azure também garante que os clientes que utilizam o Microsoft Azure Management Portal é capazes de aceder aos respetivos consolas de serviço/IaaS com criptografia segura. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore mostra como strong palavras-passe podem ser implementadas numa solução; Além disso, todos os testes podem ser efetuados para verificar que se a encriptação é implementada em toda a solução.<br /><br />A Contoso Webstore também utiliza um [ambiente de serviço de aplicações (ASE)](/azure/app-service-web/app-service-app-service-environment-intro) impor práticas chaves. Para obter mais informações, consulte [orientações de PCI - ambiente de serviço de aplicações](payment-processing-blueprint.md#app-service-environment).|



## <a name="pci-dss-requirement-24"></a>Requisito de PCI DSS 2.4

**2.4** manter um inventário dos componentes do sistema que se encontrem no âmbito de PCI DSS.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Inventário de solução de Contoso Webstore demonstração PaaS pode ser revisto na documentação do fornecido. Para obter mais informações, consulte [orientações de PCI - Pre-Installed OMS soluções](payment-processing-blueprint.md#oms-solutions).|



## <a name="pci-dss-requirement-25"></a>Requisito de PCI DSS 2,5

**2.5** Certifique-se de que as políticas de segurança e de procedimentos operacionais para gerir as predefinições do fabricante e outros parâmetros de segurança estão documentados em utilização e conhecidos para todas as partes afetadas.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore fornece documentação fornece informações sobre os parâmetros de segurança e documentos elementos de serviço. |



## <a name="pci-dss-requirement-26"></a>Requisito de PCI DSS 2.6

**2.6** fornecedores de alojamento partilhados tem de proteger dados de ambiente e cardholder alojados no cada entidade. Estes fornecedores têm de cumprir requisitos específicos, conforme detalhado em *apêndice a: PCI DSS requisitos adicionais para fornecedores de alojamento partilhados.*

**Responsabilidades:&nbsp;&nbsp;`Not Applicable`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. Microsoft Azure não é um fornecedor de alojamento partilhado. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Não aplicável. Microsoft Azure não é um fornecedor de alojamento partilhado.|




