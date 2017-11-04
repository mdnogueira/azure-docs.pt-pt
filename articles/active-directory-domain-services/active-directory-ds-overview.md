---
title: "Descrição geral dos serviços de domínio do Active Directory do Azure | Microsoft Docs"
description: "Descrição geral dos serviços de domínio do Active Directory do Azure"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mahesh-unnikrishnan
editor: curtand
ms.assetid: 0d47178f-773e-45f9-9ff4-9e8cffa4ffa2
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2017
ms.author: maheshu
ms.openlocfilehash: be18ee0266a97057499baccc5bb39a35224336d7
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="azure-active-directory-ad-domain-services"></a>Serviços de domínio do Azure Active Directory (AD)
## <a name="overview"></a>Descrição geral
Serviços de infraestrutura do Azure permitem-lhe implementar uma vasta gama de soluções de computação de forma seja ágil. Com máquinas de virtuais do Azure, pode implementar quase instantaneamente e paga apenas por minuto. Utilizar o suporte para Windows, Linux, SQL Server, Oracle, IBM, SAP e BizTalk, pode implementar qualquer carga de trabalho, qualquer idioma, em praticamente qualquer sistema operativo. Estas vantagens permitem-lhe migrar aplicações antigas implementados no local para o Azure, para guardar em despesas operacionais.

Um aspeto chave da migrar aplicações no local para o Azure está a processar as necessidades de identidade destas aplicações. Aplicações com suporte para o diretório poderão dependem de LDAP para leitura ou de acesso de escrita ao diretório da empresa ou baseiam-se na autenticação integrada do Windows (autenticação Kerberos ou NTLM) para autenticar os utilizadores finais. Aplicações do linha de negócio (LOB) em execução no Windows Server, normalmente, são implementadas em computadores associados a um domínio, pelo que podem ser geridos em segurança através da política de grupo. Para aplicações de 'comparação de precisão e shift' no local para a nuvem, estas dependências na infraestrutura de identidade empresarial tem de ser resolvidos.

Os administradores, muitas vezes, ative a uma das seguintes soluções para satisfazer as necessidades de identidade das respetivas aplicações implementadas no Azure:

* Implemente uma ligação de VPN de site a site entre as cargas de trabalho em execução nos serviços de infraestrutura do Azure e a empresa diretório no local.
* Ampliar a infraestrutura de domínio/floresta de AD empresarial ao configurar os controladores de domínio de réplica com máquinas virtuais do Azure.
* Implemente um domínio autónomo no Azure utilizando os controladores de domínio implementados como máquinas virtuais do Azure.

Todas estas abordagens sofrem do custo elevado e a sobrecarga administrativa. Os administradores são necessários para implementar controladores de domínio com máquinas virtuais no Azure. Além disso, precisam de gerir, proteger, aplicar o patch, monitorizar, cópia de segurança e resolver estas máquinas virtuais. A dependência em ligações de VPN para o diretório no local faz com que as cargas de trabalho implementadas no Azure para estar vulnerável a glitches de rede transitórios ou falhas. Estas falhas de rede por sua vez resultam num menor tempo de atividade e fiabilidade reduzida para estas aplicações.

Foi concebido serviços de domínio do Azure AD para fornecer uma alternativa mais fácil.

### <a name="watch-an-introductory-video"></a>Ver um vídeo introdutórias
<iframe width="560" height="315" src="https://www.youtube.com/embed/T1Nd9APNceQ" frameborder="0" allowfullscreen></iframe>


## <a name="introducing-azure-ad-domain-services"></a>Introdução dos serviços de domínio do Azure AD
Serviços de domínio do AD do Azure fornece serviços de domínio geridos, tais como a associação a um domínio, autenticação de Kerberos/NTLM de política, LDAP, grupo são totalmente compatível com o Windows Server Active Directory. Pode consumir estes serviços de domínio sem a necessidade de implementar, gerir e corrigir os controladores de domínio na nuvem. Serviços de domínio do AD do Azure integra-se com o seu inquilino do Azure AD existente, deste modo, permitir que os utilizadores iniciem sessão com as respetivas credenciais empresariais. Além disso, pode utilizar grupos existentes e contas de utilizador para acesso seguro a recursos, que garante uma smoother 'comparação de precisão-e-shift' de recursos no local para serviços de infraestrutura do Azure.

Funcionalidade de serviços de domínio do AD do Azure funciona na perfeição independentemente se o seu inquilino do Azure AD é só de nuvem ou sincronizado com o Active Directory no local.

