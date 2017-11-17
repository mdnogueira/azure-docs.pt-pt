---
title: "Automatização do Azure Blueprint - aplicações Web de três camadas para oficial do RU"
description: "Automatização do Azure Blueprint & - aplicações Web de três camadas para oficial do RU"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 9c32e836-0564-4906-9e15-f070d2707e63
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 5f5694367d9be2ae66c7303cfea063b7f4979307
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="azure-blueprint-automation-three-tier-web-applications-for-uk-official"></a>Automatização do Azure Blueprint: Aplicações da Web de três camadas para oficial do RU

## <a name="overview"></a>Descrição geral

 Este artigo fornece scripts de automatização e documentação de orientação para fornecer uma arquitetura de baseado na web de três camadas do Microsoft Azure, adequada para muitas cargas de trabalho classificadas como oficial no Reino Unido de processamento.

 Utilizando uma infraestrutura como código abordam o conjunto de [do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) modelos (ARM) implementar um ambiente Alinhar para o RU National informático segurança Centre (NCSC) 14 [princípios de segurança de nuvem](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles)e o Centro de segurança de Internet (IC) [controlos de segurança críticas](https://www.cisecurity.org/critical-controls.cfm).

 O NCSC Recomendamos os princípios de segurança de nuvem utilizadas pelos clientes para avaliar as propriedades de segurança do serviço e para ajudar a compreender a divisão de responsabilidade entre o cliente e o fornecedor. Fornecemos informações em cada um destes princípios para ajudar a compreender a divisão das responsabilidades.

 Esta arquitetura e os modelos ARM correspondentes são suportados pelo documento técnico da Microsoft, [Blueprint do Azure para o Government RU](https://gallery.technet.microsoft.com/14-Cloud-Security-Controls-670292c1). Este documento catalogues serviços como do Azure alinhar com RU NCSC 14 princípios da nuvem segurança, ativando assim organisations a fast-controlar a sua capacidade para cumprir as obrigações de conformidade a utilizar serviços baseados na nuvem global e o RU no Microsoft Azure nuvem.

 Este modelo implementa a infraestrutura para a carga de trabalho. Código da aplicação e o suporte de camada de negócio e o software de camada de dados tem de ser instalados e configurados. Estão disponíveis instruções de implementação detalhados [aqui](https://aka.ms/ukwebappblueprintrepo).

 Se não tiver uma subscrição do Azure, em seguida, pode inscrever-se rápida e facilmente - [introdução ao Azure](https://azure.microsoft.com/get-started/).

## <a name="architecture-diagram-and-components"></a>Diagrama da arquitetura e componentes

 Os modelos do Azure fornecem uma arquitetura de aplicação web de três camadas num ambiente em nuvem do Azure que suporta RU oficial cargas de trabalho. A arquitetura fornece um ambiente de híbrido segura que expande uma rede no local para Azure que permite baseado na web cargas de trabalho para poderem ser acedidos em segurança por utilizadores empresariais ou da internet.

![texto alternativo](images/diagram.png?raw=true "arquitetura de camada de três RU oficial de Azure")

 Esta solução utiliza os seguintes serviços do Azure. Detalhes da arquitetura de implementação estão localizados no [arquitetura de implementação](#deployment-architecture) secção.

Rede virtual do /16 (1) - VNet operacional
- (3) /24 sub-redes - 3 camadas (Web, Biz, dados)
- (1) /27 sub-rede - adiciona
- (1) /27 sub-rede - sub-rede do Gateway
- (1) /29 sub-rede - sub-rede do Gateway de aplicação
- Utiliza predefinido DNS (fornecidos pelo Azure)
- Peering ativado para a gestão de VNet
- Grupo de segurança de rede (NSG) para gerir o fluxo de tráfego

Rede virtual do /24 (1) - gestão VNet
- sub-rede de /27 (1)
- Utiliza DNS adiciona (2) e entradas de DNS do Azure (1)
- Peering ativado para a VNet operacional
- Grupo de segurança de rede (NSG) para gerir o fluxo de tráfego

(1) Gateway de aplicação
- WAF - ativado
- Modo de WAF - prevenção
- Conjunto de regras: OWASP 3.0
- Serviço de escuta de HTTP na porta 80
- Conectividade/tráfego regulado através do NSG
- Ponto final de endereço IP público definido (Azure)

(1) VPN - baseadas na rota túnel VPN IPSec de 2 local
- Ponto final de endereço IP público definido (Azure)
- Conectividade/tráfego regulado através do NSG
- (1) gateway de rede local (ponto final no local)
- (1) gateway de rede do azure (ponto final do Azure)

(9) máquinas virtuais - todas as VMs implementadas com as definições de DSC de Antimalware do IaaS do Azure

- (2) o active Directory Domain Services controladores de domínio (Windows Server 2012 R2)
  - (2) funções de servidor DNS - 1 por VM
  - (2) NICs à VNet operacional - 1 por VM
  - Ambos estão associados a um domínio para o domínio definido no modelo
    - Domínio criado como parte da implementação


- (1) Jumpbox (anfitrião de Bastion) gestão VM
  - 1 NIC a VNet de gestão com o endereço IP público
    - NSG é utilizado para limitar o tráfego (na/saída) para fontes específicas
  - Não associados a um domínio


- (2) VMs de camada web
  - (2) funções de servidor IIS - 1 por VM
  - (2) NICs à VNet operacional - 1 por VM
  - Não associados a um domínio


- (2) VMs de camada de biz
  - (2) NICs à VNet operacional - 1 por VM
  - Não associados a um domínio


- (2) VMs de camada de dados
  - (2) NICs à VNet operacional - 1 por VM
  - Não associados a um domínio

Conjuntos de Disponibilidade
- (1) definir o controlador de domínio Active Directory do VM - 2 VMs
- (1) definir a camada web VM - 2 VMs
- (1) definir a camada de biz VM - 2 VMs
- (1) definir a camada de dados VM - 2 VMs

Balanceador de Carga
- (1) Balanceador de carga de camada web
- (1) Balanceador de carga de camada de biz
- (1) Balanceador de carga de camada de dados

Armazenamento
- (14) contas de armazenamento total
  - Conjunto de disponibilidade de controlador de domínio do Active Directory
    - (2) contas de armazenamento localmente redundante (LRS) primários - 1 para cada VM  
    - (1) diagnóstico conta de armazenamento localmente redundante (LRS) para adiciona o conjunto de disponibilidade
  - Gestão Jumpbox VM
    - (1) primário armazenamento localmente redundante (LRS) de conta para a VM Jumpbox
    - (1) diagnóstico conta de armazenamento localmente redundante (LRS) para a VM Jumpbox
  - VMs de camada Web
    - (2) contas de armazenamento localmente redundante (LRS) primários - 1 para cada VM  
    - (1) diagnóstico conta de armazenamento localmente redundante (LRS) para o conjunto de disponibilidade de camada na Web
  - VMs de camada de Biz
    - (2) contas de armazenamento localmente redundante (LRS) primários - 1 para cada VM  
    - (1) diagnóstico conta de armazenamento localmente redundante (LRS) para o conjunto de disponibilidade de Biz camada
  - VMs de camada de dados
    - (2) contas de armazenamento localmente redundante (LRS) primários - 1 para cada VM  
    - (1) diagnóstico conta de armazenamento localmente redundante (LRS) para o conjunto de disponibilidade de dados de camada

### <a name="deployment-architecture"></a>Arquitetura de implementação:

**Rede no local**: uma rede de área local privada implementada um informático.

**Produção VNet**: A produção [VNet](https://docs.microsoft.com/azure/Virtual-Network/virtual-networks-overview) (Virtual Network) que aloja a aplicação e outros recursos operacionais em execução no Azure. Cada VNet pode conter várias sub-redes que são utilizadas para isolar e gerir o tráfego de rede.

**Web camada**: processa pedidos HTTP recebidos. As respostas são devolvidas através desta camada.

**Camada de negócios**: implementa processos empresariais e outra lógica funcional para o sistema.

**Camada de base de dados**: fornece armazenamento de dados persistente, utilizando [SQL Server em grupos de disponibilidade Always](https://msdn.microsoft.com/library/hh510230.aspx) para elevada disponibilidade. Os clientes podem utilizar [SQL Database do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview) como uma alternativa PaaS.

**Gateway**: O [Gateway de VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) fornece conectividade entre os routers na rede no local e a VNet de produção.

**Gateway de Internet e o endereço IP público**: O gateway de internet expõe os serviços de aplicações para utilizadores através da internet. Tráfego de aceder a estes serviços está protegido por um [Gateway de aplicação](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) oferta capacidades com proteção de firewall (WAF) de aplicação web de balanceamento de carga e de encaminhamento de 7 camadas.

**Gestão VNet**: Isto [VNet](https://docs.microsoft.com/azure/Virtual-Network/virtual-networks-overviewcontains) contém recursos que implementam a gestão e monitorização capacidades para as cargas de trabalho em execução na mesma VNet.

**Jumpbox**: também chamado um [anfitrião bastion](https://en.wikipedia.org/wiki/Bastion_host), que é uma VM segura na rede que os administradores utilizam para ligar a VMs na mesma VNet. O jumpbox tem um NSG que permita o tráfego remoto apenas a partir de endereços IP públicos numa lista segura. Para permitir o tráfego de (RDP) de ambiente de trabalho remoto, a origem do tráfego tem de ser definido no NSG. Gestão de recursos de produção é através de RDP com uma VM Jumpbox protegidos.

**Rotas definidas pelo utilizador**: [rotas definidas pelo utilizador](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) são utilizadas para definir o fluxo de tráfego de IP as VNets do Azure.

**As VNETs em modo de Peering de rede**: A produção e gestão VNets estiverem ligadas através de [o VNet peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).
Nestas VNets ainda são geridas como recursos separados, mas aparece como uma para todos os fins de conectividade para estas máquinas virtuais. Estas redes comunicam entre si diretamente através da utilização de endereços IP privados. O VNet peering encontra-se sujeita as VNets que está a ser na mesma região do Azure.

**Grupos de segurança de rede**: [NSGs](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) contêm listas de controlo de acesso que permitem ou negam o tráfego numa VNet. Os NSGs podem ser utilizados para proteger o tráfego de uma sub-rede ou nível VM individuais.

**Serviços de domínio do Active Directory (AD DS)**: esta arquitetura fornece um dedicado [serviços de domínio do Active Directory](https://technet.microsoft.com/library/hh831484.aspx) implementação.

**Registo e auditoria**: [registo de atividade do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) captura operações executadas nos recursos na sua subscrição, tais como quem iniciou a operação quando ocorreu a operação, o estado da operação e os valores das outras propriedades que podem ajudar a pesquisar a operação. Registo de atividade do Azure é um serviço de plataforma Azure que captura todas as ações de uma subscrição. Os registos podem ser arquivados ou exportados se necessário.

**Monitorização e alertar rede**: [observador de rede de Azure](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) é um serviço de plataforma fornece captura de pacotes de rede, o registo de fluxo, as ferramentas de topologia e diagnóstico para traffics de rede nas suas VNets.

## <a name="guidance-and-recommendations"></a>Orientações e recomendações

### <a name="business-continuity"></a>Continuidade do Negócio

**Elevada disponibilidade**: cargas de trabalho do servidor são agrupadas num [do conjunto de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para ajudar a garantir a elevada disponibilidade das máquinas virtuais no Azure. Esta configuração ajuda a garantir que durante um evento de manutenção planeado ou não planeado, pelo menos, uma máquina virtual irá estar disponível e cumprem 99,95% SLA do Azure.

### <a name="logging-and-audit"></a>Auditoria e registo

**Monitorização**: [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started) é o serviço de plataforma que fornece uma única origem para monitorização do registo de atividade, métricas e registos de diagnóstico dos seus recursos Azure. Monitor do Azure pode ser configurado para visualizar, consultar, encaminhar, arquivar e atuar sobre as métricas e registos proveniente de recursos no Azure. Recomenda-se que o controlo de acesso baseado em recursos é utilizado para proteger o registo de auditoria para ajudar a garantir que os utilizadores não têm a capacidade de modificar os registos.

**Registos de atividade**: configurar [registos de atividade do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) para obter informações sobre as operações que foram executadas no recursos na sua subscrição.

**Os registos de diagnóstico**: [os registos de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) são emitidos por um recurso de todos os registos. Estes registos podem incluir registos de sistema de eventos do Windows, blob, tabela e fila registos.

**Os registos de firewall**: Gateway de aplicação fornece registos de diagnóstico e de acesso completos. Os registos de firewall estão disponíveis para recursos de gateway de aplicações que tenham a WAF ativada.

**Inicie sessão arquivo**: conta de armazenamento de dados pode ser configurado para escrever um armazenamento do Azure centralised de registo para arquivo e um período de retenção definido. Os registos podem ser processados através de Log Analytics do Azure ou por sistemas SIEM de terceiros.

### <a name="identity"></a>Identidade

**Serviços de domínio do Active Directory**: esta arquitetura fornece uma implementação de serviços de domínio do Active Directory no Azure. Para ver as recomendações específicas na implementação do Active Directory no Azure, consulte os artigos seguintes:

[Expandir os serviços de domínio do Active Directory (AD DS) para o Azure](https://docs.microsoft.com/azure/guidance/guidance-identity-adds-extend-domain).

[Diretrizes para a implementação do Windows Server Active Directory em máquinas virtuais do Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx).

**Integração do Active Directory**: como alternativa a uma arquitetura de AD DS dedicada, os clientes podem pretender utilizar [do Azure Active Directory](https://docs.microsoft.com/azure/guidance/guidance-ra-identity#using-azure-active-directory) integração ou [do Active Directory no Azure associado a um local floresta](https://docs.microsoft.com/azure/guidance/guidance-ra-identity#using-active-directory-in-azure-joined-to-an-on-premises-forest).

### <a name="security"></a>Segurança

**Segurança de gestão**: este Blueprint do Azure permite aos administradores estabelecer a ligação para a gestão de VNet e Jumpbox através de RDP de uma origem fidedigna. Tráfego de rede para a gestão de VNet é controlado utilizando NSGs. O acesso à porta 3389 é restringido ao tráfego de um intervalo IP fidedigno que pode aceder a sub-rede que contém o Jumpbox.

Os clientes também podem considerar utilizar um [modelo administrativo de segurança avançada](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/securing-privileged-access) para proteger o ambiente ao estabelecer ligação com a gestão de VNet e Jumpbox. Sugestões de que para a segurança avançada aos clientes utilizam uma [estação de trabalho de acesso privilegiado](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/privileged-access-workstations#what-is-a-privileged-access-workstation-paw) e a configuração do gateway de RD. A utilização de aparelhos de rede virtual e públicas/privadas DMZ oferecem ainda mais os melhoramentos de segurança.

**Proteger a rede**: [grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (NSGs) são recomendados para cada sub-rede fornecer um segundo nível de proteção contra o tráfego de entrada ignorando um gateway desativado ou incorretamente configurado. Exemplo - [modelo ARM para implementar um NSG](https://github.com/mspnp/template-building-blocks/tree/v1.0.0/templates/buildingBlocks/networkSecurityGroups).

**Proteger pontos finais públicos**: O gateway de internet expõe os serviços de aplicações para utilizadores através da internet. Tráfego de aceder a estes serviços está protegido por um [Gateway de aplicação](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction), que fornece uma gestão de protocolo de Firewall de aplicações Web e o HTTPS.

**Intervalos de IP**: intervalos de IP na arquitetura de são intervalos sugeridos. Os clientes aconselhados considere os seus próprios ambiente e utilizar os intervalos adequados.

**Conectividade híbrida**: as cargas de trabalho baseado na nuvem estão ligadas à datacentre no local através de IPSEC de VPN com o Gateway de VPN do Azure. Os clientes devem certificar-se de que estão a utilizar um Gateway de VPN adequado para ligar ao Azure. Exemplo - [modelo ARM do VPN Gateway](https://github.com/mspnp/template-building-blocks/tree/v1.0.0/templates/buildingBlocks/vpn-gateway-vpn-connection). Os clientes em execução em grande escala, as cargas de trabalho crítico de missão requisitos de macrodados podem pretender considerar a uma utilização de arquitetura de rede do híbrida [ExpressRoute](https://docs.microsoft.com/azure/guidance/guidance-hybrid-network-expressroute) para conectividade de rede privada para a Microsoft serviços em nuvem.

**Separação das preocupações**: esta arquitetura de referência separa as VNets para operações de gestão e operações de negócio. Separado VNets e sub-redes permitem a gestão de tráfego, incluindo restrições de entrada e de saída de tráfego, utilizando NSGs entre segmentos de rede seguir [segurança de rede e serviços da nuvem Microsoft](https://docs.microsoft.com/azure/best-practices-network-security) melhores práticas.

**Gestão de recursos**: recursos do Azure como VMs, VNets e Balanceadores de carga são geridos pelo agrupá-los em conjunto para [grupos de recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groupsresource). Em seguida, podem ser atribuídas a funções de controlo de acesso baseado em recursos para cada grupo de recursos para restringir o acesso apenas aos utilizadores autorizados.

**Limitações de controlo de acesso**: Utilize [controlo de acesso baseado em funções](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) (RBAC) para gerir os recursos na sua aplicação utilizar [funções personalizadas](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles) RBAC pode ser utilizada para restringir as operações que DevOps pode executar em cada camada. Quando a concessão de permissões, utilize o [princípio do menor privilégio](https://msdn.microsoft.com/library/hdb58b2f(v=vs.110).aspx#Anchor_1). Inicie sessão todas as operações administrativas e realizar auditorias regulares para garantir que as alterações de configuração foram planeadas.

**Acesso à Internet**: esta arquitetura de referência utilises [Gateway de aplicação do Azure](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) como Balanceador de gateway e de carga com acesso à internet. Alguns clientes também podem considerar utilizar aplicações virtuais de rede de terceiros para camadas adicionais de segurança como alternativa de rede a [Gateway de aplicação do Azure](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction).

**Centro de segurança do Azure**: O [Centro de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) fornece uma vista central do Estado de segurança de recursos na subscrição e fornece recomendações que ajudam a impedir recursos comprometidos. Também pode ser utilizado para ativar políticas mais granulares. Por exemplo, as políticas podem ser aplicadas a grupos de recurso específico, que permite que a empresa personalizar a sua postura de risco. Recomenda-se que os clientes ativar o Centro de segurança do Azure na sua subscrição do Azure.

## <a name="ncsc-cloud-security-principles-compliance-documentation"></a>Documentação de conformidade de princípios de segurança de nuvem NCSC

O serviço de comerciais Crown (uma agência que funciona para melhorar a atividade comercial e aprovisionamento pelo government) renovar a classificação de no âmbito enterprise cloud services da Microsoft para v6 G a nuvem, que abrangem todas as respetivas ofertas ao nível do oficial. Podem ser encontrados detalhes do Azure e nuvem G no [resumo da avaliação de segurança do Azure RU G-Cloud](https://www.microsoft.com/en-us/trustcenter/compliance/uk-g-cloud).

Alinhar a esta solução de Blueprint RU oficial do Azure para os princípios de segurança de 14 nuvem que estão documentados no NCSC [princípios de segurança de nuvem](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) para ajudar a garantir um ambiente que suporte cargas de trabalho classificadas como RU oficial.

O [cliente responsabilidade matriz](https://aka.ms/blueprintuk-gcrm) (livro do Excel) apresenta uma lista de todos os princípios de segurança de nuvem 14 e, para cada princípio (ou subpart princípio), a matriz indica se a implementação de princípio é da responsabilidade do Microsoft, o cliente, ou partilhados entre os dois.

O [princípio implementação matriz](https://aka.ms/ukwebappblueprintpim) listas (livro do Excel) todos os princípios de segurança de nuvem 14 e a matriz indica, para cada princípio (ou subpart princípio) que é designado uma responsabilidade do cliente no cliente Responsabilidades da matriz, 1) se a automatização do Azure Blueprint implementa o princípio e 2) a descrição de como a implementação está alinhada com a requirement(s) princípio. Este conteúdo está também disponível [aqui](https://github.com/Azure/uk-official-three-tier-webapp/blob/master/principles-overview.md).

Além disso, o Alliance de segurança de nuvem (CSA) publicado a matriz de controlo da nuvem para suportar clientes na avaliação de fornecedores de nuvem e para identificar as questões que devem ser respondidas antes de mover a serviços em nuvem. Em resposta, o Microsoft Azure respondidas o CSA consenso avaliação iniciativa questionário ([CSA CAIQ](https://www.microsoft.com/en-us/TrustCenter/Compliance/CSA)), que descreve como Microsoft endereços estes princípios sugeridos.

## <a name="deploy-the-solution"></a>Implementar a solução

Existem dois métodos que os utilizadores de implementação podem utilizar para implementar esta solução Blueprint do Azure. O primeiro método utiliza um script do PowerShell, enquanto o segundo método utilises Portal do Azure para implementar a arquitetura de referência. Estão disponíveis instruções de implementação detalhados [aqui](https://aka.ms/ukwebappblueprintrepo).

## <a name="disclaimer"></a>Exclusão de Responsabilidade

 - Este documento destina-se apenas a fins informativos. MICROSOFT FAZ COM QUE NÃO OFERECE NENHUMA GARANTIA, EXPRESSAS, IMPLÍCITA OU ESTATUTARIAMENTE AS INFORMAÇÕES NESTE DOCUMENTO. Este documento é fornecido "como-está." As informações e opiniões expressados neste documento, incluindo URLs e outras referências de Web site da Internet, podem ser alteradas sem aviso prévio. Os clientes ler este documento da sua responsabilidade utilizá-la.
 - Este documento não fornece aos clientes com quaisquer direitos legais a nenhuma propriedade intelectual de qualquer produto da Microsoft ou de soluções.
 - Os clientes podem copiar e utilizar este documento para efeitos de referência interno.
 - Algumas recomendações neste documento poderão resultar numa maior dados, a rede ou a utilização de recursos de computação no Azure e podem aumentar os custos de licenciamento ou de subscrição do Azure do cliente.
 - Esta arquitetura destina-se para servir como base para os clientes ajustar os seus requisitos específicos e não devem ser utilizada como-é num ambiente de produção.
 - Este documento é desenvolvido como uma referência e não deve ser utilizado para definir todas as formas através do qual um cliente pode cumpre os requisitos de conformidade específico e regulamentos. Os clientes devem procurar suporte legal da respetiva organização em implementações de cliente aprovados.
