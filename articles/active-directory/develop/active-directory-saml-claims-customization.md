---
title: "Personalizar afirmações emitidas no token SAML para aplicações da empresa no Azure Active Directory | Microsoft Docs"
description: "Saiba como personalizar afirmações emitidas no token SAML para aplicações da empresa no Azure Active Directory"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: f1daad62-ac8a-44cd-ac76-e97455e47803
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: jeedes
ms.custom: aaddev
ms.openlocfilehash: a1a33705648a982631315439f99425bab59ad1d1
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2017
---
# <a name="customizing-claims-issued-in-the-saml-token-for-enterprise-applications-in-azure-active-directory"></a>Personalizar afirmações emitidas no token SAML para aplicações da empresa no Azure Active Directory
Hoje em dia do Azure Active Directory suporta o início de sessão único com a maioria das aplicações da empresa, incluindo ambas as aplicações previamente integradas na Galeria de aplicações do Azure AD, bem como as aplicações personalizadas. Quando um utilizador efetua a autenticação para uma aplicação através do Azure AD através do protocolo SAML 2.0, AD do Azure envia um token para a aplicação (através de um HTTP POST). E, em seguida, a aplicação valida e utiliza o token para iniciar a sessão do utilizador em vez de pedir um nome de utilizador e palavra-passe. Estes tokens SAML contém informações sobre o utilizador ao conhecido como "afirmações".

