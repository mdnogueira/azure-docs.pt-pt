---
title: "Artigo de índice para a gestão de aplicações no Azure Active Directory | Microsoft Azure"
description: "Saiba como personalizar a data de expiração para os certificados de Federação e como renovar os certificados que irão expirar em breve."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 5321b8e4-2afa-4dfe-8d53-4add7abb5ec8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/21/2017
ms.author: markvi
ms.reviewer: asteen
ms.openlocfilehash: d8ed395abb31a1cb41e35456ab5892a2e7c3a750
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="article-index-for-application-management-in-azure-active-directory"></a>Índice de Artigos da Gestão da Aplicação no Azure Active Directory
Esta página fornece uma lista completa de todos os documentos escritas sobre as várias funcionalidades relacionadas com a aplicação no Azure Active Directory (Azure AD).

Há uma breve introdução para cada área de funcionalidade principais, bem como orientações que artigos ler consoante as informações que procura.

## <a name="overview-articles"></a>Artigos de descrição geral
Os artigos abaixo são bons pontos de partida para quem pretende simplesmente uma breve explicação de funcionalidades de gestão de aplicações do Azure AD.

| Guia do artigo |  |
|:---:| --- |
| Uma introdução para os problemas de gestão de aplicações que resolve o Azure AD |[Gestão de aplicações com o Azure Active Directory (AD)](active-directory-enable-sso-scenario.md) |
| Uma descrição geral das várias funcionalidades no Azure AD relacionadas com a ativação de início de sessão, definir a quem tem acesso a aplicações e a forma como os utilizadores iniciarem aplicações |[Acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md) |
| Veja os diferentes passos envolvidos quando integrar aplicações com o seu Azure AD |[Integração do Azure Active Directory com aplicações](active-directory-integrating-applications-getting-started.md)<br /><br />[Ativar o início de sessão para aplicações SaaS](active-directory-enterprise-apps-manage-sso.md)<br /><br />[Gerir o acesso a aplicações](active-directory-managing-access-to-apps.md) |
| Uma técnica explicação de como as aplicações são representadas no Azure AD |[Como e por que razão são adicionadas aplicações para o Azure AD](active-directory-how-applications-are-added.md) |

## <a name="troubleshooting-articles"></a>Artigos de resolução de problemas
Esta secção fornece acesso rápido para guias de resolução de problemas relevantes. Podem encontrar mais informações sobre cada área de funcionalidade em rest desta página.

