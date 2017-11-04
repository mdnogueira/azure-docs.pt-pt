---
title: "Descrição geral de monitorização e gestão de segurança do Azure | Microsoft Docs"
description: " O Azure oferece mecanismos de segurança para ajudar a gestão e monitorização de máquinas virtuais e serviços em nuvem do Azure.  Este artigo fornece uma descrição geral destas funcionalidades de segurança de núcleo e serviços. "
services: security
documentationcenter: na
author: TerryLanfear
manager: StevenPo
editor: TomSh
ms.assetid: 5cf2827b-6cd3-434d-9100-d7411f7ed424
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: terrylan
ms.openlocfilehash: 6787877deabafd0b7308e190cb45b4036049b05b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-security-management-and-monitoring-overview"></a>Descrição geral de monitorização e gestão de segurança do Azure
O Azure oferece mecanismos de segurança para ajudar a gestão e monitorização de máquinas virtuais e serviços em nuvem do Azure. Este artigo fornece uma descrição geral destas funcionalidades de segurança de núcleo e serviços. São fornecidas hiperligações para artigos que fornecer detalhes de cada, pelo que pode saber mais.

A segurança dos seus serviços em nuvem do Microsoft é uma parceria e responsabilidade partilhada entre si e a Microsoft. Responsabilidade partilhada significa que Microsoft é responsável pelo Microsoft Azure e a segurança física dos seus centros de dados (ao utilizar proteções de segurança como portas de entrada de distintivo bloqueado, fences e guards). Além disso, o Azure oferece fortes níveis de segurança de nuvem na camada de software que satisfaça as necessidades de segurança, privacidade e conformidade dos seus clientes demanding.

Proprietário os dados e identidades, a responsabilidade de protegê-los, a segurança dos seus recursos no local e a segurança dos componentes de nuvem através do qual tem controlo. A Microsoft fornece-o com controlos de segurança e funcionalidades para o ajudar a proteger os seus dados e aplicações. O grau de responsabilidade de segurança baseia-se no tipo de serviço em nuvem.

O gráfico seguinte resume o equilíbrio de responsabilidade para a Microsoft e o cliente.

![Responsabilidade partilhada][1]

Para obter uma descrição mais aprofundada para gestão de segurança, consulte [gestão de segurança no Azure](azure-security-management.md).

Seguem-se as funcionalidades de núcleos para ser abordadas neste artigo:

* Controlo de Acesso Baseado em Funções
* Antimalware
* Multi-Factor Authentication
* ExpressRoute
* Gateways de rede virtual
* Gestão de identidades privilegiadas
* Proteção de identidade
* Centro de Segurança

## <a name="role-based-access-control"></a>Controlo de Acesso Baseado em Funções
Controlo de acesso baseado em funções (RBAC) fornece uma gestão pormenorizada de acesso para recursos do Azure. Utilizar o RBAC, pode conceder pessoas apenas a quantidade de acesso que precisam para desempenhar as suas funções.  RBAC também pode ajudar a garantir que quando as pessoas deixam a organização percam o acesso a recursos na nuvem.

Saiba mais:

