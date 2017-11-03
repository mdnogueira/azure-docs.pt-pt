---
title: "Serviços de domínio do Azure AD: Comparar do Azure AD dos serviços de domínio para os controladores de domínio DIY | Microsoft Docs"
description: "Comparar o Azure Active Directory Domain Services para os controladores de domínio DIY"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 165249d5-e0e7-4ed1-aa26-91a05a87bdc9
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2017
ms.author: maheshu
ms.openlocfilehash: 09a68c7f4e7169a6ca02e33e89e0f048155fa88c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-decide-if-azure-ad-domain-services-is-right-for-your-use-case"></a>Como decidir se dos serviços de domínio do Azure AD é adequado para o caso de utilização
Com os serviços de domínio do Azure AD pode implementar as cargas de trabalho nos serviços de infraestrutura do Azure, sem ter de se preocupar com a manutenção da infraestrutura de identidade no Azure. Este serviço gerido é diferente de uma implementação típica do Windows Server Active Directory que implementar e administrar por si. O serviço é fácil de implementar e oferece monitorização de estado de funcionamento automático e remediação. Estamos constantemente são evolução de serviço para adicionar suporte para cenários comuns de implementação.

Para decidir se deve utilizar os serviços de domínio do Azure AD, recomendamos o seguinte material de leitura:

