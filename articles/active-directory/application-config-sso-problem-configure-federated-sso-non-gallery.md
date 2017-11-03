---
title: "Problema de configuração federada-início de sessão único para uma aplicação não Galeria | Microsoft Docs"
description: "Resolver alguns dos problemas comuns que poderão surgir quando configurar federado-início de sessão único para a aplicação de SAML personalizada que não esteja listada na Galeria de aplicações do Azure AD"
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
ms.openlocfilehash: d8fb9c71ede7558d12ce3e22dcd359ed82527e43
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2017
---
# <a name="problem-configuring-federated-single-sign-on-for-a-non-gallery-application"></a>Configuração federada-início de sessão único para uma aplicação não galeria do problema

Se ocorrer um problema ao configurar uma aplicação. Certifique-se de que seguiu todos os passos no artigo [configurar início de sessão único para aplicações que não estejam na Galeria de aplicações do Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery)

## <a name="cant-add-another-instance-of-the-application"></a>Não é possível adicionar outra instância da aplicação

Para adicionar uma segunda instância de uma aplicação, tem de ser capaz de:

-   Configure um identificador exclusivo para a segunda instância. Não será possível configurar o mesmo identificador utilizado para a primeira instância.

-   Configure um certificado diferente daquela utilizado para a primeira instância.

Se a aplicação não suporta qualquer um dos acima. Em seguida, não será possível configurar uma segunda instância.

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>Onde definir o formato EntityID (identificador de utilizador)

Não poderá selecionar o formato de EntityID (utilizador Identifier) que envia do Azure AD para a aplicação na resposta após a autenticação de utilizador.

Selecione de AD do Azure o formato para o atributo de NameID (identificador de utilizador) com base no valor selecionado ou o formato solicitado pela aplicação na AuthRequest SAML. Para obter mais informações, visite o artigo [protocolo SAML de início de sessão único](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) na secção NameIDPolicy,

## <a name="where-do-i-get-the-application-metadata-or-certificate-from-azure-ad"></a>Onde obter o certificado ou de metadados da aplicação do Azure AD

Para transferir os metadados da aplicação ou o certificado do Azure AD, siga os passos abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Co-administrador.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **aplicações empresariais** do menu de navegação esquerdo do Azure Active Directory.

5.  Clique em **todas as aplicações** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que pretende mostrar aqui, utilize o **filtro** controlo na parte superior a **lista de todas as aplicações** e defina o **mostrar** opção para **todas as aplicações.**

6.  Selecione a aplicação tiver configurado o início de sessão único.

7.  Quando carrega a aplicação, clique em de **de sessão único-** do menu de navegação esquerdo da aplicação.

8.  Aceda a **certificado de assinatura de SAML** secção, em seguida, clique em **transferir** valor da coluna. Consoante a aplicação que necessita de configurar o início de sessão único, consulte o a opção para transferir o XML de metadados ou o certificado.

Azure AD não fornece um URL para obter os metadados. Os metadados só podem ser obtidos como um ficheiro XML.

## <a name="dont-know-how-to-customize-saml-claims-sent-to-an-application"></a>Não sabe como personalizar afirmações SAML enviadas para uma aplicação

Para saber como personalizar as afirmações de atributo SAML enviadas para a sua aplicação, consulte [afirmações mapeamento no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) para obter mais informações.

## <a name="next-steps"></a>Passos seguintes
[Gestão de aplicações com o Azure Active Directory](active-directory-enable-sso-scenario.md)