* [Blogue de equipa do Active Directory em RBAC](http://i1.blogs.technet.com/b/ad/archive/2015/10/12/azure-rbac-is-ga.aspx)
* [Controlo de acesso baseado em funções do Azure](../active-directory/role-based-access-control-configure.md)

## <a name="antimalware"></a>Antimalware
Com o Azure, pode utilizar o software antimalware dos fornecedores de principais de segurança, tais como Microsoft, da Symantec, Micro de tendência, McAfee e Kaspersky para ajudar a proteger as máquinas virtuais a partir de ficheiros maliciosos, adware e outras ameaças.

Antimalware da Microsoft oferece a capacidade de instalar um agente de antimalware para funções de PaaS e máquinas virtuais. Com base no System Center Endpoint Protection, esta funcionalidade coloca comprovado no local tecnologia de segurança para a nuvem.

Que oferecemos também a integração profunda do tendência [segurança profunda](http://www.trendmicro.com/us/enterprise/cloud-solutions/deep-security/)™ e [SecureCloud](http://www.trendmicro.com/us/enterprise/cloud-solutions/secure-cloud/)™ produtos na plataforma do Azure. DeepSecurity é uma solução de antivírus e SecureCloud é uma solução de encriptação. DeepSecurity é implementado dentro de VMs com um modelo de extensão. Usando a IU do portal e o PowerShell, pode optar por utilizar DeepSecurity dentro de novas VMs que estão a ser aceleradas ou VMs existentes que já estão implementadas.

Proteção de ponto final da Symantec (SEP) também é suportada no Azure. Através da integração do portal, os clientes podem especificar que pretende utilizar SEP dentro de uma VM. SEP pode ser instalado numa VM nova através do portal do Azure ou pode ser instalado numa VM existente com o PowerShell.

Saiba mais:

* [Implementar Soluções Antimalware em Máquinas Virtuais do Azure](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Antimalware da Microsoft para máquinas virtuais e serviços em nuvem do Azure](azure-security-antimalware.md)
* [Como instalar e configurar a segurança avançada do tendência Micro como um serviço numa VM do Windows](../virtual-machines/windows/classic/install-trend.md)
* [Como instalar e configurar o Symantec Endpoint Protection numa VM do Windows](../virtual-machines/windows/classic/install-symantec.md)
* [Novas opções de Antimalware para proteger máquinas virtuais do Azure – McAfee Endpoint Protection](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication
Azure multi-factor authentication (MFA) é um método de autenticação que requer a utilização de mais do que um método de verificação e adiciona uma segunda camada crítica de segurança aos inícios de sessão de utilizador e de transações. MFA ajuda a salvaguardar o acesso a dados e aplicações, cumprindo o pedido do utilizador para um processo de início de sessão simple. Fornece autenticação forte através de uma gama de opções de verificação — chamada telefónica, mensagem de texto ou aplicação móvel notificação ou da verificação de código e de terceiros tokens OATH.

Saiba mais:

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [O que é o Multi-Factor Authentication do Azure?](../multi-factor-authentication/multi-factor-authentication.md)
* [Como funciona o Azure multi-factor Authentication](../multi-factor-authentication/multi-factor-authentication-how-it-works.md)

## <a name="expressroute"></a>ExpressRoute
O Microsoft Azure ExpressRoute permite-lhe expandir as redes no local para a nuvem da Microsoft através de uma ligação privada dedicada facilitada por um fornecedor de conectividade. Com o ExpressRoute, pode estabelecer ligações aos serviços em nuvem da Microsoft, tais como o Microsoft Azure, Office 365 e o CRM Online. A conectividade pode ser a partir de uma rede qualquer a qualquer (VPN de IP), uma rede Ethernet de ponto a ponto ou uma ligação cruzada virtual através de um fornecedor de conectividade numa localização conjunta. As ligações do ExpressRoute não passam para a Internet pública. Tal permite que as ligações do ExpressRoute ofereçam mais fiabilidade, velocidades superiores, latências inferiores e uma maior segurança do que as ligações típicas através da Internet.

Saiba mais:

* [Descrição geral técnica do ExpressRoute](../expressroute/expressroute-introduction.md)

## <a name="virtual-network-gateways"></a>Gateways de rede virtual
Gateways de VPN, também denominados Gateways de rede Virtual do Azure, são utilizados para enviar tráfego de rede entre redes virtuais e localizações no local. Também são utilizadas para enviar tráfego entre várias redes virtuais no Azure (VNet a VNet).  Gateways de VPN fornecem uma conectividade entre instalações segura entre o Azure e a infraestrutura.

Saiba mais:

* [Acerca dos gateways de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [Descrição geral de segurança de rede do Azure](security-network-overview.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management
Por vezes, os utilizadores precisam realizar operações privilegiadas de recursos do Azure ou outras aplicações SaaS. Isto, muitas vezes, significa que as organizações têm atribuir-lhes acesso privilegiado permanente no Azure Active Directory (Azure AD). Este é um risco de segurança crescente para recursos alojados na nuvem, porque as organizações não é possível monitorizar suficientemente que esses utilizadores estão a fazer com o respetivo acesso privilegiado.
Além disso, se uma conta de utilizador com acesso privilegiado for comprometida, o que uma violação pode afetar a segurança de nuvem global. O Azure AD Privileged Identity Management ajuda a resolver este risco, diminuindo o tempo de exposição de privilégios e aumentar a visibilidade em utilização.  

Privileged Identity Management apresenta o conceito de um administrador temporário para uma função ou "just in time" acesso de administrador, que é um utilizador que precisa para concluir um processo de ativação para que a função atribuída. O processo de ativação é alterado a atribuição do utilizador para uma função no Azure AD do período inativo para o Active Directory, durante um período de tempo especificado, tais como oito horas.

Saiba mais:

* [Azure AD Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-configure.md)
* [Introdução ao Azure AD Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-getting-started.md)

## <a name="identity-protection"></a>Identity Protection
Proteção de identidade do Azure Active Directory (AD) fornece uma vista consolidada de início de sessão atividades suspeitas e potenciais vulnerabilidades para ajudar a proteger a sua empresa. Proteção de identidade Deteta atividades suspeitas para utilizadores e identidades de privilégio (administrador), com base no sinais como ataques de força bruta, podem fugir credenciais e inícios de sessão a partir de localizações desconhecidas e infetados dispositivos.

Ao fornecer notificações e a remediação recomendada, Identity Protection ajuda a mitigar os riscos em tempo real. Calculará gravidade de risco de utilizador e pode configurar as políticas baseadas em risco para o ajudar automaticamente a aplicação de salvaguarda contra ameaças futuras de acesso.

Saiba mais:

* [Proteção de identidade do Azure Active Directory](../active-directory/active-directory-identityprotection.md)
* [Canal 9: Do Azure AD e mostrar de identidade: identidade pré-visualização de proteção](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="security-center"></a>Centro de Segurança
Centro de segurança do Azure ajuda-o a evitar, detetar e responder a ameaças e fornece que maior visibilidade e controlo sobre, a segurança dos seus recursos Azure. Fornece gestão de políticas e monitorização de segurança integrada nas suas subscrições do Azure, ajuda a detetar ameaças que caso contrário podem passar despercebidas e funciona com um ecossistema abrangente de soluções de segurança.

Centro de segurança ajudam a otimizar e monitorizar a segurança dos seus recursos do Azure por:

* Definir políticas para os seus recursos de subscrição do Azure, de acordo com as necessidades de segurança da sua empresa e o tipo de aplicações ou sensibilidade dos dados em cada subscrição a ativar.
* Monitorizar o estado das máquinas virtuais do Azure, das redes e aplicações.
* Fornecer uma lista de alertas de segurança prioritários, incluindo alertas de soluções de parceiros integradas, juntamente com as informações que necessárias para investigar rapidamente e recomendações sobre como remediar um ataque.

Saiba mais:

* [Introdução ao centro de segurança do Azure](../security-center/security-center-intro.md)

<!--Image references-->
[1]: ./media/security-management-and-monitoring-overview/shared-responsibility.png