* Ver a lista de [funcionalidades oferecidas pelos serviços de domínio do Azure AD](active-directory-ds-features.md).
* Reveja comuns [cenários de implementação de serviços de domínio do Azure AD](active-directory-ds-scenarios.md).
* Por fim, [comparar os serviços de domínio do Azure AD para uma opção de AD do-it-yourself](active-directory-ds-comparison.md#compare-azure-ad-domain-services-to-diy-ad-domain-in-azure).

## <a name="compare-azure-ad-domain-services-to-diy-ad-domain-in-azure"></a>Comparar o serviços de domínio do Azure AD para o domínio do DIY AD no Azure
A tabela seguinte ajuda-o a decidir entre utilizando os serviços de domínio do Azure AD e gerir a sua própria infraestrutura de AD no Azure.

| **Funcionalidade** | **Serviços de domínio do Azure AD** | **'Do-it-yourself' AD em VMs do Azure** |
| --- |:---:|:---:|
| [**Serviço gerido**](active-directory-ds-comparison.md#managed-service) |**&#x2713;** |**& #x 2715;** |
| [**Implementações seguras**](active-directory-ds-comparison.md#secure-deployments) |**&#x2713;** |Administrador tem proteger a implementação. |
| [**Servidor DNS**](active-directory-ds-comparison.md#dns-server) |**& #x 2713;**  (serviço gerido) |**&#x2713;** |
| [**Privilégios de administrador de domínio ou Enterprise**](active-directory-ds-comparison.md#domain-or-enterprise-administrator-privileges) |**& #x 2715;** |**&#x2713;** |
| [**Associação a um domínio**](active-directory-ds-comparison.md#domain-join) |**&#x2713;** |**&#x2713;** |
| [**Autenticação de domínio utilizando NTLM e Kerberos**](active-directory-ds-comparison.md#domain-authentication-using-ntlm-and-kerberos) |**&#x2713;** |**&#x2713;** |
| [**Delegação restringida de Kerberos**](active-directory-ds-comparison.md#kerberos-constrained-delegation)|baseada em recursos|baseada em recursos & com base em conta|
| [**Estrutura de UO personalizada**](active-directory-ds-comparison.md#custom-ou-structure) |**&#x2713;** |**&#x2713;** |
| [**Extensões de esquema**](active-directory-ds-comparison.md#schema-extensions) |**& #x 2715;** |**&#x2713;** |
| [**Confianças de floresta e domínio AD**](active-directory-ds-comparison.md#ad-domain-or-forest-trusts) |**& #x 2715;** |**&#x2713;** |
| [**LDAP de leitura**](active-directory-ds-comparison.md#ldap-read) |**&#x2713;** |**&#x2713;** |
| [**LDAP seguro (LDAPS)**](active-directory-ds-comparison.md#secure-ldap) |**&#x2713;** |**&#x2713;** |
| [**Escrita LDAP**](active-directory-ds-comparison.md#ldap-write) |**& #x 2715;** |**&#x2713;** |
| [**Política de grupo**](active-directory-ds-comparison.md#group-policy) |**&#x2713;** |**&#x2713;** |
| [**Geo-distribuição implementações**](active-directory-ds-comparison.md#geo-dispersed-deployments) |**& #x 2715;** |**&#x2713;** |

#### <a name="managed-service"></a>Serviço gerido
Domínios de serviços de domínio do AD do Azure são geridos pela Microsoft. Não dispõe de preocupar com a aplicação de patches, atualizações, monitorização, cópias de segurança e garantir a disponibilidade do seu domínio. Estas tarefas de gestão são disponibilizadas como um serviço pelo Microsoft Azure para os domínios geridos.

#### <a name="secure-deployments"></a>Implementações seguras
O domínio gerido em segurança bloqueado de acordo com as recomendações de segurança da Microsoft para implementações do AD. Estas recomendações stem de decades da equipa de produto do AD de experiência de engenharia e implementações de AD de suporte. Para implementações do-it-yourself, terá de efetuar os passos de implementação específicas para bloquear secure/para baixo a implementação.

#### <a name="dns-server"></a>Servidor DNS
Um domínio gerido dos serviços de domínio do Azure AD inclui serviços DNS geridos. Os membros do grupo 'AAD DC administradores' podem gerir o DNS no domínio gerido. Os membros deste grupo recebem privilégios completos de administração de DNS para o domínio gerido. Gestão de DNS pode ser efetuada utilizando a 'Consola de administração de DNS' incluída no pacote de ferramentas de administração remota de servidor (FARS).
[Obter mais informações](active-directory-ds-admin-guide-administer-dns.md)

#### <a name="domain-or-enterprise-administrator-privileges"></a>Privilégios de administrador de empresa ou de domínio
Estes privilégios elevados não são disponibilizados num domínio gerido AAD DS. As aplicações que necessitam destas privilégios elevados não não possível implementar contra AAD DS Domínios geridos. Um subconjunto mais pequeno de privilégios administrativos está disponível para os membros do grupo de administração de delegado denominado 'AAD DC administradores'. Esses privilégios incluem privilégios para configurar o DNS, configurar a política de grupo, obter privilégios de administrador em computadores associados a um domínio etc.

#### <a name="domain-join"></a>Associação a um domínio
Pode associar máquinas virtuais ao domínio gerido semelhante à forma como associar computadores a um domínio do AD.

#### <a name="domain-authentication-using-ntlm-and-kerberos"></a>Autenticação de domínio utilizando NTLM e Kerberos
Com os serviços de domínio do Azure AD, pode utilizar as credenciais da sua empresa para autenticar com o domínio gerido. As credenciais são mantidas em sincronização com o seu inquilino do Azure AD. Para inquilinos sincronizados, o Azure AD Connect garante que serão sincronizadas as alterações efetuadas de credenciais no local ao Azure AD. Com uma configuração de domínio do-it-yourself, poderá ter de configurar um domínio do AD fidedignidade com o ambiente AD para os utilizadores sejam autenticados com as respetivas credenciais empresariais. Em alternativa, poderá ter de configurar a replicação de AD para garantir que as palavras-passe do utilizador sincronizar para as máquinas de virtuais do controlador de domínio do Azure.

#### <a name="kerberos-constrained-delegation"></a>Delegação restringida de Kerberos
Não dispõe de privilégios de administrador de domínio num domínio gerido dos serviços de domínio do Active Directory. Por conseguinte, não é possível configurar a delegação restringida de Kerberos (tradicional) com base na conta. No entanto, pode configurar a mais segura delegação restrita baseada em recursos.
[Obter mais informações](active-directory-ds-enable-kcd.md)

#### <a name="custom-ou-structure"></a>Estrutura de UO personalizada
Os membros do grupo 'AAD DC administradores' podem criar UOs personalizados dentro do domínio gerido. Os utilizadores que criar UOs personalizados são concedidos privilégios administrativos totais através da UO.
[Obter mais informações](active-directory-ds-admin-guide-create-ou.md)

#### <a name="schema-extensions"></a>Extensões de esquema
Não é possível expandir o esquema de base de um domínio gerido dos serviços de domínio do Azure AD. Por conseguinte, as aplicações que dependem de extensões de esquema do AD (por exemplo, novos atributos sob o objecto de utilizador) não não possível lifted e desviadas domínios de AAD DS.

#### <a name="ad-domain-or-forest-trusts"></a>Domínio do AD ou fidedignidades de floresta
Não não possível configurar domínios geridos para configurar as relações de confiança (entrada/saída) com outros domínios. Por conseguinte, os cenários de implementação de floresta de recursos não é possível utilizar os serviços de domínio do Azure AD. Da mesma forma, as implementações em que preferir não sincronizar as palavras-passe para o Azure AD não é possível utilizar os serviços de domínio do Azure AD.

#### <a name="ldap-read"></a>Leitura LDAP
O domínio gerido suporta LDAP cargas de trabalho de leitura. Por conseguinte, pode implementar as aplicações que efetuarem operações de leitura de LDAP contra o domínio gerido.

#### <a name="secure-ldap"></a>LDAP seguro
Pode configurar os serviços de domínio do Azure AD para fornecer acesso seguro de LDAP para o seu domínio gerido, incluindo através da internet.
[Obter mais informações](active-directory-ds-admin-guide-configure-secure-ldap.md)

#### <a name="ldap-write"></a>Escrita LDAP
O domínio gerido é só de leitura para objetos de utilizador. Por conseguinte, as aplicações que executam operações de escrita LDAP contra os atributos do objeto de utilizador não funcionam num domínio gerido. Além disso, as palavras-passe de utilizador não podem ser alteradas de dentro do domínio gerido. Outro exemplo seria modificação de membros do grupo ou os atributos de grupo no domínio gerido, que não é permitido. No entanto, quaisquer alterações aos atributos de utilizador ou palavras-passe efetuadas no Azure AD (através do portal do PowerShell/Azure) ou AD são sincronizadas com o domínio gerido AAD-DS no local.

#### <a name="group-policy"></a>Política de grupo
Não há um incorporada GPO cada para os contentores "AADDC computadores" e "AADDC utilizadores". Pode personalizar estes GPOs incorporados para configurar a política de grupo. Os membros do grupo 'AAD DC administradores' também podem criar GPOs personalizados e ligá-las para UOs existentes (incluindo UOs personalizados).
[Obter mais informações](active-directory-ds-admin-guide-administer-group-policy.md)

#### <a name="geo-dispersed-deployments"></a>Georreplicação-dispersos implementações
Domínios geridos de serviços de domínio do AD do Azure estão disponíveis numa única rede virtual no Azure. Para cenários que necessitam de controladores de domínio para estar disponível em várias regiões do Azure por todo o mundo, a configurar os controladores de domínio em VMs do IaaS do Azure pode ser a melhor alternativa.


## <a name="do-it-yourself-diy-ad-deployment-options"></a>Opções de implementação (DIY) AD 'Do-it-yourself'
Poderá ter casos de utilização de implementação em que precisa de algumas das funcionalidades oferecidas por uma instalação do Windows Server AD. Nestes casos, considere uma das seguintes opções (DIY) do-it-yourself:

* **Domínio de nuvem autónomo:** que pode configurar uma autónoma 'domínio cloud' utilizar máquinas virtuais do Azure que tenham sido configuradas como controladores de domínio. Esta infraestrutura não integrar no local com o ambiente do AD. Esta opção precisaria que um conjunto diferente de 'credenciais na nuvem' para início de sessão/administrar VMs na nuvem.
* **Implementação de floresta de recursos:** pode configurar um domínio na topologia de floresta de recursos, utilizando configurados como controladores de domínio virtual machines do Azure. Em seguida, pode configurar uma relação de fidedignidade do AD no local com o ambiente do AD. Pode computadores de associação de domínio (as VMs do Azure) para esta floresta de recursos na nuvem. Autenticação de utilizador ocorre através de um uma ligação VPN/ExpressRoute para o seu diretório no local.
* **Expandir o seu domínio no local para o Azure:** pode ligar uma rede virtual do Azure à sua rede no local utilizando uma ligação VPN/ExpressRoute. Esta configuração permite que as VMs do Azure a ser associada ao seu local AD. Outra alternativa é promover os controladores de domínio de réplica do seu domínio no local no Azure como uma VM. Pode, em seguida, configurá-lo para replicar através de uma ligação VPN/ExpressRoute para o seu diretório no local. Neste modo de implementação eficaz expande o seu domínio no local para o Azure.

> [!NOTE]
> Pode determinar que opção de DIY melhor é adequada para os casos de utilização de implementação. Considere [partilha comentários](active-directory-ds-contact-us.md) para o ajudar-na compreender o que iriam ajudar funcionalidades que escolheu serviços de domínio do Azure AD no futuro. Estes comentários ajudam-na evoluir o serviço de melhor se adequarem às suas necessidades de implementação e casos de utilização.
>
>

Iremos tiver publicado [diretrizes para implementar o Windows Server Active Directory em Azure Virtual Machines](https://msdn.microsoft.com/library/azure/jj156090.aspx) para ajudar o facilitar DIY instalações.

## <a name="related-content"></a>Conteúdo relacionado
* [Funcionalidades - Serviços de domínio do Azure AD](active-directory-ds-features.md)
* [Cenários de implementação - serviços de domínio do Azure AD](active-directory-ds-scenarios.md)
* [Diretrizes para a implementação do Windows Server Active Directory em máquinas virtuais do Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx)