### <a name="azure-ad-domain-services-for-cloud-only-organizations"></a>Serviços de domínio do Azure AD para organizações apenas na nuvem
Uma nuvem apenas de inquilino do Azure AD (normalmente designado como 'geridos inquilinos') não tem quaisquer requisitos de espaço de identidade no local. Por outras palavras, contas de utilizador, as respetivas palavras-passe e as associações de grupo são todos os nativas na nuvem - ou seja, é criada e gerida no Azure AD. Considere para um momento que a Contoso é um apenas na nuvem inquilino do Azure AD. Como é mostrado na ilustração seguinte, o administrador da Contoso tiver configurado uma rede virtual nos serviços de infraestrutura do Azure. Aplicações e cargas de trabalho do servidor são implementadas na rede virtual em virtual machines do Azure. Uma vez que a Contoso é um inquilino apenas na nuvem, todas as identidades de utilizador, as suas credenciais e associações a grupos são criadas e geridas no Azure AD.

![Descrição geral de serviços de domínio do Azure AD](./media/active-directory-domain-services-overview/aadds-overview.png)

Do contoso administrador de TI pode ativar os serviços de domínio do Azure AD para o seu inquilino do Azure AD e optar por disponibilizar os serviços de domínio na rede virtual. Depois disso, os serviços de domínio do Azure AD aprovisiona um domínio gerido e torna disponível na rede virtual. Todas as contas de utilizador, associações a grupos e credenciais de utilizador disponíveis no inquilino do Azure AD da Contoso também estão disponíveis neste domínio recentemente criado. Esta funcionalidade permite na organização aos utilizadores iniciar sessão domínio com as respetivas credenciais empresariais - por exemplo, ao ligar remotamente a computadores associados a um de domínio através do ambiente de trabalho remoto. Os administradores podem aprovisionar o acesso aos recursos no domínio utilizando as associações de grupo existente. As aplicações implementadas em máquinas virtuais na rede virtual podem utilizar funcionalidades como a associação a um domínio, LDAP leitura, LDAP bind, a autenticação NTLM e Kerberos e política de grupo.

Alguns aspetos salientes domínio gerido que está aprovisionado pelos serviços de domínio do Azure AD são os seguintes:

* Do contoso administrador de TI não precisa de gerir, patches ou para monitorizar este domínio ou quaisquer controladores de domínio para este domínio gerido.
* Não é necessário para gerir a replicação de AD para este domínio. Contas de utilizador, as associações de grupo e as credenciais de inquilino do Azure AD da Contoso estão automaticamente disponíveis dentro deste domínio gerido.
* Uma vez que o domínio é gerido pelo Azure AD nos serviços de domínio Contoso administrador de TI não tem privilégios de administrador do domínio ou de administrador de empresa neste domínio.

### <a name="azure-ad-domain-services-for-hybrid-organizations"></a>Serviços de domínio do Azure AD para organizações híbrida
As organizações com uma versão híbrida a infraestrutura de TI consumam uma combinação de recursos de nuvem e de recursos no local. Estas organizações sincronizam informações de identidade do seu diretório no local para o seu inquilino do Azure AD. Como as organizações a híbrida parecer para migrar mais das respetivas aplicações no local para a nuvem, especialmente legadas com suporte para o diretório de aplicações, os serviços de domínio do Azure AD podem ser útil aos mesmos.

Implementou litware Corporation [do Azure AD Connect](../active-directory/active-directory-aadconnect.md), para sincronizar informações de identidade do seu diretório no local para o seu inquilino do Azure AD. As informações de identidade estão sincronizadas incluem as contas de utilizador, os hashes de credencial de autenticação (sincronização de palavra-passe) e as associações de grupo.

> [!NOTE]
> **Sincronização de palavra-passe é obrigatória para as organizações de híbrida utilizar os serviços de domínio do Azure AD**. Este requisito é porque são necessárias credenciais de utilizadores no domínio gerido fornecido pelos serviços de domínio do Azure AD, para autenticar estes utilizadores através de métodos de autenticação de NTLM ou Kerberos.
>
>

![Serviços de domínio do Azure AD para Litware Corporation](./media/active-directory-domain-services-overview/aadds-overview-synced-tenant.png)

