---
title: "Problema de configuração federada-início de sessão único para uma aplicação de galeria do Azure AD | Microsoft Docs"
description: "Resolver alguns dos problemas comuns que poderão surgir quando configurar federado único início de sessão utilizando SAML para aplicações que estão listadas na Galeria de aplicações do Azure AD"
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
ms.openlocfilehash: 290ca66048281de5e031b0404919bed84ab19ffa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="problem-configuring-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Problema de configuração federada-início de sessão único para uma aplicação de galeria do Azure AD

Se ocorrer um problema ao configurar uma aplicação. Certifique-se de que seguiu todos os passos do tutorial da aplicação. A configuração da aplicação, é necessário inline documentação sobre como configurar a aplicação. Além disso, pode aceder a [lista de tutoriais sobre como integrar aplicações SaaS no Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) para uma orientação passo a passo de detalhe.

## <a name="cant-add-another-instance-of-the-application"></a>Não é possível adicionar outra instância da aplicação

Para adicionar uma segunda instância de uma aplicação, tem de ser capaz de:

-   Configure um identificador exclusivo para a segunda instância. Não será possível configurar o mesmo identificador utilizado para a primeira instância.

-   Configure um certificado diferente daquela utilizado para a primeira instância.

Se a aplicação não suporta qualquer um dos acima. Em seguida, não será possível configurar uma segunda instância.

## <a name="cant-add-the-identifier-or-the-reply-url"></a>Não é possível adicionar o identificador ou o URL de resposta

Se não for possível configurar o identificador ou o URL de resposta, confirme que os valores de identificador e o URL de resposta corresponderem os padrões de pré-configurados para a aplicação.

Para saber os padrões de pré-configurado para a aplicação:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Co-administrador.** Vá para o passo 7. Se já estiver no painel de configuração de aplicação no Azure AD.

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **aplicações empresariais** do menu de navegação esquerdo do Azure Active Directory.

5.  Clique em **todas as aplicações** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que pretende mostrar aqui, utilize o **filtro** controlo na parte superior a **lista de todas as aplicações** e defina o **mostrar** opção para **todas as aplicações.**

6.  Selecione a aplicação que pretende configurar o início de sessão único.

7.  Quando carrega a aplicação, clique em de **de sessão único-** do menu de navegação esquerdo da aplicação.

8.  Selecione **baseados em SAML início de sessão** do **modo** pendente.

9.  Vá para o **identificador** ou **URL de resposta** caixa de texto, no **secção domínio e os URLs.**

10. Existem três formas de saber os padrões suportados para a aplicação:

   * Na caixa de texto, consulte o pattern(s) suportado como um marcador de posição *exemplo:* <https://contoso.com>.

   * Se o padrão não é suportado, verá um ponto de exclamação vermelho quando tenta introduzir o valor na caixa de texto. Se pairar o rato sobre o ponto de exclamação vermelho, verá os padrões suportados.

   * O tutorial da aplicação, também pode obter informações sobre os padrões suportados. Sob o **configurar do Azure AD-início de sessão único** secção. Ir para o passo para configurar os valores no **domínios e URLs** secção.

Se os valores não coincidem com os padrões de pré-configurados no Azure AD. Pode:

-   Trabalhar com o fabricante da aplicação para obter valores que correspondam ao padrão pré-configurados no AD do Azure

-   Em alternativa, pode contactar a equipa do Azure AD, < aadapprequest@microsoft.com > ou deixe um comentário no tutorial para pedir a atualização de padrões suportados para a aplicação

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>Onde definir o formato EntityID (identificador de utilizador)

Não poderá selecionar o formato de EntityID (utilizador Identifier) que envia do Azure AD para a aplicação na resposta após a autenticação de utilizador.

Selecione de AD do Azure o formato para o atributo de NameID (identificador de utilizador) com base no valor selecionado ou o formato solicitado pela aplicação na AuthRequest SAML. Para obter mais informações, visite o artigo [protocolo SAML de início de sessão único](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) na secção NameIDPolicy,

## <a name="cant-find-the-azure-ad-metadata-to-complete-the-configuration-with-the-application"></a>Não é possível localizar os metadados do Azure AD para concluir a configuração com a aplicação

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