| Área de funcionalidade |  |
|:---:| --- |
| Federado Single Sign-On |[Resolução de problemas baseados em SAML Single Sign-On](active-directory-saml-debugging.md) |
| Baseado em palavra-passe de início de sessão |[A extensão do painel de acesso de resolução de problemas para o Internet Explorer](active-directory-saas-ie-troubleshooting.md) |
| Proxy da Aplicação |[Guia de resolução de problemas de Proxy de aplicações](active-directory-application-proxy-troubleshoot.md) |
| Início de sessão único no local AD e o Azure AD |[Resolução de problemas de sincronização de palavra-passe](connect/active-directory-aadconnectsync-implement-password-synchronization.md#troubleshoot-password-synchronization)<br /><br />[Resolução de problemas de repetição de escrita de palavras-passe](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) |
| Associações de grupo dinâmico |[As associações de grupo dinâmico de resolução de problemas](active-directory-accessmanagement-troubleshooting.md) |

## <a name="single-sign-on-sso"></a>Início de Sessão Único (SSO)
### <a name="federated-single-sign-on-sign-into-many-apps-using-one-identity"></a>Federado Single Sign-On: Inicie sessão em muitas aplicações utilizando uma identidade
O início de sessão único permite aos utilizadores aceder uma variedade de aplicações e serviços apenas um conjunto de credenciais a utilizar. Federação é um método através do qual pode ativar o início de sessão único. Quando os utilizadores tentam iniciar sessão em aplicações federadas, estes redirecionado para oficial-página sessão da respetiva organização composta pelo Azure Active Directory e, em seguida, são redirecionados para a aplicação após a autenticação com êxito.

| Guia do artigo |  |
|:---:| --- |
| Uma introdução para Federação e outros tipos de início de sessão |[Início de sessão único com o Azure AD](active-directory-appssoaccess-whatis.md) |
| Milhares de aplicações SaaS previamente integradas com o Azure AD com simplificada passos de configuração de início de sessão único |[Introdução à Galeria de aplicações do Azure AD](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery)<br /><br />[Obter uma lista completa das aplicações previamente integradas que suporta a Federação](http://aka.ms/aadfederatedapps)<br /><br />[Como adicionar a aplicação à Galeria de aplicações do Azure AD](active-directory-app-gallery-listing.md) |
| Mais de 150 aplicação tutoriais sobre como configurar único início de sessão para aplicações, tais como [Salesforce](active-directory-saas-salesforce-tutorial.md), [ServiceNow](active-directory-saas-servicenow-tutorial.md), [Google Apps](active-directory-saas-google-apps-tutorial.md), [Workday](active-directory-saas-workday-tutorial.md)e muitos mais |[Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](active-directory-saas-tutorial-list.md) |
| Como configurar e personalizar a configuração do início de sessão único manualmente |[Como para configurar federado Single Sign-On para aplicações que não estejam na Galeria de aplicações do Azure Active Directory](application-config-sso-how-to-configure-federated-sso-non-gallery.md)<br /><br />[Como personalizar afirmações emitidas no Token SAML para aplicações previamente integradas](active-directory-saml-claims-customization.md) |
| Guia de resolução de problemas para aplicações federadas que utilizam o protocolo SAML |[Resolução de problemas baseados em SAML Single Sign-On](active-directory-saml-debugging.md) |
| Como configurar a data de expiração do certificado da sua aplicação e como renovar os certificados |[Gestão de certificados para federado Single Sign-On no Azure Active Directory](active-directory-sso-certs.md) |

Federado-início de sessão único está disponível para todas as edições do Azure AD para aplicações de até dez por utilizador. [O Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) suporta aplicações ilimitadas. Se a organização tiver [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) ou [do Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/), em seguida, pode [utilizar grupos para atribuir acesso a aplicações federadas](#managing-access-to-applications).

### <a name="password-based-single-sign-on-account-sharing-and-sso-for-non-federated-apps"></a>Baseado em palavra-passe Single Sign-On: a partilha de conta e SSO para aplicações não federada
Para ativar o início de sessão para aplicações que não suportam a Federação, o Azure AD oferece funcionalidades de gestão de palavra-passe que podem armazenar com segurança as palavras-passe para aplicações SaaS e iniciar sessão automaticamente os utilizadores para essas aplicações. Pode facilmente distribuir as credenciais para contas recentemente criadas e partilhar contas de equipa com várias pessoas. Os utilizadores não necessariamente precisam de saber as credenciais para as contas que o se estiver a dado acesso ao.

| Guia do artigo |  |
|:---:| --- |
| Uma introdução ao funciona SSO como baseada em palavra-passe e uma breve descrição geral técnica |[Baseado em palavra-passe-início de sessão único com o Azure AD](active-directory-appssoaccess-whatis.md#password-based-single-sign-on) |
| Um resumo dos cenários relacionados com a conta de partilha e como estes problemas são resolvidos pelo Azure AD |[Partilha de contas com o Azure AD](active-directory-sharing-accounts.md) |
| Alterar automaticamente a palavra-passe para determinadas aplicações em intervalos regulares |[Rollover de palavra-passe automática (pré-visualização)](https://blogs.technet.microsoft.com/enterprisemobility/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview/) |
| Implementação e resolução de problemas de guias para a versão do Internet Explorer da extensão de gestão de palavra-passe do Azure AD |[Como implementar a extensão do painel de acesso para o Internet Explorer utilizando a política de grupo](active-directory-saas-ie-group-policy.md)<br /><br />[A extensão do painel de acesso de resolução de problemas para o Internet Explorer](active-directory-saas-ie-troubleshooting.md) |

Baseado em palavra-passe-início de sessão único está disponível para todas as edições do Azure AD para aplicações de até dez por utilizador. [O Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) suporta aplicações ilimitadas. Se a organização tiver [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) ou [do Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/), em seguida, pode [utilizar grupos para atribuir acesso a aplicações](#managing-access-to-applications). Rollover de palavra-passe automática é um [do Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) funcionalidade.

### <a name="app-proxy-single-sign-on-and-remote-access-to-on-premises-applications"></a>Proxy de aplicação: Único início de sessão e acesso remoto para as aplicações no local
Se tiver aplicações na sua rede privada que precisam de ser acedido por utilizadores e dispositivos fora da rede, pode utilizar o Proxy de aplicações do Azure AD para ativar o acesso seguro e remoto às aplicações.

| Guia do artigo |  |
|:---:| --- |
| Descrição geral do Proxy de aplicações do Azure AD e como funciona |[Fornecer acesso remoto seguro a aplicações no local](active-directory-application-proxy-get-started.md) |
| Tutoriais sobre como configurar o Proxy de aplicações e como publicar a sua primeira aplicação |[Como configurar o Proxy de aplicações do Azure AD](active-directory-application-proxy-enable.md)<br /><br />[Como instalar silenciosamente o conector do Proxy de aplicações](active-directory-application-proxy-silent-installation.md)<br /><br />[Como publicar aplicações através do Proxy de aplicação](active-directory-application-proxy-publish.md)<br /><br />[Como utilizar o seu próprio nome de domínio](active-directory-application-proxy-custom-domains.md) |
| Como ativar o acesso de início de sessão e condicional único para aplicações publicadas com o Proxy da aplicação |[Single-sign-on com o Proxy da aplicação](active-directory-application-proxy-sso-using-kcd.md)<br /><br />[Acesso condicional e Proxy de aplicações](application-proxy-enable-remote-access-sharepoint.md) |
| Documentação de orientação sobre como utilizar o Proxy de aplicações para os seguintes cenários |[Como suportar aplicações de cliente nativo](active-directory-application-proxy-native-client.md)<br /><br />[Como suportar aplicações com suporte para afirmações](active-directory-application-proxy-claims-aware-apps.md)<br /><br />[Como suportar as aplicações publicadas em redes separadas e localizações](active-directory-application-proxy-connectors-azure-portal.md) |
| Guia de resolução de problemas do Proxy de aplicações |[Guia de resolução de problemas de Proxy de aplicações](active-directory-application-proxy-troubleshoot.md) |

Proxy de aplicações está disponível para todas as edições do Azure AD para aplicações de até dez por utilizador. [O Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) suporta aplicações ilimitadas. Se a organização tiver [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) ou [do Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/), em seguida, pode [utilizar grupos para atribuir acesso a aplicações](#managing-access-to-applications).

Também poderá estar interessado em [serviços de domínio do Azure AD](../active-directory-domain-services/active-directory-ds-overview.md), que lhe permite migrar as suas aplicações no local para o Azure ao ainda que satisfaçam as necessidades de identidade dessas aplicações.

### <a name="enabling-single-sign-on-between-azure-ad-and-on-premises-ad"></a>Ativar o início de sessão entre o Azure AD e AD no local
Se a sua organização manter um Windows Server Active Directory no local, juntamente com o Azure Active Directory na nuvem, em seguida, irá provavelmente pretender ativar o início de sessão entre estes dois sistemas. O Azure AD Connect (a ferramenta integra-se estes dois sistemas em conjunto) fornece várias opções para configurar o início de sessão único: estabelecer federação com o AD FS ou outro fornecedor de Federação ou ativar a sincronização de palavra-passe.

| Guia do artigo |  |
|:---:| --- |
| Uma descrição geral sobre as opções de início de sessão único disponibilizadas no Azure AD Connect, bem como informações sobre gerir ambientes híbridos |[Início de sessão do utilizador em Opções no Azure AD Connect](active-directory-aadconnect-user-signin.md) |
| Orientações gerais para gerir ambientes com ambos no local do Active Directory e o Azure Active Directory |[Considerações de conceção de identidade híbrida do Azure AD](active-directory-hybrid-identity-design-considerations-overview.md)<br /><br />[Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md) |
| Documentação de orientação sobre como utilizar a sincronização de palavra-passe para permitir SSO |[Implementar a sincronização de palavra-passe com o Azure AD Connect](active-directory-aadconnectsync-implement-password-synchronization.md)<br /><br />[Resolver problemas de sincronização de palavra-passe](https://support.microsoft.com/en-us/kb/2855271) |
| Documentação de orientação sobre como utilizar a repetição de escrita de palavras-passe para permitir SSO |[Introdução à gestão de palavra-passe no Azure AD](active-directory-passwords-getting-started.md)<br /><br />[Resolver problemas na Repetição de Escrita de Palavras-passe](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) |
| Documentação de orientação sobre como utilizar fornecedores de identidade de terceiros para permitir SSO |[Lista de fornecedores de identidade de terceiros compatível que podem ser utilizadas para ativar o início de sessão único](https://aka.ms/ssoproviders) |
| Como o Windows 10 utilizadores possam desfrutar as vantagens de início de sessão através da associação do Azure AD |[Expandir as capacidades de nuvem para o Windows 10 dispositivos através do Azure Active Directory aderir](active-directory-azureadjoin-overview.md) |

O Azure AD Connect está disponível para [todas as edições do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). Azure AD Self-Service reposição palavra-passe está disponível para [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) e [do Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/). Repetição de escrita de palavras-passe no local AD é um [do Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) funcionalidade.

### <a name="conditional-access-enforce-additional-security-requirements-for-high-risk-apps"></a>Acesso condicional: Impõem os requisitos de segurança adicional para aplicações de alto risco
Depois de configurar o início de sessão para as suas aplicações e recursos, pode, em seguida, obter proteger aplicações confidenciais através da imposição de requisitos de segurança específicos em cada início de sessão para essa aplicação. Por exemplo, pode utilizar o Azure AD para a pedido que todos os acessos a uma aplicação específica necessitam sempre de autenticação multifator, independentemente se essa aplicação suporta innately essa funcionalidade. Outro exemplo comum de acesso condicional é exigir que os utilizadores ser ligado à rede fidedigna da organização para aceder a aplicações confidenciais particularmente.

| Guia do artigo |  |
|:---:| --- |
| Uma introdução às funcionalidades de acesso condicional disponibilizados através do Azure AD, Office 365 e o Intune |[Gerir o risco com o acesso condicional](active-directory-conditional-access-azure-portal.md) |
| Como ativar o acesso condicional para os seguintes tipos de recursos |[Acesso condicional para aplicações SaaS](active-directory-conditional-access-azure-portal-get-started.md)<br /><br />[Acesso condicional para serviços do Office 365](active-directory-conditional-access-device-policies.md)<br /><br />[Acesso condicional para aplicações no local](active-directory-conditional-access-azure-portal.md)<br /><br />[Acesso condicional para aplicações no local publicadas através do Proxy de aplicações do Azure AD](application-proxy-enable-remote-access-sharepoint.md) |
| Como registar dispositivos com o Azure Active Directory para ativar políticas de acesso condicional baseado no dispositivo |[Descrição geral do registo de dispositivos do Azure Active Directory](active-directory-conditional-access-device-registration-overview.md)<br /><br />[Como ativar o registo automático de dispositivos para o domínio associado dispositivos Windows](active-directory-conditional-access-automatic-device-registration.md)<br />— [Dispositivos passos para o Windows 8.1](active-directory-conditional-access-automatic-device-registration-setup.md)<br />— [Dispositivos passos para o Windows 7](active-directory-conditional-access-automatic-device-registration-setup.md) |

| Como utilizar a aplicação Microsoft Authenticator para verificação de dois passos | [Authenticator da Microsoft](../multi-factor-authentication/end-user/microsoft-authenticator-app-how-to.md) |

Acesso condicional é um [do Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) funcionalidade.

## <a name="apps--azure-ad"></a>Aplicações e do Azure AD
### <a name="cloud-app-discovery-find-which-saas-apps-are-being-used-in-your-organization"></a>O cloud App Discovery: Localizar que aplicações SaaS estão a ser utilizadas na sua organização
Cloud App Discovery ajudem os departamentos de TI saber que aplicações SaaS estão a ser utilizadas em toda a organização. Pode medir a utilização da aplicação e a popularidade, para que possa determinar que aplicações serão beneficiam de que está a ser colocado sob o controlo de TI e a ser integrado com o Azure AD.

| Guia do artigo |  |
|:---:| --- |
| Uma descrição geral de como funciona |[Localizar não sancionadas aplicações em nuvem com o Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md) |
| Uma descrição mais aprofundada como funciona, com as respostas a perguntas sobre privacidade |[Considerações de privacidade e segurança](active-directory-cloudappdiscovery-security-and-privacy-considerations.md) |
| Perguntas Mais Frequentes |[FAQ sobre o Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/24037.cloud-app-discovery-frequently-asked-questions.aspx) |
| Tutoriais para implementar o Cloud App Discovery |[Guia de implementação de política de grupo](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx)<br /><br />[Guia de implementação do System Center](http://social.technet.microsoft.com/wiki/contents/articles/30968.cloud-app-discovery-system-center-deployment-guide.aspx)<br /><br />[A instalar nos servidores de Proxy com portas personalizadas](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md) |
| O registo de alterações para as atualizações do agente de Cloud App Discovery |[Registo de alterações](http://social.technet.microsoft.com/wiki/contents/articles/24616.cloud-app-discovery-agent-changelog.aspx) |

O cloud App Discovery é um [do Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) funcionalidade.

### <a name="automatically-provision-and-deprovision-user-accounts-in-saas-apps"></a>Aprovisionar e contas de utilizador em aplicações de SaaS de desaprovisionar automaticamente
Automatizar a criação, a manutenção e a remoção de identidades de utilizador em aplicações de SaaS, como o Dropbox, Salesforce, ServiceNow e muito mais. Corresponder e sincronização de identidades existentes entre o Azure AD e a aplicações SaaS e controlar o acesso através da desativação de contas automaticamente quando os utilizadores saírem da organização.

| Guia do artigo |  |
|:---:| --- |
| Saiba mais sobre como funciona e localizar as respostas a perguntas comuns |[Automatizar utilizador aprovisionamento e desaprovisionamento para aplicações SaaS](active-directory-saas-app-provisioning.md) |
| Configurar a forma como as informações estão mapeadas entre o Azure AD e a aplicação SaaS |[Personalizar os mapeamentos de atributos](active-directory-saas-customizing-attribute-mappings.md)<br><br>[Escrever expressões para mapeamentos de atributos](active-directory-saas-writing-expressions-for-attribute-mappings.md) |
| Como ativar o aprovisionamento automatizado para qualquer aplicação que suporte o protocolo SCIM |[Configurar o aprovisionamento automatizado do utilizador para qualquer aplicação SCIM-Enabled](active-directory-scim-provisioning.md) |
| Como comunicar e resolver problemas de aprovisionamento de utilizadores |[Relatórios sobre o aprovisionamento de utilizador automáticas](active-directory-saas-provisioning-reporting.md)<br><br>[Notificações de aprovisionamento](active-directory-saas-account-provisioning-notifications.md)<br><br>[Resolução de problemas de aprovisionamento de utilizadores](active-directory-application-provisioning-content-map.md) |
| Limite quem obtém aprovisionado para uma aplicação com base nos respetivos valores de atributo |[Filtros de âmbito](active-directory-saas-scoping-filters.md) |

Aprovisionamento de utilizadores automatizada está disponível para todas as edições do Azure AD para aplicações de até dez por utilizador. [O Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) suporta aplicações ilimitadas. Se a organização tiver [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) ou [do Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/), em seguida, pode [utilizar grupos para gerir os utilizadores que obterem aprovisionados](#managing-access-to-applications).

### <a name="building-applications-that-integrate-with-azure-ad"></a>Criar aplicações que se integram com o Azure AD
Se a sua organização está a desenvolver ou manter linha de negócio (LoB) aplicações, ou se tiver um programador de aplicações com os clientes que utilizam o Azure Active Directory, os tutoriais seguintes ajudará integrar as suas aplicações com o Azure AD.

| Guia do artigo |  |
|:---:| --- |
| Orientações para profissionais de TI e programadores de aplicações em integrar aplicações com o Azure AD |[O IT Guia do Pro para desenvolver aplicações para o Azure AD](active-directory-applications-guiding-developers-for-lob-applications.md)<br /><br />[Guia do programador do Azure Active Directory](active-directory-developers-guide.md) |
| Como a aplicação fornecedores adicionar as suas aplicações para a Galeria de aplicações do Azure AD |[Listar a aplicação na Galeria de aplicações do Azure Active Directory](active-directory-app-gallery-listing.md) |
| Como gerir o acesso a aplicações desenvolvidas com o Azure Active Directory |[Como ativar a atribuição de utilizadores para aplicações desenvolvidas](active-directory-applications-guiding-developers-requiring-user-assignment.md)<br /><br />[Atribuir utilizadores a sua aplicação](active-directory-applications-guiding-developers-assigning-users.md)<br /><br />[Atribuição de grupo para a sua aplicação](active-directory-applications-guiding-developers-assigning-groups.md) |

Se estiver a desenvolver aplicações direcionadas para o consumidor, poderá estar interessado em utilizar [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) para que não tem de desenvolver o seu próprio sistema de identidade para gerir os seus utilizadores. [Saiba mais](../active-directory-b2c/active-directory-b2c-overview.md).

## <a name="managing-access-to-applications"></a>Gerir o acesso a aplicações
### <a name="using-groups-and-self-service-to-manage-who-has-access-to-which-apps"></a>Utilização de grupos e Self-Service para gerir quem tem acesso às quais as aplicações
Para ajudar a gerir a quem deve ter acesso a quais recursos, o Azure Active Directory permite-lhe definir permissões e atribuições à escala através de grupos. IT podem optar por ativar funcionalidades self-service para que os utilizadores podem simplesmente pedir permissão quando precisarem.

| Guia do artigo |  |
|:---:| --- |
| Uma descrição geral das funcionalidades de gestão de acesso do Azure AD |[Introdução à gestão de acesso a aplicações](active-directory-managing-access-to-apps.md)<br /><br />[Como funciona a gestão de acesso no Azure AD](active-directory-manage-groups.md)<br /><br />[Como utilizar grupos para gerir o acesso a aplicações SaaS](active-directory-accessmanagement-group-saasapps.md) |
| Ativar a gestão personalizada de aplicações e grupos |[Gestão de aplicações de Self-Service](active-directory-self-service-application-access.md)<br /><br />[Gestão de grupos Self-Service](active-directory-accessmanagement-self-service-group-management.md) |
| Instruções para configurar os grupos no Azure AD |[Como criar grupos de segurança](active-directory-groups-create-azure-portal.md)<br /><br />[Como designar proprietários de um grupo](active-directory-accessmanagement-managing-group-owners.md)<br /><br />[Como utilizar o grupo "Todos os utilizadores"](active-directory-accessmanagement-dedicated-groups.md) |
| Utilize grupos dinâmicos para preencher automaticamente a associação ao grupo utilizando regras de associação baseadas em atributos |[Associação de grupo dinâmico: Regras avançadas](active-directory-groups-dynamic-membership-azure-portal.md)<br /><br />[As associações de grupo dinâmico de resolução de problemas](active-directory-accessmanagement-troubleshooting.md) |

Gestão de acesso de aplicação baseada em grupo está disponível para [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) e [do Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/). Gestão de grupos self-service, a gestão de aplicações de self-service e grupos dinâmicos são [do Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) funcionalidades.

### <a name="b2b-collaboration-enable-partner-access-to-applications"></a>Colaboração B2B: Ativar o acesso de parceiro a aplicações
Se a sua empresa tem uma parceria com outras empresas, é provável que precisa de gerir o acesso de parceiro a aplicações empresariais. Azure Active Directory B2B colaboração fornece uma forma fácil e segura de partilhar as suas aplicações com parceiros.

| Guia do artigo |  |
|:---:| --- |
| Uma descrição geral do Azure AD diferentes funcionalidades que pode ajudam a gerir utilizadores externos, como parceiros, clientes, etc. |[Capacidades de comparação para a gestão de identidades externas no Azure AD](active-directory-b2b-compare-external-identities.md) |
| Uma introdução à colaboração B2B e como começar |[Simples, segura, integração de parceiro da nuvem com o Azure AD](active-directory-b2b-what-is-azure-ad-b2b.md)<br /><br />[Colaboração do B2B do Azure Active Directory](active-directory-b2b-collaboration-overview.md) |
| Uma descrição mais aprofundada para colaboração B2B do Azure AD e como utilizá-la |[Colaboração B2B: Como funciona](active-directory-b2b-how-it-works.md)<br /><br />[Limitações atuais da colaboração B2B do Azure AD](active-directory-b2b-current-limitations.md)<br /><br />[Instruções detalhadas da utilização de colaboração B2B do Azure AD](active-directory-b2b-detailed-walkthrough.md) |
| Artigos de referência com detalhes técnicos sobre como funciona a colaboração B2B do Azure AD |[Formato de ficheiro CSV para adicionar utilizadores do parceiro](active-directory-b2b-references-csv-file-format.md)<br /><br />[Atributos de utilizador afetados por colaboração B2B do Azure AD](active-directory-b2b-references-external-user-object-attribute-changes.md)<br /><br />[Formato de Token de utilizador para os utilizadores do parceiro](active-directory-b2b-references-external-user-token-format.md) |

Colaboração B2B está atualmente disponível para [todas as edições do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="access-panel-a-portal-for-accessing-apps-and-self-service-features"></a>Painel de acesso: Um portal para aceder a aplicações e funcionalidades self-service
O painel de acesso do Azure AD é onde os utilizadores finais podem iniciar as suas aplicações e aceder às funcionalidades self-service que permitem que gerem as aplicações e as associações de grupo. Para além do painel de acesso, as outras opções para aceder a aplicações SSO ativado estão incluídas na lista abaixo.

| Guia do artigo |  |
|:---:| --- |
| Ver uma comparação entre as diferentes opções disponíveis para implementar as aplicações de início de sessão único para os utilizadores |[Implementação do Azure AD integrado a aplicações para utilizadores](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users) |
| Uma descrição geral do painel de acesso e o respetivo MyApps equivalente móveis |[Introdução ao painel de acesso e MyApps](active-directory-saas-access-panel-introduction.md)<br />— [iOS](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8)<br />— [Android](https://play.google.com/store/apps/details?id=com.microsoft.myapps) |
| Como aceder a aplicações do Azure AD do Web site do Office 365 |[Utilizar o iniciador de aplicações do Office 365](https://support.office.com/en-us/article/Meet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a) |
| Como aceder a aplicações do Azure AD a partir da aplicação móvel de Browser gerido do Intune |[Browser gerido do Intune](https://technet.microsoft.com/en-us/library/dn878029.aspx)<br />— [iOS](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8)<br />— [Android](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser) |
| Como aceder a aplicações do Azure AD através de ligações avançadas para iniciar o início de sessão único |[Obter ligações de início de sessão diretas às suas aplicações](active-directory-appssoaccess-whatis.md#direct-sign-on-links-for-federated-password-based-or-existing-apps) |

Painel de acesso está disponível para [todas as edições do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="reports-easily-audit-app-access-changes-and-monitor-sign-ins-to-apps"></a>Relatórios: Facilmente auditar as alterações de acesso de aplicação e monitorizar inícios de sessão para aplicações
Azure Active Directory fornece vários relatórios e alertas para o ajudar a monitorizar o acesso da sua organização para aplicações. Pode receber alertas para inícios de sessão anómalos às suas aplicações e pode controlar quando e por que motivo o acesso dos utilizadores a uma aplicação foi alterado.

| Guia do artigo |  |
|:---:| --- |
| Uma descrição geral das funcionalidades de relatórios no Azure Active Directory |[Introdução aos relatórios do Azure AD](active-directory-reporting-getting-started.md) |
| Como monitorizar a utilização de aplicações dos utilizadores e inícios de sessão |[Ver o acesso e os relatórios de utilização](active-directory-view-access-usage-reports.md) |
| Controlar as alterações efetuadas a quem pode aceder a uma aplicação específica |[Eventos de relatório de auditoria do Azure Active Directory](active-directory-reporting-audit-events.md) |
| Exportar os dados destes relatórios para as ferramentas preferenciais utilizando a API de relatórios |[Introdução ao Azure AD API do relatório](active-directory-reporting-api-getting-started.md) |

Para ver quais os relatórios que estão incluídos nas diferentes edições do Azure Active Directory, [clique aqui](active-directory-view-access-usage-reports.md).

## <a name="see-also"></a>Consultar também
[O que é o Azure Active Directory?](active-directory-whatis.md)

[Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/)

[Serviços de domínio do Active Directory do Azure](https://azure.microsoft.com/services/active-directory-ds/)

[Multi-factor Authentication do Azure](https://azure.microsoft.com/services/multi-factor-authentication/)
