---
title: "Opções no token SAML para aplicações previamente integradas no Azure Active Directory de assinatura de certificado avançado | Microsoft Docs"
description: "Saiba como utilizar as opções no token SAML para aplicações previamente integradas no Azure Active Directory de assinatura de certificado avançado"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2017
ms.author: jeedes
ms.custom: aaddev
ms.openlocfilehash: c4fb4e220d03533bd73a663d12e2639e664b4dba
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2017
---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>Avançadas Opções no token SAML para aplicações da galeria no Azure Active Directory de assinatura de certificado
Hoje o Azure Active Directory (Azure AD) suporta milhares de aplicações previamente integradas na Galeria de aplicações do Azure Active Directory. Este número inclui mais de 500 aplicações que suportam o início de sessão único através do protocolo SAML 2.0. Quando um utilizador efetua a autenticação para uma aplicação através do Azure AD utilizando SAML, o Azure AD envia um token para a aplicação (através de um HTTP POST). Em seguida, a aplicação valida e utiliza o token para o utilizador em vez de pedir um nome de utilizador e palavra-passe de início de sessão. Estes tokens SAML são assinados com o certificado exclusivo que é gerado no Azure AD e pelos algoritmos de padrão específicos.

Azure AD utiliza algumas as predefinições para as aplicações de galeria. Os valores predefinidos são configurados com base nos requisitos da aplicação.

Azure AD suporta as definições de assinatura de certificado avançado. Para selecionar estas opções, selecione o **Mostrar avançadas definições de assinatura de certificado** caixa de verificação:

![Mostrar definições de assinatura de certificado avançado][1]

Depois de selecionar esta caixa de verificação, pode configurar as opções de assinatura de certificado e o algoritmo de assinatura de certificado.

## <a name="certificate-signing-options"></a>Opções de assinatura de certificado

Azure AD suporta três opções de assinatura de certificado:

* **Inicie sessão de asserção SAML**. Esta opção predefinida está definida para a maioria das aplicações da galeria. Se esta opção for selecionada, o Azure AD como um IdP inicia o asserção SAML e o certificado com o X509 certificado da aplicação. Além disso, utiliza o algoritmo de assinatura, o que está selecionado no **algoritmo de assinatura** na lista pendente.

* **Inicie sessão resposta SAML**. Se esta opção for selecionada, o Azure AD como um IdP inicia a resposta SAML com o X509 certificado da aplicação. Além disso, utiliza o algoritmo de assinatura, o que está selecionado no **algoritmo de assinatura** na lista pendente.

* **Assinar resposta SAML e asserção**. Se esta opção for selecionada, o Azure AD como um IdP assina o token SAML completo com o X509 certificado da aplicação. Além disso, utiliza o algoritmo de assinatura, o que está selecionado no **algoritmo de assinatura** na lista pendente.

    ![Opções de assinatura de certificado][4]

## <a name="certificate-signing-algorithms"></a>Algoritmos de assinatura de certificado

AD do Azure suporta dois algoritmos de assinatura para assinar a resposta SAML:

* **SHA-256**. Azure AD utiliza este algoritmo predefinido para iniciar a resposta SAML. Este é o algoritmo mais recente e é tratado como mais segura do que o SHA-1. A maioria das aplicações suporta o algoritmo SHA-256. Se uma aplicação suporta apenas SHA-1 como o algoritmo de assinatura, pode alterá-la. Caso contrário, recomendamos que utilize o algoritmo SHA-256 para assinar a resposta SAML.

    ![Algoritmo de assinatura de certificado de SHA-256][3]

* **SHA-1**. Este é o algoritmo mais antigo e esta é tratada como menos segura do que SH 256. Se uma aplicação suporta apenas este algoritmo de assinatura, pode selecionar esta opção no **algoritmo de assinatura** na lista pendente. Azure AD, em seguida, inicia a resposta SAML com o algoritmo SHA-1.

    ![Algoritmo de assinatura de certificado de SHA-1][2]

## <a name="next-steps"></a>Passos seguintes
* [Índice de artigos da gestão de aplicações no Azure Active Directory](active-directory-apps-index.md)
* [Configurar início de sessão único para aplicações que não estejam na Galeria de aplicações do Azure Active Directory](application-config-sso-how-to-configure-federated-sso-non-gallery.md)
* [Resolver problemas com base no SAML-início de sessão único](develop/active-directory-saml-debugging.md)

<!--Image references-->

[1]: ./media/active-directory-enterprise-apps-advance-certificate-options/saml-advance-certificate.png
[2]: ./media/active-directory-enterprise-apps-advance-certificate-options/saml-signing-algo-sha1.png
[3]: ./media/active-directory-enterprise-apps-advance-certificate-options/saml-signing-algo-sha256.png
[4]: ./media/active-directory-enterprise-apps-advance-certificate-options/saml-signing-options.png
