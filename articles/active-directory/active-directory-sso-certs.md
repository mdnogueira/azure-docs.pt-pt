---
title: "Gerir certificados de Federação no Azure AD | Microsoft Docs"
description: "Saiba como personalizar a data de expiração para os certificados de Federação e como renovar os certificados que irão expirar em breve."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: f516f7f0-b25a-4901-8247-f5964666ce23
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/09/2017
ms.author: jeedes
ms.openlocfilehash: 1283b570200f05003658824760ecbb6722f241d9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Gerir os certificados federado início de sessão no Azure Active Directory
Este artigo aborda informações relacionadas com os certificados que o Azure Active Directory (Azure AD) cria para estabelecer federado-início de sessão único (SSO) para as aplicações de SaaS e perguntas comuns. Adicione aplicações na Galeria de aplicações do Azure AD ou utilizando um modelo de aplicação não galeria. Configure a aplicação utilizando a opção de SSO federada.

Este artigo é relevante apenas para aplicações que estão configuradas para utilizar o SSO do Azure AD através de Federação de SAML, conforme mostrado no exemplo seguinte:

![Azure AD início de sessão único](./media/active-directory-sso-certs/saml_sso.PNG)

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>Certificado gerado automaticamente para as aplicações da Galeria e não da Galeria
Quando adicionar uma nova aplicação na galeria do e configurar um baseados em SAML início de sessão, o Azure AD gera um certificado para a aplicação que é válido para três anos. Pode transferir este certificado a partir de **certificado de assinatura de SAML** secção. Para aplicações de galeria, esta secção poderá mostrar uma opção para transferir o certificado ou metadados, dependendo do requisito da aplicação.

![Azure AD-início de sessão único](./media/active-directory-sso-certs/saml_certificate_download.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>Personalizar a data de expiração para o seu certificado de Federação e rollover para um novo certificado
Por predefinição, os certificados estão definidos para expirar após três anos. Pode escolher uma data de expiração diferente para o certificado, efetuando os seguintes passos.
As capturas de ecrã utilizam Salesforce com vista à, exemplo, mas estes passos podem ser aplicadas a qualquer aplicação federada de SaaS.

1. No [portal do Azure](https://aad.portal.azure.com), clique em **aplicação empresarial** no painel esquerdo e, em seguida, clique em **nova aplicação** no **descrição geral** página:

   ![Abra o Assistente de configuração de SSO](./media/active-directory-sso-certs/enterprise_application_new_application.png)

2. Procure a aplicação de galeria e, em seguida, selecione a aplicação que pretende adicionar. Se não é possível localizar a aplicação necessária, adicione a aplicação utilizando o **aplicação Galeria não** opção. Esta funcionalidade está disponível apenas o SKU do Azure AD Premium (P1 e P2).

    ![Azure AD-início de sessão único](./media/active-directory-sso-certs/add_gallery_application.png)

3. Clique em de **de sessão único-** ligação no painel esquerdo e alterar **modo de início de sessão único** para **baseados em SAML início de sessão**. Este passo gera um certificado de três anos para a sua aplicação.

4. Para criar um novo certificado, clique o **criar novo certificado** ligação no **certificado de assinatura de SAML** secção.

    ![Gerar um novo certificado](./media/active-directory-sso-certs/create_new_certficate.png)

5. O **criar um novo certificado** ligação abre o controlo de calendário. Pode definir qualquer data e hora até três anos a contar da data atual. A data selecionada e a hora é o novo data de expiração e hora do seu certificado de novo. Clique em **Guardar**.

    ![Transferir, em seguida, carregue o certificado](./media/active-directory-sso-certs/certifcate_date_selection.PNG)

6. O novo certificado está agora disponível para transferência. Clique em de **certificado** ligação para transferi-lo. Neste momento, o certificado não está ativo. Quando pretender rollover para este certificado, selecione o **tornar o novo certificado ativa** caixa de verificação e clique em **guardar**. A partir desse ponto, do Azure AD é iniciado com o novo certificado de assinatura de resposta.

7.  Para saber como carregar o certificado para a aplicação SaaS específica, clique em de **tutorial de configuração de aplicação de vista** ligação.

## <a name="renew-a-certificate-that-will-soon-expire"></a>Renovar um certificado que irá expirar em breve
Os seguintes passos de renovação devem resultar em nenhum período de indisponibilidade significativo para os seus utilizadores. Podem aplicar as capturas de ecrã esta funcionalidade de secção Salesforce como exemplo, mas estes passos para qualquer aplicação federada de SaaS.

1. No **do Azure Active Directory** aplicação **de sessão único-** página, gere o novo certificado para a sua aplicação. Pode fazê-lo ao clicar no **criar novo certificado** ligação no **certificado de assinatura de SAML** secção.

    ![Gerar um novo certificado](./media/active-directory-sso-certs/create_new_certficate.png)

2. Selecione a data de expiração pretendida e a hora para o novo certificado e clique em **guardar**.

3. Transferir o certificado no **certificado de assinatura de SAML** opção. Carregar o novo certificado para o ecrã de configuração de início de sessão único da aplicação SaaS. Para saber como fazê-lo para a sua aplicação SaaS específica, clique em de **tutorial de configuração de aplicação de vista** ligação.
   
4. Para ativar o novo certificado no Azure AD, selecione o **tornar o novo certificado ativa** caixa de verificação e clique em de **guardar** botão na parte superior da página. Agrega sobre o novo certificado no lado do Azure AD. O estado do certificado é alterado de **novo** para **Active Directory**. A partir desse ponto, do Azure AD é iniciado com o novo certificado de assinatura de resposta. 
   
    ![Gerar um novo certificado](./media/active-directory-sso-certs/new_certificate_download.png)

## <a name="related-articles"></a>Artigos relacionados
* [Lista de tutoriais sobre como integrar aplicações SaaS no Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Índice de artigos da gestão de aplicações no Azure Active Directory](active-directory-apps-index.md)
* [Acesso a aplicações e início de sessão no Azure Active Directory](active-directory-appssoaccess-whatis.md)
* [Resolução de problemas baseados em SAML-início de sessão único](active-directory-saml-debugging.md)
