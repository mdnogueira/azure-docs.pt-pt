---
title: Azure Blueprint de processamento de pagamento - os requisitos da Firewall
description: PCI DSS requisito 1
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: b1935d88-acae-42f9-bc25-bb0766f876ab
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 995ecd5ef876695145fc6313aba2a46d2cc085cc
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="firewall-requirements-for-pci-dss-compliant-environments"></a>Requisitos da firewall para ambientes em conformidade de PCI DSS 
## <a name="pci-dss-requirement-1"></a>PCI DSS requisito 1

**Instalar e manter uma configuração de firewall para proteger os dados de cardholder**

> [!NOTE]
> Estes requisitos são definidos pelo [Council de normas de segurança da indústria de cartão de pagamento (PCI)](https://www.pcisecuritystandards.org/pci_security/) como parte do [PCI dados segurança Standard (DSS) versão 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Consulte o PCI DSS para obter informações sobre testes de procedimentos e as diretrizes para cada requisito.

As firewalls são dispositivos que controlam o tráfego de computador permitido entre redes de uma entidade (internos) e redes não fidedignas (externos), bem como o tráfego para dentro e fora áreas mais confidenciais dentro de uma entidade internas fidedignos redes. O ambiente de dados de cardholder é um exemplo de uma área confidencial mais dentro da rede fidedigna uma entidade.
Uma firewall analisa todo o tráfego de rede e bloqueia as transmissões de que não cumprem os critérios de segurança especificados.
Todos os sistemas tem de estar protegidos contra acesso não autorizado de redes não fidedignas, se introduzir o sistema através da Internet, como o comércio eletrónico, o acesso à Internet de empregado através de browsers de ambiente de trabalho, acesso de e-mail dos empregados, ligações dedicadas, tais como ligações de empresa-empresa, através de redes sem fios, ou através de outras origens. Muitas vezes, seemingly insignificant caminhos e de redes não fidedignas podem fornecer caminhos desprotegidos para sistemas de chaves. As firewalls são um mecanismo de proteção por chave para qualquer rede de computador.
Outros componentes do sistema poderão fornecer a funcionalidade de firewall, desde que cumprem os requisitos mínimos para firewalls, tal como definido no requisito 1. Em que os outros componentes do sistema são utilizados no ambiente de dados cardholder para fornecer a funcionalidade de firewall, estes dispositivos têm de ser incluídos no âmbito e avaliação de requisito 1.

## <a name="pci-dss-requirement-11"></a>Requisito de PCI DSS 1.1

**1.1** estabelecer e implementar firewall e router as normas de configuração que incluem o seguinte (consulte 1.1.1 através de 1.1.7).


**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore fornece firewalling do CDE utiliza o isolamento de PaaS e uma implementação de ambiente de serviço de aplicações garante que a entrada CDE e de saída de dados está protegido.<br /><br />Um [ambiente de serviço de aplicações (ASE)](/azure/app-service-web/app-service-app-service-environment-intro) é um plano de serviço Premium utilizado por motivos de conformidade. Para obter mais informações sobre controlos de ASE e a configuração, consulte [orientações de PCI - ambiente de serviço de aplicações](payment-processing-blueprint.md#app-service-environment).|



### <a name="pci-dss-requirement-111"></a>Requisito de PCI DSS 1.1.1

**1.1.1** um processo formal para aprovar e testar todas as ligações de rede e as alterações para as configurações de firewall e router


**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Uma instância de Contoso Webstore estabelece um modelo de CI/CD DevOps para se certificar de que todas as alterações são corretamente geridas. [Operations Management Suite (OMS)](/azure/operations-management-suite/) fornece registos um vasto conjunto de alterações. As alterações podem ser revistas e podem ser verificadas em termos de exatidão. Para obter instruções mais específicas, consulte [orientações de PCI - Operations Management Suite](payment-processing-blueprint.md#logging-and-auditing).<br /><br />[Centro de segurança do Azure](https://azure.microsoft.com/services/security-center/) fornece uma vista centralizada de estado de segurança dos seus recursos do Azure. De forma rápida, pode verificar que os controlos de segurança adequados são cumpridos e corretamente configurado e que possa identificar rapidamente quaisquer recursos que necessitam da atenção.|



### <a name="pci-dss-requirement-112"></a>Requisito de PCI DSS 1.1.2

**1.1.2** diagrama de rede atual, que identifica a todas as ligações entre o ambiente de dados cardholder e outras redes, incluindo quaisquer redes sem fios

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Consulte a Contoso Webstore arquitetura e estrutura documentação de referência fornecida como parte do padrão de instalação da solução.|



### <a name="pci-dss-requirement-113"></a>Requisito de PCI DSS 1.1.3

**1.1.3** atual diagrama que mostra todos os dados de cardholder flui entre sistemas e redes

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Consulte o Contoso Webstore DFD e o modelo de ameaça.|



### <a name="pci-dss-requirement-114"></a>Requisito de PCI DSS 1.1.4

**1.1.4** requisitos de uma firewall em cada ligação à Internet e entre qualquer zona desmilitarizada (DMZ) e a zona de rede interna

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure emprega dispositivos de proteção de limite, como gateways, ACLs de rede e firewalls de aplicação para comunicações de controlo em limites internos e externos ao nível da plataforma. O cliente, em seguida, configura estas seus requisitos e especificações. Microsoft Azure filtra comunicação quando entra de plataforma. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore fornece uma rede de Perímetro utilizando o isolamento de PaaS e uma implementação de ambiente de serviço de aplicações assegura que a entrada CDE e de saída de dados está protegido.<br /><br />Um [ambiente de serviço de aplicações (ASE)](/azure/app-service-web/app-service-app-service-environment-intro) é um plano de serviço Premium utilizado por motivos de conformidade. Para obter mais informações sobre controlos de ASE e a configuração, consulte [orientações de PCI - ambiente de serviço de aplicações](payment-processing-blueprint.md#app-service-environment).|



### <a name="pci-dss-requirement-115"></a>Requisito de PCI DSS 1.1.5

**1.1.5** descrição de grupos, funções e responsabilidades para a gestão de componentes de rede

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | O Contoso Webstore utiliza [controlo de acesso em funções do Azure (RBAC)](/azure/active-directory/role-based-access-control-configure) para isolar as funções de utilizador. RBAC permite precisamente direcionadas para gestão de acesso ao Azure. Existem configurações específicas para acesso de subscrição e o acesso do Cofre de chaves do Azure.|



### <a name="pci-dss-requirement-116"></a>Requisito de PCI DSS 1.1.6

**1.1.6** documentação do justificação de negócio e aprovação para utilização de todos os serviços, protocolos e portas permitidas, incluindo documentação das funcionalidades de segurança implementado para os protocolos considerados como sendo inseguras.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore abre-se apenas portas e protocolos necessários em toda a estrutura de ar. Detalhes sobre o fluxo de dados podem ser vistos no modelo de DFD e ameaças.|



### <a name="pci-dss-requirement-117"></a>Requisito de PCI DSS 1.1.7

**1.1.7** requisito para rever a regra de firewall e router define, pelo menos, a cada seis meses

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | No Contoso Webstore, os conjuntos de regras de firewall são revistos para garantir que nenhum desnecessárias ou não utilizadas regras estão incluídas. Por predefinição, a demonstração é implementada com um privilégio menores requisitos de espaço do caminho.|



## <a name="pci-dss-requirement-12"></a>Requisito de PCI DSS 1.2

**1.2** criar configurações de firewall e router que restringem as ligações entre redes não fidedignas e quaisquer componentes de sistema no ambiente de dados de cardholder. 

> [!NOTE]
> "Rede não fidedigna" é qualquer rede que é externo para as redes que pertencem à entidade de revisão e/ou que está fora do capacidade da entidade de controlo ou gerir.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | O Webstore de Contoso CDE está definido na documentação do ar e implementação. Por predefinição serão negados redes não fidedignas.|



### <a name="pci-dss-requirement-121"></a>Requisito de PCI DSS 1.2.1

**1.2.1** restringir o tráfego de entrada e saído para o qual é necessário para o ambiente de dados cardholder e negar especificamente todo o tráfego restante.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | O Webstore de Contoso CDE está definido na documentação do ar e implementação. Por predefinição serão negados redes não fidedignas. Demonstração de Contoso Webstore configura a firewall de aplicação do Microsoft Azure para permitir apenas especificados intervalos de endereços IP para aceder aos serviços do Microsoft Azure. O Contoso Webstore fornece uma firewall de negar-all em todos os limites CDE. Todas as configurações é efetuada durante a configuração inicial da implementação.

> [!NOTE]
> Ambiente de serviço de aplicações (ASE) é utilizada nesta solução para isolar o CDE no entanto, é essencial que o seu Assessor qualificado de segurança (QSA) avalia esta solução, como ASE implementa uma isolamento de rede de Perímetro que permita ligações de saída para ser efetuada pelo ASE. PCI-DSS requer que todas as ligações de entrada e saídas que não são necessárias têm de ser impedidas. Para ASE funcione corretamente, ASE irão estabelecer ligações de saída como necessário, tal como definido no ["Considerações de rede para um ambiente de serviço de aplicações"](/azure/app-service/app-service-environment/network-info). Os clientes devem avaliar as ligações de saída com o seu QSA antes de implementar a solução para um ambiente de produção para se certificar de que cumpra as necessidades. |



### <a name="pci-dss-requirement-122"></a>Requisito de PCI DSS 1.2.2

**1.2.2** seguro e sincronizar ficheiros de configuração de router.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore fornece configurações sincronizadas para controlos de rede nativo do Microsoft Azure.|



### <a name="pci-dss-requirement-123"></a>Requisito de PCI DSS 1.2.3

**1.2.3** instalar perímetro as firewalls entre todas as redes sem fios e o ambiente de dados cardholder e configurar estas firewalls para negar ou, se o tráfego é necessário para efeitos comerciais, permitir apenas autorizado o tráfego entre sem fios o ambiente de dados de cardholder e ambiente.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore não tem quaisquer capacidades ativadas ou soluções sem fios.|



## <a name="pci-dss-requirement-13"></a>Requisito de PCI DSS 1.3

**1.3** Proibir acesso público direto entre a Internet e de qualquer componente do sistema no ambiente de dados de cardholder.

**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure emprega dispositivos de proteção de limite baseada no anfitrião e baseados na rede, tais como firewalls, balanceadores de carga e ACLs. Estes dispositivos utilizam mecanismos, tais como o isolamento de VLAN, NAT e filtragem de tráfego do cliente distinta da Internet e o tráfego de gestão de pacotes. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore fornece, no momento da implementação, as configurações de firewall de aplicação do Azure para permitir apenas especificados intervalos de endereços IP para aceder ao site, que incluem o bastion VMs do Azure no respetivo CDE.|



### <a name="pci-dss-requirement-131"></a>Requisito de PCI DSS 1.3.1

**1.3.1** implementar uma rede de Perímetro para limitar o tráfego de entrada para apenas os componentes de sistema que fornecem serviços acessíveis publicamente autorizados, protocolos e portas.


**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A implementação de Contoso Webstore do seu DMZ garante que apenas os serviços autorizados podem ligar-se com o CDE.|



### <a name="pci-dss-requirement-132"></a>Requisito de PCI DSS 1.3.2

**1.3.2** tráfego de Internet a endereços IP na rede de Perímetro de entrada de limite.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A implementação de Contoso Webstore do seu DMZ garante que apenas os serviços autorizados podem ligar-se com o CDE.|



### <a name="pci-dss-requirement-133"></a>Requisito de PCI DSS 1.3.3

**1.3.3** medidas de spoofing de anti implementar para detetar e bloquear falsificado endereços IP de origem de entrar na rede. (Por exemplo, a bloquear tráfego com origem na Internet com um endereço de origem interno.)

**Responsabilidades:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure implementa rede filtragem para impedir a denuncia tráfego e restringir o tráfego de entrada e saído para componentes de plataforma fidedigna. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Não aplicável.|



### <a name="pci-dss-requirement-134"></a>Requisito de PCI DSS 1.3.4

**1.3.4** não permitir o tráfego de saída não autorizado do ambiente de dados de cardholder à Internet.


**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A arquitetura de Contoso Webstore impede que o tráfego de saída não autorizado de ambiente no âmbito à Internet. Isto é conseguido através da configuração de tráfego de saída ACL para portas aprovadas e protocolos no Microsoft Azure. Estes controlos de incluem o acesso à CDE na base de dados do SQL Server. <br /><br />Uma instância de base de dados de SQL de PaaS é utilizada para demonstramos medidas de segurança da base de dados. Para obter mais informações, consulte [orientações de PCI - SQL Database do Azure](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-135"></a>Requisito de PCI DSS 1.3.5

**1.3.5** permitir apenas "estabelecida" ligações à rede.


**Responsabilidades:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure implementa rede filtragem para impedir a denuncia tráfego e restringir o tráfego de entrada e saído para componentes de plataforma fidedigna. A rede do Microsoft Azure é segregada para separar o tráfego de cliente da gestão de tráfego. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | Não aplicável.|



### <a name="pci-dss-requirement-136"></a>Requisito de PCI DSS 1.3.6

**1.3.6** componentes do sistema local que armazenam dados de cardholder (por exemplo, uma base de dados) na zona de rede interna, separada do DMZ e outras redes não fidedignas.


**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure utiliza segregação de rede e NAT para separar o tráfego de cliente da gestão de tráfego. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A arquitetura de Contoso Webstore impede que o tráfego de saída não autorizado de ambiente no âmbito à Internet. Isto é conseguido através da configuração de tráfego de saída ACL para portas aprovadas e protocolos no Microsoft Azure. Estes controlos de incluem o acesso à CDE na base de dados do SQL Server. <br /><br />Uma instância de base de dados de SQL de PaaS é utilizada para demonstramos medidas de segurança da base de dados. Para obter mais informações, consulte [orientações de PCI - SQL Database do Azure](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-137"></a>Requisito de PCI DSS 1.3.7

**1.3.7** não divulgar de endereços IP privados e informações de encaminhamento para entidades não autorizadas. 

> [!NOTE]
> Métodos para ocultar o endereçamento IP podem incluir, mas não estão limitados a:
> - Tradução de endereços de rede (NAT).
> - Colocar os servidores que contém dados cardholder atrás de firewalls/servidores de proxy.
> - A remoção ou de filtragem de anúncios de rota para redes privadas que utilizem registado endereçamento.
> - Utilização interna RFC1918 do espaço de endereços em vez de endereços registados.


**Responsabilidades:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Microsoft Azure utiliza a tradução de endereços de rede (NAT) e a segregação de rede para separar o tráfego de cliente da gestão de tráfego. Dispositivos do Azure exclusivamente são identificados pelo respetivo UUID e são autenticados utilizando Kerberos. Azure geridos rede dispositivos são identificados pelo IP RFC 1918 resolvido. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore coloca todos os dados de cardholder atrás de firewalls/servidores de proxy e utiliza o espaço de endereços RFC1918 internamente.|



## <a name="pci-dss-requirement-14"></a>Requisito de PCI DSS 1.4

**1.4** instalar software, ou equivalente a funcionalidade de firewall pessoais em nenhum dispositivo de informático portátil (incluindo da empresa e/ou pertencentes aos empregados) que se ligar à Internet quando fora da rede (por exemplo, computadores portáteis utilizados pelos funcionários), e que também são utilizadas para aceder a CDE. Configurações de firewall (ou equivalente) incluem:-as definições de configuração específicas estão definidas.
- Firewall pessoal (ou funcionalidade equivalente) está ativamente em execução.
- Não é alterable por utilizadores dos dispositivos portáteis informáticos pessoal firewall (ou funcionalidade equivalente).

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore não fornece proteção de utilizador final dispositivos. [Microsoft Intune](https://www.microsoft.com/cloud-platform/microsoft-intune) pode ser utilizado para gerir os sua força de trabalho utiliza para aceder aos dados da empresa de dispositivos móveis.|



## <a name="pci-dss-requirement-15"></a>Requisito de PCI DSS 1.5

**1.5** Certifique-se de que as políticas de segurança e de procedimentos operacionais para a gestão de firewalls estão documentados em utilização e conhecidos para todas as partes afetadas.

**Responsabilidades:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Fornecedor<br />(Microsoft&nbsp;Azure)** | Não aplicável. |
| **Cliente<br />(PCI &#8209; DSS&nbsp;Blueprint)** | A Contoso Webstore fornece, no momento da implementação, as configurações de firewall de aplicação do Azure para permitir apenas especificados intervalos de endereços IP para aceder ao site, que incluem o bastion VMs do Azure no respetivo CDE.|




