---
title: "Única de gestão de início de sessão para aplicações da empresa no Azure Active Directory | Microsoft Docs"
description: "Gerir definições de início de sessão único para aplicações da empresa dentro da sua organização a partir da Galeria de aplicações do Azure Active Directory"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: bcc954d3-ddbe-4ec2-96cc-3df996cbc899
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2017
ms.author: curtand
ms.reviewer: asmalser
ms.openlocfilehash: 882180a553fffee05a612cf70ddd9a0f30108415
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="managing-single-sign-on-for-enterprise-apps"></a>Gerir o início de sessão para aplicações da empresa

Este artigo descreve como utilizar o [portal do Azure](https://portal.azure.com) para gerir definições de início de sessão único para aplicações da empresa. Aplicações da empresa são aplicações que estão implementadas e utilizadas dentro da sua organização. Este artigo aplica-se especialmente para aplicações que foram adicionadas a partir de [Galeria de aplicações do Azure Active Directory](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery). 

## <a name="finding-your-apps-in-the-portal"></a>Localizar as suas aplicações no portal
Todas as aplicações da empresa que são definidas para o início de sessão único pode ser visto e gerido no portal do Azure. As aplicações podem ser encontradas no **mais serviços** &gt; **aplicações empresariais** secção do portal. 

![Painel de aplicações da empresa][1]

Selecione **todas as aplicações** para ver uma lista de todas as aplicações que tenham sido configurados. A seleção de uma aplicação apresenta os recursos para essa aplicação, onde podem ser visualizados relatórios para essa aplicação e uma variedade de definições pode ser gerida.

Para gerir as definições de início de sessão único, selecione **de sessão único-**.

![Painel de recursos de aplicação][2]

## <a name="single-sign-on-modes"></a>Modos de início de sessão único
**De sessão único-** começa com um **modo** menu, que permite que o modo único início de sessão seja configurado. As opções disponíveis incluem:

* **Baseados em SAML início de sessão** -esta opção está disponível se a aplicação suportar completa federado-início de sessão único com o Azure Active Directory utilizando o protocolo SAML 2.0, WS-Federation, ou o OpenID connect protocolos.
* **Baseado em palavra-passe de início de sessão** -esta opção está disponível se o formulário de palavra-passe ao preencher para esta aplicação de suporte do Azure AD.
* **Ligado início de sessão** -anteriormente conhecida como "Existentes single sign-on", esta opção permite aos administradores colocar uma ligação para esta aplicação no iniciador da aplicação de painel de acesso do Azure AD ou o Office 365 os seus utilizadores.

Para obter mais informações sobre estes modos, consulte [como único início de sessão com o trabalho do Azure Active Directory](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).

## <a name="saml-based-sign-on"></a>Baseados em SAML início de sessão
O **baseados em SAML início de sessão** opção está dividida em quatro secções:

### <a name="domains-and-urls"></a>Domínios e URLs
Este é onde todos os detalhes sobre o domínio e os URLs da aplicação são adicionados ao diretório do Azure AD. Todas as entradas necessárias para tornar a aplicação de trabalho de início de sessão único são apresentadas diretamente no ecrã, enquanto todas as entradas opcionais que podem ser visualizadas selecionando o **Mostrar avançadas definições de URL** caixa de verificação. A lista completa de entradas suportadas incluem:

* **início de sessão URL** – onde o utilizador vai para iniciar sessão para a aplicação. Se a aplicação está configurada para executar iniciada pelo fornecedor de serviço de início de sessão, quando um utilizador abre este URL, o fornecedor de serviços redireciona para o Azure AD para autenticar e o utilizador iniciar sessão. 
  * Se este campo é preenchido, o Azure AD utiliza o URL para iniciar a aplicação do Office 365 e o painel de acesso do Azure AD.
  * Se este campo for omitido, em seguida, Azure AD em vez disso, executa iniciada pelo fornecedor de identidade de início de sessão quando a aplicação é iniciada a partir do Office 365, o painel de acesso do Azure AD, ou do Azure AD único início de sessão no URL.
* **Identificador** -este URI deve identificar exclusivamente a aplicação para o qual o início de sessão único está a ser configurado. Este é o valor do Azure AD envia a aplicação como o parâmetro de público-alvo do SAML token e a aplicação deverá validá-lo. Este valor é também apresentado como o ID de entidade em quaisquer metadados SAML fornecido pela aplicação.
* **URL de resposta** -o URL de resposta é onde a aplicação de espera receber o token SAML. Este é também referido como o URL do serviço de consumidor da asserção (ACS). Depois de estes serem introduzidas, clique em seguinte para avançar para o ecrã seguinte. Este ecrã fornece informações sobre o que tem de ser configurado no lado de aplicação para ativá-la aceitar um token SAML do Azure AD.
* **Estado de reencaminhamento** -o estado de reencaminhamento é um parâmetro opcional que pode ajudar a informar a aplicação onde redirecionar o utilizador depois de concluída a autenticação. Normalmente, o valor é um URL válido, a aplicação, no entanto, algumas aplicações usam este campo de forma diferente (consulte único início de sessão documentação a aplicação para obter detalhes). A capacidade de definir o estado de reencaminhamento é uma funcionalidade que é exclusiva para o novo portal do Azure.

### <a name="user-attributes"></a>Atributos de utilizador
Este é onde os administradores podem visualizar e editar os atributos que são enviados no token SAML que o Azure AD emite à aplicação cada sessão de utilizadores.

O atributo só editável suportado é o **identificador de utilizador** atributo. O valor deste atributo é o campo no Azure AD que identifica exclusivamente cada utilizador na aplicação. Por exemplo, se a aplicação foi implementada utilizando o "endereço de E-Mail" como o nome de utilizador e o identificador exclusivo, em seguida, o valor seria definido para o campo "user.mail" no Azure AD.

### <a name="saml-signing-certificate"></a>Certificado de assinatura de SAML
Esta secção apresenta os detalhes do certificado utilizado pelo Azure AD para assinar os tokens SAML que são emitidos para a aplicação sempre que o utilizador é autenticado. Permite que as propriedades do certificado atual possível inspecioná-los, incluindo a data de expiração.

### <a name="application-configuration"></a>Configuração da aplicação
A secção final fornece a documentação e/ou os controlos necessários para configurar a aplicação para utilizar o Azure Active Directory como um fornecedor de identidade.

O **configurar aplicação** momento Escalamento menu fornece instruções passo concisas, incorporadas novo para configurar a aplicação. Esta é outra funcionalidade nova exclusiva para o novo portal do Azure.

> [!NOTE]
> Para obter um exemplo completo da documentação incorporado, consulte a aplicação em Salesforce.com. Documentação para mais aplicações está a ser adicionada continuamente.
> 
> 

![Docs incorporados][3]

## <a name="password-based-sign-on"></a>Baseado em palavra-passe de início de sessão
Se suportado para a aplicação, selecionar o modo SSO baseada em palavra-passe e selecionando **guardar** instantaneamente configura não SSO baseada em palavra-passe. Para obter mais informações sobre a implementação do SSO baseada em palavra-passe, consulte [como único início de sessão com o trabalho do Azure Active Directory](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).

![Baseado em palavra-passe de início de sessão][4]

## <a name="linked-sign-on"></a>O início de sessão ligado
Se suportado para a aplicação, selecionar o modo ligado do SSO permite-lhe introduzir o URL que pretende que o painel de acesso do Azure AD ou o Office 365 para redirecionar para quando os utilizadores clicarem nesta aplicação. Para mais informações sobre ligado SSO (anteriormente conhecida como "SSO existente"), consulte [como único início de sessão com o trabalho do Azure Active Directory](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).

![O início de sessão ligado][5]

##<a name="feedback"></a>Comentários

Esperamos, como a utilização a melhor experiência do Azure AD. Manter os comentários provenientes! Publique os seus comentários e ideias para melhoria no **Portal de administração** secção do nosso [fórum de comentários](https://feedback.azure.com/forums/169401-azure-active-directory/category/162510-admin-portal).  Iremos estiver entusiasmados sobre como criar conteúdo novo frio diariamente e utilize a orientações sobre a forma e definir o que devemos criar a seguir.

[1]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade.PNG
[2]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-sso-blade.PNG
[3]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-embedded-docs.PNG
[4]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-password-sso.PNG
[5]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-linked-sso.PNG
