---
title: "Como escolher o tipo de aplicação a utilizar quando adicionar uma aplicação | Microsoft Docs"
description: "Compreender os tipos suportados de aplicações pode integrar com o Azure AD e as opções de configuração relacionados"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: e0d41d1933531c2c633613bcbc1bbcbf075d6a69
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-choose-which-application-type-to-use-when-adding-an-application"></a>Como escolher o tipo de aplicação a utilizar quando adicionar uma aplicação

Este artigo ajuda-o a compreender os quatro principais tipos de aplicações pode integrar com o Azure AD:

* O que é suportado por cada um deles
* Por que motivo, pode optar por utilizar que aplicação
* Como configurar propriedades de núcleos essas aplicações, como a forma como os utilizadores são **aprovisionado**, ou que **de sessão único-** tecnologia para utilizar.

## <a name="supported-application-types-in-azure-ad"></a>Tipos de aplicação suportadas no Azure AD

AD do Azure suporta quatro tipos de aplicação principal que pode adicionar utilizando o **adicionar** funcionalidade encontrada em **aplicações empresariais**. Estas incluem:

-   **Aplicações de galeria do Azure AD** – uma aplicação que foi previamente integrada para início de sessão com o Azure AD.

-   **Aplicações de Proxy de aplicação** – uma aplicação em execução no seu ambiente no local que pretende fornecer seguro início de sessão único para externamente.

-   **Desenvolvido personalizada aplicações** – uma aplicação que a sua organização pretende desenvolver na plataforma de desenvolvimento de aplicações do Azure do AD, mas que não pode ainda existe.

-   **Aplicações não galeria** – Traga as suas próprias aplicações! Qualquer ligação web que pretende, qualquer aplicação que compõe um campo de nome de utilizador e palavra-passe, suporta os protocolos SAML ou o OpenID Connect ou suporta SCIM que pretende integrar para início de sessão com o Azure AD.

## <a name="features-and-capabilities-supported-by-all-the-above-application-types"></a>Funcionalidades e capacidades suportadas por todos os tipos de aplicação acima

As seguintes funcionalidades são suportadas por qualquer um dos tipos de 4 aplicação acima no Azure AD:

-   **Início Rápido** – começar com uma aplicação rapidamente seguindo [passos de implementação simples](https://docs.microsoft.com/azure/active-directory/active-directory-integrating-applications-getting-started)

-   **Gestão de propriedades gerais** – obtenha um [deeplink direta](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users) para uma aplicação, [personalizar a imagem corporativa](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-change-app-logo-user-azure-portal) de uma aplicação, ou [desativar a aplicação](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) para todos os utilizadores.

-   **Gestão de utilizadores e grupo** – [atribuir](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) ou [remover](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) utilizadores e grupos para uma aplicação e, opcionalmente, atribua as funções de aplicação específica estes utilizadores e grupos que têm acesso ao

-   **Acesso de aplicação personalizada** – permitir aos utilizadores pedir [acesso de aplicação personalizada](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) para uma aplicação a partir do respetivo painéis de acesso de aplicação ou ao adicionar uma aplicação diretamente ou [a associar a um grupo ativado self-service](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management)opcionalmente exigir a aprovação de negócio ao longo do processo

-   **Os registos de início de sessão** – consulte [todas os inícios de sessão a uma aplicação](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins), ou todas as suas aplicações

-   **Registos de auditoria** – consulte [detalhadas registos de auditoria sobre alterações a uma aplicação](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs), ou para todas as suas aplicações

-   **Acesso condicional e baseados em risco** – definir powerful [regras de acesso baseado em condição](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access) que são aplicadas quando os utilizadores tentam iniciar sessão para uma aplicação específica

-   **Vista de permissões** – ver qualquer um do [OAuth2 permissões](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent) uma aplicação tem acesso no seu diretório de um único colocar

## <a name="single-sign-on-and-provisioning-modes-supported-by-specific-application-types"></a>O início de sessão único e o aprovisionamento modos suportados pelos tipos de aplicação específica

A tabela abaixo descreve as diferentes-início de sessão único e modos de aprovisionamento suportados por cada um dos tipos de aplicação acima. Pode utilizar esta tabela para ajudá-lo a compreender que aplicação terá de adicionar a suportar um objetivo específico.

  ![Tabela de tipos de aplicação](./media/application-tables/table1.png)

## <a name="how-to-choose-a-single-sign-on-mode"></a>Como escolher um modo de início de sessão único

Suportado **de sessão único-** modos de aplicações do Azure AD estão listados abaixo.

-   **Azure AD-início de sessão único desativada** – escolha do Azure AD-início de sessão único desativada **modo de início de sessão único** se ainda não está preparado para integrar esta aplicação-início de sessão único com o Azure AD ou simplesmente são testá-lo de saída

-   **Ligado início de sessão** – escolha o [ligado início de sessão](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) **modo de início de sessão único** se tiver uma aplicação que já está ligada um único início de sessão solução existente, ou se pretender publicar uma ligação simple para os seus utilizadores no respetivo [painel de acesso de aplicação](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) ou [iniciador da aplicação do Office 365](https://login.microsoftonline.com/common/oauth2/authorize?response_mode=form_post&response_type=id_token&scope=openid&nonce=d508a995-f6d6-4b8a-81b8-825c71f1be46.636253878097046923&state=https%3a%2f%2fsupport.office.com%2farticle%2fMeet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a%3fui%3den-US%26rs%3den-US%26ad%3dUS&client_id=4b233688-031c-404b-9a80-a4f3f2351f90&redirect_uri=https%3a%2f%2fsupport.office.com%2fauth%2fsignin&login_hint=asteen%40microsoft.com&prompt=none)

-   **Baseado em palavra-passe de início de sessão** – escolha o [baseada em palavra-passe de início de sessão](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) **modo de início de sessão único** se a aplicação apresenta um campo de nome de utilizador e palavra-passe HTML e de que pretende armazenar esse nome de utilizador e palavra-passe segura para serem reproduzidos para a aplicação mais tarde

-   **Baseados em SAML início de sessão** – escolha o [baseados em SAML início de sessão](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) início de sessão único modo se a aplicação suporta os protocolos SAML ou o OpenID Connect, ou se pretender ser capaz de mapear os utilizadores a funções de aplicação específicos com base nas regras que define nas suas afirmações SAML *

   >[!NOTE]
   >Esta opção não está disponível quando o proxy de aplicações está configurado para uma aplicação.
   >
   >

-   **Com base no cabeçalho de início de sessão** – Escolha esta [com base no cabeçalho de início de sessão](https://docs.microsoft.com/azure/active-directory/application-proxy-ping-access#what-is-pingaccess-for-azure-ad) único início de sessão se tiver uma aplicação utilizando PingAccess que suporta o cabeçalho de HTTP basear o modo autenticação que pretende executar o início de sessão único para 

   >[!NOTE]
   >Esta opção só está disponível quando o proxy de aplicações e PingAccess está configurado para uma aplicação.
   >
   >

-   **Autenticação integrada do Windows** – escolha o [autenticação integrada do Windows](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd) início de sessão único modo quando a exposição de uma aplicação de WIA no local que pretende efetuar o início de sessão único para 

   >[!NOTE]
   >Esta opção só está disponível quando o proxy de aplicações está configurado para uma aplicação.
   >
   >

## <a name="single-sign-on-modes-for-custom-developed-applications"></a>Modos de início de sessão único para aplicações desenvolvidas por personalizada

Aplicações tiver personalizado desenvolvido através de [aplicações desenvolvidas por personalizada](#_Custom-Developed_Applications) experiência também suportam adicionais único início de sessão modos não enumerados acima. Estas incluem:

-   [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code) com base em início de sessão

-   [OpenID Connect 1.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) com base em início de sessão

-   [WS-Federation 1.2](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html) com base em início de sessão

-   [SAML 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-protocol-reference) com base em início de sessão

Leia o [guia para programadores do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide) para saber mais sobre como criar uma aplicação desenvolvida personalizada que suporta estes modos de início de sessão único.

## <a name="how-to-set-an-applications-single-sign-on-mode"></a>Como definir único início de sessão no modo uma aplicação

Para definir uma aplicação **de sessão único-** modo, siga as instruções abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Co-administrador.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **aplicações empresariais** do menu de navegação esquerdo do Azure Active Directory.

5.  Clique em **todas as aplicações** para ver uma lista de todas as suas aplicações.

  * Se não vir a aplicação que pretende mostrar aqui, utilize o **filtro** controlo na parte superior a **lista de todas as aplicações** e defina o **mostrar** opção para **todas as aplicações.**

6.  Selecione a aplicação para o qual pretende configurar o início de sessão único.

7.  Quando carrega a aplicação, clique em **de sessão único-** do menu de navegação esquerdo da aplicação.

## <a name="how-to-choose-a-provisioning-mode"></a>Como escolher um modo de aprovisionamento

-   **Aprovisionamento manual** – escolha o [Manual](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning#provisioning-modes) modo de aprovisionamento, se tiver contas existentes, ou pretende gerir as contas para esta aplicação fora do Azure AD.

-   **Aprovisionamento automático** – escolha o [automática](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning#configuring-automatic-user-account-provisioning) **modo de aprovisionamento** se pretender ativar baseado na API aprovisionamento automático e/ou anular o aprovisionamento de contas de utilizador para esta aplicação 

   >[!NOTE]
   >Esta opção só está disponível para aplicações dentro de **em destaque** categoria do [Galeria de aplicações do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-whats-new-azure-portal#the-new-and-improved-application-gallery).
   >
   >

-   **Aprovisionamento automático baseado em SCIM** – utilizar [aprovisionamento automático baseado em SCIM](https://docs.microsoft.com/azure/active-directory/active-directory-scim-provisioning) se a aplicação suporta o protocolo SCIM para detetar as alterações aos utilizadores e grupos, que são emitidos automaticamente para as alterações para qualquer aplicação integrado com o Azure AD 

   >[!NOTE]
   >Esta opção não está indicada como um modo de aprovisionamento específico, mas está ativada por predefinição para todas as aplicações que estão integradas com o Azure AD.
   >
   >

## <a name="how-to-set-an-applications-provisioning-mode"></a>Como definir uma aplicação do modo de aprovisionamento

Para definir uma aplicação **aprovisionamento** modo, siga as instruções abaixo:

Para definir uma aplicação **de sessão único-** modo, siga as instruções abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Co-administrador.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **aplicações empresariais** do menu de navegação esquerdo do Azure Active Directory.

5.  Clique em **todas as aplicações** para ver uma lista de todas as suas aplicações.

  * Se não vir a aplicação que pretende mostrar aqui, utilize o **filtro** controlo na parte superior a **lista de todas as aplicações** e defina o **mostrar** opção para **todas as aplicações.**

6.  Selecione a aplicação para o qual pretende configurar o aprovisionamento.

7.  Quando carrega a aplicação, clique em **aprovisionamento** do menu de navegação esquerdo da aplicação.

## <a name="next-steps"></a>Passos seguintes
[Gestão de aplicações com o Azure Active Directory](active-directory-enable-sso-scenario.md)