Na identidade-enunciar, uma "reclamação" informações de Estados de um fornecedor de identidade sobre um utilizador dentro do token que emitem para esse utilizador. No [SAML token](http://en.wikipedia.org/wiki/SAML_2.0), estes dados normalmente estão incluídos na declaração de atributo de SAML. ID exclusivo do utilizador é normalmente representado no requerente do SAML, também denominada como identificador de nome.

Por predefinição, o Azure Active Directory emite um token SAML para a aplicação que contém uma afirmação NameIdentifier, com um valor de nome de utilizador (também conhecidas como nome principal do utilizador) no Azure AD. Este valor pode identificar exclusivamente o utilizador. O token SAML também contém as afirmações adicionais que contém o endereço de correio eletrónico do utilizador, nome próprio e apelido.

Para ver ou editar as afirmações emitidas no SAML token para a aplicação, abra a aplicação no portal do Azure. Em seguida, selecione o **ver e editar todos os outros atributos de utilizador** caixa de verificação no **atributos de utilizador** secção da aplicação.

![Secção de atributos de utilizador][1]

Existem duas razões possíveis por que razão poderá ser necessário editar as afirmações emitidas no SAML token:
* A aplicação foi escrita para solicitar a um conjunto diferente de afirmação URIs ou valores de afirmação.
* A aplicação foi implementada de forma que requer a afirmação NameIdentifier ser algo que o nome de utilizador (também conhecidas como nome principal do utilizador) armazenado no Azure Active Directory.

Pode editar qualquer um dos valores de afirmação de predefinição. Selecione a linha de afirmação na tabela de atributos token SAML. Esta ação abre o **Editar atributo** secção e, em seguida, pode editar o nome de afirmação, o valor e o espaço de nomes associados a afirmação.

![Editar atributo de utilizador][2]

Pode também remover afirmações (que não seja NameIdentifier) utilizando o menu de contexto, que abre-se ao clicar no **...**  ícone.  Também pode adicionar nova afirmações utilizando o **adicionar atributo** botão.

![Editar atributo de utilizador][3]

## <a name="editing-the-nameidentifier-claim"></a>Editar a afirmação NameIdentifier
Para resolver o problema onde a aplicação foi implementada com um nome de utilizador diferente, clique em de **identificador de utilizador** pendente **atributos de utilizador** secção. Esta ação apresenta uma caixa de diálogo com várias opções diferentes:

![Editar atributo de utilizador][4]

Na lista pendente, selecione **user.mail** para definir a afirmação NameIdentifier para ser o endereço de correio eletrónico do utilizador no diretório. Em alternativa, selecione **user.onpremisessamaccountname** a definir para o utilizador do SAM nome da conta que tenha sido sincronizados a partir do Azure AD no local.

Também pode utilizar o especial **ExtractMailPrefix()** função para remover o sufixo de domínio, o endereço de e-mail, nome da conta do SAM ou o nome principal de utilizador. Extrai apenas a primeira parte do nome de utilizador que está a ser passem (por exemplo, "joe_smith" em vez de joe_smith@contoso.com).

![Editar atributo de utilizador][5]

Iremos agora tenha também adicionado o **join()** função para aderir ao domínio verificado com o valor do identificador de utilizador. Quando seleciona a função de join() no **identificador de utilizador** selecione primeiro o identificador de utilizador como, como o nome do principal utilizador ou endereço de e-mail e, em seguida, no segundo lista pendente, selecione o domínio verificado. Se selecionar o endereço de e-mail com o domínio verificado, em seguida, do Azure AD extrai o nome de utilizador do primeiro joe_smith de valor de joe_smith@contoso.com e o acrescenta com contoso.onmicrosoft.com. Consulte o exemplo seguinte:

![Editar atributo de utilizador][6]

## <a name="adding-claims"></a>A adição de afirmações
Ao adicionar uma afirmação, pode especificar o nome de atributo (que não estritamente tem de seguir um padrão URI de acordo com a especificação SAML). Defina o valor para qualquer atributo de utilizador que é armazenado no diretório.

![Adicione o atributo de utilizador][7]

Por exemplo, terá de enviar o departamento de que o utilizador pertence a respetiva organização como uma afirmação (como, vendas). Introduza o nome de afirmação, conforme esperado pela aplicação e, em seguida, selecione **user.department** como o valor.

> [!NOTE]
> Se para um utilizador especificado não existir nenhum valor armazenado para um atributo selecionado, em seguida, esse afirmação não é a ser emitida no token.

> [!TIP]
> O **user.onpremisesecurityidentifier** e **user.onpremisesamaccountname** só são suportadas quando a sincronização dos dados de utilizador no local do Active Directory utilizando o [ferramenta Azure AD Connect](../active-directory-aadconnect.md).

## <a name="restricted-claims"></a>Afirmações restritas

Existem algumas restritas afirmações no SAML. Se adicionar estas afirmações, em seguida, do Azure AD não envie estas afirmações. Seguem-se o conjunto de afirmações SAML restringida:

    | Tipo de afirmação (URI) |
    | ------------------- |
    | http://schemas.microsoft.com/ws/2008/06/Identity/Claims/Expiration |
    | http://schemas.microsoft.com/ws/2008/06/Identity/Claims/Expired |
    | http://schemas.microsoft.com/Identity/Claims/accesstoken |
    | http://schemas.microsoft.com/Identity/Claims/openid2_id |
    | http://schemas.microsoft.com/Identity/Claims/identityprovider |
    | http://schemas.microsoft.com/Identity/Claims/objectidentifier |
    | http://schemas.microsoft.com/Identity/Claims/PUID |
    | http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/NameIdentifier [MR1] |
    | http://schemas.microsoft.com/Identity/Claims/tenantid |
    | http://schemas.microsoft.com/ws/2008/06/Identity/Claims/AuthenticationInstant |
    | http://schemas.microsoft.com/ws/2008/06/Identity/Claims/AuthenticationMethod |
    | http://schemas.microsoft.com/accesscontrolservice/2010/07/Claims/identityprovider |
    | http://schemas.microsoft.com/ws/2008/06/Identity/Claims/Groups |
    | http://schemas.microsoft.com/Claims/Groups.Link |
    | http://schemas.microsoft.com/ws/2008/06/Identity/Claims/role |
    | http://schemas.microsoft.com/ws/2008/06/Identity/Claims/wids |
    | http://schemas.microsoft.com/2014/09/devicecontext/Claims/iscompliant |
    | http://schemas.microsoft.com/2014/02/devicecontext/Claims/isknown |
    | http://schemas.microsoft.com/2012/01/devicecontext/Claims/ismanaged |
    | http://schemas.microsoft.com/2014/03/psso |
    | http://schemas.microsoft.com/Claims/authnmethodsreferences |
    | http://schemas.xmlsoap.org/ws/2009/09/Identity/Claims/actor |
    | http://schemas.microsoft.com/ws/2008/06/Identity/Claims/samlissuername |
    | http://schemas.microsoft.com/ws/2008/06/Identity/Claims/confirmationkey |
    | http://schemas.microsoft.com/ws/2008/06/Identity/Claims/windowsaccountname |
    | http://schemas.microsoft.com/ws/2008/06/Identity/Claims/primarygroupsid |
    | http://schemas.microsoft.com/ws/2008/06/Identity/Claims/primarysid |
    | http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/authorizationdecision |
    | http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/Authentication |
    | http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/SID |
    | http://schemas.microsoft.com/ws/2008/06/Identity/Claims/denyonlyprimarygroupsid |
    | http://schemas.microsoft.com/ws/2008/06/Identity/Claims/denyonlyprimarysid |
    | http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/denyonlysid |
    | http://schemas.microsoft.com/ws/2008/06/Identity/Claims/denyonlywindowsdevicegroup |
    | http://schemas.microsoft.com/ws/2008/06/Identity/Claims/windowsdeviceclaim |
    | http://schemas.microsoft.com/ws/2008/06/Identity/Claims/windowsdevicegroup |
    | http://schemas.microsoft.com/ws/2008/06/Identity/Claims/windowsfqbnversion |
    | http://schemas.microsoft.com/ws/2008/06/Identity/Claims/windowssubauthority |
    | http://schemas.microsoft.com/ws/2008/06/Identity/Claims/windowsuserclaim |
    | http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/x500distinguishedname |
    | http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/UPN |
    | http://schemas.microsoft.com/ws/2008/06/Identity/Claims/GroupSID |
    | http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/SPN |
    | http://schemas.microsoft.com/ws/2008/06/Identity/Claims/ispersistent |
    | http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/privatepersonalidentifier |
    | http://schemas.microsoft.com/Identity/Claims/SCOPE |

## <a name="next-steps"></a>Passos seguintes
* [Índice de Artigos da Gestão da Aplicação no Azure Active Directory](../active-directory-apps-index.md)
* [Configurar o início de sessão único em aplicações que não fazem parte da galeria de aplicações do Azure Active Directory](../application-config-sso-how-to-configure-federated-sso-non-gallery.md)
* [Resolução de problemas baseados em SAML Single Sign-On](active-directory-saml-debugging.md)

<!--Image references-->
[1]: ./media/active-directory-saml-claims-customization/user-attribute-section.png
[2]: ./media/active-directory-saml-claims-customization/edit-claim-name-value.png
[3]: ./media/active-directory-saml-claims-customization/delete-claim.png
[4]: ./media/active-directory-saml-claims-customization/user-identifier.png
[5]: ./media/active-directory-saml-claims-customization/extractemailprefix-function.png
[6]: ./media/active-directory-saml-claims-customization/join-function.png
[7]: ./media/active-directory-saml-claims-customization/add-attribute.png