A ilustração anterior mostra como as organizações com uma versão híbrida a infraestrutura de TI, tais como Litware Corporation, podem utilizar os serviços de domínio do Azure AD. Aplicações e cargas de trabalho do servidor que requerem os serviços de domínio da litware são implementadas numa rede virtual nos serviços de infraestrutura do Azure. Do litware administrador de TI pode ativar os serviços de domínio do Azure AD para o seu inquilino do Azure AD e escolha disponibilizar um domínio gerido nesta rede virtual. Uma vez que Litware organização com uma versão híbrida a infraestrutura de TI, contas de utilizador, grupos e as credenciais são sincronizadas para o seu inquilino do Azure AD do respetivo diretório no local. Esta funcionalidade permite aos utilizadores iniciar sessão domínio utilizando as respetivas credenciais empresariais - por exemplo, quando ligar remotamente ao máquinas associados ao domínio através de ambiente de trabalho remoto. Os administradores podem aprovisionar o acesso aos recursos no domínio utilizando as associações de grupo existente. As aplicações implementadas em máquinas virtuais na rede virtual podem utilizar funcionalidades como a associação a um domínio, LDAP leitura, LDAP bind, a autenticação NTLM e Kerberos e política de grupo.

Alguns aspetos salientes domínio gerido que está aprovisionado pelos serviços de domínio do Azure AD são os seguintes:

* O domínio gerido é um domínio autónomo. Não é uma extensão de domínio da Litware no local.
* Do litware administrador de TI não precisa de gerir, patches, ou monitorizar controladores de domínio para este domínio gerido.
* Não é necessário para gerir a replicação de AD para este domínio. Contas de utilizador, as associações de grupo e as credenciais de diretório no local da Litware são sincronizadas com o Azure AD através do Azure AD Connect. Estas contas de utilizador, as associações de grupo e as credenciais estão automaticamente disponíveis no domínio gerido.
* Uma vez que o domínio é gerido pelo Azure AD nos serviços de domínio Litware administrador de TI não tem privilégios de administrador do domínio ou de administrador de empresa neste domínio.

## <a name="benefits"></a>Benefícios
Com os serviços de domínio do Azure AD, possam desfrutar as seguintes vantagens:

* **Simples** – pode satisfazer as necessidades de identidade das máquinas virtuais implementadas aos serviços de infraestrutura do Azure com alguns cliques. Não é necessário implementar e gerir a infraestrutura de identidade no Azure ou a configuração de conectividade de volta para a sua infraestrutura de identidade no local.
* **Integrada** – os serviços de domínio do Azure AD está profundamente integrado com o seu inquilino do Azure AD. Agora pode utilizar o Azure AD como um diretório de enterprise integrada baseada na nuvem que caters às necessidades da sua aplicações modernas e de aplicações com suporte para o diretório tradicionais.
* **Compatível** – os serviços de domínio do Azure AD baseia-se na infraestrutura de nível empresarial comprovado do Windows Server Active Directory. Por conseguinte, as aplicações podem dependem de um maior nível de compatibilidade com as funcionalidades do Windows Server Active Directory. Nem todas as funcionalidades disponíveis no Windows Server AD estão atualmente disponíveis nos serviços de domínio do Azure AD. No entanto, as funcionalidades disponíveis são compatíveis com as funcionalidades do Windows Server AD correspondentes que dependem na sua infraestrutura no local. As capacidades de associação LDAP, Kerberos, NTLM, política de grupo e domínio constituem uma oferta madura que tem sido testada e foi refinada através de várias versões do Windows Server.
* **Económica** – com os serviços de domínio do Azure AD, pode evitar o fardo de gestão e infraestrutura que está associado a gerir a infraestrutura de identidade para suportar aplicações com suporte para o diretório tradicionais. Pode mover destas aplicações nos serviços de infraestrutura do Azure e beneficiar de maiores reduções em despesas operacionais.


## <a name="next-steps"></a>Passos seguintes
### <a name="learn-more-about-azure-ad-domain-services"></a>Saiba mais sobre os serviços de domínio do Azure AD
* [Funcionalidades](active-directory-ds-features.md)
* [Cenários de implementação](active-directory-ds-scenarios.md)
* [Saber se os serviços de domínio do Azure AD se adequa aos seus casos de utilização](active-directory-ds-comparison.md)
* [Compreender a forma como os serviços de domínio do Azure AD sincroniza com o seu diretório do Azure AD](active-directory-ds-synchronization.md)

### <a name="get-started-with-azure-ad-domain-services"></a>Introdução ao Azure AD Domain Services
* [Ativar os serviços de domínio do Azure AD através do portal do Azure](active-directory-ds-getting-started.md)
